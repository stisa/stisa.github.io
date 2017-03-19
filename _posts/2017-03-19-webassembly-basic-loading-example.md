---
date: "2017-03-19T23:55:00+01:00"
draft: false
title: "WebAssembly basic example: Adding"
layout: post
tags: ["webassembly", "example"]
---
Let's begin with seeing it work.  
Write two numbers in the boxes below and then click on `=` to show the result.

<input id="arg1" style="text-align: right;" placeholder="integer" size="7" />
<span>+</span>
<input id="arg2" style="text-align: right;" placeholder="integer" size="7" />
<input type="submit" value="=" onclick="showRes()"/>
<i id="result">integer</i>

Pretty basic huh?  
Well, here's the kicker: the result was calculated with [WebAssembly](http://webassembly.org/).

Specifically, the `wast` representation of the module ( besides the comments after `#` ) used is:
```
(module 
 # Define a function that takes 2 i32 and return a i32
 (type $0 (func (param i32 i32) (result i32)))
 (memory $0 0)
 # Export the first function defined in the type section with name "add"
 (export "add" (func $0))
 # The body of the function defined above
 (func $0 (type $0) (param $var$0 i32) (param $var$1 i32) (result i32) 
  # The opcode that stands for addition of i32
  (i32.add
   (get_local $var$0) # load first param
   (get_local $var$1) # load second param
  )
 )
) 
```
And here's the binary representation:
```
  Offset: 00 01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E 0F 
--------:------------------------------------------------
00000000: 00 61 73 6D 01 00 00 00 01 07 01 60 02 7F 7F 01 
00000010: 7F 03 02 01 00 07 07 01 03 61 64 64 00 00 0A 09 
00000020: 01 07 00 20 00 20 01 6A 0B                      
```

Let's have a closer look at the binary representation:  

`00 61 73 6D`  
Spells out `\0wasm`  

`01 00 00 00`  
Tells us that this module uses wasm version 1 ( `01` )  

`01 07 01 60 02 7F 7F 01 7F`  
- `01` A `type` section follows
- `07` There are 7 more bytes in this section
- `01` Only 1 type is defined
- `60` The first type is a function
- `02` It takes two arguments
- `7F` First argument is an `i32
- `7F` Second argument is an `i32
- `01` It has a result
- `7F` The result is an `i32`

`03 02 01 00`  
- `03` An `function` section follows
- `02` It is two bytes long
- `01` There's one function in this section
- `00` It is the first function define in the `type` section

`07 07 01 03 61 64 64 00 00`  
- `07` An `export` section follows
- `07` It is 7 bytes long
- `01` One thing is exported
- `03` The name is 3 bytes long
- `61` character `a`
- `64` character `d`
- `64` character `d`
- `00` The thing is a function
- `00` The function is the first defined in the `type` section

`0A 09 01 07 00 20 00 20 01 6A 0B`  
- `0A` A `code` section follows
- `09` 9 bytes long
- `01` One function body
- `07` The body is 7 bytes
- `00` Number of local variables
- `20` Get local specified by next byte
- `00` First parameter
- `20` Get local
- `01` Second parameter
- `6A` Opcode for Add
- `0B` End marker of function body

Lastly, to load the module we need a bit of javascript:
```js
var wasmexports;
fetch('/js/add.wasm').then(response =>
  response.arrayBuffer()
  ).then(bytes =>
  WebAssembly.instantiate(bytes)
  ).then(results => {
    wasmexports = results.instance.exports
});
```
And in my case, to pass the value of the inputs to WebAssembly:
```js
function showRes(){
  var a = document.getElementById("arg1").value
  var b = document.getElementById("arg2").value
  document.getElementById("result").innerText = wasmexports.add(a,b)
}
```
<script>
    var wasmexports;
    fetch('/js/add.wasm').then(response =>
        response.arrayBuffer()
        ).then(bytes =>
        WebAssembly.instantiate(bytes)
        ).then(results => {
            wasmexports = results.instance.exports
    });
    function showRes(){
        var a = document.getElementById("arg1").value
        var b = document.getElementById("arg2").value
        document.getElementById("result").innerText = wasmexports.add(a,b)
    }
</script>

This blog is hosted on github, so you can find the wasm file [here](https://github.com/stisa/stisa.github.io/tree/master/js) and the `markdown` file [here](https://github.com/stisa/stisa.github.io/tree/master/_posts/2017-03-19-webassembly-basic-loading-example.md).