- 打印 1 到最大的 n 位数

```java
public class TestMain {
    public static void print1toMax(int n){
        char[] number = new char[n+1];
        Arrays.fill(number,'0');
        number[n] = '\0';
        while(increment(number,n)){
            prinNumber(number,n);
        }
    }

    public static boolean increment(char[] number,int length){
        int takeOver = 0;//进位
        boolean isValid = true;
        for(int i = length-1;i>=0;i--){
            int sum = number[i]-'0'+takeOver;
            if(i == length-1) sum++; //都是从最后一位加1 剩下的其余的靠进位
            if(sum>=10){
                if(i == 0) isValid = false; //若第一位就进位 说明到达了最大数
                else{
                    sum -= 10;
                    number[i] = String.valueOf(sum).charAt(0);
                    takeOver = 1;
                }
            }else{
                number[i] = String.valueOf(sum).charAt(0);
                break;//未进位 此次结束
            }
        }
        return isValid;
    }

    public static void prinNumber(char[] number,int length){
        boolean isBegin0 = true;
        for(int i = 0;i<length;i++){
            if(isBegin0 && number[i] != '0'){//去除首位为0
                isBegin0 = false;
            }
            if(!isBegin0){
                System.out.print(number[i]);
            }
        }
        System.out.print("\t");
    }
}
```

> 使用排练组合+递归算出所有的情况

```java
import java.util.Arrays;

public class TestMain {
    public static void print1toMax(int n){
        char[] number = new char[n+1];
        Arrays.fill(number,'0');
        number[n] = '\0';
        for(int i = 0;i<10;i++){//每位0~9情况
            number[0] = String.valueOf(i).charAt(0);//首位
            increment(number,n,0);
        }
    }

    public static void increment(char[] number,int length,int index){//index 控制number 从index下标开始排列组合
        if(index == length-1) { //下标为末尾时 说明此次排列完毕
            prinNumber(number,length);
            return;
        }
        for(int i = 0;i<10;i++){
            number[index+1] = String.valueOf(i).charAt(0);
            increment(number,length,index+1);
        }

    }

    public static void prinNumber(char[] number,int length){
        boolean isBegin0 = true;
        for(int i = 0;i<length;i++){
            if(isBegin0 && number[i] != '0'){
                isBegin0 = false;
            }
            if(!isBegin0){
                System.out.print(number[i]);
            }
        }
        System.out.print("\t");
    }
}

```
