---
title: Verwalten und Aktualisieren von Azure HPC Cache
description: Verwalten und Aktualisieren von Azure HPC Cache im Azure-Portal oder über die Azure-Befehlszeilenschnittstelle (CLI)
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/08/2021
ms.author: femila
ms.openlocfilehash: 46d891172c3290b4ce21723561f0689bedd7b22d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087797"
---
# <a name="manage-your-cache"></a>Verwalten Ihres Caches

Auf der Übersichtsseite für den Cache im Azure-Portal werden Projektdetails, der Cachestatus und grundlegende Statistiken für den Cache angezeigt. Außerdem verfügt die Seite über Steuerelemente zum Beenden oder Starten des Caches, zum Löschen des Caches, zum Leeren der Daten in den Langzeitspeicher und zum Aktualisieren der Software.

In diesem Artikel wird auch erläutert, wie Sie diese grundlegenden Aufgaben über die Azure-Befehlszeilenschnittstelle (CLI) durchführen.

Um die Übersichtsseite zu öffnen, wählen Sie die Cacheressource im Azure-Portal aus. Laden Sie z. B. die Seite **Alle Ressourcen**, und klicken Sie auf den Namen des Caches.

![Screenshot: Übersichtsseite einer Azure HPC Cache-Instanz](media/hpc-cache-overview.png)

Über die Schaltflächen oben auf der Seite können Sie den Cache verwalten:

