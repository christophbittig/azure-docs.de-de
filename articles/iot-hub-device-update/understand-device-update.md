---
title: Einführung in Device Update for IoT Hub | Microsoft-Dokumentation
description: Bei Device Update for IoT Hub handelt es sich um einen Dienst, mit dem Sie OTA-Updates (Over-The-Air) für Ihre IoT-Geräte bereitstellen können.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: overview
ms.service: iot-hub-device-update
ms.openlocfilehash: 0b763f9f6c83f6255a460fc4441f8cfdb74193f0
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399369"
---
# <a name="device-update-for-iot-hub-preview-overview"></a>Device Update for IoT Hub (Vorschau): Übersicht

Bei Device Update for IoT Hub handelt es sich um einen Dienst, mit dem Sie OTA-Updates (Over-The-Air) für Ihre IoT-Geräte bereitstellen können.

Da Unternehmen die Produktivität und betriebliche Effizienz weiter steigern möchten, werden IoT-Lösungen (Internet of Things, Internet der Dinge) immer häufiger eingesetzt. Daher ist es wichtig, dass die Geräte, aus denen diese Lösungen bestehen, auf einer zuverlässigen und sicheren Grundlage aufgebaut, einfach zu verbinden und maßstabsgerecht zu verwalten sind. Device Update for IoT Hub ist eine End-to-End-Plattform, mit der Kunden OTA-Updates (Over-the-Air) für alles, von winzigen Sensoren bis zu Geräten auf Gatewayebene, veröffentlichen, verteilen und verwalten können. 

Um die Vorteile der IoT-gestützten digitalen Transformation voll ausschöpfen zu können, benötigen Kunden diese Fähigkeit, Geräte in großem Umfang zu betreiben, zu verwalten und zu aktualisieren. Entdecken Sie die Vorteile der Implementierung von Device Update for IoT Hub, z. B. die Möglichkeit, schnell auf Sicherheitsbedrohungen zu reagieren und neue Funktionen zu implementieren, um die Unternehmensziele zu erreichen, ohne zusätzliche Entwicklungs- und Wartungskosten für den Aufbau eigener Updateplattformen zu verursachen.

## <a name="support-for-a-wide-range-of-iot-devices"></a>Unterstützung für eine große Bandbreite von IoT-Geräten


