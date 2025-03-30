# Web

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/sdk/password-manager/web/)
{% endhint %}

Password Manager SDK 支持基于 Web 的客户端，允许开发人员将核心密码管理功能集成到 JavaScript/TypeScript 环境中。SDK 打包为 `@bitwarden/sdk-internal` 下的 NPM 模块，并使用 WebAssembly (`wasm`) 实现与 Rust 代码的交互。本章节将提供在浏览器环境中使用 SDK 的指南和最佳实践。
