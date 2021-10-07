---
title: 'Tutorial: Sichern von SAP HANA-Datenbanken auf virtuellen Azure-Computern'
description: In diesem Tutorial wird beschrieben, wie Sie SAP HANA-Datenbanken, die auf einem virtuellen Azure-Computer ausgeführt werden, in einem Azure Backup Recovery Services-Tresor sichern.
ms.topic: tutorial
ms.date: 09/27/2021
ms.openlocfilehash: 5469c10bb62164e7feea33a1b56cef3457d46efb
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/01/2021
ms.locfileid: "129349688"
---
# <a name="tutorial-back-up-sap-hana-databases-in-an-azure-vm"></a>Tutorial: Sichern von SAP HANA-Datenbanken auf einem virtuellen Azure-Computer

In diesem Tutorial wird veranschaulicht, wie Sie SAP HANA-Datenbanken, die auf virtuellen Azure-Computern ausgeführt werden, in einem Azure Backup Recovery Services-Tresor sichern. In diesem Artikel lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Erstellen und Konfigurieren eines Tresors
> * Ermitteln von Datenbanken
> * Konfigurieren von Sicherungen

[Hier](sap-hana-backup-support-matrix.md#scenario-support) finden Sie alle Szenarien, die von uns derzeit unterstützt werden.

## <a name="prerequisites"></a>Voraussetzungen

Führen Sie vor dem Konfigurieren von Sicherungen unbedingt die folgenden Schritte aus:

* Bestimmen oder erstellen Sie einen [Recovery Services-Tresor](backup-sql-server-database-azure-vms.md#create-a-recovery-services-vault) in derselben Region und demselben Abonnement wie die VM, auf der SAP HANA ausgeführt wird.
* Lassen Sie eine Verbindung des virtuellen Computers mit dem Internet zu, damit er Azure erreichen kann. Dies ist unten im Abschnitt [Einrichten der Netzwerkkonnektivität](#set-up-network-connectivity) beschrieben.
* Stellen Sie sicher, dass der Name der SAP HANA-Server-VM und der Ressourcengruppenname zusammen 84 Zeichen für Azure Resource Manager (VMs vom Typ „ARM_“) bzw. 77 Zeichen für klassische VMs nicht überschreiten. Diese Einschränkung ist darauf zurückzuführen, dass einige Zeichen vom Dienst reserviert werden.
* In **hdbuserstore** sollte ein Schlüssel enthalten sein, der die folgenden Kriterien erfüllt:
  * Er sollte sich im Standardspeicher **hdbuserstore** befinden. Standard ist das Konto `<sid>adm`, unter dem SAP HANA installiert ist.
  * Für MDC sollte der Schlüssel auf den SQL-Port von **NAMESERVER** verweisen. Für SDC sollte er auf den SQL-Port von **INDEXSERVER** verweisen.
  * Es sollten Anmeldeinformationen zum Hinzufügen und Löschen von Benutzern vorhanden sein.
  * Beachten Sie, dass dieser Schlüssel nach der erfolgreichen Ausführung des Vorregistrierungsskripts gelöscht werden kann.
* Sie können auch einen Schlüssel für den vorhandenen HANA SYSTSEM-Benutzer in **hdbuserstore** anstelle eines benutzerdefinierten Schlüssels erstellen, wie im obigen Schritt aufgeführt.
* Führen Sie das Skript für die SAP HANA-Sicherungskonfiguration (Vorregistrierungsskript) auf dem virtuellen Computer, auf dem HANA installiert ist, als Stammbenutzer aus. Mit [diesem Skript](https://go.microsoft.com/fwlink/?linkid=2173610) wird das HANA-System auf die Sicherung vorbereitet. Dabei muss der in den vorherigen Schritten erstellte Schlüssel als Eingabe übergeben werden. Informationen dazu, wie diese Eingabe als Parameter an das Skript übergeben werden soll, finden Sie im Abschnitt [Aufgaben des Vorregistrierungsskripts](#what-the-pre-registration-script-does). In diesem Abschnitt erfahren Sie außerdem mehr über die Funktion des Vorregistrierungsskripts.
* Wenn Ihr HANA-Setup private Endpunkte verwendet, führen Sie das [Vorregistrierungsskript](https://go.microsoft.com/fwlink/?linkid=2173610) mit dem Parameter *-sn* oder *--skip-network-checks* aus.

>[!NOTE]
>Das Vorregistrierungsskript installiert **compat-unixODBC234** für SAP HANA-Workloads, die auf RHEL (7.4, 7.6 und 7.7) ausgeführt werden, sowie **unixODBC** für RHEL 8.1. [Dieses Paket befindet sich im Repository RHEL for SAP HANA (für RHEL 7 Server) Update Services für SAP-Lösungen (RPMs)](https://access.redhat.com/solutions/5094721).  Das Repository für ein Azure Marketplace RHEL-Image wäre **rhui-rhel-sap-hana-for-rhel-7-server-rhui-e4s-rpms**.

## <a name="set-up-network-connectivity"></a>Einrichten der Netzwerkkonnektivität

Eine SAP HANA-Datenbank auf einem virtuellen Azure-Computer benötigt für alle Operationen die Verbindung zum Azure Backup-Dienst, zu Azure Storage und zu Azure Active Directory. Dies kann durch die Verwendung privater Endpunkte oder durch die Gewährung des Zugriffs auf die erforderlichen öffentlichen IP-Adressen oder FQDNs erreicht werden. Wenn keine ordnungsgemäße Verbindung zu den erforderlichen Azure-Diensten zugelassen wird, kann dies zu Fehlern bei Operationen wie der Datenbankermittlung, der Konfiguration von Sicherungen, der Durchführung von Sicherungen und der Wiederherstellung von Daten führen.

In der folgenden Tabelle sind die verschiedenen Alternativen zum Herstellen der Konnektivität aufgeführt:

| **Option**                        | **Vorteile**                                               | **Nachteile**                                            |
| --------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Private Endpunkte                 | Sicherungen über private IPs innerhalb des virtuellen Netzwerks möglich  <br><br>   Genau abgestimmte Zugriffssteuerung auf Netzwerk- und Tresorseite | Standardmäßige [Kosten](https://azure.microsoft.com/pricing/details/private-link/) für private Endpunkte |
| NSG-Diensttags                  | Einfachere Verwaltung, da Bereichsänderungen automatisch zusammengeführt werden   <br><br>   Keine zusätzlichen Kosten | Nur mit NSGs möglich  <br><br>    Zugriff auf den gesamten Dienst |
| FQDN-Tags von Azure Firewall          | Einfachere Verwaltung, da die erforderlichen FQDNs automatisch verwaltet werden | Nur mit Azure Firewall möglich                         |
| Zugriff auf Dienst-FQDNs/-IPs | Keine zusätzlichen Kosten   <br><br>  Verwendung mit allen Netzwerksicherheits-Appliances und Firewalls möglich | Möglicherweise ist ein umfassender Satz von IP-Adressen oder FQDNs erforderlich.   |
| Verwenden eines HTTP-Proxys                 | Zentraler Internetzugriffspunkt für VMs                       | Zusätzliche Kosten für das Ausführen einer VM mit der Proxysoftware         |
| [Virtual Network-Dienstendpunkt](/azure/virtual-network/virtual-network-service-endpoints-overview)    |     Kann für den Azure Storage (= Recovery Services-Tresor) verwendet werden.     <br><br>     Bietet einen großen Vorteil für die Optimierung der Leistung des Datenverkehrs auf Datenebene.          |         Kann nicht für Azure AD, Azure Backup verwendet werden.    |
| Virtuelles Netzwerkgerät      |      Kann für Azure Storage, Azure AD, Azure Backup verwendet werden. <br><br> **Datenebene**   <ul><li>      Azure Storage: `*.blob.core.windows.net`, `*.queue.core.windows.net`  </li></ul>   <br><br>     **Verwaltungsebene**  <ul><li>  Azure AD: Lassen Sie den Zugriff auf FQDNs zu, die in den Abschnitten 56 und 59 von [Microsoft 365 Common und Office Online](/microsoft-365/enterprise/urls-and-ip-address-ranges?view=o365-worldwide&preserve-view=true#microsoft-365-common-and-office-online) beschrieben sind. </li><li>   Azure Backup-Dienst: `.backup.windowsazure.com` </li></ul> <br>Erfahren Sie mehr über [Azure Firewall-Diensttags](/azure/firewall/fqdn-tags#:~:text=An%20FQDN%20tag%20represents%20a%20group%20of%20fully,the%20required%20outbound%20network%20traffic%20through%20your%20firewall.).       |  Erhöht den Mehraufwand für den Datenverkehr auf Datenebene und verringert den Durchsatz bzw. die Leistung.  |

Weitere Informationen zur Verwendung dieser Optionen finden Sie unten:

### <a name="private-endpoints"></a>Private Endpunkte

Private Endpunkte ermöglichen Ihnen, sichere Verbindungen von Servern in einem virtuellen Netzwerk mit Ihrem Recovery Services-Tresor herzustellen. Der private Endpunkt verwendet eine private IP-Adresse aus dem VNET-Adressraum für Ihren Tresor. Der Netzwerkdatenverkehr zwischen Ihren Ressourcen innerhalb des virtuellen Netzwerks und dem Tresor wird über das virtuelle Netzwerk und eine private Verbindung im Microsoft-Backbone-Netzwerk übertragen. Dadurch wird er vom öffentlichen Internet isoliert. Ein privater Endpunkt wird einem bestimmten Subnetz eines virtuellen Netzwerks zugewiesen und kann nicht für Azure Active Directory verwendet werden. Weitere Informationen zu privaten Endpunkten für Azure Backup finden Sie [hier](./private-endpoints.md).

### <a name="nsg-tags"></a>NSG-Tags

Wenn Sie Netzwerksicherheitsgruppen (NSG) verwenden, können Sie den ausgehenden Zugriff auf Azure Backup mithilfe des Diensttags *AzureBackup* zulassen. Zusätzlich zum Tag „Azure Backup“ müssen Sie auch Konnektivität für Authentifizierung und Datenübertragung zulassen, indem Sie ähnliche [NSG-Regeln](../virtual-network/network-security-groups-overview.md#service-tags) für Azure AD (*AzureActiveDirectory*) und Azure Storage (*Storage*) erstellen. Die folgenden Schritte beschreiben das Vorgehen zum Erstellen einer Regel für das Azure Backup-Tag:

1. Navigieren Sie unter **Alle Dienste** zu **Netzwerksicherheitsgruppen**, und wählen Sie die Netzwerksicherheitsgruppe aus.

1. Wählen Sie unter **Einstellungen** die Option **Ausgangssicherheitsregeln** aus.

1. Wählen Sie **Hinzufügen**. Geben Sie die erforderlichen Informationen zum Erstellen einer neuen Regel ein, wie unter [Einstellungen zu Sicherheitsregeln](../virtual-network/manage-network-security-group.md#security-rule-settings) beschrieben. Stellen Sie sicher, dass die Option **Ziel** auf *Diensttag* und **Zieldiensttag** auf *AzureBackup* festgelegt wurde.

1. Wählen Sie **Hinzufügen** aus, um die neu erstellte Ausgangssicherheitsregel zu speichern.

Auf ähnliche Weise können Sie [NSG-Ausgangssicherheitsregeln](../virtual-network/network-security-groups-overview.md#service-tags) für Azure Storage und Azure AD erstellen. Weitere Informationen zu Diensttags finden Sie in [diesem Artikel](../virtual-network/service-tags-overview.md).

### <a name="azure-firewall-tags"></a>Azure Firewall-Tags

Wenn Sie Azure Firewall verwenden, erstellen Sie eine Anwendungsregel mithilfe des [Azure Firewall-FQDN-Tags](../firewall/fqdn-tags.md) *AzureBackup*. Dies erlaubt sämtlichen ausgehenden Zugriff auf Azure Backup.

### <a name="allow-access-to-service-ip-ranges"></a>Zulassen des Zugriffs auf Dienst-IP-Bereiche

Wenn Sie sich dafür entscheiden, Zugriffsdienst-IPs zuzulassen, beziehen Sie sich auf die IP-Bereiche in der [hier](https://www.microsoft.com/download/confirmation.aspx?id=56519) verfügbaren JSON-Datei. Sie müssen den Zugriff auf IPs für Azure Backup, Azure Storage und Azure Active Directory zulassen.

### <a name="allow-access-to-service-fqdns"></a>Zugriff auf Dienst-FQDNs

Sie können auch die folgenden FQDNs verwenden, um den Zugriff auf die erforderlichen Dienste von ihren Servern aus zuzulassen:

| Dienst    | Domänennamen, auf die zugegriffen werden soll                             |
| -------------- | ------------------------------------------------------------ |
| Azure Backup  | `*.backup.windowsazure.com`                             |
| Azure Storage | `*.blob.core.windows.net` <br><br> `*.queue.core.windows.net` |
| Azure AD      | Gewähren des Zugriffs auf FQDNs gemäß Abschnitt 56 und 59, wie in [diesem Artikel](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online) beschrieben |

### <a name="use-an-http-proxy-server-to-route-traffic"></a>Verwenden eines HTTP-Proxyservers für das Weiterleiten von Datenverkehr

Wenn Sie eine SAP HANA-Datenbank auf einem virtuellen Azure-Computer sichern, verwendet die Sicherungserweiterung auf dem virtuellen Computer die HTTPS-APIs, um Verwaltungsbefehle an Azure Backup und Daten an Azure Storage zu senden. Die Sicherungserweiterung verwendet auch Azure AD zur Authentifizierung. Leiten Sie den Datenverkehr der Sicherungserweiterung für diese drei Dienste über den HTTP-Proxy weiter. Verwenden Sie die oben genannte Liste der IPs und FQDNs, um den Zugriff auf die erforderlichen Dienste zuzulassen. Authentifizierte Proxyserver werden nicht unterstützt.

## <a name="understanding-backup-and-restore-throughput-performance"></a>Grundlegendes zur Durchsatzleistung bei der Sicherung und Wiederherstellung

Bei den über Backint bereitgestellten Sicherungen (Protokoll und protokollfremd) für virtuelle SAP HANA-Azure-Computer handelt es sich um Streams zu Azure Recovery Services-Tresoren (für die intern Azure Storage Blob genutzt wird). Daher ist es wichtig, diese Streamingmethodik zu verstehen.

Die Backint-Komponente von HANA stellt die Pipes (eine Pipe für Lese- und eine für Schreibvorgänge) bereit. Diese sind mit zugrunde liegenden Datenträgern verbunden, auf denen sich Datenbankdateien befinden, die dann durch den Azure Backup-Dienst gelesen und an einen Azure Recovery Services-Tresor übertragen werden. Hierbei handelt es sich um ein Azure Storage-Konto. Vom Azure Backup-Dienst wird neben den nativen Validierungsüberprüfungen von Backint auch eine Prüfsumme zum Überprüfen der Datenströme erstellt. Durch diese Überprüfungen wird sichergestellt, dass die im Azure Recovery Services-Tresor enthaltenen Daten zuverlässig und wiederherstellbar sind.

Da für die Streams hauptsächlich Datenträger verwendet werden, müssen Sie für Lesevorgänge mit der Datenträgerleistung und für die Übertragung von Sicherungsdaten mit der Netzwerkleistung vertraut sein, um die Leistung der Sicherungs- und Wiederherstellungsvorgänge messen zu können. Ausführliche Informationen zum Durchsatz und zur Leistung von Datenträgern bzw. Netzwerken bei virtuellen Azure-Computern finden Sie in [diesem Artikel](../virtual-machines/disks-performance.md). Diese Informationen sind auf die Sicherungs- und Wiederherstellungsleistung übertragbar.

**Der Azure Backup-Dienst versucht, bei protokollfremden Sicherungen für HANA (beispielsweise vollständig, differenziell oder inkrementell) eine Geschwindigkeit von bis zu ~420 MBit/s und bei Protokollsicherungen für HANA eine Geschwindigkeit von bis zu 100 MBit/s zu erreichen.** Wie bereits erwähnt, können diese Geschwindigkeiten nicht garantiert werden und hängen von folgenden Faktoren ab:

- Maximaler Datenträgerdurchsatz des virtuellen Computers ohne Zwischenspeicherung: Lesen aus dem Daten- oder Protokollbereich.
- Zugrunde liegender Datenträgertyp und zugehöriger Durchsatz: Lesen aus dem Daten- oder Protokollbereich.
- Maximaler Netzwerkdurchsatz des virtuellen Computers: Schreiben in den Recovery Services-Tresor.
- Wenn das VNET über NVA/Firewall verfügt: zugehöriger Netzwerkdurchsatz
- Bei Daten/Anmeldung für Azure NetApp Files: Bei Lesevorgängen aus ANF und Schreibvorgängen in den Tresor wird Netzwerkbandbreite des virtuellen Computers genutzt.

> [!IMPORTANT]
> Bei kleineren virtuellen Computern, bei denen der Durchsatz des Datenträgers ohne Cache sehr nahe bei oder unter 400 MBit/s liegt, gibt es ggf. Bedenken, dass die gesamten Datenträger-IOPS durch den Sicherungsdienst beansprucht werden, wodurch unter Umständen Lese- und Schreibvorgänge von SAP HANA auf den Datenträgern beeinträchtigt werden. Im nächsten Abschnitt erfahren Sie, wie Sie in diesem Fall den Verbrauch des Sicherungsdiensts drosseln oder auf den maximal zulässigen Grenzwert beschränken können.

### <a name="limiting-backup-throughput-performance"></a>Beschränken der Durchsatzleistung bei der Sicherung

Wenn den Datenträger-IOPS-Verbrauch des Sicherungsdiensts auf einen Höchstwert beschränken möchten, führen Sie die folgenden Schritte aus:

1. Navigieren Sie zum Ordner „opt/msawb/bin“.
2. Erstellen Sie eine neue JSON-Datei namens „ExtensionSettingOverrides.JSON“.
3. Fügen Sie der JSON-Datei ein Schlüssel-Wert-Paar hinzu:

    ```json
    {
    "MaxUsableVMThroughputInMBPS": 200
    }
    ```

4. Ändern Sie die Berechtigungen und den Besitz der Datei:
    
    ```bash
    chmod 750 ExtensionSettingsOverrides.json
    chown root:msawb ExtensionSettingsOverrides.json
    ```

5. Es muss kein Dienst neu gestartet werden. Vom Azure Backup-Dienst wird versucht, die Durchsatzleistung gemäß den Angaben in dieser Datei zu begrenzen.

## <a name="what-the-pre-registration-script-does"></a>Aufgaben des Vorregistrierungsskripts

Beim Ausführen des Skripts für die Vorregistrierung wird Folgendes durchgeführt:

* Je nach Ihrer Linux-Distribution werden alle Pakete installiert bzw. aktualisiert, die vom Azure Backup-Agent benötigt werden.
* Es werden Konnektivitätsprüfungen in ausgehender Richtung mit Azure Backup-Servern und abhängigen Diensten wie Azure Active Directory und Azure Storage durchgeführt.
* Die Anmeldung bei Ihrem HANA-System wird unter Verwendung des benutzerdefinierten Benutzerschlüssels oder des SYSTEM-Benutzerschlüssels (siehe [Voraussetzungen](#prerequisites)) durchgeführt. Dieser Schlüssel wird zum Erstellen eines Sicherungsbenutzers (AZUREWLBACKUPHANAUSER) im HANA-System verwendet, und der Benutzerschlüssel kann gelöscht werden, nachdem das Vorregistrierungsskript erfolgreich ausgeführt wurde. _Beachten Sie, dass der SYSTEM-Benutzerschlüssel nicht gelöscht werden darf._
* AZUREWLBACKUPHANAUSER werden die folgenden erforderlichen Rollen und Berechtigungen zugewiesen:
  * Für MDC: DATABASE ADMIN (DATENBANKADMINISTRATOR) und BACKUP ADMIN (SICHERUNGSADMINISTRATOR, ab HANA 2.0 SPS05): Erstellen neuer Datenbanken während der Wiederherstellung
  * Für SDC: BACKUP ADMIN: Erstellen neuer Datenbanken während der Wiederherstellung
  * CATALOG READ: Lesen des Sicherungskatalogs
  * SAP_INTERNAL_HANA_SUPPORT: Zugreifen auf einige private Tabellen Nur erforderlich für SDC- und MDC-Versionen unter HANA 2.0 SPS04 Rev 46. Dies ist nicht erforderlich für HANA 2.0 SPS04 Rev 46 und höhere Versionen, da die benötigten Informationen aus öffentlichen Tabellen nun mit dem Fix des HANA-Teams abgerufen werden.
* Das Skript fügt einen Schlüssel zu **hdbuserstore** für AZUREWLBACKUPHANAUSER für das HANA-Sicherungs-Plug-In hinzu, um alle Vorgänge (Datenbankabfragen, Wiederherstellungsvorgänge, Konfigurieren und Ausführen von Sicherungen) zu behandeln.
* Alternativ können Sie auch einen eigenen benutzerdefinierten Backup-Benutzer erstellen. Stellen Sie sicher, dass diesem Benutzer die folgenden erforderlichen Rollen und Berechtigungen zugewiesen sind:
  * Für MDC: DATABASE ADMIN (DATENBANKADMINISTRATOR) und BACKUP ADMIN (SICHERUNGSADMINISTRATOR, ab HANA 2.0 SPS05): Erstellen neuer Datenbanken während der Wiederherstellung
  * Für SDC: BACKUP ADMIN: Erstellen neuer Datenbanken während der Wiederherstellung
  * CATALOG READ: Lesen des Sicherungskatalogs
  * SAP_INTERNAL_HANA_SUPPORT: Zugreifen auf einige private Tabellen Nur erforderlich für SDC- und MDC-Versionen unter HANA 2.0 SPS04 Rev 46. Dies ist nicht erforderlich für HANA 2.0 SPS04 Rev 46 und höhere Versionen, da die benötigten Informationen aus öffentlichen Tabellen nun mit dem Fix des HANA-Teams abgerufen werden.
* Fügen Sie dann einen Schlüssel zu „hdbuserstore“ für Ihren benutzerdefinierten Backup-Benutzer für das HANA-Sicherungs-Plug-In hinzu, um alle Vorgänge (Datenbankabfragen, Wiederherstellungsvorgänge, Konfigurieren und Ausführen von Sicherungen) zu behandeln. Übergeben Sie diesen benutzerdefinierten Backup-Benutzerschlüssel als Parameter an das Skript: `-bk CUSTOM_BACKUP_KEY_NAME` oder `-backup-key CUSTOM_BACKUP_KEY_NAME`.  _Beachten Sie, dass der Ablauf des Kennworts dieses benutzerdefinierten Sicherungsschlüssels zu Sicherungs- und Wiederherstellungsfehlern führen kann._

>[!NOTE]
> Führen Sie den Befehl `bash msawb-plugin-config-com-sap-hana.sh --help` aus, um zu erfahren, welche anderen Parameter vom Skript akzeptiert werden.

Um die Schlüsselerstellung zu bestätigen, führen Sie auf dem HANA-Computer den Befehl HDBSQL mit SIDADM-Anmeldeinformationen aus:

```hdbsql
hdbuserstore list
```

Die Ausgabe des Befehls sollte den Schlüssel {SID} {DBNAME} und den Benutzer AZUREWLBACKUPHANAUSER enthalten.

>[!NOTE]
> Stellen Sie sicher, dass sich unter `/usr/sap/{SID}/home/.hdb/` eine eindeutige Gruppe von SSFS-Dateien befindet. In diesem Pfad darf nur ein Ordner vorhanden sein.

Nachfolgend finden Sie eine Zusammenfassung der Schritte, die zum Abschließen der Ausführung des Vorregistrierungsskripts erforderlich sind. Beachten Sie, dass Sie in diesem Flow den SYSTEM-Benutzerschlüssel als Eingabeparameter für das Vorregistrierungsskript bereitstellen.

| Wer     |    From    |    Auszuführendes Programm    |    Kommentare    |
| --- | --- | --- | --- |
| `<sid>`adm (Betriebssystem) |    HANA-Betriebssystem   | Lesen Sie das Tutorial, und laden Sie das Vorregistrierungsskript herunter.  |    Tutorial: [Sichern von SAP HANA-Datenbanken auf virtuellen Azure-Computern](/azure/backup/tutorial-backup-sap-hana-db)   <br><br>    Laden Sie das [Vorregistrierungsskript](https://go.microsoft.com/fwlink/?linkid=2173610) herunter. |
| `<sid>`adm (Betriebssystem)    |    HANA-Betriebssystem    |   Starten Sie HANA (HDB-Start).    |   Stellen Sie vor der Einrichtung sicher, dass HANA betriebsbereit ist und ausgeführt wird.   |
| `<sid>`adm (Betriebssystem)   |   HANA-Betriebssystem  |    Führen Sie den folgenden Befehl aus: <br>  `hdbuserstore Set`   |  `hdbuserstore Set SYSTEM <hostname>:3<Instance#>13 SYSTEM <password>`  <br><br>   **Hinweis** <br>  Stellen Sie sicher, dass Sie nicht die IP-Adresse bzw. den FQDN, sondern den Hostnamen verwenden.   |
| `<sid>`adm (Betriebssystem)   |  HANA-Betriebssystem    |   Führen Sie den folgenden Befehl aus:<br> `hdbuserstore List`   |  Überprüfen Sie, ob das Ergebnis wie folgt den Standardspeicher enthält: <br><br> `KEY SYSTEM`  <br> `ENV : <hostname>:3<Instance#>13`    <br>  `USER : SYSTEM`   |
| Root (Betriebssystem)   |   HANA-Betriebssystem    |    Führen Sie das [HANA-Vorregistrierungsskript für Azure Backup](https://go.microsoft.com/fwlink/?linkid=2173610) aus.     | `./msawb-plugin-config-com-sap-hana.sh -a --sid <SID> -n <Instance#> --system-key SYSTEM`    |
| `<sid>`adm (Betriebssystem)   |   HANA-Betriebssystem   |    Führen Sie den folgenden Befehl aus: <br> `hdbuserstore List`   |   Überprüfen Sie, ob das Ergebnis wie folgt neue Zeilen enthält: <br><br>  `KEY AZUREWLBACKUPHANAUSER` <br>  `ENV : localhost: 3<Instance#>13`   <br> `USER: AZUREWLBACKUPHANAUSER`    |
| Azure-Mitwirkender     |    Azure-Portal    |   Konfigurieren Sie die NSG, NVA, Azure Firewall usw., um ausgehenden Datenverkehr zum Azure Backup-Dienst, Azure AD und Azure Storage zuzulassen.     |    [Einrichten der Netzwerkkonnektivität](/azure/backup/tutorial-backup-sap-hana-db#set-up-network-connectivity)    |
| Azure-Mitwirkender |   Azure-Portal    |   Erstellen oder öffnen Sie einen Recovery Services-Tresor, und wählen Sie dann die HANA-Sicherung aus.   |   Suchen Sie nach allen virtuellen HANA-Zielcomputern, die gesichert werden sollen.   |
| Azure-Mitwirkender    |   Azure-Portal    |   Ermitteln Sie HANA-Datenbanken, und konfigurieren Sie die Sicherungsrichtlinie.   |  Beispiel: <br><br>  Wöchentliche Sicherung: Jeden Sonntag um 2:00 Uhr, wöchentliche, monatliche und jährliche Aufbewahrung (12 Wochen, 12 Monate bzw. 3 Jahre)   <br>   Differenziell oder inkrementell: Jeden Tag, Sonntag ausgenommen    <br>   Protokoll: alle 15 Minuten mit Aufbewahrungsdauer von 35 Tagen    |
| Azure-Mitwirkender  |   Azure-Portal    |    Recovery Services-Tresor: Sicherungselemente: SAP HANA     |   Überprüfen Sie die Sicherungsaufträge (Azure-Workload).    |
| HANA-Administrator    | HANA Studio   | Überprüfen Sie die Sicherungskonsole, den Sicherungskatalog, „backup.log“, „backint.log“ und „global.ini“.   |    Sowohl SYSTEMDB als auch Mandantendatenbank.   |

Nach erfolgreicher Ausführung des Vorregistrierungsskripts und anschließender Überprüfung können Sie die [Konnektivitätsanforderungen](#set-up-network-connectivity) überprüfen und dann über den Recovery Services-Tresor die [Sicherung konfigurieren](#discover-the-databases).

## <a name="create-a-recovery-services-vault"></a>Erstellen eines Recovery Services-Tresors

Bei einem Recovery Services-Tresor handelt es sich um eine Entität, in der alle im Laufe der Zeit erstellten Sicherungen und Wiederherstellungspunkte gespeichert werden. Der Recovery Services-Tresor enthält auch die Sicherungsrichtlinien, die den geschützten virtuellen Computern zugeordnet sind.

So erstellen Sie einen Recovery Services-Tresor

1. Melden Sie sich bei Ihrem Abonnement im [Azure-Portal](https://portal.azure.com/) an.

2. Wählen Sie im Menü links die Option **Alle Dienste** aus.

   ![Auswählen von „Alle Dienste“](./media/tutorial-backup-sap-hana-db/all-services.png)

3. Geben Sie im Dialogfeld **Alle Dienste** **Recovery Services** ein. Die Liste der Ressourcen wird Ihrer Eingabe entsprechend gefiltert. Wählen Sie in der Liste der Ressourcen **Recovery Services-Tresore** aus.

   ![Auswählen von Recovery Services-Tresoren](./media/tutorial-backup-sap-hana-db/recovery-services-vaults.png)

4. Wählen Sie im Dashboard **Recovery Services-Tresore** die Option **Hinzufügen** aus.

   ![Hinzufügen des Recovery Services-Tresors](./media/tutorial-backup-sap-hana-db/add-vault.png)

   Das Dialogfeld **Recovery Services-Tresor** wird geöffnet. Geben Sie Werte für **Name/Abonnement/Ressourcengruppe** und **Speicherort** an.

   ![Erstellen eines Recovery Services-Tresors](./media/tutorial-backup-sap-hana-db/create-vault.png)

   * **Name**: Der Name wird zum Identifizieren des Recovery Services-Tresors verwendet und muss für das Azure-Abonnement eindeutig sein. Geben Sie einen Namen ein, der mindestens zwei, aber nicht mehr als 50 Zeichen enthält. Der Name muss mit einem Buchstaben beginnen und darf nur Buchstaben, Zahlen und Bindestriche enthalten. In diesem Tutorial haben wir den Namen **SAPHanaVault** verwendet.
   * **Abonnement**: Wählen Sie das zu verwendende Abonnement aus. Wenn Sie nur in einem Abonnement Mitglied sind, wird dessen Name angezeigt. Falls Sie nicht sicher sind, welches Abonnement geeignet ist, können Sie das Standardabonnement bzw. das vorgeschlagene Abonnement verwenden. Es sind nur dann mehrere Auswahlmöglichkeiten verfügbar, wenn Ihr Geschäfts-, Schul- oder Unikonto mehreren Azure-Abonnements zugeordnet ist. Hier haben wir das Abonnement **SAP HANA Solution Lab** verwendet.
   * **Ressourcengruppe**: Verwenden Sie eine vorhandene Ressourcengruppe, oder erstellen Sie eine neue Ressourcengruppe. Hier haben wir **SAPHANADemo** verwendet.<br>
   Um eine Liste der verfügbaren Ressourcengruppen in Ihrem Abonnement anzuzeigen, wählen Sie **Vorhandene verwenden** und dann eine Ressource im Dropdownlistenfeld aus. Wählen Sie zum Erstellen einer neuen Ressourcengruppe **Neu erstellen** aus, und geben Sie den Namen ein. Umfassende Informationen zu Ressourcengruppen finden Sie unter [Übersicht über den Azure Resource Manager](../azure-resource-manager/management/overview.md).
   * **Standort**: Wählen Sie die geografische Region für den Tresor aus. Die Region des Tresors muss mit der Region des virtuellen Computers übereinstimmen, auf dem SAP HANA ausgeführt wird. Wir haben **USA, Osten 2** verwendet.

5. Klicken Sie auf **Überprüfen + erstellen**.

   ![Auswählen von „Bewerten + erstellen“](./media/tutorial-backup-sap-hana-db/review-create.png)

Der Recovery Services-Tresor wird jetzt erstellt.

## <a name="enable-cross-region-restore"></a>Aktivieren der regionsübergreifenden Wiederherstellung

Im Recovery Services-Tresor können Sie die regionsübergreifende Wiederherstellung aktivieren. Sie müssen die regionsübergreifende Wiederherstellung aktivieren, bevor Sie Sicherungen in Ihren HANA-Datenbanken konfigurieren und schützen. Erfahren Sie, [wie Sie die regionsübergreifende Wiederherstellung aktivieren](/azure/backup/backup-create-rs-vault#set-cross-region-restore).

[Erfahren Sie mehr über regionsübergreifende Wiederherstellung.](/azure/backup/backup-azure-recovery-services-vault-overview)

## <a name="discover-the-databases"></a>Ermitteln der Datenbanken

1. Wählen Sie im Tresor unter **Erste Schritte** die Option **Sicherung** aus. Wählen Sie unter **Wo wird Ihre Workload ausgeführt?** den Eintrag **SAP HANA in Azure-VM** aus.
2. Wählen Sie **Ermittlung starten** aus. Dadurch wird die Ermittlung nicht geschützter Linux-VMs in der Tresorregion initiiert. Der zu schützende virtuelle Azure-Computer wird angezeigt.
3. Wählen Sie im Bereich **Virtuelle Computer auswählen** den Link zum Herunterladen des Skripts aus, das dem Azure Backup-Dienst die Berechtigungen zum Zugreifen auf die SAP HANA-VMs für die Ermittlung von Datenbanken erteilt.
4. Führen Sie das Skript auf jedem virtuellen Computer aus, auf dem die zu sichernden SAP HANA-Datenbanken gehostet werden.
5. Wählen Sie nach dem Ausführen des Skripts auf der VM im Bereich **Virtuelle Computer auswählen** die VM aus. Wählen Sie dann **DBs ermitteln** aus.
6. Azure Backup ermittelt alle SAP HANA-Datenbanken auf dem virtuellen Computer. Während der Ermittlung registriert Azure Backup den virtuellen Computer beim Tresor und installiert eine Erweiterung auf dem VM. Für die Datenbank wird kein Agent installiert.

   ![Ermitteln der Datenbanken](./media/tutorial-backup-sap-hana-db/database-discovery.png)

## <a name="configure-backup"></a>Konfigurieren der Sicherung

Nachdem die zu sichernden Datenbanken ermittelt wurden, aktivieren wir die Sicherung.

1. Wählen Sie **Sicherung konfigurieren** aus.

   ![Konfigurieren der Sicherung](./media/tutorial-backup-sap-hana-db/configure-backup.png)

2. Wählen Sie unter **Elemente für die Sicherung auswählen** mindestens eine Datenbank, die Sie schützen möchten, und anschließend **OK** aus.

   ![Auswählen von Elementen für die Sicherung](./media/tutorial-backup-sap-hana-db/select-items-to-backup.png)

3. Erstellen Sie unter **Sicherungsrichtlinie > Sicherungsrichtlinie auswählen** eine neue Sicherungsrichtlinie für die Datenbank(en), indem Sie die Anleitung im nächsten Abschnitt befolgen.

   ![Auswählen der Sicherungsrichtlinie](./media/tutorial-backup-sap-hana-db/backup-policy.png)

4. Wählen Sie nach dem Erstellen der Richtlinie im Menü **Sicherung** die Option **Sicherung aktivieren** aus.

   ![Auswählen von „Sicherung aktivieren“](./media/tutorial-backup-sap-hana-db/enable-backup.png)

5. Verfolgen Sie den Fortschritt der Sicherungskonfiguration im Bereich **Benachrichtigungen** des Portals.

## <a name="creating-a-backup-policy"></a>Erstellen einer Sicherungsrichtlinie

Eine Sicherungsrichtlinie legt fest, wann Sicherungen erstellt und wie lange sie aufbewahrt werden.

* Eine Richtlinie wird auf Tresorebene erstellt.
* Mehrere Tresore können die gleiche Sicherungsrichtlinie verwenden. Allerdings müssen Sie die Sicherungsrichtlinie auf jeden Tresor anwenden.

Legen Sie die Richtlinieneinstellungen wie folgt fest:

1. Geben Sie unter **Richtlinienname** einen Namen für die neue Richtlinie ein. Geben Sie für diesen Fall **SAPHANA** ein.

   ![Eingeben eines Namens für die neue Richtlinie](./media/tutorial-backup-sap-hana-db/new-policy.png)

2. Wählen Sie in **Richtlinie für vollständige Sicherung** eine **Sicherungshäufigkeit** aus. Sie können **Täglich** oder **Wöchentlich** auswählen. Für dieses Tutorial verwenden wir für die Sicherung die Option **Täglich**.

   ![Auswählen einer Sicherungshäufigkeit](./media/tutorial-backup-sap-hana-db/backup-frequency.png)

3. Konfigurieren Sie unter **Beibehaltungsdauer** die Aufbewahrungseinstellungen für die vollständige Sicherung.
   * Standardmäßig sind alle Optionen ausgewählt. Deaktivieren Sie alle Optionen für die Beibehaltungsdauer, die Sie nicht verwenden möchten, und legen Sie die gewünschten Grenzwerte fest.
   * Die Mindestaufbewahrungsdauer beträgt für alle Sicherungstypen (vollständig/differenziell/Protokoll) sieben Tage.
   * Wiederherstellungspunkte werden unter Berücksichtigung ihrer Beibehaltungsdauer mit einer Markierung versehen. Wenn Sie beispielsweise eine tägliche vollständige Sicherung wählen, wird pro Tag nur eine vollständige Sicherung ausgelöst.
   * Die Sicherung für einen bestimmten Tag wird auf Grundlage der wöchentlichen Beibehaltungsdauer und der entsprechenden Einstellung markiert und beibehalten.
   * Mit der monatlichen und jährlichen Beibehaltungsdauer verhält es sich ähnlich.
4. Wählen Sie im Menü **Richtlinie für vollständige Sicherung** **OK** aus, um die Einstellungen zu übernehmen.
5. Wählen Sie anschließend **Differenzielle Sicherung** aus, um eine Richtlinie für eine differenzielle Sicherung hinzuzufügen.
6. Wählen Sie in **Richtlinie für differenzielle Sicherung** die Option **Aktivieren** aus, um die Einstellungen für Häufigkeit und Beibehaltung vorzunehmen. Wir haben eine differenzielle Sicherung mit den folgenden Angaben aktiviert: jeden **Sonntag** um **2:00 Uhr** mit einer Aufbewahrungsdauer von **30 Tagen**.

   ![Richtlinie für differenzielle Sicherung](./media/tutorial-backup-sap-hana-db/differential-backup-policy.png)

   >[!NOTE]
   >Sie können entweder „differenziell“ oder „inkrementell“ für die tägliche Sicherung auswählen, aber nicht beide Optionen.

7. Wählen Sie unter **Richtlinie zur inkrementellen Sicherung** die Option **Aktivieren** aus, um die Einstellungen für Häufigkeit und Beibehaltung vorzunehmen.
    * Pro Tag kann höchstens eine inkrementelle Sicherung ausgelöst werden.
    * Inkrementelle Sicherungen können maximal 180 Tage aufbewahrt werden. Wenn Sie eine längere Aufbewahrung wünschen, müssen Sie vollständige Sicherungen verwenden.

    ![Richtlinie zur inkrementellen Sicherung](./media/backup-azure-sap-hana-database/incremental-backup-policy.png)

8. Wählen Sie **OK** aus, um die Richtlinie zu speichern und zum Hauptmenü **Sicherungsrichtlinie** zurückzukehren.
9. Wählen Sie **Protokollsicherung** aus, um eine Richtlinie für eine Transaktionsprotokollsicherung hinzuzufügen.
   * **Protokollsicherung** ist standardmäßig auf **Aktivieren** festgelegt. Diese Option kann nicht deaktiviert werden, da SAP HANA alle Protokollsicherungen verwaltet.
   * Wir haben **2 Stunden** als Sicherungszeitplan und **15 Tage** als Aufbewahrungszeitraum angegeben.

    ![Richtlinie für Protokollsicherung](./media/tutorial-backup-sap-hana-db/log-backup-policy.png)

   >[!NOTE]
   > Protokollsicherungen werden erst nach erfolgreichem Abschluss einer vollständigen Sicherung übertragen.
   >

10. Wählen Sie **OK** aus, um die Richtlinie zu speichern und zum Hauptmenü **Sicherungsrichtlinie** zurückzukehren.
11. Wählen Sie nach dem Definieren der Sicherungsrichtlinie **OK** aus.

Sie haben nun erfolgreich eine Sicherung für Ihre SAP HANA-Datenbank(en) konfiguriert.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über das [Ausführen einer On-Demand-Sicherung für SAP HANA-Datenbanken auf Azure-VMs](backup-azure-sap-hana-database.md#run-an-on-demand-backup).
* Erfahren Sie, wie Sie [nur auf virtuellen Azure-Computern ausgeführte SAP HANA-Datenbanken wiederherstellen können](sap-hana-db-restore.md).
* [Verwalten und Überwachen gesicherter SAP HANA-Datenbanken](sap-hana-db-manage.md)
* [Behandeln von Problemen beim Sichern von SAP HANA-Datenbanken in Azure](backup-azure-sap-hana-database-troubleshoot.md)
