---
title: Übersicht der Validierung von Diensten mit Azure Arc-Unterstützung
description: Erläutert den Azure Arc-Überprüfungsprozess zur Sicherstellung der Konformität von Kubernetes mit Arc-Unterstützung, Datendiensten und Clustererweiterungen.
ms.date: 07/30/2021
ms.topic: overview
ms.openlocfilehash: 1cf208b9a476f4a2a16df157ebfa0bb7661cfc1c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785384"
---
# <a name="overview-of-azure-arc-enabled-service-validation"></a>Übersicht über die Validierung von Diensten mit Arc-Unterstützung

Microsoft empfiehlt, Dienste mit Arc-Unterstützung auf überprüften Plattformen auszuführen. In diesem Artikel wird auf Inhalte verwiesen, um zu erläutern, wie verschiedene Komponenten mit Arc-Unterstützung überprüft werden. 

Derzeit sind validierte Lösungen von Partnern für Kubernetes und Datendienste verfügbar.

## <a name="kubernetes"></a>Kubernetes

Kubernetes mit Azure Arc-Unterstützung funktioniert mit jedem Kubernetes-Cluster, der eine CNCF-Zertifizierung (Cloud Native Computing Foundation) besitzt. Das Azure Arc-Team hat auch mit den wichtigsten Anbietern von Kubernetes-Angeboten der Branche zusammengearbeitet, um Kubernetes mit Azure Arc-Unterstützung mit ihren [Kubernetes-Distributionen](../kubernetes/validation-program.md) zu überprüfen. Zukünftige Haupt- und Nebenversionen von Kubernetes-Distributionen, die von diesen Anbietern veröffentlicht werden, werden auf Kompatibilität mit Kubernetes mit Azure Arc-Aktivierung überprüft.

## <a name="data-services"></a>Datendienste

Wir sind außerdem eine Partnerschaft mit OEM-Partnern (Original Equipment Manufacturer) und Speicheranbietern eingegangen, um [Datendienstlösungen mit Arc-Unterstützung](../data/validation-program.md) zu überprüfen.

## <a name="validation-process"></a>Validierungsprozess

Der Azure Arc-Validierungsprozess ist in GitHub verfügbar. Weitere Informationen zum Validieren Ihres Angebots mit Azure Arc, zur Testumgebung und -strategie finden Sie im [Azure Arc-Validierungsprozess](https://github.com/Azure/azure-arc-validation/) in GitHub.

## <a name="next-steps"></a>Nächste Schritte

* [Unterstützte Kubernetes-Distributionen](../kubernetes/validation-program.md?toc=/azure/azure-arc/toc.json&bc=/azure/azure-arc/breadcrumb/toc.json)

* [Validierte Kubernetes-Distributionen für Datendienste](../data/validation-program.md?toc=/azure/azure-arc/toc.json&bc=/azure/azure-arc/breadcrumb/toc.json)
