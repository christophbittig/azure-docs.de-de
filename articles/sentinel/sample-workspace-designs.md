---
title: Beispiele für Microsoft Sentinel-Arbeitsbereichsentwürfe | Microsoft-Dokumentation
description: Hier finden Sie Beispiele für Microsoft Sentinel-Architekturentwürfe mit mehreren Mandanten, Clouds oder Regionen.
services: sentinel
author: batamig
ms.author: bagol
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: conceptual
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: cb2332a4c9854aafe1ef8193da9ca76e9aaaed95
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132520064"
---
# <a name="microsoft-sentinel-sample-workspace-designs"></a>Microsoft Sentinel-Beispielarbeitsbereichsentwürfe

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Artikel werden empfohlene Arbeitsbereichsentwürfe für Organisationen mit den folgenden Beispielanforderungen beschrieben:

- Mehrere Mandanten und Regionen, mit europäischen Anforderungen an die Datenhoheit
- Einzelner Mandant mit mehreren Clouds
- Mehrere Mandanten mit mehreren Regionen und zentralisierter Sicherheit

In den Beispielen in diesem Artikel wird die [Entscheidungsstruktur für](design-your-workspace-architecture.md) den Entwurf des Microsoft Sentinel-Arbeitsbereichs verwendet, um den besten Arbeitsbereichsentwurf für jede Organisation zu bestimmen. Weitere Informationen finden Sie unter Bewährte Methoden für die [Microsoft Sentinel-Arbeitsbereichsarchitektur.](best-practices-workspace-architecture.md)

## <a name="sample-1-multiple-tenants-and-regions"></a>Beispiel 1: Mehrere Mandanten und Regionen

Die Contoso Corporation ist ein multinationales Unternehmen mit Hauptsitz in London. Contoso verfügt über Niederlassungen auf der ganzen Welt mit besonders wichtigen Standorten in New York City und Tokio. Vor Kurzem hat Contoso seine Productivity Suite zu Office 365 migriert, wobei viele Workloads zu Azure migriert wurden.

### <a name="contoso-tenants"></a>Contoso-Mandanten

Aufgrund einer Übernahme vor einigen Jahren verfügt Contoso über zwei Azure AD-Mandanten: `contoso.onmicrosoft.com` und `wingtip.onmicrosoft.com`. Jeder Mandant verfügt über eine eigene Office 365-Instanz und mehrere Azure-Abonnements, wie in der folgenden Abbildung dargestellt:

:::image type="content" source="media/best-practices/contoso-tenants.png" alt-text="Diagramm der Contoso-Mandanten, die jeweils über separate Abonnementsätze verfügen." border="false":::

### <a name="contoso-compliance-and-regional-deployment"></a>Compliance und regionale Bereitstellung bei Contoso

Contoso verfügt derzeit über Azure-Ressourcen, die in drei verschiedenen Regionen gehostet werden: „USA, Osten“, „EU, Norden“ und „Japan, Westen“. Darüber hinaus besteht die strikte Anforderung, alle in Europa generierten Daten innerhalb der Regionen „Europa“ zu behalten.

Beide Azure AD-Mandanten von Contoso verfügen über Ressourcen in allen drei Regionen: „USA, Osten“, „EU, Norden“ und „Japan, Westen“.

### <a name="contoso-resource-types-and-collection-requirements"></a>Ressourcentypen und Sammlungsanforderungen von Contoso

Contoso muss Ereignisse aus den folgenden Datenquellen sammeln:

-   Office 365
-   Anmeldungs- und Überwachungsprotokolle von Azure AD
-   Azure-Aktivität
-   Windows-Sicherheitsereignisse, sowohl aus lokalen als auch aus Azure-VM-Quellen
-   Syslog, sowohl aus lokalen als auch aus Azure-VM-Quellen
-   CEF, von mehreren lokalen Netzwerkgeräten wie Palo Alto, Cisco ASA und Cisco Meraki
-   Mehrere Azure-PaaS-Ressourcen, z. B. Azure Firewall, AKS, Key Vault, Azure Storage und Azure SQL
-   Cisco Umbrella