Device Update for IoT Hub wurde entwickelt, um durch die Integration in [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) optimierte Updatebereitstellung und rationalisierte Abläufe zu bieten. Durch diese Integration ist es einfach, Device Update für jede vorhandene Lösung zu übernehmen. Sie bietet eine in der Cloud gehostete Lösung zum Verbinden sämtlicher IoT-Geräte. Device Update unterstützt eine breite Palette von IoT-Betriebssystemen einschließlich Linux und [Azure RTOS](https://azure.microsoft.com/services/rtos/) (Echtzeitbetriebssystem) und ist über Open-Source erweiterbar. Gemeinsam mit unseren Partnern in der Halbleiterindustrie (u. a. STMicroelectronics, NXP, Renesas und Microchip) entwickeln wir Angebote für „Device Update für IoT Hub“. Sehen Sie sich die [Beispiele](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) für die wichtigsten Halbleiter-Evaluierungsboards an. Sie enthalten Leitfäden zu den ersten Schritten, in denen Sie erfahren, wie Sie die OTA-Updates (Over-The-Air-Updates) für Geräte der MPU-Klasse konfigurieren, erstellen und bereitstellen. 

Es werden sowohl ein Device Update-Agent-Simulator als auch Raspberry Pi-Referenz-Yocto-Images bereitgestellt.
Device Update for IoT Hub unterstützt auch das Aktualisieren von Azure IoT Edge-Geräten. Ein Device Update-Agent wird für die Plattform Ubuntu Server 18.04 amd64 bereitgestellt. Device Update for IoT Hub bietet auch Open-Source-Code, wenn Sie keine der oben genannten Plattformen ausführen. Sie können den Agent in die von Ihnen ausgeführte Distribution portieren.

Device Update arbeitet mit IoT Plug & Play und kann jedes Gerät verwalten, das die erforderlichen Plug & Play-Schnittstellen unterstützt. Weitere Informationen finden Sie unter [Device Update for IoT Hub und IoT Plug & Play](device-update-plug-and-play.md).

## <a name="support-for-a-wide-range-of-update-artifacts"></a>Unterstützung für eine breite Palette von Updateartefakten

Device Update for IoT Hub unterstützt zwei Arten von Updates: imagebasiert und paketbasiert.

Paketbasierte Updates sind gezielte Updates, die nur eine bestimmte Komponente oder Anwendung auf dem Gerät ändern. Dies führt zu einem geringeren Verbrauch an Bandbreite und verringert die Zeit zum Herunterladen und Installieren des Updates. Paketupdates ermöglichen in der Regel eine geringere Ausfallzeit der Geräte beim Anwenden eines Updates und vermeiden den Mehraufwand beim Erstellen von Images.

Imageupdates bieten ein höheres Maß an Vertrauen in den Endzustand des Geräts. Es ist in der Regel einfacher, die Ergebnisse eines Imageupdates zwischen einer Vorproduktions- und einer Produktionsumgebung zu replizieren, da dies nicht die gleichen Herausforderungen wie bei Paketen und deren Abhängigkeiten mit sich bringt.
Aufgrund ihrer atomischen Natur kann auch ein A/B-Failovermodell problemlos übernommen werden.

Es gibt nicht nur eine richtige Antwort, und Sie können je nach Anwendungsfall eine andere Wahl treffen. Device Update for IoT Hub unterstützt sowohl die Image- als auch die Paketform der Aktualisierung, sodass Sie das richtige Updatemodell für Ihre Geräteumgebung auswählen können.

## <a name="flexible-features-for-updating-devices"></a>Flexible Features zum Aktualisieren von Geräten

Die Funktionen von Device Update for IoT Hub bieten eine leistungsstarke und flexible Erfahrung, einschließlich:

* In Azure IoT Hub integrierte UX für Updateverwaltung
* Schrittweises Updaterollout durch Gerätegruppierung und Steuerung der Updateplanung
* Programmgesteuerte APIs zum Aktivieren von Automatisierung und benutzerdefinierten Portalumgebungen
* Kompatibilitäts-und Statusansichten auf einen Blick über heterogene Geräteflotten hinweg
* Unterstützung für robuste Geräteupdates (A/B) zur Bereitstellung eines nahtlosen Rollbacks
* Über das Azure.com-Portal verfügbare Abonnement- und rollenbasierte Zugriffssteuerung
* Lokaler Inhaltscache und Nested Edge-Unterstützung zum Aktivieren der Aktualisierung von Geräten, die von der Cloud getrennt sind
* Ausführliche Updateverwaltung und Berichterstellungstools 

Mit Device Update for IoT Hub-Verwaltungs- und -Bereitstellungssteuerungen können Anwender ihre Produktivität maximieren und wertvolle Zeit sparen. Device Update for IoT Hub bietet die Möglichkeit, Geräte zu gruppieren und festzulegen, für welche Geräte ein Update bereitgestellt werden soll. Benutzer können auch den Status von Updatebereitstellungen anzeigen und sicherstellen, dass jedes Gerät Updates erfolgreich anwendet.

Wenn ein Updatefehler auftritt, ermöglicht Device Update for IoT Hub Benutzern außerdem, die Geräte zu identifizieren, die das Update nicht anwenden konnten, und die zugehörigen Fehlerdetails anzuzeigen. Die Möglichkeit, zu identifizieren, bei welchen Geräten das Update fehlgeschlagen ist, spart unzählige manuelle Stunden bei der Suche nach der Ursache.

### <a name="best-in-class-security-at-global-scale"></a>Erstklassige Sicherheit auf globaler Ebene

Microsoft Azure unterstützt mehr als eine Milliarde IoT-Geräte weltweit – eine Zahl, die täglich wächst. Device Update for IoT Hub baut auf dieser Erfahrung und der bewährten Zuverlässigkeit der Windows Update-Plattform auf, sodass Geräte nahtlos auf globaler Ebene aktualisiert werden können.

Device Update for IoT Hub verwendet umfassende Cloud-to-Edge-Sicherheit, die für Microsoft Azure entwickelt wurde, sodass Kunden keine Zeit damit verbringen müssen, herauszufinden, wie sie diese von Grund auf selbst integrieren können.


## <a name="device-update-workflows"></a>Device Update-Workflows

Die Funktionalität von Device Update kann in drei Bereiche unterteilt werden: Agent-Integration, Importieren und Verwaltung.

### <a name="device-update-agent"></a>Device Update-Agent

Wenn auf einem Gerät ein Updatebefehl empfangen wird, wird die angeforderte Aktualisierungsphase (Download, Installation und Anwendung) ausgeführt. Während jeder Phase wird der Status über IoT Hub an Device Update zurückgegeben, sodass Sie den aktuellen Status einer Bereitstellung anzeigen können. Wenn aktuell keine Updates ausgeführt werden, wird der Status als „Im Leerlauf“ zurückgegeben. Eine Bereitstellung kann jederzeit abgebrochen werden.

:::image type="content" source="media/understand-device-update/client-agent-workflow.png" alt-text="Abbildung des Device Update-Agent-Workflows." lightbox="media/understand-device-update/client-agent-workflow.png":::

[Erfahren Sie mehr](device-update-agent-overview.md) über den Device Update-Agent. 

### <a name="importing"></a>Importieren

Ihre Updates werden mittels Importieren in Device Update erfasst, um auf allen Geräten bereitgestellt werden zu können. Device Update unterstützt das Rollout eines einzelnen Updates pro Gerät. Dadurch eignet sich Device Update ideal für Full-Image-Updates, die eine ganze Betriebssystempartition gleichzeitig aktualisieren, oder ein [APT-Manifest](device-update-apt-manifest.md), das alle Pakete aus einem bestimmten Repository beschreibt, die Sie auf Ihrem Gerät aktualisieren möchten. Um Updates in Device Update zu importieren, erstellen Sie zunächst ein Importmanifest, das das Update beschreibt, und laden dann die Updatedatei(en) und das Importmanifest an einen Speicherort hoch, auf den Sie über das Internet zugreifen können. Danach können Sie das Azure-Portal oder die [Device Update-REST-API](/rest/api/deviceupdate/) verwenden, um den asynchronen Prozess des Updateimports zu initiieren. Device Update lädt die Dateien hoch, verarbeitet sie und stellt sie zur Verteilung an IoT-Geräte bereit.

Bei vertraulichen Inhalten schützen Sie den Download mit einer Shared Access Signature (SAS), z. B. einer Ad-hoc-SAS für Azure Blob Storage. [Weitere Informationen zu SAS](../storage/common/storage-sas-overview.md)

:::image type="content" source="media/understand-device-update/import-update.png" alt-text="Abbildung des Device Update for IoT Hub-Importworkflows." lightbox="media/understand-device-update/import-update.png":::

[Weitere Informationen](import-concepts.md) zum Importieren von Updates. 

### <a name="grouping-and-deployment"></a>Gruppierung und Bereitstellung

Nachdem Sie ein Update importiert haben, können Sie kompatible Updates für Ihre Geräte und Geräteklassen anzeigen.

Device Update unterstützt das Konzept von **Gruppen** über Tags in IoT Hub. Die Bereitstellung eines Updates zunächst für eine Testgruppe ist eine gute Möglichkeit, das Risiko von Problemen während eines Produktionsrollouts zu verringern.

In Device Update sind Bereitstellungen eine Möglichkeit, den richtigen Inhalt mit einem bestimmten Satz kompatibler Geräte zu verbinden. Device Update orchestriert den Prozess des Sendens von Befehlen an jedes Gerät, weist sie an, die Updates herunterzuladen und zu installieren und ruft den Status ab.

:::image type="content" source="media/understand-device-update/manage-deploy-updates.png" alt-text="Abbildung der Device Update for IoT Hub-Gruppierung und des Bereitstellungsworkflows." lightbox="media/understand-device-update/manage-deploy-updates.png":::

[Weitere Informationen](device-update-compliance.md) zu Bereitstellungskonzepten

[Weitere Informationen](device-update-groups.md) zu Geräteupdategruppen


## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen eines Geräteupdatekontos und einer -instanz](create-device-update-account.md)
