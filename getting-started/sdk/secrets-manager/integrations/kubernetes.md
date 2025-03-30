# Kubernetes

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/sdk/secrets-manager/integrations/kubernetes)
{% endhint %}

Bitwarden Secrets Manager Kubernetes Operator (`sm-operator`) 是一个工具，用于帮助团队无缝地将 Bitwarden Secrets Manager 集成到他们的 Kubernetes 工作流中。

sm-operator 使用[控制器](https://github.com/bitwarden/sm-kubernetes/blob/main/internal/controller/bitwardensecret_controller.go)将 Bitwarden Secrets 同步到 Kubernetes secrets 中。它的做法是将 BitwardenSecret 的自定义资源定义注册到集群中。它会监听群集上已注册的新 BitwardenSecrets，然后按可配置的时间间隔进行同步。

{% hint style="success" %}
如果您是 Secrets Manager 的新手，您应该首先[阅读帮助中心文档](https://bitwarden.com/help/secrets-manager-overview/)，以了解其工作原理。
{% endhint %}

## 要求[​](https://contributing.bitwarden.com/getting-started/sdk/secrets-manager/integrations/kubernetes#requirements) <a href="#requirements" id="requirements"></a>

{% hint style="success" %}
大部分所需的设置都可以使用 Visual Studio Code Dev Containers 来完成。这是推荐的方案，尤其是对于 macOS 和 Windows 用户。
{% endhint %}

{% tabs %}
{% tab title="Dev 容器" %}
* [Visual Studio Code](https://code.visualstudio.com/)
* [Docker](https://www.docker.com/)
* [Visual Studio Code Dev Containers Extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)

{% hint style="warning" %}
如果您使用的是除 Docker 以外的容器引擎，开发容器将无法正常工作。
{% endhint %}
{% endtab %}

{% tab title="直接设置" %}
* [Visual Studio Code](https://code.visualstudio.com/)
* [Visual Studio Code Go Extension](https://marketplace.visualstudio.com/items?itemName=golang.go)
* [Go](https://go.dev/dl/) 版本 1.21
* [musl-gcc](https://wiki.musl-libc.org/getting-started.html)
* [Make](https://www.gnu.org/software/make/)
* [kubectl](https://kubernetes.io/docs/tasks/tools/)
* [Docker](https://www.docker.com/) 或 [Podman](https://podman.io/) 或其他容器引擎
* [Kind Cluster](https://kind.sigs.k8s.io/docs/user/quick-start/) 或将 Kubectl 指向它作为本地开发的当前上下文的其他 Kubernetes 集群。
{% endtab %}
{% endtabs %}

您还需要：

* 一个[具有 Secrets Manager 的 Bitwarden 组织](https://bitwarden.com/help/sign-up-for-secrets-manager/) 。需要您的组织 ID GUID。
* Secrets Manager 机器账户（以前称为服务账户）的[访问令牌](https://bitwarden.com/help/access-tokens/)，与您要提取的项目绑定。

## 设置和配置[​](https://contributing.bitwarden.com/getting-started/sdk/secrets-manager/integrations/kubernetes#setup-and-configuration) <a href="#setup-and-configuration" id="setup-and-configuration"></a>

克隆存储库：

```
git clone https://github.com/bitwarden/sm-kubernetes.git
```

在存储库根目录下打开 Visual Studio Code。

{% tabs %}
{% tab title="Dev 容器" %}
开发容器的设置是自动化的。这将创建一个 Kind 集群并设置所有必要的软件。

* 打开命令调板（根据用户设置，使用 `Cmd/Ctrl`+`Shift`+`P` 或 `F1`）
* 输入 `Dev Containers: Reopen in Container` 以开始
{% endtab %}

{% tab title="直接设置" %}
{% hint style="success" %}
请注意，如果通过路径中任意位置的符号链接打开工作区，Visual Studio Code 的 Go 调试器将无法正常工作。要使调试正常工作，应从软件源的完整路径打开。
{% endhint %}

* 安装直接设置要求中所列出的所有要求
* 使用 `kind create cluster` 创建 Kind 群集
* 运行 `make setup` 创建默认的 .env 文件。
{% endtab %}
{% endtabs %}

### 配置设置[​](https://contributing.bitwarden.com/getting-started/sdk/secrets-manager/integrations/kubernetes#configuration-settings) <a href="#configuration-settings" id="configuration-settings"></a>

创建了 Dev 容器或运行了 `make setup` 后，就会在存储库根目录下创建一个 `.env` 文件。可以更新以下环境变量设置来改变 operator 的行为：

* **BW\_API\_URL** - 设置 Secrets Manager SDK 使用的 Bitwarden API URL。这对于自托管场景以及访问欧洲服务器非常有用。
* **BW\_IDENTITY\_API\_URL** - 设置 Secrets Manager SDK 使用的 Bitwarden Identity 服务 URL。这对于自托管场景以及访问欧洲服务器非常有用。
* **BW\_SECRETS\_MANAGER\_STATE\_PATH** - 设置 Secrets Manager SDK 存储其状态文件的基本路径。
* **BW\_SECRETS\_MANAGER\_REFRESH\_INTERVAL** - 指定 Secrets Manager 和 K8s secrets 之间同步秘密的刷新间隔（以秒为单位）。最小值为 180。

## 运行和调试[​](https://contributing.bitwarden.com/getting-started/sdk/secrets-manager/integrations/kubernetes#running-and-debugging) <a href="#running-and-debugging" id="running-and-debugging"></a>

1. 使用 `make install` 或通过在命令面板中使用来自「Tasks: Run Task」的被称为「apply-crd」的 Visual Studio 任务，将自定义资源定义安装到群集中。
2. 要调试代码，只需按一下 F5。您还可以在命令行中使用 `make run` 来运行，而无需调试。，

{% hint style="success" %}
您也可以通过运行以下命令（不使用调试器）一步到位：`make install run`
{% endhint %}

### 卸载自定义资源定义[​](https://contributing.bitwarden.com/getting-started/sdk/secrets-manager/integrations/kubernetes#uninstall-custom-resource-definition) <a href="#uninstall-custom-resource-definition" id="uninstall-custom-resource-definition"></a>

要从集群中删除 CRD：

```bash
make uninstall
```

{% hint style="success" %}
运行 `make --help` 获取所有潜在 `make` 目标的更多信息。
{% endhint %}

### 创建 BitwardenSecret 用于测试[​](https://contributing.bitwarden.com/getting-started/sdk/secrets-manager/integrations/kubernetes#create-a-bitwardensecret-to-test) <a href="#create-a-bitwardensecret-to-test" id="create-a-bitwardensecret-to-test"></a>

调试器运行中，我们现在将创建一个 BitwardenSecret 对象，以将 Secret Manager 机密同步到 K8s 机密中：

{% hint style="warning" %}
通过 kubectl 创建下面的授权令牌机密后，您的授权令牌就会出现在机器终端历史记录中。对于生产系统，请考虑使用 CSI 机密驱动程序或通过一个短暂的构建代理应用该机密。
{% endhint %}

{% hint style="success" %}
确保在 VS Code 的 Dev 容器终端中运行以下命令。
{% endhint %}

1. 在创建 BitwardenSecret 对象的命名空间中创建一个秘密，用于存放 Secrets Manager 身份验证令牌：`kubectl create secret generic bw-auth-token -n <some-namespace> --from-literal=token="<Auth-Token-Here>"`

{% hint style="success" %}
要获取命名空间列表，请运行 `kubectl get namespaces`
{% endhint %}

2. 安装 BitwardenSecret 实例。[config/samples/k8s\_v1\_bitwardensecret.yaml](https://github.com/bitwarden/sm-kubernetes/blob/main/config/samples/k8s_v1_bitwardensecret.yaml) 中有一个示例。您需要复制这个示例并根据自己的需要进行更新。然后按照这个方式应用：`kubectl apply -n <some-namespace> -f k8s_v1_bitwardensecret.yaml`
3. 在调试控制台窗口中，您应该看到表示机密已启动并完成同步的的消息
4. 运行以下命令查看是否已创建了机密：`kubectl get secrets -n <some-namespace>`
5. 运行以下命令查看已同步机密的结构和数据：`kubectl get secret -n <some-namespace> <secret-name> -o yaml`

{% hint style="success" %}
机密值以 Base64 编码的字符串形式存储。
{% endhint %}

#### BitwardenSecret 清单[**​**](https://contributing.bitwarden.com/getting-started/sdk/secrets-manager/integrations/kubernetes#bitwardensecret-manifest) <a href="#bitwardensecret-manifest" id="bitwardensecret-manifest"></a>

将 BitwardenSecret 对象视为 operator 用来创建和同步 Kubernetes 机密的同步设置。这个 Kubernetes 机密将位于命名空间内，并将注入 Secrets Manager 机器账户（以前称为服务账户）可用的数据。生成的 Kubernetes 机密将包括特定机器账户可以访问的所有机密。示例文件 ([config/samples/k8s\_v1\_bitwardensecret.yml](https://github.com/bitwarden/sm-kubernetes/blob/main/config/samples/k8s_v1_bitwardensecret.yaml)) 提供了 BitwardenSecret 清单的基本结构。下面列出了需要更新的关键设置：

* **metadata.name**：您要部署的 BitwardenSecret 对象的名称
* **spec.organizationId**：您要从其中提取 Secrets Manager 数据的 Bitwarden 组织 ID
* **spec.secretName**：将创建并注入 Secrets Manager 数据的 Kubernetes 机密的名称。
* **spec.authToken**：BitwardenSecrets 对象部署到的 Kubernetes 名称空间中的机密名称，其中包含用于访问机密的 Secrets Manager 机器账户授权令牌。

Secrets Manager 不保证跨项目机密名称的唯一性，因此默认情况下，机密将以 Secrets Manager 机密 UUID 作为键创建。为了使生成的机密更容易使用，您可以创建一个 Bitwarden 机密 ID 到 Kubernetes 机密键的映射。生成的机密将用您提供的映射友好名称替换 Bitwarden 机密 ID。以下是可以使用的映射设置：

* **bwSecretId**：这是 Secrets Manager 中机密的 UUID。可以在 Secrets Manager 门户网站或使用 [Bitwarden Secrets Manager CLI](https://github.com/bitwarden/sdk-sm/releases) 在机密名称下找到
* **secretKeyName**：在 Kubernetes 机密中生成的键，用于替换 UUID

注意，自定义映射仅作为信息目的在已生成的机密中提供，可在 `k8s.bitwarden.com/custom-map` 注解中找到。

## 测试 Docker 镜像[​](https://contributing.bitwarden.com/getting-started/sdk/secrets-manager/integrations/kubernetes#testing-the-docker-image) <a href="#testing-the-docker-image" id="testing-the-docker-image"></a>

Windows 上的 Kind 很难从本地注册表中提取数据，因此我们提供了两种不同的路径来部署映像。

{% tabs %}
{% tab title="使用注册表" %}
1. 构建并推送您的镜像到由 `IMG` 指定的注册表位置（本地或其他位置）： `make docker-build docker-push IMG=<some-registry>/sm-operator:tag`
2. 使用 `IMG` 指定的镜像将控制器部署到集群中：`make deploy IMG=<some-registry>/sm-operator:tag`
{% endtab %}

{% tab title="推送到 Kind" %}
1. 直接使用 Visual Studio Code 命令面板构建并推送镜像到 Kind。打开面板（按 F1），选择「Tasks: Run Task」，然后选择「docker-build」和「kind-push」。
2. 使用 Visual Studio Code 命令面板部署 Kubernetes 对象到 Kind。打开面板（按 F1），选择「Tasks: Run Task」，然后选择「deploy」。
{% endtab %}
{% endtabs %}

{% hint style="success" %}
在使用容器时，可通过更新 [config/manager/manager.yaml](https://github.com/bitwarden/sm-kubernetes/blob/main/config/manager/manager.yaml) 中的环境变量对 URL、刷新间隔和状态路径进行自定义配置。
{% endhint %}

安装后，根据本文档中之前所述创建您的 K8s 授权令牌机密和 BitwardenSecret 以进行测试。

### 查看 Pod 日志[​](https://contributing.bitwarden.com/getting-started/sdk/secrets-manager/integrations/kubernetes#viewing-pod-logs) <a href="#viewing-pod-logs" id="viewing-pod-logs"></a>

要查看通过上述步骤部署的 operator 日志：

1. 运行 `kubectl get pods -n sm-operator-system` 。这将获取已安装的 operator pod 的名称。
2. 运行 `kubectl logs -n sm-operator-system <name-of-pod-from-previous-step>`

### 卸载控制器[​](https://contributing.bitwarden.com/getting-started/sdk/secrets-manager/integrations/kubernetes#undeploy-controller) <a href="#undeploy-controller" id="undeploy-controller"></a>

要从集群中移除已安装的控制器 pod，请运行：

```bash
make undeploy
```

## 单元测试[​](https://contributing.bitwarden.com/getting-started/sdk/secrets-manager/integrations/kubernetes#unit-testing) <a href="#unit-testing" id="unit-testing"></a>

单元测试目前位于以下文件中：

* internal/controller/suite\_test.go
* cmd/suite\_test.go

要运行单元测试，请运行 `make test`。要调试单元测试，请打开您想调试的文件。在 Visual Studio Code 的 「Run and Debug」选项卡中，将启动配置从「Debug」更改为「Test current file」，然后按 F5。

{% hint style="success" %}
在调试测试之前，您需要运行 `make test`，这将设置一些必要的软件以进行 operator 测试。
{% endhint %}

{% hint style="warning" %}
目前不支持使用 Visual Studio Code 的「Testing」选项卡。
{% endhint %}

## 修改 API 定义[​](https://contributing.bitwarden.com/getting-started/sdk/secrets-manager/integrations/kubernetes#modifying-the-api-definitions) <a href="#modifying-the-api-definitions" id="modifying-the-api-definitions"></a>

如果您正在通过 [api/v1/bitwardensecret\_types.go](https://github.com/bitwarden/sm-kubernetes/blob/main/api/v1/bitwardensecret_types.go) 编辑 API 定义，请使用以下命令重新生成清单：

```bash
make manifests
```

更多信息请参阅 [Kubebuilder 文档](https://book.kubebuilder.io/introduction.html)。
