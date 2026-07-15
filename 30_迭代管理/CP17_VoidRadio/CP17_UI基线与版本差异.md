---
产品: "[[CP17_网络收音机]]"
类型: UI基线
状态: 进行中
created: 2026-07-11
updated: 2026-07-15
aliases:
  - CP17 UI基线
  - Void Radio UI版本差异
---
# CP17 UI 基线与版本差异

## 用途

本文件是 CP17 局部 UI 调整的可信入口，只保存可快速定位当前视觉基线的结构化信息。完整代码差异仍由 Git 保存，不在 AIPM 重复存储大段 patch。

默认工作流：

1. 先读本文件，确认当前 UI 基线 commit、关键尺寸、约束和截图。
2. 执行一次当前工作树与基线 commit 的目标范围 diff。
3. 只有索引缺失、commit 不可解析或现状与截图冲突时，才继续查询 Git 历史。
4. 完成修改后运行 `qa/update-cp17-ui-baseline.ps1` 生成 Git 事实摘要，再补充尺寸和真机结论。

## 当前可信基线

| 项目 | 值 |
|---|---|
| 当前开发版本 | V1.1.3 |
| 分支 | `codex/v1-1-3-ux-localization` |
| 上一版本 commit | `99eb199262f5bb17b3773fb183edd00176304b54` |
| 上一版本 tag | 未打 tag |
| 当前冻结 commit | `cfae8237d81cd72466dc5ee45ebd44ce7a2f06c3` |
| 当前 tag | `v1.1.3-rc.2` |
| 当前工作树 | 冻结后干净，分支已推送 |
| 基线日期 | 2026-07-12 |
| 主要 UI 目录 | `lib/home/`、`lib/map/`、`lib/standby/` |
| 真机 | moto G55，普通空间，1080 x 2400 |

## 版本差异

### V2.0-dev（明亮信号主题）

| 字段 | 内容 |
|---|---|
| 分支 | `codex/v2-signal-blue-theme` |
| Commit | `90f4e49` |
| 对比版本 | V2 私人电台 `89978b3` |
| 日期 | 2026-07-15 |
| 修改组件 | 主题令牌、首页背景、快捷按钮、调谐区、推荐频道、播放控制 |
| 保持不变 | 首页尺寸、播放行为、暖金默认主题、地图低饱和原则 |

主题实现从全局 `_VoidPalette` getter 升级为 `ColorScheme + ThemeExtension + _VoidComponentTokens`。新增主题不在 Widget 中写主题名特判，组件只读取语义令牌。

截图证据：

| 场景 | 路径 | 结论 |
|---|---|---|
| 竖屏首页 | `F:\git项目整理\void-radio-app\qa-output\v2_0_203_luminous_home_clean.png` | 页面、功能面、调谐与频道层级清楚 |
| 地图页 | `F:\git项目整理\void-radio-app\qa-output\v2_0_203_luminous_map.png` | 低饱和亮地图与蓝灰抽屉并存，按钮和列表可辨识 |
| 横屏首页 | `F:\git项目整理\void-radio-app\qa-output\v2_0_203_luminous_landscape.png` | 无重叠或 overflow |

验证状态：`flutter test --no-pub` 57 / 57，ASCII junction 下 `flutter analyze --no-pub` 0 issue，moto G55 强停重启后主题保持。

### V1.1.3（RC2）

| 字段 | 内容 |
|---|---|
| Git tag | `v1.1.3-rc.2` |
| Commit | `cfae8237d81cd72466dc5ee45ebd44ce7a2f06c3` |
| 对比版本 | V1.1.2：`99eb199` |
| 日期 | 2026-07-12 |
| 修改组件 | 首页频率卡、调谐区、推荐频道、播放控制；地图主题；横屏首页与 Now Playing；显示语言 |
| 修改文件 | `lib/home/home_widgets.dart`、`lib/home/station_chips.dart`、`lib/map/`、`lib/standby/clock_mode.dart`、`lib/main.dart`、`lib/l10n/` |

首页关键尺寸：

| 区域 | V1.1.2 | V1.1.3 当前值 | 约束 |
|---|---:|---:|---|
| 主频率卡高度 | 296 | 260 | 竖屏首页不能因主卡过高挤压调谐和推荐区 |
| 调谐区高度 | 136 | 118 | 必须完整显示，不要求用户上下滚动才能看到刻度 |
| 推荐频道轨道高度 | 106 | 88 | 推荐频道仍保留独立卡片轮廓 |
| 推荐频道 chip 高度 | 74 | 62 | 频道名和频率不得截断 |
| 推荐频道 chip 宽度 | 124（中间调整值） | 132 | `FM 100.3` 等频率标签必须完整显示 |
| 底部播放区高度 | 90 | 72 | 上一台、播放/暂停、下一台保持稳定命中区 |
| 主播放按钮直径 | 64 | 54 | 不得与相邻按钮误触，图标状态须与播放状态一致 |

