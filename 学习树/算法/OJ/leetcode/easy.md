1.Two Sum
Given an array of integers, return indices of the two numbers such that they add up to a specific target.  
You may assume that each input would have exactly one solution, and you may not use the same element twice.  
Example:  
Given nums = [2, 7, 11, 15], target = 9,  
Because nums[0] + nums[1] = 2 + 7 = 9,  
return [0, 1].  
```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        map<int,int> vessel;
        vector<int> result;
        int length = nums.size();
        for(int i = 0;i<length;i++){
            int temp = target-nums[i];
            if(vessel.count(temp)){
                result.push_back(vessel[temp]);
                result.push_back(i);
            }
            vessel[nums[i]]=i;
        }
        return result;
    }
};
//利用target-num[i] 去找。可以使用map,如java里的containsKey或者c++的find，其中要排除自身
```
2.Solution  
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)  </br>
Output: 7 -> 0 -> 8 </br>
Explanation: 342 + 465 = 807.  </br>
Explanation: 342 + 465 = 807.  </br>
//数字加和，其中数字为反转的链表形式
```java
    class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode l3 = null;   
        ListNode temp3 =null,temp1 = l1,temp2 = l2;
        int num,num1,num2;
        int forward = 0;
        boolean flag = true;
        while(temp1 != null && temp2 != null){
            int node = temp1.val+temp2.val+forward;
            num = node%10;
            if(flag){
                temp3 = new ListNode(num);
                l3 = temp3;
                flag = false;
            }else{
            temp3.next = new ListNode(num);
            temp3 = temp3.next;
            }
            temp1 = temp1.next;
            temp2 = temp2.next;
            forward = node/10;
        }
        //为考虑的情况
        if(temp1 == null && temp2 == null && forward>0){
            temp3.next = new ListNode(forward);
        }
        while(temp1 != null){
            int node = temp1.val+forward;
            num = node%10;
            temp3.next = new ListNode(num);
            temp3 = temp3.next;
            temp1 = temp1.next;
            forward = node/10;
            if(temp1 == null && forward>0){
                //temp3.val = forward;错误点 
                temp3.next = new ListNode(forward);
            }
        }
         while(temp2 != null){
            int node = temp2.val+forward;
            num = node%10;
            //temp3 = new ListNode(num);错误点
            temp3.next = new ListNode(num);
            temp3 = temp3.next;
            temp2 = temp2.next;
            forward = node/10;
             if(temp2 == null && forward>0){
                temp3.next = new ListNode(forward);
            }
        }
        return l3;
    }
}
 //先temp3 = temp3.next 此时temp3为null,在temp3 = new ListNode(num);则此时的temp3不在l3上
```
- 改进
```java
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode dummyHead = new ListNode(0);   
        ListNode curr = dummyHead,p = l1,q = l2;
        int carry = 0;
        while(p != null || q != null){
            int x = (p!=null) ? p.val : 0; //没有的数字设为0，解决不对成问题
            int y = (q!=null) ? q.val : 0;
            int sum = x+y+carry;
            carry = sum/10;
            curr.next = new ListNode(sum % 10);
            curr = curr.next;
            //p.next != null;错误点。导致超时
            if(p != null) p = p.next;
            if(q != null) q = q.next;  
        }
        if(carry>0){
            curr.next = new ListNode(carry);
        }
        return dummyHead.next;
    }
```

```java
    class Solution {
    public int reverse(int x) {    
        int result = 0;
        int temp = x;
        //不用>0 应该是!=0 就不需要区分是正还是负数
        while(temp != 0){
            int y = temp%10;
            temp = temp/10;
            //整数*整数 还是整数，此时*10溢出。应变成浮点型或者long long型
            if(result*10.0+y>Integer.MAX_VALUE || result*10.0+y < Integer.MIN_VALUE) return 0;
            result = result*10+y;       
        }
        return result;
    }
}
```
13. Roman to Integer </br>
Symbol       Value
I             1
V             5
X             10
L             50
C             100
D             500
M             1000 </br>
Input: "LVIII"
Output: 58
Explanation: L = 50, V= 5, III = 3.
```java
    class Solution {
    public int romanToInt(String s) {
        int len = s.length();
        //HashMap key和value 不能是原始数据类型
        HashMap<Character,Integer> kv = new HashMap<Character,Integer>();
        kv.put('I',1);
        kv.put('V',5);
        kv.put('X',10);
        kv.put('L',50);
        kv.put('C',100);
        kv.put('D',500);
        kv.put('M',1000);
        int sum = 0;
        for(int i = 0;i<len;i++){
            // String 根据下标或者字符 charAt()
            //从前往后遍历，需要考虑边界问题
            if((i != len-1 && kv.get(s.charAt(i))>=kv.get(s.charAt(i+1))) || i == len-1){
                sum = sum+kv.get(s.charAt(i));
            }else{
                sum = sum+kv.get(s.charAt(i+1))-kv.get(s.charAt(i));
                    i++;
            }
        }
        return sum;
    }
}
```
14. Longest Common Prefix
  
Input: ["flower","flow","flight"]  
Output: "fl"  

