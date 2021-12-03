---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 16338971721edfb68c93821ddf08c5cc36caf0b3
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845993"
---
## <a name="create-a-dps-enrollment"></a>Erstellen einer DPS-Registrierung

Verwenden Sie Ihre generierten Zertifikate und Schlüssel, um in DPS eine Registrierung für ein oder mehrere IoT Edge-Geräte zu erstellen.

Wenn Sie ein einzelnes IoT Edge-Gerät bereitstellen möchten, erstellen Sie eine **individuelle Registrierung**. Wenn Sie mehrere Geräte bereitstellen müssen, führen Sie die Schritte zum Erstellen einer DPS-**Gruppenregistrierung** aus.

Wenn Sie eine Registrierung im DPS erstellen, haben Sie die Möglichkeit zum Angeben von **Anfänglicher Status von Gerätezwilling**. Im Gerätezwilling können Sie Tags zum Gruppieren von Geräten nach jeder beliebigen Metrik, z.B. Region, Umgebung, Speicherort oder Geräte, festlegen, die Sie in Ihrer Projektmappe benötigen. Diese Tags werden zum Erstellen von [automatischen Bereitstellungen](../articles/iot-edge/how-to-deploy-at-scale.md) verwendet.

Weitere Informationen zu Registrierungen im Device Provisioning Service finden Sie unter [Verwalten von Geräteregistrierungen](../articles/iot-dps/how-to-manage-enrollments.md).

# <a name="individual-enrollment"></a>[Individuelle Registrierung](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>Erstellen einer individuellen DPS-Registrierung

Individuelle Registrierungen verwenden den öffentlichen Teil des Identitätszertifikats eines Geräts und gleichen es mit dem Zertifikat auf dem Gerät ab.

> [!TIP]
> Die Schritte in diesem Artikel gelten für das Azure-Portal. Sie können individuelle Registrierungen aber auch mithilfe der Azure CLI erstellen. Weitere Informationen finden Sie unter [az iot dps-Registrierung](/cli/azure/iot/dps/enrollment). Verwenden Sie als Teil des CLI-Befehls das Flag **edge-enabled** (Edge-fähig), um anzugeben, dass die Registrierung für ein IoT Edge-Gerät gilt.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Instanz des IoT Hub Device Provisioning Service.

1. Klicken Sie in **Einstellungen** auf **Registrierungen verwalten**.

1. Klicken Sie auf **Individuelle Registrierung hinzufügen**, und führen Sie dann die folgenden Schritte aus, um die Registrierung zu konfigurieren:  

   * **Mechanismus**: Wählen Sie **X.509** aus.

   * **Primäres Zertifikat (PEM- oder CER-Datei)** : Laden Sie die öffentliche Datei aus dem Geräteidentitätszertifikat hoch. Wenn Sie die Skripts zum Generieren eines Testzertifikats verwendet haben, wählen Sie die folgende Datei aus:

      `<WRKDIR>\certs\iot-edge-device-identity-<name>.cert.pem`

   * **IoT Hub-Geräte-ID**: Stellen Sie nach Wunsch eine ID für Ihr Gerät bereit. Sie können mithilfe von Geräte-IDs ein einzelnes Gerät als Ziel für die Modulbereitstellung festlegen. Wenn Sie keine Geräte-ID angeben, wird der allgemeine Name (Common Name, CN) im X.509-Zertifikat verwendet.

   * **IoT Edge-Gerät**: Wählen Sie **True** aus, um anzugeben, dass die Registrierung für ein IoT Edge-Gerät erfolgt.

   * **Wählen Sie die IoT-Hubs aus, denen dieses Gerät zugewiesen werden kann**: Wählen Sie den verknüpften IoT-Hub, mit dem Sie Ihr Gerät verbinden möchten. Sie können mehrere Hubs auswählen, und das Gerät wird dann je nach gewählter Zuteilungsrichtlinie einem dieser Hubs zugewiesen.

   * **Anfangszustand des Gerätezwillings**: Fügen Sie einen Tagwert hinzu, der dem Gerätezwilling bei Bedarf hinzugefügt werden soll. Mithilfe von Tags können Sie Gruppen von Geräten als Ziel für die automatische Bereitstellung festlegen. Beispiel:

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

Unter **Registrierungen verwalten** wird die **Registrierungs-ID** für die Registrierung angezeigt, die Sie gerade erstellt haben. Notieren Sie sich diese ID, da sie bei der Bereitstellung Ihres Geräts verwendet werden kann.

Nachdem nun eine Registrierung für dieses Gerät vorhanden ist, kann die IoT Edge-Runtime das Gerät während der Installation automatisch bereitstellen.

# <a name="group-enrollment"></a>[Gruppenregistrierung](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>Erstellen einer DPS-Gruppenregistrierung

Bei Gruppenregistrierungen wird ein Zwischenzertifikat oder ein Stammzertifikat der Zertifizierungsstelle aus der Zertifikatvertrauenskette verwendet, das zum Generieren der einzelnen Geräteidentitätszertifikate verwendet wurde.

#### <a name="verify-your-root-certificate"></a>Überprüfen Ihres Stammzertifikats

