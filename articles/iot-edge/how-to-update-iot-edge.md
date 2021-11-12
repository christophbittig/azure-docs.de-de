---
title: Aktualisieren der IoT Edge-Version auf Geräten – Azure IoT Edge | Microsoft-Dokumentation
description: Aktualisieren von IoT Edge-Geräten auf die neuesten Versionen des Sicherheitsdaemons und der IoT Edge-Runtime
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 06/15/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 90a03b86e54c214fb5dd17f11ea01247b7b77e9b
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057211"
---
# <a name="update-iot-edge"></a>Aktualisieren von IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Wenn neue Versionen des IoT Edge-Diensts veröffentlicht werden, sollten Sie Ihre IoT Edge-Geräte aktualisieren, damit sie über die neuesten Funktionen und Verbesserungen der Sicherheit verfügen. Dieser Artikel enthält Informationen zum Aktualisieren Ihrer IoT Edge-Geräte, wenn eine neue Version verfügbar ist.

Zwei Komponenten eines IoT Edge-Geräts müssen bei der Umstellung auf eine neue Version aktualisiert werden. Die erste ist der Sicherheitsdaemon, der auf dem Gerät ausgeführt wird und beim Starten des Geräts die Runtimemodule startet. Der Sicherheitsdaemon kann zurzeit nur vom Gerät selbst aktualisiert werden. Die zweite Komponente ist die Runtime, die aus den IoT Edge-Hub- und den IoT Edge-Agent-Modulen besteht. Je nachdem, wie Ihre Bereitstellung strukturiert ist, kann die Runtime vom Gerät oder remote aktualisiert werden.

Informationen zur neuesten Version von Azure IoT Edge finden Sie unter [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases) (Azure IoT Edge-Versionen).

## <a name="update-the-security-daemon"></a>Aktualisieren des Sicherheitsdaemons

Der IoT Edge-Sicherheitsdaemon ist eine native Komponente, die mithilfe des Paket-Managers auf dem IoT Edge-Gerät aktualisiert werden muss.

Überprüfen Sie die Version des auf Ihrem Geräts ausgeführten Sicherheitsdaemons mit dem Befehl `iotedge version`. Wenn Sie IoT Edge für Linux unter Windows verwenden, müssen Sie eine SSH-Verbindung mit dem virtuellen Linux-Computer erstellen, um die Version zu überprüfen.

# <a name="linux"></a>[Linux](#tab/linux)

