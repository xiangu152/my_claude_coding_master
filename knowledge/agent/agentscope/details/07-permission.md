---
title: "权限系统"
source: "https://docs.agentscope.io/versions/2.0.3/zh/building-blocks/permission-system"
version: "2.0.3"
language: "zh"
---

# 权限系统

> 原始文档来源：https://docs.agentscope.io/versions/2.0.3/zh/building-blocks/permission-system
> AgentScope 2.0.3 中文文档

---
概述
Permission Mode
Permission Rule
模式示例
配置规则
Built-in Checks
自定义 tool
Safety check 契约
只读命令
危险路径保护
常见场景

精细控制 agent 可以执行哪些 tool、何时执行


概述
Permission system 拦截 agent 的每一次工具调用，给出三种决策之一：允许（allow） 执行、拒绝（deny） 执行，或者询问用户（ask） 确认。
它把静态配置与动态运行时分析组合起来。三个组件共同决定结果：
Rules —— 针对每个 tool 与命令的显式 allow / deny / ask 模式，最高优先级。规则有两种来源：在 PermissionContext 中静态预配置，或在 ASK 提示中由用户接受建议规则而动态加入。建议规则由本次工具调用自动生成 —— 一旦接受，将来相同的调用便会被自动处理，不再询问。
Mode —— 配置阶段设定的全局静态策略；决定所有不命中任何规则的调用的默认行为（例如 EXPLORE 让 agent 进入只读；DONT_ASK 静默拒绝未命中的调用）。
Built-in Checks —— 由 tool 自身在运行时基于真实输入做的动态分析：只读命令检测（在调用时解析 bash 命令）、危险路径保护（检查实际文件路径或命令目标）。工具可以通过 PermissionDecision.bypass_immune=True 将一次 safety ASK 标记为不可绕过；引擎会在 DEFAULT、ACCEPT_EDITS、DONT_ASK 下尊重该标记（allow rules 也无法将其静默）。BYPASS 模式是唯一例外 —— 它按设计明确跳过所有 safety ASK。
User
Tool
Permission System
LLM
User
Tool
Permission System
LLM
Built-in Checks · Rules · Mode
alt
[User approves]
[User denies]
alt
[ALLOW]
[DENY]
[ASK + Suggestions]
Tool Call
execute
result
denied
ASK + Suggestions
allow
result
accept suggested rule
deny
denied
下面是各 mode 下的决策流程。ASK 结果会触发用户确认；如果用户接受自动生成的 suggested rule，规则会被持久化以供后续调用使用。
DEFAULT
EXPLORE
ACCEPT_EDITS
BYPASS
DONT_ASK

Match

No

Match

No

ALLOW

DENY

Safety ASK (bypass_immune)

PASSTHROUGH / other ASK

Match

No

Tool Call

Deny Rules?

DENY

Ask Rules?

ASK

Tool check_permissions

ALLOW

Allow Rules?

Deny 规则与显式 ask 规则在每个 mode 下都始终生效（包括 BYPASS）。
工具发出的 safety ASK（bypass_immune=True）在 DEFAULT、ACCEPT_EDITS、DONT_ASK 下被尊重 —— allow 规则也无法将其静默。BYPASS 模式按设计跳过：BYPASS 的语义是”用户已主动放弃 safety 提示；只剩 deny / ask 规则作为护栏”。

Permission Mode
AgentScope 支持以下模式，分别适配不同的部署场景：
| Mode | 行为 | 适用场景 |
| --- | --- | --- |
| DEFAULT | 所有操作都需要显式规则或用户确认。唯一的内置自动放行路径是 Bash 识别只读命令（ls、git status、cat 等）后返回 ALLOW。Read / Glob / Grep 返回 PASSTHROUGH，命中不到 allow 规则仍然会 ASK | 最安全，推荐默认值 |
| ACCEPT_EDITS | 自动放行工作目录内的文件操作；Bash filesystem 命令（mkdir/touch/rm/cp/mv/sed）仅当所有目标路径都在某个工作目录内才自动放行 | 用户在场的活跃开发 |
| EXPLORE | 只读：放行 read-only 工具与只读 Bash 命令（ls、git status、cat 等）；拒绝任何修改。用户配置的 DENY / ASK 规则优先级高于只读自动放行 | 代码探索、规划 |
| BYPASS | 跳过所有权限检查，除了 deny / ask 规则与工具自身的 DENY。工具的 safety ASK 不会被强制（rm -rf /、写入 ~/.bashrc、命令注入等都会放行）。请用 deny 规则保护具体路径 | 沙箱环境或完全可信的运行 |
| DONT_ASK | 把所有 ASK（默认 ASK、ASK 规则、以及 safety ASK）转为 DENY；非交互式运行下默认安全 | 无人值守 / 计划任务 |
可以在创建 agent 时通过 AgentState.permission_context 设置 mode，也可以在运行时更新：
初始化时配置
运行时切换
ACCEPT_EDITS 配合工作目录
```python
from agentscope.agent import Agent
from agentscope.state import AgentState
from agentscope.permission import PermissionContext, PermissionMode
```

