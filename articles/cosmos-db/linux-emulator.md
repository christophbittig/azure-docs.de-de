---
title: Ausführen des Azure Cosmos DB-Emulators in Docker für Linux
description: Hier erfahren Sie, wie Sie den Azure Cosmos DB-Emulator unter Linux und macOS ausführen und verwenden. Mithilfe dieses Emulators können Sie Ihre Anwendung kostenlos lokal entwickeln und testen, ohne über ein Azure-Abonnement zu verfügen.
ms.service: cosmos-db
ms.topic: how-to
author: StefArroyo
ms.author: esarroyo
ms.date: 06/04/2021
ms.openlocfilehash: c0b7dff4ac6f19b4abcf27ccf98cd8d57b270e21
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111496"
---
# <a name="run-the-emulator-on-docker-for-linux-preview"></a>Ausführen des Emulators in Docker für Linux (Vorschau)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Der Azure Cosmos DB-Emulator für Linux bietet eine lokale Umgebung, die den Azure Cosmos DB-Dienst zu Entwicklungszwecken emuliert. Derzeit unterstützt der Linux-Emulator nur die SQL-API. Bei Verwendung des Azure Cosmos DB-Emulators können Sie die Anwendung lokal entwickeln und testen, ohne ein Azure-Abonnement zu erstellen oder sonstige Kosten zu verursachen. Wenn Sie mit der Funktionsweise Ihrer Anwendung im Azure Cosmos DB-Emulator für Linux zufrieden sind, können Sie zur Verwendung eines Azure Cosmos DB-Kontos in der Cloud wechseln. In diesem Artikel erfahren Sie, wie Sie den Emulator in macOS- und Linux-Umgebungen installieren und verwenden.

> [!NOTE]
> Der Cosmos DB-Emulator für Linux befindet sich derzeit in der Vorschauphase und unterstützt nur die SQL-API. Im Vergleich zur Windows-Version können geringfügige Leistungsbeeinträchtigungen hinsichtlich der Anzahl der Anforderungen auftreten, die pro Sekunde vom Emulator verarbeitet werden. Die Standardanzahl der physischen Partitionen, die sich direkt auf die Anzahl bereitstellbarer Container auswirkt, beträgt 10 Partitionen.
> 
> Es wird nicht empfohlen, den Emulator (Vorschauversion) in der Produktion zu verwenden. Verwenden Sie den [Windows-Emulator](local-emulator.md) für größere Workloads.

## <a name="how-does-the-emulator-work"></a>Wie funktioniert der Emulator?

Der Azure Cosmos DB-Emulator für Linux stellt eine originalgetreue Emulation des Azure Cosmos DB-Diensts bereit. Er unterstützt die gleichen Funktionen wie Azure Cosmos DB, was das Erstellen und Abfragen von Daten, Bereitstellen und Skalieren von Containern sowie das Ausführen von gespeicherten Prozeduren und Triggern einschließt. Sie können Anwendungen mithilfe des Azure Cosmos DB-Emulators für Linux entwickeln und testen und diese durch Aktualisierung des Azure Cosmos DB-Verbindungsendpunkts global in Azure bereitstellen.

Bei Verwendung des Emulators werden Funktionen, die die Azure-Infrastruktur benötigen, sind nicht unterstützt. Hierzu gehören beispielsweise die globale Replikation, Lese-/Schreibvorgänge mit Latenz im einstelligen Millisekundenbereich und optimierbare Konsistenzebenen.

## <a name="differences-between-the-linux-emulator-and-the-cloud-service"></a>Unterschiede zwischen dem Linux-Emulator und dem Clouddienst
Da der Azure Cosmos DB-Emulator eine emulierte Umgebung bereitstellt, die auf einer lokalen Entwicklerarbeitsstation ausgeführt wird, gibt es einige funktionelle Unterschiede zwischen dem Emulator und einem Azure Cosmos-Konto in der Cloud:

- Der Bereich **Daten-Explorer** im Emulator unterstützt derzeit nur SQL-API-Clients vollständig.

- Mit dem Linux-Emulator können Sie ein Azure Cosmos-Konto nur im Modus [Bereitgestellter Durchsatz](set-throughput.md) erstellen. Der Modus [Serverlos](serverless.md) wird derzeit nicht unterstützt.

