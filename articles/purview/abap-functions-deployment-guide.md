---
title: ABAP-Funktionsmodul zur Metadatenextraktion in SAP R3-Azure Purview
description: In diesem Artikel werden die Schritte zum Bereitstellen des ABAP-Funktionsmoduls in SAP Server beschrieben.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/07/2021
ms.openlocfilehash: 7894e187efedf91e3c586dd0d8caaf35e753be3c
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028577"
---
# <a name="deploy-the-metadata-extraction-abap-function-module-for-the-sap-r3-family-of-bridges"></a>Bereitstellen des ABAP-Funktionsmoduls für die Metadatenextraktion für die SAP R3-Brückenfamilie

In diesem Artikel werden die Schritte zum Bereitstellen des ABAP-Funktionsmoduls in SAP Server beschrieben.

## <a name="overview"></a>Übersicht

Die SAP Business Suite 4 HANA (S/4HANA)-, ECC- und R/3 ERP-Brücke können zum Extrahieren von Metadaten vom SAP-Server verwendet werden. Hierzu wird das ABAP-Funktionsmodul auf dem SAP-Server platziert. Auf dieses Funktionsmodul kann die Brücke remote zugreifen, um die im SAP-Server enthaltenen Metadaten abzufragen und (als Textdatei) herunterzuladen.

Bei der Ausführung führt die Bücke eine der folgenden Aktionen durch:

1. Metadaten werden aus einer vorhandenen Datei importiert, die bereits lokal aus einer vorherigen Brückenausführung heruntergeladen wurde.

2. Die ABAP-Modul-API wird aufgerufen, es wird auf den Download gewartet, und dann werden Metadaten aus dieser Datei importiert.

In diesem Dokument werden die erforderlichen Schritte zum Bereitstellen dieses Moduls ausführlich beschrieben.

> [!Note]
> Die folgenden Anweisungen wurden basierend auf der SAP GUI v. 7.2 zusammengestellt.

## <a name="prerequisites"></a>Voraussetzungen

Laden Sie den Quellcode des ABAP-Funktionsmoduls aus Purview Studio herunter. Wenn Sie eine Quelle registrieren und dann einen neuen Scan für [SAP ECC](register-scan-sapecc-source.md) oder [SAP S/4HANA](register-scan-saps4hana-source.md) durchführen, finden Sie wie folgt einen Downloadlink. 

:::image type="content" source="media/abap-functions-deployment-guide/download-abap-code.png" alt-text="Herunterladen des Quellcodes des ABAP-Funktionsmoduls aus Purview Studio" border="true":::

## <a name="deployment-of-the-module"></a>Bereitstellung des Moduls

### <a name="create-a-package"></a>Erstellen eines Pakets

Dieser Schritt ist optional, und ein vorhandenes Paket kann verwendet werden.

1. Melden Sie sich beim SAP S/4HANA- oder SAP ECC-Server an, und öffnen Sie den **Object Navigator** (SE80-Transaktion).

2. Wählen Sie **Package** (Paket) in der Liste aus, geben Sie einen Namen für das neue Paket ein (z. B. Z\_MITI), und klicken Sie dann auf die Schaltfläche **Display** (Anzeige).

3. Klicken Sie im Fenster **Create Package** (Paket erstellen) auf **Yes** (Ja). Daraufhin wird ein Fenster **Package Builder: Create Package** (Paketgenerator: Paket erstellen) geöffnet. Geben Sie einen Wert in das Feld **Short Description** (Kurzbeschreibung) ein, und klicken Sie auf das Symbol **Continue** (Weiter).

4. Wählen Sie im Fenster **Prompt for local Workbench request** (Eingabeaufforderung für lokale Workbenchanforderung) die Option **Own Requests** (Eigene Anforderungen) aus. Wählen Sie die Anforderung **Development** (Entwicklung) aus.

### <a name="create-a-function-group"></a>Erstellen einer Funktionsgruppe

Wählen Sie im Object Navigator in der Liste **Function Group** (Funktionsgruppe) aus, und geben Sie deren Namen unten in das Eingabefeld ein (z. B. Z\_MITI\_FGROUP). Wählen Sie das Symbol **Ansicht** aus.

1. Wählen Sie im Fenster **Create Object** (Objekt erstellen) die Option **Yes** (Ja), um eine neue Funktionsgruppe zu erstellen.

2. Geben Sie im Feld **Short text** (Kurztext) eine entsprechende Beschreibung ein, und klicken Sie auf die Schaltfläche **Save** (Speichern).

3. Wählen Sie ein Paket aus, das im vorherigen Schritt **Erstellen eines Pakets** vorbereitet wurde, und wählen Sie **Save** (Speichern) aus.

4. Bestätigen Sie eine Anforderung durch Klicken auf das Symbol **Weiter**.

5. Aktivieren Sie die Funktionsgruppe.

### <a name="create-the-abap-function-module"></a>Erstellen des ABAP-Funktionsmoduls

1. Wählen Sie die Funktionsgruppe aus, nachdem sie erstellt wurde.

2. Markieren und halten Sie den Namen der Funktionsgruppe im Repository-Browser (oder klicken Sie mit der rechten Maustaste darauf) und wählen Sie **Erstellen** und dann **Funktionsmodul**.

