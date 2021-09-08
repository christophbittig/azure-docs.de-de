---
title: Azure Purview-Produktglossar
description: Ein Glossar, in dem die in Azure Purview verwendete Terminologie definiert ist
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.topic: conceptual
ms.date: 08/16/2021
ms.openlocfilehash: 20fdf362524a7a1a801aabd5a307b41216a81f31
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122351164"
---
# <a name="azure-purview-product-glossary"></a>Azure Purview-Produktglossar

Unten finden Sie ein Glossar, in dem die in Azure Purview verwendete Terminologie definiert ist.

## <a name="annotation"></a>Anmerkung
Informationen, die Datenassets in Azure Purview zugeordnet sind, z. B. Glossarbegriffe und Klassifizierungen. Nachdem der Anwendung können Anmerkungen in der Suche verwendet werden, um die Ermittlung der Datenassets zu unterstützen. 
## <a name="approved"></a>Genehmigt
Der Status einer Anforderung, die von der angegebenen Person oder Gruppe, die zum Ändern des Status der Anforderung berechtigt ist, als zufriedenstellend akzeptiert wurde. 
## <a name="asset"></a>Asset
Beliebiges einzelnes Objekt, das in einem Azure Purview-Datenkatalog gespeichert ist.
> [!NOTE]
> Ein einzelnes Objekt im Katalog kann potenziell viele Objekte im Speicher darstellen, z. B. ist ein Ressourcensatz ein Asset, das aber aus vielen Partitionsdateien im Speicher besteht.
## <a name="azure-information-protection"></a>Azure Information Protection
Eine Cloudlösung, die die Bezeichnung von Dokumenten und E-Mails zum Klassifizieren und Schützen von Informationen unterstützt. Bezeichnete Elemente können durch Verschlüsselung geschützt, mit einem Wasserzeichen markiert oder auf bestimmte Aktionen oder Benutzer beschränkt werden und sind an das Element gebunden. Diese cloudbasierte Lösung basiert auf Azure Rights Management Service (RMS) zum Erzwingen von Einschränkungen. 
## <a name="business-glossary"></a>Unternehmensglossar
Eine durchsuchbare Liste spezieller Begriffe, die eine Organisation verwendet, um wichtige Unternehmensbegriffe und deren Definitionen zu beschreiben. Die Verwendung eines Unternehmensglossars ermöglicht eine konsistente Datennutzung in der gesamten Organisation. 
## <a name="classification-report"></a>Klassifizierungsbericht
Ein Bericht, der wichtige Klassifizierungsdetails zu den überprüften Daten anzeigt.  
## <a name="classification"></a>Klassifizierung
Ein Anmerkungstyp, der verwendet wird, um ein Attribut eines Assets oder einer Spalte zu identifizieren, z. B. „Alter“, „E-Mail-Adresse“ und „Anschrift“. Diese Attribute können bei Überprüfungen zugewiesen oder manuell hinzugefügt werden. 
## <a name="classification-rule"></a>Klassifizierungsregel
Eine Klassifizierungsregel ist ein Satz von Bedingungen, die bestimmen, wie überprüfte Daten klassifiziert werden sollen, wenn Inhalt dem angegebenen Muster entspricht.
## <a name="classified-asset"></a>Klassifizierte Ressource
Eine Ressource, bei der Azure Purview das Schema extrahiert und während einer automatisierten Überprüfung Klassifizierungen anwendet. Der Überprüfungsregelsatz bestimmt, welche Objekte klassifiziert werden. Wenn die Ressource als Kandidat für die Klassifizierung gilt und zur Überprüfungszeit keine Klassifizierungen angewendet werden, wird die Ressource weiterhin als klassifizierte Ressource betrachtet.
## <a name="column-pattern"></a>Spaltenmuster
Ein regulärer Ausdruck in einer Klassifizierungsregel, der den Spaltennamen entspricht, die einander zugeordnet werden sollen.
## <a name="contact"></a>Contact
Eine Person, die einer Entität im Datenkatalog zugeordnet ist 
## <a name="control-plane-operation"></a>Vorgang auf Steuerungsebene
Vorgänge, die Ressourcen in Ihrem Abonnement verwalten, z. B. rollenbasierte Zugriffssteuerung und Azure-Richtlinien, die an den Azure Resource Manager-Endpunkt gesendet werden. 
## <a name="credential"></a>Anmeldeinformationen
Eine Überprüfung der Identität oder des Tools, die bzw. das in einem Zugriffssteuerungssystem verwendet wird. Anmeldeinformationen können zum Authentifizieren einer Person oder Gruppe verwendet werden, um Zugriff auf ein Datenasset zu gewähren. 
## <a name="data-catalog"></a>Datenkatalog
Azure Purview-Features, mit denen Kunden die Metadaten für Assets in Ihrem Datenbestand anzeigen und verwalten können.
## <a name="data-map"></a>Datenzuordnung
Azure Purview-Features, mit denen Kunden ihren Datenbestand verwalten können, z. B. Überprüfung, Herkunft und Bewegung.
## <a name="data-pattern"></a>Datenmuster
Ein regulärer Ausdruck, der den Daten entspricht, die in einem Datenfeld gespeichert sind. Ein Datenmuster für die Mitarbeiter-ID kann beispielsweise „Employee{GUID}“ lauten.
## <a name="data-plane-operation"></a>Datenebenenvorgang
Ein Vorgang innerhalb einer bestimmten Azure Purview-Instanz, z. B. das Bearbeiten einer Ressource oder das Erstellen eines Glossarbegriffs. Jede Instanz verfügt über vordefinierte Rollen wie „Datenleser“ und „Datenkurator“, die steuern, welche Datenebenenvorgänge ein Benutzer ausführen kann.
## <a name="discovered-asset"></a>Ermittelte Ressource
Eine Ressource, die von Azure Purview während des Überprüfungsvorgangs in einer Datenquelle identifiziert wird. Die Anzahl der ermittelten Ressourcen schließt alle Dateien oder Tabellen vor der Ressourcensatzgruppierung ein.
## <a name="distinct-match-threshold"></a>Schwellenwert für Datenunterschiede
Die Gesamtanzahl der unterschiedlichen Datenwerte, die in einer Spalte gefunden werden müssen, bevor der Scanner das Datenmuster für die Spalte ausführt. Beispielsweise erfordert ein eindeutiger Übereinstimmungsschwellenwert für die Mitarbeiter-ID von acht, dass unter den Stichprobenwerten in der Spalte, die mit dem für die Mitarbeiter-ID festgelegten Datenmuster übereinstimmen, mindestens acht eindeutige Datenwerte sind.
## <a name="expert"></a>Experte
Eine Person innerhalb einer Organisation, die den vollständigen Kontext eines Datenassets oder Glossarbegriffs versteht.
## <a name="full-scan"></a>Vollständige Überprüfung
Eine Überprüfung, die alle Ressourcen innerhalb eines ausgewählten Bereichs einer Datenquelle verarbeitet.
## <a name="fully-qualified-name-fqn"></a>Vollqualifizierter Name (FQN)
Ein Pfad, der den Speicherort eines Assets innerhalb seiner Datenquelle definiert.  
## <a name="glossary-term"></a>Glossarbegriff
Ein Eintrag im Unternehmensglossar, der ein Konzept definiert, das speziell für die jeweilige Organisation gilt. Glossarbegriffe können Informationen zu Synonymen, Akronymen und verwandten Begriffen enthalten.
## <a name="incremental-scan"></a>Inkrementelle Überprüfung
Eine Überprüfung, die Ressourcen erkennt und verarbeitet, die seit der vorherigen erfolgreichen Überprüfung erstellt, geändert oder gelöscht wurden. Um eine inkrementelle Überprüfung auszuführen, muss mindestens eine vollständige Überprüfung für die Quelle durchgeführt worden sein.
## <a name="ingested-asset"></a>Erfasste Ressource
Eine Ressource, die überprüft, ggf. klassifiziert und der Azure Purview Data Map hinzugefügt wurde. Erfasste Ressourcen können innerhalb des Datenkatalogs durch automatisierte Überprüfung oder externe Verbindungen wie Azure Data Factory und Azure Synapse ermittelt und genutzt werden.
## <a name="insights"></a>Einblicke
Ein Bereich in Azure Purview, in dem Sie Berichte anzeigen können, die Informationen zu Ihren Daten zusammenfassen.
## <a name="integration-runtime"></a>Integrationslaufzeit
Die Computeinfrastruktur, die zum Überprüfen in einer Datenquelle verwendet wird.
## <a name="lineage"></a>Herkunft
Datentransformationen und Datenflüsse während ihrer Bewegung vom Ursprung zum Ziel. Das Verständnis dieser Datenflüsse innerhalb des Datenbestands hilft Organisationen bei der Darstellung des Datenverlaufs und bei der Problembehandlung oder Auswirkungsanalyse. 
## <a name="management-center"></a>Verwaltungscenter
Ein Bereich in Azure Purview, in dem Sie Verbindungen, Benutzer, Rollen und Anmeldeinformationen verwalten können.
## <a name="minimum-match-threshold"></a>Schwellenwert für Mindestübereinstimmung
Der Mindestprozentsatz für die Übereinstimmung der unterschiedlichen Datenwerte in einer Spalte, der von der Überprüfung ermittelt werden muss, damit die Klassifizierung angewendet wird. 

Beispielsweise erfordert ein Schwellenwert für die Mindestübereinstimmung von 60 % für die Mitarbeiter-ID, dass 60 % aller unterschiedlichen Werte unter den Stichprobendaten in einer Spalte mit dem für Mitarbeiter-ID festgelegten Datenmuster übereinstimmen. Wenn die Überprüfung eine Stichprobe von 128 Werten in einer Spalte erhebt und in dieser Spalte 60 unterschiedliche Werte findet, müssen mindestens 36 der unterschiedlichen Werte (60 %) mit dem Mitarbeiter-ID-Datenmuster übereinstimmen, damit die Klassifizierung angewendet werden kann.
## <a name="on-premises-data"></a>Lokale Daten
Daten in einem Rechenzentrum, die von einem Kunden gesteuert werden, d. h. nicht in der Cloud oder als Software-as-a-Service (SaaS). 
## <a name="owner"></a>Besitzer
Eine Person oder Gruppe, die für die Verwaltung eines Datenassets verantwortlich ist.
## <a name="pattern-rule"></a>Musterregel
Eine Konfiguration, die überschreibt, wie Azure Purview Ressourcen als Ressourcensätze gruppiert und im Katalog anzeigt.
## <a name="purview-instance"></a>Azure Purview-Instanz
Eine einzelne Azure Purview-Ressource. 
## <a name="registered-source"></a>Registrierte Quelle
Eine Quelle, die einer Azure Purview-Instanz hinzugefügt wurde und jetzt als Teil des Datenkatalogs verwaltet wird. 
## <a name="related-terms"></a>Verwandte Begriffe
Glossarbegriffe, die mit anderen Begriffen innerhalb der Organisation verknüpft sind.  
## <a name="resource-set"></a>Ressourcensatz
Ein einzelnes Asset, das viele partitionierte Dateien oder Objekte im Speicher darstellt. Azure Purview speichert z. B. eine partitionierte Apache Spark-Ausgabe als einen einzelnen Ressourcensatz und nicht als einzelne Assets für jede einzelne Datei. 
## <a name="role"></a>Role
Berechtigungen, die einem Benutzer innerhalb einer Azure Purview-Instanz zugewiesen sind. Rollen wie „Purview-Datenkurator“ oder „Purview-Datenleseberechtigter“ bestimmen, welche Aktionen innerhalb des Produkts ausgeführt werden können.
## <a name="scan"></a>Überprüfen
Ein Azure Purview-Prozess, der eine Quelle oder einen Satz von Quellen untersucht und deren Metadaten im Datenkatalog erfasst. Überprüfungen können manuell oder nach einem Zeitplan mithilfe eines Überprüfungstriggers ausgeführt werden. 
## <a name="scan-ruleset"></a>Überprüfungsregelsatz
Eine Reihe von Regeln, die definieren, welche Datentypen und Klassifizierungen eine Überprüfung in einem Katalog erfasst. 
## <a name="scan-trigger"></a>Überprüfungstrigger
Ein Zeitplan für die Wiederholung einer Überprüfung.
## <a name="search"></a>Suche
Ein Datenermittlungsfeature von Azure Purview, das eine Liste von Ressourcen zurückgibt, die mit einem Schlüsselwort übereinstimmen. 
## <a name="search-relevance"></a>Suchrelevanz
Die Bewertung von Datenressourcen, die die Reihenfolge der Suchergebnisse bestimmen, wird zurückgegeben. Mehrere Faktoren bestimmen die Relevanz einer Ressource.
## <a name="self-hosted-integration-runtime"></a>Selbstgehostete Integrationslaufzeit
Eine Integration Runtime, die auf einem lokalen oder virtuellen Computer in einem privaten Netzwerk installiert ist, der zum Herstellen einer Verbindung mit lokalen Daten oder in einem privaten Netzwerk verwendet wird.
## <a name="sensitivity-label"></a>Vertraulichkeitsbezeichnung
Anmerkungen, die die Daten einer Organisation klassifizieren und schützen. Azure Purview lässt sich in Microsoft Information Protection integrieren, um Vertraulichkeitsbezeichnungen zu erstellen. 
## <a name="sensitivity-label-report"></a>Bericht der Vertraulichkeitsbezeichnungen
Eine Zusammenfassung, welche Vertraulichkeitsbezeichnungen auf den Datenbestand angewendet werden. 
## <a name="service"></a>Dienst
Ein Produkt, das eigenständige Funktionen bereitstellt und Kunden per Abonnement oder Lizenz zur Verfügung steht. 
## <a name="source"></a>`Source`
Ein System, in dem Daten gespeichert werden. Quellen können an verschiedenen Orten gehostet werden, z. B. in einer Cloud oder lokal. Sie registrieren und überprüfen Quellen, damit Sie sie in Azure Purview verwalten können. 
## <a name="source-type"></a>Quelltyp
Eine Kategorisierung der registrierten Quellen, die in einer Azure Purview-Instanz verwendet werden, z. B. Azure SQL-Datenbank, Azure Blob Storage, Amazon S3 oder SAP ECC. 
## <a name="steward"></a>Inhaltsverantwortlicher
Eine Person, die die Standards für einen Glossarbegriff definiert. Sie ist für die Aufrechterhaltung von Qualitätsstandards, Nomenklatur und Regeln für die zugewiesene Entität verantwortlich. 
## <a name="term-template"></a>Begriffsvorlage
Eine Definition von Attributen, die in einem Glossarbegriff enthalten sind. Benutzer können entweder die systemdefinierte Begriffsvorlage verwenden oder eine eigene erstellen, um benutzerdefinierte Attribute einzuschließen.
## <a name="next-steps"></a>Nächste Schritte

Informationen zu den ersten Schritten mit Azure Purview finden Sie unter [Schnellstart: Erstellen eines Azure Purview-Kontos im Azure-Portal](create-catalog-portal.md).