---
layout: post
title: Delegate Event and Observer pattern
modified: 2017-1-31
categories: C#
tags: [Delegate, Event, Design pattern]
comments: true
---

In this blog, I will talk about delegate, event and observer pattern.

In delegate part, it will include anonymous method, lambda expression, Func<> and Action<>.

<!-- more -->
<a href="#delegate">1. Delegate </a>
<a href="#11delegate">1.1 Delegate </a>
<a href="#12anonymous">1.2 Anonymous Methods </a>
<a href="#13lambda">1.3 Lambda expression </a>
<a href="#14func">1.4 Func<> and Action<> </a>
<a href="#15summary">1.5 Brief summary </a>
<a href="#event">2. Event </a>
<a href="#observer">3. Observer pattern </a>


### <a name="delegate"> 1. Delegate </a>

#### <a name="11delegate">1.1 Delegate </a>


<!-- more -->
What is delegate?

Delegate is a type. Just like int, float, double. 

A integer number could be assigned to a int variable.

A method could be assigned to a delegate variable.

Here is the steps:

Step 1, use key word "delegate" define a delegate;

Step 2, define a method to match the delegate defined in step 1;

Step 3, use the method defined in step 2 to instantiate a delegate variable.


{% highlight R %}
Public delegate int AddDel(int a, int b);  // delegate

Class program
{
    Static void Main(string[] args)
    {
        AddDel del = new AddDel(AddFunc);
        del(2,3);
    }
    
    Static int AddFunc(int a, int b)
    {
        return a+b;
    }
}

{% endhighlight %}

#### <a name="12anonymous">1.2 Anonymous Methods </a>

Usually, delegate receives disposable method or the method body is very simple.

Therefore, we could use Anonymous Methods to instead of the define of method. 

{% highlight R %}
    Public delegate int AddDel(int a, int b);  // delegate
    
    Class program
    {
        Static void Main(string[] args)
        {
            AddDel del = delegate(int a,int b) { return a+b; }
            del(2,3);
        }
    }

{% endhighlight %}

#### <a name="13lambda">1.3 Lambda expression </a>


{% highlight R %}
    //AddDel del = delegate(int a,int b) { return a+b; }
 
    AddDel lambda = (int a ,int b) => {return a+b ;};

{% endhighlight %}

Finally, 
{% highlight R %}
AddDel lambdaDemo = (a,b) => a+b;
{% endhighlight %}


#### <a name="14func">1.4 Func<> and Action<> </a>

#### <a name="15summary">1.5 Brief summary </a>
<ul>
<li>When you need to use a parameter which is a delegate type, just use lambda expression.</li>
<li>Lambda expression could be assigned to a delegate variable.</li>
<li>Lambda expression is anonymous Methods in nature.</li>
<li>If you want to use delegate, just use Func<> and Action<>.</li>
</ul>

 

### <a name="event">2. Event</a>

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

<img src="/images/delegate/childinput.jpg">

<img src="/images/delegate/MainGetinput.jpg">

<img src="/images/delegate/delegateProblem.jpg">

The requirements could be implement.

However, we can use delegate in main form like this:

{% highlight R %}
 childFrm.ChildFrmInputDel("Output anything hahaha!");
{% endhighlight %}


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

### <a name="observer">3. Observer pattern</a>





