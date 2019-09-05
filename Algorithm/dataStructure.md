## 二叉树

#### 有序数组转平衡二叉树（递归）

```js
function TreeNode(val) {
    this.value = val;
  }
  var sortedArrayToBST = function (nums) {
    if (nums.length === 0) {
      return null;
    }
    if (nums.length === 1) {
      return new TreeNode(nums[0]);
    }

    //中间项分割，左边小，右边大
    var mid = parseInt(nums.length/2)
    var left = nums.slice(0,mid);
    var right = nums.slice(mid+1,nums.length);

    let root = new TreeNode(nums[mid]);
    root.left = sortedArrayToBST(left);
    root.right = sortedArrayToBST(right)
    return root
  }

  let arr = [-7,-5,-3,0,3,5,7]
  let ss = sortedArrayToBST(arr)

```

#### 判断相同的数

```js
var isSameTree = function(p, q) {
    // pq值都没有，则为最后一层
    if(!p && !q) return true;
	// 只有p或q一方没有值，则必不等，同时可以去掉为undefined时的错误。
    if(!p || !q || p.val !== q.val) return false;
	//递归比较左右子树
    return isSameTree(p.left,q.left) && isSameTree(p.right,q.right)
	//不能直接使用p.left === q.left或(p.right===q.right)来比较，其左右子树为节点对象类型，返回为false
};
```



