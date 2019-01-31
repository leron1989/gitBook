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

### 方法和属性

#### 方法

1. 在ObjC中方法分为静态方法和动态方法两种，动态方法就是对象的方法，静态方法就是类方法，这一点跟其他高级语言没有区别。
2. ObjC中使用“-”定义动态方法，使用“+”定义静态方法。
3. 如果一个方法在.h中有声明则该方法是公共方法，如果没有在.h中声明直接在.m中定义则该方法是私有方法，外部无法访问。

Person.h

```objc
#import <Foundation/Foundation.h>//由于使用了NSObject，所以导入此头文件

//NSObject是基类，Person实现了NSObject
@interface Person : NSObject{
    /*成员变量必须包含在大括号中
     *注意成员变量不声明任何关键字的话是@Protected，其他还有@Private和@Public
     *注意在ObjC中不管是自定义的类还是系统类对象都必须是一个指针，例如下面的_name
     */
    @private
    NSString *_name;//在ObjC中推荐变量名以_开头
    int _age;
    @protected
    NSString *_nation;
    @public
    float height;
}

//声明一个动态方法，没有返回值
-(void)setName:(NSString *)name;
//声明一个静态方法，没有返回值
+(void)showMessage:(NSString *)info;

@end
```

Person.m

```objc
#import "Person.h"

@implementation Person

//实现一个动态方法
-(void)setName:(NSString *)name{
    _name=name;
}

//实现一个静态方法
+(void)showMessage:(NSString *)info{
    NSLog(@"%@",info);
}
@end
```

> 在ObjC中方法的参数类型、返回值类型需要放到()中，而且参数前必须使用冒号，并且此时**冒号是方法名的一部分**。

当方法具有多个参数时：

```objc
-(void)setAge:(int)age andHeight:(NSString *)nation{
    _age=age;
    _nation=nation;
}
```

> 其中andHeight可以省略不写，当然为了保证方法名更有意义建议书写时加上。

#### 属性

通常一个成员的访问不会直接通过成员变量而是通过属性暴漏给外界。在ObjC中属性的实现方式其实类似于Java中属性定义，通过对应的setter和getter方法进行实现。没错，上面setName其实就是属性的setter方法，但是在ObjC中gettter方法通常使用变量名，而不加“get”。下面就看一下年龄属性的实现

Person.h

```objc
#import <Foundation/Foundation.h>//由于使用了NSObject，所以导入此头文件

//NSObject是基类，Person实现了NSObject
@interface Person : NSObject{
    /*成员变量必须包含在大括号中
     *注意成员变量不声明任何关键字的话是@Protected，其他还有@Private和@Public
     *注意在ObjC中不管是自定义的类还是系统类对象都必须是一个指针，例如下面的_name
     */
    @private
    NSString *_name;//在ObjC中推荐变量名以_开头
    int _age;
    @protected
    NSString *_nation;
    @public
    float height;
}

//声明一个动态方法，没有返回值
-(void)setName:(NSString *)name;
//声明一个静态方法，没有返回值
+(void)showMessage:(NSString *)info;

//声明age的setter、getter方法
-(int)age;
-(void)setAge:(int)age;

@end
```

Person.m

```objc
#import "Person.h"

@implementation Person

//实现一个动态方法
-(void)setName:(NSString *)name{
    _name=name;
}

//私有方法
-(void)setAge:(int)age andHeight:(NSString *)nation{
    _age=age;
    _nation=nation;
}

//实现一个静态方法
+(void)showMessage:(NSString *)info{
    NSLog(@"%@",info);
}

//实现age的setter、getter方法
-(int)age{
    return _age;
}
-(void)setAge:(int)age{
    _age=age;
}
@end
```

> 通过上面的程序我们可以看到如果要定义一个属性，首先需要在.h中声明其次还要在.m中实现，而定义属性的代码基本都是类似的，那么有没有简单的方法呢，其实在ObjC中可以通过声明@property，同时通过@synthesize自动生成getter、setter方法（在新版本中甚至甚至都不用通过@synthesize只声明就可以使用）。我们通过一段代码来说明这个问题（为了方便大家查看代码，在下面的代码中暂时去掉前面定义的成员变量、属性等）

Person.h

```objc
#import <Foundation/Foundation.h>

@interface Person : NSObject{
    @public
    NSString *birthday;
    NSString *_position;
    NSString *_degress;
}

@property NSString *birthday;

@property NSString *position;

@property NSString *degress;

@property NSString *education;

@property float weight;

-(void)printInfo;
@end
```

Person.m

```objc
#import "Person.h"

@implementation Person

@synthesize birthday;
@synthesize position;
@synthesize degress=_degress;

@synthesize education;

-(void)printInfo{
    NSLog(@"_weight=%.2f",_weight);
    NSLog(@"education=%@",education);
    NSLog(@"_degress=%@",_degress);
}
@end
```

main.m

```objc
#import <Foundation/Foundation.h>
#import "Person.h"

int main(int argc, const char * argv[]) {
    
    Person *p=[[Person alloc]init];
    p->birthday=@"1987-08-20";
    p.birthday=@"1986-08-08";
    p->_position=@"developer";
    p.position=@"architect";
    
    p.degress=@"undergraduate";
    
    p.education=@"university";
    
    p.weight=60.0;
    
    NSLog(@"p->birthday=%@,p.birthday=%@",p->birthday,p.birthday);
    //结果:p->birthday=1986-08-08,p.birthday=1986-08-08
    
    NSLog(@"p->_position=%@,p.position=%@",p->_position,p.position);
    //结果：p->_position=developer,p.position=architect
    
    NSLog(@"p.weight=%.2f",p.weight);
    //结果：p.weight=60.00
    [p printInfo];
    /*结果：
     _weight=60.00
     education=university
     _degress=undergraduate*/
    
    return 0;
}
```

> * 如果只声明一个属性a，不使用@synthesize实现：编译器会使用\_a作为属性的成员变量（如果没有定义成员变量\_a则会自动生成一个私有的成员变量\_a；如果已经定义了成员变量\_a则使用自定义的成员变量\_a。注意：如果此时定义的成员变量不是\_a而是a则此时会自动生成一个成员变量\_a，它跟自定义成员变量a没有任何关系）；
>
> * 如果声明了一个属性a，使用**@synthesize a**进行实现，但是实现过程中没有指定使用的成员变量（例如上面birthday）：则此时编译器会使用a作为属性的成员变量（如果定义了成员变量a，则使用自定义成员变量；如果此时没有定义则会自动生成一个私有的成员变量a，注意如果此时定义的是\_a则它跟生成的a成员变量没有任何关系）；
>
> * 如果声明了一个属性a，使用**@synthesize a=_a**进行实现，这个过程已经指定了使用的成员变量：此时会使用指定的成员变量作为属性变量；
> * **此外再次强调一下，通过上面的方式定义变量的本质还是生成对应的gettter、setter方法（只是这个步骤编译器帮你完成了），如果通过@property定义了属性，同时在.m中又自定义实现了对应方法，则会使用自定义方法。**

### self关键字

在C#、Java中都有一个关键字this用于表示当前对象，其实在ObjC中也有一个类似的关键字self，只是self不仅可以表示当前对象还可以表示类本身，也就是说它既可以用在静态方法中又可以用在动态方法中。

Person.h

```objc
#import <Foundation/Foundation.h>

@interface Person : NSObject

@property NSString *name;
@property int age;

-(void)setName:(NSString *)name andAge:(int)age;

+(void)showMessage;
@end
```

Person.m

```objc
#import "Person.h"

@implementation Person
-(void)setName:(NSString *)name andAge:(int)age{
//    _name=name;
//    _age=age;
    self.name=name;
    self.age=age;
}

+(void)printInfo{
    NSLog(@"Hello,World!");
}

+(void)showMessage{
    [self printInfo];
}
@end
```

main.m

```objc
#import <Foundation/Foundation.h>
#import "Person.h"

int main(int argc, const char * argv[]) {

    Person *p=[[Person alloc]init];
    [p setName:@"Kenshin" andAge:28];
    [Person showMessage];
    
    return 0;
}
```

#### 扩展

Person.h

```objc
#import <Foundation/Foundation.h>

@interface Person : NSObject

@property NSString *name;
@property int age;

@end
```

Person.m

```objc
#import "Person.h"

@implementation Person

-(void)setName:(NSString *)name{
    self.name=name;
}

@end
```

main.m

```objc
#import <Foundation/Foundation.h>
#import "Person.h"

int main(int argc, const char * argv[]) {
    
    Person *p=[[Person alloc]init];
    
    p.name=@"Kenshin";
    
    return 0;
}
```

> **如果运行上面的代码将会发生死循环，原因很简单，self.name=name本身就会调用Person的setName方法，如此反复就会造成循环操作，所有一般如果需要重写setter方法，可以直接写成_name=name,由此我们也可以看到为什么之前即使没有使用@property生成对应的属性方法，在定义成员变量时也都加上了下划线（这是一好的编码习惯）。**

### 构造方法

#### 自定义构造方法

Person.h

```objc
#import <Foundation/Foundation.h>

@interface Person : NSObject

@property NSString *name;
@property int age;

-(id)initWithName:(NSString *)name andAge:(int )age;

@end
```

Person.m

```objc
#import "Person.h"

@implementation Person

//自定义构造方法
-(id)initWithName:(NSString *)name andAge:(int)age{
    if(self=[super init]){ //super代表父类
        self.name=name;
        self.age=age;
    }
    return self;
}

@end
```

main.m

```objc
#import <Foundation/Foundation.h>
#import "Person.h"

int main(int argc, const char * argv[]) {
    
    Person *p=[[Person alloc]initWithName:@"Kenshin" andAge:28];
    NSLog(@"name=%@,age=%i",p.name,p.age);
    //结果：name=Kenshin,age=28
    return 0;
}
```

> 在ObjC中super代表父类，通过调用父类的方法给当前对象赋值，然后判断这个对象是否为nil，如果不为空则依次给name、age属性赋值。

#### 扩展

通过自定义构造方法固然可以简化代码，但是在使用时还要手动申请内存，在ObjC中一般我们通过定义一个静态方法来解决这个问题。

Person.h

```objc
#import <Foundation/Foundation.h>

@interface Person : NSObject

@property NSString *name;
@property int age;

-(id)initWithName:(NSString *)name andAge:(int )age;

+(id)personWithName:(NSString *)name andAge:(int )age;
@end
```

Person.m

```objc
#import "Person.h"

@implementation Person

//自定义构造方法
-(id)initWithName:(NSString *)name andAge:(int)age{
    if(self=[super init]){ //super代表父类
        self.name=name;
        self.age=age;
    }
    return self;
}

//通过静态方法获得一个对象
+(id)personWithName:(NSString *)name andAge:(int)age{
    Person *p=[[Person alloc]initWithName:name andAge:age];
    return p;
}
@end
```

main.m

```objc
#import <Foundation/Foundation.h>
#import "Person.h"

int main(int argc, const char * argv[]) {
    
    Person *p=[[Person alloc]initWithName:@"Kenshin" andAge:28];
    NSLog(@"name=%@,age=%i",p.name,p.age);
    //结果：name=Kenshin,age=28
    
    Person *p2=[Person personWithName:@"Kaoru" andAge:27];
    NSLog(@"name=%@,age=%i",p2.name,p2.age);
    //结果：name=Kaoru,age=27
    return 0;
}
```

### description方法

在C#中每个类都有一个ToString()方法（java中叫做toString()）用于打印一个对象的信息，在ObjC中这个方法叫description，例如在前面的Person类中我们可以重写这个方法用于打印调试。

Person.m

```objc
#import "Person.h"

@implementation Person

-(NSString *)description{
    return [NSString stringWithFormat:@"{name:%@,age:%i}",self.name,self.age];
}

@end
```

main.m

```objc
#import <Foundation/Foundation.h>
#import "Person.h"

int main(int argc, const char * argv[]) {

    Person *p=[[Person alloc]init];
    p.name=@"Kenshin";
    p.age=28;
    
    NSLog(@"%@",p);//此时会调用对象description方法返回对应的描述信息
    /*结果：
     name:Kenshin,age:28}
     */
    
    return 0;
}
```

> 1. 注意上面NSLog中的格式符是%@，当使用%@输出一个对象时，ObjC会调用个对象的description返回对应的信息进行输出，默认情况下如果我们不重写description方法，输出内容是类名和地址，例如Person则输出“<Person: 0x100202310>”。
>
> 2. 需要强调的是千万不要在description中打印输出self，因为当输出self时会调用该对象的description方法，如此一来就会造成死循环。

### 继承

继承是面向对象三大特征之一，既然ObjC是面向对象语言，当然同样支持继承。事实上前面定义的Person类本身就继承于NSObject，下面再简单看一个例子，这里部分假设我们还有一个Student类继承于Person类，而且这个类有一个分数（score）属性。

Person.h

```objc
#import <Foundation/Foundation.h>

@interface Person : NSObject{
    @protected
    NSString *_nation;
}

#pragma mark - 属性
#pragma mark 姓名
@property (nonatomic,copy) NSString *name;
#pragma mark 年龄
@property (nonatomic,assign) int age;
#pragma mark 籍贯
@property (nonatomic,copy) NSString *nation;

#pragma mark - 动态方法
#pragma mark 带有参数的构造函数
-(id)initWithName:(NSString *)name andAge:(int )age;

#pragma mark - 静态方法
#pragma mark 通过静态方法返回一个对象
+(id)personWithName:(NSString *)name andAge:(int )age;

@end
```

Person.m

```objc
#import "Person.h"

@implementation Person

#pragma mark - 动态方法
#pragma mark 带有参数的构造函数
-(id)initWithName:(NSString *)name andAge:(int)age{
    if(self=[super init]){ //super代表父类
        self.name=name;
        self.age=age;
    }
    return self;
}

#pragma mark - 静态方法
#pragma mark 通过静态方法返回一个对象
+(id)personWithName:(NSString *)name andAge:(int)age{
    Person *p=[[Person alloc]initWithName:name andAge:age];
    return p;
}

#pragma mark - 重写方法
#pragma mark 重写description
-(NSString *)description{
    return [NSString stringWithFormat:@"{name:%@,age:%i}",self.name,self.age];
}

@end
```

Student.h

```objc
#import "Person.h"

@interface Student : Person

#pragma mark - 属性
#pragma mark 分数
@property (nonatomic,assign) float score;

#pragma mark - 动态方法
#pragma mark 带有参数的构造函数
-(id)initWithName:(NSString *)name andAge:(int )age andScore:(float)score;

#pragma mark - 静态方法
#pragma mark 通过静态方法返回一个对象
+(id)studentWithName:(NSString *)name andAge:(int )age andScore:(float)score;

@end
```

Student.m

```objc
#import "Student.h"

@implementation Student

#pragma mark - 动态方法
#pragma mark 带有参数的构造函数
-(id)initWithName:(NSString *)name andAge:(int )age andScore:(float)score{
    if(self=[super initWithName:name andAge:age]){
        self.score=score;
    }
    return self;
}

#pragma mark - 静态方法
#pragma mark 通过静态方法返回一个对象
+(id)studentWithName:(NSString *)name andAge:(int)age andScore:(float)score{
    Student *s=[[Student alloc]initWithName:name andAge:age andScore:score];
    return s;
}

#pragma mark - 重写方法
#pragma mark 重写description
-(NSString *)description{
    return [NSString stringWithFormat:@"{name:%@,age:%i,nation:%@,scroe:%.2f}",self.name,self.age,self->_nation,self.score]; //注意这里访问了父类的属性和方法
}

@end
```

main.m

```objc
#import <Foundation/Foundation.h>
#import "Person.h"
#import "Student.h"

int main(int argc, const char * argv[]) {
    
    Person *p=[Person personWithName:@"Kenshin" andAge:28];
    NSLog(@"p=%@",p);
    
    Student *s=[Student studentWithName:@"Kaoru" andAge:27 andScore:100];
    s.nation=@"henan";
    NSLog(@"s=%@",s);
    
    
    return 0;
}
```

