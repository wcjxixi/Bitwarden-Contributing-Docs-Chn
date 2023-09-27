# 目录连接器

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/enterprise/directory-connector/)
{% endhint %}

Bitwarden 目录连接器是一个桌面应用程序，用于将您的 Bitwarden 企业组织同步到现有的用户和群组目录。

目录连接器接收的更新少于主客户端。为了降低维护成本，它拥有自己的共享 Javascript 库（以前称为 jslib）副本，位于 `jslib` 子目录中。

## 要求 <a href="#requirements" id="requirements"></a>

* [Node.js](https://nodejs.org/) v18 (LTS)
* Windows 用户：要编译应用程序中使用的本机节点模块，您需要 Visual C++ 工具集，可通过标准 Visual Studio 安装程序（推荐）或通过 `npm` 安装 `windows-build-tools` 获得。在[编译本机组件模块](https://github.com/Microsoft/nodejs-guidelines/blob/master/windows-environment.md#compiling-native-addon-modules)中查看更多信息。

## 构建说明 <a href="#build-instructions" id="build-instructions"></a>

1、克隆存储库：

```bash
git clone https://github.com/bitwarden/directory-connector.git
```

2、安装依赖项：

```bash
cd directory-connector
npm ci
```

3、运行应用程序：

{% tabs %}
{% tab title="GUI" %}
```bash
npm run electron
```
{% endtab %}

{% tab title="CLI" %}
```bash
npm run build:cli:watch
```

然后，从 `./build-cli` 文件夹运行命令：

```bash
cd ./build-cli

node ./bwdc.js --help

# Test sync
node ./bwdc.js test

# Real sync
node bwdc.js sync
```
{% endtab %}
{% endtabs %}

## 从目录服务同步 <a href="#syncing-from-a-directory-service" id="syncing-from-a-directory-service"></a>

要正确测试目录连接器，您需要一个目录来同步。我们有相关的设置说明：

* 一个 [Open LDAP Docker 镜像](open-ldap.md)（推荐）
* [JumpCloud](jumpcloud.md)

这些都是 LDAP 目录服务。如果您需要测试另一种类型，您应该能够找到提供该免费层级的服务的平台。
