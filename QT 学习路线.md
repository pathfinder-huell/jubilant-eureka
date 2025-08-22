# Day One

## 第一.环境配置与运行流程.

​	学习了qt环境配置，了解了qt的编译过程。编译，生成，运行

写cpp，编译出project文件，最后mingw32- make 生成运行.exe。

## 第二.流程逻辑与布局

学习了使用qt creator 项目创建以及项目流程。新建qt widges Application 选择存放路径，编译器，以及类信息：明白了有菜单栏的是QmainWindow,不带菜单栏的的窗口是Qwidget. QDialog是对话框。

**理解了各个文件之间的关系逻辑。比如widget.ui会被   widget.h的ui命名空间下的widget类自动获取生成，随后在成员中生成一个私有ui::widget类型的 *ui 指针成员，该成员起到隔离的作用，防止ui中其他组件被污染，widget.cpp中的ui(new Ui:widget)初始化了ui指针，让指针有了widget.h中所自动获取类中的所有内容，此时可通过调用setupUi(this)函数实现运行，又或者是通过ui调用控件对象和信号与槽配合。**

```
namespace Ui { class Widget; }
private:
    Ui::Widget *ui;


ui(new Ui::Widget)
{
    ui->setupUi(this);
}
```

相当于是ui让h获取，h又做了一个中转，cpp链接到中转，最后调用其实是相当于又回到了ui.

理解了画图设计中 

layout是布局，

spacers是垫子。

button

item views(model-based)单元视图数据可用到。

item widgets折叠管理部件

containers 容器，容纳固件的

input widgets 输入部件

display widgets 显示部件

通过画布生成了第一个窗口

![image-20250806230354899](C:\Users\Fabious_Bile\AppData\Roaming\Typora\typora-user-images\image-20250806230354899.png)

## 第三.信号与槽

有四种方式配置信号与槽

1.画图界面右击控件

所有的槽函数都需要在头文件中加上私有slots成员与点击函数声明，点击按钮触发后会执行对应函数 ， 在对应函数中实现命令输入的功能

```

void Widget::on_commitButton_clicked()
{
    //获取文本框数据
   QString program = ui->cmdLineEdit->text();
    //创建了一个process对象
    QProcess *myProcess= new QProcess(this);
    myProcess->start(program);
}
```

通过qt自带的字符串定义了一个对象用来获取文本输入内容，随后通过指向ui(画图)中的ui指针调用ui(画图)内的控件；

Qprocess 暂时不清楚，没说是干什么的。似乎是运行文件的。

2.使用connect链接信号与槽

在构造函数里  信号与槽是两个独立的东西，信号就是按扭点击事件产生信号，槽呢是一个函数。

所以需要链接信号与槽，就需要用到connect()；这个函数呢又有4个参数，谁发出信号，发出了什么信号，谁处理这个信号，怎么处理这个信号。

```
//链接信号与槽  谁发出信号，发 出了什么信号，谁处理这个信号，怎么处理这个信号。
connect( ui->cmdLineEdit,SIGNAL(returnPressed()),this,SLOT(on_commitButton_cliked()))；
```

通过宏来写？

通过地址来写.

父类this

```
 QMessageBox::information(this,"信息","点击浏览");
```





问题待解决，main与其他文件起到什么作用，通过哪段代码造成什么影响。头文件中的qwkdget和公共类的有什么关系，都是做什么的。总之就是搞懂相互起到了什么作用。

还得问下this是怎么管理窗口的。

#  Day Two

### 一.学习写四则运算计算器

在画图页面拉去按钮控件和显示控件。计算器需要明确思路，第一呢是你要有加减乘除模块，第二呢是数字输入模块，第三呢是显示输入模块，第四呢是你需要点击等于号才进行计算让显示模块更新。

所以需要先定义一个显示模块将所有输入显示出来，这个就需要用到字符串了。

那么先定义一个字符串用于接收计算之前的字符，随后在数字按钮下开启槽，槽中将对应数字添加进字符串中；然后调用显示模块将字符串参数传入。

有了添加，那是不是得有删除和清空吗，那么就把字符串清空就好了，当然显示控件中的数据也得清空。

还学习了在按钮上添加·图片

```
  QIcon con(""); //图片路径
  ui->delButton->setIcon(con);
```

但是计算器的代码计算逻辑并没有讲，我有些许苦恼。

