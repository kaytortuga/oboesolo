Title: Breaking the Ice
Date: 2017-11-13 15:00
Modified: 2017-11-16 12:00
Category: How-to
Tags: howto
Summary: Gotta start somewhere.
Toc: <li>Starting with Pelican</li><li>Creating and Viewing Local Content</li><li>Integrating with Github</li><li>Setting up S3</li><li>Uploading to S3</li>

Good afternoon and welcome to the latest version of my blog. I am your host, KayTortuga. And this is The OBOE Solo.

In this introductory article, I will outline the steps taken so that you, too, can create a blog quickly and simply using Pelican for theming, Markdown for creating content, Github for version control, and Amazon S3 web services for hosting.

# Starting with Pelican

I have a directory on a local linux machine where I like to keep source directories for repos. For info on Debian Jessie, see [https://www.debian.org/releases/jessie/amd64/](https://www.debian.org/releases/jessie/amd64/).

You will need a few packages to get things rolling. I like using tmux, emacs, and git, so I make sure those are on the box:

    apt-get install tmux emacs-nox git

Also install Pelican-specific packages:

    apt-get install pelican markdown

Once your machine is ready, open a terminal and navigate to where you would like to start. For me, that's /usr/src. Create the directory to hold the local version of your site then cd into it. Then let Pelican make the magic happen with the command 'pelican-quickstart':

    cd /usr/src
    mkdir blog_name
    cd blog_name
    pelican-quickstart

Pelican will ask you a number of questions that it will use to configure your basic site. I want to create it right here. I want to upload the site using S3.

    > Where do you want to create your new web site? [.] .
    > What will be the title of this web site? Blog Name
    > Who will be the author of this web site? Your Name or Alias
    > What will be the default language of this web site? [en]
    > Do you want to specify a URL prefix? e.g., http://example.com   (Y/n) n
    > Do you want to enable article pagination? (Y/n) n
    > What is your time zone? [Europe/Paris] America/Los_Angeles
    > Do you want to generate a Fabfile/Makefile to automate generation and publishing? (Y/n) y
    > Do you want an auto-reload & simpleHTTP script to assist with theme and site development? (Y/n) y
    > Do you want to upload your website using FTP? (y/N) n
    > Do you want to upload your website using SSH? (y/N) n
    > Do you want to upload your website using Dropbox? (y/N) n
    > Do you want to upload your website using S3? (y/N) y
    > What is the name of your S3 bucket? [my_s3_bucket] blog_name.com
    > Do you want to upload your website using Rackspace Cloud Files? (y/N) n
    > Do you want to upload your website using GitHub Pages? (y/N) n
    Done. Your new project is available at /usr/src/blog_name

This is where I like to pause. You've just set up the site. You have the basic configs. Time to initialize a local git repo, tell git what to ignore, and make an initial commit so you can see what changes moving forward. 

    git init
    emacs .gitignore

In .gitignore, list the types of files you don't want to track. The output, for instance, is generated and not edited by the user.

    *~
    \#*
    output
    *.pyc
    *.pid

Now, add all the things, check the status (Oooh and Aaah over all the cool things Pelican made for you), and commit!

    git add .
    git status
    git commit -m "Initial Commit"

# Creating and Viewing Local Content

Prepare to be shocked. Your content is written in the pelican-created directory called ... drum roll please ... content. So let's navigate there and create our first post.

    cd content
    emacs 20171113breakingtheice.md

That .md is for Markdown, which means each file will start out something like this:

     Title: Breaking the Ice
     Date: 2017-11-13 15:00
     Category: How-to
     Tags: howto
     Summary: Gotta start somewhere.
     Status: draft

     Good afternoon and welcome to the latest version of my blog. I am your host, KayTortuga. \
     And this is The OBOE Solo.

     In this introductory article, I will outline the steps taken so that you, too, can create\
      a blog quickly and simply using Pelican for theming, Markdown for creating content, Gith\
     ub for version control, and Amazon S3 web services for hosting.
     

There are a lot of cool things you can do with Markdown. A couple good resources are these [Markdown quick reference](https://en.support.wordpress.com/markdown-quick-reference/) and [Markdown syntax](https://daringfireball.net/projects/markdown/syntax) guides.

We have created a super simple post, but how do we see it? We are currently in the content directory, so let's back out to the main directory and start the devserver:

    cd /usr/src/blog_name
    make devserver

In your browser, your basic site will be at http://localhost:8000. Since we specified the post was a draft, you will need to go directly to http://localhost:8000/drafts/breaking-the-ice.html to find it. You may have noticed something. By default, the web path to the site is not based on the markdown file name but on the post's Title.

To stop the devserver so it can no longer be viewed in the browser:

    make stopserver

You can commit changes to your post any time:

    git status
    git add content/20171113breakingtheice.md
    git commit -m "Insert your message describing the changes made here"

The files are still stored on just your local machine. What if something happened to that box? Your code would be lost! Enter Github.

# Integrating with Github

Create your account at [https://github.com/join](https://github.com/join). Once your account is created and you're happily signed in, you will want to create a New Repository. Make the name something memorable but descriptive, maybe even your blog name. Since we have an existing local repo, leave the Initialize this repository option unchecked:

![Alt text](/images/githubinit01.png)

Once the repo is created, you will want to "push an existing repository from the command line" by adding the new github repository as the remote origin and pushing to it.

    cd /usr/src/blog_name
    git remote add origin https://github.com/your_github_account/your_github_repo.git
    git push -u origin master

And voi la! Everything you've committed so far is now stored up in Github.

# Setting up S3

This part actually took me a few because I didn't have an AWS account that I could use. So I did it the slow way. First, I created a simple new account at [https://www.amazon.com/ap/register](https://www.amazon.com/ap/register?_encoding=UTF8&openid.assoc_handle=usflex&openid.claimed_id=http%3A%2F%2Fspecs.openid.net%2Fauth%2F2.0%2Fidentifier_select&openid.identity=http%3A%2F%2Fspecs.openid.net%2Fauth%2F2.0%2Fidentifier_select&openid.mode=checkid_setup&openid.ns=http%3A%2F%2Fspecs.openid.net%2Fauth%2F2.0&openid.ns.pape=http%3A%2F%2Fspecs.openid.net%2Fextensions%2Fpape%2F1.0&openid.pape.max_auth_age=0&openid.return_to=https%3A%2F%2Fwww.amazon.com%2Fgp%2Fyourstore%2Fhome%3Fie%3DUTF8%26ref_%3Dnav_custrec_newcust), because why not. Then I navigated to Get started with Amazon S3 at [https://aws.amazon.com/s3/](https://aws.amazon.com/s3/). I created an AWS account at the Free Tier.

Now to the fun part of creating [buckets](https://s3.console.aws.amazon.com/s3/home?region=us-east-1). On S3, you will need to create two buckets for your blog: yourdomain.com and www.yourdomain.com. We are going to assume that you already own this domain. Why two buckets, you ask? We want people to be able to get to the site with or without the "www" in front. First, we will create the yourdomain.com bucket to serve the files, then we will create the www.yourdomain.com bucket to redirect to yourdomain.com.

But one step at a time. Click the button to Create bucket. You should see something like this:

![Alt text](/images/s3bucket01.png)

Enter your domain and region. Rather than clicking Next and going through all the steps, keep things simple for now and click "Create" in the bottom left.

Your new bucket will now show on your S3 console bucket list. When you click on yourdomain.com, you will notice several tabs. Go to "Properties" and select "Static website hosting" then "Use this bucket to host a website." Enter "index.html" for the Index Document. TAKE NOTE of the Endpoint. Write it down; email it to yourself; save it in a google doc. Basically, copy it somewhere easy to access later.

New bucket, new bucket, move down, move down. New bucket, new bucket, move down!

Create your second bucket, this time for www.yourdomain.com. Click "Create" rather than "Next" to keep things simple. From the bucket list, click on www.yourdomain.com and go to "Properties" as before. Now, for "Static website hosting," select the option to "Redirect requests" and enter the non-www bucket, yourdomain.com, under "Target bucket or domain." And again, TAKE NOTE of the Endpoint.

S3 buckets are ready. Before we hop back over to our terminal, let's set up a user. Check out these [IAM Best Practices](http://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html?icmpid=docs_iam_console). Basically, when logged into the AWS console, select My Security Credentials from the dropdown under your username. Select Users from the navigation menu on the left and create a user, creating an access key. Leave that browser tab open for the moment.

# Uploading to S3

Back in your terminal, install the package that helps connect Pelican with S3:

    apt-get install s3cmd

Then, configure s3cmd with:

    s3md --configure

Copy and paste in the AWS access key and secret key when prompted. At the end, Yes you want to save.

Now, we are ready to upload!

    make s3_upload

This may take a moment as it's uploading quite a few files. But now you can access from your S3 bucket endpoints! Remember those endpoints you copied and pastied somewhere? Toss those in a browser and revel in your success.

# Wrap-up

There are more things to cover, like customizing your theme and using Cloudflare to connect your actual domain with S3, but this should be a good start in preparing your blog for the world.

Thank you for reading. Cheers!
