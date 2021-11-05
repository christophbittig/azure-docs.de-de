---
title: Threat Intelligence-Berichte von Microsoft Defender für Cloud | Microsoft-Dokumentation
description: Auf dieser Seite erfahren Sie, wie Sie Threat Intelligence-Berichte von Microsoft Defender für Cloud im Rahmen einer Untersuchung nutzen, um weitere Informationen zu einer Sicherheitswarnung zu erhalten.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/15/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 31a3a123356e3417aa80672c305d516651c9e23c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131096063"
---
# <a name="microsoft-defender-for-cloud-threat-intelligence-report"></a>Threat Intelligence-Berichte von Microsoft Defender für Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Auf dieser Seite wird erläutert, wie Sie anhand von Threat Intelligence-Berichten von Microsoft Defender für Cloud mehr über eine Bedrohung erfahren, die eine Sicherheitswarnung ausgelöst hat.

## <a name="what-is-a-threat-intelligence-report"></a>Was ist ein Bericht mit Bedrohungsinformationen?

Im Rahmen des Bedrohungsschutzes von Defender für Cloud werden von Ihren Azure-Ressourcen, aus dem Netzwerk und von verbundenen Partnerlösungen stammende Sicherheitsinformationen überwacht. Diese Informationen, bei denen es sich häufig um korrelierende Informationen aus mehreren Quellen handelt, werden analysiert, um Bedrohungen zu identifizieren. Weitere Informationen finden Sie unter [Erkennen von und Reagieren auf Bedrohungen mit Microsoft Defender für Cloud](alerts-overview.md#detect-threats).

Wenn Defender für Cloud eine Bedrohung erkennt, wird eine [Sicherheitswarnung](managing-and-responding-alerts.md) mit ausführlichen Informationen zum betreffenden Ereignis sowie mit Vorschlägen zur Beseitigung ausgelöst. Um die für Incidents zuständigen Teams bei der Untersuchung und Beseitigung von Bedrohungen zu unterstützen, bietet Defender für Cloud Berichte mit detaillierten Informationen zu den erkannten Bedrohungen. Die Berichte umfassen folgende Informationen:

* Identität oder Verbindungen des Angreifers (sofern verfügbar)
* Ziele der Angreifer
* Aktuelle und vergangene Angriffskampagnen (sofern verfügbar)
* Taktiken, Tools und Verfahren der Angreifer
* Zugehörige IoCs (Indicators of Compromise) wie URLs und Dateihashes
* Viktimologie (also die betroffene Branche und die geografische Verbreitung), um zu ermitteln, ob Ihre Azure-Ressourcen gefährdet sind
* Informationen zu Risikominderung und Beseitigung

> [!NOTE]
> Die Informationsmenge variiert von Bericht zu Bericht. Der Detailgrad hängt von der Aktivität und der Verbreitung der Schadsoftware ab.

Defender für Cloud bietet drei Arten von Bedrohungsberichten, die abhängig vom jeweiligen Angriff variieren können. Folgende Berichte stehen zur Verfügung:

* **Aktivitätsgruppenbericht**: Enthält detaillierte Informationen zu Angreifern sowie zu ihren Zielen und Taktiken.
* **Kampagnenbericht:** Konzentriert sich auf Details zu bestimmten Angriffskampagnen.
* **Zusammenfassender Bedrohungsbericht:** Enthält alle Elemente der beiden vorherigen Berichte.

Diese Informationen sind hilfreich bei der Reaktion auf Incidents, um im Rahmen einer laufenden Untersuchung die Angriffsquelle, die Beweggründe des Angreifers und eine geeignete Vorgehensweise zur zukünftigen Vermeidung des Problems zu ermitteln.

## <a name="how-to-access-the-threat-intelligence-report"></a>Wie greife ich auf den Bericht mit Bedrohungsinformationen zu?

1. Öffnen Sie im Menü von Defender für Cloud die Seite **Sicherheitswarnungen**.
1. Wählen Sie eine Warnung aus. 

    Eine Detailseite wird geöffnet und zeigt weitere Informationen zu der Warnung an. Im Folgenden sehen Sie die Detailseite zur Warnung **Ransomware-Indikatoren erkannt**.

    [![Detailseite zur Warnung „Ransomware-Indikatoren erkannt“](media/threat-intelligence-reports/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/threat-intelligence-reports/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. Wählen Sie den Link zum Bericht aus. Damit wird in Ihrem Standardbrowser eine PDF-Datei geöffnet.

    [![Detailseite zur Warnung „Potenziell unsichere Aktion“](media/threat-intelligence-reports/threat-intelligence-report.png)](media/threat-intelligence-reports/threat-intelligence-report.png#lightbox)

    Sie können den PDF-Bericht auch herunterladen. 

    >[!TIP]
    > Der Informationsgehalt von Sicherheitswarnungen ist abhängig von der Art der jeweiligen Warnung.

## <a name="next-steps"></a>Nächste Schritte

Auf dieser Seite wurde erläutert, wie Sie Berichte mit Bedrohungsinformationen öffnen, wenn Sie Sicherheitswarnungen untersuchen. Zugehörige Informationen finden Sie auf den folgenden Seiten:

* [Verwalten von und Reagieren auf Sicherheitswarnungen in Microsoft Defender für Cloud](managing-and-responding-alerts.md). Hier erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
* [Behandeln von Sicherheitsvorfällen in Microsoft Defender für Cloud](incidents.md)
