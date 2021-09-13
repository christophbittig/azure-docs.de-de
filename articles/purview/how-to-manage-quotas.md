---
title: Verwalten von Ressourcen und Kontingenten
titleSuffix: Azure Purview
description: Enthält eine Beschreibung der Kontingente und Grenzwerte für Azure Purview-Ressourcen und der Anforderung von Kontingenterhöhungen.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 422c096533e8b470446a29b6d45f9127d963f913
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/18/2021
ms.locfileid: "122396530"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-purview"></a>Verwalten und Erhöhen der Kontingente für Ressourcen mit Azure Purview
 
Azure Purview ist ein Clouddienst, der von Datenbenutzern verwendet werden kann. Mithilfe von Azure Purview können Sie die Datengovernance in Ihrem gesamten Datenbestand in Cloud- und lokalen Umgebungen zentral verwalten. Der Dienst ermöglicht Business Analysts, mithilfe aussagekräftiger Geschäftsbedingungen nach relevanten Daten zu suchen. Wenden Sie sich an den Support, um die Grenzwerte bis zum Höchstwert für Ihr Abonnement zu erhöhen.
 
## <a name="azure-purview-limits"></a>Azure Purview-Grenzwerte
 
|**Ressource**|  **Standardlimit**  |**Maximales Limit**|
|---|---|---|
|Purview-Konten pro Region und Mandant (alle Abonnements kombiniert)|3|Kontaktieren des Supports|
|Zum Scannen verfügbare virtuelle Kerne pro Konto*|160|160|
|Gleichzeitige Scans pro Konto an einem bestimmten Punkt. Der Grenzwert basiert auf dem Typ der überprüften Datenquellen*|5 | 10 |
|Maximale Zeit, für die eine Überprüfung ausgeführt werden kann|7 Tage|7 Tage|
|[Data Map-Kapazitätseinheit](concept-elastic-data-map.md) (Capacity Unit, CU) |1 CU (25 Vorgänge/Sekunde Durchsatz und 2 GB Metadatenspeicher) | 100 CUs (wenden Sie sich an den Support, wenn Sie eine höhere CU-Anzahl benötigen)|
|Durchsatz für Data Map-Vorgänge |25 Vorgänge/Sekunde für jede Kapazitätseinheit | 2\.500 Vorgänge/Sekunde für 100 CUs (wenden Sie sich an den Support, wenn Sie mehr Durchsatz benötigen)| 
|Data Map-Speicher |2 GB für jede Kapazitätseinheit | 200 GB für 100 CUs (wenden Sie sich an den Support, wenn Sie mehr Speicher benötigen) |
|Data Map-Elastizitätsfenster | 1–8 CUs (Data Map kann basierend auf dem Durchsatz innerhalb des Elastizitätsfensters-hoch- und herunterskaliert werden) | Wenden Sie sich an den Support, um ein größeres Elastizitätsfenster zu erhalten. |
|Ressourcenumfang pro Konto|100 Mio. physische Ressourcen |Kontaktieren des Supports|
|Maximale Größe einer Ressource in einem Katalog|2 MB|2 MB|
|Maximale Länge eines Ressourcennamens und Klassifizierungsnamens|4 KB|4 KB|
|Maximale Länge des Ressourceneigenschaftsnamens und -werts|32 KB|32 KB|
|Maximale Länge des Klassifizierungsattributnamens und -werts|32 KB|32 KB|
|Maximale Anzahl von Glossarbegriffen pro Konto|100k|100k|
 
*Szenarios mit selbstgehosteter Integrationslaufzeit liegen außerhalb des Bereichs der in der obigen Tabelle definierten Grenzwerte. 
 
## <a name="next-steps"></a>Nächste Schritte
 
> [!div class="nextstepaction"]
>[Konzept: elastische Data Map in Azure Purview](concept-elastic-data-map.md)

> [!div class="nextstepaction"]
>[Tutorial: Überprüfen von Daten mit Azure Purview](tutorial-scan-data.md)

> [!div class="nextstepaction"]
>[Tutorial: Navigieren zur Startseite und Suchen nach einer Ressource](tutorial-asset-search.md)

> [!div class="nextstepaction"]
>[Tutorial: Durchsuchen von Ressourcen und Anzeigen ihrer Herkunft](tutorial-browse-and-view-lineage.md)
