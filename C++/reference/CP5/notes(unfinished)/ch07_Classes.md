# 第七章 类

> Last updated: Aug 22, 2020
>
> Author: Yunfan Huang

### 定义抽象数据类型

- 类的基本思想
  - **数据抽象**（data abstraction）：一种依赖于接口和实现分离的编程（以及设计）技术。
    - 类的接口（interface）：用户能执行的操作。
    - 类的实现（implementation）：类的数据成员、负责接口实现的函数体以及定义类所需的各种私有函数。
  - **封装**（encapsulation）：实现了类的接口和实现的分离。
- 抽象数据类型：实现了数据抽象与封装
  - 实现：由类的设计者负责考虑类的实现过程
  - 接口：使用该类的程序员只需抽象地思考类型做了什么，无需了解类型的工作细节

#### 类成员 （Member）

- 类的成员必须在类的内部声明，不能在其他地方增加成员。
- 类成员可以是数据、函数、类型别名。

#### 类的成员变量

- 可变数据成员 （mutable data member）
  - 动机：希望能修改类的某个数据成员，即使是在一个常成员函数内。
  - 可变数据成员：声明中加入了`mutable`关键字的成员变量。它永远不会是`const`，即使它是`const`对象的成员。
- **注意**：类内初始值必须使用`=`或`{}`的初始化形式。
- 类的静态成员
  - `static`成员是与类关联，非`static`成员与该类的对象相关联。
  - 可使用作用域运算符`::`直接访问静态成员，也可以使用对象访问。
  - 声明、定义域初始化：
    - 使用`static`关键字进行声明；在类外部定义，不用加`static`。
    - `static`成员通常不能在类内部初始化，而要在定义时进行初始化。
      - 如果非得在类内初始化，则要求`static`成员必须是字面值常量类型的`constexpr`。此时通常也应在类的外部定义该成员，以便外部函数使用该成员变量，但是这时不能再指定一个初始值。
    - **建议**：如果要想确保对象只定义一次，最好将静态数据成员的定义与其他非内联函数的定义放在同一个文件中。

#### 类的成员函数

- 声明、定义与调用

  - 成员函数的**声明**必须在类的内部。
  - 成员函数的**定义**既可以在类的内部也可以在外部。
  - 成员函数可以使用点运算符`.`进行**调用**。

- 成员函数作为内联函数

  - 在类中，常有一些规模较小的函数适合于被声明成内联函数。
    - 定义在类内部的成员函数是隐式地自动`inline`的。
    - 在类外部定义的成员函数，也可以在声明时显式地加上`inline`。
  - 在声明和定义的地方同时说明`inline`是合法的，但最好只在类外定义的地方说明`inline`，以便使类更容易理解。
  - **注意**：`inline`成员函数也应该与相应的类定义在同一个头文件中。

- `*this`：

  - 每个成员函数都有一个额外的、隐含的形参`this`。默认情况下，`this`的类型是指向类类型非常量版本的常指针。例如在`Sales_data`成员函数中，`this`的类型是`Sale_data *const`，即它是一个顶层`const`。

  - 形参表后面的`const`可以改变隐含的`this`形参的类型，这种使用`const` 的成员函数被称为常成员函数，此时`this`是个指向常量的常指针，即它既是一个顶层`const`，也是一个底层`const`。可作如下理解：

    ```c++
    // 实际代码
    string Sales_data::isbn() const { return isbn; }
    // 伪代码，说明隐式的 this 是如何使用的
    // 下面的代码是非法的：因为我们不能显式定义自己的 this 指针
    string Sales_data::isbn(const Sales_data *const this) {
      return this->isbn;
    }
    ```

    - 普通的非`const`成员函数：`this`是指向类类型的`const`指针（可以改变`this`所指向对象的值，不能改变`this`保存的地址）。
    - `const`成员函数：`this`是指向`const`类类型的`const`指针（既不能改变`this`所指向对象的值，也不能改变`this`保存的地址）。

  - 通过返回对象的（常）引用，可以方便成员函数连续调用。

    ```c++
    // 返回对象的副本（返回值，不建议）
    Screen display(ostream &os) {
      do_display(os);
      return *this;
    }
    // 返回对象本身而非其副本，可以修改其值（返回非常量引用）
    Screen &display(ostream &os) {
      do_display(os);
      return *this;
    }
    // 返回对象本身且不能改变其值（返回常量引用）
    const Screen &display(ostream &os) const {
      do_display(os);
      return *this;
    }
    ```

    * **注意**：常对象、常对象的引用或指针都只能调用常成员函数。
    * **建议**：对公共代码使用私有功能函数，以避免在多处使用同样的代码
      * 随着类的规模发展，`display`函数可能变得更加复杂
      * 很可能在开发过程中给`do_display`函数添加调试信息，而这些信息将在代码的最终产品版本中去掉
      * 这个额外的函数调用不会增加任何开销，因为在类内部定义的`do_display`函数会被隐式地声明为内联函数

  - `const`关键字可以作为函数重载的依据。

