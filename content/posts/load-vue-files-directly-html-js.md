---
title: How to load Vue Components directly to html js
date: 2020-03-04T08:37:40+05:30
description: In this tutorial I am going to show you how you could load .vue files or components directly to html/js unlike full fledged vuejs project in which you have to compile project every time.
slug: load-vue-files-directly-html-js
categories:
  - Web Development
tags:
  - vuejs
---
In this tutorial I am going to show you how you could load .vue files or components directly to html/js unlike full fledged vuejs project in which you have to compile project every time.

If some of you know Angular 1.x versions, then you know that how it was easy to add the modules, in which you have to add js files to index.html and after hitting F5 you could see changes. It was also very easy to integrate the front end code with backend.

The same is not the case with Angular 2.x versions and Vuejs. Although according to documentation it is very easy to create a very simple project like

```html
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <title>Test</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <script src="https://vuejs.org/js/vue.min.js"></script>
</head>
<body>
  <div id="vapp">
    {{msg}}
  </div>
  <script>
    var app = new Vue({
      el : '#vapp',
      data : {
        msg : 'hi'
      }
    });
  </script>
</body>
</html>
```


But we all know if you have some components then you have to create a full fledged vuejs project which is a little difficult to setup.

What if you could write individual vue components in a .vue file and directly include it in index.html or whatever and without building see the changes.

To achieve this functionality we are going to use [http-vue-loader](https://github.com/FranckFreiburger/http-vue-loader) plugin. The plugin loads .vue files into your code directly.

The example is like.

**`index.html`**

```html
<!doctype html>
<html lang="en">
  <head>
    <script src="https://unpkg.com/vue"></script>
    <script src="https://unpkg.com/http-vue-loader"></script>
  </head>
  <body>
    <div id="my-app">
      <my-component></my-component>
    </div>
    <script type="text/javascript">
      new Vue({
        el: '#my-app',
        components: {
          'my-component': httpVueLoader('my-component.vue')
        }
      });
    </script>
  </body>
</html>
```


**`my-component.vue`**

```html
<template>
  <div class="hello">Hello {{who}}</div>
</template>
<script>
module.exports = {
  data: function() {
    return {
      who: 'world'
    }
  }
}
</script>
<style>
.hello {
  background-color: #ffe;
}
</style>
```

You could find more information in its official [Github repository](https://github.com/FranckFreiburger/http-vue-loader)

There is one problem though. When you'll edit .vue files the text editors like Notepad++, Gedit, Geany etc. won't be able to highlight html, css and js syntax because they won't know that they have to treat it just like .html files.

In http-vue-loader plugin you could specify .vue file paths but if your component files ends with .html extention, it won't detect. Therefore to solve this problem, I customized it so that we could write our component code in files end with .html extention. You can find the customized js in [Github](https://raw.githubusercontent.com/ashishdoneriya/vuedisk/master/js/httpVueLoader.js).

You could see the projects that I've created using this technique –  
* [VueDisk](https://github.com/ashishdoneriya/vuedisk)
* [Data Keeper](https://github.com/ashishdoneriya/datakeeper)