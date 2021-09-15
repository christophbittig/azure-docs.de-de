---
title: Data Residency für Kundendaten in Azure IoT Central | Microsoft-Dokumentation
description: In diesem Artikel wird Data Residency von Kundendaten in der Azure IoT Central-Anwendung beschrieben.
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 2086ab1e899b85f52391187425c85eeb86b21c0e
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481162"
---
# <a name="azure-iot-central-customer-data-residency"></a>Data Residency von Kundendaten in Azure IoT Central

In IoT Central werden keine Kundendaten außerhalb der vom Kunden angegebenen geografischen Region gespeichert, außer in den folgenden Szenarien:

- Wenn einer bestehenden IoT Central-Anwendung ein neuer Benutzer hinzugefügt wird, kann die E-Mail-ID des Benutzers außerhalb der geografischen Region gespeichert werden, bis der eingeladene Benutzer erstmals auf die Anwendung zugreift.

- Auf den Kartenkacheln des IoT Central-Dashboard wird [Azure Maps](../../azure-maps/about-azure-maps.md) verwendet. Wenn Sie einer bestehenden IoT Central-Anwendung eine Kartenkachel hinzufügen, werden die Standortdaten ggf. gemäß den Geolokalisierungsregeln des Diensts Azure Maps verarbeitet oder gespeichert.
