# Season 1

## Introduction to NodeJS
### History of NodeJS
* **2009: NodeJS** was born by **Ryan Dahl** independently, initially named **web.js**
    * Spider Monkey (Firefox) - 2 days
    * V8 Engine (Google Chrome)
    * **Joyent(organization)** asks Ryan to join them and build NodeJS together
* **2010: NPM** created by Isaac Z. Schlueter
* **2011: Windows Support** - (initially for Linux and Mac) **Joyent + Microsoft**
* **2012: Ryan Dahl** left the project and responsibility handover to **Isaac Z. Schlueter**
    * The pace of developing NodeJS gets slow because Joyent holds the releases of NodeJS updates
* **2014: Fedor Indutn** fork the NodeJS and named **io.js** with 3-4 developers
* **2015:** In **Sept Fedor Indutn** and **Joyent** decided to merger **io.js** and **node.js** and a **NodeJS Foundation** is formed after that this community manages the NodeJS.
* **2019:** A new community is formed after merging **JS Foundation** and **NodeJS Foundation** named as **Open JS Foundation**

## JS on Server

### JS V8 Engine
* Written in C++ program
* It can embedded into any C++ application
* JS -> V8 -> Machine Language(0 1)
* NodeJS is a C++ application with V8 embedded into it

        +----------Server-----------+
        |                           |
        |   +-------NodeJS-------+  |
        |   |                    |  |
        |   |  +-------+         |  |
        |   |  | V8    |         |  |
        |   |  | Engine|         |  |
        |   |  +-------+         |  |
        |   |                    |  |
        |   |  +--------------+  |  |
        |   |  | Super Power  |  |  |
        |   |  | API on Server|  |  |
        |   |  +--------------+  |  |
        |   |                    |  |
        |   +--------------------+  |
        +---------------------------+

### ECMAScript (Standards / Rules)
JS Engines follow these standards
* Google -> V8
* Firefox -> Spider Monkey
* MS, IE -> Chakra
* JS Code -> Safari
* **window, this, self(worker), frames** all point to **global** in the **browsers**
* **global** point to **global** the **nodejs**
* **globalThis** was introduced in **ES2020** and all the **Browsers** as well as **NodeJS** make it standard

           +-------------+
           |  JS, C++    | <--- High Level Language
           +-------------+
                 |
                 v
           +---------------+
           | Machine Code  |
           |     |         |
           |     v         | <--- Low Level Code
           | Assembly Code |
           +---------------+
                 |
                 v
             Binary Code

## module.exports & require

* Modules protected their variables and functions from leaking
* require will execute the module but we cannot access the private variable and function of the module into the main module - syntax - require(‘./xyz’)
* If you want to use private variable and function in to our main module then you need to export it module syntax - module.exports = xyz or module.exports = { xyz, zyx }
* We have 2 ways to import and export module 
    * Common Js Module (cjs)
    * ES Module Js (mjs, ESM)
    ```
    +---------------------------------------------------------------+
    |   Common JS Module (cjs)      |   ES Module Js (mjs, ESM)     |
    |---------------------------------------------------------------|
    |   module.exports, require()   |   export, import              |
    |---------------------------------------------------------------|
    |   by default in NodeJS        |   by default in React         |
    |                               |       and Angular             |
    |---------------------------------------------------------------|
    |   older way                   |   newer way                   |
    |---------------------------------------------------------------|
    |   synchronous                 |   asynchronous                |
    |---------------------------------------------------------------|
    |   non strict mode             |   strict mode                 |
    +---------------------------------------------------------------+

* If we want to use mjs method in NodeJs then we need to make changes in package.json

```
    {
        “type” : “module”,
    }
```

* At the if you console module.exports you find it is empty object, so you can also export a your variable and function as 

```
    module.exports = { xyz }
    module.exports.xyz
```

* We can set up a folder as a module by creating an index.js file inside it. In this index.js file, require the modules from other files in the folder, and then module.exports them from index.js

```
    // index.js


    const { zyx } = require('./zyx') // from module 1


    const { xyz } = require('./xyz') // from module 2


    module.exports = { xyz, zyx }
    // main module


    const { xyz, zyx } = require('./calculate')

```
* If we have to require a json file data then

```
   // JSON
   {
       "name": "xyz",
       "city": "x"
   }

   // main module
   const data = require('./a.json')

   console.log(JSON.stringify(data))
