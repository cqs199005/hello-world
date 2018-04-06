# ES6笔记

### 1.常量

- #### ES5中的声明方法

```
Object.defineProperty(window,'pi2',{
    value:3.1415926,
    writable:false,  //设置只读,不可修改
});

console.log(window.pi2);
window.pi2 = 9999;
console.log(window.pi2);
```

- #### ES6中的写法

  ```
      const pi3 = 3.1415926;
      console.log(pi3);
  //    pi3 = 4;   会报错
  ```



### 2.作用域

- #### ES5中var 作用域是全局

```
//ES5 var 声明的i是全局变量,当调用函数时,i循环完的值是3
var callback = [];
for(var i = 0; i <= 2; i++) {
    //闭包函数
    callback[i] = function () {
        return i * 2;
    }
}
console.log(callback[0]()); //6
console.log(callback[1]()); //6
console.log(callback[2]()); //6

ES5中想用块作用域,一般是是用自调用函数;(function fn() {})()
```

- #### ES6中let 是块作用域

```
//ES6 let 块作用域,每循环一次,就生成一个块作用域,调用函数时闭包指向对应的块作用域
var callback2 = [];
for(let i = 0; i <= 2; i++) {
    //闭包函数
    callback2[i] = function () {
        return i * 2;
    }
}
console.log(callback2[0]()); //0
console.log(callback2[1]()); //2
console.log(callback2[2]());//4
```

```
ES6中可以用 {
               这里面的一个单独的块作用域
            }
```



### 3.箭头函数

- #### ES3/ES5 中声明函数方法

  ```
  //ES3,ES5函数声明
  function aaa(a,b) {
      return a +b;
  }
  ```

  this的指向

  ```
  //function传统定义的函数，this指向随着调用环境的改变而改变,谁调用,this指向谁
  function aaa(){
      console.log(this)
  }
  var obj={
      aaa:aaa
  };
  aaa();//此时输出window对象
  obj.aaa();//此时输出obj对象
  ```

- #### ES6中声明函数

  ```
  //ES6箭头函数
  var bbb=(a,b)=> { return a + b;}

  // 当函数体只有一句时,可以省略{}和renturn
  var ccc=(a,b)=> a + b;

  //当参数只有一个时,可以省略括号
      var ddd = v => v;
      

  ```

this 的指向

```
    //箭头函数中,this 的指向是定义时this的指向
    var aaa=()=>{
        console.log(this)
    };
    var obj={
        aaa:aaa
    }
    aaa();//此时指向window
    obj.aaa();//此时指向window
```

注意事项:

1. 由于js的内存机制，function的级别最高，而用箭头函数定义函数的时候，需要var(let const定义的时候更不必说)关键词，而var所定义的变量不能得到变量提升，故箭头函数一定要定义于调用之前！
2. 箭头函数固然好用，但是不能用于构造函数，即不能被new一下；



### 3.设置函数参数默认值

- #### ES5

```
function fn(a,b,c) {
    a = a || 1;
    b = b || 2;
    c = c || 3;
    console.log(a)
    console.log(b)
    console.log(c)
}
```

- #### ES6

```
function fn2(a=11,b=22,c=33) {
    console.log(a)
    console.log(b)
    console.log(c)
}	
```



### 4.ES6扩展函数及数组合并

- #### ES5合并数组

```
	//ES5中合并数组
    var arr1 = [1, '你好', '世界'];
    var arr2 = ['aa', 'hello'].concat(arr1);
    console.log(arr2);
```

- #### ES6扩展运算符

```
//可变参数,当不确定传入参数有几个时,可以用...a(扩展运算符)代替形参
function fn(...a) {
    var sum = 0;
    a.forEach(key => {
        sum += key;
});
    return sum;
}
console.log(fn(1,2,3,4,5))
```

```
	//ES6合并数组
    var arr1 = [1, '你好', '世界'];
    var arr2 = ['aa', 'hello',...arr1];
    console.log(arr2);
```



### 5.代理对象,对数据隐私保护

- #### ES3中对数据的保护,通过闭包进行保护

  ```
  //设置性别属性不能更改
  var Person = function () {
      var data = {
          name: 'zs',
          age: 18,
          sex: '男'
      };
      this.get = function (v) {
          return data[v];
      }
      this.set = function (k, v) {
          if (k != 'sex') {
              return data[k] = v;
          }
      }
  }
  var person = new Person();
  console.table({
      name:person.get('name'),
      age:person.get('age'),   //男
      sex:person.get('sex')
  });
  person.set('sex','女');
  person.set('name','ls');
  console.table({
      name:person.get('name'),
      age:person.get('age'),   //男
      sex:person.get('sex')
  });
  ```


- #### ES5中通过设置常量来保护数据

  ```
  var Person = {
      name:'zs',
      age:18
  };
  Object.defineProperty(Person,'sex',{
      writable:false,
      value:'男'
  });
  console.table({
      name:Person.name,
      age:Person.age,
      sex:Person.sex
  });
  Person.name = 'ls';
  Person.sex = '女';
  console.table({
      name:Person.name,
      age:Person.age, 
      sex:Person.sex
  });
  ```


- #### ES6中通过设置代理对象,让代理对象来管理数据的访问与修改

  ```
  let Person = {
      name:'zs',
      age:18,
      sex:'男'
  };
  //设置代理对象
  let person = new Proxy(Person,{
      get(target,key) {
          return target[key];
      },
      set(target,key,value) {
          if(key != 'sex') {
              target[key] = value;
          }
       }
  });
  console.table({
      name:person.name,  //zs
      age:person.age,
      sex:person.sex   //男
  });
  person.name = 'ls';
  person.sex = '女';
  console.table({
      name:person.name,  //ls
      age:person.age,
      sex:person.sex    //男
  });
  ```