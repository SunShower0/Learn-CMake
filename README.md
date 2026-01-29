# CMake-Study

## 1. CMake的使用

### 1.1 注释

+ **注释行**

  ```cmake
  # 这是一个 CMakeLists.txt 文件
  cmake_minimum_required(VERSION 3.0.0)
  ```

+ **注释块**

  ```cmake
  #[[ 这是一个 CMakeLists.txt 文件。
  这是一个 CMakeLists.txt 文件
  这是一个 CMakeLists.txt 文件]]
  cmake_minimum_required(VERSION 3.0.0)
  ```

### 1.2 简单的CMakeLists.txt

+ **创建CMakeLists.txt 文件**

+ **添加命令**

  + **cmake_minimum_required：指定使用的 cmake 的最低版本**

    ```cmake
    cmake_minimum_required(VERSION 3.0)
    ```

  + **project：定义工程名称**，并可指定工程的版本、工程描述、web主页地址、支持的语言（默认情况支持所有语言），如果不需要这些都是可以忽略的，只需要指定出工程名字即可。

    ```cmake
    # PROJECT 指令的语法是：
    project(<PROJECT-NAME> [<language-name>...])
    project(<PROJECT-NAME>
           [VERSION <major>[.<minor>[.<patch>[.<tweak>]]]]
           [DESCRIPTION <project-description-string>]
           [HOMEPAGE_URL <url-string>]
           [LANGUAGES <language-name>...])
    ```

  + **add_executable：定义工程会生成一个可执行程序**

    ```cmake
    add_executable(可执行程序名 源文件名称)
    # 样式1
    add_executable(app add.cpp div.cpp main.cpp)
    # 样式2
    add_executable(app add.cpp;div.cpp;main.cpp)
    ```

+ **执行CMake命令： cmake CMakeLists.txt文件所在路径**

  ```cmake
  # 创建build目录
  mkdir build
  # 进入build目录
  cd build
  # 执行cmake命令
  cmake ..
  ```

  当执行CMake命令之后，CMakeLists.txt 中的命令就会被执行，我们可以看到在对应的目录下生成了一个**makefile**文件，此时再执行**make**命令，就可以对项目进行构建得到所需的可执行程序了。

### 1.3 定制

+ **定义变量SRC_LIST**

  ```cmake
  # 方式1: 各个源文件之间使用空格间隔
  set(SRC_LIST add.c  div.c   main.c  mult.c  sub.c)
  
  # 方式2: 各个源文件之间使用分号 ; 间隔
  set(SRC_LIST add.c;div.c;main.c;mult.c;sub.c)
  
  # app是生成的可执行程序
  add_executable(app  ${SRC_LIST})
  ```

+ **指定c++标准 C++11、C++14、C++17、C++20等新特性**

  + **在编译时**

    ```cmake
    g++ *.cpp -std=c++11 -o app
    ```

  + **在CMakeLists.txt中通过set指定**

    ```cmake
    #增加-std=c++11
    set(CMAKE_CXX_STANDARD 11)
    
    #增加-std=c++14
    set(CMAKE_CXX_STANDARD 14)
    
    #增加-std=c++17
    set(CMAKE_CXX_STANDARD 17)
    ```

  + **在执行cmake命令时**

    ```cmake
    #增加-std=c++11
    cmake CMakeLists.txt文件路径 -DCMAKE_CXX_STANDARD=11
    #增加-std=c++14
    cmake CMakeLists.txt文件路径 -DCMAKE_CXX_STANDARD=14
    #增加-std=c++17
    cmake CMakeLists.txt文件路径 -DCMAKE_CXX_STANDARD=17
    ```

+ **指定输出的路径**

  在CMake中指定可执行程序输出的路径，也对应一个宏，叫做**EXECUTABLE_OUTPUT_PATH**，它的值还是通过**set**命令进行设置:

  ```cmake
  set(EXECUTABLE_OUTPUT_PATH <路径>)
  ```

### 1.4 搜索文件

如果一个项目里边的源文件很多，在编写CMakeLists.txt文件的时候不可能将项目目录的各个文件一一罗列出来。所以，在CMake中为我们提供了搜索文件的命令，可以使用 **aux_source_directory** 命令或者 **file** 命令。

+ **aux_source_directory**

  > **aux_source_directory(< dir > < variable >)**
  >
  > dir：要搜索的目录
  >
  >  variable：将从dir目录下搜索到的源文件列表存储到该变量中

  ```cmake
  # 搜索 src 目录下的源文件  CMAKE_CURRENT_SOURCE_DIR 宏表示当前访问的 CMakeLists.txt 文件所在的路径， 也可以使用PROJECT_SOURCE_DIR 
  # 搜索到的文件存储到SRC_LIST中
  aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/src SRC_LIST)
  ```

+ **file**

  > **file(GLOB/GLOB_RECURSE 变量名 要搜索的文件路径和文件类型)**
  >
  > GLOB: 将指定目录下搜索到的满足条件的所有文件名生成一个列表，并将其存储到变量中。
  >
  > GLOB_RECURSE：递归搜索指定目录，将搜索到的满足条件的文件名生成一个列表，并将其存储到变量中。

  ```cmake
  # 在MAIN_SRC路径下搜索.cpp
  file(GLOB MAIN_SRC ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp)
  # 在MAIN_HEAD路径下搜索.h
  file(GLOB MAIN_HEAD ${CMAKE_CURRENT_SOURCE_DIR}/include/*.h)
  ```

### 1.5 包含头文件

在编译项目源文件的时候，很多时候都需要将源文件对应的头文件路径指定出来，这样才能保证在编译过程中编译器能够找到这些头文件，并顺利通过编译。

```cmake
include_directories(<头文件路径>)
```

### 1.6 制作库

+ **制作静态库**

  在Linux中，静态库名字分为三部分：lib+库名字+.a，此处只需要指定出库的名字就可以了，另外两部分在生成该文件的时候会自动填充。

  ```cmake
  add_library(库名称 STATIC 源文件1 源文件2 ...)
  ```

  ```cmake
  cmake_minimum_required(VERSION 3.0)
  project(CALC)
  include_directories(${PROJECT_SOURCE_DIR}/include)
  file(GLOB SRC_LIST "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp")
  add_library(calc STATIC ${SRC_LIST})
  ```

+ **制作动态库**

  在Linux中，动态库名字分为三部分：lib+库名字+.so，此处只需要指定出库的名字就可以了，另外两部分在生成该文件的时候会自动填充。

  ```cmake
  add_library(库名称 SHARED 源文件1 源文件2 ...) 
  ```

  ```cmake
  cmake_minimum_required(VERSION 3.0)
  project(CALC)
  include_directories(${PROJECT_SOURCE_DIR}/include)
  file(GLOB SRC_LIST "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp")
  add_library(calc SHARED ${SRC_LIST})
  
  ```

+ **指定输出路径    LIBRARY_OUTPUT_PATH**

  ```cmake
  set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR})
  ```

  ```cmake
  cmake_minimum_required(VERSION 3.0)
  project(CALC)
  include_directories(${PROJECT_SOURCE_DIR}/include)
  file(GLOB SRC_LIST "${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp")
  # 设置动态库/静态库生成路径
  set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)
  # 生成动态库
  #add_library(calc SHARED ${SRC_LIST})
  # 生成静态库
  add_library(calc STATIC ${SRC_LIST})
  ```

### 1.7 链接库

+ **链接静态库**

  > **link_libraries(<static lib> [<static lib>...])**
  >
  > **参数1**：指定出要链接的静态库的名字
  > **参数2-N**：要链接的其它静态库的名字
  >
  > 如果该静态库不是系统提供的，可能出现静态库找不到的情况，此时可以将静态库的路径也指定出来：
  >
  > **link_directories(<lib path>)**

  ```cmake
  # 包含静态库路径
  link_directories(<路径>)
  
  # 链接libcalc.a
  link_libraries(calc)
  ```

  ```cmake
  cmake_minimum_required(VERSION 3.0)
  project(CALC)
  # 搜索指定目录下源文件
  file(GLOB SRC_LIST ${CMAKE_CURRENT_SOURCE_DIR}/src/*.cpp)
  # 包含头文件路径
  include_directories(${PROJECT_SOURCE_DIR}/include)
  # 包含静态库路径
  link_directories(${PROJECT_SOURCE_DIR}/lib)
  # 链接静态库
  link_libraries(calc)
  add_executable(app ${SRC_LIST})
  ```

