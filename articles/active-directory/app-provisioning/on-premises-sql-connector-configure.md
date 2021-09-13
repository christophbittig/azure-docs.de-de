---
title: Konfigurieren eines generischen SQL-Connectors für den Azure AD-ECMA-Connectorhost
description: In diesem Dokument wird beschrieben, wie Sie den generischen SQL-Connector für den Azure AD-ECMA-Connectorhost konfigurieren.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 06/06/2021
ms.author: billmath
ms.reviewer: arvinh
ms.openlocfilehash: a78ae13af84f3453e3a6119f163d90cd37be16bc
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 07/22/2021
ms.locfileid: "114437364"
---
# <a name="azure-ad-ecma-connector-host-generic-sql-connector-configuration"></a>Konfigurieren eines generischen SQL-Connectors für den Azure AD-ECMA-Connectorhost

>[!IMPORTANT]
> Die Vorschauversion der lokalen Bereitstellung ist derzeit nur auf Einladung verfügbar. Verwenden Sie das [Zugriffsanforderungsformular](https://aka.ms/onpremprovisioningpublicpreviewaccess), um Zugriff auf die Funktion anzufordern. Wir öffnen die Vorschauversion im Rahmen der Vorbereitung auf die allgemeine Verfügbarkeit in den nächsten Monaten für weitere Kunden und Connectors.

In diesem Artikel wird beschrieben, wie Sie einen neuen SQL-Connector für den ECMA Connector Host für Azure AD (Azure Active Directory) erstellen und konfigurieren. Dieser Schritt muss erfolgen, nachdem Sie den ECMA Connector Host für Azure AD erfolgreich installiert haben.

>[!NOTE] 
> In diesem Artikel wird nur die Konfiguration des generischen SQL-Connectors behandelt. Ein ausführliches Beispiel für die Einrichtung des generischen SQL-Connectors finden Sie unter [Tutorial: Generischer SQL-Connector für den ECMA Connector Host](tutorial-ecma-sql-connector.md)

 Dieser Flow führt Sie durch die Installation und Konfiguration des ECMA Connector Host für Azure AD.

 ![Diagramm des Installationsflows](./media/on-premises-sql-connector-configure/flow-1.png)

Weitere Installations- und Konfigurationsinformationen finden Sie unter:
   - [Voraussetzungen für den Azure AD-ECMA-Connectorhost](on-premises-ecma-prerequisites.md)
   - [Installation des Azure AD-ECMA-Connectorhosts](on-premises-ecma-install.md)
   - [Konfigurieren des Azure AD-ECMA-Connectorhosts und des Bereitstellungsagents](on-premises-ecma-configure.md)

Je nachdem, welche Optionen Sie auswählen, sind einige Bildschirme des Assistenten möglicherweise nicht verfügbar, und die Informationen können sich geringfügig unterscheiden. Für die Zwecke dieser Konfiguration wird der Objekttyp „Benutzer“ verwendet. Die folgenden Informationen können Ihnen bei der Konfiguration nützlich sein. 

#### <a name="supported-systems"></a>Unterstützte Systeme
* Microsoft SQL Server und Azure SQL
* IBM DB2 10.x
* IBM DB2 9.x
* Oracle 10 und 11g
* Oracle 12c und 18c
* MySQL 5.x

## <a name="create-a-generic-sql-connector"></a>Erstellen eines generischen SQL-Connectors

So erstellen Sie einen generischen SQL-Connector:

 1. Wählen Sie auf dem Desktop die Verknüpfung „ECMA Connector Host“ aus.
 1. Wählen Sie **Neuer Connector** aus.
 
     ![Screenshot: Auswahl von „Neuer Connector“](.\media\on-premises-sql-connector-configure\sql-1.png)

 1. Füllen Sie die Felder auf der Seite **Eigenschaften** aus, und wählen Sie **Weiter** aus. In der Tabelle unter der Abbildung finden Sie Anweisungen für die einzelnen Felder.
 
     ![Screenshot: Eingabe von Eigenschaften](.\media\on-premises-sql-connector-configure\sql-2.png)

     |Eigenschaft|BESCHREIBUNG|
     |-----|-----|
     |Name|Der Name dieses Connectors|
     |Zeitgeber für automatische Synchronisierung (Minuten)|Der zulässige Mindestwert ist 120 Minuten.|
     |Geheimes Token|123456 (Das Token muss eine Zeichenfolge mit 10 bis 20 ASCII-Buchstaben und/oder Ziffern sein.)|
     |BESCHREIBUNG|Die Beschreibung des Connectors|
     |Erweiterungs-DLL|Wählen Sie für einen generischen SQL-Connector **Microsoft.IAM.Connector.GenericSql.dll** aus.|
 1. Füllen Sie die Felder auf der Seite **Konnektivität** aus, und wählen Sie **Weiter** aus. In der Tabelle unter der Abbildung finden Sie Anweisungen für die einzelnen Felder.
 
     ![Screenshot: Eingabe unter „Konnektivität“](.\media\on-premises-sql-connector-configure\sql-3.png)

     |Eigenschaft|BESCHREIBUNG|
     |-----|-----|
     |DSN-Datei|Die Datei mit dem Datenquellennamen, die zum Herstellen einer Verbindung mit der SQL Server-Instanz verwendet wird|
     |Benutzername|Der Benutzername einer Einzelperson mit Rechten für die SQL Server-Instanz. Dieser muss für eigenständige Server das Format „Hostname\Konto_des_SQL-Administrators“ und für Domänenmitgliedsserver das Format „Domäne\Konto_des_SQL-Administrators“ aufweisen.|
     |Kennwort|Das Kennwort des angegebenen Benutzernamens|
     |DN ist Anker|Sofern nicht bekannt ist, dass Ihre Umgebung diese Einstellungen erfordert, aktivieren Sie die Kontrollkästchen **DN ist Anker** und **Exporttyp:Objekt ersetzen** nicht.|
     |Exporttyp:Objekt ersetzen||
 1. Füllen Sie die Felder auf der Seite **Schema 1** aus, und wählen Sie **Weiter** aus. In der Tabelle unter der Abbildung finden Sie Anweisungen für die einzelnen Felder.
 
     ![Screenshot: Seite „Schema 1“](.\media\on-premises-sql-connector-configure\sql-4.png)

     |Eigenschaft|BESCHREIBUNG|
     |-----|-----|
     |Erkennungsmethode für Objekttyp|Die Methode, mit der der Objekttyp erkannt wird, den der Connector bereitstellen soll.|
     |Liste mit festem Wert/Tabelle/Sicht/SP (Stored Procedure, gespeicherte Prozedur)|Dieses Feld sollte **User** (Benutzer) enthalten.|
     |Spaltenname für Tabelle/Sicht/SP||
     |Parameter gespeicherter Prozeduren||
     |Angeben einer SQL-Abfrage zum Erkennen von Objekttypen||
 1. Füllen Sie die Felder auf der Seite **Schema 2** aus, und wählen Sie **Weiter** aus. In der Tabelle unter der Abbildung finden Sie Anweisungen für die einzelnen Felder. Dieser Schemabildschirm kann sich geringfügig unterscheiden oder zusätzliche Informationen enthalten, und zwar abhängig von den Objekttypen, die Sie im vorherigen Schritt ausgewählt haben.
 
     ![Screenshot: Seite „Schema 2“](.\media\on-premises-sql-connector-configure\sql-5.png)

     |Eigenschaft|BESCHREIBUNG|
     |-----|-----|
     |Benutzer:Attributerkennung|Diese Eigenschaft sollte auf **Table** (Tabelle) festgelegt werden.|
     |Benutzer:Tabelle/Sicht/SP|Dieses Feld muss **Employees** (Mitarbeiter) enthalten.|
     |Benutzer:Name der Tabelle mit mehreren Werten/Sichten||
     |Benutzer:Parameter der gespeicherten Prozedur||
     |Benutzer:Angeben einer SQL-Abfrage zum Erkennen von Attributen||
 1. Füllen Sie die Felder auf der Seite **Schema 3** aus, und wählen Sie **Weiter** aus. In der Tabelle unter der Abbildung finden Sie Anweisungen für die einzelnen Felder. Die angezeigten Attribute hängen von den Informationen ab, die Sie im vorherigen Schritt angegeben haben.
 
     ![Screenshot: Seite „Schema 3“](.\media\on-premises-sql-connector-configure\sql-6.png)

     |Eigenschaft|BESCHREIBUNG|
     |-----|-----|
     |DN-Attribut für Benutzer auswählen||
 1. Überprüfen Sie auf der Seite **Schema 4** die **DataType-Attribute** und Datenflussrichtung des Connectors. Sie können sie bei Bedarf anpassen und **Weiter** auswählen.
 
     ![Screenshot: Seite „Schema 4“](.\media\on-premises-sql-connector-configure\sql-7.png)  
 1. Füllen Sie die Felder auf der Seite **Global** aus, und wählen Sie **Weiter** aus. In der Tabelle unter der Abbildung finden Sie Anweisungen für die einzelnen Felder.
 
     ![Screenshot: Seite „Global“](.\media\on-premises-sql-connector-configure\sql-8.png)

     |Eigenschaft|BESCHREIBUNG|
     |-----|-----|
     |Wasserzeichenabfrage||
     |Zeitzone der Datenquelle|Wählen Sie die Zeitzone aus, in der sich die Datenquelle befindet.|
     |Datum-/Uhrzeitformat der Datenquelle|Geben Sie das Format für die Datenquelle an.|
     |Benannte Parameter zum Ausführen einer gespeicherten Prozedur verwenden||
     |Vorgangsmethoden||
     |Name der Erweiterung||
     |Kennwort festlegen: SP-Name||
     |Kennwort festlegen: SP-Parameter||
 1. Stellen Sie auf der Seite **Partition auswählen** sicher, dass die richtigen Partitionen ausgewählt sind, und wählen Sie **Weiter** aus.
 
     ![Screenshot: Seite „Partition auswählen“](.\media\on-premises-sql-connector-configure\sql-9.png)

 1. Wählen Sie auf der Seite **Ausführungsprofile** die gewünschten Ausführungsprofile aus, und wählen Sie **Weiter** aus.
 
     ![Screenshot: Seite „Ausführungsprofile“](.\media\on-premises-sql-connector-configure\sql-10.png)

     |Eigenschaft|BESCHREIBUNG|
     |-----|-----|
     |Exportieren|Ausführungsprofil zum Exportieren von Daten in SQL. Dieses Ausführungsprofil ist erforderlich.|
     |Vollständiger Import|Ausführungsprofil zum Importieren aller Daten aus den zuvor angegebenen SQL-Quellen.|
     |Deltaimport|Ausführungsprofil, das nur Änderungen aus SQL seit dem letzten vollständigen oder Deltaimport importiert.|
 
 1. Füllen Sie die Felder auf der Seite **Ausführungsprofile** aus, und wählen Sie **Weiter** aus. In der Tabelle unter der Abbildung finden Sie Anweisungen für die einzelnen Felder.
  
     ![Screenshot: Eingabe von Exportinformationen](.\media\on-premises-sql-connector-configure\sql-11.png)

     |Eigenschaft|BESCHREIBUNG|
     |-----|-----|
     |Vorgangsmethode||
     |Tabelle/Sicht/SP||
     |Name des Startindexparameters||
     |Name des Endindexparameters||
     |Parameter gespeicherter Prozeduren||
 
 1. Füllen Sie die Felder auf der Seite **Objekttypen** aus, und wählen Sie **Weiter** aus. In der Tabelle unter der Abbildung finden Sie Anweisungen für die einzelnen Felder. 
 
     ![Screenshot: Seite „Objekttypen“](.\media\on-premises-sql-connector-configure\sql-12.png)

     |Eigenschaft|BESCHREIBUNG|
     |-----|-----|
     |Zielobjekt|Das Objekt, das Sie konfigurieren|
     |Anchor|Das Attribut, das als Objektanker verwendet wird. Dieses Attribut muss im Zielsystem eindeutig sein. Der Azure AD-Bereitstellungsdienst fragt nach dem ersten Zyklus mithilfe dieses Attributs den ECMA-Host ab. Dieser Ankerwert muss mit dem Ankerwert in Schema 3 identisch sein.|
     |Abfrageattribut|Wird vom ECMA-Host zum Abfragen des In-Memory-Caches verwendet. Dieses Attribut muss eindeutig sein.|
     |DN|Das Attribut, das für den Distinguished Name von Zielobjekten verwendet wird. In den meisten Fällen sollte das Kontrollkästchen **Automatisch generiert** aktiviert werden. Falls sie deaktiviert ist, stellen Sie sicher, dass das DN-Attribut einem Attribut in Azure AD zugeordnet ist, das den DN in diesem Format speichert: CN = anchorValue, Object = objectType.|
 
 1. Der ECMA-Host erkennt die vom Zielsystem unterstützten Attribute. Sie können wählen, welches dieser Attribute Sie für Azure AD verfügbar machen möchten. Diese Attribute können dann im Azure-Portal zur Bereitstellung konfiguriert werden. Wählen Sie auf der Seite **Attribute auswählen** in der Dropdownliste die Attribute aus, die Sie hinzufügen möchten.
 
     ![Screenshot: Seite „Attribute auswählen“](.\media\on-premises-sql-connector-configure\sql-13.png)

1. Überprüfen Sie auf der Seite **Bereitstellung aufheben** die Bereitstellungsinformationen, und nehmen Sie ggf. Anpassungen vor. Die auf der vorherigen Seite ausgewählten Attribute stehen auf der Seite **Bereitstellung aufheben** nicht zur Auswahl. Wählen Sie **Fertig stellen** aus.

     ![Screenshot: Seite „Bereitstellung aufheben“](.\media\on-premises-sql-connector-configure\sql-14.png)

## <a name="next-steps"></a>Nächste Schritte

- [App-Bereitstellung](user-provisioning.md)
- [Azure AD-ECMA-Connectorhost: Installation](on-premises-ecma-install.md)
- [Azure AD-ECMA-Connectorhost: Konfiguration](on-premises-ecma-configure.md)
- [Tutorial: Generischer SQL-Connector für den ECMA Connector Host](tutorial-ecma-sql-connector.md)
