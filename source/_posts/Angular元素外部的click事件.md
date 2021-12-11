---
title: Angular 元素外部的 click 事件
date: 2019-9-20
categories:
- Code
- frontend
tags:
- Angular
- frontend
toc: false
---

### 我们在编写下拉菜单一类组件的时候常常需要点击下拉菜单以外的地方时关闭下拉菜单的效果。我们一般使用 [ng-click-outside](https://www.npmjs.com/package/ng-click-outside) 插件，一般情况下元素外部的click事件这个插件都能解决

![这个下拉菜单的关闭就用到了clickOutside事件](/images/01.gif)

<!-- more -->

* 插件安装

```bash
npm install --save ng-click-outside
```

* 插件使用

```html
<div (clickOutside)="onClickedOutside($event)">My element</div>
```

#### 一般情况下的click事件这个插件都可以解决，但是当元素有点击后消失的后代（比如单选的下拉菜单）时这个插件就会出现问题

![单选下拉菜单选择后关闭](/images/02.gif)

#### 下面我们来分析一下ng-click-outside的代码

```typescript
private _onClickBody(ev: Event) {
  if (!this.clickOutsideEnabled) { return; }
  if (this.excludeBeforeClick) {
    this._excludeCheck();
  }
  if (!this._el.nativeElement.contains(ev.target) && !this._shouldExclude(ev.target)) {
    this._emit(ev);
    if (this.attachOutsideOnClick) {
      this._removeClickOutsideListener();
    }
  }
}
private _shouldExclude(target): boolean {
  for (let excludedNode of this._nodesExcluded) {
    if (excludedNode.contains(target)) {
      return true;
    }
  }
  return false;
}
```

我们看一下上面那段ng-click-outside的源码，_onClickBody方法在click事件点击后调用，使用contains方法判断event所在元素是否在本元素或排除元素中，如果元素不满足该条件则触发（clickOutside）事件。

这会产生一个问题，如果你调用（clickOutside）事件的元素中包含如下拉菜单等会因为自身点击而消失于DOM的元素 会导致（clickOutside）事件的意外触发。
这是因为ng-click-outside是使用event.target来做判断的，target是触发事件的对象 (某个DOM元素) 的引用，但是这时该DOM元素已经不在DOM中了，所以导致事件错误触发。

编写自定义指令实现clickOutside事件
使用event.path能很好的避免该情况，path包含了事件冒泡过程的所有元素（chrome中使用path属性，Firefox和Safari中使用composedPath方法），只要path中含有本元素的后代或排除元素的后代即表面clickInside。这样就避免了clickOutside的错误触发。

下面我们来写一个自定义指令，使用event.path中的数据来判断产生的click元素是否属于指定元素内。

```typescript
import { Directive, Output, EventEmitter, ElementRef, HostListener, Input } from '@angular/core';
@Directive({
  selector: '[appClickOutside]'
})
export class ClickOutsideDirective {
  @Output() appClickOutside = new EventEmitter<void>();
  constructor(private elementRef: ElementRef) { }
  @HostListener('document:click', ['$event'])
  public onClick(event) {
    const clickedInside = event.path.slice(0, -2).some((item: any) => {
      return this.elementRef.nativeElement.contains(item);
    });
    if (!clickedInside) {
      this.appClickOutside.emit();
    }
  }
}
```

有时候还会遇到一些组件使用全局布局的情况，这时候就要添加一个输入属性记录要额外排除的元素。这里我引入了exclude属性，通过exclude输入一个css选择器记录排除的元素，但如果记录的元素本身在点击后会消失于DOM则无效（即对于消失的元素，选择器仅检索其后代）

```typescript
import { Directive, Output, EventEmitter, ElementRef, HostListener, Input } from '@angular/core';
@Directive({
  selector: '[appClickOutside]'
})
export class ClickOutsideDirective {
  @Input() exclude = '';
  @Output() appClickOutside = new EventEmitter<void>();
  constructor(private elementRef: ElementRef) { }
  @HostListener('document:click', ['$event'])
  public onClick(event) {
    let clickedInExclude = false;
    if (this.exclude) {
      try {
        if (document.querySelectorAll(this.exclude).length) {
          clickedInExclude = Array.from(document.querySelectorAll(this.exclude)).some((item: any) => {
            return item.contains(event.target);
          });
        } else {
          clickedInExclude = event.path.slice(0, -2).some((item: any) => {
            return item.querySelector(this.exclude);
          });
        }
      } catch (err) {
        console.error('[click-outside] Check your exclude selector syntax.', err);
      }
    }
    const clickedInside = event.path.slice(0, -2).some((item: any) => {
      return this.elementRef.nativeElement.contains(item);
    });
    console.log(event.path);
    console.log(event.target);
    if (!clickedInside && !clickedInExclude) {
      this.appClickOutside.emit();
    }
  }
}
```
