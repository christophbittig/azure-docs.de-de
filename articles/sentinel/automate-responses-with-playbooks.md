---
title: Automatisieren der Bedrohungsabwehr mit Playbooks in Microsoft Sentinel | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die Automatisierung in Microsoft Sentinel und zeigt, wie Sie Playbooks zur Automatisierung von Bedrohungsabwehr und -reaktion verwenden können.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 34b1400b2d3e4b69b83c572517df230380bc2a91
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522135"
---
# <a name="automate-threat-response-with-playbooks-in-microsoft-sentinel"></a>Automatisieren der Bedrohungsabwehr mit Playbooks in Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Artikel wird erklärt, was Microsoft Sentinel-Playbooks sind und wie Sie sie für die Implementierung Ihrer SOAR-Vorgänge (Security Orchestration, Automation and Response) verwenden, um bessere Ergebnisse zu erzielen und gleichzeitig Zeit und Ressourcen zu sparen.

## <a name="what-is-a-playbook"></a>Was ist ein Playbook?

SIEM/SOC-Teams werden regelmäßig mit Sicherheitswarnungen und -vorfällen überschwemmt, und zwar in einem so großen Umfang, dass das verfügbare Personal überfordert ist. Dies führt häufig dazu, dass viele Alarme ignoriert und viele Incidents nicht untersucht werden, wodurch ein Unternehmen anfällig für Angriffe wird, die unbemerkt bleiben.

Viele – wenn nicht sogar der Großteil – dieser Warnungen und Incidents entsprechen wiederkehrenden Mustern, die mithilfe spezifischer, definierter Wartungsaktionen verarbeitet werden können.

Ein Playbook ist eine Sammlung dieser Wartungsmaßnahmen, die von Microsoft Sentinel als Routine ausgeführt werden können. Ein Playbook kann dabei helfen, Ihre [**Bedrohungsreaktion zu automatisieren und zu orchestrieren**](tutorial-respond-threats-playbook.md). Es kann manuell ausgeführt oder so eingestellt werden, dass es automatisch als Reaktion auf bestimmte Alarme oder Incidents ausgeführt wird, wenn es durch eine Analyse- bzw. Automatisierungsregel ausgelöst wird.

Wenn zum Beispiel ein Konto und ein Computer kompromittiert sind, kann ein Playbook den Computer vom Netzwerk isolieren und das Konto sperren, bis das SOC-Team über den Incident informiert wird.

Playbooks können innerhalb des Abonnements verwendet werden, zu dem sie gehören. Auf der Registerkarte **Playbooks** (auf dem Blatt **Automatisierung**) werden jedoch alle Playbooks angezeigt, die für alle ausgewählten Abonnements verfügbar sind.

### <a name="playbook-templates"></a>Playbookvorlagen

> [!IMPORTANT]
>
> **Playbookvorlagen** befinden sich derzeit in der **VORSCHAUPHASE**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Eine Playbookvorlage ist ein vordefinierter, getesteter und einsatzbereiter Workflow, der an Ihre Anforderungen angepasst werden kann. Vorlagen können auch als Referenz für bewährte Methoden bei der Entwicklung vollkommen neuer Playbooks oder als Anregung für neue Automatisierungsszenarien dienen.

Playbookvorlagen sind selbst keine aktiven Playbooks, bis Sie daraus ein Playbook (eine bearbeitbare Kopie der Vorlage) erstellen.

Sie können Playbookvorlagen aus den folgenden Quellen beziehen:

- Auf der Registerkarte **Playbookvorlagen** (unter **Automatisierung**) werden die führenden Szenarien der Microsoft Sentinel-Community angezeigt. Es können mehrere aktive Playbooks aus derselben Vorlage erstellt werden.

    Wenn eine neue Version der Vorlage veröffentlicht wird, werden die aus dieser Vorlage erstellten aktiven Playbooks (auf der Registerkarte **Playbooks**) mit einer Benachrichtigung gekennzeichnet, dass ein Update verfügbar ist.

- Playbookvorlagen können auch als Teil einer [Microsoft Sentinel-Lösung](sentinel-solutions.md) im Kontext eines bestimmten Produkts abgerufen werden. Durch die Bereitstellung der Lösung werden aktive Playbooks erzeugt.

- Das [Microsoft Sentinel-GitHub-Repository](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks) enthält viele Playbookvorlagen. Diese können in einem Azure-Abonnement bereitgestellt werden, indem auf die Schaltfläche **In Azure bereitstellen** geklickt wird.

Technisch gesehen ist eine Playbookvorlage eine [ARM-Vorlage](../azure-resource-manager/templates/index.yml), die aus mehreren Ressourcen besteht: einem Azure Logic Apps-Workflow und API-Verbindungen für jede einbezogene Verbindung.

### <a name="azure-logic-apps-basic-concepts"></a>Azure Logic Appspps Grundlegende Konzepte

Playbooks in Microsoft Sentinel basieren auf Workflows, die in [Azure Logic Apps](../logic-apps/logic-apps-overview.md) erstellt wurden, einem Clouddienst, mit dem Sie Aufgaben und Workflows systemübergreifend im gesamten Unternehmen planen, automatisieren und orchestrieren können. Das bedeutet, dass Playbooks von der gesamten Leistungsfähigkeit und Anpassungsfähigkeit der integrierten Vorlagen von Logic Apps profitieren können.

> [!NOTE]
> Da Azure Logic Apps eine separate Ressource sind, können zusätzliche Gebühren anfallen. Ausführlichere Informationen finden Sie auf der Preisseite von [Azure Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps/).

Azure Logic Apps kommuniziert mit anderen Systemen und Diensten über Konnektoren. Im Folgenden finden Sie eine kurze Erläuterung der Konnektoren und einiger ihrer wichtigen Eigenschaften:

- **Verwalteter Konnektor:** Eine Reihe von Aktionen und Auslösern, die API-Aufrufe für ein bestimmtes Produkt oder einen bestimmten Dienst umschließen. Azure Logic Apps bietet Hunderte von Konnektoren für die Kommunikation mit Microsoft-und nicht-Microsoft-Diensten.
  - [Liste aller Logic Apps Konnektoren und ihre Dokumentation](/connectors/connector-reference/)

- **Benutzerdefinierter Konnketor:** Möglicherweise möchten Sie mit Diensten kommunizieren, die nicht als vorgefertigte Konnektoren verfügbar sind. Benutzerdefinierte Konnektoren adressieren diesen Bedarf, indem Sie einen Konnektor erstellen (und sogar freigeben) und seine eigenen Auslöser und Aktionen definieren können.
  - [Erstellen eigener benutzerdefinierter Logic Apps Konnektoren](/connectors/custom-connectors/create-logic-apps-connector)

- **Microsoft Sentinel-Connector:** Zum Erstellen von Playbooks, die mit Microsoft Sentinel interagieren, verwenden Sie den Microsoft Sentinel-Connector.
  - [Dokumentation zum Microsoft Sentinel-Connector](/connectors/azuresentinel/)

