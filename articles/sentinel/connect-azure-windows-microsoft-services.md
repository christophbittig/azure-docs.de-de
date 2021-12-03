---
title: Verbinden von Microsoft Sentinel mit Azure-, Windows- und Microsoft-Diensten
description: Erfahren Sie, wie Sie Microsoft Sentinel mit Azure- und Microsoft 365-Clouddiensten sowie mit Windows Server-Ereignisprotokollen verbinden.
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 79007f7716a8a24350bbfa6083b9891c425c120e
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523028"
---
# <a name="connect-microsoft-sentinel-to-azure-windows-microsoft-and-amazon-services"></a>Verbinden von Microsoft Sentinel mit Azure-, Windows-, Microsoft- und Amazon-Diensten

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Microsoft Sentinel verwendet Azure als Grundlage, um integrierte Dienst-zu-Dienst-Unterstützung für die Datenerfassung aus zahlreichen Azure- und Microsoft 365-Diensten, Amazon Web Services und verschiedenen Windows Server-Diensten bereitzustellen. Es gibt einige verschiedene Methoden, mit denen diese Verbindungen hergestellt werden, was in diesem Artikel beschrieben wird.

In diesem Artikel werden die folgenden Typen von Connectors behandelt:

- **API-basierte** Verbindungen
- Verbindungen mit **Diagnoseeinstellungen**, von denen einige von Azure Policy verwaltet werden
- **Log Analytics-Agent**-basierte Verbindungen

Dieser Artikel enthält Informationen, die für Gruppen von Connectors gemeinsam zutreffen. Informationen, die für jeden Connector spezifisch sind, finden Sie auf der zugehörigen [Referenzseite des Datenconnectors](data-connectors-reference.md), z. B. Lizenzierungsvoraussetzungen und Log Analytics-Tabellen für die Datenspeicherung.

Die folgenden Integrationen sind spezifischer und beliebter und werden jeweils in einem eigenen Artikeln behandelt:

- [Microsoft 365 Defender](connect-microsoft-365-defender.md)
- [Microsoft Defender für Cloud](connect-azure-security-center.md)
- [Azure Active Directory](connect-azure-active-directory.md)
- [Windows-Sicherheitsereignisse](connect-windows-security-events.md)
- [Amazon Web Services (AWS) – CloudTrail](connect-aws.md)

## <a name="api-based-connections"></a>API-basierte Verbindungen

### <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für den Log Analytics-Arbeitsbereich.
- Sie müssen über die Rolle „Globaler Administrator“ oder „Sicherheitsadministrator“ im Mandanten Ihres Microsoft Sentinel-Arbeitsbereichs verfügen.

### <a name="instructions"></a>Anweisungen

1. Wählen Sie im Microsoft Sentinel-Navigationsmenü die Option **Datenconnectors** aus.

1. Wählen Sie Ihren Dienst aus dem Katalog der Datenconnectors aus, und wählen Sie dann im Vorschaubereich **Connectorseite öffnen** aus.

1. Wählen Sie **Verbinden** aus, um das Streaming von Ereignissen und/oder Warnungen von Ihrem Dienst in Microsoft Sentinel zu starten.

1. Wenn auf der Connectorseite ein Abschnitt mit dem Titel **Incidents erstellen – empfohlen!** angezeigt wird, wählen Sie **Aktivieren** aus, wenn Sie aus Warnungen automatisch Incidents erstellen möchten.

Sie können die Daten für jeden Dienst mithilfe der Tabellennamen suchen und abfragen, die im Abschnitt für den Connector des Diensts auf der [Referenzseite für Datenconnectors](data-connectors-reference.md) angezeigt werden.

## <a name="diagnostic-settings-based-connections"></a>Auf Diagnoseeinstellungen basierende Verbindungen

Die Konfiguration einiger Connectors dieses Typs wird von Azure Policy verwaltet. Wählen Sie unten die Registerkarte **Azure Policy** aus, um Anweisungen zu erhalten. Wählen Sie für die anderen Connectors dieses Typs die Registerkarte **Eigenständig** aus.

# <a name="standalone"></a>[Eigenständig](#tab/SA)

### <a name="prerequisites"></a>Voraussetzungen

Zum Erfassen von Daten in Microsoft Sentinel:

- Sie benötigen Lese- und Schreibberechtigungen für den Microsoft Sentinel-Arbeitsbereich.

### <a name="instructions"></a>Anweisungen

1. Wählen Sie im Microsoft Sentinel-Navigationsmenü die Option **Datenconnectors** aus.

1. Wählen Sie Ihren Ressourcentyp aus dem Katalog der Datenconnectors aus, und wählen Sie dann im Vorschaubereich **Connectorseite öffnen** aus.

1. Wählen Sie auf der Connectorseite im Abschnitt **Konfiguration** den Link aus, um die Ressourcenkonfigurationsseite zu öffnen.

    Wenn eine Liste der Ressourcen des gewünschten Typs angezeigt wird, wählen Sie den Link für eine Ressource aus, deren Protokolle Sie erfassen möchten.

1. Wählen Sie im Navigationsmenü der Ressource **Diagnoseeinstellungen** aus.

1. Wählen Sie am Ende der Liste **+ Diagnoseeinstellung hinzufügen** aus.

1. Geben Sie auf dem Bildschirm **Diagnoseeinstellungen** einen Namen in das Feld **Name der Diagnoseeinstellungen** ein.

    Aktivieren Sie das Kontrollkästchen **An Log Analytics senden**. Zwei neue Felder werden darunter angezeigt. Wählen Sie das relevante **Abonnement** und den **Log Analytics-Arbeitsbereich** (in dem sich Microsoft Sentinel befindet) aus.

1. Aktivieren Sie die Kontrollkästchen der Protokolltypen und Metriken, die Sie erfassen möchten. Unsere empfohlenen Optionen für jeden Ressourcentyp finden Sie im Abschnitt für den Connector der Ressource auf der [Referenzseite für Datenconnectors](data-connectors-reference.md).

1. Wählen Sie im oberen Bereich des Bildschirms **Speichern** aus.

# <a name="azure-policy"></a>[Azure Policy](#tab/AP)

### <a name="prerequisites"></a>Voraussetzungen

Zum Erfassen von Daten in Microsoft Sentinel:

- Sie benötigen Lese- und Schreibberechtigungen für den Microsoft Sentinel-Arbeitsbereich.

- Wenn Sie Azure Policy verwenden möchten, um eine Richtlinie zum Protokollstreaming auf Ihre Ressourcen anzuwenden, müssen Sie die Rolle „Besitzer“ für den Zuweisungsbereich der Richtlinie besitzen.

### <a name="instructions"></a>Anweisungen

Connectors dieses Typs verwenden Azure Policy, um eine einzelne Diagnoseeinstellungskonfiguration auf eine Sammlung von Ressourcen eines einzelnen Typs anzuwenden, die als Bereich definiert sind. Sie können die Protokolltypen, die von einem bestimmten Ressourcentyp erfasst werden, auf der linken Seite der Connectorseite für diese Ressource unter **Datentypen** anzeigen.

1. Wählen Sie im Microsoft Sentinel-Navigationsmenü die Option **Datenconnectors** aus.

1. Wählen Sie Ihren Ressourcentyp aus dem Katalog der Datenconnectors aus, und wählen Sie dann im Vorschaubereich **Connectorseite öffnen** aus.

