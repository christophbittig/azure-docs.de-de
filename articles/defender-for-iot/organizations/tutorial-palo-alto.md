---
title: Integrieren von Palo Alto in Microsoft Defender für IoT
description: Die Defender für IoT-Plattform für die kontinuierliche ICS-Bedrohungsüberwachung wurde in die Palo Alto-Firewalls der nächsten Generation integriert, um eine schnellere und effizientere Blockierung von kritischen Bedrohungen zu ermöglichen.
ms.date: 11/09/2021
ms.topic: tutorial
ms.openlocfilehash: 5beec75d23fb940f35ded84f2a938dae7e3dd9bc
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132301455"
---
# <a name="tutorial-integrate-palo-alto-with-microsoft-defender-for-iot"></a>Tutorial: Integrieren von Palo Alto in Microsoft Defender für IoT

In diesem Tutorial lernen Sie, wie Sie Palo Alto mit Microsoft Defender für IoT integrieren und nutzen.

Die Defender für IoT-Plattform für die kontinuierliche ICS-Bedrohungsüberwachung wurde in die Palo Alto-Firewalls der nächsten Generation integriert, um eine schnellere und effizientere Blockierung von kritischen Bedrohungen zu ermöglichen.

Die folgenden Integrationstypen sind verfügbar:

- Option für die automatische Blockierung: Direkte Integration von Defender für IoT in Palo Alto

- Senden von Empfehlungen zur Blockierung an das zentrale Verwaltungssystem: Integration von Defender für IoT in Panorama

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> - Konfigurieren der sofortigen Blockierung durch eine angegebene Palo Alto-Firewall
> - Erstellen von Panorama-Blockierungsrichtlinien in Defender für IoT

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="prerequisites"></a>Voraussetzungen

### <a name="panorama-permissions"></a>Panorama-Berechtigungen

- Bestätigung durch den Panorama-Administrator für die Zulassung der automatischen Blockierung.

## <a name="configure-immediate-blocking-by-a-specified-palo-alto-firewall"></a>Konfigurieren der sofortigen Blockierung durch eine angegebene Palo Alto-Firewall

In bestimmten Fällen, z. B. Warnungen zu Schadsoftware, können Sie die automatische Blockierung aktivieren. Die Weiterleitungsregeln von Defender für IoT werden verwendet, um einen Blockierungsbefehl direkt an eine bestimmte Palo Alto-Firewall zu senden.

Wenn Defender für IoT eine kritische Bedrohung identifiziert, wird eine Warnung gesendet, die eine Option zum Blockieren der infizierten Quelle enthält. Durch Auswählen von **Quelle blockieren** in den Warnungsdetails wird die Weiterleitungsregel aktiviert, die den Blockierungsbefehl an die angegebene Palo Alto-Firewall sendet.

**Konfigurieren Sie die sofortige Blockierung wie folgt:**

1. Wählen Sie im linken Bereich die Option **Weiterleitung** aus.

1. Wählen Sie **Weiterleitungsregel erstellen** aus.

    :::image type="content" source="media/tutorial-palo-alto/forwarding.png" alt-text="Screenshot: Fenster mit Weiterleitungswarnung":::

1. Wählen Sie im Dropdownmenü „Aktionen“ die Option **An Palo Alto NGFW senden** aus.

   :::image type="content" source="media/tutorial-palo-alto/forward-rule.png" alt-text="Screenshot: Fenster „Weiterleitungsregel erstellen“":::

1. Legen Sie im Bereich „Aktionen“ die folgenden Parameter fest:

   - **Host**: Geben Sie die IP-Adresse des NGFW-Servers ein.
   - **Port:** Geben Sie den Port des NGFW-Servers ein.
   - **Benutzername**: Geben Sie den Benutzernamen des NGFW-Servers ein.
   - **Kennwort**: Geben Sie das Kennwort des NGFW-Servers ein.
   - **Konfigurieren**: Richten Sie die folgenden Optionen ein, um die Blockierung der verdächtigen Quellen durch die Palo Alto-Firewall zuzulassen:
     - **Unzulässige Funktionscodes blockieren**: Protokollverstöße: ungültiger Feldwert, der gegen die ICS-Protokollspezifikation verstößt (potenzieller Exploit).
     - **Nicht autorisierte SPS-Programmierung/Firmwareupdates blockieren**: Nicht autorisierte SPS-Änderungen.
     - **Nicht autorisierte SPS-Beendigung blockieren**: SPS-Beendigung (Downtime).
     - **Auf Schadsoftware bezogene Warnungen blockieren**: Blockierung von Angriffsversuchen durch industrielle Schadsoftware (TRITON, NotPetya usw.). Sie können die Option **Automatische Blockierung** auswählen. In diesem Fall wird die Blockierung automatisch und sofort ausgeführt.
     - **Nicht autorisierte Überprüfung blockieren**: Nicht autorisierte Überprüfung (potenzielle Aufklärung).

    :::image type="content" source="media/tutorial-palo-alto/edit.png" alt-text="Screenshot: Fenster „Weiterleitungsregel bearbeiten“":::

