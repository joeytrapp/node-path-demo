# Use NODE_PATH to alter load path

From this question in JSJabber forum (paid membership required)

*tl;dr*

Modifying the `$NODE_PATH` env var will change where node looks for code when using `require()`. The startup script in `bin/start` will set the `$NODE_PATH` to include `./lib` so that you can write modules locally, but require them without relative paths.

One downside to this approach is that these internal modules can't have their own package.json files (well they can, but the modules won't be installed when running `npm install`).

http://lostechies.com/derickbailey/2014/02/20/how-i-work-around-the-require-problem-in-nodejs/

https://gist.github.com/branneman/8048520

The following is an example `package.json` that does something similar, and does install npm packages from local modules, but is confusing.

```
{
  "name": "name",
  "version": "0.0.1",
  "description": "desc",
  "main": "index.js",
  "scripts": {
    "test": "make test",
    "preinstall": "find vendor -type d -maxdepth 1 | tail -n 2 | cut -c8- | xargs -L 1 -I {} rm -f node_modules/{}",
    "postinstall": "find vendor -type d -maxdepth 1 | tail -n 2 | cut -c8- | xargs -L 1 -I {} sh -c 'ln -s \"$(pwd)/vendor/{}\" node_modules/{}; cd vendor/{} && npm install'"
  },
  "author": "",
  "license": "MIT",
  "devDependencies": {
  },
  "dependencies": {
  }
}
```

This is creating symlinks for the local modules inside of `ROOT/node_modules` and running `npm install` for each one after the user run `npm install` from the root.
