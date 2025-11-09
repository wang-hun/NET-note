***WPF进阶 MVVM工具的简单使用***
### 1.MVVM模式🧨

```
MVVM模式是一种软件架构模式，MVVM模式把UI界面与数据逻辑分离，
将数据逻辑与UI界面分离，将数据逻辑与数据分离。
MVVM模式的核心是数据绑定，
数据绑定是MVVM模式中最重要的功能，
它使得UI界面与数据逻辑之间的交互变得更加简单和高效。
```
简单来说，如果你知道MVC🍊，那么MVVM就是一种升级的MVC，model只负责提供数据，view负责显示数据，viewmodel负责处理数据。不论是View还是Viewmodel层对model进行修改，都会自动通知对方，实现数据同步，不需要我们开发人员手动处理数据的更新和传递。

### 2.MVVM工具 🧰
我们知道，在wpf中要实现双向绑定。需要我们手动实现监听👀、访问器和消息发送📧。
而有一款开源的工具，可以帮我们自动实现这些功能，那就是MVVM Toolkit。你可以直接在Nuget🌐上搜索到它，搜索MVVM就能搜到。

### 3.MVVM Toolkit的简单使用
我们先创建一个WPF项目，然后安装MVVM Toolkit🧰。
然后创建三个文件夹，Views、ViewModels、Models📁。
把创建的MainWindow.xaml移动到Views文件夹下🕒。

我们写一个简单的界面✍️，包含三个文本框，一个按钮。注意一下路径和对应的命名空间，如果你按照了目录格式修改了命名空间，那么x:Class应该是MVVMTest.Views.MainWindow。
```Html
<Window x:Class="MVVMTest.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:MVVMTest"
        mc:Ignorable="d"
        Title="MainWindow" Height="140" Width="520" ResizeMode="NoResize">
    <Grid>
        <StackPanel>
            <Grid>
                <StackPanel Orientation="Horizontal">
                    <Label Content="A"/>
                    <TextBox Width="500" />
                </StackPanel>
            </Grid>
            <Grid>
                <StackPanel Orientation="Horizontal">
                    <Label Content="B"/>
                    <TextBox Width="500" />
                </StackPanel>
            </Grid>
            <Grid>
                <StackPanel Orientation="Horizontal">
                    <Label Content="C"/>
                    <TextBox Width="500" />
                </StackPanel>
            </Grid>
            <Grid>
                <Button Content="计算" />
            </Grid>
        </StackPanel>
    </Grid>
</Window>
```
然后我们新建一个Model类。
```CSharp
 public partial class Model:ObservableObject
 {
     [ObservableProperty]
     public int a;
     [ObservableProperty]
     public int b;
     [ObservableProperty]
     public int c;
 }
```
需要注意的是⚠️，Model类需要继承ObservableObject类，并且标记为partial。然后使用ObservableProperty特性来标记我们希望双向绑定的属性🍏。

千万不要忘记此类标记为partial，否则MVVM Toolkit会扔出对应的错误，因为MVVM Toolkit会自动生成类的另外一部分，这部分代码必须是partial的，虽然你看不到它，它也不会出现在你的的代码中。

被标记为ObservableProperty特性的属性，不应该🚷拥有任何访问器，因为MVVM Toolkit会自动生成访问器，并且会自动调用。

此外你需要记住的是📓,该自动生成的属性会以大驼峰命名。例如此处的a、b、c属性，自动生成的属性是A、B、C。

接下来让我们创建一个ViewModel类。
```CSharp
 public partial class ViewModel
 {
     public Model? CeModel { get; set; }

     public ViewModel() 
     { 
         CeModel=new Model();
         CeModel.A = 1;
         CeModel.B=2; 
        
     
     }
 }
```
同样的，如果你使用了对应的特性〽️，为了自动生成对应的代码，我们的ViewModel类也需要标记为partial。

我们先创建一个对应的Model实例，然后使用刚才所说的自动生成的访问器A和B，给它们赋值🧯。

哦，差点忘了，我们还需要在MainWindow.xaml.cs中添加对应的DataContext，否则访问不到ViewModel。
```CSharp
 public partial class MainWindow : Window
 {
     public MainWindow()
     {
         InitializeComponent();
         this.DataContext=new ViewModel();
     }
 }
```
然后我们在xaml中绑定一下对应的访问器。
```html
 //对应的文本框，绑定A
 <TextBox Width="500" Text="{Binding CeModel.A}"/>
 //对应的文本框，绑定B
  <TextBox Width="500" Text="{Binding CeModel.B}"/>
```

然后你就可以点击运行，📂打开这个简单的wpf程序试试看了，你会发现界面上的文本框里成功的⛳显示出了1和2。到这里我们的双向绑定就完成了，我们在界面上直接对文本框进行修改，对应的model值也会发生改变。

为了验证这一点，我们写一个简单的求和逻辑🪜。
```html
<Window x:Class="MVVMTest.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
        xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
        xmlns:local="clr-namespace:MVVMTest"
        mc:Ignorable="d"
        Title="MainWindow" Height="140" Width="520" ResizeMode="NoResize">
    <Grid>
        <StackPanel>
            <Grid>
                <StackPanel Orientation="Horizontal">
                    <Label Content="A"/>
                    <TextBox Width="500" Text="{Binding CeModel.A}"/>
                </StackPanel>
            </Grid>
            <Grid>
                <StackPanel Orientation="Horizontal">
                    <Label Content="B"/>
                    <TextBox Width="500" Text="{Binding CeModel.B}"/>
                </StackPanel>
            </Grid>
            <Grid>
                <StackPanel Orientation="Horizontal">
                    <Label Content="C"/>
                    <TextBox Width="500" Text="{Binding CeModel.C}"/>
                </StackPanel>
            </Grid>
            <Grid>
                <Button Content="计算" Command="{Binding AddCommand}"/>
            </Grid>
        </StackPanel>

    </Grid>
</Window>
```
然后是在ViewModel中添加对应的命令🖱️。我们这里使用[RelayCommand]特性来标记我们的命令。

需要记住的是，自动生成的命令名为“函数名+Command”。
```CSharp
 public partial class ViewModel
{
    public Model? CeModel { get; set; }

    public ViewModel() 
    { 
        CeModel=new Model();
        CeModel.A = 1;
        CeModel.B=2; 
        CeModel.C=3;
    
    }
    [RelayCommand]
    public void Add() 
    { 
       CeModel.C=CeModel.A+CeModel.B;
    
    }
}
```
然后我们启动这个wpf程序，随意修改A和B的值，点击计算，C的值就会自动改变🎉。
至此你就已经学会了如何使用MVVM工具自动实现双向绑定属性和命令。
