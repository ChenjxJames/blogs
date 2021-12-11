---
title: 搭建使用 Typescript，Scss 的 React 项目
date: 2019-11-26
categories:
- Code
- frontend
tags:
- React
---

1、创建使用Typescript的项目

```bash
npx create-react-app my-app --typescript
```

2、安装sass依赖

<!-- more -->

这里注意sass-loader的版本是7.3.1，使用最新版本可能会出现不兼容现象。截止至发稿，最新的8.0.0在node.js12下报错。

```bash
npm install node-sass --save-dev
npm install sass-loader@7.3.1 --save-dev
npm install style-loader --save-dev
```

3、修改webpack配置

添加以下配置

```javascript
{
  test: /\.scss$/,
  loaders: ['style-loader', 'css-loader', 'sass-loader'],
},
```
至node_modules/react-scripts-ts/config/webpack.config.dev.js 里的如下位置

```javascript
{
  test: /\.scss$/,
  loaders: ['style-loader', 'css-loader', 'sass-loader'],
},
// "file" loader makes sure those assets get served by WebpackDevServer.
// When you `import` an asset, you get its (virtual) filename.
// In production, they would get copied to the `build` folder.
// This loader doesn't use a "test" so it will catch all modules
// that fall through the other loaders.
{
  // Exclude `js` files to keep "css" loader working as it injects
  // its runtime that would otherwise processed through "file" loader.
  // Also exclude `html` and `json` extensions so they get processed
  // by webpacks internal loaders.
  exclude: [/\.(js|jsx|mjs)$/, /\.html$/, /\.json$/],
  loader: require.resolve('file-loader'),
  options: {
    name: 'static/media/[name].[hash:8].[ext]',
  },
}
```

4、运行项目

```bash
npm run start
```
