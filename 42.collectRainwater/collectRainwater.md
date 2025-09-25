
### 介绍
给定n个非负整数表示每个宽度为1的柱子的高度图，计算按此排列的柱子，下雨之后能接多少雨水。
     ![[Pasted image 20250921213248.png]]
#### 1.用空间换时间（按列计算）
遍历每一列，我们使用两个数组iLeftMax，jRightMax来存储当前列左右两边最高的位置。

对于每一个柱子接的水，那么它能接的水=min(左右两边最高柱子）-当前柱子高度。同样的，两根柱子要一起求接水，需知道它们左右两边最大值的较小值。

假设两柱子分别为 i，j。那么就有iLeftMax,iRightMax,jLeftMx,jRightMax 这个变量。由于 j>i ，故 jLeftMax>=iLeftMax，iRigthMax>=jRightMax。

那么，如果 iLeftMax>jRightMax，则必有 jLeftMax >= jRightMax，所有我们能接 j 点的水。

如果 jRightMax>iLeftMax，则必有 iRightMax >= iLeftMax，所以我们能接 i 点的水。

而我们实际只需用到 iLeftMax，jRightMax 两个变量，故我们维护这两个即可。
```python 
    class Solution(object):
       def trap(self, height):
        """
        :type height: List[int]
        :rtype: int
        """
        left = 0
        right = 0
        i = 1
        count = 0
        num = len(height)
        left_max = [0] * num
        right_max = [0] * num
        #找左边最高
        for j in range(1,num):
            left_max[j] = max(left_max[j-1],height[j-1])
        #找右边最高
        for j in range(2,num):
            right_max[num-j] = max(right_max[num-j+1],height[num-j+1])
        #求每一列能接的水
        while i < num:
            if left_max[i] > height[i] and right_max[i] > height[i]:
                if left_max[i] <= right_max[i]:
                    count = count + (left_max[i] - height[i])
                if right_max[i] < left_max[i]:
                    count = count + (right_max[i] - height[i])
            i = i + 1
        return count
    ```
#### 2.单调栈

类似与括号匹配，找互相匹配的柱子。第一根柱子进栈，i后移，比较height[i]与栈顶的大小。如果height[i]<height[栈顶],柱子i进栈；如果height[i]>=height[栈顶],那么说明该地方能接到雨水，栈顶（存到雨水的地方）出栈。==要注意可以多次出栈的场景，需要使用内循环==。

```python
class Solution(object):
    def trap(self, height):
        """
        :type height: List[int]
        :rtype: int
        """
        stack = list()
        water = 0
        #i是当前位置
        for i in range(len(height)):
            while stack and height[i] >= height[stack[-1]]:
                peek = stack.pop()
                if not stack:
                    break
                d = i - stack[-1] - 1
                if height[i] > height[stack[-1]]:
                    water = water + d * (height[stack[-1]] - height[peek])
                else:
                    water = water + d * (height[i] - height[peek])
            stack.append(i)
        return water
	    
```