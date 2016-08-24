---
layout: post
title: Installing python and Stan
date: 2016-02-22 14:40:45
---

Before we get started we'll need to download and install python and Stan.  

- explain why we are using two different languages
	--?
- mention why we use python and Stan.
	-- Stan is by far the best
		--- Winbugs and Jags are similar but not as good.  PyMC is kind of cool, but not quite there yet.
	-- you can also use R, but we just prefer python.  since we are only using python for basic stuff, the code is very similar (give link to the webpage )

To install python, we recommend that you download the Anaconda python distribution available from [here](https://www.continuum.io/downloads).  The Anaconda python includes the python language as well as the python packages most commonly used for data science (including several that we use in our examples) and a convenient package manager. This means that python will work right out of the box without having to install additional packages.  We are using python version 3.5 in all of the examples but the examples should work on python version 2.7 as well.

After installing Anaconda, type the following in the terminal to install pyStan, the version of Stan for the python langauge.

```shell
conda install pystan
```

To test that python and pyStan were installed correctly, type the following in a terminal:

```shell
python
import pystan
```

