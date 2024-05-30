# 🪵 lamda表达式|C++11

### 匿名函数的基本语法

```c
//匿名函数的基本语法为
//[捕获列表](参数列表)->返回类型{函数体}
void test1()
{
    auto Add = [](int a, int b) -> int {
        return a + b;
    };
    std::cout << Add(1, 2) << std::endl;        //输出3
}
```

一般情况下，编译器可以自动推断出lambda表达式的返回类型，所以我们可以不指定返回类型，即：

```c
//一般情况下，编译器可以自动推断出lambda表达式的返回类型，所以我们可以不指定返回类型
//[捕获列表](参数列表){函数体}
void test2()
{
    auto Add = [](int a, int b) {
        return a + b;
    };
    std::cout << Add(1, 2) << std::endl;        //输出3
}
```

<mark style="color:red;">在 C++11 及其之后的标准中，如果 Lambda 表达式函数体内有多个</mark> <mark style="color:red;"></mark><mark style="color:red;">`return`</mark> <mark style="color:red;"></mark><mark style="color:red;">语句且返回类型不一致，编译器无法自动推断出返回类型，此时必须显式指定返回类型。</mark>

以下是一个示例：

```cpp
#include <iostream>
#include <string>

int main() {
    auto example = [](int x) -> std::string {
        if (x > 0) {
            return "Positive";
        } else if (x < 0) {
            return "Negative";
        } else {
            return "Zero";
        }
    };

    std::cout << example(10) << std::endl;   // 输出 "Positive"
    std::cout << example(-5) << std::endl;   // 输出 "Negative"
    std::cout << example(0) << std::endl;    // 输出 "Zero"

    return 0;
}
```

在这个示例中，Lambda 表达式的返回类型是 `std::string`。由于函数体内有多个 `return` 语句，编译器无法自动推断出返回类型，因此我们使用 `-> std::string` 明确指定了返回类型。

<mark style="color:red;">**另一个示例，展示返回不同类型的情况：**</mark>

```cpp
#include <iostream>
#include <variant>

int main() {
    auto example = [](int x) -> std::variant<int, std::string> {
        if (x > 0) {
            return x;
        } else {
            return "Negative or Zero";
        }
    };

    auto result1 = example(10);
    auto result2 = example(-5);

    if (std::holds_alternative<int>(result1)) {
        std::cout << "Result1: " << std::get<int>(result1) << std::endl;
    } else {
        std::cout << "Result1: " << std::get<std::string>(result1) << std::endl;
    }

    if (std::holds_alternative<int>(result2)) {
        std::cout << "Result2: " << std::get<int>(result2) << std::endl;
    } else {
        std::cout << "Result2: " << std::get<std::string>(result2) << std::endl;
    }

    return 0;
}
```

在这个示例中，Lambda 表达式的返回类型是 `std::variant<int, std::string>`。这样做的好处是可以在不同情况下返回不同类型的值。由于 Lambda 表达式有多个 `return` 语句且返回类型不同，我们必须显式指定返回类型为 `std::variant<int, std::string>`。

### 捕获列表

有时候，需要在匿名函数内使用外部变量，所以用捕获列表来传参，如

```cpp
void test3()
{
    int c = 12;
    int d = 30;
    auto Add = [c, d](int a, int b)->int { //捕获列表加入使用的外部变量c，否则无法通过编译
        cout << "d = " << d  << endl;
        return c;
    };
    d = 20;
    std::cout << Add(1, 2) << std::endl;
}
```

但是，如果Add中加入一句：c = a;

```cpp
void test4()
{
    int c = 12;
    auto Add = [c](int a, int b)->int { //捕获列表加入使用的外部变量c，否则无法通过编译
//        c = a; // 编译报错
        return c;
    };
    std::cout << Add(1, 2) << std::endl;
}
```

1. 如果捕获列表为`[&]`，则表示所有的外部变量都按引用传递给lambda使用；
2. 如果捕获列表为`[=]`，则表示所有的外部变量都按值传递给lambda使用；
3. `[this]` ，捕获当前类中的this指针，让lambda表达式拥有和当前类成员函数同样的访问权限，如果已经使用了 & 或者 =, 默认添加此选项
4. 匿名函数构建的时候对于按值传递的捕获列表，会立即将当前可以取到的值拷贝一份作为常数，然 后将该常数作为参数传递。

```cpp
void test5()
{
    int c = 12;
    int d = 30;
    auto Add = [&c, &d](int a, int b)->int { //捕获列表加入使用的外部变量c，否则无法通过编译
        c = a; // 编译对的
        cout << "d = " << d  << endl;
        return c;
    };
    d = 20;
    std::cout << Add(1, 2) << std::endl;
}
```

```c
void test6() {
    int c = 12;
    int d = 30;
    auto Add = [&c, &d](int& a, int& b) -> int {  // 捕获列表加入使用的外部变量c，否则无法通过编译
        a = 11;
        b = 12;
        cout << "d = " << d << endl;  // d = 12
        return a + b; // 23
    };
    d = 20;
    std::cout << Add(c, d) << std::endl;
    cout << "c = " << c << endl;  // c = 11
    cout << "d = " << d << endl;  // d = 12
}
```

### 匿名函数的简写

匿名函数由捕获列表、参数列表、返回类型和函数体组成；可以忽略参数列表和返回类型，但不可以忽 略捕获列表和函数体，如：

> auto f = \[]{ return 1 + 2; };

### Lambda捕获列表

<table><thead><tr><th width="171">[]</th><th>空捕获列表，Lambda不能使用所在函数中的变量。</th></tr></thead><tbody><tr><td>[names]</td><td>names是一个逗号分隔的名字列表，这些名字都是Lambda所在函数的局部 变量。默认情况下，这些变量会被拷贝，然后按值传递，名字前面如果使用 了&#x26;，则按引用传递</td></tr><tr><td>[&#x26;]</td><td>隐式捕获列表，Lambda体内使用的局部变量都按引用方式传递</td></tr><tr><td>[=]</td><td>隐式捕获列表，Lanbda体内使用的局部变量都按值传递</td></tr><tr><td>[&#x26;,identifier_list]</td><td>identifier_list是一个逗号分隔的列表，包含0个或多个来自所在函数的变量， 这些变量采用值捕获的方式，其他变量则被隐式捕获，采用引用方式传递， identifier_list中的名字前面不能使用&#x26;。</td></tr><tr><td>[=,identifier_list]</td><td>identifier_list中的变量采用引用方式捕获，而被隐式捕获的变量都采用按值 传递的方式捕获。identifier_list中的名字不能包含this，且这些名字面前必须 使用&#x26;。</td></tr></tbody></table>

### lamda表达式的本质

使用lambda表达式捕获列表捕获外部变量，如果希望去修改按值捕获的外部变量，那么应该如何处理呢？这就需要使用mutable选项，**被mutable修改是lambda表达式就算没有参数也要写明参数列表，并且可以去掉按值捕获的外部变量的只读（const）属性。**

```cpp
int a = 0;
auto f1 = [=] {return a++; };              // error, 按值捕获外部变量, a是只读的
auto f2 = [=]()mutable {return a++; };     // ok 在外部a的值仍然是0
```

mutable选项的作用就在于取消operator()的const属性。

### reference

* [https://subingwen.cn/cpp/lambda/](https://subingwen.cn/cpp/lambda/)