- **Auslöser:** Eine Konnektor-Komponente, die ein Playbook startet. Sie definiert das Schema, das das Playbook beim Auslösen erwartet. Der Microsoft Sentinel-Connector verfügt zurzeit über zwei Trigger:
  - [Benachrichtigungs-Trigger](/connectors/azuresentinel/#triggers): Das Playbook erhält die Benachrichtigung als Eingabe.
  - [Incident-Trigger](/connectors/azuresentinel/#triggers): das Playbook empfängt den Incident als Eingabe und alle darin enthaltenen Warnungen und Entitäten.

    > [!IMPORTANT]
    >
    > Die **Incidentauslöserfunktion** für Playbooks befindet sich derzeit in der **VORSCHAU-** Phase. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

- **Aktionen:** Als Aktionen werden alle Schritte bezeichnet, die nach dem Trigger ausgeführt werden. Sie können sequentiell, parallel oder in einer Matrix komplexer Bedingungen angeordnet sein.

- **Dynamische Felder:** Temporäre Felder, die durch das Ausgabeschema von Triggern und Aktionen bestimmt und durch deren tatsächliche Ausgabe befüllt werden, die in den folgenden Aktionen verwendet werden können.

### <a name="permissions-required"></a>Erforderliche Berechtigungen

 Um Ihrem SecOps-Team die Möglichkeit zu geben, Logic Apps für SOAR-Vorgänge (Security Orchestration, Automation und Response) zu verwenden – also Playbooks zu erstellen und auszuführen – können Sie in Microsoft Sentinel Azure-Rollen zuweisen, entweder für bestimmte Mitglieder Ihres Security Operations-Teams oder für das gesamte Team. Im folgenden werden die verschiedenen verfügbaren Rollen und die Aufgaben beschrieben, denen Sie zugewiesen werden sollten:

#### <a name="azure-roles-for-logic-apps"></a>Azure-Rollen für Logic Apps

- Mit **Logic-App Contributor** können Sie Logik-Apps verwalten und Playbooks ausführen, aber Sie können den Zugriff darauf nicht ändern (dafür ist die Rolle **Besitzer** erforderlich.)
- Mit **Logic-App Operator** können Sie Logic-Apps lesen, aktivieren und deaktivieren, aber Sie können sie nicht bearbeiten oder aktualisieren.

#### <a name="azure-roles-for-sentinel"></a>Azure Rollen für Sentinel

- Mit der Rolle **Microsoft Sentinel-Mitwirkender** können Sie ein Playbook an eine Analyseregel anfügen.
- Mit der Rolle **Microsoft Sentinel-Antwortender** können Sie ein Playbook manuell ausführen.
- Als **Mitwirkender für Microsoft Sentinel-Automatisierung** können Sie mit Automatisierungsregeln Playbooks ausführen. Es wird nicht für andere Zwecke genutzt.

#### <a name="learn-more"></a>Erfahren Sie mehr

- [Weitere Informationen zu Azure-Rollen finden Sie unter Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md#access-to-logic-app-operations).
- [Weitere Informationen zu Azure-Rollen finden Sie unter den Berechtigungen in Microsoft Sentinel](roles.md).

## <a name="steps-for-creating-a-playbook"></a>Schritte zum Erstellen eines Playbooks

- [Definieren Sie das Automatisierungsszenario](#use-cases-for-playbooks).

- [Erstellen Sie die Azure-Logik App](tutorial-respond-threats-playbook.md).

- [Testen Sie Ihre Logik-App.](#run-a-playbook-manually-on-an-alert)

- Fügen Sie das Playbook an eine[ Automatisierungsregel](#incident-creation-automated-response) oder eine [Analyseregel](#alert-creation-automated-response) an, oder [führen Sie es bei Bedarf manuell aus](#run-a-playbook-manually-on-an-alert).

### <a name="use-cases-for-playbooks"></a>Anwendungsfälle für Playbooks

Die Azure Logic Apps Plattform bietet Hunderte von Aktionen und Auslösern, sodass nahezu jedes Automatisierungsszenario erstellt werden kann. Microsoft Sentinel empfiehlt, mit den folgenden SOC-Szenarien zu beginnen:

#### <a name="enrichment"></a>Anreicherung

**Sammeln Sie Daten, und fügen Sie an den Incident an, um intelligentere Entscheidungen zu treffen.**

Beispiel:

Ein Microsoft Sentinel-Incident wurde aus einer Warnung durch eine Analyseregel erstellt, die IP-Adressen-Entitäten generiert.

Der Incident löst eine Automatisierungsregel aus, die ein Playbook mit den folgenden Schritten ausführt:

- Starten, wenn ein [neuer Microsoft Sentinel-Incident erstellt wird](/connectors/azuresentinel/#triggers). Die im Vorfall dargestellten Entitäten werden in den dynamischen Feldern des Incident-Triggers gespeichert.

- Für jede IP-Adresse können Sie einen externen Threat Intelligence-Anbieter, z. B. [Virus Total](https://www.virustotal.com/), abfragen, um weitere Daten zu erhalten.

- Fügen Sie die zurückgemeldeten Daten und Erkenntnisse als Kommentare zum Incident hinzu.

#### <a name="bi-directional-sync"></a>Bidirektionale Synchronisierung

**Playbooks können dazu verwendet werden, Ihre Microsoft Sentinel-Incidents mit anderen Ticketausstellungssystemen zu synchronisieren.**

Beispiel:

Erstellen Sie eine Automatisierungsregel für die Erstellung aller Incidents, und fügen Sie ein Playbook an, das ein Ticket in ServiceNow öffnet:

- Starten, wenn ein [neuer Microsoft Sentinel-Incident erstellt wird](/connectors/azuresentinel/#triggers).

- Erstellen Sie ein neues Ticket in [ServiceNow](/connectors/service-now/).

- Fügen Sie in das Ticket den Namen des Incidents, wichtige Felder und eine URL zum Microsoft Sentinel-Incident ein, um die Pivotierung zu erleichtern.

#### <a name="orchestration"></a>Orchestrierung

**Verwenden Sie die SOC-Chat-Plattform, um die Warteschlange der Incidents besser zu überwachen.**

Beispiel:

Ein Microsoft Sentinel-Incident wurde aus einer Warnung durch eine Analyseregel erstellt, die Benutzernamen- und IP-Adressen-Entitäten generiert.

Der Incident löst eine Automatisierungsregel aus, die ein Playbook mit den folgenden Schritten ausführt:

- Starten, wenn ein [neuer Microsoft Sentinel-Incident erstellt wird](/connectors/azuresentinel/#triggers).

- Es wird eine Nachricht an Ihren Sicherheitskanal in [Microsoft Teams](/connectors/teams/) oder [Slack](/connectors/slack/) gesendet, um Ihre Sicherheitsanalysten auf den Vorfall aufmerksam zu machen.

- Senden Sie alle Informationen in der Warnmeldung per E-Mail an Ihren leitenden Netzwerkadministrator und Sicherheitsadministrator. Die E-Mail-Nachricht enthält die Optionsschaltflächen **Blockieren** und Benutzer **ignorieren**.

- Warten Sie, bis Sie eine Antwort von den Admins erhalten, und fahren Sie dann mit der Ausführung fort.

- Wenn die Administratoren **Blockieren** gewählt haben, senden Sie einen Befehl an die Firewall, um die IP-Adresse aus dem Alarm zu blockieren, und einen weiteren an Azure AD, um den Benutzer zu deaktivieren.

#### <a name="response"></a>Antwort

**Sofortige Reaktion auf Bedrohungen, mit minimalem Personalaufwand.**

Zwei Beispiele:

**Beispiel 1:**  Reagieren Sie auf eine Analyseregel, die auf einen kompromittierten Benutzer hinweist, der von [Azure AD Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) entdeckt wurde:

   - Starten, wenn ein [neuer Microsoft Sentinel-Incident erstellt wird](/connectors/azuresentinel/#triggers).

   - Für alle Benutzerentitäten des Incidents, die als kompromittiert eingestuft werden:

     - Senden Sie eine Teams-Nachricht an den Benutzer, in der Sie um Bestätigung bitten, dass der Benutzer die verdächtige Aktion durchgeführt hat.

     - Prüfen Sie mit Azure AD Identity Protection, ob der [Status des Benutzers als kompromittiert gilt](/connectors/azureadip/#confirm-a-risky-user-as-compromised). Azure AD Identity Protection stuft den Benutzer als **riskant** ein und wendet alle bereits konfigurierten Durchsetzungsrichtlinien an - zum Beispiel, dass der Benutzer bei der nächsten Anmeldung eine MFA verwenden muss.

        > [!NOTE]
        > Das Playbook initiiert keine Durchsetzungsmaßnahmen gegenüber dem Benutzer und initiiert auch keine Konfiguration der Durchsetzungsrichtlinie. Azure AD Identity Protection wird lediglich angewiesen, bereits definierte Richtlinien entsprechend anzuwenden. Die Durchsetzung hängt vollständig davon ab, dass die entsprechenden Richtlinien in Azure AD Identity Protection definiert sind.

**Beispiel 2:** Reagieren Sie auf eine Analyseregel, die auf einen kompromittierten Rechner hinweist, der von [Microsoft Defender for Endpoint](/windows/security/threat-protection/) entdeckt wurde:

   - Starten, wenn ein [neuer Microsoft Sentinel-Incident erstellt wird](/connectors/azuresentinel/#triggers).

   - Verwenden Sie die Aktion **Entitäten – Hosts abrufen** in Microsoft Sentinel, um die verdächtigen Computer zu analysieren, die in den Incidententitäten enthalten sind.

   - Geben Sie einen Befehl an Microsoft Defender for Endpoint aus, um die [Computer aus der Warnung zu isolieren](/connectors/wdatp/#actions---isolate-machine).

## <a name="how-to-run-a-playbook"></a>So führen Sie ein Playbook aus

Playbooks können entweder **manuell** oder **automatisch** ausgeführt werden.

Bei der manuellen Ausführung können Sie bei Eingang einer Warnung auswählen, dass ein Playbook als Reaktion auf die ausgewählte Warnung ausgeführt werden soll. Diese Funktion wird zurzeit nur für Warnungen unterstützt, nicht für Incidents.

Eine automatische Ausführung bedeutet, dass Sie sie als automatische Reaktion in einer Analyseregel (für Warnungen) oder als Aktion in einer Automatisierungsregel (für Incidents) festlegen. Erfahren Sie mehr über [Automatisierungsregeln](automate-incident-handling-with-automation-rules.md).

### <a name="set-an-automated-response"></a>Einstellen einer automatisierten Antwort

Security-Operations-Teams können Ihre Arbeitsbelastung erheblich reduzieren, indem sie die Routine-Reaktionen auf wiederkehrende Arten von Incidents und Warnungen vollständig automatisieren. So können Sie sich mehr auf einmalige Incidents und Warnungen, die Analyse von Mustern, die Ermittlung von Bedrohungen und mehr konzentrieren.

Das Einstellen der automatischen Antwort bedeutet, dass jedes Mal, wenn eine Analyseregel ausgelöst wird, zusätzlich zur Erstellung einer Warnung, die Regel ein Playbook ausführt, das die von der Regel erstellte Warnung als Eingabe erhält.

Wenn die Warnung einen Incident erzeugt, löst der Incident eine Automatisierungsregel aus, die wiederum ein Playbook ausführen kann, das als Eingabe den durch die Warnung erzeugten Incident erhält.

#### <a name="alert-creation-automated-response"></a>Erzeugung von Warnungen automatische Antwort

Für Playbooks, die durch die Erstellung von Warnungen ausgelöst werden und Warnungen als Eingaben erhalten (ihr erster Schritt ist „Wenn eine Microsoft Sentinel-Warnung ausgelöst wird“), fügen Sie das Playbook an eine Analyseregel an:

1. Bearbeiten Sie die [Analyseregel](detect-threats-custom.md), mit der die Warnung generiert wird, für die Sie eine automatisierte Antwort definieren möchten.

1. Wählen Sie unter **Warnungsautomatisierung** in der Registerkarte **Automatisierte Reaktion** das oder die Playbooks aus, die diese Analyseregel auslösen soll, wenn eine Warnung erstellt wird.

#### <a name="incident-creation-automated-response"></a>Erstellung von Incidents automatische Antwort

Für Playbooks, die durch die Erstellung von Incidents ausgelöst werden und Incidents als Eingaben erhalten (ihr erster Schritt ist "Wenn ein Microsoft Sentinel-Incident ausgelöst wird"), erstellen Sie eine Automatisierungsregel und definieren darin eine Aktion **Playbook ausführen**. Dies kann auf 2 Arten erfolgen:

- Bearbeiten Sie die Analyseregel, mit der der Incident generiert wird, für die Sie eine automatisierte Antwort definieren möchten. Erstellen Sie unter **Incident Automation** in der Registerkarte **automatisierte Antwort** eine Automatisierungsregel. Dadurch wird nur eine automatisierte Antwort für diese Analyseregel erstellt.

- Erstellen Sie in der Registerkarte **Automatisierungsregeln** im **Automatisierung**-Blade eine neue Automatisierungsregel und geben Sie die entsprechenden Bedingungen und gewünschten Aktionen an. Diese Automatisierungsregel wird auf alle Analyseregeln angewendet, die die angegebenen Bedingungen erfüllen.

    > [!NOTE]
    > **Microsoft Sentinel-Automatisierungsregeln erfordern Berechtigungen zum Ausführen von Playbooks.**
    >
    > Zum Ausführen eines Playbooks aus einer Automatisierungsregel verwendet Microsoft Sentinel ein speziell dafür autorisiertes Dienstkonto. Die Verwendung dieses Kontos (im Gegensatz zu Ihrem Benutzerkonto) erhöht das Sicherheitsniveau des Dienstes und aktiviert die Automatisierungsregeln-API zur Unterstützung von CI/CD-Anwendungsfällen.
    >
    > Diesem Konto müssen explizite Berechtigungen (in Form der Rolle **Mitwirkender für Microsoft Sentinel-Automatisierung**) für die Ressourcengruppe erteilt werden, in der sich das Playbook befindet. An diesem Punkt kann jede Automatisierungsregel jedes beliebige Playbook in dieser Ressourcengruppe ausführen.
    >
    > Wenn Sie die Aktion **Playbook ausführen** zu einer Automatisierungsregel hinzufügen, wird eine Dropdownliste mit Playbooks für Ihre Auswahl angezeigt. Playbooks, für die Microsoft Sentinel nicht über Berechtigungen verfügt, werden als nicht verfügbar (ausgeblendet) angezeigt. Sie können Microsoft Sentinel direkt eine Berechtigung erteilen, indem Sie den Link **Playbookberechtigungen verwalten** auswählen.
    >
    > In einem Szenario mit mehreren Mandanten ([Lighthouse](extend-sentinel-across-workspaces-tenants.md#managing-workspaces-across-tenants-using-azure-lighthouse)) müssen Sie die Berechtigungen für den Mandanten definieren, in dem sich das Playbook befindet, auch wenn sich die Automatisierungsregel, die das Playbook aufruft, in einem anderen Mandanten befindet. Dazu müssen Sie über **Besitzerberechtigungen** für die Ressourcengruppe des Playbooks verfügen.
    >
    > Es gibt ein spezifisches Szenario mit einem **Dienstanbieter für verwaltete Sicherheit (Managed Security Service Provider, MSSP)** , bei dem ein Dienstanbieter bei seinem eigenen Mandanten angemeldet ist und mithilfe von [Azure Lighthouse](../lighthouse/index.yml) eine Automatisierungsregel im Arbeitsbereich eines Kunden erstellt. Diese Automatisierungsregel ruft dann ein Playbook auf, das zum Mandanten des Kunden gehört. In diesem Fall müssen Microsoft Sentinel Berechtigungen für **_beide Mandanten_*erteilt werden. Im Kundenmandanten erteilen Sie diese genau wie im normalen Szenario mit mehreren Mandanten im Bereich* Playbookberechtigungen verwalten**. Um die relevanten Berechtigungen im Mandanten des Dienstanbieters zu erteilen, müssen Sie der Ressourcengruppe, in der sich das Playbook befindet, eine zusätzliche Azure Lighthouse-Delegierung hinzufügen, die der **Azure Security Insights**-App mit der Rolle **Mitwirkender für Microsoft Sentinel-Automatisierung** Zugriffsrechte erteilt. [Hier](tutorial-respond-threats-playbook.md#permissions-to-run-playbooks) erfahren Sie, wie Sie diese Delegierung hinzufügen.

Weitere Informationen finden Sie in der [ausführlichen Anleitung zum Erstellen von Automatisierungsregeln](tutorial-respond-threats-playbook.md#respond-to-incidents).

### <a name="run-a-playbook-manually-on-an-alert"></a>Manuelles Ausführen eines Playbooks für eine Warnung

Manuelles Auslösen ist im Microsoft Sentinel-Portal auf den folgenden Blades verfügbar:

- Wählen Sie in der Ansicht **Incidents** einen bestimmten Incident, öffnen Sie dessen Registerkarte **Warnungen** und wählen Sie eine Warnung aus.

- Wählen Sie unter **Untersuchung** einen bestimmten Alarm aus.

1. Klicken Sie für die ausgewählte Warnung auf **Playbooks anzeigen**. Sie erhalten eine Liste aller Playbooks, die mit **Wenn eine Microsoft Sentinel-Warnung ausgelöst wird** beginnen und auf die Sie Zugriff haben.

1. Klicken Sie in der Zeile eines bestimmten Playbooks auf **Ausführen**, um es zu initiieren.

1. Wählen Sie die Registerkarte **Ausführung**, um eine Auflistung der Ausführungen eines Playbooks für diese Warnung anzuzeigen. Es kann ein paar Sekunden dauern, bis ein gerade abgeschlossene Ausführung in dieser Liste erscheint.

1. Durch Anklicken einer konkreten Ausführung wird das vollständige Ausführungsprotokoll in Logic Apps geöffnet.

### <a name="run-a-playbook-manually-on-an-incident"></a>Manuelles Ausführen eines Playbooks für einen Incident

Wird noch nicht unterstützt.

## <a name="manage-your-playbooks"></a>Verwalten Ihrer Playbooks

In der Registerkarte **Playbooks** wird eine Liste aller Playbooks angezeigt, auf die Sie Zugriff haben, gefiltert nach den Abonnements, die derzeit in Azure angezeigt werden. Der Abonnementfilter ist im Menü **Verzeichnis + Abonnement** der globalen Seiten-Kopfzeile verfügbar.

Wenn Sie auf einen Playbook-Namen klicken, werden Sie in Logic Apps zur Hauptseite des Playbooks weitergeleitet. Die Spalte **Status** gibt an, ob Sie aktiviert oder deaktiviert ist.

**Auslöserart** stellt den Logic Apps Trigger dar, der dieses Playbook startet.

| Art des Triggers | Zeigt Komponententypen im Playbook an |
|-|-|
| **Microsoft Sentinel-Incidents/Warnungen** | Das Playbook wird mit einem der Sentinel-Trigger (Warnung, Incident) gestartet |
| **Verwenden der Microsoft Sentinel-Aktion** | Das Playbook wird mit einem Nicht-Sentinel-Trigger gestartet, verwendet aber eine Microsoft Sentinel-Aktion |
| **Andere** | Das Playbook enthält keine Sentinel-Komponenten |
| **Nicht initialisiert** | Das Playbook wurde erstellt, enthält jedoch keine Komponenten (Trigger oder Aktionen). |
|

Auf der Seite Logic App des Playbooks können Sie weitere Informationen über das Playbook anzeigen, einschließlich eines Protokolls aller Ausführungszeiten und des Ergebnisses (Erfolg oder Misserfolg und andere Details). Sie können auch den Logic Apps Designer aufrufen und das Playbook direkt bearbeiten, wenn Sie über die entsprechenden Berechtigungen verfügen.

### <a name="api-connections"></a>API-Verbindungen

API-Verbindungen werden verwendet, um Logic Apps mit anderen Diensten zu verbinden. Bei jeder neuen Authentifizierung an einem Logic Apps-Connector wird eine neue Ressource vom Typ **API-Verbindung** erstellt, die die bei der Konfiguration des Zugriffs auf den Dienst angegebenen Informationen enthält.

Wenn Sie alle API-Verbindungen anzeigen möchten, geben Sie *API-Verbindungen* in das Suchfeld in der Kopfzeile des Azure-Portals ein. Beachten Sie die relevanten Spalten:

- Anzeigename - der „freundliche“ Name (eigene Name), den Sie der Verbindung jedes Mal geben, wenn Sie eine Verbindung erstellen.
- Status - zeigt den Verbindungsstatus an: Fehler, verbunden.
- Ressourcengruppe: API-Verbindungen werden in der Ressourcengruppe der Playbook-Ressource (Logic Apps) erstellt.

Eine weitere Möglichkeit zum Anzeigen von API-Verbindungen besteht darin, das Blade **alle Ressourcen** aufzurufen und nach Typ *API-Verbindung* zu filtern. Auf diese Weise können mehrere Verbindungen gleichzeitig ausgewählt, markiert und gelöscht werden.

Um die Autorisierung einer vorhandenen Verbindung zu ändern, geben Sie die Verbindungs-Ressource ein, und wählen Sie **API-Verbindung bearbeiten** aus.

## <a name="recommended-playbooks"></a>Empfohlene Playbooks

Die folgenden empfohlenen Playbooks und ähnliche Playbooks stehen im [GitHub-Repository von Microsoft Sentinel](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks) zur Verfügung:

- **Benachrichtigungsplaybooks** werden ausgelöst, wenn eine Warnung oder ein Incident erstellt wird und eine Benachrichtigung an ein konfiguriertes Ziel gesendet wird:

    - [Posten einer Nachricht in einem Microsoft Teams-Kanal](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Post-Message-Teams)
    - [Senden einer Outlook-E-Mail-Benachrichtigung](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Incident-Email-Notification)
    - [Posten einer Nachricht in einem Slack-Kanal](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Post-Message-Slack)

- **Blockierende Playbooks** werden ausgelöst, wenn eine Warnung oder ein Incident erstellt wird. Sie sammeln Informationen zu Entitäten wie Konto, IP-Adresse und Host und sperren sie für weitere Aktionen:

    - [Aufforderung zum Blockieren einer IP-Adresse](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Block-IPs-on-MDATP-Using-GraphSecurity)
    - [Blockieren eines AAD-Benutzers](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Block-AADUser)
    - [Zurücksetzen eines AAD-Benutzerkennworts](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Reset-AADUserPassword/)
    - [Aufforderung zum Isolieren eines Computers](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Isolate-AzureVMtoNSG)

- **Playbooks mit Erstellungs-, Aktualisierungs- oder Schließfunktion** können Incidents in Microsoft Sentinel, Microsoft 365-Sicherheitsdiensten oder anderen Ticketausstellungssystemen erstellen, aktualisieren oder schließen:

    - [Ändern des Schweregrads eines Incidents](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Change-Incident-Severity)
    - [Erstellen eines ServiceNow-Incidents](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks/Create-SNOW-record)


## <a name="next-steps"></a>Nächste Schritte

- [Tutorial: Verwenden von Playbooks zur Automatisierung von Bedrohungsreaktionen in Microsoft Sentinel](tutorial-respond-threats-playbook.md)
