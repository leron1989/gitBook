# Object-C

## 基础概览

### Hello World

```objc
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        // insert code here...
        NSLog(@"Hello, World!");
    }
    return 0;
}
```

> 1. 首先#import是一个导入命令，类似于C语言的#include命令，但是相对于#include而言，#import有效的处理了重复导入的问题，在C语言中也提到过如何通过条件编译解决重复导入，而在ObjC中你不需要处理了，#import已经自动进行了重复处理；与#include类似，导入系统类库使用<>，导入自定义类库使用””；
> 2. Foundation.h是Foundation框架中的头文件，这是ObjC中的一个基础类库，基本上后面我们用到的所有ObjC的代码都需要引入这个类库；
> 3. @autoreleasepool是ObjC的关键字，它的作用是对包含在这个关键字后面大包括内的程序会自动进行内存回收，不需要开发人员手动释放无用的对象；当然表面上看起来这有点类似于C#和Java的内存垃圾回收机制，但是事实上他们的原理完全不同，以后再做具体解释。
> 4. NSLog是标准输出函数，类似于C语言中的printf()函数，但是它会自动换行，当然它同样支持格式输出（例如%i表示输出整形数据，%f输出浮点型数据），这个函数在Fundation.h中声明；
> 5. @”Hello,World!”,是一个字符串常量，在ObjC中字符串前需要使用@符号；

### 语法特点

