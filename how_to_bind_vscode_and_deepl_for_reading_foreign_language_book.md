# 如何借助DeepL阅读英文原版书？

[TOC]

上了大学，准确来说，是上硕士课程开始，我突然发现一件事，所以

我的研究领域的论文，能看的全是英文，我一搜索顶级会议，才知道 这些都是国际会议，来自不同的国家的人要相互交流，只能用目前被使用最广泛的语言 —— 英语。

于是，最后的结果就是。专业领域的知识，大家只能去读英文资料

> 从今往后，非虚构类书籍只读英文原版。

去年开始，因为笑来老师的提醒，我开始尝试阅读英文原版书。于是，慢慢地，我开始体会他的另一句话：

> 好书这东西，能读原版的话就一定要直接读原版，千万不要读译本——被坑了都不知道自己是怎样被坑的，更不知道自己被坑成了什么样子…… 

书这种东西，能看原版就尽量看原版，否则你被

一度想开始认真读几本原版书，但是看到整本书都是英文字母，内心还是不免发怵。买来的纸质书、电脑里的 pdf，就和当年背的英语词汇书一样，长久地停在前几页。

2021年，“笑来” 公众号发表了一篇文章 [《当翻译竟然变成了文本编辑》](https://mp.weixin.qq.com/s?__biz=MzIwNzA0NjExNw==&mid=2247489038&idx=1&sn=25bca47f88e3e5759512fa00f962e19c&scene=21#wechat_redirect)，还在 github 开源了他基于 deepL 翻译英文书籍的工作流。

这篇文章给了我一个新思路：我直接边看边翻译，是不是能够帮助我读原版书？

首先，有了 deepL 这个工具，“翻译” 的工作量并不大。因为它的翻译绝大部分都很准确，我只需要改改有明显错误的地方。其次，有了笑来老师的翻译工作流，效率能够进一步提升。还有，结合 github 这种版本管理工具，我的修改可以作为我读书的工作量证明（proof of work）。持续看到自己的阅读记录，这对我坚持读完一本书显然是有促进作用的。

事实上，笑来老师很快就在定投课堂里分享他边翻译边看书的意外收获，比如调用更多感官来阅读，还改正了他因为用双拼导致的错别字问题…… 这一定程度验证了我的猜想。

我花了两个周末折腾，参考着他翻译工作流做了一遍（基于 python 代码）。结果，最后因为我的 Amazon 账号被封（还没解决），我暂时没用上。

恰好他最近推荐了一本在 github 开源的书籍《World After Captial》，而这本书每一章都是 markdown 格式（前面的 python 脚本主要用于处理 html 格式的书籍）。于是，我又花了点时间走通了一个非 python 代码版本的翻译工作流。

就我的目的 —— 用 deepL 辅助我看原版书而言，这个工作流也暂时够用。更为重要的是，我发现这种方式的使用门槛相对低很多，即使不懂编程的人也能很快上手使用。所以，我写了这个教程，希望对有缘人有帮助。

## 一、简介

这个教程用到的软件主要有：VSCode、DeepL、Alfred

-  VSCode 是一个微软公司开发的文字编辑器，因为跨平台、支持丰富的插件等特性，成为程序员最喜欢的编辑器之一。他们可以用来写代码，普通人也可以用来码字、写文档，或者像下面我们要做的事情一样，用来读书、翻译……
-  deepL 是一个翻译软件，它自己号称是世界上最准确的翻译软件。从实际体验看，它的翻译效果的确很惊人。我首先从那篇文章里了解到这个软件，后来又从在出版公司工作的朋友们那里得知，他们已经用这个软件很长时间了。再后来，我看 deepL 相关的论坛信息，早在2017年，deepL 在翻译界已经很火爆了。
-  Alfred 是一个 mac 电脑上的效率提升软件，可以通过设置工作流（workflow）来实现重复工作的简化。比如，把需要重复的 “多个鼠标点选、键盘输入操作” 变成 “1 个” 快捷键，正如我们接下来要做的事情一样。
- 我目前实现的原版书阅读（翻译）工作流效果：

![effect](https://cdn.jsdelivr.net/gh/pierrelzw/blog_images/SOULFuGEcHTmtp3-20211219173719844.gif)

上面的动图里包含两件事：
1. 翻译光标所在行的内容，并把结果粘贴在当前行下方空一行的位置

  2. 对翻译结果（中文）进行排版优化（ from 笑来翻译工作流）：

     > - `"` 要相应地替换成 `“”`
     > - `'` 要相应地替换成 `‘’`
     > - 无论是单引号还是双引号，都要与相邻的字符之间留有一个空格；标点符号 `，。？！` 除外；
     > - 破折号统一使用 `——`（前后有空格，除非与标点符号相邻）而非 `—` 或者 `--`；
     > - 外国姓名之间的符号为 `·`；
     > - ……

而这两件事仅通过两个快捷键完成： `1-ctrl+alt+cmd+p`  和 `2-ctrl+alt+cmd+o`。

第一件事看起来似乎比较简单。因为 deepL 可以设置快捷键（比如我设置的是两次 cmd+c），允许用户快速调用 deepL 并翻译剪切板里的内容。更为可贵的是，deepL 还支持把翻译结果通过 `Insert to xxx`传回你正在使用的编辑器（`xxx`)。

【插入图片】

但是，这需要一次鼠标操作：移动对准+点击。如果只是翻译一段话，那一次这样的操作并没什么负担，但是如果你要翻译的是一本书……事实证明，我点了 5 次我就不想再点那个按钮了。



而第二件事，看起来似乎更加复杂，难以想象要手动一步一步、对每段中文文本都这么处理。最好的方式当然是采用脚本。


## 二、如何实现？

### 1. 准备工作

1. 一台 Macbook 电脑
2. 下载并安装 [VSCode](https://code.visualstudio.com/) (https://code.visualstudio.com/)
3. 下载并安装 [DeepL](https://www.deepl.com/en/app/) (https://www.deepl.com/en/app/)
4. 下载、安装并购买 Alfred，可以参考 `https://github.com/xiaolai/apple-computer-literacy/blob/main/alfred.md`
5. 学习如何打开 terminal（比较简单可上网搜索）

### 2. 设置 Alfred workflow

1. 打开  Alfred Preferences

快捷键（alt+space）> 输入 alfred（不用输入完整的）> show Alfred Preferences，回车打开

![image-20211212213332513](https://cdn.jsdelivr.net/gh/pierrelzw/blog_images/alfred_open.png)

2. 创建新的 workflow

![image-20221030184024170](/Users/lizhiwei/Library/Application Support/typora-user-images/image-20221030184024170.png)

![image-20221030184053726](/Users/lizhiwei/Library/Application Support/typora-user-images/image-20221030184053726.png)

![image-20221030184158287](/Users/lizhiwei/Library/Application Support/typora-user-images/image-20221030184158287.png)

3. 创建快捷键（HotKey）

   空白区域点击右键 > Triggers > Hotkey 

![image-20221030184320717](/Users/lizhiwei/Library/Application Support/typora-user-images/image-20221030184320717.png)

设置快捷键（我这里是 `cmd+shift+alt+j` ），然后设置 `Argument` 为 `Selection in macOS`（表示光标选择的内容将作为这个 workflow 的输入参数）

![image-20221030184419558](/Users/lizhiwei/Library/Application Support/typora-user-images/image-20221030184419558.png)



4. 创建快捷键关联的动作（Actions） -> Run NSAppleScript

![image-20211212215652068](https://cdn.jsdelivr.net/gh/pierrelzw/blog_images/b42L3UakAHzdOmi.png)

![image-20221030184548573](/Users/lizhiwei/Library/Application Support/typora-user-images/image-20221030184548573.png)

5. 把下面的代码粘贴进去，替换里面的内容

```
on alfred_script(q)
  -- your script here

tell application "Visual Studio Code"
	activate
	delay 2
	tell application "System Events"
		key code 37 using command down
		delay 1
		key code 8 using command down
		key code 8 using command down
	end tell
end tell
-- 以上是 cmd+l 选中当前整行/段；而后连续两次 cmd+c 将选中文字发给 DeepL

delay 5

tell application "Visual Studio Code"
	activate
	delay 2
	tell application "System Events"
		key code 124 using command down
		-- key code 124 using command down
		key code 36 using command down
		key code 36 using command down
	end tell
end tell

-- 以上是在 VSCode 中输入两个空行

-- 以下是模拟鼠标点击，按 DeepL 上的 “Insert to...” 链接
-- 也可以将以下部分，独立出来，单独为 “Insert to...” 设置个快捷键（我设置的是 “ctrl+alt+cmd+i”）
tell application "System Events"
	tell process "DeepL" to tell window 1
		activate
		set w_position to its position
		set w_size to its size
	end tell
end tell

set px to item 1 of w_position
set py to item 2 of w_position
set sx to item 1 of w_size
set sy to item 2 of w_size

tell application "System Events"
	click at {px + sx - 190, py + sy - 95} -- Insert to link
end tell

end alfred_script
```

![image-20221030184928293](/Users/lizhiwei/Library/Application Support/typora-user-images/image-20221030184928293.png)

【点击cache，运行会快一些？】



这里的脚本和笑来老师原版的脚本有小小的不同，原版的 VSCode 空 2 行实现可能有点小问题：翻译结果会直接粘贴在英文内容下方，没有空一行。这会导致 github 网页显示有问题 —— 中英文连一块了，如下图红框所示，而我实际想要的是绿框里的排版。

![image-20211212220536195](https://cdn.jsdelivr.net/gh/pierrelzw/blog_images/hwDjtqIXgHKlE9S.png)

具体更改如下图红框所示：

![image-20211219174602469](https://cdn.jsdelivr.net/gh/pierrelzw/blog_images/image-20211219174602469.png)

- `key code 36`对应的是回车键`Return`，表示换行；
- `key code 124`对应向右箭头`RightArrow`，所以 `key code 124 using command down` 是`cmd + 向右箭头`，在 VSCode 里其实就是移动光标到到本行末尾的快捷键；
- ` —— `表示注释，同行 ` —— ` 之后的内容不会被程序执行。

6. 在 alfred 设置页面把两个图标连上，表示快捷键与后面的操作关联。做完这一步，就完成 alfred workflow 设置

![image-20221030185041647](/Users/lizhiwei/Library/Application Support/typora-user-images/image-20221030185041647.png)

7. 用 VSCode 打开一个文档，使用上面设置的快捷键测试。没有错误的话，它能够实现下面这些步骤：

> 1. `cmd+l` 选中当前整行；而后连续两次` cmd+c` 将选中文字发给 DeepL
> 2. 光标移动到当前行末尾，回车两次（和之前的当前行空一行）
> 3. 模拟鼠标点击，按 DeepL 上的 “Insert to...” 链接，把翻译结果粘贴回VSCode（当前光标所在位置）

注意：第 2-3 步需要几秒钟来实现，在等待过程中，不要移动光标，否则粘贴位置会出错。这个时候，我一般会先看看原文的内容。因为英文阅读速度比较慢，所以足够上面的 workflow 运行完成了。然后，我会重新再对照翻译结果逐句看一遍，如果发现有明显的错误，就顺手改一下。



### 3. VSCode设置中文排版快捷键

1. 安装 ssmacro 插件

- 打开 VSCode ，点击下图图标或者用`cmd+shift+x`打开 extension 页面

  【图标框起来】

  ![image-20221030190914309](/Users/lizhiwei/Library/Application Support/typora-user-images/image-20221030190914309.png)

- 输入 ssmacro ，点击 install，完成安装

  ![image-20221030190940917](/Users/lizhiwei/Library/Application Support/typora-user-images/image-20221030190940917.png)



2. 设置快捷键

   打开 VSCode，快捷键  `cmd+shift+p`  > 输入 keyboard > 选择 `Open Keyboard Shortcuts(JSON)` > 回车，一个名为 keybings.json 的文件会被自动打开

![image-20221030201122284](/Users/lizhiwei/Library/Application Support/typora-user-images/image-20221030201122284.png)

3. 排版快捷键设置

![image-20221030201208564](/Users/lizhiwei/Library/Application Support/typora-user-images/image-20221030201208564.png)

把下面的内容加入中括号`[ ]`之间，如果有其他用大括号`{}`包含的内容，用逗号 `,` 隔开

```
{
	"key": "ctrl+alt+cmd+o", 
	"command": "ssmacro.macro",
	"args": {"file": "regex.json"},
	"when": "editorTextFocus"
}
```

上面这段配置的意思其实很简单：

- 设置了一个快捷键（`ctrl+alt+cmd+o`）
- 用 ssmacro 调用（批量执行）
- 执行是输入一个名为 regex.json 的文件（包含若干基于**正则表达式**的中文排版脚本）

4. regex.json  下载安装（复制）

   如果想要 ssmacro 插件准确调用 regrex.json 文件，需要满足以下条件：

>  上一步的 `“args”: {“file”:“regex.json”},` 有两种写法：
>
>  - 第一种：像上文中一样用 `“file”:` 指定，那么这个文件应该在 `$HOME/.vscode/extensions/joekon.ssmacro-0.6.0/macros/` 文件夹内；
>  - 第二种：使用 `“path”:`，然后在其后设定批处理文件（`json`文件）的绝对路径。

我用的是第一种方法。为了方便使用，我在飞书上传了一份 regex.json，可按照下面的步骤配置使用：

- 打开浏览器，输入以下链接，点击下载（理论上会下载到 Download 文件夹） `https://s7wabnxfe5.feishu.cn/file/boxcnQ68ltBhpZ1V99nkS4jALrg`

- 打开 terminal

- 复制并粘贴以下命令到 terminal，回车

  ```cp $HOME/Downloads/regex.json $HOME/.vscode/extensions/joekon.ssmacro-0.6.0/macros/```

  >  上面是一段本文唯一代码，用来把下载好的 regex.json 文件，复制到 ssmacro 可以调用的目录下。
  >
  >  `cp`表示复制，然后是空格，紧接着是你下载好的 regex.json 的路径，再然后又是一个空格，最后是目标路径（我们希望存放 regex.json 的目录）

  运行后看 terminal 是否报错，没报错的话就没问题了。

- 当然，你可以复制并粘贴以下命令到 terminal，回车，再检查一下。如果出现红框里的内容，则表示 regex.json 已经被拷贝到 ssmacro 目录下，可以被调用。

  ``ls $HOME/.vscode/extensions/joekon.ssmacro-0.6.0/macros/regex.json ``

  ![image-20211212232557641](https://cdn.jsdelivr.net/gh/pierrelzw/blog_images/EyWSbLJmpeOrF46.png)

6. 测试测试快捷键

   打开 vscode，输入 `cmd+n`，vscode会创建一个新文件，复制以下内容并粘贴到新建的文件当中

   ```markdown
   这是一个“测试”——测试快捷键(keyboard shorcut)是否能生效
   ```

   ![image-20221030222038532](/Users/lizhiwei/Library/Application Support/typora-user-images/image-20221030222038532.png)

   把光标移动到上述文字所在行，摁下快捷键，应该有如下效果。

   ![image-20221030222047505](/Users/lizhiwei/Library/Application Support/typora-user-images/image-20221030222047505.png)

   - 双引号和之前的文字之间多了空格

   - 中文、英文之间也多了一些空格
     

更多排版功能，可以自主查看 regrex.json 这个文件里的规则



## 三、后记

2022-10-30

再次更新这个教程的时候，没想到时间已经快过去 1 年了。

- 2021年年底-2022年年初，我完成了 《World After Captial》全书的边阅读边翻译，这本书其实内容不多。不过我还是看了
- 后来因为工作比较忙，我零碎地翻译

2021-12-19

走通这个流程后，我当天在走步机上走了大概 4 个小时。把《World After Captial》的第一部分看完了，也 “翻译” 完了。这个速度还是挺惊人的。之前我看了大半年，都没能真正看完一本原版书，现在半天就搞定一本书的 1/5 了。这本书总共 5 部分，如果每个部分文字数量差不多，按照这样的速度，我说不定能在年前把这本书看完。

更为关键的是，我再也没有那种发怵感了。一方面是绝大部分英文生词直接就有翻译了，另一方面，deepL 的翻译质量真的惊人，99% 的翻译都非常准确（我预估的），比我自己理解得好。

最后，我还把自己的 proof of work 上传到了我在 github 仓库里，感兴趣的可以直接看（另外，我不知道这样 fork 开源书籍然后增加内容再传播，是否侵权了？有懂的同学麻烦帮忙指出）： `https://github.com/pierrelzw/WorldAfterCapital/tree/pow`。

本文只涵盖了  github 原文展示的工作流的一部分，更多进阶、提高效率的方法可以参考原文： https://github.com/xiaolai/apple-computer-literacy/tree/main/deepl-aided-semi-automatic-book-translation，比如还有

- 用代码实现 deepL API 调用，直接翻译整本书

- 用代码直接对整本书的中文排版进行优化

  > 这两步做完以后，快捷键都不需要了，正如那篇文章所说：《当翻译只剩文字编辑工作》……

- 更进一步地，还可以设置用浏览器实时渲染查看。因为不作特殊设置的话，VSCode 无法显示 markdown 文本里的图片，网页显示是比较好的选择

- ……

另外，我真的极度推荐这个开源仓库`https://github.com/xiaolai/apple-computer-literacy`简直到处是宝藏：

- terminal 优化（如它的标题`start_from_terminal.md`所言，这可能是拿到 Macbook 后要做的第一件事了。我一直没有优化，所以经常因为难看的 terminal 界面而难受）

- alfred 使用（带我进入了新世界，最近正在研究 applescript 怎么写）

- 如何把 markdown 在 Edge 浏览器里渲染出来，然后用它的自动朗读功能朗读文章

  >  这相当于给自己又增加了一个输入维度，全方位地调动自己的感官去阅读

- ……

我使用 Macbook已经7 年了。最近刚买 Macbook 的同事问我：有什么软件推荐？当时我的脑子一片空白，好容易憋出来一个不一定所有人都需要的、而且其实全平台都支持运行的 Typora。当时隐隐觉得哪里不对，但是又说不上来。

直到最近我才意识到：其实，我和一个刚刚开始使用 Macbook 的新手没啥差别，对如何高效使用它几乎一无所知，我也需要 apple-computer-literacy（苹果电脑使用扫盲）。

