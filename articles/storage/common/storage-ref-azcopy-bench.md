---
title: azcopy bench | Microsoft-Dokumentation
description: Dieser Artikel enthält Referenzinformationen zum Befehl „azcopy bench“.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 6bd80367ab407be3d6b43750c6525b2d2ae30200
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605865"
---
# <a name="azcopy-benchmark"></a>azcopy bench

Führt einen Leistungsvergleichstest aus, indem Testdaten zu oder von einem bestimmten Ziel hoch- oder heruntergeladen werden. Für Uploads werden die Testdaten automatisch generiert.

Mit dem Befehl „bench“ wird derselbe Vorgang wie mit „copy“ ausgeführt. Allerdings bestehen folgende Unterschiede:

  - Anstatt sowohl Quell- als auch Zielparameter zu erfordern, benötigt der Vergleichstest nur einen Parameter. Dies ist der Blobcontainer, die Azure Files-Freigabe oder das Azure Data Lake Storage Gen2-Dateisystem, in das Sie hoch- oder herunterladen möchten.

  - Der Parameter „mode“ beschreibt, ob AzCopy Uploads oder Downloads für ein bestimmtes Ziel testen soll. Gültige Werte sind „Upload“ und „Download“. Standardwert ist „Upload“.

  - Bei Vergleichstests für Uploads wird die Nutzlast durch Befehlszeilenparameter beschrieben, die steuern, wie viele Dateien automatisch generiert werden und wie signifikant die Dateien sind. Die Dateien werden vollständig im Arbeitsspeicher generiert. Es wird kein Datenträger verwendet.

  - Bei Downloads besteht die Nutzlast aus den Dateien, die bereits an der Quelle vorhanden sind. (Informationen zum Generieren von Testdateien finden Sie bei Bedarf im nachfolgenden Beispiel).

  - Es werden nur einige der optionalen Parameter unterstützt, die für den Befehl „copy“ verfügbar sind.

  - Es werden zusätzliche Diagnosen ausgeführt und ausgegeben.

  - Für Uploads ist das Standardverhalten, die übertragenen Daten am Ende der Testausführung zu löschen.  Für Downloads werden die Daten nie lokal gespeichert.

Der Vergleichstestmodus wird automatisch auf die Anzahl der parallelen TCP-Verbindungen abgestimmt, die maximalen Durchsatz gewährleisten. Die Anzahl wird am Ende angezeigt. Um die automatische Abstimmung zu verhindern, legen Sie die Umgebungsvariable AZCOPY_CONCURRENCY_VALUE auf eine bestimmte Anzahl von Verbindungen fest.

Alle üblichen Authentifizierungstypen werden unterstützt. Der einfachste Ansatz für den Vergleichstest von Uploads besteht normalerweise jedoch darin, einen leeren Container mit einem SAS-Token zu erstellen und die SAS-Authentifizierung zu verwenden. (Der Modus „Download“ erfordert, dass ein Satz von Testdaten im Zielcontainer vorhanden ist.)

## <a name="related-conceptual-articles"></a>Verwandte konzeptionelle Artikel

- [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md)
- [Optimieren der Leistung von AzCopy v10 mit Azure Storage](storage-use-azcopy-optimize.md)

## <a name="examples"></a>Beispiele

```azcopy
azcopy benchmark [destination] [flags]
```

Ausführen eines Vergleichstests mit Standardparametern (geeignet für das Testen von Netzwerken mit bis zu 1 GBit/s):

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"
```

Ausführen eines Vergleichstests, bei dem 100 Dateien von jeweils 2 GiB hochgeladen werden (geeignet für das Testen von schnellen Netzwerken, z. B. mit 10 GBit/s):

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>"--file-count 100 --size-per-file 2G
```

Ausführen eines Vergleichstests, allerdings werden 50.000 Dateien von jeweils 8 MiB verwendet und deren MD5-Hashes berechnet. (Funktioniert auf dieselbe Weise wie mit dem `--put-md5`-Flag im Befehl „copy“.) Mit `--put-md5` soll bei Vergleichstests ermittelt werden, ob die MD5-Berechnung den Durchsatz der ausgewählten Dateianzahl und -größe beeinflusst:

```azcopy
azcopy bench --mode='Upload' "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5
```

