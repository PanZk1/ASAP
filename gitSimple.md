# start-git
Git is the most popular version control system which records the changes made to our project over time in a special database called a repository. We can look at our project and see who has made what changes when and why and if we screw something up we can easily revert our project back to an earlier state.

![sketch of basic workflow](<Screenshot from 2023-09-22 17-24-59-1.png>)

There are 4 space for you to work with git
- workspace
  you can create a dictionary on your computer and put your project in it
  - $ mkdir ~/projects/ASAP
  - initialize the git into the existing project
    - we should go to the project directory  such as /ASAP then we should run the following command in the terminal.
    - $ cd ~/projects/ASAP/

    - $ git init
---
- staging area
  when you make some changes in your project you should add it to the staging area.

  - The git add command is used to place the modified version of the working directory in the staging area.
  - $ git add <file-name> or git add .
    git add . means add all the files in the current directory to the staging area.
---
- local repository
- git commit command is used to place the staged version of the working directory in the local repository [commit the modified files which are available in the staging area.]
  - $ git commit -m "commit message"
- The git status command is used to show the status of the git repository. This command displays the state of the local directory and the staging area.
  $ git status


- if you want to remove the file from the staging area you can use the following command
  - $ git rm --cached <file-name>
  - if you want to remove the file from the staging area and the local repository you can use the following command
  - $ git rm <file-name>
---
- remote GitHub repository
  remote github repository can be divided in  main branch and other branches.you can communicate with other developers and share your project with them.

 - Connect to the remote GitHub repository:
   - $ git remote add origin https://github.com/your_username/your_repository.git
    - $ git remote add origin <remote-repository-URL>









#### ohmyzsh 
$ vim .zshrc