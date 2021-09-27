---
title: azcopy jobs remove | Microsoft-Dokumentation
description: Dieser Artikel enthält Referenzinformationen zum Befehl „azcopy jobs remove“.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: ac68ea66250a906cf42c489c8ad8dcb0fe972003
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128673196"
---
# <a name="azcopy-jobs-remove"></a>azcopy jobs remove

Entfernt alle Dateien, die der angegebenen Auftrags-ID zugeordnet sind.

> [!NOTE]
> Sie können den Speicherort von Protokoll- und Plandateien anpassen. Weitere Informationen finden Sie unter dem Befehl [azcopy env](storage-ref-azcopy-env.md).

```
azcopy jobs remove [jobID] [flags]
```

## <a name="related-conceptual-articles"></a>Verwandte konzeptionelle Artikel

- [Erste Schritte mit AzCopy](storage-use-azcopy-v10.md)
- [Übertragen von Daten mit AzCopy und Blobspeicher](./storage-use-azcopy-v10.md#transfer-data)
- [Übertragen von Daten mit AzCopy und Dateispeicher](storage-use-azcopy-files.md)

## <a name="examples"></a>Beispiele

```
  azcopy jobs rm e52247de-0323-b14d-4cc8-76e0be2e2d44
```

## <a name="options"></a>Tastatur

**--help:** Hilfe beim Entfernen

## <a name="options-inherited-from-parent-commands"></a>Von übergeordneten Befehlen geerbte Optionen

**--cap-mbps float:** begrenzt die Übertragungsrate (in Megabits pro Sekunde) Der Schritt-für-Schritt-Durchsatz kann von der Obergrenze geringfügig abweichen. Wenn diese Option auf „null“ festgelegt oder weggelassen wird, ist der Durchsatz nicht begrenzt.

**--output-type** string   Das Format der Befehlsausgabe. Folgende Optionen sind verfügbar: „text“ und „json“. Standardwert: `text`. (Standardwert: `text`)

**--trusted-microsoft-suffixes** string   Gibt zusätzliche Domänensuffixe an, an die Azure Active Directory-Anmeldetoken gesendet werden können.  Der Standardwert ist ' *.core.windows.net;* .core.chinacloudapi.cn; *.core.cloudapi.de;* .core.usgovcloudapi.net'. Alle hier aufgelisteten Werte werden zum Standardwert hinzugefügt. Aus Sicherheitsgründen sollten Sie hier nur Microsoft Azure-Domänen platzieren. Trennen Sie mehrere E-Mail-Adressen durch Semikolons voneinander.

## <a name="see-also"></a>Weitere Informationen

- [azcopy jobs](storage-ref-azcopy-jobs.md)
