---
title: Konzepte – Identität und Zugriff
description: Informationen zu den Identitäts- und Zugriffskonzepten von Azure VMware Solution
ms.topic: conceptual
ms.date: 07/29/2021
ms.openlocfilehash: e09a69ae1e3a9e8cba5d1027af1fc3ad57c73446
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/31/2021
ms.locfileid: "123252538"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Identitätskonzepte von Azure VMware Solution

Private Azure VMware Solution-Clouds werden mit vCenter Server und NSX-T Manager bereitgestellt. vCenter dient der Verwaltung von VM-Workloads und NSX-T Manager der Verwaltung und Erweiterung der privaten Cloud. Die CloudAdmin-Rolle wird für vCenter und eingeschränkte Administratorrechte für NSX-T Manager verwendet. 

## <a name="vcenter-access-and-identity"></a>vCenter – Zugriff und Identität

[!INCLUDE [vcenter-access-identity-description](includes/vcenter-access-identity-description.md)]

> [!IMPORTANT]
> Azure VMware Solution bietet benutzerdefinierte Rollen zwar für vCenter, jedoch derzeit nicht für das Azure VMware Solution-Portal. Weitere Informationen finden Sie im Abschnitt [Erstellen von benutzerdefinierten Rollen in vCenter](#create-custom-roles-on-vcenter) weiter unten in diesem Artikel. 

### <a name="view-the-vcenter-privileges"></a>Anzeigen der vCenter-Berechtigungen

Sie können die Berechtigungen anzeigen, die der Azure VMware Solution-Rolle CloudAdmin in vCenter Ihrer privaten Azure VMware Solution-Cloud erteilt wurden.

1. Melden Sie sich beim vSphere-Client an, und wechseln Sie zu **Menü** > **Verwaltung**.

1. Wählen Sie unter **Zugriffssteuerung** die Option **Rollen** aus.

1. Wählen Sie in der Liste der Rollen den Eintrag **CloudAdmin** aus, und wählen Sie dann **Berechtigungen** aus. 

   :::image type="content" source="media/concepts/role-based-access-control-cloudadmin-privileges.png" alt-text="Screenshot: Rollen und Berechtigungen für CloudAdmin im vSphere-Client":::

Die Azure VMware Solution-Rolle CloudAdmin verfügt über die folgenden Berechtigungen für vCenter. Weitere Informationen finden Sie in der [VMware-Produktdokumentation](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html).

| Berechtigung | BESCHREIBUNG |
| --------- | ----------- |
| **Warnungen** | Warnung bestätigen<br />Warnung erstellen<br />Warnungsaktion deaktivieren<br />Warnung ändern<br />Warnung entfernen<br />Warnungsstatus festlegen |
| **Inhaltsbibliothek** | Bibliothekselement hinzufügen<br />Abonnement für eine veröffentlichte Bibliothek erstellen<br />Lokale Bibliothek erstellen<br />Abonnierte Bibliothek erstellen<br />Bibliothekselement löschen<br />Lokale Bibliothek löschen<br />Abonnierte Bibliothek löschen<br />Abonnement für eine veröffentlichte Bibliothek löschen<br />Herunterladen von Dateien<br />Bibliothekselemente entfernen<br />Abonnierte Bibliothek entfernen<br />Speicher importieren<br />Abonnementinformationen testen<br />Bibliothekselement für seine Abonnenten veröffentlichen<br />Bibliothek für ihre Abonnenten veröffentlichen<br />Speicher lesen<br />Bibliothekselement synchronisieren<br />Abonnierte Bibliothek synchronisieren<br />Typintrospektion<br />Konfigurationseinstellungen aktualisieren<br />Dateien aktualisieren<br />Bibliothek aktualisieren<br />Bibliothekselement aktualisieren<br />Lokale Bibliothek aktualisieren<br />Abonnierte Bibliothek aktualisieren<br />Abonnement einer veröffentlichten Bibliothek aktualisieren<br />Konfigurationseinstellungen anzeigen |
| **Kryptografische Vorgänge** | Direktzugriff |
| **Datenspeicher** | Speicherplatz zuweisen<br />Durchsuchen des Datenspeichers<br />Datenspeicher konfigurieren<br />Low-Level-Dateivorgänge<br />Dateien entfernen<br />Metadaten virtueller Computer aktualisieren |
| **Ordner** | Ordner erstellen<br />Ordner löschen<br />Ordner verschieben<br />Ordner umbenennen |
| **Global** | Task abbrechen<br />Globales Tag<br />Health<br />Ereignis protokollieren<br />Benutzerdefinierte Attribute definieren<br />Dienst-Manager<br />Benutzerdefiniertes Attribut festlegen<br />Systemtag |
| **Host** | vSphere-Replikation<br />&#160;&#160;&#160;&#160;Replikation verwalten |
| **Network** | Netzwerk zuweisen |
| **Berechtigungen** | Berechtigungen ändern<br />Rolle ändern |
| **Profil** | Profilgesteuerte Speicheransicht |
| **Ressource** | Anwenden einer Empfehlung<br />vApp dem Ressourcenpool zuweisen<br />Virtuellen Computer dem Ressourcenpool zuweisen<br />Ressourcenpool erstellen<br />Ausgeschalteten virtuellen Computer migrieren<br />Eingeschalteten virtuellen Computer migrieren<br />Ressourcenpool ändern<br />Ressourcenpool entfernen<br />vMotion abfragen<br />Ressourcenpool entfernen<br />Ressourcenpool umbenennen |
| **Geplante Aufgabe** | Erstellen der Aufgabe<br />Aufgabe ändern<br />Aufgabe entfernen<br />Aufgabe ausführen |
| **Sitzungen** | `Message`<br />Sitzung überprüfen |
| **Speicheransicht** | Sicht |
| **vApp** | Virtuellen Computer hinzufügen<br />Ressourcenpool zuweisen<br />vApp zuweisen<br />Klon<br />Erstellen<br />Löschen<br />Exportieren<br />Importieren<br />Move<br />Ausschalten<br />Einschalten<br />Umbenennen<br />Angehalten<br />Unregister<br />OVF-Umgebung anzeigen<br />vApp-Anwendungskonfiguration<br />vApp-Instanzkonfiguration<br />vApp-managedBy-Konfiguration<br />vApp-Ressourcenkonfiguration |
| **Virtueller Computer** | Konfiguration ändern<br />&#160;&#160;&#160;&#160;Leasedauer des Datenträgers abrufen<br />&#160;&#160;&#160;&#160;Vorhandenen Datenträger hinzufügen<br />&#160;&#160;&#160;&#160;Neuen Datenträger hinzufügen<br />&#160;&#160;&#160;&#160;Gerät hinzufügen oder entfernen<br />&#160;&#160;&#160;&#160;Erweiterte Konfiguration<br />&#160;&#160;&#160;&#160;CPU-Anzahl ändern<br />&#160;&#160;&#160;&#160;Arbeitsspeicher ändern<br />&#160;&#160;&#160;&#160;Einstellungen ändern<br />&#160;&#160;&#160;&#160;Platzierung der Auslagerungsdatei ändern<br />&#160;&#160;&#160;&#160;Ressource ändern<br />&#160;&#160;&#160;&#160;Host-USB-Gerät konfigurieren<br />&#160;&#160;&#160;&#160;Unformatiertes Gerät konfigurieren<br />&#160;&#160;&#160;&#160;ManagedBy konfigurieren<br />&#160;&#160;&#160;&#160;Verbindungseinstellungen anzeigen<br />&#160;&#160;&#160;&#160;Virtuellen Datenträger erweitern<br />&#160;&#160;&#160;&#160;Geräteeinstellungen ändern<br />&#160;&#160;&#160;&#160;Kompatibilität der Fehlertoleranz abfragen<br />&#160;&#160;&#160;&#160;Nicht im Besitz befindliche Dateien abfragen<br />&#160;&#160;&#160;&#160;Aus Pfaden neu laden<br />&#160;&#160;&#160;&#160;Datenträger entfernen<br />&#160;&#160;&#160;&#160;Umbenennen<br />&#160;&#160;&#160;&#160;Gastinformationen zurücksetzen<br />&#160;&#160;&#160;&#160;Anmerkung festlegen<br />&#160;&#160;&#160;&#160;Änderungsnachverfolgung für Datenträger ein-/ausschalten<br />&#160;&#160;&#160;&#160;Übergeordneten Fork ein-/ausschalten<br />&#160;&#160;&#160;&#160;VM-Kompatibilität aktualisieren<br />Inventar bearbeiten<br />&#160;&#160;&#160;&#160;Aus vorhandenem Element erstellen<br />&#160;&#160;&#160;&#160;Neues Element erstellen<br />&#160;&#160;&#160;&#160;Verschieben<br />&#160;&#160;&#160;&#160;Registrieren<br />&#160;&#160;&#160;&#160;Entfernen<br />&#160;&#160;&#160;&#160;Registrierung aufheben<br />Gastvorgänge<br />&#160;&#160;&#160;&#160;Gastvorgang Aliasänderung<br />&#160;&#160;&#160;&#160;Gastvorgang Aliasabfrage<br />&#160;&#160;&#160;&#160;Gastvorgang Änderungen<br />&#160;&#160;&#160;&#160;Gastvorgang Programmausführung<br />&#160;&#160;&#160;&#160;Gastvorgang Abfragen<br />Interaktion<br />&#160;&#160;&#160;&#160;Frage beantworten<br />&#160;&#160;&#160;&#160;Sicherungsvorgang für VM<br />&#160;&#160;&#160;&#160;CD-Medien konfigurieren<br />&#160;&#160;&#160;&#160;Disketten konfigurieren<br />&#160;&#160;&#160;&#160;Geräte verbinden<br />&#160;&#160;&#160;&#160;Konsoleninteraktion<br />&#160;&#160;&#160;&#160;Screenshot erstellen<br />&#160;&#160;&#160;&#160;Alle Datenträger defragmentieren<br />&#160;&#160;&#160;&#160;Drag & Drop<br />&#160;&#160;&#160;&#160;Gastbetriebssystemverwaltung durch VIX-API<br />&#160;&#160;&#160;&#160;USB-HID-Scancodes einfügen<br />&#160;&#160;&#160;&#160;VMware-Tools installieren<br />&#160;&#160;&#160;&#160;Anhalten oder Anhalten aufheben<br />&#160;&#160;&#160;&#160;Lösch- oder Verkleinerungsvorgänge ausführen<br />&#160;&#160;&#160;&#160;Ausschalten<br />&#160;&#160;&#160;&#160;Einschalten<br />&#160;&#160;&#160;&#160;Sitzung auf VM aufzeichnen<br />&#160;&#160;&#160;&#160;Sitzung auf VM wiedergeben<br />&#160;&#160;&#160;&#160;Anhalten<br />&#160;&#160;&#160;&#160;Fehlertoleranz anhalten<br />&#160;&#160;&#160;&#160;Failover testen<br />&#160;&#160;&#160;&#160;Neustart der sekundären VM testen<br />&#160;&#160;&#160;&#160;Fehlertoleranz deaktivieren<br />&#160;&#160;&#160;&#160;Fehlertoleranz aktivieren<br />Bereitstellung<br />&#160;&#160;&#160;&#160;Datenträgerzugriff zulassen<br />&#160;&#160;&#160;&#160;Dateizugriff zulassen<br />&#160;&#160;&#160;&#160;Schreibgeschützten Datenträgerzugriff zulassen<br />&#160;&#160;&#160;&#160;VM-Download zulassen<br />&#160;&#160;&#160;&#160;Vorlage klonen<br />&#160;&#160;&#160;&#160;VM klonen<br />&#160;&#160;&#160;&#160;Vorlage aus VM erstellen<br />&#160;&#160;&#160;&#160;Gast anpassen<br />&#160;&#160;&#160;&#160;Vorlage bereitstellen<br />&#160;&#160;&#160;&#160;Als Vorlage markieren<br />&#160;&#160;&#160;&#160;Anpassungsspezifikation ändern<br />&#160;&#160;&#160;&#160;Datenträger höherstufen<br />&#160;&#160;&#160;&#160;Anpassungsspezifikationen lesen<br />Dienstkonfiguration<br />&#160;&#160;&#160;&#160;Benachrichtigungen zulassen<br />&#160;&#160;&#160;&#160;Abrufen globaler Ereignisbenachrichtigungen zulassen<br />&#160;&#160;&#160;&#160;Dienstkonfiguration verwalten<br />&#160;&#160;&#160;&#160;Dienstkonfiguration ändern<br />&#160;&#160;&#160;&#160;Dienstkonfigurationen abfragen<br />&#160;&#160;&#160;&#160;Dienstkonfiguration lesen<br />Momentaufnahmenverwaltung<br />&#160;&#160;&#160;&#160;Momentaufnahme erstellen<br />&#160;&#160;&#160;&#160;Momentaufnahme entfernen<br />&#160;&#160;&#160;&#160;Momentaufnahme umbenennen<br />&#160;&#160;&#160;&#160;Momentaufnahme wiederherstellen<br />vSphere-Replikation<br />&#160;&#160;&#160;&#160;Replikation konfigurieren<br />&#160;&#160;&#160;&#160;Replikation verwalten<br />&#160;&#160;&#160;&#160;Replikation überwachen |
| **vService** | Abhängigkeit erstellen<br />Abhängigkeit löschen<br />Abhängigkeitskonfiguration neu konfigurieren<br />Abhängigkeit aktualisieren |
| **vSphere-Tagging** | vSphere-Tag zuweisen und Zuweisung aufheben<br />vSphere-Tag erstellen<br />vSphere-Tagkategorie erstellen<br />vSphere-Tag löschen<br />vSphere-Tagkategorie löschen<br />vSphere-Tag bearbeiten<br />vSphere-Tagkategorie bearbeiten<br />Feld „UsedBy“ für Kategorie ändern<br />Feld „UsedBy“ für Tag ändern |

### <a name="create-custom-roles-on-vcenter"></a>Erstellen von benutzerdefinierten Rollen in vCenter

Azure VMware Solution unterstützt die Verwendung benutzerdefinierter Rollen mit den gleichen oder geringeren Berechtigung wie die Cloudadministratorrolle. 

Mithilfe der Cloudadministratorrolle erstellen Sie benutzerdefinierte Rolle, ändern und löschen sie, wenn deren Berechtigungen kleiner oder gleich ihrer aktuellen Rolle sind. Sie können auch Rollen mit Berechtigungen erstellen, die größer als die eines Cloudadministrators sind, aber Sie können diese Rolle dann keinen Benutzern oder Gruppen zuweisen und die Rolle auch nicht löschen.

Klonen Sie die CloudAdmin-Rolle als Grundlage zum Erstellen neuer benutzerdefinierter Rollen, um zu verhindern, dass Rollen erstellt werden, die nicht zugewiesen oder gelöscht werden können.

#### <a name="create-a-custom-role"></a>Erstellen einer benutzerdefinierten Rolle
1. Melden Sie sich bei vCenter als „cloudadmin\@vsphere.local“ oder als ein Benutzer mit der CloudAdmin-Rolle an.

1. Navigieren Sie zum Konfigurationsabschnitt **Rollen**, und wählen Sie **Menü** > **Verwaltung** > **Zugriffssteuerung** > **Rollen** aus.

1. Wählen Sie die Rolle **Cloudadministrator** und dann das Symbol **Rollenaktion klonen** aus.

   >[!NOTE] 
   >Klonen Sie die **Administratorrolle** nicht, da Sie sie nicht verwenden können. Außerdem kann die erstellte benutzerdefinierte Rolle nicht von cloudadmin\@vsphere.local gelöscht werden.

1. Geben Sie den gewünschten Namen für die geklonte Rolle an.

1. Fügen Sie Berechtigungen für die Rolle hinzu, oder entfernen Sie Berechtigungen, und wählen Sie dann **OK** aus. Die geklonte Rolle wird nun in der Liste **Rollen** angezeigt.


#### <a name="apply-a-custom-role"></a>Anwenden einer benutzerdefinierten Rolle

1. Navigieren Sie zu dem Objekt, das die hinzugefügte Berechtigung erfordert. Wenn Sie die Berechtigung z. B. auf einen Ordner anwenden möchten, navigieren Sie zu **Menü** > **VMs und Vorlagen** > **Ordnername**.

1. Klicken Sie mit der rechten Maustaste auf das Objekt, und wählen Sie **Berechtigung hinzufügen** aus.

1. Wählen Sie in der Dropdownliste **Benutzer** die Identitätsquelle aus, in der die Gruppe oder der Benutzer enthalten ist.

1. Suchen Sie nach dem Auswählen der Identitätsquelle im Abschnitt **Benutzer** nach dem Benutzer oder der Gruppe. 

1. Wählen Sie die Rolle aus, die Sie auf den Benutzer oder die Gruppe anwenden möchten.

1. Aktivieren Sie bei Bedarf die Option **In untergeordnete Elemente propagieren**, und wählen Sie **OK** aus. Die hinzugefügte Berechtigung wird im Abschnitt **Berechtigungen** angezeigt.

## <a name="nsx-t-manager-access-and-identity"></a>NSX-T Manager – Zugriff und Identität

>[!NOTE]
>NSX-T [!INCLUDE [nsxt-version](includes/nsxt-version.md)] wird derzeit von allen neuen privaten Clouds unterstützt.

Für den Zugriff auf NSX-T Manager verwenden Sie das Konto *Administrator*. Es verfügt über umfassende Berechtigungen und ermöglicht Ihnen, Tier-1-Gateways (T1), Segmente (logische Switches) und alle Dienste zu erstellen und zu verwalten. Durch die Berechtigungen erhalten Sie zudem Zugriff auf das NSX-T-Tier-0-Gateway (T0). Eine Änderung am T0-Gateway kann dazu führen, dass die Netzwerkleistung beeinträchtigt wird oder nicht auf die private Cloud zugegriffen werden kann. Erstellen Sie im Azure-Portal eine Supportanfrage, um Änderungen an Ihrem NSX-T-T0-Gateway anzufordern.

 
## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit den Zugriffs- und Identitätskonzepten von Azure VMware Solution vertraut gemacht haben, informieren Sie sich über die folgenden Themen:

- [Konfigurieren der externen Identitätsquelle für vCenter](configure-identity-source-vcenter.md)

- [So aktivieren Sie die Azure VMware Solution-Ressource](deploy-azure-vmware-solution.md#register-the-microsoftavs-resource-provider)

- [Details der einzelnen Berechtigungen](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html)

- [Wie die Azure VMware Solution private Clouds überwacht und repariert](./concepts-private-clouds-clusters.md#host-monitoring-and-remediation)



<!-- LINKS - external-->
[VMware product documentation]: https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-private-clouds-clusters#host-maintenance-and-lifecycle-management
