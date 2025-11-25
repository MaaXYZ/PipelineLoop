# PipelineLoop

## 功能简介

通过新增 attach 字段的方式，为 MaaFramework Pipeline 提供循环功能

## 字段详情

### `PipelineLoop.loop`

**int** 类型

节点自身动作循环次数。

举例：

```jsonc
{
    "A": {
        "next": "LoopNode",
    },
    "LoopNode": {
        "recognition": "OCR",
        "action": "Click",
        "attach": {
            "PipelineLoop.loop": 3
        },
        "next": "B",
    },
    "B": {
    }
}
```

流水线为：`A` -> 识别 `LoopNode`.`OCR` -> 执行 `LoopNode`.`Click` -> 执行 `LoopNode`.`Click` -> 执行 `LoopNode`.`Click` -> 识别 `B`

### `PipelineLoop.max_hit`

**int** 类型

节点最大识别命中次数，超过视为识别失败。仅在单个 task 中生效，新 task 将重置次数。

举例：

```jsonc
{
    "A": {
        "next": "LoopNode",
    },
    "LoopNode": {
        "attach": {
            "PipelineLoop.max_hit": 3,
        },
        "next": "B",
    },
    "B": {
        "next": "C"
    },
    "C": {
        "next": ["LoopNode", "D"]
    }
}
```

流水线为：`A` -> `LoopNode`(1) -> `B` -> `C` -> `LoopNode`(2) -> `B` -> `C` -> `LoopNode`(3) -> `B` -> `C` -> (`LoopNode` 已超限，直接视为识别失败) -> `D`

## 本地调试

下载本插件的 release，将 `bin` 中的动态库复制到 `MaaFramework.dll` 所在目录的 `plugins` 文件夹中。

若使用 MaaDebugger, vscode 插件 等调试工具，可使用 everything 搜索 `MaaFramework.dll` 找到安装位置，再进行复制。（请期待各调试工具支持动态加载插件）

## 发布版本

1. 在您的 CI 中下载本插件

  ```yml
  - name: Download Plugin
    uses: robinraju/release-downloader@v1
    with:
      repository: MaaXYZ/PipelineLoop
      latest: true
      fileName: "*${{ matrix.os }}-${{ matrix.arch }}*"
      out-file-path: "deps/download_plugins"
      extract: true
      token: ${{ secrets.GITHUB_TOKEN }}
  ```

2. 将插件复制到安装文件夹

  ```yml
  - name: Install Plugin
    shell: bash
    run: |
      mkdir -p install/plugins
      cp deps/download_plugins/bin/* install/plugins/
  ```
