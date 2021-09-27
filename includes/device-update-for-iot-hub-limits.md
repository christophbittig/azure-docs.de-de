---
author: vimeht
ms.author: vimeht
ms.date: 7/8/2021
ms.topic: include
ms.service: iot-hub-device-update
ms.openlocfilehash: d7970fff815449adf6412c2748e22b2d471e52b0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128645469"
---
 > [!NOTE]
 > Wenn eine bestimmte Ressource oder ein bestimmter Vorgang über keine anpassbaren Grenzwerte verfügt, sind Standardlimit und maximales Limit gleich.
 > Wenn der Grenzwert angepasst werden kann, enthält die Tabelle unterschiedliche Werte für „Standardlimit“ und „Maximales Limit“. Der Grenzwert kann über den Standardgrenzwert, aber nicht über den maximalen Grenzwert angehoben werden.
 > Wenn Sie den Grenzwert oder das Kontingent über das Standardlimit hinaus erhöhen möchten, richten Sie eine [Anfrage an den Onlinekundensupport](https://azure.microsoft.com/support/options/).


Die folgende Tabelle enthält die Grenzwerte für die Ressource „Device Update for IoT Hub“ in Azure Resource Manager:

| Resource |  Standardlimit | Maximales Limit | Anpassbar? |
| --- | --- | --- | --- |
| Konten pro Abonnement | 2 | 25 | Ja |
| Instanzen pro Konto | 2 | 25 | Ja |
| Länge des Kontonamens | Mindestwert: 3 <br/> Höchstwert: 24 | Mindestwert: 3 <br/> Höchstwert: 24 | Nein |
| Länge des Instanznamens | Mindestwert: 3 <br/> Höchstwert: 36 | Mindestwert: 3 <br/> Höchstwert: 36 | Nein |



Die folgende Tabelle enthält die verschiedenen Grenzwerte im Zusammenhang mit Vorgängen in Device Update for IoT Hub:

| Vorgang |  Standardlimit | Maximales Limit | Anpassbar? |
| --- | --- | --- | --- |
| Anzahl von Geräten pro Instanz | 10.000 | 10.000 | Nein |
| Anzahl von Updateanbietern pro Instanz | 25 | 25 | Nein |
| Anzahl von Updatenamen pro Anbieter und Instanz | 25 | 25 | Nein |
| Anzahl von Updateversionen pro Updateanbieter und Name pro Instanz | 100 | 100 | Nein |
| Gesamtanzahl von Updates pro Instanz | 100 | 100 | Nein |
| Maximale Größe einer einzelnen Updatedatei | 2 GB | 2 GB | Nein |
| Maximale kombinierte Größe aller Dateien in einer einzelnen Importaktion | 2 GB | 2 GB | Nein |
| Anzahl von Gerätegruppen pro Instanz | 75 | 75 | Nein |
