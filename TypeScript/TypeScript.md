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



# Generator



* ES6 中新增的 Generator 函数在 ts 中也有对应的类型定义,Generator 函数返回的是一个 Iterator 迭代器对象,可以用 Generator  的同名接口泛型或者 Iterator 的同名接口泛型表示返回值的类型

```typescript
type AnyType=boolean
type AnyReturnType=string
type AnyNextType=number
function *gen: Generator<AnyType, AnyReturnType, AnyNextType> {
	const nextValue =yield true	// nextValue 类型是 number,yield 后必须是 boolean 类型
    return `${nextValue}`; // 必须返回 string 类型
}
```



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



# 方法



* 方法定义格式:`function fn(a:string ,b:string):string{}`或`const fn = (a:string ,b:string):string =>{}`
* ts中的`=>`和ES6的不一样,在接口中定义方法时,`=>`右侧是返回值类型

```typescript
interface User{
	add:(a:number ,b:number)=>number;
}
const user: User={
    add:(a:number ,b:number)=>a+b;
}
```

* 无返回值的方法,在定义返回类型时,不能使用`undefined`,而需要使用`void`

```typescript
// 无返回值时,默认输出undefined
function fn(){}
// 在ts中,不能将无返回值的方法返回类型定位为undefined
function fn():undefined{} // 错误
function fn():void{} // 正确
```



# 参数类型



* `a?:string`: a参数可传可不传,但不等同于`undefined`
* `a='hi'`: a的默认值是hi,可根据类型推断a为`string | undefined`
* `a:string='hi'`: a参数是string类型,默认值是hi
* `...nums: number[]`: 剩余参数,把多个参数收集到一个变量中
* `...nums: (number | string)[]`: 多类型剩余参数



# this



* 通过指定this的类型(严格模式下,必须显式指定 this 的类型),当错误使用了this,ts就会提示:

```typescript
function say(){
	console.log(this.nane); // ts(2683) ' this' implicitly has type 'any' because it does not have a type annotation
}
// 如果直接调用 say(),this 应该指向全局 window 或 globol (Noade 中).但是,在 strict 模式下的 ts 中,它会提示this 的类型是any,此时就需要手动显式指定类型了
say();
```

* 只需要在函数的第一个参数中声明this指代的对象(即函数被调用的方式)即可

```typescript
function say(this: Window , name: string) {
	console.log(this.name);
}
window.say = say;
window.say( 'hi');
const obj = {
    say
};
// say的this不是指向window,报错
obj.say("hi');// ts(2684) The 'this' context of type '{ say:(this: Window, nane: string)=> void;}' is not assignable to method's 'this' of type Window '.
```

* 在class内部,普通方法的this默认指向当前对象.但是静态方法中的this需要显示指定



# 抽象类



* 子类必须实现抽象父类的抽象属性和抽象方法

```typescript
// 抽象类
abstract class Adder {
    // 抽象属性和抽象方法
    abstract x: number;
    abstract y: number;
    abstract add(): number;
    // 已初始化属性
    displayName = 'Adder';
    // 普通方法
    addTwice(): number {
        return (this.x + this.y)* 2;
    }
}

// 子类
class NumAdder extends Adder {
    // 实现父类的抽象属性
    x: number;
    y: number;
    constructor(x: number, y: number){
        super();
		this.x = x;this.y = y;
    }
    // 实现父类的抽象方法
	add(): number {
		return this.x + this.y;
    }
}
                              
const numAdder = new NumAdder(1,2);
console.log(numAdder.displayName); // => "Adder"
console.log(numAdder.add()); // => 3
console.log(numAdder.add Twice()); //=> 6
```



# 函数重载



* 函数重载:Function Overoad,如下示例中1~3行定义了三种各不相同的函数类型列表,并描述了不同的参数类型对应不同的返回值类型,而从第4行开始才是函数的实现

```typescript
function convert(x: string): number;
function convert(x: number): string;
function convert(x: null): -1;
function convert(x: string | number | null): any {
    if (typeof x === 'string'){
		return Number(x);
    }
    if (typeof x === 'number'){
        return String(x);
    }
	return -1;
}
const x1 = convert('1'); // =>number
const x2 = convert(1); // => string
const x3 = convert(null); // -1
```

* 函数重载列表的各个成员(即示例中的1~3行)必须是函数实现(即示例中的第4行)的子集
* 在convert函数被调用时,TypeScipt会从上到下查找函数重载列表中与入参类型匹配的类型,并优先使用第一个匹配的重截定义.如果参数类型有继承关系,即使方法调用时强转为子类型,仍然调用的是父类方法

```typescript
interface P1 {
    name: string;
}
interface P2 extends P1{
    age: number;
}
function convert(x:P1): number;
function convert(x: P2): string;
// 重载实现
function convert(x: P1 | P2): any { };

// 因为P2继承自P1,所以类型为P2的参数会和类型为P1的参数一样匹配到第一个函数重载,此时x1、x2的返回值都是rumber
const x1 = convert({ name: "" } as P1); // => number
const x2 = convert({ name: "" , age: 18 } as P2); // number

function convert(x: P2): string;
function convert(x: P1): number;
function convert(x: P1 | P2): any { }
// 只需要将函救重载列表的顺序调换一下,类型为P2和P1的参数就可以分别匹配到正确的函数重载
const x1 = convert({ name: '' } as P1); // => number
const ×2 = convert({ name: '' , age: 18 } as P2); // => string
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