- Der Linux-Emulator ist kein skalierbarer Speicherdienst und unterstützt keine große Anzahl von Containern. Bei Verwendung des Azure Cosmos DB-Emulators können Sie standardmäßig bis zu 10 Container mit fester Größe mit 400 RU/s (nur unterstützt bei Verwendung von Azure Cosmos DB SDKs) oder 5 Container mit unbegrenzter Größe erstellen. Weitere Informationen zum Ändern dieses Werts finden Sie im Artikel [Festlegen des PartitionCount-Werts](emulator-command-line-parameters.md#set-partitioncount).

- Obwohl [Konsistenzebenen](consistency-levels.md) mithilfe von Befehlszeilenargumenten nur für Testszenarien angepasst werden können (Standardeinstellung: Session (Sitzung)), erwartet ein Benutzer möglicherweise nicht dasselbe Verhalten wie beim Clouddienst. Beispielsweise haben die Konsistenzmodelle „Streng“ und „Begrenzte Veraltung“ keine Auswirkungen auf den Emulator, außer dass sie dem Cosmos DB SDK die Standardkonsistenz des Kontos melden.

- Der Linux-Emulator bietet keine [Replikation in mehrere Regionen](distribute-data-globally.md).

- Möglicherweise ist Ihre Kopie des Azure Cosmos DB-Emulators für Linux nicht immer auf dem neuesten Stand der Änderungen im Azure Cosmos DB-Dienst. Verwenden Sie daher stets das [Azure Cosmos DB-Kapazitätsplanungstool](estimate-ru-with-capacity-planner.md), um den erforderlichen Durchsatz (in RUs, Anforderungseinheiten) für Ihre Anwendung richtig einzuschätzen.

- Der Linux-Emulator unterstützt für die ID-Eigenschaft maximal 254 Zeichen.

## <a name="run-the-linux-emulator-on-macos"></a><a id="run-on-macos"></a>Ausführen des Linux-Emulators unter macOS

> [!NOTE]
> Der Emulator unterstützt nur MacBooks mit Intel-Prozessoren.

Rufen Sie Docker Hub auf, und installieren Sie [Docker Desktop für macOS](https://hub.docker.com/editions/community/docker-ce-desktop-mac/), um zu beginnen. Führen Sie die folgenden Schritte aus, um den Emulator unter macOS auszuführen:

[!INCLUDE[linux-emulator-instructions](includes/linux-emulator-instructions.md)]

## <a name="install-the-certificate"></a><a id="install-certificate"></a>Installieren des Zertifikats

1. Sobald der Emulator ausgeführt wird, laden Sie die IP-Adresse Ihres lokalen Computers mithilfe eines anderen Terminals in eine Variable.

    ```bash
    ipaddr="`ifconfig | grep "inet " | grep -Fv 127.0.0.1 | awk '{print $2}' | head -n 1`"
    ```

1. Laden Sie als Nächstes das Zertifikat für den Emulator herunter.

    ```bash
    curl -k https://$ipaddr:8081/_explorer/emulator.pem > emulatorcert.crt
    ```


## <a name="consume-the-endpoint-via-ui"></a><a id="consume-endpoint-ui"></a>Verwenden des Endpunkts über die Benutzeroberfläche

Der Emulator verwendet ein selbstsigniertes Zertifikat, um die Konnektivität mit dem Endpunkt zu schützen, und muss manuell als vertrauenswürdig eingestuft werden. Führen Sie die folgenden Schritte aus, um den Endpunkt über die Benutzeroberfläche mit Ihrem gewünschten Webbrowser zu nutzen:

1. Vergewissern Sie sich, dass Sie das selbstsignierte Zertifikat des Emulators herunter geladen haben.

   ```bash
   curl -k https://$ipaddr:8081/_explorer/emulator.pem > emulatorcert.crt
   ```

1. Öffnen Sie die App **Schlüsselbundverwaltung** auf Ihrem Mac-Gerät, um das Emulatorzertifikat zu importieren.

1. Klicken Sie auf **Datei** und **Elemente importieren**, und importieren Sie die Zertifikatdatei **emulatorcert.crt**.

1. Nachdem die Datei *emulatorcert.crt* in den Schlüsselbund geladen wurde, doppelklicken Sie auf den Namen **localhost**, und ändern Sie die Einstellung für die Vertrauensstellung in **Immer vertrauen**.

1. Sie können nun zu `https://localhost:8081/_explorer/index.html` oder `https://{your_local_ip}:8081/_explorer/index.html` navigieren und die Verbindungszeichenfolge des Emulators abrufen.

Optional können Sie die SSL-Überprüfung für die Anwendung deaktivieren. Dies wird nur zu Entwicklungszwecken empfohlen und sollte bei der Ausführung in einer Produktionsumgebung nicht gemacht werden.

## <a name="run-the-linux-emulator-on-linux-os"></a><a id="run-on-linux"></a>Ausführen des Linux-Emulators unter Linux

Verwenden Sie das `apt`-Paket, und installieren Sie die neueste Version von Docker, um zu beginnen.

```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
```

Wenn Sie das Windows-Subsystem für Linux (WSL) verwenden, führen Sie den folgenden Befehl aus, um `ifconfig` abzurufen:

```bash
sudo apt-get install net-tools
```

Führen Sie die folgenden Schritte aus, um den Emulator unter Linux auszuführen:

[!INCLUDE[linux-emulator-instructions](includes/linux-emulator-instructions.md)]

4. Sobald der Emulator ausgeführt wird, laden Sie die IP-Adresse Ihres lokalen Computers mithilfe eines anderen Terminals in eine Variable.

    ```bash
    ipaddr="`ifconfig | grep "inet " | grep -Fv 127.0.0.1 | awk '{print $2}' | head -n 1`"
    ```

5. Laden Sie als Nächstes das Zertifikat für den Emulator herunter. Alternativ können Sie auch den Endpunkt unten, über den das selbstsignierte Emulatorzertifikat heruntergeladen wird, für die Signalisierung verwenden, wenn der Emulatorendpunkt bereit ist, Anforderungen von einer anderen Anwendung zu empfangen.

    ```bash
    curl -k https://$ipaddr:8081/_explorer/emulator.pem > ~/emulatorcert.crt
    ```

6. Kopieren Sie die CRT-Datei in den Ordner, der in Ihrer Linux-Distribution benutzerdefinierte Zertifikate enthält. In Debian-Distributionen befindet er sich üblicherweise unter `/usr/local/share/ca-certificates/`.

   ```bash
   cp ~/emulatorcert.crt /usr/local/share/ca-certificates/
   ```

7. Aktualisieren Sie die TLS-/SSL-Zertifikate, wodurch der Ordner `/etc/ssl/certs/` aktualisiert wird.

   ```bash
   update-ca-certificates
   ```

    Für Anwendungen, die auf Java basieren, muss das Zertifikat in den [Java-Vertrauensspeicher](local-emulator-export-ssl-certificates.md) importiert werden.

    ```bash
    keytool -keystore ~/cacerts -importcert -alias  emulator_cert -file ~/emulatorcert.crt
    java -ea -Djavax.net.ssl.trustStore=~/cacerts -Djavax.net.ssl.trustStorePassword="changeit" $APPLICATION_ARGUMENTS
    ```

## <a name="configuration-options"></a><a id="config-options"></a>Konfigurationsoptionen

|Name  |Standard  |Beschreibung  |
|---------|---------|---------|
|  Ports: `-p`   |         |   Derzeit benötigt der Emulator nur die Ports 8081 und 10251 bis 10255.     |
| `AZURE_COSMOS_EMULATOR_PARTITION_COUNT`    |    10     |    Hiermit wird die Gesamtanzahl der physischen Partitionen gesteuert, die hingegen die Anzahl von Containern steuert, die zu einem bestimmten Zeitpunkt erstellt werden und vorhanden sein können. Es wird empfohlen, klein anzufangen, um die Startzeit des Emulators zu verbessern. 3.     |
|  Arbeitsspeicher: `-m`   |         | Es sind mindestens 3 GB Arbeitsspeicher erforderlich.     |
| Kerne: `--cpus`  |         |   Stellen Sie sicher, dass Sie ausreichend Arbeitsspeicher und CPU-Kerne bereitstellen. Zwar kann der Emulator mit nur 0,5 Kernen ausgeführt werden (sehr langsam), jedoch werden mindestens 2 Kerne empfohlen.      |
|`AZURE_COSMOS_EMULATOR_ENABLE_DATA_PERSISTENCE`  | false  | Das Verwenden allein dieser Einstellung trägt zur Beibehaltung von Daten zwischen Containerneustarts bei.  |

## <a name="troubleshoot-issues"></a><a id="troubleshoot-issues"></a>Behandeln von Problemen

In diesem Abschnitt finden Sie Tipps zur Problembehandlung von Fehlern bei Verwendung des Linux-Emulators.

### <a name="connectivity-issues"></a>Konnektivitätsprobleme

#### <a name="my-app-cant-connect-to-emulator-endpoint-the-ssl-connection-could-not-be-established-or-i-cant-start-the-data-explorer"></a>Die App kann keine Verbindung mit dem Endpunkt des Emulators herstellen („The SSL connection could not be established“ (Die SSL-Verbindung konnte nicht hergestellt werden)) oder der Daten-Explorer kann nicht gestartet werden

- Stellen Sie mithilfe des folgenden Befehls sicher, dass der Emulator ausgeführt wird:

    ```bash
    docker ps --all
    ```

- Stellen Sie sicher, dass der spezifische Emulatorcontainer ausgeführt wird.

- Stellen Sie sicher, dass keine anderen Anwendungen die Ports des Emulators verwendet: 8081 und 10250 bis 10255.

- Stellen Sie sicher, dass der Containerport 8081 ordnungsgemäß zugeordnet ist und über eine Umgebung außerhalb des Containers zugänglich ist.  

   ```bash
   netstat -lt
   ```

- Versuchen Sie, mithilfe der IP-Adresse des Docker-Containers anstelle von „localhost“ auf den Endpunkt und Port des Emulators zuzugreifen.

- Stellen Sie sicher, dass das selbstsignierte Zertifikat des Emulators ordnungsgemäß zum [Schlüsselbund](#consume-endpoint-ui) hinzugefügt wurde.

- Vergewissern Sie sich bei Java-Anwendungen, dass das Zertifikat in den [Abschnitt „Java Certificates Store“](#run-on-linux) importiert wurde.

- Für .NET-Anwendungen können Sie die SSL-Überprüfung deaktivieren:

# <a name="net-standard-21"></a>[.NET Standard 2.1+](#tab/ssl-netstd21)

Für alle Anwendungen, die unter einem Framework mit .NET Standard 2.1-Kompatibilität (oder höher) ausgeführt werden, können wir `CosmosClientOptions.HttpClientFactory` nutzen:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard21)]

# <a name="net-standard-20"></a>[.NET-Standard 2.0](#tab/ssl-netstd20)

Für alle Anwendungen, die unter einem mit .NET Standard 2.0 kompatiblen Framework ausgeführt werden, können wir `CosmosClientOptions.HttpClientFactory` nutzen:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/HttpClientFactory/Program.cs?name=DisableSSLNETStandard20)]

