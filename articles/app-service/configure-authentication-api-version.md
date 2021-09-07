---
title: Verwalten von AuthN/AuthZ-API-Versionen
description: Aktualisieren Sie Ihre App Service-Authentifizierungs-API auf V2, oder heften Sie sie bei Bedarf an eine bestimmte Version an.
ms.topic: article
ms.date: 03/29/2021
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: e526f4a56806fa41d8955337d7e8463afac80b21
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/30/2021
ms.locfileid: "113095167"
---
# <a name="manage-the-api-and-runtime-versions-of-app-service-authentication"></a>Verwalten der API- und Runtimeversionen der App Service-Authentifizierung

Dieser Artikel zeigt, wie Sie die API- und Runtimeversionen der integrierten [Authentifizierung und Autorisierung in App Service](overview-authentication-authorization.md) anpassen.

Es gibt zwei Versionen der Verwaltungs-API für die App Service Authentifizierung. Die V2-Version ist für die „Authentifizierung“-Erfahrung im Azure-Portal erforderlich. Eine App, die bereits die V1-API verwendet, kann ein Upgrade auf die V2-Version vornehmen, sobald ein paar Änderungen vorgenommen wurden. Insbesondere muss die Geheimniskonfiguration in slotpersistente Anwendungseinstellungen verschoben werden. Dies kann automatisch über den Abschnitt „Authentifizierung“ im Portal für Ihre App erfolgen.

## <a name="update-the-configuration-version"></a>Aktualisieren der Konfigurationsversion

> [!WARNING]
> Die Migration zu V2 deaktiviert die Verwaltung des App Service-Authentifizierungs-/Autorisierungsfeatures für Ihre Anwendung durch einige Clients wie das Azure-Portal, die Azure CLI und Azure PowerShell. Dies lässt sich nicht rückgängig machen.

