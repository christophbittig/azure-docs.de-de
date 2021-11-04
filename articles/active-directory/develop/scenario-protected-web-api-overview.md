---
title: 'Geschützte Web-API: Übersicht'
titleSuffix: Microsoft identity platform
description: Erfahren Sie, wie Sie eine geschützte Web-API erstellen (Übersicht).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/26/2021
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: d34d8a361e22f25e82daf0576e65e35af192bdcd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462763"
---
# <a name="scenario-protected-web-api"></a>Szenario: Geschützte Web-API

In diesem Szenario erfahren Sie, wie Sie eine Web-API verfügbar machen. Sie erfahren auch, wie Sie die API-App schützen, sodass nur authentifizierte Benutzer darauf zugreifen können.

Sie müssen entweder authentifizierten Benutzern mit Arbeits-, Schul- und Unikonten oder Benutzern mit persönlichen Microsoft-Konten die Nutzung Ihrer Web-API erlauben.

## <a name="specifics"></a>Besonderheiten

Beachten Sie beim Schützen Ihrer Web-APIs die folgenden speziellen Informationen:

- Ihre App-Registrierung muss mindestens einen _Gültigkeitsbereich_ oder eine _Anwendungsrolle_ verfügbar machen.
  - Gültigkeitsbereiche werden von Web-APIs verfügbar gemacht, die im Namen eines Benutzers aufgerufen werden.
  - Anwendungsrollen werden von Web-APIs verfügbar gemacht, die von Daemonanwendungen aufgerufen werden (die Ihre Web-API in ihrem eigenen Auftrag aufrufen).
- Wenn Sie eine neue Web-API-App-Registrierung erstellen, wählen Sie die [Zugriffstokenversion](reference-app-manifest.md#accesstokenacceptedversion-attribute) aus, die von Ihrer Web-API für `2` akzeptiert wird. Bei älteren Web-APIs kann die akzeptierte Tokenversion `null` lauten, aber dieser Wert schränkt die Anmeldezielgruppe nur auf Organisationen ein, und persönliche Microsoft-Konten (MSA) werden nicht unterstützt.
- Die Codekonfiguration für die Web-API muss das Token überprüfen, das beim Aufrufen der Web-API verwendet wird.
- Der Code in den Controlleraktionen muss die Rollen oder Bereiche im Token überprüfen.

## <a name="recommended-reading"></a>Empfohlene Literatur

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [App-Registrierung](scenario-protected-web-api-app-registration.md).
