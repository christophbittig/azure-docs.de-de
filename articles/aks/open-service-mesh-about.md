---
title: Bereitstellen von Open Service Mesh
description: Öffnen des Dienst-Mesh (OSM) in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: d03aead2056ef3394c35f5e9679edd28e47e3fd0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066898"
---
# <a name="open-service-mesh-aks-add-on"></a>Open Service Mesh AKS-Add-On

[Öffnen des Dienst-Mesh (OSM)](https://docs.openservicemesh.io/) ist ein schlankes, erweiterbares, cloudbasiertes Dienst-Mesh, mit dem Benutzer die Standardfunktionen für die Wahrnehmbarkeit für sehr dynamische Microservice-Umgebungen einheitlich verwalten, sichern und erhalten können.

OSM führt eine Envoy-basierte Kontrollebene auf Kubernetes aus, kann mit [SMI](https://smi-spec.io/)-APIs konfiguriert werden und funktioniert, indem ein Envoy-Proxy als Sidecar-Container neben jeder Instanz Ihrer Anwendung injiziert wird. Der Envoy-Proxy enthält Regeln für Zugriffskontrollrichtlinien und führt diese aus, implementiert die Routing-Konfiguration und erfasst Metriken. Die Steuerebene konfiguriert die Proxys kontinuierlich, um sicherzustellen, dass die Richtlinien und Routing-Regeln auf dem neuesten Stand sind und dass die Proxys in Ordnung sind.

Das OSM-Projekt stammt von Microsoft. Es wurde an [Cloud Native Computing Foundation (CNCF)](https://www.cncf.io/) übergeben und wird seitdem von CNCF verwaltet. Das OSM-Open-Source-Projekt ist und bleibt eine Community-basierte Zusammenarbeit rund um Features und Funktionen, und Beiträge zum Projekt sind jederzeit willkommen. Informationen dazu, wie Sie sich beteiligen können, finden Sie in unserem Leitfaden für [Mitwirkende](https://github.com/openservicemesh/osm/blob/main/CONTRIBUTOR_LADDER.md).

## <a name="capabilities-and-features"></a>Funktionen und Features

OSM bietet die folgenden Fähigkeiten und Funktionen, um ein Cloud-natives Service-Mesh für Ihre Azure Kubernetes Service (AKS)-Cluster bereitzustellen:

- OSM wurde in den AKS-Dienst integriert, um ein vollständig unterstütztes und verwaltetes Service Mesh mit dem Komfort des AKS-Feature-Add-Ons bereitzustellen.

- Sichere Kommunikation von Dienst zu Dienst durch Aktivierung von mTLS

- Einfaches Onboarding von Anwendungen auf das Mesh durch Aktivierung der automatischen Sidecar-Injektion des Envoy-Proxys

- Einfache und transparente Konfigurationen für die Verlagerung des Datenverkehrs auf Bereitstellungen

- Fähigkeit, feinkörnige Zugriffskontrollrichtlinien für Dienste zu definieren und auszuführen

- Beobachtbarkeit und Einblicke in Anwendungsmetriken zur Fehlersuche und Überwachung von Diensten

- Integration mit externen Zertifikatsverwaltungsdiensten/-lösungen mit einer steckbaren Schnittstelle

## <a name="scenarios"></a>Szenarien

OSM kann Ihre AKS-Bereitstellungen in den folgenden Szenarien unterstützen:

- Verschlüsselte Kommunikation zwischen den im Cluster bereitgestellten Service-Endpunkten

- Verkehrsautorisierung von sowohl HTTP/HTTPS- als auch TCP-Verkehr im Netz

- Konfiguration von gewichteten Verkehrskontrollen zwischen zwei oder mehr Diensten für A/B- oder Canary-Bereitstellungen

- Erfassung und Anzeige von KPIs aus dem Anwendungsverkehr

## <a name="osm-service-quotas-and-limits-preview"></a>OSM-Dienst-Kontingente und-Grenzwerte (Vorschau)

Einschränkungen für die OSM-Vorschau für Dienst-Kontingente und-Grenzwerte finden Sie auf der [Seite AKS-Kontingente und regionale Grenzwerte](./quotas-skus-regions.md).

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register
