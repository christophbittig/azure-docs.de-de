---
title: 'Tutorial: Bereitstellen einer mit Apache Kafka in Confluent Cloud verbundenen Spring Boot-App mithilfe eines Dienstconnectors in Azure Spring Cloud'
description: Erstellen Sie eine mit Apache Kafka in Confluent Cloud verbundenen Spring Boot-App mithilfe eines Dienstconnectors in Azure Spring Cloud.
ms.devlang: java
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: tutorial
ms.date: 10/28/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: a407d164ec0214ddce6e1d8bf6254876d3642230
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131449220"
---
# <a name="tutorial-deploy-a-spring-boot-app-connected-to-apache-kafka-on-confluent-cloud-with-service-connector-in-azure-spring-cloud"></a>Tutorial: Bereitstellen einer mit Apache Kafka in Confluent Cloud verbundenen Spring Boot-App mithilfe eines Dienstconnectors in Azure Spring Cloud

Hier erfahren Sie, wie Sie für eine Spring Boot-Anwendung, die auf Azure Spring Cloud ausgeführt wird, auf Apache Kafka in Confluent Cloud zugreifen. In diesem Tutorial führen Sie die folgenden Aufgaben durch:

> [!div class="checklist"]
> * Erstellen von Apache Kafka in Confluent Cloud
> * Erstellen einer Spring Cloud-Anwendung
> * Erstellen und Bereitstellen der Spring Boot-App
> * Verbinden von Apache Kafka in Confluent Cloud mit Azure Spring Cloud mithilfe eines Dienstconnectors

## <a name="1-set-up-your-initial-environment"></a>1. Einrichten der anfänglichen Umgebung

1. Sie benötigen ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
2. Installieren Sie Java 8 oder 11.
3. Installieren Sie die <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.18.0 oder höher, mit der Sie Befehle in einer beliebigen Shell ausführen, um Azure-Ressourcen bereitzustellen und zu konfigurieren.

## <a name="2-clone-or-download-the-sample-app"></a>2. Klonen oder Herunterladen der Beispiel-App

Klonen Sie das Beispielrepository:

```Bash
git clone https://github.com/Azure-Samples/serviceconnector-springcloud-confluent-springboot/
```

Navigieren Sie anschließend in diesen Ordner:

```Bash
cd serviceconnector-springcloud-confluent-springboot
```

## <a name="3-prepare-cloud-services"></a>3. Vorbereiten von Clouddiensten

### <a name="31-create-an-instance-of-apache-kafka-for-confluent-cloud"></a>3.1 Erstellen einer Instanz von Apache Kafka für Confluent Cloud

Erstellen Sie eine Instanz von Apache Kafka für Confluent Cloud, indem Sie [diese Anleitung](../partner-solutions/apache-kafka-confluent-cloud/create.md) befolgen.

### <a name="32-create-kafka-cluster-and-schema-registry-on-confluent-cloud"></a>3.2 Erstellen eines Kafka-Clusters und einer Schemaregistrierung in Confluent Cloud

1. Melden Sie sich bei Confluent Cloud über die von Azure bereitgestellte SSO-Funktionalität (Single Sign-On, einmaliges Anmelden) an.

    :::image type="content" source="media/tutorial-java-spring-confluent-kafka/azure-confluent-sso-login.png" alt-text="Link der SSO-Anmeldung bei Confluent Cloud über das Azure-Portal" lightbox="media/tutorial-java-spring-confluent-kafka/azure-confluent-sso-login.png":::

1. Verwenden der Standardumgebung oder Erstellen einer neuen Umgebung

    :::image type="content" source="media/tutorial-java-spring-confluent-kafka/confluent-cloud-env.png" alt-text="Cloudumgebung von Apache Kafka in Confluent Cloud" lightbox="media/tutorial-java-spring-confluent-kafka/confluent-cloud-env.png":::

1. Erstellen eines Kafka-Clusters mit den folgenden Informationen

    * Clustertyp: Standard
    * Region/Zonen: eastus(Virginia), Einzelzone
    * Clustername: `cluster_1` oder ein anderer Name

