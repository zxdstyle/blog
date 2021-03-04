---
title: "【好用的扩展包】Laravel 路由生成 postman 接口集合"
date: 2020-04-05
tags: ["扩展包", "Postman", "Laravel"]
categories: ["扩展包推荐", "Laravel"]
top_image: /images/laravel-api-to-postman-featured.png
slug: "laravel-api-to-postman-featured"
---

`andreaselia/laravel-api-to-postman` 是一个可以根据 Laravel 路由自动生成 Postman 接口集合的扩展包，非常方便好用。
<!-- more -->
### 使用方法

安装扩展包：
```php
    composer require andreaselia/laravel-api-to-postman
```

发布配置文件：
```php
    php artisan vendor:publish --provider="AndreasElia\PostmanGenerator\PostmanGeneratorServiceProvider" --tag="postman-config"
```

导出接口集合：
```php
    php artisan export:postman
```

其他用法配置，参见该扩展包 [Readme](https://github.com/AndreasElia/laravel-api-to-postman)