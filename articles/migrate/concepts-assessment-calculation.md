---
title: Azure VM-Bewertungen in Azure Migrate
description: Erfahren Sie etwas über Bewertungen in Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 07/28/2021
ms.openlocfilehash: f3fc574e8e381f8e5c911a340ae958ee0939db8f
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130073846"
---
# <a name="assessment-overview-migrate-to-azure-vms"></a>Bewertungsübersicht (Migration zu Azure-VMs)

Dieser Artikel bietet einen Überblick über Bewertungen im Tool [Azure Migrate: Ermittlung und Bewertung](migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool). Mit diesem Tool können Sie lokale Server in der virtuellen VMware- und Hyper-V-Umgebung sowie physische Server für die Migration zu Azure bewerten.

## <a name="whats-an-assessment"></a>Was ist eine Bewertung?

Bei einer Bewertung mit dem Serverermittlungs- und -bewertungstool wird gemessen, inwieweit lokale Server für eine Migration zu Azure bereit sind. Außerdem werden die Auswirkungen einer solchen Migration abgeschätzt.

> [!NOTE]
> Überprüfen Sie in Azure Government die [unterstützten Ziele](migrate-support-matrix.md#supported-geographies-azure-government) für die Bewertung. Beachten Sie, dass für die Empfehlungen zur VM-Größe in Bewertungen die VM-Serien für Regionen der Government-Cloud verwendet werden. [Weitere Informationen](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines) zu VM-Typen.

## <a name="types-of-assessments"></a>Arten von Bewertungen

Sie können drei Arten von Bewertungen mit der Azure Migrate-Ermittlung und -Bewertung erstellen.

***Bewertungstyp** | **Details**
--- | ---
**Azure-VM** | Bewertungen zum Migrieren Ihrer lokalen Server zu virtuellen Azure-Computern. Sie können Ihre lokalen Server in der [VMware](how-to-set-up-appliance-vmware.md)- und [Hyper-V](how-to-set-up-appliance-hyper-v.md)-Umgebung und [physischen Server](how-to-set-up-appliance-physical.md) für die Migration zu Azure-VMs mit diesem Bewertungstyp bewerten.
**Azure SQL** | Bewertungen zum Migrieren Ihrer lokalen SQL Server-Instanzen aus Ihrer VMware-Umgebung zu Azure SQL-Datenbank oder Azure SQL Managed Instance.
**Azure App Service** | Bewertungen zum Migrieren lokaler Web-Apps aus Ihrer VMware-Umgebung zu Azure App Service.
**Azure VMware Solution (AVS)** | Bewertungen zum Migrieren Ihrer lokalen Server zu [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). Sie können Ihre lokalen [VMware-VMs](how-to-set-up-appliance-vmware.md) für die Migration zu Azure VMware Solution (AVS) bewerten, indem Sie diesen Bewertungstyp verwenden. [Weitere Informationen](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Wenn die Anzahl der Azure VM- oder AVS-Bewertungen im Ermittlungs- und Bewertungstool falsch ist, klicken Sie auf die Gesamtzahl der Bewertungen, um zu allen Bewertungen zu navigieren und die Azure VM- oder AVS-Bewertungen neu zu berechnen. Das Ermittlungs- und Bewertungstool zeigt dann die richtige Anzahl für diesen Bewertungstyp an. 

Bewertungen, die Sie mit Azure Migrate erstellen, sind Momentaufnahmen von Daten zu einem gewissen Zeitpunkt. Eine Azure VM-Bewertung verfügt über zwei Optionen für Größenkriterien:

**Bewertungstyp** | **Details** | **Daten**
--- | --- | ---
**Leistungsbasiert** | Bewertungen, die Empfehlungen auf der Grundlage der erfassten Leistungsdaten aussprechen | Die Empfehlung zur VM-Größe basiert auf CPU- und RAM-Auslastungsdaten.<br/><br/> Die Empfehlung zum Datenträgertyp basiert auf den Eingabe-/Ausgabevorgängen pro Sekunde (IOPS) und dem Durchsatz der lokalen Datenträger. Mögliche Datenträgertypen sind Azure HDD Standard, Azure SSD Standard, Azure Premium-Datenträger und Azure Ultra-Datenträger.
**Aktuelle lokale Umgebung** | Bewertungen, bei denen keine Leistungsdaten verwendet werden, um Empfehlungen auszusprechen | Die Empfehlung zur VM-Größe basiert auf der lokalen Servergröße.<br/><br> Der empfohlene Datenträgertyp basiert auf dem ausgewählten Speichertyp für die Bewertung.

## <a name="how-do-i-run-an-assessment"></a>Ausführen einer Bewertung

Eine Bewertung kann auf verschiedene Arten durchgeführt werden.

- Server können mithilfe von Servermetadaten bewertet werden, die von einer einfachen Azure Migrate-Appliance erfasst werden. Die Appliance ermittelt lokale Server. Anschließend werden Servermetadaten und Leistungsdaten an Azure Migrate gesendet.
- Server können mithilfe von Servermetadaten bewertet werden, die im CSV-Format (durch Trennzeichen getrennte Werte) importiert werden.

## <a name="how-do-i-assess-with-the-appliance"></a>Wie führe ich eine Bewertung mit der Appliance durch?

Wenn Sie eine Azure Migrate-Appliance bereitstellen, um lokale Server zu ermitteln, führen Sie die folgenden Schritte aus:

1. Richten Sie Azure und Ihre lokale Umgebung so ein, dass Azure Migrate verwendet werden kann.
1. Erstellen Sie für Ihre erste Bewertung ein Azure-Projekt, und fügen Sie dem Projekt das Ermittlungs- und Bewertungstool hinzu.
1. Stellen Sie eine einfache Azure Migrate-Appliance bereit. Die Appliance ermittelt kontinuierlich lokale Server und sendet Servermetadaten und Leistungsdaten an Azure Migrate. Stellen Sie die Appliance als VM oder physischen Server bereit. Auf den Servern, die Sie bewerten möchten, müssen keine Komponenten installiert werden.

Wenn die Appliance mit der Serverermittlung begonnen hat, können Sie die Server, die Sie bewerten möchten, einer Gruppe hinzufügen und eine Bewertung für diese Gruppe mit dem Bewertungstyp **Azure VM** durchführen.

Führen Sie diese Schritte zum Testen des Verfahrens mit unseren Tutorials für [VMware](./tutorial-discover-vmware.md), [Hyper-V](./tutorial-discover-hyper-v.md) oder [physische Server](./tutorial-discover-physical.md) aus.

## <a name="how-do-i-assess-with-imported-data"></a>Wie führe ich eine Bewertung mit importierten Daten durch?

Wenn Sie Server mithilfe einer CSV-Datei bewerten, benötigen Sie keine Appliance. Führen Sie stattdessen die folgenden Schritte aus:

1. Richten Sie Azure für die Verwendung mit Azure Migrate ein.
1. Erstellen Sie für Ihre erste Bewertung ein Azure-Projekt, und fügen Sie dem Projekt das Ermittlungs- und Bewertungstool hinzu.
1. Laden Sie eine CSV-Vorlage herunter, und fügen Sie Serverdaten zu dieser Vorlage hinzu.
1. Importieren Sie die Vorlage in Azure Migrate.
1. Ermitteln Sie die mit dem Import hinzugefügten Server, fassen Sie sie in einer Gruppe zusammen, und führen Sie eine Bewertung für die Gruppe mit dem Bewertungstyp **Azure VM** durch.

## <a name="what-data-does-the-appliance-collect"></a>Welche Daten werden von der Appliance erfasst?

Wenn Sie die Azure Migrate-Appliance für die Bewertung verwenden, erfahren Sie in den folgenden verlinkten Artikeln mehr über die Metadaten und Leistungsdaten, die für [VMware](migrate-appliance.md#collected-data---vmware) und [Hyper-V](migrate-appliance.md#collected-data---hyper-v) erfasst werden.

## <a name="how-does-the-appliance-calculate-performance-data"></a>Wie berechnet die Appliance Leistungsdaten?

Wenn Sie die Appliance für die Ermittlung verwenden, führt die Appliance folgende Schritte zum Erfassen der Leistungsdaten für Computeeinstellungen aus:

1. Die Appliance erfasst einen Echtzeit-Abtastpunkt.

    - **VMware-VMs:** Ein Abtastpunkt wird alle 20 Sekunden erfasst.
    - **Virtuelle Hyper-V-Computer:** Ein Abtastpunkt wird alle 30 Sekunden erfasst.
    - **Physische Server**: Ein Abtastpunkt wird alle fünf Minuten erfasst.

1. Die Appliance kombiniert die Beispielpunkte so, dass bei VMware- und Hyper-V-Servern alle 10 Minuten und bei physischen Servern alle 5 Minuten ein einziger Datenpunkt erstellt wird. Zum Erstellen des Datenpunkts wählt die Appliance den Spitzenwert aus allen Stichproben aus. Anschließend sendet sie den Datenpunkt an Azure.
1. Die Bewertung speichert alle im zehnminütigen Intervall erfassten Datenpunkte des letzten Monats.
1. Beim Erstellen einer Bewertung wird der geeignete Datenpunkt ermittelt, um die richtige Größe zu bestimmen. Die Ermittlung basiert auf dem Perzentilwert für den *Leistungsverlauf* und dem *Perzentilwert der Nutzung*.

    - Wenn der Leistungsverlauf beispielsweise auf eine Woche festgelegt ist und der Perzentilwert der Nutzung das 95. Perzentil ist, sortiert die Bewertung die 10-minütigen Abtastpunkte für die letzte Woche. in aufsteigender Reihenfolge und wählt das 95. Perzentil zum Bestimmen der richtigen Größe aus.
    - Mit dem Wert des 95. Perzentils wird sichergestellt, dass Ausreißer ignoriert werden. Diese können enthalten sein, wenn Sie das 99. Perzentil wählen.
    - Falls Sie die Spitzenauslastung für den Zeitraum wählen und auch Ausreißer berücksichtigen möchten, sollten Sie das 99. Perzentil als Perzentilwert der Nutzung wählen.

1. Dieser Wert wird mit dem Komfortfaktor multipliziert, um die effektiven Leistungs- und Auslastungsdaten für diese Metriken zu erhalten, die von der Appliance erfasst werden:

    - CPU-Auslastung
    - RAM-Auslastung
    - Datenträger-IOPS (Lese- und Schreibvorgänge)
    - Datenträgerdurchsatz (Lese- und Schreibvorgänge)
    - Netzwerkdurchsatz (eingehend und ausgehend)

## <a name="how-are-azure-vm-assessments-calculated"></a>Wie werden Azure VM-Bewertungen berechnet?

Bei der Bewertung werden mithilfe von Metadaten und Leistungsdaten der lokalen Server Bewertungen durchgeführt. Wenn Sie die Azure Migrate-Appliance bereitstellen, erfolgt die Bewertung anhand von Daten, die von der Appliance erfasst werden. Wenn Sie jedoch eine Bewertung anhand von Daten durchführen, die mithilfe einer CSV-Datei importiert wurden, stellen Sie die Metadaten für die Berechnung bereit.

Berechnungen erfolgen in den folgenden drei Phasen:

1. **Berechnen der Azure-Bereitschaft:** Bewerten, ob die Server für die Migration zu Azure geeignet sind
1. **Berechnen der Größenempfehlungen:** Schätzen der Compute-, Speicher- und Netzwerkgröße
1. **Berechnen der monatlichen Kosten:** Berechnen der geschätzten monatlichen Compute- und Speicherkosten für die Ausführung der Server in Azure nach der Migration

Berechnungen erfolgen in der obigen Reihenfolge. Ein Server gelangt erst dann in die nächste Phase, wenn die vorherige Phase abgeschlossen wurde. Wenn ein Server z. B. die Phase zur Bewertung der Azure-Bereitschaft nicht besteht, wird er als nicht geeignet für Azure gekennzeichnet. Für diesen Server werden keine Berechnungen zur Größe und zu den Kosten durchgeführt.

## <a name="whats-in-an-azure-vm-assessment"></a>Was umfasst eine Azure VM-Bewertung?

Eine Azure VM-Bewertung umfasst Folgendes:

**Eigenschaft** | **Details**
--- | ---
**Zielstandort** | Der Standort, zu dem die Migration durchgeführt werden soll. Die Bewertung unterstützt derzeit die folgenden Azure-Zielregionen:<br/><br/> „Australien, Osten“, „Australien, Südosten“, „Brasilien, Süden“, „Kanada, Mitte“, „Kanada, Osten“, „Indien, Mitte“, „USA, Mitte“, „China, Osten“, „China, Norden“, „Asien, Osten“, „USA, Osten“, „USA, Osten 2“„Deutschland, Mitte“, „Deutschland, Nordosten“, „Japan, Osten“, „Japan, Westen“, „Korea, Mitte“, „Korea, Süden“, „USA, Norden-Mitte“, „Europa, Norden“, „USA, Süden-Mitte“, „Asien, Südosten“, „Indien, Süden“, „Vereinigtes Königreich, Süden“, „Vereinigtes Königreich, Westen“, „US Gov Arizona“, „US Gov Texas“, „US Gov Virginia“, „USA, Westen-Mitte“, „Europa, Westen“, „Indien, Westen“, „USA, Westen“ und „USA, Westen 2“.
**Zielspeicherdatenträger (übernommene Größe)** | Der Typ der Datenträger, die für die Speicherung in Azure verwendet werden sollen. <br/><br/> Geben Sie den Zielspeicherdatenträger als verwalteten Premium-, verwalteten SSD Standard- oder verwalteten HDD Standard- oder Ultra-Datenträger an.
**Zielspeicherdatenträger (leistungsbasierte Größenanpassung)** | Geben Sie den Zielspeicherdatenträger als automatischen, verwalteten Premium-, verwalteten HDD Standard-, verwalteten SSD Standard-Datenträger oder Ultra-Datenträger an.<br/><br/> **Automatisch**: Die Datenträgerempfehlung erfolgt basierend auf den Leistungsdaten der Datenträger (IOPS und Durchsatz).<br/><br/>**Premium-, Standard- oder Ultra-Datenträger**: Für die Bewertung wird eine Datenträger-SKU innerhalb des ausgewählten Speichertyps empfohlen.<br/><br/> Wenn Sie eine Einzelinstanz-VM-SLA (Vereinbarung zum Servicelevel) von 99,9 % erreichen möchten, sollten Sie verwaltete Premium-Datenträger in Betracht ziehen. Dadurch wird sichergestellt, dass für alle Datenträger in der Bewertung verwaltete Premium-Datenträger empfohlen werden.<br/><br/> Wenn Sie datenintensive Workloads ausführen möchten, die hohen Durchsatz, hohe IOPS und konsistenten Datenträgerspeicher mit geringer Latenz benötigen, sollten Sie die Verwendung von Ultra-Datenträgern in Betracht ziehen.<br/><br/> Azure Migrate unterstützt für die Migrationsbewertung ausschließlich verwaltete Datenträger.
**Azure Reserved VM Instances** | Geben Sie [reservierte Instanzen](https://azure.microsoft.com/pricing/reserved-vm-instances/) an, damit diese bei Kostenschätzungen in der Bewertung berücksichtigt werden.<br/><br/> Wenn Sie „Reservierte Instanzen“ auswählen, sind die „Rabatt (%)“- und „VM-Betriebszeit“-Eigenschaften nicht anwendbar.<br/><br/> Azure Migrate unterstützt Azure Reserved VM Instances derzeit nur bei Angeboten mit nutzungsbasierter Bezahlung.
**Größenkriterien** | Für die Anpassung der Azure-VM-Größe.<br/><br/> Sie können die Größe übernehmen oder die leistungsbasierte Größenanpassung verwenden.
**Leistungsverlauf** | Wird bei der leistungsbasierten Größenanpassung verwendet. Der Leistungsverlauf gibt den Zeitraum für die Bewertung von Leistungsdaten an.
**Perzentilwert der Nutzung** | Wird bei der leistungsbasierten Größenanpassung verwendet. Der Perzentilwert der Nutzung gibt den für die Größenanpassung zu verwendenden Perzentilwert der Leistungsstichprobe an.
**VM-Serie** | Die Azure-VM-Serie, die für die Größenanpassung berücksichtigt werden soll. Wenn Sie beispielsweise in der Produktionsumgebung keine virtuellen Computer der A-Serie in Azure benötigen, können Sie die A-Serie aus der Liste der Serien ausschließen.
**Komfortfaktor** | Der bei der Bewertung verwendete Puffer. Er wird auf die CPU-, RAM-, Datenträger- und Netzwerkdaten für VMs angewendet. Er berücksichtigt Aspekte wie saisonale Nutzung, einen kurzen Leistungsverlauf und eine voraussichtliche Zunahme der zukünftigen Nutzung.<br/><br/> Beispiel: Für einen virtuellen Computer mit zehn Kernen und einer Auslastung von 20 % ergibt sich normalerweise ein virtueller Computer mit zwei Kernen. Bei einem Komfortfaktor von 2.0 ist das Ergebnis dagegen ein virtueller Computer mit vier Kernen.
**Angebot** | Das [Azure-Angebot](https://azure.microsoft.com/support/legal/offer-details/), für das Sie registriert sind. Bei der Bewertung werden die Kosten für dieses Angebot geschätzt.
**Währung** | Die Rechnungswährung für Ihr Konto.
**Rabatt (%)** | Abonnementspezifische Rabatte, die Sie zusätzlich zum Azure-Angebot erhalten. Die Standardeinstellung ist 0 %.
**VM-Betriebszeit** | Die Dauer in Tagen pro Monat sowie Stunden pro Tag für Azure-VMs, die nicht dauerhaft ausgeführt werden. Die Kostenschätzungen basieren auf dieser Dauer.<br/><br/> Die Standardwerte sind 31 Tage pro Monat und 24 Stunden pro Tag.
**Azure-Hybridvorteil** | Gibt an, ob Sie über Software Assurance verfügen und den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-use-benefit/) nutzen können. Wenn für diese Einstellung der Standardwert „Ja“ festgelegt ist, werden für Windows-VMs die Azure-Preise für andere Betriebssysteme als Windows berücksichtigt.
**EA-Abonnement** | Mit dieser Eigenschaft wird festgelegt, dass ein EA-Abonnement (Enterprise Agreement) für die Kostenschätzung verwendet wird. Dabei wird der Rabatt berücksichtigt, der für das Abonnement gilt. <br/><br/> Behalten Sie die Standardeinstellungen für die Eigenschaften „reservierte Instanzen“, „Rabatt (%)“ und „VM-Betriebszeit“ bei.


Sehen Sie sich die [bewährten Methoden](best-practices-assessment.md) für die Erstellung einer Bewertung mit Azure Migrate an.

## <a name="calculate-readiness"></a>Berechnen der Bereitschaft

Nicht alle Server sind für die Ausführung in Azure geeignet. Bei einer Azure VM-Bewertung werden alle lokalen Server bewertet und einer Bereitschaftskategorie zugewiesen.

- **Bereit für Azure**: Der Server kann ohne Änderungen zu Azure migriert werden. Er wird in Azure mit vollständiger Azure-Unterstützung gestartet.
- **Bedingt bereit für Azure**: Der Server kann ggf. in Azure gestartet werden, verfügt jedoch möglicherweise nicht über vollständigen Azure-Support. Beispielsweise unterstützt Azure keine Server, auf denen eine ältere Version von Windows Server ausgeführt wird. Gehen Sie vor der Migration dieser Server zu Azure vorsichtig vor. Befolgen Sie die in der Bewertung empfohlene Korrekturanleitung, um Bereitschaftsprobleme zu beheben.
- **Nicht bereit für Azure**: Der Server wird in Azure nicht gestartet. Wenn auf dem Datenträger eines lokalen Server z. B. mehr als 64 TB Daten gespeichert sind, kann dieser Server nicht in Azure gehostet werden. Befolgen Sie vor der Migration die Korrekturanleitung, um das Problem zu beheben.
- **Bereitschaft unbekannt**: Azure Migrate kann die Bereitschaft des Servers aufgrund unzureichender Metadaten nicht ermitteln.

Zur Berechnung der Bereitschaft werden bei der Bewertung die in den folgenden Tabellen aufgeführten Servereigenschaften und Betriebssystemeinstellungen überprüft.

### <a name="server-properties"></a>Servereigenschaften

Für eine Azure VM-Bewertung überprüft die Bewertung die folgenden Eigenschaften einer lokalen VM, um zu ermitteln, ob diese auf Azure-VMs ausgeführt werden kann.

Eigenschaft | Details | Azure-Bereitschaftsstatus
--- | --- | ---
**Starttyp** | Azure unterstützt virtuelle Computer mit dem Starttyp BIOS, jedoch nicht UEFI. | Bedingt bereit, wenn der Starttyp UEFI ist
**Kerne** | Pro Server sind maximal 128 Kerne zulässig. Dies ist die maximale Anzahl, die von einer Azure-VM unterstützt wird.<br/><br/> Wenn der Leistungsverlauf verfügbar ist, berücksichtigt Azure Migrate die genutzten Kerne beim Vergleich. Wenn in den Bewertungseinstellungen ein Komfortfaktor festgelegt ist, wird die Anzahl von genutzten Kernen mit diesem Komfortfaktor multipliziert.<br/><br/> Wenn kein Leistungsverlauf vorhanden ist, verwendet Azure Migrate die zugeordneten Kerne zur Anwendung des Komfortfaktors. | Bereit, wenn die Anzahl von Kernen innerhalb des Limits liegt
**RAM** | Pro Server sind maximal 3.892 GB RAM zulässig. Dies ist die maximale Größe, die von einer Azure-VM der M-Serie vom Typ Standard_M128m&nbsp;<sup>2</sup> unterstützt wird. [Weitere Informationen](../virtual-machines/sizes.md)<br/><br/> Wenn der Leistungsverlauf verfügbar ist, berücksichtigt Azure Migrate den genutzten RAM zum Vergleich. Wenn ein Komfortfaktor festgelegt ist, wird der genutzte RAM mit dem Komfortfaktor multipliziert.<br/><br/> Wenn kein Verlauf vorhanden ist, wird der zugeordnete RAM verwendet, um einen Komfortfaktor anzuwenden.<br/><br/> | Bereit, wenn die RAM-Größe innerhalb des Limits liegt
**Speicherdatenträger** | Die zugeordnete Größe eines Datenträgers darf maximal 64 TB betragen.<br/><br/> Pro Server dürfen einschließlich des Betriebssystemdatenträgers höchstens 65 Datenträger angefügt sein. | Bereit, wenn die Größe und Anzahl von Datenträgern innerhalb der Limits liegen
**Netzwerk** | Pro Server dürfen maximal 32 Netzwerkschnittstellen (NICs) angefügt sein. | Bereit, wenn die Anzahl von NICs innerhalb des Limits liegt

### <a name="guest-operating-system"></a>Gastbetriebssystem

Für eine Azure VM-Bewertung untersucht die Bewertung neben den VM-Eigenschaften auch das Gastbetriebssystem eines Servers, um zu ermitteln, ob es in Azure ausgeführt werden kann.

> [!NOTE]
> Für die Gastbetriebssystemanalyse bei VMware-VMs verwendet die Bewertung das in vCenter Server für die VM angegebene Betriebssystem. vCenter Server stellt jedoch nicht die Kernelversion für Linux-VM-Betriebssysteme zur Verfügung. Zum Ermitteln der Version müssen Sie die [Anwendungsermittlung](./how-to-discover-applications.md) einrichten. Dann ermittelt die Appliance Versionsinformationen anhand der Gastanmeldeinformationen, die Sie beim Einrichten der App-Ermittlung angeben.


Die Bewertung verwendet die folgende Logik, um die Azure-Bereitschaft basierend auf dem Betriebssystem zu ermitteln:

**Betriebssystem** | **Details** | **Azure-Bereitschaftsstatus**
--- | --- | ---
Windows Server 2016 und alle SPs | Azure bietet vollständige Unterstützung. | Bereit für Azure.
Windows Server 2012 R2 und alle SPs | Azure bietet vollständige Unterstützung. | Bereit für Azure.
Windows Server 2012 und alle SPs | Azure bietet vollständige Unterstützung. | Bereit für Azure.
Windows Server 2008 R2 mit allen SPs | Azure bietet vollständige Unterstützung.| Bereit für Azure.
Windows Server 2008 (32-Bit und 64-Bit) | Azure bietet vollständige Unterstützung. | Bereit für Azure.
Windows Server 2003 und Windows Server 2003 R2 | Der Unterstützungszeitraum für diese Betriebssysteme ist abgelaufen. Für die Unterstützung in Azure wird eine [benutzerdefinierte Supportvereinbarung](/troubleshoot/azure/virtual-machines/server-software-support) (Custom Support Agreement, CSA) benötigt. | Bedingt bereit für Azure. Ziehen Sie vor der Migration zu Azure ein Upgrade des Betriebssystems in Erwägung.
Windows 2000, Windows 98, Windows 95, Windows NT, Windows 3.1 und MS-DOS | Der Unterstützungszeitraum für diese Betriebssysteme ist abgelaufen. Der Server startet möglicherweise in Azure, aber Azure stellt keine Betriebssystemunterstützung bereit. | Bedingt bereit für Azure. Es wird empfohlen, vor der Migration zu Azure ein Upgrade des Betriebssystems auszuführen.
Windows 7, Windows 8 und Windows 10 | Azure bietet [nur mit einem Visual Studio-Abonnement](../virtual-machines/windows/client-images.md) Unterstützung. | Bedingt bereit für Azure.
Windows 10 Pro | Azure bietet Unterstützung mit [mehrinstanzenfähigen Hostingrechten](../virtual-machines/windows/windows-desktop-multitenant-hosting-deployment.md). | Bedingt bereit für Azure.
Windows Vista und Windows XP Professional | Der Unterstützungszeitraum für diese Betriebssysteme ist abgelaufen. Der Server startet möglicherweise in Azure, aber Azure stellt keine Betriebssystemunterstützung bereit. | Bedingt bereit für Azure. Es wird empfohlen, vor der Migration zu Azure ein Upgrade des Betriebssystems auszuführen.
Linux | Azure unterstützt diese [Linux-Betriebssysteme](../virtual-machines/linux/endorsed-distros.md). Andere Linux-Betriebssysteme werden möglicherweise ebenfalls in Azure gestartet. Vor der Migration zu Azure wird jedoch ein Upgrade auf eine unterstützte Betriebssystemversion empfohlen. | Bereit für Azure, wenn die Version unterstützt wird.<br/><br/>Bedingt bereit, wenn die Version nicht unterstützt wird.
Andere Betriebssysteme wie Oracle Solaris, Apple macOS und FreeBSD | Azure unterstützt diese Betriebssysteme nicht. Der Server startet möglicherweise in Azure, aber Azure stellt keine Betriebssystemunterstützung bereit. | Bedingt bereit für Azure. Es wird empfohlen, vor der Migration zu Azure ein unterstütztes Betriebssystem zu installieren.  
In vCenter Server als **Sonstige** angegebenes Betriebssystem | In diesem Fall kann Azure Migrate das Betriebssystem nicht identifizieren. | Bereitschaft unbekannt. Stellen Sie sicher, dass Azure das auf dem virtuellen Computer ausgeführte Betriebssystem unterstützt.
32-Bit-Betriebssysteme | Der Server kann in Azure gestartet werden, Azure bietet jedoch möglicherweise keine vollständige Unterstützung. | Bedingt bereit für Azure. Ziehen Sie vor der Migration zu Azure ein Upgrade auf ein 64-Bit-Betriebssystems in Erwägung.

## <a name="calculating-sizing"></a>Berechnen der Größe

Nachdem ein Server als bereit für Azure markiert wurde, werden bei der Bewertung Größenempfehlungen in der Azure VM-Bewertung angegeben. Diese Empfehlungen beziehen sich auf die Azure-VM und die Datenträger-SKU. Die Berechnung der Größe hängt davon ab, ob Sie die lokale Größe übernehmen oder eine leistungsbasierte Größenanpassung verwenden.

### <a name="calculate-sizing-as-is-on-premises"></a>Berechnen der Größe (wie in der lokalen Umgebung)

 Wenn Sie die lokale Größe übernehmen, wird der Leistungsverlauf der VMs und Datenträger bei der Bewertung in der Azure VM-Bewertung nicht berücksichtigt.

- **Computegröße**: Das Bewertungstool weist basierend auf der lokal zugeordneten Größe eine Azure-VM-SKU zu.
- **Speicher-/Datenträgergröße**: Bei der Bewertung wird der Speichertyp berücksichtigt, der in den Bewertungseigenschaften angegeben wurde, und ein entsprechender Datenträgertyp empfohlen. Mögliche Speichertypen sind HDD Standard-, SSD Standard-, Premium- und Ultra-Datenträger. Der Standardspeichertyp ist „Premium“.
- **Netzwerkgröße**: Bei der Bewertung wird der Netzwerkadapter auf dem lokalen Server berücksichtigt.

### <a name="calculate-sizing-performance-based"></a>Berechnen der Größe (leistungsbasierte Anpassung)

Bei Verwendung der leistungsbasierten Größenanpassung in einer Azure VM-Bewertung werden Größenempfehlungen wie folgt ermittelt:

- Bei der Bewertung wird der Leistungsverlauf des Servers berücksichtigt, um die VM-Größe und den Datenträgertyp in Azure zu ermitteln.

> [!NOTE] 
> Wenn Sie Server mithilfe einer CSV-Datei importieren, werden die von Ihnen angegebenen Leistungswerte (CPU-Auslastung, Arbeitsspeicherauslastung, Datenträger-IOPS und Durchsatz) verwendet, wenn Sie die leistungsbasierte Größenanpassung auswählen. Sie können keine Leistungsverlaufs- und Perzentilinformationen bereitstellen.

- Diese Methode ist besonders nützlich, wenn Sie für den lokalen Server eine übermäßige Zuordnung vorgenommen haben, die Auslastung jedoch gering ist, und Sie die Größe der Azure-VM anpassen möchten, um Kosten zu sparen.
- Wenn Sie die Leistungsdaten nicht verwenden möchten, setzen Sie die Größenkriterien auf die lokale Größe zurück (siehe vorheriger Abschnitt).


#### <a name="calculate-storage-sizing"></a>Berechnen der Speichergröße

Bei der Speichergröße in einer Azure VM-Bewertung versucht Azure Migrate, jeden an den Server angefügten Datenträger einem Datenträger in Azure zuzuordnen. Dies funktioniert wie folgt:

1. Die Bewertung addiert die Lese- und Schreib-IOPS eines Datenträgers, um die gesamten benötigten IOPS zu ermitteln. Ebenso werden die Werte für Lese- und Schreibdurchsatz addiert, um den gesamten Durchsatz jedes Datenträgers zu erhalten. Bei importbasierten Bewertungen haben Sie die Möglichkeit, die gesamten IOPS, den Gesamtdurchsatz und die Gesamtanzahl von Datenträgern in der importierten Datei ohne Angabe einzelner Datenträgereinstellungen anzugeben. In diesem Fall wird die individuelle Datenträgergrößenanpassung übersprungen und die gelieferten Daten werden direkt zur Berechnung der Größenanpassung und zur Auswahl einer geeigneten VM-SKU verwendet.

1. Wenn Sie für den Speichertyp die Option „Automatisch“ festgelegt haben, basiert der ausgewählte Typ auf den effektiven IOPS- und Durchsatzwerten. Die Bewertung bestimmt, ob der Datenträger einem HDD Standard-, SSD Standard-, Premium- oder Ultra-Datenträger in Azure zugeordnet werden soll. Wenn der Speichertyp auf einen dieser Datenträgertypen festgelegt wurde, wird versucht, eine Datenträger-SKU innerhalb des ausgewählten Speichertyps zu finden.
1. Die Datenträger werden wie folgt ausgewählt:
    - Wenn die Bewertung keinen Datenträger mit den erforderlichen IOPS- und Durchsatzwerten finden kann, wird der Server als ungeeignet für Azure gekennzeichnet.
    - Wenn die Bewertung mehrere geeignete Datenträger findet, wählt sie diejenigen aus, die den in den Bewertungseinstellungen angegebenen Speicherort unterstützen.
    - Falls mehrere geeignete Datenträger vorhanden sind, wählt die Bewertung den mit den geringsten Kosten aus.
    - Wenn für einen Datenträger keine Leistungsdaten verfügbar sind, wird anhand der Datenträgergröße innerhalb der Konfiguration nach einem SSD Standard-Datenträger in Azure gesucht.

##### <a name="ultra-disk-sizing"></a>Größen für Ultra-Datenträger

Für Ultra-Datenträger gibt es einen Bereich von IOPS und Durchsatz, der für eine bestimmte Datenträgergröße zulässig ist, und daher unterscheidet sich die Logik für die Dimensionierung von Standard- und Premium-Datenträgern:
1. Es werden drei Ultra-Datenträgergrößen berechnet: 
    - Es wurde ein Datenträger (Datenträger 1) gefunden, der die Anforderungen an die Datenträgergröße erfüllen kann.
    - Es wurde ein Datenträger (Datenträger 2) gefunden, der die Gesamtanforderungen an IOPS erfüllen kann.
        - Bereitzustellende IOPS = (Quelldatenträgerdurchsatz) *1024/256
    - Es wurde ein Datenträger (Datenträger 3) gefunden, der die Gesamtanforderungen an den Durchsatz erfüllen kann.
1. Von den drei Datenträgern wird einer mit der maximalen Datenträgergröße gefunden und auf das nächste verfügbare [Angebot für Ultra-Datenträger](../virtual-machines/disks-types.md#ultra-disks) aufgerundet. Dies ist die bereitgestellte Ultra-Datenträgergröße.
1. Bereitgestellte IOPS werden mit der folgenden Logik berechnet:
    - Wenn der gefundene Quelldurchsatz im zulässigen Bereich für die Ultra-Datenträgergröße liegt, entspricht der bereitgestellte IOPS-Wert dem IOPS-Wert des Quelldatenträgers.
    - Der bereitgestellte IOPS-Wert wird andernfalls mithilfe der bereitzustellenden IOPS berechnet = (Quelldatenträgerdurchsatz) *1024/256
1. Der bereitgestellte Durchsatzbereich hängt von den bereitgestellten IOPS ab.


#### <a name="calculate-network-sizing"></a>Berechnen der Netzwerkgröße

Für eine Azure VM-Bewertung versucht die Bewertung, eine Azure-VM zu finden, die die Anzahl der an den lokalen Server angefügten Netzwerkadapter sowie deren erforderliche Leistung unterstützt.

- Um die effektive Netzwerkleistung des lokalen Servers zu ermitteln, aggregiert die Bewertung die ausgehende Datenübertragungsrate des Servers (ausgehende Netzwerkrate) aller Netzwerkadapter. Anschließend wird der Komfortfaktor angewendet. Anhand des resultierenden Werts wird nach einem virtuellen Azure-Computer gesucht, der die erforderliche Netzwerkleistung unterstützen kann.
- Neben der Netzwerkleistung prüft die Bewertung zudem, ob die Azure-VM die erforderliche Anzahl von Netzwerkadaptern unterstützen kann.
- Wenn keine Daten zur Netzwerkleistung verfügbar sind, berücksichtigt die Bewertung bei der Größenanpassung für VMs lediglich die Anzahl von Netzwerkadaptern.

#### <a name="calculate-compute-sizing"></a>Berechnen der Computegröße

Nach der Berechnung der Speicher- und Netzwerkanforderungen prüft die Bewertung die CPU- und RAM-Anforderungen, um eine geeignete VM-Größe in Azure zu ermitteln.

- Azure Migrate betrachtet die effektiv genutzten Kerne und den RAM, um eine geeignete Größe der Azure-VMs zu bestimmen.
- Wenn keine geeignete Größe gefunden wird, wird der Server als ungeeignet für Azure gekennzeichnet.
- Wenn eine geeignete Größe gefunden wird, wendet Azure Migrate die Speicher -und Netzwerkberechnungen an. Für die endgültige VM-Größenempfehlung werden dann noch Standort- und Tarifeinstellungen angewandt.
- Wenn mehrere geeignete Azure-VM-Größen vorhanden sind, wird die mit den geringsten Kosten empfohlen.

## <a name="confidence-ratings-performance-based"></a>Zuverlässigkeitsstufen (leistungsbasiert)

Jede leistungsbasierte Azure VM-Bewertung in Azure Migrate ist einer Zuverlässigkeitsstufe zugeordnet. Die Bewertung reicht von einem Stern (niedrigste Zuverlässigkeit) bis fünf Sterne (höchste Zuverlässigkeit). Anhand der Zuverlässigkeitsstufe können Sie die Zuverlässigkeit der von Azure Migrate bereitgestellten Größenempfehlungen besser einschätzen.

- Die Zuverlässigkeitsstufe wird einer Bewertung zugewiesen. Sie basiert auf der Verfügbarkeit von Datenpunkten, die zum Berechnen der Bewertung erforderlich sind.
- Die Bewertung benötigt für die leistungsbasierte Größenanpassung die folgenden Informationen:
    - Die Auslastungsdaten für CPU und RAM.
    - IOPS- und Durchsatzdaten für jeden an den Server angefügten Datenträger.
    - Informationen zur Netzwerk-E/A für die leistungsbasierte Größenanpassung für jeden Netzwerkadapter, der an einen Server angefügt ist.

Steht eine dieser Nutzungsangaben nicht zur Verfügung, sind die Größenempfehlungen möglicherweise nicht zuverlässig.

> [!NOTE]
> Servern, die anhand von importierten CSV-Dateien bewertet werden, werden keine Zuverlässigkeitsstufen zugewiesen. Die Bewertung gilt nicht für Bewertungen mit Übernahme der lokalen Größe.

### <a name="ratings"></a>Ratings

Die nachfolgende Tabelle zeigt die Zuverlässigkeitsstufen einer Bewertung, die vom Prozentsatz der verfügbaren Datenpunkte abhängen:

   **Verfügbarkeit von Datenpunkten** | **Zuverlässigkeitsstufe**
   --- | ---
   0–20 % | 1 Stern
   21–40 % | 2 Sterne
   41–60 % | 3 Sterne
   61–80 % | 4 Sterne
   81–100 % | 5 Sterne

### <a name="low-confidence-ratings"></a>Niedrige Zuverlässigkeitsstufen

Einige mögliche Gründe für eine niedrige Zuverlässigkeitsstufe einer Bewertung:

- Sie haben für den Zeitraum, für den Sie die Bewertung erstellen, kein Profil der Umgebung erstellt. Wenn Sie die Bewertung z. B. mit einer auf einen Tag festgelegten Leistungsdauer erstellen, müssen Sie bis mindestens einen Tag nach dem Start der Ermittlung warten, bis alle Datenpunkte gesammelt sind.
- Die Bewertung kann die Leistungsdaten für einige oder alle Server im Bewertungszeitraum nicht erfassen. Für eine Bewertung mit hoher Konfidenz stellen Sie Folgendes sicher: 
    - Server sind für die Dauer der Bewertung eingeschaltet.
    - Ausgehende Verbindungen am Port 443 sind zugelassen.
    - Für Hyper-V-Server ist dynamischer Arbeitsspeicher aktiviert. 
    
    Führen Sie die erneute Berechnung der Bewertung mit der Option „Neu berechnen“ durch, um die neuesten Änderungen an der Zuverlässigkeitsstufe widerzuspiegeln.

- Einige Server wurden während des Zeitraums, für den die Bewertung berechnet wird, erstellt. Beispiel: Sie haben eine Bewertung für den Leistungsverlauf im letzten Monat erstellt, einige Server wurden jedoch erst vor einer Woche erstellt. In diesem Fall stehen für die gesamte Dauer keine Leistungsdaten für die neuen Server zur Verfügung, und die Zuverlässigkeitsstufe wäre gering.

> [!NOTE]
> Bei einer Zuverlässigkeitsstufe von weniger als fünf Sternen wird empfohlen, mindestens einen Tag zu warten, damit die Appliance ein Profil der Umgebung erstellen kann. Führen Sie dann eine Neuberechnung der Bewertung durch. Anderenfalls ist die leistungsbasierte Größenanpassung möglicherweise unzuverlässig. In diesem Fall wird empfohlen, die Bewertung auf die Größenanpassung „Wie lokal“ zu ändern.

## <a name="calculate-monthly-costs"></a>Berechnen der monatlichen Kosten

Nachdem die Größenempfehlungen ermittelt wurden, berechnet eine Azure VM-Bewertung in Azure Migrate die Compute- und Speicherkosten nach der Migration.

### <a name="compute-cost"></a>Computekosten
Azure Migrate berechnet anhand der empfohlenen Azure-VM-Größe und mithilfe der Azure-Abrechnungs-API die monatlichen Kosten für den Server.

Bei dieser Berechnung wird Folgendes berücksichtigt:
- Betriebssystem
- Software Assurance
- Reservierte Instanzen
- VM-Betriebszeit
- Standort
- Währungseinstellungen

Die Bewertung fasst die Kosten aller Server zusammen, um die monatlichen Gesamtcomputekosten zu berechnen.

### <a name="storage-cost"></a>Speicherkosten
Die monatlichen Speicherkosten für einen Server werden berechnet, indem die monatlichen Kosten aller an den Server angefügten Datenträger zusammengefasst werden.

#### <a name="standard-and-premium-disk"></a>Standard- und Premium-Datenträger
Die Kosten für Standard- oder Premium-Datenträger werden basierend auf der ausgewählten/empfohlenen Datenträgergröße berechnet. 

#### <a name="ultra-disk"></a>Ultra-Datenträger 

Die Kosten für Ultra-Datenträger werden basierend auf der bereitgestellten Größe, den bereitgestellten IOPS und dem bereitgestellten Durchsatz berechnet. [Weitere Informationen](https://azure.microsoft.com/pricing/details/managed-disks/)

Die Kosten werden mithilfe der folgenden Logik berechnet: 
- Die Kosten für die Datenträgergröße werden berechnet, indem die Größe des bereitgestellten Datenträgers mit dem Stundenpreis der Datenträgerkapazität multipliziert wird.
- Die Kosten für bereitgestellte IOPS werden berechnet, indem bereitgestellte IOPS mit dem stündlich bereitgestellten IOPS-Preis multipliziert werden.
- Die Kosten für bereitgestellten Durchsatz werden berechnet, indem der bereitgestellte Durchsatz mit dem stündlich bereitgestellten Durchsatzpreis multipliziert wird.
- Die Reservierungsgebühr für Ultra-Datenträger-VMs wird nicht in den Gesamtkosten hinzugefügt. [Weitere Informationen](https://azure.microsoft.com/pricing/details/managed-disks/)

Die Bewertung berechnet die gesamten monatlichen Speicherkosten durch Aggregieren der Speicherkosten aller Server. Derzeit werden bei der Berechnung keine Angebote berücksichtigt, die in den Bewertungseinstellungen angegeben sind.

Kosten werden in der Währung angezeigt, die in den Bewertungseinstellungen festgelegt ist.

## <a name="next-steps"></a>Nächste Schritte

[Bewährte Methoden für die Erstellung von Bewertungen](best-practices-assessment.md) 

- Erfahren Sie mehr über das Ausführen von Bewertungen für [physische Server](./tutorial-discover-physical.md) sowie für Server, die in [VMware](./tutorial-discover-vmware.md)- und [Hyper-V](./tutorial-discover-hyper-v.md)-Umgebungen ausgeführt werden.
- Erfahren Sie etwas über die Bewertung von [mithilfe einer CSV-Datei importierten](./tutorial-discover-import.md) Servern.
- Erfahren Sie etwas über das Einrichten der [Visualisierung von Abhängigkeiten](concepts-dependency-visualization.md).