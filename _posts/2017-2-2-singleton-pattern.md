---
layout: post
title: Singleton pattern
modified: 2017-2-3
categories: [C#, Design pattern]
tags: [Singleton pattern]
comments: true
---
In this blog, I will talk about singleton pattern and the implement of it in C#.

<!-- more -->

<a href="#singleton">Singleton </a>

<a href="#singletonInMultiThread">Singleton in multi-thread </a>

<a href="#staticInitialization">Static Initialization </a>


----------------------------------------------------I am the split line o(^_^)Y------------------------------------------------


### <a name="singleton">Singleton</a>
<br>
What's singleton?

Make sure a class only have one instance and provide a way to global access the instance.

How to implement it?

See the code below. (づ－_－)づ

{% highlight R %}
class MySingleton
    {
        private static MySingleton _instance;

        //Make this constructor private, so the instance of MySingleton can not be "new" outside the class
        private MySingleton()
        {

        }

        //This method is the only way to get MySingleton's instance
        public static MySingleton GetInstance()
        {
            if (_instance == null) //If the instance does not exist, create it
            {
                _instance = new MySingleton();
            }

            return _instance;
        }
    }
{% endhighlight %}




### <a name="singletonInMultiThread">Singleton in multi-thread</a>
<br>
In multi-thread environment, the method <strong>GetInstance</strong> used to create instance may be call in different thread at the same time. This may cause more than one instance are created. 

Therefore, we use <strong>lock</strong> to make sure the instance could only be create once.

The code is shown below.

{% highlight R %}
class MySingletonInMultiThread
    {
        private static MySingletonInMultiThread _instanceInMultiThread;
        private static readonly object _lock = new object();
        private MySingletonInMultiThread()
        {
        }
        public static MySingletonInMultiThread GetInstance()
        {
            lock (_lock) //There is only one thread could access the code in the lock
            {
                if (_instanceInMultiThread == null)
                {
                    _instanceInMultiThread = new MySingletonInMultiThread();
                }
            }           

            return _instanceInMultiThread;
        }
    }
{% endhighlight %}

However, the lock in the code may cause performance problem.

To avoid this, the code is improved as below by using <strong>Double-Check Locking</strong>.

{% highlight R %}
class MySingletonInMultiThread
    {
        private static MySingletonInMultiThread _instanceInMultiThread;
        private static readonly object _lock = new object();
        private MySingletonInMultiThread()
        {
        }
        public static MySingletonInMultiThread GetInstance()
        {           
            if (_instanceInMultiThread == null) //Check the instance first, if the instance does not exist, add lock
            {
                lock (_lock)
                {
                    if (_instanceInMultiThread == null) 
                    {
                        _instanceInMultiThread = new MySingletonInMultiThread();
                    }
                }
            }

            return _instanceInMultiThread;
        }
    }
{% endhighlight %}

Why check the instance again inside the lock?

If the instance doesn't exist and there are two threads call <strong>GetInstance</strong> at the same time. Then these two threads could access the code in the lock together. If we don't check the instance again, there are more than one instance could be created.


### <a name="staticInitialization">Static Initialization</a>
<br>
There is another way to implement singleton in C#.NET.

The code is shown belwo.
{% highlight R %}
public sealed class SingletonStaticInitialization
    {
        private static readonly SingletonStaticInitialization _instance = new SingletonStaticInitialization();
        private SingletonStaticInitialization() { }
        public static SingletonStaticInitialization GetInstance()
        {
            return _instance;
        }
    }
{% endhighlight %}




Thanks for reading!