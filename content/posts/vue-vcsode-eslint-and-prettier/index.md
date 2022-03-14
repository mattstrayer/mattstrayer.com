---
title: "VSCode, Vue, & Eslint+Prettier - One Happy Family"
date: 2020-06-17
publishDate: 2020-06-22
lastMod: 2020-06-21
draft: true
---


Github repo for this sample project: [sample-vue-app](https://github.com/mattstrayer/sample-vue-vscode-eslint-prettier).

### If you're new to Vue entirely, and need a walk-through to get started, expand below!

{{< details "Getting Started with Vue">}}

# Vue app setup
First things first, lets make sure that `vue cli` is installed. We'll leverage its vue project scaffolding capabilities to this as easy as possible.

```bash
# Yarn
yarn global add @vue/cli

# NPM & PNPM commands are the same
npm install -g @vue/cli
```


Next up, we're going to create a new project. We're going to make a sample app called `sample`

```bash
# This will create a folder in your current directory called `sample`
vue create sample
```

Arrow down to select the `manually select features` option. (press enter to continue)

```text
Vue CLI v4.4.4
? Please pick a preset:
  default (babel, eslint)
❯ Manually select features
```

The bare minimum that we need here is Babel & Linting. (press enter)


```text
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

Select the last option, `ESLint + Prettier`.

```text
Vue CLI v4.4.4
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Linter
? Pick a linter / formatter config:
  ESLint with error prevention only
  ESLint + Airbnb config
  ESLint + Standard config
❯ ESLint + Prettier
```

Select both options (press `a` to select all).

```text
Vue CLI v4.4.4
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Linter
? Pick a linter / formatter config: Prettier
? Pick additional lint features:
 ◉ Lint on save
❯◉ Lint and fix on commit
```

Finally, select to store these configs in the `package.json` file & let vue cli build the project.

```text
Vue CLI v4.4.4
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Linter
? Pick a linter / formatter config: Prettier
? Pick additional lint features: Lint on save, Lint and fix on commit
? Where do you prefer placing config for Babel, ESLint, etc.?
  In dedicated config files
❯ In package.json
```

## What just happened?

Vue scaffolded out a baseline vue application with a built-in pre-commit hook to lint & fix on commit. Vue uses [yorkie](https://github.com/yyx990803/yorkie) (a fork of [husky](https://github.com/typicode/husky)) to handle pre-commit hooks. If you're interested, you can see what yorkie does by opening `.git/hooks/pre-commit`.

---

{{< /details >}}




# Eslint & Prettier

Once you have your vue project setup, this point ESLint is ready to roll, you can run `yarn lint` and it will format your vue project. We just have to get this same functionality set up within vscode, so that you can format on save.


# VSCode + OnSave

In order for eslint to work on-save, we have to install the [eslint extension](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) and modify our VSCode settings.

If you'd like to apply these settings just to your workspace, create `.vscode/settings.json` file. Otherwise, you can apply this across all projects by editing your user's `settings.json` file. On a mac, you can quickly get to this by `CMD + Shift + p` and entering `preferences:Open Settings (JSON)` in the prompt field.

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

Vue has recommended rules for eslint, which are documented [here](https://eslint.vuejs.org/rules/).

I have the following set up in most of my projects.
```json
"rules": {
    "vue/attribute-hyphenation": "error",
    "vue/attributes-order": "error"
}
```