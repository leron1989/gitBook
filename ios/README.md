## APP发布
通过三个坐标绑定一个新APP：证书 + appid + 描述文件
### 新APP绑定过程
1. 团队证书唯一，无需调整
2. 申请APPID
  2.1、登入developer.apple.com，进入Account，点击Identifiers - App IDs - 新增
  2.2、填写相关信息：Name、Bundle ID，然后continue，Register，Done
3. 生成描述文件
  3.1、点击：Provisioning Profiles - All，然后点击新增
  3.2、选择Distribution - In House，continue
  3.3、选择对应的Appid，continue
  3.4、选择对应的证书
  3.5、填写文件名称，生成 - download
  完了以后会下载一个.mobilevision文件到本地，双击导入到xcode中

> 新APP选择证书后，会根据Bundle Identifier寻找对应的.mobileprovision文件，并关联到APP中。到此，新APP已经绑定到对应团队的开发者账户，可以进行发布

### 注意事项
1. 证书三年过期
2. 描述文件一年过期
3. 蒲公英托管三个月过期，重新发布应用后重新计算日期
4. 修改APPID需要重新申请第三方的key，如高德地图等。
5. 



## 格式化字符串
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


## 数字
* UInt32：无符号32位整数；
* SInt16：有符号16位整数


## 变量
1. NSInteger（有符号）、NSUInteger（无符号）可以在32位和64位系统中通用的数字类型。printf这两种类型时，需要转换为long型，如：
```c
    NSInteger x = -5;
    NSUInteger y = 6;
    printf("Here they are: %ld, %lu", (long)x, (unsigned long)y)
```

## 方法
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


## 指针
> 可以通过变量存储指针地址，通过指针地址变量可以快速传递大数据所在内存地址

1. 在变量前面添加&运算符，来获得变量的内存地址
2. \*有三种用途：
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

## 结构（structure）
1. 使用结构（struct），来定义多个数据的组合体，类似Java中的pojo

2. 定义结构的两种方式
    * sturct关键字，定义的时候都要写struct关键字

    ```c
    struct Person {
        float height;
        int weight;
    }
    
    int main(int argc, const char * argv[]){
        struct Person leron; //声明Person变量时需要struct关键字
        leron.height = 1.80;
        leron.weight = 75;
    }
    ```
    * typedef关键字
    
    ```c
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
## 栈 & 堆
1. 栈：调用函数时系统自动分配，并在函数结束时自动释放。
2. 堆：特定内存区域，与栈是分开的。通常连续内存区域叫做：缓冲区。缓冲区来自堆，独立于任何函数的栈，因此可以在多个函数中使用。
    * 在C中可以通过malloc()函数得到一块内存缓冲区。当程序不再使用缓冲区时，可以使用free()函数释放内存，还给堆
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
## 添加代码库


## 获取应用.sqlite文件
1. 打开：Window -> Devices and Simulators
2. 在Devices面板点击App，Download Container


## H5嵌入

### UIWebView

### WebViewJavascriptBridge


### UITableView
#### 基本介绍
UITableView有两种风格：UITableViewStylePlain和UITableViewStyleGrouped。这两者操作起来其实并没有本质区别，只是后者按分组样式显示前者按照普通样式显示而已


iOS已经在其内部设置好了多个子控件以供开发者使用。如果我们查看UITableViewCell的声明文件可以发现在内部有一个UIView控件（contentView，作为其他元素的父控件）、两个UILable控件（textLabel、detailTextLabel）、一个UIImage控件（imageView），分别用于容器、显示内容、详情和图片。


```oc
typedef NS_ENUM(NSInteger, UITableViewCellStyle) {
    UITableViewCellStyleDefault,    // 左侧显示textLabel（不显示detailTextLabel），imageView可选（显示在最左边）
    UITableViewCellStyleValue1,        // 左侧显示textLabel、右侧显示detailTextLabel（默认蓝色），imageView可选（显示在最左边）
    UITableViewCellStyleValue2,        // 左侧依次显示textLabel(默认蓝色)和detailTextLabel，imageView可选（显示在最左边）
    UITableViewCellStyleSubtitle    // 左上方显示textLabel，左下方显示detailTextLabel（默认灰色）,imageView可选（显示在最左边）
};
```


大家在使用iPhone通讯录时会发现右侧可以按字母检索，使用起来很方便，其实这个功能使用UITableView实现很简单，只要实现数据源协议的一个方法，构建一个分组标题的数组即可实现。数组元素的内容和组标题内容未必完全一致，UITableView是按照数组元素的索引和每组数据索引顺序来定位的而不是按内容查找。


```
graph TD
A(计算分组数)-->B(计算每组的行数)
B-->C(生成分组索引)
C-->D(依次生成所有组的所有单元格)
```
值得指出的是生成单元格的方法并不是一次全部调用，而是只会生产当前显示在界面上的单元格，当用户滚动操作时再显示其他单元格。

#### MBProgressHUD
效果一 只显示文字:
```
// 只显示文字
- (void)TextButtonAction{
    
    self.HUD = [[MBProgressHUD alloc] initWithView:self.view];
    [self.view addSubview:_HUD];
    _HUD.labelText = @"加载中,请稍等...";
    _HUD.mode = MBProgressHUDModeText;
    
    [_HUD showAnimated:YES whileExecutingBlock:^{
        sleep(2);
        
    }
       completionBlock:^{
           [_HUD removeFromSuperview];
           _HUD = nil;
       }];

}

