---
title: Zonenredundanz in App Service-Umgebung
description: Hier finden Sie eine Übersicht über die Zonenredundanz in einer App Service-Umgebung.
author: madsd
ms.topic: overview
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: 3ce5a0925ae4c1dc1bc33fec73987455d612d661
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526719"
---
# <a name="availability-zone-support-for-app-service-environments"></a>Unterstützung für Verfügbarkeitszonen in App Service-Umgebungen

> [!NOTE]
> In diesem Artikel wird die App Service-Umgebung V3 beschrieben, die mit App Service-Plänen vom Typ „Isoliert V2“ verwendet wird.
>

Eine App Service-Umgebung (App Service Environment, ASE) kann in [Verfügbarkeitszonen (VZ)](../../availability-zones/az-overview.md) bereitgestellt werden. Diese Architektur wird auch als „Zonenredundanz“ bezeichnet. Wenn eine ASE als zonenredundant konfiguriert ist, verteilt die Plattform die App Service-Planinstanzen in der ASE automatisch auf alle drei Zonen in der ausgewählten Region. Wenn eine Kapazität größer als drei angegeben und die Anzahl der Instanzen durch drei teilbar ist, werden die Instanzen gleichmäßig verteilt. Andernfalls werden Anzahlen von Instanzen über 3*N hinaus auf die verbleibenden ein oder zwei Zonen verteilt.

Sie konfigurieren Zonenredundanz, wenn Sie Ihre ASE erstellen, und alle App Service-Pläne, die in dieser ASE erstellt werden, sind zonenredundant. Zonenredundanz kann nur beim Erstellen einer *neuen* App Service-Umgebung angegeben werden. Zonenredundanz wird nur in [einer Teilmenge der Regionen](./overview.md#regions) unterstützt.

Wenn eine Zone ausfällt, erkennt die App Service-Plattform verlorene Instanzen und versucht automatisch, neue Ersatzinstanzen zu finden. Wenn Sie außerdem automatische Skalierung konfiguriert haben und diese entscheidet, dass weitere Instanzen benötigt werden, gibt die automatische Skalierung auch eine Anforderung an App Service aus, um weitere Instanzen hinzuzufügen (das Verhalten der automatischen Skalierung ist unabhängig vom Verhalten der App Service-Plattform). Es ist wichtig zu beachten, dass es keine Garantie dafür gibt, dass Anforderungen für Instanzen in einem Zonenausfallszenario erfolgreich sein werden, da das Auffüllen verlorener Instanzen auf Grundlage der besten Leistung („Best Effort“) erfolgt. Die empfohlene Lösung besteht in der Skalierung Ihrer App Service-Pläne, um den Verlust einer Zone zu berücksichtigen.

In einer zonenredundanten ASE bereitgestellte Anwendungen werden weiterhin ausgeführt und verarbeiten Datenverkehr, selbst wenn andere Zonen in derselben Region ausfallen. Es ist jedoch möglich, dass Verhalten außerhalb der Laufzeit, einschließlich Skalieren von App Service-Plänen, Anwendungserstellung, -konfiguration und -veröffentlichung, weiterhin von einem Ausfall in anderen Verfügbarkeitszonen betroffen sind. Zonenredundanz für die App Service-Umgebung stellt nur eine kontinuierliche Uptime für bereitgestellte Anwendungen sicher.

Wenn die App Service-Plattform einem zonenredundanten App Service-Plan in einer ASE Instanzen zuordnet, verwendet sie einen [Best-Effort-Zonenausgleich, der von den zugrunde liegenden Azure-VM-Skalierungsgruppen angeboten wird](../../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing). Ein App Service-Plan ist „ausgeglichen“, wenn jede Zone entweder über dieselbe Anzahl von Instanzen oder +/-1 Instanz in allen anderen Zonen verfügt, die vom App Service-Plan verwendet werden.

## <a name="pricing"></a>Preise

 In einer zonenredundanten ASE werden mindestens neun App Service-Planinstanzen in Rechnung gestellt. Wenn Sie über neun oder mehr App Service Planinstanzen verfügen, fällt keine zusätzliche Gebühr für die Unterstützung von Verfügbarkeitszonen an. Wenn die zonenredundante ASE weniger als neun Instanzen (beliebiger Größe) in App Service-Plänen enthält, wird der Unterschied zwischen neun Instanzen und der Anzahl der ausgeführten Instanzen als zusätzliche Windows I1v2-Instanzen berechnet.

## <a name="next-steps"></a>Nächste Schritte

* Informieren Sie sich über [Verfügbarkeitszonen](../../availability-zones/az-overview.md).
