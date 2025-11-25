# PipelineLoop

## 功能简介

通过新增 attach 字段的方式，为 MaaFramework Pipeline 提供循环功能

## 字段详情

TODO...

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
