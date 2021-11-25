---
title: Best Practices für das Überprüfen von Datenquellen in Purview
description: Dieser Artikel erläutert Best Practices zum Registrieren und Überprüfen verschiedener Datenquellen in Azure Purview.
author: athenads
ms.author: athenadsouza
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 10/08/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2313d8b544355dd2bdf5ca528faffbcc17f848e9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132309659"
---
# <a name="azure-purview-scanning-best-practices"></a>Best Practices für die Azure Purview-Überprüfung

Azure Purview unterstützt die automatisierte Überprüfung von lokalen, Multi-Cloud- und SaaS-Datenquellen. Beim Durchführen einer Überprüfung wird der Prozess zum Erfassen von Metadaten aus den registrierten Datenquellen aufgerufen. Die am Ende des Überprüfungs- und Zusammenstellungsprozesses generierten Metadaten umfassen technische Metadaten wie etwa die Namen von Datenressourcen (Tabellennamen/Dateinamen), Dateigröße, Spalten, Datenherkunft usw. Für strukturierte Datenquellen (z. B. ein relationales Datenbank-Managementsystem) werden auch die Schemadetails erfasst. Der Überprüfungsprozess wendet automatische Klassifizierungskennzeichen auf die Schemaattribute an, die auf dem konfigurierten Scan-Regelsatz basieren, sowie Sensibilitätskennzeichen, wenn Ihr Purview-Konto mit einem Microsoft 365 Security & Compliance Center verbunden ist.

## <a name="intended-audience"></a>Zielpublikum

- Datenarchitektur-Team
- Datenkurator
- Datenquellenadministrator
- Data Engineer

## <a name="why-do-you-need-best-practices-to-manage-data-sources"></a>Weshalb werden Best Practices für die Verwaltung von Datenquellen benötigt?

Mithilfe der Best Practices können Sie Kosten optimieren, erstklassige Betriebsprozesse aufbauen, die Einhaltung von Sicherheitsvorschriften gewährleisten und die Leistungseffizienz verbessern.
Die Entwurfsüberlegungen und -empfehlungen wurden basierend auf den wichtigsten Schritten des Überprüfungsprozesses strukturiert.

## <a name="register-source-and-establish-connection"></a>Registrieren der Quelle und Herstellen einer Verbindung

### <a name="design-considerations"></a>Überlegungen zum Entwurf

- Die auf die Strategie des Unternehmens abgestimmte Hierarchie (geografisch, nach Geschäftsfunktion, Datenquelle usw.), mit der die zu registrierenden und zu überprüfenden Datenquellen definiert werden, muss mithilfe von Sammlungen erstellt werden.

- Standardmäßig können Sie Datenquellen nicht mehrmals in einem einzelnen Purview-Konto registrieren. Durch diese Architektur wird vermieden, dass derselben Datenquelle unterschiedliche Zugriffskontrollen zugewiesen werden.

### <a name="design-recommendations"></a>Entwurfsempfehlungen

- Wenn die Metadaten derselben Datenquelle von mehreren Teams genutzt werden, können Sie die Datenquelle in einer übergeordneten Sammlung registrieren und verwalten und in jeder Untersammlung entsprechende Überprüfungen erstellen, sodass die relevanten Objekte in jeder untergeordneten Sammlung aufgeführt werden. Quellen ohne übergeordnete Objekte werden in der Zuordnungsansicht in einem gestrichelt umrahmten Feld ohne Pfeile zur Verknüpfung mit übergeordneten Elementen gruppiert.

  :::image type="content" source="media/concept-best-practices/scanning-parent-child.png" alt-text="Screenshot: Azure Purview mit einer Datenquelle, die als übergeordnete Sammlung registriert ist.":::

