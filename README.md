



![](https://github.com/mayankchoubey/deno-in-2-hours/blob/ebbba967ddd3abd2593069bc794c70ab21fbc3a8/deno%20in%202%20hours%20-%20cover.png)

# Deno in 2 hours

> Learn all about Deno in just 2 hours

> All the code is shown for async calls wherever the function is available in both async and sync variants. Sync calls are mentioned wherever they are available.

## Introduction
Deno is a simple, modern, and secure runtime for JavaScript and TypeScript applications that uses V8 and is built in Rust. Deno is a complete toolchain that ships as a single executable. It runs in a sandbox and supports Typescript out the box. Deno 1.0 was made generally available in May'2020. 

## Installation
Deno installs as a single executable with no dependencies. It can be easily installed either directly or using package managers on all the supported platforms.

### Direct
For Mac and Linux, the shell command is:

`curl -fsSL https://deno.land/x/install/install.sh |  sh` 

For windows, the PowerShell command is:

`iwr https://deno.land/x/install/install.ps1 -useb | iex`

An installation error may come if `unzip`is not installed. To install `unzip` use the following command:

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
As Deno is a complete toolchain within a single executable, the upgrade is also built-in via command. To upgrade Deno, simply use:

|Upgrade Type|Command  |
|--|--|
|To latest  |`deno upgrade`  |
|To a particular version|`deno upgrade --version 1.8.3`

## Commands
Deno provides all its functionality through a large number of commands. The commands range from running code, testing, upgrade, formatting, etc.

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

## Common commands
To run an application, use `run` command:
```shell
deno run --allow-read --allow-write --allow-net app.ts
```

To run unit tests, use `test` command (this runs all the `*test*` files present in the local directory):
```shell
deno test --allow-all
```

To quickly run a piece of code, use `eval` command (use `-p` to print the output of the expression on the console):
```shell
deno eval "'hello world'" -p
//hello world
```

## Version, build, paths and other info
`Deno.version` object can be used to get the versions of Deno, Typescript, and V8 (Note: It's an object, not a function):

```ts
Deno.version;
//{ deno: "1.9.0", v8: "9.1.269.5", typescript: "4.2.2" }
```

To get build information, use `Deno.build`:
```ts
Deno.build;
//{target: "x86_64-apple-darwin", arch: "x86_64", os: "darwin", vendor: "apple", env: undefined}
```
To get Deno's executable path, use `Deno.execPath` function:
```ts
Deno.execPath();
///usr/local/bin/deno
```

## Permissions
Deno runs in a sandboxed environment. Unless enabled, there is no file, network, environment, or child process access. The permissions can be enabled through `--allow-XXX` command-line option. A `PermissionDenied` error would be raised at runtime if the user program tries to access a resource outside the sandbox.

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
The command line args are present in an array `Deno.args`. All the command line args are copied as is (no parsing is done).
```ts
deno run a.ts -a 1 -b 2 -c d -e -f -g
//["-a", "1",  "-b", "2",  "-c", "d", "-e", "-f", "-g"]
```

## Environment variables
`Deno.env` object contains useful functions `get`(get single env), `toObject`(get all), and `set` (set env) to work with environment variables. 
```ts
Deno.env.get('ENV1');
//VAR1
Deno.env.set('ENV1', 'VAR2');
Deno.env.toObject();
//{NVM_DIR: "/Users/mayankc/.nvm", XPC_SERVICE_NAME: "0", .... ....ENV1: "VAR1"}
```

## Reader, Writer, Seeker, and Closer
Any resource in Deno can implement either of the available standard interfaces depending on the type of resource:

|Type|Functionality  |Methods|
|--|--|--|
|Readers  |These get data from the resource  |`read` and `readSync`
|Writers|These write data into the resource|`write` and `writeSync`
|Seekers|These support positioning of read pointer for the next read operation|`seek` and `seekSync`
|Closer|Close the resource|`close`

Here are some resources with their interfaces:

|Resource|Interfaces  |
|--|--|
|stdin  |reader, closer  |
|stdout|writer, closer|
|stderr|writer, closer|
|File|reader, writer, seeker, closer|
|Buffer|reader, writer|
|Conn|reader, writer, closer|


## Built-in resources
Deno treats files, streams, sockets, etc. as resources. When a file is opened, or a socket is opened, a resource id (`number`) is allocated. Deno comes with three built-in resources (created and available by default):

|Resource|Rid|Type|
|--|--|--|
|stdin  |0|read
|stdout|1|write
|stderr|2|write

Depending on the resource type, it implements the reader or writer interface. Therefore, the built-in resources can be read or written directly.


```ts
await Deno.stdout.write(new TextEncoder().encode('abcd'));

await Deno.stderr.write(new TextEncoder().encode('Error occured:xyx'));

const buf=new Uint8Array(10);
await Deno.stdin.read(buf);
```

## File ops
Deno supports two types of standard file operations:
- *Low level*: opening, reading, writing, seeking, and closing a file
- *High level*: read or write a file directly (Deno takes care of opening, closing, etc.)

Here are the file ops, both low-level and high-level:

 - `open/openSync`: Opens a file and returns a file object
```ts
const file=await Deno.open('/var/tmp/a.txt');
```
- `create/createSync`: Creates a file if it doesn't exists or truncates if it exists, opens it, and returns a file object
```ts
const file=await Deno.create('/var/tmp/a.txt');
```
 - `read/readSync`: Reads a block of data from the opened file from the current cursor position
```ts
const file=await Deno.open('/var/tmp/a.txt');
const buf=new Uint8Array(1000);
await file.read(buf); //or Deno.read(file.rid, data)
```
- `write/writeSync`: Writes a block of data into the opened file at the cursor position
```ts
const file=await Deno.open('/var/tmp/a.txt', {create:true, write:true});
await file.write(new TextEncoder().encode('abcd')); //or Deno.write(file.rid, data);
```
- `seek/seekSync`: Move the cursor by an offset from a specified cursor position. There are three seek positions to offset from: Start, End, Current. A negative offset is required to move back.
```ts
const file=await Deno.open('/var/tmp/a.txt', {create:true, write:true});
const data=new TextEncoder().encode('abcd');
await file.write(data); //abcd
await file.seek(4, Deno.SeekMode.Start);
file.writeSync(data); //abcdabcd
await file.seek(4, Deno.SeekMode.Current);
await file.write(data); //abcdabcdabcd
await file.seek(-4, Deno.SeekMode.End);
await file.write(data); //abcdabcdabcd
```
- `close`: Closes the resource
```ts
file.close(); //or, Deno.close(file.rid)
```
- `writeFile/writeFileSync`:  	Write a Uint8Array to a file
```ts
await Deno.writeFile("/var/tmp/a.txt", new TextEncoder().encode("ABCD"));
```
- `writeTextFile/writeTextFileSync`: Write a string to a file
```ts
await Deno.writeTextFile("/var/tmp/a.txt", "ABCD");
```
- `readFile/readFileSync`: Read entire file into a Uint8Array
```ts
await Deno.readFile("/var/tmp/a.txt");
//Uint8Array(4) [ 65, 66, 67, 68 ]
```
- `readTextFile/readTextFileSync`: Read entire file into a string
```ts
await Deno.readTextFile("/var/tmp/a.txt");
//ABCD
```

## File system ops
Deno comes with several useful functions to work with the file system. These are different from working on the files (file ops).

- `watchFs`: Raises notifications for any changes in the observed paths (recursive by default). This functions returns an object that implements AsyncIterator.
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
- `chmod/chmodSync`: Update permissions of a given path (Input is in format 0oXXX where X is a digit)
```ts
await Deno.chmod("/var/tmp/a.txt", 0o400);
//ls -ltr /var/tmp/a.txt 
//-r--------  1 mayankc  wheel  4 Apr 12 23:43 /var/tmp/a.txt
```
- `chown/chownSync`: Update ownership of a given path (Input is uid and gid of new owner)
```ts
await Deno.chown("/var/tmp/a.txt", 100, 200);
//ls -ltr /var/tmp/a.txt 
//-r--------  1 guest  games  4 Apr 12 23:43 /var/tmp/a.txt
```
- `copyFile/copyFileSync`: Makes a copy of a file (permissions are also copied). If copied file name exists, it fails.
```ts
await Deno.copyFile("/var/tmp/a.txt", "/var/tmp/b.txt");
//-r--------  1 mayankc  wheel   4 Apr 12 23:43 /var/tmp/b.txt
//-r--------  1 mayankc  wheel   4 Apr 12 23:43 /var/tmp/a.txt
```
- `cwd`: Gets the current working directory
```ts
Deno.cwd();
///Users/mayankc/Work/source/denoExamples
```
- `chdir`: Change the current working directory
```ts
Deno.chdir('/tmp');
Deno.cwd();
// /tmp
```
- `mkdir/mkdirSync`: Create a directory at the specified path (throws error if directory already exists):
```ts
await Deno.mkdir("/tmp/1");
// ls -F /tmp/ -> 1/
```
- `readDir/readDirSync`: Reads the contents of a given directory and returns AsyncIterator to process each entity (name, type, link):
```ts
//ls /var/tmp/myDir -> file1	file2	myDir2/

for await (const dirEntry of Deno.readDir("/var/tmp/myDir"))
    dirEntry; //do something with the contents

//{ name: "file2", isFile: true, isDirectory: false, isSymlink: false }
//{ name: "file1", isFile: true, isDirectory: false, isSymlink: false }
//{ name: "myDir2", isFile: false, isDirectory: true, isSymlink: false }
```
- `rename/renameSync`: Renames a given path (file or directory):
```ts
await Deno.rename("/var/tmp/myDir", "/var/tmp/myDir1");
//ls -F /var/tmp/
//....... myDir1/ .......
```
- `stat/statSync`: Get stats of a file or a directory (symlinks are followed)
```ts
await Deno.stat('/var/tmp/myDir1');
//{isFile: false, isDirectory: true, isSymlink: false, size: 160, mtime: 2021-04-14T03:47:47.471Z, atime: 2021-04-14T03:59:58.937Z, birthtime: 2021-04-14T03:47:27.678Z, dev: 16777220, ino: 82623212, mode: 16877, nlink: 5, uid: 501, gid: 0, rdev: 0, blksize: 4096, blocks: 0}
```
- `lstat/lstatSync`: Get stats of a symlink instead of following it
```ts
await Deno.lstat('/var');
//{isFile: false, isDirectory: false, isSymlink: true, size: 11, mtime: 2019-10-17T20:46:36.987Z, atime: 2019-10-17T20:46:36.987Z, birthtime: 2019-10-17T20:46:36.987Z, dev: 16777220, ino: 1152921500312394500, mode: 41453, nlink: 1, uid: 0, gid: 80, rdev: 0, blksize: 4096, blocks: 0}
```
- `truncate/truncateSync`: Truncates a file from given length if specified, entire file if not specified
```ts
//echo "abcdefgh" > /var/tmp/myDir1/file1
//cat /var/tmp/myDir1/file1 -> abcdefgh
await Deno.truncate('/var/tmp/myDir1/file1', 5);
//cat /var/tmp/myDir1/file1 -> abcde
await Deno.truncate('/var/tmp/myDir1/file1');
//cat /var/tmp/myDir1/file1 -> 
```
- `link/linkSync`: Creates a hard link (mirror copy)
```ts
//echo "123" > fileXYZ.txt
await Deno.link("./fileXYZ.txt", "./fileABC.txt");
//ls ./ -> ..... fileXYZ.txt fileABC.txt .....
```
- `symlink/symlinkSync`: Creates a symbolic link (like pointer)
```ts
//echo "123" > fileXYZ.txt
await Deno.symlink("./fileXYZ.txt", "./fileABC.txt");
//ls -ltr -> .... fileXYZ.txt fileABC.txt -> ./fileXYZ.txt ....
```
- `readLink/readLinkSync`: Returns the destination of a symbolic link
```ts
await Deno.readLink('./fileABC.txt');
//./fileXYZ.txt
```
- `realPath/realPathSync`: Returns the full path for a relative path
```ts
await Deno.realPath('./fileXYZ.txt');
///Users/mayankc/Work/source/denoExamples/fileXYZ.txt
```
- `makeTempDir/makeTempDirSync`: Creates a directory with a random name (optionally specify the path, prefix, and suffix) at the default path:
```ts
await Deno.makeTempDir();
///var/folders/k0/3447gbp16vl309gg50ygclwr0000gn/T/0585d8f8
await Deno.makeTempDir({dir: '/var/tmp', prefix: 'ABCD', suffix: 'VXYZ'});
///var/tmp/ABCD5036d87bVXYZ
```
- `makeTempFile/makeTempFileSync`: Creates a file with a random name (optionally specify the path, prefix, and suffix) at the default path:
```ts
await Deno.makeTempFile();
///var/folders/k0/3447gbp16vl309gg50ygclwr0000gn/T/e1527deb
await Deno.makeTempFile({dir: '/tmp', pre ix: 'PREF_', suffix: '_SUFF'});
///tmp/PREF_8d5d5ce7_SUFF
```
- `remove/removeSync`: Removes the path (set recursive to true if the path is a directory and it needs to be removed recursively)
```ts
await Deno.remove('/var/tmp/myDir1/file1');
await Deno.remove('/var/tmp/myDir1/fileNotThere'); //throws error
await Deno.remove('/var/tmp/myDir1'); //throws error - Directory not empty
await Deno.remove('/var/tmp/myDir1', {recursive: true});
```
- `fdatasync/fdatasyncSync`: Immediately flushes all the data operations pending on a file to the disk
```ts
await Deno.fdatasync(file.rid);
```
- `fsync/fsyncSync`: Immediately flushes all the metadata and data operations pending on a file to the disk
```ts
await Deno.fsync(file.rid);
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
-`iter/iterSync`: Create an asynchronous (or synchronous) iterator for a given reader. It reads in chunks with a default size unless specified in options.
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

## TextEncoder and TextDecoder
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



## Child Process
`Deno.run()` can be used to start a child process to possibly run a shell command, script, another Deno program, etc. While creating the child process, the input, output, and stderr of the child process can be set to: 

- `none`: Ignore
- `pipe`: Redirect to the parent process
- `inherit`: Use parent's process's settings
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
Deno's toolchain comes with a simple unit testing framework that can be executed through `deno test` command. It runs all the files that match `*test.ts`.

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
```

```shell
deno test --allow-all
running 2 tests
test first test ... ok (1ms)
test second test ... ok (116ms)

test result: ok. 2 passed; 0 failed; 0 ignored; 0 measured; 0 filtered out (118ms)
```
