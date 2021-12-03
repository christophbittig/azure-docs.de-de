---
title: Linux-Abhängigkeiten des Micro-Agents (Vorschau)
description: In diesem Artikel werden die verschiedenen Linux-Betriebssystemabhängigkeiten beim Micro-Agent für Defender für IoT beschrieben.
ms.topic: conceptual
ms.date: 11/09/2021
ms.openlocfilehash: 9fa1d53481e5e7f3b775855e54a0387fc04518ba
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284111"
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
