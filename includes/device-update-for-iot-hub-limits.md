---
author: vimeht
ms.author: vimeht
ms.date: 7/8/2021
ms.topic: include
ms.service: iot-hub-device-update
ms.openlocfilehash: 58977fdfb05a2f2b152d66fb12065d88a98b2b2e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121803545"
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
| Maximale Größe einer einzelnen Updatedatei | 800 MB | 800 MB | Nein |
| Maximale kombinierte Größe aller Dateien in einer einzelnen Importaktion | 800 MB | 800 MB | Nein |
| Anzahl von Gerätegruppen pro Instanz | 75 | 75 | Nein |
