---
title: PowerShell-Funktionen für Azure IoT Edge für Linux unter Windows | Microsoft-Dokumentation
description: Referenzinformationen zu PowerShell-Funktionen für Azure IoT Edge für Linux unter Windows zum Bereitstellen und Abrufen des Status von IoT Edge für Linux unter Windows-VMs.
author: v-tcassi
ms.author: fcabrera
ms.date: 10/15/2021
ms.topic: reference
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 9038f8b99728b2808cb1d4cf6b23a7673fa5d92e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085903"
---
# <a name="powershell-functions-for-iot-edge-for-linux-on-windows"></a>PowerShell-Funktionen für IoT Edge für Linux unter Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Informieren Sie sich über die PowerShell-Funktionen, mit denen Sie Ihren virtuellen IoT Edge für Linux unter Windows (EFLOW)-Computer bereitstellen und seinen Status abrufen können.

## <a name="prerequisites"></a>Voraussetzungen

Die in diesem Artikel beschriebenen Befehle stammen aus der Datei `AzureEFLOW.psm1`, die Sie auf Ihrem System in Ihrem Verzeichnis `WindowsPowerShell` unter `C:\Program Files\WindowsPowerShell\Modules\AzureEFLOW` finden können.

Wenn Ihr PowerShell-Verzeichnis nicht den Ordner **AzureEflow** enthält, führen Sie die folgenden Schritte zum Herunterladen und Installieren von Azure IoT Edge für Linux unter Windows aus: 

1. Führen Sie in einer PowerShell-Sitzung mit erhöhten Rechten die folgenden Befehle nacheinander aus, um IoT Edge für Linux unter Windows herunterzuladen.

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Installieren Sie IoT Edge für Linux unter Windows auf Ihrem Gerät.

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

   Sie können benutzerdefinierte Installations- und VHDX-Verzeichnisse angeben, indem Sie dem Installationsbefehl die Parameter `INSTALLDIR="<FULLY_QUALIFIED_PATH>"` und `VHDXDIR="<FULLY_QUALIFIED_PATH>"` hinzufügen.

1. Legen Sie die Ausführungsrichtlinie auf dem Zielgerät auf `AllSigned` fest, wenn dies noch nicht so eingestellt ist.

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

## <a name="connect-eflowvm"></a>Connect-EflowVm

Der Befehl **Connect-EflowVm** stellt mithilfe von SSH eine Verbindung mit dem virtuellen Computer her. Das einzige Konto, das zum Herstellen eines SSH mit dem virtuellen Computer zulässig ist, ist der Benutzer, der es erstellt.

Dieser Befehl funktioniert nur bei einer PowerShell-Sitzung, die auf dem Hostgerät ausgeführt wird. Er funktioniert nicht, wenn Windows Admin Center oder PowerShell ISE verwendet wird.

Weitere Informationen erhalten Sie mit dem Befehl `Get-Help Connect-EflowVm -full`.

## <a name="copy-eflowvmfile"></a>Copy-EflowVmFile

Mit dem Befehl **Copy-EflowVmFile** wird die Datei mithilfe von SCP auf den bzw. vom virtuellen Computer kopiert. Verwenden Sie die optionalen Parameter zur Angabe der Quell- und Zieldateipfade sowie der Richtung für die Kopie.

Der Benutzer **iotedge-user** muss Leseberechtigung für alle Ursprungsverzeichnisse oder Schreibberechtigung für alle Zielverzeichnisse auf dem virtuellen Computer haben.

| Parameter | Zulässige Werte | Kommentare |
| --------- | --------------- | -------- |
| fromFile | Zeichenfolge, die den Pfad zur Datei darstellt | Definiert die Datei, aus der gelesen werden soll. |
| toFile | Zeichenfolge, die den Pfad zur Datei darstellt |  Definiert die Datei, in die geschrieben werden soll. |
| pushFile | Keine | Dieses Flag gibt die Kopierrichtung an. Falls vorhanden, pusht der Befehl die Datei auf den virtuellen Computer. Falls vorhanden, pullt der Befehl die Datei vom virtuellen Computer. |

