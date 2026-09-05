# 设备 Token 鉴权实现导出

本目录为当时项目中的设备凭证校验相关代码整理导出，便于单独上传 GitHub。

## 包含文件

| 文件 | 说明 |
|------|------|
| `DeviceToken.java` | 注解：声明需要校验的接口 |
| `DeviceTokenAspect.java` | AOP：方法执行前读取 Header 并校验 |
| `DeviceTokenResolver.java` | 校验接口 |
| `DeviceTokenResolverImpl.java` | 校验实现：AES 解密 + 24h + Redis 10min + 设备分类校验 |
| `LehahaAesHolder.java` | AES key/iv 配置持有与加解密入口 |
| `AesUtil.java` | AES-128-CBC 工具（`LehahaAesHolder` 依赖） |

## 依赖（原项目已有，本导出未内嵌）

- `com.lehaha.common.utils.ServletUtils`
- `com.lehaha.common.utils.StringUtils`
- `com.lehaha.common.exception.ServiceException`
- `EquipmentMapper#countByIdAndCategory`
- Spring AOP / Redis `StringRedisTemplate`

## 使用

```java
@GetMapping("/xxx")
@DeviceToken
public AjaxResult xxx(...) { ... }
```

请求头默认：`Device-Token`

## 说明

- Token 明文：`equipmentId-categoryId-timestamp`
- 业务最大有效期：24 小时
- Redis 缓存：`device_token:{cipher}`，TTL 10 分钟
- `LehahaAesHolder` 中 `@Value` 请按你们配置文件填写真实 key/iv 配置项（上传公开仓库时勿提交真实密钥）
