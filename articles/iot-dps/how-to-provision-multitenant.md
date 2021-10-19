---
title: Bereitstellen von Geräten für Mehrinstanzenfähigkeit in Azure IoT Hub Device Provisioning Service
description: Bereitstellen von Geräten für Mehrinstanzenfähigkeit mit Ihrer Device Provisioning Service-Instanz (DPS)
author: anastasia-ms
ms.author: v-stharr
ms.topic: how-to
ms.date: 10/02/2021
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 595bd473bb70f4c23554998b7240e4ef0ed1183d
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/07/2021
ms.locfileid: "129670289"
---
# <a name="how-to-provision-for-multitenancy"></a>Bereitstellen für Mehrinstanzenfähigkeit 

Diese Anleitung zeigt, wie mehrere simulierte Geräte mit symmetrischem Schlüssel mithilfe einer [Zuweisungsrichtlinie](concepts-service.md#allocation-policy) sicher einer Gruppe von IoT-Hubs zugewiesen werden können. Zuweisungsrichtlinien, die durch den Bereitstellungsdienst definiert werden, unterstützen eine Vielzahl von Zuweisungsszenarien. Zwei häufige Szenarien sind:

* **Geolocation / GeoLatency**: Wenn sich ein Gerät zwischen Standorten bewegt, wird die Netzwerklatenz verbessert, indem das Gerät dem IoT-Hub zugewiesen wird, der dem jeweiligen Standort am nächsten ist. In diesem Szenario wird eine Gruppe mit regionsübergreifenden IoT Hubs für Registrierungen ausgewählt. Für diese Registrierungen wird die Zuordnungsrichtlinie **Niedrigste Latenz** ausgewählt. Diese Richtlinie bewirkt, dass der Device Provisioning Service die Gerätelatenz auswertet und aus der Gruppe mit den IoT Hubs den nächstgelegenen IoT Hub ermittelt.

* **Mehrinstanzenfähigkeit**: Geräte, die in einer IoT-Lösung verwendet werden, müssen unter Umständen einem bestimmten IoT Hub oder einer Gruppe mit IoT Hubs zugewiesen werden. Für die Lösung kann es erforderlich sein, dass alle Geräte für einen bestimmten Mandanten mit einer bestimmten Gruppe von IoT Hubs kommunizieren. In einigen Fällen kann auch ein Mandant der Besitzer von IoT Hubs sein und erzwingen, dass Geräte seinen IoT Hubs zugewiesen werden.

Es ist üblich, diese beiden Szenarien zu kombinieren. Bei einer mandantenfähigen IoT-Lösung werden beispielsweise die Geräte der Mieter in der Regel einer Gruppe von IoT-Hubs zugewiesen, die über verschiedene Regionen verstreut sind. Diese Mietergeräte können dem IoT-Hub in der Gruppe zugewiesen werden, der je nach geografischem Standort die geringste Latenz aufweist.

In diesem Artikel wird ein Beispiel für ein simuliertes Gerät aus dem [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) verwendet, um zu veranschaulichen, wie Geräte in einem Szenario mit Mehrinstanzenfähigkeit regionsübergreifend bereitgestellt werden. In diesem Artikel führen Sie die folgenden Schritte aus:

> [!div class="checklist"]
> * Verwenden Sie die Azure CLI, um zwei regionale IoT-Hubs zu erstellen (**West US 2** und **Ost US**)
> * Erstellen einer mehrinstanzenfähigen Registrierung
> * Verwenden Sie die Azure CLI, um zwei regionale Linux-VMs zu erstellen, die als Geräte in denselben Regionen fungieren (**West US 2** und **Ost US**)
> * Einrichten der Entwicklungsumgebung für das Azure IoT C SDK auf beiden Linux-VMs
> * Simulieren der Geräte, um darzustellen, dass sie für denselben Mandanten in der am nächsten gelegenen Region bereitgestellt wurden

>[!IMPORTANT]
> Einige Regionen können von Zeit zu Zeit Beschränkungen für die Erstellung von virtuellen Maschinen auferlegen. Zum Zeitpunkt der Erstellung dieses Leitfadens erlaubten die Regionen *westus2* und *eastus* die Erstellung von VMs. Wenn Sie in einer dieser Regionen keine VMs erstellen können, können Sie eine andere Region ausprobieren. Weitere Informationen über die Auswahl geografischer Azure-Regionen bei der Erstellung von VMs finden Sie unter [Regionen für virtuelle Maschinen in Azure](../virtual-machines/regions.md)

## <a name="prerequisites"></a>Voraussetzungen

* Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) erstellen, bevor Sie beginnen.

