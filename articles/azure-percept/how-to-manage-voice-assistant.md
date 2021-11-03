---
title: Verwalten Ihrer Sprach-Assistent-Anwendung für Azure Percept
description: Konfigurieren eines Sprach-Assistenten in Azure Percept Studio
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: 7e3ab60b3f89db21c9dbc6290733b2cab365dad9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131006352"
---
# <a name="manage-your-azure-percept-voice-assistant-application"></a>Verwalten Ihrer Sprach-Assistent-Anwendung für Azure Percept

In diesem Artikel wird beschrieben, wie Sie das Schlüsselwort und die Befehle Ihres Sprach-Assistenten in [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) konfigurieren. Eine Anleitung zum Konfigurieren des Schlüsselworts in IoT Hub und nicht im Portal finden Sie in [diesem Artikel](./how-to-configure-voice-assistant.md).

Haben Sie noch keinen Sprach-Assistenten erstellt, finden Sie weitere Informationen unter [Erstellen eines Sprach-Assistenten ohne Code mit Azure Percept Studio und Azure Percept Audio](./tutorial-no-code-speech.md).

## <a name="keyword-configuration"></a>Schlüsselwortkonfiguration

Ein Schlüsselwort ist ein Wort oder ein kurzer Ausdruck, mit dem ein Sprach-Assistent aktiviert wird. „Hey Cortana“ ist z. B. das Schlüsselwort für den Cortana-Assistenten. Die Sprachaktivierung ermöglicht es Ihren Benutzern, ohne Verwendung ihrer Hände mit Ihrem Produkt zu interagieren, indem sie das Schlüsselwort aussprechen. Da Ihr Produkt kontinuierlich auf das Schlüsselwort lauscht, werden sämtliche Audiodaten lokal auf dem Gerät verarbeitet, bis eine Erkennung erfolgt, um sicherzustellen, dass die Benutzerdaten weitestgehend geschützt sind.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Konfiguration im Demofenster für den Sprach-Assistenten

1. Wählen Sie auf der Demoseite neben **Benutzerdefiniertes Schlüsselwort** die Option **Ändern** aus.

    :::image type="content" source="./media/manage-voice-assistant/hospitality-demo.png" alt-text="Screenshot: Fenster mit der Demo für das Hotel- und Gaststättengewerbe":::

    Ist die Demoseite nicht geöffnet, navigieren Sie zur Geräteseite (siehe unten), und wählen Sie unter **Aktionen** die Option **Sprach-Assistenten testen** aus, um auf die Demo zuzugreifen.

1. Wählen Sie eines der verfügbaren Schlüsselwörter aus, und wählen Sie dann **Speichern** aus, um die Änderungen zu übernehmen.

1. Die drei LED-Leuchten auf dem Azure Percept-Audiogerät wechseln zu hellblau (kein Blinken), wenn die Konfiguration abgeschlossen ist und der Sprach-Assistent verwendet werden kann.

### <a name="configuration-within-the-device-page"></a>Konfiguration auf der Geräteseite

1. Wählen Sie auf der Übersichtsseite von [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) im linken Menübereich **Geräte** aus.

    :::image type="content" source="./media/manage-voice-assistant/portal-overview-devices.png" alt-text="Screenshot: Übersichtsseite von Azure Percept Studio mit hervorgehobener Option „Geräte“":::

1. Wählen Sie das Gerät aus, auf dem der Sprach-Assistent bereitgestellt wurde.

1. Öffnen Sie die Registerkarte **Sprache**.

    :::image type="content" source="./media/manage-voice-assistant/device-page.png" alt-text="Screenshot: Seite „Edgegerät“ mit hervorgehobener Registerkarte „Sprache“":::

1. Wählen Sie neben **Schlüsselwort** die Option **Ändern** aus.

    :::image type="content" source="./media/manage-voice-assistant/change-keyword-device.png" alt-text="Screenshot: Verfügbare Aktionen der Sprachlösung":::

1. Wählen Sie eines der verfügbaren Schlüsselwörter aus, und wählen Sie dann **Speichern** aus, um die Änderungen zu übernehmen.

1. Die drei LED-Leuchten auf dem Azure Percept-Audiogerät wechseln zu hellblau (kein Blinken), wenn die Konfiguration abgeschlossen ist und der Sprach-Assistent verwendet werden kann.

## <a name="create-a-custom-keyword"></a>Erstellen eines benutzerdefinierten Schlüsselworts

Mit [Speech Studio](https://speech.microsoft.com/)können Sie ein benutzerdefiniertes Schlüsselwort für Ihren Sprach-Assistenten erstellen. Es dauert bis zu 30 Minuten, ein einfaches Modell für benutzerdefinierte Schlüsselwörter zu trainieren.

Anweisungen zum Erstellen eines benutzerdefinierten Schlüsselworts finden Sie in der [Dokumentation zu Speech Studio](../cognitive-services/speech-service/custom-keyword-basics.md). Nach der Konfiguration steht das neue Schlüsselwort im Project Santa Cruz-Portal für die Verwendung mit dem Sprach-Assistenten zur Verfügung.

## <a name="commands-configuration"></a>Befehlskonfiguration

Mit benutzerdefinierten Befehlen ist es einfach, funktionsreiche Apps mit Sprachsteuerung zu erstellen, die für Voice-First-Interaktionserfahrungen optimiert sind. Daher sind benutzerdefinierte Befehle am besten für die Aufgabenerledigung oder für Befehl-und-Steuerung-Szenarien (Command-and-Control) geeignet.

### <a name="configuration-within-the-voice-assistant-demo-window"></a>Konfiguration im Demofenster für den Sprach-Assistenten

1. Wählen Sie auf der Demoseite neben **Benutzerdefinierter Befehl** die Option **Ändern** aus. Ist die Demoseite nicht geöffnet, navigieren Sie zur Geräteseite (siehe unten), und wählen Sie unter **Aktionen** die Option **Sprach-Assistenten testen** aus, um auf die Demo zuzugreifen.

1. Wählen Sie einen der verfügbaren benutzerdefinierten Befehle und dann **Speichern** aus, um die Änderungen zu übernehmen.

### <a name="configuration-within-the-device-page"></a>Konfiguration auf der Geräteseite

1. Wählen Sie auf der Übersichtsseite von [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) im linken Menübereich **Geräte** aus.

1. Wählen Sie das Gerät aus, auf dem der Sprach-Assistent bereitgestellt wurde.

1. Öffnen Sie die Registerkarte **Sprache**.

1. Wählen Sie neben **Befehl** die Option **Ändern** aus.

1. Wählen Sie einen der verfügbaren benutzerdefinierten Befehle und dann **Speichern** aus, um die Änderungen zu übernehmen.

## <a name="create-custom-commands"></a>Erstellen von benutzerdefinierten Befehlen

Mit [Speech Studio](https://speech.microsoft.com/) können Sie benutzerdefinierte Befehle erstellen, die vom Sprach-Assistenten ausgeführt werden sollen.

Anweisungen zum Erstellen von benutzerdefinierten Befehlen finden Sie in der [Dokumentation zu Speech Studio](../cognitive-services/speech-service/quickstart-custom-commands-application.md). Nach der Konfiguration stehen Ihre neuen Befehle in Azure Percept Studio zur Verwendung mit dem Sprach-Assistenten zur Verfügung.

## <a name="next-steps"></a>Nächste Schritte

Nach dem Erstellen eines Sprach-Assistenten können Sie mit Azure Percept DK eine [Vision-Lösung ohne Code](./tutorial-nocode-vision.md) entwickeln.
