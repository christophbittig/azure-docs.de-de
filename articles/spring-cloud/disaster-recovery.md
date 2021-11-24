---
title: Georedundante Notfallwiederherstellung in Azure Spring Cloud | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihre Spring Cloud-Anwendung vor regionalen Ausfällen schützen.
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 33ff5a9e2dcf0d4e8f62d38bc36811548f9d3b3c
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484825"
---
# <a name="azure-spring-cloud-disaster-recovery"></a>Notfallwiederherstellung in Azure Spring Cloud

**Dieser Artikel gilt für:** ✔️ Java ✔️ C#

In diesem Artikel werden einige Strategien erläutert, mit denen Sie Ihre Anwendungen in Azure Spring Cloud vor Ausfallzeiten schützen können.  In jeder Region und jedem Rechenzentrum können Ausfallzeiten aufgrund von regionalen Ausfällen auftreten, durch eine sorgfältige Planung können jedoch die Auswirkungen auf Ihre Kunden abgeschwächt werden.

## <a name="plan-your-application-deployment"></a>Planen Ihrer Anwendungsbereitstellung

Anwendungen in Azure Spring Cloud werden in einer bestimmten Region ausgeführt.  Azure betreibt Datencenter in mehreren Gebieten auf der ganzen Erde. Bei einem Azure-Gebiet handelt es sich um einen definierten Bereich der Erde, der mindestens eine Azure-Region enthält. Eine Azure-Region ist ein Bereich in einem Gebiet mit mindestens einem Rechenzentrum.  Jeder Azure-Region ist innerhalb des gleichen geografischen Gebiets eine andere Region als Regionspartner zugeordnet. In Azure werden Plattformupdates (geplante Wartung) für Regionspaare serialisiert, um sicherzustellen, dass immer nur jeweils eine Region pro Paar aktualisiert wird. Bei einem Ausfall, der sich auf mehrere Regionen auswirkt, wird mindestens eine Region in jedem Paar für die Wiederherstellung priorisiert.

Zur Sicherstellung von Hochverfügbarkeit und Schutz vor Ausfällen müssen Sie Ihre Spring Cloud-Anwendungen in mehreren Regionen bereitstellen.  Azure umfasst eine Liste mit [Regionspaaren](../best-practices-availability-paired-regions.md), sodass Sie Spring Cloud-Bereitstellungen mit Regionspaaren planen können.  Es empfiehlt sich, beim Entwurf Ihrer Microservicearchitektur drei wichtige Faktoren zu berücksichtigen: regionale Verfügbarkeit, Azure-Regionspaare und Dienstverfügbarkeit.

* Regionale Verfügbarkeit:  Wählen Sie einen geografischen Bereich in der Nähe Ihrer Benutzer aus, um die Netzwerkverzögerung und Übertragungszeit zu minimieren.
* Azure-Regionspaare:  Wählen Sie Regionspaare im ausgewählten geografischen Bereich aus, sodass koordinierte Plattformupdates und priorisierte Wiederherstellungsvorgänge bei Bedarf sichergestellt sind.
* Dienstverfügbarkeit:   Legen Sie fest, ob die Regionspaare heiß/heiß, heiß/warm oder heiß/kalt sein sollen.

## <a name="use-azure-traffic-manager-to-route-traffic"></a>Weiterleiten von Datenverkehr mit Azure Traffic Manager

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) gewährleistet einen DNS-basierten Lastausgleich des Datenverkehrs und kann Netzwerkdatenverkehr auf mehrere Regionen verteilen.  Verwenden Sie Azure Traffic Manager, um Kunden an die nächstgelegene Azure Spring Cloud-Dienstinstanz weiterzuleiten.  Optimale Leistungs- und Redundanzwerte werden erzielt, indem Sie den gesamten Anwendungsdatenverkehr über Azure Traffic Manager leiten, bevor er an den Azure Spring Cloud-Dienst gesendet wird.

Wenn Sie Anwendungen in Azure Spring Cloud in mehreren Regionen ausführen, verwenden Sie Azure Traffic Manager, um den Verkehrsfluss zu Ihren Anwendungen in jeder Region zu steuern.  Definieren Sie einen Azure Traffic Manager-Endpunkt für jeden Dienst unter Verwendung der Dienst-IP-Adresse. Kunden sollten eine Verbindung mit einem DNS-Namen in Azure Traffic Manager herstellen, der auf den Azure Spring Cloud-Dienst verweist.  Mit Azure Traffic Manager wird ein Lastausgleich des Datenverkehrs über die definierten Endpunkte vorgenommen.  Bei einem Ausfall eines Rechenzentrums leitet Azure Traffic Manager den Datenverkehr von dieser Region an die zugehörige andere Region des Regionspaars weiter, um die Dienstkontinuität sicherzustellen.

## <a name="create-azure-traffic-manager-for-azure-spring-cloud"></a>Erstellen von Azure Traffic Manager für Azure Spring Cloud

1. Erstellen Sie Azure Spring Cloud in zwei unterschiedlichen Regionen.
Sie benötigen zwei Dienstinstanzen von Azure Spring Cloud, die in zwei verschiedenen Regionen bereitgestellt werden („USA, Osten“ und „Europa, Westen“). Starten Sie eine vorhandene Anwendung in Azure Spring Cloud über das Azure-Portal, um zwei Dienstinstanzen zu erstellen. Jede dient jeweils als primärer bzw. Failoverendpunkt für den Datenverkehr.

**Info zu den beiden Dienstinstanzen**:

| Service Name | Standort | Application |
|--|--|--|
| service-sample-a | East US | gateway / auth-service / account-service |
| service-sample-b | Europa, Westen | gateway / auth-service / account-service |

2. Richten Sie eine benutzerdefinierten Domäne für den Dienst ein. Befolgen Sie dazu die Anweisungen im [Dokument zu benutzerdefinierten Domänen](./tutorial-custom-domain.md), um eine benutzerdefinierte Domäne für diese beiden vorhandenen Dienstinstanzen einzurichten. Nach der erfolgreichen Einrichtung werden beide Dienstinstanzen an die benutzerdefinierte Domäne „bcdr-test.contoso.com“ gebunden.

3. Erstellen Sie einen Traffic Manager und zwei Endpunkte: [Erstellen Sie ein Traffic Manager-Profil über das Azure-Portal](../traffic-manager/quickstart-create-traffic-manager-profile.md).

Im Folgenden das Traffic Manager-Profil:
* Traffic Manager-DNS-Name: `http://asc-bcdr.trafficmanager.net`
* Endpunktprofile:

| Profil | type | Ziel | Priority | Benutzerdefinierte Headereinstellungen |
|--|--|--|--|--|
| Endpunkt für das Profil A | Externer Endpunkt | service-sample-a.asc-test.net | 1 | host: bcdr-test.contoso.com |
| Endpunkt für das Profil B | Externer Endpunkt | service-sample-b.asc-test.net | 2 | host: bcdr-test.contoso.com |

4. Erstellen Sie einen CNAME-Eintrag in der DNS-Zone: bcdr-test.contoso.com CNAME asc-bcdr.trafficmanager.net.

5. Nun ist die Umgebung vollständig eingerichtet. Kunden sollten in der Lage sein, über „bcdr-test.contoso.com“ auf die App zuzugreifen.

## <a name="next-steps"></a>Nächste Schritte

* [Schnellstart: Bereitstellen Ihrer ersten Spring Boot-App in Azure Spring Cloud](./quickstart.md)
