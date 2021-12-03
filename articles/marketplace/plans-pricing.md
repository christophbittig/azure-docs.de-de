---
title: Pläne und Preise für Angebote im kommerziellen Marketplace
description: Hier erhalten Sie Informationen zu Plänen für Angebote im kommerziellen Microsoft Marketplace im Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: mingshen-ms
ms.author: mingshen
ms.date: 07/05/2021
ms.openlocfilehash: e91e85982bb021193224010e2bd51df301240c69
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277978"
---
# <a name="plans-and-pricing-for-commercial-marketplace-offers"></a>Pläne und Preise für Angebote im kommerziellen Marketplace

Ein Plan definiert den Umfang und die Einschränkungen eines Angebots sowie ggf. die zugehörigen Preise. Beispielsweise können Sie je nach Angebotstyp regionale Märkte auswählen und festlegen, ob ein Plan öffentlich oder nur für eine private Zielgruppe sichtbar ist. Für einige Angebotstypen werden wiederkehrende Abonnements unterstützt, für andere nutzungsbasierte Preise und bei einigen kann der Kunde das Angebot mit einer Lizenz kaufen, die er direkt vom Herausgeber erworben hat. Dies bietet Ihnen die Flexibilität, Ihren Kunden verschiedene technische Optionen und Preismodelle anzubieten.

Sie können für jedes Angebot, das Pläne unterstützt, bis zu 100 Pläne erstellen, und bis zu 45 dieser Pläne können [privat](#plan-visibility) sein. Kostenpflichtige Pläne können nur für Angebote für virtuelle Azure-Computer, Azure-Anwendungen (verwaltete Anwendungen) und SaaS (Software-as-a-Service) erstellt werden. Wenn Sie ein solches Angebot über Microsoft verkaufen (transaktionsfähige Angebote), müssen Sie mindestens einen Plan erstellen. Sie können Pläne für einige andere Angebotstypen erstellen, aber die Pläne für diese Angebotstypen enthalten keine Preisoptionen.

> [!TIP]
> Bei einem transaktionsfähigen Angebot handelt es sich um ein Angebot, für das Microsoft Geldtransaktionen für eine Softwarelizenz im Namen des Herausgebers durchführt.

## <a name="plans-by-offer-type"></a>Pläne nach Angebotstyp

In der folgenden Tabelle werden die Planoptionen für jeden Angebotstyp aufgeführt. Diese Optionen werden in den folgenden Abschnitten ausführlicher erläutert. 

| Angebotstyp | Pläne mit Preisoptionen | Pläne ohne Preisoptionen | Option für private Zielgruppe |
| ------------ | ------------- | ------------- | ------------- |
| Verwaltete Azure-Anwendung | &#10004; | | &#10004; |
| Azure-Lösungsvorlage | | &#10004; | &#10004; |
| Azure-Container | | &#10004; (BYOL) | |
| IoT Edge-Modul |  | &#10004; |  |
| Verwalteter Dienst |  | &#10004; (BYOL) | &#10004; |
| Software-as-a-Service | &#10004; |  | &#10004; |
| Virtueller Azure-Computer | &#10004; |  | &#10004; |
|||||

Pläne werden für die folgenden Angebotstypen nicht unterstützt:

- Beratungsdienst
- Dynamics 365 Business Central
- Dynamics 365 Customer Engagement & Power Apps
- Dynamics 365 for Operations
- Power BI-App

## <a name="plan-information"></a>Planinformationen

Beim Erstellen eines Plans müssen je nach Angebotstyp andere Informationen angegeben werden. Links zu angebotsspezifischen Artikeln finden Sie im [Leitfaden zur Veröffentlichung nach Angebotstyp](publisher-guide-by-offer-type.md). Nachdem Sie einen neuen Plan auf der Seite **Planübersicht** erstellt haben, werden verschiedene Registerkarten angezeigt, z. B. **Planlisting** oder **Preise und Verfügbarkeit**, auf denen Sie unterschiedliche Details für Ihren Plan konfigurieren können. Jede Registerkarte zeigt als Status unvollständig oder vollständig an, wenn Sie diese Felder ausfüllen.

![Abbildung der Seite „Planlisting“ im Partner Center. Die Felder für Name, Zusammenfassung und Beschreibung des Plans sind hervorgehoben.](./media/commercial-marketplace-plans/plan-listing-tab.png)

Einige allgemeine Informationen müssen für jeden neuen Plan angegeben werden:

- **Plan-ID**: Erstellen Sie für jeden Plan im Angebot eine eindeutige ID. Diese ID kann nur klein geschriebene alphanumerische Zeichen, Bindestriche und Unterstriche enthalten und darf höchstens 50 Zeichen lang sein. Diese ID wird für Kunden in der Produkt-URL-Adresse und ggf. in Azure Resource Manager-Vorlagen angezeigt. Sobald Sie das Angebot veröffentlichen, können Sie die ID nicht mehr ändern.
- **Planname**: (1 in der Abbildung oben.) Erstellen Sie für jeden Plan im Angebot einen eindeutigen Namen. Es sind maximal 50 Zeichen zulässig. Der Planname wird verwendet, um Softwarepläne zu unterscheiden, die möglicherweise Teil desselben Angebots sind (z. B. Angebotsname: Standard-und Enterprise-Tarif). Für Kunden wird dieser Name angezeigt, wenn sie einen Plan in Ihrem Angebot auswählen.
- **Planzusammenfassung**: (2 in der Abbildung oben.) Diese Zusammenfassung wird in den Suchergebnissen im Azure Marketplace angezeigt und darf bis zu 100 Zeichen enthalten.
   > [!NOTE]
   > Dieses Feld gilt nicht für SaaS-Angebote.
- **Planbeschreibung**: (3 in der Abbildung oben.) Fügen Sie eine Beschreibung des Plans hinzu, in der erklärt wird, was diesen Plan von anderen Plänen in Ihrem Angebot unterscheidet. Es sind maximal 500 Zeichen zulässig. Diese Informationen werden Ihren Kunden auf der Seite mit der Angebotsliste angezeigt, wenn sie nach einem Plan für Ihr Angebot suchen.

Der Planname und die Beschreibung werden in der Angebotsliste in den kommerziellen Marketplace-Onlinestores angezeigt. Der folgende Screenshot zeigt drei Pläne für ein SaaS-Angebot in Azure Marketplace.

![Abbildung der Seite „Angebotsliste“ im Partner Center. Drei Pläne werden angezeigt.](./media/commercial-marketplace-plans/offer-listing-page.png)

Nachdem Sie Ihre Pläne erstellt haben, wird auf der Seite **Planübersicht** eine Liste mit dem Namen, der ID, weiteren Details, dem aktuellen Veröffentlichungsstatus und allen verfügbaren Aktionen für den Plan angezeigt. Welche Aktionen verfügbar sind, richtet sich nach dem Status Ihres Plans. Folgende Aktionen sind möglich:

- Wenn der Planstatus **Entwurf** lautet: Entwurf löschen.
- Wenn der Planstatus **Live** lautet: Verteilung des Plans beenden oder private Zielgruppe synchronisieren.

Der folgende Screenshot zeigt zwei Angebotsentwürfe.

![Abbildung der Seite „Planübersicht“ im Partner Center. Zwei Pläne werden angezeigt.](./media/commercial-marketplace-plans/plan-overview-tab.png)

## <a name="pricing-and-availability"></a>Preise und Verfügbarkeit

Der kommerzielle Marketplace arbeitet nach einem Agenturmodell, bei dem die Herausgeber die Preise festlegen, Microsoft den Kunden Rechnungen stellt und Microsoft die Einnahmen dann unter Einbehaltung einer Agenturgebühr an die Herausgeber auszahlt. Auf der Registerkarte **Preise und Verfügbarkeit** oder **Verfügbarkeit** definieren Sie die Märkte, die Sichtbarkeit und die Preise Ihres Angebots (falls zutreffend).

- **Märkte:** Jeder Plan muss in mindestens einem Markt verfügbar sein.  Mit „Bezahlte Steuern“ können Sie Länder anzeigen, in denen Microsoft für Sie Verkaufserlöse und Steuern überweist.
- **Preise:** Preismodelle gelten nur bei Plänen für Angebote für verwaltete Azure-Anwendungen, SaaS und virtuelle Azure-Computer. Alle Pläne für ein Angebot müssen dasselbe Preismodell aufweisen.  
- **Sichtbarkeit des Plans**: Je nach Angebotstyp können Sie eine private Zielgruppe definieren oder das Angebot oder den Plan aus Azure Marketplace ausblenden. Dies wird weiter unten in diesem Artikel unter [Sichtbarkeit des Plans](#plan-visibility) ausführlicher erläutert.

> [!TIP]
> Es wird empfohlen, Pläne zu erstellen, die auf die Nutzungsmuster Ihrer Zielgruppe abgestimmt sind. Dadurch wechseln die Benutzer nicht so häufig den Plan, weil sich ihr Nutzungsverhalten ändert. Ein Beispiel für ein SaaS-Angebot mit drei getakteten Abrechnungsplänen finden Sie unter [Beispielangebot](./partner-center-portal/saas-metered-billing.md#sample-offer).

### <a name="pricing-models"></a>Preismodelle

Sie müssen jedem Plan ein Preismodell für die folgenden Angebotstypen zuordnen. Für jeden dieser Angebotstypen sind verschiedene Preismodelle verfügbar:

- **Verwaltete Azure-Anwendungen**: Pauschalpreis (monatlich) und nutzungsbasierte Preise (Messungsdienstdimensionen).
- **Software-as-a-Service**: Pauschalpreis (monatlich oder jährlich), Preis pro Benutzer und nutzungsbasierte Preise (Messungsdienstdimensionen). 
- **Virtuelle Azure-Computer**: Bring Your Own License (BYOL) und nutzungsbasierte Preise. Bei einem nutzungsbasierten Preismodell können Sie die Kosten nach Kern, Kerngröße oder nach Markt und Kerngröße berechnen. Bei einem BYOL-Lizenzmodell sind keine zusätzlichen nutzungsbasierten Gebühren zulässig.   (Bei BYOL-Angeboten für virtuelle Computer ist kein Preismodell erforderlich.)

Alle Pläne für ein Angebot müssen dasselbe Preismodell aufweisen. Beispielsweise kann ein SaaS-Angebot nicht gleichzeitig über die Pläne „Pauschalgebühr“ und „Pro Benutzer“ verfügen. Ausführliche Informationen finden Sie in der Dokumentation zu den einzelnen Angeboten.

Wenn Sie bereits Preise in US-Dollar für Ihren Plan festgelegt haben und einen anderen Marktstandort hinzufügen, wird der Preis für den neuen Markt entsprechend dem aktuellen Wechselkurs berechnet. Nachdem Sie Ihre Änderungen gespeichert haben, sehen Sie den Link **Export prices (xlsx)** (Preise exportieren (XLSX)), über den Sie den Preis für jeden Markt vor der Veröffentlichung überprüfen und ändern können.

> [!IMPORTANT]
> Nach der Veröffentlichung des Angebots kann das Preismodell nicht mehr geändert werden.

SaaS-Angebote und Angebote für verwaltete Anwendungen mit Pauschalpreisen unterstützen die getaktete Abrechnung über den Marketplace-Messungsdienst. Hierbei handelt es sich um ein nutzungsbasiertes Abrechnungsmodell, mit dem Sie nicht standardmäßige Einheiten (z. B. Bandbreite oder E-Mail-Nachrichten) definieren können, für die Ihre Kunden auf Nutzungsbasis bezahlen. Weitere Informationen zur getakteten Abrechnung für [verwaltete Anwendungen](marketplace-metering-service-apis.md) und [Saas-Apps](./partner-center-portal/saas-metered-billing.md) finden Sie in der jeweiligen Dokumentation.

## <a name="custom-prices"></a>Benutzerdefinierte Preise

Wenn Sie benutzerdefinierte Preise für einen einzelnen Markt festlegen möchten, exportieren, ändern und importieren Sie die Preistabelle. Sie sind für die Überprüfung des Preismodells und diese Einstellungen verantwortlich.

Überprüfen Sie Ihre Preise vor der Veröffentlichung sorgfältig, da nicht alle Elemente eines Plans nach seiner Veröffentlichung geändert werden können:

- Das Preismodell kann nach dem Veröffentlichen eines Plans nicht geändert werden.
- Nachdem ein Abrechnungszeitraum für einen Plan veröffentlicht wurde, kann er nicht mehr entfernt werden.
- Nachdem ein Preis für einen Markt in Ihrem Plan veröffentlicht wurde, kann er nicht mehr geändert werden.

Preise in US-Dollar (USD) werden beim Speichern gemäß dem aktuellen Wechselkurs in die lokale Währung aller ausgewählten Märkte konvertiert. Überprüfen Sie vor der Veröffentlichung die Preise für jeden Markt, indem Sie die Preistabelle exportieren.

1. Sie müssen Ihre Preisänderungen speichern, damit die Preisdaten exportiert werden können. Wählen Sie im unteren Bereich der Registerkarte **Preise und Verfügbarkeit** die Option **Entwurf speichern** aus.
1. Wählen Sie unter **Preise** den Link **Preisdaten exportieren** aus.
1. Öffnen Sie die Datei „exportedPrice.xlsx“ in Microsoft Excel.
1. Nehmen Sie in der Tabelle die gewünschten Änderungen an den Preisinformationen vor, und speichern Sie die CSV-Datei.
    > [!NOTE]
    > Möglicherweise müssen Sie die Bearbeitung in Excel aktivieren, damit Sie die Datei aktualisieren können.
1. Wählen Sie auf der Registerkarte **Preise und Verfügbarkeit** unter **Preise** den Link **Preisdaten importieren** aus.
1. Wählen Sie im angezeigten Dialogfeld **Ja** aus.
1. Wählen Sie die aktualisierte Datei „exportedPrice.xlsx“ und dann **Öffnen** aus.

## <a name="plan-visibility"></a>Sichtbarkeit des Plans

Sie können Pläne für einige Angebotstypen öffentlich oder nur für eine bestimmte (private) Zielgruppe verfügbar machen. Angebote mit privaten Plänen werden im Azure-Portal veröffentlicht. Weitere Informationen über private Pläne finden Sie unter [Private Pläne auf dem kommerziellen Microsoft-Marktplatz](private-plans.md).

Abhängig vom Angebotstyp definieren Sie eine private Zielgruppe mithilfe von Azure-Mandanten-IDs oder Azure-Abonnement-IDs. Sie können bis zu 10 IDs manuell eingeben oder bis zu 10.000 Abonnement-IDs oder 20.000 Mandanten-IDs mithilfe einer CSV-Datei importieren. Sie können auch private Zielgruppen für Beratungsdienste und Dynamics 365-Angebote definieren, die nicht über Pläne verfügen.

Nachdem Sie das Angebot mit einem privaten Plan veröffentlicht haben, können Sie die Zielgruppe aktualisieren oder den Plan für alle Benutzer verfügbar machen. Sobald ein Plan als für alle Benutzer als sichtbar veröffentlicht wurde, muss er für alle Benutzer sichtbar bleiben. Er kann nicht wieder als privater Plan konfiguriert werden.

> [!NOTE]
> Eine private Zielgruppe unterscheidet sich von einer Vorschauzielgruppe. Für einige Angebotstypen können Sie auf der Seite **Verfügbarkeit** eine Zielgruppe definieren, die eine Vorschau Ihres Angebots anzeigen kann, bevor das Angebot im kommerziellen Marketplace veröffentlicht wird. Die Festlegung als private Zielgruppe gilt nur für einen bestimmten Plan. Für die Vorschauzielgruppe können alle Pläne (private und nicht private Pläne) angezeigt werden, jedoch nur während des eingeschränkten Vorschauzeitraums, in dem der Plan getestet und überprüft wird.

Sie können den Plan auch vollständig aus Azure Marketplace ausblenden, wenn Ihr Angebot als Teil einer verwalteten Anwendung oder Lösungsvorlage bereitgestellt wird.

## <a name="free-trials"></a>Kostenlose Testversionen

Sie können bei Plänen für transaktionsfähige Angebote für virtuelle Azure-Computer und SaaS eine kostenlose Testversion aktivieren.

> [!NOTE]
> In diesem Abschnitt werden kostenlose Testversionen von kostenpflichtigen Plänen für Angebote erläutert, die über Microsoft verkauft werden. Dies unterscheidet sich von den kostenlosen Testversionen von Partnern, die Ihre Transaktionen unabhängig bearbeiten. Sie können eine kostenlose Testversion (die nicht mit einem Plan verknüpft ist) für die folgenden Angebotstypen erstellen:
> - Virtueller Azure-Computer 
> - SaaS 
> - Dynamics 365 Business Central
> - Dynamics 365 for Customer Engagement und Power Apps
> - Dynamics 365 for Operations
>
> Weitere Informationen zu diesen Optionen finden Sie unter [Bestimmung Ihrer Veröffentlichungsoption](determine-your-listing-type.md).

Kostenlose Testversionen werden für alle Abrechnungsmodelle mit Ausnahme von getakteten Plänen unterstützt. Für SaaS-Pläne ist eine kostenlose Testversion für 1 Monat möglich. Bei Plänen für virtuelle Azure-Computer werden kostenlose Testversionen für 1, 3 und 6 Monate unterstützt.

Wenn ein Kunde eine kostenlose Testversion auswählt, werden die Abrechnungsinformationen erfasst, die Abrechnung beginnt aber erst, wenn die Testversion in ein kostenpflichtiges Abonnement konvertiert wird. Ein Testabonnement geht nach einem Monat automatisch in ein kostenpflichtiges Abonnement über, wenn der Kunde es nicht vor Ablauf des Testzeitraums kündigt.

Während des Testzeitraums können Kunden alle unterstützten Pläne innerhalb eines Angebots nutzen, für das die kostenlose Testversion aktiviert wurde. Wenn sie zu einer anderen Testversion innerhalb desselben Angebots wechseln, wird der Testzeitraum nicht neu gestartet. Wenn ein Kunde eine kostenlose Testversion z. B. 15 Tage lang verwendet und dann zu einer anderen kostenlosen Testversion für dasselbe Angebot wechselt, werden auf den neuen Testzeitraum die bereits genutzten 15 Tage angerechnet. Die kostenlose Testversion, die der Kunde am Ende des Testzeitraums nutzt, wird automatisch in ein kostenpflichtiges Abonnement konvertiert.

Nachdem ein Kunde eine kostenlose Testversion für einen Plan ausgewählt hat, kann er erst nach Ablauf des Testzeitraums zu einem kostenpflichtigen Abonnement wechseln. Wenn ein Kunde zu einem anderen Plan innerhalb des Angebots wechselt, für den es keine kostenlose Testversion gibt, wird der Wechsel ausgeführt, die kostenlose Testversion wird aber sofort beendet, und alle Daten gehen verloren.

> [!NOTE]
> Nachdem ein Kunde mit den Zahlungen für einen Plan begonnen hat, kann er keine kostenlose Testversion für dasselbe Angebot mehr erhalten, auch wenn er zu einem anderen Plan wechselt, der kostenlose Testversionen unterstützt.

Wenn Sie Informationen zu Kundenabonnements abrufen möchten, die derzeit an einer kostenlosen Testversion teilnehmen, verwenden Sie die neue API-Eigenschaft `isFreeTrial`, die als „true“ oder „false“ gekennzeichnet ist. Weitere Informationen finden Sie unter der [API zum Abrufen von SaaS-Abonnements](./partner-center-portal/pc-saas-fulfillment-subscription-api.md#get-subscription).

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum Hinzufügen oder Aktualisieren von Plänen für ein vorhandenes Angebot finden Sie unter [Aktualisieren eines bestehenden Angebots im kommerziellen Marketplace](./update-existing-offer.md).
- Weitere Informationen zu Transaktionsoptionen und zugehörigen Preismodellen finden Sie unter [Transaktionsfunktionen im kommerziellen Marketplace](./marketplace-commercial-transaction-capabilities-and-considerations.md).
