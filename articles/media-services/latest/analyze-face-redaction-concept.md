---
title: Gesichter in Azure Media Services v3 API suchen und bearbeiten | Microsoft Docs
description: Azure Media Services v3 bietet eine Voreinstellung für die Gesichtserkennung und Bearbeitung (Verwischen), die es Ihnen ermöglicht, eine Videodatei zu übermitteln, Gesichter zu erkennen und fakultativ eine Bearbeitung (Verwischen) auf sie anzuwenden, und zwar in einem einzigen kombinierten Durchgang oder in einem zweistufigen Vorgang, der eine Bearbeitung ermöglicht. In diesem Artikel wird veranschaulicht, wie Sie Gesichter mit der Face Detector-Voreinstellung in der V3-API suchen und bearbeiten.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/25/2021
ms.author: johndeu
ms.custom: devx-track-csharp
ms.openlocfilehash: 2c434720863a7ecff4192720874547f6f8c2e8ed
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128621412"
---
# <a name="find-and-redact-blur-faces-with-the-face-detector-preset"></a>Suchen und bearbeiten (verwischen) Sie Gesichter mit der Gesichtserkennungs-Voreinstellung

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services V3-API enthält eine Gesichtserkennungs-Voreinstellung, die eine skalierbare Gesichtserkennung und-Bearbeitung (Verwischen) in der Cloud bietet. Mit der Gesichtsbearbeitung können Sie Ihr Video ändern, um Gesichter von ausgewählten Personen unscharf anzuzeigen und so unkenntlich zu machen. Es kann beispielsweise sein, dass Sie den Gesichtsbearbeitungsdienst nutzen möchten, wenn es um die öffentliche Sicherheit oder Medienarbeit geht. Ein paar Minuten Filmmaterial, das mehrere Gesichter enthält, kann Stunden dauern, um manuell bearbeitet zu werden, aber mit dieser Voreinstellung erfordert das Gesichtsbearbeitungsverfahren nur ein paar einfache Schritte.

Dieser Artikel enthält Details zum zur **Gesichtserkennungsvoreinstellung** und zeigt, wie sie mit Azure Media Services SDK für .NET verwendet werden kann.

## <a name="compliance-privacy-and-security"></a>Compliance, Datenschutz und Sicherheit

Eine wichtige Erinnerung: Sie müssen bei der Verwendung von Analysen in Azure Media Services alle geltenden Gesetze einhalten. Sie dürfen Azure Media Services oder einen anderen Azure-Dienst nicht in einer Weise nutzen, welche die Rechte anderer verletzt. Bevor Sie Videos, einschließlich biometrischer Daten, zur Verarbeitung und Speicherung in den Azure Media Services-Dienst hochladen, müssen Sie über alle erforderlichen Rechte, einschließlich aller entsprechenden Zustimmungen der Personen im Video, verfügen. IUm mehr über Compliance, Datenschutz und Sicherheit in Azure Media Services zu erfahren, lesen Sie die rechtlichen Begriffe zum [Azure Erkennungsdienst](https://azure.microsoft.com/support/legal/cognitive-services-compliance-and-privacy/). Informationen zu den Datenschutzauflagen und zur Behandlung Ihrer Daten durch Microsoft finden Sie in den [Datenschutzbestimmungen](https://privacy.microsoft.com/PrivacyStatement), in den [Bestimmungen für Onlinedienste](https://www.microsoft.com/licensing/product-licensing/products) (Online Services Terms, OST) und im [Nachtrag zur Datenverarbeitung](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) (Data Processing Addendum, DPA). Weitere Datenschutzinformationen (unter anderem zu Datenaufbewahrung und Datenlöschung/-vernichtung) finden Sie in den Bestimmungen für Onlinedienste und [hier](../../azure-video-analyzer/video-analyzer-for-media-docs/faq.yml). Durch die Nutzung von Azure Media Services erklären Sie sich mit den Bedingungen für Cognitive Services, den OST, DPA und der Datenschutzerklärung einverstanden

## <a name="face-redaction-modes"></a>Modi der Gesichtsbearbeitung

Bei der Gesichtsbearbeitung werden Gesichter in jedem Bild eines Videos erkannt, und gleichzeitig wird das Gesichtsobjekt in Vorwärts- und Rückwärtsrichtung verfolgt, damit eine Person auch aus anderen Winkeln unkenntlich gemacht werden kann. Das automatisierte Bearbeitungsverfahren ist komplex und die Gesichtsverwischung ist nicht immer 100% garantiert. Aus diesem Grund kann bei der Voreinstellung ein Zwei-Pass-Modus verwendet werden, um die Qualität und Genauigkeit des Verwischens anhand einer Bearbeitungsstufe zu verbessern, bevor die Datei für den endgültigen Verwischungsdurchgang eingereicht wird. 

Zusätzlich zu einem vollständig automatischen **kombinierten** Modus können Sie mit dem zwei-Pass-Verafhren die Gesichter auswählen, die Sie in einer Liste mit Gesichts-IDs verwischen (oder nicht verwischen) möchten. Um willkürliche pro-Frame-Anpassungen vorzunehmen, verwendet die Voreinstellung eine Metadatendatei im JSON-Format als Eingabe für den zweiten Durchlauf. Dieser Workflow ist in die Modi **Analyze** (Analysieren) und **Redact** (Bearbeiten) unterteilt.

Sie können die beiden Modi auch einfach in einem einzigen Durchlauf kombinieren, der beide Tasks in einem Gang ausführt; dieser Modus wird als **Kombiniert** bezeichnet.  In diesem Artikel zeigt der Beispielcode, wie Sie den vereinfachten Einzel-Durchlauf-Modus **Kombiniert** für eine Beispiels-Quelldatei anwenden.

### <a name="combined-mode"></a>Kombinierter Modus

Dadurch wird in einem einzigen Durchgang eine bearbeitete MP4-Videodatei erzeugt, ohne dass eine manuelle Bearbeitung der JSON-Datei erforderlich ist. Die Ausgabe im Asset-Ordner für den Auftrag ist eine einzelne MP4-Datei, die verschwommene Gesichter mit dem ausgewählten Verwischungseffekt enthält. Verwenden Sie die Eigenschaft Auflösung, die auf **Sourceresolution** eingestellt ist, um die besten Ergebnisse der Bearbeitung zu erzielen.

| Phase | Dateiname | Notizen |
| --- | --- | --- |
| Eingabeasset |"ignite-sample.mp4" |Video im WMV-, MOV- oder MP4-Format |
| Voreinstellungen |Gesichtserkennungs-Einstellungen | **Modus**: FaceRedactorMode.Combined,  **blurType**: BlurType.Med, **resolution**: AnalysisResolution.SourceResolution |
| Ausgabeasset |"ignite-redacted.mp4 |Video mit Verwischungseffekt auf Gesichter |

### <a name="analyze-mode"></a>Analysemodus

Im **Analyze**-Durchlauf des zweistufigen Workflows wird eine Videoeingabe verwendet, und es werden eine JSON-Datei mit Gesichtspositionen und JPG-Bilder für jedes erkannte Gesicht erstellt.
Beachten Sie, dass die Gesichtserkennungs-IDs bei nachfolgenden Durchläufen der Analyse nicht unbedingt identisch sind.

| Phase | Dateiname | Notizen |
| --- | --- | --- |
| Eingabeasset |"ignite-sample.mp4" |Video im WMV-, MPV- oder MP4-Format |
| Voreinstellungen |Gesichtserkennungs-Einstellungen |**Modus**: FaceRedactorMode.Analyze, **resolution**: AnalysisResolution.SourceResolution|
| Ausgabeasset |ignite-sample_annotations.json |Anmerkungsdaten von Gesichtspositionen im JSON-Format. Die Gesichtserkennungs-IDs sind bei nachfolgenden Durchläufen der Analyse nicht unbedingt identisch. Können vom Benutzer bearbeitet werden, um die Begrenzungsrahmen für die unscharfen Bereiche zu ändern. Siehe Beispiel unten. |
| Ausgabeasset |foo_thumb%06d.jpg [foo_thumb000001.jpg, foo_thumb000002.jpg] |Zugeschnittenes JPG-Bild jedes erkannten Gesichts mit Nummer für die „labelId“ des Gesichts |

#### <a name="output-example"></a>Ausgabebeispiel

```json
{
  "version": 1,
  "timescale": 24000,
  "offset": 0,
  "framerate": 23.976,
  "width": 1280,
  "height": 720,
  "fragments": [
    {
      "start": 0,
      "duration": 48048,
      "interval": 1001,
      "events": [
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [],
        [
          {
            "index": 13,
            "id": 1138,
            "x": 0.29537,
            "y": -0.18987,
            "width": 0.36239,
            "height": 0.80335
          },
          {
            "index": 13,
            "id": 2028,
            "x": 0.60427,
            "y": 0.16098,
            "width": 0.26958,
            "height": 0.57943
          }
        ],

    ... truncated
```

### <a name="redact-blur-mode"></a>Bearbeitungs- (verschwommen)Modus

Im zweiten Durchlauf des Workflows wird eine größere Anzahl von Eingaben verwendet, die zu einem einzelnen Asset zusammengefasst werden müssen.

Hierzu gehören eine Liste mit den IDs für die Anwendung der Unschärfe, das ursprüngliche Video und die JSON-Datei mit den Anmerkungen. In diesem Modus werden die Anmerkungen verwendet, um die Unschärfe auf das Eingabevideo anzuwenden.

In der Ausgabe des Analysedurchlaufs ist das Originalvideo nicht enthalten. Das Video muss in das Eingabeasset für die Aufgabe im Bearbeitungsmodus hochgeladen und als primäre Datei ausgewählt werden.

| Phase | Dateiname | Notizen |
| --- | --- | --- |
| Eingabeasset |"ignite-sample.mp4" |Video im WMV-, MPV- oder MP4-Format. Dasselbe Video wie in Schritt 1. |
| Eingabeasset |ignite-sample_annotations.json" |Anmerkungs-Metadaten-Datei aus Phase 1, mit fakultativen Änderungen, wenn Sie die verschwommen Gesichter bearbeiten möchten. Dies muss in einer externen Anwendung, in einem Code oder in einem Text-Editor bearbeitet werden. |
| Eingabeasset | "ignite-sample_IDList.txt" (Fakultativ) | Optionale neue Liste mit zu bearbeitenden Gesicht-IDs mit Zeilentrennung. Wenn diese Option leer bleibt, wird der Verwischungseffekt auf alle Gesichter in der Quelldatei angewendet. Sie können die Liste verwenden, um bestimmte Gesichter selektiv zu verwischen. |
| Voreinstellung für die Gesichtserkennung  |Voreinstellungs-Konfiguration  | **Modus**: FaceRedactorMode.Redact, **blurType**: BlurType.Med |
| Ausgabeasset |"ignite-sample-redacted.mp4" |Video mit angewendeter Unschärfe, basierend auf Anmerkungen |

#### <a name="example-output"></a>Beispielausgabe

Dies ist die Ausgabe von IDList mit einer ausgewählten ID.
Die Gesichtserkennungs-IDs sind bei nachfolgenden Durchläufen der Analyse nicht unbedingt identisch.

Beispiel: „foo_IDList.txt“

```
1
2
3
```

## <a name="blur-types"></a>Typen von Weichzeichnern

Im Modus **Kombiniert** oder **Bearbeiten** sind fünf verschiedene Verwischungsmodi verfügbar, von denen Sie über die JSON-Eingabekonfiguration wählen können: **Niedrig**, **Mittel**, **Hoch**, **Box** und **Schwarz**. Standardmäßig wird **Medium** verwendet.

Untenstehend finden Sie Beispiele für Weichzeichnertypen.

#### <a name="low"></a>Niedrig

![Beispiel für eine Einstellung mit niedriger Auflösung.](./media/media-services-face-redaction/blur-1.png)

#### <a name="med"></a>Medium

![Beispiel für die Einstellung der mittleren Auflösung der Unschärfe.](./media/media-services-face-redaction/blur-2.png)

#### <a name="high"></a>High

![Beispiel für die Einstellung der hohen Auflösung der Unschärfe.](./media/media-services-face-redaction/blur-3.png)

#### <a name="box"></a>Feld

![Box-Modus zur Verwendung beim Debuggen Ihres Ergebnisses.](./media/media-services-face-redaction/blur-4.png)

#### <a name="black"></a>Schwarz

![Der Blackbox-Modus deckt alle Gesichter mit schwarzen Boxen ab.](./media/media-services-face-redaction/blur-5.png)

## <a name="elements-of-the-output-json-file"></a>Elemente der JSON-Ausgabedatei

Der Medienprozessor zur Gesichtsbearbeitung ermöglicht eine Gesichtspositionserkennung und -nachverfolgung mit hoher Genauigkeit, sodass bis zu 64 menschliche Gesichter in einem Videobild erkannt werden können. Frontalansichten von Gesichtern bieten die besten Ergebnisse, während Seitenansichten und kleine Gesichter (maximal 24 x 24 Pixel) unter Umständen nicht so genau erkannt werden.

[!INCLUDE [media-services-analytics-output-json](../../../includes/media-services-analytics-output-json.md)]

## <a name="net-sample-code"></a>.NET-Beispielcode

Das folgende Programm zeigt, wie Sie den „**Kombinierten** Einzeldurchgangs-Bearbeitungsmodus" verwenden:

- Sie ein Asset erstellen und eine Mediendatei in das Asset hochladen.
- Konfigurieren Sie die Voreinstellung für den Gesichtsdetektor, welche die Einstellungen für Modus und blurType verwendet.
- Erstellen Sie eine neue Transformation mit der Gesichtserkennungs-Voreinstellung
- Laden Sie die ausgegebene, bearbeitete Videodatei herunter.

## <a name="download-and-configure-the-sample"></a>Herunterladen und Konfigurieren des Beispiels

Klonen Sie ein GitHub-Repository auf Ihren Computer, das das .NET-Beispiel enthält, indem Sie den folgenden Befehl verwenden:  

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
 ```

Das Muster befindet sich im [Gesichtsbearbeitungs](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/FaceRedactor)-Ordner. Öffnen Sie in Ihrem heruntergeladenen Projekt die Datei [appsettings.json](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/VideoAnalytics/FaceRedactor/appsettings.json). Ersetzen Sie die Werte durch die Anmeldeinformationen, die Sie per [API-Zugriff](./access-api-howto.md) abgerufen haben.

**Fakultativ** können Sie die Datei **[sample.env](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/sample.env)** im Stammverzeichnis des Verzeichnisses kopieren und dort die Details ausfüllen und diese Datei in **.env** umbenennen (Beachten Sie den Punkt auf der Vorderseite!), damit sie für alle Beispiels-Projekte im Verzeichneis verwendet werden kann.  Dadurch ist es nicht mehr erforderlich, dass in jeder Stichprobe ein ausgefülltes appsettings.js Datei für jedes Muster vorhanden ist, und schützt davor, dass Sie Einstellungen in ihren eigenen geklonten git-Hub-Depots vornehmen.

#### <a name="examples"></a>Beispiele

In diesem Code wird gezeigt, wie Sie den **FaceDetectorPreset** für einen **kombinierten** Modus (verschwommen) einrichten.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPreset)]

Dieses Codebeispiel zeigt, wie die Voreinstellung bei der Erstellung an ein Transform-Objekt übergeben wird. Nach dem Erstellen des Transforms können Jobs direkt an diesen gesendet werden.

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoAnalytics/FaceRedactor/Program.cs#FaceDetectorPresetTransform)]

## <a name="next-steps"></a>Nächste Schritte

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Feedback geben

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
