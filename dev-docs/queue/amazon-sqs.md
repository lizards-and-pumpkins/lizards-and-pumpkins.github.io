---
layout: wiki
title: Amazon SQS Queue
---
## Amazon SQS Queue

### Setup

1. [Get Amazon AWS account and credentials](https://aws.amazon.com/de/sqs/) 
2. Configure the access credentials in the environment
   (where ever you set your other Lizards & Pumpkins settings)
   ```bash
    export LP_AWS_SQS_COMMAND_QUEUE_URL="arn:aws:sqs:eu-central-1:311520829372:lap-sqs-test-command"
    export LP_AWS_SQS_EVENT_QUEUE_URL="arn:aws:sqs:eu-central-1:311520829372:lap-sqs-test-event"
    export LP_AWS_REGION="eu-central-1"
    export LP_AWS_KEY="MY_AWS_KEY"
    export LP_AWS_SECRET="MY_AWS_SECRET"
    ```
    
4. Add the same config settings to your www NGINX or Apache config
    ```nginx
    fastcgi_param LP_AWS_SQS_COMMAND_QUEUE_URL="arn:aws:sqs:eu-central-1:311520829372:lap-sqs-test-command"
    fastcgi_param LP_AWS_SQS_EVENT_QUEUE_URL="arn:aws:sqs:eu-central-1:311520829372:lap-sqs-test-event"
    fastcgi_param LP_AWS_REGION="eu-central-1"
    fastcgi_param LP_AWS_KEY="MY_AWS_KEY"
    fastcgi_param LP_AWS_SECRET="MY_AWS_SECRET"
    ```
    
5. Register the `SqsFactory` with the `MasterFactory` instance, for example using the factory registration callback:
    ```php
    public function factoryRegistrationCallback(MasterFactory $masterFactory)
    {
      $masterFactory->register(new \LizardsAndPumpkins\Messaging\Queue\Sqs\SqsFactory());
    }
    ```
    
Please be aware that in order to run SQS in production **proper security measures** have to be put in place, but that is out of the scope of this document.  
