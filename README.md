# reactor-core

[![Join the chat at https://gitter.im/reactor/reactor](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/reactor/reactor?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

[![Build Status](https://drone.io/github.com/reactor/reactor-core/status.png)](https://drone.io/github.com/reactor/reactor-core/latest)

Non-Blocking [Reactive Streams](http://reactive-streams.org) Foundation for the JVM both implementing a lite [Reactive Extensions]
(http://reactivex.io) API and efficient message-passing support.

## Getting it

With Gradle from repo.spring.io or Maven Central repositories (stable releases only):
```groovy
    repositories {
      //maven { url 'http://repo.spring.io/libs-release' }
      //maven { url 'http://repo.spring.io/libs-milestone' }
      maven { url 'http://repo.spring.io/libs-snapshot' }
      mavenCentral()
    }

    dependencies {
      compile "io.projectreactor:reactor-core:2.5.0.BUILD-SNAPSHOT"
    }
```

## Flux

A Reactive Streams Publisher with basic Rx operators. 
- Static factories on Flux allow for source generation from arbitrary callbacks types.
- Instance methods allows operational building, materialized on each _Flux#subscribe()_ eventually called.

[<img src="https://raw.githubusercontent.com/reactor/projectreactor.io/master/src/main/static/assets/img/marble/flux.png" width="500">](http://projectreactor.io/core/docs/api/reactor/core/publisher/Flux.html)

Flux in action :
```java
Flux.fromIterable(getSomeList())
    .mergeWith(Flux.interval(1))
    .map(d -> d * 2)
    .zipWith(Flux.just(1, 2, 3))
    .onErrorResumeWith(errorHandler::fallback)
    .subscribe(Subscribers.consumer(System.out::println));
```

## Mono
A Reactive Streams Publisher constrained to *ZERO* or *ONE* element with appropriate operators. 
- Static factories on Mono allow for deterministic *zero or one* sequence generation from arbitrary callbacks types.
- Instance methods allows operational building, materialized on each _Mono#subscribe()_ or _Mono#get()_ eventually called.

[<img src="https://raw.githubusercontent.com/reactor/projectreactor.io/master/src/main/static/assets/img/marble/mono.png" width="500">](http://projectreactor.io/core/docs/api/reactor/core/publisher/Mono.html)

Mono in action :
```java
Mono.fromCallable(System::currentTimeMillis)
    .then(time -> Mono.any(serviceA.findRecent(time), serviceB.findRecent(time)))
    .or(Mono.delay(3))
    .otherwiseIfEmpty(Mono.just(errorHandler::fallback)
    .subscribe(Subscribers.consumer(System.out::println));
```

## Processors

### Sync Pub-Sub : EmitterProcessor

```java
```

### Async Pub-Sub : TopicProcessor

```java
```

### Async Distributed : WorkQueueProcessor

```java
```

### Hot Publishing : SignalEmitter
Usually, Processors do not support onNext call if onSubscribe has not been called before OR if there is a mismatching demand (onNext without demand). To bridge a Subscriber or Processor into an outside context that is taking care of producing non concurrently, use SignalEmitter.create() or the common FluxProcessor.startEmitter():

```java
EmitterProcessor<String> processor = EmitterProcessor.create();
processor
    .doOnNext(System.out::println)
    .subscribe();

SignalEmitter<String> emitter = processor.startEmitter();
emitter.emit("Non blocking and returning emission status");
emitter.submit("Blocking until emitted");
emitter.onNext("Fail if overrun");
emitter.finish();

```

## Schedulers

Create and Reuse scheduling resources over multiple Subscribers, with adapated strategies :

```java
SchedulerGroup async = SchedulerGroup.async();
SchedulerGroup io = SchedulerGroup.io();

Flux.create( sub -> sub.onNext(System.currentTimeMillis()) )
    .dispatchOn(async)
    .log("foo.bar")
    .flatMap(time ->
        Mono.fromCallable(() -> { Thread.sleep(1000); return time; })
            .publishOn(io)
    )
    .subscribe();
```

## The Backpressure Thing

```java
```

## What's more in it ?

"Operator Fusion" (flow optimizers), health state observers, helpers to build custom reactive components, bounded queue generator, hash-wheel timer, converters from/to RxJava1, Java 9 Flow.Publisher and Java 8 CompletableFuture.

## Reference
http://projectreactor.io/core/docs/reference/

## Javadoc
http://projectreactor.io/core/docs/api/

-------------------------------------
_Powered by [Reactive Stream Commons](http://github.com/reactor/reactive-streams-commons)_

_Licensed under [Apache Software License 2.0](www.apache.org/licenses/LICENSE-2.0)_

_Sponsored by [Pivotal](http://pivotal.io)_
