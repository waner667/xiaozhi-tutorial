# 发布到腾讯文档

## 当前线上位置

| | |
|---|---|
| 空间 | [小智 DIY 教程 · ESP32-C6](https://docs.qq.com/space/DZXBtZVh1eGxJWWRJ) |
| space_id | `epmeXuxlIYdI` |
| 总览节点 | `eeCCEnsOdLSl`（小智 DIY 教程站 · 总览） |
| 01 · 入门：语音助手 | 文件夹 `eoihdzHBncbw` / 文档 `ehtyhowTulMA` |
| 02 · 进阶：机器狗 | 文件夹 `eoFAwomqmfwo` / 文档 `eDtFhOYeaVML` |
| 03 · 排查手册 | 文件夹 `ebobnNgGThws` / 文档 `ewQlojuerdGN` |

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
python _build_tutorial_docx.py

# 2. 按 _build/docx_report.txt 里的 size / md5 走导入流程：
#    pre_import → cos_put --put → async_import → import_progress
#    然后 move_file_to_space 挂到对应文件夹
```

完整流程见 skill `tencent-docs-md-upload`。

---

## ⚠️ 换图 / 改图必须重建

即使文档已经是 word 类型，**换图也只能重建 docx 重新导入**。

`doc.replace_image` 和 `doc.upload_image` 都只收 base64 字符串，
一张 400KB 的接线照 base64 后约 560KB 字符，塞不进工具参数。

**所以换图后 `file_id` 会变。** 换之前先确认没人引用旧 ID。

---

## 关于内部互链

现在四篇之间的互引在 docx 里是**可读中文标题的纯文字，不是可点链接**。

原因：节点 ID 导入后才确定，链接只能在导入那一刻写死。
等四篇都拿到 ID 之后，可以在 `_build/link_overrides.json` 里写入：

```json
{
  "01-xiaozhi-ai.md": "https://docs.qq.com/space/epmeXuxlIYdI?resourceId=ehtyhowTulMA&mode=wiki_mode"
}
```

再重跑 `_build_tutorial_docx.py`，新导出的版本就会带真链接。

⚠️ 代价：**所有四篇都要重建一遍**，因为链接是双向的。
只有当你打算长期不再改结构时才值得做。