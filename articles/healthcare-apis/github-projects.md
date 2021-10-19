---
title: Verwandte GitHub-Projekte für Azure Healthcare-APIs
description: Auflisten aller Open Source-Repositorys (GitHub)
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.topic: reference
ms.date: 10/18/2021
ms.author: ginle
ms.openlocfilehash: 63e3ff442ab45801d079e4f61b8dae98a9b010f4
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166372"
---
# <a name="github-projects"></a>GitHub-Projekte

Wir haben viele Open-Source-Projekte auf GitHub, die Ihnen den Quellcode und Anweisungen zum Bereitstellen von Diensten für verschiedene Verwendungen bereitstellen. Sie sind jederzeit willkommen, unsere repositorys GitHub zu besuchen, um unsere Features und Produkte zu erlernen und zu experimentieren. 

## <a name="healthcare-apis-samples"></a>Beispiele für Gesundheits-APIs

* Dieses Repository enthält [Beispiele für Gesundheits-APIs,](https://github.com/microsoft/healthcare-apis-samples)einschließlich Fast Healthcare Interoperability Resources (FHIR&#174;), DICOM, IoT-Connector und datenbezogener Dienste.

## <a name="fhir-server"></a>FHIR-Server

* [microsoft/fhir-server:](https://github.com/microsoft/fhir-server/)Open-Source-FHIR-Server, der die Grundlage für den FHIR-Dienst ist
* Informationen zu den neuesten Releases finden Sie in den [Versionshinweisen.](https://github.com/microsoft/fhir-server/releases)
* [microsoft/fhir-server-samples:](https://github.com/microsoft/fhir-server-samples)eine Beispielumgebung

## <a name="data-conversion--anonymization"></a>Datenkonvertierung & Anonymisierung

#### <a name="fhir-converter"></a>FHIR-Konverter

* [microsoft/FHIR-Converter:](https://github.com/microsoft/FHIR-Converter)ein Konvertierungsprogramm zum Übersetzen von Legacydatenformaten in FHIR
* Integration in den FHIR-Dienst und den FHIR-Server für Azure in Form $convert-Data-Vorgangs
* Fortlaufende Verbesserungen in OSS und kontinuierliche Integration in die FHIR-Server
 
#### <a name="fhir-converter---vs-code-extension"></a>FHIR-Konverter – VS Code Erweiterung

* [microsoft/FHIR-Tools-for-Anonymization:](https://github.com/microsoft/FHIR-Tools-for-Anonymization)eine Reihe von Tools zur Unterstützung bei der Anonymisierung von Daten (im FHIR-Format).
* Integration in den FHIR-Dienst und den FHIR-Server für Azure in Form eines "de-identified export"

#### <a name="fhir-tools-for-anonymization"></a>FHIR-Tools für die Anonymisierung

* [microsoft/vscode-azurehealthcareapis-tools:](https://github.com/microsoft/vscode-azurehealthcareapis-tools)eine VS Code-Erweiterung, die eine Sammlung von Tools für die Arbeit mit Azure Healthcare-APIs enthält.
* Veröffentlicht im Visual Studio Marketplace
* Wird zum Erstellen von Liquid-Vorlagen verwendet, die im FHIR-Konverter verwendet werden sollen.

## <a name="iot-connector"></a>IoT-Connector

#### <a name="integration-with-iot-hub-and-iot-central"></a>Integration in IoT Hub und IoT Central

* [microsoft/iomt-fhir:](https://github.com/microsoft/iomt-fhir)Integration mit IoT Hub oder IoT Central in FHIR mit Datennormalisierung und FHIR-Konvertierung der normalisierten Daten
* Normalisierung: Gerätedateninformationen werden zur weiteren Verarbeitung in ein allgemeines Format extrahiert.
* FHIR-Konvertierung: Normalisierte und gruppierte Daten werden FHIR zugeordnet. Beobachtungen werden gemäß konfigurierten Vorlagen erstellt oder aktualisiert und mit dem Gerät und dem Patienten verknüpft.
* [Tools zum Erstellen der Konversationszuordnung:](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)Visualisieren Sie die Zuordnungskonfiguration zum Normalisieren der Geräteeingabedaten, und transformieren Sie sie in die FHIR-Ressourcen. Entwickler können dieses Tool verwenden, um die Zielzuordnungen für Geräte und FHIR zu bearbeiten und zu testen und zum Hochladen in den IoT-Connector in der Azure-Portal.

#### <a name="healthkit-and-fhir-integration"></a>Integration von HealthKit und FHIR

* [microsoft/healthkit-on-fhir:](https://github.com/microsoft/healthkit-on-fhir)Eine Swift-Bibliothek, die den Export von Apple HealthKit-Daten auf einen FHIR-Server automatisiert

>[!div class="nextstepaction"]
>[Übersicht über Azure Healthcare-APIs](healthcare-apis-overview.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet. 