---
title: Übersicht über die Geräteverwaltung mit Microsoft Azure IoT Hub
description: Übersicht über die Geräteverwaltung in Azure IoT Hub – Lebenszyklus von Unternehmensgeräten und Geräteverwaltungsmuster wie Neustart, Zurücksetzung auf Werkseinstellungen, Firmwareupdate, Konfiguration, Gerätezwillinge, Abfragen, Aufträge, Bedrohungserkennung.
author: anastasia-ms
ms.service: iot-hub
services: iot-hub
ms.author: v-stharr
ms.topic: conceptual
ms.date: 09/13/2021
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: 7e21115bd1f55d79b1ba09b91cea05576a74dd75
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312532"
---
# <a name="overview-of-device-management-with-iot-hub"></a>Übersicht über die Geräteverwaltung mit IoT Hub

Auf der Grundlage der Features und des Erweiterungsmodells von Azure IoT Hub können Geräte- und Back-End-Entwickler stabile Geräteverwaltungslösungen entwickeln. Das Spektrum der Geräte reicht von einfachen Sensoren und zweckgebundenen Microcontrollern bis hin zu leistungsfähigen Gateways zur Weiterleitung der Kommunikation für Gerätegruppen.  Die Anwendungsfälle und Anforderungen für IoT-Betreiber sind außerdem sehr branchenspezifisch.  Trotz dieser Vielfalt bietet die Geräteverwaltung mit Azure IoT Hub Funktionen, Muster und Codebibliotheken für unterschiedlichste Geräte und Endbenutzer.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Ein entscheidender Punkt bei der Erstellung einer erfolgreichen IoT-Unternehmenslösung ist die Entwicklung einer Strategie, mit der Bediener die kontinuierliche Verwaltung ihrer Geräte bewältigen können. IoT-Bediener benötigen unkomplizierte, zuverlässige Tools und Anwendungen, die es ihnen ermöglichen, sich ganz auf die strategischen Aspekte ihrer Arbeit zu konzentrieren. Dieser Artikel enthält:

* Eine kurze Übersicht über die Geräteverwaltung von Azure IoT Hub
* Eine Beschreibung allgemeiner Geräteverwaltungsprinzipien
* Eine Beschreibung des Gerätelebenszyklus
* Eine Übersicht über allgemeine Geräteverwaltungsmuster

## <a name="device-lifecycle"></a>Gerätelebenszyklus

Allgemeine Geräteverwaltungsphasen sind in den meisten IoT-Projekten von Unternehmen üblich. In Azure IoT umfasst der Gerätelebenszyklus fünf Phasen:

![Die fünf Gerätelebenszyklus-Phasen von Azure IoT: Planung, Bereitstellung, Konfiguration, Überwachung und Ausmusterung](./media/iot-hub-device-management-overview/image5.png)

In jeder dieser fünf Phasen müssen mehrere Anforderungen von Gerätebedienern erfüllt werden, um eine umfassende Lösung zu erhalten:

* **Planung:** Bediener müssen in die Lage versetzt werden, ein Schema der Gerätemetadaten zu erstellen. Damit können sie eine Gruppe von Geräten einfach und präzise abfragen und Massenvorgänge zu Verwaltungszwecken durchführen. Mit dem Gerätezwilling können Sie die Gerätemetadaten in Form von Tags und Eigenschaften speichern.
  
    *Weitere nützliche Informationen*: 
  * [Erste Schritte mit Gerätezwillingen](iot-hub-node-node-twin-getstarted.md)
  * [Grundlegendes zu Gerätezwillingen](iot-hub-devguide-device-twins.md)
  * [Verwenden der Eigenschaften von Gerätezwillingen](tutorial-device-twins.md)
  * [Bewährte Methoden für die Gerätekonfiguration innerhalb einer IoT-Lösung](iot-hub-configuration-best-practices.md)

* **Bereitstellung:** Neue Geräte können sicher für IoT Hub bereitgestellt werden, und Bediener können die Funktionen eines Geräts sofort ermitteln.  Verwenden Sie die IoT Hub-Identitätsregistrierung zum Erstellen von flexiblen Geräteidentitäten und Anmeldeinformationen, und führen Sie diesen Vorgang mithilfe eines Auftrags als Massenvorgang durch. Erstellen Sie Geräte, um ihre Funktionen und Bedingungen über Geräteeigenschaften im Gerätezwilling zu melden.
  
    *Weitere nützliche Informationen*: 
    * [Verwalten von Geräteidentitäten](iot-hub-devguide-identity-registry.md)
    * [Massenverwaltung von Geräte-Identitäten](iot-hub-bulk-identity-mgmt.md)
    * [Verwenden der Eigenschaften von Gerätezwillingen](tutorial-device-twins.md)
    * [Bewährte Methoden für die Gerätekonfiguration innerhalb einer IoT-Lösung](iot-hub-configuration-best-practices.md)
    * [Azure IoT Hub Device Provisioning-Dienst](../iot-dps/index.yml)

* **Konfiguration:** Massenvorgänge zum Ändern der Konfiguration und Firmwareupdates für Geräte können ohne Beeinträchtigung der Integrität und der Sicherheit durchgeführt werden. Verwenden Sie die gewünschten Eigenschaften, um diese Geräteverwaltungsvorgänge als Massenvorgang durchzuführen, oder verwenden Sie direkte Methoden und Broadcastaufträge.
  
    *Weitere nützliche Informationen*:
    * [Verwenden der Eigenschaften von Gerätezwillingen](tutorial-device-twins.md)
    * [Bedarfsgerechtes Konfigurieren und Überwachen von IoT-Geräten](./iot-hub-automatic-device-management.md)
    * [Bewährte Methoden für die Gerätekonfiguration innerhalb einer IoT-Lösung](iot-hub-configuration-best-practices.md)

* **Überwachung:** Überwachen Sie die allgemeine Geräteintegrität und den Status laufender Vorgänge, und weisen Sie Bediener auf Probleme hin, die ggf. ihre Aufmerksamkeit erfordern.  Wenden Sie den Gerätezwilling an, damit Geräte Bedingungen und den Status von Updatevorgängen in Echtzeit melden können. Erstellen Sie leistungsfähige Dashboardberichte, die mithilfe von Abfragen für den Gerätezwilling über neueste Probleme informieren. Schützen Sie Ihre IoT-Umgebung vor Bedrohungen, mithilfe von mehreren Bereitstellungsoptionen, darunter „vollständig lokal“, „mit der Cloud verbunden“ oder „hybrid“.
  
    *Weitere nützliche Informationen*: 
    * [Verwenden der Eigenschaften von Gerätezwillingen](tutorial-device-twins.md)
    * [IoT Hub-Abfragesprache für Gerätezwillinge, Aufträge und Nachrichtenrouting](iot-hub-devguide-query-language.md)
    * [Bedarfsgerechtes Konfigurieren und Überwachen von IoT-Geräten](./iot-hub-automatic-device-management.md)
    * [Microsoft Defender für IoT für Organisationen zum Bereitstellen einer umfassenden Bedrohungserkennung](../defender-for-iot/organizations/overview.md)
    * [Bewährte Methoden für die Gerätekonfiguration innerhalb einer IoT-Lösung](iot-hub-configuration-best-practices.md)

