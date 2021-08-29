---
title: Linux-Abhängigkeiten des Micro-Agents (Vorschau)
description: In diesem Artikel werden die verschiedenen Linux-Betriebssystemabhängigkeiten beim Micro-Agent für Defender für IoT beschrieben.
ms.topic: conceptual
ms.date: 07/19/2021
ms.openlocfilehash: e878052e534ce7740fff1fdd462d2c95fcb11609
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114481432"
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

## <a name="next-steps"></a>Nächste Schritte

[Installieren des Micro-Agents für Defender für IoT (Vorschau)](quickstart-standalone-agent-binary-installation.md)