3. Geben Sie `Z_MITI_DOWNLOAD` im Feld **Funktionsmodul** ein. Tragen Sie die richtige Beschreibung in das Eingabefeld **Kurztext** ein.

Wenn das Modul erstellt wurde, geben Sie die folgenden Informationen an:

1. Navigieren Sie zur Registerkarte **Attributes** (Attribute).

2. Wählen Sie für **Processing Type** (Verarbeitungstyp) den Wert **Remote-Enabled Function Module** (Remotefähiges Funktionsmodul) aus.

   Option :::image type="content" source="media/abap-functions-deployment-guide/processing-type.png" alt-text="Register sources - Remote-Enabled-Function Module" border="true"::: (Quellen registrieren - Remotefähiges Funktionsmodul)

3. Navigieren Sie zur Registerkarte **Source code** (Quellcode). Es gibt zwei Möglichkeiten, Code für die Funktion bereitzustellen:

   a. Laden Sie im Hauptmenü die Textdatei hoch, die Sie wie in den [Voraussetzungen](#prerequisites) beschrieben von Purview Studio heruntergeladen haben. Hierfür wählen Sie **Hilfsprogramme**, **Weitere Hilfsprogramme**, dann **Hochladen/Herunterladen** und schließlich **Hochladen** aus.

   b. Öffnen Sie alternativ die Datei, kopieren Sie den Inhalt, und fügen Sie ihn in den Bereich **Source code** (Quellcode) ein.

4. Navigieren Sie zur Registerkarte **Import** (Importieren), und erstellen Sie die folgenden Parameter:

   a.  P\_AREA TYPE DD02L-TABNAME (Optional = True)

   b.  *P\_LOCAL\_PATH TYPE STRING* (Optional = True)

   c.  *P\_LANGUAGE TYPE L001TAB-DATA DEFAULT \'E\'*

   d.  *ROWSKIPS TYPE SO\_INT DEFAULT 0*

   e.  *ROWCOUNT TYPE SO\_INT DEFAULT 0*

   > [!Note]
   > Wählen Sie für alle **Pass Value** (Wert übergeben) aus.

   Option :::image type="content" source="media/abap-functions-deployment-guide/import.png" alt-text="Register sources - Import parameters" border="true"::: (Quellen registrieren - Parameter importieren)

5. Navigieren Sie zur Registerkarte „Tables“ (Tabellen), und definieren Sie Folgendes:

   `EXPORT_TABLE LIKE TAB512`

   Optionen :::image type="content" source="media/abap-functions-deployment-guide/export-table.png" alt-text="Register sources - Tables tab" border="true"::: (Quellen registrieren - Registerkarte „Tabellen“)

6. Navigieren Sie zur Registerkarte **Exceptions** (Ausnahmen) und definieren Sie die folgende Ausnahme:`E_EXP_GUI_DOWNLOADFAILED`

   Optionen :::image type="content" source="media/abap-functions-deployment-guide/exceptions.png" alt-text="Register sources - Exceptions tab" border="true"::: (Quellen registrieren - Registerkarte „Ausnahmen“)

7. Speichern Sie die Funktion (STRG+S drücken oder im Hauptmenü **Function Module** (Funktionsmodul), **Save** (Speichern) auswählen).

8. Wählen Sie das Symbol **Aktivieren** in der Symbolleiste (ctrl+F3) und die Schaltfläche **Fortfahren** im Dialogfenster. Wenn Sie dazu aufgefordert werden, sollten Sie auswählen, dass die generierten einzuschließenden Elemente zusammen mit dem Hauptfunktionsmodul aktiviert werden sollen.

### <a name="testing-the-function"></a>Testen der Funktion

Wenn alle vorherigen Schritte abgeschlossen sind, führen Sie die folgenden Schritte aus, um die Funktion zu testen:

1. Öffnen Sie das Z\_MITI\_DOWNLOAD-Funktionsmodul.

2. Wählen Sie im Hauptmenü **Function Module** (Funktionsmodul), **Test** und dann **Test Function Module** (Funktionsmodul testen) aus (drücken Sie F8).

3. Geben Sie einen Pfad zum Ordner im lokalen Dateisystem in Parameter P\_LOCAL\_PATH ein, und klicken Sie auf der Symbolleiste auf das Symbol **Execute** (Ausführen), oder drücken Sie F8.

4. Geben Sie den Namen des relevanten Bereichs in das Feld P\_AREA ein, wenn eine Datei mit Metadaten heruntergeladen oder aktualisiert werden muss. Nach Ausführung der Funktion muss der im Parameter P\_LOCAL\_PATH angegebene Ordner mehrere Dateien mit Metadaten enthalten. Die Namen der Dateien imitieren Bereiche, die im Feld P\_AREA angegeben werden können.

Die Ausführung der Funktion und das Herunterladen der Metadaten erfolgen wesentlich schneller, wenn die Funktion auf dem Computer gestartet wird, der über eine schnelle Netzwerkverbindung mit dem SAP S/4HANA- oder ECC-Server verfügt.

## <a name="next-steps"></a>Nächste Schritte

- [Registrieren und Überprüfen einer SAP ECC-Quelle](register-scan-sapecc-source.md)
- [Registrieren und Überprüfen einer SAP S/4HANA-Quelle](register-scan-saps4hana-source.md)
