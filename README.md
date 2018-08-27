# mobx-wxapp

在微信小程序中使用[mobx](https://github.com/mobxjs/mobx)，`mobx-wxapp`简单的提供了一个`inject`函数(替换原observer方式)，用于自动响应被观察数据的更新，用法如例所示。

#### 用法

`npm install mobx-wxapp`

案例使用了 mobx v4

一些store:

```JavaScript
//store.js
import { observable, decorate, computed, action } from "./utils/mobx";

const store = observable({
  //observable
  age: 0,
  //computed
  get say() {
    return `i am ${this.age}.`;
  },
  //action
  add() {
    this.age += 1;
  }
});
/* or */
class AnotherStore {
  age = 18;
  get agex2() {
    return this.age * 2;
  }
  reset() {
    this.age = 0;
  }
}

decorate(AnotherStore, {
  age: observable,
  agex2: computed,
  reset: action
});

export { store, AnotherStore };
```

page:

```JavaScript
//index.js
import { store, AnotherStore } from "../store";
import { inject } from "../utils/mobx-wxapp";

Page({
  onLoad(options) {
    inject(this, {
      store: store,
      another: new AnotherStore()
    });
    //...
  },
  //...
  tapAdd() {
    this.props.store.add();
    this.props.another.reset();
  }
});
```

wxml:

```xml
<view>age: {{store.age}}</view>
<view>{{store.say}}</view>
<view>another: {{another.age}}</view>
<view>{{another.agex2}}</view>
<button bindtap="tapAdd">add</button>
```
当然，您也可在Component中使用:
```JavaScript
Component({
  ...
  ready(){
    this.disposer = inject(this,{store: store})
  },

  //请务必在组件生命周期结束前移除!!
  detached(){
    this.disposer();
  }
  ...
})
```



## API
### injiect(context,Object)
参数：
+ context:this
+ Object:stores

返回值：disposer:function,一个销毁器函数（在Page中使用时将自动在onUnload生命周期执行,但在Component构造器中使用时请确保在生命周期结束时手动调用此函数）。



## License

ISC licensed.
