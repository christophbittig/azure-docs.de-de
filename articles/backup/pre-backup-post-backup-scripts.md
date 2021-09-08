---
title: Verwenden von vor der Sicherung und nach der Sicherung auszuführenden Skripts
description: In diesem Artikel wird die Prozedur zum Festlegen von Skripts vor und nach der Sicherung beschrieben. Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 07/06/2021
ms.openlocfilehash: 48d65679f4ff1b6486513067058c1f385e4f1434
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/09/2021
ms.locfileid: "113568592"
---
# <a name="using-pre-backup-and-post-backup-scripts"></a>Verwenden von vor der Sicherung und nach der Sicherung auszuführenden Skripts

Gilt für: Microsoft Azure Backup Server (MABS)

Ein _Skript vor der Sicherung_ ist ein Skript, das sich auf dem geschützten Computer befindet, und vor jedem MABS-Sicherungsauftrag zur Vorbereitung der geschützten Datenquelle auf die Sicherung ausgeführt wird.

Ein _Skript nach der Sicherung_ ist ein Skript, das nach einem MABS-Sicherungsauftrag ausgeführt wird, um Verarbeitungsvorgänge nach der Sicherung auszuführen, wie etwa einen virtuellen Computer online schalten.

Wenn Sie einen Schutz-Agent auf einem Computer installieren, wird dem _Installationspfadordner_ „\Microsoft Data Protection Manager\DPM\Scripting“ auf dem geschützten Computer die Datei „ScriptingConfig.xml“ hinzugefügt. Sie können für jede geschützte Datenquelle auf dem Computer in der Datei "ScriptingConfig.xml" ein Skript vor der Sicherung und ein Skript nach der Sicherung angeben.

>[!Note]
>Bei den Skripts vor und nach der Sicherung darf es sich nicht um VBScript-Skripts handeln. Stattdessen müssen Sie für Ihr Skript, das **cscript myscript.vbs** enthält, einen Wrapper-Befehl verwenden.

Wenn von MABS ein Schutzauftrag ausgeführt wird, wird die Datei „ScriptingConfig.xml“ auf dem geschützten Computer geprüft. Wenn ein Skript vor der Sicherung angegeben wurde, wird von MABS zuerst das Skript, dann der Auftrag ausgeführt. Wenn ein Skript nach der Sicherung angegeben wurde, wird von MABS zuerst der Auftrag, dann das Skript ausgeführt.

>[!Note]
>Schutzaufträge umfassen die Erstellung von Replikaten, schnelle vollständige Sicherung, Synchronisierung und Konsistenzprüfung.

Die Skriptausführung erfolgt unter Verwendung des lokalen Systemkontos. Sie sollten sicherstellen, dass die Skripts nur über Lese- und Ausführungsberechtigungen für das Administrator- und das lokale Systemkonto verfügen. Diese Berechtigungsebene verhindert, dass unbefugte Benutzer die Skripts ändern.

**ScriptingConfig.xml**

```
<?xml version="1.0" encoding="utf-8"?>
<ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" 
xmlns:xsd="http://www.w3.org/2001/XMLSchema" 
xmlns="http://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
   <DatasourceScriptConfig DataSourceName="Data source">
     <PreBackupScript>”Path\Script Parameters” </PreBackupScript>
     <PostBackupScript>"Path\Script Parameters” </PostBackupScript>
     <TimeOut>30</TimeOut>
   </DatasourceScriptConfig>
</ScriptConfiguration>
```

So geben Sie Skripts vor und nach der Sicherung an

1. Öffnen Sie auf dem geschützten Computer die Datei „ScriptingConfig.xml“ in einem XML- oder Text-Editor.

   >[!Note]
   >Das Attribut „DataSourceName“ muss als **Laufwerk:** angegeben werden (z. B. „D:“, wenn sich die Datenquelle in Laufwerk D befindet).

1. Führen Sie das "DatasourceScriptConfig"-Element für jede Datenquelle wie folgt aus:


   1. Geben Sie für das "DataSourceName"-Attribut das Volume der Datenquelle (bei Dateidatenquellen) oder den Namen der Datenquelle (für alle anderen Datenquellen) ein. Der Name der Datenquelle für Anwendungsdaten muss das Format _Instanz\Datenbank_ für SQL, _Name der Speichergruppe_ für Exchange, _Logischer Pfad\Name der Komponente_ für Virtual Server und _SharePoint-Farm\Name des SQL Server-Computers\Name der SQL-Instanz\SharePoint-Konfigurationsdatenbank_ für Windows SharePoint Services aufweisen.
   1. Geben Sie im _PreBackupScript_-Tag den Pfad- und Skriptnamen ein.
   1. Geben Sie im _PreBackupCommandLine_-Tag durch Leerzeichen voneinander getrennt Befehlszeilenparameter ein, die an die Skripts übergeben werden sollen.
   1. Geben Sie im _PostBackupScript_-Tag den Pfad- und Skriptnamen ein.
   1. Geben Sie im _PostBackupCommandLine_-Tag durch Leerzeichen voneinander getrennt Befehlszeilenparameter ein, die an die Skripts übergeben werden sollen.
   1. Geben Sie im Tag _TimeOut_ die Zeit in Minuten ein, die MABS nach dem Aufrufen eines Skripts gewartet werden soll, bis das Zeitlimit erreicht ist und das Skript als fehlgeschlagen gekennzeichnet wird.

1. Speichern Sie die Datei "ScriptingConfig.xml".

>[!Note]
>MABS fügt dem Befehl für das Skript nach der Sicherung einen zusätzlichen booleschen Parameter (true/false) an, der den Status des MABS-Sicherungsauftrags angibt.
