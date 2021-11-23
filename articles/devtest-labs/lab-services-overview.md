---
title: Azure Lab Services im Vergleich zu Azure DevTest Labs
description: Vergleich von Azure DevTest Labs und Azure Lab Services.
ms.topic: overview
ms.date: 06/26/2020
ms.openlocfilehash: 15b45b6d2dad7037d6069f54910d67899f307926
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397583"
---
# <a name="compare-azure-devtest-labs-and-azure-lab-services"></a>Vergleich von Azure DevTest Labs und Azure Lab Services
Es gibt zwei Dienste in Azure, mit denen Laborumgebungen in der Cloud eingerichtet werden können. 

- **Azure DevTest Labs:** Mit diesem Dienst können Sie schnell eine Umgebung für Ihr Team (z. B. eine Entwicklungsumgebung oder Testumgebung in der Cloud) einrichten. Der Besitzer des Labs erstellt ein Lab, stellt virtuelle Windows- oder Linux-Computer bereit, installiert die erforderlichen Programme und Tools und stellt sie Lab-Benutzern zur Verfügung. Labbenutzer verbinden sich mit virtuellen Computern (VMs) im Lab und nutzen sie für ihre tägliche Arbeit und kurzzeitige Projekte. Sobald Benutzer die Ressourcen im Lab nutzen, kann ein Lab-Administrator die Kosten und Nutzung mehrerer Labs analysieren und übergreifende Richtlinien festlegen, um die Kosten Ihrer Organisation oder Ihres Teams zu optimieren.
- **Azure Lab Services:** Dieser Dienst ermöglicht es Ihnen, verwaltete Labtypen zu erstellen. Aktuell sind Classroom-Labs die einzige Art von verwalteten Labs, die von Azure Lab Services unterstützt werden. Der Dienst selbst übernimmt die gesamte Infrastrukturverwaltung für einen verwalteten Lab-Typ – vom Hochfahren der VMs über die Fehlerbehandlung bis hin zur Skalierung der Infrastruktur. Nachdem ein IT-Administrator ein Lab-Konto in Azure Lab Services erstellt hat, kann ein Kursleiter schnell ein Classroom-Lab einrichten. Der Kursleiter kann die Anzahl und Art der virtuellen Computer angeben, die für Übungen im Rahmen des Kurses erforderlich sind, und dem Kurs Benutzer hinzufügen. Wenn sich ein Benutzer für den Kurs registriert hat, kann er auf den virtuellen Computer zugreifen, um Übungen für den Kurs durchzuführen.  

## <a name="key-capabilities"></a>Wichtige Funktionen

Azure DevTest Labs und Azure Lab Services unterstützen die folgenden wichtigen Funktionen und Features:

- **Schnelle und flexible Einrichtung eines Labs**. Mithilfe von Azure Lab Services können Lab-Besitzer schnell ein Lab ihren Anforderungen entsprechend einrichten. Der Dienst bietet die Möglichkeit, alle Aufgaben im Zusammenhang mit der Azure-Infrastruktur für verwaltete Labs zu übernehmen Alternativ können Sie Lab-Besitzer die Infrastruktur im Abonnement des Lab-Besitzers selbst verwalten und anpassen lassen. Der Dienst bietet eine integrierte Skalierung und Resilienz von Infrastruktur für Labs, die er für Sie verwaltet.
- **Vereinfachte Umgebung für Lab-Benutzer**. In einem verwalteten Lab-Typ, z. B. einem Classroom-Lab, können sich Lab-Benutzer mit einem Registrierungscode registrieren und jederzeit auf das Lab zugreifen, um dessen Ressourcen zu nutzen. Ein Lab-Besitzer kann Lab-Benutzern die Berechtigung erteilen, virtuelle Computer zu erstellen und darauf zuzugreifen, Datenträger zu verwalten und wiederzuverwenden und wiederverwendbare Geheimnisse einzurichten.  
- **Kostenoptimierung und -analyse**. Ein Lab-Besitzer kann Lab-Zeitpläne festlegen, gemäß denen virtuelle Computer automatisch herunter- und hochgefahren werden. Der Lab-Besitzer kann in einem Zeitplan die Zeitfenster festlegen, in denen die virtuellen Computer des Labs für Benutzer zugänglich sind. Zur Kostenoptimierung kann der Besitzer lab- oder benutzerbezogene Nutzungsrichtlinien festlegen sowie Nutzungs- und Aktivitätstrends in einem Lab analysieren. Für verwaltete Lab-Typen (beispielsweise Classroom-Labs) steht eine kleinere Teilmenge von Optimierungs- und Analyseoptionen zur Verfügung.
- **Integrierte Sicherheit**. Ein Lab-Besitzer kann ein privates virtuelles Netzwerk und ein Subnetz für ein Lab einrichten und eine gemeinsame öffentliche IP-Adresse aktivieren. Lab-Benutzer können über virtuelle Netzwerke, die mit ExpressRoute oder Site-to-Site-VPNs konfiguriert sind, auf sichere Weise auf Ressourcen zugreifen. (Derzeit nur in DevTest Labs verfügbar.)
- **Integration in Ihre Workflows und Tools**. Azure Lab Services ermöglicht Ihnen, die Labs in die Website- und Verwaltungssysteme Ihres Unternehmens zu integrieren. Mithilfe Ihrer CI/CD-Tools (Continuous Integration/Continuous Deployment) können Sie Umgebungen automatisch bereitstellen. (Derzeit nur in DevTest Labs verfügbar.)

## <a name="scenarios"></a>Szenarien

Hier sind einige der Szenarien aufgeführt, die Azure DevTest Labs und Azure Lab Services unterstützen:

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Einrichtung eines Computerlabors mit anpassbarer Größe in der Cloud für Ihren Classroom  

- Erstellen Sie ein verwaltetes Classroom-Lab. Geben Sie einfach an, was Sie benötigen. Der Dienst erstellt und verwaltet daraufhin automatisch die Infrastruktur des Labs, sodass Sie sich auf das Unterrichten konzentrieren können und sich nicht um die technischen Details eines Labs kümmern müssen.
- Stellen Sie den Teilnehmern ein Lab mit virtuellen Computern zur Verfügung, die exakt für ihre Anforderungen konfiguriert sind. Weisen Sie jedem Teilnehmer eine begrenzte Anzahl von Stunden für die Nutzung der VMs für die Unterrichtsarbeit zu.  
- Verlagern Sie das physische Computerlabor Ihrer Einrichtung in die Cloud. Skalieren Sie die VM-Anzahl automatisch bis zur maximalen Auslastung und Kostenschwelle, die Sie für das Lab festgelegt haben.
- Löschen Sie das Lab mit nur einem Mausklick, wenn Sie es nicht mehr benötigen.

### <a name="use-devtest-labs-for-development-environments"></a>Verwenden von DevTest Labs für Entwicklungsumgebungen

Azure DevTest Labs kann zum Implementieren vieler wichtiger Szenarien verwendet werden. Eines der primären Szenarien stellt aber die Verwendung von DevTest Labs zum Hosten von Entwicklungscomputern für Entwickler dar. In diesem Szenario bietet DevTest Labs folgende Vorteile:

- Ermöglichen, dass Entwickler ihre Entwicklungscomputer nach Bedarf bereitstellen können.
- Bereitstellen von Linux- und Windows-Umgebungen mithilfe wiederverwendbarer Vorlagen und Artefakte.
- Entwickler können ihre Entwicklungscomputer bei Bedarf einfach anpassen.
- Administratoren können die Kosten steuern, indem sie sicherstellen, dass Entwickler nicht mehr virtuelle Computer erhalten, als sie benötigen.
- Administratoren können sicherstellen, dass virtuelle Computer heruntergefahren werden, wenn sie nicht verwendet werden.

Weitere Informationen finden Sie unter [Verwenden von DevTest Labs für die Entwicklung](devtest-lab-developer-lab.md).

