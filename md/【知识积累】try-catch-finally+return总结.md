**一、前言**

对于找Java相关工作的读者而言，在笔试中肯定免不了遇到try-catch-finally +
return的题型，需要面试这清楚返回值，这也是这篇博文产生的由来。本文将从字节码层面来解释为何不同的写法对应的返回结果不相同。当然在实际开发环境下不会这么抠知识点，但是掌握这种分析方法也未尝不可。对于没有此需求的读者大可略过此篇博文。

**二、测试**

**** 首先进行一个小小的测试，有如下代码

    
    
    package com.hust.grid.leesf.main;
    
    /**
     * Created by LEESF on 2016/9/11.
     */
    public class FinallyReturnTest {
        public static void main(String[] args) {
            System.out.println(test1());
            System.out.println(test2());
            System.out.println(test3());
            System.out.println(test4());
            System.out.println(test5());
            System.out.println(test6());
        }
    
        /**
         * 有异常抛出情况、try、catch、finally均有return语句
         * @return
         */
        public static int test1() {
            int x  = 0;
            try {
                int i = 0;
                int j = 1;
                int k = j / i;
                return x;
            } catch (Exception e) {
                x = 1;
                return x;
            } finally {
                x = 2;
                return x;
            }
        }
    
        /**
         * 无异常抛出情况、try、catch、finally均有return语句
         * @return
         */
        public static int test2() {
            int x = 0;
            try {
                int i = 0;
                int j = 1;
                return x;
            } catch (Exception e) {
                x = 1;
                return x;
            } finally {
                x = 2;
                return x;
            }
        }
    
        /**
         * 有异常抛出，try、catch均有return语句
         * @return
         */
        public static int test3() {
            int x = 0;
            try {
                int i = 0;
                int j = 1;
                int k = j / i;
                return x;
            } catch (Exception e) {
                x = 1;
                return x;
            } finally {
                x = 2;
            }
        }
        /**
         * 无异常抛出，try、catch均有return语句
         * @return
         */
        public static int test4() {
            int x = 0;
            try {
                int i = 0;
                int j = 1;
                return x;
            } catch (Exception e) {
                x = 1;
                return x;
            } finally {
                x = 2;
            }
        }
        /**
         * 无异常抛出，try、方法最后均有return语句
         * @return
         */
        public static int test5() {
            int x = 0;
            try {
                int i = 0;
                int j = 1;
                return x;
            } catch (Exception e) {
                x = 1;
            } finally {
                x = 2;
            }
    
            return x;
        }
    
        /**
         * 有异常抛出，try、方法最后均有return语句
         * @return
         */
        public static int test6() {
            int x = 0;
            try {
                int i = 0;
                int j = 1;
                int k = j / i;
                return x;
            } catch (Exception e) {
                x = 1;
            } finally {
                x = 2;
            }
            return x;
        }
    }

有兴趣的读者可以自行测试一下，看能否很完整无误的写出正确答案。以上程序的运行结果如下

    
    
    2
    2
    1
    0
    0
    2

**三、分析**

针对上面的结果进行如下的分析。

