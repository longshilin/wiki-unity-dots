# 创造玩法
This section contains information on how to create DOTS-based games and other applications in the Unity Editor. It also covers the systems and components that ECS provides to help you implement game features.

The systems include:

-   [Unity.Transforms](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Transforms.html): Provides components to define world-space transforms, 3D object hierarchies, and systems to manage them.
-   [Unity.Hybrid.Renderer](https://docs.unity3d.com/Packages/com.unity.rendering.hybrid@latest): Provides components and systems to render ECS entities in the Unity runtime.

>本节包含有关如何在Unity编辑器中创建基于DOTS的游戏和其他应用程序的信息。它还涵盖了ECS提供的用于帮助您实现游戏功能的系统和组件。
>
>这些系统包括：
>-   [Unity.Transforms](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Transforms.html)：提供定义世界空间变换、3D 对象层次结构和管理它们的系统的组件。
>-   [Unity.Hybrid.Renderer](https://docs.unity3d.com/Packages/com.unity.rendering.hybrid@latest)：提供组件和系统以在 Unity 运行时渲染 ECS 实体。

## Gameplay supporting packages

Some gameplay features in DOTS require additional packages to support them. For the list of features that require additional packages, see the table below.

>DOTS 中的一些游戏功能需要额外的包来支持它们。有关需要附加包的功能列表，请参阅下表。

| **Feature**                       | **Packages**                                                 |
| :-------------------------------- | :----------------------------------------------------------- |
| **DOTS ECS**                      | [com.unity.entities](https://docs.unity3d.com/Packages/com.unity.entities@latest) |
| **Rendering**                     | [com.unity.rendering.hybrid](https://docs.unity3d.com/Packages/com.unity.rendering.hybrid@latest) |
| **- Hybrid Renderer V2**          | [com.unity.render-pipelines.high-definition](https://docs.unity3d.com/Packages/com.unity.render-pipelines.high-definition@latest) or [com.unity.render-pipelines.universal](https://docs.unity3d.com/Packages/com.unity.render-pipelines.universal@latest) |
| **- Animation**                   | [com.unity.animation](https://docs.unity3d.com/Packages/com.unity.animation@latest) |
| **Audio**                         | [com.unity.audio.dspgraph](https://docs.unity3d.com/Packages/com.unity.audio.dspgraph@latest) |
| **Physics**                       | [com.unity.physics](https://docs.unity3d.com/Packages/com.unity.physics@latest) or [com.havok.physics](https://docs.unity3d.com/Packages/com.havok.physics@latest) |
| **- Smooth Penetration Recovery** | [com.havok.physics](https://docs.unity3d.com/Packages/com.havok.physics@latest) |
| **- Stable Object Stacking**      | [com.havok.physics](https://docs.unity3d.com/Packages/com.havok.physics@latest) |
| **- Remove Speculative Contacts** | [com.havok.physics](https://docs.unity3d.com/Packages/com.havok.physics@latest) |
| **- Rigidbody Sleeping**          | [com.havok.physics](https://docs.unity3d.com/Packages/com.havok.physics@latest) |
| **- Visual Debugger**             | [com.havok.physics](https://docs.unity3d.com/Packages/com.havok.physics@latest) |
| **Multiplayer**                   | [com.unity.netcode](https://docs.unity3d.com/Packages/com.unity.netcode@latest) |
| **- Lag Compensation**            | [com.unity.physics](https://docs.unity3d.com/Packages/com.unity.physics@latest) |
| **Project Building**              | [com.unity.platforms](https://docs.unity3d.com/Packages/com.unity.platforms@latest) |
| **- Android**                     | [com.unity.platforms.android](https://docs.unity3d.com/Packages/com.unity.platforms.android@latest) |
| **- Linux**                       | [com.unity.platforms.linux](https://docs.unity3d.com/Packages/com.unity.platforms.linux@latest) |
| **- macOS**                       | [com.unity.platforms.macos](https://docs.unity3d.com/Packages/com.unity.platforms.macos@latest) |
| **- Web**                         | [com.unity.platforms.web](https://docs.unity3d.com/Packages/com.unity.platforms.web@latest) |
| **- Windows**                     | [com.unity.platforms.windows](https://docs.unity3d.com/Packages/com.unity.platforms.windows@latest) |
## Authoring overview

You can use the Unity Editor (with the required DOTS packages) to create DOTS-based games. In the Editor, you use GameObjects as normal to author a Scene and the ECS code converts the GameObjects to entities.

>您可以使用 Unity Editor（带有所需的 DOTS 包）来创建基于 DOTS 的游戏。在编辑器中，您可以像往常一样使用游戏对象来创作场景，然后 ECS 代码将游戏对象转换为实体。

The biggest difference when you use DOTS is that instead of writing your own  [MonoBehaviours](https://docs.unity3d.com/ScriptReference/MonoBehaviour.html)  to store instance data and implement custom game logic, you define  [ECS components](https://docs.unity3d.com/Packages/com.unity.entities@0.17/manual/ecs_components.html)  to store the data at runtime, and write  [systems](https://docs.unity3d.com/Packages/com.unity.entities@0.17/manual/ecs_systems.html)  for the custom logic.

>使用 DOTS 的最大区别在于，您无需编写自己的[MonoBehaviours](https://docs.unity3d.com/ScriptReference/MonoBehaviour.html)来存储实例数据和实现自定义游戏逻辑，而是定义[ECS 组件](https://docs.unity3d.com/Packages/com.unity.entities@0.17/manual/ecs_components.html)在运行时存储数据，并为自定义逻辑编写[系统](https://docs.unity3d.com/Packages/com.unity.entities@0.17/manual/ecs_systems.html)。

### GameObject conversion

During GameObject conversion, various conversion systems handle the MonoBehaviour components that they recognize and then convert them into ECS-based components. For example, one of the the Unity.Transforms conversion systems examines the  `UnityEngine.Transform`  component and adds ECS components, such as  [LocalToWorld](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Transforms.LocalToWorld.html), to replace it.

>在 GameObject 转换过程中，各种转换系统处理它们识别的 MonoBehaviour 组件，然后将它们转换为基于 ECS 的组件。例如，Unity.Transforms 转换系统之一会检查该`UnityEngine.Transform`组件并添加 ECS 组件（例如[LocalToWorld](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Transforms.LocalToWorld.html)）来替换它。

You can implement an  [IConvertGameObjectToEntity](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Entities.IConvertGameObjectToEntity.html)  MonoBehaviour component to specify custom conversion steps. There often isn't a one-to-one relationship between the number of GameObjects ECS converts and the number of entities it creates; nor between the number of MonoBehaviours on a GameObject and the number of ECS components it adds to an entity.

>您可以实现[IConvertGameObjectToEntity](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Entities.IConvertGameObjectToEntity.html) MonoBehaviour 组件来指定自定义转换步骤。游戏对象 ECS 转换的数量与其创建的实体数量之间通常没有一一对应的关系；也不在游戏对象上的 MonoBehaviour 数量与其添加到实体的 ECS 组件数量之间。

![](https://docs.unity3d.com/Packages/com.unity.entities@0.17/manual/images/CreatingGameplay.png)

The ECS conversion code converts a GameObject if it either has a  [ConvertToEntity](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Entities.ConvertToEntity.html)  MonoBehaviour component, or if it is part of a SubScene. In either case, the conversion systems provided for various DOTS features, such as Unity.Transforms and Unity.Hybrid.Render, process the GameObject or the Scene Asset and any of their child GameObjects.

>如果游戏对象具有[ConvertToEntity](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Entities.ConvertToEntity.html) MonoBehaviour 组件，或者它是 SubScene 的一部分，则ECS 转换代码会转换游戏对象。在任一情况下，为各种 DOTS 功能（例如 Unity.Transforms 和 Unity.Hybrid.Render）提供的转换系统处理游戏对象或场景资产及其任何子游戏对象。

One difference between converting GameObjects with ConvertToEntity and converting with a SubScene is that ECS serializes and saves to disk the entity data it generates from converting a SubScene. You can load or stream this serialized data very quickly at run time. In contrast, ECS always converts GameObjects with ConvertToEntity MonoBehaviours at runtime.

>使用 ConvertToEntity 转换 GameObjects 和使用 SubScene 转换之间的一个区别是 ECS 序列化它从转换 SubScene 生成的实体数据并将其保存到磁盘。您可以在运行时非常快速地加载或流式传输此序列化数据。相比之下，ECS 总是在运行时使用 ConvertToEntity MonoBehaviours 转换游戏对象。

It's best practice to use standard MonoBehaviours to author, and use  `IConvertGameObjectToEntity`  to apply the values of those authoring components to  [IComponentData](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Entities.IComponentData.html)  structs for runtime use. Often, the most convenient data layout to use to author is not the most efficient data layout at runtime.

>最佳实践是使用标准 MonoBehaviours 进行创作，并将`IConvertGameObjectToEntity`这些创作组件的值应用于[IComponentData](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Entities.IComponentData.html)结构以供运行时使用。通常，用于创作的最方便的数据布局并不是运行时最有效的数据布局。

You can use  [IConvertGameObjectToEntity](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Entities.IConvertGameObjectToEntity.html)  to customize the conversion of any GameObject in a SubScene, or a GameObject that has a  `ConvertToEntity`  MonoBehaviour, or one that is a child of a GameObject that has a  `ConvertToEntity`  MonoBehaviour.

##### NOTE

The authoring workflow for DOTS-based applications is an area of active development. The general outlines are in place, but you should anticipate many changes in this area in the near future.

>##### 注意
>
>基于 DOTS 的应用程序的创作工作流程是一个活跃的开发领域。大纲已经到位，但您应该预计在不久的将来该领域会发生许多变化。

## Generated authoring components

Unity can automatically generate authoring components for simple runtime ECS components. When Unity generates an authoring component, you can add an add the script containing the ECS component directly to a GameObject within the Editor. You can then use the  **Inspector**  window to set the initial values for the component.

》

### For IComponentData

Unity can automatically generate authoring components for simple  [IComponentData](https://docs.unity3d.com/Packages/com.unity.entities@0.17/api/Unity.Entities.IComponentData.html)  components. When Unity generates an authoring component, you can add an  `IComponentData`  directly to a GameObject in a Scene within the Unity Editor. You can then use the  **Inspector**  window to set the initial values for the component.

To indicate that you want to generate an authoring component, add the  `[GenerateAuthoringComponent]`  attribute to the IComponentData declaration. Unity automatically generates a MonoBehaviour class that contains the public fields of the component and provides a Conversion method that converts those fields over into runtime component data.

```
[GenerateAuthoringComponent]
public struct RotationSpeed_ForEach : IComponentData
{
    public float RadiansPerSecond;
}

```

Note the following restrictions:

-   Only one component in a single C# file can have a generated authoring component, and the C# file must not have another MonoBehaviour in it.
-   ECS only reflects public fields and they have the same name as that specified in the component.
-   ECS reflects fields of an Entity type in the IComponentData as fields of GameObject types in the MonoBehaviour it generates. ECS converts the GameObjects or Prefabs you assign to these fields as referenced Prefabs.
-   Only public fields are reflected and they will have the same name as that specified in the component.
-   Fields of an Entity type in the IComponentData are reflected as fields of GameObject types in the generated MonoBehaviour. GameObjects or Prefabs you assign to these fields are converted as referenced prefabs.

### For IBufferElementData

You can also generate authoring components for types that implement  `IBufferElementData`  by adding the  `[GenerateAuthoringComponent]`  attribute:

```
[GenerateAuthoringComponent]
public struct IntBufferElement: IBufferElementData
{
    public int Value;
}

```

In this example, a class named  `IntBufferElementAuthoring`  (which inherits from  `MonoBehaviour`) is generated, exposing a public field of  `List<int>`  type. During conversion, this list will be converted into  `DynamicBuffer<IntBufferElement>`, and then added to the converted entity.

Note the following restrictions:

-   Only one component in a single C# file can have a generated authoring component, and the C# file must not have another MonoBehaviour in it.
-   `IBufferElementData`  authoring components cannot be automatically generated for types that contain 2 or more fields.
-   `IBufferElementData`  authoring components cannot be automatically generated for types that have an explicit layout.
