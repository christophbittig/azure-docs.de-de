---
title: Erstellen einer App-Registrierung (CLI)
titleSuffix: Azure Digital Twins
description: Erfahren Sie, wie eine Azure AD-App-Registrierung als Authentifizierungsoption für Client-Apps mit der CLI erstellt wird.
author: baanders
ms.author: baanders
ms.date: 9/8/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 6b769bbc1b3edb9a680fc6e2823a4e5eaeeaa201
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128617752"
---
# <a name="create-an-app-registration-to-use-with-azure-digital-twins-cli"></a>Erstellen einer App-Registrierung für die Verwendung mit Azure Digital Twins (CLI)

[!INCLUDE [digital-twins-create-app-registration-selector.md](../../includes/digital-twins-create-app-registration-selector.md)]

Bei der Verwendung einer Azure Digital Twins-Instanz ist es üblich, über Clientanwendungen mit dieser Instanz zu interagieren – etwa mit einer benutzerdefinierten Client-App oder mit einem Beispiel wie [Azure Digital Twins-Explorer](quickstart-azure-digital-twins-explorer.md). Diese Anwendungen müssen sich bei Azure Digital Twins authentifizieren, um damit zu interagieren, und einige der [Authentifizierungsmechanismen](how-to-authenticate-client.md), die Apps verwenden können, umfassen eine [Azure AD](../active-directory/fundamentals/active-directory-whatis.md)-**App-Registrierung** (Azure Active Directory).

Die App-Registrierung ist nicht für alle Authentifizierungsszenarien erforderlich. Wenn Sie jedoch eine Authentifizierungsstrategie oder ein Codebeispiel verwenden, die bzw. das eine App-Registrierung erfordert, zeigt Ihnen dieser Artikel, wie Sie diese(s) mithilfe der [Azure CLI](/cli/azure/what-is-azure-cli) einrichten. Außerdem wird erläutert, wie Sie [wichtige Werte sammeln](#collect-important-values), die Sie benötigen, um die App-Registrierung für die Authentifizierung zu verwenden.

## <a name="azure-ad-app-registrations"></a>Azure AD-App-Registrierungen

[Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) ist der cloudbasierte Identitäts- und Zugriffsverwaltungsdienst von Microsoft. Das Einrichten einer **App-Registrierung** in Azure AD ist eine Möglichkeit, einer Client-App Zugriff auf Azure Digital Twins zu gewähren.

Über diese App-Registrierung konfigurieren Sie die Zugriffsberechtigungen für die [Azure Digital Twins-APIs](concepts-apis-sdks.md). Ihre Client-App kann sich später für die App-Registrierung mit den **Client- und Mandanten-ID-Werten** der Registrierung authentifizieren. Daraufhin werden ihr die konfigurierten Zugriffsberechtigungen für die APIs erteilt.

>[!TIP]
> Es empfiehlt sich, bei Bedarf jeweils eine neue App-Registrierung einzurichten. Wenn Sie diesen Vorgang nur ein Mal ausführen möchten, können Sie *alternativ* eine einzelne App-Registrierung einrichten, die von allen Szenarien gemeinsam verwendet wird.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-manifest"></a>Erstellen eines Manifests

Erstellen Sie zunächst eine Datei mit bestimmten Dienstinformationen, die Ihre App-Registrierung für den Zugriff auf die Azure Digital Twins-APIs benötigt. Später übergeben Sie diese Datei beim Erstellen der App-Registrierung, um die Azure Digital Twins-Berechtigungen einzurichten.

Erstellen Sie auf Ihrem Computer eine neue JSON-Datei namens **manifest.json**. Kopieren Sie diesen Text in die Datei:

```json
[
    {
        "resourceAppId": "0b07f429-9f4b-4714-9392-cc5e8e80c8b0",
        "resourceAccess": [
            {
                "id": "4589bd03-58cb-4e6c-b17f-b580e39652f8",
                "type": "Scope"
            }
        ]
    }
]
```

Der statische Wert `0b07f429-9f4b-4714-9392-cc5e8e80c8b0` ist die Ressourcen-ID für den Azure Digital Twins-Dienstendpunkt, den Ihre App-Registrierung für den Zugriff auf die Azure Digital Twins-APIs benötigt.
 
Speichern Sie die fertige Datei.

### <a name="upload-to-cloud-shell"></a>Hochladen in Cloud Shell

Laden Sie als Nächstes die erstellte Manifestdatei in die Cloud Shell hoch, damit Sie beim Konfigurieren der App-Registrierung in Cloud Shell-Befehlen darauf zugreifen können.

Um die Datei hochzuladen, wechseln Sie in Ihrem Browser zum Fenster „Cloud Shell“. auf das Symbol „Dateien hochladen/herunterladen“ und dann auf „Hochladen“.

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Screenshot der Azure Cloud Shell. Das Symbol „Hochladen“ ist hervorgehoben.":::

Navigieren Sie zu der Datei **manifest.json** auf Ihrem Computer, und wählen Sie „Öffnen“ aus. Dadurch wird die Datei in den Stamm Ihres Cloud Shell-Speichers hochgeladen.

## <a name="create-the-registration"></a>Erstellen der Registrierung

In diesem Abschnitt führen Sie einen Cloud Shell-Befehl aus, um eine App-Registrierung mit den folgenden Einstellungen zu erstellen:
* Name Ihrer Wahl
* Nur für Konten im Standardverzeichnis (einzelner Mandant) verfügbar.
* Eine Webantwort-URL der Form `http://localhost`
* Lese-/Schreibberechtigungen für die Azure Digital Twins-APIs

Führen Sie den folgenden Befehl aus, um die Registrierung zu erstellen:

```azurecli-interactive
az ad app create --display-name <app-registration-name> --available-to-other-tenants false --reply-urls http://localhost --native-app --required-resource-accesses "@manifest.json"
```

Die Ausgabe des Befehls ist die Information über die App-Registrierung, die Sie erstellt haben. 

## <a name="verify-success"></a>Überprüfen des erfolgreichen Abschlusses

Sie können bestätigen, dass die Azure Digital Twins-Berechtigungen erteilt wurden, indem Sie in der Ausgabe des Befehls `az ad app create` nach den folgenden Feldern suchen und bestätigen, dass deren Werte mit den im folgenden Screenshot gezeigten Werten übereinstimmen:

:::image type="content" source="media/how-to-create-app-registration/cli-required-resource-access.png" alt-text="Screenshot von Cloud Shell Ausgabe des Befehls zum Erstellen der App-Registrierung Die Elemente unter &quot;requiredResourceAccess&quot; sind hervorgehoben: Es gibt den Wert &quot;resourceAppId&quot; 0b07f429-9f4b-4714-9392-cc5e8e80c8b0, und den Wert &quot;resourceAccess > id&quot; von 4589bd03-58cb-4e6c-b17f-b580e39652f8.":::

Sie können auch im Azure-Portal überprüfen, ob die App-Registrierung erfolgreich erstellt wurde. Anweisungen zum Portal finden Sie unter [Überprüfen des Erfolgs (Portal)](how-to-create-app-registration-portal.md#verify-success).

## <a name="collect-important-values"></a>Erfassen wichtiger Werte

Erfassen Sie als Nächstes einige wichtige Werte zu der App-Registrierung, die Sie benötigen, um die App-Registrierung zum Authentifizieren einer Clientanwendung zu verwenden. Diese werten schließen Folgende Angaben ein:
* **Ressourcenname**
* **Client-ID**
* **Mandanten-ID**
* **Geheimer Clientschlüssel**

Um mit Azure Digital Twins arbeiten zu können, lautet der **Ressourcenname** `http://digitaltwins.azure.net`.

In den folgenden Abschnitten wird beschrieben, wie Sie die anderen Werte finden.

### <a name="collect-client-id-and-tenant-id"></a>Erfassen der Client-ID und Mandanten-ID

Um die App-Registrierung für die Authentifizierung zu verwenden, müssen Sie möglicherweise deren **Anwendungs-ID (Client)** und **Verzeichnis-ID (Mandant)** angeben. In diesem Abschnitt erfassen Sie diese Werte, damit Sie sie speichern und verwenden können, wenn sie benötigt werden.

Sie finden beide Werte in der Ausgabe des Befehls `az ad app create`.

Anwendungs-ID (Client):

:::image type="content" source="media/how-to-create-app-registration/cli-app-id.png" alt-text="Screenshot der Cloud Shell-Ausgabe des Befehls zum Erstellen der App-Registrierung. Der Wert „appId“ ist hervorgehoben.":::

Verzeichnis-ID (Mandant):

:::image type="content" source="media/how-to-create-app-registration/cli-tenant-id.png" alt-text="Screenshot der Cloud Shell-Ausgabe des Befehls zum Erstellen der App-Registrierung. Der Wert „GUID“ in „odata.metadata“ ist hervorgehoben.":::

### <a name="collect-client-secret"></a>Erfassen des geheimen Clientschlüssels

Um einen **geheimen Clientschlüssel** für Ihre App-Registrierung zu erstellen, benötigen Sie den Client-ID-Wert Ihrer App-Registrierung aus dem vorherigen Abschnitt. Verwenden Sie den Wert im folgenden CLI-Befehl, um ein neues Geheimnis zu erstellen:

```azurecli-interactive
az ad app credential reset --id <client-ID> --append
```

Sie können diesem Befehl auch optionale Parameter hinzufügen, um eine Beschreibung der Anmeldeinformationen, ein Enddatum und andere Details anzugeben. Weitere Informationen zu dem Befehl und seinen Parametern finden Sie in der [Dokumentation zu „az ad app credential reset“](/cli/azure/ad/app/credential?view=azure-cli-latest&preserve-view=true#az_ad_app_credential_reset).

Die Ausgabe dieses Befehls enthält Informationen zu dem geheimen Clientschlüssel, den Sie erstellt haben. Kopieren Sie den Wert für `password`, um ihn zu verwenden, wenn Sie den geheimen Clientschlüssel für die Authentifizierung benötigen.

:::image type="content" source="media/how-to-create-app-registration/cli-client-secret.png" alt-text="Screenshot der Cloud Shell-Ausgabe des Befehls zum Erstellen der App-Registrierung. Der Wert „password“ ist hervorgehoben.":::

>[!IMPORTANT]
>Stellen Sie sicher, dass Sie den Wert jetzt kopieren, und speichern Sie ihn an einem sicheren Ort, da er nicht erneut abgerufen werden kann. Wenn Sie den Wert später nicht finden können, müssen Sie ein neues Geheimnis erstellen.

## <a name="other-possible-steps-for-your-organization"></a>Weitere mögliche Schritte für Ihre Organisation

Ihre Organisation erfordert möglicherweise weitere Aktionen von Abonnementbesitzern/Administratoren, um eine App-Registrierung erfolgreich einzurichten. Welche Schritte erforderlich sind, hängt von den spezifischen Einstellungen Ihrer Organisation ab.

Im Folgenden finden Sie einige häufig vorkommende Aktivitäten, die ein Besitzer oder Administrator für das Abonnement möglicherweise ausführen muss.
* Erteilen Sie eine Administratoreinwilligung für die App-Registrierung. In Ihrer Organisation ist möglicherweise die Einstellung **Administratoreinwilligung erforderlich** global in Azure AD für alle App-Registrierungen in Ihrem Abonnement aktiviert. In diesem Falle muss der Besitzer/Administrator möglicherweise zusätzliche delegierte Berechtigungen oder Anwendungsberechtigungen erteilen.
* Aktivieren Sie den öffentlichen Clientzugriff, indem Sie `--set publicClient=true` an einen Erstellungs- oder Aktualisierungsbefehl für die Registrierung anfügen.
* Legen Sie spezifische Antwort-URLs für den Web- und Desktopzugriff mithilfe des Parameters `--reply-urls` fest. Weitere Informationen zur Verwendung dieses Parameters mit `az ad`-Befehlen finden Sie in der [Dokumentation zu „az ad app“](/cli/azure/ad/app?view=azure-cli-latest&preserve-view=true).
* Lassen Sie implizite OAuth2-Authentifizierungsflows mithilfe des Parameters `--oauth2-allow-implicit-flow` zu. Weitere Informationen zur Verwendung dieses Parameters mit `az ad`-Befehlen finden Sie in der [Dokumentation zu „az ad app“](/cli/azure/ad/app?view=azure-cli-latest&preserve-view=true).

Weitere Informationen zur App-Registrierung und zu den verschiedenen diesbezüglichen Einrichtungsoptionen finden Sie unter [Registrieren einer Anwendung bei der Microsoft Identity Platform](/graph/auth-register-app-v2).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie eine Azure AD-App-Registrierung festgelegt, die zur Authentifizierung von Clientanwendungen mit den Azure Digital Twins-APIs verwendet werden kann.

Informieren Sie sich im nächsten Schritt über Authentifizierungsmechanismen, darunter einen Mechanismus, der App-Registrierungen verwendet, und andere, bei denen dies nicht der Fall ist:
* [Schreiben von App-Authentifizierungscode](how-to-authenticate-client.md)