---
title: Sicherheitskonformität mit Azure Policy und Azure Blueprints
description: Gewährleisten der Konformität und Erzwingen der Sicherheit mit Azure Policy und Azure Blueprints für australische Regierungsbehörden in Bezug auf ASD ISM und Essential 8
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: 79ee7b4a0d21322e8c2e497eecd1704356282eba
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/10/2021
ms.locfileid: "122353636"
---
# <a name="security-compliance-with-azure-policy-and-azure-blueprints"></a>Sicherheitskonformität mit Azure Policy und Azure Blueprints

Die Herausforderung, Governance in der IT-Umgebung zu erzwingen, sei es in einer lokalen, einer cloudnativen oder einer Hybridumgebung, stellt sich für alle Organisationen. Ein stabiles technisches Governanceframework muss vorhanden sein, um sicherzustellen, dass Ihre Microsoft Azure-Umgebung den Entwurfs-, regulatorischen und Sicherheitsanforderungen entspricht.

Für australische Regierungsbehörden sind die wichtigsten Kontrollen, die bei der Risikobewertung zu berücksichtigen sind, im [Information Security Manual (ISM) des Australian Cyber Security Centre (ACSC)](https://acsc.gov.au/infosec/ism/index.htm) enthalten. Für den Großteil der im ISM aufgeführten Kontrollen ist es erforderlich, dass die Anwendung der technischen Governance effektiv verwaltet und erzwungen wird. Dazu müssen Sie über die entsprechenden Tools zur Auswertung und Erzwingung der Konfiguration in Ihren Umgebungen verfügen.

Zu diesem Zweck umfasst Microsoft Azure zwei kostenlose Dienste: Azure Policy und Azure Blueprints.

## <a name="azure-policy"></a>Azure Policy

Azure Policy ermöglicht die Anwendung der technischen Elemente der IT-Governance einer Organisation. Azure Policy enthält eine ständig wachsende Bibliothek integrierter Richtlinien. Jede Richtlinie erzwingt Regeln und Auswirkungen für die Azure-Zielressourcen.

Nach der Zuweisung einer Richtlinie zu den Ressourcen kann die allgemeine Konformität für diese Richtlinie ausgewertet und bei Bedarf korrigiert werden.

Mit dieser Bibliothek integrierter Azure-Richtlinien kann eine Organisation die im ACSC ISM genannten Arten von Kontrollen schnell erzwingen. Beispiele für Kontrollen:

* Überwachen von virtuellen Computern auf fehlende Systemupdates
* Überprüfen von Konten mit erhöhten Berechtigungen auf mehrstufige Authentifizierung
* Identifizieren nicht verschlüsselter SQL-Datenbanken
* Überwachen der Verwendung der benutzerdefinierten rollenbasierten Zugriffssteuerung in Azure (Azure RBAC)
* Einschränken der Azure-Regionen, in denen Ressourcen erstellt werden können

Wenn Governance- oder behördliche Kontrollen durch eine integrierte Azure Policy-Definition nicht erfüllt werden, kann eine benutzerdefinierte Definition erstellt und zugewiesen werden. Alle Azure Policy-Definitionen werden in JSON definiert und entsprechen einer standardmäßigen [Definitionsstruktur](../governance/policy/concepts/definition-structure.md). Vorhandene Azure Policy-Definitionen können auch kopiert und als Grundlage für eine benutzerdefinierte Policy-Definition verwendet werden.

Die Zuweisung einzelner Azure-Richtlinien zu Ressourcen, insbesondere in komplexen Umgebungen oder in Umgebungen mit strengen regulatorischen Anforderungen, kann zu einem großen Aufwand für die Administratoren führen. Zur Unterstützung bei diesen Herausforderungen können mehrere Azure-Richtlinien in einer Azure Policy-Initiative zusammengefasst werden. Mit Policy-Initiativen werden zusammengehörige Azure-Richtlinien kombiniert. Wenn diese Richtlinien zusammen als Gruppe angewandt werden, bilden sie die Grundlage eines bestimmten Sicherheits- oder Konformitätsziels. Microsoft fügt integrierte Definitionen für Azure Policy-Initiativen hinzu, darunter Definitionen für spezifische regulatorische Anforderungen:

![Policy-Initiativen zur Einhaltung gesetzlicher Bestimmungen](media/regulatory-initiatives.png)

Alle Azure-Richtlinien und Azure-Initiativen werden einem Zuweisungsbereich zugewiesen. Dieser Bereich wird entweder im Azure-Abonnement oder auf Ebene der Azure-Verwaltungsgruppen bzw. der Azure-Ressourcengruppen definiert. Nach der Zuweisung der entsprechenden Azure-Richtlinien oder Policy-Initiativen kann eine Organisation die Konfigurationsanforderungen für alle neu erstellten Azure-Ressourcen erzwingen.

Die Zuweisung einer neuen Azure-Richtlinie oder Initiative wirkt sich nicht auf die vorhandenen Azure-Ressourcen aus. Mithilfe von Azure Policy kann eine Organisation jedoch die Konformität vorhandener Azure-Ressourcen anzeigen. Alle Ressourcen, die als nicht konform identifiziert werden, können nach Wahl der Organisation korrigiert werden.

### <a name="azure-policy-and-initiatives-in-action"></a>Azure Policy und Initiativen in Aktion

Die verfügbaren integrierten Azure Policy-Definitionen und Initiativendefinitionen finden Sie unter dem Knoten „Definitionen“ im Abschnitt „Policy“ des Azure-Portals:

![Integrierte Azure Policy-Definitionen](media/policy-definitions.png)

Mithilfe der Bibliothek der integrierten Definitionen können Sie schnell nach Richtlinien suchen, die eine Anforderung der Organisation erfüllen, die Richtliniendefinition überprüfen und die Richtlinie den entsprechenden Ressourcen zuweisen. Das ISM erfordert beispielsweise die mehrstufige Authentifizierung (MFA) für alle privilegierten Benutzer und für alle Benutzer mit Zugriff auf wichtige Datenrepositorys. In Azure Policy können Sie in den Azure Policy-Definitionen nach „MFA“ suchen:

![Azure AD MFA-Richtlinien](media/mfa-policies.png)

Nachdem Sie eine geeignete Richtlinie gefunden haben, können Sie sie dem gewünschten Bereich zuweisen. Wenn keine integrierte Richtlinie vorhanden ist, die Ihren Anforderungen entspricht, können Sie die vorhandene Richtlinie duplizieren und die gewünschten Änderungen vornehmen:

![Duplizieren einer vorhandenen Azure-Richtlinie](media/duplicate-policy.png)

Außerdem bietet Microsoft in [GitHub](https://github.com/Azure/azure-policy) eine Sammlung von Azure Policy-Beispielen als „Schnellstart“ für die Erstellung benutzerdefinierter Azure-Richtlinien an. Diese Richtlinienbeispiele können direkt in den Azure Policy-Editor im Azure-Portal kopiert werden.

Beim Erstellen von Azure Policy-Initiativen können Sie die Liste der verfügbaren integrierten sowie benutzerdefinierten Richtliniendefinitionen sortieren und die gewünschten Definitionen hinzufügen.

Beispielsweise können Sie die Liste der verfügbaren Azure Policy-Definitionen nach allen Richtlinien für virtuelle Windows-Computer durchsuchen. Dann können Sie diese Definitionen in einer Initiative kombinieren, die für die Erzwingung der empfohlenen Methoden zum Absichern von virtuellen Computern konzipiert ist:

![Liste der Azure-Richtlinien](media/initiative-definitions.png)

Beim Zuweisen einer Azure-Richtlinie oder Policy-Initiative zu einem Zuweisungsbereich können Sie Azure-Ressourcen von den Auswirkungen der Richtlinien ausschließen, indem Sie entweder Azure-Verwaltungsgruppen oder Azure-Ressourcengruppen ausschließen.

### <a name="real-time-enforcement-and-compliance-assessment"></a>Erzwingung in Echtzeit und Compliancebewertung

Azure Policy-Konformitätsprüfungen von im Gültigkeitsbereich liegenden Azure-Ressourcen werden durchgeführt, wenn die folgenden Bedingungen erfüllt sind:

* Wenn eine Azure-Richtlinie oder Azure Policy-Initiative zugewiesen wird
* Wenn der Bereich einer vorhandenen Azure-Richtlinie oder Initiative geändert wird
* Bedarfsgesteuert über die API bis zu maximal 10 Prüfungen pro Stunde
* Alle 24 Stunden (Standardverhalten)

Eine Konformitätsprüfung der Richtlinien für eine einzelne Azure-Ressource wird 15 Minuten nach einer an der Ressource vorgenommenen Änderung durchgeführt.

Eine Übersicht über die Azure Policy-Konformität von Ressourcen finden Sie im Azure-Portal auf dem Dashboard für die Richtlinienkompatibilität:

![Azure Policy – Compliancebewertung](media/simple-compliance.png)

Die Prozentzahl für die gesamte Ressourcenkonformität ist eine Zusammenfassung der Compliance aller im Gültigkeitsbereich liegenden bereitgestellten Ressourcen im Vergleich zu allen Ihren zugewiesenen Azure-Richtlinien. So können Sie die nicht konformen Ressourcen in einer Umgebung ermitteln und einen Plan zur bestmöglichen Korrektur dieser Ressourcen ausarbeiten.

Das Dashboard für die Richtlinienkompatibilität enthält außerdem den Änderungsverlauf für die einzelnen Ressourcen. Wenn eine Ressource als nicht mehr konform mit der zugewiesenen Richtlinie identifiziert wird und die automatische Wartung nicht aktiviert ist, können Sie anzeigen, wer die Änderung vorgenommen hat, was geändert wurde und wann die Änderungen an der entsprechenden Ressource vorgenommen wurden.

## <a name="azure-blueprints"></a>Azure Blueprint

Mit Azure Blueprints werden die Funktionen von Azure Policy durch Kombination mit folgenden Funktionen erweitert:

* Azure RBAC
* Azure-Ressourcengruppen
* [Azure Resource Manager-Vorlagen](../azure-resource-manager/templates/syntax.md)

Mit Blaupausen wird die Erstellung von Umgebungsentwürfen ermöglicht, bei denen Azure-Ressourcen über Resource Manager-Vorlagen bereitgestellt werden, Azure RBAC konfiguriert wird sowie die Konfiguration durch Zuweisen von Azure-Richtlinien erzwungen und überprüft wird. Blaupausen bilden eine bearbeitbare und erneut bereitstellbare Umgebungsvorlage. Nachdem eine Blaupause erstellt wurde, kann sie einem Azure-Abonnement zugewiesen werden. Nach der Zuweisung werden alle in der Blaupause definierten Azure-Ressourcen erstellt und die Azure-Richtlinien angewandt. Die Bereitstellung und Konfiguration von Ressourcen, die in einer Azure-Blaupause definiert sind, können über die Azure Blueprints-Konsole im Azure-Portal überwacht werden.

Azure-Blaupausen, die bearbeitet wurden, müssen im Azure-Portal erneut veröffentlicht werden. Bei jeder erneuten Veröffentlichung einer Blaupause wird die zugehörige Versionsnummer erhöht. Über die Versionsnummer lässt sich feststellen, welche spezifische Version einer Blaupause in den Azure-Abonnements einer Organisation bereitgestellt wurde. Bei Bedarf kann die aktuell zugewiesene Version der Blaupause auf die neueste Version aktualisiert werden.

Ressourcen, die mit einer Azure-Blaupause bereitgestellt werden, können bei der Bereitstellung mit [Azure-Ressourcensperren](../azure-resource-manager/management/lock-resources.md) konfiguriert werden. Ressourcensperren verhindern, dass Ressourcen versehentlich geändert oder gelöscht werden.

Microsoft entwickelt Azure-Blaupausenvorlagen für verschiedene Branchen und regulatorische Anforderungen. Die aktuelle Bibliothek der verfügbaren Azure-Blaupausendefinitionen kann im Azure-Portal oder auf der Seite [Azure-Blaupause für Sicherheit und Compliance](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview/) im Service Trust-Portal angezeigt werden.

### <a name="azure-blueprint-artifacts"></a>Azure-Blaupausenartefakte

Zum Erstellen einer Azure-Blaupause können Sie eine leere Blaupausenvorlage oder eine der vorhandenen Beispielblaupausen als Ausgangspunkt verwenden. Sie können der Blaupause Artefakte hinzufügen, die als Teil der Bereitstellung konfiguriert werden:

![Azure-Blaupausenartefakte](media/blueprint-artifacts.png)

Diese Artefakte können die Azure-Ressourcengruppe und Azure-Ressourcen sowie die zugehörigen Azure-Richtlinien und Policy-Initiativen umfassen, mit denen die Konfiguration erzwungen wird, die erforderlich ist, damit die Umgebung konform ist mit Ihren regulatorischen Anforderungen (z. B. den ISM-Kontrollen für die Systemabsicherung).

Jedes dieser Artefakte kann auch mit Parametern konfiguriert werden. Diese Werte werden bereitgestellt, wenn die Blaupause einem Azure-Abonnement zugewiesen und bereitgestellt wurde. Mithilfe von Parametern kann eine einzelne Blaupause erstellt und zum Bereitstellen von Ressourcen in verschiedenen Umgebungen verwendet werden, ohne dass die zugrunde liegende Blaupause bearbeitet werden muss.

Microsoft entwickelt Azure PowerShell- und Azure CLI-Cmdlets zum Erstellen und Verwalten von Azure-Blaupausen mit dem Ziel, dass eine Blaupause von einer Organisation über eine CI/CD-Pipeline verwaltet und bereitgestellt werden kann.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde erläutert, wie Governance und Sicherheit mit Azure Policy und Azure Blueprints erzwungen werden können. Nachdem Sie nun allgemein mit diesen Diensten vertraut sind, finden Sie in den folgenden Artikeln detailliertere Informationen zur Verwendung jedes Diensts:

* [Azure Policy – Übersicht](../governance/policy/overview.md)
* [Azure Blueprints – Übersicht](https://azure.microsoft.com/services/blueprints/)
* [Azure Policy-Beispiele](../governance/policy/samples/index.md)
* [Repository mit Azure Policy-Beispielen](https://github.com/Azure/azure-policy)
* [Struktur von Azure Policy-Definitionen](../governance/policy/concepts/definition-structure.md)
* [Azure Policy-Auswirkungen](../governance/policy/concepts/effects.md)