Weitere Informationen erhalten Sie mit dem Befehl `Get-Help Copy-EflowVMFile -full`.

## <a name="deploy-eflow"></a>Deploy-Eflow

Der Befehl **Deploy-Eflow** ist die Hauptbereitstellungsmethode. Der Bereitstellungsbefehl erstellt den virtuellen Computer und stellt Dateien sowie das IoT Edge-Agentmodul bereit. Obwohl keiner der Parameter erforderlich ist, können sie verwendet werden, um Einstellungen für den virtuellen Computer während der Erstellung zu ändern.

| Parameter | Zulässige Werte | Kommentare |
| --------- | --------------- | -------- |
| acceptEula | **Ja** oder **Nein** | Eine Verknüpfung zum Akzeptieren/Verweigern des EULA (End User Licence Agreement, Endbenutzer-Lizenzvertrag) und Umgehen der EULA-Eingabeaufforderung. |
| acceptOptionalTelemetry | **Ja** oder **Nein** |  Eine Verknüpfung zum Akzeptieren/Verweigern von optionaler Telemetrie und zum Umgehen der Telemetrieaufforderung. |
| cpuCount | Ganzzahliger Wert zwischen 1 und den CPU-Kernen des Geräts |  Anzahl der CPU-Kerne für den virtuellen Computer.<br><br>**Standardwert:** 1 virtueller Kern. |
| memoryInMB | Ganzzahliger Wert zwischen 1.024 und der maximalen Menge an freiem Arbeitsspeicher des Geräts |Der für den virtuellen Computer zugeordnete Arbeitsspeicher.<br><br>**Standardwert**: 1.024 MB. |
| vmDiskSize | Zwischen 8 GB und 256 GB | Maximale Datenträgergröße der dynamisch erweiterbaren virtuellen Festplatte.<br><br>**Standardwert**: 10 GB |
| vswitchName | Name des virtuellen Netzwerks |  Name des virtuellen Switches, der dem virtuellen EFLOW-Computer zugewiesen wurde. |
| vswitchType | **Intern** oder **Extern** | Typ des virtuellen Switches, der dem virtuellen EFLOW-Computer zugewiesen wurde. |
| ip4Address | IPv4-Adresse im Bereich des DCHP-Serverbereichs | Statische Ipv4-Adresse des virtuellen EFLOW-Computers. |
| ip4PrefixLength | IPv4-Präfixlänge des Subnetzes | Ipv4-Subnetzpräfixlänge; nur gültig, wenn eine statische Ipv4-Adresse angegeben wird. |
| ip4GatewayAddress | IPv4-Adresse des Subnetzgateways | Ipv4-Gatewayadresse; nur gültig, wenn eine statische Ipv4-Adresse angegeben wird. |
| gpuName | GPU-Gerätename |  Name des GPU-Geräts, das für Passthrough verwendet werden soll. |
| gpuPassthroughType | **DirectDeviceAssignment**, **ParaVirtualization** oder keine (nur CPU) |  GPU-Passthrough-Typ |
| gpuCount | Ganzzahliger Wert zwischen 1 und der Anzahl der GPU-Kerne des Geräts | Anzahl der GPU-Geräte für den virtuellen Computer. <br><br>**Hinweis**: Wenn Sie „ParaVirtualization“ verwenden, müssen Sie „gpuCount = 1“ festlegen. |

Weitere Informationen erhalten Sie mit dem Befehl `Get-Help Deploy-Eflow -full`.  

## <a name="get-eflowhostconfiguration"></a>Get-EflowHostConfiguration

Der Befehl **Get-EflowHostConfiguration** gibt die Hostkonfiguration zurück. Bei diesem Befehl werden keine Parameter verwendet. Er gibt ein Objekt mit vier Eigenschaften zurück:

* FreePhysicalMemoryInMB
* NumberOfLogicalProcessors
* DiskInfo
* GpuInfo

Weitere Informationen erhalten Sie mit dem Befehl `Get-Help Get-EflowHostConfiguration -full`.

## <a name="get-eflowlogs"></a>Get-EflowLogs

Der Befehl **Get-EflowLogs** sammelt und bündelt Protokolle aus der Bereitstellung und Installation von IoT Edge für Linux unter Windows. Die gebündelten Protokolle werden in Form eines `.zip`-Ordners ausgegeben.

Weitere Informationen erhalten Sie mit dem Befehl `Get-Help Get-EflowLogs -full`.

## <a name="get-eflowvm"></a>Get-EflowVm

Der Befehl **Get-EflowVm** gibt die aktuelle Konfiguration des virtuellen Computers zurück. Bei diesem Befehl werden keine Parameter verwendet. Er gibt ein Objekt mit vier Eigenschaften zurück:

* VmConfiguration
* EdgeRuntimeVersion
* EdgeRuntimeStatus
* SystemStatistics

Wenn Sie eine bestimmte Eigenschaft in einer lesbaren Liste anzeigen möchten, führen Sie den Befehl `Get-EflowVM` mit der erweiterten Eigenschaft aus. Beispiel:

```powershell
Get-EflowVM | Select -ExpandProperty VmConfiguration | Format-List
```

Weitere Informationen erhalten Sie mit dem Befehl `Get-Help Get-EflowVm -full`.

## <a name="get-eflowvmaddr"></a>Get-EflowVmAddr

Mit dem Befehl **Get-EflowVmAddr** wird die aktuelle IP- und MAC-Adresse des virtuellen Computers abgefragt. Mit diesem Befehl wird die Tatsache berücksichtigt, dass sich die IP- und MAC-Adresse im Laufe der Zeit ändern kann.

Wenn Sie zusätzliche Informationen benötigen, verwenden Sie den Befehl `Get-Help Get-EflowVmAddr -full`.

## <a name="get-eflowvmfeature"></a>Get-EflowVmFeature

Der Befehl **Get-EflowVmFeature** gibt den Status der Aktivierung von IoT Edge für Linux unter Windows-Features zurück.

| Parameter | Zulässige Werte | Kommentare |
| --------- | --------------- | -------- |
| Feature | **DpsTpm** | Der abzufragende Featurename |

Weitere Informationen erhalten Sie mit dem Befehl `Get-Help Get-EflowVmFeature -full`.

## <a name="get-eflowvmname"></a>Get-EflowVmName

Der Befehl **Get-EflowVmName** gibt den aktuellen Hostnamen des virtuellen Computers zurück. So wird die Tatsache berücksichtigt, dass sich der Windows-Hostname im Laufe der Zeit ändern kann.

Weitere Informationen erhalten Sie mit dem Befehl `Get-Help Get-EflowVmName -full`.

## <a name="get-eflowvmtelemetryoption"></a>Get-EflowVmTelemetryOption

Der Befehl **Get-EflowVmTelemetryOption** zeigt den Status der Telemetrie (entweder **Optional** oder **Erforderlich**) im virtuellen Computer an.

Weitere Informationen erhalten Sie mit dem Befehl `Get-Help Get-EflowVmTelemetryOption -full`.

## <a name="get-eflowvmtpmprovisioninginfo"></a>Get-EflowVmTpmProvisioningInfo

Der Befehl **Get-EflowVmTpmProvisioningInfo** gibt die TPM-Bereitstellungsinformationen zurück. Bei diesem Befehl werden keine Parameter verwendet. Er gibt ein Objekt mit zwei Eigenschaften zurück:

* Endorsement Key
* Registrierungs-ID

Weitere Informationen erhalten Sie mit dem Befehl `Get-Help Get-EflowVmTpmProvisioningInfo -full`.

## <a name="invoke-eflowvmcommand"></a>Invoke-EflowVmCommand

