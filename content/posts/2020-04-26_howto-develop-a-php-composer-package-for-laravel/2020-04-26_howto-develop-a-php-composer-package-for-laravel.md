+++
title = "Howto develop a php composer package for Laravel"
date = "2020-04-26T11:13:48+02:00"
cover = ""
tags = ["php", "composer","laravel", "package"]
keywords = ["php", "composer","laravel", "package"]
description = ""
summary="💡Wondering how to start developing a PHP package which can be installed through composer? Here's how you can start developing your own package 👉  ..."
slug="howto-develop-a-php-composer-package-for-laravel"
+++

💡Wondering how to start developing a PHP package which can be installed through composer? Here's how you can start developing your own package:

* 👉 https://mauricius.dev/require-a-local-composer-package-for-development/

```json
"repositories": {
    "package/to-be-installed": {
        "type": "path",
        "url": "packages/symlink-to-repo-holding-your-package",
        "options": {
            "symlink": true,
        }
    }
}
```
