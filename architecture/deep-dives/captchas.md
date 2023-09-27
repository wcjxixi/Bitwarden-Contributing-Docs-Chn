# Captcha

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/deep-dives/captchas/)
{% endhint %}

## 何时需要 Captcha？ <a href="#when-are-captchas-required" id="when-are-captchas-required"></a>

### 注册：\[CaptchaProtected] 匿名端点 <a href="#registration-captchaprotected-anonymous-endpoint" id="registration-captchaprotected-anonymous-endpoint"></a>

### 登录：使用资源所有者密码身份验证对令牌请求进行身份验证 <a href="#login-token-requests-authenticated-with-resource-owner-password-authentication" id="login-token-requests-authenticated-with-resource-owner-password-authentication"></a>

## Captcha 在我们的代码中如何工作？ <a href="#how-do-captchas-work-in-our-code" id="how-do-captchas-work-in-our-code"></a>

### 什么是 Captcha 绕行令牌？ <a href="#what-is-a-captcha-bypass-token" id="what-is-a-captcha-bypass-token"></a>
