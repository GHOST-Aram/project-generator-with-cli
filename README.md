# Building a CLI based Project Generator with Node

Have you noticed the file overlap that exists between the projects that you are working on? You definitely know how tedious it is to keep creating the same directories, and files, and write the same configuration code every time you want to start a new project. 

What if you could simply execute a command and have it all ready within seconds? This could save you time and reduce your chances of making configuration mistakes in starting a new project. 

This article will guide you on how to create a local project generator application. This application will make your life a lot easier when it comes to starting new projects. Take your time, and your laptop, and let us have some fun building something interesting.

## What should you expect to learn from this article?
At the end of this article, you will have learned how to do the following:

- Create an application that generates projects when executed.
- Run post-creation processes like installing project starter modules.
- Create a custom command for generating projects globally on your computer.
- Link your custom command with your computer system to enable it globally.
- Execute your custom command to generate project starter files.


## What this article will not cover.
This article is not an introductory tutorial to any of the above technologies. You do not need to be an expert at any of the technologies. All the code snippets will be provided and explained to you.

Additionally, deploying your project generator application to make it accessible remotely through tools like `npx` is also beyond the scope of this article. 

## Target audience
This article is targeted at the upcoming Node JS and Typescript programmers. 

## Prerequisites
Before beginning to learn how to create a project generator, you should at least have basic knowledge of how to do the following:

- Create simple applications using node.js
- Use Typescript to add types to your code.
- Manipulate the file system using the `fs` module.
- Work with common bash commands like `cd`.
- Work with any node package manager, for example, `npm`.



Now that you know what to expect and what not to expect, let us go ahead and jump right into it. 

To create a project generator application, we will follow a series of steps in the following order:

1. Initialize a new project directory.
2. Create example templates.
3. Prompt the user for the required input.
4. Copy files from templates to a new project.
5. Run post-process commands
6. Configure global custom CLI command for generating new projects.


## 1. Initialize a new project directory.
Like any other project, we need a directory to work on. You can [clone or download](https://github.com/GHOST-Aram/node-ts-starter) a ready-made project starter directory from my GitHub repository. You also can go ahead and create a Typescript and node project directory on your own. If you don't know how to create one, visit [this article](https://dev.to/ghostaram/initializing-project-directory-for-node-and-typescript-1f8e) to learn how.


## 2. Create example templates.
For demonstrations, we will need a few templates. Let's create them.

In the interest of uniformity and clear communication. I will suggest that we create the templates as follows:

i). Create a directory called `templates` in the root directory of your project.

ii). Inside the `templates` directory, create another directory called `simple-project-one`.

iii). Open `simple-project-one` and create a file `index.ts`

iv). Open `index.ts` and add any code snippet to avoid leaving it empty.

v). In `simple-project-one`, create a directory with a name of your choice. Inside the nested directory, create a file and populate it with some code.

vi). On your terminal, navigate to `simple-project-one` and generate a `package.json` file using the following command.

```
npm init -y
```

vii). Repeat the above steps to create one more template called `simple-project-two`.

Up to this point, you should have a template directory structure that looks like this.

![Templates directory structure](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8nbg8vrm9za5bn6mqxqy.png)

That's all we need. Let us prompt the user for input in the next step.

## 3. Prompt user for required input
We need the following values to copy files from the templates into a new project:
- The template name. 
- The new project name.

We will use an `npm` module called `prompts` to collect user input. 

We will perform this exercise in the following order:

a). Install required dependencies.

b). Create a file and import the required dependencies.

c). Create prompt questions.

d). Test it.

### a). Install required dependencies.
Install the `prompts` module by running the following command:

```
npm install prompts
```

The prompts module does not come with types. We need to install them as dev dependencies using the following command.

```
npm i -D @types/prompts
```


### b). Create a file and import the required dependencies.

Create a file called `index.ts` in the `src` directory of your project.

Import the function `prompts` and the type `PromptObject` into `index.ts` file.

```
import prompts, { PromptObject } from "prompts";
```
### c). Create prompt questions.
Getting user input involves asking questions of the `PromptObject` type. Let's create them.

Below is the code snippet for the `questions` array:

```
const questions: PromptObject[] = [
    {
        type: 'text',
        name: 'projectName',
        message: 'What is the name of your new project .',
        validate: name => name.trim().length < 1 || typeof name !== 'string'?
            'Project name is required': true
        
    },
    {
        type: 'select',
        name: 'templateName',
        message: 'Which template do you want to generate',
        validate: name => name.trim().length < 1 ?
            'Template name is required': true,
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
The list contains two questions for two values:
`projectName` 
`templateName`

We add a validation function on `projectName` to ensure we don't get empty strings or non-string values. 

We also validate `templateName` to ensure the user does not leave it blank.

### d). Test it.
Let's try querying the user and printing the answers on the console to see what we are dealing with.

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

The expected output example.
```
{ projectName: 'new-project', templateName: 'simple-project-one' }
```

## 4. Copy files from templates to the new project
Let us use the input to copy data from the template to the project. In this step, we will use the `fs` and `path` modules to read from templates and write them into the new project directory.

This step is the core part of the exercise. We will divide it into the following steps.

a). Define a new function and call it.

b). Read the templates directory.

c). Remove files and folders to be skipped.

d). Create the new project directory.

e). Read each template file and write to the project.

Let us start by defining and calling the function.

### a). Define a new function and call it.
We will create a function called `replicateTemplates` and call it inside the `.then` callback. The function will take two arguments `templatePath` and `projectPath`.


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

### b). Read the templates directory.
We will use the `readdirSync` function of the `fs` module to get the names of all files and directories within the specified template directory.

```