并且我发现，很多功能都在固定的函数中，比如对于字符串进行修改的qstring，以及显示控件函数控制显示。虽然感觉很庞大，但是有这个分类思想就可以先了解有哪些功能，随后进行熟悉。

## 二.定时器QObject

​     **因为此定时器继承自qobject，而qwidget又继承自qobject，所以我们可以通过this指针去调用存在于qobject中的定时器提供的startTime 与 KillTime函数。**

​        要用定时器，你得先开启定时器函数，并且定义一个时间来放入。一旦时间到了就会发出一个定时器事件。这个事件其实也是一个函数，

**要实现这个函数的话**

​	需要继承 `QWidget` 提供的虚函数 `timerEvent()`做为响应事件，当我调用 `startTimer(ms)` 启动定时器后，**Qt 框架会在定时器超时 时自动调用我的重写函数**，我只需要在重写的函数里写我要做的动作就可以了。

但有多个定时器的话，每个定时器都会给出定时器编号，在重写函数中判断相对应的定时器编号即可。 

```
触发时会自动调用你重写的 `timerEvent()`。

多个定时器用 `event->timerId()` 来区分。

不能直接接收信号，也不能用 lambda。

一种“底层风格”的定时器。
```



## 三,定时器Qtimer

这个定时器是来自objcet单独提供的一个类。因为是单独的一个类，所以需要一个单独的对象来运行。在头文件中包含该类的头文件，随后将对象创建到私有成员中设置为QTimer类型的指针。，在初始化对象后，调用启动函数并传时间，配合槽函数连接与匹配响应事件(slot)。

内部也是用系统定时器，但通过信号槽封装

```
不用重写任何函数
connect(发送者对象, 信号, 接收者对象, 槽函数);
可以多个实例共存

	QTimer *timer = new QTimer(this);
	connect(timer, &QTimer::timeout, this, &Widget::updateUI);
	timer->start(1000); // 每1000毫秒（1秒）触发一次timeout信号

支持 stop()、setInterval()、singleShot() 等控制方式
```

QObject
 ├── 提供 startTimer()
 │    └── 必须重写 timerEvent()
 └── QTimer : public QObject
      ├── 自带信号 timeout()
      ├── 支持 start/stop
      └── 自动调用 slot，无需 timerId 判断

## 四.三种图片显示

**显示选 Pixmap，处理用 Image。前者轻巧快，后者能修改。**

Qpixmap显示速度快

用于**显示**图像依赖底层图形系统（如 GPU），效率高、占内存小，适合在 UI 控件里显示（如 `QLabel`）

Qimage适合大图片

用于**处理**图像,像素级可操作，适合修改图像（如滤镜、图像识别、处理、保存等）

```
都可以先构造或不构造再加载
QPixmap pix("xxx.jpg");   // 直接加载图像
QImage img("xxx.jpg");    // 同样能加载图像

QPixmap pix;
pix.load("xxx.jpg");

QImage img;
img.load("xxx.jpg");
```

**图片使用场景**

用QLabel推荐使用`QPixmap`（直接使用 `setPixmap()`）

用自定义绘图推荐`QImage` 更灵活，可与 `QPainter` 配合使用

**图片相互转换**

QImage → QPixmap `QPixmap::fromImage(img)` 使用image 方式渲染 pixmap

QPixmap → QImage `pixmap.toImage()`  使用pixmap 方式渲染 image

```
QImage img("xxx.jpg");
QPixmap pix = QPixmap::fromImage(img); // QImage → QPixmap

QPixmap pix("xxx.jpg");
QImage img2 = pix.toImage();           // QPixmap → QImage
```



## 五.发现现代和旧写法

connect( ui->cmdLineEdit,SIGNAL(returnPressed()),this,SLOT(on_commitButton_cliked()))；

这个写法使用的是 Qt4/Qt5 时代早期的宏机制，它并不是 C++ 编译器识别的“函数指针”，而是：

- `SIGNAL(returnPressed())` 是一个 宏，它把 `returnPressed()` 转成了字符串 `"returnPressed()"`。
- `SLOT(on_commitButton_clicked())` 也是一个 宏，变成 `"on_commitButton_clicked()"`。

最终 Qt 内部用这两个字符串匹配信号和槽，靠的是字符串名称匹配 + 运行时查找机制。



connect(ui->cmdLineEdit, &QLineEdit::returnPressed, this, &Widget::on_commitButton_cliked);

