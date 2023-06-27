---
title: Deploy Your React App on GitHub Pages
description: Simple steps to deploy your React app for free on GitHub Pages
date: 2021-03-12
readingTime: 1 minute
tags: ["GitHub", "React"]
---

This is easy to do but I've already forgotten how since last time so I'm noting the steps here.

**Pre-Requisite** A React app that is pushed to a GitHub repository under your account.

1. Navigate to the root directory of your React App in a terminal and run the line below to install [gh-pages](https://github.com/tschaub/gh-pages) as a development dependency in your React project.

```js
npm install gh-pages --save-dev
```

2. Open your package.json file and add a "homepage" property with the URL to the GitHub Page where this app will live. **Note:** The URL is not to the repo itself at GitHub.com but to the GitHub Page at {username}.github.io/{repo}.

```js
"homepage": "https://{username}.github.io/{repo-name}"
```

3. Also in your package.json add the below lines under the `scripts` property. Note, the argument sent to `gh-pages -d` should correspond to the output directory of your compiled app. Commonly used names are build, dist, output, etc.

```js
"predeploy": "npm run build"
"deploy": "gh-pages -d build"
```

4. Run `npm run deploy` in your terminal.

This creates a new branch in your repository called 'gh-pages'. The live version of the app on GitHub Pages refers to this branch. You can now go to the homepage URL you referenced above and see your app in action.

**House Keeping:** Commit and Push these changes to your repo.

**Future Changes:** If you make changes to your app you'll need to run `npm run deploy` to deploy the latest app to the `gh-pages` branch in your repo. Pushing changes directly to your `master` branch will not make those changes live on GitHub Pages.

### **EDIT: 6/27/2023**

I recently deployed an app to Github Pages and received a mysterious 404 when accessing the URL. It immediatly started working after following the below steps. Make sure you complete this step as well when deploying your site.

Navigate to your repository on GitHub. Click the gear icon for the "About" section settings. Note how this says "No description, **website**, or topics provided".
{% image "./blog/2021/03/12/repo_about_settings.png", "GitHub Repository settings", "(max-width: 640px) 100%, 100%", page.url %}

Check the box next to "Use your GitHub Pages website".
{% image "./blog/2021/03/12/repo_details_incomplete.png", "Repository details settings incomplete", "(max-width: 640px) 100%, 100%", page.url %}

This will automatically populate the GitHub Pages URL above and "turn on" your website. Save Changes.
{% image "./blog/2021/03/12/repo_details_complete.png", "Repository details settings complete", "(max-width: 640px) 100%, 100%", page.url %}
