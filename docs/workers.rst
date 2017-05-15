Workers
=======

Understand the workers
----------------------
Workers are daemon. A worker listen a queue and process its messages.

Create a new worker
-------------------
To create a worker, you have to follow these few steps: 

    1. Create the worker class (that processes messages)
    2. Configure the worker (queue, strategy, ...)
    3. Launch it

Create the worker class
```````````````````````

.. code-block:: php

    <?php

    use Puzzle\AMQP\Workers\Worker;
    use Puzzle\AMQP\Messages\ReadableMessage;
    
    class Example implements Worker
    {
        public function process(ReadableMessage $message)
        {
            // do stuff
        }
    }
    
The method ``process`` is a blocking one, waiting for next message in the listened queue.
    
Configure the worker
````````````````````

You have to instanciate a ``WorkerContext`` object :

.. code-block:: php

    <?php

    use Puzzle\AMQP\Consumers;

    $consumer = new Consumers\Simple();
    
	$workerClosure = function() {
        return new Example();
    };
    
    $workerContext = new WorkerContext(
    	$workerClosure,
        $consumer,
        'queueName'
    );
    

Launch a worker
```````````````

Ask the consumer to start consuming the messages from the queue (as defined in the ``WorkerContext``) :

.. code-block:: php

    <?php

    use Puzzle\AMQP\Workers\ProcessorInterfaceAdapter;

    // infinite loop
    $consumer->consume(
        new ProcessorInterfaceAdapter($workerContext),
        $client,
        $workerContext
    );

Al these steps are handled in an easier way in :ref:`silex` integration.

Consumption strategies
----------------------
