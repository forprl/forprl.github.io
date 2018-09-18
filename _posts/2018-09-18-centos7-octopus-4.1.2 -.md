---
layout: post
title:  "centos7 gun 编译octopus-4.1.2遇到问题和解决方案 "
date:   2018-09-18 21:12:00 +0800
categories: DFT
tags: gnu octopus
author: cndaqiang
mathjax: true
---
* content
{:toc}

Error: Invalid character in name at (1)<br>
/usr/include/stdc-predef.h:2.3:<br>
	Included at c_pointer.F90:1:<br>
	   This file is part of the GNU C Library.<br>
   1<br>







   
centos7编译octopus遇到的问题<br>
与在centos6上，使用相同版本的gcc-4.8.4,libxc-2.0.3,gsl-1.14,openmpi-1.10.3,fftw-3.3.3,sclapack进行编译<br>
octopus-4.1.2的configure命令没有报错，make时却报下面的错误
```
/* Copyright (C) 1991-2012 Free Software Foundation, Inc.
 1
Error: Invalid character in name at (1)
/usr/include/stdc-predef.h:2.3:
	Included at c_pointer.F90:1:

   This file is part of the GNU C Library.
   1
Error: Unclassifiable statement at (1)
/usr/include/stdc-predef.h:4.3:
	Included at c_pointer.F90:1:

   The GNU C Library is free software; you can redistribute it and/or
   1
Error: Unclassifiable statement at (1)
/usr/include/stdc-predef.h:4.39:
	Included at c_pointer.F90:1:

   The GNU C Library is free software; you can redistribute it and/or
									   1
Error: Unclassifiable statement at (1)
/usr/include/stdc-predef.h:5.3:
	Included at c_pointer.F90:1:

   modify it under the terms of the GNU Lesser General Public
```

<br><br><br><br>
根据[Yambo Community Forum](http://www.yambo-code.org/forum/viewtopic.php?f=1&t=842)的建议，应该是libxc的问题，无法识别`/usr/include/stdc-predef.h`中的注释部分
<br><br>备份`stdc-predef.h`后，删除其中的注释部分，也就是`/*注释。。。*/`里面的内容，再执行编译就可以了