Die Azure-VMs befinden sich größtenteils in der Region „EU, Norden“, nur wenige sind in den Regionen „USA, Osten“ und „Japan, Westen“. Contoso verwendet Microsoft Defender für Server auf allen Azure-VMs.

Contoso geht davon aus, dass etwa 300 GB pro Tag aus allen Datenquellen erfasst werden.

### <a name="contoso-access-requirements"></a>Zugriffsanforderungen bei Contoso

Die Azure-Umgebung von Contoso verfügt bereits über einen einzigen Log Analytics-Arbeitsbereich, der vom Operations-Team zum Überwachen der Infrastruktur verwendet wird. Dieser Arbeitsbereich befindet sich im Contoso-AAD-Mandanten in der Region „EU, Norden“ und wird zum Sammeln von Protokollen von Azure-VMs in allen Regionen verwendet. Derzeit werden ca. 50 GB pro Tag erfasst.

Das Operations-Team von Contoso benötigt Zugriff auf alle Protokolle, die sich derzeit im Arbeitsbereich befinden. Dies umfasst auch mehrere Datentypen, die nicht vom SOC benötigt werden, z. B. **Perf**, **InsightsMetrics**, **ContainerLog** und mehr. Das Betriebsteam darf *keinen Zugriff* auf die neuen Protokolle haben, die in Microsoft Sentinel gesammelt werden.

### <a name="contosos-solution"></a>Die Lösung von Contoso

Mit den folgenden Schritten wird die [Entscheidungsstruktur für](design-your-workspace-architecture.md) den Entwurf des Microsoft Sentinel-Arbeitsbereichs angewendet, um den besten Arbeitsbereichsentwurf für Contoso zu ermitteln:

