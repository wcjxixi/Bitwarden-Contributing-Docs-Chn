# Table of contents

* [关于](README.md)

## 入门 <a href="#getting-started" id="getting-started"></a>

* [概述](getting-started/overview.md)
* [工具](getting-started/tools.md)
* [=服务器](getting-started/server/README.md)
  * [设置指南](getting-started/server/guide.md)
  * [高级服务器设置](getting-started/server/advanced-setup.md)
  * [数据库](getting-started/server/database/README.md)
    * [MSSQL](getting-started/server/database/mssql.md)
    * [实体框架](getting-started/server/database/ef.md)
  * [事件记录](getting-started/server/events.md)
  * [Ingress 隧道](getting-started/server/tunnel.md)
  * [SCIM](getting-started/server/scim.md)
  * [自托管指南](getting-started/server/self-hosted.md)
  * [系统管理门户](getting-started/server/portal.md)
  * [单点登录 (SSO)](getting-started/server/sso/README.md)
    * [本地 IdP](getting-started/server/sso/local.md)
    * [Okta](getting-started/server/sso/okta.md)
  * [故障排除](getting-started/server/troubleshooting.md)
  * [用户机密](getting-started/server/secrets.md)
  * [=公共 API](getting-started/server/gong-gong-api.md)
* [客户端](getting-started/clients/README.md)
  * [Web Vault](getting-started/clients/web-vault/README.md)
    * [WebAuthn](getting-started/clients/web-vault/webauthn.md)
  * [浏览器端](getting-started/clients/browser/README.md)
    * [生物识别解锁](getting-started/clients/browser/biometric.md)
    * [Firefox 隐私模式](getting-started/clients/browser/ff-private.md)
  * [桌面端](getting-started/clients/desktop/README.md)
    * [Mac App Store Dev](getting-started/clients/desktop/mac.md)
    * [Microsoft Store](getting-started/clients/desktop/microsoft-store.md)
    * [Native Messaging Test Runner](getting-started/clients/desktop/native-messaging-test-runner.md)
    * [更新测试](getting-started/clients/desktop/update.md)
  * [CLI](getting-started/clients/cli.md)
  * [移动端](getting-started/clients/mobile/README.md)
    * [Android](getting-started/clients/mobile/android.md)
    * [iOS](getting-started/clients/mobile/ios.md)
    * [watchOS](getting-started/clients/mobile/watchos.md)
  * [故障排除](getting-started/clients/troubleshooting.md)
* [企业](getting-started/enterprise/README.md)
  * [目录连接器](getting-started/enterprise/directory-connector/README.md)
    * [JumpCloud](getting-started/enterprise/directory-connector/jumpcloud.md)
    * [OpenLDAP Docker 服务器](getting-started/enterprise/directory-connector/open-ldap.md)
  * [Key Connector](getting-started/enterprise/key-connector.md)

## 贡献 <a href="#contributing" id="contributing"></a>

