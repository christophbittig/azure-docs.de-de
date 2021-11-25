---
title: Arbeitsmappenkatalog in Microsoft Defender für Cloud
description: Es wird beschrieben, wie Sie mit dem integrierten Azure Monitor-Arbeitsmappenkatalog umfassende interaktive Berichte zu Ihren Defender für Cloud-Daten erstellen.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 07c4a521764e404b7c55dd6ad4c7d30898e448a6
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526161"
---
# <a name="create-rich-interactive-reports-of-defender-for-cloud-data"></a>Erstellen umfassender interaktiver Berichte zu Defender für Cloud-Daten

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[Azure Monitor-Arbeitsmappen](../azure-monitor/visualize/workbooks-overview.md) bieten einen flexiblen Bereich für die Datenanalyse und die Erstellung umfassender visueller Berichte innerhalb des Microsoft Azure-Portals. Mit ihnen können Sie mehrere Datenquellen in Azure nutzen und sie zu vereinheitlichten interaktiven Oberflächen kombinieren.

Arbeitsmappen bieten eine Vielzahl von Funktionen zur Visualisierung Ihrer Azure-Daten. Detaillierte Beispiele für die einzelnen Visualisierungstypen finden Sie in den [Visualisierungsbeispielen und der Dokumentation](../azure-monitor/visualize/workbooks-text-visualizations.md). 

In Microsoft Defender für Cloud können Sie den Sicherheitsstatus Ihres Unternehmens anhand der integrierten Arbeitsmappen nachverfolgen. Sie können auch benutzerdefinierte Arbeitsmappen erstellen, um eine breite Palette von Daten aus Defender für Cloud oder anderen unterstützten Datenquellen anzuzeigen.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Arbeitsmappe „Sicherheitsbewertung im Zeitverlauf“":::

## <a name="availability"></a>Verfügbarkeit

