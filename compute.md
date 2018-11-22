
```java
package com.example.myf.compute;

import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.lang.*;
import java.util.ArrayList;
import java.util.*;

public class InfixInToDuffix {

    //使用集合定义好符号的运算优先级别
    private static final Map<Character,Integer>basic =new HashMap<Character, Integer>();
    static {
        basic.put('-',1);
        basic.put('+', 1);
        basic.put('*', 2);
        basic.put('/', 2);
        basic.put('(', 0);//在运算中  （）的优先级最高，但是此处因程序中需要 故设置为0
    }


    //将中缀表达式转换为后缀表达式
    public String toSuffix(StringBuilder infix){
        List<String> queue = new ArrayList<String>();                                    //定义队列  用于存储 数字  以及最后的  后缀表达式
        List<Character> stack = new ArrayList<Character>();                             //定义栈    用于存储  运算符  最后stack中会被 弹空

        char[] charArr = infix.substring(0,infix.length()).trim().toCharArray();                                    //字符数组  用于拆分数字或符号
        String standard = "*/+-()";                                                        //判定标准 将表达式中会出现的运算符写出来
        char ch = '&';                                                                    //在循环中用来保存 字符数组的当前循环变量的  这里仅仅是初始化一个值  没有意义
        int len = 0;                                                                    //用于记录字符长度 【例如100*2,则记录的len为3 到时候截取字符串的前三位就是数字】
        for (int i = 0; i < charArr.length; i++) {                                        //开始迭代

            ch = charArr[i];                                                            //保存当前迭代变量
            if(Character.isDigit(ch)) {                                                    //如果当前变量为 数字
                len++;
            }else if(ch == '.'){                                                        //如果当前变量为  .  会出现在小数里面
                len++;
            }else if(standard.indexOf(ch) != -1) {                                        //如果是上面标准中的 任意一个符号
                if(len > 0) {                                                            //长度也有
                    queue.add(String.valueOf(Arrays.copyOfRange(charArr, i - len, i)));    //说明符号之前的可以截取下来做数字
                    len = 0;                                                            //长度置空
                }
                if(ch == '(') {                                                            //如果是左括号
                    stack.add(ch);                                                        //将左括号 放入栈中
                    continue;                                                            //跳出本次循环  继续找下一个位置
                }
                if (!stack.isEmpty()) {                                                    //如果栈不为empty
                    int size = stack.size() - 1;                                        //获取栈的大小-1  即代表栈最后一个元素的下标
                    boolean flag = false;                                                //设置标志位
                    while (size >= 0 && ch == ')' && stack.get(size) != '(') {            //若当前ch为右括号，则 栈里元素从栈顶一直弹出，直到弹出到 左括号
                        queue.add(String.valueOf(stack.remove(size)));                    //注意此处条件：ch并未入栈，所以并未插入队列中；同样直到找到左括号的时候，循环结束了，所以左括号也不会放入队列中【也就是：后缀表达式中不会出现括号】
                        size--;                                                            //size-- 保证下标永远在栈最后一个元素【栈中概念：指针永远指在栈顶元素】
                        flag = true;                                                    //设置标志位为true
