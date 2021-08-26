---
title: Erstellen einer Funktion, die in Azure Logic Apps integriert ist
description: Erstellen Sie eine Funktion für die Integration in Azure Logic Apps und Azure Cognitive Services. Der resultierende Workflow kategorisiert Stimmungen von Tweets und sendet E-Mail-Benachrichtigungen.
author: craigshoemaker
ms.assetid: 60495cc5-1638-4bf0-8174-52786d227734
ms.topic: tutorial
ms.date: 04/10/2021
ms.author: cshoe
ms.custom: devx-track-csharp, mvc, cc996988-fb4f-47
ms.openlocfilehash: f4f911feef354e4f2bd1f947bef6418615b74ca2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750227"
---
# <a name="tutorial-create-a-function-to-integrate-with-azure-logic-apps"></a>Tutorial: Erstellen einer Funktion für die Integration in Azure Logic Apps

Azure Functions ist in Azure Logic Apps im Logik-App-Designer integriert. Dank dieser Integration können Sie die Rechenleistung von Functions in Orchestrierungen mit anderen Azure- und Drittanbieterdiensten nutzen.

In diesem Tutorial erfahren Sie, wie Sie einen Workflow zum Analysieren von Twitter-Aktivitäten erstellen. Bei der Auswertung von Tweets sendet der Workflow Benachrichtigungen, wenn positive Stimmungen erkannt werden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen Sie eine Cognitive Services-API-Ressource.
> * Erstellen einer Funktion, mit der die Stimmung von Tweets kategorisiert wird
> * Erstellen einer Logik-App, mit der eine Verbindung mit Twitter hergestellt wird
> * Hinzufügen der Stimmungserkennung zur Logik-App
> * Verbinden der Logik-App mit der Funktion
> * Senden einer E-Mail basierend auf der Antwort von der Funktion

## <a name="prerequisites"></a>Voraussetzungen

