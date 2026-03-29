---
tags:
  - binary-search
  - partition
  - quickselect
---
# Binary search
**Remember**:  condition `l <= r` -> l = m+1 , r = m-1 
- search space in [l, r]
- left will be in range [0 -> n] -> need to check `left == len(arr)` at the end
- right will be in range [-1, n] -> need to check `right == -1` at the end

```go
At the end
l = 0
r = -1
=> target is smaller than all elements

l = n
r = n-1
=> target is greater than all elements

SUMMARY
l: [0, n]: first element that >= target
r: [-1, n-1]: last element that <= target

DUPLICATED: first/last duplicated value
You also need to verify 
nums[l] == target
nums[r] == target 
since the value might not exist in the array.

// First duplicate (leftmost target)
func find(nums []int, l, r, target int) int {
	for l <= r {
		m := (l + r) / 2
		if nums[m] < target {
			l = m + 1
		} else {
			r = m - 1
		}
	}
	
	l = first duplicated index
	scope: [0, n]
	nums[l] >= target  (l may == n)
	need to check l == n
}

// Last duplicate (rightmost target)
func find(nums []int, l, r, target int) int {
	for l <= r {
		m := (l + r) / 2
		if nums[m] <= target {
			l = m + 1
		} else {
			r = m - 1
		}
	}
	
	r = last duplicated index 
	scope: [-1, n-1]
	nums[r] <= target  (r may == -1)
	need to check: r >= 0
}
```


# QuickSelect - topK - partition
- leetcode.com/problems/k-closest-points-to-origin
- partition() return index where all values on the left index ≤ arr[index]

```go
Example:
3 1 4 6 8

partition(l, r) 
partition(0, 4)
	choose pivot at midde: pivot=2
	store = l (the index at the end stays in the right position)
	swap pivot and last item
	
	for i:=l -> r:
		if arr[i] < arr[pivot] -> i is in correct position
			swap(i, store)
			store++
	swap(store, lastItem)
	return store
	
index = partition(0,4)
=> index = 2 -> arr becomes [3 1 4 6 8]
	at index 2 (4) arr[:2] is top 3 correct
	so if we want to find top 3 we can return at there
	
the partition func take O(n)
when combine with l,r we can reduce n to n/2 to make O(n) total n + n/2+n/4...
```

For quicksort it uses Quick Select to find the index and after that sort in sub array
```go
func quicksort(l, r)
    if l >= r return
    index = partition(l, r)
    quicksort(arr, l, index-1)   // sort LEFT
    quicksort(arr, index+1, r)   // sort RIGHT
```

# Bit - chẵn,lẻ
1 số integer đc tạo thành từ việc chia 2 (is built from repeatedly divide by 2)
Example:
13 in binary = 1101 = (2^3 + 2^2 + 0*2^1) + 2^0
- bit cuối cùng thể hiện số đó là chẵn hay lẻ vì nó (2^0) là +1 hoặc +0
    - Mod: 13%2 = 1 → lẻ ⇒ mình sẽ biết đc bit cuối cùng
- How to [count 1’s](https://leetcode.com/problems/counting-bits)
    - chia 2 đến hết , mỗi lần %2 mình sẽ biết đc bit cuối cùng là 1 hay 0
        - 13%2 → last bit = 1 ⇒ 13/2 = 6 = 2^3
        - 6%2 → last bit = 0 ⇒ 6/2 = 3 = 2^2
        - 3%2 → last bit = 1 ⇒3/2= 1 = 2^0
        - 1%2 → last bit = 1 ⇒ 1/2=0
        - ⇒ tổng có 3 bit 1