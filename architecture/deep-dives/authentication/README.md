# 身份验证

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/deep-dives/authentication/)
{% endhint %}

## 客户端登录策略 <a href="#client-login-strategies" id="client-login-strategies"></a>

## 客户端请求令牌 <a href="#client-requests-a-token" id="client-requests-a-token"></a>

### 所有身份验证请求的公共属性 <a href="#common-properties-of-all-authentication-requests" id="common-properties-of-all-authentication-requests"></a>

### 特定于类型的身份验证请求属性 <a href="#type-specific-authentication-request-properties" id="type-specific-authentication-request-properties"></a>

#### 密码令牌请求 <a href="#password-token-requests" id="password-token-requests"></a>

#### API 令牌请求 <a href="#api-token-requests" id="api-token-requests"></a>

#### SSO 令牌请求 <a href="#sso-token-requests" id="sso-token-requests"></a>

## 身份 API 授予访问权限 <a href="#identity-api-grants-access" id="identity-api-grants-access"></a>

### 验证请求 <a href="#validating-the-request" id="validating-the-request"></a>

#### 密码令牌验证 (`ResourceOwnerPasswordValidator`) <a href="#password-token-validation" id="password-token-validation"></a>

#### API 令牌验证 (`CustomTokenRequestValidator`) <a href="#api-token-request-validation" id="api-token-request-validation"></a>

#### SSO 令牌验证 (`CustomTokenRequestValidator`) <a href="#sso-token-request-validation" id="sso-token-request-validation"></a>

### 生成响应 <a href="#generating-a-response" id="generating-a-response"></a>

#### 身份验证信息 <a href="#authentication-information" id="authentication-information"></a>

#### 解密信息 <a href="#decryption-information" id="decryption-information"></a>

## 客户端处理响应 <a href="#client-handles-the-response" id="client-handles-the-response"></a>

### 使用身份验证信息来设置用户账户 <a href="#use-the-authentication-information-to-set-up-the-users-account" id="use-the-authentication-information-to-set-up-the-users-account"></a>

### 使用解密信息准备解密 <a href="#use-the-decryption-information-to-prepare-for-decryption" id="use-the-decryption-information-to-prepare-for-decryption"></a>

#### 设备密钥 (`deviceKey`) <a href="#device-key" id="device-key"></a>

#### 用户密钥 (`userKey`) <a href="#user-key" id="user-key"></a>

#### 用户私钥 (`privateKey`) <a href="#user-private-key" id="user-private-key"></a>

## 附录：`UserDecryptionOptions` <a href="#appendix" id="appendix"></a>