+ **链接动态库**

  > **target_link_libraries(
  >     **<target>
  >     **<PRIVATE|PUBLIC|INTERFACE> <item>...**
  >     **[<PRIVATE|PUBLIC|INTERFACE> <item>...]...)**
  >
  > **target：**指定要加载的库的文件的名字
  >
  > + 该文件可能是一个**源文件**
  > + 该文件可能是一个**动态库/静态库文件**
  > + 该文件可能是一个**可执行文件**
  >
  > **PRIVATE|PUBLIC|INTERFACE：**动态库的访问权限，默认为PUBLIC
  >
  > + 如果各个动态库之间没有依赖关系，无需做任何设置，三者没有没有区别，一般无需指定，使用**默认的 PUBLIC** 即可。
  > + 动态库的链接具有**传递性**，如果动态库 A 链接了动态库B、C，动态库D链接了动态库A，此时动态库D相当于也链接了动态库B、C，并可以使用动态库B、C中定义的方法。
  > + **PUBLIC：**在public后面的库会**被Link到前面的target中**，并且里面的**符号也会被导出**，提**供给第三方使用**。
  >   **PRIVATE：**在private后面的库**仅被Link到前面的target中**，并且终结掉，**第三方无法使用**
  >   **INTERFACE：**在interface后面引入的库**不会被链接到前面的target中，只会导出符号**。

  ```cmake
  cmake_minimum_required(VERSION 3.0)
  project(TEST)
  file(GLOB SRC_LIST ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)
  # 添加并指定最终生成的可执行程序名
  add_executable(app ${SRC_LIST})
  # 指定可执行程序要链接的动态库名字
  target_link_libraries(app pthread)
  ```

+ **动态库的链接和静态库是完全不同的**

  + 静态库会在**生成可执行程序的链接阶段被打包到可执行程序中**，所以**可执行程序启动**，**静态库就被加载到内存中了**。
  + 动态库在生成可执行程序的链接阶段**不会**被打包到可执行程序中，当可执行程序被启动并且**调用了动态库中的函数的时候，动态库才会被加载到内存**

  ```cmake
  # 包含动态库路径
  link_directories(<路径>)
   
  #链接libpthread.so libcalc.so
  target_link_libraries(app pthread calc)
  
  # app: 对应的是最终生成的可执行程序的名字 上述命令须写在生成可执行文件命令后
  # pthread：这是可执行程序要加载的动态库，这个库是系统提供的线程库，全名为libpthread.so，在指定的时候一般会掐头（lib）去尾（.so）。
  ```

+ **优先使用 target_link_libraries**

  + target_link_libraries 和  link_libraries 是 CMake 中用于链接库的两个命令，都可以用于链接动态库和静态库

  + target_link_libraries 用于指定一个目标（如可执行文件或库）在编译时需要链接哪些库。它支持指定库的名称、路径以及链接库的顺序。

  + link_libraries 用于设置全局链接库，这些库会链接到之后定义的所有目标上。它会影响所有的目标，适用于全局设置，但不如 target_link_libraries 精确。

### 1.8 日志

在CMake中可以用用户显示一条消息，该命令的名字为**message**

```cmake
message([STATUS|WARNING|AUTHOR_WARNING|FATAL_ERROR|SEND_ERROR] "message to display")
```

**(无)** ：重要消息
**STATUS** ：非重要消息
**WARNING**：CMake 警告, 会继续执行
**AUTHOR_WARNING**：CMake 警告 (dev), 会继续执行
**SEND_ERROR**：CMake 错误, 继续执行，但是会**跳过生成的步骤**
**FATAL_ERROR**：CMake 错误, **终止**所有处理过程

