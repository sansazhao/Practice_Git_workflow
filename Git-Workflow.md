>       姓名：赵樱 
>       学号：516030910422
####Git - Distributed Workflows
####Do What?      —— Version Control

#####Git vs SVN ——Distributed vs Centralized
Unlike Centralized Version Control Systems (CVCSs), the distributed nature of Git allows you to be far more flexible in how developers collaborate on projects. In centralized systems, every developer is a node working more or less equally on a central hub. 
######Centralized Workflow
![](http://upload-images.jianshu.io/upload_images/8742043-cf6c98f7688b99a3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/440)
######Distributed Workflow : Git
![workspace → stage → repository
](http://upload-images.jianshu.io/upload_images/8742043-05322789818a9fd9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###Start workflow
#####1.init & clone
```
$ ssh user@host
$ git init --bare/path/test.git
```

![a central repository on the server](http://upload-images.jianshu.io/upload_images/8742043-cd689878e39042f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```
$ git clone git://github.com:xxxx/test.git //readonly  
$ git clone git@github.com:xxx/test.git   // read&write
$ git clone https://github.com/xxx/test.git  // read&write
```
![clone to all developers](http://upload-images.jianshu.io/upload_images/8742043-0fdd70d236555998.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#####2.commit 
Note:*Only local! Not concerned with the central repo!*
```
$ git status //check stage
$ git add //save temperarily
$ git commit -m "message"
```

###Merge Workflow
>The merge workflow works by **copying branches** between upstream and downstream. 

- *Upstream* can merge contributions into the official history
- *Downstream* can base their work on the official history.

There are three main tools that can be used for this:
- git-push :copy your branches to a remote repository, usually to one that can be read by all involved parties
- git-fetch :copies remote branches to your repository
- git-pull :fetch and merge in one go.
Note that you should not use git pull unless you actually want to merge the remote branch.

######Push: Publishing branches / topics
*Use:*` $ git push <remote> <branch> `

Git provides the git-request-pull to send preformatted pull requests to upstream maintainers to simplify this task.
![push example](http://upload-images.jianshu.io/upload_images/8742043-44c792d590c0de9c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/440)
![push to central repo](http://upload-images.jianshu.io/upload_images/8742043-4b06b8ab4555edcb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/240)


If you just want to get the newest copies of the integration branches, staying up to date is easy too:
######Push/pull: Staying up to date
*Use :*`$ git fetch <remote> or git remote update `
Then simply fork your topic branches from the stable remotes as explained earlier.
If you are a maintainer and would like to merge other people’s topic branches to the integration branches, they will typically send a request to do so by mail. In that case, git pull can do the fetch and merge in one go, as follows.
######Push/pull: Merging remote topics
*Use:*`$ git pull <url> <branch>`
Occasionally, the maintainer may get merge conflicts when he tries to pull changes from downstream. In this case, he can ask downstream to do the merge and ***resolve the conflicts themselves*** (perhaps they will know better how to resolve them). It is one of the rare cases where downstream should merge from upstream.
![get the up-to-date information](http://upload-images.jianshu.io/upload_images/8742043-59f43f9eeb01bc19.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/440)
######Branch & Merge
```
$ git checkout -b branchname   //create a new branch
$ git checkout master    //change to master 
$ git merge --no-ff branch_name  //merge into master
```
![merge](http://upload-images.jianshu.io/upload_images/8742043-c4b0cf0ed5ca3670.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/400)

#####Pull requests
Aside from isolating feature development, branches make it possible to discuss changes via pull requests. Once someone completes a feature, they don’t immediately merge it into master. Instead, they push the feature branch to the central server and file a pull request asking to merge their additions into master. This gives other developers an opportunity to review the changes before they become a part of the main codebase.
- Code review is a major benefit of pull requests, but they’re actually designed to be a generic way to talk about code.  
- Once a pull request is accepted, the actual act of publishing a feature is much the same as in the *Centralized Workflow*. First, you need to make sure your local master is synchronized with the upstream master. Then, you ***merge*** the feature branch into master and ***push*** the updated master back to the central repository.
#####Example
![Person A has a rest ](http://upload-images.jianshu.io/upload_images/8742043-1e40c41fed2e7c5f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![Person A finishes her feature and push ](http://upload-images.jianshu.io/upload_images/8742043-4530921551513397.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![Person B receives pull request](http://upload-images.jianshu.io/upload_images/8742043-7e24f8380afb87dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
Person A & B have some back and forth via pull request 
![Person A make changes ](http://upload-images.jianshu.io/upload_images/8742043-87c9d7f8cdcf735e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/540)


![Person A publishes her feature](http://upload-images.jianshu.io/upload_images/8742043-8de8228dc1a2508c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



###Patch workflow
If you are a *contributor* that sends changes upstream in the form of emails, you should use topic branches as usual. Then use git-format-patch to generate the corresponding email.
######Publishing branches/topics
`$ git format-patch -M upstream..topic //to turn them into preformatted patch files`
`$ git send-email --to=<recipient> <patches>`
If the maintainer tells you that your patch no longer applies to the current upstream, you will have to ***rebase your topic*** (you cannot use a merge because you cannot format-patch merges):
######Keeping topics up to date
`$ git pull --rebase <url> <branch>`
You can then fix the conflicts during the rebase. Presumably you have not published your topic other than by mail, so rebasing it is not a problem.
If you receive such a patch series (as maintainer, or perhaps as a reader of the mailing list it was sent to), save the mails to files, create a new topic branch and use git am to import the commits:
######Importing patches
`$ git am < patch`
One feature worth pointing out is the ***three-way merge***, which can help if you get conflicts: `git am -3 `will use index information contained in patches to figure out the merge base. 

###Tag
```
$ git tag version
$ git tag -a v1 -m "version1" xxx //with description
$ git tag //check all tags 
```
![Example of Tag](http://upload-images.jianshu.io/upload_images/8742043-acbd4dcc5606339b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/400)

###Gitignore
> A gitignore file specifies intentionally untracked files that Git should ignore. Files already tracked by Git are not affected.

![Example of Gitignore](http://upload-images.jianshu.io/upload_images/8742043-32bd6f642575b795.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/440)

####Conclusion 
Git is about version control for source code, which can be optionally stored or backed up on a server. Most of the time, however, each developer will be using Git locally on his personal workstation.  Git is not inherently a client-server program (though it does have some optional networking capabilities), so it doesn't require a server.
![Git Workflow](http://upload-images.jianshu.io/upload_images/8742043-d8bf5b0dfb337db2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



[You can click here to check my repository.](https://github.com/zhaoying98sjtu/Practice_Git_workflow)

######Reference:
>https://segmentfault.com/a/1190000002918123
  http://www.jianshu.com/p/50892fac6cbc
https://git-scm.com/docs/gitignore
https://git-scm.com/docs/gitworkflows
https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow
