---
title: Integrieren von Forescout in Microsoft Defender für loT
description: In diesem Tutorial erfahren Sie, wie Sie Microsoft Defender für loT in Forescout integrieren.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 11/09/2021
ms.custom: template-tutorial
ms.openlocfilehash: c395c799b8f6dca602b20cb330f89630f9fb591e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132325234"
---
# <a name="tutorial-integrate-forescout-with-microsoft-defender-for-iot"></a>Tutorial: Integrieren von Forescout in Microsoft Defender für loT

> [!Note]
> Verweise auf CyberX beziehen sich auf Microsoft Defender für IoT.

In diesem Tutorial erfahren Sie, wie Sie Forescout in Microsoft Defender für loT integrieren.

Microsoft Defender für loT bietet eine ICS- und loT-Cybersicherheitsplattform. Defender für loT ist die einzige Plattform mit ICS-bewusster Bedrohungsanalyse: und Computer-Lernen. Defender für IoT bietet Folgendes:

- Unmittelbare Erkenntnisse zu ICS in der Gerätelandschaft und eine umfangreiche Palette von Details zu Attributen.

- ICS-fähige Deep Embedded-Kenntnisse zu OT-Protokollen, Geräten, Anwendungen und deren Verhalten

- Unmittelbare Erkenntnisse zu Sicherheitsrisiken und bekannten Zero-Day-Bedrohungen

- Automatisierte ICS-Bedrohungsmodellierungstechnologie, mit der die wahrscheinlichsten Pfade von gezielten ICS-Angriffen über proprietäre Analysen vorhergesagt werden können

Mit der Forescout-Integration können Sie die Zeit verringern, die für Industrieorganisationen und kritische Infrastrukturorganisationen erforderlich ist, um Cyberbedrohungen zu erkennen, zu untersuchen und darauf zu reagieren.

- Verwenden Sie OT-Geräteinformationen von Microsoft Defender für IoT, um den Sicherheitszeitraum durch Auslösen von Forescout-Richtlinienaktionen zu schließen. Beispielsweise können Sie automatisch eine Warn-E-Mail an SOC-Administratoren senden, wenn bestimmte Protokolle erkannt werden oder wenn sich Firmwaredetails ändern.

- Korrelieren Sie Defender für IoT-Informationen mit anderen *Forescout eyeExtended*-Modulen, die Überwachung, Incident Management und Gerätesteuerung kontrollieren.

Die Defender für loT-Integration mit der Forescout-Plattform stellt eine zentralisierte Sichtbarkeit, Überwachung und Steuerung für die loT- und OT-Landschaft bereit. Diese überbrückten Plattformen ermöglichen eine automatisierte Verwaltung der Gerätetransparenz für ICS-Geräte und isolierte Workflows. Die Integration bietet SOC-Analytikern mehrstufige Einblicke in OT-Protokolle, die in Industrieumgebungen bereitgestellt werden. Informationen wie Firmware, Gerätetypen, Betriebssysteme und Risikoanalysebewertungen werden auf der Grundlage von Microsoft Defender für loT-Technologien verfügbar.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> - Erstellen von einem Zugriffstoken
> - Konfigurieren von der Forescout-Plattform
> - Überprüfen der Kommunikation
> - Anzeigen von den Geräteattributen in Forescout
> - Erstellen von Microsoft Defender für IoT-Richtlinien in Forescout

