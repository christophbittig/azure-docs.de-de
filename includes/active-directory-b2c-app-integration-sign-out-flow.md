---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: 7196d4596a3e67f1482fe2955a933fbf9efc8d47
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227681"
---
Der Abmeldeflow umfasst die folgenden Schritte:

1. In der App melden sich Benutzer ab.
1. Die App löscht ihre Sitzungsobjekte, und die Authentifizierungsbibliothek leert ihren Tokencache.
1. Die App leitet den Benutzer zum Azure AD B2C-Abmeldeendpunkt, um die Azure AD B2C-Sitzung zu beenden.
1. Benutzer werden zurück zur App geleitet.
