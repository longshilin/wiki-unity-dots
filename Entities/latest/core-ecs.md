# ECS 概念
实体组件系统（ECS）架构将身份（实体），数据（组件）和行为（系统）分开。该体系结构专注于数据。系统读取组件数据流，然后将数据从输入状态转换为输出状态，然后对这些实体进行索引。

下图说明了这三个基本部分如何协同工作：
![](https://cdn.jsdelivr.net/gh/longshilin/images/20201229131912.png)

In this diagram, a system reads  `Translation`  and  `Rotation`  components, multiplies them and then updates the corresponding  `LocalToWorld`  components (`L2W = T*R`).

The fact that entities A and B have a  `Renderer`  component and entity C does not, doesn't affect the system, because the system does not care about  `Renderer`  components.

You can set up a system so that it requires a  `Renderer`  component, in which case, the system ignores the components of entity C; or, alternately, you can set up a system to exclude entities with  `Renderer`  components, which then ignores the components of entities A and B.
