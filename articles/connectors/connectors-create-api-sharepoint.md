---
title: Herstellen einer Verbindung mit SharePoint
description: Hier erfahren Sie, wie Sie Ressourcen in SharePoint Online oder SharePoint Server lokal mithilfe von Azure Logic Apps überwachen und verwalten.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: article
ms.date: 08/11/2021
tags: connectors
ms.openlocfilehash: e53fca8b0e9f8dbca3dba8bd684e71ef25c88738
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122338869"
---
# <a name="connect-to-sharepoint-resources-using-azure-logic-apps"></a>Herstellen einer Verbindung mit SharePoint-Ressourcen mithilfe von Azure Logic Apps

Mit Azure Logic Apps und dem SharePoint-Connector können Sie automatisierte Integrationsworkflows erstellen, um Aufgaben für die Überwachung und Verwaltung von Ressourcen wie Dateien, Ordner, Listen und Elemente in SharePoint Online oder in einer lokalen SharePoint Server-Instanz zu automatisieren.

Die folgende Liste enthält Beispiele für automatisierbare Aufgaben:

* Sie können die Vorgänge überwachen, wenn Dateien oder Elemente erstellt, geändert oder gelöscht werden.
* Sie können Elemente erstellen, abrufen, aktualisieren oder löschen.
* Sie können Anlagen hinzufügen, abrufen oder löschen. Sie können die Inhalte von Anlagen abrufen.
* Sie können Dateien erstellen, kopieren, aktualisieren oder löschen. 
* Sie können Dateieigenschaften aktualisieren. Sie können die Inhalte, Metadaten oder Eigenschaften einer Datei abrufen.
* Sie können Ordner auflisten oder extrahieren.
* Sie können Listen abrufen oder Ansichten auflisten.
* Sie können den Inhaltsgenehmigungsstatus festlegen.
* Sie können Personen auflösen.
* Sie können HTTP-Anforderungen an SharePoint senden.
* Sie können Entitätswerte abrufen.

In Ihrem Logik-App-Workflow können Sie einen Trigger verwenden, der Ereignisse in SharePoint überwacht und die Ausgabe für andere Aktionen verfügbar macht. Anschließend können Sie Aktionen verwenden, um diverse Aufgaben in SharePoint auszuführen. Sie können auch andere Aktionen einschließen, die die Ausgabe von SharePoint-Aktionen verwenden. Wenn Sie beispielsweise regelmäßig Dateien aus SharePoint abrufen, können Sie mithilfe des Office 365 Outlook-Connectors oder des Outlook.com-Connectors E-Mail-Benachrichtigungen zu diesen Dateien und ihren Inhalten senden. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md). Sie können aber auch [diese Schnellstartanleitung zum Erstellen Ihres ersten Beispielworkflows für eine Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md) verwenden.

## <a name="prerequisites"></a>Voraussetzungen

* Ihre Anmeldeinformationen für das Microsoft Office 365-Konto, das Sie mit SharePoint verwenden, wenn Sie sich mit einem [Geschäfts- Schul- oder Unikonto](https://support.microsoft.com/office/what-account-to-use-with-office-and-you-need-one-914e6610-2763-47ac-ab36-602a81068235#bkmk_msavsworkschool) anmelden.

  Sie benötigen diese Anmeldeinformationen, um den Zugriff des Workflows auf Ihr SharePoint-Konto autorisieren zu können.

  > [!NOTE]
  > Wenn Sie [von 21Vianet betriebenes Microsoft Azure](https://portal.azure.cn) verwenden, funktioniert die Azure Active Directory-Authentifizierung (Azure AD) nur mit einem von 21Vianet geführten Konto für Microsoft Office 365 (.cn) und nicht mit .com-Konten.

* Adresse Ihrer SharePoint-Website

* Ein Azure-Konto und ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Verbindungen mit einem lokalen SharePoint-Server erfordern die [Installation und Einrichtung des lokalen Datengateways](../logic-apps/logic-apps-gateway-install.md) auf einem lokalen Computer sowie eine [bereits in Azure erstellte Datengatewayressource](../logic-apps/logic-apps-gateway-connection.md).

  Anschließend können Sie die zu verwendende Gatewayressource auswählen, wenn Sie die SharePoint Server-Verbindung über Ihren Workflow erstellen.

* Logik-App-Workflow, der Zugriff auf Ihre SharePoint-Website oder auf Ihren SharePoint-Server benötigt.

  * Wenn Sie den Workflow mit einem SharePoint-Trigger starten möchten, benötigen Sie einen leeren Logik-App-Workflow.
  * Wenn Sie eine SharePoint-Aktion hinzufügen möchten, muss Ihr Workflow bereits über einen Trigger verfügen.

## <a name="connector-reference"></a>Connector-Referenz

Weitere technische Details zu diesem Connector (beispielsweise Trigger, Aktionen und Grenzwerte aus der Swagger-Datei des Connectors) finden Sie auf der [Referenzseite des Connectors](/connectors/sharepoint/).

## <a name="connect-to-sharepoint"></a>Herstellen einer Verbindung mit SharePoint

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

## <a name="add-a-trigger"></a>Hinzufügen eines Triggers

1. Öffnen Sie im Azure-Portal, in Visual Studio Code oder Visual Studio Ihren Logik-App-Workflow im visuellen Designer, sofern noch nicht geöffnet.

1. Geben Sie `sharepoint` als Suchbegriff in das Suchfeld des Designers ein. Wählen Sie den Connector **SharePoint** aus.

1. Wählen Sie in der Liste **Trigger** den gewünschten Trigger aus.

1. Wenn Sie aufgefordert werden, sich anzumelden und eine Verbindung zu erstellen, wählen Sie eine der folgenden Optionen aus:

   * SharePoint Online: Wählen Sie **Anmelden** aus, und authentifizieren Sie Ihre Benutzeranmeldeinformationen.
   * SharePoint Server: Wählen Sie **Über lokales Datengateway verbinden** aus. Geben Sie die Anforderungsinformationen für die zu verwendenden Gatewayressource, den Authentifizierungstyp sowie andere erforderliche Details an.

1. Wenn Sie fertig sind, wählen Sie  **Erstellen** aus.

   Nach erfolgreicher Verbindungserstellung durch den Workflow wird der ausgewählte Trigger angezeigt.

1. Geben Sie die Informationen zum Einrichten des Triggers an, und fahren Sie mit der Workflowerstellung fort.

## <a name="add-an-action"></a>Hinzufügen einer Aktion

1. Öffnen Sie im Azure-Portal, in Visual Studio Code oder Visual Studio Ihren Logik-App-Workflow im visuellen Designer, sofern noch nicht geöffnet.

1. Wählen Sie eine der folgenden Optionen aus:

   * Wenn Sie eine Aktion als derzeit letzten Schritt hinzufügen möchten, wählen Sie **Neuer Schritt** aus.
   * Wenn Sie zwischen Schritten eine Aktion einfügen möchten, bewegen Sie den Mauszeiger über den Pfeil zwischen diesen Schritten. Wählen Sie das Pluszeichen ( **+** ) und anschließend die Option **Aktion hinzufügen** aus.

1. Geben Sie unter **Vorgang auswählen** den Suchbegriff `sharepoint` in das Suchfeld ein. Wählen Sie den Connector **SharePoint** aus.

1. Wählen Sie in der Liste **Aktionen** die gewünschte Aktion aus.

1. Wenn Sie aufgefordert werden, sich anzumelden und eine Verbindung zu erstellen, wählen Sie eine der folgenden Optionen aus:

   * SharePoint Online: Wählen Sie **Anmelden** aus, und authentifizieren Sie Ihre Benutzeranmeldeinformationen.
   * SharePoint Server: Wählen Sie **Über lokales Datengateway verbinden** aus. Geben Sie die Anforderungsinformationen für die zu verwendenden Gatewayressource, den Authentifizierungstyp sowie andere erforderliche Details an.

1. Wenn Sie fertig sind, wählen Sie  **Erstellen** aus.

   Nach erfolgreicher Verbindungserstellung durch den Workflow wird die ausgewählte Aktion angezeigt.

1. Geben Sie die Informationen zum Einrichten der Aktion an, und fahren Sie mit der Workflowerstellung fort.

## <a name="next-steps"></a>Nächste Schritte

Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)