#### 类相关的非成员函数

- 和类相关的非成员函数，定义和声明都应该在类的外部。
- 若函数在概念上属于类但不定义在类中，则它一般应与类声明（而非定义）在同一个头文件内。此时用户使用接口的任何部分都只需引入一个文件。

### 访问控制与封装

- 访问说明符（access specifiers）
  - `public`：定义在`public`后面的成员在整个程序内可以被访问；`public`成员定义类的接口。
  - `private`：定义在`private`后面的成员可以被类的成员函数访问，但不能被使用该类的代码访问；`private`隐藏了类的实现细节。
- `class`和`struct`都可用于定义类，其在`C++`中的唯一区别为默认访问权限。
  - 使用`class`：在第一个访问说明符之前的成员是`priavte`的。
  - 使用`struct`：在第一个访问说明符之前的成员是`public`的。
- 友元和友元类
  - 允许特定的非成员函数访问一个类的私有成员。友元的声明以关键字`friend`开始，通常将友元声明成组地放在类定义的开始或者结尾。
  - 友元的声明仅仅指定了访问的权限，而非一个通常意义上的函数声明。如果希望类的用户能够调用某个成员函数，那么我们就必须在友元声明之外在专门对函数进行一次声明。
  - 如果一个类指定了友元类，则友元类的成员函数可以访问此类包括非公有成员在内的所有成员。
  - `friend`关键字可以作为函数重载的依据。
- **关键概念**：封装的益处
  - 确保用户的代码不会无意间破坏封装对象的状态。
  - 被封装的类的具体实现细节可以随时改变，而无需调整用户级别的代码。

### 类的作用域

- 类类型（class type）
  - 每个类定义了唯一的类型。对于两个类来说，即使它们的成员完全一样，这两个类也是两个不同的类型。
  - 声明之后、定义之前的类是不完全类型，只能在非常有限的情景下使用
    - 可以定义指向这种类型的指针或引用
    - 可以声明（但不能定义）以不完全类型为参数或返回类型的函数
  
- 每个类都会定义它自己的作用域。

  - 在类的作用域之外，普通的数据和函数成员只能由引用、对象、指针使用成员访问运算符来访问，而对于类类型成员则使用作用域运算符访问。

  - 在类的外部定义成员函数时必须同时提供类名和函数名。函数的返回类型通常在函数名前面，因此当成员函数定义在类的外部时，返回类型中使用的名字都位于类的作用域之外。

  - 一般来说，内层作用域可以重新定义外层作用域的名字，即使该名字已经在内层作用域中使用过。然而在类中，如果成员使用了外层作用域中的某个名字，而该名字代表一种类型，则类不能在之后重新定义该名字。

    ```c++
    typedef double Money;
    std::string bal;
    class Acccount {
     public:
      Money balance() { return bal; } // 返回 Account 的类成员 bal
     private:
      Money bal;
      // ...
    }
    ```

    ```c++
    int height;
    class Screen {
     public:
      typedef std::string::size_type pos;
      void setHeight(pos);
      pos height = 0; // 隐藏了外层作用域中的 height
    };
    Screen::pos verify(Screen::pos);
    void Screen::setHeight(pos var) {
      // var：参数
      // height：类成员
      // verify：全局函数
      height = verify(var);
    }
    ```

    - **注意**
      - 编译器处理完类中的全部声明后，才会处理成员函数的定义。
      - 尽管类的成员被隐藏了，但仍可通过加上类的名字或显式使用`this`指针来强制访问类成员。
    - **建议**：类型名定义通常出现在类的开始处，以确保所有使用该类型的成员都出现在类名的定义之后。

### 类的构造函数

#### 类的构造函数

- 类通过一个或几个特殊的成员函数来控制其对象的初始化，这些函数叫做构造函数。构造函数是特殊的、与类同名的成员函数，放在类的`public`部分。

- 编译器**只有在发现类不包含任何构造函数的情况下才会生成一个默认的构造函数**（称为合成的默认构造函数）。因此一旦我们定义了一些其他的构造函数，那除非我们再定义一个默认的构造函数，否则类将没有默认构造函数。

  - `= default`要求编译器合成默认的构造函数。（`C++11`）

  ```c++
  // 冒号和花括号之间的代码为类的初始化列表
  // 第一种构造函数对 units_sold 和 revenue 采用默认实参进行初始化
  Sales_data(const string &s) : bookNo(s) {}
  Sales_data(const string &s, unsigned n, double p)
      : bookNo(s), units_sold(n), revenue(p*n) {}
  ```


#### 构造函数的初始值列表

- 随着构造函数体一开始执行，初始化就完成了。

  ```c++
  // 用初始化方式进行构造
  Sales_data::Sales_data(const string &s, unsigned cnt, double price)
  	: bookNo(s), units_sold(cnt), revenue(cnt*price) {}
  // 用赋值方式进行构造，没有使用构造函数初始值
  Sales_data::Sales_data(const string &s, unsigned cnt, double price) {
    bookNo = s;
    units_sold = cnt;
    revenue = cnt * price;
  }
  ```

- **若成员是`const`或者引用类型的数据，只能采用初始化列表的方式进行构造**，而不能采用赋值的方式。类似地，**当成员属于某种类类型且该类没有定义默认构造函数时，也必须将这个成员初始化**。

  ```c++
  class ConstRef {
   public:
    ConstRef(int ii);
   private:
    int i;
    const int ci;
    int &ri;
  }
  // 错误：ci 和 ri 必须别初始化
  ConstRef::ConstRef(int ii) {
    i = ii;  // 正确
    ci = ii; // 错误：不能给 const 赋值
    ri = i;  // 错误：ri 没被初始化
  }
  // 正确：显示地初始化引用和 const 成员
  ConstRef::ConstRef(int ii) : i(ii), ci(ii), ri(i) {}
  ```

  * **建议**：使用构造函数初始值，以避免意想不到的编译错误，并提升底层效率。

- 成员的初始化顺序与它们在类定义中出现顺序一致。

  - **建议**：最好令构造函数初始值的顺序和成员声明顺序保持一致，并用构造函数的参数作为成员的初始值，而尽量避免使用同一个对象的其他成员。这样的好处是我们可以不必考虑成员的初始化顺序。

- **注意**：如果一个构造函数为所有参数都提供了默认参数，那么它实际上也定义了默认的构造函数。

#### 若干特殊的构造方式

- 委托构造函数（delegating constructor）将自己的职责委托给了其他构造函数。

    ```c++
    class Sales_data {
     public:
      // 非委托构造函数使用对应实参初始化成员
      Sales_data(std::string s, unsigned cnt, double price)
          : bookNo(s), units_sold(cnt), revenue(cnt*price) {}
      // 其余构造函数全都委托给另一个构造函数
      Sales_data() : Sales_data("", 0, 0) {}
      Sales_data(std::string s) : Sales_data(s, 0, 0) {}
      Sales_data(std::istream &s) : Sales_data() { read(is, *this); }
    }
    ```

* 隐式的类类型转换

  * 如果构造函数只接受一个实参，则它实际上定义了转换为此类类型的隐式转换机制，这种构造函数又叫**转换构造函数**（converting constructor）。注意，编译器只会自动地执行**仅一步**类型转换。
  * 抑制构造函数定义的隐式转换可以通过将构造函数声明为`explicit`加以阻止。此时，`explicit`构造函数只能用于直接初始化，不能用于拷贝形式的初始化。

* 聚合类 （aggregate class）

  ```c++
  struct Data {
    int ival;
    string s;
  }
  // 正确：使用初始值列表进行初始化，且初始值的顺序必须和声明的顺序一致
  Data val1 = {0, "Anna"};
  // 错误：不能使用 "Anna" 初始化 ival，也不能使用 1024 初始化 s
  Data val2 = {"Anna", 1024};
  ```

  * 聚合类使得用户可以直接访问其成员，其具体特征为：所有成员都是`public`的，没有定义任何构造函数，没有类内初始值，没有基类，也没有`virtual`函数。
  * **聚合类的对象必须使用由花括号括起来的成员初始值列表进行初始化**，且初始值的顺序必须和声明的顺序一致。

* 字面值常量类

  ```c++
  class Debug {
   public:
    constexpr Debug(bool b = true) : hw(b), io(b), other(b) {}
    constexpr Debug(bool h, bool i, bool o) : hw(h), io(i), other(o) {}
    constexpr bool any() { return hw || io || other; }
    void set_io(bool b) { io = b; }
    void set_hw(bool b) { hw = b; }
    void set_others(bool b) { other = b; }
   private:
    bool hw;
    bool io;
    bool other;
  }
  constexpr Debug io_sub(false, true, false);
  if (io_sub.any())
    cerr << "print appropriate error messages" << endl;
  constexpr Debug prod(false);
  if (prod.any())
    cerr << "print an error messages" << endl;
  ```

  * 概念回顾
    * `constexpr`函数的参数和返回值必须是字面值。除了算术类型、引用和指针外，某些类也是字面值类型。
    * 字面值类型的类可能含`constexpr`函数成员，这些成员应符合`constexpr`函数的要求，且是隐式`const`的。
  * 数据成员都是字面值类型的聚合类是字面值常量类。如果不是聚合类，则必须满足下面所有条件：
    * 数据成员都必须是字面值类型。
    * 类必须至少含有一个`constexpr`构造函数。
    * 如果一个数据成员含有类内部初始值，则内置类型成员的初始值必须是一条常量表达式；或者如果成员属于某种类类型，则初始值必须使用成员自己的`constexpr`构造函数。
    * 类必须使用析构函数的默认定义，该成员负责销毁类的对象。
