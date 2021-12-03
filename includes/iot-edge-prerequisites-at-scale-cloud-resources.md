---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4fe6d59d88cf4b0c32da4639a3f1132a82974c28
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845936"
---
### <a name="cloud-resources"></a>Cloudressourcen

* Ein aktiver IoT-Hub
* Eine Instanz des IoT Hub Device Provisioning Service (Gerätebereitstellungsdienst) in Azure, die mit Ihrem IoT-Hub verknüpft ist
  * Wenn Sie über keine Instanz des Device Provisioning Service verfügen, können Sie die Anweisungen in den Abschnitten [Erstellen eines neuen IoT Hub Device Provisioning Service](../articles/iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) und [Verknüpfen des IoT-Hubs und Ihres Gerätebereitstellungsdiensts](../articles/iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) der Schnellstartanleitung zum IoT Hub Device Provisioning Service befolgen.
  * Nachdem Sie den Device Provisioning-Dienst gestartet haben, kopieren Sie den Wert **ID-Bereich** von der Seite „Übersicht“. Sie können diesen Wert verwenden, wenn Sie IoT Edge-Runtime konfigurieren.
