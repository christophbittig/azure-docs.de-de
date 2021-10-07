---
title: Wiederherstellen einer App aus einer Momentaufnahme
description: Hier erfahren Sie, wie Sie Ihre App auf der Grundlage einer Momentaufnahme wiederherstellen. Wiederherstellung nach unerwartetem Datenverlust im Premium-Tarif mit automatischen Schattenkopien.
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.topic: article
ms.date: 09/02/2021
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: 48b3d859a73a2d63644e1d5881c3505cee93f9d7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679501"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Wiederherstellen einer App in Azure auf der Grundlage einer Momentaufnahme
In diesem Artikel erfahren Sie, wie Sie eine App in [Azure App Service](../app-service/overview.md) auf der Grundlage einer Momentaufnahme wiederherstellen. Mithilfe der Momentaufnahmen Ihrer App können Sie die App in einem vorherigen Zustand wiederherstellen. Momentaufnahmen müssen nicht aktiviert werden, da die Plattform automatisch eine Momentaufnahme aller Apps speichert, um die Wiederherstellung von Daten zu ermöglichen.

Momentaufnahmen sind inkrementelle Schattenkopien Ihrer App Service-App. Wenn sich Ihre App im Tarif „Premium“ oder höher befindet, erstellt App Service regelmäßige Momentaufnahmen sowohl des Inhalts als auch der Konfiguration der App. Sie bieten gegenüber [Standardsicherungen](manage-backup.md) mehrere Vorteile:

- Keine Dateikopierfehler durch Dateisperren
- Höhere maximale Momentaufnahmegröße (30 GB).
- Für unterstützte Tarife ist keine Konfiguration erforderlich.
- Momentaufnahmen können in einer neuen App Service-App in jeder Azure-Region wiederhergestellt werden.

Momentaufnahmebasierte Wiederherstellungen für Apps stehen ab dem Tarif **Premium** zur Verfügung. Informationen zum Hochskalieren der App finden Sie unter [Hochskalieren einer App in Azure](manage-scale-up.md).

## <a name="limitations"></a>Einschränkungen

- Die maximal unterstützte Größe für die Momentaufnahmewiederherstellung beträgt 30 GB. Die Momentaufnahmewiederherstellung schlägt fehl, wenn Ihre Speichergröße 30 GB überschreitet. Um Ihre Speichergröße zu reduzieren, sollten Sie z. B. Dateien wie Protokolle, Bilder, Audiodateien und Videos in [Azure Storage](../storage/index.yml) verschieben.
- Jede verbundene Datenbank, die von der [Standardsicherung](manage-backup.md#what-gets-backed-up) unterstützt wird, oder [eingebundener Azure-Speicher](configure-connect-to-azure-storage.md?pivots=container-windows) wird *nicht* in die Momentaufnahme aufgenommen. Erwägen Sie die Verwendung der nativen Sicherungsfunktionen des verbundenen Azure-Diensts (z. B. [SQL-Datenbank](../azure-sql/database/automated-backups-overview.md) und [Azure Files](../storage/files/storage-snapshots-files.md)).
- App Service beendet die Ziel-App oder den Zielslot während der Wiederherstellung einer Momentaufnahme. Um Ausfallzeiten für die Produktions-App zu minimieren, stellen Sie die Momentaufnahme zuerst in einem [Stagingslot](deploy-staging-slots.md) wieder her, und wechseln Sie dann in die Produktion.
- Momentaufnahmen für die letzten 30 Tage sind verfügbar. Der Aufbewahrungszeitraum und die Häufigkeit von Momentaufnahmen lassen sich nicht konfigurieren.
- App Services, die in einer App Service-Umgebung ausgeführt werden, unterstützen keine Momentaufnahmen.

## <a name="restore-an-app-from-a-snapshot"></a>Wiederherstellen einer App auf der Grundlage einer Momentaufnahme

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf der Seite **Einstellungen** Ihrer App auf **Sicherungen**, um die Seite **Sicherungen** anzuzeigen. Klicken Sie anschließend im Abschnitt **Momentaufnahme (Vorschau)** auf **Wiederherstellen**.
   
    ![Screenshot der Wiederherstellung einer App aus einer Momentaufnahme.](./media/app-service-web-restore-snapshots/1.png)

2. Wählen Sie auf der Seite **Wiederherstellen** die Momentaufnahme aus, die Sie wiederherstellen möchten.
   
    ![Screenshot: Auswählen der Momentaufnahme für die Wiederherstellung ](./media/app-service-web-restore-snapshots/2.png)
   
3. Geben Sie unter **Wiederherstellungsziel** das Ziel für die App-Wiederherstellung an.
   
    ![Screenshot: Angeben des Wiederherstellungsziels](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Als bewährte Methode wird empfohlen, in einem neuen Slot wiederherzustellen und anschließend einen Austausch durchzuführen. Bei Verwendung von **Überschreiben** werden alle im aktuellen Dateisystem Ihrer App vorhandenen Daten gelöscht und überschrieben. Vergewissern Sie sich vor dem Klicken auf **OK**, dass Sie diesen Schritt wirklich ausführen möchten.
   > 
   > 
      
   > [!Note]
   > Aufgrund aktueller technischer Einschränkungen sind nur App-Wiederherstellungen in der gleichen Skalierungseinheit möglich. Diese Einschränkung wird in einer späteren Version aufgehoben.
   > 
   > 
   
    Sie können **Vorhandene App** auswählen, um einen Slot als Wiederherstellungsziel zu verwenden. Wenn Sie diese Option verwenden möchten, müssen Sie in Ihrer App bereits einen Slot erstellt haben.

4. Sie können auswählen, dass Ihre Websitekonfiguration wiederhergestellt werden soll.
   
    ![Screenshot: Wiederherstellen der Websitekonfiguration](./media/app-service-web-restore-snapshots/4.png)

5. Klicken Sie auf **OK**.
