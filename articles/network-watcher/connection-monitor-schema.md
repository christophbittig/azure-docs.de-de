---
title: Schemas des Azure Network Watcher-Verbindungsmonitors | Microsoft-Dokumentation
description: Lernen Sie das Testdatenschema und das Pfaddatenschema von Azure Network Watcher Verbindungsmonitor kennen.
services: network-watcher
documentationcenter: na
author: mjha
manager: vinigam
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/05/2021
ms.author: mjha
ms.openlocfilehash: 4cad1ea0d90f85a12e7d7f9b7dbc869a61a91a39
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969764"
---
# <a name="azure-network-watcher-connection-monitor-schemas"></a>Schemas des Azure Network Watcher-Verbindungsmonitors

Der Verbindungsmonitor bietet eine einheitliche End-to-End-Verbindungsüberwachung in Azure Network Watcher. Das Feature für den Verbindungsmonitor unterstützt Hybrid- und Azure-Cloudbereitstellungen. Network Watcher stellt Tools für das Überwachen, Diagnostizieren und Anzeigen von Verbindungsmetriken für Ihre Azure-Bereitstellungen zur Verfügung.

Nachfolgend sind einige Anwendungsfälle für den Verbindungsmonitor aufgeführt:

- Ihre Front-End-Webserver-VM kommuniziert in einer Anwendung mit mehreren Ebenen mit einer Datenbankserver-VM, und Sie möchten die Netzwerkkonnektivität zwischen beiden VMs überprüfen.
- Sie möchten, dass VMs in der Region „USA, Osten“ VMs in der Region „USA, Mitte“ pingen, und Sie möchten die regionsübergreifenden Netzwerklatenzen vergleichen.
- Sie verfügen über mehrere lokale Unternehmensstandorte in Seattle, Washington, und in Ashburn, Virginia. Ihre Unternehmensstandorte sind mit Microsoft 365-URLs verbunden. Sie möchten für die Benutzer der Microsoft 365-URLs die Latenzen in Seattle und Ashburn vergleichen.
- Ihre Hybridanwendung benötigt eine Verbindung mit einem Azure Storage-Endpunkt. Der lokale Standort und die Azure-Anwendung sind mit dem gleichen Azure Storage-Endpunkt verbunden. Sie möchten die Latenzen des lokalen Standorts mit den Latenzen der Azure-Anwendung vergleichen.
- Sie möchten die Konnektivität zwischen den lokalen Setups und den Azure-VMs überprüfen, auf denen die Cloudanwendung gehostet wird.

Nachfolgend sind einige Vorteile des Verbindungsmonitors aufgeführt:

* Einheitliche, intuitive Oberfläche zum Überwachen von Azure- und Hybridbereitstellungen
* Regionsübergreifende und arbeitsbereichsübergreifende Konnektivitätsüberwachung
* Häufigere Prüfungen und bessere Einsicht in die Netzwerkleistung
* Schnellere Warnungen für Ihre Hybridbereitstellungen
* Unterstützung für Konnektivitätsprüfungen basierend auf HTTP, TCP und ICMP 
* Metriken und Unterstützung für Log Analytics, sowohl für Azure- als auch für Nicht-Azure-Testsetups

Es gibt zwei Arten von Protokollen bzw. Daten, die in Log Analytics erfasst werden. Die Testdaten (NWConnectionMonitorTestResult-Abfrage) werden basierend auf der Überwachungshäufigkeit einer bestimmten Testgruppe aktualisiert. Die Pfaddaten (NWConnectionMonitorPathResult-Abfrage) werden aktualisiert, wenn sich der Prozentsatz des Verlusts oder die Roundtripzeit erheblich ändern. Testdaten werden für einen bestimmten Zeitraum möglicherweise fortlaufend aktualisiert, während Pfaddaten weniger häufig aktualisiert werden, da beide unabhängig voneinander sind.

## <a name="connection-monitor-tests-schema"></a>Verbindungsmonitor-Testschema

In der folgenden Tabelle werden die Felder im Verbindungsmonitor-Testdatenschema und ihre Bedeutung aufgeführt. 

