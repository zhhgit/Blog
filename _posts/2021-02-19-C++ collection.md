---
layout: post
title: "C++知识整理"
description: C++知识整理
modified: 2021-02-19
category: C++
tags: [C++]
---

# 一、C++基础

1.面向过程

(1)编译
    
    # 编译生成hello.exe，Linux下是hello.out
    g++ ./test.cpp -o hello

(2)数据类型

    char	1 个字节	-128 到 127 或者 0 到 255
    unsigned char	1 个字节	0 到 255
    signed char	1 个字节	-128 到 127
    int	4 个字节	-2147483648 到 2147483647
    unsigned int	4 个字节	0 到 4294967295
    signed int	4 个字节	-2147483648 到 2147483647
    short int	2 个字节	-32768 到 32767
    unsigned short int	2 个字节	0 到 65,535
    signed short int	2 个字节	-32768 到 32767
    long int	8 个字节	-9,223,372,036,854,775,808 到 9,223,372,036,854,775,807，long int在windows64位占4字节，在Linux64位占8字节。windows想要8字节可以使用long long。
    signed long int	8 个字节	-9,223,372,036,854,775,808 到 9,223,372,036,854,775,807
    unsigned long int	8 个字节	0 到 18,446,744,073,709,551,615
    float	4 个字节	精度型占4个字节（32位）内存空间，+/- 3.4e +/- 38 (~7 个数字)
    double	8 个字节	双精度型占8 个字节（64位）内存空间，+/- 1.7e +/- 308 (~15 个数字)
    long double	16 个字节	长双精度型 16 个字节（128位）内存空间，可提供18-19位有效数字。
    wchar_t	2 或 4 个字节	1 个宽字符，其实是typedef short int wchar_t;

(3)类型最大最小值

    #include<limits>
    // 最大、最小值，可以替换<>中的类型
    (numeric_limits<int>::max)();
    (numeric_limits<int>::min)();
    
(4)枚举类型

    # 这里red, greeen, blue的值其实是0,1,2
    enum color { red, green, blue } c;
    c = blue;
    
(5)声明、定义、初始化

    #include <iostream>
    using namespace std;
     
    // 可以使用extern关键字在任何地方声明一个变量。虽然可以在C++程序中多次声明一个变量，但变量只能在某个文件、函数或代码块中被定义一次。 
    // 变量声明
    extern int a, b;
    extern int c;
    extern float f;
    // 全局变量声明
    int g;
    
    // 函数声明
    int func();
      
    int main ()
    {
      // 变量定义
      int a, b;
      int c;
      float f;
     
      // 实际初始化
      a = 10;
      b = 20;
      c = a + b;
      g = a + b;
     
      cout << c << endl ;
      cout << g << endl;
     
      f = 70.0/3.0;
      cout << f << endl ;
      
      // 函数调用
      int i = func();
     
      return 0;
    }
    
    // 函数定义
    int func()
    {
        return 0;
    }
  
(6)常量

    // 方式一：使用#define

    #include <iostream>
    using namespace std;
     
    #define LENGTH 10   
    #define WIDTH  5
    #define NEWLINE '\n'
     
    int main()
    {
       int area;  
       area = LENGTH * WIDTH;
       cout << area;
       cout << NEWLINE;
       return 0;
    }
    
    // 方式二：使用const
    
    #include <iostream>
    using namespace std;
     
    int main()
    {
       const int  LENGTH = 10;
       const int  WIDTH  = 5;
       const char NEWLINE = '\n';
       int area;  
       
       area = LENGTH * WIDTH;
       cout << area;
       cout << NEWLINE;
       return 0;
    }
    
(7)volatile修饰符告诉编译器不需要优化volatile声明的变量，让程序可以直接从内存中读取变量。对于一般的变量编译器会对变量进行优化，将内存中的变量值放在寄存器中以加快读写效率。

(8)存储类

static：修饰局部变量可以在函数多次调用保持局部变量的值。修饰全局变量时，会使变量的作用域限制在声明它的文件内。用在类数据成员上时，会导致仅有一个该成员的副本被类的所有对象共享。

    #include <iostream>
     
    // 函数声明 
    void func(void);
     
    static int count = 10; /* 全局变量 */
     
    int main()
    {
        while(count--)
        {
           func();
        }
        return 0;
    }
    // 函数定义
    void func( void )
    {
        static int i = 5; // 局部静态变量
        i++;
        std::cout << "变量 i 为 " << i ;
        std::cout << " , 变量 count 为 " << count << std::endl;
    }
 
