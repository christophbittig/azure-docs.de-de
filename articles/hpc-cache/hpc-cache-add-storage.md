---
title: Hinzufügen von Speicher zu einem Azure HPC Cache
description: Definieren von Speicherzielen, damit Ihr Azure HPC Cache Ihr lokales NFS-System oder Azure-Blobcontainer für die langfristige Speicherung von Dateien verwenden kann
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/22/2021
ms.custom: subject-rbac-steps
ms.author: femila
ms.openlocfilehash: 29ecaecc579d1947d3e86ef6d9e080030a419331
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131070601"
---
# <a name="add-storage-targets"></a>Hinzufügen von Speicherzielen

*Speicherziele* sind Back-End-Speicher für Dateien, auf die über einen Azure HPC Cache zugegriffen wird. Sie können NFS-Speicher hinzufügen, z. B. ein lokales Hardwaresystem, oder Daten in Azure Blob Storage speichern.

Sie können 10 verschiedene Speicherziele für jeden Cache definieren, außerdem können größere Caches [bis zu 20 Speicherziele unterstützen](#size-your-cache-correctly-to-support-your-storage-targets).

Der Cache stellt alle Speicherziele in einem [aggregierten Namespace](hpc-cache-namespace.md) dar. Die Namespacepfade werden separat konfiguriert, nachdem Sie die Speicherziele hinzugefügt haben.

Beachten Sie, dass der Zugriff auf die Speicherexporte vom virtuellen Netzwerk Ihres Caches aus möglich sein muss. Für einen lokalen Hardwarespeicher müssen Sie möglicherweise einen DNS-Server einrichten, der Hostnamen für den NFS-Speicherzugriff auflösen kann. Weitere Informationen finden Sie unter [DNS-Zugriff](hpc-cache-prerequisites.md#dns-access).

Fügen Sie nach dem Erstellen des Caches Speicherziele hinzu. Folgen Sie diesem Prozess:

1. [Erstellen des Caches](hpc-cache-create.md)
1. Definieren eines Speicherziels (Informationen in diesem Artikel)
1. [Erstellen Sie die clientseitigen Pfade](add-namespace-paths.md) (für den [aggregierten Namespace](hpc-cache-namespace.md)).

Je nachdem, welche Art von Speicher verwendet wird, weist das Verfahren zum Hinzufügen eines Speicherziels geringfügige Unterschiede auf. Details zu beiden Szenarien finden Sie unten.

Klicken Sie auf das Bild unten, um eine [Videodemonstration](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/) der Cacheerstellung und des Hinzufügens eines Speicherziels im Azure-Portal anzusehen.

[![Videominiaturansicht: Azure HPC Cache: Setup (klicken Sie, um die Videoseite aufzurufen)](media/video-4-setup.png)](https://azure.microsoft.com/resources/videos/set-up-hpc-cache/)

## <a name="size-your-cache-correctly-to-support-your-storage-targets"></a>Korrekte Cachegröße zur Unterstützung Ihrer Speicherziele

Die Anzahl der unterstützten Speicherziele hängt von der Cachegröße ab, die beim Erstellen des Caches festgelegt wird. Die Cachekapazität ist eine Kombination aus Durchsatzkapazität (in GB/s) und Speicherkapazität (in TB).

* Bis zu 10 Speicherziele: Ein Standardcache mit dem kleinsten oder mittleren Cachespeicherwert für den ausgewählten Durchsatz kann maximal 10 Speicherziele haben.

  Wenn Sie beispielsweise einen Durchsatz von 2 GB/Sekunde und nicht die höchste Cachespeichergröße auswählen, unterstützt Ihr Cache maximal 10 Speicherziele.

* Bis zu 20 Speicherziele:

  * Alle Caches mit hohem Durchsatz (mit vorkonfigurierten Cachespeichergrößen) können bis zu 20 Speicherziele unterstützen.
  * Standardcaches können bis zu 20 Speicherziele unterstützen, wenn Sie die höchste verfügbare Cachegröße für den ausgewählten Durchsatzwert auswählen. (Wenn Sie Azure CLI verwenden, wählen Sie die höchste gültige Cachegröße für Ihre Cache-SKU aus.)

Weitere Informationen zu Durchsatz- und Cachegrößeneinstellungen finden Sie unter [Festlegen der Cachekapazität](hpc-cache-create.md#set-cache-capacity).

## <a name="choose-the-correct-storage-target-type"></a>Auswählen des richtigen Speicherzieltyps

Sie können zwischen drei Speicherzieltypen wählen: **NFS**, **Blob** und **ADLS-NFS**. Wählen Sie den Typ aus, der dem Speichersystemtyp entspricht, mit dem Sie Ihre Dateien während dieses HPC Cache-Projekts speichern.

* **NFS**: Erstellen Sie ein NFS-Speicherziel für den Zugriff auf Daten in einem NAS-System (Network Attached Storage). Dies kann ein lokales Speichersystem oder ein anderer Speichertyp sein, auf den mit NFS zugegriffen werden kann.

  * Anforderungen: [NFS-Speicheranforderungen](hpc-cache-prerequisites.md#nfs-storage-requirements)
  * Anweisungen: [Hinzufügen eines neuen NFS-Speicherziels](#add-a-new-nfs-storage-target)

* **Blob**: Verwenden Sie ein Blobspeicherziel, um Ihre Arbeitsdateien in einem neuen Azure-Blobcontainer zu speichern. Nur von einer Azure HPC Cache-Instanz aus kann aus diesem Container gelesen oder in ihn hineingeschrieben werden.

  * Voraussetzungen: [Blobspeicheranforderungen](hpc-cache-prerequisites.md#blob-storage-requirements)
  * Anweisungen: [Hinzufügen eines neuen Azure Blobspeicherziels](#add-a-new-azure-blob-storage-target)

* **ADLS-NFS**: Das ADLS-NFS-Speicherziel hat Zugriff auf Daten aus einem [Blobcontainer mit NFS-Unterstützung](../storage/blobs/network-file-system-protocol-support.md). Sie können den Container mit NFS-Standardbefehlen vorab laden, und die Dateien können später mit NFS gelesen werden.

  * Voraussetzungen: [ADLS-NFS-Speicheranforderungen](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements)
  * Anweisungen: [Hinzufügen eines neuen ADLS-NFS-Speicherziels](#add-a-new-adls-nfs-storage-target)

## <a name="add-a-new-azure-blob-storage-target"></a>Hinzufügen eines neuen Azure Blobspeicherziels

Für ein neues Blobspeicherziel ist ein leerer Blobcontainer oder ein Container erforderlich, der mit Daten im Format des Azure HPC Cache-Clouddateisystems aufgefüllt ist. Weitere Informationen zum Vorabladen eines Blobcontainers finden Sie unter [Daten in Azure-Blobspeicher verschieben](hpc-cache-ingest.md).

Im Azure-Portal finden Sie auf der Seite **Speicherziel hinzufügen** die Option, mit der Sie einen neuen Blobcontainer direkt vor dem Hinzufügen erstellen können.

> [!NOTE]
>
> * Verwenden Sie für in NFS eingebundenen Blobspeicher den Typ [ADLS-NFS storage target](#add-a-new-adls-nfs-storage-target) (ADLS-NFS-Speicherziel).
> * [Cachekonfigurationen mit hohem Durchsatz](hpc-cache-create.md#choose-the-cache-type-for-your-needs) unterstützen keine standardmäßigen Azure Blobspeicherziele. Verwenden Sie stattdessen Blobspeicher mit NFS-Unterstützung (ADLS-NFS).

### <a name="portal"></a>[Portal](#tab/azure-portal)

Öffnen Sie im Azure-Portal Ihre Cache-Instanz, und klicken Sie in der linken Seitenleiste auf **Speicherziele**.

![Screenshot der Seite „Einstellungen > Speicherziel“ mit zwei vorhandenen Speicherzielen in einer Tabelle und einer Hervorhebung um die Schaltfläche „+ Speicherziel hinzufügen“ oberhalb der Tabelle.](media/add-storage-target-button.png)

Auf der Seite **Speicherziele** werden alle vorhandenen Ziele und ein Link zum Hinzufügen eines neuen Ziels aufgelistet.

Klicken Sie auf die Schaltfläche **Speicherziel hinzufügen**.

![Screenshot der Seite „Speicherziel hinzufügen“, aufgefüllt mit Informationen für ein neues Azure-Blobspeicherziel](media/hpc-cache-add-blob.png)

Geben Sie diese Informationen ein, um einen Azure Blobcontainer zu definieren.

* **Name des Speicherziels**: Legen Sie einen Namen fest, der dieses Speicherziel im Azure HPC Cache identifiziert.
* **Zieltyp**: Wählen Sie **Blob** aus.
* **Speicherkonto**: Wählen Sie das Speicherkonto aus, das Sie verwenden möchten.

  Sie müssen die Cache-Instanz für den Zugriff auf das Speicherkonto autorisieren, wie unter [Hinzufügen der Zugriffsrollen](#add-the-access-control-roles-to-your-account) beschrieben.

  Informationen zur Art des Speicherkontos, das Sie verwenden können, finden Sie unter [Blobspeicheranforderungen](hpc-cache-prerequisites.md#blob-storage-requirements).

* **Speichercontainer**: Wählen Sie den Blobcontainer für dieses Ziel aus, oder klicken Sie auf **Neu erstellen**.

  ![Screenshot des Dialogfelds zum Angeben von Name und Zugriffsebene (privat) für neuen Container](media/add-blob-new-container.png)

Klicken Sie abschließend auf **OK**, um das Speicherziel hinzuzufügen.

> [!NOTE]
> Wenn Ihre Speicherkontofirewall so festgelegt ist, dass der Zugriff nur auf „ausgewählte Netzwerke“ eingeschränkt wird, verwenden Sie die temporäre Problemumgehung, die unter [Problemumgehung für die Firewalleinstellungen von Blobspeicherkonten](hpc-cache-blob-firewall-fix.md) dokumentiert ist.

### <a name="add-the-access-control-roles-to-your-account"></a>Hinzufügen der Zugriffssteuerungsrollen zu Ihrem Konto

Azure HPC Cache verwendet die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../role-based-access-control/index.yml), um den Cachedienst für den Zugriff auf Ihr Speicherkonto für Azure-Blobspeicherziele zu autorisieren.

Der Besitzer des Speicherkontos muss die Rollen [Speicherkontomitwirkender](../role-based-access-control/built-in-roles.md#storage-account-contributor) und [Mitwirkender an Storage-Blobdaten](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) für den Benutzer „HPC Cache-Ressourcenanbieter“ explizit hinzufügen.

Sie können dies im Voraus erledigen, oder indem Sie auf einen Link auf der Portalseite klicken, auf der Sie ein Blobspeicherziel hinzufügen. Beachten Sie, dass es bis zu fünf Minuten dauern kann, bis die Rolleneinstellungen in der Azure-Umgebung weitergegeben werden. Daher sollten Sie nach dem Hinzufügen der Rollen einige Minuten warten, bevor Sie ein Speicherziel erstellen.

1. Öffnen Sie **Zugriffssteuerung (IAM)** für Ihr Speicherkonto.

1. Wählen Sie **Hinzufügen** > **Rollenzuweisung hinzufügen** aus, um den Bereich „Rollenzuweisung hinzufügen“ zu öffnen.

1. Weisen Sie die folgenden Rollen nacheinander zu. Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).
    
    | Einstellung | Wert |
    | --- | --- |
    | Rollen | [Mitwirkender von Speicherkonto](../role-based-access-control/built-in-roles.md#storage-account-contributor) <br/>  [Mitwirkender an Speicherblobdaten](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) |
    | Zugriff zuweisen zu | HPC Cache-Ressourcenanbieter |

    ![Seite „Rollenzuweisung hinzufügen“](../../includes/role-based-access-control/media/add-role-assignment-page.png)

   > [!NOTE]
   > Wenn Sie den HPC Cache-Ressourcenanbieter nicht finden können, suchen Sie stattdessen nach der Zeichenfolge „storagecache“. Dies war vor GA ein Name für den Dienstprinzipal.

<!-- 
Steps to add the Azure roles:

1. Open the **Access control (IAM)** page for the storage account. (The link in the **Add storage target** page automatically opens this page for the selected account.)

1. Click the **+** at the top of the page and choose **Add a role assignment**.

1. Select the role "Storage Account Contributor" from the list.

1. In the **Assign access to** field, leave the default value selected ("Azure AD user, group, or service principal").  

1. In the **Select** field, search for "hpc".  This string should match one service principal, named "HPC Cache Resource Provider". Click that principal to select it.

   > [!NOTE]
   > If a search for "hpc" doesn't work, try using the string "storagecache" instead. Users who participated in previews (before GA) might need to use the older name for the service principal.

1. Click the **Save** button at the bottom.

1. Repeat this process to assign the role "Storage Blob Data Contributor".  

![screenshot of add role assignment GUI](media/hpc-cache-add-role.png) -->

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

### <a name="prerequisite-storage-account-access"></a>Voraussetzung: Zugriff auf das Speicherkonto

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Vergewissern Sie sich vor dem Hinzufügen eines Blobspeicherziels, dass der Cache über die richtigen Rollen für den Zugriff auf das Speicherkonto verfügt und die Firewalleinstellungen die Erstellung des Speicherziels zulassen.

Azure HPC Cache verwendet die [rollenbasierte Zugriffssteuerung in Azure (Azure Role-Based Access Control, Azure RBAC)](../role-based-access-control/index.yml), um den Cachedienst für den Zugriff auf Ihr Speicherkonto für Azure-Blobspeicherziele zu autorisieren.

Der Besitzer des Speicherkontos muss die Rollen [Speicherkontomitwirkender](../role-based-access-control/built-in-roles.md#storage-account-contributor) und [Mitwirkender an Storage-Blobdaten](../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) für den Benutzer „HPC Cache-Ressourcenanbieter“ explizit hinzufügen.

Wenn der Cache nicht über diese Rollen verfügt, kann das Speicherziel nicht erstellt werden.

Es kann bis zu fünf Minuten dauern, bis die Rolleneinstellungen in der Azure-Umgebung weitergegeben werden. Daher sollten Sie nach dem Hinzufügen der Rollen einige Minuten warten, bevor Sie ein Speicherziel erstellen.

Ausführliche Anweisungen finden Sie unter [Hinzufügen oder Entfernen von Azure-Rollenzuweisungen mithilfe der Azure-Befehlszeilenschnittstelle](../role-based-access-control/role-assignments-cli.md).

Überprüfen Sie auch die Firewalleinstellungen Ihres Speicherkontos. Wenn in den Firewalleinstellungen der Zugriff nur auf „ausgewählte Netzwerke“ beschränkt ist, kann das Speicherziel möglicherweise nicht erstellt werden. Verwenden Sie die im Artikel [Umgehen der Firewalleinstellungen für Blobspeicherkonten](hpc-cache-blob-firewall-fix.md) beschriebene Problemumgehung.

### <a name="add-a-blob-storage-target-with-azure-cli"></a>Hinzufügen eines Blobspeicherziels mit der Azure-Befehlszeilenschnittstelle

Verwenden Sie den Schnittstellenbefehl [az hpc-cache blob-storage-target add](/cli/azure/hpc-cache/blob-storage-target#az_hpc_cache_blob_storage_target_add), um ein Azure-Blobspeicherziel zu definieren.

> [!NOTE]
> Für die Azure CLI-Befehle ist es derzeit erforderlich, dass Sie beim Hinzufügen eines Speicherziels einen Namespacepfad hinzufügen. Dies unterscheidet sich von dem Prozess, der mit der Azure-Portalschnittstelle verwendet wird.

Zusätzlich zu den Standardparametern für die Ressourcengruppe und den Cachenamen müssen Sie die folgenden Optionen für das Speicherziel angeben:

* ``--name``: Legen Sie einen Namen fest, der dieses Speicherziel im Azure HPC Cache identifiziert.

* ``--storage-account``: Geben Sie den Kontobezeichner im folgenden Format an: /subscriptions/ *<Abonnement-ID>* /resourceGroups/ *<Speicherressourcengruppe>* /providers/Microsoft.Storage/storageAccounts/ *<Kontoname>*

  Informationen zur Art des Speicherkontos, das Sie verwenden können, finden Sie unter [Blobspeicheranforderungen](hpc-cache-prerequisites.md#blob-storage-requirements).

* ``--container-name``: Geben Sie den Namen des Containers an, der für dieses Speicherziel verwendet werden soll.

* ``--virtual-namespace-path``: Legen Sie den clientseitigen Dateipfad für dieses Speicherziel fest. Setzen Sie Pfadnamen in Anführungszeichen. Weitere Informationen zum Feature des virtuellen Namespace finden Sie unter [Planen des aggregierten Namespace](hpc-cache-namespace.md).

Beispielbefehl:

```azurecli
az hpc-cache blob-storage-target add --resource-group "hpc-cache-group" \
    --cache-name "cache1" --name "blob-target1" \
    --storage-account "/subscriptions/<subscriptionID>/resourceGroups/myrgname/providers/Microsoft.Storage/storageAccounts/myaccountname" \
    --container-name "container1" --virtual-namespace-path "/blob1"
```

---

## <a name="add-a-new-nfs-storage-target"></a>Hinzufügen eines neuen NFS-Speicherziels

Ein NFS-Speicherziel verfügt über andere Einstellungen als ein Blobspeicherziel, einschließlich einer Nutzungsmodelleinstellung, die dem Cache mitteilt, wie Daten aus diesem Speichersystem gespeichert werden.

![Screenshot der Seite zum Hinzufügen von Speicherzielen mit definiertem NFS-Ziel](media/add-nfs-target.png)

> [!NOTE]
> Stellen Sie vor dem Erstellen eines NFS-Speicherziels sicher, dass das Speichersystem über den Azure HPC Cache zugänglich ist und die Berechtigungsanforderungen erfüllt. Das Erstellen des Speicherziels schlägt fehl, wenn der Cache auf das Speichersystem nicht zugreifen kann. Einzelheiten dazu finden Sie in [NFS-Speicheranforderungen](hpc-cache-prerequisites.md#nfs-storage-requirements) und [Behandeln von Problemen mit der NAS-Konfiguration und dem NFS-Speicherziel](troubleshoot-nas.md).

### <a name="choose-a-usage-model"></a>Auswählen eines Nutzungsmodells

Wenn Sie ein Speicherziel erstellen, das NFS zum Erreichen des Speicherziels verwendet, müssen Sie ein Nutzungsmodell für dieses Ziel auswählen. Dieses Modell bestimmt, wie Ihre Daten zwischengespeichert werden.

Weitere Informationen zu diesen Einstellungen finden Sie unter [Grundlegendes zu Nutzungsmodellen](cache-usage-models.md).

Mit den integrierten HPC Cache-Nutzungsmodellen können Sie zwischen kurzen Reaktionszeiten und dem Risiko veralteter Daten abwägen. Wenn Sie die Geschwindigkeit von Dateilesevorgängen optimieren möchten, spielt es für Sie möglicherweise keine Rolle, ob die Dateien im Cache mit den Back-End-Dateien abgeglichen werden. Wenn Sie jedoch sicherstellen möchten, dass Ihre Dateien immer auf dem neuesten Stand sind und den Dateien im Remotespeicher entsprechen, wählen Sie ein Modell aus, bei dem regelmäßig eine Überprüfung durchgeführt wird.

> [!NOTE]
> [Caches mit hohem Durchsatz](hpc-cache-create.md#choose-the-cache-type-for-your-needs) unterstützen nur Cachelesevorgänge.

Die folgenden drei Optionen decken die meisten Situationen ab:

* **Read heavy, infrequent writes** (Leseintensiv, unregelmäßige Schreibvorgänge): Der Lesezugriff auf Dateien wird beschleunigt, die statisch sind oder selten geändert werden.

  Mit dieser Option werden Dateien aus Lesevorgängen des Clients zwischengespeichert, aber Schreibvorgänge des Clients werden sofort an den Back-End-Speicher übergeben. Im Cache gespeicherte Dateien werden nicht automatisch mit den Dateien auf dem NFS-Speichervolume verglichen.

  Verwenden Sie diese Option nicht, wenn das Risiko besteht, dass eine Datei direkt im Speichersystem geändert werden kann, ohne sie zuvor in den Cache zu schreiben. In diesem Fall ist die zwischengespeicherte Version der Datei nicht mit der Back-End-Datei synchron.

* **Mehr als 15 % Schreibvorgänge**: Diese Option beschleunigt die Lese-und Schreibleistung.

  Die Lese- und Schreibvorgänge des Clients werden zwischengespeichert. Es wird davon ausgegangen, dass Dateien im Cache neuer als die Dateien im Back-End-Speichersystem sind. Zwischengespeicherte Dateien werden nur alle acht Stunden mit den Dateien im Back-End-Speicher verglichen. Geänderte Dateien im Cache werden in das Back-End-Speichersystem geschrieben, nachdem sie sich 20 Minuten lang ohne zusätzliche Änderungen im Cache befunden haben.

  Verwenden Sie diese Option nicht, wenn Clients das Back-End-Speichervolume direkt einbinden, da dies zu veralteten Dateien führen kann.

* **Clients umgehen den Cache und schreiben in das NFS-Ziel**: Wählen Sie diese Option aus, wenn Clients in Ihrem Workflow Daten direkt in das Speichersystem schreiben, ohne zuvor in den Cache zu schreiben, oder wenn Sie die Datenkonsistenz verbessern möchten.

  Die von Clients angeforderten Dateien werden zwischengespeichert, aber alle Änderungen an diesen Dateien vom Client werden sofort an das Back-End-Speichersystem übergeben. Die Dateien im Cache werden regelmäßig anhand der Back-End-Versionen auf Änderungen überprüft. Durch diese Überprüfung wird die Datenkonsistenz bewahrt, wenn Dateien direkt oder im Speichersystem geändert werden, anstatt über den Cache.

Weitere Informationen zu den anderen Optionen finden Sie unter [Grundlegendes zu Nutzungsmodellen](cache-usage-models.md).

In dieser Tabelle werden die Unterschiede zwischen allen Nutzungsmodellen zusammengefasst:

[!INCLUDE [usage-models-table.md](includes/usage-models-table.md)]

> [!NOTE]
> Der Wert **Back-End-Überprüfung** wird angezeigt, wenn der Cache Dateien automatisch mit Quelldateien im Remotespeicher vergleicht. Sie können jedoch einen Vergleich auslösen, indem Sie eine Clientanforderung senden, die einen readdirplus-Vorgang für das Back-End-Speichersystem enthält. Readdirplus ist eine standardmäßige NFS-API (erweiterter Lesevorgang), die Verzeichnismetadaten zurückgibt. Durch diesen Vorgang wird erzwungen, dass der Cache Dateien vergleicht und aktualisiert.

### <a name="create-an-nfs-storage-target"></a>Erstellen eines NFS-Speicherziels

### <a name="portal"></a>[Portal](#tab/azure-portal)

Öffnen Sie im Azure-Portal Ihre Cache-Instanz, und klicken Sie in der linken Seitenleiste auf **Speicherziele**.

![Screenshot der Seite „Einstellungen > Speicherziel“ mit zwei vorhandenen Speicherzielen in einer Tabelle und einer Hervorhebung um die Schaltfläche „+ Speicherziel hinzufügen“ oberhalb der Tabelle.](media/add-storage-target-button.png)

Auf der Seite **Speicherziele** werden alle vorhandenen Ziele und ein Link zum Hinzufügen eines neuen Ziels aufgelistet.

Klicken Sie auf die Schaltfläche **Speicherziel hinzufügen**.

![Screenshot der Seite zum Hinzufügen von Speicherzielen mit definiertem NFS-Ziel](media/add-nfs-target.png)

Geben Sie diese Informationen für ein NFS-gestütztes Speicherziel an:

* **Name des Speicherziels**: Legen Sie einen Namen fest, der dieses Speicherziel im Azure HPC Cache identifiziert.

* **Zieltyp**: Wählen Sie **NFS** aus.

* **Hostname**: Geben Sie die IP-Adresse oder den vollqualifizierten Domänennamen Ihres NFS-Speichersystems ein. (Verwenden Sie nur dann einen Domänennamen, wenn Ihr Cache Zugriff auf einen DNS-Server hat, der den Namen auflösen kann.) Sie können mehrere IP-Adressen eingeben, wenn mehrere IP-Adressen auf Ihr Speichersystem verweisen.

* **Nutzungsmodell**: Wählen Sie auf der Grundlage Ihres Workflows eines der oben im Abschnitt [Auswählen eines Nutzungsmodells](#choose-a-usage-model) beschriebenen Datencacheprofile aus.

Klicken Sie abschließend auf **OK**, um das Speicherziel hinzuzufügen.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Verwenden Sie den Azure CLI-Befehl [az hpc-cache nfs-storage-target add](/cli/azure/hpc-cache/nfs-storage-target#az_hpc_cache_nfs_storage_target_add), um das Speicherziel zu erstellen.

> [!NOTE]
> Für die Azure CLI-Befehle ist es derzeit erforderlich, dass Sie beim Hinzufügen eines Speicherziels einen Namespacepfad hinzufügen. Dies unterscheidet sich von dem Prozess, der mit der Azure-Portalschnittstelle verwendet wird.

Geben Sie zusätzlich zum Cachenamen und zur Cacheressourcengruppe die folgenden Werte an:

* ``--name``: Legen Sie einen Namen fest, der dieses Speicherziel im Azure HPC Cache identifiziert.
* ``--nfs3-target``: Die IP-Adresse Ihres NFS-Speichersystems. (Sie können hier einen vollqualifizierten Domänennamen verwenden, wenn Ihr Cache Zugriff auf einen DNS-Server hat, der den Namen auflösen kann.)
* ``--nfs3-usage-model``: Geben Sie eines der oben im Abschnitt [Auswählen eines Nutzungsmodells](#choose-a-usage-model) beschriebenen Datencacheprofile an.

  Überprüfen Sie die Namen der Nutzungsmodelle mithilfe des Befehls [az hpc-cache usage-model list](/cli/azure/hpc-cache/usage-model#az_hpc_cache_usage_model_list).

* ``--junction``: Der „junction“-Parameter verknüpft den clientseitigen virtuellen Dateipfad mit einem Exportpfad auf dem Speichersystem.

  Ein NFS-Speicherziel kann mehrere virtuelle Pfade aufweisen, solange jeder Pfad ein anderes Export- oder Unterverzeichnis in demselben Speichersystem darstellt. Erstellen Sie alle Pfade für ein Speicherziel auf einem Speichersystem.

  Sie können [Namespacepfade](add-namespace-paths.md) für ein Speicherziel jederzeit hinzufügen und bearbeiten.

  Der Parameter ``--junction`` verwendet diese Werte:

  * ``namespace-path``: Der clientseitige virtuelle Dateipfad.
  * ``nfs-export``: Der Speichersystemexport, der dem clientseitigen Pfad zugeordnet werden soll.
  * ``target-path`` (optional): Ein Unterverzeichnis des Exports, falls erforderlich.

  Beispiel: ``--junction namespace-path="/nas-1" nfs-export="/datadisk1" target-path="/test"``

  Weitere Informationen zum Feature virtueller Namespace finden Sie unter [Aggregierten Namespace konfigurieren](hpc-cache-namespace.md).

Beispielbefehl:

```azurecli

az hpc-cache nfs-storage-target add --resource-group "hpc-cache-group" --cache-name "doc-cache0629" \
    --name nfsd1 --nfs3-target 10.0.0.4 --nfs3-usage-model WRITE_WORKLOAD_15 \
    --junction namespace-path="/nfs1/data1" nfs-export="/datadisk1" target-path=""
```

Ausgabe:
```azurecli

{- Finished ..
  "clfs": null,
  "id": "/subscriptions/<subscriptionID>/resourceGroups/hpc-cache-group/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "hpc-cache-group",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}

```

---

## <a name="add-a-new-adls-nfs-storage-target"></a>Hinzufügen eines neuen ADLS-NFS-Speicherziels

ADLS-NFS-Speicherziele verwenden Azure-Blobcontainer, die das NFS 3.0-Protokoll (Network File System, Netzwerkdateisystem) unterstützen.

Weitere Informationen zu diesem Feature finden Sie unter [NFS 3.0-Protokollunterstützung](../storage/blobs/network-file-system-protocol-support.md).

ADLS-NFS-Speicherziele weisen einige Ähnlichkeiten mit Blobspeicherzielen und NFS-Speicherzielen auf. Zum Beispiel:

* Ähnlich wie bei einem Blobspeicherziel müssen Sie Azure HPC Cache die Berechtigung erteilen, um [auf Ihr Speicherkonto zuzugreifen](#add-the-access-control-roles-to-your-account).
* Wie bei einem NFS-Speicherziel müssen Sie ein [Nutzungsmodell](#choose-a-usage-model) für den Cache festlegen.
* Da NFS-fähige Blobcontainer über eine mit NFS kompatible hierarchische Struktur verfügen, müssen Sie den Cache nicht zum Erfassen von Daten verwenden, und die Container können von anderen NFS-Systemen gelesen werden.

  Sie können Daten vorab in einen ADLS-NFS-Container laden, diesen dann als Speicherziel zu einem HPC-Cache hinzufügen, und später außerhalb eines HPC-Caches auf diese Daten zugreifen. Wenn Sie einen Standard-Blobcontainer als Speicherziel für einen HPC-Cache verwenden, werden die Daten in einem geschützten Format geschrieben, und Sie können nur mit anderen mit Azure HPC Cache kompatiblen Produkten auf diese Daten zugreifen.

Bevor Sie ein ADLS-NFS-Speicherziel erstellen können, müssen Sie ein NFS-fähiges Speicherkonto erstellen. Führen Sie die Schritte unter [Voraussetzungen für Azure HPC Cache](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements) und die Anweisungen unter [Einbinden eines Blobspeichers mit einem NFS](../storage/blobs/network-file-system-protocol-support-how-to.md) aus. Wenn Sie nicht dasselbe virtuelle Netzwerk für Cache und Speicherkonto verwenden, stellen Sie sicher, dass das VNET des Caches auf das VNET des Speicherkontos zugreifen kann.

Nachdem Sie Ihr Speicherkonto eingerichtet haben, können Sie einen neuen Container erstellen, wenn Sie das Speicherziel erstellen.

Weitere Informationen zu dieser Konfiguration finden Sie unter [Verwenden von in NFS eingebundenen Blobspeicher mit Azure HPC Cache](nfs-blob-considerations.md).

Öffnen Sie die Seite **Speicherziel hinzufügen** im Azure-Portal, um ein ADLS-NFS-Speicherziel zu erstellen. (Zusätzliche Methoden befinden sich in der Entwicklung.)

![Screenshot: Seite „Speicherziel hinzufügen“ mit definiertem ADLS-NFS-Ziel](media/add-adls-target.png)

Geben Sie die folgenden Informationen ein.

* **Name des Speicherziels**: Legen Sie einen Namen fest, der dieses Speicherziel im Azure HPC Cache identifiziert.
* **Zieltyp:** Wählen Sie **ADLS-NFS** aus.
* **Speicherkonto**: Wählen Sie das Speicherkonto aus, das Sie verwenden möchten. Wenn Ihr NFS-fähiges Speicherkonto nicht in der Liste angezeigt wird, stellen Sie sicher, dass es den Voraussetzungen entspricht und dass der Cache auf dieses zugreifen kann.

  Sie müssen die Cache-Instanz für den Zugriff auf das Speicherkonto autorisieren, wie unter [Hinzufügen der Zugriffsrollen](#add-the-access-control-roles-to-your-account) beschrieben.

* **Speichercontainer:** Wählen Sie den NFS-fähigen Blobcontainer für dieses Ziel aus, oder klicken Sie auf **Neu erstellen**.

* **Nutzungsmodell**: Wählen Sie auf der Grundlage Ihres Workflows eines der oben im Abschnitt [Auswählen eines Nutzungsmodells](#choose-a-usage-model) beschriebenen Datencacheprofile aus.

Klicken Sie abschließend auf **OK**, um das Speicherziel hinzuzufügen.

## <a name="view-storage-targets"></a>Anzeigen von Speicherzielen

Sie können das Azure-Portal oder die Azure CLI verwenden, um die bereits für Ihren Cache definierten Speicherziele anzuzeigen.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Öffnen Sie im Azure-Portal Ihre Cache-Instanz, und klicken Sie in der linken Seitenleiste unter der Überschrift „Einstellungen“ auf **Speicherziele**. Auf der Seite „Speicherziele“ werden alle vorhandenen Ziele sowie Steuerelemente zum Hinzufügen oder Löschen von Zielen aufgelistet.

Klicken Sie auf den Namen eines Speicherziels, um seine zugehörige Detailseite zu öffnen.

Weitere Informationen finden Sie unter [Ansehen und Verwalten von Speicherzielen](hpc-cache-edit-storage.md) und [Hinzufügen von Speicherzielen](manage-storage-targets.md).

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Verwenden Sie die Option [az hpc-cache storage-target list](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list), um die vorhandenen Speicherziele für einen Cache anzuzeigen. Geben Sie den Cachenamen und die Ressourcengruppe an (sofern Sie dies nicht global festgelegt haben).

```azurecli
az hpc-cache storage-target list --resource-group "scgroup" --cache-name "sc1"
```

Verwenden Sie [az hpc-cache storage-target show](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage-target-list), um Details zu einem bestimmten Speicherziel anzuzeigen. (Geben Sie das Speicherziel anhand des Namens an.)

Beispiel:

```azurecli
$ az hpc-cache storage-target show --cache-name doc-cache0629 --name nfsd1

{
  "clfs": null,
  "id": "/subscriptions/<subscription_ID>/resourceGroups/scgroup/providers/Microsoft.StorageCache/caches/doc-cache0629/storageTargets/nfsd1",
  "junctions": [
    {
      "namespacePath": "/nfs1/data1",
      "nfsExport": "/datadisk1",
      "targetPath": ""
    }
  ],
  "location": "eastus",
  "name": "nfsd1",
  "nfs3": {
    "target": "10.0.0.4",
    "usageModel": "WRITE_WORKLOAD_15"
  },
  "provisioningState": "Succeeded",
  "resourceGroup": "scgroup",
  "targetType": "nfs3",
  "type": "Microsoft.StorageCache/caches/storageTargets",
  "unknown": null
}
$
```

---

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Speicherziele erstellt haben, fahren Sie mit diesen Aufgaben fort, um Ihren Cache einsatzbereit zu machen:

* [Einrichten des aggregierten Namespace](add-namespace-paths.md)
* [Einbinden der Azure HPC Cache-Instanz](hpc-cache-mount.md)
* [Daten in Azure-Blobspeicher verschieben](hpc-cache-ingest.md)

Wenn Sie Einstellungen aktualisieren müssen, können Sie ein [Speicherziel bearbeiten](hpc-cache-edit-storage.md).
