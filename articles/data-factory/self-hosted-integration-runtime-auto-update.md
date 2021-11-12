---
title: Benachrichtigung zur automatischen Aktualisierung und zum Ablauf der selbstgehosteten Integration Runtime
description: Erfahren Sie mehr über die Benachrichtigung zur automatischen Aktualisierung und zum Ablauf der selbstgehosteten Integration Runtime.
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 06/16/2021
ms.openlocfilehash: 5a1cf4366ffd86491cc3e3e09358e364d878cb87
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989335"
---
# <a name="self-hosted-integration-runtime-auto-update-and-expire-notification"></a>Benachrichtigung zur automatischen Aktualisierung und zum Ablauf der selbstgehosteten Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie die selbstgehostete Integration Runtime automatisch auf die neueste Version aktualisiert wird und wie die Versionen der selbstgehosteten Integration Runtime in ADF verwaltet werden.

## <a name="self-hosted-integration-runtime-auto-update"></a>Automatische Aktualisierung der selbstgehosteten Integration Runtime
Wenn Sie eine selbstgehostete Integration Runtime auf Ihrem lokalen Computer oder einer Azure-VM installieren, haben Sie in der Regel zwei Möglichkeiten, die Version der selbstgehosteten Integration Runtime zu verwalten: durch automatische Aktualisierung oder durch manuelle Wartung. Normalerweise werden in ADF monatlich zwei neue Versionen der selbstgehosteten Integration Runtime veröffentlicht, die neue Featurereleases, Fixes oder Verbesserungen beinhalten. Daher empfehlen wir Benutzern ein Update auf die neuere Version, damit sie die neuesten Features und Erweiterungen erhalten.

Die einfachste Möglichkeit besteht darin, beim Erstellen oder Bearbeiten der selbstgehosteten Integration Runtime die automatische Aktualisierung zu aktivieren. Es wird ein automatisches Update der selbstgehosteten Integration Runtime auf die neuere Version durchgeführt. Sie können das Update auch für den am besten geeigneten Zeitraum planen.

:::image type="content" source="media/create-self-hosted-integration-runtime/shir-auto-update.png" alt-text="Aktivieren der automatischen Aktualisierung":::

Sie können Datum und Uhrzeit der letzten Aktualisierung auf dem Client der selbstgehosteten Integration Runtime überprüfen.

:::image type="content" source="media/create-self-hosted-integration-runtime/shir-auto-update-2.png" alt-text="Screenshot der Überprüfung der Aktualisierungszeit":::

Sie können diesen [PowerShell-Befehl](/powershell/module/az.datafactory/get-azdatafactoryv2integrationruntime?view=azps-6.1.0&preserve-view=true#example-5--get-self-hosted-integration-runtime-with-detail-status) nutzen, um die Version mit automatischem Update zu erhalten. 

> [!NOTE]
> Wenn Sie über mehrere selbstgehostete Integration Runtime-Knoten verfügen, kommt es während dem automatischen Update nicht zu Ausfallzeiten. Das automatische Update erfolgt zuerst auf einem Knoten, während andere an Aufgaben arbeiten. Wenn der erste Knoten das Update abgeschlossen hat, übernimmt er die übrigen Aufgaben, wenn andere Knoten aktualisiert werden. Wenn Sie nur über eine selbstgehostete Integration Runtime verfügen, kommt es während des automatischen Updates zu Ausfallzeiten.

## <a name="auto-update-version-vs-latest-version"></a>Version mit automatischem Update im Vergleich zu neuester Version
Obwohl wir jeden Monat zwei Versionen veröffentlichen, wird pro Monat nur eine Version gepusht, um die Stabilität der selbstgehosteten Integration Runtime zu gewährleisten. Gelegentlich werden Sie daher feststellen, dass es sich bei der automatisch aktualisierten Version nicht um die aktuelle, sondern um die vorherige Version handelt. Wenn Sie die neueste Version erhalten möchten, wechseln Sie zum [Download Center](https://www.microsoft.com/download/details.aspx?id=39717). Darüber hinaus wird die automatische Aktualisierung auf eine Version einmal verwaltet. Sie können sie nicht ändern. Wenn Sie ein Upgrade auf die neueste Version durchführen möchten, müssen Sie dies manuell erledigen. 

Die selbstgehostete Integration Runtime-Seite **Auto update** (Automatische Aktualisierung) im ADF-Portal zeigt die neuere Version an, wenn die aktuelle Version veraltet ist. Wenn Ihre selbstgehostete Integration Runtime online ist, handelt es sich bei dieser Version um eine automatische Updateversion, und Ihre selbstgehostete Integration Runtime wird automatisch zur geplanten Zeit aktualisiert. Wenn Ihre selbstgehostete Integration Runtime jedoch offline ist, wird auf der Seite nur die neueste Version angezeigt.

Wenn Sie über mehrere Knoten verfügen und einige davon aus bestimmten Gründen nicht automatisch aktualisiert werden, wird für diese Knoten ein Rollback auf die Version ausgeführt, die vor dem automatischen Update auf allen Knoten identisch war. 

## <a name="self-hosted-integration-runtime-expire-notification"></a>Benachrichtigung zum Ablauf der selbstgehosteten Integration Runtime
Wenn Sie die Version der selbstgehosteten Integration Runtime manuell steuern möchten, können Sie die Einstellung zur automatischen Aktualisierung deaktivieren und die Integration Runtime manuell installieren. Jede Version der selbstgehosteten Integration Runtime läuft nach einem Jahr ab. Die Ablaufnachricht wird im ADF-Portal und auf dem Client der selbstgehosteten Integration Runtime **90 Tage** vor dem Ablaufdatum angezeigt.

## <a name="next-steps"></a>Nächste Schritte

- Lesen Sie [Integration Runtime-Konzepte in Azure Data Factory](./concepts-integration-runtime.md).

- Informieren Sie sich über das [Erstellen einer selbstgehosteten Integration Runtime im Azure-Portal](./create-self-hosted-integration-runtime.md).
