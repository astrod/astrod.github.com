---
layout: post
title: "[Head First] 커맨드 디자인 패턴"
tags:
- design_pattern
- book
- java
category:
- design_pattern
---
* toc
{:toc}

# 들어가며
이 글에 나오는 내용은 <<Head First Design Patterns>> 6장, 커맨드 패턴에 나오는 내용을 정리한 것이다.

# 요구사항
홈 오토메이션 리모컨 API 디자인을 의뢰받았다. 의뢰받은 API 는 다음과 같다.

- 일곱 가지 프로그래밍이 가능한 슬롯이 있다. 각 슬롯에 원하는 제품을 연결한 후, 옆에 있는 버튼을 가지고 기기를 조작할 수 있다.
- 일곱 개의 버튼마다 각각 "ON" 버튼과 "OFF" 버튼이 있다. 이 두 버튼으로 각각의 제품을 조작할 수 있다.
- "UNDO" 버튼이 있다. 이 버튼을 사용하여 누른 버튼에 대한 명령을 취소할 수 있다.
- 각 가전제품 제작사들은 가전제품을 동작시킬 수 있는 클래스를 제공한다.

~~~java
class TV {
    public void on();
    public void off();
    public void setInputChannel();
    public void setVolume();
}
~~~

혹은 이런 클래스도 있다.

~~~java
class OutdoorLight {
    public void on();
    public void off();
}
~~~

# 다양한 인터페이스

위의 예제처럼, 각 가전제품 제작자들은 각기 다른 인터페이스를 제공한다. 모든 클래스에 on() 하고 off() 메소드만 있을 줄 알았는데, 다양한 메소드를 잔뜩 제공하고 있다.

게다가 나중에 또 다른 제품이 추가되면, 또 다른 이름을 가진 메소드가 추가될 수도 있다. 새로운 클래스가 추가될 때마다 리모컨에 있는 코드를 고치면 버그가 발생할 가능성이 높다. 새로운 클래스를 추가하더라도, 코드를 수정하고 싶지 않다.

커멘드 패턴을 사용하면 어떨까? 커멘드 패턴을 쓰면 어떤 작업을 요청한 쪽과 작업을 처리한 쪽을 분리시킬 수 있다. 리모컨 버튼을 누르면, 커맨드 객체를 통해서 작업을 처리하게 만들면 된다. 리모컨은 커맨드 객체만 있으면 된다.

# 커맨드 패턴

객체마을 식당에서 주문하는 경우를 가정해 보자

1. 고객이 웨이트리스한테 주문을 한다.
2. 웨이트리스는 주문을 받아서 카운터에 가져다 주고, 주문 받으라고 이야기한다.
3. 주방장이 주문대로 음식을 준비한다.

위의 케이스에서, 고객은 createOrder() 라는 메소드를 통해 주문한다. 고객은 먹고 싶은 음식을 **주문서** 에 쓰고, 웨이트리스는 주문서를 주방장에 가져다 준다.

핵심은 주문서이다. 주문서 내부에는 orderUp() 이라는, 식사를 준비하기 위한 행동을 캡슐화 한 메소드가 들어있다. 이게 유일한 메소드이다. 그리고 주방장에 대한 래퍼런스도 가지고 있다.

아마 이렇게 생겼을 것이다.

~~~java
class Order {
    private Cook cook;
    
    public void orderUp() {
        // implement
    }
}
~~~ 

웨이터리스는 주문을 받아서, Order 에 있는 orderUp() 메소드를 호출하는 일을 한다. 웨이터리스는 많은 걸 알 필요가 없다. 그냥 주문서에 orderUp() 메소드가 있고, 이를 호출하면 된다는 사실만 알고 있다.

웨이터리스의 takeOrder() 메소드에는 여러 고객이 여러 주문서를 매개변수로 전달한다. 그러면 웨이터리스는 모든 주문서의 orderUp() 메소드를 호출하여 식사를 준비한다.