使用javap -c FinallyReturnTest.class命令查看FinallyReturnTest的字节码，具体如下。

    
    
    Compiled from "FinallyReturnTest.java"
     public class com.hust.grid.leesf.main.FinallyReturnTest {
      public com.hust.grid.leesf.main.FinallyReturnTest();
        Code:
           0: aload_0
           1: invokespecial #1                  // Method java/lang/Object."<init>":
    ()V
           4: return
    
      public static void main(java.lang.String[]);
        Code:
           0: getstatic     #2                  // Field java/lang/System.out:Ljava/
    io/PrintStream;
           3: invokestatic  #3                  // Method test1:()I
           6: invokevirtual #4                  // Method java/io/PrintStream.printl
    n:(I)V
           9: getstatic     #2                  // Field java/lang/System.out:Ljava/
    io/PrintStream;
          12: invokestatic  #5                  // Method test2:()I
          15: invokevirtual #4                  // Method java/io/PrintStream.printl
    n:(I)V
          18: getstatic     #2                  // Field java/lang/System.out:Ljava/
    io/PrintStream;
          21: invokestatic  #6                  // Method test3:()I
          24: invokevirtual #4                  // Method java/io/PrintStream.printl
    n:(I)V
          27: getstatic     #2                  // Field java/lang/System.out:Ljava/
    io/PrintStream;
          30: invokestatic  #7                  // Method test4:()I
          33: invokevirtual #4                  // Method java/io/PrintStream.printl
    n:(I)V
          36: getstatic     #2                  // Field java/lang/System.out:Ljava/
    io/PrintStream;
          39: invokestatic  #8                  // Method test5:()I
          42: invokevirtual #4                  // Method java/io/PrintStream.printl
    n:(I)V
          45: getstatic     #2                  // Field java/lang/System.out:Ljava/
    io/PrintStream;
          48: invokestatic  #9                  // Method test6:()I
          51: invokevirtual #4                  // Method java/io/PrintStream.printl
    n:(I)V
          54: return
    
      public static int test1();
        Code:
           0: iconst_0
           1: istore_0
           2: iconst_0
           3: istore_1
           4: iconst_1
           5: istore_2
           6: iload_2
           7: iload_1
           8: idiv
           9: istore_3
          10: iload_0
          11: istore        4
          13: iconst_2
          14: istore_0
          15: iload_0
          16: ireturn
          17: astore_1
          18: iconst_1
          19: istore_0
          20: iload_0
          21: istore_2
          22: iconst_2
          23: istore_0
          24: iload_0
          25: ireturn
          26: astore        5
          28: iconst_2
          29: istore_0
          30: iload_0
          31: ireturn
        Exception table:
           from    to  target type
               2    13    17   Class java/lang/Exception
               2    13    26   any
              17    22    26   any
              26    28    26   any
    
      public static int test2();
        Code:
           0: iconst_0
           1: istore_0
           2: iconst_0
           3: istore_1
           4: iconst_1
           5: istore_2
           6: iload_0
           7: istore_3
           8: iconst_2
           9: istore_0
          10: iload_0
          11: ireturn
          12: astore_1
          13: iconst_1
          14: istore_0
          15: iload_0
          16: istore_2
          17: iconst_2
          18: istore_0
          19: iload_0
          20: ireturn
          21: astore        4
          23: iconst_2
          24: istore_0
          25: iload_0
          26: ireturn
        Exception table:
           from    to  target type
               2     8    12   Class java/lang/Exception
               2     8    21   any
              12    17    21   any
              21    23    21   any
    
      public static int test3();
        Code:
           0: iconst_0
           1: istore_0
           2: iconst_0
           3: istore_1
           4: iconst_1
           5: istore_2
           6: iload_2
           7: iload_1
           8: idiv
           9: istore_3
          10: iload_0
          11: istore        4
          13: iconst_2
          14: istore_0
          15: iload         4
          17: ireturn
          18: astore_1
          19: iconst_1
          20: istore_0
          21: iload_0
          22: istore_2
          23: iconst_2
          24: istore_0
          25: iload_2
          26: ireturn
          27: astore        5
          29: iconst_2
          30: istore_0
          31: aload         5
          33: athrow
        Exception table:
           from    to  target type
               2    13    18   Class java/lang/Exception
               2    13    27   any
              18    23    27   any
              27    29    27   any
    
      public static int test4();
        Code:
           0: iconst_0
           1: istore_0
           2: iconst_0
           3: istore_1
           4: iconst_1
           5: istore_2
           6: iload_0
           7: istore_3
           8: iconst_2
           9: istore_0
          10: iload_3
          11: ireturn
          12: astore_1
          13: iconst_1
          14: istore_0
          15: iload_0
          16: istore_2
          17: iconst_2
          18: istore_0
          19: iload_2
          20: ireturn
          21: astore        4
          23: iconst_2
          24: istore_0
          25: aload         4
          27: athrow
        Exception table:
           from    to  target type
               2     8    12   Class java/lang/Exception
               2     8    21   any
              12    17    21   any
              21    23    21   any
    
      public static int test5();
        Code:
           0: iconst_0
           1: istore_0
           2: iconst_0
           3: istore_1
           4: iconst_1
           5: istore_2
           6: iload_0
           7: istore_3
           8: iconst_2
           9: istore_0
          10: iload_3
          11: ireturn
          12: astore_1
          13: iconst_1
          14: istore_0
          15: iconst_2
          16: istore_0
          17: goto          27
          20: astore        4
          22: iconst_2
          23: istore_0
          24: aload         4
          26: athrow
          27: iload_0
          28: ireturn
        Exception table:
           from    to  target type
               2     8    12   Class java/lang/Exception
               2     8    20   any
              12    15    20   any
              20    22    20   any
    
      public static int test6();
        Code:
           0: iconst_0
           1: istore_0
           2: iconst_0
           3: istore_1
           4: iconst_1
           5: istore_2
           6: iload_2
           7: iload_1
           8: idiv
           9: istore_3
          10: iload_0
          11: istore        4
          13: iconst_2
          14: istore_0
          15: iload         4
          17: ireturn
          18: astore_1
          19: iconst_1
          20: istore_0
          21: iconst_2
          22: istore_0
          23: goto          33
          26: astore        5
          28: iconst_2
          29: istore_0
          30: aload         5
          32: athrow
          33: iload_0
          34: ireturn
        Exception table:
           from    to  target type
               2    13    18   Class java/lang/Exception
               2    13    26   any
              18    21    26   any
              26    28    26   any
    }

