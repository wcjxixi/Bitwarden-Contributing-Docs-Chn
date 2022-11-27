# 更新测试

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/docs/clients/desktop/update)
{% endhint %}

有时可能需要测试桌面应用程序的更新流程。本文档将尝试详细描述如何做到这一点。

对于开发目的，我们不能真的将桌面应用程序发布到我们的 GitHub 页面。为了提供一个尽可能相似的环境，我们运行了一个本地 S3 模拟器，它可以让我们模拟一个 S3 提供程序环境。

虽然这与我们的场景不完全相同，但它仍然允许我们测试 UI 以及更新过程，而不会把 GitHub 存储库弄得乱七八糟。

## 准备 <a href="#preparation" id="preparation"></a>

1、在 `scripts/dev` 中使用 `docker compose up` 启动 minio docker 容器。

2、添加一个名为 `update` 的只读存储桶，通过访问 `http://localhost:9001` 并使用 `minioadmin/minioadmin` 登录，点击 `Create bucket` 填写详细信息。然后点击 `Manage`、`Access Rules` 并填写以下详细信息。

{% embed url="https://contributing.bitwarden.com/clients/desktop/minio-access-rule.png" %}

3、使用以下的 `publish` 设置修改 `package.json`

```json
"publish" : {
    "provider": "s3",
    "endpoint": "http://127.0.0.1:9000",
    "bucket": "update"
},
```

4、使用以下内容在用户目录 (Windows) 中创建 `.aws/credentials`

```systemd
[default]
aws_access_key_id=minioadmin
aws_secret_access_key=minioadmin
```

## 更新 <a href="#update" id="update"></a>

1. 使用 `npm run publish:win:dev` 生成本地构建
2. 在 `dist/nsis-web/Bitwarden-Installer-1.32.0.exe` 中安装构建
3. 更新 `src/package.json` 中的版本号
4. 使用 `npm run publish:win:dev` 发布新的版本
5. 该应用程序现在应该会提示更新

相关：[https://www.electron.build/tutorials/test-update-on-s3-locally](https://www.electron.build/tutorials/test-update-on-s3-locally)