웨이터리스가 orderUp() 메소드를 호출하면, 주방장이 그 주문을 받아서 음식을 만들기 위한 메소드를 전부 처리한다. 여기서 주방장과 웨이트리스는 완전히 분리되어 있다. 웨이터리스는 각 주문서의 orderUp() 메소드를 호출할 뿐이고, 주방장은 주문서로부터 할 일을 전달받을 뿐이다.

정리하면 다음과 같다.

- 고객 : createOrder() 메소드를 호출하여 주문서를 생성한다.
- 웨이터리스 : 주문을 받아서 주문서에 있는 orderUp() 메소드를 호출한다.
- 주문서 : 고객의 주문을 캡슐화한다. 내부에 주방장 래퍼런스를 가지고 있다.
- 주방장 : 웨이터리스가 orderUp() 메서드를 호출하면, 실제로 음식을 만들기 위한 메소드를 처리한다.

핵심은 다음과 같다 : 주문을 했을 때 호출되는 코드와 실제로 요리하는 코드를 분리시켜야 한다.

# 객체마을 식당과 커맨드 패턴

위의 객체마을 식당 역할을 그대로 커맨드 패턴으로 옮겨 보자. 이름만 변경되었을 뿐 등장인물은 동일하다.

1. 클라이언트 : 커맨드 객체를 생성한다. 커맨드 객체는 리시버에 전달할 일련의 행동으로 구성된다. 위의 예제에서는 고객이다. 
2. 리시버 : 특정 행동을 하는 메소드를 호출한다. 위의 예제에서는 요리사에 해당된다.
3. 커맨드 : execute() 메소드 단 하나만을 제공한다. 이 메소드는 행동을 캡슐화하며, 리시버에 있는 특정 행동을 처리하기 위한 메소드를 호출하기 위한 메소드이다.
4. 인보커 : 클라이언트에서는 인보커 객체에 setCommand() 메소드를 호출하는데, 이 때 커멘드 객체를 넘겨준다. 커멘드 객체는 사용되기 전까지 인보커에 보관된다. 위의 예제에서는 웨이트리스이다.

흐름대로 정리하면 다음과 같다.

1. 클라이언트 객체에서 커맨드 객체를 생성한다. 커맨드 객체 안에는 행동과 리시버에 대한 정보가 둘 다 들어있다.
2. 클라이언트에서 인보커 객체를 생성하고 커맨드 객체를 세팅한다.
3. 인보커에서 커맨드 객체의 execute() 메소드를 호출한다.
4. 리시버에서 특정 행동을 하는 메소드를 호출한다.

# 첫 번째 커멘드 객체

이제 커맨드 객체를 만들어 볼 예정이다. 커맨드 객체는 모두 같은 인터페이스를 구현하여야 한다. 다음과 같은 인터페이스가 될 것이다.

~~~java
public interface Command {
    void execute();
}
~~~

이제 전등을 켜기 위한 실제 커맨드 구현체를 만들어 보자

~~~java
public class LightOnCommand implements Command {
    Light light;
    
    // 생성자에 이 커맨드 객체가 제어할 특정 전등의 정보가 전달된다.
    public LightOnCommand(Light light) {
        this.light = light;
    }    
    
    public void execute() { // execute 메소드에서는 실제로 행동을 하는 리시버 객체의 on 메소드를 호출한다.
        light.on();
    }
}
~~~

# 커맨드 객체 사용하기

이제 위에서 만든 커맨드 객체를 사용해 보자. 버튼이 하나밖에 없는 리모컨이 있다고 가정하고 다음과 같은 코드를 만들어 보자

~~~java
public class SimpleRemoteControl {
    Command slot;
    
    public SimpleRemoteControl() {}
    
    public void setCommand(Command command) {
        this.slot = command;
    }
    
    public void buttonWasPressed() {
        slot.execute();
    }
}
~~~

이제 테스트 해 보자. 

