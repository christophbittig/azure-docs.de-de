---
title: Konfigurieren der ressourcenübergreifenden Ressourcenfreigabe im FHIR-Dienst
description: In diesem Artikel wird beschrieben, wie Sie die ressourcenübergreifende Ressourcenfreigabe im FHIR-Dienst konfigurieren.
author: matjazl
ms.author: zxue
ms.date: 08/03/2021
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: e322b1fb848f9156b1d00d2d1ae051ef62536c4a
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778884"
---
# <a name="configure-cross-origin-resource-sharing-in-fhir-service"></a>Konfigurieren der ressourcenübergreifenden Ressourcenfreigabe im FHIR-Dienst

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

Der FHIR-Dienst in den Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) unterstützt die ressourcenübergreifende Ressourcenfreigabe [(Cross-Origin Resource Sharing, CORS).](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing) CORS erlaubt es Ihnen, Einstellungen so zu konfigurieren, dass Anwendungen aus einer Domäne (Ursprung) auf Ressourcen aus einer anderen Domäne zugreifen können, bekannt als domänenübergreifende Anforderung.

CORS wird häufig in Single-Page-Apps verwendet, die eine RESTful-API für eine andere Domäne aufrufen müssen.

Um eine CORS-Einstellung im FHIR-Dienst zu konfigurieren, geben Sie die folgenden Einstellungen an:

- **Ursprünge (Access-Control-Allow-Origin)** . Eine Liste der Domänen, die ursprungsübergreifende Anforderungen an den FHIR-Dienst senden dürfen. Jede Domäne (Ursprung) muss in einer separaten Zeile eingegeben werden. Sie können ein Sternchen (*) eingeben, um Aufrufe aus einer beliebigen Domäne zuzulassen, was wir aber nicht empfehlen, da es sich um ein Sicherheitsrisiko handelt.

- **Headers (Access-Control-Allow-Headers)** . Eine Liste der Header, die die Ursprungsanforderung enthalten soll. Um alle Header zuzulassen, geben Sie ein Sternchen (*) ein.

- **Methoden (Access-Control-Allow-Methods)** . Die zulässigen Methoden (PUT, GET, POST usw.) in einem API-Aufruf. Wählen Sie **Alle auswählen** aus, um alle Methoden zu verwenden.

- **Max. Alter (Access-Control-Max-Age)** . Der Wert in Sekunden, um die Ergebnisse der Preflight-Anforderung für „Access-Control-Allow-Headers“ und „Access-Control-Allow-Methods“ zwischenzuspeichern.

- **Anmeldeinformationen zulassen (Access-Control-Allow-Credentials)** . CORS-Anforderungen enthalten normalerweise keine Cookies, um [CSRF-Angriffe (siteübergreifende Anforderungsfälschung)](https://en.wikipedia.org/wiki/Cross-site_request_forgery) zu verhindern. Wenn Sie diese Einstellung auswählen, kann die Anforderung so gestellt werden, dass sie Anmeldeinformationen (z. B. Cookies) enthält. Sie können diese Einstellung nicht konfigurieren, wenn Sie „Ursprünge“ bereits mit einem Sternchen (*) festgelegt haben.

![CORS-Einstellungen (Ressourcenfreigabe zwischen verschiedenen Ursprüngen)](media/cors/cors.png)

>[!NOTE]
>Sie können keine unterschiedlichen Einstellungen für verschiedene Domänenursprünge festlegen. Alle Einstellungen (**Headers**, **Methoden**, **Max. Alter** und **Anmeldeinformationen zulassen**) gelten für alle in der Einstellung „Ursprünge“ angegebenen Ursprünge.