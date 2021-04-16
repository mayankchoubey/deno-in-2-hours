

![](https://github.com/mayankchoubey/deno-in-2-hours/blob/ebbba967ddd3abd2593069bc794c70ab21fbc3a8/deno%20in%202%20hours%20-%20cover.png)


> Learn all about Deno in just 2 hours

> All the sample code is shown for async calls wherever the function is available in both async and sync variants, otherwise, it's sync. Sync calls are mentioned wherever they are available.

- [Introduction](#introduction)
- [Installation](#installation)
  * [Direct](#direct)
  * [Package manager](#package-manager)
- [Upgrade](#upgrade)
- [Commands](#commands)
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
- [System info](#system-info)
- [Encoding and decoding](#encoding-and-decoding)
- [Get random values](#get-random-values)
- [Process ops](#process-ops)
- [Child Process](#child-process)
- [User interaction](#user-interaction)
- [Metrics](#metrics)
- [Deno specific errors](#deno-specific-errors)
- [Unit testing](#unit-testing)

## Introduction
Deno is a simple, modern, and secure runtime for JavaScript and TypeScript applications that uses V8 and is built in Rust. Deno is a complete toolchain that ships as a single executable. It runs in a sandbox and supports Typescript out the box. Deno `1.0` was made generally available in May'2020. The latest release of Deno is `1.9.0`.

## Installation
Deno installs as a single executable with no dependencies. It can be easily installed either directly or using package managers on all the supported platforms.

### Direct
For Mac and Linux, use `curl` and `shell` commands:

`curl -fsSL https://deno.land/x/install/install.sh |  sh` 

For windows, use `powershell` command:

`iwr https://deno.land/x/install/install.ps1 -useb | iex`

An installation error may come if `unzip`is not installed on Mac or Linux. To install `unzip` use the following command:

|OS|Command  |
|--|--|
|Mac  |`brew install unzip` 
|Linux|`apt-get install unzip -y`


### Package manager
|OS|Installation command  |
|--|--|
|Mac  | `brew install deno` |
|Windows|`choco install deno`|
|Windows|`scoop install deno`

## Upgrade
As Deno is a complete toolchain within a single executable, the upgrade is also available through a built-in command. To upgrade Deno, simply use:

|Upgrade To|Command  |
|--|--|
|Latest  |`deno upgrade`  |
|A particular version|`deno upgrade --version 1.8.3`

## Commands
Deno provides all its functionality through a large number of commands. The commands range from running the application, testing, upgrade, formatting, etc.

|Command|Use  |
|--|--|
|`run`  |Executes application  |
|`fmt`|Fixes indentation|
|`types`|Print runtime TypeScript declarations|
|`cache`|Cache and compile remote dependencies recursively|
|`coverage`|Prints coverage report from coverage profiles|
|`info`|Prints information about a module, dependencies, caches, etc.|
|`eval`|Evaluate a small piece of code|
|`repl`|Runs a read, evaluate, and print loop|
|`bundle`|Prepares a single Javascript file for the application and all the dependencies
|`install`|Installs a script and its dependencies as a single executable
|`completions`|Prints completion code for specified shell
|`test`|Runs unit tests specified in `*test*.ts` files
|`upgrade`|Upgrades Deno to latest or specified release
|`doc`|Prints documentation for modules, functions, etc.
|`lint`|Runs a linter
|`compile`|Compiles the given script into a self-contained executable
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

- Use `fmt` command to fix indentation issues (it overwrites the file)
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
Hello from app { HW: "Hello from app1" } { HW: "Hello from app2" }
```
- Use `install` to install an app as a self-contained executable at the specified path
```ts
deno install -f --root /tmp -n app app.ts
✅ Successfully installed app
/private/tmp/bin/app
```

## Version, build, paths and other info
- Use `Deno.version` object to get the versions of Deno, Typescript, and V8 (Note: It's an object, not a function):

```ts
Deno.version;
//{ deno: "1.9.0", v8: "9.1.269.5", typescript: "4.2.2" }
```
- Use `Deno.build` object to get information about the Deno build being used:
```ts
Deno.build;
//{target: "x86_64-apple-darwin", arch: "x86_64", os: "darwin", vendor: "apple", env: undefined}
```
- Use `Deno.execPath` function to get Deno's executable path:
```ts
Deno.execPath();
///usr/local/bin/deno
```
- Use `import.meta` object to get information on the main module (main will be false for workers):
```ts
import.meta;
//{ url: "file:///Users/mayankc/Work/source/denoExamples/a.ts", main: true }
```

## Permissions
Deno runs in a sandboxed environment. Unless explicitly enabled, there is no file, network, environment, or child process access. The permissions can be enabled through `--allow-XXX` command-line option. A `PermissionDenied` error would be raised at __runtime__ if the user program tries to access a resource outside the sandbox.

|Permission Flag|Type|Description  |Examples|
|--|--|--|--|
|`--allow-all`  |Boolean|Enables all access (no sandboxing)  |`deno run --allow-all app.ts`
|`--allow-read`|List|Recurisvely enables read access to all (if list is empty) or given list of paths|`deno run --allow-read` app.ts or `deno run --allow-read=/var/,/tmp,/datastore
|`--allow-write`|List|Recurisvely enables write access to all (if list is empty) or given list of paths|`deno run --allow-write` app.ts or `deno run --allow-write=/var/,/tmp,/datastore
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

## Environment variables
- Use `Deno.env` object's functions to `get`(get single env), `toObject`(get all envs), `delete` (remove an env), and `set` (set an env) the environment variables:
```ts
Deno.env.get('ENV1'); //VAR1
Deno.env.set('ENV1', 'VAR2');
Deno.env.set('ENV3', 'VAR3');
Deno.env.delete('ENV2');
Deno.env.toObject();
//{NVM_DIR: "/Users/mayankc/.nvm", XPC_SERVICE_NAME: "0", .... ....ENV1: "VAR2"}
```

## Reader, Writer, Seeker, and Closer
Any resource in Deno can implement either of the available standard interfaces depending on the type of resource. This way resources can be accessed through generic methods.

|Type|Functionality  |Methods|
|--|--|--|
|Readers  |These get data from the resource  |`read` and `readSync`
|Writers|These write data into the resource|`write` and `writeSync`
|Seekers|These support positioning of read pointer for the next read operation|`seek` and `seekSync`
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
Deno treats files, streams, sockets, etc. as resources. When a file is opened, or a socket is opened, a resource id (`number`) is allocated. Deno comes with three built-in resources (created and available by default):

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
- __Low level__: User takes care of opening, reading, writing, seeking, and closing the file
- __High level__: User reads or writes the file directly (Deno takes care of opening, closing, seeking, etc.)

Here are the file ops, both low-level and high-level:

 - Use `Deno.open/Deno.openSync` to open a file and get a file object
```ts
const file=await Deno.open('/var/tmp/a.txt');
```
- Use `Deno.create/Deno.createSync` to create a file if it doesn't exists or truncates if it exists, opens it, and get a file object
```ts
const file=await Deno.create('/var/tmp/a.txt');
```
 - Use `read/readSync` to read a block of data from the opened file starting from the current cursor position
```ts
const file=await Deno.open('/var/tmp/a.txt');
const buf=new Uint8Array(1000);
await file.read(buf); //or Deno.read(file.rid, data)
```
- Use `write/writeSync` to write a block of data into the opened file starting at the cursor position
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
- Use `Deno.symlink/Deno.symlinkSync` to create a symbolic link (like pointer)
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

## Buffer ops
Buffer is a simple data storage that provides a way to store and retrieve data. Deno's buffer has a cursor to keep track of the next read/write operation. It can grow as needed. Usually, Buffers are initialized from a data source like a file or socket and then are read as needed. From v1.9.0, `Buffer` has been moved to the standard library. 
```ts
import { Buffer } from "https://deno.land/std/io/buffer.ts";
```

- `new Buffer`: Creates a Buffer with default initial storage or takes an optional `ArrayBuffer` for initialization
```ts
const buf=new Buffer();
const ab=new ArrayBuffer(10);
const buf2=new Buffer(ab);
```
- `empty`: Returns true if buffer has completely read
```ts
buf.empty(); //true
buf2.empty(); //false
```
- `length`: Returns the length of unread part of the buffer
```ts
buf.length(); //0
buf2.length(); //8
```
- `capacity`: Returns the capacity of buffer (i.e. total number of bytes present )
```ts
buf.capacity(); //0
buf2.capacity(); //8
```
- `bytes`: Returns the complete unread portion of the buffer
```ts
const buf=new Buffer(new Uint8Array(5).fill(1));
buf.bytes();
//Uint8Array(5) [ 1, 1, 1, 1, 1 ]
```
- `read/readSync`: Takes a Uint8Array as input and reads bytes from buffer upto the length of array
```ts
const uint8 = new Uint8Array(20).fill(1);
const buf=new Buffer(uint8);
const readBuf=new Uint8Array(5);
await buf.read(readBuf); //5 bytes read
buf.length; //15
```
- `write/writeSync`: Takes a Uint8Array and writes all the bytes into the buffer
```ts
const buf=new Buffer(new Uint8Array(5).fill(1));
await buf.write(new Uint8Array(5).fill(2));
buf.bytes();
//Uint8Array(10) [1, 1, 1, 1, 1, 2, 2, 2, 2, 2]
```
- `readFrom/readFromSync`: Reads all the data from a `reader` and saves it into the buffer
```ts
const file=await Deno.open('/var/tmp/child.txt');
const buf=new Buffer();
await buf.readFrom(file);
buf.bytes();
//Uint8Array(12) [104, 101, 108, 108, 111,  32, 119, 111, 114, 108, 100,  10]
```

## ReadAll, WriteAll, and Iter
`readAll`, `writeAll`, and `iter` are three very commonly used functions in Deno. From v1.9.0, these functions have been moved to the standard library.

```ts
import { readAll, writeAll, iter } from "https://deno.land/std/io/util.ts";
```

- `readAll/readAllSync`: Reads everything from a reader and returns in a Uint8Array
```ts
const file=await Deno.open('/var/tmp/child.txt');
const data=await readAll(file);
//Uint8Array(12) [104, 101, 108, 108, 111,  32, 119, 111, 114, 108, 100,  10]
```
- `writeAll/writeAllSync`: Writes everything present in Uint8Array to the writer
```ts
const file=await Deno.open('/var/tmp/child.txt', {write: true});
await writeAll(file, new Uint8Array(5).fill(65));
//cat /var/tmp/child.txt -> AAAAA world
```
- `iter/iterSync`: Create an asynchronous (or synchronous) iterator for a given reader. It reads in chunks with a default size unless specified in options.
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
    body: JSON.stringify({name: 'Mayank'})
});
if(res.ok) {
    await res.text(); //String -> {"name":"Mayank"}
    await res.json(); //JSON object -> { name: "Mayank" }
    await res.formData(); //FormData -> FormData { [Symbol(data)]: [ [ "name", "Mayank" ] ] }
}
```
- Use `Deno.listen/Deno.listenTls` to create a low-level TCP server (For HTTP servers,  use `serve/serveTls` instead)
```ts
const listener = Deno.listen({ hostname: "127.0.0.1", port: 5566 });
const conn=await listener.accept();
await Deno.copy(conn, conn); //conn is both reader and writer
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
- Use `Deno.serve/Deno.serveTls` to create HTTP servers without or with TLS
```ts
import { serve } from "https://deno.land/std/http/server.ts";
for await (const req of serve({port:3000}))
    req; //do something with request 
//--
import { serveTLS } from "https://deno.land/std/http/server.ts";
for await (const req of serveTLS({port:3000, certFile: "./c.crt", keyFile: "./k.key"}))
    req; //do something with request
```
- Use `Deno.resolveDns` to fetch a particular type of record (`A`/`AAAA`/`ANAME`/`CNAME`/`SRV`/`PTR`/`MX`/`TXT`) from the DNS
```ts
await Deno.resolveDns("www.facebook.com", "A"); //[ "69.171.250.35" ]
await Deno.resolveDns("www.facebook.com", "AAAA"); //[ "2a03:2880:f1ff:83:face:b00c:0:25de" ]
await Deno.resolveDns("www.facebook.com", "CNAME"); //[ "star-mini.c10r.facebook.com." ]
```
- Use`Deno.shutdown` to close the socket
```ts
Deno.shutdown(conn.rid);
```

## System info
> A lot of the system functions are under unstable umbrella
- Use `Deno.hostname` to get name of the host where Deno is running
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
- Use `Deno.osRelease` to get details about the operating system
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
- Use `

## Encoding and decoding
Deno comes with the web's standard `TextEncoder` and `TextDecoder` to convert data from string to Uint8Array and vice versa. These are very useful in working with file, buffer, and socket ops.

```ts
new TextEncoder().encode('ABCD');
//Uint8Array(4) [ 65, 66, 67, 68 ]
new TextDecoder().decode(new Uint8Array([ 65, 66, 67, 68 ]));
//ABCD
```

## Get random values
Deno's crypto comes with web's `getRandomValues` function to generate cryptographically strong random numbers. This function fills the input array with 8/16/32 bit random numbers.

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

## Process ops
- Use `Deno.pid` and `Deno.ppid` variables to get current and parent's process IDs:
```ts
Deno.pid;
//89597
Deno.ppid;
//81921
```
- To abruptly exit the running process, use `Deno.exit` function (an exit code is required):
```ts
//a.ts
setTimeout(() => {
    Deno.exit(1);
    console.log('Should never see this');
}, 5000);

shell > deno run a.ts
shell > //the line never gets printed
```
- To synchronously sleep current thread for given milliseconds, use `Deno.sleepSync`:
```ts
Deno.sleepSync(100);
```
- Use `Deno.kill` to send a signal to a given process id
```ts
Deno.kill(pid, Deno.Signal.SIGINT);
```


## Child Process
`Deno.run()` can be used to start a child process to possibly run a shell command, script, another Deno program, etc. While creating the child process, the input, output, and stderr of the child process can be set to: 

- `none`: Ignore
- `pipe`: Redirect to the parent process
- `inherit`: Use parent's process's settings (default)
- `number`: Redirect to given `rid`

The `Deno.run()` function call returns a process object. This process object can be used to check the status of the child process, get piped output, and terminate it if needed. The process object has a `status` function that would resolve when the child process is done.

```ts
const p=Deno.run({cmd: ['echo', 'hello' , 'world']});
await p.status();
//output on console: hello world
```
```ts
const p=Deno.run({cmd: ['echo', 'hello' , 'world'], stdout: 'piped', stderr: 'piped'});
await p.status();
const [s, e] = await Promise.all([
	p.output(),
    p.stderrOutput()]);
}
//s: hello world, e: undefined
```
```ts
const file=await Deno.open("/var/tmp/child.txt", {create: true, write: true});
const p=Deno.run({cmd: ['echo', 'hello' , 'world'], stdout: file.rid, stderr: file.rid});
await p.status();
file.close();
//cat /var/tmp/child.txt -> hello world
```
```ts
const p=Deno.run({cmd: 'infiniteLoop'});
setTimeout(() => p.kill(9), 5000);
```

## User interaction
If Deno is running as an interactive shell, the functions `alert`, `prompt`, and `confirm` can be used to print a message to the user or take input from the user. All three functions are blocking till the user presses enter.

- `alert`: Prints a message on the console, and waits for the user to press enter
- `prompt`: Print a message on the console with an optional default value, and waits for the user to give input or press enter
- `confirm`: Prints a message on console with y/N, and waits for the user to give input or press enter (only `y` and `Y` is returned as `true`, otherwise `false`)

```ts
alert('This is a message');
//This is a message [Enter]
const val=prompt('Input required');
//Input required 
const val2=prompt('Input required', '127.0.0.1'); //with default value
//Input required [127.0.0.1] 
const val3=confirm('Are you sure?');
//Are you sure? [y/N]
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

## Unit testing
Deno's toolchain comes with a simple unit testing framework that can be executed through `deno test` command. It runs all the files that match `*test.ts`. Use `ignore` to skip selective test cases or `only` to run only selective test cases matching a condition.

```ts
import { assert } from "https://deno.land/std/testing/asserts.ts";

Deno.test("first test", () => {
    assert(new TextDecoder().decode(new Uint8Array([ 65, 66, 67, 68 ])) === "ABCD");
});

Deno.test("second test", async () => {
    const res=await fetch("https://deno.land");
    const resText=await res.text();
    assert(resText.length > 0);
});

Deno.test({ name: "third test",
            ignore: true, //or add some condition
            fn: () => {
                assert(new TextDecoder().decode(new Uint8Array([ 65, 66, 67, 68 ])) === "ABCD");
            }
});
```

```shell
deno test --allow-all
running 2 tests
test first test ... ok (1ms)
test second test ... ok (116ms)

test result: ok. 2 passed; 0 failed; 1 ignored; 0 measured; 0 filtered out (118ms)
```


