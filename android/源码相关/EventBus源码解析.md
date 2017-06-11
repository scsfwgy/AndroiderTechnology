#### register
1. **双重锁获取EventBus实例**：`EventBus.getDefault().register(this);`注册过程使用了双重判断的方式，防止并发的问题，还能极大的提高效率。

	/** Convenience singleton for apps using a process-wide EventBus instance. */  
	   public static EventBus getDefault() {  
	       if (defaultInstance == null) {  
	           synchronized (EventBus.class) {  
	               if (defaultInstance == null) {  
	                   defaultInstance = new EventBus();  
	               }  
	           }  
	       }  
	       return defaultInstance;  
	   }  	

2. **注册过程**：最终通过重载执行该方法`private synchronized void register(Object subscriber, String methodName, boolean sticky, int priority)`方法， 完成注册过程。

		private synchronized void register(Object subscriber, String methodName, boolean sticky, int priority) {  
				        List<SubscriberMethod> subscriberMethods = subscriberMethodFinder.findSubscriberMethods(subscriber.getClass(),  
				                methodName);  
				        for (SubscriberMethod subscriberMethod : subscriberMethods) {  
				            subscribe(subscriber, subscriberMethod, sticky, priority);  
				        }  
				}			

3. **获取注册类中所有的要接收消息的方法**：`subscriberMethodFinder.findSubscriberMethods(subscriber.getClass(),methodName);`该方法去注册的类中去根据methodName（即@Subscribe）去匹配，匹配成功的封装成SubscriberMethod，最后返回一个List。其中返回值的SubscriberMethod，`new SubscriberMethod(method, threadMode, eventType)`可以看出返回的包含该方法的方法名、线程模式、事件类型（也就是onEvent(Object obj)中的obj）.

		List<SubscriberMethod> findSubscriberMethods(Class<?> subscriberClass, String eventMethodName) {  
		        String key = subscriberClass.getName() + '.' + eventMethodName;  
		        List<SubscriberMethod> subscriberMethods;  
		        synchronized (methodCache) {  
		            subscriberMethods = methodCache.get(key);  
		        }  
		        if (subscriberMethods != null) {  
		            return subscriberMethods;  
		        }  
		        subscriberMethods = new ArrayList<SubscriberMethod>();  
		        Class<?> clazz = subscriberClass;  
		        HashSet<String> eventTypesFound = new HashSet<String>();  
		        StringBuilder methodKeyBuilder = new StringBuilder();  
		        while (clazz != null) {  
		            String name = clazz.getName();  
		            if (name.startsWith("java.") || name.startsWith("javax.") || name.startsWith("android.")) {  
		                // Skip system classes, this just degrades performance  
		                break;  
		            }  
		  
		            // Starting with EventBus 2.2 we enforced methods to be public (might change with annotations again)  
		            Method[] methods = clazz.getMethods();  
		            for (Method method : methods) {  
		                String methodName = method.getName();  
		                if (methodName.startsWith(eventMethodName)) {  
		                    int modifiers = method.getModifiers();  
		                    if ((modifiers & Modifier.PUBLIC) != 0 && (modifiers & MODIFIERS_IGNORE) == 0) {  
		                        Class<?>[] parameterTypes = method.getParameterTypes();  
		                        if (parameterTypes.length == 1) {  
		                            String modifierString = methodName.substring(eventMethodName.length());  
		                            ThreadMode threadMode;  
		                            if (modifierString.length() == 0) {  
		                                threadMode = ThreadMode.PostThread;  
		                            } else if (modifierString.equals("MainThread")) {  
		                                threadMode = ThreadMode.MainThread;  
		                            } else if (modifierString.equals("BackgroundThread")) {  
		                                threadMode = ThreadMode.BackgroundThread;  
		                            } else if (modifierString.equals("Async")) {  
		                                threadMode = ThreadMode.Async;  
		                            } else {  
		                                if (skipMethodVerificationForClasses.containsKey(clazz)) {  
		                                    continue;  
		                                } else {  
		                                    throw new EventBusException("Illegal onEvent method, check for typos: " + method);  
		                                }  
		                            }  
		                            Class<?> eventType = parameterTypes[0];  
		                            methodKeyBuilder.setLength(0);  
		                            methodKeyBuilder.append(methodName);  
		                            methodKeyBuilder.append('>').append(eventType.getName());  
		                            String methodKey = methodKeyBuilder.toString();  
		                            if (eventTypesFound.add(methodKey)) {  
		                                // Only add if not already found in a sub class  
		                                subscriberMethods.add(new SubscriberMethod(method, threadMode, eventType));  
		                            }  
		                        }  
		                    } else if (!skipMethodVerificationForClasses.containsKey(clazz)) {  
		                        Log.d(EventBus.TAG, "Skipping method (not public, static or abstract): " + clazz + "."  
		                                + methodName);  
		                    }  
		                }  
		            }  
		            clazz = clazz.getSuperclass();  
		        }  
		        if (subscriberMethods.isEmpty()) {  
		            throw new EventBusException("Subscriber " + subscriberClass + " has no public methods called "  
		                    + eventMethodName);  
		        } else {  
		            synchronized (methodCache) {  
		                methodCache.put(key, subscriberMethods);  
		            }  
		            return subscriberMethods;  
		        }  
		    } 
4. 根据eventType（也就是onEvent(Object obj)中的obj）对消息进行分类，方便后期的发送消息：` subscribe(subscriber, subscriberMethod, sticky, priority);  `
		
		// Must be called in synchronized block  
		   private void subscribe(Object subscriber, SubscriberMethod subscriberMethod, boolean sticky, int priority) {  
		       subscribed = true;  
		       Class<?> eventType = subscriberMethod.eventType;  
		       CopyOnWriteArrayList<Subscription> subscriptions = subscriptionsByEventType.get(eventType);  
		       Subscription newSubscription = new Subscription(subscriber, subscriberMethod, priority);  
		       if (subscriptions == null) {  
		           subscriptions = new CopyOnWriteArrayList<Subscription>();  
		           subscriptionsByEventType.put(eventType, subscriptions);  
		       } else {  
		           for (Subscription subscription : subscriptions) {  
		               if (subscription.equals(newSubscription)) {  
		                   throw new EventBusException("Subscriber " + subscriber.getClass() + " already registered to event "  
		                           + eventType);  
		               }  
		           }  
		       }  
		  
		       // Starting with EventBus 2.2 we enforced methods to be public (might change with annotations again)  
		       // subscriberMethod.method.setAccessible(true);  
		  
		       int size = subscriptions.size();  
		       for (int i = 0; i <= size; i++) {  
		           if (i == size || newSubscription.priority > subscriptions.get(i).priority) {  
		               subscriptions.add(i, newSubscription);  
		               break;  
		           }  
		       }  
		  
		       List<Class<?>> subscribedEvents = typesBySubscriber.get(subscriber);  
		       if (subscribedEvents == null) {  
		           subscribedEvents = new ArrayList<Class<?>>();  
		           typesBySubscriber.put(subscriber, subscribedEvents);  
		       }  
		       subscribedEvents.add(eventType);  
		  
		       if (sticky) {  
		           Object stickyEvent;  
		           synchronized (stickyEvents) {  
		               stickyEvent = stickyEvents.get(eventType);  
		           }  
		           if (stickyEvent != null) {  
		               // If the subscriber is trying to abort the event, it will fail (event is not tracked in posting state)  
		               // --> Strange corner case, which we don't take care of here.  
		               postToSubscription(newSubscription, stickyEvent, Looper.getMainLooper() == Looper.myLooper());  
		           }  
		       }  
		   }  

5. 总结：首先采用双重锁方式获取EventBus单例，然后去执行注册过程。注册过程的第一步就是去注册的类中遍历所有方法找到所有onEvent(Object obj)方法（也就是@Subscriber)方法，将方法存到一个List中，包括该方法的各种信息：EventType(onEvent(Object obj)的obj)、线程模式、方法名等。第二步：根据EventType将onEventBus进行分类，最终存到一个Map中，其中k:EventType,V:包含onEvent所属的类、方法名、线程模式等信息。 而post会根据实参去map查找进行反射调用。

#### post
* register会把当前类中匹配的方法，存入一个map，而post会根据实参去map查找进行反射调用。分析这么久，一句话就说完了~~