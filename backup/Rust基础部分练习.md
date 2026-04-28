实验内容：使用Rust语言，做一个学生管理系统的Demo

实验功能：
&emsp;1. 设置欢迎界面
&emsp;2. 要求需要对用户输入进行检查
&emsp;3. 功能模块划分为：
&emsp;&emsp;a. 第一选项为显示所有学生
&emsp;&emsp;b. 第二选项为增加学生
&emsp;&emsp;&emsp;- 内容：姓名、年龄、班级
&emsp;&emsp;c. 第三选项为删除学生
&emsp;&emsp;&emsp;- 先显示全部学生
&emsp;&emsp;&emsp;- 通过指定序号进行删除
&emsp;&emsp;d. 第四选项为查找指定学生
&emsp;&emsp;&emsp;- 根据姓名查找学生，显示匹配的学生信息
&emsp;&emsp;e. 第五选项为退出系统

备注：需要对代码进行添加注释

#### 主界面
```rust
let mut students: Vec<(String, u8, String)> = Vec::new();  
  
// loop 是一个无限循环，会一直执行直到遇到 break 语句  
// 这是程序的主循环，用于持续显示菜单和处理用户操作  
loop {  
    println!("***学 生 管 理 系 统***");  
    println!("1.显示所有学生");  
    println!("2.增加学生");  
    println!("3.删除学生");  
    println!("4.查找指定学生");  
    println!("5.退出");  
    println!("==========================");  
    println!("请选择操作 (1-5): ");  
  
    // 创建一个空的字符串变量，用于存储用户的输入  
    // String::new() 创建一个新的空字符串  
    let mut choice = String::new();  
      
    // std::io::stdin() 获取标准输入（键盘输入）  
    // .read_line(&mut choice) 读取一行输入并存储到 choice 变量中  
    // &mut choice 表示传递 choice 的可变引用，允许函数修改它  
    // .expect("输入参数有误，请重新输入") 如果读取失败，显示错误信息并终止程序  
    std::io::stdin()  
        .read_line(&mut choice)  
        .expect("输入参数有误，请重新输入");  
  
    // ==================== 输入验证部分 ====================    
    // choice.trim() 去除字符串两端的空白字符（包括换行符）  
    // .parse() 尝试将字符串解析为数字  
    // match 是 Rust 的模式匹配，类似于其他语言的 switch-case    
    // Ok(num) 表示解析成功，num 是解析出的数字  
    // Err(_) 表示解析失败，_ 表示忽略错误的具体信息  
    let choice: u32 = match choice.trim().parse() {  
        Ok(num) => num,  // 解析成功，使用解析出的数字  
        Err(_) => {  
            println!("waring: 请输入数字！");  
            continue;  // 跳过本次循环的剩余部分，直接开始下一次循环  
        }  
    };  
  
    // ==================== 根据用户选择执行操作 ====================    
    if choice == 1 {  
        // 调用显示所有学生的函数  
        // &mut students 传递 students 的可变引用  
        // 虽然显示函数不需要修改数据，但为了与其他函数保持一致性  
        show_all_students(&mut students);  
    } else if choice == 2 {  
        // 调用添加学生的函数  
        add_student(&mut students);  
    } else if choice == 3 {  
        // 调用删除学生的函数  
        delete_student(&mut students);  
    } else if choice == 4 {  
        // 调用查找学生的函数  
        find_student(&mut students);  
    } else if choice == 5 {  
        // 用户选择退出  
        println!("感谢使用学生管理系统，再见！");  
        break;  // break 语句跳出循环，结束程序  
    } else {  
        // 用户输入了无效的选项  
        println!("无效的选择，请重新输入！");  
    }  
    // 循环结束，继续下一次迭代，重新显示菜单  
}
```

#### 添加学生功能
```rust
fn add_student(students: &mut Vec<(String, u8, String)>) {  
    // 打印功能标题，\n 表示换行  
    println!("\n---添加学生---");  
  
    // ==================== 输入学生姓名 ====================    
      
    // 创建一个空字符串用于存储姓名输入  
    let mut name = String::new();  
      
    // 读取用户输入的一行文本到 name 变量中  
    std::io::stdin()  
        .read_line(&mut name)  
        .expect("无法读取");  
      
    // trim() 去除字符串两端的空白字符（包括用户按回车产生的换行符）  
    // to_string() 将字符串切片转换为 String 类型  
    // 这样处理后，name 就是一个干净的字符串，没有多余的空白  
    let name = name.trim().to_string();  
  
    // ==================== 输入学生年龄 ====================    
      
    // 创建一个空字符串用于存储年龄输入  
    // 注意：用户输入的是文本，需要转换为数字  
    let mut age_str = String::new();  
      
    // 读取用户输入  
    std::io::stdin()  
        .read_line(&mut age_str)  
        .expect("无法读取输入");  
      
    // 尝试将字符串转换为 u8 类型的数字（0-255）  
    // match 进行模式匹配，判断转换是否成功  
    let age: u8 = match age_str.trim().parse() {  
        Ok(num) => num,  // 转换成功，使用转换后的数字  
        Err(_) => {  
            // 转换失败，说明用户输入的不是数字  
            println!("年龄必须为数字！添加失败！！！");  
            return;  // return 提前退出函数，不执行后面的代码  
        }  
    };  
  
    // ==================== 输入学生班级 ====================    
    // 创建一个空字符串用于存储班级输入  
    let mut class_name = String::new();  
      
    // 读取用户输入  
    std::io::stdin()  
        .read_line(&mut class_name)  
        .expect("无法读取");  
      
    // 去除空白字符并转换为 String 类型  
    let class_name = class_name.trim().to_string();  
  
    // ==================== 将学生信息添加到列表 ====================    
    // students.push() 向动态数组的末尾添加一个元素  
    // (name, age, class_name) 创建一个元组，包含三个数据  
    // 元组的顺序对应：(姓名: String, 年龄: u8, 班级: String)  
    students.push((name, age, class_name));  
      
    // 函数结束，学生信息已成功添加到列表中  
}

```

