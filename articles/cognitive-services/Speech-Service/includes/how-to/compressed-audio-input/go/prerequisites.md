---
author: amitkumarshukla
ms.service: cognitive-services
ms.topic: include
ms.date: 09/17/2021
ms.author: amishu
ms.openlocfilehash: 50383f178faaffa149eb371d21e95d05acdaad0a
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156668"
---
Speech SDK kann [GStreamer](https://gstreamer.freedesktop.org) verwenden, um komprimiertes Audio zu verarbeiten. Aus lizenzrechtlichen Gründen werden die GStreamer-Binärdateien jedoch nicht mit dem Speech SDK kompiliert und gelinkt. Entwickler müssen verschiedene Abhängigkeiten und Plugins installieren, siehe [Installation unter Linux](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c). Die Sprache Go wird im Speech SDK nur auf der Linux-Plattform unterstützt. Siehe [Speech SDK für Go](../../../../quickstarts/setup-platform.md?pivots=programming-language-go&tabs=dotnet%252cwindows%252cjre%252cbrowser), um mit Microsoft Speech SDK in Go zu beginnen. 

