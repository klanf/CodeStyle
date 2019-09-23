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
 
 二、职责：
 
 1. 负责呈现从ViewController给的数据
 
 2. 负责处理与用户交互的所产生的数据(由用户点击，手势等产生的数据)，并将处理好的数据通过代理反馈给ViewController，ViewController在调度给相应的Model
 
 3. 如果业务需求复杂，可以考虑抽出ViewModel层，ViewModel将作为一个Adaptor，负责提取和转化给Model层能识别的数据
 
  三、 总结
 
    View层要做的事情很简单，就是做数据的可视化处理及反馈用户操作产生的数据
  
  
## 我对ViewModel的理解：
 
 1. ViewModel实质上是在Fat Model变得比较庞大时进行拆分的产物，它的拆分对象是Model；数据流向应该是：Model层请求网络数据，回调数据给
 ViewController后，根据业务调度到相应的ViewModel，经过ViewModel转化好的数据直接传到绑定的View层
 
 2. ViewModel是作为Adaptor存在的，Model层的数据一般要经过二次转化才能给View层使用，而View层产生的数据也要通过处理才能直接交给Model层使用，
    即ViewModel是负责数据提取和转化为View或Model能够理解的对象、json（举例个例子，对于view来说，没经过转化的json数据的“0”对应转化后的“YES”，
    那么转换“0”到“YES”的过程就由ViewModel承担），这样子做的话，ViewController只需调度经过转换的数据既可以了，多余的转化代码就可以都扔给ViewModel去做
 
 3. ViewModel处理的大部分是View层的事件响应(由用户点击，手势等）的数据
    
 4. 不要把ViewModel当作Model来用，ViewModel的职责在于转化数据,涉及到增删改查的都要交给Model去处理
    
## 我对ViewController的理解：

ViewController我都会按照下面的规范写。ViewController的职责承载了非常多的事情，比如View的初始化，业务逻辑，事件响应，数据加工等等，但主要的职责还是协调V和M；除了这部分代码，其他的代码都是可以进行拆分的，下面是我的ViewController的写作风格。


@interface ViewController ()

@end

@implementation ViewController

#pragma mark - Life Cycle
/**
 * 这里负责管理view的生命周期，负责model和view的初始化、接收通知等
 */
- (void)viewDidLoad {
    [super viewDidLoad];
    // Do any additional setup after loading the view.
    
    
}

- (void)viewWillAppear:(BOOL)animated {
    [super viewWillAppear:animated];
    
}

//。。。

#pragma mark - UITableViewDataSource and UITableViewDelegate
/**
 * 这里负责系统的代理回调代码，其中UITableViewDataSource是可以拆成一个模块的，DataSource其实可以视为一个数组，通过维护这个数组就可以达到维护DataSource的需求（特别是多个cell的业务场景会导致写很多的if-else语句，然后造成这部分代码非常的长，抽出后可以简化这部分代码）；
 */

#pragma mark - Custom Delegate

/**
 * 这里负责自定义的回调代码，如网络请求的回调，自定义view的回调等；
 */

#pragma mark - Event Response

/**
 * 这里负责对事件响应的处理，如view的点击事件，手势触发的事件，处理通知等都可以写在这里；
 */

#pragma mark - Private Methods

/**
 * 不是delegate方法的，不是event response方法的，不是life cycle方法的，就是private method了。对的，正常情况下ViewController里面一般是不会存在private methods的，这个private methods一般是用于日期换算、图片裁剪啥的这种小功能。这种小功能要么把它写成一个category，要么把他做成一个模块，哪怕这个模块只有一个函数也行。
 
 ViewController基本上是大部分业务的载体，本身代码已经相当复杂，所以跟业务关联不大的东西能不放在ViewController里面就不要放。另外一点，这个private method的功能这时候只是你用得到，但是将来说不定别的地方也会用到，一开始就独立出来，有利于将来的代码复用。
 
 -- by Casa Taloyum
 
 */

#pragma mark - GET & SET

/**
 *

 对于一个ViewController来说可能会有很多的view，这部分view的初始化代码加起来就会很长，而且一般初始化后，GET & SET就完成它的任务，一般也不会多次来到这里看代码，所以在打开ViewController后直接看到主要的业务代码在我看来是比较省心的
 */

    
    
    
    
    