>[!IMPORTANT]
>Wenn Sie ein Gerät von Version 1.0 oder 1.1 auf Version 1.2 aktualisieren, gibt es Unterschiede bei den Installations- und Konfigurationsprozessen, die zusätzliche Schritte erfordern. Weitere Informationen finden Sie in den Schritten weiter unten in diesem Artikel: [Sonderfall: Update von 1.0 oder 1.1 auf 1.2](#special-case-update-from-10-or-11-to-12).

Verwenden Sie auf Linux x64-Geräten „apt-get“ oder einen geeigneten Paket-Manager, um den Sicherheitsdaemon auf die neueste Version zu aktualisieren.

Verwenden Sie die neueste Repositorykonfiguration von Microsoft:

* **Ubuntu Server 18.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspberry Pi-Betriebssystem Stretch:**

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

Kopieren Sie die generierte Liste.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Installieren Sie den öffentlichen GPG-Schlüssel von Microsoft.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

Aktualisieren Sie „apt“.

   ```bash
   sudo apt-get update
   ```

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Überprüfen Sie, welche IoT Edge-Versionen verfügbar sind.

   ```bash
   apt list -a iotedge
   ```

Wenn Sie ein Update auf die neueste Version des Sicherheitsdaemons durchführen möchten, verwenden Sie den folgenden Befehl, durch den auch **libiothsm-std** auf die neueste Version aktualisiert wird:

   ```bash
   sudo apt-get install iotedge
   ```

Wenn Sie ein Update auf eine bestimmte Version des Sicherheitsdaemons durchführen möchten, geben Sie die Version aus der Ausgabe von „apt list“ an. Bei jeder Aktualisierung von **iotedge** wird automatisch versucht, auch das Paket **libiothsm-std** auf die neueste Version zu aktualisieren, was ggf. einen Abhängigkeitskonflikt zur Folge hat. Wenn Sie kein Update auf die neueste Version durchführen, achten Sie darauf, beide Pakete auf die gleiche Version auszurichten. Mit dem folgenden Befehl wird beispielsweise eine bestimmte Version des Release 1.1 installiert:

   ```bash
   sudo apt-get install iotedge=1.1.1 libiothsm-std=1.1.1
   ```

Sollte die Version, die Sie installieren möchten, nicht über „apt-get“ verfügbar sein, können Sie mithilfe von cURL eine beliebige Version aus dem [Repository für IoT Edge-Releases](https://github.com/Azure/azure-iotedge/releases) als Ziel verwenden. Suchen Sie für die zu installierende Version nach den entsprechenden Dateien vom Typ **libiothsm-std** und **iotedge** für Ihr Gerät. Klicken Sie bei jeder Datei mit der rechten Maustaste auf den Dateilink, und kopieren Sie die Linkadresse. Verwenden Sie die Linkadresse, um die jeweiligen Versionen dieser Komponenten zu installieren:

```bash
curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo apt-get install ./libiothsm-std.deb
curl -L <iotedge link> -o iotedge.deb && sudo apt-get install ./iotedge.deb
```
<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Überprüfen Sie, welche IoT Edge-Versionen verfügbar sind.

   ```bash
   apt list -a aziot-edge
   ```

Wenn Sie ein Update auf die neueste Version von IoT Edge durchführen möchten, verwenden Sie den folgenden Befehl, mit dem auch der Identitätsdienst auf die neueste Version aktualisiert wird:

   ```bash
   sudo apt-get install aziot-edge
   ```
<!-- end 1.2 -->
:::moniker-end

# <a name="linux-on-windows"></a>[Linux unter Windows](#tab/linuxonwindows)

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>Zurzeit wird die Ausführung von IoT Edge, Version 1.2, auf virtuellen „Linux für Windows“-Computern nicht unterstützt.
>
>Die Schritte zum Aktualisieren von IoT Edge für Linux auf Windows finden Sie unter [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true&tabs=linuxonwindows).

:::moniker-end
<!-- end 1.2 -->

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

>[!IMPORTANT]
>Wenn Sie ein Gerät von der Public Preview-Version von IoT Edge für Linux unter Windows auf die allgemein verfügbare Version aktualisieren, müssen Sie Azure IoT Edge deinstallieren und neu installieren.
>
>Wenn Sie ermitteln möchten, ob Sie derzeit die Public Preview-Version verwenden, navigieren Sie auf Ihrem Windows Gerät zu **Einstellungen** > **Apps**. Suchen in der Liste der Apps und Features nach **Azure IoT Edge**. Ist bei Ihnen die Version 1.0.x aufgeführt, wird die Public Preview-Version verwendet. In diesem Fall müssen Sie die App deinstallieren und [IoT Edge für Linux unter Windows erneut installieren und bereitstellen](how-to-provision-single-device-linux-on-windows-symmetric.md). Ist bei Ihnen die Version 1.1.x aufgeführt, wird die allgemein verfügbare Version verwendet, und Sie können Updates über Microsoft Update erhalten.

>[!IMPORTANT]
>Wenn Sie ein Windows Server-SKU-Gerät vor Version 1.1.2110.03111 von IoT Edge für Linux unter Windows (Edge for Linux on Windows, EFLOW) auf die neueste verfügbare Version aktualisieren, müssen Sie eine manuelle Migration durchführen.
>
>Mit Update [1.1.2110.0311](https://github.com/Azure/iotedge-eflow/releases/tag/1.1.2110.03111) wurde eine Änderung an der VM-Technologie (HCS zu VMMS) eingeführt, die für EFLOW Windows Server-Bereitstellungen verwendet wird. Sie können die VM-Migration mit den folgenden Schritten durchführen:
> 1. Laden Sie mithilfe von Microsoft Update das Update 1.1.2110.03111 herunter, und installieren Sie es (wie bei jedem anderen EFLOW-Update sind keine manuellen Schritte erforderlich, solange EFLOW-Updates aktiviert sind).
> 2. Öffnen Sie nach Abschluss des EFLOW-Updates eine PowerShell-Sitzung mit erhöhten Rechten.
> 3. Führen Sie das Migrationsskript aus:
>  ```powershell
>   Migrate-EflowVmFromHcsToVmms
>   ```
>
> Hinweis: Neue EFLOW 1.1.2110.0311 MSI-Installationen auf Windows Server-SKUs führen zu EFLOW-Bereitstellungen mithilfe von VMMS-Technologie, sodass keine Migration erforderlich ist.

Mit IoT Edge für Linux unter Windows wird IoT Edge auf einer Linux-VM ausgeführt, die auf einem Windows-Gerät gehostet wird. Auf diesem virtuellen Computer ist IoT Edge vorinstalliert, und die IoT Edge-Komponenten können nicht manuell aktualisiert oder geändert werden. Stattdessen wird der virtuelle Computer mit Microsoft Update verwaltet, um die Komponenten automatisch auf dem neuesten Stand zu halten.

Die neueste Version von Azure IoT Edge für Linux unter Windows finden Sie unter [EFLOW-Releases](https://aka.ms/AzEFLOW-Releases).


Um IoT Edge für Linux für Windows-Updates zu erhalten, sollte der Windows-Host so konfiguriert werden, dass er Updates für andere Microsoft-Produkte empfängt. Sie können diese Option mit den folgenden Schritten aktivieren:

1. Öffnen Sie **Einstellungen** auf dem Windows-Host.

1. Wählen Sie **Updates und Sicherheit** aus.

1. Wählen Sie **Erweiterte Optionen** aus.

1. Schalten Sie die Schaltfläche *Updates für andere Microsoft-Produkte beim Windows-Update empfangen* um (**Ein**).

:::moniker-end
<!-- end 1.1 -->

# <a name="windows"></a>[Windows](#tab/windows)

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>Zurzeit wird die Ausführung von IoT Edge, Version 1.2, auf Windows-Geräten nicht unterstützt.
>
>Die Schritte zum Aktualisieren von IoT Edge für Linux auf Windows finden Sie unter [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true&tabs=windows).

:::moniker-end
<!-- end 1.2 -->

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Mit IoT Edge für Windows kann IoT Edge direkt auf dem Windows-Gerät ausgeführt werden.

Verwenden Sie den Befehl `Update-IoTEdge` zum Aktualisieren des Sicherheitsdaemons. Das Skript lädt per Pull automatisch die neueste Version des Sicherheitsdaemons herunter.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge
```

Wenn Sie den Update-IoTEdge-Befehl ausführen, wird der Sicherheitsdaemon zusammen mit den beiden Runtimecontainerimages von Ihrem Gerät entfernt und aktualisiert. Die Datei „config.yaml“ verbleibt auf dem Gerät, ebenso wie Daten der Moby-Containerengine. Durch die Beibehaltung der Konfigurationsinformationen müssen Sie während des Aktualisierungsvorgangs die Verbindungszeichenfolge oder die Device Provisioning Service-Informationen für Ihr Gerät nicht erneut angeben.

Wenn Sie ein Update auf eine bestimmte Version des Sicherheitsdaemons durchführen möchten, suchen Sie die gewünschte Zielversion aus dem Releasekanal 1.1 in [IoT Edge-Releases](https://github.com/Azure/azure-iotedge/releases). Laden Sie in dieser Version die Datei **Microsoft-Azure-IoTEdge.cab** herunter. Verwenden Sie anschließend den Parameter `-OfflineInstallationPath`, um auf den lokalen Speicherort der heruntergeladenen Datei zu verweisen. Beispiel:

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Update-IoTEdge -OfflineInstallationPath <absolute path to directory>
```

>[!NOTE]
>Der Parameter `-OfflineInstallationPath` sucht im angegebenen Verzeichnis nach der Datei **Microsoft-Azure-IoTEdge.cab**. Benennen Sie die Datei um, um das Architektursuffix zu entfernen, sofern vorhanden.

Wenn Sie ein Gerät offline aktualisieren möchten, finden Sie die gewünschte Version unter [Azure IoT Edge-Releases](https://github.com/Azure/azure-iotedge/releases). Laden Sie in dieser Version die Dateien *IoTEdgeSecurityDaemon.ps1* und *Microsoft-Azure-IoTEdge.cab* herunter. Es ist wichtig, das PowerShell-Skript für dasselbe Release zu verwenden, zu dem die CAB-Datei gehört, weil die Unterstützung für Features sich in jedem Release ändert.

Wenn die von Ihnen heruntergeladene CAB-Datei ein Suffix der Architektur enthält, benennen Sie die Datei in **Microsoft-Azure-IoTEdge.cab** um.

Für ein Update mit Offlinekomponenten führen Sie eine [DOT-Quellentnahme](/powershell/module/microsoft.powershell.core/about/about_scripts#script-scope-and-dot-sourcing) in der lokalen Kopie des PowerShell-Skripts durch. Verwenden Sie dann den `-OfflineInstallationPath`-Parameter als Teil des `Update-IoTEdge`-Befehls, und geben Sie den absoluten Pfad zum Dateiverzeichnis an. Beispiel:

```powershell
. <path>\IoTEdgeSecurityDaemon.ps1
Update-IoTEdge -OfflineInstallationPath <path>
```

Weitere Informationen zu Updateoptionen erhalten Sie mit dem Befehl `Get-Help Update-IoTEdge -full` oder in [PowerShell-Skripts für IoT Edge mit Windows-Containern](reference-windows-scripts.md).

:::moniker-end
<!-- end 1.1 -->

---

## <a name="update-the-runtime-containers"></a>Aktualisieren der Runtimecontainer

Die Art, auf die Sie die IoT Edge-Agent- und IoT Edge-Hubcontainer aktualisieren, hängt davon ab, ob Sie bei der Bereitstellung fortlaufende Versionstags (wie 1.1) oder spezifische Versionstags (wie 1.1.1) verwenden.

Überprüfen Sie die Version der aktuell auf Ihrem Gerät ausgeführten IoT Edge-Agent- und IoT Edge-Hubmodule mithilfe der Befehle `iotedge logs edgeAgent` und `iotedge logs edgeHub`. Wenn Sie IoT Edge für Linux unter Windows verwenden, müssen Sie eine SSH-Verbindung mit dem virtuellen Linux-Computer erstellen, um die Versionen des Runtimemoduls zu überprüfen.

  ![Suchen der Containerversion in Protokollen](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>Grundlagen von IoT Edge-Tags

Die IoT Edge-Agent- und IoT Edge-Hubimages sind im Tag mit der IoT Edge-Version gekennzeichnet, der sie zugeordnet sind. Es gibt zwei Möglichkeiten zum Verwenden von Tags mit den Runtime-Images:

* **Fortlaufende Tags**: Verwenden Sie nur die ersten zwei Stellen der Versionsnummer, um zum neuesten Image zu gelangen, das mit diesen Stellen übereinstimmt. Beispielsweise wird, sobald eine neue Version veröffentlicht wird, 1.1 immer so aktualisiert, dass auf die neueste 1.1.x-Version verwiesen wird. Wenn die Containerruntime auf Ihrem IoT Edge-Gerät das Image erneut per Pull herunterlädt, werden die Laufzeitmodule auf die neueste Version aktualisiert. Bereitstellungen aus dem Azure-Portal weisen standardmäßig fortlaufende Tags auf. *Dieses Vorgehen wird für Entwicklungszwecke vorgeschlagen.*

* **Spezifische Tags**: Verwenden Sie alle drei Stellen der Versionsnummer, um die Imageversion explizit festzulegen. Beispielsweise ändert sich 1.1.0 nach dem ersten Release nicht. Sie können im Bereitstellungsmanifest eine neue Versionsnummer deklarieren, wenn Sie zur Aktualisierung bereit sind. *Dieses Vorgehen wird für Produktionszwecke vorgeschlagen.*

### <a name="update-a-rolling-tag-image"></a>Aktualisieren eines Images mit fortlaufendem Tag

Wenn Sie in Ihrer Bereitstellung fortlaufende Tags verwenden (Beispiel: mcr.microsoft.com/azureiotedge-hub:**1.1**), müssen Sie die Containerruntime auf Ihrem Gerät dazu zwingen, per Pull die neueste Version des Images zu laden.

Löschen Sie die lokale Version des Images von Ihrem IoT Edge-Gerät. Auf Windows-Computern werden bei der Deinstallation des Sicherheitsdaemons auch die Runtime-Images entfernt, sodass Sie diesen Schritt nicht erneut ausführen müssen.

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.1
docker rmi mcr.microsoft.com/azureiotedge-agent:1.1
```

Möglicherweise müssen Sie das force-Flag `-f` verwenden, um die Images zu entfernen.

Der IoT Edge-Dienst lädt per Pull die neuesten Versionen der Runtimeimages herunter und startet sie automatisch wieder auf Ihrem Gerät.

### <a name="update-a-specific-tag-image"></a>Aktualisieren eines Images mit spezifischem Tag

Wenn Sie bei der Bereitstellung spezifische Tags verwenden (z. B. mcr.microsoft.com/azureiotedge-hub:**1.1.1**), müssen Sie lediglich das Tag in Ihrem Bereitstellungsmanifest aktualisieren und die Änderungen auf Ihr Gerät anwenden.

1. Wählen Sie Ihr IoT Edge-Gerät im IoT-Hub im Azure-Portal aus, und klicken Sie dann auf **Module festlegen**.

1. Klicken Sie im Abschnitt **IoT Edge-Module** auf **Laufzeiteinstellungen**.

   ![Laufzeiteinstellungen konfigurieren](./media/how-to-update-iot-edge/configure-runtime.png)

1. Ändern Sie in den **Laufzeiteinstellungen** den Wert **Image** für den **Edge-Hub** in die gewünschte Version. Wählen Sie noch nicht **Speichern** aus.

   ![Edge Hub-Imageversion aktualisieren](./media/how-to-update-iot-edge/runtime-settings-edgehub.png)

1. Reduzieren Sie die **Edge Hub**-Einstellungen, oder scrollen Sie nach unten, und ändern Sie den Wert **Image** für den **Edge-Agent** in dieselbe gewünschte Version.

   ![Edge Hub-Agent-Version aktualisieren](./media/how-to-update-iot-edge/runtime-settings-edgeagent.png)

1. Wählen Sie **Speichern** aus.

1. Klicken Sie auf **Überprüfen + erstellen**, überprüfen Sie die Bereitstellung, und klicken Sie dann auf **Erstellen**.

## <a name="special-case-update-from-10-or-11-to-12"></a>Sonderfall: Update von 1.0 oder 1.1 auf 1.2

>[!NOTE]
>Wenn Sie Windows-Container oder IoT Edge für Linux unter Windows verwenden, gilt dieser Sonderfallabschnitt nicht.

Ab Version 1.2 verwendet der IoT Edge-Dienst einen neuen Paketnamen, und es gibt einige Unterschiede bei den Installations- und Konfigurationsprozessen. Wenn Sie ein IoT Edge Gerät haben, auf dem Version 1.0 oder 1.1 ausgeführt wird, informieren Sie sich in diesen Anleitungen, wie ein Update auf 1.2 durchgeführt wird.

>[!NOTE]
>Zurzeit gibt es keine Unterstützung für IoT Edge, Version 1.2, das auf Windows-Geräten ausgeführt wird.

Einige der wichtigsten Unterschiede zwischen Version 1.2 und früheren Versionen sind folgende:

* Der Paketname wurde von **iotedge** in **aziot-edge** geändert.
* Das Paket **libiothsm-std** wird nicht mehr verwendet. Wenn Sie das als Teil der IoT Edge-Version bereitgestellte Standardpaket verwendet haben, können Ihre Konfigurationen auf die neue Version übertragen werden. Wenn Sie eine andere Implementierung von „libiothsm-std“ verwendet haben, müssen alle vom Benutzer bereitgestellten Zertifikate wie das Geräteidentitätszertifikat, die Gerätezertifizierungsstelle und das Vertrauensstellungspaket neu konfiguriert werden.
* Ein neuer Identitätsdienst, **aziot-identity-service**, wurde als Teil der Version 1.2 eingeführt. Dieser Dienst übernimmt die Identitätsbereitstellung und -verwaltung für IoT Edge und andere Gerätekomponenten, die mit IoT Hub kommunizieren müssen, wie z. B. [Device Update for IoT Hub](../iot-hub-device-update/understand-device-update.md).
* Die Standardkonfigurationsdatei hat einen neuen Namen und Speicherort. Früher lautete er `/etc/iotedge/config.yaml`. Jetzt werden Ihre Gerätekonfigurationsinformationen standardmäßig in `/etc/aziot/config.toml` gespeichert. Mithilfe des Befehls `iotedge config import` können Konfigurationsinformationen aus dem alten Speicherort und die Syntax in den neuen Speicherort migriert werden.
  * Der Importbefehl kann keine Zugriffsregeln für das vertrauenswürdige Plattformmodul (Trusted Platform Module, TPM) eines Geräts erkennen oder ändern. Wenn Ihr Gerät den TPM-Nachweis verwendet, müssen Sie die Datei „/etc/udev/rules.d/tpmaccess.rules“ manuell aktualisieren, um Zugriff auf den Dienst „aziottpm“ zu gewähren. Weitere Informationen finden Sie unter [Gewähren von IoT Edge-Zugriff auf das TPM](how-to-auto-provision-simulated-device-linux.md?view=iotedge-2020-11&preserve-view=true#give-iot-edge-access-to-the-tpm).
* Die Workload-API in Version 1.2 speichert verschlüsselte Geheimnisse in einem neuen Format. Wenn Sie ein Upgrade von einer älteren Version auf Version 1.2 durchführen, wird der vorhandene Hauptverschlüsselungsschlüssel importiert. Die Workload-API kann Geheimnisse lesen, die im vorherigen Format mit dem importierten Verschlüsselungsschlüssel gespeichert wurden. Die Workload-API kann jedoch keine verschlüsselten Geheimnisse im alten Format schreiben. Sobald ein Geheimnis von einem Modul erneut verschlüsselt wurde, wird es im neuen Format gespeichert. In Version 1.2 verschlüsselte Geheimnisse können vom selben Modul in Version 1.1 nicht gelesen werden. Wenn Sie verschlüsselte Daten in einem vom Host eingebundenen Ordner oder Volume dauerhaft speichern, erstellen Sie immer eine Sicherungskopie der Daten *bevor* Sie ein Upgrade durchführen, um wenn nötig wieder ein Downgrade durchführen zu können.

Vergewissern Sie sich vor dem Automatisieren von Updateprozessen, dass dies auf Testcomputern funktioniert.

Wenn Sie dazu bereit sind, führen Sie die folgenden Schritte zum Aktualisieren von IoT Edge auf Ihren Geräten aus:

1. Verwenden Sie die neueste Repositorykonfiguration von Microsoft:

   * **Ubuntu Server 18.04**:

     ```bash
     curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
     ```

   * **Raspberry Pi-Betriebssystem Stretch:**

     ```bash
     curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
     ```

2. Kopieren Sie die generierte Liste.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

3. Installieren Sie den öffentlichen GPG-Schlüssel von Microsoft.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

4. Aktualisieren Sie „apt“.

   ```bash
   sudo apt-get update
   ```

5. Deinstallieren Sie die vorherige Version von IoT Edge, behalten Sie aber Ihre Konfigurationsdateien an deren Speicherort bei.

   ```bash
   sudo apt-get remove iotedge
   ```

6. Installieren Sie die neueste Version von IoT Edge zusammen mit dem IoT-Identitätsdienst.

   ```bash
   sudo apt-get install aziot-edge
   ```

7. Importieren Sie die alte Datei „config.YAML“ in ihr neues Format, und wenden Sie die Konfigurationsinformationen an.

   ```bash
   sudo iotedge config import
   ```

Nachdem der auf Ihren Geräten ausgeführte IoT Edge-Dienst aktualisiert wurde, führen Sie jetzt die Schritte in diesem Artikel aus, um auch [die Laufzeitcontainer zu aktualisieren](#update-the-runtime-containers).

## <a name="special-case-update-to-a-release-candidate-version"></a>Sonderfall: Update auf eine Release Candidate-Version

>[!NOTE]
>Wenn Sie Windows-Container oder IoT Edge für Linux unter Windows verwenden, gilt dieser Sonderfallabschnitt nicht.

Azure IoT Edge veröffentlicht regelmäßig neue Versionen des IoT Edge-Dienstes. Vor jedem stabilen Release gibt es einen oder mehrere Release Candidate-Versionen (RC). RC-Versionen enthalten alle geplanten Features für das Release, werden aber weiterhin getestet und geprüft. Wenn Sie eine neue Funktion vorab testen möchten, können Sie eine RC-Version installieren und uns Ihr Feedback über GitHub mitteilen.

Release Candidate-Versionen werden genauso nummeriert wie die Releases, am Ende wird jedoch **-rc** und eine inkrementelle Zahl angehängt. Sie finden die Release Candidates in derselben Liste der [Azure IoT Edge-Releases](https://github.com/Azure/azure-iotedge/releases) wie die stabilen Versionen. Beispielsweise finden Sie **1.2.0-rc4**, eine der Release Candidates, der vor **1.2.0** herausgegeben wurde. Sie werden außerdem feststellen, dass die RC-Versionen die Bezeichnung **Vorabversion** aufweisen.

Der IoT Edge-Agent und die Hubmodule haben RC-Versionen, die mit derselben Konvention gekennzeichnet sind. Zum Beispiel **mcr.microsoft.com/azureiotedge-hub:1.2.0-rc4**.

Release Candidate-Versionen sind als Vorschauversionen nicht in der neusten Version enthalten, die reguläre Installationsprogramme aufrufen. Stattdessen müssen Sie die Objekte für die RC-Version manuell aufrufen, die Sie testen möchten. Zum größten Teil ist die Installation einer RC-Version oder die Aktualisierung darauf identisch mit der Ausrichtung auf eine andere spezifische Version von IoT Edge.

Informieren Sie sich anhand der Abschnitte in diesem Artikel, wie Sie ein IoT Edge-Gerät auf eine bestimmte Version des Sicherheitsdaemons oder der Laufzeitmodule aktualisieren.

Wenn Sie IoT Edge installieren, statt eine vorhandene Installation zu aktualisieren, führen Sie die Schritte in [Offlineinstallation oder Installation einer bestimmten Version](how-to-provision-single-device-linux-symmetric.md#offline-or-specific-version-installation-optional) aus.

## <a name="next-steps"></a>Nächste Schritte

Zeigen Sie die neuesten [Azure IoT Edge releases](https://github.com/Azure/azure-iotedge/releases) (Azure IoT Edge-Versionen) an.

Bleiben Sie mit den neuesten Updates und Ankündigungen im [Internet of Things-Blog](https://azure.microsoft.com/blog/topics/internet-of-things/) auf dem Laufenden.
