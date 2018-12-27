[TOC]
## OC开发
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