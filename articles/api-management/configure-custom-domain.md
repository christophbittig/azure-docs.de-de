---
title: Konfigurieren eines benutzerdefinierten Domänennamens für die Azure API Management-Instanz
titleSuffix: Azure API Management
description: In diesem Thema erfahren Sie, wie Sie einen benutzerdefinierten Domänennamen für Ihre Azure API Management-Instanz konfigurieren.
services: api-management
documentationcenter: ''
author: vladvino
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 08/24/2021
ms.author: apimpm
ms.openlocfilehash: ae0e5fde64cb3a00f16882698b931c9eadcf623d
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779797"
---
# <a name="configure-a-custom-domain-name-for-your-azure-api-management-instance"></a>Konfigurieren eines benutzerdefinierten Domänennamens für Ihre Azure API Management-Instanz

Wenn Sie eine Azure API Management-Dienstinstanz erstellen, weist Azure dieser einer Unterdomäne von `azure-api.net` zu (z. B. `apim-service-name.azure-api.net`). Sie können Ihre API Management-Endpunkte auch unter Ihrem eigenen benutzerdefinierten Domänennamen verfügbar machen (z. B. **`contoso.com`** ). In diesem Tutorial erfahren Sie, wie Sie Endpunkten, die durch eine API Management-Instanz verfügbar gemacht werden, einen vorhandenen benutzerdefinierten DNS-Namen zuordnen.

