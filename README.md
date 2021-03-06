# PHP IBM Watson API Bridge

[![SensioLabsInsight](https://insight.sensiolabs.com/projects/4e21093a-cc60-4a75-b7fe-cb29053faf6c/big.png)](https://insight.sensiolabs.com/projects/4e21093a-cc60-4a75-b7fe-cb29053faf6c)

[![StyleCI](https://styleci.io/repos/59507474/shield?style=flat)](https://styleci.io/repos/59507474)
[![Build Status](https://travis-ci.org/findbrok/php-watson-api-bridge.svg?branch=master)](https://travis-ci.org/findbrok/php-watson-api-bridge)
[![Latest Stable Version](https://poser.pugx.org/findbrok/php-watson-api-bridge/v/stable)](https://packagist.org/packages/findbrok/php-watson-api-bridge)
[![Total Downloads](https://poser.pugx.org/findbrok/php-watson-api-bridge/downloads)](https://packagist.org/packages/findbrok/php-watson-api-bridge)
[![Latest Unstable Version](https://poser.pugx.org/findbrok/php-watson-api-bridge/v/unstable)](https://packagist.org/packages/findbrok/php-watson-api-bridge)
[![License](https://poser.pugx.org/findbrok/php-watson-api-bridge/license)](https://packagist.org/packages/findbrok/php-watson-api-bridge)

A simple PHP wrapper for IBM Watson API

### Installation

```
composer require findbrok/php-watson-api-bridge
```

### Usage

Before using the package checkout [Watson API Explorer](https://watson-api-explorer.mybluemix.net/),
to get a sense of what you can and cannot do with Watson

```php
require 'vendor/autoload.php'

use FindBrok\WatsonBridge\Bridge;

// Create a new bridge Object.
$bridge = new Bridge('username', 'password', 'baseUrl');

// Simple get request.
$queryParams = ['foo' => 'bar'];
$response = $bridge->get('uri', $queryParams);

// Simple post request.
$dataToPost = ['foo' => 'bar'];
$response = $bridge->post('uri', $dataToPost, 'json');
```

The Package uses [Guzzle](http://docs.guzzlephp.org/en/latest/testing.html) to perform requests, 
all your responses will be instances of ```GuzzleHttp\Psr7\Response```

---
### Integration with Laravel 5

As of version 1.1.x, PHP Watson API bridge adds a new Service Provider which integrates easily with Laravel 5.

First add the ServiceProvider to your ```app.php``` file.

```php
'providers' => [
   ....
   FindBrok\WatsonBridge\WatsonBridgeServiceProvider::class,
]
```

Now publish the config file.

```php
$ php artisan vendor:publish --tag=watson-api-bridge
```

You will now have a config file ```watson-bridge.php``` in your config directory. 
You may define in this config file your credentials, auth method to use,
Watson Services and so on.

### Services

The Laravel Integration gives you 3 service classes that are bound to the IoC.
- FindBrok\WatsonBridge\Bridge (The actual Bridge class for making requests to Watson)
- FindBrok\WatsonBridge\Support\Carpenter (Which can construct Bridge instances using your credentials and service URL)
- FindBrok\WatsonBridge\Support\BridgeStack (Essentially a store where you can keep all Bridges you constructed and retrieve them back.)

### Bridge

```Bridge``` class will help you make requests to Watson API using the ```get```, ```post```, ```put```, ```patch``` methods

```php
$response = $bridge->get('uri', $queryParams);
```

### Carpenter

The ```Carpenter``` class can build any type of Bridge for you. Use the ```constructBridge``` method passing in the desired parameters like
credentials name, service to use and auth method and so on and the ```Carpenter``` will.

```php
$carpenter = app()->make(Carpenter::class);

$bridge = $carpenter->constructBridge('default', 'personality_insights');
```

Remember that your credentials names, services and auth methods are all defined in the ```watson-bridge.php``` config file. 

### BridgeStack

The ```BridgeStack``` is a great place to keep all your Bridges so that you can retrieve them anytime in your app.
Use the ```mountBridge``` method to construct and keep any type of Bridge in the Stack.

```php
$stack = app()->make(BridgeStack::class);

$stack->mountBridge('myPIBridge', 'default', 'personality_insights');
$stack->mountBridge('myTABridge', 'default', 'tradeoff_analytics');

// Now use the Bridges stored in the Stack.
$response = $stack->conjure('myPIBridge')->post('/v3/profile', $dataToPost);
```
The ```BridgeStack``` is essentially a Laravel Collection, thus you have access to all Collection methods.
 
### Facades
 
If you are using Laravel version less than 5.4 you have access to 3 Facades for the 3 services Bridge, Carpenter and BridgeStack.
Since Laravel 5.4 added automatic Facades you won't be needing those classes.
 
- FindBrok\WatsonBridge\Facades\Bridge
- FindBrok\WatsonBridge\Facades\BridgeStack
- FindBrok\WatsonBridge\Facades\Carpenter
 
Remember that if you are resolving the Bridge directly from the IoC and not constructing it with the Carpenter class a default Bridge will
be resolved for you using the default credentials and auth methods from your watson-bridge config. 
 
## Credits
 
[![Percy Mamedy](https://img.shields.io/badge/Author-Percy%20Mamedy-orange.svg)](https://twitter.com/PercyMamedy) 

Twitter: [@PercyMamedy](https://twitter.com/PercyMamedy)
<br/>
GitHub: [percymamedy](https://github.com/percymamedy)
