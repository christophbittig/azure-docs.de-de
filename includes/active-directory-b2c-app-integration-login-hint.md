---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: c2409ef7d846ff7d4d67d7bf79ab96760375da81
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037052"
---
## <a name="prepopulate-the-sign-in-name"></a>Auffüllen des Anmeldenamens

Während einer User Journey zur Anmeldung zielt Ihre App ggf. auf einen bestimmten Benutzer ab. Wenn eine App auf einen Benutzer abzielt, kann sie in der Autorisierungsanforderung den Abfrageparameter `login_hint` mit dem Anmeldenamen des Benutzers angeben. Azure AD B2C füllt den Anmeldenamen automatisch auf. Der Benutzer muss nur das Kennwort angeben. 

Gehen Sie wie folgt vor, um den Anmeldenamen vorab aufzufüllen: