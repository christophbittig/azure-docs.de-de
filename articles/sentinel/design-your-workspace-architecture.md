---
title: Entwerfen Ihrer Microsoft Sentinel-Arbeitsbereichsarchitektur | Microsoft-Dokumentation
description: Verwenden Sie eine Entscheidungsstruktur, um zu verstehen, wie Sie Ihre Microsoft Sentinel-Arbeitsbereichsarchitektur möglicherweise entwerfen möchten.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0fccafee7a645be13ae17eca98d99305dd2b4a10
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521546"
---
# <a name="design-your-microsoft-sentinel-workspace-architecture"></a>Entwerfen der Microsoft Sentinel-Arbeitsbereichsarchitektur

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Dieser Artikel enthält eine Entscheidungsstruktur, mit deren Hilfe Sie wichtige Entscheidungen zum Entwurf Ihrer Microsoft Sentinel-Arbeitsbereichsarchitektur treffen können. Weitere Informationen finden Sie unter [Beispielentwürfe von Microsoft Sentinel-Arbeitsbereichen](sample-workspace-designs.md) und [Bewährte Methoden für die Microsoft Sentinel-Arbeitsbereichsarchitektur](best-practices-workspace-architecture.md).

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie die Entscheidungsstruktur durcharbeiten, stellen Sie sicher, dass Sie über die folgenden Informationen verfügen:

|Voraussetzung  | BESCHREIBUNG |
|---------|---------|
|**Rechtliche Anforderungen im Zusammenhang mit der Data Residency in Azure**     |  Microsoft Sentinel kann in Arbeitsbereichen in den meisten, jedoch nicht in allen, Regionen ausgeführt werden, die [in der geografischen Verfügbarkeit für Log Analytics unterstützt werden](https://azure.microsoft.com/global-infrastructure/services/?products=monitor). Das Onboarding des Microsoft Sentinel-Diensts für neu unterstützte Log Analytics-Regionen kann einige Zeit dauern. <br><br> Von Microsoft Sentinel generierte Daten, z. B. Incidents, Lesezeichen und Analyseregeln, enthalten ggf. Kundendaten, die aus den Log Analytics-Arbeitsbereichen des Kunden stammen.<br><br> Weitere Informationen finden Sie unter [Geografische Verfügbarkeit und Data Residency](quickstart-onboard.md#geographical-availability-and-data-residency).|
|**Datenquellen**     |   Erfahren Sie, welche [Datenquellen](connect-data-sources.md) Sie verbinden müssen, einschließlich integrierter Connectors für Microsoft-Lösungen und Lösungen von anderen Anbietern als Microsoft. Sie können auch Common Event Format (CEF), Syslog oder eine REST-API verwenden, um Ihre Datenquellen mit Microsoft Sentinel zu verbinden. <br><br>Wenn Sie über virtuelle Azure-Computer an mehreren Azure-Standorten verfügen, von denen Sie die Protokolle erfassen müssen, und die Einsparung von Kosten für ausgehende Daten für Sie von Bedeutung ist, müssen Sie die Kosten für ausgehende Daten mithilfe des [Bandbreitenpreisrechners](https://azure.microsoft.com/pricing/details/bandwidth/#overview) für jeden Azure-Standort berechnen.      |
|**Benutzerrollen und Datenzugriffsebenen/-berechtigungen**     |    Microsoft Sentinel verwendet die [rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control/role-assignments-portal.md) (Azure Role-Based Access Control, Azure RBAC). Dabei werden [integrierte Rollen](../role-based-access-control/built-in-roles.md) bereitgestellt, die Benutzern, Gruppen und Diensten in Azure zugewiesen werden können. <br><br>Alle in Microsoft Sentinel integrierten Rollen gewähren Lesezugriff auf die Daten in Ihrem Microsoft Sentinel-Arbeitsbereich. Daher müssen Sie herausfinden, ob der Datenzugriff pro Datenquelle oder Zeilenebene gesteuert werden muss, da sich dies auf die Entscheidung beim Entwurf des Arbeitsbereichs auswirkt. Weitere Informationen finden Sie unter [Custom roles and advanced Azure RBAC](roles.md#custom-roles-and-advanced-azure-rbac) (Benutzerdefinierte Rollen und erweiterte rollenbasierte Zugriffssteuerung in Azure (RBAC)).     |
|**Tägliche Erfassungsrate**     |  Die tägliche Erfassungsrate (in der Regel in GB/Tag) ist einer der wichtigsten Faktoren bei den Überlegungen zur Kostenverwaltung und -planung sowie beim Entwurf von Arbeitsbereichen für Microsoft Sentinel. <br><br>In den meisten Cloud- und Hybridumgebungen erzeugen Netzwerkgeräte wie Firewalls oder Proxys sowie Windows- und Linux-Server die am häufigsten erfassten Daten. Damit Sie die genauesten Ergebnisse erhalten, empfiehlt Microsoft ein umfassendes Inventar von Datenquellen. <br><br>Alternativ dazu enthält der Microsoft Sentinel-[Kostenrechner](https://cloudpartners.transform.microsoft.com/download?assetname=assets%2FAzure_Sentinel_Calculator.xlsx&download=1) Tabellen, die bei der Schätzung des Speicherbedarfs von Datenquellen hilfreich sind. <br><br>**Wichtig**: Diese Schätzungen sind ein Ausgangspunkt, die Einstellungen für die Ausführlichkeit des Protokolls und die Workload führen zu Abweichungen. Es wird empfohlen, Ihr System regelmäßig zu überwachen, um Änderungen nachzuverfolgen. Eine regelmäßige Überwachung wird basierend auf Ihrem Szenario empfohlen. <br><br>Weitere Informationen finden Sie unter [Verwalten von Nutzung und Kosten mit Azure Monitor-Protokollen](../azure-monitor/logs/manage-cost-storage.md).       |
|     |         |

## <a name="decision-tree"></a>Entscheidungsstruktur

Die folgende Abbildung zeigt ein Flussdiagramm einer vollständigen Entscheidungsstruktur, mit dem Sie besser verstehen können, wie Sie Ihren Arbeitsbereich am besten entwerfen.

[![Entscheidungsstruktur für den Entwurf eines Microsoft Sentinel-Arbeitsbereichs](media/best-practices/workspace-decision-tree.png)](media/best-practices/workspace-decision-tree.png#lightbox)

Die folgenden Abschnitte enthalten eine Volltextversion dieser Entscheidungsstruktur, einschließlich der folgenden Hinweise, auf die in der Abbildung verwiesen wird:

[Hinweis #1](#note1) | [Hinweis #2](#note2)  | [Hinweis #3](#note3)  | [Hinweis #4](#note4)  | [Hinweis #5](#note5)  | [Hinweis #6](#note6)  | [Hinweis #7](#note7)  | [Hinweis #8](#note8)  | [Hinweis #9](#note9) | [Hinweis #10](#note10)

### <a name="step-1-new-or-existing-workspace"></a>Schritt 1: Neuer oder vorhandener Arbeitsbereich?

Verfügen Sie über einen vorhandenen Arbeitsbereich, den Sie für Microsoft Sentinel verwenden können?

- **Falls nicht und Sie ohnehin einen neuen Arbeitsbereich erstellen**, fahren Sie direkt mit [Schritt 2](#step-2-keeping-data-in-different-azure-geographies) fort.

- **Wenn Sie über einen vorhandenen Arbeitsbereich verfügen**, den Sie möglicherweise verwenden, überlegen Sie, wie viele Daten Sie erfassen werden.

  - **Wenn Sie *mehr* als 100 GB/Tag erfassen**, empfehlen wir aus Kostengründen, einen separaten Arbeitsbereich zu verwenden.

  - **Wenn Sie *weniger* als 100 GB/Tag erfassen**, fahren Sie zur weiteren Auswertung mit [Schritt 2](#step-2-keeping-data-in-different-azure-geographies) fort. Überdenken Sie diese Frage erneut, wenn sie sich in [Schritt 5](#step-5-collecting-any-non-soc-data) stellt.

### <a name="step-2-keeping-data-in-different-azure-geographies"></a>Schritt 2: Werden Daten in verschiedenen Azure-Geografien aufbewahrt?

- **Wenn Sie aufgrund von rechtlichen Anforderungen Daten in verschiedenen Azure-Geografien aufbewahren müssen**, verwenden Sie für jede Azure-Region, für die Complianceanforderungen bestehen, einen separaten Microsoft Sentinel-Arbeitsbereich. Weitere Informationen finden Sie unter [Überlegungen zu Bereichen](best-practices-workspace-architecture.md#region-considerations).

- **Wenn Sie Daten nicht in verschiedenen Azure-Geografien aufbewahren müssen**, fahren Sie mit [Schritt 3](#step-3-do-you-have-multiple-azure-tenants) fort.

### <a name="step-3-do-you-have-multiple-azure-tenants"></a>Schritt 3: Verfügen Sie über mehrere Azure-Mandanten?

- **Wenn Sie nur über einen einzelnen Mandanten verfügen**, fahren Sie direkt mit [Schritt 4](#step-4-splitting-billing--charge-back) fort.

- **Wenn Sie über mehrere Azure-Mandanten verfügen**, sollten Sie überlegen, ob Sie Protokolle erfassen, die sich auf eine Mandantengrenze, wie Office 365 oder Microsoft 365 Defender, beschränken.

  - **Wenn Sie über keine mandantenspezifischen Protokolle verfügen**, fahren Sie direkt mit [Schritt 4](#step-4-splitting-billing--charge-back) fort.

  - **Wenn Sie *tatsächlich* mandantenspezifische Protokolle erfassen**, verwenden Sie für jeden Azure AD-Mandanten einen separaten Microsoft Sentinel-Arbeitsbereich. Für weitere Überlegungen fahren Sie mit [Schritt 4](#step-4-splitting-billing--charge-back) fort.

    <a name="note1"></a>[Entscheidungsstrukturhinweis #1](#decision-tree): Protokolle, die sich auf Mandantengrenzen beschränken, wie bei Office 365 und Microsoft Defender for Cloud, können nur im Arbeitsbereich innerhalb desselben Mandanten gespeichert werden.

    Obwohl es *möglich* ist, benutzerdefinierte Collectors zu verwenden, um mandantenspezifische Protokolle aus einem Arbeitsbereich in einem anderen Mandanten zu erfassen, wird dies aufgrund der folgenden Nachteile nicht empfohlen:

    - Von benutzerdefinierten Connectors gesammelte Daten werden in benutzerdefinierten Tabellen erfasst. Daher können Sie nicht alle integrierten Regeln und Arbeitsmappen verwenden.
    - Benutzerdefinierte Tabellen werden von einigen der integrierten Features wie UEBA und Machine Learning-Regeln nicht berücksichtigt.
    - Erforderliche zusätzliche Kosten und zusätzlicher Aufwand für die benutzerdefinierten Connectors, z. B. die Verwendung von Azure Functions und Logic Apps.

    Wenn diese Nachteile für Ihre Organisation nicht von Bedeutung sind, fahren Sie mit [Schritt 4](#step-4-splitting-billing--charge-back) fort, anstatt separate Microsoft Sentinel-Arbeitsbereiche zu verwenden.

### <a name="step-4-splitting-billing--charge-back"></a>Schritt 4: Werden Abrechnung oder Chargeback geteilt?

Wenn Sie Ihre Abrechnung oder Ihr Chargeback teilen müssen, sollten Sie überlegen, ob die Nutzungsberichterstellung oder die manuelle Verrechnung für Sie geeignet ist.

- **Wenn Sie Ihre Abrechnung oder Ihr Chargeback *nicht* teilen müssen**, fahren Sie mit [Schritt 5](#step-5-collecting-any-non-soc-data) fort.

- **Wenn Sie Ihre Abrechnung oder Ihr Chargeback *tatsächlich* teilen müssen**, sollten Sie überlegen, ob [die Nutzungsberichterstellung oder die manuelle Verrechnung](azure-sentinel-billing.md) für Sie geeignet ist.

  - **Wenn die Nutzungsberichterstellung oder die manuelle Verrechnung für Sie geeignet ist**, fahren Sie mit [Schritt 5](#step-5-collecting-any-non-soc-data) fort.

  - **Wenn *weder* die Nutzungsberichterstellung noch die manuelle Verrechnung für Sie geeignet ist**, verwenden Sie für jeden Kostenträger einen separaten Microsoft Sentinel-Arbeitsbereich.

  <a name="note2"></a>[Entscheidungsstrukturhinweis #2](#decision-tree): Weitere Informationen finden Sie unter [Microsoft Sentinel – Kosten und Abrechnung](azure-sentinel-billing.md).

### <a name="step-5-collecting-any-non-soc-data"></a>Schritt 5: Werden Nicht-SOC-Daten erfasst?

- **Wenn Sie keine Nicht-SOC-Daten erfassen**, wie beispielsweise operative Daten, können Sie direkt mit [Schritt 6](#step-6-multiple-regions) fortfahren.

- **Wenn Sie *tatsächlich* Nicht-SOC-Daten erfassen**, sollten Sie überlegen, ob es Überlappungen gibt, bei denen dieselbe Datenquelle sowohl für SOC-Daten als auch für Nicht-SOC-Daten erforderlich ist.

  **Wenn es *tatsächlich* Überlappungen zwischen SOC-Daten und Nicht-SOC-Daten gibt**, behandeln Sie die sich überlappenden Daten nur als SOC-Daten. Überlegen Sie anschließend, ob die Erfassung *sowohl* für SOC-Daten als auch für Nicht-SOC-Daten einzeln weniger als 100 GB/Tag beträgt, kombiniert jedoch mehr als 100 GB/Tag:

  - **Ja**: Fahren Sie zur weiteren Auswertung mit [Schritt 6](#step-6-multiple-regions) fort.
  - **Nein**: Wir empfehlen aus Kostengründen, nicht denselben Arbeitsbereich zu verwenden. Fahren Sie zur weiteren Auswertung mit [Schritt 6](#step-6-multiple-regions) fort.

  Weitere Informationen zu beiden Fällen finden Sie unter [Hinweis 10](#note10).

  **Wenn es *keine* sich überlappenden Daten gibt**, sollten Sie überlegen, ob die Erfassung *sowohl* für SOC-Daten als auch für Nicht-SOC-Daten einzeln weniger als 100 GB/Tag beträgt, kombiniert jedoch mehr als 100 GB/Tag:

  - **Ja**: Fahren Sie zur weiteren Auswertung mit [Schritt 6](#step-6-multiple-regions) fort. Weitere Informationen finden Sie unter [Hinweis 3](#combining-your-soc-and-non-soc-data).
  - **Nein**: Wir empfehlen aus Kostengründen, nicht denselben Arbeitsbereich zu verwenden. Fahren Sie zur weiteren Auswertung mit [Schritt 6](#step-6-multiple-regions) fort.

#### <a name="combining-your-soc-and-non-soc-data"></a>Kombinieren Ihrer SOC-Daten und Nicht-SOC-Daten

<a name="note3"></a>[Entscheidungsstrukturhinweis #3](#decision-tree): Obwohl wir im Allgemeinen empfehlen, dass Kunden einen separaten Arbeitsbereich für ihre Nicht-SOC-Daten verwenden, damit Nicht-SOC-Daten keinen Microsoft Sentinel-Kosten unterliegen, kann es Situationen geben, in denen die Kombination von SOC-Daten und Nicht-SOC-Daten kostengünstiger ist als ihre Trennung.

Stellen Sie sich beispielsweise eine Organisation vor, die über Sicherheitsprotokolle mit einer Erfassung von 50 GB/Tag, Vorgangsprotokolle mit einer Erfassung von 50 GB/Tag und über einen Arbeitsbereich in der Region "USA, Osten" verfügt.

In der folgenden Tabelle werden Arbeitsbereichsoptionen mit und ohne separate Arbeitsbereiche verglichen.

> [!NOTE]
> Die in der folgenden Tabelle aufgeführten Kosten und Bedingungen sind nicht echt und dienen nur zur Veranschaulichung. Aktuelle Kosteninformationen erhalten Sie über den Microsoft Sentinel-Preisrechner.
>

|Arbeitsbereichsarchitektur  |BESCHREIBUNG |
|---------|---------|
|Das SOC-Team verfügt über einen eigenen Arbeitsbereich, in dem Microsoft Sentinel aktiviert ist. <br><br>Das Betriebsteam verfügt über einen eigenen Arbeitsbereich, in dem kein Microsoft Sentinel aktiviert ist.     |  **SOC-Team**: <br>Die Microsoft Sentinel-Kosten für 50 GB/Tag betragen 6.500 USD pro Monat.<br>Die ersten drei Monate der Aufbewahrung sind kostenlos. <br><br>**Betriebsteam**:<br>- Die Kosten für Log Analytics mit 50 GB/Tag betragen etwa 3.500 USD pro Monat.<br>- Die ersten 31 Tage der Aufbewahrung sind kostenlos.<br><br>Die Gesamtkosten für beides betragen 10.000 USD pro Monat.       |
|Sowohl das SOC-Team als auch das Betriebsteam nutzen denselben Arbeitsbereich, in dem Microsoft Sentinel aktiviert ist. |Bei einer Kombination beider Protokolle beträgt die Erfassung 100 GB/Tag und erfüllt damit die Voraussetzungen für die Berechtigung zur Mindestabnahme (50 % für Sentinel und 15 % für LA).       <br><br>Die Microsoft Sentinel-Kosten für 100 GB/Tag betragen 9.000 USD pro Monat.      |
|     |         |

In diesem Beispiel erzielen Sie eine Kosteneinsparung von 1.000 USD pro Monat, indem Sie beide Arbeitsbereiche kombinieren, und das Betriebsteam hat darüber hinaus den Vorteil einer kostenlosen Aufbewahrung von 3 Monaten anstelle von nur 31 Tagen.

Dieses Beispiel ist nur relevant, wenn die SOC-Daten und Nicht-SOC-Daten jeweils eine Erfassungsgröße von >=50 GB/Tag und <100 GB/Tag aufweisen.

<a name="note10"></a>[Entscheidungsstrukturhinweis #10](#decision-tree): Wir empfehlen, einen separaten Arbeitsbereich für Nicht-SOC-Daten zu verwenden, damit Nicht-SOC-Daten keinen Microsoft Sentinel-Kosten unterliegen.

Diese Empfehlung für separate Arbeitsbereiche für Nicht-SOC-Daten basiert jedoch auf einer rein kostenorientierten Perspektive, und es gibt andere wichtige Entwurfsfaktoren, die bei der Entscheidung, ob ein einzelner Arbeitsbereich oder mehrere Arbeitsbereiche verwendet werden sollen, zu untersuchen sind. Um doppelte Erfassungskosten zu vermeiden, sollten Sie erwägen, überlappende Daten nur in einem einzelnen Arbeitsbereich mit Azure RBAC auf Tabellenebene zu erfassen.

### <a name="step-6-multiple-regions"></a>Schritt 6: Mehrere Regionen?

- **Wenn Sie Protokolle von virtuellen Azure-Computern nur in einer *einzelnen* Region erfassen**, fahren Sie direkt mit [Schritt 7](#step-7-segregating-data-or-defining-boundaries-by-ownership) fort.

- **Wenn Sie Protokolle von virtuellen Azure-Computern in *mehreren* Regionen erfassen**, machen Sie sich dann viele Gedanken über die Kosten für ausgehende Daten?

  <a name="note4"></a>[Entscheidungsstrukturhinweis #4](#decision-tree): Der Begriff „Ausgehende Daten“ bezieht sich auf die [Bandbreitenkosten](https://azure.microsoft.com/pricing/details/bandwidth/) für die Übertragung von Daten aus Azure-Rechenzentren. Weitere Informationen finden Sie unter [Überlegungen zu Bereichen](best-practices-workspace-architecture.md#region-considerations).

  - Wenn die Reduzierung des Aufwands, der zum Verwalten separater Arbeitsbereiche erforderlich ist, Priorität hat, fahren Sie mit [Schritt 7](#step-7-segregating-data-or-defining-boundaries-by-ownership) fort.

  - Wenn die Kosten für ausgehende Daten eine so große Rolle spielen, dass sich die Verwaltung separater Arbeitsbereiche lohnt, verwenden Sie einen separaten Microsoft Sentinel-Arbeitsbereich für jede Region, in der Sie die Kosten für ausgehende Daten reduzieren müssen.

    <a name="note5"></a>[Entscheidungsstrukturhinweis #5](#decision-tree): Wir empfehlen, so wenige Arbeitsbereiche wie möglich zu verwenden. Verwenden Sie den [Azure-Preisrechner](azure-sentinel-billing.md#estimate-microsoft-sentinel-costs), um die Kosten zu schätzen und zu entscheiden, welche Regionen Sie tatsächlich benötigen, und kombinieren Sie Arbeitsbereiche für Regionen mit niedrigen Kosten für ausgehende Daten. Bandbreitenkosten machen im Vergleich zu separaten Kosten für die Microsoft Sentinel- und Log Analytics-Erfassung möglicherweise nur einen kleinen Teil Ihrer Azure-Rechnung aus.

    Beispielsweise könnten Ihre Kosten wie folgt geschätzt werden:

    - 1\.000 virtuelle Computer, die jeweils 1 GB/Tag generieren;
    - Senden von Daten aus einer US-Region an eine EU-Region;
    - Verwendung einer 2:1-Komprimierungsrate im Agent

    Die Berechnung für diese geschätzten Kosten wäre: `1000 VMs * (1GB/day ÷ 2) * 30 days/month * $0.05/GB = $750/month bandwidth cost`

    Diese Beispielkosten wären im Vergleich zu den monatlichen Kosten eines separaten Microsoft Sentinel- und Log Analytics-Arbeitsbereichs deutlich günstiger.

    > [!NOTE]
    > Die aufgeführten Kosten sind nicht echt und dienen nur zur Veranschaulichung. Aktuelle Kosteninformationen erhalten Sie über den Microsoft Sentinel-Preisrechner.
    >

### <a name="step-7-segregating-data-or-defining-boundaries-by-ownership"></a>Schritt 7: Werden Daten getrennt oder Grenzen nach Besitz definiert?

- **Wenn Sie *keine* Daten trennen oder Besitzgrenzen definieren müssen**, fahren Sie direkt mit [Schritt 8](#step-8-controlling-data-access-by-data-source--table) fort.

- **Wenn Sie *tatsächlich* Daten trennen oder Grenzen nach Besitz definieren müssen**, muss dann jeder Datenbesitzer das Microsoft Sentinel-Portal verwenden?

  - Wenn jeder Datenbesitzer Zugriff auf das Microsoft Sentinel-Portal haben muss, verwenden Sie einen separaten Microsoft Sentinel-Arbeitsbereich für jeden Besitzer.

    <a name="note6"></a>[Entscheidungsstrukturhinweis #6](#decision-tree): Für den Zugriff auf das Microsoft Sentinel-Portal ist es erforderlich, dass jeder Benutzer mindestens über die Rolle [Microsoft Sentinel-Leser](../role-based-access-control/built-in-roles.md) mit den **Leser**-Berechtigungen für alle Tabellen im Arbeitsbereich verfügt. Wenn ein Benutzer keinen Zugriff auf alle Tabellen im Arbeitsbereich hat, muss er Log Analytics verwenden, um auf die Protokolle in Suchabfragen zuzugreifen.

  - Wenn der Zugriff auf die Protokolle über Log Analytics für alle Besitzer ohne Zugriff auf das Microsoft Sentinel-Portal ausreicht, fahren Sie mit [Schritt 8](#step-8-controlling-data-access-by-data-source--table) fort.

  Weitere Informationen hierzu finden Sie unter [Berechtigungen in Microsoft Sentinel](roles.md).

### <a name="step-8-controlling-data-access-by-data-source--table"></a>Schritt 8: Wird der Datenzugriff nach Datenquelle/Tabelle gesteuert?

- **Wenn Sie den Datenzugriff *nicht* nach Quelle oder Tabelle steuern müssen**, verwenden Sie einen einzelnen Microsoft Sentinel-Arbeitsbereich.

- **Wenn Sie den Datenzugriff *tatsächlich* nach Quelle oder Tabelle steuern müssen**, sollten Sie erwägen, in den folgenden Situationen die [rollenbasierte Zugriffssteuerung (RBAC) im Ressourcenkontext](resource-context-rbac.md) zu verwenden:

  - Wenn Sie den Zugriff auf Zeilenebene steuern müssen, z. B. bei der Angabe mehrerer Besitzer für einzelne Datenquellen oder Tabellen

  - Wenn Sie über mehrere benutzerdefinierte Datenquellen/Tabellen verfügen, für die jeweils separate Berechtigungen erforderlich sind

  Wenn Sie in anderen Fällen den Zugriff *nicht* auf Zeilenebene steuern müssen, stellen Sie mehrere benutzerdefinierte Datenquellen/Tabellen mit separaten Berechtigungen bereit, und verwenden Sie einen einzelnen Microsoft Sentinel-Arbeitsbereich mit RBAC auf Tabellenebene für die Datenzugriffssteuerung.

#### <a name="considerations-for-resource-context-or-table-level-rbac"></a>Überlegungen zu RBAC im Ressourcenkontext oder RBAC auf Tabellenebene

Wenn Sie die Verwendung der rollenbasierten Zugriffssteuerung (RBAC) im Ressourcenkontext oder von RBAC auf Tabellenebene planen, sollten Sie Folgendes berücksichtigen:

- <a name="note7"></a>[Entscheidungsstrukturhinweis #7](#decision-tree): Für die Konfiguration der rollenbasierten Zugriffssteuerung im Ressourcenkontext für Nicht-Azure-Ressourcen möchten Sie möglicherweise den Daten eine Ressourcen-ID zuordnen, wenn Sie an Microsoft Sentinel senden, so dass die Berechtigung mit der rollenbasierten Zugriffssteuerung im Ressourcenkontext auf einen Bereich festgelegt werden kann. Weitere Informationen finden Sie unter [Explizites Konfigurieren der rollenbasierten Zugriffssteuerung im Ressourcenkontext](resource-context-rbac.md#explicitly-configure-resource-context-rbac) und [Access modes by deployment](../azure-monitor/logs/design-logs-deployment.md) (Zugriffsmodi nach Bereitstellung).

- <a name="note8"></a>[Entscheidungsstrukturhinweis #8](#decision-tree): Mit [Ressourcenberechtigungen](../azure-monitor/logs/manage-access.md) oder [Ressourcenkontext](../azure-monitor/logs/design-logs-deployment.md) wird ermöglicht, dass Benutzer Protokolle nur für Ressourcen anzeigen können, auf die sie Zugriff haben. Der Zugriffsmodus für den Arbeitsbereich muss auf **Benutzerressource oder Arbeitsbereichsberechtigungen** festgelegt werden. Nur Tabellen, die für die Ressourcen relevant sind, für die der Benutzer über Berechtigungen verfügt, sind in den Suchergebnissen auf der Seite **Protokolle** in Microsoft Sentinel enthalten.

- <a name="note9"></a>[Entscheidungsstrukturhinweis #9](#decision-tree): [RBAC auf Tabellenebene](../azure-monitor/logs/manage-access.md) ermöglicht zusätzlich zu den anderen Berechtigungen eine präzisere Steuerung von Daten in einem Log Analytics-Arbeitsbereich. Mit diesem Steuerelement können Sie bestimmte Datentypen definieren, auf die nur eine bestimmte Gruppe von Benutzern Zugriff besitzt. Weitere Informationen finden Sie unter [RBAC auf Tabellenebene in Microsoft Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043).

## <a name="next-steps"></a>Nächste Schritte

Beispiele für diese Entscheidungsstruktur in der Praxis finden Sie unter [Beispielentwürfe von Microsoft Sentinel-Arbeitsbereichen](sample-workspace-designs.md).

Weitere Informationen finden Sie unter

- [Bewährte Methoden für die Microsoft Sentinel-Arbeitsbereichsarchitektur](best-practices-workspace-architecture.md)
- [Bewährte Methoden für Microsoft Sentinel](best-practices.md)
