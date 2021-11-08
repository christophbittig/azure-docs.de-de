---
title: Microsoft Defender für Cloud-Dashboard für den Workloadschutz und zugehörige Features
description: Enthält eine Beschreibung der Features des Microsoft Defender für Cloud-Dashboards für den Workloadschutz.
author: memildin
ms.author: memildin
ms.date: 11/02/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: bd34ab6aed35ab9798552837112830e77595795a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131463542"
---
# <a name="the-workload-protections-dashboard"></a>Dashboard für den Workloadschutz

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Dieses Dashboard umfasst Folgendes:

- Einblicke in die Abdeckung von Microsoft Defender für Ihre unterschiedlichen Ressourcentypen
- Links zum Konfigurieren der Advanced Threat Protection-Funktionen
- Onboardingstatus und Agent-Installation
- Warnungen der Bedrohungserkennung 

Wählen Sie im Defender für Cloud-Menü im Abschnitt „Cloud Security“ die Option **Workloadschutz** aus, um auf das Dashboard für den Workloadschutz zuzugreifen.

## <a name="whats-shown-on-the-dashboard"></a>Was wird auf dem Dashboard angezeigt?

:::image type="content" source="./media/workload-protections-dashboard/sample-defender-dashboard-numbered.png" alt-text="Beispiel: Defender für Cloud-Dashboard für Workloadschutz" lightbox="./media/workload-protections-dashboard/sample-defender-dashboard-numbered.png":::

Das Dashboard enthält die folgenden Abschnitte:

1. **Microsoft Defender-Abdeckung:** Hier werden die Ressourcentypen angezeigt, die in Ihrem Abonnement verfügbar und für den Schutz durch Defender für Cloud geeignet sind. Sofern dies relevant ist, können Sie hier auch ein Upgrade durchführen. Wenn Sie alle möglichen berechtigten Ressourcen aktualisieren möchten, wählen Sie **Alle aktualisieren** aus.

2. **Sicherheitswarnungen:** Wenn Defender für Cloud in einem der Bereiche Ihrer Umgebung eine Bedrohung erkennt, wird eine Warnung generiert. Diese Warnungen beschreiben Details zu den betroffenen Ressourcen, empfohlene Problembehandlungsschritte sowie in einigen Fällen eine Option, mit der eine Logik-App als Reaktion ausgelöst werden kann. Durch Auswählen einer beliebigen Stelle in diesem Diagramm wird die Seite **Sicherheitswarnungen** geöffnet.

3. **Erweiterter Schutz:** Defender für Cloud verfügt über viele Advanced Threat Protection-Funktionen für virtuelle Computer, SQL-Datenbanken, Container, Webanwendungen, Ihr Netzwerk und mehr. In diesem Abschnitt zum erweiterten Schutz können Sie den Status der Ressourcen in den ausgewählten Abonnements für jede dieser Schutzmaßnahmen einsehen. Wählen Sie eine beliebige Option aus, um direkt zum Konfigurationsbereich für diesen Schutztyp zu wechseln.

4. **Erkenntnisse:** In diesem rollierenden Bereich mit Nachrichten, empfohlener Lektüre und Warnungen mit hoher Priorität werden die Erkenntnisse von Defender für Cloud zu dringenden Sicherheitsangelegenheiten bereitgestellt, die für Sie und Ihr Abonnement relevant sind. Diese Informationen werden hier im Bereich „Erkenntnisse“ unabhängig davon angezeigt, ob es sich um eine Liste mit CVEs mit hohem Schweregrad handelt, die mit einem Tool für Sicherheitsrisikoanalysen auf Ihren VMs erkannt wurden, oder um einen neuen Blogbeitrag von einem Mitglied des Defender für Cloud-Teams.




## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie Informationen zum Dashboard für den Workloadschutz erhalten. 

> [!div class="nextstepaction"]
> [Aktivieren des erweiterten Schutzes](enable-enhanced-security.md)

Weitere Informationen zum erweiterten Schutz von Microsoft Defender finden Sie in der [Einführung in Microsoft Defender für Cloud](defender-for-cloud-introduction.md).