Input: ["dog","racecar","car"]  
Output: ""  
Explanation: There is no common prefix among the input strings.  
```java
    class Solution {
    public String longestCommonPrefix(String[] strs) {
        int len = strs.length;
        if(len == 0) return "";
        //String temp = strs[0]; 如果拿第一个作为temp 和所有的字符串数组比就不需要考虑len==1 也不需要考虑minlen == 0
        if(len == 1) return strs[0];
        int minlen = strs[0].length();
        boolean flag = true;
        //简易写法
       /* int min = Integer.MAX_VALUE;
        for(String str: strs)
            min = Math.min(str.length(), min);*/
        for(int i = 1;i<len;i++){
            if(minlen>strs[i].length())
                minlen = strs[i].length();
        }
        //
        if(minlen == 0) return "";
        int lastindex = 0;
        //如果使用StringBuilder 相同的存起来，就不需要考虑下标问题，也不需要太多的substring 耗时
        for(int j = 0;j<=minlen;j++) {
            if(!flag) break;
            lastindex = j;
            for(int i = 1;i<len;i++){
                if(!strs[0].substring(0,j).equals(strs[i].substring(0,j))){
                    lastindex--;
                    flag = false;
                    break;
                }
            }
        }
        return strs[0].substring(0,lastindex);
    }
}
```
- 改进
```java 
//水平对比
public String longestCommonPrefix(String[] strs) {
        int len = strs.length; 
        if(len == 0) return "";
        //第一个不断缩小对比范围，其它不动
        String prex = strs[0];
        for(int i = 1;i<len;i++){
            while(strs[i].indexOf(prex) != 0){//若全部匹配，则开始下标为0，不匹配返回-1
                prex = prex.substring(0,prex.length()-1);
                if(prex.isEmpty()) return "";
            }
        }
        //其中，只有一个元素时，返回本身
        return prex;
    }
//垂直对比
public String longestCommonPrefix(String[] strs) {
        int len = strs.length; 
        if(len == 0) return "";
        for(int i = 0;i<strs[0].length();i++){
            char ch = strs[0].charAt(i);
            for(int j = 1;j<len;j++){
                //就不需要算出每个字符串的长度
                if(i == strs[j].length() || ch != strs[j].charAt(i))
                    return strs[0].substring(0,i);
            }
        }
        //只有一个元素时，返回本身
        return strs[0];
    }

```
20. Valid Parentheses </BR>
Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.
An input string is valid if:
Open brackets must be closed by the same type of brackets.
Open brackets must be closed in the correct order.</br>
Example 2:
Input: "()[]{}"
Output: true </br>

Example 3:
Input: "(]"
Output: false </br>
Example 4:
Input: "([)]"
Output: false </br>
Example 5:
Input: "{[]}"
Output: true
```java
    public boolean isValid(String s) {
        HashMap<Character,Character> kv = new HashMap<Character,Character>();
        kv.put('(',')');
        kv.put('{','}');
        kv.put('[',']');
        HashSet<Character> set = new HashSet<Character>();
        set.add(')');
        set.add('}');
        set.add(']');
        int len = s.length();
        int index = -1;
        if(len == 0) return true;
        char ch[] = new char[s.length()];
        for(int i = 0;i<len;i++){
            char c = s.charAt(i);
            if(index < 0) {
                if(set.contains(c)) return false;
                ch[++index] = c;
                continue;
            }
            if(kv.get(ch[index]) == c) index--;
            else  {
                if(set.contains(c)) return false;
                ch[++index] = c; 
                
            }
        }
        if(index < 0 ) return true;
        else return false;
    }
    //优雅的代码
    public boolean isValid(String s) {
        Stack<Character> stack = new Stack<>();
        for(char temp: s.toCharArray()) {
            if(temp == '{' || temp == '(' || temp == '[') {
                stack.push(temp);
            }else {
                if(stack.isEmpty())
                    return false;
                char prev = stack.pop();
                if((temp == '}' && prev != '{') || (temp == ')' && prev != '(') || (temp == ']' && prev != '['))
                    return false;
            }
        }
        return stack.isEmpty();
    }
    //
    public boolean isValid(String s) {
	Stack<Character> stack = new Stack<Character>();
	for (char c : s.toCharArray()) {
		if (c == '(')
			stack.push(')');
		else if (c == '{')
			stack.push('}');
		else if (c == '[')
			stack.push(']');
		else if (stack.isEmpty() || stack.pop() != c)
			return false;
	}
	return stack.isEmpty();
    }
```

88. Merge Sorted Array
Given two sorted integer arrays nums1 and nums2, merge nums2 into nums1 as one sorted array.

Note:

The number of elements initialized in nums1 and nums2 are m and n respectively.
You may assume that nums1 has enough space (size that is greater or equal to m + n) to hold additional elements from nums2.</br>

Input:
nums1 = [1,2,3,0,0,0], m = 3
nums2 = [2,5,6],       n = 3

Output: [1,2,2,3,5,6]
```java
    public void merge(int[] nums1, int m, int[] nums2, int n) {
        //从后往前比较，把nums1后面剩余的空间作为存放点
        int index1 = m-1,index2 = n-1,pend = m+n-1;
        while(index1>=0 || index2>=0){
            int x = (index1>=0) ? nums1[index1] : Integer.MIN_VALUE;
            int y = (index2>=0) ? nums2[index2] : Integer.MIN_VALUE;
            if(x>y) {
                nums1[pend--] = x;
                index1--;
            }
            else {
                nums1[pend--] = y;
                index2--;
            }
        }
    }
```

67.Add Binary
数字字符转成纯数字 ch - '0';
字符串转成数字 Integer.valueOf() 或者 Integer.parseInt();
低精度转高精度不会编译错，低精度转高精度需要强制转换