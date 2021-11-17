---
title: Häufig gestellte Fragen zu Anwendungsverwaltungszertifikaten
titleSuffix: Azure AD
description: Hier finden Sie Antworten auf häufig gestellte Fragen (FAQ) zum Verwalten von Zertifikaten für Apps, die Azure Active Directory als Identitätsanbieter (IdP) verwenden.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 03/19/2021
ms.author: davidmu
ms.reviewer: sureshja, saumadan
ms.openlocfilehash: e52f06d102c98979005c08f51ef394c29389bbce
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132553304"
---
# <a name="application-management-certificates-frequently-asked-questions"></a>Häufig gestellte Fragen zu Anwendungsverwaltungszertifikaten

Auf dieser Seite finden Sie Antworten auf häufig gestellte Fragen zum Verwalten von Zertifikaten für Apps, die Azure Active Directory (Azure AD) als Identitätsanbieter (IdP) verwenden.

## <a name="is-there-a-way-to-generate-a-list-of-expiring-saml-signing-certificates"></a>Gibt es eine Möglichkeit, eine Liste von ablaufenden SAML-Signaturzertifikaten zu generieren?

Sie können mit [PowerShell-Skripts](app-management-powershell-samples.md) alle App-Registrierungen mit ablaufenden Geheimnissen und Zertifikaten und ihren Besitzern für die angegebenen Apps aus Ihrem Verzeichnis in eine CSV-Datei exportieren.

## <a name="where-can-i-find-the-information-about-soon-to-expire-certificates-renewal-steps"></a>Wo finde ich die Informationen zu den Erneuerungsschritten für Zertifikate, die in Kürze ablaufen?

