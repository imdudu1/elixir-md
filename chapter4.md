# 엘릭서 기초

엘릭서에 기본 내장된 자료형은 다음과 같다.

- 값 타입
  - 정수
  - 실수
  - 아톰
  - 범위
  - 정규식
- 시스템 타입
  - 프로세스 ID, 포트
  - 레퍼런스
- 컬렉션 타입
  - 튜플
  - 리스트
  - 맵
  - 바이너리
## 값 타입

### 정수

기타 다른 언어와 마찬가지로 엘릭서도 다양한 정수 리터럴이 존재한다. 1234, 0xcafe, 0o765, 0b1010 등으로 쓸 수 있다. 엘릭서의 정수는 값의 크기에 제한이 없으며 표현하려는 값이 커질수록 언어 내부 구조 역시 그에 맞추어 확장된다.

### 실수

엘릭서의 실수는 IEEE 754 배정밀도 형식을 따르며 16자리 내외의 가수부와 10^308의 숫자를 표현할 수 있는 지수부를 가진다.

### 아톰

아톰은 어떤 이름을 나타내는 상수로, 앞에 콜론(:)을 쓰고 유효한 문자나 엘릭서 연산자를 붙이면 아톰이 된다. 아톰을 선언할 수 있는 문자로는 UTF-8, 숫자, 언더스코어, 골뱅이표(@)등이 있다. 독특한 점은 큰따옴표 사이에 임의의 문자를 넣거나, 느낌표나 물음표로 끝낼 수 있다.

### 범위

연속된 정수를 표현하는 범위는 `start..end`와 같이 쓸 수 있다. 시작 값을 나타내는 start와 end는 정수여야 한다.

### 정규식

정규식 리터럴은 `~r{정규식}` 또는 `~r{정규식}옵션` 문법으로 쓸 수 있다. 정통적인 방식인(/(슬래시 기호)가 포함된) 경우 이를 이스케이프하기 위해서 불편한 과정이 필요한 반면 엘릭서는 펄 5 호환 문법인 PCRE 방식의 정규식을 지원한다.

## 시스템 타입

시스템 타입은 얼램 VM에서 사용하는 자원들을 표현한다.

### 프로세스 ID, 포트

프로세스(이하 PID)는 로컬 또는 원격지에 있는 프로세스를 가리키며, 포트는 프로그램이 데이터를 읽거나 쓸 자원을 가리킨다.

### 레퍼런스

`make_ref` 함수를 호출해 전역적으로 고유한 레퍼런스를 얻을 수 있다. Javascript의 Symbol과 유사하며 다른 레퍼런스는 값이 동일할 수 없다. 단, 이 책에서 레퍼런스를 자세히 다루지 않는다.

## 컬렉션 타입

### 튜플

튜플은 여러 값을 순서 있게 모든 것이다. 엘릭서의 불변성 특성에 맞게 한번 생성된 튜플은 수정할 수 없다. 엘릭서는 일반적으로 함수 실행에 성공했을 때 첫 번째 값이 :ok인 튜플을 반환한다. 이를 통해 매턴 매칭과 함께 사용하면 우아한 코드를 작성할 수 있다.

### 리스트

앞서 엘릭서에서 리스트 리터럴을 접했을 때, 다른 언어의 배열과 유사하다고 생각될 수 있다. 사실 엘릭서의 튜플이 일반적인 배열에 더 가까운 형태고, 엘릭서의 리스트는 조금 다르게 생각되어야 한다.

엘릭서의 리스트는 링크드 리스트로, 이후 `7장 '리스트와 재귀'`에서 다루게 되겠지만, 재귀적으로 리스트를 정의하는 방식은 엘릭서 프로그래밍의 핵심이다.

```elixir
iex(1)> [1,2,3] ++ [4,5,6]
[1, 2, 3, 4, 5, 6]
iex(2)> [1,2,3,4] -- [2,4]
[1, 3]
iex(3)> 1 in [1,2,3,4]
true
iex(4)> "wombat" in [1,2,3,4]
false
```

#### 키워드 리스트

때때로 키-값 쌍으로 이뤄진 값의 리스트가 필요한 경우가 있다. 엘릭서는 이를 위한 단축 문법으로 다음과 같이 사용할 수 있다.

```elixir
iex(1)> [name: "Dave", city: "Dallas", likes: "Programming"]
[name: "Dave", city: "Dallas", likes: "Programming"]
```

함수 호출 시 마지막 인자로 넣게 된다면, 리스트 문법의 대괄호를 생략할 수 있다.

```elixir
iex(2)> DB.save(record, user_transaction: true, loggin: "HIGH")
```

### 맵

위 키워드 리스트를 키-값 쌍의 리스트라면 맵은 우리가 흔히 사용하는 자료구조와 동일하게 키-값으로 이뤄진 컬렉션이다.

```elixir
iex(2)> %{"name" => "Dave"}
%{"name" => "Dave"}
```

엘릭서에서 맵의 키는 다양한 타입을 가질 수 있다. 문자열, 튜플, 아톰 등 다양하게 사용할 수 있다.

```elixir
iex(3)> %{"one" => 1, :two => 2, {1, 1, 1} => 3}
%{:two => 2, {1, 1, 1} => 3, "one" => 1}
```

