---
title: 'Schnellstart: Teams-Interoperabilität unter Azure Communication Services'
titleSuffix: An Azure Communication Services quickstart
description: In dieser Schnellstartanleitung wird beschrieben, wie Sie mit dem Azure Communication Calling SDK einer Teams-Besprechung beitreten.
author: chpalm
ms.author: chpalm
ms.date: 06/30/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.subservice: calling
zone_pivot_groups: acs-plat-web-ios-android-windows
ms.openlocfilehash: 4ab25d1105f8dcad40599a8096ea114c31dfa61a
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488329"
---
# <a name="quickstart-join-your-calling-app-to-a-teams-meeting"></a>Schnellstart: Beitreten mit einer Telefonie-App zu einer Teams-Besprechung

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

::: zone pivot="platform-web"
[!INCLUDE [Calling with JavaScript](./includes/teams-interop/teams-interop-javascript.md)]
::: zone-end

::: zone pivot="platform-windows"
[!INCLUDE [Calling with Windows](./includes/teams-interop/teams-interop-windows.md)]
::: zone-end

::: zone pivot="platform-android"
[!INCLUDE [Calling with Android](./includes/teams-interop/teams-interop-android.md)]
::: zone-end

::: zone pivot="platform-ios"
[!INCLUDE [Calling with iOS](./includes/teams-interop/teams-interop-ios.md)]
::: zone-end

Von den in diesem Dokument beschriebenen Funktionen wird die allgemein verfügbare Version der Communication Services SDKs verwendet. Für Teams-Interoperabilität ist die Betaversion der Communication Services SDKs erforderlich. Sie können sich die Beta-SDKs auf der [Seite mit den Versionshinweisen](https://github.com/Azure/Communication/tree/master/releasenotes) ansehen.

Wenn Sie den Schritt „Paket installieren“ mit den Beta-SDKs ausführen, ändern Sie die Version Ihres Pakets in das aktuelle Betarelease, indem Sie im Paketnamen `communication-calling` die Version `@1.0.0-beta.10` (Version zum Zeitpunkt der Artikelerstellung) angeben. Sie müssen den Paketbefehl `communication-common` nicht ändern. Beispiel:

```console
npm install @azure/communication-calling@1.0.0-beta.10 --save
```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Communication Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind. Weitere Informationen zum Bereinigen von Ressourcen finden Sie [hier](../create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- Sehen Sie sich unser [Hero-Beispiel für Anrufe](../../samples/calling-hero-sample.md) an.
- Informieren Sie sich über die [Funktionen des Calling SDK](./getting-started-with-calling.md).
- Informieren Sie sich über die [Funktionsweise von Anrufen](../../concepts/voice-video-calling/about-call-types.md).