Der Befehl **Invoke-EflowVMCommand** führt einen Linux-Befehl im virtuellen Computer aus und gibt die Ausgabe zurück. Dieser Befehl funktioniert nur bei Linux-Befehlen, die eine finite Ausgabe zurückgeben. Er kann nicht bei Linux-Befehlen verwendet werden, die eine Benutzerinteraktion erfordern oder unbegrenzt ausgeführt werden.

Mit den folgenden optionalen Parametern kann der Befehl im Voraus angegeben werden.

| Parameter | Zulässige Werte | Kommentare |
| --------- | --------------- | -------- |
| command | String | Befehl, der im virtuellen Computer ausgeführt werden soll. |
| ignoreError | Keine |  Wenn dieses Flag vorhanden ist, ignorieren Sie Fehler aus dem Befehl. |

Weitere Informationen erhalten Sie mit dem Befehl `Get-Help Invoke-EflowVmCommand -full`.

## <a name="provision-eflowvm"></a>Provision-EflowVm

Der Befehl **Provision-EflowVm** fügt die Bereitstellungsinformationen für Ihr IoT Edge-Gerät der IoT Edge-Datei `config.yaml` des virtuellen Computers hinzu.

| Parameter | Zulässige Werte | Kommentare |
| --------- | --------------- | -------- |
| provisioningType | **ManualConnectionString**, **ManualX509**, **DpsTPM**, **DpsX509** oder **DpsSymmetricKey** |  Definiert den Bereitstellungstyp, den Sie für Ihr IoT Edge-Gerät verwenden möchten. |
| devConnString | Die Geräteverbindungszeichenfolge eines vorhandenen IoT Edge-Geräts | Geräteverbindungszeichenfolge zum manuellen Bereitstellen eines IoT Edge-Geräts (**ManualConnectionString**). |
| iotHubHostname | Der Hostname eines vorhandenen IoT-Hubs | Azure IoT-Hub-Hostname für die Bereitstellung eines IoT Edge-Geräts (**ManualX509**). |
| deviceId | Die Geräte-ID eines vorhandenen IoT Edge-Geräts | Geräte-ID für die Bereitstellung eines IoT Edge-Geräts (**ManualX509**). |
| scopeId | Die Bereichs-ID für eine vorhandene DPS-Instanz. | Bereichs-ID für die Bereitstellung eines IoT Edge-Geräts (**DpsTPM**, **DpsX509** oder **DpsSymmetricKey**). |
| symmKey | Der Primärschlüssel für eine vorhandene DPS-Registrierung oder der Primärschlüssel eines vorhandenen IoT Edge-Geräts, das mithilfe von symmetrischen Schlüsseln registriert wurde | Symmetrischer Schlüssel für die Bereitstellung eines IoT Edge-Geräts (**DpsSymmetricKey**). |
| registrationId | Die Registrierungs-ID eines vorhandenen IoT Edge-Geräts | Registrierungs-ID für die Bereitstellung eines IoT Edge-Geräts (**DpsSymmetricKey**). |
| identityCertPath | Verzeichnispfad | Absoluter Zielpfad des Identitätszertifikats auf Ihrem Windows-Hostcomputer (**ManualX509**, **DpsX509**). |
| identityPrivKeyPath | Verzeichnispfad | Absoluter Quellpfad des privaten Identitätsschlüssels auf Ihrem Windows-Hostcomputer (**ManualX509**, **DpsX509**). |

Weitere Informationen erhalten Sie mit dem Befehl `Get-Help Provision-EflowVm -full`.

## <a name="set-eflowvm"></a>Set-EflowVM

Der Befehl **Set-EflowVM** aktualisiert die Konfiguration des virtuellen Computers mit den angeforderten Eigenschaften. Verwenden Sie die optionalen Parameter zum Definieren einer bestimmten Konfiguration für den virtuellen Computer.

