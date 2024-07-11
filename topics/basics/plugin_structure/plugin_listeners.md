<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 侦听器

<link-summary>侦听器允许订阅应用程序和项目事件。</link-summary>

**监听器** 允许插件通过消息总线订阅事件（详见 [消息基础设施](messaging_infrastructure.md)）。

监听器可以在应用程序（全局）或 [项目](project.md) 级别定义。

> 所有可用的监听器/主题列在 [](intellij_platform_extension_point_list.md) 和 [](intellij_platform_extension_point_list.md) 的 **监听器** 部分。
> 通过 [IntelliJ 平台浏览器](https://jb.gg/ipe) 浏览开源 IntelliJ 平台插件现有实现中的用法。

监听器实现必须是无状态的，并且不能实现生命周期（例如，`Disposable`）。
使用检查 <control>Plugin DevKit | Code | Listener implementation implements 'Disposable'</control> 进行验证（2023.3）。

监听器的声明式注册（2019.3 及以后）可以比从代码中注册监听器实现更好的性能。
优势在于监听器实例在第一次发送事件到主题时才会被延迟创建，而不是在应用程序启动或项目打开时创建。

## 定义应用程序级别的监听器 {id=defining-application-level-listeners}

要定义应用程序级别的监听器，请在 <path>[plugin.xml](plugin_configuration_file.md)</path> 中添加 [`<applicationListeners>`](plugin_configuration_file.md#idea-plugin__applicationListeners) 部分：

```xml
<idea-plugin>
  <applicationListeners>
    <listener
        class="myPlugin.MyListenerClass"
        topic="BaseListenerInterface"/>
  </applicationListeners>
</idea-plugin>
```

`topic` 属性指定了接收事件类型对应的监听器接口。
通常，这是用作事件类型的 [`Topic`](%gh-ic%/platform/extensions/src/com/intellij/util/messages/Topic.java) 实例的类型形参的接口。
`class` 属性指定了插件中实现该监听器接口并接收事件的类。

作为一个具体示例，要接收所有 [虚拟文件系统](virtual_file_system.md) 更改的事件，实现 `BulkFileListener` 接口，该接口对应 `VirtualFileManager.VFS_CHANGES` 主题。
要从代码中订阅此主题，请使用类似以下的代码片段：

```java
messageBus.connect().subscribe(VirtualFileManager.VFS_CHANGES,
    new BulkFileListener() {
      @Override
      public void after(@NotNull List<? extends VFileEvent> events) {
        // 处理事件
      }
});
```

使用声明式注册时，不再需要引用 `Topic` 实例。
相反，直接引用监听器接口类：

```xml
<applicationListeners>
  <listener
      class="myPlugin.MyVfsListener"
      topic="com.intellij.openapi.vfs.newvfs.BulkFileListener"/>
</applicationListeners>
```

然后提供监听器实现：

```java
package myPlugin;

final class MyVfsListener implements BulkFileListener {
  @Override
  public void after(@NotNull List<? extends VFileEvent> events) {
    // 处理事件
  }
}
```

## 定义项目级 listeners {id=defining-project-level-listeners}

[项目](project.md)-级 listeners 的注册方式与其他类似，唯一的区别在于顶层标签是 [`<projectListeners>`](plugin_configuration_file.md#idea-plugin__projectListeners)。
它们用于监听项目级事件，例如 [工具窗口](tool_windows.md) 操作：

```xml
<idea-plugin>
  <projectListeners>
    <listener
        class="myPlugin.MyToolWindowListener"
        topic="com.intellij.openapi.wm.ex.ToolWindowManagerListener"/>
  </projectListeners>
</idea-plugin>
```

实现 listener 接口的类可以定义一个接受 [`Project`](%gh-ic%/platform/core-api/src/com/intellij/openapi/project/Project.java) 的单参数构造函数，它将接收创建 listener 的项目实例：

```java
package myPlugin;

final class MyToolWindowListener implements ToolWindowManagerListener {
  private final Project project;

  MyToolWindowListener(Project project) {
    this.project = project;
  }

  @Override
  public void stateChanged(@NotNull ToolWindowManager toolWindowManager) {
    // 处理状态变化
  }
}
```

## 附加属性 {id=additional-attributes}

可以使用以下属性限制 listeners 的注册。

`os`
: 允许将 listener 限制在特定操作系统上，例如 `os="windows"` 仅适用于 Windows（2020.1 及以后版本）

`activeInTestMode`
: 如果 [`Application.isUnitTestMode()`](%gh-ic%/platform/core-api/src/com/intellij/openapi/application/Application.java) 返回 `true`，将其设置为 `false` 可禁用 listener。

`activeInHeadlessMode`
: 如果 [`Application.isHeadlessEnvironment()`](%gh-ic%/platform/core-api/src/com/intellij/openapi/application/Application.java) 返回 `true`，将其设置为 `false` 可禁用 listener。
也覆盖了测试模式，因为测试模式意味着无头模式。

> 如果声明的 listener 话题意图被依赖于您的插件的其他插件使用，请考虑在插件分发中 [捆绑它们的源代码](bundling_plugin_openapi_sources.md)。
>
{style="note"}
