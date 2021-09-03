---
title: Verwalten von Azure HPC Cache-Speicherzielen
description: Vorgehensweise zum Anhalten, Entfernen, Erzwingen des Löschens und Leerens von Azure HPC Cache-Speicherzielen
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/12/2021
ms.author: v-erkel
ms.openlocfilehash: 6c747c4a79cb0413d7a96ca7b0148912eef89f84
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/16/2021
ms.locfileid: "114296685"
---
# <a name="manage-storage-targets"></a>Verwalten von Speicherzielen

Sie können Verwaltungsaktionen für einzelne Speicherziele ausführen. Diese Aktionen ergänzen die Optionen auf Cacheebene, die unter [Verwalten Ihres Caches](hpc-cache-manage.md) besprochen werden.

Diese Kontrollen können Sie bei der Wiederherstellung nach einer unerwarteten Situation (z. B. einem nicht reagierenden Speicherziel) unterstützen und ihnen auch die Möglichkeit geben, einige automatische Cacheaktionen außer Kraft zu setzen (z. B. das Zurückschreiben geänderter Dateien in das langfristige Speichersystem).

Öffnen Sie die Seite **Speicherziele** im Azure-Portal. Klicken Sie weit rechts in der Speicherzielliste auf den Text **...** , um die Liste der Aufgaben zu öffnen.

![Screenshot der Seite „Speicherziele“ im Azure-Portal, wobei der Cursor über dem Menü angezeigt wird, das durch Klicken auf das Symbol mit den drei Punkten (...) weit rechts neben der Zeile des Speicherziels in der Liste verfügbar gemacht wird.](media/storage-target-manage-options.png)

Die folgenden Optionen stehen zur Verfügung:

* **Leeren**: Schreiben aller zwischengespeicherten Änderungen in den Back-End-Speicher.
* **Anhalten**: Vorübergehendes Beenden der Bearbeitung von Anforderungen durch das Speicherziel.
* **Fortsetzen**: Reaktivieren eines angehaltenen Speicherziels.
* **Entfernen erzwingen**: Löschen eines Speicherziels, Überspringen einiger Sicherheitsschritte ( **„Entfernen erzwingen“ kann zu Datenverlust führen**).
* **Löschen**: Endgültiges Entfernen eines Speicherziels.

Einige Speicherziele verfügen in diesem Menü auch über die Option **DNS aktualisieren**, mit der die IP-Adresse des Speicherziels von einem benutzerdefinierten DNS-Server aktualisiert wird. Diese Konfiguration ist ungewöhnlich.

Weitere Informationen zu diesen Optionen finden Sie im weiteren Verlauf dieses Artikels.

## <a name="write-cached-files-to-the-storage-target"></a>Schreiben zwischengespeicherter Dateien in das Speicherziel

Die Option **Leeren** weist den Cache an, alle geänderten Dateien, die im Cache gespeichert sind, sofort in das Back-End-Speichersystem zu kopieren. Wenn Ihre Clientcomputer beispielsweise eine bestimmte Datei wiederholt aktualisieren, wird sie für einen schnelleren Zugriff im Cache gespeichert und über einen Zeitraum von mehreren Minuten bis zu mehr als einer Stunde nicht in Langzeit-Speichersystem geschrieben.

Die Aktion **Leeren** weist den Cache an, alle Dateien in das Speichersystem zu schreiben.

Der Cache akzeptiert erst nach Abschluss der Leerung Anforderungen von Clients für Dateien an diesem Speicherziel.

Sie können diese Option verwenden, um sicherzustellen, dass der Back-End-Speicher aufgefüllt wird, bevor Sie eine Sicherung durchführen, oder für Situationen, in denen Sie sicherstellen möchten, dass der Back-End-Speicher über aktuelle Updates verfügt.

Diese Option gilt hauptsächlich für Verwendungsmodelle, die das Zwischenspeichern von Schreibzugriffen umfassen. Weitere Informationen zum Zwischenspeichern von Lese- und Schreibvorgängen finden Sie unter [Verstehen von Cachenutzungsmodellen](cache-usage-models.md).

## <a name="suspend-a-storage-target"></a>Anhalten eines Speicherziels

Das Feature „Anhalten“ deaktiviert den Clientzugriff auf ein Speicherziel, entfernt das Speicherziel aber nicht endgültig aus Ihrem Cache. Sie können diese Option verwenden, wenn Sie ein Back-End-Speichersystem zum Zwecke der Wartung, Reparatur oder des Austauschs deaktivieren müssen.

## <a name="put-a-suspended-storage-target-back-in-service"></a>Reaktivieren eines angehaltenen Speicherziels

Verwenden Sie **Fortsetzen**, um das Anhalten eines Speicherziels aufzuheben.

## <a name="force-remove-a-storage-target"></a>Erzwungenes Entfernen eines Speicherziels

> [!NOTE]
> Diese Option kann zu Datenverlusten beim betroffenen Speicherziel führen.

Wenn ein Speicherziel nicht mit einer normalen Löschaktion entfernt werden kann, können Sie die Option **Entfernen erzwingen** verwenden, um es aus dem Azure HPC Cache zu löschen.

Diese Aktion überspringt den Schritt, der Dateien im Cache mit den Dateien im Back-End-Speichersystem synchronisiert. Es gibt keine Garantie dafür, dass alle Änderungen, die in den HPC Cache geschrieben werden, auch in das Back-End-Speichersystem geschrieben werden, sodass Änderungen verloren gehen können, wenn Sie diese Option verwenden.

Es gibt auch keine Garantie, dass auf das Back-End-Speichersystem zugegriffen werden kann, nachdem es aus dem Cache entfernt wurde.

In der Regel wird „Entfernen erzwingen“ nur verwendet, wenn ein Speicherziel nicht mehr reagiert oder sich anderweitig in einem fehlerhaften Zustand befindet. Mit dieser Option können Sie das ungültige Speicherziel entfernen, anstatt drastischere Maßnahmen ergreifen zu müssen.
<!-- https://msazure.visualstudio.com/One/_workitems/edit/8267141 -->

## <a name="delete-a-storage-target"></a>Löschen eines Speicherziels

Sie können das Azure-Portal oder die Azure CLI verwenden, um ein Speicherziel zu löschen.

Mit der regulären Löschoption wird das Speicherziel endgültig aus dem HPC Cache entfernt, aber zuerst wird der Cacheinhalt mit dem Back-End-Speichersystem synchronisiert. Dies unterscheidet sich von der Option „Löschen erzwingen“, bei der keine Daten synchronisiert werden.

Durch das Löschen eines Speicherziels wird die Zuordnung des Speichersystems zu diesem Azure HPC Cache entfernt, das Back-End-Speichersystem wird jedoch nicht geändert. Wenn Sie beispielsweise einen Azure Blob Storage-Container verwendet haben, sind der Container und sein Inhalt nach dem Löschen aus dem Cache weiterhin vorhanden. Im Azure-Portal können Sie den Container einer anderen Azure HPC Cache-Instanz hinzufügen, ihn erneut diesem Cache hinzufügen oder löschen.

Wenn eine große Menge geänderter Daten im Cache gespeichert ist, kann das Löschen eines Speicherziels einige Minuten dauern. Warten Sie, bis die Aktion abgeschlossen ist, um sicherzustellen, dass die Daten sicher in Ihrem Langzeit-Speichersystem gespeichert sind.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Wenn Sie ein Speicherziel entfernen möchten, öffnen Sie die Seite **Speicherziele**. Klicken Sie auf neben dem Speicherziel auf „...“, und wählen Sie im Menü **Löschen** aus.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Verwenden Sie [az hpc-cache storage-target remove](/cli/azure/hpc-cache/storage-target#az_hpc_cache_storage_target_remove), um ein Speicherziel aus dem Cache zu löschen.

```azurecli
$ az hpc-cache storage-target remove --resource-group cache-rg --cache-name doc-cache0629 --name blob1

{- Finished ..
  "endTime": "2020-07-09T21:45:06.1631571+00:00",
  "name": "2f95eac1-aded-4860-b19c-3f089531a7ec",
  "startTime": "2020-07-09T21:43:38.5461495+00:00",
  "status": "Succeeded"
}
```

---

## <a name="update-ip-address-custom-dns-configurations-only"></a>Aktualisieren der IP-Adresse (nur benutzerdefinierte DNS-Konfigurationen)

Wenn im Cache eine nicht standardmäßige DNS-Konfiguration verwendet wird, wird möglicherweise die IP-Adresse Ihres NFS-Speicherziels aufgrund von Back-End-DNS-Änderungen geändert. Wenn die IP-Adresse des Back-End-Speichersystems vom DNS-Server geändert wird, verliert Azure HPC Cache möglicherweise den Zugriff auf das Speichersystem.

Im Idealfall sollten Sie mit dem Manager des benutzerdefinierten DNS-Systems für Ihren Cache gemeinsam Updates planen, da der Speicher während dieser Änderungen nicht verfügbar ist.

Wenn Sie die vom DNS bereitgestellte IP-Adresse eines Speicherziels aktualisieren müssen, verwenden Sie die Seite **Speicherziele**. Klicken Sie in der rechten Spalte auf das Symbol **...** , um das Kontextmenü zu öffnen. Klicken Sie auf **DNS aktualisieren**, um beim benutzerdefinierten DNS-Server eine neue IP-Adresse abzufragen.

![Screenshot der Liste der Speicherziele. Bei einem Speicherziel ist das Menü mit den drei Punkten in der letzten Spalte geöffnet und enthält die beiden Optionen „Löschen“ und „DNS aktualisieren“.](media/refresh-dns.png) <!-- update screenshot if possible -->

Bei erfolgreicher Ausführung sollte das Update weniger als zwei Minuten dauern. Es kann immer nur ein Speicherziel gleichzeitig aktualisiert werden. Warten Sie, bis der vorherige Vorgang beendet wurde, bevor Sie den nächsten starten.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Verwaltungsaktionen auf Cacheebene](hpc-cache-manage.md).
* [Bearbeiten eines Speicherziels](hpc-cache-edit-storage.md)
