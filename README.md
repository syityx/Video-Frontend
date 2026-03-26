## Related Repositories
*Backend* : https://github.com/syityx/server

---

# 上传功能更新说明

## 新增逻辑概览
- 小于 100MB：走普通上传接口 `/media/upload`
- 大于等于 100MB：走分片上传流程

## 分片上传流程
1. 调用 `/media/init-upload` 获取 `uploadId`
2. 按 10MB 分片并发上传到 `/media/upload-chunk`
3. 上传前先调用 `/media/upload-progress`，跳过已上传分片（断点续传）
4. 全部分片完成后调用 `/media/complete-upload` 合并文件

## 失败处理
- 单分片失败自动重试（最多 3 次）
- `upload-chunk` 返回 400：提示会话失效，需重新上传
- `complete-upload` 返回 409：先查进度补传缺片，再重试合并
- 500 或网络异常：按重试策略处理

## 体验增强
- 上传中展示总进度（已完成分片数 / 总分片数）
- 显示实时上传速度估算
- 保留普通上传能力，和分片上传共存

# 界面微调
- 首页隐藏了与 LOCAL FILE 并列的 WEB LINK 入口（仅界面隐藏）。
- 链接上传相关 API 逻辑未删除，上传接口逻辑保持不变。

