---
title: Anpassen von Aktivitäten auf Microsoft Sentinel-Entitätenzeitachsen | Microsoft-Dokumentation
description: Hinzufügen von benutzerdefinierten Aktivitäten zu den von Microsoft Sentinel nachverfolgten und auf den Zeitachsen der Entitätsseiten angezeigten Aktivitäten
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0483d839af7a8707aa3be632f9d168447488e768
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523617"
---
# <a name="customize-activities-on-entity-page-timelines"></a>Anpassen von Aktivitäten auf den Zeitachsen der Entitätsseite

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - Die Aktivitätsanpassung befindet sich in der **Vorschauphase**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

## <a name="introduction"></a>Einführung

Zusätzlich zu den Aktivitäten, die von Microsoft Sentinel standardmäßig nachverfolgt und auf der Zeitachse angezeigt werden, können Sie beliebige andere Aktivitäten zur Nachverfolgung erstellen und diese ebenfalls auf der Zeitachse anzeigen lassen. Sie können die benutzerdefinierten Aktivitäten auf der Grundlage von Entitätsdatenabfragen aus beliebigen verbundenen Datenquellen erstellen. Die folgenden Beispiele zeigen, wie Sie diese Funktion verwenden können:

- Fügen Sie der Entitätenzeitachse neue Aktivitäten hinzu, indem Sie die vorhandenen Aktivitätsvorlagen anpassen.

- Fügen Sie neue Aktivitäten aus benutzerdefinierten Protokollen hinzu. Aus einem Protokoll der physischen Zugriffssteuerung können Sie beispielsweise das Betreten und das Verlassen eines bestimmten Gebäudes zur Zeitachse des jeweiligen Benutzers hinzufügen.

## <a name="getting-started"></a>Erste Schritte

1. Wählen Sie im Navigationsmenü von Microsoft Sentinel **Entity Behavior** aus.

1. Wählen Sie auf dem Blatt **Entitätsverhalten** die Option **Entitätsseite anpassen** am oberen Bildschirmrand aus.

    :::image type="content" source="./media/customize-entity-activities/entity-behavior-blade.png" alt-text="Seite für Entitätsverhalten":::

1. Auf der Registerkarte **Meine Aktivitäten** wird Ihnen eine Liste aller Aktivitäten angezeigt, die Sie erstellt haben. Auf der Registerkarte **Aktivitätsvorlagen** sehen Sie eine Auflistung aller Aktivitäten, die von den Microsoft-Sicherheitsexperten standardmäßig angeboten werden. Dies sind die Aktivitäten, die bereits nachverfolgt und auf den Zeitachsen Ihrer Entitätsseiten angezeigt werden.

    > [!NOTE]
    > - Solange Sie keine benutzerdefinierten Aktivitäten erstellt haben, werden auf Ihren Entitätsseiten alle Aktivitäten angezeigt, die auf der Registerkarte **Aktivitätsvorlagen** aufgeführt sind.
    >
    > - Nachdem Sie eine einzelne benutzerdefinierte Aktivität definiert haben, werden auf Ihren Entitätsseiten **nur** die Aktivitäten angezeigt, die auf der Registerkarte **Meine Aktivitäten** aufgeführt sind.
    >
    > - Wenn Sie die vordefinierten Aktivitäten weiterhin auf Ihren Entitätsseiten anzeigen möchten, müssen Sie eine Aktivität für jede Vorlage erstellen, die nachverfolgt und angezeigt werden soll. Befolgen Sie die nachstehenden Anweisungen unter „Erstellen einer Aktivität aus einer Vorlage“.

## <a name="create-an-activity-from-a-template"></a>Erstellen einer Aktivität aus einer Vorlage

