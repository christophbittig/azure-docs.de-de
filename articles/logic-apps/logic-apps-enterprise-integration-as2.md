---
title: Senden und Empfangen von AS2-Nachrichten für B2B
description: Austauschen von AS2-Nachrichten für Szenarien der B2B-Unternehmensintegration mithilfe von Azure Logic Apps mit Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 699bc3b15c47e1dc80dbb8a4defd27adad298ba0
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122514684"
---
# <a name="exchange-as2-messages-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Austauschen von AS2-Nachrichten für die B2B-Unternehmensintegration in Azure Logic Apps mit Enterprise Integration Pack

> [!IMPORTANT]
> Der ursprüngliche AS2-Connector wird als veraltet markiert, verwenden Sie stattdessen den **AS2 (v2)** -Connector, es sei denn, Sie benötigen Nachverfolgungsfunktionen. Mit Ausnahme der Nachverfolgung bietet der v2-Connector eine bessere Leistung, die gleichen Funktionen wie die ursprüngliche Version, ist in der Azure Logic Apps-Runtime nativ und sorgt für erhebliche Leistungsverbesserungen in Bezug auf Nachrichtengröße, -durchsatz und -wartezeit. Außerdem erfordert der v2-Connector keine Verbindung mit Ihrem Integrationskonto. Stellen Sie stattdessen sicher, dass Sie, wie in den Voraussetzungen beschrieben, Ihr Integrationskonto mit der Logik-App verbinden, in der Sie den Connector verwenden möchten.

Wenn Sie mit AS2-Nachrichten in Azure Logic Apps arbeiten möchten, können Sie den AS2-Connector verwenden, der Auslöser und Aktionen bereitstellt, die die AS2-Kommunikation (Version 1.2) unterstützen und verwalten. Um z. B. Sicherheit und Zuverlässigkeit bei der Übertragung von Nachrichten zu gewährleisten, können Sie die folgenden Aktionen verwenden:

* [Aktion **AS2-Nachricht codieren**](#encode) zum Bereitstellen von Verschlüsselung, einer digitalen Signatur und Bestätigungen durch Benachrichtigungen über den Nachrichtenstatus (Message Disposition Notifications, MDNs), die beim Unterstützen der Nichtabstreitbarkeit helfen. Diese Aktion wendet z. B. AS2/HTTP-Header an und führt die folgenden Aufgaben aus, wenn sie konfiguriert sind:

  * Signieren ausgehender Nachrichten
  * Verschlüsseln ausgehender Nachrichten
  * Komprimieren der Nachricht
  * Dateinamen im MIME-Header übertragen

* [Aktion **AS2-Nachricht decodieren**](#decode) zum Bereitstellen von Entschlüsselung, einer digitalen Signatur und Bestätigungen durch Benachrichtigungen über den Nachrichtenstatus (Message Disposition Notifications, MDNs). Diese Aktion führt z. B. die folgenden Aufgaben aus:

  * Verarbeiten von AS2/HTTP-Headern
  * Abstimmen empfangener MDNs mit der ursprünglichen ausgehenden Nachricht
  * Aktualisieren und Korrelieren von Datensätzen in der Nichtabstreitbarkeits-Datenbank
  * Schreiben von Datensätzen für AS2-Statusberichte
  * Base64-codierte Ausgabe von Nutzlastinhalten
  * Ermitteln, ob MDNs erforderlich sind Ermittlung auf Grundlage der AS2-Vereinbarung, ob MDNs synchron oder asynchron sein müssen
  * Generieren einer synchronen oder asynchronen MDN auf Grundlage der AS2-Vereinbarung
  * Festlegen der Korrelationstoken und Eigenschaften für MDNs

  Diese Aktion führt auch die folgenden Aufgaben aus, wenn sie konfiguriert ist:

  * Überprüfen der Signatur
  * Entschlüsseln der Nachrichten
  * Dekomprimieren der Nachrichten
  * Suchen nach und Unterbinden von doppelten Nachrichten-IDs

In diesem Artikel wird gezeigt, wie Sie die AS2-Aktionen für die Codierung und Decodierung zu einer bestehenden Logik-App hinzufügen können.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie noch kein Azure-Abonnement haben, [melden Sie sich für ein kostenloses Azure-Konto an](https://azure.microsoft.com/free/).

* Die Logik-App, von der aus Sie auf den AS2-Connector zugreifen möchten, und einen Trigger, der den Workflow Ihre Logik-App startet. Der AS2-Connector stellt nur Aktionen und keine Trigger bereit. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md) und [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* Ein [Integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), das Ihrem Azure-Abonnement zugeordnet und mit der Logik-App verknüpft ist, in der Sie den AS2-Connector verwenden möchten. Sowohl Ihre Logik-App als auch Ihr Integrationskonto müssen an demselben Standort oder in derselben Azure-Region vorhanden sein.

* Mindestens zwei [Parteien](../logic-apps/logic-apps-enterprise-integration-partners.md), die Sie bereits in Ihrem Integrationskonto unter Verwendung des AS2-Identitätsqualifizierers definiert haben.

* Bevor Sie den AS2-Connector verwenden können, müssen Sie eine AS2-[Vereinbarung](../logic-apps/logic-apps-enterprise-integration-agreements.md) zwischen den Parteien erstellen und diese Vereinbarung in Ihrem Integrationskonto speichern.

* Wenn Sie [Azure Key Vault](../key-vault/general/overview.md) für die Zertifikatsverwaltung verwenden, überprüfen Sie, ob Ihre Tresorschlüssel die Vorgänge **Verschlüsseln** und **Entschlüsseln** zulassen. Andernfalls treten beim Codieren und Decodieren Fehler auf.

  Wechseln Sie im Azure-Portal zu dem Schlüssel in Ihrem Schlüsseltresor, prüfen Sie die **Zulässigen Vorgänge** für Ihren Schlüssel, und bestätigen Sie, dass die Vorgänge **Verschlüsseln** und **Entschlüsseln** ausgewählt sind, z. B.:

  ![Überprüfen der Tresorschlüsselvorgänge](media/logic-apps-enterprise-integration-as2/key-vault-permitted-operations.png)

<a name="encode"></a>

## <a name="encode-as2-messages"></a>Codieren von AS2-Nachrichten

1. Öffnen Sie, falls noch nicht geschehen, Ihre Logik-App über das [Azure-Portal](https://portal.azure.com) im Designer für Logik-Apps.

1. Fügen Sie im Designer eine neue Aktion zu Ihrer Logik-App hinzu.

1. Wählen Sie unterhalb von **Aktion auswählen** und unterhalb des Suchfelds die Option **Alle** aus. Geben Sie im Suchfeld „AS2-Codierung“ ein, und wählen Sie die AS2 (v2)-Aktion aus: **AS2-Codierung**

   ![Auswählen von „AS2-Codierung“](./media/logic-apps-enterprise-integration-as2/select-as2-encode.png)

1. Geben Sie jetzt Informationen zu diesen Eigenschaften an:

   | Eigenschaft | BESCHREIBUNG |
   |----------|-------------|
   | **Zu codierende Nachricht** | Die Nachrichtennutzlast. |
   | **AS2-Absender** | Der Bezeichner für den Absender der Nachricht, wie in Ihrer AS2-Vereinbarung angegeben. |
   | **AS2-Empfänger** | Der Bezeichner für den Empfänger der Nachricht, wie in Ihrer AS2-Vereinbarung angegeben. |
   |||

   Beispiel:

   ![Eigenschaften für die Nachrichtencodierung](./media/logic-apps-enterprise-integration-as2/as2-message-encoding-details.png)

> [!TIP]
> Wenn beim Senden von signierten oder verschlüsselten Nachrichten Probleme auftreten, sollten Sie andere SHA256-Algorithmusformate ausprobieren. Die AS2-Spezifikation stellt keine Informationen zu SHA256-Formaten bereit, sodass jeder Anbieter seine eigene Implementierung bzw. sein eigenes Format verwendet.

<a name="decode"></a>

## <a name="decode-as2-messages"></a>Decodieren von AS2-Nachrichten

1. Öffnen Sie, falls noch nicht geschehen, Ihre Logik-App über das [Azure-Portal](https://portal.azure.com) im Designer für Logik-Apps.

1. Fügen Sie im Designer eine neue Aktion zu Ihrer Logik-App hinzu.

1. Wählen Sie unterhalb von **Aktion auswählen** und unterhalb des Suchfelds die Option **Alle** aus. Geben Sie im Suchfeld „AS2-Decodierung“ ein, und wählen Sie die AS2 (v2)-Aktion aus: **AS2-Decodierung**

   ![Auswählen von „AS2-Decodierung“](media/logic-apps-enterprise-integration-as2/select-as2-decode.png)

1. Wählen Sie für die Eigenschaften **Zu codierende Nachricht** und **Nachrichtenheader** diese Werte aus vorherigen Trigger- oder Aktionsausgaben aus.

   Angenommen, Ihre Logik-App empfängt Nachrichten über einen Anforderungstrigger. Sie können die Ausgaben von diesem Trigger auswählen.

   ![Wählen Sie bei den Ausgaben der Anforderung den Text und die Header aus.](media/logic-apps-enterprise-integration-as2/as2-message-decoding-details.png)

## <a name="sample"></a>Beispiel

Wenn Sie eine uneingeschränkt funktionsfähige Logik-App und ein AS2-Beispielszenario bereitstellen möchten, sehen Sie sich [Azure Logic Apps - AS2 Send Receive](https://azure.microsoft.com/resources/templates/logic-app-as2-send-receive/) (Azure Logic Apps – AS2: Senden/Empfangen) an.

## <a name="connector-reference"></a>Connector-Referenz

Weitere technische Details zu diesem Connector, z. B. Aktionen und Grenzwerte, wie sie in der Swagger-Datei des Connectors beschrieben werden, finden Sie auf der [Referenzseite des Connectors](/connectors/as2/). 

> [!NOTE]
> Für Logik-Apps in einer [Integrationsdienstumgebung (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) verwendet die mit ISE bezeichnete Version dieses Connectors die [B2B-Nachrichtengrenzwerte für ISE](../logic-apps/logic-apps-limits-and-config.md#b2b-protocol-limits).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)
