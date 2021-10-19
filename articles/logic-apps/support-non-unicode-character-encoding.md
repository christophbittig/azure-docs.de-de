---
title: Konvertieren von nicht in Unicode codiertem Text zum Erzielen von Kompatibilität
description: Verarbeiten Sie Nicht-Unicode-Zeichen in Azure Logic Apps, indem Sie Textnutzdaten in UTF-8 mit Base64-Codierung und Azure Functions konvertieren.
ms.date: 10/05/2021
ms.topic: how-to
ms.reviewer: estfan, azla
ms.service: logic-apps
ms.openlocfilehash: 1251a1622e62b7940c25ac810db10f70da560000
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618988"
---
# <a name="support-non-unicode-character-encoding-in-logic-apps"></a>Unterstützung von Nicht-Unicode-Zeichencodierung in Logic Apps

Bei Nutzdaten in Textform wird von Azure Logic Apps davon ausgegangen, dass der Text in einem Unicode-Format (beispielsweise UTF-8) vorliegt. Dies kann zu Problemen führen, wenn in Ihrem Workflow Zeichen mit anderen Codierungen empfangen, gesendet oder verarbeitet werden müssen. Wenn Sie also beispielsweise mit Legacysystemen ohne Unicode-Unterstützung arbeiten, erhalten Sie möglicherweise beschädigte Zeichen in Flatfiles.

Wenden Sie bei Text mit einer anderen Zeichencodierung die Base64-Codierung auf die Nicht-Unicode-Nutzdaten an. Dadurch wird von Logic Apps nicht angenommen, dass der Text im UTF-8-Format vorliegt. Anschließend können Sie eine beliebige von .NET unterstützte Codierung mithilfe von Azure Functions in UTF-8 konvertieren. 

