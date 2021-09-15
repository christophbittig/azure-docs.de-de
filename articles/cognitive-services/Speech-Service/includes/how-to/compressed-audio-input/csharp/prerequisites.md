---
author: PatrickFarley
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: pafarley
ms.openlocfilehash: ebd29149e623430ce2b1c8831ca2d80b74c0697f
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123646340"
---
Die Verarbeitung komprimierter Audiodaten wird mit [GStreamer](https://gstreamer.freedesktop.org) implementiert. Aus Lizenzierungsgründen werden die GStreamer-Binärdateien nicht kompiliert und mit dem Speech SDK verknüpft. Entwickler müssen mehrere Abhängigkeiten und Plug-Ins installieren. Weitere Informationen dazu finden Sie unter [Installieren unter Windows](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) oder [Installieren unter Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). GStreamer-Binärdateien müssen sich im Systempfad befinden, damit das Speech SDK sie zur Laufzeit laden kann. Wenn beispielsweise unter Windows das Speech SDK `libgstreamer-1.0-0.dll` oder `gstreamer-1.0-0.dll` (für die neueste Version von GStreamer) zur Laufzeit finden kann, bedeutet dies, dass sich die GStreamer-Binärdateien im Systempfad befinden.

