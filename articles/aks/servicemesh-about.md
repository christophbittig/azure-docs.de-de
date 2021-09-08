---
title: Informationen zu Dienstnetzen
description: Verschaffen Sie sich einen Überblick über Dienstnetze, unterstützte Szenarien, Auswahlkriterien und die möglichen nächsten Schritte.
author: pgibson
ms.topic: article
ms.date: 07/29/2021
ms.author: pgibson
ms.openlocfilehash: b77ee6fb25f45b365003850627276c0ecd47c7b5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339782"
---
# <a name="about-service-meshes"></a>Informationen zu Dienstnetzen

Ein Dienstnetz verfügt für Ihre Workloads über Funktionen für die Bereiche Datenverkehrsverwaltung, Resilienz, Richtlinie, Sicherheit, sichere Identität und Einblick. Ihre Anwendung ist von diesen betriebsbezogenen Funktionen abgekoppelt, und sie werden vom Dienstnetz von der Anwendungsschicht herunter auf die Infrastrukturebene verschoben.

## <a name="scenarios"></a>Szenarien

Hier sind einige Szenarien angegeben, die Sie bei Verwendung eines Dienstnetzes für Ihre Workloads aktivieren können:

- **Verschlüsselung des gesamten Datenverkehrs im Cluster**: Aktivieren Sie gegenseitiges TLS (mTLS) zwischen angegebenen Diensten im Cluster. Dies kann auf den ein- und ausgehenden Datenverkehr im Umkreisnetzwerk erweitert werden. Es wird eine sichere Standardoption bereitgestellt, ohne dass Änderungen am Anwendungscode und der Infrastruktur erforderlich sind.

- **Canary und schrittweise Rollouts**: Geben Sie Bedingungen an, um einen Teil des Datenverkehrs an eine Reihe neuer Dienste im Cluster zu leiten. Nachdem der Test des Canary-Release erfolgreich abgeschlossen wurde, können Sie die bedingte Weiterleitung entfernen und den Prozentsatz des gesamten Datenverkehrs, der an den neuen Dienst gesendet wird, schrittweise erhöhen. Schließlich wird dann der gesamte Datenverkehr an den neuen Dienst geleitet.

- **Verwaltung und Bearbeitung des Datenverkehrs**: Erstellen Sie eine Richtlinie für einen Dienst, mit der für den gesamten Datenverkehr ein Ratenlimit auf eine Version eines Diensts von einem bestimmten Ursprungsort durchgesetzt wird. Sie können auch eine Richtlinie erstellen, mit der eine Wiederholungsstrategie auf Fehlerklassen zwischen angegebenen Diensten angewendet wird. Spiegeln Sie während einer Migration oder zum Debuggen von Problemen Livedatenverkehr auf neuen Versionen von Diensten. Fügen Sie in einer Testumgebung Fehler zwischen Diensten ein, um die Resilienz zu testen.

- **Einblick**: Verschaffen Sie sich einen Einblick in der Verbindung Ihrer Dienste mit dem Datenverkehr, der zwischen den Diensten ausgetauscht wird. Rufen Sie Metriken, Protokolle und Ablaufverfolgungen für den gesamten Datenverkehr im Cluster und für den Ein- und Ausgang ab. Fügen Sie Ihren Anwendungen Funktionen für die verteilte Ablaufverfolgung hinzu.

## <a name="selection-criteria"></a>Auswahlkriterien

Stellen Sie vor dem Auswählen eines Dienstnetzes sicher, dass Sie ausreichend mit Ihren Anforderungen und den Gründen für die Installation eines Dienstnetzes vertraut sind. Stellen Sie sich die folgenden Fragen:

- **Ist ein Eingangscontroller für meine Zwecke ausreichend?** - In einigen Fällen reicht es aus, eine Funktion wie A/B-Tests oder die Datenverkehrsaufteilung für eingehenden Datenverkehr zu verwenden, um das gewünschte Szenario zu unterstützen. Erhöhen Sie die Komplexität Ihrer Anwendung nicht, wenn sich daraus keine Vorteile ergeben.

- **Können meine Workloads und meine Umgebung den zusätzlichen Aufwand verarbeiten?** - Für alle herkömmlichen Komponenten, die zur Unterstützung des Dienstnetzes benötigt werden, sind zusätzliche Ressourcen in Bezug auf CPU und Arbeitsspeicher erforderlich. Darüber hinaus führen alle Proxys und die zugehörigen Richtlinienüberprüfungen für Ihren Datenverkehr zu mehr Latenz. Falls Sie über Workloads verfügen, die sehr empfindlich auf Latenz reagieren, oder falls die zusätzlichen Ressourcen für die Dienstnetzkomponenten nicht bereitgestellt werden können, sollten Sie ggf. eine andere Lösung erwägen.

- **Wird die Komplexität unnötig erhöht?** - Falls der Grund für die Installation eines Dienstnetzes die Erzielung einer Funktion ist, die nicht unbedingt von entscheidender Bedeutung für das Geschäft bzw. den Betrieb ist, sollten Sie abwägen, ob sich der Mehraufwand für Installation, Wartung und Konfiguration lohnt.

- **Kann für die Einführung ein inkrementeller Ansatz verwendet werden?** - Einige Dienstnetze, mit denen viele Funktionen bereitgestellt werden, können auch mit einem inkrementellen Ansatz eingeführt werden. Installieren Sie nur die Komponenten, die Sie benötigen, um den Erfolg sicherzustellen. Wenn Sie später dann Erfahrung gesammelt haben und zusätzliche Funktionen erforderlich sind, können Sie diese separat erkunden. Widerstehen Sie der Versuchung, *alles* gleich am Anfang zu installieren.

## <a name="next-steps"></a>Nächste Schritte

Machen Sie sich als Nächstes mit Open Service Mesh (OSM) auf Azure Kubernetes Service (AKS) vertraut:

> [!div class="nextstepaction"]
> [Weitere Informationen zu OSM][osm-about]

Sie können sich über die folgenden Dienstnetze auf Azure Kubernetes Service (AKS) auch in der umfassenden Projektdokumentation zu den einzelnen Diensten informieren:

- [Istio][istio]
- [Linkerd][linkerd]
- [Consul Connect][consul]

Unter dem folgenden Link erfahren Sie mehr über die Dienstnetzlandschaft, weitere verfügbare Dienstnetze, Tools und Compliance:

- [Layer5: Service Mesh Landscape][service-mesh-landscape]

Sie können sich auch die verschiedenen Standardisierungsinitiativen für Dienstnetze ansehen:

- [Service Mesh Interface (SMI)][smi]
- [Service Mesh Federation][smf]
- [Service Mesh Performance (SMP)][smp]


<!-- LINKS - external -->
[istio]: https://istio.io/latest/docs/setup/install/
[linkerd]: https://linkerd.io/getting-started/
[consul]: https://learn.hashicorp.com/tutorials/consul/service-mesh-deploy
[service-mesh-landscape]: https://layer5.io/service-mesh-landscape
[smi]: https://smi-spec.io/
[smf]: https://github.com/vmware/hamlet
[smp]: https://github.com/service-mesh-performance/service-mesh-performance

<!-- LINKS - internal -->
[osm-about]: ./servicemesh-osm-about.md
