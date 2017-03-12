title: async-validator使用
date: 2017-03-02 20:35:35
tags: React
---

使用`antd`的`Form`表单组件时，然后结合文档梳理一下规则使用的几种方式。

<!-- more -->

# 基本用法
```
const Validator = require('async-validator');
const descriptor = {
  name: {type: "string", required: true}
}
const validator = new Validator(descriptor);

validator.validate({name: "demo"}, (errors, fields) => {
  if(errors) {
    console.error(errors);
  }
  // success
});
```
基本流程就是，创建一个验证器，参数是需要验证的字段以及其对应的验证规则，然后调用`validate`开始验证。

# 规则形式
`async-validator`中定义的校验规则格式有些会有简写，但通过一系列内部判断转化之后，其规则格式基本可以统一为：
```
{
    name: [{type: 'string', validator: function() {}, field: 'name'}]
}
```
必需字段包含`type`, `validator`两个字段。如果没有`validator`函数时，验证时会自动跳过。

## 1. 规则是方法

```
const descriptor = {
    number: function custom() {} // 提供只一个validate方法
    // 也可以是
    // number: [function custom() {}]
    // 或者
    // number: {validator: function custom() {}}
}
const validator = new Validator(descriptor);

....

```

这个规则在验证是会被转化为：
```
{
    number: [{type: 'string', validator: function custom() {}, field: 'number'}]
}
```

如果规则是方法时，在回调`async-validator`会注入5个参数 `rule, value, callback, source, options`:

- `rule`: 当前验证使用的规则
- `value`: 字段的值
- `callback`: 验证完成时的回调函数，参数是`null 或者 [] 或者 [new Error('not valid')]`
- `source`: 验证时的所有数据
- `options`: 额外的验证选项，可以包含`message`来覆盖默认的错误信息

## 2. 规则是一个对象
```
const descriptor = {
  user: {required: true}  // user 字段必填
}
const validator = new Validator(descriptor);

....

```

在验证时会被转化成：
```
{
    // validators.required 是内部已定义好的检验函数
    user: [{type: 'string', required: true, validator: validators.required, field: 'user'}]
}
```

## 3. 规则是一个数组
```
const descriptor = {
  email: [{required: true}, {type: 'email'}]  // user 字段必填
}
const validator = new Validator(descriptor);

....

```

## 4. 规则包含嵌套
```
const descriptor = {
    address: {
      type: 'object',
      required: true,
      fields: {
        province: {
          type: 'string',
          required: true,
          min: 4,
        },
        city: {
          type: 'string',
          message: 'custom message',
          min: 5,
        }
      }
    }
}
const validator = new Validator(descriptor);
```

# 扩展阅读
[async-validator](https://github.com/yiminghe/async-validator)
