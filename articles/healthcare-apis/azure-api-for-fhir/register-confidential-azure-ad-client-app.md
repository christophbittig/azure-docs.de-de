---
title: 'Registrieren einer vertraulichen Client-App in Azure AD: Azure API for FHIR'
description: Registrieren einer vertraulichen Clientanwendung in Azure Active Directory, die sich im Namen eines Benutzers authentifiziert und den Zugriff auf Ressourcenanwendungen anfordert
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 09/10/2021
ms.author: cavoeg
ms.openlocfilehash: 2851f230a19bfbafb514e9fb22fd3f37de1f610b
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129272545"
---
# <a name="register-a-confidential-client-application-in-azure-active-directory-for-azure-api-for-fhir"></a>Registrieren einer vertraulichen Clientanwendung in Azure Active Directory für Azure API for FHIR

In diesem Tutorial erfahren Sie, wie Sie eine vertrauliche Clientanwendung in Azure Active Directory (Azure AD.  

Eine Clientanwendungsregistrierung ist eine Azure AD Darstellung einer Anwendung, die verwendet werden kann, um sich im Namen eines Benutzers zu authentifizieren und Zugriff auf [Ressourcenanwendungen an fordern zu können.](register-resource-azure-ad-client-app.md) Eine vertrauliche Clientanwendung ist eine Anwendung, die sich beim Speichern eines Geheimnisses und dem Anzeigen dieses Geheimnisses beim Anfordern von Zugriffstoken als vertrauenswürdig erweist. Beispiele für vertrauliche Anwendungen sind serverseitige Anwendungen. 

Informationen zum Registrieren einer neuen vertraulichen Clientanwendung finden Sie in den folgenden Schritten. 

## <a name="register-a-new-application"></a>Registrieren einer neuen Anwendung

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Azure Active Directory** aus.

1. Wählen Sie **App-Registrierungen** aus. 

    :::image type="content" source="media/add-azure-active-directory/portal-aad-new-app-registration.png" alt-text="Azure-Portal. Neue App-Registrierung.":::

1. Wählen Sie **Neue Registrierung** aus.

1. Geben Sie der Anwendung einen anzeigebasierten Namen.

1. Wählen **Sie unter Unterstützte Kontotypen** aus, wer die Anwendung verwenden oder auf die API zugreifen kann.

1. (Optional) Geben Sie einen **Umleitungs-URI an.** Diese Details können später geändert werden, aber wenn Sie die Antwort-URL Ihrer Anwendung kennen, geben Sie sie jetzt ein.

    :::image type="content" source="media/add-azure-active-directory/portal-aad-register-new-app-registration-confidential-client.png" alt-text="Registrierung einer neuen vertraulichen Client-App.":::

1. Wählen Sie **Registrieren**.

## <a name="api-permissions"></a>API-Berechtigungen

Berechtigungen für Azure API for FHIR werden über RBAC verwaltet. Weitere Informationen finden Sie unter [Konfigurieren von Azure RBAC for FHIR.](configure-azure-rbac.md)

>[!NOTE]
>Verwenden grant_type von client_credentials, wenn Sie versuchen, ein Zugriffstoken für Azure API for FHIR Tools wie Postman zu verwenden. Weitere Informationen finden Sie unter [Testen der FHIR-API auf Azure API for FHIR](tutorial-web-app-test-postman.md).


## <a name="application-secret"></a>Anwendungsgeheimnis

1. Wählen Sie **Zertifikate & Geheimnisse** und dann **Neuer geheimer Clientschlüssel** aus. 

    :::image type="content" source="media/add-azure-active-directory/portal-aad-register-new-app-registration-confidential-client-secret.png" alt-text="Vertraulicher Client. Anwendungsgeheimnis.":::

1. Geben Sie unter **Beschreibung** eine Beschreibung für Ihren geheimen Clientschlüssel ein. Wählen Sie **das Dropdownmenü Läuft ab** aus, um einen Ablaufzeitrahmen auszuwählen, und klicken Sie dann auf **Hinzufügen.**

   :::image type="content" source="media/add-azure-active-directory/add-a-client-secret.png" alt-text="Fügen Sie einen geheimen Clientgeheimnis hinzu.":::

1. Kopieren Sie nach dem Erstellen  der geheimen Clientzeichenfolge den Wert und die **ID,** und speichern Sie sie an einem sicheren Ort Ihrer Wahl.

   :::image type="content" source="media/add-azure-active-directory/client-secret-string-password.png" alt-text="Geheime Clientzeichenfolge."::: 

> [!NOTE]
>Die geheime Clientzeichenfolge ist nur einmal in der Azure-Portal. Wenn Sie von der Webseite Zertifikate & Geheimnisse weg navigieren und dann zurück zur Seite zurückkehren, wird die Wertzeichenfolge maskiert. Es ist wichtig, die geheime Clientzeichenfolge sofort nach der Generung zu kopieren. Wenn Sie keine Sicherungskopie Ihres geheimen Clientgeheimnis haben, müssen Sie die oben genannten Schritte wiederholen, um sie erneut zu generieren.
 
## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie die Schritte zum Registrieren einer vertraulichen Clientanwendung im Azure AD. Außerdem wurden Sie durch die Schritte zum Hinzufügen von API-Berechtigungen zur Azure Healthcare-API geführt. Schließlich haben Sie erfahren, wie Sie ein Anwendungsgeheimnis erstellen. Darüber hinaus erfahren Sie, wie Sie mithilfe von Postman auf Ihren FHIR-Server zugreifen.
 
>[!div class="nextstepaction"]
>[Zugreifen auf den FHIR-Dienst mithilfe von Postman](./../use-postman.md)
