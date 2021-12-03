---
title: Glossar mit Begriffen zu Azure IoT | Microsoft-Dokumentation
description: Entwicklerhandbuch – ein Glossar zur Erläuterung einiger allgemeiner Begriffe, die in den Artikeln zu Azure IoT verwendet werden.
author: dominicbetts
ms.author: dobett
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: eb2e48629e07271fcc151a3de97659c97cbb6719
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491182"
---
# <a name="glossary-of-iot-terms"></a>Glossar mit IoT-Begriffen

In diesem Artikel werden einige gängige Begriffe aufgeführt, die in den Artikeln zu IoT verwendet werden.

## <a name="a"></a>A

### <a name="advanced-message-queueing-protocol"></a>Advanced Message Queueing Protocol

Eines der Messagingprotokolle, das [IoT Hub](#iot-hub) und IoT Central für die Kommunikation mit [Geräten](#device) unterstützen.

[Weitere Informationen](../iot-hub/iot-hub-devguide-protocols.md)

Gilt für: IoT Hub, IoT Central, Geräteentwickler

### <a name="allocation-policy"></a>Zuordnungsrichtlinie

Beim [Device Provisioning Service](#device-provisioning-service) bestimmt die Zuordnungsrichtlinie, wie [Geräte](#device) vom Dienst einem [verknüpften IoT-Hub](#linked-iot-hub) zugewiesen werden.

Gilt für: Device Provisioning Service

### <a name="attestation-mechanism"></a>Nachweismechanismus

Bei [Device Provisioning Service](#device-provisioning-service) handelt es sich beim Nachweismechanismus um die Methode, die zur Bestätigung der Identität eines [Geräts](#device) verwendet wird. Der Nachweismechanismus wird für eine [Registrierung](#enrollment) konfiguriert.

Nachweismechanismen sind X.509-Zertifikate, Trusted Platform [Modules](#module) und symmetrische Schlüssel.

Gilt für: Device Provisioning Service

### <a name="automatic-deployment"></a>Automatische Bereitstellung

Ein Feature von [IoT Edge](#iot-edge) zur Konfiguration einer Zielgruppe von [IoT Edge-Geräten](#iot-edge-device) für die Ausführung mehrerer IoT Edge-[Module](#module). Jede Bereitstellung stellt kontinuierlich sicher, dass alle [Geräte](#device), die der [Zielbedingung](#target-condition) entsprechen, die angegebene Gruppe von Modulen ausführen. Das gilt auch, wenn neue Geräte erstellt oder geändert werden, um der Zielbedingung zu entsprechen. Jedes IoT Edge-Gerät erhält nur die Bereitstellung mit der höchsten Priorität, deren Zielbedingung es erfüllt.

[Weitere Informationen](../iot-edge/module-deployment-monitoring.md)

Gilt für: IoT Edge

### <a name="automatic-device-configuration"></a>Automatische Gerätekonfiguration

Ein Feature von [IoT Hub](#iot-hub), mit dem Ihr [Lösungs](#solution)-Back-End einer Gruppe von [Gerätezwillingen](#device-twin) die [gewünschten Eigenschaften](#desired-properties) zuzuweisen und den [Gerätestatus](#device) anhand von Systemmetriken und benutzerdefinierten Metriken melden kann.

[Weitere Informationen](../iot-hub/iot-hub-automatic-device-management.md)

Gilt für: IoT Hub

### <a name="automatic-device-management"></a>Automatische Geräteverwaltung

Ein Feature von [IoT Hub](#iot-hub) zur Automatisierung vieler der repetitiven und komplexen Aufgaben im Zusammenhang mit der Verwaltung eines umfangreichen [Gerätebestands](#device) über den gesamten Lebenszyklus. Mit diesem Feature können Sie mehrere Geräten auf der Grundlage ihrer [Eigenschaften](#properties) als Ziel festlegen, eine [gewünschte Konfiguration](#desired-configuration) definieren und IoT Hub die Geräte aktualisieren lassen, sobald sie in den entsprechenden Bereich fallen.

Umfasst [automatische Gerätekonfigurationen](../iot-hub/iot-hub-automatic-device-management.md) und [automatische IoT Edge-Bereitstellungen](../iot-edge/how-to-deploy-at-scale.md).

Gilt für: IoT Hub

### <a name="azure-certified-device-program"></a>Azure Certified Device-Programm

[Azure Certified Device](#device) ist ein kostenloses Programm, mit dem Sie Ihre IoT-Geräte, die für die Ausführung in Azure erstellt wurden, hervorheben, zertifizieren und bewerben können.

[Weitere Informationen](../certification/overview.md)

Gilt für: IoT Hub, IoT Central

### <a name="azure-digital-twins"></a>Azure Digital Twins

Ein PaaS-Angebot (Platform as a Service) für die Erstellung digitaler Darstellungen von realen Gegenständen, Orten, Geschäftsprozessen und Personen. Erstellen Sie Zwillingsgraphen, die ganze Umgebungen darstellen, und gewinnen Sie daraus Erkenntnisse, um Produkte zu verbessern, Vorgänge und Kosten zu optimieren und bahnbrechende Kundenerfahrungen zu kreieren.

[Weitere Informationen](../digital-twins/overview.md)

Gilt für: Digital Twins

### <a name="azure-digital-twins-instance"></a>Azure Digital Twins-Instanz

Eine einzelne Instanz des [Azure Digital Twins](#azure-digital-twins)-Diensts im Abonnement eines Kunden. [Azure Digital Twins](#digital-twin) bezieht sich auf den Azure-Dienst als Ganzes, während Ihre Azure Digital Twins-*Instanz* Ihre persönliche Azure Digital Twins-Ressource darstellt.

Gilt für: Digital Twins

### <a name="azure-iot-explorer"></a>Azure IoT-Explorer

Sie können mit diesem Tool die [Telemetriedaten](#telemetry) anzeigen, die das [Gerät](#device) sendet, [Geräteeigenschaften](#properties) bearbeiten und [Befehle](#command) aufrufen. Außerdem können Sie den Explorer zur Interaktion mit Ihren Geräten, zum Testen Ihrer Geräte und zur Verwaltung von [IoT Plug & Play-Geräten](#iot-plug-and-play-device) verwenden.

[Weitere Informationen](https://github.com/Azure/azure-iot-explorer)

Gilt für: IoT Hub, Geräteentwickler

### <a name="azure-iot-tools"></a>Azure IoT-Tools

Eine plattformübergreifende Open-Source-Erweiterung für Visual Studio Code, die Sie bei der Verwaltung von [Azure IoT Hub](#iot-hub) und [Geräten](#device) in VS Code unterstützt. Mit Azure IoT Tools können IoT-Entwickler ganz einfach IoT-Projekte in VS Code entwickeln.

Gilt für: Iot Hub, IoT Edge, IoT Central, Geräteentwickler

### <a name="azure-iot-device-sdks"></a>Azure IoT-Geräte-SDKs

Diese SDKs sind für mehrere Programmiersprachen verfügbar und ermöglichen das Erstellen von [Geräte-Apps](#device-app), die mit einem [IoT-Hub](#iot-hub) oder einer IoT Central-Anwendung interagieren.

[Weitere Informationen](../iot-develop/about-iot-sdks.md)

Gilt für: IoT Hub, IoT Central, Geräteentwickler

### <a name="azure-iot-service-sdks"></a>Azure IoT-Dienst-SDKs

Diese SDKs sind für mehrere Programmiersprachen verfügbar und ermöglichen das Erstellen von [Back-End-Apps](#back-end-app), die mit einem [IoT-Hub](#iot-hub) interagieren.

[Weitere Informationen](/azure/iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks)

Gilt für: IoT Hub

## <a name="b"></a>B

### <a name="back-end-app"></a>Back-End-App

Im Kontext von [IoT Hub](#iot-hub) ist dies eine App, mit der eine Verbindung mit einem der dienstseitigen [Endpunkte](#endpoint) eines IoT-Hubs hergestellt wird. Mit einer Back-End-App werden beispielsweise [Gerät-zu-Cloud](#device-to-cloud)-Nachrichten (D2C-Nachrichten) abgerufen, oder sie wird zum Verwalten der [Identitätsregistrierung](#identity-registry) eingesetzt. Eine Back-End-App wird normalerweise in der Cloud ausgeführt, aber in vielen Tutorials handelt es sich bei den Back-End-Apps zur Vereinfachung um Konsolen-Apps, die auf dem lokalen Entwicklungscomputer ausgeführt werden.

Gilt für: IoT Hub

### <a name="built-in-endpoints"></a>Integrierte Endpunkte

[Endpunkte](#endpoint), die in [IoT Hub](#iot-hub) integriert sind. Jeder IoT-Hub umfasst z. B. einen mit Event Hubs kompatiblen integrierten Endpunkt.

Gilt für: IoT Hub

## <a name="c"></a>C

### <a name="cloud-gateway"></a>Cloudgateway

Eine in der Cloud gehostete App, mit der Verbindungen für [Geräte](#device) hergestellt werden, für die eine direkte Verbindung mit [IoT Hub](#iot-hub) oder IoT Central nicht möglich ist. Ein [Cloudgateway](#gateway) wird in der Cloud gehostet, während ein [Bereichsgateway](#field-gateway) lokal auf Ihren Geräten ausgeführt wird. Ein typischer Anwendungsfall für ein Cloudgateway ist die Implementierung der Protokollübersetzung für Ihre Geräte.

Gilt für: IoT Hub, IoT Central

### <a name="cloud-property"></a>Cloudeigenschaft

Ein Feature in IoT Central, mit dem Sie Metadaten von [Geräten](#device) in der IoT Central-Anwendung speichern können. [Cloudeigenschaften](#properties) sind in der [Gerätevorlage](#device-template) definiert, aber sie sind nicht Teil des [Gerätemodells](#device-model). Cloudeigenschaften werden nie mit einem Gerät synchronisiert.

Gilt für: IoT Central

### <a name="cloud-to-device"></a>Cloud-zu-Gerät (C2D)

Nachrichten, die von einem [IoT-Hub](#iot-hub) an ein verbundenes [Gerät](#device) gesendet werden. Diese Nachrichten sind häufig [Befehle](#command), mit denen das Gerät angewiesen wird, eine Aktion auszuführen.

Gilt für: IoT Hub

### <a name="command"></a>Befehl

In IoT Plug & Play stellen die in einer [Schnittstelle](#interface) definierten Befehle Methoden dar, die auf dem [digitalen Zwilling](#digital-twin) ausgeführt werden können. Dies kann beispielsweise ein Befehl zum Neustart eines [Geräts](#device) sein. In IoT Central werden Befehle in der [Gerätevorlage](#device-template) definiert.

Gilt für: IoT Hub, IoT Central, Geräteentwickler

### <a name="component"></a>Komponente

In IoT Plug & Play und [Azure Digital Twins](#azure-digital-twins) können Sie die [Schnittstelle](#interface) eines [Modells](#model) als Assembly aus anderen Schnittstellen erstellen. Ein [Gerätemodell](#device-model) kann mehrere Schnittstellen als Komponenten kombinieren. Ein Modell könnte beispielsweise eine Schalterkomponente und eine Thermostatkomponente enthalten. Mehrere Komponenten in einem Modell können auch denselben Schnittstellentyp verwenden. Beispielsweise könnte ein Modell zwei Thermostatkomponenten enthalten.

Gilt für: IoT Hub, Digital Twins, Geräteentwickler

### <a name="configuration"></a>Konfiguration

Im Kontext der [automatischen Gerätekonfiguration](#automatic-device-configuration) definiert in [IoT Hub](#iot-hub) definiert dies die [gewünschte Konfiguration](#desired-configuration) für eine Gruppe von [Gerätezwillingen](#device) und bietet einen Satz von Metriken zum Melden von Status und Fortschritt.

Gilt für: IoT Hub

### <a name="connection-string"></a>Verbindungszeichenfolge

Wird im App-Code verwendet, um die Informationen zu kapseln, die zum Herstellen der Verbindung mit einem [Endpunkt](#endpoint) erforderlich sind. Eine Verbindungszeichenfolge enthält üblicherweise die Adresse des Endpunkts und Sicherheitsinformationen, aber das Format der Verbindungszeichenfolge variiert für die verschiedenen Dienste. Im Zusammenhang mit dem [IoT Hub](#iot-hub)-Dienst gibt es zwei Arten von Verbindungszeichenfolgen:

- *Geräteverbindungszeichenfolgen* ermöglichen es [Geräten](#device), eine Verbindung mit den geräteseitigen Endpunkten eines IoT-Hubs herzustellen.
- *IoT Hub-Verbindungszeichenfolgen* ermöglichen es [Back-End-Apps](#back-end-app), eine Verbindung mit den dienstseitigen Endpunkten eines IoT-Hubs herzustellen.

Gilt für: IoT Hub, Geräteentwickler

### <a name="custom-endpoints"></a>Benutzerdefinierte Endpunkte

Benutzerdefinierte [Endpunkte](#endpoint) auf einem [IoT-Hub](#routing-rule) zur Weiterleitung der von einer [Routingregel](#iot-hub) gesendeten Nachrichten. Diese Endpunkte stellen eine direkte Verbindung mit einem Event Hub, einer Service Bus-Warteschlange oder einem Service Bus-Thema her.

Gilt für: IoT Hub

### <a name="custom-gateway"></a>Benutzerdefiniertes Gateway

Ermöglicht Konnektivität für [Geräte](#device), für die eine direkte Verbindung mit [IoT Hub](#iot-hub) oder IoT Central nicht möglich ist. Sie können [Azure IoT Edge](#iot-edge) zum Erstellen von benutzerdefinierten [Gateways](#gateway) verwenden, bei denen benutzerdefinierte Logik implementiert wird, um Nachrichten und benutzerdefinierte Protokollkonvertierungen zu verarbeiten und andere Verarbeitungsvorgänge durchzuführen.

Gilt für: IoT Hub, IoT Central

## <a name="d"></a>D

### <a name="default-component"></a>Standardkomponente

Alle [Modelle](#model) für [IoT Plug & Play-Geräte](#iot-plug-and-play-device) weisen eine [Standardkomponente](#component) auf. Ein einfaches [Gerätemodell](#device-model) verfügt nur über eine Standardkomponente. Ein solches Modell wird auch als [Gerät](#device) ohne Komponente bezeichnet. Bei einem komplexeren Modell sind mehrere Komponenten unterhalb der Standardkomponente geschachtelt.

Gilt für: IoT Hub, IoT Central, Geräteentwickler

### <a name="deployment-manifest"></a>Bereitstellungsmanifest

Ein JSON-Dokument in [IoT Edge](#iot-edge), das die [Konfigurationsdaten](#configuration) für einen oder mehrere [Modulzwillinge](#module-twin) eines [IoT Edge-Geräts](#iot-edge-device) enthält.

Gilt für: IoT Edge, IoT Central

### <a name="desired-configuration"></a>Gewünschte Konfiguration

Im Kontext eines [Gerätezwillings](#device-twin) bezieht sich die gewünschte [Konfiguration](#configuration) auf den vollständigen Satz von [Eigenschaften](#properties) und Metadaten im Gerätezwilling, die mit dem [Gerät](#device) synchronisiert werden sollen.

Gilt für: IoT Hub

### <a name="desired-properties"></a>Gewünschte Eigenschaften

Im Kontext eines [Gerätezwillings](#device-twin) handelt es sich bei gewünschten [Eigenschaften](#properties) um einen Unterabschnitt, der zusammen mit [gemeldeten Eigenschaften](#reported-properties) verwendet wird, um die [Konfiguration](#configuration) oder den Zustand des [Geräts](#device) zu synchronisieren. Gewünschte Eigenschaften können nur von einer [Back-End-App](#back-end-app) festgelegt werden und werden von der [Geräte-App](#device-app) erkannt. In IoT Central wird der Begriff „schreibbare Eigenschaften“ verwendet.

Gilt für: IoT Hub

### <a name="device"></a>Sicherungsmedium

Im Kontext von IoT ist ein Gerät in der Regel eine kleines, eigenständiges Datenverarbeitungsgerät, das Daten sammeln oder andere Geräte steuern kann. Ein Gerät kann beispielsweise ein Umweltüberwachungsgerät oder ein Regler für Bewässerungs- und Lüftungsanlagen in einem Gewächshaus sein. Der Gerätekatalog enthält eine Liste der zertifizierten Geräte.

Gilt für: IoT Hub, IoT Central, IoT Edge, Device Provisioning Service, Geräteentwickler

### <a name="device-provisioning-service"></a>Device Provisioning Service

Ein Hilfsdienst für [IoT Hub](#iot-hub) und IoT Central für die [Gerätebereitstellung](#device-provisioning) ohne manuelles Eingreifen. Mit DPS können Sie Millionen von [Geräten](#device) auf sichere und skalierbare Weise bereitstellen.

Gilt für: IoT Hub, Device Provisioning Service, IoT Central

### <a name="device-rest-api"></a>Geräte-REST-API

Eine REST-API für die Verwendung auf einem [Gerät](#device), um [D2C](#device-to-cloud)-Nachrichten an einen [IoT-Hub](#iot-hub) zu senden und [C2D](#cloud-to-device)-Nachrichten von einem IoT-Hub zu empfangen. In der Regel sollten Sie eines der übergeordneten [Azure IoT-Geräte-SDKs](#azure-iot-device-sdks) verwenden.

[Weitere Informationen](/rest/api/iothub/device)

Gilt für: IoT Hub

### <a name="device-app"></a>Geräte-App

Eine Geräte-App wird auf Ihrem [Gerät](#device) ausgeführt und führt die Kommunikation mit Ihrem [IoT-Hub](#iot-hub) oder Ihrer IoT Central-Anwendung durch. Normalerweise nutzen Sie eines der [Azure IoT-Geräte-SDKs](#azure-iot-device-sdks), wenn Sie eine Geräte-App implementieren.

Gilt für: IoT Hub, IoT Central, Geräteentwickler

### <a name="device-builder"></a>Geräteentwickler

Die Person, die für die Erstellung des Codes verantwortlich ist, der auf Ihren [Geräten](#device) ausgeführt werden soll. Geräteentwickler verwenden normalerweise eines der [Azure IoT-Geräte-SDKs](#azure-iot-device-sdks) zum Implementieren des Geräteclients. Ein Geräteentwickler verwendet ein [Gerätemodell](#device-model) und [Schnittstellen](#interface) beim Implementieren von Code zur Ausführung auf einem [IoT Plug & Play-Gerät](#iot-plug-and-play-device).

Gilt für: Iot Hub, IoT Central, IoT Edge, Geräteentwickler

### <a name="device-identity"></a>Geräteidentität

Ein eindeutiger Bezeichner, der jedem [Gerät](#device) zugewiesen wird, das in der [IoT Hub](#iot-hub)-[Identitätsregistrierung](#identity-registry) oder in einer IoT Central-Anwendung registriert ist.

Gilt für: IoT Hub, IoT Central

### <a name="device-management"></a>Geräteverwaltung

Die Geräteverwaltung umfasst den vollständigen Lebenszyklus, der der Verwaltung der [Geräte](#device) in Ihrer IoT-[Lösung](#solution) zugeordnet ist, z. B. Planen, Bereitstellen, Konfigurieren, Überwachen und Ausmustern.

Gilt für: IoT Hub, IoT Central

### <a name="device-model"></a>Gerätemodell

Eine Beschreibung der Funktionen eines [Geräts](#device) in der [Digital Twins Definition Language](#digital-twins-definition-language). Zu den Funktionen gehören [Telemetrie](#telemetry), [Eigenschaften](#properties) und [Befehle](#command).

[Weitere Informationen](../iot-develop/concepts-modeling-guide.md)

Gilt für: IoT Hub, IoT Central, Geräteentwickler, Digital Twins

### <a name="device-provisioning"></a>Gerätebereitstellung

Der Prozess des Hinzufügens der ersten [Gerätedaten](#device) in den Speichern in Ihrer [Lösung](#solution). Damit ein neues Gerät eine Verbindung mit Ihrem Hub herstellen kann, müssen Sie der [IoT Hub](#iot-hub)-[Identitätsregistrierung](#identity-registry) eine neue Geräte-ID und Schlüssel hinzufügen. Der [Device Provisioning Service](#device-provisioning-service) kann Geräte automatisch in einem IoT-Hub oder einer IoT Central-Anwendung bereitstellen.

Gilt für: IoT Hub, IoT Central, Device Provisioning Service

### <a name="device-template"></a>Gerätevorlage

In IoT Central ist eine [Gerätevorlage](#device) eine Blaupause, die die Merkmale und das Verhalten eines Gerätetyps definiert, der eine Verbindung mit Ihrer Anwendung herstellt.

Beispielsweise definiert die Gerätevorlage die [Telemetriedaten](#telemetry), die ein Gerät sendet, sodass IoT Central Visualisierungen mit den richtigen Einheiten und Datentypen erstellen kann. Ein [Gerätemodell](#device-model) ist Teil der Gerätevorlage.

Gilt für: IoT Central

### <a name="device-twin"></a>Gerätezwilling

Ein [Gerätezwilling](#device) ist ein JSON-Dokument, in dem Informationen zum Gerätezustand gespeichert werden, z. B. Metadaten, [Konfigurationen](#configuration) und Bedingungen. [IoT Hub](#iot-hub) speichert einen Gerätezwilling für jedes Gerät, das Sie für IoT Hub bereitstellen. Mit Gerätezwillingen können Sie Gerätebedingungen und Konfigurationen zwischen dem Gerät und dem [Lösungs](#solution)-Back-End synchronisieren. Sie können Gerätezwillinge abfragen, um bestimmte Geräte sowie den Status von Vorgängen mit langer Ausführungsdauer zu ermitteln.

Gilt für: IoT Hub

### <a name="device-to-cloud"></a>Gerät-zu-Cloud (D2C)

Bezieht sich auf Nachrichten, die von einem verbundenen [Gerät](#device) an [IoT Hub](#iot-hub) oder IoT Central gesendet werden.

Gilt für: IoT Hub

### <a name="digital-twins-definition-language"></a>Digital Twins Definition Language

Eine JSON-LD-Sprache zum Beschreiben von [Modellen](#model) und [Schnittstellen](#interface) für [IoT Plug & Play-Geräte](#iot-plug-and-play-device) und [Azure Digital Twins](#azure-digital-twins)-Entitäten. Die Sprache ermöglicht es der IoT-Plattform und IoT-[Lösungen](#solution), die Semantik der Entität zu verwenden.

[Weitere Informationen](https://github.com/Azure/opendigitaltwins-dtdl)

Gilt für: IoT Hub, IoT Central, Digital Twins

### <a name="digital-twin"></a>Digitaler Zwilling

Ein digitaler Zwilling ist eine Sammlung digitaler Daten, die ein physisches Objekt repräsentieren. Änderungen am physischen Objekt spiegeln sich im digitalen Zwilling wider. In einigen Szenarien können Sie den digitalen Zwilling verwenden, um das physische Objekt zu bearbeiten. Der [Azure Digital Twins-Dienst](../digital-twins/index.yml) verwendet [Modelle](#model), die in der [Digital Twins Definition Language](#digital-twins-definition-language) ausgedrückt werden, um digitale Zwillinge von [physischen Geräten](#physical-device) oder abstrakte Geschäftskonzepte darzustellen und so eine Vielzahl verschiedener cloudbasierter [Lösungen](#solution) für digitale Zwillinge zu ermöglichen. Ein [IoT Plug & Play](../iot-develop/index.yml)-[Gerät](#device) besitzt einen digitalen Zwilling, der durch ein DTDL-[Gerätemodell](#device-model) (Digital Twins Definition Language) beschrieben wird.

Gilt für: IoT Hub, IoT Central, Digital Twins, Geräteentwickler

### <a name="digital-twin-change-events"></a>Änderungsereignisse bei digitalen Zwillingen

Wenn ein [IoT Plug & Play-Gerät](#iot-plug-and-play-device) mit einem [IoT-Hub](#iot-hub) verbunden wird, kann der Hub die Routingfunktionen des Geräts nutzen, um Benachrichtigungen zu Änderungen bei [digitalen Zwillingen](#digital-twin) zu senden. Die Datenexportfunktion von IoT Central kann auch Änderungsereignisse von digitalen Zwillingen an andere Dienste weiterleiten. Beispiel: Wenn sich ein Eigenschaftswert auf einem [Gerät](#device) ändert, kann IoT Hub eine Benachrichtigung an einen [Endpunkt](#endpoint) wie z. B. einen Event Hub senden.

Gilt für: IoT Hub, IoT Central

### <a name="digital-twin-graph"></a>Digital Twin-Graph

Beim [Azure Digital Twins](#azure-digital-twins)-Dienst können Sie [digitale Zwillinge](#digital-twin) mit [Beziehungen](#relationship) verbinden, um Wissensgraphen zu erstellen, die Ihre gesamte physische Umgebung digital darstellen. Eine einzelne [Azure Digital Twins-Instanz](#azure-digital-twins-instance) kann viele nicht verbundene Graphen oder einen einzelnen verbundenen Graphen hosten.

Gilt für: IoT Hub

### <a name="direct-method"></a>Direkte Methode

Eine Möglichkeit zum Ausführen einer Methode auf einem [Gerät](#device), indem eine API auf Ihrem [IoT-Hub](#iot-hub) aufgerufen wird.

Gilt für: IoT Hub

### <a name="downstream-service"></a>Downstreamdienst

Ein relativer Begriff für Dienste, die Daten aus dem aktuellen Kontext empfangen. Beispielsweise gelten Time Series Insights im Kontext von [Azure Digital Twins](#azure-digital-twins) als Downstreamdienst, wenn Ihre Daten so konfiguriert sind, dass sie von [Azure Digital Twins](#digital-twin) zu Time Series Insights fließen.

Gilt für: IoT Hub, IoT Central, Digital Twins

## <a name="e"></a>E

### <a name="endpoint"></a>Endpunkt

Eine benannte Darstellung eines Datenroutingdiensts, der Daten von anderen Diensten empfangen kann.

Ein [IoT-Hub](#iot-hub) macht mehrere Endpunkte verfügbar, mit deren Hilfe Ihre Apps eine Verbindung mit dem IoT-Hub herstellen können. Es gibt geräteseitige Endpunkte, die [Geräten](#device) das Durchführen von Vorgängen ermöglichen, z. B. das Senden von [D2C](#device-to-cloud)-Nachrichten. Es gibt dienstseitige Verwaltungsendpunkte, die [Back-End-Apps](#back-end-app) das Durchführen von Vorgängen ermöglichen, z. B. die Verwaltung der [Geräteidentität](#device-identity). Es gibt dienstseitige [integrierte Endpunkte](#built-in-endpoints) zum Lesen von D2C-Nachrichten. Sie können [benutzerdefinierte Endpunkte](#custom-endpoints) erstellen, um von einer [Routingregel](#routing-rule) versendete D2C-Nachrichten zu empfangen.

Gilt für: IoT Hub

### <a name="enrollment"></a>Anmeldung

Beim [Device Provisioning Service](#device-provisioning-service) ist die Registrierung der Eintrag einzelner [Geräte](#device) oder Gerätegruppen, die über die automatische Bereitstellung bei einem [verknüpften IoT-Hub](#linked-iot-hub) registriert werden können.

Gilt für: IoT Hub, IoT Central, Device Provisioning Service

### <a name="enrollment-group"></a>Registrierungsgruppe

Beim [Device Provisioning Service](#device-provisioning-service) und bei IoT Central wird mit einer [Registrierungsgruppe](#enrollment) eine Gruppe von [Geräten](#device) bezeichnet, die gemeinsam einen [Nachweismechanismus](#attestation-mechanism) für X.509 oder einen symmetrischen Schlüssel verwenden.

Gilt für: IoT Hub, Device Provisioning Service, IoT Central

### <a name="event-hubs-compatible-endpoint"></a>Event Hub-kompatibler Endpunkt

Ein [IoT Hub](#iot-hub)-[Endpunkt](#endpoint), mit dem das Event Hubs-kompatible Lesen von [Gerätenachrichten](#device) ermöglicht wird, die an den Hub gesendet werden. Event Hub-kompatible Methoden sind beispielsweise das Verwenden von [Event Hubs-SDKs](../event-hubs/event-hubs-programming-guide.md) und [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md).

Gilt für: IoT Hub

### <a name="event-handler"></a>Ereignishandler

Ein Prozess, der durch das Eintreffen eines Ereignisses ausgelöst wird. Eine Möglichkeit, Ereignishandler zu erstellen, besteht darin, einer Azure-Funktion Code zur Ereignisverarbeitung hinzuzufügen und Daten über [Endpunkte](#endpoint) und [Ereignisrouting](#event-routing) über diesen zu senden.

Gilt für: IoT Hub

### <a name="event-routing"></a>Ereignisrouting

Der Prozess zum Senden von Ereignissen und zugehörigen Daten von einem [Gerät](#device) oder Dienst zum [Endpunkt](#endpoint) eines anderen Geräts oder Diensts.

Bei [IoT Hub](#iot-hub) können Sie [Routingregeln](#routing-rule) definieren, die beschreiben, wie Nachrichten gesendet werden sollen. Bei [Azure Digital Twins](#azure-digital-twins) sind Ereignisrouten Entitäten, die zu diesem Zweck erstellt wurden. [Azure Digital Twins](#digital-twin)-Ereignisrouten können Filter enthalten, um die Typen von Ereignissen einzuschränken, die an den jeweiligen Endpunkt gesendet werden.

Gilt für: IoT Hub, Digital Twins

## <a name="f"></a>F

### <a name="field-gateway"></a>Bereichsgateway

Ermöglicht Verbindungen für [Geräte](#device), die keine direkte Verbindung mit [IoT Hub](#iot-hub) herstellen können. Es wird meist lokal mit Ihren Geräten bereitgestellt.

Gilt für: IoT Hub, IoT Central

## <a name="g"></a>G

### <a name="gateway"></a>Gateway

Mit einem Gateway können Verbindungen für [Geräte](#device) hergestellt werden, für die eine direkte Verbindung mit [IoT Hub](#iot-hub) nicht möglich ist. Siehe auch [Bereichsgateway](#field-gateway), [Cloudgateway](#cloud-gateway) und [benutzerdefiniertes Gateway](#custom-gateway).

Gilt für: IoT Hub, IoT Central

### <a name="gateway-device"></a>Gatewaygerät

Ein Beispiel für ein [Bereichsgateway](#field-gateway). Ein [Gatewaygerät](#gateway) kann ein Standard-IoT-[Gerät](#device) oder ein [IoT Edge-Gerät](#iot-edge-device) sein.

Über ein Gatewaygerät können nachgeschaltete Geräte, die keine direkte Verbindung mit [IoT Hub](#iot-hub) herstellen können, Verbindungen herstellen.

Gilt für: IoT Hub, IoT Central, IoT Edge

## <a name="h"></a>H

### <a name="hardware-security-module"></a>Hardwaresicherheitsmodul

Wird für die sichere, hardwarebasierte Speicherung von [Gerätegeheimnissen](#device) verwendet. Dies ist die sicherste Form von geheimem Speicher für ein Gerät. Ein [Hardwaresicherheitsmodul](#module) kann sowohl X.509-Zertifikate als auch symmetrische Schlüssel speichern. Beim [Device Provisioning Service](#device-provisioning-service) kann ein Hardwaresicherheitsmodul (HSM) für einen [Nachweismechanismus](#attestation-mechanism) verwendet werden.

Gilt für: IoT Hub, Geräteentwickler, Device Provisioning Service

## <a name="i"></a>I

### <a name="id-scope"></a>ID-Bereich

Ein eindeutiger Wert, der einer [DPS-Instanz (Device Provisioning Service)](#device-provisioning-service) bei ihrer Erstellung zugewiesen wird.

IoT Central-Anwendungen nutzen DPS-Instanzen und machen den ID-Bereich über die IoT Central-Benutzeroberfläche verfügbar.

Gilt für: IoT Hub, IoT Central, Device Provisioning Service

### <a name="identity-registry"></a>Identitätsregistrierung

Eine integrierte [Komponente](#component) von [IoT-Hubs](#iot-hub), in der Informationen zu den einzelnen [Geräten](#device) gespeichert werden, die eine Verbindung mit dem Hub herstellen dürfen.

Gilt für: IoT Hub

### <a name="individual-enrollment"></a>Individuelle Registrierung

Identifiziert ein einzelnes [Gerät](#device), das der [Device Provisioning Service](#device-provisioning-service) auf einem [IoT-Hub](#iot-hub) bereitstellen kann.

Gilt für: Iot Hub, Device Provisioning Service

### <a name="interface"></a>Schnittstelle

In IoT Plug & Play beschreibt eine Schnittstelle verwandte Funktionen, die durch ein [IoT Plug & Play-Gerät](#iot-plug-and-play-device) oder einen [digitalen Zwilling](#digital-twin) implementiert werden. Sie können Schnittstellen in verschiedenen [Gerätemodellen](#device-model) wiederverwenden. Wenn eine Schnittstelle in einem [Gerätemodell](#model) verwendet wird, definiert sie eine [Komponente](#component) des [Geräts](#device). Ein einfaches Gerät enthält nur eine Standardschnittstelle.

Bei [Azure Digital Twins](#azure-digital-twins) kann das Codeelement auf oberster Ebene in einer [DTDL](#digital-twins-definition-language)-Modelldefinition (Digital Twins Definition Language) als *Schnittstelle* bezeichnet werden.

Gilt für: Geräteentwickler, Digital Twins

### <a name="iot-edge"></a>IoT Edge

Ein Dienst mit den zugehörigen Clientbibliotheken und der Runtime, der die cloudgesteuerte Bereitstellung von Azure-Diensten und [lösungsspezifischem](#solution) Code auf lokalen [Geräten](#device) ermöglicht. [IoT Edge-Geräte](#iot-edge-device) können Daten anderer Geräte aggregieren und Berechnungen und Analysen durchführen, bevor sie die Daten an die Cloud senden.

[Weitere Informationen](../iot-edge/index.yml)

Gilt für: IoT Edge

### <a name="iot-edge-agent"></a>IoT Edge-Agent

Die für die Bereitstellung und Überwachung von [Modulen](#module) zuständige Komponente der [IoT Edge-Runtime](#iot-edge-runtime).

Gilt für: IoT Edge

### <a name="iot-edge-device"></a>IoT Edge-Gerät

Ein [Gerät](#device), das containerisierte [IoT Edge](#iot-edge)-[Module](#module) verwendet, um Azure-Dienste, Drittanbieterdienste oder Ihren Code auszuführen. Auf dem Gerät verwaltet die [IoT Edge-Runtime](#iot-edge-runtime) die Module. Sie können ein IoT Edge-Gerät remote über die Cloud überwachen und verwalten.

Gilt für: IoT Edge

### <a name="iot-edge-hub"></a>IoT Edge-Hub

Der Teil der [IoT Edge-Runtime](#iot-edge-runtime), der für die Kommunikation zwischen [Modulen](#module) sowie für die Upstream- und Downstreamkommunikation zuständig ist.

Gilt für: IoT Edge

### <a name="iot-edge-runtime"></a>IoT Edge-Laufzeit

Enthält alles, was Microsoft für die Installation auf einem [IoT Edge-Gerät](#iot-edge-device) bereitstellt. Sie enthält den Edge-Agent, den Edge-Hub und den [IoT Edge](#iot-edge)-Sicherheitsdaemon.

Gilt für: IoT Edge

### <a name="iot-hub"></a>IoT Hub

Ein vollständig verwalteter Azure-Dienst, der eine zuverlässige und sichere bidirektionale Kommunikation zwischen Millionen von [Geräten](#device) und einem [Lösungs](#solution)-Back-End ermöglicht. Weitere Informationen finden Sie unter [Was ist Azure IoT Hub?](../iot-hub/about-iot-hub.md). Mit Ihrem Azure-Abonnement können Sie IoT-Hubs für die Verarbeitung Ihrer IoT-Messagingworkloads erstellen.

[Weitere Informationen](../iot-hub/about-iot-hub.md)

Gilt für: IoT Hub

### <a name="iot-hub-resource-rest-api"></a>IoT Hub-Ressourcen-REST-API

Eine API, die Sie für die Verwaltung der [IoT-Hubs](#iot-hub) in Ihrem Azure-Abonnement verwenden können und die Vorgänge wie das Erstellen, Aktualisieren und Löschen von Hubs ermöglicht.

Gilt für: IoT Hub

### <a name="iot-hub-metrics"></a>IoT Hub-Metriken

Ein Feature im Azure-Portal, mit dem Sie den Status Ihrer [IoT-Hubs](#iot-hub) überwachen können. Auf der Grundlage von IoT Hub-Metriken können Sie die allgemeine Integrität eines IoT-Hubs und der mit ihm verbundenen [Geräte](#device) beurteilen.

Gilt für: IoT Hub

### <a name="iot-hub-query-language"></a>IoT Hub-Abfragesprache

Eine SQL-ähnliche Sprache für [IoT Hub](#iot-hub), mit der Sie Ihre [Aufträge](#job), [digitalen Zwillinge](#digital-twin) und [Gerätezwillinge](#device-twin) abfragen können.

Gilt für: IoT Hub

### <a name="iot-plug-and-play-bridge"></a>IoT Plug & Play-Brücke

Eine Open-Source-Anwendung, die es vorhandenen, an Windows- oder Linux-[Gateways](#gateway) angefügten Sensoren und Peripheriegeräten ermöglicht, eine Verbindung als [IoT Plug & Play-Geräte](#iot-plug-and-play-device) herzustellen.

Gilt für: IoT Hub, Geräteentwickler, IoT Central

### <a name="iot-plug-and-play-conventions"></a>IoT Plug & Play-Konventionen

Eine Reihe von Konventionen, die IoT-[Geräte](#device) einhalten müssen, um Daten mit einer [Lösung](#solution) austauschen zu können.

Gilt für: IoT Hub, IoT Central, Geräteentwickler

### <a name="iot-plug-and-play-device"></a>IoT Plug & Play-Gerät

Ein kleines, eigenständiges [Computergerät](#device), das Daten sammelt oder andere Geräte steuert. Auf einem solchen Gerät wird Software oder Firmware ausgeführt, die ein [Gerätemodell](#device-model) implementiert. Ein IoT Plug & Play-Gerät könnte z.B. ein Gerät zur Überwachung von Umweltdaten oder ein Regler für ein intelligentes Bewässerungssystem in der Landwirtschaft sein. Ein IoT Plug & Play-Gerät kann direkt oder als [IoT Edge](#iot-edge)-[Modul](#module) implementiert werden.

Gilt für: IoT Hub, IoT Central, Geräteentwickler

### <a name="iot-extension-for-azure-cli"></a>IoT-Erweiterung für die Azure CLI

Eine Erweiterung für die Azure-Befehlszeilenschnittstelle. Mit der Erweiterung können Sie Ihre [Geräte](#device) in der [Identitätsregistrierung](#identity-registry) verwalten, Gerätenachrichten senden und empfangen und [IoT Hub](#iot-hub)-Vorgänge überwachen.

[Weitere Informationen](/cli/azure/azure-cli-reference-for-IoT)

Gilt für: IoT Hub, IoT Central, IoT Edge, Device Provisioning Service, Geräteentwickler

## <a name="j"></a>J

### <a name="job"></a>Auftrag

Im Kontext von [IoT Hub](#iot-hub) können Sie mit Aufträgen Aktivitäten für eine Gruppe von [Geräten](#device) planen und nachverfolgen, die bei Ihrem IoT-Hub registriert sind. Aktivitäten sind beispielsweise das Aktualisieren der [gewünschten Eigenschaften](#desired-properties) von [Gerätezwillingen](#device-twin) und ihrer [Tags](#tag) sowie das Aufrufen [direkter Methoden](#direct-method). Außerdem nutzt IoT Hub Aufträge zum Importieren und Exportieren aus der [Identitätsregistrierung](#identity-registry).

Im Kontext von IoT Central können Sie Ihre verbundenen Geräte mit Aufträgen gesammelt verwalten und für diese [Eigenschaften](#properties) festlegen und [Befehle](#command) aufrufen. IoT Central-Aufträge ermöglichen auch das Aktualisieren von Cloudeigenschaften in einem Massenvorgang.

Gilt für: IoT Hub, IoT Central

## <a name="l"></a>L

### <a name="leaf-device"></a>Blattgerät

Ein [Gerät](#device) ohne Verbindungen zu nachgeschalteten Geräten. In der Regel sind Blattgeräte mit einem [Gatewaygerät](#gateway-device) verbunden.

Gilt für: IoT Edge, IoT Central, Geräteentwickler

### <a name="lifecycle-event"></a>Lebenszyklusereignis

Bei [Azure Digital Twins](#azure-digital-twins) wird dieser Typ von Ereignis ausgelöst, wenn ein Datenelement, z. B. ein [digitaler Zwilling](#digital-twin), eine [Beziehung](#relationship) oder ein [Ereignishandler](#event-handler), erstellt oder aus der [Azure Digital Twins-Instanz](#azure-digital-twins-instance) gelöscht wird.

Gilt für: Digital Twins, IoT Hub, IoT Central

### <a name="linked-iot-hub"></a>Verknüpfter IoT Hub

Ein mit Ihre [DPS](#device-provisioning-service)-Instanz (Device Provisioning Service) verknüpfter [IoT-Hub](#iot-hub). Eine DPS-Instanz kann eine [Geräte](#device)-ID registrieren und die [Ausgangskonfiguration](#configuration) des [Gerätezwillings](#device-twin) in verknüpften IoT-Hubs festlegen.

Gilt für: Iot Hub, Device Provisioning Service

## <a name="m"></a>M

### <a name="mqtt"></a>MQTT

Eines der Messagingprotokolle, das [IoT Hub](#iot-hub) und IoT Central für die Kommunikation mit [Geräten](#device) unterstützen. MQTT steht für nichts.

[Weitere Informationen](../iot-hub/iot-hub-devguide-protocols.md)

Gilt für: IoT Hub, IoT Central, Geräteentwickler

### <a name="model"></a>Modell

Eine Definition eines Entitätstyps in Ihrer physischen Umgebung, einschließlich seiner [Eigenschaften](#properties), Telemetriedaten und [Komponenten](#component). Modelle werden verwendet, um [digitale Zwillinge](#digital-twin) zu erstellen, die bestimmte physische Objekte dieses Typs darstellen. Modelle werden in der [Digital Twins Definition Language](#digital-twins-definition-language) geschrieben.

Beim [Azure Digital Twins-Dienst](#azure-digital-twins) können Modelle zum Definieren von [Geräten](#device) und übergeordneten abstrakten Geschäftskonzepten verwendet werden. Bei IoT Plug & Play werden [Gerätemodelle](#device-model) verwendet, um Geräte genau zu beschreiben.

Gilt für: IoT Hub, IoT Central, Digital Twins, Geräteentwickler

### <a name="model-id"></a>Modell-ID

Wenn ein [IoT Plug & Play-Gerät](#iot-plug-and-play-device) eine Verbindung mit einem [IoT-Hub](#iot-hub) oder einer IoT Central-Anwendung herstellt, sendet es die [Modell](#model)-ID des von ihm implementierten [DTDL](#digital-twins-definition-language)-Modells (Digital Twins Definition Language). Jedes Modell hat eine eindeutige Modell-ID. Anhand dieser Modell-ID ist die [Lösung](#solution) in der Lage, das [Gerätemodell](#device-model) zu ermitteln.

Gilt für: IoT Hub, IoT Central, Geräteentwickler, Digital Twins

### <a name="model-repository"></a>Modellrepository

Speichert [Modelle](#model) und [Schnittstellen](#interface) mithilfe der [Digital Twins Definition Language](#digital-twins-definition-language). Eine [Lösung](#solution) verwendet eine [Modell-ID](#model-id), um ein Modell aus einem Repository abzurufen.

Gilt für: IoT Hub, IoT Central, Digital Twins

### <a name="model-repository-rest-api"></a>Modellrepository-REST-API

Eine API zum Verwalten von und Interagieren mit einem [Modellrepository](#model-repository). So können Sie beispielsweise mithilfe der API [Gerätemodelle](#device-model) hinzufügen und danach suchen.

Gilt für: IoT Hub, IoT Central, Digital Twins

### <a name="module"></a>Modul

Mithilfe der [IoT Hub](#iot-hub)-[Geräte](#device)-SDKs können Sie Module instanziieren, die jeweils eine unabhängige Verbindung mit Ihrem IoT-Hub herstellen. Dadurch können Sie separate Namespaces für unterschiedliche [Komponenten](#component) auf Ihrem Gerät verwenden.

[Modulidentität](#module-identity) und [Modulzwilling](#module-twin) bieten die gleichen Funktionen wie [Geräteidentität](#device-identity) und [Gerätezwilling](#device-twin), aber mit einer höheren Granularität.

Bei [IoT Edge](#iot-edge) ist ein Modul ein Docker-Container, der auf [IoT Edge-Geräten](#iot-edge-device) bereitgestellt werden kann. Es führt eine spezifische Aufgabe aus und erfasst so beispielsweise eine Nachricht von einem Gerät, transformiert eine Nachricht oder sendet eine Nachricht an eine IoT Hub-Instanz. Es kommuniziert mit anderen Modulen und sendet Daten an die [IoT Edge-Runtime](#iot-edge-runtime).

Gilt für: Iot Hub, IoT Edge, Geräteentwickler

### <a name="module-identity"></a>Modulidentität

Ein eindeutiger Bezeichner, der jedem [Modul](#module) zugewiesen ist, das zu einem [Gerät](#device) gehört. Die Modulidentität (Modul-ID, auch Modulkennung) wird auch in der [Identitätsregistrierung](#identity-registry) registriert.

Die Modulidentität gibt die Sicherheitsanmeldeinformationen an, die das Modul für die Authentifizierung beim [IoT-Hub](#iot-hub) oder im Fall eines [IoT Edge-Moduls](#iot-edge) beim [IoT Edge-Hub](#iot-edge-hub) verwendet.

Gilt für: Iot Hub, IoT Edge, Geräteentwickler

### <a name="module-image"></a>Modulimage

Das Docker-Image, das von der [IoT Edge-Runtime](#iot-edge-runtime) zum Instanziieren der [Modulinstanzen](#module) verwendet wird.

Gilt für: IoT Edge

### <a name="module-twin"></a>Modulzwilling

[Modulzwillinge](#module) ähneln [Gerätezwillingen](#device-twin) und sind JSON-Dokumente, in denen Modulstatusinformationen wie Metadaten, [Konfigurationen](#configuration) und Zustände gespeichert werden. [IoT Hub](#iot-hub) speichert einen Modulzwilling für jede [Modulidentität](#module-identity), die Sie unter einer [Geräteidentität](#device-identity) in Ihrem IoT-Hub bereitstellen. Mit Modulzwillingen können Sie Modulbedingungen und Konfigurationen zwischen dem Modul und dem [Lösungs](#solution)-Back-End synchronisieren. Sie können Modulzwillinge abfragen, um bestimmte Module zu ermitteln und den Status von Vorgängen mit langer Ausführungsdauer abzufragen.

Gilt für: IoT Hub

## <a name="o"></a>O

### <a name="ontology"></a>Ontologie

Im Kontext von [Digital Twins](#digital-twin) ist dies eine Reihe von [Modellen](#model) für eine bestimmte Domäne, z. B. Immobilien, Smart Citys, IoT-Systeme, Energienetze usw. Ontologien werden oft als Schemas für Wissensgraphen wie die in [Azure Digital Twins](#azure-digital-twins) verwendet, weil sie einen Ausgangspunkt bereitstellen, der auf Branchenstandards und bewährten Methoden basiert.

[Weitere Informationen](../digital-twins/concepts-ontologies.md)

Gilt für: Digital Twins

### <a name="operations-monitoring"></a>Vorgangsüberwachung

Ein Feature von [IoT Hub](#iot-hub), mit dem Sie den Status von Vorgängen auf Ihrem IoT-Hub in Echtzeit überwachen können. IoT Hub verfolgt Ereignisse in verschiedenen Vorgangskategorien nach. Sie können festlegen, dass Ereignisse aus einer oder mehreren Kategorien zur Verarbeitung an einen IoT Hub-[Endpunkt](#endpoint) gesendet werden. Sie können die Daten auf Fehler überwachen oder eine komplexere auf Datenmustern basierende Verarbeitung einrichten.

Gilt für: IoT Hub

## <a name="p"></a>P

### <a name="physical-device"></a>Physisches Gerät

Ein reales IoT-[Gerät](#device), das eine Verbindung mit einem [IoT-Hub](#iot-hub) herstellt. Der Einfachheit halber wird IoT-Gerätecode in vielen Tutorials und Schnellstarts auf einem Desktopcomputer und nicht auf einem physischen Gerät ausgeführt.

Gilt für: Iot Hub, IoT Central, Geräteentwickler, IoT Edge

### <a name="primary-and-secondary-keys"></a>Primär- und Sekundärschlüssel

Wenn Sie einen [geräteseitigen](#device) oder dienstseitigen [Endpunkt](#endpoint) mit einem [IoT-Hub](#iot-hub) oder einer IoT Central-Anwendung verbinden, enthält die [Verbindungszeichenfolge](#connection-string) einen Schlüssel zum Gewähren des Zugriffs. Wenn Sie der [Identitätsregistrierung](#identity-registry) ein Gerät hinzufügen oder dem Hub eine [SAS-Richtlinie](#shared-access-policy) hinzufügen, generiert der Dienst einen Primär- und einen Sekundärschlüssel. Dank der zwei Schlüssel können Sie einen Rollover von einem Schlüssel zum anderen Schlüssel durchführen, wenn Sie einen Schlüssel aktualisieren, ohne den Zugriff auf den IoT-Hub oder die IoT Central-Anwendung zu verlieren.

Gilt für: IoT Hub, IoT Central

### <a name="properties"></a>Eigenschaften

Im Kontext von [digitalen Zwillingen](#digital-twin) werden Datenfelder in einer [Schnittstelle](#interface) definiert und stellen einen beständigen Zustand eines digitalen Zwillings dar. Sie können Eigenschaften als schreibgeschützt oder beschreibbar deklarieren. Schreibgeschützte Eigenschaften, z.B. eine Seriennummer, werden durch Code festgelegt, der auf dem [IoT Plug & Play-Gerät](#iot-plug-and-play-device) selbst ausgeführt wird. Schreibbare Eigenschaften, z. B. ein Alarmschwellenwert, werden normalerweise in der cloudbasierten IoT-[Lösung](#solution) festgelegt.

Gilt für: IoT Hub, IoT Central, Digital Twins, Geräteentwickler

### <a name="property-change-event"></a>Eigenschaftenänderungsereignis

Ein Ereignis, das Folge einer Eigenschaftsänderung in einem [digitalen Zwilling](#digital-twin) ist.

Gilt für: IoT Hub, IoT Central, Digital Twins

### <a name="protocol-gateway"></a>Protokollgateway

Ein [Gateway](#gateway) wird der Regel in der Cloud bereitgestellt und bietet Protokollübersetzungsdienste für [Geräte](#device), die eine Verbindung mit einem [IoT-Hub](#iot-hub) oder einer IoT Central-Anwendung herstellen.

Gilt für: IoT Hub, IoT Central

## <a name="r"></a>R

### <a name="registration"></a>Registrierung

Ein Eintrag eines [Geräts](#device) in der [IoT Hub](#iot-hub)-[Identitätsregistrierung](#identity-registry). Sie können ein Gerät direkt oder zur Automatisierung der Geräteregistrierung über den [Device Provisioning Service](#device-provisioning-service) registrieren.

Gilt für: IoT Hub, IoT Central, Device Provisioning Service

### <a name="registration-id"></a>Registrierungs-ID

Eines eindeutige [Geräteidentität](#device-identity) im [Device Provisioning Service](#device-provisioning-service). Der Wert der [Registrierungs](#registration)-ID kann mit dem der [Geräteidentität](#device) übereinstimmen.

Gilt für: IoT Hub, IoT Central, Device Provisioning Service

### <a name="relationship"></a>Beziehung

Wird beim [Azure Digital Twins](#azure-digital-twins)-Dienst verwendet, um [digitale Zwillinge](#digital-twin) mit Wissensgraphen zu verbinden, die Ihre gesamte physische Umgebung digital darstellen. Die Typen von Beziehungen, die Ihre Zwillinge haben können, werden im [Digital Twins Definition Language](#digital-twins-definition-language)-[Modell](#model) definiert.

Gilt für: Digital Twins

### <a name="reported-configuration"></a>Gemeldete Konfiguration

Im Kontext eines [Gerätezwillings](#device-twin) bezieht sich dies auf den vollständigen Satz von [Eigenschaften](#properties) und Metadaten im [Gerätezwilling](#device), die an das [Lösungs](#solution)-Back-End gemeldet werden sollen.

Gilt für: IoT Hub, Geräteentwickler

### <a name="reported-properties"></a>Gemeldete Eigenschaften

Im Kontext eines [Gerätezwillings](#device-twin) handelt es sich bei gemeldeten [Eigenschaften](#properties) um einen Unterabschnitt des [Gerätezwillings](#device). Gemeldete Eigenschaften können nur vom Gerät festgelegt werden. Das Lesen und Abfragen ist aber über die [Back-End-App](#back-end-app) möglich.

Gilt für: IoT Hub, Geräteentwickler

### <a name="retry-policy"></a>Wiederholungsrichtlinie

Eine Möglichkeit, um vorübergehende Fehler zu verarbeiten, wenn Sie eine Verbindung mit einem Clouddienst herstellen.

Gilt für: IoT Hub, IoT Central, Geräteentwickler

### <a name="routing-rule"></a>Routingregel

Ein Feature von [IoT Hub](#iot-hub), um [D2C](#device-to-cloud)-Nachrichten an einen integrierten [Endpunkt](#endpoint) oder [benutzerdefinierte Endpunkte](#custom-endpoints) für die Verarbeitung durch Ihr [Lösungs](#solution)-Back-End weiterzuleiten.

Gilt für: IoT Hub

## <a name="s"></a>E

### <a name="saslplain"></a>SASL/PLAIN

Ein Protokoll, das vom [Advanced Message Queueing Protocol](#advanced-message-queueing-protocol) (AMQP) zum Übertragen von Sicherheitstoken verwendet wird.

[Weitere Informationen](https://wikipedia.org/wiki/Simple_Authentication_and_Security_Layer)

Gilt für: IoT Hub

### <a name="service-rest-api"></a>Dienst-REST-API

Eine REST-API, die Sie am [Lösungs](#solution)-Back-Ends zur Verwaltung Ihrer [Geräte](#device) verwenden können. Sie können z. B. die API des [Iot Hub](#iot-hub)-Diensts verwenden, um [Eigenschaften](#properties) von [Gerätezwillingen](#device-twin) abzurufen und zu aktualisieren, [direkte Methoden](#direct-method) aufzurufen und [Aufträge](#job) zu planen. In der Regel sollten Sie eines der übergeordneten Dienst-SDKs verwenden.

Gilt für: IoT Hub, IoT Central, Device Provisioning Service, IoT Edge

### <a name="service-operations-endpoint"></a>Endpunkt für Dienstvorgänge

Ein [Endpunkt](#endpoint), den Administratoren zum Verwalten von Diensteinstellungen verwenden. Beispielsweise verwenden Sie den Dienstendpunkt im [Device Provisioning Service](#device-provisioning-service) zum Verwalten von [Registrierungen](#enrollment).

Gilt für: IoT Hub, Device Provisioning Service, IoT Edge, Digital Twins

### <a name="shared-access-policy"></a>SAS-Richtlinie

Dient zum Definieren von Berechtigungen, die allen Personen mit einem Primär- oder Sekundärschlüssel, der dieser Richtlinie zugeordnet ist, gewährt werden. Sie können die SAS-Richtlinien und Schlüssel für den Hub im Portal verwalten.

Gilt für: IoT Hub, IoT Edge, Device Provisioning Service

### <a name="shared-access-signature"></a>Shared Access Signature (SAS)

Eine Shared Access Signature ist ein signierter URI, der auf eine oder mehrere Ressourcen zeigt, z. B. auf einen [IoT-Hub](#iot-hub)-[Endpunkt](#endpoint). Der URI enthält ein Token, das angibt, wie der Client auf die Ressourcen zugreifen kann. Einer der Abfrageparameter ist die Signatur. Sie besteht aus den SAS-Parametern und wird mit dem Schlüssel signiert, der zum Erstellen der SAS verwendet wurde. Diese Signatur wird von Azure Storage verwendet, um den Zugriff auf die Speicherressource zu autorisieren.

Gilt für: Iot Hub, Digital Twins, IoT Central, IoT Edge

### <a name="simulated-device"></a>Simuliertes Gerät

Der Einfachheit halber wird [Gerätecode](#device) in vielen Tutorials und Schnellstarts mit simulierten Sensoren auf Ihrem lokalen Entwicklungscomputer ausgeführt. Ein [physisches Gerät](#physical-device), z. B. ein MXCHIP, hat dagegen echte Sensoren und stellt eine Verbindung mit einem [IoT-Hub](#iot-hub) her.

Gilt für: Iot Hub, IoT Central, Geräteentwickler, IoT Edge, Digital Twins, Device Provisioning Service

### <a name="solution"></a>Lösung

Im IoT-Kontext ist eine *Lösung* meist eine IoT-Lösung, die Elemente wie Geräte, [Geräte](#device), [Geräte-Apps](#device-app), einen [IoT-Hub](#iot-hub), andere Azure-Dienste und [Back-End-Apps](#back-end-app) enthält.

Gilt für: IoT Hub, IoT Central, Device Provisioning Service, IoT Edge, Digital Twins

### <a name="system-properties"></a>Systemeigenschaften

Im Kontext eines [Gerätezwillings](#device-twin) sind diese [Eigenschaften](#properties) schreibgeschützt und enthalten Informationen zur [Gerätenutzung](#device), z. B. den Zeitpunkt der letzten Aktivität und den Verbindungsstatus.

Gilt für: IoT Hub

## <a name="t"></a>T

### <a name="tag"></a>Tag

Im Kontext eines [Gerätezwillings](#device-twin) sind Tags [Gerätemetadaten](#device), die vom [Lösungs](#solution)-Back-End in Form eines JSON-Dokuments gespeichert und abgerufen werden. Tags sind für Apps auf einem Gerät nicht sichtbar.

Gilt für: IoT Hub

### <a name="target-condition"></a>Zielbedingung

In einer [IoT Edge](#iot-edge)-Bereitstellung dient die Zielbedingung zur Auswahl der [Zielgeräte](#device) für die Bereitstellung. Die Zielbedingung wird kontinuierlich ausgewertet, um jedes neue Gerät einzubeziehen, das die Anforderungen erfüllt, bzw. Geräte zu entfernen, die die Bedingung nicht mehr erfüllen.

Gilt für: IoT Edge

### <a name="telemetry"></a>Telemetrie

Die Daten, z. B. Windgeschwindigkeit oder Temperatur, die an einen [IoT-Hub](#iot-hub) gesendet und von den Sensoren eines [Geräts](#device) gesammelt wurden.

Im Gegensatz zu [Eigenschaften](#properties) wird Telemetrie nicht in einem [digitalen Zwilling](#digital-twin) gespeichert. Telemetrie ist ein Strom zeitgebundener Datenereignissen, die in der Reihenfolge ihres Auftretens verarbeitet werden müssen.

Bei IoT Plug & Play und [Azure Digital Twins](#azure-digital-twins) stellen in einer [Schnittstelle](#interface) definierte Telemetriefelder Messungen dar. Diese Messungen sind normalerweise Werte wie Sensormesswerte, die von Geräten wie [IoT Plug & Play-Geräten](#iot-plug-and-play-device) als Datenstrom gesendet werden.

Gilt für: IoT Hub, IoT Central, Digital Twins, IoT Edge, Geräteentwickler

### <a name="telemetry-event"></a>Telemetrieereignis

Ein Ereignis in einem [IoT-Hub](#iot-hub), das den Eingang von [Telemetriedaten](#telemetry) angibt.

Gilt für: IoT Hub

### <a name="twin-queries"></a>Zwillingsabfragen

Ein Feature von [IoT Hub](#iot-hub), mit dem Sie eine SQL-ähnliche Abfragesprache verwenden können, um Informationen von Ihren [Gerätezwillingen](#device-twin) oder [Modulzwillingen](#module-twin) abzurufen.

Gilt für: IoT Hub

### <a name="twin-synchronization"></a>Zwillingssynchronisierung

Der Prozess in [IoT Hub](#iot-hub), mit dem anhand der [gewünschten Eigenschaften](#desired-properties) in Ihren [Gerätezwillingen](#device-twin) oder [Modulzwillingen](#module-twin) Ihre [Geräte](#device) oder [Module](#module) konfiguriert und [gemeldete Eigenschaften](#reported-properties) von Ihren Geräten oder Modulen abgerufen und im Zwilling gespeichert werden.

Gilt für: IoT Hub

## <a name="u"></a>U

### <a name="upstream-service"></a>Upstreamdienst

Ein relativer Begriff für Dienste, die Daten in den aktuellen Kontext einspeisen. Beispielsweise gilt [IoT Hub](#iot-hub) im Kontext von [Azure Digital Twins](#azure-digital-twins) als Upstreamdienst, da Ihre Daten vom IoT-Hub zu Azure [Digital Twins](#digital-twin) fließen.

Gilt für: IoT Hub, IoT Central, Digital Twins

