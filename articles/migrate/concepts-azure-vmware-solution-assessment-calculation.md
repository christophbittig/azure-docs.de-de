---
title: AVS-Bewertungsberechnungen in Azure Migrate | Microsoft-Dokumentation
description: Bietet eine Übersicht über AVS-Bewertungsberechnungen im Azure Migrate-Dienst.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 06/25/2020
ms.openlocfilehash: 5d917de831227dcfa7a9b5803f79a1cadbaf3112
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006031"
---
# <a name="assessment-overview-migrate-to-azure-vmware-solution"></a>Bewertungsübersicht (Migration zu Azure VMware Solution)

[Azure Migrate](migrate-services-overview.md) bietet einen zentralen Hub zum Nachverfolgen der Ermittlung, Bewertung und Migration von lokalen Apps und Workloads. Außerdem werden Ihre privaten und öffentlichen Cloudinstanzen in Azure nachverfolgt. Der Hub stellt Azure Migrate-Tools für die Bewertung und Migration sowie Angebote von unabhängigen Drittanbietern (Independent Software Vendors, ISVs) bereit.

Das Ermittlungs- und Bewertungstool in Azure Migrate bewertet lokale Server für die Migration zu virtuellen Azure IaaS-Computern und Azure VMware Solution (AVS). Dieser Artikel enthält Informationen dazu, wie AVS-Bewertungen (Azure VMware Solution) berechnet werden.

> [!NOTE]
> Eine AVS-Bewertung (Azure VMware Solution) kann nur für virtuelle VMware-Computer erstellt werden.

## <a name="types-of-assessments"></a>Arten von Bewertungen

Bewertungen, die Sie mit Azure Migrate erstellen, sind Momentaufnahmen von Daten zu einem gewissen Zeitpunkt. Es gibt zwei Arten von Bewertungen, die Sie mit der Azure