---

#### <a name="my-nodejs-app-is-reporting-a-self-signed-certificate-error"></a>Meine Node.js-App meldet einen Fehler in Zusammenhang mit einem selbstsignierten Zertifikat.

Wenn Sie versuchen, über eine andere Adresse als `localhost`, z. B. die IP-Adresse des Containers, eine Verbindung zum Emulator herzustellen, löst Node.js einen Fehler für das selbstsignierte Zertifikat aus – auch dann, wenn das Zertifikat installiert wurde.

Sie können die TLS-Überprüfung deaktivieren, indem Sie die Umgebungsvariable `NODE_TLS_REJECT_UNAUTHORIZED` auf `0` festlegen:

```bash
NODE_TLS_REJECT_UNAUTHORIZED=0
```

Dieses Flag wird nur für die lokale Entwicklung empfohlen, da es TLS für Node.js deaktiviert. Weitere Informationen finden Sie in der [Node.js-Dokumentation](https://nodejs.org/api/cli.html#cli_node_tls_reject_unauthorized_value) und in der [Dokumentation zu Cosmos DB Emulatorzertifikaten](local-emulator-export-ssl-certificates.md#how-to-use-the-certificate-in-nodejs).

#### <a name="the-docker-container-failed-to-start"></a>Fehler beim Starten des Docker-Containers

Der Emulator wird mit folgender Fehlermeldung beendet:

```bash
/palrun: ERROR: Invalid mapping of address 0x40037d9000 in reserved address space below 0x400000000000. Possible causes:
1. The process (itself, or via a wrapper) starts up its own running environment sets the stack size limit to unlimited via syscall setrlimit(2);
2. The process (itself, or via a wrapper) adjusts its own execution domain and flag the system its legacy personality via syscall personality(2);
3. Sysadmin deliberately sets the system to run on legacy VA layout mode by adjusting a sysctl knob vm.legacy_va_layout.
```

Dieser Fehler ist wahrscheinlich darauf zurückzuführen, dass der aktuelle Docker-Hostprozessortyp nicht mit dem Docker-Image kompatibel ist. Das heißt, bei dem Computer handelt es sich um ein MacBook mit einem Apple M1-Chip.

#### <a name="my-app-received-too-many-connectivity-related-timeouts"></a>Es treten zu viele konnektivitätsbezogene Timeouts bei der App auf

- Der Docker-Container verfügt über unzureichende Ressourcen [(Kerne oder Arbeitsspeicher)](#config-options). Es wird empfohlen, die Anzahl der Kerne zu erhöhen und alternativ die Anzahl physischer Partitionen zu reduzieren, die beim Start bereitgestellt werden.

- Stellen Sie sicher, dass die Anzahl von TCP-Verbindungen Ihre aktuellen Betriebssystemeinstellungen nicht überschreitet.

- Versuchen Sie, die Größe der Dokumente in Ihrer Anwendung zu reduzieren.
    
#### <a name="my-app-could-not-provision-databasescontainers"></a>Die App kann keine Datenbanken/Container bereitstellen

Die Anzahl der physischen Partitionen, die dem Emulator bereitgestellt wurden, ist zu niedrig. Löschen Sie Ihre nicht verwendeten Datenbanken/Sammlungen, oder starten Sie den Emulator mit einer [größeren Anzahl physischer Partitionen](#config-options).

### <a name="reliability-and-crashes"></a>Zuverlässigkeit und Abstürze

- Fehler beim Starten des Emulators:

  - Stellen Sie sicher, dass Sie [das neueste Image des Cosmos DB-Emulators für Linux ausführen](#refresh-linux-container). Andernfalls finden Sie weitere Informationen im Abschnitt zu Problemen hinsichtlich der Konnektivität oben.

  - Wenn der Datenordner des Cosmos DB-Emulators „in das Volume eingebunden ist“, müssen Sie sicherstellen, dass das Volume über ausreichend Speicherplatz verfügt und Lese- und Schreibvorgänge unterstützt.

  - Vergewissern Sie sich, dass das Erstellen eines Containers mit den empfohlenen Einstellungen funktioniert. Wenn ja, ist der Fehler wahrscheinlich auf die zusätzlichen Einstellungen zurückzuführen, die beim Start des Containers über den entsprechenden Docker-Befehl übergeben wurden.

  - Wenn der Start des Emulators mit folgendem Fehler fehlschlägt:
  
    ```bash
    "Failed loading Emulator secrets certificate. Error: 0x8009000f or similar, a new policy might have been added to your host that prevents an application such as Azure Cosmos DB emulator from creating and adding self signed certificate files into your certificate store."
    ```

    Dieser Fall kann auch auftreten, wenn Sie die Ausführung im Administratorkontext starten, da die spezifische Richtlinie, die normalerweise von Ihrer IT-Abteilung hinzugefügt wird, gegenüber lokalen Administratorrechten priorisiert wird. Stattdessen kann die Verwendung eines Docker-Images für den Emulator in diesem Fall hilfreich sein, solange Sie weiterhin über die Berechtigung verfügen, das selbstsignierte SSL-Zertifikat des Emulators zum Hostcomputerkontext hinzuzufügen (dies ist für die Java- und .NET Cosmos SDK-Clientanwendungen erforderlich).

- Der Emulator stürzt ab:

  - Vergewissern Sie sich, dass das Erstellen eines Containers mit den [empfohlenen Einstellungen](#run-on-linux) funktioniert. Wenn ja, ist der Fehler wahrscheinlich auf die zusätzlichen Einstellungen zurückzuführen, die beim Start des Containers über den entsprechenden Docker-Befehl übergeben wurden.

  - Starten Sie den Docker-Container des Emulators in einem angefügten Modus (siehe `docker start -it`).

  - Erfassen Sie das Absturzabbild bzw. Daten bezüglich des Absturzes, und führen Sie [diese Schritte](#report-an-emulator-issue) aus, um das Problem zu melden.  

### <a name="data-explorer-errors"></a>Daten-Explorer-Fehler

- Ich kann meine Daten nicht anzeigen:

  - Informationen hierzu finden Sie oben im Abschnitt zu Problemen hinsichtlich der Konnektivität.

  - Stellen Sie sicher, dass das selbstsignierte Zertifikat des Emulators ordnungsgemäß importiert und manuell als vertrauenswürdig eingestuft wurde, damit Ihr Browser auf die Seite für den Daten-Explorer zugreifen kann.

  - Versuchen Sie, eine Datenbank bzw. einen Container zu erstellen und ein Element mit dem Daten-Explorer einzufügen. Wenn dies funktioniert, befindet sich die Ursache des Problems höchstwahrscheinlich in Ihrer Anwendung. Wenn nicht, [kontaktieren Sie das Cosmos DB-Team](#report-an-emulator-issue).

### <a name="performance-issues"></a>Leistungsprobleme

Die Anzahl der Anforderungen pro Sekunde ist niedrig, aber die Latenz der Anforderungen ist hoch:

- Der Docker-Container verfügt über unzureichende Ressourcen [(Kerne oder Arbeitsspeicher)](#config-options). Es wird empfohlen, die Anzahl der Kerne zu erhöhen und alternativ die Anzahl physischer Partitionen zu reduzieren, die beim Start bereitgestellt werden.

## <a name="refresh-linux-container"></a><a id="refresh-linux-container"></a>Aktualisieren eines Linux-Containers

Führen Sie die folgenden Schritte aus, um den Linux-Container zu aktualisieren:

1. Führen Sie den folgenden Befehl aus, um alle Docker-Container anzuzeigen.

   ```bash
   docker ps --all
   ```

1. Entfernen Sie den Container mit der ID, die Sie mit dem obigen Befehl abgerufen haben.

   ```bash
   docker rm ID_OF_CONTAINER_FROM_ABOVE
   ```

1. Listen Sie als Nächstes alle Docker-Images auf.

   ```bash
   docker images
   ```

1. Entfernen Sie das Image mit der ID, die Sie im vorherigen Schritt abgerufen haben.

   ```bash
   docker rmi ID_OF_IMAGE_FROM_ABOVE
   ```

1. Pullen Sie das neueste Image des Cosmos DB-Emulators für Linux.

   ```bash
   docker pull mcr.microsoft.com/cosmosdb/linux/azure-cosmos-emulator
   ```

1. Führen Sie den folgenden Befehl aus, um einen angehaltenen Container zu starten:

   ```bash
   docker start -ai ID_OF_CONTAINER
   ```

## <a name="report-an-emulator-issue"></a>Melden eines Problems mit dem Emulator

Geben Sie möglichst viele Informationen zu Ihrem Problem an, wenn Sie ein Problem mit dem Linux-Emulator melden. Hierzu zählen:

- Beschreibung des Fehlers/Problems
- Umgebung (Betriebssystem, Hostkonfiguration)
- Computer- und Prozessortyp
- Verwendeter Befehl zum Erstellen und Starten des Emulators (YML-Datei, wenn „Docker compose“ verwendet wurde)
- Beschreibung der Workload
- Stichprobe der verwendeten Datenbank/Sammlung und Elemente
- Konsolenausgabe vom Starten des Docker-Containers für den Emulator im angefügten Modus
- Senden Sie alle oben aufgeführten Informationen an das [Azure Cosmos DB-Team](mailto:cdbportalfeedback@microsoft.com).

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie den Azure Cosmos DB-Emulator für Linux für die kostenlose lokale Entwicklung verwenden. Sie können jetzt mit dem nächsten Artikel fortfahren:

- [Exportieren der Azure Cosmos DB-Emulatorzertifikate für die Verwendung mit Java-, Python- und Node.js-Apps](local-emulator-export-ssl-certificates.md)
- [Debuggen von Problemen mit dem Emulator](troubleshoot-local-emulator.md)
