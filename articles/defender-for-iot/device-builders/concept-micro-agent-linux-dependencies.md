---
title: Linux-Abhängigkeiten des Micro-Agents (Vorschau)
description: In diesem Artikel werden die verschiedenen Linux-Betriebssystemabhängigkeiten beim Micro-Agent für Defender für IoT beschrieben.
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 102cf31dbe19c068de344f1f7f294a6f3f0a722b
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967557"
---
# <a name="micro-agent-linux-dependencies-preview"></a>Linux-Abhängigkeiten des Micro-Agents (Vorschau)

In diesem Artikel werden die verschiedenen Linux-Betriebssystemabhängigkeiten beim Micro-Agent für Defender für IoT beschrieben. 

## <a name="linux-dependencies"></a>Linux-Abhängigkeiten

Die folgende Tabelle zeigt die Linux-Abhängigkeiten für die einzelnen Komponenten. 

| Komponente | Abhängigkeit | type | Erforderlich für das IoT SDK | Notizen |
|--|--|--|--|--|
| **Core** |  |  |  |  |
|  | libcurl-openssl (libcurl) | Bibliothek | ✔ |  |
|  | libssl | Bibliothek | ✔ |  |
|  | UUID | Bibliothek | ✔ |  |
|  | pthread | Flag für die Kompilierung von ulibc | ✔ |  |
|  | libuv1 | Bibliothek |  |  |
|  | sudo | Paket |  |  |
|  | uuid-runtime | Paket |  |  |
| **Systeminformationssammler** |  |  |  |  |
|  | uname | Systemaufruf |  |  |
| **Baselinesammler** |  |  |  |  |
|  | BusyBox | Linux-Kompilierungsflag |  |  |
|  | Bash | Linux-Kompilierungsflag |  |  |
| **Prozesssammler** |  |  |  |  |
|  | CONFIG_CONNECTOR=y | Kernelkonfiguration |  |  |
|  | CONFIG_PROC_EVENTS=y | Kernelkonfiguration |  |  |
| **Netzwerksammler** |  |  |  |  |
|  | libpcap | Bibliothek |  |  |
|  | CONFIG_PACKET=y | Kernelkonfiguration |  |  |
|  | CONFIG_NETFILTER =y | Kernelkonfiguration |  | Optional – Leistungsverbesserung |
| **Anmeldungssammler** |  |  |  |  |
|  | Wtmp, btmp | Protokolldateien |  | [utmp](https://en.wikipedia.org/wiki/Utmp) |

## <a name="next-steps"></a>Nächste Schritte

[Installieren des Micro-Agents für Defender für IoT (Vorschau)](quickstart-standalone-agent-binary-installation.md)