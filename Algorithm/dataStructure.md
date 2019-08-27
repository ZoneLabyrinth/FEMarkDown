## 二叉树

有序数组转平衡二叉树（递归）

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

