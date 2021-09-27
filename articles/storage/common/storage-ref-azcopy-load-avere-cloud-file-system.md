---
title: azcopy load clfs | Microsoft-Dokumentation
titleSuffix: Azure Storage
description: Dieser Artikel enthält Referenzinformationen zum Befehl „azcopy load clfs“.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: b678b2830ede076169f82c4a3280845edd7db90b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128624916"
---
# <a name="azcopy-load-clfs"></a>azcopy load clfs

Überträgt lokale Daten in einen Container und speichert sie im Avere-CLFS-Format (Cloud File System) von Microsoft.

## <a name="synopsis"></a>Zusammenfassung

Der Befehl „load“ kopiert Daten in Azure Blob Storage-Container und speichert diese Daten im Avere-CLFS-Format (Cloud File System) von Microsoft. Das proprietäre CLFS-Format wird von den Azure HPC Cache- und Avere vFXT for Azure-Produkten verwendet.

Um diesen Befehl zu nutzen, installieren Sie die erforderliche Erweiterung über: pip3 install clfsload~=1.0.23. Stellen Sie sicher, dass sich „CLFSLoad.py“ in Ihrem Pfad befindet. Weitere Informationen zu diesem Schritt finden Sie unter [https://aka.ms/azcopy/clfs](https://aka.ms/azcopy/clfs).

Dieser Befehl stellt eine einfache Option zum Verschieben vorhandener Daten in den Cloudspeicher dar, wo sie mit bestimmten Microsoft-HPC-Produkten (High Performance Computing) verwendet werden können.

Da diese Produkte ein proprietäres Format als Clouddateisystem für die Datenverwaltung verwenden, können diese Daten nicht über den nativen Kopierbefehl geladen werden.

Stattdessen müssen die Daten über das Cacheprodukt selbst oder über diesen load-Befehl geladen werden, der das korrekte proprietäre Format verwendet.
Mit diesem Befehl können Sie Daten übertragen, ohne den Cache zu verwenden, um beispielsweise Speicher vorab aufzufüllen oder Dateien einem Workingset hinzuzufügen, ohne die Cacheauslastung zu erhöhen.

Das Ziel ist ein leerer Azure Storage-Container. Nach Abschluss der Übertragung kann der Zielcontainer mit einer Azure HPC Cache-Instanz oder einem Avere vFXT for Azure-Cluster verwendet werden.

> [!NOTE]
> Dies ist eine Vorschauversion des load-Befehls. Melden Sie bitte mögliche Probleme über das GitHub-Repository von AzCopy.

```
azcopy load clfs [local dir] [container URL] [flags]
```

## <a name="related-conceptual-articles"></a>Verwandte konzeptionelle Artikel

- [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md)
- [Übertragen von Daten mit AzCopy und Blobspeicher](./storage-use-azcopy-v10.md#transfer-data)
- [Übertragen von Daten mit AzCopy und Dateispeicher](storage-use-azcopy-files.md)

## <a name="examples"></a>Beispiele

Laden eines vollständigen Verzeichnisses in einen Container mit einer SAS im CLFS-Format:

```azcopy
azcopy load clfs "/path/to/dir" "https://[account].blob.core.windows.net/[container]?[SAS]" --state-path="/path/to/state/path"
```

## <a name="options"></a>Tastatur

**--compression-type** string: gibt den für die Übertragungen zu verwendenden Komprimierungstyp an. Verfügbare Werte: `DISABLED`, `LZ4`. (Standardwert: `LZ4`)

**--help:** Hilfe zum Befehl `azcopy load clfs`.

**--log-level** string: definiert, wie ausführlich die Protokolldatei sein soll. Verfügbare Stufen: `DEBUG`, `INFO`, `WARNING`, `ERROR`. (Standardwert: `INFO`)

**--max-errors** uint32: gibt die maximale Anzahl zu tolerierender Übertragungsfehler an. Wenn genügend Fehler auftreten, wird der Auftrag sofort beendet.

**--new-session:** startet einen neuen Auftrag, anstatt einen vorhandenen Auftrag fortzusetzen, dessen Verfolgungsinformationen im `--state-path` aufbewahrt werden. (Standardwert: „true“)

**--preserve-hardlinks:** behält Beziehungen fester Links bei.

**--state-path** string: erforderlicher Pfad zu einem lokalen Verzeichnis für die Nachverfolgung des Auftragszustands. Der Pfad muss auf ein vorhandenes Verzeichnis verweisen, damit ein Auftrag fortgesetzt werden kann. Für einen neuen Auftrag muss er leer sein.

## <a name="options-inherited-from-parent-commands"></a>Von übergeordneten Befehlen geerbte Optionen

|Option|BESCHREIBUNG|
|---|---|
|--cap-mbps float|Begrenzt die Übertragungsrate (in Megabit pro Sekunde). Der Schritt-für-Schritt-Durchsatz kann von der Obergrenze geringfügig abweichen. Wenn diese Option auf „null“ festgelegt oder weggelassen wird, ist der Durchsatz nicht begrenzt.|
|–output-type string|Format der Befehlsausgabe. Folgende Optionen sind verfügbar: „text“ und „json“. Der Standardwert lautet „text“.|
|--trusted-microsoft-suffixes-Zeichenfolge   | Gibt zusätzliche Domänensuffixe an, an die Azure Active Directory-Anmeldetoken gesendet werden können.  Der Standardwert ist ' *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net'. Alle hier aufgelisteten Werte werden zum Standardwert hinzugefügt. Aus Sicherheitsgründen sollten Sie hier nur Microsoft Azure-Domänen platzieren. Trennen Sie mehrere E-Mail-Adressen durch Semikolons voneinander.|

## <a name="see-also"></a>Weitere Informationen

- [azcopy](storage-ref-azcopy.md)
