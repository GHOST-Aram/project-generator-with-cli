# Building a CLI based Project Generator Using Typescript and Node

Have you noticed the files overlap that exists between your personal projects that you are working on? You definitely know how tedious it is to keep creating the same directories, files, write the same configuration code every time you want to start a new project. 

What if you could just execute a command and have it all ready within seconds? This could save you time and reduce your chances of making configuration mistakes in starting a new project. 

This article will guide on how to create your own local project generator application. This application will make your life a lot easier when it comes to starting new projects. Take your time, your laptop and let us have some fun building something interesting.

## What should you expect to learn from this article?
At the end of this article, you will have learnt how to do the following:

- Create an application that generates projects when executed.
- Run post creation processes like installing project starter modules.
- Create a custom command for generating projects globally on your computer.
- Link your custom command with your computer system to enable it globally.
- Execute your custom command to generate project starter files.


## What this article will not cover.
This article is not an introductory tutorial to any of the above technologies. You do not need to be an expert by any means because all the code snippet will be provided for you.

Additionally, deploying your project generator application to make it accessible remotely through tools like `npx` is also beyond the scope of this article. 

## Target audience
This article is aimed at the upcoming node js and Typescript programmers. 

## Prerequisites
Before begining to learn how to create a project generator, you should at least have basic knowledge on how to do the following:

- Create simple applications using node.js
- Use Typescript to add types to your code.
- Manipulate the file system using `fs` module.
- Work with common bash commands like `cd`.
- Work with any node package manager eg `npm`.



Now that you know what to expect and what not to expect, let us go go ahead and jump right in to it. 

To create a project generator application, we will follow series of steps in the following order:

1. Initialize new project directory.
2. Create example templates.
3. Prompt user for required input.
4. Copy files from templates to new project.
5. Run post process commands
6. Configure global custom CLI command for generating new project.


## 1. Initialize new project directory.
Like any other project, we need a directory to work on. You can [clone or download](https://github.com/GHOST-Aram/node-ts-starter) a ready made project starter directory from my Github repository. You also can go ahead and create a Typescript and node project directory on your own. If you don't know how to create one, visit [this article](https://dev.to/ghostaram/initializing-project-directory-for-node-and-typescript-1f8e) to learn how.


## 2. Create example template.
For the purposes of demonstation we will need a few templates. Let's create them.

In the interest of uniformity and clear communication. I will suggest that we create the templates as follows:

i). Create a directory called `templates` in the root directory of your project.

ii). Inside the `templates` directory, create another directory called `simple-project-one`.

iii). Open `simple-project-one` and create a file `index.ts`

iv). Open `index.ts` and add any code snippet just to avoid leaving it empty.

v). In `simple-project-one`, create a directory with a name of your choice. Inside the nested directory, create a file and populate it with some code.

vi). On your terminal, navigate to `simple-project-one` and generate `package.json` file using the following command.

```
npm init -y
```

vii). Repeate the above steps to create one more template called `simple-project-two`.

Up to this point, you should have a template directory structure that looks like this.

![Templates directory structure](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8nbg8vrm9za5bn6mqxqy.png)

That's all we need, lets go ahead and prompt the user for some input.

## 3. Prompt user for required input
We need two values to copy files from the templates into a new project directory;  a template name and the new project name.

We will use an `npm` module called `prompts` to collect user input. 

This exercise will be carried out in the following order:

a). Install required dependencies.

b). Create a file and import the required dependencies.

c). Create prompt questions.

d). Test it.

### a). Install required dependencies.
Install `prompts` module by executing

```
npm install prompts
```

Prompts module does not come with types. We need to install them as dev dependencies using the following command.

```
npm i -D @types/prompts
```


### b). Create a file and import the required dependencies.

Create a file called `index.ts` in the `src` directory of your project.

Import function `promps` and types `PromptObject` into `index.ts`

```
import prompts, { PromptObject } from "prompts";
```
### c). Create prompt questions.
Getting user input involves asking questions of `PromptObject` type. Let's create them.

Below is the code snippet for `questions` array:

```
const questions: PromptObject[] = [
    {
        type: 'text',
        name: 'projectName',
        message: 'What is the name of your new project .',
        validate: name => name.trim().length < 1 || typeof name !== 'string'?
            'Project name is required' : true
        
    },
    {
        type: 'select',
        name: 'templateName',
        message: 'Which template do you want to generate',
        validate: name => name.trim().length < 1 ?
            'Template name is required' : true,
        choices: [
            {
                title: 'Simple Project One',
                value: 'simple-project-one'
            },
            {
                title: 'Simple Project Two',
                value: 'simple-project-two'
            }
        ]
    }
];
```
The list contain two questions for two values ; `projectName` and  `templateName`.

We add validation function on `projectName` to ensure that we don't get empty strings or non-string values. 

We also validate `templateName` to ensure that the user does not leave it blank.

### d). Test it.
Let's try querying the user and printing the answers on console to see what we are dealing with.

```
prompts(questions )
.then((answer) => console.log(answer))
.catch(error => console.error(error.message))

```

Run the following command to execute your code.

```
npm run dev
```

Executing the code snippet above will open a command line interface with question 1. Fill in your desired values for `projectName` and press enter.

Expected output example.
```
{ projectName: 'new-project', templateName: 'simple-project-one' }
```

## 4.Copy files from templates to new project
Let us now use the input to copy data from the template to the project. In this step, we will majorly use the `fs` and `path` modules to read from templates and write into the new project directory.

This is the core part of the exercise. We will divide it into the following steps.

a). Define a new function and call it.

b). Read templates directory.

c). Remove files and folders to be skipped.

d). Create the new project directory.

e). Read each template file and write to the project.

Lets start by defining and calling the function.

### a). Define a new function and call it.
We will create a function called `replicateTemplates` and call it inside the `.then` callback. The function will take 2 arguments `templatePath` and `projectPath`.


Define the function:
```
const replicateTemplates = (
    templatePath: string, projectPath: string
    ) =>{

}
```

Call the function:
```
prompts(questions )
.then((answers) => {
    const projectName = answers.projectName
    const templateName = answers.templateName

    const projectPath = path.join(process.cwd(), projectName)
            
    const templatePath = path.join(process.cwd(), 'templates', templateName)

    replicateTemplates(templatePath, projectPath)} 
    )
.catch(error => console.error(error.message))
```

### b). Read templates directory.
We will use the `readdirSync` function of the `fs` module to get the names of all files and directories within the specified template directory.

```

const replicateTemplates = (
    templatePath: string, projectPath: string
    ) =>{
        //Get template files names
        let templateContentNames = fs.readdirSync(templatePath)
}
```

### c). Remove files and folders to be skipped.
You normally would not wan't to copy directories like `node_modules`, `build` and `dist`. Let us remove them from the list.

```
...

    //filter out skip list
    const filesToBeSkipped = ['node_modules', 'build', 'dist']

    templateContentNames = templateContentNames.filter(
        name => !filesToBeSkipped.includes(name)
    )

```

### d). Create the new project directory.
We need a new project to copy template contents into. We will use the value of `projectPath` and `mkdirSync` function of the `fs` module to create a new directory. We will first check if a directory with the same path already exists before creating a new one. 

If the directory does not already exist,we want to create an new one; otherwise, we want to exit gracefully.

```
...

if(!fs.existsSync(projectPath)){
        fs.mkdirSync(projectPath)
    } else{
        console.error(
            'Directory already exists. Choose another name'
        )
        return
    }
```


### e). Read each template file and write to the project.
The magic you have been waiting for happens here. We will iterate through the `templateContentNames` list to read the content of each file and write them into project files. 

To achive the objective of this step, we will execute actions inside the `forEach` callback function in th following order:

i). Create complete paths for the content origin and destination.

