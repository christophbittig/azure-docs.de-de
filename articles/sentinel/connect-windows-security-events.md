---
title: Verknüpfen von Windows-Sicherheitsereignisdaten mit Azure Sentinel (Version mit Registerkarten) | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit dem Connector für Windows-Sicherheitsereignisse alle sicherheitsrelevanten Ereignisse von Ihren Windows-Systemen an Ihren Azure Sentinel-Arbeitsbereich streamen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d51d2e09-a073-41c8-b396-91d60b057e6a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2021
ms.author: yelevin
ms.openlocfilehash: 133d5e01de4f5fe511677fc5226eb0aede258eab
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252282"
---
# <a name="connect-to-windows-servers-to-collect-security-events"></a>Verbinden mit Windows Server zum Sammeln von Sicherheitsereignissen

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Mit dem Connector für Windows-Sicherheitsereignisse können Sie von jedem Windows Server (physisch oder virtuell, lokal oder in einer beliebigen Cloud), der mit Ihrem Azure Sentinel-Arbeitsbereich verbunden ist, Sicherheitsereignisse streamen. Dadurch können Sie sicherheitsrelevante Windows-Ereignisse in Ihren Dashboards anzeigen, um damit benutzerdefinierte Benachrichtigungen zu erstellen und basierend darauf Ihre Untersuchungen zu verbessern. Hierdurch können Sie zusätzliche Erkenntnisse über das Netzwerk Ihrer Organisation erlangen und Ihre Möglichkeiten für Sicherheitsvorgänge ausbauen.

## <a name="connector-options"></a>Connector-Optionen

Der Connector für Windows-Sicherheitsereignisse unterstützt die folgenden Versionen:

|Connector-Version  |BESCHREIBUNG  |
|---------|---------|
|**Sicherheitsereignisse**     |Legacyversion, die auf dem Log Analytics-Agent basiert und manchmal als Microsoft Monitoring Agent (MMA) oder OMS-Agent bezeichnet wird. <br><br>Begrenzt auf 10.000 Ereignisse pro Sekunde. Um eine optimale Leistung sicherzustellen, gewährleisten Sie, dass maximal 8.500 Ereignisse pro Sekunde eingehalten werden.       |
|**Windows-Sicherheitsereignisse**     |Neuere Version, derzeit in der Vorschauphase und basiert auf dem Azure Monitor-Agent (AMA).   <br><br>Unterstützt zusätzliche Features, z. B. Protokollfilterung vor der Erfassung und individuelle Datensammlungsregeln für bestimmte Gruppen von Computern.      |
|     |         |


> [!NOTE]
> Der MMA für Linux unterstützt kein Multi-Homing, das Protokolle an mehrere Arbeitsbereiche sendet. Wenn Sie Multi-Homing benötigen, wird empfohlen, den Connector für **Windows-Sicherheitsereignisse** zu verwenden.

> [!TIP]
> Wenn Sie mehrere Agents benötigen, können Sie eine VM-Skalierungsgruppe verwenden, die für die Ausführung mehrerer Agents für die Protokollerfassung festgelegt ist, oder mehrere Computer verwenden. Sowohl der Connector für Sicherheitsereignisse als auch der Connector für Windows-Sicherheitsereignisse kann dann mit einem Lastenausgleich verwendet werden, um sicherzustellen, dass die Computer nicht überlastet werden, und um Datenduplizierung zu verhindern.
>

Dieser Artikel enthält Informationen zu beiden Versionen des Connectors. Wählen Sie auf den folgenden Registerkarten aus, um die für Ihren ausgewählten Connector relevanten Informationen anzuzeigen.


# <a name="log-analytics-agent-legacy"></a>[Log Analytics-Agent (Legacy)](#tab/LAA)

Sie können aus den folgenden Optionen auswählen, welche Ereignisse gestreamt werden sollen: <a name="event-sets"></a>

- **Alle Ereignisse:** alle Windows-Sicherheits- und AppLocker-Ereignisse.
- **Allgemein:** ein Standardsatz von Ereignissen zu Überwachungszwecken. In diesem Satz ist ein vollständiger Benutzerüberwachungspfad enthalten. Diese Gruppe enthält beispielsweise sowohl Ereignisse für Benutzeranmeldungen als auch Ereignisse für Benutzerabmeldungen (Ereignis-IDs 4624 und 4634). Außerdem enthält sie Überwachungsaktionen wie Sicherheitsgruppenänderungen, wichtige Domänencontroller-Kerberos-Vorgänge und andere Ereignisse, die Best Practices entsprechen.

    Die Ereignisgruppe **Common** (Häufige) enthält möglicherweise einige Arten von Ereignissen, die nicht so häufig auftreten.  Der Grund hierfür ist, dass der Hauptzweck der Gruppe **Common** (Häufige) darin besteht, die Anzahl von Ereignissen auf eine besser verwaltbare Anzahl zu senken und dennoch die Funktion eines vollständigen Überwachungspfads beizubehalten.

- **Minimal:** ein kleiner Satz von Ereignissen, die auf potenzielle Bedrohungen hinweisen können. Diese Gruppe enthält keinen vollständigen Überwachungspfad. Sie deckt nur Ereignisse ab, die möglicherweise auf eine erfolgreiche Sicherheitsverletzung hindeuten, sowie andere wichtige Ereignisse, die nur sehr selten auftreten. So enthält diese Gruppe beispielsweise erfolgreiche und nicht erfolgreiche Benutzeranmeldungen (Ereignis-IDs 4624 und 4625), aber keine Informationen zu Abmeldungen (4634). Letztere sind zwar wichtig für die Überwachung, aber nicht für das Erkennen von Sicherheitsverletzungen, und sie treten zudem relativ häufig auf. Der Großteil des Datenvolumens dieser Gruppe ist auf Anmeldeereignisse und auf Prozesserstellungsereignisse (Ereignis-ID 4688) zurückzuführen.

- **Keine:** keine Sicherheits- oder AppLocker-Ereignisse. (Mit dieser Einstellung wird der Connector deaktiviert.)

> [!NOTE]
> Das Erfassen von sicherheitsrelevanten Ereignissen für einen einzelnen Arbeitsbereich kann entweder im Azure Security Center oder in Azure Sentinel konfiguriert werden, aber nicht in beiden Diensten. Wenn Sie ein Onboarding für Azure Sentinel in einem Arbeitsbereich durchführen möchten, der bereits Azure Defender-Benachrichtigungen aus Azure Security Center empfängt und für das Erfassen sicherheitsrelevanter Ereignisse konfiguriert ist, haben Sie zwei Möglichkeiten:
> - Behalten Sie das Erfassen von sicherheitsrelevanten Ereignissen im Azure Security Center bei. Sie können diese Ereignisse sowohl in Azure Sentinel als auch in Azure Defender abfragen und analysieren. Allerdings können Sie in Azure Sentinel weder den Verbindungsstatus des Connectors überwachen noch dessen Konfiguration ändern. Wenn dies wichtig für Sie ist, ziehen Sie die zweite Möglichkeit in Betracht.
>
> - [Deaktivieren Sie das Erfassen von sicherheitsrelevanten Ereignissen](../security-center/security-center-enable-data-collection.md) im Azure Security Center, und fügen Sie dann erst den Connector für sicherheitsrelevante Ereignisse in Azure Sentinel hinzu. Wie bei der ersten Möglichkeit können Sie Ereignisse sowohl in Azure Sentinel als auch in Azure Defender/ASC abfragen und analysieren. Nun können Sie allerdings in Azure Sentinel – und nur dort – den Verbindungsstatus des Connectors überwachen und dessen Konfiguration ändern.

# <a name="azure-monitor-agent-new"></a>[Azure Monitor-Agent (New)](#tab/AMA)

> [!IMPORTANT]
>
> - Der auf dem Azure Monitor-Agent (AMA) basierende Datenconnector für Windows-Sicherheitsereignisse befindet sich derzeit in der **VORSCHAUPHASE**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Der [Azure Monitor-Agent](../azure-monitor/agents/azure-monitor-agent-overview.md) verwendet **Datensammlungsregeln (Data Collection Rules, DCR)** , um die Daten zu konfigurieren, die von den einzelnen Agents gesammelt werden sollen. Datensammlungsregeln ermöglichen Ihnen das Verwalten von Sammlungseinstellungen nach Maß, während Sie gleichzeitig eindeutige und begrenzte Konfigurationen für Untergruppen von Computern zulassen. Dank ihrer Unabhängigkeit von Arbeitsbereichen und virtuellen Computern können sie einmalig definiert und für verschiedene Computer und Umgebungen wiederverwendet werden. Weitere Informationen finden Sie unter [Konfigurieren der Datensammlung für den Azure Monitor-Agent](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md).

