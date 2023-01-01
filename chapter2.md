# 패턴 매칭

- 엘릭서의 등호 연산자는 다른 언어에서 활용되는 대입 연산자와 다름

  - 엘릭서의 = 기호는 `매치 연산자`로 등호 왼쪽을 오른쪽과 같게 만들 방법이 있다면 성공된다.
    - 이런 과정을 `패턴 매칭`이라 한다.

```elixir
iex> a = 1
1
iex> 1 = a
1
iex> 2 = a
** (MatchError) no match of right hand side value: 1

iex(1)> list = [1,2,3]
[1, 2, 3]
iex(2)> [a,b,c] = list
[1, 2, 3]
iex(3)> a
1
iex(4)> b
2
iex(5)> c
3

iex(1)> list = [1,2,3]
[1, 2, 3]
iex(2)> [a,2,b] = list
[1, 2, 3]
iex(3)> a
1
iex(4)> b
3
iex(5)> [a,3,b] = list
** (MatchError) no match of right hand side value: [1, 2, 3]
    (stdlib 4.2) erl_eval.erl:496: :erl_eval.expr/6
    iex:5: (file)
```

- 언더스코어는 패턴 매칭에서 와일드 카드와 같은 역할을 한다.

```elixir
iex(8)> [1, _, _] = [1, 2, 3]
[1, 2, 3]
iex(9)> [1, _, _] = [1, "b", "c"]
[1, "b", "c"]
```

- 변수는 매칭당 한 번씩만 바인딩된다.

```elixir
iex(8)> [a, a, 3] = [1, 2, 3]
** (MatchError) no match of right hand side value: [1, 2, 3]
    (stdlib 4.2) erl_eval.erl:496: :erl_eval.expr/6
    iex:8: (file)
```

- 변수의 기존 값과 매칭되도록 강제하기 위해선 핀 연산자(`^`)를 사용하면 된다.

```elixir
iex(5)> a = 1
1
iex(6)> a = 2
2
iex(7)> ^a = 1
** (MatchError) no match of right hand side value: 1
    (stdlib 4.2) erl_eval.erl:496: :erl_eval.expr/6
    iex:7: (file)
```

이처럼 엘릭서의 등호는 다른 시각으로 봐야한다.
