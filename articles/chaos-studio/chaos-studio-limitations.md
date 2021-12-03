---
title: Einschränkungen und bekannte Probleme in Azure Chaos Studio
description: Hier finden Sie Informationen zu aktuellen Einschränkungen und bekannten Problemen bei der Verwendung von Azure Chaos Studio.
services: chaos-studio
author: johnkemnetz
ms.topic: overview
ms.date: 11/02/2021
ms.author: johnkem
ms.service: chaos-studio
ms.openlocfilehash: b4df4b7f06c999bfc29154fbbed4133c2664dad8
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372452"
---
# <a name="azure-chaos-studio-preview-limitations-and-known-issues"></a>Einschränkungen und bekannte Probleme in Azure Chaos Studio (Vorschau)

In der öffentlichen Vorschauversion von Azure Chaos Studio gibt es einige Einschränkungen und bekannte Probleme, von denen das Team weiß und an deren Behebung gearbeitet wird.

## <a name="limitations"></a>Einschränkungen 

* Bei Agent-basierten Fehlern muss die VM auf die folgenden **HTTPS-Endpunkte** zugreifen können:
    * http://agentcommunicationservice-frontdoor-canary.trafficmanager.net 
    * Wenn Sie Telemetriedaten an Application Insights senden, sind auch die [in diesem Dokument](../azure-monitor/app/ip-addresses.md) aufgeführten IP-Adressen erforderlich.
* Wenn Sie ein Experiment ausführen, das den Chaos-Agent nutzt, muss die VM eines der folgenden **Betriebssysteme** ausführen:
    * Windows Server 2019, Windows Server 2016, Windows Server 2012 oder Windows Server 2012 R2
    * Redhat Enterprise Linux 8.2, SUSE Enterprise Linux 15 SP2, CentOS 8.2, Debian 10 Buster (mit erforderlicher Unzip-Installation), Oracle Linux 7.8 Ubuntu Server 16.04 LTS und Ubuntu Server 18.04 LTS
* Der Chaos-Agent wurde nicht für benutzerdefinierte Linux-Distributionen oder gehärtete Linux-Distributionen (z. B. FIPS oder SELinux) getestet.
* Die Chaos Studio-Portaloberfläche wurde nur in den folgenden Browsern getestet:
    * **Windows**: Microsoft Edge, Google Chrome, Firefox
    * **MacOS**: Safari, Google Chrome, Firefox

## <a name="known-issues"></a>Bekannte Probleme
* Über die Funktion **Enable agent-based target** (Agent-basiertes Ziel aktivieren) im Azure-Portal wird dem virtuellen Computer oder der VM-Skalierungsgruppe nicht auch die benutzerseitig zugewiesene verwaltete Identität zugewiesen. Dieser Schritt muss manuell ausgeführt werden. Andernfalls wird bei einem Agent-basierten Fehler in einem Experiment die folgende Fehlermeldung angezeigt: „Vergewissern Sie sich, dass das Onboarding des Ziels ordnungsgemäß durchgeführt wurde und der Experiment-MSI die ordnungsgemäßen Leseberechtigungen erteilt wurden.“ Dieser Schritt kann nach dem Aktivieren des Agent-basierten Ziels erfolgen, erfordert aber möglicherweise einen Neustart.
* Beim Onboarding eines Ziels im Azure-Portal kann ein Fehler auftreten, wenn Sie die Ansicht „Ziele“ verlassen, bevor das Onboarding abgeschlossen ist.
* Beim Erstellen eines Experiments kommt es nach dem Klicken auf **Überprüfen + erstellen** zu einer Verzögerung, bis das erstellte Experiment in der Experimentliste angezeigt wird. Benutzer müssen die Ansicht aktualisieren, um das Experiment in der Liste anzuzeigen.
* Bei der Auswahl von Zielressourcen für einen Agent-basierten Fehler im Experiment-Designer ist es möglich, VMs oder VM-Skalierungsgruppen mit einem Betriebssystem auszuwählen, das vom ausgewählten Fehler nicht unterstützt wird.


## <a name="next-steps"></a>Nächste Schritte
Nutzen Sie die folgenden Links, um mit der Erstellung und Ausführung von Chaos-Experimenten zu beginnen und die Anwendungsresilienz mit Chaos Studio zu verbessern.
- [Erstellen und Ausführen des ersten Experiments](chaos-studio-tutorial-service-direct-portal.md)
- [Grundlegendes zu Chaos Engineering und Resilienz](chaos-studio-chaos-engineering-overview.md)
