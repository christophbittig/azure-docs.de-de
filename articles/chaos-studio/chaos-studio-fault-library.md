---
title: Chaos Studio Fehler- und Aktionsbibliothek
description: Verstehen Sie die verfügbaren Aktionen, die Sie mit Chaos Studio verwenden können, einschließlich aller Voraussetzungen und Parameter.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/10/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: 60e9dd18d8c9357f2696ec79be08925cdd7e97da
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132371009"
---
# <a name="chaos-studio-fault-and-action-library"></a>Chaos Studio Fehler- und Aktionsbibliothek

Die folgenden Fehler stehen heute zur Verfügung. Besuchen Sie die Seite [Störungsanbieter](./chaos-studio-fault-providers.md), um zu erfahren, welche Ressourcentypen unterstützt werden.

## <a name="time-delay"></a>Zeitverzögerung

| Eigenschaft | Wert |
|-|-|
| Störungsanbieter | – |
| Unterstützte OS-Typen | – |
| Beschreibung | Fügt eine Zeitverzögerung vor, zwischen oder nach anderen Aktionen hinzu. Nützlich für das Warten auf die Auswirkungen eines Fehlers in einem Dienst oder für das Warten auf den Abschluss einer Aktivität außerhalb des Experiments (z. B. das Warten auf die Selbstheilung, bevor ein weiterer Fehler injiziert wird). |
| Voraussetzungen | – |
| URN | urn:provider:Azure-chaosStudio:Microsoft.Azure.Chaos.Delay.Timed |
| duration | Die Dauer der Verzögerung im ISO 8601-Format (Beispiel: PT10M) |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [ 
    {
      "type": "delay",
      "name": "urn:provider:Azure-chaosStudio:Microsoft.Azure.Chaos.Delay.Timed",
      "duration": "PT10M"
    }
  ] 
}
```

## <a name="cpu-pressure"></a>CPU-Druck

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | CPUDruck-1.0 |
| Zieltyp | Microsoft-Agent |
| Unterstützte OS-Typen | Windows, Linux |
| Beschreibung | Fügt CPU-Druck bis zum angegebenen Wert auf der VM hinzu, in die dieser Fehler für die Dauer der Fehleraktion injiziert wird. Der künstliche CPU-Druck wird am Ende der Laufzeit oder bei Abbruch des Experiments entfernt. |
| Voraussetzungen | **Linux:** Die Ausführung des Fehlers auf einer Linux-VM erfordert die Installation des Dienstprogramms **stress-ng**. Sie können es mit dem Paketmanager Ihrer Linux-Distribution installieren, </br> APT-Befehl zur Installation von stress-ng: *sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> YUM-Befehl zur Installation von stress-ng: *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng*  |
| | **Windows:** Nichts. |
| URN | urn:csci:microsoft:agent:cpuPressure/1.0 |
| Parameter (Schlüssel, Wert)  |
| DruckLevel | Eine ganze Zahl zwischen 1 und 99, die angibt, wie viel CPU-Druck (%) auf die VM ausgeübt wird. |
| virtualMachineScaleSetInstances | Ein Array von Instanz-IDs beim Anwenden dieses Fehlers auf eine Skalierungsskala. Erforderlich für SKALIERUNGEN FÜR VIRTUELLE COMPUTER. |

### <a name="sample-json"></a>Beispiel-JSON
```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:cpuPressure/1.0",
      "parameters": [
        {
          "key": "pressureLevel",
          "value": "95"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

### <a name="notes"></a>Hinweise
Bekannte Probleme unter Linux:
1. Der Stress-Effekt wird möglicherweise nicht korrekt beendet, wenn AzureChaosAgent unerwartet beendet wird.
2. Der Linux-CPU-Fehler wird nur unter Ubuntu 16.04-LTS und Ubuntu 18.04-LTS getestet.

## <a name="physical-memory-pressure"></a>Physischer Gedächtnisdruck

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | Physischer Gedächtnisdruck-1.0 |
| Zieltyp | Microsoft-Agent |
| Unterstützte OS-Typen | Windows, Linux |
| BESCHREIBUNG | Hinzufügen von physischem Speicherdruck bis zum angegebenen Wert auf der VM, in die dieser Fehler für die Dauer der Fehleraktion injiziert wird. Der künstliche physische Gedächtnisdruck wird am Ende der Laufzeit oder bei Abbruch des Experiments aufgehoben. |
| Voraussetzungen | **Linux:** Die Ausführung des Fehlers auf einer Linux-VM erfordert die Installation des Dienstprogramms **stress-ng**. Sie können es mit dem Paketmanager Ihrer Linux-Distribution installieren, </br> APT-Befehl zur Installation von stress-ng: *sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> YUM-Befehl zur Installation von stress-ng: *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng*  |
| | **Windows:** Nichts. |
| URN | urn:csci:microsoft:agent:physicalMemoryPressure/1.0 |
| Parameter (Schlüssel, Wert) |  |
| DruckLevel | Eine ganze Zahl zwischen 1 und 99, die angibt, wie viel physischer Speicherdruck (%) auf die VM angewendet wird. |
| virtualMachineScaleSetInstances | Ein Array von Instanz-IDs beim Anwenden dieses Fehlers auf eine Skalierungsskala. Erforderlich für SKALIERUNGEN FÜR VIRTUELLE COMPUTER. |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:physicalMemoryPressure/1.0",
      "parameters": [
        {
          "key": "pressureLevel",
          "value": "95"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="virtual-memory-pressure"></a>Virtueller Speicherdruck

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | Virtueller Speicherdruck-1.0 |
| Zieltyp | Microsoft-Agent |
| Unterstützte OS-Typen | Windows |
| Beschreibung | Hinzufügen von virtuellem Speicherdruck bis zum angegebenen Wert auf der VM, in die dieser Fehler für die Dauer der Fehleraktion injiziert wird. Der künstliche virtuelle Speicherdruck wird am Ende der Laufzeit oder bei Abbruch des Experiments entfernt. |
| Voraussetzungen | Keine |
| URN | urn:csci:microsoft:agent:physicalMemoryPressure/1.0 |
| Parameter (Schlüssel, Wert) |  |
| DruckLevel | Eine ganze Zahl zwischen 1 und 99, die angibt, wie viel physischer Speicherdruck (%) auf die VM angewendet wird. |
| virtualMachineScaleSetInstances | Ein Array von Instanz-IDs beim Anwenden dieses Fehlers auf eine Skalierungsskala. Erforderlich für SKALIERUNGEN FÜR VIRTUELLE COMPUTER. |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:virtualMemoryPressure/1.0",
      "parameters": [
        {
          "key": "pressureLevel",
          "value": "95"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="disk-io-pressure-windows"></a>Azure Data Box Disk-E/A-Druck (Windows)

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | DiskIOPressure-1.0 |
| Zieltyp | Microsoft-Agent |
| Unterstützte OS-Typen | Windows |
| Beschreibung | Verwendet das Dienstprogramm [diskspd](https://github.com/Microsoft/diskspd/wiki), um dem Primärspeicher der VM Druck auf die Festplatte hinzuzufügen, wo er für die Dauer der Fehleraktion eingespeist wird. Dieser Fehler kann auf fünf verschiedene Arten ausgeführt werden. Der künstliche Scheibendruck wird am Ende der Laufzeit oder bei Abbruch des Experiments entfernt. |
| Voraussetzungen | Keine |
| URN | urn:csci:microsoft:agent:diskIOPressure/1.0 |
| Parameter (Schlüssel, Wert) |  |
| DruckModus | Der voreingestellte Modus des Festplattendrucks, der dem primären Speicher der VM hinzugefügt wird. Muss einer der PressureModes in der nachstehenden Tabelle sein. |
| virtualMachineScaleSetInstances | Ein Array von Instanz-IDs beim Anwenden dieses Fehlers auf eine Skalierungsskala. Erforderlich für SKALIERUNGEN FÜR VIRTUELLE COMPUTER. |

### <a name="pressure-modes"></a>Druckmodi

| DruckModus | Beschreibung |
| -- | -- |
| PremiumSpeicherP10IOPS | AnzahlThreads = 1<br/>zufälligeBlockgrößeInKB = 64<br/>zufälligeSaat = 10<br/>AnzahlIOperThread = 25<br/>GrößederBlöckeInKB = 8<br/>GrößeSchreibPufferInKB = 64<br/>dateiGrößeInGB = 2<br/>percentOfWriteActions = 50 |
| PremiumSpeicherP10Drosselung |<br/>AnzahlThreads = 2<br/>zufälligeBlockgrößeInKB = 64<br/>zufälligeSaat = 10<br/>AnzahlIOperThread = 25<br/>GrößederBlöckeInKB = 64<br/>GrößeSchreibPufferInKB = 64<br/>dateiGrößeInGB = 1<br/>percentOfWriteActions = 50 |
| PremiumSpeicherP50IOPS | AnzahlThreads = 32<br/>zufälligeBlockgrößeInKB = 64<br/>zufälligeSaat = 10<br/>AnzahlIOperThread = 32<br/>GrößederBlöckeInKB = 8<br/>GrößeSchreibPufferInKB = 64<br/>dateiGrößeInGB = 1<br/>percentOfWriteActions = 50 |
| PremiumSpeicherP50Drosselung | AnzahlThreads = 2<br/>zufälligeBlockgrößeInKB = 1024<br/>zufälligeSaat = 10<br/>AnzahlIOperThread = 2<br/>GrößederBlöckeInKB = 1024<br/>GrößeSchreibPufferInKB = 1024<br/>fileSizeInGB = 20<br/>percentOfWriteActions = 50|
| Standard | AnzahlThreads = 2<br/>zufälligeBlockgrößeInKB = 64<br/>zufälligeSaat = 10<br/>AnzahlIOperThread = 2<br/>GrößederBlöckeInKB = 64<br/>GrößeSchreibPufferInKB = 64<br/>dateiGrößeInGB = 1<br/>percentOfWriteActions = 50 |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:diskIOPressure/1.0",
      "parameters": [
        {
          "key": "pressureMode",
          "value": "PremiumStorageP10IOPS"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="disk-io-pressure-linux"></a>Disk E/A Druck (Linux)

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | LinuxDiskIODruck-1.0 |
| Zieltyp | Microsoft-Agent |
| Unterstützte OS-Typen | Linux |
| Beschreibung | Mit Hilfe von Stress wird Druck auf die Scheibe ausgeübt. Es werden ein oder mehrere Arbeitsprozesse gestartet, die E/A-Prozesse mit temporären Dateien durchführen. Für Einzelheiten zur Druckausübung siehe https://wiki.ubuntu.com/Kernel/Reference/stress-ng. |
| Voraussetzungen | Für die Ausführung des Fehlers auf einer Linux-VM muss das Dienstprogramm **stress-ng** installiert sein. Sie können es mit dem Paketmanager Ihrer Linux-Distribution installieren, </br> APT-Befehl zur Installation von stress-ng: *sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> YUM-Befehl zur Installation von stress-ng: *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng* |
| URN | urn:csci:microsoft:agent:diskIOPressure/1.0 |
| Parameter (Schlüssel, Wert) |  |
| ArbeiterZahl | Anzahl der auszuführenden Arbeitsprozesse. Wird dieser Wert auf 0 gesetzt, werden so viele Arbeitsprozesse erzeugt, wie es Prozessoren gibt. |
| fileSizePerWorker | Größe der temporären Datei, für die ein Worker E/A-Operationen durchführt. Ganzzahl plus eine Einheit in Bytes (b), Kilobytes (k), Megabytes (m) oder Gigabytes (g) (z. B. 4m für 4 Megabytes, 256g für 256 Gigabytes) |
| Blockgröße | Blockgröße, die für E/A-Operationen auf der Festplatte verwendet werden soll und auf 4 Megabyte begrenzt ist. Ganzzahl plus eine Einheit in Bytes (b), Kilobytes (k) oder Megabytes (m) (z. B. 512k für 512 Kilobytes) |
| virtualMachineScaleSetInstances | Ein Array von Instanz-IDs beim Anwenden dieses Fehlers auf eine Skalierungsskala. Erforderlich für SKALIERUNGEN FÜR VIRTUELLE COMPUTER. |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:linuxDiskIOPressure/1.0",
      "parameters": [
        {
          "key": "workerCount",
          "value": "0"
        },
        {
          "key": "fileSizePerWorker",
          "value": "512m"
        },
        {
          "key": "blockSize",
          "value": "256k"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="arbitrary-stress-ng-stress"></a>Beliebiger Stress-ng stress

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | StressNg-1.0 |
| Zieltyp | Microsoft-Agent |
| Unterstützte OS-Typen | Linux |
| BESCHREIBUNG | Führen Sie jeden stress-ng-Befehl aus, indem Sie Argumente direkt an stress-ng übergeben. Nützlich für den Fall, dass einer der vordefinierten Fehler für Stress-ng nicht Ihren Anforderungen entspricht. |
| Voraussetzungen | Für die Ausführung des Fehlers auf einer Linux-VM muss das Dienstprogramm **stress-ng** installiert sein. Sie können es mit dem Paketmanager Ihrer Linux-Distribution installieren, </br> APT-Befehl zur Installation von stress-ng: *sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng* </br> YUM-Befehl zur Installation von stress-ng: *sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng* |
| URN | urn:csci:microsoft:agent:stressNg/1.0 |
| Parameter (Schlüssel, Wert) |  |
| stressNgArgumente | Ein oder mehrere Argumente, die an den stress-ng-Prozess übergeben werden. Für Einzelheiten zu den möglichen Argumenten von stress-ng siehe https://wiki.ubuntu.com/Kernel/Reference/stress-ng |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:stressNg/1.0",
      "parameters": [
        {
          "key": "stressNgArguments",
          "value": "--random 64"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="stop-windows-service"></a>Windows-Dienst beenden

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | StopService-1.0 |
| Zieltyp | Microsoft-Agent |
| Unterstützte OS-Typen | Windows |
| Beschreibung | Verwendet die Windows Service Controller-APIs, um einen Windows-Dienst für die Dauer des Fehlers anzuhalten und ihn am Ende der Dauer oder bei Abbruch des Experiments neu zu starten. |
| Voraussetzungen | Keine |
| URN | urn:csci:microsoft:agent:stressNg/1.0 |
| Parameter (Schlüssel, Wert) |  |
| serviceName | Der Name des Windows-Dienstes, den Sie anhalten möchten. Sie können `sc.exe query` in der Eingabeaufforderung ausführen, um die Namen von Diensten zu erkunden, Windows-freundliche Namen werden nicht unterstützt. |
| virtualMachineScaleSetInstances | Ein Array von Instanz-IDs beim Anwenden dieses Fehlers auf eine Skalierungsskala. Erforderlich für SKALIERUNGEN FÜR VIRTUELLE COMPUTER. |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:stopService/1.0",
      "parameters": [
        {
          "key": "serviceName",
          "value": "nvagent"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="time-change"></a>Zeitumstellung

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | TimeChange-1.0 |
| Zieltyp | Microsoft-Agent |
| Unterstützte OS-Typen | Windows |
| Beschreibung | Ändert die Systemzeit für die VM, in die es eingespeist wird, und setzt sie am Ende der Laufzeit oder bei Abbruch des Experiments zurück. |
| Voraussetzungen | Keine |
| URN | urn:csci:microsoft:agent:timeChange/1.0 |
| Parameter (Schlüssel, Wert) |  |
| dateTime | Eine DateTime-Zeichenkette im Format [ISO8601](https://www.cryptosys.net/pki/manpki/pki_iso8601datetime.html). Wenn JJJJ-MM-TT-Werte fehlen, werden sie standardmäßig auf den aktuellen Tag gesetzt, wenn das Experiment läuft. Wenn die Werte Thh:mm:ss fehlen, ist der Standardwert 12:00:00 AM. Wenn eine zweistellige Jahreszahl angegeben wird (JJ), wird sie auf der Grundlage des aktuellen Jahrhunderts in eine vierstellige Jahreszahl (JJJJ) umgewandelt. Fehlt \<Z\>, wird als Standardwert der Offset der lokalen Zeitzone verwendet. \<Z\> muss immer ein Vorzeichensymbol (negativ oder positiv) enthalten. |
| virtualMachineScaleSetInstances | Ein Array von Instanz-IDs beim Anwenden dieses Fehlers auf eine Skalierungsskala. Erforderlich für SKALIERUNGEN FÜR VIRTUELLE COMPUTER. |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:timeChange/1.0",
      "parameters": [
        {
          "key": "dateTime",
          "value": "2038-01-01T03:14:07"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="kill-process"></a>Kill-Prozess

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | KillProcess-1.0 |
| Zieltyp | Microsoft-Agent |
| Unterstützte OS-Typen | Windows, Linux |
| Beschreibung | Beendet alle laufenden Instanzen eines Prozesses, der mit dem in den Fehlerparametern angegebenen Prozessnamen übereinstimmt. Innerhalb der für die Fault Action festgelegten Dauer wird ein Prozess auf der Grundlage des angegebenen Kill-Intervalls wiederholt beendet. Bei diesem Fehler handelt es sich um einen destruktiven Fehler, bei dem der Systemadministrator den Prozess manuell wiederherstellen müsste, wenn die Selbstheilung dafür konfiguriert ist. |
| Voraussetzungen | Keine |
| URN | urn:csci:microsoft:agent:killProcess/1.0 |
| Parameter (Schlüssel, Wert) |  |
| ProzessName | Name eines Prozesses, der auf einer VM läuft (ohne .exe) |
| killIntervalInMilliseconds | Zeitspanne, die der Fault zwischen aufeinanderfolgenden Kill-Versuchen wartet, in Millisekunden. |
| virtualMachineScaleSetInstances | Ein Array von Instanz-IDs beim Anwenden dieses Fehlers auf eine Skalierungsskala. Erforderlich für SKALIERUNGEN FÜR VIRTUELLE COMPUTER. |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:killProcess/1.0",
      "parameters": [
        {
          "key": "processName",
          "value": "myapp"
        },
        {
          "key": "killIntervalInMilliseconds",
          "value": "1000"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="dns-failure"></a>DNS-Fehler.

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | DNSFehler-1.0 |
| Zieltyp | Microsoft-Agent |
| Unterstützte OS-Typen | Windows |
| Beschreibung | Ersetzt die Antwort einer DNS-Lookup-Anforderung durch einen angegebenen Fehlercode. |
| Voraussetzungen | Keine |
| URN | urn:csci:microsoft:agent:dnsFailure/1.0 |
| Parameter (Schlüssel, Wert) |  |
| hosts | Begrenztes JSON-Array von Hostnamen, für die eine DNS Lookup Anforderung fehlschlagen soll. |
| dnsFailureReturnCode | DNS-Fehlercode, der an den Client für die fehlgeschlagene Lookup zurückgegeben wird (FormErr, ServFail, NXDomain, NotImp, Refused, XDomain, YXRRSet, NXRRSet, NotAuth, NotZone). Weitere Einzelheiten zu DNS-Rückgabecodes finden Sie auf [der IANA-Website](https://www.iana.org/assignments/dns-parameters/dns-parameters.xml#dns-parameters-6) |
| virtualMachineScaleSetInstances | Ein Array von Instanz-IDs beim Anwenden dieses Fehlers auf eine Skalierungsskala. Erforderlich für SKALIERUNGEN FÜR VIRTUELLE COMPUTER. |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:dnsFailure/1.0",
      "parameters": [
        {
          "key": "hosts",
          "value": "[ \"www.bing.com\", \"msdn.microsoft.com\" ]"
        },
        {
          "key": "dnsFailureReturnCode",
          "value": "ServFail"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

### <a name="limitations"></a>Einschränkungen

* Der DNS-Fehler erfordert Windows 2019 RS5 oder neuer.
* Der DNS-Cache wird während der Dauer der Störung für die in der Störung definierten Hostnamen ignoriert.

## <a name="network-latency"></a>Netzwerklatenz

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | Netzwerk-Latenz-1.0 |
| Zieltyp | Microsoft-Agent |
| Unterstützte OS-Typen | Windows |
| Beschreibung | Erhöht die Netzwerklatenz für einen bestimmten Anschlussbereich und Netzwerkblock. |
| Voraussetzungen | Der Agent muss als Administrator ausgeführt werden. Wenn der Agent als VM-Erweiterung installiert ist, wird er standardmäßig als Administrator ausgeführt. |
| URN | urn:csci:microsoft:agent:networkLatency/1.0 |
| Parameter (Schlüssel, Wert) |  |
| LatenzInMillisekunden | Höhe der anzuwendenden Latenz in Millisekunden. |
| ZielFilter | Abgegrenztes JSON-Array von Paketfiltern, die festlegen, welche ausgehenden Pakete für die Fehlerinjektion verwendet werden sollen. Maximal 3. |
| address | IP-Adresse, die den Beginn des IP-Bereichs angibt. |
| SubnetMaske | Subnetzmaske für den IP-Adressbereich. |
| portLow | (Optional) Portnummer für den Beginn des Portbereichs. |
| portHigh | (Optional) Anschlussnummer des Endes des Anschlussbereichs. |
| virtualMachineScaleSetInstances | Ein Array von Instanz-IDs beim Anwenden dieses Fehlers auf eine Skalierungsskala. Erforderlich für SKALIERUNGEN FÜR VIRTUELLE COMPUTER. |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:networkLatency/1.0",
      "parameters": [
        {
          "key": "destinationFilters",
          "value": "[ { \"address\": \"23.45.229.97\", \"subnetMask\": \"255.255.255.224\", \"portLow\": \"5000\", \"portHigh\": \"5200\" } ]"
        },
        {
          "key": "latencyInMilliseconds",
          "value": "100",
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="network-disconnect"></a>Netzwerktrennung

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | Netzwerktrennung-1.0 |
| Zieltyp | Microsoft-Agent |
| Unterstützte OS-Typen | Windows |
| Beschreibung | Blockiert den ausgehenden Netzwerkverkehr für den angegebenen Portbereich und Netzwerkblock. |
| Voraussetzungen | Der Agent muss als Administrator ausgeführt werden. Wenn der Agent als VM-Erweiterung installiert ist, wird er standardmäßig als Administrator ausgeführt. |
| URN | urn:csci:microsoft:agent:networkLatency/1.0 |
| Parameter (Schlüssel, Wert) |  |
| ZielFilter | Abgegrenztes JSON-Array von Paketfiltern, die festlegen, welche ausgehenden Pakete für die Fehlerinjektion verwendet werden sollen. Maximal 3. |
| address | IP-Adresse, die den Beginn des IP-Bereichs angibt. |
| SubnetMaske | Subnetzmaske für den IP-Adressbereich. |
| portLow | (Optional) Portnummer für den Beginn des Portbereichs. |
| portHigh | (Optional) Anschlussnummer des Endes des Anschlussbereichs. |
| virtualMachineScaleSetInstances | Ein Array von Instanz-IDs beim Anwenden dieses Fehlers auf eine Skalierungsskala. Erforderlich für SKALIERUNGEN FÜR VIRTUELLE COMPUTER. |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:networkDisconnect/1.0",
      "parameters": [
        {
          "key": "destinationFilters",
          "value": "[ { \"address\": \"23.45.229.97\", \"subnetMask\": \"255.255.255.224\", \"portLow\": \"5000\", \"portHigh\": \"5200\" } ]"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

> [!WARNING]
> Der Netzwerktrennungsfehler betrifft nur neue Verbindungen. Bestehende **aktive** Verbindungen bleiben weiterhin bestehen. Sie können den Dienst oder Prozess neu starten, um die Unterbrechung von Verbindungen zu erzwingen.

## <a name="network-disconnect-with-firewall-rule"></a>Netzwerkunterbrechung mit Firewall-Regel

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | NetworkDisconnectViaFirewall-1.0 |
| Zieltyp | Microsoft-Agent |
| Unterstützte OS-Typen | Windows |
| Beschreibung | Wendet eine Windows-Firewall-Regel an, um ausgehenden Datenverkehr für den angegebenen Portbereich und den Netzwerkblock zu blockieren. |
| Voraussetzungen | Der Agent muss als Administrator ausgeführt werden. Wenn der Agent als VM-Erweiterung installiert ist, wird er standardmäßig als Administrator ausgeführt. |
| URN | urn:csci:microsoft:agent:networkDisconnectViaFirewall/1.0 |
| Parameter (Schlüssel, Wert) |  |
| ZielFilter | Abgegrenztes JSON-Array von Paketfiltern, die festlegen, welche ausgehenden Pakete für die Fehlerinjektion verwendet werden sollen. Maximal 3. |
| address | IP-Adresse, die den Beginn des IP-Bereichs angibt. |
| SubnetMaske | Subnetzmaske für den IP-Adressbereich. |
| portLow | (Optional) Portnummer für den Beginn des Portbereichs. |
| portHigh | (Optional) Anschlussnummer des Endes des Anschlussbereichs. |
| virtualMachineScaleSetInstances | Ein Array von Instanz-IDs beim Anwenden dieses Fehlers auf eine Skalierungsskala. Erforderlich für SKALIERUNGEN FÜR VIRTUELLE COMPUTER. |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:agent:networkDisconnectViaFirewall/1.0",
      "parameters": [
        {
          "key": "destinationFilters",
          "value": "[ { \"Address\": \"23.45.229.97\", \"SubnetMask\": \"255.255.255.224\", \"PortLow\": \"5000\", \"PortHigh\": \"5200\" } ]"
        },
        {
          "key": "virtualMachineScaleSetInstances",
          "value": "[0,1,2]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="arm-virtual-machine-shutdown"></a>Herunterfahren der virtuellen ARM-Maschine
| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | Herunterfahren-1.0 |
| Zieltyp | Microsoft-VirtualMachine |
| Unterstützte OS-Typen | Windows, Linux |
| Beschreibung | Schaltet eine VM für die Dauer des Fehlers ab und startet optional die VM am Ende der Fehlerdauer oder bei Abbruch des Experiments neu. Es werden nur virtuelle Azure Resource Manager-Computer unterstützt. |
| Voraussetzungen | Keine |
| URN | urn:csci:microsoft:virtualMachine:shutdown/1.0 |
| Parameter (Schlüssel, Wert) |  |
| abruptesHerunterfahren | (Optional) Boolescher Wert, der angibt, ob die VM sanft oder abrupt (destruktiv) heruntergefahren werden soll. |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:virtualMachine:shutdown/1.0",
      "parameters": [
        {
          "key": "abruptShutdown",
          "value": "false"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="arm-virtual-machine-scale-set-instance-shutdown"></a>ARM virtuelle Maschine skalieren, Instanz herunterfahren

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | Herunterfahren-1.0 |
| Zieltyp | Microsoft-VirtualMachineScaleSet |
| Unterstützte OS-Typen | Windows, Linux |
| Beschreibung | Fährt eine VM-Skalierungssatzinstanz für die Dauer des Fehlers herunter oder beendet sie, und startet die VM optional am Ende der Fehlerdauer neu, oder wenn das Experiment abgebrochen wird. |
| Voraussetzungen | Keine |
| URN | urn:csci:microsoft:virtualMachineScaleSet:shutdown/1.0 |
| Parameter (Schlüssel, Wert) |  |
| abruptesHerunterfahren | (Optional) Boolescher Wert, der angibt, ob die Skalierungsinstanz der virtuellen Maschine sanft oder abrupt (destruktiv) heruntergefahren werden soll. |
| instances | Eine Zeichenkette, bei der es sich um ein abgegrenztes Array von Instanz-IDs virtueller Maschinenskalierungssätze handelt, auf die der Fehler angewendet wird. |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:virtualMachineScaleSet:shutdown/1.0",
      "parameters": [
        {
          "key": "abruptShutdown",
          "value": "true"
        },
        {
          "key": "instances",
          "value": "[\"1\",\"3\"]"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="cosmos-db-failover"></a>Cosmos DB Ausfallsicherung

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | Ausfallsicherung-1.0 |
| Zieltyp | Microsoft-CosmosDB |
| Beschreibung | Bewirkt, dass ein Cosmos-DB-Konto mit einer einzelnen Schreibregion auf eine angegebene Leseregion übergeht, um einen [Ausfall der Schreibregion](../cosmos-db/high-availability.md#multi-region-accounts-with-a-single-write-region-write-region-outage) zu simulieren |
| Voraussetzungen | Keine |
| URN | urn:csci:microsoft:cosmosDB:failover/1.0 |
| Parameter (Schlüssel, Wert) |  |
| readRegion | Die Leseregion, die während der Ausfallsicherung zur Schreibregion befördert werden soll, z. B. "East US 2". |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:cosmosDB:failover/1.0",
      "parameters": [
        {
          "key": "readRegion",
          "value": "West US 2"
        }
      ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-network-faults"></a>AKS Chaos Mesh Netzstörungen

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | NetzwerkChaos-2.1 |
| Zieltyp | Microsoft-AzureKubernetesServiceChaosMesh |
| Beschreibung | Bewirkt, dass ein über [Chaos Mesh](https://chaos-mesh.org/docs/simulate-network-chaos-on-kubernetes/) verfügbarer Netzwerkfehler gegen Ihren AKS-Cluster läuft. Nützlich für die Wiederherstellung von AKS-Vorfällen aufgrund von Netzwerkausfällen, Verzögerungen, Duplikaten, Verlusten und Beschädigungen. |
| Voraussetzungen | Für den AKS-Cluster muss [Chaos Mesh bereitgestellt sein.](chaos-studio-tutorial-aks-portal.md) |
| URN | urn:csci:microsoft:azureKubernetesServiceChaosMesh:networkChaos/2.1 |
| Parameter (Schlüssel, Wert) |  |
| jsonSpec | Eine JSON-formatierte chaos Mesh-Spezifikation, die die [NetworkChaos-Art](https://chaos-mesh.org/docs/simulate-network-chaos-on-kubernetes/#create-experiments-using-the-yaml-files)verwendet, wenn sie über eine ARM-Vorlage, eine REST-API oder Azure CLI erstellt wurde. Sie können einen [YAML-zu-JSON-Konverter wie diesen](https://www.convertjson.com/yaml-to-json.htm) verwenden, um das Chaos Mesh YAML in JSON zu konvertieren und zu minimieren, und ein [JSON-String-Escape-Tool wie dieses](https://www.freeformatter.com/json-escape.html) verwenden, um die JSON-Spezifikation zu entschlüsseln. Fügen Sie nur die YAML unter der Eigenschaft "jsonSpec" ein (keine Metadaten, Art, etc.). |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:networkChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"delay\",\"mode\":\"one\",\"selector\":{\"namespaces\":[\"default\"],\"labelSelectors\":{\"app\":\"web-show\"}},\"delay\":{\"latency\":\"10ms\",\"correlation\":\"100\",\"jitter\":\"0ms\"}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-pod-faults"></a>AKS Chaos Mesh pod Fehler

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | PodChaos-2.1 |
| Zieltyp | Microsoft-AzureKubernetesServiceChaosMesh |
| BESCHREIBUNG | Bewirkt, dass ein über [Chaos Mesh](https://chaos-mesh.org/docs/simulate-pod-chaos-on-kubernetes/) verfügbarer Pod-Fehler gegen Ihren AKS-Cluster läuft. Nützlich für die Wiederherstellung von AKS-Vorfällen, die auf Pod-Ausfälle oder Container-Probleme zurückzuführen sind. |
| Voraussetzungen | Für den AKS-Cluster muss [Chaos Mesh bereitgestellt sein.](chaos-studio-tutorial-aks-portal.md) |
| URN | urn:csci:microsoft:azureKubernetesServiceChaosMesh:podChaos/2.1 |
| Parameter (Schlüssel, Wert) |  |
| jsonSpec | Eine JSON-formatierte Chaos Mesh-Spezifikation, die die [PodChaos-Art](https://chaos-mesh.org/docs/simulate-pod-chaos-on-kubernetes/#create-experiments-using-yaml-configuration-files)verwendet, wenn sie über eine ARM-Vorlage, eine REST-API oder Azure CLI erstellt wurde. Sie können einen [YAML-zu-JSON-Konverter wie diesen](https://www.convertjson.com/yaml-to-json.htm) verwenden, um das Chaos Mesh YAML in JSON zu konvertieren und zu minimieren, und ein [JSON-String-Escape-Tool wie dieses](https://www.freeformatter.com/json-escape.html) verwenden, um die JSON-Spezifikation zu entschlüsseln. Fügen Sie nur die YAML unter der Eigenschaft "jsonSpec" ein (keine Metadaten, Art, etc.). |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:podChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"pod-failure\",\"mode\":\"one\",\"duration\":\"30s\",\"selector\":{\"labelSelectors\":{\"app.kubernetes.io\/component\":\"tikv\"}}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-stress-faults"></a>AKS Chaos Mesh-Stress-Fehler

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | StressChaos-2.1 |
| Zieltyp | Microsoft-AzureKubernetesServiceChaosMesh |
| Beschreibung | Bewirkt, dass ein über [Chaos Mesh](https://chaos-mesh.org/docs/simulate-heavy-stress-on-kubernetes/) verfügbarer Stress-Fehler gegen Ihren AKS-Cluster läuft. Nützlich für die Wiederherstellung von AKS-Ereignissen aufgrund von Spannungen in einer Sammlung von Pods, z. B. aufgrund eines hohen CPU- oder Speicherverbrauchs. |
| Voraussetzungen | Für den AKS-Cluster muss [Chaos Mesh bereitgestellt sein.](chaos-studio-tutorial-aks-portal.md) |
| URN | urn:csci:microsoft:azureKubernetesServiceChaosMesh:stressChaos/2.1 |
| Parameter (Schlüssel, Wert) |  |
| jsonSpec | Eine JSON-formatierte Chaos Mesh-Spezifikation, die die [StressChaos-Art](https://chaos-mesh.org/docs/simulate-heavy-stress-on-kubernetes/#create-experiments-using-the-yaml-file)verwendet, wenn sie über eine ARM-Vorlage, eine REST-API oder Azure CLI erstellt wurde. Sie können einen [YAML-zu-JSON-Konverter wie diesen](https://www.convertjson.com/yaml-to-json.htm) verwenden, um das Chaos Mesh YAML in JSON zu konvertieren und zu minimieren, und ein [JSON-String-Escape-Tool wie dieses](https://www.freeformatter.com/json-escape.html) verwenden, um die JSON-Spezifikation zu entschlüsseln. Fügen Sie nur die YAML unter der Eigenschaft "jsonSpec" ein (keine Metadaten, Art, etc.). |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:stressChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"one\",\"selector\":{\"labelSelectors\":{\"app\":\"app1\"}},\"stressors\":{\"memory\":{\"workers\":4,\"size\":\"256MB\"}}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-io-faults"></a>AKS Chaos Mesh E/A-Fehler

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | IOChaos-2.1 |
| Zieltyp | Microsoft-AzureKubernetesServiceChaosMesh |
| Beschreibung | Bewirkt, dass ein über [Chaos Mesh](https://chaos-mesh.org/docs/simulate-io-chaos-on-kubernetes/) verfügbarer IO-Fehler gegen Ihren AKS-Cluster läuft. Nützlich für die Wiederherstellung von AKS-Vorfällen aufgrund von IO-Verzögerungen und Lese-/Schreibfehlern bei der Verwendung von IO-Systemaufrufen wie `open`, `read` und `write`. |
| Voraussetzungen | Für den AKS-Cluster muss [Chaos Mesh bereitgestellt sein.](chaos-studio-tutorial-aks-portal.md) |
| URN | urn:csci:microsoft:azureKubernetesServiceChaosMesh:IOChaos/2.1 |
| Parameter (Schlüssel, Wert) |  |
| jsonSpec | Eine JSON-formatierte Chaos Mesh-Spezifikation, die die [IOChaos-Art](https://chaos-mesh.org/docs/simulate-io-chaos-on-kubernetes/#create-experiments-using-the-yaml-files)verwendet, wenn sie über eine ARM-Vorlage, eine REST-API oder Azure CLI erstellt wurde. Sie können einen [YAML-zu-JSON-Konverter wie diesen](https://www.convertjson.com/yaml-to-json.htm) verwenden, um das Chaos Mesh YAML in JSON zu konvertieren und zu minimieren, und ein [JSON-String-Escape-Tool wie dieses](https://www.freeformatter.com/json-escape.html) verwenden, um die JSON-Spezifikation zu entschlüsseln. Fügen Sie nur die YAML unter der Eigenschaft "jsonSpec" ein (keine Metadaten, Art, etc.). |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:IOChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"latency\",\"mode\":\"one\",\"selector\":{\"labelSelectors\":{\"app\":\"etcd\"}},\"volumePath\":\"\/var\/run\/etcd\",\"path\":\"\/var\/run\/etcd\/**\/*\",\"delay\":\"100ms\",\"percent\":50,\"duration\":\"400s\"}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-time-faults"></a>AKS Chaos Mesh Zeitfehler

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | TimeChaos-2.1 |
| Zieltyp | Microsoft-AzureKubernetesServiceChaosMesh |
| Beschreibung | Verursacht eine Änderung der Systemuhr auf Ihrem AKS-Cluster mit [Chaos Mesh](https://chaos-mesh.org/docs/simulate-time-chaos-on-kubernetes/). Nützlich für die Wiederherstellung von AKS-Vorfällen, die sich aus verteilten Systemen ergeben, die nicht mehr synchronisiert sind, aus fehlender/falscher Schaltjahr-/Schaltsekunden-Logik und mehr. |
| Voraussetzungen | Für den AKS-Cluster muss [Chaos Mesh bereitgestellt sein.](chaos-studio-tutorial-aks-portal.md) |
| URN | urn:csci:microsoft:azureKubernetesServiceChaosMesh:timeChaos/2.1 |
| Parameter (Schlüssel, Wert) |  |
| jsonSpec | Eine JSON-formatierte Chaos Mesh-Spezifikation, die die [TimeChaos-Art](https://chaos-mesh.org/docs/simulate-time-chaos-on-kubernetes/#create-experiments-using-the-yaml-file)verwendet, wenn sie über eine ARM-Vorlage, eine REST-API oder Azure CLI erstellt wurde. Sie können einen [YAML-zu-JSON-Konverter wie diesen](https://www.convertjson.com/yaml-to-json.htm) verwenden, um das Chaos Mesh YAML in JSON zu konvertieren und zu minimieren, und ein [JSON-String-Escape-Tool wie dieses](https://www.freeformatter.com/json-escape.html) verwenden, um die JSON-Spezifikation zu entschlüsseln. Fügen Sie nur die YAML unter der Eigenschaft "jsonSpec" ein (keine Metadaten, Art, etc.). |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:timeChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"one\",\"selector\":{\"labelSelectors\":{\"app\":\"app1\"}},\"timeOffset\":\"-10m100ns\"}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-kernel-faults"></a>AKS Chaos Mesh-Kernelfehler

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | KernelChaos-2.1 |
| Zieltyp | Microsoft-AzureKubernetesServiceChaosMesh |
| Beschreibung | Bewirkt, dass ein über [Chaos Mesh](https://chaos-mesh.org/docs/simulate-kernel-chaos-on-kubernetes/) verfügbarer Kernel-Fehler gegen Ihren AKS-Cluster läuft. Nützlich für die Wiederherstellung von AKS-Vorfällen aufgrund von Fehlern auf Linux-Kernel-Ebene, z. B. wenn ein Mount fehlschlägt oder kein Speicher zugewiesen wird. |
| Voraussetzungen | Für den AKS-Cluster muss [Chaos Mesh bereitgestellt sein.](chaos-studio-tutorial-aks-portal.md) |
| URN | urn:csci:microsoft:azureKubernetesServiceChaosMesh:kernelChaos/2.1 |
| Parameter (Schlüssel, Wert) |  |
| jsonSpec | Eine JSON-formatierte Chaos Mesh-Spezifikation, die die [KernelChaos-Art](https://chaos-mesh.org/docs/simulate-kernel-chaos-on-kubernetes/#configuration-file)verwendet, wenn sie über eine ARM-Vorlage, eine REST-API oder Azure CLI erstellt wurde. Sie können einen [YAML-zu-JSON-Konverter wie diesen](https://www.convertjson.com/yaml-to-json.htm) verwenden, um das Chaos Mesh YAML in JSON zu konvertieren und zu minimieren, und ein [JSON-String-Escape-Tool wie dieses](https://www.freeformatter.com/json-escape.html) verwenden, um die JSON-Spezifikation zu entschlüsseln. Fügen Sie nur die YAML unter der Eigenschaft "jsonSpec" ein (keine Metadaten, Art, etc.). |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:kernelChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"one\",\"selector\":{\"namespaces\":[\"chaos-mount\"]},\"failKernRequest\":{\"callchain\":[{\"funcname\":\"__x64_sys_mount\"}],\"failtype\":0}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-http-faults"></a>HTTP-Fehler in AKS Chaos Mesh

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | HTTPChaos-2.1 |
| Zieltyp | Microsoft-AzureKubernetesServiceChaosMesh |
| BESCHREIBUNG | Bewirkt, dass ein über [Chaos Mesh](https://chaos-mesh.org/docs/simulate-http-chaos-on-kubernetes/) verfügbarer HTTP-Fehler für Ihren AKS-Cluster ausgeführt wird. Nützlich für die Neuerstellung von Vorfällen aufgrund von Http-Anforderungs- und Antwortverarbeitungsfehlern, z. B. verzögerte oder falsche Antworten. |
| Voraussetzungen | Für den AKS-Cluster muss [Chaos Mesh bereitgestellt sein.](chaos-studio-tutorial-aks-portal.md) |
| URN | urn:csci:microsoft:azureKubernetesServiceChaosMesh:httpChaos/2.1 |
| Parameter (Schlüssel, Wert) |  |
| jsonSpec | Eine JSON-formatierte Chaos Mesh-Spezifikation, die die [HTTPChaos-Art](https://chaos-mesh.org/docs/simulate-http-chaos-on-kubernetes/#create-experiments)verwendet, wenn sie über eine ARM-Vorlage, eine REST-API oder Azure CLI erstellt wurde. Sie können einen [YAML-zu-JSON-Konverter wie diesen](https://www.convertjson.com/yaml-to-json.htm) verwenden, um das Chaos Mesh YAML in JSON zu konvertieren und zu minimieren, und ein [JSON-String-Escape-Tool wie dieses](https://www.freeformatter.com/json-escape.html) verwenden, um die JSON-Spezifikation zu entschlüsseln. Fügen Sie nur die YAML unter der Eigenschaft "jsonSpec" ein (keine Metadaten, Art, etc.). |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:httpChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"mode\":\"all\",\"selector\":{\"labelSelectors\":{\"app\":\"nginx\"}},\"target\":\"Request\",\"port\":80,\"method\":\"GET\",\"path\":\"\/api\",\"abort\":true,\"duration\":\"5m\",\"scheduler\":{\"cron\":\"@every 10m\"}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="aks-chaos-mesh-dns-faults"></a>AKS Chaos Mesh-DNS-Fehler

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | DNSChaos-2.1 |
| Zieltyp | Microsoft-AzureKubernetesServiceChaosMesh |
| Beschreibung | Bewirkt, dass ein über [Chaos Mesh](https://chaos-mesh.org/docs/simulate-dns-chaos-on-kubernetes/) verfügbarer DNS-Fehler für Ihren AKS-Cluster ausgeführt wird. Nützlich für die Neuerfüllung von Vorfällen aufgrund von DNS-Fehlern. |
| Voraussetzungen | Im AKS-Cluster muss [Chaos Mesh bereitgestellt](chaos-studio-tutorial-aks-portal.md) sein, und der [DNS-Dienst muss installiert sein.](https://chaos-mesh.org/docs/simulate-dns-chaos-on-kubernetes/#deploy-chaos-dns-service) |
| URN | urn:csci:microsoft:azureKubernetesServiceChaosMesh:dnsChaos/2.1 |
| Parameter (Schlüssel, Wert) |  |
| jsonSpec | Eine JSON-formatierte Chaos Mesh-Spezifikation, die die [DNSChaos-Art](https://chaos-mesh.org/docs/simulate-dns-chaos-on-kubernetes/#create-experiments-using-the-yaml-file)verwendet, wenn sie über eine ARM-Vorlage, eine REST-API oder Azure CLI erstellt wurde. Sie können einen [YAML-zu-JSON-Konverter wie diesen](https://www.convertjson.com/yaml-to-json.htm) verwenden, um das Chaos Mesh YAML in JSON zu konvertieren und zu minimieren, und ein [JSON-String-Escape-Tool wie dieses](https://www.freeformatter.com/json-escape.html) verwenden, um die JSON-Spezifikation zu entschlüsseln. Fügen Sie nur die YAML unter der Eigenschaft "jsonSpec" ein (keine Metadaten, Art, etc.). |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "continuous",
      "name": "urn:csci:microsoft:azureKubernetesServiceChaosMesh:dnsChaos/2.1",
      "parameters": [
        {
            "key": "jsonSpec",
            "value": "{\"action\":\"random\",\"mode\":\"all\",\"patterns\":[\"google.com\",\"chaos-mesh.*\",\"github.?om\"],\"selector\":{\"namespaces\":[\"busybox\"]}}"
        }
    ],
      "duration": "PT10M",
      "selectorid": "myResources"
    }
  ]
}
```

## <a name="network-security-group-set-rules"></a>Netzwerksicherheitsgruppe (Regeln festlegen)

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | SicherheitsRegel-1.0 |
| Zieltyp | Microsoft-NetworkSecurityGroup |
| Beschreibung | Ermöglicht die Bearbeitung oder Erstellung einer Regel in einer bestehenden Azure Network Security Group oder einem Satz von Azure Network Security Groups (vorausgesetzt, die Regeldefinition ist sicherheitsgruppenübergreifend anwendbar). Nützlich für die Simulation eines Ausfalls einer nachgelagerten oder regionsübergreifenden Abhängigkeit/Nicht-Abhängigkeit, die Simulation eines Ereignisses, von dem erwartet wird, dass es eine Logik zur Erzwingung eines Dienst-Failover auslöst, die Simulation eines Ereignisses, von dem erwartet wird, dass es eine Aktion eines Überwachungs- oder Zustandsverwaltungsdienstes auslöst, oder als Alternative für die Blockierung oder Zulassung von Netzverkehr, wenn Chaos Agent nicht eingesetzt werden kann. |
| Voraussetzungen | Keine |
| URN | urn:csci:microsoft:networkSecurityGroup:securityRule/1.0 |
| Parameter (Schlüssel, Wert) |  |
| name | Ein eindeutiger Name für die Sicherheitsregel, die erstellt werden soll. Der Fehler schlägt fehl, wenn in der NSG bereits eine andere Regel mit demselben Namen existiert. Muss mit einem Buchstaben oder einer Zahl beginnen, mit einem Buchstaben, einer Zahl oder einem Unterstrich enden und darf nur Buchstaben, Zahlen, Unterstriche, Punkte oder Bindestriche enthalten. |
| Protokoll | Protokoll für die Sicherheitsregel. Muss Any, TCP, UDP oder ICMP sein. |
| sourceAddresses | Eine Zeichenkette, die ein json-begrenztes Array von CIDR-formatierten IP-Adressen darstellt. Kann auch ein Service-Tag-Name für eine eingehende Regel sein, z. B. "AppService". Das Sternchen '*' kann auch verwendet werden, um alle Quell-IPs abzugleichen. |
| destinationAddresses | Eine Zeichenkette, die ein json-begrenztes Array von CIDR-formatierten IP-Adressen darstellt. Kann auch ein Service-Tag-Name für eine ausgehende Regel sein, z. B. "AppService". Das Sternchen '*' kann auch verwendet werden, um alle Ziel-IPs abzugleichen. |
| action | Zugriffsart der Sicherheitsgruppe. Muss entweder Zulassen oder Verweigern sein |
| destinationPortRanges | Eine Zeichenkette, die ein json-begrenztes Array von einzelnen Ports und/oder Portbereichen darstellt, z. B. 80 oder 1024-65535. |
| sourcePortRanges | Eine Zeichenkette, die ein json-begrenztes Array einzelner Ports und/oder Portbereiche darstellt, z. B. 80 oder 1024-65535. |
| priority | Ein Wert zwischen 100 und 4096, der für alle Sicherheitsregeln innerhalb der Netzwerksicherheitsgruppe eindeutig ist. Der Fehler schlägt fehl, wenn bereits eine andere Regel mit der gleichen Priorität in der NSG existiert. |
| direction | Richtung des von der Sicherheitsregel beeinflussten Datenverkehrs. Muss entweder Inbound oder Outbound sein. |

### <a name="sample-json"></a>Beispiel-JSON

```json
{ 
  "name": "branchOne", 
  "actions": [ 
    { 
      "type": "continuous", 
      "name": "urn:csci:microsoft:networkSecurityGroup:securityRule/1.0", 
      "parameters": [ 
          { 
              "key": "name", 
              "value": "Block_SingleHost_to_Networks" 

          }, 
          { 
              "key": "protocol", 
              "value": "Any" 
          }, 
          { 
              "key": "sourceAddresses", 
              "value": "[\"10.1.1.128/32\"]"
          }, 
          { 
              "key": "destinationAddresses", 
              "value": "[\"10.20.0.0/16\",\"10.30.0.0/16\"]"
          }, 
          { 
              "key": "access", 
              "value": "Deny" 
          }, 
          { 
              "key": "destinationPortRanges", 
              "value": "[\"80-8080\"]"
          }, 
          { 
              "key": "sourcePortRanges", 
              "value": "[\"*\"]"
          }, 
          { 
              "key": "priority", 
              "value": "100" 
          }, 
          { 
              "key": "direction", 
              "value": "Outbound" 
          } 
      ], 
      "duration": "PT10M", 
      "selectorid": "myResources" 
    } 
  ] 
} 
```

### <a name="limitations"></a>Einschränkungen

* Der Fehler kann nur auf eine bestehende Netzwerksicherheitsgruppe angewendet werden.
* Wenn eine NSG-Regel zur Verweigerung des Datenverkehrs angewandt wird, werden bestehende Verbindungen erst dann unterbrochen, wenn sie 4 Minuten lang **leer** waren. Eine Abhilfe besteht darin, im selben Schritt einen weiteren Zweig hinzuzufügen, der einen Fehler verwendet, der bei Anwendung des NSG-Fehlers zum Abbruch bestehender Verbindungen führen würde. So würde beispielsweise das Beenden des Prozesses, das vorübergehende Anhalten des Dienstes oder der Neustart der VM dazu führen, dass die Verbindungen zurückgesetzt werden.
* Die Regeln werden zu Beginn der Aktion angewendet. Jede externe Änderung der Regel während der Dauer der Aktion führt zum Scheitern des Experiments.
* Das Erstellen oder Ändern von Regeln für Anwendungssicherheitsgruppen wird nicht unterstützt.
* Die Prioritätswerte müssen für jeden anvisierten NSG eindeutig sein. Der Versuch, eine neue Regel zu erstellen, die denselben Prioritätswert wie eine andere hat, führt zum Scheitern des Experiments.

## <a name="azure-cache-for-redis-reboot"></a>Azure Cache für Redis Neustart

| Eigenschaft | Wert |
|-|-|
| Name der Fähigkeit | Reboot-1.0 |
| Zieltyp | Microsoft-AzureClusteredCacheForRedis |
| BESCHREIBUNG | Veranlasst einen erzwungenen Neustart des Ziels, um einen kurzen Ausfall zu simulieren. |
| Voraussetzungen | Keine |
| URN | urn:csci:microsoft:azureClusteredCacheForRedis:reboot/1.0 |
| Fehlertyp | Discrete |
| Parameter (Schlüssel, Wert) |  |
| rebootType | Die Knotentypen, an denen die Neustartaktion durchgeführt werden soll, können als PrimaryNode, SecondaryNode oder AllNodes angegeben werden.  |
| shardid | Die ID des Shards, der neu gebootet werden soll.  |

### <a name="sample-json"></a>Beispiel-JSON

```json
{
  "name": "branchOne",
  "actions": [
    {
      "type": "discrete",
      "name": "urn:csci:microsoft:azureClusteredCacheForRedis:reboot/1.0",
      "parameters": [
        {
          "key": "RebootType",
          "value": "AllNodes"
        },
        {
          "key": "ShardId",
          "value": "0"
        }
      ],
      "selectorid": "myResources"
    }
  ]
}
```

### <a name="limitations"></a>Einschränkungen

* Der Reboot-Fehler führt zu einem erzwungenen Neustart, um einen Ausfall besser zu simulieren, was bedeutet, dass es zu einem Datenverlust kommen kann.
* Der Neustartfehler ist ein **diskreter** Fehlertyp. Im Gegensatz zu Dauerfehlern handelt es sich um eine einmalige Aktion, die daher nicht von Dauer ist.