| Parameter | Zulässige Werte | Kommentare |
| --------- | --------------- | -------- |
| cpuCount | Ganzzahliger Wert zwischen 1 und den CPU-Kernen des Geräts | Anzahl der CPU-Kerne für den virtuellen Computer. |
| memoryInMB | Ganzzahliger Wert zwischen 1.024 und der maximalen Menge an freiem Arbeitsspeicher des Geräts | Der für den virtuellen Computer zugeordnete Arbeitsspeicher. |
| gpuName | GPU-Gerätename |  Name des GPU-Geräts, das für Passthrough verwendet werden soll. |
| gpuPassthroughType | **DirectDeviceAssignment**, **ParaVirtualization** oder keine (kein Passthrough) |  GPU-Passthrough-Typ |
| gpuCount | Ganzzahliger Wert zwischen 1 und den GPU-Kernen des Geräts | Anzahl der GPU-Geräte für den virtuellen Computer **Hinweis**: Nur gültig, wenn „DirectDeviceAssignment“ verwendet wird |
| headless | Keine | Wenn dieses Flag vorhanden ist, bestimmt es, ob der Benutzer eine ausgegebene Sicherheitswarnung bestätigen muss. |

Weitere Informationen erhalten Sie mit dem Befehl `Get-Help Set-EflowVM -full`.

## <a name="set-eflowvmfeature"></a>Set-EflowVmFeature

Der Befehl **Set-EflowVmFeature** aktiviert oder deaktiviert den Status von IoT Edge für Linux unter Windows-Features.

| Parameter | Zulässige Werte | Kommentare |
| --------- | --------------- | -------- |
| Feature | **DpsTpm** | Featurename zum Umschalten. |
| enable | Keine | Wenn dieses Flag vorhanden ist, aktiviert der Befehl das Feature. |

Weitere Informationen erhalten Sie mit dem Befehl `Get-Help Set-EflowVmFeature -full`.

## <a name="set-eflowvmtelemetryoption"></a>Set-EflowVmTelemetryOption

Der Befehl **Set-EflowVmTelemetryOption** aktiviert oder deaktiviert die optionale Telemetrie im virtuellen Computer.

| Parameter | Zulässige Werte | Kommentare |
| --------- | --------------- | -------- |
| optionalTelemetry | **True** oder **False** | Gibt an, ob optionale Telemetrie ausgewählt wurde. |

Weitere Informationen erhalten Sie mit dem Befehl `Get-Help Set-EflowVmTelemetryOption -full`.

## <a name="start-eflowvm"></a>Start-EflowVm

Der Befehl **Start-EflowVm** startet den virtuellen Computer. Wenn der virtuelle Computer bereits gestartet wurde, wird keine Aktion ausgeführt.

Weitere Informationen erhalten Sie mit dem Befehl `Get-Help Start-EflowVm -full`.

## <a name="stop-eflowvm"></a>Stop-EflowVm

Der Befehl **Stop-EflowVm** beendet den virtuellen Computer. Wenn der virtuelle Computer bereits beendet wurde, wird keine Aktion ausgeführt.

Weitere Informationen erhalten Sie mit dem Befehl `Get-Help Stop-EflowVm -full`.

## <a name="verify-eflowvm"></a>Verify-EflowVm

Der Befehl **Verify-EflowVm** ist eine verfügbar gemachte Funktion, die überprüft, ob der virtuelle IoT Edge für Linux unter Windows-Computer erstellt wurde. Bei diesem Befehl werden nur allgemeine Parameter verwendet, und wenn der virtuelle Computer erstellt wurde, wird **True** zurückgegeben, andernfalls **False**.

Weitere Informationen erhalten Sie mit dem Befehl `Get-Help Verify-EflowVm -full`.

## <a name="next-steps"></a>Nächste Schritte

Im folgenden Artikel erfahren Sie, wie Sie diese Befehle zum Installieren und Bereitstellen von IoT Edge für Linux unter Windows verwenden können:

* [Installieren von Azure IoT Edge für Linux unter Windows](./how-to-provision-single-device-linux-on-windows-symmetric.md)
