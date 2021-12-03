---
title: Ausschluss von IP- und Portbereichen
description: Ausschluss von IP- und Portbereichen implementieren
services: container-service
ms.topic: article
ms.date: 10/8/2021
ms.custom: mvc, devx-track-azurecli
ms.author: kochhars
ms.openlocfilehash: b2f43fc1ce78a10079bc37cdf77097caaa208bd6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066708"
---
# <a name="implement-ip-and-span-classx-x-first-x-lastport-range-exclusionspan"></a><span class="x x-first x-last">Ausschluss von IP- und Portbereichen</span> implementieren

Ausgehender TCP-basierter Datenverkehr von Anwendungen wird standardmäßig mithilfe der von OSM programmierten `iptables`-Regeln abgefangen und an den Envoy-Proxy-Sidecar umgeleitet. OSM bietet eine Möglichkeit, eine Liste von IP-Adressbereichen und Ports anzugeben, die bei Bedarf von der Abfangen des Datenverkehrs ausgeschlossen werden sollen. Anleitungen zum Ausschließen von IP- und Portbereichen finden Sie [in dieser Dokumentation.](https://release-v0-11.docs.openservicemesh.io/docs/guides/traffic_management/iptables_redirection/)

> [!NOTE]
>
> - Für das Open Service Mesh-AKS-Add-On **kann der Portausschluss nur nach der Installation mit `kubectl patch` und nicht während der Installation mithilfe des OSM-CLI `--set` -Flags implementiert werden.**
> - Wenn die Anwendungspods, die Teil des Gitternetzes sind, Zugriff auf IMDS, Azure DNS oder den Kubernetes-API-Server benötigen, muss der Benutzer diese IP-Adressen mit dem obigen Befehl explizit der Liste der globalen ausgehenden IP-Adressbereiche hinzufügen. Ein Beispiel für den Ausschluss von Kubernetes API-Serverports finden Sie [hier.](https://release-v0-11.docs.openservicemesh.io/docs/guides/app_onboarding/#onboard-services)
