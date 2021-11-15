---
title: 'Azure EA: Vereinbarungen und Ergänzungen'
description: In diesem Artikel wird erläutert, wie sich Azure EA-Vereinbarungen und -Zusatzvereinbarungen auf Ihre Azure EA-Portalnutzung auswirken.
author: bandersmsft
ms.author: banders
ms.date: 10/22/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: sapnakeshari
ms.openlocfilehash: 200b71b84bd4c09e40b7c19426b9f1e1d09bf943
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130247419"
---
# <a name="azure-ea-agreements-and-amendments"></a>Azure EA: Vereinbarungen und Ergänzungen

In diesem Artikel wird beschrieben, wie sich Azure-EA-Vereinbarungen und -Zusatzvereinbarungen darauf auswirken, wie Sie auf Azure-Dienste zugreifen, diese verwenden und für diese bezahlen.

## <a name="enrollment-provisioning-status"></a>Registrierungsbereitstellungsstatus

Das Startdatum einer neuen Azure-Vorauszahlung (zuvor als „Mindestverbrauch“ bezeichnet) wird durch das Datum definiert, an dem das regionale Betriebszentrum diese verarbeitet hat. Da Azure-Vorauszahlungsbestellungen über das Azure-EA-Portal in der Zeitzone „UTC“ verarbeitet werden, kann es zu Verzögerungen kommen, wenn Ihre Bestellung im Rahmen der Azure-Vorauszahlung in einer anderen Zeitzone verarbeitet wurde. Das Startdatum der Abdeckung in der Bestellung zeigt den Beginn der Azure-Vorauszahlung an. Das Startdatum der Abdeckung ist dann, wenn die Azure-Vorauszahlung im Azure EA-Portal angezeigt wird.

## <a name="support-for-enterprise-customers"></a>Support für Unternehmenskunden

 Das [Enterprise Agreement-Supportplanangebot](https://azure.microsoft.com/offers/enterprise-agreement-support/) von Azure steht einigen Kunden zur Verfügung.

## <a name="enrollment-status"></a>Registrierungsstatus

Eine Registrierung weist einen der folgenden Statuswerte auf. Jeder Wert bestimmt, wie Sie eine Registrierung verwenden und auf diese zugreifen können. Der Registrierungsstatus legt fest, in welcher Phase sich die Registrierung befindet. Er teilt Ihnen mit, ob die Registrierung aktiviert werden muss, bevor sie verwendet werden kann, oder ob der anfängliche Zeitraum abgelaufen ist und eine Überschreitung der Nutzung in Rechnung gestellt wird.

**Ausstehend**: Der Registrierungsadministrator muss sich beim Azure EA-Portal anmelden. Nach der Anmeldung wechselt die Registrierung in den Status **Aktiv**.

**Aktiv**: Die Registrierung ist verfügbar und kann verwendet werden. Sie können Konten und Abonnements im Azure EA-Portal erstellen. Direktkunden können Abteilungen, Konten und Abonnements im [Azure-Portal](https://portal.azure.com) erstellen. Die Registrierung bleibt bis zum Enddatum des Enterprise Agreements aktiv. 

**Indefinite Extended Term** (Unbestimmte Laufzeitverlängerung): Dieser Status wird angezeigt, wenn das Enddatum des Enterprise Agreements erreicht wurde. Bevor die EA-Registrierung das Enterprise Agreement-Enddatum erreicht, sollte der Registrierungsadministrator einen der folgende Schritte ausführen:

- Verlängern der Registrierung durch Hinzufügen einer zusätzlichen Azure-Vorauszahlung
- Übertragen der vorhandenen Registrierung in eine neue Registrierung
- Migrieren zum Microsoft Online Subscription-Programm (MOSP)
- Bestätigen der Deaktivierung aller Dienste, die der Registrierung zugeordnet sind

**Abgelaufen**: Die EA-Registrierung läuft ab, wenn das Enddatum für das Enterprise Agreement erreicht ist. Der EA-Kunde hat keine Option für eine Laufzeitverlängerung mehr und alle seine Dienste werden deaktiviert.

Neue Kündigungsformulare werden ab dem 1. August 2019 für kommerzielle Azure-Kunden nicht mehr akzeptiert. Stattdessen werden alle Registrierungen unbegrenzt verlängert. Wenn Sie die Verwendung von Azure-Diensten beenden möchten, schließen Sie Ihr Abonnement im [Azure-Portal](https://portal.azure.com). Alternativ kann Ihr Partner eine Anforderung zur Beendigung übermitteln. Für Kunden mit behördlichen Verträgen ändert sich nichts.

**Übertragen**: Der Status „Übertragen“ gilt für Registrierungen, deren zugeordnete Konten und Dienste in eine neue Registrierung übertragen werden. Registrierungen werden nicht automatisch übertragen, wenn während der Verlängerung eine neue Registrierungsnummer generiert wird. Die vorherige Registrierungsnummer muss in die Verlängerungsanforderung des Kunden aufgenommen werden, damit eine automatische Übertragung erfolgt.

## <a name="partner-markup"></a>Partnermarkup

Das Markup für Partnerpreise im Azure EA-Portal ermöglicht bessere Kostenberichte für die Kunden. Das Azure EA-Portal zeigt die Nutzung und die Preise an, die Partner für ihre Kunden konfiguriert haben.

Mithilfe des Features „Markup“ können Partneradministratoren einen prozentualen Aufschlag zu ihren indirekten Enterprise Agreements hinzufügen. Der prozentuale Aufschlag gilt für alle Informationen zu Microsoft-Erstanbieterdiensten im Azure-EA-Portal, wie beispielsweise Preise für Verbrauchseinheiten, Azure-Vorauszahlung und Aufträge. Nachdem der Aufschlag vom Partner veröffentlicht wurde, werden dem Kunden die Azure-Kosten im Azure-EA-Portal angezeigt. Dies sind beispielsweise die Nutzungszusammenfassung, Preislisten und heruntergeladene Nutzungsberichte.

Ab September 2019 können Partner während einer Laufzeit jederzeit Markups anwenden. Für ein Markup müssen sie nicht bis zum Jahrestag der Laufzeit warten.

Microsoft greift nur dann auf die angegebenen Aufschläge und zugehörigen Preise zu und nutzt diese für beliebige Zwecke, wenn dies vom Kanalpartner ausdrücklich autorisiert wurde.

### <a name="how-the-calculation-works"></a>Funktionsweise der Berechnung

Der LSP gibt eine einstellige Prozentzahl im EA-Portal an.    Alle kaufmännischen Informationen im Portal werden um den vom LSP angegebenen Prozentsatz angehoben. Beispiel:

- Ein Kunde unterzeichnet ein Enterprise Agreement mit einer Azure-Vorauszahlung von 100.000 USD.
- Der Preis für Verbrauchseinheiten für Dienst A beträgt 10 USD/Stunde.
- Der LSP legt im EA Portal einen Aufschlag von 10 % fest.
- Das folgende Beispiel veranschaulicht, welche kaufmännischen Informationen dem Kunden angezeigt werden:
    - Saldo des Mindestverbrauchs: 110.000 USD.
    - Preis pro Verbrauchseinheit für Dienst A: 11 USD/Stunde.
    - Nutzungs- bzw. Hostinginformationen für Dienst A bei 100 Stunden Nutzung: 1.100 USD.
    - Für den Kunden nach Abzug des Verbrauchs für Dienst A verfügbarer Mindestverbrauchssaldo: 108.900 USD.

### <a name="when-to-use-a-markup"></a>Verwenden eines Aufschlags

Verwenden Sie dieses Feature, wenn Sie den gleichen Prozentsatz für den Aufschlag für ALLE kaufmännischen Transaktionen im EA festlegen möchten. Dies gilt beispielsweise für Informationen zur Azure-Vorauszahlung, Verbrauchseinheitenpreise, Auftragsinformationen usw.

Verwenden Sie das Feature in folgenden Fällen nicht:
- Sie legen für Azure-Vorauszahlung und Verbrauchseinheitenpreise verschiedene Tarife fest.
- Sie verwenden verschiedene Tarife für verschiedene Verbrauchseinheiten.

Wenn Sie verschiedene Tarife für verschiedene Verbrauchseinheiten verwenden, empfiehlt sich die Entwicklung einer benutzerdefinierten Lösung basierend auf dem API-Schlüssel, der vom Kunden bereitgestellt werden kann, um Verbrauchsdaten abzurufen und Berichte zu erstellen.

### <a name="other-important-information"></a>Weitere wichtige Informationen

Dieses Feature soll eine Schätzung der Azure-Kosten für Endkunden ermöglichen. Der LSP ist für alle Finanztransaktionen mit dem Kunden im Rahmen des EA verantwortlich.

Prüfen Sie alle kaufmännischen Informationen – Guthabensaldo, Preisliste usw. – sorgfältig, bevor Sie die Preise einschließlich Aufschlägen für Endkunden veröffentlichen.

### <a name="how-to-add-a-price-markup"></a>Hinzufügen eines Preisaufschlags

**Schritt 1: Hinzufügen eines Preisaufschlags**

1. Klicken Sie im Enterprise Portal im linken Navigationsbereich auf **Berichte**.
1. Klicken Sie unter _Nutzungszusammenfassung_ auf den blauen Text **Aufschlag**.
1. Geben Sie einen Prozentsatz für den Aufschlag (zwischen -100 und 100) ein, und klicken Sie auf **Vorschau**.


**Schritt 2: Überprüfen und validieren**

Überprüfen Sie den Aufschlagspreis in der _Nutzungszusammenfassung_ für die Laufzeit der Vorauszahlung in der Kundenansicht. Der Preis von Microsoft ist in der Partneransicht weiterhin verfügbar. Sie können mithilfe des Partnermarkups „Personen“ oben rechts zwischen den Ansichten wechseln.

1. Überprüfen Sie die Preise auf dem Preisblatt.
1. Sie können vor der Veröffentlichung Änderungen vornehmen, indem Sie auf der Registerkarte _Nutzungszusammenfassung anzeigen > Kundenansicht_ auf **Bearbeiten** klicken.
   
Sowohl für die Dienstpreise als auch für die Vorauszahlungssaldi wird der gleiche Prozentsatz als Aufschlag berechnet. Wenn Sie verschiedene Prozentsätze für Mindestverbrauch und Verbrauchseinheitspreise oder für verschiedene Dienste festgelegt haben, verwenden Sie dieses Feature nicht.

**Schritt 3: Veröffentlichen**

Nachdem Sie die Preise überprüft und validiert haben, klicken Sie auf **Veröffentlichen**.
  
Preise mit Aufschlägen stehen Unternehmensadministratoren sofort nach der Auswahl von „Veröffentlichen“ zur Verfügung. Aufschläge können nicht bearbeitet werden. Wenn Sie etwas ändern möchten, müssen Sie den Aufschlag deaktivieren und bei Schritt 1 beginnen.

### <a name="which-enrollments-have-a-markup-enabled"></a>Für welche Registrierungen ist ein Aufschlag aktiviert?

Um zu überprüfen, ob für eine Registrierung ein Aufschlag veröffentlicht wurde, klicken Sie im linken Navigationsbereich auf **Verwalten**, und klicken Sie dann auf die Registerkarte **Registrierung**. Wählen Sie das Feld für die gewünschte Registrierung aus, und sehen Sie sich unter _Registrierungsdetails_ den Aufschlagstatus an. Hier wird der aktuelle Status des Aufschlagfeatures für dieses EA als „Deaktiviert“, „Vorschau“ oder „Veröffentlicht“ angezeigt.

### <a name="how-can-the-customer-download-usage-estimates"></a>Wie kann der Kunde Nutzungsschätzungen herunterladen?

Nach der Veröffentlichung des Partneraufschlags haben indirekte Kunden Zugriff auf CSV-Dateien zu Saldo und Gebühren (monatlich) sowie auf CSV-Dateien zur Nutzung. Die Dateien mit Nutzungsdetails enthalten Informationen zum Ressourcenpreis und zu erweiterten Kosten.

### <a name="how-can-i-as-partner-apply-markup-to-existing-ea-customers-that-was-earlier-with-another-partner"></a>Wie kann ich als Partner Aufschläge für vorhandene EA-Kunden anwenden, die vorher bei einem anderen Partner waren?
Partner können das Aufschlagfeature (in Azure EA) verwenden, nachdem die Änderung des Kanalpartners verarbeitet wurde; sie müssen nicht auf den nächsten Jahrestag warten.


## <a name="resource-prepayment-and-requesting-quota-increases"></a>Vorauszahlung für Ressourcen und Anfordern von Kontingenterhöhungen

**Das System erzwingt die folgenden Standardkontingente pro Abonnement:**

| **Ressource** | **Standardkontingent** | **Kommentare** |
| --- | --- | --- |
| Microsoft Azure Compute-Instanzen | 20 gleichzeitige kleine Compute-Instanzen oder eine äquivalente Anzahl von Compute-Instanzen anderer Größen. | Der folgenden Tabelle können Sie entnehmen, wie die äquivalente Anzahl kleiner Instanzen berechnet wird:<ul><li> Sehr klein: 1 äquivalente kleine Instanz </li><li> Klein: 1 äquivalente kleine Instanz </li><li> Mittel: 2 äquivalente kleine Instanzen </li><li> Groß: 4 äquivalente kleine Instanzen </li><li> Sehr groß: 8 äquivalente kleine Instanzen </li> </ul>|
| V2-VMs für Microsoft Azure Compute-Instanzen | EA: 350 Kerne | GA-IaaS-v2-VMs:<ul><li> Produktfamilie A0\_A7 – 350 Kerne </li><li> Produktfamilie B\_A0\_A4 – 350 Kerne </li><li> Produktfamilie A8\_A9 – 350 Kerne </li><li> Produktfamilie DF – 350 Kerne</li><li> GF – 350 Kerne </li></ul>|
| Gehostete Microsoft Azure-Dienste | 6 gehostete Dienste | Dieses Limit bei gehosteten Diensten kann für ein einzelnes Abonnement nicht über den Wert 6 hinaus angehoben werden. Wenn zusätzliche gehostete Dienste erforderlich sind, fügen Sie weitere Abonnements hinzu. |
| Microsoft Azure Storage | 5 Speicherkonten mit einer Größe von jeweils maximal 100 TB | Sie können pro Abonnement bis zu 20 Speicherkonten hinzufügen. Wenn zusätzliche Speicherkonten erforderlich sind, fügen Sie weitere Abonnements hinzu. |
| SQL Azure | 149 Datenbanken eines beliebigen Typs (Web Edition oder Business Edition) |   |
| Zugriffssteuerung | 50 Namespaces pro Konto, 100 Millionen Access Control-Transaktionen pro Monat |   |
| Service Bus | 50 Namespaces pro Konto, 40 Service Bus-Verbindungen | Für Kunden, die Service Bus-Verbindungen im Rahmen von Verbindungspaketen kaufen, gelten Kontingente, die dem Mittelwert zwischen dem erworbenen Paket und dem nächsthöheren Verbindungspaket entsprechen. So gilt beispielsweise für Kunden, die ein Paket mit 500 Verbindungen auswählen, ein Kontingent von 750. |

## <a name="resource-prepayment"></a>Vorauszahlung für Ressourcen

Microsoft bietet Ihnen Dienste mindestens auf der Ebene der zugeordneten Nutzung an, die in der von Ihnen erworbenen monatlichen Vorauszahlung enthalten ist (die Dienstvorauszahlung). Alle anderen Erhöhungen der Nutzungsebene von Dienstressourcen (z. B. durch Hinzufügen weiterer ausgeführter Compute-Instanzen oder Vergrößern des genutzten Speichers) unterliegen der Verfügbarkeit dieser Dienstressourcen.

Keines der oben beschriebenen Kontingente gilt als Dienstvorauszahlung. Um die Anzahl der gleichzeitigen kleinen Compute-Instanzen (oder deren Äquivalente) zu bestimmen, die Microsoft im Rahmen einer Dienstvorauszahlung bereitstellt, wird die Anzahl der zugesicherten Nutzungsstunden für kleine Compute-Instanzen, die in einem Monat erworben wurden, durch die Anzahl der Stunden im kürzesten Monat des Jahres (d. h. Februar – 672 Stunden) dividiert.

## <a name="requesting-a-quota-increase"></a>Anfordern einer Kontingenterhöhung

Sie können jederzeit eine Kontingenterhöhung anfordern, indem Sie eine [Onlineanforderung](https://ms.portal.azure.com/) übermitteln. Damit Ihre Anforderung verarbeitet werden kann, geben Sie die folgenden Informationen an:

- Das Microsoft-Konto oder Geschäfts-, Schul- oder Unikonto, das dem Kontobesitzer Ihres Abonnements zugeordnet ist. Dies ist die E-Mail-Adresse, die zum Anmelden beim Microsoft Azure-Portal verwendet wird, in dem Ihre Abonnements verwaltet werden. Geben Sie auch an, dass dieses Konto einer EA-Registrierung zugeordnet ist.
- Die Ressourcen und Mengen, für die Sie eine Kontingenterhöhung wünschen.
- Die Ihrem Dienst zugeordnete Abonnement-ID für das Azure-Entwicklerportal.
  - Um Informationen zu erhalten, wie Sie Ihre Abonnement-ID abrufen, [wenden Sie sich an den Support](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="plan-skus"></a>Plan-SKUs

Plan-SKUs bieten die Möglichkeit, eine Reihe von integrierten Diensten in Form einer Suite zu einem reduzierten Preis zu erwerben. Die Plan-SKUs ergänzen einander durch weitere integrierte Angebote und Suites, um noch mehr Kosteneinsparungen zu bieten.

Ein Beispiel ist das OMS-Abonnement (Operations Management Suite). OMS bietet eine einfache Möglichkeit, auf einen umfangreichen Satz an cloudbasierten Verwaltungsfunktionen zuzugreifen, z. B. für Analyse, Konfiguration, Automatisierung, Sicherheit, Sicherung und Notfallwiederherstellung. OMS-Abonnements umfassen Rechte für System Center-Komponenten, um eine vollständige Lösung für Hybrid Cloud-Umgebungen bereitzustellen.

Unternehmensadministratoren können Kontobesitzer im Enterprise Portal mithilfe der folgenden Schritte zuvor erworbenen Plan-SKUs zuweisen:

### <a name="view-the-price-sheet-to-check-included-quantity"></a>Anzeigen des Preisblatts zum Überprüfen der enthaltenen Menge

1. Melden Sie sich als Unternehmensadministrator an.
1. Klicken Sie im linken Navigationsbereich auf **Berichte**.
1. Klicken Sie auf die Registerkarte **Preisblatt**.
1. Klicken Sie in der oberen rechten Ecke auf das Symbol „Herunterladen“.
1. Suchen Sie mit einem Filter in der Spalte „Enthaltene Menge“ nach entsprechenden Plan-SKU-Teilenummern, und wählen Sie Werte größer als 0 aus.

Direktkunden können das Preisblatt im Azure-Portal anzeigen. Weitere Informationen finden Sie unter [Anzeigen des Preisblatts im Azure-Portal](ea-pricing.md#download-pricing-for-an-enterprise-agreement).

### <a name="existingnew-account-owners-to-create-new-subscriptions"></a>Vorhandene/neue Kontobesitzer zum Erstellen neuer Abonnements

**Schritt 1: Anmelden beim Konto**
1. Klicken Sie im Azure-EA-Portal auf die Registerkarte **Verwalten**, und navigieren Sie im oberen Menü zu **Abonnement**.
1. Vergewissern Sie sich, dass Sie als Kontobesitzer dieses Kontos angemeldet sind.
1. Klicken Sie auf **+ Abonnement hinzufügen**.
1. Klicken Sie auf **Kaufen**.

Wenn Sie zum ersten Mal ein Abonnement zu einem Konto hinzufügen, müssen Sie Ihre Kontaktinformationen angeben. Wenn Sie später weitere Abonnements hinzufügen, werden Ihre Kontaktinformationen für Sie hinzugefügt.

Wenn Sie Ihrem Konto zum ersten Mal ein Abonnement hinzufügen, werden Sie aufgefordert, die MOSA-Vereinbarung und einen Tarifplan zu akzeptieren. Diese Abschnitte gelten NICHT für Enterprise Agreement-Kunden, sind aber derzeit notwendig, um Ihre Abonnement bereitzustellen. Ihre Microsoft Azure Enterprise Agreement-Registrierungszusatzvereinbarung hat Vorrang vor den oben genannten Punkten, und Ihre Vertragsbeziehung ändert sich dadurch nicht. Aktivieren Sie das Kontrollkästchen, um die Bedingungen zu akzeptieren.

**Schritt 2: Aktualisieren des Abonnementnamens**

Alle neuen Abonnements werden mit dem Standardabonnementnamen „Microsoft Azure Enterprise“ hinzugefügt. Es ist wichtig, den Abonnementnamen zu aktualisieren, um dieses Abonnement von den anderen Abonnements in Ihrer Unternehmensregistrierung zu unterscheiden und sicherzustellen, dass es in Berichten auf Unternehmensebene erkennbar ist.

Klicken Sie auf **Abonnements**, wählen Sie das von Ihnen erstellte Abonnement aus, und klicken Sie dann auf **Abonnementdetails bearbeiten**.

Aktualisieren Sie den Abonnementnamen und den Dienstadministrator, und aktivieren Sie dann das Kontrollkästchen. Der Abonnementname wird in Berichten angezeigt und ist der Name des Projekts, das dem Abonnement im Entwicklungsportal zugeordnet ist.
Es kann bis zu 24 Stunden dauern, bis neue Abonnements in die Liste der Abonnements übertragen werden.

Nur Kontobesitzer können Abonnements anzeigen und verwalten.

Direktkunden können ein Abonnement im Azure-Portal erstellen und bearbeiten. Weitere Informationen finden Sie unter [Verwalten von Abonnements im Azure-Portal](direct-ea-administration.md#create-a-subscription).

### <a name="troubleshooting"></a>Problembehandlung

**Kontobesitzer wird im Status „Ausstehend“ angezeigt**

Wenn der Registrierung neue Kontobesitzer (Account Owners, AO) hinzugefügt werden, wird unter ihrem Status immer „Ausstehend“ angezeigt. Nach Erhalt der Begrüßungs-E-Mail für die Aktivierung kann der Kontobesitzer sich anmelden, um sein Konto zu aktivieren. Durch diese Aktivierung wird der Kontostatus von „Ausstehend“ in „Aktiv“ geändert.

**Nutzungen werden berechnet, nachdem Plan-SKUs erworben wurden**

Dieses Szenario tritt auf, wenn der Kunde Dienste unter einer falschen Registrierungsnummer bereitgestellt oder die falschen Dienste ausgewählt hat.

Um sich zu vergewissern, dass Sie die Bereitstellung in der richtigen Registrierung ausführen, können Sie auf dem Preisblatt die Informationen zu Ihren enthaltenen Einheiten überprüfen. Melden Sie sich als Unternehmensadministrator an, klicken Sie im linken Navigationsbereich auf **Berichte**, und wählen Sie die Registerkarte **Preisblatt** aus. Klicken Sie in der oberen rechten Ecke auf das Symbol „Herunterladen“. Suchen Sie dann mit einem Filter in der Spalte „Enthaltene Menge“ nach entsprechenden Plan-SKU-Teilenummern, und wählen Sie Werte größer als 0 aus.

Stellen Sie sicher, dass Ihr OMS-Plan auf dem Preisblatt unter den enthaltenen Einheiten aufgeführt wird. Wenn in der Registrierung keine enthaltenen Einheiten für Ihren OMS-Plan vorhanden sind, befindet sich Ihr OMS-Plan möglicherweise in einer anderen Registrierung. Wenden Sie sich unter [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) an den Azure Enterprise Portal-Support.

Wenn die enthaltenen Einheiten für die Dienste auf dem Preisblatt nicht den von Ihnen bereitgestellten Diensten entsprechen (z. B. Operational Insights Premium Data Analyzed statt Operational Insights Standard Data Analyzed), bedeutet dies, dass Sie Dienste bereitgestellt haben, die durch den Plan nicht abgedeckt werden. Wenden Sie sich unter [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) an den Azure Enterprise Portal-Support, damit wir Ihnen weiterhelfen können.

**Bereitgestellte Plan-SKU-Dienste in der falschen Registrierung**

Wenn Sie über mehrere Registrierungen verfügen und Dienste in einer falschen Registrierung bereitgestellt haben, in der kein OMS-Plan existiert, wenden Sie sich unter [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport) an den Azure Enterprise Portal-Support.

## <a name="next-steps"></a>Nächste Schritte

- Informationen zur Verwendung des Azure EA-Portals finden Sie unter [Erste Schritte mit dem Azure EA-Portal](ea-portal-get-started.md).
- Azure EA-Portaladministratoren finden unter [Azure EA-Portalverwaltung](ea-portal-administration.md) Informationen zu allgemeinen Verwaltungsaufgaben.
