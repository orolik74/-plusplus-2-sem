## Перегрузка операторов


>min объем malloc(1) == 16 байт (x86 64) из-за выравнивания  
Если не выровнены данные, то надо очень аккуратно работать, лучше использовать санитайзер

### Перегрузка new и delete

new  
new [] 

Стандартный оператор new:
```c++
void * operator new(size_t){
    return malloc(size_t);
}
```
Операция new -> оператор new + конструктор

Операция delete аналогично
```c++
new (std::nothrow) int[2];
```
Компилятор ищет оператор new с такой сигнатрой:
```c++
void *operator new(size_t size, std::nothrow_t(по факту все, что в скобках)){
    
}
```
Размещающий new
```c++
void* operator new(size_t size, void* ptr){
    return ptr;
}
```

```c++
//data_ = new char[sizeof(Matrix) * 10];
void vector::push_back(const Matrix& v){
    new (&data[size_]) Matrix{v};
    ++size_;
}
```

```c++
class Matrix{
    void* operator new(size_t size);
    void operator delete(void* ptr);
}
```

###  Перегрузка обычных операторов

```c++
class Ratio{
    public:
        Ratio(int n, int d);
        ~Ratio() = default;
        //*
        Ratio(const Ratio&) = default;
        Ratio(Ratio&&) = default;
        Ratio& operator = (const Ratio&) = default;
        Ratio& operator = (Ratio&&) = default;
        //*
}
```
Если конструктор по умолчанию прописываем, то //* надо явно прописывать


```c++

```