* **Ausmusterung**: Geräte werden nach einem Fehler oder Upgradezyklus oder am Ende der Lebensdauer ausgetauscht oder außer Betrieb gesetzt.  Verwenden Sie zur Verwaltung von Geräteinformationen den Gerätezwilling, falls das physische Gerät ausgetauscht wird (oder das Archiv, falls das Gerät ausgemustert wird). Verwenden Sie die IoT Hub-Identitätsregistrierung zum sicheren Sperren von Geräteidentitäten und Anmeldeinformationen.
  
    *Weitere nützliche Informationen*: 
    * [Verwenden der Eigenschaften von Gerätezwillingen](tutorial-device-twins.md)
    * [Verwalten von Geräteidentitäten](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>Geräteverwaltungsmuster

IoT Hub ermöglicht die folgenden Geräteverwaltungsmuster. In den [Tutorials zur Geräteverwaltung](iot-hub-node-node-device-management-get-started.md) erfahren Sie ausführlicher, wie Sie diese Muster auf Ihr individuelles Szenario abstimmen und auf der Grundlage der grundlegenden Vorlagen neue Muster entwerfen.

* **Neustart**: Die Back-End-App informiert das Gerät mithilfe einer direkten Methode darüber, dass sie einen Neustart eingeleitet hat.  Das Gerät nutzt die gemeldeten Eigenschaften zum Aktualisieren des Neustartstatus des Geräts.
  
    ![Neustartmuster der Geräteverwaltung – Grafik](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **Zurücksetzung auf Werkseinstellungen**: Die Back-End-App informiert das Gerät mithilfe einer direkten Methode darüber, dass sie eine Zurücksetzung auf die Werkseinstellungen gestartet hat. Das Gerät nutzt die vom Gerätezwilling gemeldeten Eigenschaften zum Aktualisieren des Status der Zurücksetzung auf Werkseinstellung des Geräts.
  
    ![Muster für die Zurücksetzung auf Werkseinstellungen der Geräteverwaltung – Grafik](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **Konfiguration**: Die Back-End-App verwendet die gewünschten Eigenschaften zum Konfigurieren der auf dem Gerät ausgeführten Software. Das Gerät nutzt die gemeldeten Eigenschaften zum Aktualisieren des Konfigurationsstatus des Geräts.
  
    ![Konfigurationsmuster der Geräteverwaltung – Grafik](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **Meldung von Fortschritt und Status**: Das Lösungs-Back-End führt Gerätezwillingsabfragen für eine Gruppe von Geräten durch, um den Status und Fortschritt von Aktionen zu melden, die auf dem Gerät ausgeführt werden.
  
    ![Muster für Fortschritts- und Statusmeldungen der Geräteverwaltung – Grafik](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="device-updates"></a>Geräteupdates

[Device Update for IoT Hub](../iot-hub-device-update/understand-device-update.md) ist eine umfassende Plattform, mit der Kunden Updates per Funk (Over-the-Air-Updates) für alles – von winzigen Sensoren bis zu Geräten auf Gatewayebene – veröffentlichen, verteilen und verwalten können. Device Update for IoT Hub ermöglicht Kunden eine schnelle Reaktion auf Sicherheitsbedrohungen und die Bereitstellung von Features, um die Geschäftsziele zu erreichen, ohne dass dadurch höhere Entwicklungs- und Wartungskosten für den Aufbau von benutzerdefinierten Updateplattformen anfallen.

Die Plattform „Device Update for IoT Hub“ bietet durch die Integration in Azure IoT Hub optimierte Updatebereitstellung und rationalisierte Abläufe. Mit größerer Reichweite über Azure IoT Edge stellt sie eine in der Cloud gehostete Lösung bereit, die praktisch jedes Gerät verbindet. Sie unterstützt eine breite Palette von IoT-Betriebssystemen – einschließlich Linux und Azure RTOS (Real-Time Operating System, Echtzeitbetriebssystem) – und kann über Open-Source erweitert werden. Folgende Features sind enthalten:

* Unterstützung für das Aktualisieren von Edgegeräten, einschließlich der Komponenten auf Hostebene von Azure IoT Edge
* In Azure IoT Hub integrierte UX für Updateverwaltung
* Schrittweises Updaterollout durch Gerätegruppierung und Steuerung der Updateplanung
* Programmgesteuerte APIs zum Aktivieren von Automatisierung und benutzerdefinierten Portalumgebungen
* Kompatibilitäts-und Statusansichten auf einen Blick über heterogene Geräteflotten hinweg
* Unterstützung für robuste Geräteupdates (A/B) zur Bereitstellung eines nahtlosen Rollbacks
* Content Caching (Inhaltszwischenspeicherung) und Unterstützung getrennter Geräte – einschließlich der Geräte in geschachtelten Konfigurationen – durch integriertes Microsoft Connected Cache und Integration in Azure IoT Edge
* Über das Azure.com-Portal verfügbare Abonnement- und rollenbasierte Zugriffssteuerung
* Umfassende Cloud-zu-Edge-Sicherheitsfeatures und Datenschutzkontrollen

Weitere Informationen finden Sie unter [Device Update for IoT Hub](../iot-hub-device-update/index.yml).

## <a name="next-steps"></a>Nächste Schritte

Mit den Funktionen, Mustern und Codebibliotheken der IoT Hub-Geräteverwaltung können Sie IoT-Anwendungen erstellen, die in jeder Lebenszyklusphase des jeweiligen Geräts die Anforderungen professioneller IoT-Bediener erfüllen.

Weitere Informationen zu den Geräteverwaltungsfunktionen in IoT Hub finden Sie im Tutorial [Erste Schritte mit der Geräteverwaltung](iot-hub-node-node-device-management-get-started.md).
