# Moving o3de/o3de objects to o3de/o3de-extras
This is a guide to how to move a folder with history from one git repo to another. Since an O3DE object is defined as a folder with an object json in the root, moving an o3de object from one git repo to another uses this same procedure. This is the procedure we will use to move gems (and other objects) out of the o3de/o3de to o3de/o3de-extras.

This method was tested on the aws-lumberyard/gallifrey repo which hosted 2 xr gems and a project before we used this procedure to move them to o3de/o3de-extras.

## Concept
What we want to do is isolate a folder by using git repo filtering. The idea here is we want to filter out everything we do not need for this object. Additionally, we want to filter in a way such that the destination will be in the correct folder relative to the root, i.e. where we want to see it in the destination repo. Next we add this filtered source repo as a remote to the destination repo. Then we can do a remote update and merge using the --allowunrealted-histories option. This will effectively put the filtered source repo folder where we want it in the destination repo. This also drags in all the git history of those source files into the destination repo.

* Note that we could do these one at a time or all at the same time, for this example we'll do all 3 at the same time.

* Note that it is important to use ___freshly cloned repos___ in order for the filtering to work correctly.

* Note always use ___filter-repo___, NEVER USE ___filter-branch___!

## Method
Create a ___fresh clone___ of the source gallifrey repo:
```
git clone https://github.com/aws-lumberyard/gallifrey.git
```
This is the structure of the ___freshly cloned___ gallifrey repo:
```
root
├───XR            <--This is a gem
├───OpenXRTest    <--This is a gem
└───OpenXRVk      <--This is a project
```
This git repo has three distinct objects (folders) we want to move into the extras repos. Copy the entire freshly cloned repo 3 times, once for each object (folder). Only operate on the copies so you don't have to re-clone the repo again if you make a mistake. Note that the repos could live anywhere locally and you can use full paths instead of relative ones like I did here. I will rename each of them when I copy them. I will append the object name I want to isolate to keep things straight here.
```
cp -R .\gallifrey .\gallifrey-XR
cp -R .\gallifrey .\gallifrey-OpenXRTest
cp -R .\gallifrey .\gallifrey-OpenXRVk
```
https://github.com/o3de/o3de-extras.git repo is the destination repo. So I will ___freshly clone___ it.
```
git clone https://github.com/o3de/o3de-extras.git
```
 o3de-extras follows the standard O3DE repo structure and looks like this:
```
root
├───Engines
├───Projects
├───Gems
└───Templates
```
We want the gallifrey XR gem to land in the o3de-extras Gems/ folder
```
root
├───Engines
├───Projects
├───Gems
|   └───XR     <-- We want it to land here
└───Templates
```
Not in the root like it is in the source repo currently.
```
root
├───Engines
├───Projects
├───Gems
|───XR     <-- NOT here
└───Templates
```
We want to tell git to filter ___and move___ it to a folder called Gems/

```
cd gallifrey-XR
git filter-repo --path XR --to-subdirectory-filter Gems/
```
* Note here that --path is the source repos relative path to the folder we want isolate and move. Since the XR folder is in the root, we just have to put "XR" here. If XR was inside a sub folder, say "Gems" folder, then we would have put "Gems/XR" here instead.
* Note here that we want to ___filter and move___ so we add the --to-subdirectory-filter option and give it the destinations relative path to where we want it to land, in this case we want the XR folder moved into the o3de-extras "Gems" folder under the root, so we just have to give it "Gems/" otherwise it would end up in the root.

