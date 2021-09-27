---
title: Entwicklerportal - Häufig gestellte Fragen
titleSuffix: Azure API Management
description: Häufig gestellte Fragen zum Entwicklerportal in API Management. Das Entwicklerportal ist eine anpassbare Website, auf der API-Consumer Ihre APIs untersuchen können.
services: api-management
documentationcenter: API Management
author: dlepow
ms.service: api-management
ms.topic: troubleshooting
ms.date: 07/30/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0c8757211bf11e97cde091d49eb6512bb0e4f7cc
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128656339"
---
# <a name="api-management-developer-portal---frequently-asked-questions"></a>API Management-Entwicklerportal - Häufig gestellte Fragen

## <a name="what-if-i-need-functionality-that-isnt-supported-in-the-portal"></a>Was passiert, wenn ich über Funktionen verfügen muss, die im Portal nicht unterstützt werden?

Sie können im [GitHub-Repository](https://github.com/Azure/api-management-developer-portal) eine Funktionsanforderung öffnen oder die [fehlende Funktionalität selbst implementieren](developer-portal-implement-widgets.md). Weitere Informationen zur [Erweiterbarkeit](api-management-howto-developer-portal.md#managed-vs-self-hosted) des Entwicklerportals.


## <a name="can-i-have-multiple-developer-portals-in-one-api-management-service"></a>Kann ich über mehrere Entwicklerportale in einem API Management-Dienst verfügen?

Sie können über ein verwaltetes Portal und mehrere selbstgehostete Portale verfügen. Der Inhalt aller Portale wird in demselben API Management-Dienst gespeichert, sodass die Portale identisch sind. Wenn das Aussehen und die Funktionalität der einzelnen Portale unterschiedlich sein soll, können Sie diese eigenständig mit Ihren benutzerdefinierten Widgets hosten, die Seiten zur Runtime dynamisch anpassen, beispielsweise basierend auf der URL.

## <a name="does-the-portal-support-azure-resource-manager-templates-andor-is-it-compatible-with-api-management-devops-resource-kit"></a>Unterstützt das Portal Azure Resource Manager-Vorlagen und/oder ist es mit dem API Management DevOps Resource Kit kompatibel?

Nein.

## <a name="is-the-portals-content-saved-with-the-backuprestore-functionality-in-api-management"></a>Wird der Inhalt des Portals mit der Sicherungs-/Wiederherstellungsfunktion in API Management gespeichert?

Nein.

## <a name="do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies"></a>Muss ich zusätzliche VNET-Konnektivität für die Abhängigkeiten des verwalteten Portals aktivieren?

In den meisten Fällen nicht.

Wenn sich Ihr API Management-Dienst in einem internen VNET befindet, kann nur innerhalb des Netzwerks auf Ihr Entwicklerportal zugegriffen werden. Der Hostname des Verwaltungsendpunkts muss über den Computer, mit dem Sie auf die Verwaltungsschnittstelle des Portals zugreifen, zur internen VIP des Diensts aufgelöst werden. Vergewissern Sie sich, dass der Verwaltungsendpunkt im DNS registriert ist. Ist die Konfiguration fehlerhaft, wird ein Fehler angezeigt: `Unable to start the portal. See if settings are specified correctly in the configuration (...)`.

Wenn sich Ihr API Management-Dienst in einem internen VNet befindet und Sie über Application Gateway über das Internet darauf zugreifen, stellen Sie sicher, dass Sie die Konnektivität mit dem Entwicklerportal und den Verwaltungsendpunkten von API Management aktivieren. Möglicherweise müssen Sie die Firewallregeln für Webanwendungen deaktivieren. Weitere Informationen finden Sie in [diesem Dokumentationsartikel](api-management-howto-integrate-internal-vnet-appgateway.md).

## <a name="i-assigned-a-custom-api-management-domain-and-the-published-portal-doesnt-work"></a>Ich habe eine benutzerdefinierte API Management-Domäne zugewiesen, und das veröffentlichte Portal funktioniert nicht

Nachdem Sie die Domäne aktualisiert haben, müssen Sie [das Portal erneut veröffentlichen](api-management-howto-developer-portal-customize.md#publish), damit die Änderungen wirksam werden.

## <a name="i-added-an-identity-provider-and-i-cant-see-it-in-the-portal"></a>Ich habe einen Identitätsanbieter hinzugefügt, er wird im Portal aber nicht angezeigt

Nachdem Sie einen Identitätsanbieter (beispielsweise Azure AD oder Azure AD B2C) konfiguriert haben, müssen Sie das [Portal erneut veröffentlichen](api-management-howto-developer-portal-customize.md#publish), damit die Änderungen wirksam werden. Stellen Sie sicher, dass die Seiten des Entwicklerportals das OAuth-Schaltflächen-Widget enthalten.

## <a name="i-set-up-delegation-and-the-portal-doesnt-use-it"></a>Ich habe die Delegierung eingerichtet, sie wird vom Portal aber nicht verwendet

Nachdem Sie die Delegierung eingerichtet haben, müssen Sie [das Portal erneut veröffentlichen](api-management-howto-developer-portal-customize.md#publish), damit die Änderungen wirksam werden.

## <a name="my-other-api-management-configuration-changes-havent-been-propagated-in-the-developer-portal"></a>Meine anderen API Management-Konfigurationsänderungen wurden nicht an das Entwicklerportal weitergegeben.

Bei den meisten Konfigurationsänderungen (beispielsweise für das VNET, für die Anmeldung und für Produktbedingungen) ist eine [erneute Veröffentlichung des Portals](api-management-howto-developer-portal-customize.md#publish) erforderlich.

## <a name="im-getting-a-cors-error-when-using-the-interactive-console"></a><a name="cors"></a> Beim Verwenden der interaktiven Konsole erhalte ich einen CORS-Fehler.

Die interaktive Konsole sendet eine clientseitige API-Anforderung über den Browser. Beheben Sie das CORS-Problem, indem Sie [eine CORS-Richtlinie](api-management-cross-domain-policies.md#CORS) für Ihre API(s) hinzufügen.

Sie können den Status der CORS-Richtlinie im Abschnitt **Portalübersicht** Ihres API Management-Diensts im Azure-Portal überprüfen. Ein Warnfeld zeigt eine fehlende oder falsch konfigurierte Richtlinie an.

> [!NOTE]
> 
> Es wird nur eine CORS-Richtlinie ausgeführt. Wenn Sie mehrere CORS-Richtlinien angegeben haben (z. B. auf API-Ebene und auf der Ebene alle APIs), funktioniert Ihre interaktive Konsole möglicherweise nicht wie erwartet.

![Screenshot, der zeigt, wo Sie den Status Ihrer CORS-Richtlinie überprüfen können.](media/developer-portal-faq/cors-azure-portal.png)

Wenden Sie die COLRS-Richtlinie automatisch an, indem Sie auf die Schaltfläche **CORS aktivieren** klicken.

Sie können CORS auch manuell aktivieren.

1. Wählen Sie den Link **Manuelle Anwendung auf globaler Ebene** aus, um den generierten Richtliniencode anzuzeigen.
2. Navigieren Sie im Azure-Portal im Abschnitt **APIs** Ihres API Management-Diensts zu **Alle APIs**.
3. Wählen Sie im Abschnitt **Eingehende Verarbeitung** das Symbol **</>** aus.
4. Fügen Sie die Richtlinie in den Abschnitt **\<inbound\>** der XML-Datei ein. Stellen Sie sicher, dass der **\<origin\>** -Wert mit der Domäne Ihres Entwicklerportals übereinstimmt.

> [!NOTE]
> 
> Wenn Sie die CORS-Richtlinie nicht im API-Bereich, sondern im Produktbereich anwenden und Ihre API die Abonnementschlüsselauthentifizierung über einen Header verwendet, funktioniert Ihre Konsole nicht.
>
> Der Browser gibt automatisch eine HTTP-Anforderung vom Typ `OPTIONS` aus, die keinen Header mit dem Abonnementschlüssel enthält. Aufgrund des fehlenden Abonnementschlüssels kann API Management den `OPTIONS`-Aufruf keinem Produkt zuordnen und somit die CORS-Richtlinie nicht anwenden.
>
> Zur Umgehung dieses Problems können Sie den Abonnementschlüssel in einem Abfrageparameter übergeben.

## <a name="what-is-the-cors-proxy-feature-and-when-should-i-use-it"></a>Was ist das CORS-Proxyfeature und wann sollte ich es verwenden?

Wählen Sie in der Konfiguration des API-Vorgangsdetailswidgets die Option **CORS-Proxy verwenden** aus, um die API-Aufrufe der interaktiven Konsole über das Back-End des Portals in Ihrem API Management weiter zu routen. In dieser Konfiguration müssen Sie keine CORS-Richtlinie mehr für Ihre APIs anwenden, und es ist keine Verbindung mit dem Gateway-Endpunkt vom lokalen Computer erforderlich. Wenn die APIs über ein selbstgehostetes Gateway verfügbar gemacht werden oder sich Ihr Dienst in einem virtuellen Netzwerk befindet, ist die Konnektivität zwischen dem Back-End-Dienst der API Management und dem Gateway erforderlich. Wenn Sie das selbstgehostete Portal verwenden, geben Sie den Back-End-Endpunkt des Portals mithilfe der `backendUrl`-Option in den Konfigurationsdateien an. Andernfalls kennt das selbstgehostete Portal den Speicherort des Back-End-Diensts nicht.

## <a name="what-permissions-do-i-need-to-edit-the-developer-portal"></a>Welche Berechtigungen sind zum Bearbeiten des Entwicklerportals erforderlich?

Sollte beim Öffnen des Portals im Verwaltungsmodus der Fehler `Oops. Something went wrong. Please try again later.` angezeigt werden, verfügen Sie möglicherweise nicht über die erforderlichen Berechtigungen (Azure RBAC).

Bei den Legacyportalen war die Berechtigung `Microsoft.ApiManagement/service/getssotoken/action` im Dienstbereich (`/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>`) erforderlich, um dem Benutzer Administratorzugriff auf die Portale zu gewähren. Für das neue Portal ist die Berechtigung `Microsoft.ApiManagement/service/users/token/action` im Bereich `/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1` erforderlich.

Sie können das folgende PowerShell-Skript verwenden, um eine Rolle mit der erforderlichen Berechtigung zu erstellen. Vergessen Sie nicht, den Parameter `<subscription-id>` zu ändern. 

```powershell
#New Portals Admin Role 
Import-Module Az 
Connect-AzAccount 
$contributorRole = Get-AzRoleDefinition "API Management Service Contributor" 
$customRole = $contributorRole 
$customRole.Id = $null
$customRole.Name = "APIM New Portal Admin" 
$customRole.Description = "This role gives the user ability to log in to the new Developer portal as administrator" 
$customRole.Actions = "Microsoft.ApiManagement/service/users/token/action" 
$customRole.IsCustom = $true 
$customRole.AssignableScopes.Clear() 
$customRole.AssignableScopes.Add('/subscriptions/<subscription-id>') 
New-AzRoleDefinition -Role $customRole 
```
 
Nachdem die Rolle erstellt wurde, kann sie im Azure-Portal im Abschnitt **Zugriffssteuerung (IAM)** einem beliebigen Benutzer zugewiesen werden. Wenn Sie diese Rolle einem Benutzer zuweisen, wird die Berechtigung im Dienstbereich zugewiesen. Der Benutzer kann SAS-Token im Namen *jedes beliebigen* Benutzers im Dienst generieren. Diese Rolle muss mindestens dem Administrator des Diensts zugewiesen werden. Der folgende PowerShell-Befehl zeigt, wie Sie die Rolle dem Benutzer `user1` im kleinstmöglichen Bereich zuweisen, um dem Benutzer keine unnötigen Berechtigungen zu erteilen: 

```powershell
New-AzRoleAssignment -SignInName "user1@contoso.com" -RoleDefinitionName "APIM New Portal Admin" -Scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.ApiManagement/service/<apim-service-name>/users/1" 
```

Nach Erteilung der Berechtigungen muss sich der betreffende Benutzer abmelden und erneut beim Azure-Portal anmelden, damit die neuen Berechtigungen wirksam werden.

## <a name="im-seeing-the-unable-to-start-the-portal-see-if-settings-are-specified-correctly--error"></a>Der Fehler `Unable to start the portal. See if settings are specified correctly (...)` wird angezeigt.

Dieser Fehler wird angezeigt, wenn ein Aufruf vom Typ `GET` für `https://<management-endpoint-hostname>/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.ApiManagement/service/xxx/contentTypes/document/contentItems/configuration?api-version=2018-06-01-preview` nicht erfolgreich war. Der Aufruf wird vom Browser über die Verwaltungsschnittstelle des Portals ausgegeben.

Wenn sich Ihr API Management-Dienst in einem VNet befindet, lesen Sie die [Frage zur VNET-Konnektivität](#do-i-need-to-enable-additional-vnet-connectivity-for-the-managed-portal-dependencies).

Der Aufruffehler kann auch auf ein TLS/SSL-Zertifikat zurückzuführen sein, das einer benutzerdefinierten Domäne zugewiesen ist und dem der Browser nicht vertraut. Sie können die benutzerdefinierte Domäne des Verwaltungsendpunkts entfernen. API Management greift daraufhin auf den Standardendpunkt mit einem vertrauenswürdigen Zertifikat zurück.

## <a name="whats-the-browser-support-for-the-portal"></a>Was ist die Browserunterstützung für das Portal?

| Browser                     | Unterstützt       |
|-----------------------------|-----------------|
| Apple Safari                | Ja<sup>1</sup> |
| Google Chrome               | Ja<sup>1</sup> |
| Microsoft Edge              | Ja<sup>1</sup> |
| Microsoft Internet Explorer | Nein              |
| Mozilla Firefox             | Ja<sup>1</sup> |

 <small><sup>1</sup> In den beiden neuesten Produktionsversionen unterstützt.</small>

## <a name="local-development-of-my-self-hosted-portal-is-no-longer-working"></a>Die lokale Entwicklung meines selbstgehosteten Portals funktioniert nicht mehr

Wenn Ihre lokale Version des Entwicklerportals keine Informationen aus dem Speicherkonto oder der API Management abrufen kann, sind die SAS-Token möglicherweise abgelaufen. Sie können dies beheben, indem Sie neue Token generieren. Anweisungen finden Sie im Tutorial zum [Selbsthosten des Entwicklerportals](developer-portal-self-host.md#step-2-configure-json-files-static-website-and-cors-settings).

## <a name="how-do-i-disable-sign-up-in-the-developer-portal"></a>Gewusst wie: Deaktivieren der Anmeldung im Entwicklerportal

Wenn Sie die Registrierungsfunktionen nicht standardmäßig aktiviert im Entwicklerportal benötigen, können Sie sie mit den folgenden Schritten deaktivieren:

1. Navigieren Sie im Azure-Portal zu Ihrer API Management-Instanz.
1. Wählen Sie unter **Entwicklerportal** die Option **Identitäten** aus.
1. Löschen Sie jeden Identitätsanbieter, der in der Liste angezeigt wird. Wählen Sie jeden Anbieter aus, wählen Sie das Kontextmenü aus ( **...** ), und wählen Sie dann **Löschen** aus.
 
   :::image type="content" source="media/developer-portal-faq/delete-identity-providers.png" alt-text="Identitätsanbieter löschen":::
 
1. Navigieren Sie zur Verwaltungsoberfläche des Entwicklerportals.
1. Entfernen Sie Im Portalinhalt **Anmelde** links und Navigationselemente. Informationen zum Anpassen von Portalinhalten finden Sie unter [Tutorial: Zugreifen auf und Anpassen des Entwicklerportals](api-management-howto-developer-portal-customize.md).
 
   :::image type="content" source="media/developer-portal-faq/delete-navigation-item.png" alt-text="Navigationselement löschen":::.
 
1. Ändern Sie den Inhalt der Seite **Registrieren**, um Felder zu entfernen, die zum Eingeben von Identitätsdaten verwendet werden, falls Benutzer direkt zu ihr navigieren.
   
   Löschen Sie optional die Seite **Anmelden**. Derzeit verwenden Sie die [contentItem](/rest/api/apimanagement/2021-01-01-preview/content-item)-REST-APIs zum Auflisten und Löschen dieser Seite.
 
1. Speichern Sie Ihre Änderungen, und [veröffentlichen Sie das Portal erneut](api-management-howto-developer-portal-customize.md#publish).

## <a name="how-can-i-remove-the-developer-portal-content-provisioned-to-my-api-management-service"></a>Wie kann ich die Inhalte des Entwicklerportals entfernen, die für meinen API Management bereitgestellt werden?

Geben Sie die erforderlichen Parameter im `scripts.v3/cleanup.bat`-Skript im [GitHub-Repository](https://github.com/Azure/api-management-developer-portal) des Entwicklerportals an und führen Sie das Skript aus

```sh
cd scripts.v3
.\cleanup.bat
cd ..
```

## <a name="how-do-i-enable-single-sign-on-sso-authentication-to-self-hosted-developer-portal"></a>Wie kann ich die SSO-Authentifizierung (Single Sign-On, einmaliges Anmelden) im selbstgehosteten Entwicklerportal aktivieren?

Neben anderen Authentifizierungsmethoden unterstützt das Entwicklerportal einmaliges Anmelden (Single Sign-On, SSO). Um sich mit dieser Methode zu authentifizieren, müssen Sie einen Aufruf von `/signin-sso` mit dem Token im Abfrageparameter ausführen:

```html
https://contoso.com/signin-sso?token=[user-specific token]
```
### <a name="generate-user-tokens"></a>Generieren von Benutzertoken
Sie können *benutzerspezifische Token* (einschließlich Administratortoken) mithilfe des Vorgangs [Shared Access-Token abrufen](/rest/api/apimanagement/2020-12-01/user/get-shared-access-token) der [API Management REST-API](/rest/api/apimanagement/apimanagementrest/api-management-rest) generieren.

> [!NOTE]
> Das Token muss URL-codiert sein.


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum neuen Entwicklerportal:

- [Zugreifen auf das neue Entwicklerportal und Verwalten des Portals](api-management-howto-developer-portal-customize.md)
- [Einrichten einer selbstgehosteten Version des Portals](developer-portal-self-host.md)
- [Implementieren Ihres eigenen Widgets](developer-portal-implement-widgets.md)

Weitere Ressourcen:

- [GitHub-Repository mit dem Quellcode](https://github.com/Azure/api-management-developer-portal)

