---
title: Bereitstellung der SAP BusinessObjects BI-Plattform in Azure für Windows | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie die SAP BusinessObjects BI-Plattform in Azure für Windows bereitstellen und konfigurieren.
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,azure-sql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/08/2021
ms.author: depadia
ms.openlocfilehash: d230c000eedf4cb6a4d01b7dc20f48e5712862c4
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130225084"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-windows-on-azure"></a>Bereitstellungsleitfaden für die SAP BusinessObjects BI-Plattform für Windows in Azure

In diesem Artikel wird die Strategie für die Bereitstellung der SAP BusinessObjects Business Intelligence-Plattform (SAP BOBI) in Azure für Windows beschrieben. Im Rahmen des verwendeten Beispiels werden zwei virtuelle Computer (VMs) mit Azure SSD Premium Managed Disks als Installationsverzeichnis konfiguriert. Azure SQL-Datenbank – ein PaaS-Angebot (Platform-as-a-Service) – wird für die CMS- (Central Management Server) und die Überwachungsdatenbank verwendet. Azure Files Premium – ein SMB-Protokoll – wird als Dateispeicher genutzt, der von beiden VMs gemeinsam verwendet wird. Die standardmäßige Tomcat Java-Webanwendung und die BI-Plattformanwendung (Business Intelligence) werden zusammen auf beiden VMs installiert. Für den Lastenausgleich der Benutzeranforderungen wird das Azure Application Gateway verwendet, das native TLS/SSL-Auslagerungsfunktionen besitzt.

Diese Art von Architektur eignet sich für kleinere Bereitstellungen oder Nichtproduktionsumgebungen. Für die Bereitstellung in der Produktion bzw. im großen Stil sollten Sie die Hosts für Webanwendungen trennen. Darüber hinaus können Sie auch mehrere SAP BOBI-Anwendungshosts verwenden, damit vom Server mehr Informationen verarbeitet werden können.

![Diagramm: SAP BOBI-Bereitstellung in Azure für Windows](media/businessobjects-deployment-guide/businessobjects-deployment-windows.png)

Für dieses Beispiel werden die folgenden Produktversionen und Dateisystemlayouts verwendet:

- SAP BusinessObjects-Plattform 4.3 SP01 Patch 1
- Windows Server 2019
- SQL-Datenbank (Version 12.0.2000.8)
- Microsoft ODBC-Treiber – msodbcsql.msi (Version: 13.1)

| Dateisystem        | BESCHREIBUNG                                                                                                               | Größe (GB)             | Erforderlicher Zugriff  | Storage                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|---------------|----------------------------|
| F:          | Das Dateisystem für die Installation einer SAP BOBI-Instanz, der Tomcat-Standard-Webanwendung und der Datenbanktreiber (falls erforderlich). | SAP-Richtlinien zur Dimensionierung | Lokale Administratorrechte | Azure SSD Premium Managed Disks|
| \\\azusbobi.file.core.windows.net\frsinput  | Das Einbindungsverzeichnis ist für die freigegebenen Dateien aller SAP BOBI-Hosts vorgesehen und wird als Eingangsverzeichnis für die Dateispeicherung (Filestore) verwendet. | Geschäftliche Anforderung         | Lokale Administratorrechte | Azure NetApp Files         |
| \\\azusbobi.file.core.windows.net\frsoutput | Das Einbindungsverzeichnis ist für die freigegebenen Dateien aller SAP BOBI-Hosts vorgesehen und wird als Ausgangsverzeichnis für die Dateispeicherung (Filestore) verwendet. | Geschäftliche Anforderung         | Lokale Administratorrechte | Azure NetApp Files         |

## <a name="deploy-a-windows-virtual-machine-via-the-azure-portal"></a>Bereitstellen eines virtuellen Windows-Computers über das Azure-Portal

In diesem Abschnitt erstellen wir zwei VMs mit einem Windows-Betriebssystemimage für die SAP BOBI-Plattform. Allgemeine Schritte für die Erstellung von VMs:

1. Erstellen Sie eine [Ressourcengruppe](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups).

