---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/05/2021
ms.author: mimart
ms.openlocfilehash: f21c0a37d169885e60ae9dea34663dc0a86e161f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801232"
---
Der Abmeldeflow umfasst die folgenden Schritte:

1. In der App melden sich Benutzer ab.
1. Die App löscht ihre Sitzungscookies, die Authentifizierungsbibliothek leert ihren Tokencache.
1. Die App leitet den Benutzer zum Azure AD B2C-Abmeldeendpunkt, um die Azure AD B2C-Sitzung zu beenden.
1. Benutzer werden zurück zur App geleitet.
