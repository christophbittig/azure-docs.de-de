---
title: Verbinden von Azure Sentinel mit Azure-, Windows- und Microsoft-Diensten
description: Erfahren Sie, wie Sie Azure Sentinel mit Azure und Microsoft 365-Clouddiensten sowie mit Windows Server-Ereignisprotokollen verbinden.
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 08/18/2021
ms.author: yelevin
ms.openlocfilehash: c13e2e5ad14ada3c867682382b334242881ddb78
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123261048"
---
# <a name="connect-azure-sentinel-to-azure-windows-microsoft-and-amazon-services"></a>Verbinden von Azure Sentinel mit Azure-, Windows-, Microsoft- und Amazon-Diensten

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Azure Sentinel verwendet Azure als Grundlage, um integrierte Dienst-zu-Dienst-Unterstützung für die Datenerfassung aus zahlreichen Azure- und Microsoft 365-Diensten, Amazon Web Services und verschiedenen Windows Server-Diensten bereitzustellen. Es gibt einige verschiedene Methoden, mit denen diese Verbindungen hergestellt werden, was in diesem Artikel beschrieben wird.

In diesem Artikel werden die folgenden Typen von Connectors behandelt:

- **API-basierte** Verbindungen
- Verbindungen mit **Diagnoseeinstellungen**, von denen einige von Azure Policy verwaltet werden
- **Log Analytics-Agent**-basierte Verbindungen

Dieser Artikel enthält Informationen, die für Gruppen von Connectors gemeinsam zutreffen. Informationen, die für jeden Connector spezifisch sind, finden Sie auf der zugehörigen [Referenzseite des Datenconnectors](data-connectors-reference.md), z. B. Lizenzierungsvoraussetzungen und Log Analytics-Tabellen für die Datenspeicherung.

Die folgenden Integrationen sind spezifischer und beliebter und werden jeweils in einem eigenen Artikeln behandelt:

- [Microsoft 365 Defender](connect-microsoft-365-defender.md)
- [Azure Defender](connect-azure-security-center.md)
- [Azure Active Directory](connect-azure-active-directory.md)
- [Windows-Sicherheitsereignisse](connect-windows-security-events.md)
- [Amazon Web Services (AWS) – CloudTrail](connect-aws.md)


## <a name="api-based-connections"></a>API-basierte Verbindungen

### <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für den Log Analytics-Arbeitsbereich.
- Sie müssen über die Rolle „Globaler Administrator“ oder „Sicherheitsadministrator“ im Mandanten Ihres Azure Sentinel-Arbeitsbereichs verfügen.

### <a name="instructions"></a>Anweisungen

1. Klicken Sie im Azure Sentinel-Navigationsmenü auf **Data connectors** (Datenconnectors).

1. Wählen Sie Ihren Dienst aus dem Katalog der Datenconnectors aus, und wählen Sie dann im Vorschaubereich **Connectorseite öffnen** aus.

1. Wählen Sie **Verbinden** aus, um das Streaming von Ereignissen und/oder Warnungen von Ihrem Dienst in Azure Sentinel zu starten.

1. Wenn auf der Connectorseite ein Abschnitt mit dem Titel **Incidents erstellen – empfohlen!** angezeigt wird, wählen Sie **Aktivieren** aus, wenn Sie aus Warnungen automatisch Incidents erstellen möchten.

Sie können die Daten für jeden Dienst mithilfe der Tabellennamen suchen und abfragen, die im Abschnitt für den Connector des Diensts auf der [Referenzseite für Datenconnectors](data-connectors-reference.md) angezeigt werden.

## <a name="diagnostic-settings-based-connections"></a>Auf Diagnoseeinstellungen basierende Verbindungen

Die Konfiguration einiger Connectors dieses Typs wird von Azure Policy verwaltet. Wählen Sie unten die Registerkarte **Azure Policy** aus, um Anweisungen zu erhalten. Wählen Sie für die anderen Connectors dieses Typs die Registerkarte **Eigenständig** aus.

# <a name="standalone"></a>[Eigenständig](#tab/SA)

### <a name="prerequisites"></a>Voraussetzungen

So erfassen Sie Daten in Azure Sentinel

- Sie benötigen Lese- und Schreibberechtigungen für den Azure Sentinel-Arbeitsbereich.

### <a name="instructions"></a>Anweisungen

1. Klicken Sie im Azure Sentinel-Navigationsmenü auf **Data connectors** (Datenconnectors).

