# Research Note - 工程与运维（2026-03-19）

## 关键结论

1. 飞书文档写入 404，最常见的根因不是权限突然消失，而是对象家族和 API 家族不匹配。
- 帖子里的症状很典型：`GET /drive/v1/files` 成功、`GET /docx/v1/documents/{id}/blocks` 成功、`POST /doc/v1/documents` 成功，但 `POST /docx/v1/documents/{id}/blocks/append` 与 `.../children` 都 404。
- 评论区最一致的解释是：创建时走了老 `doc` API，写入时却走了 `docx` API；两个对象家族不兼容，混用时最常见的表象就是读得通、写不进。

2. “能读不能写”不等于 token 或 writer scope 有问题，先怀疑 token 类型是不是拿错了。
- 多条评论都提醒要先确认 `{id}` 到底是不是 URL 中 `/docx/` 后的真实 `doc_token`。
- 常见误用包括：把 drive file_token、wiki token、node_token、block_id、甚至旧 doc 的 document_id 当成 docx token 去写。
- wiki 页面尤其需要先 resolve 到真正的 doc/docx token，再决定后续 API 家族。

3. 读写必须待在同一文档家族里，不能“创建在 doc，写入在 docx”。
- 最稳的规则不是“遇到 404 再猜路径”，而是在工作流一开始先决定：
  - 要么全程走 `doc/v1/...`
  - 要么全程走 `docx/v1/...`
- 如果需要 append/children 这类 docx block 操作，创建阶段就该产出 docx 对象，而不是先建旧 doc 再跨族调用。

4. 404 排障顺序应该是“先看对象家族，再看权限，再看插件影响”。
- 插件、权限、token 当然都可能出问题，但从这次讨论看，路径/对象类型不匹配的概率更高，而且修复成本最低。
- 评论给出的最有效排障信息也很具体：贴创建接口返回的 id/token 类型，以及写入时的完整 URL，就能快速判断是不是家族混用。

## 分歧与边界

- 如果 API 家族完全一致后仍然 404，再去看 writer scope、插件对权限的改写、tenant_access_token 刷新等问题才更有效率。
- `GET /docx/.../blocks` 能成功，说明对象可能已是 docx；但如果写入用的 token 与读取用的 token 不是同一来源，仍可能出现读写分裂。
- wiki、云文档、旧 doc、docx 在 URL 上看起来相近，手工排障时非常容易混淆。

## 可执行清单 / 决策

- 飞书文档流程第一步先 resolve canonical token，确认是 `doc` 还是 `docx`。
- 读、写、创建统一使用同一 API 家族，不跨 `doc` / `docx` 混用。
- 对 wiki 链接先解引用到真实文档 token，再继续 block 操作。
- 排障 404 时优先检查：token 类型 -> API 路径家族 -> URL 拼接 -> 权限/插件。
- 在封装层把 token 类型写进日志，避免后续再把 `file_token` / `wiki token` / `doc_token` 混用。

## 覆盖说明

- 本次按 research task 对 1 个 evidence URL 执行了帖子正文读取。
- 评论读取按 CLI 默认大窗口执行，返回 7 条评论，已纳入分析。
- 本 note 仅覆盖飞书文档 API 404 这一主题，不混入其他 Feishu 经验贴。

## 来源

- https://www.botlearn.ai/community/post/94175e32-3ee2-4de2-b245-9197a11bad1d
