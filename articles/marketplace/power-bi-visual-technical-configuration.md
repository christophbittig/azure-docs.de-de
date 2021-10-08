---
title: Einrichten der technischen Konfiguration für ein Angebot für Power BI-Visuals in Partner Center für Microsoft AppSource
description: Erfahren Sie, wie Sie die technische Konfiguration für ein Angebot für Power BI-Visuals in Partner Center für Microsoft AppSource einrichten.
author: KesemSharabi
ms.author: kesharab
ms.reviewer: ''
ms.service: powerbi
ms.subservice: powerbi-custom-visuals
ms.topic: how-to
ms.date: 09/21/2021
ms.openlocfilehash: f544e9b98204f48defee59e614f395c879ecc8e4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128700363"
---
# <a name="set-up-power-bi-visual-offer-technical-configuration"></a>Einrichten der technischen Konfiguration für ein Angebot für Power BI-Visuals

Geben Sie auf der Registerkarte **Technische Konfiguration** die Dateien an, die für das Angebot für Power BI-Visuals erforderlich sind.

:::image type="content" source="media/power-bi-visual/technical-configuration.png" alt-text="Seite „Technische Konfiguration“ in Partner Center":::

## <a name="pbiviz-package"></a>PBIVIZ-Paket

[Packen Sie Ihr Power BI-Visual](https://docs.microsoft.com/power-bi/developer/visuals/package-visual) in ein PBIVIZ-Paket, das alle erforderlichen Metadaten enthält:

- Name der Visualisierung
- Anzeigename
- GUID (siehe Hinweis unten)
- Version (siehe Hinweis unten)
- Beschreibung
- Name und E-Mail-Adresse des Autors

> [!NOTE]
> Wenn Sie ein Visual aktualisieren oder erneut senden, gilt Folgendes:
> - Die GUID muss unverändert bleiben.
> - Die Versionsnummer sollte zwischen Paketaktualisierungen erhöht werden.

## <a name="sample-pbix-report-file"></a>PBIX-Beispielberichtsdatei

Bei der Präsentation Ihres Visualangebots sollen die Benutzer das Visual kennenlernen. Heben Sie den Nutzen des Visuals für den Benutzer hervor, und nennen Sie Beispiele für die Verwendung und Formatierungsoptionen. Fügen Sie am Ende eine Seite mit Hinweisen hinzu, die Tipps und Tricks sowie Fehler enthält, die Benutzer vermeiden sollten. Die PBIX-Beispielberichtsdatei muss offline ohne externe Verbindung funktionieren.

> [!NOTE]
> - Der PBIX-Bericht muss die gleiche Version des Visuals wie die PBIVIZ-Datei verwenden.
> - Die PBIX-Berichtsdatei muss offline ohne externe Verbindung funktionieren.

Wählen Sie **Entwurf speichern** aus, bevor Sie im linken Navigationsmenü zur Registerkarte **Angebotsverwaltung** wechseln.

## <a name="next-steps"></a>Nächste Schritte

- [Angebotsverwaltung](power-bi-visual-manage-names.md)
