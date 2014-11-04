---
layout: post
title: "Help -- SA-CORE-2014-005 Hack"
date: 2014-10-30 16:17:01 -0500
comments: true
categories: [drupal, hack, security, sql injection]
---

This might at least help find malicious code that could have been injected in module files. Does a cool side-by-side diff and exports to a file so you can easily move through the file and see any differences.

In the following command make sure to put the 'good' drupal install before the potentially 'bad' or existing install.

In this case 'drupal' is just a
```
git clone --branch 7.x http://git.drupal.org/project/drupal.git
```
and a
```
git checkout 7.32
```
and 'pre-update' is my old drupal install that I want to check for malicious code.

In this case the "drupal" directory will be on the left. Just switch them around to see the other directory on the left. I chose to do it this way because I wanted to see everything for the the "good" code and see just lines that were different in what I had before the update.

```
diff -r -y --left-column drupal pre-update  > dirdiff.txt
```

This does a recursive (-r) diff and places the results side-by-side (-y) in a file. The (--left-column) makes it easier to spot the diff.

On mac you can just hold down ```fn + (down arrow)```

You should at least see:

{% img center http://scottalan.github.io/images/mine/diff.png %}

This is the patch that was posted for Drupal core: https://www.drupal.org/files/issues/SA-CORE-2014-005-D7.patch

Also
------------

Something else you can do for any code you may have been version controlling (we all version control our custom code, right?):

I looked back through my git branches, as I tend to keep them for quite sometime, and I found a branch that I had labeled updates-10-13-14 so I know that these are any updates I may have pushed on the 13th of October. This is two days before the 'catastrophe' so this code shouldn't be affected. I'm working with the Panopoly distro as my base install so all of my custom code exists in sites/all and that's what I push to GitHub. In the sites directory I placed a directory called: old_all. So here is what I did:

From your sites directory:

```
mkdir old_all
cd old_all
git init
git remote add origin git@github.com:scottalan/[NAME_OF_REPO].git
git fetch origin
git checkout origin/updates-10-13-14
```

Now I have my PRE 'possible hack' code in old_all and where ever I am now in the original all directory. Now it's just a matter of running the diff command.

```
diff -r -y --left-column old_all all  > all-diff.txt
```

You could also exclude certain files to give you less to look through:
```
diff -r -y --exclude=*.css --left-column old_all all  > all-diff.txt
```

This would exclude any CSS files for example.


Now I can see a side by side diff of the changes. Of course you will see things that you recognize changing but keep an eye out for anything that doesn't seem quite right.

Sure this is tedious but a heck of a lot better then trying to look through each file individually.

Good Luck! Don't forget to checkout: https://www.drupal.org/drupalsa05FAQ

-Scott
