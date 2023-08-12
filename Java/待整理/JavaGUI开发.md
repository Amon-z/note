# JavaSE笔记 GUI程序开发

## AWT组件

### 基本框架

&emsp;&emsp;窗口及常用属性

```java
public static void main(String[] args) {
    Frame frame = new Frame();   // Frame是窗体，创建对象就会直接创建一个新窗口
    frame.setTitle("Title");   // 设置窗口标题
    frame.setLocation(100, 200);   // 设置整窗口位置
    frame.setSize(500, 300);   // 设置窗口大小
    // frame.setBounds(100, 200, 500, 300); // 设置窗口位置和大小，等同于上面两句
    frame.setBackground(Color.PINK);   // 设置窗口背景颜色
    frame.setResizable(false);    // 设置窗口大小是否固定
    frame.setAlwaysOnTop(true);    // 设置窗口是否始终展示在最前面
    frame.setVisible(true);    // 设置窗体可见性，默认情况下窗不可见的
}
```

&emsp;&emsp;让窗口在不同电脑上都能居中

```java
public static void main(String[] args) {
    Frame frame = new Frame("Title");
    frame.setSize(500, 300);
	// 获取到屏幕尺寸，计算窗口位置
    Dimension screenSize = Toolkit.getDefaultToolkit().getScreenSize();  
    int x = (int) ((screenSize.getWidth() - frame.getWidth()) / 2);   
    int y = (int) ((screenSize.getHeight() - frame.getHeight()) / 2);
    frame.setLocation(x, y);   //设置窗口位置
    frame.setVisible(true);
}
```

### 监听器

&emsp;&emsp;我们可以为窗口添加一系列的监听器，监听器会监听窗口中发生的一些事件，比如我们点击关闭窗口、移动鼠标、鼠标点击等，当发生对应的事件时，就会通知到对应的监听器进行处理，从而我们能够在发生对应事件时进行对应处理。

&emsp;&emsp;给frame添加监听器，实现点x号关闭窗口

```java
frame.addWindowListener(new WindowAdapter() {
    @Override
    public void windowClosing(WindowEvent e) { // windowClosing方法对应窗口关闭事件
        frame.dispose(); // 点击X号关闭窗口时，调用dispose()方法关闭窗口    
    }
    @Override
    public void windowClosed(WindowEvent e) {   //对应窗口已关闭事件
        System.out.println("窗口已关闭！");   // 当窗口成功关闭后，会执行这里重写的内容
      	System.exit(0);    //窗口关闭后退出当前Java程序
    }
});
```

&emsp;&emsp;窗口常用事件

```java
public interface WindowListener extends EventListener {
    public void windowOpened(WindowEvent e);  // 当窗口的可见性首次变成true时会被调用 
    public void windowClosing(WindowEvent e);   // 当点击x号时被调用
    public void windowClosed(WindowEvent e);   // 窗口被我们成功关闭之后被调用
    public void windowIconified(WindowEvent e);  // 窗口最小化时被调用
    public void windowDeiconified(WindowEvent e); // 窗口从最小化状态恢复时调用
    public void windowActivated(WindowEvent e);    // 当窗口变成活跃状态时被调用
    public void windowDeactivated(WindowEvent e);   // 当窗口变成不活跃时被调用
}
```

### 常用组件

&emsp;&emsp;标签

```java
Label label = new Label("标签");   // 添加标签只需要新建一个Label对象
label.setBounds(50, 50, 100, 50); // 这是展示空间大小，标签的实际大小要由系统决定
label.setFont(new Font("SimSong", Font.BOLD, 15));  // 设置标签字体
// Font构造方法参数：字体名称、字体样式（加粗、斜体）、字体大小
label.setBackground(Color.BLACK);    // 设置背景颜色
label.setForeground(Color.WHITE);    // 设置字体颜色
frame.add(label);    // 使用add方法添加组件到窗口中
```

&emsp;&emsp;按钮

```java
Button button = new Button("按钮");
button.setBounds(20, 50, 100, 50);
button.addActionListener(e -> System.out.println("按钮点击事件"));  // 添加监视器
frame.add(button);
```

&emsp;&emsp;文本框

```java
TextField field = new TextField();
field.setBounds(20, 50, 100, 25);
field.setEchoChar('*');   // 设定展示字符，无论输入什么都会展示指定的字符
frame.add(field);
```

&emsp;&emsp;勾选框

```java
Checkbox checkbox = new Checkbox("记住密码");
checkbox.setBounds(20, 50, 100, 30); // 这是展示空间大小，勾选框的实际大小由系统决定
frame.add(checkbox);
```

&emsp;&emsp;多选框

```java
CheckboxGroup group = new CheckboxGroup();   // 创建勾选框组
// 勾选框1 
Checkbox c1 = new Checkbox("选我");
c1.setBounds(20, 50, 100, 30);
frame.add(c1);
//勾选框2
Checkbox c2 = new Checkbox("你干嘛");
c2.setBounds(20, 80, 100, 30);
frame.add(c2);
//将勾选框加入到勾选框组中
c1.setCheckboxGroup(group);    
c2.setCheckboxGroup(group);
// 输出已经被选中的勾选框
System.out.println(group.getSelectedCheckbox()); 
```

