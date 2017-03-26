Send messages
=============

.. note::

    You need to have a configured client instance (see :ref:`client`). 


Send text message
-----------------

.. code-block:: php

    <?php

    use Puzzle\AMQP\Client;
    use Puzzle\AMQP\Messages\Message;
    
    // ...
    
    public function sendMessage(Client $client)
    {
        $message = new Message('my.routing.key');
        $message->setText('This the payload of my message');
        
        $client->publish('myExchange', $message);
    }
    
Create JSON message
-------------------

From array :

.. code-block:: php

    <?php

    $message = new Message('my.routing.key');
    $message->setJson([
        'key 1' => 'value 1',
        'key 2' => 'value 2',
    ]);

or from already encoded JSON string : 

.. code-block:: php

    <?php

    use Puzzle\AMQP\Messages\Message;
    use Puzzle\AMQP\Messages\Bodies\Json;
    
    $body = new Json();
    $body->changeContentWithJson('{"meat":"beef", "with":"fries"}');
    
    $message = new Message('my.routing.key');
    $message->setBody($body);

``Content_type`` attribute is automatically set to ``application/json``

Body types
----------

- Text
- JSON
- Binary
- Null (mainly for unit testing purpose)

.. note::

    Each body type manage ``content_type`` attribute (see ``Puzzle\AMQP\Messages\ContentType`` interface for values) 


Add headers
-----------

.. code-block:: php

    <?php

    $message = new Message('my.routing.key');
    $message->setText('This the payload of my message');
    
    $message->addHeader('X-Lib', 'Puzzle');

    $message->addHeaders([
        'One' => 'value 1',
        'Two' => 'value 2',
        'Three' => 'value 3',
    ]);

Modify an attribute
-------------------

.. code-block:: php

    <?php

    $message = new Message('my.routing.key');
    $message->setText('This the payload of my message');
    
    $message
        ->setAttribute('correlation_id', 'd09dd70e-9796-4863-910d-b7330a555ac5');
        ->setAttribute('delivery_mode', Message::TRANSIENT);

Define a TTL
------------

.. code-block:: php

    <?php

    $message = new Message('my.routing.key');
    $message->setText('This the payload of my message');
    
    // 60 seconds
    $message->setExpiration(60);

Disallow silent dropping
------------------------

In AMQP 0.9.1 (use by RabbitMQ for example), messages which are not routed to a queue are silenty dropped.
You can disallow this behaviour by setting the mandatory flag while publishing the message. Puzzle AMQP provides you
an helper :  

.. code-block:: php

    <?php

    $message = new Message('my.routing.key');
    $message->disallowSilentDropping();

or you can disable this behaviour globally in configuration :

.. code-block:: yaml

    # amqp.yml
    global:
        disallowSilentDropping: true

Dependency injection
--------------------

Use the correct interface for your type hinting :

.. code-block:: php

    <?php
    
    use Puzzle\AMQP\WritableMessage;

    // ...

    public function foobar(WritableMessage $message)
    {
        // ...
    }
