---
title: Registrieren einer öffentlichen Clientanwendung in Azure Active Directory – Azure Healthcare-APIs für DICOM
description: In diesem Artikel wird erläutert, wie Sie eine öffentliche Anwendung im Azure Active Directory.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2021
ms.author: aersoy
ms.openlocfilehash: f8a720508221d1a9a3308d64649863d11c6b57c7
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814595"
---
# <a name="register-a-public-client-application"></a>Registrieren einer öffentlichen Clientanwendung

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel erfahren Sie, wie Sie eine öffentliche Anwendung im Azure Active Directory (Azure AD.

Clientanwendungsregistrierungen sind Azure AD Darstellungen von Anwendungen, die sich authentifizieren und API-Berechtigungen im Auftrag eines Benutzers erfragen können. Öffentliche Clients sind Anwendungen wie beispielsweise mobile Anwendungen und JavaScript-Einzelseitenanwendungen, die Geheimnisse nicht vertraulich behandeln können. Das Verfahren ähnelt [](dicom-register-confidential-client-application.md)der Registrierung einer vertraulichen Clientanwendung. Da öffentliche Clients jedoch nicht als vertrauenswürdig eingestuft werden können, um ein Anwendungsgeheimnis zu enthalten, ist es nicht notwendig, eine hinzuzufügen.

Diese Schnellstartanleitung enthält allgemeine Informationen zum Registrieren einer [Anwendung beim Microsoft Identity Platform.](.././../active-directory/develop/quickstart-register-app.md)

## <a name="application-registrations-in-the-azure-portal"></a>Anwendungsregistrierungen im Azure-Portal

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Azure Active Directory** aus.
2. Wählen Sie **Azure Active Directory** Blatt **"App-Registrierungen.**
3. Wählen Sie **Neue Registrierung** aus.

   [![DICOM-Anwendungsregistrierung in Azure-Portal. ](media/dicom-azure-app-registrations.png) ](media/dicom-azure-app-registrations.png#lightbox)

## <a name="application-registration-overview"></a>Übersicht über die Anwendungsregistrierung

1. Geben Sie der DICOM-Dienstanwendung einen anzeigebasierten Anzeigenamen an.

   [![Azure registriert eine Anwendung. ](media/dicom-registration-application-name.png) ](media/dicom-registration-application-name.png#lightbox)

2. Wählen **Sie unter Unterstützte Kontotypen** aus, wer die Anwendung verwenden oder auf die API zugreifen kann.
3. (**Optional**) Geben Sie einen **Umleitungs-URI an.** Diese Details können später geändert werden, aber wenn Sie die Antwort-URL Ihrer Anwendung kennen, geben Sie sie ein.
4. Klicken Sie auf **Registrieren**.

## <a name="configuring-an-application-as-a-public-application"></a>Konfigurieren einer Anwendung als öffentliche Anwendung

Um Ihre [Desktop-,](.././../active-directory/develop/scenario-desktop-app-registration.md) [mobile oder](.././../active-directory/develop/scenario-mobile-app-registration.md) [Single-Page-Anwendung](.././../active-directory/develop/scenario-mobile-app-registration.md) als öffentliche Anwendung zu konfigurieren, lesen Sie die folgenden Schritte.

1. Wählen Sie [Azure-Portal](https://portal.azure.com)das **Blatt** App-Registrierungen und dann das Blatt **Authentifizierung** aus.

   [![Registrierung öffentlicher Azure-Anwendungen. ](media/dicom-authentication.png) ](media/dicom-authentication.png#lightbox)

2. Wählen Sie **unter Erweiterte Einstellungen** die Option Ja **aus.**

   Wählen Sie für eine Single-Page-Anwendung **Zugriffstoken** und **ID-Token** aus, um den impliziten Fluss zu aktivieren.
   * Wenn Ihre Anwendung Benutzer anmeldet, wählen Sie **ID-Token** aus.
   * Wenn Ihre Anwendung auch eine geschützte Web-API aufrufen muss, wählen Sie **Zugriffstoken** aus.

3. Wählen Sie **Speichern** aus.

## <a name="api-permissions"></a>API-Berechtigungen

Ähnlich wie bei der [vertraulichen Clientanwendung](dicom-register-confidential-client-application.md)müssen Sie auswählen, welche API-Berechtigungen diese Anwendung im Auftrag von Benutzern anfordern muss.

Wenn Sie den DICOM-Dienst verwenden, fügen Sie der Azure-API für DICOM eine Berechtigung hinzu, indem Sie unter von meiner Organisation verwendete APIs nach Azure Healthcare-APIs suchen. Sie finden dies nur, wenn Sie den Ressourcenanbieter Microsoft.HealthcareAPIs bereits registriert haben.

[![Meine API-Berechtigungen. ](media/dicom-request-my-api-permissions.png) ](media/dicom-request-my-api-permissions.png#lightbox)

Wählen Sie die Berechtigungen aus, die die DICOM-Dienstanwendung anfordern soll.

[![Anfordern von API-Berechtigungen. ](media/dicom-request-api-permissions.png) ](media/dicom-request-api-permissions.png#lightbox)

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie eine öffentliche Clientanwendung im Azure Active Directory.  

>[!div class="nextstepaction"]
>[Übersicht über den DICOM-Dienst](dicom-services-overview.md)