```cmake
# 输出一般日志信息
message(STATUS "source path: ${PROJECT_SOURCE_DIR}")
# 输出警告信息
message(WARNING "source path: ${PROJECT_SOURCE_DIR}")
# 输出错误信息
message(FATAL_ERROR "source path: ${PROJECT_SOURCE_DIR}")
```

### 1.9 变量操作

+ **追加**

  + **使用set拼接**

    ```cmake
    set(变量名1 ${变量名1} ${变量名2} ...)
    ```

  + ```cmake
    cmake_minimum_required(VERSION 3.0)
    project(TEST)
    set(TEMP "hello,world")
    file(GLOB SRC_1 ${PROJECT_SOURCE_DIR}/src1/*.cpp)
    file(GLOB SRC_2 ${PROJECT_SOURCE_DIR}/src2/*.cpp)
    # 追加(拼接)
    set(SRC_1 ${SRC_1} ${SRC_2} ${TEMP})
    message(STATUS "message: ${SRC_1}")
    ```

  + **使用list拼接**

    ```cmake
    list(APPEND 变量名1 ${变量名1} ${变量名2} ...)
    ```

    ```cmake
    cmake_minimum_required(VERSION 3.0)
    project(TEST)
    set(TEMP "hello,world")
    file(GLOB SRC_1 ${PROJECT_SOURCE_DIR}/src1/*.cpp)
    file(GLOB SRC_2 ${PROJECT_SOURCE_DIR}/src2/*.cpp)
    # 追加(拼接)
    list(APPEND SRC_1 ${SRC_1} ${SRC_2} ${TEMP})
    message(STATUS "message: ${SRC_1}")
    ```

+ **移除**

  ```cmake
  list(REMOVE_ITEM <列表> <待删除值1> <待删除值2>...])
  ```

  ```cmake
  cmake_minimum_required(VERSION 3.0)
  project(TEST)
  set(TEMP "hello,world")
  file(GLOB SRC_1 ${PROJECT_SOURCE_DIR}/*.cpp)
  # 移除前日志
  message(STATUS "message: ${SRC_1}")
  # 移除 main.cpp
  list(REMOVE_ITEM SRC_1 ${PROJECT_SOURCE_DIR}/main.cpp)
  # 移除后日志
  message(STATUS "message: ${SRC_1}")
  ```

### 1.10 宏定义

在进行程序测试的时候，我们可以在代码中添加一些宏定义，通过这些宏来控制这些代码是否生效，如下所示：

```cmake
#include <stdio.h>
#define NUMBER  3

int main()
{
    int a = 10;
#ifdef DEBUG
    printf("我是一个程序猿, 我不会爬树...\n");
#endif
    for(int i=0; i<NUMBER; ++i)
    {
        printf("hello, GCC!!!\n");
    }
    return 0;
}
```

在程序的第七行对DEBUG宏进行了判断，如果该宏被定义了，那么第八行就会进行日志输出，如果没有定义这个宏，第八行就相当于被注释掉了，因此最终无法看到日志输入出（上述代码中并没有定义这个宏）。

+ 为了让测试更灵活，我们可以不在代码中定义这个宏，而是在测试的时候去把它定义出来，其中一种方式就是**在gcc/g++命令中去指定**，如下：

```cmake
$ gcc test.c -DDEBUG -o app
```

+ 在CMake中我们也可以做类似的事情，对应的命令叫做 **add_definitions**:

```cmake
add_definitions(-D宏名称)
```

```cmake
cmake_minimum_required(VERSION 3.0)
project(TEST)
# 自定义 DEBUG 宏
add_definitions(-DDEBUG)
add_executable(app ./test.c)
```

**一些CMake中常用的宏：**

