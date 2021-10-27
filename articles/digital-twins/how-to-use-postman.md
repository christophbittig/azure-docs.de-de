---
title: Senden von API-Anforderungen mit Postman
titleSuffix: Azure Digital Twins
description: Hier erfahren Sie, wie Sie Postman zum Aufrufen von Azure Digital Twins-APIs autorisieren, konfigurieren und verwenden.
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 10/8/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: 6aeb16ea38d18082b445f8f1e12301e1ae6144b7
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133004"
---
# <a name="how-to-use-postman-to-send-requests-to-the-azure-digital-twins-apis"></a>Verwenden von Postman zum Senden von Anforderungen an die Azure Digital Twins-APIs

[Postman](https://www.getpostman.com/) ist ein REST-Testtool, das wichtige HTTP-Anforderungsfunktionen in einer desktop- und pluginbasierten grafischen Benutzeroberfläche bietet. Sie können es zum Erstellen von HTTP-Anforderungen verwenden und diese an die [REST-APIs von Azure Digital Twins](concepts-apis-sdks.md) übermitteln.

In diesem Artikel wird beschrieben, wie Sie über die folgenden Schritte den [Postman-REST-Client](https://www.getpostman.com/) für die Interaktion mit den Azure Digital Twins-APIs konfigurieren:

1. Verwenden Sie die Azure CLI, um [ein Bearertoken zu erhalten](#get-bearer-token), das Sie für API-Anforderungen in Postman verwenden können.
1. Richten Sie eine [Postman-Sammlung](#about-postman-collections) ein, und konfigurieren Sie den Postman-REST-Client, um das Bearertoken für die Authentifizierung zu verwenden. Beim Einrichten der Sammlung können Sie eine der folgenden Optionen auswählen:
    1. [Importieren einer vorgefertigte Sammlung von Azure Digital Twins-API-Anforderungen](#import-collection-of-azure-digital-twins-apis).
    1. [Erstellen Ihrer von Grund auf neuen eigenen Sammlung](#create-your-own-collection).
1. [Hinzufügen von Anforderungen zu Ihrer konfigurierten Sammlung](#add-an-individual-request) und Senden dieser an die Azure Digital Twins-APIs.

Azure Digital Twins bietet zwei Sätze von APIs, mit denen Sie arbeiten können: **Datenebene** und **Steuerungsebene**. Weitere Informationen zu den Unterschieden zwischen diesen APIs finden Sie unter [Azure Digital Twins-APIs und -SDKs](concepts-apis-sdks.md). Dieser Artikel enthält Informationen für beide API-Sätze.

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen eine Azure Digital Twins-Instanz einrichten und Postman herunterladen, um Postman weiterhin für den Zugriff auf die Azure Digital Twins-APIs zu verwenden. Im restlichen Teil dieses Abschnitts werden diese Schritte beschrieben.

### <a name="set-up-azure-digital-twins-instance"></a>Einrichten einer Azure Digital Twins-Instanz

[!INCLUDE [digital-twins-prereq-instance.md](../../includes/digital-twins-prereq-instance.md)]

### <a name="download-postman"></a>Herunterladen von Postman

Als Nächstes [laden Sie die Desktopversion des Postman-Clients herunter](https://www.getpostman.com/apps).

## <a name="get-bearer-token"></a>Abrufen des Bearertokens

Nachdem Sie Postman und die Azure Digital Twins-Instanz eingerichtet haben, müssen Sie ein Bearertoken abrufen, das von Postman-Anforderungen zur Autorisierung mit den Azure Digital Twins-APIs verwendet werden kann.

Es gibt mehrere Möglichkeiten, dieses Token abzurufen. In diesem Artikel wird die [Azure CLI](/cli/azure/install-azure-cli) verwendet, um sich bei Ihrem Azure-Konto anzumelden und auf diese Weise ein Token zu erhalten.

Wenn Sie eine [Azure CLI lokal installiert](/cli/azure/install-azure-cli) haben, können Sie eine Eingabeaufforderung auf Ihrem Computer starten, um die folgenden Befehle auszuführen.
Andernfalls können Sie ein [Azure Cloud Shell](https://shell.azure.com)-Fenster im Browser öffnen und dort die Befehle ausführen.

1. Stellen Sie zunächst sicher, dass Sie mit den entsprechenden Anmeldeinformationen bei Azure angemeldet sind, indem Sie den folgenden Befehl ausführen:

    ```azurecli-interactive
    az login
    ```

2. Verwenden Sie als Nächstes den Befehl [az account get-access-token](/cli/azure/account?view=azure-cli-latest&preserve-view=true#az_account_get_access_token), um ein Bearertoken mit Zugriff auf den Azure Digital Twins-Dienst abzurufen. In diesem Befehl übergeben Sie die Ressourcen-ID für den Azure Digital Twins-Dienstendpunkt, um ein Zugriffstoken zu erhalten, das auf Azure Digital Twins-Ressourcen zugreifen kann. 

    Der erforderliche Kontext für das Token hängt vom verwendeten API-Satz ab. Verwenden Sie daher die unten aufgeführten Registerkarten, um zwischen den APIs der [Datenebene](concepts-apis-sdks.md#overview-data-plane-apis) und denen der [Steuerungsebene](concepts-apis-sdks.md#overview-control-plane-apis) auszuwählen.

    # <a name="data-plane"></a>[Datenebene](#tab/data-plane)
    
    Um ein Token für die APIs der **Datenebene** zu erhalten, verwenden Sie den folgenden statischen Wert für den Tokenkontext: `0b07f429-9f4b-4714-9392-cc5e8e80c8b0`. Dies ist die Ressourcen-ID für den Azure Digital Twins-Dienstendpunkt.
    
    ```azurecli-interactive
    az account get-access-token --resource 0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```
    
    # <a name="control-plane"></a>[Steuerungsebene](#tab/control-plane)
    
    Um ein Token für die APIs der **Steuerungsebene** zu erhalten, verwenden Sie den folgenden Wert für den Tokenkontext: `https://management.azure.com/`.
    
    ```azurecli-interactive
    az account get-access-token --resource https://management.azure.com/
    ```
    ---

    >[!NOTE]
    > Wenn Sie mit einem Dienstprinzipal oder Benutzerkonto, der bzw. das zu einem anderen Azure Active Directory-Mandanten als dem der Instanz gehört, auf Ihre Azure Digital Twins-Instanz zugreifen müssen, müssen Sie ein **Token** vom Basismandanten der Azure Digital Twins-Instanz anfordern. Weitere Informationen zu diesem Prozess finden Sie unter [Schreiben von Authentifizierungscode für die Client-App](how-to-authenticate-client.md#authenticate-across-tenants).

3. Kopieren Sie den Wert `accessToken` in das Ergebnis, und speichern Sie ihn für die Verwendung im nächsten Abschnitt. Dies ist Ihr **Tokenwert**, den Sie dem Postman-Tool bereitstellen, um Ihre Anforderungen zu autorisieren.

    :::image type="content" source="media/how-to-use-postman/console-access-token.png" alt-text="Screenshot der Konsole mit dem Ergebnis des Befehls „az account get-access-token“. Das Feld „accessToken“ und der zugehörige Beispielwert sind hervorgehoben.":::

>[!TIP]
>Dieses Token ist für mindestens 5 Minuten und höchstens 60 Minuten gültig. Wenn die für das aktuelle Token zugewiesene Zeit abgelaufen ist, können Sie die Schritte in diesem Abschnitt wiederholen, um ein neues Token zu erhalten.

Als Nächstes richten Sie Postman so ein, dass dieses Token für API-Anforderungen an Azure Digital Twins verwendet wird.

## <a name="about-postman-collections"></a>Informationen zu Postman-Sammlungen

Anforderungen in Postman werden in **Sammlungen** (Gruppen von Anforderungen) gespeichert. Wenn Sie eine Sammlung erstellen, um Ihre Anforderungen zu gruppieren, können Sie allgemeine Einstellungen auf viele Anforderungen gleichzeitig anwenden. Dies kann die Autorisierung erheblich vereinfachen, wenn Sie planen, mehrere Anforderungen mit der Azure Digital Twins-API zu erstellen, da Sie diese Details nur einmal für die gesamte Sammlung konfigurieren müssen.

Wenn Sie mit Azure Digital Twins arbeiten, können Sie zunächst eine [vorgefertigte Sammlung aller Azure Digital Twins-Anforderungen](#import-collection-of-azure-digital-twins-apis) importieren. Dies ist möglicherweise sinnvoll, wenn Sie die APIs ausprobieren und schnell ein Projekt mit Anforderungsbeispielen einrichten möchten.

Alternativ dazu können Sie auch ganz von vorn beginnen, indem Sie eine [eigene leere Sammlung erstellen](#create-your-own-collection) und sie mit einzelnen Anforderungen auffüllen, die nur die benötigten APIs aufrufen. 

In den folgenden Abschnitten werden beide Vorgehensweisen beschrieben. Der weitere Verlauf dieses Artikels bezieht sich auf Ihre lokale Postman-Anwendung. Öffnen Sie daher jetzt die Postman-Anwendung auf Ihrem Computer.

## <a name="import-collection-of-azure-digital-twins-apis"></a>Importieren einer Sammlung von Azure Digital Twins-APIs

Eine schnelle Einstiegsmöglichkeit für Azure Digital Twins in Postman besteht darin, eine vorgefertigte Sammlung von Anforderungen für die Azure Digital Twins-APIs zu importieren.

### <a name="download-the-collection-file"></a>Herunterladen der Sammlungsdatei

Der erste Schritt beim Importieren des API-Satzes ist das Herunterladen einer Sammlung. Wählen Sie eine der unten gezeigten Registerkarten für die Datenebene oder die Steuerungsebene aus, um die vorgefertigten Sammlungsoptionen anzuzeigen.

# <a name="data-plane"></a>[Datenebene](#tab/data-plane)

Zurzeit stehen zwei Azure Digital Twins-Sammlungen für die Datenebene zur Auswahl:
* [Azure Digital Twins Postman Collection](https://github.com/microsoft/azure-digital-twins-postman-samples): Diese Sammlung bietet einen einfachen Einstieg in Azure Digital Twins in Postman. Die Anforderungen enthalten Beispieldaten, sodass Sie sie mit minimalen erforderlichen Änderungen ausführen können. Wählen Sie diese Sammlung aus, wenn Sie einen praktischen Satz von Schlüssel-API-Anforderungen mit Beispielinformationen wünschen.
    - Um die Sammlung zu finden, navigieren Sie zum Repositorylink, und öffnen Sie die Datei namens *postman_collection.json*.
* [Swagger-Datei der Azure Digital Twins-Datenebene](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins) : Dieses Repository enthält die komplette Swagger-Datei für den Azure Digital Twins-API-Satz. Sie kann heruntergeladen und in Postman als Sammlung importiert werden. Dadurch wird ein umfassender Satz aller API-Anforderungen bereitgestellt, jedoch mit leeren Daten anstelle von Beispieldaten. Wählen Sie diese Sammlung aus, wenn Sie auf alle API-Aufrufe zugreifen und alle Daten selbst ausfüllen möchten.
    - Um die Sammlung zu finden, navigieren Sie zum Repositorylink, und wählen Sie den Ordner für die neueste Version der Spezifikation aus. Öffnen Sie darin die Datei namens *digitaltwins.json*.

# <a name="control-plane"></a>[Steuerungsebene](#tab/control-plane)

Die derzeit für die Steuerungsebene verfügbare Sammlung ist die [Swagger-Datei der Azure Digital Twins-Steuerungsebene](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Dieses Repository enthält die komplette Swagger-Datei für den Azure Digital Twins-API-Satz. Sie kann heruntergeladen und in Postman als Sammlung importiert werden. Dadurch erhalten Sie einen umfassenden Satz aller API-Anforderungen.

Um die Sammlung zu finden, navigieren Sie zum Repositorylink, und wählen Sie den Ordner für die neueste Version der Spezifikation aus. Öffnen Sie darin die Datei namens *digitaltwins.json*.

---

Im Folgenden wird beschrieben, wie Sie Ihre ausgewählte Sammlung auf Ihren Computer herunterladen, damit Sie sie in Postman importieren können.
1. Verwenden Sie die oben aufgeführten Links, um die Sammlungsdatei in GitHub in Ihrem Browser zu öffnen.
1. Wählen Sie die Schaltfläche **Raw** (Rohdaten) aus, um den Rohtext der Datei zu öffnen.
    :::image type="content" source="media/how-to-use-postman/swagger-raw.png" alt-text="Screenshot der Datei „digitaltwins.json“ der Datenebene auf GitHub. Die Schaltfläche „Raw“ ist hervorgehoben." lightbox="media/how-to-use-postman/swagger-raw.png":::
1. Kopieren Sie den Text aus dem Fenster, und fügen Sie ihn in eine neue Datei auf Ihrem Computer ein.
1. Speichern Sie die Datei mit der Erweiterung .json. (Sie können einen beliebigen Dateinamen festlegen, solange Sie die Datei später wiederfinden.)

### <a name="import-the-collection"></a>Importieren der Sammlung

Importieren Sie als Nächstes die Sammlung in Postman.

1. Wählen Sie im Hauptfenster von Postman die Schaltfläche **Import** aus.
    :::image type="content" source="media/how-to-use-postman/postman-import-collection.png" alt-text="Screenshot eines neu geöffneten Postman-Fensters. Die Schaltfläche „Import“ ist hervorgehoben." lightbox="media/how-to-use-postman/postman-import-collection.png":::

1. Wählen Sie im nachfolgenden Fenster „Import“ die Option **Upload Files** (Dateien hochladen) aus, und navigieren Sie zu der zuvor erstellten Sammlungsdatei auf Ihrem Computer. Wählen Sie Öffnen aus.
1. Klicken Sie zur Bestätigung auf die Schaltfläche **Import**.

    :::image type="content" source="media/how-to-use-postman/postman-import-collection-2.png" alt-text="Screenshot des Postman-Fensters „Import“, das die als Sammlung zu importierende Datei und die Schaltfläche „Import“ zeigt.":::

Die neu importierte Sammlung kann jetzt in der Hauptansicht von Postman auf der Registerkarte „Collections“ (Sammlungen) angezeigt werden.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-imported.png" alt-text="Screenshot des Hauptfensters von Postman. Die neu importierte Sammlung wird auf der Registerkarte „Collections“ (Sammlungen) hervorgehoben." lightbox="media/how-to-use-postman/postman-post-collection-imported.png":::

Fahren Sie anschließend mit dem nächsten Abschnitt fort, um der Sammlung ein Bearertoken für die Autorisierung hinzuzufügen und es mit ihrer Azure Digital Twins-Instanz zu verbinden.

### <a name="configure-authorization"></a>Konfigurieren der Autorisierung

Bearbeiten Sie als Nächstes die erstellte Sammlung, um einige Zugriffsdetails zu konfigurieren. Markieren Sie die von Ihnen erstellte Sammlung, und wählen Sie das Symbol **Weitere Aktionen anzeigen** aus, um ein Menü zu öffnen. Wählen Sie **Bearbeiten** aus.

:::image type="content" source="media/how-to-use-postman/postman-edit-collection.png" alt-text="Screenshot von Postman. Das Symbol „Weitere Aktionen anzeigen“ für die importierte Sammlung ist hervorgehoben, und in den Optionen ist „Edit“ (Bearbeiten) markiert." lightbox="media/how-to-use-postman/postman-edit-collection.png":::

Gehen Sie folgendermaßen vor, um der Sammlung ein Bearertoken für die Autorisierung hinzuzufügen. An dieser Stelle kommt der **Tokenwert** zum Einsatz, den Sie im Abschnitt [Abrufen des Bearertokens](#get-bearer-token) abgerufen haben, um ihn für alle API-Anforderungen in der Sammlung zu verwenden.

1. Stellen Sie im Dialogfeld für Ihre Sammlung sicher, dass Sie sich auf der Registerkarte **Authorization** (Autorisierung) befinden. 

    :::image type="content" source="media/how-to-use-postman/postman-authorization-imported.png" alt-text="Screenshot des Bearbeitungsdialogfelds der importierten Sammlung in Postman mit der Registerkarte „Authorization“ (Autorisierung)." lightbox="media/how-to-use-postman/postman-authorization-imported.png":::

1. Legen Sie den Typ auf **OAuth 2.0** fest, fügen Sie das Zugriffstoken im Feld „Access Token“ (Zugriffstoken) ein, und klicken Sie auf **Save** (Speichern).

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-imported.png" alt-text="Screenshot des Postman-Bearbeitungsdialogfelds für die importierte Sammlung auf der Registerkarte „Authorization“ (Autorisierung). Der Typ lautet „OAuth 2.0“, und das Feld „Access Token“ (Zugriffstoken) ist hervorgehoben." lightbox="media/how-to-use-postman/postman-paste-token-imported.png":::

### <a name="additional-configuration"></a>Zusätzliche Konfiguration

# <a name="data-plane"></a>[Datenebene](#tab/data-plane)

Wenn Sie eine Sammlung auf [Datenebene](concepts-apis-sdks.md#overview-data-plane-apis) erstellen, können Sie diese ganz einfach mit Ihren Azure Digital Twins-Ressourcen verbinden, indem Sie einige in den Sammlungen bereitgestellte **Variablen** festlegen. Wenn viele Anforderungen in einer Sammlung denselben Wert erfordern (z. B. den Hostnamen Ihrer Azure Digital Twins-Instanz), können Sie den Wert in einer Variablen speichern, die für jede Anforderung in der Sammlung gilt. Beide herunterladbaren Sammlungen für Azure Digital Twins verfügen über vorab erstellte Variablen, die Sie auf Sammlungsebene festlegen können.

1. Wechseln Sie im Bearbeitungsdialogfeld für Ihre Sammlung zur Registerkarte **Variables** (Variablen).

1. Verwenden Sie den **Hostnamen** Ihrer Instanz aus dem [Abschnitt „Voraussetzungen“](#prerequisites), um das Feld CURRENT VALUE (AKTUELLER WERT) der relevanten Variablen festzulegen. Wählen Sie **Speichern** aus.

    :::image type="content" source="media/how-to-use-postman/postman-variables-imported.png" alt-text="Screenshot des Bearbeitungsdialogfelds der importierten Sammlung in Postman mit der Registerkarte „Variables“ (Variablen). Das Feld CURRENT VALUE (AKTUELLER WERT) ist hervorgehoben." lightbox="media/how-to-use-postman/postman-variables-imported.png":::

1. Wenn Ihre Sammlung zusätzliche Variablen umfasst, füllen Sie auch diese Werte aus, und speichern Sie sie.

Nach Durchführung der obigen Schritte haben Sie die Konfiguration der Sammlung abgeschlossen. Wenn Sie möchten, können Sie die Bearbeitungsregisterkarte für die Sammlung schließen.

# <a name="control-plane"></a>[Steuerungsebene](#tab/control-plane)

Wenn Sie eine Sammlung der [Steuerungsebene](concepts-apis-sdks.md#overview-control-plane-apis) erstellen, haben Sie bereits alle Schritte ausgeführt, die zum Konfigurieren der Sammlung erforderlich sind. Wenn Sie möchten, können Sie die Bearbeitungsregisterkarte für die Sammlung schließen und mit dem nächsten Abschnitt fortfahren.

--- 

### <a name="explore-requests"></a>Erkunden von Anforderungen

Sehen Sie sich als Nächstes die Anforderungen in der Azure Digital Twins-API-Sammlung an. Sie können die Sammlung erweitern, um die vorab erstellten Anforderungen (nach Kategorie des Vorgangs sortiert) anzuzeigen. 

Für unterschiedliche Anforderungen sind unterschiedliche Informationen zu Ihrer Instanz und den zugehörigen Daten erforderlich. Wenn Sie alle Informationen anzeigen möchten, die zum Erstellen einer bestimmten Anforderung erforderlich sind, suchen Sie in der [Referenzdokumentation zur REST-API für Azure Digital Twins](/rest/api/azure-digitaltwins/) nach den Anforderungsdetails.

Mithilfe der folgenden Schritte können Sie die Details einer Anforderung in der Postman-Sammlung bearbeiten:

1. Wählen Sie sie aus der Liste aus, um die bearbeitbaren Details zu öffnen. 

1. Geben Sie Werte für die Variablen ein, die auf der Registerkarte **Params** (Parameter) unter **Path Variables** (Pfadvariablen) aufgelistet sind.

    :::image type="content" source="media/how-to-use-postman/postman-request-details-imported.png" alt-text="Screenshot von Postman. Die Sammlung wurde erweitert, um eine Anforderung anzuzeigen. Der Abschnitt „Path Variables (Pfadvariablen) ist in den Anforderungsdetails hervorgehoben." lightbox="media/how-to-use-postman/postman-request-details-imported.png":::

1. Geben Sie auf den jeweiligen Registerkarten alle erforderlichen Details für **Header** oder **Body** (Textkörper) an.

Nachdem alle erforderlichen Informationen bereitgestellt wurden, können Sie die Anforderung über die Schaltfläche **Send** (Senden) ausführen.

Mithilfe der im folgenden Abschnitt [Hinzufügen einer eigenen Anforderung](#add-an-individual-request) beschriebenen Vorgehensweise können Sie der Sammlung auch eigene Anforderungen hinzufügen.

## <a name="create-your-own-collection"></a>Erstellen Ihrer eigenen Sammlung

Statt die vorhandene Sammlung aller Azure Digital Twins-APIs zu importieren, können Sie auch eine eigene Sammlung ganz neu erstellen. Anschließend können Sie sie gemäß der [Referenzdokumentation zur REST-API für Azure Digital Twins](/rest/api/azure-digitaltwins/) mit eigenen Anforderungen auffüllen.

### <a name="create-a-postman-collection"></a>Erstellen einer Postman-Sammlung

1. Um eine Sammlung zu erstellen, klicken Sie im Hauptfenster von Postman auf die Schaltfläche **New** (Neu).

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Screenshot des Hauptfensters von Postman. Die Schaltfläche „New“ (Neu) ist hervorgehoben." lightbox="media/how-to-use-postman/postman-new.png":::

    Wählen Sie einen Typ für **Collection** (Sammlung) aus.

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-2.png" alt-text="Screenshot des Dialogfelds „Create New“ (Neu erstellen) in Postman. Die Option „Collection“ (Sammlung) ist hervorgehoben.":::

1. Dadurch wird eine Registerkarte zur Eingabe der Details für die neue Sammlung geöffnet. Wählen Sie das Bearbeitungssymbol neben dem Standardnamen der Sammlung (**New Collection**, neue Sammlung) aus, um ihn durch einen eigenen Namen zu ersetzen. 

    :::image type="content" source="media/how-to-use-postman/postman-new-collection-3.png" alt-text="Screenshot des Bearbeitungsdialogfelds der neuen Sammlung in Postman. Das Bearbeitungssymbol neben dem Namen „New Collection“ (Neue Sammlung) ist hervorgehoben." lightbox="media/how-to-use-postman/postman-new-collection-3.png":::

Fahren Sie anschließend mit dem nächsten Abschnitt fort, um der Sammlung ein Bearertoken für die Autorisierung hinzuzufügen.

### <a name="configure-authorization"></a>Konfigurieren der Autorisierung

Gehen Sie folgendermaßen vor, um der Sammlung ein Bearertoken für die Autorisierung hinzuzufügen. An dieser Stelle kommt der **Tokenwert** zum Einsatz, den Sie im Abschnitt [Abrufen des Bearertokens](#get-bearer-token) abgerufen haben, um ihn für alle API-Anforderungen in der Sammlung zu verwenden.

1. Wechseln Sie im Bearbeitungsdialogfeld für Ihre neue Sammlung zur Registerkarte **Authorization** (Autorisierung).

    :::image type="content" source="media/how-to-use-postman/postman-authorization-custom.png" alt-text="Screenshot des Bearbeitungsdialogfelds der neuen Sammlung in Postman mit der Registerkarte „Authorization“ (Autorisierung)." lightbox="media/how-to-use-postman/postman-authorization-custom.png":::

1. Legen Sie den Typ auf **OAuth 2.0** fest, fügen Sie das Zugriffstoken im Feld „Access Token“ (Zugriffstoken) ein, und klicken Sie auf **Save** (Speichern).

    :::image type="content" source="media/how-to-use-postman/postman-paste-token-custom.png" alt-text="Screenshot des Postman-Bearbeitungsdialogfelds für die neue Sammlung auf der Registerkarte „Authorization“ (Autorisierung). Der Typ lautet „OAuth 2.0“, und das Feld „Access Token“ (Zugriffstoken) ist hervorgehoben." lightbox="media/how-to-use-postman/postman-paste-token-custom.png":::

Nach Durchführung der obigen Schritte haben Sie die Konfiguration der Sammlung abgeschlossen. Wenn Sie möchten, können Sie die Bearbeitungsregisterkarte für die neue Sammlung schließen.

Die neue Sammlung wird in der Hauptansicht von Postman auf der Registerkarte „Collections“ (Sammlungen) angezeigt.

:::image type="content" source="media/how-to-use-postman/postman-post-collection-custom.png" alt-text="Screenshot des Hauptfensters von Postman. Die neu erstellte benutzerdefinierte Sammlung wird auf der Registerkarte „Collections“ (Sammlungen) hervorgehoben." lightbox="media/how-to-use-postman/postman-post-collection-custom.png":::

## <a name="add-an-individual-request"></a>Hinzufügen einer eigenen Anforderung

Nachdem Sie Ihre Sammlung eingerichtet haben, können Sie den Azure Digital Twin-APIs Ihre eigenen Anforderungen hinzufügen.

1. Verwenden Sie noch einmal die Schaltfläche **New** (Neu), um eine Anforderung zu erstellen.

    :::image type="content" source="media/how-to-use-postman/postman-new.png" alt-text="Screenshot des Hauptfensters von Postman. Die Schaltfläche „New“ (Neu) ist hervorgehoben." lightbox="media/how-to-use-postman/postman-new.png":::

    Wählen Sie einen Typ für **Request** (Anforderung) aus.

    :::image type="content" source="media/how-to-use-postman/postman-new-request-2.png" alt-text="Screenshot des Dialogfelds „Create New“ (Neu erstellen) in Postman. Die Option „Request“ (Anforderung) ist hervorgehoben.":::

1. Mit dieser Aktion wird das Fenster SAVE REQUEST (ANFORDERUNG SPEICHERN) geöffnet, in dem Sie einen Namen und eine optionale Beschreibung für die Anforderung eingeben können. Zudem können Sie die Sammlung auswählen, zu der sie gehört. Geben Sie die Informationen ein, und speichern Sie die Anforderung in der Sammlung, die Sie zuvor erstellt haben.

    :::row:::
        :::column:::
            :::image type="content" source="media/how-to-use-postman/postman-save-request.png" alt-text="Screenshot des Fensters „Save request“ (Anforderung speichern) in Postman mit den beschriebenen Feldern. Die Schaltfläche „Save to Azure Digital Twins collection“ (In Azure Digital Twins-Sammlung speichern) ist hervorgehoben.":::
        :::column-end:::
        :::column:::
        :::column-end:::
    :::row-end:::

Nun können Sie die Anforderung in der Sammlung anzeigen und diese auswählen, um die bearbeitbaren Details aufzurufen.

:::image type="content" source="media/how-to-use-postman/postman-request-details-custom.png" alt-text="Screenshot von Postman. Die Azure Digital Twins-Sammlung ist erweitert und zeigt die Details der Anforderung an." lightbox="media/how-to-use-postman/postman-request-details-custom.png":::

### <a name="set-request-details"></a>Festlegen von Anforderungsdetails

Sie benötigen die URL der API und die dafür erforderlichen Informationen, um eine Postman-Anforderung in einer der Azure Digital Twins-APIs zu stellen. Diese Informationen finden Sie in der [Referenzdokumentation für die REST-APIs von Azure Digital Twins](/rest/api/azure-digitaltwins/).

In diesem Artikel wird die Abfrage-API und die entsprechende [Referenzdokumentation](/rest/api/digital-twins/dataplane/query/querytwins) als Beispielabfrage verwendet, um alle digitalen Zwillinge in einer Instanz abzufragen.

1. Die URL und den Typ der Anforderung erhalten Sie in der Referenzdokumentation. Für die Abfrage-API ist dies derzeit *POST* `https://digitaltwins-host-name/query?api-version=2020-10-31`.
1. Legen Sie in Postman den Typ für die Anforderung fest, und geben Sie die Anforderungs-URL ein. Füllen Sie dabei die Platzhalter in der URL nach Bedarf aus. Hier verwenden Sie den **Hostnamen** der Instanz aus dem [Abschnitt „Voraussetzungen“](#prerequisites).
    
   :::image type="content" source="media/how-to-use-postman/postman-request-url.png" alt-text="Screenshot der Details der neuen Anforderung in Postman. Die Abfrage-URL aus der Referenzdokumentation wurde im Feld für die Anforderungs-URL eingegeben." lightbox="media/how-to-use-postman/postman-request-url.png":::
    
1. Überprüfen Sie, ob die Parameter, die für die Anforderung auf der Registerkarte **Parameter** angezeigt werden, den in der Referenzdokumentation beschriebenen Parametern entsprechen. Für diese Anforderung in Postman wurde der Parameter `api-version` automatisch aufgefüllt, als die Anforderungs-URL im vorherigen Schritt eingegeben wurde. Bei der Abfrage-API ist dies der einzige erforderliche Parameter, sodass dieser Schritt erledigt ist.
1. Legen Sie auf der Registerkarte **Autorisierung** den Typ auf **Authentifizierung von der übergeordneten Instanz erben**. Dies gibt an, dass für diese Anforderung die Autorisierung verwendet wird, die Sie zuvor für die gesamte Sammlung eingerichtet haben.
1. Überprüfen Sie, ob die Header, die für die Anforderung auf der Registerkarte **Header** angezeigt werden, den in der Referenzdokumentation beschriebenen entsprechen. Für diese Anforderung wurden mehrere Header automatisch ausgefüllt. Bei der Abfrage-API ist keine der Headeroptionen erforderlich, sodass dieser Schritt erledigt ist.
1. Überprüfen Sie, ob der Textkörper, der für die Anforderung in der Registerkarte **Textkörper** angezeigt wird, den in der Referenzdokumentation beschriebenen Anforderungen entspricht. Für die Abfrage-API ist ein JSON-Textkörper erforderlich, um den Abfragetext bereitzustellen. Nachfolgend finden Sie ein Beispieltextkörper für diese Anforderung, die alle digitalen Zwillinge in der Instanz abfragt:

   :::image type="content" source="media/how-to-use-postman/postman-request-body.png" alt-text="Screenshot der Details der neuen Anforderung in Postman mit der Registerkarte „Body“ (Textkörper). Diese enthält einen unformatierten JSON-Textkörper mit der Abfrage „SELECT * FROM DIGITALTWINS“." lightbox="media/how-to-use-postman/postman-request-body.png":::

   Weitere Informationen zum Erstellen von Azure Digital Twins-Abfragen finden Sie unter [Abfragen des Zwillingsdiagramms von Azure Digital Twins](how-to-query-graph.md).

1. Überprüfen Sie die Referenzdokumentation für alle anderen Felder, die möglicherweise für den Typ der Anforderung erforderlich sind. Bei der Abfrage-API sind nun alle Anforderungen in der Postman-Anforderung erfüllt, sodass dieser Schritt erledigt ist.
1. Verwenden Sie die Schaltfläche **Senden**, um die abgeschlossene Anforderung zu senden.
   :::image type="content" source="media/how-to-use-postman/postman-request-send.png" alt-text="Screenshot von Postman mit den Details der neuen Anforderung. Die Schaltfläche „Send“ (Senden) ist hervorgehoben." lightbox="media/how-to-use-postman/postman-request-send.png":::

Nachdem die Anforderung gesendet wurde, werden die Antwortinformationen im Postman-Fenster unterhalb der Anforderung angezeigt. Sie können den Statuscode der Antwort und jeden Textkörper anzeigen.

:::image type="content" source="media/how-to-use-postman/postman-request-response.png" alt-text="Screenshot der gesendeten Anforderung in Postman. Unterhalb der Anforderungsdetails wird die Antwort angezeigt. Der Status lautet „200 OK“, und der Textkörper zeigt die Abfrageergebnisse an." lightbox="media/how-to-use-postman/postman-request-response.png":::

Sie können auch die Antwort mit den erwarteten Antwortdaten vergleichen, die in der Referenzdokumentation angegeben sind, um das Ergebnis zu überprüfen oder weitere Informationen zu auftretenden Fehlern zu erhalten.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Digital Twins-APIs finden Sie unter [Azure Digital Twins-APIs und -SDKs](concepts-apis-sdks.md) oder in der [Referenzdokumentation für die REST-APIs](/rest/api/azure-digitaltwins/).