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



# tsconfig



```json
{
    "compilerOptions": {
        "target": "es2020", // 指定 TS 编译成 JS 后的 JS版本
        "module": "ESNext", // TS编译成JS后采用的模块规范commonjs amd cmd es等
    	"moduleResolution": "Bundler",
    	"types": ["node"],
        "ib": ["DOM", "ES2020"], // 指定 TS编码期间可以使用的库文件版本,比如: ES5就不支持set集合
        "outDir": "./dist", // 指定TS文件编译成JS后的输出目录
        "rootDir": "./src", // 指定TS文件源码目录
        "strict": true, // 启用严格检查模式
        "strictNullChecks":false, // null和undefined即是值,也是类型,null 和undefined 值只能赋值给any,unknown和它们各自的类型
        "noImplicitAny": true, // 一般是指表达式或函数参数上有隐含的any类型时报错,即这些类型必须显示的指定类型
        "experimentalDecorators": true, // 启用ES7装饰器实验开启选项
        "emitDecoratorMetadata": true, // 启用装饰器元数据开启选项
        "declaration": true, // 指定TS文件编译后生成相应的.d.ts文件
        "noImplicitReturns": true, // 不是函数的所有返回路径都有返回值时报错
        "removeComments": true, // TS文件编译后删除所有的注释
        "importHelpers": true,
        "sourceMap": true, // 生成位置信息- - -map文件
        "baseUrl": "src", // 工作根目录.解析非相对模块的基地址
        "paths": {
            "@/datatype/*": ["datatype/*"],
            "@/131/*": ["131/*"],
            "@/132/*": ["132/*"]
        }
    },
    // 需要编译的ts文件,一个*表示文件匹配,**表示忽略文件的深度问题
    "include": [
        "vite.config.*",
        "./src/**/*" // 匹配src下所有的ts文件
    ],
    // 不需要编译的文件
    "exclude": []
}
```



# !,?.,??



* `!`: 非空断言,即在变量、属性的后边添加 `!` 断言操作符,它可以用来排除值为 null、undefined 的情况

  ```typescript
  let mayNullOrUndefinedOrString: null | undefined| string;
  mayNullOrUndefinedOrString!.toString(); // ok
  mayNullOrUndefinedOrString.toString(); // ts(2531),错误
  ```

* `?.`: 注意后面的点.变量如果是`null`或`undefined`,调用方式时,不会抛出异常,而是返回`undefined`

* `??`: 类似三元表达式.`const a = b ?? c`:如果b是`null`或`undefined`,则返回c,反之返回b,相当于`const a = b !== null || b !== undefined ? b : c`



# 常用类型



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

* `never`: 永远不会有返回值,never类型的值可以赋值给其他任意类型,但是除了never之外,其他类型的值都不能赋值给never.never是所有类型的子类型



# 联合类型



* 联合类型:Unions.用来表示变量、参数的类型不是单一原子类型,而可能是多种不同的类型的组合
* 通过`|`操作符分隔类型的语法来表示联合类型.可以把`|`类比为 JavaScript 中的逻辑或 `||`,只不过前者表示可能的类型
* `let name: string | number | boolean`: name可能是string,也可能是number,boolean类型
* `function test(unit: 'px' | 'rem' | 'em' | '%' = 'px')`: unit参数可以是px,rem,em,%几个值中的一个,若不传,默认是px
* 也可以使用type定义一个新的类型

```typescript
type Unit = 'px' | 'rem' | 'em' | '%';
type ModernUnit = 'vh' | 'vw';
function test(unit: Unit | ModernUnit){}
```

* 在对象中使用: 只能调用对象中都有的方法,单个对象独有的方法调用失败

```typescript
interface Bird {
	fly(): void;
	layEggs(): void;
}
interface Fish {
	swim(): void;
	layEggs(): void;
}
// 定义getPet变量为Bird或Fish类型,Fish给了默认值,并且强转为Bird | Fish 类型
const getPet: ()=> Bird| Fish=()=>{
	return {
	// ....
	} as Bird | Fish;
};
const Pet = getPet();
// ok
Pet.layEggs();
// ts(2339) 'Fish'没有'fly'属性; 'Bird | Fish'没有'fly'属性
Pet.fly();
// 使用in操作符(类型守卫)来判断,判断属性或方法,函数
if('fly' in Pet){
    // ok
    Pet.fly();
}
// 判断对象实例还是不是某一个类
if(Pet instanceof Bird){}
// 使用type报错
if(typeof Pet.fly === 'function'){
    // 错误
    Per.fly();
}
```



# 交叉类型



