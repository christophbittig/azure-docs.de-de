---
title: Azure Security Center-Bericht mit Bedrohungsinformationen | Microsoft-Dokumentation
description: Auf dieser Seite erfahren Sie, wie Sie Azure Security Center-Berichte mit Bedrohungsinformationen im Rahmen einer Untersuchung nutzen, um weitere Informationen zu einer Sicherheitswarnung zu ermitteln.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/15/2020
ms.author: memildin
ms.openlocfilehash: b2a867c99e79b2ddfe9053589a1ebac9ab569490
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237019"
---
# <a name="azure-security-center-threat-intelligence-report"></a>Azure Security Center-Bericht mit Bedrohungsinformationen

Auf dieser Seite wird erläutert, wie Sie anhand von Azure Security Center-Berichten mit Bedrohungsinformationen mehr über eine Bedrohung erfahren, die eine Sicherheitswarnung ausgelöst hat.


## <a name="what-is-a-threat-intelligence-report"></a>Was ist ein Bericht mit Bedrohungsinformationen?

Im Zuge des Bedrohungsschutzes von Security Center werden von Ihren Azure-Ressourcen, aus dem Netzwerk und von verbundenen Partnerlösungen stammende Sicherheitsinformationen überwacht. Diese Informationen, bei denen es sich häufig um korrelierende Informationen aus mehreren Quellen handelt, werden analysiert, um Bedrohungen zu identifizieren. Weitere Informationen finden Sie unter [Erkennen von und Reagieren auf Bedrohungen mit Azure Security Center](security-center-alerts-overview.md#detect-threats).

Wenn Security Center eine Bedrohung erkennt, wird eine [Sicherheitswarnung](security-center-managing-and-responding-alerts.md) mit ausführlichen Informationen zum betreffenden Ereignis sowie mit Vorschlägen zur Beseitigung ausgelöst. Um die für Incidents zuständigen Teams bei der Untersuchung und Beseitigung von Bedrohungen zu unterstützen, bietet Security Center Berichte mit detaillierten Informationen zu den erkannten Bedrohungen. Die Berichte umfassen folgende Informationen:

* Identität oder Verbindungen des Angreifers (sofern verfügbar)
* Ziele der Angreifer
* Aktuelle und vergangene Angriffskampagnen (sofern verfügbar)
* Taktiken, Tools und Verfahren der Angreifer
* Zugehörige IoCs (Indicators of Compromise) wie URLs und Dateihashes
* Viktimologie (also die betroffene Branche und die geografische Verbreitung), um zu ermitteln, ob Ihre Azure-Ressourcen gefährdet sind
* Informationen zu Risikominderung und Beseitigung

> [!NOTE]
> Die Informationsmenge variiert von Bericht zu Bericht. Der Detailgrad hängt von der Aktivität und der Verbreitung der Schadsoftware ab.

Security Center bietet drei Arten von Bedrohungsberichten, die abhängig vom jeweiligen Angriff variieren können. Folgende Berichte stehen zur Verfügung:

* **Aktivitätsgruppenbericht**: Enthält detaillierte Informationen zu Angreifern sowie zu ihren Zielen und Taktiken.
* **Kampagnenbericht:** Konzentriert sich auf Details zu bestimmten Angriffskampagnen.
* **Zusammenfassender Bedrohungsbericht:** Enthält alle Elemente der beiden vorherigen Berichte.

Diese Informationen sind hilfreich bei der Reaktion auf Incidents, um im Rahmen einer laufenden Untersuchung die Angriffsquelle, die Beweggründe des Angreifers und eine geeignete Vorgehensweise zur zukünftigen Vermeidung des Problems zu ermitteln.



## <a name="how-to-access-the-threat-intelligence-report"></a>Wie greife ich auf den Bericht mit Bedrohungsinformationen zu?

1. Öffnen Sie auf der Security Center-Randleiste die Seite **Sicherheitswarnungen**.
1. Wählen Sie eine Warnung aus. 
    Eine Detailseite wird geöffnet und zeigt weitere Informationen zu der Warnung an. Im Folgenden sehen Sie die Detailseite zur Warnung **Ransomware-Indikatoren erkannt**.

    [![Detailseite zur Warnung „Ransomware-Indikatoren erkannt“](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png)](media/security-center-threat-report/ransomware-indicators-detected-link-to-threat-intel-report.png#lightbox)

1. Wählen Sie den Link zum Bericht aus. Damit wird in Ihrem Standardbrowser eine PDF-Datei geöffnet.

    [![Detailseite zur Warnung „Potenziell unsichere Aktion“](media/security-center-threat-report/threat-intelligence-report.png)](media/security-center-threat-report/threat-intelligence-report.png#lightbox)

    Sie können den PDF-Bericht auch herunterladen. 

    >[!TIP]
    > Der Informationsgehalt von Sicherheitswarnungen ist abhängig von der Art der jeweiligen Warnung.



## <a name="next-steps"></a>Nächste Schritte

Auf dieser Seite wurde erläutert, wie Sie Berichte mit Bedrohungsinformationen öffnen, wenn Sie Sicherheitswarnungen untersuchen. Zugehörige Informationen finden Sie auf den folgenden Seiten:

* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center](security-center-managing-and-responding-alerts.md). Hier erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
* [Behandeln von Sicherheitsincidents in Azure Security Center](security-center-incident.md)