```
...

templateContentNames.forEach(name =>{
    const originPath = path.join(templatePath, name)
    const destinationPath = path.join(projectPath,name)
})
```

ii). Get the `Stats` object for each name.

```
...
templateContentNames.forEach(name =>{
    ...
    const stats = fs.statSync(originPath)
})
```

We need the `Stats` object to check if the name is a file name or a directory name. Are you interested in knowing more more about `Stats` object?.Visit the [nodejs documentation](https://nodejs.org/api/fs.html#class-fsstats) to find out.


iii). Check if the name represents a file. If true, read data and write on a the new project file.
```
...
if(stats.isFile()){
    const content = fs.readFileSync(originPath, 'utf8')
    fs.writeFileSync(destinationPath,content)

}
``` 

iii). Check if the name represents a directory. If true, call `replicateTemplates` function recursively. Use the current `originPath` and `destinationPath` as template path and project path repectively

```
...
    else if (stats.isDirectory()){
        replicateTemplates(originPath,destinationPath)
    }
```


You have successfully implemented the template replication logic. You can run it one more to make sure it does exactly what you expect before we can make it executable globally.

## 5. Run post process commands.
In most cases, after generating project files, you will want to run some commands. In this step we will write a program to install npm modules for the generated project. 

To achieve the objective of this step, we will carry out the following actions:

a). Install required dependencies.

b). Import the required dependency and write the program.


### a). Install required dependencies.
To run shell commands, we need install one more dependency called `shelljs` and its `types` as follows:

```
`npm i shelljs` 
`npm i -D @types/shelljs`
```



### b). Import the required dependency and write the program.
Import `shells` on top of your `index.ts` file and add the following code snippet at the bottom of the main block of `replicateTemplates` function.



```
...

import shell from 'shelljs'

... 

    //Run post process commands
    
    if(templateFilesNames.includes('package.json')){

        //Navigate to current directory
        shell.cd(projectPath)

        //Run npm install
        console.log('Running npm install')
        shell.exec('npm install')
    }
    
    ...
```
Run your code once more to verify that `npm install` command is executed.

## 6. Configure global custom CLI command.
This is where we configure the global custom command. You can run this command to create new projects from your templates. The command will generate your templates reguardless of the location that you execute it from within your file system. 

The task is broken down in to the following steps:

a). Add a [shebang] (https://en.wikipedia.org/wiki/Shebang_(Unix)) line to `index.ts.

b). Add the custom command to `package.json`.

c). Link the commmand to your system.

d). Build your code.

e). Test it.

### a) Add a shebang line to `index.ts`
Go ahead and add a the following line on top of your `index.ts`. The shebang(#!) line should be the very first thing in your `index.ts` file.

```
#! /usr/bin/env node

import prompts, { PromptObject } from "prompts";
import fs from 'fs'
import path from "path";

...
```
The shebang line instructs your system to execute the `index.ts` file using `node` interpreter.

### b). Add the custom command to `package.json`

Next, we add a command to  `package.json`. Open `package.json` file and add the following object.

```
"bin": {
    "generate-project": "./build/index.js"
  },
```

This does not have to be called `generate-project`. You can name it something else as long as it holds the path to the file you want to execute.

### c). Link the command to your system.
Make your system aware of this command by executing the following npm command on your terminal.

 ```
    npm link 
 ```

If you are using a Unix based system you might need to execute it as a super user with 

```
sudo npm link
```

### d). Build your code.
In this step, we will compile the Typescript code into JavaScript. This action produces the `build` directory. Run either of the following commands to compile Typescript.

```
npm run build
```
or 

```
tsc
```
### e). Test it.
After generating the `build` directory. Go ahead and test your command on the terminal.

Run

```
generate-project
```

Finally, you have successfully created a project generator app.You have also globally installed the app on your system. The code is available on my [Github repository](https://github.com/GHOST-Aram/project-generator-with-cli). Happy coding. Thanks for reading my content. Please leave a comment if you found it useful or if there is anything you would like me to do better next time.