| Aspekt                          | Details                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| Status des Release:                  | Allgemeine Verfügbarkeit (General Availability, GA)                                                                                                                    |
| Preise:                        | Kostenlos                                                                                                                                         |
| Erforderliche Rollen und Berechtigungen: | Um Arbeitsmappen zu speichern, müssen Sie mindestens über die Berechtigung [Arbeitsmappenmitwirkender](../role-based-access-control/built-in-roles.md#workbook-contributor) für die Zielressourcengruppe verfügen. |
| Clouds:                         | :::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: National (Azure Government, Azure China 21Vianet) |
|                                 |                                                                                                                                              |

## <a name="workbooks-gallery-in-microsoft-defender-for-cloud"></a>Arbeitsmappenkatalog in Microsoft Defender für Cloud

Mit der integrierten Funktionalität für Azure-Arbeitsmappen erleichtert Microsoft Defender für Cloud die Erstellung eigener interaktiver Arbeitsmappen. Defender für Cloud enthält auch einen Katalog mit den folgenden Arbeitsmappen, die von Ihnen angepasst werden können:

- [Arbeitsmappe „Sicherheitsbewertung im Zeitverlauf“](#use-the-secure-score-over-time-workbook): Nachverfolgen der Bewertungen Ihrer Abonnements und der Änderungen von Empfehlungen für Ihre Ressourcen
- [Arbeitsmappe „Systemupdates“](#use-the-system-updates-workbook): Anzeigen fehlender Systemupdates beispielsweise nach Ressource, Betriebssystem oder Schweregrad
- [Arbeitsmappe „Ergebnisse der Sicherheitsrisikobewertung“](#use-the-vulnerability-assessment-findings-workbook): Anzeigen der Ergebnisse von Sicherheitsrisikoüberprüfungen für Ihre Azure-Ressourcen
- [Arbeitsmappe „Konformität im Zeitverlauf“](#use-the-compliance-over-time-workbook): Anzeigen des Konformitätsstatus eines Abonnements mit den von Ihnen ausgewählten gesetzlichen oder branchenspezifischen Standards 

:::image type="content" source="media/custom-dashboards-azure-workbooks/workbooks-gallery-security-center.png" alt-text="Katalog mit integrierten Arbeitsmappen in Microsoft Defender für Cloud":::

Wählen Sie eine der bereitgestellten Arbeitsmappen aus, oder erstellen Sie eine eigene.

> [!TIP]
> Verwenden Sie die Schaltfläche **Bearbeiten**, um die bereitgestellten Arbeitsmappen Ihren Anforderungen entsprechend anzupassen. Wenn Sie mit der Bearbeitung fertig sind, wählen Sie **Speichern** aus, und Ihre Änderungen werden in einer neuen Arbeitsmappe gespeichert.
> 
> :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-supplied-workbooks.png" alt-text="Bearbeiten der bereitgestellten Arbeitsmappen, um sie an Ihre speziellen Anforderungen anzupassen":::

### <a name="use-the-secure-score-over-time-workbook"></a>Verwenden der Arbeitsmappe „Sicherheitsbewertung im Zeitverlauf“

In dieser Arbeitsmappe werden Sicherheitsbewertungsdaten aus Ihrem Log Analytics-Arbeitsbereich verwendet. Diese Daten müssen aus dem Tool für den fortlaufenden Export exportiert werden, wie unter [Konfigurieren des fortlaufenden Exports auf den Seiten für Defender für Cloud im Azure-Portal](continuous-export.md?tabs=azure-portal) beschrieben.

Wenn Sie den fortlaufenden Export einrichten, legen Sie die Exporthäufigkeit sowohl auf **Streamingupdates** als auch auf **Momentaufnahmen** fest.

:::image type="content" source="media/custom-dashboards-azure-workbooks/export-frequency-both.png" alt-text="Für die Arbeitsmappe „Sicherheitsbewertung im Zeitverlauf“ müssen Sie diese beiden Optionen aus den Einstellungen für die Exporthäufigkeit in der Konfiguration für den fortlaufenden Export auswählen.":::

> [!NOTE]
> Momentaufnahmen werden wöchentlich exportiert. Daher müssen Sie mindestens eine Woche auf den Export der ersten Momentaufnahme warten, bevor Sie Daten in dieser Arbeitsmappe anzeigen können.

> [!TIP]
> Verwenden Sie zum Konfigurieren des fortlaufenden Exports in Ihrer gesamten Organisation die bereitgestellten Azure Policy „DeployIfNotExist“-Richtlinien, die unter [Konfigurieren des fortlaufenden Exports in großem Maßstab](continuous-export.md?tabs=azure-policy) beschrieben werden.

Die Arbeitsmappe „Sicherheitsbewertung im Zeitverlauf“ enthält fünf Diagramme für die Abonnements, die den ausgewählten Arbeitsbereichen Bericht erstatten:

|Graph  |Beispiel  |
|---------|---------|
|**Bewertungstrends für die letzte Woche und den Monat**<br>Verwenden Sie diesen Abschnitt, um die aktuelle Bewertung und allgemeine Bewertungstrends für Ihre Abonnements zu überwachen.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-1.png" alt-text="Sicherheitsbewertungstrends in der integrierten Arbeitsmappe":::|
|**Aggregierte Bewertung für alle ausgewählten Abonnements**<br>Bewegen Sie die Maus über einen beliebigen Punkt in der Trendlinie, um die aggregierte Bewertung an einem bestimmten Datum im ausgewählten Zeitraum anzuzeigen.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-2.png" alt-text="Aggregierte Bewertung für alle ausgewählten Abonnements":::|
|**Empfehlungen mit den fehlerhaftesten Ressourcen**<br>Diese Tabelle hilft Ihnen bei der Auswahl der Empfehlungen, bei denen die meisten Ressourcen über den ausgewählten Zeitraum in „fehlerhaft“ geändert wurden.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-3.png" alt-text="Empfehlungen mit den fehlerhaftesten Ressourcen":::|
|**Bewertungen für bestimmte Sicherheitskontrollen**<br>Bei den Sicherheitskontrollen von Defender für Cloud handelt es sich um logische Gruppierungen von Empfehlungen. Dieses Diagramm zeigt Ihnen auf einen Blick die wöchentlichen Bewertungen für alle Ihre Sicherheitskontrollen.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-4.png" alt-text="Bewertungen für Ihre Sicherheitskontrollen im ausgewählten Zeitraum":::|
|**Ressourcenänderungen**<br>Hier werden die Empfehlungen mit den meisten Ressourcen aufgeführt, deren Status (fehlerfrei, fehlerhaft oder nicht zutreffend) sich im ausgewählten Zeitraum geändert hat. Wählen Sie eine beliebige Empfehlung aus der Liste aus, um eine neue Tabelle mit den spezifischen Ressourcen zu öffnen.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-5.png" alt-text="Empfehlungen mit den meisten Ressourcen, deren Integritätsstatus sich geändert hat":::|
|||

### <a name="use-the-system-updates-workbook"></a>Verwenden der Arbeitsmappe „Systemupdates“

Diese Arbeitsmappe basiert auf der Sicherheitsempfehlung „Auf Ihren Computern müssen Systemupdates installiert sein“.

Die Arbeitsmappe unterstützt Sie bei der Identifizierung von Computern mit ausstehenden Updates.

Sie können die Situation für die ausgewählten Abonnements entsprechend den folgenden Informationen anzeigen:

- Liste der Ressourcen mit ausstehenden Updates
- Liste der Updates, die auf ihren Ressourcen fehlen

:::image type="content" source="media/custom-dashboards-azure-workbooks/system-updates-report.png" alt-text="Defender für Cloud-Arbeitsmappe „Systemupdates“, basierend auf der Sicherheitsempfehlung zu fehlenden Updates":::

### <a name="use-the-vulnerability-assessment-findings-workbook"></a>Verwenden der Arbeitsmappe „Ergebnisse der Sicherheitsrisikobewertung“

Defender für Cloud enthält Sicherheitsrisikoüberprüfungen für Ihre Computer, Container in Containerregistrierungen und SQL Server-Instanzen.

Weitere Informationen zur Verwendung dieser Überprüfungen:

- [Ermitteln von Sicherheitsrisiken mit Bedrohungs- und Sicherheitsrisikoverwaltung von Microsoft](deploy-vulnerability-assessment-tvm.md)
- [Ermitteln von Sicherheitsrisiken mit der integrierten Qualys-Überprüfung](deploy-vulnerability-assessment-vm.md)
- [Überprüfen Ihrer Registrierungsimages auf Sicherheitsrisiken](defender-for-container-registries-usage.md)
- [Überprüfen der SQL-Ressourcen auf Sicherheitsrisiken](defender-for-sql-on-machines-vulnerability-assessment.md)

Die Ergebnisse für die einzelnen Ressourcentypen werden in separaten Empfehlungen angegeben:

- [Sicherheitsrisiken für VMs müssen behoben werden](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/1195afff-c881-495e-9bc5-1486211ae03f) (enthält Ergebnisse aus der Bedrohungs- und Sicherheitsrisikoverwaltung von Microsoft, der integrierten Qualys-Überprüfung und den ggf. konfigurierten [BYOL-Lösungen zur Sicherheitsrisikobewertung](deploy-vulnerability-assessment-byol-vm.md))
- [Sicherheitsrisiken in Azure Container Registry-Images müssen behoben werden (unterstützt von Qualys).](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/dbd0cb49-b563-45e7-9724-889e799fa648)
- [Ermittelte Sicherheitsrisiken für SQL-Datenbanken müssen behoben werden](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/82e20e14-edc5-4373-bfc4-f13121257c37)
- [Ermittelte Sicherheitsrisiken für SQL Server-Instanzen auf Computern müssen behoben werden](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/f97aa83c-9b63-4f9a-99f6-b22c4398f936)

Diese Arbeitsmappe sammelt diese Ergebnisse und organisiert sie nach Schweregrad, Ressourcentyp und Kategorie.

:::image type="content" source="media/custom-dashboards-azure-workbooks/vulnerability-assessment-findings-report.png" alt-text="Bericht zu den Ergebnissen der Sicherheitsrisikobewertung in Defender für Cloud":::


### <a name="use-the-compliance-over-time-workbook"></a>Verwenden der Arbeitsmappe „Konformität im Zeitverlauf“

Microsoft Defender für Cloud vergleicht die Konfiguration Ihrer Ressourcen ständig mit den Anforderungen aus Branchenstandards, Vorschriften und Benchmarks. Zu den integrierten Standards gehören beispielsweise NIST SP 800-53, SWIFT CSP CSCF v2020, Canada Federal PBMM und HIPAA HITRUST. Sie können die spezifischen, für Ihre Organisation relevanten Standards auswählen, indem Sie das Dashboard zur Einhaltung gesetzlicher Bestimmungen verwenden. Weitere Informationen finden Sie unter [Anpassen der Standards in Ihrem Dashboard für die Einhaltung gesetzlicher Bestimmungen](update-regulatory-compliance-packages.md)

Diese Arbeitsmappe verfolgt Ihren Konformitätsstatus im Zeitverlauf mit den verschiedenen Standards nach, die Sie Ihrem Dashboard hinzugefügt haben.

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-select-standards.png" alt-text="Auswählen der Standards für Ihren Bericht zur Konformität im Zeitverlauf":::

Wenn Sie im Übersichtsbereich des Berichts einen Standard auswählen, wird im unteren Bereich eine ausführlichere Aufschlüsselung angezeigt:

:::image type="content" source="media/custom-dashboards-azure-workbooks/compliance-over-time-details.png" alt-text="Detaillierte Aufschlüsselung der Änderungen in Bezug auf einen bestimmten Standard":::

Sie können einen Drilldown bis zur Empfehlungsebene durchführen, um die Ressourcen anzuzeigen, die die einzelnen Kontrollen erfolgreich absolviert oder einen Fehler verursacht haben. 

> [!TIP]
> Für jeden Bereich des Berichts können Sie die Daten mithilfe der Option „In Excel exportieren“ in Excel exportieren.
>
> :::image type="content" source="media/custom-dashboards-azure-workbooks/export-workbook-data.png" alt-text="Exportieren von Daten der Konformitätsarbeitsmappe in Excel":::


## <a name="import-workbooks-from-other-workbook-galleries"></a>Importieren von Arbeitsmappen aus anderen Arbeitsmappenkatalogen

Gehen Sie wie folgt vor, wenn Sie Arbeitsmappen in anderen Azure-Diensten erstellt haben und sie in Ihren Microsoft Defender für Cloud-Arbeitsmappenkatalog verschieben möchten:

1. Öffnen Sie die Zielarbeitsmappe.

1. Wählen Sie auf der Symbolleiste die Option **Bearbeiten** aus.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks.png" alt-text="Bearbeiten einer Azure Monitor-Arbeitsmappe":::

1. Wählen Sie auf der Symbolleiste die Option **</>** aus, um den erweiterten Editor zu öffnen.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-advanced-editor.png" alt-text="Starten des erweiterten Editors zum Abrufen des JSON-Codes der Katalogvorlage":::

1. Kopieren Sie den JSON-Code der Katalogvorlage der Arbeitsmappe.

1. Öffnen Sie den Arbeitsmappenkatalog in Defender für Cloud, und wählen Sie auf der Menüleiste die Option **Neu** aus.
1. Wählen Sie **</>** aus, um den erweiterten Editor zu öffnen.
1. Fügen Sie gesamten JSON-Code der Katalogvorlage ein.
1. Wählen Sie **Übernehmen**.
1. Wählen Sie auf der Symbolleiste die Option **Speichern unter** aus.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-save-as.png" alt-text="Speichern der Arbeitsmappe im Katalog in Defender für Cloud":::

1. Geben Sie die erforderlichen Details zum Speichern der Arbeitsmappe ein:
   1. Einen Namen für die Arbeitsmappe
   2. Die gewünschte Region
   3. Abonnement, Ressourcengruppe und Freigabe (nach Bedarf).

Sie finden Ihre gespeicherte Arbeitsmappe in der Kategorie **Kürzlich geänderte Arbeitsmappen**.


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde die in Defender für Cloud enthaltene Seite mit den integrierten Azure Monitor-Arbeitsmappen mit fertigen Berichten und der Option zum Erstellen eigener benutzerdefinierter, interaktiver Berichte beschrieben.

- Weitere Informationen zu [Azure Monitor-Arbeitsmappen](../azure-monitor/visualize/workbooks-overview.md)
- Für die integrierten Arbeitsmappen werden die Daten aus den Empfehlungen von Defender für Cloud gepullt. Weitere Informationen zu den zahlreichen Sicherheitsempfehlungen finden Sie unter [Sicherheitsempfehlungen – ein Referenzhandbuch](recommendations-reference.md).
