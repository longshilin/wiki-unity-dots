# 实体组件系统

实体组件系统 (ECS) 是 Unity Data-Oriented Tech Stack 的核心。顾名思义，ECS 具有三个主要部分：

-   [实体](https://docs.unity3d.com/Packages/com.unity.entities@0.17/manual/ecs_entities.html) — 填充您的游戏或程序的实体或事物。
-   [组件](https://docs.unity3d.com/Packages/com.unity.entities@0.17/manual/ecs_components.html)— 与您的实体相关联的数据，但由数据本身而非实体组织。（这种组织差异是面向对象和面向数据设计之间的主要区别之一。）
-   [系统](https://docs.unity3d.com/Packages/com.unity.entities@0.17/manual/ecs_systems.html) — 将组件数据从其当前状态转换为下一个状态的逻辑 — 例如，系统可能会通过速度乘以自前一帧以来的时间间隔来更新所有移动实体的位置。
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTQ4OTMwOTUxOSwtMjA4ODc0NjYxMiw3Mz
A5OTgxMTZdfQ==
-->