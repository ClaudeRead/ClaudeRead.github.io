Rust基础部分的问题记录

#### 1、变量和可变性
```rust
fn main() {  
    let x: u8 = 5;  
    // println!("第一种写法：The value of x is: {}", x);  
    println!("第二种写法：The value of x is: {x}");  
    x = 6;  
    println!("The value of x is: {x}");  
}
```
上述代码会报错：
```powershell
error[E0384]: cannot assign twice to immutable variable `x`                                                                                
 --> src\main.rs:6:5
  |
2 |     let x: u8 = 5;
  |         - first assignment to `x`
...
6 |     x = 6;
  |     ^^^^^ cannot assign twice to immutable variable
  |
help: consider making this binding mutable
  |
2 |     let mut x: u8 = 5;
  |         +++

For more information about this error, try `rustc --explain E0384`.                                                                                                                                                            
error: could not compile `variables` (bin "variables") due to 1 previous error
```
报错原因：
&emsp;&emsp;因为在Rust中，为了确保安全性，变量默认不可变，当变量不可变时，一旦值被绑定一个名称上，用户就不能改变这个值。==如果想让一个变量变，你必须明确告诉 Rust（加 `mut`），否则我就默认它是不变的。==

#### 2、遮蔽
```rust
fn main() {  
    let x = 5;  
    let x = x + 1;  
  
    {  
        let x = x * 2;  
        println!("内层作用域中这个x的值为{x}");  // x = 12
    }  
  
    println!("外层作用域中x的值为{x}")  // x = 6
}
```
问题一：遮蔽是什么：
&emsp;&emsp;变量遮蔽就是用同一个名字重新声明变量，让新变量**隐藏**掉旧的变量。

问题二：遮蔽怎么用：
&emsp;&emsp;当你需要对变量进行**逐步转换**或**改变其类型**，但不想改变变量名字时，就应该使用遮蔽。

问题三：遮蔽与`mut`的区别：
&emsp;&emsp;`mut` 是修改**同一个变量**的值，而遮蔽是创建一个**全新的同名变量**（甚至可以改变类型）。

#### 3、元组
问题一：是什么？
&emsp;&emsp;元组是一个将多个不同类型的值组合进一个复合类型的主要方式。元组长度固定：一旦声明，其长度不会增大或缩小。
```rust
fn main(){
	let tup = (500, 2.3, 1);    // 首先创建了一个元组并绑定到 tup 变量上
	
	/*
		使用了 let 和一个模式将 tup 分成了三个不同的变量，x 、y 和 z 
	*/
	let (x, y, z) = tup;
	printLn!("这个值是:{y}");    // 程序打印出了 z 的值，也就是 1 
}
```
&emsp;&emsp;但是这里运行后，编译器会有一个warning警告，大致意思是提醒：**定义了 `x` 和 `y`，但后面代码里都没用到它们，Rust 编译器就会报警：“这两个变量可能写多了没用，你检查一下是不是写错”。**
```powershell
warning: unused variable: `x`
  --> src\main.rs:13:10
   |
13 |     let (x, y, z) = tup;
   |          ^ help: if this is intentional, prefix it with an underscore: `_x`
   |
   = note: `#[warn(unused_variables)]` (part of `#[warn(unused)]`) on by default

warning: unused variable: `y`
  --> src\main.rs:13:13
   |
13 |     let (x, y, z) = tup;
   |             ^ help: if this is intentional, prefix it with an underscore: `_y`

warning: `variables` (bin "variables") generated 2 warnings (run `cargo fix --bin "variables" -p variables` to apply 2 suggestions)

```
原因：
&emsp;&emsp;Rust 默认会开 `unused_variables` 这个 warning，它觉得“声明了变量却不用，很可能是忘了用或者写错了”，所以提醒一下
如何消除：
&emsp;- 如果这两个变量真的暂时用不到（或者以后再补逻辑），按编译器提示在名字前加下划线（前导下划线是 Rust 社区的“有意不使用”约定）：`let (_x, _y, z) = tup;`
&emsp;- 如果压根不关心某些元素，可以直接用 `_` 忽略，不绑定变量名：`let (_, _, z) = tup`

问题二、也可以使用点号（. ）后跟值的索引来直接访问所需的元组元素
```rust
let numbers : (i8, f32, u8) = (32, 2.6, 10);  
  
let one = numbers.0;  // 元组的第一个索引值是 0
let two = numbers.1;  
let three = numbers.2;  
  
println!("元组中第二位为：{two}, 第一位是：{one}, 第三位是：{three}");
```
&emsp;&emsp;注：不带任何值的元组有个特殊的名称，叫做 单元（unit） 元组。这种值以及对应的类型都写作() ，表示空值或空的返回类型。如果表达式不返回任何其他值，则会隐式返回单元值。

#### 4、数组
&emsp;&emsp;与元组不同，数组中的每个元素的类型必须相同。Rust 中的数组与一些其他语言中的数组不同,Rust 中的数组长度是固定的。比如：
```rust
let a = [1,2,3,4,5];

