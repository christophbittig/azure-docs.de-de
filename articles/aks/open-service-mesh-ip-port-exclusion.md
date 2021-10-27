---
title: Ausschluss von IP- und Portbereichen
description: Ausschluss von IP- und Portbereichen implementieren
services: container-service
ms.topic: article
ms.date: 10/8/2021
ms.custom: mvc, devx-track-azurecli
ms.author: kochhars
ms.openlocfilehash: b12ec518aa46800778973524b4302ebed083d38f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "130007263"
---
# <a name="implement-ip-and-span-classx-x-first-x-lastport-range-exclusionspan"></a><span class="x x-first x-last">Ausschluss von IP- und Portbereichen</span> implementieren

Ausgehender TCP-basierter Datenverkehr von Anwendungen wird standardmäßig mithilfe der von OSM programmierten `iptables`-Regeln abgefangen und an den Envoy-Proxy-Sidecar umgeleitet. OSM bietet eine Möglichkeit, eine Liste von IP-Adressbereichen und Ports anzugeben, die bei Bedarf von der Abfangen des Datenverkehrs ausgeschlossen werden sollen. Anleitungen zum Ausschließen von IP- und Portbereichen finden Sie [in dieser Dokumentation.](https://release-v0-11.docs.openservicemesh.io/docs/guides/traffic_management/iptables_redirection/) 

**Beachten Sie, dass der Portausschluss für das Open Service Mesh-AKS-Add-On nur nach der Installation mit `kubectl patch` und nicht während der Installation mithilfe des OSM-CLI-Flags implementiert werden `--set` kann.**

> [!NOTE]
> Wenn die Anwendungspods, die Teil des Gitternetzes sind, Zugriff auf IMDS, Azure DNS oder den Kubernetes-API-Server benötigen, muss der Benutzer diese IP-Adressen mit dem obigen Befehl explizit der Liste der globalen ausgehenden IP-Adressbereiche hinzufügen. Ein Beispiel für den Ausschluss von Kubernetes API-Serverports finden Sie [hier.](https://release-v0-11.docs.openservicemesh.io/docs/guides/app_onboarding/#onboard-services)
