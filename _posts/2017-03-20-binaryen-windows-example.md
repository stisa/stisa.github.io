---
date: "2017-03-20T11:12:00+01:00"
draft: false
title: "Binaryen Windows example"
layout: post
tags: ["webassembly", "example","binaryen","windows"]
---
In this example we will compile the [C api hello-world](https://github.com/WebAssembly/binaryen/blob/master/test/example/c-api-hello-world.c) on Windows, using
[mingw-w64](https://mingw-w64.org/doku.php).

## Prerequisites 
First thing first, here are the prerequisites that we need to have installed:
- mingw-w64 **with  posix threads**, this means when installing mingw select `thread model: posix` in the dropdown. Mingw buindles `mingw32-make`, that we'll need later
- [cmake](https://cmake.org/)
- [git](https://git-scm.com/)

## Building libbinaryen.dll
1. clone  [binaryen repo](https://github.com/WebAssembly/binaryen) : `git clone https://github.com/WebAssembly/binaryen.git`
2. open a command prompt into the folder you just cloned ( or move into it if you already were in a prompt, eg. `cd binaryen`)
3. now run `cmake .  -G "MinGW Makefiles"`, this will run cmake and generate makefiles we can run with `mingw32-make`
4. finally, run make: `mingw32-make`

Now look into `binaryen/bin`, you should find the various tools listed in [the repo](https://github.com/WebAssembly/binaryen#tools) and our lib `libbinaryen.dll`  
Now we can start compiling something!

## The hello world: adding two numbers
The source is taken from the [C api hello-world](https://github.com/WebAssembly/binaryen/blob/master/test/example/c-api-hello-world.c), with a small change
to turn on some debug output.  
Also, for simplicity's sake, we are going to use `"binaryen-c.h"` instead of `<binaryen-c.h>` to include the header, so we need to copy the header (`binaryen-c.h`, found in `binaryen/src`) and our dll (`libbinaryen.dll`) to the folder that contains `helloworld.c`

```c
// we use " instead of <>
#include "binaryen-c.h" 

// "hello world" type example: create a function that adds two i32s and returns the result
int main() {
    BinaryenSetAPITracing(1); // we turn on tracing

    BinaryenModuleRef module = BinaryenModuleCreate();

    // Creation a function type for  i32 (i32, i32)
    BinaryenType params[2] = { BinaryenInt32(), BinaryenInt32() };
    BinaryenFunctionTypeRef iii = BinaryenAddFunctionType(module, "iii", BinaryenInt32(), params, 2);

    // Get the 0 and 1 arguments, and add them
    BinaryenExpressionRef x = BinaryenGetLocal(module, 0, BinaryenInt32()),
                        y = BinaryenGetLocal(module, 1, BinaryenInt32());
    BinaryenExpressionRef add = BinaryenBinary(module, BinaryenAddInt32(), x, y);

    // Create the add function
    // Note: no additional local variables
    // Note: no basic blocks here, we are an AST. The function body is just an expression node.
    BinaryenFunctionRef adder = BinaryenAddFunction(module, "adder", iii, NULL, 0, add);

    // Print it out
    BinaryenModulePrint(module);

    // Clean up the module, which owns all the objects we created above
    BinaryenModuleDispose(module);

    return 0;
}

```

To compile, run `gcc helloworld.c -L. -llibbinaryen -o helloworld.exe`

Now if you run `helloworld.exe` you should see something similar to this:
```
// beginning a Binaryen API trace
#include <math.h>
#include <map>
#include "src/binaryen-c.h"
int main() {
  std::map<size_t, BinaryenFunctionTypeRef> functionTypes;
  std::map<size_t, BinaryenExpressionRef> expressions;
  std::map<size_t, BinaryenFunctionRef> functions;
  std::map<size_t, RelooperBlockRef> relooperBlocks;
  BinaryenModuleRef the_module = NULL;
  RelooperRef the_relooper = NULL;
  the_module = BinaryenModuleCreate();
  expressions[size_t(NULL)] = BinaryenExpressionRef(NULL);
  {
    BinaryenIndex paramTypes[] = { 1, 1 };
    functionTypes[0] = BinaryenAddFunctionType(the_module, "iii", 1, paramTypes, 2);
  }
  expressions[1] = BinaryenGetLocal(the_module, 0, 1);
  expressions[2] = BinaryenGetLocal(the_module, 1, 1);
  expressions[3] = BinaryenBinary(the_module, 0, expressions[1], expressions[2]);
  {
    BinaryenType varTypes[] = { 0 };
    functions[0] = BinaryenAddFunction(the_module, "adder", functionTypes[0], varTypes, 0, expressions[3]);
  }
  BinaryenModulePrint(the_module);
(module
 (type $iii (func (param i32 i32) (result i32)))
 (memory $0 0)
 (func $adder (type $iii) (param $0 i32) (param $1 i32) (result i32)
  (i32.add
   (get_local $0)
   (get_local $1)
  )
 )
)
  BinaryenModuleDispose(the_module);
  functionTypes.clear();
  expressions.clear();
  functions.clear();
  relooperBlocks.clear();
```
Were the actual webassembly module ( in `wast` format ) is :
```
(module
 (type $iii (func (param i32 i32) (result i32)))
 (memory $0 0)
 (func $adder (type $iii) (param $0 i32) (param $1 i32) (result i32)
  (i32.add
   (get_local $0)
   (get_local $1)
  )
 )
)
```

## Running adder.wasm
Now lets actually write out the module and make it do something:
```c
#include "binaryen-c.h"
#include <stdio.h>

// "hello world" type example: create a function that adds two i32s and returns the result
int writeout(char buffer[],size_t size){
  FILE *fp;
  fp=fopen("adder.wasm", "wb");
  if(fp == NULL)
    return -1;
  fwrite(buffer, sizeof(buffer[0]), size, fp);
  fclose(fp);
  printf("Successfully written adder.wasm");
  return 0;
}

int main() {
  char buffer[1024];
  size_t size;

  //BinaryenSetAPITracing(1);
  BinaryenModuleRef module = BinaryenModuleCreate();
  // Creation a function type for  i32 (i32, i32)
  BinaryenType params[2] = { BinaryenInt32(), BinaryenInt32() };
  BinaryenFunctionTypeRef iii = BinaryenAddFunctionType(module, "iii", BinaryenInt32(), params, 2);
  // Get the 0 and 1 arguments, and add them
  BinaryenExpressionRef x = BinaryenGetLocal(module, 0, BinaryenInt32()),
                        y = BinaryenGetLocal(module, 1, BinaryenInt32());
  BinaryenExpressionRef add = BinaryenBinary(module, BinaryenAddInt32(), x, y);
  // Create the add function
  // Note: no additional local variables
  // Note: no basic blocks here, we are an AST. The function body is just an expression node.
  BinaryenFunctionRef adder = BinaryenAddFunction(module, "adder", iii, NULL, 0, add);

  // Export the function "adder" with name "adder"
  BinaryenAddExport(module, "adder", "adder");

  // Print it out
  BinaryenModulePrint(module);

   // Write to a buffer
  size = BinaryenModuleWrite(module, buffer, 1024);
  // Write to adder.wasm
  if (writeout(buffer,size) == -1)
    return -1;
  // Clean up the module, which owns all the objects we created above
  BinaryenModuleDispose(module);
  return 0;
}
```

And here it is, working ( tested on Chrome Canary, so check your browser compatibility with WebAssembly ) :

Write two numbers in the boxes below and then click on `=` to show the result.

<input id="arg1" style="text-align: right;" placeholder="integer" size="7" />
<span>+</span>
<input id="arg2" style="text-align: right;" placeholder="integer" size="7" />
<input type="submit" value="=" onclick="showRes()"/>
<i id="result">integer</i>

For completenness, here's the javascript to load and use the module:
```js
var wasmexports;
fetch('/js/adder.wasm').then(response =>
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
  document.getElementById("result").innerText = wasmexports.adder(a,b)
}
```
<script>
    var wasmexports;
    fetch('/js/adder.wasm').then(response =>
        response.arrayBuffer()
        ).then(bytes =>
        WebAssembly.instantiate(bytes)
        ).then(results => {
            wasmexports = results.instance.exports
    });
    function showRes(){
        var a = document.getElementById("arg1").value
        var b = document.getElementById("arg2").value
        document.getElementById("result").innerText = wasmexports.adder(a,b)
    }
</script>

This blog is hosted on github, so you can find the wasm file [here](https://github.com/stisa/stisa.github.io/tree/master/js/adder.wasm) and the `markdown` file [here](https://github.com/stisa/stisa.github.io/tree/master/_posts/2017-03-20-binaryen-windows-example.md).