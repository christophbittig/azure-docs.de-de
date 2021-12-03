---
title: Einbinden von Azure Storage als lokale Freigabe (Container)
description: Informieren Sie sich, wie Sie eine benutzerdefinierte Netzwerkfreigabe in einer containerisierten App in Azure App Service anfügen. Nutzen Sie Dateien in verschiedenen Apps, verwalten Sie statische Inhalte remote, greifen Sie lokal darauf zu u. v. m.
author: msangapu-msft
ms.topic: article
ms.date: 09/02/2021
ms.author: msangapu
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 12f2cc667cc568132eb4e91a1f739f81b075fd80
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400089"
---
# <a name="mount-azure-storage-as-a-local-share-in-a-container-app-in-app-service"></a>Einbinden von Azure Storage als lokale Freigabe in einer Container-App in App Service

::: zone pivot="container-windows"

> [!NOTE]
>Azure Storage im App Service Windows-Container befindet sich **in der Vorschau** und wird für **Produktionsszenarien** **nicht unterstützt**.

In diesem Leitfaden erfahren Sie, wie Sie Azure Storage-Dateien als Netzwerkfreigabe in einem Windows-Container in App Service bereitstellen. Nur [Azure Files-Dateifreigaben](../storage/files/storage-how-to-use-files-portal.md) und [Premium-Dateifreigaben](../storage/files/storage-how-to-create-file-share.md) werden unterstützt. Zu den Vorteilen benutzerdefiniert bereitgestellten Speichers zählen:

::: zone-end

::: zone pivot="container-linux"

