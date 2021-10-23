---
title: Veröffentlichen einer lokalen SharePoint-Farm mit dem Azure Active Directory-Anwendungsproxy
description: Hier werden die Grundlagen der Konfiguration einer lokalen SharePoint-Farm mit dem Azure Active Directory-Anwendungsproxy für SAML erörtert.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 2821cfeb2fc3df05d0b8862ad67adf9dd719ebd8
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129989283"
---
# <a name="integrate-azure-active-directory-application-proxy-with-sharepoint-saml"></a>Integrieren des Azure Active Directory-Anwendungsproxys in SharePoint (SAML)

Diese Schritt-für-Schritt-Anleitung erläutert, wie Sie den Zugriff auf das [in SharePoint (lokal) integrierte Azure Active Directory (SAML)](../saas-apps/sharepoint-on-premises-tutorial.md) mithilfe eines Azure AD-Anwendungsproxys sichern, wenn Benutzer in Ihrer Organisation (Azure AD, B2B) über das Internet eine Verbindung mit SharePoint herstellen.

> [!NOTE]
> Wenn Sie noch nicht mit dem Azure AD-Anwendungsproxy vertraut sind und mehr darüber erfahren möchten, finden Sie weitere Informationen im Artikel [Remotezugriff auf lokale Anwendungen über den Azure AD-Anwendungsproxy](./application-proxy.md).

Diese Konfiguration bietet drei Hauptvorteile:

- Der Azure AD-Anwendungsproxy stellt sicher, dass der authentifizierte Datenverkehr das interne Netzwerk und SharePoint erreichen kann.
- Die Benutzer können wie gewohnt auf SharePoint-Websites zugreifen, ohne VPN zu verwenden.
- Sie können den Zugriff nach Benutzerzuweisung auf Ebene des Azure AD-Anwendungsproxys steuern, und Sie können mit Azure AD-Features wie bedingtem Zugriff und Multi-Factor Authentication (MFA) die Sicherheit erhöhen.

Dieser Prozess erfordert zwei Unternehmensanwendungen. Eine ist eine lokale SharePoint-Instanz, die Sie aus dem Katalog in der Liste der verwalteten SaaS-Apps veröffentlichen. Bei der zweiten handelt es sich um eine lokale Anwendung (keine Anwendung aus dem Katalog), die Sie zum Veröffentlichen der ersten Unternehmensanwendung aus dem Katalog verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Um diese Konfiguration abschließen zu können, benötigen Sie die folgenden Ressourcen:
 - Eine SharePoint 2013-Farm (oder neuer). Die SharePoint-Farm muss [in Azure AD integriert](../saas-apps/sharepoint-on-premises-tutorial.md) sein.
 - Einen Azure AD-Mandanten mit einem Plan, der den Anwendungsproxy enthält. Weitere Informationen zu Azure AD-Tarifen und Preisen finden Sie [hier](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing).
 - Eine [benutzerdefinierte überprüfte Domäne](../fundamentals/add-custom-domain.md) im Azure AD-Mandanten. Die überprüfte Domäne muss mit dem SharePoint-URL-Suffix übereinstimmen.
 - Ein SSL-Zertifikat ist erforderlich. Weitere Informationen finden Sie unter [Konfigurieren von benutzerdefinierten Domänen](./application-proxy-configure-custom-domain.md).
 - Benutzer von lokalem Active Directory müssen mit Azure AD Connect synchronisiert werden und für die [Anmeldung bei Azure](../hybrid/plan-connect-user-signin.md) konfiguriert sein. 
 - Für auf die Cloud beschränkte Gastbenutzer und B2B-Gastbenutzer müssen Sie [im Azure-Portal den Zugriff auf ein Gastkonto für SharePoint (lokal) gewähren](../saas-apps/sharepoint-on-premises-tutorial.md#manage-guest-users-access).
 - Einen Anwendungsproxyconnector, der auf einem Computer in der Unternehmensdomäne installiert ist und ausgeführt wird.


## <a name="step-1-integrate-sharepoint-on-premises-with-azure-ad"></a>Schritt 1: Integrieren von SharePoint (lokal) in Azure AD

1. Konfigurieren Sie die lokale SharePoint-App. Weitere Informationen finden Sie unter [Integration des einmaligen Anmeldens von Azure Active Directory mit SharePoint (lokal)](../saas-apps/sharepoint-on-premises-tutorial.md).
2. Überprüfen Sie die Konfiguration, bevor Sie mit dem nächsten Schritt fortfahren. Versuchen Sie zu diesem Zweck, über das interne Netzwerk auf SharePoint (lokal) zuzugreifen, und überprüfen Sie, ob der interne Zugriff möglich ist.


## <a name="step-2-publish-the-sharepoint-on-premises-application-with-application-proxy"></a>Schritt 2: Veröffentlichen der lokalen Sharepoint-Anwendung mit dem Anwendungsproxy

In diesem Schritt wird in Ihrem Azure AD-Mandanten eine Anwendung erstellt, die den Anwendungsproxy verwendet. Sie legen die externe URL fest und geben die interne URL an. Beide werden später in SharePoint verwendet.

> [!NOTE]
> Die interne und externe URL müssen der **Anmelde-URL** in der SAML-basierten Anwendungskonfiguration in Schritt 1 entsprechen.

   ![Screenshot: der Wert der Anmelde-URL](./media/application-proxy-integrate-with-sharepoint-server/sso-url-saml.png)


 1. Erstellen Sie einen neuen Azure AD Anwendungsproxy mit einer benutzerdefinierten Domäne. Eine Schritt-für-Schritt-Anleitung finden Sie unter [Benutzerdefinierte Domänen im Azure AD-Anwendungsproxy](./application-proxy-configure-custom-domain.md).

    - Interne URL: https://portal.contoso.com/
    - Externe URL: https://portal.contoso.com/
    - Vorauthentifizierung: Azure Active Directory
    - URLs übersetzen in Headern: Nein
    - URLs übersetzen in Anwendungstext: Nein

        ![Screenshot mit den Optionen, die Sie zum Erstellen der App verwenden](./media/application-proxy-integrate-with-sharepoint-server/create-application-azure-active-directory.png)

2. Weisen Sie die [gleichen Gruppen](../saas-apps/sharepoint-on-premises-tutorial.md#grant-permissions-to-a-security-group) zu, die Sie der lokalen SharePoint-Anwendung aus dem Katalog zugewiesen haben.

3. Wechseln Sie zum Abschnitt **Eigenschaften**, und legen Sie **Für Benutzer sichtbar?** auf **Nein** fest. Mit dieser Option wird sichergestellt, dass im Portal „Meine Apps“ (https://myapplications.microsoft.com) ) nur das Symbol der ersten Anwendung angezeigt wird.

   ![Screenshot, in dem die Option „Für Benutzer sichtbar?“ markiert ist](./media/application-proxy-integrate-with-sharepoint-server/configure-properties.png)
 
## <a name="step-3-test-your-application"></a>Schritt 3: Testen der Anwendung

Navigieren Sie mit einem Browser auf einem Computer in einem externen Netzwerk zum Link, den Sie im Veröffentlichungsschritt konfiguriert haben. Stellen Sie sicher, dass Sie sich mit dem Testkonto anmelden können, das Sie eingerichtet haben.