Die Schritte finden Sie [hier](manage-certificates-for-federated-single-sign-on.md#renew-a-certificate-that-will-soon-expire).

## <a name="how-can-i-customize-the-expiration-date-for-the-certificates-issued-by-azure-ad"></a>Wie kann ich das Ablaufdatum für die von Azure AD ausgestellten Zertifikate anpassen?

Standardmäßig konfiguriert Azure AD ein Zertifikat so, dass es nach drei Jahren abläuft, wenn es während der SSO-Konfiguration von SAML automatisch erstellt wird. Da Sie das Datum eines Zertifikats nach dem Speichern des Zertifikats nicht ändern können, müssen Sie ein neues Zertifikat erstellen. Die Schritte dafür finden Sie unter [Anpassen des Ablaufdatums für das Verbundzertifikat und Rollover zu einem neuen Zertifikat](manage-certificates-for-federated-single-sign-on.md#customize-the-expiration-date-for-your-federation-certificate-and-roll-it-over-to-a-new-certificate).

## <a name="how-can-i-automate-the-certificates-expiration-notifications"></a>Wie kann ich die Ablaufbenachrichtigungen für Zertifikate automatisieren?

Azure AD sendet 60, 30 und 7 Tage vor Ablauf des SAML-Zertifikats eine Benachrichtigung per E-Mail. Sie können mehrere E-Mail-Adressen für den Empfang von Benachrichtigungen hinzufügen.

> [!NOTE]
> Sie können der Benachrichtigungsliste bis zu 5 E-Mail-Adressen hinzufügen (einschließlich der E-Mail-Adresse des Administrators, der die Anwendung hinzugefügt hat). Wenn Sie mehr Personen Benachrichtigen möchten, verwenden Sie die E-Mail-Verteilerliste.

Unter [Hinzufügen von E-Mail-Adressen für Benachrichtigungen für den Zertifikatablauf](manage-certificates-for-federated-single-sign-on.md#add-email-notification-addresses-for-certificate-expiration) können Sie die E-Mail-Adressen angeben, an die die Benachrichtigungen gesendet werden sollen.

Diese E-Mail-Benachrichtigungen, die von `aadnotification@microsoft.com` empfangen werden, können nicht bearbeitet oder angepasst werden. Sie können jedoch App-Registrierungen mit ablaufenden Geheimnissen und Zertifikaten über [PowerShell-Skripts](app-management-powershell-samples.md) exportieren.

## <a name="who-can-update-the-certificates"></a>Wer kann die Zertifikate aktualisieren?

Der Besitzer der Anwendung, globale Administrator oder Anwendungsadministrator kann die Zertifikate über die Benutzeroberfläche des Azure-Portals, PowerShell oder Microsoft Graph aktualisieren.

## <a name="i-need-more-details-about-certificate-signing-options"></a>Ich benötige weitere Details zu den Optionen für die Zertifikatsignatur

In Azure AD können Sie die Zertifikatsignaturoptionen und den Zertifikatsignaturalgorithmus einrichten. Weitere Informationen finden Sie unter [Erweiterte Zertifikatsignaturoptionen im SAML-Token für Azure AD-Apps](certificate-signing-options.md).

## <a name="i-need-to-replace-the-certificate-for-azure-ad-application-proxy-applications-and-need-more-instructions"></a>Ich muss das Zertifikat für Anwendungen mit Azure AD-Anwendungsproxy ersetzen und benötige weitere Anweisungen

Informationen zum Ersetzen von Zertifikaten für Anwendungen mit Azure AD Anwendungsproxys finden Sie unter [PowerShell-Beispiel: Ersetzen des Zertifikats in Anwendungsproxy-Apps](../app-proxy/scripts/powershell-get-custom-domain-replace-cert.md).

## <a name="how-do-i-manage-certificates-for-custom-domains-in-azure-ad-application-proxy"></a>Wie verwalte ich Zertifikate für benutzerdefinierte Domänen im Azure AD-Anwendungsproxy?

Zum Konfigurieren einer lokalen App für die Verwendung einer benutzerdefinierten Domäne benötigen Sie eine verifizierte benutzerdefinierte Azure Active Directory-Domäne, ein PFX-Zertifikat für die benutzerdefinierte Domäne und eine lokale App für die Konfiguration. Weitere Informationen finden Sie unter [Benutzerdefinierte Domänen im Azure AD-Anwendungsproxy](../app-proxy/application-proxy-configure-custom-domain.md).

## <a name="i-need-to-update-the-token-signing-certificate-on-the-application-side-where-can-i-get-it-on-azure-ad-side"></a>Ich muss das Tokensignaturzertifikat auf Anwendungsseite aktualisieren. Wo kann ich es in Azure AD erhalten?

Sie können ein SAML X.509-Zertifikat erneuern. Weitere Informationen finden Sie unter [SAML-Signaturzertifikat](configure-saml-single-sign-on.md#saml-signing-certificate).

## <a name="what-is-azure-ad-signing-key-rollover"></a>Was ist ein Rollover von Signaturschlüsseln in Azure AD?

Weitere Informationen finden Sie [hier](../develop/active-directory-signing-key-rollover.md).

## <a name="how-do-i-renew-application-token-encryption-certificate"></a>Wie erneuere ich das Tokenverschlüsselungszertifikat einer Anwendung?

Weitere Informationen zum Erneuern eines Tokenverschlüsselungszertifikats finden Sie unter [Erneuern eines Tokenverschlüsselungszertifikats für eine Unternehmensanwendung](howto-saml-token-encryption.md).

## <a name="how-do-i-renew-application-token-signing-certificate"></a>Wie erneuere ich das Tokensignaturzertifikat einer Anwendung?

Weitere Informationen zum Erneuern eines Tokensignaturzertifikats finden Sie unter [Erneuern eines Tokensignaturzertifikats für eine Unternehmensanwendung](manage-certificates-for-federated-single-sign-on.md).

## <a name="how-do-i-update-azure-ad-after-changing-my-federation-certificates"></a>Wie aktualisiere ich Azure AD nach dem Ändern meines Verbundzertifikats?

Informationen zum Aktualisieren von Azure AD nach dem Ändern Ihres Verbundzertifikats finden Sie unter [Erneuern von Verbundzertifikaten für Microsoft 365 und Azure Active Directory](../hybrid/how-to-connect-fed-o365-certs.md).
