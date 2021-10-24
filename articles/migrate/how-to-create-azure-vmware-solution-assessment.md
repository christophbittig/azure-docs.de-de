---
title: Erstellen einer AVS-Bewertung mit Azure Migrate | Microsoft-Dokumentation
description: Es wird beschrieben, wie Sie eine AVS-Bewertung mit Azure Migrate erstellen.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 89c7c903d382d67d9be64829118bdddf8ee4bd31
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005993"
---
# <a name="create-an-azure-vmware-solution-assessment"></a>Erstellen einer Azure VMware Solution-Bewertung

Dieser Artikel beschreibt die Erstellung einer AVS-Bewertung (Azure VMware Solution) für lokale Server in einer VMware-Umgebung mit „Azure Migrate: Ermittlung und Bewertung“.

[Azure Migrate](migrate-services-overview.md) hilft Ihnen bei der Migration zu Azure. Azure Migrate bietet einen zentralisierten Hub zum Nachverfolgen der Ermittlung, Bewertung und Migration lokaler Infrastruktur, Anwendungen und Daten zu Azure. Der Hub stellt Azure-Tools für die Bewertung und Migration sowie Drittanbietertools von unabhängigen Softwareanbietern (ISVs) bereit.

## <a name="before-you-start"></a>Vorbereitung

- Stellen Sie sicher, dass Sie ein Azure Migrate-Projekt [erstellt](./create-manage-projects.md) haben.
- Wenn Sie bereits ein Projekt erstellt haben, vergewissern Sie sich, dass Sie das Ermittlungs- und Bewertungstool von Azure Migrate [hinzugefügt](how-to-assess.md) haben.
- Um eine Bewertung zu erstellen, müssen Sie eine Azure Migrate-Appliance für [VMware](how-to-set-up-appliance-vmware.md) einrichten, die die lokalen Server ermittelt und Metadaten und Leistungsdaten an „Azure Migrate: Ermittlung und Bewertung“ sendet. [Weitere Informationen](migrate-appliance.md)
- Sie könnten auch die [Servermetadaten](./tutorial-discover-import.md) im CSV-Format (durch Trennzeichen getrennte Werte) importieren.


## <a name="azure-vmware-solution-avs-assessment-overview"></a>AVS-Bewertung (Azure VMware Solution) – Übersicht

Sie können drei Arten von Bewertungen mit der Azure Migrate-Ermittlung und -Bewertung erstellen.

***Bewertungstyp** | **Details**
--- | --- 
**Azure-VM** | Bewertungen zum Migrieren Ihrer lokalen Server zu virtuellen Azure-Computern. Sie können Ihre lokalen Server in der [VMware](how-to-set-up-appliance-vmware.md)- und [Hyper-V](how-to-set-up-appliance-hyper-v.md)-Umgebung und [physischen Server](how-to-set-up-appliance-physical.md) für die Migration zu Azure-VMs mit diesem Bewertungstyp bewerten.
**Azure SQL** | Bewertungen zum Migrieren Ihrer lokalen SQL Server-Instanzen aus Ihrer VMware-Umgebung zu Azure SQL-Datenbank oder Azure SQL Managed Instance.
**Azure VMware Solution (AVS)** | Bewertungen zum Migrieren Ihrer lokalen Server zu [Azure VMware Solution (AVS)](../azure-vmware/introduction.md). Mit diesem Bewertungstyp können Sie lokale Server in einer [VMware-Umgebung](how-to-set-up-appliance-vmware.md) hinsichtlich der Migration zu Azure VMware Solution (AVS) bewerten. [Weitere Informationen](concepts-azure-vmware-solution-assessment-calculation.md)

> [!NOTE]
> Die AVS-Bewertung (Azure VMware Solution) kann nur für Server in einer VMware-Umgebung erstellt werden.


Es gibt zwei Arten von Kriterien zur Größenanpassung, die Sie für die Erstellung von AVS-Bewertungen (Azure VMware Solution) verwenden können:

