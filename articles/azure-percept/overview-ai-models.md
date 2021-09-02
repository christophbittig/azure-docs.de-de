---
title: Azure Percept-KI-Beispielmodelle
description: Weitere Informationen zu den für die Prototypenerstellung und Bereitstellung verfügbaren KI-Modellen
author: tsampige
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: template-concept
ms.openlocfilehash: 78eb20b0229faef9c067b4bab475759badccd19e
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221278"
---
# <a name="azure-percept-sample-ai-models"></a>Azure Percept-KI-Beispielmodelle

Azure Percept ermöglicht Ihnen das Entwickeln und Bereitstellen von KI-Modellen direkt in Ihrer [Azure Percept DK](./overview-azure-percept-dk.md)-Instanz aus [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819). Die Modellimplementierung verwendet [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) und [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/#iotedge-overview).

## <a name="sample-ai-models"></a>KI-Beispielmodelle

Azure Percept Studio enthält Beispielmodelle für die folgenden Anwendungen:

- Personenerkennung
- Fahrzeugerkennung
- Allgemeine Objekterkennung
- Erkennung von Produkten in Regalen

Bei vortrainierten Modellen ist keine Codierungs-oder Sammlung von Trainingsdaten erforderlich. [Stellen Sie einfach das gewünschte Modell](./how-to-deploy-model.md) aus dem Portal in Ihrer Azure Percept DK-Instanz bereit, und öffnen Sie den [Videostream](./how-to-view-video-stream.md) Ihres DevKits, um die Modellrückschlüsse in Aktion zu sehen. Sie können auch über das [Azure IoT-Explorer-Tool](https://github.com/Azure/azure-iot-explorer/releases) auf [Telemetriedaten der Modellrückschlüsse](./how-to-view-telemetry.md) zugreifen.

## <a name="reference-solutions"></a>Referenzlösungen

Eine [Referenzlösung zur Zählung von Personen](https://github.com/microsoft/Azure-Percept-Reference-Solutions/tree/main/people-detection-app) ist ebenfalls verfügbar. Bei dieser Referenzlösung handelt es sich um eine Open-Source-KI-Anwendung, die Edge-basierte Personenzählung bereitstellt, mit benutzerdefinierten Betreten-/Verlassen-Ereignissen für eine Zone. Die Video- und KI-Ausgabe des lokalen Edgegeräts wird an [Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) ausgegeben, wobei die Benutzeroberfläche als Azure-Website ausgeführt wird. KI-Rückschlüsse werden von einem Open Source-KI-Modell für die Personenerkennung bereitgestellt.

:::image type="content" source="./media/overview-ai-models/people-detector.gif" alt-text="GIF der vorgefertigten Lösung für räumliche Analysen.":::

## <a name="custom-no-code-solutions"></a>Benutzerdefinierte Lösungen ohne Code

Mithilfe von Azure Percept Studio können Sie benutzerdefinierte [Vision](./tutorial-nocode-vision.md)- und [Sprachlösungen](./tutorial-no-code-speech.md) entwickeln, ohne dass Sie dazu programmieren müssen.

Für benutzerdefinierte Vision-Lösungen sind KI-Modelle sowohl für die Objekterkennung als auch -klassifizierung verfügbar. Laden Sie einfach Ihre Trainingsbilder hoch, und kennzeichnen Sie sie. Die Bilder können, falls gewünscht, direkt mit dem Azure Percept Vision-SoM von Azure Percept DK aufgenommen werden. Training und Auswertung von Modellen können problemlos in [Custom Vision](https://www.customvision.ai/) ausgeführt werden, das Bestandteil von [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/#overview) ist.

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

Für benutzerdefinierte Sprachlösungen sind derzeit Sprach-Assistenten-Vorlagen für folgende Anwendungen verfügbar:

- Hotel- und Gaststättengewerbe: Hotelzimmer mit intelligenten Geräten mit Sprachsteuerung.
- Gesundheitswesen: Pflegeeinrichtungen mit intelligenten Geräten mit Sprachsteuerung.
- Inventur: Hub für die Bestandsverwaltung mit intelligenten Geräten mit Sprachsteuerung.
- Automobilbau: Hub für die Kommunikation in Fahrzeugen mit intelligenten Geräten mit Sprachsteuerung.

Vorgefertigte Sprach-Assistenten-Schlüsselwörter und -Befehle sind direkt über das Portal verfügbar. Benutzerdefinierte Schlüsselwörter und Befehle können in [Speech Studio](https://speech.microsoft.com/) erstellt und trainiert werden, das ebenfalls Bestandteil von Azure Cognitive Services ist.

## <a name="advanced-development"></a>Komplexe Entwicklung

Aktuelle Anleitungen, Tutorials und Beispiele zu den folgenden Themen finden Sie im [GitHub-Repository zur erweiterten Entwicklung mit Azure Percept DK](https://github.com/microsoft/azure-percept-advanced-development):

- Bereitstellen eines benutzerdefinierten KI-Modells in Azure Percept DK
- Aktualisieren eines unterstützten Modells mit dem Lerntransfer
- und mehr...
