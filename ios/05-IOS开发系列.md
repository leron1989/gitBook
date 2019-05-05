[TOC]
## OC开发

## 常用操作

### 跳转









## 原生UI

### UIButton

1. 初始化
```c
// 方法一：通用UIView初始化方法
UIButton *cameraBtn =UIButton.new;
// 方法二：据不同的枚举，创建不同样式的按钮
UIButton *btn = [UIButton buttonWithType:UIButtonTypeSystem];

typedef NS_ENUM(NSInteger, UIButtonType) {
    UIButtonTypeCustom = 0,                         // 自定义按钮
    UIButtonTypeSystem NS_ENUM_AVAILABLE_IOS(7_0),  // 系统按钮--当前样式被选中情况下，背景颜色会被改变
    UIButtonTypeDetailDisclosure,                   // 蓝色箭头按钮（用作详
    UIButtonTypeInfoLight,                          // 亮色感叹号按钮
    UIButtonTypeInfoDark,                           // 深色感叹号按钮
    UIButtonTypeContactAdd,                         // 加号按钮
    UIButtonTypeRoundedRect = UIButtonTypeSystem,   // Deprecated, use UIButtonTypeSystem instead
};
```
2. 设置标题
```c
[btn setTitle:@"点我呀" forState:UIControlStateNormal];

typedef NS_OPTIONS(NSUInteger, UIControlState) {
    UIControlStateNormal       = 0,       // 正常状态
    UIControlStateHighlighted  = 1 << 0,  // 高亮状态
    UIControlStateDisabled     = 1 << 1,  // 失效状态
    UIControlStateSelected     = 1 << 2,  // 选中状态,是否选中需要手动设置
    UIControlStateFocused NS_ENUM_AVAILABLE_IOS(9_0) = 1 << 3,  // 聚焦状态 （iOS新加入 应该和3D Touch有关）
    UIControlStateApplication  = 0x00FF0000,    // 当用做应用标志时
    UIControlStateReserved     = 0xFF000000     // 框架预留 无意义
};
```
3. 添加事件
```c
/*
 * 添加事件：
 * target: 执行者
 * action：被执行的方法
 * events：事件
 * 当按钮的的事件被触发时，调用执行者的被执行方法--[self clickBtn:btn]
 */
[btn addTarget:self action:@selector(clickBtn:) forControlEvents:UIControlEventTouchUpInside];

/*
 * 移除事件
 * 删除的参数必须要添加时候的参数一样，否则删除不能成功
 */
[btn removeTarget:self action:@selector(clickBtn:) forControlEvents:UIControlEventTouchUpInside];

```
* 事件状态：
  * UIControlEventTouchDown
    单点触摸按下事件：用户点触屏幕，或者又有新手指落下的时候。
  * UIControlEventTouchDownRepeat
    多点触摸按下事件，点触计数大于1：用户按下第二、三、或第四根手指的时候。
  * UIControlEventTouchDragInside
    当一次触摸在控件窗口内拖动时。
  * UIControlEventTouchDragOutside
    当一次触摸在控件窗口之外拖动时。
  * UIControlEventTouchDragEnter
    当一次触摸从控件窗口之外拖动到内部时。
  * UIControlEventTouchDragExit
    当一次触摸从控件窗口内部拖动到外部时。
  * **<u>UIControlEventTouchUpInside</u>**
    所有在控件之内触摸抬起事，一般用于按钮。
  * UIControlEventTouchUpOutside
    所有在控件之外触摸抬起事件(点触必须开始与控件内部才会发送通知)。
  * UIControlEventTouchCancel
    所有触摸取消事件，即一次触摸因为放上了太多手指而被取消，或者被上锁或者电话呼叫打断。
  * UIControlEventTouchChanged
    当控件的值发生改变时，发送通知。用于滑块、分段控件、以及其他取值的控件。你可以配置滑块控件何时发送通知，在滑块被放下时发送，或者在被拖动时发送。
  * UIControlEventEditingDidBegin
    当文本控件中开始编辑时发送通知。
  * UIControlEventEditingChanged
    当文本控件中的文本被改变时发送通知。
  * UIControlEventEditingDidEnd
    当文本控件中编辑结束时发送通知。
  * UIControlEventEditingDidOnExit
    当文本控件内通过按下回车键（或等价行为）结束编辑时，发送通知。
  * UIControlEventAlltouchEvents
    通知所有触摸事件。
  * UIControlEventAllEditingEvents
    通知所有关于文本编辑的事件。
  * UIControlEventAllEvents
    通知所有事件。
4. 其他方法
```c
// 设置选中状态
btn.selected = NO;
// 设置背景色
btn.backgroundColor = [UIColor redColor];
// btn整体内容四周的间距
btn.contentEdgeInsets = UIEdgeInsetsMake(15, 15, 15, 15); 
// 标题四周间距
btn.titleEdgeInsets = UIEdgeInsetsMake(15, 15, 15, 15); 
// 图片四周间距
btn.imageEdgeInsets = UIEdgeInsetsMake(15, 15, 15, 15);

// 标题文本颜色
[btn setTitleColor:[UIColor redColor] forState:UIControlStateNormal]; 

// 标题阴影颜色
[btn setTitleShadowColor:[UIColor redColor] forState:UIControlStateNormal]; 

// 获取某种状态下的标题
 NSString *titleStr = [btn titleForState:UIControlStateSelected];
// 设置按钮前景图片---不会被拉伸，原比例显示, 会遮住按钮标题
[btn setImage:[UIImage imageNamed:@"btnIcon"] forState:UIControlStateNormal]; 

// 设置按钮背景图片---会被拉伸，充满整个btn, 按钮标题会显示在图片之上
[btn setBackgroundImage:[UIImage imageNamed:@"btnBgImg"] forState:UIControlStateNormal];
```
### UIActionSheet
UIActionSheet是用来选择的控件，比如微信朋友圈中的图片选择功能就是这个控件。
1. 实现UIActionSheetDelegate
2. 定义ActionSheet
```c
UIActionSheet *actionSheet = [[UIActionSheet alloc] initWithTitle:@"这是actionSheet标题" delegate:self cancelButtonTitle:@"取了个消" destructiveButtonTitle:@"确了个定" otherButtonTitles:@"按钮1", @"按钮2", nil];

_actionSheet.tag = 1001; //用于识别同一个view中不同的actionSheet
```
3. 显示ActionSheet
```c
[actionSheet showInView:self.view];
```
4. 按钮触发事件
```c
- (void)actionSheet:(UIActionSheet *)actionSheet clickedButtonAtIndex:(NSInteger)buttonIndex{
    switch (buttonIndex) {
        case 1:
            DLog(@"%ld-%@", (long)buttonIndex, @"按钮1")
            break;
        case 2:
            DLog(@"%ld-%@", buttonIndex, @"按钮2")
            break;
        default:
            DLog(@"%ld-%@", buttonIndex, @"其他")
            break;
    }
}
```
5. 其他代理方法
```c
// 如果没有定义的委托,我们模拟一个点击取消按钮（没用过）
- (void)actionSheetCancel:(UIActionSheet *)actionSheet;

 //将要显示警告框时触发的方
- (void)willPresentActionSheet:(UIActionSheet *)actionSheet; 

 //已经显示提示框时触发的方法
- (void)didPresentActionSheet:(UIActionSheet *)actionSheet; 

//提示框将要消失时触发的方法
- (void)actionSheet:(UIActionSheet *)actionSheet willDismissWithButtonIndex:(NSInteger)buttonIndex;

//提示框已经消失时触发的方法
- (void)actionSheet:(UIActionSheet *)actionSheet didDismissWithButtonIndex:(NSInteger)buttonIndex; 
```


### UITableView
#### 实现TableView基本步骤
1. 初始化TableVIew
  * UITableView有两种风格：UITableViewStylePlain 和 UITableViewStyleGrouped（分组列表）
2. 实现数据源
3. 实现代理方法

#### 数据源（UITableViewDataSource）
常用方法：
1. **numberOfSectionsInTableView**
```c
#pragma mark 计算分组数
-(NSInteger)numberOfSectionsInTableView:(UITableView *)tableView{
    NSLog(@"计算分组数");
    return _list.count;
}
```
2. **numberOfRowsInSection**

