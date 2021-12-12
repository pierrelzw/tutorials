# VSCode+DeepL简易翻译工作流

[TOC]

今年一度想开始认真读几本原版书，但是看到整本的英文字母，内心还是不免发怵。买来的纸质书、电脑里的pdf，就和当年背的英文词汇书一样，停在了前几页。

最近，笑来老师写了一篇关于翻译的文章，还在github开源了他基于这个软件翻译英文原版书的工作流。

这篇文章给了我一个新思路：我直接边看边翻译，是不是更有助于我阅读原版书？首先，“翻译” 的工作量并不大。因为deepL的翻译绝大部分都非常准确，我只需要改改有明显错误的地方。而且有了笑来老师这个翻译工作流，效率就更高了。其次，我的简单修改还可以作为我读书的工作量证明（proof of work），这对长期。

事实上，笑来老师很快就在定投课堂的几节课程里分他边翻译边看书的意外收获，比如调用更多感官来阅读，还改正了他因为用双拼导致的错别字问题……这一定程度验证了我的猜想。

我花了两个周末折腾，参考着他基于python代码调用deepL API的自动翻译工作流做了一遍。结果，最后因为我的amazon账号被封（还没解决），我暂时没用上。

恰好他最近推荐了一本在github开源的书籍《World After Captial》，而这本书每一章都是markdown格式（前面的python脚本主要用于处理html格式的书籍）。于是，我又花了点时间走通了一个非python代码版本的翻译工作流。

就我的目的而言，这个工作流也暂时够用，当然，它也有点小小的局限性（后面会说）。

更为重要的是，我也发现这种方式的使用门槛相对低，即使不懂编程的普通人也能很快上手使用。所以，我写了这个教程，希望对有缘人有帮助。

## 简介

- VSCode是一个微软开发的文字编辑器，因为跨平台、支持丰富的插件等，成为程序员最喜欢的编辑器之一。他们可以用来写代码，当然，普通人也可以用来码字、写文档，或者像下面的动图一样，用来读书、翻译。
- DeepL是一个翻译软件，它自己号称是世界上最准确的翻译软件。从实际体验看，它的翻译效果的确很惊人，我首先从笑来老师那边听过这个软件，后来又从在出版公司工作的朋友们那里得知，他们已经用这个软件很长时间了。DeepL还支持一个非常不错的功能 ——  用快捷键（两次cmd+c）可以直接翻译选中内容
- 我目前看书的工作流效果展示：

![effect](https://s2.loli.net/2021/12/12/fplB2LyNPOFGxiA.gif)

上面的图动图里包含两件事：
1. 翻译光标所在行的内容，并把结果粘贴在当前行下方空一行的位置

  2. 对翻译结果（中文）进行排版优化（from笑来翻译工作流）：

     > - `"` 要相应地替换成 `“”`
     > - `'` 要相应地替换成 `‘’`
     > - 无论是单引号还是双引号，都要与相邻的字符之间留有一个空格；标点符号 `，。？！` 除外；
     > - 破折号统一使用 `——`（前后有空格，除非与标点符号相邻）而非 `—` 或者 `--`；
     > - 外国姓名之间的符号为 `·`；
     > - ……

而这两件事，仅通过两个快捷键完成： `1-ctrl+alt+cmd+p`  和 `2-ctrl+alt+cmd+o`。

第一件事看起来似乎比较简单。因为deepL可以设置快捷键（比如我设置的是两次cmd+c），允许用户把剪切板里的内容自动传输到软件里进行翻译。更为可贵的是，deepL还支持把翻译结果通过 `Insert to [xxx]`传回你正在使用的编辑器（`xxx`)。但是，这需要一次鼠标操作：移动对准+点击。如果只是翻译一段话，那一次这样的操作并没什么负担，但是如果你要翻译的是一本书……事实证明，我点了5次我就不想再点那个按钮了。

而第二件事，看起来似乎更加复杂，更是难以想象要手动完成。最好的方式当然是要采用脚本。