extern：当有多个文件且定义了一个可以在其他文件中使用的全局变量或函数（默认是能够在）时，可以在其他文件中使用extern来得到已定义的变量或函数的引用。可以这么理解，extern是用来在另一个文件中声明一个全局变量或函数。

    // main.cpp为
    #include <iostream>
     
    int count ;
    extern void write_extern();
     
    int main()
    {
       count = 5;
       write_extern();
    }
    
    // support.cpp为
    #include <iostream>
     
    extern int count;
     
    void write_extern(void)
    {
       std::cout << "Count is " << count << std::endl;
    }
    
thread_local：声明的变量仅可在它在其上创建的线程上访问。 变量在创建线程时创建，并在销毁线程时销毁。 每个线程都有其自己的变量副本。可以将thread_local仅应用于数据声明和定义，不能用于函数声明或定义。
    
(9)三种函数调用方式

    // 传值调用，该方法把参数的实际值赋值给函数的形式参数。在这种情况下，修改函数内的形式参数对实际参数没有影响。
    swap(int x, int y);
    // 指针调用，该方法把参数的地址赋值给形式参数。在函数内，该地址用于访问调用中要用到的实际参数。这意味着，修改形式参数会影响实际参数。
    swap(int *a, int *b);
    // 引用调用，该方法把参数的引用赋值给形式参数。在函数内，该引用用于访问调用中要用到的实际参数。这意味着，修改形式参数会影响实际参数。
    swap(int &a, int &b);

(10)随机数

    #include <iostream>
    #include <ctime>
    #include <cstdlib>    
    using namespace std;
     
    int main ()
    {
       int i,j;
     
       // 设置种子
       srand( (unsigned)time( NULL ) );
     
       /* 生成 10 个随机数 */
       for( i = 0; i < 10; i++ )
       {
          // 生成实际的随机数
          j = rand();
          cout <<"随机数： " << j << endl;
       }
       return 0;
    }

2.数组

(1)初始化

    // 注意与Java的差别
    int nums[] = {1,2,3};

(2)获取长度

    int len = sizeof(nums) / sizeof(nums[0]);

(3)指向数组的指针

    #include <iostream>
    using namespace std;
     
    int main ()
    {
       // 带有 5 个元素的双精度浮点型数组
       double runoobAarray[5] = {1000.0, 2.0, 3.4, 17.0, 50.0};
       double *p;
     
       p = runoobAarray;
     
       for ( int i = 0; i < 5; i++ )
       {
           cout << *(p + i) << endl;
           cout << *(runoobAarray + i) << endl;
       }
       return 0;
    }
    
(4)传递数组给函数有如下形式，数组作为参数传递进函数时会退化为指针，获取长度错误。

    int myfunc(int nums[]) {}
    int myfunc(int *myfunc) {}
    
(5)从函数返回数组

    #include <iostream>
    #include <cstdlib>
    #include <ctime>
     
    using namespace std;
     
    // 要生成和返回随机数的函数
    int * getRandom( )
    {
      static int  r[10];
     
      // 设置种子
      srand( (unsigned)time( NULL ) );
      for (int i = 0; i < 10; ++i)
      {
        r[i] = rand();
        cout << r[i] << endl;
      }
     
      return r;
    }
     
    // 要调用上面定义函数的主函数
    int main ()
    {
       // 一个指向整数的指针
       int *p;
     
       p = getRandom();
       for ( int i = 0; i < 10; i++ )
       {
           cout << "*(p + " << i << ") : ";
           cout << *(p + i) << endl;
       }
     
       return 0;
    }

3.字符串，有两种形式，C风格字符串及C++ 引入的string类类型

    // C风格字符串
    
    #include <iostream>
    #include <cstring>
    using namespace std;
     
    int main ()
    {
       char str1[13] = "runoob";
       char str2[13] = "google";
       char str3[13];
       int  len ;
     
       // 复制 str1 到 str3
       strcpy( str3, str1);
       cout << "strcpy( str3, str1) : " << str3 << endl;
     
       // 连接 str1 和 str2
       strcat( str1, str2);
       cout << "strcat( str1, str2): " << str1 << endl;
     
       // 连接后，str1 的总长度
       len = strlen(str1);
       cout << "strlen(str1) : " << len << endl;
     
       return 0;
    }

4.指针

(1)this为指向当前对象的指针
    
    this->method()
    this->filed

(2)使用指针时必须对其初始化。

(3)是否可修改

    int x;
    int * p1 = &x; // 指针可以被修改，值也可以被修改
    const int * p2 = &x; // 指针可以被修改，值不可以被修改（const int）
    int * const p3 = &x; // 指针不可以被修改（* const），值可以被修改
    const int * const p4 = &x; // 指针不可以被修改，值也不可以被修改
    
(4)函数指针与指针函数

    // addition是指针函数，一个返回类型是指针的函数
    int* addition(int a, int b) {
        int* sum = new int(a + b);
        return sum;
    }
    int subtraction(int a, int b) {
        return a - b;
    }
    int operation(int x, int y, int (*func)(int, int)) {
        return (*func)(x,y);
    }
    // minus是函数指针，指向函数的指针
    int (*minus)(int, int) = subtraction;
    int* m = addition(1, 2);
    int n = operation(3, *m, minus);

(5)指针运算符&和*

    #include <iostream>
    using namespace std;
  
    int main ()
    {
       int  var;
       int  *ptr;
       int  val;
    
       var = 3000;
    
       // 获取 var 的地址
       ptr = &var;
    
       // 获取 ptr 的值
       val = *ptr;
       cout << "Value of var :" << var << endl;
       cout << "Value of ptr :" << ptr << endl;
       cout << "Value of val :" << val << endl;
    
       return 0;
    }
    
(6)NULL指针

在变量声明的时候，如果没有确切的地址可以赋值，为指针变量赋一个NULL值是一个良好的编程习惯。赋为NULL值的指针被称为空指针。NULL指针是一个定义在标准库中的值为零的常量。  

    nt  *ptr = NULL;  

(7)把指针运算符*应用到数组名上是完全可以的，但修改数组名的值是非法的，例如nums++。这是因为数组名是一个指向数组开头的常量，即常量指针，不能作为左值。

(8)指针数组

    #include <iostream>
     
    using namespace std;
    const int MAX = 3;
     
    int main ()
    {
       int  var[MAX] = {10, 100, 200};
       int *ptr[MAX];
     
       for (int i = 0; i < MAX; i++)
       {
          ptr[i] = &var[i]; // 赋值为整数的地址
       }
       for (int i = 0; i < MAX; i++)
       {
          cout << "Value of var[" << i << "] = ";
          cout << *ptr[i] << endl;
       }
       return 0;
    }
    
(9)指向指针的指针

    int **var;
    
(10)new操作会返回指针。

5.引用
  
(1)引用与指针的不同：不存在空引用。引用必须连接到一块合法的内存。一旦引用被初始化为一个对象，就不能被指向到另一个对象。指针可以在任何时候指向到另一个对象。引用必须在创建时被初始化。指针可以在任何时间被初始化。

(2)引用作为参数

    void swap(int& x, int& y)
    {
       int temp;
       temp = x; /* 保存地址 x 的值 */
       x = y;    /* 把 y 赋值给 x */
       y = temp; /* 把 x 赋值给 y  */      
       return;
    }
  
(3)引用作为返回值

    int& func() {
       int q;
       //! return q; // 在编译时发生错误，当返回一个引用时，要注意被引用的对象不能超出作用域。所以返回一个对局部变量的引用是不合法的，但是，可以返回一个对静态变量的引用。
       static int x;
       return x;     // 安全，x 在函数作用域外依然是有效的
    }

6.日期与时间

    #include <iostream>
    #include <ctime>
    using namespace std;
     
    int main( )
    {
       // 基于当前系统的当前日期/时间
       time_t now = time(0); 
       cout << "1970 到目前经过秒数:" << now << endl;
       tm *ltm = localtime(&now);
       // 输出 tm 结构的各个组成部分
       cout << "年: "<< 1900 + ltm->tm_year << endl;
       cout << "月: "<< 1 + ltm->tm_mon<< endl;
       cout << "日: "<<  ltm->tm_mday << endl;
       cout << "时间: "<< ltm->tm_hour << ":";
       cout << ltm->tm_min << ":";
       cout << ltm->tm_sec << endl;
    }

7.IO

(1)bool类型输出为true/false字符串，cout << boolalpha << var << endl;

(2)setw()函数只对紧接着的输出产生作用。

    #include <iomanip>
    // 开头设置间距为 14，后面 runoob 字符数为6，前面补充8个空格 
    cout << setw(14) << "runoob" << endl;

(3)输出cout/cerr/clog，输入cin

(4)文件处理

打开文件

    /* 模式标志
    * ios::app	追加模式。所有写入都追加到文件末尾。
    * ios::ate	文件打开后定位到文件末尾。
    * ios::in	打开文件用于读取。
    * ios::out	打开文件用于写入。
    * ios::trunc	如果该文件已经存在，其内容将在打开文件之前被截断，即把文件长度设为0。
    */
    void open(const char *filename, ios::openmode mode);
    // 两种或两种以上的模式结合使用。例如想要以写入模式打开文件，并希望截断文件，以防文件已存在
    ofstream outfile;
    outfile.open("file.dat", ios::out | ios::trunc );
    
关闭文件

    void close();