~~~java
// 커맨드 패턴에서 클라이언트에 해당된다.
public class RemoteControlTest {
    public static void main(String [] args) {
        SimpleRemoteControl remote = new SimpleRemoteControl(); // 인보커 역할을 한다. 필요한 작업을 요청할 때 사용할 커맨드 객체를 인자로 전달할 것이다.
        Light light = new Light();
        remote.setCommand(new LightOnCommand(light)); // 커맨드 객체를 인자로 전달한다. 커맨드 객체에는 실제로 요청을 처리할 리시버가 인자로 전달된다.
        
        remote.buttonWasPressed();
    }
}
~~~

# 커맨드 패턴의 정의

위의 예제를 통해, 커맨드 패턴이 어떻게 동작하는지는 대략적으로 알게 되었을 것이다. 이제 공식적으로 커맨드 패턴이 어떻게 정의되는지 확인해 보자

> 커맨드 패턴 - 커맨드 패턴을 이용하면 요구 사항을 객체로 캡슐화 할 수 있으며, 매개변수를 써서 여러 가지 다른 요구 사항을 집어넣을 수도 있다. 또한 요청 내역을 큐에 저장하거나 로그로 기록할 수도 있으며, 작업취소 기능도 지원 가능하다.

하나씩 확인해 보자.

우선, 커맨드 패턴은 일련의 행동(command) 을 특정 리시버하고 연결함으로써, 요구사항을 캡슐화한 것이다. 이렇게 하기 위해서 행동과 리시버를 한 객체에 집어넣고, execute() 라는 메소드 하나만 외부에 공개하는 기법을 쓴다.

이 메소드 호출을 통해 리시버에서 일련의 작업이 실행된다. 핵심은 외부에서 볼 때는 command 객체의 execute() 메소드를 호출한 것만 알기 때문에, 실제로 리시버가 어떤 일을 하는지 캡슐화된다는 점이다.

그렇기 때문에, 인보거 객체에서도 자기에게 어떤 커맨드 객체가 세팅되었는지 알 필요가 없다. 인보커 객체는 커맨드 객체의 execute() 를 실행할 뿐이다.

# 슬롯에 명령 할당하기

그러면 이제 리모컨을 만들어야 한다. 아마 다음과 같은 구조가 될 것이다.

1. 각 슬롯마다 커맨드 객체가 할당된다. ("on" / "off" 버튼에)
2. 사용자가 버튼을 누르면 해당 커맨드 객체와 execute() 메소드가 호출된다.
3. 커맨드 객체에서는 특정 리시버로 하여금 작업을 처리하게 한다.

# 리모컨 코드

리모컨은 커맨드 패턴에서 인보커를 담당한다. 커맨드 객체를 저장하고 있다가, 요청이 들어오면 적당한 커맨드 객체의 execute() 를 호출한다. 코드는 다음과 같다.

~~~java
public class RemoteControl {
    Command[] onCommands;
    Command[] offCommands;
    
    public RemoteControl() {
        onCommands = new Command[7];
        offCommands = new Command[7];
        
        // 생성자에서 각 on, off Command 객체 배열을 초기화한다.
        Command noCommand = new NoCommand();
        for(int i = 0; i<7; i++) {
            onCommands[i] = noCommand;
            offCommands[i] = noCommand;
        }
    }
    
    // Command 객체를 세팅한다. 슬롯 번호를 받아서 슬롯에 해당하는 on, off Command 를 세팅한다.
    public void setCommand(int slot, Command onCommand, Command offCommand) {
        onCommand[slot] = onCommand;
        offCommand[slot] = offCommand;
    }
    
    public void onButtonWasPushed(int slot) {
        onCommands[slot].execute();
    } 
    
    public void offButtonWasPushed(int slot) {
        offCommands[slot].execute();
    }
}
~~~

이제 커맨드 클래스를 만들어 봅시다. 앞에서 LightOnCommand 라는 커맨드 클래스를 만들었습니다. LightOffCommand 도 별반 다르지 않습니다.

~~~java
public class LightOffCommand implements Command {
    
    Light light;
    
    public LightOffCommand(Light light) {
        this.light = light;
    }    
    
