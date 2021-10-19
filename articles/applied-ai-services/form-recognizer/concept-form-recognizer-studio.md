---
title: Formularerkennung Studio | Vorschau
titleSuffix: Azure Applied AI Services
description: 'Konzept: Formular- und Dokumentverarbeitung, Datenextraktion und Analyse mit Formularerkennung Studio (Vorschau)'
author: sanjeev3
manager: netahw
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 10/11/2021
ms.author: sajagtap
ms.openlocfilehash: e476f6da88688cb055c741f0888001d864908bae
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/12/2021
ms.locfileid: "129812413"
---
# <a name="form-recognizer-studio--preview"></a>Formularerkennung Studio | Vorschau

>[!NOTE]
> Formularerkennung Studio befindet sich derzeit in der öffentlichen Vorschauphase. Dies bedeutet, dass einige Features unter Umständen nicht unterstützt werden oder nur eingeschränkt verwendbar sind.

[Formularerkennung Studio (Vorschau)](https://formrecognizer.appliedai.azure.com/) ist ein Onlinetool für das grafische Erkunden und Kennenlernen der Features des Diensts „Formularerkennung“ und das Integrieren in Ihre Anwendungen. Verwenden Sie den [Schnellstart für Formularerkennung Studio](quickstarts/try-v3-form-recognizer-studio.md), um die vorab trainierten Modelle mit Beispieldokumenten oder Ihren eigenen Dokumenten zu erkunden. Erstellen Sie Projekte für die Entwicklung von benutzerdefinierten Formularmodellen, und verweisen Sie in Ihren Anwendungen darauf, indem Sie die Informationen zum [Python SDK (Vorschau)](quickstarts/try-v3-python-sdk.md) und in anderen Schnellstartanleitungen verwenden.

Die folgende Abbildung zeigt das Feature des vordefinierten Rechnungsmodells bei der Arbeit.

:::image border="true" type="content" source="media/quickstarts/prebuilt-get-started-v2.gif" alt-text="Formularerkennung: Beispiel für vordefinierte Modelle":::

## <a name="form-recognizer-studio-features"></a>Features von Formularerkennung Studio

Die folgenden Features des Formularerkennungsdiensts sind in Studio verfügbar.

* **Layout**: Testen Sie das Layoutfeature der Formularerkennung zum Extrahieren von Text, Tabellen, Auswahlmarkierungen und Strukturinformationen aus Dokumenten (PDF, TIFF) und Bildern (JPG, PNG, BMP). Beginnen Sie mit dem [Schnellstart für das Studio-Layout](quickstarts/try-v3-form-recognizer-studio.md#layout). Erkunden Sie dies mit Beispielen und Ihren Dokumenten. Verwenden Sie die interaktive Visualisierung und die JSON-Ausgabe, um zu verstehen, wie das Feature funktioniert. In der [Layoutübersicht](concept-layout.md) finden Sie weitere Informationen zum [Python SDK-Schnellstart für das Layout](quickstarts/try-v3-python-sdk.md#try-it-layout-model) und zu den ersten Schritten.

* **Vordefinierte Modelle**: Mit den vordefinierten Modellen der Azure-Formularerkennung können Sie Ihren Anwendungen und Abläufen eine intelligente Formularverarbeitung hinzufügen, ohne dass Sie Ihre eigenen Modelle trainieren und erstellen müssen. Beginnen Sie mit dem [Schnellstart für vordefinierte Studio-Modelle](quickstarts/try-v3-form-recognizer-studio.md#prebuilt-models). Erkunden Sie dies mit Beispielen und Ihren Dokumenten. Verwenden Sie die interaktive Visualisierung, die Liste der extrahierten Felder und die JSON-Ausgabe, um zu verstehen, wie das Feature funktioniert. In der [Modellübersicht](concept-model-overview.md) finden Sie weitere Informationen zum [Python SDK-Schnellstart für die vordefinierte Rechnung](quickstarts/try-v3-python-sdk.md#try-it-prebuilt-invoice-model) und zu den ersten Schritten.

* **Benutzerdefinierte Modelle**: Die benutzerdefinierten Modelle der Formularerkennung ermöglichen es Ihnen, Felder und Werte aus Modellen zu extrahieren, die mit Ihren Daten trainiert wurden und auf Ihre Formulare und Dokumente zugeschnitten sind. Erstellen Sie eigenständige benutzerdefinierte Modelle, oder kombinieren Sie zwei oder mehr benutzerdefinierte Modelle, um ein zusammengesetztes Modell zur Extraktion von Daten aus mehreren Formulartypen zu erstellen. Beginnen Sie mit dem [Schnellstart für benutzerdefinierte Studio-Modelle](quickstarts/try-v3-form-recognizer-studio.md#custom-model-basics).  Verwenden Sie den Online-Assistenten, die Beschriftungsschnittstelle, das Training und die Visualisierungen, um zu verstehen, wie das Feature funktioniert. Testen Sie das benutzerdefinierte Modell mit Ihren Beispieldokumenten, und verbessern Sie es durch Iteration. Lesen Sie die [Übersicht über benutzerdefinierte Modelle](concept-custom.md), um mehr zu erfahren, und verwenden Sie den [Migrationsleitfaden für die Formularerkennung v3.0 (Vorschau)](v3-migration-guide.md), um mit der Integration der neuen Modelle in Ihre Anwendungen zu beginnen.

* **Benutzerdefinierte Modelle: Beschriftungsfeatures**: Die Erstellung benutzerdefinierter Modell der Formularerkennung erfordert das Identifizieren der zu extrahierenden Felder und das Beschriften der Inhalte in Ihren Dokumenten mit diesen Feldern, bevor die benutzerdefinierten Modelle trainiert werden. Beschriftungstexte, Auswahlmarkierungen, Tabellendaten und andere Inhaltstypen werden in der Regel durch eine Benutzeroberfläche unterstützt, um den Workflow für das Training zu erleichtern. Verwenden Sie z. B. die Schnellstartanleitungen [Beschriftung als Tabellen](quickstarts/try-v3-form-recognizer-studio.md#labeling-as-tables) und [Beschriftung zur Unterschriftserkennung](quickstarts/try-v3-form-recognizer-studio.md#labeling-for-signature-detection), um die Beschriftungsumgebung in Formularerkennung Studio zu verstehen.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erste Schritte: Formularerkennung Studio (Vorschau)](https://formrecognizer.appliedai.azure.com)

