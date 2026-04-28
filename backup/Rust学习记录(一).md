# Rust基础部分的语法问题记录

### 1、`use rand::Rng`  
- 使用`use` 对相关库进行引入  
- `"::"`说明：模块::类型/函数/特质(A::B 就是“在 A 里面找 B”)  
  
### 2、关于`loop{}`是否可以理解为`while(true){}`  
可以，`loop` 是Rust中的无限循环关键字，只要不遇到 break 或程序终止，就会一直执行循环体  
  
### 3、在代码中let mut guess中一定要将guess变量设为mut吗？  
是的，因为`read_line(&mut guess)`方法需要一个可变引用来修改字符串的内容，所以在创建`guess`时必须使用`mut关键字`将其声明为可变变量，如果`guess`是不可变的，就无法调用需要可变引用的方法。 
  
### 4、代码中`let mut guess = String::new()`和`let guess: u8 = match guess.trim().parse()`，当中guess是否为同一个变量？  
不是，类型上`let mut guess`为可变的String变量，用来存储前端用户输入的字符串，而`let guess: u8`中，`guess`为==不可变的整数型变量==，用来存放值是从第23行的guess（字符串）解析得到的数字  
  
### 5、能否用`let &mut guess`来代替`let guess : u8`  
不能，第23行的guess是String类型，而第51行需要的是u8类型。在Rust中，变量的类型是固定的，不能通过赋值改变类型（除非使用变量遮蔽）。`&mut guess`表示对guess的可变引用，但引用仍然指向String类型，无法直接赋值u8值。

### 6、关于这个代码的理解：
```rust
io::stdin()  
    .read_line(&mut guess)  
    .expect("Waring：无法读取行！！！！");
```
  
关于这三行代码的理解：  
    - a、`io::stdin()`：要告诉程序，“去哪找键盘？”（找到标准输入流）。必须写除非从文件读或者从网络读  
    - b、`.read_line(&mut guess)`：要给程序一个容器（guess），并命令它“把键盘打的字存进去”。因为guess 必须是一个 String 类型，而且必须是 mut（可变的），因为要往里面“填”东西。  
    - c、`.expect(...)`：因为读取可能会失败（比如输入流被关闭了），Rust 强迫必须处理这个潜在的失败。但是可以替换  
    - d、后期可以使用第三方库来改变这种写法：`text_io`  
```rust
io::stdin():  
    .read_line(&mut guess)  
    .match{  
    Ok(_) => {},    // 成功了什么都不做  
    Err(e) => println!("读取出错了，但我选择不崩溃：{}", e),  
	}  
```
```rust
// 需要在 Cargo.toml 引入库  
use text_io::read;  
fn main(){  
    let guess: u32 = read!();  
}
```
      
### 7、关于`match guess.trim().parse()`的理解  
- a、用户输入完数字通常会按回车，这会在字符串里加一个看不见的换行符。trim() 会把两端的空白字符（空格、换行等）都切掉。  
- b、`.parse()`：强力转换。这个方法尝试把字符串解析成数字 
- c、`match` 的工作：.parse() 这个动作的返回值不是直接的数字，而是一个 结果包。在 Rust 里叫 Result 枚举。这个包里只有两种可能的情况：  
    - (1)Ok：成功了，里面包着那个数字。  
    - (2)Err：失败了，里面包着错误信息  
  
### 8、关于u8  
- (1)、Rust官方提供原生整数类型有：  
    - 有符号(可以正可以负)：i8、i16、i32、i64、i128、isize  
    - 无符号(只能是 0 或正数)：u8、u16、u32、u64、u128、usize  
- (2)、为什么没有内置 u2 这种“超小”类型？  
    - a.CPU 更喜欢“按字节”来操作（8 位 = 1 字节）。  
    - b.要支持 u2 这类“非字大小整数”，编译器要生成额外指令来把多个 u2 打包到一个字节里，再做位运算和掩码，会让实现更复杂。  
    - c.可以使用社区提供的第三方库，比如 ux 或 arbitrary-int，如：  
```
[dependencies]  
ux = "0.1"  // 版本号以实际为准  
```
  
### 9、关于match guess.cmp(&secret_number)  
- (1)、类似于`if (guess > secret_number)`  
- (2)、`.cmp()` 不返回布尔值，它返回一个枚举，Less(小于)、Greater(大于)、Equal(等于)  
- (3)、`&secret_number`：只是借用数据进行"借用"，想“看看” `secret_number`，不想把它拿走
- (4)、为什么要这么写？而不是用 if / else if？  
    - a.用 if/else：如果你不小心漏写了一种情况（比如只写了大于和小于，忘了等于），编译器通常不会报错，但在运行时可能会出 bug。  
    - 用 match + cmp：Rust 的编译器非常霸道。如果只写了 Less 和 Greater，忘了写 Equal，编译器会直接报错：“你漏掉了一种可能的情况！”  
- (5)、什么情况下可以用到这种结构？  
    - a.只能用于整数、字符串、字符等可排序的类型  
    - b.浮点数用不了：==注意避开 f32/f64== 当需要穷举所有大小关系（小于、大于、等于）并且希望代码结构非常清晰、防止遗漏时，用 cmp + match 是最佳实践