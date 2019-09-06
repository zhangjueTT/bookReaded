# 剑指offer第二版总结——基于牛客网


### 1. 在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

	public class Solution {
	    public boolean Find(int target, int [][] array) {
			
			int row = array.length;	
			int col = array[0].length;
			// 从数组的左下角（或者右上角开始判断）
			int i = row-1, j = 0;
			while( i>=0 && j< col)
			{			
				if( target == array[i][j]) {
					return true;
				}
				if( target > array[i][j] ){
					j++;
				}
				else {
					i--;
				}
			}
			return false;
		}
	}

注解：解题的关键在于找到排好序的规则，左下角和右上角的位置数正好处于一个可以通过大小进行判断上下走向。

### 2. 请实现一个函数，将一个字符串中的每个空格替换成“%20”。例如，当字符串为We Are Happy.则经过替换之后的字符串为We%20Are%20Happy。

	public class Solution {
	    public String replaceSpace(StringBuffer str) {
	    		if(str.length() == 0 )  return "";		 
			// 找出被替换字符的数目，java可以省略这一步。
			 int numOfEmpty = 0;
			 for(int i =0; i<str.length(); i++ ) {
				 if( str.charAt(i) == ' ') numOfEmpty++;
			 }
			// 若是C++可以根据numOfEmpty开辟新空间，而java可以直接使用StringBuffer
			 StringBuffer outStr = new StringBuffer();
			 for(int i = 0; i<str.length(); i++ ) {
				 if( str.charAt(i) == ' ') outStr.append("%20");
				 else  outStr.append(str.charAt(i));	 
			 }
			 return outStr.toString(); 
	    }
	}

### 3. 输入一个链表，按链表值从尾到头的顺序返回一个ArrayList。
	/**
	*    public class ListNode {
	*        int val;
	*        ListNode next = null;
	*
	*        ListNode(int val) {
	*            this.val = val;
	*        }
	*    }
	*/

	import java.util.Stack;
	import java.util.ArrayList;
	public class Solution {
	    public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
	    	ArrayList<Integer> arrayList= new ArrayList<>();
	    	Stack<Integer> stack = new Stack<>();
	    	
	        if(listNode == null) return arrayList;
	        // 遍历链表存入stack中
	    	while( listNode.next != null )
	    	{
	    		stack.push(listNode.val);   
	    		listNode = listNode.next;
	    	}
	    	stack.push(listNode.val);
	    	// 将stack中的数据放入ArrayList中
	    	while( !stack.isEmpty() ) {
	        	arrayList.add(stack.pop());
	    	}
	    	return arrayList;
	    }
	}

### 4. 输入某二叉树的前序遍历和中序遍历的结果，请重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。例如输入前序遍历序列{1,2,4,7,3,5,6,8}和中序遍历序列{4,7,2,1,5,3,8,6}，则重建二叉树并返回。
	/**
	 * Definition for binary tree
	 * public class TreeNode {
	 *     int val;
	 *     TreeNode left;
	 *     TreeNode right;
	 *     TreeNode(int x) { val = x; }
	 * }
	 */

	public class Solution {
	    public TreeNode reConstructBinaryTree(int [] pre,int [] in) {
	        
	    	TreeNode rootNode=reConstructBinaryTree(pre,0,pre.length-1,in,0,in.length-1);
	        return rootNode;	
	    }
	    
	    private TreeNode reConstructBinaryTree(int [] pre,int startPre,int endPre,int [] in,int startIn,int endIn) {
	        
	        if(startPre>endPre||startIn>endIn)return null;
		// 新建根节点
	        TreeNode rootNode=new TreeNode(pre[startPre]);
	          
	        for(int i=startIn;i<=endIn;i++){
			// 找到中序中的根节点
			if(in[i]==pre[startPre]){
	            	rootNode.left=reConstructBinaryTree(pre,startPre+1,startPre+i-startIn,in,startIn,i-1);
	            	rootNode.right=reConstructBinaryTree(pre,i-startIn+startPre+1,endPre,in,i+1,endIn);
	            }
		}        
	        return rootNode;
	    }
	}

### 5. 用两个栈来实现一个队列，完成队列的Push和Pop操作。 队列中的元素为int类型。
	import java.util.Stack;

	public class Solution {
	    Stack<Integer> stack1 = new Stack<Integer>();
	    Stack<Integer> stack2 = new Stack<Integer>();
	   // 在一个stack中进
	    public void push(int node) {
	    	stack1.push(node);
	    }
	   // 从stack2中出，若stack2为空，则从stack1中把数据输出到stack2中
	    public int pop() {
	    	if(stack2.isEmpty())
	    	{
	    		while(!stack1.isEmpty())
	    		{
	    			stack2.push(stack1.pop());
	    		}
	    	}
	    	return stack2.pop();
	    }	
	}

注解：先进后出两次为先进先出。

### 6. 把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。 输入一个非减排序的数组的一个旋转，输出旋转数组的最小元素。 例如数组{3,4,5,1,2}为{1,2,3,4,5}的一个旋转，该数组的最小值为1。 NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

	import java.util.ArrayList;
	public class Solution {
	    public int minNumberInRotateArray(int [] array) {
	        if(array.length == 0) return 0;
	        int left = 0;
	        int right = array.length - 1;
		 // 折半查找
	        while(left < right) {
	            int mid = (left + right) / 2;
			// 统一与右边比
	            if(array[mid] > array[right]) {
	                left = mid + 1;
	            } else if(array[mid] < array[right]){
			 // 存在向下取整的情况
	                right = mid;
	            } else {
			// 相等的情况
	                right--;
	            }
	        }
	        return array[left];
	    }
	}

### 7. 大家都知道斐波那契数列，现在要求输入一个整数n，请你输出斐波那契数列的第n项（从0开始，第0项为0）。 n<=39

	public class Solution {
	    public int Fibonacci(int n) {
	    	if(n<=0) return 0;
	    	if(n<=2)return 1;
	    	
	    	int preNum =1;
	    	int pre2Num =1;
	    	int outNum = 0;
	    	
	    	for(int i = 3; i<=n; i++){
	    		outNum = preNum + pre2Num;
	    		pre2Num = preNum;
	    		preNum = outNum;
	    	}
	    	
	    	return outNum;
	    }
	}

### 8. 一只青蛙一次可以跳上1级台阶，也可以跳上2级。求该青蛙跳上一个n级的台阶总共有多少种跳法（先后次序不同算不同的结果）。

	public class Solution {
	    public int JumpFloor(int target) {
	    	if(target<=0) return 0;
	    	if(target<=2) return target;
	    	
	    	int preNum =2;
	    	int pre2Num =1;
	    	int outNum = 0;
	    	
	    	for(int i = 3; i<=target; i++){
	    		outNum = preNum + pre2Num;
	    		pre2Num = preNum;
	    		preNum = outNum;
	    	}
	    	return outNum;
	    }
	}

### 9. 一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。

	public class Solution {
	    public int JumpFloorII(int target) {
	        if(target<=0) return 0;

	        return 1<<(--target);
	    }
	}

注解：f(n) = f(n-1)+f(n-2)...f(1)+1; 由于f(1)=1，递推可得：f(n)=2^(n-1);

### 10. 我们可以用2*1的小矩形横着或者竖着去覆盖更大的矩形。请问用n个2*1的小矩形无重叠地覆盖一个2*n的大矩形，总共有多少种方法？

	public class Solution {
	    public int RectCover(int target) {
	    	if(target<=0) return 0;
	    	if(target<=2) return target;
	    	
	    	int preNum =2;
	    	int pre2Num =1;
	    	int outNum = 0;
	    	
	    	for(int i = 3; i<=target; i++){
	    		outNum = preNum + pre2Num;
	    		pre2Num = preNum;
	    		preNum = outNum;
	    	}
	    	return outNum;
	    }
	}
