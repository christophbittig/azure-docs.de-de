---
title: Verwalten von Azure HPC Cache-Speicherzielen
description: Anhalten, Entfernen, Erzwingen des Löschens und Leeren von Azure HPC Cache Speicherzielen und Verstehen des Speicherzielzustands
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/27/2021
ms.author: femila
ms.openlocfilehash: ebab51f480751e6a9d6c60cad09d6884c5d6cc7e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051057"
---
# <a name="view-and-manage-storage-targets"></a>Anzeigen und Verwalten von Speicherzielen

Auf der Einstellungsseite für Speicherziele werden Informationen zu den einzelnen Speicherzielen für Ihre HPC Cache sowie Optionen zum Verwalten einzelner Speicherziele angezeigt.

> [!TIP]
> Anweisungen zum Auflisten von Speicherzielen mit der Azure CLI finden Sie im Artikel [Hinzufügen von Speicherzielen](hpc-cache-add-storage.md#view-storage-targets). Andere hier aufgeführte Aktionen sind möglicherweise noch nicht in der Azure CLI verfügbar.

![Screenshot der Seite Einstellungen > Speicherkonto im Azure-Portal Es gibt mehrere Speicherziele in der Liste, und Spaltenüberschriften zeigen Name, Typ, Status, Bereitstellungsstatus, Adresse/Container und Nutzungsmodell für jedes Modell an.](media/storage-targets-list-states.png)

## <a name="manage-storage-targets"></a>Verwalten von Speicherzielen

Sie können Verwaltungsaktionen für einzelne Speicherziele ausführen. Diese Aktionen ergänzen die Optionen auf Cacheebene, die unter [Verwalten Ihres Caches](hpc-cache-manage.md) besprochen werden.

Diese Kontrollen können Sie bei der Wiederherstellung nach einer unerwarteten Situation (z. B. einem nicht reagierenden Speicherziel) unterstützen und ihnen auch die Möglichkeit geben, einige automatische Cacheaktionen außer Kraft zu setzen (z. B. das Zurückschreiben geänderter Dateien in das langfristige Speichersystem).

Öffnen Sie die Seite **Speicherziele** im Azure-Portal. Klicken Sie weit rechts in der Speicherzielliste auf das Bild **...** , um die Liste der Aufgaben zu öffnen.

![Screenshot der Seite „Speicherziele“ im Azure-Portal, wobei der Cursor über dem Menü angezeigt wird, das durch Klicken auf das Symbol mit den drei Punkten (...) weit rechts neben der Zeile des Speicherziels in der Liste verfügbar gemacht wird.](media/storage-target-manage-options.png)

Die folgenden Optionen stehen zur Verfügung:

* **Leeren**: Schreiben aller zwischengespeicherten Änderungen in den Back-End-Speicher.
* **Anhalten**: Vorübergehendes Beenden der Bearbeitung von Anforderungen durch das Speicherziel.
* **Fortsetzen**: Reaktivieren eines angehaltenen Speicherziels.
* **Entfernen erzwingen**: Löschen eines Speicherziels, Überspringen einiger Sicherheitsschritte ( **„Entfernen erzwingen“ kann zu Datenverlust führen**).
* **Löschen**: Endgültiges Entfernen eines Speicherziels.

Einige Speicherziele verfügen in diesem Menü auch über die Option **DNS aktualisieren**, mit der die IP-Adresse des Speicherziels von einem benutzerdefinierten DNS-Server aktualisiert wird. Diese Konfiguration ist ungewöhnlich.

Weitere Informationen zu diesen Optionen finden Sie im weiteren Verlauf dieses Artikels.

### <a name="write-cached-files-to-the-storage-target"></a>Schreiben zwischengespeicherter Dateien in das Speicherziel

Die Option **Leeren** weist den Cache an, alle geänderten Dateien, die im Cache gespeichert sind, sofort in das Back-End-Speichersystem zu kopieren. Wenn Ihre Clientcomputer beispielsweise eine bestimmte Datei wiederholt aktualisieren, wird sie für einen schnelleren Zugriff im Cache gespeichert und über einen Zeitraum von mehreren Minuten bis zu mehr als einer Stunde nicht in Langzeit-Speichersystem geschrieben.

Die Aktion **Leeren** weist den Cache an, alle Dateien in das Speichersystem zu schreiben.

Der Cache akzeptiert erst nach Abschluss der Leerung Anforderungen von Clients für Dateien an diesem Speicherziel.

Sie können diese Option verwenden, um sicherzustellen, dass der Back-End-Speicher aufgefüllt wird, bevor Sie eine Sicherung durchführen, oder für Situationen, in denen Sie sicherstellen möchten, dass der Back-End-Speicher über aktuelle Updates verfügt.

Diese Option gilt hauptsächlich für Verwendungsmodelle, die das Zwischenspeichern von Schreibzugriffen umfassen. Weitere Informationen zum Zwischenspeichern von Lese- und Schreibvorgängen finden Sie unter [Verstehen von Cachenutzungsmodellen](cache-usage-models.md).

### <a name="suspend-a-storage-target"></a>Anhalten eines Speicherziels

Das Feature „Anhalten“ deaktiviert den Clientzugriff auf ein Speicherziel, entfernt das Speicherziel aber nicht endgültig aus Ihrem Cache. Sie können diese Option verwenden, wenn Sie ein Back-End-Speichersystem zum Zwecke der Wartung, Reparatur oder des Austauschs deaktivieren müssen.

### <a name="put-a-suspended-storage-target-back-in-service"></a>Reaktivieren eines angehaltenen Speicherziels

Verwenden Sie **Fortsetzen**, um das Anhalten eines Speicherziels aufzuheben.

### <a name="force-remove-a-storage-target"></a>Erzwungenes Entfernen eines Speicherziels

> [!NOTE]
> Diese Option kann zu Datenverlusten beim betroffenen Speicherziel führen.

Wenn ein Speicherziel nicht mit einer normalen Löschaktion entfernt werden kann, können Sie die Option **Entfernen erzwingen** verwenden, um es aus dem Azure HPC Cache zu löschen.

Diese Aktion überspringt den Schritt, der Dateien im Cache mit den Dateien im Back-End-Speichersystem synchronisiert. Es gibt keine Garantie dafür, dass alle Änderungen, die in den HPC Cache geschrieben werden, auch in das Back-End-Speichersystem geschrieben werden, sodass Änderungen verloren gehen können, wenn Sie diese Option verwenden.

Es gibt auch keine Garantie, dass auf das Back-End-Speichersystem zugegriffen werden kann, nachdem es aus dem Cache entfernt wurde.

In der Regel wird „Entfernen erzwingen“ nur verwendet, wenn ein Speicherziel nicht mehr reagiert oder sich anderweitig in einem fehlerhaften Zustand befindet. Mit dieser Option können Sie das ungültige Speicherziel entfernen, anstatt drastischere Maßnahmen ergreifen zu müssen.

### <a name="delete-a-storage-target"></a>Löschen eines Speicherziels

Sie können das Azure-Portal oder die Azure CLI verwenden, um ein Speicherziel zu löschen.

Mit der regulären Löschoption wird das Speicherziel endgültig aus dem HPC Cache entfernt, aber zuerst wird der Cacheinhalt mit dem Back-End-Speichersystem synchronisiert. Dies unterscheidet sich von der Option „Löschen erzwingen“, bei der keine Daten synchronisiert werden.

Durch das Löschen eines Speicherziels wird die Zuordnung des Speichersystems zu diesem Azure HPC Cache entfernt, das Back-End-Speichersystem wird jedoch nicht geändert. Wenn Sie beispielsweise einen Azure Blob Storage-Container verwendet haben, sind der Container und sein Inhalt nach dem Löschen aus dem Cache weiterhin vorhanden. Im Azure-Portal können Sie den Container einer anderen Azure HPC Cache-Instanz hinzufügen, ihn erneut diesem Cache hinzufügen oder löschen.

Wenn eine große Menge geänderter Daten im Cache gespeichert ist, kann das Löschen eines Speicherziels einige Minuten dauern. Warten Sie, bis die Aktion abgeschlossen ist, um sicherzustellen, dass die Daten sicher in Ihrem Langzeit-Speichersystem gespeichert sind.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Wenn Sie ein Speicherziel entfernen möchten, öffnen Sie die Seite **Speicherziele**. Klicken Sie auf neben dem Speicherziel auf „...“, und wählen Sie im Menü **Löschen** aus.

#### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

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

### <a name="update-ip-address-custom-dns-configurations-only"></a>Aktualisieren der IP-Adresse (nur benutzerdefinierte DNS-Konfigurationen)

Wenn im Cache eine nicht standardmäßige DNS-Konfiguration verwendet wird, wird möglicherweise die IP-Adresse Ihres NFS-Speicherziels aufgrund von Back-End-DNS-Änderungen geändert. Wenn die IP-Adresse des Back-End-Speichersystems vom DNS-Server geändert wird, verliert Azure HPC Cache möglicherweise den Zugriff auf das Speichersystem.

Im Idealfall sollten Sie mit dem Manager des benutzerdefinierten DNS-Systems für Ihren Cache gemeinsam Updates planen, da der Speicher während dieser Änderungen nicht verfügbar ist.

Wenn Sie die vom DNS bereitgestellte IP-Adresse eines Speicherziels aktualisieren müssen, verwenden Sie die Seite **Speicherziele**. Klicken Sie in der rechten Spalte auf das Symbol **...** , um das Kontextmenü zu öffnen. Klicken Sie auf **DNS aktualisieren**, um beim benutzerdefinierten DNS-Server eine neue IP-Adresse abzufragen.

![Screenshot der Liste der Speicherziele. Für ein Speicherziel ist das "..."-Menü in der Spalte ganz rechts ist geöffnet, und die folgenden Optionen werden angezeigt: Leeren, Anhalten, DNS aktualisieren, Entfernen erzwingen, Fortsetzen (diese Option ist deaktiviert) und Löschen.](media/refresh-dns.png)

Bei erfolgreicher Ausführung sollte das Update weniger als zwei Minuten dauern. Es kann immer nur ein Speicherziel gleichzeitig aktualisiert werden. Warten Sie, bis der vorherige Vorgang beendet wurde, bevor Sie den nächsten starten.

## <a name="understand-storage-target-state"></a>Grundlegendes zum Speicherzielstatus

In der Speicherzielliste werden zwei Statustypen angezeigt: **Status** und **Bereitstellungsstatus**.

* Der **Status** gibt den Betriebszustand des Speicherziels an. Dieser Wert wird regelmäßig aktualisiert und hilft Ihnen zu verstehen, ob das Speicherziel für Clientanforderungen verfügbar ist und welche Verwaltungsoptionen verfügbar sind.
* Der **Bereitstellungsstatus** gibt an, ob die letzte Aktion zum Hinzufügen oder Bearbeiten des Speicherziels erfolgreich war. Dieser Wert wird nur aktualisiert, wenn Sie das Speicherziel bearbeiten.

Der Wert **Status** wirkt sich darauf aus, welche Verwaltungsoptionen Sie verwenden können. Im Folgenden finden Sie eine kurze Erläuterung der Werte und ihrer Auswirkungen.

* **Bereit**: Das Speicherziel funktioniert normal und ist für Clients verfügbar. Sie können eine der Verwaltungsoptionen für dieses Speicherziel verwenden (mit Ausnahme von **Fortsetzen**, das nur für angehaltene Speicherziele gültig ist).
* **Ausgelastet**: Das Speicherziel verarbeitet einen weiteren Vorgang. Sie können das Speicherziel löschen oder erzwungen entfernen.
* **Angehalten**: Das Speicherziel wurde offline geschaltet. Sie können dieses Speicherziel weiterhin leeren, löschen oder erzwungen entfernen. Wählen Sie **Fortsetzen** aus, um ein angehaltenes Speicherziel wieder zu reaktivieren.
* **Leeren**: Das Speicherziel schreibt Daten in den Back-End-Speicher. Das Ziel kann Clientanforderungen beim Leeren nicht verarbeiten, aber es wird automatisch wieder in den vorherigen Zustand zurückversetzt, nachdem das Schreiben von Daten abgeschlossen ist.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über [Verwaltungsaktionen auf Cacheebene](hpc-cache-manage.md).
* [Bearbeiten eines Speicherziels](hpc-cache-edit-storage.md)
