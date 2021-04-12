


# Deno in 2 hours

> Learn all about Deno in just 2 hours

## Introduction
Deno is a simple, modern, and secure runtime for JavaScript and TypeScript applications that uses V8 and is built in Rust. Deno is a complete toolchain that ships as a single executable. It runs in a sandbox and supports Typescript out the box. Deno 1.0 was made generally available in May'2020. 

## OS compatibility
Deno is available for the following platforms:
| OS |
|--|
|Mac  |
|Linux|
|Windows|

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



## Permissions
Deno runs in a sandboxed environment. Unless enabled, there is no file, network, environment, or child process access. The permissions can be enabled through `--allow-XXX` command-line option.

### Enable all
All the access can be enabled by using `--allow-all` option. In other words, this would mean that the Deno process would have access to everything the running user has. `--allow-all` doesn't take any further options.

### Read
Selective read permissions to the local file system can be enabled via `--allow-read` option.

 - `--allow-read` without any option enables read access to everything in the local file system (restricted only by running user permissions)
 - `--allow-read=<paths>` enables read access to the paths specified. The read access is recursively applied.

Some examples of granular read permissions are:
`--allow-read=/var/tmp`
`--allow-read=/var/tmp,/tmp,/datastore`

A `PermissionDenied` error would be raised at runtime if the user program tries to read outside the sandbox.

### Write
Selective write permissions to the local file system can be enabled via `--allow-write` option.

 - `--allow-write` without any option enables write access to everything in the local file system (restricted only by running user permissions)
 - `--allow-write=<paths>` enables write access to the paths specified. The write access is recursively applied.

Some examples of granular write permissions are:
`--allow-write=/var/tmp`
`--allow-write=/var/tmp,/tmp`

A `PermissionDenied` error would be raised at runtime if the user program tries to write outside the sandbox.
    
### Network
Selective network access can be enabled via `--allow-net` option.

 - `--allow-net` without any option enables access to all domains/IPs/etc.
 - `--allow-net=<networks>` enables network access to the specified list of networks. The networks are exactly applied i.e. no regex, domains, etc.

Some examples of granular network permissions are:
`--allow-net=10.1.1.0`
`--allow-net=api.abc.com,api.def.com`

A `PermissionDenied` error would be raised at runtime if the user program tries to access any network outside the sandbox.

### Environment
Access to environment variables can be enabled via `--allow-env` option. This permission doesn't go any more granular.

`--allow-env`

A `PermissionDenied` error would be raised at runtime if the user program tries to read environment variables if not allowed by the sandbox.

### Child process
Permission to create a child process can be enabled via `--allow-run` option. The use cases could be - running a shell command, invoking another Deno program, etc. This permission doesn't go any more granular.

`--allow-run`

A `PermissionDenied` error would be raised at runtime if the user program tries to create a child process if not allowed by the sandbox.

### High resolution time
This permission `--allow-hrtime` enables high-resolution time measurements instead of the standard time measurements. This could be useful if measuring in milliseconds. This permission doesn't go any more granular.

`--allow-hrtime`