写入与读取

    #include <fstream>
    #include <iostream>
    using namespace std;
     
    int main ()
    {
        
       char data[100];
     
       // 以写模式打开文件
       ofstream outfile;
       outfile.open("afile.dat");
     
       cout << "Writing to the file" << endl;
       cout << "Enter your name: "; 
       cin.getline(data, 100);
     
       // 向文件写入用户输入的数据
       outfile << data << endl;
     
       cout << "Enter your age: "; 
       cin >> data;
       // ignore() 函数会忽略掉之前读语句留下的多余字符。
       cin.ignore();
       
       // 再次向文件写入用户输入的数据
       outfile << data << endl;
     
       // 关闭打开的文件
       outfile.close();
     
       // 以读模式打开文件
       ifstream infile; 
       infile.open("afile.dat"); 
     
       cout << "Reading from the file" << endl; 
       infile >> data; 
     
       // 在屏幕上写入数据
       cout << data << endl;
       
       // 再次从文件读取数据，并显示它
       infile >> data; 
       cout << data << endl; 
     
       // 关闭打开的文件
       infile.close();
     
       return 0;
    }
    
文件位置指针：istream和ostream 都提供了用于重新定位文件位置指针的成员函数。这些成员函数包括关于istream的seekg（"seek get"）和关于ostream的seekp（"seek put"）。
seekg和seekp的参数通常是一个长整型。第二个参数可以用于指定查找方向。
       
       // 定位到 fileObject 的第 n 个字节（假设是 ios::beg）
       fileObject.seekg( n );
        
       // 把文件的读指针从 fileObject 当前位置向后移 n 个字节
       fileObject.seekg( n, ios::cur );
        
       // 把文件的读指针从 fileObject 末尾往回移 n 个字节
       fileObject.seekg( n, ios::end );
        
       // 定位到 fileObject 的末尾
       fileObject.seekg( 0, ios::end );
    

8.结构体

    struct ListNode {
        int val;
        ListNode *next;
        ListNode(int x) : val(x), next(nullptr) { }
    };
    
9.异常

    #include <iostream>
    #include <exception>
    using namespace std;
     
    struct MyException : public exception
    {
      const char * what () const throw ()
      {
        return "C++ Exception";
      }
    };
    
    // 或者
    class MyException : public exception
    {
    public:
        MyException() : message("Error."){}
        MyException(string str) : message("Error : " + str) {}
        ~MyException() throw () {
        }
    
        virtual const char* what() const throw () {
            return message.c_str();
        }
    
    private:
        string message;
    };
     
    int main()
    {
      try
      {
        throw MyException();
      }
      catch(MyException& e)
      {
        std::cout << "MyException caught" << std::endl;
        std::cout << e.what() << std::endl;
      }
      catch(std::exception& e)
      {
        //其他的错误
      }
    }
    
10.动态内存

基本数据类型

    #include <iostream>
    using namespace std;
     
    int main ()
    {
       double* pvalue  = NULL; // 初始化为 null 的指针
       pvalue  = new double;   // 为变量请求内存
     
       *pvalue = 29494.99;     // 在分配的地址存储值
       cout << "Value of pvalue : " << *pvalue << endl;
     
       delete pvalue;         // 释放内存
     
       return 0;
    }
    
数组
    
    #include <iostream>
    using namespace std;
     
    int main()
    {
        int **p;   
        int i,j;   //p[4][8] 
        //开始分配4行8列的二维数据   
        p = new int *[4];
        for(i=0;i<4;i++){
            p[i]=new int [8];
        }
     
        for(i=0; i<4; i++){
            for(j=0; j<8; j++){
                p[i][j] = j*i;
            }
        }   
        //打印数据   
        for(i=0; i<4; i++){
            for(j=0; j<8; j++)     
            {   
                if(j==0) cout<<endl;   
                cout<<p[i][j]<<"\t";   
            }
        }   
        //开始释放申请的堆   
        for(i=0; i<4; i++){
            delete [] p[i];   
        }
        delete [] p;   
        return 0;
    }
    
对象数组

    #include <iostream>
    using namespace std;
     
    class Box
    {
       public:
          Box() { 
             cout << "调用构造函数！" <<endl; 
          }
          ~Box() { 
             cout << "调用析构函数！" <<endl; 
          }
    };
     
    int main( )
    {
       Box* myBoxArray = new Box[4];
     
       delete [] myBoxArray; // 删除数组
       return 0;
    }
    
11.命名空间

    #include <iostream>
    using namespace std;
     
    // 第一个命名空间
    namespace first_space{
       void func(){
          cout << "Inside first_space" << endl;
       }
    }
    // 第二个命名空间
    namespace second_space{
       void func(){
          cout << "Inside second_space" << endl;
       }
    }
    int main ()
    {
     
       // 调用第一个命名空间中的函数
       first_space::func();
       
       // 调用第二个命名空间中的函数
       second_space::func(); 
     
       return 0;
    }
    
