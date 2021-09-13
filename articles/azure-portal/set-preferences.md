---
title: Verwalten von Einstellungen und Voreinstellungen im Azure-Portal
description: Hier erfahren Sie, wie Sie Einstellungen wie Standardabonnement/-verzeichnis, Timeouts, Menümodus, Kontrast, Design, Benachrichtigungen, Sprache/Region und vieles mehr im Azure-Portal ändern können.
ms.date: 08/10/2021
ms.topic: how-to
ms.openlocfilehash: 285afaaf51f28b0fa53a9df2a9fa232b01d3fe24
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339762"
---
# <a name="manage-azure-portal-settings-and-preferences"></a>Verwalten von Einstellungen und Voreinstellungen im Azure-Portal

Sie können die Standardeinstellungen des Azure-Portals ändern, um sie an Ihre eigenen Vorlieben anzupassen.

Die meisten Einstellungen stehen im Menü **Einstellungen** oben rechts in der globalen Kopfzeile zur Verfügung.

:::image type="content" source="media/set-preferences/settings-top-header.png" alt-text="Screenshot: Symbol „Einstellungen“ in der globalen Kopfzeile":::

> [!NOTE]
> Derzeit sind wir bemüht, alle Benutzer dazu zu bewegen, zur Benutzeroberfläche mit den neuen Einstellungen zu wechseln, die in diesem Thema beschrieben werden. Informationen zur älteren Benutzeroberfläche finden Sie unter [Verwalten von Einstellungen und Voreinstellungen im Azure-Portal (ältere Version)](original-preferences.md).

## <a name="directories--subscriptions"></a>Verzeichnisse und Abonnements

Auf der Seite **Verzeichnisse und Abonnements** können Sie Verzeichnisse verwalten und Abonnementfilter festlegen.

### <a name="switch-and-manage-directories"></a>Wechseln und Verwalten von Verzeichnissen

Im Abschnitt **Verzeichnisse** wird das **aktuelle Verzeichnis** angezeigt (bei dem Sie derzeit angemeldet sind).

Im **Startverzeichnis** wird das Standardverzeichnis bei der Anmeldung beim Azure-Portal angezeigt. Wenn Sie ein anderes Startverzeichnis verwenden möchten, wählen Sie **Ändern** aus, um zur Seite [Appearance + startup views](#appearance--startup-views) (Ansichten „Darstellung“ und „Startup“) zu wechseln. Dort können Sie diese Option ändern.

Wählen Sie **Alle Verzeichnisse** aus, um eine Liste aller Verzeichnisse anzuzeigen, auf die Sie Zugriff haben.

Wenn Sie ein Verzeichnis als Favorit kennzeichnen möchten, wählen Sie das entsprechende Sternsymbol aus. Diese Verzeichnisse werden im Abschnitt **Favoriten** aufgeführt.

Wenn Sie zu einem anderen Verzeichnis wechseln möchten, wählen Sie zuerst das gewünschte Verzeichnis, dann die Schaltfläche **Switch** (Wechseln) in der entsprechenden Zeile aus.

:::image type="content" source="media/set-preferences/settings-directories-subscriptions-default-filter.png" alt-text="Screenshot: Bereich mit Verzeichniseinstellungen":::

### <a name="subscription-filters"></a>Abonnementfilter

Sie können die Abonnements auswählen, die standardmäßig gefiltert werden, wenn Sie sich beim Azure-Portal anmelden. Das kann nützlich sein, wenn Sie mit einem primären Liste mit Abonnements arbeiten, aber gelegentlich andere verwenden.

Wenn Sie angepasste Filter verwenden möchten, wählen Sie **Erweiterte Filter** aus. Sie werden aufgefordert, den Vorgang zu bestätigen, um fortfahren zu können.

:::image type="content" source="media/set-preferences/settings-advanced-filters-enable.png" alt-text="Screenshot: Dialogfeld zur Bestätigung für „Erweiterte Filter“":::

Daraufhin wird die Seite **Erweiterte Filter** aktiviert, auf der Sie mehrere Abonnementfilter erstellen und verwalten können. Alle derzeit ausgewählten Abonnements werden als importierter Filter gespeichert, den Sie wiederverwenden können. Wenn Sie keine erweiterten Filter mehr verwenden möchten, wählen Sie die Umschaltfunktion erneut aus, um die Standardansicht des Abonnements wiederherzustellen. Alle benutzerdefinierten Filter, die Sie erstellt haben, werden gespeichert und sind verfügbar, wenn Sie in Zukunft **Erweiterte Filter** aktivieren.

:::image type="content" source="media/set-preferences/settings-advanced-filters-disable.png" alt-text="Screenshot: Dialogfeld zur Bestätigung für die Deaktivierung von „Erweiterte Filter“":::

## <a name="advanced-filters"></a>Erweiterte Filter

Auf der Seite **Erweiterte Filter** können Sie Abonnementfilter erstellen, ändern oder löschen.

:::image type="content" source="media/set-preferences/settings-advanced-filters.png" alt-text="Screenshot: Bildschirm „Erweiterte Filter“":::

Mit dem Filter **Standard** werden alle Abonnements angezeigt, auf die Sie Zugriff haben. Dieser Filter wird verwendet, wenn es keine anderen Filter gibt oder mit dem aktiven Filter keine Abonnements angezeigt werden.

Möglicherweise wird auch ein Filter mit dem Namen **Imported-filter** (Importierter-Filter) angezeigt. Mit diesem Filter werden alle bereits ausgewählten Abonnements angezeigt.

Wenn Sie den derzeit verwendeten Filter ändern möchten, wählen Sie diesen Filter im Dropdownfeld **Erweiterter Filter** aus. Sie können auch **Modify advanced filters** (Erweiterte Filter ändern) auswählen, um zur Seite **Erweiterte Filter** zu wechseln. Dort können Sie Filter erstellen, ändern und löschen.

### <a name="create-a-filter"></a>Erstellen eines Filters

Wenn Sie einen neuen Filter erstellen möchten, wählen Sie **Create a filter** (Filter erstellen) aus. Sie können bis zu zehn Filter erstellen.

Jeder Filter muss einen eindeutigen Namen haben, der zwischen 8 und 50 Zeichen lang ist und nur Buchstaben, Zahlen und Bindestriche enthält.

:::image type="content" source="media/set-preferences/azure-portal-settings-filtering-create.png" alt-text="Screenshot: Option „Create a filter“ (Filter erstellen)":::

Nachdem Sie Ihrem Filter einen Namen gegeben haben, geben Sie mindestens eine Bedingung ein. Wählen Sie hierzu im Feld **Filtertyp** entweder **Name des Abonnements**, **Abonnement-ID** oder **Abonnementstatus** aus. Wählen Sie anschließend einen Operator aus, und geben Sie einen Wert ein, nach dem gefiltert werden soll.

:::image type="content" source="media/set-preferences/azure-portal-settings-filtering-create-operators.png" alt-text="Screenshot: Liste mit Operatoren für die Filtererstellung":::

Wenn Sie alle gewünschten Bedingungen hinzugefügt haben, wählen Sie **Erstellen** aus. Daraufhin wird Ihr Filter in der Liste in **Active filters** (Aktive Filter) angezeigt.

### <a name="modify-or-delete-a-filter"></a>Ändern oder Löschen eines Filters

Sie können einen vorhandenen Filter ändern oder umbenennen, indem Sie das Bleistiftsymbol in der Zeile des jeweiligen Filters auswählen. Nehmen Sie die gewünschten Änderungen vor, und klicken Sie dann auf **Übernehmen**.

> [!NOTE]
> Wenn Sie einen derzeit aktiven Filter ändern und die Änderungen dazu führen, dass keine Abonnements angezeigt werden, wird stattdessen der **Standardfilter** aktiv. Filter, mit denen keine Abonnements angezeigt werden, können nicht aktiviert werden.

Wenn Sie einen Filter löschen möchten, wählen Sie das Papierkorbsymbol in der Zeile des jeweiligen Filters aus. Der **Standardfilter** kann ebenso wenig gelöscht werden wie der derzeit aktive Filter.

## <a name="appearance--startup-views"></a>Appearance + startup views (Ansichten „Darstellung“ und „Startup“)

Im Bereich **Appearance + startup views (Ansichten „Darstellung“ und „Startup“)** gibt es zwei Abschnitte. Im Abschnitt **Appearance** (Darstellung) können Sie das Menüverhalten, ein Farbschema sowie die Verwendung eines Designs mit hohem Kontrast festlegen. Im Abschnitt **Startup views** (Startup-Ansichten) können Sie Optionen für die Anzeige bei der ersten Anmeldung beim Azure-Portal festlegen.

:::image type="content" source="media/set-preferences/azure-portal-settings-appearance.png" alt-text="Screenshot: Abschnitt „Appearance“ (Darstellung) von „Appearance + startup views“ (Ansichten „Darstellung“ und „Startup“)":::

### <a name="set-menu-behavior"></a>Festlegen des Menüverhaltens

Im Abschnitt **Menüverhalten** können Sie festlegen, wie sich das Standardmenü im Azure-Portal verhalten soll.

- **Flyout**: Das Menü wird erst eingeblendet, wenn Sie es benötigen. Mit dem Menüsymbol in der oberen linken Ecke können Sie das Menü öffnen oder schließen.
- **Angedockt**: Das Menü wird immer angezeigt. Sie können das Menü reduzieren, um den Arbeitsbereich zu vergrößern.

### <a name="choose-a-theme-or-enable-high-contrast"></a>Auswählen eines Designs oder Aktivieren des hohen Kontrasts

Das Design, das Sie auswählen, wirkt sich auf die Hintergrund- und Schriftartfarben aus, die im Azure-Portal angezeigt werden. Im Abschnitt **Design** können Sie eines von vier vordefinierten Farbdesigns auswählen. Wählen Sie die einzelnen Miniaturansichten aus, um das Design zu ermitteln, das Ihnen am besten gefällt.

Alternativ können Sie auch im Abschnitt **Design mit hohem Kontrast** ein Design auswählen. Durch diese Designs wird das Azure-Portal übersichtlicher, insbesondere dann, wenn Sie eine Sehschwäche haben. Wenn Sie das weiße oder schwarze Design mit hohem Kontrast auswählen, wird die Auswahl aller anderen Designs außer Kraft gesetzt.

### <a name="startup-page"></a>Startseite

Wählen Sie eine der folgenden Optionen für die Seite aus, die angezeigt wird, wenn Sie sich zum ersten Mal beim Azure-Portal anmelden.

- **Home**: Zeigt die Startseite mit Verknüpfungen zu beliebten Azure-Diensten, eine Liste mit zuletzt verwendeten Ressourcen und nützliche Links zu Tools, Dokumentationen und vieles mehr an.
- **Dashboard**: Zeigt das zuletzt verwendete Dashboard an. Dashboards können angepasst werden, um einen Arbeitsbereich zu erstellen, der nur für Sie konzipiert wurde. Beispielsweise können Sie ein Dashboard erstellen, das sich auf Projekte, Aufgaben oder Rollen konzentriert. Weitere Informationen finden Sie unter [Erstellen und Freigeben von Dashboards im Azure-Portal](azure-portal-dashboards.md).

### <a name="startup-directory"></a>Startverzeichnis

Wählen Sie eine der folgenden Optionen für das Verzeichnis aus, das verwendet werden soll, wenn Sie sich zum ersten Mal beim Azure-Portal anmelden.

- **Sign in to your last visited directory**(Beim zuletzt besuchten Verzeichnis anmelden): Wenn Sie sich beim Azure-Portal anmelden, gelangen Sie zu dem Verzeichnis, in dem Sie sich zuletzt befunden haben.
- **Select a directory** (Verzeichnis auswählen): Wählen Sie diese Option aus, um ein Verzeichnis auszuwählen. Sie gelangen jedes Mal, wenn Sie sich beim Azure-Portal anmelden, zu diesem Verzeichnis, auch dann, wenn Sie sich zuletzt in einem anderen Verzeichnis befunden haben.

:::image type="content" source="media/set-preferences/azure-portal-settings-startup-views.png" alt-text="Screenshot: Abschnitt „Startup“ (Startup) von „Appearance + startup views“ (Ansichten „Darstellung“ und „Startup“)":::

## <a name="language--region"></a>Sprache und Region

Wählen Sie Ihre Sprache und das regionale Format aus. Damit wird beeinflusst, wie Daten wie Datumsangaben und Währungen im Azure-Portal angezeigt werden.

:::image type="content" source="media/set-preferences/azure-portal-settings-language-region.png" alt-text="Screenshot: Bereich für die Einstellungen „Sprache und Region“":::

> [!NOTE]
> Diese Einstellungen für Sprache und regionales Format wirken sich nur auf das Azure-Portal aus. Bei Dokumentationslinks, die in einer neuen Registerkarte oder in einem neuen Fenster geöffnet werden, werden die Einstellungen Ihres Browsers verwendet, um die anzuzeigende Sprache zu ermitteln.

### <a name="language"></a>Sprache

Verwenden Sie die Dropdownliste, um in der Liste eine verfügbare Sprache auszuwählen. Mit der Einstellung wird die Sprache festgelegt, in der Text im Azure-Portal angezeigt wird.

### <a name="regional-format"></a>Regionales Format

Wählen Sie eine Option aus, um die Anzeige von Datum, Uhrzeit, Zahlen und Währung im Azure-Portal festzulegen.

Welche Optionen in der Dropdownliste **Regionales Format** angezeigt werden, hängt davon ab, welche Option Sie für **Sprache** auswählen. Wenn Sie beispielsweise **Englisch** als Sprache und dann **Englisch (USA)** als regionales Format auswählen, wird die Währung in US-Dollar angezeigt. Wenn Sie **Englisch** als Sprache und dann **Englisch (Europa)** als regionales Format auswählen, wird die Währung in Euro angezeigt.

Wählen Sie **Anwenden** aus, um Ihre Sprache und die Einstellungen für das regionale Format zu aktualisieren.

## <a name="my-information"></a>Eigene Informationen

Auf der Seite **Eigene Informationen** können Sie die E-Mail-Adresse aktualisieren, die für Updates zu Azure-Diensten, Abrechnung, Support und Sicherheitsfragen verwendet wird. Sie können auch weitere E-Mails zu Microsoft Azure und anderen Produkten und Diensten abonnieren oder entsprechende Abonnements kündigen.

Im oberen Bereich der Seite **Eigene Informationen** werden Optionen zum Exportieren, Wiederherstellen oder Löschen von Einstellungen angezeigt.

:::image type="content" source="media/set-preferences/settings-my-information.png" alt-text="Screenshot: Seite mit Einstellungen zu „Eigene Informationen“":::

### <a name="export-user-settings"></a>Exportieren von Benutzereinstellungen

Die Informationen zu Ihren benutzerdefinierten Einstellungen werden in Azure gespeichert. Sie können folgende Benutzerdaten exportieren:

- Private Dashboards im Azure-Portal
- Benutzereinstellungen wie bevorzugte Abonnements oder Verzeichnisse
- Designs und andere benutzerdefinierte Portaleinstellungen

Es empfiehlt sich, Ihre Einstellungen zu exportieren und zu überprüfen, falls Sie sie löschen möchten. Das Neuerstellen von Dashboards oder das erneute Einrichten von Einstellungen kann zeitaufwendig sein.

Zum Exportieren Ihrer Portaleinstellungen wählen Sie oben im Einstellungsbereich **Übersicht** die Option **Einstellungen exportieren** aus. Daraufhin wird eine *JSON*-Datei erstellt, die die Daten zu Ihren Benutzereinstellungen enthält.

Aufgrund der dynamischen Art der Benutzereinstellungen und des Risikos der Datenbeschädigung können Sie keine Einstellungen aus der *JSON*-Datei importieren.

### <a name="restore-default-settings"></a>Standardeinstellungen wiederherstellen

Wenn Sie Änderungen an den Azure-Portaleinstellungen vorgenommen haben und diese verwerfen möchten, wählen Sie oben im Einstellungsbereich **Übersicht** die Option **Standardeinstellungen wiederherstellen** aus. Sie werden aufgefordert, diese Aktion zu bestätigen. Dabei gehen alle Änderungen verloren, die Sie an Ihren Azure-Portaleinstellungen vorgenommen haben. Diese Option wirkt sich nicht auf Dashboardanpassungen aus.

### <a name="delete-user-settings-and-dashboards"></a>Löschen von Benutzereinstellungen und Dashboards

Die Informationen zu Ihren benutzerdefinierten Einstellungen werden in Azure gespeichert. Sie können folgende Benutzerdaten löschen:

- Private Dashboards im Azure-Portal
- Benutzereinstellungen wie bevorzugte Abonnements oder Verzeichnisse
- Designs und andere benutzerdefinierte Portaleinstellungen

Es empfiehlt sich, Ihre Einstellungen zu exportieren und zu überprüfen, bevor Sie sie löschen. Das Neuerstellen von [Dashboards](azure-portal-dashboards.md) oder das erneute Einrichten von benutzerdefinierten Einstellungen kann zeitaufwendig sein.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

Zum Löschen Ihrer Portaleinstellungen wählen Sie im oberen Bereich der Seite **Eigene Informationen** die Option **Alle Einstellungen und private Dashboards löschen** aus. Sie werden aufgefordert, den Löschvorgang zu bestätigen. Dabei werden alle Einstellungsanpassungen auf die Standardeinstellungen zurückgesetzt, und alle Ihre privaten Dashboards gehen verloren.

## <a name="signing-out--notifications"></a>Signing out + notifications (Abmeldung und Benachrichtigungen)

In diesem Bereich können Sie Popupbenachrichtigungen und Sitzungstimeouts verwalten.

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-notifications.png" alt-text="Screenshot: Bereich „Signing out + notifications“ (Abmeldung und Benachrichtigungen)":::

### <a name="signing-out"></a>Abmeldung

Die Einstellung für das Inaktivitätstimeout schützt Ihre Ressourcen vor nicht autorisiertem Zugriff, wenn Sie vergessen, ihre Arbeitsstation abzusichern. Nachdem Sie eine Weile inaktiv waren, werden Sie automatisch von Ihrer Azure-Portalsitzung abgemeldet. Als Einzelbenutzer können Sie die Timeouteinstellung für sich selbst ändern. Wenn Sie ein Administrator sind, können Sie sie auf Verzeichnisebene für alle Benutzer im Verzeichnis festlegen.

### <a name="change-your-individual-timeout-setting-user"></a>Ändern der eigenen Timeouteinstellung (Benutzer)

Wählen Sie im Dropdownmenü neben **Bei Inaktivität abmelden** die Dauer aus, nach der die Azure-Portalsitzung abgemeldet wird, wenn Sie inaktiv sind.

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-inactive.png" alt-text="Screenshot: Option für Benutzertimeouteinstellungen":::

Wählen Sie **Übernehmen** aus, um die Änderungen zu speichern. Wenn Sie danach während der Portalsitzung inaktiv sind, werden Sie beim Azure-Portal nach der von Ihnen festgelegten Dauer abgemeldet.

Wenn Ihr Administrator eine Richtlinie für das Inaktivitätstimeout aktiviert hat, können Sie trotzdem einen eigenen Wert festlegen, sofern dieser kleiner als die Einstellung auf Verzeichnisebene ist. Wählen Sie hierzu **Timeoutrichtlinie bei Verzeichnisinaktivität außer Kraft setzen** aus, und geben Sie für den **Außerkraftsetzungswert** ein Zeitintervall ein.

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-inactive-user.png" alt-text="Screenshot: Einstellung zum Außerkraftsetzen des Timeouts bei Verzeichnisinaktivität":::

### <a name="change-the-directory-timeout-setting-admin"></a>Ändern der Timeouteinstellung auf Verzeichnisebene (Administrator)

Administratoren mit der Rolle [Globaler Administrator](../active-directory/roles/permissions-reference.md#global-administrator) können die maximale Leerlaufzeit erzwingen, nach der eine Sitzung abgemeldet wird. Diese Einstellung für das Inaktivitätstimeout wird auf Verzeichnisebene angewendet. Diese Einstellung ist für neue Sitzungen wirksam. Für Benutzer, die bereits angemeldet sind, wird sie nicht sofort angewandt. Weitere Informationen zu Verzeichnissen finden Sie unter [Übersicht über Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

Wenn Sie ein globaler Administrator sind und eine Einstellung für das Leerlauftimeout für alle Benutzer des Azure-Portals erzwingen möchten, wählen Sie **Enable directory level idle timeout** (Leerlauftimeout auf Verzeichnisebene aktivieren) aus, um die Einstellung zu aktivieren. Geben Sie als Nächstes die **Stunden** und **Minuten** ein, die ein Benutzer maximal inaktiv sein darf, bevor die Sitzung automatisch abgemeldet wird. Nachdem Sie **Übernehmen** ausgewählt haben, wird diese Einstellung für alle Benutzer im Verzeichnis übernommen.

:::image type="content" source="media/set-preferences/azure-portal-settings-sign-out-inactive-admin.png" alt-text="Screenshot: Optionen für das Leerlauftimeout auf Verzeichnisebene":::

Wenn Sie überprüfen möchten, ob die Richtlinie für das Inaktivitätstimeout ordnungsgemäß festgelegt ist, wählen Sie in der globalen Kopfzeile **Benachrichtigungen** aus, und vergewissern Sie sich, dass eine Erfolgsbenachrichtigung aufgeführt ist.

:::image type="content" source="media/set-preferences/confirmation.png" alt-text="Screenshot: Benachrichtigung über erfolgreiche Timeoutrichtlinie bei Inaktivität":::

### <a name="enable-or-disable-pop-up-notifications"></a>Aktivieren oder Deaktivieren von Popupbenachrichtigungen

Benachrichtigungen sind Systemmeldungen, die sich auf Ihre aktuelle Sitzung beziehen. Sie enthalten Informationen wie die Anzeige Ihres aktuellen Guthabens, die Bestätigung Ihrer letzten Aktion oder die Mitteilung, dass von Ihnen erstellte Ressourcen verfügbar werden. Wenn Popupbenachrichtigungen aktiviert sind, werden die Meldungen kurzzeitig in der oberen Ecke des Bildschirms angezeigt.

Wählen Sie **Popupbenachrichtigungen aktivieren** aus oder ab, um Popupbenachrichtigungen zu aktivieren oder zu deaktivieren.

Wenn Sie alle während der aktuellen Sitzung empfangenen Benachrichtigungen lesen möchten, wählen Sie **Benachrichtigungen** aus der globalen Kopfzeile aus.

:::image type="content" source="media/set-preferences/read-notifications.png" alt-text="Screenshot: Symbol „Benachrichtigungen“ in der globalen Kopfzeile":::

Wenn Sie Benachrichtigungen aus vorherigen Sitzungen anzeigen möchten, suchen Sie nach Ereignissen im Aktivitätsprotokoll. Weitere Informationen finden Sie unter [Anzeigen des Aktivitätsprotokolls](../azure-monitor/essentials/activity-log.md#view-the-activity-log).

## <a name="next-steps"></a>Nächste Schritte

- [Erfahren Sie mehr über Tastenkombinationen im Azure-Portal](azure-portal-keyboard-shortcuts.md)
- [Anzeigen von unterstützten Browsern und Geräten](azure-portal-supported-browsers-devices.md)
- [Hinzufügen, Entfernen und Neuanordnen von Favoriten](azure-portal-add-remove-sort-favorites.md)
- [Erstellen und Freigeben von benutzerdefinierten Dashboards](azure-portal-dashboards.md)
- [Ansehen der Anleitungsvideos für das Azure-Portal](azure-portal-video-series.md)
