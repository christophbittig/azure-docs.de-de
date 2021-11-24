---
title: Schützen des geistigen Eigentums von MSSPs (Managed Security Service Providers) in Microsoft Sentinel | Microsoft-Dokumentation
description: Hier erfahren Sie, wie MSSPs (Managed Security Service Providers) das von ihnen erstellte geistige Eigentum in Microsoft Sentinel schützen können.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.openlocfilehash: 11ee855d2606376ab893d581a091afeca3dd6713
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518867"
---
# <a name="protecting-mssp-intellectual-property-in-microsoft-sentinel"></a>Schützen des geistigen Eigentums von MSSPs in Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Artikel werden die Methoden beschrieben, mit denen MSSPs (Managed Security Service Providers) geistiges Eigentum schützen können, das sie in Microsoft Sentinel entwickelt haben. Beispiele hierfür wären etwa Microsoft Sentinel-Analyseregeln, Hunting-Abfragen, Playbooks und Arbeitsmappen.

Für welche Methode Sie sich entscheiden, hängt davon ab, wie Ihre Kunden Azure erwerben – also ob Sie als [Cloudlösungsanbieter (Cloud Solutions Provider, CSP)](#cloud-solutions-providers-csp) fungieren oder ob der Kunde über ein Konto mit [Enterprise Agreement (EA)/nutzungsbasierter Bezahlung (Pay-As-You-Go, PAYG)](#enterprise-agreements-ea--pay-as-you-go-payg) verfügt. Die einzelnen Methoden werden in den folgenden Abschnitten beschrieben.

## <a name="cloud-solutions-providers-csp"></a>Cloudlösungsanbieter (Cloud Solution Providers, CSPs)

Wenn Sie Azure als Cloudlösungsanbieter (Cloud Solutions Provider, CSP) weiterverkaufen, verwalten Sie das Azure-Abonnement des Kunden. Dank [Administrator im Namen von](/partner-center/azure-plan-manage) (Admin on Behalf Of, AOBO) erhalten Benutzer in der Gruppe „Administrator-Agents“ Ihres MSSP-Mandanten Besitzerzugriff auf das Azure-Abonnement des Kunden. Der Kunde hat standardmäßig keinen Zugriff.

Wenn andere Benutzer aus dem MSSP-Mandanten außerhalb der Gruppe „Administrator-Agents“ Zugriff auf die Kundenumgebung benötigen, empfiehlt sich die Verwendung von [Azure Lighthouse](multiple-tenants-service-providers.md). Mit Azure Lighthouse können Sie Benutzern oder Gruppen mithilfe einer der integrierten Rollen Zugriff auf einen bestimmten Bereich (beispielsweise eine Ressourcengruppe oder ein Abonnement) gewähren.

Wenn Sie Kundenbenutzern Zugriff auf die Azure-Umgebung gewähren müssen, empfiehlt es sich, den Zugriff nicht für das gesamte Abonnement, sondern nur auf *Ressourcengruppenebene* zu gewähren, damit Sie Teile der Umgebung nach Bedarf anzeigen/ausblenden können.

Beispiel:

- Sie können dem Kunden Zugriff auf mehrere Ressourcengruppen gewähren, in denen sich seine Anwendungen befinden, und den Microsoft Sentinel-Arbeitsbereich in einer separaten Ressourcengruppe platzieren, auf die der Kunde keinen Zugriff hat.

- Mit dieser Methode können Kunden bestimmte Arbeitsmappen und Playbooks anzeigen – also separate Ressourcen, die sich in ihrer eigenen Ressourcengruppe befinden.

Wenn Sie Kunden Zugriff auf Ressourcengruppenebene gewähren, können diese trotzdem auf Protokolldaten für die Ressourcen zugreifen, auf die sie Zugriff haben (also beispielsweise auf Protokolle eines virtuellen Computers) – auch ohne Zugriff auf Microsoft Sentinel. Weitere Informationen finden Sie unter [Verwalten des Zugriffs auf Microsoft Sentinel-Daten nach Ressource](resource-context-rbac.md).

> [!TIP]
> Falls Ihre Kunden Zugriff auf das gesamte Abonnement benötigen, sehen Sie sich die Informationen unter [Enterprise Agreements (EAs)/Nutzungsbasierte Bezahlung (Pay-As-You-Go, PAYG)](#enterprise-agreements-ea--pay-as-you-go-payg) an.
>

### <a name="sample-microsoft-sentinel-csp-architecture"></a>Microsoft Sentinel: CSP-Beispielarchitektur

Die folgende Abbildung zeigt, wie die im [vorherigen Abschnitt](#cloud-solutions-providers-csp) beschriebenen Berechtigungen bei der Zugriffsgewährung für CSP-Kunden verwendet werden können:

:::image type="content" source="media/mssp-protect-intellectual-property/csp-customers.png" alt-text="Schützen Ihres geistigen Eigentums in Microsoft Sentinel: CSP-Kunden":::

In dieser Abbildung:

- Die Benutzer mit **Besitzerzugriff** auf das CSP-Abonnement sind die Benutzer in der Gruppe „Administrator-Agents“ im Azure AD-Mandanten des MSSP.
- Anderen Gruppen des MSSP wird über Azure Lighthouse Zugriff auf die Kundenumgebung gewährt.
- Der Kundenzugriff auf Azure-Ressourcen wird per Azure RBAC auf Ressourcengruppenebene verwaltet.

    Dadurch können MSSPs Microsoft Sentinel-Komponenten wie Analyseregeln und Hunting-Abfragen nach Bedarf ausblenden.

Weitere Informationen finden Sie auch in der [Dokumentation zu Azure Lighthouse](../lighthouse/concepts/cloud-solution-provider.md).

## <a name="enterprise-agreements-ea--pay-as-you-go-payg"></a>Enterprise Agreements (EAs)/Nutzungsbasierte Bezahlung (Pay-As-You-Go, PAYG)

Wenn Ihr Kunde direkt bei Microsoft kauft, hat er bereits Vollzugriff auf die Azure-Umgebung, und Sie können nichts ausblenden, was sich im Azure-Abonnement des Kunden befindet.

Stattdessen können Sie Ihr in Microsoft Sentinel entwickeltes geistiges Eigentum wie folgt schützen (je nachdem, welche Art von Ressource Sie schützen möchten):

### <a name="analytics-rules-and-hunting-queries"></a>Analyseregeln und Hunting-Abfragen

Analyseregeln und Hunting-Abfragen sind in Microsoft Sentinel enthalten und können daher nicht vom Microsoft Sentinel-Arbeitsbereich getrennt werden.

Die Abfrage kann somit selbst von Benutzern mit Berechtigungen vom Typ „Microsoft Sentinel-Leser“ angezeigt werden. In diesem Fall empfiehlt es sich, Ihre Analyseregeln und Hunting-Abfragen nicht im Kundenmandanten, sondern in Ihrem eigenen MSSP-Mandanten zu hosten.

Hierzu benötigen Sie in Ihrem eigenen Mandanten einen Arbeitsbereich mit Microsoft Sentinel-Unterstützung, und der Kundenarbeitsbereich muss für Sie über [Azure Lighthouse](multiple-tenants-service-providers.md) sichtbar sein.

Um im MSSP-Mandanten eine Analyseregel oder Hunting-Abfrage mit Verweis auf Daten im Kundenmandanten zu erstellen, muss die Anweisung `workspace` wie folgt verwendet werden:

```kql
workspace('<customer-workspace>').SecurityEvent
| where EventID == ‘4625’
```

Berücksichtigen Sie Folgendes, wenn Sie Ihren Analyseregeln eine Anweisung vom Typ `workspace` hinzufügen:

- **Keine Warnungen im Kundenarbeitsbereich.** Durch auf diese Weise erstellte Regeln werden keine Warnungen oder Incidents im Kundenarbeitsbereich ausgelöst. Sowohl Warnungen als auch Incidents sind nur in Ihrem MSSP-Arbeitsbereich vorhanden.

- **Erstellen Sie separate Warnungen für jeden Kunden.** Bei Verwendung dieser Methode empfiehlt es sich auch, separate Warnungsregeln für jeden Kunden und jede Erkennung zu verwenden, da die Arbeitsbereichsanweisung von Fall zu Fall unterschiedlich ist.

    Zur problemlosen Identifizierung des Kunden, bei dem die Warnung ausgelöst wurde, können Sie den Kundennamen dem Namen der Warnungsregel hinzufügen. Separate Warnungen können zu einer großen Anzahl von Regeln führen, die Sie ggf. mithilfe von Skripts oder mit [Microsoft Sentinel als Code](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928) verwalten sollten.

    Beispiel:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-per-customer.png" alt-text="Erstellen separater Regeln in Ihrem MSSP-Arbeitsbereich für jeden Kunden":::

- **Erstellen Sie separate MSSP-Arbeitsbereiche für jeden Kunden.** Wenn Sie separate Regeln für jeden Kunden und jede Erkennung erstellen, kann es passieren, dass für Ihren Arbeitsbereich die maximal zulässige Anzahl von Analyseregeln (512) erreicht wird. Wenn Sie über viele Kunden verfügen und diesen Grenzwert wahrscheinlich erreichen werden, empfiehlt es sich, jeweils einen separaten MSSP-Arbeitsbereich für die einzelnen Kunden zu erstellen.

    Beispiel:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-and-workspace-per-customer.png" alt-text="Erstellen von Arbeitsbereichen und Regeln in Ihrem MSSP-Mandanten für jeden Kunden":::

> [!IMPORTANT]
> Der Schlüssel zur erfolgreichen Verwendung dieser Methode liegt in der Automatisierung der Verwaltung einer großen Gruppe von Regeln in Ihren Arbeitsbereichen.
>
> Weitere Informationen finden Sie unter [Neu: Arbeitsbereichsübergreifende Analyseregeln](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cross-workspace-analytics-rules/ba-p/1664211).
>

### <a name="workbooks"></a>Arbeitsmappen

Wenn Sie eine Microsoft Sentinel-Arbeitsmappe entwickelt haben, die von Ihrem Kunden nicht kopiert werden soll, hosten Sie die Arbeitsmappe in Ihrem MSSP-Mandanten. Stellen Sie sicher, dass Sie über Azure Lighthouse auf Ihre Kundenarbeitsbereiche zugreifen können, und ändern Sie dann die Arbeitsmappe so, dass diese Kundenarbeitsbereiche verwendet werden.

Beispiel:

:::image type="content" source="media/mssp-protect-intellectual-property/cross-workspace-workbook.png" alt-text="Arbeitsbereichsübergreifende Arbeitsmappen":::

Weitere Informationen finden Sie unter [Arbeitsbereichsübergreifende Arbeitsmappen](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks).

Wenn Sie dem Kunden das Anzeigen der Arbeitsmappenvisualisierungen ermöglichen und gleichzeitig den Code geheim halten möchten, empfiehlt es sich, die Arbeitsmappe zu Power BI zu exportieren.

Das Exportieren Ihrer Arbeitsmappe zu Power BI bewirkt Folgendes:

- **Es erleichtert die Freigabe von Arbeitsmappenvisualisierungen.** Sie können dem Kunden einen Link zum Power BI-Dashboard senden, auf dem er die gemeldeten Daten ganz ohne Azure-Zugriffsberechtigungen anzeigen kann.
- **Es ermöglicht die Verwendung von Zeitplänen.** Konfigurieren Sie Power BI so, dass in regelmäßigen Abständen E-Mails mit einer Momentaufnahme des Dashboards für die entsprechende Zeit gesendet werden.

Weitere Informationen finden Sie unter [Importieren von Azure Monitor-Protokolldaten in Power BI](../azure-monitor/logs/log-powerbi.md).

### <a name="playbooks"></a>Playbooks

Sie können Ihre Playbooks wie folgt schützen (je nachdem, wo die Analyseregeln zum Auslösen des Playbooks erstellt wurden):

- **Im MSSP-Arbeitsbereich erstellte Analyseregeln:**  Erstellen Sie Ihre Playbooks im MSSP-Mandanten, und achten Sie darauf, dass Sie alle Incident- und Warnungsdaten aus dem MSSP-Arbeitsbereich erhalten. Sie können die Playbooks anfügen, wenn Sie eine neue Regel in Ihrem Arbeitsbereich erstellen.

    Beispiel:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-mssp-workspace.png" alt-text="Im MSSP-Arbeitsbereich erstellte Regeln":::

- **Im Kundenarbeitsbereich erstellte Analyseregeln:** Verwenden Sie Azure Lighthouse, um Analyseregeln aus dem Arbeitsbereich des Kunden an ein in Ihrem MSSP-Arbeitsbereich gehostetes Playbook anzufügen. In diesem Fall werden durch das Playbook die Warnungs- und Incidentdaten sowie alle anderen Kundeninformationen aus dem Kundenarbeitsbereich abgerufen.

    Beispiel:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-customer-workspace.png" alt-text="Im Kundenarbeitsbereich erstellte Regeln":::

In beiden Fällen muss ein Benutzer- oder Dienstprinzipal mit entsprechendem Zugriff über Lighthouse verwendet werden, wenn das Playbook auf die Azure-Umgebung des Kunden zugreifen muss.

Benötigt das Playbook Zugriff auf Azure-fremde Ressourcen im Mandanten des Kunden (also beispielsweise auf Azure AD, Office 365 oder Microsoft 365 Defender), muss ein Dienstprinzipal mit den entsprechenden Berechtigungen im Kundenmandanten erstellt und diese Identität dann dem Playbook hinzugefügt werden.

> [!NOTE]
> Wenn Sie Ihre Playbooks in Kombination mit Automatisierungsregeln verwenden, müssen die Automatisierungsregelberechtigungen für die Ressourcengruppe festgelegt werden, in der sich die Playbooks befinden.
> Weitere Informationen finden Sie unter [Berechtigungen für Automatisierungsregeln zum Ausführen von Playbooks](automate-incident-handling-with-automation-rules.md#permissions-for-automation-rules-to-run-playbooks).
>

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie unter

- [Microsoft Sentinel: Technisches Playbook für MSSPs](https://cloudpartners.transform.microsoft.com/download?assetname=assets/Azure-Sentinel-Technical-Playbook-for-MSSPs.pdf&download=1)
- [Verwalten mehrerer Mandanten in Microsoft Sentinel als MSSP](multiple-tenants-service-providers.md)
- [Erweitern von Microsoft Sentinel auf Arbeitsbereiche und Mandanten](extend-sentinel-across-workspaces-tenants.md)
- [Visualisieren und Überwachen Ihrer Daten](monitor-your-data.md)
- [Tutorial: Einrichten automatisierter Reaktionen auf Bedrohungen in Microsoft Sentinel](tutorial-respond-threats-playbook.md)
