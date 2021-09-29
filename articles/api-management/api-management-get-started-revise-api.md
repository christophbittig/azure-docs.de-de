---
title: 'Tutorial: Gefahrloses Vornehmen geringfügiger API-Änderungen mithilfe von Revisionen in API Management'
titleSuffix: Azure API Management
description: In diesem Tutorial erfahren Sie Schritt für Schritt, wie Sie mithilfe von Revisionen geringfügige Änderungen in API Management vornehmen.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.custom: mvc, devx-track-azurecli
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: danlep
ms.openlocfilehash: 34ffc28e6c8e109252b68be269c1918beabfd5af
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676597"
---
# <a name="tutorial-use-revisions-to-make-non-breaking-api-changes-safely"></a>Tutorial: Gefahrloses Vornehmen geringfügiger API-Änderungen mithilfe von Revisionen
Wenn Ihre API einsatzbereit ist und von Entwicklern verwendet wird, müssen Sie letztendlich Änderungen an der API vornehmen und dabei darauf achten, keine Aufrufe Ihrer API zu beeinträchtigen. Außerdem empfiehlt es sich, Entwickler über die vorgenommenen Änderungen zu informieren. 

Nehmen Sie in Azure API Management mithilfe von *Revisionen* geringfügige API-Änderungen vor, um Änderungen sicher modellieren und testen zu können. Wenn Sie so weit sind, können Sie eine Revision als aktuelle Version festlegen und Ihre bisherige API ersetzen. 

Hintergrundinformationen finden Sie unter [Versions & Revisions](https://azure.microsoft.com/blog/versions-revisions/) (Versionen und Revisionen) sowie unter [API Versioning with Azure API Management](https://azure.microsoft.com/blog/api-versioning-with-azure-api-management/) (API-Versionsverwaltung mit Azure API Management).

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Hinzufügen einer neuen Revision
> * Vornehmen geringfügiger Änderungen an Ihrer Revision
> * Festlegen der Revision als aktuelle Revision und Hinzufügen eines Eintrags zum Änderungsprotokoll
> * Anzeigen von Änderungen und Änderungsprotokoll im Entwicklerportal

:::image type="content" source="media/api-management-getstarted-revise-api/azure-portal.png" alt-text="API-Revisionen im Azure-Portal":::

## <a name="prerequisites"></a>Voraussetzungen

+ Machen Sie sich mit der [Azure API Management-Terminologie](api-management-terminology.md) vertraut.
+ Bearbeiten Sie den folgenden Schnellstart: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)
+ Absolvieren Sie außerdem das folgende Tutorial: [Importieren und Veröffentlichen Ihrer ersten API](import-and-publish.md).

## <a name="add-a-new-revision"></a>Hinzufügen einer neuen Revision

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu Ihrer API Management-Instanz.
1. Klicken Sie auf **APIs**.
2. Wählen Sie in der API-Liste den Eintrag **Demo Conference API** aus (oder eine andere API, der Sie Revisionen hinzufügen möchten).
3. Wählen Sie die Registerkarte **Revisionen** aus.
4. Wählen Sie **+ Revision hinzufügen** aus.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-01-add-new-revision.png" alt-text="Hinzufügen der API-Revision":::

    > [!TIP]
    > **Revision hinzufügen** kann auch im Kontextmenü ( **...** ) der API ausgewählt werden.

5. Geben Sie eine Beschreibung für Ihre neue Revision an, damit Sie wissen, wofür sie verwendet wird.
6. Wählen Sie **Erstellen** aus.
7. Die neue Revision wird erstellt.

    > [!NOTE]
    > Ihre ursprüngliche API bleibt in **Revision 1**. Dies ist die Revision, die weiterhin von den Benutzern aufgerufen wird, bis Sie beschließen, eine andere Revision zur aktuellen Revision zu machen.

## <a name="make-non-breaking-changes-to-your-revision"></a>Vornehmen geringfügiger Änderungen an Ihrer Revision

1. Wählen Sie in der API-Liste **Demo Conference API** aus.
1. Klicken Sie im oberen Seitenbereich auf die Registerkarte **Entwurf**.
1. Beachten Sie, dass die **Revisionsauswahl** (direkt über der Registerkarte „Entwurf“) **Revision 2** als derzeit ausgewählt anzeigt.

    > [!TIP]
    > Mit der Revisionsauswahl können Sie zwischen Revisionen wechseln, die Sie bearbeiten möchten.
1. Klicken Sie auf **+ Vorgang hinzufügen**.
1. Legen Sie den neuen Vorgang auf **POST** und Name, Anzeigename und URL des Vorgangs auf **test** fest.
1. **Speichern** Sie Ihren neuen Vorgang.

   :::image type="content" source="media/api-management-getstarted-revise-api/07-add-revisions-02-make-changes.png" alt-text="Ändern der Revision":::
