+++
title = "Automated Deployment of a Free Blog"
date = "2020-04-19"
description = "An article detailing how this blog is automatically deployed to Firebase using GitHub Actions"
tags = [
    "GitHub",
    "Hugo",
    "Automation",
]
categories = [
    "CI/CD",
]
+++

This article is a walkthrough of how I set up a free, automatically deployed [Hugo](https://gohugo.io/) blog on [Google Firebase](https://firebase.google.com/), all using GitHub Actions.
<!--more-->

## The Setup

### Firebase

You can have up to three free projects on Google Firebase if you have a Google account, so you'll need one of those to get started. You'll also need a Hugo site ready to be deployed stored on GitHub. Once you've got those, run `npm install firebase-tools` so that you can install interact with firebase, then when it's finished you can run `firebase login:ci` to get a token that you can use later. It should look something like this:
```console
❯ firebase login:ci

Visit this URL on this device to log in:
https://accounts.google.com/o/oauth2/auth?client_id=563584335869-fgrhgmd47bqnekij5i8b5pr03ho849e6.apps.googleusercontent.com&scope=email%20openid%20https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fcloudplatformprojects.readonly%20https%3A%2F%2Fwww.googleapis.com%2Fauth%2Ffirebase%20https%3A%2F%2Fwww.googleapis.com%2Fauth%2Fcloud-platform&response_type=code&state=166870786&redirect_uri=http%3A%2F%2Flocalhost%3A9005

Waiting for authentication...

✔  Success! Use this token to login on a CI server:

thisisyourtoken-dontmakeitpublic-aliu34jnmsdfndf-r2u3rhkwjdfn

Example: firebase deploy --token "$FIREBASE_TOKEN"
```

Take that token and create a GitHub secret with it, I named mine `FIREBASE_TOKEN`, as will be seen when we get to the GitHub actions part.

### GitHub Actions
