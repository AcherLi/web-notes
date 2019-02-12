不知道大家每次新建组件的时候，是不是都要创建一个目录，然后新增一个.vue文件，然后写template、script、style这些东西，如果是公共组件，是不是还要新建一个index.js用来导出vue组件、虽然有vscode有代码片段能实现自动补全，但还是很麻烦，今天灵活运用scripts工作流，自动生成vue文件和目录。
## 实践步骤
* 安装一下chalk，这个插件能让我们的控制台输出语句有各种颜色区分
    npm install chalk --save-dev 
    yarn add chalk --save-dev
* 在根目录中创建一个 scripts 文件夹
* 新增一个generateComponent.js文件，放置生成组件的代码
* 新增一个template.js文件，放置组件模板的代码

template.js文件，里面的内容可以自己自定义，符合当前项目的模板即可
```javascript
// template.js
module.exports = {
  vueTemplate: compoenntName => {
    return `<template>
  <div class="${compoenntName}">
    ${compoenntName}组件
  </div>
</template>

<script>
export default {
  name: '${compoenntName}'
}
</script>

<style scoped lang="stylus" rel="stylesheet/stylus">
.${compoenntName} {

}
</style>

`
  },
  entryTemplate: `import Main from './main.vue'
export default Main`
}

```
generateComponent.js生成vue目录和文件的代码
```javascript
// generateComponent.js`
const chalk = require('chalk') // 控制台打印彩色
const path = require('path')
const fs = require('fs')
const resolve = (...file) => path.resolve(__dirname, ...file)
const log = message => console.log(chalk.green(`${message}`))
const successLog = message => console.log(chalk.blue(`${message}`))
const errorLog = error => console.log(chalk.red(`${error}`))
const { vueTemplate, entryTemplate } = require('./template')
const _ = process.argv.splice(2)[0] === '-com'

const generateFile = (path, data) => {
  if (fs.existsSync(path)) {
    errorLog(`${path}文件已存在`)
    return
  }
  return new Promise((resolve, reject) => {
    fs.writeFile(path, data, 'utf8', err => {
      if (err) {
        errorLog(err.message)
        reject(err)
      } else {
        resolve(true)
      }
    })
  })
}

// 公共组件目录src/base,全局注册组件目录src/base/global,页面组件目录src/components
_ ? log('请输入要生成的组件名称、如需生成全局组件，请加 global/ 前缀') : log('请输入要生成的页面组件名称、会生成在 components/目录下')
let componentName = ''
process.stdin.on('data', async chunk => {
  const inputName = String(chunk).trim().toString()

  // 根据不同类型组件分别处理
  if (_) {
    // 组件目录路径
    const componentDirectory = resolve('../src/base', inputName)
    // vue组件路径
    const componentVueName = resolve(componentDirectory, 'main.vue')
    // 入口文件路径
    const entryComponentName = resolve(componentDirectory, 'index.js')

    const hasComponentDirectory = fs.existsSync(componentDirectory)
    if (hasComponentDirectory) {
      errorLog(`${inputName}组件目录已存在，请重新输入`)
      return
    } else {
      log(`正在生成 component 目录 ${componentDirectory}`)
      await dotExistDirectoryCreate(componentDirectory)
    }

    try {
      if (inputName.includes('/')) {
        const inputArr = inputName.split('/')
        componentName = inputArr[inputArr.length - 1]
      } else {
        componentName = inputName
      }
      log(`正在生成 vue 文件 ${componentVueName}`)
      await generateFile(componentVueName, vueTemplate(componentName))
      log(`正在生成 entry 文件 ${entryComponentName}`)
      await generateFile(entryComponentName, entryTemplate)
      successLog('生成成功')
    } catch (e) {
      errorLog(e.message)
    }
  } else {
    const inputArr = inputName.split('/')
    const directory = inputArr[0]
    let componentName = inputArr[inputArr.length - 1]

    // 页面组件目录
    const componentDirectory = resolve('../src/components', directory)

    // vue组件
    const componentVueName = resolve(componentDirectory, `${componentName}.vue`)

    const hasComponentDirectory = fs.existsSync(componentDirectory)
    if (hasComponentDirectory) {
      log(`${componentDirectory}组件目录已存在,直接生成vue文件`)
    } else {
      log(`正在生成 component 目录 ${componentDirectory}`)
      await dotExistDirectoryCreate(componentDirectory)
    }

    try {
      log(`正在生成 vue 文件 ${componentName}`)
      await generateFile(componentVueName, vueTemplate(componentName))
      successLog('生成成功')
    } catch (e) {
      errorLog(e.message)
    }
  }

  process.stdin.emit('end')
})

process.stdin.on('end', () => {
  log('exit')
  process.exit()
})

function dotExistDirectoryCreate (directory) {
  return new Promise((resolve) => {
    mkdirs(directory, function () {
      resolve(true)
    })
  })
}

// 递归创建目录
function mkdirs (directory, callback) {
  var exists = fs.existsSync(directory)
  if (exists) {
    callback()
  } else {
    mkdirs(path.dirname(directory), function () {
      fs.mkdirSync(directory)
      callback()
    })
  }
}
```
* 配置package.json，scripts新增两行命令，其中-com是为了区别是创建页面组件还是公共组件
```
"scripts": {
    "new:view":"node scripts/generateComponent",
    "new:com": "node scripts/generateComponent -com"
  },
```
* 执行
```
    npm run new:view // 生成页组件
    npm run new:com // 生成基础组件
    或者
    yarn run new:view // 生成页组件
    yarn run new:com // 生成基础组件
```
