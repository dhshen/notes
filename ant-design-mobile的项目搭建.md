# ant-design-mobile搭建

### 说明

* 基于create-react-app创建应用
* 引入react-router
* 引入less支持



## 开始

1.安装create-react-app

```
sudo npm install -g create-react-app
```

2.创建项目

```
create-react-app merchant
```

等待项目创建和包安装完成。

安装完成后，会提示：

```bash
Success! Created merchant at /Users/admin/WebstormProjects/merchant
Inside that directory, you can run several commands:

  npm start
    Starts the development server.

  npm run build
    Bundles the app into static files for production.

  npm test
    Starts the test runner.

  npm run eject
    Removes this tool and copies build dependencies, configuration files
    and scripts into the app directory. If you do this, you can’t go back!

We suggest that you begin by typing:

  cd merchant
  npm start

Happy hacking!
```

进入项目执行：`npm start`，等项目跑起来后就可以在`http://localhost:3000/`看到react的demo了。

接下来的步骤，参考ant.desin的 [在 create-react-app 中使用](https://ant.design/docs/react/use-with-create-react-app-cn)

## 定制

> 我们现在已经把组件成功运行起来了，但是在实际开发过程中还有很多问题，例如上面的例子实际上加载了全部的 antd 组件的代码（对前端性能是个隐患）。

我们需要对 create-react-app 的默认配置进行自定义。可以使用 `eject` 命令将所有内建的配置暴露出来。

```
npm run eject
```

执行上面的命令后会发现多了`config`和`scripts`两个文件夹，里面放的是webpack和启动或编译的js文件。

#### 安装ant-design-mobile

```
npm install antd-mobile --save
```

#### 增加按需加载

> [babel-plugin-import](https://github.com/ant-design/babel-plugin-import) 是一个用于按需加载组件代码和样式的 babel 插件

```
npm install babel-plugin-import --save-dev
```

修改`config/webpack.config.dev.js`文件以及`config/webpack.config.prod.js`文件

```bash
// Process JS with Babel.
{
  test: /\.(js|jsx)$/,
  include: paths.appSrc,
  loader: 'babel',
  query: {
+   plugins: [
+     ['import', [{ libraryName: "antd", style: 'css' }]],
+   ],
    // This is a feature of `babel-loader` for webpack (not Babel itself).
    // It enables caching results in ./node_modules/.cache/babel-loader/
    // directory for faster rebuilds.
    cacheDirectory: true
  }
},
添加+标记的行
```

#### 添加less支持

```
npm install less less-loader --save-dev
```

```
loaders: [
  {
    exclude: [
      /\.html$/,
      /\.(js|jsx)$/,
+     /\.less$/,
      /\.css$/,
      /\.json$/,
      /\.svg$/
    ],
    loader: 'url',
  },

...

  // Process JS with Babel.
  {
    test: /\.(js|jsx)$/,
    include: paths.appSrc,
    loader: 'babel',
    query: {
      plugins: [
-       ['import', [{ libraryName: "antd", style: 'css' }]],
+       ['import', [{ libraryName: "antd", style: true }]],  // 加载 less 文件
      ],
   },

...

+ // 解析 less 文件，并加入变量覆盖配置
+ {
+   test: /\.less$/,
+   loader: 'style!css!postcss!less?{modifyVars:{"@primary-color":"#1DA57A"}}'
+ },
]
```

#### 添加路由

```
npm install react-router --save
```

#### 常见的坑

1. react-router版本的问题

   ```bash
   Uncaught TypeError: Cannot read property 'location' of undefined
   ```

   这是因为`react-router`版本的原因引起的。详见：[https://github.com/acdlite/redux-router/issues/281](https://github.com/acdlite/redux-router/issues/281)

   将`react-router`的版本替换到`v3.0.0`就可以了。

2. ```
   Note: must use https://github.com/ant-design/babel-plugin-import
   For more information, please see https://github.com/ant-design/ant-design-mobile/issues/602
   ```

   详见官网的[说明](https://github.com/ant-design/ant-design-mobile/issues/602)，官方说是因为 antd-mobile 同时用于 web 和 native 两种使用场景，以不同的后缀 *.web.js 和 *.js 作为区分，**所以项目没有设置统一的入口文件**，需要直接引用模块文件。

   但是按照该页面的解决方案似乎解决不了遇到的问题，因为我们已经按照要求安装了`babel-plugin-import`，之前有同事在解决这个问题的时候是把babel配置提出来了，而且安装了`babel-preset-es2015`和`babel-preset-react`，`.babelrc`中的配置为：

   ```
   { 
     "presets": [ 
       "es2015", 
       "react" 
     ], 
     "plugins": [ 
       [ 
         "import", 
           { 
             "libraryName": "antd-mobile", 
             "style": "css" 
           } 
         ] 
       ] 
   }
   ```

   上述报错确实不再出现了。

   后来我在尝试解决这个问题的时候尝试了不安装`babel-preset-es2015`和`babel-preset-react`，而是只使用create-react-app默认自带的`babel-preset-react-app`   将 `.babelrc`中的presets设置成`'react-app'`，发现也可以。最终的`.babelrc`内容为：

   ```
   {
       "presets": [
       "react-app"
   ],
       "plugins": [
       [
           "import",
           {
               "libraryName": "antd-mobile",
               "style": "css"
           }
       ]
   ]
   }
   ```

   `config/webpack.config.dev.js`文件以及`config/webpack.config.prod.js`文件注释之前添加的plugins:

   ```javascript
   // Process JS with Babel.
         {
           test: /\.(js|jsx)$/,
           include: paths.appSrc,
           loader: 'babel',
           query: {
               /*plugins:[
                   ['import', [{ libraryName: "antd", style: true }]]  // 加载 less 文件
               ],*/
             cacheDirectory: true
           }
         },
   ```

   resolve的extentions字段要写成：

   ```javascript
   extensions: ['','.web.js','.js', '.json', '.jsx'],
   ```

   因为antd-mobile的web模块的js是`.web.js`结尾的，`.js`结尾的是native模块，因此`.web.js`要写在`.js`之前，否则会出现错误：`Uncaught Error: Cannot find module "react-native"`

   ​

   ​



































