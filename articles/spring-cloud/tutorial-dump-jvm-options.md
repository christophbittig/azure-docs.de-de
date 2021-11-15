---
title: Diagnoseeinstellungen von JVM-Optionen für erweiterte Problembehandlung in Azure Spring Cloud
description: Mehrere bewährte Methoden für die JVM-Konfiguration zum Festlegen von Heapdump-, JFR- und GC-Protokollen.
author: YinglueZhang-MS
ms.author: yinglzh
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/31/2021
ms.custom: devx-track-java
ms.openlocfilehash: fd29b9b7356bc16132d46f125d3f3cf2792c1654
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000601"
---
# <a name="tutorial-diagnostic-settings-of-jvm-options-for-advanced-troubleshooting-in-azure-spring-cloud"></a>Tutorial: Diagnoseeinstellungen von JVM-Optionen für erweiterte Problembehandlung in Azure Spring Cloud

**Dieser Artikel gilt für:** ✔️ Java ❌ C#

Es gibt mehrere JVM-basierte Anwendungsstartparameter im Zusammenhang mit Heapdump-, JFR- und GC-Protokollen. In Azure Spring Cloud wird die Konfiguration von JVM mithilfe von jvm-options unterstützt. In diesem Artikel werden einige Beispiele dafür vorgestellt, die für unsere Kunden nützlich sein können.

## <a name="prerequisites"></a>Voraussetzungen
Für diese Übung benötigen Sie Folgendes:

* Eine bereitgestellte Azure Spring Cloud-Dienstinstanz. Informationen zu den ersten Schritten finden Sie unter [Schnellstart: Starten einer Java Spring-Anwendung mit der Azure CLI](./quickstart.md).
* Mindestens eine bereits in Ihrer Dienstinstanz erstellte Anwendung.
* Mindestens einen [persistenten Speicher, der bereits an Ihre App gebunden ist](how-to-built-in-persistent-storage.md), um generierte Diagnosedateien zu speichern.

Benutzer können das [Bereitstellungsdokument](https://docs.microsoft.com/cli/azure/spring-cloud/app/deployment?view=azure-cli-latest) befolgen, um JVM-basierte Anwendungsstartparameter zu konfigurieren. Im folgenden Abschnitt werden mehrere Beispiele vorgestellt. **Sie müssen den Parameter in jvm-options hinzufügen.**

## <a name="generate-a-heap-dump-when-out-of-memory"></a>Generieren eines Heapdumps, wenn nicht genügend Arbeitsspeicher vorhanden ist
```heap dump when OOM
   -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=<PATH_TO_HEAP_DUMP_FOLDER> 
```
Sie können auch den spezifischen Namen der Datei angeben. Es wird jedoch dringend empfohlen, nur den Ordnerpfad anzugeben. Sobald der Dateiname angegeben wurde, wird nur im ersten OOM ein Heapdump generiert. Da die hprof-format-Datei nicht abgedeckt werden kann, können Benutzer keinen Heapdump des folgenden OOM abrufen. Wenn Benutzer nur den Pfad zum Ordner angeben, erhalten Benutzer Heapdumps aller OOMs mit einem automatisch generierten Namen.

Der gesamte unten angegebene Pfadbenutzer sollte sich unter dem Einbindungspfad des persistenten Speichers des Benutzers befinden, der bereits an die App des Kunden gebunden ist.

## <a name="gc-logs"></a>GC-Protokolle
Wir geben nur ein Beispiel für die Verwendung von JVM-Optionen im Zusammenhang mit GC-Protokollen. Weitere Informationen finden Sie in der offiziellen JVM-Dokumentation.
```GC Logs
   -XX:+PrintGCDetails -Xloggc:<PATH_TO_GC_LOG_FILE>
```

## <a name="jfr-on-exit"></a>JFR beim Beenden
Wir geben nur ein Beispiel für die Verwendung von JVM-Optionen im Zusammenhang mit JFR. Weitere Informationen finden Sie in der offiziellen JVM-Dokumentation.
```JFR on exit
   -XX:StartFlightRecording=dumponexit=true,dumponexitpath=<PATH_TO_JFR_FILE>
```

## <a name="path-of-generated-file"></a>Pfad der generierten Datei.
Achten Sie darauf, dass Sie sicherstellen sollten, dass sich der Zielpfad Ihrer generierten Datei in Ihrem persistenten Speicher für Ihre App befindet. Damit Sie die Datei in Ihrem Speicher abrufen können.
Kunden geben z. B. eine JSON-Datei an, wenn Benutzer zum ersten Mal persistenten Speicher in Azure Spring Cloud erstellen.
```json
    {
       "customPersistentDisks": [
          {
              "storageName": "<storage-resource-name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<azure-file-share-name>",
                  "mountPath": "<unique-mount-path e.g. /test/Path>",
                  "mountOptions": [
                      "uid=0",
                      "gid=0"
                   ],
                   "readOnly": false 
                }
          },
          {
              "storageName": "<storage-resource-name>",
              "customPersistentDiskProperties": {
                  "type": "AzureFileVolume",
                  "shareName": "<azure-file-share-name>",
                  "mountPath": "<unique-mount-path e.g. /test/anotherPath>",
                  "readOnly": true
              }
          }
       ]
    }
```
Der Kunde kann auch die folgenden Befehle verwenden, um den persistenten Speicher anzufügen.
    
```
   az spring-cloud app append-persistent-storage --persistent-storage-type AzureFileVolume --share-name <azure-file-share-name> --mount-path <unique-mount-path e.g. /test/Path> --storage-name <storage-resource-name> -n <app-name> -g <resource-group-name> -s <spring-instance-name>
```
   
   Wenn Kunden das Speicherabbild unter ihrer eigenen oben angegebenen Azure-Datei speichern möchten, geben Sie <PATH_TO_HEAP_DUMP_FOLDER> als „/test/Path“ an. Wenn Kunden den Pfad unter dem Einbindungspfad verwenden möchten, stellen Sie sicher, dass der Unterpfad bereits erstellt wurde.