1. Klicken Sie auf die Registerkarte **Aktivitätsvorlagen**, um sich die verschiedenen standardmäßig verfügbaren Aktivitäten anzeigen zu lassen. Sie können die Liste nach Entitätstyp und nach Datenquelle filtern. Wenn Sie eine Aktivität aus der Liste auswählen, werden im Vorschaubereich die folgenden Details angezeigt:

    -  eine Beschreibung der Aktivität

    - die Datenquelle, die die Ereignisse zur Verfügung stellt, aus denen sich die Aktivität zusammensetzt

    - die Bezeichner, die zum Identifizieren der Entität in den Rohdaten verwendet werden

    - die Abfrage, mit der die Aktivität erkannt wird

1. Klicken Sie unten im Vorschaubereich auf die Schaltfläche **Aktivität erstellen**, um den Assistenten für die Erstellung der Aktivität zu starten.

    :::image type="content" source="./media/customize-entity-activities/activity-details.png" alt-text="Anzeigen von Aktivitätsdetails":::

1. Der **Aktivitätsassistent – Neue Aktivität aus Vorlage erstellen** wird geöffnet, wobei die Felder bereits aus der Vorlage aufgefüllt wurden. Sie können nach Bedarf Änderungen auf den Registerkarten **Allgemein** und **Aktivitätskonfiguration** vornehmen oder alle Einstellungen übernehmen, um die vorgefertigte Aktivität weiterhin anzuzeigen.

1. Wenn Sie zufrieden sind, wählen Sie die Registerkarte **Überprüfen und erstellen** aus. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, klicken Sie unten auf die Schaltfläche **Erstellen**.

## <a name="create-an-activity-from-scratch"></a>Erstellen einer Aktivität ohne Vorlage

Klicken Sie oben auf der Aktivitätsseite auf **Aktivität hinzufügen**, um den Assistenten für die Erstellung der Aktivität zu starten.

Der **Aktivitätsassistent – Neue Aktivität erstellen** wird geöffnet. Die enthaltenen Felder sind dabei zunächst leer.

### <a name="general-tab"></a>Registerkarte "Allgemein"
1. Geben Sie einen Namen für Ihre Aktivität ein (Beispiel: „Benutzer zur Gruppe hinzugefügt“).

1. Geben Sie eine Beschreibung der Aktivität ein (Beispiel: „Änderung der Benutzergruppenmitgliedschaft basierend auf der Windows-Ereignis-ID 4728“).

1. Wählen Sie den Entitätstyp (Benutzer oder Host) aus, der mit dieser Abfrage nachverfolgt wird.

1. Sie können nach zusätzlichen Parametern filtern, um die Abfrage zu verfeinern und ihre Leistung zu optimieren. Sie können beispielsweise nach Active Directory-Benutzern filtern, indem Sie den **IsDomainJoined**-Parameter auswählen und den Wert auf **True** festlegen.

1. Sie können den Anfangsstatus der Aktivität entweder auf **Aktiviert** oder auf **Deaktiviert** festlegen.

1. Wählen Sie **Weiter: Aktivitätskonfiguration** aus, um mit der nächsten Registerkarte fortzufahren.

    :::image type="content" source="./media/customize-entity-activities/create-new-activity.png" alt-text="Screenshot: Erstellen einer neuen Aktivität":::

### <a name="activity-configuration-tab"></a>Registerkarte „Aktivitätskonfiguration“

#### <a name="writing-the-activity-query"></a>Schreiben der Aktivitätsabfrage

Hier schreiben oder fügen Sie die KQL-Abfrage ein, die verwendet wird, um die Aktivität für die ausgewählte Entität zu erkennen, und um zu bestimmen, wie sie auf der Zeitachse dargestellt wird.

Um Ereignisse zu korrelieren und die benutzerdefinierte Aktivität zu erkennen, erfordert die KQL je nach Entitätstyp die Eingabe mehrerer Parameter. Die Parameter sind die verschiedenen Bezeichner der betreffenden Entität.

Es ist besser, einen starken Bezeichner auszuwählen, um eine 1:1-Zuordnung von Abfrageergebnissen und Entitäten zu erhalten. Die Auswahl eines schwachen Bezeichners kann zu ungenauen Ergebnissen führen. [Weitere Informationen über Entitäten und starke und schwache Bezeichner](entities-in-azure-sentinel.md).

