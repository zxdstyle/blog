---
title: "Laravel8配置vue3+tailwindcss+sass"
date: 2020-06-01
tags: ["Laravel", "Vue3", "Sass"]
categories: ["Linux", "Laravel"]
slug: "laravel8-tailwindcss-sass-vue3"
top_image: /images/tailwindcss-sass.png
---

### 安装 Laravel8
首先安装Laravel8，并且安装前端依赖。
```php
    composer create-project --prefer-dist laravel/laravel demo
    cd demo
    yarn （或 npm install）
```
<!-- more -->
### 集成 tailwindcss 和 scss

安装相关依赖:
```
 yarn add tailwindcss sass-loader sass --dev
```

在 `reourcces/css/app.scss` 添加：
```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

执行 `npx tailwind init` 命令生成 tailwind 配置文件。

调整 `webpack.mix.js` 文件：
```js
let mix = require('laravel-mix');

const tailwindcss = require('tailwindcss');
mix.js('resources/js/app.js', 'public/js')
   .sass('resources/css/app.scss', 'public/css')
   .options({ 
       processCssUrls: false,
       postCss: [tailwindcss('./tailwind.config.js')]
    });
```

### 集成vue3
安装vue-loader：
```php
yarn add vue@next @vue/compiler-sfc vue-loader@^16.1.0 --dev
```

然后在 `webpack.mix.js` 中添加以下代码：
```js
mix.vue();
```

新建测试组件 `resources/components/Test.vue`:
```vue
<template>
    <h2>test</h2>
</template>

<script>
export default {
    name: "Test"
}
</script>

<style scoped></style>
```

调整`resources/js/app.js`:
```js
require('./bootstrap');

import { createApp } from 'vue';
import Test from "../components/Test";

const app = createApp({
    components: {Test}
})


app.mount("#app")
```


到这一步，我的`package.json`：
```json
{
    "private": true,
    "scripts": {
        "dev": "npm run development",
        "development": "mix",
        "watch": "mix watch",
        "watch-poll": "mix watch -- --watch-options-poll=1000",
        "hot": "mix watch --hot",
        "prod": "npm run production",
        "production": "mix --production"
    },
    "devDependencies": {
        "@vue/compiler-sfc": "^3.0.6",
        "axios": "^0.21",
        "laravel-mix": "^6.0.6",
        "lodash": "^4.17.19",
        "postcss": "^8.1.14",
        "sass": "^1.32.8",
        "sass-loader": "^11.0.1",
        "vue-loader": "^16.1.0"
    },
    "dependencies": {
        "tailwindcss": "^2.0.3",
        "vue": "^3.0.6"
    }
}
```

### 测试
调整 `welcome.blade.php` 文件:
```php
<!DOCTYPE html>
<html>
    <head>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1">
        <title>Laravel</title>
        <link rel="stylesheet" href="{{ mix("css/app.css") }}">
    </head>
    <body>
        <div id="app">
            <Test></Test>
        </div>

        <script src="{{ mix("js/app.js") }}"></script>
    </body>
</html>

```

可以看到我们的 vue3 已经成功集成到 Laravel ！！！