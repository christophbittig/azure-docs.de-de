---
title: 'Azure VMware Solution by CloudSimple: Berechtigungsmodell für die private Cloud'
description: Beschreibt das Berechtigungsmodell, die Gruppen und Kategorien der privaten CloudSimple-Cloud.
author: suzizuber
ms.author: v-szuber
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: df65f882ff99b4c40f0cffa49d3674f8c13ce1a1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305634"
---
# <a name="cloudsimple-private-cloud-permission-model-of-vmware-vcenter"></a>Berechtigungsmodell der privaten CloudSimple-Cloud von VMware-vCenter

Cloudsimple behält vollständigen Administratorzugriff auf die Umgebung der privaten Cloud bei. Jedem CloudSimple-Kunden werden ausreichende Administratorberechtigungen rechte erteilt, um die virtuellen Computer in seiner Umgebung bereitstellen und verwalten zu können.  Bei Bedarf können Sie Ihre Berechtigungen vorübergehend zum Ausführen administrativer Funktionen eskalieren.

## <a name="cloud-owner"></a>Cloudbesitzer

Wenn Sie eine private Cloud erstellen, wird ein **CloudOwner**-Benutzer in der Domäne für einmaliges Anmelden (SSO) von vCenter mit **Cloud-Owner-Role**-Zugriff zum Verwalten von Objekten in der privaten Cloud erstellt. Dieser Benutzer kann auch weitere [vCenter-Identitätsquellen](set-vcenter-identity.md) und andere Benutzer für das vCenter der privaten Cloud einrichten.

> [!NOTE]
> Der Standardbenutzer für die vCenter-Instanz Ihrer privaten CloudSimple-Cloud ist cloudowner@cloudsimple.local, wenn eine private Cloud erstellt wird.

## <a name="user-groups"></a>Benutzergruppen

Eine Gruppe namens **Cloud-Owner-Group** wird während der Bereitstellung einer privaten Cloud erstellt. Benutzer in dieser Gruppe können verschiedene Teile der vSphere-Umgebung in der privaten Cloud verwalten. Diese Gruppe erhält automatisch die Berechtigungen **Cloud-Owner-Role**, und der **CloudOwner**-Benutzer wird als Mitglied dieser Gruppe hinzugefügt.  Cloudsimple erstellt zusätzliche Gruppen mit eingeschränkten Berechtigungen, um die Verwaltung zu vereinfachen.  Sie können jeden beliebigen Benutzer zu diesen vorab erstellten Gruppen hinzufügen, und die unten definierten Berechtigungen werden den Benutzern in den Gruppen automatisch zugewiesen.

### <a name="pre-created-groups"></a>Vorab erstellte Gruppen

