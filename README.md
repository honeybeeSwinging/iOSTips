# iOSTips
iOS开发中常用到的小技巧
---

### 隐藏导航栏下面的黑线

* 第一种方案 设置一张背景图片，然后在设置一张shadowImage

```Objective-C
[[UINavigationBar appearance] setBackgroundImage:[[UIImage alloc] init] forBarPosition:UIBarPositionAny barMetrics:UIBarMetricsDefault];
[[UINavigationBar appearance] setShadowImage:[[UIImage alloc] init]];
```

* 第二种方案 设置 clipsToBounds = YES

```Objective-C
[UINavigationBar appearance].clipsToBounds = YES;
```
* 第三种方案 

    遍历UINavigationBar的所有子视图，发现有UIImageView类型的视图就remove掉，或者设成隐藏状态（hidden）

### 获取 UITexView 高度

```Objective-C
- (CGSize)getStringSizeInTextView:(NSString *)string InTextView:(UITextView *)textView {
    //实际textView显示时我们设定的宽
    UITextView* localTextView = textView;
    CGFloat contentWidth = CGRectGetWidth(localTextView.bounds);
    //但事实上内容需要除去显示的边框值
    CGFloat broadWith    = (localTextView.contentInset.left + localTextView.contentInset.right
                            + localTextView.textContainerInset.left
                            + localTextView.textContainerInset.right
                            + localTextView.textContainer.lineFragmentPadding/*左边距*/
                            + localTextView.textContainer.lineFragmentPadding/*右边距*/);
    
    CGFloat broadHeight  = (localTextView.contentInset.top
                            + localTextView.contentInset.bottom
                            + localTextView.textContainerInset.top
                            + localTextView.textContainerInset.bottom);
    //由于求的是普通字符串产生的Rect来适应textView的宽
    contentWidth -= broadWith;
    
    CGSize InSize = CGSizeMake(contentWidth, MAXFLOAT);
    
    NSMutableParagraphStyle *paragraphStyle = [[NSMutableParagraphStyle alloc]init];
    paragraphStyle.lineBreakMode = localTextView.textContainer.lineBreakMode;
    NSDictionary *dic = @{NSFontAttributeName:localTextView.font, NSParagraphStyleAttributeName:[paragraphStyle copy]};
    
    CGSize calculatedSize =  [string boundingRectWithSize:InSize options:NSStringDrawingUsesLineFragmentOrigin | NSStringDrawingUsesFontLeading attributes:dic context:nil].size;
    
    CGSize adjustedSize = CGSizeMake(ceilf(calculatedSize.width),calculatedSize.height + broadHeight);
    return adjustedSize;
}
```

### json格式字符串转字典，字典转json格式字符串

```
/*!
 * @brief 把格式化的JSON格式的字符串转换成字典
 * @param jsonString JSON格式的字符串
 * @return 返回字典
 */

+ (NSDictionary *)dictionaryWithJsonString:(NSString *)jsonString {
    if (jsonString == nil) {
        return nil;
    }
    
    NSData *jsonData = [jsonString dataUsingEncoding:NSUTF8StringEncoding];
    NSError *err;
    NSDictionary *dic = [NSJSONSerialization JSONObjectWithData:jsonData options:NSJSONReadingMutableContainers error:&err];
    if(err) {
        NSLog(@"json解析失败：%@",err);
        return nil;
    }
    return dic;
}

+ (NSString*)dictionaryToJson:(NSDictionary *)dic {
    NSError *parseError = nil;
    NSData *jsonData = [NSJSONSerialization dataWithJSONObject:dic options:NSJSONWritingPrettyPrinted error:&parseError];
    return [[NSString alloc] initWithData:jsonData encoding:NSUTF8StringEncoding];
}
```