保持不变的 UI 约束：

- moto G55 竖屏首页必须同屏完整显示调谐区、推荐频道和底部播放控制，不通过主页面纵向滚动弥补比例失衡。
- 调谐区保持嵌入页面的低悬浮表达；推荐频道仍保留清晰卡片轮廓，两者不能被误改成同一种视觉层级。
- 横屏首页和横屏 Now Playing 不得出现遮挡或 RenderFlex overflow。
- 首页布局调整不得改变播放、切台、收藏、地图、床头钟和独立信号的行为。
- 主题切换必须保持文字、图标、卡片边界和地图抽屉的可辨识对比度。

截图证据：

| 场景 | 路径 | 结论 |
|---|---|---|
| 竖屏首页比例 | `F:\git项目整理\void-radio-app\qa-output\v1_1_3_home_no_lyrics.png` | 调谐、推荐频道和播放区同屏，`FM 100.3` 完整显示；首页只保留真实曲目，不显示歌词卡 |
| Now Playing 歌词 | `F:\git项目整理\void-radio-app\qa-output\v1_1_3_now_playing_only.png` | 歌词仅在二级沉浸页显示，支持时间轴高亮与校准 |
| 横屏首页 | `F:\git项目整理\void-radio-app\qa-output\v1_1_3_home_landscape.png` | 无 overflow |
| 横屏 Now Playing | `F:\git项目整理\void-radio-app\qa-output\v1_1_3_now_playing_landscape.png` | 无 overflow |
| Polar Graphite 设置页 | `F:\git项目整理\void-radio-app\qa-output\v1_1_3_settings_polar_fixed.png` | 对比度正常 |
| Polar Graphite 地图页 | `F:\git项目整理\void-radio-app\qa-output\v1_1_3_map_polar_english.png` | 已留图；marker 点选与抽屉联动仍需最终回归 |
| 暖金石墨首页 | `F:\git项目整理\void-radio-app\qa-output\v1_1_3_warm_gold_home.png` | 暖炭墨为底、香槟金为克制强调，布局不变 |
| 暖金石墨地图 | `F:\git项目整理\void-radio-app\qa-output\v1_1_3_warm_gold_map_selected.png` | 暖石墨瓦片、金色 marker、暗色选中卡和抽屉联动通过 |
| Google Play 首页 | `F:\git项目整理\void-radio-app\store-assets\google-play\screenshots-phone\phone-01-home.png` | 当前暖金主题与 UFM 100.3 默认入口 |
| Google Play 歌词页 | `F:\git项目整理\void-radio-app\store-assets\google-play\screenshots-phone\phone-05-now-playing.png` | 真实曲目和同步歌词状态，无占位文案 |

验证状态：

- `flutter analyze --no-pub`：通过，No issues found。
- `flutter test --no-pub`：38 项全部通过。
- 真机：横屏、语言切换、UFM ICY/官方元数据回退、同步歌词、暖金地图 marker 与抽屉联动均已验证。
- RC 短 L3：`qa-output/cp17_l3_device_smoke_20260712_080116.summary.txt`，后台、熄屏和媒体键通过。
- RC2 元数据回切：`qa-output/ufm_rc2_return.png`，`UFM 100.3 -> Radio Paradise -> UFM 100.3` 后首页和系统媒体会话均显示“周兴哲 - 怎么了”。

复现命令：

```powershell
git diff 99eb199..HEAD -- lib/home lib/map lib/standby lib/main.dart

.\qa\update-cp17-ui-baseline.ps1 `
  -Version V1.1.3 `
  -From 99eb199 `
  -To HEAD
```

### V1.1.2

| 字段 | 内容 |
|---|---|
| Git tag | 未打 tag |
| Commit | `99eb199262f5bb17b3773fb183edd00176304b54` |
| 日期 | 2026-07-11 |
| 说明 | 播放与地图发布路径加固；作为 V1.1.3 UI 收口的 Git 基线 |

## 更新规则

- 每个可交付 UI 版本冻结后回填 tag、完整 commit、上一版本 commit、关键尺寸、截图和测试结果。
- 脚本生成内容是 Git 事实草稿，不替代人工视觉验收，也不自动覆盖本文件。
- 不保存完整 patch；需要完整差异时使用本版本记录的一条目标范围命令复现。
- 历史版本资料不完整时明确写“未打 tag / 待补录”，不得推测或补造。
