---
title: Versionshinweise für das Tool für konsistente Momentaufnahmen in Azure-Anwendungen für Azure NetApp Files | Microsoft-Dokumentation
description: Bietet Versionshinweise zum Tool für konsistente Momentaufnahmen in Azure-Anwendungen, das Sie mit Azure NetApp Files verwenden können.
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
ms.topic: conceptual
ms.date: 05/27/2021
ms.author: phjensen
ms.openlocfilehash: a580a135d2158234c84f3378b024997679ad6017
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/13/2021
ms.locfileid: "124778322"
---
# <a name="release-notes-for-azure-application-consistent-snapshot-tool"></a>Versionshinweise für das Tool für konsistente Momentaufnahmen in Azure-Anwendungen

Auf dieser Seite werden wichtige Änderungen aufgelistet, die an AzAcSnap vorgenommen wurden, um neue Funktionen bereitzustellen oder Fehler zu beheben.

## <a name="aug-2021"></a>Aug-2021

### <a name="azacsnap-v502-build_2021082719086---patch-update-to-v501"></a>AzAcSnap v5.0.2 (Build_20210827.19086): Patchupdate auf v5.0.1

AzAcSnap v5.0.2 (Build_20210827.19086) wird als Patchupdate für den Branch v5.0 mit den folgenden Fehlerbehebungen und Verbesserungen bereitgestellt:

- Ignorieren der `ssh`-Exitcodes 255.  In einigen Fällen gibt der Befehl `ssh`, der für die Kommunikation mit dem Speicher in Azure (große Instanzen) verwendet wird, den Exitcode 255 aus, obwohl keine Fehler oder Ausführungsfehler aufgetreten sind (siehe `man ssh` „EXIT STATUS“). AzAcSnap würde dies anschließend als Fehler abfangen und abbrechen.  Mit diesem Update wird eine zusätzliche Überprüfung durchgeführt, um die richtige Ausführung zu überprüfen. Dies schließt zusätzlich zu herkömmlichen Exitcodeüberprüfungen die Analyse von STDOUT und STDERR für `ssh` auf Fehler ein.
- Die Überprüfung des Quellpfads für das Installationsprogramm von hdbuserstore wurde korrigiert.  Das Installationsprogramm überprüfte das Vorhandensein eines falschen Quellverzeichnisses für den hdbuserstore der Benutzer*innen, die die Installation ausführten. Dies wurde korrigiert, sodass nun auf `~/.hdb` überprüft wird.  Dies gilt für Systeme (z. B. Azure (große Instanz)), bei denen der hdbuserstore vor der Installation von `azacsnap` für `root`-Benutzer*innen vorkonfiguriert wurde.
- Das Installationsprogramm zeigt jetzt die Version an, die installiert/extrahiert wird (wenn das Installationsprogramm ohne Argumente ausgeführt wird).

Laden Sie die [neueste Version](https://aka.ms/azacsnapinstaller) des Installationsprogramms herunter, und informieren Sie sich über die [ersten Schritte](azacsnap-get-started.md).

## <a name="may-2021"></a>Mai 2021

### <a name="azacsnap-v501-build-2021052414837---patch-update-to-v50"></a>AzAcSnap v5.0.1 (Build: 20210524.14837) - Patchupdate auf v5.0

AzAcSnap v5.0.1 (Build: 20210524.14837) wird als Patchupdate für den Branch v5.0 mit den folgenden Fehlerbehebungen und Verbesserungen bereitgestellt:

- Verbesserte Exitcodebehandlung.  In einigen Fällen wurde der Exitcode 0 (null) ausgegeben, auch wenn ein Ausführungsfehler aufgetreten ist, bei dem er ungleich 0 (null) sein sollte.  Exitcodes sollten jetzt nur 0 (null) bei erfolgreicher Ausführung von `azacsnap` bis zum Abschluss und ungleich 0 (null) bei einem Fehler sein.  Darüber hinaus wurde die interne Fehlerbehandlung von AzAcSnap erweitert, um den Exitcode der externen Befehle (z. B. hdbsql, ssh), die von AzAcSnap ausgeführt werden, zu erfassen und auszugeben, wenn sie die Ursache des Fehlers sind.

## <a name="april-2021"></a>April 2021

### <a name="azacsnap-v50-build-202104216349---ga-released-21-april-2021"></a>AzAcSnap v5.0 (Build: 20210421.6349) – GA-veröffentlicht (21.04.2021)

AzAcSnap v5.0 (Build: 20210421.6349) wurde allgemein verfügbar gemacht und hatte für diesen Build die folgenden Korrekturen und Verbesserungen:

- Das hdbsql-Wiederholungs-Timeout (um auf eine Antwort von SAP HANA zu warten) wird automatisch auf die Hälfte der „savePointAbortWaitSeconds“ festgelegt, um Racebedingungen zu vermeiden.  Die Einstellung für „savePointAbortWaitSeconds“ kann direkt in der JSON-Konfigurationsdatei geändert werden und muss mindestens 600 Sekunden lang sein.

## <a name="march-2021"></a>März-2021

### <a name="azacsnap-v50-preview-build2021031830771"></a>AzAcSnap v5.0 Preview (Build: 20210318.30771)

AzAcSnap v 5.0 Preview (Build: 20210318.30771) wurde mit den folgenden Korrekturen und Verbesserungen freigegeben:

- Die Notwendigkeit, die AZACSNAP-Benutzer*innen in den SAP HANA-Mandantendatenbanken hinzuzufügen, wurde beseitigt. Weitere Informationen finden Sie im Abschnitt [Aktivieren der Kommunikation mit der Datenbank](azacsnap-installation.md#enable-communication-with-database).
- Fix, um eine [Wiederherstellung](azacsnap-cmd-ref-restore.md) mit Volumes zuzulassen, die mit manueller QoS konfiguriert sind.
- Mutex-Steuerelement zum Drosseln von SSH-Verbindungen für Azure (große Instanzen) hinzugefügt.
- Fix für das Installationsprogramm für die Behandlung von Pfadnamen mit Leerzeichen und anderen verwandten Problemen.
- Als Vorbereitung für die Unterstützung anderer Datenbankserver wurde der optionale Parameter „--hanasid“ in „--dbsid“ geändert.

## <a name="next-steps"></a>Nächste Schritte

- [Erste Schritte mit dem Tool für konsistente Momentaufnahmen in Azure-Anwendungen](azacsnap-get-started.md)
