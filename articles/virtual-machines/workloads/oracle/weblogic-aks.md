---
title: Welche Lösungen zum Ausführen von Oracle WebLogic Server in Azure Kubernetes Service gibt es?
description: Erfahren Sie, wie Oracle WebLogic Server für Azure Kubernetes Service ausgeführt wird.
author: rezar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 02/23/2021
ms.author: rezar
ms.reviewer: cynthn
ms.custom: devx-track-java, devx-track-javaee, devx-track-javaee-wls, devx-track-javaee-wls-aks
ms.openlocfilehash: 1c5d5de82f2398649999a46cca853eb7c72ff4e3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131063440"
---
# <a name="what-are-solutions-for-running-oracle-weblogic-server-on-the-azure-kubernetes-service"></a>Welche Lösungen zum Ausführen von Oracle WebLogic Server in Azure Kubernetes Service gibt es?

**Gilt für**: :heavy_check_mark: Linux-VMs 

Auf dieser Seite werden die Lösungen zum Ausführen von Oracle WebLogic Server (WLS) für Azure Kubernetes Service (AKS) beschrieben. Diese Lösungen werden gemeinsam von Oracle und Microsoft entwickelt und unterstützt.

Es ist auch möglich, WebLogic Server in Azure Virtual Machines auszuführen. Die dafür verfügbaren Lösungen werden in [diesem Microsoft-Artikel](./oracle-weblogic.md) beschrieben.

WebLogic Server ist ein führender Java-Anwendungsserver, auf dem einige der weltweit wichtigsten unternehmenskritischen Java-Unternehmensanwendungen ausgeführt werden. WebLogic Server bildet die Middleware Foundation für die Oracle-Softwaresuite. Oracle und Microsoft sind bestrebt, WebLogic Server-Kunden mit der Auswahl und Flexibilität bei der Durchführung von Workloads in Azure als führende Cloudplattform zu unterstützen.

## <a name="wls-on-aks-certified-and-supported"></a>WLS in AKS, zertifiziert und unterstützt
WebLogic Server ist von Oracle und Microsoft für die reibungslose Ausführung mit AKS zertifiziert. Die „WebLogic Server in AKS“-Lösungen sollen es Ihnen so einfach wie möglich gestalten, Ihre containerisierten und orchestrierten Java-Anwendungen in der Docker- und Kubernetes-Infrastruktur auszuführen. Die Lösungen konzentrieren sich auf Zuverlässigkeit, Skalierbarkeit, Verwaltbarkeit und Unternehmensunterstützung.

