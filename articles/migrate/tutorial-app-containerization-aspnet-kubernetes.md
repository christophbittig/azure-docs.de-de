---
title: 'Azure App-Containerisierung: ASP.NET; Containerisierung und Migration von ASP.NET-Anwendungen zu Azure Kubernetes.'
description: 'Tutorial: Containerisieren und Migrieren von ASP.NET-Anwendungen zu Azure Kubernetes Service'
services: ''
author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 6/30/2021
ms.author: rahugup
ms.openlocfilehash: fcb86290012a2888c926e335fb6169b43e0898b1
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114291995"
---
# <a name="aspnet-app-containerization-and-migration-to-azure-kubernetes-service"></a>Containerisieren und Migrieren von ASP.NET-Apps zu Azure Kubernetes Service

In diesem Artikel erfahren Sie, wie Sie unter ASP.NET-Anwendungen mit dem App-Containerisierungstool von Azure Migrate containerisieren und zu [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) migrieren können. Für den Containerisierungsvorgang ist kein Zugriff auf Ihre Codebasis erforderlich, zudem erfolgt die Containerisierung vorhandener Apps mühelos. Das Tool ermittelt anhand des Ausführungsstatus der Apps auf dem Server die App-Komponenten und packt diese in ein Containerimage. Die containerisierte Anwendung kann dann in Azure Kubernetes Service (AKS) bereitgestellt werden.

Das App-Containerisierungstool von Azure Migrate unterstützt derzeit die folgenden Vorgänge:

- Das Containerisieren von ASP.NET-Apps und das Bereitstellen dieser Apps in Windows-Containern in Azure Kubernetes Service.
- Das Containerisieren von ASP.NET-Apps und das Bereitstellen dieser Apps in Windows-Containern in Azure App Service. [Weitere Informationen](./tutorial-app-containerization-aspnet-app-service.md)
- Containerisieren von Java-Web-Apps unter Apache Tomcat (auf Linux-Servern) und Bereitstellen dieser Apps in Linux-Containern in AKS [Weitere Informationen](./tutorial-app-containerization-java-kubernetes.md)
- Das Containerisieren von Java-Web-Apps unter Apache Tomcat (auf Linux-Servern) und das Bereitstellen dieser Apps in Linux-Containern in App Service. [Weitere Informationen](./tutorial-app-containerization-java-app-service.md)

Das App-Containerisierungstool von Azure Migrate unterstützt Sie bei den folgenden Vorgängen:

- **App analysieren:** Das Tool stellt eine Remoteverbindung mit den Anwendungsservern her, auf denen Ihre ASP.NET-Anwendungen ausgeführt wird, und ermittelt die App-Komponenten. Dann erstellt das Tool ein Dockerfile, das zum Erstellen eines Containerimages für die App verwendet werden kann.
- **Containerimage erstellen:** Sie können das Dockerfile ansehen, an die Anforderungen der App anpassen und es anschließend verwenden, um das Containerimage für die App zu erstellen. Das Containerimage für die App wird an eine von Ihnen festgelegte Azure Container Registry-Instanz gepusht.
- **In Azure Kubernetes Service bereitstellen:** Das Tool generiert anschließend die YAML-Dateien für die Kubernetes-Ressourcendefinition, die benötigt werden, um die containerisierte Anwendung in Ihrem Azure Kubernetes Service-Cluster bereitzustellen. Die YAML-Dateien können angepasst und für die Bereitstellung der App in AKS verwendet werden.

> [!NOTE]
> Das App-Containerisierungstool von Azure Migrate ermittelt die App-Typen (ASP.NET- und Java-Web-Apps unter Apache Tomcat) und die App-Komponenten auf einem Anwendungsserver. Verwenden Sie die Ermittlungs- und Bewertungsfunktion von Azure Migrate, um Server sowie den Bestand an Apps, Rollen und Features zu ermitteln, die auf lokalen Computern ausgeführt werden. [Weitere Informationen](./tutorial-discover-vmware.md)

Nicht alle Apps profitieren von einer Umstellung auf Container ohne erhebliche Überarbeitungen. Unter anderem ergeben sich durch die Verschiebung vorhandener Apps in Container ohne Überarbeitung jedoch folgende Vorteile:

