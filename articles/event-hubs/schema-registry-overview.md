---
title: Azure-Schemaregistrierung in Azure Event Hubs
description: In diesem Artikel finden Sie eine Übersicht über die Unterstützung der Schemaregistrierung durch Azure Event Hubs.
ms.topic: overview
ms.date: 11/02/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: f9e405a82b73530cc53ec98f22b2c1f4473b2550
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398481"
---
# <a name="azure-schema-registry-in-azure-event-hubs"></a>Azure-Schemaregistrierung in Azure Event Hubs
In vielen Ereignisstreaming- und Messagingszenarien enthalten die Ereignis- oder Nachrichtennutzdaten strukturierte Daten. Schemagesteuerte Formate wie [Apache Avro](https://avro.apache.org/) werden häufig verwendet, um solche strukturierten Daten zu serialisieren oder zu deserialisieren. 

:::image type="content" source="./media/schema-registry-overview/schema-driven-ser-de.svg" alt-text="Schemagesteuerte Serialisierung/Deserialisierung":::

Die Produceranwendungen verwenden das Schemadokument, um die Ereignisnutzdaten zu serialisieren und in einem Ereignisbroker wie Event Hubs zu veröffentlichen. Ebenso lesen die Consumeranwendungen die Ereignisnutzdaten aus dem Broker und deserialisieren sie mit demselben Schemadokument. Daher können sowohl die Producer als auch die Consumer die Integrität der Daten mit einem Schemadokument überprüfen. 


## <a name="why-we-need-a-schema-registry"></a>Wofür wird eine Schemaregistrierung benötigt? 
Wenn Sie schemagesteuerte Formate verwenden, müssen die Produceranwendungen die Schemas der veröffentlichten Ereignisse für die Consumer bereitstellen. Es ist möglich, das entsprechende Schema für alle Ereignisdaten gemeinsam zu verwenden, dies ist jedoch ineffizient. Wenn die neuen Consumer Ereignisdaten nutzen sollen, müssen sie über eine Möglichkeit verfügen, das Format der veröffentlichten Daten zu verstehen. Sie müssen auch sicherstellen, dass Sie die Schemaentwicklung unterstützen, damit sich sowohl die Producer als auch die Consumer mit unterschiedlicher Geschwindigkeit weiterentwickeln können. 


## <a name="azure-schema-registry"></a>Azure-Schemaregistrierung
Die **Azure-Schemaregistrierungs** ist eine Funktion von Event Hubs, die ein zentrales Repository für Schemadokumente für ereignisgesteuerte und nachrichtenzentrierte Anwendungen bereitstellt. Sie bietet Ihren Producer- und Consumeranwendungen die Flexibilität, Daten auszutauschen, ohne das Schema verwalten und gemeinsam nutzen zu müssen. Die Schemaregistrierung stellt außerdem ein einfaches Governanceframework für wiederverwendbare Schemas bereit und definiert die Beziehung zwischen Schemas über ein Gruppierungskonstrukt (Schemagruppen).

:::image type="content" source="./media/schema-registry-overview/schema-registry.svg" alt-text="Schemaregistrierung":::

Bei schemagesteuerten Serialisierungsframeworks wie Apache Avro kann die Externalisierung von Serialisierungsmetadaten in gemeinsam genutzte Schemas auch dazu beitragen, den Mehraufwand für Typinformationen und Feldnamen pro Nachricht, die in jedem Datensatz enthalten sind, drastisch zu reduzieren, der bei getaggten Formaten wie JSON anfällt. Durch die Speicherung von Schemas zusammen mit den Ereignissen und innerhalb der Ereignisinfrastruktur wird sichergestellt, dass die für die Serialisierung/Deserialisierung erforderlichen Metadaten immer erreichbar sind und Schemas nicht falsch platziert werden können. 

> [!NOTE]
> Das Feature ist im **Basic**-Tarif nicht verfügbar.

## <a name="schema-registry-information-flow"></a>Informationsfluss bei der Schemaregistrierung 
Der Informationsfluss bei Verwendung der Schemaregistrierung ist für alle Protokolle identisch, die Sie zum Veröffentlichen oder Nutzen von Ereignissen von Azure Event Hubs verwenden. Das folgende Diagramm zeigt den Informationsfluss in einem Producer- und Consumerszenario für Kafka-Ereignisse, bei dem die Schemaregistrierung verwendet wird. 

:::image type="content" source="./media/schema-registry-overview/information-flow.svg" lightbox="./media/schema-registry-overview/information-flow.svg" alt-text="Abbildung des Informationsflusses bei der Schemaregistrierung":::


Der Informationsfluss beginnt auf der Producerseite, wo Kafka-Producer die Daten mithilfe des Schemadokuments serialisieren. 
- Die Kafka-Produceranwendung verwendet ``KafkaAvroSerializer``, um Ereignisdaten mithilfe des auf der Clientseite angegebenen Schemas zu serialisieren. 
- Die Produceranwendung muss Details zum Schemaregistrierungsendpunkt und andere optionale Parameter bereitstellen, die für die Schemavalidierung erforderlich sind. 
- Das Serialisierungsmodul sucht in der Schemaregistrierung anhand des Schemainhalts, der vom Producer zum Serialisieren der Ereignisdaten verwendet wird. 
- Wenn ein solches Schema gefunden wurde, wird die entsprechende Schema-ID zurückgegeben. Sie können in der Produceranwendung festlegen, dass die Produceranwendung bei einem fehlenden Schema den Schemaregistrierungsclient so konfiguriert, dass das Schema automatisch registriert wird. 
- Anschließend verwendet das Serialisierungsmodul diese Schema-ID und stellt sie den serialisierten Daten voran, die in Event Hubs veröffentlicht werden. 
- Auf Consumerseite verwendet ``KafkaAvroDeserializer`` die Schema-ID, um den Schemainhalt aus der Schemaregistrierung abzurufen. 
- Das Deserialisierungsmodul verwendet dann den Schemainhalt, um Ereignisdaten zu deserialisieren, die aus dem Event Hub gelesen werden. 
- Schemaregistrierungsclients nutzen die Zwischenspeicherung, um redundante Suchvorgänge in der Schemaregistrierung zu vermeiden.  


## <a name="schema-registry-elements"></a>Elemente der Schemaregistrierung

Ein Event Hubs-Namespace kann jetzt Schemagruppen neben Event Hubs (oder Kafka-Themen) hosten. Er hostet eine Schemaregistrierung und kann über mehrere Schemagruppen verfügen. Obwohl die Schemaregistrierung nicht in Azure Event Hubs gehostet wird, kann sie universell mit allen Azure-Messagingdiensten und beliebigen anderen Nachrichten- oder Ereignisbrokern verwendet werden. Jede dieser Schemagruppen ist ein separates sicherbares Repository für einen Satz von Schemas. Gruppen können mit einer bestimmten Anwendung oder einer Organisationseinheit ausgerichtet werden. 

:::image type="content" source="./media/schema-registry-overview/elements.png" alt-text="Abbildung der Elemente einer Schemaregistrierung":::


### <a name="schema-groups"></a>Schemagruppen
Eine Schemagruppe ist eine logische Gruppe ähnlicher Schemas, die auf Ihren Geschäftskriterien basiert. Eine Schemagruppe kann mehrere Versionen eines Schemas enthalten. Mithilfe der Einstellung für die Kompatibilitätserzwingung einer Schemagruppe kann sichergestellt werden, dass neuere Schemaversionen abwärtskompatibel sind.

Durch die Sicherheitsgrenze, die durch den Gruppierungsmechanismus festgelegt wird, wird sichergestellt, dass Geschäftsgeheimnisse nicht versehentlich durch Metadaten in Situationen offengelegt werden, in denen der Namespace von mehreren Partnern gemeinsam genutzt wird. Außerdem wird es Anwendungsbesitzern ermöglicht, Schemas unabhängig von anderen Anwendungen zu verwalten, die denselben Namespace verwenden.

### <a name="schemas"></a>Schemas
Schemas definieren den Vertrag zwischen den Producern und den Consumern. Ein Schema, das in einer Event Hubs-Schemaregistrierung definiert ist, erleichtert die Verwaltung des Vertrags außerhalb der Ereignisdaten und entfernt so den Nutzlastmehraufwand. Ein Schema verfügt über einen Namen, einen Typ (Beispiel: Datensatz, Array usw.), einen Kompatibilitätsmodus („None“ (Keiner), „Forward“ (Aufwärts), „Backward“ (Abwärts), „Full“ (Vollständig)) und einen Serialisierungstyp (zurzeit nur Avro). Sie können mehrere Versionen eines Schemas erstellen und abrufen und eine bestimmte Version eines Schemas verwenden. 

## <a name="schema-evolution"></a>Schemaentwicklung 

Schemas müssen sich mit den geschäftlichen Anforderungen von Producern und Consumern weiterentwickeln. Die Azure-Schemaregistrierung unterstützt die Schemaentwicklung, indem Kompatibilitätsmodi auf Schemagruppenebene eingeführt werden. Wenn Sie eine Schemagruppe erstellen, können Sie den Kompatibilitätsmodus der Schemas angeben, die Sie in diese Schemagruppe einfügen. Wenn Sie ein Schema aktualisieren, sollte die Änderung dem zugewiesenen Kompatibilitätsmodus entsprechen, und nur dann sollte eine neue Version des Schemas erstellt werden. 

Die Azure-Schemaregistrierung für Event Hubs unterstützt die folgenden Kompatibilitätsmodi. 

### <a name="backward-compatibility"></a>Abwärtskompatibilität
Der Abwärtskompatibilitätsmodus ermöglicht die Verwendung einer neuen Version des Schemas im Consumercode. Es können aber auch Nachrichten mit einer alten Version des Schemas verarbeitet werden. Wenn Sie den Abwärtskompatibilitätsmodus in einer Schemagruppe verwenden, können die folgenden Änderungen an einem Schema vorgenommen werden. 

- Löschen von Feldern 
- Hinzufügen optionaler Felder 


### <a name="forward-compatibility"></a>Vorwärtskompatibilität
Die Vorwärtskompatibilität ermöglicht es, eine alte Version des Schemas im Consumercode zu verwenden. Es können jedoch auch Nachrichten mit dem neuen Schema gelesen werden. Der Vorwärtskompatibilitätsmodus ermöglicht die folgenden Änderungen an einem Schema. 
- Hinzufügen von Feldern 
- Löschen optionaler Felder 


### <a name="no-compatibility"></a>Keine Kompatibilität
Wenn der Kompatibilitätsmodus ``None`` verwendet wird, werden von der Schemaregistrierung keine Kompatibilitätsprüfungen durchgeführt, wenn Sie Schemas aktualisieren. 

## <a name="client-sdks"></a>Client-SDKs

Sie können eine der folgenden Bibliotheken verwenden, die ein Avro-Serialisierungsmodul enthalten, mit dem Sie Nutzdaten serialisieren und deserialisieren können, die Schemabezeichner der Schemaregistrierung und Avro-codierte Daten enthalten.

- [.NET: Microsoft.Azure.Data.SchemaRegistry.ApacheAvro](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro)
- [Java: azure-data-schemaregistry-avro](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/schemaregistry/azure-data-schemaregistry-apacheavro)
- [Python: azure-schemaregistry-avroserializer](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer)
- [JavaScript: @azure/schema-registry-avro](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro)
- [Apache Kafka](https://github.com/Azure/azure-schema-registry-for-kafka/): Führen Sie in Kafka integrierte Apache Avro-Serialisierungs- und Deserialisierungsmodule aus, die von der Azure-Schemaregistrierung unterstützt werden. Das Apache Kafka-Clientserialisierungsmodul des Java-Clients für die Azure-Schemaregistrierung kann in jedem Apache Kafka-Szenario und mit einer beliebigen Apache Kafka®-basierten Bereitstellung oder einem Clouddienst verwendet werden. 

## <a name="limits"></a>Grenzwerte
Informationen zu Grenzwerten (z. B. zur Anzahl von Schemagruppen in einem Namespace) von Event Hubs finden Sie unter [Kontingente und Grenzwerte in Azure Event Hubs](event-hubs-quotas.md).

## <a name="azure-role-based-access-control"></a>Rollenbasierte Zugriffssteuerung in Azure
Wenn Sie programmgesteuert auf die Schemaregistrierung zugreifen, müssen Sie eine Anwendung in Azure Active Directory (Azure AD) registrieren und den Sicherheitsprinzipal der Anwendung einer der Rollen der rollenbasierten Zugriffssteuerung in Azure (Role-Based Access Control, Azure RBAC) hinzufügen:

| Role | BESCHREIBUNG | 
| ---- | ----------- | 
| Besitzer | Lesen, Schreiben und Löschen von Schemaregistrierungsgruppen und Schemas. |
| Mitwirkender | Lesen, Schreiben und Löschen von Schemaregistrierungsgruppen und Schemas. |
| [Schemaregistrierungsleser](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Lesen und Auflisten von Schemaregistrierungsgruppen und Schemas. |
| [Mitwirkender der Schemaregistrierung](../role-based-access-control/built-in-roles.md#schema-registry-reader-preview) | Lesen, Schreiben und Löschen von Schemaregistrierungsgruppen und Schemas. |

Anweisungen zum Erstellen und Registrierung einer Anwendung mit dem Azure-Portal finden Sie unter [Registrieren einer App bei Azure AD](../active-directory/develop/quickstart-register-app.md). Notieren Sie sich die Client-ID (Anwendungs-ID), die Mandanten-ID und den geheimen Schlüssel, der im Code verwendet wird. 

## <a name="next-steps"></a>Nächste Schritte

- Informationen zum Erstellen einer Schemaregistrierung mit dem Azure-Portal finden Sie unter [Erstellen einer Event Hubs-Schemaregistrierung mithilfe des Azure-Portals](create-schema-registry.md).
- Weitere Informationen finden Sie in den folgenden Beispielen zur **Schemaregistrierung der Avro-Clientbibliothek**.
    - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/schemaregistry/Microsoft.Azure.Data.SchemaRegistry.ApacheAvro/tests/Samples)
    - [Java](https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/schemaregistry/azure-data-schemaregistry-apacheavro/src/samples)
    - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/schemaregistry/schema-registry-avro/samples )
    - [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/schemaregistry/azure-schemaregistry-avroserializer/samples )
    - [Kafka Avro-Integration für die Azure-Schemaregistrierung](https://github.com/Azure/azure-schema-registry-for-kafka/tree/master/csharp/avro/samples)
