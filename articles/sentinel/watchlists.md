---
title: Verwenden von Microsoft Sentinel-Watchlists
description: In diesem Artikel wird beschrieben, wie Sie mithilfe von Microsoft Sentinel-Watchlists Positivlisten/Sperrlisten erstellen, Ereignisdaten anreichern und bei der Untersuchung von Bedrohungen helfen.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: 54fd6bb3b1d2c937364db4f1883f0919342e116d
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522078"
---
# <a name="use-microsoft-sentinel-watchlists"></a>Verwenden von Microsoft Sentinel-Watchlists

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Sentinel-Watchlists ermöglichen die Erfassung von Daten aus externen Datenquellen, um sie mit Ereignissen in Ihrer Microsoft Sentinel-Umgebung zu korrelieren. Nach der Erstellung können Sie Watchlists in Ihrer Suche, für Erkennungsregeln, bei der Bedrohungssuche sowie in Playbooks für die Reaktion auf Bedrohungen verwenden. Watchlists werden in Ihrem Microsoft Sentinel-Arbeitsbereich als Name/Wert-Paare gespeichert und für optimale Abfrageleistung und geringe Latenz zwischengespeichert.

> [!IMPORTANT]
> Entsprechend gekennzeichnete Features sind derzeit als VORSCHAUVERSION verfügbar. In den [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) finden Sie weitere rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden oder anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

Häufige Szenarien für die Verwendung von Watchlists umfassen Folgendes:

- **Untersuchen von Bedrohungen** und schnelles Reagieren auf Incidents, indem IP-Adressen, Dateihashes und anderen Daten schnell aus CSV-Dateien importiert werden. Nach dem Importieren können Sie Name-Wert-Paare der Watchlist zum Verknüpfen und Filtern in Warnungsregeln, bei der Bedrohungssuche, in Arbeitsmappen, in Notebooks und für allgemeine Abfragen verwenden.

- **Importieren von Geschäftsdaten** als Watchliste. Importieren Sie z. B. Benutzerlisten mit privilegiertem Systemzugriff oder ehemaligen Mitarbeitern, und verwenden Sie dann die Watchlist, um Positivlisten und Sperrlisten zu erstellen, mit denen eine Anmeldung dieser Benutzer beim Netzwerk erkannt oder verhindert wird.

- **Reduzieren von Warnungsmüdigkeit**. Erstellen Sie Positivlisten, um Warnungen von einer Gruppe von Benutzern zu unterdrücken (z. B. Benutzer von autorisierten IP-Adressen, die Aufgaben ausführen, durch die normalerweise die Warnung ausgelöst würde) und zu verhindern, dass zulässige Ereignisse zu Warnungen führen.

- **Erweitern von Ereignisdaten**. Verwenden Sie Watchlists, um Ihre Ereignisdaten mit Name-Wert-Kombinationen zu erweitern, die aus externen Datenquellen stammen.

> [!NOTE]
> - Die Verwendung von Watchlists sollte auf Verweisdaten beschränkt werden, da sie nicht für große Datenvolumen konzipiert sind.
>
> - Die **Gesamtzahl der aktiven Watchlistelemente** in allen Watchlists in einem einzelnen Arbeitsbereich ist derzeit auf **10 Millionen** beschränkt. Gelöschte Watchlistelemente werden auf diese Summe nicht angerechnet. Wenn Sie die Möglichkeit benötigen, auf große Datenvolumen zu verweisen, erwägen Sie stattdessen die Erfassung dieser Daten mithilfe [benutzerdefinierter Protokolle](../azure-monitor/agents/data-sources-custom-logs.md).
>
> - Auf Watchlists kann nur aus demselben Arbeitsbereich verwiesen werden. Arbeitsbereichsübergreifende bzw. Lighthouse-Szenarien werden derzeit nicht unterstützt.

## <a name="create-a-new-watchlist"></a>Erstellen einer neuen Watchlist

1. Navigieren Sie im Azure-Portal zu **Microsoft Sentinel** > **Konfiguration** > **Watchlist**, und wählen Sie dann **+ Neu hinzufügen** aus.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new.png" alt-text="Neue Watchlist" lightbox="./media/watchlists/sentinel-watchlist-new.png":::

1. Geben Sie auf der Seite **Allgemein** den Namen, die Beschreibung und den Alias für die Watchlist an, und wählen Sie dann **Weiter: Quelle** aus.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-general.png" alt-text="Seite „Allgemein“ für die Watchlist":::

1. Wählen Sie auf der Seite **Quelle** den Datasettyp aus (derzeit ist nur CSV verfügbar), geben Sie die Anzahl der Zeilen **vor der Kopfzeile** in Ihrer Datendatei ein, und wählen Sie dann eine Datei aus, die auf eine von zwei Arten hochgeladen werden soll:
    1. Klicken Sie auf den Link **Nach Dateien suchen** im Feld **Datei hochladen**, und wählen Sie Ihre hochzuladende Datendatei aus.
    1. Ziehen Sie Ihe Datendatei auf das Feld **Datei hochladen**.

    Auf dem Bildschirm des Assistenten wird eine Vorschau der ersten 50 Ergebniszeilen angezeigt.

1. Geben Sie in das Feld **SearchKey** den Namen einer Spalte in Ihrer Watchlist ein, die Sie als Verknüpfung (Join) mit anderen Daten oder als häufiges Suchobjekt verwenden möchten. Wenn Ihre Serverwatchlist z. B. Ländernamen und deren entsprechende Ländercodes aus zwei Buchstaben enthält und Sie erwarten, dass Sie die Ländercodes häufig für Suchen oder Verknüpfungen verwenden werden, verwenden Sie die Spalte **Code** als „SearchKey“.

1. <a name="review-and-create"></a>Wählen Sie **Weiter: Überprüfen und erstellen** aus.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-source.png" alt-text="Seite „Quelle“ für die Watchlist" lightbox="./media/watchlists/sentinel-watchlist-source.png":::

    > [!NOTE]
    >
    > Dateiuploads sind aktuell auf Dateien mit einer Größe von bis zu 3,8 MB beschränkt.

1. Sehen Sie sich die Informationen an, überprüfen Sie, ob sie korrekt sind, warten Sie die Meldung *Validierung erfolgreich* ab, und wählen Sie dann **Erstellen** aus.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-review.png" alt-text="Überprüfungsseite für die Watchlist":::

    Sobald die Watchlist erstellt wurde, wird eine Benachrichtigung angezeigt.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-complete.png" alt-text="Benachrichtigung über erfolgreiche Erstellung der Watchlist" lightbox="./media/watchlists/sentinel-watchlist-complete.png":::


## <a name="create-a-new-watchlist-using-a-template-public-preview"></a>Erstellen einer neuen Watchlist mithilfe einer Vorlage (Öffentliche Vorschau)

1. Navigieren Sie im Azure-Portal zu **Microsoft Sentinel** > **Konfiguration** > **Watchlist** > **Vorlagen (Vorschau)** .

1. Wählen Sie eine Vorlage aus der Liste aus, um ihre Details auf der rechten Seite anzuzeigen, und wählen Sie dann **Aus Vorlage erstellen** aus, um Ihre Watchlist zu erstellen.

    :::image type="content" source="./media/watchlists/create-watchlist-from-template.png" alt-text="Erstellen einer Watchlist aus einer integrierten Vorlage." lightbox="./media/watchlists/create-watchlist-from-template.png":::

1. Fahren Sie im **Watchlist-Assistenten** fort:

    - Bei Verwendung einer Watchlistvorlage sind die Werte von **Name**, **Beschreibung** und **Watchlistalias** der Watchlist alle schreibgeschützt.

    - Wählen Sie **Schema herunterladen** aus, um eine CSV-Datei herunterzuladen, die das relevante Schema enthält, das für die ausgewählte Watchlistvorlage erwartet wird.

    Jede integrierte Watchlistvorlage verfügt über einen eigenen Satz von Daten, die in der CSV-Datei aufgeführt sind, die an die Vorlage angefügt ist. Weitere Informationen finden Sie unter [Integrierte Watchlistschemas](watchlist-schemas.md).