- **Verbesserte Infrastrukturauslastung:** Mithilfe von Containern können mehrere Apps dieselben Ressourcen nutzen und in derselben Infrastruktur gehostet werden. Dadurch können Sie die Infrastruktur konsolidieren und die Auslastung verbessern.
- **Vereinfachte Verwaltung:** Indem Sie Ihre Anwendungen auf einer modernen verwalteten Plattform wie AKS und App Service hosten, können Sie Ihre Verwaltungspraktiken vereinfachen. Das können Sie erreichen, indem Sie Wartungs- und Verwaltungsvorgänge für die Infrastruktur einstellen oder reduzieren, die Sie für Ihre eigene Infrastruktur normalerweise durchführen würden.
- **Anwendungsportabilität:** Durch die zunehmende Nutzung und Standardisierung von Containerspezifikationsformaten und -plattformen stellt die Anwendungsportabilität keine Herausforderungen mehr dar.
- **Moderne Verwaltung mit DevOps**: Dies ermöglicht Ihnen die Einführung und Standardisierung moderner Verwaltungs- und Sicherheitsverfahren sowie den Übergang zu DevOps.


In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten eines Azure-Kontos
> * Installieren des App-Containerisierungstools von Azure Migrate
> * Erkunden Ihrer ASP.NET-Anwendung
> * Erstellen des Containerimages
> * Bereitstellen der containerisierten Anwendung in AKS

> [!NOTE]
> In den Tutorials wird der einfachste Bereitstellungspfad für ein Szenario erläutert, damit Sie schnell einen Proof of Concept einrichten können. Die Tutorials verwenden nach Möglichkeit Standardoptionen und zeigen nicht alle möglichen Einstellungen und Pfade.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, sollten folgende Voraussetzungen erfüllt sein:

