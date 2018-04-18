---
title: git部分学习笔记
date: 2018-04-18 13:15:11
tags:
---

## Git随手记
### 一.`git add .`、`git add -u`、`git add -A`的区别 
- `git add .`：他会监控工作区的状态树，使用它会把工作时的所有变化提交到暂存区，包括**文件内容修改**(modified)以及**新文件**(new)，但不包括被删除的文件。----*git 1.x version*
- `git add -u`：他仅监控*已经被add的文件*（即tracked file），他会将被**修改的文件**和**删除的文件**提交到暂存区。add -u 不会提交新文件（untracked file）。（git add --update的缩写）
- `git add -A`：是上面两个功能的合集（git add --all的缩写）
- 在*git 2.x version*后`git add .` = `git add -A`