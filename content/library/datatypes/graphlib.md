+++
title = "graphlib --- 操作类似图的结构的功能"
date = 2024-11-15T11:18:41+08:00
weight = 140
type = "docs"
description = ""
isCJKLanguage = true
draft = false
+++

> 原文: [https://docs.python.org/zh-cn/3.13/library/graphlib.html](https://docs.python.org/zh-cn/3.13/library/graphlib.html)
>
> 收录该文档的时间：`2024-11-15T11:18:41+08:00`

# `graphlib` --- 操作类似图的结构的功能

**源代码:** [Lib/graphlib.py](https://github.com/python/cpython/tree/3.13/Lib/graphlib.py)

------

## *class* graphlib.**TopologicalSorter**(*graph=None*)

​	提供以拓扑方式对由 [hashable](https://docs.python.org/zh-cn/3.13/glossary.html#term-hashable) 节点组成的图进行排序的功能。

​	拓扑排序是指图中顶点的线性排序，使得对于每条从顶点 u 到顶点 v 的有向边 u -> v，顶点 u 都排在顶点 v 之前。 例如，图的顶点可以代表要执行的任务，而边代表某一个任务必须在另一个任务之前执行的约束条件；在这个例子中，拓扑排序只是任务的有效序列。 完全拓扑排序 当且仅当图不包含有向环，也就是说为有向无环图时，完全拓扑排序才是可能的。

​	如果提供了可选的 *graph* 参数则它必须为一个表示有向无环图的字典，其中的键为节点而值为包含图中该节点的所有上级节点（即具有指向键中的值的边的节点）的可迭代对象。 额外的节点可以使用 [`add()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.add) 方法添加到图中。

​	在通常情况下，对给定的图执行排序所需的步骤如下:

- 通过可选的初始图创建一个 [`TopologicalSorter`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter) 的实例。
- 添加额外的节点到图中。
- 在图上调用 [`prepare()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.prepare)。
- 当 [`is_active()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.is_active) 为 `True` 时，迭代 [`get_ready()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.get_ready) 所返回的节点并加以处理。 完成处理后在每个节点上调用 [`done()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.done)。

​	在只需要对图中的节点进行立即排序并且不涉及并行性的情况下，可以直接使用便捷方法 [`TopologicalSorter.static_order()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.static_order):



``` python
>>> graph = {"D": {"B", "C"}, "C": {"A"}, "B": {"A"}}
>>> ts = TopologicalSorter(graph)
>>> tuple(ts.static_order())
('A', 'C', 'B', 'D')
```

​	这个类被设计用来在节点就绪时方便地支持对其并行处理。 例如:

```
topological_sorter = TopologicalSorter()

# 添加节点到 'topological_sorter'...

topological_sorter.prepare()
while topological_sorter.is_active():
    for node in topological_sorter.get_ready():
        # 工作线程或进程接受节点在
        # 'task_queue' 队列上工作和移除
        task_queue.put(node)

    # 当一个节点的工作完成时，工作线程或进程就会将节点放入
    # 'finalized_tasks_queue' 这样我们就可以继续操作其他节点。
    # 'is_active()' 的定义能够确保这一点，这时，至少有一个节点
    # 已被放置在 'task_queue' 上而尚未被传给 'done()'，因此这个
    # 阻塞中的 'get()' 必须（最终）成功。 在调用 'done()' 之后，
    # 我们将环回至再次调用 'get_ready()'，因此只要逻辑上可能
    # 就应尽快将新近可用的节点放入 'task_queue' 中。
    node = finalized_tasks_queue.get()
    topological_sorter.done(node)
```

## **add**(*node*, **predecessors*)

​	将一个新节点及其上级节点添加到图中。 *node* 和 *predecessors* 中的所有元素都必须是 [hashable](https://docs.python.org/zh-cn/3.13/glossary.html#term-hashable)。

​	如果附带相同的节点参数多次调用，则依赖项的集合将为所有被传入依赖项的并集。

​	可以添加不带依赖项的节点 (即不提供 *predecessors*) 或者重复提供依赖项。 如果有先前未提供的节点包含在 *predecessors* 中则它将被自动添加到图中并且不带自己的上级节点。

​	如果在 [`prepare()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.prepare) 之后被调用则会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

## **prepare**()

​	将图标记为已完成并检查图中是否存在环。 如何检测到任何环，则将引发 [`CycleError`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.CycleError)，但 [`get_ready()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.get_ready) 仍可被用来获取尽可能多的节点直到环阻塞了操作过程。 在调用此函数后，图将无法再修改，因此不能再使用 [`add()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.add) 添加更多的节点。

## **is_active**()

​	如果可以取得更多进展则返回 `True`，否则返回 `False`。 如果环没有阻塞操作，并且还存在尚未被 [`TopologicalSorter.get_ready()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.get_ready) 返回的已就绪节点或者已标记为 [`TopologicalSorter.done()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.done) 的节点数量少于已被 [`TopologicalSorter.get_ready()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.get_ready) 所返回的节点数量则还可以取得进展。

​	该类的 [`__bool__()`](https://docs.python.org/zh-cn/3.13/reference/datamodel.html#object.__bool__) 方法要使用此函数，因此除了:

```
if ts.is_active():
    ...
```

​	可能会简单地执行:

```
if ts:
    ...
```

​	如果之前未调用 [`prepare()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.prepare) 就调用此函数则会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

## **done**(**nodes*)

​	将 [`TopologicalSorter.get_ready()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.get_ready) 所返回的节点集合标记为已处理，解除对 *nodes* 中每个节点的后续节点的阻塞以便在将来通过对 [`TopologicalSorter.get_ready()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.get_ready) 的调用来返回它们。

​	如果 *nodes* 中的任何节点已经被之前对该方法的调用标记为已处理或者如果未通过使用 [`TopologicalSorter.add()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.add) 将一个节点添加到图中，如果未调用 [`prepare()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.prepare) 即调用此方法或者如果节点尚未被 [`get_ready()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.get_ready) 所返回则将引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

## **get_ready**()

​	返回由所有已就绪节点组成的 `tuple`。 初始状态下它将返回所有不带上级节点的节点，并且一旦通过调用 [`TopologicalSorter.done()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.done) 将它们标记为已处理，之后的调用将返回所有上级节点已被处理的新节点。 一旦无法再取得进展，则会返回空元组。

​	如果之前未调用 [`prepare()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.prepare) 就调用此函数则会引发 [`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError)。

## **static_order**()

​	返回一个迭代器，它将按照拓扑顺序来迭代所有节点。 当使用此方法时，[`prepare()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.prepare) 和 [`done()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.done) 不应被调用。 此方法等价于:

```
def static_order(self):
    self.prepare()
    while self.is_active():
        node_group = self.get_ready()
        yield from node_group
        self.done(*node_group)
```

​	所返回的特定顺序可能取决于条目被插入图中的顺序。 例如:



``` python
>>> ts = TopologicalSorter()
>>> ts.add(3, 2, 1)
>>> ts.add(1, 0)
>>> print([*ts.static_order()])
[2, 0, 1, 3]

>>> ts2 = TopologicalSorter()
>>> ts2.add(1, 0)
>>> ts2.add(3, 2, 1)
>>> print([*ts2.static_order()])
[0, 2, 1, 3]
```

​	这是由于实际上 "0" 和 "2" 在图中的级别相同（它们将在对 [`get_ready()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.get_ready) 的同一次调用中被返回） 并且它们之间的顺序是由插入顺序决定的。

​	如果检测到任何环，则将引发 [`CycleError`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.CycleError)。

> Added in version 3.9.
>

## 异常

[`graphlib`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#module-graphlib) 模块定义了以下异常类:

## *exception* graphlib.**CycleError**

[`ValueError`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#ValueError) 的子类，当特定的图中存在环时将由 [`TopologicalSorter.prepare()`](https://docs.python.org/zh-cn/3.13/library/graphlib.html#graphlib.TopologicalSorter.prepare) 引发。 如果存在多个环，则将只报告其中一个未定义的选项并将其包括在异常中。

​	检测到的环可通过异常实例的 [`args`](https://docs.python.org/zh-cn/3.13/library/exceptions.html#BaseException.args) 属性的第二个元素访问，它由一个节点列表组成，在图中每个节点都是列表中下一个节点的直接上级节点。 在报告的列表中，开头和末尾的节点将是同一对象，以表明它是一个环。
