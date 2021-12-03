---
title: Was ist der Dienstconnector?
description: Informieren Sie sich über typische Anwendungsszenarien für die Verwendung des Dienstconnectors, und lernen Sie die wichtigsten Vorteile des Dienstconnectors kennen.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: overview
ms.date: 10/29/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 799385d85e83f42835eb6e70ffb0b5a2b868e1f9
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372641"
---
# <a name="what-is-service-connector"></a>Was ist der Dienstconnector?

Mithilfe des Dienstconnectors können Sie den Azure-Computedienst problemlos mit anderen Unterstützungsdiensten verbinden. Dieser Dienst konfiguriert die Netzwerkeinstellungen und Verbindungsinformationen (z. B. Generieren von Umgebungsvariablen) zwischen dem Computedienst und dem Zielunterstützungsdienst auf der Verwaltungsebene. Entwickler verwenden nur das bevorzugte SDK oder die Bibliothek, die die Verbindungsinformationen nutzt, um Vorgänge auf der Datenebene für den Zielunterstützungsdienst durchzuführen. 

Dieser Artikel bietet eine Übersicht über den Dienstconnector-Dienst.

## <a name="what-is-service-connector-used-for"></a>Wofür wird der Dienstconnector verwendet?

Jede Anwendung, die in Azure-Computediensten ausgeführt wird und einen Unterstützungsdienst erfordert, kann den Dienstconnector verwenden. Nachstehend sind einige Beispiele aufgeführt, in denen der Dienstconnector verwendet werden kann, um die Verbindungsfunktionalität zwischen Diensten zu vereinfachen.

* **WebApp + DB:** Verwenden Sie den Dienstconnector, um PostgreSQL, MySQL, SQL DB oder Cosmos DB mit Ihrem App Service zu verbinden.  
* **WebApp + Storage:** Verwenden Sie den Dienstconnector, um eine Verbindung mit Azure Storage-Konten herzustellen und Ihre bevorzugten Speicherprodukte problemlos in Ihrem App Service zu verwenden.
* **Spring Cloud + Datenbank:** Verwenden Sie den Dienstconnector, um PostgreSQL, MySQL, SQL DB oder Cosmos DB mit Ihrer Spring Cloud-Anwendung zu verbinden.
* **Spring Cloud + Apache Kafka:** Mit dem Dienstconnector können Sie Ihre Spring Cloud-Anwendung mit Apache Kafka in Confluent Cloud verbinden.

Weitere unterstützte Dienste und Anwendungsmuster finden Sie unter [Welche Dienste werden vom Dienstconnector unterstützt?](#what-services-are-supported-in-service-connector).

## <a name="what-are-the-benefits-using-service-connector"></a>Welche Vorteile bietet die Verwendung des Dienstconnectors?

**Herstellen einer Verbindung zum Zielunterstützungsdienst mit einem einzigen Befehl oder wenigen Klicks:**

Der Dienstconnector ist für hohe Benutzerfreundlichkeit konzipiert. Es werden drei erforderliche Parameter angefordert: die Zieldienstinstanz, der Authentifizierungstyp zwischen Computedienst und Zieldienst sowie der Typ des Anwendungsclients zum Erstellen einer Verbindung. Entwickler können die Azure Connection CLI oder die Funktionalität des Azure-Portals verwenden, um ohne großen Aufwand Verbindungen zu erstellen.

**Verwenden des Verbindungsstatus, um Verbindungen zu überwachen oder Verbindungsprobleme zu ermitteln:**

Nachdem eine Dienstverbindung erstellt wurde. Entwickler können den Integritätsstatus der Verbindung validieren und überprüfen. Der Dienstconnector kann Aktionen vorschlagen, um unterbrochene Verbindungen zu reparieren.

## <a name="what-services-are-supported-in-service-connector"></a>Welche Dienste werden vom Dienstconnector unterstützt?

> [!NOTE]
> Der Dienstconnector befindet sich in der öffentlichen Vorschau. Das Produktteam fügt laufend weitere unterstützte Diensttypen in die Liste ein.

**Computedienst:**

* Azure App Service
* Azure Spring Cloud

**Zieldienst:**

* Azure Database for PostgreSQL
* Azure Database for MySQL
* Azure Cosmos DB (SQL, MangoDB, Gremlin, Cassandra, Tabelle)
* Azure Storage (Blob-, Warteschlangen-, Datei- und Tabellenspeicher)
* Azure-Schlüsseltresor
* Azure SignalR Service
* Apache Kafka in Confluent Cloud

## <a name="how-to-use-service-connector"></a>Wie wird der Dienstconnector verwendet?

Es gibt zwei Hauptmöglichkeiten, um den Dienstconnector für Azure-Anwendungen zu verwenden:

* **Azure Connection CLI:** Erstellen, Auflisten, Überprüfen und Löschen von Dienst-zu-Dienst-Verbindungen mit der Befehlsgruppe für Verbindungen in Azure CLI.
* **Dienstconnector-Benutzeroberfläche im Azure-Portal:** Verwenden der geführten Portalfunktionalität, um Dienst-zu-Dienst-Verbindungen zu erstellen und Verbindungen mit einer Hierarchieliste zu verwalten.

## <a name="next-steps"></a>Nächste Schritte

Arbeiten Sie die unten stehenden Tutorials durch, um mit dem Erstellen Ihrer eigenen Anwendung mit dem Dienstconnector zu beginnen.

> [!div class="nextstepaction"]
> [Schnellstart: Dienstconnector in App Service unter Verwendung der Azure CLI](./quickstart-cli-app-service-connection.md)

> [!div class="nextstepaction"]
> [Schnellstart: Dienstconnector in App Service unter Verwendung des Azure-Portals](./quickstart-portal-app-service-connection.md)

> [!div class="nextstepaction"]
> [Schnellstart: Dienstconnector in Spring Cloud unter Verwendung der Azure CLI](./quickstart-cli-spring-cloud-connection.md)

> [!div class="nextstepaction"]
> [Schnellstart: Dienstconnector in Spring Cloud unter Verwendung des Azure-Portals](./quickstart-portal-spring-cloud-connection.md)

> [!div class="nextstepaction"]
> [Informationen zu Service Connector-Konzepten](./concept-service-connector-internals.md)