信号的函数指针，指向 `QLineEdit` 类中的 `returnPressed` 信号槽函数指针，指向你自己类里的槽函数

不知道还有哪些现代和旧写法，对未来学习复杂度有什么影响

第二天利用两个定时器两个图片方式写了两个定时相框。原理是定时器到时间，调用对应槽或事件处理函数，随后在槽中写上图片切换以及每次定时器到时间增加图片id。还学习了QTimer中的单次点击函数singleShot用法。

# Day Three

## 认识文件操作

**Qt 文件处理与 QFileDialog 基础**

（结合你这段代码，知识可以整理成这三块）

------

### **1. 文件对话框 QFileDialog 的使用**

**核心功能**：让用户选择要打开或保存的文件路径。

- **常用函数**

  1. `getOpenFileName(...)` → 选择并返回要打开的单个文件路径。

  2. `getSaveFileName(...)` → 选择保存位置和文件名（"另存为"功能）。

  3. 参数解析：

     ```
     cpp复制编辑QFileDialog::getOpenFileName(
         this,                              // 父窗口
         tr("选择文件"),                    // 对话框标题
         QCoreApplication::applicationFilePath(), // 默认路径（这里是程序路径）
         tr("*.cpp")                        // 文件类型过滤器
     );
     ```

     - `tr()`：Qt 国际化函数（翻译用），你可以把里面的字符串翻译成不同语言。
       - 第一个 `tr` 是窗口标题翻译用
       - 第二个 `tr` 是文件类型描述翻译用
     - `QCoreApplication::applicationFilePath()`：返回当前程序的绝对路径（常用于默认路径设置）。

------

### **2. 文件访问权限（QIODevice 模式）**

`QFile::open()` 第二个参数是访问模式，由 `QIODevice` 提供枚举值：

- **常用模式**

  - `QIODevice::ReadOnly` → 只读

  - `QIODevice::WriteOnly` → 只写（覆盖原内容）

  - `QIODevice::Ap pend` → 追加写入（保留原内容）

  - 可以用 `|` 组合，比如：

    ```
    QIODevice::WriteOnly | QIODevice::Append
    ```

------

### **3. 文件读取与写入**

**主要对象**：

- `QFile` → 文件抽象类，负责文件打开、关闭、读写。
- `QByteArray` → 原始字节容器，适合文件、网络传输。
- `QString` → Unicode 字符串，适合界面显示。

**读取文件**

```
cpp复制编辑QFile file(fileName);
file.open(QIODevice::ReadOnly);
QByteArray ba = file.readAll();        // 全部读入内存
ui->textEdit->setText(QString(ba));    // 转成 QString 显示
file.close();
```

**写入文件**

```
cpp复制编辑QFile file(fileName);
file.open(QIODevice::WriteOnly);
QByteArray ba;
ba.append(ui->textEdit->toPlainText()); // QString → QByteArray
file.write(ba);
file.close();
```

------

### **4. Open 与 Save 的核心区别**

| 功能点     | Open                                | Save                   |
| ---------- | ----------------------------------- | ---------------------- |
| 对话框函数 | `getOpenFileName`                   | `getSaveFileName`      |
| 打开模式   | `ReadOnly`                          | `WriteOnly` / `Append` |
| 数据流方向 | 文件 → 内存（QByteArray → QString） | 内存（QString）→ 文件  |
| 主要用途   | 读取文件内容到控件                  | 保存控件内容到文件     |

✅ 记住文件操作三步走：**选择路径 → 打开文件 → 读/写数据**
 ✅ 文件数据流是 **QByteArray**（字节流），文本框数据是 **QString**（UTF-16）
 ✅ 打开模式必须正确匹配用途（ReadOnly / WriteOnly / ReadWrite / Append）
 ✅ QFile 只是文件对象，真正的 I/O 接口来自 QIODevice

## 事件实现文件保存

application 能够知道哪个窗口在哪个位置，能知道应该由哪个窗口来接收消息，这个窗口会调用自己的消息处理函数。
窗口的 event()函数处理所有经过窗口的消息消息处理函数是虚函数，使用要进行重载。

QT将系统产生的消息转化为 QT事件，QT事件被封装为对象，所有的QT事件均继承抽象类 @Event，用于描述程序内部或外部发生的动作，任意的Q0bject对象都具备处理 QT 事件的能力

所有的事件都可以重写event虚函数调用。

