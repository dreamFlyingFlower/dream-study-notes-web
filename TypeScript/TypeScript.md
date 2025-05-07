# TypeScript



# 安装



* `npm i -g typescript`: 全局安装最新版本ts
* `npm i -g typescript@4.2.*`: 全局安装指定版本ts
* `tsc -v`: 查看ts版本
* `tsc --init`: 创建一个`tsconfig.json`文件,配置ts行为
* `tsc file.ts`: 将ts文件编译为js文件.指定目标文件后,tsc 将忽略当前路径下的 tsconfig.json ,因此需要显式指定参数,让 tsc 以严格模式检测并转译ts
  * `tsc HelloWorld.ts --strict --alwaysStrict false`: 以严格模式编译ts
  * `tsc HelloWorld.ts --strict --alwaysStrict false --watch`: 设定一个 watch 参数监听文件内容变更,实时进行类型检测和代码编译
* 



# !,?.,??



* `!`: 非空断言,即在变量、属性的后边添加 `!` 断言操作符,它可以用来排除值为 null、undefined 的情况

  ```typescript
  let mayNullOrUndefinedOrString: null | undefined| string;
  mayNullOrUndefinedOrStringl.toString0; // ok
  mayNullOrUndefinedOrString.toString(); // ts(2531),错误
  ```

* `?.`: 注意后面的点.变量如果是`null`或`undefined`,调用方式时,不会抛出异常,而是返回`undefined`

* `??`: 类似三元表达式.`const a = b ?? c`:如果b是`null`或`undefined`,则返回c,反之返回b,相当于`const a = b !== null || b !== undefined ? b : c`



# 对象类型



* `string`: 字符串,对象String

* `number`: 数字,对象Number

* `boolean`: 布尔,对象Boolean

* `null`: 一个空对象

* `undefined`: 可缺省,未定义的属性

* `symbol`: 标记,对象Symbol

* `[]或Array<>`: 数组

* `any`: 任意类型

* `unknown`: 不确认类型.可以赋值任意类型.unknown只能赋值给unknown和any.如果使用`typeof`对变量进行检查,可以使用检查后的类型做响应操作

  ```js
  let result: unknown;
  if(typeof result === 'number'){
  	result.toFixed();
  }
  ```

* 元组: tuple.编译后仍然是数组,但是可以限制数组的长度和类型

* `never`: 永远不会有返回值,never类型的值可以赋值给其他任意类型,但是除了never之外,其他类型的值都不能赋值给never



# 类型检查和强转

* `type className = InstanceType<typeof Object>>`: 获得Object实例对象的真实类型,主要在泛型参数中使用



```typescript
const arrayNumber: number[]=[1,2,3, 4];
// ts,greaterThan2的类型既可能是数字,也可能是undefined,所以提示了一个ts(2322)错误
const greaterThan2: number = arrayNumber.find(num=> num> 2); //提示ts(2322)
// 可以使用as或<type>进行强制转换,推荐使用as
const greaterThan2: number = arrayNumber.find(num => num > 2) as number;
const greaterThan2: number = <number>arrayNumber .find(num=> num> 2);
```



# IDE



* 在VSCode中创建`tsconfig.json`约束ts的行为,进行代码类型检查

  ```json
  {
    "compilerOptions": {
      /* Strict Type-Checking Options */
      "strict": true,                           /* Enable all strict type-checking options. */
      "noImplicitAny": true,                 /* Raise error on expressions and declarations with an implied 'any' type. */
      "strictNullChecks": true             /* Enable strict null checks. */
      "strictFunctionTypes": true/* Enable strict checking of function types. */
      "strictBindCallApply": true/* Enable strict 'bind', 'call', and 'apply' methods on functions. */
      "strictPropertyInitialization": true/* Enable strict checking of property initialization in classes. */
      "noImplicitThis": true/* Raise error on 'this' expressions with an implied 'any' type. */
      "alwaysStrict": false/* Parse in strict mode and emit "use strict" for each source file. */
    }
  }
  ```

* VSCode内置ts版本可能和项目版本不一致,一般项目版本为`node_modules/typescript/lib`



