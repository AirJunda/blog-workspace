---
title: "Mac上快速打开项目文件的设置"
date: 2022-04-21T21:10:13+08:00
draft: false
tags: ["Terminal","Tech","Mac","Setup", "Vscode"，“Pycharm”]
topics: ["TechBlog"]
---

## 背景
本文主要是补充一些macbook上的额外设置。这些设置实现的功能在Windows是自带的功能。但是在Mac上却需要额外的设置才能实现。


### Vscode and Pycharm
* [Automatically Open the Current Directory in VS Code From Terminal](https://shannoncrabill.com/blog/shell-command-open-directory-in-vscode/#:~:text=If%20you%20already%20have%20a,automatically%20open%20in%20VS%20code.)

* [Launch Pycharm from CLI](https://www.jetbrains.com/help/pycharm/working-with-the-ide-features-from-command-line.html)
这个教程里我是用到Jetbrain Toolbox去实现的。



### Finder中显示路径
打开任意文件夹, 然后点击左上角 View > Show Path Bar, 就可以在finder底部显示路径。如果要copy filepath,右击file 同时长按opt.


### 右键文件夹在VS Code/Pycharm中打开
1. Open Automator
2. File > New
3. Select Quick Action
4. Click Choose
5. Select "Workflow receives current files or folders in Finder"
6. From the left hand side pane in Automator, drag-drop Library > Utilities > Run Shell Script into the right hand pane
7. Ensure Pass input is set to "as arguments"
8. Then paste the below text:
``` python
for f in "$@"; do
  open -a 'Visual Studio Code' "$@"
done
```
9. Click File > Save


如果是想open folder as a pycharm project，请用下面这段script. 这段scirpt是Jetbrain Toolbox在上一节的教程“Launch Pycharm from CLI”中生成的。如果在上一节的教程中不自定义脚本名字，脚本名字叫pycharm, 存放于/usr/local/bin 内容就是下面的内容。但是这个脚本如果换了新机不能直接copy拿去用。需要用Jetbrain Toolbox生成。
``` python
declare -a ideargs=()
declare -- wait=""

for o in "$@"; do
  if [[ "$o" = "--wait" || "$o" = "-w" ]]; then
    wait="-W"
    o="--wait"
  fi
  if [[ "$o" =~ " " ]]; then
    ideargs+=("\"$o\"")
  else
    ideargs+=("$o")
  fi
done

open -na "/Users/lij6/Library/Application Support/JetBrains/Toolbox/apps/PyCharm-P/ch-0/213.7172.26/PyCharm.app/Contents/MacOS/pycharm" $wait --args "${ideargs[@]}"
```