- Verwenden Sie die Option **Mehrere Azure-Quellen**, wenn Sie mehrere Quellen (Azure-Abonnements oder Ressourcengruppen) in der Azure-Cloud registrieren müssen. Weitere Informationen finden Sie in folgenden Dokumentation:
    * [Überprüfen Sie mehrerer Quellen in Azure Purview](./register-scan-azure-multiple-sources.md)
    * [Bereitschaftsüberprüfung für Datenquellen im großen Stil](./tutorial-data-sources-readiness.md) 
    * [Konfigurieren des Zugriffs auf Datenquellen für Azure Purview MSI in großem Stil](./tutorial-msi-configuration.md)
     
- Nachdem eine Datenquelle registriert wurde, können Sie dieselbe Quelle mehrmals überprüfen, falls dieselbe Quelle von verschiedenen Teams oder Geschäftseinheiten unterschiedlich verwendet wird.

Weitere Informationen zum Definieren einer Hierarchie zum Registrieren von Datenquellen finden Sie in den [bewährten Methoden für Sammlungsarchitekturen](./concept-best-practices-collections.md).

## <a name="scanning"></a>Überprüfung

### <a name="design-considerations"></a>Überlegungen zum Entwurf

- Nachdem die Datenquelle registriert wurde, müssen Sie eine Überprüfung einrichten, um die automatisierte und sichere Überprüfung und Zusammenstellung von Metadaten zu verwalten.
- Beim Einrichten der Überprüfung muss Folgendes konfiguriert werden: Name der Überprüfung, Überprüfungsbereich, Integration Runtime, Häufigkeit der Überprüfungsauslösung, Überprüfungsregelsatz und ein Ressourcensatz, der für jede Datenquelle/pro Überprüfungshäufigkeit eindeutig sein muss.
- Entscheiden Sie zunächst unter Berücksichtigung Ihrer Datenquellentypen und Netzwerkanforderungen, welche Authentifizierungsmethode und Integration Runtime für Ihr Szenario benötigt wird.

### <a name="design-recommendations"></a>Entwurfsempfehlungen

Um unerwartete Kosten und Überarbeitungen zu vermeiden, empfiehlt es sich, die folgende Reihenfolge beim Einrichten der Überprüfung einzuplanen und zu befolgen, nachdem Sie Ihre Quelle in der relevanten [Sammlung](./how-to-create-and-manage-collections.md) registriert haben:

1. Identifizieren Sie Ihre Klassifizierungsanforderungen anhand der integrierten Systemklassifizierungsregeln, und/oder erstellen Sie bei Bedarf spezifische benutzerdefinierte Klassifizierungsregeln basierend auf den jeweiligen Branchen-, Geschäfts- und/oder regionalen Anforderungen (diese sind nicht vorkonfiguriert).
    - Verwenden Sie reguläre Ausdrücke, wenn das zu klassifizierende Datenelement durch ein Muster regulärer Ausdrücke konsistent ausgedrückt oder das Muster aus der Datendatei generiert werden kann. Stellen Sie sicher, dass die Stichprobendaten die Gesamtheit der Daten widerspiegeln.
    - Verwenden Sie die Wörterbuchmethode, wenn erwartet wird, dass die Liste der Werte im Datenelement einem bestimmten Satz von Daten entspricht.
    - Ausführliche Informationen finden Sie im Tutorial zum [Erstellen von benutzerdefinierten Klassifizierungen und Klassifizierungsregeln](./create-a-custom-classification-and-classification-rule.md).

2. Der Klassifizierungsprozess kann zu einer längeren Überprüfungsdauer führen, wodurch zusätzliche Kosten anfallen. Daher wird empfohlen, nur die Klassifizierungsregeln auszuwählen, die für die zu überprüfende Datenquelle relevant sind.
    - Erstellen Sie Überprüfungsregelsätze nach Quelle, und wählen Sie die relevanten Klassifizierungsregeln aus, einschließlich konfigurierter System- und benutzerdefinierter Klassifizierungsregeln.
    - Stellen Sie beim Einrichten eines Überprüfungsregelsatzes Folgendes sicher:
        - Überprüfen Sie, ob der standardmäßige Überprüfungsregelsatz des Systems für die zu überprüfende Datenquelle ausreicht, oder definieren Sie Ihren eigenen Überprüfungsregelsatz.
        - Der benutzerdefinierte Überprüfungsregelsatz kann sowohl Systemstandards als auch benutzerdefinierte Regeln enthalten. Deaktivieren Sie deshalb die Regelsätze, die für die zu überprüfenden Datenressourcen nicht relevant sind.
        - Überprüfen Sie immer die Standardregeln des Systems für die Klassifizierung anhand Ihrer spezifischen Branchen-, Geschäfts- und/oder regionalen Anforderungen.
        - Erstellen Sie bei Bedarf einen benutzerdefinierten Regelsatz, um unerwünschte Klassifizierungsbezeichnungen auszuschließen. Der Regelsatz des Systems enthält beispielsweise allgemeine Regierungscodes für die ganze Welt, nicht nur für die USA. Ihre Daten können also auch mit dem Muster eines anderen Typs übereinstimmen, wie z. B. „Belgische Führerscheinnummer“.
        - Beschränken Sie aus Gründen der Übersichtlichkeit benutzerdefinierte Klassifizierungsregeln auf die relevantesten Bezeichnungen (vermeiden Sie eine Verknüpfung von zu vielen Bezeichnungen mit einer Ressource).
        - Updateszenario: Eine vollständige Überprüfung wird ausgelöst, wenn Sie die benutzerdefinierte Klassifizierung oder den Überprüfungsregelsatz ändern. Daher wird empfohlen, den Klassifizierungs- und Überprüfungsregelsatz entsprechend zu konfigurieren, um Überarbeitungen und kostspielige vollständige Überprüfungen zu vermeiden.
        - Die mehrsprachige Datenklassifizierung wird noch nicht unterstützt.
        - Stellen Sie bei der Erstellung einer benutzerdefinierten Klassifizierungsüberprüfung sicher, dass die Musterregel und der Schwellenwert überprüft und entsprechend konfiguriert wurden, um eine fehlerhafte Klassifizierung zu verhindern.
        - Beim Überprüfen eines Speicherkontos verwendet Azure Purview eine Reihe definierter Muster, um zu bestimmen, ob eine Gruppe von Ressourcen einen Ressourcensatz bildet. Mit den Musterregeln für Ressourcensätze können Sie die Art und Weise anpassen oder außer Kraft setzen, in der Azure Purview erkennt, welche Ressourcen als Ressourcensätze gruppiert sind und wie sie innerhalb des Katalogs angezeigt werden. Weitere Informationen finden Sie unter [Erstellen von benutzerdefinierten Klassifizierungen](./how-to-resource-set-pattern-rules.md). 
        > [!NOTE]
        > Dieses Feature ist mit Kosten verbunden, Einzelheiten finden Sie in der [Preisübersicht](https://azure.microsoft.com/pricing/details/azure-purview/).

3. Einrichten einer Überprüfung für die registrierten Datenquellen
    - **Name der Überprüfung**: Standardmäßig verwendet Purview die Namenskonvention **SCAN-[A-Z][a-z][a-z]** , die nicht hilfreich ist, wenn Sie versuchen, eine von Ihnen durchgeführte Überprüfung zu identifizieren. Verwenden Sie als Best Practice eine aussagekräftige Namenskonvention.  Ein Beispiel für eine Benennung der Überprüfung wäre _Umgebung-Quelle-Häufigkeit-Uhrzeit_. Damit würde „DEVODS-Daily-0200“ für eine tägliche Überprüfung um 02:00 Uhr stehen.
    
    - **Authentifizierung**
        - Azure Purview bietet verschiedene Authentifizierungsmethoden zum Überprüfen der Datenquellen, abhängig vom Typ der Quelle (Azure-Cloud, lokal oder Drittanbieterquelle). Es wird empfohlen, für die Authentifizierungsmethode das Prinzip der geringsten Rechte in der folgenden Reihenfolge zu befolgen:
            - Purview MSI – Verwaltete Identität (z. B. für Azure Data Lake Gen2-Quellen)
            - Vom Benutzer zugewiesene verwaltete Identität
            - Dienstprinzipal
            - SQL Authentifizierung (z. B. für lokale oder Azure SQL-Quellen)
            - Kontoschlüssel oder Standardauthentifizierung (z. B. für SAP S/4HANA-Quellen)
            > [!Note]
            > Wenn Sie für das Speicherkonto eine Firewall aktiviert haben, müssen Sie beim Einrichten einer Überprüfung als Authentifizierungsmethode Verwaltete Identität verwenden.
            > Beim Einrichten neuer Anmeldeinformationen darf der Anmeldeinformationsname nur _Buchstaben, Zahlen, Unterstriche und Bindestriche_ enthalten. 

    - **Integrationslaufzeit**
        - Verwenden Sie nach Möglichkeit eine Azure Integration Runtime mit automatischer Auflösung.
        - Wenn der öffentliche Endpunkt für die zu überprüfenden Datenquellen eingeschränkt ist, müssen Sie eine selbstgehostete Integration Runtime (SHIR) einrichten und Anmeldeinformationen erstellen. Mithilfe einer SHIR kann der Kunde die Überprüfung auf seinem Computer ausführen. Sie ersetzt die Azure-Compute-Ressource, auf der die Überprüfung normalerweise ausgeführt wird, durch den Rechner des Kunden.
        - Wenn Sie einen privaten Endpunkt für die Erfassung nutzen, müssen Sie eine selbstgehostete Integration Runtime zum Überprüfen der Datenquellen verwenden.
        - Stellen Sie außerdem sicher, dass die [neueste Version](https://www.microsoft.com/download/details.aspx?id=39717) der selbstgehosteten Integration Runtime installiert ist.
        - Nachdem eine SHIR gelöscht wurde, führen alle darauf basierenden Überprüfungen zu einem Fehler.
      <!--
        - In Azure Purview there is no concept of SHIR HA or Shared SHIR.
        -->
        - Stellen Sie bei Verwendung einer selbstgehosteten Integration Runtime sicher, dass ausreichend Arbeitsspeicher für die zu überprüfende Datenquelle vorhanden ist. Wenn Sie z. B. eine SHIR zum Überprüfen einer SAP-Quelle verwenden und ein Fehler aufgrund von zu wenig Arbeitsspeicher gemeldet wird, gehen Sie folgendermaßen vor:
            - Stellen Sie sicher, dass der SHIR-Computer über ausreichend Arbeitsspeicher verfügt (Empfehlung: 128 GB).
            - Legen Sie in der Überprüfungseinstellung den maximal verfügbaren Arbeitsspeicher auf einen geeigneten Wert fest (z. B. 100).
            - Ausführliche Informationen finden Sie in [diesem Dokument](./register-scan-sapecc-source.md#create-and-run-scan).

    - **Bereichsüberprüfung**
        - Wählen Sie bei der Festlegung des Bereichs für die Überprüfung nur die Ressourcen aus, die auf untergeordneter oder übergeordneter Ebene relevant sind. Dadurch werden optimale Überprüfungskosten und eine effiziente Leistung sichergestellt. Alle zukünftigen Ressourcen unterhalb eines bestimmten übergeordneten Elements werden automatisch ausgewählt, wenn das übergeordnete Element vollständig oder teilweise markiert wird. Beispiel:
            - Für Azure SQL-Datenbank oder ADLS Gen2 können Sie den Bereich Ihrer Überprüfung auf bestimmte Teile der Datenquelle (etwa auf Ordner, Sammlungen oder Schemas) festlegen, indem Sie in der Liste die entsprechenden Kontrollkästchen aktivieren.
            - Für Oracle-, Hive-Metastore-Datenbank- und Teradata-Quellen kann eine spezifische Liste der zu exportierenden Schemas über durch Semikolons getrennte Werte oder über Schemanamensmuster unter Verwendung von SQL-LIKE-Ausdrücken angegeben werden.
            - Bei Google Big-Abfragen kann eine spezifische Liste der zu exportierenden Datasets über durch Semikolons getrennte Werte angegeben werden.
            - Wenn Sie eine Überprüfung für ein ganzes AWS-Konto erstellen, können Sie bestimmte Buckets für die Überprüfung auswählen. Wenn Sie eine Überprüfung für ein bestimmtes AWS S3-Konto erstellen, können Sie bestimmte Ordner für die Überprüfung auswählen.
            - Für Erwin können Sie den Bereich ihrer Überprüfung durch Bereitstellen einer durch Semikolons getrennten Liste von Erwin-Modell-Locator-Zeichenfolgen festlegen.
            - Für Cassandra kann eine spezifische Liste der zu exportierenden Schlüsselräume über durch Semikolons getrennte Werte oder über Schlüsselraumnamensmuster unter Verwendung von SQL LIKE-Ausdrücken angegeben werden.
            - Für Looker können Sie den Bereich ihrer Überprüfung über eine durch Semikolons getrennte Liste mit Looker-Projekten angeben.
            - Für einen Power BI-Mandanten können Sie nur angeben, ob der persönliche Arbeitsbereich eingeschlossen oder ausgeschlossen werden soll.
            - Im Allgemeinen wird empfohlen, „Muster ignorieren“ (sofern unterstützt) basierend auf Platzhaltern (z. B. für Data Lakes) zu verwenden, um temporäre Dateien, Konfigurationsdateien, RDMS-Systemtabellen oder Sicherungs-/STG-Tabellen auszuschließen.
            - Vermeiden Sie beim Prüfen von Dokumenten/unstrukturierten Daten das Überprüfen einer großen Anzahl solcher Dokumente, da der Überprüfungsvorgang die ersten 20 MB der Dokumente verarbeitet, was zu einer längeren Überprüfungsdauer führen kann.

    - **Überprüfungsregelsatz**
        - Stellen Sie beim Einrichten des Überprüfungsregelsatzes sicher, dass Sie die zuvor erstellten relevanten System-/benutzerdefinierten Überprüfungsregelsätze konfigurieren.
        - Es steht eine Option zum Erstellen benutzerdefinierter Dateitypen zur Verfügung, und Sie können die Details entsprechend ausfüllen. Derzeit unterstützt Azure Purview nur ein Zeichen als benutzerdefiniertes Trennzeichen. Wenn Sie in Ihren tatsächlichen Daten benutzerdefinierte Trennzeichen wie z. B. ~ verwenden, müssen Sie einen neuen Überprüfungsregelsatz erstellen.

    - **Überprüfungstyp und -zeitplan**
        - Der Überprüfungsprozess kann so konfiguriert werden, dass vollständige oder inkrementelle Überprüfungen ausgeführt werden.
        - Der Überprüfungsprozess kann sofort ausgelöst oder für eine regelmäßige Ausführung (wöchentlich oder monatlich) geplant werden, um die Metadaten auf dem neuesten Stand zu halten. Außerdem ist die Ressourcenverwendung auch stark von der Menge der Daten abhängig, die überprüft werden.
        - Es wird empfohlen, die Überprüfungen außerhalb der Geschäftszeiten oder außerhalb der Spitzenzeiten auszuführen, um eine Überlastung der Quelle zu vermeiden.
        - Bei der ersten Überprüfung handelt es sich um eine vollständige Überprüfung, und jede nachfolgende Überprüfung erfolgt inkrementell. Nachfolgende Überprüfungen können als regelmäßige inkrementelle Überprüfungen geplant werden.
        - Die Häufigkeit der Überprüfungen sollte sich nach dem Zeitplan für das Change Management der Datenquelle und/oder nach den Geschäftsanforderungen richten. Beispiel:
            - Wenn sich die Struktur der Quelle möglicherweise wöchentlich ändert (durch Hinzufügung, Änderung oder Löschung neuer Ressourcen oder Felder innerhalb einer Ressource), sollte die Häufigkeit der Überprüfung entsprechend angepasst werden.
            - Wenn die Klassifizierungs-/Vertraulichkeitsbezeichnungen wöchentlich auf dem neuesten Stand sein sollen (z. B. aus rechtlichen Gründen), sollte eine wöchentliche Überprüfung festgelegt werden.
            Wenn einem Quell-Data Lake beispielsweise wöchentlich Partitionsdateien hinzugefügt werden, sollten Sie eher monatliche als wöchentliche Überprüfungen planen, da sich die Metadaten nicht ändern (vorausgesetzt, es gibt keine neuen Klassifizierungsszenarien).
            - Wenn Sie eine Überprüfung planen, die am selben Tag durchgeführt werden soll, an dem sie erstellt wird, muss die Startzeit mindestens eine Minute vor dem Zeitpunkt der Überprüfung liegen.
            - Die maximale Dauer der Überprüfung beträgt sieben Tage (möglicherweise aufgrund von Arbeitsspeicherproblemen), wobei der Erfassungsvorgang nicht berücksichtigt wird. Wenn nach sieben Tagen noch keine Fortschritte verzeichnet wurden, wird die Überprüfung als fehlgeschlagen eingestuft. Für die Erfassung (im Katalog) gilt derzeit keine solche Einschränkung.

    - **Abbrechen von Überprüfungen**
        - Derzeit können Überprüfungen nur abgebrochen oder angehalten werden, wenn der Status der Überprüfung nach dem Auslösen der Überprüfung von „In Warteschlange“ in den Status „In Bearbeitung“ übergegangen ist.
        - Das Abbrechen einer einzelnen untergeordneten Überprüfung wird nicht unterstützt.
        <!--
        - <need to add the concept of a parent and child scan
        - To remove any scanned assets from Purview you should remove asset by asset
        -->

    - **Szenarien für Löschung und Aktualisierung**
        - Wenn ein Feld/eine Spalte, eine Tabelle oder eine Datei nach der Überprüfung aus dem Quellsystem entfernt wird, wird dies erst nach der nächsten geplanten vollständigen/inkrementellen Überprüfung in Purview angezeigt.
        - Eine Ressource kann mit dem Symbol **Löschen** unterhalb des Namens der Ressource aus dem Azure Purview-Katalog gelöscht werden (dadurch wird das Objekt nicht aus der Quelle entfernt). Wenn Sie jedoch eine vollständige Überprüfung für dieselbe Quelle ausführen, wird sie wieder im Katalog erfasst. Wenn Sie stattdessen eine wöchentliche/monatliche Überprüfung (inkrementell) geplant haben, wird die gelöschte Ressource nicht ausgewählt – es sei denn, das Objekt wird in der Quelle geändert (z. B. durch Hinzufügen oder Entfernen einer Spalte in der Tabelle).
        - Wenn Sie eine Aktualisierung auf Ressourcenebene vornehmen, indem Sie z. B. eine Beschreibung, eine Klassifizierung auf Ressourcenebene, einen Glossarbegriff oder einen Kontakt zu einer Ressource hinzufügen, wird bei anschließenden Überprüfungen das Schema der Ressource aktualisiert (neue Spalten und Klassifizierungen werden bei nachfolgenden Überprüfungsläufen erkannt).
        - Wenn Sie eine Aktualisierung auf Spaltenebene durchführen, indem Sie z. B. eine Beschreibung, eine Klassifizierung auf Spaltenebene oder einen Glossarbegriff hinzufügen oder den Datentyp oder den Spaltennamen aktualisieren, wird bei anschließenden Überprüfungen das Schema der Ressource nicht aktualisiert (neue Spalten und Klassifizierungen werden bei nachfolgenden Überprüfungsläufen nicht erkannt).

        Weitere Informationen finden Sie im Tutorial zum [Anzeigen, Bearbeiten und Löschen von Ressourcen](./catalog-asset-details.md).

## <a name="next-steps"></a>Nächste Schritte
-  [Datenquellen verwalten](./manage-data-sources.md)
