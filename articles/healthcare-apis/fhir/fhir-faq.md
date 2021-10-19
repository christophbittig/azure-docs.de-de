---
title: Häufig gestellte Fragen zu FHIR-Diensten in Azure Healthcare-APIs
description: Hier erhalten Sie Antworten auf häufig gestellte Fragen zum FHIR-Dienst, z. B. den Speicherort der Daten hinter FHIR-APIs und Versionsunterstützung.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.custom: references_regions
ms.openlocfilehash: 7e36e91125e721d8ce5ed4dbc2603109838ca531
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/24/2021
ms.locfileid: "122780009"
---
# <a name="frequently-asked-questions-about-the-fhir-service"></a>Häufig gestellte Fragen zum FHIR-Dienst

> [!IMPORTANT]
> Azure Healthcare-APIs sind derzeit als VORSCHAUversion verfügbar. Die [zusätzlichen Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) enthalten zusätzliche rechtliche Bedingungen für Azure-Features, die sich in der Beta- oder Vorschauphase befinden oder anderweitig noch nicht allgemein verfügbar sind.

In diesem Abschnitt werden einige der häufig gestellten Fragen zum FHIR-Dienst für Azure Healthcare-APIs (auch als FHIR-Dienst bezeichnet) behandelt.

## <a name="fhir-service-the-basics"></a>FHIR-Dienst: Grundlagen

### <a name="what-is-fhir"></a>Was ist FHIR?

Fast Healthcare Interoperability Resources (FHIR, ausgesprochen wie das englische Wort „Fire“) ist ein Interoperabilitätsstandard, der den Austausch von Patientendaten zwischen verschiedenen Gesundheitssystemen ermöglicht. Dieser Standard wurde von der HL7-Organisation entwickelt und wird von Gesundheitsorganisationen auf der ganzen Welt übernommen. Die aktuelle Version von FHIR ist R4 (Release 4). Der FHIR-Dienst unterstützt R4 und auch die vorherige Version DES STANDARD3 (Standard for Trial Use 3). Weitere Informationen zu FHIR finden Sie auf [HL7.org](http://hl7.org/fhir/summary.html).

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>Werden die Daten hinter den FHIR-APIs in Azure gespeichert?

Ja. Die Daten werden in verwalteten Datenbanken in Azure gespeichert. Der FHIR-Dienst in den Azure Healthcare-APIs bietet keinen direkten Zugriff auf den zugrunde liegenden Datenspeicher.

### <a name="what-identity-provider-do-you-support"></a>Welcher Identitätsanbieter wird unterstützt?

Derzeit wird Microsoft Azure Active Directory als Identitätsanbieter unterstützt.

### <a name="what-fhir-version-do-you-support"></a>Welche FHIR-Version wird unterstützt?

Wir unterstützen die Versionen 4.0.0 und 3.0.1.

Ausführliche Informationen finden Sie unter [Unterstützte Features](fhir-features-supported.md). Informationen zu den Änderungen zwischen FHIR-Versionen (also zwischen STU3 und R4) finden Sie im [Versionsverlauf für HL7 FHIR](https://hl7.org/fhir/R4/history.html).

### <a name="what-is-the-difference-between-the-azure-api-for-fhir-and-the-fhir-service-in-the-healthcare-apis"></a>Was ist der Unterschied zwischen dem Azure API for FHIR und dem FHIR-Dienst in den ApIs für das Gesundheitswesen?

Der FHIR-Dienst ist unsere Implementierung der FHIR-Spezifikation, die sich in den Azure Healthcare-APIs befindet. Dadurch können Sie einen FHIR-Dienst und einen DICOM-Dienst in einem einzigen Arbeitsbereich verwenden. Die Azure API for FHIR war unser erstes allgemeines Produkt und ist weiterhin als eigenständiges Produkt verfügbar. Die wichtigsten Featureunterschiede sind:

* Der FHIR-Dienst hat einen Grenzwert von 4 TB und befindet sich in der öffentlichen Vorschau, während der Azure API for FHIR mehr als 4 TB unterstützt und die allgemeine Version ist.
* Der FHIR-Dienst unterstützt [Transaktionsbündel.](https://www.hl7.org/fhir/http.html#transaction)
* Das Azure API for FHIR verfügt über weitere Plattformfeatures (z. B. Private Link, vom Kunden verwaltete Schlüssel und Protokollierung), die noch nicht im FHIR-Dienst in den Azure Healthcare-APIs verfügbar sind. Weitere Informationen zu diesen Features finden Sie in der GA.

### <a name="whats-the-difference-between-fhir-service-in-the-azure-healthcare-apis-and-the-open-source-fhir-server"></a>Worin besteht der Unterschied zwischen dem FHIR-Dienst in den Azure Healthcare-APIs und dem Open-Source-FHIR-Server?

Der FHIR-Dienst in den Azure Healthcare-APIs ist eine gehostete und verwaltete Version des Open-Source-Microsoft FHIR-Servers für Azure. Im verwalteten Dienst stellt Microsoft die gesamte Wartung und alle Updates zur Verfügung.

Bei Verwendung des FHIR-Servers für Azure haben Sie direkten Zugriff auf die zugrunde liegenden Dienste, sind aber auch für die Wartung und Aktualisierung des Servers sowie für alle erforderlichen Complianceaufgaben zuständig, wenn Sie PHI-Daten speichern.

### <a name="in-which-regions-is-the-fhir-service-available"></a>In welchen Regionen ist der FHIR-Dienst verfügbar?

Wir erweitern den globalen Speicherbedarf der Gesundheits-APIs kontinuierlich basierend auf den Kundenanforderungen. Der FHIR-Dienst ist derzeit in 25 Regionen verfügbar, darunter zwei US-Regierungregionen: Australien, Osten, Brasilien,*Süden, Kanada, Mitte, Indien,* Mitte, Asien, Osten, USA, Osten *2, USA, Osten, Deutschland,**Norden, Deutschland,** Westen-Mitte, Deutschland, Norden, Japan, Osten, Japan,* Westen, Südkorea, Mitte*, USA, Norden; Europa, Norden; Südafrika, Norden, Usa, Süden-Mitte, Asien, Südosten, Schweiz, Norden,  Vereinigtes Königreich, Süden, Vereinigtes Königreich, Westen, USA, Westen-Mitte, Europa, Westen; USA, Westen 2, USGov Virginia, USGov Arizona.

*gibt private Vorschauregionen an. ** gibt nur die Notfallwiederherstellungsregion an.

Weitere Informationen finden Sie in der Unterstützung [für geografische Regionen.](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=all)

### <a name="where-can-i-see-what-is-releasing-into-the-fhir-service"></a>Wo kann ich sehen, was im FHIR-Dienst veröffentlicht wird?

Informationen dazu, was im FHIR-Dienst veröffentlicht wird, [](https://github.com/microsoft/fhir-server/releases) finden Sie in der Version des Open-Source-FHIR-Servers. Wir haben daran gearbeitet, Elemente mit FHIR-Service zu kennzeichnen, wenn sie für den verwalteten Dienst veröffentlicht werden und in der Regel zwei Wochen nach der Veröffentlichung auf der Releaseseite in Open-Source verfügbar sind. Wir haben außerdem Anweisungen zum Testen des Builds [hier](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) eingeschlossen, wenn Sie Tests in Ihrer eigenen Umgebung durchführen möchten. Wir evaluieren, wie sich zusätzliche verwaltete Dienstupdates am besten teilen lassen.

### <a name="what-is-smart-on-fhir"></a>Was ist SMART on FHIR?

Bei SMART (Substitutable Medical Applications and Reusable Technology) on FHIR handelt es sich um eine Reihe offener Spezifikationen für die Integration von Partneranwendungen in FHIR-Server und andere IT-Systeme im Gesundheitswesen, wie elektronische Patientenakten und den Austausch von Gesundheitsinformationen. Indem Sie eine SMART on FHIR-Anwendung erstellen, können Sie sicherstellen, dass auf Ihre Anwendung zugegriffen und diese von einer Vielzahl unterschiedlicher Systeme genutzt werden kann.
Weitere Informationen zu SMART finden Sie unter [SMART Health IT](https://smarthealthit.org/).

### <a name="where-can-i-find-what-version-of-fhir-is-running-on-my-database"></a>Wo sehe ich, welche Version von FHIR für meine Datenbank ausgeführt wird?

Die genaue FHIR-Version, die in der Capability-Anweisung verfügbar gemacht wird, finden Sie unter der Eigenschaft "fhirVersion" (FHIR-URL/Metadaten).

## <a name="fhir-implementations-and-specifications"></a>FHIR-Implementierungen and -Spezifikationen

### <a name="can-i-create-a-custom-fhir-resource"></a>Kann ich eine benutzerdefinierte FHIR-Ressource erstellen?

Wir lassen keine benutzerdefinierten FHIR-Ressourcen zu. Wenn Sie eine benutzerdefinierte FHIR-Ressource benötigen, können Sie eine benutzerdefinierte Ressource auf Grundlage der [Basisressource](http://www.hl7.org/fhir/basic.html) mit Erweiterungen erstellen. 

### <a name="are-extensions-supported-on-the-fhir-service"></a>Werden [Erweiterungen](https://www.hl7.org/fhir/extensibility.html) für den FHIR-Dienst unterstützt?

Wir erlauben Ihnen, alle gültigen FHIR-JSON-Daten in den Server zu laden. Wenn Sie die Strukturdefinition, die die Erweiterung definiert, speichern möchten, können Sie diese als Strukturdefinitionsressource speichern. Um nach Erweiterungen zu suchen, müssen Sie [Ihre eigenen Suchparameter definieren.](how-to-do-custom-search.md) 

### <a name="what-is-the-limit-on-_count"></a>Welcher Grenzwert gilt für _count?

Der aktuelle Grenzwert für _count ist 1000. Wenn Sie _count 1000 festlegen, erhalten Sie im Paket eine Warnung, dass nur 1.000 Datensätze angezeigt werden.

### <a name="can-i-post-a-bundle-to-the-fhir-service"></a>Kann ich ein Paket im FHIR-Dienst posten?

Wir unterstützen derzeit das Posten [von Batchpaketen](https://www.hl7.org/fhir/valueset-bundle-type.html) und das Posten [von Transaktionsbündeln](https://www.hl7.org/fhir/http.html#transaction) im FHIR-Dienst.

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-fhir-service"></a>Wie kann ich alle Ressourcen für einen einzelnen Patienten im FHIR-Dienst erhalten?

Wir unterstützen den [$patient-alles-Vorgang,](patient-everything.md) mit dem Sie alle Daten zu einem einzelnen Patienten erhalten. 

## <a name="using-the-fhir-service"></a>Verwenden des FHIR-Diensts

### <a name="where-can-i-see-some-examples-of-using-the-fhir-service-within-a-workflow"></a>Wo finde ich einige Beispiele für die Verwendung des FHIR-Diensts in einem Workflow?

Auf der [GitHub-Seite zur Gesundheitsarchitektur (Health Architecture)](https://github.com/microsoft/health-architectures) finden Sie eine Sammlung von Referenzarchitekturen.

### <a name="where-can-i-see-an-example-of-connecting-a-web-application-to-fhir-service"></a>Wo kann ich ein Beispiel für das Verbinden einer Webanwendung mit dem FHIR-Dienst sehen?

Die [GitHub-Seite zur Integritätsarchitektur](https://aka.ms/health-architectures) enthält Beispielanwendungen und -szenarien. Es wird veranschaulicht, wie sie eine Webanwendung mit dem FHIR-Dienst verbinden.
