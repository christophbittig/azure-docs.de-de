---
title: Data Residency für Azure Network Watcher | Microsoft-Dokumentation
description: In diesem Artikel erhalten Sie Informationen zur Data Residency für den Azure Network Watcher-Dienst.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 638c466e0d8573ce0b23f31188c5e952a28f74b2
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/16/2021
ms.locfileid: "112240462"
---
# <a name="data-residency-for-azure-network-watcher"></a>Data Residency für Azure Network Watcher
Mit Ausnahme des Diensts „Verbindungsmonitor“ speichert Azure Network Watcher keine Kundendaten.


## <a name="connection-monitor-data-residency"></a>Verbindungsmonitor: Datenresidenz
Der Dienst „Verbindungsmonitor“ speichert Kundendaten. Diese Daten werden von Network Watcher automatisch in einer einzelnen Region gespeichert. Verbindungsmonitor erfüllt also automatisch die regionsspezifischen Anforderungen an die Datenresidenz, z. B. wie im [Trust Center](https://azuredatacentermap.azurewebsites.net/) angegeben.

## <a name="data-residency"></a>Datenresidenz
Das Feature zum Aktivieren der Speicherung von Kundendaten in einer einzelnen Region ist in Azure derzeit nur in der Region „Asien, Südosten“ (Singapur) des geografischen Raums „Asien-Pazifik“ und in der Region „Brasilien, Süden“ (São Paulo, Bundesstaat) des geografischen Raums „Brasilien“ verfügbar. Bei allen anderen Regionen werden Kundendaten unter „Geografien“ gespeichert. Weitere Informationen finden Sie im [Trust Center](https://azuredatacentermap.azurewebsites.net/).

## <a name="next-steps"></a>Nächste Schritte

* Verschaffen Sie sich einen Überblick über [Network Watcher](./network-watcher-monitoring-overview.md).
