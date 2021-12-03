---
title: 'Tutorial: Bereitstellen von Spring Boot-Anwendungen mithilfe von IntelliJ'
description: Bereitstellen von Anwendungen in Azure Spring Cloud mithilfe von IntelliJ
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 11/03/2021
ms.custom: devx-track-java
ms.openlocfilehash: 0db8cb4393f07b4a7ab8f9f24d1c635aaffcd133
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493721"
---
# <a name="deploy-spring-boot-applications-using-intellij"></a>Bereitstellen von Spring Boot-Anwendungen mithilfe von IntelliJ


**Dieser Artikel gilt für:** ✔️ Java

Das IntelliJ-Plug-In für Azure Spring Cloud unterstützt die Anwendungsbereitstellung über IntelliJ IDEA.

Bevor Sie dieses Beispiel ausführen, können Sie den [grundlegenden Schnellstart](./quickstart.md) ausprobieren.

## <a name="prerequisites"></a>Voraussetzungen

* [IntelliJ IDEA, Community/Ultimate Edition, Version 2020.1/2020.2](https://www.jetbrains.com/idea/download/#section=windows)

## <a name="install-the-plug-in"></a>Installieren des Plug-Ins

Sie können das Azure-Toolkit für IntelliJ IDEA 3.51.0 über die **Plug-Ins**-Benutzeroberfläche von IntelliJ hinzufügen.

1. Starten Sie IntelliJ.  Wenn Sie zuvor ein Projekt geöffnet hatten, schließen Sie das Projekt, um das Dialogfeld „Willkommen“ anzuzeigen. Wählen Sie über den Link unten rechts **Konfigurieren** und dann **Plug-Ins** aus, um das Dialogfeld „Plug-In-Konfiguration“ zu öffnen. Wählen Sie anschließend **Install Plugins from disk** (Plug-Ins von Datenträger installieren) aus.

    ![Auswählen von „Configure“ (Konfigurieren)](media/spring-cloud-intellij-howto/configure-plugin-1.png)

1. Suchen Sie nach dem Azure-Toolkit für IntelliJ. Wählen Sie **Installieren** aus.

    ![Installieren des Plug-Ins](media/spring-cloud-intellij-howto/install-plugin.png)

1. Wählen Sie **IDE neu starten** aus.

## <a name="tutorial-procedures"></a>Prozeduren des Tutorials

Mit den folgenden Verfahren wird eine Hello World-Anwendung unter Verwendung von IntelliJ IDEA bereitgestellt.

* Öffnen des Projekts „gs-spring-boot“
* In Azure Spring Cloud bereitstellen
* Streamingprotokolle anzeigen

## <a name="open-gs-spring-boot-project"></a>Projekt „gs-spring-boot“ öffnen

1. Laden Sie das Quellrepository für dieses Tutorial herunter, und entpacken Sie es oder klonen Sie es mithilfe des folgenden Git-Befehls: `git clone https://github.com/spring-guides/gs-spring-boot.git`
1. Navigieren Sie zum Ordner *gs-spring-boot\complete*.
1. Öffnen Sie das IntelliJ-Dialogfeld **Willkommens**, und wählen Sie **Projekt importieren** aus, um den Importassistenten zu öffnen.
1. Wählen Sie den Ordner *gs-spring-boot\complete* aus.

    ![Importieren des Projekts](media/spring-cloud-intellij-howto/import-project-1.png)

## <a name="deploy-to-azure-spring-cloud"></a>In Azure Spring Cloud bereitstellen

Für die Bereitstellung in Azure müssen Sie sich bei Ihrem Azure-Konto anmelden und Ihr Abonnement auswählen.  Anmeldeinformationen finden Sie unter [Installation und Anmeldung](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Klicken Sie im Projektexplorer von IntelliJ mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Azure** -> **In Azure Spring Cloud bereitstellen** aus.

    ![Bereitstellen in Azure 1](media/spring-cloud-intellij-howto/deploy-to-azure-1.png)

1. Übernehmen Sie den Namen für die App im Feld **Name**. **Name** bezieht sich auf die Konfiguration, nicht auf den App-Namen. Benutzer müssen ihn in der Regel nicht ändern.
1. Übernehmen Sie den Bezeichner des Projekts für das **Artefakt**.
1. Wählen Sie **App:** und dann **App erstellen...** aus.

    ![Bereitstellen in Azure 2](media/spring-cloud-intellij-howto/deploy-to-azure-2.png)

1. Geben Sie **App-Name** ein, und wählen Sie dann **OK** aus.

    ![In Azure bereitstellen OK](media/spring-cloud-intellij-howto/deploy-to-azure-2a.png)

1. Starten Sie die Bereitstellung, indem Sie auf die Schaltfläche **Ausführen** klicken.

    ![Bereitstellen in Azure 3](media/spring-cloud-intellij-howto/deploy-to-azure-3.png)

1. Das Plug-In führt den Befehl `mvn package` für das Projekt aus, erstellt dann die neue App und stellt die vom `package`-Befehl generierte JAR-Datei bereit.

1. Wenn die App-URL im Ausgabefenster nicht angezeigt wird, finden Sie sie im Azure-Portal. Navigieren Sie von Ihrer Ressourcengruppe zur Instanz von Azure Spring Cloud.  Wählen Sie anschließend **Apps** aus.  Die ausgeführte App ist aufgelistet. Wählen Sie die App aus, und kopieren Sie dann die **URL** oder den **Testendpunkt**.

    ![Test-URL abrufen](media/spring-cloud-intellij-howto/get-test-url.png)

1. Navigieren Sie im Browser zur URL oder zum Testendpunkt.

    ![Navigieren in Browser 2](media/spring-cloud-intellij-howto/navigate-in-browser-2.png)

## <a name="show-streaming-logs"></a>Streamingprotokolle anzeigen

Abrufen der Protokolle:

1. Wählen Sie **Azure-Explorer** und dann **Spring Cloud** aus.
1. Klicken Sie mit der rechten Maustaste auf die ausgeführte App.
1. Wählen Sie in der Dropdownliste **Streamingprotokolle** aus.

    ![Streamingprotokolle auswählen](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Wählen Sie die Instanz aus.

    ![Instanz auswählen](media/spring-cloud-intellij-howto/select-instance.png)

1. Das Streamingprotokoll wird im Ausgabefenster angezeigt.

    ![Ausgabe des Streamingprotokolls](media/spring-cloud-intellij-howto/streaming-log-output.png)

## <a name="next-steps"></a>Nächste Schritte

* [Vorbereiten der Spring-Anwendung für Azure Spring Cloud](how-to-prepare-app-deployment.md)
* [Weitere Informationen zum Azure Toolkit für IntelliJ](/azure/developer/java/toolkit-for-intellij/)
