



# Deno in 2 hours

> Learn all about Deno in just 2 hours

All the code is shown for async calls wherever the function is available in both async and sync variants. Sync calls are mentioned wherever they are available.

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
|run  |Executes application  |
|fmt|Fixes indentation|
|types|Print runtime TypeScript declarations|
|cache|Cache and compile remote dependencies recursively|
|coverage|Prints coverage report from coverage profiles|
|info|Prints information about a module, dependencies, caches, etc.|
|eval|Evaluate a small piece of code|
|repl|Runs a read, evaluate, and print loop|
|bundle|Prepares a single Javascript file for the application and all the dependencies
|install|Installs a script and its dependencies as a single executable
|completions|Prints completion code for specified shell
|test|Runs unit tests specified in `*test*.ts` files
|upgrade|Upgrades Deno to latest or specified release
|doc|Prints documentation for modules, functions, etc.
|lint|Runs a linter
|compile|Compiles the given script into a self-contained executable
|lsp|For interaction with editors and IDEs

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


## Permissions
Deno runs in a sandboxed environment. Unless enabled, there is no file, network, environment, or child process access. The permissions can be enabled through `--allow-XXX` command-line option. A `PermissionDenied` error would be raised at runtime if the user program tries to access a resource outside the sandbox.


### Enable all
All the access can be enabled by using `--allow-all` option. In other words, this would mean that the Deno process would have access to everything the running user has. `--allow-all` doesn't take any further options.

### Read
Selective read permissions to the local file system can be enabled via `--allow-read` option.

 - `--allow-read` without any option enables read access to everything in the local file system (restricted only by running user permissions)
 - `--allow-read=<paths>` enables read access to the paths specified. The read access is recursively applied.

Some examples of granular read permissions are:
`--allow-read=/var/tmp`
`--allow-read=/var/tmp,/tmp,/datastore`

### Write
Selective write permissions to the local file system can be enabled via `--allow-write` option.

 - `--allow-write` without any option enables write access to everything in the local file system (restricted only by running user permissions)
 - `--allow-write=<paths>` enables write access to the paths specified. The write access is recursively applied.

Some examples of granular write permissions are:
`--allow-write=/var/tmp`
`--allow-write=/var/tmp,/tmp`
    
### Network
Selective network access can be enabled via `--allow-net` option.

 - `--allow-net` without any option enables access to all domains/IPs/etc.
 - `--allow-net=<networks>` enables network access to the specified list of networks. The networks are exactly applied i.e. no regex, domains, etc.

Some examples of granular network permissions are:
`--allow-net=10.1.1.0`
`--allow-net=api.abc.com,api.def.com`

### Environment
Access to environment variables can be enabled via `--allow-env` option. This permission doesn't go any more granular.

`--allow-env`

### Child process
Permission to create a child process can be enabled via `--allow-run` option. The use cases could be - running a shell command, invoking another Deno program, etc. This permission doesn't go any more granular.

`--allow-run`

### High resolution time
This permission `--allow-hrtime` enables high-resolution time measurements instead of the standard time measurements. This could be useful if measuring in milliseconds. This permission doesn't go any more granular.

`--allow-hrtime`

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

 - `open/openSync`: Opens a file

```ts
const file=await Deno.open('/var/tmp/a.txt');
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
const p=Deno.run({cmd: ['echo', 'hello' , 'world']});
setTimeout(() => p.kill(9), 5000);
```

## 

