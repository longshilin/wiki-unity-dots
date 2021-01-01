# HelloCube：ForEach

该示例演示了一个简单的ECS系统，该系统可旋转一对多维数据集。

## [](https://github.com/Unity-Technologies/EntityComponentSystemSamples/blob/master/ECSSamples/Assets/HelloCube/1.%20ForEach/README.md#what-does-it-show)它显示什么？

该示例演示了ECS中数据和功能的分离。数据存储在组件中，而功能则写入系统中。

该**RotationSpeedSystem_ForEach**  _更新_使用对象的旋转_数据_存储在**RotationSpeed_ForEach**组件。

## [](https://github.com/Unity-Technologies/EntityComponentSystemSamples/blob/master/ECSSamples/Assets/HelloCube/1.%20ForEach/README.md#systems-and-entitiesforeach)系统和实体

RotationSpeedSystem_ForEach是一个从SystemBase派生并使用Entities.ForEach lambda遍历Entities的系统。此示例仅创建一个实体，但是如果您向场景中添加了更多实体，则RotationSpeedSystem_ForEach会全部更新它们-只要它们具有RotationSpeed_ForEach组件（以及将GameObject的Transform转换为ECS组件时添加的Rotation组件）。

请注意，如果有多个块，则使用Entities.ForEach的系统将使用ScheduleParallel安排lambda在多个工作线程上运行。如果多个核心可用（并且您的数据跨越多个块），这将自动利用多个核心。

## [](https://github.com/Unity-Technologies/EntityComponentSystemSamples/blob/master/ECSSamples/Assets/HelloCube/1.%20ForEach/README.md#converting-from-gameobject-to-entity)从GameObject转换为Entity

本示例使用自动生成的创作组件。此MonoBehaviour通过IComponentData上的**GenerateAuthoringComponent**属性创建，并公开该类型的所有公共字段进行创作。有关如何编写自己的创作组件的示例，请参见**IJobChunk**示例。
<!--stackedit_data:
eyJoaXN0b3J5IjpbNDY4MzM4NTYwXX0=
-->