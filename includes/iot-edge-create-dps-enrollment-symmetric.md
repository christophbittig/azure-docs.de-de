---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 92c933cf2f504459359652124f72ca959241db92
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505941"
---
## <a name="create-a-dps-enrollment"></a>Erstellen einer DPS-Registrierung

Erstellen Sie eine Registrierung, um ein oder mehrere Geräte über DPS bereitzustellen.

Wenn Sie ein einzelnes IoT Edge-Gerät bereitstellen möchten, erstellen Sie eine **individuelle Registrierung**. Wenn Sie mehrere Geräte bereitstellen müssen, führen Sie die Schritte zum Erstellen einer DPS-**Gruppenregistrierung** aus.

Wenn Sie eine Registrierung im DPS erstellen, haben Sie die Möglichkeit, einen **Anfangszustand des Gerätezwillings** zu deklarieren. Im Gerätezwilling können Sie Tags zum Gruppieren von Geräten nach jeder beliebigen Metrik, z.B. Region, Umgebung, Speicherort oder Geräte, festlegen, die Sie in Ihrer Projektmappe benötigen. Diese Tags werden zum Erstellen von [automatischen Bereitstellungen](../articles/iot-edge/how-to-deploy-at-scale.md) verwendet.

Weitere Informationen zu Registrierungen im Device Provisioning Service finden Sie unter [Verwalten von Geräteregistrierungen](../articles/iot-dps/how-to-manage-enrollments.md).

# <a name="individual-enrollment"></a>[Individuelle Registrierung](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>Erstellen einer individuellen DPS-Registrierung

> [!TIP]
> Die Schritte in diesem Artikel gelten für das Azure-Portal. Sie können individuelle Registrierungen aber auch mithilfe der Azure CLI erstellen. Weitere Informationen finden Sie unter [az iot dps-Registrierung](/cli/azure/iot/dps/enrollment). Verwenden Sie als Teil des CLI-Befehls das Flag **edge-enabled** (Edge-fähig), um anzugeben, dass die Registrierung für ein IoT Edge-Gerät gilt.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Instanz des IoT Hub Device Provisioning Service.

1. Klicken Sie in **Einstellungen** auf **Registrierungen verwalten**.

1. Klicken Sie auf **Individuelle Registrierung hinzufügen**, und führen Sie dann die folgenden Schritte aus, um die Registrierung zu konfigurieren:  

   1. Wählen Sie unter **Mechanismus** die Option **Symmetrischer Schlüssel** aus.

   1. Geben Sie eine eindeutige **Registrierungs-ID** für Ihr Gerät an.

   1. Optional können Sie eine **IoT Hub-Geräte-ID** für Ihr Gerät angeben. Sie können mithilfe von Geräte-IDs ein einzelnes Gerät als Ziel für die Modulbereitstellung festlegen. Wenn Sie keine Geräte-ID angeben, wird die Registrierungs-ID verwendet.

   1. Wählen Sie **True** aus, um anzugeben, dass die Registrierung für ein IoT Edge-Gerät erfolgt.

   1. Fügen Sie optional einen Tagwert zu **Anfänglicher Status von Gerätezwilling** hinzu. Sie können mithilfe von Tags Gruppen von Geräten als Ziel für die Modulbereitstellung festlegen. Beispiel:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Wählen Sie **Speichern** aus.

1. Kopieren Sie den Wert **Primärschlüssel** der individuellen Registrierung, der beim Installieren der IoT Edge-Runtime verwendet werden soll.

Nachdem nun eine Registrierung für dieses Gerät vorhanden ist, kann die IoT Edge-Runtime das Gerät während der Installation automatisch bereitstellen.

# <a name="group-enrollment"></a>[Gruppenregistrierung](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>Erstellen einer DPS-Gruppenregistrierung

> [!TIP]
> Die Schritte in diesem Artikel gelten für das Azure-Portal. Sie können Gruppenregistrierungen aber auch mithilfe der Azure CLI erstellen. Weitere Informationen finden Sie unter [az iot dps-Registrierungsgruppe](/cli/azure/iot/dps/enrollment-group). Verwenden Sie als Teil des CLI-Befehls das Flag **edge-enabled** (Edge-fähig), um anzugeben, dass die Registrierung für IoT Edge-Geräte gilt. Bei einer Gruppenregistrierung müssen alle Geräte IoT Edge-Geräte sein, oder keines von ihnen darf ein IoT Edge-Gerät sein.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Instanz des IoT Hub Device Provisioning Service.

1. Klicken Sie in **Einstellungen** auf **Registrierungen verwalten**.

1. Klicken Sie auf **Individuelle Registrierung hinzufügen**, und führen Sie dann die folgenden Schritte aus, um die Registrierung zu konfigurieren:  

   1. Geben Sie einen Wert für **Gruppenname** an.

   1. Wählen Sie als Nachweistyp **Symmetrischer Schlüssel** aus.

   1. Wählen Sie **True** aus, um anzugeben, dass die Registrierung für ein IoT Edge-Gerät erfolgt. Bei einer Gruppenregistrierung müssen alle Geräte IoT Edge-Geräte sein, oder keines von ihnen darf ein IoT Edge-Gerät sein.

   1. Fügen Sie optional einen Tagwert zu **Anfänglicher Status von Gerätezwilling** hinzu. Sie können mithilfe von Tags Gruppen von Geräten als Ziel für die Modulbereitstellung festlegen. Beispiel:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Wählen Sie **Speichern** aus.

1. Kopieren Sie den Wert für **Primärschlüssel** Ihrer Registrierungsgruppe, der beim Erstellen von Geräteschlüsseln für eine Gruppenregistrierung verwendet werden soll.

Da es jetzt eine Registrierungsgruppe gibt, kann die IoT Edge-Runtime Geräte während der Installation automatisch bereitstellen.

#### <a name="derive-a-device-key"></a>Ableiten eines Geräteschlüssels

Jedes Gerät, das im Rahmen einer Gruppenregistrierung bereitgestellt wird, benötigt einen abgeleiteten Geräteschlüssel, um während der Bereitstellung einen Nachweis des symmetrischen Schlüssels bei der Registrierung durchzuführen.

Verwenden Sie zum Generieren eines Geräteschlüssels den aus Ihrer DPS-Registrierungsgruppe kopierten Schlüssel, um einen [HMAC-SHA256](https://wikipedia.org/wiki/HMAC)-Wert für die eindeutige Registrierungs-ID des Geräts zu berechnen und das Ergebnis in das Base64-Format zu konvertieren.

> [!IMPORTANT]
> Nehmen Sie den Primär- oder Sekundärschlüssel Ihrer Registrierung nicht in den Gerätecode auf.

Unter Windows können Sie Ihren abgeleiteten Geräteschlüssel mithilfe von PowerShell generieren, wie im folgenden Beispiel gezeigt wird.

Ersetzen Sie den Wert von **KEY** durch den **Primärschlüssel**, den Sie zuvor notiert haben.

Ersetzen Sie den Wert von **REG_ID** durch die Registrierungs-ID Ihres Geräts.

```powershell
$KEY='PASTE_YOUR_ENROLLMENT_KEY_HERE'
$REG_ID='PASTE_YOUR_REGISTRATION_ID_HERE'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

Hier ist eine Beispielausgabe eines abgeleiteten Geräteschlüssels:

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

---
