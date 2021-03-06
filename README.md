# What is this?

This is a demo Gatsby blog that provides the Github Actions setup to handle CI for testing, publishing, and updating your Gatsby blog on IPFS using Textile Buckets.

![buckets](https://2020-hackathon.textile.cafe/gatsby-bucket-flow.png)

Here's how it works.

1. You can use any Gatsby project, but the repo provides a simple starter.
2. Each time you update your project, for example with a new blog post, you can open a PR to your project on Github. Your PR will kickoff an action that will build your site and push it to IPFS as a Textile Bucket. You will get a temporary URL to view your site live.
3. If you push changes to your Pull Request the temporary bucket will update for live viewing of your changes.
4. When you close or merge your PR that temporary Bucket and URL will be removed.
5. When you merge or commit into Master, your primary Bucket with a static URL will be updated.
6. If you create a release on your project in Github, you can use your latest Bucket CID to update your live website at your custom URL.

_What are Buckets?_

Buckets are a file organization and pinning service built on Textile Threads, meaning they are dynamic folders of your files pinned to IPFS. Textile can provide a simple static URL for your Bucket to view the data online or render website content.

Read our [hackathon tutorial for buckets here](https://blog.textile.io/ethden-come-learn-how-to-publish-dynamic-ipfs-buckets-on-textile/).

## In action

You can see how this site works here.

The BUCKET_NAME for this example project is: `gatsby-ipfs-blog`.

### Pull Request

An open pull request triggers a CI job that will generate a temporary Bucket. [Here is an example](https://github.com/textileio/gatsby-ipfs-blog/pull/3). You can see that in the Action there is a final output that looks like,

```
The Bucket URL is https://gatsby-ipfs-blog-377515556.textile.cafe/
```

You can visit the temporary bucket here, [https://gatsby-ipfs-blog-377515556.textile.cafe/](https://gatsby-ipfs-blog-377515556.textile.cafe/). In the example Pull Request, there is a new blog post added to the site. The site is fully functional, so [here is the new post to review](https://gatsby-ipfs-blog-377515556.textile.cafe/textile-buckets/).

### Merge

When any Pull Request is merged into master, a different Action is kicked off that will update the root bucket available at:

[https://gatsby-ipfs-blog.textile.cafe/](https://gatsby-ipfs-blog.textile.cafe/)

Here is an [example of the Action that updated our live site](https://github.com/textileio/gatsby-ipfs-blog/runs/456779082?check_suite_focus=true).

So to update our site with the changes in our PR above, all that we need to do is merge it!

### Publish

If we want to update our own domain, we can finally create a Release in GitHub that will update our Cloudflare records with the latest CID of our root bucket, allowing you to run your site right from IPFS.

# Setup

## Clone

`git clone git@github.com:textileio/gatsby-ipfs-blog.git`

`cd gatsby-ipfs-blog`

## Create new repo

Go to GitHub and create a new repo for your blog.

Disconnect from Textile repo,

`git remote remove origin`

Add your new repo as the origin:

`git remote add origin <your new repo>`

`git push -u origin master`

## Install the Textile CLI

You'll need to install Textile on your local machine. To do so, download the binary for your computer here, [https://github.com/textileio/textile/releases/latest](https://github.com/textileio/textile/releases/latest).

_Apple users: Textile is not signing the build with Apple developer keys, you'll need to authorize the app to run in your security settings. Download the Darwin build and go through the install steps (untar, cd, `./install`). Next, run a Textile command once, e.g. `textile --help`. This will fail due to permissions. Go to System Preferences -> Security. In the bottom right, click to allow Textile to run anyway. Run `textile --help` again, confirm your decision, and you'll not be prompted again._

## Create a Textile login and team

Textile uses passwordless login for all users, giving them remote IPFS pinning for their projects.

`textile login`

Follow the instructions.

`textile whoami`

Should now show you your account. If you think you'll want to collaborate with others, you should create a team and then enter you team before creating projects and buckets.

`textile team add <NAME>`

Followed by

`textile switch`

## Initialize a Textile Project

All Buckets are part of Projects. To make Project management easy, you can initialize a Project in the any directory. Then, each time you are working in that directory with Textile, it will know which project it is working with.

`cd <GATSBY PROJECT DIRECTORY>`

`textile project init <UNIQUE PROJECT NAME>`

This will create a file `./.textile/config.yml`. If you are using Git, you should commit this file to your code history.

## Setup GitHub Variables

1. Go to the `Settings` tab of your new Github repo.
2. Select the `Secrets` option in the menu.
3. Add the following new secrets

| NAME | Example | Description|
|------|-------|----------|
| TEXTILE_AUTH_TOKEN | `f7be8d8e-2bf5-4218-ba09-d0622e917e7f` | Your private auth token for Textile. Do not share. You can find it on your local computer in your home dir. E.g. `cat ~/.textile/auth.yml` |
| DOMAIN_NAME | `textile.io` | (OPTIONAL) The raw domain you want to update on Cloudflare |
| SUBDOMAIN | `blog` | (OPTIONAL) The subdomain on your site currently setup to use DNSLink. A DNSLink must exist for this record for the update to start working. [see here](https://blog.textile.io/ethden-using-ci-to-publish-your-webpage-using-ipfs-and-textile-buckets/). |
| SUBDOMAIN | `blog` | (OPTIONAL) The subdomain on your site currently setup to use DNSLink. A DNSLink must exist for this record for the update to start working. [see here](https://blog.textile.io/ethden-using-ci-to-publish-your-webpage-using-ipfs-and-textile-buckets/). |
| CLOUDFLARE_TOKEN | `` | (OPTIONAL) Cloudflare token capable of updating your DNS records, [see here](https://blog.textile.io/ethden-using-ci-to-publish-your-webpage-using-ipfs-and-textile-buckets/). |
| CLOUDFLARE_ZONE_ID | `` | (OPTIONAL) Zone Id of your domain on Cloudflare, [see here](https://blog.textile.io/ethden-using-ci-to-publish-your-webpage-using-ipfs-and-textile-buckets/). |

## Update your GitHub Actions

There is a value in each of the four workflows you need to update. You can find those files in,

* [.github/workflows/bucket_pull_request.yml](https://github.com/textileio/gatsby-ipfs-blog/blob/master/.github/workflows/bucket_pull_request.yml)
* [.github/workflows/bucket_remove.yml](https://github.com/textileio/gatsby-ipfs-blog/blob/master/.github/workflows/bucket_remove.yml)
* [.github/workflows/bucket_publish.yml](https://github.com/textileio/gatsby-ipfs-blog/blob/master/.github/workflows/bucket_publish.yml)
* [.github/workflows/update_dnslink.yml](https://github.com/textileio/gatsby-ipfs-blog/blob/master/.github/workflows/update_dnslink.yml)
  
In each of those files, change the value for `BUCKET_NAME` from `'gatsby-ipfs-blog'` to your unique bucket name.

| NAME | Example | Description|
|------|-------|----------|
| BUCKET_NAME | `my-famous-blog` | A globally unique name for your blog, containing no spaces or special characters |

## Custom Gatsby Builds

If your Gatsby builds someplace other than the `public` directory, you need to change each of the above workflow files, updating the Bucket step field called `path` to be your path not the current `public` path.

## Create a Pull Request

Pull requests will result in an ephemeral bucket so you can view your data. The bucket will use your `BUCKET_NAME` set above plus a unique ID for your Pull Request. 

`https://<BUCKET_NAME>-<PULL_REQUEST_ID>.textile.cafe/`

You can find the live URL of your build at the end of your GitHub Action.

If you commit and push changes to your Pull Request, the above Pull Request, the Bucket will be updated and your above URL will reflect your changes.

## Merge your Pull Request

When you Merge (or Close) your Pull Request, the Bucket created in the step above will be closed and no longer accessible over the URL. 

If you Merge your Pull Request into Master (or commit right to master) your primary Bucket will be updated. Your primary bucket is just,

`https://<BUCKET_NAME>.textile.cafe/`

## Create a Release

If you set the Optional variables in the table above and went through the [steps of creating a first DNS entry for a DNSLink](https://blog.textile.io/ethden-using-ci-to-publish-your-webpage-using-ipfs-and-textile-buckets/), you can update your live website by creating a new Release of your page on GitHub.

# Setup from Scratch

### Install Gatsby

`npm install -g gatsby-cli`

### Install Blog Starter

`gatsby new gatsby-starter-blog https://github.com/gatsbyjs/gatsby-starter-blog`

select `npm` when prompted

`cd gatsby-starter-blog/`

#### Start the Dev Server

`gatsby develop`

#### Build the Site

`gatsby build`

### Create Textile Project

`textile project init <your blog name>`

### Install Bucket CI for Gatsby

See here https://blog.textile.io/ethden-using-ci-to-publish-your-webpage-using-ipfs-and-textile-buckets/
