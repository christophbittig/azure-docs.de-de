---
title: 'Tutorial: Veröffentlichen von Versionen Ihrer API mithilfe von Azure API Management'
description: In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie in API Management mehrere API-Versionen veröffentlichen.
author: dlepow
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/10/2021
ms.author: danlep
ms.openlocfilehash: 0f9d43f0b9abd53f76efb1e29d40667f1daf19b3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644807"
---
# <a name="tutorial-publish-multiple-versions-of-your-api"></a>Tutorial: Veröffentlichen mehrerer Versionen Ihrer API 

Manchmal ist es unpraktisch, wenn alle Aufrufer genau die gleiche Version Ihrer API verwenden müssen. Wenn Aufrufer auf eine höhere Version upgraden möchten, möchten sie dafür einen leicht verständlichen Ansatz verwenden. Wie in diesem Tutorial gezeigt, können in Azure API Management mehrere *Versionen* bereitgestellt werden. 

Hintergrundinformationen finden Sie unter [Versionen und Revisionen](https://azure.microsoft.com/blog/versions-revisions/).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen einer neuen Version zu einer vorhandenen API
> * Auswählen eines Versionsschemas
> * Hinzufügen der Version zu einem Produkt
> * Anzeigen der Version im Entwicklerportal

:::image type="content" source="media/api-management-getstarted-publish-versions/azure-portal.png" alt-text="Im Azure-Portal angezeigte Version":::

## <a name="prerequisites"></a>Voraussetzungen

+ Machen Sie sich mit der [Azure API Management-Terminologie](api-management-terminology.md) vertraut.
+ Bearbeiten Sie den folgenden Schnellstart: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)
+ Schließen Sie darüber hinaus das folgende Tutorial ab: [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md).

## <a name="add-a-new-version"></a>Hinzufügen einer neuen Version

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer API Management-Instanz.
1. Klicken Sie auf **APIs**.
1. Wählen Sie in der API-Liste **Demo Conference API** aus. 
1. Wählen Sie neben **Demo Conference API** das Kontextmenü ( **...** ) aus.
1. Wählen Sie **Version hinzufügen** aus.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version-menu.png" alt-text="API-Kontextmenü – Version hinzufügen":::


> [!TIP]
> Versionen können auch beim Erstellen einer neuen API aktiviert werden. Wählen Sie auf dem Bildschirm **API hinzufügen** die Option **Versionsverwaltung für diese API?** aus.

## <a name="choose-a-versioning-scheme"></a>Auswählen eines Schemas für die Versionsverwaltung

In Azure API Management legen Sie fest, wie Aufrufer die API-Version angeben, indem Sie ein *Schema für die Versionsverwaltung* auswählen: **Pfad, Header** oder **Abfragezeichenfolge**. Im folgenden Beispiel wird *Pfad* als Schema für die Versionsverwaltung verwendet.

Geben Sie die Werte aus der folgenden Tabelle ein. Wählen Sie anschließend **Erstellen** aus, um Ihre Version zu erstellen.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version.png" alt-text="Fenster zum Hinzufügen von Versionen":::