* **Starten** und [**Beenden**](#stop-the-cache): Der Cachevorgang wird begonnen oder angehalten.
* [**Leeren:**](#flush-cached-data) Geänderte Daten werden in Speicherziele geschrieben.
* [**Aktualisieren:**](#upgrade-cache-software) Die Cachesoftware wird aktualisiert.
* [**Collect diagnostics**](#collect-diagnostics) (Sammeln von Diagnosedaten): Informationen zum Uploaddebugging
* **Aktualisieren:** Die Übersichtsseite wird erneut geladen.
* [**Löschen:**](#delete-the-cache) Der Cache wird dauerhaft zerstört.

Nachfolgend finden Sie weitere Informationen zu diesen Optionen.

> [!TIP]
> Sie können auch einzelne Speicherziele verwalten. Weitere Informationen finden Sie unter [Anzeigen und Verwalten von Speicherzielen](manage-storage-targets.md).

Klicken Sie auf das Bild unten, um ein [Video](https://azure.microsoft.com/resources/videos/managing-hpc-cache/) abzuspielen, in dem Cacheverwaltungsaufgaben veranschaulicht werden.

[![Videominiaturansicht: Azure HPC Cache: Verwalten (klicken Sie, um die Videoseite aufzurufen)](media/video-5-manage.png)](https://azure.microsoft.com/resources/videos/managing-hpc-cache/)

## <a name="stop-the-cache"></a>Beenden des Caches

Sie können den Cache beenden, um die Kosten während eines inaktiven Zeitraums zu senken. Ihnen wird keine Betriebszeit in Rechnung gestellt, während der Cache angehalten wird. Es werden jedoch Gebühren für den zugeordneten Datenträgerspeicher des Caches erhoben. (Ausführlichere Informationen finden Sie auf der Seite mit der [Preisübersicht](https://aka.ms/hpc-cache-pricing).)

Ein beendeter Cache reagiert nicht auf Clientanforderungen. Sie sollten die Bereitstellung von Clients aufheben, bevor Sie den Cache beenden.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Mit der Schaltfläche **Beenden** wird ein aktiver Cache angehalten. Die Schaltfläche **Beenden** ist verfügbar, wenn der Cachestatus **Fehlerfrei** oder **Heruntergestuft** lautet.

![Screenshot der oberen Schaltflächen, wobei „Beenden“ hervorgehoben ist und in einer Popupnachricht die Aktion „Beenden“ beschrieben ist und die Frage „Möchten Sie den Vorgang fortsetzen?“ gestellt wird einschließlich der Schaltflächen „Ja“ (Standard) und „Nein“](media/stop-cache.png)

Wenn Sie auf „Ja“ klicken, um das Beenden des Caches zu bestätigen, leert der Cache seinen Inhalt automatisch in die Speicherziele. Dieser Vorgang kann einige Zeit dauern, stellt jedoch die Datenkonsistenz sicher. Schließlich ändert sich der Cachestatus in **Beendet**.

Um einen beendeten Cache erneut zu aktivieren, klicken Sie auf die Schaltfläche **Starten**. Es ist keine Bestätigung erforderlich.

![Screenshot der oberen Schaltflächen, wobei „Starten“ hervorgehoben ist](media/start-cache.png)

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Mit dem Befehl [az hpc-cache stop](/cli/azure/hpc-cache#az_hpc_cache_stop) halten Sie einen Cache vorübergehend an. Diese Aktion ist nur gültig, wenn der Cachestatus **Fehlerfrei** oder **Heruntergestuft** lautet.

Vor dem Anhalten leert der Cache seinen Inhalt automatisch in die Speicherziele. Dieser Vorgang kann einige Zeit dauern, stellt jedoch die Datenkonsistenz sicher.

Wenn die Aktion abgeschlossen ist, ändert sich der Status in **Beendet**.

Mit dem Befehl [az hpc-cache start](/cli/azure/hpc-cache#az_hpc_cache_start) reaktivieren Sie einen angehaltenen Cache.

Wenn Sie den Befehl zum Starten oder Beenden ausgeben, wird die Statusmeldung „Wird ausgeführt“ angezeigt, bis der Vorgang abgeschlossen ist.

```azurecli
$ az hpc-cache start --name doc-cache0629
 - Running ..
```

Nach Abschluss des Vorgangs wird die Meldung aktualisiert, und es wird „Beendet“ angezeigt. Außerdem werden Rückgabecodes und weitere Informationen angezeigt.

```azurecli
$ az hpc-cache start --name doc-cache0629
{- Finished ..
  "endTime": "2020-07-01T18:46:43.6862478+00:00",
  "name": "c48d320f-f5f5-40ab-8b25-0ac065984f62",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-01T18:40:28.5468983+00:00",
  "status": "Succeeded"
}
```

---

## <a name="flush-cached-data"></a>Leeren zwischengespeicherter Daten

Über die Schaltfläche **Leeren** auf der Übersichtsseite wird festgelegt, dass alle geänderten Daten im Cache sofort in die Back-End-Speicherziele geschrieben werden. Die Daten im Cache werden routinemäßig in den Speicherzielen gespeichert. Daher ist es nicht notwendig, dies manuell durchzuführen, es sei denn, Sie möchten sicherstellen, dass das Back-End-Speichersystem auf dem neuesten Stand ist. Beispielsweise können Sie **Leeren** verwenden, bevor Sie eine Momentaufnahme des Speichers erstellen oder die Größe des Datasets überprüfen.

> [!NOTE]
> Während der Leerung können im Cache keine Clientanforderungen verarbeitet werden. Der Cachezugriff wird angehalten und erst fortgesetzt, nachdem der Vorgang abgeschlossen wurde.

Wenn Sie den Leerungsvorgang für den Cache starten, werden im Cache keine Clientanforderungen mehr angenommen und der Cachestatus auf der Übersichtsseite ändert sich in **Wird geleert**.

Die Daten im Cache werden in den entsprechenden Speicherzielen gespeichert. Je nachdem, wie viele Daten geleert werden müssen, kann der Vorgang einige Minuten oder länger als eine Stunde dauern.

Nachdem alle Daten in den Speicherzielen gespeichert wurden, werden im Cache automatisch wieder Clientanforderungen angenommen. Als Cachestatus wird wieder **Fehlerfrei** angezeigt.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Um den Cache zu leeren, klicken Sie auf die Schaltfläche **Leeren** und dann auf **Ja**, um die Aktion zu bestätigen.

![Screenshot: Schaltflächen im oberen Bereich, Option „Leeren“ hervorgehoben und Popupnachricht, in der die Leerungsaktion beschrieben und die Frage „Möchten Sie den Vorgang fortsetzen?“ gestellt wird, einschließlich der Schaltflächen „Ja“ (Standard) und „Nein“](media/hpc-cache-flush.png)

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Mit dem Befehl [az hpc-cache flush](/cli/azure/hpc-cache#az_hpc_cache_flush) können Sie erzwingen, dass der Cache alle geänderten Daten in die Speicherziele schreibt.

Beispiel:

```azurecli
$ az hpc-cache flush --name doc-cache0629 --resource-group doc-rg
 - Running ..
```

Nach Abschluss der Leerung wird eine Erfolgsmeldung zurückgegeben.

```azurecli
{- Finished ..
  "endTime": "2020-07-09T17:26:13.9371983+00:00",
  "name": "c22f8e12-fcf0-49e5-b897-6a6e579b6489",
  "properties": {
    "output": "success"
  },
  "startTime": "2020-07-09T17:25:21.4278297+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="upgrade-cache-software"></a>Ausführen eines Upgrades für die Cachesoftware

Wenn eine neue Softwareversion verfügbar ist, wird die Schaltfläche **Aktualisieren** aktiviert. Oben auf der Seite sollte auch eine Meldung zur Aktualisierung der Software angezeigt werden.

![Screenshot: obere Schaltflächenleiste mit aktivierter Schaltfläche „Aktualisieren“](media/hpc-cache-upgrade-button.png)

Der Clientzugriff wird während eines Softwareupgrades nicht unterbrochen, die Leistung des Caches wird jedoch verlangsamt. Planen Sie die Aktualisierung der Software für Nebenzeiten oder innerhalb eines geplanten Wartungszeitraums.

Die Aktualisierung der Software kann mehrere Stunden in Anspruch nehmen. Bei Caches, die mit höherem Durchsatz konfiguriert wurden, dauert das Upgrade länger als bei Caches mit geringeren Spitzendurchsatzwerten.

Wenn ein Softwareupgrade verfügbar ist, haben Sie in etwa eine Woche Zeit, dieses manuell anzuwenden. Das Enddatum ist in der Upgrademeldung aufgeführt. Wenn Sie das Upgrade in diesem Zeitraum nicht durchführen, wird es in Azure automatisch auf den Cache angewandt. Die zeitliche Steuerung des automatischen Upgrades kann nicht konfiguriert werden. Wenn Sie Bedenken in Bezug auf die Auswirkungen für die Cacheleistung haben, sollten Sie das Softwareupgrade vor Ablauf des entsprechenden Zeitraums manuell durchführen.

Wenn Ihr Cache beim Überschreiten des Enddatums beendet wird, aktualisiert er die Software automatisch bei seinem nächsten Start. (Möglicherweise wird das Update nicht sofort gestartet, doch dies geschieht auf jeden Fall innerhalb der ersten Stunde.)

### <a name="portal"></a>[Portal](#tab/azure-portal)

Klicken Sie auf die Schaltfläche **Aktualisieren**, um das Softwareupdate zu starten. Der Cachestatus ändert sich in **Upgrade wird ausgeführt**, bis der Vorgang abgeschlossen ist.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Der Cache-Statusbericht in der Azure-Befehlszeilenschnittstelle enthält am Ende Informationen zu neuer Software. (Mit [az hpc-cache show](/cli/azure/hpc-cache#az_hpc_cache_show) können Sie das überprüfen.) Suchen Sie in der Meldung nach der Zeichenfolge „upgradeStatus“.

Verwenden Sie den Befehl [az hpc-cache upgrade-firmware](/cli/azure/hpc-cache#az_hpc_cache_upgrade-firmware), um das Update anzuwenden (sofern vorhanden).

Wenn kein Update verfügbar ist, bleibt dieser Vorgang wirkungslos.

In diesem Beispiel werden der Cachestatus (kein Update verfügbar) und die Ergebnisse des Befehls „upgrade-firmware“ angezeigt.

```azurecli
$ az hpc-cache show --name doc-cache0629
{
  "cacheSizeGb": 3072,
  "health": {
    "state": "Healthy",
    "statusDescription": "The cache is in Running state"
  },

<...>

  "tags": null,
  "type": "Microsoft.StorageCache/caches",
  "upgradeStatus": {
    "currentFirmwareVersion": "5.3.61",
    "firmwareUpdateDeadline": "0001-01-01T00:00:00+00:00",
    "firmwareUpdateStatus": "unavailable",
    "lastFirmwareUpdate": "2020-06-29T22:18:32.004822+00:00",
    "pendingFirmwareVersion": null
  }
}
$ az hpc-cache upgrade-firmware --name doc-cache0629
$
```

---

## <a name="collect-diagnostics"></a>Sammeln von Diagnosedaten

Mit der Schaltfläche **Collect diagnostics** (Diagnosedaten sammeln) wird der Prozess zum Sammeln und Übermitteln von Systeminformationen an den Microsoft-Kundendienst und -Support zur Problembehandlung manuell gestartet. Wenn ein schwerwiegendes Cacheproblem auftritt, werden die gleichen Diagnoseinformationen automatisch von Ihrem Cache erfasst und hochgeladen.

Verwenden Sie dieses Steuerelement, wenn dies vom Microsoft-Kundendienst und -Support angefordert wird.

Klicken Sie nach dem Klicken auf die Schaltfläche auf **Ja**, um den Upload zu bestätigen.

![Screenshot: Popupbestätigungsnachricht „Start diagnostics collection“ (Diagnosesammlung starten) Die Standardschaltfläche „Ja“ wird hervorgehoben.](media/diagnostics-confirm.png)

## <a name="delete-the-cache"></a>Löschen des Caches

Über die Schaltfläche **Löschen** wird der Cache zerstört. Wenn Sie einen Cache löschen, werden alle zugehörigen Ressourcen zerstört, und es fallen keine Kontogebühren mehr an.

Die Back-End-Speichervolumes, die als Speicherziele verwendet werden, sind nicht betroffen, wenn Sie den Cache löschen. Sie können sie später einem zukünftigen Cache hinzufügen oder ihre Verwendung einstellen.

> [!NOTE]
> Azure HPC Cache schreibt geänderte Daten nicht automatisch aus dem Cache in die Back-End-Speichersysteme, bevor der Cache gelöscht wird.
>
> Um sicherzustellen, dass alle Daten im Cache in den Langzeitspeicher geschrieben wurden, [beenden Sie den Cache](#stop-the-cache), bevor Sie ihn löschen. Vergewissern Sie sich vor dem Löschen, dass der Status **Beendet** angezeigt wird.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Nachdem Sie den Cache angehalten haben, klicken Sie auf die Schaltfläche **Löschen**, um den Cache dauerhaft zu entfernen.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

[Einrichten der Azure CLI für Azure HPC Cache](./az-cli-prerequisites.md).

Verwenden Sie in der Azure CLI den Befehl [az hpc-cache delete](/cli/azure/hpc-cache#az_hpc_cache_delete), um den Cache dauerhaft zu entfernen.

Beispiel:
```azurecli
$ az hpc-cache delete --name doc-cache0629
 - Running ..

<...>

{- Finished ..
  "endTime": "2020-07-09T22:24:35.1605019+00:00",
  "name": "7d3cd0ba-11b3-4180-8298-d9cafc9f22c1",
  "startTime": "2020-07-09T22:13:32.0732892+00:00",
  "status": "Succeeded"
}
$
```

---

## <a name="view-warnings"></a>Warnungen anzeigen

Wenn der Cache in einen fehlerhaften Zustand wechselt, überprüfen Sie die Seite **Warnungen**. Auf dieser Seite werden Benachrichtigungen der Cachesoftware angezeigt, die Ihnen möglicherweise helfen, den Zustand der Software zu verstehen.

Diese Benachrichtigungen werden nicht im Aktivitätsprotokoll angezeigt, da sie nicht vom Azure-Portal gesteuert werden. Sie sind häufig mit benutzerdefinierten Einstellungen verknüpft, die Sie unter Umständen vorgenommen haben.

Folgende Warnungen können bspw. angezeigt werden:

* Der Cache kann den NTP-Server nicht erreichen.
* Der Cache konnte die Benutzernamen von erweiterten Gruppen nicht herunterladen.
* Benutzerdefinierte DNS-Einstellungen wurden für ein Speicherziel geändert.

![Screenshot der Seite „Überwachung > Warnungen“ mit der Meldung, dass die Benutzernamen von erweiterten Gruppen nicht heruntergeladen werden konnten](media/warnings-page.png)

## <a name="next-steps"></a>Nächste Schritte

* [Überwachen des Cache mit Statistiken](metrics.md)
* Hilfe zu [Azure HPC Cache](hpc-cache-support-ticket.md)