```c
#pragma mark 返回每组行数
-(NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section{
    NSLog(@"计算每组(组%i)行数",section);
    KCContactGroup *group1=_contacts[section];
    return group1.contacts.count;
}
```
3. **cellForRowAtIndexPath**

```c
   #pragma mark返回每行的单元格
   -(UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath{
       //NSIndexPath是一个结构体，记录了组和行信息
       NSLog(@"生成单元格(组：%i,行%i)",indexPath.section,indexPath.row);
       KCContactGroup *group=_contacts[indexPath.section];//根据组标获取组数据
       KCContact *contact=group.contacts[indexPath.row];//根据行数从组数据中获取对应的行
       UITableViewCell *cell=[[UITableViewCell alloc]initWithStyle:UITableViewCellStyleDefault reuseIdentifier:nil];//初始化cell及style
       cell.textLabel.text=[contact getName];
       cell.detailTextLabel.text=contact.phoneNumber;
       return cell;
   }
```
**iOS已经在其内部设置好了多个子控件以供开发者使用：**

* UITableViewCellStyleDefault           

  左侧显示textLabel（不显示detailTextLabel），imageView可选（显示在最左边）

* UITableViewCellStyleValue1,            

  左侧显示textLabel、右侧显示detailTextLabel（默认蓝色），imageView可选（显示在最左边）

* UITableViewCellStyleValue2,            

  左侧依次显示textLabel(默认蓝色)和detailTextLabel，imageView可选（显示在最左边）

* UITableViewCellStyleSubtitle            

  左上方显示textLabel，左下方显示detailTextLabel（默认灰色）,imageView可选（显示在最左边）


4. **titleForHeaderInSection**
```c
#pragma mark 返回每组头标题名称
-(NSString *)tableView:(UITableView *)tableView titleForHeaderInSection:(NSInteger)section{
    NSLog(@"生成组（组%i）名称",section);
    KCContactGroup *group=_contacts[section];
    return group.name;
}
```
5. **titleForFooterInSection**
```c
#pragma mark 返回每组尾部说明
-(NSString *)tableView:(UITableView *)tableView titleForFooterInSection:(NSInteger)section{
    NSLog(@"生成尾部（组%i）详情",section);
    KCContactGroup *group=_contacts[section];
    return group.detail;
}
```
6. **sectionIndexTitlesForTableView**
```c
#pragma mark 返回每组标题索引
-(NSArray *)sectionIndexTitlesForTableView:(UITableView *)tableView{
    NSLog(@"生成组索引");
    NSMutableArray *indexs=[[NSMutableArray alloc]init];
    for(KCContactGroup *group in _contacts){
        [indexs addObject:group.name];
    }
    return indexs;
}
```
```flow
A=>operation: 计算分组数
B=>operation: 计算每组行数
C=>operation: 生成分组索引
D=>operation: 依次生成所有组的所有单元格
A->B->C->D
```
> 生成单元格的方法并不是一次全部调用，而是只会生产当前显示在界面上的单元格，当用户滚动操作时再显示其他单元格

#### 代理（UITableViewDelegate）

*******************************************************************
### UICollectionView
#### 实现CollectionView基本步骤
1. viewController需要实现UICollectionViewDataSource、UICollectionViewDataSource、UICollectionViewDelegateFlowLayout
2. 定义UICollectionViewFlowLayout
3. 定义UICollectionViewCell
4. 初始化UICollectionView
```c
mainCollectionView = [[UICollectionView alloc] initWithFrame:self.view.bounds collectionViewLayout:layout];
```
5. 注册UICollectionViewCell
```c
//注意，此处的ReuseIdentifier 必须和 cellForItemAtIndexPath 方法中 一致 均为 cellId
[mainCollectionView registerClass:[LRBaseCollectionViewCell class] forCellWithReuseIdentifier:@"cellId"];
```
6. 注册headerView（如果是分组CollectionView需要注册）
```c
//注册headerView  此处的ReuseIdentifier 必须和 cellForItemAtIndexPath 方法中 一致  均为reusableView
[mainCollectionView registerClass:[UICollectionReusableView class] forSupplementaryViewOfKind:UICollectionElementKindSectionHeader withReuseIdentifier:@"reusableView"];
```
7. 设置UICollectionViewDataSource
8. 设置UICollectionViewDelegate

