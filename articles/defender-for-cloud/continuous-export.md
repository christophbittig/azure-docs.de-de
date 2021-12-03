---
title: Fortlaufender Export kann Warnungen und Empfehlungen von Microsoft Defender für Cloud an Log Analytics-Arbeitsbereiche oder Azure Event Hubs senden.
description: Hier erfahren Sie, wie Sie den fortlaufenden Export von Sicherheitswarnungen und -empfehlungen in Log Analytics-Arbeitsbereiche und Azure Event Hubs konfigurieren.
author: memildin
manager: rkarlin
ms.service: defender-for-cloud
ms.topic: how-to
ms.date: 11/09/2021
ms.author: memildin
ms.openlocfilehash: 9580ee5f915417d4195ba13277e4de8df7bf55d1
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526153"
---
# <a name="continuously-export-microsoft-defender-for-cloud-data"></a>Fortlaufender Export von Microsoft Defender für Cloud-Daten

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender für Cloud generiert detaillierte Sicherheitswarnungen und -empfehlungen. Sie können diese im Portal oder über programmgesteuerte Tools anzeigen. Möglicherweise müssen Sie diese Informationen auch ganz oder teilweise für die Nachverfolgung mit anderen Überwachungstools in Ihrer Umgebung exportieren. 

*Was* exportiert wird und *wohin*, können Sie mit dem **fortlaufenden Export** umfassend anpassen. Sie können z. B. die folgenden Konfigurationen vornehmen:

- Alle Warnungen mit einem hohen Schweregrad werden an eine Azure Event Hub-Instanz gesendet.
- Alle Ergebnisse mit einem mittleren oder höheren Schweregrad bei Überprüfungen der Sicherheitsrisikobewertung Ihrer SQL Server-Instanzen werden an einen bestimmten Log Analytics-Arbeitsbereich gesendet.
- Bestimmte Empfehlungen werden bei Erstellung an eine Event Hub-Instanz oder einen Log Analytics-Arbeitsbereich gesendet. 
- Die Sicherheitsbewertung für ein Abonnement wird immer an einen Log Analytics-Arbeitsbereich gesendet, wenn sich die Bewertung für eine Kontrolle um 0,01 oder mehr ändert. 

Obwohl das Feature als *fortlaufend* bezeichnet wird, gibt es auch eine Option zum Exportieren von wöchentlichen Momentaufnahmen.

In diesem Artikel wird erläutert, wie Sie den fortlaufenden Export in Log Analytics-Arbeitsbereiche und Azure Event Hubs konfigurieren.

> [!NOTE]
> Wenn Sie Defender für Cloud in SIEM integrieren müssen, lesen Sie [Streamen von Warnungen in eine SIEM-, SOAR- oder IT-Service-Management-Lösung](export-to-siem.md).

