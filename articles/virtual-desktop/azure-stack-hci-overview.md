---
title: 'Azure Virtual Desktop für Azure Stack HCI (Vorschauversion): Azure'
description: Eine kurze Übersicht über Azure Virtual Desktop für Azure Stack HCI (Vorschauversion).
author: Heidilohr
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: helohr
manager: femila
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5faea9929fb62af8736e62ca6e629cb2bde01f3a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452165"
---
# <a name="azure-virtual-desktop-for-azure-stack-hci-preview"></a>Azure Virtual Desktop für Azure Stack HCI (Vorschauversion)

> [!IMPORTANT]
> Azure Virtual Desktop für Azure Stack HCI befindet sich derzeit in der Vorschauphase.
> Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten rechtliche Bedingungen. Sie gelten für diejenigen Azure-Features, die sich in der Beta- oder Vorschauversion befinden oder aber anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Mit Azure Virtual Desktop für Azure Stack HCI (Vorschauversion) können Sie Azure Virtual Desktop-Sitzungshosts in Ihrer lokalen Azure Stack HCI-Infrastruktur bereitstellen. Darüber hinaus ermöglicht Azure Virtual Desktop für Azure Stack HCI die Verwaltung Ihrer Sitzungshosts über das Azure-Portal. Wenn Sie bereits über eine lokale VDI-Bereitstellung (Virtual Desktop Infrastructure) verfügen, kann Azure Virtual Desktop für Azure Stack HCI Ihre Administrator- und Endbenutzererfahrung verbessern. Falls Sie Azure Virtual Desktop bereits in der Cloud verwenden, können Sie Ihre Bereitstellung auf Ihre lokale Infrastruktur erweitern, um Ihre Anforderungen in puncto Leistung oder Datenlokalität besser zu erfüllen.

Azure Virtual Desktop für Azure Stack HCI befindet sich derzeit in der Public Preview-Phase. Bestimmte wichtige Azure Virtual Desktop-Features werden derzeit nicht von Azure Stack HCI unterstützt. Aufgrund dieser Einschränkungen sollte dieses Feature noch nicht für Produktionsworkloads verwendet werden.

## <a name="key-benefits"></a>Hauptvorteile

Wir haben bereits erklärt, was Azure Virtual Desktop für Azure Stack HCI ist. Die Frage bleibt: Welche Möglichkeiten bietet Ihnen diese Lösung?

Azure Virtual Desktop für Azure Stack HCI ermöglicht Folgendes:

- Sie können die Leistung für Azure Virtual Desktop-Benutzer in Bereichen mit schlechter Konnektivität mit der öffentlichen Azure-Cloud verbessern, indem Sie ihnen Sitzungshost zur Verfügung stellen, die sich näher an ihrem Standort befinden.

- Sie können dafür sorgen, dass App- und Benutzerdaten in der lokalen Umgebung bleiben, um Anforderungen hinsichtlich Datenlokalität zu erfüllen.  Weitere Informationen finden Sie unter [Datenstandorte für Azure Virtual Desktop](data-locations.md).

- Sie können den Zugriff auf lokale Legacy-Apps und Datenquellen verbessern, indem Sie virtuelle Desktops und Apps am gleichen Standort verwenden.

- Sie können virtuelle Desktops mit mehreren Sitzungen unter Windows 10 oder Windows 11 Enterprise verwenden, um Kosten zu senken und die Benutzerfreundlichkeit zu verbessern.

- Sie können das Azure-Portal verwenden, um Ihre VDI-Bereitstellung und -Verwaltung zu vereinfachen (verglichen mit herkömmlichen lokalen VDI-Lösungen).

## <a name="pricing"></a>Preise

