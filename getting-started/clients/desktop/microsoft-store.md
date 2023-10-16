# \*Microsoft Store

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/clients/desktop/microsoft-store)
{% endhint %}

要调试 Microsoft Store 应用程序，需要生成代码签名证书，可使用以下 Powershell 命令生成：

```powershell
New-SelfSignedCertificate -Type Custom `
  -Subject "CN=ElectronSign, 0=Your Corporation, C=US" `
  -TextExtension @("2.5.29.19={text}false") `
  -KeyUsage DigitalSignature `
  -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3", "2.5.29.19={text}") `
  -FriendlyName ElectronSign `
  -CertStoreLocation "Cert:\CurrentUser\My"
```

生成的证书需要复制到 `Cert:\CurrentUser\Trusted People` 中，以告知操作系统信任该证书。使用 `certmgr` 工具最方便简单。

要访问签名工具，需要使用 [Windows SDK](https://developer.microsoft.com/en-us/windows/downloads/windows-sdk/)。

```powershell
npm run dist:win

cd dist
"C:\Program Files (x86)\Windows Kits\10\bin\10.0.19041.0\x64\signtool.exe" sign /v /fd sha256 /n "14D52771-DE3C-4886-B8BF-825BA7690418" .\Bitwarden-2022.<version>.appx
```
