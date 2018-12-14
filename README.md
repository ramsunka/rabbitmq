# Introduction to RabbitMQ

RabbitMQ is an AMQP messaging broker and it is the most popular open source and cross-platform message broker.
 
RabbitMQ is also a way to exchange the data between different platform applications such as a message sent from Java application can be read by a .Net application or Ruby application.
 
The RabbitMQ is built on Erlang general-purpose programming language and it is also used by many messaging applications in the market like WhatsApp for messaging.

The Advanced Message Queuing Protocol (AMQP) is an open standard application layer protocol for message-oriented middleware. The defining features of AMQP are message orientation, queuing, routing (including point-to-point and publish-and-subscribe), reliability and security.

RabbitMQ is lightweight and easy to deploy on available premises and it supports multiple messaging protocols. RabbitMQ can be deployed in distributed and federated configurations to meet high-scale, high-availability requirements.
 
Following is the pictorial representation of how RabbitMQ will act as a mediator between sender and consumer in our applications.

![image](https://user-images.githubusercontent.com/21335378/49988978-95678200-ff2d-11e8-908f-72d02a452519.png)

When publishing a message, publishers may specify various message attributes (message meta-data). Some of this meta-data may be used by the broker, however, the rest of it is completely opaque to the broker and is only used by applications that receive the message.

Networks are unreliable and applications may fail to process messages therefore the AMQP model has a notion of message acknowledgements: when a message is delivered to a consumer the consumer notifies the broker, either automatically or as soon as the application developer chooses to do so. When message acknowledgements are in use, a broker will only completely remove a message from a queue when it receives a notification for that message (or group of messages).

# AMQP is a Programmable Protocol:

AMQP 0-9-1 is a protocol in the sense that AMQP entities and routing schemes are primarily defined by application developers not a broker. Accordingly, provision is made for protocol to declare exchanges,queues and binding between them, subscribing queue.

The AMQP version is expressed using two or three digits – the major number, the minor number and an optional revision number. By convention, the version is expressed as major-minor[-revision] or major.minor[.revision]:
 •	The major, minor, and revision numbers can take any value from 0 to 99 for official specifications.
 •	Major, minor, and revision numbers of 100 and above are reserved for internal testing and development purposes.
 •	Version numbers indicate syntactic and semantic interoperability.
 •	Version 0-9-1 is represented as major = 0, minor = 9, revision = 1.
 •	Version 1.1 would be represented as major = 1, minor = 1, revision = 0. Writing “AMQP/1.1” is equivalent to writing “AMQP/1.1.0” or AMQP/1-1-0.

AMQP is message protocol that deals with publishers and consumers. The publisher produces the message and consumer picks them up and process it. The process of sending message from producer to consumer happens only when the exchanges and queues are setup properly with right binding/routing key.
Queues, exchanges and bindings are collectively referred to as AMQP entities.

Name	Default names
Direct Exchange	(Empty string) and amq.direct
Fanout Exchange	amq.fanout
Topic Exchange	amq.topic
Headers Exchange	Amq.match  (and amq.headers in RabbitMQ)

One of the standard headers is called routing-key and it is this that the broker uses to match messages to queues. Each queue specifies a "binding key" and if that key matches the value of the routing-key header, the queue receives the message.

# Direct Exchange:
                  A direct exchange routes the messages to consumers based on the routing key provided to exchange. The best example is organization sending a festival offer to one city out of all states under a nation.
![image](https://user-images.githubusercontent.com/21335378/49989076-f0997480-ff2d-11e8-893e-38ebcb1c74c1.png)

We cannot publish a message to queue directly. For default exchange the routing key is nothing but queue name itself.
The default exchange is implicitly bound to every queue, with a routing key equal to the queue name.
It is not possible to explicitly bind to, or unbind from the default exchange. It also cannot be deleted.
One queue can have multiple bindings is nothing but one queue can receive messages from multiple exchanges.
Publisher doesn’t know the queue names he only knows the exchange and routing key to send messages.

# Fanout Excahnge:
                  A fanout exchange routes messages to all the queues that are bound and the routing key will be ignored. The best example is organization sending a festival offer to all the stores at nationwide.

![image](https://user-images.githubusercontent.com/21335378/49989144-1f174f80-ff2e-11e8-8702-42ccfb1ee733.png)

# Topic Exchange:
                  Topic exchanges route messages to one or many queues based on matching between a message routing key and the pattern that was used to bind a queue to an exchange.The routing key must be list of words separated by dots. There are two important special cases for binding keys:
 •	(star) can substitute for exactly one word.
 •	# (hash) can substitute for zero or more words.
![image](https://user-images.githubusercontent.com/21335378/49989159-30605c00-ff2e-11e8-8077-a342a1ca6656.png)

In this image we have two queues with three types of routing keys. The summary of the routing keys is given below.
•	A message with a routing key set to "quick.orange.rabbit" will be delivered to both queues. 
•	Message "lazy.orange.elephant" also will go to both of them.
•	Message "quick.orange.fox" will only go to the first queue, and "lazy.brown.fox" only to the second.
•	Message  "lazy.pink.rabbit" will be delivered to the second queue only once, even though it matches two bindings. "quick.brown.fox" doesn't match any binding so it will be discarded.
•	Message “lazy.orange.male.rabbit", even though it has four words, will match the last binding and will be delivered to the second queue because of wildcard ‘#’ if it is ‘*’ then message will be ignored as the notation accepts only one word whereas ‘#’ zero or more words.
 If we do not use the wild card operators then topic exchange will also behave as direct exchange.

# Headers Exchange:
                Header exchanges route based on arguments containing headers and optional values. Header exchanges are most likely behaves as topic exchange but it routes based on header values instead of routing keys. A message will be consumed if the header values are matching with bindings.

                A special argument named "x-match", which can be added in the binding between your exchange and your queue, tells if all headers must match or just one. Either any common header between the message and the binding count as a match, or all the headers referenced in the binding need to be present in the message for it to match. The "x-match" property can have two different values: "any" or "all", where "all" is the default value. A value of "all" means all header pairs (key, value) must match and a value of "any" means at least one of the header pairs must match. Headers can be constructed using a wider range of data types - integer or hash for example instead of a string. The headers exchange type (used with the binding argument "any") is useful for directing messages which may contain a subset of known (unordered) criteria

![image](https://user-images.githubusercontent.com/21335378/49989185-4d952a80-ff2e-11e8-97e6-dd09fc40ed16.png)

Let’s understand from the below example:
In the above picture we are publishing messages to exchange with different key value pairs along with binding argument “x-match”. The default value of  “X-match is all” if we don’t specify x-match it will be treated as “all”.
Message1 will be consumed by Queue A because it is the only one matching with format and type.
Message2 will be consumed by  Queue A and B because the x-match is Any means format pdf is there for two queues.
Message3 will be ignored as it is not matching with all the headers.

# Some of the common things used in RabbitMQ: 
# Queues: 
  There are two types of queue we will be able to create which are mentioned below
![image](https://user-images.githubusercontent.com/21335378/49989323-b54b7580-ff2e-11e8-9a50-99a657b7b8f4.png)

•	Transient: If the node goes down or restarted and once come up then the queue will not be available from the system. But the queue will be created again as soon as any new message being routed to the queue. 
•	Durable:   Once node goes down or restarted durable queues comes up automatically as it will not fait message like Transient. The benefit of creating durable queue will help to keep the message in queue if the message was published as persistent delivery mode.
•	If we create queue directly in rabbit mq, it creates under default exchange which is same behavior for all entities such as exchanges, queues.
# Publish Message: 
Message can be published to queue using any of the below delivery modes.
•	Non Persistent: When the node goes down/restarts we will lose the messages
•	Persistent: It will store in the messages on disk which is basically persistent store. The throughput will be less if all message are persistent.
![image](https://user-images.githubusercontent.com/21335378/49989354-d2804400-ff2e-11e8-8d75-a7d84b6ad0c4.png)

# Requeue :
From the Rabbitmq broker system while consuming message there are two options available for Requeue 
          No -->Which mean it will fetch the message from the queue and it will delete the message from the queue which is more like sending acknowledgement to the queue saying message is successfully consumed.
          Yes -->It will not delete the message from the queue means we are rejecting the message to requeue again to consume properly.


![image](https://user-images.githubusercontent.com/21335378/49989377-e0ce6000-ff2e-11e8-8dd5-dc1d020ecfec.png)