agent = Agent(
    name="my_agent",
    system_prompt="...",
    model=model,
    state=AgentState(
        permission_context=PermissionContext(
            mode=PermissionMode.DEFAULT,
        )
    ),
)


Permission Rule
PermissionRule 把某个 tool 与具体的调用模式映射到三种行为之一：ALLOW、DENY、ASK。
每条规则由下述字段组成。当权限引擎评估一条规则时，它会用 rule_content 与实际调用入参调用该 tool 的 match_rule() 方法，判断规则是否命中。

tool_name
str必填
规则适用的 tool 名："Bash"、"Read"、"Write"、"Edit"，或任意自定义 tool 名。

rule_content
str | None必填
匹配模式 —— 语义随 tool_name 变化：
Bash：通配前缀模式（npm run:* 命中 npm run build、npm run test）
Read / Write / Edit：glob 模式（src/**/*.py 命中 src/ 下任意 .py）
其他 tool：对 JSON 序列化后的参数做精确匹配

behavior
PermissionBehavior必填
ALLOW、DENY 或 ASK

source
str必填
规则来源："userSettings"、"projectSettings"、"session" 等。

模式示例
rule_content 由各 tool 的 match_rule() 方法消费，并由 ToolBase.generate_suggestions() 自动生成。由于这两个方法都属于 tool 接口的一部分，每个 tool 可以独立定义自己的模式语法与匹配逻辑。
AgentScope 内置工具的模式约定如下：
Bash
文件类工具（Read / Write / Edit）
针对 command 参数做匹配。模式格式为 COMMAND_PREFIX:* —— 前缀是命令的首段 token，* 匹配后续任意参数。
| 模式 | 匹配 | 不匹配 |
| --- | --- | --- |
| npm run:* | npm run build、npm run test | npm install |
| git commit:* | git commit -m "fix" | git push |
| rm:* | rm file.txt、rm -rf /tmp/x | ls |
PermissionRule(
    tool_name="Bash",
    rule_content="npm run:*",
    behavior=PermissionBehavior.ALLOW,
    source="userSettings",
)


配置规则
初始化时 —— 在创建 agent 时把规则传入 PermissionContext：
```python
from agentscope.agent import Agent
from agentscope.state import AgentState
from agentscope.permission import (
    PermissionContext, PermissionMode, PermissionRule, PermissionBehavior
```
)

agent = Agent(
    name="my_agent",
    system_prompt="...",
    model=model,
    state=AgentState(
        permission_context=PermissionContext(
            mode=PermissionMode.DEFAULT,
            allow_rules={
                "Bash": [PermissionRule(tool_name="Bash", rule_content="npm run:*",
                                        behavior=PermissionBehavior.ALLOW, source="userSettings")],
                "Write": [PermissionRule(tool_name="Write", rule_content="src/**",
                                         behavior=PermissionBehavior.ALLOW, source="userSettings")],
            },
            deny_rules={
                "Bash": [PermissionRule(tool_name="Bash", rule_content="rm:*",
                                        behavior=PermissionBehavior.DENY, source="userSettings")],
            },
        )
    ),
)

运行时通过建议规则 —— 当权限系统返回 ASK 时，会基于本次调用自动生成建议规则。把已接受的规则附在 UserConfirmResultEvent.rules 中回传，agent 会自动写入引擎：
from agentscope.event import UserConfirmResultEvent

# ASK 决策中包含基于本次调用生成的 suggested_rules。
# 接受建议时，把它放入结果事件即可：
result = UserConfirmResultEvent(
    confirmed=True,
    rules=[suggested_rule],  # 已接受的规则会被持久化进引擎
)


Built-in Checks
每个 tool 都实现了一个 check_permissions() 方法，在运行时基于真实调用入参执行检查。AgentScope 内置工具覆盖三类检查：
危险路径保护 —— Write、Edit、Bash 检查目标文件或命令是否触及敏感路径。返回一个 bypass-immune 的 safety ASK，在 DEFAULT/ACCEPT_EDITS/DONT_ASK 下被尊重（allow 规则无法静默）。BYPASS 模式按设计跳过。
只读命令检测 —— Bash 解析命令字符串识别只读操作，在所有 mode（包括 DEFAULT）下自动放行。对于像 Bash 这种结果依赖于输入的工具，这一判定通过 check_read_only() 方法暴露（详见下文）。
ACCEPT_EDITS 模式 —— Write 与 Edit 自动放行已配置工作目录内的文件操作。Bash 额外要求 filesystem 命令（mkdir/touch/rm/cp/mv/sed 等）的所有目标路径都在某个工作目录内。