| 宏                         | 功能                                                         |
| :------------------------- | :----------------------------------------------------------- |
| `PROJECT_SOURCE_DIR`       | 使用cmake命令后紧跟的目录，一般是工程的根目录                |
| `PROJECT_BINARY_DIR`       | 执行cmake命令的目录                                          |
| `CMAKE_CURRENT_SOURCE_DIR` | 当前处理的CMakeLists.txt所在的路径                           |
| `CMAKE_CURRENT_BINARY_DIR` | target 编译目录                                              |
| `EXECUTABLE_OUTPUT_PATH`   | 重新定义目标二进制可执行文件的存放位置                       |
| `LIBRARY_OUTPUT_PATH`      | 重新定义目标链接库文件的存放位置                             |
| `PROJECT_NAME`             | 返回通过PROJECT指令定义的项目名称                            |
| `CMAKE_BINARY_DIR`         | 项目实际构建路径，假设在build目录进行的构建，那么得到的就是这个目录的路径 |

## 2. 嵌套的CMake

如果项目很大，或者项目中有很多的源码目录，在通过CMake管理项目的时候如果只使用一个CMakeLists.txt，那么这个文件相对会比较复杂，有一种化繁为简的方式就是给每个源码目录都添加一个CMakeLists.txt文件（头文件目录不需要），这样每个文件都不会太复杂，而且更灵活，更容易维护。

```cmake
$ tree
.
├── build
├── calc
│   ├── add.cpp
│   ├── CMakeLists.txt
│   ├── div.cpp
│   ├── mult.cpp
│   └── sub.cpp
├── CMakeLists.txt
├── include
│   ├── calc.h
│   └── sort.h
├── sort
│   ├── CMakeLists.txt
│   ├── insert.cpp
│   └── select.cpp
├── test1
│   ├── calc.cpp
│   └── CMakeLists.txt
└── test2
    ├── CMakeLists.txt
    └── sort.cpp

6 directories, 15 files
# include 目录：头文件目录
# calc 目录：目录中的四个源文件对应的加、减、乘、除算法  对应的头文件是include中的calc.h
# sort 目录 ：目录中的两个源文件对应的是插入排序和选择排序算法 对应的头文件是include中的sort.h
# test1 目录：测试目录，对加、减、乘、除算法进行测试
# test2 目录：测试目录，对排序算法进行测试
```

### 2.1 准备工作

众所周知，Linux的目录是树状结构，所以嵌套的 CMake 也是一个树状结构，最顶层的 CMakeLists.txt 是根节点，其次都是子节点。因此，我们需要了解一些关于 CMakeLists.txt 文件变量作用域的一些信息：

+ **根节点**CMakeLists.txt中的变量**全局有效**
+ **父节点**CMakeLists.txt中的变量可以在**子节点中使用**
+ **子节点**CMakeLists.txt中的变量只能在**当前节点中使用**

```cmake
# 添加子目录
add_subdirectory(source_dir [binary_dir] [EXCLUDE_FROM_ALL])

# source_dir：指定了CMakeLists.txt源文件和代码文件的位置，其实就是指定 子目录
# binary_dir：指定了输出文件的路径，一般不需要指定，忽略即可。
# EXCLUDE_FROM_ALL：在子路径下的目标默认不会被包含到父路径的ALL目标里，并且也会被排除在IDE工程文件之外。用户必须显式构建在子路径下的目标。
```

### 2.2 解决问题

在上面的目录中我们要做如下事情：

+ 通过 test1 目录中的测试文件进行计算器相关的测试
+ 通过 test2 目录中的测试文件进行排序相关的测试
+ 现在相当于是要进行模块化测试，对于calc和sort目录中的源文件来说，可以将它们先编译成库文件（可以是静态库也可以是动态库）然后在提供给测试文件使用即可。库文件的本质其实还是代码，只不过是从文本格式变成了二进制格式。

#### 2.2.1 根目录的CMakeLists.txt

