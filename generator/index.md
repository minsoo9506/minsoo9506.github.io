# [Python] Generator


Python generator에 대해 알아보자.

<!--more-->
## 개념
일단 [Python docs](https://docs.python.org/3/glossary.html#term-generator)에 있는 개념을 정리해보자.
### generator
- A function which returns a generator iterator
  - 그렇다면 generator iterator 란?
    - An object created by a generator function
    - `next()`로 함수가 실행되다가 `yield`를 만나면 임시적으로 processing을 멈추고 해당 값을 반환한다. 그 상태로 있다가 (함수에 사용된 local변수 등이 사라지지 않고 유지) generator iterator가 다시 시작(`next()` 호출)하면 다음 단계로 넘어간다. 모든 object가 소진되면 끝난다. 이는 코드를 통해 이해해보자.
- It looks like a normal function except that it contains   `yield` expressions for producing a series of values usable in a for-loop or that can be retrieved one at a time with the `next()` function
- 쉽게 생각하면 `yield`로 iterator를 만든다고 이해할 수 있다.

```python
def gen():
    x = [1,2,3]
    for i in x:
        yield i

g = gen()

print(type(g))
# <class 'generator'>

print(g.__dir__())
# ['__repr__', '__getattribute__', '__iter__', '__next__', '__del__', 'send', 'throw', 'close', 'gi_frame', 'gi_running', 'gi_code', '__name__', '__qualname__', 'gi_yieldfrom', '__doc__', '__hash__', '__str__', '__setattr__', '__delattr__', '__lt__', '__le__', '__eq__', '__ne__', '__gt__', '__ge__', '__init__', '__new__', '__reduce_ex__', '__reduce__', '__subclasshook__', '__init_subclass__', '__format__', '__sizeof__', '__dir__', '__class__']
# __iter__, __next__ 함수 존재

print(next(g))
# 1
print(next(g))
# 2
```

```python
# 이런식으로 쉽게 Iterator를 만들 수 있다
def number_generator(stop):
    n = 0              
    while n < stop:    
        yield n        
        n += 1
```

### `yield from`
- `yield from`은 파이썬 3.3 이상부터 사용 가능
- `yield from` 뒤에는 iterable, iterator, generator가 올 수 있다

```python
# 위에서 만든 것과 동일한 결과
def number_generator():
    x = [1, 2, 3]
    yield from x
```

```python
def number_generator(stop):
    n = 0
    while n < stop:
        yield n
        n += 1
 
def three_generator():
    yield from number_generator(3)
 
for i in three_generator():
    print(i)
```

### generator 표현
- `[]`가 아니라 `()`를 통해 만들 수 있다.

```python
# 리스트
[i for i in range(50) if i % 2 == 0]
# generator
(i for i in range(50) if i % 2 == 0)

# 메모리 절약
import sys
list_size = sys.getsizeof( [i for i in range(50) if i % 2 == 0])
get_size = sys.getsizeof((i for i in range(50) if i % 2 == 0))

print(list_size) # 312
print(get_size)  # 112
```
- 데이터 사이즈가 큰 경우, generator를 이용하면 이점이 있을 것 같다.

## Reference
- https://docs.python.org/3/glossary.html#term-generator
- https://dojang.io/mod/page/view.php?id=2412
