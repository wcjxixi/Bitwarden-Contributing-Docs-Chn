# Password Manager

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/sdk/password-manager/)
{% endhint %}

Password Manager SDK 专为 Bitwarden 内部使用而设计，支持管理加密数据、密码库访问权限和用户身份验证等关键功能。该 SDK 使用 Rust 语言编写，用途广泛，可绑定多种平台，包括移动客户端（Kotlin 和 Swift）和网页客户端（JavaScript/TypeScript）。

本章节将指导如何使用 SDK 进行开发，以确保在移动和网络平台上的兼容性。它将涵盖结构化代码的最佳实践，解决特定平台的难题，并确保您的实现能够在 Bitwarden 移动和 Web 应用程序中无缝运行。
