# 第七章 异常处理

> Last updated: Sep 2, 2020
>
> Author: Yunfan Huang

### 本章内容

本章学习 C++ 的异常处理机制，以增加程序的稳健性，主要内容包括：

* 异常的提炼、抛出与捕获，标准异常
* 局部资源管理

#### 程序案例

* `triang_seq_incE`：简单的异常类的实现

### 注意事项

* 采用资源管理的手法，定义专属类，并将初始化操作和资源请求放在构造函数中，资源释放放在析构函数中，可增加防护性和自动化性。
* 在请求资源时使用智能指针`auto_ptr`（属于类模板）可以自动删除通过`new`表达式分配的对象，注意使用前要`#include <memory>`。