    public void execute() {
        light.off();
    }
}
~~~

조금 어려운 걸 해 볼까요? 오디오를 켜고 끌 때 쓰는 커맨드 클래스를 만들어 봅시다. 끄는 건 간단합니다. 그러나 켜는 건 좀 더 어렵습니다. 우선 오디오를 켤 때 자동으로 CD 가 재생되도록 하는 클래스를 만들어 봅시다.

~~~java
public class StereoOnWithCDCommand implements Command {
    
    Stereo stereo;
    
    public StereoOnWithCDCommand(Stereo stereo) {
        this.stereo = stereo;
    }    
    
    public void execute() {
        stereo.on();
        stereo.setCD();
        stereo.setVolume(11);
    }
}
~~~

# 리모컨 테스트

이제 리모컨도 거의 다 만들어 갑니다. 테스트를 해 본 다음에 API 문서만 준비하면 될 거 같습니다. 테스트 코드는 다음과 같습니다.

~~~java
public class RemoteLoader {
    
    public static void main(String [] args) {
        RemoteControl remoteControl = new RemoteControl();
        
        // 1. 리시버 객체를 생성한다.
        Light livingRoomLight = new Light("Living Room");
        Light kitchenLight = new Light("Kitchen");
        CeilingFan ceilingFan = new CeilingFan("Living Room");
        GarageDoor garageDoor = new GarageDoor("");
        Stereo stereo = new Stereo("Living Room");
        
        // 2. 커맨드 객체를 생성한다.
        LightOnCommand livingRoomLightOn = new LightOnCommand(livingRoomLight);
        LightOffCommand livingRoomLightOff = new LightOffCommand(livingRoomLight);
        LightOnCommand kitchenLightOn = new LightOnCommand(kitchenLight);
        LightOffCommand kitchenLightOff = new LightOffCommand(kitchenLight);
        
        CeilingFanOnCommand ceilingFanOn = new CeilingFanOnCommand(ceilingFan);
        CeilingFanOffCommand ceilingFanOff = new CeilingFanOffCommand(ceilingFan);
        
        GarageDoorUpCommand garageDoorUp = new GarageDoorUpCommand(garageDoor);
        GarageDoorDownCommand garageDoorDown = new GarageDoorDownCommand(garageDoor);
        
        StereoOnWithCDCommand stereoOnWithCD = new StereoOnWithCDCommand(stereo);
        StereoOffWithCDCommand stereoOffWithCD = new StereoOffWithCDCommand(stereo);
        
        // 3. 리모컨 슬롯에 각 커맨드를 로드한다.
        remoteControl.setCommand(0, livingRoomLightOn, livingRoomLightOff);
        remoteControl.setCommand(1, kitchenLightOn, kitchenLightOff);
        remoteControl.setCommand(2, garageDoorUp, garageDoorDown);
        remoteControl.setCommand(3, stereoOnWithCD, stereoOffWithCD);
        
        // 4. 슬롯을 껐다가 켜보자
        remoteControl.onButtonWasPushed(0);
        remoteControl.offButtonWasPushed(0);
        
        remoteControl.onButtonWasPushed(1);
        remoteControl.offButtonWasPushed(1);
    }
}
~~~

위의 코드를 보면 4번부터 6번까지 슬롯에는 다른 커맨드 객체를 로드하지 않았음을 알 수 있다. 초기화할 때 이 슬롯에는 NoCommand 라는 객체를 세팅한다.

만약 어떤 객체도 세팅해놓지 않는다면, 특정 슬롯을 쓰려고 할 때 슬롯에 해당 커맨드가 있는지 확인하는 코드가 필요하다. 예를 들면

~~~java
public void onButtonWasPushed(int slot) {
    if(onCommand[slot] != null) {
        onCommand[slot].execute();
    }
}
~~~

이건 너무 귀찮다. 이를 막기 위해 아무것도 하지 않는 커맨드 클래스를 구현하여 생성자에서 세팅해준다. NoCommand 는 아마 다음과 같이 구현되었을 것이다.

