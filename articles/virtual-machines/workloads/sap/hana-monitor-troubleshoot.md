---
title: Überwachung und Problembehandlung von SAP HANA in Azure (große Instanzen) auf der HANA-Seite | Microsoft-Dokumentation
description: Hier wird die Überwachung und Problembehandlung im Zusammenhang mit SAP HANA in Azure (große Instanzen) mithilfe von Ressourcen erläutert, die von SAP HANA bereitgestellt werden.
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/18/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017, contperf-fy21q4
ms.openlocfilehash: d42304fbd5a37bdae0d40b44466c29ae1cf5b586
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131060400"
---
# <a name="monitoring-and-troubleshooting-from-hana-side"></a>Überwachung und Problembehandlung auf HANA-Seite

In diesem Artikel wird die Überwachung und Problembehandlung im Zusammenhang mit SAP HANA in Azure (große Instanzen) mithilfe von Ressourcen erläutert, die von SAP HANA bereitgestellt werden. 

Sie sollten die Grundursache eines Problems eingrenzen, um Probleme mit SAP HANA in Azure (große Instanzen) zu analysieren. SAP hat viele hilfreiche Dokumentationen veröffentlicht. Häufig gestellte Fragen zur SAP HANA-Leistung finden Sie in den folgenden SAP-Hinweisen:

- [SAP Note #2222200 – FAQ: SAP HANA Network](https://launchpad.support.sap.com/#/notes/2222200)
- [SAP Note #2100040 – FAQ: SAP HANA CPU](https://launchpad.support.sap.com/#/notes/0002100040)
- [SAP Note #199997 – FAQ: SAP HANA Memory](https://launchpad.support.sap.com/#/notes/2177064)
- [SAP Note #200000 – FAQ: SAP HANA Performance Optimization](https://launchpad.support.sap.com/#/notes/2000000)
- [SAP Note #199930 – FAQ: SAP HANA I/O Analysis](https://launchpad.support.sap.com/#/notes/1999930)
- [SAP Note #2177064 – FAQ: SAP HANA Service Restart and Crashes](https://launchpad.support.sap.com/#/notes/2177064)

## <a name="sap-hana-alerts"></a>SAP HANA-Warnungen

Überprüfen Sie zunächst die aktuellen SAP HANA-Warnungsprotokolle. Wechseln Sie in SAP HANA Studio zu **Administration Console: Alerts: Show: All alerts**. Auf dieser Registerkarte werden alle SAP HANA-Warnungen für Werte (freier physischer Arbeitsspeicher, CPU-Auslastung usw.) angezeigt, die außerhalb der festgelegten minimalen und maximalen Schwellenwerte liegen. Standardmäßig werden die Überprüfungen alle 15 Minuten automatisch aktualisiert.

![Wechseln Sie in SAP HANA Studio zu „Administration Console: Alerts: Show: All alerts“](./media/troubleshooting-monitoring/image1-show-alerts.png)

## <a name="cpu"></a>CPU

Setzen Sie den Wert bei einer Warnung, die durch eine falsche Schwellenwerteinstellung ausgelöst wird, auf den Standardwert oder einen sinnvolleren Schwellenwert zurück.

![Zurücksetzen auf den Standardwert oder einen geeigneteren Schwellenwert](./media/troubleshooting-monitoring/image2-cpu-utilization.png)

Die folgenden Warnungen können ein Hinweis auf CPU-Ressourcenprobleme sein:

- Host CPU Usage (Alert 5)
- Most recent savepoint operation (Alert 28)
- Savepoint duration (Alert 54)

Möglicherweise stellen Sie durch Folgendes eine hohe CPU-Auslastung für Ihre SAP HANA-Datenbank fest:

- „Alert 5 (Host CPU Usage)“ wird für die aktuelle oder bisherige CPU-Auslastung ausgelöst
- Die angezeigte CPU-Auslastung auf dem Übersichtsbildschirm

![Auf dem Übersichtsbildschirm angezeigte CPU-Auslastung](./media/troubleshooting-monitoring/image3-cpu-usage.png)

Das Diagramm „Load“ kann eine hohe CPU-Auslastung bzw. hohe Auslastung in der Vergangenheit anzeigen:

![Das Diagramm „Load“ kann eine hohe CPU-Auslastung bzw. eine hohe Auslastung in der Vergangenheit anzeigen](./media/troubleshooting-monitoring/image4-load-graph.png)

Eine Warnung, die durch eine hohe CPU-Auslastung ausgelöst wird, kann mehrere Gründe haben:
- Ausführen bestimmter Transaktionen
- Laden von Daten
- Aufträge, die nicht reagieren
- SQL-Anweisungen mit langer Ausführungszeit
- Schlechte Abfrageleistung (z. B. mit BW in HANA-Cubes)

Ausführliche Schritte zur Problembehandlung im Zusammenhang mit der CPU-Auslastung finden Sie unter [SAP HANA-Problembehandlung: CPU-bezogene Ursachen und Lösungen](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/4fbc915462db406aa2fe92b708b95189.html?q=%20SAP%20HANA%20Troubleshooting:%20CPU%20Related%20Causes%20and%20Solutions).

## <a name="operating-system-os"></a>Betriebssystem

Eine wichtige Überprüfung für SAP HANA unter Linux besteht darin, sicherzustellen, dass Transparent Huge Pages deaktiviert sind. Weitere Informationen finden Sie unter [SAP-Hinweis 2131662: Transparent Huge Pages (THP) auf SAP HANA-Servern](https://launchpad.support.sap.com/#/notes/2131662).

Sie können prüfen, ob Transparent Huge Pages aktiviert sind, indem Sie den folgenden Linux-Befehl ausführen: **cat /sys/kernel/mm/transparent\_hugepage/enabled**
- Wenn _always_ in eckige Klammern eingeschlossen ist, bedeutet dies, dass Transparent Huge Pages aktiviert sind: [always] madvise never
- Wenn _never_ in eckige Klammern eingeschlossen ist, bedeutet dies, dass Transparent Huge Pages deaktiviert sind: always madvise [never]

Die folgende Linux-Befehl sollte nichts zurückgeben: **rpm -qa | grep ulimit**. Sollte _ulimit_ installiert sein, führen Sie sofort eine Deinstallation durch.

## <a name="memory"></a>Arbeitsspeicher

Sie bemerken möglicherweise, dass der reservierte Arbeitsspeicher, der der SAP HANA-Datenbank zugeteilt ist, größer als erwartet ist. Die folgenden Warnungen sind Anzeichen von Problemen aufgrund einer hohen Speicherauslastung:

- Host physical memory usage (Alert 1)
- Memory usage of name server (Alert 12)
- Total memory usage of Column Store tables (Alert 40)
- Memory usage of services (Alert 43)
- Memory usage of main storage of Column Store tables (Alert 45)
- Runtime dump files (Alert 46)

Ausführliche Schritte zur Problembehandlung im Zusammenhang mit Speicher finden Sie unter [SAP HANA-Problembehandlung: Grundursachen von Arbeitsspeicherproblemen](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/3a2ea5c4593b4b8d823b5b48152bd1d4.html).

## <a name="network"></a>Netzwerk

Führen Sie die unter [SAP-Hinweis 2081065: Behandlung von SAP HANA-Netzwerkproblemen](https://launchpad.support.sap.com/#/notes/2081065) beschriebenen Schritte zur Behandlung von Netzwerkproblemen aus.

1. Analysieren Sie die Roundtripzeit zwischen Server und Client.
    - Führen Sie das SQL-Skript [_HANA\_Network\_Clients_](https://launchpad.support.sap.com/#/notes/1969700)_aus._
  
2. Analysieren Sie die Kommunikation zwischen Knoten.
    - Führen Sie das SQL-Skript [_HANA\_Network\_Services_](https://launchpad.support.sap.com/#/notes/1969700)_aus._

3. Führen Sie den Linux-Befehl **ifconfig** aus (die Ausgabe zeigt, ob Paketverluste auftreten).
4. Führen Sie den Linux-Befehl **tcpdump** aus.

Verwenden Sie außerdem ein Open-Source-Tool wie [IPERF](https://iperf.fr/), um die tatsächliche Netzwerkleistung der Anwendung zu messen.

Ausführliche Schritte zur Problembehandlung im Zusammenhang mit Netzwerken finden Sie unter [SAP HANA-Problembehandlung: Netzwerkleistung und Konnektivitätsprobleme](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/a3ccdff1aedc4720acb24ed8826938b6.html?q=Networking%20Performance%20and%20Connectivity%20Problems).

## <a name="storage"></a>Speicher

Angenommen, es gibt Probleme mit der E/A-Leistung. Endbenutzer stellen dann möglicherweise fest, dass die Ausführung von Anwendungen oder die Ausführung des Systems als Ganzes sehr langsam erfolgt und dieses kaum oder gar nicht reagiert. Auf der Registerkarte **Volumes** in SAP HANA Studio können Sie sehen, welche Volumes angefügt sind und vom jeweiligen Dienst verwendet werden.

![Auf der Registerkarte „Volumes“ in SAP HANA Studio können Sie sehen, welche Volumes angeschlossen sind und vom jeweiligen Dienst verwendet werden](./media/troubleshooting-monitoring/image5-volumes-tab-a.png)

Im unteren Teil des Bildschirms (unter der Registerkarte „Volumes“) werden Details zu den Volumes (z. B. Statistiken zu Dateien und E/A-Vorgängen) angezeigt.

![Im unteren Teil des Bildschirms werden Details zu den Volumes (z. B. Statistiken zu Dateien und E/A-Vorgängen) angezeigt.](./media/troubleshooting-monitoring/image6-volumes-tab-b.png)

Schritte zur Problembehandlung bei E/A-Vorgängen finden Sie unter [SAP HANA-Problembehandlung: E/A-bezogene Grundursachen und Lösungen](https://help.sap.com/viewer/4e9b18c116aa42fc84c7dbfd02111aba/2.0.05/en-US/dc6ff98fa36541e997e4c719a632cbd8.html?q=I%2FO%20Related%20Root%20Causes%20and%20Solutions). Schritte zum Beheben von datenträgerbezogenen Problemen finden Sie unter [SAP HANA-Problembehandlung: Datenträgerbezogene Grundursachen und Lösungen](https://help.sap.com/viewer/bed8c14f9f024763b0777aa72b5436f6/2.0.05/en-US/474cb08a715c42fe9f7cc5efdc599959.html?q=Disk%20Related%20Root%20Causes%20and%20Solutions).

## <a name="diagnostic-tools"></a>Diagnosetools

Führen Sie über „HANA\_Configuration\_Minichecks“ eine SAP HANA-Integritätsprüfung durch. Dieses Tool gibt potenzielle technische Probleme zurück, die in SAP HANA Studio bereits als Warnungen hätten ausgelöst werden sollen.

1. Lesen Sie [SAP Note #1969700 – SQL statement collection for SAP HANA](https://launchpad.support.sap.com/#/notes/1969700), und laden Sie die an diesen Hinweis angefügte Datei „SQL Statements.zip“ herunter. Speichern Sie diese ZIP-Datei auf der lokalen Festplatte.

2. Klicken Sie in SAP HANA Studio auf der Registerkarte **System Information** mit der rechten Maustaste auf die Spalte **Name**, und wählen Sie **Import SQL Statements** aus.

    ![Klicken Sie in SAP HANA Studio auf der Registerkarte „System Information“ mit der rechten Maustaste auf die Spalte „Name“, und wählen Sie „Import SQL Statements“ aus](./media/troubleshooting-monitoring/image7-import-statements-a.png)

3. Wählen Sie die lokal gespeicherte Datei „SQL-Statements.zip“ aus. Ein Ordner mit den entsprechenden SQL-Anweisungen wird importiert. An diesem Punkt können viele verschiedene Diagnoseprüfungen mit diesen SQL-Anweisungen ausgeführt werden.

    Um z.B. die Bandbreitenanforderungen für die SAP HANA-Systemreplikation zu testen, klicken Sie mit der rechten Maustaste unter **Replication: Bandwidth** auf die Anweisung **Bandwidth** und wählen in der SQL Console **Open** aus.

    Die vollständige SQL-Anweisung wird geöffnet, sodass Sie Eingabeparameter (im Abschnitt „modification“) ändern und anschließend ausführen können.

    ![Die vollständige SQL-Anweisung wird geöffnet, sodass Sie Eingabeparameter (im Abschnitt „modification“) ändern und anschließend ausführen können](./media/troubleshooting-monitoring/image8-import-statements-b.png)

4. Ein weiteres Beispiel ist das Klicken mit der rechten Maustaste auf die Anweisungen unter **Replication: Overview**. Wählen Sie im Kontextmenü **Execute** aus:

    ![Ein weiteres Beispiel ist das Klicken mit der rechten Maustaste auf die Anweisungen unter „Replication: Overview“. Wählen Sie im Kontextmenü „Execute“ aus](./media/troubleshooting-monitoring/image9-import-statements-c.png)

    Hier finden Sie hilfreiche Informationen zur Problembehandlung:

    ![Hilfreiche Informationen zur Problembehandlung](./media/troubleshooting-monitoring/image10-import-statements-d.png)

5. Führen Sie dieselben Schritte für „HANA\_Configuration\_Minichecks“ aus, und suchen Sie nach _X_ in der Spalte _C_ (Critical).

    Beispielausgaben:

    **HANA\_Configuration\_MiniChecks\_Rev102.01+1** für allgemeine SAP HANA-Prüfungen.

    ![HANA\_Configuration\_MiniChecks\_Rev102.01+1 für allgemeine SAP HANA-Prüfungen](./media/troubleshooting-monitoring/image11-configuration-minichecks.png)

    **HANA\_Services\_Overview** für eine Übersicht der derzeit ausgeführten SAP HANA-Dienste

    ![HANA\_Services\_Overview für eine Übersicht der derzeit ausgeführten SAP HANA-Dienste](./media/troubleshooting-monitoring/image12-services-overview.png)

    **HANA\_Services\_Statistics** für Informationen zu SAP HANA-Diensten (CPU, Arbeitsspeicher usw.).

    ![HANA\_Services\_Statistics für Informationen zu SAP HANA-Diensten](./media/troubleshooting-monitoring/image13-services-statistics.png)

    **HANA\_Configuration\_Overview\_Rev110+** für allgemeine Informationen zur SAP HANA-Instanz.

    ![HANA\_Configuration\_Overview\_Rev110+ für allgemeine Informationen zur SAP HANA-Instanz](./media/troubleshooting-monitoring/image14-configuration-overview.png)

    **HANA\_Configuration\_Parameters\_Rev70+** für eine Überprüfung von SAP HANA-Parametern.

    ![HANA\_Configuration\_Parameters\_Rev70+ für eine Überprüfung von SAP HANA-Parametern](./media/troubleshooting-monitoring/image15-configuration-parameters.png)

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich, wie Sie mithilfe des STONITH-Geräts die Hochverfügbarkeit unter dem SUSE-Betriebssystem einrichten.

> [!div class="nextstepaction"]
> [Hochverfügbarkeitskonfiguration unter SUSE mit STONITH](ha-setup-with-stonith.md)