针对上面的字节码，笔者并不打算从头开始分析，我们着重关心的几个test方法，有兴趣的读者可以参考博主之前写过JVM相关的文章来理解其他内容。传送门：[点这里](http://www.cnblogs.com/leesf456/p/5263764.html)

3.1 test1方法分析

    
    
    public static int test1();
        Code:
           0: iconst_0                // 常量0
           1: istore_0                // 将常量0存在局部变量表的第一个slot，值得注意的是该方法为静态方法，所以局部变量表的第一个slot并非存储的this引用
           2: iconst_0                // 常量0
           3: istore_1                // 将常量0存在局部变量表的第二个slot
           4: iconst_1                // 常量1
           5: istore_2                // 将常量1存在局部变量表的第三个slot
           6: iload_2                // 将局部变量表的第三个slot的值(1)放到栈顶
           7: iload_1                // 将局部变量表的第二个slot的值(0)放到栈顶
           8: idiv                    // 栈顶元素与次栈顶元素相除，次栈顶元素为除数
           9: istore_3                // 将相除的结果保存在局部变量表的第四个slot
          10: iload_0                // 将局部变量表的第一个slot的值(0)放到栈顶
          11: istore        4        // 将栈顶元素放入局部变量表的第五个slot
          13: iconst_2                // 常量2
          14: istore_0                // 将常量2存在局部变量表的第一个slot
          15: iload_0                // 将局部变量表的第一个slot的值(2)放到栈顶
          16: ireturn                // 将栈顶的元素返回(返回2)
          17: astore_1                // 给catch中定义的Exception e赋值，存在第二个slot中
          18: iconst_1                // 常量1
          19: istore_0                // 将常量1存在局部变量表的第一个slot
          20: iload_0                // 将局部变量表的第一个slot的值(1)放到栈顶
          21: istore_2                // 将栈顶元素(1)存在局部变量表的第三个slot中
          22: iconst_2                // 常量2
          23: istore_0                // 将常量2保存在局部变量表的第一个slot中
          24: iload_0                // 将局部变量表的第一个slot的值(2)放到栈顶
          25: ireturn                // 将栈顶的元素返回(返回2)
          26: astore        5        // 给非Exception类型的异常赋值，存在局部变量表的第六个slot中
          28: iconst_2                // 常量2
          29: istore_0                // 将常量1存在局部变量表的第一个slot
          30: iload_0                // 将局部变量表的第一个slot的值(2)放到栈顶
          31: ireturn                // 将站定的元素返回(返回2)
        Exception table:            // 异常表
           from    to  target type
               2    13    17   Class java/lang/Exception    // 当2-13行(对应try语句块)发生属于Exception异常时，则转到17行处理(对应catch语句块)
               2    13    26   any                            // 当2-13行(对应try语句块)发生属于非Exception异常时，则转到26行处理(对应finally语句块)
              17    22    26   any                            // 当17-22行(对应catch语句块)发生异常时，则转到26行处理(对应finally语句块)
              26    28    26   any                            // 当26-28行(对应finally语句块)发生异常时，则转到26行处理(对应finally语句块)

如上图所示，笔者对test1方法的每一条指令进行了详细的说明，可以看到，无论发生异常与否，最后返回的都是2（16行、25行、31行）。对于test2 ->
test6方法，均可以依葫芦画瓢进行分析，然后得到返回值。

**四、分析总结**

无论发生异常与否，finally语句块一定在return之前执行。

以下结论基于如下前提：try的return语句在发生异常语句之后、方法有返回值。

① 当finally语句块中存在return语句时，此时无论是否发生异常，都以该return语句为准，其他的return语句将不起作用。

②
当发生异常时，try存在return语句，catch存在return语句，以catch中的return语句为准，此时在finally语句块中的修改将不起作用。

③
当发生异常时，try存在return语句，catch不存在return语句，方法在最后存在return语句，以该return语句为准，此时在catch和finally的修改会起作用。

④
不发生异常时，try存在return语句，不管是catch存在return语句还是方法最后存在return语句，都以try中的return语句为准，此时在catch、finally中的修改将不起作用。

**五、总结**

在平时要注意知识点的积累，清楚细小知识点背后的原理。也谢谢各位园友的观看~

