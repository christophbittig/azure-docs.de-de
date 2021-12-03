---
title: Ermitteln von in einer VMware-Umgebung ausgeführten Servern mit der Ermittlung und Bewertung von Azure Migrate
description: Hier erfahren Sie, wie Sie in einer VMware-Umgebung lokale Server, Apps und Abhängigkeiten unter Verwendung des Azure Migrate-Tools zur Ermittlung und Bewertung entdecken.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 11/12/2021
ms.custom: mvc
ms.openlocfilehash: 7eb6aea743176bb4cdaf313d392320378bc016d1
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397495"
---
# <a name="tutorial-discover-servers-running-in-a-vmware-environment-with-azure-migrate"></a>Tutorial: Ermitteln von in einer VMware-Umgebung ausgeführten Servern mit Azure Migrate

Im Rahmen der Migration zu Azure ermitteln Sie den lokalen Bestand und die lokalen Workloads.

In diesem Tutorial wird gezeigt, wie Sie die in Ihrer VMware-Umgebung ausgeführten Server unter Verwendung des Azure Migrate-Tools zur Ermittlung und Bewertung und einer schlanken Azure Migrate-Appliance ermitteln. Sie stellen die Appliance als Server bereit, der in Ihrer vCenter Server-Instanz ausgeführt wird, um Server und deren Leistungsmetadaten, auf Servern ausgeführte Anwendungen, Serverabhängigkeiten, ASP.NET-Web-Apps und SQL Server-Instanzen und -Datenbanken kontinuierlich zu ermitteln.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Einrichten eines Azure-Kontos
> * Vorbereiten der VMware-Umgebung für die Ermittlung
> * Erstellt ein Projekt.
> * Einrichten der Azure Migrate-Appliance
> * Starten der kontinuierlichen Ermittlung

> [!NOTE]
> In den Tutorials wird der schnellste Weg zum Ausprobieren eines Szenarios veranschaulicht. Sie verwenden, wenn möglich, Standardoptionen.  

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Tutorial beginnen, überprüfen Sie, ob die folgenden Voraussetzungen erfüllt sind:

