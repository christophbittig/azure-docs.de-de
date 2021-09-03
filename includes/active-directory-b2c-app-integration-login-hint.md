---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: 9fb96a0427ecaa3431eddb47ca89458a6dcf5901
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802507"
---
## <a name="prepopulate-the-sign-in-name"></a>Auffüllen des Anmeldenamens

Während einer User Journey zur Anmeldung zielt Ihre App ggf. auf einen bestimmten Benutzer ab. Wenn eine App auf einen Benutzer abzielt, kann sie in der Autorisierungsanforderung den Abfrageparameter `login_hint` mit dem Anmeldenamen des Benutzers angeben. Azure AD B2C füllt den Anmeldenamen automatisch auf. Der Benutzer muss nur das Kennwort angeben. 

Gehen Sie wie folgt vor, um den Anmeldenamen vorab aufzufüllen: