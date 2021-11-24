---
title: Untersuchen von Vorfällen mit UEBA-Daten | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie bei Untersuchungen mithilfe von UEBA-Daten einen größeren Kontext für potenziell schädliche Aktivitäten in Ihrer Organisation erhalten.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5cf711abf0373c2c7681d71426c0590b9561708e
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522876"
---
# <a name="tutorial-investigate-incidents-with-ueba-data"></a>Tutorial: Untersuchen von Vorfällen mit UEBA-Daten

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

In diesem Artikel werden allgemeine Methoden und Beispielverfahren für die Verwendung der [Benutzer- und Entitätsverhaltensanalyse (User and Entity Behavior Analytics, UEBA)](identify-threats-with-entity-behavior-analytics.md) in Ihren regulären Untersuchungsworkflows beschrieben.

> [!IMPORTANT]
>
> Entsprechend gekennzeichnete Features in diesem Artikel sind derzeit als **VORSCHAUVERSION** verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen, die für Azure-Features gelten, die sich in der Beta- oder Vorschauversion befinden bzw. anderweitig noch nicht zur allgemeinen Verfügbarkeit freigegeben sind.
>

> [!NOTE]
> Dieses Tutorial enthält szenariobasierte Verfahren für eine wichtige Kundenaufgabe: Untersuchungen mit UEBA-Daten. Weitere Informationen finden Sie unter [Untersuchen von Vorfällen mit Microsoft Sentinel](investigate-cases.md).
>
## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie UEBA-Daten in Ihren Untersuchungen verwenden können, müssen Sie die [Benutzer- und Entitätsverhaltensanalyse (User and Entity Behavior Analytics, UEBA) in Microsoft Sentinel aktivieren](enable-entity-behavior-analytics.md).

Etwa eine Woche nach dem Aktivieren von UEBA können Sie mit der Suche nach computergestützten Erkenntnissen beginnen.

## <a name="run-proactive-routine-searches-in-entity-data"></a>Ausführen proaktiver Routinesuchvorgänge in Entitätsdaten

Wir empfehlen, regelmäßig proaktive Suchvorgänge für Benutzeraktivitäten auszuführen, um Leads für weitere Untersuchungen zu erstellen.

Mithilfe der Microsoft Sentinel-Arbeitsmappe [Benutzer- und Entitätsverhaltensanalyse (UEBA)](identify-threats-with-entity-behavior-analytics.md#hunting-queries-and-exploration-queries) können Sie zum Beispiel Daten für folgende Bereiche abfragen:

- **Benutzer mit dem höchsten Risiko**, mit Anomalien oder angefügten Vorfällen
- **Daten zu bestimmten Benutzern**, um zu ermitteln, ob die Person tatsächlich kompromittiert wurde oder ob eine Insiderbedrohung aufgrund einer vom Benutzerprofil abweichenden Aktion besteht.

Erfassen Sie darüber hinaus in der UEBA-Arbeitsmappe nicht routinemäßige Aktionen, und verwenden Sie diese, um nach anomalen Aktivitäten und potenziell nicht konformen Methoden zu suchen.

### <a name="investigate-an-anomalous-sign-in"></a>Untersuchen einer anomalen Anmeldung

Mit den folgenden Schritten führen Sie beispielsweise eine Untersuchung für einen Benutzer aus, der eine Verbindung mit einem zuvor noch nie genutzten VPN hergestellt hat, was eine anomale Aktivität darstellt.

1. Suchen Sie im Azure Sentinel-Bereich **Arbeitsmappen** die Arbeitsmappe **Benutzer- und Entitätsverhaltensanalyse (UEBA)** , und öffnen Sie diese.
1. Suchen Sie nach einem bestimmten Benutzernamen, der untersucht werden soll, und wählen Sie den Namen in der Tabelle **Wichtigste zu untersuchende Benutzer** aus.
1. Scrollen Sie in den Tabellen **Aufschlüsselung von Vorfällen** und **Aufschlüsselung von Anomalien** nach unten, um die Vorfälle und Anomalien anzuzeigen, die mit dem ausgewählten Benutzer verbunden sind.
1. Überprüfen Sie für die jeweilige Anomalie (z. B. **Anomale erfolgreiche Anmeldung**) die in der Tabelle angezeigten Details, um diese zu untersuchen. Beispiel:

    |Schritt  |Beschreibung  |
    |---------|---------|
    |**Beachten Sie die Beschreibung auf der rechten Seite**     |    Jede Anomalie enthält eine Beschreibung mit einem Link, über den Sie weitere Informationen aus der [MITRE ATT&CK-Wissensdatenbank ](https://attack.mitre.org/) abrufen können. <br>Beispiel: <br><br>        **_Initial Access_* _ <br>_The adversary is trying to get into your network.* (Erstzugang_Der Angreifer versucht, in Ihr Netzwerk zu gelangen) <br>* „Initial Access“ (Erstzugang) besteht aus Techniken, die verschiedene Eintrittsvektoren nutzen, um in einem Netzwerk Fuß zu fassen. Zu den Techniken, die verwendet werden, um ins Netzwerk zu gelangen, gehören gezieltes Spear-Phishing und das Ausnutzen von Schwachstellen auf öffentlich zugänglichen Webservern. Ein durch den Erstzugang erworbener Netzwerkzugriff ermöglicht ggf. den fortgesetzten Zugriff (z. B. gültige Konten und die Verwendung externer Remotedienste) oder kann durch das Ändern von Kennwörtern eingeschränkt werden.*     |
    |**Beachten Sie den Text in der Spalte „Beschreibung“**     |   Scrollen Sie in der Zeile „Anomalie“ nach rechts, um eine zusätzliche Beschreibung anzuzeigen. Wählen Sie den Link aus, um den vollständigen Text anzuzeigen. Beispiel: <br><br> *Angreifer stehlen ggf. die Anmeldeinformationen eines bestimmten Benutzers oder Dienstkontos, indem sie Techniken für den Zugriff auf Anmeldeinformationen verwenden oder Anmeldeinformationen vorher bei einem Erkundungsvorgang durch Social Engineering erfassen, um den Erstzugang zu erhalten. APT33 beispielsweise verwendet gültige Konten für den Erstzugang. Mit der folgenden Abfrage wird die Ausgabe einer erfolgreichen Anmeldung generiert, die von einem Benutzer von einem neuen geografischen Standort aus durchgeführt wurde, von dem er und seine Kollegen noch nie eine Verbindung hergestellt haben.*     |
    |**Beachten Sie die UsersInsights-Daten**     |  Scrollen Sie in der Zeile „Anomalie“ weiter nach rechts, um die UserInsights-Daten (z. B. Anzeigename des Kontos und Kontoobjekt-ID) anzuzeigen. Wählen Sie den Text aus, um die vollständigen Daten auf der rechten Seite anzuzeigen.         |
    |**Beachten Sie die Beweisdaten**     |  Scrollen Sie in der Zeile „Anomalie“ weiter nach rechts, um die Beweisdaten für die Anomalie anzuzeigen. Wählen Sie den Text aus, um die vollständigen Daten auf der rechten Seite anzuzeigen, z. B. die folgenden Felder: <br><br>-  **ActionUncommonlyPerformedByUser** <br>- **UncommonHighVolumeOfActions** <br>- **FirstTimeUserConnectedFromCountry** <br>- **CountryUncommonlyConnectedFromAmongPeers** <br>- **FirstTimeUserConnectedViaISP** <br>- **ISPUncommonlyUsedAmongPeers** <br>- **CountryUncommonlyConnectedFromInTenant** <br>- **ISPUncommonlyUsedInTenant** |
    |     |         |

Ermitteln Sie mithilfe der Daten in der Arbeitsmappe **Benutzer- und Entitätsverhaltensanalyse (UEBA)** , ob die Benutzeraktivität verdächtig ist und weitere Maßnahmen erfordert.

## <a name="use-ueba-data-to-analyze-false-positives"></a>Verwenden von UEBA-Daten zum Analysieren falsch positiver Ergebnisse

Manchmal ist ein in einer Untersuchung erfasster Vorfall falsch positiv.

Ein häufiges Beispiel für falsch positive Ergebnisse sind Reiseaktivitäten, die als unmöglich erkannt werden (z. B. bei einem Benutzer, der sich innerhalb einer Stunde bei einer Anwendung oder einem Portal in New York und London angemeldet). Auch wenn Microsoft Sentinel den unmöglichen Ortswechsel als Anomalie erkennt, kann eine Untersuchung des Benutzers klären, dass ein VPN mit einem anderen Standort als dem tatsächlichen Standort des Benutzers verwendet wurde.

### <a name="analyze-a-false-positive"></a>Analysieren eines falsch positiven Ergebnisses

Navigieren Sie beispielsweise bei einem Vorfall des Typs **Unmöglicher Ortswechsel**, nachdem Sie mit dem Benutzer geklärt haben, dass ein VPN verwendet wurde, von der Vorfallsseite zur Benutzerentitätsseite. Verwenden Sie die dort angezeigten Daten, um zu ermitteln, ob die erfassten Standorte in den allgemein bekannten Standorten des Benutzers enthalten sind.

Beispiel:

[ ![Öffnen Sie die Benutzerentitätsseite für einen Vorfall.](media/ueba/open-entity-pages.png) ](media/ueba/open-entity-pages.png#lightbox)

Die Benutzerentitätsseite ist auch mit der [Vorfallsseite](investigate-cases.md#how-to-investigate-incidents) selbst und dem [Untersuchungsdiagramm](investigate-cases.md#use-the-investigation-graph-to-deep-dive) verknüpft.

> [!TIP]
> Nachdem Sie die Daten auf der Benutzerentitätsseite für den speziellen Benutzer bestätigt haben, der dem Vorfall zugeordnet ist, wechseln Sie zum Microsoft Sentinel-Bereich **Hunting**, um zu erfahren, ob auch die Kollegen des Benutzers in der Regel von den gleichen Standorten aus eine Verbindung herstellen. Wenn das der Fall ist, wäre dieses Wissen ein noch stärkerer Hinweis auf ein falsch positives Ergebnis.
>
> Führen Sie im Bereich **Hunting** die Abfrage **Anmeldung von anomalen geografischen Standorten** aus. Weitere Informationen finden Sie unter [Suchen nach Bedrohungen mit Microsoft Sentinel](hunting.md).
>

### <a name="embed-identityinfo-data-in-your-analytics-rules-public-preview"></a>Einbetten von IdentityInfo-Daten in Ihre Analyseregeln (Public Preview)

Da Angreifer häufig die eigenen Benutzer- und Dienstkonten der Organisation verwenden, sind Daten über diese Benutzerkonten wie etwa Benutzeridentifikation und Berechtigungen für die Analysten bei der Untersuchung von entscheidender Bedeutung.

Betten Sie Daten aus der **Tabelle „IdentityInfo“** ein, um die Analyseregeln für Ihre Anwendungsfälle zu optimieren, falsch positive Ergebnisse zu verringern und ggf. den Untersuchungsprozess zu beschleunigen.

Zum Beispiel:

- Gehen Sie wie folgt vor, um Sicherheitsereignisse mit der Tabelle **IdentityInfo** in einer Warnung zu korrelieren, die ausgelöst wird, wenn jemand außerhalb der **IT-Abteilung** auf einen Server zugreift:

    ```kusto
    SecurityEvent
    | where EventID in ("4624","4672")
    | where Computer == "My.High.Value.Asset"
    | join kind=inner  (
        IdentityInfo
        | summarize arg_max(TimeGenerated, *) by AccountObjectId) on $left.SubjectUserSid == $right.AccountSID
    | where Department != "IT"
    ```

- Gehen Sie wie folgt vor, um Azure AD-Anmeldeprotokolle mit der Tabelle **IdentityInfo** in einer Warnung zu korrelieren, die ausgelöst wird, wenn jemand, der kein Mitglied einer bestimmten Sicherheitsgruppe ist, auf eine Anwendung zugreift:

    ```kusto
    SigninLogs
    | where AppDisplayName == "GithHub.Com"
    | join kind=inner  (
        IdentityInfo
        | summarize arg_max(TimeGenerated, *) by AccountObjectId) on $left.UserId == $right.AccountObjectId
    | where GroupMembership !contains "Developers"
    ```

Die Tabelle **IdentityInfo** wird mit Ihrem Azure AD-Arbeitsbereich synchronisiert, um eine Momentaufnahme Ihrer Benutzerprofildaten zu erstellen. Diese umfassen beispielsweise Benutzermetadaten, Gruppeninformationen und Azure AD-Rollen, die den einzelnen Benutzern zugewiesen sind. Weitere Informationen finden Sie in der Referenz zu UEBA-Anreicherungen unter [Tabelle „IdentityInfo“](ueba-enrichments.md#identityinfo-table-public-preview).

## <a name="identify-password-spray-and-spear-phishing-attempts"></a>Identifizieren von Kennwortspray- und Spear-Phishing-Versuchen

Wenn die mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) nicht aktiviert ist, sind Benutzeranmeldeinformationen anfällig für Angreifer, die mit [Kennwortspray-](https://www.microsoft.com/security/blog/2020/04/23/protecting-organization-password-spray-attacks/) oder [Spear-Phishing-Versuchen](https://www.microsoft.com/security/blog/2019/12/02/spear-phishing-campaigns-sharper-than-you-think/) Angriffe zur Kennwortkompromittierung durchführen möchten.

### <a name="investigate-a-password-spray-incident-with-ueba-insights"></a>Untersuchen eines Kennwortspray-Vorfalls mit UEBA-Erkenntnissen

Wenn Sie beispielsweise einen Kennwortspray-Vorfall mit UEBA-Erkenntnissen untersuchen möchten, könnten Sie folgende Schritte ausführen, um mehr zu erfahren:

1. Wählen Sie im Vorfall unten links die Option **Untersuchen** aus, um die Konten, Computer und anderen Datenpunkte anzuzeigen, die möglicherweise Ziel eines Angriffs waren.

    Beim Durchsuchen der Daten wird möglicherweise ein Administratorkonto mit einer relativ großen Anzahl von Anmeldefehlern angezeigt. Auch wenn dies verdächtig ist, möchten Sie das Konto möglicherweise nicht ohne weitere Bestätigung einschränken.

1. Wählen Sie in der Zuordnung die Entität des Administratorbenutzers und dann auf der rechten Seite **Erkenntnisse** aus, um nach weiteren Details (z. B. Diagramm der Anmeldungen im Zeitverlauf) zu suchen.

1. Wählen Sie auf der rechten Seite **Info** und dann **Vollständige Details anzeigen** aus, um zur [Benutzerentitätsseite](identify-threats-with-entity-behavior-analytics.md#entity-pages) zu wechseln und einen weiteren Drilldown auszuführen. 

    Achten Sie beispielsweise darauf, ob dies der erste potenzielle Kennwortspray-Vorfall des Benutzers ist, oder beobachten Sie den Anmeldeverlauf des Benutzers, um zu erkennen, ob die Fehler anomal waren.

> [!TIP]
> Sie können auch die [Hunting-Abfrage](hunting.md) **Anomale fehlgeschlagene Anmeldung** ausführen, um alle ungewöhnlichen fehlgeschlagenen Anmeldungen in einer Organisation zu überwachen. Verwenden Sie die Ergebnisse der Abfrage, um Untersuchungen zu möglichen Kennwortspray-Angriffen zu starten.
>

## <a name="url-detonation-public-preview"></a>URL-Detonation (Public Preview)

Wenn die in Microsoft Sentinel erfassten Protokolle URLs enthalten, werden diese URLs automatisch detoniert, um die Selektierung zu beschleunigen. 

Der Untersuchungsgraph enthält einen Knoten für die detonierte URL sowie die folgenden Details:

- **DetonationVerdict**: Die übergeordnete boolesche Einordnung der Detonation. **Schlecht** bedeutet beispielsweise, dass die Seite als Host für Schadsoftware oder als Phishinginhalt klassifiziert wurde.
- **DetonationFinalURL**: Die endgültige beobachtete Landing Page-URL nach allen Umleitungen von der ursprünglichen URL.
- **DetonationScreenshot**: Ein Screenshot der Seite zum Zeitpunkt der Warnungsauslösung. Wählen Sie den Screenshot aus, um ihn zu vergrößern.

Zum Beispiel:

:::image type="content" source="media/investigate-with-ueba/url-detonation-example.png" alt-text="Beispiel für eine im Untersuchungsgraphen angezeigte URL-Detonation":::

> [!TIP]
> Sollten in Ihren Protokollen keine URLs angezeigt werden, überprüfen Sie, ob die URL-Protokollierung (auch Bedrohungsprotokollierung genannt) für Ihre sicheren Webgateways, Webproxys, Firewalls oder Legacy-IDS/IPS aktiviert ist.
>
> Sie können auch benutzerdefinierte Protokolle erstellen, um bestimmte relevante URLs zur weiteren Untersuchung an Microsoft Sentinel weiterzuleiten.
>

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu UEBA, Untersuchungen und Hunting finden Sie hier:

- [Erkennen komplexerer Bedrohungen mit User and Entity Behavior Analytics (UEBA) in Microsoft Sentinel](identify-threats-with-entity-behavior-analytics.md)
- [Referenz zu Microsoft Sentinel-UEBA-Anreicherungen](ueba-enrichments.md)
- [Tutorial: Untersuchen von Incidents mit Microsoft Sentinel](investigate-cases.md)
- [Suchen nach Bedrohungen mit Microsoft Sentinel](hunting.md)
