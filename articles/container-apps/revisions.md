---
title: Revisionen in Azure Container Apps Vorschau
description: Erfahren Sie, wie Revisionen in Azure Container Apps erstellt werden.
services: container-apps
author: craigshoemaker
ms.service: container-apps
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: cshoe
ms.custom: ignite-fall-2021
ms.openlocfilehash: daa5f00ba30bfcd0af41e92339518f0d9c1f44ae
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134065"
---
# <a name="revisions-in-azure-container-apps-preview"></a>Revisionen in Azure Container Apps Vorschau

Eine Revision ist eine unveränderliche Momentaufnahme einer Container-App.

- Wenn Sie eine Container-App bereitstellen, wird die erste Revision automatisch erstellt.
- Neue Revisionen werden automatisch erstellt, wenn sich die Konfiguration einer Container-App `template` ändert.
- Revisionen sind unveränderlich, aber sie sind von Änderungen an globalen Konfigurationswerten betroffen, die für alle Revisionen gelten.

:::image type="content" source="media/revisions/azure-container-apps-revisions.png" alt-text="Azure Container Apps: Container":::

Revisionen sind besonders nützlich, wenn Sie [eingehend](ingress.md) aktivieren, um den Zugriff auf Ihre Container-App über HTTP zu ermöglichen.  Revisionen werden häufig verwendet, wenn Sie den Datenverkehr von einem Snapshot Ihrer Container-App zum nächsten leiten wollen. Typische Strategien für die Richtung des Datenverkehrs sind [A/B-Tests](https://wikipedia.org/wiki/A/B_testing) und die [BlueGreen-Bereitstellung](https://martinfowler.com/bliki/BlueGreenDeployment.html).

Das folgende Diagramm zeigt eine Container-App mit zwei Revisionen.

:::image type="content" source="media/revisions/azure-container-apps-revisions-traffic-split.png" alt-text="Azure Container Apps: Aufteilung des Datenverkehrs auf Revisionen":::

Das oben gezeigte Szenario setzt voraus, dass sich die Container-App im folgenden Zustand befindet:

- [Eingehend](ingress.md) ist aktiviert, wodurch die Container-App über HTTP verfügbar wird.
- Die erste Revision wird als _Revision 1_ bereitgestellt.
- Nachdem der Container aktualisiert wurde, wurde eine neue Revision als _Revision 2_ aktiviert.
- Die Regeln für die [Verkehrsaufteilung](revisions-manage.md#traffic-splitting) sind so konfiguriert, dass _Revision 1_ 80 % der Anfragen erhält, während _Revision 2_ die restlichen 20 % erhält.

## <a name="change-types"></a>Änderungstypen

Änderungen, die an einer Container-App vorgenommen werden, fallen in eine von zwei Kategorien: Änderungen des *Revisionsbereichs* und des *Anwendungsbereichs*. Änderungen im Revisionsbereich sind Änderungen, die eine neue Revision auslösen, während Änderungen im Anwendungsbereich keine Revisionen erstellen.

### <a name="revision-scope-changes"></a>Änderungen im Revisionsbereich

Die folgenden Arten von Änderungen erstellen eine neue Revision:

- Änderungen an Containern
- Hinzufügen oder Aktualisieren von Skalierungsregeln
- Änderungen an Dapr-Einstellungen
- Alle Änderungen, die sich auf den Abschnitt `template` der Konfiguration auswirken

### <a name="application-scope-changes"></a>Änderungen im Anwendungsbereich

Die folgenden Arten von Änderungen erstellen eine neue Revision:

- Änderungen der Regeln für die [Datenverkehrsaufteilung](revisions-manage.md#traffic-splitting)
- Aktivieren oder Deaktivieren von [Eingehend](ingress.md)
- Änderungen von [Geheimniswerten](secure-app.md)
- Alle Änderungen, die sich auf den Abschnitt `template` der Konfiguration auswirken

Während Änderungen an Geheimnissen eine Änderung des Anwendungsbereichs sind, müssen Revisionen [neu gestartet](revisions.md) werden, bevor ein Container neue Geheimniswerte erkennt.

## <a name="activation-state"></a>Aktivierungszustand

Neue Revisionen bleiben aktiv, bis Sie sie deaktivieren oder Ihre Container-App so einstellen, dass alte Revisionen automatisch deaktiviert werden.

- Inaktive Revisionen verbleiben als Momentaufnahmedatensatz Ihrer Container-App in einem bestimmten Zustand.
- Inaktive Revisionen werden Ihnen nicht in Rechnung gestellt.
- Bis zu 100 Revisionen bleiben vor dem Bereinigen verfügbar.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Schützen einer App](secure-app.md)
