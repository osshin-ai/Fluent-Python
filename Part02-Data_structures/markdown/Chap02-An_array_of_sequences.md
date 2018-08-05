
# Chap02 - 시퀀스 An array of sequences

파이썬에서 제공하는 다양한 시퀀스를 이해하면 코드를 새로 구현할 필요가 없으며, 시퀀스의 공통 인터페이스를 따라 기존 혹은 향후에 구현될 시퀀스 자료형을 적절히 지원하고 활용할 수 있게 API를 정의할 수 있다.



## 2.1 내장 시퀀스 개요

파이썬은 C로 구현된 다음과 같은 시퀀스들을 제공한다.

- ***컨테이너 시퀀스*** : 서로 다른 자료형의 항목들을 담을 수 있는 `list, tuple, collections.deque` 형태
- ***균일 시퀀스*** : 하나의 자료형만 담을 수 있는 `str, bytes, memoryview, array.array` 형태

컨테이너 시퀀스(container sequence)는 객체에 대한 참조를 담고 있으며 객체는 어떠한 자료형도 될 수 있다. 하지만, 균일 시컨스(flat sequence)는 객체에 대한 참조 대신 자신의 메모리 공간에 각 항목의 값을 직접 담는다. 따라서, 균일 시퀀스가 메모리를 더 적게 사용하지만, 문자, 바이트, 숫자 등 기본적인 자료형만 저장할 수 있다. 

시퀀스는 다음과 같이 가변성에 따라 분류할 수도 있다.

- ***가변 시퀀스*** : `list, bytearray, array.array, collections.deque, memoryview` 등
- ***불변 시퀀스*** : `tuple, str, bytes` 등

## 2.2 리스트 컴프리헨션과 제너레이터 표현식

리스트 컴프리헨션(listcomp, List Comprehension)이나 제너레이터 표현식(genexp, Generator Expression)을 사용하면 시퀀스를 간단히 생성할 수 있다. 

### 2.2.1 리스트 컴프리헨션과 가독성

아래의 [예제 2-1]과 [예제 2-2]를 보도록 하자. 


```python
# 예제 2-1 : 문자열에서 유니코드 코드 포인트 리스트 만들기 ver01 

symbols = '$¢£¥€¤'
codes = []
for symbol in symbols:
    codes.append(ord(symbol))
    
codes
```


    [36, 162, 163, 165, 8364, 164]




```python
# 예제 2-2 : 문자열에서 유니코드 코드 포인트 리스트 만들기 ver02

symbols = '$¢£¥€¤'
codes = [ord(symbol) for symbol in symbols]
codes
```


    [36, 162, 163, 165, 8364, 164]



[예제 2-1]은 `for` 루프를 이용해서 `codes` 리스트를 만들고, [예제 2-2]는 `listcomp`를 이용해서 `codes`리스트를 만든다. 파이썬에 익숙한 사람들은 리스트 컴프리헨션 방법이 가독성이 더 좋다는 것을 알 수 있다. (실제로 저도 그렇네요ㅎㅎ) <br />

하지만, 그렇다고 해서 리스트 컴프리헨션을 남용해서는 안된다. 리스트 컴프리헨션을 사용할 경우 구문이 두 줄이상 넘어가는 경우나 다중 `for`문을 사용하는 경우 [예제 2-1]처럼 코드를 분할해서 사용하는 것이 오히려 더 낫다. 

### 2.2.2 리스트 컴프리헨션과 `map()/filter()` 비교

위의 [예제 2-1]과 [예제 2-2]를 `map()`과 `filter()`를 사용해서 [예제 2-3]처럼 구현할 수 있다. 


```python
# [예제 2-3] - map/filter 로 만든 codes 리스트
symbols = '$¢£¥€¤'
codes = list(filter(lambda c: c > 1, map(ord, symbols)))
codes
```




    [36, 162, 163, 165, 8364, 164]



하지만, 가독성은 [예제 2-2]보다 떨어진다. 

### 2.2.3 데카르트 곱