Ausführen eines Vergleichstests, der vorhandene Dateien von einem Ziel herunterlädt

```azcopy
azcopy bench --mode='Download' "https://[account].blob.core.windows.net/[container]?<SAS?"
```

Ausführen eines Uploads, der die übertragenen Dateien nicht löscht (Diese Dateien können dann als Nutzlast für einen Downloadtest dienen.)

```azcopy
azcopy bench "https://[account].blob.core.windows.net/[container]?<SAS>" --file-count 100 --delete-test-data=false
```

## <a name="options"></a>Tastatur

**--blob-type** string  Definiert den Typ des Blobs am Ziel. Wird verwendet, um Vergleichstests für unterschiedliche Blobtypen zu ermöglichen. Identisch mit dem gleichnamigen Parameter im Befehl „copy“ (Standardwert: „Detect“).

**--block-size-mb** float  Verwendet diese Blockgröße (in MiB angegeben). Der Standardwert wird anhand der Dateigröße automatisch berechnet. Dezimalzahlen sind zulässig – Beispiel: 0,25 Identisch mit dem gleichnamigen Parameter im Befehl „copy“.

**--check-length**  Überprüft nach der Übertragung die Länge einer Datei am Ziel. Wenn die Quelle und das Ziel nicht übereinstimmen, wird die Übertragung als fehlerhaft gekennzeichnet. (Standardwert: „true“)

**--delete-test-data**  Falls „true“, werden die Vergleichsdaten am Ende des Vergleichstests gelöscht.  Legen Sie den Wert auf „false“ fest, wenn die Daten am Ziel erhalten bleiben sollen, beispielsweise für manuelle Tests außerhalb des Vergleichstestmodus (Standardwert: „true“).

**--file-count** uint.  Die Anzahl der zu verwendenden automatisch generierten Datendateien (Standard 100).

**--help**  Hilfe zu „bench“.

**--log-level** string  Definiert, wie ausführlich die Protokolldatei sein soll. Verfügbare Stufen: INFO (alle Anforderungen/Antworten), WARNING (langsame Antworten), ERROR (nur fehlgeschlagene Anforderungen) und NONE (keine Ausgabeprotokolle). (Standardwert: „Info“)

**--mode** string  Definiert, ob Azcopy Uploads oder Downloads von diesem Ziel testen soll. Gültige Werte sind „Upload“ und „Download“. Die Standardoption ist „Upload“. (Standard „Upload“)

**--number-of-folders** uint  Falls größer als 0, erstellen Sie Ordner, um die Daten aufzuteilen.

**--put-md5**  Erstellt einen MD5-Hash jeder Datei und speichert den Hash als „Content-MD5“-Eigenschaft des Zielblobs bzw. der Zieldatei. (Standardmäßig wird der Hash NICHT erstellt.) Identisch mit dem gleichnamigen Parameter im Befehl „copy“.

**--size-per-file** string  Die Größe jeder automatisch generierten Datendatei. Ein numerischer Wert, auf den unmittelbar K, M oder G folgen muss. Beispiel: 12k oder 200G (Standardwert: „250M“)

## <a name="options-inherited-from-parent-commands"></a>Von übergeordneten Befehlen geerbte Optionen

**--cap-mbps float**  Begrenzt die Übertragungsrate (in Megabits pro Sekunde) Der Schritt-für-Schritt-Durchsatz kann von der Obergrenze geringfügig abweichen. Wenn diese Option auf „null“ festgelegt oder weggelassen wird, ist der Durchsatz nicht begrenzt.

**--output-type** string  Das Format der Befehlsausgabe. Folgende Optionen sind verfügbar: „text“ und „json“. Der Standardwert lautet „text“. (Standardwert: „text“)

**--trusted-microsoft-suffixes** string   Gibt zusätzliche Domänensuffixe an, an die Azure Active Directory-Anmeldetoken gesendet werden können.  Der Standardwert ist ' *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net'. Alle hier aufgelisteten Werte werden zum Standardwert hinzugefügt. Aus Sicherheitsgründen sollten Sie hier nur Microsoft Azure-Domänen platzieren. Trennen Sie mehrere E-Mail-Adressen durch Semikolons voneinander.

## <a name="see-also"></a>Weitere Informationen

- [azcopy](storage-ref-azcopy.md)