1. Erstellen Sie ein [virtuelles Netzwerk](../../../virtual-network/quick-create-portal.md#create-a-virtual-network):

   - Verwenden Sie bei der Bereitstellung der SAP BI-Plattform nicht nur ein gemeinsames Subnetz für alle Azure-Dienste. Basierend auf der Architektur der SAP BI-Plattform müssen Sie ggf. mehrere Subnetze erstellen. Bei dieser Bereitstellung erstellen wir zwei Subnetze: ein Subnetz für die BI-Anwendung und ein Application Gateway-Subnetz.
   - Befolgen Sie SAP-Hinweis [2276646](https://launchpad.support.sap.com/#/notes/2276646), um Ports für die Kommunikation der SAP BOBI-Plattform über verschiedene Komponenten hinweg zu identifizieren.
   - SQL-Datenbank kommuniziert über Port 1433. Der ausgehende Datenverkehr über Port 1433 von Ihren SAP BOBI-Anwendungsservern sollte zugelassen sein.
   - In Azure muss sich die Application Gateway-Instanz in einem separaten Subnetz befinden. Weitere Informationen finden Sie unter [Application Gateway-Konfiguration: Übersicht](../../../application-gateway/configuration-overview.md).
   - Erstellen Sie ein separates Subnetz für Azure NetApp Files, falls Sie Azure NetApp Files anstelle von Azure Files für einen Dateispeicher verwenden. Weitere Informationen finden Sie unter [Richtlinien für die Azure NetApp Files-Netzwerkplanung](../../../azure-netapp-files/azure-netapp-files-network-topologies.md).

1. Erstellen Sie eine Verfügbarkeitsgruppe:

   - Stellen Sie zur Erzielung von Redundanz für jede Ebene bei einer Bereitstellung mit mehreren Instanzen die VMs für jede Ebene in einer Verfügbarkeitsgruppe zusammen. Stellen Sie sicher, dass Sie die Verfügbarkeitsgruppen für jede Ebene auf der Grundlage Ihrer Architektur trennen.

1. Erstellen Sie den ersten virtuellen Computer (azuswinboap1):

   - Sie können entweder ein benutzerdefiniertes Image verwenden oder ein Image aus Azure Marketplace auswählen. Sehen Sie sich je nach Bedarf die Informationen zum Thema [Bereitstellen einer VM aus Azure Marketplace für SAP](deployment-guide.md) oder [Bereitstellen einer VM mit einem benutzerdefinierten Image für SAP](deployment-guide.md) an.

1. Erstellen Sie den zweiten virtuellen Computer (azuswinboap2).
1. Fügen Sie einen SSD Premium-Datenträger hinzu. Er wird als SAP BOBI-Installationsverzeichnis verwendet.

## <a name="provision-azure-premium-files"></a>Bereitstellen von Azure Files Premium

Bevor Sie mit der Einrichtung von Azure Files beginnen, sollten Sie sich mit der [Azure Files-Dokumentation](../../../storage/files/storage-files-introduction.md) vertraut machen.

Azure Files verfügt über Standard-Dateifreigaben, die auf festplattenbasierter Hardware (HDD) gehostet werden, und über Premium-Dateifreigaben, die auf SSD-basierter Hardware gehostet werden. Verwenden Sie für einen SAP BusinessObjects-Dateispeicher Azure Files Premium.

Azure Premium-Dateifreigaben sind in einigen Regionen mit lokaler Redundanz und Zonenredundanz verfügbar. Um herauszufinden, ob Premium-Dateifreigaben derzeit in Ihrer Region verfügbar sind, hilft Ihnen die Seite [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=storage) weiter. Informationen zu Regionen, die zonenredundanten Speicher (ZRS) unterstützen, finden Sie unter [Azure Storage-Redundanz](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="deploy-an-azure-files-storage-account-and-nfs-shares"></a>Bereitstellen eines Azure Files-Speicherkontos und von NFS-Freigaben

Azure-Dateifreigaben werden in Speicherkonten bereitgestellt, bei denen es sich um Objekte der obersten Ebene handelt, die einen freigegebenen Speicherpool darstellen. Dieser Speicherpool kann verwendet werden, um mehrere Dateifreigaben bereitzustellen. Azure unterstützt mehrere Arten von Speicherkonten für die möglichen unterschiedlichen Speicherszenarien der Kunden. Für SAP BusinessObjects-Dateispeicher müssen Sie ein FileStorage-Konto erstellen. Sie verwenden es, um Azure-Dateifreigaben auf SSD Premium-basierter Hardware bereitzustellen.

> [!NOTE]
> FileStorage-Konten können nur zum Speichern von Azure-Dateifreigaben verwendet werden. Unter einem FileStorage-Konto können keine anderen Speicherressourcen, z. B. Blobs, Container, Warteschlangen oder Tabellen, bereitgestellt werden.

Der Zugriff auf das Speicherkonto erfolgt über einen [privaten Endpunkt](../../../storage/files/storage-files-networking-endpoints.md), und es wird in demselben virtuellen Netzwerk wie eine SAP BOBI-Plattform bereitgestellt. Bei diesem Setup verlässt der Datenverkehr Ihres SAP-Systems niemals die Sicherheitsgrenzen des virtuellen Netzwerks. Da SAP-Systeme häufig vertrauliche und unternehmenskritische Daten enthalten, ist der Verbleib innerhalb der Grenzen des virtuellen Netzwerks für viele Kunden ein wichtiger Sicherheitsaspekt.

Wenn Sie über ein anderes virtuelles Netzwerk auf das Speicherkonto zugreifen müssen, können Sie das [Peering in virtuellen Azure-Netzwerken](../../../virtual-network/virtual-network-peering-overview.md) verwenden.

#### <a name="azure-files-storage-account"></a>Azure Files-Speicherkonto

1. Wählen Sie zum Erstellen eines Speicherkontos über das Azure-Portal die Option **Ressource erstellen** > **Speicher** > **Speicherkonto** aus.

1. Füllen Sie auf der Registerkarte **Grundlagen** alle erforderlichen Felder aus, um ein Speicherkonto zu erstellen:

   1. Wählen Sie **Abonnement** > **Ressourcengruppe** > **Region** aus.

   1. Geben Sie den **Namen des Speicherkontos** ein. Geben Sie beispielsweise **azusbobi** ein. Dieser Name muss global eindeutig sein, aber ansonsten haben Sie freie Wahl.

   1. Wählen Sie **Premium** als Leistungsstufe und **FileStorage** als Kontoart aus.

   1. Wählen Sie für die Bezeichnung **Replikation** eine Redundanzstufe aus. Wählen Sie **Lokal redundanter Speicher (LRS)** aus.

      Für FileStorage vom Typ „Premium“ sind „LRS“ und „ZRS“ die einzigen verfügbaren Optionen. Wählen Sie basierend auf Ihrer Bereitstellungsstrategie (Verfügbarkeitsgruppe oder Verfügbarkeitszone) die entsprechende Redundanzstufe aus. Weitere Informationen finden Sie unter [Azure Storage-Redundanz](../../../storage/common/storage-redundancy.md).

   1. Wählen Sie **Weiter** aus.

1. Wählen Sie auf der Registerkarte **Netzwerk** die Option [Privater Endpunkt](../../../storage/files/storage-files-networking-endpoints.md) als Konnektivitätsmethode aus. Weitere Informationen finden Sie unter [Azure Files – Überlegungen zum Netzwerkbetrieb](../../../storage/files/storage-files-networking-overview.md).

   1. Wählen Sie im Abschnitt privater Endpunkt die Option **Hinzufügen** aus.

   1. Wählen Sie **Abonnement** > **Ressourcengruppe** > **Standort** aus.

   1. Geben Sie im Feld **Name** den Namen des privaten Endpunkts ein. Geben Sie beispielsweise **azusbobi-pe** ein.

   1. Wählen Sie die **Datei** in der **Speicherunterressource** aus.

   1. Wählen Sie im Abschnitt **Netzwerk** das **virtuelle Netzwerk** und das **Subnetz** aus, in dem die SAP BusinessObjects BI-Anwendung bereitgestellt wird.

   1. Übernehmen Sie die **Standardeinstellung (Ja)** für **In private DNS-Zone integrieren**.

   1. Wählen Sie in der Dropdownliste Ihre **private DNS-Zone** aus.

   1. Wählen Sie **OK** aus, um zurück zur Registerkarte **Netzwerk** unter **Speicherkonto erstellen** zu wechseln.

1. Im Abschnitt „**Datenschutz**“ können Sie die Richtlinie für vorläufiges Löschen für Azure-Dateifreigaben in Ihrem Speicherkonto konfigurieren. Standardmäßig ist die Funktion für vorläufiges Löschen deaktiviert. Weitere Informationen zum vorläufigen Löschen finden Sie unter [Verhindern des versehentlichen Löschens von Azure-Dateifreigaben](../../../storage/files/storage-files-prevent-file-share-deletion.md).

1. Wählen Sie auf der Registerkarte **Erweitert** verschiedene Sicherheitsoptionen aus.

   Im Feld **Sichere Übertragung erforderlich** wird angegeben, ob für die eingehende Kommunikation mit dem Speicherkonto eine Verschlüsselung für die Übertragung erforderlich ist. Wenn Sie Unterstützung für SMB 2.1 benötigen, müssen Sie diese Option deaktivieren. Behalten Sie für die SAP BOBI-Plattform die Einstellung **Standard (Aktiviert)** bei.

1. Fahren Sie fort und erstellen Sie das Speicherkonto.

Weitere Informationen zum Erstellen eines Speicherkontos finden Sie unter [Erstellen eines FileStorage-Speicherkontos](../../../storage/files/storage-how-to-create-file-share.md).

#### <a name="create-azure-file-shares"></a>Erstellen von Azure-Dateifreigaben

Im nächsten Schritt erstellen Sie Azure-Dateien im Speicherkonto. Azure Files verwendet für Premium-Dateifreigaben das Modell „Bereitgestellt“. Bei Geschäftsmodell „Bereitgestellt“ geben Sie für Azure-Dateien proaktiv an, wie hoch Ihr Speicherbedarf ist (anstatt die nutzungsbasierte Abrechnung zu verwenden). Weitere Informationen zu diesem Modell finden Sie unter [Bereitgestelltes Modell](../../../storage/files/understanding-billing.md#provisioned-model). In diesem Beispiel erstellen wir zwei Azure-Dateien: frsinput (256 GB) und frsoutput (256 GB) für den SAP BOBI-Dateispeicher.

1. Navigieren Sie zum Speicherkonto **azusbobi** > **Dateifreigaben**.
1. Wählen Sie **Neue Dateifreigabe** aus.
1. Geben Sie im Feld **Name** den Namen der Dateifreigabe ein. Geben Sie beispielsweise **frsinput** oder **frsoutput** ein.
1. Fügen Sie die erforderliche Dateifreigabegröße unter **Bereitgestellte Kapazität** ein. Geben Sie beispielsweise **256 GB** ein.
1. Wählen Sie **SMB** als **Protokoll** aus.
1. Klicken Sie auf **Erstellen**.

## <a name="configure-a-data-disk-on-a-windows-virtual-machine"></a>Konfigurieren eines Datenträgers auf einem virtuellen Windows-Computer

Für die Schritte in diesem Abschnitt wird das folgende Präfix verwendet:

**[A]** : Der Schritt gilt für alle Hosts.

### <a name="initialize-a-new-data-disk"></a>Initialisieren eines neuen Datenträgers

Für die SAP BusinessObjects BI-Anwendung ist eine Partition erforderlich, auf der die zugehörigen Binärdateien installiert werden können. Sie können eine SAP BOBI-Anwendung auf der Betriebssystempartition (C:) installieren, aber Sie müssen sicherstellen, dass genügend Speicherplatz für die Bereitstellung und das Betriebssystem vorhanden ist. Wir empfehlen Ihnen, mindestens 2 GB für temporäre Dateien und Webanwendungen verfügbar zu halten. Darüber hinaus ist es ratsam, die Binärdateien der SAP BOBI-Installation auf separate Partitionen aufzuteilen.

In diesem Beispiel wird eine SAP BOBI-Anwendung auf einer separaten Partition (F:) installiert. Initialisieren Sie den SSD Premium-Datenträger, den Sie während der VM-Bereitstellung angefügt haben:

1. **[A]** Wenn kein Datenträger an die VM („azuswinboap1“ und „azuswinboap2“) angefügt ist, befolgen Sie die Schritte unter [Hinzufügen eines Datenträgers](../../windows/attach-managed-disk-portal.md#add-a-data-disk), um einen neuen verwalteten Datenträger anzufügen.
1. **[A]** Nachdem der verwaltete Datenträger an die VM angefügt wurde, initialisieren Sie den Datenträger mit den Schritten unter [Initialisieren eines neuen Datenträgers](../../windows/attach-managed-disk-portal.md#initialize-a-new-data-disk).

### <a name="mount-azure-premium-files"></a>Einbinden von Azure Files Premium

Um Azure Files als Dateispeicher zu verwenden, müssen Sie den Dienst einbinden. Dies bedeutet, dass Sie dafür einen Laufwerkbuchstaben oder einen Bereitstellungspunktpfad zuweisen.

**[A]** Führen Sie zum Einbinden der Azure-Dateifreigabe die Schritte unter [Einbinden der Azure-Dateifreigabe](../../../storage/files/storage-how-to-use-files-windows.md#mount-the-azure-file-share) aus.

Stellen Sie sicher, dass Port 445 geöffnet ist, um eine Azure-Dateifreigabe auf einem Windows-Server einzubinden. Für das SMB-Protokoll muss TCP-Port 445 geöffnet sein. Die Verbindungsherstellungen sind nicht erfolgreich, wenn Port 445 blockiert ist. Sie können überprüfen, ob Port 445 von der Firewall blockiert wird, indem Sie das Cmdlet `Test-NetConnection` verwenden, das im [Leitfaden zur Problembehandlung](../../../storage/files/storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) erwähnt wird.

## <a name="configure-a-cms-database-azure-sql"></a>Konfigurieren einer CMS-Datenbank: Azure SQL

Dieser Abschnitt enthält ausführliche Informationen zur Bereitstellung von Azure SQL über das Azure-Portal. Er enthält auch Anweisungen zum Erstellen der CMS- und der Überwachungsdatenbank für eine SAP BOBI-Plattform und ein Benutzerkonto für den Zugriff auf die Datenbanken.

Die Anleitung ist nur gültig, wenn Sie SQL-Datenbank verwenden. Eine Anleitung für andere Datenbanken finden Sie in der SAP- oder datenbankspezifischen Dokumentation.

### <a name="create-a-sql-database-server"></a>Erstellen eines SQL-Datenbank-Servers

SQL-Datenbank verfügt über verschiedene Bereitstellungsoptionen: Einzeldatenbank, Pool für elastische Datenbanken und Datenbankserver. Für eine SAP BOBI-Plattform benötigen wir zwei Datenbanken: für das CMS und für die Überwachung. Anstatt zwei Einzeldatenbanken zu erstellen, können Sie auch einen SQL-Datenbank-Server erstellen, über den die Gruppe mit den Einzeldatenbanken und Pools für elastische Datenbanken verwaltet werden kann. Führen Sie die folgenden Schritte aus, um einen SQL-Datenbank-Server zu erstellen:

1. Navigieren Sie zur Seite [SQL-Bereitstellungsoption auswählen](https://portal.azure.com/#create/Microsoft.AzureSQL).
1. Ändern Sie unter **SQL-Datenbanken** den **Ressourcentyp** in **Datenbankserver**. Klicken Sie auf **Erstellen**.
1. Füllen Sie auf der Registerkarte **Grundlagen** alle erforderlichen Felder für **Neuen SQL-Datenbank-Server erstellen** aus:

   1. Wählen Sie unter **Projektdetails** das **Abonnement** und die **Ressourcengruppe** aus.

   1. Geben Sie im Feld **Servername** einen Namen ein. Geben Sie beispielsweise **azussqlbodb** ein. Der Servername muss global eindeutig sein, aber ansonsten haben Sie freie Wahl.

   1. Wählen Sie den **Speicherort** aus.

   1. Geben Sie die **Serveradministratoranmeldung** ein. Geben Sie beispielsweise **boadmin** ein. Geben Sie anschließend ein **Kennwort** ein.

1. Ändern Sie auf der Registerkarte **Netzwerk** unter **Firewallregeln** den **Zugriff von Azure-Diensten und -Ressourcen auf diesen Server erlauben** in **Nein**.
1. Behalten Sie unter **Zusätzliche Einstellungen** die Standardeinstellungen bei.
1. Fahren Sie fort und erstellen Sie den **SQL-Datenbank-Server**.

Erstellen Sie im nächsten Schritt auf dem SQL-Datenbank-Server (azussqlbodb.database.windows.net) die CMS- und die Überwachungsdatenbank.

### <a name="create-the-cms-and-the-audit-database"></a>Erstellen der CMS- und der Überwachungsdatenbank

Navigieren Sie nach der Bereitstellung des SQL-Datenbank-Servers zur Ressource „azussqlbodb“. Führen Sie dann die folgenden Schritte aus, um die CMS- und die Überwachungsdatenbank zu erstellen.

1. Wählen Sie auf der Seite **Übersicht** für „azussqlbodb“ die Option **Datenbank erstellen** aus.
1. Füllen Sie auf der Registerkarte **Grundlagen** alle Pflichtfelder aus:

   1. Geben Sie im Feld **Datenbankname** den Namen ein. Geben Sie beispielsweise **bocms** oder **boaudit** ein.

   1. Wählen Sie unter **Compute + Speicher** die Option **Datenbank konfigurieren** aus. Wählen Sie basierend auf Ihrem Dimensionierungsergebnis das passende Modell aus. Informationen zu den Optionen finden Sie unter [Dimensionierungsmodelle für Azure SQL-Datenbank](businessobjects-deployment-guide.md#sizing-models-for-azure-sql-database).
1. Wählen Sie auf der Registerkarte **Netzwerk** die Option [Privater Endpunkt](../../../private-link/tutorial-private-endpoint-sql-portal.md) als Konnektivitätsmethode aus. Der private Endpunkt wird verwendet, um auf SQL-Datenbank innerhalb des konfigurierten virtuellen Netzwerks zuzugreifen.

   1. Wählen Sie **Privaten Endpunkt hinzufügen**.

   1. Wählen Sie **Abonnement** > **Ressourcengruppe** > **Standort** aus.

   1. Geben Sie im Feld **Name** den Namen des privaten Endpunkts ein. Geben Sie beispielsweise **azusbodb-pe** ein.

   1. Wählen Sie unter **Zielunterressource** die Option **SqlServer** aus.

   1. Wählen Sie im Abschnitt **Netzwerk** das **virtuelle Netzwerk** und das **Subnetz** aus, in dem die SAP BusinessObjects BI-Anwendung bereitgestellt wird.

   1. Übernehmen Sie die **Standardeinstellung (Ja)** für **In private DNS-Zone integrieren**.

   1. Wählen Sie in der Dropdownliste Ihre **private DNS-Zone** aus.

   1. Wählen Sie **OK** aus, um zurück zur Registerkarte **Netzwerk** unter **SQL-Datenbank erstellen** zu wechseln.
1. Ändern Sie auf der Registerkarte **Zusätzliche Einstellungen** die Einstellung **Sortierung** in **SQL_Latin1_General_CP850_BIN2**.
1. Fahren Sie fort, und erstellen Sie die CMS-Datenbank.

Auf ähnliche Weise können Sie auch die Überwachungsdatenbank erstellen. Geben Sie beispielsweise **boaudit** ein.

### <a name="download-and-install-an-odbc-driver"></a>Herunterladen und Installieren eines ODBC-Treibers

Für SAP BOBI-Anwendungsserver müssen der Datenbankclient bzw. die Datenbanktreiber auf die CMS- oder die Überwachungsdatenbank zugreifen können. Ein Microsoft ODBC-Treiber wird verwendet, um auf CMS- und Überwachungsdatenbanken zuzugreifen, die unter SQL-Datenbank ausgeführt werden. Dieser Abschnitt enthält Anweisungen zum Herunterladen und Einrichten eines ODBC-Treibers unter Windows. 

1. Informationen zu den Datenbankconnectors, die mit SQL-Datenbank kompatibel sind, finden Sie im Abschnitt zum Thema **CMS- und Überwachungsrepository-Unterstützung nach Betriebssystem** in der [Produktverfügbarkeitsmatrix (PAM) für die SAP BusinessObjects BI-Plattform](https://support.sap.com/pam).
1. Laden Sie die ODBC-Treiberversion über [diesen Link](/sql/connect/odbc/windows/release-notes-odbc-sql-server-windows?preserve-view=true&view=sql-server-ver15) herunter. In diesem Beispiel laden wir den ODBC-Treiber [13.1](/sql/connect/odbc/windows/release-notes-odbc-sql-server-windows?preserve-view=true&view=sql-server-ver15#131) herunter.
1. Installieren Sie den ODBC-Treiber auf allen BI-Servern (azuswinboap1 und azuswinboap2).
1. Navigieren Sie nach dem Installieren des Treibers auf **azuswinboap1** zu **Start** > **Windows-Verwaltungsprogramme** > **ODBC-Datenquellen (64 Bit)** .
1. Wechseln Sie zur Registerkarte **System-DSN**. 
1. Wählen Sie **Hinzufügen** aus, um eine Verbindung mit der CMS-Datenbank herzustellen.
1. Wählen Sie **ODBC Driver 13 for SQL Server** und dann **Fertig stellen** aus.
1. Geben Sie die Informationen Ihrer CMS-Datenbank wie unten angegeben ein, und wählen Sie **Weiter** aus:
   - **Name**: Der Name der Datenbank, die im Abschnitt „Erstellen der CMS- und der Überwachungsdatenbank“ erstellt wurde. Geben Sie beispielsweise **bocms** oder **boaudit** ein.
   - **Beschreibung**: Enthält eine Beschreibung der Datenquelle. Geben Sie beispielsweise **CMS-Datenbank** oder **Überwachungsdatenbank** ein.
   - **Server**: Der Name des Servers, der im Abschnitt „Erstellen eines SQL-Datenbank-Servers“ erstellt wurde. Geben Sie beispielsweise „azussqlbodb.database.windows.net“ ein.
1. Wählen Sie **Mit SQL Server-Authentifizierung anhand der vom Benutzer eingegebenen Anmelde-ID und des Kennworts** aus, um die Authentizität für Azure SQL Server zu überprüfen. Geben Sie die Benutzeranmeldeinformationen ein, die während der Erstellung des SQL-Datenbank-Servers erstellt wurden. Geben Sie beispielsweise **boadmin** ein. Wählen Sie **Weiter** aus.
1. Ändern Sie die **Standarddatenbank** in **bocms**, und behalten Sie für alle anderen Optionen die Standardeinstellung bei. Wählen Sie **Weiter** aus.
1. Aktivieren Sie das Kontrollkästchen **Starke Verschlüsselung für Daten verwenden**, und behalten Sie für alle anderen Optionen die Standardeinstellung bei. Wählen Sie **Fertig stellen** aus.
1. Die Datenquelle für die CMS-Datenbank wurde erstellt. Sie können nun **Datenquelle testen** auswählen, um die Verbindung der BI-Anwendung mit der CMS-Datenbank zu testen. Dieser Vorgang sollte erfolgreich sein. Wenn ein Fehler auftritt, [beheben Sie](../../../azure-sql/database/troubleshoot-common-errors-issues.md) das Konnektivitätsproblem.

>[!Note]
>SQL-Datenbank kommuniziert über Port 1433. Der ausgehende Datenverkehr über Port 1433 von Ihren SAP BOBI-Anwendungsservern sollte zugelassen sein.

Wiederholen Sie die obigen Schritte, um eine Verbindung für die Überwachungsdatenbank auf dem Server „azuswinboap1“ zu erstellen. Installieren und konfigurieren Sie auf ähnliche Weise beide ODBC-Datenquellen („bocms“ und „boaudit“) auf allen BI-Anwendungsservern (azuswinboap2). 

## <a name="server-preparation"></a>Servervorbereitung

Befolgen Sie die neuesten Anleitungen von SAP, um Server für die Installation der BI-Plattform vorzubereiten. Aktuelle Informationen finden Sie im Abschnitt „Vorbereitung“ des [Installationshandbuchs der SAP Business Intelligence-Plattform für Windows](https://help.sap.com/viewer/df8899896b364f6c880112f52e4d06c8/4.3.1/en-US/46b0d1a26e041014910aba7db0e91070.html).

## <a name="installation"></a>Installation

Melden Sie sich mit einem Benutzer an, der über lokale Administratorrechte verfügt, um die BI-Plattform auf einem Windows-Host zu installieren.

Navigieren Sie zu den Medien der SAP BusinessObjects BI-Plattform, und führen Sie `setup.exe` aus.

Befolgen Sie die für Ihre Version bestimmte Anleitung im [Installationshandbuch der SAP Business Intelligence-Plattform für Windows](https://help.sap.com/viewer/df8899896b364f6c880112f52e4d06c8/4.3.1/en-US/46ae62456e041014910aba7db0e91070.html). Beachten Sie bei der Installation der SAP BOBI-Plattform unter Windows die folgenden Punkte:

- Geben Sie auf dem Bildschirm **Zielordner konfigurieren** den Zielordner an, in dem Sie die BI-Plattform installieren möchten. Geben Sie beispielsweise **F:\SAP BusinessObjects\*** ein. 
- Auf dem Bildschirm **Produktregistrierung konfigurieren** können Sie entweder einen temporären Lizenzschlüssel für SAP BusinessObjects Solutions aus dem SAP-Hinweis [1288121](https://launchpad.support.sap.com/#/notes/1288121) verwenden oder einen Lizenzschlüssel über den SAP Service Marketplace generieren.
- Wählen Sie auf dem Bildschirm **Select Install Type** (Installationstyp auswählen) die **vollständige** Installation auf dem ersten Server (azuswinboap1) aus. Wählen Sie für den anderen Server (azuswinboap2) die Option **Anpassen/Erweitern** aus, mit der das vorhandene SAP BOBI-Setup erweitert wird.
- Wählen Sie auf dem Bildschirm zum **Auswählen der Standard- oder einer vorhandenen Datenbank** die Option zum **Konfigurieren einer vorhandenen Datenbank** aus. Sie werden dann aufgefordert, die CMS- und die Überwachungsdatenbank auszuwählen. Wählen Sie **Microsoft SQL Server für ODBC** für den Typ **CMS-Datenbank** und den Typ **Überwachungsdatenbank** aus.

  Sie können auch die Option für **Keine Überwachungsdatenbank** auswählen, wenn Sie die Überwachung während der Installation nicht konfigurieren möchten.

- Wählen Sie geeignete Optionen auf dem Bildschirm zum **Auswählen des Java-Webanwendungsservers** basierend auf Ihrer SAP BOBI-Architektur aus. In diesem Beispiel haben wir Option 1 ausgewählt, bei der ein Tomcat-Server auf derselben SAP BOBI-Plattform installiert wird.
- Geben Sie CMS-Datenbankinformationen in **CMS-Repository-Datenbank konfigurieren: SQL-Server (ODBC)** ein. In der folgenden Abbildung ist eine Beispieleingabe für CMS-Datenbankinformationen für eine Windows-Installation dargestellt.
  
  ![Screenshot: CMS-Datenbankinformationen für Windows](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-cms.png)
- (Optional) Geben Sie Informationen zur Überwachungsdatenbank unter **Configure Auditing Database – SQL Server (ODBC)** (Überwachungsdatenbank konfigurieren – SQL Server (ODBC)) ein. In der folgenden Abbildung ist eine Beispieleingabe für Informationen zur Überwachungsdatenbank für eine Windows-Installation dargestellt.
  
  ![Screenshot: Informationen zur Überwachungsdatenbank für Windows](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-audit.png)

- Befolgen Sie die Anweisungen, und geben Sie die erforderlichen Daten ein, um die Installation abzuschließen.

Bei einer Bereitstellung mit mehreren Instanzen führen Sie das Installationssetup auf dem zweiten Host (azuswinboap2) aus. Wählen Sie auf dem Bildschirm zum **Auswählen des Installationstyps** die Option zum **Anpassen/Erweitern** aus, um das vorhandene SAP BOBI-Setup zu erweitern. Weitere Informationen finden Sie im SAP-Blog zum Thema [Setup der SAP BusinessObjects Business Intelligence-Plattform mit Azure SQL-Datenbank](https://blogs.sap.com/2020/06/19/sap-on-azure-sap-businessobjects-business-intelligence-platform-setup-with-azure-sql-db-managed-paas-database/).

> [!IMPORTANT]
> Die Versionsnummern der Datenbank-Engine für SQL Server und SQL-Datenbank sind nicht miteinander vergleichbar. Es handelt sich hierbei um interne Buildnummern für diese separaten Produkte. Die Datenbank-Engine für SQL-Datenbank basiert auf der gleichen Codebasis wie die SQL Server-Datenbank-Engine. Entscheidend ist dabei, dass die Datenbank-Engine in SQL-Datenbank immer über die neuesten SQL-Datenbank-Engine-Bits verfügt. Die Version 12 von SQL-Datenbank ist neuer als die Version 15 von SQL Server.

Um die aktuelle Version von SQL-Datenbank zu ermitteln, können Sie entweder in den Einstellungen der zentralen Verwaltungskonsole (Central Management Console, CMC) nachsehen oder die folgende Abfrage mit [sqlcmd](/sql/tools/sqlcmd-utility?preserve-view=true&view=sql-server-ver15) oder [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms?preserve-view=true&view=sql-server-ver15) ausführen. Informationen zur Ausrichtung von SQL-Versionen an der Standardkompatibilität finden Sie im Artikel [Datenbank-Kompatibilitätsgrad](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level?preserve-view=true&view=sql-server-ver15).

![Screenshot: Datenbankinformationen in CMC](media/businessobjects-deployment-guide/businessobjects-deployment-windows-sql-cmc.PNG)

```sql
1> select @@version as version;
2> go
version
------------------------------------------------------------------------------------------
Microsoft SQL Azure (RTM) - 12.0.2000.8
        Feb 20 2021 17:51:58
        Copyright (C) 2019 Microsoft Corporation

(1 rows affected)

1> select name, compatibility_level from sys.databases;
2> go
name                                                                  compatibility_level
--------------------------------------------------------------------- -------------------
master                                                                                150
bocms                                                                                 150
boaudit                                                                               150

(3 rows affected)
```

## <a name="post-installation"></a>Nach der Installation

Nach einer Installation der SAP BOBI-Plattform mit mehreren Instanzen müssen zusätzliche Nachbearbeitungsschritte ausgeführt werden, um die Hochverfügbarkeit von Anwendungen zu unterstützen.

### <a name="configure-a-cluster-name"></a>Konfigurieren eines Clusternamens

Bei einer Bereitstellung der SAP BOBI-Plattform mit mehreren Instanzen sollen mehrere CMS-Server gemeinsam in einem Cluster ausgeführt werden. Ein Cluster besteht aus zwei oder mehr CMS-Servern, die mit einer gemeinsamen CMS-Systemdatenbank zusammenarbeiten. Wenn ein Knoten ausfällt, der unter dem CMS ausgeführt wird, werden die Anforderungen der BI-Plattform von einem Knoten mit einem anderen CMS weiter verarbeitet. Standardmäßig wird mit einem Clusternamen auf der SAP BOBI-Plattform der Hostname des ersten CMS angegeben, das Sie installieren. 

Befolgen Sie die Anleitung im [Administratorhandbuch für die SAP Business Intelligence-Plattform](https://help.sap.com/viewer/2e167338c1b24da9b2a94e68efd79c42/4.3.1/en-US), um den Clusternamen unter Windows zu konfigurieren. Befolgen Sie nach dem Konfigurieren des Clusternamens den SAP-Hinweis [1660440](https://launchpad.support.sap.com/#/notes/1660440), um den Standardsystemeintrag auf der Anmeldeseite der CMC oder des BI-Launchpads festzulegen. 

### <a name="configure-the-input-and-output-filestore-location-to-azure-premium-files"></a>Konfigurieren des Speicherorts des Eingangs- und Ausgangsdateispeichers in Azure Files Premium

Mit dem Dateispeicher (Filestore) sind hier die Datenträgerverzeichnisse gemeint, in denen sich die eigentlichen SAP BusinessObjects-Dateien befinden. Der Standardspeicherort des Dateirepositoryservers für die SAP BOBI-Plattform befindet sich im lokalen Installationsverzeichnis. Bei der Bereitstellung mit mehreren Instanzen ist es wichtig, den Filestore in einem freigegebenen Speicher wie Azure Premium Files oder Azure NetApp Files einzurichten, damit darauf von allen Servern der Speicherebene zugegriffen werden kann.

1. Wenn die Erstellung nicht durchgeführt wird, sollten Sie die Anleitung im obigen Abschnitt „Bereitstellen von Azure Files Premium“ befolgen, um Azure Files Premium zu erstellen und einzubinden.

   > [!Tip]
   > Wählen Sie die Speicherredundanz für Azure Files Premium (LRS oder ZRS) basierend auf der Bereitstellungsart Ihrer VM (Verfügbarkeitsgruppe oder Verfügbarkeitszone) aus.

1. Befolgen Sie den SAP-Hinweis [2512660](https://launchpad.support.sap.com/#/notes/0002512660), um den Pfad des Dateirepositorys (Eingabe und Ausgabe) zu ändern.

### <a name="tomcat-clustering-session-replication"></a>Tomcat-Clustering: Sitzungsreplikation

Tomcat unterstützt das Clustering von mindestens zwei Anwendungsservern für Sitzungsreplikation und Failover. SAP BOBI-Plattformsitzungen sind serialisiert. Für eine Benutzersitzung kann selbst dann reibungslos ein Failover auf eine andere Instanz von Tomcat ausgeführt werden, wenn ein Anwendungsserver ausfällt. Es kann beispielsweise sein, dass ein Benutzer mit einem Webserver verbunden ist, der ausfällt, während der Benutzer in einer Ordnerhierarchie in einer SAP BI-Anwendung navigiert. In einem richtig konfigurierten Cluster kann der Benutzer weiterhin in der Ordnerhierarchie navigieren, ohne zu einer Anmeldeseite umgeleitet zu werden.

Im SAP-Hinweis [2808640](https://launchpad.support.sap.com/#/notes/2808640) sind die Schritte zum Konfigurieren von Tomcat-Clusterings per Multicast angegeben. Multicast wird in Azure aber nicht unterstützt. Damit ein Tomcat-Cluster in Azure funktioniert, müssen Sie [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership) verwenden (SAP-Hinweis [2764907](https://launchpad.support.sap.com/#/notes/2764907)). Informationen zum Einrichten eines Tomcat-Clusters in Azure finden Sie unter [Tomcat-Clustering mit statischer Mitgliedschaft für die SAP BusinessObjects BI-Plattform](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/) im SAP-Blog.

### <a name="load-balance-a-web-tier-of-an-sap-bi-platform"></a>Lastenausgleich für eine Webebene einer SAP BI-Plattform

Bei einer Bereitstellung von SAP BOBI mit mehreren Instanzen werden Java-Webanwendungsserver (Webebene) auf zwei oder mehr Hosts ausgeführt. Um die Benutzerauslastung gleichmäßig auf die Webserver zu verteilen, können Sie einen Lastenausgleich zwischen Endbenutzern und Webservern verwenden. Sie können Azure Load Balancer oder Azure Application Gateway verwenden, um den Datenverkehr zu verwalten, der an Ihre Webanwendungsserver fließt. Die verfügbaren Angebote werden in den folgenden Abschnitten näher beschrieben:

* [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) ist ein Layer 4-Lastenausgleichsmodul (TCP, UDP) mit hoher Leistung und geringer Latenz, mit dem der Datenverkehr auf fehlerfreie VMs verteilt wird. Über den Integritätstest eines Lastenausgleichsmoduls wird ein bestimmter Port auf jeder VM überwacht und Datenverkehr nur auf betriebsbereite VMs verteilt. Sie können entweder ein öffentliches oder ein internes Lastenausgleichsmodul wählen. Dies richtet sich danach, ob die SAP BI-Plattform über das Internet zugänglich sein soll. Hierbei ist für Zonenredundanz gesorgt, damit die Hochverfügbarkeit über mehrere Verfügbarkeitszonen hinweg sichergestellt ist.

   Sehen Sie sich in der folgenden Abbildung den Abschnitt zum internen Lastenausgleichsmodul an, in dem der Webanwendungsserver über Port 8080 (HTTP-Standardport von Tomcat) ausgeführt wird, der von einem Integritätstest überwacht wird. Jede von Benutzern eingehende Anforderung wird an die Webanwendungsserver („azuswinboap1“ oder „azuswinboap2“) im Back-End-Pool umgeleitet. Für das Lastenausgleichsmodul wird der TLS/SSL-Abschluss (auch als TLS/SSL-Abladung bezeichnet) nicht unterstützt. Wenn Sie Load Balancer verwenden, um Datenverkehr auf Webserver zu verteilen, empfehlen wir die Nutzung von Load Balancer Standard.

   > [!NOTE]
   > Wenn virtuelle Computer ohne öffentliche IP-Adressen im Back-End-Pool einer internen Load Balancer Standard-Instanz (ohne öffentliche IP-Adresse) platziert werden, besteht keine Internetkonnektivität in ausgehender Richtung, sofern nicht durch zusätzliche Konfigurationsschritte das Routing an öffentliche Endpunkte zugelassen wird. Informationen zum Erzielen von Konnektivität in ausgehender Richtung finden Sie unter [Konnektivität mit öffentlichen Endpunkten für virtuelle Computer mithilfe von Azure Load Balancer Standard in SAP-Szenarien mit Hochverfügbarkeit](high-availability-guide-standard-load-balancer-outbound-connections.md).

   ![Screenshot: Load Balancer zum gleichmäßigen Verteilen von Datenverkehr auf Webserver](media/businessobjects-deployment-guide/businessobjects-deployment-windows-load-balancer.png)

* [Application Gateway](../../../application-gateway/overview.md) verfügt über einen Controller zur Anwendungsbereitstellung als Dienst, mit dessen Hilfe Anwendungen Benutzerdatenverkehr an einen oder mehrere Webanwendungsserver leiten können. Sie können für Ihre Anwendungen verschiedene Layer 7-Lastenausgleichsfunktionen nutzen, z. B. TLS/SSL-Abladung, Web Application Firewall (WAF) und cookiebasierte Sitzungsaffinität.

   Auf einer SAP BI-Plattform leitet Application Gateway den Webdatenverkehr von Anwendungen an die angegebenen Ressourcen in einem Back-End-Pool weiter. In diesem Fall ist dies entweder „azuswinboap1“ oder „azuswinboap2“. Sie weisen dem Port einen Listener zu, erstellen Regeln und fügen einem Back-End-Pool Ressourcen hinzu. In der folgenden Abbildung fungiert eine Application Gateway-Instanz mit einer privaten Front-End-IP-Adresse (10.31.3.25) als Einstiegspunkt für Benutzer, verarbeitet eingehende TLS/SSL-Verbindungen (HTTPS – TCP/443), führt die TLS/SSL-Entschlüsselung durch und übergibt die Anforderung (HTTP – TCP/8080) an die Server im Back-End-Pool. Dank der integrierten Funktion für den TLS/SSL-Abschluss müssen Sie nur ein TLS/SSL-Zertifikat auf dem Anwendungsgateway verwenden. Dies bedeutet für Sie eine Vereinfachung des Betriebs.

   ![Screenshot: Application Gateway zum gleichmäßigen Verteilen von Datenverkehr auf Webserver](media/businessobjects-deployment-guide/businessobjects-deployment-windows-application-gateway.png)

   Informationen zum Konfigurieren von Application Gateway für einen SAP BOBI-Webserver finden Sie unter [Lastenausgleich für SAP BOBI-Webserver mit Application Gateway](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/) im SAP-Blog.

   > [!NOTE]
   > Verwenden Sie Application Gateway für die Durchführung des Lastenausgleichs für Datenverkehr, der an den Webserver fließt. Sie profitieren hierbei von Features wie SSL-Abladung, zentrale SSL-Verwaltung zur Reduzierung des Ver- und Entschlüsselungsaufwands auf dem Server, Roundrobin-Algorithmen zur Verteilung des Datenverkehrs, Web Application Firewall-Funktionen und Hochverfügbarkeit.

## <a name="sap-businessobjects-bi-platform-reliability-on-azure"></a>Zuverlässigkeit der SAP BusinessObjects BI-Plattform in Azure

Die SAP BusinessObjects BI-Plattform umfasst unterschiedliche Ebenen, die für bestimmte Aufgaben und Vorgänge optimiert sind. Wenn eine Komponente einer Ebene nicht mehr verfügbar ist, kann auf die SAP BOBI-Anwendung entweder nicht mehr zugegriffen werden, oder bestimmte Funktionen der Anwendung funktionieren nicht mehr. Daher müssen Sie sicherstellen, dass jede Ebene auf Zuverlässigkeit ausgelegt ist, um die Anwendung betriebsbereit zu halten und eine Unterbrechung des Geschäftsbetriebs zu vermeiden.

In diesem Leitfaden wird beschrieben, wie native Funktionen von Azure kombiniert mit einer SAP BOBI-Plattformkonfiguration die Verfügbarkeit einer SAP-Bereitstellung verbessern. In diesem Abschnitt werden die folgenden Optionen für die Zuverlässigkeit der SAP BOBI-Plattform in Azure beschrieben:

- **Sicherung und Wiederherstellung**: Bei diesem Prozess werden regelmäßig Kopien von Daten und Anwendungen an separaten Standorten erstellt. Wenn die ursprünglichen Daten oder Anwendungen verloren gehen oder beschädigt sind, können die Kopien verwendet werden, um den vorherigen Zustand wiederherzustellen.
- **Hochverfügbarkeit**: Eine Plattform mit Hochverfügbarkeit verfügt innerhalb einer Azure-Region von jeder Komponente über mindestens zwei Kopien, damit die Anwendung betriebsbereit bleibt, wenn einer der Server nicht mehr verfügbar ist.
- **Notfallwiederherstellung (Disaster Recovery, DR)** : Bei diesem Prozess wird Ihre Anwendungsfunktionalität wiederhergestellt, wenn es zu schwerwiegenden Verlusten kommt. Beispielsweise kann aufgrund einer Naturkatastrophe eine gesamte Azure-Region nicht mehr verfügbar sein.

Die Implementierung dieser Lösung variiert je nach Art des Systems, das in Azure eingerichtet wurde. Sie müssen Ihre Lösungen für die Bereiche Sicherung und Wiederherstellung, Hochverfügbarkeit und Notfallwiederherstellung basierend auf Ihren Geschäftsanforderungen entsprechend anpassen.

## <a name="backup-and-restore"></a>Sichern und Wiederherstellen

Beim Prozess für die Sicherung und Wiederherstellung werden an einem anderen Standort regelmäßig Kopien von Daten und Anwendungen erstellt, damit dafür der letzte bzw. ein vorheriger Zustand wiederhergestellt werden kann, falls die ursprünglichen Daten oder Anwendungen verloren gehen oder beschädigt sind. Dies ist auch ein wesentlicher Bestandteil jeder Strategie zur Notfallwiederherstellung in Unternehmen. Diese Sicherungen ermöglichen die Wiederherstellung von Anwendungen und Datenbanken bis zu einem bestimmten Zeitpunkt innerhalb des konfigurierten Aufbewahrungszeitraums.

Identifizieren Sie zur Entwicklung einer umfassenden Sicherungs- und Wiederherstellungsstrategie für eine SAP BOBI-Plattform die Komponenten, die zu Systemausfällen oder Störungen der Anwendung führen. Auf einer SAP BOBI-Plattform ist die Sicherung der folgenden Komponenten für den Schutz der Anwendung unerlässlich:

- SAP BOBI-Installationsverzeichnis (verwaltete Premium-Datenträger)
- Filestore (Azure Files Premium oder Azure NetApp Files für verteilte Installation)
- CMS- und Überwachungsdatenbank (SQL-Datenbank, Azure Database for MySQL oder eine Datenbank auf einem virtuellen Azure-Computer)

Im folgenden Abschnitt wird beschrieben, wie die Sicherungs- und Wiederherstellungsstrategie für die einzelnen Komponenten auf einer SAP BOBI-Plattform implementiert wird.

### <a name="backup-and-restore-for-an-sap-bobi-installation-directory"></a>Sicherung und Wiederherstellung für ein SAP BOBI-Installationsverzeichnis

In Azure ist die einfachste Möglichkeit zur Sicherung von VMs und allen angefügten Datenträgern die Verwendung von [Azure Backup](../../../backup/backup-azure-vms-introduction.md). Hiermit können unabhängige und isolierte Sicherungen erstellt werden, damit ein Schutz vor dem versehentlichen Löschen der Daten auf Ihren VMs besteht. Sicherungen werden in einem Recovery Services-Tresor mit integrierter Verwaltung von Wiederherstellungspunkten gespeichert. Die Konfiguration und die Skalierung können leicht durchgeführt werden. Die Sicherungen werden optimiert und können bei Bedarf leicht wiederhergestellt werden.

Im Rahmen des Sicherungsprozesses wird eine Momentaufnahme erstellt. Die Daten werden in den Recovery Services-Tresor übertragen, ohne dass sich dies auf Produktionsworkloads auswirkt. Die Erstellung der Momentaufnahme ermöglicht einen anderen Konsistenzgrad. Dies ist unter [Konsistenz von Momentaufnahmen](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency) beschrieben. Darüber hinaus verfügt Backup auch über eine parallele Unterstützung für die Sicherung verwalteter Datenträger per [Azure Disk Backup](../../../backup/disk-backup-overview.md) und einer Lösung für die [Sicherung von Azure-VMs](../../../backup/backup-azure-vms-introduction.md). Dies ist hilfreich, wenn Sie tägliche konsistente Sicherungen von VMs und häufigere absturzkonsistente Sicherungen von Betriebssystemdatenträgern oder eines bestimmten Datenträgers durchführen möchten. Weitere Informationen finden Sie unter [Ein Überblick über die Sicherung von Azure-VMs](../../../backup/backup-azure-vms-introduction.md), [Übersicht über Azure Disk Backup](../../../backup/disk-backup-overview.md) und [Häufig gestellte Fragen: Sichern von Azure-VMs](../../../backup/backup-azure-vm-backup-faq.yml).

### <a name="backup-and-restore-for-filestore"></a>Sichern und Wiederherstellen für Filestore

Je nach Ihrer Bereitstellung kann sich der Filestore einer SAP BOBI-Plattform auf einer Azure NetApp Files- oder Azure Files-Instanz befinden. Wählen Sie basierend auf dem Speicher, den Sie für den Filestore verwenden, eine der folgenden Optionen für die Sicherung und Wiederherstellung aus:

* **Azure NetApp Files**: Für Azure NetApp Files können Sie bedarfsabhängige Momentaufnahmen erstellen und mithilfe von Momentaufnahmerichtlinien eine automatische Momentaufnahme planen. Momentaufnahmekopien stellen eine Zeitpunktkopie Ihres Azure NetApp Files-Volumes dar. Weitere Informationen finden Sie unter [Verwalten von Momentaufnahmen mithilfe von Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md).
* **Azure Files**: Die Azure Files-Sicherung ist in eine native Instanz von [Backup](../../../backup/backup-overview.md) integriert. Auf diese Weise wird die Sicherungs- und Wiederherstellungsfunktion mit der VM-Sicherung zusammengefasst und der Betrieb vereinfacht. Weitere Informationen finden Sie unter [Sichern von Azure-Dateifreigaben](../../../backup/azure-file-share-backup-overview.md) und [Häufig gestellte Fragen zu Azure Files](../../../backup/backup-azure-files-faq.yml).

Wenn Sie einen separaten NFS-Server erstellt haben, sollten Sie sicherstellen, dass Sie dafür die Sicherungs- und Wiederherstellungsstrategie implementieren.

### <a name="backup-and-restore-for-the-cms-and-audit-database"></a>Sicherung und Wiederherstellung für die CMS- und die Überwachungsdatenbank

Für eine SAP BOBI-Plattform, die auf Windows-VMs ausgeführt wird, können die CMS- und die Überwachungsdatenbank auf allen unterstützten Datenbanken ausgeführt werden. Dies ist in der [Unterstützungsmatrix](businessobjects-deployment-guide.md#support-matrix) beschrieben, die im Planungs- und Implementierungshandbuch für die SAP BOBI-Plattform in Azure enthalten ist. Daher ist es wichtig, dass Sie die Sicherungs- und Wiederherstellungsstrategie basierend auf der Datenbank nutzen, die für die Speicherung der CMS- und Überwachungsdaten verwendet wird.

* Für **SQL-Datenbank** wird SQL Server-Technologie genutzt, um wöchentlich [vollständige Sicherungen](/sql/relational-databases/backup-restore/full-database-backups-sql-server?preserve-view=true&view=sql-server-ver15), alle 12 bis 24 Stunden [differenzielle Sicherungen](/sql/relational-databases/backup-restore/differential-backups-sql-server?preserve-view=true&view=sql-server-ver15) und alle 5 bis 10 Minuten [Transaktionsprotokollsicherungen](/sql/relational-databases/backup-restore/transaction-log-backups-sql-server?preserve-view=true&view=sql-server-ver15) zu erstellen. Die Häufigkeit von Transaktionsprotokollsicherungen basiert auf der Computegröße und dem Umfang der Datenbankaktivität.
 
   Benutzer können eine Option zum Konfigurieren der Sicherungsspeicherredundanz zwischen LRS-, ZRS- und GRS-Blobs auswählen. Bei den Mechanismen der Speicherredundanz werden mehrere Kopien Ihrer Daten gespeichert, damit diese vor geplanten und ungeplanten Ereignissen geschützt sind. Dies können vorübergehend auftretende Hardwarefehler, Netzwerk- oder Stromausfälle oder schwere Naturkatastrophen sein. Standardmäßig werden Sicherungsdaten von SQL-Datenbank in [GRS-Blobs](../../../storage/common/storage-redundancy.md) gespeichert, die in einer [gekoppelten Region](../../../best-practices-availability-paired-regions.md) repliziert werden. Dies kann je nach den geschäftlichen Anforderungen auch auf LRS- oder ZRS-Blobs umgestellt werden. Weitere aktuelle Informationen zur Planung, Aufbewahrung und Speichernutzung bei SQL-Datenbank-Sicherungen finden Sie unter [Automatisierte Sicherungen – Azure SQL-Datenbank und SQL Managed Instance](../../../azure-sql/database/automated-backups-overview.md).

* Von **Azure Database for MySQL** werden automatisch Serversicherungen erstellt und in Speicher vom Typ LRS oder GRS gespeichert, der von Benutzern konfiguriert wird. Von Azure Database for MySQL werden die Datendateien und das Transaktionsprotokoll gesichert. Abhängig von der unterstützten maximalen Speichergröße werden entweder vollständige oder differenzielle Sicherungen (Server mit maximal 4 TB Speicher) oder Momentaufnahmesicherungen (Server mit bis zu 16 TB Speicher) erstellt. Diese Sicherungen ermöglichen es Ihnen, für einen Server den Zustand zu jedem Zeitpunkt innerhalb des von Ihnen konfigurierten Aufbewahrungszeitraums für Sicherungen wiederherzustellen. Der standardmäßige Aufbewahrungszeitraum für Sicherungen beträgt sieben Tage. Sie können diesen Wert [optional auf bis zu 35 Tage erhöhen](../../../mysql/howto-restore-server-portal.md#set-backup-configuration). Für die Verschlüsselung aller Sicherungen wird die AES-256-Bit-Verschlüsselung verwendet. Diese Sicherungsdateien sind nicht für Benutzer verfügbar und können nicht exportiert werden. Sie können nur für Wiederherstellungsvorgänge in Azure Database for MySQL verwendet werden. Zum Kopieren einer Datenbank können Sie [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) verwenden. Weitere Informationen finden Sie unter [Sicherung und Wiederherstellung in Azure Database for MySQL](../../../mysql/concepts-backup.md).

* **Für eine auf einer Azure-VM installierte Datenbank** können Sie Standardtools für die Sicherung nutzen. Verwenden Sie [Backup](../../../backup/sap-hana-db-about.md) für unterstützte Datenbanken. Falls die Azure-Dienste und -Tools Ihre Anforderungen nicht erfüllen, können Sie auch unterstützte Sicherungstools von Drittanbietern verwenden, die über einen Agent für die Sicherung und Wiederherstellung aller Komponenten der SAP BOBI-Plattform verfügen.

## <a name="high-availability"></a>Hochverfügbarkeit

Zur Erzielung von Hochverfügbarkeit wird eine Reihe von Technologien genutzt, mit denen Störungen im IT-Bereich minimiert werden können. Hierbei wird die Geschäftskontinuität für Anwendungen oder Dienste sichergestellt, indem redundante, fehlertolerante oder durch Failover geschützte Komponenten innerhalb desselben Rechenzentrums bereitgestellt werden. In unserem Fall befinden sich die Rechenzentren innerhalb einer Azure-Region. Der Artikel [Architektur und Szenarien für die Hochverfügbarkeit von SAP NetWeaver](sap-high-availability-architecture-scenarios.md) enthält eine Beschreibung verschiedener Verfahren und Empfehlungen zur Erzielung von Hochverfügbarkeit für „Azure für SAP“-Anwendungen. Diese Informationen dienen als Erweiterung der Anleitung in diesem Abschnitt.

Basierend auf dem Dimensionierungsergebnis der SAP BOBI-Plattform müssen Sie die Umgebung entwerfen und die Verteilung der BI-Komponenten auf die Azure-VMs und Subnetze festlegen. Der Redundanzgrad der verteilten Architektur richtet sich nach den geschäftlichen Anforderungen in Bezug auf den RTO- (Recovery Time Objective) und RPO-Wert (Recovery Point Objective). Die SAP BOBI-Plattform verfügt über unterschiedliche Ebenen, und die Komponenten auf den einzelnen Ebenen sollten so konzipiert sein, dass Redundanz erzielt wird. Wenn dann eine Komponente ausfällt, kommt es für Ihre SAP BOBI-Anwendung nur zu einer kurzen oder zu gar keiner Unterbrechung. Beispiel:

- Redundante Anwendungsserver, z. B. BI-Anwendungsserver und -Webserver
- Eindeutige Komponenten, z. B. CMS-Datenbank, Filestore und Lastenausgleich

Im folgenden Abschnitt wird beschrieben, wie Sie für jede Komponente einer SAP BOBI-Plattform Hochverfügbarkeit erzielen können.

### <a name="high-availability-for-application-servers"></a>Hochverfügbarkeit für Anwendungsserver

Für BI- und Webanwendungsserver wird keine spezifische Hochverfügbarkeitslösung benötigt. Dies gilt unabhängig davon, ob sie zusammen oder getrennt installiert werden. Sie können Hochverfügbarkeit durch Redundanz erzielen, indem Sie mehrere Instanzen von BI- und Webservern auf verschiedenen Azure-VMs konfigurieren. Sie können die VMs entweder in [Verfügbarkeitsgruppen](sap-high-availability-architecture-scenarios.md#multiple-instances-of-virtual-machines-in-the-same-availability-set) oder [Verfügbarkeitszonen](sap-high-availability-architecture-scenarios.md#azure-availability-zones) bereitstellen. Dies hängt vom jeweils erforderlichen RTO-Wert ab. Stellen Sie für die übergreifende Bereitstellung in mehreren Verfügbarkeitszonen sicher, dass auch alle anderen Komponenten der SAP BOBI-Plattform zonenredundant sind.

Da derzeit nicht alle Azure-Regionen über Verfügbarkeitszonen verfügen, müssen Sie die Bereitstellungsstrategie nach Ihrer Region ausrichten. Die Azure-Regionen, in denen Zonen verfügbar sind, sind unter [Regionen und Verfügbarkeitszonen in Azure](../../../availability-zones/az-overview.md) aufgelistet.

> [!Important]
> Die Konzepte von Azure-Verfügbarkeitszonen und Azure-Verfügbarkeitsgruppen schließen sich gegenseitig aus. Dies bedeutet, dass Sie entweder zwei oder mehr VMs nur jeweils in einer bestimmten Verfügbarkeitszone oder Azure-Verfügbarkeitsgruppe bereitstellen können, aber nicht in beiden.

### <a name="high-availability-for-the-cms-database"></a>Hochverfügbarkeit für die CMS-Datenbank

Wenn Sie eine Azure-Datenbank als Lösung für Ihre CMS- und Überwachungsdatenbank verwenden, wird standardmäßig ein lokal redundantes Framework für Hochverfügbarkeit bereitgestellt. Sie können die Funktionen für Hochverfügbarkeit, Redundanz und Resilienz auswählen, die in der Region bzw. für den Dienst verfügbar sind, ohne dass Sie zusätzliche Komponenten konfigurieren müssen. Wenn die Bereitstellungsstrategie für eine SAP BOBI-Plattform per Verfügbarkeitszone erfolgt, sollten Sie sicherstellen, dass Sie Zonenredundanz für Ihre CMS- und Überwachungsdatenbank erzielen. Weitere Informationen zur Hochverfügbarkeit für unterstützte Datenbankangebote in Azure finden Sie unter [Hochverfügbarkeit für Azure SQL-Datenbank](../../../azure-sql/database/high-availability-sla.md) und [Hochverfügbarkeit in Azure Database for MySQL](../../../mysql/concepts-high-availability.md). 

Informationen zu anderen DBMS-Bereitstellungen (Database Management System) für eine CMS-Datenbank finden Sie unter [Azure Virtual Machines – DBMS-Bereitstellung für SAP-Workload](dbms_guide_general.md). Der Artikel enthält eine Beschreibung einer anderen DBMS-Bereitstellung und des dafür gewählten Ansatzes, mit dem Hochverfügbarkeit erzielt wird.

### <a name="high-availability-for-filestore"></a>Hochverfügbarkeit für Filestore

Filestore bezieht sich auf die Datenträgerverzeichnisse, in denen Inhalte wie Berichte, Universen und Verbindungen gespeichert werden. Es wird von allen Anwendungsservern dieses Systems gemeinsam genutzt. Daher müssen Sie sicherstellen, dass für Hochverfügbarkeit gesorgt ist (wie auch für die anderen Komponenten der SAP BOBI-Plattform).

Für eine unter Windows ausgeführte SAP BOBI-Plattform können Sie entweder [Azure Files Premium](../../../storage/files/storage-files-introduction.md) oder [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) für den Filestore auswählen, um die Hochverfügbarkeit und Langlebigkeit sicherzustellen. Für Azure Files Premium wird ZRS unterstützt. Dies kann für die zonenübergreifende Bereitstellung einer SAP BOBI-Plattform nützlich sein. Weitere Informationen finden Sie im Abschnitt [Redundanz](../../../storage/files/storage-files-planning.md#redundancy) für Azure Files.

Da der Dateifreigabedienst nicht in allen Regionen verfügbar ist, sollten Sie in der Liste mit den [verfügbaren Produkten nach Region](https://azure.microsoft.com/global-infrastructure/services/) nach aktuellen Informationen suchen. Wenn der Dienst in Ihrer Region nicht verfügbar ist, können Sie einen NFS-Server erstellen, über den Sie das Dateisystem für eine SAP BOBI-Anwendung freigeben. Sie müssen aber auch seine Hochverfügbarkeit berücksichtigen.

### <a name="high-availability-for-the-load-balancer"></a>Hochverfügbarkeit für den Lastenausgleich

Für die Verteilung von Datenverkehr für einen Webserver können Sie Load Balancer oder Application Gateway verwenden. Bei beiden Lastenausgleichsmodulen kann Redundanz basierend auf der SKU erzielt werden, die Sie für die Bereitstellung auswählen:

* **Load Balancer**: Hierbei kann Redundanz erzielt werden, indem das Front-End von Load Balancer Standard als zonenredundant konfiguriert wird. Weitere Informationen finden Sie unter [Load Balancer und Verfügbarkeitszonen](../../../load-balancer/load-balancer-standard-availability-zones.md).
* **Application Gateway**: Die Hochverfügbarkeit kann hier basierend auf der Art der Ebene erzielt werden, die während der Bereitstellung ausgewählt wurde:
   * Die v1-SKU unterstützt Szenarien mit Hochverfügbarkeit, wenn Sie mindestens zwei Instanzen bereitgestellt haben. Azure verteilt diese Instanzen auf Update- und Fehlerdomänen, um sicherzustellen, dass nicht alle Instanzen gleichzeitig ausfallen. Mit dieser SKU kann die Redundanz in der Zone erzielt werden.
   * Die v2-SKU stellt automatisch sicher, dass neue Instanzen über Fehlerdomänen und Updatedomänen hinweg verteilt werden. Haben Sie Zonenredundanz gewählt, werden die neuesten Instanzen darüber hinaus über Verfügbarkeitszonen hinweg verteilt, um Resilienz gegen Zonenausfälle zu erreichen. Weitere Informationen finden Sie unter [Automatische Skalierung und zonenredundantes Application Gateway v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md).

### <a name="reference-high-availability-architecture-for-the-sap-businessobjects-bi-platform"></a>Referenzarchitektur für Hochverfügbarkeit für die SAP BusinessObjects BI-Plattform

In der folgenden Referenzarchitektur wird die übergreifende Einrichtung einer SAP BOBI-Plattform für mehrere Verfügbarkeitszonen auf einem Windows-Server beschrieben. Die Architektur veranschaulicht die Verwendung unterschiedlicher Azure-Dienste, z. B. Application Gateway, Azure Files Premium (Filestore) und SQL-Datenbank (CMS- und Überwachungsdatenbank). Die SAP BOBI-Plattform verfügt über eine integrierte Zonenredundanz. Hierdurch kann die Komplexität der Verwaltung verschiedener Hochverfügbarkeitslösungen reduziert werden.

In der folgenden Abbildung wird für den eingehenden Datenverkehr (HTTPS – TCP/443) ein Lastenausgleich mithilfe der Application Gateway v2-SKU über mehrere Verfügbarkeitszonen hinweg durchgeführt. Das Anwendungsgateway verteilt die Benutzeranforderung auf Webserver, die über Verfügbarkeitszonen verteilt sind. Der Webserver gibt die Anforderung an Verwaltungs- und Verarbeitungsserverinstanzen weiter, die in den Verfügbarkeitszonen auf separaten VMs bereitgestellt werden. Azure Files Premium mit ZRS wird über eine private Verbindung an VMs auf der Verwaltungs- und Speicherebene angefügt, um auf Inhalte wie Berichte, Universe-Daten und Verbindungen zuzugreifen. Die Anwendung greift auf die CMS- und die Überwachungsdatenbank zu, die auf einer zonenredundanten Instanz von SQL-Datenbank ausgeführt werden. Hierbei werden die Datenbanken an mehreren physischen Standorten innerhalb einer Azure-Region repliziert.

![Diagramm: Hochverfügbarkeitsarchitektur für eine SAP BOBI-Plattform unter Windows](media/businessobjects-deployment-guide/businessobjects-deployment-windows-high-availability-availability-zone.png)

Die obige Architektur gewährt Ihnen einen Einblick, wie eine SAP BOBI-Bereitstellung in Azure erfolgen kann. Sie deckt aber nicht alle möglichen Konfigurationsoptionen für eine SAP BOBI-Plattform in Azure ab. Sie können die Bereitstellung basierend auf Ihren Geschäftsanforderungen anpassen, indem Sie für Komponenten wie Load Balancer, File Repository Server und DBMS andere Produkte oder Dienste auswählen.

Falls Verfügbarkeitszonen in Ihrer ausgewählten Region nicht verfügbar sind, können Sie Azure-VMs in Verfügbarkeitsgruppen bereitstellen. In Azure wird sichergestellt, dass die VMs innerhalb einer Verfügbarkeitsgruppe auf mehrere physische Server, Compute-Racks, Speichereinheiten und Netzwerkswitches verteilt werden. Falls es dann zu einem Hardware- oder Softwareausfall kommt, ist nur ein Teil Ihrer VMs betroffen, während die Gesamtlösung weiter betriebsbereit ist.

## <a name="disaster-recovery"></a>Notfallwiederherstellung

In diesem Abschnitt wird die Strategie beschrieben, mit der für eine SAP BOBI-Plattform der Schutz per Notfallwiederherstellung erzielt werden kann. Dies ist eine Ergänzung des Dokuments [Einrichten der Notfallwiederherstellung für die Bereitstellung einer SAP NetWeaver-App mit mehreren Ebenen](../../../site-recovery/site-recovery-sap.md), in dem die wichtigsten Ressourcen für einen allgemeinen Ansatz zur Notfallwiederherstellung für SAP beschrieben sind. Sehen Sie sich für die SAP BOBI-Plattform den SAP-Hinweis [2056228](https://launchpad.support.sap.com/#/notes/2056228) an. Darin werden die folgenden Methoden für die sichere Implementierung einer Umgebung für die Notfallwiederherstellung beschrieben:

 * Vollständige oder selektive Verwendung der Lebenszyklusverwaltung oder eines Verbunds, um den Inhalt des Hauptsystems höher zu stufen oder zu verteilen
 * Regelmäßiges Kopieren der CMS- und FRS-Inhalte

In diesem Leitfaden wird die zweite Option zum Implementieren einer Umgebung für die Notfallwiederherstellung erläutert. Es werden nicht alle möglichen Konfigurationsoptionen für die Notfallwiederherstellung behandelt. Es wird eine Lösung beschrieben, bei der native Azure-Dienste zusammen mit einer passenden Konfiguration der SAP BOBI-Plattform genutzt werden.

>[!Important]
>Die Verfügbarkeit der einzelnen Komponenten auf der SAP BOBI-Plattform sollte in die sekundäre Region integriert werden. Die gesamte Strategie für die Notfallwiederherstellung muss gründlich getestet werden.

### <a name="reference-dr-architecture-for-an-sap-businessobjects-bi-platform"></a>Referenzarchitektur für die Notfallwiederherstellung für eine SAP BusinessObjects BI-Plattform

Bei dieser Referenzarchitektur wird eine Bereitstellung mit mehreren Instanzen der SAP BOBI-Plattform mit redundanten Anwendungsservern ausgeführt. Für die Notfallwiederherstellung sollten Sie für alle Komponenten der SAP BOBI-Plattform ein Failover in eine sekundäre Region ausführen. In der folgenden Abbildung werden Azure Files Premium als Filestore, SQL-Datenbank als CMS- und Überwachungsrepository und Application Gateway zum Vornehmen eines Lastenausgleichs für den Datenverkehr genutzt. Die Strategie zur Erzielung des Notfallwiederherstellungsschutzes ist für jede Komponente anders. Dies ist im folgenden Abschnitt beschrieben.

![Diagramm: Notfallwiederherstellung für die SAP BusinessObjects BI-Plattform unter Windows](media\businessobjects-deployment-guide\businessobjects-deployment-windows-disaster-recovery.png)

### <a name="load-balancer"></a>Load Balancer

Load Balancer wird verwendet, um Datenverkehr auf die Webanwendungsserver einer SAP BOBI-Plattform zu verteilen. In Azure können Sie Azure Load Balancer oder Application Gateway verwenden, um den Datenverkehr per Lastenausgleich auf Ihre Webserver zu verteilen. Um eine Notfallwiederherstellung für die Lastenausgleichsdienste zu erzielen, müssen Sie ein weiteres Lastenausgleichsmodul oder Anwendungsgateway in einer sekundären Region implementieren. Ändern Sie den Eintrag im DNS, und richten Sie einen Verweis auf den Lastenausgleichsdienst ein, der in der sekundären Region ausgeführt wird, damit die URL nach dem Failover im Rahmen der Notfallwiederherstellung unverändert ist.

### <a name="virtual-machines-that-run-web-and-bi-application-servers"></a>Virtuelle Computer, auf denen Web- und BI-Anwendungsserver ausgeführt werden

[Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) kann verwendet werden, um VMs, auf denen Web- und BI-Anwendungsserver ausgeführt werden, in der sekundären Region zu replizieren. Hierbei werden die Server und alle angefügten verwalteten Datenträger in der sekundären Region repliziert, damit Sie bei Katastrophen und Ausfällen leicht ein Failover in Ihre replizierte Umgebung ausführen und weiterarbeiten können. Befolgen Sie die Anleitung zum [Replizieren eines virtuellen Computers in Azure](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md), um mit dem Replizieren aller virtuellen Computer für SAP-Anwendungen in das Azure-Rechenzentrum für die Notfallwiederherstellung zu beginnen.

### <a name="filestore"></a>Filestore

Filestore ist ein Datenträgerverzeichnis, in dem die eigentlichen Dateien, z. B. Berichte und BI-Dokumente, gespeichert werden. Es ist wichtig, dass alle im Filestore enthaltenen Dateien mit der Region für die Notfallwiederherstellung (Disaster Recovery, DR) synchron sind. Basierend auf dem Typ des Dateifreigabediensts, den Sie für die Ausführung der SAP BOBI-Plattform unter Windows verwenden, muss die passende DR-Strategie verwendet werden, damit der Inhalt synchron ist. Beispiel:

- **Azure Files Premium** unterstützt nur LRS und ZRS. Für die Azure Files Premium-Strategie zur Notfallwiederherstellung können Sie [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) oder [Azure PowerShell](/powershell/module/az.storage/?preserve-view=true&view=azps-5.8.0) verwenden, um die Dateien in ein anderes Speicherkonto in einer anderen Region zu kopieren. Weitere Informationen finden Sie unter [Notfallwiederherstellung und Speicherkontofailover](../../../storage/common/storage-disaster-recovery-guidance.md).
- **Azure NetApp Files** stellt NFS- und SMB-Volumes bereit, sodass jedes dateibasierte Kopiertool zur Replikation von Daten zwischen Azure-Regionen verwendet werden kann. Weitere Informationen zum Kopieren eines Azure NetApp Files-Volumes in eine andere Region finden Sie unter [Häufig gestellte Fragen zu Azure NetApp Files](../../../azure-netapp-files/faq-data-migration-protection.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region).

  Sie können die regionsübergreifende Replikation von Azure NetApp Files verwenden, die sich derzeit in der [Vorschauversion](https://azure.microsoft.com/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/) befindet. Hierfür wird die NetApp SnapMirror-Technologie genutzt. Bei dieser Technologie werden nur geänderte Blöcke in einem komprimierten, effizienten Format über das Netzwerk gesendet. Diese proprietäre Technologie minimiert die Menge an Daten, die regionsübergreifend repliziert werden muss, wodurch Datenübertragungskosten eingespart werden. Darüber hinaus wird auch die Replikationsdauer reduziert, damit Sie einen niedrigeren RPO-Wert erzielen können. Weitere Informationen finden Sie unter [Regionsübergreifende Replikation: Anforderungen und Überlegungen](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md).

### <a name="cms-database"></a>CMS-Datenbank

Bei der CMS- und der Überwachungsdatenbank in der DR-Region muss es sich um eine Kopie der Datenbanken handeln, die in der primären Region ausgeführt werden. Es ist wichtig, dass die Datenbank basierend auf dem Datenbanktyp in die Region für die Notfallwiederherstellung kopiert wird, während die jeweils erforderlichen RTO- und RPO-Werte beachtet werden. In diesem Abschnitt werden die unterschiedlichen verfügbaren Optionen für die einzelnen Datenbanklösungen in Azure beschrieben, die für eine SAP BOBI-Anwendung unter Windows unterstützt werden.

#### <a name="azure-sql-database"></a>Azure SQL-Datenbank

Für eine DR-Strategie für [SQL-Datenbank](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md) sind zwei Optionen zum Kopieren der Datenbank in die sekundäre Region verfügbar. Die beiden Wiederherstellungsoptionen weisen unterschiedliche RTO- und RPO-Ebenen auf. Weitere Informationen zum RTO- und RPO-Wert für die Wiederherstellungsoptionen finden Sie unter [Wiederherstellen einer Datenbank auf dem vorhandenen Server](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md#recover-a-database-to-the-existing-server).

Option 1: [Georedundante Datenbankwiederherstellung](../../../azure-sql/database/recovery-using-backups.md#geo-restore)

   Standardmäßig werden Daten von SQL-Datenbank in [GRS-Blobs](../../../storage/common/storage-redundancy.md) gespeichert, die in einer [gekoppelten Region](../../../best-practices-availability-paired-regions.md) repliziert werden. Für eine SQL-Datenbank-Instanz kann die Redundanz für Sicherungsspeicher beim Erstellen der CMS- und der Überwachungsdatenbank konfiguriert oder für eine vorhandene Datenbank aktualisiert werden. Änderungen, die an einer vorhandenen Datenbank vorgenommen werden, gelten nur für zukünftige Sicherungen. Sie können eine Datenbank in einer beliebigen SQL-Datenbank-Instanz in einer beliebigen Azure-Region aus den letzten georeplizierten Sicherungen wiederherstellen. Geowiederherstellung verwendet eine georeplizierte Sicherung als Quelle. Zwischen der Erstellung einer Sicherung und ihrer Georeplikation in einem Azure-Blob in einer anderen Region tritt eine Verzögerung auf. Daher kann die wiederhergestellte Datenbank bis zu einer Stunde hinter der ursprünglichen Datenbank zurückliegen.

   >[!Important]
   >Die Geowiederherstellung ist für SQL-Datenbanken verfügbar, für die georedundanter [Sicherungsspeicher](../../../azure-sql/database/automated-backups-overview.md#backup-storage-redundancy) konfiguriert wurde.

Option 2: [Georeplikation](../../../azure-sql/database/active-geo-replication-overview.md) oder [Autofailover-Gruppe](../../../azure-sql/database/auto-failover-group-overview.md)

   Die aktive Georeplikation ist ein Feature von SQL-Datenbank, mit dem Sie lesbare sekundäre Datenbanken für einzelne Datenbanken auf einem Server in derselben oder einer anderen Region erstellen können. Wenn die Georeplikation für CMS- und die Überwachungsdatenbank aktiviert ist, kann die Anwendung ein Failover in eine sekundäre Datenbank in einer anderen Azure-Region initiieren. Die Georeplikation ist für einzelne Datenbanken aktiviert. Um ein transparentes und koordiniertes Failover mehrerer Datenbanken (CMS und Überwachung) für eine SAP BOBI-Anwendung zu ermöglichen, ist es aber ratsam, eine Autofailover-Gruppe zu verwenden. Hierüber wird die Gruppensemantik auf Grundlage der aktiven Georeplikation zur Verfügung gestellt. Dies bedeutet, dass der gesamte SQL-Server (alle Datenbanken) in einer anderen Region repliziert wird, und nicht in einzelnen Datenbanken. Sehen Sie sich die Tabelle mit den Funktionen an, in der [die Georeplikation mit Failovergruppen verglichen wird](../../../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md#compare-geo-replication-with-failover-groups).

   Autofailover-Gruppen verfügen über Listenerendpunkte mit Lese-/Schreibzugriff bzw. Schreibschutz, die während eines Failovers unverändert bleiben. Der Endpunkt mit Lese-/Schreibzugriff kann als Listener im ODBC-Verbindungseintrag für die CMS- und die Überwachungsdatenbank genutzt werden. Sowohl bei manueller als auch automatischer Failover-Aktivierung schaltet das Failover alle sekundären Datenbanken in der Gruppe zur primären um. Nach Abschluss des Datenbankfailovers wird der DNS-Eintrag automatisch aktualisiert, um die Endpunkte in die neue Region umzuleiten. Die Anwendung wird automatisch mit der CMS-Datenbank verbunden, da der Endpunkt mit Lese-/Schreibzugriff als Listener für die ODBC-Verbindung genutzt wird.

   In der folgenden Abbildung wird eine Autofailover-Gruppe für den SQL-Server (azussqlbodb), der in der Region „USA, Osten 2“ ausgeführt wird, in der sekundären Region „USA, Osten“ (Standort für die Notfallwiederherstellung) repliziert. Der Listenerendpunkt mit Lese-/Schreibzugriff wird als Listener in einer ODBC-Verbindung für den BI-Anwendungsserver unter Windows genutzt. Nach Abschluss des Failovervorgangs ist der Endpunkt unverändert. Es ist kein manueller Eingriff erforderlich, um die BI-Anwendung mit der SQL-Datenbank in der sekundären Region zu verbinden.

   ![Screenshot: Autofailover-Gruppen für SQL-Datenbank](media\businessobjects-deployment-guide\businessobjects-deployment-windows-sql-failover-group.png)

   Diese Option bietet eine niedrigere RTO und RPO als Option 1. Weitere Informationen zu dieser Option finden Sie unter [Verwenden von Autofailover-Gruppen für ein transparentes und koordiniertes Failover mehrerer Datenbanken](../../../azure-sql/database/auto-failover-group-overview.md).

#### <a name="azure-database-for-mysql"></a>Azure Database for MySQL

Azure Database for MySQL verfügt über Optionen für die Wiederherstellung einer Datenbank, wenn es zu einem Katastrophenfall kommt. Wählen Sie die passende Option für Ihr Unternehmen aus:

* Aktivieren Sie die Nutzung von regionsübergreifenden Lesereplikaten, um Ihre Planung für die Geschäftskontinuität und Notfallwiederherstellung zu verbessern. Sie können über einen Quellserver die Replikation für bis zu fünf Replikate durchführen. Lesereplikate werden asynchron aktualisiert, indem die Azure Database for MySQL-Replikationstechnologie für binäre Protokolle verwendet wird. Replikate sind neue Server, die Sie ähnlich wie normale Azure Database for MySQL-Server verwalten. Weitere Informationen zu Lesereplikaten, verfügbaren Regionen, Einschränkungen und zur Vorgehensweise beim Failover finden Sie unter [Lesereplikate in Azure Database for MySQL](../../../mysql/concepts-read-replicas.md).

* Verwenden Sie das Geowiederherstellungsfeature von Azure Database for MySQL, bei dem der Server mithilfe von georedundanten Sicherungen wiederhergestellt wird. Auf diese Sicherungen kann selbst dann zugegriffen werden, wenn die Region, in der Ihr Server gehostet wird, offline ist. Sie können eine Wiederherstellung aus diesen Sicherungen in eine andere Region ausführen und den Server wieder online schalten.

  > [!Important]
  > Die Geowiederherstellung ist nur möglich, wenn Sie den Server mit einem georedundanten Sicherungsspeicher bereitgestellt haben. Das Ändern der Optionen für die Sicherungsredundanz nach der Erstellung des Servers wird nicht unterstützt. Weitere Informationen finden Sie unter [Optionen für Sicherungsredundanz](../../../mysql/concepts-backup.md#backup-redundancy-options).

In der folgenden Tabelle sind die Empfehlungen für die Notfallwiederherstellung für alle Ebenen aufgeführt, die in diesem Beispiel verwendet werden.

| Ebenen der SAP BOBI-Plattform                          | Empfehlung                                               |
| ------------------------------------------------ | ------------------------------------------------------------ |
| Azure Application Gateway oder Azure Load Balancer | Parallele Einrichtung von Application Gateway in einer sekundären Region    |
| Webanwendungsserver                          | Replikation mit Azure Site Recovery                             |
| BI-Anwendungsserver                           | Replizieren über Site Recovery                             |
| Azure Premium-Dateien                              | AzCopy *oder* Azure PowerShell                               |
| Azure NetApp Files                               | Dateibasiertes Kopiertool zum Replizieren von Daten in einer sekundären Region *oder* regionsübergreifende Replikation in Azure NetApp Files (Vorschau) |
| Azure SQL-Datenbank                               | Georeplikation/Autofailover-Gruppen *oder* Geowiederherstellung     |
| Azure Database for MySQL                         | Regionsübergreifende Lesereplikate *oder* Wiederherstellung einer Sicherung aus georedundanten Sicherungen |

## <a name="next-steps"></a>Nächste Schritte

- [Einrichten der Notfallwiederherstellung für die Bereitstellung einer SAP-App mit mehreren Ebenen](../../../site-recovery/site-recovery-sap.md)
- [Azure Virtual Machines – Planung und Implementierung für SAP](planning-guide.md)
- [Azure Virtual Machines – Bereitstellung für SAP](deployment-guide.md)
- [Azure Virtual Machines – DBMS-Bereitstellung für SAP](./dbms_guide_general.md)
