*Coming from Github? A better viewing experience of this tutorial can be had at the site below: https://pokedpeter.dev*

# Welcome!

**This tutorial will show you how to build a barebones Node (Javascript) project from scratch with the following goodies:**
  - **Typescript** - for type checking
  - **Nodemon** - for automatic project reload when you make code changes 
  - **ESLint** - for checking code quality and optionally, fixing it
  - **Pretter** - for code formatting
  - **Docker** - for app containerization to have a consistent deployment environment

**Pre-requisites:**

- Node is installed.
- Docker is installed
- You are using a debian based Linux distro
- Optional: You are using VSCode for your editor

## Node / Javascript project setup

Let's set up a barebones Node project

Create our Node project. Can skip through all the prompt options.

```bash
mkdir project
cd project
npm init
```

A file called `package.json` will be set up with the options you chose. You can skip the interface options and go with the defaults by running `npm init -y`

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

The main executable script is not created automatically. Create it.

```bash
touch index.js
```

Put something basic in `index.js` for test purposes.

```javascript
console.log('Hello World');
```
Test it from the cli:

```
node index.js
```

Which gives you some console output:

```
Hello World
```

### Install packages

Javascript doesn't come with any standard libraries built-in. The recommended approach is to install npm packages. Let's try an example. We'll install [Lodash](https://lodash.com), a popular library of handy utilities.

```
npm install lodash
```

Here's the output:

```
added 1 package, and audited 4 packages in 987ms

found 0 vulnerabilities
```

Now that we've intalled Lodash we can now use it in our app. Open up `index.js` again and update to match:

```
const _ = require('lodash);

console.log(_.snakeCase('Hello World'));
```

Run it and you'll see the following output:

```
hello_world
```

View the `package.json` file again and notice the new `dependencies` section with the lodash entry:

```json
"dependencies": {
  "lodash": "^4.17.21"
}
```

> [!TIP]
> To quickly view the file contents from the cli, type: `cat package.json` 

View your project files and notice a `node_modules` folder has been created:

```bash
$ ls -l
index.js
node_modules
package-lock.json
package.json
```

This folder is where your dependencies are stored. Let's check them out:

```bash
$ ls node_modules
@types  lodash  typescript
```

## Typescript setup

Lets add Typescript to our barebones Node project.

Install the typescript dependency, as a dev depedency. All typescript dependencies are only required during development so we do `--save-dev`

```bash
npm install --save-dev typescript
```

Install Typescript type definitions for Node:

```bash
npm install --save-dev @types/node
```

> [!TIP]
> Install multiple packages in one go by combining them on a single line: `npm install --save-dev typescript @types/node` 

Have a look at the dependencies in `package.json`. Here's the relevant portion:

```json
{
  "devDependencies": {
    "@types/node": "^20.4.9",
    "typescript": "^5.1.6"
  }
}
```

Initialise typescript by running the following command anywhere within your project.

```bash
npx tsc --init
```

This will create a `tsconfig.json` file with default settings:

```
Created a new tsconfig.json with:

  target: es2016
  module: commonjs
  strict: true
  esModuleInterop: true
  skipLibCheck: true
  forceConsistentCasingInFileNames: true
```

We use `npx` which executes locally installed binaries that have been installed via `package.json`.

> [!WARNING]
> Some installation guides will recommend installing Typescript globally `sudo npm install -g typescript`. I'd recommend to install a local version (ie. inside your project folder)
>
>The global version may end up differing from the local version installed for your project. Running `tsc` directly uses the global version. When `tsc` is run as part of npm in your project, it uses the local version.

There are several options set by default in `tsconfig.json`. There is a lot of commented out options - not shown below.

```json
{
  "compilerOptions": {
    /* Visit https://aka.ms/tsconfig.json to read more about this file */

    /* Basic Options */
    "target": "es5",                          /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017', 'ES2018', 'ES2019', 'ES2020', or 'ESNEXT'. */
    "module": "commonjs",                     /* Specify module code generation: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', 'es2020', or 'ESNext'. */

    /* Strict Type-Checking Options */
    "strict": true,                           /* Enable all strict type-checking options. */

    /* Module Resolution Options */
    "esModuleInterop": true,                  /* Enables emit interoperability between CommonJS and ES Modules via creation of namespace objects for all imports. Implies 'allowSyntheticDefaultImports'. */

    /* Advanced Options */
    "skipLibCheck": true,                     /* Skip type checking of declaration files. */
    "forceConsistentCasingInFileNames": true  /* Disallow inconsistently-cased references to the same file. */
  }
}
```

