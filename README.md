# What

This is a demo Gatsby blog that provides the Github Actions setup to handle CI for testing, publishing, and updating your Gatsby blog on IPFS using Textile Buckets.

Here's how it works.

1. You can use any Gatsby project, but the repo provides a simple starter.
2. Each time you update your project, for example with a new blog post, you can open a PR to your project on Github. Your PR will kickoff an action that will build your site and push it to IPFS as a Textile Bucket. You will get a temporary URL to view your site live.
3. When you close or merge your PR that temporary Bucket and URL will be removed.
4. When you merge or commit into Master, your primary Bucket with a static URL will be udpated.
5. If you create a release on your project in Github, you can use your latest Bucket CID to update your live website at your custom URL.

_What are Buckets?_

Buckets are a file organization and pinning service built on Textile Threads, meaning they are dynamic folders of your files pinned to IPFS. Textile can provide a simple static URL for your Bucket to view the data online or render website content.

Read our [hackathon tutorial for buckets here](https://blog.textile.io/ethden-come-learn-how-to-publish-dynamic-ipfs-buckets-on-textile/).

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

## Setup Textile Project

`textile project init <your blog name>`

This will create a hidden folder in your repo `.textile/`. You need to add and commit this folder to your repo and be sure to push it to GitHub.

`git add .textile`

`git commit -sam 'textile: added project config`

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

* .github/workflows/bucket_pull_request.yml
* .github/workflows/bucket_remove.yml
* .github/workflows/bucket_publish.yml
* .github/workflows/bucket_release.yml
  
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

@todo