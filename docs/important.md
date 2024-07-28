重点实验

# C++类和对象

## 1. 定义一个学生类，其中有3个数据成员：学号、姓名、年龄，以及若干成员函数。同时编写main函数，使用这个类，实现对学生数据的复制、赋值与输出。

代码：

```cpp
#include <iostream>
#include <cstring>
using std::cout;
using std::endl;

class Student
{
public:
    Student(int id, int age, const char *name)
    : _id(id)
    , _age(age)
    , _name(new char[strlen(name) + 1]())
    {
        strcpy(_name, name);
    }
    Student(const Student &rhs)
    : _id(rhs._id)
    , _age(rhs._age)
    , _name(new char[strlen(rhs._name) + 1]())
    {
        strcpy(_name, rhs._name);
        cout << "拷贝构造函数" << endl;
    }
    /*
     * 赋值运算符函数
     * 1. 考虑自复制问题
     * 2. 回收左操作数原本申请的堆空间
     * 3. 深拷贝（以及其他的数据成员的复制）
     * 4. 返回*this
     * */
    Student &operator=(const Student &rhs)
    {
        if(this != &rhs){
            if(_name){
                delete []_name;
            }
            _name = new char[strlen(rhs._name) + 1]();
            strcpy(_name, rhs._name);
            _id = rhs._id;
            _age = rhs._age;
            cout << "赋值运算符函数" << endl;
        }
        return *this;
    }
    ~Student()
    {
        if(_name){
            delete []_name;
            _name = nullptr;
        }
        cout << "~Student()" << endl;
    }
    void print() const
    {
        cout << "id: " << _id << endl
             << "name: " << _name << endl
             << "age: " << _age << endl;
    }
private:
    int _id;
    int _age;
    char *_name;
};

int main()
{
    Student stu1(202401, 21, "Zhang San");
    stu1.print();
    Student stu2(stu1);
    stu2.print();
    Student stu3(202402, 19, "Li Si");
    stu2 = stu3;
    stu2.print();
    return 0;
}
```

## 2. 实现单例的Point类（将单例对象创建在堆上）

代码：

```cpp
#include <iostream>
using std::cout;
using std::endl;
/*
 * 将单例对象创建在堆区
 * 1. 构造函数私有
 * 2. 通过静态成员函数getInstance创建堆上的对象，返回Point*类型的指针
 * 3. 通过静态成员函数完成堆对象的回收
 * */
class Point
{
public:
    static Point *getInstance()
    {
        if(_pInstance == nullptr){
            _pInstance = new Point(1, 2);
        }
        return _pInstance;
    }
    void init(int x, int y)
    {
        _ix = x;
        _iy = y;
    }
    static void destroy()
    {
        if(_pInstance){
            delete _pInstance;
            _pInstance = nullptr;
        }
        cout << "<<<delete heap" << endl;
    }
    void print()
    {
        cout << "(" << this->_ix
             << "," << this->_iy
             << ")" << endl;
    }
private:
    Point(int x, int y)
    : _ix(x)
    , _iy(y)
    {
        cout << "Point(int,int)" << endl;
    }
    ~Point()
    {
        cout << "~Point()" << endl;
    }
    //C++11之后可以将成员函数从类中删除
    Point(Point &rhs) = delete;
    Point &operator=(const Point &rhs) = delete;
private:
    int _ix;
    int _iy;
    //定义一个指针保存第一次创建的对象
    static Point *_pInstance;
};
Point *Point::_pInstance = nullptr;
void test1()
{
    //单例对象的使用规范
    Point::getInstance()->init(1, 2);
    Point::getInstance()->print();
    Point::getInstance()->init(10, 20);
    Point::getInstance()->print();
    Point::destroy();
}
int main()
{
    test1();
    return 0;
}
```

结果：

```txt
Point(int,int)
(1,2)
(10,20)
~Point()
<<<delete heap
```

## 3. 实现一个单例的Computer类，包含品牌和价格信息。

代码：

```cpp
#include <iostream>
#include <cstring>
using std::cout;
using std::endl;

class Computer
{
public:
    static Computer * getInstance(){
        if(_pInstance == nullptr){
            _pInstance = new Computer("xiaomi",5000);
        }
        return _pInstance;
    }

    void init(const char * brand,double price){
        if(_brand){
            delete [] _brand;
        }
        _brand = new char[strlen(brand) +1]();
        strcpy(_brand,brand);
        _price = price;
    }

    static void destroy(){
        if(_pInstance){
            delete _pInstance;
            _pInstance = nullptr;
        }
        cout << "<<<delete heap" << endl;
    }

    void print(){
        cout << "brand:" << _brand << endl;
        cout << "price:" << _price << endl;
    }
private:
    Computer(const char * brand, double price)
        : _brand(new char[strlen(brand) + 1]())
          , _price(price)
    {
        cout << "Computer(const char *,double)" << endl;
        strcpy(_brand, brand);
    }

    Computer(const Computer & rhs) = delete;
    Computer & operator=(const Computer & rhs) = delete;

    ~Computer(){
        if(_brand){
            delete [] _brand;
            _brand = nullptr;
        }
        cout << "~Computer()" << endl;
    }
private:
    char * _brand;
    double _price;
    static Computer * _pInstance;
};
Computer * Computer::_pInstance = nullptr;

void test1(){
    Computer::getInstance()->init("Apple", 15000);
    Computer::getInstance()->print();
    Computer::destroy();
}

int main()
{
    test1();
    return 0;
}
```







