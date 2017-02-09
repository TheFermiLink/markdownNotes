#Git Notes

##Starting a basic gitflow

To initialise a new Git repository, first you need to type `git init`. Following this you can easily clone a repository hosted on github by doing
```bash
git clone https://github.com/64BitTypewriter/markdownNotes.git
```
This will give you a local copy of the repository, which you can easily keep up-to-date with regular
```bash
git pull origin master
```
where in this case, "master" is the name of the branch you would like to pull from.

Alternatively, if you intend to also edit and contribute to the project, then you'll need to make sure you have an SSH key. This will let you "push" your changes to the GitHub repository. [Advice about SSH keys](https://help.github.com/articles/connecting-to-github-with-ssh/).

While you're plugging along with your code, you'll want to eventually commit your changes. To do this you should first
```bash
git add file_name
```
or alternatively if you want to add everything in the repository simply,
```bash
git add .
```

If you will often be adding several files you can include a file in your repository called a `.gitignore`. This can be a convenient way to exclude certain files or file-types from being added to a git commit. [Sample gitignore](https://gist.github.com/octocat/9257657).

Once you've added or ignored all of the files you wanted, you need to commit your changes:
```bash
git commit -m "your message"
```
The `-m` tag is important, as this attaches a message to your commit, helping others understand why you have recently added changes.

The last step is to push the changes to the repository. You can do this with:
```bash
git push origin branch_name
```
Some other useful commands for interacting with git can include:

`git branch` which returns the name of the branch you are on
`git checkout branch_name` will switch you to a new branch
`git status` returns a list of changed files which are tracked by git

Git can be complicated, but rather useful. There are many resources online for troubleshooting and help.

When creating a new repository, make sure to tell git which repository you'd like to link to, when you want to push:
`git remote add origin git://github.com/my_user_name/my_repo.git`.

If you have a forked repository and would like to pull upstream then you can simply  do:
`git remote add upstream git://github.com/secret_project/dev.git` and then `git pull upstream branch_name`.