1. Wählen Sie Ihren Ressourcentyp aus dem Katalog der Datenconnectors aus, und wählen Sie dann im Vorschaubereich **Connectorseite öffnen** aus.

1. Wählen Sie auf der Connectorseite im Abschnitt **Konfiguration** den Link aus, um die Ressourcenkonfigurationsseite zu öffnen.

    Wenn eine Liste der Ressourcen des gewünschten Typs angezeigt wird, wählen Sie den Link für eine Ressource aus, deren Protokolle Sie erfassen möchten.

1. Wählen Sie im Navigationsmenü der Ressource **Diagnoseeinstellungen** aus.

1. Wählen Sie am Ende der Liste **+ Diagnoseeinstellung hinzufügen** aus.

1. Geben Sie auf dem Bildschirm **Diagnoseeinstellungen** einen Namen in das Feld **Name der Diagnoseeinstellungen** ein.

    Aktivieren Sie das Kontrollkästchen **An Log Analytics senden**. Zwei neue Felder werden darunter angezeigt. Wählen Sie das relevante **Abonnement** und den **Log Analytics-Arbeitsbereich** (in dem sich Azure Sentinel befindet) aus.

1. Aktivieren Sie die Kontrollkästchen der Protokolltypen und Metriken, die Sie erfassen möchten. Unsere empfohlenen Optionen für jeden Ressourcentyp finden Sie im Abschnitt für den Connector der Ressource auf der [Referenzseite für Datenconnectors](data-connectors-reference.md).

1. Wählen Sie im oberen Bereich des Bildschirms **Speichern** aus.

# <a name="azure-policy"></a>[Azure Policy](#tab/AP)

### <a name="prerequisites"></a>Voraussetzungen

So erfassen Sie Daten in Azure Sentinel

- Sie benötigen Lese- und Schreibberechtigungen für den Azure Sentinel-Arbeitsbereich.

- Wenn Sie Azure Policy verwenden möchten, um eine Richtlinie zum Protokollstreaming auf Ihre Ressourcen anzuwenden, müssen Sie die Rolle „Besitzer“ für den Zuweisungsbereich der Richtlinie besitzen.

### <a name="instructions"></a>Anweisungen

Connectors dieses Typs verwenden Azure Policy, um eine einzelne Diagnoseeinstellungskonfiguration auf eine Sammlung von Ressourcen eines einzelnen Typs anzuwenden, die als Bereich definiert sind. Sie können die Protokolltypen, die von einem bestimmten Ressourcentyp erfasst werden, auf der linken Seite der Connectorseite für diese Ressource unter **Datentypen** anzeigen.

1. Klicken Sie im Azure Sentinel-Navigationsmenü auf **Data connectors** (Datenconnectors).

1. Wählen Sie Ihren Ressourcentyp aus dem Katalog der Datenconnectors aus, und wählen Sie dann im Vorschaubereich **Connectorseite öffnen** aus.

1. Erweitern Sie auf der Connectorseite im Abschnitt **Konfiguration** alle dort angezeigten Expander, und wählen Sie die Schaltfläche **Azure Policy-Zuweisungs-Assistenten starten** aus.

    Der Richtlinienzuweisungs-Assistent wird geöffnet, um eine neue Richtlinie zu erstellen, wobei ein Richtlinienname bereits ausgefüllt ist.

    1. Wählen Sie auf der Registerkarte **Grundlagen** unter **Bereich** die Schaltfläche mit den drei Punkten aus, um Ihr Abonnement (und optional eine Ressourcengruppe) auszuwählen. Sie können auch eine Beschreibung hinzufügen.

    1. In der Registerkarte **Parameter**:
       - Deaktivieren Sie das Kontrollkästchen **Nur Parameter anzeigen, die Eingaben erfordern**.
       - Wenn die Felder **Auswirkung** und **Einstellung** angezeigt werden, lassen Sie diese unverändert.
       - Wählen Sie in der Dropdownliste **Log Analytics-Arbeitsbereich** Ihren Azure Sentinel-Arbeitsbereich aus.
       - Die übrigen Dropdownfelder stellen die verfügbaren Diagnoseprotokolltypen dar. Behalten Sie für alle Protokolltypen, die erfasst werden sollen, die Markierung „True“ bei.

    1. Die Richtlinie wird auf Ressourcen angewendet, die in Zukunft hinzugefügt werden. Wählen Sie zum Anwenden der Richtlinie auf Ihre vorhandenen Ressourcen die Registerkarte **Wartung** aus, und aktivieren Sie das Kontrollkästchen **Wartungstask erstellen**.

    1. Klicken Sie auf der Registerkarte **Überprüfen + erstellen** auf **Erstellen**. Damit ist die Richtlinie dem ausgewählten Bereich zugewiesen.

