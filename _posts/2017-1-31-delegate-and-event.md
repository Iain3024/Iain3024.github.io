---
layout: post
title: Delegate and Event 
modified: 2017-1-31
categories: C#
tags: [Delegate, Event, Lambda expression]
comments: true
---

In this blog, I will talk about delegate and event in C#.

In the delegate part, it will include anonymous method, lambda expression, Func<> and Action<>.

<!-- more -->
<a href="#delegate">1. Delegate </a>

<a href="#11delegate">1.1 Delegate </a>

<a href="#12anonymous">1.2 Anonymous Methods </a>

<a href="#13lambda">1.3 Lambda expression </a>

<a href="#14func">1.4 Func<> and Action<> </a>

<a href="#15summary">1.5 Brief summary </a>

<a href="#event">2. Event </a>



----------------------------------------------------I am the split line o(^_^)Y------------------------------------------------

### <a name="delegate"> 1. Delegate </a>
<br>
#### <a name="11delegate">1.1 Delegate </a>

What is delegate?

Delegate is a type. Just like int, float, double. 

A integer number could be assigned to a int variable.

A method could be assigned to a delegate variable.

In addition, delegate is a reference type.

Delegate is similar to method pointer in C++. 

However, delegate is a type-safety pointer. 

As a delegate must point to a method that are match the define of the delegate.

How to use delegate?

Here is the steps:

Step 1, use key word <strong>delegate</strong> define a delegate;

Step 2, define a method to match the delegate defined in step 1;

Step 3, use the method defined in step 2 to instantiate a delegate variable.

For example, the followling code define a delegate named <strong>AddDel</strong>. It has two input parameters <strong>a</strong> and <strong>b</strong> that are int type. 

The method <strong>AddFunc</strong> is used to match the delegate <strong>AddDel</strong>.

<strong>del</strong> is a variable of <strong>AddDel</strong> and instantiated by using <strong>AddFunc</strong>.

{% highlight R %}
Public delegate int AddDel(int a, int b);  // define a delegate

Class program
{
    Static void Main(string[] args)
    {
        AddDel del = new AddDel(AddFunc); //instantiate a delegate variable by using AddFunc
        del(2,3);
    }
    
    Static int AddFunc(int a, int b) //define a method to match the delegate AddDel
    {
        return a+b;
    }
}
{% endhighlight %}
<br>
<br>

#### <a name="12anonymous">1.2 Anonymous Methods </a>

Usually, delegate receives disposable method or the method body is very simple.

Therefore, we could use Anonymous Method to instead of the define of method. 

{% highlight R %}
    Public delegate int AddDel(int a, int b);  // define a delegate
    
    Class program
    {
        Static void Main(string[] args)
        {
            AddDel del = delegate(int a,int b) { return a+b; } //use Anonymous Method to define a method
            del(2,3);
        }
    }

{% endhighlight %}
<br>
<br>

#### <a name="13lambda">1.3 Lambda expression </a>

Lambda expression is anonymous method in nature.

As a result, we can use Lambda expression to instead of anonymous method. 

The lambda operator => read as "goes to".

The following two lines code are equal.

{% highlight R %}
    //AddDel del = delegate(int a,int b) { return a+b; }
 
    AddDel lambda = (int a ,int b) => {return a+b ;};
{% endhighlight %}

As there are two input parameters in int type are needed when the delegate was defined. 

The type of parameters in lambda expression could be omitted.

Finally, 

{% highlight R %}
AddDel lambdaDemo = (a,b) => a+b; // equal to AddDel lambda = (int a ,int b) => {return a+b ;};
{% endhighlight %}
<br>
<br>

#### <a name="14func">1.4 Func<> and Action<> </a>

In a real project, there are many delegate with the same method signature need to be define.

This cause a lot trouble.

Therefore, Microsoft provide two generic delegate Func<> and Action<>.

Func<> could have 0 to 16 parameters and 1 return result.

Action<> is similar with Func<> with no return result.

<br>
<br>

#### <a name="15summary">1.5 Brief summary </a>
<ul>
<li>When you need to use a parameter which is a delegate type, just use lambda expression.</li>
<li>Lambda expression could be assigned to a delegate variable.</li>
<li>Lambda expression is anonymous Methods in nature.</li>
<li>If you want to use delegate, just use Func<> and Action<>.</li>
</ul>
<br>
<br>
 

### <a name="event">2. Event</a>
Event is a instance of delegate.

Event is safer than delegate because event only can be triggered in the class contained it.

Delegate could be triggered outside the class contained it.

The following example will represent why event is safer than delegate.

Here is the requirements: 

There are two forms. Main form and child form as shown below.

<img src="/images/delegate/mainandchild.jpg">

Click the button "StartChildFrm" will open the child form.

Click the button "SendToMainFrm" will send the string inputed in the textbox to main form and display on the textbox of main form.

First of all, we use delegate.

Here is the code in main form.

{% highlight R %}
namespace DelegateAndEvent
{
    public partial class MainFrm : Form
    {
        public MainFrm()
        {
            InitializeComponent();
        }

        private void StartChildFrm_Click(object sender, EventArgs e)
        {
            ChildFrm childFrm = new ChildFrm();
            childFrm.ChildFrmInputDel = new ChildFrmInput(this.GetChildInput);            

            childFrm.Show();
            //childFrm.ChildFrmInputDel("Output anything hahaha!");
        }

        public void GetChildInput(string text)
        {
            tbMainFrmShow.Text = text;
        }
    }
}

{% endhighlight %}

Here is the code in child form.

{% highlight R %}
namespace DelegateAndEvent
{
    public delegate void ChildFrmInput(string text);

    public partial class ChildFrm : Form
    {
        public ChildFrmInput ChildFrmInputDel { get; set; }

        public ChildFrm()
        {
            InitializeComponent();
        }

        private void SendToMainFrm_Click(object sender, EventArgs e)
        {
            if (ChildFrmInputDel != null)
            {
                ChildFrmInputDel(this.tbChildFrmInput.Text);
            }
        }
    }
}
{% endhighlight %}

Run the program and the result are shown below.

<img src="/images/delegate/childinput.jpg">

<img src="/images/delegate/MainGetinput.jpg">


The requirements could be implement.

However, we can use delegate in main form like this:

{% highlight R %}
 childFrm.ChildFrmInputDel("Output anything hahaha!");
{% endhighlight %}

Run it.

<img src="/images/delegate/delegateProblem.jpg">

The delegate is triggered outside ChildFrm.

Next, we use event to solve this problem.

Here is the code in main form.

{% highlight R %}
namespace DelegateAndEvent
{
    public partial class MainFrm : Form
    {
        public MainFrm()
        {
            InitializeComponent();
        }

        private void StartChildFrm_Click(object sender, EventArgs e)
        {
            ChildFrm childFrm = new ChildFrm();
            //childFrm.ChildFrmInputDel = new ChildFrmInput(this.GetChildInput);
            childFrm.ChildFrmInputEvent += GetChildInput;

            childFrm.Show();
            //childFrm.ChildFrmInputDel("Output anything hahaha!");          
        }

        public void GetChildInput(string text)
        {
            tbMainFrmShow.Text = text;
        }
    }
}

{% endhighlight %}

Here is the code in child form.

{% highlight R %}
namespace DelegateAndEvent
{
    public delegate void ChildFrmInput(string text);

    public partial class ChildFrm : Form
    {
        //public ChildFrmInput ChildFrmInputDel { get; set; }
        public event ChildFrmInput ChildFrmInputEvent;
        public ChildFrm()
        {
            InitializeComponent();
        }

        private void SendToMainFrm_Click(object sender, EventArgs e)
        {
            if (ChildFrmInputEvent != null)
            {
                //ChildFrmInputDel(this.tbChildFrmInput.Text);
                ChildFrmInputEvent(this.tbChildFrmInput.Text);
            }
        }
    }
}
{% endhighlight %}

Run the program again.

<img src="/images/delegate/eventchild.jpg">

<img src="/images/delegate/eventMain.jpg">

All good.

The followling code are not allowed anymore.

{% highlight R %}
childFrm.ChildFrmInputEvent("Output anything hahaha!");
{% endhighlight %}


Thanks for reading.

There is another <a href="http://dotnetbuilder.co.nz/c%23/design%20pattern/2017/02/01/Practice-of-delegate-and-observer-pattern.html">blog </a>that implement logging module using different ways : delegate and observer pattern.

Highly recommended！








