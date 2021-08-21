# System Update Order

Use Component System Groups to specify the update order of your systems. You can place a systems in a group using the [UpdateInGroup] attribute on the system’s class declaration. You can then use [UpdateBefore] and [UpdateAfter] attributes to specify the update order within the group.

>使用组件系统组指定系统的更新顺序。您可以使用系统类声明中的 [UpdateInGroup] 属性将系统放在一个组中。然后，您可以使用 [UpdateBefore] 和 [UpdateAfter] 属性来指定组内的更新顺序。


The ECS framework creates a set of  [default system groups](https://docs.unity3d.com/Packages/com.unity.entities@0.17/manual/system_update_order.html#default-system-groups)  that you can use to update your systems in the correct phase of a frame. You can nest one group inside another so that all systems in your group update in the correct phase and, then, also update according to the order within their group.

>ECS 框架创建了一组[默认系统组](https://docs.unity3d.com/Packages/com.unity.entities@0.17/manual/system_update_order.html#default-system-groups)，您可以使用它们在框架的正确阶段更新系统。您可以将一个组嵌套在另一个组中，以便您组中的所有系统在正确的阶段进行更新，然后也根据其组内的顺序进行更新。

## Component System Groups

The ComponentSystemGroup class represents a list of related component systems that should be updated together in a specific order. ComponentSystemGroup is derived from ComponentSystemBase, so it acts like a component system in all the important ways -- it can be ordered relative to other systems, has an OnUpdate() method, etc. Most relevantly, this means component system groups can be nested in other component system groups, forming a hierarchy.

>ComponentSystemGroup 类表示应该按特定顺序一起更新的相关组件系统的列表。ComponentSystemGroup 派生自 ComponentSystemBase，所以它在所有重要方面都像一个组件系统——它可以相对于其他系统进行排序，有一个 OnUpdate() 方法等。最相关的是，这意味着组件系统组可以嵌套在其他组件系统组，形成一个层次结构。

By default, when a ComponentSystemGroup’s  `Update()`  method is called, it calls Update() on each system in its sorted list of member systems. If any member systems are themselves system groups, they will recursively update their own members. The resulting system ordering follows a depth-first traversal of a tree.

>默认情况下，当 ComponentSystemGroup 的`Update()`方法被调用时，它会在其成员系统排序列表中的每个系统上调用 Update()。如果任何成员系统本身就是系统组，它们将递归更新自己的成员。生成的系统排序遵循树的深度优先遍历。

## System Ordering Attributes

The existing system ordering attributes are maintained, with slightly different semantics and restrictions.

>保留现有的系统排序属性，但语义和限制略有不同。

-   [UpdateInGroup] — specifies a ComponentSystemGroup that this system should be a member of. If this attribute is omitted, the system is automatically added to the default World’s SimulationSystemGroup (see below).
-   [UpdateBefore] and [UpdateAfter] — order systems relative to other systems. The system type specified for these attributes must be a member of the same group. Ordering across group boundaries is handled at the appropriate deepest group containing both systems:
    -   **Example:**  if SystemA is in GroupA and SystemB is in GroupB, and GroupA and GroupB are both members of GroupC, then the ordering of GroupA and GroupB implicitly determines the relative ordering of SystemA and SystemB; no explicit ordering of the systems is necessary.
-   [DisableAutoCreation] — prevents the system from being created during default world initialization. You must explicitly create and update the system. However, you can add a system with this tag to a ComponentSystemGroup’s update list, and it will then be automatically updated just like the other systems in that list.

>-   [UpdateInGroup] — 指定该系统应该是其成员的 ComponentSystemGroup。如果省略此属性，系统将自动添加到默认 World 的 SimulationSystemGroup（见下文）。
>-   [UpdateBefore] 和 [UpdateAfter] — 相对于其他系统的顺序系统。为这些属性指定的系统类型必须是同一组的成员。跨组边界的排序在包含两个系统的适当最深组中处理：
    -   **示例：**如果SystemA在GroupA中，SystemB在GroupB中，并且GroupA和GroupB都是GroupC的成员，那么GroupA和GroupB的顺序隐含决定了SystemA和SystemB的相对顺序；不需要对系统进行明确排序。
>-   [DisableAutoCreation] — 防止在默认世界初始化期间创建系统。您必须明确创建和更新系统。但是，您可以将带有此标记的系统添加到 ComponentSystemGroup 的更新列表中，然后它会像该列表中的其他系统一样自动更新。

## Default System Groups

The default World contains a hierarchy of ComponentSystemGroup instances. Only three root-level system groups are added to the Unity player loop (the following list also shows the pre-defined member systems in each group):

>默认世界包含 ComponentSystemGroup 实例的层次结构。Unity 播放器循环中仅添加了三个根级系统组（以下列表还显示了每个组中的预定义成员系统）：

-   InitializationSystemGroup (updated at the end of the  `Initialization`  phase of the player loop)
    -   BeginInitializationEntityCommandBufferSystem
    -   CopyInitialTransformFromGameObjectSystem
    -   SubSceneLiveLinkSystem
    -   SubSceneStreamingSystem
    -   EndInitializationEntityCommandBufferSystem
-   SimulationSystemGroup (updated at the end of the  `Update`  phase of the player loop)
    -   BeginSimulationEntityCommandBufferSystem
    -   TransformSystemGroup
        -   EndFrameParentSystem
        -   CopyTransformFromGameObjectSystem
        -   EndFrameTRSToLocalToWorldSystem
        -   EndFrameTRSToLocalToParentSystem
        -   EndFrameLocalToParentSystem
        -   CopyTransformToGameObjectSystem
    -   LateSimulationSystemGroup
    -   EndSimulationEntityCommandBufferSystem
-   PresentationSystemGroup (updated at the end of the  `PreLateUpdate`  phase of the player loop)
    -   BeginPresentationEntityCommandBufferSystem
    -   CreateMissingRenderBoundsFromMeshRenderer
    -   RenderingSystemBootstrap
    -   RenderBoundsUpdateSystem
    -   RenderMeshSystem
    -   LODGroupSystemV1
    -   LodRequirementsUpdateSystem
    -   EndPresentationEntityCommandBufferSystem

Note that the specific contents of this list is subject to change.

>-   InitializationSystemGroup (在播放器循环的`Initialization`阶段结束时更新)
    -   BeginInitializationEntityCommandBufferSystem
    -   CopyInitialTransformFromGameObjectSystem
    -   SubSceneLiveLinkSystem
    -   SubSceneStreamingSystem
    -   EndInitializationEntityCommandBufferSystem
>-   SimulationSystemGroup (在播放器循环的`Update`阶段结束时更新)
    -   BeginSimulationEntityCommandBufferSystem
    -   TransformSystemGroup
        -   EndFrameParentSystem
        -   CopyTransformFromGameObjectSystem
        -   EndFrameTRSToLocalToWorldSystem
        -   EndFrameTRSToLocalToParentSystem
        -   EndFrameLocalToParentSystem
        -   CopyTransformToGameObjectSystem
    -   LateSimulationSystemGroup
    -   EndSimulationEntityCommandBufferSystem
>-   PresentationSystemGroup (在播放器循环的`PreLateUpdate`阶段结束时更新)
    -   BeginPresentationEntityCommandBufferSystem
    -   CreateMissingRenderBoundsFromMeshRenderer
    -   RenderingSystemBootstrap
    -   RenderBoundsUpdateSystem
    -   RenderMeshSystem
    -   LODGroupSystemV1
    -   LodRequirementsUpdateSystem
    -   EndPresentationEntityCommandBufferSystem

>Note that the specific contents of this list is subject to change.
>注意，此列表的具体内容可能会有所更改。

>注：播放器循环的所有阶段。
>![](https://cdn.jsdelivr.net/gh/longshilin/images/20210821102945.png)

## Multiple Worlds

You can create multiple Worlds, in addition to (or instead of) the default World described above. The same component system class can be instantiated in more than one World, and each instance can be updated at different rates from different points in the update order.

>除了（或代替）上述默认世界之外，您还可以创建多个世界。同一个组件系统类可以在多个 World 中实例化，并且每个实例可以从更新顺序的不同点以不同的速率更新。

There is currently no way to manually update every system in a given World; instead, you can control which systems are created in which World, and which of the existing system groups they should be added to. Thus, a custom WorldB could instantiate SystemX and SystemY, adding SystemX to the default World’s SimulationSystemGroup, and adding SystemY to the default World’s PresentationSystemGroup. These systems can order themselves relative to their group siblings as usual, and will be updated along with the corresponding group.

To support this use case, a new ICustomBootstrap interface is now available:

>目前没有办法手动更新给定世界中的每个系统；相反，您可以控制在哪个世界中创建哪些系统，以及应该将它们添加到哪些现有系统组中。因此，自定义 WorldB 可以实例化 SystemX 和 SystemY，将 SystemX 添加到默认 World 的 SimulationSystemGroup，并将 SystemY 添加到默认 World 的 PresentationSystemGroup。这些系统可以像往常一样相对于它们的组兄弟来对自己进行排序，并且将与相应的组一起更新。

>为了支持这个用例，现在提供了一个新的 ICustomBootstrap 接口：

```
public interface ICustomBootstrap
{
    // Returns the systems which should be handled by the default bootstrap process.
    // If null is returned the default world will not be created at all.
    // Empty list creates default world and entrypoints
    List<Type> Initialize(List<Type> systems);
}

```

When you implement this interface, the full list of component system types will be passed to the classes  `Initialize()`  method, prior to default world initialization. A custom bootstrapper can iterate through this list and create systems in whatever World it wants. You can return a list of systems from the Initialize() method and they will be created as part of the normal, default world initialization.

>当您实现此接口时，组件系统类型的完整列表将`Initialize()`在默认世界初始化之前传递给 classes方法。自定义引导程序可以遍历此列表并在它想要的任何世界中创建系统。您可以从 Initialize() 方法返回系统列表，它们将作为正常的默认世界初始化的一部分创建。

For example, here’s the typical procedure of a custom  `MyCustomBootstrap.Initialize()`  implementation:

1.  Create any additional Worlds and their top-level ComponentSystemGroups.
2.  For each Type in the system Type list:
    1.  Traverse upward through the ComponentSystemGroup hierarchy to find this system Type’s top-level group.
    2.  If it’s one of the groups created in step 1, create the system in that World and add it to the hierarchy with  `group.AddSystemToUpdateList()`.
    3.  If not, append this Type to the List to return to DefaultWorldInitialization.
3.  Call group.SortSystemUpdateList() on new top-level groups.
    1.  Optionally add them to one of the default world groups
4.  Return list of unhandled systems to DefaultWorldInitialization.

>例如，以下是自定义`MyCustomBootstrap.Initialize()`实现的典型过程：
>
>1.  创建任何其他世界及其顶级 ComponentSystemGroup。
>2.  对于系统类型列表中的每个类型：
    1.  向上遍历 ComponentSystemGroup 层次结构以找到此系统类型的顶级组。
    2.  如果它是在步骤 1 中创建的组之一，则在该 World 中创建系统并将其添加到带有`group.AddSystemToUpdateList()`.
    3.  如果不是，则将此类型附加到列表以返回到 DefaultWorldInitialization。
>3.  在新的顶级组上调用 group.SortSystemUpdateList()。
    1.  （可选）将它们添加到默认世界组之一
>4.  将未处理的系统列表返回给 DefaultWorldInitialization。

##### NOTE

The ECS framework finds your ICustomBootstrap implementation by reflection.

>注意：
>ECS框架通过反射找到您的ICustomBootstrap实现。

## Tips and Best Practices

-   **Use [UpdateInGroup] to specify the system group for each system you write.**  If not specified, the implicit default group is SimulationSystemGroup.
-   **Use manually-ticked ComponentSystemGroups to update systems elsewhere in the Unity player loop.**  Adding the [DisableAutoCreation] attribute to a component system (or system group) prevents it from being created or added to the default system groups. You can still manually create the system with World.GetOrCreateSystem() and update it by calling manually calling MySystem.Update() from the main thread. This is an easy way to insert systems elsewhere in the Unity player loop (for example, if you have a system that should run later or earlier in the frame).
-   **Use the existing  `EntityCommandBufferSystem`s instead of adding new ones, if possible.**  An  `EntityCommandBufferSystem`  represents a sync point where the main thread waits for worker threads to complete before processing any outstanding  `EntityCommandBuffer`s. Reusing one of the predefined Begin/End systems in each root-level system group is less likely to introduce a new "bubble" into the frame pipeline than creating a new one.
-   **Avoid putting custom logic in  `ComponentSystemGroup.OnUpdate()`**. Since  `ComponentSystemGroup`  is functionally a component system itself, it may be tempting to add custom processing to its OnUpdate() method, to perform some work, spawn some jobs, etc. We advise against this in general, as it’s not immediately clear from the outside whether the custom logic is executed before or after the group’s members are updated. It’s preferable to keep system groups limited to a grouping mechanism, and to implement the desired logic in a separate component system, explicitly ordered relative to the group.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTcyMjczNTY3NywtNTY3NTcwMzg2LDEyOT
U0NDg1ODYsLTIxNDA4MzkzODUsLTE1NTU5MTE5NSwxOTEyMzkw
NzEzLDE2OTY2MDA0MjAsNjY3MDQzMDE2LDE0MDIxMTU1ODksNz
MwOTk4MTE2XX0=
-->