```

* require(‘/path) behind the scene
    * Resolve the module
        * ./localPath
        * .json
        * node.module
    * Loading the module
        * file content is loaded acc. to file type
    * Wraps inside the IIFE
    * Evaluation
    * Caching

## NodeJS github repo

* When we use **require('./xyz')** in a Node.js module, Node.js internally wraps the code of the required module in an **Immediately Invoked Function Expression (IIFE)** before executing it. This process creates a **local scope** for the module's variables and functions, preventing them from affecting or being affected by the **global scope**

* Node github repo 

```
// node repo - require a small part
   /**
    * Add the CommonJS wrapper around a module's source code.
    * @param {string} script Module source code
    */
   let wrap = function (script) { 
       return Module.wrapper[0] + script + Module.wrapper[1];
   };




   const wrapper = [
       '(function (exports, require, module, __filename, __dirname) { ',
       '\n});',
   ];
```
## Deep in v8 JS Engine

* 2 types of languages 
    * Interpreted
        * Line by Line
        * Fast initial execution
        * Interpreter
    * Compiled
        * First, compile HL Code to Machine Code
        * Initially heavy but executed fast
        * Compiler

* JS is JIT (Just In Time) Compiled 

* Code flow 
    1. **Parsing**
        * **Lexical Analysis (Tokenization):**
            * **Input:** Raw JavaScript source code.
            * **Process:** The code is read and broken down into tokens. Tokens are the smallest units of meaning in the code, such as keywords, operators, identifiers, and literals.
            * **Output:** A stream of tokens representing the code structure.
        * **Syntax Analysis (AST - Abstract Syntax Tree):**
            * **Input:** Stream of tokens from the lexical analysis.
            * **Process:** Tokens are parsed according to JavaScript grammar rules to create an Abstract Syntax Tree (AST). The AST represents the syntactic structure of the code, showing how tokens are grouped and related.
            * **Output:** AST, which is a hierarchical tree structure where nodes represent various language constructs.
    2. **Interpreter (Ignition)**
        * **Byte Code Generation:**
            * **Input:** AST from the syntax analysis.
            * **Process:** The AST is translated into bytecode. Bytecode is a lower-level, platform-independent representation of the code that is more abstract than machine code but more efficient than the raw source code.
            * **Output:** Bytecode, which is a set of instructions that the V8 interpreter (Ignition) can execute.
        * **Execution:**
            * **Input:** Bytecode generated from the interpreter.
            * **Process:** Ignition, the interpreter, executes the bytecode. It performs the code's operations but at this stage, the execution is not highly optimized.
            * **Output:** Results of code execution.
    3. **Hot Code Optimization**
        * **Identifying Hot Code:**
            * **Process:** During execution, Ignition collects profiling information to identify "hot" code paths—sections of the code that are executed frequently or are performance-critical. This is done through a process called "profiling" or "sampling."
        * **TurboFan Compilation:**
            * **Input:** Hot code paths identified by Ignition.
            * **Process:** The identified hot code paths are passed to TurboFan, V8's optimizing compiler. TurboFan takes the bytecode and generates highly optimized machine code specific to the target CPU architecture.
            * **Output:** Optimized machine code.
        * **Execution of Optimized Code:**
            * **Input:** Machine code generated by TurboFan.
            * **Process:** The optimized machine code is executed directly by the CPU, providing faster performance compared to the bytecode interpretation.
            * **Output:** Results of code execution, typically with improved performance over the initial interpretation phase.
    * **Summary**
        * **Parsing:** The code is tokenized and transformed into an AST.
        * **Interpreter:** The AST is converted into bytecode, which is then executed by Ignition.
        * **Hot Code Identification:** Ignition profiles the execution to find hot code paths.
        * **Optimization:** Hot code paths are compiled into optimized machine code by TurboFan.
        * **Execution:** The optimized machine code is executed, resulting in improved performance.

```
                                        +---------------------------------------+
                                        |       JavaScript Source Code          |
                                        +---------------------------------------+
                                                            |
                                                            v
    +-------------------------------------------------------------------------------------------------------------+
    |                                                    v8 Engine                                                |
    |                                                      |                                                      |
    |                                                      v                                                      |
    |                                +-----------------------------------------------+                            |
    |                                |                     Parsing                   |                            |
    |                                |    +---------------------------------------+  |                            |
    |                                |    |      Lexical Analysis (Tokenization)  |  |                            |
    |                                |    +---------------------------------------+  |                            |
    |                                |                        |                      |                            |
    |                                |                        v                      |                            |
    |                                |    +---------------------------------------+  |                            |
    |                                |    |      Syntax Analysis (AST)            |  |                            |
    |                                |    +---------------------------------------+  |                            |
    |                                |                                               |                            |
    |                                +-----------------------------------------------+                            |
    |                                                      |                                                      |
    |                                                      |                                                      |
    |                            -------------------------------------------------------                          |
    |                            |                                                     |                          |
    |                            v                                                     v                          |
    |   +---------------------------------------------+       +-----------------------------------------------+   |
    |   |               Interpreter                   |       |                 Compiler                      |   |
    |   |                     |                       |       |    +---------------------------------------+  |   |
    |   |                     |                       |       |    |      Profiling & Hot Code Detection   |  |   |
    |   |                     v                       |       |    +---------------------------------------+  |   |
    |   |   +---------------------------------------+ |       |                    |                          |   |
    |   |   |             Ignition                  | |       |                    v                          |   |
    |   |   +---------------------------------------+ |       |    +---------------------------------------+  |   |
    |   |                     |                       |       |    |    Optimizing Compiler (TurboFan)     |  |   |
    |   |                     v                       |       |    +---------------------------------------+  |   |
    |   |   +---------------------------------------+ |       |                    |                          |   |
    |   |   |          Bytecode Generation          | |       |                    v                          |   |
    |   |   +---------------------------------------+ |       |    +---------------------------------------+  |   |
    |   |                                             |       |    |  Generation of Optimized Machine Code |  |   |
    |   |                                             |       |    +---------------------------------------+  |   |
    |   +---------------------------------------------+       +-----------------------------------------------+   |
    |                            |                                                     |                          |
    |                            v                                                     v                          |
    |                            -------------------------------------------------------                          |
    |                                                    |                                                        |
    |                                                    v                                                        |
    |                                +---------------------------------------+                                    |
    |                                |               Execution               |                                    |
    |                                +---------------------------------------+                                    |
    +-------------------------------------------------------------------------------------------------------------+
