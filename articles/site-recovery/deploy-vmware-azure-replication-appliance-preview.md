---
title: Bereitstellen der Azure Site Recovery-Replikationsappliance – Vorschau
description: In diesem Artikel werden die Unterstützung und die Anforderungen für das Bereitstellen der Replikationsappliance für die VMware-Notfallwiederherstellung in Azure mit Azure Site Recovery beschrieben.
ms.service: site-recovery
ms.topic: article
ms.date: 08/19/2021
ms.openlocfilehash: e4021aa0f5572a51ca4d3ddda37f64f4da46a3b4
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446878"
---
# <a name="deploy-azure-site-recovery-replication-appliance---preview"></a>Bereitstellen der Azure Site Recovery-Replikationsappliance – Vorschau

>[!NOTE]
> Die Informationen in diesem Artikel gelten für die Vorschauversion von Azure Site Recovery. Informationen zu den Anforderungen des Konfigurationsservers in klassischen Versionen finden Sie [in diesem Artikel](vmware-azure-configuration-server-requirements.md).

>[!NOTE]
> Stellen Sie sicher, dass Sie einen neuen Recovery Services-Tresor zum Einrichten der Vorschauappliance erstellen. Verwenden Sie keinen vorhandenen Tresor.

Sie stellen eine lokale Replikationsappliance bereit, wenn Sie [Azure Site Recovery](site-recovery-overview.md) für die Notfallwiederherstellung von VMware-VMs und physischen Servern in Azure verwenden.

- Die Replikationsappliance koordiniert die Kommunikation zwischen der lokalen VMware-Umgebung und Azure. Darüber hinaus verwaltet er die Datenreplikation.
- [Erfahren Sie mehr](vmware-azure-architecture-preview.md) über die Komponenten und Prozesse der Azure Site Recovery-Replikationsappliance.

## <a name="hardware-requirements"></a>Hardwareanforderungen

**Komponente** | **Anforderung**
--- | ---
CPU-Kerne | 8
RAM | 32 GB
Anzahl der Datenträger | 3, einschließlich Betriebssystemdatenträger – 80 GB, Datenträger 1 – 620 GB, Datenträger 2 – 620 GB

## <a name="software-requirements"></a>Softwareanforderungen

**Komponente** | **Anforderung**
--- | ---
Betriebssystem | Windows Server 2016
Gebietsschema des Betriebssystems | Englisch (en-*)
Windows Server-Rollen | Aktivieren Sie die folgenden Rollen nicht: <br> - Active Directory Domain Services <br>- Internetinformationsdienste <br> - Hyper-V
Gruppenrichtlinien | Aktivieren Sie die folgenden Gruppenrichtlinien nicht: <br> - Zugriff auf Eingabeaufforderung verhindern <br> - Zugriff auf Programme zum Bearbeiten der Registrierung verhindern <br> - Vertrauenslogik für Dateianlagen <br> - Skriptausführung aktivieren <br> [Weitere Informationen](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - Keine bereits vorhandene Standardwebsite <br> - Keine bereits vorhandene Website/Anwendung sollte an Port 443 lauschen <br>- Die [anonyme Authentifizierung](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) ist aktiviert. <br> - Aktivieren der Einstellung [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))
FIPS (Federal Information Processing Standard) | FIPS-Modus nicht aktivieren|

## <a name="network-requirements"></a>Netzwerkanforderungen

|**Komponente** | **Anforderung**|
|--- | ---|
|Art der IP-Adresse | statischen|
|Ports | 443 (Steuerkanalorchestrierung)<br>9443 (Datentransport)|
|NIC-Typ | VMXNET3 (wenn es sich bei der Appliance um eine VMware-VM handelt)|


### <a name="allow-urls"></a>URLs zulassen

