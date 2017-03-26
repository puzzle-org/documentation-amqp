Getting started
===============

Current version
---------------

The current version is **3.0.0**

Requirements
------------

**PHP 5.6+** or **PHP 7.0+**.

`AMQP PECL extension <https://pecl.php.net/package/amqp>`_ **1.7+**

Installation
------------

The recommended way is to use `composer <http://getcomposer.org/>`_ : 

.. code-block:: json

    {
        "require": {
                "puzzle/amqp" : "~3.0"
        }
    }

Configuration
-------------

This library uses `Puzzle Configuration <http://www.puzzle-php.com/projects/configuration>`_ to abstract its configuration.

You can use both YAML or Memory configuration :

.. code-block:: yaml

    # amqp.yml
    broker:
        host: myRabbit
        login: guest
        password: guest
        vhost: /
    global:
        disallowSilentDropping: false
    
    # app.yml
    id: myApp
    
.. code-block:: php

    <?php
    
    use Puzzle\Configuration\Yaml;
    use Gaufrette\Filesystem;
    use Gaufrette\Adapter\Local;
    
    $configuration = new Yaml(
        new Filesystem(
            new Local('path/to/yaml/files/root/dir')
    ));

or

.. code-block:: php

    <?php

    use Puzzle\Configuration\Memory;
    
    $configuration = new Memory(array(
        'amqp/broker/host' => 'myRabbit',
        'amqp/broker/login' => 'guest',
        'amqp/broker/password' => 'guest',
        'amqp/broker/vhost' => '/',
        'amqp/global/disallowSilentDropping' => false,
        'app/id' => 'myApp',
    ));
    

Declare your client
-------------------

.. code-block:: php

    <?php

    use Puzzle\AMQP\Clients\Pecl;
    
    $client = new Pecl($configuration);


Send your first message
-----------------------

.. code-block:: php

    <?php

    use Puzzle\AMQP\Messages\Message;
    
    $message = new Message('my.routing.key');
    $message->setText('Hello asynchronous world !');

    $client->publish('myExchange', $message);

Whole example
-------------

.. code-block:: php

    <?php
    
    require '../vendor/autoload.php';
    
    use Puzzle\Configuration\Memory;
    use Puzzle\AMQP\Clients\Pecl;
    use Puzzle\AMQP\Messages\Message;
    
    $configuration = new Memory(array(
        'amqp/broker/host' => 'myRabbit',
        'amqp/broker/login' => 'guest',
        'amqp/broker/password' => 'guest',
        'amqp/broker/vhost' => '/',
        'app/id' => 'myApp',
    ));
    
    $client = new Pecl($configuration);
    
    $message = new Message('my.routing.key');
    $message->setJson([
        'key' => 'value',
        'key2' => 'value2',
    ]);
    
    $client->publish('myExchange', $message);
    