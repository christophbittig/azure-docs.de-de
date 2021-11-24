---
title: Verwenden von Hunting-Lesezeichen für Datenuntersuchungen in Microsoft Sentinel
description: In diesem Artikel ist beschrieben, wie Microsoft Sentinel-Suchlesezeichen verwendet werden können, um den Überblick über Daten zu behalten.
author: yelevin
ms.author: yelevin
manager: rkarlin
ms.assetid: 320ccdad-8767-41f3-b083-0bc48f1eeb37
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: 7ddfa01141ac95b78371d8fbe0a239e09057481a
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524738"
---
# <a name="keep-track-of-data-during-hunting-with-microsoft-sentinel"></a>Daten während des Huntings mit Microsoft Sentinel verfolgen

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Eine Bedrohungssuche erfordert in der Regel die Überprüfung sehr großer Mengen von Protokolldaten, um nach Beweisen für bösartiges Verhalten zu suchen. Während dieses Prozesses finden Ermittler Ereignisse, die sie sich merken, erneut besuchen und analysieren möchten, um mögliche Hypothesen zu validieren und die ganze Geschichte einer Gefährdung zu verstehen.

Hunting-Lesezeichen in Microsoft Sentinel helfen Ihnen dabei, indem sie die Abfragen, die Sie in **Microsoft Sentinel – Logs** ausgeführt haben, sowie die Abfrageergebnisse, die Sie für relevant halten, konservieren. Sie können auch Ihre kontextbezogenen Beobachtungen aufzeichnen und Ihre Ergebnisse durch Hinzufügen von Anmerkungen und Tags referenzieren. Mit Lesezeichen markierte Daten sind für Sie und Ihre Teamkollegen zur einfachen Zusammenarbeit sichtbar.

Sie können Sie Lücken in Bezug auf die Abdeckung der MITRE ATT&CK-Techniken für alle Hunting-Abfragen nun identifizieren und schließen, indem Sie Ihre benutzerdefinierten Hunting-Abfragen den MITRE ATT&CK-Techniken zuordnen.

> [!IMPORTANT]
>
> Die Zuordnung von MITRE ATT&CK-Techniken zu Lesezeichen befindet sich derzeit in der **VORSCHAUPHASE**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.