~~~java
public class NoCommand implements Command {
    public void execute() {}
}
~~~

이와 같은 객체를 널 객체(null Object) 라고 하는데, 딱히 리턴할 객체는 없지만 클라이언트 쪽에서 null 을 처리하지 않도록 하고 싶을 때 활용한다. 널 객체는 여러 디자인 패턴에서 유용하게 사용된다. 널 객체를 일종의 디자인 패턴으로 분류하기도 한다.

# UNDO

그러고보니, UNDO 버튼을 구현하지 않았다. UNDO 버튼도 구현하여야 한다. 다행히도, Command 클래스만 가지고 작업취소 기능을 쉽게 구현할 수 있다.

먼저 리모컨에 UNDO 버튼을 지원하는 기능을 추가해야 한다. 예를 들면, "ON" 버튼을 눌러서 거실 전등을 켰다고 해보자. "UNDO" 버튼을 누르면 거실 불이 꺼져야 할 것이다.

일단 커맨드 인터페이스에 UNDO 기능을 추가해야 한다. 다음과 같이 하면 될 거 같다.

~~~java
public interface Command {
    public void execute();
    public void undo(); // 새로 undo 메소드 추가
}
~~~

그 다음에, Light 커맨드로 들어가서 undo() 메소드를 구현해 보자.

~~~java
public class LightOnCommand implements Command {
    Light light;
    
    public LightOnCommand(Light light) {
        this.light = light;
    }    
    
    public void execute() {
        light.on();
    }
    
    // execute 에서는 불을 키니, undo 에서는 불을 끄면 될 거 같다.
    public void undo() {
        light.off();
    }
}
~~~

LightOffCommand 도 크게 다르지 않다.

~~~java
public class LightOffCommand implements Command {
    
    Light light;
    
    public LightOffCommand(Light light) {
        this.light = light;
    }    
    
    public void execute() {
        light.off();
    }
    
    // LightOnCommand 의 undo 와는 반대로, 불을 다시 켜면 된다.
    public void undo() {
        light.on(); 
    }
}
~~~

아직 끝난 건 아니다. RemoteControl 클래스에 사용자가 마지막으로 누른 버튼을 기록하고, UNDO 버튼이 눌렸을 때 필요한 작업을 처리하기 위한 코드를 추가해야 한다.

~~~java
public class RemoteControl {
    Command[] onCommands;
    Command[] offCommands;
    Command undoCommand; // 마지막으로 한 액션을 저장하기 위해 undo 객체를 생성한다.
    
    public RemoteControl() {
        onCommands = new Command[7];
        offCommands = new Command[7];
        
        Command noCommand = new NoCommand();
        for(int i = 0; i<7; i++) {
            onCommands[i] = noCommand;
            offCommands[i] = noCommand;
        }
        
        undoCommand = noCommand; // undoCommand 를 초기화한다.
    }
    
    public void setCommand(int slot, Command onCommand, Command offCommand) {
        onCommand[slot] = onCommand;
        offCommand[slot] = offCommand;
    }
    
    public void onButtonWasPushed(int slot) {
        onCommands[slot].execute();
        undoCommand = onCommands[slot]; // 마지막으로 한 액션을 저장해 둔다. 
    } 
    
    public void offButtonWasPushed(int slot) {
        offCommands[slot].execute();
        undoCommand = offCommands[slot]; // 마지막으로 한 액션을 저장해 둔다.
    }
    
    public void undoButtonWasPushed() {
        undoCommand.undo();
    }
}
~~~

크게 어렵지 않게 undo 버튼을 구현할 수 있다.

# 작업취소 기능을 구현할 때 상태를 사용하는 방법

Light 클래스에 작업취소 기능은 어렵지 않았다. 그러나 작업취소 기능을 구현할 때 간단히 상태를 저장해야 하는 경우도 있다. 선풍기 제조 업체에서 제공한 CeilingFan 클래스를 가지고 좀 더 복잡한 걸 해 보자.