## <u>*协议、代码块、分类*</u>

### 协议（protocol）

#### 基础概念

ObjC中**@protocol**和其他语言的接口定义是类似的，只是在ObjC中interface关键字已经用于定义类了，因此它不会再像C#、Java中使用interface定义接口了。

假设我们定义了一个动物的协议AnimalDelegate，人员Person这个类需要实现这个协议，请看下面的代码：

AnimalDelegate.h

```objc
//定义一个协议
@protocol AnimalDelegate <NSObject>

@required //必须实现的方法
-(void)eat;

@optional //可选实现的方法
-(void)run;
-(void)say;
-(void)sleep;

@end
```

Person.h

```objc
#import <Foundation/Foundation.h>
#import "AnimalDelegate.h"

@interface Person : NSObject<AnimalDelegate>

-(void)eat;

@end
```

Person.m

```objc
#import "Person.h"

@implementation Person

-(void)eat{
    NSLog(@"eating...");
}

@end
```

> 1. 一个协议可以扩展自另一个协议，例如上面AnimalDelegate就扩展自NSObject，如果需要扩展多个协议中间使用逗号分隔；
> 2. 和其他高级语言中接口不同的是协议中定义的方法不一定是必须实现的，我们可以通过关键字进行@required和@optional进行设置，如果不设置则默认是@required（**注意ObjC是弱语法，即使不实现必选方法编译运行也不会报错**）；
> 3. 协议通过<>进行实现，一个类可以同时实现多个协议，中间通过逗号分隔；
> 4. 协议的实现只能在类的声明上,不能放到类的实现上（也就是说必须写成@interface Person:NSObject\<AnimalDelegate\>而不能写成@implementation Person\<AnimalDelegate\>）；
> 5. 协议中不能定义属性、成员变量等,只能定义方法；

**说明：事实上在ObjC中协议的更多作用是用于约束一个类必须实现某些方法，而从面向对象的角度而言这个类跟接口并不一定存在某种自然关系，可能是两个完全不同意义上的事物,这种模式我们称之为代理模式（Delegation）。在Cocoa框架中大量采用这种模式实现数据和UI的分离，而且基本上所有的协议都是以Delegate结尾。**

#### 协议用法

1. 事件机制
    现在假设需要设计一个按钮，我们知道按钮都是需要点击的，在其他语言中通常会引入事件机制，只要使用者订阅了点击事件，那么点击的时候就会触发执行这个事件（这是对象之间解耦的一种方式：代码注入）。但是在ObjC中没有事件的定义，而是使用代理来处理这个问题。首先在按钮中定义按钮的代理，同时使用协议约束这个代理（事件的触发者）必须实现协议中的某些方法，当按钮处理过程中查看代理是否实现了这个方法，如果实现了则调用这个方法。

KCButton.h

```objc
#import <Foundation/Foundation.h>
@class KCButton;

//一个协议可以扩展另一个协议，例如KCButtonDelegate扩展了NSObject协议
@protocol KCButtonDelegate <NSObject>

@required //@required修饰的方法必须实现
-(void)onClick:(KCButton *)button;

@optional //@optional修饰的方法是可选实现的
-(void)onMouseover:(KCButton *)button;
-(void)onMouseout:(KCButton *)button;

@end

@interface KCButton : NSObject

#pragma mark - 属性
#pragma mark 代理属性，同时约定作为代理的对象必须实现KCButtonDelegate协议
@property (nonatomic,retain) id<KCButtonDelegate> delegate;

#pragma mark - 公共方法
#pragma mark 点击方法
-(void)click;

@end
```

KCButton.m

```objc
#import "KCButton.h"

@implementation KCButton

-(void)click{
    NSLog(@"Invoke KCButton's click method.");
    //判断_delegate实例是否实现了onClick:方法（注意方法名是"onClick:",后面有个:）
    //避免未实现ButtonDelegate的类也作为KCButton的监听
    if([_delegate respondsToSelector:@selector(onClick:)]){
        [_delegate onClick:self];
    }
}

@end
```

MyListener.h

```objc
#import <Foundation/Foundation.h>
@class KCButton;
@protocol KCButtonDelegate;

@interface MyListener : NSObject<KCButtonDelegate>
-(void)onClick:(KCButton *)button;
@end
```

MyListener.m

```objc
#import "MyListener.h"
#import "KCButton.h"

@implementation MyListener
-(void)onClick:(KCButton *)button{
    NSLog(@"Invoke MyListener's onClick method.The button is:%@.",button);
}
@end
```

main.m

```objc
#import <Foundation/Foundation.h>
#import "KCButton.h"
#import "MyListener.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        
        KCButton *button=[[KCButton alloc]init];
        MyListener *listener=[[MyListener alloc]init];
        button.delegate=listener;
        [button click];
        /* 结果：
         Invoke KCButton's click method.
         Invoke MyListener's onClick method.The button is:<KCButton: 0x1001034c0>.
         */
    }
    return 0;
}
```
> * id可以表示任何一个ObjC对象类型，类型后面的”<协议名>“用于约束作为这个属性的对象必须实现该协议(注意：使用id定义的对象类型不需要加“*”)；
> * MyListener作为事件触发者，它实现了KCButtonDelegate代理（在ObjC中没有命名空间和包的概念，通常通过前缀进行类的划分，“KC”是我们自定义的前缀）
> * 在.h文件中如果使用了另一个文件的类或协议我们可以通过@class或者@protocol进行声明，而不必导入这个文件，这样可以提高编译效率（**注意有些情况必须使用@class或@protocol，例如上面KCButton.h中上面声明的KCButtonDelegate协议中用到了KCButton类，而此文件下方的KCButton类声明中又使用了KCButtonDelegate，从而形成在一个文件中互相引用关系，此时必须使用@class或者@protocol声明，否则编译阶段会报错**），但是在.m文件中则必须导入对应的类声明文件或协议文件（如果不导入虽然语法检查可以通过但是编译链接会报错）；
> * 使用respondsToSelector方法可以判断一个对象是否实现了某个方法（需要注意方法名不是”onClick”而是“onClick:”，冒号也是方法名的一部分）；




### 代码块（block）

Block就是一个函数体（匿名函数），它是ObjC对于闭包的实现，在块状中我们可以持有或引用局部变量（不禁想到了lambda表达式），同时利用Block你可以将一个操作作为一个参数进行传递（是不是想起了C语言中的函数指针）。

KCButton.h

```objc
#import <Foundation/Foundation.h>
@class KCButton;
typedef void(^KCButtonClick)(KCButton *);

@interface KCButton : NSObject

#pragma mark - 属性
#pragma mark 点击操作属性
@property (nonatomic,copy) KCButtonClick onClick;
//上面的属性定义等价于下面的代码
//@property (nonatomic,copy) void(^ onClick)(KCButton *);

#pragma mark - 公共方法
#pragma mark 点击方法
-(void)click;
@end
```

KCButton.m

```objc
#import "KCButton.h"


@implementation KCButton

-(void)click{
    NSLog(@"Invoke KCButton's click method.");
    if (_onClick) {
        _onClick(self);
    }
}

@end
```

main.m

```objc
#import <Foundation/Foundation.h>
#import "KCButton.h"


int main(int argc, const char * argv[]) {

    KCButton *button=[[KCButton alloc]init];
    button.onClick=^(KCButton *btn){
        NSLog(@"Invoke onClick method.The button is:%@.",btn);
    };
    [button click];
    /*结果：
     Invoke KCButton's click method.
     Invoke onClick method.The button is:<KCButton: 0x1006011f0>.
     */
    
    
    return 0;
}
```

> 1. Block类型定义：**返回值类型(^ 变量名)(参数列表)**（注意Block也是一种类型）；
> 2. Block的typedef定义：**返回值类型(^类型名称)(参数列表)**；
> 3. Block的实现：**^(参数列表){操作主体}**；
> 4. Block中可以读取块外面定义的变量但是不能修改，如果要修改那么这个变量必须声明_block修饰；

### 分类（category）

当我们不改变原有代码为一个类扩展其他功能时我们可以考虑继承这个类进行实现，但是这样一来使用时就必须定义成新实现的子类才能拥有扩展的新功能。如何在不改变原有类的情况下扩展新功能又可以在使用时不必定义新类型呢？我们知道如果在C#中可以使用扩展方法，其实在ObjC中也有类似的实现，就是分类Category。利用分类，我们就可以在ObjC中动态的为已有类添加新的行为（特别是系统或框架中的类）。在C#中字符串有一个Trim()方法用于去掉字符串前后的空格，使用起来特别方便，但是在ObjC中却没有这个方法，这里我们不妨通过Category给NSString添加一个stringByTrim()方法：

NSString+Extend.h

```objc
#import <Foundation/Foundation.h>

@interface NSString (Extend)
-(NSString *)stringByTrim;
@end
```

NSString+Extend.m

```objc
#import "NSString+Extend.h"

@implementation NSString (Extend)
-(NSString *)stringByTrim{
    NSCharacterSet *character= [NSCharacterSet whitespaceCharacterSet];
    return [self stringByTrimmingCharactersInSet:character];
}
@end
```

main.m

```objc
#import <Foundation/Foundation.h>
#import "NSString+Extend.h"


int main(int argc, const char * argv[]) {

    NSString *name=@" Kenshin Cui ";
    name=[name stringByTrim];
    NSLog(@"I'm %@!",name); //结果：I'm Kenshin Cui!
    
    return 0;
}
```

> 分类文件名一般是“原有类名+分类名称”，分类的定义是通过在原有类名后加上”(分类名)”来定义的（注意声明文件.h和实现文件.m都是如此）。

## *<u>内存管理</u>*

### 概念

程序运行过程中要创建大量的对象，和其他高级语言类似，**在ObjC中对象时存储在堆中的，系统并不会自动释放堆中的内存（注意基本类型是由系统自己管理的，放在栈上）**。如果一个对象创建并使用后没有得到及时释放那么就会占用大量内存。其他高级语言如C#、Java都是通过垃圾回收来（GC）解决这个问题的，但在OjbC中并没有类似的垃圾回收机制，因此它的内存管理就需要由开发人员手动维护。

### 引用计数器

在Xcode4.2及之后的版本中由于引入了ARC（Automatic Reference Counting）机制,程序编译时Xcode可以自动给你的代码添加内存释放代码，如果编写手动释放代码Xcode会报错，因此在今天的内容中如果你使用的是Xcode4.2之后的版本（相信现在大部分朋友用的版本都比这个要高），必须手动关闭ARC，这样才有助于你理解ObjC的内存回收机制。

> ObjC中的内存管理机制跟C语言中指针的内容是同样重要的，要开发一个程序并不难，但是优秀的程序则更测重于内存管理，它们往往占用内存更少，运行更加流畅。虽然在新版Xcode引入了ARC，但是很多时候它并不能完全解决你的问题。在Xcode中关闭ARC：项目属性—Build Settings--搜索“garbage”找到Objective-C Automatic Reference Counting设置为No即可。

#### 内存管理原理

​	在C#、Java中都有GC在自动管理内存，当我们实例化一个对象之后通常会有一个变量来引用这个对象（变量中存储对象地址），当这个引用变量不再使用之后（也就是不再引用这个对象）此时GC就会自动回收这个对象，简单的说就是：当一个对象没有任何变量引用的时候就会被回收。

​	在ObjC中内存的管理是依赖对象引用计数器来进行的：**在ObjC中每个对象内部都有一个与之对应的整数（retainCount），叫“引用计数器”，当一个对象在创建之后它的引用计数器为1，当调用这个对象的alloc、retain、new、copy方法之后引用计数器自动在原来的基础上加1（ObjC中调用一个对象的方法就是给这个对象发送一个消息），当调用这个对象的release方法之后它的引用计数器减1，如果一个对象的引用计数器为0，则系统会自动调用这个对象的dealloc方法来销毁这个对象**。

Person.h

```objc
#import <Foundation/Foundation.h>

@interface Person : NSObject

#pragma mark - 属性
@property (nonatomic,copy) NSString *name;
@property (nonatomic,assign) int age;

@end
```

Person.m

```objc
#import "Person.h"

@implementation Person

#pragma mark - 覆盖方法
#pragma mark 重写dealloc方法，在这个方法中通常进行对象释放操作
-(void)dealloc{
    NSLog(@"Invoke Person's dealloc method.");
    [super dealloc];//注意最后一定要调用父类的dealloc方法（两个目的：一是父类可能有其他引用对象需要释放；二是：当前对象真正的释放操作是在super的dealloc中完成的）
}

@end
```

main.m

```objc
#import <Foundation/Foundation.h>
#import "Person.h"

void Test1(){
    Person *p=[[Person alloc]init]; //调用alloc，引用计数器+1
    p.name=@"Kenshin";
    p.age=28;
    
    NSLog(@"retainCount=%lu",[p retainCount]);
    //结果：retainCount=1
    
    [p release];
    //结果：Invoke Person's dealloc method.
    
    
    
    //上面调用过release方法，p指向的对象就会被销毁，但是此时变量p中还存放着Person对象的地址，
    //如果不设置p=nil，则p就是一个野指针，它指向的内存已经不属于这个程序，因此是很危险的
    p=nil;
    //如果不设置p=nil，此时如果再调用对象release会报错，但是如果此时p已经是空指针了，
    //则在ObjC中给空指针发送消息是不会报错的
    [p release];
}

void Test2(){
    Person *p=[[Person alloc]init];
    p.name=@"Kenshin";
    p.age=28;
    
    NSLog(@"retainCount=%lu",[p retainCount]);
    //结果：retainCount=1
    
    [p retain];//引用计数器+1
    NSLog(@"retainCount=%lu",[p retainCount]);
    //结果：retainCount=2
    
    [p release];//调用1次release引用计数器-1
    NSLog(@"retainCount=%lu",[p retainCount]);
    //结果：retainCount=1
    [p release];
    //结果：Invoke Person's dealloc method.
    p=nil;
}

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        Test1();
    }
    return 0;
}
```

> 1. 如果一个对象被释放之后，那么最后引用它的变量我们手动设置为nil，否则可能造成野指针错误，而且需要注意在ObjC中给空对象发送消息是不会引起错误的。
>
> 2. 野指针错误形式在Xcode中通常表现为：**Thread 1：EXC_BAD_ACCESS(code=EXC_I386_GPFLT)**错误。因为你访问了一块已经不属于你的内存。

#### 内存释放的原则

手动管理内存有时候并不容易，因为对象的引用有时候是错综复杂的，对象之间可能互相交叉引用，此时需要遵循一个法则：**<u>谁创建，谁释放</u>**。



假设现在有一个人员Person类，每个Person可能会购买一辆汽车Car，通常情况下购买汽车这个活动我们可能会单独抽取到一个方法中，同时买车的过程中我们可能会多看几辆来最终确定理想的车，现在我们的代码如下：

Car.h

```objc
#import <Foundation/Foundation.h>

@interface Car : NSObject

#pragma mark - 属性
#pragma mark 车牌号
@property (nonatomic,copy) NSString *no;

#pragma mark - 公共方法
#pragma mark 运行方法
-(void)run;

@end
```

Car.m

```objc
#import "Car.h"

@implementation Car

#pragma mark - 公共方法
#pragma mark 运行方法
-(void)run{
    NSLog(@"Car(%@) run.",self.no);
}

#pragma mark - 覆盖方法
#pragma mark 重写dealloc方法
-(void)dealloc{
    
    NSLog(@"Invoke Car(%@) dealloc method.",self.no);
    [super dealloc];
}
@end
```

Person.h

```objc
#import <Foundation/Foundation.h>
@class Car;

@interface Person : NSObject{
    Car *_car;
}

#pragma mark - 属性
#pragma mark 姓名
@property (nonatomic,copy) NSString *name;

#pragma mark - 公共方法
#pragma mark Car属性的set方法
-(void)setCar:(Car *)car;
#pragma mark  Car属性的get方法
-(Car *)car;
@end
```

Person.m

```objc
#import "Person.h"
#import "Car.h"

@implementation Person

#pragma mark - 公共方法
#pragma mark Car属性的set方法
-(void)setCar:(Car *)car{
    if (_car!=car) { //首先判断要赋值的变量和当前成员变量是不是同一个变量
        [_car release]; //释放之前的对象
        _car=[car retain];//赋值时重新retain
    }
}
#pragma mark  Car属性的get方法
-(Car *)car{
    return _car;
}

#pragma mark - 覆盖方法
#pragma mark 重写dealloc方法
-(void)dealloc{
    NSLog(@"Invoke Person(%@) dealloc method.",self.name);
    [_car release];//在此释放对象，即使没有赋值过由于空指针也不会出错
    [super dealloc];
}
@end
```

main.m

```objc
#import <Foundation/Foundation.h>
#import "Person.h"
#import "Car.h"

void getCar(Person *p){
    Car *car1=[[Car alloc]init];
    car1.no=@"888888";
    
    p.car=car1;
    
    NSLog(@"retainCount(p)=%lu",[p retainCount]);
    
    Car *car2=[[Car alloc]init];
    car2.no=@"666666";
    
    [car1 release]; // car1对象在给person赋值时调用了retain函数，导致引用计算+1，也就是2，因此release后的引用计数为1，并不会调用Car的dealloc方法
    car1=nil;
    
    [car2 release];
    car2=nil;
}

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        Person *p=[[Person alloc]init];
        p.name=@"Kenshin";
        
        getCar(p);
        
        [p.car run];
        
        [p release];
        
        p=nil;
        
    }
    return 0;
}
```

输出结果为：

```
2019-01-22 12:23:19.487479+0800 MemoryManageDemo[48584:1541829] retainCount(p)=1
2019-01-22 12:23:19.487756+0800 MemoryManageDemo[48584:1541829] Invoke Car(666666) dealloc method.
2019-01-22 12:23:19.487784+0800 MemoryManageDemo[48584:1541829] Car(888888) run.
2019-01-22 12:23:19.487836+0800 MemoryManageDemo[48584:1541829] Invoke Person(Kenshin) dealloc method.
2019-01-22 12:23:19.487864+0800 MemoryManageDemo[48584:1541829] Invoke Car(888888) dealloc method.
Program ended with exit code: 0
```

从运行结果来看创建的三个对象p、car1、car2都被回收了，而且[p.car run]也能顺利运行，已经达到了我们的需求。但是这里需要重点解释一下setCar方法的实现,setCar方法中为什么没有写成如下形式：

```objc
-(void)setCar:(Car *)car{
    _car=car;
}
```

前面在我们说到属性的定义时不是都采用的这种方式吗？

根据前面说到的内存释放原则，getCar方法完全符合，在这个方法中定义的两个对象car1、car2也都是在这个方法中释放的，包括main函数中的p对象也是在main函数中定义和释放的。但是如果发现调用完getCar方法之后紧接着调用了汽车的run方法，当然这在程序设计和开发过程中应该是再普通不过的设计了。如果setCar写成“_car=car”的形式，当调用完getCar方法后，人员的car属性被释放了，此时调用run方法是会报错的（大家自己可以试试）。但是如下的方式却不会有问题：

```objc
-(void)setCar:(Car *)car{
    if (_car!=car) { //首先判断要赋值的变量和当前成员变量是不是同一个变量
        [_car release]; //释放之前的对象
        _car=[car retain];//赋值时重新retain
    }
}
```

**因为在这个方法中我们通过[car retain]保证每次属性赋值的时候对象引用计数器+1,这样一来调用过getCar方法可以保证人员的car属性不会被释放，其次为了保证上一次的赋值对象（car1）能够正常释放，我们在赋新值之前对原有的值进行release操作。最后在Person的dealloc方法中对_car进行一次release操作（因为setCar中做了一次retain操作）保证_car能正常回收。**

### 属性参数

像上面这样编写setCar方法的情况是比较多的，那么如何使用@property进行自动实现呢？答案就是使用属性参数，例如上面car属性的setter方法，可以通过@property定义如下：

```objc
@property (nonatomic,retain) Car *car;
```

你会发现此刻我们不必手动实现car的getter、setter方法程序仍然没有内存泄露。其实大家也应该都已经看到前面Person的name属性定义的时候我们同样加上了（nonatomic,copy）参数，这些参数到底是什么意思呢？

![4-4-1](.\img\4-4-1.png)

@property的参数分为三类，也就是说参数最多可以有三个，中间用逗号分隔，每类参数可以从上表三类参数中人选一个。如果不进行设置或者只设置其中一类参数，程序会使用三类中的各个默认参数，默认参数：(atomic,readwrite,assign)

1. 一般情况下如果在多线程开发中一个属性可能会被两个及两个以上的线程同时访问，此时可以考虑atomic属性，否则建议使用nonatomic，不加锁，效率较高；
2. readwirte方法会生成getter、setter两个方法，如果使用readonly则只生成getter方法；
3. 关于set方法处理需要特别说明，假设我们定义一个属性a，这里列出三种方式的生成代码：
* assign，用于基本数据类型

```objc
-(void)setA:(int)a{
    _a=a;
}
```


   * retain，通常用于非字符串对象

```objc
-(void)setA:(Car *)a{
	if(_a!=a){
		[_a release];
		_a=[a retain];
	}
}
```

   * copy，通常用于字符串对象、block、NSArray、NSDictionary

```objc
-(void)setA:(NSString *)a{
	if(_a!=a){
		[_a release];
		_a=[a copy];
	}
}
```

> **备注：本文基于MRC进行介绍，ARC下的情况不同，请参阅其他文章，例如ARC下基本数据类型默认的属性参数为(atomic,readwrite,assign)，对象类型默认的属性参数为(atomic,readwrite,strong)**

### 自动释放池

在ObjC中也有一种内存自动释放的机制叫做“自动引用计数”（或“自动释放池”），与C#、Java不同的是，这只是一种半自动的机制，有些操作还是需要我们手动设置的。自动内存释放使用@autoreleasepool关键字声明一个代码块，如果一个对象在初始化时调用了autorelase方法，那么当代码块执行完之后，在块中调用过autorelease方法的对象都会自动调用一次release方法。这样一来就起到了自动释放的作用，同时对象的销毁过程也得到了延迟（统一调用release方法）。看下面的代码：

Person.h

```objc
#import <Foundation/Foundation.h>

@interface Person : NSObject

#pragma mark - 属性
#pragma mark 姓名
@property (nonatomic,copy) NSString *name;

#pragma mark - 公共方法
#pragma mark 带参数的构造函数
-(Person *)initWithName:(NSString *)name;
#pragma mark 取得一个对象（静态方法）
+(Person *)personWithName:(NSString *)name;
@end
```

Person.m

```objc
#import "Person.h"

@implementation Person

#pragma mark - 公共方法
#pragma mark 带参数的构造函数
-(Person *)initWithName:(NSString *)name{
    if(self=[super init]){
        self.name=name;
    }
    return self;
}
#pragma mark 取得一个对象（静态方法）
+(Person *)personWithName:(NSString *)name{
    Person *p=[[[Person alloc]initWithName:name] autorelease];//注意这里调用了autorelease
    return p;
}

#pragma mark - 覆盖方法
#pragma mark 重写dealloc方法
-(void)dealloc{
    NSLog(@"Invoke Person(%@) dealloc method.",self.name);
    [super dealloc];
}

@end
```

main.m

```objc
#import <Foundation/Foundation.h>
#import "Person.h"


int main(int argc, const char * argv[]) {

    @autoreleasepool {
        Person *person1=[[Person alloc]init];
        [person1 autorelease];//调用了autorelease方法后面就不需要手动调用release方法了
        person1.name=@"Kenshin";//由于autorelease是延迟释放，所以这里仍然可以使用person1
        
        Person *person2=[[[Person alloc]initWithName:@"Kaoru"] autorelease];//调用了autorelease方法
        
        Person *person3=[Person personWithName:@"rosa"];//内部已经调用了autorelease，所以不需要手动释放，这也符合内存管理原则，因为这里并没有alloc所以不需要release或者autorelease
        
        Person *person4=[Person personWithName:@"jack"];
        [person4 retain];
    }
    /*结果：
     Invoke Person(rosa) dealloc method.
     Invoke Person(Kaoru) dealloc method.
     Invoke Person(Kenshin) dealloc method.
     */
    
    return 0;
}
```

当上面@autoreleaespool代码块执行完之后，三个对象都得到了释放，但是person4并没有释放，原因很简单，由于我们手动retain了一次，当自动释放池释放后调用四个对的release方法，当调用完person4的release之后它的引用计数器为1，所有它并没有释放（这是一个反例，会造成内存泄露）；autorelase方法将一个对象的内存释放延迟到了自动释放池销毁的时候，因此上面person1，调用完autorelase之后它还存在，因此给name赋值不会有任何问题；在ObjC中通常如果一个静态方法返回一个对象本身的话，在静态方法中我们需要调用autorelease方法，因为按照内存释放原则，在外部使用时不会进行alloc操作也就不需要再调用release或者autorelase，所以这个操作需要放到静态方法内部完成。

对于自动内存释放简单总结一下：

> 1. autorelease方法不会改变对象的引用计数器，只是将这个对象放到自动释放池中；
> 2. 自动释放池实质是当自动释放池销毁后调用对象的release方法，不一定就能销毁对象（例如如果一个对象的引用计数器>1则此时就无法销毁）；
> 3. 由于自动释放池最后统一销毁对象，因此如果一个操作比较占用内存（对象比较多或者对象占用资源比较多），最好不要放到自动释放池或者考虑放到多个自动释放池；
> 4. ObjC中类库中的静态方法一般都不需要手动释放，内部已经调用了autorelease方法；


## KVC、KVO

### 键值编码（KVC）

我们知道在C#中可以通过反射读写一个对象的属性，有时候这种方式特别方便，因为你可以利用字符串的方式去动态控制一个对象。其实由于ObjC的语言特性，你根部不必进行任何操作就可以进行属性的动态读写，这种方式就是Key Value Coding（简称KVC）。

KVC的操作方法由NSKeyValueCoding协议提供，而NSObject就实现了这个协议，也就是说ObjC中几乎所有的对象都支持KVC操作，常用的KVC操作方法如下：

* 动态设置： **setValue:属性值 forKey:属性名**（用于简单路径）、**setValue:属性值 forKeyPath:属性路径**（用于复合路径，例如Person有一个Account类型的属性，那么person.account就是一个复合属性）
* 动态读取： **valueForKey:属性名** 、**valueForKeyPath:属性名**（用于复合路径）

下面通过一个例子来理解KVC

Account.h

```objc
#import <Foundation/Foundation.h>

@interface Account : NSObject
#pragma mark - 属性
#pragma mark 余额
@property (nonatomic,assign) float balance;
@end
```

Account.m

```objc
#import "Account.h"

@implementation Account

@end
```

Person.h

```objc
#import <Foundation/Foundation.h>
@class Account;

@interface Person : NSObject{
    @private
    int _age;
}
#pragma mark - 属性
#pragma mark 姓名
@property (nonatomic,copy) NSString *name;
#pragma mark 账户
@property (nonatomic,retain) Account *account;

#pragma mark - 公共方法
#pragma mark 显示人员信息
-(void)showMessage;
@end
```

Person.m

```objc
#import "Person.h"

@implementation Person

#pragma mark - 公共方法
#pragma mark 显示人员信息
-(void)showMessage{
    NSLog(@"name=%@,age=%d",_name,_age);
}
@end
```

main.m

```objc
#import <Foundation/Foundation.h>
#import "Person.h"
#import "Account.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        
        Person *person1=[[Person alloc]init];
        [person1 setValue:@"Kenshin" forKey:@"name"];
        [person1 setValue:@28 forKey:@"age"];//注意即使一个私有变量仍然可以访问
        
        [person1 showMessage];
        //结果：name=Kenshin,age=28
        NSLog(@"person1's name is :%@,age is :%@",person1.name,[person1 valueForKey:@"age"]);
        //结果：person1's name is :Kenshin,age is :28
        
        
        
        
        Account *account1=[[Account alloc]init];
        person1.account=account1;//注意这一步一定要先给account属性赋值，否则下面按路径赋值无法成功，因为account为nil，当然这一步骤也可以写成:[person1 setValue:account1 forKeyPath:@"account"];
        
        [person1 setValue:@100000000.0 forKeyPath:@"account.balance"];
        
        NSLog(@"person1's balance is :%.2f",[[person1 valueForKeyPath:@"account.balance"] floatValue]);
        //结果：person1's balance is :100000000.00 
    }
    return 0;
}
```
KVC使用起来比较简单，但是它如何查找一个属性进行读取呢？具体查找规则（假设现在要利用KVC对a进行读取）：

* 如果是动态设置属性，则优先考虑调用setA方法，如果没有该方法则优先考虑搜索成员变量_a,如果仍然不存在则搜索成员变量a，如果最后仍然没搜索到则会调用这个类的setValue:forUndefinedKey：方法(注意搜索过程中不管这些方法、成员变量是私有的还是公共的都能正确设置)；
* 如果是动态读取属性，则优先考虑调用a方法（属性a的getter方法），如果没有搜索到则会优先搜索成员变量_a，如果仍然不存在则搜索成员变量a，如果最后仍然没搜索到则会调用这个类的valueforUndefinedKey:方法(注意搜索过程中不管这些方法、成员变量是私有的还是公共的都能正确读取)；

### 键值监听（KVO）

我们知道在WPF、Silverlight中都有一种双向绑定机制，如果数据模型修改了之后会立即反映到UI视图上，类似的还有如今比较流行的基于MVVM设计模式的前端框架，例如Knockout.js。其实在ObjC中原生就支持这种机制，它叫做Key Value Observing（简称KVO）。KVO其实是一种观察者模式，利用它可以很容易实现视图组件和数据模型的分离，当数据模型的属性值改变之后作为监听器的视图组件就会被激发，激发时就会回调监听器自身。在ObjC中要实现KVO则必须实现NSKeyValueObServing协议，不过幸运的是NSObject已经实现了该协议，因此几乎所有的ObjC对象都可以使用KVO。

在ObjC中使用KVO操作常用的方法如下：
* 注册指定Key路径的监听器： **addObserver: forKeyPath: options:  context:**
* 删除指定Key路径的监听器： **removeObserver: forKeyPath**、**removeObserver: forKeyPath: context:**
* 回调监听： **observeValueForKeyPath: ofObject: change: context:**

KVO的使用步骤也比较简单：
1. 通过addObserver: forKeyPath: options: context:为被监听对象（它通常是数据模型）注册监听器
2. 重写监听器的observeValueForKeyPath: ofObject: change: context:方法

由于我们还没有介绍过IOS的界面编程，这里我们还是在上面的例子基础上继续扩展，假设当我们的账户余额balance变动之后我们希望用户可以及时获得通知。那么此时Account就作为我们的被监听对象，需要Person为它注册监听（使用addObserver: forKeyPath: options: context:）;而人员Person作为监听器需要重写它的observeValueForKeyPath: ofObject: change: context:方法，当监听的余额发生改变后会回调监听器Person监听方法（observeValueForKeyPath: ofObject: change: context:）。下面通过代码模拟上面的过程：

Account.h
```objc
#import <Foundation/Foundation.h>

@interface Account : NSObject
#pragma mark - 属性
#pragma mark 余额
@property (nonatomic,assign) float balance;
@end
```

Account.m
```objc
#import "Account.h"

@implementation Account

@end
```

Person.h
```objc
#import <Foundation/Foundation.h>
@class Account;

@interface Person : NSObject{
    @private
    int _age;
}
#pragma mark - 属性
#pragma mark 姓名
@property (nonatomic,copy) NSString *name;
#pragma mark 账户
@property (nonatomic,retain) Account *account;

#pragma mark - 公共方法
#pragma mark 显示人员信息
-(void)showMessage;
@end
```

