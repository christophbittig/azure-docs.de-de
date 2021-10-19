---
title: Registrieren einer Clientanwendung in Azure Active Directory für die Azure Healthcare-APIs
description: Registrieren einer Clientanwendung im Azure AD und Hinzufügen eines Geheimnisses und API-Berechtigungen zu den Azure Healthcare-APIs
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 08/25/2021
ms.author: ginle
ms.openlocfilehash: b1ac54f71c9c49af5bb8656bdbe05d639b8bca25
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129278046"
---
# <a name="register-a-client-application-in-azure-active-directory"></a>Registrieren einer Clientanwendung in Azure Active Directory

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Artikel erfahren Sie, wie Sie eine Clientanwendung in Azure Active Directory (Azure AD) registrieren, um auf die ApIs für das Gesundheitswesen zu zugreifen. Weitere Informationen finden Sie unter [Registrieren einer Anwendung beim Microsoft Identity Platform](../active-directory/develop/quickstart-register-app.md).

## <a name="register-a-new-application"></a>Registrieren einer neuen Anwendung

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) die Option **Azure Active Directory** aus.
2. Wählen Sie **App-Registrierungen** aus.
[![Screenshot des Fensters "Neue App-Registrierung". ](media/register-application-one.png) ](media/register-application-one.png#lightbox)
3. Wählen Sie **Neue Registrierung** aus.
4. Wählen Sie unter Unterstützte Kontotypen die Option **Nur Konten in diesem Organisationsverzeichnis aus.** Lassen Sie die anderen Optionen erhalten.
[![Screenshot der Optionen für ein neues Registrierungskonto ](media/register-application-two.png) ](media/register-application-two.png#lightbox)
5. Wählen Sie **Registrieren**.

## <a name="application-id-client-id"></a>Anwendungs-ID (Client-ID)

Nachdem Sie eine neue Anwendung registriert haben, finden Sie die Anwendungs-ID (Client) und die Verzeichnis-ID (Mandant) über die Menüoption "Übersicht". Notieren Sie sich die Werte für die spätere Verwendung.

[![Screenshot des Übersichtsbereichs für die Client-ID. ](media/register-application-three.png) ](media/register-application-three.png#lightbox)

[![Screenshot der Client-ID ](media/register-application-four.png) ](media/register-application-four.png#lightbox)

## <a name="authentication-setting-confidential-vs-public"></a>Authentifizierungseinstellung: vertraulich und öffentlich

Klicken Sie auf **Authentifizierung,** um die Einstellungen zu überprüfen. Der Standardwert für Öffentliche **Clientflüsse zulassen** ist "Nein".

Wenn Sie diesen Standardwert behalten, ist die Anwendungsregistrierung eine vertrauliche **Clientanwendung,** und ein Zertifikat oder Geheimnis ist erforderlich.

[![Screenshot der vertraulichen Clientanwendung. ](media/register-application-five.png) ](media/register-application-five.png#lightbox)

Wenn Sie den Standardwert in "Ja" ändern, ist die Anwendungsregistrierung eine öffentliche **Clientanwendung,** und ein Zertifikat oder Geheimnis ist nicht erforderlich. Der Wert "Ja" ist nützlich, wenn Sie die Clientanwendung in Ihrer mobilen App oder einer JavaScript-App verwenden möchten, in der Sie keine Geheimnisse speichern möchten.

Wählen Sie für Tools, die eine Umleitungs-URL erfordern, **Plattform hinzufügen aus,** um die Plattform zu konfigurieren.

[![Screenshot: Hinzufügen einer Plattform ](media/register-application-five-alpha.png) ](media/register-application-five-alpha.png#lightbox)

Wählen Sie für Postman die Option **Mobile Anwendungen und Desktopanwendungen aus.** Geben Sie https://www.getpostman.com/oauth2/callback " " im Abschnitt **Benutzerdefinierte Umleitungs-URIs** ein. Wählen Sie die **Schaltfläche Konfigurieren** aus, um die Einstellung zu speichern.

[![Screenshot: Konfigurieren anderer Dienste ](media/register-application-five-bravo.png) ](media/register-application-five-bravo.png#lightbox)

## <a name="certificates--secrets"></a>Zertifikate & Geheimnisse

Wählen **Sie Zertifikate & Geheimnisse und** dann Neuer **geheimer Clientgeheimnis aus.** Wählen **Sie im Feld** Läuft ab die Option Empfohlene **6 Monate** aus. Dieses neue Geheimnis ist sechs Monate lang gültig. Sie können auch verschiedene Werte auswählen, z. B.:
 
* 03 Monate
* 12 Monate
* 24 Monate
* Benutzerdefiniertes Start- und Enddatum.

>[!NOTE]
>Es ist wichtig, dass Sie den Geheimniswert speichern, nicht die Geheimnis-ID.

[![Screenshot: Zertifikate und ](media/register-application-six.png) Geheimnisse ](media/register-application-six.png#lightbox)

Optional können Sie ein Zertifikat (öffentlicher Schlüssel) hochladen und die Zertifikat-ID verwenden, einen GUID-Wert, der dem Zertifikat zugeordnet ist. Zu Testzwecken können Sie ein selbstsigniertes Zertifikat mitHilfe von Tools wie der PowerShell-Befehlszeile und erstellen und dann das Zertifikat aus `New-SelfSignedCertificate` dem Zertifikatspeicher exportieren.

## <a name="api-permissions"></a>API-Berechtigungen

Berechtigungen für die Gesundheits-APIs werden über die RBAC verwaltet. Weitere Informationen finden Sie unter [Konfigurieren von Azure RBAC für den FHIR-Dienst.](./fhir/configure-azure-rbac-for-fhir.md)

>[!NOTE]
>Verwenden grant_type von client_credentials, wenn Sie versuchen, ein Zugriffstoken für den FHIR-Dienst mithilfe von Tools wie Postman oder Rest Client zu erstellen. Weitere Informationen finden Sie unter [Access using Postman (Zugriff mit Postman)](use-postman.md) und [Accessing the Healthcare APIs using the REST Client Extension in Visual Studio Code](using-rest-client.md).

Ihre Anwendungsregistrierung ist jetzt abgeschlossen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie eine Clientanwendung im Azure AD. Darüber hinaus haben Sie erfahren, wie Sie den Azure Healthcare-APIs ein Geheimnis und API-Berechtigungen hinzufügen. Weitere Informationen zu Azure Healthcare-APIs finden Sie unter

>[!div class="nextstepaction"]
>[Übersicht über Azure Healthcare-APIs](healthcare-apis-overview.md)
