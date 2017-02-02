---
layout: post
title: Delegate Event and Observer pattern
modified: 2017-1-31
categories: C#
tags: [Delegate, Event, Design pattern]
comments: true
---


### Delegate


{% highlight R %}

{% endhighlight %}



### Event

The following example will represent why event is safer than delegate.

Here is the requirements: 

There are two forms. Main form and child form as shown below.

<img src="/images/delegate/mainfrm.jpg">

<img src="/images/delegate/childForm.jpg">

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

### Observer pattern





