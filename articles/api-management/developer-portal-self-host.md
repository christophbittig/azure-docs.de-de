---
title: Selbsthosten des Entwicklerportals
titleSuffix: Azure API Management
description: Hier erfahren Sie, wie Sie das API Management-Entwicklerportal selbst hosten.
author: dlepow
ms.author: danlep
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: f3e0004115b417c85914c7cc40fd98acf3c63d73
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128592639"
---
# <a name="self-host-the-api-management-developer-portal"></a>Selbsthosten des API Management-Entwicklerportals

In diesem Tutorial erfahren Sie, wie Sie das [API Management-Entwicklerportal](api-management-howto-developer-portal.md) selbst hosten. Das Selbsthosting gibt Ihnen die Flexibilität, das Entwicklerportal mit benutzerdefinierter Logik und benutzerdefinierten Widgets zu erweitern, um Seiten zur Laufzeit dynamisch anzupassen. Sie können mehrere Portale für Ihre API Management-Instanz mit jeweils unterschiedlichen Features selbst hosten. Wenn Sie ein Portal selbst hosten, sind Sie der Maintainer und für Upgrades verantwortlich. 

Die folgenden Schritte zeigen, wie Sie Ihre lokale Entwicklungsumgebung einrichten, Änderungen im Entwicklerportal vornehmen und diese dann in einem Azure-Speicherkonto veröffentlichen und bereitstellen.

