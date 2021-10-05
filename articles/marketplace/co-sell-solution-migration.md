---
title: Migrieren von Co-Selling-Lösungen von OCP GTM zu Partner Center für Microsoft AppSource
description: Hier erfahren Sie mehr über das Migrieren von Co-Selling-Lösungen von OCP GTM zu Partner Center (Azure Marketplace).
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 09/27/2021
ms.openlocfilehash: d80063eeb46938d3f8006f777a3209bc34d7cfcb
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/27/2021
ms.locfileid: "129083189"
---
# <a name="migration-of-co-sell-solutions-from-ocp-gtm-to-the-commercial-marketplace"></a>Migrieren von Co-Selling-Lösungen von OCP GTM zum kommerziellen Marketplace

Microsoft ändert die Umgebung für Veröffentlichungen. Die Veröffentlichung von Angeboten im [kommerziellen Marketplace](overview.md) wurde vereinfacht. Dabei wird das Co-Selling über die drei Kanäle von Microsoft erleichtert, indem die Angebotserstellung und -verwaltung im Partner Center, in dem Sie bereits Ihre Beziehung zu Microsoft verwalten, zentralisiert wird.

Als Microsoft-Partner, der im kommerziellen Marketplace registriert ist, haben Sie folgende Möglichkeiten:

- Veröffentlichen Sie Ihre Angebote zentral, und vermarkten Sie diese durch Co-Selling an Direktkunden, Partner und Vertriebskanäle von Microsoft.
- Stellen Sie sicher, dass Ihre Angebote im richtigen Onlineshop – [Microsoft AppSource](https://appsource.microsoft.com) oder [Azure Marketplace](https://azure.microsoft.com) – zu finden sind, um Millionen von Cloudkunden zu erreichen, für die die angebotenen Funktionen geeignet sind.
- Machen Sie eigene Erfahrungen bei der Veröffentlichung, indem Sie sich an Co-Selling-Angeboten beteiligen, die auf Ihre Geschäftsziele ausgerichtet sind.
- Passen Sie Ihre veröffentlichten Angebote im Partner Center an, in dem Sie bereits Ihre Beziehung zu Microsoft und Ihre Co-Selling-Verkaufschancen verwalten.
- Profitieren Sie von [Marketplace-Prämien](marketplace-rewards.md).

## <a name="prerequisites-to-continue-co-selling-with-microsoft"></a>Voraussetzungen für das weitere Co-Selling mit Microsoft

Stellen Sie sicher, dass Sie über eine aktive Microsoft Partner Network-Mitgliedschaft verfügen und im kommerziellen Marketplace im Partner Center registriert sind.

- Werden Sie [völlig kostenlos](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) Mitglied im Microsoft Partner Network. Als Partner haben Sie Zugriff auf exklusive Ressourcen, Programme, Tools und Verbindungen, um Ihr Unternehmen voranzubringen.
- Wenn Sie kein Konto im kommerziellen Marketplace besitzen, [registrieren Sie sich jetzt](create-account.md), um das Co-Selling mit Microsoft fortzusetzen und die vollständige Umgebung für Veröffentlichungen zu nutzen.

## <a name="publishing-updates-for-attaining-co-sell-ready-status"></a>Veröffentlichen von Updates zum Erreichen des Co-Sell Ready-Status

Damit Ihre Lösung für Verkäufer und Partner von Microsoft sichtbar ist, muss sie die [Co-Sell Ready-Anforderungen](./co-sell-overview.md) erfüllen. Damit ein Microsoft-Verkäufer Co-Selling-Incentives erhalten kann, muss Ihre Lösung die [Anforderungen für Incentiveberechtigungen](./co-sell-overview.md) erfüllen. Geben Sie die erforderlichen Informationen auf der Registerkarte „Co-Selling“ im Partner Center an. (Weitere Informationen finden Sie in [dieser Abbildung](#action-2-merge) weiter unten in diesem Artikel).

> [!NOTE]
> Im kommerziellen Marketplace werden Ihre Lösungen in der gesamten Umgebung für Veröffentlichungen als „Angebote“ bezeichnet.

Nachdem Sie sich im kommerziellen Marketplace registriert haben, bereiten Sie die Migration Ihrer Lösungen von OCP GTM vor. Wir haben eine Schnittstelle für die digitale Migration erstellt, die eine zentrale Ansicht der OCP GTM-Lösungen für Ihre Organisation bietet. Über diese Schnittstelle können Sie die Aktion ausführen, um die Lösung mit einem ähnlichen Angebot zusammenzuführen, das bereits im kommerziellen Marketplace veröffentlicht und live geschaltet ist, oder sie verwerfen.

Führen Sie die folgenden Schritte aus, bevor Sie Ihre Lösungen aus OCP GTM importieren:

1. Besuchen Sie die [Herausgeberliste](https://go.microsoft.com/fwlink/?linkid=2165704) Ihres Unternehmens. Sie umfasst den Kontobesitzer, Manager und Entwickler, die über Veröffentlichungszugriff verfügen. Erfahren Sie mehr über [Partner Center-Benutzerrollen](user-roles.md).
2. Bitten Sie einen der aufgelisteten Kontakte, dem kommerziellen Marketplace Benutzer als *Manager* oder *Entwickler* [hinzuzufügen](https://go.microsoft.com/fwlink/?linkid=2166003), da Lösungen nur von Inhabern dieser Rollen bearbeitet und veröffentlicht werden können.
3. Arbeiten Sie mit Ihren Entwicklern zusammen, um Ihre Lösungen aus dem OCP GTM-Konto in den kommerziellen Marketplace zu migrieren.
4. Entscheiden Sie, welche der folgenden Aktionen Sie ausführen möchten:
    1. Wenn Sie über eine Lösung in OCP GTM verfügen, die Sie zu Partner Center migrieren möchten, stehen Ihnen *zum Beibehalten von Empfehlungspipeline, Begleitmaterialien, Co-Selling-Status und Incentives* zwei Szenarien zur Auswahl:
        - Wenn im kommerziellen Marketplace kein ähnliches Angebot vorhanden ist:
            - Schritt 1: [Erstellen von Angeboten](#step-1-similar-offer-does-not-exist-in-commercial-marketplace-please-follow-these-steps)
            - Schritt 2: [Veröffentlichen im kommerziellen Marketplace](#scenario-2-similar-offer-exists-in-commercial-marketplace-please-follow-these-steps)
            - Schritt 3: [Anfordern der Zusammenführung der Lösung in OCP GTM mit neu veröffentlichtem Marketplace-Angebot](#begin-the-migration-of-your-solutions-from-ocp-gtm)
        - Wenn im kommerziellen Marketplace bereits ein ähnliches Angebot vorhanden ist:
            - Schritt 1: [Anfordern der Zusammenführung der Lösung in OCP GTM mit veröffentlichtem Marketplace-Angebot](#begin-the-migration-of-your-solutions-from-ocp-gtm)
    1. Wenn Sie über eine Lösung in OCP GTM verfügen, die nicht mehr relevant ist, verwerfen Sie diese Lösung.

> [!IMPORTANT]
> Am 17. Mai 2021 wurde die unten gezeigte Migrationsoption entfernt.  Wenn Sie zuvor die Migrationsoption verwendet haben und über ein Angebot verfügen, das als Angebotsentwurf zum kommerziellen Marketplace migriert wurde, ist es wichtig, dass Sie den Veröffentlichungsprozess im kommerziellen Marketplace abschließen.  
> - Weitere Informationen zum [Überprüfen und Veröffentlichen eines Angebots im kommerziellen Microsoft-Marketplace | Microsoft-Dokumentation](./review-publish-offer.md)
> - [Schulungspräsentation zum Veröffentlichen im kommerziellen Marketplace](https://assetsprod.microsoft.com/mpn/en-us/guidance-and-permissions-needed-to-publish-an-offer-and-co-sell-with-ms.pptm)
:::image type="content" source="media/co-sell-migrate/ready-to-migrate.png" alt-text="Ich möchte diese Lösung zum kommerziellen Marketplace migrieren":::

## <a name="begin-the-migration-of-your-solutions-from-ocp-gtm"></a>Beginn der Migration Ihrer Lösungen von OCP GTM

1. Beginnen Sie [hier](https://go.microsoft.com/fwlink/?linkid=2165807) mit der Migration.
1. Wählen Sie die Seite **Übersicht** aus, und klicken Sie dann auf **Klicken Sie hier, um zu beginnen**.

    :::image type="content" source="media/co-sell-migrate/welcome-overveiw.png" alt-text="Anzeige der Übersichtsseite":::

1. Um mit der Migration zu beginnen, wählen Sie die Registerkarte **Lösungen** aus, auf der alle mit Ihren MPN-IDs verknüpften Lösungen angezeigt werden.

    :::image type="content" source="media/co-sell-migrate/solutions-tab.png" alt-text="Seite „Übersicht“ im Partner Center, Registerkarte „Lösungen“":::

    > [!NOTE]
    > Wenn keine Lösungen für die Migration zum kommerziellen Marketplace vorhanden sind, wird auf dieser Registerkarte darauf hingewiesen. Stellen Sie sicher, dass die migrierten Lösungen im kommerziellen Marketplace veröffentlicht werden, um das Co-Selling mit Microsoft fortzusetzen.

    Weitere Informationen zum Lösungsstatus finden Sie in den QuickInfos. Alle Lösungen, für die eine Aktion aussteht, werden unter **Aktion** aufgeführt.<a name="beginmigration"></a>

1. Wählen Sie für die Lösung, die migriert werden soll, **Migration starten** aus (siehe Abbildung oben), und wählen Sie dann eine der folgenden Optionen aus:

    :::image type="content" source="media/co-sell-migrate/migration-options.png" alt-text="Drei Optionen für die Migration":::

### <a name="merge-solution"></a>Zusammenführungslösung

Welchen Nutzen hat die Zusammenführung Ihrer OCP GTM-Lösung mit einem vorhandenen veröffentlichten und live geschalteten Angebot im kommerziellen Marketplace?

- Partnern, die derzeit Co-Selling-Aktivitäten für Angebote in OCP GTM durchführen, wird empfohlen, ihre Lösungen zum kommerziellen Marketplace zu migrieren, um von den dessen Funktionen zu profitieren und die Veröffentlichungsvorgänge zu vereinfachen.

Wenn Sie über eine Lösung in OCP GTM verfügen, die Sie zu Partner Center migrieren möchten, stehen Ihnen zwei Szenarien zur Auswahl. Am Ende dieses Zusammenführungsprozesses behält Ihr Angebot die Empfehlungspipeline, Begleitmaterialien, den Co-Selling-Status und Incentives bei.

#### <a name="step-1-similar-offer-does-not-exist-in-commercial-marketplace-please-follow-these-steps"></a>Szenario 1: Auszuführende Schritte, wenn kein ähnliches Angebot im kommerziellen Marketplace vorhanden ist

#### <a name="workspaces-view"></a>[Ansicht „Arbeitsbereiche“](#tab/workspaces-view)

Wenn im kommerziellen Marketplace noch kein Angebot vorhanden ist, mit dem eine Lösung in OCP GTM zusammengeführt werden kann, müssen Sie zunächst ein Angebot im kommerziellen Marketplace erstellen UND VERÖFFENTLICHEN (dieses behält den Co-Selling-Status, Incentives und die Empfehlungspipeline bei).

1. Erstellen Sie einen Angebotsentwurf im kommerziellen Marketplace.

   1. Wählen Sie auf der Seite Übersicht des kommerziellen Marketplace die Option **+ Neues Angebot** und dann den Angebotstyp aus, den Sie wünschen.

        [ ![Screenshot der Schaltfläche "Neues Angebot" auf der Seite "Marketplace-Angebote](media/co-sell-migrate/new-offer-workspaces.png) ](media/co-sell-migrate/new-offer-workspaces.png#lightbox)

   2. Geben Sie die erforderlichen Informationen auf den einzelnen Registerkarten an.
      - Über die Links für **Weitere Informationen** und QuickInfos erfahren Sie mehr über die Anforderungen und Details.
      - Bearbeiten Sie optional die Seite **Verkauf über CSPs** (im linken Navigationsmenü unten), um Angebote über das CSP-Programm (Cloud Solution Provider) zu verkaufen.

        [ ![Anzeige der Seite „Angebotseinrichtung“ mit hervorgehobenen Übersichtsoptionen](media/co-sell-migrate/offer-setup-nav-workspaces.png) ](media/co-sell-migrate/offer-setup-nav-workspaces.png#lightbox)

   3. Wähen Sie **Entwurf speichern** aus.
        - Ausführliche Anweisungen zu den Informationen, die Sie angeben müssen, bevor das Angebot veröffentlicht werden kann, finden Sie im entsprechenden [Leitfaden zur Veröffentlichung](./publisher-guide-by-offer-type.md).
        - Berücksichtigen Sie im entsprechenden Artikel für Ihren Angebotstyp die Berechtigungsvoraussetzungen, um die Auswahl und Konfiguration Ihres Angebots abzuschließen.
        - Entnehmen Sie den Veröffentlichungsmustern der einzelnen Onlineshops Beispiele für die Zuordnung Ihrer Lösung zu einem Angebotstyp und einer Konfiguration.
        - [Bewährte Methoden für Angebotslistung – kommerzieller Microsoft-Marketplace | Microsoft-Dokumentation](./gtm-offer-listing-best-practices.md)

    > [!TIP]
    > Es wird empfohlen, die Daten auf der Registerkarte **Co-Selling mit Microsoft** *nicht auszufüllen*. Um Ihnen Zeit zu sparen, werden wir diese Daten während des Zusammenführungsprozesses mit Ihren vorhandenen Begleitmaterialien in OCP GTM auffüllen.

    Nach Abschluss der Zusammenführung können Sie zur Registerkarte „Co-Selling mit Microsoft“ zurückkehren und bei Bedarf Aktualisierungen vornehmen. Weitere Informationen finden Sie unter [Konfigurieren von Co-Selling für ein Angebot im kommerziellen Marketplace](./co-sell-configure.md).
1. Wählen Sie **Überprüfen und veröffentlichen** aus, wenn Sie fertig sind.

    [ ![Seite „Co-Selling mit Microsoft“ mit hervorgehobenen Optionen](media/co-sell-migrate/co-sell-with-ms-workspaces.png) ](media/co-sell-migrate/co-sell-with-ms-workspaces.png#lightbox)

1. Nachdem Sie alle übermittelten Informationen überprüft haben, wählen Sie **Veröffentlichen** aus, um Ihren Angebotsentwurf für die Zertifizierungsüberprüfung zu übermitteln. [Erfahren Sie mehr über die Zertifizierungsphase](./review-publish-offer.md).

    [ ![Anzeige der Seite Überprüfen und veröffentlichen](media/co-sell-migrate/review-and-publish-workspaces.png) ](media/co-sell-migrate/review-and-publish-workspaces.png#lightbox)

1. Verfolgen Sie den Status Ihrer Übermittlung auf der Registerkarte Übersicht.

    [ ![Veranschaulicht den Veröffentlichungsstatus für ein Angebot in Partner Center. Die Schaltfläche Live wechseln wird angezeigt. Der Link Überprüfungsbericht anzeigen wird auch unter Automatisierte Validierung angezeigt.](./media/review-publish-offer/publish-status-saas.png) ](./media/review-publish-offer/publish-status-saas.png#lightbox)

1. Sie werden benachrichtigt, wenn unsere Zertifizierungsüberprüfung abgeschlossen ist. Wenn Sie handlungsrelevantes Feedback von uns erhalten, sollten Sie es umsetzen und dann **Veröffentlichen** auswählen, um eine erneute Zertifizierung zu starten.

1. Sobald Ihr Angebot erfolgreich zertifiziert wurde, nutzen Sie den bereitgestellten Link, um das Angebot in der Vorschau anzuzeigen und abschließende Anpassungen vorzunehmen. Wenn Sie fertig sind, wählen Sie **Live schalten** aus (siehe Schaltfläche oben), um Ihr Angebot in relevanten Storefronts des kommerziellen Marketplace zu veröffentlichen.

1. **Fahren Sie mit Szenario 2 unten fort, um den Zusammenführungsvorgang abzuschließen.**

#### <a name="current-view"></a>[Aktuelle Ansicht](#tab/current-view)

Wenn im kommerziellen Marketplace noch kein Angebot vorhanden ist, mit dem eine Lösung in OCP GTM zusammengeführt werden kann, müssen Sie zunächst ein Angebot im kommerziellen Marketplace erstellen UND VERÖFFENTLICHEN (dieses behält den Co-Selling-Status, Incentives und die Empfehlungspipeline bei).

1. Erstellen Sie einen Angebotsentwurf im kommerziellen Marketplace.

   1. Klicken Sie auf **+ Neues Angebot**.

        :::image type="content" source="media/co-sell-migrate/new-offer.png" alt-text="Anzeige für neues Angebot":::

   2. Vervollständigen Sie die erforderlichen Informationen auf den einzelnen Registerkarten. • Die Links **Weitere Informationen** und QuickInfos führen Sie durch die Anforderungen und Details.
         • Bearbeiten Sie optional die Seite **Verkauf über CSPs** (im linken Navigationsmenü unten), um Angebote über das CSP-Programm (Cloud Solution Provider) zu verkaufen.

        :::image type="content" source="media/co-sell-migrate/offer-setup-nav.png" alt-text="Anzeige der Seite „Angebotseinrichtung“ mit hervorgehobenen Übersichtsoptionen":::
   3. Wähen Sie **Entwurf speichern** aus.
        - Ausführliche Anweisungen zu den Informationen, die Sie angeben müssen, bevor das Angebot veröffentlicht werden kann, finden Sie im entsprechenden [Leitfaden zur Veröffentlichung](./publisher-guide-by-offer-type.md).
        - Berücksichtigen Sie im entsprechenden Artikel für Ihren Angebotstyp die Berechtigungsvoraussetzungen, um die Auswahl und Konfiguration Ihres Angebots abzuschließen.
        - Entnehmen Sie den Veröffentlichungsmustern der einzelnen Onlineshops Beispiele für die Zuordnung Ihrer Lösung zu einem Angebotstyp und einer Konfiguration.
        - [Bewährte Methoden für Angebotslistung – kommerzieller Microsoft-Marketplace | Microsoft-Dokumentation](./gtm-offer-listing-best-practices.md)

    > [!TIP]
    > Es wird empfohlen, die Daten auf der Registerkarte **Co-Selling mit Microsoft** *nicht auszufüllen*. Um Ihnen Zeit zu sparen, werden wir diese Daten während des Zusammenführungsprozesses mit Ihren vorhandenen Begleitmaterialien in OCP GTM auffüllen.

    Nach Abschluss der Zusammenführung können Sie zur Registerkarte „Co-Selling mit Microsoft“ zurückkehren und bei Bedarf Aktualisierungen vornehmen. Weitere Informationen finden Sie unter [Konfigurieren von Co-Selling für ein Angebot im kommerziellen Marketplace](./co-sell-configure.md).
1. Wählen Sie **Überprüfen und veröffentlichen** aus, wenn Sie fertig sind.

     :::image type="content" source="media/co-sell-migrate/co-sell-with-ms.png" alt-text="Seite „Co-Selling mit Microsoft“ mit hervorgehobenen Optionen":::
1. Nachdem Sie alle übermittelten Informationen überprüft haben, wählen Sie **Veröffentlichen** aus, um Ihren Angebotsentwurf für die Zertifizierungsüberprüfung zu übermitteln. [Erfahren Sie mehr über die Zertifizierungsphase](./review-publish-offer.md).:::image type="content" source="media/co-sell-migrate/review-and-publish.png" alt-text="Anzeige der Seite „Überprüfen und veröffentlichen“":::
1. Verfolgen Sie den Status Ihrer Übermittlung auf der Registerkarte Übersicht.

    :::image type="content" source="media/co-sell-migrate/offer-overview-tab.png" alt-text="Anzeige der Übersichtsregisterkarte":::
1. Sie werden benachrichtigt, wenn unsere Zertifizierungsüberprüfung abgeschlossen ist. Wenn Sie handlungsrelevantes Feedback von uns erhalten, sollten Sie es umsetzen und dann **Veröffentlichen** auswählen, um eine erneute Zertifizierung zu starten.

1. Sobald Ihr Angebot erfolgreich zertifiziert wurde, nutzen Sie den bereitgestellten Link, um das Angebot in der Vorschau anzuzeigen und abschließende Anpassungen vorzunehmen. Wenn Sie fertig sind, wählen Sie **Live schalten** aus (siehe Schaltfläche oben), um Ihr Angebot in relevanten Storefronts des kommerziellen Marketplace zu veröffentlichen.

1. **Fahren Sie mit Szenario 2 unten fort, um den Zusammenführungsvorgang abzuschließen.**

---

#### <a name="scenario-2-similar-offer-exists-in-commercial-marketplace-please-follow-these-steps"></a>Szenario 2: Auszuführende Schritte, wenn bereits ein ähnliches Angebot im kommerziellen Marketplace vorhanden ist

Wählen Sie diese Option aus, wenn die Lösung bereits im kommerziellen Marketplace veröffentlicht und live geschaltet ist und die Lösung in OCP GTM mit dem Angebot im kommerziellen Marketplace in einem einzigen Angebot zusammengeführt werden soll. Dadurch wird die Erstellung doppelter Angebote vermieden.

1. Identifizieren Sie das vorhandene Angebot.
   1. Wählen Sie **Dies ähnelt einem vorhandenen Angebot im kommerziellen Marketplace** aus (siehe Abbildung **Beginn Ihrer Migration** [oben](#beginmigration)).
   1. Auf der Registerkarte **Aktion 1** werden Liveangebote im kommerzielle Marketplace angezeigt, mit denen Ihre OCP GTM-Lösung verknüpft werden kann. Wählen Sie das Liveangebot in der Liste aus, sofern vorhanden. Wenn keine Angebotsliste für die Auswahl verfügbar ist, geben Sie die kundenorientierte Adresse (URL) aus Microsoft AppSource oder Azure Marketplace ein.
   1. Wählen Sie **Weiter**.
        [![Registerkarte „Aktion 1“ des Zusammenführungsvorgangs](media/co-sell-migrate/action-1-merge.png)](media/co-sell-migrate/action-1-merge.png#lightbox)
1. Fordern Sie die Zusammenführung an.
    1. Auf der Registerkarte **Aktion 2** werden Anweisungen dazu angezeigt, wie Sie die Zusammenführung Ihrer OCP GTM-Lösung mit der von Ihnen identifizierten Lösung anfordern. Um die Zusammenführung anzufordern, wählen Sie **Save & Contact support** (Speichern und Support kontaktieren) aus, wodurch eine Seite für den Partnersupport in einem Browser geöffnet wird. 
    <a id="action-2-merge"></a>
        [![Registerkarte „Aktion 2“ des Zusammenführungsvorgangs](media/co-sell-migrate/action-2-merge.png)](media/co-sell-migrate/action-2-merge.png#lightbox)
    1. Wählen Sie **Problemdetails angeben** aus, und geben Sie Folgendes ein:
        1. Angebotstyp
        1. Angebotsname
        1. OCP-Lösungs-ID
        1. URL für vorhandene Listung
    1. Klicken Sie auf **Submit** (Senden). Das Team des Partnersupports wird sich innerhalb von zwei Werktagen bei Ihnen melden.
    1. Der Partnersupport arbeitet mit Ihnen zusammen, um eine erfolgreiche Zusammenführung dieses Angebots und dessen Veröffentlichung als Liveangebot sicherzustellen.

### <a name="discard-this-solution"></a>Verwerfen dieser Lösung

Es wird empfohlen, sich die Zeit zu nehmen, die Verwendbarkeit und Relevanz Ihrer Lösungen zu bewerten. Dadurch können Sie Ihre Präsenz und Auffindbarkeit im kommerziellen Marketplace mit qualitativ hochwertigen Angeboten optimieren, die den Kundenanforderungen entsprechen.

Wählen Sie diese Option aus, wenn eine Lösung aus den OCP GTM-Lösungen nicht mehr relevant ist. Sie werden aufgefordert, das Verwerfen zu bestätigen, und Sie können den Vorgang später auch rückgängig machen.

1. Wählen Sie **I want to discard this solution** (Ich möchte diese Lösung verwerfen) aus (siehe Abbildung **Aktion erforderlich** [oben](#beginmigration)). Wählen Sie dann **Weiter** aus.
2. Wählen Sie **Verwerfen** aus.

    :::image type="content" source="media/co-sell-migrate/migration-discard.png" alt-text="Bestätigen Sie das Verwerfen":::.

3. Um das Verwerfen rückgängig zu machen, wählen Sie **„Verwerfen“ rückgängig machen** aus.

    :::image type="content" source="media/co-sell-migrate/migration-discard-undo.png" alt-text="Link „‚Verwerfen‘ rückgängig machen“":::

### <a name="additional-help"></a>Zusätzliche Hilfe

 Wenn Sie zusätzliche Hilfe benötigen, wählen Sie die Registerkarte **Hilfe** aus, um das Team des Partnersupports zu kontaktieren.

:::image type="content" source="media/co-sell-migrate/get-support-link.png" alt-text="Supportlink auf der Registerkarte „Hilfe“":::

## <a name="next-steps"></a>Nächste Schritte

- [Verkaufen über CSP-Partner](cloud-solution-providers.md)
- [Konfigurieren von Co-Selling für ein Angebot im kommerziellen Marketplace](./co-sell-configure.md)
- Lesen Sie diese [FAQ](https://partner.microsoft.com/resources/detail/co-sell-requirements-publish-commercial-marketplace-faq-pdf) (PDF)