| Feld  |    BESCHREIBUNG   |
|---|---|
| TimeGenerated | Der Zeitstempel (UTC) für den Zeitpunkt, zu dem das Protokoll generiert wurde |
| RecordId  | Die Datensatz-ID für die eindeutige Identifizierung des Testergebnisdatensatzes |
| ConnectionMonitorResourceId   | Die ID der Verbindungsmonitorressource für den Test |
| TestGroupName | Der Name der Testgruppe, der der Test angehört |
| TestConfigurationName | Der Name der Testkonfiguration, der der Test angehört |
| SourceType    | Der Typ des Quellcomputers, der für den Test konfiguriert ist |
| SourceResourceId  | Die Ressourcen-ID des Quellcomputers |
| SourceAddress | Die Adresse der für den Test konfigurierten Quelle |
| SourceSubnet  | Das Subnetz der Quelle |
| SourceIP  | Die IP-Adresse der Quelle |
| SourceName    | Der Endpunktname der Quelle |
| SourceAgentId | Die Quell-Agent-ID |
| DestinationPort   | Der für den Test konfigurierte Zielport |
| DestinationType   | Der Typ des Zielcomputers, der für den Test konfiguriert ist |
| DestinationResourceId | Die Ressourcen-ID des Zielcomputers |
| DestinationAddress    | Die Adresse des für den Test konfigurierten Ziels |
| DestinationSubnet | Falls zutreffend, das Subnetz des Ziels |
| DestinationIP | Die IP-Adresse des Ziels |
| DestinationName   | Der Name des Zielendpunkts |
| DestinationAgentId    | Die Ziel-Agent-ID |
| Protocol  | Das Protokoll für den Test |
| ChecksTotal   | Die Gesamtzahl der im Rahmen des Tests durchgeführten Überprüfungen |
| ChecksFailed  | Die Gesamtzahl der im Rahmen des Tests durchgeführten Überprüfungen mit Fehlern |
| TestResult    | Das Ergebnis des Tests |
| TestResultCriterion   | Die Ergebniskriterien für den Test |
| ChecksFailedPercentThreshold  | Der für den Test festgelegte Schwellenwert für fehlerhafte Tests in Prozent |
| RoundTripTimeMsThreshold  | Der für den Test festgelegte Roundtripschwellenwert (in Millisekunden) |
| MinRoundTripTimeMs    | Die minimale Roundtripzeit (in Millisekunden) für den Test |
| MaxRoundTripTimeMs    | Die maximale Roundtripzeit (in Millisekunden) für den Test |
| AvgRoundTripTimeMs    | Die durchschnittliche Roundtripzeit für den Test |
| JitterMs  | Die mittlere Abweichung der Roundtripzeit für den Test |
| AdditionalData    | Die zusätzlichen Daten für den Test |


## <a name="connection-monitor-path-schema"></a>Verbindungsmonitor-Pfadschema

In der folgenden Tabelle werden die Felder im Verbindungsmonitor-Pfaddatenschema und ihre Bedeutung aufgeführt. 

| Feld  |    BESCHREIBUNG   |
|---|---|
| TimeGenerated  | Der Zeitstempel (UTC) für den Zeitpunkt, zu dem das Protokoll generiert wurde |
| RecordId  | Die Datensatz-ID für die eindeutige Identifizierung des Testergebnisdatensatzes |
| TopologyId    | Die Topologie-ID des Pfaddatensatzes |
| ConnectionMonitorResourceId   | Die ID der Verbindungsmonitorressource für den Test |
| TestGroupName | Der Name der Testgruppe, der der Test angehört |
| TestConfigurationName | Der Name der Testkonfiguration, der der Test angehört |
| SourceType    | Der Typ des Quellcomputers, der für den Test konfiguriert ist |
| SourceResourceId  | Die Ressourcen-ID des Quellcomputers |
| SourceAddress | Die Adresse der für den Test konfigurierten Quelle |
| SourceSubnet  | Das Subnetz der Quelle |
| SourceIP  | Die IP-Adresse der Quelle | 
| SourceName    | Der Endpunktname der Quelle |
| SourceAgentId | Die Quell-Agent-ID |
| DestinationPort   | Der für den Test konfigurierte Zielport |
| DestinationType   | Der Typ des Zielcomputers, der für den Test konfiguriert ist |
| DestinationResourceId | Die Ressourcen-ID des Zielcomputers |
| DestinationAddress    | Die Adresse des für den Test konfigurierten Ziels |
| DestinationSubnet | Falls zutreffend, das Subnetz des Ziels |
| DestinationIP | Die IP-Adresse des Ziels |
| DestinationName   | Der Name des Zielendpunkts |
| DestinationAgentId    | Die Ziel-Agent-ID |
| Protocol  | Das Protokoll für den Test |
| ChecksTotal   | Die Gesamtzahl der im Rahmen des Tests durchgeführten Überprüfungen |
| ChecksFailed  | Die Gesamtzahl der im Rahmen des Tests durchgeführten Überprüfungen mit Fehlern |
| PathTestResult    | Das Ergebnis des Tests |
| PathResultCriterion   | Die Ergebniskriterien für den Test | 
| ChecksFailedPercentThreshold  | Der für den Test festgelegte Schwellenwert für fehlerhafte Tests in Prozent |
| RoundTripTimeMsThreshold  | Der für den Test festgelegte Roundtripschwellenwert (in Millisekunden) |
| MinRoundTripTimeMs    | Die minimale Roundtripzeit (in Millisekunden) für den Test |
| MaxRoundTripTimeMs    | Die maximale Roundtripzeit (in Millisekunden) für den Test |
| AvgRoundTripTimeMs    | Die durchschnittliche Roundtripzeit für den Test |
| JitterMs  | Die mittlere Abweichung der Roundtripzeit für den Test |
| HopAddresses | Die für den Test identifizierten Hopadressen |
| HopTypes  | Die für den Test identifizierten Hoptypen |
| HopLinkTypes  | Die für den Test identifizierten Hoplinktypen |
| HopResourceIds    | Die für den Test identifizierten Hopressourcen-IDs |
| Probleme    | Die für den Test identifizierten Probleme |
| Hops  | Die für den Test identifizierten Hops |
| AdditionalData | Die zusätzlichen Daten für den Test |
