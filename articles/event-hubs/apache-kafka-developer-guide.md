---
title: Apache Kafka-Entwicklerleitfaden für Event Hubs
description: Dieser Artikel enthält Links zu Artikeln, in denen beschrieben wird, wie Sie Ihre Kafka-Anwendungen in Azure Event Hubs integrieren können.
ms.date: 09/20/2021
ms.topic: article
ms.openlocfilehash: 1206d01a644545d1cbcc1d92a941fc4e29aa4d76
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128660878"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Apache Kafka-Entwicklerleitfaden für Azure Event Hubs
Dieser Artikel enthält Links zu Artikeln, in denen beschrieben wird, wie Sie Ihre Apache Kafka-Anwendungen in Azure Event Hubs integrieren können. 

## <a name="overview"></a>Übersicht
Event Hubs stellt einen Kafka-Endpunkt bereit, der von Ihren vorhandenen Kafka-basierten Anwendungen als Alternative zum Betreiben eines eigenen Kafka-Clusters verwendet werden kann. Event Hubs funktioniert mit vielen Ihrer vorhandenen Kafka-Anwendungen. Weitere Informationen finden Sie unter [Event Hubs für Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).

## <a name="quickstarts"></a>Schnellstarts
Sie finden Schnellstarts in GitHub und in diesen Ressourcen, mit denen Sie sich schnell über Event Hubs für Kafka informieren können.

### <a name="quickstarts-in-github"></a>Schnellstarts in GitHub
Sehen Sie sich die folgenden Schnellstarts im Repository **azure-event-hubs-for-kafka** an: 

| Clientsprache/Framework | BESCHREIBUNG | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>In dieser Schnellstartanleitung erfahren Sie, wie Sie einen Event Hubs Kafka-Endpunkt erstellen und eine Verbindung mit diesem herstellen, indem Sie einen Beispielproducer und -consumer verwenden, der in C# mit .NET Core 2.0 geschrieben wurde.</p><p>Dieses Beispiel basiert auf dem [Apache Kafka .NET-Client von Confluent](https://github.com/confluentinc/confluent-kafka-dotnet), der für die Verwendung mit Event Hubs für Kafka geändert wurde.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | In dieser Schnellstartanleitung erfahren Sie, wie Sie einen Event Hubs Kafka-Endpunkt erstellen und eine Verbindung mit diesem herstellen, indem Sie einen Beispielproducer und -consumer verwenden, der in Java geschrieben wurde. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>In dieser Schnellstartanleitung erfahren Sie, wie Sie einen Event Hubs Kafka-Endpunkt erstellen und eine Verbindung mit diesem herstellen, indem Sie einen Beispielproducer und -consumer verwenden, der in Node geschrieben wurde.</p><p>In diesem Beispiel wird die [node-rdkafka](https://github.com/Blizzard/node-rdkafka)-Bibliothek verwendet. </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>In dieser Schnellstartanleitung erfahren Sie, wie Sie einen Event Hubs Kafka-Endpunkt erstellen und eine Verbindung mit diesem herstellen, indem Sie einen Beispielproducer und -consumer verwenden, der in Python geschrieben wurde.</p><p>Dieses Beispiel basiert auf dem [Apache Kafka Python-Client von Confluent](https://github.com/confluentinc/confluent-kafka-python), der für die Verwendung mit Event Hubs für Kafka geändert wurde.</p>|
| [Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>In dieser Schnellstartanleitung erfahren Sie, wie Sie einen Event Hubs Kafka-Endpunkt erstellen und eine Verbindung mit diesem herstellen, indem Sie einen Beispielproducer und -consumer verwenden, der in Go geschrieben wurde.</p><p>Dieses Beispiel basiert auf dem [Apache Kafka Golang-Client von Confluent](https://github.com/confluentinc/confluent-kafka-go), der für die Verwendung mit Event Hubs für Kafka geändert wurde.</p>| 
| [Sarama kafka Go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | In dieser Schnellstartanleitung erfahren Sie, wie Sie einen Event Hubs Kafka-Endpunkt erstellen und eine Verbindung mit diesem herstellen, indem Sie einen Beispielproducer und -consumer verwenden, der mithilfe der [Sarama Kafka-Clientbibliothek](https://github.com/Shopify/sarama) geschrieben wurde. |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | In dieser Schnellstartanleitung erfahren Sie, wie Sie einen Event Hubs Kafka-Endpunkt mithilfe der CLI (die in der Apache Kafka-Distribution enthalten ist) erstellen und dann eine Verbindung mit diesem Endpunkt herstellen.| 
| [Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat ist ein Nicht-JVM-Befehlszeilenconsumer und -producer, der auf librdkafka basiert und aufgrund seiner Geschwindigkeit und seines geringen Speicherbedarfs beliebt ist. Dieser Schnellstart enthält eine Beispielkonfiguration und mehrere einfache kafkacat-Beispielbefehle. | 
 
### <a name="quickstarts-in-docs"></a>Schnellstarts in der Dokumentation
Weitere Informationen finden Sie im folgenden Schnellstart: [Datenstreaming mit Event Hubs mithilfe des Kafka-Protokolls](event-hubs-quickstart-kafka-enabled-event-hubs.md) in dieser Dokumentation. Er stellt schrittweise Anleitungen zum Streamen von Daten in Event Hubs bereit. Sie lernen, wie Sie über Ihre Producer und Consumer mit Event Hubs kommunizieren – mit nur einer Konfigurationsänderung in Ihren Anwendungen. 


## <a name="tutorials"></a>Tutorials 

### <a name="tutorials-in-github"></a>Tutorials in GitHub
Arbeiten Sie die folgenden Tutorials in GitHub durch:

| Lernprogramm | BESCHREIBUNG | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | Dieses Tutorial veranschaulicht, wie Sie Akka Streams mit Kafka-fähigen Event Hubs verbinden können, ohne Ihre Protokollclients ändern oder Ihre eigenen Cluster ausführen zu müssen. Es gibt zwei separate Tutorials, in denen die Programmiersprachen **Java** und **Scala** verwendet werden. | 
| [Herstellen einer Verbindung](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | In diesem Tutorial wird die Vorgehensweise zum Integrieren von Kafka Connect in Azure Event Hubs und zum Bereitstellen von einfachen FileStreamSource- und FileStreamSink-Connectors beschrieben. Die Connectors sind zwar nicht für die Verwendung in der Produktion bestimmt, aber sie stellen ein End-to-End-Szenario für Kafka Connect dar, bei dem Azure Event Hubs als Kafka-Broker fungiert.| 
| [Filebeat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | Dieses Dokument führt Sie schrittweise durch die Integration von Filebeat und Event Hubs über die Kafka-Ausgabe von Filebeat. | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | Dieses Tutorial veranschaulicht, wie Sie Apache Flink mit Kafka-fähigen Event Hubs verbinden können, ohne Ihre Protokollclients ändern oder Ihre eigenen Cluster ausführen zu müssen. | 
| [FluentD](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | Dieses Dokument führt Sie schrittweise durch die Integration von Fluentd und Event Hubs mithilfe des `out_kafka`-Ausgabe-Plug-Ins für Fluentd. |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | In diesem Tutorial wird gezeigt, wie Sie Ereignisse zwischen Consumern und Producer mithilfe verschiedener Protokolle austauschen können. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | Dieses Tutorial führt Sie schrittweise durch die Integration von Logstash in Kafka-fähige Event Hubs mithilfe von Logstash Kafka-Eingabe-/Ausgabe-Plug-Ins. | 
| [MirrorMaker](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | In diesem Tutorial erfahren Sie, wie Sie mit einem Event Hub und Kafka MirrorMaker eine vorhandene Kafka-Pipeline in Azure integrieren, indem Sie den Kafka-Eingabedatenstrom im Event Hubs-Dienst spiegeln. |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | In diesem Tutorial wird gezeigt, wie Sie Apache NiFi mit einem Event Hubs-Namespace verbinden. | 
| [OAuth](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | In diesen Schnellstarts erfahren Sie, wie Sie einen Event Hubs Kafka-Endpunkt erstellen und eine Verbindung mit diesem herstellen, indem Sie einen Beispielproducer und -consumer verwenden, der in den Programmiersprachen Go und Java geschrieben wurde. |
| [Schemaregistrierung von Confluent](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | Dieses Tutorial führt Sie schrittweise durch die Integration der Schemaregistrierung und Event Hubs für Kafka. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | Dieses Tutorial veranschaulicht, wie Sie Ihre Spark-Anwendung mit einem Event Hub verbinden können, ohne Ihre Protokollclients ändern oder eigene Kafka-Cluster ausführen zu müssen. | 

### <a name="tutorials-in-docs"></a>Tutorials in der Dokumentation
Weitere Informationen finden Sie auch in diesem Tutorial: [Verarbeiten von Apache Kafka für Event Hubs-Ereignisse mithilfe von Stream Analytics](event-hubs-kafka-stream-analytics.md). Es zeigt, wie Daten in Event Hubs gestreamt und mit Azure Stream Analytics verarbeitet werden.

## <a name="how-to-guides"></a>Anleitungen
Weitere Informationen finden Sie in den folgenden Schrittanleitungen in unserer Dokumentation:

| Artikel | BESCHREIBUNG | 
| ------- | ----------- | 
| [Spiegeln eines Kafka-Brokers in einem Event Hub](event-hubs-kafka-mirror-maker-tutorial.md) | Zeigt, wie Sie einen Kafka-Broker in einem Event Hub mit Kafka MirrorMaker spiegeln. |
| [Verbinden von Apache Spark mit einem Event Hub](event-hubs-kafka-spark-tutorial.md) | Beschreibt ausführlich, wie Sie zum Zweck des Echtzeitstreamings eine Verbindung zwischen Ihrer Spark-Anwendung und einer Event Hubs-Instanz herstellen. |
| [Verbinden von Apache Flink mit einem Event Hub](event-hubs-kafka-flink-tutorial.md) | Zeigt, wie Sie Apache Flink mit einem Event Hub verbinden können, ohne Ihre Protokollclients ändern oder Ihre eigenen Cluster ausführen zu müssen. |
| [Integrieren von Apache Kafka Connect in einen Event Hub (Vorschau)](event-hubs-kafka-connect-tutorial.md) | Führt Sie schrittweise durch die Vorgehensweise zum Integrieren von Kafka Connect in einen Event Hub und zum Bereitstellen von einfachen FileStreamSource- und FileStreamSink-Connectors. |
| [Verbinden von Akka Streams mit einem Event Hub](event-hubs-kafka-akka-streams-tutorial.md) | Veranschaulicht, wie Sie Akka Streams mit einem Event Hub verbinden können, ohne Ihre Protokollclients ändern oder Ihre eigenen Cluster ausführen zu müssen. |
| [Verwenden von Spring Boot Starter für Apache Kafka mit Azure Event Hubs](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Beschreibt, wie Sie eine mit Spring Boot Initializer erstellte Java-basierte Spring Cloud Stream Binder-Anwendung zur Verwendung von Apache Kafka mit Azure Event Hubs konfigurieren. |

## <a name="next-steps"></a>Nächste Schritte
Sehen Sie sich die Beispiele im GitHub-Repository [azure-event-hubs-for-kafka](https://github.com/Azure/azure-event-hubs-for-kafka) unter den Schnellstart- und Tutorialordnern an.

Lesen Sie auch die folgenden Artikel:

- [Apache Kafka-Leitfaden zur Problembehandlung für Event Hubs](apache-kafka-troubleshooting-guide.md)
- [Häufig gestellte Fragen (FAQ) zu Event Hubs für Apache Kafka](apache-kafka-frequently-asked-questions.yml)
- [Apache Kafka-Migrationsleitfaden für Event Hubs](apache-kafka-migration-guide.md)