Since we want the OpenXRTest project to land in Projects/ folder and not in the root like it is in source currently, we want to command git to ___filter and move___ OpenXRTest to a sub folder called Projects/
```
cd ../gallifrey-OpenXRTest
git filter-repo --path OpenXRTest --to-subdirectory-filter Projects/
```
Since we want the OpenXRVk gem to land in Gems/ and not in the root like it is in the source currently, we want to command git to ___filter and move___ OpenXRVk to a sub folder called Gems/
```
cd ../gallifrey-OpenXRVk
git filter-repo --path OpenXRVk --to-subdirectory-filter Gems/
```
After running these filtering commands:
gallifrey-XR should look like:
```
root
└───Gems
    └───XR
```
gallifrey-OpenXRVk should look like:
```
root
└───Gems
    └───OpenXRVk
```
gallifrey-OpenXRTest should look like:
```
root
└───Projects
    └───OpenXRTest
```
Now we want to merge them into o3de-extras. Make a copy of o3de-extras (so we always have the original and wont have to re-clone if we make a mistake). 
```
cp -R .\o3de-extras .\o3de-extras-merged
```
Set the filtered repos as remotes
```
cd o3de-extras-merged
git remote add XR ../gallifrey-XR
git remote add OpenXRTest ../gallifrey-OpenXRTest
git remote add OpenXRVk ../gallifrey-OpenXRVk
git remote update
```
Create a new branch to merge into allowing unrelated histories.
```
git switch -C xr_merge_branch
git merge --allow-unrelated-histories XR/main
git merge --allow-unrelated-histories OpenXRTest/main
git merge --allow-unrelated-histories OpenXRVk/main
```
Then push the branch:
```
git push origin xr_merge_branch
```
__Note: If there are LFS objects we need to upload them__

We are done with these remotes, so we can remove them from o3de-extras-merged.
```
git remote remove gallifrey-XR
git remote remove gallifrey-OpenXRTest
git remote remove gallifrey-OpenXRVk
```
Then we create our PR and we're done.

## Moving a gem from the o3de/o3de to the o3de/o3de-extras
Now that we understand what is going on lets move a gem from the core o3de/o3de repo to the extras o3de/extras repo. We will do the same thing as above, but for just one object this time.

For this example we will move (https://github.com/o3de/o3de.git)\Gems\Achievements to (https://github.com/o3de/o3de-extras.git)\Gems\Achievements.

[O3DE excepts pull requests from forks](https://docs.o3de.org/docs/contributing/to-code/git-workflow/). For the example I'll use my forks:

* https://github.com/byrcolin/o3de.git

* https://github.com/byrcolin/o3de-extras.git

We need a ___fresh clone___ of our ___forks___ and since we'll only be doing just one object we wont copy it, we will just clone it into a folder named achievements-gem and achievements-extras to keep it straight.
```
git clone achievements-gem https://github.com/byrcolin/o3de.git
git clone achievements-extras https://github.com/byrcolin/o3de-extras.git
```
In this case the relative source path "Gems/Achievements" and the destination relative path "Gems/Achievements" are the same, so we do not need to ___move___ the folder when we filter the repo like we did in the previous example.

Filter out the rest of the repo to isolate the needed gem folder:
```
cd achievements-gem
git filter-repo --path Gems\Achievements
```
Now achievements-gem repo looks like:
```
root
└───Gems
    └───Achievements
```
We'll add the newly filtered source repo, achievements-gem, as a remote to our destination repo achievements-extras and we will refer to it as "achievements-gem":
```
cd ../achievements-extras
git remote add achievement-gem ../achievements-gem
```
Update the remote:
```
git remote update
```
Create a new branch in the destination repo so we can PR this back in:
```
git switch -C move_achievements_gem_to_extras
```
Merge allowing unrelated histories:
```
git merge --allow-unrelated-histories achievements-gem/main
```
The branch should now have all the files with history in the correct location.

Push the branch:
```
git push origin move_achievements_gem_to_extras
```
__Note: If there are LFS objects we need to upload them__

Remove the remotes:
```
git remote remove achievements-gem
```
Then create your PR.

Once accepted you will have 2 versions of the Achievements gem; one in the core and one in the extras. This is ok, since they are identical it wont matter which one gets built to pass AR. You then delete Gems\Achievements from the o3de/o3de repo. Create your PR for its removal and you're done.