Neben den vorab ausgewählten Ereignissätzen (**Alle Ereignisse**, **Minimal** oder **Allgemein**), die Sie mit dem alten Connector erfassen können, können Sie mit **Datensammlungsregeln** benutzerdefinierte Filter erstellen, um die genauen Ereignisse auszuwählen, die Sie erfassen möchten. Der Azure Monitor-Agent filtert anhand dieser Regeln die Daten *in der Quelle* und erfasst nur die von Ihnen gewünschten Ereignisse, während alles andere unberücksichtigt bleibt. Dies kann Ihnen viel Geld bei den Kosten für die Datenerfassung sparen!

In diesem Dokument erfahren Sie, wie Sie Datensammlungsregeln erstellen.

> [!NOTE]
> - **Koexistenz mit anderen Agents**
> 
>   Der Azure Monitor-Agent kann parallel zu den bereits vorhandenen Agents vorhanden sein, sodass Sie den Legacy-Connector während der Bewertung oder Migration weiterhin verwenden können. Dies ist aufgrund der eingeschränkten Unterstützung vorhandener Lösungen besonders wichtig, während sich der neue Connector in der Vorschauphase befindet. Eine doppelte Datensammlung kann jedoch zu einer Verzerrung von Abfrageergebnissen führen und zusätzliche Kosten für die Datenerfassung und -aufbewahrung zur Folge haben.
> 
> - **Sammeln von Sicherheitsereignissen von Nicht-Azure-Computern**
> 
>   Um Sicherheitsereignisse von einem System zu erfassen, das kein virtueller Azure-Computer ist, muss auf dem System [**Azure Arc**](../azure-monitor/agents/azure-monitor-agent-install.md) installiert und aktiviert sein, *bevor* Sie den Azure Monitor Agent-basierten Connector aktivieren.
>   
>   Dies schließt Folgendes ein:
>   
>    - Auf physischen Computern installierte Windows-Server
>    - Auf lokalen virtuellen Computern installierte Windows-Server
>    - Auf virtuellen Computern in Nicht-Azure-Clouds installierte Windows-Server

---

## <a name="set-up-the-windows-security-events-connector"></a>Einrichten des Connectors für sicherheitsrelevante Windows-Ereignisse

Gehen Sie wie folgt vor, um sicherheitsrelevante Windows-Ereignisse in Azure Sentinel zu erfassen:

# <a name="log-analytics-agent-legacy"></a>[Log Analytics-Agent (Legacy)](#tab/LAA)

1. Klicken Sie im Azure Sentinel-Navigationsmenü auf **Data connectors** (Datenconnectors). Wählen Sie aus der Liste der Connectors **Sicherheitsrelevante Ereignisse** und dann im Detailbereich **Connectorseite öffnen** aus. Folgen Sie anschließend wie im Rest dieses Abschnitts beschrieben den Anweisungen auf dem Bildschirm auf der Registerkarte **Anweisungen**.

1. Vergewissern Sie sich, dass Sie über die entsprechenden Berechtigungen verfügen, wie im Abschnitt **Voraussetzungen** auf der Seite „Connector“ beschrieben.

1. Laden Sie auf den Computern, für die Sie sicherheitsrelevante Ereignisse an Azure Sentinel streamen wollen, den [Log Analytics-Agent](../azure-monitor/agents/log-analytics-agent.md) (auch als Microsoft Monitoring Agent oder MMA bezeichnet) herunter, und installieren Sie ihn dort.

    Gehen Sie bei virtuellen Azure-Computern wie folgt vor:
    
    1. Klicken Sie auf **Install agent on Azure Windows Virtual Machine** (Agent auf virtuellem Windows-Computer (Azure) installieren) und anschließend auf den unten angezeigten Link.
    1. Klicken Sie für jeden virtuellen Computer, mit dem Sie eine Verbindung herstellen möchten, auf dessen Namen in der rechts angezeigten Liste, und klicken Sie dann auf **Verbinden**.

    Gehen Sie bei Windows-Computern (physische, virtuelle lokale oder virtuelle in einer anderen Cloud), die keine Azure-Computer sind, wie folgt vor:

    1. Klicken Sie auf **Install agent on non-Azure Windows Machine** (Agent auf einem Windows-Computer (kein Azure) installieren) und anschließend auf den unten angezeigten Link.
    1. Klicken Sie auf die entsprechenden Downloadlinks, die rechts unter **Windows-Computer** angezeigt werden.
    1. Installieren Sie mit der heruntergeladenen ausführbaren Datei den Agent auf den Windows-Systemen Ihrer Wahl, und konfigurieren Sie ihn mit der **Arbeitsbereich-ID und den Schlüsseln**, die unter den oben erwähnten Downloadlinks angezeigt werden.

    > [!NOTE]
    >
    > Laden Sie über den Link unten rechts das **OMS-Gateway** herunter, und installieren Sie es auf einem separaten Computer, sodass dieser als Proxy fungiert, um es Windows-Systemen zu ermöglichen, auch ohne die erforderliche Internetverbindung weiterhin Ereignisse an Azure Sentinel zu streamen.  Sie müssen dennoch den Log Analytics-Agent auf allen Windows-Systemen installieren, deren Ereignisse Sie erfassen möchten.
    >
    > Weitere Informationen zu diesem Szenario finden Sie in der [**Log Analytics-Gateway**-Dokumentation](../azure-monitor/agents/gateway.md).

    Weitere Installationsmöglichkeiten und Informationen finden Sie in der [**Log Analytics-Agent**-Dokumentation](../azure-monitor/agents/agent-windows.md).