```

效果二 圆形进度条


```
// 圆形进度条
- (void)CircularButtonAction{
    
    //圆形进度条
    self.HUD = [[MBProgressHUD alloc] initWithView:self.view];
    
    [self.view addSubview:_HUD];
    
    _HUD.mode = MBProgressHUDModeDeterminate;
    
    _HUD.delegate = self;
    
    _HUD.labelText = @"等待中";
    
    [_HUD showWhileExecuting:@selector(ProgressBar) onTarget:self withObject:nil animated:YES];
}
```

效果三 文字 加 菊花

```
//   通常情况  文字  加 菊花
- (void)GeneralButtonAction{
    //初始化进度框，置于当前的View当中
    self.HUD = [[MBProgressHUD alloc] initWithView:self.view];
    [self.view addSubview:_HUD];
    
    //如果设置此属性则当前的view置于后台
    _HUD.dimBackground = YES;
    
    //设置对话框文字
    _HUD.labelText = @"加载中";
    //细节文字
    _HUD.detailsLabelText = @"请耐心等待";
    
    //显示对话框
    [_HUD showAnimated:YES whileExecutingBlock:^{
        //对话框显示时需要执行的操作
        sleep(3);
    }// 在HUD被隐藏后的回调
       completionBlock:^{
           //操作执行完后取消对话框
           [_HUD removeFromSuperview];
           _HUD = nil;
    }];
}

```

效果四 自定义动图
```
//   自定义 动图
- (void)CustomButtonAction{
    
    //自定义view
    self.HUD = [[MBProgressHUD alloc] initWithView:self.view];
    //取消背景框
    self.HUD.color = [UIColor whiteColor];
    
    [self.view addSubview:_HUD];
    
    UIImageView *images = [[UIImageView alloc]initWithFrame:CGRectMake(0, 0, 200, 300)];
    
    NSMutableArray *imageArray = [[NSMutableArray alloc]init];
    
    for(int i = 1; i < 8 ; i++){
        NSString *imgName = [NSString stringWithFormat:@"%d.tiff",i];
        
        [imageArray addObject:[UIImage imageNamed:imgName]];
    }
    
    images.animationDuration = 0.7;
    
    images.animationImages = imageArray;
    // 开始播放
    [images startAnimating];
    
    
    //自定义
    _HUD.mode = MBProgressHUDModeCustomView;
    _HUD.delegate = self;
    
    _HUD.customView = images;
    
    [_HUD show:YES];
    //延迟
    [_HUD hide:YES afterDelay:2];
    
}
```