---
title: 'Azure Virtual Desktop: Übersicht über das Anfügen von Apps mit MSIX – Azure'
description: Was ist das MSIX-Feature zum Anfügen von Apps? In diesem Artikel erfahren Sie es.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: d55308876db0928bef4fdf3b89ace10305f7c47d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339068"
---
# <a name="what-is-msix-app-attach"></a>Was ist das MSIX-Feature zum Anfügen von Apps?

MSIX ist ein neues Paketformat, das viele Features zur Verbesserung der Paketerstellung für alle Windows-Apps bietet. Weitere Informationen zu MSIX finden Sie in der [MSIX-Übersicht](/windows/msix/overview).

Das MSIX-Feature zum Anfügen von Apps ist eine Möglichkeit, MSIX-Anwendungen sowohl auf physischen als auch auf virtuellen Computern bereitzustellen. Dieses Feature unterscheidet sich von MSIX insofern, als es speziell für Azure Virtual Desktop entwickelt wurde. In diesem Artikel wird beschrieben, was das MSIX-Feature zum Anfügen von Apps ist und wie Sie davon profitieren können.

## <a name="application-delivery-options-in-azure-virtual-desktop"></a>Optionen für die Anwendungsbereitstellung in Azure Virtual Desktop

Sie können Apps in Azure Virtual Desktop mit einer der folgenden Methoden bereitstellen:

- Einfügen von Apps in ein Masterimage
- Verwenden von Tools wie SCCM oder Intune für die zentrale Verwaltung
- Dynamische App-Bereitstellung (AppV, VMware AppVolumes oder Citrix AppLayering)
- Erstellen von benutzerdefinierten Tools oder Skripts mithilfe von Microsoft und einem Drittanbietertool

## <a name="what-does-msix-app-attach-do"></a>Wie funktioniert das MSIX-Feature zum Anfügen von Apps?

In einer Azure Virtual Desktop-Bereitstellung bietet das Feature folgende Vorteile:

- Benutzer, Betriebssystem und Apps werden mithilfe von [MSIX-Containern](/windows/msix/msix-container) voneinander getrennt.
- Beim dynamischen Bereitstellen von Anwendungen müssen keine neuen Pakete erstellt werden.
- Die Anmeldezeit für Benutzer wird verkürzt.
- Infrastrukturanforderungen und -kosten sinken.

Das MSIX-Feature zum Anfügen von Apps muss außerhalb von VDI oder SBC anwendbar sein.

## <a name="traditional-app-layering-compared-to-msix-app-attach"></a>Herkömmliche Anwendungsschichten im Vergleich zum MSIX-Feature zum Anfügen von Apps

Die folgende Tabelle vergleicht die wichtigsten Funktionen des MSIX-Features zum Anfügen von Apps mit dem Verwenden von Anwendungsschichten.

| Feature | Herkömmliche Anwendungsschichten  | MSIX-Feature zum Anfügen von Apps  |
|-----|-----------------------------|--------------------|
| Format               | Verschiedene Technologien für Anwendungsschichten erfordern verschiedene proprietäre Formate. | Funktioniert mit dem nativen MSIX-Paketformat.        |
| Mehraufwand für erneutes Packen | Proprietäre Formate erfordern bei jedem Update einen Vorgang zum Sequenzieren und erneuten Packen.         | Als MSIX veröffentlichte Apps erfordern kein erneutes Packen. Wenn das MSIX-Paket jedoch nicht verfügbar ist, fällt der Mehraufwand für das erneute Packen weiterhin an. |
| Ökosystem            | K. A. (App-V wird beispielsweise von Herstellern nicht geliefert).  | MSIX ist die Mainstreamtechnologie von Microsoft, die von wichtigen ISV-Partnern und internen Apps wie Office zunehmend eingesetzt wird. Sie können MSIX sowohl auf virtuellen Desktops als auch auf physischen Windows-Computern verwenden. |
| Infrastruktur       | Es ist zusätzliche Infrastruktur erforderlich (Server, Clients usw.). | Nur Speicher.   |
| Verwaltung       | Wartungs- und Updatemaßnahmen sind erforderlich.   | Vereinfacht App-Updates. |
| Benutzererfahrung      | Beeinträchtigt die Anmeldedauer für Benutzer. Betriebssystemstatus, App-Status und Benutzer sind voneinander abgegrenzt.  | Übermittelte Apps lassen sich nicht von lokal installierten Apps unterscheiden. |

## <a name="next-steps"></a>Nächste Schritte

Mehr über das MSIX-Feature zum Anfügen von Apps erfahren Sie in unserem [Glossar](app-attach-glossary.md) und den [häufig gestellten Fragen](app-attach-faq.yml). Oder legen Sie direkt mit dem [Einrichten des MSIX-Features zum Anfügen von Apps](app-attach.md) los.
