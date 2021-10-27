---
title: Bewerten von VMware-Servern für die Migration zu Azure VMware Solution (AVS) mit Azure Migrate
description: Hier wird beschrieben, wie Sie mit Azure Migrate Server in der VMware-Umgebung für die Migration zu AVS bewerten.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.custom: MVC
ms.openlocfilehash: a2637fbfcaf1e30b1df9f0739630ea2883eba3fa
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994200"
---
# <a name="tutorial-assess-vmware-servers-for-migration-to-avs"></a>Tutorial: Bewerten von VMware-Servern für die Migration zu AVS

Im Rahmen Ihrer Migrationsjourney zu Azure bewerten Sie Ihre lokalen Workloads, um die Cloudbereitschaft zu messen, Risiken zu identifizieren und Kosten und Komplexität zu schätzen.

In diesem Artikel wird veranschaulicht, wie Sie ermittelte virtuelle VMware-Computer oder VMware-Server für die Migration zu Azure VMware Solution (AVS) per Azure Migrate bewerten. Bei AVS handelt es sich um einen verwalteten Dienst, mit dem Sie die VMware-Plattform in Azure ausführen.

In diesem Tutorial lernen Sie Folgendes:
> [!div class="checklist"]
- Führen Sie eine Bewertung basierend auf den Servermetadaten und den Konfigurationsinformationen durch.
- Führen Sie eine Bewertung basierend auf den Leistungsdaten durch.

> [!NOTE]
> In Tutorials wird der schnellste Weg zum Ausprobieren eines Szenarios beschrieben, und nach Möglichkeit werden dabei die Standardoptionen verwendet. 

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) erstellen, bevor Sie beginnen.



## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie sicher, dass Sie die zu bewertenden Server richtig ermittelt haben, bevor Sie dieses Tutorial zum Bewerten Ihrer Server für die Migration zu AVS durcharbeiten:

- Arbeiten Sie [dieses Tutorial](tutorial-discover-vmware.md) durch, um Server mit der Azure Migrate-Appliance zu ermitteln. 
- Bei der Ermittlung von Servern mit einer importierten CSV-Datei hilft Ihnen [dieses Tutorial](tutorial-discover-import.md) weiter.


## <a name="decide-which-assessment-to-run"></a>Treffen einer Entscheidung für eine Bewertung


Entscheiden Sie, ob Sie eine Bewertung durchführen möchten, bei der die Kriterien für die Größenanpassung auf den Daten bzw. Metadaten der Server basieren, die lokal im unveränderten Zustand gesammelt werden, oder auf dynamischen Leistungsdaten.

**Bewertung** | **Details** | **Empfehlung**
--- | --- | ---
**Aktuelle lokale Umgebung** | Bewertung basierend auf den Daten bzw. Metadaten für die Serverkonfiguration.  | Die empfohlene Knotengröße in AVS basiert auf der lokalen VM-/Servergröße sowie auf den Einstellungen, die Sie in der Bewertung für den Knotentyp, für den Speichertyp und für tolerierbare Fehler angeben.
**Leistungsbasiert** | Bewertung basierend auf gesammelten dynamischen Leistungsdaten. | Die empfohlene Knotengröße in AVS basiert auf den Auslastungsdaten für CPU und Arbeitsspeicher sowie auf den Einstellungen, die Sie in der Bewertung für den Knotentyp, für den Speichertyp und für tolerierbare Fehler angeben.

> [!NOTE]
> Die AVS-Bewertung (Azure VMware Solution) kann nur für VMware-VMs bzw. -Server erstellt werden.

## <a name="run-an-assessment"></a>Durchführen einer Bewertung

Führen Sie eine Bewertung wie folgt aus:

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

## <a name="review-an-assessment"></a>Überprüfen einer Bewertung

Mit einer AVS-Bewertung wird Folgendes beschrieben:

- **AVS-Bereitschaft (Azure VMware Solution)** : Ermittelt, ob die lokalen Server für die Migration zu Azure VMware Solution (AVS) geeignet sind.
- **Anzahl Azure VMware Solution-Knoten**: Geschätzte Anzahl Azure VMware Solution-Knoten, die zum Ausführen der Server erforderlich sind
- **Übergreifende Nutzung auf AVS-Knoten**: Projizierte CPU-, Arbeitsspeicher- und Speicherauslastung über alle Knoten hinweg.
    - Die Nutzung beinhaltet Voraus-Factoring in den folgenden Mehraufwandsbereichen der Clusterverwaltung wie vCenter Server, NSX-Manager (groß) und NSX Edge, wenn HCX bereitgestellt ist, außerdem den HCX-Manager und die IX-Appliance, das ~44vCPU (11 CPU), 75 GB RAM und 722 GB Speicher vor Komprimierung und Deduplizierung beansprucht.
    - Der begrenzende Faktor bestimmt die Anzahl der Hosts/Knoten, die für die Ressourcen erforderlich sind.
- **Geschätzte monatliche Kosten**: Die geschätzten monatlichen Kosten für alle AVS-Knoten (Azure VMware Solution), auf denen die lokalen virtuellen Computer ausgeführt werden.

## <a name="view-an-assessment"></a>Anzeigen einer Bewertung

So zeigen Sie eine Bewertung an:

1. Klicken Sie unter **Windows, Linux und SQL Server** > **Azure Migrate: Ermittlung und Bewertung** auf die Zahl neben **Azure VMware Solution**.

1. Wählen Sie unter **Bewertungen** eine Bewertung aus, um sie zu öffnen. Beispiel (Schätzungen und Kosten gelten nur für das Beispiel): 

    :::image type="content" source="./media/tutorial-assess-vmware-azure-vmware-solution/avs-assessment-summary.png" alt-text="Zusammenfassung der AVS-Bewertung":::

