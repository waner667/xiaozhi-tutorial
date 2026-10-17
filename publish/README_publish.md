# 发布到腾讯文档

## 当前线上位置

| | |
|---|---|
| 空间 | [小智 DIY 教程 · ESP32-C6](https://docs.qq.com/space/DZXBtZVh1eGxJWWRJ) |
| space_id | `epmeXuxlIYdI` |
| 总览节点 | `exOOamjfJasG`（小智 DIY 教程站 · 总览） |
| 01 · 入门：语音助手 | 文件夹 `eoihdzHBncbw` / 文档 `ebKkLCCBlgkR` |
| 02 · 进阶：机器狗 | 文件夹 `eoFAwomqmfwo` / 文档 `eJMWmtdHMiqa` |
| 03 · 排查手册 | 文件夹 `ebobnNgGThws` / 文档 `ewNFROVNTbPC` |

四篇都是 `doc_type: word`（docx 导入），**可以在腾讯文档里直接编辑**。

---

## 为什么走 docx 而不是 md

**md 导入出来的是 markdown 型节点，不受文档编辑 API 支持**
（`resolve_document_structure` 报 `no rows in result set`），
既不能编辑，也就永远插不了图。

教程一、教程二都要放接线图，所以必须走 docx 路线。

---

## 重新发布

改完 `site/` 下的 md 之后：

```bash
# 1. 生成 docx（顺带算好 size / md5）
python E:/QRS/Project/xiaozhi/xiaozhi_xiage/_build_tutorial_docx.py

# 2. 跑脱敏 + 死链 + 缺图自检（必须 0 问题）
python E:/QRS/Project/xiaozhi/xiaozhi_xiage/_check2.py

# 3. 按 _build/docx_report.txt 里的 size / md5 走导入流程：
#    pre_import → cos_put --put → async_import → import_progress
#    然后 move_file_to_space 挂到对应文件夹
# 4. 确认新节点挂好之后，删掉旧节点
```

完整流程见 skill `tencent-docs-md-upload`。

**顺序很重要**：先确认新节点已经在知识库里显示正常，再删旧节点。
否则中途失败会两边都没有。

---

## ⚠️ 换图 / 改图必须重建

即使文档已经是 word 类型，**换图也只能重建 docx 重新导入**。

`doc.replace_image` 和 `doc.upload_image` 都只收 base64 字符串，
一张 400KB 的接线照 base64 后约 560KB 字符，塞不进工具参数。

**所以换图后 `file_id` 会变。** 换之前先确认没人引用旧 ID。

---

## 关于内部互链：为什么不写成真链接

已经实测过一轮，结论是**不要做**，原因是结构性的：

`link_overrides.json` 可以把站内互引写死成 `resourceId=xxx` 的真 URL，
`md2docx` 也确实会导出成可点的 `w:hyperlink`。技术链路是通的。

但它有鸡生蛋死结：

1. 节点 ID 只有导入后才确定 → 第一轮导入用的是「纯文字互引」
2. 拿到 ID 后写 `link_overrides` 重建 → **新节点 ID 又变了**
3. 旧节点一删，写死在正文里的 URL 全部指向不存在的节点

也就是说每修一轮链接就要再重导一轮，永远收敛不了。
除非接受「链接永远指向上一代的节点」这种僵尸链接。

**知识库形态下这个代价不值得。** 左侧目录树本身就承担了跳转，
正文里保留可读的中文标题（如「教程二 · 小智机器狗」）已经够用 ——
读者看到名字就知道去哪找。

站内链接走 `prep_md.rewrite()` 规则 2：命中同批文档 → 替换成可读中文标题的纯文字。
外部 URL（乐鑫文档、GitHub）会导出成真正的可点超链接。

---

---

## 外部图片来源：Flash 下载工具界面图

教程一 4.6 / 4.9 节用了 5 张乐鑫 Flash 下载工具的界面截图，
取自小智官方文档站（docusaurus 静态资源，可直接下载，不需要鉴权）：

```
https://xiaozhi.me/xz-docs/assets/images/flash-download-0N-<hash>.png
```

**为什么可以借**：这些图展示的是**第三方工具的界面**，与具体板型无关，
任何用 Flash 下载工具的人看到的都是这个界面。

**为什么接线图不能借**：虾哥那篇的接线图是 **ESP32-S3 面包板**的，
引脚和本文的 C6 完全不同（S3: OLED SDA=41/SCL=42、功放 BCLK=15/LRC=16；
C6: OLED SDA=11/SCL=10、BCLK/LRC 与麦克风共用 4/5）。照着接会接错线。

已存的文件（`assets/`）：

| 文件 | 内容 |
|---|---|
| `flash-tool-03-chiptype.png` | 芯片型号 / 工作模式 / 加载模式设置窗|
| `flash-tool-main.png` | SPIDownload 主界面（勾选框 + bin + 0x0 + START + COM） |
| `flash-tool-finish.png` | 烧录完成 FINISH |
| `flash-tool-devmgr.png` | 工具里选 COM 端口那块的截图 |
| `flash-tool-ch343.png` | 设备管理器里认出 CH343 那一项 |

README 的「许可与免责」里已标注来源、版权归原作者、**截图里是 S3 而本文用 C6**。

---

## 本轮（2026-10-17 晚）改了什么

**烧录章增加「图形工具直烧 bin」路线。**

先核实了一件事：**官方 Release 里没有 C6 面包板的预编译包**。
v2.5.2 有 190 个固件包，但上游 `main/boards` 51 个板型目录里
只有 S3 版 `bread-compact-wifi`，C6 版 `bread-compact-c6` 是本地自建的。

所以不能写「下载现成 bin 直烧」——那条路对 C6 不通。
实际可用的写法是**两条路并列**：

- 路线 A：`idf.py flash`，一条命令，最省心
- 路线 B：`python scripts/release.py` 打包出 `build/merged-binary.bin`，
  之后用乐鑫 Flash 下载工具 GUI 烧，**不用记偏移地址**

路线 B 对教程二尤其重要（要反复改固件反复烧）。

**同时把教程一整体降难度**（★★★☆☆ → ★★☆☆☆）：

- 材料表加「买的时候怎么说」和参考价钱
- 接线章前加「先认清面包板」—— 凹槽断开 / 同列相通，新手最缺的前置
- 验收章从「看日志里程碑」改成「你做什么 → 应该看到什么」
- 速查表删掉 esptool 五个偏移地址的命令（小白用不到，写错反而危险）

---

## 上一轮（2026-10-17 下午）改了什么

**内容结构整体重写：从「原理 + 排查」改成「照着做的操作手册」。**

改之前的主线是 `成品 → 原理 → 排查 → 经验`，读者读完知道原理但不知道从哪下手。
改之后的主线是 `成品 → 材料 → 接线 → 烧录 → 配网 → 验收 → 常见问题`，
深度内容全部收进文末附录。

- 教程一：8 章主线 + 5 个附录（屏为什么晚亮 3 秒 / IDF 6.1 的坑 / 误判复盘）
- 教程二：9 章主线 + 5 个附录（duty 量纲 / 为什么用 C6 / 软件偏置）
- 新增「买之前要确认」「上电前检查清单」这类**防买错/防烧板**的章节

**顺带修了 `md2docx.py` 的一个真 bug**：
`[文字](url)` 原来只渲染成蓝色下划线，**URL 被整个丢弃**，
所有外部链接都是死链。现在写标准的 `w:hyperlink` + `r:id` 关系。
