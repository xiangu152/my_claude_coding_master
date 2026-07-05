---
title: "用户征询"
source: "https://fastmcp.wiki/zh/servers/elicitation"
version: "latest"
---

# 用户征询

> 原始文档来源：https://fastmcp.wiki/zh/servers/elicitation

---

什么是征询？
基本用法
方法签名
征询动作
响应类型
标量类型
无响应
约束选项
结构化响应
多轮征询
客户端要求
交互
用户征询

在工具执行期间通过 MCP 上下文从用户请求结构化输入。

版本
2.10.0
新增
用户征询允许 MCP 服务器在工具执行期间从用户请求结构化输入。工具可以交互式地请求缺失的参数、澄清或根需要的额外上下文，而不是需要在前期提供所有输入。
本文档中的大多数示例假设您有一个名为 mcp 的 FastMCP 服务器实例，并展示如何从 @mcp.tool 装饰的函数中使用 ctx.elicit 方法请求用户输入。

什么是征询？
征询使工具能够暂停执行并从用户请求特定信息。这对以下情况特别有用：
缺失参数：请求初始未提供的必需信息
澄清请求：获取用户对模糊情况的确认或选择
渐进式公开：逐步收集复杂信息
动态工作流：根据用户响应调整工具行为
例如，文件管理工具可能会问“我应该创建哪个目录？”，或数据分析工具可能会请求“我应该分析哪个日期范围？”

基本用法
在任何工具函数中使用 ctx.elicit() 方法请求用户输入：
from fastmcp import FastMCP, Context
from dataclasses import dataclass

mcp = FastMCP("Elicitation Server")

@dataclass
class UserInfo:
    name: str
    age: int

@mcp.tool
async def collect_user_info(ctx: Context) -> str:
    """通过交互式提示收集用户信息。"""
    result = await ctx.elicit(
        message="请提供您的信息",
        response_type=UserInfo
    )
    
    if result.action == "accept":
        user = result.data
        return f"您好 {user.name}，您 {user.age} 岁"
    elif result.action == "decline":
        return "未提供信息"
    else:  # cancel
        return "操作已取消"

方法签名
上下文征询方法

ctx.elicit
异步方法

显示 参数

显示 响应

征询动作
征询结果包含一个 action 字段，指示用户的响应方式：
accept：用户提供了有效输入 - 数据在 data 字段中可用
decline：用户选择不提供请求的信息，data 字段为 None
cancel：用户取消了整个操作，data 字段为 None
@mcp.tool
async def my_tool(ctx: Context) -> str:
    result = await ctx.elicit("选择一个动作")

    if result.action == "accept":
        return "已接受！"
    elif result.action == "decline":
        return "已拒绝！"
    else:
        return "已取消！"

FastMCP 还提供了类型化结果类用于对 action 字段进行模式匹配：
from fastmcp.server.elicitation import (
    AcceptedElicitation, 
    DeclinedElicitation, 
    CancelledElicitation,
)

@mcp.tool
async def pattern_example(ctx: Context) -> str:
    result = await ctx.elicit("输入您的姓名：", response_type=str)
    
    match result:
        case AcceptedElicitation(data=name):
            return f"您好 {name}！"
        case DeclinedElicitation():
            return "未提供姓名"
        case CancelledElicitation():
            return "操作已取消"

响应类型
服务器必须向客户端发送一个结构，指示它对征询请求的响应期望的数据类型。如果请求被 accept，客户端必须发送与架构匹配的响应。
MCP 规范仅支持对征询响应使用 JSON Schema 类型的有限子集。具体来说，它仅支持具有基本属性的 JSON 对象，包括 string、number（或 integer）、boolean 和 enum 字段。
FastMCP 通过自动将它们包装在 MCP 兼容的对象架构中，使得请求更广泛的类型范围变得容易，包括标量（例如 str）或根本不需要响应。

