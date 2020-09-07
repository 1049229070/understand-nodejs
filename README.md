# 通过源码分析nodejs原理

回首对nodejs的源码研究，时间已经过去了一年多。我很喜欢js这门语言，有时候感觉他和c语言一样，在c语言里，很多东西都需要自己实现，让我们可以发挥无限的创造力和想象力，js虽然很多东西在v8里已经提供，但是用js，依然可以创造很多好玩的东西，还有好玩的写法。js应该我见过唯一的一门没有实现网络和文件功能的语言。网络和文件，是一个很重要的能力。对于程序员来说，也是很核心很基础的知识。很幸运，nodejs被创造出来了。nodejs在js的基础上，使用v8提供的能力，极大地拓展、丰富了js的能力。尤其是网络和文件。这样我就不仅可以使用js，还可以使用网络、文件等功能。这是我逐渐转向nodejs方向的原因之一。也是我开始研究nodejs源码的原因之一。因为nodejs满足了我喜好和技术上的需求。不过一开始的时候，我并没有全身心地投入代码的研究，只是偶尔会看一下某些模块的实现。真正的开始，是为了做<nodejs是如何利用libuv实现事件循环和异步>的分享。从那时候起，大部分业余时间和精力都投入源码的研究。<br />
&ensp;&ensp;&ensp;&ensp;我首先从libuv开始研究，因为libuv是nodejs的核心之一。由于曾经研究过一些linux的源码，也一直在学习操作系统的一些原理和实现，所以在阅读libuv的时候，算是没有遇到太大的困难。c语言函数的使用和原理，基本都可以看明白。重点在于需要把各个逻辑捋清楚。我使用的方法就是注释和画图。我个人比较喜欢写注释。虽然说代码是最好的注释，但是我还是愿意花时间用注释去把代码的背景和意义阐述一下，而且注释会让大部分人更快地能读懂代码的含义。读libuv的时候，也穿插地读了一些js和c++层的代码。我阅读nodejs源码的方式是，选择一个模块，垂直地从js层分析到c++层，然后到libuv层。<br/>
&ensp;&ensp;&ensp;&ensp;读完libuv，接下来读的是js层的代码，js虽然容易看懂，但是js层的代码非常多，而且我感觉逻辑上也非常绕，所以至今，我还有很多没有细读。这个作为后续的计划。nodejs中，c++算是胶水层，很多时候，不会c++，其实也不影响nodejs源码的阅读，因为c++很多时候，只是一种透传的功能，他把js层的请求，通过v8，传给libuv。然后再反过来。所以c++层我是放到最后才细读。c++层我觉得是最难的，这时候，我又不得不开始读v8的源码了，理解v8非常难。我选取的几乎是最早的版本0.1.5。然后结合8.x版本。通过早期版本，先学习v8的大概原理和一些早期实现上的细节。因为后续的版本虽然变化很大，但是更多只是功能的增强和优化，有很多核心的概念还是没有变化的，这是我选取早期版本的原因，避免一开始就陷入无穷无尽的代码中，迷失了方向，失去了动力。但是哪怕是早期的版本，有很多内容依然非常复杂。结合新版本是因为有些功能在早期版本里没有实现，这时候要明白他的原理，就只能看新版的代码，有了早期版本的经验，阅读新版的代码也有一定的好处。多少也知道了一些阅读技巧。<br />
&ensp;&ensp;&ensp;&ensp;nodejs的大部分代码都在c++和js层，所以目前仍然是在不断地阅读这两层的代码。还是按照模块垂直分析。阅读nodejs代码，让我更了解nodejs的原理，也更了解js。不过代码量非常大，需要源源不断的时间和精力投入。但是做技术，知其然知其所以然的感觉是非常美妙的，你靠着一门技术谋生，却对他知之甚少，这种感觉并不好。阅读源码，虽然不会为你带来直接的、迅速的收益，但是有几个好处是必然的。第一是他会决定你的高度，第二你写代码的时候，你看到的不再是一些冰冷冷、无生命的字符。这可能有点夸张，但是你了解了技术的原理，你在使用技术的时候，的确会有不同的体验，你的思维也会有了更多的变化。第三是提高了你的学习能力，当你对底层原理有了更多的了解和理解，你在学习其他技术的时候，就会更快地学会，比如你了解了epoll的实现，那你看nginx、redis、libuv等源码的时候，关于事件驱动的逻辑，基本上很快就能看懂。很高兴有这些经历，也投入了很多时间和经精力，希望以后对nodejs有更多的理解和了解，也希望在nodejs方向有更多的实践。

**本书的目的**

阅读nodejs源码的初衷是让自己深入理解nodejs的原理，但是我发现有很多同学对nodejs原理也非常感兴趣，因为业余时间里也一直在写一些关于nodejs源码分析的文章（基于nodejsv10和v14），所以就打算把这些内容整理成一本有体系的书，让感兴趣的同学能系统地去了解和理解nodejs的原理。不过我更希望的是，读者从书中不仅学到nodejs的知识，而且也学到了如何阅读nodejs源码，可以自己独立完成源码的研究。也希望更多同学分享自己的心得。本书不是nodejs的全部，但是尽量去讲得更多，在业余时间里，我会持续阅读nodejs的代码和更新内容。源码非常多，错综复杂，理解上可能有不对之处，欢迎交流。

**阅读建议**

nodejs的源码由js、c++、c组成。<br/>
1 libuv是c语言编写。理解libuv除了需要了解c语法外，更多的是对操作系统和网络的理解，有些经典的书籍可以参考，比如《unix网络编程》1,2两册，《linux系统编程手册》上下两册，《tcp/ip权威指南》等等，不过更好的理解方式还是阅读源码，我的公众号有一些关于linux早期内核源码分析的文章，有兴趣的也可以参考一下。<br/>
2 c++主要是利用v8提供的能力对js进行拓展，也有一部分功能使用c++实现，总的来说c++的作用更多是胶水层，利用v8作为桥梁，连接libuv和js。不会c++，也不完全影响源码的阅读，但是会c++会更好。阅读c++层代码，除了语法外，还需要对v8的概念和使用有一定的了解和理解。<br/>
3 js层代码相信学习nodejs的同学都没什么问题。<br/>
4 如果你在阅读上面有任何问题，可以联系我或者留言，我会的我都会尽量帮你解答。

下载：v0.0.1 https://file-1252105172.cos.ap-guangzhou.myqcloud.com/%E7%90%86%E8%A7%A3nodejs%E5%8E%9F%E7%90%86v0.0.1.docx <br />
微信：theratliter<br />
公众号：<img src="https://img-blog.csdnimg.cn/20200906234455406.jpg#pic_center" height="200px" width="200px"/>
&nbsp;&nbsp;&nbsp;&nbsp;赞助：<img height="200px" width="200px"  src="https://img-blog.csdnimg.cn/20200831135053466.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L1RIRUFOQVJLSA==,size_16,color_FFFFFF,t_70#pic_center)" />

