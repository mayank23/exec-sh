# exec-sh

[![NPM](https://nodei.co/npm/exec-sh.png)](https://nodei.co/npm/exec-sh/)
[![NPM Downloads](https://img.shields.io/npm/dm/exec-sh.svg)](https://www.npmjs.com/package/exec-sh)

> Execute shell command forwarding all stdio streams.

## Features

exec-sh is a wrapper for [`child_process.spawn`](http://nodejs.org/api/child_process.html#child_process_child_process_spawn_command_args_options) with some improvements:

- Cross platform command execution:
  - Windows: `cmd /C COMMAND`
  - others: `sh -c COMMAND`
- Fowrards all stdio streams to current terminal (by default):
  - `execSh("bash")`
  - `execsh("echo -n Say: && read i && echo Said:$i")`
- stdout and stderr are passed to callback when available
  - `execSh("pwd", console.log)`

## Showcase
```javascript
// JavaScript

execSh("echo hello exec-sh && bash", { cwd: "/home" }, function(err){
  if (err) {
    console.log("Exit code: ", err.code);
  }
});
```

```sh
# Terminal output: interactive bash session

hello exec-sh
bash-3.2$ pwd
/home
bash-3.2$ exit 99
exit
Exit code:  99
```

## Usage

```javascript
const execSh = require("../");

// run interactive bash shell
execSh("echo lorem && bash", { cwd: "/home" }, (err) => {
  if (err) {
    console.log("Exit code: ", err.code);
    return;
  }

  // collect streams output
  const child = execSh(["bash -c id", "echo lorem >&2"], true,
    (err, stdout, stderr) => {
      console.log("error: ", err);
      console.log("stdout: ", stdout);
      console.log("stderr: ", stderr);
    });
});
```

## Promise Interface

```javascript
const execShPromise = require("exec-sh").promise;

// run interactive bash shell
const run = async () => {
  let out;

  try {
    out = await execShPromise('pwd', true);
  } catch (e) {
    console.log('Error: ', e);
    console.log('Stderr: ', e.stderr);
    console.log('Stdout: ', e.stdout);

    return e;
  }

  console.log('out: ', out.stdout, out.stderr);
}

run();
```

## Public API

### `execSh(command, [options], [callback])`

Execute shell command forwarding all stdio.

**Parameters:**

- `command {String|Array}` - The command to run, or array of commands
- `[options] {Object|TRUE}` - Options object passed directly to [`child_process.spawn`](http://nodejs.org/api/child_process.html#child_process_child_process_spawn_command_args_options), when `TRUE` then `{ stdio: null }` used
- `[callback] {Function}` - `callback(err, stdout, stderr)`
 - `err {Error|NULL}` - Error object. Has `code` property containing last command exit code when available
 - `stdout {String|NULL}` - aggregated stdout or `NULL` if not available
 - `stderr {String|NULL}` - aggregated stderr or `NULL` if not available

**Return Values:**

Returns [ChildProcess](http://nodejs.org/api/child_process.html#child_process_class_childprocess) object.

## Scripts

- `npm test` - run tests
- `npm run jsdoc` - build jsdoc
- `npm run dev` - run tests continuously

## License

The MIT License (MIT)
