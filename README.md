说明
============

一个查看.mobileprovision文件（在iOS上）和.provisionprofile文件（在OS X上）的Quick Look插件。

1. 查看mobileprovision内文件相关内容。

2. 查询mobileprovision内证书状态（可用、移除）。

![image](https://github.com/lemon4ex/Provisioning/blob/master/QQ20170707-144659.png)

安装使用
============

OS X El Capitan 以上，内核下引入了Rootless机制，需要先关闭Rootless才能更改沙盒规则，方法自行搜索。

1. 拷贝Provisioning.qlgenerator 到 ~/Library/QuickLook

2. 修改沙盒规则：

修改/usr/share/sandbox/qlmanage.sb文件:

```
(version 1)
(deny default)
(debug deny)

;; 插入下面这行
(allow network-outbound (remote tcp "*:80"))

(allow job-creation
(regex #"^/System/Library/Frameworks/QuickLook.framework/Versions/A/Resources/quicklookd(32)?.app/Contents/MacOS/(qlmanage|quicklookd(32)?)$")
(regex #"^/System/Library/Frameworks/QTKit.framework/"))
(allow process-fork)
```

修改/usr/share/sandbox/quicklook-satellite-legacy.sb文件：

```
(version 1)
(deny default)
(debug deny)

;; 插入下面这行
(allow network-outbound (remote tcp "*:80"))

(allow job-creation
(regex #"^/System/Library/Frameworks/QuickLook.framework/Versions/A/Resources/quicklookd(32)?.app/Contents/MacOS/(qlmanage|quicklookd(32)?)$")
(regex #"^/System/Library/Frameworks/QTKit.framework/"))
(allow process-fork)
```

3. 重置Quick Look服务和缓存:

```
$ qlmanage -r
```

4. 选择.ipa或者.mobileprovision，按空格键即可看到mobileprovision相关的信息内容。

5. 如果想要从Quick Look预览中复制文本，使用以下命令打开隐藏的开关（10.11以上失效）:

```
$ defaults write com.apple.finder QLEnableTextSelection -bool TRUE
$ killall Finder
```

Provisioning
============

A Quick Look plug-in for .mobileprovision files (on iOS) and .provisionprofile files (on OS X).

A ZIP file with the latest version can be [downloaded from the Releases page](https://github.com/chockenberry/Provisioning/releases).

If you want to copy information from the Quick Look preview, you need to [change a hidden Finder preference](http://www.macworld.com/article/1164668/select_and_copy_text_within_quick_look_previews.html) using the command line:

$ defaults write com.apple.finder QLEnableTextSelection -bool TRUE
$ killall Finder

If you're like the rest of us, provisioning can sometimes make your head spin. When that happens, I recommend reading Sean Heber's [provisioning overview](http://bigzaphod.tumblr.com/post/78574849549/provisioning).

Thanks to following individuals who've helped with this project:

* [Pieter Claerhout](https://github.com/pieterclaerhout) for the OS X and profile type support.
* [Kyle Sluder](https://github.com/kylesluder) for expiration (invalidity dates) in the developer certificates.
* [Evgeny Aleksandrov](https://github.com/ealeksandrov) for locale-aware date formatting.


Project Notes
-------------

* The plug-in code can be signed, but currently isn't because it prevents the app from reading user defaults from Xcode (and showing device names and software versions.) If you want to sign the code, set the "Code Signing Identity" build setting  to the "Developer ID: *" automatic setting. If you don't have a Developer ID, get creative. You'll also need to change the SIGNED_CODE definition from 0 to 1.

* There is a "Provisioning (Install)" aggregate target that puts the build of Provisioning.qlgenerator in your ~/Library/QuickLook folder.

* The "Provisioning" schemes runs "qlmanage" with the -p argument set to "~/Library/MobileDevice/Provisioning\ Profiles/Iconfactory_Development.mobileprovision". You won't have this file, so change it to something you do have.

* QuickLook plug-ins sometimes don't like to install. Learn to use "qlmanage -r" to reset the daemon. Using "qlmanage -m plugins | grep mobileprovision" will tell you if the plug-in has been recognized. Sometimes you have to login and out before the plug-in is recognized.

* You can use the command-line to debug, as well. To dump of the output in a .qlpreview bundle, use: "qlmanage -p ~/Library/MobileDevice/Provisioning\ Profiles/Iconfactory_Development.mobileprovision -o /tmp/quicklook". You'll need to make the directory first.


