---
title: Aktivieren des eigenen persistenten Speichers in Azure Spring Cloud | Microsoft-Dokumentation
description: So stellen Sie Ihren eigenen Speicher als persistenten Speicher in Azure Spring Cloud bereit
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: xuycao
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 25af6e4fe8ab2aa097812e504b27e8023ee30fc9
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132402141"
---
# <a name="how-to-enable-your-own-persistent-storage-in-azure-spring-cloud"></a>Aktivieren des eigenen persistenten Speichers in Azure Spring Cloud

**Dieser Artikel gilt für:** ✔️ Java ✔️ C#

In diesem Artikel erfahren Sie, wie Sie Ihren eigenen persistenten Speicher in Azure Spring Cloud aktivieren.

Wenn Sie den integrierten persistenten Speicher in Azure Spring Cloud verwenden, werden von Ihrer Anwendung generierte Artefakte in Azure Storage Accounts hochgeladen. Microsoft bestimmt die Richtlinien zur Verschlüsselung ruhender Daten und zur Verwaltung der Lebensdauer für diese Artefakte. 

Mit Bring Your Own Storage werden diese Artefakte in ein Speicherkonto hochgeladen, das Ihrer Kontrolle unterliegt. Das bedeutet, dass Sie die Richtlinie zur Verschlüsselung ruhender Daten, die Richtlinie zur Verwaltung der Lebensdauer und den Netzwerkzugriff festlegen. Sie sind jedoch auch für die mit diesem Speicherkonto verbundenen Kosten verantwortlich.

## <a name="prerequisites"></a>Voraussetzungen

* Ein vorhandenes Azure Storage-Konto und eine vorab erstellte Azure-Dateifreigabe. Wenn Sie ein Speicherkonto und eine Dateifreigabe in Azure erstellen müssen, finden Sie Informationen dazu unter [Erstellen einer Azure-Dateifreigabe](../storage/files/storage-how-to-create-file-share.md).
* Die [Azure Spring Cloud-Erweiterung](/cli/azure/azure-cli-extensions-overview) für die Azure-Befehlszeilenschnittstelle (Azure CLI)

## <a name="use-the-azure-cli-to-enable-your-own-extra-persistent-storage"></a>Verwenden Sie die Azure CLI, um Ihren eigenen zusätzlichen persistenten Speicher zu aktivieren.

Sie können Ihren eigenen Speicher mit der Azure CLI aktivieren, indem Sie die folgenden Schritte ausführen.

1. Verwenden Sie den folgenden Befehl, um Ihr Azure Storage-Konto als Speicherressource in Ihre Azure Spring Cloud-Instanz einzubinden:

    ```azurecli
   az spring-cloud storage add --storage-type StorageAccount --account-name <account-name> --account-key <account-key>  -g <resource-group-name> -s <spring-instance-name> -n <storage-resource-name>
    ```

1. Verwenden Sie den folgenden Befehl, um eine App mit Ihrem eigenen persistenten Speicher zu erstellen.

    ```azurecli
    az spring-cloud app create -n <app-name> -g <resource-group-name> -s <spring-instance-name> --persistent-storage <path-to-JSON-file>
    ```

    In diesem Beispiel sehen sie die JSON-Datei, die im Create-Befehl an den Parameter `--persistent-storage` übergeben wird:

    ```json
    {
       "customPersistentDisks": [
          {
              "storageName": "<Storage-Resource-Name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<Azure-File-Share-Name>",
                  "mountPath": "<Unique-Mount-Path>",
                  "mountOptions": [
                      "uid=0",
                      "gid=0"
                   ],
                   "readOnly": false 
                }
          },
          {
              "storageName": "<Storage-Resource-Name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<Azure-File-Share-Name>",
                  "mountPath": "<Unique-Mount-Path e.g. /test/anotherPath>",
                  "readOnly": true
              }
          }
       ]
    }
    ```