접근은 대괄호 안에 키를 넣어 접근할 수 있다.

```elixir
iex(4)> states = %{"AL" => "Alabama"}
%{"AL" => "Alabama"}
iex(5)> states["AL"]
"Alabama"
```

만약 접근하려는 키가 없다면 `KeyError` 예외가 발생한다.

### 바이너리

개발을 하게 되면 연속된 비트를 다룰 일이 있다. 특히 파일 헤더에 파일을 식별할 수 있는 Signature가 담겨 있는 경우가 그 예이다. 엘릭서는 이런 작업을 위해 바이너리 자료형을 지원하며 `<<`와 `>>`를 감싸서 표현한다.

```elixir
iex(6)> bin = <<1, 2>>
<<1, 2>>
iex(7)> byte_size(bin)
2
iex(8)> bin = <<3 :: size(2), 5 :: size(4), 1 :: size(3)>>
<<212, 1::size(1)>>
```

### 날짜와 시간

엘릭서 1.3에서 캘린더 모듈과 날짜/시간 관련 타입이 추가되었지만, 저장하는 것에 불과했지만 엘릭서 1.5부터 여러 기능이 추가되고 있다.

엘릭서 Calendar 모듈은 날짜 데이터를 조작하는 데 사용하는 규칙을 나타낸다.

```elixir
iex(1)> d1 = Date.new(2023, 1, 8)
{:ok, ~D[2023-01-08]}
iex(2)> d2 = ~D[2023-01-08]
~D[2023-01-08]
```

`~D[...]`와 `~T[...]`는 시길이라는 문법으로 리터럴로 엘릭서 자료구조를 생성하는 데 사용된다.

Time 타입은 시, 분, 초, 소수점 자리의 초 정보를 담는데, 여기서 초 정보는 마이크로초 단위로 센 값과 유효 자릿수로 구성된 튜플을 의미한다. 따라서 다음과 같이 소수점 자릿수가 다른 경우 다른 값으로 판단한다.

```elixir
iex(5)> t1 = ~T[12:35:35.00]
~T[12:35:35.00]
iex(6)> t2 = ~T[12:35:35.0]
~T[12:35:35.0]
iex(7)> t1 == t2
false
iex(8)> t3 = ~T[12:35:35.00]
~T[12:35:35.00]
iex(9)> t1 == t3
true
```

기본적인 데이터 타입도 강력하지만, 만약 프로그램에서 시간을 다루게 된다면 라우 탄스코프가 만든 Calendar 등 서드파티 라이브러리의 지원을 받는 것이 좋다.

## 스코프

스코프는 다른 언어의 개념과 같이 변수의 생명 주기를 나타낸다. 엘릭서는 여러 표현식을 하나로 묶을 수 있는 do 블록로 스코프 범위를 정할 수 있다.

```
line_no = 50

if (line_no == 50) do
    IO.puts "new-page\f"
    line_no = 0
end

IO.puts line_no
```

여기서 `line_no`는 동일한 변수명을 가지지만, 두 변수가 선언된 스코프 영역은 다르기 때문에 line_no는 서로 다른 변수로 생각해야 한다. 따라서 최종 출력은 `50`이 출력된다.

### with 표현식

with 표현식은 do와 비슷하게 로컬 스코프를 정의를 하게 된다. 또 다른 용도는 패턴 매칭이 실패했을 때 대응할 수 있는 역할도 제공한다.

```
content = "Now is the time"

lp  =  with {:ok, file}   = File.open("/etc/passwd"),
            content       = IO.read(file, :all),  # 위와 같은 변수명 사용
            :ok           = File.close(file),
            [_, uid, gid] = Regex.run(~r/^_lp:.*?:(\d+):(\d+)/m, content)
       do
            "Group: #{gid}, User: #{uid}"
       end

IO.puts lp             #=> Group: 26, User: 26
IO.puts content        #=> Now is the time
```

위 변수명이 동일하지만, with로 스코프가 분리돼 서로 다른 메모리 영역을 가지게 된다. 또 중요하게 봐야할 점은 6번째 줄로 정규식 결과를 패턴 매칭하는 부분이다.

하지만 `=`는 패턴 매칭에 실패한 경국 `MatchError` 예외가 발생하기 때문이다. 이런 경우 `<-` 연산자를 이용해 실패한 경우 좀 더 우아한 방식으로 처리할 수 있다.

```
iex(1)> with [a | _] <- [1, 2, 3], do: a
1
iex(2)> with [a | _] <- nil, do: a      
nil
iex(3)> with [a | _] = nil, do: a 
** (MatchError) no match of right hand side value: nil
    (stdlib 4.2) erl_eval.erl:496: :erl_eval.expr/6
    iex:3: (file)
```

with는 함수나 매크로를 호출하는 것과 같은 취급을 받는다. 따라서 다음과 같은 사용할 수 없다.

```
mean = with
          count = Enum.count(values),
          sum = Enum.count(values)
       do
          sum / count
       end
```

만약 with 다음 바로 개행이 필요한 경우 괄호를 사용해 내부 표현식을 묶을 필요가 있다.