---
title: Erstellen einer Azure App Service-Bewertung
description: Informationen zum Bewerten von Web-Apps für die Migration zu Azure App Service
author: rashi-ms
ms.author: rajosh
ms.topic: tutorial
ms.date: 07/28/2021
ms.openlocfilehash: 39dfd96a92dcce152a6a8ff5afdd1c18b2f1d754
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121784652"
---
# <a name="create-an-azure-app-service-assessment"></a>Erstellen einer Azure App Service-Bewertung

Im Rahmen Ihrer Migrationsjourney zu Azure bewerten Sie Ihre lokalen Workloads, um die Cloudbereitschaft zu messen, Risiken zu identifizieren und Kosten und Komplexität zu schätzen.
In diesem Artikel wird gezeigt, wie Sie ermittelte ASP.NET-Web-Apps für die Migration zu Azure App Service mithilfe des Tools „Azure Migrate: Ermittlung und Bewertung“ bewerten.

> [!Note]
> Die Ermittlung und Bewertung von ASP.NET-Web-Apps, die in Ihrer VMware-Umgebung ausgeführt werden, befindet sich nun in der Vorschauphase. Erste Schritte mit [diesem Tutorial](tutorial-discover-vmware.md). Falls Sie dieses Feature in einem vorhandenen Projekt ausprobieren möchten, sollten Sie sicherstellen, dass Sie die in diesem Artikel beschriebenen [Voraussetzungen](how-to-discover-sql-existing-project.md) erfüllt haben.

## <a name="before-you-start"></a>Vorbereitung

- Stellen Sie sicher, dass Sie ein Azure Migrate-Projekt [erstellt](./create-manage-projects.md) und das Azure Migrate-Tool für die Ermittlung und Bewertung hinzugefügt haben.
- Zum Erstellen einer Bewertung müssen Sie eine Azure Migrate-Appliance für [VMware](how-to-set-up-appliance-vmware.md) einrichten. Die [Appliance](migrate-appliance.md) ermittelt lokale Server und sendet Metadaten und Leistungsdaten an Azure Migrate. Dieselbe Appliance ermittelt ASP.NET-Web-Apps, die in Ihrer VMware-Umgebung ausgeführt werden.

## <a name="azure-app-service-assessment-overview"></a>Azure App Service-Bewertung – Übersicht

Eine Azure App Service-Bewertung bietet ein Kriterium für die Dimensionierung:

**Größenkriterien** | **Details** | **Daten**
--- | --- | ---
**Konfigurationsbasiert** | Bewertungen, die Empfehlungen auf der Grundlage der erfassten Konfigurationsdaten liefern | Bei der Azure App Service-Bewertung werden nur Konfigurationsdaten für die Bewertungsberechnung berücksichtigt. Leistungsdaten für Web-Apps werden nicht erfasst.

[Weitere Informationen](concepts-azure-webapps-assessment-calculation.md) zu Azure App Service-Bewertungen.

## <a name="run-an-assessment"></a>Durchführen einer Bewertung

Führen Sie eine Bewertung wie folgt aus:

1. Klicken Sie auf der Seite **Übersicht** unter **Server, Datenbanken und Web-Apps** auf **Entdecken, bewerten und migrieren**.
    :::image type="content" source="./media/tutorial-assess-webapps/discover-assess-migrate.png" alt-text="Seite „Übersicht“ für Azure Migrate":::
2. Klicken Sie unter **Azure Migrate: Ermittlung und Bewertung** auf **Bewerten**, und wählen Sie **Azure App Service** als Bewertungstyp aus.
    :::image type="content" source="./media/tutorial-assess-webapps/assess.png" alt-text="Dropdownliste für die Auswahl von Azure App Service als Bewertungstyp":::
3. Unter **Bewertung erstellen** sehen Sie, dass **Azure App Service** bereits als Bewertungstyp ausgewählt ist und für die Ermittlungsquelle die Standardeinstellung **Von Azure Migrate-Appliance erkannte Computer** angezeigt wird.
4. Klicken Sie auf **Bearbeiten**, um die Eigenschaften für die Bewertung zu überprüfen.

    :::image type="content" source="./media/tutorial-assess-webapps/assess-webapps.png" alt-text="Schaltfläche „Bearbeiten“ zum Anpassen der Bewertungseigenschaften":::

1. Die folgenden Eigenschaften sind in den Azure App Service-Bewertungseigenschaften enthalten:

    | **Eigenschaft** | **Details** |
    | --- | --- |
    | **Zielstandort** | Die Azure-Region, zu der die Migration durchgeführt werden soll. Empfehlungen zur Azure App Service-Konfiguration und zu den Kosten basieren auf dem von Ihnen angegebenen Standort. |
    | **Isolation erforderlich** | Wählen Sie „Ja“ aus, wenn Ihre Web-Apps in einer privaten und dedizierten Umgebung in einem Azure-Rechenzentrum mit VMs der Dv2-Serie mit schnelleren Prozessoren, SSD-Speicher und doppelt so großem Arbeitsspeicher/Kern-Verhältnis im Vergleich zu Standardplänen ausgeführt werden sollen. |
    | **Reservierte Instanzen** | Geben Sie reservierte Instanzen an, damit diese bei Kostenschätzungen in der Bewertung berücksichtigt werden.<br/><br/> Wenn Sie eine Option für eine reservierte Instanz auswählen, können Sie nicht „Rabatt (%)“ angeben. |
    | **Angebot** | Das [Azure-Angebot](https://azure.microsoft.com/support/legal/offer-details/), für das Sie registriert sind. Bei der Bewertung werden die Kosten für dieses Angebot geschätzt. |
    | **Währung** | Die Rechnungswährung für Ihr Konto. |
    | **Rabatt (%)** | Abonnementspezifische Rabatte, die Sie zusätzlich zum Azure-Angebot erhalten. Die Standardeinstellung ist 0 %. |
    | **EA-Abonnement** | Mit dieser Eigenschaft wird festgelegt, dass ein EA-Abonnement (Enterprise Agreement) für die Kostenschätzung verwendet wird. Dabei wird der Rabatt berücksichtigt, der für das Abonnement gilt. <br/><br/> Übernehmen Sie die Standardeinstellungen für reservierte Instanzen und „Rabatt (%)“-Eigenschaften. |

    :::image type="content" source="./media/tutorial-assess-webapps/webapps-assessment-properties.png" alt-text="App Service-Bewertungseigenschaften":::

1. Klicken Sie unter **Bewertung erstellen**  auf „Weiter“.
1. Geben Sie in **Server für die Bewertung auswählen** > **Bewertungsname** einen Namen für die Bewertung an.
1. Wählen Sie unter **Gruppe auswählen oder erstellen** die Option **Neu erstellen** aus, und geben Sie einen Namen für die Gruppe ein.
1. Wählen Sie die Appliance und dann die Server aus, die Sie der Gruppe hinzufügen möchten. Klicken Sie anschließend auf Weiter.
1. Sehen Sie sich unter **Überprüfen + Bewertung erstellen** die Bewertungsdetails an, und klicken Sie auf Bewertung erstellen, um die Gruppe zu erstellen und die Bewertung durchzuführen.
1. Wechseln Sie nach dem Erstellen der Bewertung zur Kachel **Server, Datenbanken und Web-Apps** > **Azure Migrate: Ermittlung und Bewertung**, und aktualisieren Sie die Kacheldaten, indem Sie auf der Kachel auf die Option „Aktualisieren“ klicken. Warten Sie, bis die Daten aktualisiert wurden.
     :::image type="content" source="./media/tutorial-assess-webapps/tile-refresh.png" alt-text="Aktualisieren der Daten des Tools „Ermittlung und Bewertung“":::
1. Klicken Sie auf die Zahl neben der Azure App Service-Bewertung.
     :::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-navigation.png" alt-text="Navigation zur erstellten Bewertung":::
1. Klicken Sie auf den Namen der Bewertung, die Sie anzeigen möchten.

## <a name="review-an-assessment"></a>Überprüfen einer Bewertung

**Zeigen Sie eine Bewertung wie folgt an**:

1. Wählen Sie **Server, Datenbanken und Web-App** > **Azure Migrate: Ermittlung und Bewertung** aus, und klicken Sie auf die Zahl neben der Azure App Service-Bewertung.
2. Klicken Sie auf den Namen der Bewertung, die Sie anzeigen möchten.
      :::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-summary.png" alt-text="App Service-Bewertungsübersicht":::
3. Sehen Sie sich die Zusammenfassung der Bewertung an. Sie können auch die Bewertungseigenschaften bearbeiten oder die Bewertung neu berechnen.

#### <a name="azure-app-service-readiness"></a>Azure App Service-Bereitschaft

Hier wird die Verteilung der bewerteten Web-Apps angezeigt. Sie können einen Drilldown ausführen, um sich mit den Details zu Migrationsproblemen und -warnungen vertraut zu machen, die Sie vor der Migration zu Azure App Service beheben können. [Weitere Informationen](concepts-azure-webapps-assessment-calculation.md) Sie können auch die empfohlene App Service-SKU für die Migration zu Azure App Service überprüfen.

#### <a name="azure-app-service-cost-details"></a>Details zu Azure App Service-Kosten

Für einen [App Service-Plan](/azure/app-service/overview-hosting-plans) fällt eine [Gebühr](https://azure.microsoft.com/pricing/details/app-service/windows/) für die genutzten Computeressourcen an.

### <a name="review-readiness"></a>Überprüfen der Bereitschaft

1. Klicken Sie auf **Azure App Service-Bereitschaft**.
    :::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-readiness.png" alt-text="Azure App Service-Bereitschaft: Details":::
1. Überprüfen Sie die Spalte für die Azure App Service-Bereitschaft in der Tabelle für die bewerteten Web-Apps:
    1. Wenn keine Kompatibilitätsprobleme gefunden werden, wird die Bereitschaft für den Zielbereitstellungstyp als **Bereit** gekennzeichnet.
    1. Wenn unkritische Kompatibilitätsprobleme vorhanden sind, z. B. heruntergestufte oder nicht unterstützte Funktionen, die die Migration zu einem bestimmten Zielbereitstellungstyp nicht blockieren, wird die Bereitschaft als **Bereit mit Bedingungen** mit einem Hyperlink zu **Warnungsdetails** und Anleitungen zur empfohlenen Korrektur gekennzeichnet.
    1. Wenn Kompatibilitätsprobleme vorliegen, die die Migration zu einem bestimmten Zielbereitstellungstyp blockieren können, erfolgt die Kennzeichnung **Nicht bereit** mit **Problemdetails** und Anleitung zur empfohlenen Korrektur.
    1. Wenn die Ermittlung noch läuft oder Ermittlungsprobleme für eine Web-App aufgetreten sind, wird die Bereitschaft als **Unbekannt** gekennzeichnet, da die Bewertung die Bereitschaft für diese SQL-Instanz nicht berechnen konnte.
1. Überprüfen Sie die empfohlene SKU für die Web-App, die Sie über die folgende Matrix ermitteln können:

**Isolation erforderlich** | **Reservierte Instanz** | **App Service-Plan/SKU**
--- | --- | ---
Ja  | Ja | I1
Ja  | Nein  | I1
Nein  | Ja | P1v3
Nein  | Nein | P1v2

**Azure App Service-Bereitschaft** | **Ermitteln der App Service-SKU** | **Ermitteln der Kostenschätzungen**
--- | --- | ---
Bereit  | Ja | Ja
Bereit mit Bedingungen  | Ja  | Ja
Nicht bereit.  | Nein | Nein
Unknown  | Nein | Nein

1. Klicken Sie in der Tabelle auf den Hyperlink für den App Service-Plan, um die Details des App Service-Plans, z. B. Computeressourcen, und andere Web-Apps anzuzeigen, die Teil desselben Plans sind.

### <a name="review-cost-estimates"></a>Überprüfen der Kostenschätzungen

Die Bewertungszusammenfassung zeigt die geschätzten monatlichen Kosten für das Hosten Ihrer Web-Apps in App Service an. In App Service zahlen Sie Gebühren pro App Service-Plan und nicht pro Web-App. Es können eine oder mehrere Apps für die Ausführung auf denselben Computeressourcen (oder in demselben App Service-Plan) konfiguriert werden. Alle Apps, die Sie in diesen App Service-Plan einfügen, werden auf diesen Computeressourcen ausgeführt, wie in Ihrem App Service-Plan definiert.
Um die Kosten zu optimieren, ordnet die Azure Migrate-Bewertung jedem empfohlenen App Service-Plan mehrere Web-Apps zu. Die Anzahl der Web-Apps, die den einzelnen Planinstanzen zugeordnet werden, sind in der folgenden Tabelle aufgeführt.

**App Service-Plan** | **Web-Apps pro App Service-Plan**
--- | ---
I1  | 8
P1v2  | 8
P1v3  | 16

   :::image type="content" source="./media/tutorial-assess-webapps/assessment-webapps-cost.png" alt-text="Kostendetails":::

## <a name="next-steps"></a>Nächste Schritte

- [Weitere Informationen](concepts-azure-webapps-assessment-calculation.md) zur Berechnung von Azure App Service-Bewertungen.