In the serialization of the full set of learning tutorials for the mall project, [Follow the Official Account](#Public number) get it immediately.

# Git operation in IDEA, it is enough to read this article!

> When you use Git, you will choose a Git client. This client is built into IDEA, which allows you to easily operate without using Git commands. This article will talk about some common Git operations in IDEA.

## Environmental preparation

- Before use, you need to install a remote Git repository and a local Git client, specific reference: [Build your own Git repository in 10 minutes](https://mp.weixin.qq.com/s/6GyYlR9lpVcjgYmHMYLi0w)。
- Since the Git plugin in IDEA depends on the local Git client, the following configuration is required:

![](../images/gitlab_screen_02.png)

## Operating procedures

> Here we use the source code of the mall-tiny project to demonstrate, as far as possible to restore a formal operation process.

### Create a project in Gitlab and add a README file

![](../images/gitlab_screen_22.png)

### clone project to local

- Open the interface to check out the project from Git:

![](../images/gitlab_screen_23.png)
- Enter the Git address to check out:

![](../images/gitlab_screen_24.png)
- The IDEA project is not generated temporarily because the project has not been initialized:

![](../images/gitlab_screen_25.png)

### Initialize the project and submit the code

- Copy the mall-tiny code into this directory:

![](../images/gitlab_screen_26.png)
- Here we need a .gitignore file to prevent some IDEA automatically generated code from being submitted to the Git repository:

```
# Maven #
target/

# IDEA #
.idea/
*.iml

# Eclipse #
.settings/
.classpath
.project
```
- Use IDEA to open the project:

![](../images/gitlab_screen_27.png)
- Right-click the project to open the menu and add all files to the temporary storage area:

![](../images/gitlab_screen_28.png)
- Add comments and commit code:

![](../images/gitlab_screen_29.png)

### Push the code to the remote repository

- Click the push button to push the code:

![](../images/gitlab_screen_30.png)
- Confirm the push content:

![](../images/gitlab_screen_31.png)
- Check the remote repository and find that it has been submitted:

![](../images/gitlab_screen_32.png)

### Pull code from remote repository

- Add a README-TEST.md file in the remote repository：

![](../images/gitlab_screen_33.png)
- Pull the code from the remote repository:

![](../images/gitlab_screen_34.png)
- Confirm pull branch information：

![](../images/gitlab_screen_35.png)

### Create branch from local and push to remote
- Create a dev branch locally and click the Git:master button in the lower right corner:

![](../images/gitlab_screen_36.png)
- Use push to push the local dev branch to the remote:

![](../images/gitlab_screen_30.png)
- Confirm the push content:

![](../images/gitlab_screen_37.png)
- Looking at the remote repository, it is found that the dev branch has been created:

![](../images/gitlab_screen_38.png)


### Branch switch
- Switch from the dev branch back to the master branch:
 
![](../images/gitlab_screen_39.png)

### Git file conflict resolution

- Modify the remote repository code:

![](../images/gitlab_screen_40.png)
- Modify the local repository code:

![](../images/gitlab_screen_41.png)
- Commit the local repository code and pull it, and find that the code conflicts, click Merge to merge:

![](../images/gitlab_screen_42.png)
- Click the arrow to merge the left and right codes into the middle area:

![](../images/gitlab_screen_43.png)
- After the conflict merge is completed, click Apply to take effect:

![](../images/gitlab_screen_44.png)
- Commit the code and push it to the remote.

### Merge code from dev branch to master

- Modify the dev branch code in the remote repository:

![](../images/gitlab_screen_45.png)
- Pull the code in the local repository, choose to pull from the dev branch and merge:

![](../images/gitlab_screen_46.png)
- If conflicts are found, commit them and push them to the remote repository.

![](../images/gitlab_screen_47.png)

### View Git repository commit history

![](../images/gitlab_screen_48.png)

## No public

![Public account picture](https://kratos47mhs.github.io/images/logo.png)


