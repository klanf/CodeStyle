# CodeStyle
简介：个人代码风格模板，核心是MVC的框架，复杂业务会考虑抽取出ViewModel层

## 我对model层的理解：
 
 一、数据承载：fat model
 
 1. 原因 ：
    因为从接口获取到的数据(json/xml等)通常是不能直接使用的数据，
    而是需要进行二次转换才可以给view直接使用，
    这部分需要二次转换的代码就可以写到model上
 
 2. 特点：处理模型本身的数据，弱业务(如数组排序，去重，时间戳转换等)
 
 3. 优点：<br>
 
    1）可以避免ViewController因数据转换而变的臃肿，
       让其更专心于协调model层与view层的数据交换；
 
    2）如果这部分代码在别处也有使用可以直接移植过去，
       使用频率高的可以考虑抽取这部分代码写成一个模块

 PS：强弱业务的划分：当业务层有改变时，随着这个改变所需要的改动越多，业务关联就越强时，它就是强业务，反之为弱业务。 -- casa Taloyum
 
 二、 构建网络层
 
 1. 可以把散落在各个ViewController中的数据请求代码统一抽取放到Model层来构建网络层，以减轻ViewController的负担,对业务数据和业务逻辑进行隔离
 
 2. 在ViewController发起网络请求后，网络层调用相应代码，并通过代理的方式将网络层获取到的数据回调给ViewController，这么做可以统一回调方法，便于调试和维护
 
 三、 数据持久化
 
 1. 对数据的增删改查也是model层进行维护的
 
 2. 使用NSUserDefault、KeyChain、File，以及基于数据库的无数子方案进行持久化
 
 四、 模块化
 
 1. 把业务的通用逻辑抽象出来，写到model层，碰到相似的业务直接使用就行
 
 
 五、 总结
 
    Model层要做的事情就是从网络获取数据、从数据库获得数据，然后将其转化为View层可以理解的方式（对象，字典等）
    
## 我对view层的理解：

 一、理解
 
 1. 并不是在UI上显示出来的view就是一个View层，而是根据业务划分成一个个子模块后抽取出来的，一个View层是实现一个业务的view的集合
 
 2. 负责与用户交互，并处理由此产生的数据，然后交给ViewContrler
 
 二、职责：
 
 1. 负责呈现从ViewController给的数据
 
 2. 负责处理与用户交互的所产生的数据(由用户点击，手势等产生的数据)，并将处理好的数据通过代理反馈给ViewController
 
 3. 如果业务需求复杂，可以考虑抽出ViewModel层，ViewModel将作为一个Adaptor，负责提取和转化给Model层能识别的数据
 
  三、 总结
 
    View层要做的事情很简单，就是做数据的可视化处理及反馈用户操作产生的数据
  
  
## 我对ViewModel的理解：
 
 1. ViewModel实质上是在Fat Model变得比较庞大时进行拆分的产物，它的拆分对象是Model；数据流向应该是：Model层请求网络数据，回调数据给
 ViewController后，根据业务调度到相应的ViewModel，经过ViewModel转化好的数据直接传到绑定的View层
 
 2. ViewModel是作为Adaptor存在的，Model层的数据一般要经过二次转化才能给View层使用，而View层产生的数据也要通过处理才能直接交给Model层使用，
    即ViewModel是负责数据提取和转化为View或Model能够理解的对象、json（举例个例子，对于view来说，没经过转化的json数据的“0”对应转化后的“YES”，
    那么转换“0”到“YES”的过程就由ViewModel承担），这样子做的话，ViewController只需调度经过转换的数据既可以了，多余的转化代码就可以都扔给ViewModel去做
 
 3. ViewModel处理的大部分是View层的事件响应
    
 4. 不要把ViewModel当作Model来用，ViewModel的职责在于转化数据,涉及到增删改查的都要交给Model去处理
    
    
    
    
    
    
    
    