1. Erweitern Sie auf der Connectorseite im Abschnitt **Konfiguration** alle dort angezeigten Expander, und wählen Sie die Schaltfläche **Azure Policy-Zuweisungs-Assistenten starten** aus.

    Der Richtlinienzuweisungs-Assistent wird geöffnet, um eine neue Richtlinie zu erstellen, wobei ein Richtlinienname bereits ausgefüllt ist.

    1. Wählen Sie auf der Registerkarte **Grundlagen** unter **Bereich** die Schaltfläche mit den drei Punkten aus, um Ihr Abonnement (und optional eine Ressourcengruppe) auszuwählen. Sie können auch eine Beschreibung hinzufügen.

    1. In der Registerkarte **Parameter**:
       - Deaktivieren Sie das Kontrollkästchen **Nur Parameter anzeigen, die Eingaben erfordern**.
       - Wenn die Felder **Auswirkung** und **Einstellung** angezeigt werden, lassen Sie diese unverändert.
       - Wählen Sie in der Dropdownliste **Log Analytics-Arbeitsbereich** Ihren Microsoft Sentinel-Arbeitsbereich aus.
       - Die übrigen Dropdownfelder stellen die verfügbaren Diagnoseprotokolltypen dar. Behalten Sie für alle Protokolltypen, die erfasst werden sollen, die Markierung „True“ bei.

    1. Die Richtlinie wird auf Ressourcen angewendet, die in Zukunft hinzugefügt werden. Wählen Sie zum Anwenden der Richtlinie auf Ihre vorhandenen Ressourcen die Registerkarte **Wartung** aus, und aktivieren Sie das Kontrollkästchen **Wartungstask erstellen**.

    1. Klicken Sie auf der Registerkarte **Überprüfen + erstellen** auf **Erstellen**. Damit ist die Richtlinie dem ausgewählten Bereich zugewiesen.

---

> [!NOTE]
>
> Mit diesem Typ von Datenconnector werden die Konnektivitätsstatusindikatoren (ein Farbstreifen im Datenconnectors-Katalog sowie Verbindungssymbole neben den Datentypnamen) nur dann als *verbunden* (grün) angezeigt, wenn Daten irgendwann innerhalb der letzten 14 Tage erfasst wurden. Wenn 14 Tage ohne Datenerfassung vergangen sind, wird der Connector als „getrennt“ angezeigt. In dem Moment, in dem weitere Daten den Connector passieren, wird der Status wieder als *verbunden* angezeigt.

Sie können die Daten für jeden Ressourcentyp mithilfe der Tabellennamen suchen und abfragen, die im Abschnitt für den Connector der Ressource auf der [Referenzseite für Datenconnectors](data-connectors-reference.md) angezeigt werden.

## <a name="windows-agent-based-connections"></a>Auf Windows-Agent-basierende Verbindungen

# <a name="azure-monitor-agent"></a>[Azure Monitor-Agent](#tab/AMA)

> [!IMPORTANT]
>
> - Einige Connectors, die auf dem Azure Monitor-Agent (AMA) basieren, befinden sich derzeit in der **VORSCHAUPHASE**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Der [Azure Monitor-Agent](../azure-monitor/agents/azure-monitor-agent-overview.md) verwendet **Datensammlungsregeln (Data Collection Rules, DCRs)** , um die Daten zu konfigurieren, die von den einzelnen Agents gesammelt werden sollen. Datensammlungsregeln haben zwei Vorteile:

- Sie ermöglichen Ihnen das **allgemeine Verwalten von Sammlungseinstellungen**, während Sie gleichzeitig eindeutige, bereichsbezogene Konfigurationen für Untergruppen von Computern festlegen können. Dank ihrer Unabhängigkeit von Arbeitsbereichen und virtuellen Computern können sie einmalig definiert und für verschiedene Computer und Umgebungen wiederverwendet werden. Weitere Informationen finden Sie unter [Konfigurieren der Datensammlung für den Azure Monitor-Agent](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md).

- **Erstellen Sie benutzerdefinierte Filter**, um genau die Ereignisse auszuwählen, die Sie erfassen möchten. Der Azure Monitor-Agent filtert anhand dieser Regeln die Daten *in der Quelle* und erfasst nur die von Ihnen gewünschten Ereignisse, während alles andere unberücksichtigt bleibt. Dies kann Ihnen viel Geld bei den Kosten für die Datenerfassung sparen!

Informationen zum Erstellen von Datensammlungsregeln finden Sie unten.

### <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für den Microsoft Sentinel-Arbeitsbereich.

- Um Ereignisse von einem System zu erfassen, das kein virtueller Azure-Computer ist, muss auf dem System [**Azure Arc**](../azure-monitor/agents/azure-monitor-agent-install.md) installiert und aktiviert sein, *bevor* Sie den Azure Monitor-Agent-basierten Connector aktivieren.

  Dies schließt Folgendes ein:

  - Auf physischen Computern installierte Windows-Server
  - Auf lokalen virtuellen Computern installierte Windows-Server
  - Auf virtuellen Computern in Nicht-Azure-Clouds installierte Windows-Server

