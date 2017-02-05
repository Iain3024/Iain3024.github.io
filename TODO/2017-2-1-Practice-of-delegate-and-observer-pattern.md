---
layout: post
title: Practice of delegate and observer pattern
modified: 2017-2-1
categories: [C#, Design pattern]
tags: [Delegate, Observer pattern] 
comments: true
---
In this blog, I will represent two ways of implementing logging module by using delegate and observer pattern.

<!-- more -->

<a href="#delegate">Delegate </a>
<a href="#observer">Observer pattern </a>

----------------------------------------------------------------------------------

Logging module is essential to a system. There are many actions need to be record in system log such as administrator login or logout, system exception. 

Some people would like to record log in text file, others may record log in database or even in both text file and database. These things may always change. Add new way to record log or remove the old way.

Therefore, using a flexible way to implement logging module is necessary.

Here is the requirements:
<ul>
<li>
	Implement a logging module.
</li>
<li>
	System log may be record in different media such as text file, MS Sqlserver. 
</li>
<li>
	The media used to store the log may also be changed to another way such as from text file to MS Sqlserver, add a new way to record log.
</li>
</ul>

I will implement these in two ways by using delegate and observer pattern.

I assume that the log would be record in MS SQLServer and text file.

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

There may also have <strong>MySQLWriter</strong>, <strong>OracleWriter</strong> in the real project. This blog is focus on how to deal with this different way of writing log.

### <a name="delegate"> Delegate</a>
<br>
First of all, I use delegate to implement the requirements.

Delegate is a type in C#. There is another <a href="">blog </a>discussed it.

I use a class named LogHelper to implement the process of writing log.

The code is shown below.

<img src="/images/delegate/.jpg">

{% highlight R %}
	public delegate void WriteLogDel(string str);
    public class LogHelper
    {
        public static Queue<string> ExceptionStringQueue = new Queue<string>();
        public static WriteLogDel WriteLogDelFunc;

        static LogHelper()
        {
            WriteLogDelFunc = new WriteLogDel(WriteLogToFile);
            WriteLogDelFunc += WriteLogToSQLServer;

            //write the exception msgs which are from the queue to the log file
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

Observer pattern 

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


Thanks for reading!