**Anforderung** | **Details**
--- | ---
**Computer für die Installation des Tools** | Sie benötigen einen Windows-Computer, auf dem das App-Containerisierungstool von Azure Migrate installiert und ausgeführt werden kann. Auf diesem Windows-Computer kann ein Server- (Windows Server 2016 oder höher) oder ein Clientbetriebssystem (Windows 10) ausgeführt werden. Das Tool ist also für Desktopcomputer geeignet. <br/><br/> Der Windows-Computer, auf dem das Tool ausgeführt wird, sollte über eine Netzwerkverbindung zu den Servern bzw. VMs verfügen, auf denen die ASP.NET-Apps gehostet werden, die containerisiert werden sollen.<br/><br/> Auf dem Windows-Computer, auf dem das App-Containerisierungstool von Azure Migrate ausgeführt wird, muss mindestens 6 GB freier Speicherplatz für die Anwendungsartefakte vorhanden sein. <br/><br/> Der Windows-Computer sollte direkt oder über einen Proxy auf das Internet zugreifen können. <br/> <br/>Installieren Sie (falls noch nicht geschehen) das Microsoft Web Deploy-Tool auf dem Computer, auf dem das App-Containerisierungstool und der Anwendungsserver ausgeführt werden. Sie können das Tool [hier](https://aka.ms/webdeploy3.6) herunterladen.
**Anwendungsserver** | Aktivieren Sie das PowerShell-Remoting auf den Anwendungsservern: Melden Sie sich beim Anwendungsserver an, und befolgen Sie [diese](/powershell/module/microsoft.powershell.core/enable-psremoting) Anweisungen, um das PowerShell-Remoting zu aktivieren. <br/><br/> Wenn auf dem Anwendungsserver Windows Server 2008 R2 ausgeführt wird, stellen Sie sicher, dass PowerShell 5.1 auf dem Anwendungsserver installiert ist. Befolgen Sie [dieser Anweisung](/powershell/scripting/windows-powershell/wmf/setup/install-configure), um PowerShell 5.1 auf dem Anwendungsserver herunterzuladen und zu installieren. <br/><br/> Installieren Sie (falls noch nicht geschehen) das Microsoft Web Deploy-Tool auf dem Computer, auf dem das App-Containerisierungstool und der Anwendungsserver ausgeführt werden. Sie können das Tool [hier](https://aka.ms/webdeploy3.6) herunterladen.
**ASP.NET-Anwendung** | Das Tool unterstützt zurzeit: <br/><br/> – ASP.NET-Anwendungen, die Microsoft .NET Framework 3.5 oder höher verwenden<br/> – Anwendungsserver, auf denen Windows Server 2008 R2 oder höher ausgeführt wird (auf Anwendungsservern muss PowerShell Version 5.1 ausgeführt werden) <br/> – Anwendungen, die auf Internetinformationsdiensten Version 7.5 (IIS) oder höher ausgeführt werden <br/><br/> Das Tool unterstützt zurzeit nicht: <br/><br/> – Anwendungen, die eine Windows-Authentifizierung erfordern (gMSA wird derzeit nicht von AKS unterstützt) <br/> – Anwendungen, die von anderen Windows-Diensten abhängen, die außerhalb von IIS gehostet werden


## <a name="prepare-an-azure-user-account"></a>Vorbereiten eines Azure-Benutzerkontos

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, bevor Sie beginnen.

Sobald Ihr Abonnement eingerichtet ist, benötigen Sie ein Azure-Benutzerkonto mit den folgenden Berechtigungen:
- Besitzerberechtigungen für das Azure-Abonnement
- Berechtigungen zum Registrieren von Azure Active Directory-Apps

Wenn Sie gerade erst ein kostenloses Azure-Konto erstellt haben, sind Sie der Besitzer Ihres Abonnements. Wenn Sie nicht der Besitzer des Abonnements sind, müssen Sie mit dem Besitzer zusammenarbeiten, um die Berechtigungen wie folgt zuzuweisen:

1. Suchen Sie im Azure-Portal nach „Abonnements“, und wählen Sie unter **Dienste** die Option **Abonnements** aus.

    ![Suchfeld, mit dem nach dem Azure-Abonnement gesucht wird](./media/tutorial-discover-vmware/search-subscription.png)

2. Wählen Sie auf der Seite **Abonnements** das Abonnement aus, in dem Sie ein Azure Migrate-Projekt erstellen möchten.
3. Wählen Sie im Abonnement die Option **Zugriffssteuerung (IAM)**  > **Zugriff überprüfen** aus.
4. Suchen Sie unter **Zugriff überprüfen** nach dem entsprechenden Benutzerkonto.
5. Klicken Sie unter **Rollenzuweisung hinzufügen** auf **Hinzufügen**.

    ![Suche nach einem Benutzerkonto, um den Zugriff zu überprüfen und eine Rolle zuzuweisen](./media/tutorial-discover-vmware/azure-account-access.png)

6. Wählen Sie unter **Rollenzuweisung hinzufügen** die Rolle „Besitzer“ und das Konto aus (in diesem Beispiel „azmigrateuser“). Klicken Sie anschließend auf **Speichern**.

    ![Die Seite „Rollenzuweisung“ wird geöffnet, auf der Sie dem Konto eine Rolle zuweisen können.](./media/tutorial-discover-vmware/assign-role.png)

7. Ihr Azure-Konto benötigt zudem **Berechtigungen zum Registrieren von Azure Active Directory-Apps**.
8. Navigieren Sie im Azure-Portal zu **Azure Active Directory** > **Benutzer** > **Benutzereinstellungen**.
9. Vergewissern Sie sich unter **Benutzereinstellungen**, dass Azure AD-Benutzer Anwendungen registrieren können (standardmäßig auf **Ja** festgelegt).

      ![Überprüfung in den Benutzereinstellungen, ob Benutzer Active Directory-Apps registrieren können](./media/tutorial-discover-vmware/register-apps.png)

10. Wenn die Einstellungen für „App-Registrierungen“ auf „Nein“ festgelegt sind, fordern Sie den Mandantenadministrator/globalen Administrator auf, die erforderliche Berechtigung zuzuweisen. Alternativ kann der Mandantenadministrator bzw. der globale Administrator einem Konto die Rolle **Anwendungsentwickler** zuweisen, um die Registrierung von Azure Active Directory-Apps zuzulassen. [Weitere Informationen](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

## <a name="download-and-install-azure-migrate-app-containerization-tool"></a>Herunterladen und Installieren des App-Containerisierungstools von Azure Migrate

1. Laden Sie das [Installationsprogramm](https://go.microsoft.com/fwlink/?linkid=2134571) für das App-Containerisierungstool von Azure Migrate auf einen Windows-Computer herunter.
2. Starten Sie PowerShell im Administratormodus, und ändern Sie das PowerShell-Verzeichnis in den Ordner, der das Installationsprogramm enthält.
3. Führen Sie das Installationsskript mithilfe des folgenden Befehls aus:

   ```powershell
   .\AppContainerizationInstaller.ps1
   ```

## <a name="launch-the-app-containerization-tool"></a>Starten des App-Containerisierungstools

1. Öffnen Sie auf einem Computer, der eine Verbindung mit dem Windows-Computer herstellen kann, auf dem das Anwendungscontainerisierungstool ausgeführt wird, in einem Browser die URL für das Tool: **https://*Computername oder IP-Adresse* 44369**.

   Alternativ können Sie auch auf dem Desktop auf die App-Verknüpfung klicken, um die App zu öffnen.

2. Wenn eine Warnung angezeigt wird, dass die Verbindung nicht privat ist, klicken Sie auf „Erweitert“ und wählen Sie die Option aus, dennoch zur Website zu wechseln. Diese Warnung wird angezeigt, da die Webschnittstelle ein selbstsigniertes TLS/SSL-Zertifikat verwendet.
3. Verwenden Sie auf dem Anmeldebildschirm das lokale Administratorkonto um sich auf dem Computer anzumelden.
4. Wählen Sie **ASP.NET Web-Apps** als den Anwendungstyp aus, den Sie containerisieren möchten.
5. Für den Azure-Zieldienst wählen Sie **Container in Azure Kubernetes Service** aus.

    ![Standard-Load-up für das App-Containerisierungstool](./media/tutorial-containerize-apps-aks/tool-home.png)

### <a name="complete-tool-pre-requisites"></a>Erfüllen der Voraussetzungen für das Tool
1. Akzeptieren Sie die **Lizenzbedingungen**, und lesen Sie die Drittanbieterinformationen.
6. Gehen Sie in der Web-App für das Tool unter **Erforderliche Komponenten einrichten** wie folgt vor:
   - **Konnektivität**: Das Tool überprüft, ob der Windows-Computer über Internetzugriff verfügt. Falls der Computer einen Proxy verwendet:
     - Klicken Sie auf **Proxy einrichten**, um die Proxyadresse (im IP-Adressformat oder als FQDN) und den lauschenden Port anzugeben.
     - Geben Sie die Anmeldeinformationen an, wenn der Proxy eine Authentifizierung erfordert.
     - Es werden nur HTTP-Proxys unterstützt.
     - Wenn Sie Proxydetails hinzugefügt oder den Proxy und/oder die Authentifizierung deaktiviert haben, klicken Sie auf **Speichern**, um die Konnektivitätsprüfung noch mal auszulösen.
   - **Updates installieren**: Das Tool sucht automatisch nach den aktuellsten Updates und installiert diese. Sie können die aktuelle Version des Tools auch [hier](https://go.microsoft.com/fwlink/?linkid=2134571) herunterladen und manuell installieren.
   - **Microsoft Web Deploy-Tool erstellen:** Das Tool überprüft, dass das Microsoft Web Deploy-Tool auf dem Windows-Computer installiert ist, der Azure Migrate ausführt (App-Containerisierungstool).
   - **PowerShell-Remoting aktivieren:** Das Tool erinnert Sie daran, sicherzustellen, dass das PowerShell-Remoting auf den Anwendungsservern aktiviert ist, die ASP.NET-Anwendungen ausführen, die containerisiert werden sollen.


## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Klicken Sie auf **Anmelden**, um sich bei Ihrem Azure-Konto anzumelden.

1. Für die Authentifizierung bei Azure benötigen Sie einen Gerätecode. Wenn Sie auf „Anmelden“ klicken, wird ein modales Dialogfeld mit dem Gerätecode angezeigt.
2. Klicken Sie auf **Code kopieren und anmelden**, um den Gerätecode zu kopieren und eine Azure-Anmeldeaufforderung auf einer neuen Registerkarte im Browser zu öffnen. Sollte keine Aufforderung angezeigt werden, vergewissern Sie sich, dass Sie den Popupblocker im Browser deaktiviert haben.

    ![Modales Dialogfeld mit Gerätecode](./media/tutorial-containerize-apps-aks/login-modal.png)

3. Fügen Sie auf der neuen Registerkarte den Gerätecode ein, und melden Sie sich mit den Anmeldeinformationen für Ihr Azure-Konto an. Sobald Sie angemeldet sind, können Sie die Registerkarte im Browser schließen und zur Weboberfläche des App-Containerisierungstools zurückkehren.
4. Wählen Sie den **Azure-Mandanten** aus, den Sie verwenden möchten.
5. Geben Sie das gewünschte **Azure-Abonnement** an.

## <a name="discover-aspnet-applications"></a>Erkunden von ASP.NET-Anwendungen

Das App-Containerisierungstool stellt unter Verwendung der angegebenen Anmeldeinformationen eine Remoteverbindung mit den Anwendungsservern her und versucht, ASP.NET-Anwendungen zu ermitteln, die auf den Anwendungsservern gehostet werden.

1. Geben Sie die **IP-Adresse oder den FQDN und die Anmeldeinformationen** für den Server an, auf dem die ASP.NET-Anwendung ausgeführt wird. Diese Daten sollen verwendet werden, um eine Remoteverbindung mit dem Server zur Anwendungsermittlung herzustellen.
    - Die angegebenen Anmeldeinformationen müssen für einen lokalen Administrator (Windows) auf dem Anwendungsserver angegeben werden.
    - Bei Domänenkonten (der Benutzer muss Administrator auf dem Anwendungsserver sein) müssen Sie dem Domänennamen den Benutzernamen voranstellen ( *<Domäne\Benutzername>* ).
    - Sie können die Anwendungsermittlung für bis zu fünf Server gleichzeitig ausführen.

2. Klicken Sie auf **Überprüfen**, um sich zu vergewissern, dass der Anwendungsserver von dem Computer aus erreichbar ist, auf dem das Tool ausgeführt wird, und dass die Anmeldeinformationen gültig sind. Bei erfolgreicher Überprüfung wird der Status in der entsprechenden Spalte als **Zugeordnet** angezeigt.  

    ![Screenshot: IP-Adresse und Anmeldeinformationen für den Server](./media/tutorial-containerize-apps-aks/discovery-credentials-asp.png)

3. Klicken Sie auf **Weiter**, um die Anwendungsermittlung auf den ausgewählten Anwendungsservern zu starten.

4. Nach erfolgreichem Abschluss der Anwendungsermittlung können Sie die Liste der zu containerisierenden Anwendungen auswählen.

    ![Screenshot der ermittelten ASP.NET-Anwendung](./media/tutorial-containerize-apps-aks/discovered-app-asp.png)


4. Verwenden Sie das Kontrollkästchen, um die zu containerisierenden Anwendungen auszuwählen.
5. **Containername angeben**: Legen Sie Namen für die Zielcontainer der ausgewählten Anwendungen fest. Der Containername sollte im Format <*Name:Tag*> angegeben werden, wobei „Tag“ für das Containerimage verwendet wird. Sie können den Zielcontainernamen beispielsweise auf *appname:v1* festlegen.   

### <a name="parameterize-application-configurations"></a>Parametrisieren von Anwendungskonfigurationen
Durch das Parametrisieren der Konfiguration steht diese zur Bereitstellungszeit als Parameter zur Verfügung. Das bedeutet, dass Sie diese Einstellung während der Anwendungsbereitstellung konfigurieren können, anstatt sie in einem bestimmten Wert im Containerimage hartzucodieren. Diese Option ist beispielsweise für Parameter wie Datenbank-Verbindungszeichenfolgen nützlich.
1. Klicken Sie auf **App-Konfigurationen**, um erkannte Konfigurationen anzuzeigen.
2. Aktivieren Sie das Kontrollkästchen, um die erkannten Anwendungskonfigurationen zu parametrisieren.
3. Wählen Sie die zu parametrisierenden Konfigurationen aus, und klicken Sie dann auf **Anwenden**.

   ![Screenshot: Parametrisierung der Konfiguration einer ASP.NET-App](./media/tutorial-containerize-apps-aks/discovered-app-configs-asp.png)

### <a name="externalize-file-system-dependencies"></a>Externalisieren von Dateisystemabhängigkeiten

 Sie können weitere Ordner hinzufügen, die von der Anwendung verwendet werden. Legen Sie fest, ob diese Teil des Containerimages werden oder mithilfe persistenter Volumes in Azure-Dateifreigabe externalisiert werden sollen. Persistente Volumes sind für zustandsbehaftete Anwendungen ideal, die den Zustand außerhalb des Containers speichern oder statische Inhalte im Dateisystem gespeichert haben. [Weitere Informationen](../aks/concepts-storage.md)

1. Klicken Sie unter „App-Ordner“ auf **Bearbeiten**, um die erkannten Anwendungsordner anzuzeigen. Bei den erkannten Anwendungsordnern handelt es sich um für die Anwendung erforderliche Artefakte, deshalb werden sie ins Containerimage kopiert.

2. Klicken Sie auf **Ordner hinzufügen**, und geben Sie die Pfade der Ordner an, die hinzugefügt werden sollen.
3. Wenn Sie mehrere Ordner zum selben Volume hinzufügen möchten, müssen Sie diese mit einem Trennzeichen (`,`) trennen.
4. Wählen Sie **Persistentes Volume** als Speicheroption aus, wenn die Ordner außerhalb des Containers in einem persistenten Volume gespeichert werden sollen.
5. Klicken Sie nach der Überprüfung der Anwendungsordner auf **Speichern**.
   ![Screenshot: Speicherauswahl für App-Volumes](./media/tutorial-containerize-apps-aks/discovered-app-volumes-asp.png)

6. Klicken Sie auf **Weiter**, um zur Buildphase des Containerimages zu wechseln.

## <a name="build-container-image"></a>Erstellen des Containerimage


1. **Azure Container Registry-Instanz auswählen**: Wählen Sie über das Dropdownmenü eine [Azure Container Registry-Instanz](../container-registry/index.yml) aus, die zum Erstellen und Speichern der Containerimages für die App verwendet werden soll. Sie können eine vorhandene Azure Container Registry-Instanz wählen oder über die Option „Create new registry“ (Neue Registrierung erstellen) eine neue erstellen.

    ![Screenshot: ACR-Auswahl für die App](./media/tutorial-containerize-apps-aks/build-aspnet-app.png)


2. **Dockerfile überprüfen**: Das Dockerfile, das für die Erstellung der Containerimage für die ausgewählten Apps benötigt wird, wird zu Beginn des Buildschritts generiert. Klicken Sie auf **Überprüfen**, um das Dockerfile zu öffnen. Im Überprüfungsschritt können Sie auch sämtliche notwendigen Anpassungen am Dockerfile vornehmen und die Änderung speichern, bevor Sie den Buildprozess einleiten.

3. **Buildprozess auslösen**: Wählen Sie die Apps aus, für die Images erstellt werden sollen, und klicken Sie auf **Erstellen**. Wenn Sie auf „Erstellen“ klicken, werden die Containerimages für die einzelnen Apps erstellt. Das Tool überwacht den Buildstatus kontinuierlich. Wenn der Buildprozess abgeschlossen ist, können Sie mit dem nächsten Schritt fortfahren.

4. **Buildstatus nachverfolgen**: Sie können den Fortschritt des Buildschritts überwachen, indem Sie unter der Statusspalte auf **Buildvorgang wird ausgeführt** klicken. Der Link ist erst einige Minuten nach Beginn des Buildprozesses verfügbar.  

5. Wenn der Build abgeschlossen ist, klicken Sie auf **Weiter**, um die Bereitstellungseinstellungen festzulegen.

    ![Screenshot: Abschluss des Buildprozesses für App-Containerimages](./media/tutorial-containerize-apps-aks/build-aspnet-app-completed.png)

## <a name="deploy-the-containerized-app-on-aks"></a>Bereitstellen der containerisierten App in AKS

Nachdem das Containerimage erstellt wurde, müssen Sie die App als Container in [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) bereitstellen.

1. **Azure Kubernetes Service-Cluster auswählen**: Geben Sie den AKS-Cluster an, in dem die App bereitgestellt werden soll.

     - Der ausgewählte AKS-Cluster muss über einen Windows-Knotenpool verfügen.
     - Der Cluster muss so konfiguriert werden, dass Images aus der Azure Container Registry-Instanz gepullt werden können, in der diese gespeichert werden.
         - Führen Sie den folgenden Befehl in der Azure CLI aus, um den AKS-Cluster an die ACR-Instanz anzufügen.
           ``` Azure CLI
           az aks update -n <cluster-name> -g <cluster-resource-group> --attach-acr <acr-name>
           ```  
     - Wenn Sie keinen AKS-Cluster besitzen oder einen neuen AKS-Cluster erstellen möchten, in dem die App bereitgestellt werden soll, können Sie hierzu im Tool auf **Create new AKS cluster** (Neuen AKS-Cluster erstellen) klicken.      
          - Das Tool erstellt den AKS-Cluster innerhalb eines Windows-Knotenpools. Der Cluster wird so konfiguriert, dass Images aus der Azure Container Registry-Instanz gepullt werden können, die zuvor erstellt wurde (falls diese Option ausgewählt wurde).
     - Wählen Sie den AKS-Cluster aus, und klicken Sie auf **Weiter**.
2. **Das Angeben des Geheimnisspeichers**: Wenn Sie sich für die Parametrisierung von Anwendungskonfigurationen entschieden haben, geben Sie den geheimen Speicher an, der für die Anwendung verwendet werden soll. Sie können den Azure Key Vault oder die App Service Anwendungseinstellungen auswählen, um Ihre Anwendungsgeheimnisse zu verwalten. [Weitere Informationen](../app-service/configure-common.md#configure-connection-strings)

     - Wenn Sie App Service-Anwendungseinstellungen für die Verwaltung von Geheimnissen ausgewählt haben, klicken Sie auf **Weiter**.
     - Wenn Sie eine Azure Key Vault-Instanz zum Verwalten Ihrer Anwendungsgeheimnisse verwenden möchten, geben Sie die gewünschte Azure Key Vault-Instanz an.     
         - Wenn Sie über keinen Azure Key Vault verfügen oder einen neuen Key Vault erstellen möchten, können Sie ihn über das Tool erstellen, indem Sie auf die Funktion **Neuen Azure Key Vault erstellen** klicken.
         - Das Tool weist automatisch die erforderlichen Berechtigungen zum Verwalten von Geheimnissen über die Key Vault-Instanz zu.

3. **Azure-Dateifreigabe angeben**: Wenn Sie mehrere Ordner hinzugefügt und die Option „Persistentes Volume“ ausgewählt haben, geben Sie die Azure-Dateifreigabe an, die das App-Containerisierungstool von Azure Migrate während des Bereitstellungsprozesses verwenden soll. Das Tool erstellt neue Verzeichnisse in dieser Azure-Dateifreigabe, um die Anwendungsordner zu kopieren, die für persistenten Volumespeicher konfiguriert sind. Sobald die Anwendung bereitgestellt wurde, bereinigt das Tool die Azure-Dateifreigabe, indem die erstellten Verzeichnisse gelöscht werden.

     - Wenn Sie keine Azure-Dateifreigabe besitzen oder eine neue erstellen möchten, können Sie hierzu im Tool auf **Create new Storage Account and file share** (Neues Speicherkonto und neue Dateifreigabe erstellen) klicken.  

4. **Konfiguration für die Anwendungsbereitstellung**: Nachdem Sie die obigen Schritte abgeschlossen haben, müssen Sie die Bereitstellungskonfiguration für die App festlegen. Klicken Sie auf **Konfigurieren**, um die Bereitstellung der App anzupassen. Im Konfigurationsschritt können Sie die folgenden Anpassungen vornehmen:
     - **Präfixzeichenfolge**: Geben Sie eine Präfixzeichenfolge an, die dem Namen aller Ressourcen vorangestellt werden soll, die für die containerisierte Anwendung im AKS-Cluster erstellt werden.
     - **SSL-Zertifikat**: Wenn Ihre Anwendung eine HTTP-Sitebindung erfordert, geben Sie die PFX-Datei an, die das Zertifikat enthält, das für die Bindung verwendet werden soll. Die PFX-Datei sollte nicht kennwortgeschützt sein, und die ursprüngliche Site sollte nicht über mehrere Bindungen verfügen.
     - **Replikatgruppen**: Geben Sie an, wie viele Anwendungsinstanzen (Pods) innerhalb der Container ausgeführt werden sollen.
     - **Lastenausgleich**: Wählen Sie *Extern* aus, wenn die containerisierte Anwendung von öffentlichen Netzwerken aus erreichbar sein soll.
     - **Anwendungskonfiguration**: Geben Sie für parametrisierte Anwendungskonfigurationen die Werte an, die für die aktuelle Bereitstellung verwendet werden sollen.
     - **Speicher**: Machen Sie für sämtliche Anwendungsordner, die für den persistenten Volumespeicher konfiguriert wurden, eine Angabe, ob das Volume anwendungsinstanzübergreifend freigegeben oder einzeln mit jeder Instanz im Container initialisiert werden soll. Standardmäßig sind alle Anwendungsordner in persistenten Volumes als freigegeben konfiguriert.  
     - Klicken Sie auf **Anwenden**, um die Bereitstellungskonfiguration zu speichern.
     - Klicken Sie auf **Weiter**, um die App bereitzustellen.

    ![Screenshot: Bereitstellungskonfiguration für die App](./media/tutorial-containerize-apps-aks/deploy-aspnet-app-config-aks.png)

4. **App bereitstellen**: Sobald die Bereitstellungskonfiguration für die App gespeichert wurde, generiert das Tool die Kubernetes-Bereitstellungsdatei (YAML) für diese.
     - Klicken Sie auf **Bearbeiten**, um die Kubernetes-Bereitstellungsdatei (YAML) für die Anwendungen zu überprüfen und anzupassen.
     - Wählen Sie die bereitzustellende App aus.
     - Klicken Sie auf **Bereitstellen**, um mit der Bereitstellung der ausgewählten Apps zu beginnen.

         ![Screenshot: Bereitstellungskonfiguration für die App](./media/tutorial-containerize-apps-aks/deploy-aspnet-app-deploy-aks.png)

     - Nachdem die App bereitgestellt wurde, können Sie auf die Spalte *Bereitstellungsstatus* klicken, um die Ressourcen nachzuverfolgen, die für die App bereitgestellt wurden.

## <a name="download-generated-artifacts"></a>Herunterladen generierter Artefakte

Sämtliche für die Erstellung und Bereitstellung der App in AKS verwendeten Artefakte (einschließlich des Dockerfile und der YAML-Spezifikationsdateien für Kubernetes) werden auf dem Computer gespeichert, auf dem das Tool ausgeführt wird. Die Artefakte befinden sich unter *C:\ProgramData\Microsoft Azure Migrate App Containerization*.

Für jeden Anwendungsserver wird ein separater Ordner erstellt. Sie können alle im Containerisierungsprozess verwendeten Zwischenartefakte anzeigen und herunterladen, indem Sie zu diesem Ordner navigieren. Die Ordner für Anwendungsserver werden immer dann bereinigt, wenn das Tool für einen bestimmten Server gestartet wird.

## <a name="troubleshoot-issues"></a>Behandeln von Problemen

Wenn Sie Probleme mit dem Tool behandeln müssen, sollten Sie sich die Protokolldateien auf dem Windows-Computer ansehen, auf dem das App-Containerisierungstool ausgeführt wird. Die Protokolldateien für das Tool befinden sich im Ordner *C:\ProgramData\Microsoft Azure Migrate App Containerization\Logs*.

## <a name="next-steps"></a>Nächste Schritte

- Containerisieren von ASP.NET-Web-Apps und Bereitstellen dieser Apps in Windows-Containern in App Service. [Weitere Informationen](./tutorial-app-containerization-aspnet-app-service.md)
- Das Containerisieren von Java-Web-Apps unter Apache Tomcat (auf Linux-Servern) und das Bereitstellen dieser Apps in Linux-Containern in AKS. [Weitere Informationen](./tutorial-app-containerization-java-kubernetes.md)
- Containerisieren von Java-Web-Apps unter Apache Tomcat (auf Linux-Servern) und Bereitstellen dieser Apps in Linux-Containern in App Service. [Weitere Informationen](./tutorial-app-containerization-java-app-service.md)