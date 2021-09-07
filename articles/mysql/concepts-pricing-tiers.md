---
title: Tarife – Azure Database for MySQL
description: Erfahren Sie mehr über die verschiedenen Tarife für Azure Database for MySQL einschließlich Computegeneration, Speichertypen, Speichergröße, V-Kerne, Arbeitsspeicher und Aufbewahrungszeiträume für Sicherungen.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: c9e726e53d40ac90aec6bbbaaf41399698b11209
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340149"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Azure Database for MySQL – Tarife

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

Sie können eine Azure Database for MySQL-Serverinstanz basierend auf drei unterschiedlichen Tarifen erstellen: „Basic“, „Allgemein“ und „Arbeitsspeicheroptimiert“. Die Tarife unterscheiden sich anhand der bereitstellbaren Menge an Rechenleistung in V-Kernen, des Arbeitsspeichers pro V-Kern und der zum Speichern der Daten verwendeten Speichertechnologie. Alle Ressourcen werden auf der MySQL-Serverebene bereitgestellt. Ein Server kann über eine oder mehrere Datenbanken verfügen.

| attribute   | **Grundlegend** | **Allgemeiner Zweck** | **Arbeitsspeicheroptimiert** |
|:---|:----------|:--------------------|:---------------------|
| Computegeneration | Gen 4, Gen 5 | Gen 4, Gen 5 | Gen 5 |
| V-Kerne | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Arbeitsspeicher pro V-Kern | 2 GB | 5 GB | 10 GB |
| Speichergröße | 5 GB bis 1 TB | 5 GB bis 16 TB | 5 GB bis 16 TB |
| Aufbewahrungszeitraum von Datenbanksicherungen | 7 bis 35 Tage | 7 bis 35 Tage | 7 bis 35 Tage |

Um einen Tarif auszuwählen, verwenden Sie die folgende Tabelle als Ausgangspunkt.

| Tarif | Zielworkloads |
|:-------------|:-----------------|
| Basic | Workloads mit geringen Anforderungen an Rechen- und E/A-Leistung. Beispiele hierfür sind Server, die für die Entwicklung, für Tests oder für kleine, selten verwendete Anwendungen verwendet werden. |
| Universell | Geeignet für die meisten Unternehmensworkloads mit gängigen Compute- und Arbeitsspeicheranforderungen und skalierbarem E/A-Durchsatz. Hierzu zählen beispielsweise zum Hosten von Web- und mobilen Apps verwendete Server und andere Unternehmensanwendungen.|
| Arbeitsspeicheroptimiert | Geeignet für Hochleistungs-Datenbankworkloads, für die In-Memory-Leistung erforderlich ist, um eine schnellere Transaktionsverarbeitung und höhere Parallelität zu erzielen. Hierzu zählen beispielsweise Server für die Verarbeitung von Echtzeitdaten und leistungsstarke Transaktions- oder Analyse-Apps.|

