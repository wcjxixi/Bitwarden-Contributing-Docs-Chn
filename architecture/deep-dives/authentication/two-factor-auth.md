# 双重身份验证

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/deep-dives/authentication/two-factor-auth)
{% endhint %}

当用户账户需要双重身份验证 (2FA) 时，正常的 Bitwarden 身份验证流程将被转移以处理 2FA 请求。

## 发起身份验证请求 <a href="#initial-authentication-request" id="initial-authentication-request"></a>

当 Identity API 收到用户的身份验证请求时，[验证过程](./#validating-the-request)的一部分是确保用户的账户不需要双因素身份验证。

如果账户（或用户的组织）要求 2FA，则 `/connect/token` 端点将返回带有 HTTP 状态代码 `400` 的响应，值为 `invalid_grant` 的 `Error`，以及「要求两因素」的 `ErrorMessage`。响应还将包含带有以下 JSON 数据的 `CustomResponse`：

```json
{
    { "TwoFactorProviders", [2FA provider keys] },
    { "TwoFactorProviders2", [2FA provider data] },
    { "MasterPasswordPolicy", [Master password policy] },
    // Only if the user has email 2FA
    { "SsoEmail2faSessionToken", [Token to allow email to be passed through SSO flow] }
}
```

## 在客户端处理 2FA 响应 <a href="#handling-2fa-response-on-the-client" id="handling-2fa-response-on-the-client"></a>

如果客户端在尝试登录时检测到 [`IdentityTwoFactorResponse`](https://github.com/bitwarden/clients/blob/master/libs/common/src/auth/models/response/identity-two-factor.response.ts)，则会从 `CustomResponse` 中提取可用的 2FA 提供程序并将其呈现给用户。

每个 2FA 方法都有自己的 UI，但在所有情况下，用户都会生成某种「令牌」来捕获用户的响应。当用户完成 2FA 流程时，将在 [`TwoFactorComponent`](https://github.com/bitwarden/clients/blob/master/apps/desktop/src/auth/two-factor.component.ts) 上捕获此令牌。

用户输入 2FA 后，新的身份验证请求将被 `POST` 到 `/connect/token` 端点，其内容与原始失败请求相同，并在请求正文中添加以下内容：

* `twoFactorToken` - 设置为用户的 2FA 响应生成的令牌
* `twoFactorProvider` - 设置为用于 2FA 的提供程序
* `twoFactorRemember` - 根据用户是否选择「记住」其 2FA 响应进行设置

## 使用 2FA 响应验证请求 <a href="#validating-request-with-2fa-response" id="validating-request-with-2fa-response"></a>

当验证 PI 收到包含附加 2FA 属性的第二个身份验证请求时，它会再次执行身份验证过程。

这次，服务器能够验证 2FA 响应。为此，它会针对客户端在请求中发送的 `twoFactorProvider` 的相关 `IUserTwoFactorTokenProvider` 实现调用 `ValidateAsync`。

如果令牌有效，则会[生成成功的响应并将其返回给客户端](./#generating-a-response)。如果用户选择记住其 2FA 实体，则此响应会在 `TwoFactorToken` 属性中包含一个令牌，该令牌可以包含在后续请求中以绕过 2FA。
