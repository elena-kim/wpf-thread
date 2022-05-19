# wpf-thread

## Contents
- [Thread](#thread)
- [Dispatcher](#dispatcher)
- [BackgroundWorker](#backgroundworker)

<br>

## Thread
일반적으로 우리가 사용하는 운영체제는 멀티태스크를 지원한다. 브라우저, 프로그램 등은 각각 하나의 **프로세스**이며, 이 프로세스는 하나 이상의 **스레드(Thread)** 로 이루어진다.
하나의 OS에서 여러 프로세스가 작업하는 것처럼, 한 프로세스에서 여러 스레드가 동시에 작업을 처리할 수 있다. 이처럼 여러 스레드를 사용하는 것을 **멀티스레드(Multi-Thread)** 라고 한다.

> 예를 들어, 오디오 프로그램이라는 하나의 프로세스 안에서 한 스레드는 음악을 재생하고, 또 다른 스레드는 가사를 보여주면서 음악 재생 시간에 맞춰 싱크를 맞추는 등의 방식으로 동시에 복수의 작업을 처리할 수 있다.

<br>

### STA

**`STA(Single-Threaded Aparatments)`** 는 단 하나의 스레드만 가질 수 있다. 이 Apartment에 있는 모든 개체들은 이 스레드에서만 메서드 호출을 받을 수 있다. 모든 메서드 호출은 싱글 스레드에서 동기적으로 발생하기 때문에 동기화가 필요하지 않다. 

STA는 다른 스레드의 호출을 처리하기 위해 **메시지 큐**가 필요하다. 다른 스레드가 STA 스레드의 개체를 호출하면 메서드 호출이 메시지 큐에 대기하게 되고, STA 개체는 해당 메시지 큐에서 호출을 받는다.

<br>

### MTA

**`MTA(Multi-Threaded Aparatments)`** 는 하나 이상의 스레드를 가진다. 이 Apartment에 있는 모든 개체들은 모든 스레드에서 호출을 받을 수 있다. 모든 개체들은 그들의 데이터 동기화를 유지 및 관리해야 한다.

<br>

### WPF에서의 Thread

WPF는 기본적으로 **STA** 모델을 지원한다. 따라서 기본 스레드를 제외한 다른 스레드에서는 WPF 객체에 엑세스할 수 없으며, 이를 **스레드 선호도(Thread Affinity)** 라고 한다.

모든 WPF 프로그램은 **UI 스레드**와 **Render 스레드**로 기동된다. UI 스레드는 사용자의 입력을 받고 이벤트를 처리하는 동작, 기본적인 코드의 실행 등을 수행한다. Render 스레드는 복잡하고 시간이 오래 걸리는 연산을 처리한다. 

즉, 시간이 오래 걸리는 연산을 Render 스레드에서 처리할 동안 사용자가 다른 동작을 할 수 있도록 UI 스레드가 기본 동작을 수행한다. 이후 연산이 완료되면 UI 스레드를 통해 화면에 결과를 출력할 수 있다.

WPF에서 멀티 스레드를 다루는 방법에는 **`Dispatcher`** 와 **`BackgroundWorker`** 가 있다.

<br>

## Dispatcher

![image](https://user-images.githubusercontent.com/74305823/167331110-0b53dfe4-7bd0-4e6b-92b6-932c511d2b02.png)

**Dispatcher**는 `System.Windows.Threading.Dispatcher` 클래스의 인스턴스로, UI 컨트롤 작업 항목의 대기열을 관리한다. WPF 애플리케이션을 실행하면 자동적으로 Dispatcher 객체가 생성되고 Run 메서드가 호출된다. Run 메서드는 작업 대기열을 초기화하기 위해 사용된다. 

Dispatcher는 UI 스레드와 연관되어 있으며, UI 스레드 대기열에 있는 메서드는 Dispatcher 객체 내에서 호출된다. 이벤트가 호출되거나 화면이 바뀔 때, 혹은 코드 비하인드에서 메서드가 호출될 때마다 이 모든 일들은 UI 스레드와 UI 스레드 대기열에서 일어나며, 호출된 메서드는 Dispatcher 큐에 들어가게 된다. Dispatcher는 동기 순서에 따라 메시지 큐를 실행한다.

<br>

### DispatcherObject

Window, Button, TextBox 등 모든 WPF UI 컨트롤은 **DispatcherObject** 클래스를 상속받았다.

![image](https://user-images.githubusercontent.com/74305823/167590896-ef9bd74d-c6e9-4a00-9e31-0f8aadb2f847.png)

예를 들어, WPF에서 Button을 하나 생성하면 DispatcherObject의 생성자가 호출된다. 생성자에서는 현재 스레드의 Dispatcher를 DispatcherObject 클래스의 Dispatcher 변수에 저장한다.

#### `Class`
![image](https://user-images.githubusercontent.com/74305823/167592002-30082dee-e852-4653-9821-8dfef53c8611.png)

#### `Constructor`
```csharp
private Dispatcher _dispatcher;

public Dispatcher Dispatcher
{
    get
    {
        // This property is free-threaded.
        return _dispatcher;
    }
}
        
protected DispatcherObject()
{
    _dispatcher = Dispatcher.CurrentDispatcher;
}
```

<br>

### CheckAccess
`CheckAccess()` 메서드는 해당 Thread가 UI Thread인지 체크하는 역할을 한다. 해당 Thread가 작업 Thread인 경우 `Dispatcher.Invoke()` 또는 `Dispatcher.BeginInvoke()`를 사용하여 UI Thread로 작업 요청을 보낸다.

```csharp
using System;
using System.Windows;
using System.Threading.Tasks;

namespace WpfApp
{
    public partial class MainWindow : Window
    {
        public MainWindow()
        {
            InitializeComponent();
        }

        private void button1_Click(object sender, RoutedEventArgs e)
        {
            // 작업 Thread 생성
            Task.Factory.StartNew(Run);            
        }

        private void Run()
        {
            // 해당 Thread가 UI Thread인가?
            if (textBox1.Dispatcher.CheckAccess())
            {
                //UI Thread인 경우
                textBox1.Text = "Data";
            }
            else
            {
                // 작업 Thread인 경우
                textBox1.Dispatcher.BeginInvoke(new Action(Run));
            }
        }
    }
}
```

<br>

### Invoke
`Invoke`는 `Action` 또는 `Delegate`를 사용하여 동기적으로 메서드를 수행한다. 즉 이를 호출한 외부 스레드에서 이벤트 핸들러 함수가 끝날 때까지 Sleep 상태로 기다렸다가 함수 실행이 끝나면 다음 코드가 진행된다. 

```csharp
public partial class MainWindow : Window
{
    public MainWindow()
    {
	InitializeComponent();
	Task.Factory.StartNew(() =>
	{
	    InvokeMethodExample();
	});
    }

    private void InvokeMethodExample()
    {
	Thread.Sleep(2000);
	Dispatcher.Invoke(() =>
	{
	    btn1.Content = "By Invoke";
	});
    }
}
```

<br>

### BeginInvoke
`BeginInvoke`는 `Delegate`를 사용하여 비동기로 메서드를 수행한다. 즉 메서드가 호출되기 전 즉시 반환된다. 

```csharp
public partial class MainWindow : Window
{
    public MainWindow()
    {
        InitializeComponent();
        Task.Factory.StartNew(() =>
        {
	    BeginInvokeExample();
        });
    }

    private void BeginInvokeExample()
    {
        DispatcherOperation op = Dispatcher.BeginInvoke((Action)(() => {
	    btn1.Content = "By BeginInvoke";
        }));
    }
}
```

`BeginInvoke`는 `DispatcherOperation` 객체를 반환한다. `DispatcherOperation`은 작업의 완료 여부를 알고 싶을 때 사용할 수 있다.
- `Aborted` : 작업이 중단될 때 발생
- `Completed` : 작업이 완료되면 발생

<br>

## BackgroundWorker

**BackgroundWorker**는 `System.ComponentModel.BackgroundWorker` 클래스의 인스턴스로, 기본 프로그램 동작과 함께 동시에 처리되어야 하는 어떤 동작이 필요할 때 사용한다. 즉, 메인 스레드가 실행되는 도중에 별도의 스레드에서 비동기적으로 코드를 수행할 수 있다.

#### `MainWindow.xaml`
```xaml
<Window x:Class="ThreadTest.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="450" Width="800">
    <Grid>
        <StackPanel VerticalAlignment="Center">
            <Button x:Name="btn" Content="Click to capture" FontSize="20" Click="btn_Click"/>
            <Label x:Name="txtLbl" Content="NULL" FontSize="20" HorizontalAlignment="Center" VerticalAlignment="Center"/>
            <Label x:Name="txtBackLbl" Content="number" FontSize="20" HorizontalAlignment="Center" VerticalAlignment="Center"/>
        </StackPanel>
    </Grid>
</Window>
```

#### `MainWindow.xaml.cs`
```csharp
using System.ComponentModel;
using System.Threading;
using System.Windows;
using System.Windows.Threading;

namespace ThreadTest
{
    public partial class MainWindow : Window
    {
        private BackgroundWorker _backgroundWorker;
        private int number = 0;

        public MainWindow()
        {
            InitializeComponent();
            initBackgroundThread();
        }

        private void initBackgroundThread()
        {
            _backgroundWorker = new BackgroundWorker();
            _backgroundWorker.DoWork += _DoWork;
            _backgroundWorker.RunWorkerAsync();
        }

        private void _DoWork(object? sender, DoWorkEventArgs e)
        {
            while (true)
            {
                Thread.Sleep(200);

                this.Dispatcher.BeginInvoke(DispatcherPriority.Normal, (ThreadStart)delegate ()
		{
		    txtBackLbl.Content = number.ToString();

		    number++;
		    if (number % 10000 == 0)
		    {
			number = 0;
		    }
		});
            }
        }

        private void btn_Click(object sender, RoutedEventArgs e)
        {
            txtLbl.Content = $"capture: {number}";
        }
    }
}
```

<br>

## References
- [Thread](https://wergia.tistory.com/187)
- [WPF Multi Thread](https://ddka.tistory.com/entry/WPF-multi-thread)
- [Thread Affinity](https://blueasa.tistory.com/1258)
- [Dispatcher](https://techlog.gurucat.net/167)
- [WPF Dispatcher - Introduction and How to use?](http://dotnetpattern.com/wpf-dispatcher)
- [BackgroundWorker](http://eincs.com/2009/09/wpf-multi-threaed-programming/)