Die Höhe der Betriebskosten von Azure Virtual Desktop für Azure Stack HCI hängt von folgenden Faktoren ab:
 - Infrastrukturkosten. Sie zahlen monatliche Dienstgebühren für Azure Stack HCI. Weitere Informationen finden Sie unter [Azure Stack HCI – Preise](https://azure.microsoft.com/pricing/details/azure-stack/hci/).
 
- Benutzerzugriffsrechte für Azure Virtual Desktop. Die Lizenzen, die den Zugriff auf Azure Virtual Desktop in der Cloud ermöglichen, decken auch Azure Virtual Desktop für Azure Stack HCI ab. Weitere Informationen finden Sie unter [Azure Virtual Desktop – Preise](https://azure.microsoft.com/pricing/details/virtual-desktop/).

- Die Gebühr für den Azure Virtual Desktop-Hybriddienst. Bei dieser Gebühr fallen Kosten für jede aktive virtuelle CPU (vCPU) von Azure Virtual Desktop-Sitzungshosts an, die Sie in Azure Stack HCI ausführen. Diese Gebühr wird erhoben, sobald der Vorschauzeitraum endet.

## <a name="known-issues-and-limitations"></a>Einschränkungen und bekannte Probleme

Folgende Probleme, die sich auf die Public Preview-Version von Azure Virtual Desktop für Azure Stack HCI auswirken, sind bekannt:

- Von Azure Virtual Desktop-Bereitstellungen mit [RDP Shortpath](shortpath.md) kann derzeit keine Verbindung mit in Azure Stack HCI ausgeführten Sitzungshosts hergestellt werden.

- Folgende Azure Virtual Desktop-Features werden derzeit nicht von Azure Stack HCI-Hostpools unterstützt:
    
    - [Azure Monitor für Azure Virtual Desktop](azure-monitor.md)
    - [Skalieren von Sitzungshosts mit Azure Automation](set-up-scaling-script.md)
    - [Autoskalierung (Vorschauversion)](autoscale-scaling-plan.md)
    - [Starten von virtuellen Computern bei der Verbindungsherstellung](start-virtual-machine-connect.md)
    - [Multimediaumleitung (Vorschau)](multimedia-redirection.md)
    - [Preise für benutzerspezifischen Zugriff](./remote-app-streaming/licensing.md)

- Auf der Registerkarte „Azure Virtual Desktop“ im Azure-Portal ist es nicht möglich, neue virtuelle Computer direkt in der Azure Stack HCI-Infrastruktur zu erstellen. Stattdessen müssen Administratoren separat lokale virtuelle Computer erstellen und sie dann bei einem Azure Virtual Desktop-Hostpool registrieren.

- Beim Herstellen einer Verbindung mit einem virtuellen Windows-10- oder 11-Unternehmens-Desktop mit mehreren Sitzungen können für Benutzer Aktivierungsprobleme angezeigt werden, z. B. ein Desktop-Wasserzeichen mit den Worten "Windows aktivieren", selbst wenn sie über eine berechtigte Lizenz verfügen.

- Azure Virtual Desktop für Azure Stack HCI unterstützt derzeit keine Hostpools, die sowohl cloudbasierte als auch lokale Sitzungshosts enthalten. Jeder Hostpool in der Bereitstellung darf nur einen einzelnen Hostpooltyp aufweisen.

- Bestimmte rein cloudbasierte Azure-Dienste werden von Sitzungshosts in Azure Stack HCI nicht unterstützt.

- Da Azure Stack HCI so viele Arten von Hardware und lokalen Netzwerkfunktionen unterstützt, können sich Leistung und Benutzerdichte zwischen Sitzungshosts, die in der Azure-Cloud ausgeführt werden, stark unterscheiden. Die [Richtlinien zur Größenfestlegung virtueller Computer](/windows-server/remote/remote-desktop-services/virtual-machine-recs) für Azure Virtual Desktop sind weit gefasst und sollten daher nur für anfängliche Leistungsschätzungen herangezogen werden.

Sollten während der Vorschauphase Probleme auftreten, die nicht in dieser Liste aufgeführt sind, können Sie diese gerne melden.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie nun mit Azure Virtual Desktop für Azure Stack HCI vertraut sind, können Sie sich als Nächstes unter [Einrichten von Azure Virtual Desktop für Azure Stack HCI (Vorschauversion)](azure-stack-hci.md) darüber informieren, wie Sie dieses Feature bereitstellen.
