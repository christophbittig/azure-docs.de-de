---
title: Behandeln von Problemen mit der Azure Migrate-Appliance
description: Hilfe bei der Behandlung von Problemen, die mit der Azure Migrate-Appliance auftreten können.
author: Vikram1988
ms.author: vibansa
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 07/01/2020
ms.openlocfilehash: 405976fdf418c02172c3ad8129d206c3313380e2
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/14/2021
ms.locfileid: "122350727"
---
# <a name="troubleshoot-the-azure-migrate-appliance"></a>Behandeln von Problemen mit der Azure Migrate-Appliance

Dieser Artikel hilft Ihnen bei der Behebung von Problemen bei der Bereitstellung der [Azure Migrate](migrate-services-overview.md)-Appliance und bei der Verwendung der Appliance zur Erkennung von lokalen Servern.

## <a name="whats-supported"></a>Was wird unterstützt?

[Überprüfen](migrate-appliance.md) Sie die Anforderungen an die Applianceunterstützung.

## <a name="invalid-ovf-manifest-entry-during-appliance-set-up"></a>„Ungültiger OVF-Manifesteintrag“ während der Einrichtung der Appliance

**Fehler**

Sie erhalten die Fehlermeldung „Die bereitgestellte Manifestdatei ist ungültig: Ungültiger OVF-Manifesteintrag“, wenn Sie eine Appliance mithilfe der OVA-Vorlage einrichten.

**Wartung**