1. Klicken Sie auf **Submit** (Senden).

Anschließend müssen Sie alle verdächtigen Quellen blockieren.

**Blockieren Sie eine verdächtige Quelle wie folgt**:

1. Navigieren Sie zum Bereich **Warnungen**, und wählen Sie die Warnung zur Palo Alto-Integration aus.

1. Zum automatischen Blockieren der verdächtigen Quelle wählen Sie **Quelle blockieren** aus. Das Dialogfeld **Bestätigen** wird angezeigt.

    :::image type="content" source="media/tutorial-palo-alto/unauthorized.png" alt-text="Screenshot: Schaltfläche „Quelle blockieren“, um die nicht autorisierte Quelle zu blockieren":::

1. Klicken Sie auf **OK**.

Die verdächtige Quelle wird jetzt durch die Palo Alto-Firewall blockiert.

## <a name="create-panorama-blocking-policies-in-defender-for-iot"></a>Erstellen von Panorama-Blockierungsrichtlinien in Defender für IoT

Die Integration von Defender für IoT mit dem Palo Alto-Netzwerk bewirkt, dass im NMS des Palo Alto-Netzwerks und in Panorama automatisch neue Richtlinien erstellt werden.

In dieser Tabelle ist angegeben, für welche Incidents diese Integration bestimmt ist:

| Incidenttyp | BESCHREIBUNG |
|--|--|
|**Nicht autorisierte SPS-Änderungen** | Ein Update der Leiterlogik oder Firmware eines Geräts. Dieser Incident kann für eine legitime Aktivität oder eine versuchte Kompromittierung des Geräts stehen. Beispiele hierfür sind Schadcode, z. B. ein Remotezugriffstrojaner (Remote Access Trojan, RAT), oder Parameter, die dazu führen, dass ein physischer Prozess, z. B. eine sich drehende Turbine, auf unsichere Weise ausgeführt wird. |
|**Protokollverstoß** | Eine Paketstruktur oder ein Feldwert, die bzw. der gegen die Protokollspezifikation verstößt. Dieser Incident kann für eine falsch konfigurierte Anwendung oder eine böswillige versuchte Kompromittierung eines Geräts stehen. Ein Beispiel hierfür ist das Verursachen einer Pufferüberlaufbedingung auf dem Zielgerät. |
|**SPS-Beendigung** | Ein Befehl, der bewirkt, dass ein Gerät nicht mehr funktioniert. Dies stellt ein Risiko für den physischen Prozess dar, der von der SPS gesteuert wird. |
|**Industrielle Schadsoftware im ICS-Netzwerk gefunden** | Schadsoftware, die ICS-Geräte über ihre nativen Protokolle manipuliert, beispielsweise TRITON und Industroyer. Defender für IoT erkennt auch IT-Schadsoftware, die per Seitwärtsbewegung in die ICS- und SCADA-Umgebung eingeschleust wurde. Beispiele hierfür sind Conficker, WannaCry und NotPetya. |
|**Schadsoftware für Überprüfung** | Aufklärungstools, die in einer Phase vor einem Angriff Daten zur Systemkonfiguration sammeln. Beispielsweise durchsucht der Havex-Trojaner Industrienetzwerke nach Geräten, die OPC verwenden. OPC ist ein Standardprotokoll, das von Windows-basierten SCADA-Systemen für die Kommunikation mit ICS-Geräten verwendet wird. |

Wenn Defender für IoT einen vorkonfigurierten Anwendungsfall erkennt, wird der Warnung die Schaltfläche **Quelle blockieren** hinzugefügt. Wenn dann der CyberX-Benutzer die Schaltfläche **Quelle blockieren** auswählt, werden von Defender für IoT Richtlinien in Panorama erstellt, indem die vordefinierte Weiterleitungsregel gesendet wird.

Die Richtlinie wird nur angewendet, wenn der Panorama-Administrator sie per Push an die entsprechende NGFW im Netzwerk sendet.

In IT-Netzwerken können dynamische IP-Adressen vorhanden sein. Daher muss die Richtlinie für diese Subnetze auf dem vollqualifizierten Domänennamen (DNS-Namen) basieren, nicht auf der IP-Adresse. Defender für IoT führt ein Reverse-Lookup durch und gleicht Geräte mit dynamischer IP-Adresse im konfigurierten Stundenintervall mit dem zugehörigen vollqualifizierten Domänennamen (DNS-Namen) ab.

Darüber hinaus sendet Defender für IoT eine E-Mail an den entsprechenden Panorama-Benutzer, dass eine neue von Defender für IoT erstellte Richtlinie auf Genehmigung wartet. Die folgende Abbildung zeigt die Architektur der Integration von Defender für IoT und Panorama.

:::image type="content" source="media/tutorial-palo-alto/structure.png" alt-text="Screenshot: Architektur für CyberX-Panorama-Integration":::

Der erste Schritt beim Erstellen von Panorama-Blockierungsrichtlinien in Defender für IoT ist die Konfiguration der DNS-Suche.

**Konfigurieren Sie die DNS-Suche wie folgt**:

1. Wählen Sie im linken Bereich **Systemeinstellungen** aus.

1. Wählen Sie die Schaltfläche **DNS-Einstellungen** :::image type="icon" source="media/tutorial-palo-alto/settings.png"::: aus.

1. Legen Sie Dialogfeld **DNS-Einstellungen bearbeiten** die folgenden Parameter fest:

   - **Status:** Der Status des DNS-Resolvers.

   - **DNS-Serveradresse**: Geben Sie die IP-Adresse oder den vollqualifizierten Domänenname des DNS-Netzwerkservers ein.
   - **DNS-Serverport**: Geben Sie den zur Abfrage des DNS-Servers verwendeten Port ein.
   - **Subnetze**: Legen Sie den Subnetzbereich für dynamische IP-Adressen fest. Dies ist der Bereich, den Defender für IoT zum Reverse-Lookup von IP-Adressen im DNS-Server verwendet, um aktuelle vollqualifizierte Domänennamen abzugleichen.
   - **Reverse-Lookup planen**: Definieren Sie die Planungsoptionen wie folgt:
     - Nach bestimmter Uhrzeit: Geben Sie an, wann das Reverse-Lookup jeden Tag erfolgen soll.
     - Nach festen Intervallen (in Stunden): Legen Sie fest, wie häufig das Reverse-Lookup ausgeführt werden soll.
   - **Anzahl von Bezeichnungen**: Weisen Sie Defender für IoT an, IP-Netzwerkadressen automatisch in die vollqualifizierten Domänennamen der Geräte aufzulösen. <br />Um die DNS-Auflösung zu konfigurieren, fügen Sie die Anzahl der anzuzeigenden Domänenbezeichnungen hinzu. Es werden bis zu 30 Bezeichnungen von links nach rechts angezeigt.

    :::image type="content" source="media/tutorial-palo-alto/configuration.png" alt-text="Screenshot: Fenster zum Konfigurieren der DNS-Einstellungen":::

1. Wählen Sie **SAVE** (SPEICHERN) aus.

Um sicherzustellen, dass die DNS-Einstellungen korrekt sind, wählen Sie **Lookuptest** aus. Mit dem Test wird sichergestellt, dass die IP-Adresse und der Port des DNS-Servers richtig festgelegt sind.

## <a name="block-suspicious-traffic-with-the-palo-alto-firewall"></a>Blockieren von verdächtigem Datenverkehr mit der Palo Alto-Firewall

Verdächtiger Datenverkehr muss mit der Palo Alto-Firewall blockiert werden. Sie können verdächtigen Datenverkehr blockieren, indem Sie Weiterleitungsregeln in Defender für IoT verwenden.

**Gehen Sie wie folgt vor, um verdächtigen Datenverkehr mit der Palo Alto-Firewall über eine Defender für IoT-Weiterleitungsregel zu blockieren**:

1. Wählen Sie im linken Bereich die Option **Weiterleitung** aus.

1. Wählen Sie **Weiterleitungsregel erstellen** aus.

1. Wählen Sie im Dropdownmenü **Aktionen** die Option **An Palo Alto Panorama senden** aus.

1. Legen Sie im Bereich „Aktionen“ die folgenden Parameter fest:

   - **Host**: Geben Sie die IP-Adresse des Panorama-Servers ein.

   - **Port:** Geben Sie den Port des Panorama-Servers ein.

   - **Benutzername**: Geben Sie den Benutzernamen des Panorama-Servers ein.

   - **Kennwort**: Geben Sie das Kennwort des Panorama-Servers ein.

   - **Berichtsadresse**: Definieren Sie wie folgt, wie die Blockierung ausgeführt wird:

     - **Nach IP-Adresse**: Blockierungsrichtlinien werden in Panorama immer basierend auf der IP-Adresse erstellt.

     - **Nach FQDN oder IP-Adresse**: Blockierungsrichtlinien werden in Panorama basierend auf dem vollqualifizierten Domänennamen erstellt, falls vorhanden, andernfalls basierend auf der IP-Adresse.

   - **E-Mail**: Legen Sie die E-Mail-Adresse für die E-Mail-Benachrichtigung zur Richtlinie fest.

    > [!NOTE]
    > Stellen Sie sicher, dass Sie einen Mailserver in Defender für IoT konfiguriert haben. Wenn keine E-Mail-Adresse angegeben wurde, sendet Defender für IoT keine Benachrichtigungs-E-Mail.

   - **DNS-Lookup bei Warnungserkennung ausführen (Kontrollkästchen)** : Wenn unter „Berichtsadresse“ die Option „Nach FQDN oder IP-Adresse“ festgelegt ist, ist dieses Kontrollkästchen standardmäßig aktiviert. Wenn nur die IP-Adresse festgelegt ist, ist diese Option deaktiviert.

   - **Konfigurieren**: Richten Sie die folgenden Optionen ein, um die Blockierung verdächtiger Quellen durch Palo Alto Panorama zuzulassen:

     - **Unzulässige Funktionscodes blockieren**: Protokollverstöße: ungültiger Feldwert, der gegen die ICS-Protokollspezifikation verstößt (potenzieller Exploit).

     - **Nicht autorisierte SPS-Programmierung/Firmwareupdates blockieren**: Nicht autorisierte SPS-Änderungen.

     - **Nicht autorisierte SPS-Beendigung blockieren**: SPS-Beendigung (Downtime).

     - **Auf Schadsoftware bezogene Warnungen blockieren**: Blockierung von Angriffsversuchen durch industrielle Schadsoftware (TRITON, NotPetya usw.). Sie können die Option **Automatische Blockierung** auswählen. In diesem Fall wird die Blockierung automatisch und sofort ausgeführt.

     - **Nicht autorisierte Überprüfung blockieren**: Nicht autorisierte Überprüfung (potenzielle Aufklärung).

    :::image type="content" source="media/tutorial-palo-alto/details.png" alt-text="Screenshot: Fenster „Aktion auswählen“":::

1. Klicken Sie auf **Submit** (Senden).

Anschließend müssen Sie die verdächtige Quelle blockieren.

**Blockieren Sie die verdächtige Quelle wie folgt:**

1. Wählen Sie im Bereich **Warnungen** die auf die Palo Alto-Integration bezogene Warnung aus. Das Dialogfeld **Warnungsdetails** wird angezeigt.

  :::image type="content" source="media/tutorial-palo-alto/unauthorized.png" alt-text="Screenshot: Fenster „Warnungen“ (Warnung zu Palo Alto und dann „Quelle blockieren“ auswählen)":::

1. Zum automatischen Blockieren der verdächtigen Quelle wählen Sie **Block Source** (Quelle blockieren) aus.

1. Wählen Sie **OK**.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Es müssen keine Ressourcen bereinigt werden.

## <a name="next-step"></a>Nächster Schritt

In diesem Tutorial haben Sie gelernt, wie Sie mit der Palo Alto-Integration beginnen können.

> [!div class="nextstepaction"]
> [Schaltfläche „Nächste Schritte“](tutorial-splunk.md)