注解：这题和青蛙跳没区别。

### 11. 输入一个整数，输出该数二进制表示中1的个数。其中负数用补码表示。
	public class Solution {
	    public int NumberOf1(int n) {
	    	int temp = 1;
	    	int count1 = 0;
	    	while(temp!=0)
	    	{
	    		if( (temp&n)!=0 ) count1++;
			// 移动1，避免出现负数移动补1的情况
	    		temp = temp <<1;
	    	}
	    	return count1;
	    }
	}

### 12. 给定一个double类型的浮点数base和int类型的整数exponent。求base的exponent次方。

	public class Solution {
	    public double Power(double base, int exponent) {
	        if(exponent==0) return 1;
		 // 由于要考虑正负数，先统一绝对值计算
	        int absE = Math.abs(exponent);
	         
	        if(exponent<0) {
			return 1/subPower(base,absE);
		  }
	        else{
	        	return subPower(base,absE);
	        }
	    }
	    
	    public double subPower(double base, int exponent) {
	    	if( exponent>1 ){
			//折半乘，提高效率
	    		if( exponent%2 != 0) {
				return base * subPower(base, exponent/2) * subPower(base, exponent/2);
	    		}
			else{
	    			return subPower(base, exponent/2) * subPower(base, exponent/2);
			}
	    	}
	    	return base;    	
	    }
	}

### 13. 输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有的奇数位于数组的前半部分，所有的偶数位于数组的后半部分，并保证奇数和奇数，偶数和偶数之间的相对位置不变。
	
	import java.util.LinkedList;
	import java.util.Queue;
	
	public class Solution {
	    public void reOrderArray(int [] array) {
	        Queue<Integer> queue1 = new LinkedList<Integer>();
	        Queue<Integer> queue2 = new LinkedList<Integer>();
	        
	        for(int i=0;i<array.length;i++){
	            if(array[i]%2==1){
	            	queue1.add(array[i]);
	            }
	            else {
	            	queue2.add(array[i]);
	            }
	        }
	
	        for(int i=0;i<array.length;i++){
	        	if(!queue1.isEmpty()) {
	                array[i]=queue1.poll();
	        	}
	        	else {
	                array[i]=queue2.poll();
	        	}
	        }
	    }
	}


### 14. 输入一个链表，输出该链表中倒数第k个结点。
	/*
	public class ListNode {
	    int val;
	    ListNode next = null;
	
	    ListNode(int val) {
	        this.val = val;
	    }
	}*/
	// 双指针实现
	public class Solution {
	    public ListNode FindKthToTail(ListNode head,int k) {
	    	if ( head == null || k <= 0) {
	    		return null;
	    	}
	       ListNode fastHead = head;
	    	
		// 先走k-1步
	    	for(int i=1; i< k; i++) {
	    		if(fastHead.next != null) {
	        		fastHead = fastHead.next;		
	    		}
	    		else{
	    			return null;
	    		}
	    	}
	    	// 以前走
	    	while(fastHead.next != null){
	    		head = head.next;
	    		fastHead = fastHead.next;
	    	}
	    	return head;
	    }
	}

### 15. 输入一个链表，反转链表后，输出新链表的表头。
	/*
	public class ListNode {
	    int val;
	    ListNode next = null;
	
	    ListNode(int val) {
	        this.val = val;
	    }
	}*/

	public class Solution {
	    public ListNode ReverseList(ListNode head) {
	   	if ( head == null ) {
	    		return null;
	    	}
	    	// 需要前一个，当前，后一个，三个节点处理转向.	    		
	    	ListNode preNode = head;
	    	ListNode nextNode = null;
		//处理首节点
	    	if(head.next != null){
	    		head = head.next;
	    		preNode.next = null;
	    	}
	    	else{
	    		return head;
	    	}
	    	// 递归处理    		
	    	while(head.next != null) {
	    		nextNode = head.next;
	    		head.next = preNode;   		
	    		preNode = head;
	    		head = nextNode;
	    	}
	    	// 尾节点处理
	    	head.next = preNode; 	
	    	return head;
	    }
	}

### 16. 输入两个单调递增的链表，输出两个链表合成后的链表，当然我们需要合成后的链表满足单调不减规则。
	/*
	public class ListNode {
	    int val;
	    ListNode next = null;
	
	    ListNode(int val) {
	        this.val = val;
	    }
	}*/

	public class Solution {
	    public ListNode Merge(ListNode list1,ListNode list2) {
	        if(list1 ==null){
	        	return list2;
	        }
	        if(list2 ==null){
	        	return list1;
	        }
	    	
	    	ListNode head = new ListNode(0);
	    	ListNode indexNode = head;
	    	while(list1 != null && list2 !=null){		
	        	if(list1.val <= list2.val){
	        		indexNode.next = list1;
	        		list1 = list1.next;
	        	}
	        	else{
	        		indexNode.next = list2;
	        		list2 = list2.next;
	        	}
	        	
	    		indexNode = indexNode.next;
	    	}
	    	
	    	if(list1 == null){
	    		indexNode.next = list2;
	    	}
	    	else{
	    		indexNode.next = list1;
	    	}
	  	
	    	return head.next;
	    }
	}

### 17. 输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构）
	/**
	public class TreeNode {
	    int val = 0;
	    TreeNode left = null;
	    TreeNode right = null;
	    public TreeNode(int val) {
	        this.val = val;
	    }
	}
	*/

	public class Solution {
	    public boolean HasSubtree(TreeNode root1,TreeNode root2) {
	        if(root1 == null || root2 == null){
	        	return false;
	        }
	    	
	        if( root1.val == root2.val ){
	            if(sameHasSubtree( root1.left, root2.left) && sameHasSubtree( root1.right, root2.right)){
	            	return true;
	            }
	        }
	        
	        if( HasSubtree(root1.left,root2) || HasSubtree(root1.right,root2)){
	        	return true;
	        }
	        
	        return false;
	    }
	    
	    public boolean sameHasSubtree(TreeNode root1,TreeNode root2) {
	    	
	    	if(root2 == null){
	    		return true; 		
	    	}
	    	if(root1 == null){
	    		return false; 		
	    	}
	    	if( root1.val == root2.val){
	            if(sameHasSubtree( root1.left, root2.left) && sameHasSubtree( root1.right, root2.right)){
	            	return true;
	            }
	    	}
	
	    	return false;
	    }
	}

### 18. 操作给定的二叉树，将其变换为源二叉树的镜像。
	public class Solution {
	    public void Mirror(TreeNode root) {
	        if(root == null){
		 	return ;	
		  }
	        if(root.left != null ){ 
	        	Mirror(root.left);
	        }
	        if(root.right != null){
	        	Mirror(root.right); 
	        }
	    	TreeNode temp = root.left;
	    	root.left = root.right;
	    	root.right = temp;     
	    }
	}

