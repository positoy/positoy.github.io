# [자바스크립트에서 클래스를 만드는 3가지 방법](http://steadypost.net/post/lecture/id/13/)

1. function object

- internal

    var Apple = function() {
        this.x = 1;
        this.y = 2;
        this.func = function() {
            console.log(this.x + this.y);
        }
    }
    var apple = new Apple();

- prototype

    var Apple = function() {
        this.x = 1;
        this.y = 2;
    }
    Apple.prototype = {
        func:function() {
            console.log(this.x + this.y);
        }
    }
    var apple = new Apple();

2. function object singleton

    var apple = new function() {
        this.x = 1;
        this.y = 2;
        this.func = function() {
            console.log(this.x + this.y);
        }
    }
        

3. literal object (singleton)

    var o = {}
    var o = new Object();
    var a = []
    var a = new Array();
    var apple = {
        x:1,
        y:2,
        func:function() {
            console.log(this.x + this.y);
        }
    }

4. class (ES5)

    class Apple {
        constructor() {
            this.x = 1;
            this.y = 2;
        }

        func() {
            console.log(this.x + this.y);
        }
    }
    var apple = new Apple();

# [call, apply, bind](https://blog.weirdx.io/post/3214)

1. 필요성

    var apple = new function() {
            this.x = 1;
            this.y = 2;
            this.printSum = function() {
                    console.log(this.x + this.y);
            }

            function printMultiply(a,b) {
                    console.log(this.x * this.y);
                    console.log(a,b);
            }

            this.func = function() {
                    printMultiply(); // NaN!
            }
            this.funcCall = function() {
                    printMultiply.call(this,10,100);
            }
            this.funcApply = function() {
                    printMultiply.apply(this,[10,100]);
            }
            this.funcBind = function() {
                    var bindFunc = printMultiply.bind(this);
                    bindFunc();
            }
    }

2. function.prototype.call(this, param1, param2, ...)

3. function.prototype.apply(this. [param1, param2, ...])

4. function.prototype.bind(this) => new binded function
