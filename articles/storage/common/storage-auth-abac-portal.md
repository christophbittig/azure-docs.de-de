---
title: 'Tutorial: Hinzufügen einer Rollenzuweisungsbedingung zum Einschränken des Zugriffs auf Blobs über das Azure-Portal (Vorschau): Azure ABAC'
titleSuffix: Azure Storage
description: Hier erfahren Sie, wie Sie über das Azure-Portal und mithilfe von Azure ABAC (Attribute-Based Access Control, attributbasierte Zugriffssteuerung) eine Rollenzuweisungsbedingung hinzufügen, um den Zugriff auf Blobs einzuschränken.
services: storage
author: rolyon
ms.service: storage
ms.topic: tutorial
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 09/24/2021
ms.openlocfilehash: 0ab670b9ef6309b8db01f4a53f41c703dea69423
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092274"
---
# <a name="tutorial-add-a-role-assignment-condition-to-restrict-access-to-blobs-using-the-azure-portal-preview"></a>Tutorial: Hinzufügen einer Rollenzuweisungsbedingung zum Einschränken des Zugriffs auf Blobs über das Azure-Portal (Vorschau)

> [!IMPORTANT]
> Azure ABAC und Azure-Rollenzuweisungsbedingungen befinden sich derzeit in der Vorschauphase.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

In den meisten Fällen gewährt eine Rollenzuweisung die für Azure-Ressourcen benötigten Berechtigungen. Manchmal ist jedoch unter Umständen eine präzisere Zugriffssteuerung erforderlich, was durch Hinzufügen einer Rollenzuweisungsbedingung erreicht werden kann.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> - Hinzufügen einer Bedingung zu einer Rollenzuweisung
> - Einschränken des Zugriffs auf Blobs basierend auf einem Blobindextag

## <a name="prerequisites"></a>Voraussetzungen

Informationen zu den Voraussetzungen für das Hinzufügen oder Bearbeiten von Rollenzuweisungsbedingungen finden Sie unter [Voraussetzungen für Bedingungen](../../role-based-access-control/conditions-prerequisites.md).

## <a name="condition"></a>Bedingung

In diesem Tutorial wird der Zugriff auf Blobs mit einem bestimmten Tag eingeschränkt. Hierzu wird beispielsweise einer Rollenzuweisung eine Bedingung hinzugefügt, die dafür sorgt, dass von Chandra nur Dateien mit dem Tag „Project=Cascade“ gelesen werden können.

![Diagramm: Rollenzuweisung mit einer Bedingung](./media/shared/condition-role-assignment-rg.png)

Wenn Chandra versucht, ein Blob ohne das Tag „Project=Cascade“ zu lesen, wird der Zugriff verweigert.

![Diagramm: Lesezugriff auf Blobs mit dem Tag „Project=Cascade“](./media/shared/condition-access.png)