### 19. 输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字，例如，如果输入如下4 X 4矩阵： 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 则依次打印出数字1,2,3,4,8,12,16,15,14,13,9,5,6,7,11,10.
	import java.util.ArrayList;
	public class Solution {
	    public ArrayList<Integer> printMatrix(int [][] matrix) {
	        if(matrix == null){
	        	return null;
	        }
	    	ArrayList<Integer> outList = new ArrayList<>();
	 	
	        int row = matrix.length;
	        int col = matrix[0].length;
	        // 分四个方向讨论遍历，注意截止条件就好。
	        int left = 0,top = 0,bottom = row - 1,right = col - 1;
	        while(left <= right && top <= bottom){
	            for(int i = left;i<=right;i++){
	            	outList.add(matrix[top][i]);
	            }
	            for(int j = top+1;j<=bottom;j++){
	            	outList.add(matrix[j][right]);
	            }
	            if (top != bottom){
	                for(int t = right-1;t>=left;t--){
	                	outList.add(matrix[bottom][t]);
	                }
	            }
	            if(left != right){
	                for(int k = bottom-1;k>top;k--){
	                	outList.add(matrix[k][left]);
	                }
	            }        
	            top++;left++;right--;bottom--;
	        }  
	    	return outList;
	    }	
	}

### 20. 定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））。
	import java.util.Stack;
	
	public class Solution {
		Stack<Integer> stack = new Stack<>();
		Stack<Integer> minStack = new Stack<>();
	
	    public void push(int node) {
	    	stack.push(node);
	    	if( minStack.isEmpty() || node < minStack.peek()){
	    		minStack.push(node);
	    	}
	    	else{
	    		minStack.push(minStack.peek()); // 压自己
	    	}
	    }
	    
	    public void pop() {
	    	stack.pop();
	    	minStack.pop();
	    }
	    
	    public int top() {
	    	return stack.peek();
	    }
	    
	    public int min() {
	    	return minStack.peek();
	    }
	}

### 21. 输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否可能为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如序列1,2,3,4,5是某栈的压入顺序，序列4,5,3,2,1是该压栈序列对应的一个弹出序列，但4,3,5,1,2就不可能是该压栈序列的弹出序列。（注意：这两个序列的长度是相等的）
	import java.util.ArrayList;
	import java.util.Stack;
	
	public class Solution {
	    public boolean IsPopOrder(int [] pushA,int [] popA) {
	        if(pushA.length != popA.length || pushA.length<=0 || popA.length<=0){
	            return false;
	        }
	        Stack<Integer> stack = new Stack<>();
	        stack.push(pushA[0]);
	        for (int i = 0,j = 0; i < popA.length; i++) {
	            while (j < popA.length && stack.peek() != popA[i]) {
	                stack.push(pushA[j++]);
	            }
	            if(stack.peek() != popA[i]){
	                return false;
	            }
	            stack.pop();
	        }     
	        return true;
	    }
	}

### 22. 从上往下打印出二叉树的每个节点，同层节点从左至右打印。
	import java.util.LinkedList;
	import java.util.Queue;
	public class Solution {
	    public ArrayList<Integer> PrintFromTopToBottom(TreeNode root) {
	
	    	Queue<TreeNode> queue = new LinkedList<TreeNode>();
	    	ArrayList<Integer> list = new ArrayList<>();
	    	
	    	while(root != null){
	    		list.add(root.val);
	        	if( root.left != null){
	        		queue.add(root.left);
	        	}
	        	if( root.right != null){
	        		queue.add(root.right);
	        	}
	        	root = queue.poll();
	    	}
	    	
	    	return list;
	    }
	}

### 23. 输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。如果是则输出Yes,否则输出No。假设输入的数组的任意两个数字都互不相同。
	public class Solution {
	    public boolean VerifySquenceOfBST(int [] sequence) {
	        if(sequence.length == 0) return false;
	        return IsTreeBST(sequence, 0, sequence.length-1);
	    }
	    public boolean IsTreeBST(int [] sequence,int start,int end ){
	        if(end <= start) return true;     
	        for (int i = start; i < end; i++) {
	            if(sequence[i] > sequence[end]) break;
	        }
		// 验证后面的是否都大于根值
	        for (int j = i; j < end; j++) {
	            if(sequence[j] < sequence[end]) return false;
	        }
	        return IsTreeBST(sequence, start, i-1) && IsTreeBST(sequence, i, end-1);
	    }	
	}

### 24. 输入一颗二叉树的根节点和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。(注意: 在返回值的list中，数组长度大的数组靠前)
	import java.util.ArrayList;
	
	public class Solution {
	    public ArrayList<ArrayList<Integer>> FindPath(TreeNode root,int target) {
	    	    
	        ArrayList<Integer> a1=new ArrayList<Integer>();
	        ArrayList<ArrayList<Integer>> arr=new ArrayList<ArrayList<Integer>>();               
	
	        path( root, target, arr, a1);
	        return arr;	
	    }	
		
	    public void path(TreeNode root, int target, ArrayList<ArrayList<Integer>> arr, ArrayList<Integer> a1 ){
	        if(root==null){
			return ;
		 }
	        
	        target -= root.val;
	        a1.add(root.val);
	    
	        if(root.left!=null){
	            path( root.left, target, arr, a1);
	        }
	        if(root.right!=null){
	            path( root.right, target, arr, a1);
	        }
	
	        if(root.left==null && root.right==null && target==0){ 
	            arr.add(new ArrayList<Integer>(a1));
	        }   
	        a1.remove(a1.size()-1);   
	    }
	}
注解：本题为保证数组长度大的在前也通过，若需要保证，只要加一级的排序就好。

### 25. 输入一个复杂链表（每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针指向任意一个节点），返回结果为复制后复杂链表的head。（注意，输出结果中请不要返回参数中的节点引用，否则判题程序会直接返回空）

	public class Solution {
		public RandomListNode Clone(RandomListNode pHead){
			if(pHead == null){
				return null;
			}
			
			RandomListNode p=pHead;
			RandomListNode t=pHead;
			// next 赋值
			while(p!=null){
				RandomListNode q=new RandomListNode(p.label);
				q.next=p.next;
				p.next=q;
				p=q.next;
			}
			// random 赋值
			while(t!=null){
				RandomListNode q=t.next;
				if(t.random!=null)
					q.random=t.random.next;
				t=q.next;
			}
			
			// 拆分
			RandomListNode s = new RandomListNode(0);
			RandomListNode s1 = s;
			while (pHead != null) {
				RandomListNode q = pHead.next;
				pHead.next = q.next;
					
				s.next = q;
				s = s.next;
				pHead = pHead.next;
	
			}
			return s1.next;
		}
	}

### 26. 输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。
	public class Solution {
		TreeNode head = null;
		TreeNode realHead = null;
	
		public TreeNode Convert(TreeNode pRootOfTree) {
			if (pRootOfTree == null)
				return null;
			
			Convert(pRootOfTree.left);
			
			if (realHead == null) {
				head = pRootOfTree;
				realHead = pRootOfTree;
			} else {
				head.right = pRootOfTree;
				pRootOfTree.left = head;
				head = pRootOfTree;
			}
			
			Convert(pRootOfTree.right);
			return realHead;
		}
	}

### 27. 输入一个字符串,按字典序打印出该字符串中字符的所有排列。例如输入字符串abc,则打印出由字符a,b,c所能排列出来的所有字符串abc,acb,bac,bca,cab和cba。
	import java.util.ArrayList;
	import java.util.Collections;
	
	public class Solution {
	    public ArrayList<String> Permutation(String str)
	    {
	        ArrayList<String> res=new ArrayList<String>();
	        if(str.length()==0||str==null)return res;
	        char[] chars = str.toCharArray();
	        
	        Permutation(chars,  0, res);
	        
	        // 字典序排序
	        Collections.sort(res);
	        return res;
	         
	    }
	    
	    public void Permutation(char[] chars, int begin, ArrayList<String> result) {
	        if(chars==null || chars.length==0 || begin<0 || begin>chars.length-1) { 
	        	return ; 
	        }
	 
	        if(begin==chars.length-1){
	        	result.add(new String(chars));
	        }
	        
	        // 最初自己的排序也是要算上的
	        for(int i=begin;i<chars.length;i++)
	        {
	            if(i==begin||chars[begin]!=chars[i])
	            {
	                swap(chars,begin,i);
	                Permutation( chars, begin+1, result);   
	                swap(chars,begin,i);
	            }
	        }
	    }
	 
	    public void swap(char[]t,int i,int j)
	     {
	        char c=t[i];
	        t[i]=t[j];
	        t[j]=c;
	    }
		
	}
注解：这种都是典型的回溯法（1，n-1）

### 28. 数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。
	import java.util.HashMap;
	
	public class Solution {
	    public int MoreThanHalfNum_Solution(int [] array) {	
	    	HashMap<Integer, Integer> map = new HashMap<>();
	
	    	for(int i = 0; i<array.length; i++){
	    		if(map.containsKey(array[i])){
	    			map.put(array[i], map.get(array[i]) + 1 );
	    		} 
	    		else{
	    			map.put(array[i], 1 );
	    		}
	    	}
	    	
	    	for (Integer key : map.keySet()) { 
	    		if ( map.get(key) > array.length/2){
	    			return key;
	    		}  	
	    	} 
	    	return 0;
	    }
	}
注解：由于不存在的可能性，故剑指offer中的技巧不能用。

### 29. 输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4,。
	import java.util.Collections;
	import java.util.ArrayList;
	
	public class Solution {
	    public ArrayList<Integer> GetLeastNumbers_Solution(int [] input, int k) {
	    	ArrayList<Integer> res = new ArrayList<Integer>();
	        if(input.length < 1 || k > input.length) return res;
	        
	        for(int i =0; i<input.length; i++){
	        	res.add(input[i]);
	        }
	        Collections.sort(res);
	         
	    	ArrayList<Integer> out = new ArrayList<Integer>();
	        for(int i =0; i<k; i++){
	        	out.add(res.get(i));
	        }	        
	        return out;
	    }
	}
注解：这题真扯淡

### 30. HZ偶尔会拿些专业问题来忽悠那些非计算机专业的同学。今天测试组开完会后,他又发话了:在古老的一维模式识别中,常常需要计算连续子向量的最大和,当向量全为正数的时候,问题很好解决。但是,如果向量中包含负数,是否应该包含某个负数,并期望旁边的正数会弥补它呢？例如:{6,-3,-2,7,-15,1,2,2},连续子向量的最大和为8(从第0个开始,到第3个为止)。给一个数组，返回它的最大连续子序列的和，你会不会被他忽悠住？(子向量的长度至少是1)
	public class Solution {
	    public int FindGreatestSumOfSubArray(int[] array) {
	    	if(array == null){
	    		return 0;
	    	}
	    	int[] arr2 = new int[array.length];
	    	arr2[0] = array[0];
	    	for(int i=1; i< array.length; i++){
			// 核心思想
	    		if(arr2[i-1] < 0){
	    			arr2[i] = array[i];
	    		}
	    		else{
	    			arr2[i] = arr2[i-1] + array[i];
	    		}
	    	}
	    	
	    	int max = arr2[0];
	    	for(int i=1; i< array.length; i++){
	    		if(arr2[i]>max){
	    			max = arr2[i];
	    		}
	    	}
	    	return max;
	    }
	}

### 31. 求出1~13的整数中1出现的次数,并算出100~1300的整数中1出现的次数？为此他特别数了一下1~13中包含1的数字有1、10、11、12、13因此共出现6次,但是对于后面问题他就没辙了。ACMer希望你们帮帮他,并把问题更加普遍化,可以很快的求出任意非负整数区间中1出现的次数（从1 到 n 中1出现的次数）。
	public class Solution {
	    public int NumberOf1Between1AndN_Solution(int n) {
	        int base = 1;
	        int count =0;
	        int round = n;
			int weight = 0；

	        while(round>0){
	        	weight = round%10;
	        	round = round /10;
				// 按位计算1的数目
	        	count += round * base;
	        	if(weight==1){
	        		count += (n % base) + 1;
	        	}
	        	else if (weight>1) {
	        		count += base;
				}
	        	
	        	base *= 10;
	        } 
	        return count;
	    }
	}

### 32. 输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。
	import java.util.ArrayList;
	import java.util.Arrays;
	import java.util.Comparator;
	
	public class Solution {
	    public String PrintMinNumber(int [] numbers) {
	        if(numbers == null || numbers.length == 0) return "";
	        int len = numbers.length;
	        String[] str = new String[len];
	        StringBuilder sb = new StringBuilder();
		// 数字可能越界，转字符串计算
	        for(int i = 0; i < len; i++){
	            str[i] = String.valueOf(numbers[i]);
	        }
	        Arrays.sort(str,new Comparator<String>(){
	            @Override
	            public int compare(String s1, String s2) {
	                String c1 = s1 + s2;
	                String c2 = s2 + s1;
	                return c1.compareTo(c2);
	            }
	        });
	        for(int i = 0; i < len; i++){
	            sb.append(str[i]);
	        }
	        return sb.toString();
	    }
	}

### 33. 把只包含质因子2、3和5的数称作丑数（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含质因子7。 习惯上我们把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。
	public class Solution {
	    public int GetUglyNumber_Solution(int index) {
	    	if( index<=1 ) return index;
	    	int[] list = new int[index];
	    	
	    	list[0] = 1;
	    	int temp2=0,temp3=0,temp5=0;
	    	
	    	int count = 0;
	    	while( count<index-1 ){
	    		while(list[temp2]*2<=list[count]){
	    			temp2++;
	    		}
	    		while(list[temp3]*3<=list[count]){
	    			temp3++;
	    		}
	    		while(list[temp5]*5<=list[count]){
	    			temp5++;
	    		}
	    		list[++count] = Math.min(Math.min(2*list[temp2], 3*list[temp3]) , 5*list[temp5]);
	    	}	
	        return list[list.length-1];
	    }
	}
注解：空间换时间算法

### 34. 在一个字符串(0<=字符串长度<=10000，全部由字母组成)中找到第一个只出现一次的字符,并返回它的位置, 如果没有则返回 -1（需要区分大小写）.
	import java.util.LinkedHashMap;
	
	public class Solution {
	    public static int FirstNotRepeatingChar(String str) {
	    	LinkedHashMap <Character, Integer> map = new LinkedHashMap<Character, Integer>();
	        for(int i=0;i<str.length();i++){
	            if(map.containsKey(str.charAt(i))){
	                map.put(str.charAt(i), map.get(str.charAt(i)) + 1 );
	            }
	            else {
	                map.put(str.charAt(i), 1);
	            }
	        }
	        
	        int i=0;
	        for(;i<str.length();i++){
	            char c = str.charAt(i);
	            if (map.get(c) == 1) {
	                return i;
	            }
	        }
	        return -1;
	    }
	}
注解：借助hashmap

