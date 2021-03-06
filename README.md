# Haskell Code Explorer

Haskell Code Explorer is a web application for exploring and understanding Haskell codebases. It provides IDE-like code intelligence features such as types and documentation on hover, "go to definition", "find references" and semantic highlighting.

Examples:

- [https://haskell-code-explorer.mfix.io/package/stm-2.4.5.0/show/Control/Concurrent/STM/TQueue.hs#L87](https://haskell-code-explorer.mfix.io/package/stm-2.4.5.0/show/Control/Concurrent/STM/TQueue.hs#L87)
- [https://haskell-code-explorer.mfix.io/package/async-2.1.1.1/show/Control/Concurrent/Async.hs#L251](https://haskell-code-explorer.mfix.io/package/async-2.1.1.1/show/Control/Concurrent/Async.hs#L251)
- [https://haskell-code-explorer.mfix.io/package/haxl-0.5.1.0/show/Haxl/Core/Monad.hs#L569](https://haskell-code-explorer.mfix.io/package/haxl-0.5.1.0/show/Haxl/Core/Monad.hs#L569)

![Haskell Code Explorer](https://haskell-code-explorer.mfix.io/screenshot.png)

The public instance of Haskell Code Explorer is available at [https://haskell-code-explorer.mfix.io](https://haskell-code-explorer.mfix.io). It contains core libraries (ghc, base, etc.) and a subset of packages from a Stackage snapshot.

Haskell Code Explorer consists of an indexer, an HTTP server, and a JavaScript application. The indexer uses GHC API to create a data structure that contains detailed information about the source code of a Cabal package. The HTTP server reads that data structure into memory and responds to HTTP requests from the JavaScript application.

## Motivation

Reading and understanding code is an essential part of the software development process. Understanding code in any statically typed language is much easier when code intelligence features (types on hover, go-to-definition, etc.) are available. Code intelligence for Haskell is especially useful because types are informative and precise (thanks to Haskell's purity and global type inference).

## Features

* Types (actual type, instantiated type, instance resolution tree) and documentation on hover. Types are interactive: left-click on a type constructor -> go to definition, right click on a type constructor -> show kind signature.<br />
  ![Hover](https://haskell-code-explorer.mfix.io/hover.png)

* Go to definition (cross-package)

* Find references (cross-package)
  ![References](https://haskell-code-explorer.mfix.io/references.png)

* Type of each expression<br />
  ![Expression](https://haskell-code-explorer.mfix.io/expressions.png)<br />
  Select a piece of text to get the type of each Haskell expression inside the selection.

* Semantic highlighting

## Installation

```bash
git clone https://github.com/alexwl/haskell-code-explorer
cd haskell-code-explorer
```

To build Haskell Code Explorer Stack ([https://docs.haskellstack.org/en/stable/README/](https://docs.haskellstack.org/en/stable/README/)) is needed.

At the moment Haskell Code Explorer supports GHC 8.6.3, GHC 8.4.4, GHC 8.4.3, GHC 8.2.2, and 8.0.2.

For GHC 8.6.3:

```bash
stack install
```

For GHC 8.4.4:

```bash
stack --stack-yaml=stack-8.4.4.yaml install
```

For GHC 8.4.3:

```bash
stack --stack-yaml=stack-8.4.3.yaml install
```

For GHC 8.2.2:

```bash
stack --stack-yaml=stack-8.2.2.yaml install
```

For GHC 8.0.2:

```bash
stack --stack-yaml=stack-8.0.2.yaml install
```

## Indexing source code of a Cabal package

`haskell-code-indexer` executable is responsible for indexing packages (by default, index is saved to `.haskell-code-explorer` directory).

A package should be built using either cabal-install or stack before indexing (`cabal new-build`,`cabal build`, or `stack build` command should be executed).

`haskell-code-indexer` requires globally installed GHC and cabal-install (`cabal`). The reason for this is that `haskell-code-indexer` uses `cabal-helper` library [https://hackage.haskell.org/package/cabal-helper](https://hackage.haskell.org/package/cabal-helper) to get package build information. `cabal-helper` builds (at runtime) an executable linked against a version of Cabal library that was used to configure the package.

If there is no globally installed GHC on the system, then it is possible to use `stack exec` command ([https://docs.haskellstack.org/en/stable/GUIDE/#exec](https://docs.haskellstack.org/en/stable/GUIDE/#exec)) that adds a path to GHC binaries installed by Stack to `PATH` environment variable:

```bash
stack --resolver=lts-13.0 exec --no-ghc-package-path haskell-code-indexer -- INDEXER_OPTIONS
```

### Examples

Show all indexer options:
```bash
haskell-code-indexer -h
```

Index package:
```bash
haskell-code-indexer --package PATH
```

Index package with specific `dist` directory:
```bash
haskell-code-indexer --package PATH --dist dist-newstyle/build/x86_64-linux/ghc-8.2.2/hpath-0.9.2
```

Index package built by Stack:
```bash
haskell-code-indexer --package PATH --dist $(stack path --dist-dir)
```

## Starting HTTP Server

`haskell-code-server` executable reads the package index created by `haskell-code-indexer` and starts the HTTP server. The HTTP server responds to API requests and serves static assets (JavaScript files that are in `haskell-code-explorer/javascript/release` directory).

### Examples

Show all server options:

```bash
haskell-code-server -h
```

Load the indexed package and start the server:

```bash
haskell-code-server --package PATH --port 8080
```

Load multiple indexed packages and start the server:

```bash
haskell-code-server --package PATH1 --package PATH2 --package PATH3 --port 8080
```

Open [http://localhost:8080](http://localhost:8080) in a browser to explore source code of the package.
