# CLI

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/clients/presentation/cli)
{% endhint %}

相比其他客户端，CLI 的架构略有不同，这符合它的本质。CLI 的会话是短暂的，只执行一个任务然后退出。这意味着 CLI 不需要像其他客户端一样跟踪内存状态。

## 命令 <a href="#commands" id="commands"></a>

由于 CLI 是命令行界面，因此一切都是围绕命令构建的。每个命令执行一个特定的任务。与 _Angular 组件_ 类似，_命令_的目标是尽可能轻量化，大部分业务逻辑都由服务处理。这允许在不同命令之间共享代码，也允许与 Angular 表示层共享代码。

一个基本命令本质上只实现一个单一的方法，`run`。命令类由 `Program` 类实例化。

```javascript
export class ConfigCommand {
  // Dependencies are manually wired up in the Program class.
  constructor(private environmentService: EnvironmentService) {}

  async run(setting: string, value: string, options: program.OptionValues): Promise<Response> {
    // Set configuration
  }
}
```