### 布局

- BorderLayout

  - BorderLayout布局的容器某个位置的某个组件会直接充满整个区域。

  - 如果在某个位置重复添加组件，只有最后一个添加的组件可见。

  - 缺少某个位置的组件时，其他位置的组件会延伸到该位置。

```java
BorderLayout b = new BorderLayout();
borderLayout.setHgap(50);   // Hgap是横向间距
borderLayout.setVgap(50);   // Vgap是纵向间距
frame.setLayout(b);   // 使用BorderLayout布局
frame.add(new Button("1号按钮"), BorderLayout.WEST);  // 添加组件时加入约束
frame.add(new Button("2号按钮"), b.EAST);
frame.add(new Button("3号按钮"), b.SOUTH);
frame.add(new Button("4号按钮"), b.NORTH);
frame.add(new Button("5号按钮"), b.CENTER);
```

- FlowLayout
  - 按顺序排列，排不下自动换行

```java
FlowLayout f = new FlowLayout();
frame.setLayout(f); // 使用FlowLayout布局
f.setHgap(50);	// 设置横向间距
f.setVgap(0); // 设置纵向间距
f.setAlignment(FlowLayout.LEFT);  // 设置对齐方式
```

- CardLayout
  - 一次只显示一个卡片，其他位于下方
  - 可以将下面的卡片切换到上面来

```java
CardLayout c = new CardLayout();
frame.setLayout(c);
frame.add(new Label("我是1号"));
frame.add(new Label("我是2号"));
frame.setVisible(true);
while (true) { 
    Thread.sleep(3000);
    c.next(frame); // 每3秒切换一次卡片
}
```

- GridLayout
  - 以矩形网格形式对组件进行管理
  - 默认情况下生成一行按格子划分的相等区域
  - 也可以手动指定行数和列数

```java
GridLayout g = new GridLayout();
g.setRows(2);  // 设置行数
g.setColumns(2);  // 设置列数
frame.setLayout(gridLayout);
```

- GridBagLayout
  - 同样按网格形式划分
  - 一个组件可以占一个或多个网格

```java
GridBagLayout g = new GridBagLayout();
```

### 布局中组件大小

&emsp;&emsp;当布局管理器在自动调整内部组件大小时，如果不是必须要按照布局大小来展示或者是高度或宽度不确定，那么就会采用我们建议的大小展示。

```java
button.setPreferredSize(new Dimension(100, 50));   // 设置首选大小
```

### 面板

- Panel
  - 面板本身也是容器
  - 可以单独设置面板内的布局

```java
Panel p1 = new Panel(new BorderLayout); // 创建一个面板
Panel p1 = new Panel(new BorderLayout); 
Panel p2 = new Panel(new FlowLayout);
```

### 滚动面板

- ScrollPanel
  - 滚动面板也是一个容器
  - 无法修改它的布局
  - 只能容纳单个组件
  - 可以与图片、列表或面板配合使用

```java
ScrollPane s = new ScrollPane();   // 创建滚动面板
frame.add(s);
// 创建滚动面板内部的要展示的面板
GridLayout layout = new GridLayout();    
layout.setRows(20);
Panel panel = new Panel();
panel.setLayout(layout);
for (int i = 0; i < 20; i++)
    panel.add(new Button("我是按钮"+i));   // 为面板添加按钮
// 将面板放入滚动面板，无法显示的部分滚动即可展示
scrollPane.add(panel); 
```

### 列表

- list

```java
List list = new List();   //注意是awt包下的List，别导错了
list.add("列表项一");
list.add("列表项二");
list.add("列表项三");
list.add("列表项四");
list.setMultipleMode(true);   //是否开启多选模式
list.addItemListener(System.out::println);  // 监听器，当我们选择某个物品时触发
```

### 菜单栏

```java
MenuBar bar = new MenuBar();    // 创建菜单栏 
Menu menu = new Menu("我是菜单");	// 菜单项
menu.add(new MenuItem("测试1")); // 菜单下拉选项
// 为选项二设置监听器
MenuItem item2 = new MenuItem("测试2");
item.addActionListener(e -> System.out.println("二号选项被点击了！"));
// 为选项三指定快捷键Ctrl + A
MenuItem item3 = new MenuItem("测试3");
item.setShortcut(new MenuShortcut('A')); 
// 可勾选的选项
menu.add(new CheckboxMenuItem("测试4"));
// 为窗口设定刚刚定义好的菜单栏
menu.add(item2);
menu.add(item2);
bar.add(menu);
frame.setMenuBar(bar);
```

### 对话框

### 自定义组件

### 窗口修饰和自定义形状

## Swing组件

### 基本使用

### 新增组件介绍

### 多面板和分割面板

### 选项窗口

### 自定义主题



