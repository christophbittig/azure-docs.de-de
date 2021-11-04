---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: kengaderdus
ms.openlocfilehash: 0a29cf2d195e1fdd0bda03f718b1693fffaa5e9c
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "131007595"
---
Der Abmeldeflow umfasst die folgenden Schritte:

1. In der App melden sich Benutzer ab.
1. Die App löscht ihre Sitzungsobjekte, und die Authentifizierungsbibliothek leert ihren Tokencache.
1. Die App leitet den Benutzer zum Azure AD B2C-Abmeldeendpunkt, um die Azure AD B2C-Sitzung zu beenden.
1. Benutzer werden zurück zur App geleitet.
