---
title: Schützen eines benutzerdefinierten DNS mit einer TLS-/SSL-Bindung
description: Schützen Sie den HTTPS-Zugriff auf Ihre benutzerdefinierte Domäne durch Erstellung einer TLS/SSL-Bindung mit einem Zertifikat. Verbessern Sie die Sicherheit Ihrer Website, indem Sie HTTPS oder TLS 1.2 erzwingen.
tags: buy-ssl-certificates
ms.topic: tutorial
ms.date: 05/13/2021
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 717c9595a9fbda39583be0be5bc6565d2938dc63
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122823366"
---
# <a name="secure-a-custom-dns-name-with-a-tlsssl-binding-in-azure-app-service"></a>Schützen eines benutzerdefinierten DNS-Namens mit einer TLS-/SSL-Bindung in Azure App Service

In diesem Artikel erfahren Sie, wie Sie durch die Erstellung einer Zertifikatsbindung die [benutzerdefinierte Domäne](app-service-web-tutorial-custom-domain.md) in Ihrer [App Service-App](./index.yml) oder [Funktions-App](../azure-functions/index.yml) schützen. Wenn Sie fertig sind, können Sie am Endpunkt `https://` für Ihren benutzerdefinierten DNS-Namen (z. B. `https://www.contoso.com`) auf Ihre App Service-App zugreifen. 

![Web-App mit benutzerdefiniertem TLS-/SSL-Zertifikat](./media/configure-ssl-bindings/app-with-custom-ssl.png)

Das Schützen einer [benutzerdefinierten Domäne](app-service-web-tutorial-custom-domain.md) mit einem Zertifikat umfasst zwei Schritte:

- [Hinzufügen eines privaten Zertifikats zu App Service](configure-ssl-certificate.md), das alle [Anforderungen an private Zertifikate erfüllt](configure-ssl-certificate.md#private-certificate-requirements)
-  Erstellen einer TLS-/SSL-Bindung für die entsprechende benutzerdefinierte Domäne. Dieser zweite Schritt wird in diesem Artikel behandelt.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Aktualisieren des Tarifs für Ihre App
> * Schützen einer benutzerdefinierten Domäne mit einem Zertifikat
> * Erzwingen von HTTPS
> * Erzwingen von TLS 1.1/1.2
> * Automatisieren der TLS-Verwaltung mit Skripts

## <a name="prerequisites"></a>Voraussetzungen

Im Rahmen dieser Schrittanleitung müssen Sie folgende Schritte durchführen:

- [Erstellen einer App Service-App](./index.yml)
- [Zuordnen eines Domänennamens zu Ihrer App](app-service-web-tutorial-custom-domain.md) oder [Kaufen und Konfigurieren eines Domänennamens in Azure](manage-custom-dns-buy-domain.md)
- [Hinzufügen eines privaten Zertifikats zu Ihrer App](configure-ssl-certificate.md)

> [!NOTE]
> Die einfachste Möglichkeit zum Hinzufügen eines privaten Zertifikats besteht darin, [ein von App Service verwaltetes kostenloses Zertifikat zu erstellen](configure-ssl-certificate.md#create-a-free-managed-certificate).

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

<a name="upload"></a>

## <a name="secure-a-custom-domain"></a>Schützen einer benutzerdefinierten Domäne

Führen Sie die folgenden Schritte aus:

Wählen Sie im <a href="https://portal.azure.com" target="_blank">Azure-Portal</a> im linken Menü **App Services** >  **\<app-name>** aus.

Öffnen Sie im linken Navigationsbereich Ihrer App das Dialogfeld **TLS-/SSL-Bindung**, indem Sie folgende Schritte ausführen:

- Auswählen von **Benutzerdefinierte Domänen** > **Bindung hinzufügen**
- Auswählen von **TLS-/SSL-Einstellungen** > **TLS-/SSL-Bindung hinzufügen**

![Hinzufügen der Bindung zur Domäne](./media/configure-ssl-bindings/secure-domain-launch.png)

Wählen Sie unter **Benutzerdefinierte Domäne** die benutzerdefinierte Domäne aus, für die Sie eine Bindung hinzufügen möchten.

Wenn Ihre App bereits über ein Zertifikat für die ausgewählte benutzerdefinierte Domäne verfügt, navigieren Sie direkt zu [Bindung erstellen](#create-binding). Gehen Sie andernfalls wie im Anschluss beschrieben vor.

### <a name="add-a-certificate-for-custom-domain"></a>Hinzufügen eines Zertifikats für eine benutzerdefinierte Domäne

Verfügt Ihre App über kein Zertifikat für die ausgewählte benutzerdefinierte Domäne, haben Sie zwei Optionen:

- **Hochladen eines PFX-Zertifikats:** Führen Sie den Workflow unter [Hochladen eines privaten Zertifikats](configure-ssl-certificate.md#upload-a-private-certificate) aus, und wählen Sie dann diese Option hier aus.
- **Importieren eines App Service-Zertifikats:** Führen Sie den Workflow unter [Importieren eines App Service-Zertifikats](configure-ssl-certificate.md#import-an-app-service-certificate) aus, und wählen Sie dann diese Option hier aus.

> [!NOTE]
> Sie können auch ein [kostenloses Zertifikat erstellen](configure-ssl-certificate.md#create-a-free-managed-certificate) oder [ein Key Vault-Zertifikat importieren](configure-ssl-certificate.md#import-a-certificate-from-key-vault). Sie müssen diesen Schritt jedoch separat ausführen und anschließend zum Dialogfeld **TLS-/SSL-Bindung** zurückkehren.

### <a name="create-binding"></a>Erstellen einer Bindung

Konfigurieren Sie mithilfe der folgende Tabelle die TLS-/SSL-Bindung im Dialogfeld **TLS-/SSL-Bindung**, und klicken Sie dann auf **Bindung hinzufügen**.

| Einstellung | BESCHREIBUNG |
|-|-|
| Benutzerdefinierte Domäne | Der Domänenname, für den die TLS-/SSL-Bindung hinzugefügt werden soll |
| Fingerabdruck des privaten Zertifikats | Das zu bindende Zertifikat. |
| TLS-/SSL-Typ | <ul><li>**[SNI SSL:](https://en.wikipedia.org/wiki/Server_Name_Indication)** Es können mehrere SNI-SSL-Bindungen hinzugefügt werden. Bei dieser Option können mehrere zur selben IP-Adresse gehörige Domänen durch mehrere TLS-/SSL-Zertifikate geschützt werden. Die meisten modernen Browser (einschließlich Internet Explorer, Chrome, Firefox und Opera) unterstützen SNI (ausführlichere Informationen finden Sie unter [Servernamensanzeige](https://wikipedia.org/wiki/Server_Name_Indication)).</li><li>**IP-SSL:** Unter Umständen kann nur eine IP-SSL-Bindung hinzugefügt werden. Bei dieser Option kann eine dedizierte öffentliche IP-Adresse nur durch ein TLS-/SSL-Zertifikat geschützt werden. Führen Sie nach dem Konfigurieren der Bindung die Schritte unter [Neuzuordnen eines Datensatzes für IP-SSL](#remap-records-for-ip-ssl) durch.<br/>IP-SSL wird nur in der Dienstebene **Standard** oder einer höheren Ebene unterstützt. </li></ul> |

Nach Abschluss des Vorgangs wird der TLS-/SSL-Status der benutzerdefinierten Domäne in **Sicher** geändert.

![TLS-/SSL-Bindung erfolgreich](./media/configure-ssl-bindings/secure-domain-finished.png)

> [!NOTE]
> Der Zustand **Sicher** unter **Benutzerdefinierte Domänen** bedeutet, dass die Domäne durch ein Zertifikat geschützt ist. App Service überprüft jedoch nicht, ob das Zertifikat beispielsweise selbstsigniert oder abgelaufen ist, was ebenfalls dazu führen kann, dass ein Browser einen Fehler oder eine Warnung anzeigt.

## <a name="remap-records-for-ip-ssl"></a>Neuzuordnen von Datensätzen für IP-SSL

Wenn Sie nicht IP-SSL in Ihrer App verwenden, fahren Sie mit [Testen von HTTPS](#test-https) fort.

Sie müssen eventuell zwei Änderungen vornehmen:

- Standardmäßig verwendet Ihre App eine freigegebene öffentliche IP-Adresse. Wenn Sie ein Zertifikat mit IP-SSL binden, erstellt App Service eine neue, dedizierte IP-Adresse für Ihre App. Wenn Sie Ihrer App einen A-Eintrag zugeordnet haben, aktualisieren Sie Ihre Domänenregistrierung mit dieser neuen, dedizierten IP-Adresse.

    Die Seite **Benutzerdefinierte Domäne** Ihrer App wird mit der neuen, dedizierten IP-Adresse aktualisiert. [Kopieren Sie diese IP-Adresse](app-service-web-tutorial-custom-domain.md#info) und [ordnen Sie dieser neuen IP-Adresse dann den A-Eintrag neu zu](app-service-web-tutorial-custom-domain.md#4-create-the-dns-records).

- Wenn eine SNI SSL-Bindung an `<app-name>.azurewebsites.net` besteht, [ordnen Sie alle CNAME-Zuordnungen](app-service-web-tutorial-custom-domain.md#4-create-the-dns-records) neu zu, um stattdessen auf `sni.<app-name>.azurewebsites.net` zu verweisen. (Fügen Sie das Präfix `sni` hinzu.)

## <a name="test-https"></a>Testen von HTTPS

Rufen Sie in verschiedenen Browsern `https://<your.custom.domain>` auf, um zu überprüfen, ob Ihre App angeboten wird.

:::image type="content" source="./media/configure-ssl-bindings/app-with-custom-ssl.png" alt-text="Screenshot: Beispiel zum Navigieren zur benutzerdefinierten Domäne mit hervorgehobener URL „contoso.com“":::

Der Anwendungscode kann das Protokoll anhand des Headers „x-appservice-proto“ untersuchen. Der Header hat den Wert `http` oder `https`. 

> [!NOTE]
> Wenn Ihre App Zertifikatüberprüfungsfehler meldet, verwenden Sie wahrscheinlich ein selbstsigniertes Zertifikat.
>
> Wenn dies nicht der Fall ist, haben Sie beim Exportieren des Zertifikats als PFX-Datei eventuell Zwischenzertifikate ausgelassen.

## <a name="prevent-ip-changes"></a>Verhindern von IP-Änderungen

Ihre IP-Adresse für eingehenden Datenverkehr kann sich ändern, wenn Sie eine Bindung löschen, auch wenn es sich um eine IP-SSL-Bindung handelt. Dies ist besonders wichtig, wenn Sie ein Zertifikat verlängern, das sich bereits in einer IP-SSL-Bindung befindet. Um eine Änderung der IP-Adresse in Ihrer App zu vermeiden, führen Sie diese Schritte in angegebener Reihenfolge aus:

1. Laden Sie das neue Zertifikat hoch.
2. Binden Sie das neue Zertifikat an die gewünschte benutzerdefinierte Domäne, ohne die alte zu löschen. Dadurch wird die Bindung ersetzt, anstatt die alte Bindung zu entfernen.
3. Löschen Sie das alte Zertifikat. 

## <a name="enforce-https"></a>Erzwingen von HTTPS

Standardmäßig können Benutzer weiterhin mit HTTP auf Ihre App zugreifen. Sie können alle HTTP-Anforderungen an den HTTPS-Port umleiten.

Wählen Sie im linken Navigationsbereich der App-Seite **TLS/SSL-Einstellungen** aus. Wählen Sie anschließend für **Nur HTTPS** die Option **Ein**.

![Erzwingen von HTTPS](./media/configure-ssl-bindings/enforce-https.png)

Wenn der Vorgang abgeschlossen ist, navigieren Sie zu einer beliebigen HTTP-URL, die auf Ihre App verweist. Beispiel:

- `http://<app_name>.azurewebsites.net`
- `http://contoso.com`
- `http://www.contoso.com`

## <a name="enforce-tls-versions"></a>Erzwingen von TLS-Versionen

Ihre App lässt standardmäßig [TLS](https://wikipedia.org/wiki/Transport_Layer_Security) 1.2 zu. Diese TLS-Version wird von Branchenstandards wie [PCI-DSS](https://wikipedia.org/wiki/Payment_Card_Industry_Data_Security_Standard) empfohlen. Andere TLS-Versionen können wie folgt erzwungen werden:

Wählen Sie im linken Navigationsbereich der App-Seite **TLS/SSL-Einstellungen** aus. Wählen Sie dann unter **TLS-Version** die gewünschte TLS-Mindestversion aus. Diese Einstellung steuert nur eingehende Aufrufe. 

![Erzwingen von TLS 1.1 oder 1.2](./media/configure-ssl-bindings/enforce-tls1-2.png)

Nach Abschluss des Vorgangs lehnt Ihre App alle Verbindungen mit niedrigerer TLS-Version ab.

## <a name="handle-tls-termination"></a>Behandeln der TLS-Terminierung

In App Service erfolgt die [TLS-Terminierung](https://wikipedia.org/wiki/TLS_termination_proxy) in den Modulen für den Netzwerklastenausgleich, sodass alle HTTPS-Anforderungen Ihre App unverschlüsselt erreichen. Wenn Ihre App-Logik überprüfen muss, ob Benutzeranforderungen verschlüsselt sind, können Sie dazu den Header `X-Forwarded-Proto` untersuchen.

In sprachspezifischen Konfigurationsleitfäden (etwa unter [Konfigurieren einer Linux-Node.js-App für Azure App Service](configure-language-nodejs.md#detect-https-session)) wird die Erkennung einer HTTPS-Sitzung in Ihrem Anwendungscode gezeigt.

## <a name="renew-certificate-binding"></a>Verlängern der Zertifikatbindung

> [!NOTE]
> Informationen zum Verlängern eines [von Ihnen erworbenen App Service-Zertifikats](configure-ssl-certificate.md#import-an-app-service-certificate) finden Sie unter [Exportieren eines (App Service)-Zertifikats](configure-ssl-certificate.md#export-certificate). App Service-Zertifikate können automatisch verlängert und die Bindung automatisch synchronisiert werden.

Beim Ersatz eines ablaufenden Zertifikats kann sich die Art der Aktualisierung der Zertifikatbindung durch das neue Zertifikat negativ auf die Benutzerfreundlichkeit auswirken. Ihre IP-Adresse für eingehenden Datenverkehr kann sich z. B. ändern, wenn Sie eine Bindung löschen, auch wenn diese Bindung IP-basiert ist. Dies ist besonders wichtig, wenn Sie ein Zertifikat erneuern, das sich bereits in einer IP-basierten Bindung befindet. Führen Sie die folgenden Schritte in der folgenden Reihenfolge aus, um eine Änderung der IP-Adresse Ihrer App und Ausfallzeiten für Ihre App zu vermeiden:

1. Laden Sie das neue Zertifikat hoch.
2. Binden Sie das neue Zertifikat an dieselbe benutzerdefinierte Domäne, ohne das vorhandene (ablaufende) Zertifikat zu löschen. Dadurch wird die Bindung ersetzt, anstatt das vorhandene Zertifikat zu entfernen.
3. Löschen Sie das vorhandene Zertifikat.

## <a name="automate-with-scripts"></a>Automatisieren mit Skripts

### <a name="azure-cli"></a>Azure CLI

[!code-azurecli[main](../../cli_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.sh?highlight=3-5 "Bind a custom TLS/SSL certificate to a web app")] 

### <a name="powershell"></a>PowerShell

[!code-powershell[main](../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

## <a name="more-resources"></a>Weitere Ressourcen

* [Verwenden eines TLS-/SSL-Zertifikats in Ihrem Code in Azure App Service](configure-ssl-certificate-in-code.md)
* [Häufig gestellte Fragen: App Service-Zertifikate](./faq-configuration-and-management.yml)