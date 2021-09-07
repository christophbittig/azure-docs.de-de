---
title: Beheben von Problemen mit der Registrierungsleistung
description: Symptome, Ursachen und Lösung von häufigen Problemen mit der Leistung einer Registrierung.
ms.topic: article
ms.date: 08/11/2020
ms.openlocfilehash: 4a93df09c8ceb587815227c073ec69dcd1205451
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122338864"
---
# <a name="troubleshoot-registry-performance"></a>Beheben von Problemen mit der Registrierungsleistung

Dieser Artikel hilft Ihnen bei der Behebung von Problemen, die bei der Leistung einer Azure Container Registry auftreten können. 

## <a name="symptoms"></a>Symptome

Mindestens eines der folgenden Probleme kann ein Symptom sein:

* Das Pullen oder Pushen von Images mit der DockerCLI dauern länger als erwartet.
* Die Bereitstellung von Images in einem Dienst wie Azure Kubernetes Service dauert länger als erwartet.
* Sie können eine große Anzahl von gleichzeitigen Pull- oder Pushvorgängen nicht in der erwarteten Zeit abschließen.
* Möglicherweise wird ein HTTP 429-Fehler ähnlich dem folgenden angezeigt: `Too many requests`
* Pull- oder Pushvorgänge in einer georeplizierten Registrierung dauern länger als erwartet, oder ein Push schlägt mit dem Fehler `Error writing blob` oder `Error writing manifest` fehl.

## <a name="causes"></a>Ursachen