## 二、如何实现？

> 工作环境：一台macbook电脑

本文主要参考笑来老师在github开源的苹果电脑扫盲（apple-computer-literacy）文档实现。因为使用了一些苹果上的软件，所以本文仅针对mac电脑有效。

### 准备工作

1. 下载并安装[VSCcode](https://code.visualstudio.com/)
2. 下载并安装[DeepL](https://code.visualstudio.com/)
3. 下载并安装Alfred，可以参考[xiaolai /apple-computer-literacy/alfred.md](https://github.com/xiaolai/apple-computer-literacy/blob/main/alfred.md)

### 设置Alfred workflow

1. 成功安装了alfred以后，可以直接用快捷键（我设置的是cmd+space）调用它，然后输入alfred，第一个就是alfred prefence，所以直接回车

![image-20211212213332513](https://s2.loli.net/2021/12/12/RLnEytfIVoHeM4T.png)

2. 点击左下角的 + ，创建一个blank workflow 

![image-20211212214843826](https://s2.loli.net/2021/12/12/l7p1aLrEiGAuoWR.png)

3. 创建快捷键: 空白区域点击右键：`Triggers - Hotkey`，设置`Argument`为`Selection in macOS`（表示光标选择的内容为输入这个workflow的参数）

![image-20211212215208459](https://s2.loli.net/2021/12/12/7SOir6wUmvN92ao.png)

4. 创建`Actions - Run NSAppleScript`

![image-20211212215652068](https://s2.loli.net/2021/12/12/b42L3UakAHzdOmi.png)

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
-- 以上是 cmd+l 选中当前整行；而后连续两次 cmd+c 将选中文字发给 DeepL

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

这里的脚本和笑来老师原版的脚本有小小的不同，原版的VSCode空2行实现可能有点小问题：翻译结果会直接粘贴在英文内容下方，没有空一行，这会导致markdown内容在github上显示的时候中英文连一块儿，就像下图的红框一样，而不是我想要的绿框的样子。

![image-20211212220536195](https://s2.loli.net/2021/12/12/hwDjtqIXgHKlE9S.png)

于是我改了一下脚本，这里`key code 36`对应的是`Return`,`key code 124`对应`RightArrow`，`--`表示注释，不会被运行。所以 `key code 124 using command down` 是`cmd + 向右箭头`，在vscode里其实就是移动光标到到本行末尾的快捷键。

<img src="https://s2.loli.net/2021/12/12/NQt7scpKPSrEBdT.png" alt="image-20211212215817050" style="zoom:50%;" />

6. 把两个图标连上，完成alfred workflow设置

![image-20211212221603532](https://s2.loli.net/2021/12/12/QDNJeqPOIfC6MdW.png)

### VScode快捷键测试

1. 用vscode打开一个文档，你就可以使用你设置的快捷键实现下面这些步骤了

> 1. cmd+l 选中当前整行；而后连续两次 cmd+c 将选中文字发给 DeepL
> 2. 光标移动到当前行末尾，回车两次（和之前的当前行空一行）
> 3. 模拟鼠标点击，按 DeepL 上的 “Insert to...” 链接，把翻译结果粘贴在在当前光标所在位置

注意：第2-3步需要几秒钟来实现，在等待过程中，不要移动光标，否则粘贴位置会出错。这个时候，我就先看看原文的内容。因为英文阅读速度比较慢，所以足够上面的workflow完成了。然后，我会重新再对照翻译结果逐句看一遍，如果发现有明显的错误，我就顺手改一下。

### 设置中文排版快捷键ctrl+alt+cmd+o

1. 安装ssmacro插件

- 打开VSCode，点击下图图标或者用cmd+shift+x打开extension页面

- 输入ssmacro，点击install，完成安装

<img src="https://s2.loli.net/2021/12/12/yMhKfG4SbnmLYc6.png" alt="image-20211212223942361" style="zoom:33%;" />



2. 在VSCode里，用 “快速启动快捷键” `cmd + shift + p`，打开`keybindings.json`

![image-20211212223354988](https://s2.loli.net/2021/12/12/1fYElvPkwmReXo6.png)

3. 快捷键设置

   把下面的内容加入[ ]之间，和其他用{}包含的内容用`,`隔开

```
{
	"key": "ctrl+alt+cmd+o",
	"command": "ssmacro.macro",
	"args": {"file": "regex.json"},
	"when": "editorTextFocus"
}
```

上面的内容其实是设置了一个快捷键——可以调用regex.json来处理当前选中的文档内容

>  其中的 `“args”: {“file”:“regex.json”},` 有两种写法，一个是像这样用 `“file”:` 指定，那么这个文件应该在 `$HOME/.vscode/extensions/joekon.ssmacro-0.6.0/macros/` 文件夹内；第二种写法使用 `“path”:`，然后在其后设定批处理文件（`json`文件）的绝对路径。

我用的是第一种方法。

4. 配置regex.json

- 下载regex.json，打开浏览器，输入以下链接，点击下载（理论上会下载到Download文件夹） `https://s7wabnxfe5.feishu.cn/file/boxcnQ68ltBhpZ1V99nkS4jALrg`

- 打开terminal（最便捷的方式使用Spotlight，或者在VScode里用快捷键 ctrl +` 打开）

- 复制并粘贴以下命令到terminal，回车

  ```cp $HOME/Downloads/regex.json $HOME/.vscode/extensions/joekon.ssmacro-0.6.0/macros/```

  >  cp表示复制，然后是你下载好的regex.json的路径（如果你的系统语言是中文，你可能要把上面的Download改为`下载`），以及目标路径（你希望存放regex.json的目录）

  看terminal是否报错，没报错的话就没问题了。

- 当然你可以复制并粘贴以下命令到terminal，回车，再检查一下：出现红框里的内容表示没问题

  ```ls $HOME/.vscode/extensions/joekon.ssmacro-0.6.0/macros/regex.json ``

  ![image-20211212232557641](https://s2.loli.net/2021/12/12/EyWSbLJmpeOrF46.png)

6. 把光标移动到翻译结果（在中文）所在行，使用ctrl+alt+cmd+o看效果：找有引号、破折号的内容尝试一下，就可以看到快捷键设置是否生效。

## 后记

走通这个流程后，我今天在走步机上走了大概4个小时。把《World After Captial》的第一部分看完了。这个速度还是听惊人的，更为关键的是，我再也没有那种发怵感了。因为deepL的翻译质量的确很不多，这么读原版书难度降低了许多。

这本书总共5部分，我还没看后面的内容。如果每个部分文字数量差不多，按照这样的速度，我说不定能在年前把这本书看完。

最后，我还把我的proof of work上传到了我在的github上fork的仓库里： `https://github.com/pierrelzw/WorldAfterCapital/tree/pow`。

本文只涵盖了原文工作流的一小部分，更多进阶、提高效率可以参考 https://github.com/xiaolai/apple-computer-literacy/tree/main/deepl-aided-semi-automatic-book-translation。比如还有

- 用代码实现deepl API调用，直接翻译整本书；并用代码直接优化格式，然后用浏览器实时渲染查看

仔细研究，你会发现这个仓库[xiaolai](https://github.com/xiaolai)/**[apple-computer-literacy](https://github.com/xiaolai/apple-computer-literacy)**简直到处是宝藏：

- 如何把markdown也在Edge浏览器里渲染出来，然后用它的自动朗读功能朗读文章

  >  这相当于给自己又增加了一个输入维度，全方位地调动自己的感官去读书

- terminal优化

- alfred使用，带我打开了新世界

- ……

我用了7年多的macbook，最近刚买mac的同事问我：有什么mac的软件推荐，我当时脑子一片空白，好容易憋出来一个Typora。但是，我没觉得自己有什么问题，直到最近才意识到：我也需要apple-computer-literacy（扫盲）。