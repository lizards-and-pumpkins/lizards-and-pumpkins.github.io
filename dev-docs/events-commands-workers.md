---
layout: wiki
title: Events, Commands and Workers
---
## Events, Commands and Workers

[ work in progress ]

The **import** flow looks like this:

```
_(AP | CLI) -> Command -> CommandQueue -> CommandConsumer -> CommandHandler ->_  
 _Event -> EventQueue -> EventConsumer -> EventHandler ->_  
 _Content Snippets -> KeyValue Store_
```

The processes above are simplified. For example, during the projection, the CommandHandlers can also issue new commands that are added to the command queue.


[ below block is copied from old Vibai's getting started guide ]

In case you want to experiment with background processes, feel free to try out the command
 `vendor/lizards-and-pumpkins/catalog/bin/interactive-consumer-ui.sh` (use the keys `[1]` and `[2]` and `[+]` and `[-]` to select, start
 and stop workers). To learn how start workers from a script try `vendor/lizards-and-pumpkins/catalog/bin/manage-consumer.sh`.

[ end of copied block ]

[ below block is copied from old Vibai's getting started guide ]

## Start the queue workers

In the example covered in this post the import data was so far processed directly by the `vendor/bin/lp import:template` and the `vendor/bin/lp import:catalog` scripts.  
In a real production setup that isn't good enough, as it is reasonable to expect imports to happen automatically!

In Lizards & Pumpkins this is done by background processes that process the queue messages.  
These are called queue workers.  
Even though that does add some complexity, the reason it was decided that it is worth the cost is because the number of
worker processes can easily be scaled up.  
If the queue network capable (like for example RabbitMQ), multiple queue workers can be run on multiple hosts, thereby
processing even large amounts of import data quite quickly.  

There are two kinds of workers in L&P, which can be scaled individually: command consumers and event consumers.  
To start and stop workers manually, use the `vendor/lizards-and-pumpkins/catalog/bin/interactive-consumer-ui.sh` script.  

This is nice for testing, but usually workers need to be started automatically.  
For this purpose the script `vendor/lizards-and-pumpkins/catalog/bin/manage-consumer.sh` exists.

For example, to start 2 command consumers, run  
`vendor/lizards-and-pumpkins/catalog/bin/manage-consumer.sh command start 2`.  
Or to start 4 event consumers, run  
`vendor/lizards-and-pumpkins/catalog/bin/manage-consumer.sh event start 4`.  

To scale down, use it with the `stop` action:  
`vendor/lizards-and-pumpkins/catalog/bin/manage-consumer.sh event stop 2`.  
Or to stop all workers of a kind, use  
`vendor/lizards-and-pumpkins/catalog/bin/manage-consumer.sh event stop-all`.  

When stopping workers, it might take longer than a few seconds for them to actually shut down, as they will continue to
finish their current task before quitting.
  
The number of workers you want to run depends on the hardware, the catalog data and will vary from project to project.  
The `interactive-consumer-ui.sh` is handy for tuning them just right.

[ end of copied block ]

### Workers

Lizards & Pumpkins can scale out horizontally using an arbitrary number worker processes.
If a distributed queue and storage engine is used, the worker processes can run on multiple hosts.

The amount of worker processes to start depends on many factory. On my local development box I tend to start one command consumer and 4 event consumers, as the event consumers do the majority or processing work.

The number of workers is only limited by the resources of the system they run on. I don't have that many CPU cores, so 4 event consumers keep my machine pretty busy. On a high-end server with 64 CPU cores, dozens of workers could run on a single machine.
Of course the amount of messages having to be processed also should be taken into consideration.

#### Starting Worker Processes

To start the worker processes in the background, run the following bash command:

```bash
$ bin/consumerSupervisor.sh bin/eventConsumer.php &
$ bin/consumerSupervisor.sh bin/commandConsumer.php &
```
On a remote host you might want to start the workers using `nohup` or in a `screen` session, so they continue running even in case the connection is terminated.

#### Stopping Worker Processes

To stop worker processes simply kill them. There are many ways to do that, for example using the `kill` command, or - if the terminal connection still is open - by bringing them back to run in the foreground using the `fg` command and hitting Ctrl-C.
