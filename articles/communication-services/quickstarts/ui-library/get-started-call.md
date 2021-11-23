---
title: 'Schnellstart: Hinzufügen zusammengesetzter Komponenten für Anrufe zu Ihrer App'
titleSuffix: An Azure Communication Services quickstart
description: In dieser Schnellstartanleitung finden Sie Informationen zu den ersten Schritten mit zusammengesetzten Komponenten für Anrufe aus der UI-Bibliothek.
author: jorgegarc
ms.author: jorgegarc
ms.date: 10/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
zone_pivot_groups: acs-plat-ios-android
ms.openlocfilehash: a8bd62d55dfe4cf85b4af01bb93d9a4ea44a0a40
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132134013"
---
# <a name="quickstart-get-started-with-calling-composite"></a>Schnellstart: Erste Schritte mit zusammengesetzten Komponenten für Anrufe 

Beginnen Sie mit der Verwendung von Azure Communication Services, indem Sie die UI-Bibliothek verwenden, um schnell Kommunikationsumgebungen in Ihre Anwendungen zu integrieren. In dieser Schnellstartanleitung erfahren Sie, wie Sie die zusammengesetzten Komponenten für Anrufe in Ihre Android- oder iOS-Anwendung integrieren.

Von der UI-Bibliothek wird eine vollständige Kommunikationsumgebung direkt in Ihrer Anwendung gerendert. Sie übernimmt die Verbindungsherstellung mit dem gewünschten Anruf und richtet sie im Hintergrund ein. Als Entwickler müssen Sie sich nur Gedanken darüber machen, wo in Ihrer Umgebung die Kommunikationsumgebung gestartet werden soll. Die zusammengesetzte Komponente führt den Benutzer durch das Einrichten seiner Geräte, das Beitreten zum Anruf und die Teilnahme daran sowie das Rendern anderer Teilnehmer.

[!INCLUDE [Public Preview Notice](../../includes/private-preview-include.md)]

::: zone pivot="platform-android"
[!INCLUDE [UI Library with Android](./includes/get-started-call/android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [UI Library with iOS](./includes/get-started-call/ios.md)]
::: zone-end

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Communication Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen.

Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind.
Weitere Informationen zum Bereinigen von Ressourcen finden Sie [hier](../create-communication-resource.md#clean-up-resources).
