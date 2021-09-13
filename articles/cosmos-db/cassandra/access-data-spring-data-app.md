---
title: Verwenden der Spring Data-Apache Cassandra-API mit Azure Cosmos DB
description: Erfahren Sie, wie Sie die Spring Data-Apache Cassandra-API mit Azure Cosmos DB verwenden.
ms.service: cosmos-db
author: TheovanKraay
ms.author: thvankra
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: how-to
ms.date: 07/17/2021
ms.openlocfilehash: 92f9045891aa56589e13d887dfea6c3157292185
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122347194"
---
# <a name="how-to-use-spring-data-apache-cassandra-api-with-azure-cosmos-db"></a>Verwenden der Spring Data-Apache Cassandra-API mit Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

In diesem Artikel wird die Erstellung einer Beispielanwendung veranschaulicht, die [Spring Data] verwendet, um Informationen mithilfe der [Cassandra-API für Azure Cosmos DB](/azure/cosmos-db/cassandra-introduction) zu speichern und abzurufen.

## <a name="prerequisites"></a>Voraussetzungen

Für die Durchführung der Schritte in diesem Artikel müssen folgende Voraussetzungen erfüllt sein:

* Ein Azure-Abonnement – wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile] anwenden oder sich für ein [Kostenloses Azure-Konto] registrieren
* Ein unterstütztes Java Development Kit (JDK). Weitere Informationen zu den JDKs, die für die Entwicklung in Azure verfügbar sind, finden Sie im Artikel zur [Java-Unterstützung in Azure und Azure Stack](/azure/developer/java/fundamentals/java-support-on-azure).
* [Apache Maven](http://maven.apache.org/), Version 3.0 oder höher
* [cURL](https://curl.haxx.se/) oder ein ähnliches HTTP-Hilfsprogramm zum Testen der Funktionalität
* Einen [Git-Client](https://git-scm.com/downloads)

> [!NOTE]
> In den unten genannten Beispiele werden benutzerdefinierte Erweiterungen implementiert, um die Nutzung der Azure Cosmos DB-Cassandra-API zu verbessern. Dazu gehören benutzerdefinierte Wiederholungs- und Lastenausgleichsrichtlinien sowie die Implementierung empfohlener Verbindungseinstellungen. Eine ausführlichere Untersuchung der Verwendung der benutzerdefinierten Richtlinien finden Sie unter den Java-Beispielen für [Version 3](https://github.com/Azure-Samples/azure-cosmos-cassandra-extensions-java-sample) und [Version 4](https://github.com/Azure-Samples/azure-cosmos-cassandra-extensions-java-sample-v4). 

## <a name="create-a-cosmos-db-cassandra-api-account"></a>Erstellen eines Cosmos DB-Cassandra-API-Kontos

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="configure-the-sample-application"></a>Konfigurieren der Beispielanwendung

Mit dem folgenden Verfahren wird die Testanwendung konfiguriert:

1. Öffnen Sie eine Befehlsshell, und klonen Sie eines der folgenden Beispiele:

   Für den [Java-Treiber (Version 3)](https://github.com/datastax/java-driver/tree/3.x) und die entsprechende Spring-Version:

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-cassandra-on-azure-extension-v3.git
   ```
   
   Für den [Java-Treiber (Version 4)](https://github.com/datastax/java-driver/tree/4.x) und die entsprechende Spring-Version:

   ```shell
   git clone https://github.com/Azure-Samples/spring-data-cassandra-on-azure-extension-v4.git
   ```     

    > [!NOTE]    
    > Auch wenn die unten beschriebene Verwendung für die beiden Java-Beispiele der Versionen 3 und 4 oben identisch ist, unterscheidet sich die Art, wie sie implementiert wurden, um benutzerdefinierte Wiederholungs- und Lastenausgleichsrichtlinien einzuschließen. Es wird empfohlen, den Code zu überprüfen, um zu verstehen, wie benutzerdefinierte Richtlinien implementiert werden, bevor Sie Änderungen an einer vorhandenen Spring Java-Anwendung vornehmen.  

1. Suchen Sie im Verzeichnis *resources* des Beispielprojekts nach der Datei *application.properties*, oder erstellen Sie diese Datei, wenn sie noch nicht vorhanden ist.

1. Öffnen Sie die Datei *application.properties* in einem Text-Editor, und fügen Sie die folgenden Zeilen in der Datei hinzu, bzw. konfigurieren Sie diese Zeilen. Ersetzen Sie dabei die Beispielwerte durch die entsprechenden Werte, die Sie zuvor festgelegt haben:

   ```yaml
   spring.data.cassandra.contact-points=<Account Name>.cassandra.cosmos.azure.com
   spring.data.cassandra.port=10350
   spring.data.cassandra.username=<Account Name>
   spring.data.cassandra.password=********
   ```

   Hierbei gilt:

   | Parameter | BESCHREIBUNG |
   |---|---|
   | `spring.data.cassandra.contact-points` | Der **Kontaktpunkt**, den Sie weiter oben in diesem Artikel kopiert haben |
   | `spring.data.cassandra.port` | Der **Port**, den Sie weiter oben in diesem Artikel kopiert haben |
   | `spring.data.cassandra.username` | Der **Benutzername**, den Sie weiter oben in diesem Artikel kopiert haben |
   | `spring.data.cassandra.password` | Das **primäre Kennwort**, das Sie weiter oben in diesem Artikel kopiert haben |

1. Speichern und schließen Sie die Datei *application.properties*.

## <a name="package-and-test-the-sample-application"></a>Verpacken und Testen der Beispielanwendung 

Navigieren Sie zum Verzeichnis mit der POM-Datei, um die Anwendung zu erstellen und zu testen.

1. Erstellen Sie die Beispielanwendung mit Maven. Beispiel:

   ```shell
   mvn clean package
   ```

1. Starten Sie die Beispielanwendung. Beispiel:

   ```shell
   java -jar target/spring-data-cassandra-on-azure-0.1.0-SNAPSHOT.jar
   ```

1. Erstellen Sie wie in den folgenden Beispielen dargestellt über eine Eingabeaufforderung mit `curl` neue Datensätze:

   ```shell
   curl -s -d "{\"name\":\"dog\",\"species\":\"canine\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets

   curl -s -d "{\"name\":\"cat\",\"species\":\"feline\"}" -H "Content-Type: application/json" -X POST http://localhost:8080/pets
   ```

   Ihre Anwendung sollte Werte zurückgeben, die dem folgenden Beispiel ähneln:

   ```shell
   Added Pet{id=60fa8cb0-0423-11e9-9a70-39311962166b, name='dog', species='canine'}.

   Added Pet{id=72c1c9e0-0423-11e9-9a70-39311962166b, name='cat', species='feline'}.
   ```

1. Rufen Sie wie im folgenden Beispiel dargestellt über eine Eingabeaufforderung mit `curl` alle vorhandenen Datensätze ab:

   ```shell
   curl -s http://localhost:8080/pets
   ```

   Ihre Anwendung sollte Werte zurückgeben, die dem folgenden Beispiel ähneln:

   ```json
   [{"id":"60fa8cb0-0423-11e9-9a70-39311962166b","name":"dog","species":"canine"},{"id":"72c1c9e0-0423-11e9-9a70-39311962166b","name":"cat","species":"feline"}]
   ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Spring und Azure finden Sie im Dokumentationscenter zu Spring in Azure.

> [!div class="nextstepaction"]
> [Spring Framework in Azure](../../index.yml)

### <a name="additional-resources"></a>Weitere Ressourcen

Weitere Informationen zur Verwendung von Azure mit Java finden Sie unter [Azure für Java-Entwickler] und [Working with Azure DevOps and Java] (Arbeiten mit Azure DevOps und Java).

<!-- URL List -->

[Azure für Java-Entwickler]: ../index.yml
[kostenloses Azure-Konto]: https://azure.microsoft.com/pricing/free-trial/
[Working with Azure DevOps and Java]: /azure/devops/ (Arbeiten mit Azure DevOps und Java)
[MSDN-Abonnentenvorteile]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/
[Spring Boot]: http://projects.spring.io/spring-boot/
[Spring Data]: https://spring.io/projects/spring-data
[Spring Initializr]: https://start.spring.io/
[Spring Framework]: https://spring.io/

<!-- IMG List -->

[COSMOSDB01]: media/access-data-spring-data-app/create-cosmos-db-01.png
[COSMOSDB02]: media/access-data-spring-data-app/create-cosmos-db-02.png
[COSMOSDB03]: media/access-data-spring-data-app/create-cosmos-db-03.png
[COSMOSDB04]: media/access-data-spring-data-app/create-cosmos-db-04.png
[COSMOSDB05]: media/access-data-spring-data-app/create-cosmos-db-05.png
[COSMOSDB05-1]: media/access-data-spring-data-app/create-cosmos-db-05-1.png
[COSMOSDB06]: media/access-data-spring-data-app/create-cosmos-db-06.png