#### 删除学生
```rust
fn delete_student(students: &mut Vec<(String, u8, String)>) {  
    println!("\n--- 删除学生 ---");  
  
    // ==================== 检查列表是否为空 ====================    
    
    // students.is_empty() 检查动态数组是否为空  
    if students.is_empty() {  
        println!("暂无学生数据。");  
        return;  // 列表为空，直接退出函数  
    }  
  
    // ==================== 显示所有学生供用户选择 ====================    
    // 先调用显示函数，让用户看到所有学生及其序号  
    show_all_students(students);  
  
    // ==================== 读取要删除的学生序号 ====================    
      
    // 创建字符串变量存储用户输入的序号  
    let mut index_str = String::new();  
      
    // 读取用户输入  
    std::io::stdin()  
        .read_line(&mut index_str)  
        .expect("无法读取输入");  
      
    // 将字符串转换为 usize 类型（无符号整数，用于数组索引）  
    let index: usize = match index_str.trim().parse() {  
        Ok(num) => num,  
        Err(_) => {  
            println!("序号必须是数字！删除失败。");  
            return;  // 转换失败，退出函数  
        }  
    };  
  
    // ==================== 验证序号是否有效 ====================    
    // index == 0 表示用户输入了 0，但序号从 1 开始  
    // index > students.len() 表示用户输入的序号超出了列表范围  
    // students.len() 返回列表中元素的数量  
    if index == 0 || index > students.len() {  
        println!("✗ 无效的序号！删除失败。");  
        return;  // 序号无效，退出函数  
    }  
  
    // ==================== 删除指定位置的学生 ====================    
    // students.remove(index - 1) 删除指定位置的元素并返回它  
    // 注意：数组索引从 0 开始，但用户看到的序号从 1 开始  
    // 所以需要用 index - 1 来转换为实际的数组索引  
    let removed = students.remove(index - 1);  
      
    // removed 是被删除的元组，removed.0 表示元组的第一个元素（姓名）  
    println!("✓ 已删除学生 - 姓名: {}", removed.0);  
}  
```

#### 查找学生
``` rust
 fn find_student(students: &Vec<(String, u8, String)>) {  
    println!("\n--- 查找学生 ---");  
  
    // ==================== 检查列表是否为空 ====================    
    if students.is_empty() {  
        println!("暂无学生数据。");  
        return;  
    }  
  
    // ==================== 读取要查找的学生姓名 ====================    
    println!("请输入要查找的学生姓名: ");  
      
    // 创建字符串变量存储要查找的姓名  
    let mut search_name = String::new();  
      
    // 读取用户输入  
    std::io::stdin()  
        .read_line(&mut search_name)  
        .expect("无法读取输入");  
      
    // 去除空白字符，得到要查找的姓名  
    let search_name = search_name.trim();  
  
    // ==================== 遍历列表查找学生 ====================    
    // 创建一个布尔变量，用于标记是否找到匹配的学生  
    let mut found = false;  
      
    // for 循环遍历 students 列表中的每一个元素  
    // student 是每次循环中的一个学生元组  
    for student in students {  
        // 解构元组，将元组的三个元素分别赋值给三个变量  
        // (name, age, class) 对应元组的三个部分  
        let (name, age, class) = student;  
          
        // 判断当前学生的姓名是否与要查找的姓名相同  
        // == 是相等比较运算符  
        if name == search_name {  
            // 找到匹配的学生，打印其信息  
            // {} 是占位符，会被后面的变量值替换  
            println!("✓ 找到学生 - 姓名: {}, 年龄: {}, 班级: {}", name, age, class);  
              
            // 将 found 标记为 true，表示找到了至少一个匹配的学生  
            found = true;  
        }  
        // 如果不匹配，继续循环查找下一个学生  
    }  
  
    // ==================== 检查是否找到学生 ====================    
    // !found 表示 found 为 false，即没有找到任何匹配的学生  
    if !found {  
        println!("✗ 未找到名为 {} 的学生。", search_name);  
    }
```

#### 显示所有学生
```rust
fn show_all_students(students: &mut Vec<(String, u8, String)>) {  
    println!("\n--- 所有学生列表 ---");  
  
    // ==================== 检查列表是否为空 ====================    
    if students.is_empty() {  
        println!("暂无数据");  
        return;  
    }  
  
    // ==================== 遍历并显示所有学生 ====================    
    // 创建序号计数器，从 1 开始  
    let mut index = 1;  
      
    // for 循环遍历 students 列表  
    for student in students {  
        // 解构元组，获取姓名、年龄、班级  
        let (name, age, class) = student;  
          
        // 打印学生信息，格式：序号->姓名，年龄，班级  
        // {index}、{name}、{age}、{class} 是占位符  
        println!("序号：{index}->姓名：{name}，年龄：{age}，班级：{class}");  
          
        // 序号加 1，为下一个学生做准备  
        index += 1;  
    }

```
<!-- ##{"timestamp":1772476812}## -->