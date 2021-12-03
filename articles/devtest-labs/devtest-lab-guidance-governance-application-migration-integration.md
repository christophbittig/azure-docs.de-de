---
title: Anwendungsmigration und -integration
description: Dieser Artikel enthält einen Leitfaden zur Governance der Azure DevTest Labs-Infrastruktur. Der Kontext ist Anwendungsmigration und -integration.
ms.topic: how-to
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 0c80db4ef4d9fa6b3b58d84d663a84b89f93219f
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397621"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Governance der Azure DevTest Labs-Infrastruktur: Migration und Integration von Anwendungen
Nach der Einrichtung Ihrer Entwicklungs-/Test Lab-Umgebung müssen Sie sich die folgenden Fragen stellen:

- Wie verwenden Sie die Umgebung innerhalb Ihres Projektteams?
- Wie stellen Sie sicher, dass alle erforderlichen Organisationsrichtlinien befolgt werden und zugleich die Agilität erhalten bleibt, um Mehrwert für Ihre Anwendung zu generieren?

## <a name="azure-marketplace-images-vs-custom-images"></a>Azure Marketplace-Images im Vergleich mit benutzerdefinierten Images

### <a name="question"></a>Frage
Wann sollte einem Azure Marketplace-Image der Vorzug gegenüber einem eigenen, benutzerdefinierten Image der Organisation gegeben werden?

### <a name="answer"></a>Antwort
Azure Marketplace sollte standardmäßig verwendet werden, sofern keine bestimmten Anliegen oder die Organisation betreffenden Anforderungen bestehen. Dies sind einige häufige Beispiele:

- Eine komplexe Softwareeinrichtung, bei der eine Anwendung als Teil des Basisimages enthalten sein muss.
- Installation und Einrichtung einer Anwendung können viele Stunden dauern, und dieses Hinzufügen zu einem Azure Marketplace-Image stellt keine effiziente Nutzung der Computezeit dar.
- Entwickler und Tester benötigen schnell Zugriff auf einen virtuellen Computer und möchten die erforderliche Zeit für die Einrichtung eines neuen virtuellen Computers minimieren.
- Compliance- oder behördliche Bestimmungen (z.B. Sicherheitsrichtlinien), die für alle Computer umgesetzt werden müssen.

Erwägen Sie die Verwendung benutzerdefinierter Images sorgfältig. Benutzerdefinierte Images führen zu zusätzlicher Komplexität, da Sie sich nun um die Verwaltung der VHD-Dateien für die zugrundeliegenden Basisimages kümmern müssen. Außerdem müssen Sie die Basisimages regelmäßig mit Softwareupdates patchen. Zu diesen Updates gehören neue Betriebssystemupdates und alle Updates oder Konfigurationsänderungen, die am Softwarepaket selbst erforderlich werden.

## <a name="formula-vs-custom-image"></a>Formel- im Vergleich mit benutzerdefinierten Images

### <a name="question"></a>Frage
Wann sollte ein Formel- im Vergleich zu einem benutzerdefinierten Image bevorzugt werden?

### <a name="answer"></a>Antwort
In der Regel sind die entscheidenden Faktoren Kosten und Wiederverwendbarkeit.

Sie können unter den folgenden Umständen die Kosten senken, indem Sie ein benutzerdefiniertes Image erstellen:
- Viele Benutzer oder Labs benötigen das Image.
- Das erforderliche Image verfügt über umfangreiche Software über dem Basisimage.

Diese Lösung bedeutet, dass Sie das Image ein Mal erstellen. Ein benutzerdefiniertes Image reduziert die Einrichtungszeit der VM. Bei der Ausführung der VM während des Setups fallen keine Kosten an.

Ein weiterer Faktor ist die Häufigkeit von Änderungen an Ihrem Softwarepaket. Wenn Sie täglich Builds ausführen und es erforderlich ist, dass Software auf den VMs Ihrer Benutzer vorhanden ist, ziehen Sie die Verwendung einer Formel anstelle eines benutzerdefinierten Images in Betracht.

## <a name="use-custom-organizational-images"></a>Verwenden von benutzerdefinierten Images der Organisation

### <a name="question"></a>Frage
Wie kann ich einen leicht wiederholbaren Prozess einrichten, um meine benutzerdefinierten organisationseigenen Images in einer DevTest Labs-Umgebung einzusetzen?

