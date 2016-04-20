---
layout: post
title:  "Rsync Script"
date:   2014-12-18 18:05:42
tags: ["front"]
---

Previously, to update my Jekyll blog, I had to manually copy my files to my Cyberduck connection to my AWS EC2 server. That took too much time. I wrote a script to update my local blog files to my remote from the command line.

In order to update files from local to server the .pem key is required. The -i option allows the .pem key to be used, which I copied from my .ssh directory into the same location as my script. The first path is the files to be copied (be sure to include the / after the path to copy the contents and not just the directory), then the path after the os and ip address is the file path to the files on the server to be synced.

My script:
rsync -rave "ssh -i karisite.pem" karisite/_site/ ubuntu@54.148.101.140:/var/www/html

How to run the script:

First check the permissions on the script filename.
`ls -l blog_update.sh`
Make sure at least the owner has permission to execute; I also gave the group permission.
`chmod 774 blog_update.sh`
Then run the script:
`./blog_update.sh`

I've included these commands because one character can make or break a script and patterns that work are helpful to follow when you are first working with linux.