* 交叉类型:Intersection Type,它可以把多个类型合并成一个类型,合并后的类型将拥有所有成员类型的特性
* 在TypeScript中,使用`&`操作符来声明交叉类型:`type Useless = string & number;`
* 如果仅仅把原始类型、字面量类型、函数类型等原子类型合并成交叉类型,是没有用处的,因为任何类型都不能满足同时属于多种原子类型.因此,Useless的类型就是个never
* 交叉类型主要用在接口的合并,可以获得类似接口继承的效果.但是同名的属性如果类型不兼容,合并后,属性类型可能变为never

```typescript
type IntersectionTypeConfict = { id: number; name: string; } & { age: number; name: number; };
const mixedConflict: IntersectionTypeConfict = {
    id:1,
	name: 2,	// ts(2322)错误,'number'类型不能赋给'never'类型
    age: 2
};
```

* 给mivedConfict对象的name属性赋任意值都会提示类型错误,而如果不设置nane属性,又会提示一个缺少必选的 name属性的错误
* 如果同名属性的类型兼容,比如一个是number,另一个是number的子类型、数字字面量类型,合并后 name属性的类型就是两者中的子类型

```typescript
type IntersectionTypeConfict = { id: number; name: 2; }& {age: number; name: number; };
let mixedConflict: IntersectionTypeConfict = {
    id: 1,
	name: 2,	// ok,age: 2
};
mixedConflict = {
    id: 1,
	name: 22,	// '22'类型不能赋给'2′类型age: 2
};
```

* 联合类型也可以使用交叉类型,但是交叉的结果是取他们的交集,即联合类型中都有的部分.如果没有任何交集,则结果是never



# 类型缩减



* 如果将 string 原始类型和`string字面量类型`组合成联合类型会是什么效果?效果就是类型缩减成 string 了

```typescript
type URStr = 'string' | string; //类型是string
type URNum = 2 | number; //类型是number
type URBoolen = true | boolean; //类型是boolean
enum EnumUR{
	ONE,
	Two
}
type URE = EnumUR.ONE |EnumUR;//类型是EnumUR
```

* 类型缩减会降低IDE的提示功能,加上交叉即可

```typescript
type BorderColor = 'black' | 'red' | 'green' | 'yellow' | 'blue' | string & {};//字面类型都被保留
```

* 当联合类型的成员是接口类型,如果满足其中一个接口的属性是另外一个接口属性的子集,这个属性也会类型缩减

```typescript
// 这里因为 '1' 是 '1' | '2' 的子集，所以 age 的属性变成 '1' | '2'
type UnionInterce =
  | {age:  '1';}
  | ({
      age: '1' | '2';
      [key:string]: string;
  });
```

* never有个特性是所有类型的子类型,有时候可以做一些很奇怪的事

```typescript
// 以下代码中即定义了 number 类型的 age 属性,又定义了never类型的age 属性,等价于age 属性的类型是由 nunber 和o never类型组成的联合类型,所以可以把 number 类型的值赋子age 属性;但是不能把其他任何类型的值(比如说字符串字面量'string')赋予cge
// 同时,在下面定义的接口类型中,还额外定义了string类型的字符串索引签名.因为never同时又是strinq类型的子类型,所以age属性的类型和字符串索引签名类型不冲突
// 这样就可以把一个age 属性是2、string 属性是'string'的对象字面量赋值给 UnionInterce 类型的变量O
// 因为正常情况下,age必须是string类型
type UnionInterce =
l {age: number;}
l ({
	age: never;
	[key: string]: string;
   });
const O: UnionInterce = {
    age: 2,
    string: 'string'
};
```



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



# 泛型



* 比any更具体的参数类型,指定参数传递的类型

```typescript
// T可以是任意类型
Array<T>
// 如果使用Array的时候不指定泛型,则默认使用any.如果不指定,则是unknown
Array<T=any>;
// 泛型默认为对象
Array<T={}>;
// 此时arr1中的元素类型是unknown,而不是any
let arr1 = new Array();
```

* `T extends (params: infer P) => any`: 适用于非继承时,T的类型符合`(params: infer P)=>any`,满足某个条件.P可以出现在参数上,也可以出现在返回值上
  * 相当于将T的实际类型替换道T的位置,然后判断表达式定义是否满足extends后的表达式定义
  * 多数适用于参数是泛型,然后还带其他泛型,如`T<K<M>>`获取M的类型

```typescript
type a = (str: string) => string; // 函数类型
type b = (str:string,num:number)=>string;
// 此时,当a的参数表示和返回值类型都被extends后的表达式兼容时,返回P类型,否则返回T
type c<T> = T extends (params: infer P)=>any ? P:T
// a类型满足extends后的表达式,所以d为string类型
// 相当于 (str: string) => string extends (params: infer P)=>any ? P:T,此时T的实际定义格式满足extends后的格式,P为string
type d = c<a>;
// b不满足extends后的表达式,所以e为T类型.如果要返回P,则c<T>=T extends (params: infer P,num:number)=>any ? P:T
type e = c<b>;
```



# extends



* 类的继承
* 在判断泛型时也可以使用

```typescript
// 如果T继承U,返回T类型,否则返回never.其实可以直接用Extract<T extends U,U>,这样写的意义何在?
type type1<T,U> = T extends U ? T : never;
```



# Extract,Exclude



* Extract:判断第一个参数类型是否为第二个参数类型的子类.该方法之后2个参数类型,本质上是一个三元表达式
* 泛型类型可以使用联合类型,交叉类型,方法
* Exclude:和Extract刚好相反

```typescript
// 源码T extends U ? T : never.如果T是U的子类则返回T,否则返回never
type type1 = Extract<T,U>;
// 源码:T extends U ? never : T;.如果T是U的子类则返回never,否则返回T
type type2 = Exclude<T,U>;
```



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



# Type



* 类型别名: `type 别名 = 类型定义`,接收抽离出来的内联类型

```typescript
// 定义一个类型接收新的对象定义
type Alias = {
	name: string;
	age: ()=> number;
}
// 定义常规类型无法定义的变量
type MixedType = string | number;
// 交叉定义
type InterType = {id:number,name:string} & {age:number,name:string}
// 提取接口属性类型
type NameType = Alias['name'];
```

* `typeof obj`: 获取对象实例的类型
* `keyof typeof obj`: 获得对象实例的键的联合类型,相当于获取map中的key的集合,不是key类型的集合,就是key的集合
* `keyof T`: 得到的是泛型的属性,方法名等
* `keyof any`: 只能是`string | number | symbol`,是规定



# in



* 使用in操作符(类型守卫)来判断,判断属性或方法,函数

```typescript
if('fly' in Pet){
    // ok
    Pet.fly();
}
```

* 在泛型中指定类型,此时为迭代的意思

```typescript
tyep Record<K extends keyof any,T>={
    // P的类型根据K来改变,K传进来是什么就是什么
    // 如果K是联合类型,那联合类型中所有的类型都要定义
	[P in K]: T
}
```



# 接口



```typescript
// 在接口中定义类似Map的接口,并约束key和value的类型
interface LikeMap{
    // 约束key的类型为string,可以指向任意参数名,key只是一个代指.如果不想约束value的类型,string可以改成any
    [key: string]: string;
    // age必须是string,此处会报错
    age: number;
}

let map: LikeMap={
    'xxx': 'vvvv'
}
```

* 大多数场景下,type可以替代接口,但是type重复定义会报错,而接口不会

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



# 类



* 类中如果有不定参数,可以使用引用类型,也可定义一个Map

```typescript
class Test{
	[name:string]: any;
}
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

* 函数重载的好处是可以有提示,不用强转
* 函数重载时若是有其他方法的参数个数不匹配,可将重载实现的参数给默认值或使用`?`将参数变成可传可不传的参数
* `class`中的构造函数也可以重载



# 枚举



* 7 种常见的枚举类型: 数字类型、字符串类型、异构类型、常量成员和计算（值）成员、枚举成员类型和联合枚举、常量枚举、外部枚举
* 在仅仅指定常量命名的情况下,实际定义的就是一个默认从 0 开始递增的数字集合,称之为数字枚举
* 如果需要枚举值从其他值开始递增,则可以通过`常量命名 = 数值` 的格式显示指定枚举成员的初始值

```typescript
enum Day {
    SUNDAY = 1,
    MONDAY,
    TUESDAY,
    WEDNESDAY,
    THURSDAY,
    FRIDAY,
    SATURDAY
  }
```

* 可以给 SUNDAY 指定任意类型(比如整数、负数、小数等)、任意起始的数字,其后未显示指定值的成员会递增加 1
* 如果给SATURDAY 赋值5,实际结果是所有的枚举值都是5,这可能是一个BUG,所以最好只给第一个成员赋值
* 字符串枚举

```typescript
enum Day {
    SUNDAY = 'SUNDAY',
    MONDAY = 'MONDAY',
   ...
  }
```

* 异构枚举:值不是同种类型,没什么实际用到的场景

```typescript
enum Day {
    SUNDAY = 'SUNDAY',
    MONDAY = 2
   ...
  }
```

* 定义枚举时可以加上const,将枚举变为一个常量枚举,其中的成员都是不可变值



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



