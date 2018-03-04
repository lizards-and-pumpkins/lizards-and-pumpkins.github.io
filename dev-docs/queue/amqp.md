---
layout: wiki
title: AMQP
---
## AMQP

# Lizards & Pumpkins AMQP Queue Library

## Setup Steps

1. Add project dependency: `composer require lizards-and-pumpkins/lib-queue-backend-amqp dev-master`
2. Install RabbitMQ: e.g. `sudo apt-get install rabbitmq-server`
3. Configure the access credentials in the environment
   (where ever you set your other Lizards & Pumpkins settings)
   ```bash
        # insecure default example access credentials: 
        export LP_AMQP_HOST="localhost"
        export LP_AMQP_PORT="5672"
        export LP_AMQP_USERNAME="guest"
        export LP_AMQP_PASSWORD="guest"
        export LP_AMQP_VHOST="/"
    ```
    
4. Add the same config settings to your www NGINX or Apache config
    ```nginx
        # insecure default example access credentials:
        fastcgi_param LP_AMQP_HOST "localhost";
        fastcgi_param LP_AMQP_PORT "5672";
        fastcgi_param LP_AMQP_USERNAME "guest";
        fastcgi_param LP_AMQP_PASSWORD "guest";
        fastcgi_param LP_AMQP_VHOST "/";
    ```
    
5. Register the `AmqpFactory` with the `MasterFactory` instance, for example using the factory registration callback:
    ```php
        public function factoryRegistrationCallback(MasterFactory $masterFactory)
        {
            $masterFactory->register(new \LizardsAndPumpkins\Messaging\Queue\Amqp\AmqpFactory());
        }
    ```
    
Please be aware that in order to run RabbitMQ in production **proper security measures** have to be put in place, but that is out of the scope of this document.  
Please refer to the [access control](https://www.rabbitmq.com/access-control.html) and [TLS](https://www.rabbitmq.com/ssl.html) RabbitMQ documentation for further information.
