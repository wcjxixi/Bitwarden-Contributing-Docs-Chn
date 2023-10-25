# 身份验证

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/deep-dives/authentication/)
{% endhint %}

## 客户端登录策略 <a href="#client-login-strategies" id="client-login-strategies"></a>

我们的身份验证方法是通过客户端代码中的「策略」定义的。这些策略是：

* 密码登录策略：使用电子邮件地址和主密码进行身份验证。
* 无密码登录策略：使用一次性访问代码的[设备登录](https://help.ppgg.in/my-account/log-in-and-unlock/log-in-with-device)进行身份验证。
* API 登录策略：使用 API 密钥和机密进行身份验证。
* SSO 登录策略：通过 SAML 或 OpenIDConnect 使用 SSO IdP 进行身份验证。

无论采用哪种策略，「登录」Bitwarden 都需要向我们的身份服务器上的 `/connect/token` 端点 `POST` 请求。该请求的内容因多种因素而各不相同，但最重要的是登录策略。

## 客户端请求令牌 <a href="#client-requests-a-token" id="client-requests-a-token"></a>

对于每一种策略，客户端都会通过其子类形成 [`TokenRequest`](https://github.com/bitwarden/clients/blob/master/libs/common/src/auth/models/request/identity-token/token.request.ts)：

* [`PasswordTokenRequest`](https://github.com/bitwarden/clients/blob/master/libs/common/src/auth/models/request/identity-token/password-token.request.ts) - 由密码和无密码登录策略使用
* [`UserApiTokenRequest`](https://github.com/bitwarden/clients/blob/master/libs/common/src/auth/models/request/identity-token/user-api-token.request.ts) - 由 API 登录策略使用
* [`SSOTokenRequest`](https://github.com/bitwarden/clients/blob/master/libs/common/src/auth/models/request/identity-token/user-api-token.request.ts) - 由 SSO 登录策略使用

这些请求中的每一个都会重写 `toIdentityToken()` 方法，该方法负责将 `TokenRequest` 中的信息转换为将发送到我们的身份服务器上的 `/connect/token` 端点的有效负载。

### 所有身份验证请求的公共属性 <a href="#common-properties-of-all-authentication-requests" id="common-properties-of-all-authentication-requests"></a>

以下属性是从所有登录策略生成的身份验证请求的公共属性：

| 属性                       | 目的                                                                                                                         |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------------- |
| `deviceType`             | 请求设备的 [`DeviceType`](https://github.com/bitwarden/clients/blob/master/libs/common/src/enums/device-type.enum.ts)。          |
| `deviceName`             | 请求设备的 [`DeviceType`](https://github.com/bitwarden/clients/blob/master/libs/common/src/enums/device-type.enum.ts) 的字符串表示形式。 |
| `deviceIdentifier`       | 安装应用程序时在设备上生成的设备的唯一标识符。                                                                                                    |
| `twoFactorToken`         | 对于启用了双因素身份验证的用户，包含 2FA 提供商生成的令牌。否则为空。详细信息，请参阅[双因素身份验证](two-factor-auth.md)。                                                |
| `twoFactorTokenProvider` | 在 `twoFactorToken` 中生成令牌的 2FA 提供商。否则为空。                                                                                    |
| `twoFactorRemember`      | 指示用户是否已请求记住他们的 2FA 选择。                                                                                                     |

### 特定于类型的身份验证请求属性 <a href="#type-specific-authentication-request-properties" id="type-specific-authentication-request-properties"></a>

#### 密码令牌请求 <a href="#password-token-requests" id="password-token-requests"></a>

* **授权类型**：`password`
* **标头**：`Auth-Email` 标头被设置为用户电子邮件地址的 base-64 编码

| 内容属性              | 描述                                                       |
| ----------------- | -------------------------------------------------------- |
| `client_id`       | 发出请求的客户端类型的 `ClientType` 枚举值。                            |
| `scope`           | `api` `offline access`                                   |
| `username`        | 电子邮件地址                                                   |
| `password`        | 主密码哈希或访问代码（如果使用设备登录）                                     |
| `captchaResponse` | Captcha 响应令牌（如果提供）（详细信息，请参 [Captcha 文档](../captchas.md)） |
| `authRequest`     | 设备登录身份验证请求 ID（如果这是设备登录请求）                                |

#### API 令牌请求 <a href="#api-token-requests" id="api-token-requests"></a>

* **授权类型**：`client_credentials`
* **标头**：无

| 内容属性            | 描述                                                               |
| --------------- | ---------------------------------------------------------------- |
| `client_id`     | 由 API 客户端提供的 `client_id`                                         |
| `client_secret` | 由 API 客户端提供的 `client_secret`                                     |
| `scope`         | 如果 `client_id` 以「organization」开头，则为 `api.organization`，否则为 `api` |

#### SSO 令牌请求 <a href="#sso-token-requests" id="sso-token-requests"></a>

* **授权类型**：`authorization_code`
* **标头**：无

| 内容属性            | 描述                                                               |
| --------------- | ---------------------------------------------------------------- |
| `code`          | SSO 登录时，代码查询字符串参数中提供的代码                                          |
| `code_verifier` | 在客户端发出 SSO 请求之前生成的唯一 64 位验证码                                     |
| `redirect_uri`  | 在 `SsoComponent` 和 `LinkSsoComponent` 中设置为 `/sso-connector.html` |

## 身份 API 授予访问权限 <a href="#identity-api-grants-access" id="identity-api-grants-access"></a>

当身份服务在 `/connect/token` 端点接收到令牌请求时，两个类之一将根据身份验证请求中指定的授权类型进行接管。

有关每个验证器职责的更多信息，请参阅 [IdentityServer4 文档](https://identityserver4.readthedocs.io/en/latest/index.html)。

### 验证请求 <a href="#validating-the-request" id="validating-the-request"></a>

#### 密码令牌验证 (`ResourceOwnerPasswordValidator`) <a href="#password-token-validation" id="password-token-validation"></a>

{% hint style="success" %}
**哪种授权类型？**

该验证器负责颁发 `password` 授权类型的令牌。
{% endhint %}

为了使请求得到验证，以下必须为真：

* `Auth-Email` 标头必须存在且正确。
* 该请求不需要 Captcha，或者如果需要，则提供有效的 `captchaResponse`（详细信息，请参阅 [Captcha 文档](../captchas.md)）。
* 该请求不需要 2FA，或者如果需要，则提供有效的 `twoFactorToken`（请参阅 [2FA 文档](two-factor-auth.md)）
* 如果该请求具有 `authRequest` 属性（即无密码请求），则访问代码有效并且请求尚未过期。
* 该密码是正确的。这可以是：
  * 主密码哈希与用户的数据库记录匹配，或者
  * 设备登录访问代码尚未过期，并且与客户端提供的 `authRequest` 上的服务器端代码匹配。
* 用户不属于要求 SSO 的组织。

#### API 令牌验证 (`CustomTokenRequestValidator`) <a href="#api-token-request-validation" id="api-token-request-validation"></a>

{% hint style="success" %}
**哪种授权类型？**

该验证器负责颁发 `client_credential` 授权类型的令牌。
{% endhint %}

有不同类型的客户端可以使用 client\_credential 授权类型请求访问权限：

| 客户端类型        | 目的                                                                         |
| ------------ | -------------------------------------------------------------------------- |
| Installation | 用于从自托管 API 到移动推送通知的推送中继的私密客户端通信。每个自托管安装都以自身身份进行身份验证，并有权限提交推送通知请求。          |
| Internal     | 用于 API 和通知服务之间的通信。这仅用于自托管安装，其中通知直接发送到 SignalR 通知中心，而不是发送到 Azure 队列。        |
| Organization | 用于组织代表组织和非个人用户做更改。                                                         |
| User         | 用于个人用户使用他们自己的 API 令牌执行操作。在这种情况下，用户的声明将添加到 `access_token` 中，就像用户通过密码流程登录一样。 |

不同的 API 客户端类型都有不同的验证。`client_credentials` 授权类型验证的关键是内置于 IdentityServer 中的 `ClientStore`（并在我们的代码中重写）。.NET 请求中间件管道中的每个请求都会查询 `ClientStore`，以从请求中查找给定了 `client_id` 的客户端。

| 客户端类型        | 验证逻辑                                                                                           |
| ------------ | ---------------------------------------------------------------------------------------------- |
| Installation | 确保提供的 `client_secret` 与 `client_id` 中提供的安装 ID 的安装密钥匹配。                                         |
| Internal     | 确保提供的 `client_secret` 与全局设置中配置的 `InternalIdentityKey` 匹配。如果 Identity 和 API 共享相同的配置，则它们将根据定义匹配。 |
| Organization | 确保提供的 `client_secret` 与 `client_id` 中提供的组织 ID 的安装密钥匹配。                                         |
| User         | 确保提供的 `client_secret` 与 `client_id` 中提供的用户 ID 的 API 密钥匹配。                                      |

#### SSO 令牌验证 (`CustomTokenRequestValidator`) <a href="#sso-token-request-validation" id="sso-token-request-validation"></a>

{% hint style="success" %}
**哪种授权类型？**

该验证器负责颁发 `authorization_code` 授权类型的令牌。
{% endhint %}

请求中的 authorization\_code 中的代码验证由 IdentityServer 完成（请参阅[此处](https://identityserver4.readthedocs.io/en/latest/topics/grant\_types.html#interactive-clients)的文档）。该代码已通过 SSO 流获取，现在被提交给 IdentityServer 以交换 `access_token`。

### 生成响应 <a href="#generating-a-response" id="generating-a-response"></a>

通过给定授权类型的适当逻辑验证了请求后，身份 API 将生成响应。在 Bitwarden，此响应包含一个带有声明的 `access_token` 以及一个自定义 JSON 对象，其中包含启动密码库解密过程的基本信息。

{% hint style="info" %}
**已知设备**

此时，当令牌请求已被验证时，请求设备将被存储并作为「已知设备」与用户关联。
{% endhint %}

#### 身份验证信息 <a href="#authentication-information" id="authentication-information"></a>

对于经过验证的密码和 SSO 令牌请求（授予类型为 `password` 和 `authorization_code` ），响应将包含 `access_token`，它是具有以下声明的 JWT:

* `device` - 设备标识符。
* `premium` - 一个布尔值，指示他们是否有权访问高级功能。
* `email` - 用户的电子邮件地址。
* `email_verified` - 用户是否已验证其电子邮件地址。
* `sstamp` - 用户的安全标记（存储在 `User` 表中的用户的唯一 GUID，并在生成新密码或密钥时重新生成）。`sstamp` 改变表示用户需要重新进行身份验证。
* `name` - 用户名。
* **组织声明**
  * `orgowner` - 用户是其所有者的任何组织的 `ID`。
  * `orgadmin` - 用户是其管理员的任何组织的 `ID`。
  * `orgmanager` - 用户是其经理的组织的 `ID`。
  * `orguser` - 用户是其普通用户的组织的 `ID`。
  * `orgcustom` - 用户拥有自定义权限的组织的 `ID`。在这种情况下，用户还将按照为该组织定义的方式添加自定义声明。
* **提供商声明**
  * `providerprovideradmin` - 用户是其管理员的提供商的 `ID`。
  * `providerserviceuser` - 用户是其普通用户的提供商的 `ID`。

对于 API 令牌请求（授权类型为 `client_credentials` ），响应因客户端类型而异，如下所示：

| 客户端类型        | 声明                                                                                                                                     |
| ------------ | -------------------------------------------------------------------------------------------------------------------------------------- |
| Installation | `sub` - 安装 `ID`                                                                                                                        |
| Internal     | `sub` - 内部 `ID`                                                                                                                        |
| Organization | `sub` - 组织 `ID`                                                                                                                        |
| User         | <p><code>sub</code> - 用户 ID<br><code>amr</code> - 设置为 <code>Application</code> 和 <code>external</code><br>加上上面列出的密码身份验证请求中的所有用户声明。</p> |

#### 解密信息 <a href="#decryption-information" id="decryption-information"></a>

该响应还将包含一个自定义 JSON 响应对象，该对象包含以下属性：

| 属性                            | 目的                                                                                                                      |
| ----------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `PrivateKey`                  | 用户的 RSA 私钥，使用用户的对称加密密钥进行加密。                                                                                             |
| `Key`                         | 用户的对称加密密钥，使用用户的主密码加密。                                                                                                   |
| `UserDecryptionOptions`       | 用户可以用来解密其对称密钥的方式。详细信息，请参阅[用户解密选项](./#appendix)。                                                                         |
| `Kdf`                         | 用户定义的 KDF 类型。                                                                                                           |
| `KdfIterations`               | 用户定义的 KDF 迭代。                                                                                                           |
| `ForcePasswordReset`          | 指示用户是否必须立即重置密码的布尔值。                                                                                                     |
| `MasterPasswordPolicy`        | 用户所属的任何组织的主密码策略。                                                                                                        |
| `TwoFactorToken`              | 可用于绕过 2FA 的令牌。当用户选择「记住」他们的 2FA 响应时生成。                                                                                   |
| `ResetMasterPassword` （_已过时_） | 指示用户是否需要设置主密码的布尔值。对于配置了 Key Connector 的用户，它被显式设置为 `false`。它已被 `UserDecryptionOptions.HasMasterPassword` 取代，但保留它是为了向后兼容。 |

对于 API 令牌请求，以下附加属性将添加到响应中：

* `ApiUseKeyConnector` - 如果用户设置为使用 Key Connector 进行主密码检索，则设置为 `true`。

## 客户端处理响应 <a href="#client-handles-the-response" id="client-handles-the-response"></a>

当令牌请求经过验证并将其发送回客户端时，客户端负责将结果数据正确存储在状态中，以便用户可以进行身份​​验证然后可以解密其密码库。

处理令牌响应的核心逻辑位于我们的基础 `LoginStrategy` 的 `processTokenResponse()` 方法中，我们在其中执行以下操作：

### 使用身份验证信息来设置用户账户 <a href="#use-the-authentication-information-to-set-up-the-users-account" id="use-the-authentication-information-to-set-up-the-users-account"></a>

响应包含一个 JWT `access_token`，其中包含用户声明，可以在后续 API 请求中提供以对用户进行身份验证，以及一个 `refresh_token`，可用于请求新的 `access_token` 而不提示用户再次进行身份验证。

`access_token` 上的声明用于在状态中初始化用户的 `AccountProfile`，`access_token` 和 `refresh_token` 在状态中存储在 `AccountTokens` 中，用于后续 API 请求。

### 使用解密信息准备解密 <a href="#use-the-decryption-information-to-prepare-for-decryption" id="use-the-decryption-information-to-prepare-for-decryption"></a>

`AccountKeys` （在状态中存储在 `account.keys` 中）也使用令牌响应中提供的信息在用户账户中设置。

此时设置的关键点是：

#### 设备密钥 (`deviceKey`) <a href="#device-key" id="device-key"></a>

设备密钥是存储在用户受信任设备上的对称加密密钥，用于在使用受信任设备加密时解密用户的对称加密密钥。

它不会在请求时返回（因为它永远不会离开设备），但此时会在经过身份验证的用户的账户状态上设置它。

#### 用户密钥 (`userKey`) <a href="#user-key" id="user-key"></a>

用户密钥是用于解密用户密码库的对称加密密钥。

每个登录策略中定义了如何设置用户密钥的具体细节：

* **密码和设备登录策略**
  * 令牌响应中的 `Key` 存储在帐户状态的 `userKeyMasterKey` 中，用户输入主密码时已处于状态的 `masterKey` 用于解密并存储 `userKey`。
* **API策略**
  * 令牌响应中的 `Key` 存储在账户状态的 `userKeyMasterKey` 中。
  * 如果在令牌响应上设置了 `ApiUseKeyConnector` 属性，我们将从状态中检索 `masterKey` 并使用它来解密和存储 `userKey`。
* **单点登录策略**
  * 受信任设备加密为我们提供了多种获取用户密钥的方法，这些方法都在 SSO 登录策略中进行处理。
  * 这些包括：
    * 如果用户处于受信任设备上，则使用设备密钥
    * 使用已批准的管理员批准请求来接收密钥
    * 如果用户有主密码，则使用主密钥

#### 用户私钥 (`privateKey`) <a href="#user-private-key" id="user-private-key"></a>

如果响应中提供了 `PrivateKey`，它将存储在用户账户状态的 `privateKey.encrypted` 中。

## 附录：`UserDecryptionOptions` <a href="#appendix" id="appendix"></a>

令牌响应将包含 `UserDecryptionOptions` 的实例。该对象包含以下属性，这些属性描述用户如何解密其密码库。

* `HasMasterPassword` - 如果用户在数据库中有主密码，则为 `true`。
* `TrustedDeviceOption` - 包含了定义用户是否可以使用受信任设备加密解密其对称密钥的属性。
  * `HasAdminApproval` - 如果用户可以使用管理员批准来请求批准新的受信任设备，则为 `true`。
  * `HasLoginApprovingDevice` - 如果用户拥有任何可用于请求批准新的受信任设备的合格设备，则为 `true`。
  * `HasManageResetPasswordPermission` - 如果用户拥有 `ManageResetPassword` 权限，则为 `true` 。这是必需的，以便接收客户端可以知道是否要求用户设置主密码。
  * `EncryptedPrivateKey` - 如果发出请求的设备是可信的，则这将包含设备特定的私钥，并使用仅存在于受信任设备上的设备密钥进行加密。
  * `EncryptedUserKey` - 如果发出请求的设备是可信的，则这将包含用户的对称加密密钥，并使用与上面 `EncryptedPrivateKey` 中的私钥相对应的公钥进行加密。
* `KeyConnectorOption` - 包含用户是否可以使用 Key Connector 解密其对称密钥的属性定义。
  * `KeyConnectorUrl` - Key Connector 实例的 URL。
