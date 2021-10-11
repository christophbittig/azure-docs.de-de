---
title: Azure-Schemaregistrierung in Event Hubs (Vorschau)
description: Dieser Artikel bietet eine Übersicht über die Unterstützung der Schemaregistrierung durch Azure Event Hubs (Vorschau).
ms.topic: overview
ms.date: 05/10/2021
ms.custom: references_regions
ms.openlocfilehash: 6ac6361643d9baafb41259e6db69f816d747b1d8
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129534828"
---
# <a name="azure-schema-registry-in-event-hubs-preview"></a>Azure-Schemaregistrierung in Event Hubs (Vorschau)
In vielen Ereignisstreaming- und Messagingszenarien enthält die Ereignis- oder Nachrichtennutzlast strukturierte Daten, die entweder serialisiert oder mithilfe eines schemagesteuerten Formats wie Apache Avro deserialisiert werden. Absender und Empfänger können die Integrität der Daten mit einem Schemadokument wie bei einem JSON-Schema überprüfen. Bei schemagesteuerten Formaten ist die Bereitstellung des Schemas für den Nachrichtenconsumer eine Voraussetzung für den Consumer, und die Daten deserialisieren zu können. 

Die **Azure-Schemaregistrierungs** ist eine Funktion von Event Hubs, die ein zentrales Repository für Schemadokumente für ereignisgesteuerte und nachrichtenzentrierte Anwendungen bereitstellt. Sie bietet Ihren Producer- und Consumeranwendungen die Flexibilität, Daten auszutauschen, ohne das Schema verwalten und gemeinsam nutzen zu müssen, und sich mit unterschiedlichen Geschwindigkeiten zu entwickeln. Die Schemaregistrierung stellt außerdem ein einfaches Governanceframework für wiederverwendbare Schemas bereit und definiert die Beziehung zwischen Schemas über ein Gruppierungskonstrukt (Schemagruppen).

> [!NOTE]
> - Die Funktion **Schemaregistrierung** ist derzeit als **Vorschau** verfügbar und nicht für Produktionsworkloads vorgesehen.
> - Das Feature ist im **Basic**-Tarif nicht verfügbar.

Bei schemagesteuerten Serialisierungsframeworks wie Apache Avro kann die Externalisierung von Serialisierungsmetadaten in gemeinsam genutzte Schemas auch dazu beitragen, den Mehraufwand für Typinformationen und Feldnamen pro Nachricht, die in jedem Datensatz enthalten sind, drastisch zu reduzieren, der bei getaggten Formaten wie JSON anfällt. Durch die Speicherung von Schemas zusammen mit den Ereignissen und innerhalb der Ereignisinfrastruktur wird sichergestellt, dass die für die Serialisierung/Deserialisierung erforderlichen Metadaten immer erreichbar sind und Schemas nicht falsch platziert werden können. 

## <a name="event-hubs-namespace"></a>Event Hubs-Namespace
Ein Event Hubs-Namespace kann jetzt Schemagruppen neben Event Hubs (oder Kafka-Themen) hosten. Er hostet eine Schemaregistrierung und kann über mehrere Schemagruppen verfügen. Obwohl die Schemaregistrierung nicht in Azure Event Hubs gehostet wird, kann sie universell mit allen Azure-Messagingdiensten und beliebigen anderen Nachrichten- oder Ereignisbrokern verwendet werden. Jede dieser Schemagruppen ist ein separates sicherbares Repository für einen Satz von Schemas. Gruppen können mit einer bestimmten Anwendung oder einer Organisationseinheit ausgerichtet werden. 

## <a name="schema-groups"></a>Schemagruppen
Eine Schemagruppe ist eine logische Gruppe ähnlicher Schemas, die auf Ihren Geschäftskriterien basiert. Eine Schemagruppe kann mehrere Versionen eines Schemas enthalten. Mithilfe der Einstellung für die Kompatibilitätserzwingung einer Schemagruppe kann sichergestellt werden, dass neuere Schemaversionen abwärtskompatibel sind.

Durch die Sicherheitsgrenze, die durch den Gruppierungsmechanismus festgelegt wird, wird sichergestellt, dass Geschäftsgeheimnisse nicht versehentlich durch Metadaten in Situationen offengelegt werden, in denen der Namespace von mehreren Partnern gemeinsam genutzt wird. Außerdem wird es Anwendungsbesitzern ermöglicht, Schemas unabhängig von anderen Anwendungen zu verwalten, die denselben Namespace verwenden.


## <a name="schemas"></a>Schemas
Schemas definieren den Vertrag zwischen den Producern und den Consumern. Ein Schema, das in einer Event Hubs-Schemaregistrierung definiert ist, erleichtert die Verwaltung des Vertrags außerhalb der Ereignisdaten und entfernt so den Nutzlastmehraufwand. Ein Schema verfügt über einen Namen, einen Typ (Beispiel: Datensatz, Array usw.), einen Kompatibilitätsmodus („None“ (Keiner), „Forward“ (Aufwärts), „Backward“ (Abwärts), „Full“ (Vollständig)) und einen Serialisierungstyp (zurzeit nur Avro). Sie können mehrere Versionen eines Schemas erstellen und abrufen und eine bestimmte Version eines Schemas verwenden. 

## <a name="client-sdks"></a>Client-SDKs
Sie können eine der folgenden Bibliotheken verwenden, die ein Avro-Serialisierungsprogramm enthalten, das Sie verwenden können, um Nutzlasten zu serialisieren und zu deserialisieren, die Schemabezeichner der Schemaregistrierung und Avro-codierte Daten enthalten.

- [.NET: Microsoft.Azure.Data.SchemaRegistry.ApacheAvro](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro)
- [Java: azure-data-schemaregistry-avro](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/)
- [Python: azure-schemaregistry-avroserializer](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer)
- [JavaScript: @azure/schema-registry-avro](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro)
- [Apache Kafka](https://github.com/Azure/azure-schema-registry-for-kafka/): Führen Sie in Kafka integrierte Apache Avro-Serialisierungs- und Deserialisierungsmodule aus, die von der Azure-Schemaregistrierung unterstützt werden. Das Apache Kafka-Clientserialisierungsmodul des Java-Clients für die Azure-Schemaregistrierung kann in jedem Apache Kafka-Szenario und mit einer beliebigen Apache Kafka®-basierten Bereitstellung oder einem Clouddienst verwendet werden. 

Die folgende Abbildung zeigt den Informationsfluss der Schemaregistrierung mit Event Hubs: 

:::image type="content" source="./media/schema-registry-overview/flow-diagram.png" alt-text="Flussdiagramm":::

## <a name="limits"></a>Grenzwerte
Informationen zu Grenzwerten (z. B. zur Anzahl von Schemagruppen in einem Namespace) von Event Hubs finden Sie unter [Kontingente und Grenzwerte in Azure Event Hubs](event-hubs-quotas.md).

## <a name="azure-role-based-access-control"></a>Rollenbasierte Zugriffssteuerung in Azure
Wenn Sie programmgesteuert auf die Schemaregistrierung zugreifen, müssen Sie eine Anwendung in Azure Active Directory (Azure AD) registrieren und den Sicherheitsprinzipal der Anwendung einer der Rollen der rollenbasierten Zugriffssteuerung in Azure (Role-Based Access Control, Azure RBAC) hinzufügen:

| Role | BESCHREIBUNG | 
| ---- | ----------- | 
| Besitzer | Lesen, Schreiben und Löschen von Schemaregistrierungsgruppen und Schemas. |
| Mitwirkender | Lesen, Schreiben und Löschen von Schemaregistrierungsgruppen und Schemas. |
| [Schemaregistrierungsleser (Vorschau)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Lesen und Auflisten von Schemaregistrierungsgruppen und Schemas. |
| [Mitwirkender der Schemaregistrierung (Vorschau)](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Lesen, Schreiben und Löschen von Schemaregistrierungsgruppen und Schemas. |

Anweisungen zum Erstellen und Registrierung einer Anwendung mit dem Azure-Portal finden Sie unter [Registrieren einer App bei Azure AD](../active-directory/develop/quickstart-register-app.md). Notieren Sie sich die Client-ID (Anwendungs-ID), die Mandanten-ID und den geheimen Schlüssel, der im Code verwendet wird. 

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Erstellen einer Schemaregistrierung mit dem Azure-Portal finden Sie unter [Erstellen einer Event Hubs-Schemaregistrierung mithilfe des Azure-Portals](create-schema-registry.md).
- Weitere Informationen finden Sie in den folgenden Beispielen zur **Schemaregistrierung der Avro-Clientbibliothek**.
    - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro/tests/Samples)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/schemaregistry/azure-data-schemaregistry-avro/src/samples)
    - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro/samples )
    - [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer/samples )
    - [Kafka Avro-Integration für die Azure-Schemaregistrierung](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/csharp/avro/samples)
