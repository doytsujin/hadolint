# Dockerfile Linter written in Haskell [![Build Status](https://travis-ci.org/lukasmartinelli/hadolint.svg)](https://travis-ci.org/lukasmartinelli/hadolint)

<img align="right" alt="pipecat" width="150" src="http://hadolint.lukasmartinelli.ch/img/cat_container.png" />

Try it out online: http://hadolint.lukasmartinelli.ch/

A smarter Dockerfile linter that helps you build [best practice Docker images](https://docs.docker.com/engine/articles/dockerfile_best-practices/).
The linter is parsing the Dockerfile into an AST and performs rules on top of the AST.
It additionally is using the famous [Shellcheck](https://github.com/koalaman/shellcheck) to lint the Bash
code inside `RUN` instructions.

[![Screenshot](screenshot.png)](http://hadolint.lukasmartinelli.ch/)

## How to use

**On the web**

The best way to use `hadolint` is using it online on http://hadolint.lukasmartinelli.ch/.

**From your terminal**

If you have `hadolint` installed locally you can run it on any local Dockerfile
to get the lint results.

```
hadolint <dockerfile>
```

**With Docker**

Docker comes to the rescue to provide an easy way how to run `hadolint` on most platforms.
To lint a Dockerfile you mount it into the running container and run `hadolint` on top of it.

```
docker run --rm -v $(pwd):/lint lukasmartinelli/hadolint hadolint /lint/Dockerfile
```

## Install

To install `hadolint` locally you need [Haskell](https://www.haskell.org/platform/) and [Cabal](https://wiki.haskell.org/Cabal-Install) installed.
On systems with Cabal you can now install the linter directly from Hackage (installs to `~/.cabal/bin`).

```
cabal update
cabal install hadolint
```

### Binaries

Haskell does not have such a great cross compile story like Go. If someone has experience in
creating static Haskell binaries for Windows, OSX and Linux I would be thankful for your help.

## Rules

List of implemented rules. Take a look into `Rules.hs` to find the implementation of the rules.
Rules with the prefix `DL` originate from the `hadolint` Dockerfile linter while rules with the
prefix `SC` are the [supported error codes from ShellCheck](https://github.com/koalaman/shellcheck/wiki/SC1000).
ShellCheck supports over 100 rules and therefore only the most relevant rules are listed below.

| Rule   | Decscription
| ------ | --------------------------------------------------------------------------------------------------
| DL3000 | Use absolute WORKDIR.
| DL3001 | For some bash commands it makes no sense running them in a Docker container like ssh, vim, shutdown, service, ps, free, top, kill, mount, ifconfig.
| DL3002 | Do not switch to root USER.
| DL3003 | Use WORKDIR to switch to a directory.
| DL3004 | Do not use sudo as it leads to unpredictable behavior. Use a tool like gosu to enforce root.
| DL3005 | Do not use apt-get upgrade or dist-upgrade.
| DL3007 | Using latest is prone to errors if the image will ever update. Pin the version explicitely to a release tag.
| DL3006 | Always tag the version of an image explicitely.
| DL3008 | Pin versions in apt get install.
| DL3009 | Delete the apt-get lists after installing something.
| DL3010 | Use ADD for extracting archives into an image.
| DL3011 | Valid UNIX ports range from 0 to 65535.
| DL3012 | Provide an email adress or URL as maintainer.
| DL3013 | Pin versions in pip.
| DL3014 | Use the -y switch.
| DL3015 | Avoid additional packages by specifying --no-install-recommends.
| DL4000 | Specify a maintainer of the Dockerfile.
| DL4001 | Either use Wget or Curl but not both.
| SC1000 | `$` is not used specially and should therefore be escaped.
| SC1001 | This `\c` will be a regular `'c'`  in this context.
| SC1007 | Remove space after `=` if trying to assign a value (or for empty string, use `var='' ...`).
| SC1010 | Use semicolon or linefeed before `done` (or quote to make it literal).
| SC1015 | This is a unicode double quote. Delete and retype it.
| SC1016 | This is a unicode single quote. Delete and retype it.
| SC1018 | This is a unicode non-breaking space. Delete it and retype as space.
| SC1035 | You need a space here
| SC1037 | Braces are required for positionals over `9`, e.g. `${10}`.
| SC1038 | Shells are space sensitive. Use `< <(cmd)`, not `<<(cmd)`.
| SC1040 | When using <<-, you can only indent with tabs.
| SC1044 | Couldn't find the end of the here doc.
| SC1045 | It's not `foo &; bar`, just `foo & bar`.
| SC1065 | Trying to declare parameters? Don't. Use () and refer to params as $1, $2..
| SC1066 | Don't use $ on the left side of assignments.
| SC1068 | Don't put spaces around the = in assignments.
| SC1077 | For command expansion, the tick should slant left (\` vs ´).
| SC1078 | Did you forget to close this double quoted string?
| SC1079 | This is actually an end quote, but due to next char it looks suspect.
| SC1081 | Scripts are case sensitive. Use 'if', not 'If'.
| SC1083 | This {/} is literal. Check expression (missing ;/\n?) or quote it.
| SC1086 | Don't use $ on the iterator name in for loops.
| SC1087 | Braces are required when expanding arrays, as in ${array[idx]}.
| SC1088 | Parsing stopped here. Invalid use of parentheses?
| SC1089 | Parsing stopped here. Is this keyword correctly matched up?
| SC1090 | Can't follow non-constant source. Use a directive to specify location.
| SC1091 | Not following: (error message here)
| SC1095 | You need a space or linefeed between the function name and body.
| SC1097 | Unexpected `==`. For assignment, use `=`. For comparison, use `[/[[`.
| SC1098 | Quote/escape special characters when using eval, e.g. eval "a=(b)".
| SC1099 | You need a space before the #.
| SC2001 | See if you can use `${variable//search/replace}` instead.
| SC2002 | Useless cat. Consider `cmd < file | ..` or `cmd file | ..` instead.
| SC2003 | expr is antiquated. Consider rewriting this using $((..)), ${} or .
| SC2004 | `$/${}` is unnecessary on arithmetic variables.
| SC2005 | Useless `echo?`. Instead of echo `$(cmd)`, just use `cmd`.
| SC2006 | Use $(..) instead of legacy \`..\`
| SC2008 | echo doesn't read from stdin, are you sure you should be piping to it?
| SC2009 | Consider using pgrep instead of grepping ps output.
| SC2010 | Don't use ls | grep. Use a glob or a for loop with a condition to allow non-alphanumeric filenames.

## Develop

This is my first Haskell program. If you are a experienced Haskeller I would be really thankful
if you would tear my code apart in a review.

### Setup

1. Clone repository
    ```
    git clone --recursive git@github.com:lukasmartinelli/hadolint.git
    ```
2. Create a new sandbox.
    ```
    cabal init
    ```
3. Install modified ShellCheck version
    ```
    cabal install deps/shellcheck
    ```
4. Install the dependencies
    ```
    cabal install --only-dependencies
    ```

### REPL

The easiest way to try out the parser is using the REPL.

```
cabal repl
```

In the REPL you can load the parser code with `:l Parser.hs` and use `parseString` or `parseFile` to get a quick look at the AST.

```
parseString "FROM debian:jessie"
```

### Parsing

The Dockerfile is parsed using [Parsec](https://wiki.haskell.org/Parsec) and is using the lexer `Lexer.hs` and parser `Parser.hs`.

### AST

Dockerfile syntax is is fully described in the [Dockerfile reference](http://docs.docker.com/engine/reference/builder/).  Just take a look at `Syntax.hs` to see the AST definition.


## Alternatives

- https://github.com/RedCoolBeans/dockerlint/
- https://github.com/projectatomic/dockerfile_lint/
- http://dockerfile-linter.com/
