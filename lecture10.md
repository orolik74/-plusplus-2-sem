### Конструктор копирования 
```c++
class Array{
    ...
    public:
    ...
    Array copy();
};

Array a(10);
Array b = a;
```
Стандартный конструктор копирования просто копирует все, но если есть ссылка(указатель),
то ссылка скопируется, деструктор не сможет выполниться корректно

```c++
class Array{
    ...
    public:
    ...
    Array (const Array& other){ // конструктор копирования
        ...
    }
};

Array b = a; // вызывается конструктор копирования
```

NB: Передача в функцию по значению и ли return - тоже вызов копирования

```c++
Arrac makeArray(){
    ...
    return a; // вызов конст. копирования
}

void print(Array a){
    ...
}

Array a(10);
print(a); // конст.копирования для аргумента
```

НЕЛЬЗЯ вот так, потому что это беск. рекурсия:
```c++
class Array{
    ...
    public:
    Array(Array other)
}
```

### Оператор присваивания

```c++
//array.h
class Array{
    public:
    void operator =(const Array& other);
};

//array.cpp
void Array::operator = (const Array& other){
    //что-то для копирования
}
```
Более идеоматичный вариант

```c++
Array& Array::operator = (const Array& other){
    if (&other == this){
        return *this; // что-бы ничего не произошло с a = a
    }
    delete [] data;
    size = other.size;
    data = new int[size];
    for (size_t i = 0; i < size(); ++i)
        data[i] = other.data[i];
    return *this; // *this, чтобы a = b = c работало
}
```
a = b = c это b = c, a = c

Неявное преобразование size_t -> Array:
```c++
Array(size_t) // существует

Array a = 10; // сделает Array a(10);

print(Array array){...}
print(10); // создаст массив из 10 элементов и напечатает
```

Решение: явный (explicit)
```c++
class Array{
    public:
        explicit Array(size_t size);
    ...
};

Array a(10); // все ок

Array a = 10; // это CE

print(10); //CE 
```

```c++
Array makeArray(){
    ...
    return Array(10);
}
```
функция, вызывающая makeArray заводит место под возвращаемое значение и 
передает его адрес в функцию, потом создает локальный объект Array(10),
поместит его по адресу  
RVO

```c++
Array makeArray(){
    Array array(10);
    ...
    return array;
}
```
NRVO (named return variable optimization)

общее название copy elision  
компиляторы сами это делают  
начиная с C++17 URVO обязательно(всегда делает компилятор)

Про это важно помнить, так как компилятор может убрать вызов 
копирования даже если есть побочные эффекты при копировании

### Конструкторы полей

Если у класса есть поля, то все конструкторы обязаны проинициализировать 
все поля (т.е. вызвать их конструкторы)

По умолчанию вызывается дефолтный конструктор (для примитивных типов они ничего не делают)

```c++
class Vector3{
    public:
        Vector3(float x ,float y, float z);
};

class RigidBody{
    private:
    Vector3 position;
    public: 
        
        RigidBody(args):position(0, 0, 0){}
};
```

В коде конструктора поля класса уже созданы и тд

### const 
В c от ошибок 
константное поле обязательно нужно явно проинициализировать в конструкторе

Методы могут быть помечены const - те, что не меняют состояние
```c++
size_t get_size() const{
    return size;
}
```
у const объектов нельзя вызвать не const объектов

const obj -> const method (помечен const)  
ne const obj -> const / ne const method

если obj const => все поля const 

mutable у класса означает, что это поле можно менять даже из const методов класса  
почти всегда это считает плозим тоном

### операторы
их больше 60
операторы можно перегружать, почти все, но нельзя:
a.b, a.*b, a?b:c

a,b - сделать a, сделать b, вернуть a

BigInt, например 

свободная функция:
```c++
BigInt operator +(const BigInt& a, const BigInt& b){...}
```
Раб класса:
```c++
BigInt BigInt::operator+(const DigInt& other){..}
```

```c++
BigInt a, b;
BigInt c = a + b === operator+(a, b) === a.operator+(b);
```

>3 + a //не ок, если как operator+ BigInt, у int не может быть operatir+(BigInt)

Общее правило:
* нужны знания, о внутренности: метод
* не нужны - свободная функция
RMK: иногда удобнее метод

a += b compound assignment // можно реализовать через a + b и наоборот

часто эффективнее a + b через a += b 
(a += b через метод, так как нужен доступ к данным нужен)

```c++
int operator[](size_t index) const {
    return data[index];
}
int& operato[](size_s index){ // для a[5] = x;
    return data[index];
}
```
с C++23 оператор с произвольным числом аргументов
```c++
class Matrix{
..
    public:
    float& operator[](size_t row, size_t column);
};

Matrix m;
m[3, 5] = 2.718f; // до C++23 это оператор ,
```

```c++
class BigInt{
    BigInt& operator++(); // по умолч префикс
    
    BigInt operator++(int){
        BigInt copy(*this);
        ++(*this) // или this -> operator++();
        return copy;
    }  
};
```

Операторы сравнения, их 6, из них 2 надо явно написать, а остальные можно через < и ==  
можно даже a == b определить как !(a < b) && !(b < a)

с c++20 three-way comparator (spaceship operator) a <=> b
особый тип из 3 значений (меньше, равно, больше), например std::strong_ordering

можно определять только == и <=>

Оператор приведения типа (или конструктор)

```c++
/// BigInt -> double
operator double() const {...} 
```

Можно тоже сделать explicit (только явные, лооол) (лучше делать так)  
Иногда хочется использовать как bool, в if, while, && и тд.

В if можно explicit operator