Person.m
```objc
#import "Person.h"
#import "Account.h"

@implementation Person

#pragma mark - 公共方法
#pragma mark 显示人员信息
-(void)showMessage{
    NSLog(@"name=%@,age=%d",_name,_age);
}

#pragma mark 设置人员账户
-(void)setAccount:(Account *)account{
    _account=account;
    //添加对Account的监听
    [self.account addObserver:self forKeyPath:@"balance" options:NSKeyValueObservingOptionNew context:nil];
}

#pragma mark - 覆盖方法
#pragma mark 重写observeValueForKeyPath方法，当账户余额变化后此处获得通知
-(void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary *)change context:(void *)context{
    if([keyPath isEqualToString:@"balance"]){//这里只处理balance属性
        NSLog(@"keyPath=%@,object=%@,newValue=%.2f,context=%@",keyPath,object,[[change objectForKey:@"new"] floatValue],context);
    }
}
#pragma mark 重写销毁方法
-(void)dealloc{
    [self.account removeObserver:self forKeyPath:@"balance"];//移除监听
    //[super dealloc];//注意启用了ARC，此处不需要调用
}
@end
```

main.m
```objc
#import <Foundation/Foundation.h>
#import "Person.h"
#import "Account.h"

int main(int argc, const char * argv[]) {
    @autoreleasepool {
        
        Person *person1=[[Person alloc]init];
        person1.name=@"Kenshin";
        Account *account1=[[Account alloc]init];
        account1.balance=100000000.0;
        person1.account=account1;
        
        account1.balance=200000000.0;//注意执行到这一步会触发监听器回调函数observeValueForKeyPath: ofObject: change: context:
        //结果：keyPath=balance,object=<Account: 0x100103aa0>,newValue=200000000.00,context=(null)
        
        
    }
    return 0;
}
```
在上面的代码中我们在给人员分配账户时给账户的balance属性添加了监听，并且在监听回调方法中输出了监听到的信息，同时在对象销毁时移除监听，这就构成了一个典型的KVO应用。


## <u>*Foundation框架*</u>

### 概述

Cocoa是什么呢？

Cocoa不是一种编程语言（它可以运行多种编程语言），它也不是一个开发工具（通过命令行我们仍然可以开发Cocoa程序），它是创建Mac OS X和IOS程序的原生面向对象API，为这两者应用提供了编程环境。

我们通常称为“Cocoa框架”，事实上Cocoa本身是一个框架的集合，它包含了众多子框架，其中最重要的要数“Foundation”和“UIKit”。前者是框架的基础，和界面无关，其中包含了大量常用的API；后者是基础的UI类库，以后我们在IOS开发中会经常用到。这两个框架在系统中的位置如下图：

![4-5-1](.\img\4-5-1.jpg)

#### Foundation框架API概览

其实所有的Mac OS X和IOS程序都是由大量的对象构成，而这些对象的根对象都是NSObject，NSObject就处在Foundation框架之中，具体的类结构如下：

![4-5-2](.\img\4-5-2.jpg)

![4-5-3](.\img\4-5-3.jpg)

![4-5-4](.\img\4-5-4.jpg)

通常我们会将他们分为几类：

1. 值对象
2. 集合
3. 操作系统服务：文件系统、URL、进程通讯
4. 通知
5. 归档和序列化
6. 表达式和条件判断
7. Objective-C语言服务

#### UIKit架构概览

UIKit主要用于界面构架，这里我们不妨也看一下它的类结构：

![4-5-5](.\img\4-5-5.jpg)

### 常用结构体

在Foundation中定义了很多常用结构体类型来简化我们的日常开发，这些结构体完全采用Objective-C定义，和我们自己定义的结构体没有任何区别，之所以由框架为我们提供完全是为了简化我们的开发。常用的结构体有**NSRange、NSPoint、NSSize、NSRect**等

#### NSRange(范围)

```objc
NSRange rg={3,5};//第一参数是起始位置第二个参数是长度
//NSRange rg;
//rg.location=3;
//rg.length=5;
//NSRange rg={.location=3,.length=5};
NSRange rg2=NSMakeRange(3,5); //常用此方式定义，使用NSMakeRange定义一个NSRange 
NSLog(@"rg2 is %@", NSStringFromRange(rg2));//打印NSRange可以使用Foundation中方法 
//注意不能直接NSLog(@"rg2 is %@", rg2)，因为rg2不是对象（准确的说%@是指针）而是结构体
```

#### NSPoint(点)

```objc
NSPoint p = NSMakePoint(10, 15); //NSPoint其实就是CGPoint
NSPoint p2=CGPointMake(10, 15); //这种方式比较常见 
NSLog(NSStringFromPoint(p2));
```

#### NSSize(大小)

```objc
NSSize s=NSMakeSize(10, 15);//NSSize其实就是CGSize
CGSize s2=CGSizeMake(10, 15); //这种方式比较常见
NSLog(NSStringFromSize(s2));
```

#### NSRect(矩形)

```objc
NSRect r=NSMakeRect(10, 5, 100, 200);//NSRect其实就是CGRect
NSRect r2=CGRectMake(10, 5, 100, 200);//这种方式比较常见 
NSLog(NSStringFromRect(r2));
```

#### 总结

1. 对于常用结构体在Foundation框架中都有一个对应的make方法进行创建，这也是我们日后比较常用的操作;

2. 而且与之对应的还都有一个NSStringFromXX方法来进行字符串转换，方便我们调试。
3. 上面也提到NSSize其实就是CGSize，NSRect其实就是CGRect，我们可以通过查看代码进行确认，例如NSSize定义：

```objc
typedef CGSize NSSize;
```

继续查看CGSize的代码：

```objc
/** Size **/
struct CGSize {
    CGFloat width;
    CGFloat height;
};
typedef struct CGSize CGSize;
```

### 日期

#### 当前日期

```objc
NSDate *date1=[NSDate date];//获得当前日期
NSLog(@"%@",date1); //结果：2014-07-16 07:25:28 +0000
```

#### 当前日期 + N秒

```objc
NSDate *date2=[NSDate dateWithTimeIntervalSinceNow:100];//在当前日期的基础上加上100秒，注意在ObjC中多数时间单位都是秒
NSLog(@"%@",date2); //结果：2014-07-16 07:27:08 +0000
```

#### 随机将来日期

```objc
NSDate *date3=[NSDate distantFuture];//随机获取一个将来的日期
NSLog(@"%@",date3); //结果：4001-01-01 00:00:00 +0000
```

#### 日期之差（秒）

```objc
NSTimeInterval time=[date2 timeIntervalSinceDate:date1];//日期之差,返回单位为秒
NSLog(@"%f",time); //结果：100.008833
```

#### 日期比较（返回较早一个）

```objc
NSDate *date5=[date1 earlierDate:date3];//返回比较早的日期
NSLog(@"%@",date5); //结果：2014-07-16 07:25:28 +0000
```

#### 日期格式化

```objc
NSDateFormatter *formater1=[[NSDateFormatter alloc]init];
formater1.dateFormat=@"yy-MM-dd HH:mm:ss";
NSString *datestr1=[formater1 stringFromDate:date1];
NSLog(@"%@",datestr1); //结果：14-07-16 15:25:28
```

#### 字符串转日期

```objc
NSDate *date6=[formater1 dateFromString:@"14-02-14 11:07:16"];
NSLog(@"%@",date6); //结果：2014-02-14 03:07:16 +0000
```

### 字符串

#### 不可变字符串(NSString)

> 注意：以下代码注释中提到的需要释放内存指的是在MRC下的情况，当然本质上在ARC下也需要释放，只是这部分代码编译器会自动创建。

##### 字符串定义

```objc
char *str1="C string";//这是C语言创建的字符串
NSString *str2=@"OC string";//ObjC字符串需要加@，并且这种方式创建的对象不需要自己释放内存

//下面的创建方法都应该释放内存
NSString *str3=[[NSString alloc] init];
str3=@"OC string";
NSString *str4=[[NSString alloc] initWithString:@"Objective-C string"];
NSString *str5=[[NSString alloc] initWithFormat:@"age is %i,name is %.2f",19,1.72f];
NSString *str6=[[NSString alloc] initWithUTF8String:"C string"];//C语言的字符串转换为ObjC字符串

//以上方法都有对应静态方法（一般以string开头）,不需要管理内存（系统静态方法一般都是自动释放）
NSString *str7=[NSString stringWithString:@"Objective-C string"];
```

##### 字符串分割

1. substringFromIndex
2. substringToIndex
3. substringWithRange
4. componentsSeparatedByString

```objc
NSLog(@"%@",[@"abcdef" substringFromIndex:3]);//从第三个索引开始（包括第三个索引对应的字符）截取到最后一位
//结果：def
NSLog(@"%@",[@"abcdef" substringToIndex:3]);////从0开始截取到第三个索引（不包括第三个索引对应的字符）
//结果：abc
NSLog(@"%@",[@"abcdef" substringWithRange:NSMakeRange(2, 3)]);
//结果：cde
NSString *str1=@"12.abcd.3a";
NSArray *array1=[str1 componentsSeparatedByString:@"."];//字符串分割
NSLog(@"%@",array1);
/*结果：
(
	12,
	abcd,
	3a
)
*/
```

##### 大小写转换

1. 小写转大写
2. 大写转小写
3. 首字母大写，其他小写

```objc
NSLog(@"\"Hello world!\" to upper is %@",[@"Hello world!" uppercaseString]);
//结果："Hello world!" to upper is HELLO WORLD!
NSLog(@"\"Hello world!\" to lowwer is %@",[@"Hello world!" lowercaseString]);
//结果："Hello world!" to lowwer is hello world!

//首字母大写，其他字母小写
NSLog(@"\"Hello world!\" to capitalize is %@",[@"Hello world!" capitalizedString]);
//结果："Hello world!" to capitalize is Hello World!
```

##### 字符串比较

1. isEqualToString
2. compare
3. caseInsensitiveCompare

```objc
BOOL result= [@"abc" isEqualToString:@"aBc"];
NSLog(@"%i",result);
//结果：0
NSComparisonResult result2= [@"abc" compare:@"aBc"];//如果是[@"abc" caseInsensitiveCompare:@"aBc"]则忽略大小写比较
if(result2==NSOrderedAscending){
    NSLog(@"left<right.");
}else if(result2==NSOrderedDescending){
    NSLog(@"left>right.");
}else if(result2==NSOrderedSame){
    NSLog(@"left=right.");
}
//结果：left>right.
```

##### 字符串搜索

1. hasPrefix
2. hasSuffix
3. rangeOfString

```objc
NSLog(@"has prefix ab? %i",[@"abcdef" hasPrefix:@"ab"]);
//结果：has prefix ab? 1
NSLog(@"has suffix ab? %i",[@"abcdef" hasSuffix:@"ef"]);
//结果：has suffix ab? 1
NSRange range=[@"abcdefabcdef" rangeOfString:@"cde"];//注意如果遇到cde则不再往后面搜索,如果从后面搜索或其他搜索方式可以设置第二个options参数
if(range.location==NSNotFound){
    NSLog(@"not found.");
}else{
    NSLog(@"range is %@",NSStringFromRange(range));
}
//结果：range is {2, 3}
```

##### 其他操作

1. intValue（类型转换）
2. length（长度）
3. characterAtIndex（指定位置字符）
4. UTF8String（objc转c字符串）

```objc
NSLog(@"%i",[@"12" intValue]);//类型转换
//结果：12
NSLog(@"%zi",[@"hello world,世界你好！" length]);//字符串长度注意不是字节数
//结果：17
NSLog(@"%c",[@"abc" characterAtIndex:0]);//取出指定位置的字符
//结果：a
const char *s=[@"abc" UTF8String];//转换为C语言字符串
NSLog(@"%s",s);
//结果：abc
```

#### 可变字符串(NSMutableString)

ObjC为我们提供了新的可变字符串类NSMutableString，它是NSString的子类。

##### 定义可变字符串

```objc
/*可变字符串，注意NSMutableString是NSString子类*/
//注意虽然initWithCapacity分配字符串大小，但是不是绝对的不可以超过此范围，声明此变量对性能有好处
NSMutableString *str1= [[NSMutableString alloc] initWithCapacity:10];
[str1 setString:@"hello"];//设置字符串
NSLog(@"%@",str1);
//结果：hello
```

##### 追加字符串

```objc
[str1 appendString:@",world!"];//追加字符串
NSLog(@"%@",str1);
//结果：hello,world!

[str1 appendFormat:@"我的年龄是%i。dear,I love you.",18];
NSLog(@"%@",str1);
//结果：hello,world!我的年龄是18。dear,I love you.
```

##### 替换字符串

```objc
NSRange range=[str1 rangeOfString:@"dear"];
[str1 replaceCharactersInRange:range withString:@"Honey"];
NSLog(@"%@",str1);
//结果：hello,world!我的年龄是18。Honey,I love you.
```

##### 插入字符串

```objc
[str1 insertString:@"My name is Kenshin." atIndex:12];
NSLog(@"%@",str1);
//结果：hello,world!My name is Kenshin.我的年龄是18。Honey,I love you.
```

##### 删除指定字符串

```objc
[str1 deleteCharactersInRange:[str1 rangeOfString:@"My name is Kenshin."]];//删除指定范围的字符串
NSLog(@"%@",str1);
//结果：hello,world!我的年龄是18。Honey,I love you.
```



### 文件操作

主要介绍文件的读、写，以及路径的操作，文件扩展名操作

#### 读取文件

```objc
NSString *path=@"/Users/kenshincui/Desktop/test.txt";
NSString *str1=[NSString stringWithContentsOfFile:path encoding:NSUTF8StringEncoding error:nil];
//注意上面也可以使用gb2312 gbk等,例如kCFStringEncodingGB_18030_2000，但是需要用CFStringConvertEncodingToNSStringEncoding转换
NSLog(@"str1 is %@",str1);
//结果：str1 is hello world,世界你好！
//上面我们看到了读取文件，但并没有处理错误,当然在ObjC中可以@try @catch @finnally但通常我们并不那么做
//由于我们的test.txt中有中文，所以使用下面的编码读取会报错，下面的代码演示了错误获取的过程
NSError *error;
NSString *str2=[NSString stringWithContentsOfFile:path encoding:kCFStringEncodingGB_18030_2000 error:&error];//注意这句话中的error变量是**error，就是指针的指针那就是指针的地址，由于error就是一个指针此处也就是error的地址&error，具体原因见下面补充
if(error){
    NSLog(@"read error ,the error is %@",error);
}else{
    NSLog(@"read success,the file content is %@",str2);
}
//结果：read error ,the error is Error Domain=NSCocoaErrorDomain Code=261 "The file couldn’t be opened using the specified text encoding." UserInfo=0x100109620 {NSFilePath=/Users/kenshincui/Desktop/test.txt, NSStringEncoding=1586}

//读取文件内容还有一种方式就是利用URl，它除了可以读取本地文件还可以读取网络文件
//NSURL *url=[NSURL URLWithString:@"file:///Users/kenshincui/Desktop/test.txt"];
NSURL *url=[NSURL URLWithString:@"http://www.apple.com"];
NSString *str3=[NSString stringWithContentsOfURL:url encoding:NSUTF8StringEncoding error:nil];
NSLog(@"str3 is %@",str3);
```

#### 文件写入

```objc
NSString *path1=@"/Users/kenshincui/Desktop/test2.txt";
NSError *error1;
NSString *str11=@"hello world,世界你好！";
[str11 writeToFile:path1 atomically:YES encoding:NSUTF8StringEncoding error:&error1];//automically代表一次性写入，如果写到中间出错了最后就全部不写入
if(error1){
    NSLog(@"write fail,the error is %@",[error1 localizedDescription]);//调用localizedDescription是只打印关键错误信息
}else{
    NSLog(@"write success!");
}
//结果：write success!
```

#### 路径操作

```objc
NSMutableArray *marray=[NSMutableArray array];//可变数组
[marray addObject:@"Users"];
[marray addObject:@"KenshinCui"];
[marray addObject:@"Desktop"];

NSString *path=[NSString pathWithComponents:marray];
NSLog(@"%@",path);//字符串拼接成路径
//结果：Users/KenshinCui/Desktop

NSLog(@"%@",[path pathComponents]);//路径分割成数组
/*结果： 
     (
        Users,
        KenshinCui,
        Desktop
    )
    */

NSLog(@"%i",[path isAbsolutePath]);//是否绝对路径（其实就是看字符串是否以“/”开头）
//结果：0
NSLog(@"%@",[path lastPathComponent]);//取得最后一个目录
//结果：Desktop
NSLog(@"%@",[path stringByDeletingLastPathComponent]);//删除最后一个目录，注意path本身是常量不会被修改,只是返回一个新字符串
//结果：Users/KenshinCui
NSLog(@"%@",[path stringByAppendingPathComponent:@"Documents"]);//路径拼接
//结果：Users/KenshinCui/Desktop/Documents
```

#### 扩展名操作

```objc
NSString *path=@"Users/KenshinCui/Desktop/test.txt";
NSLog(@"%@",[path pathExtension]);//取得扩展名，注意ObjC中扩展名不包括"."
//结果：txt
NSLog(@"%@",[path stringByDeletingPathExtension]);//删除扩展名，注意包含"."
//结果：Users/KenshinCui/Desktop/test
NSLog(@"%@",[@"Users/KenshinCui/Desktop/test" stringByAppendingPathExtension:@"mp3"]);//添加扩展名
//结果：Users/KenshinCui/Desktop/test.mp3
```

### 数组

#### 不可变数组（NSArray）

常用的数组操作：初始化、数组对象的方法执行、数组元素的遍历、在原有数组基础上产生新数组、数组排序等。

##### 初始化

```objc
 //NSArray长度不可变所以初始化的时候就赋值，并且最后以nil结尾
//此外需要注意NSArray不能存放C语言的基础类型
NSObject *obj=[[NSObject alloc]init];
//NSArray *array1=[[NSArray alloc] initWithObjects:@"abc",obj,@"cde",@"opq", nil];
NSArray *array1=[NSArray arrayWithObjects:@"abc",obj,@"cde",@"opq",@25, nil];
NSLog(@"%zi",array1.count);//数组长度,结果：5
NSLog(@"%i",[array1 containsObject:@"cde"]);//是否包含某个对象，结果：1
NSLog(@"%@",[array1 lastObject]);//最后一个对象，结果：25
NSLog(@"%zi",[array1 indexOfObject:@"abc"]);//对象所在的位置：0

Person *person1=[Person personWithName:@"Kenshin"];
Person *person2=[Person personWithName:@"Kaoru"];
Person *person3=[Person personWithName:@"Rosa"];
NSArray *array2=[[NSArray alloc]initWithObjects:person1,person2,person3, nil];
[array2 makeObjectsPerformSelector:@selector(showMessage:) withObject:@"Hello,world!"];//执行所有元素的showMessage方法,后面的参数最多只能有一个
/*结果：
     My name is Kenshin,the infomation is "Hello,world!".
     My name is Kaoru,the infomation is "Hello,world!".
     My name is Rosa,the infomation is "Hello,world!".
*/
```

##### 数组遍历

1. for(int i=0,len=array.count;i<len;++i)
2. for(id obj in array)
3. 利用代码块方法
4. 利用迭代器

```objc
NSObject *obj=[[NSObject alloc]init];
NSArray *array=[[NSArray alloc] initWithObjects:@"abc",obj,@"cde",@"opq",@25, nil];
//方法1
for(int i=0,len=array.count;i<len;++i){
    NSLog(@"method1:index %i is %@",i,[array objectAtIndex:i]);
}
/*结果：
     method1:index 0 is abc
     method1:index 1 is <NSObject: 0x100106de0>
     method1:index 2 is cde
     method1:index 3 is opq
     method1:index 4 is 25
     */


//方法2
for(id obj in array){
    NSLog(@"method2:index %zi is %@",[array indexOfObject:obj],obj);
}
/*结果：
     method2:index 0 is abc
     method2:index 1 is <NSObject: 0x100602f00>
     method2:index 2 is cde
     method2:index 3 is opq
     method2:index 4 is 25
     */


//方法3,利用代码块方法
[array enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
    NSLog(@"method3:index %zi is %@",idx,obj);
    if(idx==2){//当idx=2时设置*stop为YES停止遍历
        *stop=YES;
    }
}];
/*结果：
     method3:index 0 is abc
     method3:index 1 is <NSObject: 0x100106de0>
     method3:index 2 is cde
     */


//方法4，利用迭代器
//NSEnumerator *enumerator= [array objectEnumerator];//获得一个迭代器
NSEnumerator *enumerator=[array reverseObjectEnumerator];//获取一个反向迭代器
//NSLog(@"all:%@",[enumerator allObjects]);//获取所有迭代对象，注意调用完此方法迭代器就遍历完了，下面的nextObject就没有值了
id obj2=nil;
while (obj2=[enumerator nextObject]) {
    NSLog(@"method4:%@",obj2);
}
/*结果：
     method4:25
     method4:opq
     method4:cde
     method4:<NSObject: 0x100106de0>
     method4:abc
     */
```

##### 数组派生

```objc
NSArray *array=[NSArray arrayWithObjects:@"1",@"2",@"3", nil];
NSArray *array2=[array arrayByAddingObject:@"4"];//注意此时array并没有变
NSLog(@"%@",array2);
/*结果：
     (
         1,
         2,
         3,
         4
     )
     */


NSLog(@"%@",[array2 arrayByAddingObjectsFromArray:[NSArray arrayWithObjects:@"5",@"6", nil]]);//追加形成新的数组
/*结果：
     (
         1,
         2,
         3,
         4,
         5,
         6
     )
     */


NSLog(@"%@",[array2 subarrayWithRange:NSMakeRange(1, 3)]);//根据一定范围取得生成一个新的数组
/*结果：
     (
         2,
         3,
         4
     )
     */


NSLog(@"%@",[array componentsJoinedByString:@","]);//数组连接，形成一个字符串
//结果：1,2,3

//读写文件
NSString *path=@"/Users/KenshinCui/Desktop/array.xml";
[array writeToFile:path atomically:YES];
NSArray *array3=[NSArray arrayWithContentsOfFile:path];
NSLog(@"%@",array3);
/*结果：
     (
         1,
         2,
         3
     )
     */
```

##### 数组排序

1. 自带的比较器
2. 自己定义比较器
3. 使用代码块
4. 通过描述器定义排序规则

```objc
//方法1,使用自带的比较器
NSArray *array=[NSArray arrayWithObjects:@"3",@"1",@"2", nil];
NSArray *array2= [array sortedArrayUsingSelector:@selector(compare:)];
NSLog(@"%@",array2);
/*结果：
     (
         1,
         2,
         3
     )
     */


//方法2,自己定义比较器
Person *person1=[Person personWithName:@"Kenshin"];
Person *person2=[Person personWithName:@"Kaoru"];
Person *person3=[Person personWithName:@"Rosa"];
NSArray *array3=[NSArray arrayWithObjects:person1,person2,person3, nil];
NSArray *array4=[array3 sortedArrayUsingSelector:@selector(comparePerson:)];
NSLog(@"%@",array4);
/*结果：
     (
         "name=Kaoru",
         "name=Kenshin",
         "name=Rosa"
     )
     */


//方法3使用代码块
NSArray *array5=[array3 sortedArrayUsingComparator:^NSComparisonResult(Person *obj1, Person *obj2) {
    return [obj2.name compare:obj1.name];//降序
}];
NSLog(@"%@",array5);
/*结果：
     (
         "name=Rosa",
         "name=Kenshin",
         "name=Kaoru"
     )
     */


//方法4 通过描述器定义排序规则
Person *person4=[Person personWithName:@"Jack"];
Person *person5=[Person personWithName:@"Jerry"];
Person *person6=[Person personWithName:@"Tom"];
Person *person7=[Person personWithName:@"Terry"];
NSArray *array6=[NSArray arrayWithObjects:person4,person5,person6,person7, nil];
//定义一个排序描述
NSSortDescriptor *personName=[NSSortDescriptor sortDescriptorWithKey:@"name" ascending:YES];
NSSortDescriptor *accountBalance=[NSSortDescriptor sortDescriptorWithKey:@"account.balance" ascending:YES];
NSArray *des=[NSArray arrayWithObjects:personName,accountBalance, nil];//先按照person的name排序再按照account的balance排序
NSArray *array7=[array6 sortedArrayUsingDescriptors:des];
NSLog(@"%@",array7);
/*结果：
     (
         "name=Jack",
         "name=Jerry",
         "name=Terry",
         "name=Tom"
     )
     */
```

##### 总结

需要注意几点：
* **NSArray中只能存放对象**，不能存放基本数据类型，通常我们可以通过在基本数据类型前加@进行转换；
* 数组中的元素后面必须加nil以表示数据结束；
* makeObjectsPerformSelector执行数组中对象的方法，其参数最多只能有一个；
* 上面数组操作中无论是数组的追加、删除、截取都没有改变原来的数组，只是产生了新的数组而已；
* 对象的比较除了使用系统自带的方法，我们可以通过自定义比较器的方法来实现；



#### 可变数组(NSMutableArray)

##### 初始化

```objc
#import "Person.h"

Person *person1=[Person personWithName:@"Kenshin"];
Person *person2=[Person personWithName:@"Kaoru"];
Person *person3=[Person personWithName:@"Rosa"];

NSMutableArray *array = [[NSMutableArray alloc] init];
NSMutableArray *array1=[NSMutableArray arrayWithObjects:person1,person2,person3, nil];
NSLog(@"%@",array1);
/*结果：
     (
         "name=Kenshin",
         "name=Kaoru",
         "name=Rosa"
     )
     */
```

##### 添加元素

```objc
Person *person4=[Person personWithName:@"Jack"];//此时person4的retainCount为1
[array1 addObject:person4];//添加一个元素,此时person4的retainCount为2
NSLog(@"%@",array1);
/*结果：
     (
         "name=Kenshin",
         "name=Kaoru",
         "name=Rosa",
         "name=Jack"
     )
     */
```

##### 删除一个元素

```objc
[array1 removeObject:person3];//删除一个元素
NSLog(@"%@",array1);
/*结果：
     (
         "name=Kenshin",
         "name=Kaoru",
         "name=Jack"
     )
     */
```

##### 删除最后一个元素

```objc
[array1 removeLastObject];//删除最后一个元素,//此时person4的retainCount为1
NSLog(@"%@",array1);
/*结果：
     (
         "name=Kenshin",
         "name=Kaoru"
     )
     */
```

##### 删除所有元素

```objc
[array1 removeAllObjects];//删除所有元素
//注意当往数组中添加一个元素时会retain因此计数器+1，当从数组中移除一个元素时会release因此计数器-1
//当NSMutalbeArray对象release的时候会依次调用每一个对象的release
```

##### 排序

1. sortedArrayUsingSelector
2. sortUsingSelector

```objc
NSMutableArray *array1=[NSMutableArray arrayWithObjects:@"1",@"3",@"2", nil];
NSLog(@"%@",array1);
/*结果：
     (
         1,
         3,
         2
     )
     */

NSArray *array2= [array1 sortedArrayUsingSelector:@selector(compare:)];//注意这个方法没有修改array1
NSLog(@"%@",array1);
/*结果：
     (
         1,
         3,
         2
     )
     */

NSLog(@"%@",array2);
/*结果：
     (
         1,
         2,
         3
     )
     */
[array1 sortUsingSelector:@selector(compare:)];//这个方法会修改array1
NSLog(@"%@",array1);
/*结果：
     (
         1,
         2,
         3
     )
     */
```

##### 总结
1. 可变数组中的元素后面必须加nil以表示数据结束；
2. 往一个可变数组中添加一个对象，此时这个对象的引用计数器会加1，当这个对象从可变数组中移除其引用计数器减1。同时当整个数组销毁之后会依次调用每个对象的releaes方法。
3. 在不可变数组中无论对数组怎么排序，原来的数组顺序都不会改变，但是在可变数组中如果使用sortUsingSelector:排序原来的数组顺序就发生了变化。

### 字典(NSDictionary & NSMutableDictionary)

字典在我们日常开发中也是比较常用的，通过下面的代码我们看一下在ObjC中的字典的常用操作：初始化、遍历、排序

> **注意：同数组一样，不管是可变字典还是不可变字典初始化元素后面必须加上nil以表示结束。**

#### 初始化(NSDictionary)

```objc
NSDictionary *dic1=[NSDictionary dictionaryWithObject:@"1" forKey:@"a"];
NSLog(@"%@",dic1);
/*结果：
     {
        a = 1;
     }
     */

//常用的方式
NSDictionary *dic2=[NSDictionary dictionaryWithObjectsAndKeys:
                    @"1",@"a",
                    @"2",@"b",
                    @"3",@"c",
                    nil];
NSLog(@"%@",dic2);
/*结果：
     {
         a = 1;
         b = 2;
         c = 3;
     }
     */


NSDictionary *dic3=[NSDictionary dictionaryWithObjects:[NSArray arrayWithObjects:@"1",@"2", nil] forKeys:[NSArray arrayWithObjects:@"a",@"b", nil]];
NSLog(@"%@",dic3);
/*结果：
     {
         a = 1;
         b = 2;
     }
     */


//更简单的方式
NSDictionary *dic4=@{@"1":@"a",@"2":@"b",@"3":@"c"};
NSLog(@"%@",dic4);
/*结果：
     {
         1 = a;
         2 = b;
         3 = c;
     }
     */
```

#### 字典长度、取值

```objc
NSDictionary *dic1=[NSDictionary dictionaryWithObjectsAndKeys:
                    @"1",@"a",
                    @"2",@"b",
                    @"3",@"c",
                    @"2",@"d",
                    nil];
NSLog(@"%zi",[dic1 count]); //结果：4
NSLog(@"%@",[dic1 valueForKey:@"b"]);//根据键取得值，结果：2
NSLog(@"%@",dic1[@"b"]);//还可以这样读取，结果：2
NSLog(@"%@,%@",[dic1 allKeys],[dic1 allValues]);
/*结果：
     (
         d,
         b,
         c,
         a
     ),(
         2,
         2,
         3,
         1
     )

     */

NSLog(@"%@",[dic1 objectsForKeys:[NSArray arrayWithObjects:@"a",@"e" , nil]notFoundMarker:@"not fount"]);//后面一个参数notFoundMarker是如果找不到对应的key用什么值代替
/*结果：
     (
         1,
         "not fount"
     )
     */
```

#### 遍历

```objc
NSDictionary *dic1=[NSDictionary dictionaryWithObjectsAndKeys:
                    @"1",@"a",
                    @"2",@"b",
                    @"3",@"c",
                    @"2",@"d",
                    nil];
//遍历1
for (id key in dic1) {//注意对于字典for遍历循环的是key
    NSLog(@"%@=%@",key,[dic1 objectForKey:key]);
}
/*结果：
     d=2
     b=2
     c=3
     a=1
     */

//遍历2
NSEnumerator *enumerator=[dic1 keyEnumerator];//还有值的迭代器[dic1 objectEnumerator]
id key=nil;
while (key=[enumerator nextObject]) {
    NSLog(@"%@=%@",key,[dic1 objectForKey:key]);

}
/*结果：
     d=2
     b=2
     c=3
     a=1
     */

//遍历3
[dic1 enumerateKeysAndObjectsUsingBlock:^(id key, id obj, BOOL *stop) {
    NSLog(@"%@=%@",key,obj);
}];
/*结果：
     d=2
     b=2
     c=3
     a=1
     */
```