### 35. 在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组,求出这个数组中的逆序对的总数P。并将P对1000000007取模的结果输出。 即输出P%1000000007
	public class Solution {
	public static int InversePairs(int [] array) {
	        if(array==null||array.length==0){
	            return 0;
	        }
	        int[] copy = new int[array.length];
	        int count = InversePairsCore( array, copy, 0, array.length-1);//数值过大求余
	        return count;  
	    }
	    
	    private  static int InversePairsCore( int[] array, int[] copy, int low, int high)
	    {
	        if(low==high){
	            return 0;
	        }

	        int mid = (low+high)>>1;
	        int leftCount = InversePairsCore( array, copy, low, mid);
	        int rightCount = InversePairsCore( array, copy, mid+1, high);
	        
	        int count = 0;
	        int i=mid;
	        int j=high;
	        int locCopy = high;
		// 归并排序，保证有序
	        while(i>=low&&j>mid){
	            if(array[i]>array[j]){
	                count += j-mid;		// 核心计算逆序对代码
	                copy[locCopy--] = array[i--];
	                if(count>=1000000007){
	                    count%=1000000007;
	                }
	            }
	            else{
	                copy[locCopy--] = array[j--];
	            }
	        }

	        for(;i>=low;i--)
	        {
	            copy[locCopy--]=array[i];
	        }
	        for(;j>mid;j--)
	        {
	            copy[locCopy--]=array[j];
	        }
		// 复制回去，保证有序
	        for(int s=low;s<=high;s++)
	        {
	            array[s] = copy[s];
	        }

	        return (leftCount+rightCount+count)%1000000007;
	    }
	}
注解：这一题肯定可以简化，懒得改了

### 36. 输入两个链表，找出它们的第一个公共结点。
	public class Solution {
	    public ListNode FindFirstCommonNode(ListNode pHead1, ListNode pHead2) { 
	        if(pHead1==null||pHead2==null){
	            return null;
	        }
	        if(pHead1==pHead2){
	            return pHead1;
	        }
		
	        int len1=0;
	        int len2=0;
	        ListNode curr1=pHead1;
	        ListNode curr2=pHead2;
	        while(curr1!=null){
	            len1++;
	            curr1=curr1.next;
	        }
	        while(curr2!=null){
	            len2++;
	            curr2=curr2.next;
	        }

	        curr1=pHead1;
	        curr2=pHead2;
	        if(len1>len2){
	            int moreLen=len1-len2;
	            while(moreLen!=0){
	                curr1=curr1.next;
	                moreLen--;
	            }
	        }
	        else{
	            int moreLen=len2-len1;
	            while(moreLen!=0){
	                curr2=curr2.next;
	                moreLen--;
	            }
	        }
	 
	        while(curr1!=curr2&&curr1!=null){
	            curr1=curr1.next;
	            curr2=curr2.next;
	        }
	         
	        return curr1;
	    }
	}

### 37. 统计一个数字在排序数组中出现的次数。
	public class Solution {
	    public int GetNumberOfK(int [] array , int k) {
	        if(array.length<1) return 0;    
	        int begin =0;
	        int end = array.length-1;
	        
	        return GetNumberOfK(array , k, begin, end);
	    }
		
	    public int GetNumberOfK(int[] array,int k, int begin, int end){
	        if(begin>end)
	            return 0;
	        int mid=(end-begin)/2+begin;
	        
	        if(k>array[mid])
	            return GetNumberOfK(array,k,mid+1,end);
	        else if(k<array[mid])
	            return GetNumberOfK(array,k,begin,mid-1);
	        else {
	            return 1+GetNumberOfK(array,k,begin,mid-1)+GetNumberOfK(array,k,mid+1,end);
	        }   
	    }
	}
注解：折半查找提高效率

### 38. 输入一棵二叉树，求该树的深度。从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。
	public class Solution {
		public int TreeDepth(TreeNode root) {
			if (root == null) {
				return 0;
			}
	
			return Math.max(TreeDepth(root.left), TreeDepth(root.right)) + 1;
		}
	}

### 39. 输入一棵二叉树，判断该二叉树是否是平衡二叉树。
	public class Solution {
	    public boolean IsBalanced_Solution(TreeNode root) {
	        if (root == null) {
	            return true;
	        }
	        int left = getTreeDepth(root.left);
	        int right = getTreeDepth(root.right);
	        int diff = Math.abs(left - right);
	        if (diff > 1)
	            return false;
	        return IsBalanced_Solution(root.left) && IsBalanced_Solution(root.right);
	         
	    }
   
	    public  int getTreeDepth(TreeNode root) {
	        if (root == null)
	            return 0;
	        else
	            return Math.max(getTreeDepth(root.left), getTreeDepth(root.right)) + 1;
	    }
	}

### 40. 一个整型数组里除了两个数字之外，其他的数字都出现了偶数次。请写程序找出这两个只出现一次的数字。
	//num1,num2分别为长度为1的数组。传出参数
	//将num1[0],num2[0]设置为返回结果
	public class Solution {
	    public void FindNumsAppearOnce(int [] array,int num1[] , int num2[]) {
	        if (array == null) return ;
	        
	        int binaryVal = array[0];
	
	        for(int i=1; i< array.length; i++){
	        	binaryVal ^= array[i];
	        }
	        // 找到第一个bit不为1的数
	        int binary1 = 1;
	        while((binary1&binaryVal)==0){
	        	binary1 = binary1<<1;        	
	        }
	        
	        boolean flag1 = true;
	        boolean flag2 = true;    
	        for(int i=0; i< array.length; i++){
	        	if( (binary1 & array[i]) !=0 && flag1){
	        		num1[0] = array[i];
				flag1 = false;
	        	}
			else if( (binary1 & array[i]) !=0){
				num1[0] = num1[0] ^array[i];
			}
	        	else if(flag2){
	        		num2[0] = array[i];
				flag2 = false;
	        	}
			else{
				num2[0] = num2[0] ^array[i];
			}
	        }
	    }
	}

### 41. 小明很喜欢数学,有一天他在做数学作业时,要求计算出9~16的和,他马上就写出了正确答案是100。但是他并不满足于此,他在想究竟有多少种连续的正数序列的和为100(至少包括两个数)。没多久,他就得到另一组连续正数和为100的序列:18,19,20,21,22。现在把问题交给你,你能不能也很快的找出所有和为S的连续正数序列? Good Luck!
	import java.util.ArrayList;
	public class Solution {
	    public ArrayList<ArrayList<Integer> > FindContinuousSequence(int sum) {
	    	 ArrayList<ArrayList<Integer> >  list = new ArrayList<ArrayList<Integer> > ();
	         if(sum<=1)return list;
		  // 这里是一个优化，前提单个数为100不行
	         for(int i=1;i<=sum/2;i++){
	             ArrayList<Integer> List2=new ArrayList<Integer>();
	             int count=0;
	             for(int j=i;j<sum;j++){
	                 count+=j;
	                 List2.add(j);
	                 if(count>sum)
	                     break;
	                 else if(count==sum){
	                	 list.add(List2);
	                     break;  
	                 }
	             }
	         }
	          
	         return list;
	    }
	}

### 42. 输入一个递增排序的数组和一个数字S，在数组中查找两个数，使得他们的和正好是S，如果有多对数字的和等于S，输出两个数的乘积最小的。
	import java.util.ArrayList;
	
	public class Solution {
	    public ArrayList<Integer> FindNumbersWithSum(int [] array,int sum) {
	    	ArrayList<Integer> list = new ArrayList<>();
	    	if(array.length<2) return list;
	    	
	    	int begin = 0;
	    	int end = array.length - 1;
	    	
	    	while(begin<end){
	    		if( (array[begin] + array[end])< sum){
	    			begin++;
	    		}
	    		else if( (array[begin] + array[end])> sum){
	    			end--;
	    		}
	    		else{
	    			list.add(array[begin]);
	    			list.add(array[end]);
	                break;
	    		}
	    	}
	    	
	    	return list;
	    }
	}