Anforderung | Details
--- | ---
**vCenter Server-/ESXi-Host** | Sie benötigen eine Instanz mit der vCenter Server-Version 6.7, 6.5, 6.0 oder 5.5.<br /><br /> Server müssen auf einem ESXi-Host mit einer Version ab 5.5 gehostet werden.<br /><br /> Lassen Sie in der vCenter Server-Instanz eingehende Verbindungen über den TCP-Port 443 zu, damit die Appliance Konfigurations- und Leistungsmetadaten sammeln kann.<br /><br /> Die Appliance stellt standardmäßig an Port 443 eine Verbindung mit vCenter Server her. Wenn der Server mit vCenter Server an einem anderen Port lauscht, können Sie den Port beim Angeben der vCenter Server-Details im Appliance-Konfigurations-Manager ändern.<br /><br /> Stellen Sie auf den ESXi-Hosts sicher, dass der eingehende Zugriff an TCP-Port 443 erlaubt ist, um die Ermittlung installierter Anwendungen und die Abhängigkeitsanalyse ohne Agent auf Servern durchzuführen.
**Azure Migrate-Appliance** | vCenter Server müssen über diese Ressourcen verfügen, um die Zuordnung zu einem Server zu ermöglichen, der die Azure Migrate hostet:<br /><br /> - 32 GB RAM, 8 vCPUs und etwa 80 GB Speicherplatz auf dem Datenträger.<br /><br /> - Einen externen virtuellen Switch und Internetzugriff für den Server auf der Appliance (direkt oder über einen Proxy).
**Server** | Für die Ermittlung von Konfigurations- und Leistungsmetadaten werden alle Windows- und Linux-Betriebssystemversionen unterstützt. <br /><br /> Zur Anwendungsermittlung auf Servern werden alle Windows- und Linux-Betriebssystemversionen unterstützt. Überprüfen Sie die [Betriebssystemversionen, die für die Abhängigkeitsanalyse ohne Agent unterstützt werden](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless).<br /><br /> Für die Ermittlung installierter Anwendungen und Abhängigkeitsanalyse ohne Agent müssen VMware Tools (ab 10.2.1) auf den Servern installiert sein und ausgeführt werden. Auf Windows-Servern muss PowerShell ab Version 2.0 installiert sein.<br /><br /> Um SQL Server-Instanzen und -Datenbanken zu ermitteln, überprüfen Sie die [unterstützten SQL Server- und Windows-Betriebssystem-Versionen und -Editionen](migrate-support-matrix-vmware.md#sql-server-instance-and-database-discovery-requirements) und Windows-Authentifizierungsmechanismen.<br /><br /> Informationen zum Ermitteln von ASP.NET-Web-Apps, die auf einem IIS-Webserver ausgeführt werden, finden Sie unter [Unterstützungsmatrix für die VMware-Bewertung](migrate-support-matrix-vmware.md#aspnet-web-apps-discovery-requirements).

## <a name="prepare-an-azure-user-account"></a>Vorbereiten eines Azure-Benutzerkontos

Um ein Projekt zu erstellen und die Azure Migrate zu registrieren, müssen Sie über ein Azure-Konto verfügen, das über die folgenden Berechtigungen verfügt:

- Berechtigungen vom Typ „Mitwirkender“ oder „Besitzer“ im Azure-Abonnement.
- Berechtigungen zum Registrieren von Azure Active Directory-Apps (Azure AD).
- Berechtigungen des Typs „Besitzer“ oder „Mitwirkender“ und Berechtigungen des Typs „Benutzerzugriffsadministrator“ für das Azure-Abonnement, um eine Azure Key Vault-Instanz zu erstellen, die zur Servermigration ohne Agent verwendet wird.

Wenn Sie ein kostenloses Azure-Konto erstellt haben, sind Sie standardmäßig der Besitzer Ihres Azure-Abonnements. Wenn Sie nicht der Besitzer des Abonnements sind, müssen Sie mit dem Besitzer zusammenarbeiten, um Berechtigungen zu erteilen.

Berechtigungen vom Typ „Mitwirkender“ oder „Besitzer“ im Azure-Abonnement einstellen:

1. Suchen Sie im Azure-Portal nach „Abonnements“. Wählen Sie in den Suchergebnissen unter **Dienste** die Option **Abonnements** aus.

    :::image type="content" source="./media/tutorial-discover-vmware/search-subscription.png" alt-text="Screenshot: Suchen nach einem Azure-Abonnement im Suchfeld.":::

1. Wählen Sie unter **Abonnements** das Abonnement aus, in dem Sie ein Projekt erstellen möchten.
1. Wählen Sie im linken Menü **Zugriffssteuerung (IAM)** aus.
1. Suchen Sie auf der Registerkarte **Zugriff überprüfen** unter **Zugriff überprüfen** nach dem Benutzerkonto, das Sie verwenden möchten.
1. Klicken Sie im Zuweisungsbereich **Rolle hinzufügen** auf **Hinzufügen**.

    :::image type="content" source="./media/tutorial-discover-vmware/azure-account-access.png" alt-text="Screenshot: Suchen nach einem Benutzerkonto zum Überprüfen des Zugriffs und Hinzufügen einer Rollenzuweisung.":::
    
1. Wählen Sie unter **Rollenzuweisung hinzufügen** die Rolle „Mitwirkender“ oder „Besitzer“ und dann das Konto aus. Wählen Sie **Speichern** aus.

    :::image type="content" source="./media/tutorial-discover-vmware/assign-role.png" alt-text="Screenshot: Seite „Rollenzuweisung“, auf der Sie dem Konto eine Rolle zuweisen können.":::

So erteilen Sie dem Konto die erforderlichen Berechtigungen zum Registrieren Azure AD-Apps:

1. Gehen Sie im Portal zu **Azure Active Directory** > **Benutzer** > **Benutzereinstellungen**.
1. Vergewissern Sie sich unter **Benutzereinstellungen**, dass Azure AD-Benutzer Anwendungen registrieren können (standardmäßig auf **Ja** festgelegt).

    :::image type="content" source="./media/tutorial-discover-vmware/register-apps.png" alt-text="Screenshot: Überprüfen der Benutzereinstellung zum Registrieren von Apps.":::

9. Wenn die **App-Registrierungen** auf **Nein** festgelegt sind, fordern Sie den Mandantenadministrator/globalen Administrator auf, die erforderliche Berechtigung zuzuweisen. Alternativ kann der Mandantenadministrator/globale Administrator einem Konto die Rolle Anwendungsentwickler zuweisen, um die Registrierung von Azure AD-Apps durch Benutzer zuzulassen. [Weitere Informationen](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)

## <a name="prepare-vmware"></a>Vorbereiten von VMware

Überprüfen Sie im vCenter Server, ob Ihr Konto über Berechtigungen zum Erstellen eines virtuellen Computers mithilfe einer VMware Open Virtualization Appliance(OVA)-Installationsdatei verfügt. Sie müssen über diese Berechtigungen verfügen, wenn Sie die Azure Migrate-Appliance unter Verwendung einer OVA-Datei als virtuellen VMware-Computer bereitstellen.

Azure Migrate muss über ein vCenter Server-Konto mit Lesezugriff für die Ermittlung und Bewertung von Servern verfügen, die in Ihrer VMware-Umgebung laufen. Wenn Sie auch die Ermittlung installierter Anwendungen und die Abhängigkeitsanalyse ohne Agent ausführen möchten, muss das Konto über in VMware aktivierte Berechtigungen für VM-Gastvorgänge verfügen.

### <a name="create-an-account-to-access-vcenter-server"></a>Erstellen eines Kontos für den Zugriff auf vCenter Server

Richten Sie im VMware vSphere-Webclient ein schreibgeschütztes Konto ein, das für vCenter Server verwendet wird:

1. Wählen Sie in einem Konto mit Administratorrechten im vSphere-Webclient im Menü **Start** die Option **Verwaltung** aus.
1. Wählen Sie unter **Single Sign-On** (Einmaliges Anmelden) die Option **User and Groups** (Benutzer und Gruppen) aus.
1. Unter **Benutzer** **Neuer Benutzer** auswählen.
1. Geben Sie die Kontoeigenschaften ein, und wählen Sie dann **OK** aus.
1. Wählen Sie im Menü unter **Verwaltung** unter **Zugriffssteuerung** die Option **Globale Berechtigungen** aus.
1. Wählen Sie das Benutzerkonto und dann **Schreibgeschützt** aus, um die Rolle dem Konto zuzuweisen. Wählen Sie **OK** aus.
1. Um die Ermittlung installierter Anwendungen und die Abhängigkeitsanalyse ohne Agent starten zu können, wählen Sie im Menü unter **Zugriffssteuerung** die Option **Rollen** aus. Wählen Sie im Bereich **Rollen** unter **Rollen** die Option **Schreibgeschützt** aus. Wählen Sie unter **Berechtigungen** die Option **Gastvorgänge** aus. Um die Berechtigungen für alle Objekte im vCenter Server weiterzugeben, aktivieren Sie das Kontrollkästchen **Propagate to children** (An untergeordnete Objekte weitergeben).

    :::image type="content" source="./media/tutorial-discover-vmware/guest-operations.png" alt-text="Screenshot: v Sphere-Webclient und Erstellen eines neuen Kontos und Auswählen von Benutzerrollen und Berechtigungen.":::

> [!NOTE]
> Sie können das vCenter Server-Konto einschränken, um die Erkennung auf bestimmte vCenter Server-Rechenzentren, Cluster, Hosts, Ordner von Clustern oder Hosts oder einzelne Server zu beschränken. Erfahren Sie mehr über das [Einschränken des vCenter Server-Benutzerkontos](set-discovery-scope.md).

> [!NOTE]
> vCenter-Ressourcen, die über Linked-Mode mit dem für die Ermittlung angegebenen vCenter-Server verbunden sind, werden von Azure Migrate nicht ermittelt. Eine Azure Migrate-Appliance muss für jede vCenter-Umgebung bereitgestellt werden, die Sie ermitteln möchten.

### <a name="create-an-account-to-access-servers"></a>Erstellen eines Kontos für den Zugriff auf Server

Ihr Benutzerkonto auf Ihren Servern muss über die erforderlichen Berechtigungen verfügen, um die Ermittlung installierter Anwendungen, die Abhängigkeitsanalyse ohne Agent sowie die Ermittlung von Web-Apps und SQL Server-Instanzen und -Datenbanken zu initiieren. Sie können die Benutzerkontoinformationen im Appliance-Konfigurations-Manager angeben. Die Appliance installiert keine Agents auf den Servern.

* Erstellen Sie für die Ermittlung von Servern unter Windows und Web-Apps ein Konto (lokales Konto oder Domänenkonto) mit Administratorberechtigungen für die Server. Um SQL Server Instanzen und -Datenbanken zu ermitteln, muss das Windows- oder SQL Server-Konto Mitglied der Serverrolle sysadmin sein. Erfahren Sie mehr darüber, wie Sie dem [Benutzerkonto die erforderliche Rolle zuweisen](/sql/relational-databases/security/authentication-access/server-level-roles).
* Geben Sie bei Linux-Servern die Details des Root-Benutzerkontos an, oder erstellen Sie ein Konto, das die Berechtigungen CAP_DAC_READ_SEARCH und CAP_SYS_PTRACE für die Dateien „/bin/netstat“ und „/bin/ls“ hat.

> [!NOTE]
> Sie können dem Konfigurations-Manager in der Azure Migrate-Appliance mehrere Serveranmeldeinformationen hinzufügen, um die Ermittlung installierter Anwendungen, die Abhängigkeitsanalyse ohne Agent sowie die Ermittlung von Web-Apps und SQL Server-Instanzen und -Datenbanken zu initiieren. Sie können Anmeldeinformationen für die Authentifizierung für mehrere Domänen, Windows (ohne Domäne), Linux (ohne Domäne) oder SQL Server angeben. Erfahren Sie mehr über das [Hinzufügen von Serveranmeldeinformationen](add-server-credentials.md).

## <a name="set-up-a-project"></a>Einrichten eines Projekts

Ein neues Projekt einrichten:

1. Klicken Sie im Azure-Portal auf **Alle Dienste** und suchen Sie nach **Azure Migrate**.
1. Wählen Sie unter **Dienste** die Option **Azure Migrate** aus.
1. Wählen Sie unter **Übersicht** basierend auf Ihren Migrationszielen eine der folgenden Optionen aus: **Server, Datenbanken und Web-Apps**, **SQL Server (ausschließlich)** oder **Weitere Szenarios erkunden**.
1. Wählen Sie **Projekt erstellen** aus.
1. Wählen Sie unter **Create project** (Projekt erstellen) Ihr Azure-Abonnement und die Ressourcengruppe aus. Falls noch keine vorhanden ist, erstellen Sie eine Ressourcengruppe.
1. Geben Sie unter **Projektdetails** den Projektnamen und die geografische Region an, in der Sie das Projekt erstellen möchten. Beachten Sie die unterstützten geografischen Regionen für [öffentliche Clouds](migrate-support-matrix.md#supported-geographies-public-cloud) und die [unterstützten geografischen Regionen Government Clouds](migrate-support-matrix.md#supported-geographies-azure-government).

    :::image type="content" source="./media/tutorial-discover-vmware/new-project.png" alt-text="Screenshot: Hinzufügen von Projektdetails für ein neues Azure Migrate-Projekt.":::

1. Klicken Sie auf **Erstellen**.
1. Warten Sie einige Minuten, bis das Projekt bereitgestellt wurde. Das Tool **Azure Migrate: Ermittlung und Bewertung** wird dem neuen Projekt standardmäßig hinzugefügt.

> [!NOTE]
> Wenn Sie bereits ein Projekt erstellt haben, können Sie dieses Projekt zum Registrieren zusätzlicher Appliances verwenden, um mehr Server zu ermitteln und zu bewerten. Erfahren Sie mehr über die [Verwaltung von Projekten](create-manage-projects.md#find-a-project).

## <a name="set-up-the-appliance"></a>Einrichten der Appliance

Für das Azure Migrate-Tool zur Ermittlung und Bewertung wird eine einfache Azure Migrate-Appliance verwendet. Die Appliance führt die Serverermittlung komplett durch und sendet Metadaten zur Serverkonfiguration und -leistung an Azure Migrate. Richten Sie die Appliance durch Bereitstellen einer OVA-Vorlage ein, die aus dem Projekt heruntergeladen werden kann.

> [!NOTE]
> Wenn Sie die Appliance nicht mithilfe der OVA-Vorlage einrichten können, können Sie für die Einrichtung ein PowerShell-Skript auf einem vorhandenen Windows Server 2016 ausführen. Erfahren Sie, wie Sie eine [Azure Migrate-Appliance mit PowerShell einrichten](deploy-appliance-script.md#set-up-the-appliance-for-vmware). <br/>
> Die Option zum Bereitstellen einer Appliance mithilfe einer OVA-Vorlage wird in der Azure Government-Cloud nicht unterstützt. [Hier](./deploy-appliance-script-government.md) erfahren Sie mehr über das Bereitstellen einer Appliance für die Azure Government-Cloud.

### <a name="deploy-by-using-an-ova-template"></a>Bereitstellen mithilfe einer OVA-Vorlage

Um die Appliance mithilfe einer OVA-Vorlage einzurichten, führen Sie die folgenden Schritte aus, die in diesem Abschnitt ausführlicher beschrieben werden:

1. Geben Sie einen Appliancenamen ein, und generieren Sie einen Projektschlüssel im Portal.
1. Laden Sie eine OVA-Vorlagendatei herunter und importieren Sie die Datei in vCenter Server. Vergewissern Sie sich, dass die OVA sicher ist.
1. Erstellen Sie die Appliance aus der OVA-Datei. Überprüfen Sie, ob die Appliance eine Verbindung mit Azure Migrate herstellen kann.
1. Führen Sie die Konfiguration der Appliance durch. 
1. Registrieren Sie die Appliance mithilfe des Projektschlüssels beim Projekt.

#### <a name="generate-the-project-key"></a>Generieren des Projektschlüssels

1. Wählen Sie unter **Migrationsziele** die Option **Server, Datenbanken und Web-Apps** > **Azure Migrate: Ermittlung und Bewertung** > **Ermitteln** aus.
1. Wählen Sie unter **Server ermitteln** die Option **Sind Ihre Server virtualisiert?** aus > **Ja, mit VMware vSphere-Hypervisor**.
1. Geben Sie unter **1: Projektschlüssel generieren** einen Namen für die Azure Migrate-Appliance ein, die Sie für die Ermittlung von Servern in der VMware-Umgebung einrichten. Für den Namen können bis zu 14 alphanumerische Zeichen angegeben werden.
1. Um mit dem Erstellen der erforderlichen Azure-Ressourcen zu beginnen, wählen Sie **Schlüssel generieren** aus. Schließen Sie den Bereich **Entdecken** nicht, während die Ressourcen erstellt werden.
1. Nach der erfolgreichen Erstellung der Azure-Ressourcen wird ein *Projektschlüssel* generiert.
1. Kopieren Sie den Schlüssel. Sie verwenden den Schlüssel zum Abschließen der Registrierung der Appliance, wenn Sie die Appliance konfigurieren.

#### <a name="download-the-ova-template"></a>Herunterladen der OVA-Vorlage

Wählen Sie in **2: Azure Migrate-Appliance herunterladen** die OVA-Datei aus, und klicken Sie auf **Herunterladen**.

##### <a name="verify-security"></a>Überprüfen der Sicherheit

Vergewissern Sie sich vor dem Bereitstellen der OVA-Datei, dass die Datei sicher ist:

1. Öffnen Sie auf dem Server, auf dem Sie die Datei heruntergeladen haben, mithilfe der Option **Als Administrator ausführen** ein Eingabeaufforderungsfenster.
1. Führen Sie den folgenden Befehl aus, um den Hash für die OVA-Datei zu generieren:
  
    ```bash
    C:\>CertUtil -HashFile <file_location> <hashing_agorithm>
    ```
   
    Ein Beispiel: `C:\>CertUtil -HashFile C:\Users\Administrator\Desktop\MicrosoftAzureMigration.ova SHA256`

1. Überprüfen Sie die aktuellen Applianceversionen und Hashwerte:

    - Öffentliche Azure-Cloud:
    
        **Algorithmus** | **Download** | **SHA256**
        --- | --- | ---
        VMware (11,9 GB) | [Aktuelle Version](https://go.microsoft.com/fwlink/?linkid=2140333) | 08e4fa3aa827a88b68d3b56306926773f75c3a8a47ba4296871cfcb6c9830ea8

    - Azure Government:
    
        **Algorithmus** | **Download** | **SHA256**
        --- | --- | ---
        VMware (85,8 MB) | [Aktuelle Version](https://go.microsoft.com/fwlink/?linkid=2140337) | 3C00F9EB54CC6C55E127EDE47DFA28CCCF752697377EB1C9F3435E75DA5AA029

#### <a name="create-the-appliance-server"></a>Erstellen des Servers auf der Appliance

Importieren Sie die heruntergeladene Datei, und erstellen Sie dann in der VMware-Umgebung einen Server:

1. Klicken Sie in der vSphere-Clientkonsole auf **Datei** > **OVF-Vorlage bereitstellen**.
1. Wählen sie im Assistenten für die Bereitstellung der OVF-Vorlage die Option **Quelle** und geben dann den Speicherort der OVA-Datei an.
1. Unter **Name**: Geben Sie einen Namen für die Notiz ein. Unter **Ort**: Wählen Sie das Bestandsobjekt aus, in dem der Server gehostet wird.
1. Unter **Host/Cluster**: Wählen Sie den Host oder Cluster, auf bzw. in dem der Server ausgeführt wird.
1. Unter **Speicher**: Geben Sie das Speicherziel des Servers an.
1. Unter **Datenträgerformat**: Geben Sie den Typ und die Größe des Datenträgers an.
1. Unter **Netzwerkzuordnung**: Geben Sie das Netzwerk an, mit dem der Server eine Verbindung herstellt. Das Netzwerk erfordert Internetkonnektivität, um Metadaten an Azure Migrate zu senden.
1. Überprüfen und bestätigen Sie die Einstellungen, und klicken Sie auf **Fertig stellen**.

#### <a name="verify-appliance-access-to-azure"></a>Überprüfen des Appliancezugriffs auf Azure

Stellen Sie sicher, dass der Server auf der Appliance eine Verbindung mit Azure-URLs für [öffentliche](migrate-appliance.md#public-cloud-urls) und [Government](migrate-appliance.md#government-cloud-urls)-Clouds herstellen kann.

### <a name="configure-the-appliance"></a>Konfigurieren der Appliance

Führen Sie die Ersteinrichtung der Appliance durch:

> [!NOTE]
> Wenn Sie die Appliance mithilfe eines [PowerShell-Skripts](deploy-appliance-script.md) und nicht mithilfe der heruntergeladenen OVA-Datei einrichten, können Sie die ersten beiden Schritte in diesem Verfahren überspringen.

1. Klicken Sie im vSphere-Client mit der rechten Maustaste auf den Server, und wählen Sie **Konsole öffnen** aus.
1. Wählen oder geben Sie die Sprache, die Zeitzone und das Kennwort für die Appliance an.
1. Öffnen Sie einen Browser auf einem beliebigen Computer, der eine Verbindung zur Appliance herstellen kann. Öffnen Sie dann die URL des Appliance-Konfigurationsmanagers: `https://appliance name or IP address: 44368`.

     Sie können den Konfigurationsmanager auch auf dem Desktop des Servers auf der Appliance öffnen, indem Sie die Verknüpfung für den Konfigurations-Manager auswählen.
1. Akzeptieren Sie die Lizenzbedingungen und lesen Sie die Drittanbieterinformationen.
1. Wählen Sie im Konfigurationsmanager **die Option Voraussetzungen einrichten** aus und führen Sie dann die folgenden Schritte aus:
    1. **Konnektivität**: Die Appliance überprüft, ob der Server über Internetzugriff verfügt. Wenn der Server einen Proxy verwendet:
        1. Wählen **Sie Proxy einrichten** aus, um die Proxyadresse (im Formular `http://ProxyIPAddress` oder `http://ProxyFQDN`, wobei der *FQDN* auf einen *vollqualifizierten Domänennamen* verweist) und den Lauschport anzugeben.
        1.  Geben Sie die Anmeldeinformationen ein, wenn der Proxy eine Authentifizierung erfordert.
        1. Wenn Sie Proxydetails hinzugefügt oder den Proxy oder die Authentifizierung deaktiviert haben, wählen Sie **Speichern**, um die Konnektivitätsprüfung auszulösen und erneut zu überprüfen.

            Es werden nur HTTP-Proxys unterstützt.
    1. **Uhrzeitsynchronisierung**: Überprüfen Sie, ob die Uhrzeit der Appliance mit der Internetzeit synchronisiert ist, damit die Ermittlung ordnungsgemäß funktioniert.
    1. **Updates installieren**: Die Appliance stellt sicher, dass die neuesten Updates installiert sind. Wenn die Überprüfung fertig ist, können Sie **Appliancedienste anzeigen** auswählen, um den Status und die Versionen der auf dem Applianceserver ausgeführten Dienste anzuzeigen.
    1. **Install the VDDK** (VDDK installieren): Die Appliance überprüft, ob das VMware vSphere-VDDK (Virtual Disk Development Kit) installiert ist. Wenn das VDDK nicht installiert ist, laden Sie VDDK 6.7 von VMware herunter. Extrahieren Sie den Inhalt der heruntergeladenen ZIP-Datei am angegebenen Ort auf der Appliance, wie unter *Installationsanweisungen* beschrieben.

        Bei der Servermigration mit Azure Migrate wird das VDDK zur Replikation von Servern während der Migration zu Azure verwendet. 
1. Während der Konfiguration der Appliance können Sie jederzeit die *Voraussetzungen erneut ausführen*, um zu prüfen, ob die Appliance alle Voraussetzungen erfüllt:

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-prerequisites.png" alt-text="Screenshot: Einrichten der erforderlichen Komponenten im Appliance-Konfigurationsmanager.":::

#### <a name="register-the-appliance-with-azure-migrate"></a>Registrieren der Appliance bei Azure Migrate

1. Fügen Sie den aus dem Portal kopierten Projektschlüssel ein. Wenn Sie nicht über den Schlüssel verfügen, wechseln Sie zu **Azure Migrate:Ermittlung und Bewertung**  > **Ermittlung** > **Verwaltung vorhandener Appliances**. Wählen Sie den Appliancenamen aus, den Sie beim Erstellen des Projektschlüssels angegeben haben, und kopieren Sie dann den angezeigten Schlüssel.
1. Für die Authentifizierung bei Azure benötigen Sie einen Gerätecode. Wählen Sie **Anmelden**. Bei **Forfahren mit Azure Login** wählen Sie **Code kopieren und anmelden** aus, um den Gerätecode zu kopieren und eine Azure-Anmeldeaufforderung auf einer neuen Browserregisterkarte zu öffnen. Vergewissern Sie sich, dass Sie den Popupblocker im Browser deaktiviert haben, um die Eingabeaufforderung zu sehen.

    :::image type="content" source="./media/tutorial-discover-vmware/device-code.png" alt-text="Screenshot: Kopieren des Gerätecodes und Login.":::

1. Fügen Sie auf einer neuen Registerkarte in Ihrem Browser den Gerätecode ein, und melden Sie sich mit Ihrem Azure-Benutzernamen und dem zugehörigen Kennwort an. Die Anmeldung mit einer PIN wird nicht unterstützt.

    Wenn Sie die Registerkarte für die Anmeldung versehentlich schließen, ohne die Anmeldung durchzuführen, aktualisieren Sie die Browserregisterkarte des Appliance-Konfigurations-Managers, um den Gerätecode und die Schaltfläche **Code kopieren und anmelden** wieder anzuzeigen.
1. Kehren Sie nach erfolgreicher Anmeldung zur Browserregisterkarte zurück, auf der der Appliance-Konfigurationsmanager angezeigt wird. Wenn das Azure-Benutzerkonto, mit dem Sie sich angemeldet haben, über die erforderlichen Berechtigungen für die Azure-Ressourcen verfügt, die während der Schlüsselgenerierung erstellt wurden, wird die Applianceregistrierung gestartet.
1. Nachdem die Appliance erfolgreich registriert wurde, können Sie sich die Registrierungsdetails anzeigen lassen, indem Sie auf **Details anzeigen** klicken.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-registration.png" alt-text="Screenshot des Bereichs „Bei Azure Migrate registrieren“, der zeigt, dass die Appliance erfolgreich registriert wurde.":::

## <a name="start-continuous-discovery"></a>Starten der kontinuierlichen Ermittlung

Führen Sie die Setupschritte im Appliance-Konfigurationsmanager aus, um die Ermittlung vorzubereiten und zu starten.

### <a name="provide-vcenter-server-details"></a>Angeben von vCenter Server-Details

Die Appliance muss eine Verbindung mit der vCenter Server-Instanz herstellen, um die Konfigurations- und Leistungsdaten der Server zu ermitteln:

1. Wählen Sie unter **Schritt 1: Geben Sie vCenter Server Anmeldeinformationen an** aus und wählen Sie **Anmeldeinformationen hinzufügen** aus, um einen Namen für die Anmeldeinformationen einzugeben. Fügen Sie den Benutzernamen und das Kennwort für das vCenter Server-Konto hinzu, mit dem die Appliance Server ermittelt, die auf vCenter Server ausgeführt werden.
    - Sie sollten ein Konto mit den erforderlichen Berechtigungen festgelegt haben, wie weiter oben in diesem Artikel beschrieben.
    - Wenn Sie die Erkennung auf bestimmte VMware-Objekte (vCenter Server-Rechenzentren, Cluster, Hosts, Ordner von Clustern oder Hosts oder einzelne Server) beschränken möchten, lesen Sie die Anweisungen zum [Festlegen des Erkennungsbereichs](set-discovery-scope.md), um das von Azure Migrate verwendete Konto einzuschränken.
1. Unter **Schritt 2: Bereitstellen von vCenter Server-Details** wählen Sie **Erkennungsquelle hinzufügen**, um den Namen für die Anmeldeinformationen aus der Dropdown-Liste auszuwählen. Wählen Sie die IP-Adresse oder den FQDN des vCenter Server aus. Sie können den Port als Standard (443) beibehalten oder einen benutzerdefinierten Port angeben, an dem vCenter Server lauscht. Wählen Sie **Speichern** aus.
1. Die Appliance versucht, die Verbindung mit dem Server, auf dem vCenter Server ausgeführt wird, mithilfe der Anmeldeinformationen zu überprüfen. Der Überprüfungsstatus für die vCenter Server IP-Adresse oder den FQDN wird in der Tabelle mit den Anmeldeinformationen angezeigt.
1. Sie können die Verbindung zu vCenter Server jederzeit vor Beginn der Ermittlung *erneut überprüfen*.

    :::image type="content" source="./media/tutorial-discover-vmware/appliance-manage-sources.png" alt-text="Screenshot: Verwalten von Anmeldeinformationen und Ermittlungsquellen für vCenter Server im Appliance-Konfigurationsmanager.":::

### <a name="provide-server-credentials"></a>Angeben von Serveranmeldeinformation

In diesem Schritt können Sie mehrere Serveranmeldeinformationen angeben: **Schritt 3: Geben Sie Anmeldeinformationen für den Server an, um eine Softwareinventur, eine Abhängigkeitsanalyse ohne Agent, eine Ermittlung von SQL Server-Instanzen und -Datenbanken und eine Ermittlung von ASP.NET-Web-Apps in Ihrer VMware-Umgebung durchzuführen**. Wenn Sie keine dieser Appliancefunktionen verwenden möchten, können Sie diesen Schritt überspringen und mit der vCenter Server-Ermittlung fortfahren. Sie können diese Option jederzeit ändern.

:::image type="content" source="./media/tutorial-discover-vmware/appliance-server-credentials-mapping.png" alt-text="Screenshot: Bereitstellen von Anmeldeinformationen für die Softwareinventur, Abhängigkeitsanalyse und s q l Serverermittlung.":::

Wenn Sie diese Features nutzen möchten, stellen Sie die Serveranmeldeinformationen bereit, indem Sie die folgenden Schritte ausführen. Die Appliance versucht, die Anmeldeinformationen automatisch den Servern zuzuordnen, um die Ermittlungsfeatures auszuführen.

Hinzufügen von Serveranmeldeinformationen:

1. Wählen Sie **Add Credentials** (Anmeldeinformationen hinzufügen) aus.
1. Wählen Sie im Dropdown-Menü **Anmeldeinformationstyp** aus.
    
    Sie können Anmeldeinformationen für die Authentifizierung für eine Domäne/, Windows (ohne Domäne)/, Linux (ohne Domäne)/ und SQL Server angeben. Erfahren Sie mehr über die [Bereitstellung von Anmeldeinformationen](add-server-credentials.md) und zu deren Handhabung.
1. Geben Sie für jeden Anmeldeinformationstyp Folgendes ein:
    * Einen Anzeigenamen.
    * Einen Benutzernamen.
    * Ein Kennwort.
    Wählen Sie **Speichern** aus.

    Wenn Sie Domänenanmeldeinformationen zu Verwendung wählen, müssen Sie auch den FQDN für die Domäne eingeben. Der FQDN wird benötigt, um die Echtheit der Anmeldeinformationen mithilfe der Active Directory-Instanz der jeweiligen Domäne zu bestätigen.
1. Überprüfen Sie die [erforderlichen Berechtigungen](add-server-credentials.md#required-permissions) des Kontos für die Ermittlung installierter Anwendungen, die Abhängigkeitsanalyse ohne Agent sowie für die Ermittlung von Web-Apps und SQL Server-Instanzen und -Datenbanken.
1. Um mehrere Anmeldeinformationen gleichzeitig hinzuzufügen, wählen Sie **Weitere hinzufügen**, um die Anmeldeinformationen zu speichern und dann weitere Anmeldeinformationen hinzuzufügen.
    Wenn Sie **Speichern** oder **Weitere hinzufügen** auswählen, überprüft die Appliance die Domänenanmeldeinformationen mit dem Active Directory-Instanz der Domäne auf Authentifizierung. Die Überprüfung erfolgt nach jeder Addition, um Kontosperren zu vermeiden, während die Appliance die Zuordnung von Anmeldeinformationen zu den jeweiligen Servern durchlauft.

So überprüfen Sie die Überprüfung der Domänenanmeldeinformationen:

Sehen Sie sich im Konfigurationsmanager in der Tabelle mit den Anmeldeinformationen den **Überprüfungsstatus** für Domänenanmeldeinformationen an. Nur Domänenanmeldeinformationen werden überprüft.

Wenn die Überprüfung fehlschlägt, können Sie den Status **Fehler** auswählen, um den Validierungsfehler anzuzeigen. Beheben Sie das Problem und wählen Sie dann **Anmeldeinformationen erneut überprüfen** aus, um die Überprüfung der Anmeldeinformationen erneut zu überprüfen.

:::image type="content" source="./media/tutorial-discover-vmware/add-server-credentials-multiple.png" alt-text="Screenshot: Bereitstellen und Überprüfen mehrerer Anmeldeinformationen.":::

### <a name="start-discovery"></a>Ermittlung starten

Wählen Sie **Ermittlung starten** aus, um die vCenter Server-Ermittlung zu starten. Nachdem die Ermittlung erfolgreich gestartet wurde, können Sie den Ermittlungsstatus anhand von IP-Adresse oder FQDN von vCenter Server in der Tabelle mit den Quellen überprüfen.

## <a name="how-discovery-works"></a>Wie die Ermittlung funktioniert

* Die Inventur benötigt ca. 15 Minuten, bis Metadaten des ermittelten Servers im Azure-Portal angezeigt werden.
* Wenn Sie Serveranmeldeinformationen angegeben haben, wird die Software-Inventur (Ermittlung installierter Anwendungen) automatisch eingeleitet, wenn die vCenter Server-Ermittlung abgeschlossen wurde. Die Software-Inventur erfolgt einmal alle 12 Stunden.
* [Die Softwareinventur](how-to-discover-applications.md) identifiziert SQL Server-Instanzen, die auf den Servern ausgeführt werden. Anhand dieser gesammelten Informationen versucht die Appliance, mithilfe der in der Appliance angegebenen Anmeldeinformationen für Windows-Authentifizierung oder SQL Server-Authentifizierung eine Verbindung mit den entsprechenden SQL Server-Instanzen herzustellen. Anschließend werden Daten zu SQL Server-Datenbanken und deren Eigenschaften erfasst. Die SQL Server-Ermittlung erfolgt einmal alle 24 Stunden.
* Die Appliance kann nur mit den SQL Server-Instanzen verbunden werden, mit denen sie über eine direkte Verbindung verfügt. Für die Softwareinventur selbst ist möglicherweise keine direkte Netzwerkverbindung erforderlich.
* Die Ermittlung installierter Anwendungen kann länger als 15 Minuten dauern. Die Dauer hängt von der Anzahl der ermittelten Server ab. Bei 500 Servern dauert es ungefähr ein Stunde, bis der ermittelte Bestand im Azure Migrate-Portal angezeigt wird.
* Bei der [Softwareinventur](how-to-discover-applications.md) wird die auf ermittelten Servern vorhandene Webserverrolle identifiziert. Wenn für einen Server die Webserverrolle aktiviert ist, führt Azure Migrate die Ermittlung von Web-Apps auf dem Server durch. Konfigurationsdaten von Web-Apps werden alle 24 Stunden aktualisiert.
* Bei der Softwareinventur werden die hinzugefügten Serveranmeldeinformationen mit Servern abgeglichen und für die Abhängigkeitsanalyse ohne Agent überprüft. Wenn die Ermittlung von Servern abgeschlossen ist, können Sie im Portal die Abhängigkeitsanalyse ohne Agent auf den Servern aktivieren. Nur die Server mit erfolgreicher Überprüfung können ausgewählt werden, um die [Abhängigkeitsanalyse ohne Agent](how-to-create-group-machine-dependencies-agentless.md) zu aktivieren.
* Die Daten von ASP.NET-Web-Apps und SQL Server-Instanzen und -Datenbanken werden innerhalb von 24 Stunden nach Beginn der Ermittlung im Portal angezeigt.
* Standardmäßig verwendet Azure Migrate für Verbindungen mit SQL-Instanzen die sicherste Methode, d. h., Azure Migrate verschlüsselt die Kommunikation zwischen der Azure Migrate-Appliance und den SQL Server-Quellinstanzen, indem die TrustServerCertificate-Eigenschaft auf `true` festgelegt wird. Darüber hinaus verwendet die Transportschicht SSL zum Verschlüsseln des Kanals und Umgehen der Zertifikatkette zur Überprüfung der Vertrauenswürdigkeit. Daher muss der Server der Appliance so eingerichtet sein, dass er die Stammzertifizierungsstelle des Zertifikats als vertrauenswürdig einstuft. Sie können die Verbindungseinstellungen jedoch ändern, indem Sie in der Appliance **Edit SQL Server connection properties** (SQL Server-Verbindungseigenschaften bearbeiten) auswählen. [Hier erfahren Sie mehr](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine), um zu verstehen, was Sie auswählen müssen.

    :::image type="content" source="./media/tutorial-discover-vmware/sql-connection-properties.png" alt-text="Screenshot der Bearbeitung der SQL Server-Verbindungseigenschaften":::

Wählen Sie **Ermittlung starten** aus, um die vCenter Server-Ermittlung zu starten. Nachdem die Ermittlung erfolgreich gestartet wurde, können Sie den Ermittlungsstatus anhand von IP-Adresse oder FQDN von vCenter Server in der Tabelle mit den Quellen überprüfen.

### <a name="view-discovered-data"></a>Anzeigen der ermittelten Daten

1. Kehren Sie zu Azure Migrate im Azure-Portal zurück.
1. Wählen Sie **Aktualisieren** aus, um ermittelte Daten anzuzeigen.

   :::image type="content" source="./media/tutorial-discover-vmware/discovery-assessment-tile.png" alt-text="Screenshot: Aktualisieren von Daten auf der Kachel für Ermittlung und Bewertung":::

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie, wie Sie [Server für die Migration zu Azure-VMs bewerten.](./tutorial-assess-vmware-azure-vm.md)
- Erfahren Sie, wie Sie [Server bewerten, auf denen SQL Server für die Migration zu Azure SQL ausgeführt wird.](./tutorial-assess-sql.md)
- Erfahren Sie, wie Sie [Web-Apps für die Migration zu Azure App Service bewerten](./tutorial-assess-webapps.md).
- Überprüfen Sie die [Daten, die Azure Migrate während der Ermittlung und Bewertung sammelt](migrate-appliance.md#collected-data---vmware).