Sie können beim Hunting mit Lesezeichen auch weitere Arten von Entitäten untersuchen, indem Sie den vollständigen Satz an Entitätstypen und Bezeichnern, die von Microsoft Sentinel Analytics unterstützt werden, in Ihren benutzerdefinierten Abfragen zuordnen. Auf diese Weise können Sie Lesezeichen nutzen, um die Entitäten, die in den Ergebnissen von Hunting-Abfragen zurückgegeben werden, über [Entitätsseiten](entities-in-azure-sentinel.md#entity-pages), [Incidents](investigate-cases.md) und das [Untersuchungsdiagramm](investigate-cases.md#use-the-investigation-graph-to-deep-dive) zu untersuchen. Wenn über ein Lesezeichen die Ergebnisse einer Hunting-Abfrage erfasst werden, werden die Zuordnungen der MITRE ATT&CK-Techniken und Entitäten automatisch geerbt.

> [!IMPORTANT]
>
> Die Zuordnung eines erweiterten Satzes mit Entitätstypen und Bezeichnern zu Lesezeichen befindet sich derzeit in der **VORSCHAUPHASE**. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

Wenn Sie während des Hunting-Vorgangs in Ihren Protokollen auf ein Problem stoßen, das dringend behandelt werden muss, können Sie einfach ein Lesezeichen erstellen und das Problem entweder auf einen Incident hochstufen oder einem vorhandenen Incident hinzufügen. Weitere Informationen zu Incidents finden Sie unter [Untersuchen von Vorfällen mit Microsoft Sentinel](investigate-cases.md).

Wenn Sie ein Problem finden, für das Sie ein Lesezeichen vergeben möchten, aber keine unmittelbare Dringlichkeit besteht, können Sie wie folgt vorgehen: Erstellen Sie ein Lesezeichen, damit Sie auf diese gekennzeichneten Daten auf der Seite **Hunting** über die Registerkarte **Lesezeichen** jederzeit erneut zugreifen können. Sie können Filter- und Suchoptionen verwenden, um bestimmte Daten für Ihre aktuelle Untersuchung schnell zu finden.

Sie können Ihre mit Lesezeichen versehenen Daten visualisieren, indem Sie in den Lesezeichendetails die Option **Untersuchen** auswählen. Dadurch wird der Untersuchungsvorgang gestartet, in dem Sie Ihre Ergebnisse mithilfe eines interaktiven Entität-Grafikdiagramms und einer Zeitachse anzeigen, untersuchen und visuell kommunizieren können.

Alternativ können Sie Ihre mit Lesezeichen markierten Daten direkt in der Tabelle **HuntingBookmark** in Ihrem Log Analytics-Arbeitsbereich anzeigen. Beispiel:

:::image type="content" source="media/bookmarks/bookmark-table.png" alt-text="Screenshot: Anzeigen der Tabelle mit Hunting-Lesezeichen" lightbox="media/bookmarks/bookmark-table.png":::

Durch Anzeigen von Lesezeichen aus der Tabelle können Sie mit Lesezeichen markierte Daten filtern, zusammenfassen und mit anderen Datenquellen verknüpfen, wodurch die Suche nach fundierten Beweisen vereinfacht wird.

## <a name="add-a-bookmark"></a>Hinzufügen eines Lesezeichens

1. Navigieren Sie im Azure-Portal zu **Microsoft Sentinel** > **Bedrohungsverwaltung** > **Hunting**, um Abfragen zu verdächtigem und ungewöhnlichem Verhalten auszuführen.

1. Wählen Sie eine der Hunting-Abfragen und dann rechts in den Details der Hunting-Abfrage die Option **Abfrage ausführen** aus.

1. Wählen Sie **Abfrageergebnisse anzeigen** aus. Beispiel:

    :::image type="content" source="media/bookmarks/new-processes-observed-example.png" alt-text="Screenshot: Anzeigen von Abfrageergebnissen für Microsoft Sentinel-Hunting":::

    Diese Aktion öffnet die Abfrageergebnisse im Bereich **Protokolle**.

1. Verwenden Sie in der Liste mit den Protokollabfrageergebnissen die Kontrollkästchen, um eine oder mehrere Zeilen auszuwählen, in denen die für Sie interessanten Informationen enthalten sind.

1. Wählen Sie **Lesezeichen hinzufügen** aus:

    :::image type="content" source="media/bookmarks/add-hunting-bookmark.png" alt-text="Screenshot: Hinzufügen eines Hunting-Lesezeichens zu einer Abfrage" lightbox="media/bookmarks/add-hunting-bookmark.png":::

1. Aktualisieren Sie optional im rechten Bereich **Lesezeichen hinzufügen** den Lesezeichennamen, und fügen Sie Tags und Anmerkungen hinzu, damit Sie später erkennen können, was an dem Element interessant war.

1. **(Vorschau)** Lesezeichen können optional MITRE ATT&CK-Techniken bzw. -Untertechniken zugeordnet werden. MITRE ATT&CK-Zuordnungen werden von zugeordneten Werten in Hunting-Abfragen geerbt, aber Sie können sie auch manuell erstellen. Wählen Sie die MITRE ATT&CK-Taktik, die der gewünschten Technik zugeordnet ist, im Bereich **Lesezeichen hinzufügen** im Abschnitt **Taktiken und Techniken (Vorschau)** im Dropdownmenü aus. Das Menü wird erweitert, und es werden alle MITRE ATT&CK-Techniken angezeigt. In diesem Menü können Sie mehrere Techniken und Untertechniken auswählen.

    :::image type="content" source="media/bookmarks/mitre-attack-mapping.png" alt-text="Screenshot: Zuordnen von MITRE ATT&CK-Taktiken und -Techniken zu Lesezeichen":::

1. **(Vorschau)** Sie können jetzt einen erweiterten Satz mit Entitäten aus mit Lesezeichen versehenen Abfrageergebnissen extrahieren, um die weitere Untersuchung zu ermöglichen. Verwenden Sie im Abschnitt **Entitätszuordnung (Vorschau)** die Dropdownlisten, um [Entitätstypen und Bezeichner](entities-reference.md) auszuwählen. Ordnen Sie anschließend die Spalte in den Abfrageergebnissen zu, die den entsprechenden Bezeichner enthält. Beispiel:

    :::image type="content" source="media/bookmarks/map-entity-types-bookmark.png" alt-text="Screenshot: Zuordnen von Entitätstypen für Hunting-Lesezeichen":::

    Um das Lesezeichen im Untersuchungsdiagramm anzeigen zu können, müssen Sie mindestens eine Entität zuordnen. Entitätszuordnungen für die Entitätstypen „Konto“, „Host“, „IP-Adresse“ und „URL“, die vor dieser Vorschauphase erstellt wurden, werden weiterhin unterstützt, und die Abwärtskompatibilität bleibt erhalten.

1. Klicken Sie auf **Speichern**, um Ihre Änderungen zu übernehmen und das Lesezeichen hinzuzufügen. Alle mit Lesezeichen markierten Daten werden für andere Analysten freigegeben. Dies ist ein erster Schritt zur Schaffung einer Umgebung für gemeinsame Untersuchungen.

> [!NOTE]
> Die Ergebnisse der Protokollabfrage unterstützen Lesezeichen, wenn dieser Bereich von Microsoft Sentinel aus geöffnet wird. Sie wählen z. B. **Allgemein** > **Protokolle** auf der Navigationsleiste, dann Ereignislinks im Untersuchungsdiagramm oder eine Warnungs-ID aus den vollständigen Details eines Incidents aus (derzeit in der Vorschauversion). Sie können keine Lesezeichen erstellen, wenn der Bereich **Protokolle** von anderen Positionen aus geöffnet wird, z. B. direkt über Azure Monitor.

## <a name="view-and-update-bookmarks"></a>Anzeigen und Aktualisieren von Lesezeichen

1. Navigieren Sie im Azure-Portal zu **Microsoft Sentinel** > **Bedrohungsverwaltung** > **Hunting**.

2. Wählen Sie die Registerkarte **Lesezeichen** aus, um die Liste der Lesezeichen anzuzeigen.

3. Um Ihnen bei der Suche nach einem bestimmten Lesezeichen zu helfen, verwenden Sie das Suchfeld oder die Filteroptionen.

4. Wählen Sie einzelne Lesezeichen aus, um die Lesezeichendetails im rechten Detailbereich anzuzeigen.

5. Nehmen Sie bei Bedarf Ihre Änderungen vor, die automatisch gespeichert werden.

## <a name="exploring-bookmarks-in-the-investigation-graph"></a>Untersuchen von Lesezeichen im Untersuchungsdiagramm

1. Navigieren Sie im Azure-Portal zur Registerkarte **Microsoft Sentinel** > **Bedrohungsverwaltung** > **Hunting** > **Lesezeichen**, und wählen Sie das bzw. die Lesezeichen aus, die Sie untersuchen möchten.

2. Stellen Sie in den Lesezeichendetails sicher, dass mindestens eine Entität zugeordnet ist.

3. Wählen Sie die Option **Untersuchen** aus, um das Lesezeichen im Untersuchungsdiagramm anzuzeigen.

Anweisungen zur Verwendung des Untersuchungsdiagramms finden Sie unter [Ausführliche Untersuchung mit dem Untersuchungsdiagramm](investigate-cases.md#use-the-investigation-graph-to-deep-dive).

## <a name="add-bookmarks-to-a-new-or-existing-incident"></a>Hinzufügen von Lesezeichen zu einem neuen oder bestehenden Incident

1. Navigieren Sie im Azure-Portal zur Registerkarte **Microsoft Sentinel** > **Bedrohungsverwaltung** > **Hunting** > **Lesezeichen**, und wählen Sie das bzw. die Lesezeichen aus, die Sie zu einem Incident hinzufügen möchten.

2. Wählen Sie auf der Befehlsleiste die Option **Incidentaktionen** aus:

    :::image type="content" source="media/bookmarks/incident-actions.png" alt-text="Screenshot: Hinzufügen von Lesezeichen zu einem Incident":::

3. Wählen Sie entweder **Neuen Incident erstellen** oder **Zum vorhandenem Incident hinzufügen** aus. Führen Sie dann folgende Schritte aus:

    - Für einen neuen Incident: Aktualisieren Sie optional die Details für den Incident, und wählen Sie dann **Erstellen** aus.
    - Zum Hinzufügen eines Lesezeichens zu einem vorhandenen Incident: Wählen Sie einen Incident und anschließend **Hinzufügen** aus.

Zeigen Sie das Lesezeichen im Incident wie folgt an: Navigieren Sie zu **Microsoft Sentinel** > **Bedrohungsmanagement** > **Incidents**, und wählen Sie den Incident mit Ihrem Lesezeichen aus. Wählen Sie **Alle Informationen anzeigen** und dann die Registerkarte **Lesezeichen** aus.

> [!TIP]
> Als Alternative zur Option **Incidentaktionen** auf der Befehlsleiste können Sie das Kontextmenü ( **...** ) jeweils für Lesezeichen verwenden, um die Optionen **Neuen Incident erstellen**, **Zu vorhandenem Incident hinzufügen** und **Aus Incident entfernen** auszuwählen.

## <a name="view-bookmarked-data-in-logs"></a>Anzeigen von mit Lesezeichen markierten Daten in Protokollen

Wählen Sie das Lesezeichen auf der Registerkarte **Hunting** > **Lesezeichen** aus, um die mit Lesezeichen versehenen Abfragen, Ergebnisse oder deren Verlauf anzuzeigen, und verwenden Sie die im Detailbereich angegebenen Links:

- **Quellabfrage anzeigen**, um die Quellabfrage im Bereich **Protokolle** anzuzeigen.

- **Lesezeichenprotokolle anzeigen**, um alle Lesezeichenmetadaten anzuzeigen, z. B. die Person, die die Aktualisierung vorgenommen hat, die aktualisierten Werte und die Uhrzeit, zu der die Aktualisierung erfolgt ist.

Sie können auch die unformatierten Lesezeichendaten für alle Lesezeichen anzeigen, indem Sie **Lesezeichenprotokolle** auf der Registerkarte **Hunting** > **Lesezeichen** auf der Befehlsleiste auswählen:

:::image type="content" source="media/bookmarks/bookmark-logs.png" alt-text="Screenshot: Option „Lesezeichenprotokolle“":::

In dieser Ansicht werden alle Ihre Lesezeichen mit den zugehörigen Metadaten angezeigt. Sie können KQL-Abfragen ([Kusto Query Language](/azure/data-explorer/kql-quick-reference)) verwenden, um nach der neuesten Version des gesuchten Lesezeichens zu filtern.

> [!NOTE]
> Es kann eine erhebliche Verzögerung (gemessen in Minuten) zwischen der Erstellung eines Lesezeichens und dem Zeitpunkt liegen, ab dem es auf der Registerkarte **Lesezeichen** angezeigt wird.

## <a name="delete-a-bookmark"></a>Löschen eines Lesezeichens

1. Navigieren Sie im Azure-Portal zur Registerkarte **Microsoft Sentinel** > **Bedrohungsverwaltung** > **Hunting** > **Lesezeichen**, und wählen Sie das bzw. die Lesezeichen aus, die Sie löschen möchten.

2. Klicken Sie mit der rechten Maustaste auf Ihre Auswahl, und wählen Sie die Option zum Löschen der ausgewählten Lesezeichen aus.

Das Löschen eines Lesezeichens bewirkt, dass das Lesezeichen aus der Liste auf der Registerkarte **Lesezeichen** (Bookmarks) entfernt wird. In der Tabelle **HuntingBookmark** für Ihren Log Analytics-Arbeitsbereich sind frühere Lesezeicheneinträge weiter enthalten, aber für den letzten Eintrag wird der Wert von **SoftDelete** in „true“ geändert, um das Filtern nach alten Lesezeichen zu erleichtern. Das Löschen eines Lesezeichens bewirkt nicht, dass irgendwelche Entitäten aus den Untersuchungsvorgängen entfernt werden, die mit anderen Lesezeichen oder Benachrichtigungen verknüpft sind.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie eine Bedrohungsuntersuchung mithilfe von Lesezeichen in Microsoft Sentinel ausführen. Weitere Informationen über Microsoft Sentinel finden Sie in den folgenden Artikeln:

- [Proactively hunt for threats (Proaktive Ermittlung von Bedrohungen)](hunting.md)
- [Verwenden von Notebooks zur Ermittlung von Anomalien](notebooks.md)
- [Bedrohungssuche mit Microsoft Sentinel (Learn-Modul)](/learn/modules/hunt-threats-sentinel/)
