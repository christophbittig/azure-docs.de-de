---
title: Berechtigungen und Sicherheit für Azure Chaos Studio
description: Erfahren Sie, wie Berechtigungen in Azure Chaos Studio funktionieren und wie Sie Ressourcen vor versehentlicher Einschleusung von Fehlern schützen können.
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: fa547771b125b17d05e6a615f01cecc899cba7e0
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132372206"
---
# <a name="permissions-and-security-in-azure-chaos-studio"></a>Berechtigungen und Sicherheit in Azure Chaos Studio

Das Azure Chaos Studio ermöglicht es Ihnen, die Resilienz von Diensten zu verbessern, indem Sie systematisch Fehler in Ihre Azure-Ressourcen einschleusen. Die Einschleusung von Fehlern ist eine effiziente Möglichkeit zur Verbesserung der Resilienz von Diensten, sie kann aber auch gefährlich sein. Das Verursachen von Fehlern in Ihrer Anwendung kann mehr Auswirkungen haben als ursprünglich vorgesehen und böswilligen Akteuren die Möglichkeit bieten, Ihre Anwendungen zu infiltrieren. Chaos Studio verfügt über ein starkes Berechtigungsmodell, das verhindert, dass Fehler unbeabsichtigt oder von einem böswilligen Akteur ausgeführt werden. In diesem Abschnitt erfahren Sie, wie Sie Ressourcen schützen können, die für die Einschleusung von Fehlern mit Chaos Studio vorgesehen sind.

## <a name="how-can-i-restrict-the-ability-to-inject-faults-with-chaos-studio"></a>Wie kann ich die Möglichkeiten einschränken, Fehler mit Chaos Studio einzuschleusen?

Chaos Studio verfügt über drei Sicherheitsebenen, mit denen Sie steuern können, wie und wann Fehler für eine Ressource eingeschleust werden können.

Erstens ist ein Chaos-Experiment eine Azure-Ressource, die in einer Region, einer Ressourcengruppe und einem Abonnement bereitgestellt wird. Benutzer müssen über die entsprechenden Azure Resource Manager-Berechtigungen zum Erstellen, Aktualisieren, Starten, Abbrechen, Löschen oder Anzeigen eines Experiments verfügen. Jede Berechtigung ist ein ARM-Vorgang, der präzise einer Identität oder im Rahmen einer Rolle mit Platzhalterberechtigungen zugewiesen werden kann. Die Rolle „Mitwirkender“ in Azure verfügt beispielsweise über die Berechtigung „*/write“ im zugewiesenen Bereich, die die Berechtigung „Microsoft.Chaos/experiments/write“ umfasst. Beim Versuch, die Möglichkeit zum Einschleusen von Fehlern in eine Ressource zu steuern, ist der wichtigste einzuschränkende Vorgang „Microsoft.Chaos/experiments/start/action“, da dieser Vorgang ein Chaos-Experiment startet, das Fehler einschleust.

Zweitens verfügt ein Chaos-Experiment über eine [systemseitig zugewiesene verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md), die Fehler für eine Ressource ausführt. Wenn Sie ein Experiment erstellen, wird die systemseitig zugewiesene verwaltete Identität ohne Berechtigungen in Ihrem Azure Active Directory-Mandanten erstellt. Bevor Sie Ihr Chaos-Experiment ausführen, müssen Sie der jeweiligen Identität [entsprechende Berechtigungen](chaos-studio-fault-providers.md) für alle Zielressourcen erteilen. Wenn die Experimentidentität nicht über die entsprechende Berechtigung für eine Ressource verfügt, kann sie keinen Fehler für diese Ressource ausführen.

Schließlich muss für jede Ressource ein Onboarding als [Ziel mit entsprechenden aktivierten Funktionen](chaos-studio-targets-capabilities.md) in Chaos Studio durchgeführt werden. Wenn ein Ziel oder die Funktion für den ausgeführten Fehler nicht vorhanden ist, schlägt das Experiment ohne Auswirkung auf die Ressource fehl.

## <a name="agent-authentication"></a>Agent-Authentifizierung

Wenn agentenbasierte Fehler ausgeführt werden, müssen Sie den Chaos Studio-Agent auf Ihrem virtuellen Computer oder ihrer VM-Skalierungsgruppe installieren. Der Agent verwendet eine [benutzerseitig zugewiesene verwaltete Identität](../active-directory/managed-identities-azure-resources/overview.md) für die Authentifizierung bei Chaos Studio und ein *Agentprofil*, um eine Beziehung zu einer bestimmten VM-Ressource herzustellen. Beim Onboarding eines virtuellen Computers oder einer VM-Skalierungsgruppe für agentbasierte Fehler erstellen Sie zunächst ein Agentziel. Das Agentziel muss über einen Verweis auf die benutzerseitig zugewiesene verwaltete Identität verfügen, die für die Authentifizierung verwendet wird. Das Agentziel enthält eine *Agentprofil-ID*, die bei der Installation des Agents als Konfiguration bereitgestellt wird. Agentprofile sind für jedes Ziel eindeutig, und Ziele sind für jede Ressource eindeutig.

## <a name="arm-operations-and-roles"></a>ARM-Vorgänge und -Rollen

Chaos Studio umfasst die folgenden Vorgänge:

| Vorgang | BESCHREIBUNG |
| -- | -- |
| Microsoft.Chaos/targets/[Read,Write,Delete] | Ziel abrufen, erstellen, aktualisieren oder löschen. |
| Microsoft.Chaos/targets/capabilities/[Read,Write,Delete] | Funktion abrufen, erstellen, aktualisieren oder löschen. |
| Microsoft.Chaos/locations/targetTypes/Read | Alle Zieltypen abrufen. |
| Microsoft.Chaos/locations/targetTypes/capabilityTypes/Read | Alle Funktionstypen abrufen. |
| Microsoft.Chaos/experiments/[Read,Write,Delete] | Chaos-Experiment abrufen, erstellen, aktualisieren oder löschen. |
| Microsoft.Chaos/experiments/start/action | Chaos-Experiment starten. |
| Microsoft.Chaos/experiments/cancel/action | Chaos-Experiment beenden. |
| Microsoft.Chaos/experiments/statuses/Read | Ausführungsstatus für eine Ausführung eines Chaos-Experiments abrufen. |
| Microsoft.Chaos/experiments/executionDetails/Read | Ausführungsdetails (Status und Fehler für jede Aktion) für eine Ausführung eines Chaos-Experiments abrufen. |

Um diese Berechtigungen präzise zu zuweisen, können Sie [eine benutzerdefinierte Rolle erstellen](../role-based-access-control/custom-roles.md).

## <a name="network-security"></a>Netzwerksicherheit

Alle Benutzerinteraktionen mit Chaos Studio erfolgen über Azure Resource Manager. Wenn ein Benutzer ein Experiment startet, kann das Experiment abhängig vom jeweiligen Fehler mit anderen Endpunkten als Resource Manager interagieren.
* Dienstspezifische Fehler: Die meisten dienstspezifischen Fehler werden über Azure Resource Manager ausgeführt. Für Zielressourcen sind keine Netzwerkendpunkte in einer Positivliste erforderlich.
* Dienstspezifische AKS Chaos Mesh-Fehler: Dienstspezifische Fehler für Azure Kubernetes Service, die Chaos Mesh verwenden, setzen voraus, dass der AKS-Cluster über einen öffentlich verfügbar gemachten Kubernetes-API-Server verfügt. [Hier erfahren Sie, wie Sie den AKS-Netzwerkzugriff auf bestimmte IP-Adressbereiche beschränken.](../aks/api-server-authorized-ip-ranges.md)
* Agentbasierte Fehler: Agentbasierte Fehler erfordern Agentzugriff auf den Chaos Studio-Agentdienst. Ein virtueller Computer oder eine VM-Skalierungsgruppe muss über ausgehenden Zugriff auf http://agentcommunicationservice-frontdoor-canary.trafficmanager.net verfügen, damit der Agent erfolgreich eine Verbindung herstellen kann.

Azure Chaos Studio unterstützt keine Diensttags und keine privaten Verbindungen.

## <a name="data-encryption"></a>Datenverschlüsselung

Chaos Studio verschlüsselt standardmäßig alle Daten. Chaos Studio akzeptiert nur Eingaben für Systemeigenschaften wie Objekt-IDs der verwalteten Identität, Experiment-/Schritt-/Branchnamen und Fehlerparameter (z. B. der Netzwerkportbereich, der bei einem Fehler beim Trennen der Netzwerkverbindung blockiert werden soll). Diese Eigenschaften sollten nicht zum Speichern vertraulicher Daten wie Zahlungsinformationen oder Kennwörter verwendet werden. Weitere Informationen zum Schutz Ihrer Daten durch Chaos Studio finden Sie im [Artikel zum Schutz von Azure-Kundendaten](../security/fundamentals/protection-customer-data.md).

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie nun wissen, wie Sie Ihr Chaos-Experiment schützen können, können Sie folgende Aufgaben durchführen:
- [Erstellen und Ausführen des ersten Experiments](chaos-studio-tutorial-service-direct-portal.md)
- [Erstellen und Ausführen Ihres ersten Azure Kubernetes Service-Experiments](chaos-studio-tutorial-aks-portal.md)
