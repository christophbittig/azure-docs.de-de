---
title: Überlegungen im Zusammenhang mit dem Patchen der Bare-Metal-Infrastruktur für Oracle
description: Hier finden Sie Überlegungen im Zusammenhang mit dem Patchen des Betriebssystems/Kernels für die Bare-Metal-Infrastruktur für Oracle.
ms.topic: reference
ms.subservice: baremetal-oracle
ms.date: 10/06/2021
ms.openlocfilehash: 145601a4725b257be283840eed1e31f68ed720d1
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215913"
---
# <a name="patching-considerations-for-baremetal-for-oracle"></a>Überlegungen im Zusammenhang mit dem Patchen der Bare-Metal-Infrastruktur für Oracle

In diesem Artikel werden wichtige Überlegungen im Zusammenhang mit dem Patchen des Betriebssystems/Kernels für die Bare-Metal-Infrastruktur für Oracle behandelt.

Installieren Sie die betriebssystemspezifische Version der eNIC- und fNIC-Treiber, um eine ordnungsgemäße Netzwerkleistung und Systemstabilität zu gewährleisten. Weitere Informationen finden Sie in der folgenden Kompatibilitätstabelle. 

Server werden mit kompatiblen Versionen an Kunden ausgeliefert. Beim Anwenden von Patches auf das Betriebssystem/den Kernel kann jedoch ein Rollback der Treiber auf die Standardtreiberversionen erfolgen. Stellen Sie daher sicher, dass nach dem Patchen von Betriebssystem/Kernel die richtige Treiberversion ausgeführt wird.

| Betriebssystemhersteller | Betriebssystem-Paketversion | Firmware Version | eNIC-Treiber | fNIC-Treiber |
| --- | --- | --- | --- | --- |
| Red Hat | RHEL 7.6 | 3.2.3i | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7.6 | 4.1.1b | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7.6 | 4.1.1b | 4.0.0.8  | 2.0.0.60 |

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich mit der Ethernetkonfiguration von Bare-Metal für Oracle vertraut.

> [!div class="nextstepaction"]
> [Ethernetkonfiguration von Bare-Metal für Oracle](oracle-baremetal-ethernet.md)

