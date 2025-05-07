# JS



# ES6



## Decorator



* 修饰器:是一个函数,用来修改类的行为.ES2017 引入了这项功能,目前 Babel 转码器己经支持

  ```javascript
  <script>
      // 通过@符号进行引用该方法,类似java中的注解.T方法会在User被调用之前调用
      @T
      class User {
          constructor(name, age = 20){
              this.name = name;
              this.age = age;
          }
      }
  	// 定义一个普通的方法.target对象为修饰的目标对象,这里是User对象
      function T(target) {
          console.log(target);
          // 为User类添加一个静态属性country
          target.country = "中国"; 
      }
  	console.log(User.country);
  </script>
  ```

  



