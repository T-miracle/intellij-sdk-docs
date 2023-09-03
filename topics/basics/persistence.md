<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 持久化模型

<link-summary>IntelliJ 平台持久性模型简介。</link-summary>

The IntelliJ Platform Persistence Model is used to store a variety of information.
For example, [Run Configurations](run_configurations.md) and [Settings](settings.md) are stored using the Persistence Model.

There are two distinct approaches, depending on the type of data being persisted:
* [Persisting State of Components](persisting_state_of_components.md)
* [Persisting Sensitive Data](persisting_sensitive_data.md)
