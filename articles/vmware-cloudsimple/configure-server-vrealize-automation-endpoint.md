---
title: 'Azure VMware Solution by CloudSimple: Einrichten von vCenter in privater Cloud für vRealize Automation'
description: Erfahren Sie, wie Sie einen VMware vCenter-Server in Ihrer privaten CloudSimple-Cloud als Endpunkt für VMware vRealize Automation einrichten.
author: suzizuber
ms.author: v-szuber
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c005ab9d957ba72e42dcbb7f41f65b97a486df65
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322700"
---
# <a name="set-up-vcenter-on-your-private-cloud-for-vmware-vrealize-automation"></a>Einrichten von vCenter in Ihrer privaten Cloud für VMware vRealize Automation

Sie können einen VMware vCenter-Server in Ihrer privaten CloudSimple-Cloud als Endpunkt für VMware vRealize Automation einrichten.

## <a name="before-you-begin"></a>Voraussetzungen

Führen Sie diese Aufgaben aus, bevor Sie den vCenter-Server konfigurieren:

* Konfigurieren Sie eine [Site-to-Site-VPN-Verbindung](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) zwischen Ihrer lokalen Umgebung und privaten Cloud.
* [Konfigurieren Sie die DNS-Weiterleitung lokaler DNS-Anforderungen](on-premises-dns-setup.md) an die DNS-Server Ihrer privaten Cloud.
* Senden Sie eine [Supportanfrage](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), um einen IaaS-Administratorbenutzer für vRealize Automation mit den in der folgenden Tabelle aufgeführten Berechtigungen zu erstellen.

| Attributwert | Berechtigung |
------------ | ------------- |  
| Datenspeicher |  Speicherplatz zuordnen <br> Datenspeicher durchsuchen |
| Datenspeichercluster | Datenspeichercluster konfigurieren |
| Ordner | Erstellen eines Ordners <br>Ordner löschen |
| Global |  Benutzerdefinierte Attribute verwalten<br>Benutzerdefiniertes Attribut festlegen |
| Netzwerk | Netzwerk zuweisen |
| Berechtigungen | Berechtigungen ändern |
| Resource | VM zu Ressourcenpool zuweisen<br>Ausgeschaltete VM migrieren<br>Eingeschaltete VM migrieren |
| VM-Bestand |  Aus vorhandenem Element erstellen<br>Create New<br>Move<br>Remove (Entfernen) | 
| Interaktion mit VM |  CD-Medien konfigurieren<br>Konsoleninteraktion<br>Geräteverbindung<br>Ausschalten<br>Einschalten<br>Reset<br>Angehalten<br>Tools installieren | 
| VM-Konfiguration |  Vorhandenen Datenträger hinzufügen<br>Neuen Datenträger hinzufügen<br>Hinzufügen oder entfernen<br>Datenträger entfernen<br>Erweitert<br>CPU-Anzahl ändern<br>Ressource ändern<br>Virtuellen Datenträger erweitern<br>Änderungsnachverfolgung für Datenträger<br>Arbeitsspeicher<br>Geräteeinstellungen ändern<br>Umbenennen<br>Anmerkung festlegen (ab Version 5.0)<br>Einstellungen<br>Platzierung der Auslagerungsdatei |
| Bereitstellung |  Anpassen<br>Vorlage klonen<br>VM klonen<br>Vorlage bereitstellen<br>Anpassungsspezifikationen lesen |
| VM-Status | Momentaufnahme erstellen<br>Momentaufnahme entfernen<br>Auf Momentaufnahme zurücksetzen |

## <a name="install-vrealize-automation-in-your-on-premises-environment"></a>Installieren von vRealize Automation in Ihrer lokalen Umgebung