const replicateTemplates = (
    templatePath: string, projectPath: string
    ) =>{
        //Get template file names
        let templateContentNames = fs.readdirSync(templatePath)
}
```

### c). Remove files and folders to be skipped.
You would not want to copy directories like:
`node_modules`
`build`
 `dist`
Let us remove them from the list.

```
...

    //filter out skip list
    const filesToBeSkipped = ['node_modules', 'build', 'dist']

    templateContentNames = templateContentNames.filter(
        name => !filesToBeSkipped.includes(name)
    )

```

### d). Create the new project directory.
We need a new project to copy template contents into. We will use the value of the `projectPath` and `mkdirSync` function of the `fs` module to create a new directory. We will first check if a directory with the same path name exists before we create a new one. 

If the directory does not exist, we want to create a new one; otherwise, we want to exit gracefully.

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

To achieve the objective of this step, we will execute actions inside the `forEach` callback function in the following order:

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

We need the `Stats` object to check if the name is a file name or a directory name. Are you interested in knowing more about the `Stats` object? Visit the [nodejs documentation](https://nodejs.org/api/fs.html#class-fsstats) to find out.


iii). Check if the name represents a file. If true, read the data and write on a new project file.
```
...
if(stats.isFile()){
    const content = fs.readFileSync(originPath, 'utf8')
    fs.writeFileSync(destinationPath,content)

}
``` 

iii). Check if the name represents a directory. If true, call the `replicateTemplates` function recursively. Use the current `originPath` and `destinationPath` as template path and project path respectively

```
...
    else if (stats.isDirectory()){
        replicateTemplates(originPath,destinationPath)
    }
```


You have successfully implemented the template replication logic. You can run the program to ensure it generates the new project. 

## 5. Run post-process commands.
In most cases, after generating project files, you will want to run some commands. In this step, we will write a program to install npm modules for the generated project. 

To achieve the objective of this step, we will carry out the following actions:

a). Install required dependencies.

b). Import the required dependency and write the program.


### a). Install required dependencies.
To run shell commands, we need to install one more dependency called `shelljs` and its `types` as follows:

```
npm i shelljs
npm i -D @types/shelljs
```



### b). Import the required dependency and write the program.
Import `shells` on top of your `index.ts` file and add the following code snippet at the bottom of the main block of the `replicateTemplates` function.



```
...

import shell from 'shelljs'

... 

    //Run post process commands
    
    if(templateFilesNames.includes('package.json')){

        //Navigate to the new project directory
        shell.cd(projectPath)

        //Run npm install
        console.log('Running npm install')
        shell.exec('npm install')
    }
    
    ...
```
Run your code to verify that the `npm install` command is executed.

## 6. Configure global custom CLI command.
This step is where we configure the global custom command. You can run this command to create new projects from your templates. The command will generate your templates regardless of the location that you execute it from within your file system. 

We will carry out the task in the following steps:

a). Add a [shebang] (https://en.wikipedia.org/wiki/Shebang_(Unix)) line to `index.ts.

b). Add the custom command to `package.json`.

c). Link the command to your system.

d). Build your code.

e). Test it.

### a) Add a shebang line to `index.ts`
Add the following line on top of your `index.ts`. The shebang(#!) should be the first line in your `index.ts` file.


```
#! /usr/bin/env node

import prompts, { PromptObject } from "prompts";
import fs from 'fs'
import path from "path";

...
```

The shebang line instructs your system to execute the `index.ts` file using the Node interpreter.

### b). Add the custom command to `package.json`

Next, we add a command to  `package.json`. Open the `package.json` file and add the following object.

```
"bin": {
    "generate-project": "./build/index.js"
  },
```

This command does not have to be called `generate-project`. You can name it something else and assign the path to the file you want to execute it.

### c). Link the command to your system.
Make your system aware of this command by executing the following npm command on your terminal.

 ```
    npm link 
 ```

If you are using a Unix-based operating system you might need to execute it as a super user with 

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

Finally, you have successfully created a project generator app. You have also globally installed the app on your system. The code is available on my [Github repository](https://github.com/GHOST-Aram/project-generator-with-cli). Happy coding. Thanks for reading my content. Please comment if you found it useful or if there is anything you would like me to do better next time.