---
title: Grundlegendes zu Chaos Engineering und Resilienz mit Azure Chaos Studio
description: Grundlegendes zu den Konzepten von Chaos Engineering und Resilienz.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: 75a225031986b595f3e3d83b729b73588726a465
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131094986"
---
# <a name="understanding-chaos-engineering-and-resilience"></a>Grundlegendes zu Chaos Engineering und Resilienz

Bevor Sie mit der Verwendung von Chaos Studio beginnen, ist es hilfreich, sich mit den grundlegenden Konzepten für Websitezuverlässigkeit vertraut zu machen, die angewendet werden.

## <a name="what-is-resilience"></a>Was ist Resilienz?

Das Erstellen von großen verteilten Anwendungen war noch nie zuvor einfacher. Die Infrastruktur wird in der Cloud gehostet, die Unterstützung der Programmiersprache ist vielfältig, und es gibt eine Vielzahl von Open Source- und gehosteten Komponenten und Diensten, auf deren Grundlage sie aufbauen können. Leider gibt es keine Zuverlässigkeitsgarantie für diese zugrunde liegenden Komponenten und Abhängigkeiten oder für darauf aufbauende Systeme. Die Infrastruktur geht mitunter offline, und Dienstunterbrechungen oder -ausfälle können jederzeit auftreten. Kleinere Unterbrechungen in einem Bereich können sich vergrößern und in einem anderen Bereich langfristige Nebeneffekte haben. 

Anwendungen und Dienste müssen Dienstausfälle, Unterbrechungen bekannter und unbekannter Abhängigkeiten, plötzliche unerwartete Auslastung und Latenzen im gesamten System planen und berücksichtigen. Anwendungen und Dienste müssen so entworfen werden, dass sie Fehler behandeln können und gegen Unterbrechungen gewappnet sind. 

Anwendungen und Dienste, die sachgemäß mit Problemen und umgehen, sind **resilient**. Die Zuverlässigkeit einzelner Komponenten ist gut, doch **Resilienz ist eine Eigenschaft des gesamten Systems**. Die End-to-End-Systemresilienz muss in einer integrierten, produktionsbasierten Umgebung mit den Bedingungen und der Last überprüft werden, die während des Betriebs zu finden sind.

## <a name="what-are-chaos-engineering-and-fault-injection"></a>Was sind Chaos Engineering und Fault Injection?

Beim **Chaos Engineering** werden Anwendungen und Dienste realen Belastungen und Störungen ausgesetzt, um deren Widerstandsfähigkeit gegenüber unzuverlässigen Bedingungen und fehlenden Verfügbarkeiten zu überprüfen und zu verstehen. **Fault injection** bezeichnet die Einführung eines Fehlers in ein System. Verschiedene Fehler, z. B. Netzwerklatenz oder Verlust des Zugriffs auf den Speicher, können verwendet werden, um auf Systemkomponenten abzuzielen. Dies führt zu Szenarien, die eine Anwendung oder ein Dienst verarbeiten oder wiederherstellen können muss. Ein Chaosexperiment ist die Anwendung von Fehlern einzeln, parallel und/oder sequenziell auf eine oder mehrere Abonnementressourcen oder Verfügbarkeiten mit dem Ziel, das Verhalten und die Integrität des Systems zu überwachen und bei allen auftretenden Problemen handeln zu können. Ein Experiment kann ein reales Szenario darstellen, beispielsweise einen Stromausfall eines Rechenzentrums oder etwa eine Netzwerklatenz für einen DNS-Server. Es kann auch verwendet werden, um Randbedingungen zu simulieren, die bei Black Friday Einkaufsorgien auftreten oder wenn Tickets für eine beliebte Band in den Verkauf gehen.
