---
title: Upgrade von Azure Blob Storage mit Azure Data Lake Storage Gen2-Funktionen  | Micosoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie Resource Manager Vorlagen verwenden, um ein Upgrade von Azure Blob Storage auf Data Lake Storage durchzuführen.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: normesta
ms.openlocfilehash: ecf0988069bf023225354b4ad864537737999094
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008706"
---
#  <a name="upgrade-azure-blob-storage-with-azure-data-lake-storage-gen2-capabilities"></a>Ein Upgrade von Azure Blob Storage mit Azure Data Lake Storage Gen2-Funktionen

Dieser Artikel hilft Ihnen, Funktionen wie Sicherheit auf Datei- und Verzeichnisebene und schnellere Vorgänge aufzuschließen. Diese Funktionen werden häufig von großen Datenanalyse-Workloads verwendet und werden als Azure Data Lake Storage Gen2 bezeichnet. 

Weitere Informationen zu diesen Funktionen und zur Bewertung der Auswirkungen dieses Upgrades auf die Workloads, Anwendungen, Kosten, Dienstintegrationen, Tools, Funktonen und Dokumentation finden Sie unter [Upgraden von Azure Blob Storage mit Azure Data Lake Storage Gen2-Funktionen](upgrade-to-data-lake-storage-gen2.md).

> [!IMPORTANT]
> Ein Upgrade ist eine unidirektionale Aktion. Nachdem Sie das Upgrade durchgeführt haben, gibt es keine Möglichkeit, Ihr Konto wiederherzustellen. Wir empfehlen Ihnen, das Upgrade in einer Nichtproduktionsumgebung zu testen.

## <a name="review-feature-support"></a>Überprüfen der Featureunterstützung

Ihr Konto ist möglicherweise für die Verwendung von Features konfiguriert, die in Data Lake Storage Gen2-fähigen Konten noch nicht unterstützt werden. Wenn Ihr Konto ein Feature verwendet, das noch nicht unterstützt wird, besteht das Upgrade den Validierungsschritt nicht. 

Informationen zum Identifizieren nicht unterstützter Features finden Sie im Artikel [Unterstützung von Blob-Storage-Funktionen in Azure Storage-Konten](storage-feature-support-in-storage-accounts.md). Wenn Sie eines dieser nicht unterstützten Features in Ihrem Konto verwenden, stellen Sie sicher, dass Sie es deaktivieren, bevor Sie mit dem Upgrade beginnen.

