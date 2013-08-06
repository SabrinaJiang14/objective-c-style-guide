# 紐約時報行動軟體團隊的Objective-C程式碼撰寫風格手冊

這份手冊摘要描述紐約時報iOS開發團隊的程式碼書寫慣例。歡迎各位回饋意見，[提出問題](https://github.com/NYTimes/objetive-c-style-guide/issues)、[提出pull請求](https://github.com/NYTimes/objetive-c-style-guide/pulls)、以及[發推文](https://twitter.com/nytimesmobile)，另外，我們正[招募中](http://jobs.nytco.com/job/New-York-iOS-Developer-Job-NY/2572221/)。

感謝所有的[貢獻者](https://github.com/NYTimes/objective-c-style-guide/contributors)。

## 導論

底下列出一些Apple公司關於程式碼風格與慣例的文件，若你發現某事項本手冊並未提及，通常都能在這些文件裡找到詳細的描述。

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## 目錄

* [句點標記法的語法（Dot-Notation Syntax）](#句點標記法的語法（Dot-Notation Syntax）)
* [空格與縮排（Spacing）](#spacing)
* [條件判斷（Conditionals）](#conditionals)
* [方法（Methods）](#methods)
* [變數（Variables）](#variables)
* [命名（Naming）](#naming)
  * [底線（Underscores）](#underscores)
* [註解（Comments）](#comments)
* [初始化與解構式（init and dealloc）](#init-and-dealloc)
* [字面值（Literals）](#literals)
* [CGRect函式（CGRect Functions）](#cgrect-functions)
* [常數（Constants）](#constants)
* [列舉型別（Enumerated Types）](#enumerated-types)
* [私有屬性（Private Properties）](#private-properties)
* [圖檔命名（Image Naming）](#image-naming)
* [布林（Booleans）](#booleans)
* [單件設計模式（Singletons）](#singletons)
* [Xcode專案（Xcode project）](#xcode-project)

## 句點標記法的語法（Dot-Notation Syntax）

使用句點標記法的地方一定是想要存取與改變屬性的時候，任何其他地方應採用方括號標記法。

**譬如應該如下這樣寫：**  
```objc
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**而不是這樣寫：**
```objc
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

## 空格與縮排（Spacing）

* 縮排時使用4個空白，千萬不要使用tab鍵，記得在Xcode的偏好設定裡啟用此選項。
* 方法的大括號與其他程式構件的大括號（`if`/`else`/`switch`/`while`等等），左大括號必須跟程式構件位於同一行，但右大括號則位於新的一行。

**例如：**  
```objc
if (user.isHappy) {
//作事情
}
else {
//作事情
}
```
* 方法之間必須恰好相隔一個空白行，有助於程式碼整體組織性與視覺辨識。方法內也可用空白行區隔功能，但碰到這種情況時，通常就是該分出新方法的時候。
* 在實作裡的`@synthesize`與`@dynamic`，每個必須單獨宣告佔據一行。

## 條件判斷（Conditionals）

條件判斷的主體一定要包在大括號裡，就算是能省略大括號（也就是說，主體只有一行），可避免各種
[錯誤](https://github.com/NYTimes/objective-c-style-guide/issues/26#issuecomment-22074256)，常見錯誤包括，之後加入第二行程式碼卻以為它會被if述句所涵蓋， 另一個會出錯且[更加危險的錯誤](http://programmers.stackexchange.com/a/16530)是，當if述句「裡」唯一一行程式碼被註解掉後，下一行卻變成if述句的主體了。另外，這項書寫慣例能與其他條件判斷式保持一致性，檢查程式碼時更容易被找出錯誤。

**譬如：**
```objc
if (!error) {
    return success;
}
```

**而不是：**
```objc
if (!error)
    return success;
```

或  

```objc
if (!error) return success;
```

## 方法（Methods）

方法宣告時，在-/+符號之後必須是一個空白。方法每個參數區段之間必須相隔一個空白。  

**例如：**:  
```objc  
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```
## 變數（Variables）

盡量以淺顯易懂的方式命名變數，避免使用只有一個字元的變數名，除了在`for()`裡。

星號指出指標屬於變數，例如`NSString *text`，而不是`NSString* text`或`NSString * text`，除非碰到常數的情況。

只要可以，都應該以屬性定義取代單純的實體變數。應避免直接存取實體變數，除了在初始化方法（`init`、`initWithCoder:`、等等）、dealloc方法、以及自訂的取值子與設值子方法。關於在初始化方法與dealloc方法裡使用存取子方法，更多細節請見[這裡](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6)。

**例如：**  

```objc
@interface NYTSection: NSObject

@property (nonatomic) NSString *headline;

@end
```

**而不是：**

```objc
@interface NYTSection : NSObject {
    NSString *headline;
}
```

## 命名（Naming）

不論如何，都應該盡量遵守Apple的命名規則，特別是關於[記憶體管理](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508))部份的規則。

夠長且一看就懂的方法名與變數名，是好事。 

**例如：**  

```objc
UIButton *settingsButton;
```

**而不是：**  

```objc
UIButton *setBut;
```

類別名與常數應冠上由三字母組成的字頭（譬如NYT），但Core Data的entity名可省略。常數應採用駝峰式大小寫命名法，每個單字的第一個字母應大寫，為了清楚起見冠上相關的類別名。


**例如：**  

```objc
static const NSTimeInterval NYTArticleViewControllerNavigationFadeAnimationDuration = 0.3;
```

**而不是：**

```objc
static const NSTimeInterval fadetime = 1.7;
```

屬性名也採用駝峰式大小寫命名法，但開頭單字應小寫，注意：若Xcode能自動合成實體變數，就讓它負責；否則，屬性背後的實體變數的名字，應採用駝峰式大小寫命名法但開頭單字小寫，並加上底線（_），這也是Xcode合成名字時的預設規則。

**例如：**  

```objc
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

**而不是：**

```objc
id varnm;
```

### 底線（Underscores）

使用屬性時，一定使用`self.`來存取實體變數，這麼一來，一看就能看出所有的屬性，因為其前頭都有`self.`。區域變數不該含有底線。

## 註解（Comments）

當需要寫註解時，其功用應該是試著解釋「為什麼」這一段程式碼做了某件事。任何註解都應該隨時更新，否則就該刪除。

一般來說，應避免使用區塊型註解，因為程式碼應盡量保持「一看就能明白」的樣子，只需要偶爾插入少數幾行的說明描述即可。但這項慣例不適用於用來產生文件的註解。

## 初始化與解構式（init and dealloc）

`dealloc`方法應位於實作的最上方，緊跟著 `@synthesize`與`@dynamic`，而`init`方法應置於`dealloc` 之下。

## 字面值（Literals）

建立`NSString`、`NSDictionary`、`NSArray`、與`NSNumber`的不可變物件時，應使用其字面值。請特別注意， 不可將`nil`值放進`NSArray`與`NSDictionary`的字面值，將會導致當掉。

**例如：**  

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**而不是：**  

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *ZIPCode = [NSNumber numberWithInteger:10018];
```

## CGRect函式（CGRect Functions）

存取`CGRect`的`x`、`y`、`width`、或`height`時，一律使用[`CGGeometry`函式](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html)，而不要直接存取結構成員。摘錄Apple的`CGGeometry`參考文件：

> 本參考文件中，所有接受CGRect資料結構作為輸入的函式，都會暗中將這些矩形予以標準化，所以，您的程式碼應避免直接讀寫存放在CGRect裡的資料，反之，請使用此處列出的函式來操作矩形與各項特性。

**例如：**  

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

**而不是：**  

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

## 常數（Constants）

比起在程式碼裡直接寫入字串字面值或數字，較佳的作法是使用常數，這麼一來便能輕易地重複使用，想修改時也很方便，不需要大海撈針逐一更動。常數應宣告為`static`常數、而不是以`#define`定義，除非是作為巨集之用。

**例如：**  

```objc
static NSString * const NYTAboutViewControllerCompanyName = @"The New York Times Company";  

static const CGFloat NYTImageThumbnailHeight = 50.0;
```

**而不是：**  

```objc
#define CompanyName @"The New York Times Company"

#define thumbnailHeight 2
```

## 列舉型別（Enumerated Types）

使用`enum`時，建議使用擁有底層固定型別的新型宣告，因為可得到更佳的型別檢查與程式碼補齊功能；SDK裡含有巨集`NS_ENUM()`，方便我們加以運用。

**例如：**  

```objc
typedef NS_ENUM(NSInteger, NYTAdRequestState) {
    NYTAdRequestStateInactive,
    NYTAdRequestStateLoading
};
```

## 私有屬性（Private Properties）

私有屬性應宣告在類別實作檔裡的類別延伸（class extension）裡，也就是無名類目（anonymous category），不該使用有名的類目（例如`NYTPrivate`或`private`），除非你想要延伸擴充某類別。

**例如：**  

```objc
@interface NYTAdvertisement ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```

## 圖檔命名（Image Naming）

請務必讓圖檔名保持一致性，便於管理，才不會讓開發人員發瘋，應採用駝峰式大小寫命名法，首先詳細地描述出該圖檔的用途，然後跟著與它們相關、不帶有前置字串的類別名或屬性名（若有的話），然後跟著顏色、編排等資訊，最後是圖檔的狀態。

**例如：**

* `RefreshBarButtonItem` / `RefreshBarButtonItem@2x` 和 `RefreshBarButtonItemSelected` / `RefreshBarButtonItemSelected@2x`
* `ArticleNavigationBarWhite` / `ArticleNavigationBarWhite@2x` 和 `ArticleNavigationBarBlackSelected` / `ArticleNavigationBarBlackSelected@2x`. 

用途類似的圖檔，應置於Images目錄下，分門別類放在相對應的群組之下。

## 布林（Booleans）

因為`nil`會被決議成`NO`，所以在條件判斷時並必須要與之作比較。千萬不要將某物直接與`YES`作比較，因為`YES`被定義為1，而`BOOL`可能是8位元的任何值。

遵守此規則的話，檔案間便能擁有更高的一致性，閱讀程式碼時也較清楚明白。

**例如：**  

```objc
if (!someObject) {
}
```
		
**而不是：**  

```objc
if (someObject == nil) {
}
```

-----

**若是BOOL型別，底下有兩個例子：**  

```objc
if (isAwesome)
if (![someObject boolValue])
```

**而不是：** 

```objc 
if ([someObject boolValue] == NO)
if (isAwesome == YES) // 千萬別這麼寫
```

-----

若BOOL屬性的名字是個形容詞，那麼可省略“is”前置字串，但可為取值子方法設定約定俗成的名稱，例如：

```objc
@property (assign, getter=isEditable) BOOL editable;
```
描述與範例取自[Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE)。

## 單件設計模式（Singletons）

建立共享的單件物件時，應使用執行緒安全的寫法。
Singleton objects should use a thread-safe pattern for creating their shared instance.
```objc
+ (instancetype)sharedInstance {
   static id sharedInstance = nil;

   static dispatch_once_t onceToken;
   dispatch_once(&onceToken, ^{
      sharedInstance = [[self alloc] init];
   });

   return sharedInstance;
}
```
這麼做可免除[各種可能出現的臭蟲與錯誤] (http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html)。

## Xcode專案（Xcode project）

實體檔案應隨時與Xcode專案檔保持同步，避免檔案四處流散；Xcode專案裡的群組（group）必須在檔案系統中有其相對應的目錄；不僅要根據類型區分程式碼，也要根據功能加以劃分，使之更為清晰。

盡量啟用建構標的之Treat Warnings as Errors這項建構設定，並盡量啟用[額外的警告](http://boredzo.org/blog/archives/2009-11-07/warnings)，越多越好，若你需要忽略某一項特定的警告，可使用[Clang的pragma功能](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas)。

# 其他關於Objective-C程式碼撰寫風格的手冊

如果我們的規範並不符合您的口味，請看看其他人的風格手冊：

* [Google](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
* [GitHub](https://github.com/github/objective-c-conventions)
* [Adium](https://trac.adium.im/wiki/CodingStyle)
* [Sam Soffes](https://gist.github.com/soffes/812796)
* [CocoaDevCentral](http://cocoadevcentral.com/articles/000082.php)
* [Luke Redpath](http://lukeredpath.co.uk/blog/my-objective-c-style-guide.html)
* [Marcus Zarra](http://www.cimgf.com/zds-code-style-guide/)
