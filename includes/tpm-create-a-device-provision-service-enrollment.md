---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6bc968f7763131c4c12e7d523619029698e4ac09
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131579336"
---
## <a name="create-a-device-provisioning-service-enrollment"></a>Erstellen einer DPS-Registrierung (Device Provisioning Service)

Verwenden Sie die Bereitstellungsinformationen Ihres TPM, um eine individuelle Registrierung in Device Provisioning Service (DPS) zu erstellen.

Wenn Sie eine Registrierung in DPS erstellen, haben Sie die Möglichkeit, einen **anfänglichen Gerätezwillingsstatus** zu deklarieren. Im Gerätezwilling können Sie Tags zum Gruppieren von Geräten nach jeder beliebigen Metrik – z. B. Region, Umgebung, Speicherort oder Gerätetyp – festlegen, die in Ihrer Lösung verwendet wird. Diese Tags werden zum Erstellen von [automatischen Bereitstellungen](../articles/iot-edge/how-to-deploy-at-scale.md) verwendet.

> [!TIP]
> Die Schritte in diesem Artikel beziehen sich auf das Azure-Portal. Sie können aber auch individuelle Registrierungen über die Azure CLI erstellen. Weitere Informationen finden Sie unter [az iot dps-Registrierung](/cli/azure/iot/dps/enrollment). Verwenden Sie als Teil des CLI-Befehls das Flag **edge-enabled** (Edge-fähig), um anzugeben, dass die Registrierung für ein IoT Edge-Gerät gilt.

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Instanz von IoT Hub Device Provisioning Service.

1. Klicken Sie in **Einstellungen** auf **Registrierungen verwalten**.

1. Wählen Sie **Individuelle Registrierung hinzufügen** aus, und führen Sie dann die folgenden Schritte zum Konfigurieren der Registrierung aus:

   1. Klicken Sie unter **Mechanismus** auf die Option **TPM**.

   1. Geben Sie den **Endorsement Key** und die **Registrierungs-ID** an, den bzw. die Sie von Ihrer VM oder Ihrem physischen Gerät kopiert haben.

   1. Geben Sie bei Bedarf eine ID für Ihr Gerät an. Wenn Sie keine Geräte-ID angeben, wird die Registrierungs-ID verwendet.

   1. Wählen Sie **True** aus, um zu deklarieren, dass Ihre VM oder Ihr physisches Gerät ein IoT Edge-Gerät ist.

   1. Wählen Sie die verknüpfte IoT Hub-Instanz, mit der Sie Ihr Gerät verbinden möchten, oder **Link to new IoT Hub** (Mit neuer IoT Hub-Instanz verknüpfen) aus. Sie können mehrere Hubs auswählen. Das Gerät wird dann je nach gewählter Zuweisungsrichtlinie einem dieser Hubs zugewiesen.

   1. Fügen Sie bei Bedarf einen Tagwert zu **Anfänglicher Status von Gerätezwilling** hinzu. Sie können mithilfe von Tags Gruppen von Geräten als Ziel für die Modulbereitstellung festlegen. Weitere Informationen finden Sie unter [Bedarfsgerechtes Bereitstellen von IoT Edge-Modulen mithilfe des Azure-Portals](../articles/iot-edge/how-to-deploy-at-scale.md).

   1. Wählen Sie **Speichern** aus.

Nachdem nun eine Registrierung für dieses Gerät vorhanden ist, kann die IoT Edge-Runtime das Gerät während der Installation automatisch bereitstellen.
