![](https://github.com/mayankchoubey/deno-in-2-hours/blob/96aee24246f2b2c74ad3d319225f2b5ccf756c21/cover-deno-in-2-hours.png)


> Learn about Deno core and standard library in just 2 hours

- [Introduction](#introduction)
- [Installation](#installation)
- [Upgrade](#upgrade)
- [Commands](#commands)
- [Imports](#imports)
- [Third-party packages](#third-party-packages)
- [Version, build, paths and other info](#version-build-paths-and-other-info)
- [Permissions](#permissions)
- [Command line args](#command-line-args)
- [Environment variables](#environment-variables)
- [Reader, Writer, Seeker, and Closer](#reader-writer-seeker-and-closer)
- [Built-in resources](#built-in-resources)
- [File ops](#file-ops)
- [File system ops](#file-system-ops)
- [Buffer ops](#buffer-ops)
- [ReadAll, WriteAll, and Iter](#readall-writeall-and-iter)
- [Network ops](#network-ops)
- [Paths](#paths)
- [System info](#system-info)
- [Encoding and decoding](#encoding-and-decoding)
- [Hash and UUID](#hash-and-uuid)
- [Web Crypto APIs](#web-crypto-apis)
- [Web Storage APIs](#web-storage-apis)
- [Process ops](#process-ops)
- [Child Process](#child-process)
- [User interaction](#user-interaction)
- [Utilities](#utilities)
- [Logger](#logger)
- [Metrics](#metrics)
- [Unit testing](#unit-testing)
- [Deno specific errors](#deno-specific-errors)


## Introduction
Deno is a simple, modern, and secure runtime for JavaScript and TypeScript applications that uses Google’s V8 engine and is built in Rust. Deno is a complete toolchain that ships as a single executable with no dependencies. It runs in a sandbox and supports Typescript out the box. Deno `1.0` was generally available in May'2020. This guide was written to match version `1.11.1` of Deno.

## Releases
Deno has an aggressive release schedule. There is a minor release almost every week. Every release contains a large number of features and bug fixes.

- Visit https://github.com/denoland/deno/releases to get detailed information on Deno's releases

## Installation
Deno installs as a single executable with no dependencies. It can be easily installed either directly or using package managers on all the supported platforms: Mac, Linux, and Windows.

- Use `curl/shell` or `powershell` commands to install on Mac/Linux or Windows

`curl -fsSL https://deno.land/x/install/install.sh |  sh` 

`iwr https://deno.land/x/install/install.ps1 -useb | iex`

An installation error may come if `unzip`is not installed on Mac or Linux. To install `unzip` use the following command:

|OS|Command  |
|--|--|
|Mac  |`brew install unzip` 
|Linux|`apt-get install unzip -y`

- Use the following OS-specific package manager to install Deno

|OS|Installation command  |
|--|--|
|Mac  | `brew install deno` |
|Windows|`choco install deno`|
|Windows|`scoop install deno`

## Upgrade
As Deno is a complete toolchain within a single executable, the upgrade manager is also available through a built-in command present in the Deno executable.

- Use `deno upgrade` to upgrade Deno to either latest or a specific version

|Upgrade To|Command  |
|--|--|
|Latest  |`deno upgrade`  |
|A particular version|`deno upgrade --version 1.8.3`

## Commands
Deno provides all its functionality through a large number of commands. The commands range from running the application, unit testing, upgrading, formatting, bundling, etc.

|Command|Use  |
|--|--|
|`run`  |Executes application
|`fmt`|Fixes indentation
|`types`|Prints runtime TypeScript declarations
|`cache`|Caches and compiles remote dependencies recursively
|`coverage`|Prints coverage report from coverage profiles
|`info`|Prints information about a module, dependencies, caches, etc.
|`eval`|Evaluates a small piece of code
|`repl`|Runs a read, evaluate, and print loop
|`bundle`|Prepares a single Javascript file for the application including all the dependencies
|`install`|Installs a script and its dependencies as a single executable
|`completions`|Prints completion code for specified shell
|`test`|Runs unit tests specified in `*test*.ts` files
|`upgrade`|Upgrades Deno to latest or specified release
|`doc`|Prints documentation for modules, functions, etc.
|`lint`|Runs a linter
|`compile`|Compiles the given script and its dependencies into a self-contained executable
|`lsp`|For interaction with editors and IDEs

Some of the commonly used commands:

- Use `run` command to run an application:
```ts
deno run --allow-read --allow-write --allow-net app.ts
//Server listening on http://localhost:8080
```

- Use `test` command to run the unit tests (this runs all the `*test.ts` files present in the local directory):
```ts
deno test --allow-all
//test result: ok. 2 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out (118ms)
```

- Use `eval` command to quickly run a piece of code (use `-p` to print the output of the expression on the console):
```ts
deno eval "'hello world'" -p
//hello world
```

- Use `fmt` command to fix indentation issues (it overwrites the original file)
```ts
//Before app.ts
import {abc,def} from "https://some.server.com";
const o={a:1, b:2, c:'hello'}, a=[1,2,3];
for(const k in o)
	console.log(k);
for(const k of a)
	console.log(k);
//--
deno fmt app.ts
//After app.ts
import { abc, def } from "https://some.server.com";

const o = { a: 1, b: 2, c: "hello" }, a = [1, 2, 3];
for (const k in o) {
  console.log(k);
}
for (const k of a) {
  console.log(k);
}
```

- Use `cache` to download and cache all the dependencies
```ts
//app.ts
import { doze } from "https://deno.land/x/doze/mod.ts";
//--
deno cache app.ts
Download https://deno.land/x/doze/mod.ts
Warning Implicitly using latest version (1.0) for https://deno.land/x/doze/mod.ts
Download https://deno.land/x/doze@1.0/mod.ts
Download https://deno.land/x/doze@1.0/doze.ts
Check file:///private/var/tmp/myDir/app.ts
```
- Use `info` to get detailed information about the module and it's dependencies
```ts
deno info app.ts
local: /private/var/tmp/myDir/app.ts
type: TypeScript
emit: /Users/mayankc/Library/Caches/deno/gen/file/private/var/tmp/myDir/app.ts.js
dependencies: 2 unique (total 552B)

file:///private/var/tmp/myDir/app.ts (191B)
└─┬ https://deno.land/x/doze@1.0/mod.ts (25B)
  └── https://deno.land/x/doze@1.0/doze.ts (336B)
```
- Use `bundle` to create a single JS file containing the module and all it's dependencies (useful in distribution)
```ts
//app1.ts
export const HW="Hello from app1";
//app2.ts
export const HW="Hello from app2";
//app.ts
import * as one from "./app1.ts";
import * as two from "./app2.ts";
const HW='Hello from app';
console.log(HW, one, two);
//--
deno bundle app.ts
//bundled output
const HW = "Hello from app1";
const mod = function() {
    return {
        HW: HW
    };
}();
const HW1 = "Hello from app2";
const mod1 = function() {
    return {
        HW: HW1
    };
}();
const HW2 = 'Hello from app';
console.log(HW2, mod, mod1);
```
- Use `compile` to build a self-contained executable for a module and all its dependencies
```ts
deno compile app.ts
Check file:///private/var/tmp/myDir/app.ts
Bundle file:///private/var/tmp/myDir/app.ts
Compile file:///private/var/tmp/myDir/app.ts
Emit app
./app
//Hello from app { HW: "Hello from app1" } { HW: "Hello from app2" }
```
- Use `install` to install an app as a self-contained executable at the specified path
```ts
deno install -f --root /tmp -n app app.ts
✅ Successfully installed app
/private/tmp/bin/app
```

## Imports
Deno supports only the ES6 style imports (`import`). It doesn't support commonjs style of importing (`require`).

- Use `import` statement to statically import modules
```ts
import { serve } from "https://deno.land/std/http/server.ts";
import * as path from "https://deno.land/std/path/mod.ts";
```
- Use `import` function to dynamically import modules at runtime
```ts
let serveFunc;
if(true /*some condition*/) {
    const {serve}=await import("https://deno.land/std/http/server.ts");
    serveFunc=serve;
} else {
    serveFunc=/*some other dynamic import*/
}
serveFunc(5000);
```

## Third-party packages
Deno doesn't have a package manager (the only missing piece in the complete toolchain). Instead, it supports downloading and importing packages/modules from anywhere (local or remote). There are three sources of third-party packages:

- *Deno's standard library*: This isn't really a third-party package, but it isn't included in Deno by default as well (`https://deno.land/std/`);
- *Deno aware third-party packages*: A large number of third-party packages are registered with Deno. They are made available through Deno's URL rewriting service (`https://deno.land/x/`);
- *Other third-party packages*: The remaining ones are any third-party packages that are hosted anywhere in the world (GitHub, enterprise or personal web server, any CDN, etc.)

There is an easy way to maintain all the package versions in a common place (like package.json).

- Use `deps.ts` to maintain package/module versions in a common place
```ts
//deps.ts
export * as path from "https://deno.land/std@0.90.0/path/mod.ts"; //A module from standard library
export * as doze from "https://deno.land/x/doze@1.0/mod.ts"; //A deno aware third-party module
export * as bodyparser from "https://raw.githubusercontent.com/mayankchoubey/deno-body-parser/1.0.0/mod.ts"; //Some other third party module
```
- Use `deps.ts` to import all the packages (do not import them directly)
```ts
//app.ts
import { path, doze, bodyparser } from "./deps.ts";
.....
```

## Version, build, paths and other info
- Use `Deno.version` object to get the versions of Deno, Typescript, and V8
```ts
Deno.version;
//{ deno: "1.11.1", v8: "9.1.269.35", typescript: "4.3.2" }
```
- Use `Deno.build` to get information about the Deno build being used
```ts
Deno.build;
//{target: "x86_64-apple-darwin", arch: "x86_64", os: "darwin", vendor: "apple", env: undefined}
```
- Use `Deno.execPath` function to get Deno's executable path
```ts
Deno.execPath();
///usr/local/bin/deno
```
- Use `import.meta` to get information on the main module (main will be false for workers):
```ts
import.meta;
//{ url: "file:///Users/mayankc/Work/source/denoExamples/a.ts", main: true }
```

## Permissions
Deno runs in a sandboxed environment. Unless explicitly enabled, there is no file, network, environment, or child process access. The permissions can be enabled through `--allow-XXX` command-line option. A `PermissionDenied` error would be raised at __runtime__ if the user program tries to access a resource outside the sandbox.

|Permission Flag|Type|Description  |Examples|
|--|--|--|--|
|`--allow-all`  |Boolean|Enables all access (no sandboxing)  |`deno run --allow-all app.ts`
|`--allow-read`|List|Recurisvely enables read access to all (if list is empty) or given list of paths|`deno run --allow-read app.ts` or `deno run --allow-read=/var/,/tmp,/datastore app.ts`
|`--allow-write`|List|Recurisvely enables write access to all (if list is empty) or given list of paths|`deno run --allow-write app.ts` or `deno run --allow-write=/var/,/tmp,/datastore app.ts`
|`--allow-net`|List|Enables access to all (if list is empty) or given networks|`deno run --allow-net app.ts` or `deno run --allow-net=10.1.1.0,api.abc.com,api.xyz.com,internal.system.com app.ts`
|`--allow-env`|List|Enables access to all (if list is empty) or given environment variables|`deno run --allow-env app.ts` or `deno run --allow-env=ENV1,ENV2 app.ts`
|`--allow-run`|List|Enables spawing all (if list is empty) or given child processes|`deno run --allow-run app.ts` or `deno run --allow-run=cat,ls,deno app.ts`
 |`--allow-hrtime`|Boolean|Enables high resolution time measurements|`deno run --allow-hrtime app.ts`


## Command line args
- Use `Deno.args` object to get the command-line arguments passed to the application (array of strings). All the command line args are copied as is (no parsing is done):
```ts
deno run a.ts -a 1 -b 2 -c d -e -f -g
//["-a", "1",  "-b", "2",  "-c", "d", "-e", "-f", "-g"]
```
- Use standard library's `flags` module's `parse` function to parse command-line args into usable format (uncategorized args go into `_`)
```ts
import { parse } from "https://deno.land/std/flags/mod.ts";
parse(Deno.args);
//deno run a.ts -a 1 -b 2 -c d -e -f -g abcd x y z 1 2 4
//{ _: [ "x", "y", "z", 1, 2, 4 ], a: 1, b: 2, c: "d", e: true, f: true, g: "abcd" }
```

## Environment variables
`Deno.env` contains functions to get, set, and delete environment variables. 

- Use `Deno.env.get` to get a single environment variable
```ts
Deno.env.get('ENV1'); //VAR1
Deno.env.get('ENV999'); //undefined
```
- Use `Deno.env.toObject` to get all the environment variables in an object
```ts
Deno.env.toObject();
////{NVM_DIR: "/Users/mayankc/.nvm", XPC_SERVICE_NAME: "0", .... ....ENV1: "VAR2"}
```
- Use `Deno.env.set` to set an environment variable (overwritten if it exists)
```ts
Deno.env.set('ENV1', 'VAR2');
Deno.env.set('ENV3', 'VAR3');
```
- Use `Deno.env.delete` to delete an environment variable
```ts
Deno.env.delete('ENV2');
```

## Reader, Writer, Seeker, and Closer
Any resource in Deno can implement some or all of the available standard interfaces (it depends on the type of resource). This way resources can be accessed/modified through generic functions.

|Type|Functionality  |Functions|
|--|--|--|
|Readers  |These get data from the resource  |`read` and `readSync`
|Writers|These write data into the resource|`write` and `writeSync`
|Seekers|These support positioning of cursor for the next read operation|`seek` and `seekSync`
|Closer|Close the resource|`close`

Here are some common resources with their supported interfaces:

|Resource|Interfaces  |
|--|--|
|`stdin`  |reader, closer  |
|`stdout`|writer, closer|
|`stderr`|writer, closer|
|`File`|reader, writer, seeker, closer|
|`Buffer`|reader, writer|
|`Conn`|reader, writer, closer|

- Use `Deno.read/Deno.readSync` to get a chunk of data from any resource implementing reader interface:
```ts
const buf=new Uint8Array(5);
const file=await Deno.open('/var/tmp/a.txt');
await Deno.read(file.rid, buf);
//buf: Uint8Array(5) [ 49, 50, 51, 52, 53 ]
await Deno.read(Deno.stdin.rid, buf);
//User input: ABCD
//buf: Uint8Array(5) [ 65, 66, 67, 68, 10 ]
```
- Use `Deno.write/Deno.writeSync` to write a chunk of data to any resource implementing writer interface:
```ts
const buf=new Uint8Array(5).fill(65);
const file=await Deno.open('/var/tmp/a.txt', {write: true});
await Deno.write(file.rid, buf); //cat /var/tmp.txt -> AAAAA67890
await Deno.write(Deno.stderr.rid, buf); //AAAAA
```
- Use `Deno.seek/Deno.seekSync` to move cursor to any position in any resource implementing seeker interface:
```ts
await Deno.seek(file.rid, 6, Deno.SeekMode.Start);
await Deno.seek(file.rid, 6, Deno.SeekMode.Current);
await Deno.seek(file.rid, -6, Deno.SeekMode.End); //minus to move back
```
- Use `Deno.copy` to copy all the data from a reader to a writer (file, socket, etc.)
```ts
//cat /var/tmp/a.txt -> AAAAA67890
const file=await Deno.open('/var/tmp/a.txt');
await Deno.copy(file, Deno.stdout);
//AAAAA67890
```
- Use `Deno.close` to close any resource implementing closer interface:
```ts
Deno.close(file.rid);
Deno.close(Deno.stdin.rid); //no more input needed
```


## Built-in resources
Deno treats files, streams, sockets, etc. as resources. When a resource is opened (like file or socket), a unique resource id (`number`) is allocated. Deno comes with three built-in resources (created and available by default):

|Resource|Rid|Type|
|--|--|--|
|`stdin`  |0|read
|`stdout`|1|write
|`stderr`|2|write

Depending on the resource type, it implements the reader or writer interface. Therefore, the built-in resources can be read or written directly.

```ts
await Deno.stdout.write(new TextEncoder().encode('abcd'));
await Deno.stderr.write(new TextEncoder().encode('Error occured:xyx'));
const buf=new Uint8Array(10);
await Deno.stdin.read(buf); //reads input till user presses enter
```

## File ops
Deno supports two types of file operations:
- __Low level__: User takes care of opening, reading, writing, seeking, and closing the file (Useful in reading big files)
- __High level__: User reads or writes the entire file directly (Deno takes care of opening, closing, seeking, etc.)

Here are the file ops, both low-level and high-level:

 - Use `Deno.open/Deno.openSync` to open a file and get a file object
```ts
const file=await Deno.open('/var/tmp/a.txt');
```
- Use `Deno.create/Deno.createSync` to create a file if it doesn't exists or truncates if it exists, open it, and get a file object
```ts
const file=await Deno.create('/var/tmp/a.txt');
```
 - Use `read/readSync` to read a block of data from the opened file starting from the current cursor position
```ts
const file=await Deno.open('/var/tmp/a.txt');
const buf=new Uint8Array(1000);
await file.read(buf); //or Deno.read(file.rid, data)
```
- Use `write/writeSync` to write a block of data into the opened file starting at the current cursor position
```ts
const file=await Deno.open('/var/tmp/a.txt', {create:true, write:true});
await file.write(new TextEncoder().encode('abcd')); //or Deno.write(file.rid, data);
```
- Use `seek/seekSync` to move the cursor by an offset from a specified cursor position. There are three seek positions to offset from: Start, End, Current. A negative offset is required to move back.
```ts
const file=await Deno.open('/var/tmp/a.txt', {create:true, write:true});
const data=new TextEncoder().encode('abcd');
await file.write(data); //abcd
await file.seek(4, Deno.SeekMode.Start);
file.writeSync(data); //abcdabcd
await file.seek(4, Deno.SeekMode.Current);
await file.write(data); //abcdabcdabcd
await file.seek(-4, Deno.SeekMode.End); //move back
await file.write(data); //abcdabcdabcd
```
- Use `close` to close a resource
```ts
file.close(); //or, Deno.close(file.rid)
```
- Use `Deno.writeFile/Deno.writeFileSync` to write a Uint8Array into a file (open/create, close taken care by Deno)
```ts
await Deno.writeFile("/var/tmp/a.txt", new TextEncoder().encode("ABCD"));
```
- Use `Deno.writeTextFile/Deno.writeTextFileSync` to write a string into a file (open/create, close taken care by Deno)
```ts
await Deno.writeTextFile("/var/tmp/a.txt", "ABCD");
```
- Use `Deno.readFile/Deno.readFileSync` to read entire file into a Uint8Array (open, close taken care by Deno)
```ts
await Deno.readFile("/var/tmp/a.txt");
//Uint8Array(4) [ 65, 66, 67, 68 ]
```
- Use `Deno.readTextFile/Deno.readTextFileSync` to read entire file into a string (open, close taken care by Deno)
```ts
await Deno.readTextFile("/var/tmp/a.txt");
//ABCD
```

## File system ops
Deno comes with several useful functions to work with the file system. These are different from working on the files (file ops).

- Use `Deno.watchFs` to get notifications for any changes in the observed paths (recursive by default). This functions returns an object that implements AsyncIterator.
```ts
const watcher=Deno.watchFs(["/var/tmp/", "./"]);
for await(const event of watcher)
    console.log(event); //process event
//----
touch file1.txt
//{ kind: "create", paths: [ "/Users/mayankc/Work/source/denoExamples/file1.txt" ] }
rm file1.txt
//{ kind: "remove", paths: [ "/Users/mayankc/Work/source/denoExamples/file1.txt" ] }
echo "abcd" > file1.txt
//{ kind: "create", paths: [ "/Users/mayankc/Work/source/denoExamples/file1.txt" ] }
//{ kind: "modify", paths: [ "/Users/mayankc/Work/source/denoExamples/file1.txt" ] }
```
- Use `Deno.chmod/Deno.chmodSync` to update permissions of a given path (Input is in format 0oXXX where X is a digit)
```ts
await Deno.chmod("/var/tmp/a.txt", 0o400);
//ls -ltr /var/tmp/a.txt 
//-r--------  1 mayankc  wheel  4 Apr 12 23:43 /var/tmp/a.txt
```
- Use `Deno.chown/Deno.chownSync` to update ownership of a given path (Input is uid and gid of new owner)
```ts
await Deno.chown("/var/tmp/a.txt", 100, 200);
//ls -ltr /var/tmp/a.txt 
//-r--------  1 guest  games  4 Apr 12 23:43 /var/tmp/a.txt
```
- Use `Deno.copyFile/Deno.copyFileSync` to make a copy of a file (permissions are also copied). If copied file name exists, it fails.
```ts
await Deno.copyFile("/var/tmp/a.txt", "/var/tmp/b.txt");
//-r--------  1 mayankc  wheel   4 Apr 12 23:43 /var/tmp/b.txt
//-r--------  1 mayankc  wheel   4 Apr 12 23:43 /var/tmp/a.txt
```
- Use `Deno.cwd` to get the current working directory
```ts
Deno.cwd();
///Users/mayankc/Work/source/denoExamples
```
- Use `Deno.chdir` to change the current working directory
```ts
Deno.chdir('/tmp');
Deno.cwd();
// /tmp
```
- Use `Deno.mkdir/Deno.mkdirSync` to create a directory at the specified path (throws error if directory already exists):
```ts
await Deno.mkdir("/tmp/1");
// ls -F /tmp/ -> 1/
```
- Use `Deno.readDir/Deno.readDirSync` to read the contents of a given directory and get an AsyncIterator to process each entity (name, type, link):
```ts
//ls /var/tmp/myDir -> file1	file2	myDir2/
for await (const dirEntry of Deno.readDir("/var/tmp/myDir"))
    dirEntry; //do something with the contents
//{ name: "file2", isFile: true, isDirectory: false, isSymlink: false }
//{ name: "file1", isFile: true, isDirectory: false, isSymlink: false }
//{ name: "myDir2", isFile: false, isDirectory: true, isSymlink: false }
```
- Use `Deno.rename/Deno.renameSync` to rename a given path (file or directory):
```ts
await Deno.rename("/var/tmp/myDir", "/var/tmp/myDir1");
//ls -F /var/tmp/
//....... myDir1/ .......
```
- Use `Deno.stat/Deno.statSync` to get stats of a file or a directory (symlinks are followed)
```ts
await Deno.stat('/var/tmp/myDir1');
//{isFile: false, isDirectory: true, isSymlink: false, size: 160, mtime: 2021-04-14T03:47:47.471Z, atime: 2021-04-14T03:59:58.937Z, birthtime: 2021-04-14T03:47:27.678Z, dev: 16777220, ino: 82623212, mode: 16877, nlink: 5, uid: 501, gid: 0, rdev: 0, blksize: 4096, blocks: 0}
```
- Use `Deno.lstat/Deno.lstatSync` to get stats of a symlink instead of following it
```ts
await Deno.lstat('/var');
//{isFile: false, isDirectory: false, isSymlink: true, size: 11, mtime: 2019-10-17T20:46:36.987Z, atime: 2019-10-17T20:46:36.987Z, birthtime: 2019-10-17T20:46:36.987Z, dev: 16777220, ino: 1152921500312394500, mode: 41453, nlink: 1, uid: 0, gid: 80, rdev: 0, blksize: 4096, blocks: 0}
```
- Use `Deno.truncate/Deno.truncateSync` to truncate a file from given length if specified, entire file if not specified
```ts
//echo "abcdefgh" > /var/tmp/myDir1/file1
//cat /var/tmp/myDir1/file1 -> abcdefgh
await Deno.truncate('/var/tmp/myDir1/file1', 5);
//cat /var/tmp/myDir1/file1 -> abcde
await Deno.truncate('/var/tmp/myDir1/file1');
//cat /var/tmp/myDir1/file1 -> 
```
- Use `Deno.link/Deno.linkSync` to creates a hard link (mirror copy)
```ts
//echo "123" > fileXYZ.txt
await Deno.link("./fileXYZ.txt", "./fileABC.txt");
//ls ./ -> ..... fileXYZ.txt fileABC.txt .....
```
- Use `Deno.symlink/Deno.symlinkSync` to create a symbolic link (like a pointer)
```ts
//echo "123" > fileXYZ.txt
await Deno.symlink("./fileXYZ.txt", "./fileABC.txt");
//ls -ltr -> .... fileXYZ.txt fileABC.txt -> ./fileXYZ.txt ....
```
- Use `Deno.readLink/Deno.readLinkSync` to get the destination of a symbolic link
```ts
await Deno.readLink('./fileABC.txt');
//./fileXYZ.txt
```
- Use `Deno.realPath/Deno.realPathSync` to get the full path for a relative path
```ts
await Deno.realPath('./fileXYZ.txt');
///Users/mayankc/Work/source/denoExamples/fileXYZ.txt
```
- Use `Deno.makeTempDir/Deno.makeTempDirSync` to create a directory with a random name (optionally specify the path, prefix, and suffix) at the default path:
```ts
await Deno.makeTempDir();
///var/folders/k0/3447gbp16vl309gg50ygclwr0000gn/T/0585d8f8
await Deno.makeTempDir({dir: '/var/tmp', prefix: 'ABCD', suffix: 'VXYZ'});
///var/tmp/ABCD5036d87bVXYZ
```
- Use `Deno.makeTempFile/Deno.makeTempFileSync` to create a file with a random name (optionally specify the path, prefix, and suffix) at the default path:
```ts
await Deno.makeTempFile();
///var/folders/k0/3447gbp16vl309gg50ygclwr0000gn/T/e1527deb
await Deno.makeTempFile({dir: '/tmp', pre ix: 'PREF_', suffix: '_SUFF'});
///tmp/PREF_8d5d5ce7_SUFF
```
- Use `Deno.remove/Deno.removeSync` to remove the path (set recursive to true if the path is a directory and it needs to be removed recursively)
```ts
await Deno.remove('/var/tmp/myDir1/file1');
await Deno.remove('/var/tmp/myDir1/fileNotThere'); //throws error
await Deno.remove('/var/tmp/myDir1'); //throws error - Directory not empty
await Deno.remove('/var/tmp/myDir1', {recursive: true});
```
- Use `Deno.fdatasync/Deno.fdatasyncSync` to immediately flush all the data operations pending on a file to the disk
```ts
await Deno.fdatasync(file.rid);
```
- Use `Deno.fsync/Deno.fsyncSync` to immediately flush all the metadata and data operations pending on a file to the disk
```ts
await Deno.fsync(file.rid);
```
- Use `Deno.utime/Deno.utime` to update the access and modification time of a given path (takes unix epoch or a date object)
```ts 
//-rw-r--r--  1 mayankc  wheel  11 Apr 15 12:55 /var/tmp/a.txt
await Deno.utime('/var/tmp/a.txt', new Date(), new Date());
//-rw-r--r--  1 mayankc  wheel  11 Apr 16 12:36 /var/tmp/a.txt
```
- Use standard library's `fs` module's `copy/copySync` functions to recursively copy a directory
```ts
//ls -FR dir1 -> dir2/	file3 dir1/dir2: file1	file2
import { copy } from "https://deno.land/std/fs/mod.ts";
await copy("/var/tmp/dir1", "/var/tmp/dir5");
//ls -FR dir5 -> dir2/	file3 dir5/dir2: file1	file2
```
- Use standard library's `fs` module's `emptyDir/emptyDirSync` functions to ensure an empty directory (if directory is not empty, it'd be emptied, or if directory doesn't exists, it'd be created)
```ts
import { emptyDir } from "https://deno.land/std/fs/mod.ts";
await emptyDir("/var/tmp/dir5");
//ls -ltr /var/tmp/dir5/ -> 
```
- Use standard library's `fs` module's `ensureDir/ensureDirSync/ensureFile/ensureFileSync` functions to create a directory structure (just like `mkdir -p`)
```ts
import { ensureDir, ensureFile } from "https://deno.land/std/fs/mod.ts";
await ensureDir("/var/tmp/dir5/dir6/dir7/dir8");
//ls -FR /var/tmp/dir5 -> dir6/ /var/tmp/dir5/dir6: dir7/ /var/tmp/dir5/dir6/dir7: dir8/
await ensureFile("/var/tmp/dir9/dir10/file11");
//ls -FR /var/tmp/dir9 -> dir10/ /var/tmp/dir9/dir10: file11
```
- Use standard library's `fs` module's `exists/existsSync` functions to check if a path exists (directory or file)
```ts
import { exists } from "https://deno.land/std/fs/mod.ts";
await exists("/var/tmp/dir9/dir10/file11"); //true
await exists("/var/tmp/dir5/dir6/dir7/dir8"); //true
await exists("/var/tmp/dir5/dir6/dir7/dir8/dir9"); //false
```
- Use standard library's `fs` module's `walk/walkSync` functions to recursively process every file/directory/symlink present inside a given path
```ts
import { walk } from  "https://deno.land/std/fs/mod.ts";
for await (const entry of walk("/var/tmp/dir1"))
    entry;
//{path: "/var/tmp/dir1", name: "dir1", isFile: false, isDirectory: true, isSymlink: false}
//{path: "/var/tmp/dir1/file3", name: "file3", isFile: true, isDirectory: false, isSymlink: false}
//{path: "/var/tmp/dir1/dir2", name: "dir2", isFile: false, isDirectory: true, isSymlink: false}
//{path: "/var/tmp/dir1/dir2/file2", name: "file2", isFile: true, isDirectory: false, isSymlink: false}
//{path: "/var/tmp/dir1/dir2/file1", name: "file1", isFile: true, isDirectory: false, isSymlink: false}
```

## Buffer ops
Buffer is a simple data storage that provides a way to store and retrieve data. Deno's buffer has a cursor to keep track of the next read/write operation. Buffer implements both reader and writer interfaces. It can grow as needed. Usually, Buffers are initialized from a data source like a file or socket or Uint8Array and then are read as needed. From v1.9.0, `Buffer` has been recommended to be used from the standard library. 
```ts
import { Buffer } from "https://deno.land/std/io/buffer.ts";
```
- Use `new Buffer`  to create a Buffer with default initial storage or from an optional `ArrayBuffer` for initialization
```ts
const buf=new Buffer();
const ab=new Uint8Array(10).fill(1);
const buf2=new Buffer(ab);
```
- Use `empty` to find out if buffer has been completely read
```ts
buf.empty(); //true
buf2.empty(); //false
```
- Use `length` to get the length of the unread part of the buffer
```ts
buf.length(); //0
buf2.length(); //8
```
- Use `capacity` to get the capacity of the buffer (i.e. total number of bytes present )
```ts
buf.capacity(); //0
buf2.capacity(); //8
```
- Use `bytes` to get the complete unread portion of the buffer
```ts
const buf=new Buffer(new Uint8Array(5).fill(1));
buf.bytes();
//Uint8Array(5) [ 1, 1, 1, 1, 1 ]
```
- Use `read/readSync` to reads bytes from buffer upto the length of input Uint8Array
```ts
const uint8 = new Uint8Array(20).fill(1);
const buf=new Buffer(uint8);
const readBuf=new Uint8Array(5);
await buf.read(readBuf); //5 bytes read
buf.length; //15 (unread bytes)
```
- Use `write/writeSync` to write an Uint8Array into the buffer
```ts
const buf=new Buffer(new Uint8Array(5).fill(1));
await buf.write(new Uint8Array(5).fill(2));
buf.bytes();
//Uint8Array(10) [1, 1, 1, 1, 1, 2, 2, 2, 2, 2]
```
- Use `readFrom/readFromSync` to read all the data from a `reader` and saves it into the buffer
```ts
const file=await Deno.open('/var/tmp/child.txt');
const buf=new Buffer();
await buf.readFrom(file);
buf.bytes();
//Uint8Array(12) [104, 101, 108, 108, 111,  32, 119, 111, 114, 108, 100,  10]
```

## ReadAll, WriteAll, and Iter
`readAll`, `writeAll`, and `iter` are three very commonly used functions in Deno. From v1.9.0, these functions have been recommended to be used from the standard library.

```ts
import { readAll, writeAll, iter } from "https://deno.land/std/io/util.ts";
```

- Use `readAll/readAllSync` to read everything from a reader (returns in an Uint8Array)
```ts
const file=await Deno.open('/var/tmp/child.txt');
const data=await readAll(file);
//Uint8Array(12) [104, 101, 108, 108, 111,  32, 119, 111, 114, 108, 100,  10]
```
- Use `writeAll/writeAllSync` to write everything present in an Uint8Array to the writer
```ts
const file=await Deno.open('/var/tmp/child.txt', {write: true});
await writeAll(file, new Uint8Array(5).fill(65));
//cat /var/tmp/child.txt -> AAAAA world
```
- Use `iter/iterSync` to create an asynchronous (or synchronous) iterator for a given reader. It reads in chunks with a default size unless specified in options.
```ts
const file=await Deno.open('/var/tmp/child.txt');
for await(const c of iter(file))
    c; //Uint8Array(12) [104, 101, 108, 108, 111,  32, 119, 111, 114, 108, 100,  10]
--
for await(const val of iter(file, {bufSize:6}))
    c;
//Uint8Array(6) [ 65, 65, 65, 65, 65, 32 ]
//Uint8Array(6) [ 119, 111, 114, 108, 100, 10 ]
```

## Network ops
Deno supports HTTP, TCP, UDP, and WebSocket client/servers. Some of the ops are a part of the core runtime, while others are implemented in standard library.

- Use `fetch` to fetch data through HTTP (aka HTTP client)
```ts
const res=await fetch("https://localhost:3000", {
    body: JSON.stringify({name: 'First Last'})
});
if(res.ok) {
    await res.text(); //String -> {"name":"First Last"}
    await res.json(); //JSON object -> { name: "First Last" }
    await res.formData(); //FormData -> FormData { [Symbol(data)]: [ [ "name", "First Last" ] ] }
}
```
- Use `Deno.listen/Deno.listenTls` to create a TCP server (For HTTP servers,  use `serve/serveTls` instead)
```ts
const listener = Deno.listen({ hostname: "127.0.0.1", port: 5566 });
const conn=await listener.accept();
await Deno.copy(conn, conn); //echo the data back conn -> conn
conn.close();
listener.close();
//--
const listener = Deno.listenTls({ hostname: "127.0.0.1", port: 5566, certFile: "./c.crt", keyFile: "./k.key" });
```
- Use `Deno.listenDatagram` to create a UDP server (UDP is bothways server)
```ts
const server = Deno.listenDatagram({ port: 3000, transport: "udp" });
while (true) {
  const [recvd, remote] = await server.receive();
  await server.send(recvd, remote); //echo it back
}
```
- Use `Deno.connect/Deno.connectTls` to create a TCP client without or with TLS
```ts
const conn=await Deno.connect({port: 4544 });
await conn.write(new TextEncoder().encode("ABCD"));
const buf=new Uint8Array(100);
const n=await conn.read(buf);
//buf: Uint8Array(4) [ 65, 66, 67, 68 ]
//--
const conn=await Deno.connectTls({hostname: 'localhost', port: 4544, certFile: './server.crt' });
```
- Use `Deno.serve/Deno.serveTls` to create a (scripted) HTTP server without or with TLS
```ts
import { serve } from "https://deno.land/std/http/server.ts";
for await (const req of serve({port:3000}))
    req; //do something with request 
//--
import { serveTLS } from "https://deno.land/std/http/server.ts";
for await (const req of serveTLS({port:3000, certFile: "./c.crt", keyFile: "./k.key"}))
    req; //do something with request
```
- Use `Deno.serveHttp` to create a native (hyper) HTTP server. The native HTTP server takes any TCP connection and serves HTTP over it. The native HTTP server supports web's standard Request & Response objects.
```ts
const listener = Deno.listen({port: 5000});
for await(const conn of listener)
    handleNewConnection(conn);
    
async function handleNewConnection(conn: Deno.Conn) {
    for await(const req of Deno.serveHttp(conn))
        req.respondWith(new Response());
}
```
- Use `Deno.resolveDns` to fetch a particular type of record (`A`/`AAAA`/`ANAME`/`CNAME`/`SRV`/`PTR`/`MX`/`TXT`) from the DNS
```ts
await Deno.resolveDns("www.facebook.com", "A"); //[ "69.171.250.35" ]
await Deno.resolveDns("www.facebook.com", "AAAA"); //[ "2a03:2880:f1ff:83:face:b00c:0:25de" ]
await Deno.resolveDns("www.facebook.com", "CNAME"); //[ "star-mini.c10r.facebook.com." ]
```
- Use standard library's `ws` module to create a websocket server. Websocket client follows the web standards.
```ts
//echoServer.ts
import { serve } from "https://deno.land/std/http/server.ts";
import { acceptWebSocket, isWebSocketCloseEvent } from "https://deno.land/std/ws/mod.ts";
for await (const req of serve(":5000")) {
    const { conn, r: bufReader, w: bufWriter, headers } = req;
    const ws=await acceptWebSocket({conn, bufReader, bufWriter, headers});
    for await(const e of ws) {
        if(isWebSocketCloseEvent(e))
            break;
        ws.send(e as string|Uint8Array); //e: SAMPLE DATA
    }
}
```
```ts
//echoClient.ts
const ws=new WebSocket('ws://localhost:5000');
ws.onopen=()=>ws.send("SAMPLE DATA");
ws.onmessage=(m)=>ws.close(); //m.data: SAMPLE DATA
ws.onclose=()=>console.log('Exited'); //Exited
```
- Use`Deno.shutdown` to close the socket
```ts
Deno.shutdown(conn.rid);
```

## Paths
Deno's standard library comes with a `path` module that contains several useful functions to parse, extract info, and build paths.

```ts
import * as path from "https://deno.land/std/path/mod.ts";
```

- Use `basename` to get the end of the path (could be directory or file)
```ts
path.basename("/var/tmp/a/b/d.txt"); //d.txt
path.basename("/var/tmp/a/b/"); //b
```
- Use `dirname` to get the path of the directory containing the basename of the path
```ts
path.dirname("/var/tmp/a/b/d.txt"); // /var/tmp/a/b
path.dirname("/var/tmp/a/b/"); // /var/tmp/a
```
- Use `extname` to get the extension of the path (return empty string if path's basename is a directory)
```ts
path.extname("/var/tmp/a/b/d.txt"); //.txt
path.extname("/var/tmp/a/b/"); //
```
- Use `fromFileUrl` to convert a `file://` path into a local path
```ts
path.fromFileUrl("file:///var/tmp/a/b/d.txt"); // /var/tmp/a/b/d.txt
path.fromFileUrl("file:///var/tmp/a/b"); // /var/tmp/a/b
```
- Use `isAbsolute` to find if a path is relative or absolute
```ts
path.isAbsolute("/var/tmp/a/b/d.txt"); //true
path.isAbsolute("./a/b"); //false
```
- Use `join` to combine paths into a single path (usually it'd be a base path and then paths relative to base paths)
```ts
path.join("/var/tmp/a", "/b/c.txt"); // /var/tmp/a/b/c.txt
path.join("./data/", "/d/e", "f.txt"); // data/d/e/f.txt
```
- Use `normalize` to convert any arbitrary relative path to a normal relative path (append relative paths containing `.` or `..` and then normalize it)
```ts
path.normalize("./a/b"); // a/b
path.normalize("../data/a/./b/c/../d/e"); // ../data/a/b/d/e
```
- Use `parse` to convert any path into a path object that is useful in getting all the useful info together like basename, dirname, extname, root
```ts
path.parse("./a/b/c"); //{ root: "", dir: "./a/b", base: "c", ext: "", name: "c" }
path.parse("/var/tmp/a/b/c/d/e.txt"); //{ root: "/", dir: "/var/tmp/a/b/c/d", base: "e.txt", ext: ".txt", name: "e" }
```
- Use `resolve` to convert relative (to `cwd`) path segments into a single absolute path
```ts
path.resolve("./a/b/c", "d/", "e.txt"); // /Users/mayankc/Work/source/denoExamples/a/b/c/d/e.txt
path.resolve("../../a/b/c", "d/e/f", "g/h", "i.txt"); // /Users/mayankc/Work/a/b/c/d/e/f/g/h/i.txt
```
- Use `toFileUrl` to convert a local path to a file URL `file://` (It returns a URL object, use `toString` to get a path)
```ts
console.log(path.toFileUrl("/var/tmp/a").toString()); //file:///var/tmp/a
console.log(path.toFileUrl("/data/a/b/c.txt").toString()); //file:///data/a/b/c.txt
```

## System info
> A lot of the system functions are under unstable umbrella
- Use `Deno.hostname` to get the name of the host where Deno is running
```ts
Deno.hostname(); //Mayanks-MacBook-Pro.local
```
- Use `Deno.consoleSize` to get rows and columns in the console window
```ts
Deno.consoleSize(); //{ columns: 67, rows: 15 }
```
- Use `Deno.loadavg` to get the system-wide load average numbers for 1, 5, and 15 minutes
```ts
Deno.loadavg(); //[ 1.92919921875, 2.50830078125, 2.3837890625 ]
```
- Use `Deno.osRelease` to get details about the operating system version
```ts
Deno.osRelease(); //19.6.0 (on mac)
```
- Use `Deno.systemMemoryInfo` to get detailed information about system-wide memory usage
```ts
Deno.systemMemoryInfo();
//{total: 8388608, free: 32220, available: 1862560, buffers: 0, cached: 0, swapTotal: 4194304, swapFree: 1158144}
```
- Use `Deno.systemCpuInfo` to get information about number of cores and their speed
```ts
Deno.systemCpuInfo(); //{ cores: 8, speed: 1400 }
```

## Encoding and decoding
- Use web's standard `TextEncoder` and `TextDecoder` to convert data from string to Uint8Array and vice versa. These are very useful in working with file, buffer, and socket ops.
```ts
new TextEncoder().encode('ABCD');
//Uint8Array(4) [ 65, 66, 67, 68 ]
new TextDecoder().decode(new Uint8Array([ 65, 66, 67, 68 ]));
//ABCD
```
- Use standard library's `encoding/base64` module's `encode/decode` functions to encode and decode base64 data
```ts
import { decode, encode } from "https://deno.land/std/encoding/base64.ts";
const e=encode("ABCDE");
const d=decode(e); //or, new TextDecoder().decode(decode(e)); to get string
//e: QUJDREU=     d: Uint8Array(5) [ 65, 66, 67, 68, 69 ]
```
- Use standard library's `encoding/base64url` module's `encode/decode` functions to encode and decode base64 URLs
```ts
import { encode, decode } from "https://deno.land/std/encoding/base64url.ts";
const e=encode(new TextEncoder().encode("https://abc.def.com/api/v1/a/b?x=1&y=2&z"));
const d=new TextDecoder().decode(decode(e));
//e: aHR0cHM6Ly9hYmMuZGVmLmNvbS9hcGkvdjEvYS9iP3g9MSZ5PTImeg
//d: https://abc.def.com/api/v1/a/b?x=1&y=2&z
```
- Use standard library's `encoding/hex` module's `encode/decode` functions to encode and decode hex data
```ts
import { encode, encodeToString, decode, decodeString } from "https://deno.land/std/encoding/hex.ts";
const data=new TextEncoder().encode("ABCD1234");
const e=encodeToString(data); //e: 4142434431323334
const f=encode(data); //f: Uint8Array(16) [52, 49, 52, 50, 52, 51, 52, 52, 51, 49, 51, 50, 51, 51, 51, 52]
const g=decode(f); //g: Uint8Array(8) [65, 66, 67, 68, 49, 50, 51, 52]
const h=new TextDecoder().decode(decodeString(e)); //h: ABCD1234
```

## Hash and UUID
- Use standard library's `hash` module's `createHash, update, toString` functions to create a hash of a given string
```ts
import { createHash } from "https://deno.land/std/hash/mod.ts";
createHash("md5").update("ABCD").toString(); //cb08ca4a7bb5f9683c19133a84872ca7
createHash("sha1").update("ABCD").toString(); //fb2f85c88567f3c8ce9b799c7c54642d0c7b41f6
createHash("sha256").update("ABCD").toString(); //e12e115acf4552b2568b55e93cbd39394c4ef81c82447fafc997882a02d23677
createHash("sha512").update("ABCD").toString(); //49ec55bd83fcd67838e3d385ce831669e3f815a7f44b7aa5f8d52b5d42354c46d89c8b9d06e47a797ae4fbd22291be15bcc35b07735c4a6f92357f93d5a33d9b
```
- Use standard library's `uuid` module to generate & validate v1, v4, and v5 UUIDs (v4 is completely random, v5 produces the same uuid if the same options are given)
```ts
import * as uuid from "https://deno.land/std/uuid/mod.ts";
uuid.v1.generate(); //ce750730-9f9d-11eb-b120-0738fc55abf6
uuid.v4.generate(); //770bb8bb-1caf-4f24-a473-e3ef83e791c8
uuid.v5.generate({value: "Hello, World", namespace: "1b671a64-40d5-491e-99b0-da01ff1f3341"}); //4b4f2adc-5b27-57b5-8e3a-c4c4bcf94f05
uuid.v1.validate("770bb8bb-1caf-4f24-a473-e3ef83e791c8"); //false
uuid.v4.validate("770bb8bb-1caf-4f24-a473-e3ef83e791c8"); //true
```


### Web Crypto APIs
Web crypto APIs implement hashing functions and a random number generator following web standards.

- Use `crypto.getRandomValues` function to generate cryptographically strong random numbers. This function fills the input array with 8/16/32 bit cryptographicall strong random numbers.
```ts
const rv=new Uint8Array(5);
crypto.getRandomValues(rv);
//rv: Uint8Array(5) [ 134, 11, 79, 126, 167 ]
const rv=new Uint16Array(5);
crypto.getRandomValues(rv);
//rv: Uint16Array(5) [ 63909, 62236, 9310, 49137, 60005 ]
const rv=new Uint32Array(5);
crypto.getRandomValues(rv);
//rv: Uint32Array(5) [ 1947225378, 753635950, 415007456, 3202401173, 1941195170 ]
```
- Use `crypto.randomUUID` to generate a V4 UUID
```ts
crypto.randomUUID(); //49d25949-7045-4bdd-a17b-a5b4e3f16be1
```
- Use `crypto.subtle.digest` to generate a secure hash (SHA). The supported algorithms are: SHA-1, SHA-256, SHA-384, and SHA-512.
```ts
const i=Uint8Array.from({length: 50}, () => Math.floor(Math.random() * 50));
new Uint8Array(await crypto.subtle.digest('sha-1', i));
//Uint8Array(20) [87, 55, 240, 228, 250, 122, 3, 204, 85, 72, 125,  85, 159, 198, 81, 239, 35, 254, 77, 27]
new Uint8Array(await crypto.subtle.digest('sha-256', i));
//Uint8Array(32) [197, 138, 201, 31, 201, 218, 169, 117, 12, 31,  16, 116, 230, 169, 44, 154, 108, 120, 28, 123, 64, 183, 162, 129, 196, 82, 114, 116, 245, 211, 116, 122]
new Uint8Array(await crypto.subtle.digest('sha-384', i));
//Uint8Array(48) [72, 183, 141, 243, 202, 146, 234, 12, 199, 105, 108, 145, 37, 170,  47, 121, 203, 166, 4, 170, 33, 1, 13, 33, 23, 243, 148, 58, 137, 107, 243, 74, 127, 49, 200, 62, 30,  62,  54,  63, 167, 251, 139, 102, 120, 17, 3, 7]
new Uint8Array(await crypto.subtle.digest('sha-512', i));
//Uint8Array(64) [114, 18, 60, 52, 57, 109, 160, 248, 102, 239, 118, 250, 222, 76, 187, 139, 250, 239, 232, 179, 31, 144, 192, 174, 39,  73, 17, 211, 237, 236,  93, 143, 12, 33, 234, 172, 70, 22, 96, 110, 120, 151, 207, 228, 250, 96, 114, 88, 158, 63, 122, 120, 198, 210, 221, 118, 28, 19, 99, 162, 145, 236, 182, 184]
```
- Use standard library's hex module's `encodeToString` to convert the raw output of `crypto.subtle.digest` to a string
```ts
import { encodeToString } from "https://deno.land/std/encoding/hex.ts";

encodeToString(new Uint8Array(await crypto.subtle.digest('sha-1', i)));
//df3ed451c9ea86ab10ff7583874ad1bd4813c3e0
encodeToString(new Uint8Array(await crypto.subtle.digest('sha-256', i)));
//c58ac91fc9daa9750c1f1074e6a92c9a6c781c7b40b7a281c4527274f5d3747a
encodeToString(new Uint8Array(await crypto.subtle.digest('sha-384', i)));
//48b78df3ca92ea0cc7696c9125aa2f79cba604aa21010d2117f3943a896bf34a7f31c83e1e3e363fa7fb8b6678110307
encodeToString(new Uint8Array(await crypto.subtle.digest('sha-512', i)));
//72123c34396da0f866ef76fade4cbb8bfaefe8b31f90c0ae274911d3edec5d8f0c21eaac4616606e7897cfe4fa6072589e3f7a78c6d2dd761c1363a291ecb6b8
```


### Web Storage APIs
Web storage APIs provide temporary and persistent storage of key-value pairs. Deno uses sqlitedb to store key-value pairs in memory (sessionStorage) or at disk (localStorage). SessionStorage's life is with process, while localStorage persists across process restarts.
- Use `sessionStorage.getItem`, `sessionStorage.setItem`, and `sessionStorage.removeItem` to get, set, and remove a KV from sessionStorage (temporary)
```ts
sessionStorage.setItem('A', 'B');
sessionStorage.setItem('F', JSON.stringify({a:1, b: true}));
sessionStorage.length; //2
sessionStorage.getItem('A'); //B
sessionStorage.getItem('F'); //{"a":1,"b":true}
sessionStorage.removeItem('A');
sessionStorage.removeItem('F');
```
- Use `localStorage.getItem`, `localStorage.setItem`, and `localStorage.removeItem` to get, set, and remove a KV from localStorage (persistent)
```ts
localStorage.setItem(`${Date.now()}`, 'B');
localStorage.length; //1
localStorage.key(0); //1620969933244
localStorage.setItem('A', 'B');
localStorage.getItem('A'); //B
localStorage.removeItem('A');
```

## Process ops
- Use `Deno.pid` and `Deno.ppid` variables to get current and parent's process IDs:
```ts
Deno.pid;
//89597
Deno.ppid;
//81921
```
- Use `Deno.exit` to abruptly exit the running process (an exit code is required)
```ts
//a.ts
setTimeout(() => {
    Deno.exit(1);
    console.log('Should never see this');
}, 5000);

shell > deno run a.ts
shell > //the line never gets printed
```
- Use `Deno.sleepSync` to synchronously sleep current thread for given milliseconds
```ts
Deno.sleepSync(100);
```
- Use standard library's `async` module's `delay` function to asynchronously sleep current thread for given milliseconds
```ts
import { delay } from "https://deno.land/std/async/mod.ts";
await delay(5000);
```
- Use `Deno.kill` to send a signal to a given process id
```ts
Deno.kill(pid, Deno.Signal.SIGINT);
```


## Child Process
`Deno.run()` can be used to start a child process to run a shell command, script, another Deno program, etc. While creating the child process, the input, output, and stderr of the child process can be set to: 

- `none`: Ignore
- `pipe`: Redirect to the parent process
- `inherit`: Use parent's process's settings (default)
- `number`: Redirect to given `rid`

The `Deno.run()` function call returns a process object. This process object can be used to check the status of the child process, get piped output, and terminate it if needed. The process object has a `status` function that would resolve when the child process is done.

- Use `Deno.run` to create a child process (stdin/out/err are inherited as they are unspecified)
```ts
const p=Deno.run({cmd: ['echo', 'hello' , 'world']});
await p.status();
//output on console: hello world
```
- Use `Deno.run` to create a child process and pipe the streams (stdin/out/err) to the parent
```ts
const p=Deno.run({cmd: ['echo', 'hello' , 'world'], stdout: 'piped', stderr: 'piped'});
await p.status();
const [s, e] = await Promise.all([
    p.output(),
    p.stderrOutput()]);
}
//s: hello world, e: undefined
```
- Use `Deno.run` to create a child process and redirect streams (out/err) to a file
```ts
const file=await Deno.open("/var/tmp/child.txt", {create: true, write: true});
const p=Deno.run({cmd: ['echo', 'hello' , 'world'], stdout: file.rid, stderr: file.rid});
await p.status();
file.close();
//cat /var/tmp/child.txt -> hello world
```
- Use `kill` to terminate a child process (say it's not responding) 
```ts
const p=Deno.run({cmd: 'infiniteLoop'});
setTimeout(() => p.kill(9), 5000);
```

## User interaction
If Deno is running as an interactive shell, the functions `alert`, `prompt`, and `confirm` can be used to print a message to the user or take input from the user. All three functions are blocking till the user presses enter.

- Use `alert` to print a message on the console, and wait for the user to press enter
```ts
alert('This is a message');
//This is a message [Enter]
```
- Use `prompt` to print a message on the console with an optional default value, and wait for the user to give input or press enter
```ts
const val=prompt('Input required');
//Input required 
const val2=prompt('Input required', '127.0.0.1'); //with default value
//Input required [127.0.0.1]
```
- Use `confirm` to print a message on console with y/N, and wait for the user to give input or press enter (only `y` and `Y` is returned as `true`, otherwise `false`)

```ts
const val3=confirm('Are you sure?');
//Are you sure? [y/N]
```

## Utilities
- Use standard library's `tar` module to create tar files
```ts
import { Tar } from "https://deno.land/std/archive/tar.ts";
import { Buffer } from "https://deno.land/std/io/buffer.ts";
const content = new TextEncoder().encode("A tar file sample!");
const tar=new Tar();
tar.append("a1.txt", {filePath: "/var/tmp/a1.txt"});
tar.append("a2.txt", {reader: new Buffer(content), contentSize: content.byteLength});
const file = await Deno.open("/var/tmp/a.tar", { create: true, write: true });
await Deno.copy(tar.getReader(), file);
file.close();
//tar -tf /var/tmp/a.tar -> a1.txt a2.txt
```
- Use standard library's `deferred` promise to create resolvable promises i.e. a promise that contains `resolve` and `reject` functions
```ts
import { deferred } from "https://deno.land/std/async/deferred.ts";
const ds=deferred<string>(), di=deferred<number>();
//.....
ds.resolve("ABCD");
di.reject(1);
```

## Logger
Deno's standard library comes with a feature-rich logger to log messages at different destinations/formats/etc. The logger also supports custom log 
handlers, rotating of log files, log formatting, etc. The logger accepts any type of data.

- Use default logger to write log messages on the console
```ts
import * as log from "https://deno.land/std/log/mod.ts";

log.debug("ABCD"); //DEBUG ABCD
log.info({data: "ABCD"}); //INFO {"data":"ABCD"}
log.warning(5000); //WARNING 5000
log.error({data: {err: "MESSAGE"}}); //ERROR {"data":{"err":"MESSAGE"}}
log.critical("ABCD"); //CRITICAL ABCD
```
- Use `RotatingFileHandler` to direct log messages to files that would rotate after reaching a maximum size and maintains a given number of backups
```ts
import * as log from "https://deno.land/std/log/mod.ts";
await log.setup({
    handlers: {
        file: new log.handlers.RotatingFileHandler("DEBUG", {
            filename: "./log.txt",
            maxBytes: 50,
            maxBackupCount: 3,
            mode: "w"
      }),
    },
    loggers: {
        default: {
          level: "DEBUG",
          handlers: ["file"],
        }
    }
});

log.debug("ABCD");
log.info({data: "ABCD"});
log.warning(5000);
log.error({data: {err: "MESSAGE"}});
log.critical("ABCD");
//cat log.txt
//ERROR {"data":{"err":"MESSAGE"}}
//CRITICAL ABCD
//cat log.txt.1 
//DEBUG ABCD
//INFO {"data":"ABCD"}
//WARNING 5000
```


## Metrics
To get details of the async/sync ops that have been sent, responded, etc. during the lifecycle of the program, use `Deno.metrics()`:

```ts
Deno.metrics();
//{
  opsDispatched: 9,
  opsDispatchedSync: 2,
  opsDispatchedAsync: 7,
  opsDispatchedAsyncUnref: 0,
  opsCompleted: 9,
  opsCompletedSync: 2,
  opsCompletedAsync: 7,
  opsCompletedAsyncUnref: 0,
  bytesSentControl: 247,
  bytesSentData: 16,
  bytesReceived: 180
}
```

## Unit testing
Deno's toolchain comes with a simple unit testing framework that can be executed through `deno test` command. It runs all the files that match `*test.ts`. 

- Use `Deno.test` with a test name and a test function to run
```ts
import { assert } from "https://deno.land/std/testing/asserts.ts";
Deno.test("first test", () => {
    assert(new TextDecoder().decode(new Uint8Array([ 65, 66, 67, 68 ])) === "ABCD");
});
```
- Use `ignore` to skip selective test cases matching a condition
```ts
Deno.test({ name: "second test",
            ignore: true, //or add some condition
            fn: () => {
                assert(new TextDecoder().decode(new Uint8Array([ 65, 66, 67, 68 ])) === "ABCD");
            }
});
```
- Use `only` to run only selective tests matching a condition
```ts
Deno.test({ name: "third test",
            only: true, //or add some condition
            fn: () => {
                assert(new TextDecoder().decode(new Uint8Array([ 65, 66, 67, 68 ])) === "ABCD");
            }
});
```
- Use `async` functions to run tests involving async code
```ts
Deno.test("fourth test", async () => {
    const res=await fetch("https://deno.land");
    const resText=await res.text();
    assert(resText.length > 0);
});
```

```shell
deno test --allow-all
running 2 tests
test first test ... ok (1ms)
test second test ... ignored
test third test ... ok (1ms)
test fourth test ... ok (116ms)

test result: ok. 3 passed; 0 failed; 1 ignored; 0 measured; 0 filtered out (128ms)
```
- Use asserts in the test function to check the data
```ts
import * as asserts from "https://deno.land/std/testing/asserts.ts";
```

|Assert function|Use  |
|--|--|
|`assert`  |Checks if the given expression is true
|`assertEquals`|Checks if `actual` and `expected` are deeply equal
|`assertNotEquals`|Checks if `actual` and `expected` are not deeply equal
|`assertStrictEquals`|Checks if `actual` and `expected` are strictly equal
|`assertNotStrictEquals`|Checks if `actual` and `expected` are not strictly equal
|`assertExists`|Checks if a value is not undefined nor null
|`assertStringIncludes`|Checks if a string contains a sub-string
|`assertArrayIncludes`|Checks if an array contains an element
|`assertMatch`|Checks if a string matches given regex
|`assertNotMatch`|Check if a string doesn't match given regex
|`assertObjectMatch`|Checks if a given object is a sub-set of another object
|`assertThrows`|Checks if a function does throw an error
|`assertThrowsAsync`|Checks if an async function does throw an error



## Deno specific errors
|Error  |
|--|
|NotFound  |
|PermissionDenied
|ConnectionRefused
|ConnectionReset
|ConnectionAborted
|NotConnected
|AddrInUse
|AddrNotAvailable
|BrokenPipe
|AlreadyExists
|InvalidData
|TimedOut
|Interrupted
|WriteZero
|UnexpectedEof
|BadResource
|Http
|Busy
|NotSupported


## Next steps
Please open an issue if there is any missing commonly used functionality.
