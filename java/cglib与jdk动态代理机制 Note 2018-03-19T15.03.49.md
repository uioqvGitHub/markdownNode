[TOC]



cglib与jdk动态代理机制 Note 2018-03-19T15.03.49
========================

### jdk动态代理
#### 涉及的类InvocationHandler,Proxy

1.  InvocationHandler 
用来编写具体的代理逻辑

	    public Object invoke(Object proxy, Method method,
	        Object[] args) throws Throwable;
	    //参数:1 代理对象, 2 代理的方法，3 代理方法的参数
  
2.  Proxy
用来生成代理对象

	    Proxy.newProxyInstance(getClass().getClassLoader(),
	    	obj.getClass().getInterfaces(), this);
	    //参数:1 类加载器, 2 代理接口的数组, 3 InvocationHandler类型的对象具体的代理逻辑

> 注意: 在调用代理方法时, method.invoke(arg1, arg2), 第一个参数应该传入真实对象

#### src

		package com.uioqv.test;
			    
		import java.lang.reflect.InvocationHandler;
		import java.lang.reflect.Method;
		import java.lang.reflect.Proxy;
		
		
		
		
		public class MyJDKProxy {
			static interface UserService {
				boolean login(String username, String password);
			}
			
			static class UserServiceImpl implements UserService {
			
				@Override
				public boolean login(String username, String password) {
					if("zhaoyun".equals(username) && "123456".equals(password)) {
						System.out.println("登录成功");
						return true;
					}
					System.out.println("登录失败");
					return false;
				}
			
			}
			
			
			
			static class MyInvocationHandler<T> implements InvocationHandler {
				T obj;
				@SuppressWarnings("unchecked")
				public T proxy(T obj) {
					this.obj = obj;
					obj = (T) Proxy.newProxyInstance(getClass().getClassLoader(),
					obj.getClass().getInterfaces(), this);
					return obj;
				}	
				
				@Override
				public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
					System.out.println("方法执行前");
					Object ret = method.invoke(obj, args);
					System.out.println("方法执行后");
					return ret;
				}
			
			
			}
			
			public static void main(String[] args) {
				UserService userService = new UserServiceImpl();
				System.out.println("-----------------------------------------目标对象执行");
				userService.login("zhaoyun", "123456");
				
				System.out.println("-----------------------------------------代理对象执行");
				MyInvocationHandler<UserService> myProxy = new MyInvocationHandler<>();
				userService = myProxy.proxy(userService);
				userService.login("zhaoyun", "123456");
			}
		
		}

### cglib动态代理
#### 涉及的类 MethodInterceptor, Enhancer

1.  MethodInterceptor 
用来编写具体的代理逻辑

	    public Object intercept(Object proxy, Method method,
	        Object[] args, MethodProxy methodProxy) throws Throwable;
	    //参数: 1 代理类对象, 2 Method 类型的代理方法对象, 3 代理方法参数, 4 MethodProxy 类型的代理方法对象

2.  Enhancer
用来生成代理对象

	    enhancer.setSuperclass(obj.getClass());
	    //参数:1 设置要代理的类, 设置代理的类, 继承只可以设置一个, 也可以设置要代理的接口, 可以设置多个
	    enhancer.setCallback(this);
	    //参数:1 设置回调的MethodInterceptor类型对象
	    enhancer.create();
	    //创建代理对象
> 注意: 使用cglib需要导入两个jar包cglib与asm, 若出现继承了一个接口的异常, 可能是导错asm的jar包, 应导入org.ow2的asm jar包

#### src

		package com.uioqv.test;
				
		import java.lang.reflect.Method;
		import net.sf.cglib.proxy.Enhancer;
		import net.sf.cglib.proxy.MethodInterceptor;
		import net.sf.cglib.proxy.MethodProxy;
		
		public class MyCglibProxy {
			static interface UserService {
				boolean login(String username, String password);
			}
		
			static class UserServiceImpl implements UserService {
		
				@Override
				public boolean login(String username, String password) {
					if("zhaoyun".equals(username) && "123456".equals(password)) {
						System.out.println("登录成功");
						return true;
					}
					System.out.println("登录失败");
					return false;
				}
				
			}
		
			static class MyMethodIntercepter<T> implements MethodInterceptor {
				T obj;
				
				@SuppressWarnings("unchecked")
				public T proxy(T obj) {
					this.obj = obj;
					Enhancer enhancer = new Enhancer();
					enhancer.setSuperclass(obj.getClass());
					enhancer.setCallback(this);
					return (T) enhancer.create();
				}
				
				
				@Override
				public Object intercept(Object proxy, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
					System.out.println(method.getClass());
					System.out.println(methodProxy.getClass());
					System.out.println("方法执行之前");
					Object obj = methodProxy.invoke(this.obj, args);
					
		            //Object obj = method.invoke(this.obj, args);
					System.out.println("方法执行之后");
					return obj;
				}
				
				
				
			} 
		
			public static void main(String[] args) {
				UserService userService = new UserServiceImpl();
				MyMethodIntercepter<UserService> proxy = new MyMethodIntercepter<UserService>();
				System.out.println("-------------------------------------代理前");
				userService.login("zhaoyun", "123456");
				userService = proxy.proxy(userService);
				System.out.println("-------------------------------------代理后");
				userService.login("zhaoyun", "123456");
		 	}
		
		}