Wenn Sie noch nicht über ein Azure-Konto verfügen, können Sie [noch heute Ihr kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Voraussetzungen

- Microsoft Defender für IoT, Version 2.4 oder höher

- Forescout, Version 8.0 oder höher

- Eine Lizenz für das Forescout eyeExtended-Modul für die Microsoft Defender für IoT-Plattform.

## <a name="generate-an-access-token"></a>Erstellen von einem Zugriffstoken

Zugriffstoken ermöglichen externen Systemen den Zugriff auf die von Defender für loT entdeckten Daten. Zugriffstoken ermöglichen es, dass diese Daten für externe REST-APIs und über SSL-Verbindungen verwendet werden können. Sie können Zugriffstoken für den Zugriff auf die REST-API von Microsoft Defender für IoT generieren.

Um die Kommunikation von Defender für loT zu Forescout zu gewährleisten, müssen Sie ein Zugriffstoken in Defender für loT erstellen.

**Erstellen von einem Zugriffstoken**:

1. Melden Sie sich bei dem Defender für loT-Sensor an, der von Forescout abgefragt werden wird.

1. Wählen Sie **Systemeinstellungen** > **Zugriffstoken** aus dem Abschnitt **Allgemein**.

1. Wählen Sie **Neues Token generieren** aus.

    :::image type="content" source="media/tutorial-forescout/generate-access-tokens-screen.png" alt-text="Screenshot von dem Bildschirm zur Erstellung von Zugangstoken.":::

1. Geben Sie im Dialogfeld **Neues Zugriffstoken** eine Tokenbeschreibung ein.

   :::image type="content" source="media/tutorial-forescout/new-forescout-token.png" alt-text="Neues Zugriffstoken":::

1. Wählen Sie **Weiter** aus. Der Token wird dann im Dialogfeld angezeigt.

   > [!NOTE]
   > Bewahren Sie den Token an einem sicheren Ort auf. Sie benötigen ihn, wenn Sie die Forescout-Plattform konfigurieren.

1. Wählen Sie **Fertig stellen** aus.

   :::image type="content" source="media/tutorial-forescout/forescout-access-token-added-successfully.png" alt-text="Hinzufügen des Tokens beenden":::

## <a name="configure-the-forescout-platform"></a>Konfigurieren Sie die Forescout-Plattform

Sie können nun die Forescout-Plattform für die Kommunikation mit einem Defender für loT-Sensor konfigurieren.

**Konfigurieren von der Forescout-Plattform**:

1. Suchen Sie auf der Forescout-Plattform nach dem **Forescout eyeExtend-Modul für CyberX**, und installieren Sie es.

1. Melden Sie sich an der CounterACT-Konsole an.

1. Wählen Sie Optionen im Menü **Extras** aus.

1. Navigieren Sie zu **Module** > **CyberX Plattform**.

   :::image type="content" source="media/tutorial-forescout/settings-for-module.png" alt-text="Einstellungen des Microsoft Defender für IoT-Moduls":::

1. Geben Sie in das Feld Server-Adresse die IP-Adresse des Defender für loT-Sensors ein, der von der Forescout-Appliance abgefragt werden soll.

1. Geben Sie in das Feld "Access Token" den zuvor generierten Access Token ein.

1. Wählen Sie **Übernehmen**.

### <a name="change-sensors-in-forescout"></a>Ändern von Sensoren in Forescout

Um die Forescout-Plattform mit einem anderen Sensor kommunizieren zu lassen, muss die Konfiguration in Forescout geändert werden.

**Um Sensoren in Forescout zu ändern**:

1. Erstellen Sie ein neues Zugriffstoken im relevanten Defender für IoT-Sensor.

1. Navigieren Sie zu **Forescout Module** > **CyberX Platform**.

1. Löschen Sie die in beiden Feldern angezeigten Informationen.

1. Melden Sie sich am neuen Defender für loT-Sensor an und [generieren Sie einen neuen Zugriffstoken](#generate-an-access-token).

1. Geben Sie in das Feld "Serveradresse" die neue IP-Adresse des Defender für loT-Sensoren ein, der von der Forescout-Appliance abgefragt werden soll.

1. Geben Sie in das Feld Access Token den neuen Zugriffstoken ein.

1. Wählen Sie **Übernehmen**.

## <a name="verify-communication"></a>Überprüfen der Kommunikation

Nachdem die Verbindung konfiguriert wurde, müssen Sie bestätigen, dass die beiden Plattformen miteinander kommunizieren.

**Um zu bestätigen, dass die beiden Plattformen miteinander kommunizieren**:

1. Melden Sie sich am Defender für loT Sensor an.

1. Navigieren Sie zu **Systemeinstellungen** > **Zugriffstoken**.

Das Feld "Benutzt" warnt Sie, wenn die Verbindung zwischen dem Sensor und der Forescout-Appliance nicht funktioniert. Wenn **–** angezeigt wird, funktioniert die Verbindung nicht. Wenn **Benutzt** angezeigt wird, wird das letzte Mal angezeigt, als ein externer Anruf mit diesem Token empfangen wurde.

:::image type="content" source="media/tutorial-forescout/forescout-access-token-added-successfully.png" alt-text="Überprüft, ob das Token empfangen wurde":::

## <a name="view-device-attributes-in-forescout"></a>Anzeigen von Geräteattributen in Forescout

Durch die Integration von Defender für loT mit Forescout: können Sie verschiedene Geräteattribute, die von Defender für loT erkannt wurden, in der Forescout-Anwendung anzeigen.

In der folgenden Tabelle sind alle Attribute aufgeführt, die in der Forescout-Anwendung sichtbar sind:

| Element | BESCHREIBUNG |
|--|--|
| **Autorisiert von Microsoft Defender für IoT** | Ein Gerät wurde während des Netzwerklernzeitraums in Ihrem Netzwerk von Defender für IoT erkannt. |
| **Firmware** | Die Firmwaredetails des Geräts. Beispielsweise Modell und Versionsdetails. |
| **Name** | Der Name des Geräts. |
| **Betriebssystem** | Das Betriebssystem des Geräts. |
| **Typ** | Der Typ des Geräts. Beispielsweise eine SPS-, Historian- oder Engineering-Station. |
| **Hersteller** | Der Hersteller des Geräts. Beispielsweise Rockwell Automation. |
| **Risikostufe** | Die von Defender für IoT berechnete Risikostufe. |
| **Protokolle** | Die Protokolle, die im vom Gerät generierten Datenverkehr erkannt wurden |

**Um die Attribute eines Geräts anzuzeigen**:

1. Melden Sie sich bei der Forescout-Plattform an, und navigieren Sie dann zum **Ressourceninventar**.

   :::image type="content" source="media/tutorial-forescout/device-firmware-attributes-in-forescout.png" alt-text="Anzeigen der Firmwareattribute.":::

1. Wählen Sie **CyberX Platform aus**.

   :::image type="content" source="media/tutorial-forescout/vendor-attributes-in-forescout.png" alt-text="Anzeigen der Herstellerattribute.":::

### <a name="view-more-details"></a>Anzeigen weiterer Details

Nachdem Sie die Attribute eines Geräts angezeigt haben, können Sie weitere Details zu jedem Gerät anzeigen, z. B. Forescout-Compliance und Richtlinieninformationen.

**Um zusätzliche Details anzuzeigen**:

1. Melden Sie sich bei der Forescout-Plattform an, und navigieren Sie dann zum **Ressourceninventar**.

1. Wählen Sie **CyberX Platform aus**.

1. Klicken Sie im Abschnitt Gerätebestandshosts mit der rechten Maustaste auf ein Gerät. Das Dialogfeld „Hostdetails“ wird mit zusätzlichen Informationen geöffnet.

## <a name="create-microsoft-defender-for-iot-policies-in-forescout"></a>Erstellen von Microsoft Defender für IoT-Richtlinien in Forescout

Forescout-Richtlinien können verwendet werden, um die Steuerung und Verwaltung von Geräten zu automatisieren, die von Defender für IoT erkannt werden. Beispiel:

- Automatische E-Mail an die SOC-Administratoren, wenn bestimmte Firmwareversionen erkannt werden.

- Hinzufügen bestimmter Defender für IoT-Geräte zu einer Forescout-Gruppe zur weiteren Behandlung von Incident- und Sicherheitsworkflows, z. B. mit anderen SIEM-Integrationen.

Sie können benutzerdefinierte Richtlinien in Forescout erstellen, indem Sie Defender für loT bedingte Eigenschaften verwenden.

**Um auf die Eigenschaften von Defender für loT zuzugreifen**:

1. Navigieren Sie zu **Richtlinienbedingungen** > **Eigenschaftenstruktur**.

1. Erweitern Sie den Ordner CyberX Platform in der Eigenschaftenstruktur. Die folgenden Defender für IoT-Eigenschaften sind verfügbar.

:::image type="content" source="media/tutorial-forescout/forescout-property-tree.png" alt-text="Eigenschaften":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Es müssen keine Ressourcen bereinigt werden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie mit der Forescout-Integration beginnen können. Fahren Sie fort, um mehr über unsere [Palo Alto-Integration](./tutorial-palo-alto.md).

> [!div class="nextstepaction"]
> [Schaltfläche „Nächste Schritte“](./tutorial-palo-alto.md)
