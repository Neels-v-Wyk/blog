+++
title = "Automated Deployment of a Free Blog"
date = "2020-04-19"
description = "An article detailing how this blog is automatically deployed to Firebase using GitHub Actions"
categories = [
    "Tech",
]
tags = [
    "GitHub",
    "Hugo",
    "Automation",
    "CI/CD",
]
+++

This article is a walkthrough of how I set up a free, automatically deployed [Hugo](https://gohugo.io/) blog on [Google Firebase](https://firebase.google.com/), all using GitHub Actions.
<!--more-->

### Why do this?

My reasons for doing this are multifold, but what it boils down to are these two simple facts:
* I am lazy
* I don't like spending money

I've never had a blog so I thought I'd try my hand at one. After getting some advice from a more experienced blogger, [Charl](https://cpbotha.net/), I decided to have a Hugo blog (because it's easy) hosted on Firebase (because it's free). After going through the motions I realized every time I wanted to publish a new post I'd have to go through like three or four easy steps. The thing is, that's something like 25 seconds of my life I won't get back every time I make a post. So spent a few hours automating and documenting it, for maximum efficiency.

## The Setup

### Firebase

You can have up to three free projects on Google Firebase if you have a Google account, so you'll need one of those to get started. You'll also need a Hugo site ready to be deployed stored on GitHub. Once you've got those, run `npm install firebase-tools` so that you can interact with firebase, then when it's finished you can run `firebase login:ci` to get a token that you can use later. It should look something like this:
```console
❯ firebase login:ci

Visit this URL on this device to log in:
https://accounts.google.com/o/oauth2/auth?client_id=563584335869-fgrhgmd47bqnekij5i8b5pr03ho849e6.apps.googleusercontent.com&scope=email%20openid%20https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fcloudplatformprojects.readonly%20https%3A%2F%2Fwww.googleapis.com%2Fauth%2Ffirebase%20https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fcloud-platform&response_type=code&state=166870786&redirect_uri=http%3A%2F%2Flocalhost%3A9005

Waiting for authentication...

✔  Success! Use this token to login on a CI server:

thisisyourtoken-dontmakeitpublic-aliu34jnmsdfndf-r2u3rhkwjdfn

Example: firebase deploy --token "$FIREBASE_TOKEN"
```

Take that token and create a GitHub secret with it, I named mine `FIREBASE_TOKEN`, as will be seen when we get to the next part.

In the root of your project directory run `firebase init` and do the following:
1. Select the 'Hosting' option
2. Choose the project we set up earlier
3. Select default rules for database file
4. Select hugo deploy directory (`public/` by default)
5. Say 'No' to the single-app page question

This should create a few firebase related files, then we're all set to set up the Actions.

### GitHub Actions

GitHub Actions are pretty cool. GitLab and BitBucket have been the leading git platforms with fancy tools like testing and CI/CD built into it, but recently GitHub has released Actions to also have a stake in the game. The best thing about GitHub actions is that they're completely free (for up to 2000 build minutes per month) for public repositories.

In the root of my repo, I have the directory `.github/workflows/` with one file in it, `deploy-hugo.yml`:
```yaml
name: Build & deploy

on:
  push:
    branches: 
    - master

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@master
      
    - name: Get Hugo
      uses: peaceiris/actions-hugo@v2.2.0
      with:
        hugo-version: 'latest'

    - name: Build static files
      run: hugo --gc --minify --cleanDestinationDir
      
    - name: Deploy to Firebase
      uses: w9jds/firebase-action@master
      with:
        args: deploy --only hosting --project blog-neels-tech
      env:
        FIREBASE_TOKEN: ${{ secrets.FIREBASE_TOKEN }}
```

#### Let's break that down

```yaml
name: Build & deploy

on:
  push:
    branches: 
    - master

jobs:
  build:

    runs-on: ubuntu-latest
```
What the above does, is whenever there's a new push to master, a bunch of tasks will be run on a clean ubuntu container.

```yaml
    steps:
    - uses: actions/checkout@master
```
Get the current repo's master branch using the `checkout` action.

```yaml
    - name: Get Hugo
      uses: peaceiris/actions-hugo@v2.2.0
      with:
        hugo-version: 'latest'

    - name: Build static files
      run: hugo --gc --minify --cleanDestinationDir
```
Get the [Hugo Setup Action](https://github.com/peaceiris/actions-hugo) module and then use it to create the static files for hugo to serve, and finally,

```yaml
    - name: Deploy to Firebase
      uses: w9jds/firebase-action@master
      with:
        args: deploy --only hosting --project blog-neels-tech
      env:
        FIREBASE_TOKEN: ${{ secrets.FIREBASE_TOKEN }}
```
Deploy to Firebase with the token we generated earlier.

### In conclusion

Whenever you want to create a new post, just run `hugo new posts/tech-stuff.md` and commit/push your changes when done, the rest will be handled for you :D
