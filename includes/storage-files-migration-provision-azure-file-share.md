---
title: Datei einfügen
description: include file
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: c1fda4eb853e97b7a5b85beebe162ab3869e4a68
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/15/2021
ms.locfileid: "113798908"
---
Eine Azure-Dateifreigabe wird in der Cloud in einem Azure-Speicherkonto gespeichert.
Im Hinblick auf die Leistung sollten dabei einige wichtige Aspekte berücksichtigt werden.

Wenn Ihre Freigaben intensiv genutzt werden, z. B. von einer großen Anzahl von Benutzern und/oder Anwendungen, wird die maximale Leistung eines Speicherkontos möglicherweise mit zwei Azure-Dateifreigaben erreicht.

Als bewährte Methode empfiehlt es sich, Speicherkonten mit je einer Dateifreigabe bereitzustellen.
Sie können mehrere Azure-Dateifreigaben in demselben Speicherkonto zusammenfassen, wenn Sie Archivierungsfreigaben verwenden oder nur eine geringe tägliche Aktivität erwarten.

Diese Überlegungen gelten eher für direkten Cloudzugriff (über eine Azure-VM oder Azure-Dateisynchronisierung) als für die Azure-Dateisynchronisierung. Wenn Sie Freigaben lediglich für die Azure-Dateisynchronisierung verwenden möchten, können Sie mehrere Freigaben in einem einzelnen Azure-Speicherkonto gruppieren.

Wenn Sie eine Liste Ihrer Freigaben erstellt haben, sollten Sie jede Freigabe dem Speicherkonto zuordnen, in dem sie sich befindet.

Im vorherigen Schritt haben Sie die geeignete Anzahl von Freigaben bestimmt. In diesem Schritt haben Sie eine Zuordnung von Speicherkonten und Dateifreigaben erstellt. Nun stellen Sie die geeignete Anzahl von Azure-Speicherkonten mit der geeigneten Anzahl von Azure-Dateifreigaben bereit.

Stellen Sie sicher, dass die Region der einzelnen Speicherkonten identisch ist und mit der Region der Speichersynchronisierungsdienst-Ressource übereinstimmt, die Sie bereits bereitgestellt haben.

> [!CAUTION]
> Wenn Sie eine Azure-Dateifreigabe mit maximal 100 TiB erstellen, kann diese Freigabe als Redundanzoptionen nur lokal redundanten Speicher oder zonenredundanten Speicher verwenden. Daher sollten Sie Ihre Speicherredundanzanforderungen berücksichtigen, bevor Sie Dateifreigabe mit 100 TiB verwenden.

Azure-Dateifreigaben werden standardmäßig weiterhin mit einem Grenzwert von 5 TiB erstellt. Führen Sie die unter [Erstellen einer Azure-Dateifreigabe](../articles/storage/files/storage-how-to-create-file-share.md) beschriebenen Schritte aus, um eine große Dateifreigabe zu erstellen.

Ein weiterer Aspekt, den Sie bei der Bereitstellung eines Speicherkontos berücksichtigen sollten, ist die Redundanz von Azure Storage. Weitere Informationen finden Sie unter [Redundanzoptionen von Azure Storage](../articles/storage/common/storage-redundancy.md).

Auch die Namen Ihrer Ressourcen sind wichtig. Wenn Sie z.B. mehrere Freigaben für die Personalabteilung in einem Azure-Speicherkonto gruppieren, sollten Sie einen entsprechenden Namen für das Speicherkonto wählen. Gleichermaßen sollten Sie beim Benennen Ihrer Azure-Dateifreigaben Namen verwenden, die denen der lokalen Entsprechungen ähneln.