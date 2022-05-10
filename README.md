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

`Dispatcher`

<br>

## BackgroundWorker

<br>

## References
- [Thread](https://wergia.tistory.com/187)
- [WPF Multi Thread](https://ddka.tistory.com/entry/WPF-multi-thread)
- [Thread Affinity](https://blueasa.tistory.com/1258)
- [Dispatcher](https://techlog.gurucat.net/167)
- [WPF Dispatcher - Introduction and How to use?](http://dotnetpattern.com/wpf-dispatcher)
- [BackgroundWorker](http://eincs.com/2009/09/wpf-multi-threaed-programming/)
