---
title: Azure Blockchain Service Benachrichtigung und Anleitung zur Abmeldung
description: Migrieren von Azure Blockchain Service zu einem verwalteten oder selbstverwalteten Blockchainangebot
ms.date: 05/10/2021
ms.topic: how-to
ms.openlocfilehash: 6a767a8e4d31cd76012039f40e688f08708fc0c2
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/07/2021
ms.locfileid: "123541547"
---
# <a name="migrate-azure-blockchain-service"></a>Migrieren von Azure Blockchain Service

Sie können Ledgerdaten von Azure Blockchain Service zu einem alternativen Angebot migrieren.

> [!IMPORTANT]
> Am **10. September 2021** wird Azure Blockchain eingestellt. Migrieren Sie Ledgerdaten von Azure Blockchain Service zu einem alternativen Angebot basierend auf Ihrem Entwicklungsstatus in der Produktion oder Auswertung.

## <a name="evaluate-alternatives"></a>Auswerten von Alternativen

Der erste Schritt bei der Planung einer Migration ist die Auswertung alternativer Angebote. Bewerten Sie die folgenden Alternativen basierend auf Ihrem Entwicklungsstatus in der Produktion oder Auswertung.

### <a name="production-or-pilot-phase"></a>Produktions- oder Pilotphase

Wenn Sie bereits eine Blockchainlösung bereitgestellt und entwickelt haben, die sich in der Produktions- oder Pilotphase befindet, sollten Sie die folgenden Alternativen in Betracht ziehen.

#### <a name="quorum-blockchain-service"></a>Quorum Blockchain Service

Quorum Blockchain Service ist ein verwaltetes Angebot von ConsenSys in Azure, das Quorum als Ledgertechnologie unterstützt.

