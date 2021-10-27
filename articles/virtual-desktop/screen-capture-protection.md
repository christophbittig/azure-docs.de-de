---
title: Schutz der Bildschirmaufnahme in Azure Virtual Desktop
titleSuffix: Azure
description: So richten Sie den Schutz Ihrer Bildschirmaufnahme in Azure Virtual Desktop ein.
author: gundarev
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: denisgun
ms.service: virtual-desktop
ms.openlocfilehash: f9571f89bdb3acab7f042c63f2c8bba05044a758
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/14/2021
ms.locfileid: "129992247"
---
# <a name="screen-capture-protection"></a>Schutz von Bildschirmaufnahmen

Mit der Funktion für den Bildschirmaufnahmeschutz wird verhindert, dass vertrauliche Informationen auf Clientendpunkten aufgezeichnet werden. Wenn Sie diese Funktion aktivieren, werden Remoteinhalte in Screenshots und Bildschirmfreigaben automatisch blockiert oder ausgeblendet. Außerdem schützt der Remotedesktop-Client Inhalte vor Schadsoftware, die möglicherweise am Bildschirm angezeigt werden.

## <a name="prerequisites"></a>Voraussetzungen

Die Bildschirmaufnahme-Schutzfunktion wird auf Sitzungshostebene konfiguriert und für den Client erzwungen. Nur Clients, die dieses Feature unterstützen, können eine Verbindung mit der Remotesitzung herstellen.
Folgende Clients unterstützen derzeit den Schutz von Bildschirmaufnahmen:

* Der Windows-Desktop-Client unterstützt den Bildschirmaufzeichnungsschutz nur für vollständige Desktops.
* macOS-Client Version 10.7.0 oder älter unterstützt den Bildschirmaufzeichnungsschutz sowohl für RemoteApp als auch für vollständige Desktops.

Angenommen, der Benutzer versucht, einen nicht unterstützten Client zu verwenden, um eine Verbindung zum geschützten Sitzungshost herzustellen. In diesem Fall tritt bei der Verbindung der Fehler 0x1151 auf.

## <a name="configure-screen-capture-protection"></a>Konfigurieren des Schutzes von Bildschirmaufnahmen

1. Zum Konfigurieren des Bildschirmaufnahmeschutzes müssen Sie administrative Vorlagen installieren, durch die Regeln und Einstellungen für Azure Virtual Desktop hinzugefügt werden.
2. Laden Sie die [Azure Virtual Desktop-Richtlinienvorlagendatei](https://aka.ms/avdgpo) (AVDGPTemplate.cab) herunter und extrahieren Sie den Inhalt der CAB-Datei und des ZIP-Archivs.
3. Kopieren Sie die Datei *terminalserver-avd.admx* in den Ordner *%windir%\PolicyDefinitions*
4. Kopieren Sie die Datei *en-us\terminalserver-avd.adml* in den Ordner *%windir%\PolicyDefinitions\en-us*
5. Öffnen Sie für eine Überprüfung der aktuell kopierten Dateien den lokalen Gruppenrichtlinien-Editor, und navigieren Sie zu **Computerrichtlinien** -> **Administrative Vorlagen** -> **Windowskomponenten** -> **Remotedesktopdienste** -> **Remotedesktop-Sitzungshost** -> **Azure Virtual Desktop**
6. Ihnen sollten eine oder mehrere Azure Virtual Desktop-Richtlinien angezeigt werden, wie unten dargestellt.

   :::image type="content" source="media/azure-virtual-desktop-gpo.png" alt-text="Screenshot des Gruppenrichtlinien-Editors" lightbox="media/azure-virtual-desktop-gpo.png":::

   > [!TIP]
   > Sie können auch administrative Vorlagen für die Gruppenrichtlinie „Zentraler Speicher in Ihrer Active Directory-Domäne“ installieren.
   > Weitere Informationen über den zentralen Speicher für administrative Vorlagedateien für Gruppenrichtlinien finden Sie unter [Erstellen und Verwalten des zentralen Speichers für administrative Vorlagendateien für Gruppenrichtlinien in Windows](/troubleshoot/windows-client/group-policy/create-and-manage-central-store).

7. Öffnen Sie die Richtlinie **„Aktivieren des Schutzes von Bildschirmaufnahmen“** und setzen Sie diese auf **„Aktiviert“** .

## <a name="limitations-and-known-issues"></a>Einschränkungen und bekannte Probleme

* Diese Funktion schützt das Remotedesktop-Fenster davor, durch eine bestimmte Gruppe von öffentlichen Betriebssystemfunktionen und APIs erfasst zu werden. Es gibt jedoch keine Garantie dafür, dass durch diese Funktion Inhalte auch dann vollständig geschützt werden, wenn beispielsweise jemand den Bildschirm fotografiert.
* Kunden sollten die Funktion zusammen mit der Deaktivierung der Zwischenablage, des Laufwerks und der Druckerumleitung verwenden. Das Deaktivieren der Umleitung verhindert, dass der Benutzer den erfassten Bildschirminhalt aus der Remotesitzung kopiert.
* Benutzer können das Remotedesktop-Fenster nicht mithilfe lokaler Zusammenarbeitssoftware wie Microsoft Teams freigeben, wenn die Funktion aktiviert ist. Wenn Microsoft Teams verwendet wird, können sowohl die lokale Teams-App als auch Teams, die mit Medienoptimierungen ausgeführt werden, den geschützten Inhalt nicht freigeben.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu bewährten Methoden für die Sicherheit von Azure Virtual Desktop finden Sie unter [Bewährte Methoden für die Azure Virtual Desktop-Sicherheit](security-guide.md).
