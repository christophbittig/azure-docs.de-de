---
title: Ermitteln von Fehlern und Fortsetzen von Aufträgen mithilfe von Protokollen in AzCopy (Azure Storage) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie anhand von Protokollen Fehler diagnostizieren und angehaltene Aufträge mithilfe von Plandateien fortsetzen.
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 04/02/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: 8fc9934c5d524550929c3400af9f257c4cbcccc8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665908"
---
# <a name="find-errors-and-resume-jobs-by-using-log-and-plan-files-in-azcopy"></a>Ermitteln von Fehlern und Fortsetzen von Aufträgen mithilfe von Protokoll- und Plandateien in AzCopy

AzCopy ist ein Befehlszeilenhilfsprogramm, das Sie verwenden können, um Blobs oder Dateien in oder aus einem Speicherkonto zu kopieren. In diesem Artikel erfahren Sie, wie Sie mithilfe von Protokollen Fehler diagnostizieren und dann Plandateien verwenden, um Aufträge fortzusetzen. Zudem wird in diesem Artikel gezeigt, wie Sie Protokoll- und Plandateien konfigurieren, indem Sie deren Ausführlichkeitsgrad und Standardspeicherort ändern.

> [!NOTE]
> Inhalte, die Sie beim Einstieg in AzCopy unterstützen, finden Sie unter [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md). Dieser Artikel gilt für AzCopy **V10**, da diese Version von AzCopy derzeit unterstützt wird. Wenn Sie eine vorherige Version von AzCopy verwenden müssen, finden Sie Informationen hierzu unter [Verwenden der vorherige Version von AzCopy](storage-use-azcopy-v10.md#previous-version).

## <a name="log-and-plan-files"></a>Protokoll- und Plandateien

AzCopy erstellt für jeden Auftrag *Protokoll*- und *Plandateien*. Sie können diese Protokolle verwenden, um potenzielle Probleme zu untersuchen und zu behandeln.

Die Protokolle enthalten den Status des Fehlers (`UPLOADFAILED`, `COPYFAILED` und `DOWNLOADFAILED`), den vollständigen Pfad und die Ursache des Fehlers.

Standardmäßig befinden sich die Protokoll- und Plandateien im Verzeichnis `%USERPROFILE%\.azcopy` (Windows) oder `$HOME$\.azcopy` (Mac und Linux). Dieser Speicherort kann allerdings geändert werden.

Der relevante Fehler ist nicht zwingend der erste Fehler in der Datei. Bei Netzwerkfehlern, Timeouts, Serverauslastungsfehlern und Ähnlichem wird der Vorgang von AzCopy bis zu 20-mal wiederholt, was in der Regel zum Erfolg führt.  Der erste angezeigte Fehler ist unter Umständen ein harmloser Vorgang, der erfolgreich wiederholt wurde.  Sehen Sie sich daher nicht den ersten Fehler in der Datei an, sondern die Fehler in der Nähe von `UPLOADFAILED`, `COPYFAILED` oder `DOWNLOADFAILED`.

> [!IMPORTANT]
> Geben Sie beim Senden einer Anforderung an den Microsoft-Support (oder bei der Problembehandlung unter Einbeziehung eines Drittanbieters) die bearbeitete Version des Befehls an, den Sie ausführen möchten. So wird sichergestellt, dass die SAS nicht versehentlich offengelegt wird. Die editierte Version steht am Anfang der Protokolldatei.

## <a name="review-the-logs-for-errors"></a>Überprüfen der Protokolle auf Fehler

Der folgende Befehl ruft alle Fehler mit dem Status `UPLOADFAILED` aus dem Protokoll `04dc9ca9-158f-7945-5933-564021086c79` ab:

**Windows (PowerShell)**

```
Select-String UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

**Linux**

```
grep UPLOADFAILED .\04dc9ca9-158f-7945-5933-564021086c79.log
```

## <a name="view-and-resume-jobs"></a>Anzeigen und Fortsetzen von Aufträgen

Jeder Übertragungsvorgang erstellt einen AzCopy-Auftrag. Verwenden Sie den folgenden Befehl, um den Verlauf von Aufträgen anzuzeigen:

```
azcopy jobs list
```

Verwenden Sie den folgenden Befehl, um die Auftragsstatistiken anzuzeigen:

```
azcopy jobs show <job-id>
```

Um die Übertragungen nach ihrem Status zu filtern, verwenden Sie den folgenden Befehl:

```
azcopy jobs show <job-id> --with-status=Failed
```

Verwenden Sie den folgenden Befehl, um einen fehlgeschlagenen bzw. abgebrochenen Auftrag fortzusetzen. Dieser Befehl verwendet seinen Bezeichner zusammen mit dem SAS-Token, da er aus Sicherheitsgründen nicht persistent ist:

```
azcopy jobs resume <job-id> --source-sas="<sas-token>" --destination-sas="<sas-token>"
```

> [!TIP]
> Schließen Sie Pfadargumente wie das SAS-Token in einfache Anführungszeichen ('') ein. Verwenden Sie in allen Befehlsshells außer der Windows-Befehlszeile (cmd.exe) einfache Anführungszeichen. Wenn Sie eine Windows-Befehlszeile (cmd.exe) verwenden, müssen Sie Pfadargumente in doppelte Anführungszeichen ("") anstelle von einfachen Anführungszeichen ('') einschließen.

Wenn Sie einen Auftrag fortsetzen, überprüft AzCopy die Plandatei. In der Plandatei werden alle Dateien aufgelistet, die zur Verarbeitung identifiziert wurden, als der Auftrag erstmalig erstellt wurde. Wenn Sie einen Auftrag fortsetzen, versucht AzCopy, alle Dateien zu übertragen, die in der Plandatei aufgeführt sind, die nicht bereits übertragen wurden.

## <a name="change-the-location-of-plan-files"></a>Ändern des Speicherorts der Plandateien

Verwenden Sie einen der folgende Befehle.

| Betriebssystem | Get-Help  |
|--------|-----------|
| **Windows** | PowerShell:`$env:AZCOPY_JOB_PLAN_LOCATION="<value>"` <br> Verwenden Sie an einer Eingabeaufforderung: `set AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **Linux** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |
| **macOS** | `export AZCOPY_JOB_PLAN_LOCATION=<value>` |

Verwenden Sie `azcopy env`, um den aktuellen Wert dieser Variablen zu überprüfen. Wenn der Wert leer ist, werden Plandateien an den Standardspeicherort geschrieben.

## <a name="change-the-location-of-log-files"></a>Ändern des Speicherorts der Protokolldateien

Verwenden Sie einen der folgende Befehle.

| Betriebssystem | Get-Help  |
|--------|-----------|
| **Windows** | PowerShell:`$env:AZCOPY_LOG_LOCATION="<value>"` <br> Verwenden Sie an einer Eingabeaufforderung: `set AZCOPY_LOG_LOCATION=<value>`|
| **Linux** | `export AZCOPY_LOG_LOCATION=<value>` |
| **macOS** | `export AZCOPY_LOG_LOCATION=<value>` |

Verwenden Sie `azcopy env`, um den aktuellen Wert dieser Variablen zu überprüfen. Wenn der Wert leer ist, werden Protokolle an den Standardspeicherort geschrieben.

## <a name="change-the-default-log-level"></a>Ändern der Standardprotokollebene

Standardmäßig ist die AzCopy-Protokollebene auf `INFO` festgelegt. Wenn Sie die Ausführlichkeit des Protokolls verringern möchten, um Speicherplatz auf dem Datenträger zu sparen, überschreiben Sie die Einstellung mithilfe der Option ``--log-level``.

Verfügbare Protokolliergrade sind: `NONE`, `DEBUG`, `INFO`, `WARNING`, `ERROR`, `PANIC` und `FATAL`.

## <a name="remove-plan-and-log-files"></a>Entfernen von Plan- und Protokolldateien

Wenn Sie alle Plan- und Protokolldateien von Ihrem lokalen Computer entfernen möchten, um Speicherplatz zu sparen, verwenden Sie den Befehl `azcopy jobs clean`.

Verwenden Sie `azcopy jobs rm <job-id>`, um die Plan- und Protokolldateien zu entfernen, die nur einem Auftrag zugeordnet sind. Ersetzen Sie den Platzhalter `<job-id>` in diesem Beispiel durch die Auftrags-ID des Auftrags.

## <a name="see-also"></a>Weitere Informationen

- [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md)
