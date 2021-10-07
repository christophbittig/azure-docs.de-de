---
title: Erste Schritte mit dem Tool für konsistente Momentaufnahmen in Azure-Anwendungen für Azure NetApp Files | Microsoft-Dokumentation
description: Enthält eine Installationsanleitung für das Tool für konsistente Momentaufnahmen in Azure-Anwendungen, das Sie mit Azure NetApp Files verwenden können.
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/21/2021
ms.author: phjensen
ms.openlocfilehash: 754a4540bc7164e672abd4fc81d8ecb86e2359a9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124737351"
---
# <a name="get-started-with-azure-application-consistent-snapshot-tool"></a>Erste Schritte mit dem Tool für konsistente Momentaufnahmen in Azure-Anwendungen

Dieser Artikel enthält eine Installationsanleitung für das Tool für konsistente Momentaufnahmen in Azure-Anwendungen, das Sie mit Azure NetApp Files verwenden können.

## <a name="getting-the-snapshot-tools"></a>Erstellen der Momentaufnahmetools

Unsere Empfehlung für Kunden lautet, die aktuellste Version des [AzAcSnap-Installationsprogramms](https://aka.ms/azacsnapdownload) von Microsoft zu nutzen.

Die Datei für die Selbstinstallation verfügt über eine zugeordnete [Signaturdatei für das AzAcSnap-Installationsprogramm](https://aka.ms/azacsnapdownloadsignature), die durch den öffentlichen Schlüssel von Microsoft signiert ist, um die GPG-Überprüfung des heruntergeladenen Installationsprogramms zu ermöglichen.

Führen Sie nach Abschluss dieser Downloads die Schritte für die Installation aus, die in dieser Anleitung angegeben sind.

### <a name="verifying-the-download"></a>Überprüfen des Downloads

Dem herunterladbaren Installationsprogramm ist eine PGP-Signaturdatei mit der Dateinamenerweiterung `.asc` zugeordnet. Mit dieser Datei kann sichergestellt werden, dass es sich bei dem heruntergeladenen Installationsprogramm um eine überprüfte Datei von Microsoft handelt. Der öffentliche PGP-Schlüssel von Microsoft zum Signieren von Linux-Paketen ist unter <https://packages.microsoft.com/keys/microsoft.asc> verfügbar und wurde zum Signieren der Signaturdatei verwendet.

Der öffentliche PGP-Schlüssel von Microsoft kann wie folgt in die lokale Umgebung eines Benutzers importiert werden:

```bash
wget https://packages.microsoft.com/keys/microsoft.asc
gpg --import microsoft.asc
```

Für die folgenden Befehle wird der öffentliche PGP-Schlüssel von Microsoft als vertrauenswürdig angesehen:

1. Auflisten der Schlüssel im Speicher
2. Bearbeiten des Microsoft-Schlüssels
3. Überprüfen des Fingerabdrucks mit `fpr`
4. Signieren des Schlüssels zur Sicherstellung der Vertrauenswürdigkeit

```bash
gpg --list-keys
```

Aufgelistete Schlüssel:
```output
----<snip>----
pub rsa2048 2015- 10 - 28 [SC]
BC528686B50D79E339D3721CEB3E94ADBE1229CF
uid [ unknown] Microsoft (Release signing) gpgsecurity@microsoft.com
```

```bash
gpg --edit-key gpgsecurity@microsoft.com
```

Ausgabe der interaktiven `gpg`-Sitzung zum Signieren des öffentlichen Microsoft-Schlüssels:
```output
gpg (GnuPG) 2.1.18; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
[ unknown] (1). Microsoft (Release signing) <gpgsecurity@microsoft.com>

gpg> fpr
pub rsa2048/EB3E94ADBE1229CF 2015- 10 - 28 Microsoft (Release signing)
<gpgsecurity@microsoft.com>
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF

gpg> sign
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF
Microsoft (Release signing) <gpgsecurity@microsoft.com>
Are you sure that you want to sign this key with your
key "XXX XXXX <xxxxxxx@xxxxxxxx.xxx>" (A1A1A1A1A1A1)
Really sign? (y/N) y

gpg> quit
Save changes? (y/N) y
```

Die PGP-Signaturdatei für das Installationsprogramm kann wie folgt überprüft werden:

```bash
gpg --verify azacsnap_installer_v5.0.run.asc azazsnap_installer_v5.0.run
```

```output
gpg: Signature made Sat 13 Apr 2019 07:51:46 AM STD
gpg: using RSA key EB3E94ADBE1229CF
gpg: Good signature from "Microsoft (Release signing)
<gpgsecurity@microsoft.com>" [full]
```

Ausführlichere Informationen zur Verwendung von GPG finden Sie im [GNU-Datenschutzhandbuch](https://www.gnupg.org/gph/en/manual/book1.html).

## <a name="supported-scenarios"></a>Unterstützte Szenarien

Die Momentaufnahmetools können in den folgenden Szenarien verwendet werden:

- Einzelne SID
- Mehrere SIDs
- HSR
- Horizontales Skalieren
- MDC (nur Unterstützung eines einzelnen Mandanten)
- Einzelner Container
- SUSE-Betriebssystem
- RHEL-Betriebssystem
- SKU-TYP I
- SKU-TYP II

Weitere Informationen finden Sie unter [Unterstützte Szenarien für große HANA-Instanzen](../virtual-machines/workloads/sap/hana-supported-scenario.md).

## <a name="snapshot-support-matrix-from-sap"></a>Unterstützungsmatrix für Momentaufnahmen von SAP

In der folgenden Matrix ist angegeben, welche Versionen von SAP HANA von SAP für Speichermomentaufnahmen unterstützt werden.

| Datenbankversionen       |1.0 SPS12|2.0 SPS0|2.0 SPS1|2.0 SPS2|2.0 SPS3|2.0 SPS4|
|-------------------------|---------|--------|--------|--------|--------|--------|
|Einzelne Containerdatenbank| √       | √      | -      | -      | -      | -      |
|Ein MDC-Mandant        | -       | -      | √      | √      | √      | √      |
|Mehrere MDC-Mandanten     | -       | -      | -      | -      | -      | √      |
> √ = <small>Unterstützung von SAP für Speichermomentaufnahmen vorhanden</small>

## <a name="important-things-to-remember"></a>Wichtige Punkte

- Nach dem Einrichten der Momentaufnahmetools sollten Sie ständig überwachen, wie viel Speicherplatz verfügbar ist, und bei Bedarf regelmäßig die alten Momentaufnahmen löschen, um eine Überlastung des Speichers zu vermeiden.
- Verwenden Sie immer die neuesten Momentaufnahmetools.
- Verwenden Sie für die gesamte Umgebung dieselbe Version der Momentaufnahmetools.
- Testen Sie die Momentaufnahmetools, und machen Sie sich mit den erforderlichen Parametern und der Ausgabe des Befehls vertraut, bevor Sie diese im Produktionssystem verwenden.
- Beim Einrichten des HANA-Benutzers für Sicherungen (siehe Details weiter unten in diesem Dokument) müssen Sie dies für den Benutzer für jede HANA-Instanz durchführen. Erstellen Sie ein SAP HANA-Benutzerkonto, um auf die HANA-Instanz unter der SYSTEMDB (nicht in der SID-Datenbank) für MDC zuzugreifen. In einer Umgebung mit nur einem Container kann dies unter der Mandantendatenbank eingerichtet werden.
- Kunden müssen den öffentlichen SSH-Schlüssel für den Speicherzugriff angeben. Diese Aktion muss einmal pro Knoten und für jeden Benutzer durchgeführt werden, für den der Befehl ausgeführt wird.
- Die Anzahl von Momentaufnahmen ist pro Volume auf 250 beschränkt.
- Verwenden Sie beim manuellen Bearbeiten der Konfigurationsdatei immer einen Linux-Text-Editor, z. B. „vi“, und nicht einen Editor wie den Editor von Windows. Bei Verwendung des Editors von Windows kann das Dateiformat beschädigt werden.
  - Richten Sie `hdbuserstore` für den SAP HANA-Benutzer ein, um mit SAP HANA zu kommunizieren.
- Für die Notfallwiederherstellung: Vor der Einrichtung der Notfallwiederherstellung müssen die Momentaufnahmetools auf dem Knoten für die Notfallwiederherstellung getestet werden.
- Führen Sie eine regelmäßige Überprüfung des Speicherplatzes durch. Das automatisierte Löschen von Protokollen wird mit der Option `--trim` von `azacsnap -c backup` für SAP HANA 2 und neuere Releases verwaltet.
- **Risiko: Keine Erstellung von Momentaufnahmen**: Die Momentaufnahmetools interagieren nur mit dem Knoten des SAP HANA-Systems, der in der Konfigurationsdatei angegeben ist.  Wenn dieser Knoten nicht mehr verfügbar ist, gibt es keinen Mechanismus, mit dem die Kommunikation mit einem anderen Knoten automatisch gestartet werden kann.  
  - Für ein Szenario vom Typ **Horizontale SAP HANA-Skalierung mit Standby** werden die Momentaufnahmetools häufig auf dem Masterknoten installiert und konfiguriert. Wenn der Masterknoten aber nicht mehr verfügbar ist, übernimmt der Standbyknoten die Rolle des Masterknotens. In diesem Fall sollte das Implementierungsteam die Momentaufnahmetools auf beiden Knoten (Master und Standby) konfigurieren, um zu vermeiden, dass einige Momentaufnahmen ausgelassen und nicht erstellt werden. Im Normalzustand erstellt der Masterknoten HANA-Momentaufnahmen, die von crontab initiiert werden. Nach einem Failover des Masterknotens müssen diese Momentaufnahmen aber über einen anderen Knoten ausgeführt werden, z. B. den neuen Masterknoten (zuvor der Standbyknoten). Zur Erreichung dieses Ergebnisses müssen für den Standbyknoten die folgenden Bedingungen erfüllt sein: Installation des Momentaufnahmetools, Aktivierung der Speicherkommunikation, Konfiguration von hdbuserstore und von `azacsnap.json` und Staging der crontab-Befehle vor dem Failover.
  - Für ein Szenario vom Typ **SAP HANA HSR-Hochverfügbarkeit** empfehlen wir Ihnen, die Momentaufnahmetools auf beiden Knoten (primär und sekundär) zu installieren, zu konfigurieren und zu planen. Wenn der primäre Knoten dann nicht mehr verfügbar ist, übernimmt der sekundäre Knoten diese Aufgabe und erstellt die Momentaufnahmen. Im Normalzustand erstellt der primäre Knoten die per crontab initiierten HANA-Momentaufnahmen, und der sekundäre Knoten versucht ebenfalls, Momentaufnahmen zu erstellen. Dies ist aber nicht erfolgreich, da der primäre Knoten richtig funktioniert.  Nach dem Failover des primären Knotens werden diese Momentaufnahmen dann aber auf dem sekundären Knoten erstellt. Zur Erreichung dieses Ergebnisses müssen für den sekundären Knoten die folgenden Bedingungen erfüllt sein: Installation des Momentaufnahmetools, Aktivierung der Speicherkommunikation, Konfiguration von `hdbuserstore` und von „azacsnap.json“ und Aktivierung von crontab vor dem Failover.

## <a name="guidance-provided-in-this-document"></a>Anleitungen

In den folgenden Anleitungen wird die Nutzung der Momentaufnahmetools veranschaulicht.

### <a name="taking-snapshot-backups"></a>Erstellen von Momentaufnahmesicherungen

- [Voraussetzungen für Speichermomentaufnahmen](azacsnap-installation.md#prerequisites-for-installation)
  - [Aktivieren der Kommunikation mit dem Speicher](azacsnap-installation.md#enable-communication-with-storage)
  - [Aktivieren der Kommunikation mit der Datenbank](azacsnap-installation.md#enable-communication-with-database)
- [Manuelles Erstellen von Momentaufnahmen](azacsnap-tips.md#take-snapshots-manually)
- [Einrichten von automatischen Momentaufnahmesicherungen](azacsnap-tips.md#setup-automatic-snapshot-backup)
- [Überwachen der Momentaufnahmen](azacsnap-tips.md#monitor-the-snapshots)
- [Löschen einer Momentaufnahme](azacsnap-tips.md#delete-a-snapshot)
- [Wiederherstellen einer Momentaufnahme](azacsnap-tips.md#restore-a-snapshot)
- [Wiederherstellen einer `boot`-Momentaufnahme](azacsnap-tips.md#restore-a-boot-snapshot)
- [Wichtige Fakten zu Momentaufnahmen](azacsnap-tips.md#key-facts-to-know-about-snapshots)

> Momentaufnahmen werden sowohl für die Verwendung mit nur einer SID als auch mit mehreren SIDs getestet.

### <a name="performing-disaster-recovery"></a>Durchführen der Notfallwiederherstellung

- [Voraussetzungen für die Einrichtung der Notfallwiederherstellung](azacsnap-disaster-recovery.md#prerequisites-for-disaster-recovery-setup)
- [Einrichten der Notfallwiederherstellung](azacsnap-disaster-recovery.md#set-up-disaster-recovery)
- [Überwachen der Datenreplikation vom primären Standort zum Standort der Notfallwiederherstellung](azacsnap-disaster-recovery.md#monitor-data-replication-from-primary-to-dr-site)
- [Ausführen eines Failovers zum Standort für die Notfallwiederherstellung](azacsnap-disaster-recovery.md#perform-a-failover-to-dr-site)

## <a name="next-steps"></a>Nächste Schritte

- [Installieren des Tools für konsistente Momentaufnahmen](azacsnap-installation.md)