1. Sehen Sie sich die Zusammenfassung der Bewertung an. Sie können auf **Größenannahmen** klicken, um die Annahmen zu verstehen, die in Berechnungen zur Knotengröße und Ressourcenverwendung berücksichtigt wurden. Sie können auch die Bewertungseigenschaften bearbeiten oder die Bewertung neu berechnen.
 

### <a name="review-readiness"></a>Überprüfen der Bereitschaft

1. Klicken Sie auf **Azure-Bereitschaft**.
2. Sehen Sie sich unter **Azure-Bereitschaft** den Bereitschaftsstatus an.

    - **Bereit für AVS**: Der Server kann ohne Änderungen zu AVS migriert werden. Der Server startet in AVS mit voller AVS-Unterstützung.
    - **Bereit mit Bedingungen**: Für den Server bestehen unter Umständen Kompatibilitätsprobleme in Bezug auf die aktuelle vSphere-Version. Gegebenenfalls müssen VMware-Tools installiert oder andere Einstellungen vorgenommen werden, bevor die volle Funktionalität in AVS zur Verfügung steht.
    - **Nicht bereit für AVS**: Die VM wird in AVS nicht gestartet. Wenn an einen lokalen VMware-Server beispielsweise ein externes Gerät (z. B. ein CD-ROM-Laufwerk) angefügt ist und Sie VMware VMotion verwenden, tritt beim VMotion-Vorgang ein Fehler auf.
 - **Bereitschaft unbekannt**: Azure Migrate konnte die Bereitschaft des Servers aufgrund unzureichender Metadaten aus der lokalen Umgebung nicht bestimmen.

3. Überprüfen Sie das vorgeschlagene Tool.

    - VMware HCX oder Enterprise: Für VMware-Server wird die VMware HCX-Lösung (Hybrid Cloud Extension) empfohlen, um Ihre lokale Workload zu Ihrer privaten AVS-Cloud (Azure VMware Solution) zu migrieren. Weitere Informationen
    - Unbekannt: Für Server, die über eine CSV-Datei importiert wurden, ist das Standardmigrationstool unbekannt. Für VMware-Server empfiehlt sich jedoch die Verwendung der VMware HCX-Lösung (Hybrid Cloud Extension).
4. Klicken Sie auf einen AVS-Bereitschaftsstatus. Sie können Details zur Serverbereitschaft sowie Serverdetails wie Compute-, Speicher- und Netzwerkeinstellungen anzeigen.

### <a name="review-cost-estimates"></a>Überprüfen der Kostenschätzungen

Die Zusammenfassung der Bewertung enthält die geschätzten Compute- und Speicherkosten für die Serverausführung in Azure. 

1. Überprüfen Sie die monatlichen Gesamtkosten. Die Kosten für alle Server in der bewerteten Gruppe werden aggregiert.

    - Kostenschätzungen basieren auf der Anzahl von benötigten AVS-Knoten unter Berücksichtigung der Ressourcenanforderungen aller Server insgesamt.
    - Da die Preise für AVS pro Knoten angegeben werden, enthalten die Gesamtkosten keine Verteilung der Compute- und Speicherkosten.
    - Die Kostenschätzung gilt für die Ausführung der lokalen Server in AVS. Bei der AVS-Bewertung werden keine PaaS- oder SaaS-Kosten berücksichtigt.

2. Überprüfen Sie den geschätzten monatlichen Speicheraufwand. In der Ansicht werden die aggregierten Speicherkosten für die bewertete Gruppe angezeigt, aufgeschlüsselt nach verschiedenen Arten von Speicherdatenträgern. 
3. Sie können einen Drilldown ausführen, um Kostendetails für bestimmte Server anzuzeigen.

### <a name="review-confidence-rating"></a>Prüfen der Zuverlässigkeitsstufe

Von der Serverbewertung wird für leistungsbezogene Bewertungen eine Zuverlässigkeitsstufe zugewiesen. Die Bewertung kann einen Wert zwischen einem Stern (am niedrigsten) und fünf Sternen (am höchsten) aufweisen.

Anhand der Zuverlässigkeitsstufe können Sie die Zuverlässigkeit der Größenempfehlungen in der Bewertung besser einschätzen. Die Stufe basiert auf der Verfügbarkeit von Datenpunkten, die für die Berechnung der Bewertung erforderlich sind.

> [!NOTE]
> Zuverlässigkeitsstufen werden nicht zugewiesen, wenn Sie eine Bewertung basierend auf einer CSV-Datei erstellen.

Die Zuverlässigkeitsstufen lauten wie unten angegeben.

**Verfügbarkeit von Datenpunkten** | **Zuverlässigkeitsstufe**
--- | ---
0 % bis 20 % | 1 Stern
21 % bis 40 % | 2 Sterne
41 % bis 60 % | 3 Sterne
61 % bis 80 % | 4 Sterne
81 % bis 100 % | 5 Sterne

Informieren Sie sich über [Zuverlässigkeitsstufen](concepts-assessment-calculation.md#confidence-ratings-performance-based).

## <a name="next-steps"></a>Nächste Schritte

- Suchen Sie nach Serverabhängigkeiten, indem Sie die [Zuordnung von Abhängigkeiten](concepts-dependency-visualization.md) verwenden.
- Richten Sie die Abhängigkeitszuordnung [ohne Agent](how-to-create-group-machine-dependencies-agentless.md) oder [mit Agent](how-to-create-group-machine-dependencies.md) ein.
