# wpf-thread

## Contents
- [Thread](#thread)
- [BackgroundWorker](#backgroundworker)
- [Dispatcher](#dispathcer)
- [Invoke / BeginInvoke](#invoke--begininvoke)

<br>

## Thread
일반적으로 우리가 사용하는 운영체제는 멀티태스크를 지원한다. 브라우저, 프로그램 등은 각각 하나의 **프로세스**이며, 이 프로세스는 하나 이상의 **스레드(Thread)** 로 이루어진다.
하나의 OS에서 여러 프로세스가 작업하는 것처럼, 한 프로세스에서 여러 스레드가 동시에 작업을 처리할 수 있다. 이처럼 여러 스레드를 사용하는 것을 **멀티스레드(Multi-Thread)** 라고 한다.

> 예를 들어, 오디오 프로그램이라는 하나의 프로세스 안에서 한 스레드는 음악을 재생하고, 또 다른 스레드는 가사를 보여주면서 음악 재생 시간에 맞춰 싱크를 맞추는 등의 방식으로 동시에 복수의 작업을 처리할 수 있다.

<br>

### WPF에서의 Thread

모든 WPF 프로그램은 최소한의 렌더링을 위한 **UI 스레드**와 **작업 스레드**로 기동된다. UI 스레드는 사용자의 입력을 받고 이벤트를 처리하는 동작, 기본적인 코드의 실행 등을 수행한다. 작업 스레드는 복잡하고 시간이 오래 걸리는 연산을 처리한다. 

즉, 시간이 오래 걸리는 연산을 작업 스레드에서 처리할 동안 사용자가 다른 동작을 할 수 있도록 UI 스레드가 기본 동작을 수행한다. 이후 연산이 완료되면 UI 스레드를 통해 화면에 결과를 출력할 수 있다.

WPF는 기본적으로 **STA(Single Thread Apartment)** 모델을 지원한다. 이는 하나의 기본 스레드가 전체 응용 프로그램에서 실행되고 이 스레드에 모든 WPF 객체가 종속되어 있다. 기본 스레드를 제외한 다른 스레드에서는 WPF 객체에 엑세스할 수 없으며 이를 **스레드 선호도(Thread Affinity)** 라고 한다.

<br>

## Dispatcher

<br>

## Invoke / BeginInvoke

<br>

## References
- [Thread](https://wergia.tistory.com/187)
- [WPF Multi Thread](https://ddka.tistory.com/entry/WPF-multi-thread)
- [Thread Affinity](https://blueasa.tistory.com/1258)
