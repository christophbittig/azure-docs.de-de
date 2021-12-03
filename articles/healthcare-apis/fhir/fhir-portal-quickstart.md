---
title: Bereitstellen eines FHIR-Diensts in Azure Healthcare-APIs
description: In diesem Artikel erfahren Benutzer, wie Sie einen FHIR-Dienst im Azure-Portal bereitstellen.
author: stevewohl
ms.service: healthcare-apis
ms.topic: quickstart
ms.date: 09/10/2021
ms.author: ginle
ms.custom: mode-other
ms.openlocfilehash: 659fb82e35cd25622158d05a88d85a16e736c770
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2021
ms.locfileid: "133069705"
---
# <a name="deploy-a-fhir-service-within-azure-healthcare-apis---using-portal"></a>Bereitstellen eines FHIR-Diensts in Azure Healthcare-APIs über das Portal

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel erfahren Sie, wie Sie den FHIR-Dienst mithilfe der Azure-Portal in den Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) bereitstellen.

## <a name="prerequisite"></a>Voraussetzung

Bevor Sie beginnen, sollten Sie die Azure Healthcare-APIs bereits bereitgestellt haben. Weitere Informationen zum Bereitstellen von Azure Healthcare-APIs finden Sie unter [Bereitstellen eines Arbeitsbereichs im Azure-Portal](../healthcare-apis-quickstart.md).

## <a name="create-a-new-fhir-service"></a>Erstellen eines neuen FHIR-Diensts

Wählen Sie im Arbeitsbereich **Die FHIR-Dienste bereitstellen** aus.

[![Bereitstellen des FHIR-Diensts ](media/fhir-service/deploy-fhir-services.png) ](media/fhir-service/deploy-fhir-services.png#lightbox)

Wählen Sie **+ FHIR-Dienst hinzufügen** aus.

[![Hinzufügen des FHIR-Diensts ](media/fhir-service/add-fhir-service.png) ](media/fhir-service/add-fhir-service.png#lightbox)

Geben Sie einen **Kontonamen** für Ihren FHIR-Dienst ein. Wählen Sie die **FHIR-Version** **(STU3** oder **R4)** und dann **Überprüfen + erstellen** aus.

[![Erstellen des FHIR-Diensts ](media/fhir-service/create-fhir-service.png) ](media/fhir-service/create-fhir-service.png#lightbox)

Bevor Sie Erstellen auswählen, überprüfen **Sie** die Eigenschaften der Einstellungen Grundlagen und **Zusätzliche Einstellungen** Ihres FHIR-Diensts.  Wenn Sie zurückwechseln und Änderungen vornehmen müssen, wählen Sie **Zurück** aus. Vergewissern Sie sich, dass die Meldung **Überprüfung erfolgreich** angezeigt wird. 

[Überprüfen des ![ FHIR-Diensts ](media/fhir-service/validation-fhir-service.png) ](media/fhir-service/validation-fhir-service.png#lightbox)

## <a name="additional-settings-optional"></a>Zusätzliche Einstellungen (optional)

Sie können auch die Registerkarte **Zusätzliche Einstellungen** auswählen, um die Authentifizierungseinstellungen anzuzeigen. In der Standardkonfiguration für Azure API for FHIR **werden Datenebenenrollen mithilfe von Azure RBAC zugewiesen**. Wenn sie in diesem Modus konfiguriert ist, wird die "Autorität" für den FHIR-Dienst auf den Azure Active Directory Mandanten des Abonnements festgelegt.

[![Zusätzliche Einstellungen für den FHIR-Dienst ](media/fhir-service/additional-settings-tab.png) ](media/fhir-service/additional-settings-tab.png#lightbox)

Beachten Sie, dass das Feld für die Eingabe **von Zulässigen Objekt-IDs** ausgegraut ist. Dies liegt daran, dass wir in diesem Fall Azure RBAC zum Konfigurieren von Rollenzuweisungen verwenden.

Wenn Sie den FHIR-Dienst für die Verwendung eines externen oder sekundären Azure Active Directory-Mandanten konfigurieren möchten, können Sie die Autorität ändern und Objekt-IDs für Benutzer und Gruppen eingeben, denen Zugriff auf den Server gewährt werden soll.

## <a name="fetch-fhir-api-capability-statement"></a>Abrufen der FHIR-API-Funktionsbestätigung

Um zu überprüfen, ob das neue FHIR-API-Konto bereitgestellt wurde, rufen Sie eine Funktionsaufstellung ab, indem Sie zu `https://<WORKSPACE NAME>-<ACCOUNT-NAME>.fhir.azurehealthcareapis.com/metadata` navigieren.

## <a name="next-steps"></a>Nächste Schritte

>[!div class="nextstepaction"]
>[Zugreifen auf den FHIR-Dienst mit postman](../use-postman.md)