---

> [!NOTE]
>
> Mit diesem Typ von Datenconnector werden die Konnektivitätsstatusindikatoren (ein Farbstreifen im Datenconnectors-Katalog sowie Verbindungssymbole neben den Datentypnamen) nur dann als *verbunden* (grün) angezeigt, wenn Daten irgendwann innerhalb der letzten 14 Tage erfasst wurden. Wenn 14 Tage ohne Datenerfassung vergangen sind, wird der Connector als „getrennt“ angezeigt. In dem Moment, in dem weitere Daten den Connector passieren, wird der Status wieder als *verbunden* angezeigt.

Sie können die Daten für jeden Ressourcentyp mithilfe der Tabellennamen suchen und abfragen, die im Abschnitt für den Connector der Ressource auf der [Referenzseite für Datenconnectors](data-connectors-reference.md) angezeigt werden.

## <a name="log-analytics-agent-based-connections"></a>Log Analytics-Agent-basierte Verbindungen

### <a name="prerequisites"></a>Voraussetzungen

- Sie benötigen Lese- und Schreibberechtigungen für den Log Analytics-Arbeitsbereich und jeden Arbeitsbereich, der Computer enthält, von denen Sie Protokolle sammeln möchten.
- Zusätzlich zu allen Azure Sentinel-Rollen müssen Sie noch die Rolle **Log Analytics-Mitwirkender** für die SecurityInsights-Lösung (Azure Sentinel) in diesen Arbeitsbereichen besitzen.

### <a name="instructions"></a>Anweisungen

1. Klicken Sie im Azure Sentinel-Navigationsmenü auf **Data connectors** (Datenconnectors).

1. Wählen Sie Ihren Dienst(**DNS** oder **Windows Firewall**) und dann **Connectorseite öffnen** aus.

1. Installieren und registrieren Sie den Agent auf dem Gerät, das die Protokolle generiert.

    | Computertyp  | Anweisungen  |
    | --------- | --------- |
    | **Für einen virtuellen Windows-Computer in Azure** | 1. Erweitern Sie unter **Wählen Sie aus, wo der Agent installiert werden soll** die Option **Agent auf virtuellem Windows-Computer in Azure installieren**. <br><br>2. Wählen Sie den Link **Agent für virtuellen Windows-Computer in Azure herunterladen und installieren >** . <br><br>3. Wählen Sie auf dem Blatt **Virtuelle Computer** eine VM aus, auf der der Agent installiert werden soll, und wählen Sie dann **Verbinden** aus. Wiederholen Sie diesen Schritt für jeden virtuellen Computer, den Sie verbinden möchten. |
    | **Für alle anderen Windows-Computer** | 1. Erweitern Sie unter **Wählen Sie aus, wo der Agent installiert werden soll** die Option **Agent auf Windows-Computer (kein Azure) installieren**. <br><br>2. Klicken Sie auf den Link **Agent für Windows-Computer (kein Azure) herunterladen und installieren**.  <br><br>3. Wählen Sie auf dem Blatt **Agents-Verwaltung** auf der Registerkarte **Windows-Server** je nach Eignung den Link **Windows-Agent herunterladen** für 32-Bit- oder 64-Bit-Systeme aus.      |

1. Wählen Sie die Schaltfläche **Lösung installieren** für DNS oder Windows Firewall aus.

Sie können die Daten für DNS und Windows Firewall mithilfe der Tabellennamen **DnsEvents**, **DnsInventory** und **WindowsFirewall** suchen und abfragen. Sie können diese und andere Informationen zu diesen beiden Dienstconnectors in ihren jeweiligen Abschnitten auf der [Referenzseite für Datenconnectors](data-connectors-reference.md) anzeigen.


## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Sie Azure-, Microsoft- und Windows-Dienste sowie Amazon Web Services mit Azure Sentinel verbinden. 
- Erfahren Sie Allgemeines über [Azure Sentinel-Datenconnectors](connect-data-sources.md).
- [Ermitteln des richtigen Azure Sentinel-Datenconnectors](data-connectors-reference.md).
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](detect-threats-built-in.md).