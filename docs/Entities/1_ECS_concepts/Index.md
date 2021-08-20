# ECS概念

实体组件系统 (ECS) 架构将身份（**实体**）、数据（**组件**）和行为（**系统**）分开。该架构专注于数据。系统读取组件数据流，然后将数据从输入状态转换为输出状态，然后实体对其进行索引。

下图说明了这三个基本部分如何协同工作：

![](https://docs.unity3d.com/Packages/com.unity.entities@0.17/manual/images/ECSBlockDiagram.png)

在此图中，系统读取`Translation`和`Rotation`组件，将它们相乘，然后更新相应的`LocalToWorld`组件 ( `L2W = T*R`)。

实体 A 和 B 有一个`Renderer`组件而实体 C 没有，这一事实不会影响系统，因为系统不关心`Renderer`组件。

您可以设置一个系统，使其需要一个`Renderer`组件，在这种情况下，系统将忽略实体 C 的组件；或者，您可以设置一个系统来排除具有`Renderer`组件的实体，然后忽略实体 A 和 B 的组件。

## Archetypes

组件类型的独特组合称为[EntityArchetype](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Entities.EntityArchetype.html)。例如，一个 3D 对象可能有一个用于其世界变换的组件，一个用于线性运动，一个用于旋转，一个用于其视觉表示。这些 3D 对象之一的每个实例对应于单个实体，但由于它们共享相同的组件集，ECS 将它们分类为单个原型：

![](https://docs.unity3d.com/Packages/com.unity.entities@0.17/manual/images/ArchetypeDiagram.png)

在此图中，实体 A 和 B 共享原型 M，而实体 C 具有原型 N。

要顺利更改实体的原型，您可以在运行时添加或删除组件。例如，如果您`Renderer`从实体 B 中移除组件，则它会移动到原型 N。

## Memory Chunks

实体的原型决定了 ECS 存储该实体组件的位置。ECS 以“块”的形式分配内存，每个块由一个[ArchetypeChunk](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Entities.ArchetypeChunk.html)对象表示。块总是包含单个原型的实体。当一块内存变满时，ECS 会为使用相同原型创建的任何新实体分配一块新内存。如果您添加或删除组件，然后更改实体原型，ECS 会将该实体的组件移动到不同的块。

![](https://docs.unity3d.com/Packages/com.unity.entities@0.17/manual/images/ArchetypeChunkDiagram.png)

这种组织方案提供了原型和块之间的一对多关系。这也意味着找到具有给定组件集的所有实体只需要搜索现有的原型，这些原型通常数量很少，而不是所有实体，它们的数量通常要大得多。

ECS 不会以特定顺序存储块中的实体。当一个实体被创建或更改为新的原型时，ECS 将其放入存储原型的第一个块中，该块有空间。然而，块仍然紧紧地包装在一起。当实体从原型中移除时，ECS 将块中最后一个实体的组件移动到组件数组中新腾出的插槽中。

##### 注意

原型中共享组件的值还决定了哪些实体存储在哪个块中。给定块中的所有实体对于任何共享组件都具有完全相同的值。如果您更改共享组件中任何字段的值，则修改后的实体将移动到不同的块，就像您更改该实体的原型一样。如有必要，将分配一个新块。

当一起处理它们更有效时，使用共享组件将原型内的实体分组。例如，Hybrid Renderer 定义了它的[RenderMesh 组件](https://docs.unity3d.com/Packages/com.unity.rendering.hybrid@latest?subfolder=/api/Unity.Rendering.RenderMesh.html)来实现这一点。

## Entity queries

要确定系统应该处理哪些实体，请使用[EntityQuery](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Entities.EntityQuery.html)。实体查询在现有原型中搜索那些具有符合您要求的组件的原型。您可以使用查询指定以下组件要求：

-   **All** — 原型必须包含**All**类别中的**所有**组件类型。
-   **Any** — 原型必须至少包含**Any**类别中的一种组件类型。
-   **None** — 原型不得包含**None**类别中的任何组件类型。

实体查询提供包含查询所需组件类型的块列表。然后，您可以直接使用[IJobEntityBatch](https://docs.unity3d.com/Packages/com.unity.entities@0.17/manual/ecs_ijobentitybatch.html)迭代这些块中的组件。

## Jobs

要利用多线程，您可以使用[C# 作业系统](https://docs.unity3d.com/2020.1/Documentation/Manual/JobSystem.html)。ECS 提供[SystemBase](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Entities.SystemBase.html)类`Entities.ForEach`以及[IJobEntityBatch](https://docs.unity3d.com/Packages/com.unity.entities@0.17/manual/ecs_ijobentitybatch.html)  `Schedule()`和`ScheduleParallel()`方法，用于在主线程之外转换数据。`Entities.ForEach`是最简单的使用方法，通常需要更少的代码来实现。您可以将 IJobChunk 用于更复杂的`Entities.ForEach`无法处理的情况。

ECS 按照[系统的排列顺序](https://docs.unity3d.com/Packages/com.unity.entities@0.17/manual/ecs_core.html#system-organization)在主线程上调度作业。当作业被调度时，ECS 会跟踪哪些作业读取和写入哪些组件。读取组件的作业依赖于写入同一组件的任何先前计划作业，反之亦然。作业调度程序使用作业依赖关系来确定哪些作业可以并行运行，哪些必须按顺序运行。

## 系统组织

ECS 按[World](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Entities.World.html)然后按[组](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Entities.ComponentSystemGroup.html)组织系统。默认情况下，ECS 使用一组预定义的组创建一个默认世界。它找到所有可用的系统，实例化它们，并将它们添加到默认世界中的预定义[模拟组](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Entities.SimulationSystemGroup.html)。

您可以指定同一组内系统的更新顺序。组是一种系统，因此您可以将一个组添加到另一个组并指定其顺序，就像任何其他系统一样。组内的所有系统在下一个系统或组之前更新。如果您不指定顺序，ECS 会以不依赖于创建顺序的确定性方式将系统插入到更新顺序中。换句话说，即使您没有明确指定顺序，同一组系统在其组内始终以相同的顺序更新。

系统更新发生在主线程上。但是，系统可以使用作业将工作卸载到其他线程。[SystemBase](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Entities.SystemBase.html)提供了一种直接的方式来创建和调度作业。

有关系统创建、更新顺序以及可用于组织系统的属性的更多信息，请参阅有关[系统更新顺序](https://docs.unity3d.com/Packages/com.unity.entities@0.17/manual/system_update_order.html)的文档。

## ECS创作

当您在 Unity 编辑器中创建游戏或应用程序时，您可以使用 GameObjects 和 MonoBehaviours 创建一个转换系统，将这些 UnityEngine 对象和组件映射到实体。有关更多信息，请参阅[创建游戏玩法](https://docs.unity3d.com/Packages/com.unity.entities@0.17/manual/gp_overview.html)。
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE3NDg5MTUzNTMsLTE0MTgwODIzNDhdfQ
==
-->