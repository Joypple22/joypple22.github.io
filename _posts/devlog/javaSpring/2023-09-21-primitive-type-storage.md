---
layout: post
title: "[Java] Primitive Type 데이터가 저장되는 공간은 어딜까?"
subtitle: "javap로 디어셈블하여 Primitive type 데이터를 확인해보자."
categories: devlog
tags: java primitive-type
---

> javap CMD를 사용하여 어떻게 동작하는지 비교합니다.

<!--more-->

## 🌱 Primitive Type 데이터란?

Java에서 Primitive Type 데이터는 아주 기본적인 타입, 더이상 나눌 수 없는 단위의 데이터를 의미하며 8가지의 기본 타입 데이터가 존재합니다.

Primitive type 데이터에 대한 자세한 정보는 Java입문서와 블로그에서 확인할 수 있었으나, 어디에 저장되는지는 명확하게 파악할 방법이 없었습니다. 이에 
이를 알아보고자 합니다.

---

## 🌱 블로깅의 발단

참조형 데이터는 대개 Heap 영역에 저장된다고 알려져 있습니다. 그리고 Primitive type 데이터는 JVM Stack에 저장된다고 알려져 있습니다. 하지만 사실 Primitive Type 데이터에는 
많은 이야기들이 돌고 있습니다.

- 원시 타입 데이터 또한 참조형 데이터처럼 원시 타입 데이터가 저장된 메모리의 주소를 비교한다.
- 원시 타입 데이터는 컴파일 시점에 Constant Pool에 저장되고 런타임 시점에 Constant Pool에서 저장된 데이터의 주소를 JVM Stack에 저장한다.
- 원시 타입 데이터를 == 연산자로 비교할 때는 `값`을 비교하고, 참조형 데이터를 == 연산자로 비교할 때는 `데이터 주소`를 비교한다. (그래서 참조형 데이터는 equals를 사용한다.)
- 반대로 원시 타입 데이터 또한 == 연산자로 비교할 때 데이터 주소를 비교한다.
- 클래스 안의 맴버 변수로 선언된 원시 타입 데이터는 그대로 Heap에 저장된다.

등등 수많은 정보들을 접할 수 있는데.. 어떤 정보는 다른 정보와 정반대되는 의미를 가지고 있다는 것을 확인했습니다. 그래서 이 부분을 명확하게 정리하고 싶은 생각이 블로깅의 발단이 되었습니다.

----

## 🌱 어떻게 확인할 수 있을까?

> Java JDK 17을 사용하고 있습니다.

값이 Constant Pool에 저장되는지 그저 Stack에 저장되는지 사실 공식 문서를 제외하고는 명확하게 확인할 방법이 없지 않나 생각했습니다. 하지만 javap로 디어샘블링하여 실행되는 클래스가 constant pool에 어떤 값을 
참조하고 있는지는 파악할 수 있지 않을까 생각하여 javap를 사용해보기로 했습니다.

```java
public class Parent {

	static int b = 14;

	public static void main(String[] args) {
		int a = 11;

		int c = a + b;
	}
}
```

이 코드를 통해 여러가지 가설을 세워보았습니다.

