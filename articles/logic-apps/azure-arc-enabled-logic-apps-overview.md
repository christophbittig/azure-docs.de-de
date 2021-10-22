---
title: 'Übersicht: Logic Apps mit Azure Arc-Unterstützung'
description: Erfahren Sie mehr über Workflows von Logic Apps mit nur einem Mandanten, die überall dort ausgeführt werden können, wo Kubernetes ausgeführt werden kann.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, ladolan, reylons, archidda, sopai, azla
ms.topic: overview
ms.date: 05/25/2021
ms.openlocfilehash: 2a8d3187eecb684f7c0c2b89c135745dbcc9d8e9
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713367"
---
# <a name="what-is-azure-arc-enabled-logic-apps-preview"></a>Was ist Logic Apps mit Azure Arc-Unterstützung? (Vorschau)

> [!NOTE]
> Diese Funktion befindet sich in der Vorschauphase und unterliegt den [Zusätzlichen Nutzungsbedingungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Mit Logic Apps mit Azure Arc-Unterstützung können Sie Logik-Apps auf Basis eines einzelnen Mandanten überall dort entwickeln und ausführen, wo Kubernetes ausgeführt werden kann. Beispielsweise können Sie Ihre Logik-App-Workflows in Azure, Azure Kubernetes Service, lokal und sogar bei anderen Cloudanbietern ausführen. Dieses Angebot bietet eine zentralisierte Verwaltungsplattform über Azure Arc und das Azure-Portal für folgende Funktionen:

- Verwenden von Azure Logic Apps als Integrationsplattform.
- Verbinden Ihrer Workflows mit allen Ihren Diensten, unabhängig davon, wo sie gehostet werden.
- Ausführen Ihrer Integrationslösungen direkt zusammen mit Ihren Diensten.
- Erstellen und Bearbeiten von Workflows mit Visual Studio Code.
- Bereitstellen Ihrer Auswahl von Pipelines für DevOps.
- Steuern Ihrer Infrastruktur und Ressourcen in Azure- und Nicht-Azure-Umgebungen, mehreren Clouds, lokal und Edgeumgebungen.

Weitere Informationen finden Sie in der folgenden Dokumentation:

- [Übersicht: Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md)
- [Einzelner Mandant im Vergleich zu anderen Logic Apps-Umgebungen](../logic-apps/single-tenant-overview-compare.md)
- [Übersicht über Azure Arc](../azure-arc/overview.md)
- [Übersicht über Azure Kubernetes Service](../aks/intro-kubernetes.md)
- [Was ist Kubernetes mit Azure Arc-Unterstützung?](../azure-arc/kubernetes/overview.md)
- [Was ist Kubernetes?](https://kubernetes.io/docs/concepts/overview/what-is-kubernetes/)

<a name="why-use"></a>

## <a name="why-use-azure-arc-enabled-logic-apps"></a>Gründe für die Verwendung von Logic Apps mit Azure Arc-Unterstützung

Mit Logic Apps mit Azure Arc-Unterstützung können Sie Logik-App-Workflows auf dieselbe Weise erstellen und bereitstellen wie in der Azure Logic Apps-Umgebung mit nur einem Mandanten. Sie erhalten auch mehr Kontrolle und Flexibilität, wenn Sie Logik-Apps in einer Kubernetes-Infrastruktur ausführen, die Sie betreiben und verwalten.

Es gibt geringfügige Unterschiede zwischen der Azure Arc-Erfahrung und der einzelinstanzenfähigen Logic Apps-Erfahrung beim Erstellen, Entwerfen und Bereitstellen von Logik-Apps. Wenn Sie Logic Apps mit Azure Arc-Unterstützung verwenden, besteht der Hauptunterschied darin, dass Ihre Logik-Apps an einem *benutzerdefinierten Standort* ausgeführt werden. Dieser Standort ist einem Kubernetes-Cluster mit Azure Arc-Unterstützung zugeordnet, in dem Sie das Paket mit den Azure App Service-Plattformerweiterungen installiert und aktiviert haben.

Dieser Cluster kann z. B. Azure Kubernetes Service, Bare-Metal-Kubernetes oder eine andere Einrichtung sein. Mit dem Erweiterungspaket können Sie Plattformdienste wie Azure Logic Apps, Azure Functions und Azure App Service in Ihrem Kubernetes-Cluster ausführen.

Weitere Informationen finden Sie in der folgenden Dokumentation:

- [Einzelner Mandant im Vergleich zu anderen Azure Logic Apps-Umgebungen](../logic-apps/single-tenant-overview-compare.md)
- [Übersicht über Azure Kubernetes Service](../aks/intro-kubernetes.md)
- [Was ist Kubernetes mit Azure Arc-Unterstützung?](../azure-arc/kubernetes/overview.md)
- [Benutzerdefinierte Standorte in Kubernetes-Clustern mit Azure Arc-Unterstützung](../azure-arc/kubernetes/conceptual-custom-locations.md)
- [App Service, Funktionen und Logic Apps in Azure Arc (Vorschau)](../app-service/overview-arc-integration.md)
- [Einrichten eines Kubernetes-Clusters mit Azure Arc-Unterstützung zum Ausführen von App Service, Funktionen und Logic Apps (Vorschau)](../app-service/manage-create-arc-environment.md)

<a name="when-to-use"></a>

## <a name="when-to-use-azure-arc-enabled-logic-apps"></a>Wann sollte Logic Apps mit Azure Arc-Unterstützung verwendet werden?

Obwohl Kubernetes mehr Kontrolle und Flexibilität bietet, kommt es auch zu Mehraufwand beim Betrieb. Wenn Sie zufrieden sind, dass der Logic Apps-Dienst Ihre Anforderungen erfüllt, wird empfohlen, diesen Dienst weiterhin zu verwenden. Erwägen Sie jedoch die Verwendung von Logic Apps mit Azure Arc-Unterstützung, wenn folgende Szenarien vorliegen:

- Sie führen bereits alle Ihre Apps und Dienste in Kubernetes aus. Sie möchten diese Prozesse und Kontrollen auf alle Ihre anderen PaaS-Dienste ausweiten.

- Sie möchten Logic Apps als Ihre Integrationsplattform verwenden. Sie benötigen jedoch differenzierte Netzwerke mit Computesteuerung und Flexibilität. Sie möchten keine Integrationsdienstumgebung (Integration Service Environment, ISE) oder App Service-Umgebung (ASE) verwenden.

- Aus Sicherheitsgründen benötigen Sie die Kontrolle darüber, wo Ihre Logik-Apps ausgeführt werden, z. B. in Ihrer eigenen Region oder in Ihrem eigenen Rechenzentrum. 

- Sie möchten Ihre Logik-Apps in Multicloudszenarien ausführen und den Logic Apps-Dienst als einzige Integrationsplattform für all Ihre Anwendungen überall dort verwenden, wo sie ausgeführt werden.

<a name="compare"></a>

## <a name="compare-offerings"></a>Vergleich von Angeboten

Diese Tabelle enthält einen allgemeinen Vergleich zwischen den Funktionen in den aktuellen Azure Logic Apps-Angeboten:

:::row:::
   :::column:::
      **Funktion**
   :::column-end:::
   :::column:::
      **Mehrinstanzenfähige Logic Apps (Verbrauch)**
   :::column-end:::
   :::column:::
      **Einzelmandanten-Logic Apps (Standard)**
   :::column-end:::
   :::column:::
      **Eigenständige Container**
   :::column-end:::
   :::column:::
      **Azure Arc**
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      Lokale Entwicklung
   :::column-end:::
   :::column:::
      Visual Studio Code, Visual Studio
   :::column-end:::
   :::column:::
      Visual Studio Code, einschließlich Ausführungsverlauf und Übersicht mit Breakpointdebuggen
   :::column-end:::
   :::column:::
      Visual Studio Code
   :::column-end:::
   :::column:::
      Visual Studio Code, einschließlich Ausführungsverlauf und Übersicht mit Breakpointdebuggen
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      Hosting
   :::column-end:::
   :::column:::
      Nur in Azure ausführen
   :::column-end:::
   :::column:::
      Nur in Azure ausführen
   :::column-end:::
   :::column:::
      Überall ausführen, wo Ihre Container ausgeführt werden
   :::column-end:::
   :::column:::
      Überall, wo ein Kubernetes-Cluster mit Azure Arc-Unterstützung vorhanden ist, ausführen
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      Verwaltung
   :::column-end:::
   :::column:::
      Vollständig verwaltete Logic Apps-Erfahrung
   :::column-end:::
   :::column:::
      Vollständig verwaltete Logic Apps-Erfahrung
   :::column-end:::
   :::column:::
      Nicht verwaltet
   :::column-end:::
   :::column:::
      Verwaltete Logic Apps-Erfahrung mit Betriebskontrolle auf Kubernetes-Ebene
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      Überwachung
   :::column-end:::
   :::column:::
      Überwachung im Azure-Portal, einschließlich Ausführungsverlauf, erneutem Senden von Ausführungen und Application Insights-Funktionen bei Bedarf
   :::column-end:::
   :::column:::
      Überwachung im Azure-Portal, einschließlich Ausführungsverlauf, erneutem Senden von Ausführungen und Application Insights-Funktionen bei Bedarf
   :::column-end:::
   :::column:::
      Überwachung nur mit Application Insights oder anderen Containerüberwachungstools
   :::column-end:::
   :::column:::
      Überwachung im Azure-Portal, einschließlich Ausführungsverlauf, erneutem Senden von Ausführungen und Application Insights-Funktionen bei Bedarf
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      Skalierung
   :::column-end:::
   :::column:::
      Kontrolle der Skalierung mithilfe des Verbrauchsplans
   :::column-end:::
   :::column:::
      Kontrolle der Skalierung mithilfe des Standardplans
   :::column-end:::
   :::column:::
      Nicht verfügbar
   :::column-end:::
   :::column:::
      Kontrolle der Skalierung mithilfe von [Kubernetes-based Event Driven Autoscaling (Kubernetes-basierte, ereignisgesteuerte automatische Skalierung, KEDA)](https://keda.sh/). Konfigurieren von Skalierungsereignissen auf Grundlage der Warteschlangenlänge.
   :::column-end:::
:::row-end:::

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen und Bereitstellen von Workflows mit Logic Apps mit Azure Arc-Unterstützung](azure-arc-enabled-logic-apps-create-deploy-workflows.md)