Diese Lösung kann sowohl für *mehrinstanzenfähige Workflows* als auch für *Workflows mit einem einzelnen Mandanten* verwendet werden. Außerdem können Sie [diese Lösung mit dem AS2-Connector verwenden](#convert-payloads-for-as2).

## <a name="convert-payload-encoding"></a>Konvertieren der Nutzdatencodierung

Vergewissern Sie sich zunächst, dass Ihr Trigger den Inhaltstyp ordnungsgemäß identifizieren kann. Dadurch wird sichergestellt, dass Logic Apps nicht mehr davon ausgeht, dass der Text im UTF-8-Format vorliegt. 

Wählen Sie in Triggern und Aktionen, die über die Eigenschaft **Inhaltstyp ableiten** verfügen, **Nein** aus.  Diese Eigenschaft finden Sie in der Regel in der Liste **Parameter hinzufügen** des Vorgangs. Wenn der Vorgang diese Eigenschaft jedoch nicht enthält, wird der Inhaltstyp durch die eingehende Nachricht festgelegt.

In der folgenden Liste sind einige Connectors aufgeführt, bei denen Sie das automatische Ableiten des Inhaltstyps deaktivieren können:
* [OneDrive](/connectors/onedrive/)
* [Azure Blob Storage](/connectors/azureblob/)
* [Azure File Storage](/connectors/azurefile/)
* [Dateisystem](/connectors/filesystem/)
* [Google Drive](/connectors/googledrive/)
* [SFTP – SSH](/connectors/sftpwithssh/)
 
Wenn Sie den Anforderungstrigger für Inhalte vom Typ `text/plain` verwenden, muss der Parameter `charset` im Header `Content-Type` des Aufrufs festgelegt werden. Andernfalls kann es passieren, dass Zeichen beschädigt werden oder dass der Parameter nicht dem Codierungsformat der Nutzdaten entspricht. Weitere Informationen zur Behandlung des Inhaltstyps `text/plain` finden Sie [hier](logic-apps-content-type.md#text-plain).

Ein Beispiel: Durch den HTTP-Trigger wird der eingehende Inhalt in UTF-8 konvertiert, wenn der Header `Content-Type` mit dem korrekten Parameter vom Typ `charset` festgelegt wird:

```json
{
    "headers": {
        <...>
        "Content-Type": "text/plain; charset=windows-1250"
        },
        "body": "non UTF-8 text content"
}
```

Wenn Sie den Header `Content-Type` auf `application/octet-stream` festlegen, erhalten Sie möglicherweise auch UTF-8-fremde Zeichen. Weitere Informationen zur Behandlung des Inhaltstyps `application/octet-stream` finden Sie [hier](logic-apps-content-type.md#applicationxml-and-applicationoctet-stream).

## <a name="base64-encode-content"></a>Codieren von Inhalten mit Base64

Vor dem [Codieren mit Base64](workflow-definition-language-functions-reference.md#base64) muss der [Text in UTF-8 konvertiert](#convert-payload-encoding) worden sein. Wenn Sie den Inhalt mit Base64 in eine Zeichenfolge decodieren, bevor der Text in UTF-8 konvertiert wurde, werden möglicherweise Zeichen beschädigt.

Konvertieren Sie als Nächstes eine beliebige von .NET unterstützte Codierung in eine andere von .NET unterstützte Codierung. Sehen Sie sich das [Azure Functions-Codebeispiel](#azure-functions-version) oder das [.NET-Codebeispiel](#net-version) an:

> [!TIP]
> Bei *Logik-Apps mit einem einzelnen Mandanten* können Sie die Leistung verbessern und die Wartezeit verkürzen, indem Sie die Konvertierungsfunktion lokal ausführen.

### <a name="azure-functions-version"></a>Azure Functions-Version

Das folgende Beispiel ist für die Azure Functions-Version 2 konzipiert: 

```csharp
using System;
using System.IO;
using System.Text;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Azure.WebJobs.Host;
using Newtonsoft.Json;

public static class ConversionFunctionv2 {
  [FunctionName("ConversionFunctionv2")]
  public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, TraceWriter log) {
    log.Info("C# HTTP trigger function processing a request.");

    Encoding inputEncoding = null;

    string requestBody = new StreamReader(req.Body).ReadToEnd();
    dynamic data = JsonConvert.DeserializeObject(requestBody);

    if (data == null || data.text == null || data.encodingInput == null || data.encodingOutput == null) {
      return new BadRequestObjectResult("Please pass text/encodingOutput properties in the input JSON object.");
    }

    Encoding.RegisterProvider(CodePagesEncodingProvider.Instance);

    try {
      string encodingInput = data.encodingInput.Value;
      inputEncoding = Encoding.GetEncoding(name: encodingInput);
    } catch (ArgumentException) {
      return new BadRequestObjectResult($"Input character set value '{data.encodingInput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
    }

    Encoding encodingOutput = null;
    try {
      string outputEncoding = data.encodingOutput.Value;
      encodingOutput = Encoding.GetEncoding(outputEncoding);
    } catch (ArgumentException) {
      return new BadRequestObjectResult($"Output character set value '{data.encodingOutput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
    }

    return (ActionResult) new JsonResult(
      value: new {
        text = Convert.ToBase64String(
          Encoding.Convert(
            srcEncoding: inputEncoding,
            dstEncoding: encodingOutput,
            bytes: Convert.FromBase64String((string) data.text)))
      });
  }
}
```

### <a name="net-version"></a>.NET-Version

Das folgende Beispiel ist für die Verwendung mit **.NET Standard** und **Version 2** von Azure Functions vorgesehen:

```csharp
    using System;
    using System.IO;
    using System.Text;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Extensions.Http;
    using Microsoft.AspNetCore.Http;
    using Microsoft.Azure.WebJobs.Host;
    using Newtonsoft.Json;

    public static class ConversionFunctionNET
    {
        [FunctionName("ConversionFunctionNET")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]HttpRequest req, TraceWriter log)
        {
            log.Info("C# HTTP trigger function processing a request.");

            Encoding inputEncoding = null;

            string requestBody = new StreamReader(req.Body).ReadToEnd();
            dynamic data = JsonConvert.DeserializeObject(requestBody);

            if (data == null || data.text == null || data.encodingInput == null || data.encodingOutput == null)
            {
                return new BadRequestObjectResult("Please pass text/encodingOutput properties in the input JSON object.");
            }

            Encoding.RegisterProvider(CodePagesEncodingProvider.Instance);

            try
            {
                string encodingInput = data.encodingInput.Value;
                inputEncoding = Encoding.GetEncoding(name: encodingInput);
            }
            catch (ArgumentException)
            {
                return new BadRequestObjectResult($"Input character set value '{data.encodingInput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
            }

            Encoding encodingOutput = null;
            try
            {
                string outputEncoding = data.encodingOutput.Value;
                encodingOutput = Encoding.GetEncoding(outputEncoding);
            }
            catch (ArgumentException)
            {
                return new BadRequestObjectResult($"Output character set value '{data.encodingOutput.Value}' is not supported. Supported values are listed at https://msdn.microsoft.com/en-us/library/system.text.encoding(v=vs.110).aspx.");
            }

            return (ActionResult)new JsonResult(
                value: new
                {
                    text = Convert.ToBase64String(
                        Encoding.Convert(
                            srcEncoding: inputEncoding,
                            dstEncoding: encodingOutput,
                            bytes: Convert.FromBase64String((string)data.text)))
                });
        }
    }
```

Mit den gleichen Konzepten können Sie auch [Nicht-Unicode-Nutzdaten aus Ihrem Workflow senden](#send-non-unicode-payload).

## <a name="sample-payload-conversions"></a>Beispiele für Nutzdatenkonvertierungen

In diesem Beispiel wird als Base64-codierte Beispieleingabezeichenfolge ein Personenname mit Akzentzeichen (*H&eacute;lo&iuml;se*) verwendet.

Beispieleingabe:

```json
{  
    "text": "SMOpbG/Dr3Nl",
    "encodingInput": "utf-8",
    "encodingOutput": "windows-1252"
}
```

Beispielausgabe:

```json
{
    "text": "U01PcGJHL0RyM05s"
}
```

## <a name="send-non-unicode-payload"></a>Senden von Nicht-Unicode-Nutzdaten

Wenn Sie Nicht-Unicode-Nutzdaten aus Ihrem Workflow senden müssen, führen Sie die Schritte zum [Konvertieren der Nutzdatencodierung](#convert-payload-encoding) in umgekehrter Reihenfolge aus. Belassen Sie den Text in Ihrem System so lange wie möglich im UTF-8-Format. Verwenden Sie als Nächstes die gleiche Funktion, um die Base64-codierten UTF-8-Zeichen in die erforderliche Codierung zu konvertieren. Wenden Sie anschließend die Base64-Decodierung auf den Text an, und senden Sie Ihre Nutzdaten.

## <a name="convert-payloads-for-as2"></a>Konvertieren von Nutzdaten für AS2

Diese Lösung kann auch mit Nicht-Unicode-Nutzdaten im [AS2 (v2)-Connector](logic-apps-enterprise-integration-as2.md) verwendet werden. Wenn Sie Nutzdaten, die Sie an AS2 übergeben, nicht in UTF-8 konvertieren, kommt es möglicherweise zu Problemen bei der Nutzdateninterpretation. Diese Probleme können zu einem MIC-Hashkonflikt zwischen den Partnern führen, der auf falsch interpretierte Zeichen zurückzuführen ist.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Codieren und Decodieren von Flatfiles in Azure Logic Apps mithilfe des Enterprise Integration Pack](logic-apps-enterprise-integration-flatfile.md)