```cmake
# 指定CMake版本
cmake_minimum_required(VERSION 3.0)
# 指定项目名称
project(test)
# 定义变量
# 静态库生成的路径
set(LIB_PATH ${CMAKE_CURRENT_SOURCE_DIR}/lib)
# 测试程序生成的路径
set(EXEC_PATH ${CMAKE_CURRENT_SOURCE_DIR}/bin)
# 头文件目录
set(HEAD_PATH ${CMAKE_CURRENT_SOURCE_DIR}/include)
# 静态库的名字
set(CALC_LIB calc)
set(SORT_LIB sort)
# 可执行程序的名字
set(APP_NAME_1 test1)
set(APP_NAME_2 test2)
# 添加子目录
add_subdirectory(calc)
add_subdirectory(sort)
add_subdirectory(test1)
add_subdirectory(test2)

# 定义全局变量和添加子目录。

# 定义的全局变量主要是给子节点使用，目的是为了提高子节点中的CMakeLists.txt文件的可读性和可维护性，避免冗余并降低出差的概率。

# 一共添加了四个子目录，每个子目录中都有一个CMakeLists.txt文件，这样它们的父子关系就被确定下来了。
```

#### 2.2.2 calc 目录

```cmake
cmake_minimum_required(VERSION 3.0)
project(CALCLIB)
#打包源文件
aux_source_directory(./ SRC)
# 包含头文件
include_directories(${HEAD_PATH})
# 设置静态库生成的路径 在生成库文件的时候，这个库可以是静态库也可以是动态库，一般需要根据实际情况来确定。如果生成的库比较大，建议将其制作成动态库。
set(LIBRARY_OUTPUT_PATH ${LIB_PATH})
add_library(${CALC_LIB} STATIC ${SRC})
```

#### 2.2.3 sort 目录

```cmake
cmake_minimum_required(VERSION 3.0)
project(SORTLIB)
# 打包源文件到SRC
aux_source_directory(./ SRC)
#  包含头文件
include_directories(${HEAD_PATH})
# 设置动态库生成的路径
set(LIBRARY_OUTPUT_PATH ${LIB_PATH})
add_library(${SORT_LIB} SHARED ${SRC})
```

#### 2.2.4  test1 目录

```cmake
cmake_minimum_required(VERSION 3.0)
project(CALCTEST)
aux_source_directory(./ SRC)
include_directories(${HEAD_PATH})
# 链接的库路径
link_directories(${LIB_PATH})
# 链接静态库
link_libraries(${CALC_LIB})
# 指定可执行程序的生成路径
set(EXECUTABLE_OUTPUT_PATH ${EXEC_PATH})
# 生成可执行程序
add_executable(${APP_NAME_1} ${SRC})
```

#### 2.2.5 test2 目录

```cmake
cmake_minimum_required(VERSION 3.0)
project(SORTTEST)
aux_source_directory(./ SRC)
include_directories(${HEAD_PATH})
set(EXECUTABLE_OUTPUT_PATH ${EXEC_PATH})
link_directories(${LIB_PATH})
add_executable(${APP_NAME_2} ${SRC})
# 链接动态库
target_link_libraries(${APP_NAME_2} ${SORT_LIB})
```

#### 2.2.6  构建项目

一切准备就绪之后，开始构建项目，进入到**根节点目录的build 目录中**，执行cmake 命令，

```cmake
cmake ..
```

```cmake
make
```

结果应是：

+ 在项目根目录的lib目录中生成了静态库libcalc.a
+ 在项目根目录的lib目录中生成了动态库libsort.so
+ 在项目根目录的bin目录中生成了可执行程序test1
+ 在项目根目录的bin目录中生成了可执行程序test2

在项目中，如果将程序中的某个模块制作成了动态库或者静态库并且在CMakeLists.txt 中指定了库的输出目录，而后其它模块又需要加载这个生成的库文件，此时直接使用就可以了，如果没有指定库的输出路径或者需要直接加载外部提供的库文件，此时就需要使用 link_directories 将库文件路径指定出来。

## 3. 流程控制

...



参考：苏丙榅  链接：[CMake上]( https://subingwen.cn/cmake/CMake-primer/#2-1-1-%E6%B3%A8%E9%87%8A%E8%A1%8C)    [CMake下](https://subingwen.cn/cmake/CMake-advanced/#1-2-6-%E6%9E%84%E5%BB%BA%E9%A1%B9%E7%9B%AE)