Wenn Sie eine Registrierungsgruppe erstellen, haben Sie die Möglichkeit, ein überprüftes Zertifikat zu verwenden. Sie können ein Zertifikat mit DPS überprüfen, indem Sie den Besitz des Stammzertifikats nachweisen. Weitere Informationen finden Sie unter [Nachweis des Besitzes für X.509-Zertifizierungsstellenzertifikate](../articles/iot-dps/how-to-verify-certificates.md).

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Instanz des IoT Hub Device Provisioning Service.

1. Wählen Sie im Menü auf der linken Seite **Zertifikate** aus.

1. Wählen Sie **Hinzufügen** aus, um ein neues Zertifikat hinzuzufügen.

1. Geben Sie einen Anzeigenamen für Ihr Zertifikat ein, und navigieren Sie zu der CER- oder PEM-Datei, die den öffentlichen Teil Ihres X.509-Zertifikats darstellt.

   Wenn Sie die Demozertifikate verwenden, laden Sie das Zertifikat `<wrkdir>\certs\azure-iot-test-only.root.ca.cert.pem` hoch.

1. Wählen Sie **Speichern** aus.

1. Ihr Zertifikat sollte nun auf der Seite **Zertifikate** aufgeführt werden. Wählen Sie es aus, um die Zertifikatdetails zu öffnen.

1. Wählen Sie **Prüfcode generieren**  aus, und kopieren Sie den generierten Code.

1. Unabhängig davon, ob Sie Ihr eigenes Zertifizierungsstellenzertifikat verwendet haben oder die Demozertifikate verwenden, können Sie das Überprüfungstool aus dem IoT Edge-Repository verwenden, um den Nachweis des Besitzes zu überprüfen. Das Überprüfungstool verwendet Ihr Zertifizierungsstellenzertifikat zum Signieren eines neuen Zertifikats mit dem angegebenen Prüfcode als Antragstellername.

   ```powershell
   New-CACertsVerificationCert "<verification code>"
   ```

1. Laden Sie im Azure-Portal auf derselben Seite „Zertifikatdetails“ das neu generierte Verifizierungszertifikat hoch.

1. Wählen Sie **Überprüfen** aus.

#### <a name="create-enrollment-group"></a>Erstellen einer Registrierungsgruppe

Weitere Informationen zu Registrierungen im Device Provisioning Service finden Sie unter [Verwalten von Geräteregistrierungen](../articles/iot-dps/how-to-manage-enrollments.md).

> [!TIP]
> Die Schritte in diesem Artikel gelten für das Azure-Portal. Sie können Gruppenregistrierungen aber auch mithilfe der Azure CLI erstellen. Weitere Informationen finden Sie unter [az iot dps-Registrierungsgruppe](/cli/azure/iot/dps/enrollment-group). Verwenden Sie als Teil des CLI-Befehls das Flag **edge-enabled** (Edge-fähig), um anzugeben, dass die Registrierung für IoT Edge-Geräte gilt. Bei einer Gruppenregistrierung müssen alle Geräte IoT Edge-Geräte sein, oder keines von ihnen darf ein IoT Edge-Gerät sein.

1. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zu Ihrer Instanz des IoT Hub Device Provisioning Service.

1. Klicken Sie in **Einstellungen** auf **Registrierungen verwalten**.

1. Klicken Sie auf **Registrierungsgruppe hinzufügen**, und führen Sie dann die folgenden Schritte zum Konfigurieren der Registrierung aus:

   * **Gruppenname**: Geben Sie einen einprägsamen Namen für diese Gruppenregistrierung an.

   * **Nachweistyp**: Wählen Sie **Certificate** aus.

   * **IoT Edge-Gerät**: Wählen Sie **True** aus. Bei einer Gruppenregistrierung müssen alle Geräte IoT Edge-Geräte sein, oder keines von ihnen darf ein IoT Edge-Gerät sein.

   * **Zertifikattyp**: Wählen Sie **Zertifizierungsstellenzertifikat** aus.

   * **Primäres Zertifikat**: Wählen Sie Ihr Zertifikat aus der Dropdownliste aus.

   * **Wählen Sie die IoT-Hubs aus, denen dieses Gerät zugewiesen werden kann**: Wählen Sie den verknüpften IoT-Hub, mit dem Sie Ihr Gerät verbinden möchten. Sie können mehrere Hubs auswählen, und das Gerät wird dann je nach gewählter Zuteilungsrichtlinie einem dieser Hubs zugewiesen.

   * **Anfangszustand des Gerätezwillings**: Fügen Sie einen Tagwert hinzu, der dem Gerätezwilling bei Bedarf hinzugefügt werden soll. Mithilfe von Tags können Sie Gruppen von Geräten als Ziel für die automatische Bereitstellung festlegen. Beispiel:

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

Unter **Registrierungen verwalten** wird die **Registrierungs-ID** für die Registrierung angezeigt, die Sie gerade erstellt haben. Notieren Sie sich diese ID, da sie bei der Bereitstellung Ihres Geräts verwendet werden kann.

Nachdem jetzt eine Registrierung für diese Geräte vorhanden ist, kann die IoT Edge-Runtime die Geräte während der Installation automatisch bereitstellen.

---
