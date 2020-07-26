# Getting Started with Node, Typescript and Docker

Pre-requisites:

- Node is installed

## Node Setup

Let's set up a barebones Node project

Create our Node project. Can skip through all the prompt options.

```bash
mkdir project
cd project
npm init
```

A file called package.json will be set up with the options you chose.

```json
{
  "name": "project",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC"
}
```

The main executable scfript is not created automatically. Create it.

```bash
touch index.js
```

Put something basic in index.js for test purposes.

```javascript
console.log('Hello World');
```
Test it from the cli:

```
node index.js
```

Which gives you some console output:

    Hello World

## Typescript Setup

Lets add Typescript to our barebones Node project