1. Rufen Sie in **Cluster-Übersicht** -> **Cluster-Einstellungen** die **Bootstrapserver-URL** für Kafka ab und notieren Sie diese.

    :::image type="content" source="media/tutorial-java-spring-confluent-kafka/confluent-cluster-setting.png" alt-text="Clustereinstellungen von Apache Kafka in Confluent Cloud" lightbox="media/tutorial-java-spring-confluent-kafka/confluent-cluster-setting.png":::

1. Erstellen Sie in **Datenintegration** -> **API-Schlüssel** ->  **+ Schlüssel hinzufügen** API-Schlüssel für den Cluster mit **globalem Zugriff**. Notieren Sie sich den Schlüssel und das Geheimnis.
1. Erstellen eines Themas mit dem Namen `test` mit Partitionen (6) in **Themen** ->  **+ Thema hinzufügen**
1. Klicken Sie in der Standardumgebung auf die Registerkarte **Schemaregistrierung**. Aktivieren Sie die Schemaregistrierung, und notieren Sie sich den **API-Endpunkt**.
1. Erstellen Sie API-Schlüssel für die Schemaregistrierung. Notieren Sie sich den Schlüssel und das Geheimnis.

### <a name="33-create-a-spring-cloud-instance"></a>3.3 Erstellen einer Spring Cloud-Instanz

Erstellen Sie eine Instanz von Azure Spring Cloud, indem Sie [diese Anleitung](../spring-cloud/quickstart.md) in Java befolgen. Stellen Sie sicher, dass Ihre Spring Cloud-Instanz in einer Region mit [Dienstconnectorunterstützung](concept-region-support.md) erstellt wird.

## <a name="4-build-and-deploy-the-app"></a>4. Erstellen und Bereitstellen der App

### <a name="41-build-the-sample-app-and-create-a-new-spring-app"></a>4.1 Erstellen der Beispiel-App und Erstellen einer neuen Spring-App

1. Melden Sie sich bei Azure an, und wählen Sie Ihr Abonnement aus.

```azurecli
az login

az account set --subscription <Name or ID of your subscription>
```

1. Erstellen des Projekt mithilfe von gradle

```Bash
./gradlew build
```

1. Erstellen Sie die App mit einem zugewiesenen öffentlichen Endpunkt. Wenn Sie beim Generieren des Spring Cloud-Projekts Java Version 11 ausgewählt haben, schließen Sie den Schalter „--runtime-version=Java_11“ ein.

```azurecli
az spring-cloud app create -n hellospring -s <service-instance-name> -g <your-resource-group-name> --assign-endpoint true
```

## <a name="42-create-service-connection-using-service-connector"></a>4.2 Erstellen einer Dienstverbindung mithilfe des Dienstconnectors

#### <a name="cli"></a>[BEFEHLSZEILENSCHNITTSTELLE (CLI)](#tab/Azure-CLI)

Führen Sie den folgenden Befehl aus, um Ihre Apache Kafka-Instanz in Confluent Cloud mit Ihrer Spring Cloud-App zu verbinden.

```azurecli
az spring-cloud connection create confluent-cloud -g <your-spring-cloud-resource-group> --service <your-spring-cloud-service> --app <your-spring-cloud-app> --deployment <your-spring-cloud-deployment> --bootstrap-server <kafka-bootstrap-server-url> --kafka-key <cluster-api-key> --kafka-secret <cluster-api-secret> --schema-registry <kafka-schema-registry-endpoint> --schema-key <registry-api-key> --schema-secret <registry-api-secret>
```

* **Ersetzen** Sie *\<your-resource-group-name>* durch den Ressourcengruppennamen, den Sie für Ihre Spring Cloud-Instanz erstellt haben.
* **Ersetzen** Sie *\<kafka-bootstrap-server-url>* durch Ihre Kafka-Bootstrapserver-URL (der Wert sollte ungefähr so aussehen: `pkc-xxxx.eastus.azure.confluent.cloud:9092`).
* **Ersetzen** Sie *\<cluster-api-key>* und *\<cluster-api-secret>* durch Ihren Cluster-API-Schlüssel und das zugehörige Geheimnis.
* **Ersetzen** Sie *\<kafka-schema-registry-endpoint>* durch den Endpunkt Ihrer Kafka-Schemaregistrierung (der Wert sollte ungefähr so aussehen: `https://psrc-xxxx.westus2.azure.confluent.cloud`).
* **Ersetzen** Sie *\<registry-api-key>* und *\<registry-api-secret>* durch Ihren Kafka-Schemaregistrierungs-API-Schlüssel und das zugehörige Geheimnis.

