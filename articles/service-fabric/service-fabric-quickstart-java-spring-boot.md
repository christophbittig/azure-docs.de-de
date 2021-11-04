---
title: 'Schnellstart: Erstellen einer Spring Boot-App in Azure Service Fabric'
description: In diesem Schnellstart stellen Sie eine Spring Boot-Anwendung für Azure Service Fabric bereit, indem Sie eine Spring Boot-Beispielanwendung verwenden.
ms.date: 01/29/2019
ms.topic: quickstart
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019, devx-track-java, mode-api
ms.openlocfilehash: 73f133e38fbe0fd2deb547650ef0afbfa0d92beb
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131075180"
---
# <a name="quickstart-deploy-a-java-spring-boot-app-on-azure-service-fabric"></a>Schnellstart: Bereitstellen einer Java Spring Boot-App in Azure Service Fabric

In dieser Schnellstartanleitung stellen Sie eine Java Spring Boot-Anwendung in Azure Service Fabric bereit, indem Sie vertraute Befehlszeilentools in Linux oder MacOS verwenden. Azure Service Fabric ist eine Plattform, mit der verteilte Systeme bereitgestellt und skalierbare und zuverlässige Microservices und Container verwaltet werden können. 

## <a name="prerequisites"></a>Voraussetzungen

#### <a name="linux"></a>[Linux](#tab/linux)