이번에는 리스트 컴프리헨션을 이용해 데카르트 곱을 구현해 보자. 데카르트 곱(곱집합)에 대해서는 [wikipedia](https://ko.wikipedia.org/wiki/%EA%B3%B1%EC%A7%91%ED%95%A9)를 참고하면 된다. 아래의 그림은 데카르트 곱에 대한 예제이다.

![](./images/descartes.PNG)

다음 [예제 2-4] 와 같이 두 가지 색상과 세 가지 크기의 티셔츠에 대해 데카르트 곱을 통해 리스트를 만들게 되면 총 6가지 항목이 만들어 진다.


```python
# [예제 2-4] 리스트 컴프리헨션을 이용한 데카르트 곱
colors = ['black', 'white']
sizes = ['S', 'M', 'L']
tshirts = [(color, size) for color in colors 
                         for size in sizes]
tshirts
```


    [('black', 'S'),
     ('black', 'M'),
     ('black', 'L'),
     ('white', 'S'),
     ('white', 'M'),
     ('white', 'L')]



### 2.2.4 제너레이터 표현식

튜플(tuple), 배열(array) 등의 시퀀스를 초기화 하기위해서는 리스트 컴프리헨션을 사용할 수도 있지만, 리스트를 한번에 통째로 만들지 않고, 반복자 프로토콜(iterator protocol)을 이용하여 항목을 하나씩 생성하는 제너레이터(generator) 표현식을 사용하면 메모리를 더 적게 사용한다. 

제너레이터 표현식은 리스트 컴프리헨션과 동일한 구문을 사용하지만, 대괄호`[]`가 아닌 괄호`()`를 사용한다.

[예제 2-5]는 제너레이터 표현식을 이용해 튜플을 생성하는 코드이다.


```python
# [예제 2-5] - 제너레이터 표현식을 이용한 튜플, 배열 초기화
symbols = '$¢£¥€¤'
tuple(ord(symbol) for symbol in symbols)
```




    (36, 162, 163, 165, 8364, 164)




```python
import array

array.array('I', (ord(symbol) for symbol in symbols))
```




    array('I', [36, 162, 163, 165, 8364, 164])



아래의 [예제 2-6]은 데카르트 곱을 제너레이터 표현식을 사용해 [예제 2-4]를 구현한 코드이다. [예제 2-4]와 달리 [예제 2-6]은 생성된 리스트의 항목을 메모리에 할당하지 않는다. 제너레이터 표현식은 한번에 한 항목을 생성할 수 있도록 설정되어 있기 때문에 [예제 2-6]과 같이 `for` 루프에 데이터를 전달한다. 따라서, 항목이 많은 리스트를 생성할 때 생길 수 있는 메모리 부족 문제를 해결할 수 있다.


```python
# [예제 2-6] - 제너레이터 표현식을 이용한 데카르트 곱
colors = ['black', 'white']
sizes = ['S', 'M', 'L']
for tshirt in ('%s %s' % (c, s) for c in colors for s in sizes):
    print(tshirt)
```

    black S
    black M
    black L
    white S
    white M
    white L


## 2.3 튜플은 단순한 불변 리스트가 아니다

튜플은 단순히 *'불변 리스트'* 로 사용할 수 있지만, 필드명이 없는 *레코드* 로 사용할 수도 있다.

### 2.3.1 레코드로서의 튜플

튜플은 레코드로서 사용될 수 있다. 튜플의 각 항목은 레코드의 필드 하나를 의미하며 항목의 위치가 의미를 결정한다. 

튜플을 단순히 '불변 리스트'로 생각한다면 경우에 따라 항목의 크기와 순서가 중요할 수도 있고 그렇지 않을 수도 있다.  하지만, 튜플을 필드의 집합으로 사용하는 경우에는 항목 수가 고정되어 있고 항목의 순서가 중요하다.

[예제 2-7]은 튜플을 레코드로 사용하는 경우를 보여준다.


```python
# [예제 2-7] : 레코드로 사용된 튜플
lax_coordinates = (33.9425, -118.408056)  # LA 국제공항의 위도와 경도
city, year, pop, chg, area = ('Tokyo', 2003, 32450, 0.66, 8014)  # 도쿄에 대한 데이터(지명, 연도, 인구수, 인구 변화율, 면적)
traveler_ids = [('USA', '31195855'), ('BRA', 'CE342567'), ('ESP', 'XDA206856')]  # (국가코드, 여권 번호) 튜플 리스트

for passport in sorted(traveler_ids):  # 리스트를 반복할 때 passport 변수가 각 튜플에 바인딩된다.
    print('%s/%s' % passport)  # 퍼센트(%) 포맷 연산자는 튜플을 이해하고 각 항목을 하나의 필드로 처리
```

    BRA/CE342567
    ESP/XDA206856
    USA/31195855



```python
for country, _ in traveler_ids:  # 언패킹을 이용한 각 항목 가져오기 '_'는 dummy variable
    print(country)
```

    USA
    BRA
    ESP


### 2.3.2 튜플 언패킹(Unpacking)

[예제 2-7]에서 `city, year, pop, chg, area ` 변수에 `('Tokyo', 2003, 32450, 0.66, 8014)`를 각각 할당했다. 이러한 방법이 바로 *튜플 언패킹(tuple unpacking)* 이다. 언패킹은 반복 가능한 객체라면 어느 객체든 적용할 수 있다. 

튜플 언패킹은 **병렬 할당**(parallel assignment)을 할 때 주로 사용한다. 아래의 코드는 튜플을 변수에 병렬할당하는 예제이다. 



```python
lax_coordinates = (33.9425, -118.408056)
latitude, longitude = lax_coordinates  # 튜플 언패킹
print(latitude)
print(longitude)
```

    33.9425
    -118.408056




튜플 언패킹을 이용하면 임시 변수를 사용하지 않고도 두 변수의 값을 서로 교환할 수 있다.


```python
a, b = (1, 2)
print('a =', a, 'b =', b)

# 두 변수 교환하기
b, a = a, b
print('a =', a, 'b =', b)
```

    a = 1 b = 2
    a = 2 b = 1


또한, 다음과 같이 함수를 호출할 때 인수 앞에 `*` 을 붙여 튜플을 언패킹 할 수 있다.


```python
divmod(20, 8)
```




    (2, 4)




```python
t= (20, 8)
divmod(*t)
```




    (2, 4)




```python
quotient, remainder = divmod(*t)
quotient, remainder
```




    (2, 4)




```python
import os

path, filename = os.path.split('./fluent/python/chap02/sequences.py')
print(path)  # 경로
print(filename)  # 파일명
```

    ./fluent/python/chap02
    sequences.py


#### 초과 항목을 잡기위한 `*` 사용하기

파이썬 3에서는 `*`를 이용해 아래와 같이 병렬 할당에도 사용할 수 있다.  언패킹을 하고난 뒤 나머지 값들을  `*`를 사용하여 할당해 줄 수 있다. 이때 `*`를 적용한 변수는 리스트`[]` 형태로 반환된다.


```python
a, b, *rest = (1, 2, 3, 4, 5)
print(a, b, rest)

a, b, *rest = range(5)
print(a, b, rest)

a, b, *rest = range(3)
print(a, b, rest)

a, b, *rest = range(2)
print(a, b, rest)
```

    1 2 [3, 4, 5]
    0 1 [2, 3, 4]
    0 1 [2]
    0 1 []


병렬 할당의 경우 `*`는 단 하나의 변수에만 적용할 수 있다. 대신 `*` 위치는 어떠한 곳에도 상관이 없다. 


```python
*head, b, c = range(5)
print(start, b, c)

a, *body, c = range(5)
print(a, body, c)
```

    [0, 1, 2] 3 4
    0 [1, 2, 3] 4


### 2.3.3 내포된 튜플 언패킹 - Nested tuple unpacking

튜플은 `(a, b, (c, d))` 처럼 튜플 안에 튜플이 내포된(nested) 형태로 되어있을 수도 있다. 파이썬은 내포된 튜플의 경우도 변수 할당만 제대로 해주면 무리없이 언패킹이 가능하다. [예제 2-8]을 보도록 하자.


```python
# [예제 2-8]: longitude에 접근하기 위해 내포된 튜플 언패킹하기

metro_areas = [
    ('Tokyo', 'JP', 36.933, (35.689722, 139.691667)),
    ('Delhi NCR', 'IN', 21.935, (28.613889, 77.208889)), 
    ('Mexico City', 'MX', 20.142, (19.433333, -99.133333)), 
    ('New York-Newark', 'US', 20.104, (40.808611, -74.020386)), 
    ('Sao Paulo', 'BR', 19.649, (-23.547778, -46.635833)),
]

print('{:15} | {:^9} | {:^9}'.format('', 'lat.', 'long.'))
fmt = '{:15} | {:9.4f} | {:9.4f}'
for name, cc, pop, (latitude, longitude) in metro_areas:
    print(fmt.format(name, latitude, longitude))
```

                    |   lat.    |   long.  
    Tokyo           |   35.6897 |  139.6917
    Delhi NCR       |   28.6139 |   77.2089
    Mexico City     |   19.4333 |  -99.1333
    New York-Newark |   40.8086 |  -74.0204
    Sao Paulo       |  -23.5478 |  -46.6358


지금까지 살펴본 예를 통해 튜플은 아주 편리하게 사용할 수 있다는 것을 알 수 있다. 그러나 레코드로 사용하기에는 부족한 점이 있다. 때로는 필드에 이름을 붙여야할 경우도 있다. 이를 위해 `collections` 모듈의 `namedtuple()` 함수가 고안되었다.

### 2.3.4 Named tuples

`collections.namedtuple()` 함수는 필드명과 클래스명을 추가한 튜플의 서브클래스를 생성하는 팩토리 함수이다.  `collections.namedtuple(typename, field_names, verbose=False, rename=False)`을 입력값으로 받으며, *field_names* 를 통해 `namedtuple()`의 키 즉, 필드명(fieldname)을 정의할 수 있다.

[예제 2-9]는 `namedtuple()` 을 정의해 도시에 대한 정보를 담고 있는 객체를 만드는 방법을 보여주는 예시이다.


```python
# [예제 2-9]: namedtuple 정의하고 사용하기
from collections import namedtuple

City = namedtuple('City', 'name country population coordinates')
seoul = City('Seoul', 'KR', 10.204, (37.5668237, 126.9779504))
print(seoul)
print(seoul.population)
print(seoul.coordinates)
print(seoul[1])
```

    City(name='Seoul', country='KR', population=10.204, coordinates=(37.5668237, 126.9779504))
    10.204
    (37.5668237, 126.9779504)
    KR


`namedtuple()`은 튜플에서 상속받은 속성 외에 몇가지 속송들을 더 가지고 있다. 
- `_fields` : 클래스의 필드명을 담고 있는 튜플을 반환한다.
- `_make()` : 반복형 객체로 부터 namedtuple을 만든다. 
- `_asdict()` : namedtuple 에서 만들어진 `collections.OrderedDict` 객체를 반환한다. 

[예제 2-10]은 `_fields` 클래스 속성, `_make(iterable)` 클래스 메소드, `_asdict()` 객체 메소드를 보여주는 예제이다.


```python
# [예제 2-10] : namedtuple의 속성과 메소드

print(City._fields)

LatLong = namedtuple('LatLong', 'lat long')
delhi_data = ('Delhi NCR', 'IN', 21.935, LatLong(28.613889, 77.208889))
delhi = City._make(delhi_data)
print(delhi._asdict())

for key, value in delhi._asdict().items():
    print(key + ':', value)
```

    ('name', 'country', 'population', 'coordinates')
    OrderedDict([('name', 'Delhi NCR'), ('country', 'IN'), ('population', 21.935), ('coordinates', LatLong(lat=28.613889, long=77.208889))])
    name: Delhi NCR
    country: IN
    population: 21.935
    coordinates: LatLong(lat=28.613889, long=77.208889)


### 2.3.5 불변 리스트로서의 튜플

튜플을 불변 리스트로 사용할 때, 튜플과 리스트가 얼마나 비슷한지 알고 있으면 도움이 된다.

| 메소드                     | 리스트 | 튜플 | 설명                                                         |
| -------------------------- | ------ | ---- | ------------------------------------------------------------ |
| `s.__add__(s2)`            | O      | O    | `s + s2`: 리스트를 연결한다.                                 |
| `s.__iadd__(s2)`           | O      |      | `s += s2` : 리스트를 연결하고 `s`에 저장한다.                |
| `s.append(e)`              | O      |      | 제일 뒤에 원소를 하나 추가한다.                              |
| `s.clear()`                | O      |      | 모든 항목을 삭제한다.                                        |
| `s.__contains__(e)`        | O      | O    | `e in s`                                                     |
| `s.copy()`                 | O      |      | 리스트를 복사한다.                                           |
| `s.count(e)`               | O      | O    | `e`가 나타난 횟수를 계산한다.                                |
| `s.__delitem__(p)`         | O      |      | `p` 위치의 원소를 삭제한다.                                  |
| `s.extend(it)`             | O      |      | 반복형 `it` 안에 있는 원소를 추가한다.                       |
| `s.__getitem__(p)`         | O      | O    | `s[p]`: `p` 위치의 원소를 가져온다.                          |
| `s.__getnewargs__()`       |        | O    | `pickle` 을 이용해서 최적화된 직렬화를 지원한다.             |
| `s.index(e)`               | O      | O    | `s` 안에서 `e` 가 처음 나타나는 위치를 찾는다.               |
| `s.insert(p, e)`           | O      |      | `p` 위치에 있는 원소 앞에 `e` 원소를 삽입한다.               |
| `s.__iter__()`             | O      | O    | 반복자를 가져온다.                                           |
| `s.__len__()`              | O      | O    | `len(s)` : 항목 개수를 구한다.                               |
| `s.__mul__(n)`             | O      | O    | `s * n` : 문자열을 반복한다.                                 |
| `s.__imul__(n)`            | O      |      | `s *= n` : 문자열을 반복하여 `s` 에 저장한다.                |
| `s.__rmul__(n)`            | O      | O    | `n * s` : 역순 반복 추가 메소드                              |
| `s.pop([p])`               | O      |      | 마지막 항목이나 `p` 위치의 항목을 제거하고 반환한다.         |
| `s.remove(e)`              | O      |      | `e` 값을 가진 첫 번째 항목을 제거한다.                       |
| `s.reverse()`              | O      |      | 항목을 역순으로 정렬한 후 `s` 에 저장한다.                   |
| `s.__reversed__()`         | O      |      | 마지막에서 첫 번째 항목까지 반복하는 반복자를 반환한다.      |
| `s.__setitem__(p, e)`      | O      |      | `s[p] = e` : `e` 를 `p` 위치에 저장하고, 기존항목을 덮어쓴다. |
| `s.sort([key], [reverse])` | O      |      | 선택적인 `key` 와 `reverse`에 따라 항목을 정렬하고 `s` 에 저장한다. |

## 2.4 슬라이싱 Slicing

파이썬에서 `list, tuple, str` 등 모든 시퀀스형은 슬라이싱(slicing) 연산을 지원한다.


### 2.4.1 슬라이스와 범위 지정시에 마지막 항목이 포함되지 않는 이유

슬라이싱 연산에서 마지막 항목을 포함하지 않는 이유는 간단하다. 바로 `index`가 `0` 부터 시작하기 때문이다! 이러한 이유 덕분에 아래와 같은 장점이 있다.

- 세 개의 항목을 생성하는 `range(3)` 이나 `my_list[:3]` 처럼 중단점만 이용해서 슬라이스나 범위를 지정할 때 *길이를 계산하기 쉽다*.
- 시작점(start)과 중단점(end)을 모두 지정할 때도 *길이를 계산하기 쉽다*. $\rightarrow$ `len = end - start` 
- 아래의 예제 코드에서 보듯이 `x` 인덱스를 기준으로 *겹침 없이 시퀀스를 분할하기 쉽다*. 
  - 인덱스 `x` 이전 : `my_list[:x]`
  - 인덱스 `x` 이후 : `my_list[x:]`


```python
l = [10, 20, 30, 40, 50, 60]

print('l[:2] =', l[:2])  # 2번 인덱스 전까지 분할
print('l[2:] =', l[2:])  # 2번 인덱스 부터 나머지까지 분할

print('l[:3] =', l[:3])  # 3번 인덱스 전까지 분할
print('l[3:] =', l[3:])  # 3번 인덱스 부터 나머지까지 분할
```

    l[:2] = [10, 20]
    l[2:] = [30, 40, 50, 60]
    l[:3] = [10, 20, 30]
    l[3:] = [40, 50, 60]


### 2.4.2 슬라이스 객체

슬라이싱 `s[a:b:c]`에서 `c`는 보폭(stride) 만큼씩 항목을 건너뛰게 한다. `c < 0` (음수) 인 경우에는 거꾸로 거슬러 항목을 반환한다.


```python
s = 'bicycle'

print('s[::3] =', s[::3])
print('s[::-1] =', s[::-1])
print('s[::-2] =', s[::-2])
```

    s[::3] = bye
    s[::-1] = elcycib
    s[::-2] = eccb


`a:b:c` 표기법은 인덱스 연산을 수행하는 `[]` 안에서만 사용가능하며, `slice(a, b, c)` 객체를 생성한다. 이러한 작동 원리는 `seq[start:stop:step]` 은 `seq.__getitem__(slice(start, stop, step))` 을 호출함으로써 동작한다. 

`slicing()` 을 이용하면 일관된 데이터 형식에서 슬라이스를 하드코딩 하는 대신에 각 슬라이스에 이름을 붙여 가독성을 높일 수 있다.  아래의 [예제 2-11]은 텍스트 파일로 구성된 청구서를 슬라이싱하는 예제이다.


```python
# 예제 2-11 : 단순 텍스트 파일 청구서의 행 항목들

invoice = """
0.....6.................................40........52...55........
1909  Pimoroni PiBrella                     $17.50    3    $52.50
1489  6mm Tactile Switch x20                $4.95     2    $9.90
1510  Panavise Jr. - PV-201                 $28.00    1    $28.00
1601  PiTFT Mini Kit 320x240                $34.95    1    $34.95
"""

SKU = slice(0, 6)
DESCRIPTION = slice(6, 40)
UNIT_PRICE = slice(40, 52)
QUANTITY = slice(52, 55)
ITEM_TOTAL = slice(55, None)
line_items = invoice.split('\n')[2:]

for item in line_items:
    print(item[UNIT_PRICE], item[DESCRIPTION])
```

        $17.50   Pimoroni PiBrella                 
        $4.95    6mm Tactile Switch x20            
        $28.00   Panavise Jr. - PV-201             
        $34.95   PiTFT Mini Kit 320x240            


​    

### 2.4.3 다차원 슬라이싱과 생략 기호

`[]` 연산자는 콤마로 구분해서 여러 개의 인덱스나 슬라이스를 가질 수 있다. 대표적으로 `numpy` 모듈에서 $n$-차원의 데이터를 슬라이싱 할때 사용한다. `a[i, j]` 는 2차원의 `numpy.ndarray` 배열의 항목이나 `a[m:n, k:1]` 형태와 같이 2차원 데이터를 슬라이싱할 때 사용한다. 이는 `a[i, j]` 의 인덱스 `(i, j)`를 튜플로 받아 `a.__getitem__((i, j))`를 호출한다. 아래의 예제 코드는 `numpy` 모듈을 이용하여 행렬 $M=\begin{bmatrix} 1 & 4 & 7 \\ 2 & 5 & 8 \\ 3 & 6 & 9 \end{bmatrix}$ 에서 슬라이싱을 이용하여 $1$열 $[1, 2, 3]$ 을 슬라이싱한 예제이다.


```python
import numpy as np

M = np.array([[1, 4, 7], 
              [2, 5, 8], 
              [3, 6, 9]])

M_col = M[:, 0]
```


```python
M_col
```




    array([1, 2, 3])



### 2.4.4 슬라이스에 할당하기

슬라이스 표기법이나 `del` 을 이용하여 가변 시퀀스를 연결하거나, 잘라 내거나, 값을 변경할 수 있다. 아래의 예제를 보자.


```python
l = list(range(10))
l
```




    [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]




```python
l[2:5] = [20, 30]  # 2, 3 인덱스를 20, 30으로 바꾸고 4번째 인덱스의 값은 제거
l
```




    [0, 1, 20, 30, 5, 6, 7, 8, 9]




```python
del l[5:7]  # 5, 6 인덱스 제거
l
```




    [0, 1, 20, 30, 5, 8, 9]




```python
l[3::2] = [11, 22]
l
```




    [0, 1, 20, 11, 5, 22, 9]



할당문의 대상이 슬라이스인 경우, 항목 하나만 할당하는 경우에도 할당문 오른쪽에는 반복 가능한 객체가 와야한다.


```python
l[2:5] = 100
```


    ---------------------------------------------------------------------------
    
    TypeError                                 Traceback (most recent call last)
    
    <ipython-input-5-da8b10461280> in <module>()
    ----> 1 l[2:5] = 100


    TypeError: can only assign an iterable



```python
l[2:5] = [100]
l
```




    [0, 1, 100, 22, 9]



 

## 2.5 시퀀스에 덧셈과 곱셈 연산자 사용하기

하나의 시퀀스를 정수를 곱해 여러 번 연결할 수 있다.


```python
l = [1, 2, 3]
print(l*3)
```

    [1, 2, 3, 1, 2, 3, 1, 2, 3]




### 2.5.1 리스트의 리스트 만들기

리스트 내부에 리스트를 생성할 수도 있다. 아래의 예제는 리스트 컴프리헨션을 이용해 리스트안에 리스트를 초기화하는 예제이다.


```python
board = [['_']*3 for i in range(3)]
print(board)
```

    [['_', '_', '_'], ['_', '_', '_'], ['_', '_', '_']]



```python
board[1][2] = 'X'
print(board)
```

    [['_', '_', '_'], ['_', '_', 'X'], ['_', '_', '_']]




위의 방법을 리스트에 정수를 곱해 다음과 같이 나타낼 수 있다. 하지만, 특정 인덱스에 값을 넣어줄 경우 예상치 못한 결과가 나타난다.


```python
weird_board = [['_']*3]*3
weird_board
```




    [['_', '_', '_'], ['_', '_', '_'], ['_', '_', '_']]




```python
weird_board[1][2] = 'X'
weird_board
```




    [['_', '_', 'X'], ['_', '_', 'X'], ['_', '_', 'X']]



 

그 이유는 리스트 안의 리스트가 동일한 참조를 가지기 때문이다. 위의 리스트 컴프리헨션과 리스트에 정수를 곱하는 방법을 풀어쓰면 다음과 같다.


```python
# 리스트 컴프리헨션을 풀어서 쓴 경우
board = []
for i in range(3):
    row = ['_'] * 3
    board.append(row)

print(board)

board[1][2] = 'X'
print(board)
```

    [['_', '_', '_'], ['_', '_', '_'], ['_', '_', '_']]
    [['_', '_', '_'], ['_', '_', 'X'], ['_', '_', '_']]



```python
# 리스트 * 정수를 풀어서 쓴 경우
row = ['_'] * 3
weird_board = []
for i in range(3):
    weird_board.append(row)
    
print(weird_board)

weird_board[1][2] = 'X'
weird_board
```

    [['_', '_', '_'], ['_', '_', '_'], ['_', '_', '_']]





    [['_', '_', 'X'], ['_', '_', 'X'], ['_', '_', 'X']]



 

## 2.6 시퀀스의 복합 할당

`+=` 연산자가 동작하도록 만드는 [매직 메소드](http://excelsior-cjh.tistory.com/127)는 `__iadd__()` 다. 만약, `__iadd__()` 메소드가 없을 경우 `__add__()` 메소드를 대신 호출한다. 마찬가지로 `*=`에 대해서도 `__imul__()`을 호출한다. 여기서 `i`는 **in-place**의 **i**를 의미하며, 해당 변수를 직접 변경한다.

`list, bytearray, array.array` 등과 같이 가변 시퀀스인 경우 `+=`를 사용하면 `__iadd__()`가 호출되지만, `tuple`과 같은 불변 시퀀스인 경우에는 `__add__()`메소드가 대신 호출된다. 


```python
print('list는 __iadd__()를 가지고 있는가? :', 
      '__iadd__' in dir(list))

print('tuple은 __iadd__()를 가지고 있는가? :', 
      '__iadd__' in dir(tuple))
```

    list는 __iadd__()를 가지고 있는가? : True
    tuple은 __iadd__()를 가지고 있는가? : False




아래의 예제는 리스트와 튜플에 `*=`를 적용한 예제이다. 아래의 코드에서 [`id()`](https://docs.python.org/3/library/functions.html#id)는 파이썬의 내장함수이며, 반환되는 숫자는 해당 객체의 메모리 주소를 의미한다. 

예제에서도 확인할 수 있듯이 리스트는 `__iadd__()`를 가지고 있으므로, 해당 변수를 직접 변경하여 메모리 주소가 그대로지만, 튜플은 `__add__()`를 호출하므로 새로운 튜플 객체가 만들어 진다.


```python
# list에 *= 적용
l = [1, 2, 3]
print('*=을 적용하기 전 l의 id :', id(l))

l *= 2
print('*= 적용 결과 :', l)
print('*=을 적용한 후 l의 id :', id(l))

# tuple에 *= 적용
t = (1, 2, 3)
print('*=을 적용하기 전 t의 id :', id(t))

t *= 2
print('*= 적용 결과 :', t)
print('*=을 적용한 후 l의 id :', id(t))
```

    *=을 적용하기 전 l의 id : 4541151880
    *= 적용 결과 : [1, 2, 3, 1, 2, 3]
    *=을 적용한 후 l의 id : 4541151880
    *=을 적용하기 전 t의 id : 4541047288
    *= 적용 결과 : (1, 2, 3, 1, 2, 3)
    *=을 적용한 후 l의 id : 4540966184




## 2.7 list.sort()와 sorted() 내장 함수

### list.sort()

`list.sort()` 메소드는 새로운 리스트를 만들지 않고 해당 리스트 내부를 정렬해준다. `sort()`메소드는 해당 리스트를 변경하고 새로운 리스트를 생성하지 않았음을 알려주기 위해 `None`을 반환하는데, 이것은 파이썬 API의 관례라고 한다. 

따라서, 객체를 직접 변경하는 함수 또는 메소드는 해당 객체가 변경되었고 새로운 객체를 생성되지 않았음을 알려주기 위해 `None`을 반환한다. 이 책을 읽기 전까지만 해도 리스트의 `sort()`를 사용했을때 아무것도 반환하지 않아서 당황했다(역시 사람은 공부를 해야해...).

아래의 예제는 리스트 `l`을 `.sort()`를 이용해 정렬한 결과이다. 아래의 출력에서도 확인할 수 있듯이, `l.sort()`를 출력하면 `None`을 반환한다.


```python
l = [1, 3, 5, 2, 4]
print('l.sort() 전의 id 값 :', id(l))
print('l.sort()의 반환값 :', l.sort())
print('l.sort() 후의 id 값 :', id(l))
print('l.sort() 후의 l :', l)
```

    l.sort() 전의 id 값 : 1754136386376
    l.sort()의 반환값 : None
    l.sort() 후의 id 값 : 1754136386376
    l.sort() 후의 l : [1, 2, 3, 4, 5]




### sorted()

`sorted()` 내장함수는 `list.sort()`와는 달리 새로운 리스트를 생성한 뒤 정렬하여 반환한다. `sorted()`함수는 리스트 뿐만 아니라 튜플과 같은 불변 시쿤스 및 제너레이터와 같은 반복 가능한(iterable) 모든 객체에 적용 가능하다.


```python
l2 = [1, 3, 5, 2, 4]
print('sorted(l2) 전의 id 값 :', id(l2))

l2 = sorted(l2)
print('sorted(l2) 후의 id 값 :', id(l2))
print('sort(l2) 후의 l2 :', l2)
```

    sorted(l2) 전의 id 값 : 1754137063944
    sorted(l2) 후의 id 값 : 1754136328328
    sort(l2) 후의 l2 : [1, 2, 3, 4, 5]




`list.sort()`와 `sorted()`는 `reverse`와 `key` 두 개의 키워드를 인자로 받는다. 

 - **reverse** : `True`일 경우 내림차순으로 정렬, default는 `False`
 - **key** : 정렬에 사용할 기준을 정의하는 함수를 인자로 받는다. 
     - 예를 들어, `key=len`일 경우 문자열의 길이를 기준으로 정렬한다.


```python
fruits = ['grape', 'raspberry', 'apple', 'banana']

# sorted()
print('sorted(fruits) :', sorted(fruits))
print('sorted(fruits, reverse=True) :', sorted(fruits, reverse=True))
print('sorted(fruits, key=len) :', sorted(fruits, key=len))
print('sorted(fruits, key=len, reverse=True) :', sorted(fruits, key=len, reverse=True))

# fruits.sort()
fruits.sort(key=len, reverse=True)
print('fruits.sort(key=len, reverse=True)의 결과 :', fruits)
```

    sorted(fruits) : ['apple', 'banana', 'grape', 'raspberry']
    sorted(fruits, reverse=True) : ['raspberry', 'grape', 'banana', 'apple']
    sorted(fruits, key=len) : ['grape', 'apple', 'banana', 'raspberry']
    sorted(fruits, key=len, reverse=True) : ['raspberry', 'banana', 'grape', 'apple']
    fruits.sort(key=len, reverse=True)의 결과 : ['raspberry', 'banana', 'grape', 'apple']




## 2.8 정렬된 시퀀스를 bisect로 관리하기

`bisect`모듈은 `bisect()`와 `insort()`함수를 제공한다. 

- **bisect()** : 이진 검색 알고리즘을 이용해 시퀀스를 검색
- **insort()** : 정렬된 시퀀스 안에 항목을 삽입

 

### 2.8.1 bisect()로 검색하기

아래의 예제는 정렬된 시퀀스인 `HAYSTACK`에 시퀀스 `NEEDLES`의 아이템들을 `bisect`함수를 이용해 추가해줄 위치를 찾아내는 코드이다. 아래의 코드에서 `bisect`함수는 `bisect_left`와 `bisect_right` 두 가지가 있는데, 추가할 항목(아이템)의 위치를 기존 항목 왼쪽(앞, left)에 추가할건지 오른쪽(뒤, right)에 추가할건지에 따라 다르다.


```python
import bisect
import sys

HAYSTACK = [1, 4, 5, 6, 8, 12, 15, 20, 21, 23, 23, 26, 29, 30]
NEEDLES = [0, 1, 2, 5, 8, 10, 22, 23, 29, 30, 31]

ROW_FMT = '{0:2d} @ {1:2d}    {2}{0:<2d}'

def demo(bisect_fn):
    for needle in reversed(NEEDLES):
        position = bisect_fn(HAYSTACK, needle)  # 2. bisect함수 사용
        offset = position * '  |'  
        print(ROW_FMT.format(needle, position, offset))
        

if __name__ == '__main__':
    if sys.argv[-1] == 'left':  # 1. 삽입 위치를 찾기위한 bisect 함수 선택
        bisect_fn = bisect.bisect_left
    else:
        bisect_fn = bisect.bisect  # == bisect.bisect_right
        
    print('DEMO:', bisect_fn.__name__)
    print('haystack ->', ' '.join('%2d' % n for n in HAYSTACK))
    demo(bisect_fn)
```

    DEMO: bisect
    haystack ->  1  4  5  6  8 12 15 20 21 23 23 26 29 30
    31 @ 14      |  |  |  |  |  |  |  |  |  |  |  |  |  |31
    30 @ 14      |  |  |  |  |  |  |  |  |  |  |  |  |  |30
    29 @ 13      |  |  |  |  |  |  |  |  |  |  |  |  |29
    23 @ 11      |  |  |  |  |  |  |  |  |  |  |23
    22 @  9      |  |  |  |  |  |  |  |  |22
    10 @  5      |  |  |  |  |10
     8 @  5      |  |  |  |  |8 
     5 @  3      |  |  |5 
     2 @  1      |2 
     1 @  1      |1 
     0 @  0    0 




`bisect`함수는 lookup 테이블로 사용하기에 유용하다. 아래의 예제는 https://docs.python.org/3.6/library/bisect.html 에 있는 예제로써, 시험 점수를 입력받고, `bisect`함수를 이용해 `A ~ F` 까지 등급을 반환하는 코드이다.


```python
def grade(score, breakpoints=[60, 70, 80, 90], grades='FDCBA'):
    idx = bisect.bisect(breakpoints, score)
    return grades[idx]

[grade(score) for score in [33, 99, 77, 70, 89, 90, 100]]
```




    ['F', 'A', 'C', 'C', 'B', 'A', 'A']



 

### 2.8.2 bisect.insort()로 삽입하기

`bisect.insort()`는 시퀀스를 오름차순으로 유지한 채로 항목(item)을 삽입할 수 있다. 


```python
import bisect
import random

SIZE = 7
random.seed(1729)

my_list = []
for i in range(SIZE):
    new_item = random.randrange(SIZE*2)
    bisect.insort(my_list, new_item)
    print('%2d ->' % new_item, my_list)
```

    10 -> [10]
     0 -> [0, 10]
     6 -> [0, 6, 10]
     8 -> [0, 6, 8, 10]
     7 -> [0, 6, 7, 8, 10]
     2 -> [0, 2, 6, 7, 8, 10]
    10 -> [0, 2, 6, 7, 8, 10, 10]




## 2.9 리스트가 답이 아닐 때

### 2.9.1 배열 

리스트 안에 숫자만 있으면 리스트보다 배열(`array.array`)이 훨씬 더 효율적이다. `array.array`는 `pop(), inser(), extend()` 뿐만 아니라, 빠르게 파일에 저장하고 읽을 수 있는 `frombytes()`와 `tofile()`메소드도 제공한다.

아래의 예제는 천만 개의 랜덤한 실수로 이루어진 배열을 생성한 뒤 저장하고, 로딩하는 코드이다. 


```python
%%time
from array import array
from random import random

floats = array('d', (random() for i in range(10**7)))  # 'd': double
print('floats[-1] :', floats[-1])

with open('./floats.bin', 'wb') as f:
    floats.tofile(f)
```

    floats[-1] : 0.7413197791451314
    Wall time: 3.57 s



```python
%%time
floats2 = array('d')
with open('./floats.bin', 'rb') as f:
    floats2.fromfile(f, 10**7)
    
print('floats2[-1] :', floats2[-1])
print('floats == floats :', floats2 == floats)
```

    floats2[-1] : 0.7413197791451314
    floats == floats : True
    Wall time: 540 ms




위의 예제 코드 결과에서도 볼 수 있듯이, 엄청 빠르다는 것을 알 수 있다. 교재에서는 `float()`함수를 이용해 텍스트 파일에서 숫자를 읽어오는 거보다 60배 정도 빠르다고 한다. `array.tofile()` 메소드로 저장하는 것은 7배 빠르다고 한다.

 

### 2.9.2 메모리 뷰

메모리 뷰(`memoryview`) 클래스는 공유 메모리 시퀀스형이며 `bytes`를 복사하지 않고 배열의 슬라이스를 다룰 수 있게 해준다. 

메모리뷰는 NumPy에서 영감을 받아 만들어 졌다고 한다. NumPy의 개발자인 트래비스 올리판트(Travis Oliphant)는 '언제 메모리 뷰를 사용하는가?' 에 대해 아래와 같이 답했다고 한다. 

> 메모리 뷰(memoryview)는 NumPy 배열 구조체를 일반화한 것이며, 메모리 뷰는 PIL 이미지, SQLite 데이터베이스, NumPy 배열 등 데이터 구조체를 복사하지 않고 메모리를 공유할 수 있게 해준다. 데이터셋이 커지는 경우 유용하다.

`memoryview.cast()` 메소드는 `array`모듈과 비슷한 기능을 하며, 동일한 메모리를 공유한다.

아래의 예제 코드는 실제로 `memoryview`가 동일한 메모리를 공유하는지 `id()`함수를 이용해 배열의 항목(item) 주소가 같은지를 출력해보고, `memoryview`의 값을 변경했을 경우 동일하게 변경되는지 확인하는 코드이다.


```python
import array

numbers = array.array('h', [-2, -1, 0, 1, 2])  # h = signed short
memv = memoryview(numbers)

print('len(memv) :', len(memv))
print('id(memv) == id(numbers) :', id(memv[0]) == id(numbers[0]))

memv[0] = 2
print(numbers)
```

    len(memv) : 5
    id(memv) == id(numbers) : True
    array('h', [2, -1, 0, 1, 2])




## 2.10 정리

파이썬에서 제공하는 시퀀스를 제대로 알고 있으면, 효율적으로 파이써닉(Pythonic)한 코드를 작성할 수 있다. 
