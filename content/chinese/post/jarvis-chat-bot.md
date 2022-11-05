---
title: "A.L.I.C.E"
date: 2022-11-05T13:38:33+08:00
# post thumb
images:
- "images/post/post-1.jpg"
#author
author: "郁金香啊"
# description
description: "基于AIML的聊天机器人"
# Taxonomies
categories: ["AI","ALICE","Chat Bot", "gRPC"]
tags: ["AI","ALICE","聊天机器人"]
type: "regular"
draft: false
---

最近在看rpc相关的内容，刚好看到gRPC关于客户端服务端双向流模式(bidirectional streaming)，所以也想顺便写个小demo看下效果。

这种双向流模式，我觉得也可以实现一个聊天的效果，所以就搜索了一下，然后发现一个库，[基于AIML（Artificial Intelligence Markup Language）实现聊天机器人](https://github.com/alejandro-du/vaadin-ai-chat.git/) ，也觉得很有意思就拿来用一下。

关于 [AIML](https://www.pandorabots.com/pandora/pics/wallaceaimltutorial.html) 以及 [ALICE](https://github.com/28kayak/Alicebot) 相关的内容，如果感兴趣，可以查看连接。

此处基于这个库以及Alice的AIML实现了一个简单的星爵向Jarvis问问题的小Demo。

```protobuf
//jarvis.proto
syntax = "proto3";

option java_multiple_files = true;
option java_package = "io.grpc.examples.jarvis";
option java_outer_classname = "JarvisAI";

message Question {
  string content = 1;
}

message Answer {
  string content = 1;
}

service Jarvis {
  rpc answer (stream Question) returns (stream Answer) {}
}
```

```java
package io.grpc.examples.jarvis;

import io.grpc.Server;
import io.grpc.ServerBuilder;
import io.grpc.stub.ServerCallStreamObserver;
import io.grpc.stub.StreamObserver;
import org.alicebot.ab.Bot;
import org.alicebot.ab.Chat;
import org.alicebot.ab.configuration.BotConfiguration;

import java.io.IOException;
import java.util.concurrent.TimeUnit;
import java.util.logging.Logger;

public class JarvisServer {
    private static final Logger logger = Logger.getLogger(JarvisServer.class.getName());

    private final Server server;

    public JarvisServer(int port) {
        this.server = ServerBuilder.forPort(port).addService(new Jarvis()).build();
    }

    public void onDuty() throws IOException {
        server.start();

        logger.info("Jarvis is online now.");

        Runtime.getRuntime().addShutdownHook(new Thread(() -> {
            try {
                logger.info("Jarvis is going to shutdown.");
                JarvisServer.this.offDuty();
                logger.info("Jarvis has been successfully shutdown");
            } catch (Exception e) {
                logger.warning("Error occurred while shutting down Jarvis");
            }
        }));
    }

    private void offDuty() throws Exception {
        if (server != null) {
            server.shutdown().awaitTermination(10, TimeUnit.SECONDS);
        }
    }

    private void keepWakeUp() throws InterruptedException {
        if (server != null) {
            server.awaitTermination();
        }
    }

    public static void main(String[] args) throws Exception {
        JarvisServer server = new JarvisServer(6666);
        server.onDuty();
        server.keepWakeUp();
    }

    private static class Jarvis extends JarvisGrpc.JarvisImplBase {
        private final Chat chatSession;

        public Jarvis() {
            chatSession = new Chat(
                new Bot(BotConfiguration.builder()
                    .name("jarvis")
                    .path("src/main/resources")
                    .build())
            );
        }

        @Override
        public StreamObserver<Question> answer(StreamObserver<Answer> responseObserver) {
            final ServerCallStreamObserver<Answer> serverCallStreamObserver =
                (ServerCallStreamObserver<Answer>) responseObserver;
            serverCallStreamObserver.disableAutoRequest();

            class OnReadyHandler implements Runnable {
                private boolean isReady;

                @Override
                public void run() {
                    if(serverCallStreamObserver.isReady() && !isReady) {
                        isReady = true;

                        serverCallStreamObserver.request(1);
                    }
                }
            }

            OnReadyHandler onReadyHandler = new OnReadyHandler();

            serverCallStreamObserver.setOnReadyHandler(onReadyHandler);

            return new StreamObserver<Question>() {
                @Override
                public void onNext(Question question) {
                    logger.info("Questioner: " + question.getContent());

                    String answer = chatSession.multisentenceRespond(question.getContent());

                    thinking();

                    logger.info("Jarvis: " + answer);

                    responseObserver.onNext(Answer.newBuilder().setContent(answer).build());

                    if(serverCallStreamObserver.isReady()) {
                        serverCallStreamObserver.request(1);
                    } else {
                        onReadyHandler.isReady = false;
                    }
                }

                private void thinking() {
                    try {
                        Thread.sleep(1000 + Math.round(Math.random() * 5000));
                    } catch (InterruptedException ignore) {
                        logger.warning("Interrupted when thinking.");
                    }
                }

                @Override
                public void onError(Throwable t) {
                    logger.info("Sorry, Jarvis has crashed!");
                }

                @Override
                public void onCompleted() {
                    logger.info("Cool, mission completed!");
                }
            };
        }
    }
}
```

```java
package io.grpc.examples.jarvis;

import io.grpc.ManagedChannel;
import io.grpc.ManagedChannelBuilder;
import io.grpc.stub.ClientCallStreamObserver;
import io.grpc.stub.ClientResponseObserver;

import java.util.Arrays;
import java.util.Iterator;
import java.util.List;
import java.util.concurrent.CountDownLatch;
import java.util.concurrent.TimeUnit;
import java.util.logging.Logger;

public class StarLord {
    private static final Logger logger = Logger.getLogger(StarLord.class.getName());

    public static void main(String[] args) throws InterruptedException {
        final CountDownLatch done = new CountDownLatch(questions().size());

        ManagedChannel channel = ManagedChannelBuilder
            .forAddress("localhost", 6666)
            .usePlaintext()
            .build();
        JarvisGrpc.JarvisStub stub = JarvisGrpc.newStub(channel);

        ClientResponseObserver<Question, Answer> clientResponseObserver =
            new ClientResponseObserver<Question, Answer>() {
                private ClientCallStreamObserver<Question> requestStream;

                @Override
                public void onNext(Answer answer) {
                    logger.info("Jarvis: " + answer.getContent());

                    tryingToUnderstand();

                    done.countDown();

                    requestStream.request(1);
                }

                private void tryingToUnderstand() {
                    try {
                        Thread.sleep(1000 + Math.round(Math.random() * 5000));
                    } catch (Exception e) {
                        logger.warning("Star lord failed to understand the answer.");
                    }
                }

                @Override
                public void onError(Throwable t) {
                    logger.info("Star lord has asked too many question so he got exhausted.");
                    done.countDown();
                }

                @Override
                public void onCompleted() {
                    logger.info("Star lord doesn't have any questions any more!.");
                    done.countDown();
                }

                @Override
                public void beforeStart(ClientCallStreamObserver<Question> requestStream) {
                    this.requestStream = requestStream;

                    requestStream.disableAutoRequestWithInitial(1);

                    requestStream.setOnReadyHandler(new Runnable() {
                        private final Iterator<String> questionIterator = questions().iterator();

                        @Override
                        public void run() {
                            while (requestStream.isReady()) {
                                if (questionIterator.hasNext()) {
                                    String question = questionIterator.next();
                                    logger.info("Start Lord: " + question);

                                    requestStream.onNext(Question.newBuilder().setContent(question).build());
                                } else {
                                    logger.info("Start Lord has no question.");

                                    requestStream.onCompleted();
                                }
                            }
                        }
                    });
                }
            };

        stub.answer(clientResponseObserver);

        done.await();

        channel.shutdown();
        channel.awaitTermination(1, TimeUnit.SECONDS);
    }

    private static List<String> questions() {
        return Arrays.asList(
            "What's your name?",
            "I am stupid Star Lord.",
            "I am stupid Star Lord.",
            "I am stupid Star Lord.",
            "I am stupid Star Lord.",
            "I am stupid Star Lord.",
            "I am stupid Star Lord.",
            "I am stupid Star Lord.",
            "I am stupid Star Lord."
        );
    }
}
```