| Gruppenname | Zweck | Role |
| -------- | ------- | ------ |
| Cloud-Owner-Group | Mitglieder dieser Gruppe verfügen über Administratorberechtigungen für das vCenter der privaten Cloud. | [Cloud-Owner-Role](#cloud-owner-role) |
| Cloud-Global-Cluster-Admin-Group | Mitglieder dieser Gruppe verfügen über Administratorberechtigungen für den vCenter-Cluster der privaten Cloud. | [Cloud-Cluster-Admin-Role](#cloud-cluster-admin-role) |
| Cloud-Global-Storage-Admin-Group | Mitglieder dieser Gruppe können den Speicher im vCenter der privaten Cloud verwalten. | [Cloud-Storage-Admin-Role](#cloud-storage-admin-role) |
| Cloud-Global-Network-Admin-Group | Mitglieder dieser Gruppe können das Netzwerk-und verteilte Portgruppen für das vCenter der privaten Cloud verwalten. | [Cloud-Network-Admin-Role](#cloud-network-admin-role) |
| Cloud-Global-VM-Admin-Group | Mitglieder dieser Gruppe können virtuelle Computer für das vCenter der privaten Cloud verwalten. | [Cloud-VM-Admin-Role](#cloud-vm-admin-role) |

Um einzelnen Benutzern Berechtigungen zum Verwalten der privaten Cloud zu erteilen, erstellen Sie Benutzerkonten und fügen diese den entsprechenden Gruppen hinzu.

> [!CAUTION]
> Neue Benutzer müssen lediglich *Cloud-Owner-Group*, *Cloud-Global-Cluster-Admin-Group*, *Cloud-Global-Storage-Admin-Group*, *Cloud-Global-Network-Admin-Group* oder *Cloud-Global-VM-Admin-Group* hinzugefügt werden.  Benutzer, die der Gruppe *Administratoren* hinzugefügt wurden, werden automatisch entfernt.  Nur Dienstkonten dürfen der Gruppe *Administratoren* hinzugefügt werden, und Dienstkonten dürfen nicht für die Anmeldung bei der vSphere-Webbenutzeroberfläche verwendet werden.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Liste der vCenter-Berechtigungen für Standardrollen

### <a name="cloud-owner-role"></a>Cloud-Owner-Role

| **Kategorie** | **Berechtigung** |
|----------|-----------|
| **Warnungen** | Warnung bestätigen <br> Warnung erstellen <br> Warnungsaktion deaktivieren <br> Warnung ändern <br> Warnung entfernen <br> Warnungsstatus festlegen |
| **Berechtigungen** | Berechtigungen ändern |
| **Inhaltsbibliothek** | Bibliothekselement hinzufügen <br> Lokale Bibliothek erstellen <br> Abonnierte Bibliothek erstellen <br> Bibliothekselement löschen <br> Lokale Bibliothek löschen <br> Abonnierte Bibliothek löschen <br> Herunterladen von Dateien <br> Bibliothekselement entfernen <br> Abonnierte Bibliothek entfernen <br> Speicher importieren <br> Abonnementinformationen testen <br> Speicher lesen <br> Bibliothekselement synchronisieren <br> Abonnierte Bibliothek synchronisieren <br> Typintrospektion <br> Konfigurationseinstellungen aktualisieren <br> Dateien aktualisieren <br> Bibliothek aktualisieren <br> Bibliothekselement aktualisieren <br> Lokale Bibliothek aktualisieren <br> Abonnierte Bibliothek aktualisieren <br> Konfigurationseinstellungen anzeigen |
| **Kryptografische Vorgänge** | Datenträger hinzufügen <br> Klon <br> Entschlüsseln <br> Direct Access <br> Verschlüsseln <br> Neu verschlüsseln <br> KMS verwalten <br> Verwalten von Verschlüsselungsrichtlinien <br> Verwalten von Schlüsseln <br> Migrieren <br> Erneut verschlüsseln <br> VM registrieren <br> Host registrieren |
| **dvPort-Gruppe** | Erstellen <br> Löschen <br> Ändern <br> Richtlinienvorgänge <br> Bereichsvorgang |
| **Datenspeicher** | Speicherplatz zuweisen <br> Durchsuchen des Datenspeichers <br> Datenspeicher konfigurieren <br> Low-Level-Dateivorgänge <br> Datenspeicher verschieben <br> Datenspeicher entfernen <br> Datei entfernen <br> Datenspeicher umbenennen <br> Dateien virtueller Computer aktualisieren <br> Metadaten virtueller Computer aktualisieren |
| **ESX-Agent-Manager** | Config <br> Ändern <br> Sicht |
| **Erweiterung** | Erweiterung registrieren <br> Registrierung der Erweiterung aufheben <br> Erweiterung aktualisieren |
| **Externer Statistikanbieter**| Register <br> Unregister <br> Aktualisieren |
| **Ordner** | Ordner erstellen <br> Ordner löschen <br> Ordner verschieben <br> Ordner umbenennen |
| **Global** | Task abbrechen <br> Kapazitätsplanung <br> Diagnose <br> Methoden deaktivieren <br> Methoden aktivieren <br> Globales Tag <br> Health <br> Lizenzen <br> Ereignis protokollieren <br> Benutzerdefinierte Attribute definieren <br> Proxy <br> Skriptaktion <br> Dienst-Manager <br> Benutzerdefiniertes Attribut festlegen <br> Systemtag |
| **Integritätsupdateanbieter** | Register <br> Unregister <br> Aktualisieren |
| **Host > Konfiguration** | Speicherpartitionskonfiguration |
| **Host > Bestand** | Cluster ändern |
| **vSphere-Tagging** | vSphere-Tag zuweisen oder Zuweisung aufheben <br> vSphere-Tag erstellen <br> vSphere-Tagkategorie erstellen <br> vSphere-Tag löschen <br> vSphere-Tagkategorie löschen <br> vSphere-Tag bearbeiten <br> vSphere-Tagkategorie bearbeiten <br> Feld „UsedBy“ für Kategorie ändern <br> Feld „UsedBy“ für Tag ändern |
| **Network** | Netzwerk zuweisen <br> Konfigurieren <br> Netzwerk verschieben <br> Remove (Entfernen) |
| **Leistung** | Intervalle ändern |
| **Hostprofil** | Sicht |
| **Ressource** | Anwenden einer Empfehlung <br> vApp dem Ressourcenpool zuweisen <br> Virtuellen Computer dem Ressourcenpool zuweisen <br> Ressourcenpool erstellen <br> Ausgeschalteten virtuellen Computer migrieren <br> Eingeschalteten virtuellen Computer migrieren <br> Ressourcenpool ändern <br> Ressourcenpool entfernen <br> vMotion abfragen <br> Ressourcenpool entfernen <br> Ressourcenpool umbenennen |
| **Geplante Aufgabe** | Erstellen von Tasks <br> Aufgabe ändern <br> Aufgabe entfernen <br> Aufgabe ausführen |
| **Sitzungen** | Identität des Benutzers annehmen <br> `Message` <br> Sitzung überprüfen <br> Sitzungen anzeigen und beenden |
| **Datenspeichercluster** | Konfigurieren eines Datenspeicherclusters |
| **Profilgesteuerter Speicher** | Profilgesteuerte Speicheraktualisierung <br> Profilgesteuerte Speicheransicht |
| **Speicheransichten** | Dienst konfigurieren <br> Sicht |
| **Aufgaben** | Erstellen der Aufgabe <br> Aufgabe aktualisieren |
| **Übertragungsdienst**| Verwalten <br> Monitor |
| **vApp** | Virtuellen Computer hinzufügen <br> Ressourcenpool zuweisen <br> vApp zuweisen <br> Klon <br> Erstellen <br> Löschen <br> Exportieren <br> Importieren <br> Move <br> Ausschalten <br> Einschalten <br> Umbenennen <br> Angehalten <br> Unregister <br> OVF-Umgebung anzeigen <br> vApp-Anwendungskonfiguration <br> vApp-Instanzkonfiguration <br> vApp-managedBy-Konfiguration <br> vApp-Ressourcenkonfiguration |
| **VRMPolicy** | VRMPolicy abfragen <br> VRMPolicy aktualisieren |
| **Virtueller Computer > Konfiguration** | Vorhandenen Datenträger hinzufügen <br> Neuen Datenträger hinzufügen <br> Gerät hinzufügen oder entfernen <br> Erweitert <br> CPU-Anzahl ändern <br> Ressource ändern <br> ManagedBy konfigurieren <br> Datenträger-Änderungsnachverfolgung <br> Datenträgerlease <br> Verbindungseinstellungen anzeigen <br> Virtuellen Datenträger erweitern <br> USB-Gerät hosten <br> Arbeitsspeicher <br> Ändern von Geräteeinstellungen <br> Kompatibilität der Abfragefehlertoleranz <br> Nicht im Besitz befindliche Dateien abfragen <br> Unformatiertes Gerät <br> Aus Pfad neu laden <br> Datenträger entfernen <br> Umbenennen <br> Gastinformationen zurücksetzen <br> Anmerkung festlegen <br> Einstellungen <br> Platzierung der Auslagerungdatei <br> Übergeordnetes Forkelement umschalten <br> Virtuellen Computer entsperren <br> Kompatibilität des virtuellen Computers aktualisieren |
| **Virtueller Computer > Gastvorgänge** | Gastvorgang Aliasänderung <br> Gastvorgang Aliasabfrage <br> Gastvorgang Änderungen <br> Gastvorgang Programmausführung <br> Gastvorgang Abfragen |
| **Virtueller Computer > Interaktion** | Frage beantworten <br> Sicherungsvorgang für virtuellen Computer <br> CD-Medien konfigurieren <br> Floppy-Medien konfigurieren <br> Konsoleninteraktion <br> Screenshot erstellen <br> Alle Datenträger defragmentieren <br> Geräteverbindung <br> Drag &amp; Drop <br> Gastbetriebssystemverwaltung durch VIX-API <br> USB-HID-Scancodes einfügen <br> Anhalten oder Anhalten beenden <br> Lösch- oder Verkleinerungsvorgänge ausführen <br> Ausschalten <br> Einschalten <br> Sitzung auf virtuellem Computer aufzeichnen <br> Sitzung auf virtuellem Computer wiedergeben <br> Reset <br> Fehlertoleranz fortsetzen <br> Angehalten <br> Fehlertoleranz anhalten <br> Testfailover <br> Testneustart der sekundären VM <br> Fehlertoleranz deaktivieren <br> Fehlertoleranz aktivieren <br> VMware-Tools installieren |
| **Virtueller Computer > Bestand** | Aus vorhandenem Element erstellen <br> Neu erstellen <br> Move <br> Register <br> Remove (Entfernen) <br> Unregister |
| **Virtueller Computer > Bereitstellung** | Datenträgerzugriff zulassen <br> Dateizugriff zulassen <br> Schreibgeschützten Datenträgerzugriff zulassen <br> Download virtueller Computer zulassen <br> Upload von Dateien virtueller Computer zulassen <br> Vorlage klonen <br> Virtuellen Computer klonen <br> Vorlage aus virtuellem Computer erstellen <br> Anpassen <br> Bereitstellen der Vorlage <br> Als Vorlage markieren <br> Als virtuellen Computer markieren <br> Anpassungsspezifikation ändern <br> Datenträger höher stufen <br> Anpassungsspezifikationen lesen |
| **Virtueller Computer > Dienstkonfiguration** | Benachrichtigungen zulassen <br> Abrufen von globaler Ereignisbenachrichtigungen zulassen <br> Dienstkonfigurationen verwalten <br> Dienstkonfigurationen ändern <br> Dienstkonfigurationen abfragen <br> Dienstkonfigurationen lesen |
| **Virtueller Computer > Momentaufnahmeverwaltung** | Erstellen einer Momentaufnahme <br> Momentaufnahme entfernen <br> Momentaufnahme umbenennen <br> Auf Momentaufnahme zurücksetzen |
| **Virtueller Computer > vSphere-Replikation** | Konfigurieren der Replikation <br> Replikation verwalten <br> Überwachen der Replikation |
| **vService** | Abhängigkeit erstellen <br> Abhängigkeit löschen <br> Abhängigkeitskonfiguration neu konfigurieren <br> Abhängigkeit aktualisieren |

### <a name="cloud-cluster-admin-role"></a>Cloud-Cluster-Admin-Role

| **Kategorie** | **Berechtigung** |
|----------|-----------|
| **Datenspeicher** | Speicherplatz zuweisen <br> Durchsuchen des Datenspeichers <br> Datenspeicher konfigurieren <br> Low-Level-Dateivorgänge <br> Datenspeicher entfernen <br> Datenspeicher umbenennen <br> Dateien virtueller Computer aktualisieren <br> Metadaten virtueller Computer aktualisieren |
| **Ordner** | Ordner erstellen <br> Ordner löschen <br> Ordner verschieben <br> Ordner umbenennen |
| **Host > Konfiguration**  | Speicherpartitionskonfiguration |
| **vSphere-Tagging** | vSphere-Tag zuweisen oder Zuweisung aufheben <br> vSphere-Tag erstellen <br> vSphere-Tagkategorie erstellen <br> vSphere-Tag löschen <br> vSphere-Tagkategorie löschen <br> vSphere-Tag bearbeiten <br> vSphere-Tagkategorie bearbeiten <br> Feld „UsedBy“ für Kategorie ändern <br> Feld „UsedBy“ für Tag ändern |
| **Network** | Netzwerk zuweisen |
| **Ressource** | Anwenden einer Empfehlung <br> vApp dem Ressourcenpool zuweisen <br> Virtuellen Computer dem Ressourcenpool zuweisen <br> Ressourcenpool erstellen <br> Ausgeschalteten virtuellen Computer migrieren <br> Eingeschalteten virtuellen Computer migrieren <br> Ressourcenpool ändern <br> Ressourcenpool entfernen <br> vMotion abfragen <br> Ressourcenpool entfernen <br> Ressourcenpool umbenennen |
| **vApp** | Virtuellen Computer hinzufügen <br> Ressourcenpool zuweisen <br> vApp zuweisen <br> Klon <br> Erstellen <br> Löschen <br> Exportieren <br> Importieren <br> Move <br> Ausschalten <br> Einschalten <br> Umbenennen <br> Angehalten <br> Unregister <br> OVF-Umgebung anzeigen <br> vApp-Anwendungskonfiguration <br> vApp-Instanzkonfiguration <br> vApp-managedBy-Konfiguration <br> vApp-Ressourcenkonfiguration |
| **VRMPolicy** | VRMPolicy abfragen <br> VRMPolicy aktualisieren |
| **Virtueller Computer > Konfiguration** | Vorhandenen Datenträger hinzufügen <br> Neuen Datenträger hinzufügen <br> Gerät hinzufügen oder entfernen <br> Erweitert <br> CPU-Anzahl ändern <br> Ressource ändern <br> ManagedBy konfigurieren <br> Datenträger-Änderungsnachverfolgung <br> Datenträgerlease <br> Verbindungseinstellungen anzeigen <br> Virtuellen Datenträger erweitern <br> USB-Gerät hosten <br> Arbeitsspeicher <br> Ändern von Geräteeinstellungen <br> Kompatibilität der Abfragefehlertoleranz <br> Nicht im Besitz befindliche Dateien abfragen <br> Unformatiertes Gerät <br> Aus Pfad neu laden <br> Datenträger entfernen <br> Umbenennen <br> Gastinformationen zurücksetzen <br> Anmerkung festlegen <br> Einstellungen <br> Platzierung der Auslagerungdatei <br> Übergeordnetes Forkelement umschalten <br> Virtuellen Computer entsperren <br> Kompatibilität des virtuellen Computers aktualisieren |
| **Virtueller Computer > Gastvorgänge** | Gastvorgang Aliasänderung <br> Gastvorgang Aliasabfrage <br> Gastvorgang Änderungen <br> Gastvorgang Programmausführung <br> Gastvorgang Abfragen |
| **Virtueller Computer > Interaktion** | Frage beantworten <br> Sicherungsvorgang für virtuellen Computer <br> CD-Medien konfigurieren <br> Floppy-Medien konfigurieren <br> Konsoleninteraktion <br> Screenshot erstellen <br> Alle Datenträger defragmentieren <br> Geräteverbindung <br> Drag &amp; Drop <br> Gastbetriebssystemverwaltung durch VIX-API <br> USB-HID-Scancodes einfügen <br> Anhalten oder Anhalten beenden <br> Lösch- oder Verkleinerungsvorgänge ausführen <br> Ausschalten <br> Einschalten <br> Sitzung auf virtuellem Computer aufzeichnen <br> Sitzung auf virtuellem Computer wiedergeben <br> Reset <br> Fehlertoleranz fortsetzen <br> Angehalten <br> Fehlertoleranz anhalten <br> Testfailover <br> Testneustart der sekundären VM <br> Fehlertoleranz deaktivieren <br> Fehlertoleranz aktivieren <br> VMware-Tools installieren
| **Virtueller Computer > Bestand** | Aus vorhandenem Element erstellen <br> Neu erstellen <br> Move <br> Register <br> Remove (Entfernen) <br> Unregister |
| **Virtueller Computer > Bereitstellung** | Datenträgerzugriff zulassen <br> Dateizugriff zulassen <br> Schreibgeschützten Datenträgerzugriff zulassen <br> Download virtueller Computer zulassen <br> Upload von Dateien virtueller Computer zulassen <br> Vorlage klonen <br> Virtuellen Computer klonen <br> Vorlage aus virtuellem Computer erstellen <br> Anpassen <br> Bereitstellen der Vorlage <br> Als Vorlage markieren <br> Als virtuellen Computer markieren <br> Anpassungsspezifikation ändern <br> Datenträger höher stufen  <br> Anpassungsspezifikationen lesen |
| **Virtueller Computer > Dienstkonfiguration** | Benachrichtigungen zulassen <br> Abrufen von globaler Ereignisbenachrichtigungen zulassen <br> Dienstkonfigurationen verwalten <br> Dienstkonfigurationen ändern <br> Dienstkonfigurationen abfragen <br> Dienstkonfigurationen lesen
| **Virtueller Computer > Momentaufnahmeverwaltung** | Erstellen einer Momentaufnahme <br> Momentaufnahme entfernen <br> Momentaufnahme umbenennen <br> Auf Momentaufnahme zurücksetzen |
| **Virtueller Computer > vSphere-Replikation** | Konfigurieren der Replikation <br> Replikation verwalten <br> Überwachen der Replikation |
| **vService** | Abhängigkeit erstellen <br> Abhängigkeit löschen <br> Abhängigkeitskonfiguration neu konfigurieren <br> Abhängigkeit aktualisieren |

### <a name="cloud-storage-admin-role"></a>Cloud-Storage-Admin-Role

| **Kategorie** | **Berechtigung** |
|----------|-----------|
| **Datenspeicher** | Speicherplatz zuweisen <br> Durchsuchen des Datenspeichers <br> Datenspeicher konfigurieren <br> Low-Level-Dateivorgänge <br> Datenspeicher entfernen <br> Datenspeicher umbenennen <br> Dateien virtueller Computer aktualisieren <br> Metadaten virtueller Computer aktualisieren |
| **Host > Konfiguration** | Speicherpartitionskonfiguration |
| **Datenspeichercluster** | Konfigurieren eines Datenspeicherclusters |
| **Profilgesteuerter Speicher** | Profilgesteuerte Speicheraktualisierung <br> Profilgesteuerte Speicheransicht |
| **Speicheransichten** | Dienst konfigurieren <br> Sicht |

### <a name="cloud-network-admin-role"></a>Cloud-Network-Admin-Role

| **Kategorie** | **Berechtigung** |
|----------|-----------|
| **dvPort-Gruppe** | Erstellen <br> Löschen <br> Ändern <br> Richtlinienvorgänge <br> Bereichsvorgang |
| **Network** | Netzwerk zuweisen <br> Konfigurieren <br> Netzwerk verschieben <br> Remove (Entfernen) |
| **Virtueller Computer > Konfiguration** | Ändern von Geräteeinstellungen |

### <a name="cloud-vm-admin-role"></a>Cloud-VM-Admin-Role

| **Kategorie** | **Berechtigung** |
|----------|-----------|
| **Datenspeicher** | Speicherplatz zuweisen <br> Durchsuchen des Datenspeichers |
| **Network** | Netzwerk zuweisen |
| **Ressource** | Virtuellen Computer dem Ressourcenpool zuweisen <br> Ausgeschalteten virtuellen Computer migrieren <br> Eingeschalteten virtuellen Computer migrieren
| **vApp** | Exportieren <br> Importieren |
| **Virtueller Computer > Konfiguration** | Vorhandenen Datenträger hinzufügen <br> Neuen Datenträger hinzufügen <br> Gerät hinzufügen oder entfernen <br> Erweitert <br> CPU-Anzahl ändern <br> Ressource ändern <br> ManagedBy konfigurieren <br> Datenträger-Änderungsnachverfolgung <br> Datenträgerlease <br> Verbindungseinstellungen anzeigen <br> Virtuellen Datenträger erweitern <br> USB-Gerät hosten <br> Arbeitsspeicher <br> Ändern von Geräteeinstellungen <br> Kompatibilität der Abfragefehlertoleranz <br> Nicht im Besitz befindliche Dateien abfragen <br> Unformatiertes Gerät <br> Aus Pfad neu laden <br> Datenträger entfernen <br> Umbenennen <br> Gastinformationen zurücksetzen <br> Anmerkung festlegen <br> Einstellungen <br> Platzierung der Auslagerungdatei <br> Übergeordnetes Forkelement umschalten <br> Virtuellen Computer entsperren <br> Kompatibilität des virtuellen Computers aktualisieren |
| **Virtueller Computer > Gastvorgänge** | Gastvorgang Aliasänderung <br> Gastvorgang Aliasabfrage <br> Gastvorgang Änderungen <br> Gastvorgang Programmausführung <br> Gastvorgang Abfragen    |
| **Virtueller Computer > Interaktion** | Frage beantworten <br> Sicherungsvorgang für virtuellen Computer <br> CD-Medien konfigurieren <br> Floppy-Medien konfigurieren <br> Konsoleninteraktion <br> Screenshot erstellen <br> Alle Datenträger defragmentieren <br> Geräteverbindung <br> Drag &amp; Drop <br> Gastbetriebssystemverwaltung durch VIX-API <br> USB-HID-Scancodes einfügen <br> Anhalten oder Anhalten beenden <br> Lösch- oder Verkleinerungsvorgänge ausführen <br> Ausschalten <br> Einschalten <br> Sitzung auf virtuellem Computer aufzeichnen <br> Sitzung auf virtuellem Computer wiedergeben <br> Reset <br> Fehlertoleranz fortsetzen <br> Angehalten <br> Fehlertoleranz anhalten <br> Testfailover <br> Testneustart der sekundären VM <br> Fehlertoleranz deaktivieren <br> Fehlertoleranz aktivieren <br> VMware-Tools installieren |
| **Virtueller Computer > Bestand** | Aus vorhandenem Element erstellen <br> Neu erstellen <br> Move <br> Register <br> Remove (Entfernen) <br> Unregister |
| **Virtueller Computer > Bereitstellung** | Datenträgerzugriff zulassen <br> Dateizugriff zulassen <br> Schreibgeschützten Datenträgerzugriff zulassen <br> Download virtueller Computer zulassen <br> Upload von Dateien virtueller Computer zulassen <br> Vorlage klonen <br> Virtuellen Computer klonen <br> Vorlage aus virtuellem Computer erstellen <br> Anpassen <br> Bereitstellen der Vorlage <br> Als Vorlage markieren <br> Als virtuellen Computer markieren <br> Anpassungsspezifikation ändern <br> Datenträger höher stufen <br> Anpassungsspezifikationen lesen |
| **Virtueller Computer > Dienstkonfiguration** | Benachrichtigungen zulassen <br> Abrufen von globaler Ereignisbenachrichtigungen zulassen <br> Dienstkonfigurationen verwalten <br> Dienstkonfigurationen ändern <br> Dienstkonfigurationen abfragen <br> Dienstkonfigurationen lesen
| **Virtueller Computer > Momentaufnahmeverwaltung** | Erstellen einer Momentaufnahme <br> Momentaufnahme entfernen <br> Momentaufnahme umbenennen <br> Auf Momentaufnahme zurücksetzen |
| **Virtueller Computer > vSphere-Replikation** | Konfigurieren der Replikation <br> Replikation verwalten <br> Überwachen der Replikation |
| **vService** | Abhängigkeit erstellen <br> Abhängigkeit löschen <br> Abhängigkeitskonfiguration neu konfigurieren <br> Abhängigkeit aktualisieren |