> [!NOTE]
> Wird die Fehlermeldung „Das Abonnement ist nicht für die Verwendung des Ressourcenanbieters "{0}" registriert.“ angezeigt, führen Sie `az provider register -n Microsoft.ServiceLinker` aus, um den Dienstconnector-Ressourcenanbieter zu registrieren. Führen Sie anschließend erneut den Verbindungsbefehl aus. 

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

Klicken Sie auf **Dienstconnector (Vorschau)** , und wählen Sie die folgenden Einstellungen aus, oder geben Sie sie ein.

| Einstellung      | Vorgeschlagener Wert  | BESCHREIBUNG                               |
| ------------ |  ------- | -------------------------------------------------- |
| **Diensttyp** | Apache Kafka in Confluent Cloud | Zieldiensttyp. Wenn Sie keine Apache Kafka-Instanz in Confluent Cloud haben, führen Sie die vorherigen Schritte in diesem Tutorial aus. |
| **Name** | Generierter eindeutiger Name | Der Verbindungsname, der die Verbindung zwischen Ihrer Spring Cloud-Instanz und dem Zieldienst identifiziert  |
| **Kafka-Bootstrapserver-URL** | Ihre Kafka-Bootstrapserver-URL | Sie erhalten diesen Wert in Schritt 3.2. |
| **Cluster-API-Schlüssel** | Ihr Cluster-API-Schlüssel |  |
| **Cluster-API-Geheimnis** |  Ihr Cluster-API-Geheimnis |  |
| **Verbindung für Schemaregistrierung erstellen**  | checked | Erstellt auch eine Verbindung mit der Schemaregistrierung |
| **Schemaregistrierungsendpunkt** | Ihr Kafka-Schemaregistrierungsendpunkt  |  |
| **API-Schlüssel der Schemaregistrierung** | Der API-Schlüssel für Ihre Kafka-Schemaregistrierung | |
| **API-Geheimnis der Schemaregistrierung** | Das API-Geheimnis für Ihre Kafka-Schemaregistrierung | |

Wählen Sie **Überprüfen + erstellen** aus, um die Verbindungseinstellungen zu überprüfen. Wählen Sie dann **Erstellen** aus, um mit der Erstellung der Dienstverbindung zu beginnen.

---

## <a name="43-deploy-the-jar-file-for-the-app"></a>4.3 Bereitstellen der JAR-Datei für die App

Führen Sie den folgenden Befehl aus, um die JAR-Datei (`build/libs/java-springboot-0.0.1-SNAPSHOT.jar`) in Ihre Spring Cloud-App hochzuladen.

```azurecli
az spring-cloud app deploy -n hellospring -s <service-instance-name> -g <your-resource-group-name>  --artifact-path build/libs/java-springboot-0.0.1-SNAPSHOT.jar
```

## <a name="5-validate-the-kafka-data-ingestion"></a>5. Überprüfen der Kafka-Datenerfassung

Navigieren Sie im Azure-Portal zum Endpunkt Ihrer Spring Cloud-App, und klicken Sie auf die Anwendungs-URL. Der Text „10 Meldungen wurden für den Thementest erstellt.“ wird angezeigt.

Wechseln Sie dann zum Confluent-Portal. Auf der Seite des Themas wird der Produktionsdurchsatz angezeigt.

:::image type="content" source="media/tutorial-java-spring-confluent-kafka/confluent-sample-metrics.png" alt-text="Beispielmetriken" lightbox="media/tutorial-java-spring-confluent-kafka/confluent-sample-metrics.png":::

## <a name="next-steps"></a>Nächste Schritte

Befolgen Sie die unten aufgeführten Tutorials, um mehr über Service Connector zu erfahren.

> [!div class="nextstepaction"]
> [Informationen zu Service Connector-Konzepten](./concept-service-connector-internals.md)