WebLogic Server-Cluster können über den WebLogic Kubernetes Operator (im Folgenden einfach „Operator“ genannt) vollständig für Kubernetes ausgeführt werden. Der Operator folgt dem Standardmuster des Kubernetes-Operators. Er vereinfacht die Verwaltung und den Betrieb von WebLogic-Domänen und Bereitstellungen auf Kubernetes, indem er ansonsten manuelle Aufgaben automatisiert und zusätzliche Features zur Zuverlässigkeit des Betriebs hinzufügt. Der Operator unterstützt Oracle WebLogic Server 12c, Oracle Fusion Middleware Infrastructure 12c und höher. Wir haben die offiziellen Docker-Images für WebLogic Server 12.2.1.3 und 12.2.1.4 mit dem Operator getestet. Weitere Informationen zum Operator finden Sie in der [offiziellen Dokumentation von Oracle](https://oracle.github.io/weblogic-kubernetes-operator/).

## <a name="guidance-scripts-and-samples-for-wls-on-aks"></a>Leitfäden, Skripts und Beispiele für WLS in AKS
Über die Zertifizierung von WebLogic Server in AKS hinaus stellen Oracle und Microsoft gemeinsam detaillierte Leitfäden, Skripts und Beispiele für die Ausführung von WebLogic Server in AKS zur Verfügung. Der Leitfaden ist in den Beispielabschnitt von Azure Kubernetes Service in der [Operator-Dokumentation](https://oracle.github.io/weblogic-kubernetes-operator/) integriert. Der Leitfaden ist darauf ausgerichtet, die Produktion von WebLogic Server in AKS-Bereitstellungen so einfach wie möglich zu gestalten. Der Leitfaden verwendet offizielle WebLogic Server Docker-Images, die von Oracle bereitgestellt werden. Das Failover wird über Azure Files erreicht, auf das über Kubernetes-Ansprüche auf persistente Volumes zugegriffen wird. Azure Load Balancer-Instanzen werden unterstützt, wenn sie mithilfe eines Kubernetes-Diensts vom Typ „LoadBalancer“ bereitgestellt werden. Die Azure Container Registry (ACR) wird für das Bereitstellen von WLS-Domänen in benutzerdefinierten Docker-Images unterstützt. Die Anleitung gestattet ein hohes Maß an Konfiguration und Anpassung.

:::image type="content" source="media/oracle-weblogic/wls-on-aks.gif" alt-text="Sie können die Beispielskripts zur Bereitstellung von WebLogic Server in AKS verwenden.":::

Die Lösungen umfassen zwei Möglichkeiten zum Bereitstellen von WLS-Domänen in AKS. Domänen können direkt in persistenten Kubernetes-Volumes bereitgestellt werden. Diese Bereitstellungsoption eignet sich gut, wenn Sie zu AKS migrieren möchten, WLS jedoch weiterhin mit der Verwaltungskonsole oder dem WebLogic Scripting Tool (WLST) verwalten möchten. Mit dieser Option können Sie auch ohne Docker-Entwicklung zu AKS wechseln. Die stärker an Kubernetes angelehnte Bereitstellung von WLS-Domänen in AKS erfolgt durch die Erstellung benutzerdefinierter Docker-Images auf Grundlage offizieller WLS-Images aus Oracle Container Registry. Die benutzerdefinierten Images werden dann in ACR veröffentlicht, und die Domäne wird mit dem Operator in AKS bereitgestellt. Mit dieser Option in der Lösung können Sie auch die Domäne über Kubernetes-ConfigMaps aktualisieren, nachdem die Bereitstellung abgeschlossen wurde.

Weitere Integrationen zur Benutzerfreundlichkeit und zum Azure-Dienst sind in Zukunft über Marketplace-Angebote möglich, die Oracle WebLogic Server auf Azure Virtual Machine-Lösungen spiegeln.

_Diese Lösungen sind vom Typ „Bring Your Own License“ (BYOL)_ . Es wird davon ausgegangen, dass Sie bereits über die entsprechenden Lizenzen bei Oracle verfügen und mit ordnungsgemäßen Lizenzen ausgestattet sind, um Angebote in Azure auszuführen.

_Wenn Sie daran interessiert sind, eng mit dem Entwicklungsteam, das diese Lösungen entwickelt, an Ihren Migrationsszenarien zu arbeiten, füllen Sie [diese kurze Umfrage](https://aka.ms/wls-on-azure-survey) aus, und geben Sie Ihre Kontaktinformationen an_. Programm-Manager, Architekten und Techniker setzen sich dann in Kürze mit Ihnen in Verbindung und beginnen mit der engen Zusammenarbeit! Während sich die Lösungen in der aktiven anfänglichen Entwicklungsphase befinden, haben Sie die Möglichkeit, kostenlos an einem Migrationsszenario zusammenzuarbeiten.

## <a name="deployment-architectures"></a>Bereitstellungsarchitekturen

Die Lösungen für die Ausführung von Oracle WebLogic Server für Azure Kubernetes Service ermöglichen relativ einfach eine breite Palette von produktionsbereiten Bereitstellungsarchitekturen.

:::image type="content" source="media/oracle-weblogic/weblogic-architecture-aks.png" alt-text="Komplexe WebLogic Server-Bereitstellungen sind in AKS aktiviert":::

Neben dem, was die Lösungen bieten, haben Kunden die Flexibilität, ihre Bereitstellung weiter anzupassen. Ganz oben bei der Bereitstellung von Anwendungen liegt für Kunden wahrscheinlich die Integration weiterer Azure-Ressourcen mit ihren Bereitstellungen. Kunden wird empfohlen, Feedback zur weiteren Verbesserung der Lösungen in der Umfrage bereitzustellen.

## <a name="next-steps"></a>Nächste Schritte

Untersuchen Sie die Ausführung von Oracle WebLogic Server für Azure Kubernetes Service.

> [!div class="nextstepaction"]
> [Leitfäden, Skripts und Beispiele zum Ausführen von WLS in AKS](https://oracle.github.io/weblogic-kubernetes-operator/)

> [!div class="nextstepaction"]
> [WebLogic Kubernetes Operator](https://oracle.github.io/weblogic-kubernetes-operator/)
