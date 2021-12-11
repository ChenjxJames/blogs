---
title: Typescript 项目使用 jest 进行单元测试
date: 2019-12-11
categories:
- Code
- nodejs
tags:
- jest
- test
- Typescript
---

## 安装依赖

```bash
npm install --save-dev typescript jest ts-jest @types/jest
```

## 创建jest配置文件

```bash
npx ts-jest config:init
```

<!-- more -->

## 配置jest，往jest.config.js中添加如下配置

```javascript
const { default: tsjPreset } = require('ts-jest/presets');
module.exports = {
  roots: ["<rootDir>/tests"],
  verbose: true,
  transform: {
    "^.+\\.tsx?$": "ts-jest"
  },
  globals: {
    "ts-jest": {
      diagnostics: false
    }
  }
}
```

## 在package.json中添加脚本

```json
{
  "scripts": {
    "test": "jest --collectCoverage"
  }
}
```

## 创建测试文件

```bash
mkdir tests
cd tests
touch index.test.ts
```

### src/index.ts //待测试文件

```typescript
export function sum(a: number, b: number) {
  return a + b;
}
```

### index.test.ts

```typescript
import { sum } = from '../src/index';
test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3);
});
```

## 配置tsconfig.json

```json
{
  "compilerOptions": {
    "target": "es5",                          /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019' or 'ESNEXT'. */
    "module": "commonjs",                     /* Specify module code generation: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', or 'ESNext'. */
    "declaration": true,                   /* Generates corresponding '.d.ts' file. */
    "outDir": "./dist/",                        /* Redirect output structure to the directory. */
    "rootDir": "./src/",                       /* Specify the root directory of input files. Use to control the output directory structure with --outDir. */
    "strict": true,                           /* Enable all strict type-checking options. */
    "esModuleInterop": true,                  /* Enables emit interoperability between CommonJS and ES Modules via creation of namespace objects for all imports. Implies 'allowSyntheticDefaultImports'. */
    "forceConsistentCasingInFileNames": true  /* Disallow inconsistently-cased references to the same file. */
  },
  "exclude": ["node_modules", "tests/**/*.test.ts"]
}
```

## 目录结构

```
.
├── coverage
│   ├── clover.xml
│   ├── coverage-final.json
│   ├── lcov-report
│   │   ├── base.css
│   │   ├── block-navigation.js
│   │   ├── index.html
│   │   ├── index.ts.html
│   │   ├── prettify.css
│   │   ├── prettify.js
│   │   ├── sort-arrow-sprite.png
│   │   └── sorter.js
│   └── lcov.info
├── dist
│   ├── index.d.ts
│   └── index.js
├── node_modules
├── jest.config.js
├── package-lock.json
├── package.json
├── src
│   └── index.ts
├── tests
│   └── index.test.ts
└── tsconfig.json
```

## 运行测试

```bash
npm run test
```

### 运行测试后，console会显示测试结果

### 同时会生成coverage目录，该目录下存有测试结果

> [jest官网](https://jestjs.io/zh-Hans/)
