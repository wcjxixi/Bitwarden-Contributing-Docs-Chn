# 故障排除

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/server/troubleshooting)
{% endhint %}

## macOS

### AppleCFErrorCryptographicExceptio <a href="#applecferrorcryptographicexception" id="applecferrorcryptographicexception"></a>

错误信息：`Interop.AppleCrypto.AppleCFErrorCryptographicException: The operation couldn't be completed.`

Mac 有时可以将证书的信任设置设置为管理员而不是用户。如果您运行下面的命令后没有看到您的证书：

```bash
security dump-trust-settings
```

那么请尝试运行：

```bash
security dump-trust-settings -d
```

如果您的证书显示在此处，则您必须使用 [security 命令](https://ss64.com/osx/security.html)将信任设置导出给用户，因为无法在钥匙串访问应用程序中指定此设置。

为此，请运行 `security trust-settings-export -d <filename>` 以导出管理员证书。然后使用 `security trust-settings-import <filename>` 将它们导入用户。

请参阅相关的 [Github 话题](https://github.com/dotnet/runtime/issues/59703)了解更多信息。

### Error NU1403: Package content hash validation failed <a href="#error-nu1403-package-content-hash-validation-failed" id="error-nu1403-package-content-hash-validation-failed"></a>

以下命令应该可以解决问题：

```bash
dotnet nuget locals all --clear  
git clean -xfd  
git rm \*\*/packages.lock.json -f  
dotnet restore
```

有关更多详细信息，请阅读 [https://github.com/NuGet/Home/issues/7921#issuecomment-478152479](https://github.com/NuGet/Home/issues/7921#issuecomment-478152479)

## Windows

### An attempt was made to access a socket in a way forbidden by its access permissions <a href="#an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions" id="an-attempt-was-made-to-access-a-socket-in-a-way-forbidden-by-its-access-permissions"></a>

当应用程序尝试使用已被使用或保留的端口时，通常会发生此错误。带有 Hyper-V 的较新 Windows 保留了许多 50000+ 端口。

幸运的是，可以手动将端口标记为反向，以防止 Hyper-V 保留它们。以特权模式启动 CMD 会话并运行以下命令，然后重新启动计算机。

```bash
net stop winnat

netsh int ipv4 add excludedportrange protocol=tcp startport=<port> numberofports=1 store=persistent

net start winnat
```

