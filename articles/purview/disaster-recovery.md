---
title: Notfallwiederherstellung für Azure Purview
description: Erfahren Sie, wie Sie eine Notfallwiederherstellungsumgebung für Azure Purview konfigurieren.
author: sudheerreddykoppula
ms.author: sukoppul
ms.service: purview
ms.topic: how-to
ms.date: 04/23/2021
ms.openlocfilehash: a5b06b204cf0124a73639a9c2c59a2bc05712cb7
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129214292"
---
# <a name="disaster-recovery-for-purview"></a>Notfallwiederherstellung für Purview

In diesem Artikel wird erläutert, wie Sie eine Notfallwiederherstellungsumgebung für Azure Purview konfigurieren. Ausfälle von Azure-Rechenzentren sind selten, können aber von wenigen Minuten bis zu mehreren Stunden dauern. Rechenzentrumsausfälle können zu Störungen in Umgebungen führen, die für die Data Governance erforderlich sind. Durch das Befolgen der in diesem Artikel beschriebenen Schritte bleibt die Data Governance auch beim Ausfall eines Rechenzentrums in der primären Region für Ihr Purview-Konto weiterhin erhalten.

## <a name="achieve-business-continuity-for-azure-purview"></a>Erzielen von Geschäftskontinuität für Azure Purview

Business Continuity & Disaster Recovery (BCDR) bezieht sich in einer Azure Purview-Instanz auf die Mechanismen, Richtlinien und Verfahren, durch die sich Ihr Unternehmen bei einem Ausfall vor einem Datenverlust schützen und den Betrieb aufrechterhalten kann – dies gilt insbesondere in Bezug auf Überprüfung, Katalog und Erkenntnisse. In diesem Artikel wird erläutert, wie Sie eine Notfallwiederherstellungsumgebung für Azure Purview konfigurieren.

Derzeit bietet Azure Purview keine Unterstützung für automatisierte BCDR-Funktionen. Bis diese Unterstützung verfügbar ist, sind Sie für die Sicherungs- und Wiederherstellungsaktivitäten verantwortlich. Sie können manuell ein sekundäres Purview-Konto als betriebsbereite Standby-Instanz in einer anderen Region erstellen.

Die folgenden Schritte zeigen, wie Sie die Notfallwiederherstellung manuell durchführen können:

1. Sobald das primäre Purview-Konto in einer bestimmten Region erstellt wurde, müssen Sie über das Azure-Portal mindestens ein sekundäres Purview-Konto in einer separaten Region bereitstellen. 

2. Alle für das primäre Purview-Konto ausgeführten Aktivitäten müssen auch für die sekundären Purview-Konten ausgeführt werden. Dies schließt Folgendes ein: 

    - Verwalten von Kontoinformationen
    - Erstellen und Verwalten benutzerdefinierter Überprüfungsregelsätze, Klassifizierungen und Klassifizierungsregeln
    - Registrieren und Überprüfen von Quellen
    - Erstellen und Warten von Sammlungen zusammen mit der Zuordnung von Quellen zu den Sammlungen
    - Erstellen und Verwalten von Anmeldeinformationen, die während der Überprüfung verwendet werden
    - Zusammenstellen von Datenressourcen
    - Erstellen und Verwalten von Glossarbegriffen


Beachten Sie beim Erstellen Ihres Plans für manuelle BCDR-Vorgänge folgende Punkte: 

- Ihnen werden primäre und sekundäre Purview-Konten in Rechnung gestellt. 

- Die primären und sekundären Purview-Konten können ggf. nicht für die gleichen Azure Data Factory-, Azure Data Share- und Synapse Analytics-Konten konfiguriert werden.  Dies führt dazu, dass die Azure Data Factory- und Azure Data Share-Herkunft in den sekundären Purview-Konten nicht angezeigt werden kann. Darüber hinaus kann der Synapse Analytics-Arbeitsbereich, der dem primären Purview-Konto zugeordnet ist, keinen sekundären Purview-Konten zugeordnet werden. Dies ist eine derzeit geltende Einschränkung, die aufgehoben wird, wenn automatisierte BCDR-Funktionen unterstützt werden. 

- Die Integration Runtimes sind spezifisch für ein Purview-Konto. Falls daher parallele Überprüfungen in primären und sekundären Purview-Konten erforderlich sind, müssen mehrere selbstgehostete Integration Runtimes verwaltet werden. Diese Einschränkung wird ebenfalls aufgehoben, sobald automatisierte BCDR-Funktionen unterstützt werden. 

- Die parallele Ausführung von Überprüfungen über primäre und sekundäre Purview-Konten in derselben Quelle kann sich auf die Leistung der Quelle auswirken. Dies kann dazu führen, dass die Überprüfungsdauer für die Purview-Konten variiert.   

## <a name="related-information"></a>Verwandte Informationen

- [Business Continuity & Disaster Recovery](../best-practices-availability-paired-regions.md)
- [Hochverfügbarkeit – ein Teil Ihrer BCDR-Strategie](/azure/architecture/solution-ideas/articles/build-high-availability-into-your-bcdr-strategy)
- [Azure-Status](https://status.azure.com/status)

## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit Azure Purview finden Sie unter [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal](create-catalog-portal.md).