Wenn Sie bereits Mediendateien im verwalteten Portal hochgeladen oder geändert haben, lesen Sie den Abschnitt [Wechseln von einem verwalteten zu einem selbstgehosteten Entwicklerportal](#move-from-managed-to-self-hosted-developer-portal) weiter unten in diesem Artikel.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Voraussetzungen

Zum Einrichten einer lokalen Entwicklungsumgebung benötigen Sie Folgendes:

- Eine API Management-Dienstinstanz. Sollten Sie über keine verfügen, lesen Sie [Schnellstart: Erstellen einer neuen Azure API Management-Dienstinstanz mithilfe des Azure-Portals](get-started-create-service-instance.md).
- Ein Azure-Speicherkonto mit aktiviertem [Feature für statische Websites](../storage/blobs/storage-blob-static-website.md). Siehe [Erstellen Sie ein Speicherkonto](../storage/common/storage-account-create.md).
- Git auf Ihrem Computer. Verwenden Sie zum Installieren [dieses Git-Tutorial](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
- Node.js (LTS-Version, `v10.15.0` oder höher) und npm auf Ihrem Computer. Weitere Informationen finden Sie unter [Herunterladen und Installieren von Node.js und npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm).
- Azure-Befehlszeilenschnittstelle. Führen Sie die [Schritte zum Installieren der Azure CLI](/cli/azure/install-azure-cli-windows) aus.

## <a name="step-1-set-up-local-environment"></a>Schritt 1: Einrichten der lokalen Umgebung

Die Einrichtung Ihrer lokalen Umgebung umfasst das Klonen des Repositorys, den Wechsel zur neuesten Version des Entwicklerportals und die Installation von npm-Paketen.

1. Klonen Sie das Repository [api-management-developer-portal](https://github.com/Azure/api-management-developer-portal.git) aus GitHub:

    ```console
    git clone https://github.com/Azure/api-management-developer-portal.git
    ```
1. Navigieren Sie zu Ihrer lokalen Kopie des Repositorys:

    ```console
    cd api-management-developer-portal
    ```

1. Checken Sie das neueste Release des Portals aus.

    Überprüfen Sie vor dem Ausführen des folgenden Codes das aktuelle Releasetag im [Abschnitt „Releases“ des Repositorys](https://github.com/Azure/api-management-developer-portal/releases), und ersetzen Sie `<current-release-tag>` durch das neueste Releasetag.
    
    ```console
    git checkout <current-release-tag>
    ```

1. Installieren Sie alle verfügbaren npm-Pakete:

    ```console
    npm install
    ```

> [!TIP]
> Verwenden Sie immer das [neueste Portalrelease](https://github.com/Azure/api-management-developer-portal/releases), und halten Sie Ihr geforktes Portal auf dem neuesten Stand. Die Softwareentwickler verwenden den Branch `master` dieses Repositorys für die alltägliche Entwicklung. Er enthält instabile Versionen der Software.

## <a name="step-2-configure-json-files-static-website-and-cors-settings"></a>Schritt 2: Konfigurieren von JSON-Dateien, statischer Website und CORS-Einstellungen

Für das Entwicklerportal wird die REST-API von API Management benötigt, um den Inhalt verwalten zu können.

### <a name="configdesignjson-file"></a>Datei „config.design.json“

Navigieren Sie zum Ordner `src`, und öffnen Sie die Datei `config.design.json`.

```json
{
  "environment": "development",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature ...",
  "backendUrl": "https://<service-name>.developer.azure-api.net",
  "useHipCaptcha": false
}
```

Konfigurieren Sie die Datei:

1. Ersetzen Sie `<service-name>` im Wert `managementApiUrl` durch den Namen Ihrer API Management-Instanz. Sollten Sie eine [benutzerdefinierte Domäne](configure-custom-domain.md) konfiguriert haben, verwenden Sie stattdessen diese Domäne (beispielsweise `https://management.contoso.com`).

    ```json
    {
    ...
    "managementApiUrl": "https://contoso-api.management.azure-api.net"
    ...
    ``` 

1. [Erstellen Sie manuell ein SAS-Token](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-authentication#ManuallyCreateToken), um den direkten REST-API-Zugriff auf Ihre API Management-Instanz zu ermöglichen.

1. Kopieren Sie das generierte Token, und fügen Sie es als Wert für `managementApiAccessToken` ein.

1. Ersetzen Sie `<service-name>` im Wert `backendUrl` durch den Namen Ihrer API Management-Instanz. Sollten Sie eine [benutzerdefinierte Domäne](configure-custom-domain.md) konfiguriert haben, verwenden Sie stattdessen diese Domäne (beispielsweise `https://portal.contoso.com`).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

1. Wenn Sie CAPTCHA in Ihrem Entwicklerportal aktivieren möchten, finden Sie weitere Informationen unter [Aktivieren von CAPTCHA](#enable-captcha).

### <a name="configpublishjson-file"></a>Datei „config.publish.json“

Navigieren Sie zum Ordner `src`, und öffnen Sie die Datei `config.publish.json`.

```json
{
  "environment": "publishing",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "managementApiAccessToken": "SharedAccessSignature...",
  "useHipCaptcha": false
}
```

Konfigurieren Sie die Datei:

1. Kopieren Sie die Werte von `managementApiUrl` und `managementApiAccessToken` aus der vorherigen Konfigurationsdatei, und fügen Sie sie ein.

1. Wenn Sie CAPTCHA in Ihrem Entwicklerportal aktivieren möchten, finden Sie weitere Informationen unter [Aktivieren von CAPTCHA](#enable-captcha).

### <a name="configruntimejson-file"></a>Datei „config.runtime.json“

Navigieren Sie zum Ordner `src`, und öffnen Sie die Datei `config.runtime.json`.

```json
{
  "environment": "runtime",
  "managementApiUrl": "https://<service-name>.management.azure-api.net",
  "backendUrl": "https://<service-name>.developer.azure-api.net"
}
```

Konfigurieren Sie die Datei:

1. Kopieren Sie den Wert von `managementApiUrl` aus der vorherigen Konfigurationsdatei, und fügen Sie ihn ein.

1. Ersetzen Sie `<service-name>` im Wert `backendUrl` durch den Namen Ihrer API Management-Instanz. Sollten Sie eine [benutzerdefinierte Domäne](configure-custom-domain.md) konfiguriert haben, verwenden Sie stattdessen diese Domäne (beispielsweise `https://portal.contoso.com`).

    ```json
    {
    ...
    "backendUrl": "https://contoso-api.developer.azure-api.net"
    ...
    ```

### <a name="configure-the-static-website"></a>Konfigurieren der statischen Website

Konfigurieren Sie das Feature **Statische Website** in Ihrem Speicherkonto, indem Sie Routen zu den Index- und Fehlerseiten angeben:

1. Navigieren Sie im Azure-Portal zu Ihrem Speicherkonto, und wählen Sie im Menü auf der linken Seite die Option **Statische Website** aus.

1. Wählen Sie auf der Seite **Statische Website** die Option **Aktiviert** aus.

1. Geben Sie im Feld **Name des Indexdokuments** den Namen *index.html* ein.

1. Geben Sie im Feld **Pfad zum Fehlerdokument** die Zeichenfolge *404/index.html* ein.

1. Wählen Sie **Speichern** aus.

### <a name="configure-the-cors-settings"></a>Konfigurieren der CORS-Einstellungen

So konfigurieren Sie die Einstellungen für die Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS):

1. Navigieren Sie im Azure-Portal zu Ihrem Speicherkonto, und wählen Sie im Menü auf der linken Seite die Option **CORS** aus.

1. Konfigurieren Sie auf der Registerkarte **Blob-Dienst** die folgenden Regeln:

    | Regel | Wert |
    | ---- | ----- |
    | Zulässige Ursprünge | * |
    | Zulässige Methoden | Wählen Sie alle HTTP-Verben aus. |
    | Zulässige Header | * |
    | Verfügbar gemachte Header | * |
    | Maximales Alter | 0 |

1. Wählen Sie **Speichern** aus.

## <a name="step-3-run-the-portal"></a>Schritt 3: Ausführen des Portals

Nun können Sie eine lokale Portalinstanz im Entwicklungsmodus erstellen und ausführen. Im Entwicklungsmodus sind alle Optimierungen deaktiviert und die Quellzuordnungen aktiviert.

Führen Sie den folgenden Befehl aus:

```console
npm start
```

Nach kurzer Zeit wird der Standardbrowser automatisch mit Ihrer lokalen Instanz des Entwicklerportals geöffnet. Die Standardadresse lautet `http://localhost:8080`, der Port kann sich allerdings ändern, falls `8080` bereits belegt ist. Änderungen an der Codebasis des Projekts haben jeweils eine Neuerstellung und die Aktualisierung Ihres Browserfensters zur Folge.

## <a name="step-4-edit-through-the-visual-editor"></a>Schritt 4: Bearbeiten über den visuellen Editor

Führen Sie mithilfe des visuellen Editors die folgenden Aufgaben durch:

- Anpassen Ihres Portals
- Erstellen von Inhalt
- Strukturieren der Website
- Gestalten der Darstellung

Weitere Informationen finden Sie im Tutorial [Zugreifen auf und Anpassen des Entwicklerportals](api-management-howto-developer-portal-customize.md). Darin werden die Grundlagen der administrativen Benutzeroberfläche behandelt und empfohlene Änderungen für den Standardinhalt aufgeführt. Speichern Sie alle Änderungen in der lokalen Umgebung, und drücken Sie **STRG+C**, um sie zu schließen.

## <a name="step-5-publish-locally"></a>Schritt 5: Lokales Veröffentlichen

Die Portaldaten liegen ursprünglich als stark typisierte Objekte vor. Der folgende Befehl wandelt sie in statische Dateien um und platziert die Ausgabe im Verzeichnis `./dist/website`:

```console
npm run publish
```

## <a name="step-6-upload-static-files-to-a-blob"></a>Schritt 6: Hochladen statischer Dateien in ein Blob

Verwenden Sie die Azure CLI, um die lokal generierten statischen Dateien in ein Blob hochzuladen, und stellen Sie sicher, dass Ihre Besucher auf sie zugreifen können:

1. Öffnen Sie die Windows-Eingabeaufforderung, PowerShell oder eine andere Befehlsshell.

1. Führen Sie den folgenden Azure-CLI-Befehl aus:
   
    Ersetzen Sie `<account-connection-string>` durch die Verbindungszeichenfolge Ihres Speicherkontos. Sie finden sie im Abschnitt **Zugriffsschlüssel** Ihres Speicherkontos.

    ```azurecli
    az storage blob upload-batch --source dist/website \
        --destination '$web' \
        --connection-string <account-connection-string>
    ```


## <a name="step-7-go-to-your-website"></a>Schritt 7: Aufrufen Ihrer Website

Ihre Website ist jetzt unter dem Hostnamen verfügbar, der in Ihren Azure Storage-Eigenschaften angegeben ist (**Primärer Endpunkt** unter **Statische Websites**).

## <a name="step-8-change-api-management-notification-templates"></a>Schritt 8: Ändern der API Management-Benachrichtigungsvorlagen

Ersetzen Sie die URL des Entwicklerportals in API Management-Benachrichtigungsvorlagen, um auf Ihr selbstgehostetes Portal zu verweisen. Weitere Informationen finden Sie unter [Konfigurieren von Benachrichtigungen und E-Mail-Vorlagen in Azure API Management](api-management-howto-configure-notifications.md).

Führen Sie insbesondere die folgenden Änderungen an den Standardvorlagen durch:

> [!NOTE]
> Bei den Werten in den folgenden Abschnitten vom Typ **Aktualisiert** wird davon ausgegangen, dass Sie das Portal unter **https:\//portal.contoso.com/** hosten. 

### <a name="email-change-confirmation"></a>Bestätigung der Änderung der E-Mail-Adresse

Aktualisieren Sie die URL des Entwicklerportals in der Benachrichtigungsvorlage **Email change confirmation** (Bestätigung der Änderung der E-Mail-Adresse):

**Ursprünglicher Inhalt**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Aktualisiert**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="new-developer-account-confirmation"></a>Bestätigung für neues Entwicklerkonto

Aktualisieren Sie die URL des Entwicklerportals in der Benachrichtigungsvorlage **New developer account confirmation** (Bestätigung für neues Entwicklerkonto):

**Ursprünglicher Inhalt**

```html
<a id="confirmUrl" href="$ConfirmUrl" style="text-decoration:none">
  <strong>$ConfirmUrl</strong></a>
```

**Aktualisiert**

```html
<a id="confirmUrl" href="https://portal.contoso.com/signup?$ConfirmQuery" style="text-decoration:none">
  <strong>https://portal.contoso.com/signup?$ConfirmQuery</strong></a>
```

### <a name="invite-user"></a>Benutzer einladen

Aktualisieren Sie die URL des Entwicklerportals in der Benachrichtigungsvorlage **Benutzer einladen**:

**Ursprünglicher Inhalt**

```html
<a href="$ConfirmUrl">$ConfirmUrl</a>
```

**Aktualisiert**

```html
<a href="https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery">https://portal.contoso.com/confirm-v2/identities/basic/invite?$ConfirmQuery</a>
```

### <a name="new-subscription-activated"></a>Neues Abonnement aktiviert

Aktualisieren Sie die URL des Entwicklerportals in der Benachrichtigungsvorlage für **Neues Abonnement aktiviert**:

**Ursprünglicher Inhalt**

```html
Thank you for subscribing to the <a href="http://$DevPortalUrl/products/$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Aktualisiert**

```html
Thank you for subscribing to the <a href="https://portal.contoso.com/product#product=$ProdId"><strong>$ProdName</strong></a> and welcome to the $OrganizationName developer community. We are delighted to have you as part of the team and are looking forward to the amazing applications you will build using our API!
```

**Ursprünglicher Inhalt**

```html
Visit the developer <a href="http://$DevPortalUrl/developer">profile area</a> to manage your subscription and subscription keys
```

**Aktualisiert**

```html
Visit the developer <a href="https://portal.contoso.com/profile">profile area</a> to manage your subscription and subscription keys
```

**Ursprünglicher Inhalt**

```html
<a href="http://$DevPortalUrl/docs/services?product=$ProdId">Learn about the API</a>
```

**Aktualisiert**

```html
<a href="https://portal.contoso.com/product#product=$ProdId">Learn about the API</a>
```

**Ursprünglicher Inhalt**

```html
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/applications">Feature your app in the app gallery</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">You can publish your application on our gallery for increased visibility to potential new users.</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
  <strong>
    <a href="http://$DevPortalUrl/issues">Stay in touch</a>
  </strong>
</p>
<p style="font-size:12pt;font-family:'Segoe UI'">
      If you have an issue, a question, a suggestion, a request, or if you just want to tell us something, go to the <a href="http://$DevPortalUrl/issues">Issues</a> page on the developer portal and create a new topic.
</p>
```

**Aktualisiert**

```html
<!--Remove the entire block of HTML code above.-->
```

### <a name="password-change-confirmation"></a>Bestätigung der Kennwortänderung

Aktualisieren Sie die URL des Entwicklerportals in der Benachrichtigungsvorlage **Password change confirmation** (Bestätigung der Kennwortänderung):

**Ursprünglicher Inhalt**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Aktualisiert**

```html
<a href="https://portal.contoso.com/confirm-password?$ConfirmQuery">https://portal.contoso.com/confirm-password?$ConfirmQuery</a>
```

### <a name="all-templates"></a>Alle Vorlagen

Aktualisieren Sie die URL des Entwicklerportals in jeder Vorlage mit einem Link in der Fußzeile:

**Ursprünglicher Inhalt**

```html
<a href="$DevPortalUrl">$DevPortalUrl</a>
```

**Aktualisiert**

```html
<a href="https://portal.contoso.com/">https://portal.contoso.com/</a>
```

## <a name="move-from-managed-to-self-hosted-developer-portal"></a>Wechseln von einem verwalteten zu einem selbstgehosteten Entwicklerportal

Im Laufe der Zeit ändern sich möglicherweise Ihre geschäftlichen Anforderungen. Dadurch kann sich eine Situation ergeben, in der die verwaltete Version des Entwicklerportals für API Management Ihre Anforderungen nicht mehr erfüllt. Eine neue Anforderung kann beispielsweise die Erstellung eines benutzerdefinierten Widgets für die Integration eines externen Datenanbieters erforderlich machen. Im Gegensatz zur verwalteten Version bietet Ihnen die selbstgehostete Version des Portals uneingeschränkte Flexibilität und Erweiterbarkeit.

### <a name="transition-process"></a>Übergangsprozess

Sie können innerhalb der gleichen API Management-Dienstinstanz von der verwalteten Version auf eine selbstgehostete Version umsteigen. Bei diesem Prozess bleiben die Änderungen erhalten, die Sie in der verwalteten Version des Portals vorgenommen haben. Erstellen Sie vorab unbedingt eine Sicherung des Portalinhalts. Das Sicherungsskript befindet sich im Ordner `scripts` des [GitHub-Repositorys für das API Management-Entwicklerportal](https://github.com/Azure/api-management-developer-portal).

Der Konvertierungsprozess ist nahezu identisch mit der Einrichtung eines generischen selbstgehosteten Portals, die in den vorherigen Schritten in diesem Artikel gezeigt wurde. Es gibt allerdings eine Ausnahme im Konfigurationsschritt. Das Speicherkonto in der Datei `config.design.json` muss dem Speicherkonto der verwalteten Version des Portals entsprechen. Eine Anleitung zum Abrufen der SAS-URL finden Sie im Tutorial [Verwenden einer systemseitig zugewiesenen verwalteten Identität eines virtuellen Linux-Computers für den Zugriff auf Azure Storage mithilfe von SAS-Anmeldeinformationen](../active-directory/managed-identities-azure-resources/tutorial-linux-vm-access-storage-sas.md#get-a-sas-credential-from-azure-resource-manager-to-make-storage-calls).

> [!TIP]
> Es empfiehlt sich, in der Datei `config.publish.json` ein separates Speicherkonto zu verwenden. Dieser Ansatz gibt Ihnen mehr Kontrolle und vereinfacht die Verwaltung des Hostingdiensts Ihres Portals.

## <a name="enable-captcha"></a>Aktivieren von CAPTCHA

Beim Einrichten des selbstgehosteten Portals haben Sie möglicherweise CAPTCHA über die Einstellung `useHipCaptcha` deaktiviert. Die Kommunikation mit CAPTCHA erfolgt über einen Endpunkt, der die Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS) nur für den Hostnamen des verwalteten Entwicklerportals zulässt. Von einem selbstgehosteten Entwicklerportal wird ein anderer Hostname verwendet, und die Kommunikation wird von CAPTCHA nicht zugelassen.

### <a name="update-the-json-config-files"></a>Aktualisieren der JSON-Konfigurationsdateien

So aktivieren Sie CAPTCHA in Ihrem selbstgehosteten Portal:

1. Weisen Sie dem Endpunkt **Entwicklerportal** Ihres API Management-Diensts eine benutzerdefinierte Domäne (beispielsweise `api.contoso.com`) zu.

    Diese Domäne gilt für die verwaltete Version Ihres Portals und den CAPTCHA-Endpunkt. Eine entsprechende Anleitung finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens für Ihre Azure API Management-Instanz](configure-custom-domain.md).

1. Navigieren Sie in der [lokalen Umgebung](#step-1-set-up-local-environment) für Ihr selbstgehostetes Portal zum Ordner `src`.

1. Bearbeiten Sie die Konfigurationsdateien (`json`):

    | Datei | Neuer Wert | Hinweis |
    | ---- | --------- | ---- |
    | `config.design.json`| `"backendUrl": "https://<custom-domain>"` | Ersetzen Sie `<custom-domain>` durch die benutzerdefinierte Domäne, die Sie im ersten Schritt eingerichtet haben. |
    |  | `"useHipCaptcha": true` | Ändern Sie den Wert in `true`. |
    | `config.publish.json`| `"backendUrl": "https://<custom-domain>"` | Ersetzen Sie `<custom-domain>` durch die benutzerdefinierte Domäne, die Sie im ersten Schritt eingerichtet haben. |
    |  | `"useHipCaptcha": true` | Ändern Sie den Wert in `true`. |
    | `config.runtime.json` | `"backendUrl": "https://<custom-domain>"` | Ersetzen Sie `<custom-domain>` durch die benutzerdefinierte Domäne, die Sie im ersten Schritt eingerichtet haben. |

1. [Veröffentlichen](#step-5-publish-locally) Sie das Portal.

1. Laden Sie das neu veröffentlichte Portal hoch, und hosten Sie es. (Informationen zum Hochladen finden Sie [hier](#step-6-upload-static-files-to-a-blob).)

1. Machen Sie das selbstgehostete Portal über eine benutzerdefinierte Domäne verfügbar.

Die erste und zweite Ebene der Portaldomäne müssen der im ersten Schritt eingerichteten Domäne entsprechen. Beispiel: `portal.contoso.com`. Die genauen Schritte hängen von der gewählten Hostingplattform ab. Wenn Sie ein Azure-Speicherkonto verwendet haben, finden Sie eine entsprechende Anleitung unter [Zuordnen einer benutzerdefinierten Domäne zu einem Azure Blob Storage-Endpunkt](../storage/blobs/storage-custom-domain-name.md).

## <a name="next-steps"></a>Nächste Schritte

- Informieren Sie sich über [alternative Selbsthosting-Ansätze](developer-portal-alternative-processes-self-host.md).
