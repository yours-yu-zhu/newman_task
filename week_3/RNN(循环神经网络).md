# RNN(循环神经网络)

# **RNN应用案例**

## **智慧订票系统**

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201026478-784130664.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201026478-784130664.png)

例如一些智慧客服或者智慧订票系统里，往往需要槽填充。

**什么是槽填充？**

系统里有两个槽位，分别叫做目的地和到达时间。假如一个人对订票系统说“ i would like to arrive Taipei on November 2nd”，那么系统要自动知道每个词汇属于哪一个槽位，例如“Taipei”属于目的地这个槽位，“November 2nd”属于到达时间这个槽位，其他词汇不属于任何槽位。

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201026758-1686443997.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201026758-1686443997.png)

**如何进行槽填充？**

思考是否可以用一个前馈神经网络来解

input一个词汇，例如把“Taipei”转化为向量丢到神经网络里去

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201026965-1035425612.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201026965-1035425612.png)

**如何把词汇转化为向量？**

最简单的方式是1-of-N encoding，类似独热编码

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201027164-310249183.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201027164-310249183.png)

**如何把词汇转化为向量？**

dimension for “Other”：

有时候只用1-of-N encoding描述一个词汇会出现问题，因为向量是根据词典制作的，如果出现了不在词典里的新词汇，原来的向量就没法表达，那么在原来向量里添加一个新的维度“other”，所有遇到的新词汇都归到“other”类。比如“Gandalf、Sauron”不在词典里，就归为“other”类。

Word hashing：

也可以用某个词汇的字母来表示成词向量，如果用n-gram（n元）字母表示的话，就不会出现不在词典中的新词汇问题。比如一个词汇“apple”，用三元字母可表示为“app”，“ppl”，“ple”，那么"apple"在词向量里的“app”、“ppl”、“ple”维度都为1，其余维度为0

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201027370-514311752.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201027370-514311752.png)

把词汇表示为词向量后，作为input丢到前馈神经网络里去，在槽填充这个任务里，希望输出是一个概率分布（属于哪个槽的概率）。

例如上图Taipei属于槽“目的地”的概率、属于槽"出发时间"的概率等等。

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201027592-1814555516.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201027592-1814555516.png)

光有前馈神经网络是不能够做槽填充的

**为什么不够？**

假设一个使用者说"arrive Taipei on November 2nd",arrive是other，Taipei是目的地，on是other，November是时间，2nd也是时间。另外一个使用者说"leave Taipei on November 2nd"，那Taipei就应该是出发地而不是目的地。但是对前馈神经网络来说，输入一样，输出也是一样的，不能让Taipei的输出即是目的地又是出发地。这个时候我们就希望神经网络是有记忆力的，在看到红色Taipei的时候，记得Taipei前的arrive，看到绿色Taipei的时候记得leave，可以根据词语的上下文，产生不同的输出。那有记忆的神经网络就做到相同输入，不同输出。

# **什么是RNN**

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201027776-1479851234.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201027776-1479851234.png)

有记忆的神经网络叫做RNN循环神经网络

每次隐藏层里的神经元产生输出的时候，这个输出会被存到memory里去（上图蓝色方块表示memory）。下一次有输入的时候，神经元不是只考虑$x_1,x_2$ ，还会考虑存在memory里的值，即$a_1,a_2$也会影响神经元的输出。

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201027950-1652392802.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201027950-1652392802.png)

举个例子

上图网络所有的权重为1，所有的神经元没有偏置，假设所有的激活函数都是线性激活函数（让计算不要太复杂）。

现在假设输入是一个序列[11][11][22]⋯，输入到循环神经网络里。在开始使用RNN前，要先设置memory的初始值，现在假设初始值为0,0。输入1,1后，上图第一个绿色神经元除了连接到输入1,1外，还连接到memory 0,0，那么输出等于2，第二个绿色神经元输出因为2，那么两个红色神经元的输出都为4。

输入[1,1]，输出[4,4]

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201028132-250964205.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201028132-250964205.png)

接下来RNN会把绿色神经元的输出存在memory里去，memory里的值更新为2

