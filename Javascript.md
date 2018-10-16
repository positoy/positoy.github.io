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

