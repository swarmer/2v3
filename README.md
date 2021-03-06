# 2v3
Отличия Python 2.7 от 3.x


## Удалено

### Old-style classes
В Python 2.x если не указать класс-наследник, создаётся старый класс, который ведёт себя по-другому иногда неочевидным образом.

В Python 3.x `class A:` эквивалентно `class A(object):`.

### Стандартная библиотека
Из стандартной библиотеки удалены некоторые старые модули.

### Исключения
Удалён старый синтаксис отлова (`except Exception, e`)
и поднятия (`raise Exception, args`) исключений.

### Операторы
Удалён старый синтаксис repr: `` `obj` ``  
Удалён оператор сравнения `<>`

### Специальные методы
- `__cmp__`
- `__getslice__`

### long
В Python 3 есть только один `int`, который может быть сколь угодно большим.

### Неявные относительные импорты
```python
# a/b/c/m1.py

# python2
import a.b.c.m2
from . import m2
import m2

#python3
import a.b.c.m2
from . import m2
```

### Unbound methods
В Python3 нет непривязанных методов, только функции и привязанные методы:

```python
#python 2
class A(object):
    def f(self):
        return self

>>> type(A.f)
instancemethod

>>> type(A().f)
instancemethod

>>> A().f()
<__main__.A at 0x7f562109a190>

>>> A.f(4)
TypeError: unbound method f() must be called with A instance as first argument (got int instance instead)


#python 3
class A:
    def f(self):
        return self

>>> type(A.f)
function

>>> type(A().f)
method

>>> A().f()
<__main__.A at 0x7f5589b76e48>

>>> A.f(4)
4
```


## Изменено

### Деление
В Python 3 оператор `/` всегда возвращает float,
а для целочисленного деления нужно использовать оператор `//`

```python
# python 3
>>> 8 / 2
4.0
>>> 8 // 2
4
```

Это поведение можно включить в Python 2 при помощи `from __future__ import division`

### Сравнение несовместимых типов
Несовместимые типы (например, int и str или list и set) теперь бросают исключение при попытке сравнения:
    
```python
#python 3
>>> 3 < 'abc'
TypeError: unorderable types: int() < str()
```

### super
`super` теперь умеет угадывать свои аргументы при помощи чёрной магии со стеком:

```python
# python 2
super(C, self).__init__()

# python 3
super(C, self).__init__()
super().__init__()
```

### Метаклассы
Метакласс теперь объявляется ключевым аргументом `metaclass` вместе с родительскими классами.

Весёлый факт: вместе с ними можно передавать любые ключевые аргументы и они будут переданы в конструктор метакласса.

Также у метаклассов может быть поле `__prepare__` и если оно есть, определение класса будет выполняться
в контексте dict или совместимого объекта, возвращённого `__prepare__`:

```python
# python 3
class AnswerMeta(type):
    @staticmethod
    def __new__(cls, name, bases, namespace, **kwargs):
        return super().__new__(cls, name, bases, namespace)

    def __init__(self, name, bases, namespace, **kwargs):
        super().__init__(name, bases, namespace)

    @staticmethod
    def __prepare__(name, bases, answer):
        return {'ANSWER': answer}

class Universe(metaclass=AnswerMeta, answer=42):
    answer = ANSWER

print(Universe.answer, Universe.ANSWER)

# output:
# 42 42
```

### print
`print` как ключевое слово удалён, вместо него добавлена встроенная функция:

    print(value, ..., sep=' ', end='\n', file=sys.stdout, flush=False)

    Prints the values to a stream, or to sys.stdout by default.
    Optional keyword arguments:
    file:  a file-like object (stream); defaults to the current sys.stdout.
    sep:   string inserted between values, default a space.
    end:   string appended after the last value, default a newline.
    flush: whether to forcibly flush the stream.

В Python 2 это можно получить при помощи `from __future__ import print_function`

### Строки
В Python 3 более чёткое и принудительное разделение между строками и последовательностями байтов
`str` - теперь класс юникодных строк, состоящих из логических символов, а не байтов
и они пишутся через обычные кавычки

Для работы с байтами создан неизменяемый класс `bytes`,
его литералы пишутся с префиксом 'b': `b'bytestring'`
Байтовые строки не поддерживают многие строковые операции, хотя в 3.5 "возвращено"
форматирование при помощи оператора `%`.

`basestring` удалён.

`str.encode(encoding) -> bytes`

`bytes.decode(encoding) -> str`

В Python 3.3 для упрощения портирования кода "возвращён" префикс 'u',
который ничего не делает: `'str' == u'str'`

### Ленивые view и последовательности
Большинство методов и функций, которые возвращали листы, заменены на варианты,
которые возвращают ленивые последовательности, а старые ленивые варианты удалены.
Теперь часто приходится заворачивать вызовы в `list()`.

Таким образом:
`xrange() -> range()`  
`dict.iterkeys() -> dict.keys()`  
`itertools.izip() -> zip()`  
и т.д.

### Кодировка кода
В Python 2 кодировка по умолчанию - ascii, в Python 3 - utf-8.

### builtins
Переименован модуль `__builtin__ -> builtins`, но не глобальная переменная `__builtins__`
Некоторые функции были перемещены или удалены, в частности:
- `raw_input -> input`, старый `input` удалён.
- удалён `execfile`
- удалён `apply`
- `reduce -> functools.reduce`
- `reload -> importlib.reload`  
и т.д.

### exec
Вместо ключевого слова `exec` используется глобальная функция `exec`.

### Файловые дескрипторы
С версии 3.4 новые файловые дескрипторы по умолчанию не наследуются дочерними процессами.