获取鼠标定位。

# Day Four

## 1.TCP客户端

创建一个QTscoket的对象来调用connect的函数发起连接。，然后槽函数判断连接成功或失败的信号。

```
void Widget::on_connectButton_clicked()
{
        QString IP = ui->ipLineEdit->text();
        QString port = ui->portLineEdit->text();

        socket->connectToHost(QHostAddress(IP),port.toShort());

        connect(socket,&QTcpSocket::connected,[this]()
        {
           QMessageBox::information(this,"连接提示","连接成功");` 
        });

        connect(socket,&QTcpSocket::disconnected,[this]()
        {
           QMessageBox::warning(this,"连接提示","连接失败");
        });
}
```



## 2.TCP服务器

需要有个server头文件,监听地址,一旦监听到被监听发起连接就会发出一个新的连接信号，随后调用槽函数创建tcp连接，tcp连接也是一个对象，我们定义一个QTcpsocket对象返回值去接收tcp连接对象，在我们的tcp连接中还包含的有客户端ip与端口号。 	

​	

# Day Five

## 一.QT 启动新窗口





## 二.线程是怎么使用的

首先得创建一个myThread文件作为线程类，继承Qt提供的线程 QThread类。

在myThread重写一个run函数=线程处理函数。

```
class myThread : public QThread
{
    Q_OBJECT
public:
    explicit myThread(QTcpSocket *s);
    void run(); //线程处理函数

```

随后在cpp处线程处理实现一个connect接收客户端发送的信号。

```
void myThread::run()
{
    connect(socket,&QTcpSocket::readyRead,this,&myThread::clientInfoSlot);
    exec();
}
```

随后在槽函数中处理。

```
void myThread::clientInfoSlot()
{
   qDebug()<< socket->readAll();
}

```

 在建立tcp连接的函数定义域内，创建一个线程

```
QTcpSocket *socket = server->nextPendingConnection();
```

通过构造函数传入socket把指针传入。随后调用start函数，start函数会自动调用fun.

    myThread *t = new myThread(socket); //创建线程对象
     t->start();     //开始线程

这就实现了一个线程新建

## 三.多线程，自定义信号

界面不能在其他类里面操作界面.

把socket的信号存入二进制处理ba存储，后通过emit 发射已定义好的信号，并传递 `ba` 作为参数

```
	qDebug()<< socket->readAll();
    QByteArray ba=socket->readAll();
    emit sendToWidget(ba); 
```

emit发送的前提是在对应的头文件中自定义信号名称之后

```
signals:
    void sendToWidget(QByteArray b);
```

随后在connect函数中连接从另一个线程传来的信号。并且调用槽函数在主线程处理

```
    myThread *t = new myThread(socket); //创建线程对象
  t->start();                 //开始线程
    connect(t,&myThread::sendToWidget,this,&Widget::threadSlot);
```

## 四moveThread使用，不重写fun()

```
class Worker : public QObject {
    Q_OBJECT
public slots:
    void doWork() {
        qDebug() << "工作线程:" << QThread::currentThreadId();
        // 这里写你的逻辑，比如读取 socket

​        // 什么时候发送信号？
​        QByteArray ba; 
​        // ... 你可能在这里获得 ba，比如读socket数据
​        emit sendToWidget(ba);  // 这句就是发信号，触发槽调用
​    }

signals:
    void sendToWidget(QByteArray ba);
};
```

把worker线程移动到新创建的线程，随后当线程启动后，接收到开始信号，让worker运行槽函数，槽函数内有自定义的emit信号发送，需要子线程到主线程所以我们让woker接收到槽函数发送的信号，随后调用主线程中的槽threadSlot。实现了将子线程的处理调用到主线程。

`threadSlot` 运行在主线程，适合更新界面元素

处理子线程传来的数据，比如：

- 解析协议内容
- 校验数据正确性
- 根据数据状态做逻辑判断，决定下一步动作 例如发起网络请求、播放提示音

```
QThread* thread = new QThread;//新建线程
Worker* worker = new Worker;

// 把 worker 移动到新线程
worker->moveToThread(thread);

// 当线程启动时，让 worker 开始工作
connect(thread, &QThread::started, worker, &Worker::doWork);

// Worker 发信号给主线程
connect(worker, &Worker::sendToWidget, this, &Widget::threadSlot);

thread->start();
```

# Day Six

## 一.服务器与Qt