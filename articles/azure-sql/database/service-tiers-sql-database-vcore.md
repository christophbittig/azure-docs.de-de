---
title: Kaufmodell für virtuelle Kerne
description: Mit dem Kaufmodell für virtuelle Kerne können Sie Compute- und Speicherressourcen einzeln skalieren, eine Leistung wie in Ihrer lokalen Umgebung erzielen und den Preis für die Azure SQL-Datenbank optimieren.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sashan, moslake
ms.date: 09/10/2021
ms.custom: references_regions, ignite-fall-2021
ms.openlocfilehash: fb168fad05acf84fd9b64f34c929176933a1fd2f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026030"
---
# <a name="vcore-purchase-model-overview---azure-sql-database"></a>Azure SQL-Datenbank: Übersicht über das Kaufmodell für virtuelle Kerne 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

In diesem Artikel wird das V-Kern-Kaufmodell für [Azure SQL-Datenbank](sql-database-paas-overview.md) erläutert. Weitere Informationen zur Wahl zwischen dem V-Kern- und DTU-Kaufmodell finden Sie unter [Entscheidung zwischen dem V-Kern- und DTU-Kaufmodell](purchasing-models.md).

Das von Azure SQL-Datenbank verwendete Kaufmodell für virtuelle Kerne (vCore) bietet gegenüber dem DTU-Kaufmodell mehrere Vorteile:

- Höhere Grenzwerte für Compute, Arbeitsspeicher, E/A und Speicher
- Kontrolle über die Hardwaregeneration, um besser auf Compute- und Arbeitsspeicheranforderungen der Workload reagieren zu können
- Rabatte für [Azure-Hybridvorteil (AHB)](../azure-hybrid-benefit.md).
- Mehr Transparenz bei den Hardwaredetails für das Computing zur besseren Planung von Migrationsvorgängen von lokalen Bereitstellungen
- [Die Preise für reservierte Instanzen](reserved-capacity-overview.md) sind nur für das V-Kern-Kaufmodell verfügbar. 

## <a name="service-tiers"></a>Dienstebenen

Als Optionen für die Dienstebene stehen im V-Kern-Kaufmodell „Universell“, „Unternehmenskritisch“ und „Hyperscale“ zur Verfügung. Die Dienstebene definiert ganz allgemein die Speicherarchitektur, die Speicherplatz- und E/A-Grenzwerte sowie Optionen für die Geschäftskontinuität im Zusammenhang mit der Verfügbarkeit und Notfallwiederherstellung.