```

* Sometimes JS engine deoptimezed code as well. 

```
   function sum(a, b) {
       return a + b;
   }


   // Compiler optimized this code first time for Integer
   sum(10, 5)


   // 2nd time run optimized code
   sum(3, 5)


   // 3rd time
   sum(5, 6)


   // If you pass string it confuses and deoptimized code
   // and pass to Interpreter
   sum("a", "b")
```

* V8 handles garbage collection to efficiently manage memory and reclaim unused space while the engine is running. It uses several strategies, including:
    * **Mark & Sweep:** Identifies and reclaims unused memory by marking reachable objects and sweeping away the rest.
    * **Orinoco:** A generational collector that optimizes performance by managing young and old objects separately.
    * **Oilpan:** Manages memory for C++ objects in V8, focusing on reducing memory leaks.
    * **Scavenger:** Handles garbage collection for new objects, quickly reclaiming memory from short-lived objects.
    * **MCompact:** Compacting collector that reduces memory fragmentation and improves memory layout.
* Earlier v8 was using the Crank Shaft compiler

## libuv & Event Loop

### Event Loop Inside
* The event loop runs in many phases. Major phases of the event loop include below 4 phases 
    * Timer - uses **minHeap DS**
        * setTimeout
        * setInterval
    * Poll
        * I/O Callbacks
        * incoming connections
        * data
        * fs, crypto, http.get
    * Check
        * setImmediate
    * Close
        * socket .on(“close”)


* Before every phase event loop follows the inside cycle, process.nextTick(), and promises also a way to schedule the callback.
* The inside cycle has a higher priority than the outer.

```
   const fs = require('fs')


   const a = 100;


   setImmediate(() => console.log("Immediate"));


   fs.readFile('./a.txt', 'utf-8', (data) => console.log("data"))


   setTimeout(() => console.log("timeout"), 0)


   function printA() {
       console.log("a: ", a)
   }


   process.nextTick(() => console.log("process"))


   printA();


   console.log("sync code end")


   // Output:
   // a: 100
   // sync code end
   // process
   // timeout
   // Immediate
   // data
```

* Browser vs. Node.js Event Loop:
    * The browser and Node.js both use an event loop, but there are differences. In browsers, the event loop continuously checks for tasks to execute (e.g., UI rendering, user input events).
    * In Node.js, the event loop also runs continuously, but there are certain phases (like timers, pending callbacks, and polling) that determine what happens next.

* Poll Phase in Node.js:
    * The poll phase in Node.js is where it waits for incoming I/O operations (e.g., reading from a file or network requests). When the event loop reaches this phase and there are no tasks, it will wait (block) if necessary until new tasks come in or a timer expires. However, this is not the case with all phases.

```
 const fs = require('fs')


   setImmediate(() => console.log("Immediate Outside"))


   setTimeout(() => console.log("Timeout Outside"), 0)


   Promise.resolve().then(() => {


       console.log("Promise Outside")


       process.nextTick(() => {
           console.log('Promise Outside => Process')
       })
   })


   fs.readFile('./a.txt', 'utf-8',
       () => {
           setImmediate(() => console.log("Immediate Inside"))


           setTimeout(() => console.log("Timeout Inside"), 0)


           process.nextTick(() => console.log("Process Inside"))


       })


   process.nextTick(() => {


       console.log("Process Outside")


       process.nextTick(() => console.log("Process Outside => Process"))
   })




   console.log("sync code end")


   // Output
   // sync code end
   // Process Outside
   // Process Outside => Process
   // Promise Outside
   // Promise Outside => Process
   // Timeout Outside
   // Immediate Outside
   // Process Inside
   // Immediate Inside
   // Timeout Inside