Die folgende Tabelle enthält Informationen zu den Bezeichnern einer Entität.

**Starke Bezeichner für Konto- und Hostentitäten**

Eine Abfrage muss mindestens einen Bezeichner enthalten.

| Entität | Bezeichner | BESCHREIBUNG |
| - | - | - |
| **Konto** | Account_Sid | Die lokale SID des Active Directory-Kontos. |
| | Account_AadUserId | Die Azure AD-Objekt-ID des Benutzers in Azure Active Directory. |
| | Account_Name + Account_NTDomain | Ähnlich wie SamAccountName (Beispiel: Contoso\Joe) |
| | Account_Name + Account_UPNSuffix | Ähnlich wie UserPrincipalName (Beispiel: Joe@Contoso.com) |
| **Host** | Host_HostName + Host_NTDomain | Ähnlich wie der vollqualifizierte Domänenname (Fully Qualified Domain Name, FQDN) |
| | Host_HostName + Host_DnsDomain | Ähnlich wie der vollqualifizierte Domänenname (Fully Qualified Domain Name, FQDN) |
| | Host_NetBiosName + Host_NTDomain | Ähnlich wie der vollqualifizierte Domänenname (Fully Qualified Domain Name, FQDN) |
| | Host_NetBiosName + Host_DnsDomain | Ähnlich wie der vollqualifizierte Domänenname (Fully Qualified Domain Name, FQDN) |
| | Host_AzureID | Die Azure AD-Objekt-ID des Hosts in Azure Active Directory (wenn dieser in die AAD-Domäne eingebunden ist) |
| | Host_OMSAgentID | Die OMS-Agent-ID des Agents, der auf einem bestimmten Host installiert ist (einzigartig pro Host) |
|

Basierend auf der von Ihnen ausgewählten Entität werden die verfügbaren Bezeichner angezeigt. Wenn Sie auf den relevanten Bezeichner klicken, wird dieser an der Position des Cursors in die Abfrage eingefügt.

> [!NOTE]
> - Die Abfrage kann **bis zu 10 Felder** enthalten, daher müssen Sie die gewünschten Felder projizieren.
>
> - Die projizierten Felder müssen das Feld **TimeGenerated** beinhalten, damit die erkannte Aktivität auf der Zeitachse der Entität platziert wird.

```kusto
SecurityEvent
| where EventID == "4728"
| where (SubjectUserSid == '{{Account_Sid}}' ) or (SubjectUserName == '{{Account_Name}}' and SubjectDomainName == '{{Account_NTDomain}}' )
| project TimeGenerated, SubjectUserName, MemberName, MemberSid, GroupName=TargetUserName
```

:::image type="content" source="./media/customize-entity-activities/new-activity-query.png" alt-text="Screenshot: Eingeben einer Abfrage zum Erkennen der Aktivität":::

#### <a name="presenting-the-activity-in-the-timeline"></a>Darstellen der Aktivität auf der Zeitachse

Der Einfachheit halber sollten Sie festlegen, wie die Aktivität auf der Zeitachse dargestellt wird, indem Sie der Aktivitätsausgabe dynamische Parameter hinzufügen.

Microsoft Sentinel stellt vorgefertigte Parameter zur Nutzung bereit, und Sie können auch andere verwenden, die auf den in der Abfrage projizierten Feldern beruhen.

Verwenden Sie für Ihre Parameter das folgende Format: `{{ParameterName}}`

Nachdem die Aktivitätsabfrage die Überprüfung erfolgreich durchlaufen hat und der Link **Abfrageergebnisse anzeigen** unterhalb des Abfragefensters angezeigt wird, können Sie den Abschnitt **Verfügbare Werte** erweitern, um die verfügbaren Parameter anzuzeigen, die Sie beim Erstellen eines dynamischen Aktivitätstitels verwenden können.