* [贡献](contributing/contributing.md)
* [代码样式](contributing/code-style/README.md)
  * [Angular & TypeScript](contributing/code-style/angular.md)
  * [C#](contributing/code-style/csharp.md)
  * [T-SQL](contributing/code-style/t-sql.md)
  * [Tailwind](contributing/code-style/tailwind.md)
* [数据库迁移](contributing/database-migrations/README.md)
  * [进化数据库设计](contributing/database-migrations/edd.md)
* [提交签名](contributing/commit-signing.md)
* [拉取请求](contributing/pull-requests/README.md)
  * [分支](contributing/pull-requests/branching.md)
  * [UI 审查 - Chromatic](contributing/pull-requests/chromatic.md)
  * [代码审查指南](contributing/pull-requests/code-review.md)
* [无障碍](contributing/accessibility.md)
* [依赖管理](contributing/dependencies.md)
* [功能标记](contributing/feature-flags.md)
* [模板库](contributing/template-repository.md)
* [测试](contributing/testing/README.md)
  * [负载测试](contributing/testing/load.md)
  * [单元测试](contributing/testing/unit/README.md)
    * [命名约定](contributing/testing/unit/naming-conventions.md)
    * [测试结构](contributing/testing/unit/structure.md)
* [修改用户机密](contributing/user-secrets.md)

## 架构 <a href="#architecture" id="architecture"></a>

* [架构](architecture/architecture.md)
* [架构决策记录](architecture/adr/README.md)
  * [0001 - Angular Reactive Forms](architecture/adr/0001-angular-reactive-forms.md)
  * [0002 - Public API for modules](architecture/adr/0002-public-api-for-modules.md)
  * [0003 - Adopt Observable Data Services for Angular](architecture/adr/0003-adopt-observable-data-services-for-angular.md)
  * [0004 - Refactor State Service](architecture/adr/0004-refactor-state-service.md)
  * [0005 - Refactor Api Service](architecture/adr/0005-refactor-api-service.md)
  * [0006 - Clients: Use Jest Mocks](architecture/adr/0006-clients-use-jest-mocks.md)
  * [0007 - Manifest V3 sync Observables](architecture/adr/0007-manifest-v3-sync-observables.md)
  * [0008 - Server: Adopt CQRS](architecture/adr/0008-server-adopt-cqrs.md)
  * [0009 - Composition over inheritance](architecture/adr/0009-composition-over-inheritance.md)
  * [0010 - Angular Modules](architecture/adr/0010-angular-modules.md)
  * [0011 - Scalable Angular Clients folder structure](architecture/adr/0011-scalable-angular-clients-folder-structure.md)
  * [0012 - Angular Filename convention](architecture/adr/0012-angular-filename-convention.md)
  * [0013 - Avoid layered folder structure for request/response models](architecture/adr/0013-avoid-layered-folder-structure-for-request-response-models.md)
  * [0014 - Adopt Typescript Strict flag](architecture/adr/0014-adopt-typescript-strict-flag.md)
  * [0015 - Short Lived Browser Services](architecture/adr/0015-short-lived-browser-services.md)
  * [0016 - Move Decryption and Encryption to Views](architecture/adr/0016-move-decryption-and-encryption-to-views.md)
  * [0017 - Use Swift to build watchOS app](architecture/adr/0017-use-swift-to-build-watchos-app.md)
  * [0018 - Feature management](architecture/adr/0018-feature-management.md)
  * [0019 - Adoption of Web Push](architecture/adr/0019-adoption-of-web-push.md)
  * [0020 - Observability with OpenTelemetry](architecture/adr/0020-observability-with-opentelemetry.md)
  * [0021 - Logging to Standard Output](architecture/adr/0021-logging-to-standard-output.md)
* [网络客户端架构](architecture/clients/README.md)
  * [概述](architecture/clients/overview.md)
  * [数据模型](architecture/clients/data-model.md)
  * [表示层](architecture/clients/presentation/README.md)
    * [Angular](architecture/clients/presentation/angular.md)
    * [CLI](architecture/clients/presentation/cli.md)
  * [服务层](architecture/clients/services/README.md)
    * [Vision](architecture/clients/services/vision.md)
    * [实现](architecture/clients/services/implementation.md)
* [移动客户端架构](architecture/mobile-clients/README.md)
  * [概述](architecture/mobile-clients/overview.md)
  * [watchOS](architecture/mobile-clients/watchos.md)
* [服务器架构](architecture/server.md)
* [深度剖析](architecture/deep-dives/README.md)
  * [身份验证](architecture/deep-dives/authentication/README.md)
    * [双因素身份验证](architecture/deep-dives/authentication/two-factor-auth.md)
  * [=浏览器自动填充](architecture/deep-dives/autofill/README.md)
    * [收集页面详细信息](architecture/deep-dives/autofill/collecting-page-details.md)
    * [生成并执行填充脚本](architecture/deep-dives/autofill/generating-fill-scripts.md)
    * [表单提交检测](architecture/deep-dives/autofill/form-submission-detection.md)
    * [Shadow DOM](architecture/deep-dives/autofill/shadow-dom.md)
    * [=内联自动填充菜单](architecture/deep-dives/autofill/autofill-menu.md)
  * [Captcha](architecture/deep-dives/captchas.md)
  * [事件日志](architecture/deep-dives/event-logs.md)
  * [推送通知](architecture/deep-dives/push-notifications/README.md)
    * [移动端推送通知](architecture/deep-dives/push-notifications/mobile.md)
    * [其他客户端推送通知](architecture/deep-dives/push-notifications/non-mobile.md)
