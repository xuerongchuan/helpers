
### 数据表格式
#### 用户表（userTable）
字段名|字段含义
-|--
id|用户id，自增，数值型，唯一标识


#### 项目表（workTable）
字段名|字段含义
-|-
id|自增，唯一标识
userId|用户id
parameter|参数字典
**parameter**：{'algoId'：<算法id>,'<参数名>'：<参数值>···}
#### 算法表（algoTable）
字段名|字段含义
-|-
id|算法id，自增，唯一标识
parameter|默认参数字典
type|算法类型




> Written with [StackEdit](https://stackedit.io/).
<!--stackedit_data:
eyJoaXN0b3J5IjpbNjI5MDU4NzI1LDE3ODI4ODc0NzhdfQ==
-->