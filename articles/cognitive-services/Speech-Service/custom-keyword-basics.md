---
title: 'Schnellstart: Erstellen von Schlüsselwörtern – Speech-Dienst'
titleSuffix: Azure Cognitive Services
description: Ihr Gerät lauscht immer auf ein Schlüsselwort (oder einen Ausdruck). Wenn ein Benutzer das Schlüsselwort spricht, sendet Ihr Gerät sein Diktat an die Cloud, bis der Benutzer aufhört zu sprechen. Durch das Anpassen Ihres Schlüsselworts können Sie Ihr Gerät auf effektive Weise differenzieren und Ihr Branding stärken.
services: cognitive-services
author: eric-urban
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/12/2021
ms.author: eur
ms.custom: devx-track-csharp, ignite-fall-2021
zone_pivot_groups: keyword-quickstart
ms.openlocfilehash: 59756ec624bfc4d716bcf222195f53029be05420
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493181"
---
# <a name="get-started-with-custom-keyword"></a>Erste Schritte mit dem benutzerdefinierten Schlüsselwort

In dieser Schnellstartanleitung erfahren Sie die Grundlagen der Arbeit mit benutzerdefinierten Schlüsselwörtern. Ein Schlüsselwort ist ein Wort oder ein kurzer Ausdruck, mit dem Ihr Produkt per Sprache aktiviert werden kann. Sie erstellen Schlüsselwortmodelle in Speech Studio. Exportieren Sie dann eine Modelldatei, die Sie mit dem Speech SDK in Ihren Anwendungen verwenden.

## <a name="prerequisites"></a>Voraussetzungen

Die Schritte in diesem Artikel erfordern ein Speech-Abonnement und das Speech SDK. Wenn Sie nicht bereits ein Abonnement besitzen, [können Sie den Speech-Dienst kostenlos testen](overview.md#try-the-speech-service-for-free). Informationen zum Erhalt des SDK finden Sie im [Installationshandbuch](quickstarts/setup-platform.md) für Ihre Plattform.

## <a name="create-a-keyword-in-speech-studio"></a>Erstellen eines Schlüsselworts in Speech Studio

Bevor Sie ein benutzerdefiniertes Schlüsselwort verwenden können, müssen Sie auf der Seite [Benutzerdefiniertes Schlüsselwort](https://aka.ms/sdsdk-wakewordportal) in [Speech Studio](https://aka.ms/sdsdk-speechportal) ein Schlüsselwort erstellen. Nachdem Sie ein Schlüsselwort eingegeben haben, wird eine `.table`-Datei erzeugt, die Sie mit dem Speech SDK verwenden können.

> [!IMPORTANT]
> Benutzerdefinierte Schlüsselwortmodelle und die daraus resultierenden `.table`-Dateien können **nur** in Speech Studio erstellt werden.
> Sie können keine benutzerdefinierten Schlüsselwörter über das SDK oder mit REST-Aufrufen erstellen.

1. Wechseln Sie zum [Speech Studio](https://aka.ms/sdsdk-speechportal), und **melden** Sie sich an. Wenn Sie nicht über ein Speech-Abonnement verfügen, wechseln Sie zu [**Erstellen von Speech-Diensten**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Wählen Sie auf der Seite [Benutzerdefiniertes Schlüsselwort](https://aka.ms/sdsdk-wakewordportal) die Option **Neues Projekt erstellen** aus. 

1. Geben Sie einen **Namen**, eine **Beschreibung** und eine **Sprache** für Ihr benutzerdefiniertes Schlüsselwortprojekt ein. Sie können nur eine Sprache pro Projekt wählen, und die Unterstützung ist derzeit auf Englisch (USA) und Chinesisch (Mandarin, vereinfacht) beschränkt. 

    ![Beschreiben Ihres Schlüsselwortprojekts](media/custom-keyword/custom-kw-portal-new-project.png)

1. Wählen Sie den Namen Ihres Projekts aus der Liste aus. 

    :::image type="content" source="media/custom-keyword/custom-kw-portal-project-list.png" alt-text="Wählen Sie Ihr Schlüsselwortprojekt aus.":::

1. Um ein benutzerdefiniertes Schlüsselwort für Ihren virtuellen Assistenten zu erstellen, wählen Sie **Neues Modell erstellen** aus.

1. Geben Sie einen **Namen** für das Modell, eine **Beschreibung** und ein **Schlüsselwort** Ihrer Wahl ein, und wählen Sie dann **Weiter** aus. Weitere Informationen zum Auswählen eines effektiven Schlüsselworts finden Sie in den [Richtlinien](keyword-recognition-guidelines.md#choosing-an-effective-keyword).

    ![Eingeben Ihres Schlüsselworts](media/custom-keyword/custom-kw-portal-new-model.png)

1. Im Portal werden nun Kandidaten für die Aussprache Ihres Schlüsselworts erstellt. Hören Sie sich jeden Kandidaten an, indem Sie auf die Wiedergabeschaltflächen klicken und die Häkchen bei den falschen Aussprachen entfernen. Wählen Sie alle Aussprachen aus, die den Erwartungen Ihrer Benutzer entsprechen, und wählen Sie dann **Weiter** aus, um mit der Erstellung des Schlüsselwortmodells zu beginnen. 

    :::image type="content" source="media/custom-keyword/custom-kw-portal-choose-prons.png" alt-text="Screenshot, der zeigt, wo Sie die richtigen Aussprachen auswählen.":::

1. Wählen Sie einen Modelltyp und dann **Erstellen** aus. Eine Liste der Regionen, die den Modelltyp **Erweitert** unterstützen, finden Sie in der Dokumentation zur [Unterstützung von Regionen für die Schlüsselworterkennung](keyword-recognition-region-support.md). 

1. Die Generierung des Modells kann bis zu 30 Minuten dauern. Wenn das Modell fertig ist, ändert sich die Liste mit den Schlüsselwörtern von **Wird verarbeitet...** in **Erfolgreich**. 

    :::image type="content" source="media/custom-keyword/custom-kw-portal-review-keyword.png" alt-text="Überprüfen Sie Ihr Schlüsselwort.":::

1. Wählen Sie im reduzierbaren Menü auf der linken Seite die Option **Optimieren** aus, um Ihr Modell zu optimieren und herunterzuladen. Die heruntergeladene Datei ist ein `.zip`-Archiv. Extrahieren Sie das Archiv, und Sie erhalten eine Datei mit der Erweiterung `.table`. Sie verwenden die `.table`-Datei mit dem SDK. Notieren Sie sich daher unbedingt ihren Pfad.

    :::image type="content" source="media/custom-keyword/custom-kw-portal-download-model.png" alt-text="Laden Sie Ihre Modelltabelle herunter.":::


## <a name="use-a-keyword-model-with-the-speech-sdk"></a>Verwenden eines Schlüsselwortmodells mit dem Speech SDK

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/keyword-recognition/keyword-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/keyword-recognition/keyword-basics-python.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [ObjectiveC/Swift Basics include](includes/how-to/keyword-recognition/keyword-basics-objc.md)]
::: zone-end

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Abrufen des Speech SDK](speech-sdk.md)
