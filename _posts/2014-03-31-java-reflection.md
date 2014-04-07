---
layout: post
title: '[黑马程序员]Java 中的反射'
---
利用反射可以分析类的结构，在运行时查看并操作对象的成员，动态地操作数组，使 Java 具有动态操作代码的能力，在系统工具和框架中大量使用。

### Class 类

在程序运行期间，虚拟机为每一个类维护了所需的类型信息，可以通过对应的 Class 类访问这些信息。有三种获得类对象的方法：

{% highlight java linenos %}
// 调用对象的 getClass() 方法
Date date;
Class cl = date.getClass();

// 通过静态方法 forName 获得类名对应的 Class 对象
String className = "java.util.Date";
Class cl = Class.forName(className);

// 通过 T.class 获得对应的类对象
Class cl1 = Date.class;
Class cl2 = int.class;
Class cl3 = Double[].class;
{% endhighlight %}

虚拟机为每个类型维护一个 Class 对象，因此可以利用 == 运算符实现两个类对象的比较操作。

获得 Class 对象后，可以调用 newInstance 方法使用默认构造器创建一个类的实例（没有默认构造器则会抛出一个异常）。将 forName 和 newInstance 配合使用可以根据存储的字符串创建一个类的对象：

{% highlight java linenos %}
String className = "java.util.Data";
Object obj = Class.forName(className).newInstance();
{% endhighlight %}

### 利用反射查看类的结构

{% highlight java linenos %}
// 查看一个类的公有方法并打印出方法签名
public class ClassChecker {
    public static void printMethods(Class cl) {
        Method[] methods = cl.getMethods();
        for (Method m : methods) {
            Class retType = m.getReturnType();
            String name = m.getName();
            String modifiers = Modifier.toString(m.getModifiers());
            if (modifiers.length() > 0) {
                System.out.print(modifiers + " ");
            }
            System.out.print(retType.getName() + " " + name + "(");
            
            Class[] paramTypes = m.getParameterTypes();
            for (int i = 0; i < paramTypes.length; i++) {
                if (j > 0) {
                    System.out.print(", ");
                }
                System.out.print(paramTypes[i].getName());
            }
            System.out.println(");");
        }
    }
}
{% endhighlight %}

上面这段代码利用反射打印出一个类的公有方法，用类似的方法也可以查看一个类的域和构造器等信息。

### 在运行时使用反射分析对象

使用反射不仅可以查看类的结构，也可以查看甚至更改一个对象的实际内容。下面这段代码获取了对象的实例域并打印它的值：

{% highlight java linenos %}
class ObjectReflect {
    public static void main(String[] args) throws Exception {
        Player player = new Player("Beck", 23);
        Field[] fields = Player.class.getDeclaredFields();
        AccessibleObject.setAccessible(fields, true);
        for (Field field : fields) {
            String name = field.getName();
            Object val = field.get(player);
            System.out.println(name + " = " + val);
        }
    }
}

class Player {
    private String name;
    private int age;
    
    public Player(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
{% endhighlight %}

### 使用反射操作数组

{% highlight java linenos %}
Integer[] intArr = {1, 2, 3, 4, 5};

// 获得数组的类对象
Class cl = intArr.getClass();

// 判断该类对象是否表示一个数组类型
cl.isArray()

// 获得数组元素的类型
Class componentType = cl.getComponentType();

// 获得数组长度
int length = Array.getLength(intArr);

// 获取数组的元素
int e = Array.get(intArr, 3);

// 创建一个新数组
Array.newInstance(componentType, length + 10)
{% endhighlight %}

### “方法指针”

很多时候需要对数组中每一个元素进行相同的操作，利用“方法指针”的概念可以将这一过程抽象成一个方法：

{% highlight java linenos %}
public class MethodPointerTest {
    public static void processIntArray(int[] arr, Method f) throws Exception {
        for (int i = 0; i < arr.length; i++) {
            arr[i] = (Integer) f.invoke(null, arr[i]);
        }
    }
    
    public static int square(int x) {
        return x * x;
    }
    
    public static void main(String[] args) throws Exception {
        int[] arr = {1, 2, 3, 4, 5};
        System.out.println(Arrays.toString(arr));
        Method method = MethdPointerTest.class.getMethod("square", int.class);
        processIntArray(arr, method);
        System.out.println(Arrays.toString(arr));
    }
}
{% endhighlight %}