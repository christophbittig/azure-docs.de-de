---
title: 'Schnellstart: Hinzufügen von Azure-Ressourcen zu Ihrer IoT-Lösung'
description: In dieser Schnellstartanleitung erfahren Sie, wie Sie Ihre End-to-End-IoT-Lösung mit Azure Defender für IoT konfigurieren.
ms.topic: quickstart
ms.date: 09/13/2021
ms.openlocfilehash: e66accdc1ba671941c13433eeca9b3e9c541781b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634822"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>Schnellstart: Konfigurieren Ihrer Lösung mit Azure Defender für IoT

In diesem Artikel wird erläutert, wie Sie Ihre IoT-Sicherheitslösung mithilfe von Defender für IoT zum ersten Mal konfigurieren.

## <a name="prerequisites"></a>Voraussetzungen

- Keine

## <a name="what-is-defender-for-iot"></a>Was ist Defender für IoT?

Defender für IoT bietet umfassende End-to-End-Sicherheit für Azure-basierte IoT-Lösungen.

Mit Defender für IoT können Sie Ihre gesamte IoT-Lösung über ein zentrales Dashboard überwachen, auf dem alle Ihre IoT-Geräte, IoT-Plattformen und Back-End-Ressourcen in Azure angezeigt werden.

Nachdem Sie Defender für IoT in Ihrer IoT Hub-Instanz aktiviert haben, identifiziert Defender für IoT andere Azure-Dienste automatisch und stellt eine Verbindung mit verwandten Diensten her, die Ihrer IoT-Lösung zugeordnet sind.

Sie können auch andere Azure-Ressourcengruppen auswählen, die zu Ihrer IoT-Lösung gehören.

Dadurch können Sie vollständige Abonnements, Ressourcengruppen oder auch einzelne Ressourcen hinzufügen.

Nachdem Sie alle Ressourcenbeziehungen definiert haben, stellt Ihnen Defender für IoT mithilfe von Defender Sicherheitsempfehlungen und Warnungen für diese Ressourcen bereit.

## <a name="add-azure-resources-to-your-iot-solution"></a>Hinzufügen von Azure-Ressourcen zu Ihrer IoT-Lösung

**So fügen Sie Ihrer IoT-Lösung eine neue Ressource hinzu**:

1. Suchen Sie im Azure-Portal nach **IoT Hub**, und wählen Sie den Eintrag aus.

1. Wählen Sie unter dem Abschnitt „Sicherheit“ **Einstellungen** > **Überwachte Ressourcen** aus.

1. Wählen Sie **Bearbeiten** und dann die überwachten Ressourcen aus, die zu Ihrer IoT-Lösung gehören.

1. Wählen Sie **Hinzufügen**.

Ihrer IoT-Lösung wird eine neue Ressourcengruppe hinzugefügt.

Defender für IoT überwacht jetzt Ihre neu hinzugefügten Ressourcengruppen und zeigt relevante Sicherheitsempfehlungen sowie Warnungen im Rahmen Ihrer IoT-Lösung an.

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel fort, um sich über das Erstellen von Defender-IoT-Micro-Agents zu informieren:

> [!div class="nextstepaction"]
> [Erstellen von Defender-IoT-Micro-Agents](quickstart-create-security-twin.md)