### <a name="instructions"></a>Anweisungen

1. Wählen Sie im Microsoft Sentinel-Navigationsmenü die Option **Datenconnectors** aus. Wählen Sie in der Liste Ihren Connector und dann im Detailbereich die Option **Connectorseite öffnen** aus. Folgen Sie anschließend wie im Rest dieses Abschnitts beschrieben den Anweisungen auf dem Bildschirm auf der Registerkarte **Anweisungen**.

1. Vergewissern Sie sich, dass Sie über die entsprechenden Berechtigungen verfügen, wie im Abschnitt **Voraussetzungen** auf der Seite „Connector“ beschrieben.

1. Wählen Sie unter **Konfiguration** die Option **+ Datensammlungsregeln hinzufügen** aus. Der Assistent zum **Erstellen von Datensammlungsregel** wird auf der rechten Seite geöffnet.

1. Geben Sie unter **Grundlagen** einen **Regelnamen** ein, und geben Sie ein **Abonnement** und eine **Ressourcengruppe** an, in denen die Datensammlungsregel (DCR) erstellt werden soll. Dies *muss nicht* dieselbe Ressourcengruppe oder dasselbe Abonnement sein, in denen sich die überwachten Computer und ihre Zuordnungen befinden, solange sie sich im selben Mandanten befinden.

1. Wählen Sie auf der Registerkarte **Ressourcen** die Option **+Ressourcen hinzufügen** aus, um Computer hinzuzufügen, für die die Datensammlungsregel gelten soll. Das Dialogfeld **Bereich auswählen** wird geöffnet, und es wird eine Liste der verfügbaren Abonnements angezeigt. Erweitern Sie ein Abonnement, um seine Ressourcengruppen anzuzeigen, und erweitern Sie eine Ressourcengruppe, um die verfügbaren Computer anzuzeigen. In der Liste werden virtuelle Azure-Computer und Server mit Azure Arc-Unterstützung angezeigt. Sie können die Kontrollkästchen von Abonnements oder Ressourcengruppen aktivieren, um alle darin enthaltenen Computer auszuwählen, oder Sie können einzelne Computer auswählen. Wählen Sie **Anwenden** aus, wenn Sie alle Ihre Computer ausgewählt haben. Am Ende dieses Prozesses wird der Azure Monitor-Agent auf allen ausgewählten Computern installiert, auf denen er noch nicht installiert ist.