1. Überprüfen Sie, ob die OVA-Datei für die Azure Migrate-Appliance ordnungsgemäß heruntergeladen wird, indem Sie deren Hashwert überprüfen. [Weitere Informationen](./tutorial-discover-vmware.md) Wenn der Hashwert nicht übereinstimmt, laden Sie die OVA-Datei erneut herunter, und wiederholen Sie die Bereitstellung.
2. Wenn immer noch Fehler bei der Bereitstellung auftreten und Sie den VMware vSphere-Client zum Bereitstellen der OVF-Datei verwenden, versuchen Sie, die Bereitstellung über den vSphere-Webclient vorzunehmen. Wenn die Bereitstellung weiterhin fehlschlägt, versuchen Sie es mit einem anderen Webbrowser.
3. Wenn Sie den vSphere-Webclient verwenden und die Bereitstellung auf vCenter Server 6.5 oder 6.7 ausführen möchten, versuchen Sie, die OVA-Datei direkt auf dem ESXi-Host bereitzustellen:
   - Stellen Sie mit dem Webclient (https://<*Host-IP-Adresse*>/ui) eine direkte Verbindung mit dem ESXi-Host her (anstelle von vCenter Server).
   - Wählen Sie unter **Startseite** > **Bestand** die Optionen **Datei** > **OVF-Vorlage bereitstellen** aus. Navigieren Sie zur OVA-Datei, und schließen Sie die Bereitstellung ab.
4. Wenn weiterhin ein Fehler bei der Bereitstellung auftritt, wenden Sie sich an den Azure Migrate-Support.

## <a name="connectivity-check-failing-during-set-up-prerequisites"></a>Fehler bei der Konnektivitätsprüfung während der „Einrichtung von Voraussetzungen“

**Fehler**

Bei der Konnektivitätsprüfung auf der Appliance tritt ein Fehler auf.

**Wartung**

1. Stellen Sie sicher, dass Sie von der Appliance aus eine Verbindung mit den erforderlichen [URLs](./migrate-appliance.md#url-access) herstellen können.
1. Überprüfen Sie, ob ein Proxy oder eine Firewall den Zugriff auf diese URLs blockiert. Wenn Sie eine Positivliste erstellen müssen, sollten Sie darauf achten, dass Sie alle URLs einbeziehen.
1. Falls lokal ein Proxyserver konfiguriert ist, sollten Sie darauf achten, dass Sie die Proxydetails richtig angeben, indem Sie im selben Schritt die Option **Proxy einrichten** auswählen. Geben Sie bei Bedarf die Anmeldeinformationen für die Autorisierung an.
1. Vergewissern Sie sich, dass der Server nicht bereits verwendet wurde, um die [Replikationsappliance](./migrate-replication-appliance.md) einzurichten, oder dass Sie den Mobilitätsdienst-Agent auf dem Server installiert haben.

## <a name="connectivity-check-failing-for-akams-url-during-set-up-prerequisites"></a>Fehler bei der Konnektivitätsprüfung für die aka.ms-URL während der „Einrichtung von Voraussetzungen“

**Fehler**

Bei der Konnektivitätsprüfung auf der Appliance tritt ein Fehler für die aka.ms-URL auf.

**Wartung**

1. Stellen Sie sicher, dass Sie mit dem Internet verbunden sind und URL-aka.ms/* zum Herunterladen der neuesten Versionen der Dienste in die Positivliste aufgenommen haben.
2. Überprüfen Sie, ob ein Proxy bzw. eine Firewall den Zugriff auf diese URL blockiert. Stellen Sie sicher, dass Sie die Proxydetails im Schritt „Voraussetzungen“ des Konfigurations-Managers richtig angegeben haben.
3. Sie können zurück zum Konfigurations-Manager der Appliance wechseln und die Voraussetzungen erneut ausführen, um ein automatisches Update zu initiieren.
3. Wenn der Wiederholungsversuch nicht hilft, können Sie [hier](https://aka.ms/latestapplianceservices) die Datei *latestcomponents.js* herunterladen, um die neuesten Versionen der fehlerhaften Dienste zu überprüfen und sie manuell über die Downloadlinks in der Datei zu aktualisieren.

 Wenn Sie die Appliance für **private Endpunktkonnektivität** aktiviert haben und den Zugriff auf diese URL über das Internet nicht zulassen möchten, können Sie [automatische Updates deaktivieren](./migrate-appliance.md#turn-off-auto-update), da der aka.ms-Link für diesen Dienst erforderlich ist.

 >[!Note]
 >Wenn Sie den Dienst für automatische Updates deaktivieren, werden für die Dienste, die auf der Appliance ausgeführt werden, die aktuellen Updates nicht automatisch bereitgestellt. [Aktualisieren Sie die Appliancedienste manuell](./migrate-appliance.md#manually-update-an-older-version), um dies zu vermeiden.

## <a name="auto-update-check-failing-during-set-up-prerequisites"></a>Fehler bei der Prüfung automatischer Updates während der „Einrichtung von Voraussetzungen“

**Fehler**

Sie erhalten einen Fehler bei der automatischen Updateüberprüfung auf der Appliance.

**Wartung**

1. Vergewissern Sie sich, dass Sie für die [erforderlichen URLs](./migrate-appliance.md#url-access) eine Positivliste erstellt haben und diese nicht durch eine Proxy- oder Firewalleinstellung blockiert werden.
1. Sollte bei der Aktualisierung einer Appliancekomponente ein Fehler auftreten, wiederholen Sie entweder die Vorbereitungsschritte, oder [aktualisieren Sie die Appliancedienste manuell](./migrate-appliance.md#manually-update-an-older-version).

## <a name="time-sync-check-failing-during-set-up-prerequisites"></a>Fehler bei der Zeitsynchronisierungsprüfung während der „Einrichtung von Voraussetzungen“

**Fehler**

Ein Fehler bei der Zeitsynchronisierung weist darauf hin, dass die Serveruhr möglicherweise um mehr als fünf Minuten von der aktuellen Uhrzeit abweicht.

**Wartung**

- Stellen Sie sicher, dass die Zeit des Applianceservers mit der Internetzeit synchronisiert wird, indem Sie die Einstellungen für Datum und Uhrzeit in der Systemsteuerung überprüfen.
- Sie können die Uhrzeit auf dem Applianceserver auch so ändern, dass sie mit der aktuellen Uhrzeit übereinstimmt, indem Sie die folgenden Schritte ausführen:
     1. Öffnen Sie auf dem Server eine Administratoreingabeaufforderung.
     2. Führen Sie **w32tm /tz** aus, um die Zeitzone zu überprüfen.
     3. Führen Sie **w32tm /resync** aus, um die Zeit zu synchronisieren.

## <a name="vddk-check-failing-during-set-up-prerequisites-on-vmware-appliance"></a>Fehler bei VDDK-Überprüfung während der „Einrichtung von Voraussetzungen“ auf der VMware-Appliance

**Fehler**

Fehler bei der VDDK-Überprüfung, da die Appliance das erforderliche VDDK Kit nicht finden konnte, das auf der Appliance installiert ist. Dies kann zu Fehlern bei der laufenden Replikation führen.

**Wartung**

1. Stellen Sie sicher, dass Sie das VDDK Kit 6.7 heruntergeladen und die Dateien in **C:\Programme\VMware\VMware Virtual Disk Development Kit\** auf dem Applianceserver kopiert haben.
2. Stellen Sie sicher, dass keine andere Software oder Anwendung eine andere Version des VDDK Kits auf der Appliance verwendet.

## <a name="getting-project-key-related-error-during-appliance-registration"></a>Auftreten eines projektschlüsselbezogenen Fehlers während der Applianceregistrierung

**Fehler** 

Sie haben Probleme, wenn Sie versuchen, die Appliance mit dem aus dem Projekt kopierten Azure Migrate-Projektschlüssel zu registrieren.

**Wartung**

1. Vergewissern Sie sich, dass Sie den richtigen Schlüssel aus dem Projekt kopiert haben: Wählen Sie auf der Karte **Azure Migrate: Ermittlung und Bewertung** in Ihrem Projekt die Option **Ermitteln** und im ersten Schritt dann **Vorhandene Appliance verwalten** aus. Wählen Sie im Dropdownmenü den Namen der Appliance aus (für die Sie zuvor einen Schlüssel generiert haben), und kopieren Sie den entsprechenden Schlüssel.
2. Achten Sie darauf, dass Sie den Schlüssel in die Appliance mit dem richtigen **Cloudtyp** („Öffentlich“/„US Gov“) und **Appliancetyp** („VMware“/„Hyper-V“/„Physisch“ oder „Sonstiges“) einfügen. Überprüfen Sie im oberen Bereich des Appliance-Konfigurations-Managers den Cloud- und Szenariotyp.

## <a name="failed-to-connect-to-the-azure-migrate-project-during-appliance-registration"></a>„Fehler beim Herstellen einer Verbindung mit dem Azure Migrate-Projekt“ während der Applianceregistrierung

**Fehler**

Nach der erfolgreichen Anmeldung mit dem Azure-Benutzerkonto tritt im Registrierungsschritt für die Appliance der folgende Fehler auf: **„Fehler beim Herstellen einer Verbindung mit dem Azure Migrate-Projekt. Überprüfen Sie die Fehlerdetails, und befolgen Sie die Schritte zur Behebung, indem Sie auf „Wiederholen“ klicken“** .

Dieses Problem tritt auf, wenn das Azure-Benutzerkonto, das für die Anmeldung über den Appliance-Konfigurations-Manager verwendet wurde, nicht das gleiche Benutzerkonto ist, das beim Generieren des Azure Migrate-Projektschlüssels im Portal verwendet wurde.

**Wartung**
1. Um die Registrierung der Appliance abzuschließen, verwenden Sie dasselbe Azure-Benutzerkonto, mit dem der Azure Migrate-Projektschlüssel im Portal generiert wurde. ODER
1. Weisen Sie die erforderlichen Rollen und [Berechtigungen](./tutorial-discover-vmware.md#prepare-an-azure-user-account) dem anderen Azure-Benutzerkonto zu, das für die Registrierung der Appliance verwendet wird.

## <a name="azure-active-directory-aad-operation-failed-with-status-forbidden-during-appliance-registration"></a>„Fehler beim Azure Active Directory-Vorgang (AAD) mit dem Status „Forbidden“ (Verboten) während der Applianceregistrierung“

**Fehler**

Sie können die Registrierung aufgrund unzureichender AAD-Berechtigungen nicht abschließen, und der Fehler „Azure Active Directory-Vorgang (AAD) mit folgendem Status fehlgeschlagen: „Forbidden““ tritt auf.

**Wartung**

Stellen Sie sicher, dass Sie über die erforderlichen [Berechtigungen](./tutorial-discover-vmware.md#prepare-an-azure-user-account) zum Erstellen und Verwalten von AAD-Anwendungen in Azure verfügen. Sie müssen über die Rolle **Anwendungsentwickler** ODER über die Benutzerrolle verfügen, für die auf Mandantenebene die Berechtigung **Benutzer kann Anwendungen registrieren** aktiviert ist.

## <a name="forbidden-to-access-key-vault-during-appliance-registration"></a>„Unzulässiger Zugriff auf Key Vault“ während der Applianceregistrierung

**Fehler**

Fehler bei Azure Key Vault-Vorgang zum Erstellen oder Aktualisieren von „{KeyVaultName}“: „{KeyVaultErrorMessage}“.

Dies geschieht in der Regel, wenn sich das Azure-Benutzerkonto, das zum Registrieren der Appliance verwendet wird, von dem Konto unterscheidet, das zum Generieren des Azure Migrate-Projektschlüssels im Portal verwendet wird (bei der Erstellung des Schlüsseltresors).

**Wartung**

1. Stellen Sie sicher, dass das derzeit angemeldete Benutzerkonto auf der Appliance über die erforderlichen Berechtigungen für die Key Vault-Instanz verfügt (in der Fehlermeldung angegeben). Das Benutzerkonto benötigt die [hier](./tutorial-discover-vmware.md#prepare-an-azure-user-account) erwähnten Berechtigungen.
2. Navigieren Sie zu Key Vault, und stellen Sie sicher, dass Ihr Benutzerkonto über eine Zugriffsrichtlinie mit allen _Schlüssel-, Geheimnis- und Zertifikatberechtigungen_ verfügt, die unter der Key Vault-Zugriffsrichtlinie zugewiesen werden. [Weitere Informationen](../key-vault/general/assign-access-policy-portal.md)
3. Wenn Sie die Appliance für **Konnektivität mit privaten Endpunkten** aktiviert haben, stellen Sie sicher, dass die Appliance entweder in demselben VNet gehostet wird, in dem die Key Vault-Instanz erstellt wurde, oder dass sie über eine private Verbindung mit dem Azure-VNet (in dem die Key Vault-Instanz erstellt wurde) verbunden ist. Stellen Sie sicher, die private Key Vault-Verbindung von der Appliance auflösbar ist. Navigieren Sie zu **Azure Migrate**: **Ermittlung** und **Bewertung**> **Eigenschaften**, um die Details privater Endpunkte für Ressourcen wie die Key Vault-Instanz zu ermitteln, die während der Azure Migrate-Schlüsselerstellung erstellt wurden. [Weitere Informationen](./troubleshoot-network-connectivity.md)
4. Wenn Sie über die erforderlichen Berechtigungen und Konnektivität verfügen, versuchen Sie die Registrierung auf der Appliance nach einiger Zeit erneut.

## <a name="unable-to-connect-to-vcenter-server-during-validation"></a>Während der Überprüfung kann keine Verbindung mit vCenter Server hergestellt werden

**Fehler**

Wenn dieser Verbindungsfehler angezeigt wird, können Sie möglicherweise keine Verbindung mit vCenter Server „*Servername*.com:9443“ herstellen. Die Fehlerdetails weisen darauf hin, dass unter `https://\*servername*.com:9443/sdk` kein Endpunkt lauscht, der die Nachricht akzeptieren kann.

**Wartung**

- Überprüfen Sie, ob Sie die neueste Version der Appliance ausführen. Ist dies nicht der Fall, führen Sie ein Upgrade der Appliance auf die [neueste Version](./migrate-appliance.md) durch.
- Wenn das Problem bei der neuesten Version weiterhin auftritt, ist die Appliance möglicherweise nicht in der Lage, den angegebenen vCenter Server-Namen aufzulösen, oder der angegebene Port ist eventuell falsch. Wenn der Port nicht angegeben ist, versucht Collector standardmäßig, eine Verbindung mit Port 443 herzustellen.

    1. Versuchen Sie, „*Servername*.com“ von der Appliance aus per Ping zu erreichen.
    2. Wenn Schritt 1 nicht erfolgreich ist, können Sie versuchen, eine Verbindung mit vCenter Server über die IP-Adresse herzustellen.
    3. Ermitteln Sie die richtige Portnummer für die Verbindung mit vCenter Server.
    4. Überprüfen Sie, ob vCenter Server ausgeführt wird und betriebsbereit ist.

## <a name="server-credentials-domain-failing-validation-on-vmware-appliance"></a>Überprüfungsfehler der Serveranmeldeinformationen (Domäne) auf der VMware-Appliance

**Fehler**

Sie erhalten einen „Überprüfungsfehler“ für Domänenanmeldeinformationen, die auf der VMware-Appliance hinzugefügt wurden, um die Softwareinventar und Abhängigkeitsanalyse ohne Agent durchzuführen.

**Wartung**

1. Vergewissern Sie sich, dass Sie den richtigen Domänennamen und die Anmeldeinformationen angegeben haben.
1. Stellen Sie sicher, dass die Domäne von der Appliance aus erreichbar ist, damit die Anmeldeinformationen überprüft werden können. Unter Umständen bestehen für die Appliance Sichtverbindungsprobleme, oder der Domänenname kann über den Applianceserver nicht aufgelöst werden.
1. Sie können die Option **Bearbeiten** auswählen, um den Domänennamen oder die Anmeldeinformationen zu aktualisieren, und dann **Anmeldeinformationen erneut überprüfen** auswählen, um die Anmeldeinformationen nach einiger Zeit noch einmal überprüfen zu lassen.

## <a name="access-is-denied-when-connecting-to-hyper-v-hosts-or-clusters-during-validation"></a>„Zugriff verweigert“ beim Herstellen einer Verbindung mit Hyper-V-Hosts oder -Clustern während der Überprüfung

**Fehler**

Sie können den hinzugefügten Hyper-V-Host/-Cluster aufgrund einer Fehlermeldung „Zugriff verweigert“ nicht überprüfen.

**Wartung**

1. Stellen Sie sicher, dass alle [Voraussetzungen für die Hyper-V-Hosts erfüllt sind](./migrate-support-matrix-hyper-v.md#hyper-v-host-requirements). 
1. Lesen Sie [**hier**](./tutorial-discover-hyper-v.md#prepare-hyper-v-hosts) die Schritte, wie Sie die Hyper-V-Hosts manuell oder mithilfe eines PowerShell-Skripts für die Bereitstellung vorbereiten.

## <a name="the-server-does-not-support-ws-management-identify-operations-during-validation"></a>„Der Server unterstützt keine WS-Management Identify-Vorgänge“ während der Überprüfung

**Fehler**

Sie können Hyper-V-Cluster auf der Appliance aufgrund des folgenden Fehlers nicht überprüfen: „Der Server unterstützt keine WS-Management Identify-Vorgänge. Überspringen Sie den TestConnection-Teil der Anforderung, und versuchen Sie es noch mal.“

**Wartung**

Dies ist normalerweise der Fall, wenn Sie eine Proxykonfiguration auf der Appliance bereitgestellt haben. Die Appliance stellt die Verbindung mit den Clustern mithilfe des Kurznamens für die Clusterknoten her, auch wenn der Benutzer den FQDN des Knotens angibt. Fügen Sie der Proxyliste für die Umgehung auf der Appliance den Kurznamen für die Clusterknoten hinzu. Das Problem wird behoben, und die Überprüfung des Hyper-V-Clusters ist erfolgreich.

## <a name="cant-connect-to-host-or-cluster-during-validation-on-hyper-v-appliance"></a>„Es kann keine Verbindung mit dem Host oder Cluster hergestellt werden“ während der Überprüfung auf der Hyper-V-Appliance

**Fehler**

„Es ist keine Verbindung mit einem Host oder Cluster möglich, da der Servername nicht aufgelöst werden kann. WinRM-Fehlercode: 0x803381B9“. Dieser Fehler tritt auf, wenn der Azure DNS-Dienst für die Appliance den von Ihnen angegebenen Cluster- oder Hostnamen nicht auflösen kann.

Dieser Fall tritt normalerweise ein, wenn Sie die IP-Adresse eines Hosts hinzugefügt haben, die vom DNS nicht aufgelöst werden kann. Sie sehen diesen Fehler eventuell auch für Hosts in einem Cluster. In diesem Fall kann die Appliance zwar eine Verbindung mit dem Cluster herstellen, doch gibt der Cluster Hostnamen zurück, bei denen es sich nicht um FQDNs handelt.

**Wartung**

Um diesen Fehler zu beheben, aktualisieren Sie die „hosts“-Datei auf der Appliance und fügen Sie ein Mapping von IP-Adresse und Hostnamen hinzu:
1. Öffnen Sie Notepad als Administrator.
1. Öffnen Sie die Datei „C:\Windows\System32\Drivers\etc\hosts“.
1. Fügen Sie die IP-Adresse und den Hostnamen in einer Zeile hinzu. Wiederholen Sie dies für jeden Host oder Cluster, bei dem dieser Fehler angezeigt wird.
1. Speichern und schließen Sie die Datei „hosts“.
1. Überprüfen Sie mithilfe der Applianceverwaltungs-App, ob die Appliance eine Verbindung mit den Hosts herstellen kann. Nach 30 Minuten sollten die neuesten Informationen für diese Hosts im Azure-Portal angezeigt werden.

## <a name="unable-to-connect-to-server-during-validation-of-physical-servers"></a>„Verbindung mit Server kann nicht hergestellt werden“ während der Überprüfung physischer Server

**Wartung**

- Stellen Sie sicher, dass eine Verbindung zwischen der Appliance und dem Zielserver besteht.
- Wenn es sich um einen Linux-Server handelt, stellen Sie sicher, dass kennwortbasierte Authentifizierung mithilfe der folgenden Schritte aktiviert ist:
    1. Melden Sie sich beim Linux-Server an, und öffnen Sie die SSH-Konfigurationsdatei mit dem Befehl „vi /etc/ssh/sshd_config“.
    2. Setzen Sie die Option „PasswordAuthentication“ auf „Ja“. Speichern Sie die Datei .
    3. Starten Sie den SSH-Dienst durch Ausführen von „service sshd restart“ neu.
- Wenn es sich um einen Windows-Server handelt, vergewissern Sie sich, dass Port 5985 geöffnet ist, um WMI-Remoteaufrufe zuzulassen.
- Wenn Sie einen GCP-Linux-Server ermitteln und einen Root-Benutzer verwenden, verwenden Sie die folgenden Befehle, um die Standardeinstellung für die Root-Anmeldung zu ändern.
    1. Melden Sie sich beim Linux-Server an, und öffnen Sie die SSH-Konfigurationsdatei mit dem Befehl „vi /etc/ssh/sshd_config“.
    2. Setzen Sie die Option „PermitRootLogin“ auf „Ja“.
    3. Starten Sie den SSH-Dienst durch Ausführen von „service sshd restart“ neu.

## <a name="failed-to-fetch-bios-guid-for-server-during-validation"></a>„Fehler beim Abrufen der BIOS-GUID“ für den Server während der Überprüfung

**Fehler**

Die Überprüfung eines physischen Servers schlägt auf der Appliance mit der Fehlermeldung „Fehler beim Abrufen der BIOS-GUID“ fehl.

**Wartung**

**Linux-Server**: Stellen Sie eine Verbindung mit dem Zielserver her, für den die Überprüfung fehlschlägt, und führen Sie die folgenden Befehle aus, um zu überprüfen, ob die BIOS-GUID des Servers zurückgegeben wird:
````
cat /sys/class/dmi/id/product_uuid
dmidecode | grep -i uuid | awk '{print $2}'
````
Sie können die Befehle auch über die Eingabeaufforderung auf dem Applianceserver ausführen, indem Sie mithilfe des folgenden Befehls eine SSH-Verbindung mit dem Linux-Zielserver herstellen:
````
ssh <username>@<servername>
````

**Windows Server**: Führen Sie den folgenden Code in PowerShell vom Applianceserver für den Zielserver aus, für den die Überprüfung fehlschlägt, um zu überprüfen, ob die BIOS-GUID des Servers zurückgegeben wird:
````
[CmdletBinding()]
Param(
  [Parameter(Mandatory=$True,Position=1)]
   [string]$Hostname
)
$HostNS = "root\cimv2"
$error.Clear()

$Cred = Get-Credential
$Session = New-CimSession -Credential $Cred -ComputerName $Hostname

if ($Session -eq $null -or $Session.TestConnection() -eq $false)
{
    Write-Host "Connection failed with $Hostname due to $error"
    exit -1
}

Write-Host "Connection established with $Hostname"
#Get-WmiObject -Query "select uuid from Win32_ComputerSystemProduct" 

$HostIntance = $Session.QueryInstances($HostNS, "WQL", "Select UUID from Win32_ComputerSystemProduct")
$HostIntance | fl *
````

Beim Ausführen des Codes oben müssen Sie den Hostnamen des Zielservers angeben (IP-Adresse, FQDN oder Hostname). Anschließend werden Sie aufgefordert, die Anmeldeinformationen anzugeben, um eine Verbindung mit dem Server herzustellen.

## <a name="no-suitable-authentication-method-found-for-server-during-validation"></a>„Keine geeignete Authentifizierungsmethode gefunden“ für den Server während der Überprüfung

**Fehler**

Sie erhalten diesen Fehler, wenn Sie versuchen, einen Linux-Server über die physische Appliance zu überprüfen: „Keine geeignete Authentifizierungsmethode gefunden“.

**Wartung**

Stellen Sie mithilfe der folgenden Schritte sicher, dass die kennwortbasierte Authentifizierung auf dem Linux-Server aktiviert ist:

1. Melden Sie sich beim Linux-Server an, und öffnen Sie die SSH-Konfigurationsdatei mit dem Befehl „vi /etc/ssh/sshd_config“.
2. Setzen Sie die Option „PasswordAuthentication“ auf „Ja“. Speichern Sie die Datei .
3. Starten Sie den SSH-Dienst durch Ausführen von „service sshd restart“ neu.

## <a name="access-is-denied-when-connecting-to-physical-servers-during-validation"></a>„Zugriff verweigert“ beim Herstellen einer Verbindung mit physischen Servern während der Überprüfung

**Fehler**

Sie erhalten diesen Fehler, wenn Sie versuchen, einen Windows-Server über die physische Appliance zu überprüfen: „Der WS-Management-Dienst kann die Anforderung nicht verarbeiten. Der WMI-Dienst hat einen Fehler „Zugriff verweigert“ zurückgegeben.“

**Wartung**

- Wenn dieser Fehler auftritt, stellen Sie sicher, dass das im Appliancekonfigurations-Manager bereitgestellte Benutzerkonto (domain/local) diesen Gruppen hinzugefügt wurde: Remoteverwaltungsbenutzer, Leistungsmonitorbenutzer und Leistungsprotokollbenutzer.
- Wenn die Gruppe „Remoteverwaltungsbenutzer“ nicht vorhanden ist, fügen Sie der Gruppe ein Benutzerkonto hinzu: WinRMRemoteWMIUsers_.
- Sie können auch überprüfen, ob das WS-Management-Protokoll auf dem Server aktiviert ist, indem Sie den folgenden Befehl an der Eingabeaufforderung des Zielservers ausführen.
    
    ```` winrm qc ````
- Wenn das Problem weiterhin auftritt, stellen Sie sicher, dass das Benutzerkonto über Zugriffsberechtigungen für den CIMV2-Namespace und Unternamespaces in der WMI-Systemsteuerung verfügt. Sie können den Zugriff festlegen, indem Sie die folgenden Schritte ausführen:
    1.  Navigieren Sie zu dem Server, bei dem die Überprüfung auf der Appliance fehlschlägt.
    2.  Suchen Sie im Startmenü nach „Ausführen“, und wählen Sie diese Option aus. Geben Sie im Dialogfeld „Ausführen“ „wmimgmt.msc“ in das Textfeld „Öffnen:“ ein, und drücken Sie die EINGABETASTE.
    3.  Die wmimgmt-Konsole wird geöffnet, in der im linken Bereich „WMI-Steuerung (lokal)“ angezeigt wird. Klicken Sie mit der rechten Maustaste darauf, und wählen Sie im Menü „Eigenschaften“ aus.
    4.  Klicken Sie im Dialogfeld „Eigenschaften von WMI-Steuerung (lokal)“ auf die Registerkarte „Sicherheiten“.
    5.  Erweitern Sie auf der Registerkarte „Sicherheiten“ den Ordner „Root“ in der Namespacestruktur, und wählen Sie den Namespace „cimv2“ aus.
    6.  Klicken Sie auf die Schaltfläche „Sicherheit“, um das Dialogfeld „Sicherheit für ROOT\cimv2“ zu öffnen.
    7.  Klicken Sie im Abschnitt „Gruppen- oder Benutzernamen“ auf die Schaltfläche „Hinzufügen“, um das Dialogfeld „Benutzer, Computer, Dienstkonten oder Gruppen auswählen“ zu öffnen.
    8.  Suchen Sie nach dem Benutzerkonto, wählen Sie es aus, und klicken Sie auf die Schaltfläche „OK“, um zum Dialogfeld „Sicherheit für ROOT\cimv2“ zurückzukehren.
    9.  Wählen Sie im Abschnitt „Gruppen- oder Benutzernamen“ das soeben hinzugefügte Benutzerkonto aus, und überprüfen Sie, ob die folgenden Berechtigungen zulässig sind:<br/>
    Konto aktivieren <br/>
    Remoteaktivierung
    10. Klicken Sie auf „Anwenden“, um die für das Benutzerkonto festgelegten Berechtigungen zu aktivieren.

- Die gleichen Schritte gelten auch für ein lokales Benutzerkonto für Nicht-Domänen-/Arbeitsgruppenserver, aber in einigen Fällen kann [UAC](/windows/win32/wmisdk/user-account-control-and-wmi)-Filterung einige WMI-Eigenschaften blockieren, da die Befehle als Standardbenutzer ausgeführt werden. Sie können also entweder ein lokales Administratorkonto verwenden oder UAC deaktivieren, sodass das lokale Benutzerkonto nicht gefiltert und stattdessen zu einem Volladministrator wird.
- Das Deaktivieren von Remote-UAC durch Ändern des Registrierungseintrags, der Remote-UAC steuert, wird nicht empfohlen, kann aber in einer Arbeitsgruppe erforderlich sein. Der Registrierungseintrag lautet HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\system\LocalAccountTokenFilterPolicy. Wenn der Wert dieses Eintrags null (0) ist, ist Remote-UAC-Zugriffstokenfilterung aktiviert. Wenn der Wert 1 ist, ist Remote-UAC deaktiviert.

## <a name="appliance-is-disconnected"></a>Appliance ist getrennt

**Fehler**

Sie erhalten die Fehlermeldung „Appliance getrennt“, wenn Sie versuchen, Replikation auf einigen VMware-Servern über das Portal zu aktivieren.

Dies kann passieren, wenn sich die Appliance im heruntergefahrenen Zustand befindet oder der DRA-Dienst auf der Appliance nicht mit Azure kommunizieren kann.

**Wartung**

 1. Wechseln Sie zum Appliance-Konfigurations-Manager, und führen Sie die Voraussetzungen erneut aus, um den Status des DRA-Diensts unter **Appliancedienste anzeigen** zu sehen. 
 1. Wenn der Dienst nicht ausgeführt wird, führen Sie das Beenden und Neustarten des Diensts über die Eingabeaufforderung mit den folgenden Befehlen aus:

    ````
    net stop dra
    net start dra
    ````

## <a name="next-steps"></a>Nächste Schritte

Richten Sie eine Appliance für [VMware](how-to-set-up-appliance-vmware.md), [Hyper-V](how-to-set-up-appliance-hyper-v.md) oder [physische Server](how-to-set-up-appliance-physical.md) ein.