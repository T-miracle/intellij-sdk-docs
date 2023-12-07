<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 服务

<link-summary>注册和使用按需服务，以封装插件功能。</link-summary>

一个 _服务_ 是在插件调用相应的 [`ComponentManager`](%gh-ic%/platform/extensions/src/com/intellij/openapi/components/ComponentManager.java) 实例的 `getService()` 方法时按需加载的插件组件（参见 [Types](#类型)）。
IntelliJ 平台确保只加载一个服务实例，即使它被多次调用。
服务用于封装在一组相关类上操作的逻辑，或提供一些可在整个插件项目中使用的可重用功能，从概念上讲，它们与其他语言或框架中的服务类别无异。

服务必须有一个用于服务实例化的实现类。
服务还可以有一个用于获取服务实例并提供服务 API 的接口类。

需要关闭挂钩/清理例程的服务可以实现 [`Disposable`](%gh-ic%/platform/util/src/com/intellij/openapi/Disposable.java) 并在 `dispose()` 中执行必要的工作（参见 [自动处理的对象](disposers.md#automatically-disposed-objects)）。

#### 类型 {id=类型}

IntelliJ 平台提供三种类型的服务：_应用级_ 服务（全局单例）、_项目级_ 服务和 _模块级_ 服务。
对于后两者，将为其相应范围的每个实例创建一个单独的服务实例，参见 [项目模型介绍](project_structure.md)。

> 避免使用模块级服务，因为它可能会增加具有许多模块的项目的内存使用。
>
{style="note"}

#### 构造函数 {id=构造函数}

项目/模块级服务的构造函数可以具有 [`Project`](%gh-ic%/platform/core-api/src/com/intellij/openapi/project/Project.java)/[`Module`](%gh-ic%/platform/core-api/src/com/intellij/openapi/module/Module.java) 参数。
为了提高启动性能，避免在构造函数中进行任何繁重的初始化。

> 出于性能原因，已弃用（并且在 [](#轻量级服务) 中不支持）使用构造函数注入依赖服务。
>
> 所有其他服务依赖项必须在所有相应方法中 [仅在需要时获取](#获取服务)，例如，如果需要服务来获取一些数据或执行任务，请在调用其方法之前检索服务。
> 不要在构造函数中检索服务以将它们存储在类字段中。
>
> 使用检查 <control>Plugin DevKit | Code | Non-default constructors for service and extension class</control> 来验证代码。
>
{style="warning"}

## 轻量级服务 {id=轻量级服务}

不打算被覆盖的服务不需要在 <path>[plugin.xml](plugin_configuration_file.md)</path> 中注册（请参见 [声明服务](#获取服务)）。
相反，请使用 [`@Service`](%gh-ic%/platform/core-api/src/com/intellij/openapi/components/Service.java) 注解服务类。
项目级服务必须指定 `@Service(Service.Level.PROJECT)`。
服务实例将根据调用方的范围在相应的范围内创建（请参见 [获取服务](#获取服务)）。

### 轻量级服务限制

* 没有必需的属性：`os`、`client`、`overrides`、`id`、`preload`。
* 服务类必须是 `final`。
* 不支持[依赖服务的构造函数注入](#构造函数)。
* 如果应用级服务是 [PersistentStateComponent](persisting_state_of_components.md)，必须禁用漫游（`roamingType = RoamingType.DISABLED`）。

使用以下检查来验证这些限制并突出显示可以转换的服务（2023.3）：
- <control>Plugin DevKit | Code | Light service must be final</control>
- <control>Plugin DevKit | Code | Mismatch between light service level and its constructor</control>
- <control>Plugin DevKit | Code | A service can be converted to a light one</control> 和相应的 <control>Plugin DevKit | Plugin descriptor | A service can be converted to a light one</control> 用于 <path>plugin.xml</path>

### 示例

<tabs group="languages">


<tab title="Java" group-key="java">

应用级轻量级服务：

```java
@Service
public final class MyAppService {
  public void doSomething(String param) {
    // ...
  }
}
```

项目级轻量级服务示例：

```java
@Service(Service.Level.PROJECT)
public final class MyProjectService {
  private final Project myProject;

  public MyProjectService(Project project) {
    myProject = project;
  }

  public void doSomething(String param) {
    String projectName = myProject.getName();
    // ...
  }
}
```

</tab>

<tab title="Kotlin" group-key="kotlin">

应用级轻量级服务：

```kotlin
@Service
class MyAppService {
  fun doSomething(param: String) {
    // ...
  }
}
```

项目级轻量级服务示例：

```kotlin
@Service(Service.Level.PROJECT)
class MyProjectService(private val project: Project) {
  fun doSomething(param: String) {
    val projectName = project.name
    // ...
  }
}
```

</tab>

</tabs>

## 声明服务

要注册非 [轻量级服务](#轻量级服务)，为每种类型提供了不同的扩展点：

* `com.intellij.applicationService` - 应用级服务
* `com.intellij.projectService` - 项目级服务
* `com.intellij.moduleService` - 模块级服务（不建议使用，请参见上面的注意）

为了公开服务 API，请为 `serviceInterface` 创建一个单独的类，并在相应的已注册的 `serviceImplementation` 类中扩展它。
如果未指定 `serviceInterface`，则假定其与 `serviceImplementation` 具有相同的值。
使用检查 <control>Plugin DevKit | Plugin descriptor | Plugin.xml extension registration</control> 来突出显示多余的 `serviceInterface` 声明。

为了在测试/无头环境中提供自定义实现，另外指定 `testServiceImplementation`/`headlessImplementation`。

### 示例


<tabs group="languages">

<tab title="Java" group-key="java">

应用级服务：

- 接口：

  ```java
  public interface MyAppService {
    void doSomething(String param);
  }
  ```

- 实现：

  ```java
  public final class MyAppServiceImpl implements MyAppService {
    @Override
    public void doSomething(String param) {
      // ...
    }
  }
  ```

项目级服务：

- 接口：

  ```java
  public interface MyProjectService {
    void doSomething(String param);
  }
  ```

- 实现：

  ```java
  public final class MyProjectServiceImpl implements MyProjectService {
    private final Project myProject;

    public MyProjectServiceImpl(Project project) {
      myProject = project;
    }

    public void doSomething(String param) {
      String projectName = myProject.getName();
      // ...
    }
  }
  ```
</tab>

<tab title="Kotlin" group-key="kotlin">

应用级服务：

- 接口：

  ```kotlin
  interface MyAppService {
    fun doSomething(param: String)
  }
  ```

- 实现：

  ```kotlin
  class MyAppServiceImpl : MyAppService {
    override fun doSomething(param: String) {
      // ...
    }
  }
  ```

项目级服务：

- 接口：

  ```kotlin
  interface MyProjectService {
    fun doSomething(param: String)
  }
  ```

- 实现：

  ```kotlin
  class MyProjectServiceImpl(private val project: Project)
      : MyProjectService {

    fun doSomething(param: String) {
      val projectName = project.name
      // ...
    }
  }
  ```
</tab>

</tabs>

在 <path>plugin.xml</path> 中注册：
```xml
<extensions defaultExtensionNs="com.intellij">
  <!-- 声明应用级服务 -->
  <applicationService
      serviceInterface="com.example.MyAppService"
      serviceImplementation="com.example.MyAppServiceImpl"/>

  <!-- 声明项目级服务 -->
  <projectService
      serviceInterface="com.example.MyProjectService"
      serviceImplementation="com.example.MyProjectServiceImpl"/>
</extensions>
```

> 如果声明的服务打算供依赖于您的插件的其他插件使用，请考虑在插件分发中 [捆绑它们的源代码](bundling_plugin_openapi_sources.md)。
>
{style="note"}

## 获取服务 {id=获取服务}

> **永远不要**过早地获取服务实例或将它们存储在字段中以供以后使用。
> 相反，**总是**直接在需要它们的位置获取这些服务实例，**只在**需要的地方。
> 如果未这样做，可能会导致意外的异常并对插件的功能产生严重影响。
>
> 此类问题通过检查（2023.3）突出显示：
> - <control>Plugin DevKit | Code | Application service assigned to a static final field or immutable property</control>
> - <control>Plugin DevKit | Code | Incorrect service retrieving</control>
> - <control>Plugin DevKit | Code | Simplifiable service retrieving</control>
>
{style="warning"}

获取服务不需要读取动作，可以在任何线程中执行。
如果从多个线程请求服务，则它将在第一个线程中初始化，并阻塞其他线程，直到它完全初始化。

<tabs group="languages">
<tab title="Java" group-key="java">

```java
MyAppService applicationService =
    ApplicationManager.getApplication().getService(MyAppService.class);

MyProjectService projectService =
    project.getService(MyProjectService.class);
```

服务实现可以使用方便的静态 `getInstance()` 或 `getInstance(Project)` 方法包装这些调用：

```java
MyAppService applicationService = MyAppService.getInstance();

MyProjectService projectService = MyProjectService.getInstance(project);
```

</tab>

<tab title="Kotlin" group-key="kotlin">

```kotlin
val applicationService = service<MyAppService>()

val projectService = project.service<MyProjectService>()
```
</tab>

</tabs>

<procedure title="获取服务流程" collapsible="true" default-state="collapsed">

![获取服务](getting_service.svg){thumbnail="true" thumbnail-same-file="true"}

</procedure>

## 示例插件

为了阐明如何使用服务，请考虑[代码示例](%gh-sdk-samples%/max_opened_projects)中提供的 **maxOpenProjects** 示例插件。

该插件有一个应用级服务，用于计算当前在IDE中打开的项目数。
如果此数字超过插件允许的同时打开的项目的最大数量（3），则显示信息消息。

请参阅 [代码示例](code_samples.md) 了解如何设置和运行该插件。
