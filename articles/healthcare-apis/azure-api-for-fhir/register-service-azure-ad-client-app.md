---
title: 'Registrieren einer Dienst-App in Azure AD: Azure API for FHIR'
description: Erfahren Sie, wie Sie eine Dienstclientanwendung in Azure Active Directory registrieren.
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: cavoeg
ms.openlocfilehash: 3eb942160e45c589377ee3ecd283ea2939a23934
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129273398"
---
# <a name="register-a-service-client-application-in-azure-active-directory-for-azure-api-for-fhir"></a>Registrieren einer Dienstclientanwendung in Azure Active Directory für Azure API for FHIR

In diesem Artikel erfahren Sie, wie Sie eine Dienstclientanwendung in Azure Active Directory registrieren. Clientanwendungsregistrierungen sind Azure Active Directory-Darstellungen von Anwendungen, mit denen Token authentifiziert und abgerufen werden können. Ein Dienstclient ist zur Verwendung durch eine Anwendung vorgesehen, um ein Zugriffstoken ohne interaktive Authentifizierung eines Benutzers abzurufen. Der Client hat bestimmte Anwendungsberechtigungen und verwendet ein Anwendungsgeheimnis (Kennwort), wenn er Zugriffstoken abruft.

Führen Sie die folgenden Schritte aus, um einen neuen Dienstclient zu erstellen.

## <a name="app-registrations-in-azure-portal"></a>App-Registrierungen im Azure-Portal

1. Navigieren Sie im **Azure-Portal** zu [Azure Active Directory](https://portal.azure.com).

2. Wählen Sie **App-Registrierungen** aus.

    ![Azure-Portal. Neue App-Registrierung.](media/add-azure-active-directory/portal-aad-new-app-registration.png)

3. Wählen Sie **Neue Registrierung** aus.

4. Geben Sie dem Dienstclient einen Anzeigenamen. Dienstclientanwendungen verwenden in der Regel keine Antwort-URL.

    :::image type="content" source="media/service-client-app/service-client-registration.png" alt-text="Azure-Portal. Registrierung einer neuen Dienstclient-App.":::

5. Wählen Sie **Registrieren**.

## <a name="api-permissions"></a>API-Berechtigungen

Berechtigungen für Azure API for FHIR werden über RBAC verwaltet. Weitere Informationen finden Sie unter [Konfigurieren von Azure RBAC für FHIR.](configure-azure-rbac.md)

>[!NOTE]
>Verwenden Sie grant_type von client_credentials, wenn Sie versuchen, ein Zugriffstoken für Azure API for FHIR mithilfe von Tools wie Postman zu verwenden. Weitere Informationen finden Sie unter [Testen der FHIR-API auf Azure API for FHIR](tutorial-web-app-test-postman.md).

## <a name="application-secret"></a>Anwendungsgeheimnis

Der Dienstclient benötigt ein Geheimnis (Kennwort), um ein Token abrufen zu können.

1. Wählen Sie **Zertifikate & Geheimnisse** aus.
2. Wählen Sie **Neuer geheimer Clientschlüssel**.

    ![Azure-Portal. Geheimer Schlüssel für Dienstclient](media/add-azure-active-directory/portal-aad-register-new-app-registration-service-client-secret.png)

3. Geben Sie eine Beschreibung und die Dauer des Geheimnisses an (1 Jahr, 2 Jahre oder nie).

4. Sobald das Geheimnis generiert wurde, wird es nur einmal im Portal angezeigt. Notieren Sie sich das Geheimnis, und bewahren Sie es sicher auf.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie eine Dienstclientanwendung in Azure Active Directory registrieren. Als Nächstes testen Sie den Zugriff auf Ihren FHIR-Server über Postman.
 
>[!div class="nextstepaction"]
>[Zugreifen auf den FHIR-Dienst mit postman](./../use-postman.md)
