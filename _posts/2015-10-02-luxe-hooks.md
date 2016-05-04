---
layout: post
date: 2015-10-02T22:36:29+02:00
draft:  false
title: "Luxe/Flow Hooks Example"
tags: [flow, hooks, luxe, example, haxe]
---

I'm playing around with [luxe](http://luxeengine.com/), building a simple depth first search-based maze generator, which I plan on turning into a full game ( btw you can try it [here](http://stksa.space/mazes/) ).  
While iterating, I had to copy-paste the content of `bin/web/` to the folder linked with the project [git repo](https://github.com/stisa/mazes), but it got old fast, and I **had** to find a faster way.    
At first, I tried using the  `--output-path <path>` flag in `flow`, that overrides the flow tree `project.app.output` path. The problem with this method is that flow generates two folders, `web` and `web.build`, inside your selected path, and I didn't like it very much.  
That's when I found out about hooks, digging trough `flow` source code.

### Hooks

flow has the ability of launching node.js scripts before ( **pre** ) and after ( **post** ) building and running your project.  
This gave me the option of using a simple script to copy the output of the build to another folder, without having to copy-paste manually every time.

To use hooks, you have to add them to your `project.flow` :

``` config
  build : {
    dependencies : {
      //your dependencies
    },
    post : {  
      priority : 1,
      name : 'copy-to-gh-pages',
      desc : 'copy bin/web to gh-pages',
      script : './hooks/post.js',
    }
  }
```
- `post` is when to run the hook
- `name` is the name of your hook
- `desv` is the description for your hook
- `script` is the path to your script, relative to your project folder root

Then you have to write your script, in my case `post.js`:

``` js
  var fs = require('fs-extra');
  var path = require('path');

  exports.hook = function(flow, done){
    // generic json reading function
    function read_json (file_path) {
        var temp;
        //console.log(path.resolve(file_path))
        temp = fs.readFileSync(path.resolve(file_path));
        return JSON.parse(temp);
    };

    //read our config file
    var config_file = "./hooks/config.json";

    var config = read_json(config_file);
    var dirs = config.dirs;

    console.log("copy-to-gh-pages - Source dir: "+ dirs.bin+", Target dir: "+ dirs.gh_pages);
    fs.copy(dirs.bin,dirs.gh_pages, function copy_site(err){
      if (err) throw err;
      console.log("copy-to-gh-pages - Project copied");
      console.log("copy-to-gh-pages - Copying git_files...");
      fs.readdir(dirs.git_files,function copy_git_files(err,files){
        if (err) throw err;
        for (var i = 0; i<files.length;i++) {
          var file = files[i];
          console.log("copy-to-gh-pages -  Copied "+file);
          fs.copy(path.join(dirs.git_files,file),path.join(dirs.gh_pages,file), function copy_file(err){
            if (err) throw err;
          })
        } // for
        done();

      })  // readdir
    })   // copy
  }

```

The important bits here are :

``` js
  exports.hook = function(flow, done){
    // do something here...

    // when you're finished, just say it:
    done();

  }
```

The `exports.hook` part allows `flow` to call our hook, passing it a `flow` object and the `done` function.  
Calling `done()` signals the build tool that our script is finished executing and that it can go on.

And that's it, just `flow run <target>` your project and watch as your hook does its thing.

Thanks for reading this, and if you find errors or if I got something wrong, please [open an issue on github](https://github.com/stisa/stisa.github.io)
