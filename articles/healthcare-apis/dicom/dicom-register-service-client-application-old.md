---
title: Registrieren einer Dienstclientanwendung in Azure Active Directory – Azure Healthcare-APIs für DICOM
description: In diesem Artikel wird erläutert, wie Sie eine Dienstclientanwendung in Azure Active Directory.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2021
ms.author: aersoy
ms.openlocfilehash: 8408d1e2e670083f1e5bb1c30723a3c4ad83b8f2
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814594"
---
# <a name="register-a-service-client-application"></a>Registrieren einer Dienstclientanwendung

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel erfahren Sie, wie Sie eine Dienstclientanwendung in Azure Active Directory (Azure AD.

## <a name="application-registrations-in-the-azure-portal"></a>Anwendungsregistrierungen im Azure-Portal

Clientanwendungsregistrierungen sind Azure AD Darstellungen von Anwendungen, die zum Authentifizieren und Abrufen von Token verwendet werden können. Ein Dienstclient ist zur Verwendung durch eine Anwendung vorgesehen, um ein Zugriffstoken ohne interaktive Authentifizierung eines Benutzers abzurufen. Der Client hat bestimmte Anwendungsberechtigungen und verwendet ein Anwendungsgeheimnis (Kennwort), wenn er Zugriffstoken abruft.

Informationen zum Erstellen eines neuen Dienstclients finden Sie in den folgenden Schritten.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Azure Active Directory** aus.
2. Wählen Sie **Azure Active Directory** Blatt **"App-Registrierungen.**
3. Wählen Sie **Neue Registrierung** aus.

   [![Azure-App Registrierungen. ](media/dicom-azure-app-registrations.png) ](media/dicom-azure-app-registrations.png#lightbox)

4. Geben Sie der DICOM-Dienstanwendung einen anzeigebasierten Anzeigenamen an. Dienstclientanwendungen verwenden in der Regel keine Antwort-URL.

   [![Azure registriert eine Anwendung. ](media/dicom-registration-application-name.png) ](media/dicom-registration-application-name.png#lightbox)

5. Wählen Sie **Registrieren**.

## <a name="api-permissions"></a>API-Berechtigungen

Nachdem Sie Ihre DICOM-Dienstanwendung registriert haben, müssen Sie auswählen, welche API-Berechtigungen diese Anwendung im Auftrag von Benutzern anfordern muss.

1. Wählen Sie das Blatt **API-Berechtigungen** aus.

   [![Fügen Sie API-Berechtigungen hinzu. ](media/dicom-add-api-permissions.png) ](media/dicom-add-api-permissions.png#lightbox)

2. Wählen Sie **Berechtigung hinzufügen** aus.

   Wenn Sie DICOM verwenden, fügen Sie der Azure-API für DICOM eine Berechtigung hinzu, indem Sie unter **APIs,** die meine Organisation verwendet, nach **Azure Healthcare-APIs** suchen. Sie können dies nur finden, wenn Sie den Ressourcenanbieter bereits `Microsoft.HealthcareAPIs` registriert haben.

   [![Meine API-Berechtigungen. ](media/dicom-request-my-api-permissions.png) ](media/dicom-request-my-api-permissions.png#lightbox)


   [![Anfordern von API-Berechtigungen. ](media/dicom-request-api-permissions.png) ](media/dicom-request-api-permissions.png#lightbox)

3. Wählen Sie Bereiche (Berechtigungen) aus, die die vertrauliche Clientanwendung im Auftrag eines Benutzers anfragt.

   [![Wählen Sie Berechtigungsbereich aus. ](media/dicom-select-scopes.png) ](media/dicom-select-scopes.png#lightbox)

4. Gewähren Sie der Anwendung die Einwilligung. Wenn Sie nicht über die erforderlichen Berechtigungen verfügen, wenden Sie sich an Ihren ADD-Administrator.


## <a name="application-secret"></a>Anwendungsgeheimnis

Der Dienstclient benötigt ein Geheimnis (Kennwort), um ein Token abrufen zu können.

1. Wählen Sie **Zertifikate & Geheimnisse** und dann **Neuer geheimer Clientschlüssel** aus.

   [![Zertifikate und Geheimnisse. ](media/dicom-new-client-secret.png) ](media/dicom-new-client-secret.png#lightbox)

2. Geben Sie unter **Beschreibung** eine Beschreibung für Ihren geheimen Clientschlüssel ein. Wählen Sie **das Dropdownmenü Läuft ab** aus, um einen Ablaufzeitrahmen auszuwählen, und klicken Sie dann auf **Hinzufügen.**

   [![Beschreibung des geheimen Clientgeheimnis. ](media/dicom-client-secret-description.png) ](media/dicom-client-secret-description.png#lightbox)

3. Kopieren Sie nach dem Erstellen  der geheimen Clientzeichenfolge den Wert und die **ID,** und speichern Sie sie an einem sicheren Ort Ihrer Wahl.

   [![Wert-ID des geheimen Clientgeheimnis. ](media/dicom-client-secret-value-id.png) ](media/dicom-client-secret-value-id.png#lightbox)

4. Klicken Sie auf „Hinzufügen“.

> [!NOTE]
> Die geheime Clientzeichenfolge ist nur einmal in der Azure-Portal. Wenn Sie von der Webseite Zertifikate & Geheimnisse weg navigieren und dann zurück zur Seite zurückkehren, wird die Wertzeichenfolge maskiert. Es ist wichtig, die geheime Clientzeichenfolge sofort nach der Generung zu kopieren. Wenn Sie keine Sicherungskopie Ihres geheimen Clientgeheimnis haben, müssen Sie die oben genannten Schritte wiederholen, um ihn erneut zu generieren.


## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie eine Dienstclientanwendung in Azure AD. 

>[!div class="nextstepaction"]
>[Übersicht über den DICOM-Dienst](dicom-services-overview.md)




