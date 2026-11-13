# 升级连接与旧系统证书兼容

## 0.1.77解决什么问题

微鲸Android 5.0能解析GitHub域名，但原版升级连接返回SSLHandshakeException/Trust anchor for certification path not found。它当前收到的证书链由Sectigo DV E36、Root E46和USERTrust ECC组成，旧系统及原有补充包缺少相应受信任根。

本版为升级请求附带三份官方公开根证书：USERTrust ECC、Sectigo Public Server Authentication Root E46/R46。按证书DER的SHA-256固定指纹验证，拒绝缺失、重复、未知和无效的根。保留系统及原有信任、HTTPS域名验证和安装包摘要/包名/签名/版本验证，不安装电视系统CA。

证书来源：[Sectigo官方根证书说明](https://www.sectigo.com/knowledge-base/detail/Sectigo-Root-Certificates)、[官方中间证书与根证书列表](https://www.sectigo.com/knowledge-base/detail/Sectigo-Public-Intermediates-and-Roots)。准确下载地址和指纹见[证书来源清单](../licenses/update-root-sources.json)。

## 老版本如何获得修复

如果原版0.1.74–0.1.76已经无法验证升级服务器证书，它无法通过该连接自行取得修复。请先从电脑下载0.1.77，采用电视支持的U盘或ADB方式覆盖安装，保留数据。之后再使用“设置 → 家长设置 → 版本更新”。不需要卸载，不需要导入系统证书。

针对本次用户手测，另准备了仅本地使用的0.1.76证书兼容测试包作为升级起点；它与正式0.1.77除清单版本外内容一致。该测试包不替换GitHub上原有0.1.76附件，也不是原版0.1.74。

## 检查过什么

- 微鲸API21复现原版根证书验证失败；同机使用修复后的生产信任管理器，版本接口返回200，GitHub附件入口返回302，下载域名返回206并取得64字节APK头。
- 未关闭证书或主机名校验；原有根保留，未知/空证书包被拒绝。
- 电视版与通用版代码、资源、资产逐项一致；兼容测试包与正式电视版除清单版本外一致；两版签名验证通过。
- 该诊断没有安装QA应用，没有发起完整APK下载或系统升级。完整电视自升级仍交由用户手动验证，不能标为已经通过。

## 仍存在的边界

目前版本信息和安装包仍走GitHub及其附件域名，没有国内备用源。证书兼容修复只解决本次明确复现的信任问题，不能保证所有地区、运营商、路由设置及未来证书链都能访问。连接超时、DNS失败与证书失败应分别判断；电脑存在代理时下载成功不能视作电视无代理测试。
