---
title: 'Erste Schritte: REST-API v2.1 für die Azure-Formularerkennung'
description: Verwenden Sie die REST-API v2.1 für die Azure-Formularerkennung, um eine Formularverarbeitungs-App zu erstellen, die Schlüssel-Wert-Paare und Tabellendaten aus Ihren benutzerdefinierten Dokumenten extrahiert.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 11/02/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 7d53287f5ed83f87d21880605e80206527264dd3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030220"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->

> [!IMPORTANT]
>
> In dieser Schnellstartanleitung geht es um Version **2.1** der REST-API für die Azure-Formularerkennung mit cURL zum Ausführen von REST-API-Aufrufen.

| [Formularerkennungs-REST-API](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm) | [Formularerkennungs-REST-API](/rest/api/azure/) |

In dieser Schnellstartanleitung verwenden Sie die folgenden APIs zum Extrahieren strukturierter Daten aus Formularen und Dokumenten:

* [Layout](#try-it-layout-model)

* [Rechnung](#try-it-prebuilt-model)

## <a name="prerequisites"></a>Voraussetzungen

* Azure-Abonnement – [Erstellen eines kostenlosen Kontos](https://azure.microsoft.com/free/cognitive-services)

* [cURL](https://curl.haxx.se/windows/) muss installiert sein.

* [PowerShell-Version 6.0 oder höher](/powershell/scripting/install/installing-powershell-core-on-windows) oder eine ähnliche Befehlszeilenanwendung

* Eine Cognitive Services- oder Formularerkennungsressource. Wenn Sie über Ihr Azure-Abonnement verfügen, können Sie im Azure-Portal eine Formularerkennungsressource mit [einem Dienst](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) oder [mehreren Diensten](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) erstellen, um Ihren Schlüssel und Endpunkt zu erhalten. Sie können den kostenlosen Tarif (`F0`) verwenden, um den Dienst zu testen, und später für die Produktion auf einen kostenpflichtigen Tarif upgraden.

  > [!TIP]
  > Erstellen Sie eine Cognitive Services-Ressource, wenn Sie planen, über einen einzelnen Endpunkt bzw. Schlüssel auf mehrere Cognitive Services-Instanzen zuzugreifen. Erstellen Sie eine Formularerkennungsressource, falls nur auf die Formularerkennung zugegriffen werden soll. Beachten Sie hierbei, dass Sie eine Ressource mit einem einzelnen Dienst benötigen, falls Sie die [Azure Active Directory-Authentifizierung](/azure/active-directory/authentication/overview-authentication) nutzen möchten.

* Wählen Sie nach der Bereitstellung der Ressource **Zu Ressource wechseln** aus. Sie benötigen den Schlüssel und Endpunkt der von Ihnen erstellten Ressource, um Ihre Anwendung mit der Formularerkennungs-API zu verbinden. Der Schlüssel und der Endpunkt werden weiter unten in der Schnellstartanleitung in den Code eingefügt:

  :::image type="content" source="../../media/containers/keys-and-endpoint.png" alt-text="Screenshot: Schlüssel und Endpunktspeicherort im Azure-Portal.":::

### <a name="select-a-code-sample-to-copy-and-paste-into-your-application"></a>Wählen Sie ein Codebeispiel zum Kopieren und Einfügen in Ihre Anwendung aus:

* [**Layout**](#try-it-layout-model)

* [**Vordefinierte Rechnung**](#try-it-prebuilt-model)

> [!IMPORTANT]
>
> Denken Sie daran, den Schlüssel aus Ihrem Code zu entfernen, wenn Sie fertig sind, und ihn niemals zu veröffentlichen. Verwenden Sie in der Produktionsumgebung sichere Methoden, um Ihre Anmeldeinformationen zu speichern und darauf zuzugreifen. Weitere Informationen finden Sie im Cognitive Services-Artikel zur [Sicherheit](/azure/cognitive-services/cognitive-services-security.md).

## <a name="try-it-layout-model"></a>**Try it** (Ausprobieren): Layoutmodell

> [!div class="checklist"]
>
> * Für dieses Beispiel benötigen Sie eine **Formulardokumentdatei unter einem URI**. Sie können für diese Schnellstartanleitung unser [Beispielformulardokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf) verwenden.

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `\"{your-document-url}` durch eine URL für ein Beispielformular:

```http
https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-layout.pdf
```

#### <a name="request"></a>Anforderung

```bash
curl -v -i POST "https://{endpoint}/formrecognizer/v2.1/layout/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{'source': '{your-document-url}'}"
```

#### <a name="operation-location"></a>Operation-Location

Sie erhalten die Antwort `202 (Success)` mit einem **Operation-Location**-Header. Der Wert dieses Headers enthält eine Ergebnis-ID, mit der Sie den Status des asynchronen Vorgangs abfragen und die Ergebnisse abrufen können:

https://<span></span>cognitiveservice/formrecognizer/v2.1/layout/analyzeResults/ **{resultId}** .

Im folgenden Beispiel als Teil der URL ist die Zeichenfolge nach `analyzeResults/` die Ergebnis-ID.

```console
https://cognitiveservice/formrecognizer/v2/layout/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-layout-results"></a>Abrufen von Layoutergebnissen

Nachdem Sie die **[Analyze Layout](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeLayoutAsync)** -API aufgerufen haben, rufen Sie die **[Get Analyze Layout Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/GetAnalyzeLayoutResult)** -API auf, um den Status des Vorgangs und die extrahierten Daten abzurufen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `{resultId}` durch die Ergebnis-ID aus dem vorherigen Schritt.
<!-- markdownlint-disable MD024 -->

#### <a name="request"></a>Anforderung

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/layout/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-results"></a>Untersuchen der Ergebnisse

Sie erhalten die Antwort `200 (success)` mit dem JSON-Inhalt.

Vergleichen Sie das folgende Bild einer Rechnung mit der entsprechenden JSON-Ausgabe.

* Der Knoten `"readResults"` enthält jede Textzeile mit der Platzierung des zugehörigen Begrenzungsrahmens auf der Seite.
* Der Knoten `selectionMarks` zeigt jede Auswahlmarkierung (Kontrollkästchen, Optionsfeld) und ihren Status („ausgewählt“ oder „nicht ausgewählt“).
* Der Abschnitt `"pageResults"` enthält die extrahierten Tabellen. Für jede Tabelle werden der Text-, Zeilen- und Spaltenindex, die Zeilen- und Spaltenaufteilung, der Begrenzungsrahmen und mehr extrahiert.

:::image type="content" source="../../media/contoso-invoice.png" alt-text="Contoso-Projektangabedokument mit einer Tabelle":::

#### <a name="response-body"></a>Antworttext

Sie können die [vollständige Beispielausgabe auf GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-layout-output.json) anzeigen.

## <a name="try-it-prebuilt-model"></a>**Probieren Sie es aus:** Vordefiniertes Modell

> [!div class="checklist"]
>
> * In diesem Beispiel analysieren Sie ein Rechnungsdokument mithilfe eines vordefinierten Modells. Für diese Schnellstartanleitung können Sie unser [Beispiel für ein Rechnungsdokument](https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf) verwenden.

### <a name="choose-a-prebuilt-model"></a>Auswählen eines vordefinierten Modells

Sie sind hierbei nicht auf Rechnungen beschränkt, sondern können zwischen mehreren vordefinierten Modellen wählen, von denen jedes über eine Gruppe unterstützter Felder verfügt. Welches Modell für den Analysevorgang verwendet wird, hängt vom Typ des zu analysierenden Dokuments ab. Dies sind die vordefinierten Modelle, die vom Formularerkennungsdienst derzeit unterstützt werden:

* [**Rechnung**](../../concept-invoice.md): Extrahieren von Text, Auswahlmarkierungen, Tabellen, Feldern und wichtigen Informationen aus Rechnungen
* [**Beleg**](../../concept-receipt.md): Extrahieren von Text und wichtigen Informationen aus Belegen
* [**Ausweisdokument**](../../concept-id-document.md): Extrahieren von Text und wichtigen Informationen aus Führerscheinen und Reisepässen
* [**Visitenkarte**](../../concept-business-card.md): Extrahieren von Text und wichtigen Informationen aus Visitenkarten

Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnement für die Formularerkennung erhalten haben.
1. Ersetzen Sie `{subscription key}` durch den Abonnementschlüssel, den Sie im vorherigen Schritt kopiert haben.
1. Ersetzen Sie `\"{your-document-url}` durch eine URL für ein Rechnungsbeispiel:

    ```http
    https://raw.githubusercontent.com/Azure-Samples/cognitive-services-REST-api-samples/master/curl/form-recognizer/sample-invoice.pdf
    ```

#### <a name="request"></a>Anforderung

```bash
curl -v -i POST https://{endpoint}/formrecognizer/v2.1/prebuilt/invoice/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key:  {subscription key}" --data-ascii "{'source': '{your invoice URL}'}"
```

#### <a name="operation-location"></a>Operation-Location

Sie erhalten die Antwort `202 (Success)` mit einem **Operation-Location**-Header. Der Wert dieses Headers enthält eine Ergebnis-ID, mit der Sie den Status des asynchronen Vorgangs abfragen und die Ergebnisse abrufen können:

 _https://<span></span>cognitiveservice/formrecognizer/v2.1/prebuilt/receipt/analyzeResults/ **{resultId}**_

Im folgenden Beispiel als Teil der URL ist die Zeichenfolge nach `analyzeResults/` die Ergebnis-ID:

```console
https://cognitiveservice/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/54f0b076-4e38-43e5-81bd-b85b8835fdfb
```

### <a name="get-invoice-results"></a>Abrufen von Rechnungsergebnissen

Nachdem Sie die **Analyze Invoice**-API aufgerufen haben, rufen Sie die **[Get Analyze Invoice Result](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9acb78c40a2533aee83)** -API auf, um den Status des Vorgangs und die extrahierten Daten abzurufen. Nehmen Sie die folgenden Änderungen vor, bevor Sie den Befehl ausführen:

1. Ersetzen Sie `{endpoint}` durch den Endpunkt, den Sie mit Ihrem Abonnementschlüssel für die Formularerkennung erhalten haben. Sie finden ihn auf der Registerkarte **Übersicht** der Formularerkennungsressource.
1. Ersetzen Sie `{resultId}` durch die Ergebnis-ID aus dem vorherigen Schritt.
1. Ersetzen Sie `{subscription key}` durch Ihren Abonnementschlüssel.

#### <a name="request"></a>Anforderung

```bash
curl -v -X GET "https://{endpoint}/formrecognizer/v2.1/prebuilt/invoice/analyzeResults/{resultId}" -H "Ocp-Apim-Subscription-Key: {subscription key}"
```

### <a name="examine-the-response"></a>Untersuchen der Antwort

Sie erhalten die Antwort `200 (Success)` mit der folgenden JSON-Ausgabe:

* Das `"readResults"`-Feld enthält alle Textzeilen, die aus der Rechnung extrahiert wurden.
* `"pageResults"` beinhaltet die Tabellen und Informationen zu den Auswahlkästchen, die aus der Rechnung extrahiert wurden.
* Das `"documentResults"`-Feld enthält die Schlüssel-/Wertinformationen für die relevantesten Teile der Rechnung.

Weitere Informationen finden Sie im Dokument mit der [Beispielrechnung](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/curl/form-recognizer/sample-invoice.pdf).

#### <a name="response-body"></a>Antworttext

Auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/curl/form-recognizer/sample-invoice-output.json) finden Sie die vollständige Beispielausgabe.

## <a name="next-steps"></a>Nächste Schritte

Glückwunsch! In dieser Schnellstartanleitung haben Sie die REST-API für die Formularerkennung verwendet, um auf unterschiedliche Weise verschiedene Formulare zu analysieren. Lesen Sie als Nächstes die Referenzdokumentation, um weitere Informationen zur API für Formularerkennung v3.0 zu erhalten.

> [!div class="nextstepaction"]
> [Referenzdokumentation zur REST-API v2.1](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/5ed8c9843c2794cbb1a96291)
