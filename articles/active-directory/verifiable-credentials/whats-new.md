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
ms.openlocfilehash: 42194075a3f97c13a3c45535ba68d2b4d23de0b8
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129984913"
---
# <a name="whats-new-in-azure-active-directory-verifiable-credentials-preview"></a>Neuerungen im Azure Active Directory-Nachweisdienst (Vorschau)

In diesem Dokument finden Sie die neuesten Features, Verbesserungen und Änderungen des Azure AD-Nachweisdiensts.

## <a name="october-2021"></a>Oktober 2021

**Öffentliche Vorschau der Anforderungsdienst-API.**

- Mit der neuen [Anforderungsdienst-API](get-started-request-api.md) können Entwickler auf einfache Weise Anwendungen erstellen, die Nachweise ausstellen und überprüfen können, wobei eine beliebige Programmiersprache verwendet werden kann. Diese neue REST-API bietet eine verbesserte Abstraktionsschicht und Integration in den Azure AD-Nachweisdienst.

- Mit der Veröffentlichung der Anforderungsdienst-API wenden wir uns von unserem ursprünglichen NodeJS-basierten SDK ab, und wir empfehlen Ihnen dringend, die API zu verwenden, weil das NodeJS SDK in den nächsten Monaten eingestellt wird. Unsere Dokumentation und Beispiele wurden für die Verwendung der Anforderungsdienst-API aktualisiert. Erfahren Sie mehr über die [Anforderungsdienst-API](get-started-request-api.md).

## <a name="april-2021"></a>April 2021

**Der Azure Active Directory-Nachweisdienst befindet sich in der öffentlichen Vorschauphase.**

Azure AD-Kunden können jetzt auf einfache Weise [Nachweise](decentralized-identifier-overview.md) ausstellen, um einen Beschäftigungsnachweis, einen Ausbildungsnachweis oder einen anderen Anspruch darzustellen, sodass der Besitzer eines solchen Nachweises entscheiden kann, wann und für wen er seine Nachweise freigibt. Jeder Nachweis wird mit kryptografischen Schlüsseln signiert, die dem DID zugeordnet sind, den der Benutzer besitzt und steuert.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie Fragen haben, können Sie sich an unseren [Support](https://azure.microsoft.com/support/options/) wenden.
