# Asynchronous Workflow in Javasript

### Nothing
```javascript
function async(level, callback){
    setTimeout(function(){
        callback('Level: ' + level);
    }, 100 + Math.floor(Math.random() * (1000 - 100 + 1)));
}

async(1, function(result) {console.log(result)});
async(2, function(result) {console.log(result)});
async(3, function(result) {console.log(result)});
```

### Callback
```javascript
function asyncWithCallback(level, callback){
    setTimeout(function(){
        callback('Level: ' + level);
    }, 100 + Math.floor(Math.random() * (500 - 100 + 1)))
}

asyncWithCallback(1, function(result){
    console.log(result);
    asyncWithCallback(2, function(result){
        console.log(result);
        asyncWithCallback(3, function(result){
            console.log(result);
        });
    });
});
```
### Promises
```javascript
function asyncWithPromise(level){
    return new Promise (function(resolve){
        setTimeout(function(){
            resolve('level: ' + level);
        }, 100 + Math.floor(Math.random() * (500 - 100 + 1)));
    });
}

asyncWithPromise(1)
    .then(function(result){
        console.log(result);
        asyncWithPromise(2)
            .then(function(result){
                console.log(result);
                asyncWithPromise(3)
                    .then(function(result){
                        console.log(result);
                    });
            });

    });
```

### Better Promises
```javascript
function asyncWithPromise(level){
    return new Promise (function(resolve){
        setTimeout(function(){
            resolve('level: ' + level);
        }, 100 + Math.floor(Math.random() * (500 - 100 + 1)));
    })
    
}

asyncWithPromise(1)
    .then(function(result){
        console.log(result);
        return asyncWithPromise(2);
    })
    .then(function(result){
        console.log(result);
        return asyncWithPromise(3);
    })
    .then(function(result){
        console.log(result);
    });
```

### Even Better Promises
```javascript
function asyncWithPromise(level){
    return new Promise (function(resolve){
        setTimeout(function(){
            resolve('level: ' + level);
        }, 100 + Math.floor(Math.random() * (500 - 100 + 1)));
    });
}

function level1(){
    return asyncWithPromise(1)
        .then(function(result){
            console.log(result); 
        });
}
function level2(){
    return asyncWithPromise(2)
        .then(function(result){
            console.log(result); 
        });
}
function level3(){
    return asyncWithPromise(3)
        .then(function(result){
            console.log(result); 
        });
}

level1().then(level2).then(level3);
```

### Generators - 101
```javascript
function* foo(x){
    yield x;
    yield 2 * x;
    yield 3 * x;
}

var it = foo(1);

console.log(it.next()); // {value: 1, done: false}
console.log(it.next()); // {value: 2, done: false}
console.log(it.next()); // {value: 3, done: false}
console.log(it.next()); // {value: undefined, done: true}

// with for-of loop

for(var i of foo(2)){
    console.log(i);
}

function* bar(){
    yield 1;
    yield 2;
    yield 3;
    return 4; // not a good way since for-of will throw away return
}

var g = bar();

console.log(g.next()); // {value: 1, done: false}
console.log(g.next()); // {value: 2, done: false}
console.log(g.next()); // {value: 3, done: false}
console.log(g.next()); // {value: 4, done: true}

// with for-of loop

for (var j of bar()){
    console.log(j)
}


```

### Promises with Generators
```javascript
function asyncWithGenerators(level){
    return new Promise (function(resolve){
        setTimeout(function(){
            resolve('level: ' + level);
        }, 100 + Math.floor(Math.random() * (500 - 100 + 1)));
    });
}

co(function*(){
    var level1, level2, level3;

    level1 = yield asyncWithGenerators(1);
    console.log(level1);
    level2 = yield asyncWithGenerators(2);
    console.log(level2);
    level3 = yield asyncWithGenerators(3);
    console.log(level3);
});
```

### Error Handling
```javascript
function asyncErrorHandling(level){
    return new Promise (function(resolve, reject){
        var timeout = 100 + Math.floor(Math.random() * (500 - 100 + 1));

        setTimeout(function(){
            if (timeout % 2 === 0) return reject('Error at level: ' + level);
            resolve('level: ' + level);
        }, timeout);
    });
}

co(function*(){
    var level1, level2, level3;

    try {
        level1 = yield asyncErrorHandling(1);
        console.log(level1);
        level2 = yield asyncErrorHandling(2);
        console.log(level2);
        level3 = yield asyncErrorHandling(3);
        console.log(level3);
    }
    catch(e) {
        console.log(e);
    }
    
});
```
