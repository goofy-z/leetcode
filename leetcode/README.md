### 124. Binary Tree Maximum Path Sum

给一个不为空的二叉树，找出一条路径，这个路径可以从树中的任意一个节点开始，必须包含一个节点且不需要一定经过root节点，使这条路径的的值加起来最大，并返回这个最大值。

```
Input: [-10,9,20,null,null,15,7]

   -10
   / \
  9  20
    /  \
   15   7

Output: 42(路径为15 20 7)  
```

- 思路

  首先根据题意这个最大路径一定是包含至少一个节点，那我们对于任意一个节点有这五种情况

  1. 不包含任何分支，就只有节点A，最大值就是A节点的value。
  2. 包含了节点A的左节点，那么最大值就一定是 A.value + A.leftChild.value + X。这里的X指的是包含A.leftChild这个节点的路径最大值，同样A的左节点也包含这四种情况，这就是一个递归过程。
  3. 包含了节点A的右节点，同左节点也是一个递归求最大值的过程。
  4. 同时包含左右节点和顶点。
  5. 这条最大路径不包含节点A，是它的两个子节点中最大的一个，那么就有A的值要小于左右两个节点递归的最大值。

  然后我们需要的就是计算这五种情况的最大值，然后我们记录这个最大值。需要注意的是，因为作为一个条最大路径，A的左节点的最大值，不能是其左节点+其两个分支的value之和，只能为两个分支的最大值+左节点的值

- 迭代解法

  ```go
  func maxPathSum(root *TreeNode) int {
    res := -9223372036854775808 //定义当前的res为最小值
  	dsf(root, &res)
  	return res
  }
  
  func dsf(node *TreeNode, res *int)int{
  	if node == nil{
  		return 0
  	}
  	left := dsf(node.Left, res)
  	right := dsf(node.Right, res)
  	maxRootWithChild := Max(Max(left, right) + node.Val, node.Val)
  	maxAll := Max(left + right + node.Val, maxRootWithChild)
  	*res = Max(maxAll, *res)
  	return maxRootWithChild
  }
  
  func Max(a, b int)int{
  	if a>b {
  		return a
  	}
  	return b
  }
  ```



### 146. LRU Cache

实现一个LRU算法，要求GET方法的时间复杂度为O(1)

- 思路

  LRU就是最近最少使用的意思，每次使用GET获取一次某个key，则这个key的优先度就需要被提前，当容量超出最大值时，自动删除优先级最低的key，可以使用一个双向链表来保存每一个key。要实现复杂度为O(1),则可以使用map存每一个key的value，

  在golang的标准库里有一个实现了双向链表的库--list

  在每次GET的时候我们应该在链表里调动元素的位置

  每次Set先判断是否存在该key，不存在我们判断容量来删除最后一个元素并在表头添加一个元素，存在我们就调动元素到表头并设定值

  ```go
  type LRUCache struct {
  	items map[int]*list.Element
  	l *list.List
  	capacity int
  }
  
  type entry struct {
  	key int
  	value int
  }
  
  func Constructor(capacity int) LRUCache {
    return LRUCache{
  		items: make(map[int]*list.Element),
  		l: &list.List{},
  		capacity: capacity,
  	}
  }
  
  
  func (l *LRUCache) Get(key int) int {
    if v,ok := l.items[key];ok{
          l.l.MoveToFront(v)
  		return v.Value.(*entry).value
  	}
  	return -1
  }
  
  
  func (l *LRUCache) Put(key int, value int)  {
    if v,ok := l.items[key];ok{
      v.Value.(*entry).value = value
  		l.l.MoveToFront(v)
  		return
  	}
  	if len(l.items) == l.capacity{
  		v := l.l.Back()
  		l.l.Remove(v)
          delete(l.items, v.Value.(*entry).key)
  	}
  	newE := l.l.PushFront(&entry{key, value})
  	l.items[key] = newE
  	return
  }
  ```

  