1. Sie haben nun eine Änderung an **Revision 2** vorgenommen. Kehren Sie mithilfe der **Revisionsauswahl** im oberen Seitenbereich zu **Revision 1** zurück.
1. Wie Sie sehen, ist der neue Vorgang in **Revision 1** nicht vorhanden. 

## <a name="make-your-revision-current-and-add-a-change-log-entry"></a>Festlegen der Revision als aktuelle Revision und Hinzufügen eines Eintrags zum Änderungsprotokoll

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Klicken Sie im oberen Bereich der Seite auf die Registerkarte **Revisionen**.
1. Öffnen Sie das Kontextmenü (**...**) für **Revision 2**.
1. Wählen Sie **Als aktuell festlegen** aus.
1. Aktivieren Sie das Kontrollkästchen **Post to Public Change log for this API** (In öffentlichem Änderungsprotokoll für diese API veröffentlichen), wenn Sie Hinweise zu dieser Änderung veröffentlichen möchten. Geben Sie eine Änderungsbeschreibung für Entwickler an, beispielsweise **Testrevisionen. Neuer Vorgang „Test“ hinzugefügt**).
1. **Revision 2** ist jetzt aktuell.

    :::image type="content" source="media/api-management-getstarted-revise-api/revisions-menu.png" alt-text="Menü „Revision“ im Fenster „Revisionen“":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

So beginnen Sie mit der Verwendung der Azure CLI:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Verwenden Sie dieses Verfahren, um ein Release zu erstellen und zu aktualisieren.

1. Führen Sie zum Anzeigen der API-IDs den Befehl [az apim api list](/cli/azure/apim/api#az_apim_api_list) aus:

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

   Bei der API-ID, die im nächsten Befehl verwendet werden soll, handelt es sich um den Wert `Name`. Die API-Revision befindet sich in der Spalte `ApiRevision`.

1. Führen Sie zum Erstellen des Release mit einem Versionshinweis den Befehl [az apim api release create](/cli/azure/apim/api/release#az_apim_api_release_create) aus:

   ```azurecli
   az apim api release create --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --api-revision 2 --service-name apim-hello-world \
       --notes 'Testing revisions. Added new "test" operation.'
   ```

   Die von Ihnen veröffentlichte Revision wird zur aktuellen Revision.

1. Verwenden Sie zum Anzeigen der Releases den Befehl [az apim api release list](/cli/azure/apim/api/release#az_apim_api_release_list):

   ```azurecli
   az apim api release list --resource-group apim-hello-word-resource-group \
       --api-id echo-api --service-name apim-hello-world --output table
   ```

   Die von Ihnen angegebenen Hinweise werden im Änderungsprotokoll angezeigt. Sie werden in der Ausgabe des vorherigen Befehls angezeigt.

1. Der Parameter `--notes` ist beim Erstellen eines Release optional. Sie können die Hinweise später mithilfe des Befehls [az apim api release update](/cli/azure/apim/api/release#az_apim_api_release_update) hinzufügen oder ändern:

   ```azurecli
   az apim api release update --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --release-id 00000000000000000000000000000000 \
       --service-name apim-hello-world --notes "Revised notes."
   ```

   Verwenden Sie den Wert in der Spalte `Name` als Release-ID.

Releases können mithilfe des Befehls [az apim api release delete ](/cli/azure/apim/api/release#az_apim_api_release_delete) entfernt werden:

```azurecli
az apim api release delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --release-id 00000000000000000000000000000000 
    --service-name apim-hello-world
```

---

## <a name="browse-the-developer-portal-to-see-changes-and-change-log"></a>Anzeigen von Änderungen und Änderungsprotokoll im Entwicklerportal

Wenn Sie das [Entwicklerportal](api-management-howto-developer-portal-customize.md) ausprobiert haben, können Sie die API-Änderungen und das Änderungsprotokoll dort überprüfen.

1. Klicken Sie im Azure-Portal auf **APIs**.
1. Wählen Sie im oberen Menü die Option **Entwicklerportal** aus.
1. Wählen Sie im Entwicklerportal die Option **APIs** und anschließend **Demo Conference API** aus.
1. Wie Sie sehen, ist Ihr neuer **test**-Vorgang nun verfügbar.
1. Wählen Sie in der Nähe des API-Namens die Option **Änderungsprotokoll** aus.
1. Der Änderungsprotokolleintrag ist in der Liste enthalten.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Hinzufügen einer neuen Revision
> * Vornehmen geringfügiger Änderungen an Ihrer Revision
> * Festlegen der Revision als aktuelle Revision und Hinzufügen eines Eintrags zum Änderungsprotokoll
> * Anzeigen von Änderungen und Änderungsprotokoll im Entwicklerportal

Fahren Sie mit dem nächsten Tutorial fort:

> [!div class="nextstepaction"]
> [Veröffentlichen mehrerer Versionen Ihrer API](api-management-get-started-publish-versions.md)