1. ObjC中没有命名空间(C#)或包（Java）的概念，那也就是说在**同一个应用中不能同时存在两个完全相同的类名，通常情况下我们通过前缀加以区分**，例如在ObjC中的NSString、NSLog中的NS就是前缀。

   > NS其实是NeXTSTEP缩写，是乔布斯离开苹果后在NeXT公司所开发的系统，这个操作系统在当时虽然没有获得广泛认同，但是却为后面Mac OX奠定了基础。

2. ObjC中很多关键字都是以@开头（例如@autoreleasepool、@interface、@protocol），另外需要注意的是为了区分C语言中的字符串，ObjC中的字符串也必须加上@。

### 基本数据类型

在ObjC中的基本数据类型除了C语言中的char、int、float、double之外还有：

1. BOOL类型，包含两个值YES和NO，其实ObjC中的布尔类型就是整数1和0；
2. id类型，是一个对象类型，可以表示所有对象；
3. NSString是Fundation框架中定义的字符串类型；

```objc
#import <Foundation/Foundation.h>

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        char a='a';
        int b=1;
        float c=8.5;
        double d=8.5e+12;
        char e='a';
        BOOL f=YES,g=NO;
        NSLog(@"a=%c,b=%i,c=%f,d=%f,e=%c,f=%i,g=%i",a,b,c,d,e,f,g);
        //结果：a=a,b=1,c=8.500000,d=8500000000000.000000,e=a,f=1,g=0
        
        NSString *str=@"Hello,World!";
        NSLog(@"str=%@",str); //结果：str=Hello,World!
        
        NSLog(@"object=%p",nil);//结果：object=0x0
    }
    return 0;
}
```

### <u>*常用格式符*</u>

![4-1-1](./img/4-1-1.png)

> 注意
>
> 1. 在C或者ObjC中整形可以使用%i也可以使用%d来格式化，只有使用scanf()输入的时候二者才有区别（由于%d始终被编译器解释为decimal intergeer所以即使输入010会被认为是整形10，而%i则会将前面的0或者0x认为此输入是八进制或十六进制整数）;
>
> 2. %p本质就是输出指针地址，在32位编译器下对于一个指针类型使用%x输出和%p输出其实是一样的，只是后者会在前面加上“ox”（注意在64位编译器下由于指针长度8byte，而整形只有4byte所以%x输出的内容只是%p的低位数据）；

## 类和对象

### 类定义

在ObjC中定义一个类需要两个文件.h和.m：

* **.h文件：**放类的声明，包括成员变量、属性和方法声明（事实上**.h文件不参与编译过程**）；关键字@interface声明一个类，同时它必须以@end结束，在这两个关键字中间声明相关成员；在声明Person类的同时可以看到它继承于NSObject，这是ObjC的基类，所有的类最终都继承于这个类（但是需要注意ObjC中的基类或者根类并不只有一个，例如NSProxy也是ObjC的基类），由于这个类在Foundation框架中定义，所以导入了<Foundation/Foundaton.h>（这么描述的意思是导入Foundation框架中的Foundation.h声明文件）；
* **.m文件：**放属性、方法的具体实现；关键字@implementation用于实现某个类，同时必须以@end结尾，在这两个关键字中间实现具体的属性、方法；由于.m中使用了Person类，所以需要导入声明文件“Person.h”；

Person.h:

```objc
#import <Foundation/Foundation.h>

@interface Person : NSObject

@end
```

Person.m

```objc
#import "Person.h"

@implementation Person

@end
```

### 成员变量

#### 修饰符

成员变量定义在.h文件中，同时必须定义在类后面的{}内。成员的可访问性通过下面三个关键字声明：

* @private 私有成员，只有当前类可以访问；
* @protected 受保护成员，只有当前类或子类可以访问（如果没有添加任何修饰则默认为@protected）；
* @public 公共成员，所有类均可访问；

```objc
#import <Foundation/Foundation.h>//由于使用了NSObject，所以导入此头文件

//NSObject是基类，Person实现了NSObject
@interface Person : NSObject{
    /*成员变量必须包含在大括号中
     *注意成员变量不声明任何关键字的话是默认可访问性@Protected
     *注意在ObjC中不管是自定义的类还是系统类对象都必须是一个指针，例如下面的_name
     */
    @private
    NSString *_name;//在ObjC中推荐成员变量名以_开头
    int _age; //姓名和年龄两个成员变量是私有的
    @protected
    NSString *_nation; //只有子类可以访问
    @public
    float height; //身高是公开的
}

@end
```

> 在ObjC中可访问性修饰符除了这三种，还有一个**@package**不太常用，它类似于C#中的internal在框架内是公共的，但是框架外是私有的（也就是只能在一个框架内可以访问）。

#### 变量访问
1. ObjC中所有的对象类型的变量都必须加上“*”,在ObjC中对象其实就是一个指针（例如之前看到的NSString也是如此，但是基本类型不用加”*”）；
2. ObjC中使用[]进行方法调用，在ObjC中方法调用的本质就是给这个对象或类发送一个消息；
3. 在ObjC中类的实例化需要两个步骤：分配内存、初始化；
4. 类的初始化调用了父类的init方法，如果使用默认初始化方法进行初始化（没有参数），内存分配和初始化可以简写成[Person new]；
5. 公共成员的调用使用“->”操作符；
```objc
#import <Foundation/Foundation.h>
#import "Person.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        Person *p=[Person alloc];
        p=[p init];
        //上面两句代码可以直接写成：Person *p=[[Person alloc] init];
        //还可以写成：Person *p=[Person new];
        
        p->height=1.72;
        NSLog(@"height=%.2f",p->height);//结果：height=1.72
    }
    return 0;
}
```
























### 格式化字符串

* %d：十进制整数
* %s：字符串（char，NSString是对象）
* %o：八进制（octol）
* %x：十六进制（hexadecimal）
* %u：无符号十进制整数
* %f：浮点数
* %e：浮点数科学计数法
* %p：内存地址格式说明，一般内存地址都已十六进制输出
* %zu：数据类型大小
* %@：输出对象的描述信息
* 

> 1. 在%和转换字符间加入==l（long）或者ll（long long）==，指明是大整数数据类型，如：%ld、%lo、%lx
> 2. 通过在%和f或者e之间加入小数点和数字来指定浮点数小数位数，如：%.2f（两位小数）, %.2e（两位小数科学计数法）


### 变量
1. NSInteger（有符号）、NSUInteger（无符号）可以在32位和64位系统中通用的数字类型。printf这两种类型时，需要转换为long型，如：
```c
    NSInteger x = -5;
    NSUInteger y = 6;
    printf("Here they are: %ld, %lu", (long)x, (unsigned long)y)
```

### 方法
1. abs(), labs()：前者计算int型绝对值、后者计算long型绝对值，需要include <stdlib.h>
2. include <math.h>使用数学代码库。
3. readline()：获取用户输入，需要引入代码库；libreadline.tbd
4. atoi()：将字符串转为整数，如：
```c
int num = "23" //出错

int num = atoi("23") //正确

int num = atoi("abc") //记过为0
```
> 如果字符串无法转为int，则返回0

5. sizeof()：得到某个数据类型的大小，结合指针地址使用可以获取数据在内存中的结束地址。
    * sizeof()会返回一个类型为size_t的数，与之对应的格式说明符为%zu
    * sizeof()传入的参数还可以是变量
```c
int i = 7;
int *addressOfI = &i
printf("An int is %zu\n", sizeof(int)); //结果为：AN int is 4

printf("A pointer is %zu\n", sizeof(int *));//结果为：A pointer is 8

printf("A pointer is %zu\n", sizeof(addressOfI));//结果为：A pointer is 8
```

6. modf()：调用该方法时传入double类型的数与指针地址，会返回小数部分，并且将整数部分保存到指针地址中。
    * 需要include <math.h>
    * 这种参数传递方式称为==通过引用传递==


### 指针
> 可以通过变量存储指针地址，通过指针地址变量可以快速传递大数据所在内存地址

1. 在变量前面添加&运算符，来获得变量的内存地址
2. 有三种用途：
    * 声明指针，使变量指向内存地址；
    * 访问保存在内存地址中的数据；
    * 用在赋值表达式的左侧，将数据保存在指定的地址；
```c
int i = 7;
int *addressOfI = &i; //声明指针，使变量指向内存地址
printf("i stores its value at %p\n", &addressOfI);
//输出结果为：i stores its value at 0x7fff5fbff65c

printf("the int stored at addressofi is %d\n", *addressOfI); //访问保存在内存地址中的数据
//输出结果为：the int stored at addressofi is 7

*addressOfI = 88; //用于赋值表达式左侧，将88保存到指定的内存地址，因为变量i也指向相同地址，所以i的值为：88
printf("Now i is %d\n", i);
//输出结果为：Now i is 88
```
3. NULL
    * objective-c中nil等价于NULL，均表示空指针；
    * NULL就是0

> 某些情况需要使用“空”指针，不指向任何地址。换句话说，有一个指针变量，要给这个变量附上一个值，用来说明此变量没指向任何地址。NULL就是这样一个值，用来说明这个指针变量未指向任何地址。

### 结构（structure）
1. 使用结构（struct），来定义多个数据的组合体，类似Java中的pojo
2. 定义结构的两种方式
  * sturct关键字，定义的时候都要写struct关键字
  * typedef关键字
```c
/// 1.sturct关键字，定义的时候都要写struct关键字
struct Person {
    float height;
    int weight;
}

int main(int argc, const char * argv[]){
    struct Person leron; //声明Person变量时需要struct关键字
    leron.height = 1.80;
    leron.weight = 75;
}

/// 2.typedef关键字
typedef struct {
    float height;
    int weight;
}Person；

int main(int argc, const char * argv[]){
    Person leron; //声明Person变量时不需要struct关键字
    leron.height = 1.80;
    leron.weight = 75;
}
```

### 栈 & 堆
1. 栈：调用函数时系统自动分配，并在函数结束时自动释放。
2. 堆：特定内存区域，与栈是分开的。通常连续内存区域叫做：缓冲区。缓冲区来自堆，独立于任何函数的栈，因此可以在多个函数中使用。
> 在C中可以通过malloc()函数得到一块内存缓冲区。当程序不再使用缓冲区时，可以使用free()函数释放内存，还给堆

```c
#include <stdio.h>
#include <stdlib.h>

void get1000floatBuffer(){
    float *startOfBuffer; //声明缓冲区指针变量
    
    startOfBuffer = malloc(1000 * sizeof(float));//获取可以存放1000个float类型变量的内存大小的缓冲区
    
    //。。。使用缓冲区。。。
    
    free(startOfBuffer);//释放缓冲区
    
    startOfBuffer = NULL;//将指针变量赋值为NULL
}

typedef struct {
    float height;
    int weight;
}Person;

float bodyMassIndex(Person *p){
    return p->weight / (p->height * p->height);
}

int main(int argc, const char * argv[]) {
    // insert code here...
    //get1000floatBuffer();
    
    //为Person分配缓冲区
    Person *leron = (Person *)malloc(sizeof(Person));
    printf("leron buffer address: %p\n", &leron);
    
    //为数据结构赋值
    //->函数作用：先获取指针leron指向的数据结构，然后返回该结构的成员变量
    leron->height = 1.8;
    leron->weight = 75;
    
    float leronBMI = bodyMassIndex(leron);
    printf("leron has a BMI of %f\n", leronBMI);
    
    free(leron);
    
    leron = NULL;
    
    return 0;
}
```