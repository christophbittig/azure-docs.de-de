---
title: Definieren von Handelspartnern für Workflows
description: Fügen Sie Ihrem Integrationskonto Handelspartner für Workflows in Azure Logic Apps mithilfe von Enterprise Integration Pack hinzu.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/16/2021
ms.openlocfilehash: f0e0fed5bf7e3354cffa8d6799c4d3e5d39595d9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128549833"
---
# <a name="add-trading-partners-to-integration-accounts-for-workflows-in-azure-logic-apps"></a>Fügen Sie Handelspartner zu den Integrationskonten für Workflows in Azure Logic Apps hinzu

Um Ihre Organisation und andere Unternehmen in Workflows der Unternehmensintegration Business-to-Business (B2B) zu repräsentieren, erstellen Sie in Ihrem [Integrationskonto](logic-apps-enterprise-integration-create-integration-account.md) einen *Handelspartner*, um jeden Teilnehmer an einer Geschäftsbeziehung repräsentiert. Partner sind Geschäftsentitäten, die an B2B-Transaktionen teilnehmen und untereinander Nachrichten austauschen.

> [!IMPORTANT]
> Bevor Sie diese Partner definieren, sollten Sie mit Ihren Partnern darüber sprechen, wie Sie die Nachrichten identifizieren und überprüfen, die Sie einander senden. Die Partner in Ihrem Integrationskonto müssen die gleichen oder kompatiblen *Geschäftsqualifizierer* verwenden, um an einer Vereinbarung teilzunehmen und Nachrichten miteinander austauschen zu können. Sobald Sie diese Details festgelegt haben, können Sie Partner in Ihrem Integrationskonto erstellen.

