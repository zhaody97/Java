# 异常处理机制

JVM在执行代码的过程中若发现了一个异常，则会new一个异常对象，并将代码整个执行过程完整的封装到异常中来表示错误报告设置完毕后将该异常抛出，若抛出的语句被try--catch包围，则JVM会检查catch中是否有匹配该异常对象，若有则交给catch解决否则会将异常抛出当前方法外，由方法的调用者解决

   	作用：异常处理机制能让程序在错误发生时，按照代码的预先设定的异常处理程序，针对性地处理错误
	异常最先发生的地方，叫做异常抛出点	
	

### java.lang.Throwable类：Java 语言中所有错误或异常的父类

   两个子类：错误类（Error）+ 异常类（Exception）(微小的错误)

	Error：一般是指与虚拟机相关的问题，如系统崩溃，虚拟机错误，内存空间不足，方法调用栈溢出等
	Exception：由于网络故障，用户非法输入，文件损坏等导致的异常等细小的错误

### 2种处理异常的方式

?	1：使用try…catch…finally语句块处理它
?	2：在函数声明中使用throws 声明交给函数调用者(caller)去解决
?	  
throw：用在方法体中用作抛异常对象的关键字
?       当一个方法使用throw抛出异常时，就要在方法上使用throws声明该类异常的抛出以通知调用者解决，只有RuntimeException
?       及其子类异常使用throw抛出时，不强制要求使用throws声明，其他异常都是强制要求的，否则编译不通过

throws：用在方法的参数列表之后，后跟异常类型列表，仅仅进行该方法所出现异常进行抛给调用者，并不处理这些异常
	当调用一个含有throws声明异常抛出的方法时，编译器要求必须处理该异常，如果是main函数则抛给调用者（JVM）
	处理手段两种:
		1：使用try --catch 捕获并处理
		2：在当前方法继续使用throw是声明该异常抛出的方法



java异常捕获机制中的try--catch，try时用来包围可能出错的代码，catch用来捕获try中代码抛出的错误并解决

```java
try{
	出现异常的代码
   }
catch(异常类型 异常变量)
{
	处理的程序
}
 finally{
	}
```

   注意:
     1：Exception ：一切异常都可以处理，在最后一个catch中捕获Exception，避免因未捕获异常导致程序中断
     2: 当多个catch捕获不同异常时，这些异常间存在继承关系，那么子类异常要在上，先行捕获，父类异常在下，从上到下开始匹配


fianlly 块：
  定义在异常捕获机制的最后，可以直接跟在try块之后或者最后一个catch块之后，finally块中的代码一定执行，
  无论try块中的代码是否抛出异常。所以，通常把释放资源等操作放在finally中	

```java
	finally {
		   //finally...必须执行			
		} 
```

 注意：

?	  1：try块后面可以跟对个catch块，匹配顺序是从上向下，编写捕获的类型时必须同级或者逐渐变大不能减小,
?	  	 并且执行完一个catch后跳出，执行完继续返回继续执行代码
?	   2：finally主要做一些清理工作，如流的关闭，数据库连接的关闭等

?	   3：finally与return见面return让步 

?    	   4：在catch最后加上Exception异常，做终极处理

?	
继承父类，重写父类一个含有throws异常抛出声明的方法时，子类该方法的throws的重写原则	  
?	1：不在抛出任何异常
?	2：可仅抛出父类方法中抛出的部分异常
?	3：允许抛出父类方法抛出异常的子类型异常
?	4：不能抛出比父类更大的异常

### 异常分为：

### 	编译时异常（强制性异常，CheckedException） + 运行时异常（RuntimeException，uncheckedException)

```java
常见的RuntimeException：
	1：NULLpointerException :空指针异常
	2：ArrayIndexoutOfBoundsException :下标越界
	3：IllegalArgumentException :传递不合法的参数
	4：ClassCastException :对象强制转换时，不是实例的子类
	5：ClassNotFoundException ：指定的类找不到
```

### 异常的方法：

?	1：printStackTrace():追踪异常抛出点，方便我们处理异常
?	2：getMassage（）:显示出错原因

### 自定义异常：通常用来描述业务逻辑上的出现的问题，命名见名知意

?	1：一般继承Exception
?	2：重写构造方法，加版本号

### 异常处理的原则：

  在方法之间互调的情况下：一般进行throws处理，如果再抛见到main方法时必须try--catch，若果抛出main方法，JVM会将程序中断  打印异常
		
		