### Изменены некоторые имена
`StringIO.StringIO -> io.StringIO`  
`SimpleHTTPServer -> http.server`
и т.д.

### Специальные методы
`iterator.next() -> iterator.__next__()`  
Глобальная функция `next` всё так же работает в обеих версиях.

`__nonzero__ -> __bool__`


## Добавлено

### Распаковка
```python
>>> a, *b, c = [1, 2, 3, 4]
>>> print a, b, c
1 [2, 3] 4

>>> print(*[1], *[2], 3, *[4, 5])
1 2 3 4 5

>>> def fn(a, b, c, d):
...     print(a, b, c, d)
...

>>> fn(**{'a': 1, 'c': 3}, **{'b': 2, 'd': 4})
1 2 3 4

>>> *range(4), 4
(0, 1, 2, 3, 4)

>>> [*range(4), 4]
[0, 1, 2, 3, 4]

>>> {*range(4), 4, *(5, 6, 7)}
{0, 1, 2, 3, 4, 5, 6, 7}

>>> {'x': 1, **{'y': 2}}
{'x': 1, 'y': 2}
```

### nonlocal
С помощью `nonlocal x` можно указать в замыкании, что мы хотим изменять
переменную во внешней функции вместо создания новой локальной.

### raise from
Можно указать одно исключение как причину другого при помощи конструкции `raise ... from ...`:

```python
>>> class DatabaseError(Exception):
...   pass
... 
>>> try:
...   raise psycopg2.IntegrityError(':(')
... except psycopg2.IntegrityError as ierr:
...   raise DatabaseError('Cannot insert data') from ierr
... 
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
psycopg2.IntegrityError: :(

The above exception was the direct cause of the following exception:

Traceback (most recent call last):
  File "<stdin>", line 4, in <module>
__main__.DatabaseError: Cannot insert data
```

### importlib
Новый в 3.1 модуль, предоставляющий доступ ко внутренностям реализации импортов.

### futures
В 3.2 добавлены примитивы для асинхронного исполнения задач в других потоках или процессах.

### Новые исключения
До 3.3 при выполнении различных системных операций нужно было отлавливать разные типы исключений
(OSError, IOError, WindowsError, socket.error и т.д.), или сверять поле `.errno`, чтобы определить конкретную ошибку.

В 3.3 эти имена исключений ссылаются на OSError, а у него есть подклассы для разных кодов ошибок:
- BlockingIOError
- ChildProcessError
- ConnectionError
    - BrokenPipeError
    - ConnectionAbortedError
    - ConnectionRefusedError
    - ConnectionResetError
- FileExistsError
- FileNotFoundError
- InterruptedError
- IsADirectoryError
- NotADirectoryError
- PermissionError
- ProcessLookupError
- TimeoutError

### yield from
В 3.3 добавлен синтаксис делегации генераторов `yield from`.
В самых простых случаях его можно описать как:

```python
def gen1():
    yield from gen2

def gen1():
    for val in gen2:
        yield val
```

Но он также пробрасывает переданные извне значение в подгенератор, а также корректно обрабатывает исключения
и таким образом может заменить несколько десятков строк кода.

### mock
С 3.3 в стандартную библиотеку встроен `unittest.mock`.

### venv
C 3.3 в стандартную библиотеку встроен venv - совместимый аналог virtualenv, вызываемый командой `pyvenv`.

### Неявные пакеты
С 3.3 если питон не находит нужный пакет с `__init__.py`, но находит обычные папки с нужным названием
(даже если в разных местах), он будет считать их содержимое искомым пакетом.

Не нужно это использовать :). Это сделано для случаев, когда нужно склеивать пакет (вроде `mylibcontib`)
из нескольких сторонних библиотек, написанных разными авторами.

### pathlib
В 3.4 добавлена библиотека для удобной работы с путями:

```python
>>> p = Path('/etc')
>>> q = p / 'init.d' / 'reboot'
>>> q
PosixPath('/etc/init.d/reboot')
>>> q.resolve()
PosixPath('/etc/rc.d/init.d/halt')
```

### asyncio
TODO 3.4

### coroutines, async, await
TODO 3.5

### enum
В 3.5 добавлен модуль enum для удобного создания перечисляемых типов.

### Матричное умножение
В 3.5 добавлен оператор матричного умножения `@`. Учёные в судорогах от счастья, остальным пофиг.

### Аннотации
В 3.0 добавлены аннотации аргументов и возврата функций:

```python
def haul(item: Haulable, *vargs: PackAnimal) -> Distance:
    ...
```

Они ничего не делают и просто хранятся как поля функции.

Я не видел ни одну известную библиотеку, которая это использует, кроме...

### typing
В 3.5 при помощи аннотаций добавлен модуль typing со стандартным API для описания типов аргументов и возврата функций:

```python
T = TypeVar('T', int, float)

def vec2(x: T, y: T) -> List[T]:
    return [x, y]
```

Он ничего не проверяет, но предоставляет общий для всех сторонних утилит статического анализа синтаксис типизации.

## Портирование

### warnings
Можно запустить Python 2 с флагом `-3`, тогда он будет писать предупреждения
о проблемах при будущем переходе на Python 3.

### 2to3
Утилита, автоматически переводящая код Python 2 на код Python 3.
Однако, она не может справиться со всеми случаями или использовать многие нововведения.

### six
Самый популярный способ перевода библиотек на Python 3 - написание кода, который одинаково работает
и на Python 2, и на Python 3. Для этого есть множество вспомогательных утилит в библиотеке `six`.