let a: [i32; 5] = [1,2,3,4,5]; // i32 是每个元素的类型。分号之后，数字 5 表明该数组包含五个元素。

let a = [3; 5] // 变量名为 a 的数组将包含 5 个元素，这些元素的值最初都将被设置为 3 
```
&emsp;&emsp;想要在栈（stack）而不是在堆（heap）上为数据分配空间，或者是想要确保总是有固定数量的元素时，数组非常有用。但是数组并不如 vector 类型灵活。vector 类型是标准库提供的一个 允许 增长和缩小长度的类似数组的集合类型。当不确定是应该使用数组还是 vector 的时候，那么很可能应该使用 vector。然而，当你确定元素个数不会改变时，数组会更有用。
&emsp;&emsp;访问数组元素(参考以下代码)，但是当索引超出了数组长度，就会导致运行时错误。
```rust
let a = [1,2,3,4,5]
let first = a[0];
```
```rust
use std::io;  
  
fn main() {  
    let a = [1, 2, 3, 4, 5];  
  
    println!("请输入数组的一个索引.");  
  
    let mut index = String::new();  
  
    io::stdin()  
        .read_line(&mut index)  
        .expect("无法读取行");  
  
  
	// 将用户输入的字符串转换为 `usize` 类型（数组索引需要是无符号整数）
    let index : usize = index  
        .trim()  // 去除字符串前后的空白字符（如用户输入后的换行符 `\n`）
        .parse()  // 尝试将字符串解析为 `usize` 类型（`parse` 是 `String` 的方法，需指定目标类型）
        .expect("输入的索引不是数字");  // 处理解析错误（如用户输入非数字），若解析失败则打印错误信息并终止。
  
    let element = a[index];  
  
    println!("索引{index}的值为：{element}");  
  
}
```
```powershell
thread 'main' (43672) panicked at src\main.rs:19:19:
index out of bounds: the len is 5 but the index is 9
stack backtrace:
   0: std::panicking::panic_handler
             at /rustc/ded5c06cf21d2b93bffd5d884aa6e96934ee4234/library\std\src\panicking.rs:698
   1: core::panicking::panic_fmt
             at /rustc/ded5c06cf21d2b93bffd5d884aa6e96934ee4234/library\core\src\panicking.rs:80
   2: core::panicking::panic_bounds_check
             at /rustc/ded5c06cf21d2b93bffd5d884aa6e96934ee4234/library\core\src\panicking.rs:276
   3: variables::main
             at .\src\main.rs:19
   4: core::ops::function::FnOnce::call_once<void (*)(),tuple$<> >
             at /rustc/ded5c06cf21d2b93bffd5d884aa6e96934ee4234\library\core\src\ops\function.rs:250
   5: core::hint::black_box
             at /rustc/ded5c06cf21d2b93bffd5d884aa6e96934ee4234\library\core\src\hint.rs:472
note: Some details are omitted, run with `RUST_BACKTRACE=full` for a verbose backtrace.
error: process didn't exit successfully: `target\debug\variables.exe` (exit code: 101)
```

#### 5、函数
```rust
fn main() {  
    println!("Hello, world!");  
  
    // 调用函数  
    another_function("Jack",32);  
}  
  
// 创建一个函数  
fn another_function(name: &str, value: u8){  
    println!("这是一个名为another_function的函数,这个函数的中学生姓名为：{name},年龄为：{value}");  
}
```
1. Rust 不关心函数定义所在的位置，只要函数被调用时出现在调用之处可见的作用域内就行
2. 在函数签名中，必须 声明每个参数的类型
3. 当定义多个参数时，使用逗号分隔

#### 6、语句与表达式

|       语句        |   表达式    |
| :-------------: | :------: |
| 是执行一些操作但不返回值的指令 | 计算并产生一个值 |
```rust
fn main(){
	let y = 6;    //这是一个语句
}
```
1. 语句不返回值。因此，不能把 let 语句赋值给另一个变量,例如：
```rust
fn main(){
	let x = (let y = 6)
}
```
2. 表达式的结尾没有分号。如果在表达式的结尾加上分号，它就变成了语句，而语句不会返回值：例如
```rust
fn main() {  
    let x = plus_one(5);  
  
    println!("这个值为：{x}");  
}  
  
fn plus_one(x: i32) -> i32 {  
    x + 1  
}
```
上述这段代码是正常的，但是如果将`x + 1`后面加一个分号`x + 1;` 此时，编译器会报错：
```powershell
error[E0308]: mismatched types
 --> src\main.rs:7:24
  |