#### CollectionViewFlowLayout
1. 默认UICollectionViewFlowLayout（每个item固定大小时可用此方法）
基本设置如下，初始化layout、设置headerView大小（如果需要）、设置item大小：
```c
//初始化flowLayout
UICollectionViewFlowLayout *layout = [[UICollectionViewFlowLayout alloc] init];

//设置headerView的大小
layout.headerReferenceSize = CGSizeMake(self.view.frame.size
.width, 100);

//设置itemSize
layout.itemSize = CGSizeMake(110, 150);
```
2. 自定义UICollectionViewFlowLayout
* 继承UICollectionViewLayout
* 需要定义储存模型数据（UICollectionViewLayoutAttributes）的变量数组
```c
/** 所有的模型数据 */
@property (nonatomic, strong) NSMutableArray<UICollectionViewLayoutAttributes *> *array;
```
* 实现**prepareLayout**方法
	* 必须[super prepareLayout];
	* 改方法主要用于清空及初始化模型数据等变量
	```c
	- (void)prepareLayout
    {
        [super prepareLayout];

        [self.array removeAllObjects];

        NSUInteger count = [self.collectionView numberOfItemsInSection:0];

        for (NSInteger i = 0; i < count; i++) {
            UICollectionViewLayoutAttributes *atrb = [self layoutAttributesForItemAtIndexPath:[NSIndexPath indexPathForItem:i inSection:0]];
            [self.array addObject:atrb];
        }
    }
	```
* 实现**layoutAttributesForItemAtIndexPath**方法
	* 该方法主要用于设置每个item的布局
	* collectionViewItem数量决定了此方法调用的次数
```c
-(UICollectionViewLayoutAttributes *)layoutAttributesForItemAtIndexPath:(NSIndexPath *)indexPath
{
    // 1. 创建布局属性
    UICollectionViewLayoutAttributes *attrs = [UICollectionViewLayoutAttributes layoutAttributesForCellWithIndexPath:indexPath];
    
    // 2. 计算每个item的左上角坐标，及宽度高度（x, y, width, height）
    // 省略详细代码，需要具体问题具体分析
    
    // 3. 设置item的frame
    attrs.frame = CGRectMake(x, y, width, height);
    
    return attrs;
}
```
* 实现**layoutAttributesForElementsInRect**方法
该方法主要用于布局所有item的属性,包括header、footer，直接返回perpareLayout中定义的模型数据变量数组即可
```c
-(NSArray *)layoutAttributesForElementsInRect:(CGRect)rect
{
    return [self.attrsArray copy];
}
```
* 实现**collectionViewContentSize**方法
该方法用于计算CollectionView的滚动区域，具体情况需要具体分析，此处提供一种方式，改方式保存每列的高度
```c
// 最长那列的长度+边距
-(CGSize)collectionViewContentSize
{
    //假设最长的那一列为第0列
    __block NSString *maxColumn = @"0";
    
    //遍历字典,找出最长的那一列
    [self.maxYDic enumerateKeysAndObjectsUsingBlock:^(NSString *column, NSNumber *maxY, BOOL *stop) {
        
        if ([maxY floatValue] > [self.maxYDic[maxColumn] floatValue])
        {
            maxColumn = column;
        }
    }];
    return CGSizeMake(0, [self.maxYDic[maxColumn]floatValue]+self.sectionInset.bottom);
}
```
3. 加载顺序（待验证）
```flow
A=>operation: prepareLayout（此方法调用layoutAttributesForItemAtIndexPath）
B=>operation: collectionViewContentSize
C=>operation: layoutAttributesForElementsInRect
A->B->C
```