### 43. 汇编语言中有一种移位指令叫做循环左移（ROL），现在有个简单的任务，就是用字符串模拟这个指令的运算结果。对于一个给定的字符序列S，请你把其循环左移K位后的序列输出。例如，字符序列S=”abcXYZdef”,要求输出循环左移3位后的结果，即“XYZdefabc”。是不是很简单？OK，搞定它！
	public class Solution {
	    public String LeftRotateString(String str,int n) {
	    	
	    	if(str.length()<0 || n<0 || n>str.length()) return str;
	    	
	        char[] chars = str.toCharArray();
	
	        for(int i = 0; i<chars.length/2; i++){
	        	char temp = chars[i];
	        	chars[i] = chars[chars.length-1-i];
	        	chars[chars.length-1-i] = temp;
	        }
	        for(int i = 0; i<(chars.length-n)/2; i++){
	        	char temp = chars[i];
	        	chars[i] = chars[chars.length-n-1-i];
	        	chars[chars.length-n-1-i] = temp;
	        }
	        
	        for(int i = chars.length-n; i<(chars.length-n/2); i++){
	        	char temp = chars[i];
	        	chars[i] = chars[chars.length-1-i + chars.length-n];
	        	chars[chars.length-1-i + chars.length-n] = temp;
	        }
	        
	        return new String(chars);
	    }
	}
注解：不借助辅助空间的算法，需要两次翻转。为了避免混乱，也可以使用前后两指针交换的算法。

### 44. 牛客最近来了一个新员工Fish，每天早晨总是会拿着一本英文杂志，写些句子在本子上。同事Cat对Fish写的内容颇感兴趣，有一天他向Fish借来翻看，但却读不懂它的意思。例如，“student. a am I”。后来才意识到，这家伙原来把句子单词的顺序翻转了，正确的句子应该是“I am a student.”。Cat对一一的翻转这些单词顺序可不在行，你能帮助他么？
	public class Solution {
	    public String ReverseSentence(String str) {
	    	if(str.length()<1 ) return str;
	
	        char[] chars = str.toCharArray();        
	        // 先翻转
	        for(int i = 0; i<chars.length/2; i++){
	        	char temp = chars[i];
	        	chars[i] = chars[chars.length-1-i];
	        	chars[chars.length-1-i] = temp;
	        }

	        int end;
	        int begin = 0;
	        for(int i = 0; i<=chars.length-1; i++){
	        	if( chars[i]== ' ' || i==chars.length-1){
	        		end = i - 1;
	        		if(i==chars.length-1){
	        			end++;
	        		}
	                while(begin<end){
	                	char temp = chars[begin];
	                	chars[begin] = chars[end];
	                	chars[end] = temp;
	                	begin++;
	                	end--;
	                }
	                begin = i + 1;
	        	}
	        }
	        return new String(chars);
	    }
	}
注解：上一题的一般化

### 45. LL今天心情特别好,因为他去买了一副扑克牌,发现里面居然有2个大王,2个小王(一副牌原本是54张^_^)...他随机从中抽出了5张牌,想测测自己的手气,看看能不能抽到顺子,如果抽到的话,他决定去买体育彩票,嘿嘿！！“红心A,黑桃3,小王,大王,方片5”,“Oh My God!”不是顺子.....LL不高兴了,他想了想,决定大\小 王可以看成任何数字,并且A看作1,J为11,Q为12,K为13。上面的5张牌就可以变成“1,2,3,4,5”(大小王分别看作2和4),“So Lucky!”。LL决定去买体育彩票啦。 现在,要求你使用这幅牌模拟上面的过程,然后告诉我们LL的运气如何， 如果牌能组成顺子就输出true，否则就输出false。为了方便起见,你可以认为大小王是0。
	import java.util.Arrays;
	
	public class Solution {
	    public boolean isContinuous(int [] numbers) {
	        if(numbers.length <1)
	            return false;
	        Arrays.sort(numbers);  //先排序
	        int zero = 0, i = 0;
	        for(; i < numbers.length && numbers[i] == 0; i++){
	            zero ++;  //统计0的个数
	        }
	        for(i = zero; i < numbers.length - 1; i++){
	            if(numbers[i] == numbers[i+1]) //有对子，则返回false
	                return false;
	        }
        
	        if( (numbers[numbers.length -1] - numbers[zero]+1) <=5 )
	            return true;
	        else
	            return false;
	    }
	}

### 46. 每年六一儿童节,牛客都会准备一些小礼物去看望孤儿院的小朋友,今年亦是如此。HF作为牛客的资深元老,自然也准备了一些小游戏。其中,有个游戏是这样的:首先,让小朋友们围成一个大圈。然后,他随机指定一个数m,让编号为0的小朋那个小朋友友开始报数。每次喊到m-1的要出列唱首歌,然后可以在礼品箱中任意的挑选礼物,并且不再回到圈中,从他的下一个小朋友开始,继续0...m-1报数....这样下去....直到剩下最后一个小朋友,可以不用表演,并且拿到牛客名贵的“名侦探柯南”典藏版(名额有限哦!!^_^)。请你试着想下,哪个小朋友会得到这份礼品呢？(注：小朋友的编号是从0到n-1)
	public class Solution {
	    public int LastRemaining_Solution(int n, int m) {
	        if(n==0||m==0)return -1;
	        int s=0;
	        for(int i=2;i<=n;i++)
	        {
	            s=(s+m)%i;
	        }   
	       return s ;
	    }
	}
注解：每一次都看成一个新的排序f[i-1]，但是下标是从k=m%n开始的。所以上一次的结果f[i]等于(f[i-1]+k)%n。于是有：

##### 递推公式：f[i]=(f[i-1]+k)%n => f[1]=0，f[i]=(f[i-1]+m)%i;

### 47. 求1+2+3+...+n，要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句（A?B:C）。
public class Solution {
    public int Sum_Solution(int n) {
       int sum = n;
       boolean flag = (n>0)&&((sum +=Sum_Solution(n-1))>0);
       return sum;
    }
}
注解：利用了逻辑&&的截断功能。

### 48. 写一个函数，求两个整数之和，要求在函数体内不得使用+、-、*、/四则运算符号。
public class Solution {
	public int Add(int num1, int num2) {
		int num3;
		int num4;
		do {
			num3 = num1 ^ num2;
			num4 = (num1 & num2) << 1;
			num1 = num3;
			num2 = num4;
		} while (num4 != 0);
		return num1;
	}
}
注解：通过位运算

### 49. 将一个字符串转换成一个整数(实现Integer.valueOf(string)的功能，但是string不符合数字要求时返回0)，要求不能使用字符串转换整数的库函数。 数值为0或者字符串不是一个合法的数值则返回0。
	public class Solution {
	    public int StrToInt(String str)
	    {
	        if ( str.length() < 1)
	            return 0;
	        char[] a = str.toCharArray();
	        
	        boolean fuhao = true;
	        int begin = 0;
	        
	        if (a[0] == '-'){
	            fuhao = false;
	            begin = 1;
	        }
	        else if(a[0] == '+'){
	            begin = 1;
	        }
	
	        int sum = 0;
	        for (int i = begin; i < a.length; i++){
	            if (a[i] < 48 || a[i] > 57)
	                return 0;
	            sum = sum * 10 + a[i] - 48;
	        }  
	        return fuhao ? sum : sum * -1;
	    }
	}
注解：主要符号的判断。字符0的ASCII码是48