1. Contoso verfügt bereits über einen Arbeitsbereich, sodass wir die Aktivierung von Microsoft Sentinel in demselben Arbeitsbereich prüfen können.

    Die Erfassung von Nicht-SOC-Daten liegt bei weniger als 100 GB/Tag, daher können wir mit [Schritt 2](design-your-workspace-architecture.md#step-2-keeping-data-in-different-azure-geographies) fortfahren und sicherstellen, dass die entsprechende Option in [Schritt 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data) ausgewählt wird.

1.  Contoso hat gesetzliche Anforderungen, daher benötigen wir mindestens einen Microsoft Sentinel-Arbeitsbereich in Europa.

1.  Contoso verfügt über zwei verschiedene Azure AD-Mandanten und sammelt Daten aus Datenquellen auf Mandantenebene, z. B. Office 365 sowie Azure AD-Anmelde- und Überwachungsprotokolle. Daher benötigen wir mindestens einen Arbeitsbereich pro Mandant.

1.  Contoso benötigt keine [verbrauchsbasierte Kostenzuteilung](design-your-workspace-architecture.md#step-4-splitting-billing--charge-back), deshalb können wir mit [Schritt 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data) fortfahren.

1.  Contoso muss Nicht-SOC-Daten sammeln, aber es gibt keine Überlappung zwischen SOC- und Nicht-SOC-Daten. Die SOC-Daten umfassen darüber hinaus ca. 250 GB/Tag, sodass aus Kostengründen separate Arbeitsbereiche verwendet werden sollten.

1.  Die meisten VMs von Contoso befinden sich in der Region „Europa, Norden“. Dort verfügt Contoso bereits über einen Arbeitsbereich. Daher sind die Bandbreitenkosten in diesem Fall kein Problem.

1.  Contoso verfügt über ein einzelnes SOC-Team, das Microsoft Sentinel verwenden wird, sodass keine zusätzliche Trennung erforderlich ist.

1.  Alle Mitglieder des SOC-Teams von Contoso haben Zugriff auf alle Daten, sodass keine zusätzliche Trennung erforderlich ist.

Das resultierende Design des Microsoft Sentinel-Arbeitsbereichs für Contoso ist in der folgenden Abbildung dargestellt:

:::image type="content" source="media/best-practices/contoso-solution.png" alt-text="Diagramm der Contoso-Lösung mit einem separaten Arbeitsbereich für das Operations-Team" border="false":::

Die vorgeschlagene Lösung umfasst Folgendes:

- Einen separaten Log Analytics-Arbeitsbereich für das Operations-Team von Contoso. Dieser Arbeitsbereich enthält ausschließlich Daten, die von dem SOC-Team von Contoso nicht benötigt werden, z. B. die **Perf**-, **InsightsMetrics**- oder **ContainerLog**-Tabellen.

- Zwei Microsoft Sentinel-Arbeitsbereiche, jeweils Azure AD Mandanten, zum Erfassung von Daten aus Office 365, Azure-Aktivität, Azure AD und allen Azure PaaS-Diensten.

- Alle anderen Daten, die aus lokalen Datenquellen stammen, können an einen der beiden Microsoft Sentinel-Arbeitsbereiche geroutet werden.


## <a name="sample-2-single-tenant-with-multiple-clouds"></a>Beispiel 2: Einzelner Mandant mit mehreren Clouds

Fabrikam ist eine Organisation mit Hauptsitz in New York City und mehreren Niederlassungen in den USA. Fabrikam steht noch am Anfang der Cloudeinführung. Die Organisation muss im nächsten Schritt ihre erste Azure-Zielzone bereitstellen und die ersten Workloads migrieren. Fabrikam verfügt bereits über einige Workloads in AWS, die mit Microsoft Sentinel überwacht werden sollen.

### <a name="fabrikam-tenancy-requirements"></a>Mandantenanforderungen von Fabrikam

Fabrikam verfügt über einen einzelnen Azure AD-Mandanten.

### <a name="fabrikam-compliance-and-regional-deployment"></a>Compliance und regionale Bereitstellung bei Fabrikam

Es gibt keine besonderen Compliance-Anforderungen bei Fabrikam. Fabrikam verfügt über Ressourcen in mehreren Azure-Regionen in den USA, aber die regionsübergreifenden Bandbreitenkosten stellen kein großes Problem dar.

### <a name="fabrikam-resource-types-and-collection-requirements"></a>Ressourcentypen und Sammlungsanforderungen von Fabrikam

Fabrikam muss Ereignisse aus den folgenden Datenquellen sammeln:

-   Anmeldungs- und Überwachungsprotokolle von Azure AD
-   Azure-Aktivität
-   Sicherheitsereignisse, sowohl aus lokalen als auch aus Azure-VM-Quellen
-   Windows-Ereignisse, sowohl aus lokalen als auch aus Azure-VM-Quellen
-   Leistungsdaten, sowohl aus lokalen als auch aus Azure-VM-Quellen
-   AWS CloudTrail
-   AKS-Überwachungs- und Leistungsprotokolle

### <a name="fabrikam-access-requirements"></a>Zugriffsanforderungen von Fabrikam

Das Operations-Team von Fabrikam muss auf Folgendes zugreifen:

-   Sicherheitsereignisse und Windows-Ereignisse, sowohl aus lokalen als auch aus Azure-VM-Quellen
-   Leistungsdaten, sowohl aus lokalen als auch aus Azure-VM-Quellen
-   AKS-Leistungs- (Container Insights) und Überwachungsprotokolle
-   Alle Azure-Aktivitätsdaten

Das SOC-Team von Fabrikam muss auf Folgendes zugreifen:
-   Anmeldungs- und Überwachungsprotokolle von Azure AD
-   Alle Azure-Aktivitätsdaten
-   Sicherheitsereignisse, sowohl aus lokalen als auch aus Azure-VM-Quellen
-   AWS CloudTrail-Protokolle
-   AKS-Überwachungsprotokolle
-   Das vollständige Microsoft Sentinel-Portal

### <a name="fabrikams-solution"></a>Die Lösung von Fabrikam

Mit den folgenden Schritten wird die [Entscheidungsstruktur für](design-your-workspace-architecture.md) den Entwurf des Microsoft Sentinel-Arbeitsbereichs angewendet, um den besten Arbeitsbereichsentwurf für Fabrikam zu ermitteln:

1.  Fabrikam besitzt noch keinen Arbeitsbereich, also fahren wir mit [Schritt 2](design-your-workspace-architecture.md#step-2-keeping-data-in-different-azure-geographies) fort.

1.  Fabrikam muss keine bestimmten gesetzlichen Anforderungen erfüllen. Fahren wir daher mit [Schritt 3](design-your-workspace-architecture.md#step-3-do-you-have-multiple-azure-tenants) fort.

1.  Fabrikam verfügt über eine Umgebung mit nur einem Mandanten. Fahren wir mit [Schritt 4](design-your-workspace-architecture.md#step-4-splitting-billing--charge-back) fort.

1.  Fabrikam muss die Gebühren nicht aufteilen. Fahren wir daher mit [Schritt 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data) fort.

1.  Fabrikam benötigt separate Arbeitsbereiche für das SOC- und das Operations-Team:

    Das Operations-Team von Fabrikam muss Leistungsdaten sowohl von VMs als auch von AKS sammeln. Da AKS auf Diagnoseeinstellungen basiert, kann das Team bestimmte Protokolle auswählen, die an bestimmte Arbeitsbereiche gesendet werden sollen. Fabrikam kann AKS-Überwachungsprotokolle an den Microsoft Sentinel-Arbeitsbereich und alle AKS-Protokolle an einen separaten Arbeitsbereich senden, in dem Microsoft Sentinel nicht aktiviert ist. In dem Arbeitsbereich, in dem Microsoft Sentinel nicht aktiviert ist, aktiviert Fabrikam die Container Insights Lösung.

    Für Windows-VMs kann Fabrikam den [Azure Monitoring Agent (AMA)](connect-windows-security-events.md#connector-options) verwenden, um die Protokolle zu teilen, Sicherheitsereignisse an den Microsoft Sentinel-Arbeitsbereich zu senden und Leistungs- und Windows-Ereignisse an den Arbeitsbereich ohne Microsoft Sentinel zu senden.

    Fabrikam wählt aus, die überlappenden Daten wie Sicherheitsereignisse und Azure-Aktivitätsereignisse nur als SOC-Daten zu betrachten, und sendet diese Daten mit Microsoft Sentinel an den Arbeitsbereich.

1.  Die Bandbreitenkosten stellen kein Problem für Fabrikam dar, deshalb fahren wir mit [Schritt 7](design-your-workspace-architecture.md#step-7-segregating-data-or-defining-boundaries-by-ownership) fort.

1.  Fabrikam hat sich bereits dazu entschieden, separate Arbeitsbereiche für das SOC- und das Operations-Team zu verwenden. Eine weitere Trennung ist nicht erforderlich.

1.  Fabrikam muss den Zugriff auf überlappende Daten, z. B. Sicherheitsereignisse und Azure-Aktivitätsereignisse, steuern können. Es gibt jedoch keine gesonderten Anforderungen auf Zeilenebene.

    Weder bei Sicherheitsereignissen noch bei Azure-Aktivitätsereignissen handelt es sich um benutzerdefinierte Protokolle, sodass Fabrikam RBAC auf Tabellenebene verwenden kann, um dem Operations-Team Zugriff auf diese beiden Tabellen zu gewähren.

Das resultierende Design des Microsoft Sentinel-Arbeitsbereichs für Fabrikam ist in der folgenden Abbildung dargestellt, die der Einfachheit halber nur wichtige Protokollquellen enthält:

:::image type="content" source="media/best-practices/fabrikam-solution.png" alt-text="Diagramm der Fabrikam-Lösung mit einem separaten Arbeitsbereich für das Operations-Team" border="false" :::

Die vorgeschlagene Lösung umfasst Folgendes:

- Zwei separate Arbeitsbereiche in der Region "USA": einer für das SOC-Team mit aktivierter Microsoft Sentinel und ein weiterer für das Betriebsteam ohne Microsoft Sentinel.

- Den [Azure Monitoring Agent (AMA)](connect-windows-security-events.md#connector-options), der verwendet wird, um zu bestimmen, welche Protokolle von Azure und den lokalen VMs an den jeweiligen Arbeitsbereich gesendet werden.

- Diagnoseeinstellungen, mit denen bestimmt wird, welche Protokolle von Azure-Ressourcen wie AKS an die einzelnen Arbeitsbereiche gesendet werden.

- Überlappende Daten, die an den Microsoft Sentinel-Arbeitsbereich gesendet werden, mit RBAC auf Tabellenebene, um dem Betriebsteam bei Bedarf Zugriff zu gewähren.

## <a name="sample-3-multiple-tenants-and-regions-and-centralized-security"></a>Beispiel 3: Mehrere Mandanten und Regionen und zentralisierte Sicherheit

Adventure Works ist ein multinationales Unternehmen mit Hauptsitz in Tokio. Adventure Works verfügt über 10 verschiedene untergeordnete Entitäten, die sich in verschiedenen Ländern auf der ganzen Welt befinden.

Adventure Works ist Kunde von Microsoft 365 E5 und verfügt bereits über Workloads in Azure.

### <a name="adventure-works-tenancy-requirements"></a>Mandantenanforderungen von Adventure Works

Adventure Works verfügt über drei Azure AD-Mandanten, einen für jeden Kontinent, auf dem sich die untergeordneten Entitäten befinden: Asien, Europa und Afrika. Die Identitäten der verschiedenen untergeordneten Entitäten befinden sich im jeweiligen Mandanten des Kontinents, zu dem sie gehören. Japanische Benutzer befinden sich z. B. im Mandanten *Asien*, deutsche Benutzer im Mandanten *Europa* und ägyptische Benutzer im Mandanten *Afrika*.

### <a name="adventure-works-compliance-and-regional-requirements"></a>Compliance und regionale Anforderungen von Adventure Works

Adventure Works verwendet derzeit drei Azure-Regionen, die jeweils auf den Kontinent ausgerichtet sind, in dem sich die untergeordneten Entitäten befinden. Adventure Works muss keine strengen Compliance-Anforderungen einhalten.

### <a name="adventure-works-resource-types-and-collection-requirements"></a>Ressourcentypen und Sammlungsanforderungen von Adventure Works

Adventure Works muss die folgenden Datenquellen für jede untergeordnete Entität sammeln:

-   Anmeldungs- und Überwachungsprotokolle von Azure AD
-   Office 365-Protokolle
-   Microsoft 365 Defender for Endpoint-Rohprotokolle
-   Azure-Aktivität
-   Microsoft Defender für Cloud
-   Azure PaaS-Ressourcen, z. B. aus Azure Firewall, Azure Storage, Azure SQL und Azure WAF
-   Sicherheits- und Windows-Ereignisse von Azure-VMs
-   CEF-Protokolle von lokalen Netzwerkgeräten

Die Azure-VMs sind zwar auf die drei Kontinente verteilt, aber Bandbreitenkosten stellen kein Problem dar.

### <a name="adventure-works-access-requirements"></a>Zugriffsanforderungen von Adventure Works

Adventure Works verfügt über ein zentralisiertes SOC-Team, das die Sicherheitsvorgänge aller untergeordneten Entitäten überwacht.

Adventure Works verfügt darüber hinaus über drei unabhängige SOC-Teams, eines für jeden Kontinent. Die SOC-Teams der Kontinente dürfen nur auf die Daten zugreifen, die in der jeweiligen Region generiert wurden, ohne dabei die Daten von anderen Kontinenten sehen zu können. Das SOC-Team aus Asien darf beispielsweise nur auf Daten von Azure-Ressourcen, die in Asien bereitgestellt wurden, auf AAD-Anmeldungen vom Asien-Mandanten und auf Defender for Endpoint-Protokolle des Asien-Mandanten zugreifen.

Das SOC-Team jedes Kontinents muss auf das gesamte Microsoft Sentinel-Portal zugreifen.

Das Operations-Team von Adventure Works arbeitet unabhängig und verfügt über eigene Arbeitsbereiche ohne Microsoft Sentinel.

### <a name="adventure-works-solution"></a>Die Lösung von Adventure Works

Mit den folgenden Schritten wird die [Entscheidungsstruktur für](design-your-workspace-architecture.md) den Entwurf des Microsoft Sentinel-Arbeitsbereichs angewendet, um den besten Arbeitsbereichsentwurf für Adventure Works zu ermitteln:

1.  Das Operations-Team von Adventure Works verfügt bereits über eigene Arbeitsbereiche, fahren wir also mit [Schritt 2](design-your-workspace-architecture.md#step-2-keeping-data-in-different-azure-geographies) fort.

1.  Adventure Works muss keine bestimmten gesetzlichen Anforderungen erfüllen. Fahren wir daher mit [Schritt 3](design-your-workspace-architecture.md#step-3-do-you-have-multiple-azure-tenants) fort.

1.  Adventure Works verfügt über drei Azure AD-Mandanten und muss Datenquellen auf Mandantenebene erfassen, z. B. Office 365-Protokolle. Daher sollte Adventure Works mindestens einen Microsoft Sentinel-Arbeitsbereich für jeden Mandanten erstellen.

1.  Adventure Works muss die Gebühren nicht aufteilen. Fahren wir daher mit [Schritt 5](design-your-workspace-architecture.md#step-5-collecting-any-non-soc-data) fort.

1.  Da das Operations-Team von Adventure Works über eigene Arbeitsbereiche verfügt, werden alle in diesem Beispiel berücksichtigten Daten vom SOC-Team von Adventure Works verwendet.

1.  Die Bandbreitenkosten stellen kein Problem für Adventure Works dar, deshalb fahren wir mit [Schritt 7](design-your-workspace-architecture.md#step-7-segregating-data-or-defining-boundaries-by-ownership) fort.

1.  Adventure Works muss Daten nach Besitzer trennen, da das SOC-Team eines bestimmten Kontinents nur auf die Daten zugreifen darf, die für diesen Kontinent relevant sind. Das SOC-Team jedes Kontinents benötigt jedoch auch Zugriff auf das vollständige Microsoft Sentinel-Portal.

1.  Adventure Works muss den Datenzugriff nicht nach Tabellen steuern.

Das resultierende Design des Microsoft Sentinel-Arbeitsbereichs für Adventure Works ist in der folgenden Abbildung dargestellt, die der Einfachheit halber nur wichtige Protokollquellen enthält:

:::image type="content" source="media/best-practices/adventure-works-solution.png" alt-text="Diagramm der Adventure Works-Lösung mit separaten Arbeitsbereichen für jeden Azure AD-Mandanten" border="false":::

Die vorgeschlagene Lösung umfasst Folgendes:

- Ein separater Microsoft Sentinel-Arbeitsbereich für jeden Azure AD Mandanten. Jeder Arbeitsbereich sammelt Daten im Zusammenhang mit seinem Mandanten für alle Datenquellen.

- Die SOC-Teams der Kontinente haben ausschließlich Zugriff auf den Arbeitsbereich ihrer eigenen jeweiligen Mandanten. So wird sichergestellt, dass jedes SOC-Team nur auf Protokolle zugreifen kann, die innerhalb der Mandantengrenze generiert wurden.

- Das zentrale SOC-Team kann mithilfe von Azure Lighthouse weiterhin über einen separaten Azure AD-Mandanten auf jede der verschiedenen Microsoft Sentinel-Umgebungen zugreifen. Auch wenn kein zusätzlicher Mandant verfügbar ist, kann das zentrale SOC-Team über Azure Lighthouse auf die Remote-Arbeitsbereiche zugreifen.

- Darüber hinaus kann das zentrale SOC-Team auch einen zusätzlichen Arbeitsbereich erstellen, in dem Artefakte gespeichert werden können, die vor den SOC-Teams der Kontinente verborgen bleiben sollen, oder in dem Daten aufgenommen werden, die für die SOC-Teams der Kontinente nicht relevant sind.



## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
>[On-board Microsoft Sentinel](quickstart-onboard.md)

> [!div class="nextstepaction"]
>[Einblick in Warnungen](get-visibility.md)
