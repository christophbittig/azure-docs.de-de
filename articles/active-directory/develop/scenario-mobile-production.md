---
title: Vorbereiten von mobilen Apps auf die Produktion, die Web-APIs aufrufen | Azure
titleSuffix: Microsoft identity platform
description: Hier erfahren Sie, wie Sie eine mobile App erstellen, die Web-APIs aufruft. (Vorbereiten von Apps auf die Produktion.)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7756029d4b3ed43f55c86fa12128e1943c99b6bb
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778386"
---
# <a name="prepare-mobile-apps-for-production"></a>Vorbereiten von mobilen Apps auf die Produktion

In diesem Artikel wird beschrieben, wie Sie die Qualität und Zuverlässigkeit Ihrer mobilen App erhöhen, bevor Sie diese in die Produktion überführen.

## <a name="handle-errors"></a>Behandeln von Fehlern

Beim Vorbereiten von mobilen Apps für die Produktion können verschiedene Fehlerbedingungen auftreten. In den Hauptszenarien werden Sie sich mit verborgenen (stillen) Ausfällen und dem Ausweichen auf eine Interaktion befassen. Weitere Fehlerzustände sind Netzwerkstörungen, Dienstausfälle, fehlende Administratoreinwilligungen und weitere Fälle, die auf das jeweilige Szenario bezogen sind.

Für jeden MSAL-Typ (Microsoft Authentication Library, Microsoft Authentifizierungsbibliothek) können Sie Beispielcode und Wiki-Inhalte finden, die beschreiben, wie Fehlerbedingungen zu behandeln sind:

- [Android-Wiki für MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-android)
- [iOS-Wiki für MSAL](https://github.com/AzureAD/microsoft-authentication-library-for-objc/wiki)
- [Wiki für MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki)


[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Nächste Schritte

Weitere Beispiele zum Testen finden Sie unter [Mobile öffentliche Clientanwendungen](sample-v2-code.md#mobile).