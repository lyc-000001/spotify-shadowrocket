# spotify-shadowrocket

Spotify Premium module for Shadowrocket（小火箭）, adapted from the Loon plugin in [xzavier-amico/modules](https://github.com/xzavier-amico/modules).

去广告 + 解锁 Premium 状态与顺序/随机播放：改写 Spotify `bootstrap` 与 `user-customization-service` 两个接口的 protobuf 响应。

## 导入小火箭

模块地址（在小火箭「配置 → 模块 → +」里粘贴这个 URL 添加）：

```
https://raw.githubusercontent.com/lyc-000001/spotify-shadowrocket/main/spotify-premium.module
```

生效前提：

1. 小火箭 MITM 已开启，且根证书已安装并在 iOS 里设为信任。
2. 导入后彻底杀掉 Spotify 再重开。

## 适配说明

原插件的 JS 是为 Loon / Egern 写的，直接放进小火箭不生效。在模拟小火箭脚本环境的本地测试中，原版脚本会因为宿主缺少 `TextEncoder` / `TextDecoder` 而直接报错退出（`ReferenceError: TextEncoder is not defined`），`$done` 从未执行。

`spotify_premium_shadowrocket.js` = 一段宿主兼容头 + 原脚本（改写逻辑逐字节未改动）。兼容头只做三件事：

1. `TextEncoder` / `TextDecoder` 的 UTF-8 补丁（宿主已提供时不生效）。
2. 二进制 body 归一化：body 若以 `$response.bodyBytes` 到达，映射回脚本预期的 `$response.body`。
3. 状态码归一化：`$response.statusCode` 映射到 `$response.status`。

开关取原插件默认值（小火箭模块没有可视化开关）：伪装 Premium 开、去广告 开、解锁播放限制 开、日志 关。

本地测试：伪造的 customize 响应在无 TextEncoder 的环境中被成功改写（`type`、`catalogue` 写入 `premium`），两种 body 交付方式均通过。

## 文件

- `spotify-premium.module` — 小火箭模块（脚本规则 + MITM 主机名）
- `spotify_premium_shadowrocket.js` — 适配版脚本

## Credit

Original Loon plugin and script: xzavier-amico — https://github.com/xzavier-amico/modules
