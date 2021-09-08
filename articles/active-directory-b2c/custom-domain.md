---
title: Aktivieren von benutzerdefinierten Domänen für Azure AD B2C
titleSuffix: Azure AD B2C
description: Erfahren Sie, wie Sie benutzerdefinierte Domänen in Umleitungs-URLs für Azure Active Directory B2C aktivieren.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/16/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: b64806b3683db8f6cd3ec665b462f4f6f26397eb
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770152"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>Aktivieren von benutzerdefinierten Domänen für Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

In diesem Artikel wird beschrieben, wie Sie benutzerdefinierte Domänen in Umleitungs-URLs für Azure Active Directory B2C (Azure AD B2C) aktivieren. Die Verwendung einer benutzerdefinierten Domäne für Ihre Anwendung ermöglicht eine einheitliche Benutzeroberfläche. Aus der Perspektive der Benutzer bleiben diese während des Anmeldevorgangs in Ihrer Domäne und werden nicht auf die Azure AD B2C-Standarddomäne *&lt;Mandantenname&gt;.b2clogin.com* umgeleitet.

![Screenshot der Benutzeroberfläche für benutzerdefinierten Azure AD B2C-Domänen.](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>Übersicht über benutzerdefinierte Domänen

Sie können benutzerdefinierte Domänen für Azure AD B2C mithilfe von [Azure Front Door](https://azure.microsoft.com/services/frontdoor/) aktivieren. Azure Front Door ist ein globaler Einstiegspunkt, der es ermöglicht, über das globale Microsoft-Edge-Netzwerk schnelle, sichere und umfassend skalierbare Webanwendungen zu erstellen. Sie können Azure AD B2C-Inhalte hinter Azure Front Door rendern und dann eine Option in Azure Front Door konfigurieren, um die Inhalte über eine benutzerdefinierte Domäne in der URL Ihrer Anwendung bereitzustellen.

Die Integration in Azure Front Door wird in der Abbildung unten veranschaulicht:

1. Ein Benutzer wählt die Anmeldeschaltfläche in einer Anwendung aus, über die er auf die Azure AD B2C-Anmeldeseite gelangt. Auf dieser Seite ist ein benutzerdefinierter Domänenname angegeben.
1. Der benutzerdefinierte Domänenname wird im Webbrowser in die IP-Adresse von Azure Front Door aufgelöst. Bei der DNS-Auflösung zeigt ein kanonischer Namenseintrag (CNAME-Eintrag) mit einem benutzerdefinierten Domänennamen auf den standardmäßigen Front Door-Front-End-Host (z. B. `contoso.azurefd.net`). 
1. Der an die benutzerdefinierte Domäne (z. B. `login.contoso.com`) gerichtete Datenverkehr wird an den angegebenen standardmäßigen Front Door-Front-End-Host (`contoso.azurefd.net`) weitergeleitet.
1. In Azure Front Door werden die Azure AD B2C-Inhalte über die Azure AD B2C-Standarddomäne `<tenant-name>.b2clogin.com` aufgerufen. Die Anforderung an den Azure AD B2C-Endpunkt enthält den ursprünglichen benutzerdefinierten Domänennamen.
1. In Azure AD B2C wird durch Anzeigen der relevanten Inhalte und der ursprünglichen benutzerdefinierten Domäne auf die Anforderung geantwortet.

![Diagramm des Netzwerkflusses für benutzerdefinierte Domänen.](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> Die Verbindung zwischen dem Browser und Azure Front Door sollte immer über IPv4 und nicht über IPv6 erfolgen.

Bei der Verwendung von benutzerdefinierten Domänen ist Folgendes zu beachten:

- Sie können mehrere benutzerdefinierte Domänen einrichten. Informationen zur maximalen Anzahl unterstützter benutzerdefinierter Domänen finden Sie unter [Dienst- und andere Einschränkungen](../active-directory/enterprise-users/directory-service-limits-restrictions.md) für Azure AD B2C und unter [Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits) für Azure Front Door.
- Azure Front Door ist ein separater Azure-Dienst, sodass zusätzliche Gebühren anfallen. Weitere Informationen finden Sie unter [Azure Front Door – Preise](https://azure.microsoft.com/pricing/details/frontdoor).
- Um [Web Application Firewall](../web-application-firewall/afds/afds-overview.md) für Azure Front Door verwenden zu können, müssen Sie prüfen, ob die Firewallkonfiguration und -regeln ordnungsgemäß mit den Azure AD B2C-Benutzerflows ausgeführt werden.
- Nachdem Sie benutzerdefinierte Domänen konfiguriert haben, können Benutzer weiterhin auf den Azure AD B2C-Standarddomänennamen *&lt;Mandantenname&gt;.b2clogin.com* zugreifen (es sei denn, Sie verwenden eine benutzerdefinierte Richtlinie und [blockieren den Zugriff](#block-access-to-the-default-domain-name)).
- Wenn Sie mehrere Anwendungen verwenden, migrieren Sie alle in die benutzerdefinierte Domäne, da die Azure AD B2C-Sitzung im Browser unter dem aktuell verwendeten Domänennamen gespeichert wird.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant"></a>Schritt 1: Hinzufügen eines benutzerdefinierten Domänennamens zum Azure AD B2C-Mandanten

Jeder neue Azure AD B2C-Mandant verfügt über den anfänglichen Domänennamen „&lt;domänenname&gt;.onmicrosoft.com“. Der anfängliche Domänenname kann nicht geändert oder gelöscht werden, Sie können jedoch einen benutzerdefinierten Domänennamen hinzufügen. 

Führen Sie die folgenden Schritte aus, um Ihrem Mandanten eine benutzerdefinierte Azure AD B2C-Domäne hinzuzufügen:

1. [Hinzufügen des benutzerdefinierten Domänennamens zu Azure AD](../active-directory/fundamentals/add-custom-domain.md#add-your-custom-domain-name-to-azure-ad).

    > [!IMPORTANT]
    > Melden Sie sich zum Ausführen dieser Schritte bei Ihrem **Azure AD B2C**-Mandanten an, und wählen Sie den Dienst **Azure Active Directory** aus.

1. [Hinzufügen Ihrer DNS-Informationen in der Domänenregistrierungsstelle](../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar). Nachdem Sie Ihren benutzerdefinierten Domänennamen zu Azure AD hinzugefügt haben, erstellen Sie einen DNS-`TXT`- oder -`MX`-Eintrag für Ihre Domäne. Über die Erstellung dieses DNS-Eintrags wird der Besitz des Domänennamens bestätigt.

    Die folgenden Beispiele zeigen TXT-Einträge für *login.contoso.com* und *account.contoso.com*:

    |Name (Hostname)  |Typ  |Daten  |
    |---------|---------|---------|
    |login   | TXT  | MS=ms12345678  |
    |account | TXT  | MS=ms87654321  |
    
    Der TXT-Eintrag muss der Unterdomäne oder dem Hostnamen der Domäne zugeordnet sein. Beispielsweise dem Teil *login* der Domäne *contoso.com*. Wenn der Hostname leer oder `@` ist, kann Azure AD die von Ihnen hinzugefügte benutzerdefinierte Domäne nicht überprüfen. In den folgenden Beispielen sind beide Einträge falsch konfiguriert.
    
    |Name (Hostname)  |Typ  |Daten  |
    |---------|---------|---------|
    | | TXT  | MS=ms12345678  |
    | @ | TXT  | MS=ms12345678  | 
    
    > [!TIP]
    > Sie können Ihre benutzerdefinierte Domäne mit einem beliebigen öffentlich verfügbaren DNS-Dienst wie GoDaddy verwalten. Wenn Sie über keinen DNS-Server verfügen, können Sie eine [Azure DNS-Zone](../dns/dns-getstarted-portal.md) oder [App Service-Domänen](../app-service/manage-custom-dns-buy-domain.md) verwenden.

1. [Überprüfen des benutzerdefinierten Domänennamens](../active-directory/fundamentals/add-custom-domain.md#verify-your-custom-domain-name). Überprüfen Sie alle Unterdomänen oder Hostnamen, die Sie verwenden möchten. Für die Anmeldung bei *login.contoso.com* und *account.contoso.com* müssen Sie beispielsweise beide Unterdomänen und nicht die Domäne der obersten Ebene *contoso.com* überprüfen. 

    **Löschen** Sie nach dem Überprüfen der Domäne den erstellten DNS-TXT-Eintrag.

    
## <a name="step-2-create-a-new-azure-front-door-instance"></a>Schritt 2: Erstellen einer neuen Azure Front Door-Instanz

Führen Sie die folgenden Schritte aus, um eine Front Door für Ihren Azure AD B2C-Mandanten zu erstellen. Weitere Informationen finden Sie unter [Erstellen einer Front Door für Ihre Anwendung](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application).
  

1. Melden Sie sich am [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie **Verzeichnis + Abonnement** und dann das Verzeichnis aus, das das Azure-Abonnement enthält, das für Azure Front Door verwendet werden soll. Bei diesem Verzeichnis sollte es sich *nicht* um das Verzeichnis handeln, das Ihren Azure AD B2C-Mandanten enthält.
1. Wählen Sie auf der Startseite oder im Azure-Menü die Option **Ressource erstellen** aus. Wählen Sie **Netzwerk** > **Alle anzeigen** > **Front Door** aus.
1. Geben Sie auf der Registerkarte **Grundlagen** der Seite **Frontdoor-Instanz erstellen** die folgenden Informationen ein, oder wählen Sie sie aus. Wählen Sie anschließend **Weiter: Konfiguration** aus.

    | Einstellung | Wert |
    | --- | --- |
    | **Abonnement** | Wählen Sie Ihr Azure-Abonnement. |    
    | **Ressourcengruppe** | Wählen Sie eine vorhandene Ressourcengruppe oder **Neu erstellen** aus, um eine neue Ressourcengruppe zu erstellen.|
    | **Ressourcengruppenstandort** | Wählen Sie den Standort der Ressourcengruppe aus. Beispiel: **USA, Mitte**. |

### <a name="21-add-frontend-host"></a>2.1 Hinzufügen des Front-End-Hosts

Der Front-End-Host ist der Domänenname, der von Ihrer Anwendung verwendet wird. Wenn Sie eine Front Door erstellen, ist der Standard-Front-End-Host eine Unterdomäne von `azurefd.net`.

Azure Front Door bietet Ihnen die Möglichkeit, dem Front-End-Host eine benutzerdefinierte Domäne zuzuordnen. Mit dieser Option ordnen Sie die Azure AD B2C-Benutzeroberfläche einer benutzerdefinierten Domäne in Ihrer URL anstelle eines Front Door-Domänennamens zu. Beispiel: `https://login.contoso.com`.

Führen Sie die folgenden Schritte aus, um einen Front-End-Host hinzuzufügen:

1. Wählen Sie unter **Front-Ends/Domänen** das Symbol **+** aus, um **Front-End-Host hinzufügen** auszuwählen.
1. Geben Sie unter **Hostname** einen global eindeutigen Hostnamen ein. Der Hostname ist nicht Ihre benutzerdefinierte Domäne. In diesem Beispiel wird *contoso-frontend* verwendet. Wählen Sie **Hinzufügen**.

    ![Screenshot zum Hinzufügen eines Front-End-Hosts.](./media/custom-domain/add-frontend-host-azure-front-door.png)

### <a name="22-add-backend-and-backend-pool"></a>2.2 Hinzufügen von Back-End und Back-End-Pool

Ein Back-End bezieht sich auf Ihren [Azure AD B2C-Mandantennamen](tenant-management.md#get-your-tenant-name) `tenant-name.b2clogin.com`. Um einen Back-End-Pool hinzuzufügen, führen Sie diese Schritte aus:

1. Wählen Sie unter **Frontdoor-Instanz erstellen** im Bereich **Back-End-Pools** das Symbol **+** aus, um **Back-End-Pool hinzufügen** zu öffnen.

1. Geben Sie einen **Namen** ein. Beispielsweise *myBackendPool*. Wählen Sie **Back-End hinzufügen** aus.
    
    Der folgende Screenshot zeigt, wie Sie einen Back-End-Pool erstellen:
    
    ![Screenshot zum Hinzufügen eines Back-End-Pools.](./media/custom-domain/front-door-add-backend-pool.png)

1. Wählen Sie auf dem Blatt **Back-End hinzufügen** die folgenden Informationen und dann **Hinzufügen** aus.

    | Einstellung | Wert |
    | --- | --- |
    | **Back-End-Hosttyp**| Wählen Sie **Benutzerdefinierter Host** aus.| 
    | **Back-End-Hostname**| Wählen Sie den Namen für Ihren [Azure AD B2C](tenant-management.md#get-your-tenant-name)-`<tenant-name>.b2clogin.com` aus. Beispiel: contoso.b2clogin.com|
    | **Back-End-Hostheader**| Wählen Sie den gleichen Wert aus, den Sie für **Zielhostname** ausgewählt haben.|
    
    **Übernehmen Sie für alle anderen Felder die Standardeinstellungen.*
    
    Der folgende Screenshot zeigt, wie Sie ein benutzerdefiniertes Host-Back-End erstellen, das einem Azure AD B2C-Mandanten zugeordnet ist:
    
    ![Screenshot zum Hinzufügen eines benutzerdefinierten Host-Back-Ends.](./media/custom-domain/add-a-backend.png)

1. Schließen Sie die Konfiguration des Back-End-Pools ab, indem Sie auf dem Blatt **Back-End-Pool hinzufügen** die Option **Hinzufügen** auswählen.

1. Nachdem Sie das **Back-End** dem **Back-End-Pool** hinzugefügt haben, deaktivieren Sie die **Integritätstests**.

    ![Screenshot zum Hinzufügen eines Back-End-Pools und Deaktivieren der Integritätstest.](./media/custom-domain/add-a-backend-pool.png)

### <a name="23-add-a-routing-rule"></a>2.3 Hinzufügen einer Routingregel

Fügen Sie zum Schluss eine Routingregel hinzu. Die Routingregel ordnet Ihren Front-End-Host dem Back-End-Pool zu. Die Regel leitet eine Anforderung für den [Front-End-Host](#21-add-frontend-host) an das Azure AD B2C-[Back-End](#22-add-backend-and-backend-pool) weiter. Führen Sie die folgenden Schritte aus, um eine Routingregel hinzuzufügen:

1. Geben Sie unter **Regel hinzufügen** für **Name** den Namen *LocationRule* ein. Übernehmen Sie alle Standardwerte, und wählen Sie anschließend **Hinzufügen** aus, um die Routingregel hinzuzufügen.
1. Wählen Sie **Überprüfen + erstellen** und danach **Erstellen** aus.
    
     ![Screenshot zum Erstellen von Azure Front Door.](./media/custom-domain/configuration-azure-front-door.png)


## <a name="step-3-set-up-your-custom-domain-on-azure-front-door"></a>Schritt 3: Einrichten der benutzerdefinierten Domäne in Azure Front Door

In diesem Schritt fügen Sie die benutzerdefinierte Domäne hinzu, die Sie in [Schritt 1](#step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant) für Ihre Front Door registriert haben. 

### <a name="31-create-a-cname-dns-record"></a>3.1 Erstellen eines CNAME-DNS-Eintrags

Bevor Sie eine benutzerdefinierte Domäne mit Ihrer Azure Front Door Service-Konfiguration verwenden können, müssen Sie zunächst einen kanonischen Namenseintrag (CNAME) bei Ihrem Domänenanbieter erstellen, der auf den Front-End-Standardhost von Azure Front Door Service verweist (z. B. „contoso.azurefd.net“).

Ein CNAME-Eintrag ist eine Art von DNS-Eintrag, mit dem ein Quelldomänenname einem Zieldomänennamen zugeordnet wird (Alias). Für Azure Front Door ist der Quelldomänenname Ihr benutzerdefinierter Domänenname und der Zieldomänenname der Name Ihres Azure Front Door-Standardhosts, den Sie in [Schritt 2.1](#21-add-frontend-host) konfiguriert haben. 

Nachdem der von Ihnen erstellte CNAME-Eintrag von Azure Front Door Service überprüft wurde, wird der für die benutzerdefinierte Quelldomäne (z. B. login.contoso.com) bestimmte Datenverkehr an den angegebenen Ziel-Front-End-Standardhost von Azure Front Door Service geleitet (z. B. `contoso.azurefd.net`). Weitere Informationen finden Sie unter [Hinzufügen einer benutzerdefinierten Domäne zu Front Door](../frontdoor/front-door-custom-domain.md). 

Erstellen Sie wie folgt einen CNAME-Eintrag für Ihre benutzerdefinierten Domäne:

1. Melden Sie sich an der Website des Domänenanbieters für Ihre benutzerdefinierte Domäne an.

1. Suchen Sie die Seite für die Verwaltung von DNS-Einträgen mithilfe der Dokumentation des Anbieters, oder suchen Sie nach Bereichen der Website mit der Bezeichnung **Domänennamen**, **DNS**, oder **Namensserververwaltung**. 

1. Erstellen Sie einen CNAME-Eintrag für Ihre benutzerdefinierte Domäne, und füllen Sie die Felder wie in der folgenden Tabelle gezeigt aus (Feldnamen können variieren):

    | `Source`          | type  | Destination           |
    |-----------------|-------|-----------------------|
    | `<login.contoso.com>` | CNAME | `contoso.azurefd.net` |

   - Quelle: Geben Sie den Namen Ihrer benutzerdefinierten Domäne ein (z. B. login.contoso.com).

   - Typ: Geben Sie *CNAME* ein.

   - Ziel: Geben Sie den in [Schritt 2.1](#21-add-frontend-host) erstellten Front Door-Front-End-Standardhost ein. Der Host muss im folgenden Format angegeben werden: _&lt;hostname&gt;_ .azurefd.net. Beispiel: `contoso.azurefd.net`.

1. Speichern Sie die Änderungen.

### <a name="32-associate-the-custom-domain-with-your-front-door"></a>3.2 Zuordnen der benutzerdefinierten Domäne zu Ihrer Front Door

Nachdem Sie Ihre benutzerdefinierte Domäne registriert haben, können Sie sie Ihrer Azure Front Door Service-Konfiguration hinzufügen.
    
1. Wählen Sie auf der Seite **Frontdoor-Designer** unter **Front-Ends/Domänen** das **+** aus, um eine benutzerdefinierte Domäne hinzuzufügen.

    ![Screenshot zum Hinzufügen einer benutzerdefinierten Domäne.](./media/custom-domain/azure-front-door-add-custom-domain.png) 
    
1. Für **Front-End-Host** wird der Front-End-Host, der als Zieldomäne Ihres CNAME-Eintrags verwendet werden soll, aus Ihrer Azure Front Door Service-Konfiguration abgeleitet und vorab ausgefüllt: *&lt;Name des Standardhosts&gt;* .azurefd.NET. Diese Einstellung kann nicht geändert werden.

1. Geben Sie unter **Benutzerdefinierter Hostname** Ihre benutzerdefinierte Domäne (einschließlich Unterdomäne) ein, die als Quelldomäne Ihres CNAME-Eintrags verwendet werden soll. Beispielsweise login.contoso.com.

    ![Screenshot zum Überprüfen einer benutzerdefinierten Domäne.](./media/custom-domain/azure-front-door-add-custom-domain-verification.png)

    Azure überprüft, ob der CNAME-Eintrag für den eingegebenen Namen der benutzerdefinierten Domäne vorhanden ist. Wenn der CNAME-Eintrag korrekt ist, wird Ihre benutzerdefinierte Domäne überprüft.

    
1. Nachdem der benutzerdefinierte Domänenname überprüft wurde, wählen Sie unter **HTTPS für benutzerdefinierte Domänen** die Option **Aktiviert** aus. 
    
    ![Screenshot zum Aktivieren von HTTPS über ein Azure Front Door-Zertifikat.](./media/custom-domain/azure-front-door-add-custom-domain-https-settings.png)

1. Wählen Sie unter **Zertifikatverwaltungstyp** die Option [Mit Front Door verwaltet](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door) oder [Eigenes Zertifikat verwenden](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate) aus. Wenn Sie die Option *Mit Frontdoor verwaltet* auswählen, warten Sie, bis das Zertifikat vollständig bereitgestellt wurde.

1. Wählen Sie **Hinzufügen**.

### <a name="33-update-the-routing-rule"></a>3.3 Aktualisieren der Routingregel

1. Wählen Sie unter **Routingregeln** die in [Schritt 2.3](#23-add-a-routing-rule) erstellte Routingregel aus.

    ![Screenshot zum Auswählen einer Routingregel.](./media/custom-domain/select-routing-rule.png)

1. Wählen Sie unter **Front-End/Domänen** Ihren benutzerdefinierten Domänennamen aus.
    
    ![Screenshot zum Aktualisieren der Azure Front Door-Routingregel.](./media/custom-domain/update-routing-rule.png)

1. Wählen Sie **Aktualisieren** aus.
1. Wählen Sie im Hauptfenster **Speichern** aus.

## <a name="step-4-configure-cors"></a>Schritt 4. Konfigurieren von CORS

Wenn Sie [die Azure AD B2C-Benutzeroberfläche](customize-ui-with-html.md) mit einer HTML-Vorlage anpassen, müssen Sie [CORS](customize-ui-with-html.md?pivots=b2c-user-flow.md#3-configure-cors) mit Ihrer benutzerdefinierten Domäne konfigurieren.

Führen Sie die folgenden Schritte aus, um Azure Blob Storage für die Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS) zu konfigurieren:

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrem Speicherkonto.
1. Wählen Sie im Menü **CORS** aus.
1. Geben Sie für **Zulässige Ursprünge** den Wert `https://your-domain-name` ein. Ersetzen Sie `your-domain-name` durch Ihren Domänennamen. Beispiel: `https://login.contoso.com`. Verwenden Sie bei der Eingabe Ihres Mandantennamens ausschließlich Kleinbuchstaben.
1. Wählen Sie für **Zulässige Methoden** sowohl `GET` als auch `OPTIONS` aus.
1. Geben Sie für **Zulässige Header** ein Sternchen (*) ein.
1. Geben Sie für **Verfügbar gemachte Header** ein Sternchen (*) ein.
1. Für **Max. Alter** geben Sie 200 ein.
1. Wählen Sie **Speichern** aus.

## <a name="test-your-custom-domain"></a>Testen Ihrer benutzerdefinierten Domäne

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wählen Sie im oberen Menü den Filter **Verzeichnis und Abonnement** aus, und wählen Sie dann das Verzeichnis aus, das Ihren Azure AD B2C-Mandanten enthält.
1. Suchen Sie im Azure-Portal nach **Azure AD B2C**, und wählen Sie diese Option dann aus.
1. Wählen Sie unter **Richtlinien** die Option **Benutzerflows (Richtlinien)** aus.
1. Wählen Sie einen Benutzerflow und dann **Benutzerflow ausführen** aus.
1. Wählen Sie für **Anwendung** die Webanwendung *webapp1* aus, die Sie zuvor registriert haben. Als **Antwort-URL** sollte `https://jwt.ms` angezeigt werden.
1. Kopieren Sie die URL unter **Benutzerflow-Endpunkt ausführen**.

    ![Screenshot zum Kopieren des Autorisierungsanforderungs-URI.](./media/custom-domain/user-flow-run-now.png)

1. Um eine Anmeldung mit Ihrer benutzerdefinierten Domäne zu simulieren, öffnen Sie einen Webbrowser, und verwenden Sie die kopierte URL. Ersetzen Sie die Azure AD B2C-Domäne ( _&lt;Mandantenname&gt;_ .b2clogin.com) durch Ihre benutzerdefinierte Domäne.   

    Verwenden Sie anstelle von

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    sondern:

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```

1. Vergewissern Sie sich, dass Azure AD B2C ordnungsgemäß geladen wurde. Melden Sie sich dann mit einem lokalen Konto an.
1. Wiederholen Sie den Test mit den restlichen Richtlinien.

## <a name="configure-your-identity-provider"></a>Konfigurieren des Identitätsanbieters

Wenn sich ein Benutzer mit einem sozialen Netzwerk als Identitätsanbieter anmelden möchte, wird in Azure AD B2C eine Autorisierungsanforderung initiiert und der Benutzer zum Durchführen des Anmeldevorgangs an den ausgewählten Identitätsanbieter weitergeleitet. In der Autorisierungsanforderung wird `redirect_uri` mit dem Azure AD B2C-Standarddomänennamen angegeben: 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>/oauth2/authresp
```

Wenn Sie die Richtlinie so konfiguriert haben, dass die Anmeldung mit einem externen Identitätsanbieter zulässig ist, aktualisieren Sie die OAuth-Umleitungs-URIs mit der benutzerdefinierten Domäne. Bei den meisten Identitätsanbietern können Sie mehrere Umleitungs-URIs registrieren. Es wird empfohlen, Umleitungs-URIs hinzuzufügen, anstatt sie zu ersetzen, damit Sie die benutzerdefinierte Richtlinie testen können, ohne dass sich dies auf Anwendungen auswirkt, in denen der Azure AD B2C-Standarddomänenname verwendet wird. 

Gehen Sie beim folgenden Umleitungs-URI wie folgt vor:

```http
https://<custom-domain-name>/<tenant-name>/oauth2/authresp
``` 

- Ersetzen Sie **&lt;custom-domain-name&gt;** durch Ihren benutzerdefinierten Domänennamen.
- Ersetzen Sie **&lt;tenant-name&gt;** durch den Namen Ihres Mandanten oder Ihre Mandanten-ID.

Das folgende Beispiel zeigt einen gültigen OAuth-Umleitungs-URI:

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

Wenn Sie die [Mandanten-ID](#optional-use-tenant-id) verwenden, sieht ein gültiger OAuth-Umleitungs-URI wie im folgenden Beispiel aus:

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

Die Metadaten der [SAML-Identitätsanbieter](saml-identity-provider-technical-profile.md) sehen wie im folgenden Beispiel aus:

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="configure-your-application"></a>Konfigurieren der Anwendung 

Nach dem Konfigurieren und Testen der benutzerdefinierten Domäne können Sie Ihre Anwendungen aktualisieren, um die URL zu laden, die anstelle der Azure AD B2C-Domäne die benutzerdefinierte Domäne als Hostname angibt. 

Die Integration der benutzerdefinierten Domäne gilt für Authentifizierungsendpunkte, bei denen zur Authentifizierung von Benutzern Azure AD B2C-Richtlinien (Benutzerflows oder benutzerdefinierte Richtlinien) verwendet werden. Diese Endpunkte können wie im folgenden Beispiel aussehen:

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

Ersetzen Sie:
- **custom-domain** durch die benutzerdefinierte Domäne
- **tenant-name** durch den Namen oder die ID Ihres Mandanten
- **policy-name** durch den Namen Ihrer Richtlinie. [Erfahren Sie mehr über Azure AD B2C-Richtlinien](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 


Die Metadaten des [SAML-Dienstanbieters](./saml-service-provider.md) können wie im folgenden Beispiel aussehen: 

```html
https://custom-domain-name/tenant-name/policy-name/Samlp/metadata
```

### <a name="optional-use-tenant-id"></a>(Optional:) Verwenden der Mandanten-ID

Sie können den Namen des B2C-Mandanten in der URL durch die GUID der Mandanten-ID ersetzen, um alle Verweise auf „b2c“ in der URL zu entfernen. Die GUID der Mandanten ID finden Sie auf der B2C-Übersichtsseite im Azure-Portal.
Ändern Sie zum Beispiel `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/` in `https://account.contosobank.co.uk/<tenant ID GUID>/`.

Wenn Sie die Mandanten-ID anstelle des Mandantennamens verwenden, müssen Sie die **OAuth-Umleitungs-URIs** des Identitätsanbieters entsprechend aktualisieren. Weitere Informationen finden Sie unter [Konfigurieren des Identitätsanbieters](#configure-your-identity-provider).

### <a name="token-issuance"></a>Tokenausstellung

Der Anspruch für den Namen des Tokenausstellers (iss) wird basierend auf der verwendeten benutzerdefinierten Domäne geändert. Beispiel:

```http
https://<domain-name>/11111111-1111-1111-1111-111111111111/v2.0/
```

::: zone pivot="b2c-custom-policy"

## <a name="block-access-to-the-default-domain-name"></a>Blockieren des Zugriffs auf den Standarddomänennamen

Nachdem Sie die benutzerdefinierte Domäne hinzugefügt und die Anwendung konfiguriert haben, können Benutzer weiterhin auf die Domäne &lt;Mandantenname&gt;.b2clogin.com zugreifen. Um den Zugriff zu verhindern, können Sie die Richtlinie so konfigurieren, dass der Hostname in der Autorisierungsanforderung anhand einer zulässigen Liste von Domänen überprüft wird. Der Hostname ist der Domänenname, der in der URL angezeigt wird. Der Hostname ist über `{Context:HostName}`-[Anspruchskonfliktlöser](claim-resolver-overview.md) verfügbar. Anschließend kann eine benutzerdefinierte Fehlermeldung angezeigt werden. 

1. Rufen Sie über [GitHub](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name) das Beispiel für eine Richtlinie für bedingten Zugriff ab, mit der der Hostname überprüft wird.
1. Ersetzen Sie in jeder Datei die Zeichenfolge `yourtenant` durch den Namen Ihres Azure AD B2C-Mandanten. Wenn der Name des B2C-Mandanten z.B. *contosob2c* lautet, werden alle Instanzen von `yourtenant.onmicrosoft.com` zu `contosob2c.onmicrosoft.com`.
1. Laden Sie die Richtliniendateien in der folgenden Reihenfolge hoch: `B2C_1A_TrustFrameworkExtensions_HostName.xml` und dann `B2C_1A_signup_signin_HostName.xml`.

::: zone-end

## <a name="troubleshooting"></a>Problembehandlung

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>In Azure AD B2C wird die Fehlermeldung „Seite nicht gefunden“ zurückgegeben

- **Symptom:** Wenn Sie sich nach dem Konfigurieren einer benutzerdefinierten Domäne bei der Domäne anmelden möchten, wird eine HTTP 404-Fehlermeldung ausgegeben.
- **Mögliche Ursachen:** Dieses Problem kann mit der DNS-Konfiguration oder der Konfiguration des Azure Front Door-Back-Ends zusammenhängen. 
- **Lösung:**  
    1. Stellen Sie sicher, dass die benutzerdefinierte Domäne in Ihrem Azure AD B2C-Mandanten [registriert und erfolgreich überprüft](#step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant) wurde.
    1. Vergewissern Sie sich, dass die [benutzerdefinierte Domäne](../frontdoor/front-door-custom-domain.md) ordnungsgemäß konfiguriert ist. Der `CNAME`-Eintrag für die benutzerdefinierte Domäne muss auf den standardmäßigen Azure Front Door-Front-End-Host zeigen (z. B. contoso.azurefd.net).
    1. Vergewissern Sie sich, dass in der [Konfiguration des Azure Front Door-Back-End-Pools](#22-add-backend-and-backend-pool) auf den Mandanten verwiesen wird, in dem Sie den benutzerdefinierten Domänennamen eingerichtet haben und in dem die Benutzerflows bzw. benutzerdefinierten Richtlinien gespeichert werden.


### <a name="azure-ad-b2c-returns-the-resource-you-are-looking-for-has-been-removed-had-its-name-changed-or-is-temporarily-unavailable"></a>Azure AD B2C gibt zurück, dass die gesuchte Ressource entfernt oder umbenannt wurde oder dass sie vorübergehend nicht zur Verfügung steht.

- **Symptom:** Wenn Sie nach dem Konfigurieren einer benutzerdefinierten Domäne versuchen, sich mit der benutzerdefinierten Domäne anzumelden, wird die Fehlermeldung *Die gesuchte Ressource wurde entfernt oder umbenannt, oder sie steht vorübergehend nicht zur Verfügung* angezeigt.
- **Mögliche Ursachen:** Dieses Problem kann mit der Überprüfung von benutzerdefinierten Azure AD-Domäne zusammenhängen. 
- **Lösung:** Stellen Sie sicher, dass die benutzerdefinierte Domäne in Ihrem Azure AD B2C-Mandanten [registriert und **erfolgreich überprüft**](#step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant) wurde.

### <a name="identify-provider-returns-an-error"></a>Identitätsanbieter gibt einen Fehler zurück

- **Symptom:** Nach dem Konfigurieren einer benutzerdefinierten Domäne können Sie sich mit lokalen Konten anmelden. Wenn Sie sich jedoch mit den Anmeldeinformationen externer [Identitätsanbieter für soziale Netzwerke oder Unternehmen](add-identity-provider.md) anmelden, gibt der Identitätsanbieter eine Fehlermeldung aus.
- **Mögliche Ursachen:** Wenn der Benutzer in Azure AD B2C zur Anmeldung bei einem Verbundidentitätsanbieter weitergeleitet wird, wird der Umleitungs-URI angegeben. Der Umleitungs-URI ist der Endpunkt, an dem der Identitätsanbieter das Token zurückgibt. Der Umleitungs-URI ist die gleiche Domäne, die in Ihrer Anwendung für die Autorisierungsanforderung verwendet wird. Wenn der Umleitungs-URI noch nicht beim Identitätsanbieter registriert ist, wird der neue Umleitungs-URI möglicherweise nicht als vertrauenswürdig eingestuft, sodass eine Fehlermeldung ausgegeben wird. 
- **Lösung:** Führen Sie die Schritte unter [Konfigurieren des Identitätsanbieters](#configure-your-identity-provider) aus, um den neuen Umleitungs-URI hinzuzufügen. 

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="can-i-use-azure-front-door-advanced-configuration-such-as-web-application-firewall-rules"></a>Kann ich die erweiterte Konfiguration in Azure Front Door verwenden, z. B. *Web Application Firewall-Regeln*? 
  
Obwohl die erweiterten Konfigurationseinstellungen für Azure Front Door offiziell nicht unterstützt werden, können Sie sie auf eigene Gefahr verwenden. 

### <a name="when-i-use-run-now-to-try-to-run-my-policy-why-i-cant-see-the-custom-domain"></a>Warum wird die benutzerdefinierte Domäne nicht angezeigt, wenn ich die Richtlinie über „Jetzt ausführen“ ausführen möchte?

Kopieren Sie die URL, ändern Sie den Domänennamen manuell, und fügen Sie sie dann wieder im Browser ein.

### <a name="which-ip-address-is-presented-to-azure-ad-b2c-the-users-ip-address-or-the-azure-front-door-ip-address"></a>Welche IP-Adresse wird an Azure AD B2C übermittelt? Die IP-Adresse des Benutzers oder die IP-Adresse von Azure Front Door?

Mit Azure Front Door wird die ursprüngliche IP-Adresse des Benutzers übergeben. Dies ist die IP-Adresse, die in Überwachungsberichten oder in der benutzerdefinierten Richtlinie angezeigt wird.

### <a name="can-i-use-a-third-party-web-application-firewall-waf-with-b2c"></a>Kann eine Web Application Firewall (WAF) eines Drittanbieters mit B2C verwendet werden?

Zur Verwendung einer eigenen Web Application Firewall vor Azure Front Door müssen Sie die entsprechenden Vorgänge konfigurieren und überprüfen, ob sie ordnungsgemäß mit den Azure AD B2C-Benutzerflows oder benutzerdefinierten Richtlinien durchgeführt werden.  

## <a name="next-steps"></a>Nächste Schritte

Informationen zu [OAuth-Autorisierungsanforderungen](protocols-overview.md)
