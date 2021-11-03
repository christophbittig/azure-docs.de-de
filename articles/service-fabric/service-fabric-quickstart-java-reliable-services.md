---
title: 'Schnellstart: Erstellen einer Java-App in Azure Service Fabric'
description: In diesem Schnellstart erstellen Sie eine Java-Anwendung für Azure, indem Sie eine Service Fabric Reliable Services-Beispielanwendung verwenden.
ms.date: 01/29/2019
ms.topic: quickstart
ms.custom: mvc, devcenter, seo-java-august2019, seo-java-september2019, devx-track-java, mode-api
ms.openlocfilehash: e1c4fe08c0d1a5ace5fec0c1a2330c111a5122ed
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131083496"
---
# <a name="quickstart--deploy-a-java-app-to-azure-service-fabric-on-linux"></a>Schnellstart: Bereitstellen einer Java-App in Azure Service Fabric unter Linux

In dieser Schnellstartanleitung stellen Sie mithilfe der Eclipse-IDE eine Java-Anwendung in Azure Service Fabric auf einem Linux-Entwicklercomputer bereit. Das Ergebnis ist eine Anwendung mit einem Java-Web-Front-End, mit der Abstimmungsergebnisse im Cluster in einem zustandsbehafteten Back-End-Dienst gespeichert werden.

Azure Service Fabric ist eine Plattform, mit der verteilte Systeme bereitgestellt und skalierbare und zuverlässige Microservices und Container verwaltet werden können.

## <a name="prerequisites"></a>Voraussetzungen

