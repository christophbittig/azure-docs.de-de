---
title: Ändern der Topologie für die Azure-Dateisynchronisierung | Microsoft-Dokumentation
description: Anleitung zum Ändern der Topologie für die Azure-Dateisynchronisierung
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 4/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 51fc737284e62d1efd3a947e29ea71a4bb2594a0
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/14/2021
ms.locfileid: "113768666"
---
# <a name="modify-your-azure-file-sync-topology"></a>Ändern der Topologie für die Azure-Dateisynchronisierung

In diesem Artikel werden die gängigsten Methoden beschrieben, die Kunden zum Ändern der Topologie für die Azure-Dateisynchronisierung zur Verfügung stehen. Außerdem werden Empfehlungen für die Vorgehensweise vorgestellt. Wenn Sie Ihre Topologie für die Azure-Dateisynchronisierung ändern möchten, lesen Sie die nachstehenden Best Practices, um Fehler und/oder einen potenziellen Datenverlust zu vermeiden.

## <a name="migrate-a-server-endpoint-to-a-different-azure-file-sync-storage-sync-service"></a>Migrieren eines Serverendpunkts zu einem anderen Speichersynchronisierungsdienst für die Azure-Dateisynchronisierung

Nachdem Sie sichergestellt haben, dass Ihre Daten auf dem lokalen Server auf dem neuesten Stand sind, heben Sie die Bereitstellung Ihres Serverendpunkts auf. Ausführliche Anweisungen hierzu finden Sie unter [Aufheben der Bereitstellung Ihres Serverendpunkts für die Azure-Dateisynchronisierung](./file-sync-server-endpoint-delete.md#scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share). Führen Sie anschließend eine erneute Bereitstellung in der gewünschten Synchronisierungsgruppe und im Speichersynchronisierungsdienst durch.

Wenn Sie alle Serverendpunkte, die einem Server zugeordnet sind, zu einer anderen Synchronisierungsgruppe oder einem anderen Speichersynchronisierungsdienst migrieren möchten, finden Sie weitere Informationen unter [Aufheben der Bereitstellung aller Serverendpunkte, die einem registrierten Server zugeordnet sind](#deprovision-all-server-endpoints-associated-with-a-registered-server).

## <a name="change-the-granularity-of-a-server-endpoint"></a>Ändern der Granularität eines Serverendpunkts

Nachdem Sie sich vergewissert haben, dass Ihre Daten auf dem lokalen Server auf dem neuesten Stand sind (siehe [Aufheben der Bereitstellung Ihres Serverendpunkts für die Azure-Dateisynchronisierung](./file-sync-server-endpoint-delete.md#scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share)), heben Sie die Bereitstellung Ihres Serverendpunkts auf. Führen Sie dann eine erneute Bereitstellung mit der gewünschten Granularität durch.

## <a name="deprovision-azure-file-sync-topology"></a>Aufheben der Bereitstellung der Topologie für die Azure-Dateisynchronisierung

Die Bereitstellung von Ressourcen für die Azure-Dateisynchronisierung muss in einer bestimmten Reihenfolge aufgehoben werden: Serverendpunkte, Synchronisierungsgruppe, Speicherdienst. Nachstehend wird der gesamte Ablauf dokumentiert ist, Sie können den Vorgang jedoch an einem beliebigen Punkt beenden. 

Navigieren Sie zunächst im Azure-Portal zur Ressource für den Speichersynchronisierungsdienst, und wählen Sie im Speichersynchronisierungsdienst eine Synchronisierungsgruppe aus. Führen Sie die Schritte unter [Aufheben der Bereitstellung Ihres Serverendpunkts für die Azure-Dateisynchronisierung](./file-sync-server-endpoint-delete.md) aus, um Datenintegrität und -verfügbarkeit beim Löschen von Serverendpunkten sicherzustellen. Um die Bereitstellung Ihrer Synchronisierungsgruppe oder des Speichersynchronisierungsdiensts aufzuheben, müssen alle Serverendpunkte gelöscht werden. Wenn Sie nur bestimmte Serverendpunkte löschen möchten, können Sie den Vorgang hier beenden. 

Nachdem Sie alle Serverendpunkte in der Synchronisierungsgruppe gelöscht haben, löschen Sie den Cloudendpunkt. 

Löschen Sie anschließend die Synchronisierungsgruppe. 

Wiederholen Sie diese Schritte für alle Synchronisierungsgruppen im Speichersynchronisierungsdienst, die Sie löschen möchten. Nachdem alle Synchronisierungsgruppen in diesem Speichersynchronisierungsdienst gelöscht wurden, löschen Sie die Ressource für den Speichersynchronisierungsdienst.

## <a name="change-a-server-endpoint-path"></a>Ändern eines Serverendpunktpfads

Ein Serverendpunktpfad ist eine unveränderliche Eigenschaft. Die Auswahl eines anderen Speicherorts auf dem Server hat Auswirkungen auf die Daten am alten Speicherort, auf die Azure-Dateifreigabe und auf den neuen Speicherort. Die Verhaltensweise bliebe in den meisten Fällen undefiniert, wenn Sie einfach den Pfad ändern würden. Sie können einen Serverendpunkt nur entfernen und dann einen neuen Serverendpunkt mit dem neuen Pfad erstellen. Berücksichtigen Sie unbedingt den Synchronisierungsstatus Ihres Servers, um den richtigen Zeitpunkt für diese weitreichende Änderung zu finden.

Das Löschen eines Serverendpunkts ist nicht unerheblich und kann bei falscher Vorgehensweise zu einem Datenverlust führen. Im [Artikel zum Löschen des Serverendpunkts](file-sync-server-endpoint-delete.md) werden Sie durch den Vorgang geleitet.

Wenn Sie derzeit Laufwerk D verwenden und eine Migration zur Cloud planen, finden Sie weitere Informationen unter [Verwenden des Laufwerks „D:“ einer VM als Datenlaufwerk](../../virtual-machines/windows/change-drive-letter.md).

## <a name="rename-a-sync-group"></a>Umbenennen einer Synchronisierungsgruppe

Eine Synchronisierungsgruppe kann nicht umbenannt werden. Der Name ist Bestandteil der URL, mit der der Cloudendpunkt und die Serverendpunkte der untergeordneten Ressourcen gespeichert und verwaltet werden. Wählen Sie den Namen sorgfältig aus, wenn Sie die Ressource erstellen.

## <a name="deprovision-all-server-endpoints-associated-with-a-registered-server"></a>Aufheben der Bereitstellung aller Serverendpunkte, die einem registrierten Server zugeordnet sind

Um vor dem Aufheben der Bereitstellung sicherzustellen, dass Ihre Daten sicher sind und vollständig aktualisiert wurden, lesen Sie den Artikel [Aufheben der Bereitstellung Ihres Serverendpunkts für die Azure-Dateisynchronisierung](./file-sync-server-endpoint-delete.md).

Eine Massenentfernung aller Serverendpunkte sollte nur durchgeführt werden, wenn es sich um eine Testbereitstellung mit entbehrlichen Daten auf dem Server und in der Cloud handelt. Das Aufheben der Registrierung des Servers beim *Speichersynchronisierungsdienst* der Azure-Dateisynchronisierung führt zu einer Massenentfernung aller Serverendpunkte. Bei Verwendung dieser Methode ist ein Datenverlust wahrscheinlich. 

Wenn Sie die Registrierung eines Servers unabhängig von den negativen Auswirkungen aufheben möchten, navigieren Sie zu Ihrer Speichersynchronisierungsdienst-Ressource, und wechseln Sie zur Registerkarte **Registrierte Server**. Wählen Sie den Server aus, dessen Registrierung Sie aufheben möchten, und klicken Sie auf **Serverregistrierung aufheben**. Alle Serverendpunkte, die diesem Server zugeordnet sind, werden entfernt.

## <a name="next-steps"></a>Nächste Schritte
* [Aufheben der Bereitstellung Ihres Serverendpunkts für die Azure-Dateisynchronisierung](./file-sync-server-endpoint-delete.md)