Some recommended tweaks to the defaults:

```json
"target": "es2015",     // I'd recommend es2015 at a minimum. es5 is ancient.
"outDir": "build",      // Keep our compiled javascript in the build directory
"rootDir": "src",       // Keep our source code in a separate directory
"noImplicitAny": true,  // We're using Typescript yeah? And not adding Typescript to an existing project. Enforce good habits from the start.
"lib": ["es2020"],      // Setting this excludes DOM typings as we are using Node. Otherwise you'll run into errors declaring variables like 'name'
```

In regard to `target`. Generally newer versions of node will support the newer ECMA features. The below website is a great resource to see what ECMA features are available to each version of Node:

https://node.green/

No need for index.js anymore:

```bash
rm index.js
```

Set up our project source code:

```bash
mkdir src
cd src
touch index.ts
```

Add something basic to `index.ts` to test it:

```javascript
console.log('Hello typescript!');
```

Compile our barebones project.

```bash
npx tsc
```

The compiled output, as javascript, can be found in the `/build` directory. It'll contain `index.js` mirroring our `src/index.ts`

Contents of index.js:
```javascript
"use strict";
console.log('Hello World!');
```

You are now ready to build Javascript projects with Typescript!

## Find & fix code issues with ESLint

### Command line linting

Until recently, tslint was the go-to Typescript code linter but it's now deprecated as the project has been consolidated into eslint. Here's the official homepage:

Website:

https://eslint.org

The Github site:

https://github.com/typescript-eslint/typescript-eslint

Install eslint (as a dev dependency of course)

```bash
npm install --save-dev eslint
```

Let's setup our lint config using eslint's init command:

```bash
npx eslint --init
```

Follow the prompts. We are using node, so no browser support is required.
It'll ask you if you want to install the dependent typescript plugins. Go ahead and do that.

```bash
✔ How would you like to use ESLint? · problems
✔ What type of modules does your project use? · esm
✔ Which framework does your project use? · none
✔ Does your project use TypeScript? · No / Yes
✔ Where does your code run? · node
✔ What format do you want your config file to be in? · JSON
The config that you've selected requires the following dependencies:

@typescript-eslint/eslint-plugin@latest @typescript-eslint/parser@latest
✔ Would you like to install them now with npm? · No / Yes
Successfully created .eslintrc.json file in /your/project
```

More information about lint configuration below:

https://eslint.org/docs/user-guide/configuring

Using the options selected above, our .eslintrc.json file looks like this:

```json
{
    "env": {
        "es2020": true,
        "node": true
    },
    "extends": [
        "eslint:recommended",
        "plugin:@typescript-eslint/recommended"
    ],
    "parser": "@typescript-eslint/parser",
    "parserOptions": {
        "ecmaVersion": 11,
        "sourceType": "module"
    },
    "plugins": [
        "@typescript-eslint"
    ],
    "rules": {
    }
}
```

### Coding style guides (optional)

You might have noticed one of the questions during the eslint init process was this:

```bash
? How would you like to use ESLint? …
  To check syntax only
▸ To check syntax and find problems
  To check syntax, find problems, and enforce code style
```

The last option additionally enforces a code style. If you select that option, a follow up question will be:

```bash
? How would you like to define a style for your project? …
▸ Use a popular style guide
  Answer questions about your style
  Inspect your JavaScript file(s)
```

If you opt to use a popular style guide, you'll then have a choice from the following:

```bash
? Which style guide do you want to follow? …
▸ Airbnb: https://github.com/airbnb/javascript
  Standard: https://github.com/standard/standard
  Google: https://github.com/google/eslint-config-google
  XO: https://github.com/xojs/eslint-config-xo
```

- Airbnb: https://github.com/airbnb/javascript
- Standard: https://github.com/standard/standard
- Google: https://github.com/google/eslint-config-google
- XO: https://github.com/xojs/eslint-config-xo

I'd recommend researching the above style guides and go with one that aligns with your preferences.

### ESLint ignore file

Next, we'll create another config file in plaint text to allow us to exclude files and directories from linting:

```bash
touch .eslintignore
```

And add the following contents to the file:

```
node_modules
build
```

