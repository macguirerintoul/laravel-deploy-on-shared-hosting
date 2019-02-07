# How to deploy a Laravel application on shared hosting/webserver
[![API Documentation](http://img.shields.io/badge/en-English-brightgreen.svg)](README.md)
[![API Documentation](http://img.shields.io/badge/es-Español-yellow.svg)](README-es.md)
[![API Documentation](http://img.shields.io/badge/vi-Ti%E1%BA%BFng%20Vi%E1%BB%87t-yellow.svg)](README-vi.md)
[![API Documentation](https://img.shields.io/badge/zh_CN-%E4%B8%AD%E6%96%87%EF%BC%88%E4%B8%AD%E5%9B%BD%E5%A4%A7%E9%99%86%EF%BC%89-yellow.svg)](README-zh_CN.md)

This guide covers how to deploy a Laravel application on shared hosting or a webserver. This guide assumes that there is nothing else in the `public_html` directory that needs to be served.

## Requirements
**SSH access to your shared hosting/webserver is required for this guide.**

Before deploying a Laravel application to shared hosting, ensure the hosting provider offers the required software described in the [Laravel documentation](https://laravel.com/docs/5.7/installation#server-requirements). If you're not sure if your hosting meets these requirements, feel free to try anyways. You can also try to install the software yourself using the corresponding package manager for your server's operating system.

* PHP >= 7.1.3
* OpenSSL PHP Extension
* PDO PHP Extension
* Mbstring PHP Extension
* Tokenizer PHP Extension
* XML PHP Extension
* Ctype PHP Extension
* JSON PHP Extension
* BCMath PHP Extension

The server will also need [Composer](https://getcomposer.org/) to install Laravel dependencies, and you might like to use [Git](https://git-scm.com/) to pull your code onto the server.

## Instructions
Your shared hosting/webserver will likely have a directory structure similar to the one below.

```bash
.bash_history
.bash_logout
.bash_profile
.bashrc
.cache
.cpanel
.htpasswds
logs
mail
public_ftp
public_html
.ssh
tmp
etc
www -> public_html
...
```

To make the application accessible at your domain, we will be linking the `public_html` directory to the `public` directory of our Laravel application. However, because our Laravel project also contains sensitive information like `.env` files, we will keep these outside of `public_html`.

### Create a `projects` directory
Connect to your shared hosting/webserver via SSH or FTP. At the same level as the `public_html` directory, create a new directory to store the Laravel project. You can call it whatever you like, but we're going to call it `projects`.

```bash
$ mkdir projects
$ cd projects
```

### Pull your project's code
If your code is stored on a Git server, you can clone your repository. Otherwise, copy your entire Laravel application to the `projects` directory.

```bash
$ git clone http://[GIT_SERVER]/[REPO_NAME].git
```

### Create symbolic links
Next, we will create a symbolic link from the `www` and `public_html` directories in our shared hosting/webserver, to the `public` directory in our Laravel application. Go back to the account directory:

```bash
$ cd ~ # go to your account directory
# or
$ cd .. # go up one level
```

Make sure you're in the correct directory by running ```$ ls``` and ensuring `public_html` is in the output. Next, make a backup of `public_html` if you like. **Note: this assumes there is nothing else in `public_html` that needs to be served.**

```bash
$ cp public_html public_html_backup # make a backup of public_html
$ cp www www_backup # make a backup of www
```

Now, we will create the symbolic links. In the following few commands, make sure to replace `projects` with the name of the directory you created, and `repo-name` with the directory of your Laravel application.

```bash
$ ln -s projects/repo-name/public public_html
$ ln -s projects/repo-name/public www
```

### Laravel configuration
Now, we'll go back into our Laravel project and run some more commands.

```bash
$ cd projects/repo-name
```

If your application uses Laravel's `storage` directory, allow write permissions on it:

```bash
$ chmod -R o+w storage
```

Now, create a file called `.env` and enter the environment variables for your application. This will be similar to your local `.env`, but will likely need some tweaking for production.

```bash
$ vi .env
```

Install Laravel dependencies from `composer.lock`:

```bash
$ composer install
$ composer dump-autoload
```

Create caches:
```bash
$ php artisan config:cache
$ php artisan route:cache
```

And navigate to your domain to see if your Laravel application is up and running.

**Congratulations! You've successfully set up a Laravel application on shared hosting/webserver.**

## FAQ

**1. How can I get SSH access to my shared hosting/webserver?**

This varies depending on your hosting provider. Please contact your hosting provider's support.

**2. How do I install Git on my server?**

This varies depending on your server's operating system. Please look up specific instructions for how to install Git on your operating system.

If your server uses CPanel, Git can be found here: `/usr/local/cpanel/3rdparty/bin/git`. To use it, you can either replace `git` in any command with that path, or you can run the following command to create a temporary alias.
```bash
alias git="/usr/local/cpanel/3rdparty/bin/git"
```

**3. How do I install Composer?**

[Instructions for installing Composer](https://getcomposer.org/doc/00-intro.md) can be found on the Composer website.

**4. Does this work for Lumen applications?**

Because Laravel and Lumen are so similar, this should work for Lumen applications as well.

## List of hosting providers tested

This guide has been tested on the following hosting providers. Feel free to submit a pull request if you deploy successfully on a provider not listed here.

* [NameCheap](https://www.namecheap.com/)
* [JustHost](https://www.justhost.com/)
* [Bluehost](https://www.bluehost.com/)
* [GoDaddy](https://godaddy.com/)
* [HostGator](http://www.hostgator.com/)
* [GeekStorage*](https://www.geekstorage.com/)
* [Site5](https://www.site5.com/)

\*On GeekStorage, PHP had to be enabled via `.htaccess`. For PHP 7.2, add the following line to the `.htaccess` file in the `public` directory, or create it if it does not exist. For other PHP versions/more details, see [this StackOverflow post](https://stackoverflow.com/questions/12561203/how-to-change-php-version-in-htaccess-in-server).

```
AddHandler application/x-httpd-php72 .php
```

## Troubleshooting
If the above steps haven't worked for you, feel free to [submit an issue](https://github.com/petehouston/laravel-deploy-on-shared-hosting/issues) detailing any problems you encounter.

## Contributing
Free free to fork the project and submit [a pull request](https://github.com/petehouston/laravel-deploy-on-shared-hosting/pulls).
