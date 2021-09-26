---
author: wanfeng
sidebar: 'auto'
date: 2021-8-10

categories: Javascript
tags:
  - Javascript
  - Web
---
## vue中的响应式原理

### vue2.x中的响应式

1. 最开始的时候把 data 里面的数据进行加工，变成一个拥有一堆 getter 和 setter 的对象(Observer)[数据劫持]，原理是通过 Object.defineProperty 来实现



2. data 完成加工后就会把赋值给 \_data，_data通过数据代理，就可以把上面的数据给到 vm 身上



3. 数据代理实际上的用途就是让数据访问起来更加方便，直接在插值表达式写 name 就行，不同 _data.name



4. 页面一旦数据发生改变，就会调用 setter 方法，里面有一个调用，会重新触发模板解析，生成新的虚拟DOM（diff算法会计算最小更新量）

​          

### defineProperty的使用

`Object.defineProperty` 是 ES5 中一个无法 shim (降级) 的特性，这也就是 Vue 不支持 IE8 以及更低版本浏览器的原因

vue2.x中的响应式数据原理是依赖 Object.defineProperty 来实现的

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app"></div>
    <!-- <input id="app"></input> -->
    <script>
        // Object.defineProperty 是 ES5 中一个无法 shim (降级)的特性，
        // 这也就是 Vue 不支持 IE8 以及更低版本浏览器的原因

        // 假设是 vue 实例中的 data 选项
        let data = {
            msg: 'hello'
        }

        // vm 实例
        let vm = {};

        // 数据劫持: 当访问或者设置 vm 对象中的成员的时候
        // 做出的一些干预性的操作
        Object.defineProperty(vm, 'msg', {
            // 是否可枚举
            enumerable: true,
            // 是否可配置
            configurable: true,
            get(){
                console.log('get',data.msg)
                return data.msg
            },
            set(newValue){
                console.log('set',newValue)
                if(newValue === data.msg){
                    return 
                }
                data.msg = newValue
                // document.querySelector('#app').value = data.msg
                document.querySelector('#app').textContent = data.msg

            }
        })


        vm.msg = 'abc'
        // console.log(vm.msg)
        // console.dir(document.querySelector('#app'));
        // document.querySelector('#app').value = 12


    </script>
</body>
</html>
```

注意这仅仅是响应式，描述的是当数据发生变化时，视图也会随之变化
而非双向绑定，注意概念的区分

### 当 data 中有多个属性时

```js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div id="app"></div>
    <script>
        // 假设是 vue 实例中的 data 选项
        let data = {
            msg: 'hello'
        }

        // vm 实例
        let vm = {};
        
        function proxyData(data){
            // keys: 用于获取对象上所有的属性
            // key: 属性名
            Object.keys(data).forEach( (key) => {
                Object.defineProperty(vm, key, {
                    // 是否可枚举
                    enumerable: true,
                    // 是否可配置
                    configurable: true,
                    get(){
                        console.log('get',key,data[key])
                        return data[key]
                    },
                    set(newValue){
                        console.log('set',key,newValue)
                        if(newValue === data[key]){
                            return 
                        }
                        data[key] = newValue
                        document.querySelector('#app').textContent = data[key]
                    }
                })
            })
        }
    </script>
</body>
</html>
```