Klicken Sie neben einem bestimmten Parameter auf das Symbol **Kopieren**, um diesen Parameter in die Zwischenablage zu kopieren. Anschließend können Sie ihn in das obige Feld **Aktivitätstitel** einfügen.

Fügen Sie Ihrer Abfrage einen der folgenden Parameter hinzu:

- Felder, die Sie in die Abfrage projiziert haben.

- Entitätsbezeichner für in der Abfrage erwähnte Entitäten.

- `StartTimeUTC`, um die Startzeit der Aktivität in UTC hinzuzufügen.

- `EndTimeUTC`, um die Endzeit der Aktivität in UTC hinzuzufügen.

- `Count`, um mehrere KQL-Abfrageausgaben in einer einzigen Ausgabe zusammenzufassen.

    Der Parameter `count` fügt der Abfrage im Hintergrund den folgenden Befehl hinzu, er wird allerdings nicht vollständig im Editor angezeigt:

    ```kql
    Summarize count() by <each parameter you’ve projected in the activity>
    ```

    Wenn Sie dann den Filter **Bucketgröße** auf den Entitätsseiten verwenden, wird der Abfrage im Hintergrund außerdem auch folgender Befehl hinzugefügt:

    ```kql
    Summarize count() by <each parameter you’ve projected in the activity>, bin (TimeGenerated, Bucket in Hours)
    ```

Zum Beispiel:

:::image type="content" source="./media/customize-entity-activities/new-activity-title.png" alt-text="Screenshot: Anzeigen der verfügbaren Werte für Ihren Aktivitätstitel":::

Wenn Sie mit Ihrer Abfrage und dem Aktivitätstitel zufrieden sind, wählen Sie **Weiter: Überprüfen** aus.

### <a name="review-and-create-tab"></a>Registerkarte „Überprüfen und erstellen“

1. Überprüfen Sie alle Konfigurationsinformationen Ihrer benutzerdefinierten Aktivität.

1. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, klicken Sie auf **Erstellen**, um die Aktivität zu erstellen. Sie können sie später auf der Registerkarte **Meine Aktivitäten** bearbeiten oder ändern.

## <a name="manage-your-activities"></a>Verwalten Ihrer Aktivitäten 

Verwalten Sie Ihre benutzerdefinierten Aktivitäten auf der Registerkarte **Meine Aktivitäten**. Klicken Sie auf die Auslassungspunkte (...) am Ende der Zeile einer Aktivität, um:

- die Aktivität zu bearbeiten.
- die Aktivität zu duplizieren, um eine neue, angepasste Aktivität zu erstellen.
- die Aktivität zu löschen.
- die Aktivität zu deaktivieren (ohne sie zu löschen).

## <a name="view-activities-in-an-entity-page"></a>Anzeigen von Aktivitäten auf einer Entitätsseite

Jedes Mal, wenn Sie eine Entitätsseite aufrufen, werden alle aktivierten Aktivitätsabfragen für diese Entität ausgeführt. Auf diese Weise erhalten Sie minutengenaue Informationen auf der Entitätenzeitachse. Die Aktivitäten werden zusammen mit Warnungen und Lesezeichen auf der Zeitachse angezeigt. 

Sie können den Filter **Zeitachseninhalt** anwenden, um nur Aktivitäten (oder eine beliebige Kombination von Aktivitäten, Warnungen und Lesezeichen) anzeigen zu lassen.  

Sie können ebenfalls den Filter **Aktivitäten** nutzen, um bestimmte Aktivitäten ein- oder auszublenden. 

## <a name="next-steps"></a>Nächste Schritte

In diesem Dokument haben Sie Informationen darüber erhalten, wie Sie benutzerdefinierte Aktivitäten für die Zeitachsen Ihrer Entitätsseiten erstellen. Weitere Informationen über Microsoft Sentinel finden Sie in den folgenden Artikeln:
- Umfassende Informationen über [Entitätsseiten](identify-threats-with-entity-behavior-analytics.md).
- Hier finden Sie eine vollständige Liste mit [Entitäten und Bezeichnern](entities-reference.md).
