# 익명 함수

엘릭서는 함수형 언어답게 함수도 기본 타입이다. 함수는 값으로 다뤄질 수 있고, 이번 챕터에서 다루는 익명 함수처럼 이름이 없이 간단히 함수를 선언해 사용할 수 있다.

```elixir
fn
  parameter-list -> body
  parameter-list -> body ...
end
```

`fn...end` 를 문자열 리터럴 감싸는 따옴표이 함수를 감싸는 키워드로 최종적으로 함수를 반환하는 문법이다.

```elixir
iex(1)> sum = fn (a, b) -> a + b end
#Function<41.3316493/2 in :erl_eval.expr/6>
iex(2)> sum.(1, 2)
3
iex(3)> greet = fn -> IO.puts "Hello" end
#Function<43.3316493/0 in :erl_eval.expr/6>
iex(4)> greet.()
Hello
:ok
```

여기서 잠깐 생각하면, 우리가 일반적으로 생각하는 호출 형태로 sum의 `a, b`에 각각 `1, 2`가 할당됐다고 생각될 수 있다. 하지만 함수형 언어인 엘릭서에서 `할당문`은 없으며 대신 값을 패턴에 매칭하려 한다.

따라서 a와 b에 1과 2가 바인딩됐고, 다음 패턴 매칭이 일어난다고 할 수 있다.

## 함수는 하나, 본문은 여러 개

함수형 언어의 패턴 매칭이란 특성 덕분에 하나의 함수에 본문을 여러 개 작성할 수 있다. 

```elixir
handle_open = fn
  {:ok, file} -> "First line: #{IO.read(file, :line)}"
  {_, error} -> "Error: #{:file.format_error(error)}"
end

IO.puts handle_open.(File.open("Rakefile"))    # 존재하는 파일을 연다.
IO.puts handle_open.(File.open("nonexistent")) # 존재하지 않는 파일을 연다.
```

`File.open` 결과가 `:ok`와 매칭되면 첫번째 본문이 수행되고, 그게 아니면 두번째 본문이 수행되면서 error에 바인딩된 값을 출력하게 된다. 또 하나 중요하게 봐야할 부분은 3번째 라인에서 `:file`은 얼랭의 파일 처리 모듈로 엘릭서는 성숙한 얼랭 환경도 함께 가져갈 수 있다.

## 함수를 반환하는 함수

```elixir
iex(5)> fun1 = fn -> fn -> "Hello" end end
#Function<43.3316493/0 in :erl_eval.expr/6>
iex(6)> fun1.().()
"Hello"
```

함수형 언어에서 함수는 단순히 호출할 수 있는 코드가 아닌 값처럼 인자로 반환값 등등으로 활용된다. 따라서 함수형 언어가 추구하는 합성이나, 지연 평가 등 다양한 형태로 다룰 수 있기 때문인데, 당연히 엘릭서도 이를 지원한다. 위 코드는 함수를 반환하는 함수에 대한 간단한 코드로, fun1을 처음 호출하면 반환값으로 함수를 받고, 그 함수를 다시 호출하면 최종적으로 "Hello"를 볼 수 있다.

```elixir
iex(7)> fun2 = fn name -> fn -> "Hello #{name}" end end
#Function<42.3316493/1 in :erl_eval.expr/6>
iex(8)> fun2.("yohan").()
"Hello yohan"
```

또 다른 특징은 `클로저`라고 불리는 성질로, 함수 내부 스코프에 정의된 변수(매개변수, 함수 내 지역변수) 등은 함수가 종료되면 접근할 수 있는 방법이 없지만, 위 코드처럼 함수가 반환한 함수는 상위 함수의 스코프에 접근할 수 있으며, 상위 함수가 종료되어도 값을 가질 수 있다.

## 함수 파라미터 고정하기

핀 연산자(^)는 패턴 안에 있는 변수에 변수의 현재 값을 사용하도록 해준다.

```elixir
defmodule Greeter do
  def for(name, greeting) do
    fn
      (^name) -> "#{greeting} #{name}"
      (_)     -> "I don't know you"
    end
  end
end

mr_valim = Greeter.for("José", "Oi!")

IO.puts mr_valim.("José")    # => Oi! José
IO.puts mr_valim.("Dave")    # => I don't know you
```

만약 핀 연산자를 사용하지 않고, 패턴 매칭을 하게 되면 name이 호출되는 name과 매칭돼 항상 환영 메시지를 출력하게 된다.

## & 표기법

엘릭서에서는 함수를 만드는 전략이 하나 더 존재하는데, `&` 표기법을 이용하는 방법이다.

```elixir
iex(1)> add_one = &(&1 + 1)
#Function<42.3316493/1 in :erl_eval.expr/6>
iex(2)> add_one.(3)
4
iex(3)> add_one_2 = fn x -> x + 1 end
#Function<42.3316493/1 in :erl_eval.expr/6>
iex(4)> add_one_2.(3)
4
```

& 연산자 뒤에 나오는 표현식을 함수로 변환하며, 이때 괄호 안의 `&1`은 첫번째 인자를 의미한다.

```elixir
iex(6)> divrem = &{ div(&1, &2), rem(&1, &2) }
#Function<41.3316493/2 in :erl_eval.expr/6>
iex(7)> divrem.(1, 2)
{0, 1}
iex(8)> s = &"bacon and #{&1}"
#Function<42.3316493/1 in :erl_eval.expr/6>
iex(9)> s.("custard")
"bacon and custard"
```

이렇게 함수를 정의하는 형태로 사용될 수 있지만, 기존 함수를 다른 이름으로 변경하여 캡처할 수 있는 형태도 제공한다.

```elixir
iex(1)> l = &length/1
&:erlang.length/1
iex(2)> l.([1,2,3,4])
4
```