Stellen Sie sicher, dass die folgenden URLs zulässig und von der Azure Site Recovery-Replikationsappliance aus erreichbar sind, um kontinuierliche Konnektivität zu gewährleisten:

  | **URL**                  | **Details**                             |
  | ------------------------- | -------------------------------------------|
  | portal.azure.com          | Navigieren Sie zum Azure-Portal.              |
  | `*.windows.net `<br>`*.msftauth.net`<br>`*.msauth.net`<br>`*.microsoft.com`<br>`*.live.com `<br>`*.office.com ` | So melden Sie sich bei Ihrem Azure-Abonnement an.  |
  |`*.microsoftonline.com `|Erstellen von Azure Active Directory-Apps (AD) für die Kommunikation zwischen der Appliance und Azure Site Recovery. |
  |management.azure.com |Erstellen von Azure AD-Apps, damit die Appliance mit dem Azure Site Recovery-Dienst kommunizieren kann. |
  |`*.services.visualstudio.com `|Laden Sie App-Protokolle hoch, die für die interne Überwachung verwendet werden. |
  |`*.vault.azure.net `|Verwalten von Geheimnissen in Azure Key Vault Hinweis: Stellen Sie sicher, dass die replizierten Computer darauf zugreifen können. |
  |aka.ms |Lassen Sie den Zugriff (auch als Links bezeichnet) zu. Wird für Azure Site Recovery-Applianceupdates verwendet. |
  |download.microsoft.com/download |Zulassen von Downloads von Microsoft Download Center |
  |`*.servicebus.windows.net `|Kommunikation zwischen der Appliance und dem Azure Site Recovery-Dienst. |
  |`*.discoverysrv.windowsazure.com `|Stellen Sie eine Verbindung mit der Azure Site Recovery-Ermittlungsdienst-URL her. |
  |`*.hypervrecoverymanager.windowsazure.com `|Herstellen einer Verbindung mit Azure Site Recovery-Microservices-URLs  |
  |`*.blob.core.windows.net `|Hochladen von Daten in Azure-Speicher, der zum Erstellen von Zieldatenträgern verwendet wird |
  |`*.backup.windowsazure.com `|Schutzdienst-URL: Ein Microservice, der von Azure Site Recovery zum Verarbeiten und Erstellen replizierter Datenträger in Azure verwendet wird |

> [!NOTE]
> Private Links werden in der Vorschauversion nicht unterstützt.

## <a name="prepare-azure-account"></a>Vorbereiten des Azure-Kontos

Zum Erstellen und Registrieren der Azure Site Recovery-Appliance benötigen Sie ein Azure-Konto mit den folgenden Berechtigungen:

- Berechtigungen vom Typ „Mitwirkender“ oder „Besitzer“ für das Azure-Abonnement.
- Berechtigungen zum Registrieren von AAD-Apps (Azure Active Directory)
- „Besitzer“ oder „Mitwirkender“ plus „Benutzerzugriffsadministrator“ für das Azure-Abonnement, um eine Schlüsseltresor zu erstellen, der während der Registrierung der Azure Site Recovery-Replikationsappliance bei Azure verwendet wird.

Wenn Sie gerade erst ein kostenloses Azure-Konto erstellt haben, sind Sie der Besitzer Ihres Abonnements. Wenn Sie nicht der Besitzer des Abonnements sind, arbeiten Sie zum Zwecke der erforderlichen Berechtigungen mit dem Besitzer zusammen.

## <a name="prerequisites"></a>Voraussetzungen

**Dies sind die erforderlichen Schlüsseltresorberechtigungen**:

