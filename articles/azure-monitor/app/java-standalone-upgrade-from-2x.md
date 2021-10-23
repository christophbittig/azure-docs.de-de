---
title: Upgrade des Application Insights Java 2.x SDK für Azure Monitor
description: Upgrade des Application Insights Java 2.x SDK für Azure Monitor
ms.topic: conceptual
ms.date: 11/25/2020
ms.custom: devx-track-java
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 4de1f6788b5df94bc2c35516983cf67748e66ee0
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131199"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>Upgrade des Application Insights Java 2.x SDK

Wenn Sie bereits das Application Insights Java 2.x SDK in Ihrer Anwendung nutzen, können Sie es weiterhin verwenden.
Der Application Insights Java 3.x-Agent erkennt es und erfasst sowie korreliert alle benutzerdefinierten Telemetriedaten, die über das 2.x SDK gesendet werden, während gleichzeitig alle vom 2.x SDK durchgeführten automatischen Sammlungen unterdrückt werden, um doppelte Telemetriedaten zu verhindern.

Wenn Sie den Application Insights 2.x-Agent verwendet haben, müssen Sie das JVM-Argument `-javaagent:` entfernen, das auf den 2.x-Agent verweist.

Im restlichen Dokument werden die Einschränkungen und Änderungen beschrieben, die beim Upgrade von 2.x auf 3.x auftreten können, sowie einige Problemumgehungen, die für Sie nützlich sein können.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers und TelemetryProcessors

TelemetryInitializers und TelemetryProcessors des 2.x SDK werden bei Verwendung des 3.x-Agents nicht ausgeführt.
Viele der Anwendungsfälle, in denen diese Eigenschaften zuvor erforderlich waren, können in Application Insights Java 3.x gelöst werden, indem [benutzerdefinierte Dimensionen](./java-standalone-config.md#custom-dimensions) oder [Telemetrieprozessoren](./java-standalone-telemetry-processors.md) konfiguriert werden.

## <a name="multiple-applications-in-a-single-jvm"></a>Mehrere Anwendungen in einer einzelnen JVM

Derzeit unterstützt Application Insights Java 3.x pro laufendem Prozess nur eine einzige [Verbindungszeichenfolge und einen einzigen Rollennamen](./java-standalone-config.md#connection-string-and-role-name). Insbesondere sind in derselben Tomcat-Bereitstellung noch nicht mehrere Tomcat-Web-Apps mit unterschiedlichen Verbindungszeichenfolgen oder Rollennamen möglich.

## <a name="operation-names"></a>Vorgangsnamen

Im Application Insights Java 2.x SDK enthielten die Vorgangsnamen in einigen Fällen den vollständigen Pfad.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="Screenshot: Vorgangsnamen mit vollständigem Pfad":::

Vorgangsnamen in Application Insights Java 3.x haben sich geändert, um allgemein eine bessere aggregierte Ansicht auf der Benutzeroberfläche des Application Insights-Portals zu bieten.

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-parameterized.png" alt-text="Screenshot mit parametrisierten Vorgangsnamen":::

Bei einigen Anwendungen bevorzugen Sie jedoch möglicherweise immer noch die aggregierte Ansicht auf der Benutzeroberfläche, die durch die vorherigen Vorgangsnamen bereitgestellt wurde. In diesem Fall können Sie die Funktion [Telemetrieprozessoren](./java-standalone-telemetry-processors.md) (Vorschau) in Version 3.x verwenden, um das vorherige Verhalten zu replizieren.

Mit dem folgenden Codeausschnitt werden drei Telemetrieprozessoren konfiguriert, die zum Replizieren des vorherigen Verhaltens kombiniert werden.
Die Telemetrieprozessoren führen die folgenden Aktionen aus (in der angegebenen Reihenfolge):

1. Der erste Telemetrieprozessor ist ein Attributprozessor (Typ `attribute`). Das bedeutet, dass er für alle Telemetrien zutrifft, die über Attribute verfügen (derzeit `requests` und `dependencies`, bald aber auch `traces`).

   Er sucht nach Telemetrien, die über Attribute mit dem Namen `http.method` und `http.url` verfügen.

   Anschließend wird der Pfadteil des Attributs `http.url` in ein neues Attribut mit dem Namen `tempName` extrahiert.

2. Der zweite Telemetrieprozessor ist ein Span-Prozessor (Typ `span`). Das bedeutet, dass er für `requests` und `dependencies` zutrifft.

   Er sucht nach Spans, die über ein Attribut mit dem Namen `tempPath` verfügen.

   Anschließend wird der Span-Name aus dem Attribut `tempPath` aktualisiert.

3. Der letzte Telemetrieprozessor ist ein Attributprozessor, der dem ersten Telemetrieprozessor entspricht.

   Er sucht nach Telemetrien, die über ein Attribut mit dem Namen `tempPath` verfügen.

   Anschließend wird das Attribut mit dem Namen `tempPath`gelöscht, damit es nicht als benutzerdefinierte Dimension gemeldet wird.

```
{
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "actions": [
          {
            "key": "http.url",
            "pattern": "https?://[^/]+(?<tempPath>/[^?]*)",
            "action": "extract"
          }
        ]
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempPath" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```

## <a name="2x-sdk-logging-appenders"></a>2.x SDK-Protokollierungs-Appender

Application Insights Java 3.x [erfasst die Protokollierung automatisch](./java-standalone-config.md#auto-collected-logging), ohne dass Protokollierungs-Appender konfiguriert werden müssen.
Wenn Sie 2.x SDK-Protokollierungs-Appender verwenden, können Sie diese entfernen, da sie von Application Insights Java 3.x ohnehin unterdrückt werden.

## <a name="2x-sdk-spring-boot-starter"></a>2.x SDK Spring Boot Starter

Es gibt keinen Application Insights Java 3.x Spring Boot Starter.
Bei der Einrichtung und Konfiguration von 3.x gelten dieselben [einfachen Schritte](./java-in-process-agent.md#get-started), unabhängig davon, ob Sie Spring Boot verwenden.

Beachten Sie beim Upgrade vom Application Insights Java 2.x SDK Spring Boot Starter, dass der Name der Cloudrolle nicht mehr standardmäßig `spring.application.name` lautet.
Informationen zum Festlegen des Cloudrollennamens in 3.x über die JSON-Konfiguration oder die Umgebungsvariable finden Sie in der [Dokumentation zur 3.x-Konfiguration](./java-standalone-config.md#cloud-role-name).
