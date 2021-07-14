# 检测项目中未使用到的方法

## 一、使用方式

```
python FindSelectorsUnrefs.py -a /Users/.../Build/Products/Debug-iphonesimulator/XXX.app -p /Users/.../ProjectPath -w WB,JD
```

> 参数说明
>
>  -a Xcode运行之后的，项目 Product 的路径
>
>  -p 项目的路径
>
>  -w 结果白名单处理（可不加）。检测结果只想要以什么开头的类的方法，多个用逗号隔开，比如JD,BD,AL
>
>  -b 结果黑名单处理（可不加）。检测结果不想要以什么开头的类的方法，多个用逗号隔开，比如Pod,AF,SD
>
>  -w 和 -b 不能共存，共存会报错

## 二、运行结果示例

```
获取所有的protocol中的方法...
获取所有被调用的方法...
获取所有的方法，除了setter and getter方法...


查找到未被使用的方法: 5074个

1 : -[PNColor imageFromColor:]
2 : +[CMAllMediaVoiceDictationConfig isDot]
3 : +[UIImage(PMExtension) imageNamedForOriginal:bundel:]
4 : -[NSObject(LKModel) setValue:forUndefinedKey:]
5 : -[NSString(MGPAdd) enumerateUTF32CharInRange:usingBlock:]
6 : +[IFlyResourceUtil ENGINE_DESTROY]
7 : +[ShareSDK(SSUI) showShareActionSheet:customItems:sheetConfiguration:onStateChanged:]
8 : +[SAPassThroughValueTransformer allowsReverseTransformation]
9 : -[NSData(MIGUSSZipArchive) _migubase64RFC4648]
10 : -[MCOIMAPBaseOperation isUrgent]
11 : +[XWSignStore loadURL]
12 : -[NSData(WYEncryption) WYAES128DecryptWithMD5Key:]
...
5069 : -[UIView(IQToolbarAddition) addPreviousNextRightOnKeyboardWithTarget:rightButtonTitle:previousAction:nextAction:rightButtonAction:]
5070 : +[IFlyVerifierUtil ARGBToGray:]
5071 : -[ASLoginEngine getVerifyCode:]
5072 : -[UICKeyChainStore(Deprecation) synchronizeWithError:]
5073 : -[SSDKRegister setupPocketWithConsumerKey:redirectUrl:]
5074 : +[UIImage(GIF) sd_imageWithGIFData:]

项目中未使用方法检测完毕，相关结果存储到当前目录 selector_unrefs.txt 中
请在项目中进行二次确认后处理
```

## 三、原理

Mach-O 文件中的`__TEXT:__objc_methname`包含了代码中的所有方法名，而`__DATA__objc_selrefs`中则包含了所有被使用的方法的引用，通过取两个集合的差集就可以得到所有未被使用的代码。

## 四、检测不到的情况

1. 方法1调用方法2，但是没有类调用方法1。只能检测出方法1，将方法1删除之后才会检测出方法2，所以需要检测多次
2. Swift 测不到
3. C 语言方法检测不到，会被认为是已使用方法
4. 通过字符串转化成 Selector 检测不到，会被认为是为被调用的方法