### <a name="answer"></a>Antwort
Sehen Sie sich dazu [dieses Video zum Image Factory-Muster](./devtest-lab-faq.yml#blog-post) an. Dabei handelt es sich um ein erweitertes Szenario, und die bereitgestellten Skripts stellen lediglich Beispielskripts dar. Wenn irgendwelche Änderungen erforderlich sind, müssen Sie die in Ihrer Umgebung verwendeten Skripts verwalten und warten.

Hier finden Sie weitere Informationen, um mit DevTest Labs eine benutzerdefinierte Imagepipeline in Azure Pipelines zu erstellen:

- [Introduction: Get VMs ready in minutes by setting up an image factory in Azure DevTest Labs](./devtest-lab-faq.yml#blog-post) (Einführung: Betriebsfertig machen von VMs innerhalb von Minuten durch Einrichten einer Image Factory in Azure DevTest Labs)
- [Image Factory – Teil 2: Einrichten von Azure Pipelines und Factory Lab zum Erstellen von VMs](./devtest-lab-faq.yml#blog-post)
- [Image Factory – Teil 3: Save Custom Images and Distribute to Multiple Labs](./devtest-lab-faq.yml#blog-post) (Speichern von benutzerdefinierten Images und Verteilen an mehrere Labs)
- [Video: Custom Image Factory with Azure DevTest Labs](./devtest-lab-faq.yml#blog-post) (Factory für benutzerdefinierte Images mit Azure DevTest Labs)

## <a name="patterns-to-set-up-network-configuration"></a>Muster zum Einrichten der Netzwerkkonfiguration

### <a name="question"></a>Frage
Wie kann ich sicherstellen, dass VMs für Entwicklung und Test das öffentliche Internet nicht erreichen können? Gibt es empfohlene Muster zum Einrichten der Netzwerkkonfiguration?

### <a name="answer"></a>Antwort
Ja. Zwei Aspekte müssen berücksichtigt werden: eingehender und ausgehender Datenverkehr.

**Eingehender Datenverkehr**: Wenn die VM über keine öffentliche IP-Adresse verfügt, kann sie aus dem Internet nicht erreicht werden. Ein gängiger Ansatz besteht darin, eine Richtlinie auf Abonnementebene festzulegen, die sicherstellt, dass kein Benutzer eine öffentliche IP-Adresse erstellen kann.

**Ausgehender Datenverkehr**: Wenn Sie verhindern möchten, dass VMs direkte Verbindungen mit dem öffentlichen Internet herstellen und Datenverkehr durch eine Unternehmensfirewall senden können, können Sie den Datenverkehr lokal mithilfe von Azure ExpressRoute oder VPN routen, indem Sie erzwungenes Routing einsetzen.

> [!NOTE]
> Wenn Sie über einen Proxyserver verfügen, der ohne Proxyeinstellungen Datenverkehr blockiert, vergessen Sie nicht, Ausnahmen für das Speicherkonto des Labs für Artefakte hinzuzufügen.

Sie können außerdem Netzwerksicherheitsgruppen für virtuelle Computer oder Subnetze verwenden. Dieser Schritt fügt eine weitere Schutzschicht zum Zulassen/Blockieren von Datenverkehr hinzu.

## <a name="new-vs-existing-virtual-network"></a>Neues im Vergleich mit vorhandenem virtuellem Netzwerk

### <a name="question"></a>Frage
Wann sollte ich ein neues virtuelles Netzwerk für meine DevTest Labs-Umgebung erstellen und wann ein vorhandenes virtuelles Netzwerk nutzen?

### <a name="answer"></a>Antwort
Wenn Ihre VMs mit vorhandener Infrastruktur interagieren müssen, sollten Sie die Verwendung eines vorhandenen virtuellen Netzwerks innerhalb Ihrer DevTest Labs-Umgebung in Erwägung ziehen. Wenn Sie ExpressRoute verwenden, minimieren Sie die Anzahl virtueller Netzwerke und Subnetze, sodass Sie den IP-Adressraum, der Ihren Abonnements zugewiesen ist, nicht fragmentieren. Sie sollten auch die Verwendung des Peeringmusters für virtuelle Netzwerke (Hub-Spoke-Modell) in Erwägung ziehen. Dieser Ansatz ermöglicht die abonnementübergreifende Kommunikation zwischen virtuellen Netzwerken und Subnetzen innerhalb einer bestimmten Region.

Jede DevTest Labs-Umgebung kann über ein eigenes virtuelles Netzwerk verfügen, aber es gibt [Grenzwerte](../azure-resource-manager/management/azure-subscription-service-limits.md) für die Anzahl virtueller Netzwerke pro Abonnement. Standardmäßig sind 50 möglich, dieser Grenzwert kann jedoch auf 100 heraufgesetzt werden.

## <a name="shared-public-or-private-ip"></a>Freigegebene, öffentliche oder private IP

### <a name="question"></a>Frage
Wann sollte eine gemeinsame IP-Adresse, wann eine öffentliche, wann eine private verwendet werden?

### <a name="answer"></a>Antwort
Wenn Sie ein Standort-zu-Standort-VPN oder Express Route verwenden, ziehen Sie die Verwendung privater IPs in Erwägung, so dass auf Ihre Computer über Ihr internes Netzwerk zugegriffen werden kann, nicht aber aus dem öffentlichen Internet.

> [!NOTE]
> Labbesitzer können diese Subnetzrichtlinie ändern, um sicherzustellen, dass niemand versehentlich öffentliche IP-Adressen für seine VMs erstellt. Der Abonnementbesitzer sollte eine Abonnementrichtlinie einrichten, die das Erstellen öffentlicher IPs verhindert.

Bei der Verwendung gemeinsamer öffentlicher IPs teilen die virtuellen Computer in einem Lab eine öffentliche IP-Adresse. Dieser Ansatz kann nützlich sein, wenn Sie das Überschreiten des Grenzwerts für öffentliche IP-Adressen für ein bestimmtes Abonnement vermeiden müssen.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Grenzen der Anzahl virtueller Computer pro Benutzer oder Lab

### <a name="question"></a>Frage
Gibt es eine Regel hinsichtlich der Anzahl von VMS, die pro Benutzer oder Lab eingerichtet werden sollten?

### <a name="answer"></a>Antwort
Beim Betrachten der Anzahl virtueller Computer pro Benutzer oder Lab gibt es drei Hauptanliegen:

- Die **Gesamtkosten**, die das Team für Ressourcen im Lab aufwenden kann. Es ist natürlich kein Problem, viele Computer laufen zu lassen. Ein Mechanismus zur Kostenkontrolle besteht darin, die Anzahl der VMs pro Benutzer oder Lab zu beschränken.
- Die Gesamtzahl der virtuellen Computer in einem Lab wird durch die verfügbaren [Kontingente auf Abonnementebene](../azure-resource-manager/management/azure-subscription-service-limits.md) beeinflusst. Eine der Obergrenzen liegt bei 800 Ressourcengruppen pro Abonnement. DevTest Labs erstellt derzeit eine neue Ressourcengruppe für jeden virtuellen Computer (sofern keine gemeinsamen öffentlichen IP-Adressen verwendet werden). Wenn Sie 10 Labs in einem Abonnement betreiben, könnten pro Lab ungefähr 79 virtuelle Computer eingesetzt werden (Obergrenze von 800 – 10 Ressourcengruppen für die 10 Labs selbst) = 79 virtuelle Computer pro Lab.
- Wenn das Lab mithilfe von ExpressRoute (beispielsweise) mit dem lokalen Netzwerk verbunden ist, sind für das VNet/Subnetz **definierte IP-Adressräume verfügbar**. Um sicherzustellen, dass keine Fehler beim Erstellen von VMs im Lab auftreten (Fehler: IP-Adresse kann nicht abgerufen werden), können sich Lab-Besitzer beim Festlegen der maximalen Anzahl von VMs pro Lab am zur Verfügung stehenden IP-Adressraum orientieren.

## <a name="use-resource-manager-templates"></a>Verwenden von Resource Manager-Vorlagen

### <a name="question"></a>Frage
Wie können Resource Manager-Vorlagen in der DevTest Labs-Umgebung verwendet werden?

### <a name="answer"></a>Antwort
Stellen Sie Ihre Resource Manager-Vorlagen bereit, indem Sie die Schritte unter [Verwenden von Azure DevTest Labs für Testumgebungen](devtest-lab-test-env.md) ausführen. Im Wesentlichen checken Sie Ihre Resource Manager-Vorlagen in ein Azure Repos- oder GitHub Git-Repository ein und fügen dem Lab ein [privates Repository für Ihre Vorlagen](devtest-lab-test-env.md) hinzu.

Dieses Szenario ist möglicherweise nicht sinnvoll, wenn Sie DevTest Labs zum Hosten von Entwicklungscomputern verwenden. Verwenden Sie dieses Szenario, um eine Stagingumgebung zu erstellen, die für die Produktion repräsentativ ist.

Die Option für die Anzahl von VMs pro Lab oder Benutzer schränkt nur die Anzahl der nativ im Lab selbst erstellten Computer ein. Diese Option schränkt nicht die Erstellung durch Umgebungen mit Resource Manager Vorlagen ein.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Use environments in DevTest Labs](devtest-lab-test-env.md) (Verwenden von Umgebungen in DevTest Labs).