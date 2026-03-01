# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a **Rime 输入法** configuration directory for 小鹤音形 (Flypy) Chinese phonetic input method on macOS (鼠鬚管/Squirrel).

## Key Concepts

- **Schema (方案)**: Input method configuration defined in `*.schema.yaml` files
- **Dictionary (词典)**: Character/word data in `*.dict.yaml` files
- **Build (编译)**: Rime compiles `.yaml` → `.bin` files in `build/` directory
- **Custom patches (定制)**: User modifications in `*.custom.yaml` files using patch syntax

## Common Commands

### Deploying Changes

Rime 配置修改后需要重新部署才能生效：

```bash
# macOS: 右键点击输入法学霸图标 → 重新部署
# 或在终端运行：
rm -rf build/*
# 然后在输入法偏好设置中点击"重新部署"
```

### Testing Schema Changes

修改 schema 或 dictionary 后，删除 `build/` 目录下的对应 `.bin` 文件使其重新生成：
```bash
rm build/flypy.*.bin
```

## File Structure

```
~/Library/Rime/
├── flypy.schema.yaml      # 主方案配置（音形输入）
├── flypydz.schema.yaml    # 辅助方案（叠字等）
├── flypydz.dict.yaml      # 字典数据
├── default.custom.yaml    # 全局定制
├── squirrel.custom.yaml   # macOS 界面定制（配色、字体）
├── rime.lua               # Lua 脚本入口
├── lua/
│   └── calculator_translator.lua  # 计算器插件
├── build/                 # 编译生成的二进制文件
│   └── *.bin
└── flypy_*.txt           # 用户词典文件
```

## Architecture

### Input Flow

1. **Speller (拼写器)**: 处理拼音编码输入 (`abcdefghijklmnopqrstuvwxyz;'`)，最长4码
2. **Translators (翻译器)**: 多级候选项来源
   - `table_translator`: 主词典翻译
   - `reverse_lookup_translator`: 反查（` 键）
   - `lua_translator`: Lua 脚本（日期、时间、计算器）
   - 用户词典 (`custom_phrase*`)
3. **Filters (过滤器)**: 简繁转换 (`simplifier`)、去重 (`uniquifier`)

### Key Bindings

| 按键 | 功能 |
|------|------|
| `;` | 次选（分号） |
| `,` | 逗号顶屏 |
| `.` | 句号顶屏 |
| `[` / `]` | 翻页 |
| `Shift+Space` | 全/半角切换 |
| `Ctrl+.` | 中/英标点切换 |
| `Ctrl+j` | 简/繁切换 |
| `` ` `` | 反查（后面跟拼音） |

### Lua Translators

- `orq` → 日期 (`2026年03月01日`)
- `ouj` → 时间 (`14:30`)
- `=<expression>` → 计算器（输入 `=1+1` 输出 `2`）

## Customization

### 修改候选项数量

在 `flypy.schema.yaml` 中：
```yaml
menu:
  page_size: 5  # 默认5个
```

### 修改配色

在 `squirrel.custom.yaml` 中修改 `preset_color_schemes/metro` 下的颜色值（RGB 顺序，十六进制，如 `0xe89f00`）。

### 添加用户词

在对应的 `.txt` 文件中添加词条（格式：`词组\t编码`），然后删除 `build/` 下相关 `.bin` 文件重新部署。

## Dictionary Priority

用户词典优先级（从高到低）：
1. `flypy_top` - 置顶词
2. `flypy_sys` - 系统词
3. `flypy_user` - 用户词
4. `flypy_full` - 全码词
5. `flypy_ok` - OK词

## Notes

- 这是配置目录而非代码项目，无需构建、测试或 lint
- `build/` 目录下的 `.bin` 文件是 Rime 自动编译生成的缓存
- 修改任何 `.yaml` 文件后需要重新部署生效
