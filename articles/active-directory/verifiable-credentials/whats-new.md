---
title: Neuerungen für den Azure Active Directory-Nachweisdienst (Vorschau)
description: Aktuelle Updates für Azure Active Directory-Nachweise
author: barclayn
manager: karenh444
ms.service: active-directory
ms.subservice: verifiable-credentials
ms.topic: reference
ms.date: 10/08/2021
ms.author: barclayn
ms.openlocfilehash: 4a00a787e61e6de2eda5753262b94d8a316c9b01
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440505"
---
# <a name="whats-new-in-azure-active-directory-verifiable-credentials-preview"></a>Neuerungen im Azure Active Directory-Nachweisdienst (Vorschau)

In diesem Artikel werden die neuesten Features, Verbesserungen und Änderungen des Azure AD-Nachweisdiensts aufgelistet.

## <a name="october-2021"></a>Oktober 2021

Sie können die neue [Anforderungsdienst-REST-API](get-started-request-api.md) nun nutzen, um Anwendungen zu erstellen, die Nachweise ausstellen und überprüfen können, wobei eine beliebige Programmiersprache verwendet werden kann. Diese neue REST-API bietet eine verbesserte Abstraktionsschicht und Integration in den Azure AD-Nachweisdienst.

Es ist eine gute Idee, bald mit der Nutzung der API zu beginnen, da das NodeJS SDK in den nächsten Monaten eingestellt wird. In der Dokumentation und den Beispielen wird jetzt die Anforderungsdienst-REST-API verwendet. Weitere Informationen finden Sie unter [Anforderungsdienst-REST-API (Vorschau)](get-started-request-api.md).

## <a name="april-2021"></a>April 2021

Sie können nun [Nachweise](decentralized-identifier-overview.md) in Azure AD ausstellen. Dieser Dienst ist nützlich, wenn Sie einen Beschäftigungsnachweis, einen Ausbildungsnachweis oder einen anderen Nachweis vorbringen müssen, sodass der entsprechende Besitzer entscheiden kann, wann und für wen er seine Nachweise freigibt. Jeder Nachweis wird mit kryptografischen Schlüsseln signiert, die der dezentralisierten Identität zugeordnet sind, die der Benutzer besitzt und steuert.