12.模板

函数模板

    #include <iostream>
    #include <string>
     
    using namespace std;
     
    template <typename T> 
    inline T const& Max (T const& a, T const& b) 
    { 
        return a < b ? b:a; 
    } 
    int main ()
    {
        int i = 39;
        int j = 20;
        cout << "Max(i, j): " << Max(i, j) << endl; 
     
        double f1 = 13.5; 
        double f2 = 20.7; 
        cout << "Max(f1, f2): " << Max(f1, f2) << endl; 
     
        string s1 = "Hello"; 
        string s2 = "World"; 
        cout << "Max(s1, s2): " << Max(s1, s2) << endl; 
     
       return 0;
    }
    
类模板

    #include <iostream>
    #include <vector>
    #include <cstdlib>
    #include <string>
    #include <stdexcept>
     
    using namespace std;
     
    template <class T> 
    class Stack { 
      private: 
        vector<T> elems;     // 元素 
     
      public: 
        void push(T const&);  // 入栈
        void pop();               // 出栈
        T top() const;            // 返回栈顶元素
        bool empty() const{       // 如果为空则返回真。
            return elems.empty(); 
        } 
    }; 
     
    template <class T>
    void Stack<T>::push (T const& elem) 
    { 
        // 追加传入元素的副本
        elems.push_back(elem);    
    } 
     
    template <class T>
    void Stack<T>::pop () 
    { 
        if (elems.empty()) { 
            throw out_of_range("Stack<>::pop(): empty stack"); 
        }
        // 删除最后一个元素
        elems.pop_back();         
    } 
     
    template <class T>
    T Stack<T>::top () const 
    { 
        if (elems.empty()) { 
            throw out_of_range("Stack<>::top(): empty stack"); 
        }
        // 返回最后一个元素的副本 
        return elems.back();      
    } 
     
    int main() 
    { 
        try { 
            Stack<int>         intStack;  // int 类型的栈 
            Stack<string> stringStack;    // string 类型的栈 
     
            // 操作 int 类型的栈 
            intStack.push(7); 
            cout << intStack.top() <<endl; 
     
            // 操作 string 类型的栈 
            stringStack.push("hello"); 
            cout << stringStack.top() << std::endl; 
            stringStack.pop(); 
            stringStack.pop(); 
        } 
        catch (exception const& ex) { 
            cerr << "Exception: " << ex.what() <<endl; 
            return -1;
        } 
    }

13.预处理器

    #define PI 3.14159
    
    // 参数宏
    #define MIN(a,b) (a<b ? a : b)
    
    // 如果在指令#ifdef DEBUG 之前已经定义了符号常量DEBUG，则会对程序中的cerr语句进行编译。
    #ifdef DEBUG
       cerr <<"Variable x = " << x << endl;
    #endif
    
    // #x代表"x"，加引号
    #define MKSTR( x ) #x
    
    // ##代表拼接，即x ## y为xy
    #define CONCAT( x, y )  x ## y
    
    // 预定义宏
    __LINE__	这会在程序编译时包含当前行号。
    __FILE__	这会在程序编译时包含当前文件名。
    __DATE__	这会包含一个形式为 month/day/year 的字符串，它表示把源文件转换为目标代码的日期。
    __TIME__	这会包含一个形式为 hour:minute:second 的字符串，它表示程序被编译的时间。
    
14.信号的处理

    #include <iostream>
    #include <csignal>
    #include <unistd.h>
     
    using namespace std;
     
    // 信号处理程序
    void signalHandler( int signum )
    {
        cout << "Interrupt signal (" << signum << ") received.\n";
     
        // 清理并关闭
        // 终止程序 
     
       exit(signum);  
     
    }
     
    int main ()
    {
        int i = 0;
        // 注册信号 SIGINT 和信号处理程序
        signal(SIGINT, signalHandler);  
     
        while(++i){
           cout << "Going to sleep...." << endl;
           if( i == 3 ){
              // 生成信号
              raise( SIGINT);
           }
           sleep(1);
        }
     
        return 0;
    }

15.多线程

编译时使用

    g++ test.cpp -lpthread -o test

