---
title: 'Azure Confidential Computing: Übersicht'
description: Enthält eine Übersicht über Azure Confidential Computing (ACC).
services: virtual-machines
author: stempesta
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 11/01/2021
ms.author: stempesta
ms.custom: ignite-fall-2021
ms.openlocfilehash: 77c65a7c5418ebefd7b2414d04bec614eccdc5ba
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132284358"
---
# <a name="what-is-confidential-computing"></a>Was ist Confidential Computing?

Mit Confidential Computing können Sie Ihre vertraulichen Daten während der Verarbeitung isolieren. Confidential Computing wird in vielen Branchen wie folgt genutzt, um die eigenen Daten zu schützen:

- Schützen von Finanzdaten
- Schützen von Patientendaten
- Ausführen von Machine Learning-Prozessen für vertrauliche Daten
- Ausführen von Algorithmen für verschlüsselte Datasets aus mehreren Quellen


## <a name="overview"></a>Übersicht
<p><p>


> [!VIDEO https://www.youtube.com/embed/rT6zMOoLEqI]

Wir wissen, wie wichtig der Schutz Ihrer Clouddaten für Sie ist. Wir nehmen Ihre Bedenken ernst. Hier sind einige Fragen aufgeführt, die unsere Kunden in Bezug auf die Verlagerung von sensiblen Workloads in die Cloud ggf. stellen: 

- Wie kann ich sicherstellen, dass Microsoft keinen Zugriff auf unverschlüsselte Daten hat?
- Wie kann ich Sicherheitsbedrohungen durch privilegierte Administratoren innerhalb meines Unternehmens verhindern?
- Welche weiteren Möglichkeiten habe ich, um zu verhindern, dass Dritte Zugriff auf vertrauliche Kundendaten haben?

Mit Azure können Sie Ihre Angriffsfläche verkleinern, um einen höheren Schutz von Daten zu erzielen. Azure verfügt bereits über viele Tools zum Schützen von [**ruhenden Daten**](../security/fundamentals/encryption-atrest.md), indem Modelle wie die clientseitige und serverseitige Verschlüsselung genutzt werden. Darüber hinaus können in Azure Verfahren genutzt werden, um [**Daten während der Übertragung**](../security/fundamentals/data-encryption-best-practices.md#protect-data-in-transit) zu verschlüsseln. Hierfür werden sichere Protokolle wie TLS und HTTPS verwendet. Auf dieser Seite wird noch eine dritte Möglichkeit zum Verschlüsseln von Daten vorgestellt: die Verschlüsselung von **Daten in Gebrauch**.

## <a name="introduction-to-confidential-computing"></a>Einführung in Confidential Computing  

Confidential Computing ist ein branchenüblicher Begriff, der vom [Confidential Computing Consortium](https://confidentialcomputing.io/) (CCC) definiert wurde. Diese Vereinigung hat es sich zur Aufgabe gemacht, Definitionen für die Einführung des Confidential Computing festzulegen und die Nutzung zu fördern. Das CCC definiert Confidential Computing wie folgt: Der Schutz verwendeter Daten durch Ausführen von Berechnungen in einer hardwarebasierten vertrauenswürdigen Ausführungsumgebung (Trusted Execution Environment, TEE).

Eine TEE ist eine Umgebung, mit der die ausschließliche Ausführung von autorisiertem Code erzwungen wird. Für alle Daten in der TEE ist es nicht möglich, dass sie mit Code, der sich außerhalb dieser Umgebung befindet, gelesen oder manipuliert werden. 

### <a name="lessen-the-need-for-trust"></a>Weniger Vertrauen erforderlich
Das Ausführen von Workloads in der Cloud erfordert Vertrauen. Sie bringen dieses Vertrauen mehreren Anbietern entgegen, um die Verwendung verschiedener Komponenten Ihrer Anwendung zu ermöglichen.


**App-Softwarehersteller:** Sie können Software vertrauen, indem Sie Anwendungssoftware lokal, unter Verwendung von Open-Source-Lösungen oder durch interne Entwicklung bereitstellen.

**Hardwareanbieter:** Sie können Hardware vertrauen, indem Sie lokale oder interne Hardware verwenden. 

**Infrastrukturanbieter:** Sie können Cloudanbietern vertrauen oder Ihre eigenen lokalen Rechenzentren verwalten.

Azure Confidential Computing macht es einfacher, dem Cloudanbieter zu vertrauen, da sie ihm bei verschiedenen Aspekten der Compute-Cloudinfrastruktur weniger Vertrauen entgegenbringen müssen. Azure Confidential Computing minimiert das notwendige Vertrauen für den Betriebssystemkernel des Hosts, für den Hypervisor, für den VM-Administrator und für den Hostadministrator.

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über alle Confidential Computing-Produkte in Azure.

> [!div class="nextstepaction"]
> [Übersicht über Azure Confidential Computing-Dienste](overview-azure-products.md)