We don't want linting on our compiled javascript code.

### Breaking the rules

By default, the standard rules are enabled. See the ticked items in the list at:

https://eslint.org/docs/rules/

For example, let's try breaking the `no-extra-semi` rule.

Try adding a semi-colon to the end of the line in index.ts and do the lint check to see an error:

```typescript
console.log('Hello typescript');;
```

And then:

```bash
npx eslint src
```

Which results in:

```
  1:34  error  Unnecessary semicolon  @typescript-eslint/no-extra-semi

✖ 1 problem (1 error, 0 warnings)
  1 error and 0 warnings potentially fixable with the `--fix` option.
```

> [!WARNING]
> When installing the Airbnb option, I've noticed eslint needs to have the .ts file extension passed:
>
> ```bash
> npx eslint src --ext .ts
> ```

Create a script for this in the `package.json` so we can conveniently call it:

```json
"scripts": {
  ...
  "lint": "eslint src --ext .ts"
},
```

Applicable only when on npm < v7 but note that if you run the npm script and issues are found, you will see the following npm error message appended below the eslint output:

```bash
> eslint src --ext .ts


/home/user/dev/test/src/index.ts
  1:1   warning  Unexpected console statement        no-console
  1:21  error    Missing whitespace after semicolon  semi-spacing
  1:22  error    Unnecessary semicolon               no-extra-semi

✖ 3 problems (2 errors, 1 warning)
  2 errors and 0 warnings potentially fixable with the `--fix` option.

npm ERR! code ELIFECYCLE
npm ERR! errno 1
npm ERR! test@1.0.0 lint: `eslint src --ext .ts`
npm ERR! Exit status 1
npm ERR! 
npm ERR! Failed at the test@1.0.0 lint script.
npm ERR! This is probably not a problem with npm. There is likely additional logging output above.

npm ERR! A complete log of this run can be found in:
npm ERR!     /home/user/.npm/_logs/2021-08-18T15_27_55_318Z-debug.log
```

I do not see the npm error when on npm v7 (eg. when using Node 16.6.2)

### Adding linting rules

Each rule can be one of three states:

|Rule Mode    |Description |
|:------------|:-----------|
|0 or "off"   |Disables the rule|
|1 or "warn"  |Warning, linter won't fail|
|2 or "error" |Error, linter will fail|

Rules can be added as keys to a rules object in the lint config file .eslintrc.json:

```json
{
  "root": true,
  "parser": "@typescript-eslint/parser",
  "plugins": [...],
  "extends": [...],
  "rules": {
    ..your rules go here..
  }
}
```

Find rules at eslint.org:

https://eslint.org/docs/rules/

Let's test out the stylistic rule named 'comma-dangle'. We want to warn the user if an array with multiple lines is missing a comma on the last item.

Add the rule:

```json
"rules": {
  "comma-dangle": [
    "warn", {
      "arrays": "always-multiline"
    }
  ]
}
```

Read the rule details on the website. There's quite a bit of customisation allowed for many rules. We want to blanket enforce dangling commas in all scenarios in this example.

Change index.ts with the following code:

```typescript
const movies = [
  'Lord of the Flies',
  'Battle Royale'
];
movies.pop();
```

Run the linter:

```bash
npx eslint src
```

Now we should see the following warning:

```bash
  3:18  warning  Missing trailing comma  comma-dangle

✖ 1 problem (0 errors, 1 warning)
  0 errors and 1 warning potentially fixable with the `--fix` option.
```

### Fix linter issues

Note in the output there's an option to fix the issue. Try running the linter with the `--fix` option:

```bash
npx eslint --fix src
```

There's no output from the linter this time and if we check `index.ts` we'll see that the dangling comma has been added automatically:

```javascript
const movies = [
  'Lord of the Flies',
  'Battle Royale', // <-- dangling comma added
];
movies.pop();
```

## Format code with Prettier

We'll use Prettier to format code. It's an opinionated code formatter that supports many languages including Typescript, Javascript and other formats you may use for configs like JSON and YAML.

https://prettier.io

Alternatively, you can stick with ESLint to stylistically format your code. Prettier differs in that it doesn't modify your code, unless you set one of the small handful of options.

Install the module:

```bash
npm install --save-dev prettier
```

Create the config file. This lets editors and other tools know you are using prettier:

```bash
echo {} > .prettierrc.json
```

