---
title: Swagger-Dokumentation – Speech-Dienst
titleSuffix: Azure Cognitive Services
description: Die Swagger-Dokumentation kann zum automatischen Generieren von SDKs für verschiedene Programmiersprachen verwendet werden. Alle Vorgänge unseres Diensts werden von Swagger unterstützt.
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 02/16/2021
ms.author: alexeyo
ms.openlocfilehash: edc01f30ea3090f5c95a1da16e4d62feecbb18cc
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989278"
---
# <a name="swagger-documentation"></a>Swagger-Dokumentation

Der Speech-Dienst verfügt über eine Swagger-Spezifikation zum Interagieren mit einigen REST-APIs, die zum Importieren von Daten, Erstellen von Modellen, Testen der Modellgenauigkeit, Erstellen von benutzerdefinierten Endpunkten, Einreihen von Batchtranskriptionen in die Warteschlange und Verwalten von Abonnements verwendet werden. Die meisten Vorgänge, die über den [Custom Speech-Bereich von Speech Studio](https://aka.ms/speechstudio/customspeech) verfügbar sind, können mit diesen APIs programmgesteuert durchgeführt werden.

> [!NOTE]
> Der Speech-Dienst verfügt über mehrere REST-APIs für die [Spracherkennung](rest-speech-to-text.md) und die [Sprachsynthese](rest-text-to-speech.md).  
>
> In der Swagger-Spezifikation sind jedoch nur die [Spracherkennungs-REST-API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) und die Spracherkennungs-REST-API v2.0 dokumentiert. Informationen zu allen anderen REST-APIs der Speech-Dienste finden Sie in den Dokumenten, auf die im vorherigen Abschnitt verwiesen wurde.

## <a name="generating-code-from-the-swagger-specification"></a>Generieren von Code über die Swagger-Spezifikation

Die [Swagger-Spezifikation](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0) verfügt über Optionen, die Ihnen das schnelle Testen für verschiedene Pfade ermöglichen. Es kann aber auch der Fall sein, dass Code für alle Pfade generiert werden soll, um eine zentrale Bibliothek mit Aufrufen zu erstellen, die als Grundlage für zukünftige Lösungen dienen kann. Wir sehen uns nun den Prozess zum Generieren einer Python-Bibliothek an.

Swagger muss auf die Region Ihrer Speech-Ressource festgelegt werden. Die Region können Sie im Azure-Portal im Bereich **Übersicht** Ihrer Speech-Ressourceneinstellungen überprüfen. Die vollständige Liste der unterstützten Regionen finden Sie [hier](regions.md#speech-to-text).

1. Navigieren Sie in einem Browser zur Swagger-Spezifikation für Ihre [Region](regions.md#speech-to-text):  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. Klicken Sie auf dieser Seite auf **API-Definition** und anschließend auf **Swagger**. Kopieren Sie die URL der Seite, die angezeigt wird.
1. Navigieren Sie in einem neuen Browser zu [https://editor.swagger.io](https://editor.swagger.io).
1. Klicken Sie auf **Datei** und **Import URL** (URL importieren), kopieren Sie die URL, und klicken Sie dann auf **OK**.
1. Klicken Sie auf **Generate Client** (Client generieren), und wählen Sie **Python** aus. Die Clientbibliothek wird in einer `.zip`-Datei auf den Computer heruntergeladen.
1. Extrahieren Sie alles aus dem Download. Sie können `tar -xf` verwenden, um alles zu extrahieren.
1. Installieren Sie das extrahierte Modul in Ihrer Python-Umgebung:  
      `pip install path/to/package/python-client`
1. Das installierte Paket hat den Namen `swagger_client`. Vergewissern Sie sich, dass die Installation erfolgreich war:  
       `python -c "import swagger_client"`

Sie können die Python-Bibliothek verwenden, die Sie mit den [Speech-Dienstbeispielen auf GitHub](https://aka.ms/csspeech/samples) generiert haben.

## <a name="reference-documents"></a>Referenzdokumente

* [Swagger: Spracherkennungs-REST-API v3.0](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [Spracherkennungs-REST-API](rest-speech-to-text.md)
* [Text-to-Speech-REST-API](rest-text-to-speech.md)

## <a name="next-steps"></a>Nächste Schritte

* [Speech-Dienstbeispiele auf GitHub](https://aka.ms/csspeech/samples)
* [Kostenloses Testen des Speech-Diensts](overview.md#try-the-speech-service-for-free)
