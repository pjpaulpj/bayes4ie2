---
layout: post
title: Getting started with Stan - a simple example
date: 2016-03-22 14:40:45
---





1. simple linear regression of data from an RCT
2. include a diagram showing how Stan is called, how the data maps to the data block, and then what the Stan call spits out
3. include a second diagram showing how the data is modeled and processed internally within Stan.  this seems to be...
	- data gets passed to Stan and the data is mapped to the variable types based on the data block
	- data gets transformed in the transformation block
	- --> not quite sure the difference between the parameters and model block. need to review this.  might just be that the parameters are the things that Stan keeps track of.  
	- --> don't forget transformed parameters and generated quantities block and the functions block
	- check out the Stan manual starting at page 300 for this info
4. include a link to the Stan manual
    1. mention which chapters people should read
5. links to other materials