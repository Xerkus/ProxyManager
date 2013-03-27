# Proxy Manager

[![Build Status](https://travis-ci.org/Ocramius/ProxyManager.png?branch=master)](https://travis-ci.org/Ocramius/ProxyManager)

This library aims at providing abstraction for generating various kinds of proxy classes.

The idea was originated by a [talk about Proxies in PHP OOP](http://marco-pivetta.com/proxy-pattern-in-php/) that I gave
at the [@phpugffm](https://twitter.com/phpugffm) in January 2013.

## Installation

The suggested installation method is via [composer](https://getcomposer.org/):

```sh
php composer.phar require ocramius/proxy-manager:0.1.*
```

## Lazy Loading Value Holders

Currently, this library can generate [lazy loading value holders](http://www.martinfowler.com/eaaCatalog/lazyLoad.html),
which are a way to save performance and memory for objects that require a lot of dependencies or CPU cycles to be
initialized, and may not always be used.

```php
$config  = new \ProxyManager\Configuration(); // customize this if needed for production
$factory = new \ProxyManager\Factory\LazyLoadingValueHolderFactory($config);

$proxy = $factory->createProxy(
    'MyApp\HeavyComplexObject',
    function ($proxy, &$wrappedObject, $method, $parameters) {
        $wrappedObject = new HeavyComplexObject(); // instantiation logic here
    
        return true;
    }
);

$proxy->doFoo();
```

See the [complete documentation about lazy loading value holders](https://github.com/Ocramius/ProxyManager/tree/master/docs/lazy-loading-value-holder.md)
in the `docs/` directory.

## Access Interceptors

An access interceptor allows you to execute logic before and after a particular method is executed or a particular
property is accessed, and it allows to manipulate parameters and return values depending on your needs.

```php
$config  = new \ProxyManager\Configuration(); // customize this if needed for production
$factory = new \ProxyManager\Factory\AccessInterceptorValueHolderFactory($config);

$proxy = $factory->createProxy(
    new \My\Db\Connection(),
    array('query' => function () { echo "Query being executed!\n"; }),
    array('query' => function () { echo "Query completed!\n"; })
);

$proxy->query(); // produces "Query being executed!\nQuery completed!\n"
```

See the [complete documentation about access interceptor value holders](https://github.com/Ocramius/ProxyManager/tree/master/docs/access-interceptor-value-holder.md)
in the `docs/` directory.

## Fallback Value Holders

A fallback value holder is a particular value holder that implements the [null object pattern](http://en.wikipedia.org/wiki/Null_Object_pattern).

This kind of value holder allows you to have fallback logic in case loading of the wrapped value failed.

This feature is [planned](https://github.com/Ocramius/ProxyManager/issues/5).

## Ghost Objects

Similar to value holder, a ghost object is usually created to handle lazy loading.

The difference between a value holder and a ghost object is that the ghost object does not contain a real instance of
the required object, but handles lazy loading by initializing its own inherited properties.

Ghost objects are useful in cases where the overhead caused by accessing a proxy's methods must be very low, such as in
the context of data mappers.

This feature is [planned](https://github.com/Ocramius/ProxyManager/issues/6).

## Smart References

A smart reference proxy is actually a proxy backed by some kind of reference holder (usually a registry) that can fetch
existing instances of a particular object.

A smart reference is usually necessary when multiple instances of the same object can be avoided, or when the instances
are not hard links (like with [Weakref](http://php.net/manual/en/book.weakref.php)), and could be garbage-collected to
save memory in long time running processes.

This feature [yet to be planned](https://github.com/Ocramius/ProxyManager/issues/8).

## Remote Object

A remote object proxy is an object that is located on a different system, but is used as if it was available locally.
There's various possible remote proxy implementations, which could be based on xmlrpc/jsonrpc/soap/dnode/etc.

This feature [yet to be planned](https://github.com/Ocramius/ProxyManager/issues/7).

## Contributing

Please read the [CONTRIBUTING.md](https://github.com/Ocramius/ProxyManager/blob/master/CONTRIBUTING.md) contents if you
wish to help out!

