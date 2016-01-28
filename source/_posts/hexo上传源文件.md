title: hexo上传源文件
date: 2016-01-28 17:57:15
tags: hexo
---
# 一、背景
之前用hexo和github搭建了一个免费博客。但本地的目录结构有些乱，想调整一下，趁此机会把hexo的源文件和所使用的主题上传到github上，以防需要换电脑时，原始数据丢失。

# 二、实现
上传hexo源文件同时也不和hexo编译后的文件冲突，可以使用一个仓库多个分支的思路。为我们的代码仓库创建两个分支，一个是 master， 一个hexo。master分支用来管理hexo编译之后的静态资源，而hexo分支用来管理hexo的源码，即一些markdown文件和主题等，需要设置这个分支为默认分支，以便拷贝仓库代码到本地时更方便。

1. 把代码仓库拷贝到本地  `git clone https://github.com/swxy/swxy.github.io.git`, 因为我之前本地已经用了，所以可以忽视这个;
2. 查看分支 `git branch`, 如果没有分支则新建一个`git remote add origin https://github.com/swxy/swxy.github.io.git`, 此时应该会有一个 **master** 分支了。
3. 新建一个 **hexo** 分支`git branch hexo` 。
4. 切换到 **hexo** 分支`git checkout hexo` 。
5. 添加源文件`git add *`, 因为 `.gitignore` 默认已经把 hexo 编译的文件忽略了，所以可以可以用 `git add *`。
6. 然后提交文件的变更 `git commit -m "xxx"`。
7. push变更到远程仓库中`git push origin hexo` (用hexo分支来管理源码）。

# 三、附录
### 常见问题收集：
1. `hexo g`编译时，出现错误：
```
{ [Error: Cannot find module './build/Release/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
{ [Error: Cannot find module './build/default/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
{ [Error: Cannot find module './build/Debug/DTraceProviderBindings'] code: 'MODULE_NOT_FOUND' }
```

解决方案，安装hexo加上 `--no-optional`;
>npm install hexo --no-optional

解决方案来源[hexo安装问题解决方法](http://www.jianshu.com/p/98e993305653)