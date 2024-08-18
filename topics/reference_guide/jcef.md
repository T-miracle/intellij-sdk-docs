<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

#  嵌入式浏览器 (JCEF)
<primary-label ref="2020.2"/>

<link-summary>在 IDE 中嵌入基于 Chromium 的浏览器。</link-summary>

> JCEF 从 2020.1 开始作为实验特性提供，并且从 2020.2 起默认启用。

JCEF（Java Chromium Embedded Framework）是 [CEF](https://bitbucket.org/chromiumembedded/cef/wiki/Home) 的 Java 版本。
它允许在 Swing 应用程序中嵌入 [基于 Chromium 的浏览器](https://www.chromium.org/Home)。

在 IDE 内嵌入浏览器组件可以用于：

- 渲染 HTML 内容
- 预览生成的 HTML（例如，来自 Markdown）
- 创建自定义的基于 web 的组件（例如，图表预览、图像浏览器等）

推荐在默认的 IntelliJ Platform UI 框架中实现 UI，即 Swing。
只有在插件需要显示 HTML 文档或标准的 UI 创建方法不足够时，才考虑使用 JCEF 方法。

JCEF 替代了过去在 IDE 中用来渲染 web 内容的 JavaFX。

> 从 2020.2 开始，使用 JavaFX 的第三方插件已被弃用。
> 要在 2020.2 或更高版本中继续使用 JavaFX，请添加对 [JavaFX Runtime for Plugins](https://plugins.jetbrains.com/plugin/14250-javafx-runtime-for-plugins) 的显式 [依赖](plugin_dependencies.md)（不推荐）。
>
> 请参阅 [IntelliJ Platform 中的 JavaFX 和 JCEF](https://blog.jetbrains.com/platform/2020/07/javafx-and-jcef-in-the-intellij-platform/) 博客文章了解详细信息。
>
{style="warning" title="使用 JavaFX"}

## 启用 JCEF {id=enabling-jcef}

<tabs>
<tab title="2020.2及更高版本">

自 2020.2 起，JCEF 可用并默认启用。
无需额外操作。

</tab>
<tab title="早期版本">

使用 JCEF 需要使用专用的 JetBrains Runtime 并在 IDE 注册表中启用 JCEF。

<procedure>

1. 访问 [JetBrains Runtime 发布列表](https://github.com/JetBrains/JetBrainsRuntime/releases)。
2. 下载适合你操作系统的“启动 IntelliJ IDEA 的二进制文件”，例如，macOS 的 `<path>jbr_jcef-17.0.9-osx-x64-b1087.7.tar.gz</path>`。
3. 解压缩归档文件。
4. 按照 [IDEA Web 帮助](https://www.jetbrains.com/help/idea/2020.2/switching-boot-jdk.html) 中描述的步骤操作，并选择下载的 JBR。
5. 调用 <ui-path>Help | Find Action...</ui-path>，输入“Registry”，按回车打开 <control>Registry</control> 对话框。
6. 启用 `ide.browser.jcef.enabled` 标志。
7. 重启 IDE 以使更改生效。

</procedure>

</tab>
</tabs>

## 在插件中使用 JCEF {id=using-jcef-in-a-plugin}

IntelliJ Platform API 暴露的核心 JCEF 类是 [`JBCefApp`](%gh-ic%/platform/platform-api/src/com/intellij/ui/jcef/JBCefApp.java)。
它负责初始化 JCEF 上下文并管理其生命周期。

无需显式初始化 `JBCefApp`。
在调用 `JBCefApp.getInstance()` 时或创建[浏览器](#browser)或[客户端](#browser-client)对象时，它会自动初始化。

在使用 JCEF API 之前，需要检查当前运行的 IDE 是否支持 JCEF。
可以通过调用 `JBCefApp.isSupported()` 来完成此操作：

```java
if (JBCefApp.isSupported()) {
  // 使用 JCEF
} else {
  // 可选的备用无浏览器解决方案
}
```

JCEF 可能不受支持的情况包括：
- IDE 使用了不包含 JCEF 的备用 JDK 启动。
- 其版本与当前运行的 IDE 不兼容。

### 浏览器 {id=browser}

JCEF 浏览器由 [`JBCefBrowser`](%gh-ic%/platform/platform-api/src/com/intellij/ui/jcef/JBCefBrowser.java) 类表示。
它负责在实际的基于 Chromium 的浏览器中加载和渲染请求的文档。

JCEF 浏览器可以通过使用 `JBCefBrowser` 类的构造函数，或通过 [`JBCefBrowserBuilder`](%gh-ic%/platform/platform-api/src/com/intellij/ui/jcef/JBCefBrowserBuilder.java) 创建。
在浏览器需要默认的[客户端](#browser-client)和默认选项时，使用构造函数即可。
构建器方法允许使用自定义客户端和配置其他选项。

#### 将浏览器添加到 UI {id=adding-browser-to-ui}

[`JBCefBrowser.getComponent()`](%gh-ic%/platform/platform-api/src/com/intellij/ui/jcef/JBCefBrowser.java) 提供了嵌入实际浏览器的 UI 组件。
这个组件是 Swing `JComponent` 的实例，可以添加到插件 UI 中：

```java
// 假设 'JPanel myPanel' 是工具窗口 UI 的一部分
JBCefBrowser browser = new JBCefBrowser();
myPanel.add(browser.getComponent());
```

#### 加载文档 {id=loading-documents}

要在浏览器中加载文档，使用 `JBCefBrowserBase.load*()` 方法之一。
加载文档的方法可以从 EDT 和后台线程调用。
可以设置一个初始 URL（传递给构造函数或构建器），在浏览器创建和初始化时加载。

To load a document in the browser, use one of [`JBCefBrowserBase.load*()`](%gh-ic%/platform/platform-api/src/com/intellij/ui/jcef/JBCefBrowserBase.java) methods.
Methods loading documents can be called from both [EDT and background threads](threading_model.md).
It is possible to set an initial URL (passed to constructor or builder) that will be loaded when browser is created and initialized.

### 浏览器客户端 {id=browser-client}

浏览器客户端提供了设置与各种浏览器事件相关的[处理程序](#event-handlers)的接口，例如：
- 加载 HTML 文档
- 控制台消息打印
- 浏览器获得焦点

处理程序允许在插件代码中响应这些事件，并改变浏览器的行为。
每个浏览器绑定到一个客户端，一个客户端可以与多个浏览器实例共享。

浏览器客户端由 [`JBCefClient`](%gh-ic%/platform/platform-api/src/com/intellij/ui/jcef/JBCefClient.java) 表示，这是对 JCEF 的 [`CefClient`](%gh-jcef-master%/org/cef/CefClient.java) 的包装。
`JBCefClient` 允许注册多个同类型的处理程序，而 `CefClient` 则不支持。
要访问底层的 `CefClient` 及其 API，请调用 `JBCefClient.getCefClient()`。

```java
JBCefClient client = new JBCefClient();
CefClient cefClient = client.getCefClient();
```

#### 创建和访问客户端 {id=creating-and-accessing-client}

如果没有传递特定的客户端，`JBCefBrowser` 实例将绑定到默认客户端，该客户端会隐式创建。
隐式客户端在关联的浏览器实例被处置时会自动[释放](#disposing-resources)。

对于更高级的用例，通过调用 `JBCefApp.createClient()` 创建自定义客户端，并注册所需的[处理程序](#event-handlers)。
自定义客户端必须在插件代码中显式处置。

要访问与浏览器关联的客户端，调用 `JBCefBrowser.getJBCefClient()`。

```java
JBCefClient client = JBCefApp.createClient();
JBCefBrowser browser = new JBCefBrowser(client);
JBCefClient associatedClient = browser.getJBCefClient();
```

### 事件处理程序 {id=event-handlers}

JCEF API 提供了各种事件处理程序接口，允许处理浏览器发出的大量事件。示例处理程序：
- [`CefLoadHandler`](%gh-jcef-master%/org/cef/handler/CefLoadHandler.java) - 处理浏览器加载事件。<br/>
  **示例：** 实现 `CefLoadHandler.onLoadEnd()` 来在文档加载后[执行脚本](#executing-javascript)。

- [`CefDisplayHandler`](%gh-jcef-master%/org/cef/handler/CefDisplayHandler.java) - 处理与浏览器显示状态相关的事件。<br/>
  **示例：** 实现 `CefDisplayHandler.onAddressChange()` 在点击本地文件链接时在浏览器中加载项目文件，或点击外部链接时打开外部浏览器。

- [`CefContextMenuHandler`](%gh-jcef-master%/org/cef/handler/CefContextMenuHandler.java) - 处理上下文菜单事件。<br/>
  **示例：** 实现 `CefContextMenuHandler.onBeforeContextMenu()` 来更改浏览器上下文菜单的项目。

- [`CefDownloadHandler`](%gh-jcef-master%/org/cef/handler/CefDownloadHandler.java) - 文件下载事件。<br/>
  **示例：** 实现 `CefDownloadHandler.onBeforeDownload()` 以启用在嵌入式浏览器中下载文件。

请参阅 [org.cef.handler](%gh-jcef-master%/org/cef/handler) 包中的所有可用处理程序。

> 对于每个处理程序接口，JCEF API 提供了一个适配器类，可以扩展该类以避免实现未使用的方法，例如，[`CefLoadHandlerAdapter`](%gh-jcef-master%/org/cef/handler/CefLoadHandlerAdapter.java)。

处理程序应使用 `JBCefClient.getCefClient().add*Handler()` 方法进行注册。

> 请注意，`JBCefClient` 提供了添加处理程序的方法，但不推荐使用这些方法。
>
{style="warning"}

### 执行 JavaScript {id=executing-javascript}

JCEF API 允许从插件代码在嵌入式浏览器中执行 JavaScript 代码。
JavaScript 可用于操作 DOM、创建在实现功能中需要的函数、注入样式等。

在最简单的情况下，可以使用 `JBCefBrowser.getCefBrowser().executeJavaScript()` 执行 JavaScript 代码，例如：

```java
browser.getCefBrowser().executeJavaScript(
    "alert('Hello World!')",
    url, lineNumber
);
```

上述代码将在嵌入式浏览器中执行，并显示一个带有 "Hello World!" 消息的警告框。
`url` 和 `lineNumber` 参数用于浏览器中的错误报告，如果脚本抛出错误。
这些参数的目的是帮助调试错误，并且对于脚本执行并非至关重要。
通常将 `browser.getCefBrowser().getUrl()` 或 null/空字符串，以及 `0` 作为这些参数传递。

### 从 JavaScript 执行插件代码 {id=executing-plugin-code-from-javascript}

JCEF 不提供直接从插件代码访问 DOM 的功能（未来可能会[更改](https://youtrack.jetbrains.com/issue/JBR-2046)），与 JavaScript 的异步通信通过回调机制实现。
它允许通过 JavaScript 从嵌入的浏览器执行插件代码，例如，当点击按钮或链接、按下快捷键、调用 JavaScript 函数等时。

JavaScript 查询回调由 [`JBCefJSQuery`](%gh-ic%/platform/platform-api/src/com/intellij/ui/jcef/JBCefJSQuery.java) 表示。
它是一个绑定到特定浏览器的对象，持有一组实现所需插件行为的处理器。

考虑一个需要在编辑器中打开本地文件链接，在外部浏览器中打开外部链接的案例。这样的需求可以如下实现（每一步在代码片段下方进行解释）：

```java
JBCefJSQuery openLinkQuery = JBCefJSQuery.create(browser); // 1
openLinkQuery.addHandler((link) -> { // 2
    if (LinkUtil.isExternal(link)) {
      BrowserUtil.browse(link);
    } else {
      EditorUtil.openFileInEditor(link);
    }
    return null; // 3
});

browser.getCefBrowser().executeJavaScript( // 4
    "window.openLink = function(link) {" +
      openLinkQuery.inject("link") + // 5
      "};",
    browser.getCefBrowser().getURL(), 0
);

browser.getCefBrowser().executeJavaScript( // 6
    """
    document.addEventListener('click', function (e) {
      const link = e.target.closest('a').href;
      if (link) {
        window.openLink(link);
      }
    });""",
    browser.getCefBrowser().getURL(), 0
);
```

1. 创建 `JBCefJSQuery` 实例。确保传递的浏览器实例为 `JBCefBrowserBase` 类型（可能需要进行类型转换）。
2. 添加一个处理器实现要执行的插件代码。
   示例实现根据链接是本地还是外部，将链接在编辑器或外部浏览器中打开。
3. 处理器可以选择性地返回 `JBCefJSQuery.Response` 对象，该对象包含插件代码端发生的成功或错误信息。
   如果需要，可以在浏览器中处理此信息。
4. 执行 JavaScript 代码，创建自定义的 `openLink` 函数。
5. 注入 JavaScript 代码，负责调用步骤 2 中实现的插件代码。
   添加到 `openLinkQuery` 的处理器将在每次调用 `openLink` 函数时被触发。

   注意 `JBCefJSQuery.inject()` 方法的 `"link"` 参数。
   它是 `openLink` 函数的 `link` 参数名称。
   该值被注入到查询函数调用中，可以是处理器所需的任何值，例如 `"myJsObject.prop"`、`"'JavaScript string'"` 等。
6. 执行 JavaScript 代码，注册一个点击事件监听器。
   每当点击浏览器中的 `a` 元素时，监听器将调用步骤 4 中定义的 `openLink` 函数，并传入点击链接的 `href` 值。

### 从插件分发中加载资源 {id=loading-resources-from-plugin-distribution}

当插件功能实现了基于 Web 的用户界面时，插件可能会在其[分发](plugin_content.md)中提供 HTML、CSS 和 JavaScript 文件，或者根据某些配置动态生成这些文件。
这些资源无法被浏览器轻松访问。

可以通过实现适当的请求[处理器](#event-handlers)来使这些资源可访问，使其在预定义的 URL 上对浏览器可用。

这种方法需要实现 [`CefRequestHandler`](%gh-jcef-master%/org/cef/handler/CefRequestHandler.java) 和 [`CefResourceRequestHandler`](%gh-jcef-master%/org/cef/handler/CefResourceRequestHandler.java)，这些处理器将资源路径映射到资源提供者。

这类资源的服务由负责在 IntelliJ Platform 基于 IDE 中显示 SVG 文件的 Image Viewer 组件实现。
有关实现细节，请参见 [`JCefImageViewer`](%gh-ic%/images/src/org/intellij/images/editor/impl/jcef/JCefImageViewer.kt) 和相关类。

### 滚动条的外观和感觉 {id=scrollbars-look-and-feel}

默认的浏览器滚动条可能不足以满足需求，例如，当它们与 IDE 的滚动条外观不匹配，或者需要特定的外观和行为时。

在 JCEF 浏览器中，滚动条的外观和感觉可以通过 CSS 和 JavaScript 进行自定义。IntelliJ Platform 提供了 [`JBCefScrollbarsHelper`](%gh-ic%/platform/platform-api/src/com/intellij/ui/jcef/JBCefScrollbarsHelper.java)，允许以两种方式自定义滚动条：
1. 使用 `JBCefScrollbarsHelper.getOverlayScrollbarStyle()`，该方法提供适配 IDE 滚动条的样式。
2. 使用 [OverlayScrollbars](https://kingsora.github.io/OverlayScrollbars/) 库，该库已适配 IDE 的外观和感觉。
   详情请参见 `getOverlayScrollbarsSourceCSS()`、`getOverlayScrollbarsSourceJS()` 和 `buildScrollbarsStyle()` 的 Javadoc。
   当需要透明滚动条或其他高级选项时，应使用该库。

### 释放资源 {id=disposing-resources}

`JBCefBrowser`、`JBCefClient` 和 `JBCefJSQuery` 类实现了 [`JBCefDisposable`](%gh-ic%/platform/platform-api/src/com/intellij/ui/jcef/JBCefDisposable.java)，该接口扩展了 [`Disposable`](%gh-ic%/platform/util/src/com/intellij/openapi/Disposable.java)。
这意味着这些类应按照 [disposers.md](disposers.md) 页面上描述的规则清理其资源。

例如，自定义的 `JBCefClient` 如果注册了处理器，应该在 `dispose()` 方法的实现中移除它们。

## 测试 {id=testing}

参见 [`JBCefTestHelper`](%gh-ic%/platform/platform-tests/testSrc/com/intellij/ui/jcef/JBCefTestHelper.java) 以及该包中的测试。

## 调试 {id=debugging}

嵌入在 JCEF 中的 [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools/) 可以用作调试和分析工具。
默认情况下，它是启用的，因此 Chrome DevTools 客户端可以通过默认的 9222 端口连接到它。
默认端口可以通过注册表键 `ide.browser.jcef.debug.port` 更改（前往 <ui-path>Help | Find Action...</ui-path> 并输入 "Registry"）。

因此，IntelliJ IDEA Ultimate 中的 JavaScript 调试器可以用于调试通过 Chrome DevTools 在 IDE 中运行的 JavaScript 代码。
使用 [<control>Attach to Node.js/Chrome</control>](https://www.jetbrains.com/help/idea/run-debug-configuration-node-js-remote-debug.html) 配置，并指定正确的端口号。

此外，JCEF 提供了一个默认的 Chrome DevTools 前端（类似于 Chrome 浏览器中的前端），可以通过 JCEF 的浏览器组件上下文菜单中的 <ui-path>Open DevTools</ui-path> 打开。
此菜单项仅在 [内部模式](enabling_internal.md) 下可用，并且从版本 2021.3 开始，注册表键 `ide.browser.jcef.contextMenu.devTools.enabled` 必须显式设置为 `true`。

### 程序化访问 DevTools {id=accessing-devtools-programmatically}

要在插件代码中访问 Chrome DevTools，请使用以下 API：

```java
CefBrowser devTools = browser.getCefBrowser().getDevTools();
JBCefBrowser devToolsBrowser = JBCefBrowser.createBuilder()
    .setCefBrowser(devTools)
    .setClient(browser.getJBCefClient())
    .build();
```

要在单独的窗口中打开 DevTools，请调用 `JBCefBrowser.openDevtools()`。

## JCEF 使用示例 {id=jcef-usage-examples}

- [Markdown 预览面板](%gh-ic%/plugins/markdown/core/src/org/intellij/plugins/markdown/ui/preview/jcef/MarkdownJCEFHtmlPanel.kt)
- [SVG 图像查看器](%gh-ic%/images/src/org/intellij/images/editor/impl/jcef/JCefImageViewer.kt)
- [PDF 查看器](https://github.com/FirstTimeInForever/intellij-pdf-viewer) 插件
- [CodeStream](https://github.com/TeamCodeStream/codestream) 插件
- [Excalidraw 集成](https://github.com/bric3/excalidraw-jetbrains-plugin) 插件
- [使用 WebView 创建 IntelliJ 插件](https://medium.com/virtuslab/creating-intellij-plugin-with-webview-3b27c3f87aea) 博客文章

<include from="snippets.md" element-id="missingContent"/>