In diesem Artikel erfahren Sie, wie Sie die Partner erstellen und verwalten, die Sie später verwenden können, um Vereinbarungen zu erstellen, die das spezifische Branchenstandardprotokoll für den Nachrichtenaustausch zwischen Partnern definieren.

Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](logic-apps-overview.md). Weitere Informationen zur B2B-Unternehmensintegration finden Sie unter [Workflows für die B2B-Unternehmensintegration mit Azure Logic Apps](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Sollten Sie noch kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine [Integrationskontoressource](logic-apps-enterprise-integration-create-integration-account.md), in der Sie Artefakte wie Handelspartner, Vereinbarungen, Zertifikate usw. für die Verwendung in Ihrer Unternehmensintegration und in B2B-Workflows definieren und speichern. Diese Ressource muss die folgenden Anforderungen erfüllen:

  * Sie muss demselben Azure-Abonnement zugeordnet sein wie Ihre Logik-App-Ressource.

  * Sie muss sich am selben Standort oder in derselben Azure-Region wie Ihre Logik-App-Ressource befinden.

  * Wenn Sie den [Ressourcentyp **Logik-App (Verbrauch)** ](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, benötigt Ihr Integrationskonto einen [Link zu Ihrer Logik-App-Ressource](logic-apps-enterprise-integration-create-integration-account.md#link-account), bevor Sie Artefakte in Ihrem Workflow verwenden können.

  * Wenn Sie den [Ressourcentyp **Logik-App (Standard)** ](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, benötigt Ihr Integrationskonto keinen Link zu Ihrer Logik-App-Ressource. Es ist aber trotzdem erforderlich, um andere Artefakte wie Partner, Vereinbarungen und Zertifikate zusammen mit den [AS2](logic-apps-enterprise-integration-as2.md)-, [X12](logic-apps-enterprise-integration-x12.md)- und [EDIFACT](logic-apps-enterprise-integration-edifact.md)-Vorgängen zu speichern. Ihr Integrationskonto muss darüber hinaus weitere Anforderungen erfüllen. So muss es z. B. dasselbe Azure-Abonnement und denselben Standort wie Ihre Logik-App-Ressource verwenden.

  > [!NOTE]
  > Derzeit unterstützt nur der Ressourcentyp **Logik-App (Verbrauch)** [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)-Vorgänge. Der Ressourcentyp **Logik-App (Standard)** umfasst keine [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)-Vorgänge.

<a name="add-partner"></a>

## <a name="add-a-partner"></a>Fügen Sie einen Partner hinzu

1. Geben Sie im [Azure-Portal](https://portal.azure.com) in das Suchfeld `integration accounts` ein, und wählen Sie **Integrationskonten** aus.

1. Wählen Sie unter **Integrationskonten** das Integrationskonto aus, dem Sie Ihre Partner hinzufügen möchten.

1. Wählen Sie im Menü des Integrationskontos unter **Einstellungen** den Eintrag **Partner** aus.

1. Wählen Sie im Bereich **Partner** die Option **Hinzufügen** aus.

1. Geben Sie im Bereich **Partner hinzufügen** die folgenden Informationen zu dem Partner an:

   | Eigenschaft | Erforderlich | Beschreibung |
   |----------|----------|-------------|
   | **Name** | Ja | Der Name des Partners |
   | **Qualifizierer** | Ja | Der authentifizierende Text, der Organisationen eindeutige Geschäftsidentitäten verleiht, z. B. **D-U-N-S (Dun & Bradstreet)** . <p>Partner können ihre Geschäftsidentitäten gemeinsam definieren. Wählen Sie dafür unter EDIFACT **Einvernehmlich definiert** oder unter X12 **Einvernehmlich festgelegt (X12)** aus. <p>Wählen Sie unter RosettaNet hingegen nur **DUNS** aus (die Standardeinstellung). <p>**Wichtig**: Damit die Partner in Ihrem Integrationskonto an einer Vereinbarung teilnehmen und untereinander Nachrichten austauschen können, müssen sie denselben oder einen kompatiblen Qualifizierer verwenden. |
   | **Wert** | Ja | Ein Wert, der die Dokumente ermittelt, die Ihre Logik-Apps empfangen. <p>Bei Partnern, die RosettaNet verenden muss dieser Wert neun Ziffern umfassen, die der DUNS-Nummer entsprechen. Sie können weitere Informationen für RosettaNet-Partner bereitstellen, wie z. B. ihre Klassifizierung und Kontaktinformationen, indem Sie zuerst die Partner erstellen und [anschließend ihre Definitionen bearbeiten](#edit-partner). |
   ||||

1. Wenn Sie fertig sind, wählen Sie **OK**.

   Ihr Partner sollte nun auf der Liste **„Partner“** angezeigt werden.

<a name="edit-partner"></a>

## <a name="edit-a-partner"></a>Einen Partner bearbeiten

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihr Integrationskonto.

1. Wählen Sie im Menü des Integrationskontos unter **Einstellungen** den Eintrag **Partner** aus.

1. Wählen Sie im Bereich **Partner** den Partner aus, wählen Sie dann **Bearbeiten** aus und nehmen Sie Ihre Änderungen vor.

   Für Partner, die RosettaNet verwenden, können Sie unter **RosettaNet-Partnereigenschaften** weitere Informationen bereitstellen, wie in der folgenden Tabelle beschrieben:

   | Eigenschaft | Erforderlich | BESCHREIBUNG |
   |----------|----------|-------------|
   | **Partner Classification** (Partnerklassifizierung) | Nein | Der Typ der Organisation des Partners |
   | **Lieferkettencode** | Nein | Der Lieferkettentyp des Partners, z. B. „IT“ oder „Elektronische Komponenten“. |
   | **Kontaktname** | Nein | Der Kontaktname des Partners |
   | **E-Mail** | Nein | Die E-Mail-Adresse des Partners |
   | **Fax** | Nein | Die Faxnummer des Partners |
   | **Telefon** | Nein | Die Telefonnummer des Partners |
   ||||

1. Wenn Sie fertig sind, wählen Sie **OK**.

<a name="delete-partner"></a>

## <a name="delete-a-partner"></a>Löschen eines Partners

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) Ihr Integrationskonto.

1. Wählen Sie im Menü des Integrationskontos unter **Einstellungen** den Eintrag **Partner** aus.

1. Wählen Sie im Bereich **Partner** den zu löschenden Partner und dann **Löschen** aus.

1. Wählen Sie **Ja** aus, um zu bestätigen, dass Sie den Partner löschen möchten.

## <a name="next-steps"></a>Nächste Schritte

* [Hinzufügen von Partnervereinbarungen](logic-apps-enterprise-integration-agreements.md)