---
title: 'Schnellstart: Erstellen und Pushen von Java-Containerimages in Azure Container Registry mithilfe von Maven und Jib'
description: Erstellen Sie eine Java-App in einem Container, und übertragen Sie sie mithilfe des Maven-Jib-Plug-ins per Push an Azure Container Registry.
author: KarlErickson
ms.author: karler
ms.date: 02/26/2020
ms.topic: quickstart
ms.custom: devx-track-java, devx-track-azurecli, mode-api
ms.openlocfilehash: eccbad4964dc34c33fd00873d92e58302e9bf2cf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131080192"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Schnellstart: Erstellen und Pushen von Java-Containerimages in Azure Container Registry

In diesem Schnellstart erfahren Sie, wie Sie eine Java-App in einem Container erstellen und diese mithilfe des Maven-Jib-Plug-ins per Push an Azure Container Registry übertragen. Die Verwendung von Maven und Jib ist ein Beispiel für die Nutzung von Entwicklertools zur Interaktion mit einer Azure-Containerregistrierung.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement – wenn Sie noch kein Azure-Abonnement besitzen, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) anwenden oder sich für ein [Kostenloses Azure-Konto](https://azure.microsoft.com/pricing/free-trial) registrieren
* Die [Azure-Befehlszeilenschnittstelle (CLI)](/cli/azure/overview)
* Ein unterstütztes Java Development Kit (JDK). Weitere Informationen zu den JDKs, die für die Entwicklung in Azure verfügbar sind, finden Sie im Artikel zur [Java-Unterstützung in Azure und Azure Stack](/azure/developer/java/fundamentals/java-support-on-azure).
* Das Erstellungstool [Maven](http://maven.apache.org) von Apache (Version 3 oder höher)
* Einen [Git-Client](https://git-scm.com)
* Einen [Docker](https://www.docker.com)-Client
* Das [Hilfsprogramm für ACR-Docker-Anmeldeinformationen](https://github.com/Azure/acr-docker-credential-helper)

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Erstellen der Web-App für erste Schritte mit Spring Boot in Docker

Im Folgenden werden die Schritte zum Erstellen einer Spring Boot-Webanwendung und zum lokalen Testen dieser Anwendung beschrieben.

1. Geben Sie in der Eingabeaufforderung den folgenden Befehl ein, um das Beispielprojekt [Spring Boot on Docker Getting Started](https://github.com/spring-guides/gs-spring-boot-docker) zu klonen.

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Wechseln Sie in das Verzeichnis mit dem abgeschlossenen Projekt.

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Verwenden Sie Maven zum Erstellen und Ausführen der Beispiel-App.

   ```bash
   mvn package spring-boot:run
   ```

1. Testen Sie die Web-App durch Navigieren zu `http://localhost:8080` oder mit dem folgenden `curl`-Befehl:

   ```bash
   curl http://localhost:8080
   ```

Die folgende Meldung sollte angezeigt werden: **Hello Docker World!**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Erstellen einer Azure Container Registry-Instanz über die Azure-Befehlszeilenschnittstelle

Als Nächstes erstellen Sie mithilfe der folgenden Schritte eine Azure-Ressourcengruppe und die ACR-Instanz:

1. Melden Sie sich mithilfe des folgenden Befehls bei Ihrem Azure-Konto an:

   ```azurecli
   az login
   ```

1. Geben Sie an, welches Azure-Abonnement verwendet werden soll:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Erstellen Sie eine Ressourcengruppe für die in diesem Tutorial verwendeten Azure-Ressourcen. Im folgenden Befehl müssen Sie die Platzhalter durch Ihren eigenen Ressourcennamen und einen Speicherort wie `eastus` ersetzen.

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Erstellen Sie mit dem folgenden Befehl eine private Azure-Containerregistrierung in der Ressourcengruppe. Ersetzen Sie die Platzhalter durch tatsächliche Werte. Die Beispiel-App wird in diesem Tutorial in späteren Schritten als Docker-Image per Push in diese Registrierung übertragen.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Pushen der App in die Containerregistrierung über Jib

Abschließend aktualisieren Sie Ihre Projektkonfiguration und verwenden die Eingabeaufforderung, um Ihr Image zu erstellen und bereitzustellen.

> [!NOTE]
> Der Docker-Daemon muss ausgeführt werden, damit Sie sich bei der eben erstellten Azure-Containerregistrierung anmelden können. Wenn Sie Docker auf Ihrem Computer installieren möchten, finden Sie [hier](https://docs.docker.com/install/) die offizielle Docker-Dokumentation.

1. Melden Sie sich mit dem folgenden Befehl über die Azure-Befehlszeilenschnittstelle bei Ihrer Azure Container Registry-Instanz an. Ersetzen Sie den Platzhalter durch den Namen Ihrer eigenen Registrierung.

   ```azurecli
   az config set defaults.acr=<your registry name>
   az acr login
   ```

   Der Befehl `az config` legt den Standardregistrierungsnamen fest, der mit den `az acr`-Befehlen verwendet wird.

1. Navigieren Sie zum Verzeichnis des abgeschlossenen Projekts für Ihre Spring Boot-Anwendung (z.B.„*C:\SpringBoot\gs-spring-boot-docker\complete*“ oder „ */users/robert/SpringBoot/gs-spring-boot-docker/complete*“), und öffnen Sie die Datei *pom.xml* mit einem Text-Editor.

1. Aktualisieren Sie die Auflistung `<properties>` in der Datei *pom.xml* mit der folgenden XML. Ersetzen Sie den Platzhalter durch Ihren Registrierungsnamen, und fügen Sie eine Eigenschaft vom Typ `<jib-maven-plugin.version>` mit dem Wert `2.2.0` oder eine neuere Version von [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin) hinzu.

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <jib-maven-plugin.version>2.2.0</jib-maven-plugin.version>
   </properties>
   ```

1. Aktualisieren Sie die Sammlung `<plugins>` in der Datei *pom.xml*, sodass das `<plugin>`-Element einen Eintrag für `jib-maven-plugin` enthält, wie im folgenden Beispiel gezeigt. Beachten Sie, dass ein Basisimage aus der Microsoft Container Registry (MCR) „`mcr.microsoft.com/java/jdk:8-zulu-alpine`“ verwendet wird, das eine offiziell unterstützte JDK-Version für Azure enthält. Informationen zu anderen MCR-Basisimages mit offiziell unterstützten JDK-Versionen finden Sie unter [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre), [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless) und [Java SE JDK and Maven](https://hub.docker.com/_/microsoft-java-maven).

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Navigieren Sie zum Verzeichnis des abgeschlossenen Projekts für Ihre Spring Boot-Anwendung, und führen Sie den folgenden Befehl aus, um das Image zu erstellen und per Push in die Registrierung zu übertragen:

   ```bash
   az acr login && mvn compile jib:build
   ```

> [!NOTE]
>
> Aus Sicherheitsgründen sind die mit `az acr login` erstellten Anmeldeinformationen nur 1 Stunde lang gültig. Wenn Sie den Fehler *401 – Nicht autorisiert* erhalten, können Sie den Befehl `az acr login -n <your registry name>` nochmals ausführen, um sich noch mal zu authentifizieren.

## <a name="verify-your-container-image"></a>Verifizieren des Containerimages

Glückwunsch! Sie haben nun Ihre in Azure erstellte Java-App in einem Container, der JDK unterstützt, in Ihre ACR-Instanz gepusht. Jetzt können Sie das Image testen, indem Sie es im Azure App Service bereitstellen oder es mit einem Befehl (in dem die Platzhalter ersetzt sind) auf die lokale Ebene pullen:

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker
```

## <a name="next-steps"></a>Nächste Schritte

Weitere Versionen der offiziellen, von Microsoft unterstützten Java-Basisimages finden Sie unter:

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK and Maven](https://hub.docker.com/_/microsoft-java-maven)

Weitere Informationen zu Spring und Azure finden Sie im Dokumentationscenter zu Spring in Azure.

> [!div class="nextstepaction"]
> [Spring Framework in Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Weitere Ressourcen

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Azure für Java-Entwickler](/azure/java)
* [Working with Azure DevOps and Java](/azure/devops/java) (Arbeiten mit Azure DevOps und Java)
* [Spring Boot on Docker Getting Started](https://spring.io/guides/gs/spring-boot-docker) (Erste Schritte mit Spring Boot in Docker)
* [Spring Initializr](https://start.spring.io)
* [Bereitstellen von Spring Boot-Anwendungen in Azure App Service](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-on-linux#configure-maven-to-build-image-to-your-azure-container-registry)
* [Verwenden eines benutzerdefinierten Docker-Images für Azure-Web-Apps unter Linux](../app-service/tutorial-custom-container.md)