再输入[1,1]，这时候绿色神经元的输入为4个，[1,1]和[2,2]，权重都为1，所以输出为2+2+1+1=6，最后红色神经元的输出为6+6=12

**所以对RNN来说，就算是同样的输入，也可能会有不同的输入，因为存在memory里的值是不一样的**

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201028345-1446103659.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201028345-1446103659.png)

因为绿色神经元的输出是[6,6]，被存到memory里去，memory的值更新为6

接下来输入[2,2]，绿色神经元考虑4个输入，[2,2]和[6,6]，输出为6+6+2+2=16，那么红色神经元的输出为16+16=32

**做RNN的时候有一件很重要的事情是，RNN认为输入的序列不是独立的，如果调换输入序列的顺序（比如把[22][22]换到最前面），那么输出就会完全不一样，所以RNN会考虑输入序列的顺序**

# **RNN架构**

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201028549-832948301.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201028549-832948301.png)

用RNN做槽填充

有一个使用者说“arrive Taipei on November 2nd”，则把arrive变成一个向量丢到神经网络里，隐藏层的输出为$a^1$ (是一排神经元的输出，是一个向量)，根据$a^1$ 产生$y^1$ (arrive属于哪一个槽的概率)，然后$a^1$被存到memory里去。

接下来输入表示Taipei的向量，隐藏层会同时考虑Taipei和memory $a^1$，得到$a^2$，再根据$a^2$得到$y^2$(Taipei属于哪一个槽的概率)，然后$a^2$被存到memory里去。

后面的词语处理类推之前的过程，输入表示on的向量，隐藏层同时考虑输入和memory $a^2$，得到$a^3$再得到$y^3$（on属于哪一个槽的概率)。

这里要注意，有人看到上图会觉得有3个网络，其实是同一个网络在3个不同的时间点被使用了3次。上图同样的weight用相同的颜色表示。

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201028748-1517402324.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201028748-1517402324.png)

有了memory之后，输入相同，就可以有不同输出了。例如上图的Taipei，红色Taipei前面接的是leave，绿色Taipei前面接的是arrive，因为leave和arrive的向量不同，所以memory 的值不同，隐藏层的输出也会不同。

# **其他RNN架构**

## **Elman Network**

保存隐藏层的输出值，在下一个时间点被隐藏层使用

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201028914-1581598193.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201028914-1581598193.png)

RNN的架构是可以任意设计的，可以变得很深

叠加隐藏层，每个隐藏层的输出都被存在memory里，在下一个时间点的隐藏层会读取上一个时间点保存的memory 值。

## **Jordan Network**

保存整个网络的输出值，在下一个时间点被隐藏层使用

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201029109-1706525316.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201029109-1706525316.png)

据说Jordan Network 可以得到更好的效果，因为隐藏层是没有学习目标的，学什么样的隐藏信息很难控制（学到的东西放到memory里），但是$y$（输出层）是有学习目标的，可以比较清楚地知道存在memory里的是什么样的东西

## **双向RNN**

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201029305-1704341902.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201029305-1704341902.png)

RNN可以是双向的

之前input一个句子是从句首读到句尾，假设词语用$x^t$表示，那就是先读$x^t$，再读$x^{t+1}$，再读$x^{t+2}$

读取方向也可以反过来，先读$x^{t+2}$，再读$x^{t+1}$，再读$x^t$，可以同时训练一个正向的RNN和一个反向的RNN，然后把两个RNN的隐藏层拿出来，接到同一个输出层得到最后的$y$

如上图，正向RNN的$x^t$的输出和反向RNN的$x^t$的输出，都输入到一个输出层得到$y^t$

**用双向RNN有什么好处？**

双向RNN在产生输出的时候，看得范围是比较广的。如果只有正向RNN，在输出$y^{t+1}$时，RNN只看过$x^1$到$x^{t+1}$的部分，但是双向RNN在输出$y^{t+1}$时，不仅看了$x^1$到$x^{t+1}$所有输入，而且看了句尾到$x^{t+1}$的所有输入，等于看了整个输入序列，再去决定词汇的槽位是什么，这样效果比只看一半句子会好。

# **LSTM**

## 定义

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201029473-1255459634.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201029473-1255459634.png)

之前的RNN是比较简单的版本，随时可以把值存到memory中，也可以随时读取memory中的值

现在比较常用的memory称为Long Short-term Memory（长短期记忆单元），比较复杂有3个门

1. **输入门：隐藏层的值要存到memory时，要先通过输入门，被打开时才可以把值保存到memory，门是打开还是关闭是神经元自己学习的。**
2. **输出门：输出的地方也有一个门，表示其他的神经元可不可以从memory里读取值，只有被打开的时候才可以读取，输出门什么时候打开关闭也是神经元自己学习的。**
3. **遗忘门：第三个门，表示什么时候memory要把过去保存的东西忘记，或者什么时候要把保存的东西做一些格式化，格式化掉还是保存下来也是神经元自己学习的。**

一个LSTM memory cell，可以看成有4个输入，1个输出。4个输入分别是

1. **想要被存到memory cell里的值（由输入门控制是否保存）**
2. **操控输入门的信号**
3. **操控输入门的信号**
4. **操控遗忘门的信号**
- **一个小小的冷知识，Long Short-term里的划线应该被放在哪里?**

有些人把划线放在Long和Short之间，这是没有意义的，应该放在Short和term之间

因为LSTM的memory还是一个比较短期的记忆（只是稍微长一点的短期记忆），RNN的memory在每个时间点都会被更新掉，只保存前一个时间点的东西，LSTM可以记得稍微长一点的东西，只要遗忘门不要决定遗忘掉

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201029662-680260589.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201029662-680260589.png)

详细的LSTM cell 公式如上图所示。

- $z$是输入
- $z_i$（一个数值）是操控输入门的信号
- $z_f$是操控遗忘门的信号
- $z_o$是操控输出门的信号
- 最后得到一个输出$a$

假设一个单元里，有上面4个输入$(z,z_i,z_f,z_o)$之前，已经保存了值$c$。

$z_i,z_f,z_o$通过的激活函数通常选择sigmoid函数（取值0-1，代表门被打开的程度），激活函数输出为1代表门是处于打开的状态，为0表示门处于关闭的状态

**计算当前时刻要保留的新信息$g(z)f(z_i)$**

- $z$通过一个激活函数得到$g(z)$ ，$z_i$通过另一个激活函数得到$f(z_i)$（当前时刻信息保留程度）
- 把$g(z)$乘以$f(z_i)$

**接下来计算当前时刻的保留信息（新的memory值）$c'=g(z)f(z_i)+cf(z_f)$**

- $z_f$通过另一个激活函数得到$f(z_f)$（上一个时刻的信息保留程度）
- 把存在memory 里的$c$乘上 $f(z_f)$（上一时刻要保留的信息）
- 计算 $c'=g(z)f(z_i)+cf(z_f)$（当前时刻要保留的新信息+上一时刻要保留的信息）
- $c′$是新的保存在memory里的值（当前时刻的保留信息）

根据到目前为止的计算发现

- $f(z_i)$在控制$g(z)$，$f(z_i)=0$就好像没有输入，$f(z_i)=1$就好像直接把$g(z)$输入
- $f(z_f)$决定要不要把之前存在memory里的$c$洗掉，$f(z_f)=1$意思是保留之前的值，$f(z_f)=0$意思是洗掉之前保留的值

遗忘门跟我们直觉的想法是相反的，遗忘门打开的时候表示记得，关闭的时候表示遗忘

