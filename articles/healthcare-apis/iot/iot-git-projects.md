---
title: Verwandte GitHub für IoT-Connector – Azure Healthcare-APIs
description: Auflisten aller Open-Source-Repositorys (GitHub) für IoT-Connector
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.topic: reference
ms.date: 10/18/2021
ms.author: jasteppe
ms.openlocfilehash: 0092482af2f9f4f6f9a090a00cc387ae901e5b68
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130187582"
---
# <a name="open-source-projects"></a>Open-Source-Projekte

Sehen Sie sich unsere Open-Source-Projekte auf GitHub, die Quellcode und Anweisungen zum Bereitstellen von Diensten für verschiedene Verwendungen mit dem IoT-Connector bereitstellen. 

## <a name="iot-connector-github-projects"></a>IoT-Connector GitHub Projekten

#### <a name="integration-with-iot-hub-and-iot-central"></a>Integration in IoT Hub IoT Central

* [microsoft/iomt-fhir:](https://github.com/microsoft/iomt-fhir)Integration mit IoT Hub oder IoT Central in Fast Healthcare Interoperability Resources (FHIR&#174;) mit Datennormalisierung und FHIR-Konvertierung der normalisierten Daten
* Normalisierung: Gerätedateninformationen werden zur weiteren Verarbeitung in ein allgemeines Format extrahiert.
* FHIR-Konvertierung: Normalisierte und gruppierte Daten werden FHIR zugeordnet. Beobachtungen werden gemäß konfigurierten Vorlagen erstellt oder aktualisiert und mit dem Gerät und dem Patienten verknüpft.

#### <a name="device-and-fhir-destination-mappings-authoring-and-troubleshooting"></a>Erstellung und Problembehandlung von Geräte- und FHIR-Zielzuordnungen

* [Tools zum Erstellen von Geräte- und FHIR-Zielzuordnungen:](https://github.com/microsoft/iomt-fhir/tree/master/tools/data-mapper)Visualisieren Sie die Zuordnungskonfiguration zum Normalisieren der Geräteeingabedaten, und transformieren Sie sie in FHIR-Ressourcen. Entwickler können dieses Tool verwenden, um Gerätezuordnungen und FHIR-Zielzuordnungen zu bearbeiten und zu testen. Exportieren Sie sie zum Hochladen in den IoT-Connector im Azure-Portal.

#### <a name="healthkit-and-fhir-integration"></a>Integration von HealthKit und FHIR

* [microsoft/healthkit-on-fhir:](https://github.com/microsoft/healthkit-on-fhir)Eine Swift-Bibliothek, die den Export von Apple HealthKit-Daten in einen FHIR-Dienst automatisiert.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Sie den IoT-Connector im Azure-Portal

>[!div class="nextstepaction"]
>[Bereitstellen eines verwalteten IoT Connector-Diensts](deploy-iot-connector-in-azure.md)

(FHIR&#174;) ist eine registrierte Marke von [HL7](https://hl7.org/fhir/) und wird mit der Berechtigung von HL7 verwendet.