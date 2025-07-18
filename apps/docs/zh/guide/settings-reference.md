# 设置参考

目前 `S3 Image Port` 的设置包含三部分：

1. S3 存储桶设置
2. 画廊设置
3. 上传设置

## S3 储存桶设置 { #s3-settings }

端点、储存桶名称、区域、Access Key 和 Secret Key 都是创建 S3 储存桶时会提供的，不再赘述。

### 使用路径形式 API { #use-path-style-api }

对于绝大部分 S3 供应商来说，都应该保持这个选项关闭。这是为了某些特别旧的 S3 供应商准备的回退。

关于 path name 和 virtual hosted-style 的更多信息，可以参考 [AWS S3 文档](https://docs.aws.amazon.com/AmazonS3/latest/userguide/VirtualHosting.html)。

### 公共 URL { #public-url }

储存桶中的图片需要可以通过一个链接直接访问。

例如，一张图片在储存桶中的路径是 `i/2024/05/29/name.jpg`，而你可以通过 `https://i.yfi.moe/i/2024/05/29/name.jpg` 这个链接直接（不需要验证地）访问到它，那么 `https://i.yfi.moe/` 就是你需要填写的 Public URL.

如果你直接使用了某些 S3 兼容服务的“公开储存桶”功能，也是同理。例如，对于 Cloudflare R2，它应该形如 `https://pub-<一堆字符>.r2.dev`。对于腾讯云 COS，应该形如 `https://<BucketName-APPID>.cos.<Region>.myqcloud.com`

## 上传设置 { #upload-settings }

### 键（名称）模板（Key Template） { #key-template }

上传到 S3 时的命名模板，以 <code v-pre>{{}}</code> 包裹的占位符会被替换。

支持以下占位符：

- <code v-pre>{{year}}</code>: 年份。例如 `2024`
- <code v-pre>{{month}}</code>: 月份（两位数）。例如 `05`
- <code v-pre>{{day}}</code>: 日期（两位数）。例如 `29`
- <code v-pre>{{timestamp}}</code>: Unix 时间戳。例如 `1732847234567`（毫秒）
- <code v-pre>{{filename}}</code>: 文件名（不包含扩展名）。例如 `image`
- <code v-pre>{{ext}}</code>: 文件扩展名。例如 `jpg`
- <code v-pre>{{ulid}}</code>: 唯一标识符 (ULID)。例如 `01BX5ZZKBKACTAV9WEVGEMMVR0`
- <code v-pre>{{ulid-dayslice}}</code>: ULID 的日期切片（推荐与 year、month、day 组合使用）。例如 `5zzkbk-mmvr`
- <code v-pre>{{random}}</code>: 随机字符串（已弃用，建议使用 `ulid-dayslice`）

默认模板：<code v-pre>i/{{year}}/{{month}}/{{day}}/{{ulid-dayslice}}.{{ext}}</code>

示例结果：<code v-pre>i/2024/05/29/5zzkbk-mmvr.jpg</code>

### 图像压缩和转换

会在上传时按照给定的参数对图片进行处理。

## 画廊设置 { #gallery-settings }

### 自动刷新 { #auto-refresh }

每次加载画廊时自动刷新。如果启用，画廊的缓存会和 S3 储存桶更好的同步，但会出现更多的 `ListObjects` 请求，可能会小幅增加 S3 费用。
