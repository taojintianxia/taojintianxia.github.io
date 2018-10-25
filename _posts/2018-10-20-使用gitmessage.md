---
layout: post  
title: 使用gitmessage  
categories: [gitmessage, git]  
description:   
keywords: gitmessage, git   
---

#### 在开始前, 我们先看看一些糟糕的git log的样子 :
![](https://taojintianxia.github.io/images/posts/git/bad_git_commit_2x.png)  
#### 完全不知道这些log在描述什么, 还有如下的这种git commit log:  
- bug fixes  
- copy edit  
- Major refactor of all the stuffs  

#### 看起来也是让人云里雾里的, 接下来推荐一个看起来还不错的git message模板:
![](https://taojintianxia.github.io/images/posts/git/git_commit_with_message.png)  
#### 感觉通过模板, git log的描述看起来更清晰了. 下面就说说如何引入这个模板:  
##### 1. 来到github的相关页面 : [template](https://github.com/joelparkerhenderson/git_commit_template) , 下载git_commit_template.txt
##### 2. 将文件放在如下路径:  
```
 ~/.git_commit_template.txt
 ```
##### 3. 配置全局message :
```
git config --global commit.template ~/.git_commit_template.txt
```
##### 4. 在~/.gitconfig文件中添加tempalte:  
```
[commit]
  template = ~/.git_commit_template.txt
```

#### 每次在命令行提交的时候, 直接输入git commit就会使用模板.不用再使用git commit -m了.

#### 最后, 如下是一些官方建议的行为:
```
Add = Create a capability e.g. feature, test, dependency.
Drop = Delete a capability e.g. feature, test, dependency.
Fix = Fix an issue e.g. bug, typo, accident, misstatement.
Bump = Increase the version of something e.g. a dependency.
Make = Change the build process, or tools, or infrastructure.
Start = Begin doing something; e.g. enable a toggle, feature flag, etc.
Stop = End doing something; e.g. disable a toggle, feature flag, etc.
Refactor = A change that MUST be just refactoring.
Reformat = A change that MUST be just format, e.g. indent line, trim space, etc.
Rephrase = A change that MUST be just textual, e.g. edit a comment, doc, etc.
Optimize = A change that MUST be just about performance, e.g. speed up code.
Document = A change that MUST be only in the documentation, e.g. help files.
```