标量类型
您可以为基本输入请求简单的标量数据类型，例如字符串、整数或布尔值。
当您请求标量类型时，FastMCP 会自动将其包装在对象架构中以兼容 MCP 规范。客户端将看到相应的架构，请求所请求类型的单个 “value” 字段。一旦客户端响应，提供的对象将被“解包”，标量值作为 ElicitationResult 对象的 data 字段返回给您的工具函数。
作为开发者，这意味着当您只需要标量值时，不必担心创建或访问结构化对象。
请求字符串
请求整数
请求布尔值
@mcp.tool
async def get_user_name(ctx: Context) -> str:
    """获取用户姓名。"""
    result = await ctx.elicit("您的姓名是什么？", response_type=str)
    
    if result.action == "accept":
        return f"您好，{result.data}！"
    return "未提供姓名"

无响应
有时，征询的目标只是让用户批准或拒绝一个动作。在这种情况下，您可以传递 None 作为响应类型，表示不期望响应。为了符合 MCP 规范，客户端将看到请求空对象作为响应的架构。在这种情况下，当用户接受征询时，ElicitationResult 对象的 data 字段将为 None。
无响应
@mcp.tool
async def approve_action(ctx: Context) -> str:
    """批准动作。"""
    result = await ctx.elicit("批准此动作？", response_type=None)

    if result.action == "accept":
        return do_action()
    else:
        raise ValueError("动作被拒绝")

约束选项
通常您希望将用户的响应限制为特定的值集。您可以通过使用 Literal 类型或 Python 枚举作为响应类型来实现这一点，或者通过将字符串列表传递给 response_type 参数作为便捷的快捷方式。
使用字符串列表
使用 Literal 类型
使用 Python 枚举
@mcp.tool
async def set_priority(ctx: Context) -> str:
    """设置任务优先级。"""
    result = await ctx.elicit(
        "什么优先级？", 
        response_type=["低", "中", "高"],
    )
    
    if result.action == "accept":
        return f"优先级设置为：{result.data}"

结构化响应
您可以通过使用数据类、类型字典或 Pydantic 模型作为响应类型来请求具有多个字段的结构化数据。请注意，MCP 规范仅支持具有标量（字符串、数字、布尔值）或枚举属性的浅层对象。
from dataclasses import dataclass
from typing import Literal

@dataclass
class TaskDetails:
    title: str
    description: str
    priority: Literal["低", "中", "高"]
    due_date: str

@mcp.tool
async def create_task(ctx: Context) -> str:
    """使用用户提供的详细信息创建新任务。"""
    result = await ctx.elicit(
        "请提供任务详细信息",
        response_type=TaskDetails
    )
    
    if result.action == "accept":
        task = result.data
        return f"已创建任务：{task.title}（优先级：{task.priority}）"
    return "任务创建已取消"

多轮征询
工具可以进行多次征询调用来逐步收集信息：
@mcp.tool
async def plan_meeting(ctx: Context) -> str:
    """通过逐步收集详细信息来规划会议。"""
    
    # 获取会议标题
    title_result = await ctx.elicit("会议标题是什么？", response_type=str)
    if title_result.action != "accept":
        return "会议规划已取消"
    
    # 获取持续时间
    duration_result = await ctx.elicit("持续时间（分钟）？", response_type=int)
    if duration_result.action != "accept":
        return "会议规划已取消"
    
    # 获取优先级
    priority_result = await ctx.elicit(
        "这是紧急的吗？", 
        response_type=Literal["是", "否"]
    )
    if priority_result.action != "accept":
        return "会议规划已取消"
    
    urgent = priority_result.data == "是"
    return f"会议 '{title_result.data}' 已规划为 {duration_result.data} 分钟（紧急：{urgent}）"

客户端要求
征询需要客户端实现征询处理程序。有关客户端如何处理这些请求的详细信息，请参阅客户端征询。
如果客户端不支持征询，调用 ctx.elicit() 将引发一个错误，指示不支持征询。
自定义提供方

LLM 采样

x

