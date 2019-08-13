### 罗马数转整数

使用map

```js
var romanToInt = function(s) {
        let map = {
            CM: 900,
            M: 1000,
            CD: 400,
            D: 500,
            XC: 90,
            C: 100,
            XL: 40,
            L: 50,
            IX: 9,
            X:  10,
            IV: 4,
            V: 5,
            I: 1
        }
        let sum = 0
    
        for(let i = 0 ;i< s.length;i++){
            let key = s.substring(i,i+2);
            if(map[key]){
                sum += map[key]
                i +=1;
            }else{
                sum += map[s[i]]
            }
        }
        return sum
};
// 240ms
```

### 报数

```js
var countAndSay = function(n) {
    let num = '1';
    let result = '';
    let temp = 1;
    for(let i = 0;i<n-1;i++){
        for(j=0;j<num.length;j++){
            if(num[j]!==num[j+1] || j == num.length){
                result += temp + num[j]
                temp = 1
            }else{
                temp+=1;
            }
        }
        //每次完成后，num赋值为新的报数，重新报下一组数
        num = result;
        result =''
    }
    return num
};
```

### 搜索插入位置

给定有序数组，搜索返回插入位置（有序数组，可用二分查找）

```js

/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
var searchInsert = function(nums, target) {
    return binarySearch(nums,0,nums.length-1,target)
};

var binarySearch = function(arr,start,end,target){
    if(start > end) return start

    let binary = Math.floor((start+end)/2);

    if(arr[binary] == target) return binary

    if(arr[binary]>target){
        return binarySearch(arr,start,binary -1 ,target)
    }else{
        return binarySearch(arr,binary + 1,end,target)
    }
}
```

### 最大子序和

给定一个整数数组 `nums` ，找到一个具有最大和的连续子数组（子数组最少包含一个元素），返回其最大和。

```js
var maxSubArray = function(nums) { 
    let ans = nums[0]
    let sum = 0;
    for(let i = 0;i<nums.length;i++){
        //如果上次的和为正增益，那么加上，否则重置为本次数值
        if(sum>0){
            sum+=nums[i]
        }else{
            sum = nums[i];
        }
        ans = Math.max(ans,sum)
    }
    return ans
};
//104ms
```

```js
var maxSubArray = function(nums) { 
    let pre = 0;
    //给定最大负数
    let max = -Number.MAX_VALUE;
    for(var i = 0 ;i<nums.length;i++){
        // 比较序列和与当前值的大小，选择大的为下一个参数
        pre = Math.max(pre + nums[i],nums[i])
        // 比较当前值与暂存最大值大小
        max = Math.max(max,pre)
    }
    return max
};
//84ms
```

### 最后单词的长度

```js
var lengthOfLastWord = function(s) {
    return s.trim().split(' ').pop().length;
};
```

### 加1

```js
var plusOne = function(digits) {
    let len = digits.length;
    if(digits[len-1] === 9){
        while(digits[len-1] === 9){
            digits[len-1] = 0
            len -=1
        }
        len==0?digits.unshift(1):digits[len - 1] +=1
        return digits
    }
    digits[len-1]+=1
    return digits
};
//64ms
```

### 二进制求和

对于从末尾进一有关的，可以反转后进行遍历叠加

```js
var addBinary = function(a, b) {
    a = a.split("").reverse().join("")
    b = b.split("").reverse().join("")

    let len = a.length>b.length?a.length:b.length
    let res = []
    //暂存进位
    let temp = 0;
    for(let i = 0;i<len;i++ ){
        tem1 = Number(a[i]||0);
        tem2 = Number(b[i]||0);
        let sum = tem1 + tem2 +temp 
        if(sum > 1){
            //取余
            res[i] = sum -2;
            temp = 1;
        }else{
            res[i] = sum
            temp = 0
        }
     }
    	//最后还有进位，添加一位
     if(temp === 1){res.push(1)}
     return res.reverse().join("")

};
//104
```

### x平方根

二分法

```js
var mySqrt = function(x) {
    var left = 1;
    var right = Math.floor(x/2) +1;
    var mid;
    while(left <= right){
        mid = Math.floor((left+right)/2);
        if(mid*mid >x){
            right = mid -1
        }else if(mid* mid <x){
            left = mid +1
        }else{
            return mid
        }
    }
    return right
};
```

### 斐波那契数列

递归容易爆栈，利用数组

```js
var climbStairs = function(n) {
    if(n === 1) return 1
    if(n === 2) return 2
    
    var arr= [1,2]
    for(let i = 2;i<n;i++){
        arr[i] = arr[i-1]+arr[i-2]
    }
    
    return arr[n-1]
};
```

### 83 删除有序列表重复项

```js
/*
 * @lc app=leetcode.cn id=83 lang=javascript
 *
 * [83] 删除排序链表中的重复元素
 */
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} head
 * @return {ListNode}
 */
var deleteDuplicates = function(head) {
    //获取修改当前指针
    var cur = head
    while(cur){
        if( cur.next !== null && cur.val == cur.next.val){
            cur.next = cur.next.next
        }else{
            cur = cur.next
        }
    }
    return head;

};



```