### 50. 在一个长度为n的数组里的所有数字都在0到n-1的范围内。 数组中某些数字是重复的，但不知道有几个数字是重复的。也不知道每个数字重复几次。请找出数组中任意一个重复的数字。 例如，如果输入长度为7的数组{2,3,1,0,2,5,3}，那么对应的输出是第一个重复的数字2。
	public boolean duplicate(int numbers[],int length,int [] duplication) {
	    if(length <1 ) return false; 
		
	    for(int i=0;i<length;i++){
	        while(numbers[i]!=i){
	            if(numbers[i]==numbers[numbers[i]]){
	            	duplication[0]=numbers[i];
	                return true;
	            }
	            int temp=numbers[i];
	            numbers[i]=numbers[temp];
	            numbers[temp]=temp;
	        }
	    }
	    return false;
	}
注解：利用数组值与下标的关系

### 51. 给定一个数组A[0,1,...,n-1],请构建一个数组B[0,1,...,n-1],其中B中的元素B[i]=A[0]*A[1]*...*A[i-1]*A[i+1]*...*A[n-1]。不能使用除法。
	import java.util.ArrayList;
	public class Solution {
	    public int[] multiply(int[] A) {
	    	if(A.length<1) return A;
	    	
	    	int[] B = new int[A.length];
	    	
	    	B[0] = 1;
	    	for(int i=1; i< A.length; i++){
	    		B[i] = B[i-1] * A[i-1];
	    	}
	    	
	
	    	int temp = 1;
	    	for(int i=A.length-2; i>=0; i--){ 
	    		temp *= A[i+1];
	    		B[i] = B[i] * temp;
	    	}
	    	
	    	return B;
	    }
	}
注解：利用前后两次乘，逃离中间乘。

### 52. 请实现一个函数用来匹配包括'.'和'*'的正则表达式。模式中的字符'.'表示任意一个字符，而'*'表示它前面的字符可以出现任意次（包含0次）。 在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串"aaa"与模式"a.a"和"ab*ac*a"匹配，但是与"aa.a"和"ab*a"均不匹配

	public class Solution {
		public boolean match(char[] str, char[] pattern) {
			return matchTwo(str, 0, str.length, pattern, 0, pattern.length);
		}
	
		private boolean matchTwo(char[] str, int i, int length1, char[] pattern, int j, int length2) {
			if (i == length1 && j == length2) {
				return true;
			}
			if (i == length1 && j != length2) {
				while (j != length2) {
					if (pattern[j] != '*' && (j + 1 >= length2 || pattern[j + 1] != '*')) {
						return false;
					}
					j++;
				}
				return true;
			}
			if (i != length1 && j == length2) {
				return false;
			}
			if (j + 1 == length2) {
				if (str[i] == pattern[j] || pattern[j] == '.')
					return matchTwo(str, i + 1, length1, pattern, j + 1, length2);
				else {
					return false;
				}
			}
			if ((str[i] == pattern[j] || pattern[j] == '.') && pattern[j + 1] != '*')
				return matchTwo(str, i + 1, length1, pattern, j + 1, length2);
			if ((str[i] == pattern[j] || pattern[j] == '.') && pattern[j + 1] == '*')
				return matchTwo(str, i, length1, pattern, j + 2, length2)
						|| matchTwo(str, i + 1, length1, pattern, j, length2);
			if (pattern[j + 1] == '*')
				return matchTwo(str, i, length1, pattern, j + 2, length2);
			return false;
		}
	}
注解：不是我说，让你写一个这种函数还真不好写！！！！

### 53. 请实现一个函数用来判断字符串是否表示数值（包括整数和小数）。例如，字符串"+100","5e2","-123","3.1416"和"-1E-16"都表示数值。 但是"12e","1a3.14","1.2.3","+-5"和"12e+4.3"都不是。
	public class Solution {
	    public boolean isNumeric(char[] str) {
	        String s=String.valueOf(str);
	        return s.matches("[+-]?[0-9]*(\\.[0-9]*)?([eE][+-]?[0-9]+)?");  
	    }
	}

### 54. 请实现一个函数用来找出字符流中第一个只出现一次的字符。例如，当从字符流中只读出前两个字符"go"时，第一个只出现一次的字符是"g"。当从该字符流中读出前六个字符“google"时，第一个只出现一次的字符是"l"。如果当前字符流没有存在出现一次的字符，返回#字符。
	import java.util.HashMap;
	import java.util.ArrayList;
	
	public class Solution {
	    HashMap<Character, Integer> map=new HashMap<>();
	    ArrayList<Character> list=new ArrayList<Character>();
	
	    public void Insert(char ch)
	    {
	        if(map.containsKey(ch)){
	            map.put(ch,map.get(ch)+1);
	        }else{
	            map.put(ch,1);
	        }
	          
	        list.add(ch);
	    }
	      
	    public char FirstAppearingOnce()
	    {   
	    	char c='#';
	        for(char key : list){
	            if(map.get(key)==1){
	                c=key;
	                break;
	            }
	        }
	          
	        return c;
	    }
	}

### 55. 给一个链表，若其中包含环，请找出该链表的环的入口结点，否则，输出null。
	public class Solution {
	
	    public ListNode EntryNodeOfLoop(ListNode pHead){
	        if (pHead == null || pHead.next == null || pHead.next.next == null)
	              return null;
	          ListNode slow = pHead.next;
	          ListNode quick = pHead.next.next;
	          
	          // 1 找到是否有环，两个指针
	          while (slow != quick) {
	              if (slow.next != null && quick.next.next != null) {
	                  slow = slow.next;
	                  quick = quick.next.next;
	              } else{
	                  return null;
	              }
	          }
	      	// 2 确定环的大小k      但是其大小正好是 slow走的距离。 // 3 先走k步，相遇点为入口。
	          quick = pHead;
	          while (quick != slow) {
	              quick = quick.next;
	              slow = slow.next;
	          }
	          return slow; 
	      }
	}

### 56. 在一个排序的链表中，存在重复的结点，请删除该链表中重复的结点，重复的结点不保留，返回链表头指针。 例如，链表1->2->3->3->4->4->5 处理后为 1->2->5
	public class Solution {
		public ListNode deleteDuplication(ListNode pHead) {
			ListNode result;
			ListNode temp = pHead;
			ListNode index = new ListNode(1);
			index.next = pHead;
			result = index;
			while (temp != null) {
				if (temp.next != null && temp.next.val == temp.val) {
					while (temp.next != null && temp.next.val == temp.val) {
						temp = temp.next;
					}
					temp = temp.next;
					index.next = temp;
				} else {
					index = index.next;
					temp = temp.next;
				}
			}
			return result.next;
		}
	}

### 57. 给定一个二叉树和其中的一个结点，请找出中序遍历顺序的下一个结点并且返回。注意，树中的结点不仅包含左右子结点，同时包含指向父结点的指针。
	public class Solution {
	    public TreeLinkNode GetNext(TreeLinkNode pNode)
	    {
	        if(pNode==null) return null;
	        // 右节点不为空
	        if(pNode.right!=null)
	        {
			// 找到右节点的最左节点
	        	pNode=pNode.right;
	            while(pNode.left!=null)
	            {
	            	pNode=pNode.left;
	               
	            }
	            return pNode;
	        }
	        // 右节点为空，找到其为父节点的左节点的那个最近的父节点。
	        while(pNode.next!=null)
	        {
	            if(pNode.next.left==pNode) return pNode.next;
	            pNode=pNode.next;
	        }
	        return null;
	    }
		
	}