You probably won't need to add any options to the config file as most will involve transforming your code. Best leave that to ESLint and just let Prettier deal with formatting.

Create an ignore file. This lets the prettier cli and editors know which files to exclude from formatting.

```bash
touch .prettierignore
```

Add the following lines to `.prettierignore`:
```bash
node_modules
build
```

Test prettier with the following command. It won't overrite anything just output the formatted code:

```bash
npx prettier src
```

Based on our last change to index.ts, the output will be:

```javascript
const movies = ["Lord of the Flies", "Battle Royale"];
movies.pop();
```

We can see that the multi-line array has been formatted into a single line.

Write the changes to file by repeating the command with the option `--write`:

```bash
npx prettier --write src
```

This will list the files that have been formatted:

```bash
src/index.ts 279ms
```

### Make Prettier and ESLint work together

Given both ESLint and Prettier can format code, you can expect some conflicts to occur. Prettier has created rules specifically for ESLint that basically disables any rules that are unnecessary or conflicting when combined with Prettier.

The first one is `eslint-config-prettier`: 

https://github.com/prettier/eslint-config-prettier

This config turns off all rules that are unnecessary or conflict with Prettier. Install it with the following command:

```bash
npm install --save-dev eslint-config-prettier
```

Then, add it to the last line of your `extends` section in the ESLint config, `.eslintrc.json`:

```javascript
{
  "extends": [
    "eslint:recommended", 
    "plugin:@typescript-eslint/recommended",
    "plugin:@typescript-eslint/eslint-recommended",
    "prettier" // <-- Add this
  ],
}
```

You can run the following command on any file to check that there's no conflict between ESLint and Prettier:

```bash
npx eslint-config-prettier src/index.ts
```

If all goes well, you should get the following respone:

```bash
No rules that are unnecessary or conflict with Prettier were found.
```

Next one to install is `eslint-plugin-prettier`:

https://github.com/prettier/eslint-plugin-prettier

Runs Prettier as an ESLint rule and reports differences as individual ESLint issues. Install it with the following command:

```bash
npm install --save-dev eslint-plugin-prettier
```

Then update your `.eslintrc.json` file as follows:

```json
{
  "plugins": ["prettier"],
  "rules": {
    "prettier/prettier": "error"
  }
}
```

## Install VSCode plugins

### ESLint

Search for the below plugin:

`ESLint` - by Dirk Baeumer

### Prettier

Search for the below plugin:

`Prettier - Code formatter` - by Prettier

Press `Ctrl + Shift + I` to format code. You'll be prompted to select the default formatter. Select Prettier as your default.

### Prettier ESLint

Search for the below plugin:

`Prettier ESLint` - by Rebecca Vest

## Add Node scripts

These scripts are tailored to our typescript project. We are only checking .ts files.

Add the below commands to the `scripts` section of your `package.json`.

```json
"start:dev": "nodemon"
```

### ESLint

```json
"lint": "eslint --ext .ts src"
"lint-fix": "eslint --fix --ext .ts src"
```

### Prettier

```json
"pretty": "prettier --write 'src/**/*.ts'"
```

## API framework with Curveball

Curveball is a micro framework for building APIs in node. It's built from the ground up with support for Typescript as opposed to its more popular predecessor Koa.

https://curveballjs.org/

Install it:

```bash
npm install @curveball/core
```

Copy the example from the website into `index.ts`. Only thing we'll change is the port number. For two reasons. One, port 80 may be blocked. Two. Running on different ports allows us to have multiple node projects running simultaneously.

```typescript
import { Application } from '@curveball/core';

const app = new Application();
app.use( async ctx => {
  ctx.response.type = 'text/plain';
  ctx.response.body = 'hello world';
});

app.listen(9000);
```

Start up the server in dev mode with the script we created previously:

```bash
npm run start:dev
```

You will get the following output as nodemon listens to file changes:

```bash
> project@1.0.0 start:dev /home/your_name/project
> nodemon

[nodemon] 2.0.4
[nodemon] to restart at any time, enter `rs`
[nodemon] watching path(s): src/**/*
[nodemon] watching extensions: ts,js
```

After making a code change, watch the server console output to be nodemon at work. Also refresh the webpage to see updates.

## Auto code reload with Nodemon

Set up automatic reload after code changes for development. Install `nodemon` to monitor for file changes and `ts-node` to run the typescript code directly instead of having to compile and then pass on to `node`.


