# =内联自动填充菜单

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/deep-dives/autofill/autofill-menu)
{% endhint %}

## 项目结构 <a href="#project-structure" id="project-structure"></a>

## 实施细节 <a href="#implementation-details" id="implementation-details"></a>

### 自动填充菜单注入 <a href="#injection-of-the-auto-fill-menu" id="injection-of-the-auto-fill-menu"></a>

### 通过沙盒 iFrame 渲染视图 <a href="#rendering-views-through-sandboxed-iframes" id="rendering-views-through-sandboxed-iframes"></a>

### 在自动填充菜单和扩展之间传递消息 <a href="#passing-messages-between-the-auto-fill-menu-and-the-extension" id="passing-messages-between-the-auto-fill-menu-and-the-extension"></a>

### 用数据填充自动填充菜单 <a href="#populating-the-auto-fill-menu-with-data" id="populating-the-auto-fill-menu-with-data"></a>

### 处理用户交互 <a href="#handling-user-interaction" id="handling-user-interaction"></a>

## 安全注意事项 <a href="#security-considerations" id="security-considerations"></a>

### Clickjacking <a href="#clickjacking" id="clickjacking"></a>

### 跨站脚本 (XSS) <a href="#cross-site-scripting-xss" id="cross-site-scripting-xss"></a>

### DOM 破坏 <a href="#dom-clobbering" id="dom-clobbering"></a>