1.  Füllen Sie Ihre lokale Version der CSV-Datei auf, und laden Sie sie dann wieder in den Assistenten hoch.

1. Fahren Sie so fort, als ob Sie [eine ganz neue Watchlist erstellen](#review-and-create), und verwenden Sie dann Ihre Watchlist mit [Abfragen](#use-watchlists-in-queries) und [Analyseregeln](#use-watchlists-in-analytics-rules).

## <a name="use-watchlists-in-queries"></a>Verwenden von Watchlists in Abfragen

> [!TIP]
> Um eine optimale Abfrageleistung zu erzielen, verwenden Sie **SearchKey** (der das Feld darstellt, das Sie beim Erstellen der Watchlist definiert haben) als Schlüssel für Verknüpfungen (Joins) in Ihren Abfragen. Betrachten Sie das folgende Beispiel.

1. Navigieren Sie im Azure-Portal zu **Microsoft Sentinel** > **Konfiguration** > **Watchlist**, wählen Sie die gewünschte Watchlist aus, und wählen Sie dann **In Log Analytics anzeigen** aus.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-list.png" alt-text="Verwenden von Watchlists in Abfragen" lightbox="./media/watchlists/sentinel-watchlist-queries-list.png" :::

1. Die Elemente in Ihrer Watchlist werden automatisch für Ihre Abfrage extrahiert und auf der Registerkarte **Ergebnisse** angezeigt. Im folgenden Beispiel werden die Ergebnisse der Extraktion der Felder **Name** und **IP-Adresse** angezeigt. Der **SearchKey** wird als eigene Spalte angezeigt.

    > [!NOTE]
    > Der Zeitstempel Ihrer Abfragen wird sowohl auf der Benutzeroberfläche der Abfrage als auch in geplanten Benachrichtigungen ignoriert.

    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-fields.png" alt-text="Abfragen mit Feldern der Watchlist" lightbox="./media/watchlists/sentinel-watchlist-queries-fields.png":::

1. Sie können für die Daten einer Tabelle eine Abfrage für die Daten aus einer Watchlist durchführen, indem Sie die Watchlist für Verknüpfungs- und Suchvorgänge wie eine Tabelle behandeln. Verwenden Sie **SearchKey** als Schlüssel für Ihre Verknüpfung.

    ```kusto
    Heartbeat
    | lookup kind=leftouter _GetWatchlist('mywatchlist') 
     on $left.RemoteIPCountry == $right.SearchKey
    ```
    :::image type="content" source="./media/watchlists/sentinel-watchlist-queries-join.png" alt-text="Abfragen für Watchlist zu Suchzwecken" lightbox="./media/watchlists/sentinel-watchlist-queries-join.png":::

## <a name="use-watchlists-in-analytics-rules"></a>Verwenden von Watchlists in Analyseregeln

> [!TIP]
> Um eine optimale Abfrageleistung zu erzielen, verwenden Sie **SearchKey** (der das Feld darstellt, das Sie beim Erstellen der Watchlist definiert haben) als Schlüssel für Verknüpfungen (Joins) in Ihren Abfragen. Betrachten Sie das folgende Beispiel.

Um Watchlists in Analyseregeln zu verwenden, navigieren Sie im Azure-Portal zu **Microsoft Sentinel** > **Konfiguration** > **Analysen**, und erstellen Sie eine Regel mithilfe der Funktion `_GetWatchlist('<watchlist>')` in der Abfrage.

1. In diesem Beispiel erstellen Sie eine Watchlist mit dem Namen „ipwatchlist“, die die folgenden Werte enthält:

    :::image type="content" source="./media/watchlists/create-watchlist.png" alt-text="Liste mit vier Elementen für die Watchlist":::

    :::image type="content" source="./media/watchlists/sentinel-watchlist-new-other.png" alt-text="Erstellen einer Watchlist mit vier Elementen":::

1. Als Nächstes erstellen Sie die Analyseregel.  In diesem Beispiel fügen wir nur Ereignisse von IP-Adressen in die Watchlist ein:

    ```kusto
    //Watchlist as a variable
    let watchlist = (_GetWatchlist('ipwatchlist') | project IPAddress);
    Heartbeat
    | where ComputerIP in (watchlist)
    ```
    ```kusto
    //Watchlist inline with the query
    //Use SearchKey for the best performance
    Heartbeat
    | where ComputerIP in ( 
        (_GetWatchlist('ipwatchlist')
        | project SearchKey)
    )
    ```

    :::image type="content" source="./media/watchlists/sentinel-watchlist-analytics-rule.png" alt-text="Verwenden von Watchlists in Analyseregeln":::

## <a name="view-list-of-watchlists-aliases"></a>Anzeigen der Liste von Watchlist-Aliasen

Um eine Liste von Watchlist-Aliasen abzurufen, navigieren Sie im Azure-Portal zu **Microsoft Sentinel** > **Allgemein** > **Protokolle**, und führen Sie die Abfrage `_GetWatchlistAlias` aus. Die Liste der Aliase wird auf der Registerkarte **Ergebnisse** angezeigt.

   :::image type="content" source="./media/watchlists/sentinel-watchlist-alias.png" alt-text="Auflisten von Watchlists" lightbox="./media/watchlists/sentinel-watchlist-alias.png":::

## <a name="manage-your-watchlist-in-the-microsoft-sentinel-portal"></a>Verwalten Ihrer Watchlist im Microsoft Sentinel-Portal

Sie können neue Watchlistelemente auch direkt über das Blatt „Watchlist“ im Microsoft Sentinel-Portal anzeigen, bearbeiten und erstellen.

1. Um Ihre Watchlist zu bearbeiten, navigieren Sie zu **Microsoft Sentinel > Konfiguration > Watchlist**, wählen Sie die Watchlist aus, die Sie bearbeiten möchten, und wählen Sie im Bereich „Details“ die Option **Watchlistelemente bearbeiten** aus.

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit.png" alt-text="Screenshot, der das Bearbeiten einer Watchlist zeigt." lightbox="./media/watchlists/sentinel-watchlist-edit.png":::

1. Um ein vorhandenes Watchlistelement zu bearbeiten, aktivieren Sie das Kontrollkästchen dieses Watchlistelements, bearbeiten Sie das Element, und wählen Sie **Speichern** aus. Wählen Sie bei der Aufforderung zur Bestätigung **Ja** aus.

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit-change.png" alt-text="Screenshot, der das Markieren und Bearbeiten eines Watchlistelements zeigt.":::

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit-confirm.png" alt-text="Screenshot: Bestätigen Ihrer Änderungen.":::

1. Um Ihrer Watchlist ein neues Element hinzuzufügen, wählen Sie **Neues hinzufügen** im Bildschirm **Watchlistelemente bearbeiten** aus, tragen Sie die Felder im Bereich **Watchlistelement hinzufügen** ein, und wählen Sie unten im Bereich **Hinzufügen** aus.

   :::image type="content" source="./media/watchlists/sentinel-watchlist-edit-add.png" alt-text="Screenshot, der zeigt, wie Sie Ihrer Watchlist ein neues Element hinzufügen.":::

## <a name="next-steps"></a>Nächste Schritte
In diesem Dokument haben Sie erfahren, wie Sie Watchlists in Microsoft Sentinel verwenden, um Daten zu erweitern und Untersuchungen zu verbessern. Weitere Informationen über Microsoft Sentinel finden Sie in den folgenden Artikeln:
- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](get-visibility.md).
- Beginnen Sie mit [Erkennung von Bedrohungen mithilfe von Microsoft Sentinel](./detect-threats-built-in.md).
- [Verwenden Sie Arbeitsmappen](monitor-your-data.md), um Ihre Daten zu überwachen.