```bash
npm install --save-dev ts-node nodemon
```

Add a `nodemon.json` config. This will configure nodemon to watch for changes to .ts and .js files inside your source code directory and then run the exec command after the changes.

```json
{
  "watch": ["src"],
  "ext": ".ts,.js",
  "ignore": [],
  "exec": "ts-node ./src/index.ts"
}
```

Add a npm script inside of `package.json` to kick off `nodemon` for development:

```json
  "start:dev": "nodemon"
```

Run `npm run start:dev` to start the reload process.

## Containers with Docker

Install docker and lookup instructions on how to run docker without sudo - instructions omitted here as they are OS / distro specific.

Create a `docker-compose.yml` file with a single node container as a service:

The name of our project is "project" as is the name of the service and the container. We have a volume mapping our project directory to /project inside the node container.
Lastly ensure the ports match the ports exposed in your application.

More details below:

https://docs.docker.com/compose/compose-file/

```yaml
version: '3'

services:
  project:
    build: .
    container_name: project
    volumes:
      - .:/project
    ports:
      - "9000"
```

Next create `Dockerfile` with the following contents.

```dockerfile
FROM node:12

WORKDIR /project
COPY package.json .
RUN npm install
COPY . .
CMD ["npm", "run", "start:dev"]
```

This sets up our project directory inside the container, installs our node packages, copies over the content from our native filesystem and starts up our Curveball server in dev mode.

Now bring up the container:

```bash
docker-compose up
```

You'll see the following output:

```
Creating network "project_default" with the default driver
Building project
Step 1/7 : FROM node:12
 ---> dfbb88cfffc8
Step 2/7 : WORKDIR /project
 ---> Running in 86fff3a3c90b
Removing intermediate container 86fff3a3c90b
 ---> 5912fd119492
Step 3/7 : COPY package.json .
 ---> 4fa4df04cc6b
Step 4/7 : RUN npm install
 ---> Running in 8b814e4d75d2

...
(Node package installation happens here)
...

Removing intermediate container 8b814e4d75d2
 ---> 3bfd2b1a83e4
Step 5/7 : COPY . .
 ---> f6971fdf7fb5
Step 6/7 : EXPOSE 9000
 ---> Running in 2ab0a152b0a6
Removing intermediate container 2ab0a152b0a6
 ---> 0e883b79c1b3
Step 7/7 : CMD ["npm", "run", "start:dev"]
 ---> Running in f64884ae2643
Removing intermediate container f64884ae2643
 ---> 1abb8edf6373
Successfully built 1abb8edf6373
Successfully tagged project_project:latest
WARNING: Image for service project was built because it did not already exist. To rebuild this image you must use `docker-compose build` or `docker-compose up --build`.
Creating project ...
Creating project ... done
Attaching to project
project    |
project    | > project@1.0.0 start:dev /project
project    | > nodemon
project    |
project    | [nodemon] 2.0.4
project    | [nodemon] to restart at any time, enter `rs`
project    | [nodemon] watching path(s): src/**/*
project    | [nodemon] watching extensions: ts,js
project    | [nodemon] starting `ts-node ./src/index.ts`
```

You can use `CTRL-C` to stop the container.

Use `docker-compose up -d` to bring the container up in detached mode - it's brought up in the brackground and you're free to continue using the command line.

We are creating a container based on node v12. Our working directory inside the container is defined as `/project` (where our project code will be mapped to)

## Install a specific version of Node

The version of node installed will depend on the OS installed and how up to date its packages are.

If you use Node outside of the docker containers and work on multiple Node projects each requiring different versions of Node then install nvm:

https://github.com/nvm-sh/nvm

## Reference

### Node Scripts

## Credit

A big thanks to the following for helping me create this tutorial.

- Static document generator [Docsify](https://docsify.js.org)
- Hosting on [Netlify](https://www.netlify.com/)
- Alerts plugin for Docsify: https://github.com/fzankl/docsify-plugin-flexible-alerts

## Changelog

- 2023/08/11 - Updated Javascript section with more content
- 2023/08/04 - Fix typo referencing incorrect filename
- 2021/08/18 - Add some info about Airbnb option
- 2021/08/10 - Add section on ESLint style guides
- 2021/08/09 - Updated section on eslint-config-prettier due to v8 changes

