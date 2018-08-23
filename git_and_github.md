GIT and GitHub
==============

Setup system to use SSH
-----------------------
- https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/
- this eliminates need to login when connecting to github from cli
- do this on the ec2 instance, not the local desktop

Create Machine User for Automation
----------------------------------
- create a new github user. Suggested here:
  - https://developer.github.com/v3/guides/managing-deploy-keys/#machine-users
- add machine user as a member of organization
- add machine user to a group with read access to desired repos
- create ssh credentials on the server where machine user needs to operate

setup, username, & password
---------------------------
- initial setup
  - `git config --global user.name "T. Brian Jones"`
  - `git config --global user.email "email_address@gmail.com"`
- cache username and password so you don't have to type it all the time
  - `git config --global credential.helper "cache --timeout=21600"`
  - timeout is in seconds
  - this must be done on each machine that you use


migrating from SVN
------------------

### Migrate without History
- `svn export /project/working/dir/ /exported/project/dir/`
- this will remove all SVN stuff from the project and put clean folders into /exported/project/dir/
- then just start a new git repo in that folder, or move these files to a git project
- **you lose all project history from svn**
  - we generally don't care with our old projects


using git with github
---------------------

### cloning
*this is done to checkout a repository the first time, from a remote location ( github ) to a local system ( the computer you are doing dev on )*
- git clone https://github.com/Industrial-Interface/repository_name.git ./local_directory_to_clone_into

### pushing
*push updates on a local machine to github ( the remote git server )*
- simple ( usually works fine ): git push
- explicit ( eg. when repsoitory contains branches ):
  - git push https://github.com/Industrial-Interface/repository_name.git local_branch_name:remote_branch_name
  - branch names are usually `master` unless other branches have been created and checked out locally ( checkout )

### pulling
*used to update a local repository with changes that exist in a remote repository ( on github )*
- simple ( usually works fine ): git pull
- explicit ( eg. when repsoitory contains branches ):
  - git pull https://github.com/Industrial-Interface/repository_name.git local_branch_name:remote_branch_name
  - branch names are usually `master` unless other branches have been created and checked out locally ( checkout )

### tagging
*tags are labels applied to repository versions for later references*
- view local tags ( git tag )
- add a local tag ( git tag -a tag_name -m "message about this tag"
- push local tags to remote repository ( git push --tags )
  - creating tags locally only adds them to the local repository.  You have to explicitly push tags to github to add them to the master branch.

### branching
*Branching creates a branched repository that can later be merged with other branches. It's kept "inside" the same repository.*
- view branches on local repository ( git branch )
- add a branch to the local repository ( git branch branch_name )
- switch local repository to a different branch ( git checkout branch_name )
  - this can be executed as soon as you create a new branch locally
- push a local branch to github
  - git push https://github.com/Industrial-Interface/repository_name.git local_branch_name:remote_branch_name
  - good overview of the complexities here: http://longair.net/blog/2011/02/27/an-asymmetry-between-git-pull-and-git-push/
  - the local and remote branch names are generally going to be the same
  - running this will create the new branch in GitHub

#### Clone A Specific Branch
- `git clone -b <branch> <remote_repo>`

### forking
*Forking creates a seperate repository which is linked to the parent repository so it's easy to pull updates to the parent into the fork.*
- use the fork option on github
- forking your own repo is a bit complicated and can't be done through the web interface: http://bitdrift.com/post/4534738938/fork-your-own-project-on-github


Working With Old Revs
---------------------

- get a file/folder from an old rev back into the current working branch
  - `git checkout sha-of-old-rev path/to/folder/or/file
  - Example Directory Restoration: `git checkout b94110b429c14b9eac40cd38d872cc79beca32c0 utilities/linkedin_position_extractor/`


Submodules
----------
*allows you to have git repos inside other git repos ( eg. api clients inside a project )*

### terminology
- superproject: the master repo with the embedded submodules ( embedded repos )
  - the superproject tracks the version of the submodule and will clone that version into itself when cloned as opposed to the most current version
- submodule: the repo that is embedded in the superproject

### working with submodules
- clone a repo into another repo: `git submodule add https://github.com/Industrial-Interface/cortex_php_api_client.git cortex_php_api_client`
- clone a repo that contains submodules: `git clone --recursive https://github.com/Industrial-Interface/cortex_company_crawler.git`
- work on each submodule ( repo ) as you would normally.
- work on the superproject as you would normally
  - when changes are made to a submodule, however, you will see changes that need to be commited in the submodule repo.  Simply commit these as normal.

### Helpful Articles
- overview: http://git-scm.com/book/en/Git-Tools-Submodules
- deleting a submodule: http://sermoa.wordpress.com/2011/11/30/git-remove-a-submodule/


Reverting Revs
--------------
*revert to an earlier rev and then commit that rev as head*

- SO Answer: http://stackoverflow.com/questions/1895059/revert-to-a-commit-by-sha-hash


Restoring a Cloned Repo to GitHub
---------------------------------
- Use Case: a repo was cloned. The repo was then deleted from GitHub. You want to restore that repo to GitHub maintianing it's hiustory and otherwise in it's original condition.
  - This is used by TBJ to archive old repos. He clones them, then zips them, then stashes them somewhere, then deletes them from Github.

### Method
- create the new repo on github ( do not create a readme and make sure it's empty )
  - this is where we're going to restore the repo
- cd into the cloned repo directory on the local machine
- `git remote set-url origin git://new.url.here` ( the url of repo we just created )
- `git remote -v` make sure the remote origin url was created succesfully
- `git push origin master`
- tutorials used to figure this out
  - https://help.github.com/articles/adding-an-existing-project-to-github-using-the-command-line
  - http://stackoverflow.com/questions/2432764/change-the-uri-url-for-a-remote-git-repository


Problems
--------

### Update to Master
- You want to get all files as they exist in the most recent rev of the master
- `git fetch --all`
- `git reset --hard origin/master`

### HEAD detached from 1c15ff1
- This means you are not currently attached to a branch
- `git checkout master`
- If you have local changes, you will have to rectify them first.
