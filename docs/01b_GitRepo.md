# GIT repository for the site we will build (optional)

You need somewhere to save the work done on the site created throughout the day. I recommend you use Github for this as pushing to a repository on Github means you have an off-pc backup of the site you can refer to in future.

If you don't want to create a Github repository for the site you will build during the course then the site can be copied to a USB key before leaving at the end of the day. If you don't have your own USB key, one might be able to be provided - please check with your instructor at this time.

Some of you will be familiar with Github, others not so much, but its pretty easy to use. Please create an account on <a href="http://github.com" target="\_blank">http://github.com</a> if you don't already have one and then create a repository to push code changes to throughout the day.

* Create repository on Github, perhaps the name is training-museum
* Git init in the root of the site on the command line
```
git init
```
* Add a remote to the github repository you created. The link needed below can be found by clicking the Green "clone or download" button and copying the HTTPS link...
```
git remote add origin <repo link>
```
* Add all files
```
git add .
```
* Do an initial commit
```
git commit -m "Initial Commit"
```
* Do the first push
```
git push
```

You should now have the base for the site we will create commited in to the github repo you created. If you go to the repository on Github in your browser you should now see a number of files.

# Next

[Lesson 02 - Project Structure](02_SiteProjectStructure.md)