---
title: "Custom Directory Collector"
source: "https://docs.pytest.org/en/stable/example/customdirectory.html"
version: "stable"
---

# Custom Directory Collector

> 原始文档来源：https://docs.pytest.org/en/stable/example/customdirectory.html

---

Using a custom directory collector

By default, pytest collects directories using pytest.Package, for directories with __init__.py files, and pytest.Dir for other directories. If you want to customize how a directory is collected, you can write your own pytest.Directory collector, and use pytest_collect_directory to hook it up.

A basic example for a directory manifest file

Suppose you want to customize how collection is done on a per-directory basis. Here is an example conftest.py plugin that allows directories to contain a manifest.json file, which defines how the collection should be done for the directory. In this example, only a simple list of files is supported, however you can imagine adding other keys, such as exclusions and globs.

# content of conftest.py
from __future__ import annotations

import json

import pytest

class ManifestDirectory(pytest.Directory):
    def collect(self):
        # The standard pytest behavior is to loop over all `test_*.py` files and
        # call `pytest_collect_file` on each file. This collector instead reads
        # the `manifest.json` file and only calls `pytest_collect_file` for the
        # files defined there.
        manifest_path = self.path / "manifest.json"
        manifest = json.loads(manifest_path.read_text(encoding="utf-8"))
        ihook = self.ihook
        for file in manifest["files"]:
            yield from ihook.pytest_collect_file(
                file_path=self.path / file, parent=self
            )

@pytest.hookimpl
def pytest_collect_directory(path, parent):
    # Use our custom collector for directories containing a `manifest.json` file.
    if path.joinpath("manifest.json").is_file():
        return ManifestDirectory.from_parent(parent=parent, path=path)
    # Otherwise fallback to the standard behavior.
    return None

You can create a manifest.json file and some test files:

{
    "files": [
        "test_first.py",
        "test_second.py"
    ]
}

# content of test_first.py
from __future__ import annotations

def test_1():
    pass

# content of test_second.py
from __future__ import annotations

def test_2():
    pass

# content of test_third.py
from __future__ import annotations

def test_3():
    pass

And you can now execute the test specification:

customdirectory $ pytest
=========================== test session starts ============================
platform linux -- Python 3.x.y, pytest-9.x.y, pluggy-1.x.y
rootdir: /home/sweet/project/customdirectory
configfile: pytest.ini
collected 2 items

tests/test_first.py .                                                [ 50%]
tests/test_second.py .                                               [100%]

============================ 2 passed in 0.12s =============================

Notice how test_three.py was not executed, because it is not listed in the manifest.

You can verify that your custom collector appears in the collection tree:

customdirectory $ pytest --collect-only
=========================== test session starts ============================
platform linux -- Python 3.x.y, pytest-9.x.y, pluggy-1.x.y
rootdir: /home/sweet/project/customdirectory
configfile: pytest.ini
collected 2 items

<Dir customdirectory>
  <ManifestDirectory tests>
    <Module test_first.py>
      <Function test_1>
    <Module test_second.py>
      <Function test_2>

======================== 2 tests collected in 0.12s ========================
