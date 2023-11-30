---
title: 浅谈图片加载优化
date: 2023-11-30 19:14:06
categories:
- Code
- frontend
tags:
- React
- Typescript
---

## 图片压缩

遇到图片加载慢第一想到的就是图片压缩，可以使用在线的 [TinyPng](https://tinypng.com/) 服务来压缩，也可以使用开源的 [sharp](https://www.npmjs.com/package/sharp) 库压缩。

## 懒加载

遇到大量图片的页面可以考虑使用懒加载优化网页加载速度，避免大量图片同时加载占用带宽。

### 实现一个懒加载组件

我们可以用 [IntersectionObserver](https://developer.mozilla.org/zh-CN/docs/Web/API/IntersectionObserver) 这个接口来判断图片是否可见从而来实现懒加载。

<!-- more -->

React 实现懒加载组件如下，Lazyload 组件需要设置一个高度, 避免高度为 0 而堆积在一块 （部分情况下会导致懒加载失效）。  


```javascript
import React, { useEffect, useRef, useState } from 'react';

interface IProps {
    children: any;
}

const Lazyload: React.FC<IProps> = (props: IProps) => {
    const [show, setShow] = useState(false);
    const ref = useRef(null);
    const observer = useRef<any>();
    const { height = '200px', children } = props;

    useEffect(() => {
        observer.current = new IntersectionObserver((entries) => {
            entries.forEach(item => {
            if(item.intersectionRatio > 0) {
                setShow(true);
                observer.current.unobserve(item.target);
            }
            });
        });
        observer.current.observe(ref.current);
        return () => {
            observer.current.disconnect()
        };
    }, []);

    return (
        <div ref={ref} style={{ height }}>
            {show ? children : null}
        </div>
    );
};

export default Lazyload;
```

`Lazyload` 组件使用如下，需要设置一个固定高度 height，初始化时 `Lazyload` 组件的子节点不可见，该组件对于用户可见时加载子节点。

```html
<Lazyload height="300px">
    <div>
        <div>图片</div>
        <img src="https://developer.mozilla.org/zh-CN/docs/Learn/Tools_and_testing/Client-side_JavaScript_frameworks/React_getting_started/default-create-react-app.png" alt="React page" />
    </div>
</Lazyload>
```

![懒加载效果](/images/lazyload.gif)

### 图片兜底优化

虽然懒加载优化了页面加载速度问题，但是遇到大图片时，用户下滑将看到图片会线性地一点点出来，用户体验不太好。
对此可以在图片加载完成前展示兜底图（loading图片），onload 事件后展示图片。

更好的方法是压缩一张模糊的图片作为兜底图，大图覆盖在小图上，这样图片就会由模糊逐渐变清晰，用户体验会比较好。

![懒加载兜底优化效果](/images/lazyload-thumb.gif)

## cdn

除了上述编程优化方案，还可以使用 cdn 储存图片加快图片的访问速度。

### cdn 加载失败兜底

我的个人站点使用的是一个免费图床 [imgse](https://imgse.com/) ，为了避免图床跑路导致页面显示异常需要进行兜底处理。

实现思路如下，图片默认使用 cdn 图片链接,在图片加载失败时通过onerror事件将图片链接替换为自有服务器图片链接，从而保证页面显示始终正常。

```javascript
<img src="cdn图片链接" onerror="this.src = '自有服务器图片链接'" />
```

注意：要添加 flag 防止 onerror 获取的图片也加载失败从而导致 onerror 被循环触发

React 完整实现如下

```javascript
const [isOnError, setIsOnError] = useState(false);
const imageRef = useRef<any>(null);
const { imageSrc, defaultImageSrc } = props;

const handleImageError = () => {
    if (!isOnError) {
        setIsOnError(true);
        imageRef.current.src = defaultImageSrc;
    }
}

return (<img src={imageSrc} ref={imageRef} onError={handleImageError} />);
```

## 预加载

### css

使用image标签加载需要预加载的图片将其缩小并移到用户看不到的地方实现预加载（注意：不能使用 display=none , 否则预加载会失效）

```html
<body>
  <img src="image url" style="height: 1px; width: 1px; position: fixed; top: -10px; left: -10px;">
</body>
```

### javascript

javascript 预加载图片只需要创建一个 Image 对象，并将其的 src 属性设置为需要预加载图片的 URL 即可  
相较 css 方式更加灵活多变，且 dom 上也不会产生多余的元素

```javascript
const img = new Image();
img.src = 'image url';
```

## 预加载与懒加载结合

页面图片懒加载，且通过预判用户下一步可能访问的图片进行预加载，来实现更好的使用体验
