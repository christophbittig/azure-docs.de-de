---
author: kengaderdus
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 06/11/2021
ms.author: kengaderdus
ms.openlocfilehash: bf7295f4cc7b2010e7c700fbde25840f7a7f0df1
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037087"
---
## <a name="pass-an-id-token-hint"></a>Übergeben eines ID-Tokenhinweises

Eine Anwendung der vertrauenden Seite kann ein eingehendes JWT (JSON Web Token) als Teil der OAuth2-Autorisierungsanforderung senden.  Das eingehende Token ist ein Hinweis zum Benutzer oder zur Autorisierungsanforderung. Azure AD B2C überprüft das Token und extrahiert die Ansprüche.

Führen Sie die folgenden Schritte aus, um einen ID-Tokenhinweis in die Authentifizierungsanforderung einzufügen: