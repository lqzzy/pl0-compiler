## bug记录

### 比较过程不可以有多个

#### 举例：
如$2>3>4$会报错

#### 原因：
在condition中检测完第一个条件后就会退出condition，没有再对后续条件进行检测。
```cpp
condition(){
    if{
        ...
    }
    else{
        op = sym;
        get_sym();
        expression();
        switch (op)
        {
            ...
        default:
            break;
        }
    }
}
```

#### 修改如下
```cpp
condition(){
    if{
        ...
    }
    while(){
        op = sym;
        get_sym();
        expression();
        switch (op)
        {
            ...
        default:
            break;
        }
    };
}
```


### `print`中不能打印负数的问题

```c
print(-1);
//会报错
```

修改：

排查后发现`statement`的`print`部分，`First`集合漏掉了`SYM_MINUS`。后面添加了条件表达式功能，
又在`First`集里添加了`SYM_LPARENT`，`SYM_NOT`，使得`print`现在能够直接打印表达式的值。


## 功能添加

### 1.将条件表达式也添加到了`expression`里面，优先级小于运算符。单目运算符`!`除外

***大改动！！！***

#### 方法：

将原本的`expression函数`变成`expression_without_condition函数`，再将原来的`condition函数`变成`expression函数`，
将`if`，`while`后面跟着的`condition`全部变成`expression`

### 2.添加了`&&`，`||`，`!`，三种逻辑运算符优先级小于运算符，采用短路方式计算

### 3.`if` 语句后的条件表达式后必须带一个`=`，`>`，`<`，等关系运算符，将其修改为直接用表达式的值

#### 举例

```c
    if (1+2) then
        print(1);
```
会报错

#### 方法

将原来`condition`改成`expression`。