**最后计算要输出的信息$a=h(c')f(z_o)$**

- $c′$通过$h$得到$h(c')$
- 输出门$z_o$操控，$z_o$通过$f$得到 $f(z_o)$，$f(z_o)=0$表示memory的值无法通过输出门
- 把$h(c')$和$f(z_o)$乘起来（当前时刻要输出的信息）

## 计算例子

![Untitled](RNN(%E5%BE%AA%E7%8E%AF%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C)%20a65ecf374281479c81f5157dc64e0335/Untitled.png)

![Untitled](RNN(%E5%BE%AA%E7%8E%AF%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C)%20a65ecf374281479c81f5157dc64e0335/Untitled%201.png)

![Untitled](RNN(%E5%BE%AA%E7%8E%AF%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C)%20a65ecf374281479c81f5157dc64e0335/Untitled%202.png)

![Untitled](RNN(%E5%BE%AA%E7%8E%AF%E7%A5%9E%E7%BB%8F%E7%BD%91%E7%BB%9C)%20a65ecf374281479c81f5157dc64e0335/Untitled%203.png)

## **LSTM和一般的神经网络有什么关系？**

在一般的神经网络里有很多的神经元，会把input 乘上很多不同的权重，作为不同神经元的输入，每个神经元都是一个函数，输入一个值，输出另一个值。

把LSTM的memory cell 想成是一个神经元

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201031645-2093990544.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201031645-2093990544.png)

现在input $x_1,x_2$会乘上不同的权重，当做LSTM的不同的输入

例如上图第一个LSTM

- $x_1,x_2$乘上一组权重产生一个值，去操控输出门
- $x_1,x_2$乘上一组权重产生一个值，去操控输出门
- $x_1,x_2$乘上一组权重产生一个值，去操控遗忘门

对第二个LSTM也一样

所以刚才讲过LSTM有4个input，1个output，对LSTM来说4个input是不一样的。在原来的神经元里，就是1个input，1个output。

假设LSTM cell的数量和一般神经网络的神经元数量相同，那LSTM需要的参数量就是一般神经网络的4倍。

LSTM看起来也不像RNN，那它跟RNN有什么关系

## **LSTM跟RNN的关系是什么？**

假设有一整排的LSTM cell，每个cell的memory都存了一个值

把所有memory值接起来变成一个向量，写成$c^{t−1}$ ，memory cell里存的memory 值代表向量的一个维度

在时间点$t$ input一个向量$x^t$

- $x^t$首先乘上一个线性的转换函数（一个矩阵），变成另外一个向量$z$
    
    $z$的每个维度是每个LSTM cell的input，第一维丢给第一个cell，第二维丢给第二个cell，以此类推
    
- $x^t$再乘上另一个线性的转换函数（一个矩阵）得到$z^i$
    
    $z^i$ 的每个维度是每个LSTM cell的输入门的input值（来操控输入门打开的程度）
    
- $x^t$再乘上另一个线性的转换函数（一个矩阵）得到$z^f$
    
    $z^f$的每个维度是每个LSTM cell的遗忘门的input值（来操控遗忘门打开的程度）
    
- $x^t$再乘上另一个线性的转换函数（一个矩阵）得到$z^o$
    
    $z^o$的每个维度是每个LSTM cell的输出门的input值（来操控输出门打开的程度）
    

得到4个向量，每个向量的维度和cell 个数相同，那么4个向量合起来就会去操控所有memory cell的运作。

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201032081-1619103751.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201032081-1619103751.png)

**注意4个$z$都是向量，input到每个cell里的只是向量中的一个维度，那么丢进不同cell的是不同维度的数据**

虽然每个cell input的数据不一样，但是可以被一起运算

## **所有的cell怎么被一起运算？**

$z^i$通过激活函数的值，要乘以$z$，即计算当前时刻要保留的新信息

$z^f$通过激活函数的值，要乘以memory 保存的值，即计算上一时刻要保留的信息

$z^o$通过激活函数的值，即保留信息的输出程度

然后当前时刻要保留的新信息+上一时刻要保留的信息，再通过一个激活函数得到一个值，（这个值）乘（保留信息的输出程度）得到最后的输出$y^t$

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201032310-1153038077.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201032310-1153038077.png)

当前时刻要保留的新信息+上一时刻要保留的信息=当前时刻要保留的信息（memory里要存的值$c^t$）

当前时刻要保留的信息 ×保留信息的输出程度=要输出的信息（$y^t$）

计算过程反复继续下去，在下一个时间点input $x^{t+1}$

上图不是LSTM的最终形态，只是一个简单的版本

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201032620-998423601.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201032620-998423601.png)

真正的LSTM会把输出接进来，把隐藏层的输出当做下一个时间点的input ，下一个时间点操控门的值不只是只看$x^{t+1}$，还会看$h^t$。

