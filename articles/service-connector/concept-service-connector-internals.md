---
title: Besonderheiten des Dienstconnectors
description: Erfahren Sie mehr über die Besonderheiten, die Architektur, die Verbindungen und die Datenübertragung des Dienstconnectors.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fda89409a39644057ebd76d949665b281d32dc2a
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131501664"
---
# <a name="service-connector-internals"></a>Besonderheiten des Dienstconnectors

Der Dienstconnector ist ein Erweiterungsressourcenanbieter zur mühelosen Erstellung und Verwaltung von Verbindungen zwischen Diensten.
- Unterstützen Sie gängige Datenbanken, Speicher, Echtzeitdienste sowie Zustands- und Geheimnisspeicher, die zusammen mit Ihrer nativen Cloudanwendung verwendet werden. (Die Liste wird aktiv erweitert.)
- Erstellen Sie eine Dienstverbindung mit nur einem Befehl oder wenigen Klicks, um Netzwerkeinstellungen und die Authentifizierung zu konfigurieren und Umgebungsvariablen oder Eigenschaften der Verbindung zu verwalten.
- Überprüfen Sie Verbindungen, und nutzen Sie entsprechende Vorschläge, um eine Dienstverbindung zu reparieren. 

## <a name="service-connection-overview"></a>Übersicht über Dienstverbindungen

Die Dienstverbindung ist das zentrale Konzept im Ressourcenmodell des Dienstconnectors. Im Kontext des Dienstconnectors ist eine Dienstverbindung eine Abstraktion der Verknüpfung zwischen zwei Diensten. Für eine Dienstverbindung werden folgende Eigenschaften definiert:

| Eigenschaft | BESCHREIBUNG |
|--------|-----------|
| Verbindungsname | Der eindeutige Name der Dienstverbindung.  |
| Art des Quelldiensts | Quelldienste sind in der Regel Azure-Computedienste. Dienstconnectorfunktionen finden Sie in unterstützten Computediensten, indem Sie die entsprechenden Azure-Computedienstanbieter erweitern.  |
| Art des Zieldiensts | Zieldienste sind unterstützende Dienste oder Abhängigkeitsdienste, mit denen Ihre Computedienste eine Verbindung herstellen. Der Dienstconnector unterstützt verschiedene Arten von Zieldiensten. Hierzu zählen beispielsweise gängige Datenbanken, Speicher, Echtzeitdienste sowie Zustands- und Geheimnisspeicher. |
| Clienttyp | Der Clienttyp bezieht sich auf Ihren Computelaufzeitstapel, das Entwicklungsframework oder den spezifischen Clientbibliothekstyp, der bzw. das das spezifische Format der Umgebungsvariablen oder Eigenschaften der Verbindung akzeptiert. |
| Authentifizierungstyp | Der für die Dienstverbindung verwendete Authentifizierungstyp. Dabei kann es sich um eine reine Geheimnis-/Verbindungszeichenfolge, um eine verwaltete Identität oder um einen Dienstprinzipal handeln. |

Sie können mehrere Dienstverbindungen von einer einzelnen Quelldienstinstanz aus erstellen, wenn Ihre Instanz mehrere Zielressourcen verbinden soll. Außerdem kann von mehreren Quellinstanzen aus eine Verbindung mit der gleichen Zielressource hergestellt werden. Der Dienstconnector verwaltet alle Verbindungen in den Eigenschaften der Quellinstanz. Das bedeutet, dass Sie die Verbindungen im Portal oder CLI-Befehl der zugehörigen Quelldienstinstanz erstellen, abrufen, aktualisieren und löschen können. 

Die Verbindung wird abonnement- oder mandantenübergreifend unterstützt. Quell- und Zieldienst können zu unterschiedlichen Abonnements oder Mandanten gehören. Wenn Sie eine neue Dienstverbindung erstellen, befindet sich die Verbindungsressource standardmäßig in der gleichen Region wie Ihre Computedienstinstanz.

## <a name="create-or-update-a-service-connection"></a>Erstellen oder Aktualisieren einer Dienstverbindung

Beim Erstellen oder Aktualisieren einer Verbindung werden vom Dienstconnector mehrere Schritte ausgeführt:

- Konfigurieren der Netzwerk- und Firewalleinstellungen der Zielressource, um sicherzustellen, dass Quell- und Zieldienste auf der Netzwerkebene miteinander kommunizieren können
- Konfigurieren von Verbindungsinformationen für die Quellressource
- Konfigurieren von Authentifizierungsinformationen für Quelle und Ziel, sofern erforderlich
- Erstellen oder Aktualisieren der Verbindung (mit Rollbackunterstützung im Falle eines Fehlers)

Das Erstellen und Aktualisieren einer Verbindung umfasst mehrere Schritte. Sollte bei einem der Schritte ein Fehler auftreten, führt der Dienstconnector ein Rollback für alle vorherigen Schritte aus, um die ursprünglichen Einstellungen in Quell- und Zielinstanzen beizubehalten.

## <a name="connection-configurations"></a>Verbindungskonfigurationen

Nachdem eine Dienstverbindung erstellt wurde, wird die Verbindungskonfiguration auf den Quelldienst festgelegt.
Navigieren Sie im Portal zur Seite **Dienstconnector (Vorschau)** . Sie können jede Verbindung erweitern und die Verbindungskonfigurationen anzeigen.

:::image type="content" source="media/tutorial-java-spring-confluent-kafka/portal-list-connection-config.png" alt-text="Liste: Portalkonfiguration":::

In der CLI können Sie die Verbindungskonfiguration mithilfe des Befehls `list-configuration` anzeigen.

```azurecli
az webapp connection list-configuration -g {webapp_rg} -n {webapp_name} --connection {service_connection_name}
```

```azurecli
az spring-cloud connection list-configuration -g {spring_cloud_rg} -n {spring_cloud_name} --connection {service_connection_name}
```

## <a name="configuration-naming-convention"></a>Namenskonvention für Konfigurationen

Der Dienstconnector legt die Konfiguration (Umgebungsvariablen oder Spring Boot-Konfigurationen) beim Erstellen einer Verbindung fest. Die Schlüssel-Wert-Paare der Umgebungsvariablen werden durch Ihren Client- und Authentifizierungstyp bestimmt. Wenn Sie also beispielsweise das Azure SDK mit verwalteter Identität verwenden, sind Client-ID, geheimer Clientschlüssel usw. erforderlich. Bei Verwendung des JDBC-Treibers wird die Datenbankverbindungszeichenfolge benötigt. Für die Konfiguration gelten folgende Benennungsregeln:

Bei Verwendung von **Spring Boot** als Clienttyp:

* Für die Spring Boot-Bibliothek der einzelnen Zieldienste gilt jeweils eine eigene Namenskonvention. Die MySQL-Verbindungseinstellungen lauten beispielsweise `spring.datasource.url`, `spring.datasource.username` und `spring.datasource.password`. Für Kafka werden folgende Verbindungseinstellungen verwendet: `spring.kafka.properties.bootstrap.servers`.

Bei Verwendung **anderer Clienttypen** (nicht Spring Boot):

* Bei einer Verbindung mit einem Zieldienst hat der Schlüsselname der ersten Verbindungskonfiguration das Format `{Cloud}_{Type}_{Name}`. Beispiele: `AZURE_STORAGEBLOB_RESOURCEENDPOINT`, `CONFLUENTCLOUD_KAFKA_BOOTSTRAPSERVER`. 
* Der Schlüsselname der zweiten Verbindungskonfiguration für die gleiche Art von Zielressource hat das Format `{Cloud}_{Type}_{Connection Name}_{Name}`. Beispiele: `AZURE_STORAGEBLOB_CONN2_RESOURCEENDPOINT`, `CONFLUENTCLOUD_KAFKA_CONN2_BOOTSTRAPSERVER`.

## <a name="validate-a-service-connection"></a>Überprüfen einer Dienstverbindung
Bei der Verbindungsüberprüfung werden folgende Punkte geprüft:

* Die Existenz der Quell- und Zielressourcen
* Die Netzwerk- und Firewalleinstellungen der Zielressource
* Die Verbindungsinformationen für die Quellressource
* Die Authentifizierungsinformationen für Quelle und Ziel, sofern erforderlich

## <a name="delete-connection"></a>Löschen einer Verbindung

Beim Löschen der Verbindung werden die Verbindungsinformationen für die Quellressource gelöscht. 