1. Wählen Sie die Gruppe von Ereignissen (Alle, Häufige oder Minimal) aus, die Sie streamen wollen. Sehen Sie sich die [Listen der Ereignis-IDs](#event-id-reference) an, die in den „Minimal“- und „Allgemein“-Ereignissätzen enthalten sind.

1. Wählen Sie **Aktualisieren** aus.

1. Tippen Sie `SecurityEvent` in das Abfragefenster, um das relevante Schema in Log Analytics für sicherheitsrelevante Windows-Ereignisse zu verwenden.

# <a name="azure-monitor-agent-new"></a>[Azure Monitor-Agent (New)](#tab/AMA)

1. Klicken Sie im Azure Sentinel-Navigationsmenü auf **Data connectors** (Datenconnectors). Klicken Sie in der Liste mit Connectors auf **Windows-Sicherheitsereignisse (Vorschau)** und dann unten rechts auf die Schaltfläche **Connectorseite öffnen**. Folgen Sie anschließend wie im Rest dieses Abschnitts beschrieben den Anweisungen auf dem Bildschirm auf der Registerkarte **Anweisungen**.

1. Vergewissern Sie sich, dass Sie über die entsprechenden Berechtigungen verfügen, wie im Abschnitt **Voraussetzungen** auf der Seite „Connector“ beschrieben.

    1. Sie benötigen Lese- und Schreibberechtigungen für Ihren Arbeitsbereich und für alle Datenquellen, aus denen Sie Windows-Sicherheitsereignisse erfassen möchten.

    1. Um Ereignisse von Windows-Computern zu erfassen, die keine Azure-VMs sind, muss auf den Computern (physisch oder virtuell) Azure Arc installiert und aktiviert sein. [Weitere Informationen](../azure-monitor/agents/azure-monitor-agent-install.md)

1. Wählen Sie unter **Konfiguration** die Option **+ Datensammlungsregeln hinzufügen** aus. Der Assistent zum **Erstellen von Datensammlungsregel** wird auf der rechten Seite geöffnet.

1. Geben Sie unter **Grundlagen** einen **Regelnamen** ein, und geben Sie ein **Abonnement** und eine **Ressourcengruppe** an, in denen die Datensammlungsregel (DCR) erstellt werden soll. Dies *muss nicht* dieselbe Ressourcengruppe oder dasselbe Abonnement sein, in denen sich die überwachten Computer und ihre Zuordnungen befinden, solange sie sich im selben Mandanten befinden.

1. Wählen Sie auf der Registerkarte **Ressourcen** die Option **+Ressourcen hinzufügen** aus, um Computer hinzuzufügen, für die die Datensammlungsregel gelten soll. Das Dialogfeld **Bereich auswählen** wird geöffnet, und es wird eine Liste der verfügbaren Abonnements angezeigt. Erweitern Sie ein Abonnement, um seine Ressourcengruppen anzuzeigen, und erweitern Sie eine Ressourcengruppe, um die verfügbaren Computer anzuzeigen. In der Liste werden virtuelle Azure-Computer und Server mit Azure Arc-Unterstützung angezeigt. Sie können die Kontrollkästchen von Abonnements oder Ressourcengruppen aktivieren, um alle darin enthaltenen Computer auszuwählen, oder Sie können einzelne Computer auswählen. Wählen Sie **Anwenden** aus, wenn Sie alle Ihre Computer ausgewählt haben. Am Ende dieses Prozesses wird der Azure Monitor-Agent auf allen ausgewählten Computern installiert, auf denen er noch nicht installiert ist.

1. Wählen Sie auf der Registerkarte **Sammeln** den [Ereignissatz](#event-id-reference) aus, den Sie sammeln möchten, oder wählen Sie **Benutzerdefiniert** aus, um andere Protokolle anzugeben oder um Ereignisse mittels [XPath-Abfragen](../azure-monitor/agents/data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries) zu filtern. Geben Sie Ausdrücke in das Feld ein, die zu bestimmten XML-Kriterien für zu erfassende Ereignisse ausgewertet werden, und wählen Sie dann **Hinzufügen** aus. Sie können bis zu 20 Ausdrücke in ein einzelnes Feld und bis zu 100 Felder in eine Regel eingeben.

    Weitere Informationen zu [Datensammlungsregeln](../azure-monitor/agents/data-collection-rule-overview.md#create-a-dcr) finden Sie in der Dokumentation zu Azure Monitor.

    > [!NOTE]
    > - Stellen Sie sicher, dass Sie nur Windows-Sicherheits- und AppLocker-Protokolle abfragen. Ereignisse aus anderen Windows-Protokollen oder aus Sicherheitsprotokollen anderer Umgebungen entsprechen möglicherweise nicht dem Windows-Sicherheitereignisschema und werden nicht ordnungsgemäß analysiert. In diesem Fall werden sie nicht in Ihrem Arbeitsbereich erfasst.
    >
    > - Der Azure Monitor-Agent unterstützt XPath-Abfragen **nur für [XPath, Version 1.0](/windows/win32/wes/consuming-events#xpath-10-limitations)** .

1. Wenn Sie alle gewünschten Filterausdrücke hinzugefügt haben, wählen Sie **Weiter: Überprüfen + erstellen** aus.

1. Wenn die Meldung „Überprüfung erfolgreich“ angezeigt wird, wählen Sie **Erstellen** aus. 

Alle Ihre Datensammlungsregeln (einschließlich der über die API erstellten Regeln) werden unter **Konfiguration** auf der Connectorseite angezeigt. Dort können Sie vorhandene Regeln bearbeiten oder löschen.

> [!TIP]
> Verwenden Sie zum Testen der Gültigkeit einer XPath-Abfrage das PowerShell-Cmdlet **Get-WinEvent** mit dem Parameter *-FilterXPath*. Das folgende Skript ist ein Beispiel hierfür:
> ```powershell
> $XPath = '*[System[EventID=1035]]'
> Get-WinEvent -LogName 'Application' -FilterXPath $XPath
> ```
> - Werden Ereignisse zurückgegeben, ist die Abfrage gültig.
> - Wenn Sie die Meldung Es wurden keine Ereignisse gefunden, die mit den angegebenen Auswahlkriterien übereinstimmen erhalten, ist die Abfrage möglicherweise gültig, aber es sind keine übereinstimmenden Ereignisse auf dem lokalen Computer vorhanden.
> - Wenn Sie die Meldung Die angegebene Abfrage ist ungültig erhalten, ist die Abfragesyntax ungültig.

### <a name="create-data-collection-rules-using-the-api"></a>Erstellen von Datensammlungsregeln mithilfe der API

Sie können Datensammlungsregeln auch mithilfe der API erstellen ([siehe Schema](/rest/api/monitor/data-collection-rules)), was ihnen das Leben erleichtern kann, wenn Sie viele Regeln erstellen (z. B. wenn Sie ein MSSP sind). Hier sehen Sie ein Beispiel, das Sie als Vorlage zum Erstellen einer Regel verwenden können:

**Anforderungs-URL und -Header**

```http
PUT https://management.azure.com/subscriptions/703362b3-f278-4e4b-9179-c76eaf41ffc2/resourceGroups/myResourceGroup/providers/Microsoft.Insights/dataCollectionRules/myCollectionRule?api-version=2019-11-01-preview
```
**Anforderungs-URL und -Header**

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



---

## <a name="validate-connectivity"></a>Überprüfen der Konnektivität

Es kann etwa 20 Minuten dauern, bis Ihre Protokolle in Log Analytics angezeigt werden. 

### <a name="configure-the-security-events--windows-security-events-connector-for-anomalous-rdp-login-detection"></a>Konfigurieren des Connectors für Sicherheitsereignisse/Windows-Sicherheitsereignisse zur Erkennung ungewöhnlicher RDP-Anmeldungen

> [!IMPORTANT]
> Die Erkennung ungewöhnlicher RDP-Anmeldungen befindet sich derzeit in der öffentlichen Vorschauphase.
> Dieses Feature wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Azure Sentinel kann Machine Learning (ML) auf die Daten von sicherheitsrelevanten Ereignissen anwenden, um ungewöhnliche RDP-Anmeldeaktivitäten (Remotedesktopprotokoll) zu erkennen. Mögliche Szenarien:

- **Ungewöhnliche IP-Adresse**: Die IP-Adresse ist in den letzten 30 Tagen nur selten oder gar nicht aufgetreten.

- **Ungewöhnliche Geolocation**: IP-Adresse, Ort, Land und ASN sind in den letzten 30 Tagen nur selten oder gar nicht aufgetreten.

- **Neuer Benutzer**: Ein neuer Benutzer meldet sich über eine IP-Adresse oder Geolocation an, mit deren Auftreten (einzeln oder gemeinsam) anhand der Daten der letzten 30 Tage nicht gerechnet wurde.

**Konfigurationsanweisungen**

1. Sie müssen RDP-Anmeldedaten (Ereignis-ID 4624) über den Datenconnector für **Sicherheitsereignisse** oder für **Windows-Sicherheitsereignisse** erfassen. Stellen Sie sicher, dass Sie neben „Keine“ einen [Ereignissatz](#event-id-reference) ausgewählt oder eine Datensammlungsregel erstellt haben, die diese Ereignis-ID umfasst, um in Azure Sentinel zu streamen.

1. Wählen Sie im Azure Sentinel-Portal die Option **Analysen** und dann die Registerkarte **Regelvorlagen** aus. Wählen Sie die Regel **(Vorschau) Erkennung anomaler RDP-Anmeldungen** aus, und bewegen Sie den Schieberegler **Status** auf **Aktiviert**.

    > [!NOTE]
    > Da für den Machine Learning-Algorithmus zum Erstellen eines Basisprofils für das Benutzerverhalten die Daten von 30 Tagen benötigt werden, müssen Sie die Erfassung von Windows-Sicherheitsereignissen für 30 Tage zulassen, bevor Incidents erkannt werden können.

## <a name="event-id-reference"></a>Ereignis-ID Referenz

Die folgende Liste bietet eine vollständige Aufschlüsselung der Sicherheits- und AppLocker-Ereignis-IDs für jeden Satz:

| Gruppe von Ereignissen | Erfasste Ereignis-IDs |
| --- | --- |
| **Minimal** | 1102, 4624, 4625, 4657, 4663, 4688, 4700, 4702, 4719, 4720, 4722, 4723, 4724, 4727, 4728, 4732, 4735, 4737, 4739, 4740, 4754, 4755, 4756, 4767, 4799, 4825, 4946, 4948, 4956, 5024, 5033, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222 |
| **Common** | 1, 299, 300, 324, 340, 403, 404, 410, 411, 412, 413, 431, 500, 501, 1100, 1102, 1107, 1108, 4608, 4610, 4611, 4614, 4622, 4624, 4625, 4634, 4647, 4648, 4649, 4657, 4661, 4662, 4663, 4665, 4666, 4667, 4688, 4670, 4672, 4673, 4674, 4675, 4689, 4697, 4700, 4702, 4704, 4705, 4716, 4717, 4718, 4719, 4720, 4722, 4723, 4724, 4725, 4726, 4727, 4728, 4729, 4733, 4732, 4735, 4737, 4738, 4739, 4740, 4742, 4744, 4745, 4746, 4750, 4751, 4752, 4754, 4755, 4756, 4757, 4760, 4761, 4762, 4764, 4767, 4768, 4771, 4774, 4778, 4779, 4781, 4793, 4797, 4798, 4799, 4800, 4801, 4802, 4803, 4825, 4826, 4870, 4886, 4887, 4888, 4893, 4898, 4902, 4904, 4905, 4907, 4931, 4932, 4933, 4946, 4948, 4956, 4985, 5024, 5033, 5059, 5136, 5137, 5140, 5145, 5632, 6144, 6145, 6272, 6273, 6278, 6416, 6423, 6424, 8001, 8002, 8003, 8004, 8005, 8006, 8007, 8222, 26401, 30004 |
|

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie Windows-Sicherheitsereignisse mit Azure Sentinel verbinden. Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit dem Erkennen von Bedrohungen mit Azure Sentinel mithilfe von [integrierten](detect-threats-built-in.md) oder [benutzerdefinierten](detect-threats-custom.md) Regeln.