还会加一个叫“peephole”的东西，peephole会把存在memory cell里的值也拉过来。所以在计算4个$z$的时候，同时考虑了$x,h,c$ 。$x,h,c$并在一起乘上4个不同的transform，得到4个不同的$z$向量，再去操控LSTM的门。

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201032869-1700655854.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201032869-1700655854.png)

LSTM通常不会只有一层，多层如上图所示

# **RNN架构如何学习**

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201033247-321338633.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201033247-321338633.png)

之前说过，**model要学习的话，要定义一个损失函数来评估model的参数是好是坏，选一个让损失函**数最小的参数。

## **在RNN里如何确定损失函数？**

假设我们的任务是做槽填充，给你一些句子的训练数据（词语和label）

把“arrive"input进RNN得到一个输出$y^1$ ，$y^1$会和一个reference 向量计算交叉熵。希望丢进去arrive后输出的$y^1$，对应到other=1，其余为0的reference 向量。reference 向量的维度是槽位的数目，有40个槽位，则reference 向量的维度为40。

把Taipei（$x^2$）input进去，希望$y^2$和reference 向量的距离越近越好，reference 向量里dest槽位为1，其余槽位为为0。这里要注意，input Taipei之前一定要先input arrive，不然就永远不知道memory里的值是多少了。

在训练的时候，不能把word sequence打散来看，word sequence仍要当成一个整体

**所以损失函数就是每一个时间点输出和reference 向量的交叉熵之和，去最小化。**

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201033426-1859130114.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201033426-1859130114.png)

**有了损失函数后怎么做？**

也是使用梯度下降，现在有损失函数$L$，使用梯度下降更新网络里的每一个参数$w$

在前馈神经网络里，为了实现高效率的梯度下降，设计出了BP算法

在RNN里，也有一种高效率的计算方式叫BPTT（Back propagation through time），是BP的进阶算法，和BP很类似，由于RNN是在时间序列上运作，所以BPTT考虑了时间的信息。

# **RNN学习中的问题**

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201033617-1313992933.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201033617-1313992933.png)

不幸的是RNN的训练是比较困难的

一般我们在训练的时候，希望学习误差可以像上图蓝线一样，随着迭代次数增加，误差减小，但是在训练RNN时，会出现上图绿线的情况，随着迭代次数增加，训练误差剧烈抖动，甚至出现NAN

**这个时候会有什么想法呢？**

第一个想法是程序有BUG啊

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201033811-113950014.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201033811-113950014.png)

**这个时候会有什么想法呢？**

如上图纵轴是Total Loss，x轴和y轴是两个参数$w_1,w_2$

发现RNN的误差超平面非常崎岖，有些地方非常平坦，有些地方非常陡峭，就像悬崖峭壁一样。

**RNN的误差超平面非常崎岖会造成什么样的问题?**

假设橙点（-2.2和-2.0之间的那个点）为初始点，使用梯度下降调整参数，可能跳过悬崖导致Loss暴增（上下剧烈震荡）。有时候会正好处于悬崖脚下，之前的梯度很小（学习率调地比较大），而该点的梯度很大，再乘以一个比较大的学习率，参数就飞出去了（更新过多）变成NAN，程序就出错了。

**怎么处理误差超平面非常崎岖的问题？**

使用裁剪，当梯度大于某一个阈值时就进行裁剪，比如当梯度大于15时裁剪为15。那就算是在悬崖脚上，使用的梯度也不会太大，参数不会飞出去。

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201033999-997078658.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201033999-997078658.png)

**为什么RNN会有这种奇特的特性？**

是不是可能来自于sigmoid函数，之前讲ReLU的时候，说过梯度消失的问题，并且是从sigmoid函数来的。可能想到使用Relu试试，但在实际训练RNN时，很少使用Relu，因为Relu的效果也是比较差的，所以激活函数不是这个问题的关键点。

有一个直观的方法来看一个梯度的大小，把某个参数做个小小的变化，看对输出的变化有多大，就可以知道这个参数梯度的大小。

**举一个简单的RNN例子：**

有一个最简单的RNN，只有一个线性的神经元，input没有偏置，input权重为1，output权重也为1，transition部分的权重为$w$ (memory 接到神经元的权重为$w$) 。