- [Java-Umgebung](./service-fabric-get-started-linux.md#set-up-java-development) und [Yeoman](./service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables)
- [Eclipse Neon (4.6) oder höher](https://www.eclipse.org/downloads/packages/) und [Eclipse-Plug-In für Service Fabric](./service-fabric-get-started-linux.md#install-the-eclipse-plug-in-optional)
- [Service Fabric SDK und die Befehlszeilenschnittstelle (Command Line Interface, CLI)](./service-fabric-get-started-linux.md#installation-methods)
- [Git-Client](https://git-scm.com/downloads)

## <a name="download-the-sample"></a>Herunterladen des Beispiels

Führen Sie in einem Befehlsfenster den folgenden Befehl aus, um das Beispiel-App-Repository auf Ihren lokalen Computer zu klonen.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
```

## <a name="run-the-application-locally"></a>Lokales Ausführen der Anwendung

1. Starten Sie Ihren lokalen Cluster mit dem folgenden Befehl:

    ```bash
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Es kann einige Zeit dauern, bis der lokale Cluster startet. Um zu prüfen, ob der Cluster vollständig betriebsbereit ist, greifen Sie unter `http://localhost:19080` auf Service Fabric Explorer zu. Wenn die fünf Knoten fehlerfrei sind, wird der lokale Cluster ausgeführt.

    ![Azure Service Fabric Explorer zeigt fehlerfreie Knoten an.](./media/service-fabric-quickstart-java/service-fabric-explorer-healthy-nodes.png)

2. Öffnen Sie Eclipse.
3. Klicken Sie auf **Datei** > **Importieren** > **Gradle** > **Vorhandenes Gradle-Projekt**, und führen Sie die Schritte des Assistenten aus.
4. Wählen Sie **Verzeichnis** und dann im Ordner **service-fabric-java-quickstart**, den Sie aus GitHub geklont haben, das Verzeichnis **Voting** aus. Wählen Sie **Fertig stellen** aus.

    ![Importieren des Gradle-Projekts in Eclipse](./media/service-fabric-quickstart-java/eclipse-import-gradle-project.png)

5. Das Projekt `Voting` befindet sich nun im Paket-Explorer von Eclipse.
6. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie in der Dropdownliste **Service Fabric** die Option **Anwendung veröffentlichen** aus. Wählen Sie **PublishProfiles/Local.json** als Zielprofil aus, und klicken Sie auf **Veröffentlichen**.

    ![Veröffentlichen von lokalem JSON in Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-publish-local-json.png)

7. Öffnen Sie Ihren bevorzugten Browser, und greifen Sie über `http://localhost:8080` auf die Anwendung zu.

    ![Lokaler Azure Service Fabric-Host](./media/service-fabric-quickstart-java/service-fabric-local-host.png)

Sie können jetzt einen Satz mit Abstimmungsoptionen hinzufügen und die Abstimmung freigeben. Die Anwendung führt alle Daten in Ihrem Service Fabric-Cluster aus und speichert sie dort, ohne dass eine separate Datenbank verwendet werden muss.

![Azure Service Fabric-Beispiel „Voting“](./media/service-fabric-quickstart-java/service-fabric-voting-sample.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>Skalieren von Anwendungen und Diensten in einem Cluster

Dienste können clusterweit skaliert werden, um eine Änderung der Last für die Dienste auszugleichen. Sie skalieren einen Dienst, indem Sie die Anzahl von Instanzen ändern, die im Cluster ausgeführt werden. Es gibt viele Methoden zum Skalieren Ihrer Dienste. Sie können beispielsweise Skripts oder Befehle über die Service Fabric-Befehlszeilenschnittstelle (`sfctl`) verwenden. In den folgenden Schritten wird Service Fabric Explorer verwendet.

Service Fabric Explorer wird in allen Service Fabric-Clustern ausgeführt und kann in einem Browser geöffnet werden. Navigieren Sie hierzu zum HTTP-Verwaltungsport des Clusters (19080). Beispiel: `http://localhost:19080`.

Gehen Sie zum Skalieren des Web-Front-End-Diensts wie folgt vor:

1. Öffnen Sie Service Fabric Explorer in Ihrem Cluster. Beispiel: `https://localhost:19080`.
2. Klicken Sie in der Strukturansicht neben dem Knoten **fabric:/Voting/VotingWeb** auf das Auslassungszeichen (**...**), und wählen Sie **Dienst skalieren** aus.

    ![Skalieren eines Diensts in Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-scale-service.png)

    Sie können jetzt angeben, dass die Anzahl von Instanzen des Web-Front-End-Diensts skaliert werden soll.

3. Ändern Sie die Anzahl in **2**, und wählen Sie **Dienst skalieren** aus.
4. Wählen Sie in der Strukturansicht den Knoten **fabric:/Voting/VotingWeb** aus, und erweitern Sie den Partitionsknoten (durch eine GUID dargestellt).

    ![Skalierter Dienst in Azure Service Fabric](./media/service-fabric-quickstart-java/service-fabric-explorer-service-scaled.png)

    Sie sehen nun, dass der Dienst über zwei Instanzen verfügt, und in der Strukturansicht ist zu erkennen, auf welchen Knoten die Instanzen ausgeführt werden.

Mit dieser einfachen Verwaltungsaufgabe haben Sie die Ressourcen verdoppelt, die dem Front-End-Dienst zur Bewältigung der Benutzerauslastung zur Verfügung stehen. Es ist wichtig zu verstehen, dass Sie nicht mehrere Instanzen eines Diensts benötigen, damit er zuverlässig ausgeführt wird. Sollte ein Dienst ausfallen, stellt Service Fabric sicher, dass im Cluster eine neue Dienstinstanz ausgeführt wird.

## <a name="next-steps"></a>Nächste Schritte

In diesem Schnellstart haben Sie Folgendes gelernt:

* Verwenden von Eclipse als Tool für Ihre Service Fabric-Java-Anwendungen
* Bereitstellen von Java-Anwendungen im lokalen Cluster
* Horizontales Hochskalieren der Anwendung über mehrere Knoten hinweg

Weitere Informationen zur Verwendung von Java-Apps in Service Fabric finden Sie im Tutorial für Java-Apps.

> [!div class="nextstepaction"]
> [Bereitstellen einer Java-App](./service-fabric-tutorial-create-java-app.md)
