---
title: Was ist eine Unterressource einer virtuellen Netzwerkverknüpfung von privaten Azure DNS-Zonen?
description: Überblick über die Unterressource für die VNET-Verknüpfung einer privaten Azure DNS-Zone
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 04/26/2021
ms.author: rohink
ms.openlocfilehash: fa1729029d7576dac1dab2ea77cfe1c257441b42
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131500809"
---
# <a name="what-is-a-virtual-network-link"></a>Was ist eine virtuelle Netzwerkverknüpfung?

Nachdem Sie eine private Azure DNS-Zone erstellt haben, müssen Sie ein virtuelles Netzwerk damit verknüpfen. Nach der Verknüpfung können in diesem virtuellen Netzwerk gehostete VMs auf die private DNS-Zone zugreifen. Jede private DNS-Zone verfügt über eine Sammlung untergeordneter Ressourcen der virtuellen Netzwerkverknüpfung. Jede dieser Ressourcen stellt eine Verbindung mit einem virtuellen Netzwerk dar. Ein virtuelles Netzwerk kann nur mit einer privaten DNS-Zone als ein virtuelles Netzwerk für die Registrierung oder Auflösung verknüpft werden.

## <a name="registration-virtual-network"></a>Virtuelles Registrierungsnetzwerk

Wenn Sie [eine Verknüpfung zwischen einer privaten DNS-Zone und einem virtuellen Netzwerk erstellen](./private-dns-getstarted-portal.md#link-the-virtual-network), können Sie die [automatische Registrierung](./private-dns-autoregistration.md) aktivieren. Wenn diese Einstellung aktiviert ist, fungiert das virtuelle Netzwerk als Registrierungsnetzwerk für die private DNS-Zone. Für jede VM, die Sie im Netzwerk bereitstellen, wird automatisch ein DNS-Eintrag erstellt. Für bereits im Netzwerk bereitgestellte VMs wird ebenfalls ein DNS-Eintrag erstellt.

Aus der Perspektive des virtuellen Netzwerks wird die private DNS-Zone zur Registrierungszone für dieses virtuelle Netzwerk. Eine private DNS-Zone kann mehrere virtuelle Registrierungsnetzwerke umfassen. Jedem virtuellen Netzwerk kann jedoch nur eine Registrierungszone zugeordnet sein.

## <a name="resolution-virtual-network"></a>Virtuelles Auflösungsnetzwerk

Für den Fall, dass Sie Ihr virtuelles Netzwerk ohne automatische Registrierung mit der privaten DNS-Zone verknüpfen möchten. wird das virtuelle Netzwerk nur als virtuelles Auflösungsnetzwerk behandelt. Für VMs, die in diesem virtuellen Netzwerk bereitgestellt sind, werden nicht automatisch DNS-Einträge in der privaten Zone erstellt. Die im virtuellen Netzwerk bereitgestellten VMs können jedoch erfolgreich DNS-Einträge in der privaten DNS-Zone abfragen. Dies schließt manuell erstellte und automatisch registrierte Einträge aus anderen virtuellen Netzwerken ein, die mit der privaten DNS-Zone verknüpft sind.

Eine private DNS-Zone kann mehrere virtuelle Auflösungsnetzwerke aufweisen, und einem virtuellen Netzwerk können mehrere Auflösungszonen zugeordnet sein.

## <a name="limits"></a>Grenzwerte

Um zu verstehen, wie viele Registrierungs- und Auflösungsnetzwerke Sie mit privaten DNS-Zonen verknüpfen können, finden Sie unter [Azure DNS-Limits](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-dns-limits).

## <a name="other-considerations"></a>Weitere Überlegungen

* Virtuelle Netzwerke, die unter Verwendung des klassischen Bereitstellungsmodells bereitgestellt wurden, werden nicht unterstützt.

* Sie können nur eine Verknüpfung zwischen einer privaten DNS-Zone und einem virtuellen Netzwerk erstellen.

* Jede virtuelle Netzwerkverknüpfung unter einer privaten DNS-Zone muss einen innerhalb des Kontexts der privaten DNS-Zone eindeutigen Namen besitzen. Verknüpfungen mit demselben Namen können in unterschiedlichen privaten DNS-Zonen vorhanden sein.

* Überprüfen Sie nach dem Erstellen einer virtuellen Netzwerkverknüpfung das Feld „Verknüpfungsstatus“ der virtuellen Netzwerkverknüpfungsressource. Abhängig von der Größe des virtuellen Netzwerks kann es ein paar Minuten dauern, bis die Verknüpfung funktioniert und sich der Verknüpfungsstatus in *Abgeschlossen* ändert.

* Wenn Sie ein virtuelles Netzwerk löschen, werden alle virtuellen Netzwerkverknüpfungen und automatisch registrierten DNS-Einträge, die ihm in verschiedenen privaten DNS-Zonen zugeordnet sind, automatisch gelöscht.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie ein virtuelles Netzwerk mithilfe des [Azure-Portals](./private-dns-getstarted-portal.md#link-the-virtual-network) mit einer privaten DNS-Zone verknüpfen.

* Erfahren Sie, wie Sie über [Azure PowerShell](./private-dns-getstarted-powershell.md) oder [Azure CLI](./private-dns-getstarted-cli.md) eine private Zone in Azure DNS erstellen.

* Informieren Sie sich über einige gängige [Szenarien zu privaten Zonen](./private-dns-scenarios.md), die mit privaten Zonen in Azure DNS realisiert werden können.

* Allgemeine Fragen und Antworten zu privaten Zonen in Azure DNS, einschließlich des spezifischen Verhaltens für bestimmte Vorgänge, finden Sie unter [Häufig gestellte Fragen zu privatem Azure DNS](./dns-faq-private.yml).
