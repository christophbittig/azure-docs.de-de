---
title: Registrieren einer Clientanwendung in Azure Active Directory für die Azure Healthcare-APIs
description: Registrieren einer Clientanwendung im Azure AD und Hinzufügen eines Geheimnisses und VON API-Berechtigungen zu den Azure Healthcare-APIs
services: healthcare-apis
author: SteveWohl
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 11/17/2021
ms.author: zxue
ms.openlocfilehash: dcb88484144674122f0a108b92f8986084b80b9f
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 11/19/2021
ms.locfileid: "132812596"
---
# <a name="register-a-client-application-in-azure-active-directory"></a>Registrieren einer Clientanwendung in Azure Active Directory

> [!IMPORTANT]
> Azure Healthcare-APIs befinden sich derzeit in der VORSCHAU. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel erfahren Sie, wie Sie eine Clientanwendung in Azure Active Directory (Azure AD) registrieren, um auf die Healthcare-APIs zuzugreifen. Weitere Informationen finden Sie unter [Registrieren einer Anwendung bei der Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md).

## <a name="register-a-new-application"></a>Registrieren einer neuen Anwendung

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Azure Active Directory** aus.
2. Klicken Sie auf **App-Registrierungen**.
[![Screenshot des Fensters "Neue App-Registrierung". ](media/register-application-one.png) ](media/register-application-one.png#lightbox)
3. Wählen Sie **Neue Registrierung** aus.
4. Wählen Sie unter Unterstützte Kontotypen die Option **Nur Konten in diesem Organisationsverzeichnis** aus. Belassen Sie die anderen Optionen wie belassen.
[![Screenshot der Optionen ](media/register-application-two.png) für das neue Registrierungskonto ](media/register-application-two.png#lightbox)
5. Klicken Sie auf **Registrieren**.

## <a name="application-id-client-id"></a>Anwendungs-ID (Client-ID)

Nachdem Sie eine neue Anwendung registriert haben, finden Sie die Anwendungs-ID (Client) und die Verzeichnis-ID (Mandant) über die Menüoption Übersicht. Notieren Sie sich die Werte zur späteren Verwendung.

[![Screenshot des Übersichtsbereichs "Client-ID". ](media/register-application-three.png) ](media/register-application-three.png#lightbox)

[![Screenshot der Client-ID ](media/register-application-four.png) ](media/register-application-four.png#lightbox)

## <a name="authentication-setting-confidential-vs-public"></a>Authentifizierungseinstellung: vertraulich und öffentlich

Klicken Sie auf **Authentifizierung,** um die Einstellungen zu überprüfen. Der Standardwert für **Öffentliche Clientflows zulassen** ist "Nein".

Wenn Sie diesen Standardwert beibehalten, ist die Anwendungsregistrierung eine **vertrauliche Clientanwendung,** und ein Zertifikat oder Geheimnis ist erforderlich.

[![Screenshot der vertraulichen Clientanwendung ](media/register-application-five.png) ](media/register-application-five.png#lightbox)

Wenn Sie den Standardwert für die Option "Öffentliche Clientflows zulassen" in der erweiterten Einstellung in "Ja" ändern, ist die Anwendungsregistrierung eine **öffentliche Clientanwendung,** und es ist kein Zertifikat oder Geheimnis erforderlich. Der Wert "Ja" ist nützlich, wenn Sie die Clientanwendung in Ihrer mobilen App oder in einer JavaScript-App verwenden möchten, in der Sie keine Geheimnisse speichern möchten.

Wählen Sie für Tools, für die eine Umleitungs-URL erforderlich ist, Plattform **hinzufügen** aus, um die Plattform zu konfigurieren.

[![Screenshot: Hinzufügen einer Plattform ](media/register-application-five-alpha.png) ](media/register-application-five-alpha.png#lightbox)

Wählen Sie für Postman die Option **Mobile Anwendungen und Desktopanwendungen** aus. Geben Sie " https://www.getpostman.com/oauth2/callback " " im Abschnitt **Benutzerdefinierte Umleitungs-URIs** ein. Wählen Sie die Schaltfläche **Konfigurieren** aus, um die Einstellung zu speichern.

[![Screenshot: Konfigurieren anderer Dienste ](media/register-application-five-bravo.png) ](media/register-application-five-bravo.png#lightbox)

## <a name="certificates--secrets"></a>Zertifikate & Geheimnisse

Wählen Sie **Zertifikate & Geheimnisse** und dann Neuer **geheimer Clientschlüssel** aus. Wählen Sie im Feld **Läuft ab** die Option Empfohlene **6 Monate** aus. Dieses neue Geheimnis ist sechs Monate lang gültig. Sie können auch verschiedene Werte auswählen, z. B.:
 
* 03 Monate
* 12 Monate
* 24 Monate
* Benutzerdefiniertes Start- und Enddatum.

>[!NOTE]
>Es ist wichtig, dass Sie den Geheimniswert und nicht die Geheimnis-ID speichern.

[![Screenshot: Zertifikate und Geheimnisse ](media/register-application-six.png) ](media/register-application-six.png#lightbox)

Optional können Sie ein Zertifikat (öffentlicher Schlüssel) hochladen und die Zertifikat-ID verwenden, einen GUID-Wert, der dem Zertifikat zugeordnet ist. Zu Testzwecken können Sie mithilfe von Tools wie der PowerShell-Befehlszeile ein selbstsignes Zertifikat erstellen `New-SelfSignedCertificate` und das Zertifikat dann aus dem Zertifikatspeicher exportieren.

## <a name="api-permissions"></a>API-Berechtigungen

Die folgenden Schritte sind für den DICOM-Dienst erforderlich, für den FHIR-Dienst jedoch optional. Darüber hinaus werden Benutzerzugriffsberechtigungen oder Rollenzuweisungen für die Healthcare-APIs über RBAC verwaltet. Weitere Informationen finden Sie unter [Konfigurieren von Azure RBAC für die Gesundheits-APIs.](configure-azure-rbac.md)

1. Wählen Sie das Blatt **API-Berechtigungen** aus.

   [![Hinzufügen von API-Berechtigungen ](dicom/media/dicom-add-api-permissions.png) ](dicom/media/dicom-add-api-permissions.png#lightbox)

2. Wählen Sie **Berechtigung hinzufügen** aus.

   Wenn Sie die Azure Healthcare-APIs verwenden, fügen Sie dem DICOM-Dienst eine Berechtigung hinzu, indem Sie unter von meiner Organisation verwendeten **APIs** nach **Azure API for DICOM** suchen. 

   [![Berechtigungen für ](dicom/media/dicom-search-apis-permissions.png) die Such-API ](dicom/media/dicom-search-apis-permissions.png#lightbox)

   Das Suchergebnis für Azure API for DICOM wird nur zurückgegeben, wenn Sie den DICOM-Dienst bereits im Arbeitsbereich bereitgestellt haben.

   Wenn Sie auf eine andere Ressourcenanwendung verweisen, wählen Sie Ihre DICOM-API-Ressourcenanwendungsregistrierung aus, die Sie zuvor unter **APIs meine Organisation** erstellt haben.

3. Wählen Sie Bereiche (Berechtigungen) aus, nach denen die vertrauliche Clientanwendung im Namen eines Benutzers fragt. Wählen Sie **user_impersonation** aus, und wählen Sie dann **Berechtigungen hinzufügen** aus.

   [![Wählen Sie Berechtigungsbereiche aus. ](dicom/media/dicom-select-scopes.png) ](dicom/media/dicom-select-scopes.png#lightbox)

>[!NOTE]
>Verwenden Sie grant_type von client_credentials, wenn Sie versuchen, ein Zugriffstoken für den FHIR-Dienst mithilfe von Tools wie Postman oder REST-Client auszulassen. Weitere Informationen finden Sie unter [Zugriff mit Postman](use-postman.md) und [Zugreifen auf die ApIs für das Gesundheitswesen mithilfe der REST-Clienterweiterung in Visual Studio Code](using-rest-client.md).
>>Verwenden Sie grant_type von client_credentials oder authentication_doe, wenn Sie versuchen, ein Zugriffstoken für den DICOM-Dienst auszulassen. Weitere Informationen finden Sie unter [Verwenden von DICOM mit cURL.](dicom/dicomweb-standard-apis-curl.md)

Ihre Anwendungsregistrierung ist jetzt abgeschlossen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie eine Clientanwendung im Azure AD registrieren. Darüber hinaus haben Sie erfahren, wie Sie den Azure Healthcare-APIs ein Geheimnis und API-Berechtigungen hinzufügen. Weitere Informationen zu Azure Healthcare-APIs finden Sie unter

>[!div class="nextstepaction"]
>[Übersicht über Azure Healthcare-APIs](healthcare-apis-overview.md)