* Ein aktives [Twitter](https://twitter.com/)-Konto
* Ein [Outlook.com](https://outlook.com/)-Konto (zum Senden von Benachrichtigungen)

> [!NOTE]
> Wenn Sie den Gmail-Connector verwenden möchten, können nur G-Suite-Geschäftskonten diesen Connector ohne Einschränkungen in Logik-Apps verwenden. Wenn Sie über ein Gmail-Consumerkonto verfügen, können Sie den Gmail-Connector nur mit bestimmten von Google genehmigten Apps und Diensten verwenden, oder Sie können [eine Google-Client-App erstellen, die für die Authentifizierung in Ihrem Gmail-Connector verwendet werden soll](/connectors/gmail/#authentication-and-bring-your-own-application). <br><br>Weitere Informationen finden Sie unter [Datensicherheit und Datenschutzrichtlinien für Google-Connectors in Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

## <a name="create-text-analytics-resource"></a>Erstellen einer Textanalyseressource

Die Cognitive Services-APIs sind in Azure als einzelne Ressourcen verfügbar. Verwenden Sie die Textanalyse-API, um die Stimmungslage der geposteten Tweets zu ermitteln.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.

1. Klicken Sie im Azure-Portal links oben auf **Ressource erstellen**.

1. Wählen Sie unter _Kategorien_ die Option **KI + Machine Learning** aus.

1. Wählen Sie unter _Textanalyse_ die Option **Erstellen** aus.

1. Geben Sie auf dem Bildschirm _Textanalyse erstellen_ die folgenden Werte ein:

    | Einstellung | Wert | Hinweise |
    | ------- | ----- | ------- |
    | Subscription | Der Name Ihres Azure-Abonnements | |
    | Resource group | Erstellen Sie eine neue Ressourcengruppe namens **tweet-sentiment-tutorial**. | Später löschen Sie diese Ressourcengruppe, um alle Ressourcen zu entfernen, die während dieses Tutorials erstellt wurden. |
    | Region | Wählen Sie die nächstgelegene Region aus. | |
    | Name | **TweetSentimentApp** | |
    | Tarif | Wählen Sie **Free F0** aus. | |

1. Klicken Sie auf **Überprüfen und erstellen**.

1. Klicken Sie auf **Erstellen**.

1. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressource wechseln** aus.

## <a name="get-text-analytics-settings"></a>Abrufen von Textanalyseeinstellungen

Sie kopieren mit der erstellten Textanalyseressource einige Einstellungen und speichern sie zur späteren Verwendung.

1. Wählen Sie **Schlüssel und Endpunkt** aus.

1. Kopieren Sie **Schlüssel 1**, indem Sie auf das Symbol am Ende des Eingabefelds klicken.

1. Fügen Sie den Wert in einen Text-Editor ein.

1. Kopieren Sie den **Endpunkt**, indem Sie auf das Symbol am Ende des Eingabefelds klicken.

1. Fügen Sie den Wert in einen Text-Editor ein.

## <a name="create-the-function-app"></a>Erstellen der Funktionen-App

1. Suchen Sie im oberen Suchfeld nach **Funktions-App**, und wählen Sie den Eintrag aus.

1. Klicken Sie auf **Erstellen**.

1. Geben Sie die folgenden Werte ein.

    | Einstellung | Empfohlener Wert | Hinweise |
    | ------- | ----- | ------- |
    | Subscription | Der Name Ihres Azure-Abonnements | |
    | Resource group | **tweet-sentiment-tutorial** | Verwenden Sie im gesamten Tutorial den gleichen Ressourcengruppennamen. |
    | Name der Funktions-App | **TweetSentimentAPI** + ein eindeutiges Suffix | Funktionsanwendungsnamen sind global eindeutig. Gültige Zeichen sind `a-z` (Groß-/Kleinschreibung nicht beachtet), `0-9` und `-`. |
    | Veröffentlichen | **Code** | |
    | Laufzeitstapel | **.NET** | Der für Sie bereitgestellte Funktionscode ist in C# geschrieben. |
    | Version | Wählen Sie die neueste Versionsnummer aus. | |
    | Region | Wählen Sie die nächstgelegene Region aus. | |

1. Klicken Sie auf **Überprüfen und erstellen**.

1. Klicken Sie auf **Erstellen**.

1. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressource wechseln** aus.

## <a name="create-an-http-triggered-function"></a>Erstellen einer durch HTTP ausgelösten Funktion  

1. Wählen Sie im linken Menü des Fensters _Funktionen_ die Option **Funktionen** aus.

1. Wählen Sie dann im oberen Menü **Hinzufügen** aus, und geben Sie die folgenden Werte ein:

    | Einstellung | Wert | Hinweise |
    | ------- | ----- | ------- |
    | Entwicklungsumgebung | **Im Portal entwickeln** | |
    | Vorlage | **HTTP-Trigger** | |
    | Neue Funktion | **TweetSentimentFunction** | Dies ist der Name Ihrer Funktion. |
    | Autorisierungsstufe | **Function** | |

1. Wählen Sie die Schaltfläche **Hinzufügen** aus.

1. Wählen Sie die Schaltfläche **Programmieren und testen** aus.

1. Fügen Sie den folgenden Code in das Code-Editor-Fenster ein:

    ```csharp
    #r "Newtonsoft.Json"
    
    using System;
    using System.Net;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Logging;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;
    
    public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
    {
    
        string requestBody = String.Empty;
        using (StreamReader streamReader =  new  StreamReader(req.Body))
        {
            requestBody = await streamReader.ReadToEndAsync();
        }
    
        dynamic score = JsonConvert.DeserializeObject(requestBody);
        string value = "Positive";
    
        if(score < .3)
        {
            value = "Negative";
        }
        else if (score < .6) 
        {
            value = "Neutral";
        }
    
        return requestBody != null
            ? (ActionResult)new OkObjectResult(value)
           : new BadRequestObjectResult("Pass a sentiment score in the request body.");
    }
    ```

    Ein Stimmungswert wird an die Funktion übergeben, die einen Kategorienamen für den Wert zurückgibt.

1. Wählen Sie auf der Symbolleiste die Schaltfläche **Speichern** aus, um Ihre Änderungen zu speichern.

    > [!NOTE]
    > Wählen Sie zum Testen der Funktion im oberen Menü **Testen/Ausführen** aus. Geben Sie auf der Registerkarte _Eingabe_ in das Eingabefeld _Textkörper_ den Wert `0.9` ein, und wählen Sie dann **Ausführen** aus. Überprüfen Sie, ob im Abschnitt _Ausgabe_ im Feld _Inhalt der HTTP-Antwort_ der Wert _Positive_ zurückgegeben wird.

Erstellen Sie als Nächstes eine Logik-App für die Integration in Azure Functions, Twitter und die Cognitive Services-API.

## <a name="create-a-logic-app"></a>Erstellen einer Logik-App

1. Suchen Sie im oberen Suchfeld nach **Logik-Apps**, und wählen Sie den Eintrag aus.

1. Wählen Sie **Hinzufügen**.

1. Wählen Sie **Verbrauch** aus, und geben Sie die folgenden Werte ein:

    | Einstellung | Empfohlener Wert |
    | ------- | --------------- |
    | Subscription | Der Name Ihres Azure-Abonnements |
    | Resource group | **tweet-sentiment-tutorial** |
    | Logik-App-Name | **TweetSentimentApp** |
    | Region | Wählen Sie die Ihnen am nächsten gelegene Region aus, vorzugsweise die gleiche Region, die Sie auch in den vorherigen Schritten ausgewählt haben. |

    Übernehmen Sie die Standardwerte für alle anderen Einstellungen.

1. Klicken Sie auf **Überprüfen und erstellen**.

1. Klicken Sie auf **Erstellen**.

1. Wählen Sie nach Abschluss der Bereitstellung **Zu Ressource wechseln** aus.

1. Wählen Sie die Schaltfläche **Leere Logik-App** aus.

    :::image type="content" source="media/functions-twitter-email/blank-logic-app-button.png" alt-text="Schaltfläche „Leere Logik-App“":::

1. Wählen Sie auf der Symbolleiste die Schaltfläche **Speichern** aus, um Ihren Fortschritt zu speichern.

Sie können jetzt den Logik-App-Designer verwenden, um Ihrer Anwendung Dienste und Trigger hinzuzufügen.

## <a name="connect-to-twitter"></a>Verbinden mit Twitter

Stellen Sie eine Verbindung mit Twitter her, damit Ihre App neue Tweets abrufen kann.

1. Suchen Sie im oberen Suchfeld nach **Twitter**.

1. Wählen Sie das **Twitter**-Symbol aus.

1. Wählen Sie den Trigger **Wenn ein neuer Tweet gepostet wird** aus.

1. Geben Sie die folgenden Werte ein, um die Verbindung einzurichten:

    | Einstellung |  Wert |
    | ------- | ---------------- |
    | Verbindungsname | **MyTwitterConnection** |
    | Authentifizierungstyp | **Freigegebene Standardanwendung verwenden** |

1. Wählen Sie **Anmelden**.

1. Befolgen Sie die Anweisungen im Popupfenster, um die Anmeldung bei Twitter abzuschließen.

1. Geben Sie als Nächstes im Feld _Wenn ein neuer Tweet gepostet wird_ die folgenden Werte ein:

    | Einstellung | Wert |
    | ------- | ----- |
    | Suchtext | **#my-twitter-tutorial** |
    | Wie oft möchten Sie auf Elemente prüfen?: | **1** im Textfeld und <br> **Stunde** in der Dropdownliste. Sie können verschiedene Werte eingeben. Überprüfen Sie jedoch unbedingt die aktuellen [Einschränkungen](/connectors/twitterconnector/#limits) des Twitter-Connectors.  |

1. Wählen Sie auf der Symbolleiste die Schaltfläche **Speichern** aus, um Ihren Fortschritt zu speichern.

Stellen Sie als Nächstes eine Verbindung mit der Textanalyse her, um die Stimmung für die gesammelten Tweets zu ermitteln.

## <a name="add-text-analytics-sentiment-detection"></a>Hinzufügen der Textanalyse-Stimmungserkennung

1. Wählen Sie **Neuer Schritt** aus.

1. Suchen Sie im Suchfeld nach **Textanalyse**.

1. Wählen Sie das Symbol **Textanalyse** aus.

1. Wählen Sie **Stimmung erkennen** aus, und geben Sie die folgenden Werte ein:

    | Einstellung | Wert |
    | ------- | ----- |
    | Verbindungsname | **TextAnalyticsConnection** |
    | Kontoschlüssel | Fügen Sie den Textanalyse-Kontoschlüssel ein, den Sie zuvor gespeichert haben. |
    | Website-URL | Fügen Sie den Textanalyseendpunkt ein, den Sie zuvor gespeichert haben. |

1. Klicken Sie auf **Erstellen**.

1. Klicken Sie in das Feld _Neuen Parameter hinzufügen_, und aktivieren Sie das Kontrollkästchen neben **Dokumente**, das im Popupfenster angezeigt wird.

1. Klicken Sie in das Textfeld _documents Id - 1_ (Dokument-ID: 1), um das Popupfenster für dynamische Inhalte zu öffnen.

1. Suchen Sie im Suchfeld _Dynamischer Inhalt_ nach **ID**, und klicken Sie auf **Tweet-ID**.

1. Klicken Sie in das Textfeld _documents Text - 1_ (Dokumenttext: 1), um das Popupfenster für dynamische Inhalte zu öffnen.

1. Suchen Sie im Suchfeld _Dynamischer Inhalt_ nach **Text**, und klicken Sie auf **Tweettext**.

1. Geben Sie unter **Aktion auswählen** den Begriff **Textanalyse** ein, und klicken Sie anschließend auf die Aktion **Stimmung erkennen**.

1. Wählen Sie auf der Symbolleiste die Schaltfläche **Speichern** aus, um Ihren Fortschritt zu speichern.

Das Feld _Stimmung erkennen_ sollte wie auf dem folgenden Screenshot aussehen:

:::image type="content" source="media/functions-twitter-email/detect-sentiment.png" alt-text="Einstellungen unter „Stimmung erkennen“":::

## <a name="connect-sentiment-output-to-function-endpoint"></a>Verbinden der Stimmungsausgabe mit dem Funktionsendpunkt

1. Wählen Sie **Neuer Schritt** aus.

1. Suchen Sie im Suchfeld nach **Azure Functions**.

1. Wählen Sie das Symbol **Azure Functions** aus.

1. Suchen Sie im Suchfeld nach Ihrem Funktionsnamen. Wenn Sie die obige Anleitung befolgt haben, beginnt Ihr Funktionsname mit **TweetSentimentAPI**.

1. Wählen Sie das Funktionssymbol aus.

1. Wählen Sie das Element **TweetSentimentFunction** aus.

1. Klicken Sie in das Feld _Anforderungstext_, und wählen Sie im Popupfenster das Element **Bewertung** für _Stimmung erkennen_ aus.

1. Wählen Sie auf der Symbolleiste die Schaltfläche **Speichern** aus, um Ihren Fortschritt zu speichern.

## <a name="add-conditional-step"></a>Hinzufügen eines bedingten Schritts

1. Wählen Sie die Schaltfläche **Aktion hinzufügen** aus.

1. Klicken Sie in das Feld _Steuerung_, suchen Sie im Popupfenster nach **Steuerung**, und wählen Sie den Eintrag aus.

1. Wählen Sie **Bedingung** aus.

1. Klicken Sie in das Feld _Wert auswählen_, und wählen Sie im Popupfenster das Element **Text** für _TweetSentimentFunction_ aus.

1. Geben Sie in das Feld _Wert auswählen_ den Wert **Positive** ein.

1. Wählen Sie auf der Symbolleiste die Schaltfläche **Speichern** aus, um Ihren Fortschritt zu speichern.

## <a name="add-email-notifications"></a>Hinzufügen von E-Mail-Benachrichtigungen

1. Wählen Sie unter dem Feld _True_ die Schaltfläche **Aktion hinzufügen** aus.

1. Suchen Sie im Textfeld nach **Office 365 Outlook**, und wählen Sie den Eintrag aus.

1. Suchen Sie nach **Senden**, und wählen Sie im Textfeld **E-Mail senden** aus.

1. Wählen Sie die Schaltfläche **Anmelden** aus.

1. Befolgen Sie die Anweisungen im Popupfenster, um die Anmeldung bei Office 365 Outlook abzuschließen.

1. Geben Sie in das Feld _An_ Ihre E-Mail-Adresse ein.

1. Klicken Sie in das Feld _Betreff_ und dann unter _TweetSentimentFunction_ auf das Element **Text**. Wird das Element _Text_ nicht in der Liste angezeigt, klicken Sie auf den Link **Mehr anzeigen**, um die Optionsliste zu erweitern.

1. Geben Sie unter _Betreff_ nach dem Element _Text_ den Text **Tweet von:** ein.

1. Klicken Sie nach dem Text **Tweet von:** erneut auf das Feld, und wählen Sie in der Optionsliste _Wenn ein neuer Tweet gepostet wird_ den Eintrag _Benutzername_ aus.

1. Klicken Sie in das Feld _Text_, und wählen Sie in der Optionsliste _Wenn ein neuer Tweet gepostet wird_ den Eintrag **Tweettext** aus. Wird das Element _Text_ nicht in der Liste angezeigt, klicken Sie auf den Link **Mehr anzeigen**, um die Optionsliste zu erweitern.

1. Wählen Sie auf der Symbolleiste die Schaltfläche **Speichern** aus, um Ihren Fortschritt zu speichern.

Das E-Mail-Feld sollte nun wie auf dem folgenden Screenshot aussehen:

:::image type="content" source="media/functions-twitter-email/email-notification.png" alt-text="E-Mail-Benachrichtigung":::

## <a name="run-the-workflow"></a>Ausführen des Workflows

1. Senden Sie über Ihr Twitter-Konto einen Tweet mit dem folgenden Text: **I'm enjoying #my-twitter-tutorial** (#my-twitter-tutorial macht Spaß!).

1. Kehren Sie zum Logik-App-Designer zurück, und wählen Sie die Schaltfläche **Ausführen** aus.

1. Suchen Sie nach einer E-Mail vom Workflow.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, um alle in diesem Tutorial erstellten Azure-Dienste und -Konten zu bereinigen.

1. Suchen Sie im oberen Suchfeld nach **Ressourcengruppen**.

1. Wählen Sie **tweet-sentiment-tutorial** aus.

1. Wählen Sie **Ressourcengruppe löschen** aus.

1. Geben Sie in das Textfeld **tweet-sentiment-tutorial** ein.

1. Klicken Sie auf die Schaltfläche **Löschen**.

Optional können Sie zu Ihrem Twitter-Konto zurückkehren und alle Testtweets aus Ihrem Feed löschen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer serverlosen API mit Azure Functions](functions-create-serverless-api.md)