> [!TIP]
> Defender für Cloud bietet auch die Möglichkeit, einen einmaligen manuellen Export in eine CSV-Datei auszuführen. Weitere Informationen finden Sie unter [Manueller einmaliger Export von Warnungen und Empfehlungen](#manual-one-time-export-of-alerts-and-recommendations).


## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|Kostenlos|
|Erforderliche Rollen und Berechtigungen:|<ul><li>**Sicherheitsadministrator** oder **Besitzer** für die Ressourcengruppe</li><li>Schreibberechtigungen für die Zielressource.</li><li>Wenn Sie die unten beschriebenen „DeployIfNotExist“-Richtlinien von Azure Policy verwenden, benötigen Sie auch die Berechtigungen zum Zuweisen von Richtlinien.</li><li>Zum Exportieren von Daten zu einem Event Hub benötigen Sie die Schreibberechtigung für die Event Hub-Richtlinie.</li><li>So exportieren Sie in einen Log Analytics-Arbeitsbereich<ul><li>Wenn er **über die SecurityCenterFree-Lösung verfügt**, benötigen Sie mindestens Leseberechtigungen für die Arbeitsbereichslösung: `Microsoft.OperationsManagement/solutions/read`</li><li>Wenn er **nicht über die SecurityCenterFree-Lösung verfügt**, benötigen Sie Schreibberechtigungen für die Arbeitsbereichslösung: `Microsoft.OperationsManagement/solutions/action`</li><li>Weitere Informationen zu [Azure Monitor und Log Analytics-Arbeitsbereichslösungen](../azure-monitor/insights/solutions.md)</li></ul></li></ul>|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: National (Azure Government, Azure China 21Vianet)| 
|||


## <a name="what-data-types-can-be-exported"></a>Welche Datentypen können exportiert werden?

Die folgenden Datentypen können mithilfe des fortlaufende Exports exportiert werden, wenn sie sich ändern:

- Sicherheitswarnungen.
- Sicherheitsempfehlungen.
- Sicherheitsergebnisse. Diese können als „untergeordnete“ Empfehlungen betrachtet werden, die zu einer „übergeordneten“ Empfehlung gehören. Beispiel:
    - Die Empfehlung „Auf Ihren Computern müssen Systemupdates installiert werden“ verfügt über eine „untergeordnete“ Empfehlung für jedes ausstehende Systemupdate.
    - Die Empfehlung „Sicherheitsrisiken in Ihren virtuellen Computern müssen beseitigt werden“ verfügt über eine „untergeordnete“ Empfehlung für jedes von der Überprüfung auf Sicherheitsrisiken identifizierte Sicherheitsrisiko.
    > [!NOTE]
    > Wenn Sie einen fortlaufenden Export mit der REST-API konfigurieren, schließen Sie immer das übergeordnete Element in die Ergebnisse ein. 
- Sicherheitsbewertung pro Abonnement oder pro Kontrolle.
- Daten zur Einhaltung gesetzlicher Bestimmungen.


## <a name="set-up-a-continuous-export"></a>Einrichten eines fortlaufenden Exports 

Sie können den fortlaufenden Export auf den Seiten für Microsoft Defender für Cloud im Azure-Portal, über die REST-API oder im großen Stil mithilfe der bereitgestellten Azure Policy-Vorlagen konfigurieren. Klicken Sie auf die entsprechende Registerkarte, um Details zu den einzelnen Möglichkeiten anzuzeigen.

### <a name="use-the-azure-portal"></a>[**Verwenden des Azure-Portals**](#tab/azure-portal)

### <a name="configure-continuous-export-from-the-defender-for-cloud-pages-in-azure-portal"></a>Konfigurieren des fortlaufenden Exports auf den Seiten für Defender für Cloud im Azure-Portal

Die folgenden Schritte sind unabhängig davon erforderlich, ob Sie einen fortlaufenden Export in einen Log Analytics-Arbeitsbereich oder in Azure Event Hubs einrichten.

1. Öffnen Sie im Menü von Defender für Cloud **Umgebungseinstellungen**.

1. Wählen Sie das Abonnement aus, für das Sie den Datenexport konfigurieren möchten.

1. Wählen Sie auf der Randleiste der Seite „Einstellungen“ für dieses Abonnement **Fortlaufender Export** aus.

    :::image type="content" source="./media/continuous-export/continuous-export-options-page.png" alt-text="Exportoptionen in Microsoft Defender für Cloud":::

    Im Folgenden finden Sie die Exportoptionen. Es gibt eine Registerkarte für jedes verfügbare Exportziel. 

1. Wählen Sie den Datentyp, den Sie exportieren möchten, und dann Filter für die einzelnen Typen aus (z. B. nur Warnungen mit hohem Schweregrad exportieren).

1. Wählen Sie die gewünschte Exporthäufigkeit aus:
    - **Streaming**: Bewertungen werden gesendet, wenn der Integritätszustand einer Ressource aktualisiert wird (wenn keine Updates durchgeführt werden, werden keine Daten gesendet).
    - **Momentaufnahmen**: Eine Momentaufnahme des aktuellen Status der ausgewählten Datentypen wird einmal pro Woche pro Abonnement gesendet. Suchen Sie nach dem Feld ``IsSnapshot``, um Momentaufnahmedaten zu identifizieren.

1. Wenn Ihre Auswahl eine dieser Empfehlungen enthält, können Sie optional die Ergebnisse der Sicherheitsrisikobewertung mit aufnehmen:
    -  Bei SQL-Datenbanken sollten gefundene Sicherheitsrisiken behoben werden.
    -  Bei SQL Servern auf Computern sollten gefundene Sicherheitsrisiken behoben werden.
    - Sicherheitsrisiken in Azure Container Registry-Images müssen behoben werden (unterstützt von Qualys).
    - Sicherheitsrisiken für VMs müssen behoben werden
    - Systemupdates sollten auf Ihren Computern installiert sein.

    Wenn Sie die Ergebnisse in diese Empfehlungen aufnehmen möchten, aktivieren Sie die Option **Include security findings** (Sicherheitsergebnisse einschließen).

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Umschalter „Sicherheitsrelevante Ergebnisse einbeziehen“ in der Konfiguration für den fortlaufenden Export." :::

1. Wählen Sie im Bereich „Exportziel“ aus, wo die Daten gespeichert werden sollen. Daten können in einem Ziel in einem anderen Abonnement gespeichert werden (z.B. in einer Central Event Hub-Instanz oder in einem zentralen Log Analytics-Arbeitsbereich).
1. Wählen Sie **Speichern** aus.

### <a name="use-the-rest-api"></a>[**Verwenden der REST-API**](#tab/rest-api)

### <a name="configure-continuous-export-using-the-rest-api"></a>Konfigurieren des fortlaufenden Exports über die REST-API

Der fortlaufende Export kann über die [Automatisierungs-API](/rest/api/securitycenter/automations) in Microsoft Defender für Cloud konfiguriert und verwaltet werden. Verwenden Sie diese API, um Regeln für den Export an eines der folgenden möglichen Ziele zu erstellen oder zu aktualisieren:

- Azure Event Hub
- Log Analytics-Arbeitsbereich
- Azure Logic Apps 

Die API bietet zusätzliche Funktionen, die im Azure-Portal nicht verfügbar sind, z. B.:

* **Größeres Volumen**: Mit der API können Sie mehrere Exportkonfigurationen für ein einzelnes Abonnement erstellen. Die Seite **Fortlaufender Export** auf der Portalbenutzeroberfläche von Defender für Cloud unterstützt nur eine Exportkonfiguration pro Abonnement.

* **Zusätzliche Features** – Die API bietet zusätzliche Parameter, die nicht in der Benutzeroberfläche angezeigt werden. Beispielsweise können Sie Ihrer Automatisierungsressource Tags hinzufügen und Ihren Export auf der Grundlage einer breiteren Palette von Warnungs- und Empfehlungseigenschaften definieren, als sie auf der Seite **Fortlaufender Export** auf der Portalbenutzeroberfläche von Defender für Cloud angeboten werden.

* **Stärker fokussierte Bereiche**: Die API bietet eine differenziertere Ebene für den Umfang ihrer Exportkonfigurationen. Wenn Sie einen Export mit der API definieren, können Sie dies auf der Ebene der Ressourcengruppe tun. Wenn Sie die Seite **Fortlaufender Export** auf der Portalbenutzeroberfläche von Defender für Cloud verwenden, müssen Sie sie auf der Abonnementsebene definieren.

    > [!TIP]
    > Wenn Sie mehrere Exportkonfigurationen mit der API eingerichtet haben oder reine API-Parameter verwendet haben, werden diese zusätzlichen Features nicht auf der Benutzeroberfläche von Defender für Cloud angezeigt. Stattdessen erscheint ein Banner, das Sie darüber informiert, dass andere Konfigurationen existieren.

Weitere Informationen zur Automatisierungen-API finden Sie in der [REST-API-Dokumentation](/rest/api/securitycenter/automations).

### <a name="deploy-at-scale-with-azure-policy"></a>[**Bereitstellung im großen Stil mit Azure Policy**](#tab/azure-policy)

### <a name="configure-continuous-export-at-scale-using-the-supplied-policies"></a>Konfigurieren des fortlaufenden Exports im großen Stil mithilfe der bereitgestellten Richtlinien

Die Automatisierung der Prozesse Ihrer Organisation zur Überwachung und Reaktion auf Vorfälle kann die Zeit, die zum Untersuchen von Sicherheitsvorfällen und zur Durchführung entsprechender Gegenmaßnahmen benötigt wird, erheblich verkürzen.

Verwenden Sie für die Bereitstellung Ihrer Konfigurationen für den fortlaufenden Export in Ihrer Organisation die unten beschriebenen von Azure Policy bereitgestellten „DeployIfNotExist“-Richtlinien zum Erstellen und Konfigurieren von Prozeduren für den fortlaufenden Export.

**So implementieren Sie diese Richtlinien**

1. Klicken Sie in der folgenden Tabelle auf die Richtlinie, die Sie anwenden möchten:

    |Zielsetzung  |Richtlinie  |Richtlinien-ID  |
    |---------|---------|---------|
    |Fortlaufender Export zu einem Event Hub|[Bereitstellen eines Exports in Event Hub für Warnungen und Empfehlungen von Microsoft Defender für Cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
    |Fortlaufender Export in einen Log Analytics-Arbeitsbereich|[Bereitstellen eines Exports in einem Log Analytics-Arbeitsbereich für Warnungen und Empfehlungen von Microsoft Defender für Cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
    ||||

    > [!TIP]
    > Sie können auch in Azure Policy nach diesen Richtlinien suchen:
    > 1. Öffnen Sie Azure Policy.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Zugreifen auf Azure Policy.":::
    > 2. Klicken Sie im Azure Policy-Menü auf **Definitionen**, und suchen Sie nach dem Namen der gewünschten Richtlinie. 

1. Klicken Sie auf der entsprechenden Azure Policy-Seite auf **Zuweisen**.
    :::image type="content" source="./media/continuous-export/export-policy-assign.png" alt-text="Zuweisen der Azure-Richtlinie.":::

1. Öffnen Sie alle Registerkarten, und legen Sie die Parameter wie gewünscht fest:
    1. Legen Sie auf der Registerkarte **Grundeinstellungen** den Bereich für die Richtlinie fest. Weisen Sie die Richtlinie für eine zentrale Verwaltung der Verwaltungsgruppe mit den Abonnements zu, die die Konfiguration für den fortlaufenden Export verwenden sollen. 
    1. Legen Sie auf der Registerkarte **Parameter** die Ressourcengruppe und die Details für den Datentyp fest. 
        > [!TIP]
        > Jeder Parameter verfügt über eine QuickInfo, in der die verfügbaren Optionen erläutert werden.
        >
        > Die Registerkarte „Parameter“ in Azure Policy (1) bietet ähnliche Konfigurationsoptionen wie die Seite „Fortlaufender Export“ in Defender für Cloud (2).
        > :::image type="content" source="./media/continuous-export/azure-policy-next-to-continuous-export.png" alt-text="Vergleich der Parameter auf der Seite „Fortlaufender Export“ mit Azure Policy." lightbox="./media/continuous-export/azure-policy-next-to-continuous-export.png":::
    1. Wenn Sie diese Zuweisung auf vorhandene Abonnements anwenden möchten, können Sie die Registerkarte **Wartung** öffnen und die Option zum Erstellen eines Wartungstasks auswählen.
1. Überprüfen Sie die Seite „Zusammenfassung“, und klicken Sie auf **Erstellen**.

--- 

## <a name="information-about-exporting-to-a-log-analytics-workspace"></a>Informationen zum Export in einen Log Analytics-Arbeitsbereich

Wenn Sie Daten von Microsoft Defender für Cloud in einem Log Analytics-Arbeitsbereich analysieren oder Azure-Warnungen zusammen mit Defender für Cloud-Warnungen verwenden möchten, richten Sie einen fortlaufenden Export für Ihren Log Analytics-Arbeitsbereich ein.

### <a name="log-analytics-tables-and-schemas"></a>Log Analytics-Tabellen und -Schemas

Sicherheitswarnungen und -empfehlungen werden in den Tabellen *SecurityAlert* und *SecurityRecommendation* gespeichert. 

Der Name der Log Analytics-Lösung, die diese Tabellen enthält, hängt davon ab, ob Sie die erweiterten Sicherheitsfeatures aktiviert haben: Sicherheit („Sicherheit und Überwachung“) oder SecurityCenterFree. 

> [!TIP]
> Sie müssen die Lösung **Sicherheit und Überwachung** oder **SecurityCenterFree** aktivieren, um die Daten im Zielarbeitsbereich anzuzeigen.

![Die Tabelle *SecurityAlert* in Log Analytics.](./media/continuous-export/log-analytics-securityalert-solution.png)

Die Ereignisschemas der exportierten Datentypen finden Sie bei den [Log Analytics-Tabellenschemas](https://aka.ms/ASCAutomationSchemas).


##  <a name="view-exported-alerts-and-recommendations-in-azure-monitor"></a>Anzeigen exportierter Warnungen und Empfehlungen in Azure Monitor

Sie können die exportierten Sicherheitswarnungen und Empfehlungen auch in [Azure Monitor](../azure-monitor/alerts/alerts-overview.md) anzeigen. 

Azure Monitor bietet eine einheitliche Warnungsbenutzeroberfläche für eine Vielzahl von Azure-Warnungen, einschließlich Diagnoseprotokoll, Metrikwarnungen und benutzerdefinierte Warnungen, die auf Log Analytics-Arbeitsbereichsabfragen basieren.

Konfigurieren Sie eine Warnungsregel, die auf Log Analytics-Abfragen (Protokollwarnung) basiert, um Warnungen und Empfehlungen von Defender für Cloud in Azure Monitor anzuzeigen:

1. Wählen Sie auf der Seite **Warnungen** von Azure Monitor **Neue Warnungsregel** aus.

    ![Seite „Warnungen“ von Azure Monitor.](./media/continuous-export/azure-monitor-alerts.png)

1. Konfigurieren Sie auf der Seite „Regel erstellen“ Ihre neue Regel (auf die gleiche Weise, wie Sie eine [Protokollwarnungsregel in Azure Monitor](../azure-monitor/alerts/alerts-unified-log.md) konfigurieren würden):

    * Wählen Sie als **Ressource** den Log Analytics-Arbeitsbereich aus, in den Sie Sicherheitswarnungen und Empfehlungen exportiert haben.

    * Wählen Sie als **Bedingung** die Option **Benutzerdefinierte Protokollsuche** aus. Konfigurieren Sie auf der Seite, die angezeigt wird, die Abfrage, den Rückblickzeitraum und den Häufigkeitszeitraum. In der Suchabfrage können Sie *SecurityAlert* oder *SecurityRecommendation* eingeben, um die Datentypen abzufragen, in die Defender für Cloud kontinuierlich exportiert, wenn Sie die Funktion „Fortlaufender Export in Log Analytics“ aktivieren. 
    
    * Konfigurieren Sie optional die [Aktionsgruppe](../azure-monitor/alerts/action-groups.md), die Sie auslösen möchten. Aktionsgruppen können das Senden von E-Mails-, ITSM-Tickets, WebHooks und vieles mehr auslösen.
    ![Azure Monitor-Warnungsregel.](./media/continuous-export/azure-monitor-alert-rule.png)

Ihnen werden nun neue Warnungen oder Empfehlungen von Defender für Cloud (abhängig von Ihren konfigurierten Regeln für den fortlaufenden Export und den in Ihrer Azure Monitor-Warnungsregel definierten Bedingungen) in den Azure Monitor-Warnungen angezeigt, und eine Aktionsgruppe (sofern vorhanden) wird automatisch ausgelöst.

## <a name="manual-one-time-export-of-alerts-and-recommendations"></a>Manueller einmaliger Export von Warnungen und Empfehlungen

Wenn Sie einen CSV-Bericht für Warnungen oder Empfehlungen herunterladen möchten, öffnen Sie die Seite **Sicherheitswarnungen** oder **Empfehlungen**, und wählen Sie **CSV-Bericht herunterladen** aus.

> [!TIP]
> Aufgrund der Einschränkungen von Azure Resource Graph sind die Berichte auf eine Dateigröße von 13.000 Zeilen beschränkt. Wenn Fehler im Zusammenhang mit zu vielen exportierten Daten auftreten, versuchen Sie, die Ausgabe einzuschränken, indem Sie eine kleinere Menge an Abonnements auswählen, die exportiert werden sollen.

:::image type="content" source="./media/continuous-export/download-alerts-csv.png" alt-text="Herunterladen von Warnungsdaten als CSV-Datei." lightbox="./media/continuous-export/download-alerts-csv.png":::

> [!NOTE]
> Diese Berichte enthalten Warnungen und Empfehlungen für Ressourcen in den aktuell ausgewählten Abonnements.


## <a name="faq---continuous-export"></a>Häufig gestellte Fragen – Fortlaufender Export

### <a name="what-are-the-costs-involved-in-exporting-data"></a>Welche Kosten sind mit dem Datenexport verbunden?

Für die Ermöglichung eines fortlaufenden Exports fallen keine Kosten an. Möglicherweise fallen Kosten für die Erfassung und Aufbewahrung von Daten in Ihrem Log Analytics-Arbeitsbereich an, abhängig von Ihrer Konfiguration. 

Weitere Informationen: [Preise für Log Analytics-Arbeitsbereiche](https://azure.microsoft.com/pricing/details/monitor/).

Weitere Informationen: [Azure Event Hub – Preise](https://azure.microsoft.com/pricing/details/event-hubs/).


### <a name="does-the-export-include-data-about-the-current-state-of-all-resources"></a>Enthält der Export Daten zum aktuellen Status aller Ressourcen?

Nein. Der fortlaufende Export wurde für das Streaming von **Ereignissen** konzipiert:

- Vor dem Aktivieren des Exports empfangene **Warnungen** werden nicht exportiert.
- **Empfehlungen** werden immer dann gesendet, wenn sich der Konformitätszustand einer Ressource ändert. Wenn eine Ressource z. B. von „fehlerfrei“ in „fehlerhaft“ wechselt. Daher werden, wie bei Warnungen, keine Empfehlungen für Ressourcen exportiert, deren Zustand sich seit der Aktivierung des Exports nicht geändert hat.
- **Sicherheitsbewertung** pro Sicherheitskontrolle oder Abonnement wird gesendet, wenn sich die Bewertung einer Sicherheitskontrolle um 0,01 oder mehr ändert. 
- **Der Status der Einhaltung gesetzlicher Bestimmungen** wird gesendet, wenn sich der Status der Compliance der Ressource ändert.



### <a name="why-are-recommendations-sent-at-different-intervals"></a>Warum werden Empfehlungen in unterschiedlichen Intervallen gesendet?

Unterschiedliche Empfehlungen weisen unterschiedliche Intervalle für die Konformitätsauswertung auf, die von einigen Minuten bis zu einigen Tagen reichen können. Folglich unterscheiden sich die Empfehlungen in der Zeit, die es dauert, bis sie in Ihren Exporten erscheinen.

### <a name="does-continuous-export-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>Unterstützt der fortlaufende Export irgendwelche Szenarien der Geschäftskontinuität oder Notfallwiederherstellung (BCDR)?

Wenn Sie Ihre Umgebung für BCDR-Szenarien vorbereiten, in denen in der Zielressource ein Ausfall oder ein anderer Notfass eintritt, liegt es in der Verantwortung der Organisation, durch Einrichten von Sicherungen gemäß den Richtlinien von Azure Event Hubs, Log Analytics-Arbeitsbereichen und der Logik-App Datenverluste zu verhindern.

Weitere Informationen finden Sie unter [Azure Event Hubs: Georedundante Notfallwiederherstellung](../event-hubs/event-hubs-geo-dr.md).


### <a name="is-continuous-export-available-for-free"></a>Steht der fortlaufende Export kostenlos zur Verfügung?

Ja. Beachten Sie, dass viele Warnungen nur bereitgestellt werden, wenn Sie erweiterten Schutz aktiviert haben. Eine gute Möglichkeit, eine Vorschau der in den exportierten Daten erhaltenen Warnungen anzuzeigen, besteht darin, sich die Warnungen auf den Seiten von Defender für Cloud des Azure-Portals anzuschauen.



## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie fortlaufende Exporte ihrer Empfehlungen und Warnungen konfigurieren. Außerdem haben Sie gelernt, wie Sie Ihre Warnungsdaten als CSV-Datei herunterladen. 

Verwandtes Material finden Sie in der folgenden Dokumentation: 

- Informieren Sie sich über [Vorlagen zur Workflowautomatisierung](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).
- [Dokumentation zu Azure Event Hubs](../event-hubs/index.yml)
- [Dokumentation zu Microsoft Sentinel](../sentinel/index.yml)
- [Azure Monitor-Dokumentation](../azure-monitor/index.yml)
- [Exportieren von Datentypenschemas](https://aka.ms/ASCAutomationSchemas)