现在input [1 0 0 0 0 0 ...]，第一个时间段为1，接下来都是0，那最后一个时间点的输出$y^{1000}=w^{999}$

$w$是要训练的参数，需要知道它的梯度大小，即改变$w$的值，对输出有多大的影响。如果$w=1$，那$y^{1000}=1$ ，当$w=1.01$时，$y^{1000}\approx 20751$。意味着$w$有一点小小的变化，对output的影响非常大，所以$w$有很大的梯度。可能会想把它的学习率设小一点，梯度大也没什么关系。

但是事实上，如果把$w$设成0.99，那$y^{1000}\approx 0$，如果把$w$设成0.01，$y^{1000}$还是约等于0。也就是说，在1这个地方有很大的梯度，但在0.99这个地方梯度就变得非常小，这时候又需要一个很大学习率。这样就说明误差超平面很崎岖，设定学习率很麻烦，因为梯度时大时小，而且在非常短的区域内，梯度就会有很大的变化。

从这个例子看，RNN的问题来自于在transition的时候，不同时间点反复使用同样的东西（从memory接到神经元的$w$ )。导致$w$一有变化，有可能对梯度没有影响（0.99和0.01的情况），也有可能造成天崩地裂的影响（1.01的情况），造成梯度时大时小。

所以RNN不容易训练的问题不是来自于激活函数，而是来自于它有time sequence，同样的$w$在不同的时间点被反复使用。

# **如何解决RNN学习中的问题**

## **LSTM**

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201034213-795864150.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201034213-795864150.png)

**有什么技巧可以解决RNN训练的问题？**

现在最广泛使用的技巧就是LSTM，LSTM可以把误差超平面比较平坦的地方拿掉，可以解决梯度消失的问题，但是不会解决梯度爆炸的问题，有些地方仍然非常崎岖（变化非常剧烈）。LSTM让大部分的地方变化都很剧烈，就可以一直使用较小的学习率。

**为什么LSTM可以处理梯度消失问题？**

RNN和LSTM处理memory 的操作是不一样的

RNN在每个时间点，memory 的信息会被洗掉，然后存入当前时间点的信息

LSTM在每个时间点，是把原来memory的值乘上一个值（上一时刻信息保留程度），再加上当前时刻要保留的新信息，作为新的memory的值。LSTM的memory 和 input 是相加的。

如果weight可以影响memory的值，这种影响在RNN中的每个时间点会被重置掉，而在LSTM中影响会遗留下来，除非遗忘门关闭（=0，意味着洗掉上一个时刻保留信息），所以可以处理梯度消失问题。

**可是LSTM有遗忘门啊，遗忘门关闭的时候，就不能遗留影响了啊？**

LSTM第一个版本是没有遗忘门的，设计出来就是为了处理梯度消失问题，遗忘门是后来加上去的。

现在有说，要给遗忘门设置一个比较大的偏置，确保大部分情况下不是关闭的，只有少数情况会关闭洗掉memory的值。

### **GRU**

现在有另外一个用门操控memory cell的方法叫做门控循环单元（GRU），GRU只有2个门，因此需要的参数比LSTM少1/3，参数少在训练的时候会更鲁棒。如果在训练LSTM的时候觉得过拟合比较严重，那可以试一下GRU。

**GRU为什么可以少用一个门？**

精神就是旧的不去，新的不来，把输入门和遗忘门连动起来。当输入门打开的时候，遗忘门自动关闭（=0）， 存在memory的值会被洗掉，当遗忘门打开的时候，输入门关闭。要把存在memory的值清掉，才可以把新的值放进去

### **更多其他的方法**

![https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201034432-852329898.png](https://img2020.cnblogs.com/blog/1242619/202007/1242619-20200707201034432-852329898.png)

还有很多其他技术可以用来处理梯度消失问题，比如Clockwise RNN和SCRN等等。

还有一个，使用一般RNN，但是用单位矩阵初始化为transition的weight，然后使用ReLU作为激活函数，可以得到比LSTM好的效果。如果transition的weight是随机初始化的，那么使用ReLU会比使用sigmoid的效果差。