## <a name="perform-the-upgrade"></a>Durchführen des Upgrades

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Melden Sie sich zunächst beim [Azure-Portal](https://portal.azure.com/) an.

2. Suchen Sie nach Ihrem Speicherkonto, und zeigen Sie die Kontoübersicht an.

3. Wählen Sie **Data Lake Gen2-Migration** aus.

   Die Konfigurationsseite **Upgrade auf ein Speicherkonto mit Azure Data Lake Gen2-Funktionen** wird angezeigt.

   > [!div class="mx-imgBorder"]
   > ![Seite „Konfiguration“](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-page.png)

4. Erweitern Sie den Abschnitt **Schritt 1: Überprüfen von Kontoänderungen vor dem Upgrade** und klicken Sie auf **Änderungen überprüfen und zustimmen**.

5. Aktivieren Sie auf der Seite **Kontoänderungen überprüfen** das Kontrollkästchen, und klicken Sie dann auf **Änderungen zustimmen**.

6. Erweitern Sie den Abschnitt **Schritt 2: Überprüfen des Kontos vor dem Upgrade** und klicken Sie dann auf **Überprüfung starten**.

   Wenn bei der Überprüfung ein Fehler auftritt, wird auf der Seite ein Fehler angezeigt. In einigen Fällen wird ein Link **Fehler anzeigen** angezeigt. Wenn dieser Link angezeigt wird, wählen Sie ihn aus. 

   > [!div class="mx-imgBorder"]
   > ![Link Fehler anzeigen](./media/upgrade-to-data-lake-storage-gen2-how-to/validation-errors.png)

   Wählen Sie dann im Kontextmenü der Datei **error.json** die Option **Herunterladen** aus.

   > [!div class="mx-imgBorder"]
   > ![Seite: json-Fehler](./media/upgrade-to-data-lake-storage-gen2-how-to/error-json.png)

   Öffnen Sie die heruntergeladene Datei, um zu ermitteln, warum der Überprüfungsschritt für das Konto nicht erfolgreich war. 

   Der folgende JSON-Code gibt an, dass eine inkompatibles Funktion für das Konto aktiviert ist. In diesem Fall würden Sie die Funktion deaktivieren und dann den Überprüfungsprozess erneut starten.

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

7. Nachdem Ihr Konto erfolgreich überprüft wurde, erweitern Sie den Abschnitt **Schritt 3: Upgradekonto** und klicken Sie dann auf **Upgrade starten**.

   > [!IMPORTANT]
   > Schreibvorgänge sind während des Upgrades Ihres Kontos deaktiviert. Lesevorgänge sind nicht deaktiviert, aber wir empfehlen dringend, dass Sie Lesevorgänge anhalten, da diese den Upgrade-Prozess destabilisieren könnten.

   Wenn die Migration erfolgreich abgeschlossen wurde, wird eine Meldung ähnlich der folgenden angezeigt. 

   > [!div class="mx-imgBorder"]
   > ![Seite: Migration abgeschlossen](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-completed.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Öffnen Sie ein Windows PowerShell-Befehlsfenster.

2. Stellen Sie sicher, dass das neueste Azure PowerShell-Modul installiert ist. Weitere Informationen finden Sie unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-Az-ps).

3. Melden Sie sich mit dem Befehl `Connect-AzAccount` bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm.

   ```powershell
   Connect-AzAccount
   ```

4. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement fest.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

6. Überprüfen Sie Ihr Speicherkonto mit dem folgenden Befehl.

   ```powershell
   $result = Invoke-AzStorageAccountHierarchicalNamespaceUpgrade -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>" -RequestType Validation -AsJob
   ```

   - Ersetzen Sie den Platzhalterwert `<resource-group-name>` durch den Namen Ihrer Ressourcengruppe.

   - Ersetzen Sie den Platzhalterwert `<storage-account-name>` durch den Namen Ihres Speicherkontos.

   Je nach Größe Ihres Kontos kann dieser Vorgang einige Zeit in Anspruch nehmen. Sie können die Option `asJob` verwenden, um den Befehl in einem Hintergrundauftrag auszuführen, damit Ihr Client nicht blockiert wird. Der Befehl wird remote ausgeführt, aber der Auftrag befindet sich auf Ihrem lokalen Computer oder auf der VM, von der aus Sie den Befehl ausführen. Die Ergebnisse werden an Ihren lokalen Computer oder die VM übertragen.

7. Um den Status des Auftrags zu überprüfen und alle Eigenschaften des Auftrags in einer Liste anzuzeigen, geben Sie die Rückgabevariable an das Cmdlet `Format-List` weiter. 

   ```powershell
   $result | Format-List -Property *
   ```

   Wenn die Validierung erfolgreich ist, wird die Eigenschaft **State** auf **Completed** festgelegt.

   Wenn bei der Validierung ein Fehler auftritt, wird die Eigenschaft **State** auf **Failed** festgelegt, und die Eigenschaft **Error** zeigt Validierungsfehler an. 

   Die folgende Ausgabe zeigt, dass im Konto eine inkompatibles Funktion aktiviert ist. In diesem Fall würden Sie die Funktion deaktivieren und dann den Überprüfungsprozess erneut starten.

   > [!div class="mx-imgBorder"]
   > ![Validierungsfehler](./media/upgrade-to-data-lake-storage-gen2-how-to/validation-error-powershell.png)

   In einigen Fällen gibt die Eigenschaft **Error** einen Pfad zu einer Datei mit dem Namen **error.json** an. Sie können diese Datei öffnen, um zu ermitteln, warum der Validierungsschritt für das Konto nicht erfolgreich war. 

   Der folgende JSON-Code gibt an, dass eine inkompatibles Funktion für das Konto aktiviert ist. In diesem Fall würden Sie die Funktion deaktivieren und dann den Überprüfungsprozess erneut starten.

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

8. Nachdem Ihr Konto erfolgreich validiert wurde, starten Sie das Upgrade, indem Sie den folgenden Befehl ausführen.
   
   ```powershell
   $result = Invoke-AzStorageAccountHierarchicalNamespaceUpgrade -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>" -RequestType Upgrade -AsJob -Force
   ```

   Wie im obigen Validierungsbeispiel wird in diesem Beispiel die Option `asJob` verwendet, um den Befehl in einem Hintergrundauftrag auszuführen. Die Option `Force` überschreibt Eingabeaufforderungen zur Bestätigung des Upgrades.  Wenn Sie die Option `AsJob` nicht verwenden, müssen Sie auch die Option `Force` nicht verwenden, da Sie einfach auf die Eingabeaufforderungen reagieren können.

   > [!IMPORTANT]
   > Schreibvorgänge sind während des Upgrades Ihres Kontos deaktiviert. Lesevorgänge sind nicht deaktiviert, aber wir empfehlen dringend, dass Sie Lesevorgänge anhalten, da diese den Upgrade-Prozess destabilisieren könnten.

   Zum Überprüfen des Auftragsstatus verwenden Sie dieselben Techniken, die in den vorherigen Schritten beschrieben wurden. Während der Prozess ausgeführt wird, wird die Eigenschaft **State** auf **Running** festgelegt.

   Wenn die Migration erfolgreich abgeschlossen wurde, wird die Eigenschaft **State** auf **Completed** festgelegt, und die Eigenschaft **Error** zeigt keine Fehler an.
   
   > [!div class="mx-imgBorder"]
   > ![Ausgabe nach erfolgreichem Abschluss](./media/upgrade-to-data-lake-storage-gen2-how-to/success-message-powershell.png)


### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

1. Öffnen Sie zunächst [Azure Cloud Shell](../../cloud-shell/overview.md), oder falls Sie die Azure-CLI lokal [installiert](/cli/azure/install-azure-cli) haben, öffnen Sie eine Befehlskonsolenanwendung wie Windows PowerShell.

2. Überprüfen Sie mit dem folgenden Befehl, ob die installierte Version der Azure-Befehlszeilenschnittstelle `2.29.0` oder höher ist.

   ```azurecli
    az --version
   ```

   Wenn Ihre Version der Azure-Befehlszeilenschnittstelle niedriger ist als `2.29.0`, installieren Sie die neueste Version. Weitere Informationen finden Sie unter [Installieren der Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli).

2. Wenn Ihre Identität mehreren Abonnements zugeordnet ist, legen Sie das aktive Abonnement fest.

   ```azurecli
      az account set --subscription <subscription-id>
   ```

   Ersetzen Sie den Platzhalterwert `<subscription-id>` durch die ID Ihres Abonnements.

3. Überprüfen Sie Ihr Speicherkonto mit dem folgenden Befehl.

   ```azurecli
   az storage account hns-migration start --type validation -n <storage-account-name> -g <resource-group-name>
   ```

   - Ersetzen Sie den Platzhalterwert `<resource-group-name>` durch den Namen Ihrer Ressourcengruppe.

   - Ersetzen Sie den Platzhalterwert `<storage-account-name>` durch den Namen Ihres Speicherkontos.

   Wenn die Validierung erfolgreich ist, wird der Prozess abgeschlossen, und es werden keine Fehler angezeigt.
   
   Wenn die Validierung nicht erfolgreich ist, wird in der Konsole ein Validierungsfehler angezeigt. Der Fehler `(IncompatibleValuesForAccountProperties) Values for account properties are incompatible: Versioning Enabled` beispielsweise gibt an, dass ein inkompatibles Feature (Versionsverwaltung) im Konto aktiviert ist. In diesem Fall würden Sie die Funktion deaktivieren und dann den Überprüfungsprozess erneut starten.

   In einigen Fällen wird der Pfad zu einer Datei mit dem Namen **error.json** in der Konsole angezeigt. Sie können diese Datei öffnen, um zu ermitteln, warum der Validierungsschritt für das Konto nicht erfolgreich war. 

   Der folgende JSON-Code gibt an, dass eine inkompatibles Funktion für das Konto aktiviert ist. In diesem Fall würden Sie die Funktion deaktivieren und dann den Überprüfungsprozess erneut starten.

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

5. Nachdem Ihr Konto erfolgreich validiert wurde, starten Sie das Upgrade, indem Sie den folgenden Befehl ausführen.
   
   ```azurecli
   az storage account hns-migration start --type upgrade -n storage-account-name -g <resource-group-name>
   ```

   > [!IMPORTANT]
   > Schreibvorgänge sind während des Upgrades Ihres Kontos deaktiviert. Lesevorgänge sind nicht deaktiviert, aber wir empfehlen dringend, dass Sie Lesevorgänge anhalten, da diese den Upgrade-Prozess destabilisieren könnten.

   Wenn die Migration erfolgreich ist, wird der Prozess abgeschlossen, und es werden keine Fehler angezeigt.

---

## <a name="stop-the-upgrade"></a>Abbrechen des Upgrades

Sie können die Migration beenden, bevor sie abgeschlossen ist. Hier finden Sie Informationen dazu, was in diesem Fall geschieht.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Um das Upgrade zu beenden, bevor es abgeschlossen ist, wählen Sie **Upgrade abbrechen** aus, während das Upgrade ausgeführt wird.

> [!div class="mx-imgBorder"]
> ![Upgrade abbrechen](./media/upgrade-to-data-lake-storage-gen2-how-to/cancel-the-upgrade.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Um das Upgrade zu beenden, bevor es abgeschlossen ist, verwenden Sie den Befehl `Stop-AzStorageAccountHierarchicalNamespaceUpgrade`.

```powershell
Stop-AzStorageAccountHierarchicalNamespaceUpgrade -ResourceGroupName <resource-group-name> -Name <storage-account-name>
```

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Um das Upgrade zu beenden, bevor es abgeschlossen ist, verwenden Sie den Befehl `az storage account hns-migration stop`.

```azurecli
az storage account hns-migration stop -n <storage-account-name> -g <resource-group-name>
```

---


## <a name="migrate-data-workloads-and-applications"></a>Migrieren von Daten, Workloads und Anwendungen 

1. Konfigurieren Sie die [Dienste in Ihren Workloads](./data-lake-storage-supported-azure-services.md) so, dass sie entweder auf den **Blob-Dienstendpunkt** oder den **Data Lake Storage-Endpunkt** verweisen.

   > [!div class="mx-imgBorder"]
   > ![Kontoendpunkte](./media/upgrade-to-data-lake-storage-gen2-how-to/storage-endpoints.png)
  
3. Stellen Sie sicher, das Hadoop-Workloads, die den Windows Azure Storage Blob-Treiber oder den [WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html)-Treiber verwenden, so geändert werden, dass sie den [Azure Blob File System (ABFS)](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html)-Treiber verwenden. Anders als der WASB-Treiber, der Anforderungen an den Endpunkt des **Blob-Dienstes** stellt, stellt der ABFS-Treiber Anforderungen an den **Data Lake Storage**-Endpunkt Ihres Kontos.

2. Testen Sie die benutzerdefinierten Anwendungen, um sicherzustellen, dass sie wie erwartet mit Ihrem aktualisierten Konto funktionieren. 

   [Der Zugriff mit mehreren Protokollen auf Data Lake Storage](data-lake-storage-multi-protocol-access.md) ermöglicht es den meisten Anwendungen, Blob-APIs weiterhin ohne Änderungen zu verwenden. Wenn Probleme auftreten oder Sie APIs verwenden möchten, um mit Verzeichnisvorgängen und ACLs zu arbeiten, sollten Sie einen Teil Ihres Codes verschieben, um Data Lake Storage Gen2-APIs zu verwenden. Weitere Informationen finden Sie in den Anleitungen für [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md), [Node.js](data-lake-storage-acl-javascript.md) und [REST](/rest/api/storageservices/data-lake-storage-gen2). 

3. Testen Sie alle benutzerdefinierten Skripts, um sicherzustellen, dass sie wie erwartet mit Ihrem aktualisierten Konto funktionieren. 

   Wie bei Blob-APIs funktionieren viele Ihrer Skripts wahrscheinlich, ohne dass Sie sie ändern müssen. Aktualisieren Sie Skripts aber bei Bedarf für die Verwendung der [PowerShell-Cmdlets](data-lake-storage-directory-file-acl-powershell.md) und [Azure CLI-Befehle](data-lake-storage-directory-file-acl-cli.md) für Data Lake Storage Gen2.
 

## <a name="see-also"></a>Siehe auch

[Einführung in Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)