```

* https://nodejs.org/en/learn/asynchronous-work/event-loop-timers-and-nexttick

```
       ┌───────────────────────────┐
    ┌─>│           timers          │
    │  └─────────────┬─────────────┘
    │  ┌─────────────┴─────────────┐
    │  │     pending callbacks     │
    │  └─────────────┬─────────────┘
    │  ┌─────────────┴─────────────┐
    │  │       idle, prepare       │
    │  └─────────────┬─────────────┘      ┌───────────────┐
    │  ┌─────────────┴─────────────┐      │   incoming:   │
    │  │           poll            │<─────┤  connections, │
    │  └─────────────┬─────────────┘      │   data, etc.  │
    │  ┌─────────────┴─────────────┐      └───────────────┘
    │  │           check           │
    │  └─────────────┬─────────────┘
    │  ┌─────────────┴─────────────┐
    └──┤      close callbacks      │
       └───────────────────────────┘
```

## Thread Pool in libuv

* Thread Pool is used to handle operations that cannot be performed asynchronously in a non-blocking manner on the main event loop.
* By default, it has **4** threads, but you can configure this size using the **UV_THREADPOOL_SIZE** environment variable.
* It is used for 
    * **File System Operations:** Such as reading or writing files.
    * **DNS Resolution:** Performing DNS lookups.
    * **Cryptographic Operations:** Such as hashing or encrypting data.
    * **Other Blocking Operations:** Tasks that would otherwise block the event loop.
* Understanding and optimizing the use of the thread pool can help improve the performance of Node.js applications.
* How libuv interact with OS
    * All the networking done on sockets
    * Each socket have Socket Descriptor or File Descriptor
    * In our system we have - Scalable I/O event Notification Mechanism
        * epoll (Linux) - uses **reb-black tree DS**
        * kqueue (MacOS)
    * epoll descriptor is the collection of epoll descriptor or socket descriptor 

## Type of Database

* **Relational DB:** MySQL, PostgreSQL 
* **NoSQL DB:** MongoDB
* **In Memory DB:** Redis
* **Distributed SQL DB:** Cockroach DB
* **Time Series DB:** Influx DB
* **OO DB:** db4o
* **Graph DB:** Neo4j
* **Hierarchical DB:** IBM IMS
* **Network DB:** IDMS
* **Cloud DB:** Amazon RDS

### RDBMS (NoSQL, PostgreSQL)

* EF Codd 
    * Codd's  12 rules [0-12]
    * If database follow these rules then it is a **Relation Database**
* MySQL - Michael Widenius
    * 1 daughter - My - MySQL - Sun Microsystems - Oracle 
    * 2 daughter - Max - MaxDB
    * 3 daughter - Maria - Maria DB - when Acquires happens Michael fork of MySQL and make MariaDB
* PostgreSQL - Michael Ralph Stonebraker

### NoSQL (MongoDB)

* Types of NoSQL
    * Document DB - MongoDB
    * Key Value DB
    * Graph DB
    * Wide Column DB
    * Multi Model DB
* Mongo DB
    * In 2009
    * 10gen --> MongoDB Inc.

    ```
    +---------------------------------------------------------------+
    |        RDBMS (MySQL)          |      NoSQL (MongoDB)          |
    |---------------------------------------------------------------|
    |    Table, rows, columns       |  Collection, documents, fields|
    |---------------------------------------------------------------|
    |   Structured data             |   Unstructured data           |
    |---------------------------------------------------------------|
    |   Fixed schema                |   Flexible schema             |
    |---------------------------------------------------------------|
    |SQL(structured query language) |  Mongo(MQL) Neo4J(Graph)      |
    |---------------------------------------------------------------|
    |                               |   Easy to scale horizontally  |
    |   Tough horizontal scaling    |              +                |
    |                               |           vertically          |
    |---------------------------------------------------------------|
    |        Relationship           |   Nested Relationship         |
    |    Foreign keys + joins       |                               |
    |---------------------------------------------------------------|
    |   Read heavy applications     |   Real Times, Big Data        |
    |   transactions workloads      |   Distributed computing       |
    |---------------------------------------------------------------|
    |   Ex. Banking apps            |   Real time analysis          |
    +---------------------------------------------------------------+
    ```