- b에 할당된 14는 Parent가 로드될 때 초기화되는 static 변수이다. static 데이터가 Heap에 저장되는지, Metaspace에 저장되는지 (Java8 이후)는 [논란이 많은 주제라고 생각되지만](https://stackoverflow.com/questions/8387989/where-are-static-methods-and-static-variables-stored-in-java),
결국 b는 Parent 객체에 할당된 값이기 때문에 Constant Pool이 아닌 Parent로부터 참조될 것이다.
- 로컬 변수 a에 할당된 11은 Constant Pool에 저장되어 참조되는 것이 아니라, Parent의 main 메소드가 컴파일 되어 바이트코드로 변환될 때 수집된 정보로써, 바이트코드가 실행될 때 JVM stack에 각 데이터가 저장된다.
- b에 할당된 데이터는 참조를 통해, a에 할당된 데이터는 바이트코드를 통해 알 수 있으며, a + b의 결과인 c는 JVM 스택에서 연산된 값이므로 Constant Pool에 저장될 필요없이 JVM Stack에서 연산되어 소모된다. 

해당 가설과 함께 위의 코드를 javap로 디어샘블 해보았습니다.

```
Classfile ***
  Compiled from "Parent.java"
public class Parent
  minor version: 0
  major version: 61
  flags: (0x0021) ACC_PUBLIC, ACC_SUPER
  this_class: #8                          // Parent
  super_class: #2                         // java/lang/Object
  interfaces: 0, fields: 1, methods: 3, attributes: 1
Constant pool:
   #1 = Methodref          #2.#3          // java/lang/Object."<init>":()V
   #2 = Class              #4             // java/lang/Object
   #3 = NameAndType        #5:#6          // "<init>":()V
   #4 = Utf8               java/lang/Object
   #5 = Utf8               <init>
   #6 = Utf8               ()V
   #7 = Fieldref           #8.#9          // Parent.b:I
   #8 = Class              #10            // Parent
   #9 = NameAndType        #11:#12        // b:I
  #10 = Utf8               Parent
  #11 = Utf8               b
  #12 = Utf8               I
  #13 = Utf8               Code
  #14 = Utf8               LineNumberTable
  #15 = Utf8               main
  #16 = Utf8               ([Ljava/lang/String;)V
  #17 = Utf8               <clinit>
  #18 = Utf8               SourceFile
  #19 = Utf8               Parent.java
{
  static int b;
    descriptor: I
    flags: (0x0008) ACC_STATIC

  public Parent();
    descriptor: ()V
    flags: (0x0001) ACC_PUBLIC
    Code:
      stack=1, locals=1, args_size=1
         0: aload_0
         1: invokespecial #1                  // Method java/lang/Object."<init>":()V
         4: return
      LineNumberTable:
        line 1: 0

  public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: (0x0009) ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=3, args_size=1
         0: bipush        11
         2: istore_1
         3: iload_1
         4: getstatic     #7                  // Field b:I
         7: iadd
         8: istore_2
         9: return
      LineNumberTable:
        line 6: 0
        line 8: 3
        line 9: 9

  static {};
    descriptor: ()V
    flags: (0x0008) ACC_STATIC
    Code:
      stack=1, locals=0, args_size=0
         0: bipush        14
         2: putstatic     #7                  // Field b:I
         5: return
      LineNumberTable:
        line 3: 0
}
SourceFile: "Parent.java"
```

...

먼저, Constant Pool 입니다.

```
Constant pool:
   #1 = Methodref          #2.#3          // java/lang/Object."<init>":()V
   #2 = Class              #4             // java/lang/Object
   #3 = NameAndType        #5:#6          // "<init>":()V
   #4 = Utf8               java/lang/Object
   #5 = Utf8               <init>
   #6 = Utf8               ()V
   #7 = Fieldref           #8.#9          // Parent.b:I
   #8 = Class              #10            // Parent
   #9 = NameAndType        #11:#12        // b:I
  #10 = Utf8               Parent
  #11 = Utf8               b
  #12 = Utf8               I
  #13 = Utf8               Code
  #14 = Utf8               LineNumberTable
  #15 = Utf8               main
  #16 = Utf8               ([Ljava/lang/String;)V
  #17 = Utf8               <clinit>
  #18 = Utf8               SourceFile
  #19 = Utf8               Parent.java
```

이 바이트코드를 보면 Constant Pool에 정수형 데이터가 저장되어 있는 것을 확인할 수 없습니다. Constant Pool은 대개 타입, 이름, 상수 등의 정보들을 저장해놓는 공간이지만, 
숫자형 데이터라고해서 모든 데이터가 상수가 되는 것은 아니라는 것을 확인할 수 있었습니다.

"그냥 상수형 데이터는 안나온거일 수도 있잖아요!" 라고 할 수 있을 것 같아 main 메소드에 `String d = "hello"`를 선언해보았습니다.

```
#10 = Utf8               Parent
#13 = String             #14            // hello
#14 = Utf8               hello
#15 = Utf8               Code
```

신기하게도 hello라는 데이터가 Constant Pool에 저장되어 있는 것을 확인할 수 있었습니다. String을 문자 리터럴 방식으로 선언하면 Constant Pool에 저장되는 특징을 가지고 있습니다. 이를 통해 
숫자형 데이터가 Constant Pool에 저장되지 않는다는 것을 반증할 수 있습니다.

...

그럼 14, 11 등의 데이터는 어디에 있을까요? 

```
public static void main(java.lang.String[]);
    descriptor: ([Ljava/lang/String;)V
    flags: (0x0009) ACC_PUBLIC, ACC_STATIC
    Code:
      stack=2, locals=4, args_size=1
         0: bipush        11
         2: istore_1
         3: iload_1
         4: getstatic     #7                  // Field b:I
         7: iadd
         8: istore_2
         9: ldc           #13                 // String hello
        11: astore_3
        12: return
      LineNumberTable:
        line 6: 0
        line 8: 3
        line 10: 9
        line 11: 12

  static {};
    descriptor: ()V
    flags: (0x0008) ACC_STATIC
    Code:
      stack=1, locals=0, args_size=0
         0: bipush        14
         2: putstatic     #7                  // Field b:I
         5: return
      LineNumberTable:
        line 3: 0
```

javap로 볼 수 있는 Constant Pool이외에 2가지 데이터는 main 메소드가 실행될 때, static 변수에 값이 초기화될 때 입니다. 해당 바이트코드를 보면, 어디에서 가져온지도 모른체 
bipush로 11과 14를 각각 JVM Stack에 저장하는 것을 볼 수 있습니다.

11과 14 데이터는 Constant Pool에서 참조하지 않고, 메소드 내에서 직접 처리되는 것을 확인할 수 있습니다. 즉, 메소드 정보로 값을 가지고 있다가 런타임 시 바이트코드가 인터프리터 등으로 해석될 때 
JVM Stack에 저장되는 것입니다.

---

## 🌱 위의 분석을 통해 어떤 것을 알게 되었나요?

원시 타입 데이터라고 해서 모두 Constant Pool에 저장되는 것은 아닙니다. 또한, 누군가 작성했던 Constant Pool에 저장된 원시 타입 데이터의 주소를 비교하여 동일성을 판단한다는 것도 사실이 아님을 볼 수 있었습니다.

결론으로 정리할 수 있는 것들은 다음과 같습니다.

### 🟤 직접적으로 알 수 있었던 것들
- Primitive Type 데이터의 동일성을 비교할 때는 주소가 아닌 `값`을 비교한다. (참조형 데이터는 주소값을 비교하는게 맞다.)
- 메소드에서 연산되어 새롭게 생성되는 데이터 (ex. int i = a + b)는 Constant Pool같은 별도의 공간에 할당되는 것이 아니라 JVM Stack에서 연산되어 바로 소모된다.


### 🟤 간접적으로 알 수 있었던 것들
- static 으로 선언된 데이터는 Method Area의 static 영역에 저장되고, 클래스의 맴버 변수로 선언된 원시 타입 데이터는 객체 인스턴스와 함께 Heap 영역에 저장된다.
- String은 Constant Pool에 선언된 문자열 그대로 저장되기 때문에 String값을 무차별적으로 + 연산하면 Constant Pool의 메모리 공간을 낭비할 수 있다. (StringBuilder, StringBuffer 사용 권장)

----

## 🌱 결론

사실 이렇게 블로그를 작성했지만 위의 결과가 틀릴 수도 있습니다. 지금은 oracle에서 만든 Java 17버전을 사용한 결과이지만, Java의 종류와 제공하는 회사는 여러개 존재한다. 자바라는 큰 인터페이스에서 명세하는 
기능을 어떤 방식으로 구현한다면 그것은 Java이기 때문에 세부적으로 동작하는 원리는 각 Java 종류마다 다를 수 있습니다.

> 따라서, Oracle의 openJDK 한정으로 클래스 안에 있는 숫자형 리터럴 데이터는 어디에서 어떻게 선언되었느냐에 따라 저장되는 위치가 다르다.
> 또한, 맴버 변수, 로컬 변수에 할당된 숫자형 리터럴 데이터는 Constant Pool에 저장되어 변수가 참조하는 것이 아니라 직접 변수에 값이 할당된다.

---

#### 🧑🏻‍💻 느낀점
javap라는 디어샘블링을 통해 "내가 사용하고 있는 Java는 어떤 특징을 가지고 있는지", "쉽게 접근할 수 있는 정보를 한번쯤 옳은 정보인지에 대한 분석능력"을 기르기 위한 과정이었습니다. 
널브러져있는 기술을 갖다쓰는 것은 초짜, 코더여도 할 수 있는 일이지만, 그것을 분석하고 판단해서 유용한 것인지를 분별해내는 것이 개발자라고 생각합니다. 기술을 사용하는데 있어서 나름의 이유와 그 이유를 뒷받침해줄 수 있는 
근거를 제시할 수 있는 개발자가 되고 싶습니다. (물론 빠르게 구현하는 능력도 키우고 싶습니다 😁)