**Bewertungstyp** | **Details**
--- | --- 
**Azure-VM** | Bewertungen zum Migrieren Ihrer lokalen Server zu virtuellen Azure-Computern. Sie können Ihre lokalen Server in der [VMware](how-to-set-up-appliance-vmware.md)- und [Hyper-V](how-to-set-up-appliance-hyper-v.md)-Umgebung und [physischen Server](how-to-set-up-appliance-physical.md) für die Migration zu Azure-VMs mit diesem Bewertungstyp bewerten.
**Azure SQL** | Bewertungen zum Migrieren Ihrer lokalen SQL Server-Instanzen aus Ihrer VMware-Umgebung zu Azure SQL-Datenbank oder Azure SQL Managed Instance.
**Azure VMware Solution (AVS)** | Bewertungen zum Migrieren Ihrer lokalen Server zu [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). Sie können Ihre lokalen [VMware-VMs](how-to-set-up-appliance-vmware.md) für die Migration zu Azure VMware Solution (AVS) bewerten, indem Sie diesen Bewertungstyp verwenden. [Weitere Informationen](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Wenn die Anzahl der Azure VM- oder AVS-Bewertungen im Ermittlungs- und Bewertungstool falsch ist, klicken Sie auf die Gesamtzahl der Bewertungen, um zu allen Bewertungen zu navigieren und die Azure VM- oder AVS-Bewertungen neu zu berechnen. Das Ermittlungs- und Bewertungstool zeigt dann die richtige Anzahl für diesen Bewertungstyp an. 

Die AVS-Bewertung (Azure VMware Solution) bietet zwei Optionen für Dimensionierungskriterien:

| **Bewertung** | **Details** | **Daten** |
| - | - | - |
| **Leistungsbasiert** | Bewertungen basierend auf gesammelten Leistungsdaten von lokalen virtuellen Computern. | **Empfohlene Knotengröße**: Basierend auf den Daten zur CPU- und Arbeitsspeicherauslastung zusammen mit dem Knotentyp, dem Speichertyp und der FTT-Einstellung, die Sie für die Bewertung auswählen. |
| **Wie lokal** | Bewertungen basierend auf lokaler Größenanpassung | **Empfohlene Knotengröße**: Basierend auf der lokalen VM-Größe zusammen mit dem Knotentyp, dem Speichertyp und der FTT-Einstellung, die Sie für die Bewertung auswählen. |

## <a name="how-do-i-run-an-assessment"></a>Ausführen einer Bewertung

Eine Bewertung kann auf verschiedene Arten durchgeführt werden.

- Server können mithilfe von Servermetadaten bewertet werden, die von einer einfachen Azure Migrate-Appliance erfasst werden. Die Appliance ermittelt lokale Server. Anschließend werden Servermetadaten und Leistungsdaten an Azure Migrate gesendet. Dies ermöglicht eine höhere Genauigkeit.
- Server können mithilfe von Servermetadaten bewertet werden, die im CSV-Format (durch Trennzeichen getrennte Werte) importiert werden.

## <a name="how-do-i-assess-with-the-appliance"></a>Wie führe ich eine Bewertung mit der Appliance durch?

Wenn Sie eine Azure Migrate-Appliance bereitstellen, um lokale Server zu ermitteln, führen Sie die folgenden Schritte aus:

1. Richten Sie Azure und Ihre lokale Umgebung so ein, dass Azure Migrate verwendet werden kann.
2. Erstellen Sie für Ihre erste Bewertung ein Azure-Projekt, und fügen Sie dem Projekt das Ermittlungs- und Bewertungstool hinzu.
3. Stellen Sie eine einfache Azure Migrate-Appliance bereit. Die Appliance ermittelt kontinuierlich lokale Server und sendet Servermetadaten und Leistungsdaten an Azure Migrate. Stellen Sie die Appliance als virtuellen Computer bereit. Auf den Servern, die Sie bewerten möchten, müssen keine Komponenten installiert werden.

Wenn die Appliance mit der Serverermittlung begonnen hat, können Sie die Server, die Sie bewerten möchten, einer Gruppe hinzufügen und eine Bewertung für diese Gruppe mit dem Bewertungstyp **Azure VMware Solution (AVS)** durchführen.

Erstellen Sie Ihre erste AVS-Bewertung (Azure VMware Solution), indem Sie den Schritten [hier](how-to-create-azure-vmware-solution-assessment.md) folgen.

## <a name="how-do-i-assess-with-imported-data"></a>Wie führe ich eine Bewertung mit importierten Daten durch?

Wenn Sie Server mithilfe einer CSV-Datei bewerten, benötigen Sie keine Appliance. Führen Sie stattdessen die folgenden Schritte aus:

1. Richten Sie Azure für die Verwendung mit Azure Migrate ein.
2. Erstellen Sie für Ihre erste Bewertung ein Azure-Projekt, und fügen Sie dem Projekt das Ermittlungs- und Bewertungstool hinzu.
3. Laden Sie eine CSV-Vorlage herunter, und fügen Sie Serverdaten zu dieser Vorlage hinzu.
4. Importieren Sie die Vorlage in Azure Migrate.
5. Ermitteln Sie die mit dem Import hinzugefügten Server, fassen Sie sie in einer Gruppe zusammen, und führen Sie eine Bewertung für die Gruppe mit dem Bewertungstyp **Azure VMware Solution (AVS)** durch.

## <a name="what-data-does-the-appliance-collect"></a>Welche Daten werden von der Appliance erfasst?

Wenn Sie die Azure Migrate-Appliance für die Bewertung verwenden, erfahren Sie in den folgenden verlinkten Artikeln mehr über die Metadaten und Leistungsdaten, die für [VMware](migrate-appliance.md#collected-data---vmware) erfasst werden.

## <a name="how-does-the-appliance-calculate-performance-data"></a>Wie berechnet die Appliance Leistungsdaten?

Wenn Sie die Appliance für die Ermittlung verwenden, führt die Appliance folgende Schritte zum Erfassen der Leistungsdaten für Computeeinstellungen aus:

1. Die Appliance erfasst einen Echtzeit-Abtastpunkt.

   - **VMware-VMs:** Ein Abtastpunkt wird alle 20 Sekunden erfasst.
2. Die Appliance fasst die Abtastpunkte alle zehn Minuten zu einem einzigen Abtastpunkt zusammen. Zum Erstellen des Datenpunkts wählt die Appliance den Spitzenwert aus allen Stichproben aus. Anschließend sendet sie den Datenpunkt an Azure.
3. Azure Migrate speichert alle im zehnminütigen Intervall erfassten Datenpunkte des letzten Monats.
4. Beim Erstellen einer Bewertung wird der geeignete Datenpunkt ermittelt, um die richtige Größe zu bestimmen. Die Ermittlung basiert auf dem Perzentilwert für den *Leistungsverlauf* und dem *Perzentilwert der Nutzung*.

   - Wenn der Leistungsverlauf beispielsweise auf eine Woche festgelegt ist und der Perzentilwert der Nutzung das 95. Perzentil ist, sortiert die Bewertung die 10-minütigen Abtastpunkte für die letzte Woche. in aufsteigender Reihenfolge und wählt das 95. Perzentil zum Bestimmen der richtigen Größe aus.
   - Mit dem Wert des 95. Perzentils wird sichergestellt, dass Ausreißer ignoriert werden. Diese können enthalten sein, wenn Sie das 99. Perzentil wählen.
   - Falls Sie die Spitzenauslastung für den Zeitraum wählen und auch Ausreißer berücksichtigen möchten, sollten Sie das 99. Perzentil als Perzentilwert der Nutzung wählen.
5. Dieser Wert wird mit dem Komfortfaktor multipliziert, um die effektiven Leistungs- und Auslastungsdaten für diese Metriken zu erhalten, die von der Appliance erfasst werden:

   - CPU-Auslastung
   - RAM-Auslastung

Die folgenden Leistungsdaten werden gesammelt, aber nicht für Größenempfehlungen für AVS-Bewertungen verwendet:

- IOPS- und Durchsatzdaten für jeden an den virtuellen Computer angefügten Datenträger
- Informationen zur Netzwerk-E/A für die leistungsbasierte Größenanpassung für jeden Netzwerkadapter, der an einen virtuellen Computer angefügt ist

## <a name="how-are-avs-assessments-calculated"></a>Wie werden AVS-Bewertungen berechnet?

Bei der AVS-Bewertung werden mithilfe von Metadaten und Leistungsdaten der lokalen Server Bewertungen durchgeführt. Wenn Sie die Azure Migrate-Appliance bereitstellen, erfolgt die Bewertung anhand von Daten, die von der Appliance erfasst werden. Wenn Sie jedoch eine Bewertung anhand von Daten durchführen, die mithilfe einer CSV-Datei importiert wurden, stellen Sie die Metadaten für die Berechnung bereit.

Berechnungen erfolgen in den folgenden drei Phasen:

1. **Berechnen der AVS-Bereitschaft (Azure VMware Solution)** : Ermittelt, ob die lokalen virtuellen Computer für die Migration zu Azure VMware Solution (AVS) geeignet sind.
2. **Berechnen der Anzahl von AVS-Knoten und der knotenübergreifenden Auslastung**: Geschätzte Anzahl der AVS-Knoten, die für die Ausführung der VMware-VMs erforderlich sind, und prognostizierte CPU-, Arbeitsspeicher- und Speicherauslastung über alle Knoten hinweg.
3. **Geschätzte monatliche Kosten**: Die geschätzten monatlichen Kosten für alle AVS-Knoten (Azure VMware Solution), auf denen die lokalen virtuellen Computer ausgeführt werden.

Berechnungen erfolgen in der obigen Reihenfolge. Ein Server gelangt erst dann in die nächste Phase, wenn die vorherige Phase abgeschlossen wurde. Wenn ein Server z. B. die Phase zur Bewertung der AVS-Bereitschaft nicht besteht, wird er als nicht geeignet für Azure gekennzeichnet. Für diesen Server werden keine Berechnungen zur Größe und zu den Kosten durchgeführt.

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Was ist eine Bewertung vom Typ „Azure VMware Solution (AVS)“?

Eine AVS-Bewertung umfasst Folgendes:

| **Eigenschaft** | **Details** |
| - | - |
| **Zielstandort** | Gibt den Standort der privaten AVS-Cloud an, zu der Sie migrieren möchten. |
| **Speichertyp** | Gibt die Speicher-Engine an, die in AVS verwendet werden soll. AVS unterstützt derzeit nur vSAN als Standardspeichertyp, jedoch sind für die Zukunft weitere Speicheroptionen geplant. |
| **Reservierte Instanzen (RI)** | Mit dieser Eigenschaft können Sie reservierte Instanzen in AVS (sofern erworben) und ihre Gültigkeitsdauer angeben. Wird zum Berechnen der Kosten verwendet. Dies ist derzeit deaktiviert und standardmäßig auf *Keine reservierten Instanzen* festgelegt. [Azure VMware Solution unterstützt reservierte Instanzen](../azure-vmware/reserved-instance.md), und in Bewertungen wird diese Eigenschaft in Kürze aktiviert.|
| **Knotentyp** | Gibt den in Azure zu verwendenden [AVS-Knotentyp](../azure-vmware/concepts-private-clouds-clusters.md) an. Der Standardknotentyp ist AV36. In Zukunft sind möglicherweise weitere Knotentypen verfügbar.  Azure Migrate empfiehlt eine erforderliche Anzahl von Knoten für die VMs, die zu AVS migriert werden sollen. |
| **FTT-Einstellung, RAID-Ebene** | Gibt die gültigen Kombinationen zu tolerierender Fehler und RAID-Ebenen an. Die ausgewählte FTT-Option in Kombination mit der RAID-Ebene und der lokalen VM-Datenträgeranforderung bestimmt den gesamten in AVS erforderlichen vSAN-Speicher. Der gesamte verfügbare Speicher nach Berechnungen umfasst auch a) für Verwaltungsobjekte, z. B. vCenter, reservierten Speicherplatz und b) 25 % erforderlichen Schlupfspeicher für vSAN-Vorgänge. |
| **Größenkriterium** | Legt die Kriterien zur Bestimmung von Arbeitsspeicher-, CPU- und Speicheranforderungen für AVS-Knoten fest. Sie können für die Dimensionierung *Leistungsbasiert* oder *Wie lokal* auswählen, ohne den Leistungsverlauf zu berücksichtigen. Wählen Sie „Wie lokal“ aus, wenn Sie per Lift & Shift migrieren möchten. Wählen Sie „Leistungsbasiert“ aus, wenn die Dimensionierung auf der Nutzung basieren soll. |
| **Leistungsverlauf** | Legt die zur Bewertung der Leistungsdaten der Server zu berücksichtigende Dauer fest. Diese Eigenschaft ist nur anwendbar, wenn als Dimensionierungskriterium *Leistungsbasiert* festgelegt ist. |
| **Perzentilwert der Nutzung** | Gibt den für die Größenanpassung zu berücksichtigenden Perzentilwert des festgelegten Leistungsbeispiels. Diese Eigenschaft ist nur anwendbar, wenn eine leistungsbasierte Größenanpassung ausgewählt wurde. |
| **Komfortfaktor** | Bei Azure Migrate wird während der Bewertung ein Puffer (Komfortfaktor) berücksichtigt. Dieser Puffer wird zusätzlich zu den Daten zur Servernutzung für VMs (CPU, Arbeitsspeicher und Datenträger) angewendet. Beim Komfortfaktor geht es um Bereiche wie saisonale Nutzung, kurzer Leistungsverlauf und voraussichtliche zukünftige Zunahme der Nutzung. Beispiel: Für einen virtuellen Computer mit zehn Kernen und einer Auslastung von 20 Prozent ergibt sich normalerweise ein virtueller Computer mit zwei Kernen. Bei einem Komfortfaktor von 2.0x ist das Ergebnis dagegen eine VM mit vier Kernen. |
| **Angebot** | Zeigt das [Azure-Angebot](https://azure.microsoft.com/support/legal/offer-details/) an, bei dem Sie registriert sind. Dies wird dann von Azure Migrate bei der Kostenschätzung entsprechend berücksichtigt. |
| **Währung** | Zeigt die Abrechnungswährung für Ihr Konto an. |
| **Rabatt (%)** | Listet alle abonnementspezifischen Rabatte auf, die Sie zusätzlich zum Azure-Angebot erhalten. Die Standardeinstellung ist 0 %. |
| **Azure-Hybridvorteil** | Gibt an, ob Sie über Software Assurance verfügen und den [Azure-Hybridvorteil](https://azure.microsoft.com/pricing/hybrid-use-benefit/) nutzen können. Obwohl dies aufgrund des knotenbasierten Preises keine Auswirkungen auf die Preisgestaltung von Azure VMware-Lösungen hat, können Kunden dennoch ihre lokalen Betriebssystem- oder SQL-Lizenzen (Microsoft-basiert) in AVS mit Azure-Hybridvorteilen anwenden. Andere Anbieter von Softwarebetriebssystemen werden ihre eigenen Lizenzbedingungen wie RHEL zur Verfügung stellen müssen. |
| **vCPU-Überzeichnung** | Gibt das Verhältnis der Anzahl der virtuellen Kerne an, die an einen physischen Kern im AVS-Knoten gebunden sind. Der Standardwert in den Berechnungen in AVS beträgt vier virtuelle CPUs pro physischem Kern. API-Benutzer können diesen Wert als ganze Zahl (Integer) festlegen. Beachten Sie, dass eine vCPU-Überzeichnung > 4:1 je nach CPU-Auslastung Auswirkungen auf die Workloads haben kann. Bei der Dimensionierung wird immer davon ausgegangen, dass die ausgewählten Kerne zu 100 % ausgelastet sind. |
| **Faktor für übermäßigen Speichercommit** | Gibt das Verhältnis der Überbelegung im Cluster an. Der Wert 1 gibt eine Speicherauslastung von 100 % an, 0,5 gibt 50 % an, und 2 bedeutet, dass der verfügbare Speicher 200 % beträgt. Sie können nur Werte zwischen 0,5 und 10 mit bis zu einer Dezimalstelle hinzufügen. |
| **Deduplizierungs- und Komprimierungsfaktor** | Gibt den erwarteten Deduplizierungs- und Komprimierungsfaktor für die Workloads an. Der tatsächliche Wert kann aus der lokalen vSAN- oder Speicherkonfiguration abgerufen werden. Dieser variiert je nach Arbeitsauslastung. Ein Wert von 3 gibt eine Multiplikation mit 3 an, sodass für einen 300-GB-Datenträger nur 100 GB Speicher verwendet werden. Der Wert 1 bedeutet, dass keine Deduplizierung und keine Komprimierung erfolgen. Sie können nur Werte zwischen 1 und 10 mit bis zu einer Dezimalstelle hinzufügen. |

## <a name="azure-vmware-solution-avs-suitability-analysis"></a>AVS-Eignungsanalyse (Azure VMware Solution)

AVS-Bewertungen bewerten die einzelnen lokalen virtuellen Computer hinsichtlich ihrer Eignung für AVS, indem die Eigenschaften des Servers überprüft werden. Außerdem werden die einzelnen bewerteten Server einer der folgenden Eignungskategorien zugewiesen:

- **Bereit für AVS**: Der Server kann ohne Änderungen zu Azure (AVS) migriert werden. Er wird in AVS mit voller AVS-Unterstützung starten.
- **Bereit mit Bedingungen**: Die VM hat möglicherweise Kompatibilitätsprobleme mit der aktuellen vSphere-Version und erfordert möglicherweise VMware-Tools und/oder andere Einstellungen, bevor die volle Funktionalität der VM in AVS erreicht werden kann.
- **Nicht bereit für AVS**: Der virtuelle Computer kann nicht in AVS gestartet werden. Wenn z. B. an den lokalen virtuellen VMware-Computer ein externes Gerät wie ein CD-ROM angeschlossen ist, tritt beim VMware VMotion-Vorgang ein Fehler auf (bei Verwendung von VMware VMotion).
- **Bereitschaft unbekannt**: Azure Migrate konnte die Bereitschaft des Servers aufgrund unzureichender Metadaten aus der lokalen Umgebung nicht bestimmen.

Die Bewertung überprüft die Servereigenschaften, um die Azure-Bereitschaft des lokalen Servers zu bestimmen.

### <a name="server-properties"></a>Servereigenschaften

Die Bewertung überprüft die folgende Eigenschaft des lokalen virtuellen Computers, um zu ermitteln, ob dieser in Azure VMware Solution (AVS) ausgeführt werden kann.

| **Eigenschaft** | **Details** | **AVS-Bereitschaftsstatus** |
| - | - | - |
| **Internetprotokoll** | Die End-to-End-IPv6-IP-Adressierung wird von Azure derzeit nicht unterstützt. Sollte Ihr Server mit IPv6 erkannt werden, wenden Sie sich an Ihr lokales MSFT-AVS-GBB-Team. | Internetprotokoll mit bedingter Bereitschaft |

### <a name="guest-operating-system"></a>Gastbetriebssystem

Gegenwärtig werden Betriebssysteme bei AVS-Bewertungen nicht im Rahmen der Eignungsanalyse überprüft. Alle Betriebssysteme, die auf lokalen virtuellen Computern ausgeführt werden, können wahrscheinlich unter Azure VMware Solution (AVS) ausgeführt werden.

Neben den VM-Eigenschaften untersucht die Bewertung auch das Gastbetriebssystem der Server, um zu ermitteln, ob sie auf Azure ausgeführt werden können.

## <a name="sizing"></a>Festlegen der Größe

Nachdem ein Server als „bereit für AVS“ markiert wurde, gibt die AVS-Bewertung Empfehlungen zur Knotendimensionierung ab, die die Identifizierung der entsprechenden lokalen VM-Anforderungen und die Ermittlung der Gesamtanzahl der erforderlichen AVS-Knoten umfassen. Die Empfehlungen variieren je nach den angegebenen Bewertungseigenschaften.

- Wenn die Bewertung eine *leistungsbasierte Dimensionierung* verwendet, berücksichtigt Azure Migrate den Leistungsverlauf des Servers, um die geeignete Empfehlung zur Dimensionierung für AVS abzugeben. Diese Methode ist besonders hilfreich, wenn Sie die lokalen virtuellen Computer überbelegt haben, die Auslastung jedoch gering ist, und Sie die Größe der virtuellen Computer in AVS optimal anpassen möchten, um Kosten zu sparen. Diese Methode ist hilfreich, um die Größen bei der Migration zu optimieren.

> [!NOTE] 
>Beim Importieren von Servern mithilfe einer CSV-Datei werden die von Ihnen angegebenen Leistungswerte (CPU-Auslastung, Arbeitsspeicherauslastung, Datenträger-IOPS und Durchsatz) verwendet, wenn Sie die leistungsbasierte Größenanpassung auswählen. Sie können keine Leistungsverlaufs- und Perzentilinformationen bereitstellen.

- Wenn Sie die Leistungsdaten für die VM-Größe nicht berücksichtigen und stattdessen die lokalen Server unverändert zu AVS übertragen möchten, können Sie die Dimensionierungskriterien auf „Wie lokal“ festlegen. Anschließend wird die Größe der virtuellen Computer basierend auf der lokalen Konfiguration ohne Berücksichtigung der Nutzungsdaten von der Bewertung berechnet.

### <a name="ftt-sizing-parameters"></a>FTT-Parameter für die Größenanpassung

Die in AVS verwendete Speicher-Engine ist vSAN. Die Speicheranforderungen für Ihre Server werden mithilfe von vSAN-Speicherrichtlinien definiert. Diese Richtlinien steuern, wie Speicher dem virtuellen Computer zugeordnet wird, um die erforderliche Dienstebene für Ihre virtuellen Computer zu garantieren. Folgende FTT-RAID-Kombinationen sind verfügbar:

| **Zu tolerierende Fehler (Failures to Tolerate, FTT)** | **RAID-Konfiguration** | **Mindestens erforderliche Hostanzahl** | **Überlegungen zur Größe** |
| - | - | - | - |
| 1 | RAID-1 (Spiegelung) | 3 | Von einem virtuellen Computer mit 100 GB werden 200 GB beansprucht. |
| 1 | RAID-5 (Erasure Coding) | 4 | Von einem virtuellen Computer mit 100 GB werden 133,33 GB beansprucht. |
| 2 | RAID-1 (Spiegelung) | 5 | Von einem virtuellen Computer mit 100 GB werden 300 GB beansprucht. |
| 2 | RAID-6 (Erasure Coding) | 6 | Von einem virtuellen Computer mit 100 GB werden 150 GB beansprucht. |
| 3 | RAID-1 (Spiegelung) | 7 | Von einem virtuellen Computer mit 100 GB werden 400 GB beansprucht. |

### <a name="performance-based-sizing"></a>Leistungsbasierte Größenanpassung

Für die leistungsbasierte Dimensionierung erstellt die Azure Migrate-Appliance Profile der lokalen Umgebung, um Leistungsdaten für CPU, Speicher und Datenträger zu sammeln. Folglich wird bei der leistungsbasierte Dimensionierung für AVS der zugewiesene Speicherplatz berücksichtigt, und es wird der ausgewählte Perzentilwert für die Arbeitsspeicherauslastung und CPU-Auslastung verwendet. Wenn beispielsweise für einen virtuellen Computer 4 virtuelle Kerne zugewiesen sind, aber nur 25 % genutzt werden, berechnet AVS die Größe für 1 virtuellen Kern dieses virtuellen Computers.

**Schritte zur Leistungsdatensammlung:**

1. Bei virtuellen VMware-Computern sammelt die Azure Migrate-Appliance Echtzeit-Abtastpunkte in einem 20-Sekunden-Intervall.
2. Das Gerät führt einen Rollup für die alle 10 Minuten gesammelten Abtastpunkte aus und sendet den Maximalwert der letzten 10 Minuten an Azure Migrate.
3. Azure Migrate speichert alle 10-Minuten-Abtastpunkte des letzten Monats. Abhängig von den Bewertungseigenschaften, die für den *Leistungsverlauf* und die *prozentuale Auslastung* angegeben werden, wird der entsprechende Datenpunkt identifiziert, der für die richtige Dimensionierung verwendet werden soll. Wenn beispielsweise der Leistungsverlauf auf einen Tag festgelegt ist und die prozentuale Auslastung das 95. Perzentil ist, verwendet Azure Migrate die 10-minütigen Abtastpunkte für den letzten Tag, sortiert sie in aufsteigender Reihenfolge und wählt das 95. Perzentil für die richtige Größe aus.
4. Dieser Wert wird mit dem Komfortfaktor multipliziert, um die effektiven Leistungs- und Auslastungsdaten für jede Metrik (CPU-Auslastung und Arbeitsspeicherauslastung) zu erhalten, die von der Appliance erfasst werden.

Nachdem der Wert für die effektive Auslastung festgelegt wurde, werden Speicher-, Netzwerk- und Computegröße wie folgt behandelt.

**Speichergröße**: Azure Migrate verwendet den gesamten lokalen VM-Speicherplatz auf dem Datenträger als Berechnungsparameter, um die AVS vSAN-Speicheranforderungen zusätzlich zu der vom Kunden gewählten FTT-Einstellung zu bestimmen. FTT – Wenn eine FTT-Option nicht toleriert wird und eine Mindestanzahl von Knoten pro FTT-Option erforderlich ist, wird der gesamte erforderliche vSAN-Speicher in Kombination mit der VM-Datenträgeranforderung bestimmt. Die Speicherauslastung wird derzeit nicht berücksichtigt, und die Logik berücksichtigt nur den zugewiesenen Speicher pro VM.

**Netzwerkgröße**: Bei AVS-Bewertungen werden derzeit keine Netzwerkeinstellungen berücksichtigt.

**Computegröße**: Nach der Berechnung der Speicheranforderungen werden von der AVS-Bewertung die CPU- und Arbeitsspeicheranforderungen berücksichtigt, um die erforderliche Knotenanzahl für AVS auf der Grundlage des Knotentyps zu bestimmen.

- Basierend auf den Kriterien zur Größenanpassung betrachtet die AVS-Bewertung entweder die leistungsbasierten VM-Daten oder die lokale VM-Konfiguration. Die Einstellung des Komfortfaktors ermöglicht die Angabe des Zuwachsfaktors des Clusters. Da Hyperthreading standardmäßig aktiviert ist, verfügt ein Knoten mit 36 Kernen über 72 virtuelle Kerne. Pro physischem Kern werden vier virtuelle Kerne verwendet, um CPU-Schwellenwerte pro Cluster zu ermitteln. Dabei gilt der VMware-Standard einer maximalen Auslastung von 80 Prozent, um Wartungsmaßnahmen oder Fehlerbehandlungen zu ermöglichen, ohne die Clusterverfügbarkeit zu beeinträchtigen. Gegenwärtig gibt es keine Möglichkeit, die Überzeichnungswerte zu ändern, und dies könnte in zukünftigen Versionen der Fall sein.

### <a name="as-on-premises-sizing"></a>Größenanpassung vom Typ „Wie lokal“

Wenn Sie die Dimensionierung *Wie lokal* verwenden, wird der Leistungsverlauf der VMs und Datenträger von der AVS-Bewertung nicht berücksichtigt. Stattdessen werden AVS-Knoten basierend auf der lokal zugeordneten Größe zugewiesen. Der Standardspeichertyp in AVS ist vSAN.

[Weitere Informationen](./tutorial-assess-vmware-azure-vmware-solution.md#review-an-assessment) zum Überprüfen einer Bewertung vom Typ „Azure VMware Solution (AVS)“.

### <a name="cpu-utilization-on-avs-nodes"></a>CPU-Auslastung auf den AVS-Knoten

Bei der Bestimmung der CPU-Auslastung wird davon ausgegangen, dass die verfügbaren Kerne zu 100 % ausgelastet sind. Um die Anzahl erforderlicher Knoten zu reduzieren, können Sie die Überzeichnung basierend auf den Workloadmerkmalen und der lokalen Umgebung z. B. von 4:1 auf 6:1 erhöhen. Anders als bei Datenträgern legt AVS keine Begrenzungen für die CPU-Auslastung fest. Daher müssen die Kunden sicherstellen, dass der Cluster optimal ausgeführt wird, und ggf. entsprechende Anpassungen vornehmen, um eine Überlastung zu vermeiden. Um mehr Zuwachs zu ermöglichen, verringern Sie die Überzeichnung, oder erhöhen Sie den Wert für den Zuwachsfaktor.

Bei der Bestimmung der CPU-Auslastung wird bereits der Verwaltungsaufwand von vCenter, NSX Manager und anderen kleineren Ressourcen berücksichtigt.

### <a name="memory-utilization-on-avs-nodes"></a>Arbeitsspeicherauslastung auf den AVS-Knoten

Die Arbeitsspeicherauslastung beinhaltet den gesamten Arbeitsspeicher aller Knoten im Vergleich zu den Anforderungen vom Server oder den Workloads. Der Arbeitsspeicher kann überzeichnet werden, und auch für diesen legt AVS keine Begrenzungen fest. Es liegt in der Verantwortung des Kunden, eine optimale Clusterleistung für seine Workloads sicherzustellen.

Bei der Bestimmung der Arbeitsspeicherauslastung wird bereits der Verwaltungsaufwand von vCenter, NSX Manager und anderen kleineren Ressourcen berücksichtigt.

### <a name="storage-utilization-on-avs-nodes"></a>Speicherauslastung auf den AVS-Knoten

Die Speicherauslastung wird in der folgenden Reihenfolge berechnet:

1. Erforderliche Größe für VMs (entweder Zuweisung des tatsächlichen Werts oder der verwendete Speicherplatz auf Grundlage der Leistung)
2. Ggf. Anwendung eines Zuwachsfaktors
3. Hinzufügen von Verwaltungsaufwand und Anwenden des TTT-Verhältnisses
4. Anwenden eines Deduplizierungs- und Komprimierungsfaktors
5. Anwenden von 25 % Schlupfspeicher für vSAN
6. Bestimmen des Anteils des verfügbaren Speichers für VMs am Gesamtspeicher, einschließlich Verwaltungsaufwand

Der verfügbare Speicher in einem Cluster mit drei Knoten basiert auf der Standardspeicherrichtlinie, d. h. RAID-1 mit Thick Provisioning. Bei der Berechnung für beispielsweise Erasure Coding oder RAID-5 sind mindestens vier Knoten erforderlich. Beachten Sie, dass in AVS die Speicherrichtlinie vom Administrator geändert werden muss, um mehr Speicherplatz zu ermöglichen.

## <a name="confidence-ratings"></a>Zuverlässigkeitsstufen

Jeder leistungsbasierten Bewertung in Azure Migrate wird eine Zuverlässigkeitsstufe zugeordnet, die zwischen ein (niedrigster Wert) bis fünf Sterne (höchster Wert) betragen kann.

- Die Zuverlässigkeitsstufe wird einer Bewertung auf der Grundlage der Verfügbarkeit von Datenpunkten zugeordnet, die zum Berechnen der Bewertung erforderlich sind.
- Anhand der Zuverlässigkeitsstufe können Sie die Zuverlässigkeit der von Azure Migrate bereitgestellten Größenempfehlungen besser einschätzen.
- Die Zuverlässigkeitsstufe gilt nicht für Bewertungen vom Typ *Wie lokal*.
- Für eine leistungsbasierte Dimensionierung benötigen die AVS-Bewertungen die Auslastungsdaten für CPU und VM-Arbeitsspeicher. Die folgenden Daten werden gesammelt, aber nicht für Größenempfehlungen für AVS verwendet:

  - IOPS- und Durchsatzdaten für jeden an den virtuellen Computer angefügten Datenträger
  - Informationen zur Netzwerk-E/A für die leistungsbasierte Größenanpassung für jeden Netzwerkadapter, der an einen virtuellen Computer angefügt ist

  Steht eine dieser Nutzungsangaben in vCenter Server nicht zur Verfügung, ist die Größenempfehlung unter Umständen nicht zuverlässig.

Die Zuverlässigkeitsstufe für die Bewertung ist abhängig davon, wie viele Datenpunkte verfügbar sind (in Prozent).

| **Verfügbarkeit von Datenpunkten** | **Zuverlässigkeitsstufe** |
| - | - |
| 0–20 % | 1 Stern |
| 21–40 % | 2 Sterne |
| 41–60 % | 3 Sterne |
| 61–80 % | 4 Sterne |
| 81–100 % | 5 Sterne |

### <a name="low-confidence-ratings"></a>Niedrige Zuverlässigkeitsstufen

Einige mögliche Gründe für eine niedrige Zuverlässigkeitsstufe einer Bewertung:

- Sie haben für den Zeitraum, für den Sie die Bewertung erstellen, kein Profil der Umgebung erstellt. Wenn Sie die Bewertung z. B. mit einer auf einen Tag festgelegten Leistungsdauer erstellen, müssen Sie bis mindestens einen Tag nach dem Start der Ermittlung warten, bis alle Datenpunkte gesammelt sind.
- Die Bewertung kann die Leistungsdaten für einige oder alle VMs im Bewertungszeitraum nicht erfassen. Für eine Bewertung mit hoher Konfidenz stellen Sie Folgendes sicher:

  - VMs sind für die Dauer der Bewertung eingeschaltet.
  - Ausgehende Verbindungen am Port 443 sind zugelassen.
  - Für Hyper-V-VMs ist dynamischer Arbeitsspeicher aktiviert.

  Führen Sie die erneute Berechnung der Bewertung mit der Option „Neu berechnen“ durch, um die neuesten Änderungen an der Zuverlässigkeitsstufe widerzuspiegeln.
- Einige virtuelle Computer wurden während des Zeitraums, für den die Bewertung berechnet wird, erstellt. Beispiel: Sie haben eine Bewertung für den Leistungsverlauf im letzten Monat erstellt, einige VMs wurden jedoch erst vor einer Woche erstellt. In diesem Fall stehen für die gesamte Dauer keine Leistungsdaten für die neuen VMs zur Verfügung und die Zuverlässigkeitsstufe wäre gering.

> [!NOTE]
> Bei einer Zuverlässigkeitsstufe von weniger als fünf Sternen wird empfohlen, mindestens einen Tag zu warten, damit die Appliance ein Profil der Umgebung erstellen kann. Führen Sie dann eine Neuberechnung der Bewertung durch. Wenn Sie dies nicht tun, ist die leistungsbasierte Dimensionierung möglicherweise nicht zuverlässig. In diesem Fall wird empfohlen, die Bewertung auf die Größenanpassung „Wie lokal“ zu ändern.

## <a name="monthly-cost-estimation"></a>Schätzung der monatlichen Kosten

Nachdem die Größenempfehlungen abgeschlossen sind, berechnet Azure Migrate die Gesamtkosten für die Ausführung der lokalen Workloads in AVS, indem die Anzahl der erforderlichen AVS-Knoten mit dem Knotenpreis multipliziert wird. Die Kosten pro VM werden berechnet, indem die Gesamtkosten durch die Anzahl der virtuellen Computer in der Bewertung dividiert werden.

- Bei der Berechnung werden die Anzahl erforderlicher Knoten, der Knotentyp und der Standort berücksichtigt.
- Die Kosten aller Knoten werden zusammengefasst, um die monatlichen Gesamtkosten zu berechnen.
- Kosten werden in der Währung angezeigt, die in den Bewertungseinstellungen festgelegt ist.

Da die Preise für Azure VMware Solution (AVS) pro Knoten angegeben werden, enthalten die Gesamtkosten keine Verteilung der Compute- und Speicherkosten. [Weitere Informationen](../azure-vmware/introduction.md)

## <a name="migration-tool-guidance"></a>Leitfaden zum Migrationstool

Im Bericht zur Azure-Bereitschaft für die Bewertung „Azure VMware Solution (AVS)“ werden folgende Tools empfohlen:

- **VMware HCX oder Enterprise**: Für VMware-Server wird die VMware HCX-Lösung (Hybrid Cloud Extension) empfohlen, um Ihre lokale Workload zu Ihrer privaten AVS-Cloud (Azure VMware Solution) zu migrieren. [Weitere Informationen](../azure-vmware/install-vmware-hcx.md).
- **Unbekannt**: Für Server, die über eine CSV-Datei importiert wurden, ist das Standardmigrationstool unbekannt. Für VMware-Server empfiehlt sich jedoch die Verwendung der VMware HCX-Lösung (Hybrid Cloud Extension).

## <a name="next-steps"></a>Nächste Schritte

Erstellen Sie eine Bewertung für [virtuelle AVS VMware-Computer](how-to-create-azure-vmware-solution-assessment.md).
