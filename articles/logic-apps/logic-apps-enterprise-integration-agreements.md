---
title: Definieren von Vereinbarungen zwischen Partnern in Workflows
description: Fügen Sie Ihrem Integrationskonto Vereinbarungen für Workflows in Azure Logic Apps mithilfe des Enterprise Integration Pack hinzu.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 3a1b714be1f6eb70a4780c7abf58f13a45eb3f3f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128584161"
---
# <a name="add-agreements-between-partners-to-integration-accounts-for-workflows-in-azure-logic-apps"></a>Hinzufügen von Vereinbarungen zwischen den Partnern zu den Integrationskonten für Workflows in Azure Logic Apps

Nachdem Sie Ihrem Integrationskonto Partner hinzugefügt haben, geben Sie an, wie Partner Nachrichten austauschen, indem Sie [*Vereinbarungen*](logic-apps-enterprise-integration-agreements.md) in Ihrem Integrationskonto definieren. Vereinbarungen unterstützen Organisationen bei der nahtlosen Kommunikation, indem sie das spezifische Branchenstandardprotokoll für den Austausch von Nachrichten definieren. Außerdem bieten sie die folgenden gemeinsamen Vorteile:

* Sie ermöglichen Organisationen den Austausch von Informationen über ein bekanntes Format.

* Sie erhöhen die Effizienz beim Durchführen von B2B-Transaktionen (Business-to-Business).

* Sie vereinfachen das Erstellen, Verwalten und Verwenden von Vereinbarungen zum Erstellen von Lösungen für die Unternehmensintegration.

Eine Vereinbarung erfordert einen *Hostpartner*, der immer Ihre Organisation ist, und einen *Gastpartner*, bei dem es sich um die Organisation handelt, die Nachrichten mit Ihrer Organisation austauscht. Der Gastpartner kann ein anderes Unternehmen oder sogar eine Abteilung innerhalb Ihrer eigenen Organisation sein. Mithilfe dieser Vereinbarung geben Sie an, wie ein- und ausgehende Nachrichten aus Sicht des Hostpartners behandelt werden sollen.

In diesem Artikel erfahren Sie, wie Sie eine Vereinbarung, mit der Sie B2B-Nachrichten mit einem anderen Partner über AS2-, X12-, EDIFACT- oder RosettaNet-Vorgänge austauschen können, erstellen und verwalten.

Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](logic-apps-overview.md). Weitere Informationen zur B2B-Unternehmensintegration finden Sie unter [Workflows für die B2B-Unternehmensintegration mit Azure Logic Apps und dem Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Sollten Sie noch kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine [Integrationskontoressource](logic-apps-enterprise-integration-create-integration-account.md), in der Sie Artefakte wie Handelspartner, Vereinbarungen, Zertifikate usw. für die Verwendung in Ihrer Unternehmensintegration und in B2B-Workflows definieren und speichern. Diese Ressource muss die folgenden Anforderungen erfüllen:

  * Sie muss demselben Azure-Abonnement zugeordnet sein wie Ihre Logik-App-Ressource.

  * Sie muss sich am selben Standort oder in derselben Azure-Region wie Ihre Logik-App-Ressource befinden.

  * Wenn Sie den [Ressourcentyp **Logik-App (Verbrauch)** ](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, benötigt Ihr Integrationskonto eine [Verbindung mit Ihrer Logik-App-Ressource](logic-apps-enterprise-integration-create-integration-account.md#link-account), bevor Sie Artefakte in Ihrem Workflow verwenden können.

  * Wenn Sie den [Ressourcentyp **Logik-App (Standard)** ](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, benötigt Ihr Integrationskonto keine Verbindung mit Ihrer Logik-App-Ressource. Sie ist aber trotzdem erforderlich, um andere Artefakte wie Partner, Vereinbarungen und Zertifikate zusammen mit den [AS2](logic-apps-enterprise-integration-as2.md)-, [X12](logic-apps-enterprise-integration-x12.md)- und [EDIFACT](logic-apps-enterprise-integration-edifact.md)-Vorgängen zu speichern. Ihr Integrationskonto muss darüber hinaus weitere Anforderungen erfüllen. So muss es z. B. dasselbe Azure-Abonnement und denselben Standort wie Ihre Logik-App-Ressource verwenden.

  > [!NOTE]
  > Derzeit unterstützt nur der Ressourcentyp **Logik-App (Verbrauch)** [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)-Vorgänge. Der Ressourcentyp **Logik-App (Standard)** umfasst keine [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)-Vorgänge.

* Mindestens zwei [Parteien](logic-apps-enterprise-integration-partners.md) (Handelspartner) in Ihrem Integrationskonto. Eine Vereinbarung erfordert sowohl einen Host- als auch einen Gastpartner. Außerdem erfordert eine Vereinbarung, dass beide Partner denselben oder einen kompatiblen Qualifizierer für die *Geschäftsidentität* verwenden, der für eine AS2-, X12-, EDIFACT- oder RosettaNet-Vereinbarung geeignet ist.

* Optional die Logik-App-Ressource und den Workflow, in denen Sie die Vereinbarung zum Austauschen von Nachrichten verwenden möchten. Für den Workflow ist ein Trigger erforderlich, der den Workflow Ihrer Logik-App startet.

Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](logic-apps-overview.md) und [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](quickstart-create-first-logic-app-workflow.md).

## <a name="add-an-agreement"></a>Hinzufügen einer Vereinbarung

1. Geben Sie im [Azure-Portal](https://portal.azure.com) in das Suchfeld `integration accounts` ein, und wählen Sie **Integrationskonten** aus.

1. Wählen Sie unter **Integrationskonten** das Integrationskonto aus, dem Sie Ihre Partner hinzufügen möchten.

1. Wählen Sie im Menü des Integrationskontos unter **Einstellungen** den Eintrag **Vereinbarungen** aus.

1. Wählen Sie im Bereich **Vereinbarungen** die Option **Hinzufügen** aus.

1. Geben Sie im Bereich **Hinzufügen** die folgenden Informationen zu der Vereinbarung an:

   | Eigenschaft | Erforderlich | Wert | Beschreibung |
   |----------|----------|-------|-------------|
   | **Name** | Ja | <*agreement-name*> | Der Name für Ihre Vereinbarung. |
   | **Vereinbarungstyp** | Ja | **AS2**, **X12**, **EDIFACT** oder **RosettaNet** | Der Protokolltyp für Ihre Vereinbarung. Wenn Sie Ihre Vereinbarungsdatei erstellen, muss der Inhalt dieser Datei mit dem Vereinbarungstyp übereinstimmen. |
   | **Hostpartner** | Ja | <*host-partner-name*> | Der Hostpartner repräsentiert Ihre Organisation. |
   | **Hostidentität** | Ja | <*host-partner-identifier*> | Der Bezeichner des Hostpartners. |
   | **Gastpartner** | Ja | <*guest-partner-name*> | Der Gastpartner repräsentiert die Organisation, die mit Ihrer Organisation kommuniziert. |
   | **Gastidentität** | Ja | <*guest-partner-identifier*> | Der Bezeichner des Gastpartners. |
   | **Empfangseinstellungen** | Varies | Varies | Diese Eigenschaften legen fest, wie der Hostpartner eingehende Nachrichten vom Gastpartner in der Vereinbarung empfängt. Weitere Informationen finden Sie unter dem jeweiligen Vereinbarungstyp: <p>- [AS2-Nachrichteneinstellungen](logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT-Nachrichteneinstellungen](logic-apps-enterprise-integration-edifact.md) <br>- [X12-Nachrichteneinstellungen](logic-apps-enterprise-integration-x12.md) |
   | **Sendeeinstellungen** | Varies | Varies | Diese Eigenschaften legen fest, wie der Hostpartner ausgehende Nachrichten an den Gastpartner in der Vereinbarung sendet. Weitere Informationen finden Sie unter dem jeweiligen Vereinbarungstyp: <p>- [AS2-Nachrichteneinstellungen](logic-apps-enterprise-integration-as2-message-settings.md) <br>- [EDIFACT-Nachrichteneinstellungen](logic-apps-enterprise-integration-edifact.md) <br>- [X12-Nachrichteneinstellungen](logic-apps-enterprise-integration-x12.md) |
   | **RosettaNet-PIP-Verweise** | Varies | Varies | In diesem Bereich werden Informationen zu PIPs (Partner Interface Processes) zur Verwendung von RosettaNet-Nachrichten angegeben. Weitere Informationen finden Sie unter [Austauschen von RosettaNet-Nachrichten](logic-apps-enterprise-integration-rosettanet.md). |
   |||||

   > [!IMPORTANT]
   > Die Auflösung für eine Vereinbarung hängt von der Übereinstimmung der folgenden Elemente ab, die für den Partner und in der eingehenden Nachricht definiert sind:
   >
   > * Qualifizierer und Bezeichner des Absenders
   > * Qualifizierer und Bezeichner des Empfängers
   >
   > Falls sich diese Werte für Ihren Partner ändern, stellen Sie sicher, dass Sie die Vereinbarung entsprechend aktualisieren.

1. Wenn Sie fertig sind, wählen Sie **OK**.

   Ihre Vereinbarung wird nun in der Liste **Vereinbarungen** angezeigt.

## <a name="edit-an-agreement"></a>Bearbeiten einer Vereinbarung

1. Geben Sie im [Azure-Portal](https://portal.azure.com) in das Suchfeld `integration accounts` ein, und wählen Sie **Integrationskonten** aus.

1. Wählen Sie unter **Integrationskonten** das Integrationskonto aus, dem Sie Ihre Partner hinzufügen möchten.

1. Wählen Sie im Menü des Integrationskontos unter **Einstellungen** den Eintrag **Vereinbarungen** aus.

1. Wählen Sie im Bereich **Vereinbarungen** Ihre Vereinbarung und dann **Bearbeiten** aus, und nehmen Sie Ihre Änderungen vor.

1. Wenn Sie fertig sind, wählen Sie **OK**.

## <a name="delete-an-agreement"></a>Löschen einer Vereinbarung

1. Geben Sie im [Azure-Portal](https://portal.azure.com) in das Suchfeld `integration accounts` ein, und wählen Sie **Integrationskonten** aus.

1. Wählen Sie unter **Integrationskonten** das Integrationskonto aus, dem Sie Ihre Partner hinzufügen möchten.

1. Wählen Sie im Menü des Integrationskontos unter **Einstellungen** den Eintrag **Vereinbarungen** aus.

1. Wählen Sie im Bereich **Vereinbarungen** die zu löschende Vereinbarung und dann **Löschen** aus.

1. Wählen Sie **Ja** aus, um zu bestätigen, dass Sie die Vereinbarung löschen möchten.

## <a name="next-steps"></a>Nächste Schritte

* [Austauschen von AS2-Nachrichten](logic-apps-enterprise-integration-as2.md)
* [Austauschen von EDIFACT-Nachrichten](logic-apps-enterprise-integration-edifact.md)
* [Austauschen von X12-Nachrichten](logic-apps-enterprise-integration-x12.md)
* [Austauschen von RosettaNet-Nachrichten](logic-apps-enterprise-integration-rosettanet.md)
