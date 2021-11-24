---
title: Lastenausgleich für Azure Virtual Desktop-Hostpools – Azure
description: Erfahren Sie mehr über die Algorithmen zum Lastausgleich von Hostpools in einer Azure Virtual Desktop-Umgebung.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: f2b73879183bf3d85588c17a9b24bf42d649c06f
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277237"
---
# <a name="host-pool-load-balancing-algorithms"></a>Algorithmen für den Lastausgleich von Host-Pools

>[!IMPORTANT]
>Dieser Inhalt gilt für Azure Virtual Desktop mit Azure Virtual Desktop-Objekten für Azure Resource Manager. Wenn Sie Azure Virtual Desktop (klassisch) ohne Azure Resource Manager-Objekte verwenden, finden Sie weitere Informationen in [diesem Artikel](./virtual-desktop-fall-2019/host-pool-load-balancing-2019.md).

Azure Virtual Desktop unterstützt zwei Lastausgleichsalgorithmen. Jeder Algorithmus bestimmt, welcher Sitzungshost die Sitzung eines Benutzers hostet, wenn dieser eine Verbindung zu einer Ressource in einem Hostpool herstellt.

Die folgenden Lastausgleichsalgorithmen sind in Azure Virtual Desktop verfügbar:

- Beim Lastenausgleich vom Typ „Breiter Ansatz“ können Sie Benutzersitzungen gleichmäßig auf die Sitzungshosts in einem Hostpool verteilen.
- Beim Lastenausgleich vom Typ „Tiefer Ansatz“ können Sie einen Sitzungshost mit Benutzersitzungen in einem Hostpool füllen. Sobald der erste Sitzungshost der Schwellenwert des Sitzungslimits erreicht, leitet der Lastenausgleich alle neuen Benutzerverbindungen an den nächsten Sitzungshost im Hostpool weiter, bis dessen Limit erreicht ist, usw.

Für jeden Hostpool kann nur jeweils eine Art von Lastenausgleich konfiguriert werden. Beide Lastausgleichsalgorithmen haben jedoch die folgenden Verhaltensweisen gemeinsam, unabhängig davon, in welchem Hostpool sie sich befinden:

- Wenn ein Benutzer bereits eine aktive oder getrennte Sitzung im Host-Pool hat und sich erneut anmeldet, leitet der Load Balancer ihn erfolgreich an den Sitzungshost mit seiner bestehenden Sitzung weiter. Dieses Verhalten gilt auch dann, wenn die Eigenschaft AllowNewConnections des Sitzungshosts auf False gesetzt ist (Abflussmodus ist aktiviert).
- Wenn ein Benutzer im Hostpool nicht bereits über eine Sitzung verfügt, werden Sitzungshosts, für die die AllowNewConnections-Eigenschaft auf „False“ festgelegt ist, vom Lastenausgleichsmodul während des Lastenausgleichs nicht berücksichtigt.

## <a name="breadth-first-load-balancing-algorithm"></a>Breadth-first-Lastausgleichsalgorithmus

Der Breadth-First-Lastausgleichsalgorithmus ermöglicht es Ihnen, Benutzersitzungen auf Sitzungshosts zu verteilen, um die Sitzungsleistung zu optimieren. Diese Methode ist ideal für Organisationen geeignet, die Benutzern, die eine Verbindung mit ihrer virtuellen Desktopumgebung im Pool herstellen, ein optimales Nutzungserlebnis bieten möchten.

Der Breadth-First-Algorithmus fragt zunächst Sitzungshosts ab, die neue Verbindungen zulassen. Der Algorithmus wählt dann einen Sitzungshost zufällig aus der Hälfte der Sitzungshosts mit der geringsten Anzahl von Sitzungen aus. Gibt es z. B. neun Computer mit 11, 12, 13, 14, 15, 16, 17, 18 und 19 Sitzungen, wird eine neue Sitzung, die Sie erstellen, nicht automatisch auf dem ersten Computer eingerichtet. Stattdessen kann sie auf einem der ersten fünf Computer mit der niedrigsten Anzahl von Sitzungen (11, 12, 13, 14, 15) eingerichtet werden.

## <a name="depth-first-load-balancing-algorithm"></a>Depth-First-Lastausgleichsalgorithmus

Der Depth-First-Lastausgleichsalgorithmus ermöglicht es Ihnen, jeweils einen Sitzungshost zu sättigen, um für Scale-Down-Szenarien zu optimieren. Dieser Algorithmus ist ideal für kostenbewusste Unternehmen, die die Anzahl der virtuellen Maschinen, die sie einem Host-Pool zugewiesen haben, genauer kontrollieren möchten.

Der Depth-First-Algorithmus fragt zunächst Sitzungshosts ab, die neue Verbindungen zulassen und deren maximales Sitzungslimit noch nicht überschritten ist. Der Algorithmus wählt dann den Sitzungshost mit der höchsten Anzahl von Sitzungen aus. Bei einem Gleichstand wählt der Algorithmus den ersten Sitzungshost in der Abfrage aus.

>[!IMPORTANT]
>Der Algorithmus für den tiefenorientierten Lastenausgleich verteilt Sitzungen auf der Grundlage des maximalen Grenzwerts für Hostsitzungen. Dieser Parameter ist erforderlich, wenn der tiefenorientierte Lastenausgleich verwendet wird. Zum Erzielen einer optimalen Benutzererfahrung sollten Sie darauf achten, den Parameter für den maximalen Grenzwert der Hostsitzungen auf eine Zahl festzulegen, die möglichst gut zu Ihrer Umgebung passt.
