---
title: Übersicht über Connectors für Azure Logic Apps
description: Erfahren Sie mehr über Connectors und wie sie das schnelle und einfache Erstellen automatisierter Integrationsworkflows mit Azure Logic Apps unterstützen.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: conceptual
ms.date: 09/13/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: cccd744e8c123cd9441ff9aca47d2341ea9d80fb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679861"
---
# <a name="about-connectors-in-azure-logic-apps"></a>Informationen zu Connectors in Azure Logic Apps

Wenn Sie Workflows mithilfe von Azure Logic Apps erstellen, können Sie mit *Connectors* schnell und einfach auf Daten, Ereignisse und Ressourcen in anderen Apps, Diensten, Systemen, Protokollen und Plattformen zu zugreifen – häufig ohne Code zu schreiben. Ein Connector bietet vordefinierte Vorgänge, die Sie als Schritte in Ihren Workflows verwenden können. Azure Logic Apps bietet Hunderte von Connectors, die Sie verwenden können. Wenn kein Connector für die Ressource verfügbar ist, auf die Sie zugreifen möchten, können Sie den generischen HTTP-Vorgang verwenden, um mit dem Dienst zu kommunizieren, oder Sie können [einen benutzerdefinierten Connector erstellen](#custom-apis-and-connectors).

Diese Übersicht bietet eine Einführung in Connectors, deren Funktionsweise im Allgemeinen und die gängigeren und häufiger verwendeten Connectors in Azure Logic Apps. Weitere Informationen finden Sie in der folgenden Dokumentation:

* [Übersicht über Connectors für Azure Logic Apps, Microsoft Power Automate und Microsoft Power Apps](/connectors)
* [Connectors-Referenz für Azure Logic Apps](/connectors/connector-reference/connector-reference-logicapps-connectors)
* [Preis- und Abrechnungsmodelle für Azure Logic Apps](../logic-apps/logic-apps-pricing.md)
* [Azure Logic Apps – Preise](https://azure.microsoft.com/pricing/details/logic-apps/)

## <a name="what-are-connectors"></a>Was sind Konnektoren?

Technisch gesehen ist ein Connector ein Proxy oder Wrapper um eine API, den der zugrunde liegende Dienst für die Kommunikation mit Azure Logic Apps verwendet. Dieser Connector stellt Vorgänge zur Verfügung, die Sie in Ihren Workflows zum Ausführen von Aufgaben verwenden. Ein Vorgang ist entweder als *Trigger* oder *Aktion* verfügbar, dessen/deren Eigenschaften Sie konfigurieren können. Einige Trigger und Aktionen erfordern auch, dass Sie zuerst [eine Verbindung mit dem zugrunde liegenden Dienst oder System herstellen und konfigurieren](#connection-configuration), damit Sie z. B. den Zugriff auf ein Benutzerkonto authentifizieren können.

### <a name="triggers"></a>Trigger

Ein *Trigger* gibt das Ereignis an, das den Workflow startet, und ist immer der erste Schritt in einem Workflow. Jeder Trigger folgt zudem einem bestimmten Auslösemuster, das steuert, wie der Trigger Ereignisse überwacht und darauf reagiert. Normalerweise folgt ein Trigger dem *Abrufmuster* oder *Pushmuster*, manchmal ist ein Trigger jedoch in beiden Versionen verfügbar.

- *Abfragetrigger* überprüfen regelmäßig einen bestimmten Dienst oder ein bestimmtes System nach einem bestimmten Zeitplan, um nach neuen Daten oder einem bestimmten Ereignis zu suchen. Wenn neue Daten verfügbar sind oder das spezifische Ereignis eintritt, erstellen diese Trigger eine neue Instanz Ihres Workflows und führen sie aus. Diese neue Instanz kann dann die Daten verwenden, die als Eingabe übergeben werden.
- *Pushtrigger* lauschen auf neue Daten oder darauf, dass ein Ereignis ohne Abruf erfolgt. Wenn neue Daten verfügbar sind oder das Ereignis eintritt, erstellen diese Trigger eine neue Instanz Ihres Workflows und führen sie aus. Diese neue Instanz kann dann die Daten verwenden, die als Eingabe übergeben werden.

Beispielsweise können Sie einen Workflow erstellen, der etwas auslöst, wenn eine Datei auf Ihren FTP-Server hochgeladen wird. Als ersten Schritt in Ihrem Workflow können Sie den FTP-Trigger mit dem Namen **Wenn eine Datei hinzugefügt oder geändert wird** verwenden, welcher dem Abrufmuster folgt. Sie können dann einen Zeitplan angeben, um regelmäßig nach Upload-Ereignissen zu sehen.

Ein Trigger übergibt auch alle Eingaben und anderen erforderlichen Daten an Ihren Workflow, wo spätere Aktionen auf diese Daten im gesamten Workflow verweisen und diese verwenden können. Wenn Sie beispielsweise den Office 365 Outlook-Trigger mit dem Namen **Wenn eine neue E-Mail eintrifft** verwenden möchten, um einen Workflow zu starten, wenn Sie eine neue E-Mail erhalten. Sie können diesen Trigger so konfigurieren, dass der Inhalt jeder neuen E-Mail übergeben wird, z. B. Absender, Betreffzeile, Text, Anlagen und so weiter. Der Workflow kann diese Informationen dann mithilfe anderer Aktionen verarbeiten.

### <a name="actions"></a>Aktionen

Eine *Aktion* ist ein Vorgang, der dem Trigger folgt und eine Art von Aufgabe in Ihrem Workflow ausführt. Sie können mehrere Aktionen in Ihrem Workflow verwenden. Beispielsweise könnten Sie den Workflow über einen SQL-Trigger starten, der neue Kundendaten in einer SQL-Datenbank erkennt. Nach dem Trigger kann der Workflow eine SQL-Aktion beinhalten, die die Kundendaten abruft. Nach der SQL-Aktion kann der Workflow eine weitere Aktion beinhalten, nicht notwendigerweise über SQL, die die Daten verarbeitet.

## <a name="connector-categories"></a>Konnektorkategorien

In Azure Logic Apps sind die meisten Trigger und Aktionen entweder in einer *integrierten* Version oder einer *verwalteten Connector*-Version verfügbar. In beiden Versionen sind einige Trigger und Aktionen verfügbar. Die vorhandenen Versionen hängen davon ab, ob Sie eine mehrstufige Logik-App oder eine Logik-App mit nur einem Mandanten erstellen, die derzeit nur in [Azure Logic Apps mit einem Mandanten](../logic-apps/single-tenant-overview-compare.md) verfügbar ist.

[Integrierte Trigger und Aktionen](built-in.md) werden nativ in der Logic Apps-Runtime ausgeführt, erfordern keine Verbindungen und führen diese Arten von Aufgaben aus:

- [Auführen von Code in Ihren Workflows](built-in.md#run-code-from-workflows).
- [Organisieren und Steuern Ihrer Daten](built-in.md#control-workflow).
- [Verwalten oder Bearbeiten von Daten](built-in.md#manage-or-manipulate-data).

[Verwaltete Konnektoren](managed.md) werden von Microsoft bereitgestellt, gehostet und verwaltet. Diese Konnektoren bieten Trigger und Aktionen für Cloud-Dienste, lokale Systeme oder beides. Verwaltete Connectors sind in den folgenden Kategorien verfügbar:

- [Lokale Konnektoren](managed.md#on-premises-connectors), die Ihnen den Zugriff auf Daten und Ressourcen in lokalen Systemen ermöglichen.
- [Unternehmensconnectors](managed.md#enterprise-connectors), die Zugriff auf Unternehmenssysteme bieten.
- [Integrationskonto-Connectors](managed.md#integration-account-connectors), die B2B-Kommunikationsszenarien (Business-to-Business) unterstützen.
- [Connectors für die Integrationsdienstumgebung (ISE)](managed.md#ise-connectors), eine kleine Gruppe von [verwalteten Connectors, die nur für ISEs verfügbar sind](#ise-and-connectors).

<a name="connection-configuration"></a>

## <a name="connection-configuration"></a>Verbindungskonfiguration

Um Ressourcen und Verbindungen von Logic Apps zu erstellen oder zu verwalten, benötigen Sie bestimmte Berechtigungen, die über Rollen mithilfe der rollenbasierten Zugriffskontrolle von [Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) bereitgestellt werden. Sie können Mitgliedern, die Zugriff auf Ihr Azure-Abonnement haben, integrierte oder angepasste Rollen zuweisen. Azure Logic Apps verfügt über diese spezifischen Rollen:

* [Logik-App-Mitwirkender:](../role-based-access-control/built-in-roles.md#logic-app-contributor) Ermöglicht Ihnen die Verwaltung von Logik-Apps, aber nicht die Änderung des App-Zugriffs.

* [Logik-App-Operator:](../role-based-access-control/built-in-roles.md#logic-app-operator) Ermöglicht Ihnen das Lesen, Aktivieren und Deaktivieren von Logik-Apps, die Sie aber nicht bearbeiten oder aktualisieren können.

* [Mitwirkender](../role-based-access-control/built-in-roles.md#contributor): Gewährt vollen Zugriff auf die Verwaltung aller Ressourcen, erlaubt aber nicht die Zuweisung von Rollen in Azure RBAC, die Verwaltung von Zuweisungen in Azure Blueprints oder die Freigabe von Bildergalerien.

  Angenommen, Sie müssen mit einer Logik-App arbeiten, die Sie nicht erstellt und keine Verbindungen authentifiziert haben, die vom Workflow dieser Logik-App verwendet werden. Für Ihr Azure-Abonnement ist die Berechtigung „Mitwirkender“ für die Ressourcengruppe erforderlich, die diese Logik-App-Ressource enthält. Wenn Sie eine Logik-App-Ressource erstellen, haben Sie automatisch Zugriff als Mitwirkender.

Bevor Sie den Trigger oder die Aktionen eines Konnektors in Ihrem Workflow verwenden können, müssen Sie bei den meisten Konnektoren zunächst eine *Verbindung* zum Zieldienst oder -system herstellen. Um eine Verbindung aus einem Logic-App-Workflow heraus zu erstellen, müssen Sie Ihre Identität mit Kontodaten und manchmal auch anderen Verbindungsinformationen authentifizieren. Bevor Ihr Workflow beispielsweise auf Ihr Office 365 Outlook-E-Mail-Konto zugreifen und damit arbeiten kann, müssen Sie eine Verbindung mit diesem Konto autorisieren. Für eine kleine Anzahl integrierter Operationen und verwalteter Konnektoren können Sie eine [verwaltete Identität für die Authentifizierung einrichten und verwenden](../logic-apps/create-managed-service-identity.md#triggers-actions-managed-identity), anstatt Ihre Anmeldedaten anzugeben.

<a name="connection-security-encyrption"></a>

### <a name="connection-security-and-encryption"></a>Verbindungssicherheit und -Verschlüsselung

Verbindungskonfigurationsdetails wie Serveradresse, Benutzername und Kennwort, Anmeldeinformationen und Geheimnisse werden[ verschlüsselt und in der sicheren Azure-Umgebung gespeichert](../security/fundamentals/encryption-overview.md). Diese Informationen können nur in logischen Anwendungsressourcen und von Clients verwendet werden, die über Berechtigungen für die Verbindungsressource verfügen, was durch verknüpfte Zugriffsprüfungen erzwungen wird. Verbindungen, die Azure Active Directory Open Authentication (Azure AD OAuth) verwenden, wie z. B. Office 365, Salesforce und GitHub, erfordern eine Anmeldung, aber Azure Logic Apps speichert nur Zugriffs- und Aktualisierungs-Tokens als Geheimnisse, keine Anmeldedaten.

Hergestellte Verbindungen können solange auf den Zieldienst oder das Zielsystem zugreifen, wie der Dienst oder das System dies zulässt. Bei Diensten, die Azure AD-OAuth-Verbindungen verwenden (z. B. Office 365 und Dynamics), aktualisiert der Logic Apps-Dienst die Zugriffstoken unbegrenzt. Bei anderen Diensten gibt es unter Umständen eine zeitliche Begrenzung für die Verwendung eines Tokens durch Logic Apps, nach der eine Aktualisierung erforderlich ist. Durch einige Aktionen (z. B. Ändern des Kennworts) werden alle Zugriffstoken ungültig.

Obwohl Sie Verbindungen in einem Workflow erstellen, sind Verbindungen separate Azure-Ressourcen mit eigenen Ressourcendefinitionen. Um diese Verbindungsressourcendefinitionen zu überprüfen, [laden Sie Ihre Logik-App von Azure in Visual Studio herunter](../logic-apps/manage-logic-apps-with-visual-studio.md). Diese Methode ist der einfachste Weg, um eine gültige, parametrisierte Logik-App-Vorlage zu erstellen, die größtenteils bereit für die Bereitstellung ist.

> [!TIP]
> Wenn Ihre Organisation den Zugriff auf bestimmte Ressourcen über Logic Apps-Konnektoren nicht zulässt, können Sie die [Möglichkeit, solche Verbindungen zu erstellen](../logic-apps/block-connections-connectors.md), mithilfe von [Azure Policy](../governance/policy/overview.md) blockieren.

Weitere Informationen zum Sichern von Logic Apps und Verbindungen finden Sie unter [Sicherer Zugriff und Daten in Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md).

<a name="firewall-access"></a>

### <a name="firewall-access-for-connections"></a>Firewallzugriff für Verbindungen

Falls Sie eine Firewall verwenden, die Datenverkehr beschränkt, und Ihr Logik-App-Workflow über diese Firewall kommunizieren muss, muss Folgendes sichergestellt sein: sie müssen die Firewall so einstellen, dass der Zugriff für IP-Adressen, die vom Logik-Apps-Dienst oder der Runtime in der Azure-Region Ihrer Logik-App genutzt werden, in [eingehender](../logic-apps/logic-apps-limits-and-config.md#inbound) und [ausgehender](../logic-apps/logic-apps-limits-and-config.md#outbound) Richtung zugelassen wird. Falls für Ihren Workflow auch verwaltete Connectors (z. B. Office 365 Outlook-Connector oder SQL-Connector) oder benutzerdefinierte Connectors verwendet werden, muss in der Firewall in der Azure-Region Ihrer Logik-App zusätzlich der Zugriff für *alle* ausgehenden IP-Adressen der [verwalteten Connectors](/connectors/common/outbound-ip-addresses#azure-logic-apps) zulässig sein. Weitere Informationen finden Sie unter [Firewall-Konfiguration](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags).

## <a name="recurrence-behavior"></a>Wiederholungsverhalten

Wiederkehrende integrierte Trigger, z. B. der [Wiederholungstrigger](connectors-native-recurrence.md), werden nativ in der Logic Apps-Runtime ausgeführt und unterscheiden sich von sich wiederholenden verbindungsbasierten Triggern, z. B. dem Office 365 Outlook-Connectortrigger, bei dem Sie zuerst eine Verbindung erstellen müssen.

Für beide Triggerarten gilt: Wenn eine Serie kein bestimmtes Startdatum und keine Startuhrzeit angibt, wird die erste Wiederholung trotz der Serieneinrichtung Ihres Triggers sofort ausgeführt, wenn Sie die Logik-App speichern oder bereitstellen. Um dieses Verhalten zu vermeiden, geben Sie ein Startdatum und eine Startuhrzeit für die Ausführung der ersten Wiederholung an.

### <a name="recurrence-for-built-in-triggers"></a>Integrierte Trigger für Wiederholungen

Integrierte Trigger für Wiederholungen folgen dem von Ihnen festgelegten Zeitplan, einschließlich der von Ihnen angegebenen Zeitzone. Wenn eine Serie jedoch keine weiteren erweiterten Zeitplanungsoptionen angibt, wie z. B. spezifische Uhrzeiten zum Ausführen zukünftiger Wiederholungen, basieren diese Wiederholungen auf der letzten Triggerausführung. Hieraus ergibt sich, dass die Startzeiten für diese Wiederholungen sich aufgrund von Faktoren wie Wartezeiten während Speicheraufrufen verschieben können.

Weitere Informationen finden Sie in der folgenden Dokumentation:

* [Planen und Ausführen von wiederkehrenden automatisierten Aufgaben, Prozessen und Workflows mit Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
* [Erstellen, Planen und Ausführen von wiederkehrenden Aufgaben und Workflows mit dem Serientrigger](connectors-native-recurrence.md)
* [Problembehandlung bei Wiederholungsproblemen](#recurrence-issues)

### <a name="recurrence-for-connection-based-triggers"></a>Wiederholung für verbindungsbasierte Trigger

Bei sich wiederholenden verbindungsbasierten Triggern wie Office 365 Outlook ist der Zeitplan nicht der einzige Treiber, der die Ausführung steuert. Die Zeitzone bestimmt nur die anfängliche Startzeit. Nachfolgende Ausführungen richten sich nach dem Wiederholungszeitplan, der letzten Triggerausführung und anderen Faktoren, die zu einer Verschiebung der Ausführungszeiten oder zu unerwartetem Verhalten führen können. Beispiele:

* Der Trigger greift auf einen Server mit weiteren Daten zu, für die der Trigger sofort einen Abruf startet.
* Alle Fehler oder Wiederholungsversuche des Triggers.
* Wartezeit bei Speicheraufrufen.
* Nichteinhaltung des angegebenen Zeitplans zu Beginn und Ende der Sommerzeit.
* Andere Faktoren, die sich auf den Zeitpunkt der nächsten Ausführung auswirken können.

Weitere Informationen finden Sie in der folgenden Dokumentation:

* [Planen und Ausführen von wiederkehrenden automatisierten Aufgaben, Prozessen und Workflows mit Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)
* [Problembehandlung bei Wiederholungsproblemen](#recurrence-issues)

<a name="recurrence-issues"></a>

### <a name="troubleshooting-recurrence-issues"></a>Problembehandlung bei Wiederholungsproblemen

Um sicherzustellen, dass Ihr Workflow zur angegebenen Startzeit ausgeführt wird und keine Wiederholung verpasst – insbesondere wenn die Häufigkeit im Bereich von Tagen oder länger liegt – versuchen Sie die folgenden Lösungen:

* Wenn die Sommerzeit beginnt, passen Sie die Wiederholung manuell an, damit Ihr Workflow weiterhin zur erwarteten Zeit läuft. Andernfalls verschiebt sich die Startzeit um eine Stunde nach vorn, wenn die Sommerzeit beginnt, und eine Stunde nach hinten, wenn die Sommerzeit endet. Weitere Informationen und Beispiele finden Sie unter [Wiederholung für Sommerzeit und Normalzeit](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#daylight-saving-standard-time).

* Wenn Sie einen **Wiederholungstrigger** verwenden, geben Sie eine Zeitzone, ein Startdatum und eine Startzeit an. Konfigurieren Sie die spezifischen Zeiten, um nachfolgende Wiederholungen auszuführen, indem Sie dazu die Eigenschaften namens **Zu diesen Stunden** und **Zu diesen Minuten**, die nur für die Häufigkeiten **Tag** und **Woche** verfügbar sind. Einige Zeitfenster können bei einer Zeitverschiebung dennoch weiterhin Probleme verursachen.

* Erwägen Sie die Verwendung eines [**gleitendes Fenster**-Triggers](connectors-native-sliding-window.md) anstelle eines **Wiederholungstriggers**, um verpasste Wiederholungen zu vermeiden.

## <a name="custom-apis-and-connectors"></a>Benutzerdefinierte APIs und Connectors

Zum Aufrufen von APIs, die benutzerdefinierten Code ausführen oder nicht als Connector verfügbar sind, können Sie die Logic Apps-Plattform erweitern, indem Sie [benutzerdefinierte API-Apps erstellen](../logic-apps/logic-apps-create-api-app.md). Sie können außerdem für alle REST- oder SOAP-basierten APIs [benutzerdefinierte Connectors erstellen](../logic-apps/custom-connector-overview.md), mit denen diese APIs in allen Logik-Apps in Ihrem Azure-Abonnement verfügbar werden. Um benutzerdefinierte API-Apps oder Connectors für alle Benutzer in Azure öffentlich zu machen, können Sie [Connectors für die Microsoft-Zertifizierung einreichen](/connectors/custom-connectors/submit-certification).

## <a name="ise-and-connectors"></a>ISE und Konnektoren

Für Workflows, die Direktzugriff auf Ressourcen in einem virtuellen Azure-Netzwerk benötigen, können Sie eine dedizierte [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) erstellen, in der Sie Ihren Workflow auf dedizierten Ressourcen erstellen, bereitstellen und ausführen können. Weitere Informationen zum Erstellen von ISEs finden Sie unter [Verbinden mit Azure Virtual Networks über Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

Benutzerdefinierte Connectors, die in einer ISE erstellt wurden, funktionieren nicht mit dem lokalen Datengateway. Diese Connectors können jedoch direkt auf lokale Datenquellen zugreifen, die mit dem virtuellen Azure-Netzwerk verbunden sind, das die ISE hostet. Deshalb benötigen Logik-Apps in einer ISE sehr wahrscheinlich das Datengateway nicht, wenn sie mit diesen Ressourcen kommunizieren. Wenn Sie benutzerdefinierte Konnektoren haben, die Sie außerhalb einer ISE erstellt haben und die das lokale Datengateway benötigen, können Logikanwendungen in einer ISE diese Konnektoren verwenden.

Wenn Sie im Logic Apps Designer die integrierten Trigger und Aktionen oder verwalteten Connectors durchsuchen, die Sie für Logik-Apps in einer ISE verwenden möchten, wird die **CORE**-Bezeichnung in integrierten Triggern und Aktionen angezeigt, während die **ISE**-Bezeichnung auf verwalteten Connectors angezeigt wird, die speziell für die Verwendung mit einer ISE konzipiert sind.

:::row:::
    :::column:::
        ![CORE-Beispielconnector](./media/apis-list/example-core-connector.png)
        \
        \
        **CORE**
        \
        \
        Integrierte Trigger und Aktionen mit dieser Bezeichnung werden in derselben Integrationsdienstumgebung wie Ihre Logik-Apps ausgeführt.
    :::column-end:::
    :::column:::
        ![Beispiel für einen ISE-Connector](./media/apis-list/example-ise-connector.png)
        \
        \
        **ISE**
        \
        \
        Verwaltete Connectors mit dieser Bezeichnung werden in derselben ISE wie Ihre Logik-Apps ausgeführt. 
        \
        \
        Wenn Sie ein lokales System haben, das mit einem virtuellen Azure-Netzwerk verbunden ist, ermöglicht eine ISE Ihren Workflows den Direktzugriff auf dieses System ohne das [lokale Datengateway](../logic-apps/logic-apps-gateway-connection.md). Stattdessen können Sie entweder, falls verfügbar, den **ISE**-Connector dieses Systems verwenden, eine HTTP-Aktion oder einen [benutzerdefinierten Connector](#custom-apis-and-connectors).
        \
        \
        Für lokale Systeme, die keine **ISE**-Connectors aufweisen, verwenden Sie das lokale Datengateway. Informationen zu verfügbaren ISE-Connectors finden Sie unter [ISE-Connectors](#ise-and-connectors).
    :::column-end:::
    :::column:::
        ![Beispiel für einen Nicht-ISE-Connector](./media/apis-list/example-multi-tenant-connector.png)
        \
        \
        Keine Bezeichnung     \
        \
        Alle anderen Connectors ohne Bezeichnung, die Sie weiterhin verwenden können, werden im globalen, mehrinstanzenfähigen Logic Apps-Dienst ausgeführt.
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="known-issues"></a>Bekannte Probleme

Im Folgenden werden bekannte Probleme bei Logic Apps Connectors behandelt.

| Fehlermeldung| BESCHREIBUNG | Lösung |
|--------------|-------------|------------|
| `Error: BadGateway. Client request id: '{GUID}'` | Dieser Fehler tritt beim Aktualisieren der Tags in einer Logik-App auf, bei der mindestens eine Verbindung die OAuth-Authentifizierung mit Azure Active Directory (Azure AD) nicht unterstützt (z. B. SFTP und SQL), sodass diese Verbindungen unterbrochen werden. | Um dieses Verhalten zu vermeiden, sollten Sie diese Tags nicht aktualisieren. |
||||

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen benutzerdefinierter APIs, die über Azure Logic Apps aufgerufen werden können](../logic-apps/logic-apps-create-api-app.md)
