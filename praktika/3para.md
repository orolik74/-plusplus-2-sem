```c++
static_cast<T> // приведение типов ошибки на этапе компиляции
dinamic_cast<T> //в рантайме, должен быть полиморфным, у него 
// или его родителя есть virtual(нужен для нормального приведения в рамках иерархии классов)
```

```c++
//Child1->Base
Base* b = ?;
Child1* c = dynamic_cast<Child1*>(b); // если не приводится, то nullptr
```
Не обязательно писать dinamic_cast для приведения к более конкретному типу(к Base)

С ссылками:
```c++
Child1& r = dynamic_cast<Child1&>(b); // если приведение невозможно, то искл. std::bad_cast
is 
as
```
Должна быть включена RTTI для dinamic_cast (по умолчанию сейчас она всегда включена)

```c++
const_cast<T> // только точечно в нужных местах
_const
_valatile
```
```c++
std::string::c_str()->const char*
```

```c++
reinterpret_cast <T> // расслабься, я понимаю, что я делаю, что угодно к чему угодно
```

### Исключения
```c++
try{
    code:
    throw std::runtime_error("Oops");
    throw 42;
    throw false;
    throw "string"; // можно сделать throw чего угодно 
}
```

Общая практика:
```c++
std::exception // базовый тип исключений
```
```c++
try{
    code;
    throw std::runtime_error("Oops");
    ...
}
catch(int e){}
catch (std::exeption & e){
    throw;
    throw e;
}
```

```c++
//>=C++11
void foo() noexcept{
    throw 1; // std::terminate
}
```
noexcept может быть true(нет исключений) или false(точно исключение)

```c++
~AllDtors() noexcept(default);
```

```c++
void foo() throw(A, int)
            throw()
```

```c++
catch (...){} // ловит исключения всех типов, не очень рекомендуется использовать,
// так как на разных архитектурах(? мб платформах) они могут быть разными
```

### Про память
Подход основанный на RAII
Умные указатели -- указатели, которые отслеживают количество ссылок на один объект

C++11  
\<memory>

раньше был auto_ptr, но сейчас его нет(вырезали, хахаха)

1. unique_ptr<T>
2. shared_ptr<T> / weak_ptr<T>