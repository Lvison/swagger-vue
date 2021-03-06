<!--
 * @Description: readme
 * @Author: lvison
 * @Date: 2019-08-14 11:31:58
 * @LastEditTime: 2020-12-10 11:15:48
 * @LastEditors: lvison
 -->

# swagger-vue
Swagger to JS &amp; Vue &amp; Axios Codegen

# Installation
```shell
npm install swagger-vue --dev
```
# Generate
## Using NodeJS file
```javascript
const swaggerGen = require('swagger-vue')
const jsonData = require('../api-docs.json')
const fs = require('fs')
const path = require('path')

let opt = {
  swagger: jsonData,
  moduleName: 'api',
  className: 'api'
}
const codeResult = swaggerGen.getApi(opt)
fs.writeFileSync(path.join(__dirname, '../config/api.js'), codeResult)//生成API文档

const filterContent = swaggerGen.getFilter(opt)
fs.writeFileSync(path.join(__dirname, '../config/filter.js'), filterContent)//生成filter文档及枚举
```
### if api-docs.json from API
```javascript
const swaggerGen = require('swagger-vue')
const fs = require('fs')
const path = require('path')
const url = 'http://demo.api.com';

swaggerGen.apiRequest(url).then(jsondata=>{
  let opt = {
    swagger: jsonData,
    moduleName: 'api',
    className: 'api'
  }

  // const codeResult = swaggerGen.getApi(opt, [‘xxx’,'xxx']) //1.0.2支持传入生成需要的function,xxx-operationID
  const codeResult = swaggerGen.getApi(opt)
  fs.writeFileSync(path.join(__dirname, '../config/api.js'), codeResult)//生成API文档

  const filterContent = swaggerGen.getFilter(opt)
  fs.writeFileSync(path.join(__dirname, '../config/filter.js'), filterContent)//生成filter文档及枚举
})
``` 

## Using Grunt task

Create **Gruntfile.js**
```javascript
const fs = require('fs')
const path = require('path')
const swaggerGen = require('swagger-vue')

module.exports = function(grunt) {

  grunt.initConfig({
    'pkg': grunt.file.readJSON('package.json'),
    'swagger-vue-codegen': {
      options: {
        swagger: "<%= pkg.swagger.definition %>",
        className: "<%= pkg.swagger.className %>",
        moduleName: "vue-<%= pkg.swagger.moduleName %>",
        dest: 'client/javascript'
      },
      dist: {

      }
    }
  });

  grunt.registerMultiTask('swagger-vue-codegen', function() {
    var callback = this.async();
    var opt = this.options();
    var distFileName = path.join(opt.dest, opt.moduleName + '.js');

    fs.readFile(opt.swagger, function(err, data) {
      if (err) {
        grunt.log.error(err.toString());
        callback(false);
      } else {
        var parsedData = JSON.parse(data);

        var codeResult = swaggerGen({
          swagger: parsedData,
          moduleName: opt.moduleName,
          className: opt.className
        });

        fs.writeFile(distFileName, codeResult, function(err) {
          if (err) {
            grunt.log.error(err.toString());
            callback(false);
          } else {
            grunt.log.writeln('Generated ' + distFileName + ' from ' + opt.swagger);
          }
        })
      }
    });
  });

  grunt.registerTask('vue', ['swagger-vue-codegen']);

};

```
And set options in package.json
```json
...
  "swagger": {
    "definition": "client/swagger.json",
    "className": "API",
    "moduleName": "api-client"
  }
...
```
Now you can use `grunt vue` to run build task

# Generated client usage

In Vue.js main file set API domain
```javascript
import { setDomain } from './lib/api-client.js'
setDomain('http://localhost:3000/api')
```

Import API function into Vue.js component, for example to log in
```javascript
import { user_login as userLogin } from '../lib/api-client.js'

userLogin({
  credentials: {
    username: 'admin',
    password: 'admin'
  }
}).then(function (response) {
  console.log(response.data) // {id: "<token>", ttl: 1209600, created: "2017-01-01T00:00:00.000Z", userId: 1}
})
```
All requests use **axios** module with promise, for more information about that follow axios documentation 

# Links
 - [swagger-js-codegen](https://github.com/wcandillon/swagger-js-codegen)
 - [axinos](https://www.npmjs.com/package/axios)

# License

[MIT](https://opensource.org/licenses/MIT)


# changelog
# [1.0.0](http://) (2019-08-13)
* swgger2.0生成代码注释错乱优化
* 优化example
* 优化模版
* 修改filter bug
* 暴露Apirequet函数,如果API文件是以接口形式提供的，我们可以调用该函数请求接口，将文档转换为JSON数据
* 修改日志路径
* 新增：抓取swgger枚举变量，生成filter(支持swgger3.0)
* 删除生成api时生成的无用函数
* 暴露生成api和filter的接口,引用方式更改
---


# [1.0.1](http://) (2019-03-19)
### Features
* 取消parameters参数camelCase转换 by lvison
* 兼容openApi by lvison
---

# [1.0.4](http://) (2020-11-11)
### Features
* getApi支持传入过滤function列表，只生成所需要的funciton代码, function值为swagger描述中，每个接口的operationID
* 新增部分代码注释
---

# [2.0.0](http://) (2020-11-24)
### Features
* 新增对openAPI版本requestBody中的入参做详细解析，并在模版方法中新增对应的参数校验（request body默认为object） - 暂时不支持swagger2.0
* 新增部分代码注释
---
# [2.0.3](http://) (2020-12-10)
### Features
* 修复复delete 方法无法接收自定义参数，修复post 、put、patch等方式传参错误bug
* 新增部分代码注释
---