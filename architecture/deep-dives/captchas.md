# Captcha

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/deep-dives/captchas/)
{% endhint %}

{% hint style="info" %}
\[**译者注**]：[CAPTCHA](https://zh.wikipedia.org/zh-cn/%E9%AA%8C%E8%AF%81%E7%A0%81)，又称验证码。（Completely Automated Public Turing test to tell Computers and Humans Apart）全自动区分计算机和人类的图灵测试，是一种区分用户是机器或人类的公共全自动程序。
{% endhint %}

## 何时需要 Captcha？ <a href="#when-are-captchas-required" id="when-are-captchas-required"></a>

Bitwarden 代码库中的两个位置需要 Captcha：

* 通过 `[CapchaProtected]` 属性的中注册
* 通过 `ResourceOwnerPasswordValidator` 的登录

对于是否需要 Captcha，每种情况的验证都略有不同。

### 注册：\[CaptchaProtected] 匿名端点 <a href="#registration-captchaprotected-anonymous-endpoint" id="registration-captchaprotected-anonymous-endpoint"></a>

使用 `[CaptchaProtected]` 属性保护的唯一端点是身份服务上的 `/register` 端点。该端点是匿名的，因此没有身份验证中间件可以执行 Captcha 检查。

对于这些请求，如果满足以下任一条件，则服务器需要 Captcha：

* CloudFlare `x-Cf-Is-Bot` 标头出现在请求中，或者
* `ForceCaptchaRequired` 设置已启用

### 登录：使用资源所有者密码身份验证对令牌请求进行身份验证 <a href="#login-token-requests-authenticated-with-resource-owner-password-authentication" id="login-token-requests-authenticated-with-resource-owner-password-authentication"></a>

身份服务中的 `/identity/connect/token` 的请求通过 `ResourceOwnerPasswordValidator` 进行验证。在此验证器中，我们执行不同的检查以查看是否需要 Captcha，因为端点已通过身份验证并且我们从请求中了解用户（假设他们已成功通过身份验证）。

对于已知设备，不需要 Captcha。此检查是在应用以下任何规则之前执行的。

对于这些请求，如果满足以下任一条件，则服务器需要 Captcha：

* CloudFlare `x-Cf-Is-Bot` 标头出现在请求中
* `ForceCaptchaRequired` 设置已启用
* 此实例是云托管的，并且用户的电子邮件地址未经验证
* 登录失败次数大于 `MaximumFailedLoginAttempts` 的设置

## Captcha 在我们的代码中如何工作？ <a href="#how-do-captchas-work-in-our-code" id="how-do-captchas-work-in-our-code"></a>

在较高级别上，服务器**发起**并**验证** Captcha 请求，客户端通过**显示Captcha** 并**收集响应**以验证服务器端来处理该要求。

更详细地，该过程如下：

1. 服务器使用上面定义的规则接收需要 Captcha 的请求。
2. 然后，服务器响应包含 `HCaptcha_SiteKey` 的响应，客户端将其解释为「此请求需要 Captcha」。
3. 前端使用 `CaptchaProtectedComponent` 上的 `showCaptcha()` 方法根据此响应显示 Captcha `<div>` 。
4. `CaptchaProtectedComponent` 将 `CaptchaIFrame` 组件作为输入，其中包含实际的 Captcha UI。
5. `CaptchaProtectedComponent` 通过将 `captchaToken` 属性设置为 Captcha 响应中的值来处理 `CaptchaIFrame` 的 `successCallback` 。
6. 然后，用户可以再次发起操作（登录、注册等）。 `captchaToken` 将作为正文中的另一个元素与请求一起发送。
7. 服务器通过 `POST` 将包含令牌和我们的站点密钥的消息发送到 [https://hcaptcha.com/siteverify](https://hcaptcha.com/siteverify) 来验证 `HCaptchaValidationService` 中的请求。

### 什么是 Captcha 绕行令牌？ <a href="#what-is-a-captcha-bypass-token" id="what-is-a-captcha-bypass-token"></a>

在某些情况下，同一用户向受 Captcha 保护的端点之一发出多个请求。我们希望基于后续请求之前已经填写过 Captcha 的事实来「绕过」Captcha 请求，即使根据上述规则的定义，这些后续请求需要 Captcha 。

我们使用 Captcha 绕行令牌来完成此操作。这是验证 Captcha 时在服务器上为第一个请求创建的令牌，它包含：

* 用户 ID
* 用户邮箱
* 到期日期（生成后 5 分钟）

由于 Captcha 绕行令牌是使用用户信息生成的，因此仅在登录的 Captcha 验证时对其进行检查。检查注册的 Captcha 验证没有意义，因为注册用户没有有效的用户 ID。

该令牌使用服务器上的数据保护密钥进行保护，并以 `BWCaptchaBypass_{tokenContents}` 格式在 `CaptchaBypassToken` 元素中返回。

然后，客户端可以在接下来的 5 分钟时间，对内来自同一用户的后续请求中使用该 `captchaResponse`，以绕过 Captcha 请求。服务器端代码处理此绕行令牌的方式与处理从 Captcha UI 显示中收集的 `captchaResponse` 的方式相同。

大多数用户流程中不需要 Captcha 绕行令牌，因为用户是从已知设备登录的。从已知设备登录会绕过所有 Captcha 请求，因此不需要令牌。