CeilingFan 코드는 다음과 같다.

~~~java
class CeilingFan {
    
    public static final int HIGH = 3;
    public static final int MEDIUM = 2;
    public static final int LOW = 1;
    public static final int OFF = 0;
    
    String location;
    int speed;
    
    private CeilingFan(String location) {
        this.location = location;
        speed = OFF;
    }
    
    public void high() {
        speed = HIGH;
    }
    
    public void medium() {
        speed =MEDIUM;
    }
    
    public void low() {
        speed = LOW;
    }
    
    public void off() {
        speed = OFF;
    }
    
    public int getSpeed() {
        return speed;
    }      
}
~~~ 

선풍기의 바람을 high / medium / low / off 로 변경할 수 있는 기능이 담긴 클래스이다. 위의 CeilingFan 커맨드 클래스에 작업취소 기능을 추가해 보자.

작업취소 기능을 구현하려면 선풍기의 이전 속도를 저장해 두었다가, undo() 메소드가 호출되면 기존 설정으로 돌아가면 된다.

코드는 다음과 같다.

~~~java
public class CeilingFanHighCommand implements Command {
    CeilingFan ceilingFan;
    int prevSpeed;
    
    public CeilingFanHighCommand(CeilingFan ceilingFan) {
        this.ceilingFan = ceilingFan;
    }
    
    public void execute() {
        prevSpeed = ceilingFan.getSpeed(); // 현재 상태값을 저장해 둔다.
        ceilingFan.high();
    }
    
    // 이전의 상태값을 확인하여 undo 메소드를 구현한다.
    public void undo() {
        if(prevSpeed == CeilingFan.HIGH) {
            ceilingFan.high();
        } else if(prevSpeed == CeilingFan.MEDIUM) {
            ceilingFan.medium();
        } else if(prevSpeed == CeilingFan.LOW) {
            ceilingFan.low();
        } else if(prevSpeed == CeilingFan.OFF) {
            ceilingFan.off();
        }
    }
} 
~~~

# 매크로 커맨드

앞에서 커맨드를 만들고, 리모컨에 커맨드를 매핑하는 방법까지 알아보았다. 그렇다면, 버튼 하나에 여러 기능을 묶어서 실행할 수도 있지 않을까?

예를 들면 다음과 같은 매크로 커맨드를 만들 수 있을 거 같다.

~~~java
public class MacroCommand implements Command {
    Command[] commands;
    
    public MacroCommand(Command [] commands) {
        this.commands = commands;
    }
    
    // 여러 커맨드를 한 번에 받아서 실행시킨다.
    public void execute() {
        for(int i = 0; i<commands.length; i++) {
            commands[i].execute();
        }
    }
}
~~~

이제 이 커맨드 객체에 필요한 커맨드 객체를 세팅해주면 될 거 같다. 다음과 같이 구현할 수 있지 않을까?

~~~java
public class MacroTest {
    public static void main(String [] args) {
        
        // 리시버 객체를 생성한다.
        Light light = new Light("Living Room");
        TV tv = new TV("Living Room");
        Stereo stereo = new Stereo("Living Room");
        Hottub hottub = new Hottub();
        
        // 커맨드 객체를 생성한다.
        LightOnCommand lightOn = new LightOnCommand(light);
        TVOnCommand tvOn = new TVOnCommand(tv);
        StereoOnCommand stereoOn = new StereoOnCommand(stereo);
        HottubOnCommand hottubOn = new HottubOnCommand(hottub);
        
        Command[] partyOn = { lightOn, tvOn, stereoOn, hottubOn};
        
        // 생성한 커맨드 객체를 배열로 넘긴다.
        MacroCommand partyOnMacro = new MacroCommand(partyOn);
        
        // 매크로 커맨드를 세팅한다.
        remoteComtrol.setCommand(0, partyOnMacro);
    }
}
~~~

이제 버튼 한번에 불, TV, 음악, 핫튜브를 모두 킬 수 있게 되었다.

