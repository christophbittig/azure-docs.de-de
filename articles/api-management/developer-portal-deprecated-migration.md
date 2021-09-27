---
title: Migrieren zum neuen Entwicklerportal aus dem Legacyentwicklerportal
titleSuffix: Azure API Management
description: Hier erfahren Sie, wie Sie aus dem Legacyentwicklerportal zum neuen Entwicklerportal in API Management migrieren können.
services: api-management
documentationcenter: API Management
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 04/15/2021
ms.author: danlep
ms.openlocfilehash: f09f83fdac6de53f59801e33dd9e668b1009136c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642565"
---
# <a name="migrate-to-the-new-developer-portal"></a>Migrieren zum neuen Entwicklerportal

In diesem Artikel werden die Schritte beschrieben, die Sie ausführen müssen, um aus dem veralteten Legacyportal zu dem neuen Entwicklerportal in API Management zu migrieren.

> [!IMPORTANT]
> Das Legacyentwicklerportal ist mittlerweile veraltet, und es werden nur noch Sicherheitsupdates dafür bereitgestellt. Sie können das Portal bis zu seiner Einstellung im Oktober 2023 wie gewohnt weiter nutzen. Danach wird es dann aus allen API Management-Diensten entfernt.

![Entwicklerportal für API Management](media/api-management-howto-developer-portal/cover.png)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="improvements-in-new-developer-portal"></a>Verbesserungen im neuen Entwicklerportal

