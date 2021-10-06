---
title: Glossar mit Begriffen zu Azure IoT | Microsoft-Dokumentation
description: Entwicklerhandbuch – ein Glossar zur Erläuterung einiger allgemeiner Begriffe, die in den Artikeln zu Azure IoT verwendet werden.
author: dominicbetts
ms.author: dobett
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: ff8726ff95bd8e5e3c24fecb90d52ec48529ef7b
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/29/2021
ms.locfileid: "129236008"
---
# <a name="glossary-of-iot-terms"></a>Glossar mit IoT-Begriffen

In diesem Artikel werden einige gängige Begriffe aufgeführt, die in den Artikeln zu IoT verwendet werden.

## <a name="a"></a>A

### <a name="advanced-message-queueing-protocol"></a>Advanced Message Queueing Protocol

[Advanced Message Queueing Protocol (AMQP)](https://www.amqp.org/) ist eines der Messagingprotokolle, die von [IoT Hub](#iot-hub) für die Kommunikation mit Geräten unterstützt werden. Weitere Informationen zu den Messagingprotokollen, die IoT Hub unterstützt, finden Sie unter [Senden und Empfangen von Nachrichten mit IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="allocation-policy"></a>Zuordnungsrichtlinie

Beim [Device Provisioning Service](#device-provisioning-service) bestimmt die Zuordnungsrichtlinie, wie Geräte vom Dienst [verknüpften IoT Hubs](#linked-iot-hub) zugeordnet werden.

### <a name="attestation-mechanism"></a>Nachweismechanismus

Im [Device Provisioning Service](#device-provisioning-service) handelt es sich beim Nachweismechanismus um die Methode, die zur Bestätigung der Identität eines Geräts verwendet wird. Der Nachweismechanismus wird für eine [Registrierung](#enrollment) konfiguriert.

Nachweismechanismen sind X.509-Zertifikate, Trusted Platform Modules und symmetrische Schlüssel.

### <a name="automatic-deployment"></a>Automatische Bereitstellung

Bei IoT Edge wird mithilfe einer automatischen Bereitstellung eine Zielgruppe von IoT Edge-Geräten für die Ausführung einer Gruppe von IoT Edge-Modulen konfiguriert. Jede Bereitstellung stellt kontinuierlich sicher, dass alle Geräte, die der Zielbedingung entsprechen, die angegebene Gruppe von Modulen ausführen. Das gilt auch, wenn neue Geräte erstellt oder geändert werden, um der Zielbedingung zu entsprechen. Jedes IoT Edge-Gerät erhält nur die Bereitstellung mit der höchsten Priorität, deren Zielbedingung es erfüllt. Erfahren Sie mehr zur [automatischen IoT Edge-Bereitstellung](../iot-edge/module-deployment-monitoring.md).

### <a name="automatic-device-configuration"></a>Automatische Gerätekonfiguration

Ihr Lösungs-Back-End kann [automatische Gerätekonfigurationen](../iot-hub/iot-hub-automatic-device-management.md) verwenden, um einer Gruppe von [Gerätezwillingen](#device-twin) gewünschte Eigenschaften zuzuweisen und den Status anhand von Systemmetriken und benutzerdefinierten Metriken zu melden.

### <a name="automatic-device-management"></a>Automatische Geräteverwaltung

Die automatische Geräteverwaltung in Azure IoT Hub automatisiert viele der repetitiven und komplexen Aufgaben im Zusammenhang mit der Verwaltung eines umfangreichen Gerätebestands über den gesamten Lebenszyklus. Mit der automatischen Geräteverwaltung können Sie eine Gruppe von Geräten auf der Grundlage ihrer Eigenschaften als Ziel festlegen, eine gewünschte Konfiguration definieren und IoT Hub die Geräte aktualisieren lassen, sobald sie in den entsprechenden Bereich fallen.  Umfasst [automatische Gerätekonfigurationen](../iot-hub/iot-hub-automatic-device-management.md) und [automatische IoT Edge-Bereitstellungen](../iot-edge/how-to-deploy-at-scale.md).

### <a name="azure-digital-twins"></a>Azure Digital Twins

Azure Digital Twins ist ein PaaS-Angebot (Platform as a Service) für die Erstellung digitaler Darstellungen von realen Gegenständen, Orten, Geschäftsprozessen und Personen. Erstellen Sie Zwillingsgraphen, die ganze Umgebungen darstellen, und gewinnen Sie daraus Erkenntnisse, um Produkte zu verbessern, Vorgänge und Kosten zu optimieren und bahnbrechende Kundenerfahrungen zu kreieren. Weitere Informationen finden Sie unter [Azure Digital Twins](../digital-twins/index.yml).

### <a name="azure-digital-twins-instance"></a>Azure Digital Twins-Instanz

Eine einzelne Instanz des Azure Digital Twins-Dienstanbieter im Abonnement eines Kunden. [Azure Digital Twins](#azure-digital-twins) bezieht sich auf den Azure-Dienst als Ganzes, wohingegen Ihre Azure Digital Twins-**Instanz** Ihre persönliche Azure Digital Twins-Ressource darstellt.

### <a name="azure-iot-device-sdks"></a>Azure IoT-Geräte-SDKs

Es sind _Geräte-SDKs_ für mehrere Sprachen verfügbar, mit denen Sie [Geräte-Apps](#device-app) erstellen können, die mit IoT Hub interagieren. In den IoT Hub-Tutorials wird veranschaulicht, wie Sie diese Geräte-SDKs verwenden. Den Quellcode und weitere Informationen zu den Geräte-SDKs finden Sie in [diesem GitHub-Repository](https://github.com/Azure/azure-iot-sdks).

### <a name="azure-iot-explorer"></a>Azure IoT-Explorer

Mit dem [Azure IoT-Explorer](https://github.com/Azure/azure-iot-explorer) können Sie die Telemetriedaten anzeigen, die das Gerät sendet, Geräteeigenschaften einrichten und Befehle aufrufen. Außerdem können Sie den Explorer zur Interaktion mit Ihren Geräten, zum Testen Ihrer Geräte und zur Verwaltung von [IoT Plug & Play-Geräten](#iot-plug-and-play-device) verwenden.

### <a name="azure-iot-service-sdks"></a>Azure IoT-Dienst-SDKs

Es sind _Dienst-SDKs_ für mehrere Sprachen verfügbar, mit denen Sie [Back-End-Apps](#back-end-app) erstellen können, die mit IoT Hub interagieren. In den IoT Hub-Tutorials wird veranschaulicht, wie Sie diese Dienst-SDKs verwenden. Den Quellcode und weitere Informationen zu den Dienst-SDKs finden Sie in [diesem GitHub-Repository](https://github.com/Azure/azure-iot-sdks).

### <a name="azure-iot-tools"></a>Azure IoT-Tools

Die [Azure IoT-Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) sind eine plattformübergreifende, Open-Source-Erweiterung für Visual Studio Code, die Sie bei der Verwaltung von Azure IoT Hub und Geräten in VS Code unterstützt. Mit Azure IoT-Tools können IoT-Entwicklern ganz einfach IoT-Projekte in Visual Studio Code entwickeln.

## <a name="b"></a>B

### <a name="back-end-app"></a>Back-End-App

Im Kontext von [IoT Hub](#iot-hub) ist eine Back-End-App eine App, mit der eine Verbindung mit einem der dienstseitigen Endpunkte von IoT Hub hergestellt wird. Mit einer Back-End-App werden beispielsweise [Gerät-zu-Cloud](#device-to-cloud)-Nachrichten (D2C-Nachrichten) abgerufen, oder sie wird zum Verwalten der [Identitätsregistrierung](#identity-registry) eingesetzt. Eine Back-End-App wird normalerweise in der Cloud ausgeführt, aber in vielen Tutorials handelt es sich bei den Back-End-Apps um Konsolen-Apps, die auf dem lokalen Entwicklungscomputer ausgeführt werden.

### <a name="built-in-endpoints"></a>Integrierte Endpunkte

Ein Typ von [Endpunkt](#endpoint), der in IoT Hub integriert ist. Jeder IoT Hub umfasst einen mit Event Hub kompatiblen [Endpunkt](../iot-hub/iot-hub-devguide-endpoints.md). Sie können einen beliebigen für Event Hubs geeigneten Mechanismus zum Lesen von D2C-Nachrichten von diesem Endpunkt verwenden.

## <a name="c"></a>C

### <a name="cloud-gateway"></a>Cloudgateway

Mit einem Cloudgateway können Verbindungen für Geräte hergestellt werden, für die eine direkte Verbindung mit [IoT Hub](#iot-hub) nicht möglich ist. Ein Cloudgateway wird in der Cloud gehostet, während ein [Bereichsgateway](#field-gateway) lokal auf Ihren Geräten ausgeführt wird. Ein typischer Anwendungsfall für ein Cloudgateway ist die Implementierung der Protokollübersetzung für Ihre Geräte.

### <a name="cloud-property"></a>Cloudeigenschaft

In IoT Central sind Cloudeigenschaften Teil der [Gerätevorlage](#device-model), aber nicht des [Gerätemodells](#device-model). Mit Cloudeigenschaften kann der Lösungsentwickler Gerätemetadaten angeben, die in der IoT Central-Anwendung gespeichert werden sollen. Cloudeigenschaften haben keinen Einfluss auf den Code, den ein Geräteentwickler zum Implementieren des Gerätemodells schreibt.

### <a name="cloud-to-device"></a>Cloud-zu-Gerät (C2D)

Bezieht sich auf Nachrichten, die von IoT Hub an ein verbundenes Gerät gesendet werden. Diese Nachrichten sind häufig Befehle, mit denen das Gerät angewiesen wird, eine Aktion auszuführen. Weitere Informationen finden Sie unter [Senden und Empfangen von Nachrichten mit IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="commands"></a>Befehle

In IoT Plug & Play stellen die in einer [Schnittstelle](#interface) definierten Befehle Methoden dar, die auf dem [digitalen Zwilling](#digital-twin) ausgeführt werden können. Dies kann beispielsweise ein Befehl zum Neustart eines Geräts sein.

### <a name="component"></a>Komponente

In IoT Plug & Play und Azure Digital Twins können Sie die [Schnittstelle](#interface) eines Modells als Assembly aus anderen Schnittstellen erstellen. Ein [Gerätemodell](#device-model) kann mehrere Schnittstellen als Komponenten kombinieren. Ein Modell könnte beispielsweise eine Schalterkomponente und eine Thermostatkomponente enthalten. Mehrere Komponenten in einem Modell können auch denselben Schnittstellentyp verwenden. Beispielsweise könnte ein Modell zwei Thermostatkomponenten enthalten.

### <a name="configuration"></a>Konfiguration

Im Kontext der [automatischen Gerätekonfiguration](../iot-hub/iot-hub-automatic-device-management.md) definiert eine Konfiguration in IoT Hub die gewünschte Konfiguration für eine Gruppe von Gerätezwillingen und bietet einen Satz von Metriken zum Melden von Status und Fortschritt.

### <a name="connection-string"></a>Verbindungszeichenfolge

Sie können Verbindungszeichenfolgen in Ihrem App-Code verwenden, um die Informationen zu kapseln, die zum Herstellen der Verbindung mit einem Endpunkt erforderlich sind. Eine Verbindungszeichenfolge enthält üblicherweise die Adresse des Endpunkts und Sicherheitsinformationen, aber das Format der Verbindungszeichenfolge variiert für die verschiedenen Dienste. Im Zusammenhang mit dem IoT Hub-Dienst gibt es zwei Arten von Verbindungszeichenfolgen:

- *Geräteverbindungszeichenfolgen:* Ermöglichen es Geräten, eine Verbindung mit den geräteseitigen Endpunkten einer IoT Hub-Instanz herzustellen.

- *IoT Hub-Verbindungszeichenfolgen:* Ermöglichen es Back-End-Apps, eine Verbindung mit den dienstseitigen Endpunkten einer IoT Hub-Instanz herzustellen.

### <a name="custom-endpoints"></a>Benutzerdefinierte Endpunkte

Sie können benutzerdefinierte [Endpunkte](../iot-hub/iot-hub-devguide-endpoints.md) auf einen IoT Hub erstellen, um von einer [Routingregel](#routing-rules) versendete Nachrichten weiterzuleiten. Benutzerdefinierte Endpunkte stellen eine direkte Verbindung mit einem Event Hub, einer Service Bus-Warteschlange oder einem Service Bus-Thema her.

### <a name="custom-gateway"></a>Benutzerdefiniertes Gateway

Mit einem Gateway können Verbindungen für Geräte hergestellt werden, für die eine direkte Verbindung mit [IoT Hub](#iot-hub) nicht möglich ist. Sie können Azure IoT Edge zum Erstellen von benutzerdefinierten Gateways verwenden, bei denen benutzerdefinierte Logik implementiert wird, um Nachrichten und benutzerdefinierte Protokollkonvertierungen zu verarbeiten und andere Edge-Verarbeitungsvorgänge durchzuführen.

## <a name="d"></a>D

### <a name="data-point-message"></a>Datenpunktnachricht

Eine Datenpunktnachricht ist eine [D2C-Nachricht](#device-to-cloud), die [Telemetriedaten](#telemetry) wie Windgeschwindigkeit oder Temperatur enthält.

### <a name="default-component"></a>Standardkomponente

In IoT Plug & Play weisen alle [Gerätemodelle](#device-model) eine Standardkomponente auf. Ein einfaches Gerätemodell verfügt nur über eine Standardkomponente. Ein solches Modell wird auch als Gerät ohne Komponente bezeichnet. Bei einem komplexeren Modell sind mehrere Komponenten unterhalb der Standardkomponente geschachtelt.

### <a name="deployment-manifest"></a>Bereitstellungsmanifest

Bei [IoT Edge](#iot-edge) bezeichnet das Bereitstellungsmanifest ein JSON-Dokument mit Informationen, die in mindestens einen Modulzwilling von IoT Edge-Geräten kopiert werden, um eine Gruppe von Modulen, Routen und zugeordneten gewünschten Moduleigenschaften bereitzustellen.

### <a name="desired-configuration"></a>Gewünschte Konfiguration

Im Kontext eines [Gerätezwillings](../iot-hub/iot-hub-devguide-device-twins.md) bezieht sich die gewünschte Konfiguration auf den vollständigen Satz von Eigenschaften und Metadaten im Gerätezwilling, die mit dem Gerät synchronisiert werden sollen.

### <a name="desired-properties"></a>Gewünschte Eigenschaften

Im Kontext eines [Gerätezwillings](../iot-hub/iot-hub-devguide-device-twins.md) handelt es sich bei gewünschten Eigenschaften um einen Unterabschnitt, der zusammen mit [gemeldeten Eigenschaften](#reported-properties) verwendet wird, um die Gerätekonfiguration oder den Gerätezustand zu synchronisieren. Gewünschte Eigenschaften können nur von einer [Back-End-App](#back-end-app) festgelegt werden und werden von der [Geräte-App](#device-app) erkannt.

### <a name="device"></a>Sicherungsmedium

Im Kontext von IoT ist ein Gerät in der Regel eine kleines, eigenständiges Datenverarbeitungsgerät, das Daten sammeln oder andere Geräte steuern kann. Ein Gerät kann beispielsweise ein Umweltüberwachungsgerät oder ein Regler für Bewässerungs- und Lüftungsanlagen in einem Gewächshaus sein. Der [Gerätekatalog](https://catalog.azureiotsolutions.com/) enthält eine Liste mit Hardwaregeräten, die für die Verwendung mit [IoT Hub](#iot-hub) zertifiziert sind.

### <a name="device-app"></a>Geräte-App

Eine Geräte-App wird auf Ihrem [Gerät](#device) ausgeführt und führt die Kommunikation mit [IoT Hub](#iot-hub) durch. Normalerweise nutzen Sie eines der [Azure IoT-Geräte-SDKs](#azure-iot-device-sdks), wenn Sie eine Geräte-App implementieren. In vielen IoT-Tutorials verwenden Sie der Einfachheit halber ein [simuliertes Gerät](#simulated-device).

### <a name="device-builder"></a>Geräteentwickler

Ein Geräteentwickler verwendet ein [Gerätemodell](#device-model) und [Schnittstellen](#interface) beim Implementieren von Code zur Ausführung auf einem [IoT Plug & Play-Gerät](#iot-plug-and-play-device). Geräteentwickler verwenden normalerweise eines der [Azure IoT-Geräte-SDKs](#azure-iot-device-sdks) zum Implementieren des Geräteclients.

### <a name="device-certification"></a>Gerätezertifizierung

Mit dem IoT Plug & Play-Gerätezertifizierungsprogramm wird überprüft, ob ein Gerät die IoT Plug & Play-Zertifizierungsanforderungen erfüllt. Sie können ein zertifiziertes Gerät zum öffentlichen [Katalog mit Certified for Azure IoT-Geräten](https://aka.ms/devicecatalog) hinzufügen.

### <a name="device-condition"></a>Gerätezustand

Bezieht sich auf Informationen zum Gerätezustand, z.B. die derzeit verwendete Verbindungsmethode, die von einer [Geräte-App](#device-app) gemeldet wird. [Geräte-Apps](#device-app) können auch ihre Funktionen melden. Sie können Informationen zum Zustand und zu den Funktionen abfragen, indem Sie Gerätezwillinge verwenden.

### <a name="device-data"></a>Gerätedaten

Gerätedaten sind Daten, die pro Gerät in der IoT Hub-[Identitätsregistrierung](#identity-registry) gespeichert werden. Es ist möglich, diese Daten zu importieren und zu exportieren.

### <a name="device-identity"></a>Geräteidentität

Die Geräteidentität (oder Geräte-ID) ist der eindeutige Bezeichner, der jedem Gerät zugewiesen wird, das in der IoT Hub-[Identitätsregistrierung](#identity-registry) registriert ist.

### <a name="device-management"></a>Geräteverwaltung

Die Geräteverwaltung umfasst den vollständigen Lebenszyklus, der der Verwaltung der Geräte in Ihrer IoT-Lösung zugeordnet ist, z.B. Planen, Bereitstellen, Konfigurieren, Überwachen und Ausmustern.

### <a name="device-management-patterns"></a>Geräteverwaltungsmuster

[IoT Hub](#iot-hub) ermöglicht allgemeine Schritte zur Geräteverwaltung, z.B. Neustart, Zurücksetzung auf die Werkseinstellungen und Durchführung von Firmwareupdates auf Ihren Geräten.

### <a name="device-model"></a>Gerätemodell

Ein Gerätemodell ist ein Typ von [Modell](#model), bei dem die [Digital Twins Definition Language](#digital-twins-definition-language-dtdl) zum Beschreiben der Funktionen eines IoT Plug & Play-Geräts verwendet wird. Ein einfaches Gerätemodell verwendet eine einzelne Schnittstelle, um die Gerätefunktionen zu beschreiben. Ein komplexeres Gerätemodell umfasst mehrere Komponenten, die jeweils eine Reihe von Funktionen beschreiben. Weitere Informationen finden Sie unter [IoT Plug & Play-Komponenten in Modellen](../iot-develop/concepts-modeling-guide.md).

### <a name="device-modeling"></a>Gerätemodellierung

Ein [Geräteentwickler](#device-builder) oder [Modulentwickler](#module-builder) verwendet die [Digital Twins Definition Language](#digital-twins-definition-language-dtdl) zum Modellieren der Funktionen eines [IoT Plug & Play-Geräts](#iot-plug-and-play-device). Ein [Lösungsentwickler](#solution-builder) kann eine IoT-Lösung aus dem Modell konfigurieren.

### <a name="device-provisioning"></a>Gerätebereitstellung

Die Gerätebereitstellung ist der Prozess des Hinzufügens der ersten [Gerätedaten](#device-data) zu den Speichern in Ihrer Lösung. Damit ein neues Gerät eine Verbindung mit Ihrem Hub herstellen kann, müssen Sie der IoT Hub-[Identitätsregistrierung](#identity-registry) eine neue Geräte-ID und Schlüssel hinzufügen. Im Rahmen des Bereitstellungsprozesses müssen Sie unter Umständen gerätespezifische Daten in anderen Lösungsspeichern initialisieren.

### <a name="device-provisioning-service"></a>Device Provisioning Service

Der IoT Hub Device Provisioning Service (DPS) ist ein Hilfsdienst für [IoT Hub](#iot-hub), mit dem Sie Geräte ohne Benutzereingriff auf einem angegebenen IoT Hub konfigurieren können. Mit DPS können Sie Millionen von Geräten auf sichere und skalierbare Weise bereitstellen.

### <a name="device-rest-api"></a>Geräte-REST-API

Sie können die [Geräte-REST-API](/rest/api/iothub/device) auf einem Gerät nutzen, um D2C-Nachrichten an IoT Hub zu senden und [C2D-Nachrichten](#cloud-to-device) von IoT Hub zu empfangen. Es ist in der Regel ratsam, eines der allgemeinen [Geräte-SDKs](#azure-iot-device-sdks) zu nutzen, wie in den IoT Hub-Tutorials veranschaulicht.

### <a name="device-template"></a>Gerätevorlage

In IoT Central ist eine [Gerätevorlage](../iot-central/core/concepts-device-templates.md) eine Blaupause, die die Merkmale und das Verhalten eines Gerätetyps definiert, der eine Verbindung mit Ihrer Anwendung herstellt. Beispielsweise definiert die Gerätevorlage die Telemetrie, die ein Gerät sendet, sodass IoT Central Visualisierungen mit den richtigen Einheiten und Datentypen erstellen kann. Ein [Gerätemodell](#device-model) ist Teil der Gerätevorlage.

### <a name="device-twin"></a>Gerätezwilling

Ein Gerätezwilling ist ein JSON-Dokument, mit dem Informationen zum Gerätezustand gespeichert werden, z.B. Metadaten, Konfigurationen und Bedingungen. IoT Hub speichert einen Gerätezwilling für jedes Gerät, das Sie für IoT Hub bereitstellen. Mit Gerätezwillingen können Sie Gerätebedingungen und Konfigurationen zwischen dem Gerät und dem Lösungs-Back-End synchronisieren. Sie können Gerätezwillinge abfragen, um bestimmte Geräte sowie den Status von Vorgängen mit langer Ausführungsdauer zu ermitteln.

### <a name="device-to-cloud"></a>Gerät-zu-Cloud (D2C)

Bezieht sich auf Nachrichten, die von einem verbundenen Gerät an [IoT Hub](#iot-hub) gesendet werden. Bei diesen Nachrichten kann es sich um [datenpunktbezogene](#data-point-message) oder um [interaktive](#interactive-message) Nachrichten handeln. Weitere Informationen finden Sie unter [Senden und Empfangen von Nachrichten mit IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

### <a name="digital-twin"></a>Digitaler Zwilling

Ein digitaler Zwilling ist eine Sammlung digitaler Daten, die ein physisches Objekt repräsentieren. Änderungen am physischen Objekt spiegeln sich im digitalen Zwilling wider. In einigen Szenarien können Sie den digitalen Zwilling verwenden, um das physische Objekt zu bearbeiten. Der [Azure Digital Twins-Dienst](../digital-twins/index.yml) verwendet [Modelle](#model), die in der [Digital Twins Definition Language (DTDL)](#digital-twins-definition-language-dtdl) formuliert werden, um digitale Zwillinge von physischen Geräten oder abstrakten Geschäftskonzepten darzustellen und so eine Vielzahl verschiedener cloudbasierter Lösungen für digitale Zwillinge zu ermöglichen. Ein [IoT Plug & Play](../iot-develop/index.yml)-Gerät besitzt einen digitalen Zwilling, der durch ein DTDL-[Gerätemodell](#device-model) beschrieben wird.

### <a name="digital-twin-change-events"></a>Änderungsereignisse bei digitalen Zwillingen

Wenn ein [IoT Plug & Play-Gerät](#iot-plug-and-play-device) mit einem IoT-Hub verbunden wird, kann der Hub die Routingfunktionen des Geräts nutzen, um Benachrichtigungen zu Änderungen bei digitalen Zwillingen zu senden. Beispiel: Wenn sich ein [Eigenschaftswert](#properties) auf einem Gerät ändert, kann IoT Hub eine Benachrichtigung an einen Endpunkt wie z. B. einen Event Hub senden.

### <a name="digital-twin-route"></a>Route für digitale Zwillinge

Eine Route, die in einem IoT Hub eingerichtet wurde, um [Änderungsereignisse bei digitalen Zwillingen](#digital-twin-change-events) an einen Endpunkt wie einen Event Hub zu übermitteln.

### <a name="digital-twins-definition-language-dtdl"></a>Digital Twins Definition Language (DTDL)

Eine JSON-LD-Sprache zum Beschreiben von [Modellen](#model) und [Schnittstellen](#interface) für [IoT Plug & Play-Geräte](#iot-plug-and-play-device) und [Azure Digital Twins](../digital-twins/index.yml)-Entitäten. Verwenden Sie die [Digital Twins Definition Language, Version 2](https://github.com/Azure/opendigitaltwins-dtdl), um die Funktionen eines [digitalen Zwillings](#digital-twin) zu beschreiben und es der IoT-Plattform und den IoT-Lösungen zu ermöglichen, die Semantik der Entität zu nutzen. Digital Twins Definition Language wird oft als DTDL abgekürzt.

### <a name="direct-method"></a>Direkte Methode

Eine [direkte Methode](../iot-hub/iot-hub-devguide-direct-methods.md) ist eine Möglichkeit zum Auslösen einer auf einem Gerät auszuführenden Methode, indem eine API auf Ihrem IoT Hub aufgerufen wird.

### <a name="downstream-services"></a>Downstreamdienste

Ein relativer Begriff für Dienste, die Daten aus dem aktuellen Kontext empfangen. Beispielsweise gelten [Time Series Insights](../time-series-insights/index.yml) im Kontext von Azure Digital Twins als Downstreamdienst, wenn Ihre Daten so konfiguriert sind, dass sie von Azure Digital Twins zu Time Series Insights fließen.

## <a name="e"></a>E

### <a name="endpoint"></a>Endpunkt

Eine benannte Darstellung eines Datenroutingdiensts, der Daten von anderen Diensten empfangen kann.

Ein IoT Hub macht mehrere [Endpunkte](../iot-hub/iot-hub-devguide-endpoints.md) verfügbar, mit deren Hilfe Ihre Apps eine Verbindung mit dem IoT Hub herstellen können. Es gibt geräteseitige Endpunkte, die Geräten das Durchführen von Vorgängen ermöglichen, z.B. das Senden von [D2C](#device-to-cloud)-Nachrichten und das Empfangen von [C2D](#cloud-to-device)-Nachrichten. Es gibt dienstseitige Verwaltungsendpunkte, die [Back-End-Apps](#back-end-app) das Durchführen von Vorgängen ermöglichen, z.B. die Verwaltung der [Geräteidentität](#device-identity) und von Gerätezwillingen. Es gibt dienstseitige [integrierte Endpunkte](#built-in-endpoints) zum Lesen von D2C-Nachrichten. Sie können [benutzerdefinierte Endpunkte](#custom-endpoints) erstellen, um von einer [Routingregel](#routing-rules) versendete D2C-Nachrichten zu empfangen.

### <a name="enrollment"></a>Anmeldung

Beim [Device Provisioning Service](#device-provisioning-service) ist die Registrierung der Eintrag einzelner Geräte oder Gerätegruppen, die über die automatische Bereitstellung bei einem [verknüpften IoT Hub](#linked-iot-hub) registriert werden können.

### <a name="enrollment-group"></a>Registrierungsgruppe

Beim [Device Provisioning Service](#device-provisioning-service) wird mit einer Registrierungsgruppe eine Gruppe von Geräten bezeichnet, die gemeinsam einen [Nachweismechanismus](#attestation-mechanism) für X.509 oder einen symmetrischen Schlüssel verwenden.

### <a name="event-handlers"></a>Ereignishandler

Dies kann ein beliebiger Prozess sein, der bei Eintritt eines Ereignisses ausgelöst wird und eine bestimmte Verarbeitungsaktion ausführt. Eine Möglichkeit, Ereignishandler zu erstellen, besteht darin, einer Azure-Funktion Code zur Ereignisverarbeitung hinzuzufügen und Daten über [Endpunkte](#endpoint) und [Ereignisrouting](#event-routing) über diesen zu senden.

### <a name="event-hub-compatible-endpoint"></a>Event Hub-kompatibler Endpunkt

Zum Lesen von [D2C](#device-to-cloud)-Nachrichten, die an Ihren IoT Hub gesendet werden, können Sie sich mit einem Endpunkt auf Ihrem Hub verbinden und eine beliebige Event Hub-kompatible Methode zum Lesen dieser Nachrichten nutzen. Event Hub-kompatible Methoden sind beispielsweise das Verwenden von [Event Hubs SDKs](../event-hubs/event-hubs-programming-guide.md) und [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

### <a name="event-routing"></a>Ereignisrouting

Der Prozess zum Senden von Ereignissen und zugehörigen Daten von einem Gerät oder Dienst zum [Endpunkt](#endpoint) eines anderen Geräts oder Diensts. 

Bei IoT Hub können Sie [Routingregeln](#routing-rules) definieren, die beschreiben, wie Nachrichten gesendet werden sollen. Bei Azure Digital Twins sind Ereignisrouten Entitäten, die zu diesem Zweck erstellt wurden. Azure Digital Twins-Ereignisrouten können Filter enthalten, um die Typen von Ereignissen einzuschränken, die an den jeweiligen Endpunkt gesendet werden.

## <a name="f"></a>F

### <a name="field-gateway"></a>Bereichsgateway

Ein Bereichsgateway ermöglicht Verbindungen für Geräte, die keine direkte Verbindung mit dem [IoT Hub](#iot-hub) herstellen können. Es wird meist lokal mit Ihren Geräten bereitgestellt. Weitere Informationen finden Sie unter [Was ist Azure IoT Hub?](../iot-hub/about-iot-hub.md).

## <a name="g"></a>G

### <a name="gateway"></a>Gateway

Mit einem Gateway können Verbindungen für Geräte hergestellt werden, für die eine direkte Verbindung mit [IoT Hub](#iot-hub) nicht möglich ist. Siehe auch [Bereichsgateway](#field-gateway), [Cloudgateway](#cloud-gateway)und [Benutzerdefiniertes Gateway](#custom-gateway).

### <a name="gateway-device"></a>Gatewaygerät

Ein Gerät ist ein Beispiel für ein [Bereichsgateway](#field-gateway). Ein Gatewaygerät kann ein Standard-IoT-[Gerät](#device) oder ein [IoT Edge-Gerät](#iot-edge-device) sein.

Über ein Gatewaygerät können nachgeschaltete Geräte, die keine direkte Verbindung mit [IoT Hub](#iot-hub) herstellen können, Verbindungen herstellen.

## <a name="h"></a>H

### <a name="hardware-security-module"></a>Hardwaresicherheitsmodul

Ein Hardwaresicherheitsmodul (HSM) wird für die sichere, hardwarebasierte Speicherung von Gerätegeheimnissen verwendet. Dies ist die sicherste Form von geheimem Speicher für ein Gerät. In einem HSM können sowohl X.509-Zertifikate als auch symmetrische Schlüssel gespeichert werden. Beim [Device Provisioning Service](#device-provisioning-service) kann ein HSM für einen [Nachweismechanismus](#attestation-mechanism) verwendet werden.

## <a name="i"></a>I

### <a name="id-scope"></a>ID-Bereich

Der ID-Bereich ist ein eindeutiger Wert, der einer [DPS-Instanz (Device Provisioning Service)](#device-provisioning-service) bei ihrer Erstellung zugewiesen wird.

IoT Central-Anwendungen nutzen DPS-Instanzen und machen den ID-Bereich über die IoT Central-Benutzeroberfläche verfügbar.

### <a name="identity-registry"></a>Identitätsregistrierung

Die [Identitätsregistrierung](../iot-hub/iot-hub-devguide-identity-registry.md) ist die integrierte Komponente in IoT Hub, in der Informationen zu den einzelnen Geräten gespeichert werden, die sich mit einem IoT Hub verbinden dürfen.

### <a name="individual-enrollment"></a>Individuelle Registrierung

Beim [Device Provisioning Service](#device-provisioning-service) wird ein einzelnes Gerät, das ein untergeordnetes X.509-Zertifikat oder einen symmetrischen Schlüssel als [Nachweismechanismus](#attestation-mechanism) verwendet, über eine individuelle Registrierung identifiziert.

### <a name="interactive-message"></a>Interaktive Nachricht

Eine interaktive Nachricht ist eine [C2D](#cloud-to-device)-Nachricht, die eine sofortige Aktion im Lösungs-Back-End auslöst. Beispielsweise kann ein Gerät einen Alarm zu einem Fehler senden, der automatisch in einem CRM-System protokolliert werden soll.

### <a name="interface"></a>Schnittstelle

In IoT Plug & Play beschreibt eine Schnittstelle verwandte Funktionen, die durch ein [IoT Plug & Play-Gerät](#iot-plug-and-play-device) oder einen [digitalen Zwilling](#digital-twin) implementiert werden. Sie können Schnittstellen in verschiedenen [Gerätemodellen](#device-model) wiederverwenden. Wenn eine Schnittstelle in einem Gerätemodell verwendet wird, definiert sie eine [Komponente](#component) des Geräts. Ein einfaches Gerät enthält nur eine Standardschnittstelle.

Bei Azure Digital Twins kann das Codeelement auf oberster Ebene in einer [DTDL](#digital-twins-definition-language-dtdl)-Modelldefinition als *Schnittstelle* bezeichnet werden.

### <a name="iot-edge"></a>IoT Edge

Azure IoT Edge ermöglicht die cloudgesteuerte Bereitstellung von Azure-Diensten und lösungsspezifischem Code auf lokalen Geräten. [IoT Edge-Geräte](#iot-edge-device) können Daten anderer Geräte aggregieren und Berechnungen und Analysen durchführen, bevor sie die Daten an die Cloud senden. Weitere Informationen finden Sie unter [Azure IoT Edge](../iot-edge/index.yml).

### <a name="iot-edge-agent"></a>IoT Edge-Agent

Die für die Bereitstellung und Überwachung von Modulen zuständige Komponente der IoT Edge-Laufzeit.

### <a name="iot-edge-device"></a>IoT Edge-Gerät

Ein IoT Edge-Gerät verwendet containerisierte IoT Edge-[Module](#modules), um Azure-Dienste, Drittanbieterdienste oder Ihren Code auszuführen. Auf dem IoT Edge-Gerät verwaltet die [IoT Edge-Runtime](#iot-edge-runtime) die Module. Sie können ein IoT Edge-Gerät remote über die Cloud überwachen und verwalten.

### <a name="iot-edge-hub"></a>IoT Edge-Hub

Die Komponente der IoT Edge-Laufzeit, die für die Kommunikation zwischen Modulen sowie für die (bei IoT Hub eingehende) Upstreamkommunikation und die (von IoT Hub ausgehende) Downstreamkommunikation zuständig ist.

### <a name="iot-edge-runtime"></a>IoT Edge-Laufzeit

Die IoT Edge-Laufzeit enthält alles, was Microsoft für die Installation auf einem IoT Edge-Gerät bereitstellt. Sie enthält den Edge-Agent, den Edge-Hub und den IoT Edge-Sicherheitsdaemon.

### <a name="iot-extension-for-azure-cli"></a>IoT-Erweiterung für die Azure CLI

Die [IoT-Erweiterung für die Azure-Befehlszeilenschnittstelle](https://github.com/Azure/azure-iot-cli-extension) ist ein plattformübergreifendes Befehlszeilentool. Mit dem Tool können Sie Ihre Geräte in der [Identitätsregistrierung](#identity-registry) verwalten, Nachrichten und Dateien für Ihre Geräte senden und empfangen und die IoT Hub-Vorgänge überwachen.

### <a name="iot-hub"></a>IoT Hub

IoT Hub ist ein vollständig verwalteter Azure-Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von Geräten und einem Lösungs-Back-End ermöglicht. Weitere Informationen finden Sie unter [Was ist Azure IoT Hub?](../iot-hub/about-iot-hub.md). Mit Ihrem Azure-Abonnement können Sie IoT-Hubs für die Verarbeitung Ihrer IoT-Messagingworkloads erstellen.

### <a name="iot-hub-metrics"></a>IoT Hub-Metriken

Mit IoT Hub-Metriken erhalten Sie Daten zum Zustand von IoT Hubs in Ihrem Azure-Abonnement. Auf der Grundlage von IoT Hub-Metriken können Sie die allgemeine Integrität des Diensts und der mit ihm verbundenen Geräte beurteilen. Anhand von IoT Hub-Metriken können Sie sich über den Status Ihrer IoT Hub-Instanz informieren und den Ursachen von Fehlern nachgehen, ohne sich dafür an den Azure-Support wenden zu müssen. Weitere Informationen finden Sie unter [Überwachen von Azure IoT Hub](../iot-hub/monitor-iot-hub.md).

### <a name="iot-hub-query-language"></a>IoT Hub-Abfragesprache

Die [IoT Hub-Abfragesprache](../iot-hub/iot-hub-devguide-query-language.md) ist eine SQL-ähnliche Sprache, mit der Sie Ihre [Aufträge](#job), digitalen Zwillinge und Gerätezwillinge abfragen können.

### <a name="iot-hub-resource-rest-api"></a>IoT Hub-Ressourcen-REST-API

Sie können die [IoT Hub-Ressourcen-REST-API](/rest/api/iothub/iothubresource) verwenden, um die IoT Hub-Instanzen in Ihrem Azure-Abonnement zu verwalten, in dem Vorgänge wie das Erstellen, Aktualisieren und Löschen von Hubs durchgeführt werden.

### <a name="iot-plug-and-play-bridge"></a>IoT Plug & Play-Brücke

IoT Plug & Play-Brücke ist eine Open-Source-Anwendung, die es vorhandenen, an Windows- oder Linux-Gateways angefügten Sensoren und Peripheriegeräten ermöglicht, eine Verbindung als [IoT Plug & Play-Geräte](#iot-plug-and-play-device) herzustellen.

### <a name="iot-plug-and-play-conventions"></a>IoT Plug & Play-Konventionen

Von IoT Plug & Play-[Geräten](#iot-plug-and-play-device) wird erwartet, dass sie beim Datenaustausch mit einer Lösung eine Reihe von Konventionen einhalten.

### <a name="iot-plug-and-play-device"></a>IoT Plug & Play-Gerät

Ein IoT Plug & Play-Gerät ist normalerweise ein kleines, eigenständiges Computergerät, das Daten sammelt oder andere Geräte steuert. Auf einem solchen Gerät wird Software oder Firmware ausgeführt, die ein [Gerätemodell](#device-model) implementiert.  Ein IoT Plug & Play-Gerät könnte z.B. ein Gerät zur Überwachung von Umweltdaten oder ein Regler für ein intelligentes Bewässerungssystem in der Landwirtschaft sein. Ein IoT Plug & Play-Gerät kann direkt oder als IoT Edge-Modul implementiert werden. Sie können eine in der Cloud gehostete IoT-Lösung zum Steuern, Überwachen und Empfangen von Daten von IoT Plug & Play-Geräten schreiben.

## <a name="j"></a>J

### <a name="job"></a>Auftrag

In [IoT Hub](#iot-hub) können Sie mit [Aufträgen](../iot-hub/iot-hub-devguide-jobs.md) Aktivitäten für eine Gruppe von Geräten planen und nachverfolgen, die bei Ihrem IoT-Hub registriert sind. Aktivitäten sind beispielsweise das Aktualisieren der [gewünschten Eigenschaften](#desired-properties) von Gerätezwillingen und ihrer [Tags](#tags) sowie das Aufrufen [direkter Methoden](#direct-method). Außerdem nutzt IoT Hub Aufträge zum [Importieren und Exportieren](../iot-hub/iot-hub-devguide-identity-registry.md#import-and-export-device-identities) aus der [Identitätsregistrierung](#identity-registry).

In IoT Central können Sie Ihre verbundenen Geräte mit [Aufträgen](../iot-central/core/howto-manage-devices-in-bulk.md) gesammelt verwalten und für diese Eigenschaften festlegen und Befehle aufrufen. IoT Central-Aufträge ermöglichen auch das Aktualisieren von [Cloudeigenschaften](#cloud-property) in einem Massenvorgang.

## <a name="l"></a>L

### <a name="leaf-device"></a>Blattgerät

Bei [IoT Edge](#iot-edge) bezeichnet ein Blattgerät ein Gerät ohne nachgeschaltete Geräte.

### <a name="lifecycle-event"></a>Lebenszyklusereignis

Bei Azure Digital Twins wird dieser Typ von Ereignis ausgelöst, wenn ein Datenelement, z. B. ein digitaler Zwilling, eine Beziehung oder ein Ereignishandler, erstellt oder aus der Azure Digital Twins-Instanz gelöscht wird.

### <a name="linked-iot-hub"></a>Verknüpfter IoT Hub

Der [Device Provisioning Service (DPS)](#device-provisioning-service) kann Geräte in IoT Hubs bereitstellen, die mit ihm verknüpft wurden. Wenn Sie einen IoT Hub mit einer DPS-Instanz verknüpfen, kann der Dienst eine Geräte-ID registrieren und die Ausgangskonfiguration des Gerätezwillings festlegen.

## <a name="m"></a>M

### <a name="model"></a>Modell

Ein Modell definiert einen Typ von Entität in Ihrer physischen Umgebung, einschließlich ihrer Eigenschaften, Telemetrien, Komponenten und zuweilen weiterer Informationen. Modelle werden verwendet, um [digitale Zwillinge](#digital-twin) zu erstellen, die bestimmte physische Objekte dieses Typs darstellen. Modelle werden in der [Digital Twins Definition Language](#digital-twins-definition-language-dtdl) geschrieben.

Beim [Azure Digital Twins-Dienst](../digital-twins/index.yml) können Modelle zum Definieren von Geräten und übergeordneten abstrakten Geschäftskonzepten verwendet werden. Bei [IoT Plug & Play](../iot-develop/index.yml) werden [Gerätemodelle](#device-model) verwendet, um Geräte genau zu beschreiben.

### <a name="model-id"></a>Modell-ID

Wenn ein IoT Plug & Play-Gerät eine Verbindung mit einem IoT-Hub herstellt, sendet es die **Modell-ID** des von ihm implementierten [DTDL](#digital-twins-definition-language-dtdl)-Modells. Anhand dieser ID ist die Lösung in der Lage, das Gerätemodell zu finden.

### <a name="model-repository"></a>Modellrepository

In einem Modellrepository werden [Gerätemodelle](#device-model) und [Schnittstellen](#interface) gespeichert.

### <a name="model-repository-rest-api"></a>Modellrepository-REST-API

Eine API zum Verwalten von und Interagieren mit dem Modellrepository. So können Sie beispielsweise mithilfe der API [Gerätemodelle](#device-model) hinzufügen und danach suchen.

### <a name="module-builder"></a>Modulentwickler

Ein Modulentwickler verwendet ein [Gerätemodell](#device-model) und [Schnittstellen](#interface) beim Implementieren von Code zur Ausführung auf einem [IoT Plug & Play-Gerät](#iot-plug-and-play-device). Modulentwickler implementieren den Code als Modul oder IoT Edge-Modul, das für die IoT Edge-Runtime auf einem Gerät bereitgestellt werden soll.

### <a name="module-identity"></a>Modulidentität

Die Modulidentität ist der eindeutige Bezeichner, der jedem Modul zugeordnet ist, das zu einem Gerät gehört. Die Modulidentität wird auch in der [Identitätsregistrierung](#identity-registry) registriert.

Die Modulidentität gibt die Sicherheitsanmeldeinformationen an, die das Modul für die Authentifizierung beim [IoT Hub](#iot-hub) oder im Falle eines IoT Edge-Moduls beim [IoT Edge Hub](#iot-edge-hub) verwendet.

### <a name="module-image"></a>Modulimage

Das Docker-Image, das von der [IoT Edge-Runtime](#iot-edge-runtime) zum Instanziieren der Modulinstanzen verwendet wird.

### <a name="module-twin"></a>Modulzwilling

Modulzwillinge ähneln Gerätezwillingen und sind JSON-Dokumente, in denen Modulstatusinformationen wie Metadaten, Konfigurationen und Zustände gespeichert werden. IoT Hub speichert einen Modulzwilling für jede Modulidentität, die Sie unter einer Geräteidentität in Ihrer IoT Hub-Instanz bereitstellen. Mit Modulzwillingen können Sie Modulbedingungen und Konfigurationen zwischen dem Modul und dem Lösungs-Back-End synchronisieren. Sie können Modulzwillinge abfragen, um bestimmte Module zu ermitteln und den Status von Vorgängen mit langer Ausführungsdauer abzufragen.

### <a name="modules"></a>Module

Auf der Geräteseite können Sie mithilfe der IoT Hub-Geräte-SDKs [Module](../iot-hub/iot-hub-devguide-module-twins.md) erstellen, die jeweils eine unabhängige Verbindung mit IoT Hub herstellen. Durch diese Funktionalität können Sie separate Namespaces für unterschiedliche Komponenten auf Ihrem Gerät verwenden.

Modulidentität und Modulzwilling bieten die gleichen Funktionen wie [Geräteidentität](#device-identity) und [Gerätezwilling](#device-twin), aber mit einer höheren Granularität. Dank dieser höheren Granularität können geeignete Geräte (etwa betriebssystembasierte Geräte oder Firmwaregeräte, die mehrere Komponenten verwalten) Konfigurationen und Zustände für die einzelnen Komponenten isolieren.

Bei [IoT Edge](#iot-edge) ist ein Modul ein Docker-Container, der auf IoT Edge-Geräten bereitgestellt werden kann. Es führt eine spezifische Aufgabe aus und erfasst so beispielsweise eine Nachricht von einem Gerät, transformiert eine Nachricht oder sendet eine Nachricht an eine IoT Hub-Instanz. Es kommuniziert mit anderen Modulen und sendet Daten an die [IoT Edge-Runtime](#iot-edge-runtime).

### <a name="mqtt"></a>MQTT

[MQTT](https://mqtt.org/) ist eines der Messagingprotokolle, die von [IoT Hub](#iot-hub) für die Kommunikation mit Geräten unterstützt werden. Weitere Informationen zu den Messagingprotokollen, die IoT Hub unterstützt, finden Sie unter [Senden und Empfangen von Nachrichten mit IoT Hub](../iot-hub/iot-hub-devguide-messaging.md).

## <a name="o"></a>O

### <a name="ontology"></a>Ontologie

Eine Reihe von Modellen für eine bestimmte Domäne, z. B. Immobilien, Smart Citys, IoT-Systeme, Energienetze und mehr. Ontologien werden oft als Schemas für Wissensgraphen wie die in [Azure Digital Twins](#azure-digital-twins) verwendet, weil sie einen Ausgangspunkt bereitstellen, der auf Branchenstandards und bewährten Methoden basiert.

Weitere Informationen zu Ontologien finden Sie unter [Was ist eine Ontologie?](../digital-twins/concepts-ontologies.md).

### <a name="operations-monitoring"></a>Vorgangsüberwachung

Die IoT Hub-[Vorgangsüberwachung](../iot-hub/iot-hub-operations-monitoring.md) ermöglicht Ihnen das Überwachen des Status von Vorgängen auf Ihrem IoT Hub in Echtzeit. [IoT Hub](#iot-hub) verfolgt Ereignisse in verschiedenen Vorgangskategorien nach. Sie können festlegen, dass Ereignisse aus einer oder mehreren Kategorien zur Verarbeitung an einen IoT Hub-Endpunkt gesendet werden. Sie können die Daten auf Fehler überwachen oder eine komplexere auf Datenmustern basierende Verarbeitung einrichten.

## <a name="p"></a>P

### <a name="physical-device"></a>Physisches Gerät

Ein physisches Gerät ist ein echtes Gerät, z.B. ein Raspberry Pi mit einer Verbindung mit einem IoT Hub. Der Einfachheit halber werden in vielen IoT Hub-Tutorials [simulierte Geräte](#simulated-device) verwendet, damit Sie Beispiele auf Ihrem lokalen Computer ausführen können.

### <a name="primary-and-secondary-keys"></a>Primär- und Sekundärschlüssel

Wenn Sie einen geräteseitigen oder dienstseitigen Endpunkt mit einem IoT Hub verbinden, enthält die [Verbindungszeichenfolge](#connection-string) einen Schlüssel zum Gewähren des Zugriffs. Wenn Sie der [Identitätsregistrierung](#identity-registry) ein Gerät hinzufügen oder dem Hub eine [SAS-Richtlinie](#shared-access-policy) hinzufügen, generiert der Dienst einen Primär- und einen Sekundärschlüssel. Dank der zwei Schlüssel können Sie ein Rollover von einem Schlüssel zum anderen Schlüssel durchführen, wenn Sie einen Schlüssel aktualisieren, ohne den Zugriff auf den IoT Hub zu verlieren.

### <a name="properties"></a>Eigenschaften

Eigenschaften sind Datenfelder, die in einer [Schnittstelle](#interface) definiert sind und einen bestimmten beständigen Zustand eines [digitalen Zwillings](#digital-twin) repräsentieren. Sie können Eigenschaften als schreibgeschützt oder beschreibbar deklarieren. Schreibgeschützte Eigenschaften, z.B. eine Seriennummer, werden durch Code festgelegt, der auf dem [IoT Plug & Play-Gerät](#iot-plug-and-play-device) selbst ausgeführt wird. Schreibbare Eigenschaften, z.B. ein Alarmschwellenwert, werden normalerweise aus der cloudbasierten IoT-Lösung festgelegt.

### <a name="property-change-event"></a>Eigenschaftenänderungsereignis

Ein Ereignis, das Folge einer Eigenschaftsänderung in einem [digitalen Zwilling](#digital-twin) ist.

### <a name="protocol-gateway"></a>Protokollgateway

Ein Protokollgateway wird der Regel in der Cloud bereitgestellt und bietet Protokollübersetzungsdienste für Geräte, die sich mit [IoT Hub](#iot-hub) verbinden. Weitere Informationen finden Sie unter [Was ist Azure IoT Hub?](../iot-hub/about-iot-hub.md).

## <a name="r"></a>R

### <a name="registration"></a>Registrierung

Bei einer Registrierung handelt es sich um den Eintrag eines Geräts in der IoT Hub-[Identitätsregistrierung](#identity-registry). Sie können ein Gerät direkt oder zur Automatisierung der Geräteregistrierung über den [Device Provisioning Service](#device-provisioning-service) registrieren.

### <a name="registration-id"></a>Registrierungs-ID

Die Registrierungs-ID wird zur eindeutigen Identifizierung der [Registrierung](#registration) eines Geräts beim [Device Provisioning Service](#device-provisioning-service) verwendet. Der Wert der Registrierungs-ID kann mit dem der [Geräteidentität](#device-identity) übereinstimmen.

### <a name="relationship"></a>Relationship

Beim [Azure Digital Twins](../digital-twins/index.yml)-Dienst werden Beziehungen verwendet, um [digitale Zwillinge](#digital-twin) mit Wissensgraphen zu verbinden, die Ihre gesamte physische Umgebung digital darstellen. Die Typen von Beziehungen, die für Ihre Zwillinge möglich sind, werden im Rahmen der [Modell](#model)definitionen der Zwillinge definiert: Das [DTDL](#digital-twins-definition-language-dtdl)-Modell für einen bestimmten Zwillingstyp enthält Informationen zu seinen möglichen Beziehungen zu anderen Zwillingen.

### <a name="reported-configuration"></a>Gemeldete Konfiguration

Im Kontext eines [Gerätezwillings](../iot-hub/iot-hub-devguide-device-twins.md) bezieht sich die gemeldete Konfiguration auf den vollständigen Satz von Eigenschaften und Metadaten im Gerätezwilling, die an das Lösungs-Back-End gemeldet werden sollen.

### <a name="reported-properties"></a>Gemeldete Eigenschaften

Im Kontext eines [Gerätezwillings](../iot-hub/iot-hub-devguide-device-twins.md) handelt es sich bei gemeldeten Eigenschaften um einen Unterabschnitt, der zusammen mit [gewünschten Eigenschaften](#desired-properties) verwendet wird, um die Gerätekonfiguration oder den Gerätezustand zu synchronisieren. Gemeldete Eigenschaften können nur von der [Geräte-App](#device-app) festgelegt werden. Das Lesen und Abfragen ist per [Back-End-App](#back-end-app) möglich.

### <a name="retry-policy"></a>Wiederholungsrichtlinie

Sie verwenden eine Wiederholungsrichtlinie, um [vorübergehende Fehler](/azure/architecture/best-practices/transient-faults) zu verarbeiten, wenn Sie eine Verbindung mit einem Clouddienst herstellen.

### <a name="routing-rules"></a>Routingregeln

Sie konfigurieren [Routingregeln](../iot-hub/iot-hub-devguide-messages-read-custom.md) in Ihrem IoT Hub, um D2C-Nachrichten an einen [integrierten Endpunkt](#built-in-endpoints) oder [benutzerdefinierte Endpunkte](#custom-endpoints) für die Verarbeitung durch Ihr Lösungs-Back-End weiterzuleiten.

## <a name="s"></a>E

### <a name="sasl-plain"></a>SASL PLAIN

SASL PLAIN ist ein Protokoll, das vom AMQP-Protokoll zum Übertragen von Sicherheitstoken verwendet wird.

### <a name="service-operations-endpoint"></a>Endpunkt für Dienstvorgänge

Ein [Endpunkt](#endpoint) für die Verwaltung von Diensteinstellungen, der von einem Dienstadministrator verwendet wird. Beispielsweise verwenden Sie den Dienstendpunkt im [Device Provisioning Service](#device-provisioning-service) zum Verwalten von Registrierungen.

### <a name="service-rest-api"></a>Dienst-REST-API

Sie können die [Dienst-REST-API](/rest/api/iothub/service/configuration) des Lösungs-Back-Ends zur Verwaltung Ihrer Geräte verwenden. Mithilfe der API können Sie Eigenschaften von [Gerätezwillingen](#device-twin) abrufen und aktualisieren, [direkte Methoden](#direct-method) aufrufen und [Aufträge](#job) planen. Es ist in der Regel ratsam, ein allgemeines [Dienst-SDK](#azure-iot-service-sdks) zu nutzen, wie in den IoT Hub-Tutorials veranschaulicht.

### <a name="shared-access-policy"></a>SAS-Richtlinie

Mit einer SAS-Richtlinie werden die Berechtigungen definiert, die allen Personen mit einem [Primär- oder Sekundärschlüssel](#primary-and-secondary-keys), der dieser Richtlinie zugeordnet ist, gewährt werden. Sie können die SAS-Richtlinien und Schlüssel für den Hub im Portal verwalten.

### <a name="shared-access-signature"></a>Shared Access Signature (SAS)

Bei Shared Access Signatures handelt es sich um einen Authentifizierungsmechanismus, der auf sicheren Hashes (SHA-256) oder URIs basiert. Die SAS-Authentifizierung besteht aus zwei Komponenten: einer _Richtlinie für den gemeinsamen Zugriff_ und einer _Shared Access Signature_ (häufig auch als „Token“ bezeichnet). Ein Gerät verwendet eine SAS für die Authentifizierung mit IoT Hub. [Back-End-Apps](#back-end-app) nutzen ebenfalls eine SAS für die Authentifizierung mit den dienstseitigen Endpunkten von IoT Hub. Normalerweise binden Sie das SAS-Token in die [Verbindungszeichenfolge](#connection-string) ein, die von einer App verwendet wird, um eine Verbindung mit IoT Hub herzustellen.

### <a name="simulated-device"></a>Simuliertes Gerät

Der Einfachheit halber werden in vielen IoT Hub-Tutorials simulierte Geräte verwendet, damit Sie Beispiele auf Ihrem lokalen Computer ausführen können. Ein [physisches Gerät](#physical-device) ist dagegen ein echtes Gerät, z.B. ein Raspberry Pi mit einer Verbindung mit IoT Hub.

### <a name="solution"></a>Lösung

Eine _Lösung_ kann eine Visual Studio-Lösung (Projektmappe) sein, die mindestens ein Projekt enthält. Eine _Lösung_ kann auch eine IoT-Lösung sein, die Elemente wie Geräte, [Geräte-Apps](#device-app), einen IoT Hub, andere Azure-Dienste und [Back-End-Apps](#back-end-app) enthält.

### <a name="solution-builder"></a>Lösungsentwickler

Ein Lösungsentwickler erstellt das Lösungs-Back-End. Ein Lösungsentwickler arbeitet normalerweise mit Azure-Ressourcen wie IoT Hub und [Modellrepositorys](#model-repository).

### <a name="system-properties"></a>Systemeigenschaften

Im Kontext eines [Gerätezwillings](../iot-hub/iot-hub-devguide-device-twins.md) sind Systemeigenschaften schreibgeschützt und enthalten Informationen zur Gerätenutzung, z.B. den Zeitpunkt der letzten Aktivität und den Verbindungsstatus.

## <a name="t"></a>T

### <a name="tags"></a>`Tags`

Im Kontext eines [Gerätezwillings](../iot-hub/iot-hub-devguide-device-twins.md) sind Tags Gerätemetadaten, die vom Lösungs-Back-End in Form eines JSON-Dokuments gespeichert und abgerufen werden. Tags sind für Apps auf einem Gerät nicht sichtbar.

### <a name="target-condition"></a>Zielbedingung

Bei einer IoT Edge-Bereitstellung werden die Zielgeräte für die Bereitstellung anhand der Zielbedingung ausgewählt, z. B. **tag.environment = prod**. Die Zielbedingung wird kontinuierlich ausgewertet, um jedes neue Gerät einzubeziehen, das die Anforderungen erfüllt, bzw. Geräte zu entfernen, die die Bedingung nicht mehr erfüllen.

### <a name="telemetry"></a>Telemetrie

Mit Geräten werden Telemetriedaten erfasst, z.B. Windgeschwindigkeit oder Temperatur, und es werden Datenpunktnachrichten verwendet, um Telemetriedaten an IoT Hub zu senden.

Bei IoT Plug & Play und Azure Digital Twins stellen in einer [Schnittstelle](#interface) definierte Telemetriefelder Messungen dar. Diese Messungen sind normalerweise Werte wie Sensormesswerte, die von Geräten wie [IoT Plug & Play-Geräten](#iot-plug-and-play-device) als Datenstrom gesendet werden.

Im Gegensatz zu [Eigenschaften](#properties) wird Telemetrie nicht in einem [digitalen Zwilling](#digital-twin) gespeichert. Telemetrie ist ein Strom zeitgebundener Datenereignissen, die in der Reihenfolge ihres Auftretens verarbeitet werden müssen.

### <a name="telemetry-event"></a>Telemetrieereignis

Ein Ereignis, das den Eingang von Telemetriedaten angibt.

### <a name="token-service"></a>Tokendienst

Sie können einen Tokendienst verwenden, um einen Authentifizierungsmechanismus für Ihre Geräte zu implementieren. Er verwendet eine [SAS-Richtlinie](#shared-access-policy) von IoT Hub mit **DeviceConnect**-Berechtigungen, um Token mit *Gerätebereich* zu erstellen. Mit diesen Token kann ein Gerät eine Verbindung mit Ihrem IoT Hub herstellen. Ein Gerät verwendet einen benutzerdefinierten Authentifizierungsmechanismus für die Authentifizierung mit dem Tokendienst. Sofern die Authentifizierung des Geräts erfolgreich ist, stellt der Tokendienst ein SAS-Token für das Gerät bereit, mit dem auf IoT Hub zugegriffen werden kann.

### <a name="twin-graph-or-digital-twin-graph"></a>Zwillingsgraph (oder Digital Twin-Graph)

Beim [Azure Digital Twins](../digital-twins/index.yml)-Dienst können Sie [digitale Zwillinge](#digital-twin) mit [Beziehungen](#relationship) verbinden, um Wissensgraphen zu erstellen, die Ihre gesamte physische Umgebung digital darstellen. Eine einzelne [Azure Digital Twins-Instanz](#azure-digital-twins-instance) kann viele nicht verbundene Graphen oder einen einzelnen verbundenen Graphen hosten.

### <a name="twin-queries"></a>Zwillingsabfragen

Für [Geräte- und Modulzwillingsabfragen](../iot-hub/iot-hub-devguide-query-language.md) wird die SQL-ähnliche IoT Hub-Abfragesprache verwendet, um Informationen von ihren Geräte- oder Modulzwillingen abzufragen. Sie können die gleiche IoT Hub-Abfragesprache verwenden, um Informationen zu einem [Auftrag](#job) abzurufen, der in IoT Hub ausgeführt wird.

### <a name="twin-synchronization"></a>Zwillingssynchronisierung

Bei der Zwillingssynchronisierung werden die [gewünschten Eigenschaften](#desired-properties) in Ihren Geräte- oder Modulzwillingen verwendet, um die Geräte oder Module zu konfigurieren und [gemeldete Eigenschaften](#reported-properties) von Ihren Geräten oder Modulen abzurufen und im entsprechenden Zwilling zu speichern.

## <a name="u"></a>U

### <a name="upstream-services"></a>Upstreamdienste

Ein relativer Begriff für Dienste, die Daten in den aktuellen Kontext einspeisen. Beispielsweise gilt IoT Hub im Kontext von Azure Digital Twins als Upstreamdienst, da Ihre Daten vom IoT Hub zu Azure Digital Twins fließen.

## <a name="x"></a>X

### <a name="x509-client-certificate"></a>X.509-Clientzertifikat

Ein Gerät kann ein X.509-Zertifikat für die Authentifizierung beim [IoT Hub](#iot-hub) verwenden. Der Einsatz eines X.509-Zertifikats ist eine Alternative zur Verwendung eines [SAS-Tokens](#shared-access-signature).