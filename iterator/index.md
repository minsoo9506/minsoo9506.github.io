# [Python] Iterator


Python iterator애 대해 알아보자.

<!--more-->
## 개념
일단 [Python docs](https://docs.python.org/3/glossary.html#term-iterable)에 있는 개념을 정리해보자.

### iterable
- 정의
  - An object capable of returning its members one at a time
- 종류
  - include all sequence types (such as list, str, and tuple)
  - some non-sequence types like dict and file
  - objects of any classes you define with an `__iter__()` or `__getitem__()` method that implements sequence semantics
- 작동원리
  - Iterables can be used in a for loop and in many other places where a sequence is needed (`zip()`, `map()`, …)
  - When an iterable object is passed as an argument to the built-in function `iter()`, it returns an iterator for the object
    - 예들 들어, list는 iterable이지만 iterator는 아니다. 이를 iterator로 만들고 싶으면 `iter(해당list)`처럼 `iter()`로 감싸면 된다.
  - it is usually not necessary to call `iter()` or deal with iterator objects yourself
  - The for statement does that automatically for you, creating a temporary unnamed variable to hold the iterator for the duration of the loop
    - 위의 예시에서 우리는 iterator로 만드는 과정이 필요했다. 하지만 for문에서는 이를 자동으로 해준다는 의미이다.

### iterator
- 정의
  - An object representing a stream of data
- 작동원리
  - Repeated calls to the iterator’s `__next__()` method (or passing it to the built-in function `next()`) return successive items in the stream
    - list, tuple, set, dict 등은 iterable이지만 iterator는 아니다 (`next()` 함수 씌우면 에러발생)
  - When no more data are available a `StopIteration` exception is raised instead
  - Iterators are required to have an `__iter__()` method that returns the iterator object itself so every iterator is also iterable

## 코드
```python
x = [1,2,3] # iterable
x_iter = iter(x) # iterator로 만들기
print(type(x_iter)) 
# <class 'list_iterator'>
print(next(x_iter))
# 1
```

```python
# iterator
# __next__ 가 없으면 iterable
class Counter:
    def __init__(self, stop):
        self.current = 0
        self.stop = stop

    def __iter__(self):
        return self # 스스로가 iterator인 상태

    def __next__(self):
        if self.current < self.stop:
            r = self.current
            self.current += 1
            return r
        else:
            raise StopIteration
        
for i in Counter(3):
    print(i)
# 0
# 1
# 2
c = Counter(3)
print(next(c))
# 0
```

```python
# iterable
# __getitem__ 만 구현되어 있으면 for문은 가능
class Counter:
    def __init__(self, stop):
        self.current = 0
        self.stop = stop

    def __getitem__(self, index):
        if index < self.stop:
            return index
        else:
            raise IndexError

c = Counter(3)
for i in c:
    print(i)
# 0
# 1
# 2

print(next(c))
# TypeError: 'Counter' object is not an iterator

c_iter  = iter(c)
print(next(c_iter))
# 0
print(next(c_iter))
# 1
```

## Reference
- https://dojang.io/mod/page/view.php?id=2406
- https://wikidocs.net/16068
- https://docs.python.org/3/glossary.html#term-iterable
