---
title: Sicherheitshärtung bei AKS-Hosts für virtuelle Computer
description: Hier erfahren Sie mehr über die Sicherheitshärtung beim AKS-Hostbetriebssystem für virtuelle Computer.
services: container-service
author: georgewallace
ms.topic: article
ms.date: 03/29/2021
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 9c39d67fbf52295fd6b31d417c7780cf7e240304
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132055434"
---
# <a name="security-hardening-for-aks-agent-node-host-os"></a>Sicherheitshärtung beim AKS-Hostbetriebssystem für Agent-Knoten

Da es sich bei Azure Kubernetes Service (AKS) um einen sicheren Dienst handelt, ist er mit den Standards SOC, ISO, PCI-DSS und HIPAA konform. Dieser Artikel befasst sich mit der Sicherheitshärtung, die auf AKS-Hosts für virtuelle Computer (virtual machines, VMs) angewendet wird. Weitere Informationen zur AKS-Sicherheit finden Sie unter [Sicherheitskonzepte für Anwendungen und Cluster in Azure Kubernetes Service (AKS)](./concepts-security.md).

> [!Note]
> Dieses Dokument bezieht sich nur auf Linux-Agents in AKS.

AKS-Cluster werden auf virtuellen Hostcomputern mit einem sicherheitsoptimierten Betriebssystem bereitgestellt, das für in AKS ausgeführte Container verwendet wird. Dieses Hostbetriebssystem basiert auf einem Image vom Typ **Ubuntu 18.04.5 LTS**, auf das zusätzliche die [Sicherheitshärtung](#security-hardening-features) und Optimierungen angewendet werden.

Das Ziel des sicherheitsgehärteten Hostbetriebssystems ist es, die Angriffsfläche zu verringern und die Bereitstellung von Containern auf sichere Weise zu optimieren.

> [!Important]
> Für das sicherheitsgehärtete Betriebssystem wurden **keine** CIS-Benchmarks erstellt. Es gibt zwar Überschneidungen mit CIS-Benchmarks, das Ziel besteht jedoch nicht darin, CIS-kompatibel zu sein. Das Ziel für die Härtung des Hostbetriebssystems ist es, eine Sicherheitsstufe zu erreichen, die den eigenen internen Microsoft-Sicherheitsstandards für Hosts entspricht.

## <a name="security-hardening-features"></a>Features für die Sicherheitshärtung

* AKS bietet standardmäßig ein sicherheitsoptimiertes Hostbetriebssystem, aber keine Option zum Auswählen eines alternativen Betriebssystems.

* Azure wendet tägliche Patches (einschließlich Sicherheitspatches) auf AKS-Hosts für virtuelle Computer an. 
    * Für einige dieser Patches ist ein Neustart erforderlich, für andere nicht. 
    * Neustarts von AKS-VM-Hosts müssen bei Bedarf von Ihnen geplant werden. 
    * Anleitungen zum Automatisieren der Anwendung von AKS-Patches finden Sie unter [Patchen von AKS-Knoten](./node-updates-kured.md).

## <a name="what-is-configured"></a>Was wird konfiguriert?

| CIS  | Überwachungsbeschreibung|
|---|---|
| 1.1.1.1 |Sicherstellen, dass die Einbindung von cramfs-Dateisystemen deaktiviert ist|
| 1.1.1.2 |Sicherstellen, dass die Einbindung von freevxfs-Dateisystemen deaktiviert ist|
| 1.1.1.3 |Sicherstellen, dass die Einbindung von jffs2-Dateisystemen deaktiviert ist|
| 1.1.1.4 |Sicherstellen, dass die Einbindung von HFS-Dateisystemen deaktiviert ist|
| 1.1.1.5 |Sicherstellen, dass die Einbindung von HFS Plus-Dateisystemen deaktiviert ist|
|1.4.3 |Sicherstellen, dass eine Authentifizierung für den Einzelbenutzermodus erforderlich ist |
|1.7.1.2 |Sicherstellen, dass das Warnbanner für lokale Anmeldung richtig konfiguriert ist |
|1.7.1.3 |Sicherstellen, dass das Warnbanner für Remoteanmeldung richtig konfiguriert ist |
|1.7.1.5 |Sicherstellen, dass Berechtigungen für „/etc/issue“ konfiguriert sind |
|1.7.1.6 |Sicherstellen, dass Berechtigungen für „/etc/issue.net“ konfiguriert sind |
|2.1.5 |Sicherstellen, dass „--streaming-connection-idle-timeout“ nicht auf 0 festgelegt ist |
|3.1.2 |Sicherstellen, dass das Senden von Paketumleitungen deaktiviert ist |
|3.2.1 |Sicherstellen, dass an die Quelle geleitete Pakete nicht akzeptiert werden |
|3.2.2 |Sicherstellen, dass ICMP-Umleitungen nicht akzeptiert werden |
|3.2.3 |Sicherstellen, dass sichere ICMP-Umleitungen nicht akzeptiert werden |
|3.2.4 |Sicherstellen, dass verdächtige Pakete protokolliert werden |
|3.3.1 |Sicherstellen, dass IPv6-Routerankündigungen nicht akzeptiert werden |
|3.5.1 |Sicherstellen, dass DCCP deaktiviert ist |
|3.5.2 |Sicherstellen, dass SCTP deaktiviert ist |
|3.5.3 |Sicherstellen, dass RDS deaktiviert ist |
|3.5.4 |Sicherstellen, dass TIPC deaktiviert ist |
|4.2.1.2 |Sicherstellen, dass die Protokollierung konfiguriert ist |
|5.1.2 |Sicherstellen, dass Berechtigungen für „/etc/crontab“ konfiguriert sind |
|5.2.4 |Sicherstellen, dass X11-Forwarding für SSH deaktiviert ist |
|5.2.5 |Sicherstellen, dass „MaxAuthTries“ für SSH auf höchstens 4 festgelegt ist |
|5.2.8 |Sicherstellen, dass Root-Anmeldung für SSH deaktiviert ist |
|5.2.10 |Sicherstellen, dass „PermitUserEnvironment“ für SSH deaktiviert ist |
|5.2.11 |Sicherstellen, dass nur genehmigte MAX-Algorithmen verwendet werden |
|5.2.12 |Sicherstellen, dass das Timeoutintervall für Leerlauf für SSH konfiguriert ist |
|5.2.13 |Sicherstellen, dass „LoginGraceTime“ für SSH auf höchstens eine Minute festgelegt ist |
|5.2.15 |Sicherstellen, dass das Warnbanner für SSH konfiguriert ist |
|5.3.1 |Sicherstellen, dass Anforderungen für die Kennworterstellung konfiguriert sind |
|5.4.1.1 |Sicherstellen, dass der Kennwortablauf höchstens 90 Tage beträgt |
|5.4.1.4 |Sicherstellen, dass die Sperre für inaktive Kennwörter höchstens 30 Tage beträgt |
|5.4.4 |Sicherstellen, dass der Befehl „umask“ für Standardbenutzer 027 oder stärker einschränkend ist |
|5.6 |Sicherstellen, dass der Zugriff auf den Befehl „su“ eingeschränkt ist|

## <a name="additional-notes"></a>Zusätzliche Hinweise
 
* Um die Angriffsfläche weiter zu verringern, wurden einige unnötige Kernelmodultreiber im Betriebssystem deaktiviert.

* Das sicherheitsgehärtete Betriebssystem wird speziell für AKS erstellt und verwaltet und außerhalb der AKS-Plattform **nicht** unterstützt.

## <a name="next-steps"></a>Nächste Schritte  

Weitere Informationen zur AKS-Sicherheit finden Sie in den folgenden Artikeln: 

* [Azure Kubernetes Service (AKS)](./intro-kubernetes.md)
* [Sicherheitskonzepte für Anwendungen und Cluster in Azure Kubernetes Service (AKS)](./concepts-security.md)
* [Best Practices für Clusterbetreiber und -entwickler zum Erstellen und Verwalten von Anwendungen in Azure Kubernetes Service (AKS)](./best-practices.md)