* Führen Sie die Schritte im Artikel [Schnellstart: Einrichten des IoT Hub Device Provisioning-Diensts über das Azure-Portal](./quick-setup-auto-provision.md) aus.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-two-regional-iot-hubs"></a>Erstellen von zwei regionalen IoT Hubs

In diesem Abschnitt werden Sie eine Azure-Ressourcengruppe und zwei neue regionale IoT-Hub-Ressourcen für einen Mandanten erstellen. Ein IoT-Hub wird für die Region **West US 2** und der andere für die Region **Ost US** sein.

>[!IMPORTANT]
>Es wird empfohlen, für alle in diesem Artikel erstellten Ressourcen die gleiche Ressourcengruppe zu verwenden. Dies erleichtert das Bereinigen nach Abschluss der Schritte in diesem Artikel.

1. Erstellen Sie in der Azure Cloud Shell eine Ressourcengruppe mit dem folgenden Befehl [az group create](/cli/azure/group#az_group_create):

    ```azurecli-interactive
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Erstellen Sie einen IoT-Hub am Standort *eastus* und fügen Sie ihn der Ressourcengruppe hinzu, die Sie mit dem folgenden Befehl [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) (ersetzen Sie `{unique-hub-name}` durch Ihren eigenen eindeutigen Namen) erstellt haben:

    ```azurecli-interactive
    az iot hub create --name {unique-hub-name} --resource-group contoso-us-resource-group --location eastus --sku S1
    ```

    Die Ausführung dieses Befehls kann einige Minuten in Anspruch nehmen.

3. Erstellen Sie nun einen IoT-Hub am Standort *westus2* und fügen Sie ihn mit dem folgenden Befehl [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) (ersetzen Sie `{unique-hub-name}` durch Ihren eigenen eindeutigen Namen) zu der von Ihnen erstellten Ressourcengruppe hinzu:

    ```azurecli-interactive
    az iot hub create --name {unique-hub-name} --resource-group contoso-us-resource-group --location westus2 --sku S1
    ```

    Die Ausführung dieses Befehls kann einige Minuten in Anspruch nehmen.

## <a name="create-the-multitenant-enrollment"></a>Erstellen der mehrinstanzenfähigen Registrierung

In diesem Abschnitt erstellen Sie eine neue Registrierungsgruppe für die Mietergeräte.  

Der Einfachheit halber wird in diesem Artikel ein [Nachweis des symmetrischen Schlüssels](concepts-symmetric-key-attestation.md) für die Registrierung verwendet. Für eine Lösung mit höherer Sicherheit empfiehlt sich die Verwendung eines [X.509-Zertifikatnachweises](concepts-x509-attestation.md) mit einer Kette von Vertrauensstellungen.

1. Wählen Sie im Azure-Portal Ihre Device Provisioning Service-Instanz aus.

2. Wählen Sie im Menü **Einstellungen** die Option **Registrierungen verwalten** aus.

3. Wählen Sie **+ Anmeldergruppe** hinzufügen.

4. Geben Sie auf der Seite **Add Enrollment Group** die folgenden Informationen ein:

    **Gruppenname**: Geben Sie *contoso-us-devices* ein.

    **Nachweistyp**: Wählen Sie *Symmetrischer Schlüssel*.

    **Schlüssel automatisch generieren**: Dieses Kontrollkästchen sollte bereits aktiviert sein.

    **Wählen Sie, wie Geräte den Hubs zugewiesen werden sollen**: Wählen Sie die Option *Niedrigste Latenz*.

5. Wählen Sie **Neuen IoT-Hub verknüpfen**

    :::image type="content" source="./media/how-to-provision-multitenant/create-multitenant-enrollment.png" alt-text="Mehrmandantenregistrierungsgruppe für symmetrische Schlüsselbestätigung hinzufügen.":::

6. Geben Sie auf der Seite **Link zu IoT-Hub** hinzufügen die folgenden Informationen ein:

    **Abonnement**: Wenn Sie über mehrere Abonnements verfügen, sollten Sie das Abonnement auswählen, in dem Sie die IoT Hubs für die Regionen erstellt haben.

    **IoT-Hub**: Wählen Sie den IoT-Hub, den Sie für den Standort *eastus* erstellt haben.

    **Zugangsrichtlinie**: Wählen Sie *iothubowner*.

    :::image type="content" source="./media/how-to-provision-multitenant/link-regional-hubs.png" alt-text="Verknüpfen Sie die regionalen IoT-Hubs mit dem Bereitstellungsdienst.":::

7. Wählen Sie **Speichern** aus.

8. Wiederholen Sie die Schritte 5 bis 7 für den zweiten IoT-Hub, den Sie für den Standort *Westgus* erstellt haben.

9. Wählen Sie die beiden IoT-Hubs, die Sie in der Dropdown-Liste **Wählen Sie die IoT-Hubs, denen diese Gruppe zugewiesen werden soll**.

    :::image type="content" source="./media/how-to-provision-multitenant/enrollment-regional-hub-group.png" alt-text="Wählen Sie die verknüpften IoT-Hubs.":::

10. Wählen Sie **Speichern** aus.

11. Wählen Sie *contoso-us-devices* in der Liste der Anmeldungsgruppen.

12. Kopieren Sie den *Primärschlüssel*. Dieser Schlüssel wird später verwendet, um eindeutige Geräteschlüssel für beide simulierten Geräte zu erzeugen.

    :::image type="content" source="./media/how-to-provision-multitenant/copy-primary-key.png" alt-text="Kopieren Sie den Primärschlüssel":::.

## <a name="create-regional-linux-vms"></a>Erstellen von regionalen Linux-VMs

In diesem Abschnitt werden Sie zwei regionale virtuelle Linux-Maschinen (VMs) erstellen. Auf diesen VMs wird ein Beispiel für eine Gerätesimulation aus jeder Region ausgeführt, um die Gerätebereitstellung für Mandantengeräte aus beiden Regionen zu veranschaulichen.

Zur Vereinfachung der Bereinigung werden diese VMs derselben Ressourcengruppe hinzugefügt, in der die erstellten IoT Hubs enthalten sind: *contoso-us-resource-group*. Die VMs werden jedoch in separaten Regionen ausgeführt (**West US 2** und **Ost US**).

1. Führen Sie in der Azure Cloud Shell den folgenden Befehl aus, um eine VM der Region **East US** zu erstellen, nachdem Sie die folgenden Parameteränderungen im Befehl vorgenommen haben:

    **--name**: Geben Sie einen eindeutigen Namen für den virtuellen Computer in der Region **USA, Osten** ein.

    **--admin-username**: Verwenden Sie Ihren eigenen Administratorbenutzernamen.

    **--admin-password**: Verwenden Sie Ihr eigenes Administratorkennwort.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    --public-ip-sku Standard
    ```

    Die Ausführung dieses Befehls dauert einige Minuten. 

2. Kopieren Sie nach Abschluss des Befehls den Wert **publicIpAddress** für Ihre VM der Region East US.

3. Führen Sie in der Azure Cloud Shell den Befehl zum Erstellen einer VM der Region **West US 2** aus, nachdem Sie die folgenden Parameteränderungen im Befehl vorgenommen haben:

    **--name**: Geben Sie einen eindeutigen Namen für Ihre regionale Geräte-VM **West US 2** ein. 

    **--admin-username**: Verwenden Sie Ihren eigenen Administratorbenutzernamen.

    **--admin-password**: Verwenden Sie Ihr eigenes Administratorkennwort.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest2 \
    --location westus2 \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    --public-ip-sku Standard
    ```

    Die Ausführung dieses Befehls dauert einige Minuten.

4. Kopieren Sie nach Abschluss des Befehls den Wert **publicIpAddress** für Ihre VM der Region West US 2.

5. Öffnen Sie zwei Befehlszeilenshells.

6. Stellen Sie in jeder Shell eine Verbindung mit einem der regionalen virtuellen Computer her, indem Sie SSH verwenden.

    Übergeben Sie Ihren Admin-Benutzernamen und die öffentliche IP-Adresse, die Sie kopiert haben, als Parameter an SSH. Geben Sie das Administratorkennwort ein, wenn Sie dazu aufgefordert werden.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```

## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Vorbereiten der Azure IoT C SDK-Entwicklungsumgebung

In diesem Abschnitt klonen Sie das Azure IoT C SDK auf jeder VM. Das SDK enthält ein Beispiel, das die Gerätebereitstellung eines Mandanten aus jeder Region simuliert.

Für jede VM:

1. Installieren Sie **CMake**, **g++** , **gcc** und [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) mit den folgenden Befehlen:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

2. Suchen und kopieren Sie den Tag-Namen für die [neueste Version](https://github.com/Azure/azure-iot-sdk-c/releases/latest) des SDK.

3. Klonen Sie das [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) auf beiden VMs.  Verwenden Sie das im vorherigen Schritt gefundene Tag als Wert für den Parameter `-b`:

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Sie sollten damit rechnen, dass die Ausführung dieses Vorgangs mehrere Minuten in Anspruch nimmt.

4. Erstellen Sie einen neuen Ordner **cmake** innerhalb des Repositorys und wechseln Sie in diesen Ordner.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

5. Führen Sie den folgenden Befehl aus, der eine für Ihre Entwicklungs-Client-Plattform spezifische Version des SDK erstellt:

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

6. Nach erfolgreicher Erstellung ähneln die letzten Ausgabezeilen der folgenden Ausgabe:

    ```bash
    -- IoT Client SDK Version = 1.7.0
    -- Provisioning SDK Version = 1.7.0
    -- Looking for include file stdint.h
    -- Looking for include file stdint.h - found
    -- Looking for include file stdbool.h
    -- Looking for include file stdbool.h - found
    -- target architecture: x86_64
    -- Performing Test CXX_FLAG_CXX11
    -- Performing Test CXX_FLAG_CXX11 - Success
    -- Found OpenSSL: /usr/lib/x86_64-linux-gnu/libcrypto.so (found version "1.1.1")
    -- Found CURL: /usr/lib/x86_64-linux-gnu/libcurl.so (found version "7.58.0")
    -- Found CURL: /usr/lib/x86_64-linux-gnu/libcurl.so
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/azure-iot-sdk-c
    ```

## <a name="derive-unique-device-keys"></a>Ableiten eindeutiger Geräteschlüssel

Bei Verwendung des Nachweises des symmetrischen Schlüssels mit Gruppenregistrierungen verwenden Sie die Registrierungsgruppenschlüssel nicht direkt. Stattdessen leiten Sie für jedes Gerät einen eindeutigen Schlüssel aus dem Gruppenschlüssel für die Registrierung ab. Weitere Informationen finden Sie unter [Gruppenregistrierungen mit symmetrischen Schlüsseln](concepts-symmetric-key-attestation.md#group-enrollments).

In diesem Teil des Tutorials erzeugen Sie einen Geräteschlüssel aus dem Hauptschlüssel der Gruppe, um eine [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) der eindeutigen Registrierungs-ID für das Gerät zu berechnen. Das Ergebnis wird dann in das Base64-Format konvertiert.

>[!IMPORTANT]
>Fügen Sie Ihren Gruppenhauptschlüssel nicht in Ihren Gerätecode ein.

Für **beide** *eastus* und *westus 2* Geräte:

1. Erzeugen Sie Ihren eindeutigen Schlüssel mit **openssl**. Verwenden Sie das folgende Bash-Shell-Skript (ersetzen Sie `{primary-key}` durch den **Primärschlüssel** der Registrierungsgruppe, den Sie zuvor kopiert haben, und ersetzen Sie `{contoso-simdevice-east}` durch Ihre eigene eindeutige Registrierungs-ID für jedes Gerät. Verwenden Sie Kleinbuchstaben und Bindestriche ('-'), um beide IDs zu definieren):

    ```bash
    KEY={primary-key}
    REG_ID={contoso-simdevice}
    
    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
    ```

2. Das Skript gibt in etwa den folgenden Schlüssel aus:

    ```bash
    p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
    ```

3. Jetzt verfügt jedes Mietergerät über einen eigenen abgeleiteten Geräteschlüssel und eine eindeutige Registrierungs-ID, um während des Bereitstellungsprozesses eine symmetrische Schlüsselbestätigung mit der Registrierungsgruppe durchzuführen.

## <a name="simulate-the-devices-from-each-region"></a>Simulieren der Geräte aus den einzelnen Regionen

In diesem Abschnitt aktualisieren Sie ein Bereitstellungsbeispiel im Azure IoT C SDK für die beiden regionalen VMs. 

Der Beispielcode simuliert eine Gerätestartsequenz, über die die Bereitstellungsanforderung an die Instanz des Device Provisioning-Diensts gesendet wird. Die Boot-Sequenz führt dazu, dass das Gerät erkannt und dem IoT-Hub zugewiesen wird, der auf der Grundlage der Latenzzeit am nächsten liegt.

1. Navigieren Sie im Azure-Portal zur Registerkarte **Übersicht** für Ihre Device Provisioning Service-Instanz, und notieren Sie sich den Wert unter **_ID-Bereich_**.

    :::image type="content" source="./media/how-to-provision-multitenant/copy-id-scope.png" alt-text="Extrahieren Sie Gerätebereitstellungsservice-Endpunktinformationen aus dem Portal-Blade.":::

2. Öffnen Sie auf beiden VMS **~/azure-iot-sdk-c/provisioning\_client/samples/prov\_dev\_client\_sample/prov\_dev\_client\_sample.c** zur Bearbeitung.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

3. Suchen Sie auf beiden VMs die Konstante `id_scope`, und ersetzen Sie den Wert durch den zuvor kopierten Wert **ID Scope**. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

4. Suchen Sie auf beiden VMs die Definition für die Funktion `main()` in derselben Datei. Stellen Sie sicher, dass die Variable `hsm_type` wie unten gezeigt für die Nachweismethode für Registrierungsgruppen auf `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` festgelegt ist. 

    Speichern Sie Ihre Änderungen an den Dateien auf beiden VMs.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

5. Suchen Sie auf beiden VMs den Aufruf zum `prov_dev_set_symmetric_key_info()` in **prov\_dev\_client\_sample.c**, der auskommentiert ist.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Dekommentieren Sie die Funktionsaufrufe und ersetzen Sie die Platzhalterwerte (einschließlich der spitzen Klammern) durch die eindeutigen Registrierungs-IDs und abgeleiteten Geräteschlüssel für jedes Gerät, die Sie im vorherigen Abschnitt abgeleitet haben. Die nachfolgenden Schlüssel dienen nur als Beispiel. Verwenden Sie die zuvor erstellten Schlüssel.

    USA, Osten:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    USA, Westen:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

6. Speichern Sie die Datei auf beiden VMs.

7. Navigieren Sie auf beiden VMs zum unten angegebenen Beispielordner, und erstellen Sie das Beispiel.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

8. Führen Sie nach der erfolgreichen Erstellung **prov\_dev\_client\_sample.exe** auf beiden VMs aus, um ein Mandantengerät aus jeder Region zu simulieren. Beachten Sie, dass jedes Gerät dem Mandanten-IoT Hub zugeordnet ist, der den Regionen des simulierten Geräts am nächsten liegt.

    Führen Sie die Simulation aus:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Beispielausgabe der VM „USA, Osten“:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    Beispielausgabe der VM „USA, Westen“:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie die in diesem Artikel erstellten Ressourcen weiterverwenden möchten, können Sie sie beibehalten. Andernfalls gehen Sie wie folgt vor, um alle von diesem Artikel erstellten Ressourcen zu löschen, um unnötige Kosten zu vermeiden.

Die folgenden Schritte gehen davon aus, dass Sie alle Ressourcen in diesem Artikel wie beschrieben in derselben Ressourcengruppe mit dem Namen **contoso-us-resource-group** erstellt haben.

> [!IMPORTANT]
> Das Löschen einer Ressourcengruppe kann nicht rückgängig gemacht werden. Die Ressourcengruppe und alle darin enthaltenen Ressourcen werden unwiderruflich gelöscht. Achten Sie daher darauf, dass Sie nicht versehentlich die falsche Ressourcengruppe oder die falschen Ressourcen löschen. Wenn Sie die IoT Hub-Ressource in einer bereits vorhandenen Ressourcengruppe erstellt haben, die Ressourcen enthält, die Sie behalten möchten, löschen Sie nicht die Ressourcengruppe, sondern nur die IoT Hub-Ressource.
>

Löschen Sie die Ressourcengruppen wie folgt nach Namen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Klicken Sie auf **Ressourcengruppen**.

3. Geben Sie im Textfeld **Nach Name filtern...** den Namen der Ressourcengruppe ein, die Ihre Ressourcen enthält: **contoso-us-resource-group**.

4. Klicken Sie in der Ergebnisliste rechts neben Ihrer Ressourcengruppe auf **...** und dann auf **Ressourcengruppe löschen**.

5. Sie werden aufgefordert, das Löschen der Ressourcengruppe zu bestätigen. Geben Sie den Namen Ihrer Ressourcengruppe zur Bestätigung erneut ein, und wählen Sie **Löschen** aus. Daraufhin werden die Ressourcengruppe und alle darin enthaltenen Ressourcen gelöscht.

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen über Reprovisionierung finden Sie unter:

> [!div class="nextstepaction"]
> [IoT Hub Device-Konzepte für die erneute Bereitstellung](concepts-device-reprovision.md)

* Weitere Informationen zum Aufheben der Bereitstellung finden Sie unter
> [!div class="nextstepaction"]
> [Aufheben der Bereitstellung von Geräten, die zuvor automatisch bereitgestellt wurden](how-to-unprovision-devices.md)