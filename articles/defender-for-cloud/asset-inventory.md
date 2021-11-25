---
title: Ressourcenbestand von Microsoft Defender für Cloud
description: Erfahren Sie mehr über die Ressourcenverwaltungsfunktionen von Microsoft Defender für Cloud, die vollständige Einblicke in alle von Defender für Cloud überwachten Ressourcen bieten.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 11/09/2021
ms.service: defender-for-cloud
ms.topic: how-to
ms.openlocfilehash: b8732094b7b575faf97753b8e0e8a5c234f733d2
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526157"
---
# <a name="use-asset-inventory-to-manage-your-resources-security-posture"></a>Verwenden Sie die Bestandsaufnahme zur Verwaltung der Sicherheitslage Ihrer Ressourcen

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Der Ressourcenbestand von Microsoft Defender für Cloud bietet eine einzelne, zentrale Ansicht des Sicherheitsstatus der Ressourcen, die Sie mit Microsoft Defender für Cloud verknüpft haben.

Defender für Cloud analysiert regelmäßig den Sicherheitsstatus von Ressourcen, die mit Ihren Abonnements verbunden sind, um potenzielle Sicherheitsrisiken zu identifizieren. Anschließend erhalten Sie Empfehlungen dazu, wie Sie diese Sicherheitsrisiken beheben können.

Wenn eine Ressource ausstehende Empfehlungen hat, werden diese im Inventar angezeigt.

Verwenden Sie diese Ansicht und ihre Filter zur Beantwortung folgender Fragen:

- Für welche meiner Abonnements mit aktivierten erweiterten Sicherheitsfeatures gibt es ausstehende Empfehlungen?
- Auf welchen meiner Computer mit dem Tag „Produktion“ fehlt der Log Analytics-Agent?
- Für wie viele meiner Computer, die mit einem bestimmten Tag gekennzeichnet sind, stehen Empfehlungen aus?
- Welche Rechner in einer bestimmten Ressourcengruppe haben eine bekannte Sicherheitslücke (unter Verwendung einer CVE-Nummer)?

Die Ressourcenverwaltungsfunktionen dieses Tools sind umfassend und wachsen weiter.

> [!TIP]
> Die Sicherheitsempfehlungen auf der Ressourcenbestandsseite sind identisch mit denen auf der Seite **Empfehlungen**, aber hier werden sie nach betroffener Ressource gefiltert. Informationen zur Anwendung von Empfehlungen finden Sie unter [Implementieren von Sicherheitsempfehlungen in Microsoft Defender für Cloud](review-security-recommendations.md).

## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemeine Verfügbarkeit (General Availability, GA)|
|Preise:|Kostenlos *<br>* Einige Funktionen der Bestandsseite, wie z. B. [Softwareinventur](#access-a-software-inventory), erfordern kostenpflichtige Lösungen.|
|Erforderliche Rollen und Berechtigungen:|Alle Benutzer|
|Clouds:|:::image type="icon" source="./media/icons/yes-icon.png"::: Kommerzielle Clouds<br>:::image type="icon" source="./media/icons/yes-icon.png"::: National (Azure Government, Azure China 21Vianet)|
|||

## <a name="what-are-the-key-features-of-asset-inventory"></a>Welche sind die wichtigsten Features des Ressourcenbestands?

Auf der Bestandsseite werden die folgenden Tools angezeigt:

:::image type="content" source="media/asset-inventory/highlights-of-inventory.png" alt-text="Hauptfeatures der Ressourcenbestandsseite in Microsoft Defender für Cloud." lightbox="media/asset-inventory/highlights-of-inventory.png":::

### <a name="1---summaries"></a>1: Zusammenfassungen

Bevor Sie Filter definieren, wird oben in der Bestandsansicht ein auffälliger Streifen mit Werten angezeigt:

- **Ressourcen gesamt**: Die Gesamtanzahl von Ressourcen, die mit Defender für Cloud verknüpft sind.
- **Fehlerhafte Ressourcen:** Ressourcen mit aktiven Sicherheitsempfehlungen. [Weitere Informationen zu Sicherheitsempfehlungen](review-security-recommendations.md).
- **Nicht überwachte Ressourcen**: Ressourcen mit Agent-Überwachungsproblemen – Der Log Analytics-Agent wurde bereitgestellt, aber der Agent sendet keine Daten oder hat andere Integritätsprobleme.
- **Nicht registrierte Abonnements**: Alle Abonnements im ausgewählten Bereich, die noch nicht mit Microsoft Defender für Cloud verknüpft wurden.

### <a name="2---filters"></a>2: Filter

Die zahlreichen Filter am oberen Rand der Seite bieten eine Möglichkeit, die Liste der Ressourcen schnell entsprechend der Frage zu verfeinern, die Sie zu beantworten versuchen. Wenn Sie beispielsweise die Frage *Auf welchen meiner Computer mit dem Tag „Produktion“ fehlt der Log Analytics-Agent?* beantworten möchten, könnten Sie den Filter **Agent-Überwachung** mit dem Filter **Tags** kombinieren.

Sobald Sie Filter angewendet haben, werden die Zusammenfassungswerte aktualisiert und an die Abfrageergebnisse angepasst.

### <a name="3---export-and-asset-management-tools"></a>3: Export- und Ressourcenverwaltungstools

**Exportoptionen:** Der Bestand bietet die Möglichkeit, die Ergebnisse der ausgewählten Filteroptionen in eine CSV-Datei zu exportieren. Darüber hinaus können Sie die Abfrage selbst in den Azure Resource Graph-Tester exportieren, um die KQL-Abfrage (Kusto Query Language KQL) weiter zu verfeinern, zu speichern oder zu bearbeiten.

> [!TIP]
> Die KQL-Dokumentation bietet eine Datenbank mit einigen Beispieldaten zusammen mit einigen einfachen Abfragen, um ein „Gefühl“ für die Sprache zu bekommen. [Weitere Informationen finden Sie in diesem KQL-Tutorial](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).

**Ressourcenverwaltungsoptionen**: Wenn Sie die Ressourcen gefunden haben, die Ihren Abfragen entsprechen, bietet der Ressourcenbestand Verknüpfungen für Vorgänge wie z. B.:

- Zuweisen von Tags zu den gefilterten Ressourcen: Aktivieren Sie die Kontrollkästchen neben den Ressourcen, die Sie mit einem Tag versehen möchten.
- Durchführen des Onboardings für neue Server in Defender für Cloud: Verwenden Sie die Symbolleistenschaltfläche **Neue Azure-Server hinzufügen**.
- Automatisieren von Workloads mit Azure Logic Apps: Verwenden Sie die Schaltfläche **Logik-App auslösen**, um eine Logik-App für eine oder mehrere Ressourcen auszuführen. Ihre Logik-Apps müssen zuvor vorbereitet werden und den entsprechenden Triggertyp (HTTP-Anforderung) akzeptieren. [Erfahren Sie mehr über Logik-Apps](../logic-apps/logic-apps-overview.md).

## <a name="how-does-asset-inventory-work"></a>Wie funktioniert der Ressourcenbestand?

Der Ressourcenbestand verwendet [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml), einen Azure-Dienst, der die Möglichkeit bietet, die Daten zum Sicherheitsstatus von Defender für Cloud über mehrere Abonnements hinweg abzufragen.

ARG ist so konzipiert, dass es eine effiziente Ressourcenerkundung mit der Fähigkeit zur bedarfsorientierten Abfrage ermöglicht.

Mithilfe der [Kusto-Abfragesprache](/azure/data-explorer/kusto/query/) (Kusto Query Language, KQL) kann der Ressourcenbestand durch Querverweise zwischen Defender für Cloud-Daten und anderen Ressourceneigenschaften schnell detaillierte Einblicke verschaffen.

## <a name="how-to-use-asset-inventory"></a>Verwenden des Ressourcenbestands

1. Wählen Sie auf der Seitenleiste von Defender für Cloud **Bestand** aus.

1. Verwenden Sie das Feld **Nach Name filtern**, um eine bestimmte Ressource anzuzeigen, oder verwenden Sie die Filter gemäß der nachstehenden Beschreibung.

1. Wählen Sie die entsprechenden Optionen in den Filtern aus, um die spezifische Abfrage zu erstellen, die Sie ausführen möchten.

    Standardmäßig werden die Ressourcen nach der Anzahl aktiver Sicherheitsempfehlungen sortiert.

    > [!IMPORTANT]
    > Die Optionen in jedem Filter sind spezifisch für die Ressourcen in den aktuell ausgewählten Abonnements **und** Ihre Auswahl in den anderen Filtern.
    >
    > Wenn Sie z. B. nur ein Abonnement ausgewählt haben und das Abonnement keine Ressourcen mit ausstehenden Sicherheitsempfehlungen aufweist, die zu beheben sind (0 fehlerhafte Ressourcen), enthält der Filter **Empfehlungen** keine Optionen.

    :::image type="content" source="./media/asset-inventory/filtering-to-prod-unmonitored.gif" alt-text="Verwenden der Filteroptionen im Ressourcenbestand von Microsoft Defender für Cloud zum Filtern von Ressourcen nach Produktionsressourcen, die nicht überwacht werden":::

1. Um den Filter **Sicherheitsergebnisse enthalten** zu verwenden, geben Sie freien Text aus der ID, der Sicherheitsprüfung oder dem CVE-Namen eines Sicherheitsergebnisses ein, um nach den betroffenen Ressourcen zu filtern:

    ![Filter „Sicherheitsergebnisse enthalten“](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > Die Filter **Sicherheitsergebnisse enthalten** und **Tags** akzeptieren nur einen einzigen Wert. Um nach mehreren Werten zu filtern, verwenden Sie **Filter hinzufügen**.

1. Wählen Sie mindestens eine Option („Aus“, „Ein“ oder „Partiell“) aus, um den Filter **Defender für Cloud** zu verwenden:

    - **Aus**: Ressourcen, die nicht durch einen Microsoft Defender-Plan geschützt sind. Sie können mit der rechten Maustaste auf eine dieser Ressourcen klicken und ein Upgrade ausführen:

        :::image type="content" source="./media/asset-inventory/upgrade-resource-inventory.png" alt-text="Führen Sie über die rechte Maustaste ein Upgrade für eine Ressource durch, die durch den relevanten Microsoft Defender-Plan geschützt werden soll." lightbox="./media/asset-inventory/upgrade-resource-inventory.png":::

    - **Ein**: Ressourcen, die durch einen Microsoft Defender-Plan geschützt sind.
    - **Partiell**: Dies gilt für **Abonnements**, für die einige, aber nicht alle Microsoft Defender-Pläne deaktiviert sind. Für das folgende Abonnement sind z. B. sieben Microsoft Defender-Pläne deaktiviert.

        :::image type="content" source="./media/asset-inventory/pricing-tier-partial.png" alt-text="Abonnement, das teilweise durch Microsoft Defender-Pläne geschützt ist.":::

1. Um die Ergebnisse der Abfrage weiter zu untersuchen, wählen Sie die Ressourcen aus, die für Sie von Interesse sind.

1. Um die zurzeit ausgewählten Filteroptionen als Abfrage im Resource Graph-Explorer anzuzeigen, wählen Sie **Abfrage öffnen** aus.

    ![Bestandsabfrage in ARG](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. Wenn Sie einige Filter definiert und die Seite offen gelassen haben, aktualisiert Defender für Cloud die Ergebnisse nicht automatisch. Änderungen an den Ressourcen wirken sich nicht auf die angezeigten Ergebnisse aus, es sei denn, Sie laden die Seite manuell neu oder wählen **Aktualisieren** aus.

## <a name="access-a-software-inventory"></a>Zugreifen auf einen Softwarebestand

Wenn Sie die Integration mit Microsoft Defender für Endpunkt und Microsoft Defender für Server aktiviert haben, haben Sie Zugriff auf den Softwarebestand.

:::image type="content" source="media/asset-inventory/software-inventory-filters.gif" alt-text="Wenn Sie die Lösung für Bedrohungen und Schwachstellen aktiviert haben, bietet das Bestandsverzeichnis von Defender für Cloud einen Filter zur Auswahl von Ressourcen anhand ihrer installierten Software.":::

> [!NOTE]
> Die Option „Leer“ zeigt Computer ohne Microsoft Defender für Endpunkt (oder ohne Microsoft Defender für Server) an.

Zusätzlich zu den Filtern auf der Ressourcenbestandsseite können Sie die Softwarebestandsdaten im Azure Resource Graph-Explorer untersuchen.

Beispiele für die Verwendung von Azure Resource Graph-Explorer für den Zugriff auf und das Untersuchen von Softwarebestandsdaten:

1. Öffnen Sie den **Azure Resource Graph-Explorer**.

    :::image type="content" source="./media/multi-factor-authentication-enforcement/opening-resource-graph-explorer.png" alt-text="Die Empfehlungsseite des Azure Resource Graph-Explorer** wird gestartet." :::

1. Wählen Sie den folgenden Abonnementbereich aus: „securityresources/softwareinventories“.

1. Geben Sie eine der folgenden Abfragen ein (Sie können sie auf anpassen oder ihre eigenen schreiben), und wählen Sie **Abfrage ausführen** aus.

    - So generieren Sie eine einfache Liste der installierten Software:

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | project id, Vendor=properties.vendor, Software=properties.softwareName, Version=properties.version
        ```

    - So filtern Sie nach Versionsnummern:

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | project id, Vendor=properties.vendor, Software=properties.softwareName, Version=tostring(properties.    version)
        | where Software=="windows_server_2019" and parse_version(Version)<=parse_version("10.0.17763.1999")
        ```

    - So suchen Sie Computer mit einer Kombination von Softwareprodukten:

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | extend vmId = properties.azureVmId
        | where properties.softwareName == "apache_http_server" or properties.softwareName == "mysql"
        | summarize count() by tostring(vmId)
        | where count_ > 1
        ```

    - Kombination eines Softwareprodukts mit einer anderen Sicherheitsempfehlung:

        (In diesem Beispiel: Computer mit installiertem MySQL und verfügbar gemachten Verwaltungsports)

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | extend vmId = tolower(properties.azureVmId)
        | where properties.softwareName == "mysql"
        | join (
        securityresources
        | where type == "microsoft.security/assessments"
        | where properties.displayName == "Management ports should be closed on your virtual machines" and properties.status.code == "Unhealthy"
        | extend vmId = tolower(properties.resourceDetails.Id)
        ) on vmId
        ```

## <a name="faq---inventory"></a>Häufig gestellte Fragen: Ressourcenbestand

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>Warum werden nicht alle meine Abonnements, Computer, Speicherkonten usw. angezeigt?

In der Bestandsansicht werden Ihre mit Defender für Cloud verknüpften Ressourcen aus der Perspektive von Cloud Security Posture Management (CSPM) aufgelistet. Die Filter geben nicht jede Ressource in Ihrer Umgebung zurück, sondern nur diejenigen mit ausstehenden (oder „aktiven“) Empfehlungen.

Der folgende Screenshot zeigt z. B. einen Benutzer mit Zugriff auf acht Abonnements, von denen jedoch nur sieben Empfehlungen aufweisen. Wenn Sie also nach **Ressourcentyp = Abonnements** filtern, werden nur die sieben Abonnements mit aktiven Empfehlungen im Bestand angezeigt:

:::image type="content" source="./media/asset-inventory/filtered-subscriptions-some.png" alt-text="Es werden nicht alle Abonnements zurückgegeben, wenn keine aktiven Empfehlungen vorliegen." lightbox="./media/asset-inventory/filtered-subscriptions-some.png":::

### <a name="why-do-some-of-my-resources-show-blank-values-in-the-defender-for-cloud-or-monitoring-agent-columns"></a>Warum zeigen einige meiner Ressourcen leere Werte in den Spalten für Defender für Cloud oder den Überwachungs-Agent an?

Nicht alle von Defender für Cloud überwachten Ressourcen verfügen über Agents. Beispielsweise müssen Azure Storage-Konten oder PaaS-Ressourcen wie Datenträger, Logic Apps, Data Lake Analysis und Event Hub nicht von Defender für Cloud überwacht werden.

Wenn Preise oder Agent-Überwachung für eine Ressource nicht relevant sind, wird in diesen Ressourcenbestandspalten nichts angezeigt.

:::image type="content" source="./media/asset-inventory/agent-pricing-blanks.png" alt-text="Einige Ressourcen zeigen in der Spalte für den Überwachungs-Agent oder für Defender für Cloud leere Informationen an." lightbox="./media/asset-inventory/agent-pricing-blanks.png":::

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurde die Ressourcenbestandsseite von Microsoft Defender für Cloud beschrieben.

Weitere Informationen zu verwandten Tools finden Sie auf den folgenden Seiten:

- [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml)
- [Kusto-Abfragesprache (KQL)](/azure/data-explorer/kusto/query/)
