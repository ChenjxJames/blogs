---
title: CSS 选择器优先级
date: 2019-10-25
categories:
- Code
- frontend
tags:
- css
---

#### 下表由上到下 从左到右 优先级依次降低

|   |   |   |
| - | - | - |
| `!important` | | |
| ID 选择器 (例如，`#example`) | | |
| 伪类 (例如，`:hover`) | 属性选择器 (例如，`div[type=”radio”]`)  | 类选择器 (例如，`.example`) |
| 伪元素 (例如，`::before`) | 标签选择器 (例如，`h1`) |

|   |
| - |
| 标签内style属性（例如：`<div style=”color: red;”></div>`）|
| 页面内`<style></style>`中定义的样式 |
| 引用的外部文件内的样式 |

<!-- more -->

#### 总结的优先级表如下，左上角优先级最高，右下角优先级最低

|   |   |   |
| - | - | - |
| 标签内style属性的`!important` | | |
| HTML页面`<style></style>`中的`!important` | | |
| 引用的外部文件内的样式中的`!important` | | |
| 标签内style属性中的样式 | | |
| 页面内`<style></style>`中定义的ID选择器 | | |
| 页面内`<style></style>`中定义的伪元素 | 标签选择器 | |
| 引用的外部文件内定义的ID | 选择器 | |
| 引用的外部文件内定义的伪类 | 属性选择器 | 类选择器 |
| 引用的外部文件内定义的伪元素 | 标签选择器 | |
