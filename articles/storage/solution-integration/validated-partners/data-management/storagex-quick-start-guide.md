---
title: Analysieren und Migrieren Ihrer Dateidaten zu Azure mit Data Dynamics StorageX
titleSuffix: Azure Storage
description: Leitfaden mit den ersten Schritten zum Implementieren von Data Dynamics StorageX. Der Leitfaden zeigt, wie Sie Ihre Daten zu Azure Files, Azure NetApp Files, Azure Blob Storage oder einer verfügbaren NAS-Lösung eines ISV migrieren.
author: dukicn
ms.author: nikoduki
ms.date: 06/15/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: partner
ms.openlocfilehash: 4b3f85c06c8dccd5873556c8c1ebfe1f0087b11e
ms.sourcegitcommit: f3f2ec7793ebeee19bd9ffc3004725fb33eb4b3f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2021
ms.locfileid: "129407606"
---
# <a name="migrate-data-to-azure-with-data-dynamics-storagex"></a>Migrieren von Daten zu Azure mit Data Dynamics StorageX

Dieser Artikel unterstützt Sie beim Bereitstellen von Data Dynamics StorageX in Microsoft Azure. Er enthält wichtige Konzepte zur Funktionsweise von StorageX, Voraussetzungen für die Bereitstellung, eine Beschreibung des Installationsprozesses und Anleitungen für den Betrieb. Ausführlichere Informationen finden Sie im [Data Dynamics-Kundenportal](https://www.datdynsupport.com/).

Data Dynamics StorageX ist eine einheitliche Verwaltungsplattform für unstrukturierte Daten, die das Analysieren, Verwalten und Verschieben von Daten in heterogenen Speicherumgebungen ermöglicht. Grundlegende Funktionen sind:
- Datenverschiebungsfunktionen
    - NAS zu NAS (mit oder ohne Analyse)
    - NAS-zu-Objekt-Replikation
    - NAS-zu-Objekt-Archiv
- Überprüfungsbericht für doppelt vorhandene Dateien
- Bericht für offene Freigaben
- Dateimetadatenanalyse mit benutzerdefiniertem Tagging
  
Weitere Funktionen in StorageX und Vergleiche mit ähnlichen Tools finden Sie in unserer [Vergleichstabelle](./migration-tools-comparison.md).

In diesem Dokument werden drei Hauptthemen behandelt:
- Prozess für die Bereitstellung des StorageX-Angebots in der Azure-Umgebung
- Grundlegende Schritte zum Hinzufügen von Datei- und Cloudspeicherressourcen zu StorageX
- Erstellen von Datenverschiebungsrichtlinien

## <a name="reference-architecture"></a>Referenzarchitektur

Das folgende Diagramm veranschaulicht die Referenzarchitektur für Bereitstellungen vom lokalen Standort zu Azure und für Azure-interne Bereitstellungen von StorageX.

:::image type="content" source="./media/storagex-quick-start-guide/storagex-architecture.png" alt-text="Diagramm, das die Referenzarchitektur für die StorageX-Implementierung veranschaulicht":::

## <a name="storagex-interoperability-matrix"></a>Interoperabilitätsmatrix für StorageX

| Komponente | SMB | NFS | Notizen |
| ------- |--- | --- | --- |
| **Migration** | SMB zu SMB | NFSv3 zu NFSv3 | Kombination für mehrere Protokolle möglich |
| **Datei-zu-Objekt-Archiv** | SMB zu Azure Blob Storage | NFSv3 zu Azure Blob Storage | Datenarchivierung zur Kostenoptimierung |
| **Datei-zu-Objekt-Replikation** | SMB zu Azure Blob Storage | NFSv3 zu Azure Blob Storage | Datenreplikation zum Erhöhen des Schutzes |

## <a name="before-you-begin"></a>Vorbereitung

Eine Vorabplanung vereinfacht die Migration und verringert die Risiken. Einige Tipps zur Vereinfachung der Migration sind:

- Erfassen Sie Daten für die Quell- und Zieldienste.
- Stellen Sie sicher, dass Sie über eine Liste Ihrer Konnektivitätspunkte und aller erforderlichen Netzwerkports verfügen.
- Ihr StorageX-Server in Azure muss Teil Ihrer Active Directory-Infrastruktur sein. Stellen Sie sicher, dass der Server von einem Administrator mit Active Directory-Berechtigungen zu Active Directory hinzugefügt wird.
- Für einfachere Implementierungen kann [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=Overview) verwendet werden.

### <a name="get-started-with-azure"></a>Erste Schritte mit Azure

Microsoft bietet ein Framework, das Ihnen den Einstieg in Azure erleichtert. Das [Cloud Adoption Framework](/azure/architecture/cloud-adoption/) (CAF) ist ein detailliertes Konzept für die digitale Transformation in Unternehmen und ein umfassender Leitfaden für die Planung der Cloudeinführung in einer Produktionsumgebung. Das CAF enthält eine Schrittanleitung zum [Einrichten von Azure](/azure/cloud-adoption-framework/ready/azure-setup-guide/), damit Sie es schnell und sicher in Betrieb nehmen können. Eine interaktive Version finden Sie im [Azure-Portal](https://portal.azure.com/?feature.quickstart=true#blade/Microsoft_Azure_Resources/QuickstartCenterBlade). Sie finden dort Beispielarchitekturen, spezielle Best Practices für die Bereitstellung von Anwendungen sowie kostenlose Trainingsressourcen, mit denen Sie die notwendigen Fachkenntnisse zu Azure erhalten.

### <a name="considerations-for-migrations"></a>Überlegungen zu Migrationen

Bei der Migration von Dateidaten zu Azure sind mehrere wichtige Aspekte zu berücksichtigen. Bevor Sie fortfahren, erfahren Sie mehr:

- [Übersicht über die Azure Storage-Migration](../../../common/storage-migration-overview.md)
- Aktuelle von Data Dynamics StorageX unterstützte Features in der [Vergleichsmatrix für Migrationstools](./migration-tools-comparison.md)

Denken Sie daran, dass Sie genügend Netzwerkkapazität benötigen, um Migrationen zu unterstützen, ohne Produktionsanwendungen zu beeinträchtigen. In diesem Abschnitt werden die Tools und Techniken beschrieben, die für die Bewertung Ihrer Netzwerkanforderungen verfügbar sind.

#### <a name="determine-unutilized-internet-bandwidth"></a>Nicht ausgelastete Internetbandbreite ermitteln

Es ist wichtig zu wissen, wie viel Spielraum an ungenutzter Bandbreite (oder welchen *Toleranzbereich*) Sie täglich haben. Auf diese Weise können Sie bewerten, ob Sie Ihre Ziele für Folgendes erreichen können:

- Anfangszeit für Migrationen, wenn Sie nicht Azure Data Box für die Offlinemethode verwenden
- erforderliche Zeit für die inkrementelle Neusynchronisierung vor dem endgültigen Wechsel zum Zieldateidienst

Verwenden Sie die folgenden Methoden, um den Spielraum an Bandbreite zu ermitteln, der für Azure zur Verfügung steht.

- Wenn Sie ein vorhandener Azure ExpressRoute-Kunde sind, können Sie die [Inanspruchnahme](../../../../expressroute/expressroute-monitoring-metrics-alerts.md#circuits-metrics) im Azure-Portal anzeigen.
- Wenden Sie sich an Ihren ISP, und fordern Sie Berichte an, die Ihre vorhandene tägliche und monatliche Nutzung zeigen.
- Es gibt eine Reihe von Tools, die durch Überwachung des Netzwerkdatenverkehrs auf Ebene des Routers bzw. Switches die Nutzung messen können:
  - [SolarWinds Bandwidth Analyzer Pack](https://www.solarwinds.com/network-bandwidth-analyzer-pack?CMP=ORG-BLG-DNS)
  - [Paessler PRTG](https://www.paessler.com/bandwidth_monitoring)
  - [Cisco Network Assistant](https://www.cisco.com/c/en/us/products/cloud-systems-management/network-assistant/index.html)
  - [WhatsUp Gold](https://www.whatsupgold.com/network-traffic-monitoring)

#### <a name="creating-a-storagex-service-account"></a>Erstellen eines StorageX-Dienstkontos

Für SMB-Migrationen wird ein Dienstkonto zum Ausführen der StorageX-Dienste empfohlen. Das Dienstkonto ermöglicht die Ablaufverfolgung der Zugriffe aus Supportgründen. Gleichzeitig werden die richtigen Berechtigungen erteilt, um Vollzugriff zu gewähren.

Der Prozess, der die Datenverschiebungsrichtlinien in den universellen StorageX-Datenengines ausführt, verwendet das StorageX-Dienstkonto. Dem Konto müssen alle erforderlichen Berechtigungen in der Active Directory-Domäne zugewiesen sein.

Sie können ein ähnliches Konto wie den Domänenadministrator erstellen, wenn dies in Ihrer Umgebung zulässig ist. Es wird empfohlen, Berechtigungen direkt auf dem NAS-Server zu gewähren. Fügen Sie das Dienstkonto dazu zu den Gruppen „Administratoren“ und „Sicherungsoperatoren“ hinzu.

Für das StorageX-Dienstkonto sind folgende Berechtigungen erforderlich:

- SeServiceLogonRight
- SeSecurityPrivilege 
- SeBackupPrivilege 
- SeRestorePrivilege 
- SeAuditPrivilege
- SeInteractiveLogonRight

Für den NFS-Zugriff ist eine Exportregel für die IP-Adresse des StorageX-Servers erforderlich, außerdem muss „RW“ und „root“ Zugriff gewährt werden, z. B. „root=10.2.3.12, rw=10.2.3.12“, wobei 10.2.3.12 die IP-Adresse des StorageX-Servers ist.

## <a name="deployment-guide"></a>Bereitstellungshandbuch

Dieser Abschnitt enthält Anleitungen zum Bereitstellen eines StorageX-Servers und anderer StorageX-Komponenten in Ihrer Azure-Umgebung.

Vor Beginn der Implementierung müssen mehrere Voraussetzungen erfüllt sein:

- Der Zielspeicherdienst muss identifiziert und erstellt werden.
- Es muss sichergestellt sein, dass die richtigen Ports für den Zugriff auf die Speicherdienste geöffnet sind. Sie können das Tool [portqryui](https://www.microsoft.com/download/details.aspx?id=24009) von Microsoft verwenden, um festzustellen, ob die erforderlichen Ports geöffnet sind.

Weitere Informationen finden Sie hier:

- Erstellen einer [Azure-Dateifreigabe](../../../files/storage-how-to-create-file-share.md)
- Erstellen eines [SMB-Volumes](../../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md) oder [NFS-Exports](../../../../azure-netapp-files/azure-netapp-files-create-volumes.md) in Azure NetApp Files

Für alle SMB-Migrationen muss Active Directory ordnungsgemäß eingerichtet sein, bevor Ressourcen hinzugefügt werden. Wir verwenden eine vorhandene Azure NetApp Files-Implementierung mit einem neuen Volume für das Migrationsziel. Damit ein neues Azure NetApp Files-Volume erstellt werden kann, müssen folgende Maßnahmen durchgeführt werden:

- [Erstellen eines Azure NetApp Files-Kontos](../../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal#create-a-netapp-account)
- [Kapazitätspool erstellen](../../../../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md?tabs=azure-portal#create-a-capacity-pool)
- [Hinzufügen des Azure NetApp Files-Kontos zur Active Directory-Domäne](../../../../azure-netapp-files/create-active-directory-connections.md)

Nachdem die Konfiguration für Azure NetApp Files erledigt wurde, kann eine Freigabe erstellt werden, die für die Migration verwendet wird. 

1. Navigieren Sie zum vorhandenem Azure NetApp Files-Konto, und wählen Sie **Volumes** aus. Wählen Sie **Volume hinzufügen** aus. Weisen Sie einen Namen zu, und wählen Sie Kapazitätspool sowie Netzwerkeinrichtung aus.

    :::image type="content" source="./media/storagex-quick-start-guide/anf-create-volume-1.png" alt-text="Screenshot: Hinzufügen eines neuen Azure NetApp Files Volumes":::

1. Wählen Sie das **SMB**-Protokoll sowie eine vorhandene Active Directory-Verbindung aus, und geben Sie einen **Freigabenamen** ein.

    :::image type="content" source="./media/storagex-quick-start-guide/anf-create-volume-2.png" alt-text="Screenshot: SMB-Eigenschaften für ein Azure NetApp Files Volume":::

1. Fügen Sie **Tags** hinzu (sofern Sie welche verwenden), und **erstellen** Sie eine Freigabe.

    Nachdem das Volume erstellt wurde, kann die StorageX-Bereitstellung fortgesetzt werden.

1.  Stellen Sie das Datenverschiebungs- und Mobilitätsangebot von Data Dynamics in Azure bereit. Es wird empfohlen, dafür [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/datadynamicsinc1581991927942.vm_4?tab=Overview) zu verwenden. Fügen Sie den bereitgestellten Server zu ihrer Active Directory-Domäne hinzu.
1.  Überprüfen Sie auf dem bereitgestellten Server, ob alle erforderlichen Netzwerkports geöffnet sind.
1.  Führen Sie das StorageX-Installationsskript aus. Das Installationsskript ist auf dem Desktop verfügbar, wenn Sie sich beim bereitgestellten Server in Azure anmelden. 
    Alle Installationsdateien und Ausgabeprotokolle befinden sich im Ordner **C:\ProgramData\data Dynamics\StorageX**. In diesem Ordner enthält der Unterordner **InstallationFiles** die komprimierten Quellinstallationsdateien, während der Ordner **SilentInstaller-DD** die XML-Konfigurationsdateien und Installationsprotokolle enthält.

    Standardmäßig stellt das Installationsskript die folgenden Dienste bereit:

    - StorageX
    - StorageX API
    - StorageX Processing Engine
    - StorageX Universal Data Engine
    - StorageX File Recovery
    - Elasticsearch
    - Microsoft SQL Express
    - Microsoft SQL Management Studio
    - Kibana
    - Jede unterstützende Software (z. B. Java)

    Das Installationsskript verfügt über eine XML-Konfigurationsdatei, in der die betreffenden Dienste angepasst werden können. Es wird jedoch nicht empfohlen, die Konfiguration manuell zu ändern.

    Klicken Sie zum Installieren mit der rechten Maustaste auf die Verknüpfung **StorageXPOCSilentInstaller** auf dem Desktop, und wählen Sie **Als Administrator ausführen** aus. Nachdem die Installation gestartet wurde, werden Sie aufgefordert, das StorageX-Dienstkonto und das Kennwort für dieses Konto einzugeben.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-cli-1.jpg" alt-text="Screenshot der Befehlszeilenschnittstelle für die Installation":::

    Das Installationsskript wird fortgesetzt und installiert alle Dienste. Wenn der Installationsvorgang beendet wurde, wird eine entsprechende Meldung in der Befehlszeilenschnittstelle angezeigt.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-cli-2.jpg" alt-text="Screenshot: Installation beendet":::

    StorageX wird automatisch gestartet.

## <a name="migration-guide"></a>Migrationsleitfaden

Nachdem die Installation beendet wurde und alle Dienste gestartet wurden, kann der Migrationsprozess gestartet werden.

1.  Öffnen Sie die StorageX-Konsole.
1.  Klicken Sie auf die Registerkarte **Storage Resources** (Speicherressourcen).
1.  Fügen Sie in der Ansicht **My Resources** (Meine Ressourcen) Ihre NAS-Geräte oder Speicherdienste, sogenannte **Speicherressourcen** in StorageX, hinzu. Sie können verschiedene Typen hinzufügen:
    - **Add file storage resource** (Dateispeicherressource hinzufügen) für lokale NAS-Geräte, Windows-Dateiserver, Azure NetApp Files oder generische Dateiserver
    - **Add object storage resource** (Objektspeicherressource hinzufügen) für S3 oder Azure Blob Storage (einschließlich Azure Files Service)
    - **Add DFS namespace** (DFS-Namespace hinzufügen) für DFS-Dateiserver

    Wählen Sie **Add file storage resource** (Dateispeicherressource hinzufügen) aus, um den Assistenten zu starten und das Azure NetApp Files-Migrationsziel hinzuzufügen.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-1.png" alt-text="Screenshot: Hinzufügen einer neuen Ressource zur StorageX-Bereitstellung":::

1. Wählen Sie **Generic CIFS** (Generisches CIFS) für CIFS- und SMB-Migrationen aus.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-2.png" alt-text="Screenshot: Hinzufügen einer generischen CIFS-Ressource zur StorageX-Bereitstellung":::

1. Suchen Sie das Azure NetApp Files-Computerkonto. Das Azure NetApp Files-Computerkonto wurde beim Hinzufügen zur Active Directory-Domäne erstellt.
   
    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-3.png" alt-text="Screenshot: Hinzufügen des Azure NetApp Files-Computerkontos":::

    **Beenden** Sie den Assistenten, und überprüfen Sie, ob das Konto hinzugefügt wurde und die erstellte Freigabe enthält.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-add-resource-4.png" alt-text="Screenshot: Durchsuchen der hinzugefügten Ressourcen":::

    Alle Quellen und Ziel müssen jeweils als separate Ressource hinzugefügt werden. Das Beispiel veranschaulicht das Hinzufügen einer Azure NetApp Files-Freigabe. Der Screenshot kann für andere Dienste geringfügig abweichen.

1.  Klicken Sie auf die Registerkarte **Data Movement** (Datenverschiebung).

1.  Klicken Sie im Strukturbereich mit der rechten Maustaste auf den Ordner, in dem Sie die neue Richtlinie erstellen möchten, und wählen Sie **Phased Migration Policy** (Richtlinie für stufenweise Migration) aus.

1.  Führen Sie die Schritte im Assistenten aus, um eine Richtlinie für die stufenweise Migration zu erstellen. Wählen Sie die entsprechenden Ordner für **Source** (Quelle) und **Destination** (Ziel) aus.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-migration-1.png" alt-text="Screenshot: Auswählen der richtigen Quell- und Zielordner":::

1. Geben Sie einen Namen und eine Beschreibung für die Richtlinie an, und **beenden** Sie den Assistenten.

1.  Klicken Sie mit der rechten Maustaste auf die neue Richtlinie, und wählen Sie **Run** (Ausführen) aus, um ein Testmigration auszuführen. Es wird empfohlen, die Sicherheitskonfiguration für mehrere Dateien in der Quellressource zu überprüfen, um zu überprüfen, ob die erforderlichen Berechtigungen angegeben wurden.

    :::image type="content" source="./media/storagex-quick-start-guide/storagex-migration-2.png" alt-text="Screenshot: Ausführen einer definierten Richtlinie":::

1. Überprüfen Sie den Status der Richtlinie, um sicherzustellen, dass die erste Migration erfolgreich ausgeführt wurde. 

## <a name="support"></a>Support

Wenn Probleme auftreten, können Microsoft und Data Dynamics Ihnen über die reguläre Supportkanäle Hilfe anbieten. Wenden Sie sich bei Infrastrukturproblemen oder bei Problemen mit einem Azure-Dienst an Microsoft. Wenden Sie sich bei Problemen im Zusammenhang mit der Data Dynamics StorageX-Installation an Data Dynamics.

### <a name="how-to-open-a-case-with-azure"></a>So erstellen Sie einen Supportfall für Azure-Probleme

Suchen Sie im [Azure-Portal](https://portal.azure.com) auf der Suchleiste oben nach Support. Wählen Sie **Hilfe und Support** -> **Neue Supportanfrage**.

### <a name="how-to-open-a-case-with-data-dynamics"></a>So erstellen Sie einen Supportfall für Data Dynamics-Probleme

Rufen Sie das [Data Dynamics-Supportportal](https://www.datdynsupport.com/) auf. Geben Sie Ihre E-Mail-Adresse an, wenn Sie sich noch nicht registriert haben. Unser Supportteam erstellt dann ein Konto für Sie. Erstellen Sie eine Benutzeranfrage, nachdem Sie sich angemeldet haben. Notieren Sie sich beim Erstellen der Anfrage die Supportanfragenummer, wenn Sie bereits einen Azure-Supportfall erstellt haben.

## <a name="next-steps"></a>Nächste Schritte

Sie können verschiedene Ressourcen nutzen, um mehr zu erfahren:

- [Übersicht über die Azure Storage-Migration](../../../common/storage-migration-overview.md)
- Von Data Dynamics StorageX unterstützte Features in der [Vergleichsmatrix für Migrationstools](./migration-tools-comparison.md)
- [Data Dynamics](https://www.datadynamicsinc.com/)
- Das [Data Dynamics-Kundenportal](https://www.datdynsupport.com/) enthält die vollständige Dokumentation für StorageX.