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


# IO Stream
> IOStream을 사용하려면 다음 항목을 추가해야 한다.
> import java.io.*;

Java는 Input, Output과 관련된 IOStream API를 제공한다.
읽기와 쓰기를 동시에 할 수 없으며, **단방향 스트림**이다.

처리를 할 때 여러가지 방법이 있는데 다음과 같다.

**byte단위로 처리**
byte는 영어의 경우 1byte를 차지, 한글의 경우는 2byte를 차지하며, 이와 같은 방식으로 읽어내는 것을 의미한다. *(파일 전송, 동영상, 이미지 파일 등의 처리)*
- byte단위로 읽기 - InputStream
- byte단위로 쓰기 - OutputStream

**문자단위로 처리**
영어와 한글 상관 없이 하나의 문자로 처리한다는 것을 의미한다. *(채팅프로그램 등)*
- 문자단위로 읽기 - Reader
- 문자단위로 쓰기 - Writer

용어 정리
- 노드 Stream - 읽기, 쓰기를 바로 할 수 있는 것. 반드시 필요함.
- 필터(보조) Stream - 중간에 읽기 또는 쓰기를 보조stream에 넣어 가공하는 역할. 없어도 읽기, 쓰기 가능 ex) 입출력향상에 도움을 주는 Buffered사용 / byte단위 -> 문자단위로 OR 문자단위 -> byte단위로 변환

## File
읽기 메소드
> ~.read()

InputStream과 Reader의 각 read()메소드는 같은 의미가 아니다.
[Java Platform SE 8](https://docs.oracle.com/javase/8/docs/api/) 사이트에서 찾아보면 각각 해당하는 의미를 찾아볼 수 있다.

쓰기 메소드
> ~.write()

OutputStream과 Writer의 각 write()메소드는 같은 의미가 아니다.

File에 대한 IO 코딩 순서는 다음과 같다.
절대적인 것은 아니지만, 나름의 틀과 순서를 지키는 것이 **프로그램의 규모가 커짐**에 따라 빛을 발할 수 있다.
```java
try {
	1) 생성
	2) 읽기 또는 쓰기
} catch () {

} finally {
	3) 닫기
}
```
하지만 위의 순서를 지키려고 하면 다음과 같은 오류가 발생할 수 있다.
```java
	public FileInputStreamExam() {
		try {
			FileInputStream fis = new FileInputStream("src/ex0218/io/test.txt");
		} catch (Exception e) {
			
		} finally {
			fis.close(); // 오류 발생! fis가 지역 변수임
		}
	}
```
> "src/ex0218/io/text.txt"// 필자는 eclipse를 사용하기 때문에 **현재 실행 중인 패키지**가 기반이 된다.
> 이것은 각자가 파일 경로를 새롭게 설정해주어야 한다.

fis는 try 내부에서 선언된 지역변수이며, 따라서 finally에서 사용할 수 없는 것이다.
사용하기 위해서는 밖으로 빼내는 작업이 필요한 것이다.
그러면 다음 코드로 변환을 해보자.
```java
		FileInputStream fis = null;
		try {
			fis = new FileInputStream("src/ex0218/io/test.txt");
		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			try {
				fis.close();
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
```
그랬더니 코드가 정말 지저분해 보인다.
fis.close()는 try구문 안에 있어야 Exception으로 catch가 가능하기 때문에 무조건 try내부에 있어야 한다.
그러다 보니 finally 안에서 또 다른 try 구문이 발생하고, 점차 코드는 길어져만 간다.
그래서 나온 문법이다. 다음 코드를 보자.
```java
	public FileInputStreamExam() {
		try (FileInputStream fis = new FileInputStream("src/ex0218/io/test.txt")){

		} catch (Exception e) {
			e.printStackTrace();
		} finally {

		}
	}
```
이는 자동으로 try구문이 끝나게 되면 **'자동 리소스 닫기'** 기능이 실행된다.
도중에 예외가 발생이 될 때 자동으로 close()메소드가 호출이 되어 **close()가 실행된 이후** catch블록이 실행이 되는 것이다.

## File 여러가지 예제
먼저 InputStream에 대한 예제를 확인한 후에 OutputStream을 사용해보기로 한다.

### FileExam.java
```java
public class FileExam {
	public FileExam() throws IOException {
		String fileName = JOptionPane.showInputDialog("파일 이름은?");
		File file = new File(fileName);
		if(file.isDirectory()) { //fileName에 해당하는 것이 폴더라면 폴더리스트 출력
			System.out.println("=======" + fileName + "폴더 정보=======");
			String fnames [] = file.list();
			for(String name : fnames) {
				System.out.println(name);
			}
		} else { //fileName에 해당하는 것이 파일이면 파일에 대한 정보들을 출력
			System.out.println("=======" + fileName + "파일 정보=======");
			System.out.println("읽기기능 = " + file.canRead());
			System.out.println("쓰기기능 = " + file.canWrite());
			System.out.println("파일이름 = " + file.getName());
			System.out.println("절대경로 = " + file.getAbsolutePath());
			System.out.println("파일크기 = " + file.length());
			System.out.println("마지막 수정일 = " + file.lastModified());
			
			System.out.println(new Date(file.lastModified()).toLocaleString());
		}
	}
	public static void main(String[] args) throws IOException{
		new FileExam();
	}
}
```
```java
//결과화면
=======src\ex0218\io\newTextFile.txt파일 정보=======
읽기기능 = true
쓰기기능 = true
파일이름 = newTextFile.txt
절대경로 = C:\Users\oueya\Desktop\java\JavaWorkspace\step07_IOExam\src\ex0218\io\newTextFile.txt
파일크기 = 18
마지막 수정일 = 1613625682148
2021. 2. 18. 오후 2:21:22
```

### FileInputStreamExam.java
```java
public class FileInputStreamExam {
	public FileInputStreamExam() {
		try (FileInputStream fis = new FileInputStream("src/ex0218/io/test.txt")){
			int size = fis.available(); // test.txt에 있는 사용한 공간 알아내어서
			byte b [] = new byte[size]; //byte의 크기 배열만큼 저장
			fis.read(b); //b를 이용하여 fis 객체에 읽어들이고
			String name = new String(b); //String에서는 바이트를 읽을 수 있도록 String으로 변환하는 기능을 제공한다.
			System.out.println(name); //그 String에 담긴 내용을 출력
		} catch (Exception e) {
			e.printStackTrace();
		} finally {

		}
	}
	
	public static void main(String[] args) {
		new FileInputStreamExam();
	}
}
```

### FileCopyExam.java
```java
public class FileCopyExam {
	public FileCopyExam(String r, String w) {
		FileInputStream fis = null;
		FileOutputStream fos = null;
		try {
			fis = new FileInputStream(r);
			fos = new FileOutputStream(w);
			
			int size = fis.available();
			byte [] d = new byte[size];
			fis.read(d);
			
			//String data = new String(d);
			fos.write(d);
		} catch(Exception e) {

		} finally {
			try {
				fis.close();
				fos.close();
			} catch (Exception e) {
				
			}
		}
	}
	
	public static void main(String[] args) {
		String readFile = JOptionPane.showInputDialog("읽을 파일 명");
		String writeFile = JOptionPane.showInputDialog("저장할 파일명");
		new FileCopyExam(readFile, writeFile);
	}
}

```
항상 메소드나 알아야 하는 클래스가 존재한다면 api docs를 무조건 참고하는 편이다.
꼭 참고하도록 하자.

## 보조Stream
아까 정리했던 byte단위 Stream과 문자 단위 Stream을 정리했었다.
보조Stream은 다음과 같이 바뀐다.

**byte단위로 처리**
- byte단위로 읽기 - InputStream -> BufferedInputStream
- byte단위로 쓰기 - OutputStream -> BufferedOutputStream 

**문자단위로 처리**
- 문자단위로 읽기 - Reader -> BufferedReader
- 문자단위로 쓰기 - Writer -> BufferedWriter

그래서 위에서 그저 사용했었던 FileInputStream을 BufferedInputStream으로 바꾸어 줌으로써
속도로 이득을 볼 수 있다.
```java
public class FileCopyBufferedExam {
	public FileCopyBufferedExam(String r, String w) {
		BufferedInputStream fis = null;
		BufferedOutputStream fos = null;
		try {
			fis = new BufferedInputStream(new FileInputStream(r));
			fos = new BufferedOutputStream(new FileOutputStream(w));
			
			int i = 0;
			while((i = fis.read()) != -1) {
				fos.write(i);
			}
		} catch(Exception e) {

		} finally {
			try {
				fis.close();
				fos.close();
			} catch (Exception e) {
				
			}
		}
	}
	
	public static void main(String[] args) {
		String readFile = JOptionPane.showInputDialog("읽을 파일 명");
		String writeFile = JOptionPane.showInputDialog("저장할 파일명");
		
		long startTime;
		long endTime;
		
		startTime = System.nanoTime();
		new FileCopyBufferedExam(readFile, writeFile);
		endTime = System.nanoTime();
		System.out.println(endTime - startTime);
	}
}
```

Buffered으로 바꾸었더니 700KB정도 되는 사진을 복사했을 때 무려
4105883900 - 39798600 = 4066085300 즉, **4.0660853초 차이**가 발생했다.

700KB밖에 안되어도 복사하고 붙여 넣는데 4초의 시간 차이가 발생한다.

## 키보드 입력
키보드 입력은 기본적으로 InputStream이기 때문에
Reader으로 변환하는 과정이 필요로 하다.
그때 사용하는 것이 보조Stream이며 **InputStreamReader을 사용**하면 된다.
```java
public class KeyBoardInputExam {
	public KeyBoardInputExam() {
		try {
			BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
			System.out.print("입력 > ");
			String str = br.readLine();
			System.out.println("결과 : " + str);
		} catch (Exception e) {
			e.printStackTrace();
		}
	}
	
	public static void main(String[] args) {
		new KeyBoardInputExam();
	}
}
```

# List

List 컬렉션은 객체를 일렬로 늘어놓은 구조이다.
**객체를 인덱스로 관리하며, 객체 자체를 저장하는 것이 아닌, 객체의 번지를 참조한다**

ArrayList, Vector, LinkedList가 있으며, 각각 정의할 때에는
`ArrayList<String> list = new ArrayList<String>(5);` 의 형태보다는
`List<String> list = new ArrayList<String>(5);` 의 형태를 선호한다.

**List**란 각 구현 클래스의 틀을 정하는 **interface**로 정의되어 있으며
사용자의 편의로 인해, 또는 어떤 기업의 요구에 맞게 ArrayList에서 속도가 좀더 빠른 LinkedList로 변환하기를 요구할 수 있다.

즉, 새로운 상황이나 유지 보수 측면에서는 List로 선언하는 것이 다른 변화에 위험 부담이 적다는 뜻이다.

## ArrayList

일반 배열과 ArrayList는 인덱슬 객체를 관리한다는 점에서는 유사하지만,
**배열**은 생성할 때 크기가 고정되고, 사용 중에 크기를 변경할 수 없지만,
**ArrayList**는 저장 용량을 초과한 객체가 들어오면 자동적으로 용량이 늘어난다.

```java
List<String> list1 = new ArratList<String>();
//or
List<String> list2 = new ArrayList<String>(30);
```

ArrayList의 초기 생성자 저장 용량은 10이다. 생성자가 오버로딩 되어 있어 int형으로 인수를 넘겨준다면, 그 값이 **ArrayList의 저장 용량**이 된다.


이처럼 ArrayList class를 바로 사용해도 되지만, 나만의 리스트를 만들고 싶다면, ArrayList를 상속하는 class를 만들면 된다.
```java
import java.util.ArrayList;

class ArrayListExam extends ArrayList{

	Object obj;
	int i;

	//위에서 설명했듯 ArrayList는 생성자로 int가 들어오면 저장 용량이 된다.
	public ArrayListExam(){
			super(5);
	}
	
	.
	.
	.
```

메소드를 다음과 같이 만들어 보았다.
```java
	public void addInt(int i) {
		Integer it =  new Integer(i);
		add(it);
	}
		
	public void addFloat(float f) {
		Float ft = new Float(f);
		add(ft);
	}
	
	public void addString(String s) {
		add(st);
	}
	
	public void printList() {
		i = size();
		System.out.println("list에 들어있는 객체의 총 개수는 = " + i);
		for(int count = 0; count < i; count++) {
			obj = get(count);
			System.out.println(obj);
		}
	}
}
```

여기서 알아야 할 것은 add메소드는 모두 상속받고 있는 **ArrayList**에서 제공하고 있는 것이며,
super.add( .. ) 와 같은 맥락이라고 보면 된다.

`Integer it =  new  Integer(i)`의 과정이 필요한 이유는 ArrayList에는 객체만 담을 수 있기 때문에, i를 포장 객체로 만들어야만 한다. 그것을 위해 박싱(Boxing)의 과정을 거친 것이다.

그러나 Java는 오토박싱을 지원한다. 즉, `Integer int = new Integer();` 과정을 거치지 않아도, 인수로 받은 i 가 **자동으로 Integer Boxing**이 되어 저장된다는 것이다.

> Integer it =  new ~~Integer~~(i) 와 같은 모습이 보인다면,
> `Integer it =  Integer.valueOf(i)` 으로 교체해주길 바란다.
> 이는 version 9 이후에 새로운 문법에서 추천하지 않는 구문이기 때문이다.

## Vector
Vector는 ArrayList와 동일한 구조를 취하고 있기 때문에 어렵지 않다. 또한 생성하는 방법도 같기 때문에 상위 인터페이스인 List를 객체에 취함으로써 생성하는 것이 효율적이다.
`List<E> list = new Vector<E>();`

Vector는 ArrayList와 차이점은 **동기화된 메소드로 구성**되어 있는가, 아닌가 이다. Vector는 동기화된 메소드로 구성되어 있기 때문에 하나의 스레드가 실행을 완료해야만 다른 스레드를 실행할 수 있다.

간단히 정리하자면, ArrayList는 동기화가 되어있지 않아 속도 면에서 우월하지만, Vector는 속도가 조금 느린 대신에 스레드가 안전하다고 볼 수 있는 것이다.

ArrayList와 Vector는 각각 포함하고 있는 **상속과 인터페이스**가 같기 때문에,
큰 차이 없이 메소드를 이용하면 된다.
### ArrayList
![20210215103838](https://user-images.githubusercontent.com/68142821/107896593-0ecde580-6f7a-11eb-9496-bee75981d6d4.png)

### Vector
![20210215103851](https://user-images.githubusercontent.com/68142821/107896595-0f667c00-6f7a-11eb-956f-dd9aa459a60a.png)

## LinkedList
ArrayList와 Vector 모두 같은 인터페이스를 상속 받았기에 사용하는 방법이 매우 유사하였다.
하지만 그 둘이 내부 배열에 객체를 저장하여 인덱스로 관리하였다고 하면
LinkedList는 **인접 참조를 링크**하는 방식이다.

> 다음 사진을 보면 Deque라는 인터페이스가 추가로 있는 것이 보인다.
![20210215105310](https://user-images.githubusercontent.com/68142821/107897263-fe1e6f00-6f7b-11eb-896b-aeea0f20d89d.png)


ArrayList와 Vector에서는 하나의 인덱스를 제거하면, 그 뒤에 있는 모든 요소들이 한 칸씩 앞으로 자리하게되는 구조였다.

즉, 객체를 빈번하게 제거하고, 추가하는 작업에서의 ArrayList는 **속도가 느려질 수** 밖에 없다.

그러나 LinkedList는 수정이 일어나는 곳 **앞, 뒤**에서 link하는 객체를 바꾸기만 하면 되기 때문에 객체의 삽입, 수정, 삭제의 요청에 효율적으로 응할 수 있다.
<img  src = "https://user-images.githubusercontent.com/68142821/107897840-8cdfbb80-6f7d-11eb-863f-1005bc779467.jpg"  width="60%">  

다음은 ArrayList와 LinkedList의 속도 차이를 보자.
```java
import java.util.*;
public class LinkedListExample {
	public static void main(String[] args) {
		List<String> list1 = new ArrayList<String>();
		List<String> list2 = new LinkedList<String>();
	
		long startTime;
		long endTime;
		
		//임의의 고정된 구간에서 nano seconds의 값을 반환합니다.
		//자바 버전 **1.5 이상**
		startTime = System.nanoTime();
		for(int i = 0; i < 10000; i++) { //0번지에 지속적으로 추가하여 객체가 뒤로 밀려나게 합니다.
			list1.add(0, String.valueOf(i));
		}
		
		//nano seconds 끝
		endTime = System.nanoTime();
		System.out.println("ArrayList 걸린시간: " + (endTime-startTime) + " ns");
		
		startTime = System.nanoTime();
		for(int i = 0; i < 10000; i++) {
			list2.add(0, String.valueOf(i));
		}
		endTime = System.nanoTime();
		System.out.println("LinkedList 걸린시간: " + (endTime-startTime) + " ns");
	}
}
```

다음은 결과이다.
```java
ArrayList 걸린시간: 12056400 ns
LinkedList 걸린시간: 4505500 ns
```

고정 값은 아니지만, 이 실험으로 객체의 삽입에는 LinkedList가 속도가 빠르다는 것을 알 수 있다.
지금은 0번지에 계속 객체를 삽입하여 ArrayList측에서는 객체가 계속 뒤로 밀려나게 했지만,
순차적으로 추가하거나 삭제하는 부분에서는 LinkedList보다 ArrayList가 더 빠를 것이다.

**중요한 점은 어떤 상황에서 어느 컬렉션이 더 나을 것인가를 생각해보고, 구현하는 것이 올바른 선택이라는 것이다.**

# Set
이전 post에서 List에 관한 내용을 알아보았다.
List에서 알아본 ArrayList, Vector, LinkedList는 모두 하나의 공통점을 가지고 있었는데,
**저장 순서를 유지한다는 점**이다.

remove를 통해 중간 인덱스에 해당하는 객체를 지워도, 뒤에 있는 모든 객체는 앞으로 한 칸씩 앞당겨 졌고,
중간에 삽입 과정을 거쳐도, 뒤로 한 칸씩 전체적으로 밀려났다.
> 물론 LinkedList는 밀려난다는 표현보다는 중간의 Link가 달라진다는 말이 더 어울릴 것 같다.

다음은 Oracle의 docs에 있는 Java Platform SE 8 의 공식 문서이다.
Set은 어떤 메소드를 가지고 있는지 한 번 확인해보자.
![20210215115023](https://user-images.githubusercontent.com/68142821/107900332-0d091f80-6f84-11eb-85ee-b2668d4e85ac.png)
*출처 : [Oracle docs](https://docs.oracle.com/javase/8/docs/api/)*

Set의 개념을 조금 더 잘 이해해보기 위해 간단한 코드를 작성해보았다.
SetExam은 HashSet을 상속하였고, 그 클래스 자체가 Set이 되어 사용해보는 것이다.

실행하기 전 String[] args으로 인수를 넘긴 것은 다음과 같다
=> 1 2 3 4 5 **3 4 5** 6
```java
	import java.util.HashSet;

	public class SetExam extends HashSet<String> {
		public SetExam(String[] args) {
			//super(5);
			for(String s : args) {
				boolean re = super.add(s);
				System.out.println(s + "추가 결과 : " + re);
			}
			
			System.out.println("--등록결과--");
			System.out.println(this); //this는 this.toString()과 같은 의미이다.
		}
		public static void main(String[] args) {
			new SetExam(args);
		}
	}
```
```java
	1추가 결과 : true
	2추가 결과 : true
	3추가 결과 : true
	4추가 결과 : true
	5추가 결과 : true
	3추가 결과 : false
	4추가 결과 : false
	5추가 결과 : false
	6추가 결과 : true
	--등록결과--
	[1, 2, 3, 4, 5, 6]
```

Set은 중복이 되지 않는다고 했다.
그러면 다음과 같은 코드는 중복일 수 있을까?
```java
import java.util.HashSet;
import java.util.Set;

public class MemberSetExam {
	//Member라는 클래스가 하나 존재한다고 가정한다.
	Set<Member> set = new HashSet<Member>();
	
	public MemberSetExam() {
		set.add(new Member("김동헌", 24, "서울"));
		set.add(new Member("김가람", 28, "대구"));
		set.add(new Member("김동헌", 24, "서울"));
		
		System.out.println("저장된 객체 수 : " + set.size());
	}
	
	public static void main(String[] args) {
		new MemberSetExam();
	}
}
```
답은 **아니다**이다.

```java
저장된 객체 수 : 3
```

이는 객체가 각각 가지고 있는 hashCode와 equals의 메소드 기능을 살펴보아야 하는데,
Set에서 add()메소드를 통해 객체를 삽입할 때 해당 객체의 hashCode() 메소드가 실행이 되며,
같은 hashCode를 가지고 있는 객체가 존재할 경우 equals() 메소드가 실행이 된다.

이 구문이 포함이 된다고 했을 때 결과 창을 살펴보자.

```java
	@Override
	public int hashCode() {
		System.out.println("hashCode 실행");
		return name.hashCode() + age;
	}
	
	@Override
	public boolean equals(Object obj) {
		System.out.println("equals 실행");
		if(obj instanceof Member) {
			Member mb = (Member) obj;
			if(name.equals(mb.getName()) && age == mb.getAge()) {
				return true;
			}
		}
		return false;
	}
```
```java
	//결과창
	
	hashCode 실행
	hashCode 실행
	hashCode 실행
	equals 실행
	저장된 객체 수 : 2
```
## Iterator

Set컬렉션은 객체를 검색하는 메소드가 존재하지 않기 때문에 전체 객체를 대상으로 한 번씩 반복해서 가져오는 반복자 **Iterator**를 제공한다.

어떻게 사용하는지 바로 알아보도록 하자.
```java
	import java.util.HashSet;

	public class SetExam extends HashSet<String> {
		public SetExam(String[] args) {
			//super(5);
			for(String s : args) {
				boolean re = super.add(s);
				System.out.println(s + "추가 결과 : " + re);
			}
			
			System.out.println("--등록결과--");
			System.out.println(this);
			
			// 추가된 부분
			Iterator<String> it = super.iterator(); //super인 이유는 부모인 HashSet을 따르고 있기 때문이다.
			while(it.hasNext()) {
				String name = it.next();
				System.out.println(name);
			}
			// 추가된 부분
			
		}
		public static void main(String[] args) {
			new SetExam(args);
		}
	}
```

# Map
List와 Set은 하나의 **공간**에 하나의 **값**이 들어간 것이라고 인식할 수 있다.
Map은 하나의 공간에 **두 개의 값**이 들어간 것이라고 생각하면 편하다.

물론 정확히 말하자면, key값과 value값으로 구성된 객체를 저장하는 구조를 가지고 있다.

지금껏 List<**E**>, Set<**E**> 이라고 표현해왔고, E는 **the type of elements maintained by this set**를 의미했다.

Map에서는 Map<**K**, **V**>과 같은 방법으로 표기를 하는데
K는 **the type of keys maintained by this map**,
V는 **the type of mapped values**를 의미하는 것이다.

~~그냥 Key, Value라고 외우면 편하다~~

## Hash Map
다음 예시를 보고 Map이 어떤 역할을 하는지 빠르게 이해해보자.
```java
public class MapExam extends HashMap<Integer, String>{
	public MapExam() {
		put(10, "동원");
		put(30, "동투");
		put(20, "동쓰리");
		put(10, "동포");
		put(40, "동파이브");
		put(30, "동식스");
		
		System.out.println("추가 후 : " + this);
	}
	
	public static void main(String[] args) {
		new MapExam();
	}
}
```

HashMap을 상속받았기 때문에 super.put 메소드를 사용할 수 있으며,
List에서 사용한 add와 비슷한 개념으로 생각하면 된다.
```java
	//결과
	추가 후 : {20=동쓰리, 40=동파이브, 10=동포, 30=동식스}
```

`put(10,  "동원");`  `put(30,  "동투");`가 먼저 실행되었음에도 '**동쓰리**'가 먼저 출력되었다.
이는 Set과 동일하게 **값의 중복을 피하는** 결과를 나타냈지만, 다른 시선으로 보면 **중복 값을 삽입하게 되면 원래 있던 값은 사라지고, 새로운 값으로 저장**이 되는 결과를 보여주었다.

## TreeMap
HashMap과 TreeMap의 가장 큰 차이는 TreeMap은 **정렬**이 된다는 것이다!
이참에 제거하는 방법까지 알아본다.
```java
public class MapExam extends TreeMap<Integer, String>{
	public MapExam() {
		put(10, "동원");
		put(30, "동투");
		put(20, "동쓰리");
		put(10, "동포");
		put(40, "동파이브");
		put(30, "동식스");
		
		System.out.println("추가 후 : " + this);
		String value = super.remove(30);
		System.out.println("제거된 객체 : " + value);
		System.out.println("제거 후 : " + this);
	}
	
	public static void main(String[] args) {
		new MapExam();
	}
}
```

```java
추가 후 : {10=동포, 20=동쓰리, 30=동식스, 40=동파이브}
제거된 객체 : 동식스
제거 후 : {10=동포, 20=동쓰리, 40=동파이브}
```

## Iterator
Set에서 배웠던 방법으로 Iterator를 사용하여 하나씩 꺼내는 연습을 해본다.
```java
public class MapExam extends TreeMap<Integer, String>{
	public MapExam() {
		put(10, "동원");
		put(30, "동투");
		put(20, "동쓰리");
		put(10, "동포");
		put(40, "동파이브");
		put(30, "동식스");
		
		System.out.println("추가 후 : " + this);
		String value = super.remove(30);
		System.out.println("제거된 객체 : " + value);
		System.out.println("제거 후 : " + this);
		
		//Iterator
		Set<Integer> set = super.keySet();
		Iterator<Integer> it = set.iterator();
		while(it.hasNext()) {
			int k = it.next();
			String v = super.get(k);
			System.out.println(k + " : " + v);
		}
	}
	
	public static void main(String[] args) {
		new MapExam();
	}
}
```

Iterator구문은 개선된 for문으로 변환할 수 있다.
```java
for(Integer k : super.keySet()) {
	String v = super.get(key);
	System.out.println(k + " : " + v);
}
```

다음과 같은 방법도 있다.

Set에 Map의 Key와 Value가 담긴 하나의 Entry를 entrySet() 메소드를 통해 담는 것이다.
위의 Iterator방법과 동일하지만 Integer대신에 Map.Entry<Integer, String>을 통하여 담는 방법으로,
조금 복잡해 보이기는 한다.
```java
Set<Map.Entry<Integer, String>> entrySet = super.entrySet();
Iterator<Map.Entry<Integer, String>> entryIt = entrySet.iterator();
while(entryIt.hasNext()) {
	Map.Entry<Integer, String> entry = entryIt.next();
	int k = entry.getKey();
	String v = entry.getValue();
	System.out.println(k + " : " + v);
}
```

위의 방법에도 개선된 for 구문으로 조금 쉽게 가능하다.
```java
for(Map.Entry<Integer, String> e : super.entrySet()) {
	Integer k = e.getKey();
	String v = e.getValue();
	system.out.println(k + " : " + v);
}
```
