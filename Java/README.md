# Exception

Java에서 compile를 하다 보면 일반 오류가 나와 실행하지 못하는 경우가 있다.
하지만 사용자의 잘못된 사용으로 **실행 중 오류**가 발생할 수 있는데, Exception은 개발자의 입장에서 그 상황을 방지해준다.

예외 처리란 **일반 예외**와 **실행 예외**가 있다.

**일반 예외**란 자바 소스를 컴파일하는 과정에서 예외 처리 코드가 필요한지 검사하며,
**실행 예외**란 컴파일하는 과정에서 예외 처리 코드를 검사하지 않는 예외를 의미한다. 


## try, catch

예외 처리의 방법으로는 여러 가지가 있지만, try catch문으로 어느 정도 파악이 가능하다.
다음과 같은 상황을 보자.

```java
public static void main(String[] args) {
        String data = args[0];
        int convertNo = Integer.parseInt(data);        
        int result = 100 / convertNo;
```
컴파일 상으로는 문제가 되지 않는다. **args**는 실행하기 전 인수로 넘어오는 값에 의존하기 때문에
컴파일 하기 전 0번지 값을 사용할 수 있다.

하지만 args로 들어올 인수가 없을 수 있다. 이는 실행 중 오류라는 말이다.

또한, data배열을 String으로 생성하였지만 3번째 줄을 보면 Integer 클래스의 parseInt() 메소드를 사용하여 data를 int값으로 변환하는 것을 시도하고 있다. String 값에 숫자의 문자열이 들어오면 변환이 가능하겠지만, "2a"와 같은 문자열이 들어왔을 때에는 숫자로 변환할 수 없다.

이처럼 어떤 문제가 발생할 수도 있는 구문에 대해서 예외처리를 하는 것이다.

위의 코드에서 발생할 수 있는 가능성을 다시 한 번 정리해보겠다.
> 1. args에 들어오는 인수 값이 없음에도 존재하지 않는 인덱스 값에 접근할 수 있다.
> 2. 문자열이 들어왔을 때 숫자로 변환할 수 없는 값이 존재할 수 있다.
> 3. convertNo가 0일 때 100/0은 불가능하다.


**다음은 try와 catch구문으로 코드를 재구성한 것이다.**

```java
public class ExceptionExam {
    public static void main(String[] args) {

        try {
            System.out.println("---메인 시작---");
            String data = args[0];
            int convertNo = Integer.parseInt(data);        
            int result = 100 / convertNo;
        }
        catch(ArrayIndexOutOfBoundsException e) { } 
        catch(NumberFormatException e) { }
        catch(Exception e) { e.printStackTrace(); }
        
        System.out.println("---메인 끝---");
    }
}
```
예외가 발생할 수 있는 구문은 try { ... } 으로 묶고, 어떤 오류가 발생할 것인지에 대해 catch 구문에서 나열하는 것이다. 
1. `String data = args[0];`은 존재하지 않는 인덱스에 접근할 때에 첫 번째 catch 구문인 `ArrayIndexOutOfBoundsException`에서 걸러진다.
2. `int convertNo = Integer.parseInt(data);`은 문자열이 숫자로 변환할 수 없는 상황일 때 두 번째 catch구문인 `NumberFormatException`에서 걸러진다.
3. 위의 상황을 제외하고 또 다른 오류가 발생할 경우 모든 Exception의 최고 조상인 세 번째 catch 구문 `Exception`에서 걸러진다.

가장 중요한 점은 **try 구문을 실행하고 있을 때 오류가 발생하면, 아래에 있는 구문을 실행하지 않고 catch 구문을 통해 빠져나가는 것이다.** 이를 통해 오류가 발생해도, 프로그램을 중지하지 않고도 계속 실행시킬 수 있다.

### 잘못된 예시
```java
    try {
        System.out.println("---메인 시작---");
        String data = args[0];
        int convertNo = Integer.parseInt(data);        
        int result = 100 / convertNo;
    }
    catch(Exception e) { e.printStackTrace(); }
    catch(ArrayIndexOutOfBoundsException e) { } 
    catch(NumberFormatException e) { } 
```

`Exception` 은 모든 예외 처리의 최고 조상이다. 결국 인덱스를 벗어나는 오류가 발생해도, 첫번째 catch인 Exception에 의해 걸러지게 된다. 즉, 구현할 예외 처리에 대해 가장 높은 곳에 위치하고 있는 클래스를 가장 아래의 catch 구문에 위치 시켜야 한다.

## finally
try에 의해 결국 실행시켜야 할 부분도 **오류 발생** 으로 인하여 실행시키지 못하는 경우가 발생한다.
그 부분을 위해 finally라는 키워드를 제공한다.
```java
    public void aa(int i) /* throws ArithmeticException */ {
        System.out.println("aa()호출");
        try {
            int result = 100 / i;
        } catch (ArithmeticException e) {
            e.printStackTrace();
        } finally {
            System.out.println("aa()종료");
        }
    }
```
try 안에 있는 `int result = 100 / i;`은 i가 0일 때 catch문으로 걸러지지만, finally의 aa()종료를 출력하는 명령은 끝까지 수행된다.

그런데 첫 번째 문장의 `throws ArithmeticException`는 무엇이기에 주석 처리가 되어있을까?

# Exception 넘기기

try catch 으로 예외를 처리하고, 프로그램을 계속 돌릴 수 있도록 하는 방법을 배웠다.
하지만 `try { ... }` `catch { ... }`을 반복 사용하다 보면 코드의 길이가 늘어나게 되고, 예외 처리에 대한 수정이 어렵게 된다.

**작은 프로젝트**의 경우에는 어느 정도의 수준으로 커버가 가능하겠지만, 규모가 점차 커짐에 따라 예외 처리의 복잡성 또한 늘어난다. 그렇기에 예외를 처리하는 곳을 하나의 클래스로 모으는 것이 필요하게 되고, 이 상황이 아니더라도 경우에 따라 메소드를 호출한 곳으로 예외를 떠넘겨야 하는 상황이 발생한다.



## throws
먼저 바로 위의 코드에서 throws를 사용한 예시를 보여주겠다.
```java
    class Test {
        public void aa(int i) throws ArithmeticException {
            System.out.println("aa()호출");
            try {
                int result = 100 / i;
            /* } catch (ArithmeticException e) {
                e.printStackTrace(); */
            } finally {
                System.out.println("aa()종료");
            }
        }
```

try에서 `ArithmeticException ` 예외가 발생하게 되면 catch 구문을 통과하는 것이 아닌
메소드 이름 옆에 있는 throws를 통해 **메소드를 호출한 곳**으로 예외를 넘기게 된다.

결국 aa를 호출한 메인에서는 그것에 대한 예외 처리를 해야 한다.

다음은 main의 코드이다.
```java
public class ThrowsExceptionExam {
    public static void main(String[] args) {
        System.out.println("---메인 시작---");
        Test t = new Test();
        try {
            t.aa(0);
        } catch (ArithmeticException e) {
            e.printStackTrace();
        }
        System.out.println("---메인 끝---");
    }
}
```
aa메소드에 0을 넘겨주는 것을 예외 행위로 본다는 것이다. aa는 throws를 통해 오류가 발생하면 그 예외를 언제든 throws할 수 있기 때문에, 그것을 받아주는 main에서 대비를 해야 한다.

## 사용자 정의 예외

Java에서 기본적으로 제공해주는 Exception을 제외하고도, 사용자가 직접 예외를 제작하여 처리할 수도 있다.
이는 표준 API를 제외하고, 다양한 종류의 예외를 표현하기 위해 사용한다고 보면 된다.
```java
public class LoginExample {
    public static void main(String[] args) {
        try {
            login("white", "12345");
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }
}
```

4번째 줄의 login메소드가 있다. 첫 번째 파라미터는 아이디, 두 번째 파라미터는 비밀번호인 듯 하다.
login의 메소드를 살펴보도록 하자.

```java
    public static void login(String id, String password) throws WrongPasswordException {
        if(!password.equals("12345")) {
            throw new WrongPasswordException("패스워드가 틀립니다.");
        }
    }
```
처음 보는 Exception이 존재한다. `WrongPasswordException` 예외는 사용자가 직접 만든 것이고, login에서 직접 예외 처리를 하는 것이 아닌, main에서 예외 처리를 할 수 있도록 넘겨주었다.

우리가 살펴보아야 하는 것은 2가지 이다.

> WrongPasswordException의 내부 구조
> throw new 키워드

### WrongPasswordException의 내부 구조
```java
    public class WrongPasswordException extends Exception{
        public WrongPasswordException() {}
        public WrongPasswordException(String message) {
            super(message);
        }
    }
```
extends로 Exception을 상속한다. 모든 예외는 Exception을 조상으로 하기 때문에 상속 받아야 하며, 상황에 따라 RuntimeException을 상속하기도 한다. 두 번째 생성자에선 message를 super 즉 **Exception** 클래스에 넘겨주게 되면서 오류 e의 getMessage() 메소드를 실행하게 된다면 message의 값을 출력하게 된다.

###  throw new 키워드
**throws**는 메소드 이름 옆에 쓰이면서 이 메소드 안에 있는 모든 예외를 넘겨주는 기능을 제공하기 때문에 's'가 붙어 있고,
**throw**는 어떤 상황에서 발생할 수 있는 오류를 '던지다'라는 의미를 포함하고 있다.

결국 `throw new WrongPasswordException()`이라는 것은,  사용자가 정의한 Exception 클래스를 새로 공간을 할당하고, 그것을 던진다는 의미이다.


# 자료 구조

자료 구조는 데이터를 저장하는 구조이다.
Java에서는 이러한 구조에 해당하는 객체를 미리 만들어서 제공하며 이는 **Collection API**라고 불린다.

제공되는 interface를 살펴보도록 하자.
1. List :  순서가 있고 중복이 가능함
* ArrayList
* Vector
* LinkedList
2. Set : 순서가 없고, 중복도 되지 않음
* HashSet
* TreeSet
3. Map : key와 value의 한 쌍이며, key는 중복 불가, value는 중복 가능함
* HashMap
* HashTable
* TreeMap
* Properties

지금 까지 같은 이름으로 변수를 선언하려면 **배열**을 사용했지만, Java는 널리 알려져 있는 자료 구조를 바탕으로 객체들을 효율적으로 관리할 수 있는 컬렉션을 제공했다!

> 배열의 한계 : 
컴파일 하기 전 배열의 길이를 지정.
길이가 정해지면 바뀌지 않음.
**다양한 데이터 타입의 저장이 불가능하다.**

```java
        String[] array = new String[10];
        array[0] = new String();
``` 

## 자료구조의 장점
자료구조에서는 **다양한 데이터 타입의 저장**이 가능하다. 설명을 덧붙이자면,
자료구조에 기본형을 그냥 저장할 수 없기 때문에 Wrapper클래스를 이용하게 되며,
autoBoxingm, unBoxing 개념이 적용되면 Wrapper클래스 없이도 자동으로 변환해준다.

또한, **다양한 데이터들을 저장, 조회, 수정, 제거 기능을 제공**한다는 점이다.
배열로 구성했을 때는 하나 하나의 메소드를 직접 구현해야 했다.

![array_list_method](https://user-images.githubusercontent.com/68142821/107457428-6395e880-6b95-11eb-9a79-b86d400db6e1.png)
*출처 : https://docs.oracle.com/javase/8/docs/api/*

여기서 헷갈릴 수도 있으니 다시 한 번 overload와 override의 설명을 짚고 넘어가자.
>Overload : 
반드시 하나의 클래스내에 이름이 같은 것이 여러 개 존재하는 것
사용자 입장에서는 하나의 메소드를 사용하지만,
호출될 때 전달되는 매개변수에 따라 다른 메소드를 호출

>Override : 
반드시 상속관계일 때 부모에 있는 메소드를 자식 클래스 내에 동일하게 만드는 것
부모의 있는 메소드의 기능이 마음에 들지 않아 다른 일을 하고 싶을 때
메소드를 Override를 하여 다른 기능을 구현함

## 컬렉션 프레임워크의 인터페이스

다음은 내가 참고하고 있는 책인 「이것이 자바다」 라는 책에서 컬렉션 프레임워크를
설명한 것이다.
![image1](https://user-images.githubusercontent.com/68142821/107894814-49348400-6f74-11eb-8582-f1afbe062d1b.jpg)

위의 사진에서 화살표의 모습은 다음 사진을 보도록 하자.
![image2](https://user-images.githubusercontent.com/68142821/107894842-65d0bc00-6f74-11eb-80c5-bbe12374c7b6.jpg)
