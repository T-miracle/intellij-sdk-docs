<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# JCEF — Java Chromium 嵌入式框架

<link-summary>在 IDE 中嵌入基于 Chromium 的浏览器。</link-summary>

> JCEF（**Java Chromium Embedded Framework**）自2020.1版本起作为**实验性功能**可用。
> 我们计划在2020.2版本中弃用第三方插件中的JavaFX，并切换到JCEF。
> 要在2020.2或更高版本中继续使用JavaFX，必须显式添加对[用于插件的 JavaFX 运行时](https://plugins.jetbrains.com/plugin/14250-javafx-runtime-for-plugins)的依赖。
> 还请参阅博客文章[JavaFX and JCEF in the IntelliJ Platform](https://blog.jetbrains.com/platform/2020/07/javafx-and-jcef-in-the-intellij-platform/)以获取计划概要。
>
{style="warning"}
JCEF是[CEF](https://bitbucket.org/chromiumembedded/cef/wiki/Home)框架的Java端口，用于在使用Swing的应用程序中嵌入[基于Chromium的浏览器](https://www.chromium.org/Home)。

将浏览器组件嵌入IDE中允许：

- 渲染HTML内容
- 预览生成的HTML（例如，来自Markdown的HTML）

> 还请参阅[使用 WebView 创建 IntelliJ 插件](https://medium.com/virtuslab/creating-intellij-plugin-with-webview-3b27c3f87aea)教程。

## 启用 JCEF

<tabs>
<tab title="2020.2及以后版本">

> JCEF 自2020.2版本起默认可用并已启用。
>
{style="note"}

</tab>
<tab title="早期版本">

使用 JCEF 需要使用专用的 JetBrains 运行时。
请参阅[安装说明](https://youtrack.jetbrains.com/issue/IDEA-231833#focus=streamItem-27-3993099.0-0)以了解如何在您的 IDE 中获取并激活它。
在<control>Registry</control>对话框中启用 `ide.browser.jcef.enabled`（调用<ui-path>Help | Find Action...</ui-path>，然后键入“Registry”），并重新启动 IDE 以使更改生效。

</tab>
</tabs>

## 调试

嵌入到 JCEF 中的 [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools/) 可用作调试和性能分析工具。
它默认处于活动状态，因此 Chrome DevTools 客户端可以通过默认端口号（9222）连接到它。
可以使用以下注册表键配置端口号：

```
ide.browser.jcef.debug.port=9222
```

因此，可以使用 IntelliJ IDEA Ultimate 中的 JavaScript 调试器来调试通过 Chrome DevTools 在 IDE 中运行的 JavaScript 代码。
使用适当的端口号，使用 <control>Attach to Node.js/Chrome</control> 配置。

此外，JCEF 提供了一个默认的 Chrome DevTools 前端（类似于 Chrome 浏览器中的前端），可以通过 JCEF 的浏览器组件上下文菜单中的<ui-path>Open DevTools</ui-path>打开。
该菜单项仅在[内部模式](enabling_internal.md)下可用。
从2021.3版本开始，必须显式将注册表键 `ide.browser.jcef.contextMenu.devTools.enabled` 设置为 `true` 才能访问。

要在插件代码中访问 Chrome DevTools，请使用以下 API：

```java
JBCefBrowser browser = new JBCefBrowser(myUrl);
CefBrowser devTools = browser.getCefBrowser().getDevTools();
JBCefBrowser devToolsBrowser = JBCefBrowser.createBuilder()
    .setCefBrowser(devTools)
    .setClient(browser.getJBCefClient())
    .build();
```

或者，为了只是在单独的窗口中打开它：

```java
JBCefBrowser browser = new JBCefBrowser(myUrl);
browser.openDevtools();
```

## 测试

查看[`JBCefTestHelper`](%gh-ic%/platform/platform-tests/testSrc/com/intellij/ui/jcef/JBCefTestHelper.java)以及该包中的测试。

## API

### JBCefApp

[`JBCefApp`](%gh-ic%/platform/platform-api/src/com/intellij/ui/jcef/JBCefApp.java)执行JCEF自动初始化，管理其生命周期，并提供`JBCefClient`实例。

在使用JCEF之前，必须调用`JBCefApp.isSupported()`检查：

```java
if (!JBCefApp.isSupported()) {
  // 返回到替代的无浏览器解决方案
  return;
}
// 使用JCEF
```

当以下情况发生时，可能不支持JCEF：
- 在IDE运行时不可用（IDE使用替代的OpenJDK启动）。
- 其版本与运行的IDE不兼容。

为了避免上述问题，IDE应使用捆绑的JetBrains运行时（JBR）运行（另请参见[](ide_development_instance.md)）。

### JBCefClient

[`JBCefClient`](%gh-ic%/platform/platform-api/src/com/intellij/ui/jcef/JBCefClient.java) 与每个浏览器组件显式或隐式地关联。
它用于向关联的浏览器添加处理程序。
同一个实例可以在多个浏览器之间共享。
开发人员可以根据处理程序的逻辑使用共享或每个浏览器的实例。
如果客户端是显式创建的，则应由开发人员[处理](disposers.md)。
否则，它将在关联的浏览器实例被处理后自动释放。

### JBCefBrowser

[`JBCefBrowser`](%gh-ic%/platform/platform-api/src/com/intellij/ui/jcef/JBCefBrowser.java) 提供浏览器UI组件：

```java
JComponent getComponent();
```

它还提供了加载方法（也可从非EDT线程调用）：

```java
void loadURL(String);
void loadHTML(String);
```

要执行JS代码和回调（参见[](#jbcefjsquery)），直接使用包装的 `CefBrowser` 实例：

```java
getCefBrowser().executeJavaScript(myCode, myUrl, myLine);
```

默认情况下，`JBCefBrowser` 使用隐式的 `JBCefClient` 创建（由系统自动处理）。
也可以传递自己的 `JBCefClient`（由开发人员处理）。

要访问浏览器，请使用 `JBCefClient`：

```java
JBCefClient getJBCefClient();
```

将浏览器组件添加到插件UI的最简单方法：

```java
JPanel myPanel = ...;
myPanel.add(new JBCefBrowser("https://example.com").getComponent());
```

### JBCefJSQuery

[`JBCefJSQuery`](%gh-ic%/platform/platform-api/src/com/intellij/ui/jcef/JBCefJSQuery.java) 提供了JS查询回调机制。

在Java中没有直接访问JS DOM的方法（就像在JavaFX WebView中一样，也可以参考[此问题](https://youtrack.jetbrains.com/issue/JBR-2046)）。
但是，JCEF提供了一种与JS通信的异步方式。

下面的示例显示了如何在外部浏览器中打开链接，并处理它：

```java
JBCefBrowser browser = new JBCefBrowser();
CefBrowser cefBrowser = browser.getCefBrowser();

// 创建一个JS查询实例
JBCefJSQuery openInBrowserJsQuery =
    JBCefJSQuery.create((JBCefBrowserBase)browser);

// 添加一个查询处理程序
openInBrowserJsQuery.addHandler((link) -> {
      // 在这里处理链接
      return null; // 可以用JBCefJSQuery.Response回应JS
    });

// 将查询回调注入到JS中
cefBrowser.executeJavaScript(
    "window.JavaPanelBridge = {" +
        "openInExternalBrowser : function(link) {" +
            openInBrowserJsQuery.inject("link") +
        "}" +
    "};",
    cefBrowser.getURL(), 0);

// 在必要时处理查询
Disposer.dispose(openInBrowserJsQuery);
```
