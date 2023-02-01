# CRD

为了实现快照相关功能，ACK通过CRD定义了以下3个相关的资源类型。
| 资源类型名称          | 描述                                                                       |
| --------------------- | -------------------------------------------------------------------------- |
| VolumeSnapshotContent | 存储后端的快照实例，由系统管理员创建维护，无NameSpace。类似PV概念。        |
| VolumeSnapshot        | 声明一个快照实例，由用户创建维护，属于特定NameSpace。类似PVC概念。         |
| VolumeSnapshotClass   | 定义一个快照类，描述创建快照使用的参数、Controller。类似StorageClass概念。 |

# 创建 VolumeSnapshot

## 从 PVC

需要先创建 VSClass
在清单中指定  VSClass 并在 source 字段指定 PVC 

## 从云端现有 Spanshot （从 SnapshotContent ）

先创建 VSContent （ source 字段指定 id 等）
创建 VS ，指定 source 为 VSContent 名

# 创建 PVC

在 PVC 的 dataSource 字段中指定 VS 与 VS 名

