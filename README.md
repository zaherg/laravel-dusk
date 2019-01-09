# PHP image to run laravel dusk tests

[![Docker Pulls](https://img.shields.io/docker/pulls/zaherg/laravel-dusk.svg)](https://hub.docker.com/r/zaherg/laravel-dusk/) [![](https://images.microbadger.com/badges/image/zaherg/laravel-dusk.svg)](https://microbadger.com/images/zaherg/laravel-dusk "Get your own image badge on microbadger.com") [![](https://images.microbadger.com/badges/version/zaherg/laravel-dusk.svg)](https://microbadger.com/images/zaherg/laravel-dusk "Get your own version badge on microbadger.com") [![](https://images.microbadger.com/badges/commit/zaherg/laravel-dusk.svg)](https://microbadger.com/images/zaherg/laravel-dusk "Get your own commit badge on microbadger.com")  [![](https://img.shields.io/github/last-commit/linuxjuggler/laravel-dusk.svg)](https://github.com/linuxjuggler/laravel-dusk)

This is a simple PHP 7.x image with Composer and xDebug, which contain just the minimum required to run Laravel Dusk a CI.
So you should use it as the __base__ for your custom image. 


## Image versions

1. zaherg/laravel-dusk:latest
2. zaherg/laravel-dusk:7.2
3. zaherg/laravel-dusk:7.3


## Duck configuration:

To make it work, you will need to make sure that you add the argument `--no-sandbox` to your options so the function
should look like

```
    /**
     * Create the RemoteWebDriver instance.
     *
     * @return \Facebook\WebDriver\Remote\RemoteWebDriver
     */
    protected function driver()
    {
        $options = (new ChromeOptions)->addArguments([
            '--disable-gpu',
            '--headless',
            '--window-size=1920,1080',
            '--no-sandbox'
        ]);

        return RemoteWebDriver::create(
            'http://localhost:9515', DesiredCapabilities::chrome()->setCapability(
                ChromeOptions::CAPABILITY, $options
            )
        );
    }
```

## xDebug configuration

By default xdebug is enabled, to disable it you need to create a `.env` file which should contain the following 
variables, but remember to change the value based one what you want to achieve:

```
PHP_XDEBUG_DEFAULT_ENABLE=0
PHP_XDEBUG_REMOTE_ENABLE=0
PHP_XDEBUG_REMOTE_HOST=127.0.0.1
PHP_XDEBUG_REMOTE_PORT=9001
PHP_XDEBUG_REMOTE_AUTO_START=0
PHP_XDEBUG_REMOTE_CONNECT_BACK=0
PHP_XDEBUG_IDEKEY=docker
PHP_XDEBUG_PROFILER_ENABLE=0
PHP_XDEBUG_PROFILER_OUTPUT_DIR=/tmp
```

Then run the docker and specify the env file that you have created like this

```
docker run --env-file .env zaherg/laravel-dusk:<version>
```


## Bitbucket Pipeline Example

You can read more about it from this post [https://zah.me/2018/02/03/running-laravel-dusk-tests-on-bitbucket-the-easy-way/](https://zah.me/2018/02/03/running-laravel-dusk-tests-on-bitbucket-the-easy-way/)


This is just an example, the content of the file `.env.dusk` is not different that `.env.example` except that
the `APP_URL` value is `http://localhost:8000` since am using `artisan serve` command.

```
APP_URL=http://localhost:8000
```

Your `bitbucket-pipeline.yml` should look like this:


```
# This is a sample build configuration for PHP.
# Check our guides at https://confluence.atlassian.com/x/e8YWN for more examples.
# Only use spaces to indent your .yml configuration.
# -----
# You can specify a custom docker image from Docker Hub as your build environment.
image: zaherg/laravel-dusk:latest


pipelines:
  default:
    - step:
        caches:
          - composer
        script:
          - cp .env.dusk .env
          - composer install --no-progress --no-suggest --prefer-dist
          - php artisan serve &
          - php artisan dusk
```


## TODO:

[] Write about using the image with Docker Hub CI.