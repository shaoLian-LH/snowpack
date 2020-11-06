---
layout: layouts/guide.njk
permalink: '/guides/react/'
title: The Snowpack Guide to Getting Started with Svelte
description: 'Get started with this in-depth tutorial on how to build Svelte applications and websites with Snowpack'
date: 2020-10-01
tags: guides
sidebarTitle: React
---

<img src="/img/SvelteGuide.jpg" alt="header image, showing the Svelte and Snowpack logo against a background of blue mountains" />

Snowpack is a great fit for [Svelte](https://svelte.dev/) projects of any size. It's easy to get started and can scale to projects containing thousands of components and pages without any impact on development speed. Unlike traditional Svelte application tooling, Snowpack saves you from getting bogged down with complex bundler setups and configuration files.

In this guide, you'll go from an empty directory to a fully configured Snowpack project with support for Svelte and several other useful developer tools. In the process, you'll learn:

- How to set up your Snowpack development environment
- Adding your first Svelte component
- Working with CSS, images and other web assets
- Enabling Hot Module Replacement (HMR)
- Connecting your favorite tools

Prerequisites: Snowpack is a command line tool installed from npm. This guide assumes a basic understanding of Node.js, npm, and how to run commands in the terminal. Knowledge of React is not required, Snowpack is a great way to learn React!

> 💡 Tip: if you want to jump to the end to see a full featured React setup, the [Create Snowpack App Svelte template](https://github.com/snowpackjs/snowpack/tree/master/create-snowpack-app/app-template-svelte) comes with everything you'll learn in this guide plus other useful tools.

## Getting started

The easiest way to start a new Snowpack project is with [Create Snowpack App](<https://www.snowpack.dev/#create-snowpack-app-(csa)>), a tool for creating a new project based on our example templates. `@snowpack/project-template-minimal` is a Create Snowpack App template for a simple, bare-bones Snowpack project setup that the rest of this guide builds on.

To get started, open your terminal and head to a directory where you want to put your new project. Now run the following command in your terminal to create a new directory called `svelte-snowpack` with the minimal template automatically installed.

```bash
npx create-snowpack-app svelte-snowpack --template @snowpack/app-template-minimal
```

You can now head to the new directory and start Snowpack with the following two commands:

```bash
cd svelte-snowpack
npm run start
```

You should see your new website up and running!

> 💡 Tip: the `README.md` in your new project contains useful information about what each file does.

<div class="frame"><img src="/img/guides/react/minimalist-hello-world.png" alt="screenshot of project-template-minimal, which shows 'Hello world' in text on a white background." class="screenshot"/></div>

Now that you have a basic project up and running, to install Svelte, run the following command in your project directory:

```bash
npm install svelte --save
```

> 💡 Tip: add the "--use-yarn" or "--use-pnpm" flag to use something other than npm

You'll also need the `@snowpack/plugin-svelte` plugin so that Snowpack knows how built `.svelte` files into JavaScript and CSS files that run in the browser. Snowpack plugins are a way to extend Snowpack's capabilities with a bare minimum of configuration. To start, install the package in your project:

```bash
npm install @snowpack/plugin-svelte --save-dev
```

Once installed, you'll need to add the plugin to your Snowpack configuration file so that Snowpack knows to use it:

```diff
  module.exports = {
    mount: {
      public: '/',
      src: '/_dist_',
    },
-   plugins: []
+   plugins: ['@snowpack/plugin-svelte'],
  };
```

`@snowpack/plugin-svelte` also has built in HMR (hot module replacement) and Fast Refresh, features you'll explore in the next step.

## Create your first Svelte component

Svelte relies on HTML-based templating in .svelte files. Let's create our first `.svelte` component named `App.svelte` with the following code

```svelte
<script>
  /* Svelte component logic will go here! */
</script>
<style>
  /* Styling will go here! */
</style>
<div class="App">
    <header class="App-header">
        <a class="App-link" href="https://svelte.dev" target="_blank" rel="noopener noreferrer">
          Learn Svelte
        </a>
    </header>
</div>
```

Now head to `index.js` and import it the new `app.svelte` file

```diff
/* Add JavaScript code here! */
-console.log('Hello World! You did! Welcome to Snowpack :D');
+import App from "./App.svelte";

+var app = new App({
+  target: document.body,
+});

+export default app;
```

Start up Snowpack with `npm start` and you should see a page that says "Learn Svelte." Learn job, you now have Svelte up and running. When you make changes to the code and save them, you'll see them instantly in the browser without refreshing the entire page. That's the Hot Module Refresh from `@snowpack/plugin-svelte`.

Image: GIF showing code and site side by side, site is a "Learn Svelte" link on a white background. When the text is edit to add "Hello world" and the file saves, the changes show up in the site immediately.

> 💡 Tip: check out your network tab in the browser inspector- you'll see only the file that changed reloading, not all the files that make up the site.

## Customize your project layout

Since you'll be adding a bunch of new files, you probably don't want them crowding up your top-level root directly. Snowpack is flexible enough to support whatever project layout that you prefer. In this guide, you'll learn how to use a popular project pattern from the Svelte community.

```
📁 src : your Svelte components and their assets (CSS, images)
    ↳ index.js
    ↳ App.svelte
📁 public : global assets like images, fonts, icons, and global CSS
    ↳ index.css
    ↳ index.html
```

Use your favorite visual editor to rearrange and rename, or run these commands in the terminal:

```bash
mkdir src
mkdir public
mv index.js src/index.js
mv App.svelte src/App.svelte
mv index.html public/index.html
mv index.css public/index.css
```

This means if you are running Snowpack right now, the site is now broken as the files are all in different places. Lets add a "mount" configuration to update your site to your new project layout.

The `mount` configuration changes where Snowpack looks for and builds files. Every Snowpack project comes with a `snowpack.config.js` file for any configuration that you might need. Right now, you should see a configuration file with empty options. Add this to the empty `mount` object:

```diff
  mount: {
-   /* ... */
+   // directory name: 'build directory'
+   public: '/',
+   src: '/_dist_',
  },
```

<img src="/img/guides/react/folderstructure.png" alt="The original file configuration had Snowpack building the directory structure the same as the directories in the project, including root. Now the config builds only src and public. Src to the dist folder and public to root." />

`mount` is part of the [Snowpack Configuration API](https://www.snowpack.dev/#configuration). It allows you to customize the file structure of your project. The key is the name of the directory and the value is where you'd like them in the final build. With this new configuration, Snowpack builds files in `public` like `public/index.css` directory into `index.css`. It builds files in `src` like `src/index.js` into `/_dist_/index.js`, so you'll need to change that path in your `index.html`:

```diff
  <body>
-   <h1>Welcome to Snowpack!</h1>
-   <script type="module" src="/index.js"></script>
+   <script type="module" src="/_dist_/index.js"></script>
  </body>
```

You'll need to restart Snowpack for configuration file changes. When you start up again, if it worked, it should look the same.

## Styling your project

Your site is a little plain, so in this step you'll add an image and animate it with CSS.

> 💡 Tip: as you're doing this, you should not need to reload the page or restart Snowpack. Snowpack automatically updates the project in the browser as you edit code.

Add this file [`logo.svg`](https://github.com/snowpackjs/snowpack/blob/master/create-snowpack-app/app-template-svelte/public/logo.svg) to your `public` directory. Now you can add it to your `App.svelte`

```diff
    <header class="App-header">
+       <img src="/logo.svg" class="App-logo" alt="logo" />
        <a class="App-link" href="https://svelte.dev" target="_blank" rel="noopener noreferrer">
          Learn Svelte
```

Image: Site showing the new logo

With Svelte, CSS is included in your `.svelte` component and you'll add it there. Add this code to the top of `App.svelte` between the `<style>` tags:

```css
<style>
  .App-header {
    background-color: #f9f6f6;
    color: #333;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    font-size: calc(10px + 2vmin);
  }
  .App-logo {
    height: 36vmin;
    pointer-events: none;
    margin-bottom: 3rem;
    animation: App-logo-spin infinite 1.6s ease-in-out alternate;
  }
  @keyframes App-logo-spin {
    from {
      transform: scale(1);
    }
    to {
      transform: scale(1.06);
    }
  }
</style>
```

> 💡 Tip: Snowpack has built-in support for [CSS Modules](https://www.snowpack.dev/#import-css-modules) and if you'd like to use Sass there is an official [Sass Plugin](https://www.snowpack.dev/#sass).

Image: Gif showing the results with the logo animated

## Adding a simple counter

In a previous step we demonstrated the built in Hot Module Replacement (HMR) from `@snowpack/plugin-svelte`, but what about the Fast Refresh? Fast Refresh preserves state when you're developing, so you can see how your code changes affect the code that's running live in the browser. In this step you'll add a simple timer with a state based on the seconds on the page so you can see Fast Refresh in action.

Svelte components include component specific scripts in a `<script>` tag. Let's add the counter here in `App.svelte` between the `<scripe>` tags:

```js
<script>
  import {onMount} from 'svelte';
  let count = 0;
  onMount(() => {
    const interval = setInterval(() => count++, 1000);
    return () => {
      clearInterval(interval);
    };
  });
</script>
```

Then lower down in your component's body, add this code that will display the results of the timer.

```diff
<div class="App">
    <header class="App-header">
      <img src="/logo.svg" class="App-logo" alt="logo" />
+     <p>Page has been open for <code>{count}</code> seconds.</p>
      <a class="App-link" href="https://svelte.dev" target="_blank" rel="noopener noreferrer">
        Learn Svelte
      </a>
    </header>
</div>
```

Trying making a change to the code. You'll see the timer does not reset.

Image: GIF showing code and site side by side, when the word "Hello" is added to the .svelte page and the code is saved, the change shows up in the browser without the timer resetting (it keeps counting)

## Hot Module Replacement with Svelte

POINT TWO: Under stand the concept a bit more, show that there's a bit more to do here.

- point: plugin-svelte ONLY enables this fast refresh for .svelte files. With no other changes, a change to `api/getUser.js` would trigger a full page reload.
- what about your non .svelte files? helpers JS files, state management, etc. example: `api/getUser.js`
- add the HMR snippet to the top of your app to enable
- to see it in action, open up your network panel. When you change the index.js file, you should see only that file get fetched, not a full page reload.
- HMR is super cool and powerful, to learn more about it check out the docs.
- you could even say: "Feel free to skip this and come back to it if it's too tricky"

HMR: by definition, is the SYSTEM that Snowpack uses to push individual updates. The confusing bit is that developers have starting thinking about it as a feature of their environment: "does Snowpack support HMR".

The 3 Levels of Developer UX Speed

1. Full Browser Refresh on change (browsersync)
2. Basic HMR - refetch the changed file, and then re-render the app
3. Fast Refresh - refetch the changed file, and then re-render that single component, preserving state

## Making Snowpack Even Faster with Fast Reload/Hot Module Replacement

Hot Module Replacment (HMR) and Svelte are enhancements that let you push individual file changes to update the browser without refreshing the page.

For example make a change now and you'll see the entire page refresh.
TODO: GIF

To enable HMR head to `index.js` and add this snippet

```js
// Hot Module Replacement (HMR) - Remove this snippet to remove HMR.
// Learn more: https://www.snowpack.dev/#hot-module-replacement
```

TODO: GIF

## Going further

Great job! You're now ready to build the Svelte project of your dreams with Snowpack. Want to tweet your accomplishment to the world? Click the button below:

<a href="https://twitter.com/share?ref_src=twsrc%5Etfw" class="twitter-share-button" data-text="I just learned how to build a React app with #Snowpack. Check out the tutorial:" data-show-count="false">Tweet</a><script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

At this point you have the basics and have a great starter for any React project. But if you compare with the official [Snowpack Svelte template](https://github.com/snowpackjs/snowpack/tree/master/create-snowpack-app/app-template-svelte) you'll notice it has some other developer tools you might find useful:

- [Prettier](https://prettier.io/)—a popular code formatter

- [Tests](https://www.snowpack.dev/#testing)—Snowpack supports any popular JavaScript testing framework

- [`@snowpack/plugin-dotenv`](https://github.com/snowpackjs/snowpack/tree/master/plugins/plugin-dotenv)—Use `dotenv` in your Snowpack. This is useful for environment specific variables

If you'd like to use Typescript with Snowpack and Svelte, check out the [Snowpack Svelte Typescript](https://github.com/snowpackjs/snowpack/tree/master/create-snowpack-app/app-template-svelte-typescript) template.

If you have any questions, comments, or corrections, we'd love to hear from you in the Snowpack [discussion](https://github.com/snowpackjs/snowpack/discussions) forum or our [Snowpack Discord community](https://discord.gg/rS8SnRk).