向线程传递参数

    #include <iostream>
    #include <cstdlib>
    #include <pthread.h>
     
    using namespace std;
     
    #define NUM_THREADS  5
     
    struct thread_data{
       int  thread_id;
       char *message;
    };
     
    void *PrintHello(void *threadarg)
    {
       struct thread_data *my_data;
       // 对传入的参数进行强制类型转换，由无类型指针变为thread_data指针，然后再读取
       my_data = (struct thread_data *) threadarg;
     
       cout << "Thread ID : " << my_data->thread_id ;
       cout << " Message : " << my_data->message << endl;
     
       pthread_exit(NULL);
    }
     
    int main ()
    {
       pthread_t threads[NUM_THREADS];
       struct thread_data td[NUM_THREADS];
       int rc;
       int i;
     
       for( i=0; i < NUM_THREADS; i++ ){
          cout <<"main() : creating thread, " << i << endl;
          td[i].thread_id = i;
          td[i].message = (char*)"This is message";
          
          /* pthread_create (thread, attr, start_routine, arg) 
          * thread	指向线程标识符指针。
          * attr	一个不透明的属性对象，可以被用来设置线程属性。您可以指定线程属性对象，也可以使用默认值 NULL。
          * start_routine	线程运行函数起始地址，一旦线程被创建就会执行。
          * arg	运行函数的参数。它必须通过把引用作为指针强制转换为 void 类型进行传递。如果没有传递参数，则使用 NULL。
          */
          // 传入的时候必须强制转换为void* 类型，即无类型指针
          rc = pthread_create(&threads[i], NULL, PrintHello, (void *)&td[i]);
          if (rc){
             cout << "Error:unable to create thread," << rc << endl;
             exit(-1);
          }
       }
       // 
       // 如果 main() 是在它所创建的线程之前结束，并通过 pthread_exit() 退出，那么其他线程将继续执行。否则，它们将在 main() 结束时自动被终止。这里等各个线程退出后，进程才结束，否则进程强制结束了，线程可能还没反应过来。
       pthread_exit(NULL);
    }
    
pthread_join使一个线程等待另一个线程结束。代码中如果没有pthread_join；主线程会很快结束从而使整个进程结束，从而使创建的线程没有机会开始执行就结束了。
加入pthread_join后，主线程会一直等待直到等待的线程结束自己才结束，使创建的线程有机会执行。

    #include <iostream>
    #include <cstdlib>
    #include <pthread.h>
    #include <unistd.h>
     
    using namespace std;
     
    #define NUM_THREADS     5
     
    void *wait(void *t)
    {
       int i;
       long tid;
     
       tid = (long)t;
     
       sleep(1);
       cout << "Sleeping in thread " << endl;
       cout << "Thread with id : " << tid << "  ...exiting " << endl;
       pthread_exit(NULL);
    }
     
    int main ()
    {
       int rc;
       int i;
       pthread_t threads[NUM_THREADS];
       pthread_attr_t attr;
       void *status;
     
       // 初始化并设置线程为可连接的（joinable）
       pthread_attr_init(&attr);
       pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_JOINABLE);
     
       for( i=0; i < NUM_THREADS; i++ ){
          cout << "main() : creating thread, " << i << endl;
          rc = pthread_create(&threads[i], NULL, wait, (void *)&i );
          if (rc){
             cout << "Error:unable to create thread," << rc << endl;
             exit(-1);
          }
       }
     
       // 删除属性，并等待其他线程
       pthread_attr_destroy(&attr);
       for( i=0; i < NUM_THREADS; i++ ){
          rc = pthread_join(threads[i], &status);
          if (rc){
             cout << "Error:unable to join," << rc << endl;
             exit(-1);
          }
          cout << "Main: completed thread id :" << i ;
          cout << "  exiting with status :" << status << endl;
       }
     
       cout << "Main: program exiting." << endl;
       pthread_exit(NULL);
    }

# 二、面向对象

1.类的定义

(1)注意类定义后面的分号。
(2)成员和类的默认访问修饰符是private。

    #include <iostream>
    using namespace std;
     
    class Box
    {
       public:
          double length;   // 长度
          double breadth;  // 宽度
          double height;   // 高度
          // 成员函数声明
          double get(void);
          void set( double len, double bre, double hei );
    };
    // 成员函数定义
    double Box::get(void)
    {
        return length * breadth * height;
    }
     
    void Box::set( double len, double bre, double hei)
    {
        length = len;
        breadth = bre;
        height = hei;
    }
    
2.构造函数与析构函数

(1)构造函数

    Line::Line( double len)
    {
        cout << "Object is being created, length = " << len << endl;
        length = len;
    }
    // 使用初始化列表来初始化字段
    Line::Line( double len): length(len)
    {
        cout << "Object is being created, length = " << len << endl;
    }
    
(2)析构函数

    Line::~Line(void)
    {
        cout << "Object is being deleted" << endl;
    }