* Die Netzwerkverbindungsgeschwindigkeit kann Registrierungsvorgänge verlangsamen: [Lösung](#check-expected-network-speed).
* Die Komprimierung oder Extraktion der Imageschicht ist möglicherweise auf dem Client langsam: [Lösung](#check-client-hardware).  
* Sie erreichen ein konfiguriertes Limit in der Registrierungsdienstebene oder der Umgebung: [Lösung](#review-configured-limits).
* Ihre georeplizierte Registrierung verfügt über Replikate in Regionen in der Nähe: [Lösung](#configure-geo-replicated-registry).
* Sie pullen aus einem geografisch entfernten Registrierungsreplikat: [Lösung](#configure-dns-for-geo-replicated-registry).

Wenn Sie Ihr Problem hier nicht beheben können, finden Sie weitere Informationen unter [Erweiterte Problembehandlung](#advanced-troubleshooting) und [Nächste Schritte](#next-steps).

## <a name="potential-solutions"></a>Mögliche Lösungen

### <a name="check-expected-network-speed"></a>Überprüfen der erwarteten Netzwerkgeschwindigkeit

Überprüfen Sie die Internetupload- und -downloadgeschwindigkeit, oder verwenden Sie ein Tool wie AzureSpeed, um den [Upload](https://www.azurespeed.com/Azure/Uploadß) und [Download](https://www.azurespeed.com/Azure/Download) aus Azure Blob Storage zu testen, der Registrierungsimageebenen hostet.

Überprüfen Sie die Imagegröße im Hinblick auf die maximal unterstützte Größe und die unterstützte Download- oder Uploadbandbreite für die Registrierungsdienstebene. Wenn sich Ihre Registrierung im Basic- oder Standard-Tarif befindet, sollten Sie ein Upgrade in Erwägung ziehen, um die Leistung zu verbessern. 

Überprüfen Sie für die Imagebereitstellung für andere Dienste die Regionen, in denen sich die Registrierung und das Ziel befinden. Es empfiehlt sich, die Registrierung und das Bereitstellungsziel in derselben Region oder in netzwerknahen Regionen zu platzieren, um die Leistung zu verbessern.

Verwandte Links:

* [Azure Container Registry-Tarife](container-registry-skus.md)    
* [Häufig gestellte Fragen (FAQ) zur Containerregistrierung](container-registry-faq.yml)
* [Skalierbarkeits- und Leistungsziele für Azure Blob Storage](../storage/blobs/scalability-targets.md)

### <a name="check-client-hardware"></a>Überprüfen der Clienthardware

Der Datenträgertyp und die CPU auf dem Docker-Client können sich auf die Geschwindigkeit auswirken, mit der Imageebenen auf dem Client als Teil von Pull- oder Pushvorgängen extrahiert oder komprimiert werden. Die Ebenenextraktion auf einem Festplattenlaufwerk nimmt z. B. mehr Zeit in Anspruch als auf einem Festkörperlaufwerk. Vergleichen Sie Pullvorgänge für vergleichbare Images aus Ihrer Azure Container Registry und einer öffentlichen Registrierung, z. B. aus Docker Hub.

### <a name="review-configured-limits"></a>Überprüfen konfigurierter Einstellungen

Wenn Sie gleichzeitig mehrere oder zahlreiche mehrschichtige Images aus Ihrer Registrierung pushen oder in sie pullen, überprüfen Sie die unterstützten ReadOps- und WriteOps-Grenzwerte für die Registrierungsdienstebene. Wenn sich Ihre Registrierung im Basic- oder Standard-Tarif befindet, sollten Sie ein Upgrade in Erwägung ziehen, um diese Grenzwerte zu erhöhen. Informieren Sie sich auch bei Ihrem Netzwerkanbieter über Netzwerkdrosselung, die bei vielen gleichzeitigen Vorgängen auftreten kann. 

Überprüfen Sie die Konfiguration des Docker-Daemons auf die maximale Anzahl gleichzeitiger Uploads oder Downloads für jeden Push- oder Pullvorgang auf dem Client. Konfigurieren Sie bei Bedarf höhere Grenzwerte.

Da jede Bildebene einen separaten Registrierungslese- oder -schreibvorgang erfordert, überprüfen Sie die Anzahl der Ebenen in Ihren Images. Erwägen Sie Strategien zur Verringerung der Anzahl der Imageebenen.

Verwandte Links:

* [Azure Container Registry-Tarife](container-registry-skus.md)
* [dockerd](https://docs.docker.com/engine/reference/commandline/dockerd/)

### <a name="configure-geo-replicated-registry"></a>Konfigurieren einer georeplizierten Registrierung

Ein Docker-Client, der ein Image in eine georeplizierte Registrierung pusht, pusht möglicherweise nicht alle Imageebenen und das Manifest in eine einzige replizierte Region. Dies kann der Fall sein, wenn Azure Traffic Manager Registrierungsanforderungen an die dem Netzwerk nächstgelegene replizierte Registrierung weiterleitet. Wenn sich die Registrierung in der Nähe von zwei Replikationsregionen befindet, können Imageebenen und das Manifest an die beiden Standorte verteilt werden, und der Pushvorgang schlägt beim Überprüfen des Manifests fehl.

Um die DNS-Auflösung beim Pushen von Images auf das nächstgelegene Replikat zu optimieren, konfigurieren Sie eine georeplizierte Registrierung in denselben Azure-Regionen wie die Quelle der Pushvorgänge oder die nächstgelegene Region, wenn Sie außerhalb von Azure arbeiten.

Zur Problembehandlung von Vorgängen mit einer georeplizierten Registrierung können Sie das Traffic Manager-Routing für mindestens eine Replikation vorrübergehend deaktivieren.

Verwandte Links:

* [Georeplikation in Azure Container Registry](container-registry-geo-replication.md)

### <a name="configure-dns-for-geo-replicated-registry"></a>Konfigurieren von DNS für eine georeplizierte Registrierung

Wenn Pullvorgänge aus einer georeplizierten Registrierung langsam erscheinen, wird die DNS-Konfiguration auf dem Client möglicherweise zu einem geografisch entfernten DNS-Server aufgelöst. In diesem Fall leitet Traffic Manager Anforderungen ggf. an ein Replikat weiter, das sich im Netzwerk in der Nähe des DNS-Servers befindet, aber weit vom Client entfernt ist. Führen Sie ein Tool wie `nslookup` oder `dig` (unter Linux) aus, um das Replikat zu ermitteln, an das Traffic Manager die Registrierungsanforderungen weiterleitet. Beispiel:

```console
nslookup myregistry.azurecr.io
```

Eine mögliche Lösung besteht darin, einen näheren DNS-Server zu konfigurieren.

Verwandte Links:

* [Georeplikation in Azure Container Registry](container-registry-geo-replication.md)
* [Problembehandlung bei Pushvorgängen mit georeplizierten Registrierungen](container-registry-geo-replication.md#troubleshoot-push-operations-with-geo-replicated-registries)
* [Vorübergehendes Deaktivieren des Routings zur Replikation](container-registry-geo-replication.md#temporarily-disable-routing-to-replication)
* [Häufig gestellte Fragen (FAQ) zu Traffic Manager](../traffic-manager/traffic-manager-faqs.md)

### <a name="advanced-troubleshooting"></a>Erweiterte Problembehandlung

Wenn Ihre Berechtigungen für Registrierungsressourcen dies zulassen, [überprüfen Sie die Integrität der Registrierungsumgebung](container-registry-check-health.md). Wenn Fehler gemeldet werden, überprüfen Sie die [Fehlerreferenz](container-registry-health-error-reference.md) auf mögliche Lösungen.

Wenn [Sammlung von Ressourcenprotokollen](monitor-service.md) in der Registrierung aktiviert ist, überprüfen Sie das Protokoll „ContainterRegistryRepositoryEvents“. Dieses Protokoll speichert Informationen zu Vorgängen wie Push- oder Pullereignissen. Fragen Sie das Protokoll auf [Vorgangsfehler auf Repositoryebene](monitor-service.md#repository-level-operation-failures) ab. 

Verwandte Links:

* [Protokolle für die Diagnoseauswertung und -überwachung](./monitor-service.md)
* [Häufig gestellte Fragen (FAQ) zur Containerregistrierung](container-registry-faq.yml)
* [Bewährte Methoden für Azure Container Registry](container-registry-best-practices.md)

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie Ihr Problem hier nicht lösen können, sehen Sie sich die folgenden Optionen an.

* Weitere Themen zur Problembehandlung der Registrierung sind:
  * [Beheben von Problemen mit der Registrierungsanmeldung](container-registry-troubleshoot-login.md)
  * [Beheben von Netzwerkproblemen mit der Registrierung](container-registry-troubleshoot-access.md)
* Optionen für [Support durch die Community](https://azure.microsoft.com/support/community/)
* [Microsoft Q&A (Fragen und Antworten)](/answers/products/)
* [Öffnen eines Supporttickets](https://azure.microsoft.com/support/create-ticket/)