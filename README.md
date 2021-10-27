# Git Basics
## What is Git
### Decentralised Version Control System  
Each instance of a repository is completely stand alone and separate from the authority of a central server. 
This means a developer is free to work on their local copy of the source code, without interference from other members of the team; e.g. files being locked for editing etc.  
It is _only_ by convention that we use a central service (e.g. GitHub, GitLab, BitBucket) as the authoritative controller for the code base.
### Track Changes
Git is used to record changes to a file base, as commits, and provide tools to manage those changes. As these changes are logged with a checksum at commit time, this is also a secure log of changes and authors.
* View the history of changes
* Merge changes from two or more sources
* Revert the file base back to a point in its history
## Concepts
### Repository
The file base we are controlling.
### Commit
A snapshot of the file base, (or the action to create one, see below) that is immutable and has a link to the previous commit.  
As each commit contains a reference to the previous commit, we can build the history of the branch, and as each commit is immutable we can ensure the history is secure and valid e.g. if you changed a historical commit, the chain of history would be broken.  
__Note__ unlike other version control systems this is *not* a delta, but a complete snapshot, making it easier to use and more powerful than other SVC applications.
### Branch
A pointer to a commit in the git history, creating a lightweight copy of the main file base that may have diverged from the master version. 
Git can manage an unlimited number of branches, although you can only have one branch checkout at any one time, and differentiates between local branches and remote branches e.g. `master` (local) is seen as a separate branch to `master` (remote) even though the two are 'linked'.
These are used to perform changes and are 'merged' back to the master once the work is complete/stable.  
It is common to have one or more branches defined (again purely by convention) as special branches such as 'master' which acts as the default branch.
Git should always be on a branch, unless doing advanced actions beyond the scope of this document.
### Tags
Another way of holding a pointer to a commit, typically used to 'tag' releases of a semantic version.
### File Status  
Files in a Git repository will have a _status_, defining what actions Git can perform on them.
* untracked  - git knows the file exists but will perform no actions unless instructed
* unmodified - git is tracking the file but there are no current changes 
* modified - git is tracking the file, there are current uncommitted changes but changes will not be committed
* staged - there are current changes that will be committed
Due to the way git works, files can have a dual status of modified and staged, when changes have been made after a stage.
## Basic 101 Operations
### clone
Gets a copy of a new remote repository on your local machine.  
Example: ```git clone git@github.com:Tommy647/git_basics.git```  
### branch
#### View current and List known 
```git branch```  
 ```git branch --remote```  view with (known) remote branches  
The current branch is marked with `*`
#### Create new local branch
To create a new local branch ```git checkout -b <BRANCH_NAME>```
#### Create a new local branch, from an existing remote branch
To create a local copy of a remote branch we can just check it out ```git checkout <BRANCH_NAME>``` -- note no `-b`
If this command errors, your local git instance may need to get the latest data from the server, see `fetch` below.
#### Change local branch 
```git checkout <branch_name>```
### status
Check the current status of the file system as git sees it   
```git status```   
(I personally prefer to add the `-s` flag for nicer output)
### add
Add changes to the staged status in git ready to commit e.g. add a new file or add changes to existing files  
```git add <file_name> <file_name>```   
```git add .``` which adds everything, only use this once you are confident with git
### commit
Takes the staged changes and commits them  
```git commit``` opens up your editor ready to add a commit message, commits on save  
```git commit -m 'your message here''``` if you prefer to leave a commit message inline  
```git commit -a ``` *danger* adds all unstaged files and starts the commit, I rarely use this

If you want to change the default editor used for commits
```git config --global core.editor vim|nano|emacs``` works with most standard text editors, even gui editors, just write, save and close

### push  
Push your new commits to the server  
```git push```   
This may throw an error the first time you push a new branch that does not exist on the remote:  
```git push --set-upstream origin <branch_name>``` you should only need to run this command once for each new branch
## Basic Team operations
### fetch
Get the latest data from the remote server, i.e. updates to and new branches. Note this does not change any data in your local branches, just updates the references git is aware of.
```git  fetch```
### merge   
Get the changes from another branch and (try) to merge them into the current branch  
```git merge <branch>```  
This is possibly my least used command, as there are easier ways to manage this.
### pull
Git pull performs a git fetch and a git merge (using the current branch and an upstream if one is set), updating your local copy of a branch to match the latest remote copy.  
```git pull```  
This command I tend to use over the above.
## When things go wrong
### conflicts
When attempting to merge changes, git will attempt to merge file changes automatically.   
Sometimes this will fail, git will halt the operation in a temporary state for the user to solve. This usually ends with a commit being reauthored, but should only happen on local branches where the history is not yet canonical.
### reset - DESTRUCTIVE
Completely messed something up and want to get back to a working state?  
`git checkout <file_name>`  undo all changes to the file, reverting it to the last committed state
Really, really messed up and want to get everything back to the last commit?   
`git reset --hard <branch>` reset everything back to the latest commit on a branch  
You can also you this command if your local branch is completely out of sync with a remote e.g.
`git reset --hard origin/develop` sets your current branch to the state of the remote develop branch


## Typical Workflow
### Branching
`master` is the canonical copy of the code base, in a releasable state. No one writes to this branch, code is merge into here once tested in `develop`.  
`develop` the testing/working branch, merges into master once stable. All features branch off from the latest commit here and merge back to develop  
`feature-xyz` a local development branch, where work is carried out, tested and then merged back into `develop`
### Starting work
```shell
git checkout develop # move to the develop branch
git pull # make sure I have the latest updates
git checkout -b feature-awesome-changes # create a new local branch
git push --set-upstream origin feature-awesome-changes
```
Work on the code, until I hit one of the following milestones:
* I have added some code, it compiles and tests pass (note: not necessarily feature complete)
* I have to leave the code for a meeting, lunch etc...
* I have added code, it is feature complete, compiles and tests pass
I commit, and I push to the remote
```shell
git status -s # check the changes, anything unexpected
git add . # add the changes
git commit -m 'Useful message that tells my teammates what changed and a brief why' # make the commit 
git push # send the changes to the remote
```
Commit and push, little and often - it is cheap.

### Merging the changes
Code is then prepared for a Peer Review, via a Pull Request (PR) which takes place on the remote repository (GitHub/GitLab).  
Opening the PR should trigger the CI/CD pipeline, to run the tests/linters/integration tests ready to build a release.  

If the PR is approved, the branch is merged into the develop branch on the remote repo. It is a good practice here to alert other team members the main branches have updated.
## Tools
### Command line
### Configuring via the command line
```git config ```

```git config --global core.editor <EDITOR>```
### Gui
[GitHub Desktop](https://desktop.github.com/) - Windows/OSx [unofficial linux](https://github.com/shiftkey/desktop)  
[Sourcetree (Atlassian/Bitbucket)](https://www.sourcetreeapp.com/)
## Advanced
### rebase TODO    

### squash TODO  Gus Question