#### UICollectionViewCell
1. 默认**UICollectionViewCell**
此情况无需在CollectionView初始化时注册
```c
UICollectionViewCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:@"UICollectionViewCell" forIndexPath:indexPath];
```
2. 自定义**UICollectionViewCell**
* 此情况需要继承**UICollectionViewCell**，并且在CollectionView初始化时进行注册
* 需要重写**initWithFrame**方法
```c
-(instancetype)initWithFrame:(CGRect)frame{
    if ([super initWithFrame:frame]) {
        // 构建cellView，具体情况具体分析
    }
    return self;
}
```

#### 设置UICollectionViewDataSource
1. 需要实现主要三个方法：
* numberOfSectionsInCollectionView（分组数，该方法为非必须实现、不实现情况下返回1）
```c
-(NSInteger)numberOfSectionsInCollectionView:(UICollectionView *)collectionView{
    return 1;
}
```

* numberOfItemsInSection（每组item数量，必须实现）
```c
-(NSInteger)collectionView:(UICollectionView *)collectionView numberOfItemsInSection:(NSInteger)section{
    return _logic.dataArray.count;
}
```

* **cellForItemAtIndexPath**（设置cell，必须实现）
```c
-(UICollectionViewCell *)collectionView:(UICollectionView *)collectionView cellForItemAtIndexPath:(NSIndexPath *)indexPath{
    PersonDemoCollectionViewCell *cell = [collectionView dequeueReusableCellWithReuseIdentifier:NSStringFromClass([PersonDemoCollectionViewCell class]) forIndexPath:indexPath];
    // 设置cell模型数据
    cell.personModel = _logic.dataArray[indexPath.row];
    return cell;
}
```
2. 不常用方法
```c
// 为collection view添加一个补充视图(页眉或页脚)
- (UICollectionReusableView *)collectionView:(UICollectionView *)collectionView viewForSupplementaryElementOfKind:(NSString *)kind atIndexPath:(NSIndexPath *)indexPath

// 设定页眉的尺寸
- (CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout referenceSizeForHeaderInSection:(NSInteger)section

// 设定页脚的尺寸
- (CGSize)collectionView:(UICollectionView *)collectionView layout:(UICollectionViewLayout*)collectionViewLayout referenceSizeForFooterInSection:(NSInteger)section

// 添加页眉和页脚以前需要注册类和标识
- (void)registerClass:(Class)viewClass forSupplementaryViewOfKind:(NSString *)elementKind withReuseIdentifier:(NSString *)identifier
```
具体使用请参考：[UICollectionView基础](https://www.cnblogs.com/wayne23/p/4013522.html)

#### 设置UICollectionViewDelegate
1. 常用方法
* didSelectItemAtIndexPath（item被点击时触发方法）
```c
// 当指定indexPath处的item被选择时触发
- (void)collectionView:(UICollectionView *)collectionView didSelectItemAtIndexPath:(NSIndexPath *)indexPath{
	[self.myArray removeObjectAtIndex:indexPath.row];

	[collectionView deleteItemsAtIndexPaths:[NSArray arrayWithObject:indexPath]];
}
```
> 当删除或添加元素时，一定要更新numberOfItemsInSection的返回情况

2. 非常用方法
```c
// 当指定indexPath处的item被取消选择时触发，仅在允许多选时被调用
- (void)collectionView:(UICollectionView *)collectionView didDeselectItemAtIndexPath:(NSIndexPath *)indexPath

// 下面是三个和高亮有关的方法：
- (BOOL)collectionView:(UICollectionView *)collectionView shouldHighlightItemAtIndexPath:(NSIndexPath *)indexPath
- (void)collectionView:(UICollectionView *)collectionView didHighlightItemAtIndexPath:(NSIndexPath *)indexPath
- (void)collectionView:(UICollectionView *)collectionView didUnhighlightItemAtIndexPath:(NSIndexPath *)indexPath
```
具体使用请参考：[UICollectionView基础](https://www.cnblogs.com/wayne23/p/4013522.html)

#### UICollectionViewDelegateFlowLayout
// TODO 还未涉及，后面涉及到再补充
参考文档：[UICollectionView基础](https://www.cnblogs.com/wayne23/p/4013522.html)