1. Melden Sie sich bei der IaaS-Serverappliance für vRealize Automation mit dem IaaS-Administratorkonto an, das der Support von CloudSimple für Sie erstellt hat.
2. Stellen Sie einen vSphere-Agent für den vRealize Automation-Endpunkt bereit.
    1. Rufen Sie https://*vra-url*:5480/installer auf, wobei *vra-url* die URL ist, die Sie für den Zugriff auf die vRealize Automation-Verwaltungsoberfläche verwenden.
    2. Klicken Sie auf **IaaS Installer**, um das Installationsprogramm herunterzuladen.<br>
    Die Namenskonvention für die Installationsprogrammdatei lautet setup_ *vra-url*@5480.exe.
    3. Führen Sie den Installer aus. Klicken Sie auf dem Bildschirm Willkommen auf **Weiter**.
    4. Akzeptieren Sie die Lizenzbedingungen, und klicken Sie auf **Next** (Weiter).
    5. Geben Sie die Anmeldeinformationen an, klicken Sie auf **Accept Certificate** (Zertifikat annehmen), und klicken Sie dann auf **Next** (Weiter).
    ![vRA-Anmeldeinformationen](media/configure-vra-endpoint-login.png)
    6. Wählen Sie **Custom Install** (Benutzerdefinierte Installation) und **Proxy Agents** (Proxy-Agents) aus, und klicken Sie auf **Next** (Weiter).
    ![vRA-Installationstyp](media/configure-vra-endpoint-install-type.png)
    7. Geben Sie die Anmeldeinformationen für den IaaS-Server ein, und klicken Sie auf **Next** (Weiter). Wenn Sie Active Directory verwenden, geben Sie den Benutzernamen im Format **Domäne\Benutzer** ein. Verwenden Sie andernfalls das Format **user@domain** .
    ![vRA-Anmeldeinformation](media/configure-vra-endpoint-account.png)
    8. Geben Sie für die Proxyeinstellungen **vSphere** als **Agent type** (Agent-Typ) ein. Geben Sie einen Namen für den Agent ein.
    9. Geben Sie in die Felder **Manager Service Host** (Manager: Diensthost) und **Model Manager Web Service Host** (Modell-Manager: Webdiensthost) den FQDN des IaaS-Servers ein. Klicken Sie auf **Test**, um die Verbindung für jeden der FQDN-Werte zu testen. Wenn der Test fehlschlägt, ändern Sie die DNS-Einstellungen so, dass der Hostname des IaaS-Servers aufgelöst wird.
    10. Geben Sie einen Namen für den vCenter-Serverendpunkt für die private Cloud ein. Notieren Sie sich den Namen für die spätere Verwendung im Konfigurationsprozess.

        ![vRA-Installationsproxy](media/configure-vra-endpoint-proxy.png)

    11. Klicken Sie auf **Weiter**.
    12. Klicken Sie auf **Installieren**.

## <a name="configure-the-vsphere-agent"></a>Konfigurieren des vSphere-Agents

1. Wechseln Sie zu https://*vra-url*/vcac, und melden Sie sich als **ConfigurationAdmin** an.
2. Wählen Sie **Infrastructure** > **Endpoints** > **Endpoints** (Infrastruktur->Endpunkt->Endpunkt) aus.
3. Wählen Sie **New** > **Virtual** > **vSphere** (Neu->Virtuell->vSphere) aus.
4. Geben Sie den Namen des vSphere-Endpunkts ein, den Sie im vorherigen Schritt angegeben haben.
5. Geben Sie für **Address** die URL des vCenter-Servers der privaten Cloud im Format https://*vcenter-fqdn*/sdk ein, wobei *vcenter-fqdn* der Name des vCenter-Servers ist.
6. Geben Sie die Anmeldeinformationen des IaaS-Administratorkontos für vRealize Automation ein, das der CloudSimple-Support für Sie erstellt hat.
7. Klicken Sie auf **Test Connection** (Verbindung testen), um die Anmeldeinformationen des Benutzers zu überprüfen. Wenn der Test fehlschlägt, überprüfen Sie die URL, Kontoinformationen und den [Namen des Endpunkts](#verify-the-endpoint-name), und wiederholen Sie den Test.
8. Klicken Sie nach einem erfolgreichen Test auf **OK**, um den vSphere-Endpunkt zu erstellen.
    ![Zugriff auf die Konfiguration des vRA-Endpunkts](media/configure-vra-endpoint-vra-edit.png)

### <a name="verify-the-endpoint-name"></a>Überprüfen des Endpunktnamens

Gehen Sie folgendermaßen vor, um den richtigen Namen des Endpunkts des vCenter-Servers zu bestimmen:

1. Öffnen Sie auf der IaaS-Appliance eine Eingabeaufforderung.
2. Wechseln Sie zum Verzeichnis C:\Program Files (x86)\VMware\vCAC\Agents\agent-name, wobei *agent-name* der Name ist, den Sie dem Endpunkt des vCenter-Servers zugewiesen haben.
3. Führen Sie den folgenden Befehl aus.

```
..\..\Server\DynamicOps.Vrm.VRMencrypt.exe VRMAgent.exe.config get
```

Die Ausgabe sieht in etwa wie folgt aus: Der Wert des Felds `managementEndpointName` ist ein Endpunktname.

```
managementEndpointName: cslab1pc3-vc
doDeletes: true
```
