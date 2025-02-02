# iOS_IAPTool
iOS in-app-purchase tool, you can use it to upload iap or edit it through ruby scripts.

由于我现在公司产品的特殊性（主要是手游 app），经常性的要加巨大数量的 ipa(in-app-purchases)，真的是数量非常的多，导致我这边的工作量很重，其实添加ipa无非是个体力劳动，重复的操作同一个流程完全不用费脑子，但是苹果却没有提供一个可以批量上传的工具，这就导致了上传的工作效率很低，于是我就想写一个批量上传的工具来提高下效率，经过一番的调研后，我锁定了 [fastlane](https://github.com/fastlane/fastlane) 这个工具。

# 环境搭建

1.安装 Xcode 工具
xcode-select --install

2.安装 fastlane
[sudo] gem install fastlane -NV

注意: Mac安装会出现"ruby/config.h"报错，解决办法参考: https://qiita.com/kitoko552/items/bca3f487074711ff61fc

```shell
brew install rbenv ruby-build
echo 'if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi' >> ~/.zshrc 
rbenv install 2.6.3
rbenv global 2.6.3
```

`which gem` でパスが以下になっていなかったので、ターミナルを再起動して再度パスを確認後直った。

```shell
$ which gem
/Users/<youruser>/.rbenv/shims/gem
```



# 准备工作

正常我们操作iap商品上传都是这样的：
1. 策划同事将商品信息在 Excel 中配置好
2. 我们对照着表中的信息将iTunesconnect中的信息补全，点击保存

很low 很浪费时间对吧！

现在只需要下面几部，就可以解放你的双手:
1. 将 excel 文件格式转换为 .csv 文件（文件另存为，选择.csv 文件即可），文件配置格式见下面
2. 将 .csv 文件用记事本打开，另存为，编码方式选择 UTF-8，解决中文乱码的问题
3. 在脚本中，配置好你的 .csv 文件路径(代码137行)和iap商店图的路径(代码55行review_screenshot)
4. 执行你的脚本 ruby your_script_path 

**注：excel 配置格式**

<img src="https://github.com/xiongdi9456/iOS_IAPTool/blob/master/screenshot/screenshot3.png">


<img src="https://github.com/xiongdi9456/iOS_IAPTool/blob/master/screenshot/screenshot4.png">

# 开始使用
在脚本的开头，需要配置你自己的账号与游戏信息
```
Spaceship::Tunes.login("your apple id")
app = Spaceship::Application.find("your app bundleid")
```

在脚本最下面，有俩个调用方法，根据自己的需求俩者用其一
```
create_iap_demo(app, '/Users/shenjie/Desktop/mytest.csv')

###modify_iap_demo(app, '/Users/shenjie/Desktop/mytest.csv', '.')
```
1.创建 iap 商品时，传入配置好的 csv 文件，选择 create_iap_demo 执行脚本

<img src="https://github.com/xiongdi9456/iOS_IAPTool/blob/master/screenshot/screenshot1.png">

如果你有多个本地化操作，你可以在脚本中加入自定义的本地化：
```
app.in_app_purchases.create!(
    type: iapType, 
    versions: {
      'zh-Hans': {
        name: product_name,
        description: description
      }，
      'zh-Hans': {
        name: product_name,
        description: description
      }，
      'zh-Hans': {
        name: product_name,
        description: description
      }
    },
    reference_name: product_name,
    product_id: product_id,
    cleared_for_sale: true,
    review_notes: " ",
    review_screenshot: "your review screenshot filePath", 
    pricing_intervals: 
    [
      {
        country: "WW",
        begin_date: nil,
        end_date: nil,
        tier: tier
      }
    ]  
  )
```

其中对应语言地区代码可以参考：[RFC 5646 Language Code](https://help.apple.com/asc/appsspec/en.lproj/static.html#itce40fff472)


2.若很不巧你的 app 被苹果打回了，你新加的商品也被打回需要开发者操作，你就可以使用
```
modify_iap_demo(app, '/Users/shenjie/Desktop/mytest.csv', '.')
```
修改每个商品的描述，最简单的就是为其添加一个 ‘.’ 或者删除一个 ‘.’

<img src="https://github.com/xiongdi9456/iOS_IAPTool/blob/master/screenshot/screenshot2.png">

# 其它

该版本比较粗糙，但不影响正常使用，我会定期更新，如果你觉得很好，请给个 Star ✨。

# 补充

特殊商品价格参考：
备用等级A -- 510
备用等级B -- 530
备用等级1 - 5 : 550--590

若有需要配置上述价格的，直接配置后面的对应值即可～