#### 可变字典(NSMutableDictionary)

```objc
NSMutableDictionary *dic=[NSMutableDictionary dictionaryWithObjectsAndKeys:@"1",@"a",
                          @"2",@"b",
                          @"3",@"c",
                          @"2",@"d",
                          nil];
[dic removeObjectForKey:@"b"];
NSLog(@"%@",dic);
/*结果：
     {
         a = 1;
         c = 3;
         d = 2;
     }
     */

[dic addEntriesFromDictionary:@{@"e":@"7",@"f":@"6"}];
NSLog(@"%@",dic);
/*结果：
     {
         a = 1;
         c = 3;
         d = 2;
         e = 7;
         f = 6;
     }
     */

[dic setValue:@"5" forKey:@"a"];
NSLog(@"%@",dic);
/*结果：
     {
         a = 5;
         c = 3;
         d = 2;
         e = 7;
         f = 6;
     }
     */


//注意，一个字典的key或value添加到字典中时计数器+1；字典释放时调用key或value的release一次，计数器-1
```



### 装箱和拆箱

#### 概念

​	其实从上面的例子中我们也可以看到，数组和字典中只能存储对象类型，其他基本类型和结构体是没有办法放到数组和字典中的，当然你也是无法给它们发送消息的（也就是说有些NSObject的方法是无法调用的），这个时候通常会用到装箱（boxing）和拆箱（unboxing）。其实各种高级语言基本上都有装箱和拆箱的过程，例如C#中我们将基本数据类型转化为Object就是一个装箱的过程，将这个Object对象转换为基本数据类型的过程就是拆箱，而且在C#中装箱的过程可以自动完成，基本数据类型可以直接赋值给Object对象。但是在ObjC中装箱的过程必须手动实现，ObjC不支持自动装箱。

#### 装箱（基本数据类型转NSObject）

在ObjC中我们一般将基本数据类型装箱成NSNumber类型（当然它也是NSObject的子类，但是NSNumber不能对结构体装箱），调用其对应的方法进行转换：

**+(NSNumber \*)numberWithChar:(char)value;**

**+(NSNumber \*)numberWithInt:(int)value;**

**+(NSNumber \*)numberWithFloat:(float)value;**

**+(NSNumber \*)numberWithDouble:(double)value;**

**+(NSNumber \*)numberWithBool:(BOOL)value;**

**+(NSNumber \*)numberWithInteger:(NSInteger)value;**

```objc
//包装类NSNumber，可以包装基本类型但是无法包装结构体类型
NSNumber *number1=[NSNumber numberWithChar:'a'];//'a'是一个C语言的char类型我们无法放倒NSArray中，但是我们可以通过NSNumber包装
NSArray *array1=[NSArray arrayWithObject:number1];
NSLog(@"%@",array1);
/*结果：
     (
        97
     )
     */

NSNumber *number2= [array1 lastObject];
NSLog(@"%@",number2);//返回的不是基本类型,结果：97
```



#### 拆箱（NSObject转基本数据类型）

拆箱的过程就更加简单了，可以调用如下方法：

**-(char)charValue;**

**-(int)intValue;**

**-(float)floatValue;**

**-(double)doubleValue;**

**-(BOOL)boolValue;**

```objc
char char1=[number2 charValue];//number转化为char
NSLog(@"%c",char1); //结果：a
```



#### 常用结构体装箱拆线

这个时候我们需要引入另外一个类型NSValue，其实上面的NSNumber就是NSValue的子类，它包装了一些基本数据类型的常用装箱、拆箱方法，当要对结构体进行装箱、拆箱操作我们需要使用NSValue，NSValue可以对任何数据类型进行装箱、拆箱操作。

对于常用的结构体Foundation已经为我们提供好了具体的装箱方法：

**+(NSValue *)valueWithPoint:(NSPoint)point;**

**+(NSValue *)valueWithSize:(NSSize)size;**

**+(NSValue *)valueWithRect:(NSRect)rect;**

对应的拆箱方法：

**-(NSPoint)pointValue;**

**-(NSSize)sizeValue;**

**-(NSRect)rectValue;**

```objc
CGPoint point1=CGPointMake(10, 20);
NSValue *value1=[NSValue valueWithPoint:point1];//对于系统自带类型一般都有直接的方法进行包装
NSArray *array1=[NSArray arrayWithObject:value1];//放倒数组中
NSLog(@"%@",array1);
/*结果：
     (
        "NSPoint: {10, 20}"
     )
     */

NSValue *value2=[array1 lastObject];
CGPoint point2=[value2 pointValue];//同样对于系统自带的结构体有对应的取值方法（例如本例pointValue）
NSLog(@"x=%f,y=%f",point2.x,point2.y);//结果：x=10.000000,y=20.000000
```



#### 自定义结构图装箱拆线

这个时候我们需要使用NSValue如下方法进行装箱：

**+(NSValue \*)valueWithBytes:(const void \*)value objCType:(const char \*)type;**

调用下面的方法进行拆箱：

**-(void)getValue:(void \*)value;**

```objc
#import <Foundation/Foundation.h>

typedef struct {
    int year;
    int month;
    int day;
} Date;


//NSNumber是NSValue的子类，而NSValue可以包装任何类型，包括结构体
void test1(){
    //如果我们自己定义的结构体包装
    Date date={2014,2,28};
    char *type=@encode(Date);
    NSValue *value3=[NSValue value:&date withObjCType:type];//第一参数传递结构体地址，第二个参数传递类型字符串
    NSArray *array2=[NSArray arrayWithObject:value3];
    NSLog(@"%@",array2);
    /*结果：
     (
        "<de070000 02000000 1c000000>"
     )
     */
    
    Date date2;
    [value3 getValue:&date2];//取出对应的结构体，注意没有返回值
    //[value3 objCType]//取出包装内容的类型
    NSLog(@"%i,%i,%i",date2.year,date2.month,date2.day); //结果：2014,2,28
    
}


int main(int argc, const char * argv[]) {
    test1();
    return  0;
}
```



### 反射

由于ObjC动态性，在ObjC中实现反射可以说是相当简单，下面代码中演示了常用的反射操作，具体作用也都在代码中进行了注释说明:

Account.h

```objc
#import <Foundation/Foundation.h>

@interface Account : NSObject

@property (nonatomic,assign) double balance;

@end
```

Account.m

```objc
#import "Account.h"

@implementation Account

@end
```

Person.h

```objc
#import <Foundation/Foundation.h>
@class Account;

@interface Person : NSObject

@property (nonatomic,copy) NSString *name;
@property (nonatomic,retain) Account *account;

-(Person *)initWithName:(NSString *)name;

+(Person *)personWithName:(NSString *)name;

-(void)showMessage:(NSString *)infomation;

//自己实现对象比较方法
-(NSComparisonResult)comparePerson:(Person *)person;
@end
```

Person.m

```objc
#import "Person.h"

@implementation Person

-(Person *)initWithName:(NSString *)name{
    if(self=[super init]){
        self.name=name;
    }
    return self;
}

+(Person *)personWithName:(NSString *)name{
    Person *person=[[Person alloc]initWithName:name];
    return person;
}

-(void)showMessage:(NSString *)infomation{
    NSLog(@"My name is %@,the infomation is \"%@\".",_name,infomation);
}

//自己实现对象比较方法
-(NSComparisonResult)comparePerson:(Person *)person{
    return [_name compare:person.name];
}

-(NSString *)description{
    return [NSString stringWithFormat:@"name=%@",_name];
}

@end
```

main.m

```objc
#import <Foundation/Foundation.h>
#import "Person.h"


int main(int argc, const char * argv[]) {
    /*常用方法*/
    Person *person1=[Person personWithName:@"Kenshin"];
    NSLog(@"%i",[person1 isKindOfClass:[NSObject class]]); //判断一个对象是否为某种类型（如果是父类也返回YES），结果：1
    NSLog(@"%i",[person1 isMemberOfClass:[NSObject class]]); //判断一个对象是否是某个类的实例化对象，结果：0
    NSLog(@"%i",[person1 isMemberOfClass:[Person class]]); //结果：1
    NSLog(@"%i",[person1 conformsToProtocol:@protocol(NSCopying)]);//是否实现了某个协议，结果：0
    NSLog(@"%i",[person1 respondsToSelector:@selector(showMessage:)]);//是否存在某个方法，结果：1
    
    [person1 showMessage:@"Hello,world!"];//直接调用一个方法
    [person1 performSelector:@selector(showMessage:) withObject:@"Hello,world!"];
    //动态调用一个方法，注意如果有参数那么参数类型只能为ObjC对象，并且最多只能有两个参数

    
    /*反射*/
    //动态生成一个类
    NSString *className=@"Person";
    Class myClass=NSClassFromString(className);//根据类名生成类
    Person *person2=[[myClass alloc]init]; //实例化
    person2.name=@"Kaoru";
    NSLog(@"%@",person2);//结果：name=Kaoru

    //类转化为字符串
    NSLog(@"%@,%@",NSStringFromClass(myClass),NSStringFromClass([Person class])); //结果：Person,Person

    //调用方法
    NSString *methodName=@"showMessage:";
    SEL mySelector=NSSelectorFromString(methodName);
    Person *person3=[[myClass alloc]init];
    person3.name=@"Rosa";
    [person3 performSelector:mySelector withObject:@"Hello,world!"]; //结果：My name is Rosa,the infomation is "Hello,world!".

    //方法转化为字符串
    NSLog(@"%@",NSStringFromSelector(mySelector)); //结果：showMessage:
    
    return 0;
}
```



### 拷贝

对象拷贝操作也比较常见，在ObjC中有两种方式的拷贝：copy和mutablecopy，这两种方式都将产生一个新的对象，只是后者产生的是一个可变对象。在ObjC中如果要想实现copy或者mutablecopy操作需要实现NSCopy或者NSMutableCopy协议，拷贝操作产生的新的对象默认引用计数器是1，在非ARC模式下我们应该对这个对象进行内存管理。在熟悉这两种操作之前我们首先需要弄清两个概念:深复制（或深拷贝）和浅复制（或浅拷贝）。

* 浅复制：在执行复制操作时，对于对象中每一层（对象中包含的对象，例如说属性是某个对象类型）复制都是指针复制（如果从引用计数器角度出发，那么每层对象的引用计数器都会加1）。

* 深复制：在执行复制操作时，至少有一个对象的复制是对象内容复制（如果从引用计数器角度出发，那么除了对象内容复制的那个对象的引用计数器不变，其他指针复制的对象其引用计数器都会加1）。

> **注：**
>
> **指针拷贝：拷贝的是指针本身（也就是具体对象的地址）而不是指向的对象内容本身。**
>
> **对象复制：对象复制指的是复制内容是对象本身而不是对象的地址。**
>
> **完全复制：上面说了深复制和浅复制，既然深复制是至少一个对象复制是对象内容复制，那么如果所有复制都是对象内容复制那么这个复制就叫完全复制。**

对比copy和mutablecopy其实前面我们一直还用到一个操作是retain，它们之间的关系如下：

1. retain：始终采取浅复制，引用计数器会加1，返回的对象和被复制对象是同一个对象1（也就是说这个对象的引用多了一个，或者说是指向这个对象的指针多了一个）；

2. copy：对于不可变对象copy采用的是浅复制，引用计数器加1（其实这是编译器进行了优化，既然原来的对象不可变，复制之后的对象也不可变那么就没有必要再重新创建一个对象了）；对于可变对象copy采用的是深复制，引用计数器不变（原来的对象是可变，现在要产生一个不可变的当然得重新产生一个对象）；

3. mutablecopy：无论是可变对象还是不可变对象采取的都是深复制，引用计数器不变（如果从一个不可变对象产生一个可变对象自然不用说两个对象绝对不一样肯定是深复制；如果从一个可变对象产生出另一个可变对象，那么当其中一个对象改变自然不希望另一个对象改变，当然也是深复制）。

> **注：**
>
> **可变对象：当值发生了改变，那么地址也随之发生改变；**
>
> **不可变对象：当值发生了改变，内容首地址不发生变化；**
>
> **引用计数器：用于计算一个对象有几个指针在引用（有几个指针变量指向同一个内存地址）；**

 

```objc
#import <Foundation/Foundation.h>


void test1(){
    NSString *name=@"Kenshin";
    NSString *str1=[NSString stringWithFormat:@"I'm %@.",name];//注意此时str1的计数器是1
    NSLog(@"%lu",[str1 retainCount]); //结果：1
    
    
    NSMutableString *str2=[str1 mutableCopy];//注意此时str2的计数器为1，str1的计数器还是1
    //NSMutableString *str5 =CFRetain((__bridge CFTypeRef)str2);
    NSLog(@"retainCount(str1)=%lu,retainCount(str2)=%lu",[str1 retainCount],[str2 retainCount]);
    //结果：retainCount(str1)=1,retainCount(str2)=1
    
    
    [str2 appendString:@"def"];//改变str2，str1不变
    NSLog(@"%zi",str1==str2);//二者不是向同一个对象,结果：0
    NSLog(@"str1=%@",str1); //结果：str1=I'm Kenshin.
    NSLog(@"str2=%@",str2); //结果：str2=I'm Kenshin.def
    
    
    NSLog(@"str1's %lu",[str1 retainCount]);
    NSString *str3=[str1 copy];//str3不是产生的新对象而是复制了对象指针，但是str1的计数器+1（当然既然str3同样指向同一个对象，那么如果计算str3指向的对象引用计数器肯定等于str1的对象引用计数器）
    NSLog(@"%zi",str1==str3);//二者相等指向同一个对象,结果：1
    NSLog(@"retainCount(str1)=%lu,retainCount(str3)=%lu",str1.retainCount,str3.retainCount);
    //结果：retainCount(str1)=2,retainCount(str3)=2
    
    //需要注意的是使用copy和mutableCopy是深复制还是浅复制不是绝对，关键看由什么对象产生什么样的对象
    NSString *str4=[str2 copy];//由NSMutableString产生了NSString，二者类型都不同肯定是深拷贝，此时str2的计数器还是1，str4的计数器也是1
    [str2 appendString:@"g"];//改变原对象不影响str4
    NSLog(@"%zi",str2==str4); //结果：0
    NSLog(@"str2=%@",str2); //结果：str2=I'm Kenshin.defg
    NSLog(@"str4=%@",str4); //结果：str4=I'm Kenshin.def

    
    [str1 release];
    str1=nil;
    [str3 release];//其实这里也可以调用str1再次release，因为他们两个指向的是同一个对象（但是一般不建议那么做，不容易理解）
    str3=nil;
    
    [str2 release];
    str2=nil;
    [str4 release];
    str4=nil;
    
    //上面只有一种情况是浅拷贝：不可变对象调用copy方法
    
}

int main(int argc,char *argv[]){
    test1();
    return 0;
}
```

![4-6-1](.\img\4-6-1.png)

从上面可以清楚的看到str1和str3同时指向同一个对象，因此这个对象的引用计数器是2(可以看到两箭头指向那个对象)，str2和str4都是两个新的对象；另外ObjC引入对象拷贝是为了改变一个对象不影响另一个对象，但是我们知道NSString本身就不能改变那么即使我重新复制一个对象也没有任何意义，因此为了性能着想如果通过copy方法产生一个NSString时ObjC不会再复制一个对象而是将新变量指向同一个对象。

> **注意网上很多人支招在ARC模式下可以利用_objc_rootRetainCount()或者CFGetRetainCount()取得retainCount都是不准确的，特别是在对象拷贝操作之后你会发现二者取值也是不同的，因此如果大家要查看retainCount最好还是暂时关闭ARC。**

要想支持copy或者mutablecopy操作那么对象必须实现NSCoping协议并实现**-(id)copyWithZone:(NSZone\*)zone**方法，在Foundation中常用的可复制对象有：NSNumber、NSString、NSMutableString、NSArray、NSMutableArray、NSDictionary、NSMutableDictionary。下面看一下如何让自定义的类支持copy操作：