|**Anwendungsfall**|**Allgemeiner Zweck**|**Unternehmenskritisch**|**Hyperscale**|
|---|---|---|---|
|Am besten geeignet für:|Die meisten geschäftlichen Workloads. Bietet budgetorientierte, ausgewogene und skalierbare Compute- und Speicheroptionen. |Bietet Geschäftsanwendungen die höchste Resilienz gegenüber Fehlern durch die Verwendung mehrerer isolierter Replikate sowie die höchste E/A-Leistung pro Datenbankreplikat.|Die meisten geschäftlichen Workloads mit hohen Anforderungen an skalierbaren Speicher und Leseskalierung.  Bietet eine höhere Ausfallsicherheit, da mehrere isolierte Datenbankreplikate konfiguriert werden können. |
|Storage|Verwendet Remotespeicher.<br/>**Bereitgestelltes SQL-Datenbank-Computing:**<br/>5 GB – 4 TB<br/>**Serverloses Computing**:<br/>5 GB bis 3 TB|Verwendet lokalen SSD-Speicher.<br/>**Bereitgestelltes SQL-Datenbank-Computing:**<br/>5 GB – 4 TB|Flexible automatische Speichervergrößerung nach Bedarf. Unterstützt bis zu 100 TB Speicher. Verwendet lokalen SSD-Speicher für den lokalen Pufferpoolcache und den lokalen Datenspeicher. Verwendet Azure-Remotespeicher als endgültigen langfristigen Datenspeicher. |
|IOPS und Durchsatz (ungefähr)|**SQL-Datenbank:** Weitere Informationen finden Sie in den Ressourcenlimits für [Einzeldatenbanken](resource-limits-vcore-single-databases.md) und [Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md).|Weitere Informationen finden Sie in den Ressourcenlimits für [Einzeldatenbanken](resource-limits-vcore-single-databases.md) und [Pools für elastische Datenbanken](resource-limits-vcore-elastic-pools.md).|Hyperscale ist eine mehrstufige Architektur mit Caching auf mehreren Ebenen. Die tatsächlichen Werte für IOPS und Durchsatz hängen vom Workload ab.|
|Verfügbarkeit|1 Replikat, keine Replikate mit Leseskalierung, <br/>zonenredundante Hochverfügbarkeit (High Availability, HA) (Vorschau)|3 Replikate, 1 [Replikat, Leseskalierung](read-scale-out.md),<br/>zonenredundante Hochverfügbarkeit (High Availability, HA)|Einzelnes Replikat mit Lese-/Schreibzugriff sowie bis zu vier [Replikate mit Leseskalierung](read-scale-out.md)|
|Backups|Eine Auswahl aus georedundantem, zonenredundantem\* oder lokal redundantem\* Sicherungsspeicher mit einer Aufbewahrungsdauer von 1 bis 35 Tagen (Standardeinstellung: 7 Tage)|Eine Auswahl aus georedundantem, zonenredundantem\* oder lokal redundantem\* Sicherungsspeicher mit einer Aufbewahrungsdauer von 1 bis 35 Tagen (Standardeinstellung: 7 Tage)|Eine Auswahl aus georedundantem, zonenredundantem\*\* oder lokal redundantem\*\* Sicherungsspeicher, Aufbewahrungsdauer: 7 Tage<p>Auf Momentaufnahmen basierende Sicherungen in Azure-Remotespeicher. Bei Wiederherstellungen werden zur schnellen Wiederherstellung Momentaufnahmen verwendet. Sicherungen werden sofort ausgeführt und haben keine Auswirkungen auf die E/A-Computeleistung. Wiederherstellungen sind schnell und nicht datenintensiv (dauern also nicht Stunden, sondern nur Minuten).|
|In-Memory|Nicht unterstützt|Unterstützt|Teilweise unterstützt. Speicheroptimierte Tabellentypen, Tabellenvariablen und nativ kompilierte Module werden unterstützt.|
|||

\* In der Vorschau

\*\* In der Vorschau; nur für neue Hyperscale-Datenbanken

### <a name="choosing-a-service-tier"></a>Auswählen einer Dienstebene

Weitere Informationen zum Auswählen einer Dienstebene für die jeweilige Workload finden Sie in den folgenden Artikeln:

- [Wann sollte die Dienstebene „Universell“ gewählt werden?](service-tier-general-purpose.md#when-to-choose-this-service-tier)
- [Wann sollte die Dienstebene „Unternehmenskritisch“ ausgewählt werden?](service-tier-business-critical.md#when-to-choose-this-service-tier)
- [Wann sollte die Dienstebene „Hyperscale“ ausgewählt werden?](service-tier-hyperscale.md#who-should-consider-the-hyperscale-service-tier)


## <a name="compute-tiers"></a>Computetarife

Als Optionen für den Computetarif stehen im V-Kern-Modell die bereitgestellten und serverlosen Computetarife zur Auswahl.


### <a name="provisioned-compute"></a>Bereitgestelltes Computing

Bereitgestellte Computetarife bieten eine bestimmte Menge an Computeressourcen, die unabhängig von der Workloadaktivität ständig bereitgestellt werden. Die Abrechnung erfolgt dann zu einem Festpreis pro Stunde für die Menge an bereitgestellten Computeressourcen.


### <a name="serverless-compute"></a>Serverloses Computing

Beim [serverlosen Computetarif](serverless-tier-overview.md) erfolgt auch eine automatische Skalierung der Computeressourcen basierend auf den Workloadaktivitäten, und die Abrechnung erfolgt nach Menge der verbrauchten Computeressourcen pro Sekunde.



## <a name="hardware-generations"></a>Hardwaregenerationen

Zu den Optionen für die Hardwaregeneration im V-Kern-Modell gehören Gen4/Gen5, M-Serie, Fsv2-Serie und DC-Serie. Die Hardwaregeneration definiert im Allgemeinen die Compute- und Arbeitsspeicherlimits sowie weitere Eigenschaften, die sich auf die Leistung der Workload auswirken.

### <a name="gen4gen5"></a>Gen4/Gen5

- Gen4/Gen5-Hardware bietet ausgeglichene Compute- und Arbeitsspeicherressourcen und eignet sich für die meisten Datenbankworkloads, die keine erhöhten Anforderungen an Arbeitsspeicher, virtuelle Kerne oder schnellere Einzel-V-Kerne stellen, wie sie von der Fsv2-Serie oder der M-Serie bereitgestellt werden.

Informationen zu den Regionen, in denen Gen4/Gen5 verfügbar ist, finden Sie unter [Verfügbarkeit von Gen4/Gen5](#gen4gen5-1).

### <a name="fsv2-series"></a>Fsv2-Serie

- Die Fsv2-Serie ist eine für Compute optimierte Hardwareoption, die eine niedrige CPU-Latenz und eine hohe Taktfrequenz für die meisten Workloads mit hohen CPU-Anforderungen bereitstellt.
- Abhängig von der Workload kann die Fsv2-Serie eine höhere CPU-Leistung pro virtuellem Kern erzielen als Gen5, und mit der Größe von 72 V-Kernen erzielen Sie mehr CPU-Leistung für weniger Kosten als mit 80 virtuellen Kernen bei Gen5. 
- Fsv2 bietet weniger Arbeitsspeicher und tempdb pro virtuellem Kern als andere Hardware, sodass für Workloads, für die diese Grenzwerte wichtig sind, stattdessen Gen5 oder die M-Serie in Erwägung gezogen werden sollte.  

Fsv2-series wird von der Dienstebene „Universell“ nicht unterstützt. Weitere Informationen zu Regionen, in denen die Fsv2-Serie verfügbar ist, finden Sie unter [Verfügbarkeit der Fsv2-Serie](#fsv2-series-1).

### <a name="m-series"></a>M-Serie

- Die M-Serie ist eine arbeitsspeicheroptimierte Hardwareoption für Workloads, die mehr Arbeitsspeicher und höhere Computegrenzwerte erfordert, als von Gen5 bereitgestellt werden.
- Die M-Serie bietet 29 GB pro V-Kern und bis zu 128 V-Kerne. Dadurch erhöht sich der Grenzwert für den Arbeitsspeicher im Vergleich zu Gen5 um das Achtfache auf fast 4 TB.

Die M-Serie wird nur auf der Dienstebene „Unternehmenskritisch“ unterstützt und unterstützt keine Zonenredundanz.  Weitere Informationen zu Regionen, in denen die M-Serie verfügbar ist, finden Sie unter [Verfügbarkeit der M-Serie](#m-series-1).

#### <a name="azure-offer-types-supported-by-m-series"></a>Von der M-Serie unterstützte Azure-Angebotstypen

Für den Zugriff auf die M-Serie muss das Abonnement ein kostenpflichtiger Angebotstyp sein, einschließlich nutzungsbasierter Bezahlung oder Enterprise Agreement (EA).  Eine vollständige Liste mit den von der M-Serie unterstützten Azure-Angebotstypen finden Sie unter [Aktuelle Angebote ohne Ausgabenlimits](https://azure.microsoft.com/support/legal/offer-details).

<!--
To enable M-series hardware for a subscription and region, a support request must be opened. The subscription must be a paid offer type including Pay-As-You-Go or Enterprise Agreement (EA).  If the support request is approved, then the selection and provisioning experience of M-series follows the same pattern as for other hardware generations. For regions where M-series is available, see [M-series availability](#m-series).
-->

### <a name="dc-series"></a>DC-Serie

- In Hardware der DC-Serie werden Intel-Prozessoren mit Software Guard Extensions-Technologie (Intel SGX) verwendet.
- Die DC-Serie ist für [Always Encrypted mit Secure Enclaves](/sql/relational-databases/security/encryption/always-encrypted-enclaves) erforderlich. Dies wird bei anderen Hardwarekonfigurationen nicht unterstützt.
- Die DC-Serie ist für Workloads konzipiert, durch die vertrauliche Daten verarbeitet werden und die Funktionen für die vertrauliche Abfrageverarbeitung erfordern, die von Always Encrypted mit Secure Enclaves bereitgestellt werden.
- Hardware der DC-Serie bietet ausgewogene Compute- und Speicherressourcen.

Die DC-Serie wird nur für bereitgestellte Computeressourcen (und nicht für „Serverlos“) unterstützt. Außerdem unterstützt sie keine Zonenredundanz. Die Regionen, in denen die DC-Serie verfügbar ist, finden Sie unter [Verfügbarkeit der DC-Serie](#dc-series-1).

#### <a name="azure-offer-types-supported-by-dc-series"></a>Von der DC-Serie unterstützte Azure-Angebotstypen

Für den Zugriff auf die DC-Serie muss das Abonnement ein kostenpflichtiger Angebotstyp sein und nutzungsbasierte Zahlung oder Enterprise Agreement (EA) einschließen.  Eine vollständige Liste mit den von der DC-Serie unterstützten Azure-Angebotstypen finden Sie unter [Aktuelle Angebote ohne Ausgabenlimits](https://azure.microsoft.com/support/legal/offer-details).

### <a name="compute-and-memory-specifications"></a>Spezifikationen zu Compute- und Arbeitsspeicherressourcen


|Hardwaregeneration  |Compute  |Arbeitsspeicher  |
|:---------|:---------|:---------|
|Gen4     |- Intel&reg; E5-2673 v3-Prozessoren (Haswell) mit 2,4 GHz<br>- Bereitstellung von bis zu 24 virtuellen Kernen (1 virtueller Kern = 1 physischer Kern)  |- 7 GB pro virtuellem Kern<br>- Bereitstellung von bis zu 168 GB|
|Gen5     |**Bereitgestelltes Computing**<br>– Intel&reg; E5-2673 v4-Prozessoren (Broadwell) mit 2,3 GHz, Intel&reg; SP-8160-Prozessoren (Skylake)\* und Intel&reg; 8272CL-Prozessoren (Cascade Lake) mit 2,5 GHz\*<br>- Bereitstellung von bis zu 80 virtuellen Kernen (1 virtueller Kern = 1 Hyperthread)<br><br>**Serverloses Computing**:<br>– Intel&reg; E5-2673 v4-Prozessoren (Broadwell) und Intel&reg; SP-8160-Prozessoren (Skylake)* mit 2,3 GHz<br>– Automatisches Hochskalieren auf bis zu 40 virtuelle Kerne (1 virtueller Kern = 1 Hyperthread)|**Bereitgestelltes Computing**<br>- 5,1 GB pro virtuellem Kern<br>- Bereitstellung von bis zu 408 GB<br><br>**Serverloses Computing**:<br>- Automatische Skalierung auf bis zu 24 GB pro V-Kern<br>– Automatisches Hochskalieren auf bis zu 120 GB|
|Fsv2-Serie     |– Intel&reg; 8168-Prozessoren (Skylake)<br>- Kontinuierliche Turbo-Taktfrequenz von 3,4 GHz für alle Kerne und maximale Turbo-Taktfrequenz für Einzelkerne von 3,7 GHz<br>– Bereitstellung von bis zu 72 virtuellen Kernen (1 virtueller Kern = 1 Hyperthread)|- 1,9 GB pro virtuellem Kern<br>– Bereitstellung von bis zu 136 GB|
|M-Serie     |– Intel&reg; E7-8890 v3-Prozessoren mit 2,5 GHz und Intel&reg; 8280M-Prozessoren mit 2,7 GHz (Cascade Lake)<br>– Bereitstellung von bis zu 128 virtuellen Kernen (1 virtueller Kern = 1 Hyperthread)|- 29 GB pro virtuellem Kern<br>– Bereitstellung von bis zu 3,7 GB|
|DC-Serie     | – Intel XEON E-2288G-Prozessoren<br>– Inklusive Intel Software Guard Extensions (Intel SGX)<br>– Bereitstellung von bis zu 8 virtuellen Kernen (1 virtueller Kern = 1 physischer Kern) | – 4,5 GB pro virtuellem Kern |

\* In der dynamischen Verwaltungssicht [sys. dm_user_db_resource_governance](/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) wird die Hardwaregenerierung für Datenbanken mit Intel&reg; SP-8160-Prozessoren (Skylake) als „Gen6“ und die für Datenbanken mit Intel&reg; 8272CL-Prozessoren (Cascade Lake) als „Gen7“ angezeigt. Ressourcenlimits für alle Gen5-Datenbanken sind unabhängig vom Prozessortyp (Broadwell, Skylake oder Cascade Lake) identisch.

Weitere Informationen zu Ressourcenlimits finden Sie unter [Ressourcenlimits für Singletons (V-Kern)](resource-limits-vcore-single-databases.md) oder [Ressourcenlimits für Pools für elastische Datenbanken (V-Kern)](resource-limits-vcore-elastic-pools.md).

### <a name="selecting-a-hardware-generation"></a>Auswählen einer Hardwaregeneration

Im Azure-Portal können Sie die Hardwaregeneration für eine Datenbank oder einen Pool in SQL-Datenbank zum Zeitpunkt der Erstellung auswählen. Sie können auch die Hardwaregeneration einer vorhandenen Datenbank oder eines Pools ändern.

**So wählen Sie eine Hardwaregeneration beim Erstellen einer SQL-Datenbank oder eines Pools aus**

Ausführliche Informationen finden Sie unter [Erstellen einer SQL-Datenbank-Instanz](single-database-create-quickstart.md).

Wählen Sie auf der Registerkarte **Grundlagen** im Abschnitt **Compute + Speicher** den Link **Datenbank konfigurieren** und dann den Link **Konfiguration ändern** aus:

:::image type="content" source="./media/service-tiers-vcore/configure-sql-database.png" alt-text="SQL-Datenbank konfigurieren" loc-scope="azure-portal":::

Wählen Sie die gewünschte Hardwaregeneration aus:

:::image type="content" source="./media/service-tiers-vcore/select-hardware.png" alt-text="Auswählen der Hardware für SQL-Datenbank" loc-scope="azure-portal":::

**So ändern Sie die Hardwaregeneration einer vorhandenen SQL-Datenbank oder eines Pools**

Wählen Sie für eine Datenbank auf der Seite „Übersicht“ den Link **Tarif** aus:

:::image type="content" source="./media/service-tiers-vcore/change-hardware.png" alt-text="Ändern der Hardware für SQL-Datenbank" loc-scope="azure-portal":::

Wählen Sie für einen Pool auf der Seite „Übersicht“ die Option **Konfigurieren** aus.

Führen Sie die Schritte zum Ändern der Konfiguration aus, und wählen Sie die Hardwaregeneration wie in den vorherigen Schritten beschrieben aus.

### <a name="hardware-availability"></a>Hardwareverfügbarkeit

#### <a name="gen4gen5"></a><a id="gen4gen5-1"></a> Gen4/Gen5

Die Gen4-Hardware [wird eingestellt](https://azure.microsoft.com/updates/gen-4-hardware-on-azure-sql-database-approaching-end-of-life-in-2020/) und steht für neue Bereitstellungen nicht mehr zur Verfügung. Alle neuen Datenbanken müssen auf späteren Hardwaregenerationen bereitgestellt werden.

Gen5 ist in allen öffentlichen Regionen weltweit verfügbar.

#### <a name="fsv2-series"></a>Fsv2-Serie

Die Fsv2-Serie ist in den folgenden Regionen verfügbar: „Australien, Mitte“, „Australien, Mitte 2“, „Australien, Osten“, „Australien, Südosten“, „Brasilien, Süden“, „Kanada, Mitte“, „Asien, Osten“, „USA, Osten“, „Frankreich, Mitte“, „Indien, Mitte“, „Südkorea, Mitte“, „Südkorea, Süden“, „Europa, Norden“, „Südafrika, Norden“, „Asien, Südosten“, „Vereinigtes Königreich, Süden“, „Vereinigtes Königreich, Westen“, „Europa, Westen“, „USA, Westen 2“.

#### <a name="m-series"></a>M-Serie

Die M-Serie ist in den folgenden Regionen verfügbar: „USA, Osten“, „Europa, Norden“, „Europa, Westen“, „USA, Westen 2“.
<!--
M-series may also have limited availability in additional regions. You can request a different region than listed here, but fulfillment in a different region may not be possible.

To enable M-series availability in a subscription, access must be requested by [filing a new support request](#create-a-support-request-to-enable-m-series).


##### Create a support request to enable M-series: 

1. Select **Help + support** in the portal.
2. Select **New support request**.

On the **Basics** page, provide the following:

1. For **Issue type**, select **Service and subscription limits (quotas)**.
2. For **Subscription** = select the subscription to enable M-series.
3. For **Quota type**, select **SQL database**.
4. Select **Next** to go to the **Details** page.

On the **Details** page, provide the following:

1. In the **PROBLEM DETAILS** section select the **Provide details** link. 
2. For **SQL Database quota type** select **M-series**.
3. For **Region**, select the region to enable M-series.
    For regions where M-series is available, see [M-series availability](#m-series).

Approved support requests are typically fulfilled within 5 business days.
-->

#### <a name="dc-series"></a>DC-Serie

Die DC-Serie ist in den folgenden Regionen verfügbar: Kanada, Mitte; Kanada, Osten; USA, Osten; Europa, Norden; Vereinigtes Königreich, Süden; Europa, Westen; USA, Westen.

Wenn Sie die DC-Serie in einer derzeit nicht unterstützten Region benötigen, [reichen Sie ein Supportticket ein](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Geben Sie unter **Grundlagen** die Folgendes an:

1. Wählen Sie als **Problemtyp** die Option **Technisch** aus.
1. Wählen Sie für **Diensttyp** den Eintrag **SQL-Datenbank** aus.
1. Wählen Sie für **Problemtyp** den Eintrag **Sicherheit, privat und Compliance** aus.
1. Wählen Sie für **Problemuntertyp** die Option **Always Encrypted** aus.

:::image type="content" source="./media/service-tiers-vcore/request-dc-series.png" alt-text="Beantragen der DC-Serie in einer neuen Region" loc-scope="azure-portal":::

## <a name="next-steps"></a>Nächste Schritte

- Für den Einstieg lesen Sie [Erstellen einer SQL-Datenbank-Instanz mithilfe des Azure-Portals](single-database-create-quickstart.md).
- Ausführliche Informationen zu Preisen finden Sie auf der [Preisseite für Azure SQL-Datenbank](https://azure.microsoft.com/pricing/details/sql-database/single/).
- Ausführliche Informationen zu den spezifischen Compute- und Speichergrößen finden Sie unter:
    - [Ressourcenlimits des auf virtuellen Kernen basierenden Kaufmodells für Azure SQL-Datenbank](resource-limits-vcore-single-databases.md)
    - [Ressourcenlimits des auf virtuellen Kernen basierenden Kaufmodells für gepoolte Azure SQL-Datenbank](resource-limits-vcore-elastic-pools.md)
