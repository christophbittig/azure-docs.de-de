---
title: Batchverarbeitung von Nachrichten als Gruppe
description: Senden und Empfangen von Nachrichten in Gruppen zwischen Ihren Workflows mithilfe der Batchverarbeitung in Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: article
ms.date: 07/31/2020
ms.openlocfilehash: cb38f70c20ad8a45ed1cb91d93e7a6f09a7d51df
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644370"
---
# <a name="send-receive-and-batch-process-messages-in-azure-logic-apps"></a>Versand, Empfang und Batchverarbeitung von Nachrichten in Azure Logic Apps

Um Nachrichten auf eine bestimmte Weise als Gruppen gemeinsam zu senden und zu verarbeiten, können Sie eine Batchverarbeitungslösung erstellen. Diese Lösung sammelt Nachrichten in einem *Batch* und wartet, bis die angegebenen Kriterien erfüllt sind, um dann den Nachrichtenbatch freizugeben und zu verarbeiten. Mittels Batchverarbeitung können Sie erreichen, dass Ihre Logik-App Nachrichten weniger häufig verarbeitet.

In diesem Artikel wird gezeigt, wie Sie eine Batchverarbeitungslösung erstellen, indem Sie zwei Logik-Apps im selben Azure-Abonnement und der selben Azure-Region sowie in dieser Reihenfolge erstellen:

1. Die [Logik-App für den Batchempfang](#batch-receiver), die Nachrichten in einem Batch annimmt und sammelt, bis die angegebenen Kriterien zur Freigabe und Verarbeitung der Nachrichten erfüllt sind Stellen Sie sicher, dass Sie zuerst die Logik-App für diesen Batchempfänger erstellen, sodass Sie später beim Erstellen der Logik-App für den Batchversand das Batchziel auswählen können.

1. Mindestens eine [Logik-App für den Batchversand](#batch-sender), die die Nachrichten an die zuvor erstellte Logik-App für den Batchempfang sendet

   Darüber hinaus können Sie einen eindeutigen Schlüssel (beispielsweise eine Kundennummer) angeben, die den Zielbatch auf der Grundlage dieses Schlüssels in logische Untergruppen *partitioniert* (unterteilt). Auf diese Weise kann die App für den Empfang alle Elemente mit demselben Schlüssel sammeln und zusammen verarbeiten.

Ihr Batchempfänger und Batchsender müssen sich im selben Azure-Abonnement *und* der selben Azure-Region befinden. Wenn dies nicht der Fall ist, können Sie beim Erstellen der Logik-App für den Batchversand nicht die Logik-App für den Batchempfang auswählen, da sie gegenseitig nicht sichtbar sind.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Falls Sie über kein Abonnement verfügen, können Sie [mit einem kostenlosen Azure-Konto beginnen](https://azure.microsoft.com/free/). Oder [registrieren Sie sich für ein Abonnement mit nutzungsbasierter Bezahlung](https://azure.microsoft.com/pricing/purchase-options/).

* Ein E-Mail-Konto bei einem [von Azure Logic Apps unterstützten E-Mail-Anbieter](../connectors/apis-list.md)

  > [!IMPORTANT]
  > Wenn Sie den Gmail-Connector verwenden möchten, können nur G-Suite-Geschäftskonten diesen Connector ohne Einschränkung in Logik-Apps verwenden. Wenn Sie über ein Gmail-Consumerkonto verfügen, können Sie diesen Connector nur mit bestimmten von Google genehmigten Diensten verwenden, oder Sie können [eine Google-Client-App erstellen, die für die Authentifizierung mit Ihrem Gmail-Connector verwendet werden soll](/connectors/gmail/#authentication-and-bring-your-own-application). Weitere Informationen finden Sie unter [Datensicherheit und Datenschutzrichtlinien für Google-Connectors in Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Grundlegende Kenntnisse über die [Erstellung von Logik-Apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Um anstelle des Azure-Portals Visual Studio zu verwenden, müssen Sie [Visual Studio zur Verwendung mit Logik Apps einrichten](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="batch-receiver"></a>

## <a name="create-batch-receiver"></a>Erstellen der Logik-App für den Batchempfang

Um Nachrichten an einen Batch senden zu können, muss dieser Batch zunächst als Ziel vorhanden sein, an das die Nachrichten gesendet werden. Daher müssen Sie zunächst die Logik-App für den Batchempfang erstellen, die über den Trigger **Batch** gestartet wird. Dadurch können Sie die Logik-App für den Batchempfang auswählen, wenn Sie die Logik-App für den Batchversand erstellen. Die Logik-App für den Batchempfang sammelt Nachrichten, bis die angegebenen Kriterien zur Freigabe und Verarbeitung der Nachrichten erfüllt sind. Beim Batchempfang sind keine Angaben zum Batchversand erforderlich, beim Batchversand wird dagegen das Ziel benötigt, an das die Nachrichten gesendet werden.

1. Erstellen Sie im [Azure-Portal](https://portal.azure.com) oder in Visual Studio eine Logik-App mit diesem Namen: `BatchReceiver`

1. Fügen Sie im Logik-App-Designer den Trigger **Batch** hinzu, um den Workflow Ihrer Logik-App zu starten. Geben Sie in das Suchfeld `batch` ein, und wählen Sie diesen Trigger aus: **Batchnachrichten**

   ![Hinzufügen des Triggers „Batchnachrichten“](./media/logic-apps-batch-process-send-receive-messages/add-batch-receiver-trigger.png)

1. Legen Sie diese Eigenschaften für den Batchempfang fest:

   | Eigenschaft | BESCHREIBUNG |
   |----------|-------------|
   | **Batchmodus** | - **Inline**: Zum Definieren von Freigabekriterien im Batchtrigger <br>- **Integrationskonto**: Zum Definieren mehrerer Konfigurationen für Freigabekriterien über ein [Integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md). Mit einem Integrationskonto können Sie diese Konfigurationen an zentraler Stelle statt in separaten Logik-Apps verwalten. |
   | **Batchname** | Der Name für Ihren Batch (in diesem Beispiel „TestBatch“). Er gilt nur für den Batchmodus **Inline**. |
   | **Freigabekriterien** | Diese Eigenschaft gilt nur für den Batchmodus **Inline** und gibt die Kriterien an, die vor der Verarbeitung der einzelnen Batches erfüllt sein müssen: <p>- **Basierend auf der Nachrichtenanzahl**: Den Batch auf Grundlage der Anzahl der vom Batch gesammelten Nachrichten freigeben. <br>- **Basierend auf Größe**: Den Batch auf Grundlage der Gesamtgröße aller von diesem Batch gesammelten Nachrichten in Bytes freigeben. <br>- **Zeitplan**: Den Batch auf Grundlage eines Wiederholungszeitplans freigeben, der ein Intervall und eine Häufigkeit angibt. In den erweiterten Optionen können Sie außerdem eine Zeitzone auswählen und ein Startdatum sowie eine Startzeit bereitstellen. <br>- **Alle auswählen**: Alle angegebenen Kriterien verwenden. |
   | **Nachrichtenanzahl** | Die Anzahl von Nachrichten, die im Batch gesammelt werden sollen, z. B. 10 Nachrichten Der Grenzwert für einen Batch ist 8.000 Nachrichten. |
   | **Batch Size** | Die Gesamtgröße in Bytes, die im Batch gesammelt werden soll, z. B. 10 MB. Das Größenlimit für einen Batch beträgt 80 MB. |
   | **Zeitplan** | Das Intervall und die Häufigkeit zwischen Batchfreigaben, z. B. 10 Minuten. Der Mindestwert für die Wiederholungen beträgt 60 Sekunden bzw. 1 Minute. Minutenbruchteile werden auf 1 Minute aufgerundet. Öffnen Sie zum Angeben einer Zeitzone oder eines Startdatums und einer Startzeit die Liste **Neuen Parameter hinzufügen**, und wählen Sie die entsprechenden Eigenschaften aus. |
   |||

   > [!NOTE]
   >
   > Wenn Sie die Freigabekriterien ändern, während der Trigger noch über als Stapel zusammengefasste, aber nicht gesendete Nachrichten verfügt, verwendet er die aktualisierten Freigabekriterien, um die nicht gesendeten Nachrichten zu verarbeiten.

   Dieses Beispiel zeigt alle Kriterien, doch probieren Sie für Ihre eigenen Tests nur ein Kriterium aus:

   ![Angeben von Details für den Batchtrigger](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-criteria.png)

1. Fügen Sie nun eine oder mehrere Aktionen für die Verarbeitung der einzelnen Batches hinzu.

   Fügen Sie für dieses Beispiel eine Aktion hinzu, die eine E-Mail sendet, wenn der Batchtrigger ausgelöst wird. Der Trigger wird ausgeführt und sendet eine E-Mail, wenn der Batch 10 Nachrichten enthält oder 10 MB umfasst oder wenn 10 Minuten vergangen sind.

   1. Wählen Sie unter dem Batchtrigger **Neuer Schritt** aus.

   1. Geben Sie im Suchfeld den Begriff `send email` als Filter ein. Wählen Sie basierend auf Ihrem E-Mail-Anbieter einen E-Mail-Connector aus.

      Wenn Sie beispielsweise über ein Geschäfts-, Schul- oder Unikonto wie @fabrikam.com oder @fabrikam.onmicrosoft.com verfügen, wählen Sie den **Microsoft 365 Outlook**-Connector aus. Wenn Sie ein persönliches Konto wie @outlook.com oder @hotmail.com besitzen, wählen Sie den **Outlook.com**-Connector aus. In diesem Beispiel wird der Microsoft 365 Outlook-Connector verwendet.

   1. Wählen Sie die Aktion „E-Mail senden“ für Ihren Anbieter aus, z. B.:

      ![Auswählen der Aktion „E-Mail senden“ für Ihren E-Mail-Anbieter](./media/logic-apps-batch-process-send-receive-messages/batch-receiver-send-email-action.png)

1. Melden Sie sich nach Aufforderung bei Ihrem E-Mail-Konto an.

1. Legen Sie die Eigenschaften für die hinzugefügte Aktion fest.

   * Geben Sie im Feld **An** die E-Mail-Adresse des Empfängers ein. Zu Testzwecken können Sie hier Ihre eigene E-Mail-Adresse angeben.

   * Wählen Sie im Feld **Betreff** in der angezeigten Liste mit dynamischem Inhalt das Feld **Partitionsname** aus.

     ![Auswählen von „Partitionsname“ in der Liste „Dynamischer Inhalt“](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details.png)

     Später im Batchversand können Sie einen eindeutigen Partitionsschlüssel angeben, um den Zielbatch in logische Untergruppen zu unterteilen, an die Nachrichten gesendet werden können. Jede Gruppe besitzt eine eindeutige Nummer, die von der Logik-App für den Batchversand generiert wird. Dadurch können Sie einen einzelnen Batch mit mehreren Untergruppen verwenden und die einzelnen Untergruppen jeweils mit einem Namen Ihrer Wahl definieren.

     > [!IMPORTANT]
     > Eine Partition hat einen Grenzwert von 5.000 Nachrichten oder 80 MB. Wenn eine der Bedingungen erfüllt ist, gibt Logic Apps den Batch ggf. frei, auch wenn Ihre definierte Freigabebedingung nicht erfüllt ist.

   * Wählen Sie im Feld **Text** in der angezeigten Liste mit dynamischem Inhalt das Feld **Nachrichten-ID** aus.

     Der Logik-App-Designer fügt automatisch eine **For each**-Schleife um die Aktion „E-Mail senden“ hinzu, da diese Aktion die Ausgabe der vorherigen Aktion nicht als Batch, sondern als Sammlung behandelt.

     ![Auswählen von „Nachrichten-ID“ unter „Text“](./media/logic-apps-batch-process-send-receive-messages/send-email-action-details-for-each.png)

1. Speichern Sie Ihre Logik-App. Sie haben jetzt eine Logik-App für den Batchempfang erstellt.

    ![Speichern Ihrer Logik-App](./media/logic-apps-batch-process-send-receive-messages/save-batch-receiver-logic-app.png)

   > [!IMPORTANT]
   > Stellen Sie bei Verwendung von Visual Studio sicher, bevor Sie mit dem nächsten Abschnitt fortfahren, dass Sie zuerst Ihre [Batchempfänger-Logik-App in Azure *bereitstellen*](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure). Andernfalls können Sie beim Erstellen der Logik-App für den Batchversand nicht die Logik-App für den Batchempfang auswählen.

<a name="batch-sender"></a>

## <a name="create-batch-sender"></a>Erstellen der Logik-App für den Batchversand

Erstellen Sie nun eine oder mehrere Logik-Apps für den Batchversand, die Nachrichten an die Logik-App für den Batchempfang senden. In jeder Logik-App für den Batchversand geben Sie die Logik-App für den Batchempfang sowie den Batchnamen, den Nachrichteninhalt und gegebenenfalls weitere Einstellungen an. Optional können Sie einen eindeutigen Partitionsschlüssel angeben, um den Batch zum Sammeln von Nachrichten mit diesem Schlüssel in logische Untergruppen zu unterteilen.

* Stellen Sie sicher, dass Sie zuvor [Ihren Batchempfänger erstellt und bereitgestellt haben](#batch-receiver), sodass Sie beim Erstellen Ihres Batchsenders den vorhandenen Batchempfänger als Zielbatch auswählen können. Beim Batchempfang sind keine Angaben zum Batchversand erforderlich, beim Batchversand wird dagegen das Ziel benötigt, an das die Nachrichten gesendet werden.

* Stellen Sie sicher, dass sich die Batchempfänger-Logik-App und die Batchsender-Logik-App in der selben Azure-Region *und* dem selben Azure-Abonnement befinden. Wenn dies nicht der Fall ist, können Sie beim Erstellen der Logik-App für den Batchversand nicht die Logik-App für den Batchempfang auswählen, da sie gegenseitig nicht sichtbar sind.

1. Erstellen Sie eine weitere Logik-App mit diesem Namen: `BatchSender`

   1. Geben Sie im Suchfeld den Begriff `recurrence` als Filter ein. Wählen Sie in der Triggerliste den folgenden Trigger aus: **Serie**

      ![Hinzufügen des Serientriggers](./media/logic-apps-batch-process-send-receive-messages/add-schedule-trigger-batch-sender.png)

   1. Legen Sie das Intervall und die Häufigkeit der Ausführung der Logik-App für den Versand auf einmal pro Minute fest.

      ![Festlegen von Häufigkeit und Intervall für den Wiederholungstrigger](./media/logic-apps-batch-process-send-receive-messages/recurrence-trigger-batch-sender-details.png)

1. Fügen Sie eine neue Aktion zum Senden von Nachrichten an einen Batch hinzu.

   1. Wählen Sie unter dem **Wiederholung** strigger die Option **Neuer Schritt** aus.

   1. Geben Sie `batch` als Filter in das Suchfeld ein, und wählen Sie die folgende Aktion aus: **Logik-App-Workflow mit Batchtrigger auswählen**

      ![Auswählen von „Logik-App-Workflow mit Batchtrigger auswählen“](./media/logic-apps-batch-process-send-receive-messages/send-messages-batch-action.png)

      Es wird eine Liste mit nur den Logik-Apps angezeigt, die über Batchtrigger verfügen und sich in derselben Azure-Region *und* demselben Azure-Abonnement wie Ihre Batchsender-Logik-App befinden.

   1. Wählen Sie die zuvor erstellte Batchempfänger-Logik-App aus der Liste der Logik-Apps aus.

      ![Auswählen Ihrer Batchempfänger-Logik-App](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch-receiver.png)

      > [!IMPORTANT]
      > Wenn Sie Visual Studio verwenden und keine Batchempfänger-Logik-Apps zur Auswahl angezeigt werden, vergewissern Sie sich, dass Sie Ihren Batchempfänger zuvor erstellt und in Azure bereitgestellt haben. Ist dies nicht der Fall, lesen Sie die Informationen zum [Bereitstellen Ihrer Batchempfänger-Logik-App in Azure](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-logic-app-to-azure).

   1. Wählen Sie in der Liste mit den Aktionen diese Aktion aus: **Batch_messages – \<*your-logic-app-name*\>** .

      ![Auswählen der Aktion „Batch_messages – \<Ihre Logik-App\>“](./media/logic-apps-batch-process-send-receive-messages/batch-sender-select-batch.png)

1. Legen Sie die Eigenschaften für den Batchversand fest.

   | Eigenschaft | BESCHREIBUNG |
   |----------|-------------|
   | **Batchname** | Der von der Empfänger-Logik-App definierte Batchname, der in diesem Beispiel `TestBatch` lautet. <p>**Wichtig**: Der Batchname wird zur Laufzeit überprüft und muss mit dem in der Logik-App für den Empfang angegebenen Namen übereinstimmen. Wenn Sie den Batchnamen ändern, funktioniert die Logik-App für den Batchversand nicht. |
   | **Nachrichteninhalt** | Der Inhalt für die Nachricht, die Sie senden möchten |
   |||

   > [!NOTE]
   > Die Eigenschaftswerte von **Triggername** und **Workflow** werden automatisch von Ihrer ausgewählten Logik-App aufgefüllt.

   Fügen Sie in diesem Beispiel den folgenden Ausdruck hinzu, um das aktuelle Datum und die aktuelle Uhrzeit in den Nachrichteninhalt einzufügen, den Sie an den Batch senden:

   1. Klicken Sie in das Feld **Nachrichteninhalt**.

   1. Wählen Sie bei Anzeige der Liste mit dynamischen Inhalten **Ausdruck** aus.

   1. Geben Sie den Ausdruck `utcnow()` ein, und wählen Sie **OK** aus.

      ![Wählen Sie in „Nachrichteninhalt“ den Wert „Ausdruck“ aus, geben Sie „utcnow()“ ein, und wählen Sie „OK“ aus.](./media/logic-apps-batch-process-send-receive-messages/batch-sender-details.png)

1. Richten Sie nun eine Partition für den Batch ein. Öffnen Sie in der `BatchReceiver`-Aktion die Liste **Neuen Parameter hinzufügen**, und wählen Sie diese Eigenschaften aus:

   | Eigenschaft | BESCHREIBUNG |
   |----------|-------------|
   | **Partitionsname** | Ein optionaler eindeutiger Partitionsschlüssel, der es ermöglicht, den Zielbatch in logische Untergruppen aufzuteilen und Nachrichten auf der Grundlage dieses Schlüssels zu sammeln |
   | **Nachrichten-ID** | Eine optionale Nachrichten-ID, bei der es sich um einen global eindeutigen Bezeichner (Globally Unique Identifier, GUID) handelt, wenn nichts angegeben wird |
   |||

   Fügen Sie in diesem Beispiel im Feld **Partitionsname** einen Ausdruck hinzu, der eine Zufallszahl zwischen eins und fünf generiert. Lassen Sie das Feld **Nachrichten-ID** leer.

   1. Klicken Sie in das Feld **Partitionsname**, damit die Liste mit dynamischem Inhalt angezeigt wird.

   1. Wählen Sie in der Liste mit den dynamischen Inhalten die Option **Ausdruck** aus.

   1. Geben Sie den Ausdruck `rand(1,6)` ein, und wählen Sie **OK** aus.

      ![Einrichten einer Partition für den Zielbatch](./media/logic-apps-batch-process-send-receive-messages/batch-sender-partition-advanced-options.png)

      Diese Funktion vom Typ **rand** generiert eine Zahl zwischen eins und fünf. Der Batch wird also in fünf nummerierte Partitionen unterteilt, die dynamisch durch diesen Ausdruck festgelegt werden.

1. Speichern Sie Ihre Logik-App. Ihre Logik-App für den Versand sieht nun in etwa wie folgt aus:

   ![Speichern Ihrer Logik-App für den Versand](./media/logic-apps-batch-process-send-receive-messages/batch-sender-finished.png)

## <a name="test-your-logic-apps"></a>Testen Ihrer Logik-Apps

Lassen Sie Ihre Logik-Apps zum Testen Ihrer Batchverarbeitungslösung einige Minuten laufen. In Kürze erhalten Sie E-Mails in Fünfergruppen, die jeweils über den gleichen Partitionsschlüssel verfügen.

Die Logik-App für den Batchversand wird einmal pro Minute ausgeführt. Sie generiert eine Zufallszahl zwischen eins und fünf und verwendet diese generierte Zahl als Partitionsschlüssel für den Zielbatch, an den Nachrichten gesendet werden. Sobald der Batch fünf Elemente mit dem gleichen Partitionsschlüssel enthält, wird die Logik-App für den Batchempfang ausgelöst und sendet für jede Nachricht eine E-Mail.

> [!IMPORTANT]
> Vergessen Sie am Ende Ihres Tests nicht, die `BatchSender`-Logik-App zu deaktivieren, um zu verhindern, dass die Logik-App weiterhin Nachrichten sendet und dadurch Ihren Posteingang überlädt.

## <a name="next-steps"></a>Nächste Schritte

* [Zusammenfassen von EDI-Nachrichten als Batch und Senden der Nachrichten](../logic-apps/logic-apps-scenario-edi-send-batch-messages.md)
* [Erstellen von Workflowdefinitionen für Logik-Apps per JSON-Code](../logic-apps/logic-apps-author-definitions.md)
* [Ausnahmebehandlung und Fehlerprotokollierung für Logik-Apps](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
