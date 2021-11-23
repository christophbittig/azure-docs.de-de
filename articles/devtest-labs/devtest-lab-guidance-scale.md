---
title: Zentrales Hochskalieren Ihrer Azure DevTest Labs-Infrastruktur
description: Hier finden Sie Informationen und Leitfäden zum Hochskalieren Ihrer Azure DevTest Labs-Infrastruktur.
ms.topic: how-to
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 02019d65424acde2459dc24317dd607168ff2934
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398552"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Zentrales Hochskalieren Ihrer Azure DevTest Labs-Infrastruktur
Vor der Implementierung von DevTest Labs auf Unternehmensebene sind mehrere wichtige Entscheidungspunkte zu berücksichtigen. Diese Entscheidungspunkte auf hoher Ebene zu verstehen, hilft Organisationen bei zukünftigen Entwurfsentscheidungen. Sie sollten jedoch eine Organisation nicht daran hindern, einen Proof of Concept zu starten. Die obersten drei Bereiche für die anfängliche Planung der Hochskalierung sind:

- Netzwerke und Sicherheit
- Abonnementtopologie
- Rollen und Zuständigkeiten

## <a name="networking-and-security"></a>Netzwerke und Sicherheit
Netzwerke und Sicherheit sind die Stützpfeiler aller Organisationen. Während für eine unternehmensweite Bereitstellung eine erheblich genauere Analyse erforderlich ist, gibt es weniger Anforderungen für einen erfolgreichen Proof of Concept. Zu den wichtigen Schwerpunkten zählen:

