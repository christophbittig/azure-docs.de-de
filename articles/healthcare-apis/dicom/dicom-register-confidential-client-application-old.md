---
title: Registrieren einer vertraulichen Clientanwendung in Azure Active Directory – Azure Healthcare-APIs für DICOM
description: In diesem Artikel wird erläutert, wie Sie eine vertrauliche Clientanwendung in Azure Active Directory registrieren.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2021
ms.author: aersoy
ms.openlocfilehash: f5dd5e5136ac2cd47b637d7b95cb7ba95c3ddaaa
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814697"
---
# <a name="register-a-confidential-client-application"></a>Registrieren einer vertraulichen Clientanwendung

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Tutorial erfahren Sie, wie Sie eine vertrauliche Clientanwendung in Azure Active Directory (Azure AD) registrieren.

## <a name="register-a-new-application"></a>Registrieren einer neuen Anwendung

Eine Clientanwendungsregistrierung ist eine Azure AD Darstellung einer Anwendung, die verwendet werden kann, um sich im Namen eines Benutzers zu authentifizieren und Zugriff auf Ressourcenanwendungen anzufordern. Eine vertrauliche Clientanwendung ist eine Anwendung, die sich beim Speichern eines Geheimnisses und dem Anzeigen dieses Geheimnisses beim Anfordern von Zugriffstoken als vertrauenswürdig erweist. Beispiele für vertrauliche Anwendungen sind serverseitige Anwendungen.

Informationen zum Registrieren einer neuen vertraulichen Clientanwendung finden Sie in den folgenden Schritten.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Azure Active Directory** aus.
2. Wählen Sie das **Blatt App-Registrierungen** aus.
3. Wählen Sie **Neue Registrierung** aus.

   [![Azure-App Registrierungen. ](media/dicom-azure-app-registrations.png) ](media/dicom-azure-app-registrations.png#lightbox)

4. Geben Sie einen benutzerseitigen Anzeigenamen für die Anwendung ein.

   [![Azure registriert eine Anwendung. ](media/dicom-registration-application-name.png) ](media/dicom-registration-application-name.png#lightbox)

5. Wählen Sie unter **Unterstützte Kontotypen** aus, wer die Anwendung verwenden oder auf die API zugreifen kann.
6. (**Optional)** Geben Sie einen **Umleitungs-URI** an. Diese Details können später geändert werden, aber wenn Sie die Antwort-URL Ihrer Anwendung kennen, geben Sie sie ein.
7. Wählen Sie **Registrieren**.

## <a name="api-permissions"></a>API-Berechtigungen

Nachdem Sie Ihre Anwendung registriert haben, müssen Sie auswählen, welche API-Berechtigungen diese Anwendung im Namen von Benutzern anfordern muss.

1. Wählen Sie das Blatt **API-Berechtigungen** aus.

   [![Hinzufügen von API-Berechtigungen ](media/dicom-add-api-permissions.png) ](media/dicom-add-api-permissions.png#lightbox)

2. Wählen Sie **Berechtigung hinzufügen** aus.

   Wenn Sie die Azure Healthcare-APIs verwenden, fügen Sie dem DICOM-Dienst eine Berechtigung hinzu, indem Sie unter von meiner Organisation verwendeten **APIs** nach **Azure API for DICOM** suchen. 

   [![Berechtigungen für ](media/dicom-search-apis-permissions.png) die Such-API ](media/dicom-search-apis-permissions.png#lightbox)

   Das Suchergebnis für Azure API for DICOM wird nur zurückgegeben, wenn Sie den DICOM-Dienst bereits im Arbeitsbereich bereitgestellt haben.

   Wenn Sie auf eine andere Ressourcenanwendung verweisen, wählen Sie Ihre DICOM-API-Ressourcenanwendungsregistrierung aus, die Sie zuvor unter **APIs meine Organisation** erstellt haben.

3. Wählen Sie Bereiche (Berechtigungen) aus, nach denen die vertrauliche Clientanwendung im Namen eines Benutzers fragt. Wählen Sie **user_impersonation** aus, und wählen Sie dann **Berechtigungen hinzufügen** aus.

   [![Wählen Sie Berechtigungsbereiche aus. ](media/dicom-select-scopes.png) ](media/dicom-select-scopes.png#lightbox)

## <a name="application-secret"></a>Anwendungsgeheimnis

1. Wählen Sie **Zertifikate & Geheimnisse** und dann **Neuer geheimer Clientschlüssel** aus.

   [![Zertifikate und Geheimnisse. ](media/dicom-new-client-secret.png) ](media/dicom-new-client-secret.png#lightbox)

2. Geben Sie unter **Beschreibung** eine Beschreibung für Ihren geheimen Clientschlüssel ein. Wählen Sie das Dropdownmenü **Läuft ab** aus, um einen Ablaufzeitpunkt auszuwählen, und klicken Sie dann auf **Hinzufügen.**

   [![Beschreibung des geheimen Clientschlüssels. ](media/dicom-client-secret-description.png) ](media/dicom-client-secret-description.png#lightbox)

3. Kopieren Sie nach dem Erstellen der Clientgeheimniszeichenfolge den **Wert** und die **ID**, und speichern Sie sie an einem sicheren Ort Ihrer Wahl.

   [![Wert-ID des geheimen Clientschlüssels. ](media/dicom-client-secret-value-id.png) ](media/dicom-client-secret-value-id.png#lightbox)

   > [!NOTE]
   > Die zeichenfolge des geheimen Clientschlüssels ist nur einmal im Azure-Portal sichtbar. Wenn Sie von der Webseite Certificates & secrets (Zertifikate & Geheimnisse) weg navigieren und dann zurück zur Webseite zurückkehren, wird die Wertzeichenfolge maskiert. Es ist wichtig, dass Sie ihre geheime Clientzeichenfolge sofort nach der Generierung kopieren. Wenn Sie nicht über eine Sicherungskopie Ihres geheimen Clientschlüssels verfügen, müssen Sie die oben genannten Schritte wiederholen, um es erneut zu generieren.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden Sie durch die Schritte zum Registrieren einer vertraulichen Clientanwendung im Azure AD geführt. Außerdem wurden Sie durch die Schritte zum Hinzufügen von API-Berechtigungen zur Azure-API für DICOM geführt. Schließlich wurde Ihnen gezeigt, wie Sie ein Anwendungsgeheimnis erstellen. 

>[!div class="nextstepaction"]
>[Übersicht über den DICOM-Dienst](dicom-services-overview.md)



