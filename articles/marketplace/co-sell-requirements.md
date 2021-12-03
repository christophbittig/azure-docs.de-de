---
title: Co-Selling-Anforderungen | Azure Marketplace
description: Hier erfahren Sie mehr über die Voraussetzungen, die ein Angebot im kommerziellen Marketplace erfüllen muss, damit es sich für die Status „Co-Sell Ready“ (Bereit für Co-Selling) oder „Co-Sell Incentive“ (Co-Selling-Incentive) qualifiziert.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: vamahtan
ms.author: vamahtan
ms.reviewer: stmummer
ms.date: 10/21/2021
ms.openlocfilehash: 09403aa97df2fe0ab451eee3194b1d6e3b239ab2
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130249436"
---
# <a name="co-sell-requirements"></a>Co-Selling-Anforderungen

In diesem Artikel finden Sie die Voraussetzungen für die verschiedenen Ebenen des Co-Selling-Status. Eine aktuelle Liste der Angebotstypen, die das Co-Selling unterstützen, finden Sie unter [Konfigurieren von Co-Selling für ein Angebot im kommerziellen Marketplace](co-sell-configure.md). Eine Übersicht über das Co-Selling finden Sie unter [Übersicht über das Co-Selling mit Vertriebsteams und Partnern von Microsoft](co-sell-overview.md).

In der folgenden Tabelle werden alle möglichen Co-Selling-Status aufgeführt:

| Status | Comment |
| ------------ | ------------- |
| Not Co-Sell Ready (Nicht für Co-Selling bereit) | Die [Mindestanforderungen für den Status „Co-Sell Ready“](#requirements-for-co-sell-ready-status) (Bereit für Co-Selling) wurden nicht erfüllt. |
| Co-Sell Ready | Alle [Anforderungen für den Status „Co-Sell Ready“](#requirements-for-co-sell-ready-status) wurden erfüllt. |
| Azure IP Co-Sell Incentive | Die Anforderungen für den Status „Co-Sell Ready“ sowie [diese zusätzlichen Anforderungen](#requirements-for-azure-ip-co-sell-incentive-status) wurden erfüllt. |
| Business Applications Co-Sell Incentive | Dieser Status gilt für Dynamics 365- und Power Apps-Angebote im [Microsoft Business Applications ISV Connect Program](business-applications-isv-program.md) und gibt an, dass alle [ Anforderungen für diesen Status](#requirements-for-business-applications-co-sell-incentive-status) erfüllt wurden. |
|||

## <a name="requirements-for-co-sell-ready-status"></a>Anforderungen für den Status „Co-Sell Ready“

> [!NOTE]
> Jedes Angebot, das über das Entwicklerprogramm des kommerziellen Marketplace in Partner Center veröffentlicht wird, ist für den Status „Co-Sell Ready“ berechtigt, sofern die entsprechenden Anforderungen erfüllt sind. Office-Apps und -Add-Ins sind nicht berechtigt (z. B. Teams, SharePoint-Lösungen, Outlook, Excel).

Damit ein Angebot den Status „Co-Sell Ready“ erhalten kann, müssen die folgenden Anforderungen erfüllt werden:

**Alle Partner:**

- Sie müssen über eine MPN-ID (Microsoft Partner Network) und ein aktives [Konto für den kommerziellen Marketplace in Partner Center](create-account.md) verfügen.
- Sie müssen über ein vollständiges [Geschäftsprofil](/partner-center/create-a-marketing-profile) in Partner Center verfügen. Als qualifizierter Microsoft-Partner von Microsoft können Sie Kunden mit Ihrem Geschäftsprofil von Ihrem Unternehmen überzeugen, wenn diese nach Ihren einzigartigen Lösungen und Fachkenntnissen suchen, um ihre eigenen geschäftlichen Anforderungen zu bewältigen, was zu [Empfehlungen](/partner-center/referrals) führt.
- Füllen Sie die Registerkarte **Co-Selling mit Microsoft** aus, und veröffentlichen Sie das Angebot im kommerziellen Marketplace.
- Geben Sie für jeden zum Co-Selling berechtigten Standort einen Vertriebskontakt und die erforderliche Stückliste an.

**Dienstleistungspartner:**

- Für Angebote vom Typ _Service Solution_ (Dienstleistung) müssen Sie in jedem Kompetenzbereich über Gold-Kompetenz verfügen.

**Geschäftsanwendungen von unabhängigen Softwareanbietern:**

- Dynamics 365 und Power Apps (mit Ausnahme von Dynamics 365 Business Central)-Lösungen erfordern eine ISV Connect-Registrierung.

### <a name="complete-the-co-sell-with-microsoft-tab"></a>Ausfüllen der Registerkarte „Co-Selling mit Microsoft“

Geben Sie beim Veröffentlichen oder Ändern Ihres Angebots wie unter [Konfigurieren von Co-Selling für ein Angebot im kommerziellen Marketplace](./co-sell-configure.md) beschrieben alle erforderlichen Informationen auf der Registerkarte **Co-Selling mit Microsoft** an. Dazu gehören die folgenden Dokumente:

- Onepager zur Lösung bzw. zum Angebot
- Pitch Deck zur Lösung bzw. zum Angebot

Zum Erstellen dieser Dokumente werden Vorlagen zur Verfügung gestellt. Weitere Informationen über die erforderlichen und optionalen Angaben auf der Registerkarte „Co-Selling mit Microsoft“ finden Sie unter [Konfigurieren von Co-Selling für ein Angebot im kommerziellen Marketplace](./co-sell-configure.md).

### <a name="publish-your-offer-live"></a>Veröffentlichen Ihres Angebots im Livemodus

Damit Ihr Angebot oder Ihre Lösung sich für den Status „Co-Sell Ready“ qualifizieren kann, muss es in mindestens einem der kommerziellen Marketplace-Onlineshops veröffentlicht sein: Azure Marketplace oder Microsoft AppSource. Informationen zum Veröffentlichen von Angeboten im kommerziellen Marketplace finden Sie im [Leitfaden zur Veröffentlichung nach Angebotstyp](publisher-guide-by-offer-type.md). Wenn Sie noch kein Angebot im kommerziellen Marketplace veröffentlicht haben, stellen Sie sicher, dass Sie über ein [Konto für den kommerziellen Marketplace](create-account.md) verfügen.

## <a name="requirements-for-azure-ip-co-sell-incentive-status"></a>Anforderungen für den Status „Azure IP Co-Sell Incentive“

Der Status „Azure IP Co-Sell Incentive“ gilt für die folgenden Angebotstypen:

- Azure-Anwendung
- Azure-Container
- Virtueller Azure-Computer
- IoT Edge-Modul
- Software-as-a-Service (SaaS)

Nachdem der Status „Co-Sell Ready“ erreicht wurde, müssen drei zusätzliche Anforderungen für den Status „Azure IP Co-Sell Incentive“ erfüllt werden:

Anforderung 1: Erreichen Sie Folgendes:

- Erzielen Sie auf _Organisationsebene_ den Schwellenwert von mindestens 100.000 USD Azure-Umsatzverbrauch in den letzten 12 Monaten. Dieser Schwellenwert kann durch eine Kombination mehrerer Azure-Angebote erreicht werden. Wenn das Angebot im kommerziellen Marketplace transaktionsfähig ist, können Sie diese Anforderung erfüllen, indem Sie einen abgerechneten Umsatz von 100.000 USD in den letzten 12 Monaten erzielen.

Anforderung 2: Bestehen Sie die technische Prüfung von Microsoft für Azure-basierte Lösungen:
- Bei der technischen Prüfung muss festgestellt werden, dass mehr als 50 % der Infrastruktur Ihres Angebots wiederholbaren IP-Code in Azure verwendet. Beachten Sie, dass transaktionsfähige Angebote für Azure-VMs und Azure-Anwendungen im kommerziellen Marketplace diese Anforderung standardmäßig erfüllen.

Anforderung 3: Stellen Sie ein Diagramm zur Referenzarchitektur bereit:
- Laden Sie ein Referenzarchitekturdiagramm mit Ihren Co-Selling-Dokumenten zur Überprüfung in Partner Center hoch. Eine Anleitung zum Erstellen dieses Diagramms finden Sie unter [Referenzarchitekturdiagramm](reference-architecture-diagram.md). Informationen zum Hochladen des Diagramms finden Sie unter [Konfigurieren von Co-Selling für ein Angebot im kommerziellen Marketplace](./co-sell-configure.md).

## <a name="requirements-for-business-applications-co-sell-incentive-status"></a>Anforderungen für den Status „Business Applications Co-Sell Incentive“

Dieser Status gilt für IP-basierte Lösungen, die auf Dynamics 365 oder PowerApps (mit Ausnahme von Dynamics 365 Business Central) basieren, die im ISV Connect Program registriert sind. Angebote müssen jedoch auch die Anforderungen für den Status der Co-Selling-Bereitstellung erfüllen (oben beschrieben), damit Microsoft-Verkäufer in der Lage sind, das Angebot mit Ihnen gemeinsam zu verkaufen.

## <a name="next-steps"></a>Nächste Schritte

- [Konfigurieren von Co-Selling für ein Angebot im kommerziellen Marketplace](./co-sell-configure.md)