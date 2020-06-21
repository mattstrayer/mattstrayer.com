---
title: "VSCode, Vue, & Eslint/Prettier - One Happy Family"
date: 2020-06-17T08:51:30-04:00
draft: true
---

# intro


Github repo for this sample project: [sample-vue-app](www.github.com/mattsrayer/sample-vue-app).
for those of you who already have a vue app setup & just want all the eslint/prettier goodness, you can skip down to [here](#eslint).

# Vue app setup
First things first, lets make sure that vue-cli is installed. We'll leverage its vue project scaffolding capabilities here to this as easy as possible.

```
# Yarn
yarn global add @vue/cli

# NPM || PNPM commands are the same
npm install -g @vue/cli
```


Next up, we're going to create a new project. We're going to make a sample app called `sample`

```
vue create sample
# This will create a folder in your current directory called `sample`
```

```
Vue CLI v4.4.4
? Please pick a preset:
  default (babel, eslint)
❯ Manually select features
```
Select the `manually select features` option. (press enter to continue)


```
Vue CLI v4.4.4
? Please pick a preset: Manually select features
? Check the features needed for your project: (Press <space> to select, <a> to toggle all, <i> to invert selection)
 ◉ Babel
 ◯ TypeScript
 ◯ Progressive Web App (PWA) Support
 ◯ Router
 ◯ Vuex
 ◯ CSS Pre-processors
❯◉ Linter / Formatter
 ◯ Unit Testing
 ◯ E2E Testing
```
The bare minimum that we need here is Babel & Linting. (press enter)


```
Vue CLI v4.4.4
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Linter
? Pick a linter / formatter config:
  ESLint with error prevention only
  ESLint + Airbnb config
  ESLint + Standard config
❯ ESLint + Prettier
```
Select the last option, `ESLint + Prettier`.

```
Vue CLI v4.4.4
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Linter
? Pick a linter / formatter config: Prettier
? Pick additional lint features:
 ◉ Lint on save
❯◉ Lint and fix on commit
```
Select both options (press `a` to select all).

```
Vue CLI v4.4.4
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Linter
? Pick a linter / formatter config: Prettier
? Pick additional lint features: Lint on save, Lint and fix on commit
? Where do you prefer placing config for Babel, ESLint, etc.?
  In dedicated config files
❯ In package.json
```
Finally, select to store these configs in the `package.json` file & let the cli work for you.

## What just happened?

Vue scaffolded out a baseline vue application with a built-in pre-commit hook to lint & fix on commit. Vue uses [yorkie](https://github.com/yyx990803/yorkie) (a fork of [husky](https://github.com/typicode/husky)) to handle pre-commit hooks. If you're interested, you can see what yorkie does by opening `.git/hooks/pre-commit`. 

# Eslint & Prettier

At this point ESLint is ready to roll, you can run `yarn lint` and it will format your vue project. We just have to get this same functionality set up within vscode, so that you can format on save.


# VSCode + OnSave

In order for eslint to work on-save, we have to modify our VSCode settings. If you'd like to apply these settings just to your workspace, create `.vscode/settings.json` file. Otherwise, you can apply this across all projects by editing your user's `settings.json` file. On a mac, you can quickly get to this by `CMD + Shift + p` and entering `preferences:Open Settings (JSON)` in the prompt field.

Add the following.

```json
"editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },
  "[vue]": {
    "editor.formatOnSave": false,
  },
  "[js]": {
    "editor.formatOnSave": false,
  }
```

Here we're telling VSCode to allow eslint to fix formatting issues via a `codeAction`. We also need to tell VSCode to disable the default formatting option (so we don't get formatting collisions) for `vue` and `js` files, and we're good to go 👍.


# extras. 


Preferred eslint rules:
```json
"rules": {
    "vue/attribute-hyphenation": "error",
    "vue/attributes-order": "error"
}
```

