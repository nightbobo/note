## 해더 Headers 
<br>

# 제목 '#1'    H1
## 제목 '#2'    H2
### 제목 '#3'     H3
#### 제목 '#4'    H4
##### 제목 '#5'     H5
###### 제목 '#6'    H6
<br>

---
## 강조<Emphasis>

### Tag 를 이용한 방법 
강조는 <strong>\<strong></strong> , 취소선은 <del>\<del></del> , 밑줄 <u>\<u></u> 태그로 변환됩니다. 

### 특수기호 
이텔릭체는 *별표(asterisks)* 혹은 _언더바(underscore)_를 사용하세요.<br>
두껍게는 **별표(asterisks)** 혹은 __언더바(underscore)__를 사용하세요.<br>
**_이텔릭체_와 두껍게**를 같이 사용할 수 있습니다.<br>
취소선은 ~~물결표시(tilde)~~를 사용하세요.<br>
<u>밑줄</u>은 `<u></u>`를 사용하세요.

---

## BlockQuote
이메일에서 사용하는 '>' 블럭인용문자를 이용한다. 
> This is a first Blockquite. <br>
> > This is a second blockqute. 
> > > This is a third blockqute. 
<br>
<br>

## 목록 
1. 첫번째
2. 두번째 
3. 세번째 

> 1. 첫번째 
> 2. 두번째 
> 3. 세번째 

<br>
순서없는 목록 (글머리 기호 : * , + , - 지원) 

* 1단계
  - 2단계
    + 3단계
      + 4단계


1. 순서가 필요한 목록
1. 순서가 필요한 목록
  - 순서가 필요하지 않은 목록(서브) 
  - 순서가 필요하지 않은 목록(서브) 
1. 순서가 필요한 목록
  1. 순서가 필요한 목록(서브)
  1. 순서가 필요한 목록(서브)
1. 순서가 필요한 목록

- 순서가 필요하지 않은 목록에 사용 가능한 기호
  - 대쉬(hyphen)
  * 별표(asterisks)
  + 더하기(plus sign)

<br>

------------

## 코드 
### First Method
4개의 공백 또는 하나의 탭으로 들여쓰기를 만나면 변환되기 시작하여 들여쓰지 않은 행을 만날때까지 변환이 계속된다.

    public class BootSpringBootApplication {
        public static void main(String[] args) {
            System.out.println("Hello, Honeymon");
        }
    }

실제로 적용해보면, 

### Second Method
tag 조합으로 생성 가능 단 아래처럼 보기가 좋지 않아 사용하지 않을듯 하다. 
<pre>
<code>
    public class BootSpringBootApplication {
        public static void main(String[] args) {
            System.out.println("Hello, Honeymon");
        }
    }
</code>
</pre>

### Third Method 
\``` 문자를 이용하여  문법강조(Syntax highlighting) 적용가능
```java 
public class BootSpringBootApplication {
  public static void main(String[] args) {
    System.out.println("Hello, Honeymon");
  }
}
```
---

## 링크 
### 사용문법 : [Title][link]
[Google](https://google.com, "google link")

---

## 줄바꿈 
<br> 쓰는게 최고 
* 줄 바꿈을 하기 위해서는 문장 마지막에서 3칸이상을 띄어쓰기해야 한다. 
이렇게

* 줄 바꿈을 하기 위해서는 문장 마지막에서 3칸이상을 띄어쓰기해야 한다.<br> 
  띄어쓰기이렇게

---

## 표(Table) 
| 값 | 의미 | 기본값 |
|---|:---:|---:|
| `static` | 유형(기준) 없음 / 배치 불가능 | `static` |
| `relative` | 요소 자신을 기준으로 배치 |  |
| `absolute` | 위치 상 부모(조상)요소를 기준으로 배치 |  |
| `fixed` | 브라우저 창을 기준으로 배치 |  |

값 | 의미 | 기본값
---|:---:|---:
`static` | 유형(기준) 없음 / 배치 불가능 | `static`
`relative` | 요소 **자신**을 기준으로 배치 |
`absolute` | 위치 상 **_부모_(조상)요소**를 기준으로 배치 |
`fixed` | **브라우저 창**을 기준으로 배치 |
      



