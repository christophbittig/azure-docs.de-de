---
title: Azure SQL-Bewertungen in Azure Migrate Discovery und Bewertungstool
description: Erfahren Sie mehr über Azure SQL-Bewertungen in Azure Migrate Discovery und Bewertungstool.
author: rashi-ms
ms.author: rajosh
ms.topic: conceptual
ms.date: 02/07/2021
ms.openlocfilehash: bba3965dc3f03bb0b42595ba6bdf5d8889b483ee
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130004291"
---
# <a name="assessment-overview-migrate-to-azure-sql"></a>Bewertungsübersicht (Migration zu Azure SQL)

Dieser Artikel bietet eine Übersicht über Bewertungen für die Migration von lokalen SQL Server-Instanzen aus einer VMware-Umgebung zu Azure SQL-Datenbanken oder Managed Instances mithilfe des [Azure Migrate-Tools zur Ermittlung und Bewertung](./migrate-services-overview.md#azure-migrate-discovery-and-assessment-tool).

## <a name="whats-an-assessment"></a>Was ist eine Bewertung?
Eine Bewertung mit dem Ermittlungs- und Bewertungstool ist eine Momentaufnahme der Daten, misst die Bereitschaft lokaler Server zur Migration zu Azure und schätzt die Auswirkungen ein.

## <a name="types-of-assessments"></a>Arten von Bewertungen

Sie können drei Arten von Bewertungen mit dem Azure Migrate-Tool zur Ermittlung und Bewertung erstellen.

**Bewertungstyp** | **Details**
--- | --- 
**Azure-VM** | Bewertungen zum Migrieren Ihrer lokalen Server zu virtuellen Azure-Computern. <br/><br/> Sie können Ihre lokalen Server in der [VMware](how-to-set-up-appliance-vmware.md)- und [Hyper-V](how-to-set-up-appliance-hyper-v.md)-Umgebung und [physischen Server](how-to-set-up-appliance-physical.md) für die Migration zu Azure-VMs mit diesem Bewertungstyp bewerten.
**Azure SQL** | Bewertungen zum Migrieren Ihrer lokalen SQL Server-Instanzen aus Ihrer VMware-Umgebung zu Azure SQL-Datenbank oder Azure SQL Managed Instance. <br/><br/> Wenn Ihre SQL Server-Instanzen auf einer Nicht-VMware-Plattform ausgeführt werden, können Sie die Bereitschaft mithilfe des [Datenmigrations-Assistenten](/sql/dma/dma-assess-sql-data-estate-to-sqldb) bewerten.
**Azure VMware Solution (AVS)** | Bewertungen zum Migrieren Ihrer lokalen Server zu [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). <br/><br/> Sie können Ihre lokalen [VMware-VMs](how-to-set-up-appliance-vmware.md) für die Migration zu Azure VMware Solution (AVS) bewerten, indem Sie diesen Bewertungstyp verwenden. [Weitere Informationen](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Wenn die Anzahl der Azure VM- oder AVS-Bewertungen im Ermittlungs- und Bewertungstool falsch ist, klicken Sie auf die Gesamtzahl der Bewertungen, um zu allen Bewertungen zu navigieren und die Azure VM- oder AVS-Bewertungen neu zu berechnen. Das Ermittlungs- und Bewertungstool zeigt dann die richtige Anzahl für diesen Bewertungstyp an. 

Eine Azure SQL-Bewertung bietet Kriterien für die Dimensionierung:

**Größenkriterien** | **Details** | **Daten**
--- | --- | ---
**Leistungsbasiert** | Bewertungen, die Empfehlungen auf der Grundlage der erfassten Leistungsdaten aussprechen | Die Azure SQL-Konfiguration basiert auf Leistungsdaten von SQL-Instanzen und-Datenbanken, darunter CPU-Auslastung, Speicherauslastung, IOPS (Daten- und Protokolldateien), Durchsatz und Latenz von E/A-Vorgängen.

## <a name="how-do-i-assess-my-on-premises-sql-servers"></a>Wie bewerte ich meine lokalen SQL Server-Instanzen?

Mithilfe der Konfigurations- und Auslastungsdaten, die von einer einfachen Azure Migrate-Appliance gesammelt werden, können Sie Ihre lokalen SQL Server-Instanzen bewerten. Die Appliance ermittelt lokale SQL Server-Instanzen und -Datenbanken und sendet die Konfigurations- und Leistungsdaten an Azure Migrate. [Weitere Informationen](how-to-set-up-appliance-vmware.md)

## <a name="how-do-i-assess-with-the-appliance"></a>Wie führe ich eine Bewertung mit der Appliance durch?
Wenn Sie eine Azure Migrate-Appliance bereitstellen, um lokale Server zu ermitteln, führen Sie die folgenden Schritte aus:
1.  Richten Sie Azure und Ihre lokale Umgebung so ein, dass Azure Migrate verwendet werden kann.
2.  Erstellen Sie für Ihre erste Bewertung ein Azure Migrate-Projekt, und fügen Sie ihm das Azure Migrate-Tool zur Ermittlung und Bewertung hinzu.
3.  Stellen Sie eine einfache Azure Migrate-Appliance bereit. Die Appliance ermittelt kontinuierlich lokale Server und sendet Konfigurations- und Leistungsdaten an Azure Migrate. Stellen Sie die Appliance als VM oder physischen Server bereit. Auf den Servern, die Sie bewerten möchten, müssen keine Komponenten installiert werden.

Wenn die Appliance mit der Ermittlung begonnen hat, können Sie die Server, die Sie bewerten möchten, einer Gruppe hinzufügen und eine Bewertung für diese Gruppe mit dem Bewertungstyp **Azure SQL** durchführen.

Befolgen Sie unser Tutorial zum Bewerten von [SQL Server-Instanzen](tutorial-assess-sql.md), um diese Schritte auszuprobieren.

## <a name="how-does-the-appliance-calculate-performance-data-for-sql-instances-and-databases"></a>Wie werden Leistungsdaten für SQL-Instanzen und -Datenbanken von der Appliance berechnet?

Die Appliance sammelt mit folgenden Schritten Leistungsdaten für Computeeinstellungen:
1. Die Appliance erfasst einen Echtzeit-Abtastpunkt. Für SQL Server-Instanzen wird ein Abtastpunkt alle 30 Sekunden erfasst.
2. Die Appliance aggregiert die Stichproben-Datenpunkte, die über 10 Minuten alle 30 Sekunden erfasst werden. Zum Erstellen des Datenpunkts wählt die Appliance den Spitzenwert aus allen Stichproben aus. Sie sendet Maximalwert, Mittelwert und Varianz für jeden Zähler an Azure.
3. Azure Migrate speichert alle im 10-minütigen Intervall erfassten Datenpunkte des letzten Monats.
4. Beim Erstellen einer Bewertung ermittelt Azure Migrate den geeigneten Datenpunkt, um die richtige Größe zu bestimmen. Die Ermittlung basiert auf dem Perzentilwert für den Leistungsverlauf und dem Perzentilwert der Nutzung.
    - Wenn der Leistungsverlauf beispielsweise auf eine Woche festgelegt ist und der Perzentilwert der Nutzung das 95. Perzentil ist, sortiert die Bewertung die 10-minütigen Abtastpunkte für die letzte Woche. in aufsteigender Reihenfolge und wählt das 95. Perzentil zum Bestimmen der richtigen Größe aus.
    - Mit dem Wert des 95. Perzentils wird sichergestellt, dass Ausreißer ignoriert werden. Diese können enthalten sein, wenn Sie das 99. Perzentil wählen.
    - Falls Sie die Spitzenauslastung für den Zeitraum wählen und auch Ausreißer berücksichtigen möchten, sollten Sie das 99. Perzentil als Perzentilwert der Nutzung wählen.
5. Dieser Wert wird mit dem Komfortfaktor multipliziert, um die effektiven Leistungs- und Auslastungsdaten für diese Metriken zu erhalten, die von der Appliance erfasst werden:
    - CPU-Auslastung (%)
    - Arbeitsspeicherauslastung (%)
    - E/A-Vorgänge/s beim Lesen und E/A-Vorgänge/s beim Schreiben (Daten- und Protokolldateien)
    - MB/s beim Lesen und MB/s beim Schreiben (Durchsatz)
    - Latenz von E/A-Vorgängen

## <a name="what-properties-are-used-to-create-and-customize-an-azure-sql-assessment"></a>Welche Eigenschaften werden zum Erstellen und Anpassen einer Azure SQL-Bewertung verwendet?

Die folgenden Eigenschaften sind in den Azure SQL-Bewertungseigenschaften enthalten:

**Eigenschaft** | **Details**
--- | ---
**Zielstandort** | Die Azure-Region, zu der die Migration durchgeführt werden soll. Empfehlungen zur Azure SQL-Konfiguration und zu den Kosten basieren auf dem von Ihnen angegebenen Standort.
**Typ der Zielbereitstellung** | Der Zielbereitstellungstyp, für den die Bewertung ausgeführt werden soll: <br/><br/> Wählen Sie die Option **Empfohlen** aus, wenn mit Azure Migrate die Bereitschaft Ihrer SQL-Server für die Migration zu Azure SQL MI und Azure SQL-Datenbank bewertet werden soll und zusätzlich die am besten geeignete Zielbereitstellungsoption, der Zieltarif, die Azure SQL-Konfiguration und monatliche Schätzungen empfohlen werden sollen.<br/><br/>Wählen Sie **Azure SQL-Datenbank** aus, falls Sie nur Ihre SQL Server-Instanzen für die Migration zu Azure SQL-Datenbank-Instanzen bewerten lassen und den Zieltarif, die Azure SQL DB-Konfiguration und die monatlichen Schätzungen überprüfen möchten.<br/><br/>Wählen Sie **Azure SQL MI** aus, falls Sie nur Ihre SQL Server-Instanzen für die Migration zu Azure SQL-Datenbank-Instanzen bewerten lassen und den Zieltarif, die Azure SQL MI-Konfiguration und die monatlichen Schätzungen überprüfen möchten.
**Reservierte Kapazität** | Gibt die reservierte Kapazität an, damit diese bei Kostenschätzungen in der Bewertung berücksichtigt wird.<br/><br/> Wenn Sie eine Option für reservierte Kapazität auswählen, können Sie nicht „Abzug (%)“ auswählen.
**Größenkriterien** | Diese Eigenschaft wird für die Dimensionierung der Azure SQL-Konfiguration verwendet. <br/><br/> Sie wird standardmäßig auf **Leistungsbasiert** festgelegt, was bedeutet, dass die Bewertung die Leistungsmetriken für die SQL Server-Instanzen und -Datenbanken erfasst, um Empfehlungen für eine optimale Dimensionierung für Tarif und Konfiguration von Azure SQL Managed Instance und/oder Azure SQL-Datenbank zu geben.
**Leistungsverlauf** | Der Leistungsverlauf gibt den Zeitraum für die Bewertung von Leistungsdaten an.
**Perzentilwert der Nutzung** | Der Perzentilwert der Nutzung gibt den für die Größenanpassung zu verwendenden Perzentilwert der Leistungsstichprobe an.
**Komfortfaktor** | Der bei der Bewertung verwendete Puffer. Er berücksichtigt Aspekte wie saisonale Nutzung, einen kurzen Leistungsverlauf und eine voraussichtliche Zunahme der zukünftigen Nutzung.<br/><br/> Beispiel: Für eine Instanz mit zehn Kernen und einer Auslastung von 20 % ergibt sich normalerweise eine Instanz mit zwei Kernen. Bei einem Komfortfaktor von 2,0 ist das Ergebnis dagegen eine Instanz mit vier Kernen.
**Angebot/Lizenzierungsprogramm** | Das [Azure-Angebot](https://azure.microsoft.com/support/legal/offer-details/), für das Sie registriert sind. Derzeit können Sie nur zwischen „Nutzungsbasierte Bezahlung“ und „Dev/Test Pay-As-You-Go“ wählen. Beachten Sie, dass Sie einen zusätzlichen Rabatt erzielen können, indem Sie zusätzlich zum Angebot „Nutzungsbasierte Bezahlung“ die reservierte Kapazität und den Azure-Hybridvorteil anwenden.
**Dienstebene** | Die am besten geeignete Dienstebenenoption, um Ihre geschäftlichen Anforderungen für die Migration zu Azure SQL-Datenbank und/oder Azure SQL Managed Instance zu erfüllen:<br/><br/>**Empfohlen**, wenn von Azure Migrate die am besten geeignete Dienstebene für Ihre Server empfohlen werden soll. Dies kann beispielsweise „Universell“ oder „Unternehmenskritisch“ sein. <br/><br/> **Universell**, wenn Sie eine Azure SQL-Konfiguration erzielen möchten, die für budgetorientierte Workloads ausgelegt ist. [Weitere Informationen](../azure-sql/database/service-tier-general-purpose.md) <br/><br/> **Unternehmenskritisch**, wenn Sie eine Azure SQL-Konfiguration erhalten möchten, die auf latenzarme Workloads mit hoher Resilienz gegenüber Fehlern und schnellen Failovern ausgelegt ist. [Weitere Informationen](../azure-sql/database/service-tier-business-critical.md)
**Währung** | Die Rechnungswährung für Ihr Konto.
**Rabatt (%)** | Abonnementspezifische Rabatte, die Sie zusätzlich zum Azure-Angebot erhalten. Die Standardeinstellung ist 0 %.
**Azure-Hybridvorteil** | Gibt an, ob Sie bereits über eine SQL Server-Lizenz verfügen. <br/><br/> Wenn dies der Fall ist und eine aktive Software Assurance-Abdeckung für SQL Server-Abonnements besteht, können Sie sich für den Azure-Hybridvorteil bewerben, sofern Sie eigene Lizenzen für Azure mitbringen.

Sehen Sie sich die [bewährten Methoden](best-practices-assessment.md) für die Erstellung einer Bewertung mit Azure Migrate an.

## <a name="calculate-readiness"></a>Berechnen der Bereitschaft

> [!NOTE]
Die Bewertung umfasst nur Datenbanken, die online sind. Bei Datenbank mit einem anderen Status werden Bereitschaft, Dimensionierung und Kostenberechnung ignoriert. Wenn Sie Datenbanken dieser Art bewerten möchten, ändern Sie den Status der Datenbank, warten Sie etwas, und berechnen Sie die Bewertung neu.

### <a name="azure-sql-readiness"></a>Azure SQL-Bereitschaft

Die Azure SQL-Bereitschaft für SQL-Instanzen und -Datenbanken basiert auf einer Prüfung von Features auf Kompatibilität mit Azure SQL-Datenbank und Azure SQL Managed Instance:
1. Bei der Azure SQL-Bewertung werden die Funktionen der SQL Server-Instanz berücksichtigt, die derzeit von den SQL Server-Quellworkloads (SQL-Agent-Aufträge, verbundene Server usw.) und den Schemas der Benutzerdatenbanken (Tabellen, Sichten, Trigger, gespeicherte Prozeduren usw.) verwendet werden, um Kompatibilitätsprobleme zu identifizieren.
1. Wenn keine Kompatibilitätsprobleme gefunden werden, wird der Zielbereitstellungstyp (Azure SQL-Datenbank oder Azure SQL Managed Instance) als **Bereit** markiert.
1. Wenn nicht kritische Kompatibilitätsprobleme auftreten, z. B. heruntergestufte oder nicht unterstützte Funktionen, die die Migration zu einem bestimmten Zielbereitstellungstyp nicht blockieren, erfolgt die Kennzeichnung **Bereit** (mit Hyperlink und blauem Informationssymbol versehen) mit **Warnungsdetails** und Anleitung zur empfohlenen Korrektur.
1. Wenn Kompatibilitätsprobleme vorliegen, die die Migration zu einem bestimmten Zielbereitstellungstyp blockieren können, erfolgt die Kennzeichnung **Nicht bereit** mit **Problemdetails** und Anleitung zur empfohlenen Korrektur.
    - Wenn sich in einer SQL-Instanz sogar eine Datenbank befindet, die für einen bestimmten Zielbereitstellungstyp nicht bereit ist, wird die Instanz für diesen Bereitstellungstyp als **Nicht bereit** markiert.
1. Wenn die Ermittlung noch läuft oder bei einer SQL-Instanz oder -Datenbank Ermittlungsprobleme auftreten, erfolgt die Bereitschaftskennzeichnung **Unbekannt**, da die Bewertung die Bereitschaft für diese SQL-Instanz nicht berechnen konnte.

### <a name="recommended-deployment-type"></a>Empfohlener Bereitstellungstyp

Wenn Sie den Zielbereitstellungstyp wie in den Azure SQL-Bewertungseigenschaften **Empfohlen** auswählen, empfiehlt Azure Migrate einen Azure SQL-Bereitstellungstyp, der mit Ihrer SQL-Instanz kompatibel ist. Eine Migration zu einem von Microsoft empfohlenen Ziel ist insgesamt weniger aufwändig. 

#### <a name="recommended-deployment-type-based-on-azure-sql-readiness"></a>Empfohlener Bereitstellungstyp basierend auf Azure SQL-Bereitschaft

 **Azure SQL DB-Bereitschaft** | **Azure SQL MI-Bereitschaft** | **Empfohlener Bereitstellungstyp** | **Azure SQL-Konfiguration und Kostenschätzungen berechnet?**
 --- | --- | --- | --- |
 Bereit | Bereit | Azure SQL-Datenbank oder <br/>Azure SQL MI  | Ja
 Bereit | Nicht bereit oder<br/> Unbekannt | Azure SQL-Datenbank | Ja
 Nicht bereit oder<br/>Unbekannt | Bereit | Azure SQL MI  | Ja
 Nicht bereit. | Nicht bereit. | Potenziell bereit für Azure-VM | Nein
 Nicht bereit oder<br/>Unbekannt | Nicht bereit oder<br/>Unbekannt | Unbekannt | Nein

> [!NOTE]
> Wenn der in den Bewertungseigenschaften **Empfohlene** Bereitstellungstyp ausgewählt ist und die SQL Server-Quellinstanz für die Azure SQL DB-Einzeldatenbank und die Azure SQL Managed Instance geeignet ist, empfiehlt die Bewertung eine bestimmte Option, die ihre Kosten optimiert und zu den Größen- und Leistungsgrenzen passt.

#### <a name="potentially-ready-for-azure-vm"></a>Potenziell bereit für Azure-VM

Wenn die SQL-Instanz nicht für Azure SQL-Datenbank und Azure SQL Managed Instance bereit ist, wird der empfohlene Bereitstellungstyp als *Potenziell bereit für Azure-VM* gekennzeichnet.
- Dem Benutzer wird empfohlen, in Azure Migrate eine Bewertung mit dem Bewertungstyp „Azure-VM“ zu erstellen, um zu ermitteln, ob der Server, auf dem die Instanz ausgeführt wird, stattdessen zu einem virtuellen Azure-Computer migriert werden kann. Beachten Sie dabei Folgendes:
    - Bei Bewertungen vom Typ „Azure-VM“ in Azure Migrate liegt das Hauptaugenmerk aktuell auf Lift & Shift-Migrationen, und die spezifischen Leistungsmetriken für die Ausführung von SQL-Instanzen und -Datenbanken auf dem virtuellen Azure-Computer werden nicht berücksichtigt. 
    - Wenn Sie eine Bewertung vom Typ „Azure-VM“ auf einem Server ausführen, gelten die Größenempfehlung und die Kostenschätzungen für alle Instanzen, die auf dem Server ausgeführt werden und mithilfe des Servermigrationstools zu einem virtuellen Azure-Computer migriert werden können. Lesen Sie vor der Migration die [Leistungsrichtlinien](../azure-sql/virtual-machines/windows/performance-guidelines-best-practices-checklist.md) für SQL Server auf virtuellen Azure-Computern.


## <a name="calculate-sizing"></a>Berechnen der Dimensionierung

### <a name="azure-sql-configuration"></a>Azure SQL-Konfiguration

Nachdem die Bewertung die Bereitschaft und den empfohlenen Azure SQL-Bereitstellungstyp ermittelt hat, berechnet sie eine bestimmte Dienstebene und Azure SQL-Konfiguration (SKU-Größe), die mit der Leistung der lokalen SQL-Instanz identisch sein oder sie überschreiten kann:
1. Während des Ermittlungsvorgangs erfasst Azure Migrate Konfiguration und Leistung der SQL-Instanz, einschließlich:
    - Virtuelle Kerne (zugeordnet) und CPU-Auslastung (%)
        - Die CPU-Auslastung für eine SQL-Instanz gibt an, zu wieviel Prozent die SQL Server-Instanz die zugeordnete CPU verwendet
        - Die CPU-Auslastung für eine Datenbank gibt an, zu wieviel Prozent die Datenbank auf der SQL Server-Instanz die zugeordnete CPU verwendet
    - Arbeitsspeicher (zugeordnet) und Arbeitsspeicherauslastung (%)
    - E/A-Vorgänge/s beim Lesen und E/A-Vorgänge/s beim Schreiben (Daten- und Protokolldateien)
        - E/A-Vorgänge/s beim Lesen und E/A-Vorgänge/s beim Schreiben auf SQL-Instanzebene werden durch Addieren der E/A-Vorgänge/s beim Lesen und E/A-Vorgänge/s beim Schreiben für alle in dieser Instanz ermittelten Datenbanken berechnet.
    - MB/s beim Lesen und MB/s beim Schreiben (Durchsatz)
    - Latenz von E/A-Vorgängen
    - Datenbankgröße und Datenbankdateiorganisationen insgesamt
        - Die Datenbankgröße wird berechnet, indem alle Daten- und Protokolldateien addiert werden.
1. Die Bewertung aggregiert alle Konfigurations- und Leistungsdaten und versucht, die beste Entsprechung für verschiedene Azure SQL-Dienstebenen und -Konfigurationen zu finden, und wählt unter Beachtung der Kostenoptimierung eine Konfiguration aus, die den Leistungsanforderungen der SQL-Instanz entsprechen oder sie überschreiten kann.

### <a name="confidence-ratings"></a>Zuverlässigkeitsstufen 
Jede Azure SQL-Bewertung ist einer Zuverlässigkeitsstufe zugeordnet. Die Bewertung reicht von einem Stern (niedrigste Zuverlässigkeit) bis fünf Sterne (höchste Zuverlässigkeit). Anhand der Zuverlässigkeitsstufe können Sie die Zuverlässigkeit der von Azure Migrate bereitgestellten Größenempfehlungen besser einschätzen.
- Die Zuverlässigkeitsstufe wird einer Bewertung zugewiesen. Sie basiert auf der Verfügbarkeit von Datenpunkten, die zum Berechnen der Bewertung erforderlich sind.
- Bei der leistungsbasierten Dimensionierung sammelt die Bewertung Leistungsdaten aller SQL-Instanzen und -Datenbanken, einschließlich:
    - CPU-Auslastung (%)
    - Arbeitsspeicherauslastung (%)
    - E/A-Vorgänge/s beim Lesen und E/A-Vorgänge/s beim Schreiben (Daten- und Protokolldateien)
    - MB/s beim Lesen und MB/s beim Schreiben (Durchsatz)
    - Latenz von E/A-Vorgängen
    
Steht eine dieser Nutzungsangaben nicht zur Verfügung, sind die Größenempfehlungen möglicherweise nicht zuverlässig.
Die nachfolgende Tabelle zeigt die Zuverlässigkeitsstufen einer Bewertung, die vom Prozentsatz der verfügbaren Datenpunkte abhängen:

**Verfügbarkeit von Datenpunkten** | **Zuverlässigkeitsstufe**
--- | ---
0 % bis 20 % | 1 Stern
21 % bis 40 % | 2 Sterne
41 % bis 60 % | 3 Sterne
61 % bis 80 % | 4 Sterne
81 % bis 100 % | 5 Sterne

#### <a name="low-confidence-ratings"></a>Niedrige Zuverlässigkeitsstufen
Einige mögliche Gründe für eine niedrige Zuverlässigkeitsstufe einer Bewertung:
- Sie haben für den Zeitraum, für den Sie die Bewertung erstellen, kein Profil der Umgebung erstellt. Wenn Sie die Bewertung z. B. mit einer auf einen Tag festgelegten Leistungsdauer erstellen, müssen Sie bis mindestens einen Tag nach dem Start der Ermittlung warten, bis alle Datenpunkte gesammelt sind.
- Die Bewertung kann die Leistungsdaten für einige oder alle Server im Bewertungszeitraum nicht erfassen. Für eine Bewertung mit hoher Konfidenz stellen Sie Folgendes sicher:
    - Server sind für die Dauer der Bewertung eingeschaltet.
    - Ausgehende Verbindungen am Port 443 sind zugelassen.
    - Der SQL-Agent in Azure Migrate hat den Azure Migrate-Verbindungsstatus „Verbunden“. (Überprüfen Sie auch den letzten Heartbeat.) 
    - Der Azure Migrate-Verbindungsstatus für alle SQL-Instanzen auf dem Blatt der ermittelten SQL-Instanz lautet „Verbunden“.

    Führen Sie die erneute Berechnung der Bewertung mit der Option „Neu berechnen“ durch, um die neuesten Änderungen an der Zuverlässigkeitsstufe widerzuspiegeln.
- Einige Datenbanken oder Instanzen wurden während des Zeitraums, für den die Bewertung berechnet wird, erstellt. Beispiel: Sie haben eine Bewertung für den Leistungsverlauf im letzten Monat erstellt, einige Datenbanken oder Instanzen wurden jedoch erst vor einer Woche erstellt. In diesem Fall stehen für die gesamte Dauer keine Leistungsdaten für die neuen Server zur Verfügung und die Zuverlässigkeitsstufe wäre gering.

> [!NOTE]
> Da Azure SQL-Bewertungen leistungsbasiert sind, wird bei einer Zuverlässigkeitsstufe von weniger als fünf Sternen empfohlen, mindestens einen Tag zu warten, damit die Appliance ein Profil der Umgebung erstellen kann. Führen Sie dann eine Neuberechnung der Bewertung durch. Anderenfalls ist die leistungsbasierte Größenanpassung möglicherweise unzuverlässig.

## <a name="calculate-monthly-costs"></a>Berechnen der monatlichen Kosten
Nach Abschluss der Empfehlungen zur Dimensionierung berechnet die Azure SQL-Bewertung mithilfe einer internen Preis-API die Compute- und Speicherkosten für die empfohlenen Azure SQL-Konfigurationen. Die Compute- und Speicherkosten aller Instanzen werden zusammengefasst, um die monatlichen Gesamtcomputekosten zu berechnen. 
### <a name="compute-cost"></a>Computekosten
- Zum Berechnen der Computekosten für eine Azure SQL-Konfiguration berücksichtigt die Bewertung die folgenden Eigenschaften:
    - Azure-Hybridvorteil für SQL-Lizenzen
    - Reservierte Kapazität
    - Azure-Zielspeicherort
    - Währung
    - Angebot/Lizenzierungsprogramm
    - Abzug (%)

### <a name="storage-cost"></a>Speicherkosten
- Die geschätzten Speicherkosten enthalten nur Datendateien und keine Protokolldateien. 
- Zum Berechnen der Speicherkosten für eine Azure SQL-Konfiguration berücksichtigt die Bewertung die folgenden Eigenschaften:
    - Azure-Zielspeicherort
    - Währung
    - Angebot/Lizenzierungsprogramm
    - Abzug (%)
- Kosten für Sicherungsspeicher sind nicht in der Bewertung enthalten.
- **Azure SQL-Datenbank**
    - In der Kostenschätzung werden mindestens 5 GB Speicherkosten hinzugefügt, und zusätzliche Speicherkosten werden in 1-GB-Schritten hinzugefügt. [Weitere Informationen](https://azure.microsoft.com/pricing/details/sql-database/single/)
- **Verwaltete Azure SQL-Datenbank-Instanz**
    - Für die ersten 32 GB Speicher pro Instanz und Monat werden keine Speicherkosten hinzugefügt, und zusätzliche Speicherkosten werden in 32-GB-Schritten hinzugefügt. [Weitere Informationen](https://azure.microsoft.com/pricing/details/azure-sql/sql-managed-instance/single/)
        
## <a name="next-steps"></a>Nächste Schritte
- [Bewährte Methoden für die Erstellung von Bewertungen](best-practices-assessment.md) 
- Weitere Informationen zum Ausführen einer [Azure SQL-Bewertung](how-to-create-azure-sql-assessment.md).