Person.h

```objc
#import <Foundation/Foundation.h>
@class Account;

@interface Person : NSObject

@property  NSMutableString *name;
@property (nonatomic,assign) int age;


@end
```

Person.m

```objc
#import "Person.h"

@implementation Person


-(NSString *)description{
    return [NSString stringWithFormat:@"name=%@,age=%i",_name,_age];
}

//实现copy方法
-(id)copyWithZone:(NSZone *)zone{
    //注意zone是系统已经分配好的用于存储当前对象的内存
    //注意下面创建对象最好不要用[[Person allocWithZone:zone]init]，因为子类如果没有实现该方法copy时会调用父类的copy方法，此时需要使用子类对象初始化如果此时用self就可以表示子类对象，还有就是如果子类调用了父类的这个方法进行重写copy也需要调用子类对象而不是父类Person
    Person *person1=[[[self class] allocWithZone:zone]init];
    person1.name=_name;
    person1.age=_age;
    return person1;
}

@end
```

main.m

```objc
#import <Foundation/Foundation.h>
#import "Account.h"
#import "Person.h"

void test1(){
    Person *person1=[[Person alloc]init];
    NSMutableString *str1=[NSMutableString stringWithString:@"Kenshin"];
    person1.name=str1;
    //由于name定义的时候使用属性参数采用的是copy策略，而根据前面的知识我们知道NSMutableString的copy策略采用的是对象内容复制，因此如果修改str1不会改变person1.name
    [str1 appendString:@" Cui"];
    NSLog(@"%@",str1);//结果：Kenshin Cui
    NSLog(@"%@",person1.name); //结果：Kenshin
    
}

void test2(){
    Person *person1=[[Person alloc]init];
    person1.name=[NSMutableString stringWithString:@"Kenshin"];
    person1.age=28;
    Person *person2=[person1 copy];
    NSLog(@"%@",person1); //结果：name=Kenshin,age=0
    NSLog(@"%@",person2); //结果：name=Kenshin,age=0
    
    [person2.name appendString:@" Cui"];
    
    NSLog(@"%@",person1);//结果：name=Kenshin Cui,age=28
    NSLog(@"%@",person2);//结果：name=Kenshin Cui,age=28
}

int main(int argc,char *argv[]){
    test1();
    test2();
    return 0;
}
```

![4-6-2](.\img\4-6-2.png)

上面test2的写法纯属为了让大家了解复制的原理和本质，实际开发中我们很少会遇到这种情况，首先我们一般定义name的话可能用的是NSString类型，根本也不能修改；其次我们定义字符串类型的话一般使用(copy)参数，同样可以避免这个问题（因为NSMutableString的copy是深复制）。那么如果我们非要使用NSMutabeString同时不使用属性的copy参数如何解决这个问题呢？答案就是使用深复制，将-(id)copyWithZone:(NSZone *)zone方法中person1.name=_name改为，person1.name=[_name copy];或person1.name=[_name mutablecopy]即可，这样做也正好满足我们上面对于深复制的定义。



### 文件操作

```objc
//
//  main.m
//  FoundationFramework
//
//  Created by Kenshin Cui on 14-2-16.
//  Copyright (c) 2014年 Kenshin Cui. All rights reserved.
//

#import <Foundation/Foundation.h>

/*目录操作*/
void test1(){
    //文件管理器是专门用于文件管理的类
    NSFileManager *manager=[NSFileManager defaultManager];
    
    //获得当前程序所在目录(当然可以改变)
    NSString *currentPath=[manager currentDirectoryPath];
    NSLog(@"current path is :%@",currentPath);
    //结果：/Users/kenshincui/Library/Developer/Xcode/DerivedData/FoundationFramework-awxjohcpgsqcpsanqofqogwbqgbx/Build/Products/Debug
    
    //创建目录
    NSString *myPath=@"/Users/kenshincui/Desktop/myDocument";
    BOOL result= [manager createDirectoryAtPath:myPath withIntermediateDirectories:YES attributes:nil error:nil];
    if(result==NO){
        NSLog(@"Couldn't create directory!");
    }
    
    //目录重命名，如果需要删除目录只要调用removeItemAtPath:<#(NSString *)#> error:<#(NSError **)#>
    NSError *error;
    NSString *newPath=@"/Users/kenshincui/Desktop/myNewDocument";
    if([manager moveItemAtPath:myPath toPath:newPath error:&error]==NO){
        NSLog(@"Rename directory failed!Error infomation is:%@",error);
    }
    
    //改变当前目录
    if([manager changeCurrentDirectoryPath:newPath]==NO){
        NSLog(@"Change current directory failed!");
    }
    NSLog(@"current path is :%@",[manager currentDirectoryPath]);
    //结果：current path is :/Users/kenshincui/Desktop/myNewDocument
    
    //遍历整个目录
    NSString *path;
    NSDirectoryEnumerator *directoryEnumerator= [manager enumeratorAtPath:newPath];
    while (path=[directoryEnumerator nextObject]) {
        NSLog(@"%@",path);
    }
    /*结果：
     documents
     est.txt
    */
    
    //或者这样遍历
    NSArray *paths= [manager contentsOfDirectoryAtPath:newPath error:nil];
    NSObject *p;
    for (p in paths) {
        NSLog(@"%@",p);
    }
    /*结果：
     documents
     est.txt
     */
}

/*文件操作*/
void test2(){
    NSFileManager *manager=[NSFileManager defaultManager];
    NSString *filePath=@"/Users/kenshincui/Desktop/myNewDocument/test.txt";
    NSString *filePath2=@"/Users/kenshincui/Desktop/test.txt";
    NSString *newPath=@"/Users/kenshincui/Desktop/myNewDocument/test2.txt";
    
    //判断文件是否存在，这个方法也可以判断目录是否存在，这要后面的参数设置位YES
    if ([manager fileExistsAtPath:filePath isDirectory:NO]) {
        NSLog(@"File exists！");
    }
    
    //文件是否可读
    if([manager isReadableFileAtPath:filePath]){
        NSLog(@"File is readable!");
    }
    
    //判断两个文件内容是否相等
    if ([manager contentsEqualAtPath:filePath andPath:filePath2]) {
        NSLog(@"file1 equals file2");
    }
    
    //文件重命名，方法类似于目录重命名
    if (![manager moveItemAtPath:filePath toPath:newPath error:nil]) {
        NSLog(@"Rename file1 failed!");
    }
    
    //文件拷贝
    NSString *filePath3=@"/Users/kenshincui/Desktop/test3.txt";
    if(![manager copyItemAtPath:newPath toPath:filePath3 error:nil]){
        NSLog(@"Copy failed!");
    }
    
    //读取文件属性
    NSDictionary *attributes;
    if ((attributes=[manager attributesOfItemAtPath:newPath error:nil])==nil) {
        NSLog(@"Read attributes failed!");
    }
    for (NSObject *key in attributes) {
        NSLog(@"%@=%@",key,attributes[key]);
    }
    /*结果：
         NSFileOwnerAccountID=501
         NSFileHFSTypeCode=0
         NSFileSystemFileNumber=1781953
         NSFileExtensionHidden=0
         NSFileSystemNumber=16777218
         NSFileSize=27
         NSFileGroupOwnerAccountID=20
         NSFileOwnerAccountName=kenshincui
         NSFileCreationDate=2014-07-28 11:47:58 +0000
         NSFilePosixPermissions=420
         NSFileHFSCreatorCode=0
         NSFileType=NSFileTypeRegular
         NSFileExtendedAttributes={
         "com.apple.TextEncoding" = <7574662d 383b3133 34323137 393834>;
         }
         NSFileGroupOwnerAccountName=staff
         NSFileReferenceCount=1
         NSFileModificationDate=2014-07-28 11:47:58 +0000
     */
    
    //删除文件
    [manager removeItemAtPath:newPath error:nil];
    
}
//文件操作--文件内容操作（NSData，非结构化字节流对象，有缓冲区管理机制，可用于网络传输）
void test3(){
    NSFileManager *manager=[NSFileManager defaultManager];
    NSString *filePath=@"/Users/kenshincui/Desktop/myNewDocument/test2.txt";
    NSData *data=[manager contentsAtPath:filePath];
    NSLog(@"%@",data);//存储的是二进制字节流
    //结果:<68656c6c 6f20776f 726c642c e4b896e7 958ce4bd a0e5a5bd efbc81>
    
    //NSData转化成字符串
    NSString *str1=[[NSString alloc]initWithData:data encoding:NSUTF8StringEncoding];
    NSLog(@"%@",str1);
    //结果：hello world,世界你好！
    
    //字符串转化成NSData
    NSString *str2=@"Kenshin";
    NSData *data2=[str2 dataUsingEncoding:NSUTF8StringEncoding];
    NSLog(@"%@",data2);
    
    //当然一般如果仅仅是简单读取文件内容，直接用户NSString方法即可
    NSString *content=[NSString stringWithContentsOfFile:filePath encoding:NSUTF8StringEncoding error:nil];
    NSLog(@"%@",content);
    //结果：hello world,世界你好！
    
}
//文件操作--细粒度控制文件,文件操作柄
void test4(){
    NSFileManager *manager=[NSFileManager defaultManager];
    NSString *filePath=@"/Users/kenshincui/Desktop/myNewDocument/test2.txt";
    
    //以只读方式打开文件
    NSFileHandle *fileHandle=[NSFileHandle fileHandleForReadingAtPath:filePath];//注意这个方法返回类型为instancetype，也就是说对于上面的NSFileHandle它的返回类型也是NSFileHandle
    NSData *data= [fileHandle readDataToEndOfFile];//完整读取文件
    NSString *newPath=@"/Users/kenshincui/Desktop/test4.txt";
    [manager createFileAtPath:newPath contents:nil attributes:nil];
    NSFileHandle *fileHandle2=[NSFileHandle fileHandleForWritingAtPath:newPath];//以可写方式打开文件
    [fileHandle2 writeData:data];//写入文件内容
    
    [fileHandle2 closeFile];//关闭文件

    
    //定位到指定位置,默认在文件开头
    [fileHandle seekToFileOffset:12];
    NSData *data2= [fileHandle readDataToEndOfFile];
    NSLog(@"data2=%@",[[NSString alloc]initWithData:data2 encoding:NSUTF8StringEncoding]);
    //结果：data2=世界你好！
    
    [fileHandle seekToFileOffset:6];
    NSData *data3=[fileHandle readDataOfLength:5];
    NSLog(@"data3=%@",[[NSString alloc]initWithData:data3 encoding:NSUTF8StringEncoding]);
    //结果：data3=world
    
    [fileHandle closeFile];
    
}

//文件路径
void test5(){
    NSString *filePath=@"/Users/kenshincui/Desktop/myDocument";
    NSString *filePath2=@"/Users/kenshincui/Desktop/test.txt";

    //临时文件所在目录
    NSString *path=NSTemporaryDirectory();
    NSLog(@"temporary directory is :%@",path);
    //结果：/var/folders/h6/lss6gncs509c2pgzgty3wd_40000gn/T/

    NSString *lastComponent= [filePath lastPathComponent];
    NSLog(@"%@",lastComponent); //结果：myDocument
    
    NSLog(@"%@",[filePath stringByDeletingLastPathComponent]);
    //结果：/Users/kenshincui/Desktop
    NSLog(@"%@",[filePath stringByAppendingPathComponent:@"Pictrues"]);
    //结果：/Users/kenshincui/Desktop/myDocument/Pictrues
    NSLog(@"%@",[filePath2 pathExtension]);
    //结果：txt
    
    [[filePath pathComponents] enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
        NSLog(@"%i=%@",idx,obj);
    }];
    /*结果：
     0=/
     1=Users
     2=kenshincui
     3=Desktop
     4=myDocument
     */
    
    
}

//文件操作--NSURL
void test6(){
    NSURL *url=[NSURL URLWithString:@"http://developer.apple.com"];
    NSString *str1=[NSString stringWithContentsOfURL:url encoding:NSUTF8StringEncoding error:nil];
    NSLog(@"%@",str1);
}

//文件操作--NSBundle，程序包，一般用于获取Resource中的资源（当然由于当前并非IOS应用没有程序包，只是表示当前程序运行路径）
//在ios中经常用于读取应用程序中的资源文件，如图片、声音、视频等
void test7(){
    //在程序包所在目录创建一个文件
    NSFileManager *manager=[NSFileManager defaultManager];
    NSString *currentPath=[manager currentDirectoryPath];
    NSLog(@"current path is :%@",currentPath);
    //结果：current path is :/Users/kenshincui/Library/Developer/Xcode/DerivedData/FoundationFramework-awxjohcpgsqcpsanqofqogwbqgbx/Build/Products/Debug
    NSString *filePath=[currentPath stringByAppendingPathComponent:@"test.txt"];
    [manager createFileAtPath:filePath contents:[@"Hello,world!" dataUsingEncoding:NSUTF8StringEncoding] attributes:nil];
    
    
    //利用NSBundle在程序包所在目录查找对应的文件
    NSBundle *bundle=[NSBundle mainBundle];//主要操作程序包所在目录
    //如果有test.txt则返回路径，否则返回nil
    NSString *path=[bundle pathForResource:@"test" ofType:@"txt"];//也可以写成：[bundle pathForResource:@"instructions.txt" ofType:nil];
    NSLog(@"%@",path);
    //结果：/Users/kenshincui/Library/Developer/Xcode/DerivedData/FoundationFramework-awxjohcpgsqcpsanqofqogwbqgbx/Build/Products/Debug/test.txt
    NSLog(@"%@",[NSString stringWithContentsOfFile:path encoding:NSUTF8StringEncoding error:nil]);
    //结果：Hello,world!
    
    //假设我们在程序运行创建一个Resources目录，并且其中新建pic.jpg，那么用下面的方法获得这个文件完整路径
    NSString *path1= [bundle pathForResource:@"pic" ofType:@"jpg" inDirectory:@"Resources"];
    NSLog(@"%@",path1);
    //结果：/Users/kenshincui/Library/Developer/Xcode/DerivedData/FoundationFramework-awxjohcpgsqcpsanqofqogwbqgbx/Build/Products/Debug/Resources/pic.jpg
}

int main(int argc,char *argv[]){

    test1();
    test2();
    test3();
    test4();
    test5();
    test6();
    test7();
    
    return 0;
}
```



### 归档

归档，在其他语言中又叫“序列化”，就是将对象保存到硬盘；解档，在其他语言又叫“反序列化”就是将硬盘文件还原成对象。其实归档就是数据存储的过程，在IOS中数据的存储有五种方式：

1. xml属性列表（plist归档）
2. NSUserDefaults（偏好设置）
3. NSKeyedArchiver归档（加密形式）
4. SQLite3(嵌入式数据库)
5. Core Data（面向对象方式的嵌入式数据库）

当然关于2、4、5点不是我们今天介绍的重点，这个在IOS开发过程中我们会重点说到。

#### xml属性列表

首先我们先来看一下xml属性列表，xml属性列表进行归档的方式是将对象存储在一个plist文件中，这个操作起来比较简单，其实相当于xml序列化。但是同时它也有缺点：一是这种方式是明文保存的；二是这种方式操作的对象有限，只有NSArray、NSMutableArray、NSDictionary、NSMutableDictionary支持（归档时只要调用对应的writeToFile方法即可，解档调用arrayWithContentsOfFile或dictionaryWithContentsOfFile，注意像NSString、NSNumber、NSData即使有这个方法它存储的也不是xml格式）。

