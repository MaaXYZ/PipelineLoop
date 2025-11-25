# PipelineExt

## 功能简介

通过新增 attach 字段的方式，为 MaaFramework Pipeline 提供非线性扩展逻辑。

## 字段详情

### `PipelineExt.loop`

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
            "PipelineExt.loop": 3
        },
        "next": "B",
    },
    "B": {
    }
}
```

流水线为：`A` -> 识别 `LoopNode`.`OCR` -> 执行 `LoopNode`.`Click` -> 执行 `LoopNode`.`Click` -> 执行 `LoopNode`.`Click` -> 识别 `B`

### `PipelineExt.max_hit`

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
            "PipelineExt.max_hit": 3,
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

### `PipelineExt.goto` + `PipelineExt.label`

**string** 或 **list<string,>** 类型

在前序节点中添加 label，后序 goto 节点将会在动作执行完成后，使用 goto list 替换 next list 进行后续流程。

请留意，仅已执行的前序节点的 label 才会生效，并且会覆盖之前相同的 label。即 goto 会跳转到最近一次的同名 label 中。

#### 举例 1

```jsonc
{
    "Entry": {
        "attach": {
            "PipelineExt.label": "哦在这停顿",
        },
        "next": "B"
    },
    "Entry_2": {
        "attach": {
            "PipelineExt.label": "哦在这停顿",
        },
        "next": "B"
    },
    "B": {
        "next": "C"
    },
    "C": {
        "attach": {
            "PipelineExt.goto": "哦在这停顿",
        },
    },
}
```

可用于实现 `B`, `C` 节点的复用，流水线为：

- `Entry` -> `B` -> `C` -> `Entry` -> ...
- `Entry_2` -> `B` -> `C` -> `Entry_2` -> ...

#### 举例 2

若 label 为数组，即代表其可作为多个标记。

goto 逻辑与 next 相同，若为数组则会依次进行识别。若其中出现未曾执行过的 label，则认为该 label 识别失败

```jsonc
{
    "A": {
        "attach": {
            "PipelineExt.label": [ "标记111", "标记222" ],
        },
        "next": "B"
    },
    "B": {
        "attach": {
            "PipelineExt.label": "标记333",
        },
        "next": "C"
    },
    "C": {
        "attach": {
            "PipelineExt.goto": [ "标记333", "标记111" ],
        },
    },
}
```

流水线为: `A` -> `B` -> `C` -> `C`.`next` = [ `B`, `A` ]

## 本地调试

下载本插件的 release，将 `bin` 中的动态库复制到 `MaaFramework.dll` 所在目录的 `plugins` 文件夹中。

若使用 MaaDebugger, vscode 插件 等调试工具，可使用 everything 搜索 `MaaFramework.dll` 找到安装位置，再进行复制。（请期待各调试工具支持动态加载插件）

## 发布版本

1. 在您的 CI 中下载本插件

  ```yml
  - name: Download Plugin
    uses: robinraju/release-downloader@v1
    with:
      repository: MaaXYZ/PipelineExt
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
