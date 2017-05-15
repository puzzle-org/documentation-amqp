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
    
Configure the worker
````````````````````

Launch a worker
```````````````

Consumption strategies
----------------------