1. Fügen Sie optional einer vorhandenen App mithilfe des folgenden Befehls zusätzlichen persistenten Speicher hinzu:

    ```azurecli
    az spring-cloud app append-persistent-storage --persistent-storage-type AzureFileVolume --share-name <azure-file-share-name> --mount-path <unique-mount-path> --storage-name <storage-resource-name> -n <app-name> -g <resource-group-name> -s <spring-instance-name>
    ```

1. Listen Sie optional den gesamten vorhandenen persistenten Speicher einer bestimmten Speicherressource mit dem folgenden Befehl auf:

   ```azurecli
   az spring-cloud storage list-persistent-storage -g <resource-group-name> -s <spring-instance-name> -n <storage-resource-name>
   ```

## <a name="use-best-practices"></a>Verwenden von Best Practices

Diese bewährten Methoden unterstützen Sie beim Hinzufügen Ihres eigenen persistenten Speichers zu Azure Spring Cloud.

- Um potenzielle Latenzprobleme zu vermeiden, platzieren Sie die Azure Spring Cloud-Instanz und das Azure Storage-Konto in derselben Azure-Region.

- Vermeiden Sie es im Azure Storage-Konto, den verwendeten Kontoschlüssel neu zu generieren. Das Speicherkonto enthält zwei verschiedene Schlüssel. Verwenden Sie einen Schritt-für-Schritt-Ansatz, um sicherzustellen, dass Ihr eigener persistenter Speicher während der Schlüsselneugenerierung für die Anwendungen verfügbar bleibt. 

   Angenommen, Sie haben „key1“ verwendet, um ein Speicherkonto an Azure Spring Cloud zu binden, und führen dann die folgenden Schritte aus:

   1. Generieren Sie key2 neu.
   1. Aktualisieren Sie den Kontoschlüssel der Speicherressource, um den neu generierten „key2“ zu verwenden.
   1. Starten Sie die Anwendungen neu, die den persistenten Speicher aus dieser Speicherressource einbinden. (Sie können ```az spring-cloud storage list-persistent-storage``` verwenden, um alle zugehörigen Anwendungen aufzulisten.)
   1. Generieren Sie key1 neu.

- Wenn Sie ein Azure Storage-Konto oder eine Azure-Dateifreigabe löschen, entfernen Sie die entsprechende Speicherressource oder den persistenten Speicher in den Anwendungen, um mögliche Fehler zu vermeiden.

## <a name="faqs"></a>Häufig gestellte Fragen

Im Folgenden finden Sie häufig gestellte Fragen (FAQ) zur Verwendung Ihres eigenen persistenten Speichers mit Azure Spring Cloud.

- Werden meine Daten in mein Storage-Konto migriert, wenn ich den integrierten persistenten Speicher aktiviert habe und dann zusätzlich meinen eigenen Speicher als zusätzlichen persistenten Speicher aktiviert habe?

   *Nein. In Kürze wird jedoch ein Dokument bereitgestellt, das Sie dabei unterstützt, die Migration selbst durchzuführen.*

- Was sind die reservierten Bereitstellungspfade?

   *Diese Bereitstellungspfade werden vom Azure Spring Cloud-Dienst reserviert:*
   - */tmp*
   - */persistent*
   - */secrets*
   - */app-insights/agents*
   - */etc/azure-spring-cloud/certs*
   - */app-insights/agents/settings*
   - */app-lifecycle/settings*

- Welche Bereitstellungsoptionen sind verfügbar?

   *Derzeit werden die folgenden Bereitstellungsoptionen unterstützt:*
   - `uid`
   - `gid`
   - `file_mode`
   - `dir_mode`
   
   *Die `mountOptions`-Eigenschaft ist optional. Die Standardwerte für die oben genannten Bereitstellungsoptionen sind: ["uid=0", "gid=0", "file_mode=0777", "dir_mode=0777"]*

## <a name="next-steps"></a>Nächste Schritte

* Erfahren [Sie, wie Sie Logback verwenden, um Protokolle in Ihren eigenen persistenten Speicher zu schreiben. ](./how-to-write-log-to-custom-persistent-storage.md)
* Erfahren Sie, wie Sie [eine Anwendung in Azure Spring Cloud skalieren](./how-to-scale-manual.md).