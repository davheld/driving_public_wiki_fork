As our project is now hosted on github, here is the workflow. This page is intended for the members of the `collaborators` team, i.e. those with read-only access to the repository. Members of the `developers` team have write access and should know what they are doing ;)

## Basic steps

### Step 1: fork the repository

Because you don't have write access to the `StanfordDrivingTeam/driving` repository, you need to create your own copy of it on github. On the main repository's page, click on the "Fork" button in the upper right corner. This redirects you to your own copy (here we will call it `brice/driving`).

### Step 2: get a local copy on your computer

clone your own copy of the repository (`brice/driving`) to your computer so that you can make changes and test them. Copy the clone URL in the lower right corner. Then use `git clone git@github.com:brice/driving.git` on your local machine.

It is recommended that you setup your SSH keys and clone using the SSH URL for a password less access to your github repos (see https://help.github.com/articles/which-remote-url-should-i-use)

### Step 3: edit, test, commit

On your computer you can now edit and test the code. Commit and push to `brice/driving` for backup and synchronization across different computers.

### Step 4: share your modifications (pull request)

Once you are satisfied with your changes, push them to `brice/driving`, and create a pull request to notify the developers of the `StanfordDrivingTeam/driving` repository: [[https://help.github.com/articles/creating-a-pull-request]]. Your changes will be reviewed and potentially merged into the main repo; or you will be asked to make some modifications.


## More advanced

### integrating with `rosinstall`

If you followed the installation instructions from [[Software installation]], you should have a local copy of the main repository, to which you don't have access. You need to switch to using your own repository instead.

So first, open the `.rosinstall` file and change the corresponding line to use your own repository instead. Then in the repo itself:

```
git remote set-url origin git@github.com:brice/driving.git
```

### updating your own repo with the changes from the upstream repo

As developers are pushing commits to `StanfordDrivingTeam/driving` your copy drifts away.
- you don't have the latest bug fixes and features
- you are developing on code that is potentially outdated, resulting in difficult to merge situations

Therefore, it is desirable to keep your copy in sync with the main copy. Unfortunately, github does not offer this functionality through the web interface. The solution is to add an additional remote to your repo that will point to the upstream repo:
```
git remote add upstream git@github.com:StanfordDrivingTeam/driving.git
```

Then to get and merge the latest changes from upstream:
```
git pull upstream
```
