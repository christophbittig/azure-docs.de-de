---
title: Migration der StorSimple 8000-Serie zur Azure-Dateisynchronisierung
description: Erfahren Sie, wie Sie eine StorSimple 8100- oder 8600-Appliance zur Azure-Dateisynchronisierung migrieren.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 10/22/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: ba5be8cad5c7189d207a8e2915e970589e414175
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132063154"
---
# <a name="storsimple-8100-and-8600-migration-to-azure-file-sync"></a>StorSimple 8100- und 8600-Migration zur Azure-Dateisynchronisierung

Die StorSimple 8000-Serie wird durch physische, lokale Appliances der Typen 8100 oder 8600 und die zugehörigen Clouddienstkomponenten dargestellt. Die virtuellen Geräte StorSimple 8010 und 8020 werden ebenfalls in diesem Migrationsleitfaden behandelt. Es ist möglich, die Daten von einer dieser Appliances mit der optionalen Azure-Dateisynchronisierung in Azure-Dateifreigaben zu migrieren. Langfristig ist die Azure-Dateisynchronisierung der standardmäßige und strategische Azure-Dienst, der die lokale StorSimple-Funktionalität ersetzt.

Die StorSimple 8000-Serie erreicht im Dezember 2022 das [Ende des Lebenszyklus](/lifecycle/products/azure-storsimple-8000-series). Es ist wichtig, möglichst bald mit der Planung der Migration zu beginnen. Dieser Artikel enthält die erforderlichen Hintergrundinformationen und Migrationsschritte für eine erfolgreiche Migration zur Azure-Dateisynchronisierung.

## <a name="phase-1-prepare-for-migration"></a>Phase 1: Vorbereiten der Migration

Dieser Abschnitt enthält die Schritte, die Sie zu Beginn der Migration von StorSimple-Volumes zu Azure-Dateifreigaben ausführen sollten.

### <a name="inventory"></a>Inventarisierung

Wenn Sie mit der Planung Ihrer Migration beginnen, ermitteln Sie zunächst alle StorSimple-Appliances und -Volumes, die Sie migrieren müssen. Anschließend können Sie sich für den Migrationspfad entscheiden, der für Sie am besten geeignet ist.

* Für physische StorSimple-Appliances (Serie 8000) verwenden Sie dieses Migrationshandbuch.
* Für virtuelle Appliances, [StorSimple 1200-Serie, verwenden Sie eine anderes Migrationsanleitung](storage-files-migration-storsimple-1200.md).

### <a name="migration-cost-summary"></a>Zusammenfassung der Migrationskosten

Migrationen zu Azure-Dateifreigaben von StorSimple-Volumes über Migrationsaufträge in einer StorSimple Data Manager-Ressource sind kostenlos. Während und nach einer Migration können ggf. andere Kosten anfallen:

* **Netzwerkausgang:** Ihre StorSimple-Dateien befinden sich in einem Speicherkonto in einer bestimmten Azure-Region. Wenn Sie die Azure-Dateifreigaben, zu denen Sie migrieren, in einem Speicherkonto in derselben Azure-Region bereitstellen, entstehen keine Ausgangskosten. Sie können Ihre Dateien im Rahmen dieser Migration in ein Speicherkonto in einer anderen Region verschieben. In diesem Fall werden Ihnen Ausgangskosten in Rechnung gestellt.
* **Azure-Dateifreigabetransaktionen:** Werden Dateien auf eine Azure-Dateifreigabe kopiert (im Rahmen einer Migration oder auch nicht), fallen Transaktionskosten an, weil Dateien und Metadaten geschrieben werden. Es empfiehlt sich, dass Sie Ihre Azure-Dateifreigabe während der Migration auf der für Transaktionen optimierten Ebene starten. Wechseln Sie nach Abschluss der Migration zu der von Ihnen gewünschten Ebene. In den folgenden Phasen ist dies an der entsprechenden Stelle jeweils angegeben.
* **Wechseln einer Ebene einer Azure-Dateifreigabe:** Ein Wechseln der Ebene einer Azure-Dateifreigabe führt zu Kosten für Transaktionen. In den meisten Fällen ist es kostengünstiger, die Ratschläge aus dem vorherigen Punkt zu befolgen.
* **Speicherkosten:** Wenn in dieser Migration damit begonnen wird, Dateien in eine Azure-Dateifreigabe zu kopieren, wird Azure Files-Speicher genutzt und in Rechnung gestellt. Migrierte Sicherungen werden zu [Momentaufnahmen von Azure-Dateifreigaben](storage-snapshots-files.md). Momentaufnahmen für Dateifreigaben belegen nur die Speicherkapazität für die Unterschiede, die sie enthalten.
* **StorSimple:** Solange Sie keine Möglichkeit haben, die Bereitstellung der StorSimple-Geräte und -Speicherkonten aufzuheben, fallen weiter StorSimple-Kosten für Speicher, Sicherungen und Geräte an.

### <a name="direct-share-access-vs-azure-file-sync"></a>Direkter Zugriff auf Dateifreigaben im Vergleich mit Azure-Dateisynchronisierung

Azure-Dateifreigaben eröffnen eine ganz neue Welt der Möglichkeiten zum Strukturieren der Bereitstellung Ihrer Dateidienste. Eine Azure-Dateifreigabe ist lediglich eine SMB-Freigabe in der Cloud, die Sie so einrichten können, dass Benutzer direkt über das SMB-Protokoll mit der vertrauten Kerberos-Authentifizierung und vorhandenen NTFS-Berechtigungen (Datei- und Ordnerzugriffssteuerungslisten) darauf zugreifen können. Erfahren Sie mehr über [identitätsbasierten Zugriff auf Azure-Dateifreigaben](storage-files-active-directory-overview.md).

Eine Alternative zum direkten Zugriff ist die [Azure-Dateisynchronisierung](../file-sync/file-sync-planning.md). Die Azure-Dateisynchronisierung ist das direkte Gegenstück zur StorSimple-Funktionalität, bei der häufig verwendete Dateien lokal zwischengespeichert werden.

Die Azure-Dateisynchronisierung ist ein Microsoft-Clouddienst, der auf zwei Hauptkomponenten basiert:

* Dateisynchronisation und Cloudtiering zur Erstellung eines leistungsfähigen Zugriffscaches auf eine beliebige Windows Server-Instanz.
* Dateifreigaben als nativer Speicher in Azure, auf den über verschiedene Protokolle wie SMB und Datei-REST zugegriffen werden kann.

In Azure-Dateifreigaben werden wichtige Dateigenauigkeitsaspekte, etwa Attribute, Berechtigungen und Zeitstempel, beibehalten. Mit Azure-Dateifreigaben ist es nicht mehr erforderlich, dass eine Anwendung oder ein Dienst die in der Cloud gespeicherten Dateien und Ordner interpretiert. Sie können darauf nativ über vertraute Protokolle und Clients wie den Windows-Datei-Explorer zugreifen. Azure-Dateifreigaben ermöglichen es Ihnen, allgemeine Dateiserverdaten und Anwendungsdaten in der Cloud speichern. Das Sichern einer Azure-Dateifreigabe ist eine integrierte Funktion und kann mit Azure Backup weiter verbessert werden.

In diesem Artikel werden hauptsächlich die Migrationsschritte behandelt. Weitere Informationen zur Azure-Dateisynchronisierung vor der Migration finden Sie in den folgenden Artikeln:

* [Azure-Dateisynchronisierung – Übersicht](../file-sync/file-sync-planning.md "Übersicht")
* [Azure-Dateisynchronisierung – Bereitstellungsleitfaden](../file-sync/file-sync-deployment-guide.md)

### <a name="storsimple-service-data-encryption-key"></a>StorSimple-Dienstdatenverschlüsselungs-Schlüssel

Als Sie Ihr StorSimple-Gerät erstmalig eingerichtet haben, wurde ein Dienstdatenverschlüsselungs-Schlüssel generiert, und Sie wurden gebeten, den Schlüssel sicher zu speichern. Dieser Schlüssel wird verwendet, um alle Daten im zugeordneten Azure-Speicherkonto zu verschlüsseln, in dem Ihre Dateien vom StorSimple-Gerät gespeichert werden.

Der Dienstdatenverschlüsselungs-Schlüssel ist für eine erfolgreiche Migration erforderlich. Daher ist es nun erforderlich, dass Sie diesen Schlüssel für jedes der Geräte in Ihrem Bestand aus Ihren Aufzeichnungen abrufen.

Wenn Sie die Schlüssel in Ihren Aufzeichnungen nicht finden können, können Sie einen neuen Schlüssel aus der Appliance generieren. Jedes Gerät hat einen eindeutigen Verschlüsselungsschlüssel.

#### <a name="change-the-service-data-encryption-key"></a>Ändern des Verschlüsselungsschlüssels für Dienstdaten

[!INCLUDE [storage-files-migration-generate-key](../../../includes/storage-files-migration-generate-key.md)]

> [!CAUTION]
> Bei der Entscheidung, wie Sie die Verbindung mit Ihrer StorSimple-Appliance herstellen, sollten Sie Folgendes beachten:
>
> * Eine Verbindung über eine HTTPS-Sitzung ist die sicherste und empfohlene Option.
> * Das direkte Verbinden mit der seriellen Konsole des Geräts ist sicher, was für das Verbinden mit der seriellen Konsole über Netzwerkswitches nicht gilt.
> * HTTP-Sitzungsverbindungen sind eine Option, aber sie sind *nicht verschlüsselt*. Sie sind nur zu empfehlen, wenn sie in einem geschlossenen vertrauenswürdigen Netzwerk verwendet werden.

### <a name="known-limitations"></a>Bekannte Einschränkungen

Für StorSimple Data Manager und Azure-Dateifreigaben gelten einige Einschränkungen, die Sie berücksichtigen müssen, bevor Sie mit der Migration beginnen, da sie eine Migration verhindern können:
* Nur NTFS-Volumes von StorSimple-Appliances werden unterstützt. ReFS-Volumes werden nicht unterstützt.
* Alle auf [dynamischen Windows Server-Datenträgern](/troubleshoot/windows-server/backup-and-storage/best-practices-using-dynamic-disks) gespeicherte Volumes werden nicht unterstützt. (vor Windows Server 2012 veraltet)
* Der Dienst funktioniert nicht mit Volumen, die mit BitLocker verschlüsselt sind oder für die [Datendeduplizierung](/windows-server/storage/data-deduplication/understand) aktiviert ist.
* Beschädigte StorSimple-Sicherungen können nicht migriert werden.
* Spezielle Netzwerkoptionen, z. B. Firewalls oder die ausschließliche Kommunikation zwischen privaten Endpunkten, können weder für das Quellspeicherkonto, in dem StorSimple-Sicherungen gespeichert sind, noch für das Zielspeicherkonto aktiviert werden, das die Azure-Dateifreigaben enthält.


### <a name="file-fidelity"></a>Dateigenauigkeit