- [Java-Umgebung](./service-fabric-get-started-linux.md#set-up-java-development) und [Yeoman](./service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Service Fabric SDK und die Service Fabric-Befehlszeilenschnittstelle (Command Line Interface, CLI)](./service-fabric-get-started-linux.md#installation-methods)
- [Git-Client](https://git-scm.com/downloads)

#### <a name="macos"></a>[MacOS](#tab/macos)

- [Java-Umgebung und Yeoman](./service-fabric-get-started-mac.md#create-your-application-on-your-mac-by-using-yeoman)
- [Service Fabric SDK und die Service Fabric-Befehlszeilenschnittstelle (Command Line Interface, CLI)](./service-fabric-cli.md#cli-mac)
- [Git-Client](https://git-scm.com/downloads)

--- 

## <a name="download-the-sample"></a>Herunterladen des Beispiels

Führen Sie in einem Terminalfenster den folgenden Befehl aus, um das Spring Boot-Beispiel für die ersten Schritte ([Getting Started](https://github.com/spring-guides/gs-spring-boot)) auf Ihrem lokalen Computer zu klonen:

```bash
git clone https://github.com/spring-guides/gs-spring-boot.git
```

## <a name="build-the-spring-boot-application"></a>Erstellen der Spring Boot-Anwendung 
Führen Sie im Verzeichnis *gs-spring-boot/complete* den folgenden Befehl aus, um die Anwendung zu erstellen: 

```bash
./gradlew build
``` 

## <a name="package-the-spring-boot-application"></a>Packen der Spring Boot-Anwendung 
1. Führen Sie in Ihrem Klon im Verzeichnis *gs-spring-boot* den Befehl `yo azuresfguest` aus. 

1. Geben Sie die folgenden Details für die einzelnen Eingabeaufforderungen ein.

    ![Spring Boot-Yeoman-Einträge](./media/service-fabric-quickstart-java-spring-boot/yeoman-entries-spring-boot.png)

1. Erstellen Sie im Ordner *SpringServiceFabric/SpringServiceFabric/SpringGettingStartedPkg/code* eine Datei namens *entryPoint.sh*. Fügen Sie der Datei *entryPoint.sh* den folgenden Code hinzu: 

    ```bash
    #!/bin/bash
    BASEDIR=$(dirname $0)
    cd $BASEDIR
    java -jar *spring-boot*.jar
    ```

1. Fügen Sie die Ressource **Endpunkte** in der Datei *gs-spring-boot/SpringServiceFabric/SpringServiceSabric/SpringGettingStartedPkg/ServiceManifest.xml* hinzu.

    ```xml 
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
    ```

    Die Datei *ServiceManifest.xml* sieht nun wie folgt aus: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceManifest Name="SpringGettingStartedPkg" Version="1.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" >

       <ServiceTypes>
          <StatelessServiceType ServiceTypeName="SpringGettingStartedType" UseImplicitHost="true">
       </StatelessServiceType>
       </ServiceTypes>

       <CodePackage Name="code" Version="1.0.0">
          <EntryPoint>
             <ExeHost>
                <Program>entryPoint.sh</Program>
                <Arguments></Arguments>
                <WorkingFolder>CodePackage</WorkingFolder>
             </ExeHost>
          </EntryPoint>
       </CodePackage>
        <Resources>
          <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
          </Endpoints>
       </Resources>
     </ServiceManifest>
    ```

Damit haben Sie eine Service Fabric-Anwendung für das Spring Boot Getting Started-Beispiel erstellt, die Sie in Service Fabric bereitstellen können.

## <a name="run-the-application-locally"></a>Lokales Ausführen der Anwendung

1. Starten Sie Ihren lokalen Cluster auf Ubuntu-Computern mit dem folgenden Befehl:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```

    Starten Sie bei Verwendung eines Mac den lokalen Cluster über das Docker-Image. (Dabei wird davon ausgegangen, dass die [Voraussetzungen](./service-fabric-get-started-mac.md#create-a-local-container-and-set-up-service-fabric) zum Einrichten Ihres lokalen Clusters für Mac erfüllt sind.) 

    ```bash
    docker run --name sftestcluster -d -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 -p 8080:8080 mysfcluster
    ```

    Es kann einige Zeit dauern, bis der lokale Cluster startet. Um zu prüfen, ob der Cluster vollständig betriebsbereit ist, greifen Sie unter `http://localhost:19080` auf Service Fabric Explorer zu. Wenn die fünf Knoten fehlerfrei sind, wird der lokale Cluster ausgeführt. 
    
    ![Service Fabric Explorer zeigt fehlerfreie Knoten an.](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-healthy-nodes.png)

1. Öffnen Sie den Ordner *gs-spring-boot/SpringServiceFabric*.
1. Führen Sie den folgenden Befehl aus, um eine Verbindung mit dem lokalen Cluster herzustellen.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```
1. Führen Sie das Skript *install.sh* aus.

    ```bash
    ./install.sh
    ```

1. Öffnen Sie Ihren bevorzugten Browser, und greifen Sie über `http://localhost:8080` auf die Anwendung zu.

    ![Spring Boot-Beispiel für Service Fabric](./media/service-fabric-quickstart-java-spring-boot/spring-boot-service-fabric-sample.png)

Sie können nun auf die Spring Boot-Anwendung zugreifen, die in einem Service Fabric-Cluster bereitgestellt wurde.

Weitere Informationen finden Sie auf der Spring-Website im Spring Boot-Beispiel für die [ersten Schritte](https://spring.io/guides/gs/spring-boot/).

## <a name="scale-applications-and-services-in-a-cluster"></a>Skalieren von Anwendungen und Diensten in einem Cluster

Dienste können clusterweit skaliert werden, um eine Laständerungen für die Dienste auszugleichen. Sie skalieren einen Dienst, indem Sie die Anzahl von Instanzen ändern, die im Cluster ausgeführt werden. Dienste können auf unterschiedliche Weise skaliert werden – beispielsweise mithilfe von Skripts oder Befehlen der Service Fabric-Befehlszeilenschnittstelle (sfctl). In den folgenden Schritten wird Service Fabric Explorer verwendet.

Service Fabric Explorer wird in allen Service Fabric-Clustern ausgeführt und kann in einem Browser geöffnet werden. Navigieren Sie hierzu zum HTTP-Verwaltungsport des Clusters (19.080). Beispiel: `http://localhost:19080`.

Gehen Sie zum Skalieren des Web-Front-End-Diensts wie folgt vor:

1. Öffnen Sie Service Fabric Explorer in Ihrem Cluster, z.B. `http://localhost:19080`.
1. Klicken Sie in der Strukturansicht neben dem Knoten **fabric:/SpringServiceFabric/SpringGettingStarted** auf das Auslassungszeichen (**...**), und wählen Sie **Dienst skalieren** aus.

    ![Service Fabric Explorer: Beispiel zur Dienstskalierung](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-scale-sample.png)

    Sie können jetzt angeben, wie viele Instanzen des Diensts skaliert werden sollen.

1. Ändern Sie die Anzahl in **3**, und wählen Sie **Dienst skalieren** aus.

    Alternativ kann der Dienst wie folgt über eine Befehlszeile skaliert werden.

    ```bash
    # Connect to your local cluster
    sfctl cluster select --endpoint https://<ConnectionIPOrURL>:19080 --pem <path_to_certificate> --no-verify

    # Run Bash command to scale instance count for your service
    sfctl service update --service-id 'SpringServiceFabric~SpringGettingStarted' --instance-count 3 --stateless 
    ``` 

1. Wählen Sie in der Strukturansicht den Knoten **fabric:/SpringServiceFabric/SpringGettingStarted** aus, und erweitern Sie den Partitionsknoten (durch eine GUID dargestellt).

    ![Service Fabric Explorer: Dienstskalierung abgeschlossen](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-partition-node.png)

    Der Dienst verfügt über drei Instanzen, und die Strukturansicht zeigt, auf welchen Knoten die Instanzen ausgeführt werden.

Mit dieser einfachen Verwaltungsaufgabe haben Sie die Ressourcen verdoppelt, die dem Front-End-Dienst zur Bewältigung der Benutzerauslastung zur Verfügung stehen. Es ist wichtig zu verstehen, dass Sie nicht mehrere Instanzen eines Diensts benötigen, damit er zuverlässig ausgeführt wird. Sollte ein Dienst ausfallen, stellt Service Fabric sicher, dass im Cluster eine neue Dienstinstanz ausgeführt wird.

## <a name="fail-over-services-in-a-cluster"></a>Ausführen eines Failovers für Dienste in einem Cluster

Zur Veranschaulichung eines Dienstfailovers wird unter Verwendung von Service Fabric Explorer ein Neustart des Knotens simuliert. Vergewissern Sie sich, dass nur eine einzelne Instanz des Diensts ausgeführt wird.

1. Öffnen Sie Service Fabric Explorer in Ihrem Cluster, z.B. `http://localhost:19080`.
1. Klicken Sie neben dem Knoten, auf dem die Instanz des Diensts ausgeführt wird, auf das Auslassungszeichen (**...**), und starten Sie den Knoten neu.

    ![Service Fabric Explorer: Neustarten des Knotens](./media/service-fabric-quickstart-java-spring-boot/service=fabric-explorer-restart=node.png)
1. Die Instanz des Diensts wird auf einen anderen Knoten verschoben, und die Anwendung kann ohne Ausfallzeit weiter verwendet werden.

    ![Service Fabric Explorer: Neustart des Knotens erfolgreich](./media/service-fabric-quickstart-java-spring-boot/service-fabric-explorer-service-moved.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie Folgendes gelernt:

* Bereitstellen einer Spring Boot-Anwendung in Service Fabric
* Bereitstellen der Anwendung im Cluster
* Horizontales Hochskalieren der Anwendung über mehrere Knoten hinweg
* Ausführen eines Failovers für den Dienst ohne Verfügbarkeitstreffer

Weitere Informationen zur Verwendung von Java-Apps in Service Fabric finden Sie im Tutorial für Java-Apps.

> [!div class="nextstepaction"]
> [Bereitstellen einer Java-App](./service-fabric-tutorial-create-java-app.md)