```objc
#import <Foundation/Foundation.h>

//xml属性
void test1(){
    //数组
    NSString *path=@"/Users/kenshincui/Desktop/arrayXml.plist";
    NSArray *array1=@[@"Kenshin",@"Kaoru",@"Rosa"];
    [array1 writeToFile:path atomically:YES];
    
    NSArray *array2=[NSArray arrayWithContentsOfFile:path];
    [array2 enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
        NSLog(@"array2[%lu]=%@",idx,obj);
    }];
    /*结果：
     array1[0]=Kenshin
     array1[1]=Kaoru
     array1[2]=Rosa
     */
    
    
    //字典
    NSString *path2=@"/Users/kenshincui/Desktop/dicXml.plist";
    NSDictionary *dic1=@{@"name":@"Kenshin",@"age":@28,@"height":@172.5};
    [dic1 writeToFile:path2 atomically:YES];
    
    NSDictionary *dic2=[NSDictionary dictionaryWithContentsOfFile:path2];
    [dic2 enumerateKeysAndObjectsUsingBlock:^(id key, id obj, BOOL *stop) {
        NSLog(@"dic2[%@]=%@",key,obj);
    }];
    /*结果：
     dic2[height]=172.5
     dic2[age]=28
     dic2[name]=Kenshin
     */
}

int main(int argc,char *argv[]){
    
    test1();
    
    return 0;
}
```

生成的文件如下:

![4-6-3](.\img\4-6-3.png)

![4-6-4](.\img\4-6-4.png)

#### NSKeyedArchiver归档

如果要针对更多对象归档或者需要归档时能够加密的话就需要使用NSKeyedArchiver进行归档和解档，使用这种方式归档的范围更广而且归档内容是密文存储。从归档范围来讲NSKeyedArchiver适合所有ObjC对象，但是对于自定义对象我们需要实现NSCoding协议；从归档方式来讲NSKeyedArchiver分为简单归档和复杂对象归档，简单归档就是针对单个对象可以直接将对象作为根对象（不用设置key），复杂对象就是针对多个对象，存储时不同对象需要设置不同的Key。

首先看一下系统对象两种归档方式（注意由于本章主要介绍Foundation内容，下面的程序是OS X命令行程序并没有创建成iOS应用，如果移植到到iOS应用下运行将NSArchiver和NSUnarchiver换成NSKeyedArchiver和NSKeyedUnarchiver。虽然在Foundation部分iOS和OS X在设计上尽可能通用但是还存在着细微差别。）

```objc
#import <Foundation/Foundation.h>

//系统对象简单归档
void test1(){
    //NSString归档
    NSString *str1=@"Hello,world!";
    NSString *path1=@"/Users/kenshincui/Desktop/archiver1.arc";
    if(![NSArchiver archiveRootObject:str1 toFile:path1]){
        NSLog(@"archiver failed!");
    }
    //NSString解档
    NSString *str2= [NSUnarchiver unarchiveObjectWithFile:path1];
    NSLog(@"str2=%@",str2);//结果：str2=Hello,world!
    
    
    //NSArray归档
    NSString *path2=@"/Users/kenshincui/Desktop/archiver2.arc";
    NSArray *array1=@[@"Kenshin",@"Kaoru",@"Rosa"];
    if(![NSArchiver archiveRootObject:array1 toFile:path2]){
        NSLog(@"archiver failed!");
    }
    //NSArray解档
    NSArray *array2=[NSUnarchiver unarchiveObjectWithFile:path2];
    [array2 enumerateObjectsUsingBlock:^(id obj, NSUInteger idx, BOOL *stop) {
        NSLog(@"array2[%lu]=%@",idx,obj);
    }];
    /*结果：
     array2[0]=Kenshin
     array2[1]=Kaoru
     array2[2]=Rosa
     */
}

//系统复杂对象归档（多对象归档）
void test2(){
    /*归档*/
    NSString *path1=@"/Users/kenshincui/Desktop/archiver3.arc";
    
    int int1=89;
    CGSize size1={12.5,16.8};
    NSNumber *number1=@60.5;
    NSString *str1=@"Hello,world!";
    NSArray *array1=@[@"Kenshin",@"Kaoru",@"Rosa"];
    NSDictionary *dic1=@{@"name":@"Kenshin",@"age":@28,@"height":@172.5};
    
    //同时对多个对象进行归档
    NSMutableData *data1=[[NSMutableData alloc]init];//定义一个NSMutableData用于临时存放数据
    NSKeyedArchiver *archiver=[[NSKeyedArchiver alloc]initForWritingWithMutableData:data1];//定义归档对象
    [archiver encodeInt:int1 forKey:@"int"];//对int1归档并指定一个key以便以后读取
    [archiver encodeSize:size1 forKey:@"size"];
    [archiver encodeObject:number1 forKey:@"number"];
    [archiver encodeObject:str1 forKey:@"string"];
    [archiver encodeObject:array1 forKey:@"array"];
    [archiver encodeObject:dic1 forKey:@"dic"];

    [archiver finishEncoding];//结束归档
    
    [data1 writeToFile:path1 atomically:YES];//写入文件
    
    
    
    /*解档*/
    int int2;
    CGSize size2;
    NSNumber *number2;
    NSString *str2;
    NSArray *array2;
    NSDictionary *dic2;
    
    NSData *data2=[[NSData alloc]initWithContentsOfFile:path1];//读出数据到NSData
    NSKeyedUnarchiver *unarchiver=[[NSKeyedUnarchiver alloc]initForReadingWithData:data2];
    
    int2= [unarchiver decodeInt64ForKey:@"int"];
    size2=[unarchiver decodeSizeForKey:@"size"];
    number2=[unarchiver decodeObjectForKey:@"number"];
    str2=[unarchiver decodeObjectForKey:@"string"];
    array2=[unarchiver decodeObjectForKey:@"array"];
    dic2=[unarchiver decodeObjectForKey:@"dic"];
    
    [unarchiver finishDecoding];
    
    NSLog(@"int2=%i,size=%@,number2=%@,str2=%@,array2=%@,dic2=%@",int2,NSStringFromSize(size2),number2,str2,array2,dic2);
    /*结果：
     int2=89,
     size={12.5, 16.800000000000001},
     number2=60.5,
     str2=Hello,world!,
     array2=(
         Kenshin,
         Kaoru,
         Rosa
     ),
     dic2={
         age = 28;
         height = "172.5";
         name = Kenshin;
     }
     */
}


int main(int argc,char *argv[]){

    test1();
    test2();
    
    return 0;
}
```

接下来看一下自定义的对象如何归档，上面说了如果要对自定义对象进行归档那么这个对象必须实现NSCoding协议，在这个协议中有两个方法都必须实现：

**-(void)encodeWithCoder:(NSCoder \*)aCoder**;通过给定的Archiver对消息接收者进行编码；

**-(id)initWithCoder:(NSCoder \*)aDecoder**;从一个给定的Unarchiver的数据返回一个初始化对象；

这两个方法分别在归档和解档时调用。下面通过一个例子进行演示（注意对于自定义类的多对象归档与系统类多对象归档完全一样，代码中不再演示）：

Person.h

```objc
#import <Foundation/Foundation.h>

@interface Person : NSObject<NSCoding>

@property (nonatomic,copy) NSString *name;
@property (nonatomic,assign) int age;
@property (nonatomic,assign) float height;
@property (nonatomic,assign) NSDate *birthday;

@end
```

Person.m

```objc
#import "Person.h"

@implementation Person

#pragma mark 解码
-(id)initWithCoder:(NSCoder *)aDecoder{
    NSLog(@"decode...");
    if (self=[super init]) {
        self.name=[aDecoder decodeObjectForKey:@"name"];
        self.age=[aDecoder decodeInt64ForKey:@"age"];
        self.height=[aDecoder decodeFloatForKey:@"heiht"];
        self.birthday=[aDecoder decodeObjectForKey:@"birthday"];
    }
    return self;
}

#pragma mark 编码
-(void)encodeWithCoder:(NSCoder *)aCoder{
    NSLog(@"encode...");
    [aCoder encodeObject:_name forKey:@"name"];
    [aCoder encodeInt64:_age forKey:@"age" ];
    [aCoder encodeFloat:_height forKey:@"height"];
    [aCoder encodeObject:_birthday forKey:@"birthday"];

}

#pragma mark 重写描述
-(NSString *)description{
    NSDateFormatter *formater1=[[NSDateFormatter alloc]init];
    formater1.dateFormat=@"yyyy-MM-dd";
    return [NSString stringWithFormat:@"name=%@,age=%i,height=%.2f,birthday=%@",_name,_age,_height,[formater1 stringFromDate:_birthday]];
}

@end
```

main.m

```objc
#import <Foundation/Foundation.h>
#import "Person.h"


int main(int argc,char *argv[]){

    //归档
    Person *person1=[[Person alloc]init];
    person1.name=@"Kenshin";
    person1.age=28;
    person1.height=1.72;
    NSDateFormatter *formater1=[[NSDateFormatter alloc]init];
    formater1.dateFormat=@"yyyy-MM-dd";
    person1.birthday=[formater1 dateFromString:@"1986-08-08"];
    
    NSString *path1=@"/Users/kenshincui/Desktop/person1.arc";
    
    [NSKeyedArchiver archiveRootObject:person1 toFile:path1];

    //解档
    Person *person2= [NSKeyedUnarchiver unarchiveObjectWithFile:path1];
    NSLog(@"%@",person2);
    /*结果：
     name=Kenshin,age=28,height=0.00,birthday=1986-08-08
     */
    
    return 0;
}
```



### 其他

#### NSNull

通过前面的介绍大家都知道无论在数组还是在字典中都必须以nil结尾，否则数组或字典无法判断是否这个数组或字典已经结束（与C语言中的字符串比较类似，C语言中定义字符串后面必须加一个”\0”）。但是我们有时候确实想在数据或字典中存储nil值而不是作为结束标记怎么办呢？这个时候需要使用NSNull，这个类是一个单例，只有一个null方法。简单看一下：

```objc
#import <Foundation/Foundation.h>



int main(int argc, const char * argv[]) {
    
    NSNull *nl=[NSNull null];//注意这是一个对象，是一个单例，只有一个方法null创建一个对象
    NSNull *nl2=[NSNull null];
    NSLog(@"%i",nl==nl2);//由于是单例所以地址相等,结果：1
    
    NSArray *array1=[NSArray arrayWithObjects:@"abc",nl,@123, nil];
    NSLog(@"%@",array1);
    /*结果：
     (
         abc,
         "<null>",
         123
     )
     */

    return  0;
}
```



#### @符号

我们知道在ObjC中很多关键字前都必须加上@符号，例如@protocol、@property等，当然ObjC中的字符串必须使用@符号，还有就是%@可以表示输出一个对象。其实@符号在新版的ObjC中还有一个作用：装箱。

```objc
#import <Foundation/Foundation.h>

typedef enum {
    spring,
    summer,
    autumn,
    winter
} Season;

int main(int argc, const char * argv[]) {
    /*装箱*/
    NSNumber *number1=@100;
    NSArray *array1=[NSArray arrayWithObjects:number1,@"abc",@16,@'A',@16.7,@YES, nil];
    NSLog(@"%@",array1);
    /*结果：
     (
         100,
         abc,
         16,
         65,
         "16.7"
         1
     )
     */
    NSNumber *number2=@(1+2*3);
    NSLog(@"%@",number2); //结果：7
    NSNumber *number3=@(autumn);
    NSLog(@"%@",number3); //结果：2
    

    NSArray *array2=@[@"abc",@16,@'A',@16.7,@YES];//使用这种方式最后不用添加nil值了
    NSLog(@"%@",array2[2]); //结果：65
    NSMutableArray *array3=[NSMutableArray arrayWithArray:array2];
    array3[0]=@"def";
    NSLog(@"%@",array3[0]); //结果：def
    
    NSDictionary *dic1=@{@"a":@123,@"b":@'c',@"c":@YES};
    NSLog(@"%@",dic1);
    /*结果：
     {
         a = 123;
         b = 99;
         c = 1;
     }
     */
    NSMutableDictionary *dic2=[NSMutableDictionary dictionaryWithDictionary:dic1];
    dic2[@"a"]=@456;
    NSLog(@"%@",dic2[@"a"]);//结果：456

    return 0;
}
```

#### NSString的引用计数器

在好多语言中字符串都是一个特殊的对象，在ObjC中也不例外。NSString作为一个对象类型存储在堆中，多数情况下它跟一般的对象类型没有区别，但是这里我们需求强调一点那就是字符串的引用计数器。

```objc
#import <Foundation/Foundation.h>


int main(int argc,char *argv[]){
    
    NSString *str1=@"Kenshin";
    NSLog(@"retainCount(str1)=%i",(unsigned long)str1.retainCount); //结果：-1
    [str1 retain];
    NSLog(@"retainCount(str1)=%i",(unsigned long)str1.retainCount); //结果：-1
    
    NSString *str2=[NSString stringWithString:@"Kaoru"];
    NSLog(@"retainCount(str2)=%i",str2.retainCount); //结果：-1
    [str1 retain];
    NSLog(@"retainCount(str2)=%i",str2.retainCount); //结果：-1
    NSString *str2_1=[NSString stringWithString:[NSString stringWithFormat:@"Kaoru %@",@"sun"]];
    NSLog(@"retainCount(str2_1)=%i",str2_1.retainCount);//结果：2 
    [str2_1 release];
    [str2_1 release];
    
    
    
    NSString *str3=[NSString stringWithFormat:@"Rosa %@",@"Sun"];
    NSLog(@"retainCount(str3)=%i",str3.retainCount); //结果：1
    [str3 retain];
    NSLog(@"retainCount(str3)=%i",str3.retainCount); //结果：2
    [str3 release];
    [str3 release];
    
    NSString *str4=[NSString stringWithUTF8String:"Jack"];
    NSLog(@"retainCount(str4)=%i",str4.retainCount); //结果：1
    [str4 retain];
    NSLog(@"retainCount(str4)=%i",str4.retainCount); //结果：2
    [str4 release];
    [str4 release];
    
    NSString *str5=[NSString stringWithCString:"Tom" encoding:NSUTF8StringEncoding];
    NSLog(@"retainCount(str5)=%i",str5.retainCount); //结果：1
    [str5 retain];
    NSLog(@"retainCount(str5)=%i",str5.retainCount); //结果：2
    [str5 release];
    [str5 release];
    
    
    
    NSMutableString *str6=@"Jerry";
    NSLog(@"retainCount(str6)=%i",str6.retainCount); //结果：-1
    [str6 retain];
    NSLog(@"retainCount(str6)=%i",str6.retainCount); //结果：-1
    [str6 release];
    [str6 release];
    
    NSMutableArray *str7=[NSMutableString stringWithString:@"Lily"];
    NSLog(@"retainCount(str7)=%i",str7.retainCount); //结果：1
    [str7 retain];
    NSLog(@"retainCount(str7)=%i",str7.retainCount); //结果：2
    [str7 release];
    [str7 release];

    
    return 0;
}
```

看完上面的例子如果不了解NSString的处理你也许会有点奇怪（注意上面的代码请在Xcode5下运行）？请看下面的解释

* str1是一个字符串常量，它存储在常量区，系统不会对它进行引用计数，因此无论是初始化还是做retain操作其引用计数器均为-1；
* str3、str4、str5创建的对象同一般对象类似，存储在堆中，系统会对其进行引用计数；
* 采用stringWithString定义的变量有些特殊，当后面的字符串是字符串常量，则它本身就作为字符串常用量存储（str2），类似于str1；如果后面的参数是通过类似于str3、str4、str5的定义，那么它本身就是一个普通对象，只是后面的对象引用计数器默认为1，当给它赋值时会做一次拷贝操作（浅拷贝），引用计数器加1，所有str2_1引用计数器为2；
* str6其实和str1类似，虽然定义的是可变数组，但是它的本质还是字符串常量，事实上对于可变字符串只有为字符串常量时引用计数器才为-1，其他情况它的引用计数器跟一般对象完全一致；

**后记：注意上面这段代码的运行结果是在Xcode5中运行的结果，事实上针对最新的Xcode6由于LLVM的优化，只有str2_1和str7的引用计数器为1（str7 retain一次后第二次为2），其他均为-1。**