3.拷贝构造函数是一种特殊的构造函数，它在创建对象时，是使用同一类中之前创建的对象来初始化新创建的对象。
拷贝构造函数通常用于：(1)通过使用另一个同类型的对象来初始化新创建的对象。(2)复制对象，把它作为参数传递给函数。(3)复制对象，并从函数返回这个对象。
如果在类中没有定义拷贝构造函数，编译器会自行定义一个。如果类带有指针变量，并有动态内存分配，则它必须有一个拷贝构造函数。拷贝构造函数的最常见形式如下：

    classname (const classname &obj) {
       // 构造函数的主体
    }
    
例如

    #include <iostream>
    using namespace std;
     
    class Line
    {
       public:
          int getLength( void );
          Line( int len );             // 简单的构造函数
          Line( const Line &obj);      // 拷贝构造函数
          ~Line();                     // 析构函数
     
       private:
          int *ptr;
    };
     
    // 成员函数定义，包括构造函数
    Line::Line(int len)
    {
        cout << "调用构造函数" << endl;
        // 为指针分配内存
        ptr = new int;
        *ptr = len;
    }
     
    Line::Line(const Line &obj)
    {
        cout << "调用拷贝构造函数并为指针 ptr 分配内存" << endl;
        ptr = new int;
        *ptr = *obj.ptr; // 拷贝值
    }
     
    Line::~Line(void)
    {
        cout << "释放内存" << endl;
        delete ptr;
    }
    int Line::getLength( void )
    {
        return *ptr;
    }
     
    void display(Line obj)
    {
       cout << "line 大小 : " << obj.getLength() <<endl;
    }
     
    // 程序的主函数
    int main( )
    {
       Line line1(10);
     
       Line line2 = line1; // 这里也调用了拷贝构造函数
     
       display(line1);
       display(line2);
     
       return 0;
    }
    
4.友元函数

    #include <iostream>
     
    using namespace std;
     
    class Box
    {
       double width;
    public:
       friend void printWidth( Box box );
       void setWidth( double wid );
    };
     
    // 成员函数定义
    void Box::setWidth( double wid )
    {
        width = wid;
    }
     
    // 请注意：printWidth() 不是任何类的成员函数
    void printWidth( Box box )
    {
       /* 因为 printWidth() 是 Box 的友元，它可以直接访问该类的任何成员 */
       cout << "Width of box : " << box.width <<endl;
    }
     
    // 程序的主函数
    int main( )
    {
       Box box;
     
       // 使用成员函数设置宽度
       box.setWidth(10.0);
       
       // 使用友元函数输出宽度
       printWidth( box );
     
       return 0;
    }
    
友元类

    // 声明类ClassTwo的所有成员函数作为类ClassOne的友元，需要在类ClassOne的定义中放置如下声明：
    friend class ClassTwo;
    
5.内联函数通常与类一起使用。如果一个函数是内联的，那么在编译时，编译器会把该函数的代码副本放置在每个调用该函数的地方。对内联函数进行任何修改，都需要重新编译函数的所有客户端，因为编译器需要重新更换一次所有的代码，否则将会继续使用旧的函数。
如果想把一个函数定义为内联函数，则需要在函数名前面放置关键字inline，在调用函数之前需要对函数进行定义。如果已定义的函数多于一行，编译器会忽略inline限定符。
在类定义中的定义的函数都是内联函数，即使没有使用inline说明符。
    
    #include <iostream>
    using namespace std;
    
    inline int Max(int x, int y)
    {
       return (x > y)? x : y;
    }

6.静态成员

初始化类 Box 的静态成员

    int Box::objectCount = 0;

静态成员方法：
(a)头文件中声明static void printNumVector(vector<int> &nums);
(b)cpp文件中定义，void PrintUtil::printNumVector(vector<int> &nums){} 
(c)调用PrintUtil::printNumVector(nums);

7.对象创建两种方式：

(1)ListNode node;或者ListNode node(4);在栈内存创建，退出代码块{}后会释放内存。
(2)ListNode node = new ListNode(4);在堆内存创建，一般需要delete手动释放内存。

8.继承

(1)类的继承

    // 访问修饰符 access-specifier是public、protected或private其中的一个，默认为private，正常是public
    class derived-class: access-specifier base-class
    
(2)多继承

    class <派生类名>:<继承方式1><基类名1>,<继承方式2><基类名2>,…
    {
    <派生类类体>
    };
    
9.运算符重载

    #include <iostream>
    using namespace std;
     
    class Box
    {
       public:
     
          double getVolume(void)
          {
             return length * breadth * height;
          }
          void setLength( double len )
          {
              length = len;
          }
     
          void setBreadth( double bre )
          {
              breadth = bre;
          }
     
          void setHeight( double hei )
          {
              height = hei;
          }
          // 重载 + 运算符，用于把两个 Box 对象相加
          Box operator+(const Box& b)
          {
             Box box;
             box.length = this->length + b.length;
             box.breadth = this->breadth + b.breadth;
             box.height = this->height + b.height;
             return box;
          }
       private:
          double length;      // 长度
          double breadth;     // 宽度
          double height;      // 高度
    };
    
10.多态

    #include <iostream> 
    using namespace std;
     
    class Shape {
       protected:
          int width, height;
       public:
          Shape( int a=0, int b=0)
          {
             width = a;
             height = b;
          }
          int area()
          {
             cout << "Parent class area :" <<endl;
             return 0;
          }
    };
    class Rectangle: public Shape{
       public:
          Rectangle( int a=0, int b=0):Shape(a, b) { }
          int area ()
          { 
             cout << "Rectangle class area :" <<endl;
             return (width * height); 
          }
    };
    class Triangle: public Shape{
       public:
          Triangle( int a=0, int b=0):Shape(a, b) { }
          int area ()
          { 
             cout << "Triangle class area :" <<endl;
             return (width * height / 2); 
          }
    };
    // 程序的主函数
    int main( )
    {
       Shape *shape;
       Rectangle rec(10,7);
       Triangle  tri(10,5);
     
       // 存储矩形的地址
       shape = &rec;
       // 调用矩形的求面积函数 area
       shape->area();
     
       // 存储三角形的地址
       shape = &tri;
       // 调用三角形的求面积函数 area
       shape->area();
       
       return 0;
    }

导致错误输出的原因是，调用函数 area() 被编译器设置为基类中的版本，这就是所谓的静态多态，或静态链接 - 函数调用在程序执行前就准备好了。有时候这也被称为早绑定，因为 area() 函数在程序编译期间就已经设置好了。

    class Shape {
       protected:
          int width, height;
       public:
          Shape( int a=0, int b=0)
          {
             width = a;
             height = b;
          }
          virtual int area()
          {
             cout << "Parent class area :" <<endl;
             return 0;
          }
    };

area()函数前添加virtual，虚函数是在基类中使用关键字virtual 声明的函数。在派生类中重新定义基类中定义的虚函数时，会告诉编译器不要静态链接到该函数。此时，编译器看的是指针的内容，而不是它的类型。
因此，由于tri和rec类的对象的地址存储在*shape中，所以会调用各自的area()函数。实现了多态。这种操作被称为动态链接，或后期绑定。

11.纯虚函数，可能想要在基类中定义虚函数，以便在派生类中重新定义该函数更好地适用于对象，但是在基类中又不能对虚函数给出有意义的实现，这个时候就会用到纯虚函数。=0告诉编译器，函数没有主体是纯虚函数。
如果类中至少有一个函数被声明为纯虚函数，则这个类就是抽象类。
    
    class Shape {
       protected:
          int width, height;
       public:
          Shape( int a=0, int b=0)
          {
             width = a;
             height = b;
          }
          // pure virtual function
          virtual int area() = 0;
    };

# 三、STL

1.比较最大最小值

    #include<algorithm>
    max(a,b); 
    min(a,b);

2.字符串

(1)#include <string>
(2)获取长度s.size()
(3)遍历：a).s[i]；b).for(auto c:str)
(4)构造：string("str"),string(1,'c')
(5)拼接：str1.append("aaa");
(6)数字转字符串：to_string(5);
(7)查找包含字符位置：s.find_first_not_of(" ");s.find_last_not_of(" ");查找不到就返回-1。

3.stack：
(1)#include<stack>
(2)初始化：stack<char> stack1;
(3)入栈：stack1.push(ele)
(4)出栈：stack1.pop()
(5)栈顶元素：stack1.top()

4.vector：
(1)头部应该有#include<vector>及using namespace std;
(2)初始化：vector<int> ret {1,2,3};
(3)获取长度：nums.size()
(4)获取某元素：nums[3]
(5)插入，末尾插入ret.push_back(1);
(6)排序，sort(nums.begin(), nums.end());其中begin()、end()分别返回容器中起始、结束元素的迭代器。
(7)累加，accumulate(nums.begin(), nums.end(),init);返回将范围中的所有值累加[first,last)到init的结果。
(8)判空，nums.empty();

5.unordered_map：
(1)初始化；unordered_map<int,int> map;
(2)查找：map.find("key") != map.end()，end()方法返回迭代器指向尾后，如果不等即在map中存在为key的键。
(3)插入：map.insert({"key","value"});
(4)获取：map.at("key");

# 四、参考

1.[菜鸟C++教程](https://www.runoob.com/cplusplus/cpp-tutorial.html)