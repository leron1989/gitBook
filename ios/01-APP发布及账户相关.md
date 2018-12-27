[TOC]
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