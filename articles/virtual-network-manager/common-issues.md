---
title: Häufig auftretende Probleme mit Azure Virtual Network Manager (Vorschau)
description: Erfahren Sie mehr über häufig auftretende Probleme bei der Verwendung von Azure Virtual Network Manager.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: bcf66883ba766189215fd4ce267391c9358b10f9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131094830"
---
# <a name="common-issues-seen-with-azure-virtual-network-manager-preview"></a>Häufig auftretende Probleme mit Azure Virtual Network Manager (Vorschau)

In diesem Artikel werden häufige Probleme behandelt, die bei der Verwendung von Azure Virtual Network Manager auftreten können, und einige mögliche Lösungen bereitgestellt.

## <a name="why-isnt-my-configuration-getting-applied"></a>Warum wird meine Konfiguration nicht angewendet? 

**Häufige Gründe dafür, dass Konfigurationen nicht angewendet werden:** 

* Die Konfiguration wird nicht in den Regionen bereitgestellt, in denen sich virtuelle Netzwerke befinden. 

* Sie haben die Konfiguration noch nicht bereitgestellt. Sie müssen die Konfiguration bereitstellen, damit sie wirksam wird. 

* Die Konfiguration hatte nicht genügend Zeit, um wirksam zu werden. Es dauert etwa 15 bis 20 Minuten, bis die Konfiguration angewendet wird, nachdem Sie sie committet haben. Wenn ein Aktualisierung Ihrer Netzwerkgruppenmitgliedschaft erfolgt, dauert es ungefähr 10 Minuten, bis die Änderungen wirksam werden. 

## <a name="i-updated-my-configuration-but-the-changes-arent-reflected-in-my-environment"></a>Ich habe meine Konfiguration aktualisiert, aber die Änderungen werden in meiner Umgebung nicht wirksam. 

Sie müssen die neue Konfiguration bereitstellen, nachdem die Konfiguration geändert wurde. 

## <a name="why-is-my-connectivity-configuration-not-working"></a>Warum funktioniert meine Konnektivitätskonfiguration nicht? 

**Sie müssen die folgenden Punkte berücksichtigen:** 

* Wenn Sie in einer Hub-and-Spoke-Topologie die Option aktivieren, den *Hub als Gateway zu verwenden*, benötigen Sie ein Gateway im virtuellen Hubnetzwerk. Andernfalls schlägt die Erstellung des Peerings virtueller Netzwerke zwischen dem Hub und den virtuellen Spokenetzwerken fehl. 

* Wenn Mitglieder in der Netzwerkgruppe regionsübergreifend in einer Hub-and-Spoke-Topologiekonfiguration miteinander kommunizieren sollen, müssen Sie die globale Meshoption aktivieren. 

## <a name="next-steps"></a>Nächste Schritte

Antworten auf häufig gestellte Fragen finden Sie unter [Häufig gestellte Fragen zu Azure Virtual Network Manager](faq.md).