Wenn keine der in [Bekannte Einschränkungen ](#known-limitations) aufgeführten Einschränkungen eine Migration verhindert, gibt es weiterhin Einschränkungen bei den Elementen, die in Azure-Dateifreigaben gespeichert werden können, die Sie kennen müssen.
Die _Dateigenauigkeit_ bezieht sich auf die Vielzahl von Attributen, Zeitstempeln und Daten, aus denen eine Datei besteht. Bei einer Migration ist die Dateigenauigkeit ein Maß, wie gut die Informationen von der Quelle (StorSimple-Volume) in das Ziel (Azure-Dateifreigabe) übersetzt (migriert) werden können.
[Azure Files unterstützt eine Teilmenge](/rest/api/storageservices/set-file-properties) der [NTFS-Dateieigenschaften](/windows/win32/fileio/file-attribute-constants). Zugriffssteuerungslisten, allgemeine Metadaten und einige Zeitstempel werden migriert. Die folgenden Elemente verhindern keine Migration, verursachen jedoch elementspezifische Probleme bei einer Migration:

* Zeitstempel: Die Dateiänderungszeit wird nicht festgelegt. Sie ist derzeit über das REST-Protokoll schreibgeschützt. Der Zeitstempel für den letzten Zugriff auf eine Datei wird nicht übertragen, da dieses Attribut derzeit nicht für Dateien unterstützt wird, die in einer Azure-Dateifreigabe gespeichert sind.
* [Alternative Datenströme](/openspecs/windows_protocols/ms-fscc/b134f29a-6278-4f3f-904f-5e58a713d2c5) können nicht in Azure-Dateifreigaben gespeichert werden. Dateien, die alternative Datenströme enthalten, werden kopiert, Die alternativen Datenströme werden dabei aber aus der Datei entfernt.
* Symbolische Verknüpfungen, feste Links, Verbindungen und Analysepunkte werden bei einer Migration übersprungen. In den Kopierprotokollen der Migration werden alle übersprungenen Elemente mit einem Grund aufgeführt.
* EFS-verschlüsselte Dateien können nicht kopiert werden. In den Kopierprotokollen wird angezeigt, dass das Element nicht kopiert werden konnte, weil der Zugriff verweigert wurde.
* Beschädigte Dateien werden übersprungen. In den Kopierprotokollen können verschiedene Fehler für die einzelnen Elemente aufgeführt werden, die auf dem StorSimple-Datenträger beschädigt sind, z. B. dass die Anforderung aufgrund eines schwerwiegenden Gerätehardwarefehlers fehlgeschlagen ist, dass die Datei oder das Verzeichnis beschädigt oder nicht lesbar ist, oder dass die Struktur der Zugriffssteuerungsliste (ACL) ungültig ist.
* Einzelne Dateien, die größer als 4 TiB sind, werden übersprungen.
* Die Länge von Dateipfaden ist auf maximal 2048 Zeichen begrenzt. Dateien und Ordner mit längeren Pfaden werden übersprungen.

### <a name="storsimple-volume-backups"></a>StorSimple-Volumesicherungen

StorSimple bietet differenzielle Sicherungen auf Volumeebene. Azure-Dateifreigaben bieten diese Funktionalität ebenfalls in Form der sogenannten Freigabemomentaufnahmen.
Ihre Migrationsaufträge können nur Sicherungen verschieben, keine Daten vom Livevolume. Daher sollte die aktuellste Sicherung immer auf der Liste der bei einer Migration verschobenen Sicherungen stehen.

Entscheiden Sie, ob Sie während der Migration ältere Sicherungen verschieben müssen.
Am besten halten Sie diese Liste so kurz wie möglich, damit Ihre Migrationsaufträge schneller abgeschlossen werden.

Um wichtige Sicherungen zu identifizieren, die migriert werden müssen, erstellen Sie eine Prüfliste mit Ihren Sicherungsrichtlinien. Beispiel:
* Die neueste Sicherung. (Hinweis: Die neueste Sicherung sollte immer in dieser Liste enthalten sein).
* Ein Sicherung pro Monat für 12 Monate.
* Eine Sicherung pro Jahr für drei Jahre. 

Später, wenn Sie Ihre Migrationsaufträge erstellen, können Sie diese Liste verwenden, um die genauen StorSimple-Volumesicherungen zu identifizieren, die migriert werden müssen, um die Anforderungen auf Ihrer Liste zu erfüllen.

> [!CAUTION]
> Die Auswahl von mehr als **50** StorSimple-Volumesicherungen wird nicht unterstützt.
> Ihre Migrationsaufträge können nur Sicherungen verschieben, nie Daten vom Livevolume. Daher entspricht die neueste Sicherung am ehesten den Livedaten und sollte daher immer bei einer Migration in der Liste der zu verschiebenden Sicherungen enthalten sein.

### <a name="map-your-existing-storsimple-volumes-to-azure-file-shares"></a>Zuordnen Ihrer vorhandenen StorSimple-Volumes zu Azure-Dateifreigaben

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

### <a name="number-of-storage-accounts"></a>Anzahl von Speicherkonten

Ihre Migration profitiert wahrscheinlich von einer Bereitstellung mehrerer Speicherkonten, von denen jedes eine kleinere Anzahl von Azure-Dateifreigaben enthält.

Wenn Ihre Dateifreigaben intensiv genutzt werden (Nutzung durch viele Benutzer oder Anwendungen), wird unter Umständen für zwei Azure-Dateifreigaben die Leistungsgrenze Ihres Speicherkontos erreicht. Aus diesem Grund empfiehlt es sich, zu mehreren Speicherkonten zu migrieren, wobei jedes seine eigenen individuellen Dateifreigaben hat, und normalerweise nicht mehr als zwei oder drei Freigaben pro Speicherkonto zu nutzen.

Als bewährte Methode empfiehlt es sich, Speicherkonten mit je einer Dateifreigabe bereitzustellen. Sie können mehrere Azure-Dateifreigaben in einem Speicherkonto zusammenfassen, falls Sie darin über Archivierungsfreigaben verfügen.

Diese Überlegungen gelten eher für [direkten Cloudzugriff](#direct-share-access-vs-azure-file-sync) (über eine Azure-VM oder einen Azure-Dienst) als für die Azure-Dateisynchronisierung. Wenn Sie Freigaben ausschließlich für die Azure-Dateisynchronisierung verwenden möchten, können Sie mehrere Freigaben in einem einzelnen Azure-Speicherkonto gruppieren. In Zukunft möchten Sie vielleicht eine App per Lift & Shift in die Cloud migrieren, die dann direkt auf eine Dateifreigabe zugreift. Dieses Szenario würde von höheren IOPS und einem höheren Durchsatz profitieren. Oder Sie könnten mit der Nutzung eines Diensts in Azure beginnen, der ebenfalls von höheren IOPS und einem höheren Durchsatz profitieren würde.

Nachdem Sie eine Liste mit Ihren Freigaben erstellt haben, ordnen Sie jede Freigabe dem Speicherkonto zu, unter dem diese sich befinden soll.

> [!IMPORTANT]
> Entscheiden Sie sich für eine Azure-Region, und stellen Sie sicher, dass jedes Speicherkonto und jede Ressource der Azure-Dateisynchronisierung mit der von Ihnen ausgewählten Region übereinstimmt.
> Konfigurieren Sie jetzt keine Netzwerk- und Firewalleinstellungen für die Speicherkonten. Die Durchführung dieser Konfigurationen zu diesem Zeitpunkt würde eine Migration unmöglich machen. Konfigurieren Sie diese Azure-Speichereinstellungen, nachdem die Migration abgeschlossen ist.

### <a name="storage-account-settings"></a>Speicherkontoeinstellungen

Es gibt viele Konfigurationen, die Sie für ein Speicherkonto vornehmen können. Die folgende Prüfliste sollte verwendet werden, um Ihre Speicherkontokonfigurationen zu bestätigen. Sie können z. B. die Netzwerkkonfiguration nach Abschluss der Migration ändern. 

> [!div class="checklist"]
> * Freigabe großer Dateien: Aktiviert - Freigaben großer Dateien verbessern die Leistung und ermöglichen es Ihnen, bis zu 100 TiB in einer Freigabe zu speichern. Diese Einstellung gilt für Zielspeicherkonten mit Azure-Dateifreigaben.
> * Firewall und virtuelle Netzwerke: Deaktiviert - Konfigurieren Sie keine IP-Einschränkungen, oder beschränken Sie den Speicherkontozugriff auf ein bestimmtes VNET. Der öffentliche Endpunkt des Speicherkontos wird während der Migration verwendet. Alle IP-Adressen von Azure-VMs müssen erlaubt sein. Es ist am besten, alle Firewallregeln für das Speicherkonto nach der Migration zu konfigurieren. Konfigurieren Sie sowohl Ihre Quell- als auch Zielspeicherkonten auf diese Weise.
> * Private Endpunkte: Unterstützt - Sie können private Endpunkte aktivieren, aber der öffentliche Endpunkt wird für die Migration verwendet und muss verfügbar bleiben. Dieser Aspekt gilt sowohl für Quell- als auch für Zielspeicherkonten.

### <a name="phase-1-summary"></a>Zusammenfassung von Phase 1

Am Ende der Phase 1:

* Sie haben einen guten Überblick über Ihre StorSimple-Geräte und -Volumes.
* Der Data Manager-Dienst hat Zugriff auf Ihre StorSimple-Volumes in der Cloud, weil Sie Ihren Dienstdatenverschlüsselungs-Schlüssel für jedes StorSimple-Gerät abgerufen haben.
* Sie haben einen Plan, welche Volumes und Sicherungen (falls es welche gibt, die über die letzte Sicherung hinausgehen) migriert werden müssen.
* Sie wissen, wie Sie Ihre Volumes der entsprechenden Anzahl von Azure-Dateifreigaben und Speicherkonten zuordnen können.

## <a name="phase-2-deploy-azure-storage-and-migration-resources"></a>Phase 2: Bereitstellen von Azure-Speicher- und -Migrationsressourcen

In diesem Abschnitt werden Aspekte zum Bereitstellen der verschiedenen Ressourcentypen erläutert, die in Azure benötigt werden. In einigen Ressourcen befinden sich Ihre Daten nach der Migration, und einige werden ausschließlich für die Migration benötigt. Starten Sie die Bereitstellung von Ressourcen erst, nachdem Sie Ihren Bereitstellungsplan fertiggestellt haben. Es ist schwierig bzw. in einigen Fällen auch unmöglich, bestimmte Aspekte Ihrer Azure-Ressourcen zu ändern, nachdem sie bereitgestellt wurden.

### <a name="deploy-storage-accounts"></a>Bereitstellen von Speicherkonten

Sie müssen wahrscheinlich mehrere Azure-Speicherkonten bereitstellen. Jedes dieser Konten enthält eine kleinere Anzahl von Azure-Dateifreigaben, wie dies in Ihrem Bereitstellungsplan vorgesehen ist, den Sie im vorherigen Abschnitt dieses Artikels fertiggestellt haben. Wechseln Sie zum Azure-Portal, um [Ihre geplanten Speicherkonten bereitzustellen](../common/storage-account-create.md#create-a-storage-account). Es bietet sich an, die unten angegebenen grundlegenden Einstellungen für jedes neue Speicherkonto zu berücksichtigen.

> [!IMPORTANT]
> Konfigurieren Sie jetzt keine Netzwerk- und Firewalleinstellungen für Ihre Speicherkonten. Die Durchführung dieser Konfigurationen zu diesem Zeitpunkt würde eine Migration unmöglich machen. Konfigurieren Sie diese Azure-Speichereinstellungen, nachdem die Migration abgeschlossen ist.

#### <a name="subscription"></a>Abonnement

Sie können das Abonnement verwenden, das Sie für Ihre StorSimple-Bereitstellung verwendet haben. Sie können aber auch ein anderes Abonnement verwenden. Die einzige Einschränkung besteht darin, dass sich Ihr Abonnement auf demselben Azure Active Directory-Mandanten wie das StorSimple-Abonnement befinden muss. Verschieben Sie das StorSimple-Abonnement ggf. auf den entsprechenden Mandanten, bevor Sie eine Migration starten. Sie können nur das gesamte Abonnement verschieben. Einzelne StorSimple-Ressourcen können nicht in einen anderen Mandanten oder ein anderes Abonnement verschoben werden.

#### <a name="resource-group"></a>Ressourcengruppe

Ressourcengruppen unterstützen beim Strukturieren von Ressourcen und administrativen Verwaltungsberechtigungen. Erfahren Sie mehr über [Ressourcengruppen in Azure](../../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group).

#### <a name="storage-account-name"></a>Speicherkontoname

Der Name Ihres Speicherkontos wird Bestandteil einer URL, und für ihn gelten bestimmte Zeichenbeschränkungen. In Ihrer Namenskonvention sollten Sie berücksichtigen, dass Speicherkontonamen weltweit eindeutig sein müssen, nur Kleinbuchstaben und Ziffern enthalten können, zwischen 3 und 24 Zeichen lang sein müssen und keine Sonderzeichen wie Bindestriche oder Unterstriche aufweisen dürfen. Weitere Informationen finden Sie unter [Benennungsregeln für Azure-Speicherressourcen](../../azure-resource-manager/management/resource-name-rules.md#microsoftstorage).

#### <a name="location"></a>Standort

Der Standort bzw. die Azure-Region eines Speicherkontos ist äußerst wichtig. Wenn Sie Azure-Dateisynchronisierung verwenden, müssen sich Ihre Speicherkonten in derselben Region befinden wie die Speichersynchronisierungsdienst-Ressource. Die Azure-Region, die Sie auswählen, sollte sich in der Nähe bzw. am Mittelpunkt Ihrer lokalen Server und Benutzer befinden. Nachdem Ihre Ressource bereitgestellt wurde, können Sie die zugehörige Region nicht mehr ändern.

Sie können eine Region auswählen, die nicht mit der identisch ist, in der sich Ihre StorSimple-Daten (Speicherkonto) aktuell befinden.

> [!IMPORTANT]
> Wenn Sie eine andere Region als den aktuellen Standort Ihres StorSimple-Speicherkontos auswählen, werden [Gebühren für ausgehenden Datenverkehr](https://azure.microsoft.com/pricing/details/bandwidth) fällig. Die Daten werden aus der StorSimple-Region in die Region Ihres neuen Speicherkontos gesendet. Wenn Sie innerhalb derselben Azure-Region bleiben, fallen keine Bandbreitengebühren an.

#### <a name="performance"></a>Leistung

Sie haben die Möglichkeit, Storage Premium (SSD) für Azure-Dateifreigaben oder Standardspeicher auszuwählen. Standardspeicher umfasst [verschiedenen Ebenen für eine Dateifreigabe](storage-how-to-create-file-share.md#change-the-tier-of-an-azure-file-share). Standardspeicher ist die richtige Option für die meisten Kunden, die von StorSimple migrieren.

Sie sind immer noch nicht sicher?

* Wählen Sie Storage Premium aus, wenn Sie die [Leistung einer Premium-Azure-Dateifreigabe](understanding-billing.md#provisioned-model) benötigen.
* Wählen Sie Standardspeicher für allgemeine Dateiserverworkloads aus, einschließlich heißer Daten und Archivdaten. Wählen Sie Standardspeicher auch aus, wenn Azure-Dateisynchronisierung die einzige Workload auf der Freigabe in der Cloud ist.
* Wählen Sie für Premium-Dateifreigaben im Assistenten zum Erstellen von Speicherkontos *Dateifreigaben* aus.

#### <a name="replication"></a>Replikation

Es sind mehrere Replikationseinstellungen verfügbar. Erfahren Sie mehr über die verschiedenen Replikationstypen.

Wählen Sie eine dieser beiden Optionen aus:

* *Lokal redundanter Speicher (LRS)* :
* *Zonenredundanter Speicher (ZRS)* : Ist nicht in allen Azure-Regionen verfügbar.

> [!NOTE]
> Nur die Redundanztypen LRS und ZRS sind mit den großen Azure-Dateifreigaben mit einer Kapazität von 100 TiB kompatibel.

Alle Varianten des georedundanten Speichers (GRS) werden derzeit nicht unterstützt. Sie können den Redundanztyp zu einem späteren Zeitpunkt ändern und zu GRS wechseln, sobald dieser Typ in Azure unterstützt wird.

#### <a name="enable-100-tib-capacity-file-shares"></a>Aktivieren von Dateifreigaben mit 100 TiB Kapazität

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png" alt-text="Registerkarte „Erweitert“ im Azure-Portal, auf der ein Speicherkonto erstellt werden kann":::
    :::column-end:::
    :::column:::
        Im Abschnitt **Erweitert** des Assistenten für neue Speicherkonten im Azure-Portal können Sie Unterstützung für **Große Dateifreigaben** in diesem Speicherkonto aktivieren. Falls Ihnen diese Option nicht zur Verfügung steht, haben Sie wahrscheinlich den falschen Redundanztyp ausgewählt. Sie dürfen nur LRS oder ZRS auswählen, damit diese Option verfügbar wird.
    :::column-end:::
:::row-end:::

Die Entscheidung für die großen Dateifreigaben mit 100 TiB Kapazität hat mehrere Vorteile:

* Die Leistung wird im Vergleich zu den kleineren Dateifreigaben mit 5 TiB Kapazität deutlich erhöht (z. B. IOPS-Verzehnfachung).
* Ihre Migration wird erheblich schneller abgeschlossen.
* Sie stellen sicher, dass eine Dateifreigabe über genügend Kapazität verfügt, um alle Daten aufzunehmen, die Sie dorthin migrieren werden, einschließlich der für differenzielle Sicherungen erforderlichen Speicherkapazität.
* Zukünftige Vergrößerungen sind abgedeckt.

> [!IMPORTANT]
> Wenden Sie vor oder während der Migration kein spezielles Netzwerk auf Ihr Speicherkonto an. Auf den öffentlichen Endpunkt muss für Quell- und Zielspeicherkonten zugegriffen werden können. Es wird keine Beschränkung auf bestimmte IP-Adressbereiche oder VNETs unterstützt. Sie können die Netzwerkkonfigurationen des Speicherkontos nach der Migration ändern.

### <a name="azure-file-shares"></a>Azure-Dateifreigaben

Nachdem Sie Ihre Speicherkonten erstellt haben, navigieren Sie zum Abschnitt **Dateifreigabe** des Speicherkontos und stellen die entsprechende Anzahl von Azure-Dateifreigaben gemäß Ihrem Migrationsplan aus Phase 1 bereit. Es bietet sich an, die unten angegebenen grundlegenden Einstellungen für Ihre neuen Dateifreigaben in Azure zu berücksichtigen.

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-share.png" alt-text="Azure-Portal-Screenshot des neuen Dialogfelds „Neue Dateifreigabe“":::
    :::column-end:::
    :::column:::
        </br>**Name**</br>Kleinbuchstaben, Zahlen und Bindestriche werden unterstützt.</br></br>**Kontingent**</br>„Kontingent“ ist hier vergleichbar mit einer harten SMB-Kontingentgrenze auf einer Windows Server-Instanz. Sie sollten in diesem Fall nach Möglichkeit kein Kontingent festlegen, da die Migration und andere Dienste fehlschlagen, wenn das Kontingent erreicht wird.</br></br>**Tarife**</br>Wählen Sie für Ihre neue Dateifreigabe die Option **Transaktionsoptimiert** aus. Während der Migration werden viele Transaktionen durchgeführt. Es ist kostengünstiger, Ihren Tarif später so zu ändern, wie dies für Ihre Workload am besten geeignet ist.
    :::column-end:::
:::row-end:::

### <a name="storsimple-data-manager"></a>StorSimple Data Manager

Die Azure-Ressource, die der Ihre Migrationsaufträge dann enthalten sind, wird als **StorSimple Data Manager** bezeichnet. Wählen Sie die Option **Neue Ressource** aus, und suchen Sie danach. Klicken Sie anschließend auf **Erstellen**.

Diese temporäre Ressource wird für die Orchestrierung verwendet. Sie heben die Bereitstellung dafür auf, nachdem Ihre Migration abgeschlossen ist. Sie sollte in derselben Umgebung (Abonnement, Ressourcengruppe und Region) wie Ihr StorSimple-Speicherkonto bereitgestellt werden.

### <a name="azure-file-sync"></a>Azure-Dateisynchronisierung

Bei der Azure-Dateisynchronisierung können Sie die lokale Zwischenspeicherung für die Dateien hinzufügen, auf die am häufigsten zugegriffen wird. Ähnlich wie die Zwischenspeicherungsfunktionen von StorSimple bietet die Cloudtieringfunktionalität der Azure-Dateisynchronisierung lokale Zugriffslatenz in Kombination mit verbesserter Kontrolle der verfügbaren Cachekapazität bei der Synchronisierung der Windows Server-Instanz und von mehreren Standorten. Wenn Sie einen lokalen Cache benötigen, bereiten Sie in Ihrem lokalen Netzwerk eine Windows Server-VM (physische Server und Failovercluster werden ebenfalls unterstützt) mit genügend Kapazität für direkt angeschlossenen Speicher vor.

> [!IMPORTANT]
> Richten Sie die Azure-Dateisynchronisierung noch nicht ein. Es ist ratsam, die Azure-Dateisynchronisierung einzurichten, nachdem die Migration Ihrer Freigabe beendet wurde. Die Bereitstellung der Azure-Dateisynchronisierung sollte nicht vor Beginn von Phase 4 einer Migration starten.

### <a name="phase-2-summary"></a>Zusammenfassung von Phase 2

Am Ende von Phase 2 haben Sie Ihre Speicherkonten und alle zugehörigen Azure-Dateifreigaben bereitgestellt. Darüber hinaus verfügen Sie über eine StorSimple Data Manager-Ressource. Sie verwenden diese Ressource in Phase 3, wenn Sie Ihre Migrationsaufträge konfigurieren.

## <a name="phase-3-create-and-run-a-migration-job"></a>Phase 3: Erstellen und Ausführen eines Migrationsauftrags

In diesem Abschnitt ist beschrieben, wie Sie einen Migrationsauftrag einrichten und sorgfältig die Verzeichnisse auf einem StorSimple-Volume zuordnen, die in die von Ihnen ausgewählte Azure-Zieldateifreigabe kopiert werden sollen. Navigieren Sie zunächst zu Ihrem StorSimple Data Manager, suchen Sie im Menü nach der Option **Auftragsdefinitionen**, und wählen Sie **+ Auftragsdefinition** aus. Der richtige Zielspeichertyp ist die Standardeinstellung: **Azure-Dateifreigabe**.

![Migrationsauftragstypen für die StorSimple 8000-Serie](media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job-type.png "Screenshot des Azure-Portal-Abschnitts „Auftragsdefinitionen“ mit dem neuen Dialogfeld „Auftragsdefinitionen“, in dem nach dem Typ des Auftrags gefragt wird: Kopieren in eine Dateifreigabe oder einen Blobcontainer.")

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-new-job.png" alt-text="Screenshot des Formulars zum Erstellen eines neuen Auftrags für einen Migrationsauftrag.":::
    :::column-end:::
    :::column:::
        **Auftragsdefinitionsname**</br>Mit diesem Namen sollten die zu verschiebenden Dateien angegeben werden. Es hat sich bewährt, einen Namen zu vergeben, der dem Ihrer Azure-Dateifreigabe ähnelt. </br></br>**Speicherort, in dem der Auftrag ausgeführt wird**</br>Sie müssen hierbei die Region auswählen, in der sich auch Ihr StorSimple-Speicherkonto befindet. Falls diese Region nicht verfügbar ist, wählen Sie eine Region in deren Nähe aus. </br></br><h3>`Source`</h3>**Quellabonnement**</br>Wählen Sie das Abonnement aus, unter dem Sie Ihre StorSimple-Geräte-Manager-Ressource speichern. </br></br>**StorSimple-Ressource**</br>Wählen Sie Ihren StorSimple-Geräte-Manager aus, bei dem Ihre Appliance registriert ist. </br></br>**Verschlüsselungsschlüssel für Dienstdaten**</br>Lesen Sie den [vorherigen Abschnitt dieses Artikels](#storsimple-service-data-encryption-key), falls Sie den Schlüssel nicht in Ihren Aufzeichnungen finden können. </br></br>**Device**</br>Wählen Sie das StorSimple-Gerät aus, auf dem sich das Volume befindet, zu dem Sie migrieren möchten. </br></br>**Umfang**</br>Wählen Sie das Quellvolume aus. Später können Sie entscheiden, ob das gesamte Volume oder Unterverzeichnisse in die Azure-Zieldateifreigabe migriert werden sollen.</br></br> **Volumesicherungen**</br>Sie können *Volumesicherungen auswählen* auswählen, um bestimmte Sicherungen auszuwählen, die als Teil dieses Auftrags verschoben werden sollen. Ein nachfolgender, [dedizierter Abschnitt in diesem Artikel](#selecting-volume-backups-to-migrate) behandelt den Prozess im Detail.</br></br><h3>Ziel</h3>Wählen Sie das Abonnement, das Speicherkonto und die Azure-Dateifreigabe als Ziel dieses Migrationsauftrags aus.</br></br><h3>Verzeichniszuordnung</h3>[Ein dedizierter Abschnitt in diesem Artikel](#directory-mapping) behandelt alle relevanten Details.
    :::column-end:::
:::row-end:::

### <a name="selecting-volume-backups-to-migrate"></a>Auswählen der zu migrierenden Volumesicherungen

Bei der Auswahl der zu migrierenden Sicherungen gibt es wichtige Aspekte:

- Ihre Migrationsaufträge können nur Sicherungen, aber keine Daten von einem Livevolume übertragen. Die neueste Sicherung entspricht also am ehesten den Livedaten und sollte immer auf der Liste der bei einer Migration zu verschiebenden Sicherungen stehen. Wenn Sie das Auswahldialogfeld für Sicherungen öffnen, ist standardmäßig diese Sicherung ausgewählt.
- Stellen Sie sicher, dass Ihre letzte Sicherung aktuell ist, um die Abweichung von der Livefreigabe so klein wie möglich zu halten. Es kann sinnvoll sein, eine weitere Volumesicherung manuell auszulösen und abzuschließen, bevor Sie einen Migrationsauftrag erstellen. Eine geringe Abweichung von der Livefreigabe verbessert die Erfahrung bei der Migration. Wenn diese Abweichung (Delta) Null sein kann (d.h. es sind keine Änderungen mehr am StorSimple-Volume aufgetreten, nachdem die neueste Sicherung in Ihrer Liste erstellt wurde), wird Phase 5: „Benutzerübernahme“ drastisch vereinfacht und beschleunigt.
- Sicherungen müssen **von der ältesten zur neuesten** zurück in die Azure-Dateifreigabe übertragen werden. Eine ältere Sicherung kann nicht in die Liste der Sicherungen auf der Azure-Dateifreigabe „einsortiert“ werden, nachdem ein Migrationsauftrag ausgeführt wurde. Daher müssen Sie sicherstellen, dass Ihre Liste der Sicherungen vollständig ist, *bevor* Sie einen Auftrag erstellen. 
- Diese Liste der Sicherungen in einem Auftrag kann nicht mehr geändert werden, sobald der Auftrag erstellt wurde, selbst wenn der Auftrag nie ausgeführt wurde.
- Um Sicherungen auswählen zu können, muss das zu migrierende StorSimple-Volume online sein.

:::row:::
    :::column:::        
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups.png" alt-text="Ein Screenshot des Formulars zur Erstellung eines neuen Auftrags mit dem Teil, in dem StorSimple-Sicherungen für die Migration ausgewählt werden." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-expanded.png":::
    :::column-end:::
    :::column:::
        Um Sicherungen Ihres StorSimple-Volumes für Ihren Migrationsauftrag auszuwählen, wählen Sie die Option *Volumesicherungen auswählen* im Formular zur Auftragserstellung aus.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png" alt-text="Eine Abbildung, die zeigt, dass in der oberen Hälfte des Blatts zur Auswahl von Sicherungen alle verfügbaren Sicherungen aufgelistet sind. Eine ausgewählte Sicherung wird in dieser Liste abgeblendet und einer zweiten Liste in der unteren Hälfte des Blatts hinzugefügt. Dort kann sie auch wieder gelöscht werden." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-annotated.png":::
    :::column-end:::
    :::column:::
        Wenn das Blatt für die Auswahl der Sicherung geöffnet wird, ist es in zwei Listen unterteilt. In der ersten Liste werden alle verfügbaren Sicherungen angezeigt. Sie können das Resultset erweitern und einschränken, indem Sie nach einem bestimmten Zeitbereich filtern. (siehe nächster Abschnitt) </br></br>Eine ausgewählte Sicherung wird abgeblendet angezeigt und einer zweiten Liste in der unteren Hälfte des Blatts hinzugefügt. In der zweiten Liste werden alle für die Migration ausgewählten Sicherungen angezeigt. Eine versehentlich ausgewählte Sicherung kann auch wieder entfernt werden.
        > [!CAUTION]
        > Sie müssen **alle** Sicherungen auswählen, die Sie migrieren möchten. Sie können ältere Sicherungen nicht nachträglich hinzufügen. Sie können den Auftrag nicht mehr ändern, um Ihre Auswahl zu ändern, sobald der Auftrag erstellt ist.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time.png" alt-text="Ein Screenshot, der die Auswahl eines Zeitbereichs des Blatts für die Sicherungsauswahl zeigt." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-select-backups-time-expanded.png":::
    :::column-end:::
    :::column:::
        Standardmäßig wird die Liste so gefiltert, dass die StorSimple-Volumesicherungen innerhalb der letzten sieben Tage angezeigt werden. Die letzte Sicherung ist standardmäßig auch dann ausgewählt, wenn sie nicht in den letzten sieben Tagen erfolgt ist. Verwenden Sie für ältere Sicherungen den Zeitbereichsfilter oben im Blatt. Sie können entweder einen vorhandenen Filter auswählen oder einen benutzerdefinierten Zeitbereich festlegen, um nur die Sicherungen zu filtern, die in diesem Zeitraum erstellt wurden.
    :::column-end:::
:::row-end:::

> [!CAUTION]
> Die Auswahl von mehr als 50 StorSimple-Volumesicherungen wird nicht unterstützt. Bei Aufträgen mit einer großen Anzahl von Sicherungen können Fehler auftreten.

### <a name="directory-mapping"></a>Verzeichniszuordnung

Die Verzeichniszuordnung ist für Ihren Migrationsauftrag optional. Wenn Sie diesen Abschnitt leer lassen, werden *alle* Dateien und Ordner im Stammverzeichnis Ihres StorSimple-Volumes in das Stammverzeichnis Ihrer Azure-Zieldateifreigabe verschoben. In den meisten Fällen ist das Speichern der Inhalte eines gesamten Volumes auf einer Azure-Dateifreigabe nicht der beste Ansatz. Häufig ist es besser, den Inhalt eines Volumes auf mehrere Dateifreigaben in Azure aufzuteilen. Falls Sie noch keinen Plan erstellt haben, helfen Ihnen die Informationen unter [Zuordnen Ihrer vorhandenen StorSimple-Volumes zu Azure-Dateifreigaben](#map-your-existing-storsimple-volumes-to-azure-file-shares) weiter.

Im Rahmen Ihres Migrationsplans haben Sie ggf. die Entscheidung getroffen, dass die Ordner auf einem StorSimple-Volume auf mehrere Azure-Dateifreigaben aufgeteilt werden müssen. Ist dies der Fall, können Sie die Aufteilung wie folgt vornehmen:

1. Definieren mehrerer Aufträge zum Migrieren der Ordner auf einem Volume. Jeder verfügt über dieselbe StorSimple-Volumequelle, aber eine andere Azure-Dateifreigabe als Ziel.
1. Geben Sie genau an, welche Ordner aus dem StorSimple-Volume in die angegebene Dateifreigabe migriert werden müssen. Verwenden Sie hierfür den Abschnitt **Verzeichniszuordnung** des Auftragserstellungsformulars, und halten Sie sich an die spezifische [Zuordnungssemantik](#semantic-elements).

> [!IMPORTANT]
> Die Pfade und Zuordnungsausdrücke in diesem Formular können nicht überprüft werden, wenn das Formular übermittelt wird. Sind Zuordnungen falsch angegeben, kann ein Auftrag entweder vollständig fehlschlagen oder zu einem unerwünschten Ergebnis führen. In diesem Fall empfiehlt es sich in der Regel, die Azure-Dateifreigabe zu löschen, sie erneut zu erstellen und dann die Zuordnungsanweisungen in einem neuen Migrationsauftrag für die Freigabe zu korrigieren. Durch Ausführen eines neuen Auftrags mit korrigierten Zuordnungsanweisungen können nicht berücksichtigte Ordner bestimmt und auf die vorhandene Freigabe übertragen werden. Allerdings können so nur Ordner erfasst werden, die aufgrund von falsch geschriebenen Pfaden nicht berücksichtigt wurden.

#### <a name="semantic-elements"></a>Semantikelemente

Eine Zuordnung wird von links nach rechts formuliert: [\Quellpfad] \> [\Zielpfad].

|Semantisches Zeichen          | Bedeutung  |
|:---------------------------|:---------|
| **\\**                     | Kennzeichen für Stammebene.       |
| **\>**                     | Operator für [Quelle] und [Ziel] der Zuordnung.     |
|**\|** oder EINGABETASTE (neue Zeile) | Trennzeichen von zwei Anweisungen für die Ordnerzuordnung. </br>Alternativ können Sie auf dieses Zeichen verzichten und die **EINGABETASTE** drücken, um den nächsten Zuordnungsausdruck in seiner eigenen Zeile zu erhalten.        |

### <a name="examples"></a>Beispiele
Verschiebt den Inhalt des Ordners *User Data* in das Stammverzeichnis der Zieldateifreigabe:
``` console
\User data > \
```
Verschiebt den gesamten Volumeinhalt in einen neuen Pfad in der Zieldateifreigabe:
``` console
\ > \Apps\HR tracker
```
Verschiebt den Inhalt des Quellordners unter einen neuen Pfad auf der Zieldateifreigabe:
``` console
\HR resumes-Backup > \Backups\HR\resumes
```
Sortiert mehrere Quellspeicherorte in einer neuen Verzeichnisstruktur:
``` console
\HR\Candidate Tracker\v1.0 > \Apps\Candidate tracker
\HR\Candidates\Resumes > \HR\Candidates\New
\Archive\HR\Old Resumes > \HR\Candidates\Archived
```

### <a name="semantic-rules"></a>Semantikregeln

* Geben Sie Ordnerpfade immer relativ zur Stammebene an.
* Beginnen Sie jeden Ordnerpfad mit dem folgenden Kennzeichen für die Stammebene: „\\“.
* Fügen Sie keine Laufwerkbuchstaben ein.
* Wenn Sie mehrere Pfade angeben, dürfen sich die Quell- und Zielpfade nicht überlappen:</br>
   Beispiel für eine ungültige Quellpfadüberlappung:</br>
    *\\Ordner\1 > \\Ordner*</br>
    *\\Ordner\\1\\2 > \\Ordner2*</br>
   Beispiel für eine ungültige Zielpfadüberlappung:</br>
   *\\Ordner > \\*</br>
   *\\2 > \\*</br>
* Nicht vorhandene Quellordner werden ignoriert.
* Ordnerstrukturen, die auf dem Ziel nicht vorhanden sind, werden erstellt.
* Wie bei Windows wird die Groß-/Kleinschreibung für Ordnernamen nicht beachtet und beibehalten.

> [!NOTE]
> Der Inhalt des Ordners *\System Volume Information* und der Inhalt von *$Recycle.Bin* auf Ihrem StorSimple-Volume werden vom Migrationsauftrag nicht kopiert.

### <a name="run-a-migration-job"></a>Ausführen eines Migrationsauftrags

Ihre Migrationsaufträge sind unter *Auftragsdefinitionen* in der Data Manager-Ressource aufgeführt, die Sie für eine Ressourcengruppe bereitgestellt haben.
Wählen Sie in der Liste der Auftragsdefinitionen den Auftrag aus, den Sie ausführen möchten.

Im geöffneten Auftragsblatt werden der aktuelle Status Ihres Auftrags und eine Liste der ausgewählten Sicherungen angezeigt. Die Liste der Sicherungen ist von der ältesten zur neuesten sortiert. Die Sicherungen werden in dieser Reihenfolge zur Azure-Dateifreigabe migriert.  

:::row:::
    :::column:::        
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-never-ran-focused.png" alt-text="Screenshot des Migrationsauftragsblatt mit einer Hervorhebung des Befehls zum Starten des Auftrags. Außerdem werden die ausgewählten Sicherungen angezeigt, deren Migration geplant ist." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-never-ran.png":::
    :::column-end:::
    :::column:::
        Zunächst weist der Migrationsauftrag den Status **Nie ausgeführt** auf. </br>Wenn Sie bereit sind, können Sie diesen Migrationsauftrag starten. (Wählen Sie das Image für eine Version mit höherer Auflösung aus.) </br> Wenn eine Sicherung erfolgreich migriert wurde, wird automatisch eine Momentaufnahme der Azure-Dateifreigabe erstellt. Das ursprüngliche Sicherungsdatum der StorSimple-Sicherung wird zum Abschnitt *Kommentare* der Momentaufnahme der Azure-Dateifreigabe hinzugefügt. Über dieses Feld können Sie feststellen, wann die Daten im Vergleich zum Zeitpunkt der Erstellung der Momentaufnahme der Dateifreigabe ursprünglich gesichert wurden.
        </br></br>
        > [!CAUTION]
        > Sicherungen müssen von der ältesten zur neuesten verarbeitet werden. Nachdem ein Migrationsauftrag erstellt wurde, können Sie die Liste der ausgewählten StorSimple-Volumesicherungen nicht mehr ändern. Starten Sie den Auftrag nicht, wenn die Liste der Sicherungen nicht richtig oder unvollständig ist. Löschen Sie den Auftrag, und erstellen Sie einen neuen Auftrag mit den richtigen Sicherungen.
    :::column-end:::
:::row-end:::

### <a name="per-item-errors"></a>Elementspezifische Fehler

Die Migrationsaufträge enthalten in der Liste der Sicherungen zwei Spalten, in denen alle Probleme aufgeführt sind, die während des Kopiervorgang u. U. aufgetreten sind:

* Kopierfehler </br>In dieser Spalte werden die Dateien oder Ordner aufgelistet, die kopiert werden sollten, aber nicht kopiert wurden. Diese Fehler können oftmals behoben werden. Überprüfen Sie die Kopierprotokolle, wenn für eine Sicherung elementspezifische Probleme in dieser Spalte aufgelistet werden. Wenn diese Dateien migriert werden müssen, wählen Sie **Sicherung wiederholen** aus. Diese Option wird verfügbar, sobald die Verarbeitung der Sicherung abgeschlossen wurde. Im Abschnitt [Verwalten eines Migrationsauftrags](#manage-a-migration-job) werden die jeweiligen Optionen ausführlicher erläutert.
* Nicht unterstützte Dateien </br>In dieser Spalte werden Dateien oder Ordner aufgelistet, die nicht migriert werden können. Azure Storage weist Einschränkungen bei Dateinamen, Pfadlängen und Dateitypen auf, die derzeit oder logischerweise nicht in einer Azure-Dateifreigabe gespeichert werden können. Ein Migrationsauftrag wird für derartige Fehler nicht angehalten. Das Ergebnis ändert sich durch Wiederholung der Migration der Sicherung nicht. Überprüfen Sie die Kopierprotokolle, wenn für eine Sicherung elementspezifische Probleme in dieser Spalte aufgelistet werden, und nehmen Sie das Problem zur Kenntnis. Wenn solche Probleme in Ihrer letzten Sicherung auftreten und Sie im Kopierprotokoll festgestellt haben, dass der Fehler auf einen Dateinamen, eine Pfadlänge oder ein anderes Problem zurückzuführen ist, auf das Sie Einfluss haben, können Sie das Problem im StorSImple-Livevolume beheben, eine StorSimple-Volumesicherung erstellen und einen neuen Migrationsauftrag erstellen, der nur diese Sicherung umfasst. Anschließend migrieren Sie diesen korrigierten Namespace. Die Sicherung wird dann zur neuesten Version/Liveversion der Azure-Dateifreigabe. Dieser manuelle Prozess ist zeitaufwändig. Überprüfen Sie die Kopierprotokolle sorgfältig, und überlegen Sie, ob sich die Migration lohnt.

Bei diesen Kopierprotokollen handelt es sich um *\*.csv*-Dateien, in denen Namespace-Elemente aufgelistet sind, die erfolgreich waren, sowie Elemente, die nicht kopiert werden konnten. Die Fehler werden weiter in die zuvor beschriebenen Kategorien unterteilt.
Am Speicherort der Protokolldatei finden Sie Protokolle für fehlerhafte Dateien, indem Sie nach „failed“ suchen. Das Ergebnis sollten Protokolle für Dateien sein, die nicht kopiert werden konnten. Sortieren Sie diese Protokolle nach Größe. Möglicherweise werden zusätzliche Protokolle mit einer Größe von 17 Bytes erstellt. Diese sind leer und können ignoriert werden. Durch eine Sortierung können Sie sich ganz auf die Protokolle mit Inhalt konzentrieren.

Das gleiche Verfahren gilt für Protokolldateien, die erfolgreiche Kopien aufzeichnen.

### <a name="manage-a-migration-job"></a>Verwalten eines Migrationsauftrags

Migrationsaufträge weisen die folgenden Zustände auf:
* **Nie ausgeführt** </br>Ein neuer Auftrag, der definiert, aber noch nie ausgeführt wurde.
* **Wartet** </br>Ein Auftrag in diesem Zustand wartet auf die Bereitstellung von Ressourcen im Migrationsdienst. Er wechselt automatisch in einen anderen Zustand, wenn er ausgeführt werden kann.
* **Fehler** </br>Bei einem fehlerhaften Auftrag ist ein schwerwiegender Fehler aufgetreten, der die Verarbeitung weiterer Sicherungen verhindert. Ein Auftrag sollte nicht in diesen Zustand wechseln. In diesem Fall ist eine Supportanfrage die beste Vorgehensweise.
* **Abgebrochen** / **Wird abgebrochen**</br>Entweder der ganze Migrationsauftrag oder einzelne Sicherungen innerhalb des Auftrags können abgebrochen werden. Abgebrochene Sicherungen werden nicht verarbeitet. Ein abgebrochener Migrationsauftrag beendet die Verarbeitung weiterer Sicherungen. Gehen Sie davon aus, dass das Abbrechen eines Auftrags sehr lange dauern wird. Dies hindert Sie nicht daran, einen neuen Auftrag zu erstellen. Die beste Vorgehensweise besteht darin, solange zu warten, bis ein Auftrag endgültig den Zustand **Abgebrochen** aufweist. Sie können fehlgeschlagene/abgebrochene Aufträge entweder ignorieren oder zu einem späteren Zeitpunkt löschen. Sie müssen keine Aufträge löschen, bevor Sie die Data Manager-Ressource am Ende ihrer StorSimple-Migration löschen können.


:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-running-focused.png" alt-text="Screenshot des Migrationsauftragsblatts mit einem großen Statussymbol im oberen Bereich im Zustand „Wird ausgeführt“." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-running.png":::
    :::column-end:::
    :::column:::
        **Wird ausgeführt** </br></br>Ein ausgeführter Auftrag verarbeitet derzeit eine Sicherung. Anhand der Tabelle in der unteren Hälfte des Blatts können Sie feststellen, welche Sicherung gerade verarbeitet wird und welche bereits migriert wurden. </br>Bereits migrierte Sicherungen verfügen über eine Spalte mit einem Link zu einem Kopierprotokoll. Wenn Fehler für eine Sicherung gemeldet werden, sollten Sie das Kopierprotokoll überprüfen.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-paused-focused.png" alt-text="Screenshot des Migrationsauftragsblatts mit einem großen Statussymbol im oberen Bereich im Zustand „Angehalten“." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-paused.png":::
    :::column-end:::
    :::column:::
        **Angehalten** </br></br>Ein Migrationsauftrag wird angehalten, wenn eine Entscheidung erforderlich ist. Dieser Zustand aktiviert zwei Befehlsschaltflächen über dem Blatt: </br>Wählen Sie **Sicherung wiederholen** aus, wenn in der Sicherung Dateien angezeigt werden, die eigentlich übertragen werden sollten, aber nicht übertragen wurden (Spalte *Kopierfehler*). </br>Wählen Sie **Sicherung überspringen** aus, wenn die Sicherung fehlt (von der Richtlinie gelöscht wurde, nachdem der Migrationsauftrag erstellt wurde) oder wenn die Sicherung beschädigt ist. Ausführliche Fehlerinformationen finden Sie auf dem Blatt, das geöffnet wird, wenn Sie auf die fehlgeschlagene Sicherung klicken. </br></br>Wenn Sie die aktuelle Sicherung *überspringen* oder *wiederholen*, erstellt der Migrationsdienst eine neue Momentaufnahme in Ihrer Azure-Zieldateifreigabe. Sie sollte die vorherige ggf. später löschen, da sie wahrscheinlich unvollständig ist.
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-success-focused.png" alt-text="Abbildung des Migrationsauftragsblatts mit einem großen Statussymbol im oberen Bereich im Zustand „Abgeschlossen“." lightbox="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-job-success.png":::
    :::column-end:::
    :::column:::
        **Abgeschlossen** und **Mit Warnungen abgeschlossen**</br></br>Ein Migrationsauftrag wird als **Abgeschlossen** aufgeführt, wenn alle Sicherungen im Auftrag erfolgreich verarbeitet wurden. </br>Der Zustand **Mit Warnungen abgeschlossen** tritt in folgenden Fällen auf: <ul><li>Bei einer Sicherung ist ein behebbares Problem aufgetreten. Diese Sicherung wird als *teilweise erfolgreich* oder *fehlgeschlagen* gekennzeichnet.</li><li>Sie haben sich entschieden, den angehaltenen Auftrag fortzusetzen, indem Sie die Sicherung mit den genannten Problemen übersprungen haben. (Sie haben *Sicherung überspringen* anstelle von *Sicherung wiederholen* ausgewählt.)</li></ul> Wenn der Migrationsauftrag mit Warnungen abgeschlossen wird, sollten Sie immer das Kopierprotokoll für die relevanten Sicherungen überprüfen.
    :::column-end:::
:::row-end:::

#### <a name="run-jobs-in-parallel"></a>Paralleles Ausführen von Aufträgen

Wahrscheinlich verfügen Sie über mehrere StorSimple-Volumes mit jeweils eigenen Freigaben, die in eine Azure-Dateifreigabe migriert werden müssen. Sie müssen wissen, wie viel parallel möglich ist. Es gibt Einschränkungen, die für Benutzer nicht offensichtlich sind und eine vollständige Migration entweder beeinträchtigen oder verhindern, wenn Aufträge gleichzeitig ausgeführt werden.

Es gibt keine Einschränkungen bei der Definition von Migrationsaufträgen. Sie können dasselbe StorSimple-Quellvolumen und dieselbe Azure-Dateifreigabe für die gleichen oder für verschiedene StorSimple-Appliances definieren. Die Ausführung der Migration unterliegt jedoch Einschränkungen:

* Es kann jeweils nur ein Migrationsauftrag mit demselben StorSimple-Quellvolumen ausgeführt werden.
* Es kann jeweils nur ein Migrationsauftrag mit derselben Azure-Zieldateifreigabe ausgeführt werden.
* Sie können bis zu vier Migrationsaufträge pro StorSimple-Geräte-Manager parallel ausführen, solange auch die oben aufgeführten Regeln eingehalten werden.

Wenn Sie versuchen, einen Migrationsauftrag zu starten, werden die oben aufgeführten Regeln überprüft. Wenn Aufträge ausgeführt werden, können Sie den aktuellen Auftrag möglicherweise nicht starten. Sie erhalten eine Warnung mit einer Liste der Namen von aktuell ausgeführten Aufträgen, die beendet sein müssen, damit Sie den neuen Auftrag starten können.

> [!TIP]
> Sie sollten Ihre Migrationsaufträge regelmäßig auf der Registerkarte *Auftragsdefinition* Ihrer *Data Manager*-Ressource überprüfen, um festzustellen, ob einer dieser Aufträge angehalten wurde und Sie eine Eingabe tätigen müssen, um den Auftrag fortzusetzen.

### <a name="phase-3-summary"></a>Zusammenfassung von Phase 3

Am Ende von Phase 3 haben Sie mindestens einen Ihrer Migrationsaufträge von StorSimple-Volumes zu Azure-Dateifreigaben ausgeführt. Nach der Ausführung wurden die angegebenen Sicherungen im Azure-Dateifreigabemomentaufnahmen migriert. Sie können nun entweder die Azure-Dateisynchronisierung für die Freigabe einrichten (nachdem die Migrationsaufträge für eine Freigabe abgeschlossen wurden) oder für Ihre Information-Worker und Apps den Zugriff auf die Azure-Dateifreigabe festlegen.

## <a name="phase-4-access-your-azure-file-shares"></a>Phase 4: Zugreifen auf Ihre Azure-Dateifreigaben

Es gibt zwei Hauptstrategien für das Zugreifen auf Ihre Azure-Dateifreigaben:

* **Azure-Dateisynchronisierung:** Führen Sie die [Bereitstellung der Azure-Dateisynchronisierung](#deploy-azure-file-sync) auf einer lokalen Windows Server-Instanz durch. Die Azure-Dateisynchronisierung verfügt genauso wie StorSimple über alle Vorteile eines lokalen Caches.
* **Direkter Zugriff auf Dateifreigaben:** [Stellen Sie den direkten Zugriff auf Dateifreigaben bereit](#deploy-direct-share-access). Verwenden Sie diese Strategie, wenn Ihr Zugriffsszenario für eine bestimmte Azure-Dateifreigabe nicht vom lokalen Zwischenspeichern profitiert oder Sie keine Möglichkeit mehr haben, eine lokale Windows Server-Instanz zu hosten. Hier greifen Ihre Benutzer und Apps weiterhin über das SMB-Protokoll auf SMB-Freigaben zu. Diese Freigaben befinden sich nicht mehr auf einem lokalen Server, sondern direkt in der Cloud.

Sie sollten bereits in [Phase 1](#phase-1-prepare-for-migration) dieser Anleitung die Entscheidung getroffen haben, welche Option für Sie am besten geeignet ist.

Im Rest dieses Abschnitts liegt der Schwerpunkt auf Bereitstellungsanweisungen.

### <a name="deploy-azure-file-sync"></a>Bereitstellen der Azure-Dateisynchronisierung

Es ist an der Zeit, einen Teil der Azure-Dateisynchronisierung bereitzustellen.

1. Erstellen Sie die Cloudressource für die Azure-Dateisynchronisierung.
1. Stellen Sie den Azure-Dateisynchronisierungs-Agent auf Ihrem lokalen Server bereit.
1. Registrieren Sie den Server bei der Cloudressource.

Erstellen Sie noch keine Synchronisierungsgruppen. Das Einrichten der Synchronisierung mit einer Azure-Dateifreigabe sollte erst erfolgen, nachdem Ihre Aufträge für die Migration zu einer Azure-Dateifreigabe abgeschlossen sind. Wenn Sie mit der Nutzung der Azure-Dateisynchronisierung beginnen, bevor Sie die Migration abgeschlossen haben, wird die Migration unnötig erschwert. Sie können dann nicht einfach erkennen, wann es an der Zeit ist, eine Übernahme einzuleiten.

#### <a name="deploy-the-azure-file-sync-cloud-resource"></a>Bereitstellen der Cloudressource für die Azure-Dateisynchronisierung

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

> [!TIP]
> Wenn Sie möchten, dass sich Ihre Daten nach Abschluss der Migration in einer anderen Azure-Region als bisher befinden, sollten Sie den Speichersynchronisierungsdienst in der Region bereitstellen, in der sich die Zielspeicherkonten für diese Migration befinden.

#### <a name="deploy-an-on-premises-windows-server-instance"></a>Bereitstellen einer lokalen Windows Server-Instanz

* Erstellen Sie eine Windows Server 2019-Instanz (mindestens 2012 R2) als virtuellen Computer oder physischen Server. Ein Windows Server-Failovercluster wird ebenfalls unterstützt. Verwenden Sie nicht wieder den Server, den Sie StorSimple 8100 oder 8600 vorgeschaltet haben.
* Stellen Sie einen direkt angeschlossenen Speicher bereit, oder fügen Sie ihn hinzu. NAS (Network-Attached Storage) wird nicht unterstützt.

Wir empfehlen Ihnen, für Ihre neue Windows Server-Instanz eine gleich große oder größere Menge an Speicher bereitzustellen, als für Ihre StorSimple 8100- oder 8600-Appliance lokal für die Zwischenspeicherung zur Verfügung steht. Sie verwenden die Windows Server-Instanz genauso wie die StorSimple-Appliance. Falls diese über die gleiche Speichermenge wie die Appliance verfügt, sollte das Verhalten bei der Zwischenspeicherung ähnlich oder gleich sein. Sie können für Ihre Windows Server-Instanz wie gewünscht Speicher hinzufügen oder entfernen. Mit dieser Funktion können Sie die Größe des lokalen Volumes und die Menge des für die Zwischenspeicherung verfügbaren lokalen Speichers anpassen.

#### <a name="prepare-the-windows-server-instance-for-file-sync"></a>Vorbereiten der Windows Server-Instanz für die Dateisynchronisierung

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

#### <a name="configure-azure-file-sync-on-the-windows-server-instance"></a>Konfigurieren der Azure-Dateisynchronisierung auf der Windows Server-Instanz

Die registrierte lokale Windows Server-Instanz muss für diesen Prozess vorbereitet und mit dem Internet verbunden sein.

> [!IMPORTANT]
> Bevor Sie die weiteren Schritte ausführen, muss Ihre StorSimple-Migration von Dateien und Ordnern in die Azure-Dateifreigabe abgeschlossen sein. Stellen Sie sicher, dass keine weiteren Änderungen an der Dateifreigabe vorgenommen werden.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

> [!IMPORTANT]
> Vergewissern Sie sich, dass das Cloudtiering aktiviert ist. Cloudtiering ist ein Feature der Azure-Dateisynchronisierung, das es dem lokalen Server ermöglicht, weniger Speicherkapazität als in der Cloud zu haben, aber trotzdem über den vollständigen Namespace zu verfügen. Lokal interessante Daten werden zudem für eine schnelle, lokale Zugriffsleistung lokal zwischengespeichert. Ein weiterer Grund für die Aktivierung des Cloudtierings in diesem Schritt ist, dass wir in dieser Phase keine Dateiinhalte synchronisieren möchten. Hier soll nur der Namespace verschoben werden.

### <a name="deploy-direct-share-access"></a>Bereitstellen von direktem Zugriff auf Dateifreigaben

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        Dieses Video ist eine Anleitung und Demo dazu, wie Azure-Dateifreigaben in fünf einfachen Schritte für Information-Worker und Apps auf sichere Weise direkt verfügbar gemacht werden können.</br>
        In diesem Video wird auf dedizierte Dokumentation für einige Themen verwiesen:

* [Übersicht über Identitäten](storage-files-active-directory-overview.md)
* [Übersicht – lokale Active Directory Domain Services-Authentifizierung über SMB für Azure-Dateifreigaben](storage-files-identity-auth-active-directory-enable.md)
* [Azure Files – Überlegungen zum Netzwerkbetrieb](storage-files-networking-overview.md)
* [Konfigurieren von Azure Files-Netzwerkendpunkten](storage-files-networking-endpoints.md)
* [Konfigurieren eines Site-to-Site-VPN zur Verwendung mit Azure Files](storage-files-configure-s2s-vpn.md)
* [Konfigurieren eines P2S-VPN (Point-to-Site) unter Windows zur Verwendung mit Azure Files](storage-files-configure-p2s-vpn-windows.md)
* [Konfigurieren eines P2S-VPN (Point-to-Site) unter Linux zur Verwendung mit Azure Files](storage-files-configure-p2s-vpn-linux.md)
* [Konfigurieren der DNS-Weiterleitung für Azure Files](storage-files-networking-dns.md)
* [Übersicht über DFS-Namespaces](/windows-server/storage/dfs-namespaces/dfs-overview)
   :::column-end:::
:::row-end:::

### <a name="phase-4-summary"></a>Zusammenfassung von Phase 4

Am Ende dieser Phase haben Sie mehrere Migrationsaufträge in Ihrem StorSimple Data Manager erstellt und ausgeführt. Mit diesen Aufträgen wurden Ihre Dateien und Ordner sowie die zugehörigen Sicherungen in Azure-Dateifreigaben migriert. Darüber hinaus haben Sie die Azure-Dateisynchronisierung bereitgestellt oder Ihr Netzwerk und Ihre Speicherkonten für den direkten Zugriff auf Dateifreigaben vorbereitet.

## <a name="phase-5-user-cut-over"></a>Phase 5: Benutzerübernahme

In dieser Phase geht es darum, Ihre Migration abzuschließen:

* Planen Sie Ihre Downtime.
* Informieren Sie sich über alle Änderungen, die Ihre Benutzer und Apps auf der StorSimple-Seite vorgenommen haben, während die Migrationsaufträge in Phase 3 ausgeführt wurden.
* Führen Sie für die Benutzer mit der Azure-Dateisynchronisierung ein Failover zur neuen Windows Server-Instanz oder über direkten Zugriff ein Failover zu den Azure-Dateifreigaben aus.

### <a name="plan-your-downtime"></a>Planen der Downtime

Dieser Migrationsansatz erfordert etwas Downtime für Ihre Benutzer und Apps. Das Ziel besteht darin, die Downtime möglichst gering zu halten. Hierbei ist die Beachtung der folgenden Aspekte hilfreich:

* Halten Sie Ihre StorSimple-Volumes verfügbar, während Ihre Migrationsaufträge ausgeführt werden.
* Nachdem die von Ihnen ausgeführten Datenmigrationsaufträge für eine Freigabe abgeschlossen sind, sollten Sie den Benutzerzugriff (mindestens Schreibzugriff) aus den StorSimple-Volumes oder -Freigaben entfernen. Mit einem abschließenden RoboCopy-Vorgang wird Ihre Azure-Dateifreigabe auf den aktuellen Stand gebracht. Anschließend können Sie für Ihre Benutzer die Übernahme durchführen. Wo Sie RoboCopy ausführen, hängt davon ab, ob Sie sich für die Verwendung von Azure-Dateisynchronisierung oder direkten Zugriff auf Dateifreigaben entschieden haben. Informationen zu diesem Thema finden Sie weiter unten im Abschnitt zu RoboCopy.
* Nachdem Sie den RoboCopy-Vorgang abgeschlossen haben, um den aktuellen Stand herzustellen, können Sie den neuen Standort für Ihre Benutzer verfügbar machen. Hierfür verwenden Sie entweder direkt die Azure-Dateifreigabe oder eine SMB-Freigabe auf einer Windows Server-Instanz mit Azure-Dateisynchronisierung. Häufig ermöglicht eine DFS-Namespace-Bereitstellung eine schnelle und effiziente Übernahme. Sie bewirkt, dass Ihre vorhandenen Freigabenadressen konsistent bleiben, und verweist auf einen neuen Speicherort, der Ihre migrierten Dateien und Ordner enthält.

Bei Archivierungsdaten besteht eine brauchbare Vorgehensweise zur Vermeidung von Ausfallzeiten für das StorSimple-Volume (oder die Unterordner) darin, eine weitere StorSimple-Volumesicherung zu erstellen, die Migration auszuführen und dann das Migrationsziel für den Zugriff von Benutzern und Apps zu öffnen. Dadurch entfällt die Notwendigkeit, RoboCopy zu verwenden, wie in diesem Abschnitt beschrieben. Diese Vorgehensweise geht jedoch zulasten eines längeren Ausfallzeitfensters, das je nach Anzahl der zu migrierenden Dateien und Sicherungen mehrere Tage oder länger dauern kann. Dies ist wahrscheinlich nur eine Option für Archivierungsworkloads, für die über einen längeren Zeitraum kein Schreibzugriff erforderlich ist.

### <a name="determine-when-your-namespace-has-fully-synced-to-your-server"></a>Feststellen, wann Ihr Namespace vollständig mit dem Server synchronisiert ist

Wenn Sie die Azure-Dateisynchronisierung für eine Azure-Dateifreigabe verwenden, müssen Sie *vor* dem Starten eines lokalen RoboCopy-Befehls ermitteln, ob Ihr gesamter Namespace vollständig auf den Server heruntergeladen wurde. Wie viel Zeit für das Herunterladen Ihres Namespace erforderlich ist, hängt von der Anzahl der Elemente in ihrer Azure-Dateifreigabe ab. Sie können anhand von zwei Methoden ermitteln, ob der Download Ihres Namespace auf den Server vollständig abgeschlossen wurde.

#### <a name="azure-portal"></a>Azure-Portal

Sie können das Azure-Portal verwenden, um zu prüfen, ob Ihr Namespace vollständig heruntergeladen ist.

* Melden Sie sich beim Azure-Portal an, und navigieren Sie zu Ihrer Synchronisierungsgruppe. Überprüfen Sie den Synchronisierungsstatus Ihrer Synchronisierungsgruppe und des Serverendpunkts.
* Uns interessiert hier die Downloadrichtung. Wenn der Serverendpunkt neu bereitgestellt wird, wird dafür **Erste Synchronisierung** angezeigt. Dies ist ein Hinweis darauf, dass der Download des Namespace noch nicht abgeschlossen ist.
Wenn der Status als etwas anderes als **Erste Synchronisierung** angezeigt wird, befindet sich Ihr Namespace vollständig auf dem Server. Sie können jetzt mit einem lokalen RoboCopy-Vorgang fortfahren.

#### <a name="windows-server-event-viewer"></a>Windows Server-Ereignisanzeige

Sie können auch die Ereignisanzeige auf Ihrer Windows Server-Instanz verwenden, um zu ermitteln, wann der Namespace vollständig heruntergeladen wurde.

1. Öffnen Sie die **Ereignisanzeige**, und navigieren Sie zu **Anwendungen und Dienste**.
1. Navigieren Sie zu **Microsoft\FileSync\Agent\Telemetry**, und öffnen Sie den Ordner.
1. Suchen Sie nach dem neuesten **Ereignis 9102**, das einer abgeschlossenen Synchronisierungssitzung entspricht.
1. Wählen Sie **Details** aus, und vergewissern Sie sich, dass ein Ereignis angezeigt wird, für das **SyncDirection** den Wert **Download** hat.
1. Für den Zeitpunkt, zu dem der Download Ihres Namespace auf den Server vollständig abgeschlossen war, ist ein einzelnes Ereignis mit **Scenario**, **FullGhostedSync**-Wert und **HResult** = **0** vorhanden.
1. Sollte dieses Ereignis nicht vorhanden sein, können Sie nach anderen **9102-Ereignissen** mit **SyncDirection** = **Download** und **Scenario** = **RegularSync** suchen. Wenn Sie eines dieser Ereignisse finden, ist dies auch ein Hinweis darauf, dass das Herunterladen des Namespace abgeschlossen und die Synchronisierung zu regelmäßigen Synchronisierungssitzungen übergegangen ist. Dies gilt unabhängig davon, ob derzeit eine Synchronisierung erforderlich ist oder nicht.

### <a name="a-final-robocopy"></a>Abschließende RoboCopy

An diesem Punkt gibt es Unterschiede zwischen Ihrer lokalen Windows Server-Instanz und der StorSimple 8100- oder 8600-Appliance.

1. Sie müssen die Änderungen erfassen, die Benutzer oder Apps auf der StorSimple-Seite vorgenommen haben, während die Migration durchgeführt wurde.
1. In den Fällen, in denen Sie Azure-Dateisynchronisierung verwenden: Die StorSimple-Appliance weist einen gefüllten Cache auf, wohingegen die Windows Server-Instanz zu diesem Zeitpunkt nur über einen Namespace ohne lokal gespeicherten Dateiinhalt verfügt. Daher kann der abschließende RoboCopy-Befehl als Starthilfe für Ihren lokalen Cache der Azure-Dateisynchronisierung dienen, indem so viele lokal zwischengespeicherte Dateiinhalte abgerufen werden, wie verfügbar sind und auf den Azure-Dateisynchronisierungsserver passen.
1. Einige Dateien wurden vom Migrationsauftrag wegen ungültiger Zeichen unter Umständen nicht übertragen. Ist dies der Fall, kopieren Sie diese Dateien auf die Windows Server-Instanz mit aktivierter Azure-Dateisynchronisierung. Später können Sie die Dateien so anpassen, dass sie synchronisiert werden. Wenn Sie die Azure-Dateisynchronisierung für eine bestimmte Freigabe nicht verwenden, ist es besser, die Dateien auf dem StorSimple-Volume mit ungültigen Zeichen umzubenennen. Führen Sie RoboCopy dann direkt für die Azure-Dateifreigabe aus.

> [!WARNING]
> Bei Robocopy unter Windows Server 2019 tritt zurzeit ein Problem auf, das dazu führt, dass Dateien, die von der Azure-Dateisynchronisierung auf dem Zielserver verwendet werden, bei Anwendung der /MIR-Funktion von Robocopy an der Quelle neu kopiert und in Azure hochgeladen werden. Unter anderen Windows Server-Versionen als 2019 muss unbedingt Robocopy verwendet werden. Eine bevorzugte Wahl ist Windows Server 2016. Dieser Hinweis wird aktualisiert, wenn das Problem über Windows Update behoben wurde.

> [!WARNING]
> Sie dürfen RoboCopy *nicht* starten, bevor der Server den Namespace für eine Azure-Dateifreigabe vollständig heruntergeladen hat. Weitere Informationen finden Sie unter [Feststellen, wann Ihr Namespace vollständig mit dem Server synchronisiert ist](#determine-when-your-namespace-has-fully-synced-to-your-server).

 Sie möchten nur Dateien kopieren, die nach dem letzten Ausführen des Migrationsauftrags geändert wurden, sowie Dateien, die durch diese Aufträge noch nicht verschoben wurden. Das Problem, das dazu geführt hat, dass diese Dateien nicht verschoben wurden, können Sie später nach Abschluss der Migration auf dem Server beheben. Weitere Informationen finden Sie unter [Problembehandlung für Azure-Dateisynchronisierung](../file-sync/file-sync-troubleshoot.md#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).

RoboCopy hat mehrere Parameter. Im folgenden Beispiel sind ein fertiger Befehl und eine Liste mit den Gründen für die Auswahl dieser Parameter aufgeführt.

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

Wenn Sie den Quell- und Zielspeicherort des RoboCopy-Befehls konfigurieren, sollten Sie die Struktur der Quelle und des Ziels überprüfen, um sicherzustellen, dass sie übereinstimmen. Wenn Sie die Verzeichniszuordnungsfunktion des Migrationsauftrags verwendet haben, kann es sein, dass sich die Struktur Ihres Stammverzeichnisses von der Struktur Ihres StorSimple-Volumes unterscheidet. Ist dies der Fall, benötigen Sie unter Umständen mehrere RoboCopy-Aufträge (einen für jedes Unterverzeichnis). Falls Sie unsicher sind, ob der Befehl zum gewünschten Ergebnis führt, können Sie den Parameter */L* verwenden. Hiermit wird der Befehl simuliert, ohne dass Änderungen vorgenommen werden.

Bei diesem RoboCopy-Befehl wird der Zusatz „/MIR“ verwendet, damit keine gleichen Dateien (z. B. mehrstufige Dateien) verschoben werden. Wenn aber der Quell- und Zielpfad fehlerhaft ist, werden mit „/MIR“ auch Verzeichnisstrukturen auf Ihrer Windows Server-Instanz oder Azure-Dateifreigabe bereinigt, die im StorSimple-Quellpfad nicht vorhanden sind. Die Pfade müssen genau übereinstimmen, damit der RoboCopy-Auftrag das beabsichtigte Ziel erreicht und lediglich Ihre migrierten Inhalte mit den neuesten Änderungen aktualisiert, die während der Ausführung der Migration vorgenommen wurden.

Überprüfen Sie die RoboCopy-Protokolldatei, um festzustellen, ob Dateien ausgelassen wurden. Wenn Probleme vorliegen, beheben Sie diese, und führen Sie den RoboCopy-Befehl erneut aus. Heben Sie keine Bereitstellung einer StorSimple-Ressource auf, bevor Sie nicht alle Probleme für Dateien oder Ordner behoben haben, die für Sie wichtig sind.

Wenn Sie nicht die Azure-Dateisynchronisierung zum Zwischenspeichern der betreffenden Azure-Dateifreigabe verwenden, sondern sich für den direkten Zugriff auf Dateifreigaben entschieden haben:

1. [Binden Sie Ihre Azure-Dateifreigabe](storage-how-to-use-files-windows.md#mount-the-azure-file-share) als Netzwerklaufwerk auf einem lokalen Windows-Computer ein.
1. Führen Sie den RoboCopy-Befehl zwischen Ihrer StorSimple- und der eingebundenen Azure-Dateifreigabe aus. Falls Dateien nicht kopiert werden, sollten Sie deren Namen auf der StorSimple-Seite korrigieren, um ungültige Zeichen zu entfernen. Versuchen Sie dann erneut, RoboCopy auszuführen. Der weiter oben angegebene RoboCopy-Befehl kann mehrmals ausgeführt werden, ohne dass unnötige StorSimple-Abrufe verursacht werden.

### <a name="troubleshoot-and-optimize"></a>Problembehandlung und Optimierung

[!INCLUDE [storage-files-migration-robocopy-optimize](../../../includes/storage-files-migration-robocopy-optimize.md)]

### <a name="user-cut-over"></a>Benutzerübernahme

Wenn Sie die Azure-Dateisynchronisierung verwenden, müssen Sie wahrscheinlich die SMB-Freigaben auf der Windows Server-Instanz mit aktivierter Azure-Dateisynchronisierung erstellen, die den Freigaben auf den StorSimple-Volumes entsprechen. Sie können diesen Schritt vorverlegen und zu einem früheren Zeitpunkt ausführen, um hier keine Zeit zu verlieren. Allerdings müssen Sie sicherstellen, dass vor diesem Zeitpunkt niemand Zugriff hat, damit auf der Windows Server-Instanz keine Änderungen vorgenommen werden.

Wenn Sie über eine DFS-N-Bereitstellung verfügen, können Sie die DFN-Namespaces auf die neuen Speicherorte für Serverordner verweisen. Wenn Sie keine DFS-N-Bereitstellung nutzen und Ihrer 8100- oder 8600-Appliance lokal eine Windows Server-Instanz vorgeschaltet haben, können Sie diesen Server aus der Domäne entfernen. Binden Sie anschließend Ihre neue Windows Server-Instanz mit aktivierter Azure-Dateisynchronisierung in die Domäne ein. Geben Sie dem Server im Rahmen dieses Vorgangs denselben Servernamen und dieselben Freigabenamen wie beim alten Server, damit die Übernahme für Ihre Benutzer, Gruppenrichtlinien und Skripts transparent bleibt.

Erfahren Sie mehr zu [DFS-Namespaces](/windows-server/storage/dfs-namespaces/dfs-overview).

## <a name="phase-6-deprovision"></a>Phase 6: Aufheben der Bereitstellung

Wenn Sie die Bereitstellung einer Ressource aufheben, haben Sie keinen Zugriff mehr auf die Konfiguration dieser Ressource und die zugehörigen Daten. Die Aufhebung der Bereitstellung kann nicht rückgängig gemacht werden. Fahren Sie erst fort, nachdem Sie Folgendes sichergestellt haben:

* Die Migration ist abgeschlossen.
* Es sind keine Abhängigkeiten von den StorSimple-Dateien, -Ordnern oder -Volumesicherungen vorhanden, für die Sie die Bereitstellung aufheben möchten.

Bevor Sie damit beginnen, ist es ratsam, die neue Bereitstellung der Azure-Dateisynchronisierung in der Produktionsumgebung eine Weile zu beobachten. Dieser Zeitraum gibt Ihnen die Möglichkeit, etwaige auftretende Probleme zu beheben. Nachdem Sie Ihre Bereitstellung der Azure-Dateisynchronisierung einige Tage lang beobachtet haben, können Sie damit beginnen, die Bereitstellung von Ressourcen in der folgenden Reihenfolge aufzuheben:

1. Heben Sie die Bereitstellung Ihrer StorSimple Data Manager-Ressource über das Azure-Portal auf. Dabei werden alle Datentransformationsdienst-Aufträge gelöscht, die Sie erstellt haben. Sie können die Kopierprotokolle nicht auf einfache Weise abrufen. Falls diese für Ihre Aufzeichnungen wichtig sind, müssen Sie sie abrufen, bevor Sie die Bereitstellung aufheben.
1. Vergewissern Sie sich, dass Ihre physischen StorSimple-Appliances migriert wurden, und heben Sie anschließend deren Registrierungen auf. Wenn Sie nicht sicher sind, dass sie migriert wurden, sollten Sie keine weiteren Schritte ausführen. Wenn Sie die Bereitstellungen dieser Ressourcen aufheben, während sie noch erforderlich sind, können Sie weder die Daten noch deren Konfiguration wiederherstellen.<br>Optional können Sie zuerst die Bereitstellung der StorSimple-Volumeressource aufheben. Dies führt dazu, dass die Daten auf der Appliance bereinigt werden. Dieser Prozess kann mehrere Tage dauern und umfasst kein vollständiges forensisches Überschreiben der Daten durch Nullen auf der Appliance. Falls dies für Sie wichtig ist, sollten Sie die Ersetzung durch Nullen auf dem Datenträger separat von der Aufhebung der Bereitstellung der Ressourcen und gemäß Ihren Richtlinien durchführen.
1. Sind in einem StorSimple-Geräte-Manager keine Geräte mehr registriert, können Sie diese Geräte-Manager-Ressource entfernen.
1. An diesem Punkt kann das StorSimple-Speicherkonto in Azure gelöscht werden. Gehen Sie erneut so vor, dass Sie die nächsten Schritte erst ausführen, nachdem Sie sich vergewissert haben, dass die Migration abgeschlossen ist und keine Abhängigkeiten von diesen Daten bestehen.
1. Trennen Sie die physische StorSimple-Appliance von Ihrem Rechenzentrum.
1. Wenn Sie der Besitzer der StorSimple-Appliance sind, können Sie sie in die Wiederverwertung geben. Ist Ihr Gerät geleast, informieren Sie den Leasinggeber, und geben Sie das Gerät entsprechend zurück.

Die Migration ist abgeschlossen.

---

> [!NOTE]
> Haben Sie noch Fragen, oder sind Probleme aufgetreten?</br>
> Wir sind hier, um Ihnen zu helfen: :::image type="content" source="media/storage-files-migration-storsimple-8000/storage-files-migration-storsimple-8000-migration-email.png" alt-text="E-Mail-Adresse in einem Wort: Azure Files migration at microsoft dot com":::

## <a name="next-steps"></a>Nächste Schritte

* Machen Sie sich mit der [Azure-Dateisynchronisierung](../file-sync/file-sync-planning.md) vertraut.
* Informationen über die Flexibilität von [Cloudtiering](../file-sync/file-sync-cloud-tiering-overview.md)-Richtlinien.
* [Aktivieren Sie Azure Backup](../../backup/backup-afs.md#configure-backup-from-the-file-share-pane) in ihren Azure-Dateifreigaben, um Momentaufnahmen zu planen und Aufbewahrungszeitpläne für Sicherungen zu definieren.
* Wenn Sie im Azure-Portal feststellen, dass einige Dateien dauerhaft nicht synchronisiert werden, helfen Ihnen die Schritte zum Lösen des Problems im [Leitfaden zur Problembehandlung](../file-sync/file-sync-troubleshoot.md) weiter.
