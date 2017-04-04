.. _client:

Configure the client
====================

Create a client using the PECL extension
----------------------------------------

.. code-block:: php

    <?php

    use Puzzle\AMQP\Clients\Pecl;
    
    $client = new Pecl($configuration);


Publish a message to an exchange
--------------------------------

.. code-block:: php

    <?php

    $client->publish("myExchangeName", $message);


Add headers to all published messages
-------------------------------------

.. code-block:: php

    <?php

    use Puzzle\AMQP\Clients\Pecl;
    use Puzzle\AMQP\Messages\Processors\AddHeader;
    
    $client = new Pecl($configuration);
    $client->appendMessageProcessor(
        new AddHeader([
            'X-pony' => 'unicorn',
            'X-meat' => 'beef',
    ]));


Automatic prefix for exchanges
------------------------------

If all your exchanges are prefixed, for example by ``my.company``, you can handle it globally. It allows you to use exchange without prefix. 

.. code-block:: php

    <?php

    use Puzzle\AMQP\Clients\Pecl;
    use Puzzle\AMQP\Clients\Decorators\PrefixedExchangesClient;
    
    $client = new Pecl($configuration);
    $client = new PrefixedExchangesClient($client, 'my.company');
    
    // ...
    
    $client->publish('mosquito', $message); // will publish on "my.company.mosquito" exchange
    
The decorator automatically adds a dot as a separator between a non-empty prefix and your exchange name.
    
    
Automatic prefix for queues
---------------------------

Same feature exists for prefixed queues : 

.. code-block:: php

    <?php

    use Puzzle\AMQP\Clients\Pecl;
    use Puzzle\AMQP\Clients\Decorators\PrefixedQueuesClient;
    
    $client = new Pecl($configuration);
    $client = new PrefixedQueuesClient($client, 'my.company');

As they are decorators, you can combine them : 

.. code-block:: php

    <?php

    use Puzzle\AMQP\Clients\Pecl;
    use Puzzle\AMQP\Clients\Decorators\PrefixedExchangesClient;
    use Puzzle\AMQP\Clients\Decorators\PrefixedQueuesClient;

    $prefix = 'my.company';
    
    $client = new Pecl($configuration);
    $client = new PrefixedExchangesClient($client, $prefix);
    $client = new PrefixedQueuesClient($client, $prefix);


Plug your own process on message publishing
-------------------------------------------

You have to implement the ``OnPublishProcessor`` interface :

.. code-block:: php

    <?php
    
    use Puzzle\AMQP\Messages\OnPublishProcessor;
    use Puzzle\AMQP\WritableMessage;
    
    class MyProcess implements OnPublishProcessor
    {
        public function onPublish(WritableMessage $message)
        {
            $message->setExpiration(60);
        }
    }

You have to plug it to your client :

.. code-block:: php

    <?php
    
    use Puzzle\AMQP\Clients\Pecl;
    
    $client = new Pecl($configuration);
    $client->appendMessageProcessor(new MyProcess());

In this example, all the published message will have an expiration of 60s.

Unit testing
------------

An InMemory implementation of Client interface is provided for unit testing purpose. It allows you to unit test without amqp broker.
It stores all the published messages.

.. code-block:: php

    <?php
    
    use Puzzle\AMQP\Clients\InMemory;
    
    $client = new InMemory();
    $client->publish('my.exchange', $message);
    
    $sentMessages = $client->getSentMessages();
    
You can empty the published messages list :

.. code-block:: php

    <?php
    
    $client->dropSentMessages();

Full example :

.. code-block:: php

    <?php
    
    class FoobarTest extends \PHPUnit_Framework_TestCase
    {
        private
            $client;
            
        protected function setUp()
        {
            $this->client = new InMemory();
        }
        
        public function testX()
        {
            $baz = new Baz($this->client);
            $baz->doStuff();
            
            $sentMessages = $this->client->getSentMessages();
            
            // do all your asserts
        }
    }


    

