# 代理模式

## 代理模式是什么（以下多翻自维基）

代理即一个包装器或代理对象，客户端通过调用它来访问幕后真实服务的对象。代理的使用可以轻易地转发至真实对象，并实现额外的逻辑。</br>

代理解决的问题：

- 控制到一个对象的访问
- 访问一个对象时添加额外的逻辑

## 代理在java中的实现

java中代理有三种实现方式：静态代理、动态代理及cglib代理。下面将分别对这三种代理实现分别进行描述、原理解析与代码实现。

### 静态代理

静态代理即代理对象与目标对象继承自同一个接口或继承自同一个父类，调用的时候通过调用代理对象的方法来调用目标对象

以艺人与经纪人为例，艺人是实际进行表演的角色，但在此之前会联系经纪人进行活动安排等。经纪人为代理对象，艺人为目标对象

```
interface Celebrity {
    void show();
}
```

```
class KrisWu implements Celebrity {
    @override
    void show() {
        System.out.println("skr");
    }
}
```

```

class KrisWuAgent implements Celebrity {
    private Celebrity celebrity;
    KrisWuAgent(Celebrity celebrity) {
        this.celebrity = celebrity;
    }
    void show() {
        System.out.println("交钱，20w");
        celebrity.show();
        System.out.println("活动结束");
    }
}

```

### 动态代理

jdk中提供了api动态生成代理对象。在类Proxy提供了newProxyInstance方法
```
public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h)
```
ClassLoader loader：目标对象的类加载器</br>
Class<?>[] interfaces：需要实现的接口数组</br>
InvocationHandler h：每一个代理实例的invocation handler都实现的接口。每一个代理实例都关联一个invocation handler。当代理实例的一个方法被调用时，方法调用被编码并分发到它的invocation handler。翻译多有不当，会在后面详细阐述，英文注释如下：
```
 * {@code InvocationHandler} is the interface implemented by
 * the <i>invocation handler</i> of a proxy instance.
 *
 * <p>Each proxy instance has an associated invocation handler.
 * When a method is invoked on a proxy instance, the method
 * invocation is encoded and dispatched to the {@code invoke}
 * method of its invocation handler.
```

依然以艺人为例。

```
public static void main(String[] args) {
        KrisWu krisWu = new KrisWu();
        Celebrity krisWuAgent1 = (Celebrity) Proxy.newProxyInstance(
                krisWu.getClass().getClassLoader(), krisWu.getClass().getInterfaces(), new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        System.out.println("交钱，20W");
                        return method.invoke(krisWu, args);
                    }
                });

        krisWuAgent1.show();

        Celebrity krisWuAgent2 = (Celebrity) Proxy.newProxyInstance(
                krisWu.getClass().getClassLoader(), krisWu.getClass().getInterfaces(), new InvocationHandler() {
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        System.out.println("这次免费");
                        return method.invoke(krisWu, args);
                    }
                });

        krisWuAgent2.show();
    }
```
输入结果如下：
```
交钱，20W
skr
这次免费
skr
```

动态代理依赖反射实现，原理实现将在反射笔记中以例子的形式进行解析。

#### 动态代理在设计模式中的使用

// todo

### cglib 代理

### 代理在spring中的使用

skr～skr～，终于写到了这里，想写下这个笔记，就是因为在学习spring bean的作用域时，对短作用域的bean注入到长作用域的bean（如会话作用域的bean注入到单例作用域的bean，实例化单例作用域bean的时候会话作用域的bean还未被创建，且我们期望每次会话注入该会话域的bean）使用代理来实现一知半解。以及spring aop编程同样使用了代理模式。这部分知识将在spring源码解析中进行阐述。










