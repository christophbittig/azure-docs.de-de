---
title: Formularerkennung Studio | Vorschau
titleSuffix: Azure Applied AI Services
description: 'Konzept: Formular- und Dokumentverarbeitung, Datenextraktion und Analyse mit Formularerkennung Studio (Vorschau)'
author: sanjeev3
manager: netahw
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: sajagtap
ms.custom: ignite-fall-2021
ms.openlocfilehash: 938f1cc6058794deba4d6dc5182dbd2cea2a31ab
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027567"
---
# <a name="form-recognizer-studio-preview"></a>Formularerkennung Studio (Vorschau)

>[!NOTE]
> Formularerkennung Studio befindet sich derzeit in der öffentlichen Vorschauphase. Dies bedeutet, dass einige Features unter Umständen nicht unterstützt werden oder nur eingeschränkt verwendbar sind.

[Formularerkennung Studio (Vorschau)](https://formrecognizer.appliedai.azure.com/) ist ein Onlinetool für das grafische Erkunden und Kennenlernen der Features des Diensts „Formularerkennung“ und das Integrieren in Ihre Anwendungen. Verwenden Sie die [Schnellstartanleitung für Formularerkennung Studio](quickstarts/try-v3-form-recognizer-studio.md), um mit der Analyse von Dokumenten mit vortrainierten Modellen zu beginnen. Erstellen Sie benutzerdefinierte Formularmodelle, und verweisen Sie in Ihren Anwendungen auf die Modelle, indem Sie die Informationen zum [Python SDK (Vorschau)](quickstarts/try-v3-python-sdk.md) und andere Schnellstartanleitungen verwenden.

Die folgende Abbildung zeigt das Feature des vordefinierten Rechnungsmodells bei der Arbeit.

:::image border="true" type="content" source="media/quickstarts/prebuilt-get-started-v2.gif" alt-text="Formularerkennung: Beispiel für vordefinierte Modelle":::

## <a name="form-recognizer-studio-features"></a>Features von Formularerkennung Studio

Die folgenden Features des Formularerkennungsdiensts sind in Studio verfügbar.

* **Layout**: Testen Sie das Layoutfeature der Formularerkennung zum Extrahieren von Text, Tabellen, Auswahlmarkierungen und Strukturinformationen aus Dokumenten (PDF, TIFF) und Bildern (JPG, PNG, BMP). Beginnen Sie mit dem [Schnellstart für das Studio-Layout](quickstarts/try-v3-form-recognizer-studio.md#layout). Erkunden Sie dies mit Beispielen und Ihren Dokumenten. Verwenden Sie die interaktive Visualisierung und die JSON-Ausgabe, um zu verstehen, wie das Feature funktioniert. In der [Layoutübersicht](concept-layout.md) finden Sie weitere Informationen zum [Python SDK-Schnellstart für das Layout](quickstarts/try-v3-python-sdk.md#try-it-layout-model) und zu den ersten Schritten.

* **Vordefinierte Modelle**: Mit den vordefinierten Modellen der Azure-Formularerkennung können Sie Ihren Anwendungen und Abläufen eine intelligente Formularverarbeitung hinzufügen, ohne dass Sie Ihre eigenen Modelle trainieren und erstellen müssen. Beginnen Sie mit dem [Schnellstart für vordefinierte Studio-Modelle](quickstarts/try-v3-form-recognizer-studio.md#prebuilt-models). Erkunden Sie dies mit Beispielen und Ihren Dokumenten. Verwenden Sie die interaktive Visualisierung, die Liste der extrahierten Felder und die JSON-Ausgabe, um zu verstehen, wie das Feature funktioniert. In der [Modellübersicht](concept-model-overview.md) finden Sie weitere Informationen zum [Python SDK-Schnellstart für die vordefinierte Rechnung](quickstarts/try-v3-python-sdk.md#try-it-prebuilt-model) und zu den ersten Schritten.

* **Benutzerdefinierte Modelle**: Die benutzerdefinierten Modelle der Formularerkennung ermöglichen es Ihnen, Felder und Werte aus Modellen zu extrahieren, die mit Ihren Daten trainiert wurden und auf Ihre Formulare und Dokumente zugeschnitten sind. Erstellen Sie eigenständige benutzerdefinierte Modelle, oder kombinieren Sie zwei oder mehr benutzerdefinierte Modelle, um ein zusammengesetztes Modell zur Extraktion von Daten aus mehreren Formulartypen zu erstellen. Beginnen Sie mit dem [Schnellstart für benutzerdefinierte Studio-Modelle](quickstarts/try-v3-form-recognizer-studio.md#custom-model-basics).  Verwenden Sie den Online-Assistenten, die Beschriftungsschnittstelle, das Training und die Visualisierungen, um zu verstehen, wie das Feature funktioniert. Testen Sie das benutzerdefinierte Modell mit Ihren Beispieldokumenten, und verbessern Sie es durch Iteration. Lesen Sie die [Übersicht über benutzerdefinierte Modelle](concept-custom.md), um mehr zu erfahren, und verwenden Sie den [Migrationsleitfaden für die Formularerkennung v3.0 (Vorschau)](v3-migration-guide.md), um mit der Integration der neuen Modelle in Ihre Anwendungen zu beginnen.

* **Benutzerdefinierte Modelle: Beschriftungsfeatures**: Die Erstellung benutzerdefinierter Modell der Formularerkennung erfordert das Identifizieren der zu extrahierenden Felder und das Beschriften dieser Felder, bevor die benutzerdefinierten Modelle trainiert werden. Beschriftungstexte, Auswahlmarkierungen, Tabellendaten und andere Inhaltstypen werden in der Regel durch eine Benutzeroberfläche unterstützt, um den Workflow für das Training zu erleichtern. Verwenden Sie z. B. die Schnellstartanleitungen [Beschriftung als Tabellen](quickstarts/try-v3-form-recognizer-studio.md#labeling-as-tables) und [Beschriftung zur Unterschriftserkennung](quickstarts/try-v3-form-recognizer-studio.md#labeling-for-signature-detection), um die Beschriftungsumgebung in Formularerkennung Studio zu verstehen.

## <a name="next-steps"></a>Nächste Schritte

* Befolgen Sie unser [**Migrationshandbuch zur Formularerkennung v3.0**](v3-migration-guide.md), um die Unterschiede zur vorherigen Version der REST-API kennen zu lernen.
* Erkunden Sie unsere [**Schnellstartanleitungen zum Vorschau-SDK**](quickstarts/try-v3-python-sdk.md), um die Previewfunktionen in Ihren Anwendungen mithilfe der neuen SDKs auszuprobieren.
* Sehen Sie sich unsere [**Schnellstartanleitungen zur Vorschau-REST-API**](quickstarts/try-v3-rest-api.md) an, um die Previewfunktionen mithilfe der neuen REST-API auszuprobieren.

> [!div class="nextstepaction"]
> [Schnellstart: Formularerkennung Studio (Vorschau)](quickstarts/try-v3-form-recognizer-studio.md)
