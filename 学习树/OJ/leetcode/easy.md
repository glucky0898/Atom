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
Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8
Explanation: 342 + 465 = 807.
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
            int x = (p!=null) ? p.val : 0;
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