- **Azure-Abonnement:** Zum Bereitstellen von DevTest Labs benötigen Sie Zugriff auf ein Azure-Abonnement mit ausreichenden Berechtigungen zum Erstellen von Ressourcen. Es gibt mehrere Möglichkeiten, Zugriff auf Azure-Abonnements zu erhalten, etwa ein Enterprise Agreement oder die nutzungsbasierte Bezahlung. Weitere Informationen zum Erhalten des Zugriffs auf ein Azure-Abonnement finden Sie unter [Lizenzierung von Azure für das Unternehmen](https://azure.microsoft.com/pricing/enterprise-agreement/).
- **Zugriff auf lokale Ressourcen:** Bei einigen Organisationen müssen die Ressourcen in DevTest Labs Zugriff auf lokale Ressourcen haben. Sie benötigen eine sichere Verbindung aus Ihrer lokalen Umgebung mit Azure. Es ist wichtig, vorab entweder eine VPN-Verbindung oder eine Azure ExpressRoute-Leitung einzurichten und zu konfigurieren. Weitere Informationen finden Sie unter [Übersicht über virtuelle Netzwerke](../virtual-network/virtual-networks-overview.md).
- **Weitere Sicherheitsanforderungen:** Andere Sicherheitsanforderungen, z. B. Computerrichtlinien, Zugriff auf öffentliche IP-Adressen, Verbindung mit dem Internet, sind Szenarien, die möglicherweise vor dem Implementieren eines Proof of Concept überprüft werden müssen. 

## <a name="subscription-topology"></a>Abonnementtopologie
Die Abonnementtopologie ist ein wichtiger Entwurfsaspekt für die Bereitstellung von DevTest Labs in Unternehmen. Die Entscheidungen müssen jedoch erst nach Abschluss eines Proof of Concept endgültig festgelegt werden. Bei der Auswertung der erforderlichen Anzahl von Abonnements für eine Unternehmensimplementierung gibt es zwei Extreme: 

- Ein Abonnement für die gesamte Organisation
- Ein Abonnement pro Benutzer

Als Nächstes werden die Vorteile der beiden Ansätze genauer erläutert.

### <a name="one-subscription"></a>Ein Abonnement
Häufig ist der Ansatz, nur ein Abonnement zu verwenden, in einem großen Unternehmen nicht zu bewältigen. Das Beschränken der Anzahl von Abonnements bietet jedoch die folgenden Vorteile:

- Die **Prognosekosten** für das Unternehmen.  Die Budgetzuweisung ist mit einem Abonnement viel einfacher, da sich alle Ressourcen in einem einzelnen Pool befinden. Dieser Ansatz ermöglicht eine einfachere Entscheidungsfindung zur Umsetzung von Kostenkontrollmaßnahmen zu einem beliebigen Zeitpunkt während eines Abrechnungszyklus.
- Die **Verwaltbarkeit** von VMs, Artefakten, Formeln, Netzwerkkonfigurationen, Berechtigungen und Richtlinien ist einfacher, da alle Aktualisierungen nur in einem und nicht in vielen Abonnements vorgenommen werden müssen.
- Der **Netzwerkaufwand** verringert sich bei einem einzelnen Abonnement für Unternehmen, bei denen lokale Konnektivität eine Voraussetzung ist. Es ist notwendig, virtuelle Netzwerke über Abonnements hinweg zu verbinden (Hub-Spoke-Modell), und dies erfordert bei weiteren Abonnements zusätzliche Konfiguration, Verwaltung, IP-Adressbereiche usw.
- Die **Zusammenarbeit im Team** ist einfacher, wenn alle im selben Abonnement arbeiten. Beispielsweise ist es einfacher, Kolleg*innen eine VM erneut zuzuweisen oder Teamressourcen zu teilen.

### <a name="subscription-per-user"></a>Ein Abonnement pro Benutzer
Ein separates Abonnement pro Benutzer bietet gleiche Möglichkeiten in einem alternativen Spektrum. Die Vorteile der Nutzung mehrerer Abonnements sind etwa:

- Die Umsetzung wird nicht durch **Azure-Skalierungskontingente** beeinträchtigt. Zum Zeitpunkt dieses Artikels lässt Azure beispielsweise 200 Speicherkonten pro Abonnement zu. Für die meisten Dienste in Azure bestehen operative Kontingente (viele können angepasst werden, einige nicht). Bei diesem Modell mit einem Abonnement pro Benutzer*in ist es bei den meisten Kontingenten sehr unwahrscheinlich, dass sie erreicht werden. Weitere Informationen zu aktuellen Azure-Skalierungskontingenten finden Sie unter [Grenzwerte für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Die **verbrauchsbasierte Kostenzuteilung** für Gruppen oder einzelne Entwickler ist wesentlich einfacher, sodass Organisationen Kosten mit ihrem aktuellen Modell einplanen können.
- **Besitz und Berechtigungen** sind in DevTest Labs-Umgebungen einfach. Sie gewähren Entwicklern Zugriff auf Abonnementebene, und diese sind dann uneingeschränkt für alle Elemente verantwortlich, einschließlich Netzwerkkonfiguration, Labrichtlinien und VM-Verwaltung.

In Unternehmen gibt es möglicherweise zahlreiche Einschränkungen für die Extreme des Spektrums. Daher müssen Sie Abonnements auf eine Weise einrichten, die in der Mitte zwischen diesen Extremen liegt. Als bewährte Methode sollte das Ziel einer Organisation die Verwendung von möglichst wenigen Abonnements sein. Beachten Sie dabei Funktionen, die das Erhöhen der Gesamtanzahl der Abonnements erzwingen. Zusammenfassend gilt: Die Abonnementtopologie ist wichtig für eine Unternehmensbereitstellung von DevTest Labs, sollte jedoch einen Proof of Concept nicht hinauszögern. Im Artikel [Governance](devtest-lab-guidance-governance-policy-compliance.md) finden Sie weitere Details zur Entscheidung über die Granularität für das Abonnement und das Lab innerhalb der Organisation.

## <a name="roles-and-responsibilities"></a>Rollen und Zuständigkeiten
Ein Proof of Concept für DevTest-Labs weist drei primäre Rollen mit definierten Aufgaben auf: Abonnementbesitzer, DevTest Labs-Besitzer, DevTest Labs-Benutzer – und optional einen Mitwirkenden.

- **Abonnementbesitzer:** Der Besitzer des Abonnements verfügt über Rechte zum Verwalten eines Azure-Abonnements, einschließlich der Zuweisung von Benutzer*innen, der Verwaltung von Richtlinien, der Erstellung und Verwaltung der Netzwerktopologie und der Anforderung von Kontingenterhöhungen. [hier finden Sie weitere Informationen](../role-based-access-control/rbac-and-directory-admin-roles.md)
- **DevTest Labs-Besitzer:** Der DevTest Labs-Besitzer hat vollen administrativen Zugriff auf das Lab. Diese Person ist verantwortlich für das Hinzufügen/Entfernen von Benutzern, das Verwalten der Kosteneinstellungen, die allgemeinen Lab-Einstellungen und andere Aufgaben bezüglich virtueller Computer und Artefakte. Ein Labbesitzer verfügt auch über alle Rechte von DevTest Labs-Benutzern.
- **DevTest Labs-Benutzer:** Der DevTest Labs-Benutzer kann die virtuellen Computer im Lab erstellen und nutzen. Diese Personen erfüllen einige minimale Verwaltungsfunktionen auf virtuellen Computern, die sie erstellen (Starten/Beenden/Löschen/Konfigurieren ihrer virtuellen Computer). Die Benutzer können keine VMs von anderen Benutzern verwalten.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie im nächsten Artikel dieser Serie: [Orchestrieren der Implementierung von Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md).