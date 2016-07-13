---
layout: post
category : Computer Trick
tags : [python,ubuntu,rant]
---
## Broken Pip
So I managed upgrading Ubuntu from 12.04 to 14.04 and somehow even fixed some issues with tex by typing obscure commands in my shell.
Now I wanted to install [zipline](https://github.com/quantopian/zipline) for which I need [conda](http://continuum.io/downloads) for which I need pip, the python package manager.  
Well, seems the upgrade to 14.04 broke pip.
Any command line switches I have tried lead to ``ImportError: cannot import name IncompleteRead``.

The guys with the funny names on the internet recommended to do a ``sudo easy_install pip`` which basically convinces another python package manager to install pip.
This is of course stupid, since Ubuntu is package-based and so my aptitude would get confused if i install stuff into the global /usr/local/lib/python2.7/

I did a ``easy_install --user pip`` which installed pip into .local/bin/pip I think I can live with that.
Finally, I wanted to have anaconda. Anaconda is a virtual environment for python, like virtualenv, but it can also cope with non-python library dependencies.

{% highlight bash %}
$ ./.local/bin/pip install --user conda
Downloading/unpacking conda
  Real name of requirement conda is conda
  Could not find any downloads that satisfy the requirement conda
{% endhighlight %}
Stupid machine. Y u no installing?  
So i tried the broken global pip from /usr/bin/ which now magically worked again and did not throw ImportErrors.
{% highlight bash %}
$ pip install conda
Downloading/unpacking conda
  Cannot fetch index base URL https://pypi.python.org/simple/
  Could not find any downloads that satisfy the requirement conda
{% endhighlight %}
Then another ``./.local/bin/pip install --user conda`` worked.
Don't ask me why.

## Arguing with Conda
Now, on to zipline.
At least that was my thought process.

{% highlight bash %}
$ conda install -c Quantopian zipline
Error: This installation of conda is not initialized. Use 'conda create -n
envname' to create a conda environment and 'source activate envname' to
activate it.

# Note that pip installing conda is not the recommended way for setting up your
# system.  The recommended way for setting up a conda system is by installing
# Miniconda, see: http://repo.continuum.io/miniconda/index.html
{% endhighlight %}

Damn, I did it the wrong way, via pip. FYI, miniconda cannot be found with aptitude.
So I made my own environment: ``conda create -n myenv python`` which fetched some packages and put them somewhere in $HOME/envs/myenv
Activating this environment should be straightforward with ``source activate myenv``.
After creating myenv this conda stuff even said to me:
{% highlight bash %}
# To activate this environment, use:
# $ source activate myenv
#
# To deactivate this environment, use:
# $ source deactivate
#
{% endhighlight %}

Oh, how wrong I was:
{% highlight bash %}
$ source activate myenv
Traceback (most recent call last):
  File "/home/hjgk/.local/bin/conda", line 5, in <module>
    sys.exit(main())
  File "/home/hjgk/.local/lib/python2.7/site-packages/conda/cli/main.py", line 67, in main
    activate.main()
  File "/home/hjgk/.local/lib/python2.7/site-packages/conda/cli/activate.py", line 99, in main
    conda.install.symlink_conda(join(binpath, '..'), conda.config.root_dir)
  File "/home/hjgk/.local/lib/python2.7/site-packages/conda/install.py", line 371, in symlink_conda
    os.symlink(root_conda, prefix_conda)
OSError: [Errno 17] File exists
{% endhighlight %}

After googling I found out that the problem apparently was that conda could not handle local installations.
{% highlight bash %}
$ ls -la envs/myenv/bin/activate 
lrwxrwxrwx 1 hjgk hjgk 17 Feb  8 17:29 envs/myenv/bin/activate -> /usr/bin/activate
{% endhighlight %}
And /usr/bin/activate did not exist.
I decided to remove all that damn conda-stuff and install zipline via pip.
At least ``pip uninstall conda`` worked like a charm.

## More pip
So according to the [zipline docs](https://github.com/quantopian/zipline) I needed to do
{% highlight bash %}
pip install numpy   # Pre-install numpy to handle dependency chain quirk
pip install zipline
{% endhighlight %}
Of course I quickly added the ``--user`` switches, and indeed it worked.
I do not know why, because ``which pip`` gave me /usr/bin/pip which was broken at the beginning of this post and the correct one should have been in ~/.local/bin/pip.

I ran ``python ~/.local/bin/run_algo.py -f dual_mavg.py --symbols AAPL --start 2011-1-1 --end 2012-1-1 -o dma.pickle`` according to the zipline docs which wrote a dma.pickle file.
It would be nice to set a path for python, so it looks into ~/.local/bin/ and i can just write ``python run_algo.py -f dual_mavg.py blablabla`` but I have not found out how this works.

## Conclusion
The python ecosystem sucks. I have two package managers, easy_install and pip, which can repair each other.
I mean, what is the point of having a package manager bundled with the operating system when you cannot use it and are doomed to update your python-packages manually like some Windows-user?  
There is virtualenv and anaconda, which mostly do the same thing.
I have not tested virtualenv and do not intend to do so, but anaconda or conda or miniconda or whichever part of that stuff I have used feels like being stuck in haskells cabal hell.  
And don't get me even started on python 3 and those python notebooks.