- Microsoft.OffAzure/*
- Microsoft.KeyVault/register/action
- Microsoft.KeyVault/vaults/read
- Microsoft.KeyVault/vaults/keys/read
- Microsoft.KeyVault/vaults/secrets/read
- Microsoft.Recoveryservices/*

**Führen Sie die folgenden Schritte aus, um die erforderlichen Berechtigungen zuzuweisen**:

1. Suchen Sie im Azure-Portal nach **Abonnements**, und wählen Sie unter **Dienste** das Kontrollkästchen **Abonnements** aus, um nach dem Azure-Abonnement zu suchen.

2. Wählen Sie auf der Seite **Abonnements** das Abonnement aus, in dem Sie den Recovery Services-Tresor erstellt haben.

3. Wählen Sie im ausgewählten Abonnement die Option **Zugriffssteuerung** (IAM) > **Zugriff überprüfen** aus. Suchen Sie unter **Zugriff überprüfen** nach dem entsprechenden Benutzerkonto.

4. Wählen Sie unter **Rollenzuweisung hinzufügen** die Option **Hinzufügen**, dann die Rolle „Mitwirkender“ oder „Besitzer“ und anschließend das Konto aus. Wählen Sie dann **Speichern** aus.

  Ihr Azure-Konto benötigt Berechtigungen zum Registrieren von AAD-Apps, um die Appliance registrieren zu können.

  **Führen Sie die folgenden Schritte aus, um erforderliche Berechtigungen zuzuweisen**:

  - Navigieren Sie im Azure-Portal zu **Azure Active Directory** > **Benutzer** > **Benutzereinstellungen**. Vergewissern Sie sich unter **Benutzereinstellungen**, dass Azure AD-Benutzer Anwendungen registrieren können (standardmäßig auf *Ja* festgelegt).

  - Wenn die Einstellungen für die **App-Registrierungen** auf *Nein* festgelegt sind, können Sie beim Mandantenadministrator/globalen Administrator anfordern, Ihnen die erforderliche Berechtigung zuzuweisen. Alternativ kann der Mandantenadministrator/globale Administrator einem Konto die Rolle Anwendungsentwickler zuweisen, um die Registrierung von AAD-Apps zuzulassen.


## <a name="prepare-infrastructure"></a>Infrastruktur vorbereiten

Sie müssen eine Azure Site Recovery-Replikationsappliance in der lokalen Umgebung einrichten, um die Wiederherstellung auf Ihrem lokalen Computer zu ermöglichen. Ausführliche Informationen zu den von der Appliance ausgeführten Vorgängen finden Sie [in diesem Abschnitt](vmware-azure-architecture-preview.md).

Navigieren Sie zu **Recovery Services-Tresor** > **Erste Schritte**. Wählen Sie unter VMware-Computer in Azure die Option **Infrastruktur vorbereiten** aus, und fahren Sie mit den folgenden Abschnitten fort:

![Recovery Services-Tresor – Vorschau](./media/deploy-vmware-azure-replication-appliance-preview/prepare-infra.png)

Zum Einrichten einer neuen Appliance können Sie eine OVF-Vorlage (empfohlen) oder PowerShell verwenden. Stellen Sie sicher, dass Sie alle [Hardware-](#hardware-requirements) und [Softwareanforderungen](#software-requirements) sowie alle anderen Voraussetzungen erfüllen.

## <a name="create-azure-site-recovery-replication-appliance"></a>Erstellen der Azure Site Recovery-Replikationsappliance

Sie können die Site Recovery-Replikationsappliance mithilfe der OVF-Vorlage oder über PowerShell erstellen.

>[!NOTE]
> Die Einrichtung der Appliance muss sequenziell erfolgen. Die parallele Registrierung mehrerer Appliances kann nicht ausgeführt werden.


### <a name="create-replication-appliance-through-ovf-template"></a>Erstellen einer Replikationsappliance über eine OVF-Vorlage

Dieser Ansatz wird empfohlen, da Azure Site Recovery sicherstellt, dass alle erforderlichen Konfigurationen von der Vorlage verarbeitet werden.
Die OVF-Vorlage erstellt einen Computer mit den erforderlichen Spezifikationen.

![Vorbereiten der Infrastruktur für die Applianceerstellung](./media/deploy-vmware-azure-replication-appliance-preview/prepare-infra.png)

**Führen Sie die folgenden Schritte aus:**

1. Laden Sie die OVF-Vorlage herunter, um eine Appliance in Ihrer lokalen Umgebung einzurichten.
2. Schalten Sie nach Abschluss der Bereitstellung die Appliance-VM ein, um die Microsoft Evaluation-Lizenz zu akzeptieren.
3. Geben Sie auf dem nächsten Bildschirm das Kennwort für den Administratorbenutzer an.
4. Wählen Sie **Abschließen** aus. Das System wird neu gestartet, und Sie können sich mit dem Administratorbenutzerkonto anmelden.

### <a name="set-up-the-appliance-through-powershell"></a>Einrichten der Appliance über PowerShell

Im Falle von Organisationseinschränkungen können Sie die Site Recovery-Replikationsappliance manuell über PowerShell einrichten. Führen Sie die folgenden Schritte aus:

1. Laden Sie die Installationsprogramme [hier](https://aka.ms/V2ARcmApplianceCreationPowershellZip) herunter, und platzieren Sie diesen Ordner auf der Azure Site Recovery-Replikationsappliance.
2. Nachdem Sie den ZIP-Ordner erfolgreich kopiert haben, entzippen und extrahieren Sie die Komponenten des Ordners.
3. Wechseln Sie in das Verzeichnis, in das der Ordner extrahiert wird, und führen Sie das folgende PowerShell-Skript als Administrator aus:

    **DRInstaller.ps1**  

## <a name="register-appliance"></a>Appliance registrieren
  Nachdem Sie die Appliance erstellt haben, wird automatisch der Konfigurations-Manager der Microsoft Azure-Appliance gestartet. Voraussetzungen wie Internetkonnektivität, Zeitsynchronisierung, Systemkonfigurationen und Gruppenrichtlinien (unten aufgeführt) werden überprüft.

  - CheckRegistryAccessPolicy: Verhindert den Zugriff auf Registrierungsbearbeitungstools.
      - Key: HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
      - Der DisableRegistryTools-Wert darf nicht gleich 0 sein.

  - CheckCommandPromptPolicy: Verhindert den Zugriff auf die Eingabeaufforderung.

      - Key: HKLM\SOFTWARE\Policies\Microsoft\Windows\System
      - Der DisableCMD-Wert darf nicht gleich 0 sein.

  - CheckTrustLogicAttachmentsPolicy: Vertrauensstellungslogik für Dateierweiterungen.

      - Key: HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Attachments
      - Der UseTrustedHandlers-Wert darf nicht gleich 3 sein.

  - CheckPowershellExecutionPolicy: Skriptausführung aktivieren.

      - Die PowerShell-Ausführungsrichtlinie darf nicht auf „AllSigned“ oder „Restricted“ festgelegt sein.
      - Stellen Sie sicher, dass die Gruppenrichtlinie Anlagen-Manager für ‚Skriptausführung aktivieren‘“ nicht auf „Deaktiviert“ oder „Nur signierte Skripts zulassen“ festgelegt ist.


  **Führen Sie zum Registrieren der Appliance die folgenden Schritte aus**:

1. Konfigurieren Sie die Proxyeinstellungen, indem Sie die Option **Proxy zum Herstellen einer Verbindung mit dem Internet verwenden** aktivieren.

    Alle Azure Site Recovery-Dienste verwenden diese Einstellungen, um eine Verbindung mit dem Internet herzustellen. Es werden nur HTTP-Proxys unterstützt.

2. Stellen Sie sicher, dass die [erforderlichen URLs](#allow-urls) zulässig und von der Azure Site Recovery-Replikationsappliance aus erreichbar sind, um kontinuierliche Konnektivität zu gewährleisten.

3. Nach dem Überprüfen dieser die Voraussetzungen werden im nächsten Schritt Informationen zu allen Appliancekomponenten abgerufen. Überprüfen Sie den Status aller Komponenten, und klicken Sie dann auf **Weiter**. Nachdem Sie die Details gespeichert haben, wählen Sie die Appliancekonnektivität aus.

4. Wählen Sie nach dem Speichern der Konnektivitätsdetails **Weiter** aus, um mit der Registrierung bei Microsoft Azure fortzufahren.

5. Stellen Sie sicher, dass die [Voraussetzungen](#prerequisites) erfüllt sind, und fahren Sie mit der Registrierung fort.

    ![Appliance registrieren](./media/deploy-vmware-azure-replication-appliance-preview/app-setup-register.png)

  - **Anzeigename der Appliance**: Geben Sie einen Anzeigenamen an, mit dem Sie diese Appliance im Azure-Portal unter „Infrastruktur des Recovery Services-Tresors“ nachverfolgen möchten.

  - **Azure Site Recovery-Replikationsapplianceschlüssel**: Kopieren Sie den Schlüssel aus dem Portal, indem Sie zu **Recovery Services-Tresor** > **Erste Schritte** > **VMware zu Azure – Infrastruktur vorbereiten** navigieren.

  - Klicken Sie nach dem Einfügen des Schlüssels auf **Anmelden**.
    Sie werden zu einer neuen Registerkarte für die Authentifizierung umgeleitet.

      Standardmäßig wird auf der Authentifizierungs-Manager-Seite ein Authentifizierungscode generiert, wie unten hervorgehoben. Verwenden Sie diesen Code auf der Registerkarte „Authentifizierung“.

  - Geben Sie Ihre Microsoft Azure-Anmeldeinformationen ein, um die Registrierung abzuschließen.

      Nach erfolgreicher Registrierung können Sie die Registerkarte schließen und zum Konfigurations-Manager wechseln, um die Einrichtung fortzusetzen.

      ![Authentifizierungscode](./media/deploy-vmware-azure-replication-appliance-preview/enter-code.png)

      > [!NOTE]
      > Ein Authentifizierungscode läuft fünf Minuten nach der Generierung ab. Bei Inaktivität über diesen Zeitraum hinaus werden Sie aufgefordert, sich erneut bei Azure anzumelden.


6. Wählen Sie **Anmelden** aus, um erneut eine Verbindung mit der Sitzung herzustellen. Informationen zum Authentifizierungscode finden Sie im Abschnitt *Zusammenfassung* oder *Registrieren beim Azure Recovery Services-Tresor* im Konfigurations-Manager.

7. Nach erfolgreicher Anmeldung werden Details zu Abonnement, Ressourcengruppe und Recovery Services-Tresor angezeigt. Sie können sich abmelden, falls Sie den Tresor ändern möchten. Wählen Sie andernfalls die Option **Fortsetzen** aus, um fortzufahren.

    ![Appliance registriert](./media/deploy-vmware-azure-replication-appliance-preview/app-setup.png)

    Fahren Sie nach der erfolgreichen Registrierung mit dem Konfigurieren von vCenter-Details fort.

    ![Konfiguration von vCenter](./media/deploy-vmware-azure-replication-appliance-preview/vcenter-information.png)

8. Wählen Sie **vCenter Server hinzufügen** aus, um vCenter-Informationen hinzuzufügen. Geben Sie den Servernamen oder die IP-Adresse der vCenter-Instanz sowie Portinformationen ein. Geben Sie anschließend Benutzername, Kennwort und Anzeigename an, die verwendet werden, um Details zu dem [über vCenter verwalteten virtuellen Computer](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) abzurufen. Die Benutzerkontodetails werden verschlüsselt und lokal auf dem Computer gespeichert.

>[!NOTE]
> Wenn Sie versuchen, mehreren Appliances die gleiche vCenter Server-Instanz hinzuzufügen, müssen Sie sicherstellen, dass in allen Appliances derselbe Anzeigename verwendet wird.

9. Nachdem Sie die vCenter-Informationen erfolgreich gespeichert haben, wählen Sie **Anmeldeinformationen für virtuelle Computer hinzufügen** aus, um Benutzerdetails zu den VMs bereitzustellen, die über vCenter ermittelt wurden.

   >[!NOTE]
   > - Stellen Sie für das Linux-Betriebssystem sicher, dass Stammanmeldeinformationen angegeben werden. Für das Windows-Betriebssystem sollte ein Benutzerkonto mit Administratorrechten hinzugefügt werden. Diese Anmeldeinformationen werden verwendet, um den Mobilitäts-Agent während des Vorgangs der Aktivierung der Replikation per Push auf die Quell-VM zu übertragen. Die Anmeldeinformationen können während der Aktivierung des Replikationsworkflows pro VM im Azure-Portal ausgewählt werden.
   > - Besuchen Sie den Appliancekonfigurator, um Anmeldeinformationen für den Zugriff auf Ihre Computer zu bearbeiten oder hinzuzufügen.

10. Wählen Sie nach dem erfolgreichen Hinzufügen der Details **Weiter** aus, um alle Komponenten der Azure Site Recovery-Replikationsappliance zu installieren und bei Azure-Diensten zu registrieren. Diese Aktivität kann bis zu 30 Minuten dauern.

    Stellen Sie sicher, dass Sie den Browser nicht schließen, während die Konfiguration ausgeführt wird.


## <a name="view-azure-site-recovery-replication-appliance-in-azure-portal"></a>Anzeigen der Azure Site Recovery-Replikationsappliance im Azure-Portal

Navigieren Sie nach erfolgreicher Konfiguration der Azure Site Recovery-Replikationsappliance zum **Recovery Services-Tresor** im Azure-Portal.

Wählen Sie **Infrastruktur vorbereiten (Vorschau)** unter **Erste Schritte** aus. Sie können sehen, dass eine Azure Site Recovery Replikationsappliance nun bereits bei diesem Tresor registriert ist. Jetzt sind Sie soweit. Beginnen Sie mit dem Schutz Ihrer Quellcomputer über diese Replikationsappliance.

Wenn Sie auf *1 Appliance(s) auswählen* klicken, werden Sie zur Ansicht der Azure Site Recovery-Replikationsappliance weitergeleitet, in der die Liste der für diesen Tresor registrierten Appliances angezeigt wird.

Außerdem wird eine Registerkarte für **Ermittelte Elemente** angezeigt, auf der alle ermittelten vCenter Server-Instanzen/vSphere-Hosts aufgelistet sind.

![Vorschau der Replikationsappliance](./media/deploy-vmware-azure-replication-appliance-preview/discovered-items.png)

## <a name="sizing-and-capacity"></a>Dimensionierung und Kapazität
Eine Appliance, die einen integrierten Prozessserver zum Schutz der Workload verwendet, kann basierend auf den folgenden Konfigurationen bis zu 200 virtuelle Computer ausführen:

  |CPU |    Arbeitsspeicher |    Größe des Cachedatenträgers |    Datenänderungsrate |    Geschützte Computer |
  |---|-------|--------|------|-------|
  |16 vCPUs (2 Sockets * 8 Kerne mit 2,5 GHz)    | 32 GB |    1 TB |    Mehr als 1 TB bis 2 TB    | Wird verwendet, um 151 bis 200 Computer zu replizieren.|

- Sie können die Ermittlung aller Computer auf einer vCenter Server-Instanz mithilfe einer der Replikationsappliances im Tresor durchführen.

- Sie können [einen geschützten Computer zwischen verschiedenen Appliances im gleichen Tresor wechseln](switch-replication-appliance-preview.md), wenn die ausgewählte Appliance fehlerfrei ist.

Ausführliche Informationen zur Verwendung mehrerer Appliances und zum Failover einer Replikationsappliance finden Sie in [diesem Artikel](switch-replication-appliance-preview.md).

## <a name="next-steps"></a>Nächste Schritte
[Einrichten der Notfallwiederherstellung in Azure für lokale VMware-VMs](vmware-azure-tutorial.md)