In diesem Leitfaden wird gezeigt, wie sie Azure Storage als Netzwerkfreigabe in einem integrierten Linux-Container oder benutzerdefinierten Linux-Container in App Service bereitstellen. Siehe das Video [Einbinden von Azure Storage als lokale Freigabe](https://www.youtube.com/watch?v=OJkvpWYr57Y). Zu den Vorteilen benutzerdefiniert bereitgestellten Speichers zählen:

::: zone-end

- Konfigurieren Sie persistenten Speicher für Ihre App Service-App, und verwalten Sie den Speicher separat.
- Machen Sie statische Inhalte wie Videos und Bilder ohne Weiteres für Ihre App Service-App verfügbar. 
- Schreiben Sie Anwendungsprotokolldateien, oder archivieren Sie ein älteres Anwendungsprotokoll in Azure-Dateifreigaben.  
- Geben Sie Inhalte mehrere Apps übergreifend oder für andere Azure-Dienste frei.

::: zone pivot="container-windows"

Die folgenden Features werden für Windows-Container unterstützt:

- Gesicherter Zugriff auf Speicherkonten mit [privaten Verbindungen](../storage/common/storage-private-endpoints.md) (bei Verwendung der [VNET-Integration](./overview-vnet-integration.md)). Die Unterstützung des [Dienstendpunkts](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) ist derzeit nicht verfügbar.
- Azure Files (Lesen/Schreiben).
- Bis zu fünf Bereitstellungspunkte pro App.
- Zuordnung von Laufwerkbuchstaben (`C:` bis `Z:`).

::: zone-end

::: zone pivot="container-linux"

Die folgenden Features werden für Linux-Container unterstützt:

- Gesicherter Zugriff auf Speicherkonten mit [Service-Endpunkten](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) und [privaten Verbindungen](../storage/common/storage-private-endpoints.md) (bei Verwendung der [VNET-Integration](./overview-vnet-integration.md)).
- Azure Files (Lesen/Schreiben).
- Azure-Blobs (schreibgeschützt).
- Bis zu fünf Bereitstellungspunkte pro App.

::: zone-end

## <a name="prerequisites"></a>Voraussetzungen

::: zone pivot="container-windows"

- [Eine vorhandene Windows-Container-App in Azure App Service](quickstart-custom-container.md)
- [Erstellen einer Azure Files-Dateifreigabe](../storage/files/storage-how-to-use-files-portal.md)
- [Hochladen von Dateien in eine Azure Files-Dateifreigabe](../storage/files/storage-how-to-create-file-share.md)

::: zone-end

::: zone pivot="container-linux"

- Eine vorhandene [App Service für Linux-App](index.yml).
- Ein [Azure Storage-Konto](../storage/common/storage-account-create.md?tabs=azure-cli).
- [Azure-Dateifreigabe und -Verzeichnis](../storage/files/storage-how-to-use-files-portal.md).

::: zone-end

> [!NOTE]
> Azure Storage ist ein nicht standardmäßiger Speicher für App Service und wird separat berechnet, nicht im App Service-Lieferumfang enthalten.
>

## <a name="limitations"></a>Einschränkungen

::: zone pivot="container-windows"

- Speicherbereitstellungen werden für (nicht containerisierte) native Windows-Apps nicht unterstützt.
- Azure-Blobs werden nicht unterstützt.
- Die [Speicherfirewall](../storage/common/storage-network-security.md) wird nur über [private Endpunkte](../storage/common/storage-private-endpoints.md) unterstützt (wenn die [VNET-Integration](./overview-vnet-integration.md) verwendet wird). Benutzerdefinierte DNS-Unterstützung ist derzeit nicht verfügbar, wenn das bereitgestellte Azure Storage-Konto einen privaten Endpunkt verwendet.
- FTP/FTPS-Zugriff auf bereitgestellten Speicher wird nicht unterstützt (verwenden Sie den [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/)).
- Die Zuordnung von `[C-Z]:\`, `[C-Z]:\home`, `/` und `/home` zu benutzerdefiniert bereitgestelltem Speicher wird nicht unterstützt.
- Speicherbereitstellungen können nicht zusammen mit der Kloneinstellungenoption bei der Erstellung von [Bereitstellungsslots](deploy-staging-slots.md) verwendet werden.
- Speicherbereitstellungen werden nicht gesichert, wenn Sie [Ihre App sichern](manage-backup.md). Achten Sie darauf, zum Sichern der Azure Storage-Konten bewährte Methoden zu befolgen. 

::: zone-end

::: zone pivot="container-linux"

- Die [Speicherfirewall](../storage/common/storage-network-security.md) wird nur über [Dienstendpunkte](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) und [private Endpunkte](../storage/common/storage-private-endpoints.md) unterstützt (wenn die [VNET-Integration](./overview-vnet-integration.md) verwendet wird). Benutzerdefinierte DNS-Unterstützung ist derzeit nicht verfügbar, wenn das bereitgestellte Azure Storage-Konto einen privaten Endpunkt verwendet.
- FTP/FTPS-Zugriff auf benutzerdefiniert bereitgestellten Speicher wird nicht unterstützt (verwenden Sie den [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/)).
- Die Unterstützung von Azure CLI, Azure PowerShell und Azure SDK befindet sich in der Vorschau.
- Die Zuordnung von `/` oder `/home` zu benutzerdefiniert bereitgestelltem Speicher wird nicht unterstützt.
- Ordnen Sie die benutzerdefinierte Speichereinbindung nicht zu `/tmp` oder seinen Unterverzeichnissen zu, da dies beim Starten der App zu einem Timeout führen kann.
- Speicherbereitstellungen können nicht zusammen mit der Kloneinstellungenoption bei der Erstellung von [Bereitstellungsslots](deploy-staging-slots.md) verwendet werden.
- Speicherbereitstellungen werden nicht gesichert, wenn Sie [Ihre App sichern](manage-backup.md). Achten Sie darauf, zum Sichern der Azure Storage-Konten bewährte Methoden zu befolgen. 

::: zone-end

::: zone pivot="container-windows"
## <a name="mount-storage-to-windows-container"></a>Bereitstellen von Speicher für Windows-Container
::: zone-end
::: zone pivot="container-linux"
## <a name="mount-storage-to-linux-container"></a>Bereitstellen von Speicher für Linux-Container
::: zone-end

# <a name="azure-portal"></a>[Azure-Portal](#tab/portal)

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur App.
1. Klicken Sie im linken Navigationsbereich auf **Konfiguration** > **Pfadzuordnungen** > **Neue Azure-Speicherbereitstellung**. 
1. Konfigurieren Sie die Speicherbereitstellung gemäß der folgenden Tabelle. Wenn Sie fertig sind, klicken Sie auf **OK**.

    ::: zone pivot="container-windows"
    | Einstellung | Beschreibung |
    |-|-|
    | **Name** | Name der Bereitstellungskonfiguration. Leerzeichen sind nicht zulässig. |
    | **Konfigurationsoptionen** | Wählen Sie **Basic** aus, wenn das Speicherkonto keine [privaten Endpunkte](../storage/common/storage-private-endpoints.md) verwendet. Wählen Sie andernfalls **Erweitert** aus. |
    | **Speicherkonten** | Azure Storage-Konto. Es muss eine Azure Files-Freigabe enthalten sein. |
    | **Freigabename** | Die bereitzustellende Dateifreigabe. |
    | **Zugriffsschlüssel** (nur „Erweitert“) | [Zugriffsschlüssel](../storage/common/storage-account-keys-manage.md) für Ihr Speicherkonto. |
    | **Einbindungspfad** | Verzeichnis innerhalb des Windows-Containers für die Bereitstellung in Azure Storage. Verwenden Sie kein Stammverzeichnis (`[C-Z]:\` oder `/`) oder das `home`-Verzeichnis (`[C-Z]:\home` oder `/home`).|
    ::: zone-end
    ::: zone pivot="container-linux"
    | Einstellung | Beschreibung |
    |-|-|
    | **Name** | Name der Bereitstellungskonfiguration. Leerzeichen sind nicht zulässig. |
    | **Konfigurationsoptionen** | Wählen Sie **Basic** aus, wenn das Speicherkonto keine [Dienstendpunkte](../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) oder [privaten Endpunkte](../storage/common/storage-private-endpoints.md) verwendet. Wählen Sie andernfalls **Erweitert** aus. |
    | **Speicherkonten** | Azure Storage-Konto. |
    | **Speichertyp** | Wählen Sie den Typ basierend auf dem Speicher aus, den Sie bereitstellen möchten. Azure-Blobs unterstützen nur schreibgeschützten Zugriff. |
    | **Speichercontainer** oder **Freigabename** | Dateifreigabe oder Blobscontainer, die/der bereitgestellt werden soll. |
    | **Zugriffsschlüssel** (nur „Erweitert“) | [Zugriffsschlüssel](../storage/common/storage-account-keys-manage.md) für Ihr Speicherkonto. |
    | **Einbindungspfad** | Verzeichnis innerhalb des Linux-Containers für die Bereitstellung in Azure Storage. Verwenden Sie nicht `/` oder `/home`.|
    ::: zone-end

    > [!CAUTION]
    > Das in **Einbindungspfad** angegebene Verzeichnis im Container sollte leer sein. Alle in diesem Verzeichnis gespeicherten Inhalte werden gelöscht, wenn Azure Storage bereitgestellt wird (wenn Sie z. B. unter `/home` ein Verzeichnis angeben). Wenn Sie Dateien für eine bestehende App migrieren, erstellen Sie vor Beginn eine Sicherung der App und ihres Inhalts.
    >
    
# <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/cli)

Verwenden Sie den Befehl [`az webapp config storage-account add`](/cli/azure/webapp/config/storage-account#az_webapp_config_storage_account_add). Zum Beispiel:

```azurecli
az webapp config storage-account add --resource-group <group-name> --name <app-name> --custom-id <custom-id> --storage-type AzureFiles --share-name <share-name> --account-name <storage-account-name> --access-key "<access-key>" --mount-path <mount-path-directory>
```

::: zone pivot="container-windows"
- `--storage-type` muss für Windows-Container `AzureFiles` sein. 
- `mount-path-directory` muss in der Form `/path/to/dir` oder `[C-Z]:\path\to\dir` ohne Laufwerksbuchstabe vorliegen. Verwenden Sie kein Stammverzeichnis (`[C-Z]:\` oder `/`) oder das `home`-Verzeichnis (`[C-Z]:\home` oder `/home`).
::: zone-end
::: zone pivot="container-linux"
- `--storage-type` kann `AzureBlob` oder `AzureFiles` sein. `AzureBlob` ist schreibgeschützt.
- `--mount-path` ist das Verzeichnis innerhalb des Linux-Containers für die Bereitstellung in Azure Storage. Verwenden Sie nicht `/` (das Stammverzeichnis).
::: zone-end

Vergewissern Sie sich, dass Ihr Speicher bereitgestellt ist, indem Sie den folgenden Befehl ausführen:

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

> [!CAUTION]
> Das in `--mount-path` angegebene Verzeichnis im Container sollte leer sein. Alle in diesem Verzeichnis gespeicherten Inhalte werden gelöscht, wenn Azure Storage bereitgestellt wird (wenn Sie z. B. unter `/home` ein Verzeichnis angeben). Wenn Sie Dateien für eine bestehende App migrieren, erstellen Sie vor Beginn eine Sicherung der App und ihres Inhalts.
>

Führen Sie den folgenden Befehl aus, um Ihre Konfiguration zu überprüfen:

```azurecli
az webapp config storage-account list --resource-group <resource-group> --name <app-name>
```

---

> [!NOTE]
> Das Hinzufügen, Bearbeiten oder Löschen einer Speicherbereitstellung führt dazu, dass die App neu gestartet wird. 

::: zone pivot="container-linux"

## <a name="test-the-mounted-storage"></a>Testen des bereitgestellten Speichers

So überprüfen Sie, ob Azure Storage erfolgreich für die App bereitgestellt wurde:

1. [Öffnen Sie eine SSH-Sitzung](configure-linux-open-ssh-session.md) im Container.
1. Führen Sie dann im SSH-Terminal folgenden Befehl aus:

    ```bash
    df –h 
    ```
1. Überprüfen Sie, ob die Speicherfreigabe bereitgestellt wurde. Wenn sie nicht vorhanden ist, liegt ein Problem beim Bereitstellen der Speicherfreigabe vor.
1. Überprüfen Sie die Latenz oder die allgemeine Erreichbarkeit der Speichermenge mit folgendem Befehl:

    ```bash
    tcpping Storageaccount.file.core.windows.net 
    ```

::: zone-end

## <a name="best-practices"></a>Bewährte Methoden

- Um potenzielle Probleme im Zusammenhang mit der Latenz zu vermeiden, platzieren Sie die App und das Azure Storage-Konto in derselben Azure-Region. Beachten Sie jedoch, dass diese IP-Einschränkungen nicht berücksichtigt werden, wenn sich die App und das Azure Storage-Konto in derselben Azure-Region befinden und Sie Zugriff von App Service-IP-Adressen in der [Azure Storage-Firewallkonfiguration](../storage/common/storage-network-security.md) gewähren.
::: zone pivot="container-windows"
- Das Bereitstellungsverzeichnis in der Container-App sollte leer sein. Alle in diesem Pfad gespeicherten Inhalte werden gelöscht, wenn Azure Storage bereitgestellt wird. Wenn Sie Dateien für eine bestehende App migrieren, erstellen Sie vor Beginn eine Sicherung der App und ihres Inhalts.
::: zone-end
::: zone pivot="container-linux"
- Das Bereitstellungsverzeichnis in der Container-App sollte leer sein. Alle in diesem Pfad gespeicherten Inhalte werden gelöscht, wenn Azure Storage bereitgestellt wird (wenn Sie z. B. unter `/home` ein Verzeichnis angeben). Wenn Sie Dateien für eine bestehende App migrieren, erstellen Sie vor Beginn eine Sicherung der App und ihres Inhalts.

- Das Bereitstellen des Speichers in `/home` wird nicht empfohlen, da dies bei der App zu Leistungsengpässen führen kann. 
::: zone-end
- Vermeiden Sie im Azure Storage-Konto das [erneute Generieren des Zugriffsschlüssels](../storage/common/storage-account-keys-manage.md), der zum Bereitstellen des Speichers in der App verwendet wird. Das Speicherkonto enthält zwei verschiedene Schlüssel. Stellen Sie in einem schrittweisen Ansatz sicher, dass die Speicherbereitstellung während der erneuten Schlüsselgenerierung für die App verfügbar bleibt. Nehmen wir zum Beispiel an, dass Sie mit **key1** die Speicherbereitstellung in Ihrer Anwendung konfiguriert haben:

    1. Generieren Sie **key2** neu. 
    1. Aktualisieren Sie in der Konfiguration der Speicherbereitstellung den Zugriff auf den Schlüssel, um den neu generierten **key2** zu verwenden.
    1. Generieren Sie **key1** neu.

- Wenn Sie ein Azure Storage-Konto, einen Container oder eine Freigabe löschen, entfernen Sie die entsprechende Speicherbereitstellungskonfiguration in der App, um mögliche Fehlerszenarien zu vermeiden. 

- Die Leistungsstufe des bereitgestellten Azure Storage-Kontos kann Standard oder Premium sein. Wählen Sie die entsprechende Leistungsstufe für das Speicherkonto nach den Kapazität- und Durchsatzanforderungen der App aus. Betrachten Sie die Skalierbarkeits- und Leistungsziele, die dem Speichertyp entsprechen:

    - [Für Dateien](../storage/files/storage-files-scale-targets.md) (Windows- und Linux-Container)
    - [Für Blobs](../storage/blobs/scalability-targets.md) (nur Linux-Container)

- Wenn Ihre App [auf mehrere Instanzen skaliert wird](../azure-monitor/autoscale/autoscale-get-started.md), wird für alle Instanzen eine Verbindung mit demselben bereitgestellten Azure Storage-Konto hergestellt. Um Leistungsengpässe und Durchsatzprobleme zu vermeiden, wählen Sie die entsprechende Leistungsstufe für das Speicherkonto aus.  

- Sie sollten Speicherbereitstellungen nicht für lokale Datenbanken (z. B. SQLite) oder andere Anwendungen und Komponenten verwenden, die auf Dateihandles und -sperren basieren. 

- Wenn Sie [private Azure Storage-Endpunkte](../storage/common/storage-private-endpoints.md) mit der App verwenden, müssen Sie die folgenden zwei App-Einstellungen festlegen:

    - `WEBSITE_DNS_SERVER` = `168.63.129.16`
    - `WEBSITE_VNET_ROUTE_ALL` = `1`

- Wenn Sie [ein Speicherfailover initiieren](../storage/common/storage-initiate-account-failover.md) und das Speicherkonto in der App bereitgestellt wird, kann die Bereitstellung erst dann eine Verbindung herstellen, wenn Sie entweder die App neu starten oder die Azure Storage-Bereitstellung entfernen und hinzufügen. 

## <a name="next-steps"></a>Nächste Schritte

::: zone pivot="container-windows"

- [Migrieren benutzerdefinierter Software zu Azure App Service mithilfe eines benutzerdefinierten Containers](tutorial-custom-container.md?pivots=container-windows)

::: zone-end

::: zone pivot="container-linux"

- [Konfigurieren eines benutzerdefinierten Containers](configure-custom-container.md?pivots=platform-linux)
- [Video: Einbinden von Azure Storage als lokale Freigabe](https://www.youtube.com/watch?v=OJkvpWYr57Y).

::: zone-end
