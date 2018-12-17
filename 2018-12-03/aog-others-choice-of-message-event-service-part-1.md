---
title: "Azure 消息 & 事件服务的选择 – 上篇"
description: "Azure 消息 & 事件服务的选择 – 上篇"
author: Miley Chen
resourceTags: 'Storage Queue, Service Bus, Event Hub'
ms.service: multiple
wacn.topic: aog
ms.topic: article
ms.author: Miley Chen
ms.date: 12/17/2018
wacn.date: 12/17/2018
---

# Azure 消息 & 事件服务的选择 – 上篇

Microsoft Azure 平台提供了不同类型处理消息或事件的服务。用户可以将他们的应用数据（消息/事件）传递到这些服务中，也可以通过一定应用程序或者服务获取放在这些服务中的数据。对于刚开始或者准备开始使用 Azure 消息 & 事件服务的用户来讲，心里会存有以下疑问：这些消息 & 事件服务有什么区别，各有什么特征呢？或者这些服务分别适用于哪些应用场景呢？这篇文章就以上的问题展开做详细的介绍。

首先我们来看一下在 Azure 平台上有哪些可用的消息/事件服务，目前 Azure 平台提供 8 种不同类型的消息/事件服务它们分别是：

* Storage queue

* Service bus queue

* Service bus topic

* Event Hub

* IOT Hub

* Service bus Relay

* Notification Hub

* Event Grid

通过以下图表我们先直观清晰的对这些服务有一个大概的了解。

![01](media/aog-others-choice-of-message-event-service-part-1/01.png "01")

接下来我们就这些服务分别做相应的介绍：

1. Storage queue

    1. 什么是 Storage queue

        Storage queue 服务是 Azure 存储提供的存储服务之一，也是 Azure 平台最早出现的 messaging 服务，可以追溯到 2010 年。Storage queue 允许用户存储大量的消息，这里讲的量可达到甚至几百 TB 的数量级。

    2. 适用场景

        如果用户刚开始接触云端消息队列服务，并且有需求存储 TB 数量级别的消息时，可以考虑使用 Storage queue。

    3. 服务特性

        Storage queue 服务的主要优势在于：实现简单，节省费用(用多少付多少)，支持存储大量的消息。

        另外 Storage queue 支持在消费消息时 "at  least once" 的模式，换句话说一个客户端接收某条消息后，这条消息不会从服务端被删除掉，这样其他的客户端也可以再次接收该条消息。因此使用 Storage queue 服务时要注意接收端可能会受到重复消息。

        Storage queue 有一项很有意思的功能是其他 Azure 消息服务没有的，那就是 Storage queue 提供记录日志的功能，用户需要在 Storage 服务端开启日志记录功能，这样所有对 queue 的操作包括这些操作来源于那些IP地址都会被记录，方便用户去检索对应的操作行为。

    4. 一些限制和说明

        用户在使用 Storage queue 的时候，有些服务端的限制也需要考虑，比如在 Storage queue 里能够存储的消息大小最大为 64KB，消息最多可以在服务端存放 7 天（Max TTL = 7 days）。

2. Service bus queue

    1. 什么是 Service bus queue

        Azure Service bus queue 相比较 Storage queue 是 Microsoft Azure 提供的具备更强大功能、更复杂的消息传递服务，可以支持更加复杂的消息传递解决方案。

        当我们从 Service bus queue 中接收消息时支持 "First In, First Out(FIFO)" 的消息传递模式，这种模式下每条消息只能被一个消费端接收。

    2. 服务特性

        Service bus queue 有一个很特殊的功能，就是 “死信” 队列，简单讲，就是当客户端无法成功接收到某条消息或者消息在一定的期限中过期没有被接收时，该条消息会自动转移到第二个队列中，也就是 “死信” 队列。

        另外 Service bus queue 的一个很有意思的功能就是可以检测重复消息，一旦开启该功能，如果接收端重复接收一条已经接收过的消息，第二条重复的消息会被忽略。

        值得被提的一点是我们提供两种不同从 Service bus queue 中接收消息的方式，分别是 "Peek and Lock" 和 "Receive and Delete"，当用户采用第一种方式（也是默认方式）接收消息时，当接收端还在处理该条消息的时候，这条消息会被服务端锁定，从而其他接收端无法接收该条消息，直到接收端成功处理完这条消息后，这条消息才会从 queue 中删除掉。如果在一定的时间段(可以设置的 timeout 时间) 服务端还是没有收到接收端的成功接收的返回请求，那么该条消息会被服务端解除锁定重新释放掉，这个时候其他的接收端可以再次接收这条消息。

    3. 与 Storage queue 的区别

        首先 Service bus queue 与 Storage queue 底层的实现是完全不一样的。相比较 Storage queue,Service bus queue 存储的的消息大小最大可以达到 256KB，并且可以根据属性 TimeSpan.Max 调整消息存储在服务端的时间，满足用户需要长时间存储消息的需求，然而不同与 Storage queue 强大的存储功能，Service bus queue 最大能存放 80GB 的消息。

        其次 Service bus queue 服务支持使用 AMQP 1.0 协议进行数据传输，这一特点很好的适用于嵌入式设备。这意味着，用户可以使用 Service bus queue 构建 cross-platform 的混合应用，用户也可以在不同的操作平台上使用不同语言和开发框架去连接 Service bus queue。

        最后正如我们上一节“服务特性”中讲到的，相比较 Storage queue，Service bus queue 提供更丰富更强大的消息传递功能。

        更多关于 Storage queue 与 Service bus queue 的区别用户可以参考[官方对比文档](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted)。

    4. 适用场景

        Service bus 服务主要提供给用户一种强大的企业级消息传递解决方案，在这种典型的云端消息方案体系下，Service bus 将服务端和应用程序进行分离。

        细心的读者在阅读 Service bus queue 的“服务特性”章节后不难发现，Service bus queue 更加适用于对消息敏感（即不允许重复消息、不允许消息丢失），需要长期存储消息的使用场景。

    5. 一些限制和说明

        Service bus 服务提供不同的等级 (SKU) 供用户选择，在标准和高级版本（目前高级版本还没有在中国上线）的服务中，Service bus queue 最大可以到达 80GB。

        另外在在同一个命名空间下 (namespace) 的所有服务（包括 Service bus queue 以及 Service bus topic）最大的并发连接数可以达到 5000 个，这里包括接收端的连接也包括发送端的连接。

3. Service bus

    1. 什么是 Service bus topic

        Service bus topic 与 Service bus queue 很类似，因此 Service bus queue 所具备的功能 topic 都有。但是 Service bus topic 还有一个非常重要也区别于 queue 的功能，就是一条消息发送到 topic 中，可以生成多个 copy 根据一定的规则分发到多个的接收端，这里我们称为订阅 (subscribers)。

        我们可以将每个订阅理解为一个 “queue”，而具体每个 queue 里接收的消息是由对应的规则定义的。

    2. 服务特性

        Service bus topic 是典型的支持 Publish / Subscribe 消息传输模式的服务。每个 topic 最多可以支持 2000 个订阅，这意味着每一条发到 topic 里的消息都可以被多达 2000 个订阅接收。我们可以在 topic 运行期间的任何时刻增加新的订阅，这不会影响其他已经存在的订阅接收消息，一旦新的订阅添加成功，新进入 topic 中的消息就会根据规则被新添加的订阅接收。

        上面我们提到“规则”，在 Service bus topic 的世界中，我们将其称为 “filter”，用户可以根据自己的需求为每个订阅定义不同的 “filter” 规则（每一条传递到service bus服务中的消息都会包含一组属性 properties，用户可以通过使用属性来自定义“filter”规则）所有满足 “filter” 规则的消息将会被相应的订阅接收。通过这样的方式，可以达到不同的订阅侦听或接收不同消息的目的。

        Service bus topic 的特性不止于此，我们不仅可以自定义不同的 “filter” 规则，我们还可以为每个订阅定义不同的 action，即当该订阅接收到相应的消息后就会执行对应的 action（比如我们可以修改某个属性的名称值等等），这对于某些场景非常有用。

    3. 与 Service bus queue 的区别

        根据前面的介绍，我们能得到 Service bus queue 与 topic 一个最大的区别在于：Service bus queue 是 "one to one" 的消息分发模式，而 Service bus topic 是 "one to many" 的消息分发模式。

        Queue 的消息分发模式可以参照下图：

        ![02](media/aog-others-choice-of-message-event-service-part-1/02.png "02")

        Topic 的消息分发模式可以参考下图：

        ![03](media/aog-others-choice-of-message-event-service-part-1/03.png "03")

    4. 适用场景

        从上面的特性描述中我们不难发现，如果用户有需要将同一份消息传递到不同的接收端或系统中做不同的后续分析或处理，并且接收端可能在动态变化，那么 Service bus topic 将会是非常好的解决方案。

4. Event Hub

    1. 什么是 Event Hub

        Event Hub 服务可以在短时间内接收由设备或服务生产大量数据流。

        这里的“短时间”和“大量”可以达到每秒钟接收百万级别的数量。传递到 Event Hub 的数据更准确来讲是数据流，即使流动到 Event Hub 的数据量非常大，也可以保证低延迟接收到所有的数据流，并且系统是可靠稳定的。

        在 Event Hub 的世界里，数据被称为 “事件” 而不再是 “消息” ，这里我们对这两个概念做进一步的介绍，这将帮助我们了解消息服务和时间服务的区别。“事件”和“消息”是不同的，简单来讲消息是有明确目的意图的，即它被发送到后端是用来 执行特定的操作，发送端需要知道对应的操作是否被执行，需要得到对应的响应；而“事件”则是反映一个事实，是发生在过去的一个事件记录，发送端发出这个“事件”后则不需要了解后续的操作，也不需要拿到接收端收到“事件”的响应。

    2. 服务特性

        Event Hub 做为处理高并发数据流的服务有丰富的产品特性：

        首先 Event Hub 支持用户使用 AMQP 协议发送事件到服务端，这方便不同的设备、应用传递事件到服务端。

        其次在 Event Hub 中，用户是通过 “consumer groups” 来接收发送到 Event Hub 中的事件的，用户可以创建多个 “consumer groups”，这样每个接收端可以根据自己的节奏并且依赖于特定的 “consumer groups” 来接收 Event Hub 中的数据，依赖于不同 “consumer groups” 的不同的接收端相互之间是完全独立互不干扰的。

        另外在 Event Hub 中有一项非常重要的功能，即 Event Hub 允许接收端从不同的位置开始接收消息，这依赖于 Event Hub 中一个非常重要的概念 offset（我们可以理解为游标），offset 就是用来记录接收端当前在 Event Hub 中读取事件的位置。在 Event Hub 中用户可以定义多个 partition，我们可以将每个 partition 理解为一个队列，接收端读取每个partition上的事件，因此每个 partition 上都会对应有一个 offset 用来记录当前的位置，而 “consumer groups” 则可以理解为一组 offset 记录，它包含了当前接收端在每个 partition 上读取消息的当前位置。通过这项功能，接收端不需要每次都从头开始获取消息，而是可以通过获取记录位置的 offset 值，从相应的位置开始获取数据。

        最后，作为处理高并发百万级别事件流的服务，Event Hub 有很好的扩容性，一方面用户可以定义多个 partition（目前我们最多可以自己创建 32 个 partition）分担并发数据流，另外一方面我们可以增加 TU (Throughput Units)，每个 TU 允许 1MB/S 的进入数据和 2MB/S 的输出数据。需要提的一点是 partition 和 TU 是两个不同方向上的扩容。我们可以将进入 Event Hub 的数据想象成一个水池中的水，这一水池（事件流）通过管道（partition）1 秒钟就进入 Event Hub 服务中。

        当我们增加 partition 时，相当于接到 Event Hub 中的管道增加，这样对于同样的数据流，增加 partition 则进入 Event Hub 的数据更快；当我们增加一个TU时，相当于允许再额外增加一水池的水，这样我们允许双倍的数据流1秒钟传入到 Event Hub 服务中。

        下面这张图表让我们能更直观的对 Event Hub 中 partition，consumer group 有个了解：

        ![04](media/aog-others-choice-of-message-event-service-part-1/04.png "04")

    3. 与 Service bus queue 的区别

        Event Hub 不同于以上 的三种服务 Storage queue/Service bus queue & topic，这些服务都是每次处理一条消息，而 Event Hub 则可以理解为是一个接收事件流的 “管道” (pipeline)。

        Event Hub 的设计侧重于数据流的解决方案，它是一个 “事件摄取器” ，用来接受并存储事件数据，并可以快速将事件流推送到接收端，具备每秒接收和处理数百万个事件的能力。而 Service bus queue 则主要面向传统的企业应用程序，是典型的传递消息任务的解决方案，这些解决方案中对消息的顺序、监测重复消息、保证数据不能丢失有很高的要求。

        更多关于这两项服务的区别可以参考[官方链接](https://docs.microsoft.com/azure/event-grid/compare-messaging-services)。

    4. 适用场景

        基于 Event Hub 产品特性，它非常适用于大量的消息的传递处理，如遥测或物联网的使用场景。

    5. 一些限制和说明

        Event Hub 允许多个接收端根据不同的 consumer group 独立相互不影响的接收消息，这一点与 Service bus topic 很像， 但是目前每个 Event Hub 下最多能创建 32 个 consumer group，而 topic 的 subscription 可以多达 2000 个。

        另外传递到 Event Hub 中的事情大小最大不能超过 256KB，TU 最多可以增加到 20 个，partition 最多可以增加到 32 个（需要注意 partition 在创建 Event Hub 时指定，一旦指定之后就不能改变）。

        在 Service bus queue 中用户可以设定消息有 TTL（time to live）值，而在 Event Hub 中用户可以指定事件的 retention period （最长保留期）值，这是两个不同的概念，消息一旦过了 TTL 值，该条消息是一定会从 Service bus queue 中移出，然而事件过了 retention period ，有可能继续保留在 Event Hub 服务中（取决于一段时间内数据量的多少），而可以保证的是在指定的 retention period 期限内，该事件是一定会在 Event Hub 中保留，目前 Event Hub 事件的 retention period 最大值为 7 天。

[下篇](aog-others-choice-of-message-event-service-part-2)我们会对另外的四种服务继续做分析和比较。