---
title: Konfigurieren der Ressourcenfreigabe zwischen verschiedenen Ursprüngen in Azure API for FHIR
description: In diesem Artikel wird beschrieben, wie Sie die Ressourcenfreigabe zwischen verschiedenen Ursprüngen in Azure API for FHIR konfigurieren.
author: matjazl
ms.author: zxue
ms.date: 3/11/2019
ms.topic: reference
ms.service: healthcare-apis
ms.subservice: fhir
ms.openlocfilehash: a98687854a82c7dc17d2765a8c71467609435fa5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122355152"
---
# <a name="configure-cross-origin-resource-sharing-in-azure-api-for-fhir"></a>Konfigurieren der Ressourcenfreigabe zwischen verschiedenen Ursprüngen in Azure API for FHIR

Azure-API for Fast Healthcare Interoperability Resources (FHIR) unterstützt [Ressourcenfreigabe zwischen verschiedenen Ursprüngen (CORS)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing). CORS erlaubt es Ihnen, Einstellungen so zu konfigurieren, dass Anwendungen aus einer Domäne (Ursprung) auf Ressourcen aus einer anderen Domäne zugreifen können, bekannt als domänenübergreifende Anforderung.

CORS wird häufig in Single-Page-Apps verwendet, die eine RESTful-API für eine andere Domäne aufrufen müssen.

## <a name="configure-cors-settings"></a>Konfigurieren von CORS-Einstellungen

Um eine CORS-Einstellung in Azure API for FHIR zu konfigurieren, geben Sie die folgenden Einstellungen an:

- **Ursprünge (Access-Control-Allow-Origin)** . Eine Liste der Domänen, die Anforderungen zwischen verschiedenen Ursprüngen an Azure API for FHIR stellen dürfen. Jede Domäne (Ursprung) muss in einer separaten Zeile eingegeben werden. Sie können ein Sternchen (*) eingeben, um Aufrufe aus einer beliebigen Domäne zuzulassen, was wir aber nicht empfehlen, da es sich um ein Sicherheitsrisiko handelt.

- **Headers (Access-Control-Allow-Headers)** . Eine Liste der Header, die die Ursprungsanforderung enthalten soll. Um alle Header zuzulassen, geben Sie ein Sternchen (*) ein.

- **Methoden (Access-Control-Allow-Methods)** . Die zulässigen Methoden (PUT, GET, POST usw.) in einem API-Aufruf. Wählen Sie **Alle auswählen** aus, um alle Methoden zu verwenden.

- **Max. Alter (Access-Control-Max-Age)** . Der Wert in Sekunden, um die Ergebnisse der Preflight-Anforderung für „Access-Control-Allow-Headers“ und „Access-Control-Allow-Methods“ zwischenzuspeichern.

- **Anmeldeinformationen zulassen (Access-Control-Allow-Credentials)** . CORS-Anforderungen enthalten normalerweise keine Cookies, um [CSRF-Angriffe (siteübergreifende Anforderungsfälschung)](https://en.wikipedia.org/wiki/Cross-site_request_forgery) zu verhindern. Wenn Sie diese Einstellung auswählen, kann die Anforderung so gestellt werden, dass sie Anmeldeinformationen (z. B. Cookies) enthält. Sie können diese Einstellung nicht konfigurieren, wenn Sie „Ursprünge“ bereits mit einem Sternchen (*) festgelegt haben.

![CORS-Einstellungen (Ressourcenfreigabe zwischen verschiedenen Ursprüngen)](media/cors/cors.png)

>[!NOTE]
>Sie können keine unterschiedlichen Einstellungen für verschiedene Domänenursprünge festlegen. Alle Einstellungen (**Headers**, **Methoden**, **Max. Alter** und **Anmeldeinformationen zulassen**) gelten für alle in der Einstellung „Ursprünge“ angegebenen Ursprünge.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie die Freigabe zwischen verschiedenen Ursprüngen in Azure API for FHIR konfigurieren. Als Nächstes stellen Sie eine vollständig verwaltete Azure API for FHIR-Instanz bereit:
 
>[!div class="nextstepaction"]
>[Bereitstellen von Azure API for FHIR](fhir-paas-portal-quickstart.md)