---
layout: post
category : Computer Trick
tags : [python,rant]
---
In one of my last posts I installed python packages with pip and the `--user`
switch.
This worked, but sucked.
I was too stupid to manage packages with conda and would not even
advise my enemies to do so.
But I found a better way.

There is a tool called `vitualenv` which can set up virtual Python
instances.
It works in userspace and so does not interfere with my
package manager.
This may sound like an obvious thing to you, and perhaps I am noobish
for mentioning it, but I hope it helps someone.
Remember, I wanted to install
[zipline](https://github.com/quantopian/zipline).
So I did a 
{% highlight bash %}
$ virtualenv ~/venv/zipline
New python executable in /home/hjgk/venv/zipline/bin/python
Installing setuptools, pip...done.
{% endhighlight %}
which set up a new virtual environment in `~/venv/zipline`.
Note that zipline is not only the name of the package I want to
install, but also the name of my virtual environment.

Then I switched to the new environment with
{% highlight bash %}
$ source ~/venv/zipline/bin/activate
{% endhighlight %}
and my prompt was prepended with `(zipline)`, because I am now in my
new
virtual environment where I can install packages like a lunatic.
{% highlight bash %}
(zipline)$ pip install numpy&&pip install zipline
{% endhighlight %}
Now it is compiling and throwing some warnings, but finally it
finishes. At least in my case.

Then I tested everything according to the zipline docs
{% highlight bash %}
(zipline)$ python ~/venv/zipline/bin/run_algo.py -f dual_mavg.py --symbols AAPL --start 2011-1-1 --end 2012-1-1 -o dma.pickle
{% endhighlight %}
And it worked!

Note that I have to write `~/venv/zipline/bin/run_algo.py`, since I
have not found out how to set a path in python, as in one of my
last posts.
But at least I am able to install packages without arguing with
aptitude.
