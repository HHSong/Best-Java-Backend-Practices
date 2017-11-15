# Git - Version Control
 -- by Hao Hsiang Song

I would simply say version control is such an essence in programming that if a company does not have that, don't even go there. Imagine deploy codes using a USB drive. That would be interesting...

---

#### 1. Stage, Distributed, Bla Bla ..
So typical git tutorial has so much jargon and comparison with svn. Yeah, it is good to know the difference. Yet, all I care for the beginners are just how to make files being controlled. That's easy! Or not... Programming sometimes is annoying when first getting things set up. I guess you just have to do what you have to do. After that two simple commands may do the job: git add [filePath], git commit -m "msg goes here". What happens here is git internally has a convoluted algorithm that stores the file information in a .git folder under current folder.

#### 2. Git is not fun playing alone
Git is especially powerful in collaborations. Conflicts arise when multiple people edit the same version of the files and try to have their commit pushed to the remote repository. Git is smart enough to tell the difference between lines. However, it falls back to the programmer's job to resolve the conflicts. Thankfully there are bunch of tools and IDE simplifies the work.

#### 3. Branching
We branch because we want codes being checked in but not reflected to everyone. Or sometimes we want codes being protected from changes. They are feature branching and release branching. Feature branching has new codes developed in new branches. And when the feature is ready, we normally merge dev branch and send a pull request the changes being merged to dev branch. 

#### 4. Move folder is painful


#### 5. Rebase vs Merge