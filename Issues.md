
[< Back](README.md)

## 代码风格问题
举几个项目里的例子：

### 第一个例子，一段普通的代码实现

修改前：

```Objective-C
- (DPGSTaskTable *)dayTask{
    if (!_dayTask) {
        _dayTask = [[DPGSTaskTable alloc]initWithFrame:CGRectZero style:UITableViewStylePlain];
        __weak DPGSHomeViewController *weakSelf = self;
        [_dayTask addPullToRefreshWithActionHandler:^{
            [weakSelf requestDataFrowServer];
        }];
        _dayTask.emptyBlock = ^(){
            [weakSelf requestDataFrowServer];
        };
        _dayTask.cellTapped = ^(DPGSTaskObject *object){
            switch (object.stata) {
                case initiraState:
                {
                    //初始状态－》跳转到相应链接
                    if (object.taskEvent.length>0) {
                         [DPAppRoutes handleURL:[NSURL URLWithString:object.taskEvent]];
                    }
                   
                }
                    break;
                case canGet:
                {
                   [weakSelf getTaskAwardWithTaskid:object.taskId];
                }
                    break;
                case finished:
                {
                    
                }
                    break;
                default:
                    break;
            }
            
        };

    }
    return _dayTask;
}
```

修改后：

```Objective-C
// 注意 switch 中大括号位置，以及前后空格
// 注意 block 声明时 小括号 和 大括号 间的空格
// 推荐将 break 写到大括号块内部
- (DPGSTaskTable *)dayTask {
    if (!_dayTask) {
        _dayTask = [[DPGSTaskTable alloc] initWithFrame:CGRectZero style:UITableViewStylePlain];
        __weak DPGSHomeViewController *weakSelf = self;
        [_dayTask addPullToRefreshWithActionHandler:^{
            [weakSelf requestDataFrowServer];
        }];
        _dayTask.emptyBlock = ^() {
            [weakSelf requestDataFrowServer];
        };
        _dayTask.cellTapped = ^(DPGSTaskObject *object) {
            switch (object.stata) {
                case initiraState: {
                    //初始状态－》跳转到相应链接
                    if (object.taskEvent.length > 0) {
                        [DPAppRoutes handleURL:[NSURL URLWithString:object.taskEvent]];
                    }
                    break;
                }
                case canGet: {
                    [weakSelf getTaskAwardWithTaskid:object.taskId];
                    break;
                } 
                case finished:
                    break;
                default:
                    break;
            }
        };
    }
    return _dayTask;
}

```

### 第二个例子，关于枚举定义

修改前：

```Objective-C
typedef enum{
    finished =3,
    canGet = 2,
    initiraState =1
}taskState;

typedef enum{
    common =0,
    up = 1,
    superPlay = 2,
}taskType;
```

修改后：

```Objective-C
// 这里修改了枚举命名，枚举类型的全局的，没有前缀很容易造成冲突
// 通知修改了代码风格，注意等号前后的空格，大括号间的空格

typedef NS_ENUM(NSInteger, DPGSTaskState) {
    DPGSTaskStateInitiraState = 1,
    DPGSTaskStateCanGet = 2,
    DPGSTaskStateFinished = 3,
};

typedef NS_ENUM(NSInteger, DPGSTaskType) {
    DPGSTaskTypeCommon = 0,
    DPGSTaskTypeUp = 1,
    DPGSTaskTypeSuperPlay = 2,
};
```

### 第三个例子，类定义

修改前：

```Objective-C
typedef enum{
    common =0,
    up = 1,
    superPlay = 2,
}taskType;
@interface DPGSTaskObject : NSObject
/**
 *  taskId
 */
@property(nonatomic ,copy)NSString *taskId;
/**
 *  titleLabel
 */
@property(nonatomic ,copy)NSString *titleLabelStr;

// 剩下的省略掉了
// ...

@end
```

修改后：

```Objective-C
// 类定义与枚举定义之间的空行，枚举命名修改见上一个例子
// 属性定义中的空格，@property 后面 和 变量类型 前面
// 逗号分割符后面加空格而不是前面，和西文写法保持一致

typedef NS_ENUM(NSInteger, DPGSTaskType) {
    DPGSTaskTypeCommon = 0,
    DPGSTaskTypeUp = 1,
    DPGSTaskTypeSuperPlay = 2,
};

@interface DPGSTaskObject : NSObject
/**
 *  taskId
 */
@property (nonatomic, copy) NSString *taskId;
/**
 *  titleLabel
 */
@property (nonatomic, copy) NSString *titleLabelStr;
@end

```

### 第四个例子，命名太随意

修改前：

```Objective-C
- (void)layoutUI{
    
    [_headerView mas_makeConstraints:^(MASConstraintMaker *make) {
        make.top.mas_equalTo(0);
        make.left.mas_equalTo(0);
        make.right.mas_equalTo(0);
        make.height.mas_equalTo(220);
    }];
    
    [self.taskTitleSeg mas_makeConstraints:^(MASConstraintMaker *make) {
        make.top.equalTo(self.headerView.mas_bottom);
        make.left.mas_equalTo(0);
        make.right.mas_equalTo(0);
        make.height.mas_equalTo(45);
    }];
    // ...
}
- (void)setNav{
    
    UIButton *leftNavItem = [UIButton buttonWithType:UIButtonTypeCustom];
    [leftNavItem setImage:dp_NavigationImage(@"back.png") forState:UIControlStateNormal];
    [leftNavItem addTarget:self action:@selector(back) forControlEvents:UIControlEventTouchUpInside];
    [_headerView addSubview:leftNavItem];
}
```

修改后：

```Objective-C
// 不要随意使用 set 前缀作为方法名称，可以使用 setup 之类的，命名要清晰
// 函数实现之间保留一行空行
// 使用点语法访问变量而不是ivar
// 函数声明大括号前缺少空格
- (void)setupViewConstrains {
    [self.headerView mas_makeConstraints:^(MASConstraintMaker *make) {
        make.top.mas_equalTo(0);
        make.left.mas_equalTo(0);
        make.right.mas_equalTo(0);
        make.height.mas_equalTo(220);
    }];
    [self.taskTitleSeg mas_makeConstraints:^(MASConstraintMaker *make) {
        make.top.equalTo(self.headerView.mas_bottom);
        make.left.mas_equalTo(0);
        make.right.mas_equalTo(0);
        make.height.mas_equalTo(45);
    }];
}

- (void)setupNavigationBar {
    UIButton *leftNavItem = [UIButton buttonWithType:UIButtonTypeCustom];
    [leftNavItem setImage:dp_NavigationImage(@"back.png") forState:UIControlStateNormal];
    [leftNavItem addTarget:self action:@selector(back) forControlEvents:UIControlEventTouchUpInside];
    [self.headerView addSubview:leftNavItem];
}
```

### 第五个例子，各种缩进样式，大块注释代码
这样的例子很多久，不要删除一句代码中的一部分时，删掉后不补齐，或者好几个字符直接用一个空格替换。

```Objective-C
- (UITableViewCell *)tableView:(DPCollapseTableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    // ...
    NSArray *titleArray=[NSArray arrayWithObjects:@"支付宝钱包", @"银行卡快捷支付",@"银联卡在线支付",nil];
    NSArray *infoArray=[NSArray arrayWithObjects:@"支持支付宝余额和银行卡充值", @"支持银行卡快捷支付",@"无需开通网银",nil];
    NSArray *imageArray=[NSArray arrayWithObjects:dp_RedPacketImage(@"aliPay.png"), dp_RedPacketImage(@"bank.png"), dp_RedPacketImage(@"yinlian.png"), nil];
      [cell iconImageViewImage:[imageArray objectAtIndex:indexPath.row]];
      [cell payTitleString:[titleArray objectAtIndex:indexPath.row]];
      [cell payInfoString:[infoArray objectAtIndex:indexPath.row]];
    [cell paySelectedView:NO];
    if (indexPath.row==_selectedChargeType) {
        [cell paySelectedView:YES];
    }else{
        [cell paySelectedView:NO];
    }
    return cell;
}
```


```Objective-C
// , 号后面的空格
// 二元符号（=号）前后空格
// 缩进问题
- (UITableViewCell *)tableView:(DPCollapseTableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    // ...
    NSArray *titleArray = [NSArray arrayWithObjects:@"支付宝钱包", @"银行卡快捷支付", @"银联卡在线支付", nil];
    NSArray *infoArray = [NSArray arrayWithObjects:@"支持支付宝余额和银行卡充值", @"支持银行卡快捷支付", @"无需开通网银", nil];
    NSArray *imageArray = [NSArray arrayWithObjects:dp_RedPacketImage(@"aliPay.png"), dp_RedPacketImage(@"bank.png"), dp_RedPacketImage(@"yinlian.png"), nil];
    [cell iconImageViewImage:[imageArray objectAtIndex:indexPath.row]];
    [cell payTitleString:[titleArray objectAtIndex:indexPath.row]];
    [cell payInfoString:[infoArray objectAtIndex:indexPath.row]];
    [cell paySelectedView:NO];
    if (indexPath.row == _selectedChargeType) {
        [cell paySelectedView:YES];
    } else {
        [cell paySelectedView:NO];
    }
    return cell;
}
```

还有 DPPayRedPacketVC.m 这个类，里面的缩进问题很多，尤其是 block 的，还有许多无用代码的注释。

### 第六，关于日志
提交日志，不要永远是 fix bug，或者自己的名字之类的。
至少要写清楚修改了哪该模块，修复了什么内容。

关于代码格式化，虽然有插件 `clang-format` 可以用，但我希望先养成手写出的代码就是标准的习惯，之后在去使用插件。