**Bewertung** | **Details** | **Daten**
--- | --- | ---
**Leistungsbasiert** | Bewertungen basierend auf gesammelten Leistungsdaten von lokalen Servern. | **Empfohlene Knotengröße**: Basierend auf den Daten zur CPU- und Arbeitsspeicherauslastung zusammen mit dem Knotentyp, dem Speichertyp und der FTT-Einstellung, die Sie für die Bewertung auswählen.
**Wie lokal** | Bewertungen basierend auf lokaler Größenanpassung | **Empfohlene Knotengröße**: Basierend auf der Größe lokaler Server zusammen mit dem Knotentyp, dem Speichertyp und der FTT-Einstellung, die Sie für die Bewertung auswählen.


## <a name="run-an-azure-vmware-solution-avs-assessment"></a>Ausführen einer Bewertung vom Typ „Azure VMware Solution (AVS)“

1.  Klicken Sie auf der Seite **Übersicht** unter **Windows, Linux und SQL Server** auf **Server bewerten und migrieren**.
    :::image type="content" source="./media/tutorial-assess-sql/assess-migrate.png" alt-text="Seite „Übersicht“ für Azure Migrate":::

1. Klicken Sie unter **Azure Migrate: Ermittlung und Bewertung** auf **Bewerten**.

   ![Position der Schaltfläche „Bewerten“](./media/tutorial-assess-vmware-azure-vmware-solution/assess.png)

1. Wählen Sie unter **Server bewerten** > **Bewertungstyp** die Option **Azure VMware Solution (AVS)** aus.

1. Unter **Ermittlungsquelle**:

    - Wählen Sie die Option **Von Azure Migrate-Appliance erkannte Server** aus, falls Sie Server mit der Appliance ermittelt haben.
    - Wählen Sie die Option **Importierte Server** aus, falls Sie Server unter Verwendung einer importierten CSV-Datei ermittelt haben. 
    
1. Klicken Sie auf **Bearbeiten**, um die Eigenschaften für die Bewertung zu überprüfen.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-servers.png" alt-text="Seite zum Auswählen der Bewertungseinstellungen":::
 

1. Unter **Bewertungseigenschaften** > **Zieleigenschaften**:

    - Geben Sie unter **Zielspeicherort** die Azure-Region an, zu der Sie die Migration durchführen möchten.
       - Empfehlungen zur Größe und zu den Kosten basieren auf dem von Ihnen angegebenen Standort.
   - Der **Speichertyp** wird standardmäßig auf **vSAN** festgelegt. Dies ist der Standardspeichertyp für eine private AVS-Cloud.
   - **Reservierte Instanzen** werden für AVS-Knoten derzeit nicht unterstützt.
1. Unter **VM-Größe**:
    - Der **Knotentyp** wird standardmäßig auf **AV36** festgelegt. Azure Migrate empfiehlt aus der Gruppe der Knoten denjenigen Knoten, der zum Migrieren der Server zu AVS benötigt wird.
    - Wählen Sie unter **FTT-Einstellung, RAID-Ebene** die Kombination für tolerierbare Fehler (Failure to Tolerate, FTT) und RAID aus.  Die ausgewählte FTT-Option in Kombination mit der lokalen Server-Datenträgeranforderung bestimmt den insgesamt in AVS erforderlichen vSAN-Speicher.
    - Geben Sie unter **CPU-Überzeichnung** das Verhältnis der virtuellen Kerne an, die auf dem AVS-Knoten einem physischen Kern zugeordnet sind. Eine höhere Überzeichnung als 4:1 kann unter Umständen zwar zu einer Leistungsbeeinträchtigung führen, aber sie kann für Workloads vom Typ „Webserver“ verwendet werden.
    - Geben Sie unter **Faktor für übermäßigen Speichercommit** das Verhältnis der Überbelegung des Speichers im Cluster an. Der Wert 1 gibt eine Speicherauslastung von 100 % an, 0,5 gibt 50 % an, und 2 bedeutet, dass 200 % des verfügbaren Speichers verwendet werden. Sie können nur Werte zwischen 0,5 und 10 mit bis zu einer Dezimalstelle hinzufügen.
    - Geben Sie unter **Deduplizierungs- und Komprimierungsfaktor** den erwarteten Deduplizierungs- und Komprimierungsfaktor für Ihre Arbeitsauslastungen an. Der tatsächliche Wert kann aus der lokalen vSAN- oder Speicherkonfiguration abgerufen werden, und dieser kann je nach Arbeitsauslastung variieren. Ein Wert von 3 gibt eine Multiplikation mit 3 an, sodass für einen 300-GB-Datenträger nur 100 GB Speicher verwendet werden. Der Wert 1 bedeutet, dass keine Deduplizierung und keine Komprimierung erfolgen. Sie können nur Werte zwischen 1 und 10 mit bis zu einer Dezimalstelle hinzufügen.