自定义 tool
自定义 tool 可以重写 check_permissions() 添加工具特定的权限逻辑。如果工具的只读状态取决于输入（比如 Bash —— ls 是只读，rm 不是），还应该重写 check_read_only()。
```python
from agentscope.tool import ToolBase
from agentscope.permission import PermissionContext, PermissionDecision, PermissionBehavior
```

```python
class MyTool(ToolBase):
    name = "MyTool"
    # 静态默认值。对于结果取决于输入的工具，把这里设成保守默认，
    # 然后重写 check_read_only()。
    is_read_only = False
```

```python
    async def check_read_only(self, tool_input: dict) -> bool:
        """可选：动态只读判定。
```

```python
        默认返回 self.is_read_only。当某次调用是否修改状态取决于
        输入时进行重写。引擎用它来决定在 EXPLORE 和 ACCEPT_EDITS
        模式下是否自动放行。
        """
        return tool_input.get("operation") in {"list", "describe", "get"}
```

```python
    async def check_permissions(
        self,
        tool_input: dict,
        context: PermissionContext,
    ) -> PermissionDecision:
        target = tool_input.get("target")
```

```python
        # 自定义安全检查：阻止操作生产资源。
        # 设置 bypass_immune=True 让这条 ASK 在 DEFAULT / ACCEPT_EDITS /
        # DONT_ASK 下不被 allow 规则覆盖；BYPASS 模式仍然会跳过。
        if target and target.startswith("prod-"):
            return PermissionDecision(
                behavior=PermissionBehavior.ASK,
                message=f"Operation targets production resource: {target}",
                decision_reason="Safety check: production resource",
                bypass_immune=True,
            )
```

```python
        # 返回 PASSTHROUGH 让引擎继续按 rules / mode 评估
        return PermissionDecision(behavior=PermissionBehavior.PASSTHROUGH)
```


Safety check 契约
Safety check 是工具自己发出、认为太危险不能被静默放行的 ASK —— 例如 Write 到 ~/.bashrc、Bash 执行 rm -rf /。在决策上设 bypass_immune=True，即使命中了 allow 规则或处于会自动放行的 mode，引擎也仍然把 ASK 浮到用户。
适用于”一次错误调用就会造成用户几乎肯定不想发生的破坏”的场景。例如：自定义的 DeployTool 在目标是 prod-* 时返回 bypass_immune=True，那么为 staging 配的 allow_rules["DeployTool"] = ["*"] 也不会意外授权生产部署。
各 mode 下的具体处理：
Mode	bypass_immune=True 的 ASK
DEFAULT	尊重 —— allow 规则不能将其覆盖
ACCEPT_EDITS	尊重 —— 同 DEFAULT
EXPLORE	不适用（EXPLORE 不会调用 check_permissions，只读判定就已经决断了一切）
BYPASS	忽略 —— BYPASS 按设计跳过所有 safety ASK
DONT_ASK	转为 DENY（没有用户可以回答）
普通 ASK（bypass_immune=False，默认值）可以被 DEFAULT/ACCEPT_EDITS 下匹配的 allow 规则覆盖，在 BYPASS 下被兜底放行。

只读命令
常见的只读 bash 命令在没有任何规则的情况下也会被自动放行，在所有 mode（包括 DEFAULT）下都生效。复合命令（&&、||、;、|）只有在所有子命令都只读时才视为只读。输出重定向（>、>>）会让命令立即失去只读属性。

完整只读命令列表


危险路径保护
针对以下路径的操作在 DEFAULT、ACCEPT_EDITS、DONT_ASK 下触发一个 bypass-immune 的 ASK（在 DONT_ASK 下被转为 DENY）。BYPASS 模式按设计跳过此检查 —— 如果你在 BYPASS 下仍想要危险路径保护，请为具体路径添加 deny 规则。
类别	路径
Shell 配置	.bashrc、.zshrc、.bash_profile、.profile
Git 配置	.gitconfig、.gitmodules
SSH	.ssh/config、.ssh/authorized_keys、id_rsa、id_ed25519
凭证	.env、.env.local、.npmrc、.pypirc、.aws/credentials
目录	.git/、.ssh/、.claude/、.vscode/、.aws/、.kube/

常见场景
下面的示例展示了如何为常见部署场景配置 AgentState.permission_context。每个示例把一种 mode 与一组规则结合，匹配特定的使用场景。
只读探索
无人值守自动化
BYPASS 配显式护栏
# EXPLORE 模式：agent 可以自由使用只读工具（Read、Grep、Glob）
# 和只读 bash 命令（`ls`、`git status`、`cat` 等）。
# 任何修改 —— Write、Edit、非只读 bash 命令 —— 都会被自动拒绝。
agent = Agent(
    name="explorer",
    system_prompt="...",
    model=model,
    state=AgentState(
        permission_context=PermissionContext(mode=PermissionMode.EXPLORE)
    ),
)