Nach der Servererstellung können Sie die Anzahl von virtuellen Kernen, die Hardwaregeneration und den Tarif (mit Ausnahme eines Wechsels zu oder von Basic) innerhalb weniger Sekunden ändern. Außerdem haben Sie die Möglichkeit, die Speichermenge einzeln zu erhöhen und den Aufbewahrungszeitraum für Sicherungen zu erhöhen oder zu verringern, ohne dass bei der Anwendung Ausfallzeiten auftreten. Der Sicherungsspeichertyp kann nach der Servererstellung nicht mehr geändert werden. Weitere Informationen finden Sie im Abschnitt [Skalieren von Ressourcen](#scale-resources).

## <a name="compute-generations-and-vcores"></a>Computegenerationen und V-Kerne

Computeressourcen werden in Form von virtuellen Kernen bereitgestellt und repräsentieren die logische CPU der zugrunde liegenden Hardware. China, Osten 1, China, Norden 1, US DoD, Mitte und US DoD, Osten nutzen logische CPUs der 4. Generation, die auf Intel-Prozessoren vom Typ E5-2673 v3 (Haswell) mit 2,4 GHz basieren. Alle anderen Regionen nutzen logische CPUs der 5. Generation, die auf Intel-Prozessoren vom Typ E5-2673 v4 (Broadwell) mit 2,3 GHz basieren.

## <a name="storage"></a>Storage

Der von Ihnen bereitgestellte Speicher definiert die Speicherkapazität, die für Ihren Azure Database for MySQL-Server zur Verfügung steht. Der Speicher wird für die Datenbankdateien, temporären Dateien, Transaktionsprotokolle und MySQL-Serverprotokolle verwendet. Außerdem wird durch die Gesamtmenge an bereitgestelltem Speicher die E/A-Kapazität Ihres Servers definiert.

Azure Database for MySQL Single Server unterstützt die folgenden Back-End-Speichertypen für die Server. 

| Speichertyp   | Basic | Universell V1 | Universell V2 |
|:---|:----------|:--------------------|:---------------------|
| Speichergröße | 5 GB bis 1 TB | 5 GB bis 4 TB | 5 GB bis 16 TB |
| Speicherinkrementgröße | 1 GB | 1 GB | 1 GB |
| IOPS | Variable |3 IOPS/GB<br/>Min. 100 IOPS<br/>Max. 6.000 IOPS | 3 IOPS/GB<br/>Min. 100 IOPS<br/>Max. 20.000 IOPS |

>[!NOTE]
> Speicher im Tarif „Basic“ bietet keine IOPS-Garantie. Für Speicher im Tarif „Universell“ wird der IOPS-Wert gegenüber der bereitgestellten Speichergröße in einem Verhältnis von 3:1 skaliert.

### <a name="basic-storage"></a>Basic-Speicher 
Basic-Speicher ist der Back-End-Speicher, der Server im Tarif „Basic“ unterstützt. Basic-Speicher nutzt Azure-Standardspeicher im Back-End, bei dem die bereitgestellte IOPS-Menge nicht garantiert wird und die Latenz variabel ist. Der Basic-Tarif eignet sich am besten für Workloads, die wenig Rechenleistung, niedrige Kosten und E/A-Leistung für die Entwicklung oder kleine, selten genutzte Anwendungen erfordern.

### <a name="general-purpose-storage"></a>Universeller Speicher 
Universeller Speicher ist der Back-End-Speicher, der Server im Tarif „Universell“ und „Arbeitsspeicheroptimiert“ unterstützt. Für Speicher im Tarif „Universell“ wird der IOPS-Wert gegenüber der bereitgestellten Speichergröße in einem Verhältnis von 3:1 skaliert. Es gibt zwei Generationen von universellem Speicher, wie unten beschrieben:

#### <a name="general-purpose-storage-v1-supports-up-to-4-tb"></a>Speichertyp „Universell V1“ (unterstützt bis zu 4 TB)
Speicher vom Typ „Universell V1“ basiert auf Legacy-Speichertechnologie, die bis zu 4 TB Speicher und 6.000 IOPs pro Server unterstützen kann. Speichertyp „Universell V1“ ist so optimiert, dass Arbeitsspeicher von den Computeknoten zur Ausführung der MySQL-Engine für die lokale Zwischenspeicherung und Sicherungen genutzt wird. Der Sicherungsprozess für den Speichertyp „Universell V1“ liest die Daten- und Protokolldateien im Arbeitsspeicher der Computeknoten und kopiert die Informationen für eine Aufbewahrung von bis zu 35 Tage in den Zielsicherungsspeicher. Aus diesem Grund liegt der Arbeitsspeicher- und E/A-Verbrauch des Speichers während der Durchführung von Sicherungen relativ hoch. 

Alle Azure-Regionen unterstützen den Speichertyp „Universell V1“.

Für Speicher im Tarif „Universell“ oder „Arbeitsspeicheroptimiert“ mit dem Speichertyp „Universell V1“ sollte Folgendes berücksichtigt werden:

*   Planen Sie für den Compute-SKU-Tarif 10–30 % zusätzlichen Speicher für Speichercaching und Sicherungspuffer ein. 
*   Stellen Sie 10 % mehr IOPS als für die Datenbankworkload benötigt bereit, um E/A-Anforderungen für Sicherungen zu berücksichtigen. 
*   Alternativ können Sie zu einem Speicher vom Typ „Universell V2“ migrieren (siehe Beschreibung unten), der bis zu 16 TB unterstützt, wenn die zugrunde liegende Speicherinfrastruktur in Ihren bevorzugten Azure-Regionen verfügbar ist. 

#### <a name="general-purpose-storage-v2-supports-up-to-16-tb-storage"></a>Speichertyp „Universell V2“ (unterstützt bis zu 16 TB Speicher)
Speicher vom Typ „Universell V2“ basiert auf der neuesten Speicherinfrastruktur, die bis zu 16 TB und 20.000 IOPS unterstützen kann. In einer Teilmenge der Azure-Regionen, in denen die Infrastruktur verfügbar ist, entsprechen alle neu bereitgestellten Server standardmäßig dem Speichertyp „Universell V2“. Speicher vom Typ „Universell V2“ verbraucht keinen Arbeitsspeicher von den MySQL-Computeknoten und bietet im Vergleich zum Speichertyp „Universell V1“ besser vorhersagbare E/A-Latenzzeiten. Sicherungen für Server mit dem Speichertyp „Universell V2“ basieren auf Momentaufnahmen und verursachen keinen zusätzlichen E/A-Overhead. Es ist zu erwarten, dass die Leistung eines MySQL-Servers für den Speichertyp „Universell V2“ im Vergleich zu „Universell V1“ bei gleichem Speicherplatz und gleicher IOPS-Menge höher ist. Für universellen Speicher, der bis zu 16 TB unterstützt, fallen keine zusätzlichen Kosten an. Unterstützung bei der Migration auf einen 16-TB-Speicher erhalten Sie, indem Sie über das Azure-Portal ein Supportticket öffnen.

Der Speichertyp „Universell V2“ wird in den folgenden Azure-Regionen unterstützt: 

| Region | Verfügbarkeit des Speichertyps „Universell V2“ | 
| --- | --- | 
| Australien (Osten) | :heavy_check_mark: | 
| Australien, Südosten | :heavy_check_mark: | 
| Brasilien Süd | :heavy_check_mark: | 
| Kanada, Mitte | :heavy_check_mark: |
| Kanada, Osten | :heavy_check_mark: |
| USA (Mitte) | :heavy_check_mark: | 
| East US | :heavy_check_mark: | 
| USA (Ost) 2 | :heavy_check_mark: |
| Asien, Osten | :heavy_check_mark: | 
| Japan, Osten | :heavy_check_mark: | 
| Japan, Westen | :heavy_check_mark: | 
| Korea, Mitte | :heavy_check_mark: |
| Korea, Süden | :heavy_check_mark: |
| Nordeuropa | :heavy_check_mark: | 
| USA Nord Mitte | :heavy_check_mark: | 
| USA Süd Mitte | :heavy_check_mark: | 
| Asien, Südosten | :heavy_check_mark: | 
| UK, Süden | :heavy_check_mark: | 
| UK, Westen | :heavy_check_mark: | 
| USA, Westen-Mitte | :heavy_check_mark: | 
| USA (Westen) | :heavy_check_mark: | 
| USA, Westen 2 | :heavy_check_mark: | 
| Europa, Westen | :heavy_check_mark: | 
| Indien, Mitte* | :heavy_check_mark: | 
| Frankreich, Mitte* | :heavy_check_mark: | 
| VAE, Norden* | :heavy_check_mark: | 
| Südafrika, Norden* | :heavy_check_mark: |

> [!Note] 
> *Regionen, in denen sich der Speichertyp „Universell V2“ für Azure Database for MySQL in der öffentlichen Vorschau befindet <br /> *Für diese Azure-Regionen haben Sie die Möglichkeit, Server mit dem Speichertyp „Universell V1“ und „Universell V2“ zu erstellen. Für Server, die in der öffentlichen Vorschauversion des Speichertyps „Universell V2“ erstellt wurden, gelten die folgenden Einschränkungen: <br /> 
> * Die georedundante Sicherung wird nicht unterstützt.<br /> 
> * Der Replikatserver muss sich in einer der Regionen befinden, die den Speichertyp „Universell V2“ unterstützen. <br /> 
    

### <a name="how-can-i-determine-which-storage-type-my-server-is-running-on"></a>Wie kann ich bestimmen, mit welchem Speichertyp mein Server ausgeführt wird?

Der Speichertyp Ihres Servers ist im Portal auf dem Blatt „Tarif“ angegeben. 
* Wenn der Server mit der SKU „Basic“ bereitgestellt wird, lautet der Speichertyp „Basic“.
* Wenn der Server mit der SKU „Universell“ oder „Arbeitsspeicheroptimiert“ bereitgestellt wurde, lautet der Speichertyp „Universell“.
   *  Wenn auf Ihrem Speicher maximal 4 TB an Speicher bereitgestellt werden können, lautet der Speichertyp „Universell V1“.
   *  Wenn auf Ihrem Speicher bis zu 16 TB an Speicher bereitgestellt werden können, lautet der Speichertyp „Universell V2“.

### <a name="can-i-move-from-general-purpose-storage-v1-to-general-purpose-storage-v2-if-yes-how-and-is-there-any-additional-cost"></a>Kann ich von Speichertyp „Universell V1“ zu „Universell V2“ wechseln? Falls ja, wie kann ich vorgehen, und entstehen hierbei zusätzliche Kosten?
Ja, die Migration von Speichertyp „Universell V1“ zu „Universell V2“ wird unterstützt, wenn die zugrunde liegende Speicherinfrastruktur in der Azure-Region des Quellservers verfügbar ist. Die Migration und der V2-Speicher sind ohne zusätzliche Kosten verfügbar.

### <a name="can-i-grow-storage-size-after-server-is-provisioned"></a>Kann ich den Server nach der Bereitstellung des Servers vergrößern?
Während und nach der Erstellung des Servers können Sie zusätzliche Speicherkapazität hinzufügen und dem System erlauben, den Speicher auf der Grundlage des Speicherbedarfs Ihrer Workload automatisch zu vergrößern. 

>[!IMPORTANT]
> Der Speicher kann nur zentral hochskaliert und nicht herunterskaliert werden.

### <a name="monitoring-io-consumption"></a>Überwachen des E/A-Verbrauchs
Sie können Ihren E/A-Verbrauch im Azure-Portal oder mit Azure CLI-Befehlen überwachen. Die relevanten zu überwachenden Metriken sind [Speicherlimit, Speicherprozentsatz, verwendeter Speicher und E/A-Prozentsatz](concepts-monitoring.md). Die Überwachungsmetriken für einen MySQL-Server mit dem Speichertyp „Universell V1“ melden den von der MySQL-Engine verbrauchten Arbeitsspeicher und die E/A-Auslastung, erfassen jedoch möglicherweise nicht den Arbeitsspeicher- und E/A-Verbrauch der Speicherebene, was eine Einschränkung darstellt.

### <a name="reaching-the-storage-limit"></a>Erreichen der Speicherbegrenzung

Server mit 100 GB oder weniger bereitgestelltem Speicher werden als schreibgeschützt gekennzeichnet, wenn der freie Speicher weniger als fünf Prozent der bereitgestellten Speichergröße beträgt. Server mit mehr als 100 GB bereitgestelltem Speicher werden als schreibgeschützt gekennzeichnet, wenn der freie Speicher weniger als 5 GB beträgt.

Wenn Sie also beispielsweise 110 GB Speicher bereitgestellt haben und die tatsächliche Auslastung 105 GB überschreitet, wird der Server als schreibgeschützt gekennzeichnet. Wenn Sie andererseits 5 GB Speicher bereitgestellt haben, wird der Server als schreibgeschützt gekennzeichnet, wenn der freie Speicher unter 256 MB sinkt.

Während der Dienst versucht, den Server als schreibgeschützt zu kennzeichnen, werden alle neuen Schreibtransaktionsanforderungen blockiert, und bestehende aktive Transaktionen werden weiterhin ausgeführt. Wenn der Server als schreibgeschützt festgelegt ist, führen alle nachfolgenden Schreibvorgänge und die Transaktionscommits zu einem Fehler. Leseabfragen werden weiterhin ununterbrochen fortgesetzt. Nachdem Sie den bereitgestellten Speicher erhöht haben, ist der Server für die erneute Annahme von Schreibtransaktionen bereit.

Sie sollten die automatische Speichervergrößerung aktivieren oder eine Benachrichtigung einrichten, damit Sie informiert werden, wenn sich der Serverspeicher dem Grenzwert nähert. So können Sie den schreibgeschützten Zustand vermeiden. Weitere Informationen finden Sie in der Dokumentation zum [Einrichten einer Benachrichtigung](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Automatische Speichervergrößerung

Die automatische Speichervergrößerung verhindert, dass der Server nicht mehr über genügend Speicherplatz verfügt und schreibgeschützt wird. Wenn die automatische Speichervergrößerung aktiviert ist, wird der Speicher automatisch ohne Beeinträchtigung der Workload vergrößert. Bei Servern mit 100 GB oder weniger bereitgestelltem Speicher wird die bereitgestellte Speichergröße um 5 GB erhöht, sobald der freie Speicher unter zehn Prozent des bereitgestellten Speichers sinkt. Bei Servern mit mehr als 100 GB bereitgestelltem Speicher wird die bereitgestellte Speichergröße um fünf Prozent erhöht, sobald der freie Speicherplatz unter 10 GB der bereitgestellten Speichergröße sinkt. Dabei gelten die maximalen, oben beschriebenen Speichergrenzwerte.

Wenn Sie also beispielsweise 1000 GB Speicher bereitgestellt haben und die tatsächliche Auslastung 990 GB überschreitet, wird die Speichergröße des Servers auf 1050 GB erhöht. Bei 10 GB bereitgestelltem Speicher wird die Speichergröße alternativ auf 15 GB erhöht, wenn weniger als 1 GB Speicher frei ist.

Beachten Sie, dass der Speicher nur zentral hochskaliert und nicht herunterskaliert werden kann.

## <a name="backup-storage"></a>Sicherungsspeicher 

Bei Azure Database for MySQL werden bis zu 100% Ihres bereitgestellten Serverspeichers ohne zusätzliche Kosten als Sicherungsspeicher hinzugefügt. Alle Sicherungsspeicher, die Sie über diesen Betrag hinaus verwenden, werden in GB pro Monat abgerechnet. Beispiel: Wenn Sie einen Server mit 250 GB bereitstellen, verfügen Sie über 250 GB an zusätzlichem Speicher, der kostenlos für Serversicherungen zur Verfügung steht. Speicher für Sicherungen, die die 250 GB überschreiten, wird gemäß dem [Preismodell](https://azure.microsoft.com/pricing/details/mysql/) abgerechnet. Informationen zu Faktoren, die sich auf die Sicherungsspeichernutzung sowie die Überwachung und Steuerung der Sicherungsspeicherkosten beziehen, finden Sie in der [Dokumentation zur Sicherung](concepts-backup.md).

## <a name="scale-resources"></a>Skalieren von Ressourcen

Nachdem Sie Ihren Server erstellt haben, können Sie die virtuellen Kerne, die Hardwaregeneration, den Tarif (mit Ausnahme eines Wechsels zu oder von Basic), die Speichermenge und den Aufbewahrungszeitraum für Sicherungen einzeln ändern. Der Sicherungsspeichertyp kann nach der Servererstellung nicht mehr geändert werden. Die Anzahl virtueller Kerne kann zentral hoch- oder herunterskaliert werden. Die Aufbewahrungsdauer für Sicherungen kann von 7 bis zu 35 Tagen zentral hoch- oder herunterskaliert werden. Die Speichergröße kann nur erhöht werden. Die Skalierung der Ressourcen kann entweder über das Portal oder per Azure CLI durchgeführt werden. Ein Beispiel für die Skalierung mit der Azure CLI finden Sie unter [Überwachen und Skalieren eines einzelnen MySQL-Servers mit der Azure CLI](scripts/sample-scale-server.md).

Beim Ändern der Anzahl von virtuellen Kernen, der Hardwaregeneration oder des Tarifs wird eine Kopie des ursprünglichen Servers mit der neuen Computezuteilung erstellt. Sobald der neue Server betriebsbereit ist und ausgeführt wird, werden die Verbindungen auf den neuen Server verschoben. Während des Moments, in dem das System den Wechsel zum neuen Server durchführt, können keine neuen Verbindungen hergestellt werden, und für alle Transaktionen ohne Commit erfolgt ein Rollback. Diese Ausfallzeit während der Skalierung kann ungefähr 60–120 Sekunden betragen. Die Ausfallzeit während der Skalierung hängt von der Wiederherstellungszeit der Datenbank ab. Es kann daher länger dauern, die Datenbank wieder online zu schalten, wenn während des Skalierungsvorgangs auf dem Server extrem viele Transaktionsaktivitäten auftreten. Um eine längere Neustartzeit zu vermeiden, empfiehlt es sich, Skalierungsvorgänge in Zeiten mit geringer Transaktionsaktivität auf dem Server auszuführen.

Das Skalieren des Speichers und das Ändern der Aufbewahrungsdauer für Sicherungen sind reine Onlinevorgänge. Es gibt keine Ausfallzeit, und Ihre Anwendung wird nicht beeinträchtigt. Da der IOPS-Wert in Abhängigkeit der Größe des bereitgestellten Speichers skaliert wird, können Sie die IOPS-Menge, die für Ihren Server verfügbar ist, durch das zentrale Hochskalieren des Speichers erhöhen.

## <a name="pricing"></a>Preise

Aktuelle Preisinformationen finden Sie auf der Seite [Azure-Datenbank für MySQL – Preise](https://azure.microsoft.com/pricing/details/mysql/). Informationen zu den Kosten der gewünschten Konfiguration können Sie im [Azure-Portal](https://portal.azure.com/#create/Microsoft.MySQLServer) anzeigen. Die monatlichen Kosten für die von Ihnen ausgewählten Optionen werden auf der Registerkarte **Tarif** angegeben. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie den Azure-Preisrechner verwenden, um einen geschätzten Preis zu erhalten. Wählen Sie auf der Website des [Azure-Preisrechners](https://azure.microsoft.com/pricing/calculator/) die Option **Elemente hinzufügen** aus, erweitern Sie die Kategorie **Datenbanken**, und wählen Sie **Azure Database for MySQL** aus, um die Optionen anzupassen.

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich, wie Sie [im Portal einen MySQL-Server erstellen](howto-create-manage-server-portal.md).
- Weitere Informationen zu [Dienstgrenzwerten](concepts-limits.md).
- Weitere Informationen zum [Aufskalieren mit Lesereplikaten](howto-read-replicas-portal.md).