1. Wählen Sie auf der Registerkarte **Sammeln** die Ereignisse aus, die Sie sammeln möchten: Wählen Sie **Alle Ereignisse** oder **Benutzerdefiniert** aus, um andere Protokolle anzugeben oder Ereignisse mit [XPath-Abfragen](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries) zu filtern (siehe Hinweis unten). Geben Sie Ausdrücke in das Feld ein, die zu bestimmten XML-Kriterien für zu erfassende Ereignisse ausgewertet werden, und wählen Sie dann **Hinzufügen** aus. Sie können bis zu 20 Ausdrücke in ein einzelnes Feld und bis zu 100 Felder in eine Regel eingeben.

    Weitere Informationen zu [Datensammlungsregeln](../azure-monitor/agents/data-collection-rule-overview.md#create-a-dcr) finden Sie in der Dokumentation zu Azure Monitor.

    > [!NOTE]
    >
    > - Der Connector „Windows-Sicherheitsereignisse“ verfügt über zwei andere [**vordefinierte Ereignissätze**](windows-security-event-id-reference.md), die Sie für die Sammlung auswählen können: **Allgemein** und **Minimal**.
    >
    > - Der Azure Monitor-Agent unterstützt XPath-Abfragen **nur für [XPath, Version 1.0](/windows/win32/wes/consuming-events#xpath-10-limitations)** .

1. Wenn Sie alle gewünschten Filterausdrücke hinzugefügt haben, wählen Sie **Weiter: Überprüfen + erstellen** aus.

1. Wenn die Meldung „Überprüfung erfolgreich“ angezeigt wird, wählen Sie **Erstellen** aus. 

Alle Ihre Datensammlungsregeln (einschließlich der über die API erstellten Regeln) werden unter **Konfiguration** auf der Connectorseite angezeigt. Dort können Sie vorhandene Regeln bearbeiten oder löschen.

> [!TIP]
> Verwenden Sie zum Testen der Gültigkeit einer XPath-Abfrage das PowerShell-Cmdlet **Get-WinEvent** mit dem Parameter *-FilterXPath*. Das folgende Skript ist ein Beispiel hierfür:
>
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
>
> - Werden Ereignisse zurückgegeben, ist die Abfrage gültig.
> - Wenn Sie die Meldung „Es wurden keine Ereignisse gefunden, die mit den angegebenen Auswahlkriterien übereinstimmen“ erhalten, ist die Abfrage möglicherweise gültig, aber es sind keine übereinstimmenden Ereignisse auf dem lokalen Computer vorhanden.
> - Wenn Sie die Meldung „Die angegebene Abfrage ist ungültig“ erhalten, ist die Abfragesyntax ungültig.

### <a name="create-data-collection-rules-using-the-api"></a>Erstellen von Datensammlungsregeln mithilfe der API

Sie können Datensammlungsregeln auch mithilfe der API erstellen ([siehe Schema](/rest/api/monitor/data-collection-rules)), was ihnen das Leben erleichtern kann, wenn Sie viele Regeln erstellen (z. B. wenn Sie ein MSSP sind). Hier ist ein Beispiel angegeben (für den Connector [Sicherheitsrelevante Windows-Ereignisse über Azure Monitor-Agent](data-connectors-reference.md#windows-security-events-via-ama)), das Sie als Vorlage für die Erstellung einer Regel verwenden können:

**Anforderungs-URL und -Header**

```http
PUT https://management.azure.com/subscriptions/703362b3-f278-4e4b-9179-c76eaf41ffc2/resourceGroups/myResourceGroup/providers/Microsoft.Insights/dataCollectionRules/myCollectionRule?api-version=2019-11-01-preview
```

**Anforderungstext**

```json
{
    "location": "eastus",
    "properties": {
        "dataSources": {
            "windowsEventLogs": [
                {
                    "streams": [
                        "Microsoft-SecurityEvent"
                    ],
                    "xPathQueries": [
                        "Security!*[System[(EventID=) or (EventID=4688) or (EventID=4663) or (EventID=4624) or (EventID=4657) or (EventID=4100) or (EventID=4104) or (EventID=5140) or (EventID=5145) or (EventID=5156)]]"
                    ],
                    "name": "eventLogsDataSource"
                }
            ]
        },
        "destinations": {
            "logAnalytics": [
                {
                    "workspaceResourceId": "/subscriptions/703362b3-f278-4e4b-9179-c76eaf41ffc2/resourceGroups/myResourceGroup/providers/Microsoft.OperationalInsights/workspaces/centralTeamWorkspace",
                    "name": "centralWorkspace"
                }
            ]
        },
        "dataFlows": [
            {
                "streams": [
                    "Microsoft-SecurityEvent"
                ],
                "destinations": [
                    "centralWorkspace"
                ]
            }
        ]
    }
}
```

Lesen Sie sich die [vollständige Beschreibung der Datensammlungsregeln](../azure-monitor/agents/data-collection-rule-overview.md) in der Azure Monitor-Dokumentation durch.

# <a name="log-analytics-agent-legacy"></a>[Log Analytics-Agent (Legacy)](#tab/LAA)

### <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für den Log Analytics-Arbeitsbereich und jeden Arbeitsbereich, der Computer enthält, von denen Sie Protokolle sammeln möchten.
- Zusätzlich zu allen Microsoft Sentinel-Rollen müssen Sie noch die Rolle **Log Analytics-Mitwirkender** für die SecurityInsights-Lösung (Microsoft Sentinel) in diesen Arbeitsbereichen besitzen.

### <a name="instructions"></a>Anweisungen

1. Wählen Sie im Microsoft Sentinel-Navigationsmenü die Option **Datenconnectors** aus.

1. Wählen Sie Ihren Dienst(**DNS** oder **Windows Firewall**) und dann **Connectorseite öffnen** aus.

1. Installieren und registrieren Sie den Agent auf dem Gerät, das die Protokolle generiert.

    | Computertyp  | Anweisungen  |
    | --------- | --------- |
    | **Für einen virtuellen Windows-Computer in Azure** | 1. Erweitern Sie unter **Wählen Sie aus, wo der Agent installiert werden soll** die Option **Agent auf virtuellem Windows-Computer in Azure installieren**. <br><br>2. Wählen Sie den Link **Agent für virtuellen Windows-Computer in Azure herunterladen und installieren >** . <br><br>3. Wählen Sie auf dem Blatt **Virtuelle Computer** eine VM aus, auf der der Agent installiert werden soll, und wählen Sie dann **Verbinden** aus. Wiederholen Sie diesen Schritt für jeden virtuellen Computer, den Sie verbinden möchten. |
    | **Für alle anderen Windows-Computer** | 1. Erweitern Sie unter **Wählen Sie aus, wo der Agent installiert werden soll** die Option **Agent auf Windows-Computer (kein Azure) installieren**. <br><br>2. Klicken Sie auf den Link **Agent für Windows-Computer (kein Azure) herunterladen und installieren**.  <br><br>3. Wählen Sie auf dem Blatt **Agents-Verwaltung** auf der Registerkarte **Windows-Server** je nach Eignung den Link **Windows-Agent herunterladen** für 32-Bit- oder 64-Bit-Systeme aus.  <br><br>4. Installieren Sie den Agent mithilfe der heruntergeladenen ausführbaren Datei auf den Windows-Systemen Ihrer Wahl, und konfigurieren Sie ihn mit der **Arbeitsbereich-ID und den Schlüsseln**, die unter den Downloadlinks im vorherigen Schritt angezeigt werden. |
    | | |

> [!NOTE]
>
> Damit auch Windows-Systeme ohne die erforderliche Internetverbindung weiterhin Ereignisse an Microsoft Sentinel streamen können, laden Sie das **Log Analytics Gateway** herunter, und installieren Sie es auf einem separaten Computer, der als Proxy fungiert. Dazu verwenden Sie auf der Seite **Agent-Verwaltung** den Link **Log Analytics-Gateway herunterladen**.  Sie müssen dennoch den Log Analytics-Agent auf allen Windows-Systemen installieren, deren Ereignisse Sie erfassen möchten.
>
> Weitere Informationen zu diesem Szenario finden Sie in der [**Log Analytics-Gateway**-Dokumentation](../azure-monitor/agents/gateway.md).

Weitere Installationsmöglichkeiten und Informationen finden Sie in der [**Log Analytics-Agent**-Dokumentation](../azure-monitor/agents/agent-windows.md).


#### <a name="determine-the-logs-to-send"></a>Bestimmen der zu sendenden Protokolle

Wählen Sie für den Windows DNS-Server- und Windows Firewall-Connector jeweils die Schaltfläche **Lösung installieren** aus. Wählen Sie für den Legacy-Connector „Windows-Sicherheitsereignisse“ den [**Ereignissatz**](windows-security-event-id-reference.md), den Sie senden möchten, und dann die Option **Aktualisieren** aus.

Sie können die Daten für diese Dienste suchen und abfragen, indem Sie die Tabellennamen in den entsprechenden Abschnitten auf der Seite mit der [Referenz zu Datenconnectors](data-connectors-reference.md) verwenden.

---

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Azure-, Microsoft- und Windows-Dienste sowie Amazon Web Services mit Microsoft Sentinel verbinden. 
- Erfahren Sie Allgemeines über [Microsoft Sentinel-Datenconnectors](connect-data-sources.md).
- [Ermitteln Sie den richtigen Microsoft Sentinel-Datenconnector](data-connectors-reference.md).
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Microsoft Sentinel](detect-threats-built-in.md).
