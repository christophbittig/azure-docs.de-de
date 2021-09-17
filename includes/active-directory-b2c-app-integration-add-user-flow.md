---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: b6b0808f01cc47397c4f2585033733fa7b6ce05a
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227784"
---
Wenn Benutzer versuchen, sich bei Ihrer App anzumelden, startet die App über einen [Benutzerflow](../articles/active-directory-b2c/user-flow-overview.md) eine Authentifizierungsanforderung an den Autorisierungsendpunkt. Der Benutzerflow definiert und kontrolliert die Benutzeroberfläche. Nachdem Benutzer den Benutzerflow abgeschlossen haben, generiert Azure AD B2C ein Token und leitet den Benutzer anschließend wieder zurück zu Ihrer Anwendung.

Falls noch nicht geschehen, [erstellen Sie einen Benutzerflow oder eine benutzerdefinierte Richtlinie](../articles/active-directory-b2c/add-sign-up-and-sign-in-policy.md).
