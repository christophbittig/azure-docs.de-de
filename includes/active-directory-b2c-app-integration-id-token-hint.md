---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: mimart
ms.openlocfilehash: f66eac86f4267a7b824eb551cdd877d3d34472a0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121801751"
---
## <a name="pass-an-id-token-hint"></a>Übergeben eines ID-Tokenhinweises

Eine Anwendung der vertrauenden Seite kann ein eingehendes JWT (JSON Web Token) als Teil der OAuth2-Autorisierungsanforderung senden.  Das eingehende Token ist ein Hinweis zum Benutzer oder zur Autorisierungsanforderung. Azure AD B2C überprüft das Token und extrahiert die Ansprüche.

Führen Sie die folgenden Schritte aus, um einen ID-Tokenhinweis in die Authentifizierungsanforderung einzufügen: