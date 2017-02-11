---
layout: post
title: Practice of delegate and observer pattern
modified: 2017-2-1
categories: [C#, Design pattern]
tags: [Delegate, Observer pattern] 
comments: true
---
In this blog, I will represent two ways of implementing exception log record module by using delegate and observer pattern.

<!-- more -->

<a href="#delegate">Delegate </a>

<a href="#observer">Observer pattern </a>

----------------------------------------------------I am the split line o(^_^)Y------------------------------------------------

Exception log record is essential to a system. Usually we can not install a visual studio in a production environment to track bugs. Therefore, record the exception information is crucial for finding and analyzing problems in enterprise server environments.

Sometimes exception log may need to be record in text file, sometimes may record in database or using a web service to write log to another remote server. These things may always change. Add new way to record log or remove the old way.

As a result, using a flexible way to implement log record is necessary.

Here is the requirements:
<ul>
<li>
	Implement a exception log record module.
</li>
<li>
	Exception log may be record in different media such as text file, MS Sqlserver. 
</li>
<li>
	The media used to store the log may also be changed to another way such as from text file to MS Sqlserver, add a new way to record log.
</li>
</ul>

I will implement these in two ways by using delegate and observer pattern.

First, I assume that the log would be record in MS SQLServer. Then, I will also record the log in text file.

There are some common code both in delegate way and observer pattern way.

I use a interface to define the common action of different way on writing
 log. Therefore, if the system need to add a new way to record log, just add a class that implement the interface.

The code of the interface is shown below.

{% highlight R %}
public interface ILogWriter
{
    void WriteLogInfo(string txt);
}
{% endhighlight %}

There is a class named <strong>SQLServerWriter</strong> implement write log to SQLServer database. I did not implement this class as this blog is focus on the process of writing log to different media.

The code is shown below.

{% highlight R %}
public class SQLServerWriter : ILogWriter
{
    public void WriteLogInfo(string txt)
    {
        throw new NotImplementedException();
    }
}
{% endhighlight %}


There is another class named <strong>TextFileWriter</strong> implement write log to text file. The same with <strong>SQLServerWriter</strong>.

The code is shown below.

{% highlight R %}
public class TextFileWriter : ILogWriter
{
    public void WriteLogInfo(string txt)
    {
        throw new NotImplementedException();
    }
}
{% endhighlight %}



### <a name="delegate"> Delegate</a>
<br>
First of all, I use delegate to implement the requirements.

Delegate is a type in C#. There is another <a href="">blog </a>discussed it.

I use a class named LogHelper to implement the process of writing log.

In a large scale system, there may be many exception information need to be record in the same time. It may cause concurrency problem when we call the method or service to write this information at the same time and may also cause performance problem when writing the information.

Here is one of the solution, 

step 1, use a queue to store the exception information;

step 2, use another thread to write the information to the media such as database, text file.

For step 1, as queue is in the memory which provide an extremely high speed to read and write, it could solve the problem caused by high concurrency. 

For step 2, it is a asynchronous way to deal with the performance problem.


The code is shown below.


{% highlight R %}
	public delegate void WriteLogDel(string str); 
    public class LogHelper
    {
        public static Queue<string> ExceptionStringQueue = new Queue<string>(); //define a queue to store the exception msgs
        public static WriteLogDel WriteLogDelFunc;

        static LogHelper()
        {
            WriteLogDelFunc = new WriteLogDel(WriteLogToFile);
            WriteLogDelFunc += WriteLogToSQLServer;

            //write the exception msgs from the queue to the log file
            ThreadPool.QueueUserWorkItem(o =>
            {
                lock (ExceptionStringQueue)
                {
                    string str = ExceptionStringQueue.Dequeue();
                    WriteLogDelFunc(str);
                }
            });
        }

        public static void WriteLogToFile(string txt)
        {
            throw new NotImplementedException();
        }

        public static void WriteLogToSQLServer(string txt)
        {
            throw new NotImplementedException();
        }

        public static void WriteLog(string exceptionText)
        {
            lock (ExceptionStringQueue)
            {
                ExceptionStringQueue.Enqueue(exceptionText);
            }
        }
    }
{% endhighlight %}



<br>
<br>

### <a name="observer"> Observer pattern</a>

Next, I use observer pattern to implemnt the requirements.

The code is shown below.

{% highlight R %}
	public class LogHelper
    {
        public static Queue<string> ExceptionStringQueue = new Queue<string>();
        public static List<ILogWriter> LogWriterList = new List<ILogWriter>();

        static LogHelper()
        {
            LogWriterList.Add(new TextFileWriter());
            //LogWriterList.Add(new SQLServerWriter());

            //write the exception msgs which are from the queue to the log file
            ThreadPool.QueueUserWorkItem(o =>
            {
                lock (ExceptionStringQueue)
                {
                    string str = ExceptionStringQueue.Dequeue();
                    foreach (var logWriter in LogWriterList)
                    {
                        logWriter.WriteLogInfo(str);
                    }
                }
            });
        }

        public static void WriteLog(string exceptionText)
        {
            lock (ExceptionStringQueue)
            {
                ExceptionStringQueue.Enqueue(exceptionText);
            }
        }
    }
{% endhighlight %}

There may also have <strong>MySQLWriter</strong>, <strong>OracleWriter</strong> in the real project. Then just add an implement of the write action and implement it with the interface <strong>ILogWriter</strong>. Add a new 

Thanks for reading!