Die V2-API unterstützt kein Erstellen oder Bearbeiten eines Microsoft-Kontos als gesonderten Anbieter, wie es in V1 der Fall war. Stattdessen nutzt sie die konvergierte [Microsoft Identity Platform](../active-directory/develop/v2-overview.md), um Benutzer sowohl mit Azure AD als auch mit persönlichen Microsoft-Konten anzumelden. Wenn Sie zur V2-API wechseln, wird die V1-Konfiguration von Azure Active Directory verwendet, um den Microsoft Identity Platform-Anbieter zu konfigurieren. Der Microsoft-Kontoanbieter V1 wird in den Migrationsprozess übernommen und funktioniert weiterhin wie bisher. Es wird aber empfohlen, zum neueren Microsoft Identity Platform-Modell zu wechseln. Weitere Informationen finden Sie unter [Unterstützung für Microsoft-Kontoanbieterregistrierungen](#support-for-microsoft-account-provider-registrations).

Beim automatisierten Migrationsvorgang werden Anbietergeheimnisse in Anwendungseinstellungen verschoben, und der Rest der Konfiguration wird in das neue Format konvertiert. So verwenden Sie die automatische Migration

1. Navigieren Sie im Portal zu Ihrer App, und wählen Sie die Menüoption **Authentifizierung** aus.
1. Wenn die App mit dem V1-Modell konfiguriert wurde, wird eine Schaltfläche **Aktualisieren** angezeigt.
1. Überprüfen Sie die Beschreibung in der Bestätigungsaufforderung. Wenn Sie bereit sind, die Migration durchzuführen, klicken Sie in der Eingabeaufforderung auf **Upgrade**.

### <a name="manually-managing-the-migration"></a>Manuelles Verwalten der Migration

Mithilfe der folgenden Schritte können Sie die Anwendung manuell zur V2-API migrieren, wenn Sie nicht die oben genannte automatische Version verwenden möchten.

#### <a name="moving-secrets-to-application-settings"></a>Verschieben von Geheimnissen in Anwendungseinstellungen

1. Rufen Sie Ihre vorhandene Konfiguration ab, indem Sie die V1-API verwenden:

   ```azurecli
   az webapp auth show -g <group_name> -n <site_name>
   ```

   Notieren Sie sich aus der resultierenden JSON-Nutzlast den Geheimniswert für jeden von Ihnen konfigurierten Anbieter:

   * AAD: `clientSecret`
   * Google: `googleClientSecret`
   * Facebook: `facebookAppSecret`
   * Twitter: `twitterConsumerSecret`
   * Microsoft-Konto: `microsoftAccountClientSecret`

   > [!IMPORTANT]
   > Die Geheimniswerte sind wichtige Sicherheitsanmeldeinformationen und sollten sorgfältig behandelt werden. Teilen Sie diese Werte nicht, und speichern Sie sie nicht dauerhaft auf einem lokalen Computer.

1. Erstellen Sie slotpersistente Anwendungseinstellungen für jeden Geheimniswert. Sie können den Namen jeder einzelnen Anwendungseinstellung auswählen. Ihr Wert sollte dem Wert entsprechen, den Sie im vorherigen Schritt abgerufen haben, oder [auf ein Key Vault-Geheimnis verweisen](./app-service-key-vault-references.md?toc=/azure/azure-functions/toc.json), das Sie mit diesem Wert erstellt haben.

   Um die Einstellung zu erstellen, können Sie das Azure-Portal verwenden oder für jeden Anbieter eine Variation der folgenden Aktionen ausführen:

   ```azurecli
   # For Web Apps, Google example    
   az webapp config appsettings set -g <group_name> -n <site_name> --slot-settings GOOGLE_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>

   # For Azure Functions, Twitter example
   az functionapp config appsettings set -g <group_name> -n <site_name> --slot-settings TWITTER_PROVIDER_AUTHENTICATION_SECRET=<value_from_previous_step>
   ```

   > [!NOTE]
   > Die Anwendungseinstellungen für diese Konfiguration müssen als slotpersistent gekennzeichnet werden, was bedeutet, dass Sie während eines [Slotaustauschvorgangs](./deploy-staging-slots.md) nicht zwischen Umgebungen verschoben werden. Der Grund hierfür ist, dass Ihre Authentifizierungskonfiguration selbst an die Umgebung gebunden ist. 

1. Erstellen Sie eine neue JSON-Datei namens `authsettings.json`. Nehmen Sie die Ausgabe, die Sie zuvor erhalten haben, und entfernen Sie daraus jeden Geheimniswert. Schreiben Sie die verbleibende Ausgabe in die Datei, und stellen Sie sicher, dass kein Geheimnis enthalten ist. In einigen Fällen kann die Konfiguration Arrays enthalten, die leere Zeichenfolgen enthalten. Stellen Sie sicher, dass dies auf `microsoftAccountOAuthScopes` nicht zutrifft, und wenn doch, ändern Sie diesen Wert in `null`.

1. Fügen Sie `authsettings.json` eine Eigenschaft hinzu, die auf den Namen der Anwendungseinstellung verweist, die Sie zuvor für jeden Anbieter erstellt haben:
 
   * AAD: `clientSecretSettingName`
   * Google: `googleClientSecretSettingName`
   * Facebook: `facebookAppSecretSettingName`
   * Twitter: `twitterConsumerSecretSettingName`
   * Microsoft-Konto: `microsoftAccountClientSecretSettingName`

   Nach diesem Vorgang könnte eine Beispieldatei etwa wie folgt aussehen, im vorliegenden Fall nur für AAD konfiguriert:

   ```json
   {
       "id": "/subscriptions/00d563f8-5b89-4c6a-bcec-c1b9f6d607e0/resourceGroups/myresourcegroup/providers/Microsoft.Web/sites/mywebapp/config/authsettings",
       "name": "authsettings",
       "type": "Microsoft.Web/sites/config",
       "location": "Central US",
       "properties": {
           "enabled": true,
           "runtimeVersion": "~1",
           "unauthenticatedClientAction": "AllowAnonymous",
           "tokenStoreEnabled": true,
           "allowedExternalRedirectUrls": null,
           "defaultProvider": "AzureActiveDirectory",
           "clientId": "3197c8ed-2470-480a-8fae-58c25558ac9b",
           "clientSecret": "",
           "clientSecretSettingName": "MICROSOFT_IDENTITY_AUTHENTICATION_SECRET",
           "clientSecretCertificateThumbprint": null,
           "issuer": "https://sts.windows.net/0b2ef922-672a-4707-9643-9a5726eec524/",
           "allowedAudiences": [
               "https://mywebapp.azurewebsites.net"
           ],
           "additionalLoginParams": null,
           "isAadAutoProvisioned": true,
           "aadClaimsAuthorization": null,
           "googleClientId": null,
           "googleClientSecret": null,
           "googleClientSecretSettingName": null,
           "googleOAuthScopes": null,
           "facebookAppId": null,
           "facebookAppSecret": null,
           "facebookAppSecretSettingName": null,
           "facebookOAuthScopes": null,
           "gitHubClientId": null,
           "gitHubClientSecret": null,
           "gitHubClientSecretSettingName": null,
           "gitHubOAuthScopes": null,
           "twitterConsumerKey": null,
           "twitterConsumerSecret": null,
           "twitterConsumerSecretSettingName": null,
           "microsoftAccountClientId": null,
           "microsoftAccountClientSecret": null,
           "microsoftAccountClientSecretSettingName": null,
           "microsoftAccountOAuthScopes": null,
           "isAuthFromFile": "false"
       }   
   }
   ```

1. Übermitteln Sie diese Datei als neue Authentifizierungs-/Autorisierungskonfiguration für Ihre App:

   ```azurecli
   az rest --method PUT --url "/subscriptions/<subscription_id>/resourceGroups/<group_name>/providers/Microsoft.Web/sites/<site_name>/config/authsettings?api-version=2020-06-01" --body @./authsettings.json
   ```

1. Überprüfen Sie, ob Ihre App nach dieser Geste weiterhin erwartungsgemäß funktioniert.

1. Löschen Sie die in den vorherigen Schritten verwendete Datei.

Sie haben die App nun migriert, um Geheimnisse von Identitätsanbietern als Anwendungseinstellungen zu speichern.

#### <a name="support-for-microsoft-account-provider-registrations"></a>Unterstützung für Microsoft-Kontoanbieterregistrierungen

Wenn Ihre vorhandene Konfiguration einen Microsoft-Kontoanbieter, aber keinen Azure Active Directory-Anbieter enthält, können Sie die Konfiguration auf den Azure Active Directory-Anbieter umstellen und dann die Migration durchführen. Gehen Sie dazu folgendermaßen vor:

1. Wechseln Sie zu [**App-Registrierungen**](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) im Azure-Portal, und suchen Sie die Registrierung, die Ihrem Microsoft-Kontoanbieter zugeordnet ist. Möglicherweise befindet sie sich unter der Überschrift „Anwendungen aus persönlichem Konto“.
1. Navigieren Sie zur Seite „Authentifizierung“ für die Registrierung. Unter „Umleitungs-URIs“ sollte ein Eintrag angezeigt werden, der auf `/.auth/login/microsoftaccount/callback`endet. Kopieren Sie diesen URI.
1. Fügen Sie einen neuen URI hinzu, der mit dem soeben kopierten übereinstimmt, außer dass er auf `/.auth/login/aad/callback` endet. Dies ermöglicht, dass die Registrierung von der Konfiguration der App Service-Authentifizierung/-Autorisierung verwendet werden kann.
1. Navigieren Sie zu der App Service-Authentifizierungs-/Autorisierungskonfiguration für Ihre App.
1. Sammeln Sie die Konfiguration für den Microsoft-Kontoanbieter.
1. Konfigurieren Sie den Azure Active Directory-Anbieter mithilfe des Verwaltungsmodus „Erweitert“, wobei Sie die Client-ID und die geheimen Clientschlüsselwerte bereitstellen, die Sie im vorherigen Schritt erfasst haben. Verwenden Sie für die Aussteller-URL `<authentication-endpoint>/<tenant-id>/v2.0`, und ersetzen Sie *\<authentication-endpoint>* durch den [Authentifizierungsendpunkt für Ihre Cloudumgebung](../active-directory/develop/authentication-national-cloud.md#azure-ad-authentication-endpoints) (z. B. „https://login.microsoftonline.com“ für globales Azure), und ersetzen Sie außerdem *\<tenant-id>* durch Ihre **Verzeichnis (Mandanten)-ID**.
1. Nachdem Sie die Konfiguration gespeichert haben, testen Sie den Anmeldeablauf, indem Sie in Ihrem Browser zum Endpunkt `/.auth/login/aad` in Ihrem Standort navigieren und den Anmeldevorgang durchführen.
1. An diesem Punkt haben Sie die Konfiguration erfolgreich übernommen, aber die vorhandene Konfiguration des Microsoft-Kontoanbieters bleibt erhalten. Bevor Sie sie entfernen, stellen Sie sicher, dass alle Teile Ihrer App über Anmeldelinks auf den Azure Active Directory-Anbieter verweisen usw. Vergewissern Sie sich, dass alle Teile Ihrer App erwartungsgemäß funktionieren.
1. Nachdem Sie überprüft haben, ob der AAD Azure Active Directory-Anbieter funktioniert, können Sie die Konfiguration des Microsoft-Kontoanbieters entfernen.

> [!WARNING]
> Es ist möglich, die beiden Registrierungen durch Ändern der [unterstützten Kontotypen](../active-directory/develop/supported-accounts-validation.md) für die AAD-App-Registrierung zu konvergieren. Dies würde jedoch eine neue Zustimmungsaufforderung für Benutzer von Microsoft-Konten erzwingen, und die Identitätsansprüche dieser Benutzer können sich in der Struktur unterscheiden, weil `sub` Werte signifikant ändert, da eine neue App-ID verwendet wird. Dieser Ansatz wird nur empfohlen, wenn Sie sich über die Konsequenzen vollkommen bewusst sind. Warten Sie stattdessen lieber, bis Unterstützung für die zwei Registrierungen in der V2-API-Oberfläche bereitgestellt wird.

#### <a name="switching-to-v2"></a>Wechseln zu V2

Nachdem Sie die obigen Schritte ausgeführt haben, navigieren Sie im Azure-Portal zu der App. Wählen Sie den Abschnitt „Authentifizierung (Vorschau)“ aus. 

Alternativ können Sie eine PUT-Anforderung an die `config/authsettingsv2`-Ressource unterhalb der Standortressource stellen. Das Schema für die Nutzdaten ist dasselbe wie das im Abschnitt [Dateibasierte Konfiguration in Azure App Service-Authentifizierung](configure-authentication-file-based.md) erfasste.

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>Anheften Ihrer App an eine bestimmte Authentifizierungsruntimeversion

Wenn Sie die Authentifizierung/Autorisierung aktivieren, wird die Plattformmiddleware in Ihre HTTP-Anforderungspipeline eingefügt, wie in der [Featureübersicht](overview-authentication-authorization.md#how-it-works) beschrieben. Diese Plattformmiddleware wird im Rahmen der routinemäßigen Plattformupdates in regelmäßigen Abständen mit neuen Features und Verbesserungen aktualisiert. Standardmäßig wird Ihre Web- oder Funktions-App auf der neuesten Version dieser Plattformmiddleware ausgeführt. Diese automatischen Updates sind immer abwärtskompatibel. In dem seltenen Fall, dass dieses automatische Update ein Runtimeproblem bei Ihrer Web- oder Funktions-App verursacht, können Sie jedoch vorübergehend ein Rollback zur vorherigen Middlewareversion ausführen. In diesem Artikel wird erläutert, wie Sie eine App temporär einer bestimmten Version der Authentifizierungsmiddleware anheften.

### <a name="automatic-and-manual-version-updates"></a>Automatische und manuelle Versionsupdates 

Sie können Ihre App einer bestimmten Version der Plattformmiddleware anheften, indem Sie eine `runtimeVersion`-Einstellung für die App vornehmen. Ihre App wird immer auf der neuesten Version ausgeführt, es sei denn, Sie möchten sie explizit einer bestimmten Version anheften. Es werden mehrere Versionen gleichzeitig unterstützt. Wenn Sie sie einer ungültigen Version anheften, die nicht mehr unterstützt wird, verwendet Ihre App stattdessen die neueste Version. Um immer die neueste Version auszuführen, legen Sie `runtimeVersion` auf ~1 fest. 

### <a name="view-and-update-the-current-runtime-version"></a>Anzeigen und Aktualisieren der aktuellen Runtimeversion

Sie können die von der App verwendete Runtimeversion ändern. Die neue Runtimeversion sollte nach dem Neustart der App wirksam werden. 

#### <a name="view-the-current-runtime-version"></a>Anzeigen der aktuellen Runtimeversion

Sie können die aktuelle Version der Plattformauthentifizierungs-Middleware entweder mithilfe der Azure CLI oder über einen der HTTP-Endpunkte der integrierten Version in ihrer App anzeigen.

##### <a name="from-the-azure-cli"></a>Über die Azure CLI

Zeigen Sie mithilfe der Azure CLI die aktuelle Middlewareversion mit dem Befehl [az webapp auth show](/cli/azure/webapp/auth#az_webapp_auth_show) an.

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

Ersetzen Sie in diesem Code `<my_app_name>` durch den Namen der App. Ersetzen Sie außerdem `<my_resource_group>` durch den Namen der Ressourcengruppe für Ihre App.

Das Feld `runtimeVersion` wird in der CLI-Ausgabe angezeigt. Es ähnelt der folgenden Beispielausgabe, die zur besseren Lesbarkeit beschnitten wurde: 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>Vom Versionsendpunkt

Sie können auch über den /.auth/version-Endpunkt einer App die aktuelle Middlewareversion anzeigen, auf der die App ausgeführt wird. Es ähnelt der folgenden Beispielausgabe:
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>Aktualisieren der aktuellen Runtimeversion

Mithilfe der Azure CLI können Sie die `runtimeVersion`-Einstellung in der App mit dem Befehl [az webapp auth update](/cli/azure/webapp/auth#az_webapp_auth_update) aktualisieren.

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

Ersetzen Sie `<my_app_name>` durch den Namen Ihrer App. Ersetzen Sie außerdem `<my_resource_group>` durch den Namen der Ressourcengruppe für Ihre App. Ersetzen Sie außerdem `<version>` durch eine gültige Version der Runtime 1.x oder durch `~1` für die aktuelle Version. Mithilfe der [Versionshinweise zu den verschiedenen Runtimeversionen](https://github.com/Azure/app-service-announcements) können Sie die Version ermitteln, an die die App angeheftet werden soll.

Sie können diesen Befehl an der [Azure Cloud Shell](../cloud-shell/overview.md) ausführen, indem Sie im vorangehenden Codebeispiel **Ausprobieren** auswählen. Sie können auch die [Azure-Befehlszeilenschnittstelle lokal](/cli/azure/install-azure-cli) zum Ausführen dieses Befehls verwenden, nachdem Sie sich mit [az login](/cli/azure/reference-index#az_login) angemeldet haben.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Tutorial: Umfassendes Authentifizieren und Autorisieren von Benutzern in Azure App Service](tutorial-auth-aad.md)