|Einstellung   |Wert  |Beschreibung  |
|---------|---------|---------|
|**Name**     |  *demo-conference-api-v1*       |  Eindeutiger Name in Ihrer API Management-Instanz<br/><br/>Da eine Version eigentlich eine neue API ist, die auf der [Revision](api-management-get-started-revise-api.md) einer API basiert, wird mit dieser Einstellung der Name der neuen API angegeben.   |
|**Versionsverwaltungsschema**     |  **Pfad**       |  Die Art und Weise, wie Aufrufer die API-Version angeben     |
|**Version identifer** (Versionsbezeichner)     |  *v1*       |  Schemaspezifischer Indikator der Version. Bei Auswahl von **Pfad** wird das Suffix für den API-URL-Pfad verwendet. <br/><br/> Geben Sie bei Auswahl von **Header** oder **Abfragezeichenfolge** einen zusätzlichen Wert an: den Namen des Headers oder Abfragezeichenfolgenparameters.<br/><br/> Ein Verwendungsbeispiel wird angezeigt.        |
|**Produkte**     |  **Unbegrenzt**       |  Optional ein oder mehrere Produkte, mit denen die API-Version verknüpft ist. Zur Veröffentlichung der API muss sie einem Produkt zugeordnet werden. Sie können [die Version einem Produkt auch später hinzufügen](#add-the-version-to-a-product).      |

Nach der Erstellung der Version wird sie nun in der API-Liste unter **Demo Conference API** angezeigt. Sie sehen jetzt zwei APIs: das **Original** und **v1**.

![Unter einer API aufgeführte Versionen im Azure-Portal](media/api-management-getstarted-publish-versions/version-list.png)

Nun können Sie **v1** als eine API bearbeiten und konfigurieren, die vom **Original** getrennt ist. Änderungen an einer Version haben keine Auswirkungen auf die andere.

> [!Note]
> Wenn Sie einer API ohne Versionsverwaltung eine Version hinzufügen, wird außerdem automatisch ein **Original** erstellt. Diese Version reagiert auf die Standard-URL. Durch die Erstellung einer Originalversion wird sichergestellt, dass alle vorhandenen Aufrufer durch das Hinzufügen einer Version nicht beeinträchtigt werden. Wenn Sie Versionen gleich bei der Erstellung einer neuen API aktivieren, wird kein Original erstellt.

## <a name="add-the-version-to-a-product"></a>Hinzufügen der Version zu einem Produkt

Damit Aufrufern die neue Version angezeigt wird, muss sie einem *Produkt* hinzugefügt werden. Wenn Sie einem Produkt die Version noch nicht bereits hinzugefügt haben, können Sie das jederzeit nachholen.

So fügen Sie beispielsweise dem Produkt **Unlimited** die Version hinzu:
1. Navigieren Sie im Azure-Portal zu Ihrer API Management-Instanz.
1. Wählen Sie **Produkte** > **Unlimited** > **APIs** >  **+ Hinzufügen** aus.
1. Wählen Sie **Demo Conference API** mit Version **v1** aus.
1. Klicken Sie auf **Auswählen**.

:::image type="content" source="media/api-management-getstarted-publish-versions/08-add-multiple-versions-03-add-version-product.png" alt-text="Hinzufügen einer Version zum Produkt":::

## <a name="use-version-sets"></a>Verwenden von Versionsgruppen

Beim Erstellen mehrerer Versionen wird vom Azure-Portal eine *Versionsgruppe* erstellt. Diese stellt eine Gruppe von Versionen für eine einzelne logische API dar. Wählen Sie den Namen einer API mit mehreren Versionen aus. Im Azure-Portal wird die zugehörige **Versionsgruppe** angezeigt. Sie können den **Namen** und die **Beschreibung** einer virtuellen Gruppe anpassen.

Mithilfe der Azure CLI können Sie direkt mit Versionsgruppen interagieren:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Führen Sie zum Anzeigen aller Versionsgruppen den Befehl [az apim api versionset list](/cli/azure/apim/api/versionset#az_apim_api_versionset_list) aus:

```azurecli
az apim api versionset list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Wird vom Azure-Portal eine Versionsgruppe für Sie erstellt, wird dabei ein alphanumerischer Name zugewiesen. Dieser wird in der Spalte **Name** der Liste angezeigt. Verwenden Sie diesen Namen in anderen Azure CLI-Befehlen.

Führen Sie den Befehl [az apim api versionset show](/cli/azure/apim/api/versionset#az_apim_api_versionset_show) aus, um Details zu einer Versionsgruppe anzuzeigen:

```azurecli
az apim api versionset show --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --version-set-id 00000000000000000000000
```

Weitere Informationen zu Versionsgruppen finden Sie unter [Versionen in Azure API Management](api-management-versions.md#how-versions-are-represented).

## <a name="browse-the-developer-portal-to-see-the-version"></a>Anzeigen der Version im Entwicklerportal

Wenn Sie das [Entwicklerportal](api-management-howto-developer-portal-customize.md) ausprobiert haben, können Sie die API-Versionen dort anzeigen.

1. Klicken Sie im oberen Menü auf **Entwicklerportal**.
2. Klicken Sie auf **APIs** und anschließend auf **Demo Conference API**.
3. Ein Dropdownmenü mit mehreren Versionen neben dem API-Namen sollte angezeigt werden.
4. Klicken Sie auf **v1**.
5. Beachten Sie die **Anforderungs-URL** des ersten Vorgangs in der Liste. Dort sehen Sie, dass der API-URL-Pfad **v1** enthält.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Hinzufügen einer neuen Version zu einer vorhandenen API
> * Auswählen eines Versionsschemas 
> * Hinzufügen der Version zu einem Produkt
> * Anzeigen der Version im Entwicklerportal

Fahren Sie mit dem nächsten Tutorial fort:

> [!div class="nextstepaction"]
> [Anpassen des Stils der Seiten im Entwicklerportal](api-management-howto-developer-portal-customize.md)
