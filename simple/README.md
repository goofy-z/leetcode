### 二分法

给定一个顺序，且元素不重复int数组 list，和查找值 target，返回target所在list的索引

- 回溯二分法

  ```go
  func binarySearch(list []int, target int)int{
  	realIndex := 0  // 就数组相比新数组的index偏移量
  	tmpList := list
  	for{
  		bIndex := 0
  		eIndex := len(tmpList) - 1
  		if len(tmpList) < 1{
  			return -1
  		}
  		mid := len(tmpList)/2
  		if tmpList[mid] == target{
  			return mid + realIndex
  		}
  		if list[mid] < target{
  			bIndex = mid
  			realIndex += mid  // bIndex发生改变就改变一次偏移量
  		} else{
  			bIndex = 0
  			eIndex = mid
  		}
  		tmpList = tmpList[bIndex:eIndex+1]
  	}
  }
  ```

  

  