# MDSplus python 接口的用法
> 设置环境变量
``` 
$export east_path=202.127.204.12::
```
## 获取节点的一些方法
> 打开指定的树，如50000炮
```
>>> tree = ('EAST',50000)
```
> 获得树下相应的节点，如TOP节点
```
>>> top = tree.getNode('\\TOP')
```
> 获得节点的属性
```
>>> top_name = top.getNodeName()
>>> top_name = top.getLength()
>>> top_name = top.getData()
>>> top_name = top.getDtype()
>>> top_name = top.getPath()
>>> top_name = top.isOn()
```
> 获得父节点
```
>>> top_member = top.getMember()
>>> top = top_member.getParent()
```
> 获得子节下的成员节点
```
>>> top_members = top.getMembers()
```
> 获得子节点
```
>>> top_childs = top.getChildren()
```
> 进入相应的节点,如进入top节点
```
>>> tree.setDefaults(top)
```
> 获取某节点底下的所有节点
```
>>> top.getNodeWild('***')
```
## 获取节点数据的方法
> 获得相应节点的数据
```
>>> top.getData()
```
> 给相应节点赋值
```
>>> d = top.getData()
>>> top.putData(d)
>>> top.putData(Float64(3.14))
>>> top.putData(Int32Array([1, 2, 3, 4]))
```
> 获取原始数据
```
>>> native_data = top.getData().data()
```
> 使用表达式
```
>>> top.putData(Function('add', (2, 1)))
>>> top.putData(tree.tdlCompile('2 + top'))
>>> top.putData(tree.tdiCompile("BUILD_SIGNAL(($VALUE + 5) * 3, $1, $2)", rawData, dimData))
```
## 编辑树
> 增加节点、删除节点、增加tag、删除tag
```
>>> tree = Tree('east',-1,'new')
>>> tree.addNode(':NUM1',"NUMERIC") # 类型可以为NUMERIC、TEXT、STRUCTURE
>>> tree.deleteNode('NUM1')
>>> tree.renameNode('NUM1', 'NUM2')
>>> top.addTag("TAG1")
>>> top.removeTag("TAG1")
>>> tree.write()
```
## Segment
> 使用Segment
```
>>> start = Float64(10.)
>>> end = Float64(11.)
>>> dim = Range(start, end, Float64(1E-3))
>>> top.beginSegment(start, end, dim, Float32Array(data))
```
> 填充Segment
```
>>> top.putSegment(Float32Array(currData),-1)
```
> 使用时间戳Segment
```
top.putRow(10000, Float32(currVal), Int64(i))
```
## 参考
[MDSplus](http://www.mdsplus.org)
