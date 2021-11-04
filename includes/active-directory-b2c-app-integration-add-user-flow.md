---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: f2d96d8babc11034769258b5ea63b8a3d23fdc5b
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007565"
---
Wenn Benutzer versuchen, sich bei Ihrer App anzumelden, startet die App über einen [Benutzerflow](../articles/active-directory-b2c/user-flow-overview.md) eine Authentifizierungsanforderung an den Autorisierungsendpunkt. Der Benutzerflow definiert und kontrolliert die Benutzeroberfläche. Nachdem Benutzer den Benutzerflow abgeschlossen haben, generiert Azure AD B2C ein Token und leitet den Benutzer anschließend wieder zurück zu Ihrer Anwendung.

Falls noch nicht geschehen, [erstellen Sie einen Benutzerflow oder eine benutzerdefinierte Richtlinie](../articles/active-directory-b2c/add-sign-up-and-sign-in-policy.md).