1. Unter **Knotengröße**: 
    - Wählen Sie unter **Größenkriterium** aus, ob die Bewertung auf statischen Metadaten oder leistungsbezogenen Daten basieren soll. Bei Verwendung von Leistungsdaten:
        - Geben Sie unter **Leistungsverlauf** die Datendauer an, auf der die Bewertung basieren soll.
        - Geben Sie unter **Perzentilwert der Nutzung** den Perzentilwert an, den Sie für das Leistungsbeispiel verwenden möchten. 
    - Geben Sie unter **Komfortfaktor** den Puffer an, den Sie während der Bewertung verwenden möchten. Hierbei werden Aspekte wie saisonale Nutzung, ein kurzer Leistungsverlauf und eine voraussichtliche Zunahme der zukünftigen Nutzung berücksichtigt. Beispiel für einen Komfortfaktor von „2“:
    
        **Komponente** | **Tatsächliche Auslastung** | **Komfortfaktor hinzufügen (2.0)**
        --- | --- | ---
        Kerne | 2  | 4
        Arbeitsspeicher | 8 GB | 16 GB  

1. Unter **Preise**:
    - Zeigt unter **Angebot** das [Azure-Angebot](https://azure.microsoft.com/support/legal/offer-details/) an, bei dem Sie registriert sind. Bei der Bewertung werden die Kosten für dieses Angebot geschätzt.
    - Wählen Sie unter **Währung** die Abrechnungswährung für Ihr Konto aus.
    - Fügen Sie unter **Abzug (%)** alle abonnementspezifischen Rabatte hinzu, die Sie zusätzlich zum Azure-Angebot erhalten. Die Standardeinstellung ist 0 %.

1. Klicken Sie auf **Speichern**, falls Sie Änderungen vorgenommen haben.

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-view-all.png" alt-text="Bewertungseigenschaften":::

1. Klicken Sie unter **Server bewerten** auf **Weiter**.

1. Geben Sie in **Server für die Bewertung auswählen** > **Bewertungsname** einen Namen für die Bewertung an. 
 
1. Wählen Sie unter **Gruppe auswählen oder erstellen** die Option **Neu erstellen** aus, und geben Sie einen Namen für die Gruppe ein. 
    
    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/assess-group.png" alt-text="Hinzufügen von Servern zu einer Gruppe":::
 
1. Wählen Sie die Appliance und dann die Server aus, die Sie der Gruppe hinzufügen möchten. Klicken Sie dann auf **Weiter**.

1. Sehen Sie sich unter **Überprüfen + Bewertung erstellen** die Bewertungsdetails an, und klicken Sie auf **Bewertung erstellen**, um die Gruppe zu erstellen und die Bewertung durchzuführen.

    > [!NOTE]
    > Bei leistungsbezogenen Bewertungen empfehlen wir Ihnen, nach dem Starten einer Ermittlung mindestens einen Tag zu warten, bevor Sie eine Bewertung erstellen. Dies ermöglicht einen längeren Zeitraum zum Sammeln von Leistungsdaten mit höherer Zuverlässigkeit. Idealerweise sollten Sie nach dem Starten der Ermittlung den Zeitraum der Leistungsdauer abwarten, den Sie angegeben haben (Tag/Woche/Monat), um eine Bewertung mit einer hohen Zuverlässigkeit zu erzielen.


## <a name="review-an-azure-vmware-solution-avs-assessment"></a>Überprüfen einer Bewertung vom Typ „Azure VMware Solution (AVS)“

Eine Bewertung vom Typ „Azure VMware Solution (AVS)“ beschreibt Folgendes:

- **AVS-Bereitschaft (Azure VMware Solution)** : Ermittelt, ob die lokalen Server für die Migration zu Azure VMware Solution (AVS) geeignet sind.
- **Anzahl Azure VMware Solution-Knoten**: Geschätzte Anzahl Azure VMware Solution-Knoten, die zum Ausführen der Server erforderlich sind.
- **Übergreifende Nutzung auf AVS-Knoten**: Projizierte CPU-, Arbeitsspeicher- und Speicherauslastung über alle Knoten hinweg.
    - Die Nutzung beinhaltet Voraus-Factoring in den folgenden Mehraufwandsbereichen der Clusterverwaltung wie vCenter Server, NSX-Manager (groß) und NSX Edge, wenn HCX bereitgestellt ist, außerdem den HCX-Manager und die IX-Appliance, das ~44vCPU (11 CPU), 75 GB RAM und 722 GB Speicher vor Komprimierung und Deduplizierung beansprucht.
    - Der begrenzende Faktor bestimmt die Anzahl der Hosts/Knoten, die für die Ressourcen erforderlich sind.
- **Geschätzte monatliche Kosten**: Die geschätzten monatlichen Kosten für alle AVS-Knoten (Azure VMware Solution), auf denen die lokalen virtuellen Computer ausgeführt werden.


### <a name="view-an-assessment"></a>Anzeigen einer Bewertung

1. Klicken Sie unter **Windows, Linux und SQL Server** > **Azure Migrate: Ermittlung und Bewertung** auf die Zahl neben **Azure VMware Solution**.

1. Wählen Sie unter **Bewertungen** eine Bewertung aus, um sie zu öffnen. Beispiel (Schätzungen und Kosten gelten nur für das Beispiel): 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="Zusammenfassung der AVS-Bewertung":::

1. Sehen Sie sich die Zusammenfassung der Bewertung an. Sie können auf **Größenannahmen** klicken, um die Annahmen zu verstehen, die in Berechnungen zur Knotengröße und Ressourcenauslastung berücksichtigt wurden. Sie können auch die Bewertungseigenschaften bearbeiten oder die Bewertung neu berechnen.

### <a name="review-azure-vmware-solution-avs-readiness"></a>Überprüfen der AVS-Bereitschaft (Azure VMware Solution)

1. Überprüfen Sie unter **Azure-Bereitschaft**, ob Server für die Migration zu AVS bereit sind.

2. Überprüfen Sie den Serverstatus:
    - **Bereit für AVS**: Der Server kann ohne Änderungen zu Azure (AVS) migriert werden. Er wird in AVS mit voller AVS-Unterstützung starten.
    - **Bereit mit Bedingungen**: Der Server hat möglicherweise Kompatibilitätsprobleme mit der aktuellen vSphere-Version und erfordert möglicherweise VMware-Tools und/oder andere Einstellungen, bevor die volle Funktionalität des Servers in AVS erreicht werden kann.
    - **Nicht bereit für AVS**: Der Server kann nicht in AVS gestartet werden. Wenn z. B. an den lokalen Server ein externes Gerät wie ein CD-ROM angeschlossen ist, tritt beim VMotion-Vorgang ein Fehler auf (bei Verwendung von VMware VMotion).
    - **Bereitschaft unbekannt**: Azure Migrate konnte die Bereitschaft des Servers aufgrund unzureichender Metadaten aus der lokalen Umgebung nicht bestimmen.

3. Überprüfen Sie das vorgeschlagene Tool:
    - **VMware HCX oder Enterprise**: Für VMware-Server wird die VMware HCX-Lösung (Hybrid Cloud Extension) empfohlen, um Ihre lokale Workload zu Ihrer privaten AVS-Cloud (Azure VMware Solution) zu migrieren. [Weitere Informationen](../azure-vmware/configure-vmware-hcx.md).
    - **Unbekannt**: Für Server, die über eine CSV-Datei importiert wurden, ist das Standardmigrationstool unbekannt. Für VMware-Server empfiehlt sich jedoch die Verwendung der VMware HCX-Lösung (Hybrid Cloud Extension). 

4. Klicken Sie auf einen **AVS-Bereitschaftsstatus**. Sie können Details zur VM-Bereitschaft sowie VM-Details wie Compute-, Speicher- und Netzwerkeinstellungen anzeigen.

### <a name="review-cost-details"></a>Überprüfen der Kostendetails

In dieser Ansicht werden die geschätzten Kosten für die Ausführung der Server in Azure VMware Solution (AVS) angezeigt.

1. Überprüfen Sie die monatlichen Gesamtkosten. Die Kosten für alle Server in der bewerteten Gruppe werden aggregiert. 

    - Kostenschätzungen basieren auf der Anzahl von benötigten AVS-Knoten unter Berücksichtigung der Ressourcenanforderungen aller Server insgesamt.
    - Da die Preise für Azure VMware Solution (AVS) pro Knoten angegeben werden, enthalten die Gesamtkosten keine Verteilung der Compute- und Speicherkosten.
    - Die Kostenschätzung gilt für die Ausführung der lokalen Server in AVS. Bei der AVS-Bewertung werden keine PaaS- oder SaaS-Kosten berücksichtigt.
    
2. Sie können die geschätzten monatlichen Speicherkosten überprüfen. Diese Ansicht zeigt aggregierte Speicherkosten für die bewertete Gruppe, aufgeschlüsselt nach verschiedenen Arten von Speicherdatenträgern.

3. Sie können einen Drilldown ausführen, um die Details für bestimmte Server anzuzeigen.


### <a name="review-confidence-rating"></a>Prüfen der Zuverlässigkeitsstufe

Wenn Sie leistungsbasierte Bewertungen ausführen, wird der Bewertung eine Zuverlässigkeitsstufe zugewiesen.

- Es wird eine Bewertung zwischen einem Stern (niedrigste Stufe) und fünf Sternen (höchste Stufe) vergeben.
- Anhand der Zuverlässigkeitsstufe können Sie die Zuverlässigkeit der von der Bewertung bereitgestellten Größenempfehlungen besser einschätzen.
- Die Zuverlässigkeitsstufe basiert auf der Verfügbarkeit von Datenpunkten, die zum Berechnen der Bewertung erforderlich sind.
- Für eine leistungsbasierte Größenanpassung benötigen die AVS-Bewertungen die Auslastungsdaten für CPU und Arbeitsspeicher des Servers. Die folgenden Leistungsdaten werden gesammelt, aber nicht für Größenempfehlungen für AVS-Bewertungen verwendet:
  - IOPS- und Durchsatzdaten für jeden an den Server angefügten Datenträger.
  - Informationen zur Netzwerk-E/A für die leistungsbasierte Größenanpassung für jeden Netzwerkadapter, der an einen Server angefügt ist.

Die Zuverlässigkeitsstufen für eine Bewertung sehen wie folgt aus:

**Verfügbarkeit von Datenpunkten** | **Zuverlässigkeitsstufe**
--- | ---
0 % bis 20 % | Ein Stern
21 % bis 40 % | Zwei Sterne
41 % bis 60 % | Drei Sterne
61 % bis 80 % | Vier Sterne
81 % bis 100 % | Fünf Sterne

[Weitere Informationen](concepts-azure-vmware-solution-assessment-calculation.md) zu Leistungsdaten. 


## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das Erstellen von Gruppen mit hoher Vertrauenswürdigkeit mithilfe der [Zuordnung von Abhängigkeiten](how-to-create-group-machine-dependencies.md).
- [Weitere Informationen](concepts-azure-vmware-solution-assessment-calculation.md) zur Berechnung von AVS-Bewertungen.
