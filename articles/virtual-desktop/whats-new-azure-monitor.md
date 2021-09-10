---
title: Neuigkeiten in Azure Monitor für Azure Virtual Desktop
description: Neue Features und Produktupdates für den Azure Virtual Desktop-Agent.
author: Heidilohr
ms.topic: reference
ms.date: 07/09/2021
ms.author: helohr
manager: femila
ms.custom: references_regions
ms.openlocfilehash: 000fb5a26c34a8fbb9013ad873aa167aa759ae7b
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114450875"
---
# <a name="whats-new-in-azure-monitor-for-azure-virtual-desktop"></a>Neuigkeiten in Azure Monitor für Azure Virtual Desktop

In diesem Artikel werden die Änderungen beschrieben, die wir an jeder neuen Version von Azure Monitor für Azure Virtual Desktop vornehmen.

Wenn Sie nicht sicher sind, welche Version von Azure Monitor Sie derzeit verwenden, finden Sie sie in der unteren rechten Ecke der Seite „Erkenntnisse“ oder in der Konfigurationsarbeitsmappe. Wechseln Sie zu [https://aka.ms/azmonwvdi](https://aka.ms/azmonwvdi), um auf Ihre Arbeitsmappe zuzugreifen.

## <a name="how-to-read-version-numbers"></a>Lesen von Versionsnummern

Jede Version von Azure Monitor für Azure Virtual Desktop umfasst drei Nummern. Die einzelnen Nummern bedeuten Folgendes:

- Die erste Nummer ist die Hauptversion und wird in der Regel für größere Releases verwendet.

- Die zweite Zahl ist die Nebenversion. Nebenversionen sind für abwärtskompatible Änderungen wie neue Features und Hinweise zur eingestellten Unterstützung vorgesehen.

- Die dritte Nummer ist die Patchversion, die für kleine Änderungen verwendet wird, die falsches Verhalten oder Fehler beheben.

Ein Release mit der Versionsnummer 1.2.31 gehört z. B. zur ersten Hauptversion, der zweiten Nebenversion und der Patchnummer 31.

Wenn einer der Nummern erhöht wird, müssen auch alle nachstehenden Nummern geändert werden. Ein Release verfügt über eine Versionsnummer. Allerdings verfolgen nicht alle Versionsnummern die Releases nach. Patchnummern können z. B. etwas willkürlich sein.

## <a name="version-100"></a>Version 1.0.0

Veröffentlichungsdatum: 21. März 2021

In dieser Version wurden die folgenden Änderungen vorgenommen:

- Wir haben einen neuen visuellen Indikator für Fehler und Warnungen mit hohen Auswirkungen aus dem Ereignisprotokoll des Azure Virtual Desktop-Agents auf der Diagnoseseite des Hosts eingeführt.

- Wir haben fünf kostenintensive Prozessleistungsindikatoren aus der Standardkonfiguration entfernt. Weitere Informationen finden Sie in unserem Blogbeitrag unter [Aktualisierte Anleitung zu Azure Monitor für Azure Virtual Desktop](https://techcommunity.microsoft.com/t5/windows-virtual-desktop/updated-guidance-on-azure-monitor-for-wvd/m-p/2236173).

- Der Setupvorgang für das Windows-Ereignisprotokoll für die Konfigurationsarbeitsmappe ist jetzt automatisiert.

- Die Konfigurationsarbeitsmappe unterstützt jetzt die automatisierte Bereitstellung empfohlener Windows-Ereignisprotokolle.

- Die Konfigurationsarbeitsmappe kann jetzt den Log Analytics-Agent und den Arbeitsbereich mit den bevorzugten Einstellungen für Sitzungshosts außerhalb der Region der Ressourcengruppe installieren.

- Die Konfigurationsarbeitsmappe verfügt jetzt über ein Registerkartenlayout für den Setupvorgang.

- Mit diesem Update wurde die Versionsverwaltung eingeführt.

## <a name="next-steps"></a>Nächste Schritte

Die Seite mit allgemeinen Neuigkeiten finden Sie unter [Neuigkeiten in Azure Virtual Desktop](whats-new.md).

Weitere Informationen zu Azure Monitor für Azure Virtual Desktop finden Sie unter [Verwenden von Azure Monitor für Azure Virtual Desktop zum Überwachen Ihrer Bereitstellung](azure-monitor.md).