> [!IMPORTANT]
> API Management akzeptiert nur Anforderungen, deren [Hostheader](https://tools.ietf.org/html/rfc2616#section-14.23)werte mit Folgendem übereinstimmen:
>
>* Dem Standarddomänennamen
>* Jedem der konfigurierten, benutzerdefinierten Domänennamen

> [!WARNING]
> Wenn Sie die Sicherheit ihrer Anwendungen durch Anheften von Zertifikaten erhöhen möchten, müssen einen benutzerdefinierten Domänennamen und ein selbstverwaltetes Zertifikat (nicht das Standardzertifikat) verwenden. Das Anheften des Standardzertifikats erfordert eine harte Abhängigkeit von den Eigenschaften des Zertifikats, das Sie nicht verwalten. Dies wird nicht empfohlen.

## <a name="prerequisites"></a>Voraussetzungen

-   Ein aktives Azure-Abonnement. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
-   Eine API Management-Instanz. Weitere Informationen finden Sie unter [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md).
-   Einen benutzerdefinierten Domänennamen, der sich in Ihrem Besitz oder im Besitz Ihrer Organisation befindet. Dieses Thema enthält keine Anleitung zum Erwerben eines benutzerdefinierten Domänennamens.
-   Einen [CNAME-Eintrag, der auf einem DNS-Server gehostet wird](#dns-configuration), der den benutzerdefinierten Domänennamen dem Standarddomänennamen Ihrer API Management-Instanz zuordnet. Dieses Thema enthält keine Anleitung zum Hosten eines CNAME-Eintrags.
-   Ein gültiges Zertifikat mit einem öffentlichen und privaten Schlüssel (.pfx). Der Antragstellername oder der alternative Antragstellername (Subject Alternative Name, SAN) muss dem Domänennamen entsprechen, damit die API Management-Instanz auf sichere Weise URLs über TLS verfügbar machen kann.

## <a name="use-the-azure-portal-to-set-a-custom-domain-name"></a>Festlegen eines benutzerdefinierten Domänennamens über das Azure-Portal

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com/) zu Ihrer API Management-Instanz.
1. Klicken Sie auf **Benutzerdefinierte Domänen**.

    Es stehen mehrere Endpunkte zur Verfügung, denen Sie einen benutzerdefinierten Domänennamen zuweisen können. Derzeit sind folgende Endpunkte verfügbar:

    | Endpunkt | Standard |
    | -------- | ----------- |
    | **Gateway** | Der Standardwert ist `<apim-service-name>.azure-api.net`. Gateway ist der einzige Endpunkt, der zur Konfiguration im Tarif „Verbrauch“ verfügbar ist. |
    | **Entwicklerportal (Legacy)** | Der Standardwert ist `<apim-service-name>.portal.azure-api.net`. |
    | **Entwicklerportal** | Der Standardwert ist `<apim-service-name>.developer.azure-api.net`. |
    | **Verwaltung** | Der Standardwert ist `<apim-service-name>.management.azure-api.net`. |
    | **SCM** | Der Standardwert ist `<apim-service-name>.scm.azure-api.net`. |

    > [!NOTE]
    > Sie können jeden der Endpunkte aktualisieren. In der Regel aktualisieren Kunden **Gateway** (diese URL wird zum Aufrufen der über API Management verfügbar gemachten API verwendet) und **Portal** (die URL des Entwicklerportals).
    > 
    > Nur API Management-Instanzbesitzer können **Verwaltungs**- und **SCM**-Endpunkte intern verwenden. Diesen Endpunkten wird seltener ein benutzerdefinierter Domänenname zugewiesen.
    >
    > In den Tarifen **Premium** und **Developer** können mehrere Hostnamen für den Endpunkt **Gateway** festgelegt werden.

1. Wählen Sie **+Hinzufügen** oder einen vorhandenen Endpunkt aus, den Sie aktualisieren möchten.
1. Wählen Sie im Fenster auf der rechten Seite den **Typ** des Endpunkts für die benutzerdefinierte Domäne aus.
1. Geben Sie im Feld **Hostname** den gewünschten Namen an. Beispiel: `api.contoso.com`.
1. Wählen Sie unter **Zertifikat** entweder **Schlüsseltresor** oder **Benutzerdefiniert** aus.
    - **Schlüsseltresor**
        - Klicken Sie auf **Auswählen**.
        - Wählen Sie in der Dropdownliste **Abonnement** aus.
        - Wählen Sie in der Dropdownliste den **Schlüsseltresor** aus.
        - Nachdem die Zertifikate geladen wurden, wählen Sie das **Zertifikat** aus der Dropdownliste aus.
        - Klicken Sie auf **Auswählen**.
    - **Benutzerdefiniert**
        - Wählen Sie das Feld **Zertifikatdatei** aus, um ein Zertifikat auszuwählen und hochzuladen.
        - Wenn das Zertifikat mit einem Kennwort geschützt ist, laden Sie eine gültige PFX-Datei hoch, und geben Sie deren **Kennwort** an.
1. Wählen Sie beim Konfigurieren eines Gatewayendpunkts [nach Bedarf weitere Optionen](#clients-calling-with-server-name-indication-sni-header) aus, bzw. deaktivieren Sie sie, z. B. **Clientzertifikat aushandeln** oder **SSL-Standardbindung**.
1. Wählen Sie **Aktualisieren** aus.

    > [!NOTE]
    > Platzhalterdomänennamen wie `*.contoso.com` werden in allen Tarifen mit Ausnahme des Tarifs „Verbrauch“ unterstützt.

    > [!TIP]
    > Wir empfehlen Ihnen die Verwendung von [Azure Key Vault zur Verwaltung von Zertifikaten](../key-vault/certificates/about-certificates.md). Legen Sie hierfür außerdem die Option `autorenew` fest.
    >
    > Sollten Sie das benutzerdefinierte TLS/SSL-Domänenzertifikat mit Azure Key Vault verwalten, stellen Sie sicher, dass das Zertifikat [als ein _Zertifikat_](/rest/api/keyvault/createcertificate/createcertificate) und nicht als ein _Geheimnis_ in Key Vault eingefügt wird.
    >
    > Zum Abrufen des TLS/SSL-Zertifikats benötigt API Management die Berechtigung zum Auflisten und Abrufen von Geheimnissen für die Azure Key Vault-Instanz, die das Zertifikat enthält. 
    >
    >* Wenn Sie Azure-Portal verwenden, werden alle notwendigen Konfigurationsschritte automatisch durchgeführt. 
    >* Wenn Sie Befehlszeilentools oder eine Verwaltungs-API verwenden, müssen diese Berechtigungen manuell in zwei Schritten erteilt werden:
    >    * Verwenden Sie die Seite **Verwaltete Identitäten** in Ihrer API Management-Instanz, um sicherzustellen, dass die verwaltete Identität aktiviert ist. Notieren Sie sich außerdem die Prinzipal-ID auf dieser Seite. 
    >    * Weisen Sie dieser Prinzipal-ID die Berechtigungsliste und die Berechtigung zum Abrufen geheimer Schlüssel für die Azure Key Vault-Instanz mit dem Zertifikat zu.
    >
    > Wenn für das Zertifikat `autorenew` eingerichtet ist und Ihr API Management-Tarif über eine SLA verfügt (d. h. bei allen Tarifen mit Ausnahme des Developer-Tarifs), wählt API Management automatisch die neuste Version aus, ohne dass der Dienst ausfällt.

1. Klicken Sie auf „Übernehmen“.

    > [!NOTE]
    > Die Zuweisung des Zertifikats kann je nach Größe der Bereitstellung 15 Minuten und länger dauern. Bei der Entwickler-SKU (Developer) tritt eine Ausfallzeit auf, bei der SKU „Basic“ und höheren SKUs jedoch nicht.

[!INCLUDE [api-management-custom-domain](../../includes/api-management-custom-domain.md)]

## <a name="dns-configuration"></a>DNS-Konfiguration

Bei der DNS-Konfiguration für Ihren benutzerdefinierten Domänennamen haben Sie zwei Möglichkeiten:

-   Konfigurieren eines CNAME-Eintrags, der auf den Endpunkt Ihres konfigurierten benutzerdefinierten Domänennamens verweist, oder
-   Konfigurieren eines A-Eintrags, der auf die Gateway-IP-Adresse für API Management verweist

Zwar können Sie sowohl mit CNAME-Einträgen (oder Aliaseinträgen) als auch mit A-Einträgen einem bestimmten Server oder Dienst einen Domänennamen zuordnen, doch beide funktionieren unterschiedlich. 

### <a name="cname-or-alias-record"></a>CNAME- oder Alias-Eintrag
Ein CNAME-Eintrag ordnet einem kanonischen Domänennamen eine *spezifische* Domäne (beispielsweise `contoso.com` oder www\.contoso.com) zu. Nach der Erstellung erstellt der CNAME-Eintrag einen Alias für die Domäne. Der CNAME-Eintrag wird automatisch zu der IP-Adresse Ihres benutzerdefinierten Domänendiensts aufgelöst. Wenn sich also die IP-Adresse ändert, müssen Sie keine Maßnahmen ergreifen.

> [!NOTE]
> Bei einigen Domänenregistrierungen dürfen Sie Unterdomänen nur mit einem CNAME-Eintrag wie „www\.contoso.com“ zuweisen und nicht mit einem Stammnamen wie „contoso.com“. Weitere Informationen zu CNAME-Datensätzen finden Sie in der durch Ihre Registrierung zur Verfügung gestellten Dokumentation, [dem Wikipedia-Eintrag „CNAME Resource Record“](https://en.wikipedia.org/wiki/CNAME_record) oder dem Dokument [IETF Domain Names – Implementation and Specification](https://tools.ietf.org/html/rfc1035) (IEFT-Domänennamen – Implementierung und Spezifizierung, in englischer Sprache).

### <a name="a-record"></a>A-Eintrag
Ein A-Eintrag weist einer IP-Adresse eine Domäne wie `contoso.com` oder **www\.contoso.com** *oder eine Platzhalterdomäne* wie **\*.contoso.com** zu. Da ein A-Eintrag einer statischen IP-Adresse zugeordnet ist, können Änderungen an der IP-Adresse nicht automatisch aufgelöst werden. Es wird empfohlen, anstelle eines A-Eintrags den stabileren CNAME-Eintrag zu verwenden.

> [!NOTE]
> Obgleich die IP-Adresse der API Management-Instanz statisch ist, kann sie sich dennoch in bestimmten Szenarien ändern. Bei der Auswahl der DNS-Konfigurationsmethode wird empfohlen, beim Konfigurieren einer benutzerdefinierten Domäne einen CNAME-Eintrag zu verwenden, da er stabiler als ein A-Eintrag ist, falls sich die IP-Adresse ändert. Weitere Informationen finden Sie im [IP-Dokumentationsartikel](api-management-howto-ip-addresses.md#changes-to-the-ip-addresses) und im [API Management-FAQ](./api-management-faq.yml#how-can-i-secure-the-connection-between-the-api-management-gateway-and-my-back-end-services-).

## <a name="next-steps"></a>Nächste Schritte

[Upgrade and scale an API Management instance](upgrade-and-scale.md) (Upgraden und Skalieren einer API Management-Instanz)