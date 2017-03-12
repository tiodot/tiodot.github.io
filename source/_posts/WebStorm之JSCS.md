title: WebStorm之JSCS
date: 2017-03-11 10:04:26
tags: JS
---
在开发代码时，通常需要配置一些额外的代码质量校验工具来保证代码质量，像我们这边使用的就是[fecs](http://fecs.baidu.com/)。因为平常都是用Webstorm来开发，正好`fecs`有提供这样的[插件](https://github.com/leeight/Baidu-FE-Code-Style#webstorm)，用起来却也很方便。

<!-- more -->
# 安装
1. 把代码下载或者`git clone`到本地某个目录
2. 然后全局安装`fecs`, `npm i -g fecs`
3. 然后修改jscs的配置, 目录指向下载的`Baidu-FE-Code-Style`即可
4. 然后可能还需要指定一个`jscs`的配置文件，可以在`Baidu-FE-Code-Style`下新建一个`.jscsrc`文件，内容随意一个json就行，`fecs`并不会用到这个配置，我配置的是：
```
{
  "preset": "airbnb"
}
```

# 原理
每次文件文件有变更时，会执行`Baidu-FE-Code-Style`工程目录下的`[bin/jscs](https://github.com/leeight/Baidu-FE-Code-Style/blob/master/bin/jscs)`, 最主要的代码:
```
spawn('fecs',
    ['--silent', '--reporter=baidu', '--format=checkstyle', file],
    {stdio: 'inherit'});
```
即执行全局的fecs命令，`--fotmat=checkstyle`是指输入符合webstorm规范的xml形式的检测结果，内容大致为：
```
<?xml version="1.0" encoding="utf-8"?>

<checkstyle version="4.3">
  <file name="/Users/xxx/project/util.js" count="1">
    <error line="107" column="24" severity="error" source="fecs" message="  JS021: [强制] 不得省略语句结束的分号。" rule="semi"/>
  </file>
</checkstyle>
```
webstorm会根据error标签中的信息展示相关的提示。

# 其他
由于webstorm只支持给统一设置检测错误提示的颜色，所以这里没有办法根据`severity`的不用取值,例如`error`, `warning`等展示不同的颜色。

# 扩展阅读
[leeight/Baidu-FE-Code-Style](https://github.com/leeight/Baidu-FE-Code-Style)