### 58. 请实现一个函数，用来判断一颗二叉树是不是对称的。注意，如果一个二叉树同此二叉树的镜像是同样的，定义其为对称的。
	public class Solution {
		public boolean isSymmetrical(TreeNode pRoot) {
	    	if(pRoot==null) return true;
	    	return isSymmetrical(pRoot.left,pRoot.right);
	    }
	
		public boolean isSymmetrical(TreeNode left, TreeNode right) {
			if (left == null && right == null)
				return true;
			if (left == null || right == null)
				return false;
			if (left.val == right.val)
				// 左左相比，右右相比，这是最核心的思想了。
				return isSymmetrical(left.left, right.right) && isSymmetrical(left.right, right.left);
			return false;
		}
	}

### 59. 请实现一个函数按照之字形打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右至左的顺序打印，第三行按照从左到右的顺序打印，其他行以此类推。
	import java.util.ArrayList;
	import java.util.Queue;
	import java.util.LinkedList;
	public class Solution {
	    public ArrayList<ArrayList<Integer> > Print(TreeNode pRoot) {
	        ArrayList<ArrayList<Integer>> res=new ArrayList<ArrayList<Integer>>();
	        if(pRoot==null){
	            return res;
	        }
	        ArrayList<Integer> list;
	        Queue<TreeNode> queue=new LinkedList<TreeNode>();
	        int rows=1;
	        queue.add(pRoot);
	        while(!queue.isEmpty()){
	            list=new ArrayList<>();
	            int size=queue.size();
	            for(int i=0;i<size;i++){
	                TreeNode t=queue.poll();
					// 分层考虑
	                if(rows%2==0){
	                    list.add(0,t.val); // 每次都插入到最前面
	                }else{
	                    list.add(t.val);
	                }
	                if(t.left!=null){
	                    queue.offer(t.left);
	                }
	                if(t.right!=null){
	                    queue.offer(t.right);
	                }
	            }
	            res.add(list);
	            rows++;
	        }
	        return res;
	    }
	
	}

### 60. 从上到下按层打印二叉树，同一层结点从左至右输出。每一层输出一行。
	public class Solution {
	ArrayList<ArrayList<Integer> > Print(TreeNode pRoot) {
	        ArrayList<ArrayList<Integer> > result = new ArrayList<>();
	        if(pRoot == null) {
	            return result;
	        }
	        ArrayList<TreeNode> nodes = new ArrayList<>();
	        nodes.add(pRoot);
	        Print(result, nodes);
	        return result;
	    }
	     
	    public void Print(ArrayList<ArrayList<Integer> > result, ArrayList<TreeNode> nodes) {
	        if(!nodes.isEmpty()) {
	            ArrayList<Integer> temp = new ArrayList<>();
	            ArrayList<TreeNode> next = new ArrayList<>();
	            for(TreeNode t : nodes) {
	                temp.add(t.val);
	                if(t.left != null) {
	                    next.add(t.left);
	                }
	                if(t.right != null) {
	                    next.add(t.right);
	                }
	            }
	            result.add(temp);
	            Print(result, next);
	        }
	    }
	}

注解：借助队列会更好做吧

### 62. 给定一棵二叉搜索树，请找出其中的第k小的结点。例如， （5，3，7，2，4，6，8）    中，按结点数值大小顺序第三小结点的值为4。
	public class Solution {
		int index = 0; 
		TreeNode KthNode(TreeNode root, int k) {
			if (root != null) { // 中序遍历寻找第k个
				TreeNode node = KthNode(root.left, k);
				if (node != null)
					return node;
				index++;
				if (index == k)
					return root;
				node = KthNode(root.right, k);
				if (node != null)
					return node;
			}
			return null;
		}
	}

### 63. 如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。我们使用Insert()方法读取数据流，使用GetMedian()方法获取当前读取数据的中位数。
	import java.util.ArrayList;
	import java.util.Collections;
	
	public class Solution {
	
		ArrayList<Integer> al = new ArrayList<Integer>();
	
		public void Insert(Integer num) {
			al.add(num);
			Collections.sort(al);
		}
	
		public Double GetMedian() {
			int mid = al.size() / 2;
			if ((al.size() & 1) == 0) {
				Integer n1 = al.get(mid);
				Integer n2 = al.get(mid - 1);
				double s = (Double.valueOf(n1 + "") + Double.valueOf(n2 + "")) / 2;
				return s;
			} else {
				double s = Double.valueOf(al.get(mid) + "");
				return s;
			}
		}
	}

### 65. 请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一个格子开始，每一步可以在矩阵中向左，向右，向上，向下移动一个格子。如果一条路径经过了矩阵中的某一个格子，则之后不能再次进入这个格子。 例如 a b c e s f c s a d e e 这样的3 X 4 矩阵中包含一条字符串"bcced"的路径，但是矩阵中不包含"abcb"路径，因为字符串的第一个字符b占据了矩阵中的第一行第二个格子之后，路径不能再次进入该格子。
	public class Solution {
		// 65
	    public boolean hasPath(char[] matrix, int rows, int cols, char[] str)
	    {
	        int []flag=new int[matrix.length];
		// 可选开始值
	        for(int i=0;i<rows;i++) {
	            for(int j=0;j<cols;j++) {
	               if(hasPath(matrix,rows,cols,i,j,0,str,flag))
	                  return  true;
	            }
	        }
	      return false;
	    }
	    public boolean hasPath(char[]matrix,int rows,int cols,int i,int j,int k,char[]str,int[]flag)
	    {
	        int index=i*cols+j;
	        if(i<0||i>=rows||j<0||j>=cols||flag[index]==1||matrix[index]!=str[k])return false;
	        if(k==str.length-1) return true;
	        
	        flag[index]=1;
	        // 核心遍历
	        if(hasPath(matrix,rows,cols,i+1,j,k+1,str,flag)||hasPath(matrix,rows,cols,i-1,j,k+1,str,flag)||hasPath(matrix,rows,cols,i,j+1,k+1,str,flag)||hasPath(matrix,rows,cols,i,j-1,k+1,str,flag))
	          return  true;
	        
	        flag[index]=0;
	        return false;
	    }
	}

### 66. 地上有一个m行和n列的方格。一个机器人从坐标0,0的格子开始移动，每一次只能向左，右，上，下四个方向移动一格，但是不能进入行坐标和列坐标的数位之和大于k的格子。 例如，当k为18时，机器人能够进入方格（35,37），因为3+5+3+7 = 18。但是，它不能进入方格（35,38），因为3+5+3+8 = 19。请问该机器人能够达到多少个格子？
	public class Solution {
	 public int movingCount(int threshold, int rows, int cols)
		    {
		        boolean[] visited=new boolean[rows*cols];
				// 与上一题相比，课题初始值只有一个
		        return movingCountCore(threshold, rows, cols, 0,0,visited);
		    }
		    private int movingCountCore(int threshold, int rows, int cols,
		            int row,int col,boolean[] visited) {
		        if(row<0||row>=rows||col<0||col>=cols) return 0;
		        int i=row*cols+col;
		        if(visited[i]||!checkSum(threshold,row,col)) return 0;
		        visited[i]=true;
				// 这里保持统一，而在返回推荐控制越界。
		        return 1+movingCountCore(threshold, rows, cols,row,col+1,visited)
		                +movingCountCore(threshold, rows, cols,row,col-1,visited)
		                +movingCountCore(threshold, rows, cols,row+1,col,visited)
		                +movingCountCore(threshold, rows, cols,row-1,col,visited);
		    }
			//指定规则
		    private boolean checkSum(int threshold, int row, int col) {
		        int sum=0;
		        while(row!=0){
		            sum+=row%10;
		            row=row/10;
		        }
		        while(col!=0){
		            sum+=col%10;
		            col=col/10;
		        }
		        if(sum>threshold) return false;
		        return true;
		    }
	}