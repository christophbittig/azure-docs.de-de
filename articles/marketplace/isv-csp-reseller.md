---
title: Konfigurieren Sie private Angebote von ISV zu Cloud Reseller im Microsoft Partner Center
description: Konfigurieren Sie private Angebote von ISVs für Cloud Reseller im Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emerb19
ms.author: emerb
ms.date: 10/07/2021
robots: noindex
ms.openlocfilehash: bb663f42be1e7d1c798420ef483cf52c34c84aff
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131048290"
---
# <a name="isv-to-cloud-reseller-private-offers"></a>Private ISV-zu-Cloud-Reseller-Angebote

> [!IMPORTANT]
> Dieses Feature ist zurzeit als öffentliche Preview verfügbar.

## <a name="overview"></a>Übersicht

Private Angebote ermöglichen es unabhängigen Softwareanbietern (ISVs) und Cloud-Wiederverkäufern (CSP-Partnern), ihre Umsätze zu steigern, indem sie zeitlich begrenzte, maßgeschneiderte Margen erstellen, die den geschäftlichen Anforderungen des jeweiligen Unternehmens entsprechen.

Als ISV können Sie die Marge und die Dauer festlegen, um einen Großhandelspreis für Ihren CSP-Partner zu erstellen. Wenn Ihr Partner einen Verkauf an einen Kunden tätigt, wird Microsoft seine Zahlungen an Sie auf der Grundlage des Großhandelspreises tätigen.

Als CSP-Partner können Sie alle Margen, die Ihnen zur Verfügung stehen, über die Partner Center UI oder API einsehen. Für jeden Verkauf, den Sie tätigen, erhalten Sie Ihre Rechnung von Microsoft zum Großhandelspreis. Sie legen weiterhin Ihren Kundenpreis fest und stellen Ihren Kunden die Rechnung außerhalb des Marktplatzes. Weitere Informationen über die CSP-Partnererfahrung für private Angebote finden Sie unter [Entdecken Sie von ISVs konfigurierte Margen](/partner-center/csp-commercial-marketplace-margins).

:::image type="content" source="media/isv-csp-cloud/isv-private-offer-experience.png" alt-text="Zeigt die Entwicklung der ISV-Erfahrung mit privaten Angeboten ":::.

:::image type="content" source="media/isv-csp-cloud/csp-private-offer-experience.png" alt-text="Zeigt den Verlauf der Erfahrung mit privaten Angeboten von CSPs.":::

> [!NOTE]
> Wenn Sie Ihr Angebot nur auf Microsoft AppSource veröffentlichen (es ist also nicht über Azure Marketplace verfügbar) und es für den Verkauf durch Partner im Cloud Solution Provider (CSP)-Programm ausgewählt haben, kann es zu einer Verzögerung bei der Anzeige Ihres Angebots für CSP-Partner kommen, die es in ihrem Portal erwerben können. Bitte wenden Sie sich an [Support](./support.md), wenn Sie der Meinung sind, dass Ihr Angebot für Ihre CSP-Partner nicht verfügbar ist.

## <a name="prerequisites-to-create-a-private-offer-for-cloud-resellers"></a>Voraussetzungen für die Erstellung eines privaten Angebots für Cloud Reseller

Sie müssen diese Voraussetzungen erfüllen, um ein privates Angebot für Cloud-Wiederverkäufer zu erstellen:

1. Sie haben ein kommerzielles Marktplatzkonto im Partner Center erstellt.
2. Ihr Konto ist für das kommerzielle Marktplatzprogramm angemeldet.
3. Sie haben ein transaktionsfähiges und öffentlich verfügbares Angebot auf dem Azure Marketplace veröffentlicht.
4. Ihr Angebot ist für den Cloud Reseller-Channel optiert.
5. Sie erstellen ein privates Angebot für einen Cloud Reseller, der Teil des Cloud Solution Provider (CSP) Programms von Microsoft ist.

### <a name="supported-offer-types"></a>Unterstützte Angebotstypen

Private Angebote können für alle transaktionsfähigen Marketplace-Angebotstypen erstellt werden. Dazu gehören SaaS, Azure Virtual Machines und Azure Applications.

> [!NOTE]
> Margen werden auf alle benutzerdefinierten Zählerdimensionen angewendet, die Ihr Angebot verwenden kann. Die Margen werden nur auf die von Ihnen festgelegten Softwaregebühren angewandt, nicht auf die zugehörigen Azure-Infrastruktur-Hardwaregebühren.

## <a name="private-offers-dashboard"></a>Dashboard für private Angebote

Das Dashboard **Private Angebote** im linken Navigationsmenü des Partner Centers ist Ihr zentraler Ort zum Erstellen und Verwalten privater Angebote. Dieses Dashboard hat zwei Registerkarten:

1. **Kunden**: Erfahren Sie mehr über private Angebote für Kunden, sobald die Funktion im Herbst 2021 in die Private Preview geht.
2. **Cloud-Wiederverkäufer**: Öffnet das Dashboard für private Angebote für Cloud-Wiederverkäufer, mit dem Sie:

    - Neue private Angebote erstellen
    - Einsicht in den Status aller Ihrer privaten Angebote
    - Vorhandene private Angebote klonen
    - Private Angebote zurückziehen
    - Private Angebote löschen

## <a name="create-a-new-private-offer-for-cloud-resellers"></a>Ein neues privates Angebot für Cloud Reseller erstellen

1. Melden Sie sich bei Partner Center an.
2. Wählen Sie **Private Angebote** im linken Navigationsmenü, um das Dashboard zu öffnen.
3. Wählen Sie die Registerkarte **Cloud-Anbieter**.
4. Wählen Sie **+ Neues privates Angebot**.
5. Geben Sie einen Namen für das private Angebot ein. Dies ist ein beschreibender Name, den Sie verwenden, um auf Ihr privates Angebot im Partner Center zu verweisen. Dieser Name ist für Cloud Reseller nicht sichtbar.  

### <a name="offer-setup"></a>Angebot einrichten

Auf der Seite zur Einrichtung des Angebots können Sie die Bedingungen für das private Angebot, den Benachrichtigungskontakt, die Preise und die Cloud-Wiederverkäufer festlegen.

1. **Die Bedingungen für private Angebote** bestimmen die Dauer, während der Ihre Cloud Reseller Ihr privates Angebot entdecken und verkaufen können.

    - Wenn Ihr privates Angebot sofort beginnen soll, wählen Sie als **Startdatum** "So bald wie möglich" aus. Wenn alle Voraussetzungen erfüllt sind, wird Ihr privates Angebot innerhalb von 15 Minuten nach der Einreichung zur Verfügung gestellt. Wird ein privates Angebot auf einen bestehenden Kunden eines Pay-as-you-go-Produkts ausgedehnt, so gilt der private Preis für den gesamten Monat.
    - Um Ihr privates Angebot in einem kommenden Monat beginnen zu lassen, wählen Sie **Bestimmter Monat** und treffen Sie eine Auswahl. Das Startdatum für diese Option ist immer der Erste des Monats.
    - Wählen Sie den Monat für das **Enddatum** Ihres privaten Angebots. Dies wird immer das letzte Datum des Monats sein.

2. Geben Sie bis zu fünf E-Mail-Adressen als **Benachrichtigungskontakte** an, um E-Mail-Updates über den Status Ihres privaten Angebots zu erhalten. Diese E-Mails werden gesendet, wenn Ihr privates Angebot auf **Live**, **Beendet** oder **Zurückgezogen** geht.

3. Konfigurieren Sie einen **Preisgestaltung** Margenprozentsatz für bis zu 10 Angebote/Pläne in einem privaten Angebot. Die Marge, die der Cloud Reseller erhält, ist ein Prozentsatz des Listenpreises Ihres Plans auf dem Marktplatz.

    - Wählen Sie **+ Angebote/Pläne hinzufügen**, um die Angebote/Pläne auszuwählen, für die Sie ein privates Angebot erstellen möchten.
    - Wählen Sie, ob Sie ein privates Angebot auf Angebotsebene (alle aktuellen und zukünftigen Pläne unter diesem Angebot werden mit einem privaten Preis versehen) oder auf Planebene (nur der von Ihnen ausgewählte Plan wird mit einem privaten Preis versehen) erstellen möchten.
    - Wählen Sie bis zu 10 Angebote/Pläne und dann **Hinzufügen**.
    - Geben Sie den Prozentsatz der Marge ein, die Sie Ihren Cloud-Wiederverkäufern gewähren möchten. Die von Ihnen angegebene Marge wird als Prozentsatz des Listenpreises Ihres Plans auf dem Marktplatz berechnet.

    > [!NOTE]
    > Im Auswahlmenü werden nur Angebote/Pläne angezeigt, die in Microsoft AppSource oder Azure Marketplace getätigt werden können.

4. Wählen Sie die **Cloud Reseller**, die Sie zum Verkauf Ihres privaten Angebots autorisieren.

    1. Wählen Sie **+Add Cloud Resellers**.
    2. Suchen Sie nach Ihrem Cloud Reseller anhand des Namens/der Mieter-ID. Sie können auch mit Hilfe von Filtern wie Regionen, Fähigkeiten oder Kompetenzen suchen.
    3. Wählen Sie die Cloud Reseller und wählen Sie **Hinzufügen**.

    > [!NOTE]
    > - Sie können nur Cloud Reseller auswählen, die Teil des Cloud Solution Provider (CSP) Programms von Microsoft sind. 
    > - Sobald Ihr privates Angebot ausläuft, können die von Ihnen autorisierten Cloud Reseller Ihr Marketplace-Angebot weiterhin zum Listenpreis verkaufen.
    > - Private Angebote können auf maximal 400 Cloud Reseller-Mieter erweitert werden.

### <a name="review-and-submit"></a>Überprüfen und Einreichen

Auf dieser Seite können Sie alle von Ihnen eingegebenen Informationen überprüfen.

Einmal abgegebene private Angebote können nicht mehr geändert werden. Vergewissern Sie sich, dass Ihre Angaben korrekt sind.

Wenn Sie bereit sind, klicken Sie auf **Abgeben**. Sie kehren zum Dashboard zurück, wo Sie den Status Ihres privaten Angebots einsehen können. Der/die Benachrichtigungskontakt(e) erhält/erhalten eine E-Mail, sobald das private Angebot aktiv ist.

## <a name="view-private-offers-status"></a>Status privater Angebote anzeigen

Um den Status Ihres privaten Angebots zu sehen:

1. Wählen Sie **Private Angebote** im linken Navigationsmenü, um das Dashboard zu öffnen.
2. Wählen Sie die Registerkarte **Cloud-Anbieter**.
3. Überprüfen Sie die Spalte **Status**.

Der Status des privaten Angebots ist einer der folgenden:

- **Entwurf**: Sie haben mit der Erstellung eines privaten Angebots begonnen, es aber noch nicht eingereicht.
- **In Bearbeitung**: Sie haben ein privates Angebot eingereicht und es wird gerade in unserem System veröffentlicht.
- **Live**: Ihr privates Angebot kann von Cloud Resellern entdeckt und abgewickelt werden.
- **Ended**: Ihr privates Angebot ist abgelaufen oder hat sein Enddatum überschritten.

## <a name="clone-a-private-offer"></a>Klonen eines privaten Angebots

Durch das Klonen eines privaten Angebots können Sie schnell ein neues privates Angebot erstellen.

1. Wählen Sie **Private Angebote** im linken Navigationsmenü, um das Dashboard zu öffnen.
2. Wählen Sie die Registerkarte **Cloud-Anbieter**.
3. Markieren Sie das Kästchen des privaten Angebots, das Sie klonen möchten.
4. Wählen Sie **Klonen** aus.
5. Geben Sie einen neuen Namen für das private Angebot ein.
6. Wählen Sie **Klonen** aus.
7. Bearbeiten Sie die Details auf der Seite Angebotseinrichtung nach Bedarf.
8. **Senden Sie** das neue private Angebot ab.

## <a name="withdraw-a-private-offer"></a>Ein privates Angebot zurückziehen

Wenn Sie ein privates Angebot zurückziehen, erhalten Ihre Cloud Reseller ab sofort keine Marge mehr und alle zukünftigen Käufe erfolgen zum Listenpreis.

> [!IMPORTANT]
> Private Angebote können nur zurückgezogen werden, wenn kein Cloud Reseller sie an einen Kunden verkauft hat.

So ziehen Sie ein privates Angebot zurück:

1. Wählen Sie **private Angebote** aus dem linken Navigationsmenü, um das Dashboard zu öffnen.
2. Wählen Sie die Registerkarte **Cloud-Anbieter**.
3. Markieren Sie das Kästchen des privaten Angebots, das Sie zurückziehen möchten.
4. Wählen Sie **Zurückziehen**.
5. Wählen Sie **Anforderung zurückziehen**.
6. Ihr(e) Benachrichtigungskontakt(e) erhalten eine E-Mail, wenn Ihr privates Angebot erfolgreich zurückgezogen wurde.

## <a name="delete-a-private-offer"></a>Ein privates Angebot löschen

So löschen Sie ein privates Angebot im Entwurfsstatus:

1. Wählen Sie **Private Angebote** im linken Navigationsmenü, um das Dashboard zu öffnen.
2. Wählen Sie die Registerkarte **Cloud-Anbieter**.
3. Markieren Sie das Kästchen des privaten Angebots, das Sie löschen möchten.
4. Klicken Sie auf **Löschen**.
1. Klicken Sie auf **Bestätigen**.

## <a name="find-additional-details"></a>Zusätzliche Details finden

Während Ihr privates Angebot veröffentlicht wird, können Sie weitere Details zu seinem aktuellen Stand einsehen:

1. Wählen Sie **private Angebote** aus dem linken Navigationsmenü, um das Dashboard zu öffnen.
2. Wählen Sie die Registerkarte **Cloud-Anbieter**.
3. Wählen Sie den Hyperlink "In Bearbeitung" des privaten Angebots in der Spalte **Status**.

Die zusätzlichen Details werden eine der folgenden sein:

- **Cloud Reseller Autorisierung in Bearbeitung**: Wir autorisieren gerade den angegebenen Cloud Reseller, um Ihr Angebot zu verkaufen.
- **Veröffentlichung des privaten Angebots in Bearbeitung**: Wir veröffentlichen gerade den privaten Preis des angegebenen Cloud Resellers.
- **Live**: Das private Angebot ist jetzt für diesen Cloud Reseller live.

## <a name="reporting-on-private-offers"></a>Berichterstattung über private Angebote

Der Auszahlungsbetrag und die Vermittlungsgebühr, die Microsoft erhebt, basieren auf dem Preis nach Anwendung der Marge für Positionen mit einer entsprechenden Marge.

## <a name="next-steps"></a>Nächste Schritte

- [Häufig gestellte Fragen](./isv-csp-faq.yml) zur Konfiguration von privaten ISV-zu-Cloud-Reseller-Angeboten