Im Code sieht die Bedingung wie folgt aus:

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}
        AND
        @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEqualsIgnoreCase 'Cascade'
    )
)
```

## <a name="step-1-create-a-user"></a>Schritt 1: Erstellen eines Benutzers

1. Melden Sie sich beim Azure-Portal als Besitzer eines Abonnements an.

1. Klicken Sie auf **Azure Active Directory**.

1. Erstellen Sie einen Benutzer, oder suchen Sie nach einem bereits vorhandenen Benutzer. In diesem Tutorial wird Chandra als Beispiel verwendet.

## <a name="step-2-set-up-storage"></a>Schritt 2: Einrichten des Speichers

1. Erstellen Sie ein Speicherkonto, das mit dem Blobindextag-Feature kompatibel ist. Das Feature befindet sich derzeit in der Public Preview-Phase. Weitere Informationen finden Sie unter [Regionale Verfügbarkeit und Unterstützung von Speicherkonten](../blobs/storage-manage-find-blobs.md#regional-availability-and-storage-account-support).

1. Erstellen Sie innerhalb des Speicherkontos einen neuen Container, und legen Sie die öffentliche Zugriffsebene auf **Privat (kein anonymer Zugriff)** fest.

1. Klicken Sie im Container auf **Hochladen**, um den Bereich „Blob hochladen“ zu öffnen.

1. Suchen Sie nach einer Textdatei zum Hochladen.

1. Klicken Sie auf **Erweitert**, um den Bereich zu erweitern.

1. Fügen Sie der Textdatei im Abschnitt **Blobindextags** das folgende Blobindextag hinzu.

    Falls der Abschnitt „Blobindextags“ nicht angezeigt wird und Sie Ihr Abonnement eben erst registriert haben, warten Sie ggf. einige Minuten, damit die Änderungen weitergegeben werden können. Weitere Informationen finden Sie unter [Verwenden von Blobindextags (Vorschau) zum Verwalten und Suchen von Daten in Azure Blob Storage](../blobs/storage-blob-index-how-to.md).

    > [!NOTE]
    > Von Blobs wird auch das Speichern beliebiger benutzerdefinierter Schlüssel-Wert-Metadaten unterstützt. Metadaten sind zwar mit Blobindextags vergleichbar, für Bedingungen müssen jedoch Blobindextags verwendet werden.

    | Schlüssel | Wert |
    | --- | --- |
    | Project  | Cascade |

   ![Screenshot: Bereich „Blob hochladen“ mit dem Abschnitt „Blogindextags“](./media/storage-auth-abac-portal/container-upload-blob.png)

1. Klicken Sie auf die Schaltfläche **Hochladen**, um die Datei hochzuladen.

1. Laden Sie eine zweite Textdatei hoch.

1. Fügen Sie der zweiten Textdatei das folgende Blobindextag hinzu:

    | Schlüssel | Wert |
    | --- | --- |
    | Project  | Baker |

## <a name="step-3-assign-a-storage-blob-data-role"></a>Schritt 3: Zuweisen einer Speicherblobdaten-Rolle

1. Öffnen Sie die Ressourcengruppe.

1. Klicken Sie auf **Zugriffssteuerung (IAM)** .

1. Klicken Sie auf die Registerkarte **Rollenzuweisungen**, um die Rollenzuweisungen für diesen Bereich anzuzeigen.

1. Klicken Sie auf **Hinzufügen** > **Rollenzuweisung hinzufügen (Vorschau)** .

   ![Screenshot: „Hinzufügen“ > „Rollenzuweisung hinzufügen (Vorschau)“](./media/storage-auth-abac-portal/add-role-assignment-menu-preview.png)

    Die Seite „Rollenzuweisung hinzufügen“ wird geöffnet.

1. Wählen Sie auf der Registerkarte **Rollen** die Rolle [Storage-Blobdatenleser](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) aus.

    ![Screenshot: Seite „Rollenzuweisung hinzufügen“ mit der Registerkarte „Rollen“](./media/storage-auth-abac-portal/roles.png)

1. Wählen Sie auf der Registerkarte **Mitglieder** den zuvor erstellten Benutzer aus.

   ![Screenshot: Seite „Rollenzuweisung hinzufügen“ mit der Registerkarte „Mitglieder“](./media/storage-auth-abac-portal/members.png)

1. (Optional) Geben Sie im Feld **Beschreibung** den Text **Read access to blobs with the tag Project=Cascade** (Lesezugriff auf Blobs mit dem Tag „Project=Cascade“) ein.

1. Klicken Sie auf **Weiter**.

## <a name="step-4-add-a-condition"></a>Schritt 4: Hinzufügen einer Bedingung

1. Klicken Sie auf der Registerkarte **Bedingungen (optional)** auf **Bedingung hinzufügen**.

    ![Screenshot: Seite „Bedingung für Rollenzuweisung hinzufügen“ für eine neue Bedingung](./media/storage-auth-abac-portal/condition-add-new.png)

    Die Seite „Bedingung für Rollenzuweisung hinzufügen“ wird angezeigt.

1. Klicken Sie im Abschnitt „Aktion hinzufügen“ auf **Aktionen auswählen**.

    Die Seite „Aktion auswählen“ wird angezeigt. Bei diesem Bereich handelt es sich um eine gefilterte Liste von Datenaktionen – basierend auf der Rollenzuweisung, die als Ziel Ihrer Bedingung verwendet wird.

    ![Screenshot: Bereich „Aktion auswählen“ mit ausgewählter Aktion](./media/storage-auth-abac-portal/condition-actions-select.png)

1. Klicken Sie unter „Read a blog“ (Blog lesen) auf **Read content from a blob with tag conditions** (Inhalt aus einem Blob mit Tagbedingungen lesen) und anschließend auf **Auswählen**.

1. Klicken Sie im Abschnitt „Ausdruck erstellen“ auf **Ausdruck hinzufügen**.

    Der Abschnitt „Ausdruck“ wird erweitert.

1. Legen Sie die folgenden Einstellungen für den Ausdruck fest:

    | Einstellung | Wert |
    | --- | --- |
    | Attributquelle | Resource |
    | attribute | Blobindextags [Werte in Schlüssel] |
    | Key | Project |
    | Betreiber | StringEqualsIgnoreCase |
    | Wert | Cascade |

    ![Screenshot: Abschnitt „Ausdruck erstellen“ für Blobindextags](./media/storage-auth-abac-portal/condition-expressions.png)

1. Scrollen Sie nach oben zu **Editor-Typ**, und klicken Sie auf **Code**.

    Die Bedingung wird als Code angezeigt. In diesem Code-Editor können Sie Änderungen an der Bedingung vornehmen. Durch Klicken auf **Visual** können Sie zum visuellen Editor zurückkehren.

    ![Screenshot: Im Code-Editor angezeigte Bedingung](./media/storage-auth-abac-portal/condition-code.png)

1. Klicken Sie auf **Speichern**, um die Bedingung hinzuzufügen und zur Seite „Rollenzuweisung hinzufügen“ zurückzukehren.

1. Klicken Sie auf **Weiter**.

1. Klicken Sie auf der Registerkarte **Review + assign** (Überprüfen und zuweisen) auf **Review + assign** (Überprüfen und zuweisen), um die Rolle mit einer Bedingung zuzuweisen.

    Nach einigen Augenblicken wird dem Sicherheitsprinzipal die Rolle für den Bereich zugewiesen.

    ![Screenshot: Rollenzuweisungsliste nach dem Zuweisen der Rolle](./media/storage-auth-abac-portal/rg-role-assignments-condition.png)

## <a name="step-5-test-the-condition"></a>Schritt 5: Testen der Bedingung

Zum Testen der Bedingung muss Azure PowerShell verwendet werden.

1. Öffnen Sie ein PowerShell-Fenster.

1. Verwenden Sie [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount), um sich als Chandra anzumelden.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Initialisieren Sie die folgenden Variablen mit den von Ihnen verwendeten Namen:

    ```azurepowershell
    $storageAccountName = "<storageAccountName>"
    $containerName = "<containerName>"
    $blobNameBaker = "<blobNameBaker>"
    $blobNameCascade = "<blobNameCascade>"
    ```

1. Erstellen Sie mithilfe von [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) einen spezifischen Kontext für den einfacheren Zugriff auf Ihr Speicherkonto.

    ```azurepowershell
    $bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
    ```

1. Versuchen Sie, mithilfe von [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob) die Datei für das Projekt „Baker“ zu lesen.

    ```azurepowershell
    Get-AzStorageBlob -Container $containerName -Blob $blobNameBaker -Context $bearerCtx 
    ```

    Im Anschluss sehen Sie ein Beispiel für die Ausgabe. Wie Sie sehen, können Sie die Datei aufgrund der hinzugefügten Bedingung **nicht** lesen.

    ```azurepowershell
    Get-AzStorageBlob: This request is not authorized to perform this operation using this permission. HTTP Status Code: 403 - HTTP Error Message: This request is not authorized to perform this operation using this permission.
    ErrorCode: AuthorizationPermissionMismatch
    ErrorMessage: This request is not authorized to perform this operation using this permission.
    RequestId: <requestId>
    Time: Sun, 13 Sep 2020 12:33:42 GMT
    ```

1. Lesen Sie die Datei für das Projekt „Cascade“.

    ```azurepowershell
    Get-AzStorageBlob -Container $containerName -Blob $blobNameCascade -Context $bearerCtx 
    ```

    Im Anschluss sehen Sie ein Beispiel für die Ausgabe. Wie Sie sehen, können Sie die Datei lesen, da sie über das Tag „Project=Cascade“ verfügt.

    ```azurepowershell
       AccountName: <storageAccountName>, ContainerName: <containerName>

    Name                 BlobType  Length          ContentType                    LastModified         AccessTier SnapshotT
                                                                                                                  ime
    ----                 --------  ------          -----------                    ------------         ---------- ---------
    CascadeFile.txt      BlockBlob 7               text/plain                     2021-04-24 05:35:24Z Hot
    ```

## <a name="step-6-clean-up-resources"></a>Schritt 6: Bereinigen der Ressourcen

1. Entfernen Sie die von Ihnen hinzugefügte Rollenzuweisung.

1. Löschen Sie das von Ihnen erstellte Testspeicherkonto.

1. Löschen Sie den von Ihnen erstellten Benutzer.

## <a name="next-steps"></a>Nächste Schritte

- [Exemplarische Azure-Rollenzuweisungsbedingungen (Vorschau)](storage-auth-abac-examples.md)
- [Aktionen und Attribute für Azure-Rollenzuweisungsbedingungen in Azure Storage (Vorschau)](storage-auth-abac-attributes.md)
- [Format und Syntax von Azure-Rollenzuweisungsbedingungen](../../role-based-access-control/conditions-format.md)