Das neue Entwicklerportal überwindet zahlreiche der Einschränkungen des veralteten Portals. Es verfügt über einen [visuellen Drag & Drop-Editor zum Bearbeiten von Inhalten](api-management-howto-developer-portal-customize.md) sowie einen dedizierten Bereich für Designer, um die Website zu formatieren. Seiten, Anpassungen und Konfigurationen werden als Azure Resource Manager-Ressourcen in Ihrem API Management-Dienst gespeichert, wodurch Sie [Portalbereitstellungen automatisieren](automate-portal-deployments.md) können. Schließlich ist die Codebasis des Portals Open-Source, sodass [Sie diese mit benutzerdefinierten Funktionen](api-management-howto-developer-portal.md#managed-vs-self-hosted) erweitern können.

## <a name="how-to-migrate-to-new-developer-portal"></a>Migrieren zum neuen Entwicklerportal

Das neue Entwicklerportal ist nicht mit dem veralteten Portal kompatibel, und eine automatisierte Migration ist nicht möglich. Sie müssen den Inhalt (Seiten, Text, Mediendateien) manuell neu erstellen und das Aussehen des neuen Portals anpassen. Die genauen Schritte unterscheiden sich in Abhängigkeit von den Anpassungen und der Komplexität Ihres Portals. Eine entsprechende Anleitung finden Sie im [Tutorial zum Entwicklerportal](api-management-howto-developer-portal-customize.md). Die restliche Konfiguration, wie die Liste der APIs, Produkte, Benutzer und Identitätsanbieter, wird automatisch von beiden Portalen gemeinsam genutzt.

> [!IMPORTANT]
> Wenn Sie das neue Entwicklerportal zuvor gestartet, aber keine Änderungen vorgenommen haben, setzen Sie den Standardinhalt zurück, um ihn auf die neueste Version zu aktualisieren.

Beachten Sie beim Migrieren aus dem veralteten Portal die folgenden Änderungen:

- Wenn Sie Ihr Entwicklerportal über eine benutzerdefinierte Domäne verfügbar machen, weisen Sie dem neuen Entwicklerportal [eine Domäne zu](configure-custom-domain.md). Verwenden Sie die Option **Entwicklerportal** aus dem Dropdown im Azure-Portal.
- [Wenden Sie eine CORS-Richtlinie](developer-portal-faq.md#cors) auf Ihre APIs an, um die interaktive Testkonsole zu aktivieren.
- Wenn Sie benutzerdefinierte CSS zum Formatieren des Portals einfügen, müssen Sie [das Format mithilfe des integrierten Entwurfsbereichs](api-management-howto-developer-portal-customize.md) replizieren. CSS-Einfügung ist im neuen Portal nicht zulässig.
- Sie können [in der selbstgehosteten Version des neuen Portals](api-management-howto-developer-portal.md#managed-vs-self-hosted) nur benutzerdefiniertes JavaScript einfügen.
- Wenn sich Ihr API Management in einem virtuellen Netzwerk befindet und per Application Gateway über das Internet verfügbar gemacht wird, [lesen Sie diesen Dokumentationsartikel](api-management-howto-integrate-internal-vnet-appgateway.md), um genaue Konfigurationsschritte zu erhalten. Sie müssen folgende Schritte durchführen:

    - Aktivieren der Konnektivität mit dem Verwaltungsendpunkt von API Management.
    - Aktivieren der Konnektivität mit dem Endpunkt des neuen Portals.
    - Deaktivieren ausgewählter Web Application Firewall-Regeln.

- Wenn Sie die E-Mail-Standardbenachrichtigungsvorlagen so geändert haben, dass sie eine explizit definierte, veraltete Portal-URL enthalten, ändern Sie diese so, dass sie entweder den Portal-URL-Parameter verwenden oder auf die neue Portal-URL verweisen. Wenn die Vorlagen stattdessen den integrierten Portal-URL-Parameter verwenden, sind keine Änderungen erforderlich.
- *Issues* und *Anwendungen* werden im neuen Entwicklerportal nicht unterstützt.
- Direkte Integration mit Facebook, Microsoft, Twitter und Google als Identitätsanbieter wird im neuen Entwicklerportal nicht unterstützt. Sie können eine Integration mit diesen Anbietern über Azure AD B2C vornehmen.
- Wenn Sie Delegierung verwenden, ändern Sie die Rückgabe-URL in Ihren Anwendungen, und verwenden Sie den [*Freigegebenes Zugriffstoken abrufen*-API-Endpunkt](/rest/api/apimanagement/2020-12-01/user/get-shared-access-token) anstelle des Endpunkts *SSO-URL generieren*.
- Wenn Sie Azure AD als Identitätsanbieter verwenden:

    - Ändern Sie die Rückgabe-URL in Ihrer Anwendung so, dass sie auf die Domäne des neuen Entwicklerportals verweist.
    - Ändern Sie das Suffix der Rückgabe-URL in Ihrer Anwendung von `/signin-aad` in `/signin`.

- Wenn Sie Azure AD B2C als Identitätsanbieter verwenden:

    - Ändern Sie die Rückgabe-URL in Ihrer Anwendung so, dass sie auf die Domäne des neuen Entwicklerportals verweist.
    - Ändern Sie das Suffix der Rückgabe-URL in Ihrer Anwendung von `/signin-aad` in `/signin`.
    - Nehmen Sie in die Anwendungsansprüche *Vorname*, *Nachname* und *Objekt-ID des Benutzers* auf.

- Wenn Sie OAuth 2.0 in der interaktiven Testkonsole verwenden, ändern Sie die Rückgabe-URL in Ihrer Anwendung so, dass sie auf die Domäne des neuen Entwicklerportals verweist, und ändern Sie das Suffix:

    - Von `/docs/services/[serverName]/console/oauth2/authorizationcode/callback` in `/signin-oauth/code/callback/[serverName]` für den Flow zur Autorisierungscodegenehmigung.
    - Von `/docs/services/[serverName]/console/oauth2/implicit/callback` in `/signin-oauth/implicit/callback` für den Flow zur impliziten Genehmigung.
- Wenn Sie OpenID Connect in der interaktiven Testkonsole verwenden, ändern Sie die Rückgabe-URL in Ihrer Anwendung so, dass sie auf die Domäne des neuen Entwicklerportals verweist, und ändern Sie das Suffix:

    - Von `/docs/services/[serverName]/console/openidconnect/authorizationcode/callback` in `/signin-oauth/code/callback/[serverName]` für den Flow zur Autorisierungscodegenehmigung.
    - Von `/docs/services/[serverName]/console/openidconnect/implicit/callback` in `/signin-oauth/implicit/callback` für den Flow zur impliziten Genehmigung.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Entwicklerportal:

- [Übersicht über das Azure API Management-Entwicklerportal](api-management-howto-developer-portal.md)
- [Zugreifen auf das Entwicklerportal und Anpassen dieses Portals](api-management-howto-developer-portal-customize.md)
