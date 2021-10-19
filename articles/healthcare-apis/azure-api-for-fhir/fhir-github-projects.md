---
title: Verwandte GitHub-Projekte für Azure API for FHIR
description: Listet alle Open Source-Repositorys (GitHub) für Azure API for FHIR auf.
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 02/01/2021
ms.author: ginle
ms.openlocfilehash: 9977765183bd567377592631d65f3e548bef4b34
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122340038"
---
# <a name="related-github-projects"></a>Verwandte GitHub-Projekte

Wir haben viele Open-Source-Projekte auf GitHub, die Ihnen den Quellcode und Anweisungen zum Bereitstellen von Diensten für verschiedene Zwecke bereitstellen. Sie können jederzeit unsere GitHub Repositorys besuchen, um unsere Features und Produkte zu erlernen und mit ihnen zu experimentieren. 

## <a name="fhir-server"></a>FHIR-Server
* [microsoft/fhir-server:](https://github.com/microsoft/fhir-server/)Open-Source-FHIR-Server, der die Grundlage für Azure API for FHIR
* Die neuesten Releases finden Sie in den [Versionshinweisen.](https://github.com/microsoft/fhir-server/releases)
* [microsoft/fhir-server-samples:](https://github.com/microsoft/fhir-server-samples)Eine Beispielumgebung

## <a name="data-conversion--anonymization"></a>Datenkonvertierung & Anonymisierung

#### <a name="fhir-converter"></a>FHIR-Konverter
* [microsoft/FHIR-Converter:](https://github.com/microsoft/FHIR-Converter)ein Konvertierungshilfsprogramm zum Übersetzen von Legacydatenformaten in FHIR
* Integration in den Azure API for FHIR- und FHIR-Server für Azure in Form eines $convert-Daten-Vorgangs
* Fortlaufende Verbesserungen in OSS und kontinuierliche Integration in die FHIR-Server
 
#### <a name="fhir-converter---vs-code-extension"></a>FHIR-Konverter – VS Code-Erweiterung
* [microsoft/FHIR-Tools-for-Anonymization:](https://github.com/microsoft/FHIR-Tools-for-Anonymization)eine Reihe von Tools zur Unterstützung der Datenanonymisierung (im FHIR-Format)
* Integriert in den Azure API for FHIR und FHIR-Server für Azure in Form von "de-identified export"

#### <a name="fhir-tools-for-anonymization"></a>FHIR-Tools für die Anonymisierung
* [microsoft/vscode-azurehealthcareapis-tools:](https://github.com/microsoft/vscode-azurehealthcareapis-tools)eine VS Code-Erweiterung, die eine Sammlung von Tools für die Arbeit mit Azure Healthcare-APIs enthält.
* Veröffentlichung im Visual Studio Marketplace
* Wird zum Erstellen von Liquid-Vorlagen verwendet, die im FHIR-Konverter verwendet werden sollen

## <a name="iot-connector"></a>IoT-Konnektor

#### <a name="integration-with-iot-hub-and-iot-central"></a>Integration in IoT Hub und IoT Central
* [microsoft/iomt-fhir:](https://github.com/microsoft/iomt-fhir)Integration mit IoT Hub oder IoT Central in FHIR mit Datennormalisierung und FHIR-Konvertierung der normalisierten Daten
* Normalisierung: Gerätedateninformationen werden zur weiteren Verarbeitung in ein gemeinsames Format extrahiert.
* FHIR-Konvertierung: Normalisierte und gruppierte Daten werden FHIR zugeordnet. Beobachtungen werden gemäß konfigurierten Vorlagen erstellt oder aktualisiert und mit dem Gerät und dem Patienten verknüpft.
* [Tools zum Erstellen der Konversationszuordnung:](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)Visualisieren Sie die Zuordnungskonfiguration für die Normalisierung der Geräteeingabedaten, und transformieren Sie sie in die FHIR-Ressourcen. Entwickler können dieses Tool verwenden, um die Zuordnungen, die Gerätezuordnung und die FHIR-Zuordnung zu bearbeiten und zu testen und sie zum Hochladen in die IoT Connector im Azure-Portal zu exportieren.

#### <a name="healthkit-and-fhir-integration"></a>HealthKit- und FHIR-Integration
* [microsoft/healthkit-on-fhir:](https://github.com/microsoft/healthkit-on-fhir)Eine Swift-Bibliothek, die den Export von Apple HealthKit-Daten auf einen FHIR-Server automatisiert.

 