7 | fn plus_one(x: i32) -> i32 {
  |    --------            ^^^ expected `i32`, found `()`
  |    |
  |    implicitly returns `()` as its body has no tail or `return` expression
8 |     x + 1;
  |          - help: remove this semicolon to return this value
```
&emsp;&emsp;这是因为，函数plus_one需要返回一个i32类型的值，但是`x + 1;` 是一个语句，语句并不会返回数值，这就导致与函数定义不相符，所以报错。故`x + 1` 是表示计算并保留结果，而`x + 1;` 表示计算但不保留结果，直接丢弃。

##### 7、循环
``` rust
// Rust循环示例：嵌套循环和标签的使用  
// 这个示例展示了如何使用循环标签来控制嵌套循环的跳出行为  
  
fn main() {  
    // 声明一个可变的计数器变量，初始值为0  
    // mut 关键字表示这个变量可以被修改  
    let mut count = 0;  
  
    // 'counting_up 是一个循环标签（loop label）  
    // 标签以单引号开头，后面跟着标识符，然后是冒号  
    // 标签用于在嵌套循环中指定要跳出哪个循环  
    'counting_up: loop {  
        // 打印当前count的值  
        println!("count = {}", count);  
  
        // 声明一个可变的剩余变量，初始值为10  
        let mut reamining = 10;
  
        // 内层循环，没有标签  
        loop {  
            // 打印当前reamining的值  
            // 使用大括号 {} 进行字符串插值，变量名可以直接放在大括号内  
            println!("reamining = {reamining}");  
  
            // 如果reamining等于9，跳出内层循环  
            // 这里的break只跳出当前的内层循环  
            if reamining == 9 {  
                break;  
            }  
  
            // 如果count等于2，跳出外层循环（带标签的那个循环）  
            // 'counting_up 指定了要跳出的循环标签  
            // 这是标签的主要用途：在嵌套循环中跳出外层循环  
            if count == 2 {  
                break 'counting_up;  
            }  
  
            // 将reamining减1  
            reamining -= 1;  
        }  
  
        // 内层循环结束后，count加1  
        // 这行代码只有在内层循环正常结束时才会执行  
        // 如果是通过 break 'counting_up 跳出的，这行不会执行  
        count += 1;  
    }  
  
    // 打印最终的count值  
    // 当count等于2时，通过 break 'counting_up 跳出循环  
    // 所以最终count的值是2  
    println!("End count = {count}");  
}  
  
/*
  
最终输出：  
count = 0  
reamining = 10  
reamining = 9  
count = 1  
reamining = 10  
reamining = 9  
count = 2  
reamining = 10  
End count = 2  
*/
```

代码执行流程分析：
&emsp;第一次外层循环 (count = 0):  
&emsp;&emsp;- 打印 "count = 0"  
&emsp;&emsp;- reamining = 10
&emsp;&emsp;- 内层循环：
&emsp;&emsp;&emsp;- 打印 "reamining = 10"
&emsp;&emsp;&emsp;- reamining != 9，count != 2
&emsp;&emsp;&emsp;- reamining -= 1 → reamining = 9
&emsp;&emsp;&emsp;- 打印 "reamining = 9"
&emsp;&emsp;&emsp;- reamining == 9，break 跳出内层循环
&emsp;&emsp;- count += 1 → count = 1
&emsp;第二次外层循环 (count = 1):  
&emsp;&emsp;- 打印 "count = 1"  
&emsp;&emsp;- reamining = 10  
&emsp;&emsp;- 内层循环：  
&emsp;&emsp;&emsp;- 打印 "reamining = 10"   
&emsp;&emsp;&emsp;- reamining != 9，count != 2  
&emsp;&emsp;&emsp;- reamining -= 1 → reamining = 9    
&emsp;&emsp;&emsp;- 打印 "reamining = 9"   
&emsp;&emsp;&emsp;- reamining == 9，break 跳出内层循环  
&emsp;&emsp;- count += 1 → count = 2
&emsp;第三次外层循环 (count = 2):  
&emsp;&emsp;- 打印 "count = 2"  
&emsp;&emsp;- reamining = 10  
&emsp;&emsp;- 内层循环：  
&emsp;&emsp;&emsp;- 打印 "reamining = 10"    
&emsp;&emsp;&emsp;- reamining != 9，count == 2  
&emsp;&emsp;&emsp;- break 'counting_up 跳出外层循环  
&emsp;&emsp;- count += 1 不会执行

关键知识点：  
1. loop 是Rust中的无限循环，需要使用break来跳出  
2. 循环标签（如 'counting_up）可以用于在嵌套循环中指定要跳出哪个循环  
3. break 默认跳出当前循环，break 'label 跳出指定标签的循环  
4. Rust中的字符串插值可以使用 {} 或 {变量名} 两种形式  
5. 变量声明使用 let 关键字，可变变量需要添加 mut 关键字 
6. 使用循环标签的的建议：
	- 只在“简单两层嵌套 + 明确要跨层退出”这种典型场景使用标签；
	- 标签名尽量清晰：`'outer`/`'inner` 比 `'a`/`'b` 好很多；
	- 如果嵌套太深、标签太多，先考虑：拆函数、用 iterator、引入 early return，而不是硬撑着一堆标签。
<!-- ##{"timestamp":1751358761}## -->