### <a name="use-devtest-labs-for-test-environments"></a>Verwenden von DevTest Labs für Testumgebungen

Mit Azure DevTest Labs lassen sich zahlreiche Schlüsselszenarien implementieren. Ein primäres Szenario ist jedoch das Hosten von Computern für Tester. In diesem Szenario bietet DevTest Labs folgende Vorteile:

- Tester können schnell Windows- und Linux-Umgebungen mit wiederverwendbaren Vorlagen und Artefakten bereitstellen und so die neueste Version ihrer Anwendung testen.
- Tester können ihre Auslastungstests durch Bereitstellen mehrerer Test-Agents hochskalieren.
- Administratoren können die Kosten steuern, indem sie sicherstellen, dass Tester nicht mehr virtuelle Computer erhalten, als sie benötigen.
- Administratoren können sicherstellen, dass virtuelle Computer heruntergefahren werden, wenn sie nicht verwendet werden.

Weitere Informationen finden Sie unter [Verwenden von DevTest Labs für Tests](devtest-lab-test-env.md).

## <a name="types-of-labs"></a>Arten von Labs
Sie können zwei Arten von Labs erstellen: **verwaltete Lab-Typen** mit Azure Lab Services und **Labs** mit Azure Lab Services. Wenn Sie lediglich Ihre Lab-Anforderungen eingeben und die Einrichtung und Verwaltung der dafür erforderlichen Infrastruktur dem Dienst überlassen möchten, können Sie einen der **verwalteten Lab-Typen** wählen. Derzeit können mit Azure Lab Services nur verwaltete Lab-Typen der Art **Classroom-Lab** erstellt werden. Wenn Sie Ihre eigene Infrastruktur verwalten möchten, können Sie mithilfe von **Azure DevTest Labs** ein Lab erstellen.

Ausführlichere Informationen zu diesen Labs finden Sie in den folgenden Abschnitten. 

## <a name="managed-lab-types"></a>Verwaltete Labtypen
Mit Azure Lab Services können Sie Labs erstellen, deren Infrastruktur von Azure verwaltet wird. In diesem Artikel werden sie als verwaltete Lab-Typen bezeichnet. Verwaltete Lab-Typen umfassen verschiedene Arten von Labs für Ihre individuellen Anforderungen. Derzeit wird das **Classroom-Lab** als einziger verwalteter Lab-Typ unterstützt. 

Verwaltete Lab-Typen zeichnen sich durch ihren geringen Einrichtungsaufwand aus und sind schnell einsatzbereit. Der Dienst übernimmt die gesamte Infrastrukturverwaltung für das Lab – vom Hochfahren der virtuellen Computer über die Fehlerbehandlung bis hin zur Skalierung der Infrastruktur.  Wenn Sie einen verwalteten Lab-Typ, z. B. ein Classroom-Lab, erstellen möchten, müssen Sie zunächst ein Lab-Konto für Ihre Organisation erstellen. Das Lab-Konto fungiert als zentrales Konto, unter dem alle Labs der Organisation verwaltet werden. 

Wenn Sie Azure-Ressourcen unter diesen verwalteten Lab-Typen erstellen und verwenden, erstellt und verwaltet der Dienst Ressourcen in internen Microsoft-Abonnements. Ressourcen werden nicht in Ihrem eigenen Azure-Abonnement erstellt. Der Dienst überwacht die Nutzung dieser Ressourcen in internen Microsoft-Abonnements. Diese Nutzung wird dann über Ihr Azure-Abonnement abgerechnet, dem das Lab-Konto angehört.   

Im Anschluss finden Sie einige **Anwendungsfälle für verwaltete Lab-Typen**: 

- Stellen Sie den Teilnehmern ein Lab mit virtuellen Computern zur Verfügung, die exakt für ihre Anforderungen konfiguriert sind. Weisen Sie jedem Teilnehmer eine begrenzte Anzahl von VM-Stunden für Hausaufgaben oder persönliche Projekte zu.
- Richten Sie einen Pool mit hochleistungsfähigen virtuellen Computern für rechen- oder grafikintensive Untersuchungen ein. Führen Sie die virtuellen Computer nach Bedarf aus, und bereinigen Sie sie anschließend. 
- Verlagern Sie das physische Computerlabor Ihrer Einrichtung in die Cloud. Skalieren Sie die VM-Anzahl automatisch bis zur maximalen Auslastung und Kostenschwelle, die Sie für das Lab festgelegt haben.  
- Stellen Sie schnell ein Lab mit virtuellen Computern für einen Hackathon bereit. Löschen Sie das Lab mit nur einem Mausklick, wenn Sie es nicht mehr benötigen. 


## <a name="devtest-labs"></a>DevTest Labs
In bestimmten Szenarien kann es wünschenswert sein, die gesamte Infrastruktur und Konfiguration selbst innerhalb des eigenen Abonnements zu verwalten. Zu diesem Zweck können Sie über das Azure-Portal mithilfe von Azure DevTest Labs ein Lab erstellen. Für diese Labs muss kein Lab-Konto erstellt werden. Diese Labs werden nicht im Lab-Konto für die verwalteten Lab-Typen angezeigt.  

Im Anschluss finden Sie einige **Anwendungsfälle für DevTest Labs**: 

- Stellen Sie schnell ein Lab mit virtuellen Computern für einen Hackathon oder eine Praxiseinheit für eine Konferenz bereit. Löschen Sie das Lab mit nur einem Mausklick, wenn Sie es nicht mehr benötigen. 
- Erstellen Sie einen Pool mit virtuellen Computern, die mit Ihrer Anwendung konfiguriert sind, und ermöglichen Sie Ihrem Team ganz einfach die Verwendung eines virtuellen Computers für die Fehlerbeseitigung.  
- Stellen Sie Entwicklern virtuelle Computer zur Verfügung, die mit allen benötigten Tools konfiguriert sind. Planen Sie das automatische Starten und Herunterfahren, um die Kosten zu minimieren. 
- Erstellen Sie im Rahmen Ihrer Bereitstellung wiederholt ein Lab mit Testcomputern. Testen Sie Ihre neuesten Komponenten, und bereinigen Sie die Testcomputer, wenn Sie sie nicht mehr benötigen. 
- Richten Sie mehrere unterschiedlich konfigurierte virtuelle Computer und mehrere Test-Agents für Skalierungs- und Leistungstests ein. 
- Verwenden Sie ein Lab, das mit der neuesten Version Ihres Produkts konfiguriert ist, um Schulungen für Kunden anzubieten. Geben Sie jedem Kunden ein begrenztes Zeitkontingent für die Lab-Verwendung. 


## <a name="managed-lab-types-vs-devtest-labs"></a>Verwaltete Labtypen im Vergleich zu DevTest Labs
In der folgenden Tabelle werden die beiden Arten von Labs verglichen, die von Azure Lab Services unterstützt werden: 

| Features | Verwaltete Labtypen | DevTest Labs |
| -------- | ----------------- | ---------- |
| Verwaltung der Azure-Infrastruktur im Lab |  Automatisch durch den Dienst | Manuelle Verwaltung  |
| Integrierte Resilienz gegen Infrastrukturprobleme | Automatisch durch den Dienst | Manuelle Verwaltung  |
| Abonnementverwaltung | Der Dienst übernimmt die Zuteilung von Ressourcen in Microsoft-Abonnements, die dem Dienst zugrunde liegen. Die Skalierung wird automatisch vom Dienst durchgeführt. | Die Verwaltung muss manuell in Ihrem eigenen Azure-Abonnement durchgeführt werden. Abonnements werden nicht automatisch skaliert. |
| Azure Resource Manager-Bereitstellung innerhalb des Labs | Nicht verfügbar | Verfügbar |

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in folgenden Artikeln: 

- [Informationen zu Classroom-Labs](../lab-services/classroom-labs-overview.md)
- [Informationen zu DevTest Labs](devtest-lab-overview.md)