- **Verwaltetes Angebot**: Der Quorum Blockchain-Dienst hat im Vergleich zu Azure Blockchain Service keinen zusätzlichen Verwaltungsaufwand.
- **Ledger-Technologie**: Basiert auf ConsenSys Quorum, einer erweiterten Version der GoQuorum Ledger-Technologie, die in Azure Blockchain Service verwendet wird. Es ist kein erneutes Lernen erforderlich. Weitere Informationen finden Sie in den [Häufig gestellten Fragen zu Consensys Quorum](https://consensys.net/quorum/faq).
- **Kontinuität**: Sie können Ihre vorhandenen Daten zu Quorum Blockchain Service von ConsenSys migrieren. Weitere Informationen finden Sie unter [Exportieren von Daten aus Azure Blockchain Service](#export-data-from-azure-blockchain-service)

Weitere Informationen finden Sie unter [Quorum Blockchain Service](https://consensys.net/QBS).

#### <a name="azure-vm-based-deployment"></a>Azure-VM-basierte Bereitstellung

Es gibt mehrere Vorlagen für die Blockchainressourcenverwaltung, mit denen Sie Blockchain auf IaaS-VMs bereitstellen können.

- **Ledgertechnologie**: Sie können weiterhin die Quorum-Ledgertechnologie verwenden, einschließlich des neuen ConsenSys-Quorums.
- **Selbstverwaltung**: Nach der Bereitstellung verwalten Sie die Infrastruktur und den Blockchainstapel.

### <a name="new-deployment-or-evaluation-phase"></a>Neue Bereitstellungs- oder Evaluierungsphase

Wenn Sie mit der Entwicklung einer neuen Lösung beginnen oder sich in einer Evaluierungsphase befinden, sollten Sie je nach Ihren Szenarioanforderungen die folgenden Alternativen in Betracht ziehen.

- [Quorum-Vorlage aus Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/consensys.quorum-dev-quickstart)
- Besu-Vorlage aus Azure Marketplace

### <a name="how-to-migrate-to-an-alternative"></a>So migrieren Sie zu einer Alternative

Um eine Produktionsworkload zu migrieren, [exportieren Sie zuerst Ihre Daten aus Azure Blockchain Service](#export-data-from-azure-blockchain-service). Sobald Sie über eine Kopie Ihrer Daten verfügen, können Sie diese Daten in Ihre bevorzugte Alternative transferieren.

Das empfohlene Migrationsziel ist ConsenSys Quorum Blockchain Service. Registrieren Sie sich auf der Seite [Quorum Blockchain Service](https://consensys.net/QBS), um das Onboarding für diesen Dienst zu erstellen.

Informationen zur selbstverwaltenden Blockchainlösung mithilfe von virtuellen Computern in Azure finden Sie unter [Azure VM-basierte Quorum-Anleitung](#azure-vm-based-quorum-guidance) zum Einrichten von Transaktions- und Validierungsknoten.
## <a name="export-data-from-azure-blockchain-service"></a>Exportieren von Daten aus Azure Blockchain Service

Basierend auf Ihrem aktuellen Entwicklungsstatus können Sie entweder vorhandene Ledgerdaten in Azure Blockchain Service verwenden oder ein neues Netzwerk starten und die Lösung Ihrer Wahl verwenden. Es wird empfohlen, ein neues Konsortium basierend auf einer Lösung Ihrer Wahl in allen Szenarios zu erstellen, in denen Sie keine vorhandenen Ledgerdaten auf dem Azure Blockchain Service benötigen oder verwenden möchten.

### <a name="open-support-case"></a>Supportanfrage öffnen

Wenn Sie über einen kostenpflichtigen Supportplan verfügen, öffnen Sie ein Microsoft Support Ticket, um das Konsortium anzuhalten und Ihre Blockchaindaten zu exportieren.

1. Verwenden Sie das Azure-Portal, um ein Supportticket zu öffnen. Geben Sie unter *Problembeschreibung* die folgenden Details ein:

    ![Formular zur Beschreibung des Supportticketproblems im Azure-Portal](./media/migration-guide/problem-description.png)

    | Feld | Antwort |
    |-------|--------- |
    | Typ des Problems | Technisch |
    | Dienst | Azure Blockchain Service: Vorschau |
    | Zusammenfassung | Anfordern von Daten für die Migration |
    | Problemtyp | andere |

1. Fügen Sie unter *Zusätzliche Details* die folgenden Details ein:

    ![Formular mit zusätzlichen Details zum Supportticket im Azure-Portal](./media/migration-guide/additional-details.png)

    - Abonnement-ID oder Azure Resource Manager-Ressourcen-ID
    - Tenant
    - Konsortiumsname
    - Region
    - Membername
    - Bevorzugter Zeitpunkt zum Initiieren der Migration

Wenn Ihr Konsortium über mehrere Mitglieder verfügt, muss jedes Mitglied ein separates Supportticket für die jeweiligen Mitgliedsdaten öffnen.

### <a name="pause-consortium"></a>Anhalten des Konsortiums

Sie müssen sich mit Mitgliedern des Konsortiums für den Datenexport abstimmen, da das Konsortium für den Datenexport angehalten wird und Transaktionen während dieser Zeit fehlschlagen.

Azure Blockchain Service Team hält das Konsortium an, exportiert eine Momentaufnahme der Daten und stellt die Daten über eine kurzlebige SAS-URL zum Herunterladen in einem verschlüsselten Format zur Verfügung. Das Konsortium wird nach dem Erstellen der Momentaufnahme fortgesetzt.

> [!IMPORTANT]
> Sie sollten alle Anwendungen beenden, die neue Blockchaintransaktionen im Netzwerk initiieren. Aktive Anwendungen können dazu führen, dass Daten verlorengehen oder Ihre ursprünglichen und migrierten Netzwerke nicht synchron sind.

### <a name="download-data"></a>Herunterladen von Daten

#### <a name="data-format-v1"></a>Datenformat v1

Laden Sie die Daten mithilfe der vom Microsoft Support bereitgestellten kurzlebigen SAS-URL-Links herunter.

> [!IMPORTANT]
> Sie müssen Ihre Daten innerhalb von sieben Tagen herunterladen.

Entschlüsseln Sie die Daten mithilfe des API-Zugriffsschlüssels. Sie [erhalten den Schlüssel im Azure-Portal](configure-transaction-nodes.md#access-keys) oder [über die REST-API](/rest/api/blockchain/2019-06-01-preview/blockchainmembers/listapikeys).

> [!CAUTION]
> Nur der API-Standardzugriffsschlüssel 1 für Transaktionsknoten wird verwendet, um alle Knotendaten dieses Mitglieds zu verschlüsseln.
>
> Setzen Sie den API-Zugriffsschlüssel zwischen der Migration nicht zurück.

#### <a name="data-format-v2"></a>Datenformat v2

In dieser Version wird das SAS-Token anstelle der Daten verschlüsselt, was zu einer schnelleren Erstellung von Momentaufnahmen führt. *Wenn* Sie sich für eine Migration zu ConsenSys Quorum Blockchain Service entscheiden, ist der Import zu Quorum Blockchain Service ebenfalls schneller.

Nachdem das SAS-Token entschlüsselt wurde, können die Daten wie gewohnt heruntergeladen werden. Die Daten selbst haben keine zusätzliche Verschlüsselungsebene.

> [!IMPORTANT]
> Das Erstellen einer Momentaufnahme im Datenformat v2 ist etwa 8-10 Mal schneller, sodass Sie weniger Downtime haben.

> [!CAUTION]
> Der standardmäßige Transaktionsknoten-API-Zugriffsschlüssel 1 wird zur Verschlüsselung des SAS-Tokens verwendet.
>
> Setzen Sie den API-Zugriffschlüssel zwischen oder während der Migration nicht zurück.

Sie können die Daten entweder mit dem ConsenSys Quorum Blockchain-Dienst oder mit Ihrer IaaS VM-basierten Bereitstellung verwenden.

Kontaktieren Sie ConsenSys zur Migration des ConsenSys Quorum Blockchain Service unter [qbsmigration@consensys.net](mailto:qbsmigration@consensys.net).

Um die Daten mit Ihrer IaaS-VM-basierten Bereitstellung zu verwenden, führen Sie die Schritte im Abschnitt [Leitfaden zum Azure VM-basierten Quorum](#azure-vm-based-quorum-guidance) dieses Artikels aus.

### <a name="delete-resources"></a>Löschen von Ressourcen

Nachdem Sie den Datenkopiervorgang abgeschlossen haben, wird empfohlen, die Azure Blockchain-Mitgliedsressourcen zu löschen. Sie werden weiterhin in Rechnung gestellt, solange diese Ressourcen vorhanden sind.

## <a name="azure-vm-based-quorum-guidance"></a>Leitfaden zum Azure VM-basierten Quorum

Mithilfe der folgenden Schritte erstellen Sie Transaktionsknoten und Validierungsknoten.

### <a name="transaction-node"></a>Transaktionsknoten

Ein Transaktionsknoten verfügt über zwei Komponenten. Tessera wird für die privaten Transaktionen und Geth für die Quorumanwendung verwendet. Validierungsknoten erfordern nur die Geth-Komponente.

#### <a name="tessera"></a>Tessera

1. Installieren Sie Java 11. Beispielsweise `apt install default-jre`.
1. Aktualisieren Sie Pfade in `tessera-config.json`. Ändern Sie alle Referenzen von `/working-dir/**` auf `/opt/blockchain/data/working-dir/**`.
1. Aktualisieren Sie die IP-Adresse anderer Transaktionsknoten entsprechend der neuen IP-Adresse. HTTPS funktioniert nicht, da es in der Tessera-Konfiguration nicht aktiviert ist. Informationen zum Konfigurieren von TLS finden Sie im Artikel [Tessera-Konfigurieren von TLS](https://docs.tessera.consensys.net/en/stable/HowTo/Configure/TLS/).
1. Aktualisieren Sie NSG-Regeln, um eingehende Verbindungen an Port 9000 zu ermöglichen.
1. Führen Sie Tessera mithilfe des folgenden Befehls aus:

    ```bash
    java -Xms512M -Xmx1731M -Dlogback.configurationFile=/tessera/logback-tessera.xml -jar tessera.jar -configfile /opt/blockchain/data/working-dir/tessera-config.json > tessera.log 2>&1 &
    ```

#### <a name="geth"></a>Geth

1. Aktualisieren Sie IPs in Enode-Adressen zu `/opt/blockchain/data/working-dir/dd/static-nodes.json`. Die öffentliche IP-Adresse ist zulässig.
1. Nehmen Sie die gleichen IP-Adressänderungen unter dem StaticNodes-Schlüssel in `/geth/config.toml` vor.
1. Aktualisieren Sie NSG-Regeln, um eingehende Verbindungen an Port 30303 zu ermöglichen.
1. Führen Sie Geth mit folgenden Befehlen aus:

    ```bash
    export NETWORK_ID='' # Get network ID from metadata. The network ID is the same for consortium.

    PRIVATE_CONFIG=tm.ipc geth --config /geth/config.toml --datadir /opt/blockchain/data/working-dir/dd --networkid $NETWORK_ID --istanbul.blockperiod 5 --nodiscover --nousb --allow-insecure-unlock --verbosity 3 --txpool.globalslots 80000 --txpool.globalqueue 80000 --txpool.accountqueue 50000 --txpool.accountslots 50000 --targetgaslimit 700000000 --miner.gaslimit 800000000 --syncmode full --rpc --rpcaddr 0.0.0.0 --rpcport 3100 --rpccorsdomain '*' --rpcapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul --ws --wsaddr 0.0.0.0 --wsport 3000 --wsorigins '*' --wsapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul
    ```

### <a name="validator-node"></a>Validierungsknoten

Die Schritte des Validierungsknotens ähneln dem für Transaktionsknoten, mit der Ausnahme, dass der Geth-Startbefehl das zusätzliche Flag `-mine` hat. Tessera wird nicht auf einem Validierungsknoten gestartet. Um Geth ohne eine gekoppelte Tessera auszuführen, übergeben Sie `PRIVATE_CONFIG=ignore` im Geth-Befehl. Führen Sie Geth mit folgenden Befehlen aus:

```bash
export NETWORK_ID=`j q '.APP_SETTINGS | fromjson | ."network-id"' env.json`

PRIVATE_CONFIG=ignore geth --config /geth/config.toml --datadir /opt/blockchain/data/working-dir/dd --networkid $NETWORK_ID --istanbul.blockperiod 5 --nodiscover --nousb --allow-insecure-unlock --verbosity 3 --txpool.globalslots 80000 --txpool.globalqueue 80000 --txpool.accountqueue 50000 --txpool.accountslots 50000 --targetgaslimit 700000000 --miner.gaslimit 800000000 --syncmode full --rpc --rpcaddr 0.0.0.0 --rpcport 3100 --rpccorsdomain '*' --rpcapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul --ws --wsaddr 0.0.0.0 --wsport 3000 --wsorigins '*' --wsapi admin,db,eth,debug,net,shh,txpool,personal,web3,quorum,istanbul –mine
```

## <a name="upgrading-quorum"></a>Aktualisieren des Quorums

Azure Blockchain Service wird eventuell in einer der im Folgenden aufgeführten Versionen von Quorum ausgeführt. Sie können die gleiche Quorumversion verwenden oder die folgenden Schritte ausführen, um die neueste Version von ConsenSys Quorum zu verwenden.

### <a name="upgrade-quorum-version-260-or-270-to-consensys-2110"></a>Quorumversion 2.6.0 oder 2.7.0 auf ConsenSys 21.1.0 aktualisieren

Das Aktualisieren von Quorum-Version 2.6 oder 2.7 ist einfach. Laden Sie mithilfe der folgenden Links herunter und aktualisieren Sie diese.
1. Laden Sie [ConsenSys Quorum und zugehörige Binärdateien v21.1.0](https://github.com/ConsenSys/quorum/releases/tag/v21.1.0) herunter.
1. Laden Sie die neueste Version von Tessera [tessera-app-21.1.0-app.jar](https://github.com/ConsenSys/tessera/releases/tag/tessera-21.1.0) herunter.

### <a name="upgrade-quorum-version-250-to-consensys-2110"></a>Aktualisieren Sie Quorumversion 2.5.0 auf ConsenSys 21.1.0

1. Laden Sie [ConsenSys Quorum und zugehörige Binärdateien v21.1.0](https://github.com/ConsenSys/quorum/releases/tag/v21.1.0) herunter.
1. Laden Sie die neueste Version von Tessera [tessera-app-21.1.0-app.jar](https://github.com/ConsenSys/tessera/releases/tag/tessera-21.1.0) herunter.
Für die Versionen 2.5.0 gibt es einige geringfügige Änderungen an der Genesis-Datei. Nehmen Sie die folgenden Änderungen an der Genesis-Datei vor.

1. Der Wert `byzantiumBlock` wurde auf „1“ festgelegt und darf nicht weniger als `constantinopleBlock` betragen, d. h. 0. Legen Sie den `byzantiumBlock` Wert auf 0 fest.
1. Legen Sie `petersburgBlock`, `istanbulBlock` auf einen zukünftigen Block fest. Dieser Wert sollte auf allen Knoten identisch sein.
1. Dieser Schritt ist optional. `ceil2Nby3Block` wurde fälschlicherweise in Azure Blockchain Service Quorum Version 2.5.0 platziert. Dies muss sich innerhalb der Konfigurationsdatei „istanbul“ befinden und den Wert für den zukünftigen Block festlegen. Dieser Wert sollte auf allen Knoten identisch sein.
1. Führen Sie „geth“ aus, um den Block „genesis“ mit folgendem Befehl erneut zu initialisieren:

    ```bash
    geth --datadir "Data Directory Path" init "genesis file path"
    ```

1.  Führen Sie „Geth“ aus.

## <a name="exported-data-reference"></a>Referenz zu exportierten Daten

In diesem Abschnitt werden die Metadaten und die Ordnerstruktur beschrieben, um die Daten in Ihre IaaS-VM-Bereitstellung zu importieren.

### <a name="metadata-info"></a>Informationen zu Metadaten

| Name               | Beispiel                | BESCHREIBUNG           |
|--------------------|-----------------------|-----------------------|
| consortium_name    | \<ConsortiumName\>    | Konsortiumsname (eindeutig in Azure Blockchain Service). |
| Consortium_Member_Count || Anzahl der Mitglieder im Konsortium |
| member_name        | \<memberName\>        | Name des Blockchainmitglieds (eindeutig in Azure Blockchain Service). |
| node_name          | transaction-node      | Knotenname (jedes Mitglied verfügt über mehrere Knoten). |
| network_id         | 543                   | Geth-Netzwerk-ID.      |
| is_miner           | Falsch                 | Is_Miner == true (Validierungsknoten), Is_Miner == false (Transaktionsknoten) |
| quorum_version     | 2.7.0                 | Version von Quorum     |
| tessera_version    | 0.10.5                | Version von Tessera      |
| java_version       | java-11-openjdk-amd64 | Von Tessera verwendete Java-Version |
| CurrentBlockNumber |                       | Aktuelle Blockzahl für das Blockchain-Netzwerk |

## <a name="migrated-data-folder-structure"></a>Migrierte Datenordnerstruktur

Auf der obersten Ebene gibt es Ordner, die den einzelnen Knoten der Member entsprechen.

- **Standard-SKU**: Zwei Validierungsknoten (validator-node-0 und validator-node-1)
- **Basic-SKU**: Ein Validierungsknoten (validator-node-0)
- **Transaktionsknoten**: Standardtransaktionsknoten mit dem Namen „transaction-node“.

Andere Transaktionsknotenordner werden nach dem Namen des Transaktionsknotens benannt.

### <a name="node-level-folder-structure"></a>Ordnerstruktur auf Knotenebene

Jeder Ordner auf Knotenebene enthält eine per Verschlüsselungsschlüssel verschlüsselte ZIP-Datei. Details zum Erhalten des Verschlüsselungsschlüssels erhalten Sie im Abschnitt [Daten herunterladen](#download-data) dieses Artikels.

| Verzeichnis/Datei | BESCHREIBUNG |
|----------------|--------------|
| /config/config.toml | „Geth“-Parameter. Befehlszeilenparameter haben Vorrang |
| /config/genesis.json | Genesis-Datei |
| /config/logback-tessera.xml | Logback-Konfiguration für Tessera |
| /config/static-nodes.json | Statische Knoten. Bootstrap-Knoten werden entfernt, und die automatische Ermittlung ist deaktiviert. |
| /config/tessera-config.json | Tessera-Konfiguration |
| /data/c/ | Tessera-DB |
| /data/dd/ | „Geth“-Datenverzeichnis |
| /env/env | Metadaten |
| /keys/ | Tessera-Schlüssel |
| /scripts/ | Startskripts (nur als Referenz bereitgestellt) |

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="what-does-service-retirement-mean-for-existing-customers"></a>Was bedeutet die Dienststilllegung für Bestandskunden?

Die vorhandenen Azure Blockchain Service-Bereitstellungen können nicht über den 10. September 2021 hinaus fortgesetzt werden. Beginnen Sie vor der Stilllegung mit der Evaluierung der in diesem Artikel vorgeschlagenen Alternativen, basierend auf Ihren Anforderungen.

### <a name="what-happens-to-existing-deployments-after-the-announcement-of-retirement"></a>Was geschieht mit vorhandenen Bereitstellungen nach der Ankündigung der Stilllegung?

Vorhandene Bereitstellungen werden bis zum 10. September 2021 unterstützt. Bewerten Sie die vorgeschlagenen Alternativen, migrieren Sie die Daten zum alternativen Angebot, betreiben Sie Ihre Anforderung für das alternative Angebot, und beginnen Sie mit der Migration von der Bereitstellung Azure Blockchain Service.

### <a name="how-long-will-the-existing-deployments-be-supported-on-azure-blockchain-service"></a>Wie lange werden die vorhandenen Bereitstellungen auf dem Azure Blockchain Service unterstützt?

Vorhandene Bereitstellungen werden bis zum 10. September 2021 unterstützt.

### <a name="will-i-be-allowed-to-create-new-azure-blockchain-members-while-in-retirement-phase"></a>Kann ich während der Stilllegung neue Azure Blockchain-Mitglieder erstellen?

Nach dem 10. Mai 2021 können keine neuen Mitglieder oder Bereitstellungen erstellt werden.
