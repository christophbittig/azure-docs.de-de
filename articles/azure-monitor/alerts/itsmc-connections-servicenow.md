---
title: Verbinden von ServiceNow mit dem ITSM-Connector
description: Erfahren Sie, wie Sie ServiceNow mit dem ITSM-Connector (ITSMC) in Azure Monitor verbinden, um ITSM-Arbeitselemente zentral zu überwachen und zu verwalten.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/21/2020
ms.openlocfilehash: e3d696580acd995a2396d56c404e2f904611ccac
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/04/2021
ms.locfileid: "129430134"
---
# <a name="connect-servicenow-with-it-service-management-connector"></a>Verbinden von ServiceNow mit dem ITSM-Connector

In diesem Artikel erfahren Sie, wie Sie die Verbindung zwischen einer ServiceNow-Instanz und dem ITSM-Connector (ITSMC) in Log Analytics konfigurieren, damit Sie ITSM-Arbeitselemente(IT-Service-Management) zentral verwalten können.

## <a name="prerequisites"></a>Voraussetzungen
Stellen Sie sicher, dass die folgenden Voraussetzungen für die Verbindung erfüllt sind.

### <a name="itsmc-installation"></a>ITSMC-Installation

Informationen zum Installieren von ITSMC finden Sie unter [Hinzufügen der IT Service Management Connector-Lösung](./itsmc-definition.md#add-it-service-management-connector).

> [!NOTE]
> ITSMC unterstützt nur das offizielle SaaS-Angebot (Software-as-a-Service) von ServiceNow. Private Bereitstellungen von ServiceNow werden nicht unterstützt.

### <a name="oauth-setup"></a>OAuth-Setup

Zu den von ServiceNow unterstützten Versionen gehören Quebec, Paris, Orlando, New York, Madrid, London, Kingston, Jakarta, Istanbul, Helsinki und Geneva.

ServiceNow-Administratoren müssen eine Client-ID und einen geheimen Clientschlüssel für ihre ServiceNow-Instanz generieren. Sehen Sie sich die jeweils zutreffenden folgenden Informationen an:

- [OAuth-Setup für Quebec](https://docs.servicenow.com/bundle/quebec-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [OAuth-Setup für Paris](https://docs.servicenow.com/bundle/paris-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [OAuth-Setup für Orlando](https://docs.servicenow.com/bundle/orlando-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)
- [OAuth-Setup für London](https://docs.servicenow.com/bundle/london-platform-administration/page/administer/security/task/t_SettingUpOAuth.html)

Im Rahmen des OAuth-Setup wird Folgendes empfohlen:

1. [Erstellen Sie einen Endpunkt für Clients, um auf die Instanz zuzugreifen](https://docs.servicenow.com/bundle/rome-platform-administration/page/administer/security/task/t_CreateEndpointforExternalClients.html).

1. Aktualisieren Sie die Lebensdauer des Aktualisierungstokens:

   1. Suchen Sie im Bereich **ServiceNow** nach **System-OAuth**, und wählen Sie dann **Anwendungsregistrierung** aus. 
   1. Wählen Sie den Namen des definierten OAuth aus, und ändern Sie die **Lebensdauer des Aktualisierungstokens** in **7.776.000 Sekunden** (90 Tage). 
   1. Wählen Sie **Aktualisieren** aus. 

1. Richten Sie eine interne Prozedur ein, um sicherzustellen, dass die Verbindung aktiv bleibt. Führen Sie einige Tage bevor die Lebensdauer des Aktualisierungstokens erwartungsgemäß abläuft die folgenden Vorgänge aus:

   1. [Führen Sie einen manuellen Synchronisierungsprozess für die Konfiguration des ITSM-Connectors aus.](./itsmc-resync-servicenow.md)

   1. Widerrufen Sie das alte Aktualisierungstoken. Aus Sicherheitsgründen wird davon abgeraten, alte Schlüssel aufzubewahren. 
   
      1. Suchen Sie im Bereich **ServiceNow** nach **System-OAuth**, und wählen Sie dann **Token verwalten** aus. 
      
      1. Wählen Sie das alte Token nach OAuth-Name und Ablaufdatum aus der Liste aus.

         ![Screenshot einer Liste von Token für OAuth](media/itsmc-connections-servicenow/snow-system-oauth.png)
      1. Wählen Sie **Zugriff widerrufen** > **Widerrufen** aus.

## <a name="install-the-user-app-and-create-the-user-role"></a>Installieren der Benutzer-App und Erstellen der Benutzerrolle

Gehen Sie wie folgt vor, um die ServiceNow-Benutzer-App zu installieren und die Benutzerrolle „Integration“ dafür zu erstellen. Sie verwenden diese Anmeldeinformationen zum Herstellen der ServiceNow-Verbindung in Azure.

> [!NOTE]
> ITSMC unterstützt nur die offizielle Benutzer-App für die Microsoft Log Analytics-Integration, die aus dem ServiceNow Store heruntergeladen wird. ITSMC unterstützt weder eine Codeerfassung seitens ServiceNow noch Anwendungen, die nicht Teil der offiziellen ServiceNow-Lösung sind. 

1. Rufen Sie den [ServiceNow Store](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ab0265b2dbd53200d36cdc50cf961980/1.0.1) auf, und installieren Sie die **Benutzer-App für die ServiceNow- und Microsoft OMS-Integration** in Ihrer ServiceNow-Instanz.
   
   >[!NOTE]
   >Im Rahmen der laufenden Umstellung von der Microsoft Operations Management Suite (OMS) auf Azure Monitor wird OMS jetzt Log Analytics genannt.     
2. Wechseln Sie nach der Installation zur linken Navigationsleiste der ServiceNow-Instanz, suchen Sie nach dem **Microsoft OMS-Integrator**, und wählen Sie ihn aus.  
3. Wählen Sie **Installationsprüfliste** aus.

   Der Status **Nicht abgeschlossen** wird angezeigt, da die Benutzerrolle noch nicht erstellt wurde.

4. Geben Sie im Textfeld neben **Integrationsbenutzer erstellen** den Namen für den Benutzer ein, der in Azure eine Verbindung mit ITSMC herstellen kann.
5. Geben Sie das Kennwort für diesen Benutzer ein, und wählen Sie dann **OK** aus.  

Der neu erstellte Benutzer wird mit den zugewiesenen Standardrollen angezeigt:

- personalize_choices
- import_transformer
- x_mioms_microsoft.user
- itil
- template_editor
- view_changer

Nachdem Sie den Benutzer erfolgreich erstellt haben, wechselt der Status von **Installationsprüfliste überprüfen** zu **Abgeschlossen**, und die Details der für die App erstellten Benutzerrolle werden aufgelistet.

> [!NOTE]
> ITSMC kann Incidents an ServiceNow senden, ohne dass andere Module auf Ihrer ServiceNow-Instanz installiert sind. Wenn Sie das EventManagement-Modul in Ihrer ServiceNow-Instanz verwenden und Ereignisse oder Warnungen mit dem Connector in ServiceNow erstellen möchten, fügen Sie dem Integrationsbenutzer die folgenden Rollen hinzu:
> 
> - evt_mgmt_integration
> - evt_mgmt_operator  

## <a name="create-a-connection"></a>Erstellen einer Verbindung
Verwenden Sie das folgende Verfahren, um eine ServiceNow-Verbindung zu erstellen.

> [!NOTE]
> Durch die Warnungen, die von Azure Monitor gesendet werden, kann in ServiceNow eines der folgenden Elemente erstellt werden: Ereignisse, Incidents oder Warnungen. 

1. Navigieren Sie im Azure-Portal zu **Alle Ressourcen**, und suchen Sie nach **ServiceDesk(YourWorkspaceName)** .

2. Wählen Sie unter **Arbeitsbereichsdatenquellen** die Option **ITSM-Verbindungen** aus.

   ![Screenshot der Auswahl einer Datenquelle](media/itsmc-overview/add-new-itsm-connection.png)

3. Wählen Sie oben im rechten Bereich **Hinzufügen** aus.

4. Geben Sie die Informationen gemäß der Beschreibung in der folgenden Tabelle an, und wählen Sie dann **OK** aus.

   | **Feld** | **Beschreibung** |
   | --- | --- |
   | **Verbindungsname**   | Geben Sie einen Namen für die ServiceNow-Instanz ein, die Sie mit ITSMC verbinden möchten. Diesen Namen verwenden Sie später in Log Analytics beim Konfigurieren von ITSM-Arbeitselementen und beim Anzeigen ausführlicher Analysen. |
   | **Partnertyp**   | Wählen Sie **ServiceNow** aus. |
   | **Server-URL**   | Geben Sie die URL der ServiceNow-Instanz ein, die Sie mit ITSMC verbinden möchten. Die URL muss auf eine unterstützte SaaS-Version mit dem Suffix *.servicenow.com* verweisen (z. B. `https://XXXXX.service-now.com/`).|
   | **Benutzername**   | Geben Sie den Integrationsbenutzernamen ein, den Sie in der ServiceNow-App zur Unterstützung der Verbindung mit ITSMC erstellt haben.|
   | **Kennwort**   | Geben Sie das diesem Benutzernamen zugeordnete Kennwort ein. **Hinweis**: Der Benutzername und das Kennwort werden nur zum Generieren von Authentifizierungstoken verwendet. Sie werden nicht im ITSMC-Dienst gespeichert.  |
   | **Client-ID**   | Geben Sie die Client-ID ein, die Sie für die OAuth2-Authentifizierung verwenden möchten, die Sie zuvor generiert haben. Weitere Informationen zum Generieren einer Client-ID und eines geheimen Schlüssels finden Sie unter dem [OAuth-Setup](https://old.wiki/index.php/OAuth_Setup). |
   | **Geheimer Clientschlüssel**   | Geben Sie den für diese ID generierten geheimen Clientschlüssel ein.   |
   | **Bereich für Datensynchronisierung (in Tagen)** | Geben Sie die Anzahl der vergangenen Tage ein, aus denen die Daten abgerufen werden sollen. Der Grenzwert beträgt 120 Tage. |
   | **Zu synchronisierende Arbeitselemente**   | Wählen Sie die ServiceNow-Arbeitselemente aus, die Sie über ITSMC mit Azure Log Analytics synchronisieren möchten. Die ausgewählten Werte werden in Log Analytics importiert. Optionen sind Incidents und Änderungsanforderungen.|
   | **Neues Konfigurationselement in ITSM-Produkt erstellen** | Wählen Sie diese Option, wenn Sie die Konfigurationselemente im ITSM-Produkt erstellen möchten. Wenn diese Option ausgewählt ist, erstellt ITSMC im unterstützten ITSM-System Konfigurationselemente (falls keine vorhanden sind). Standardmäßig ist es deaktiviert. |

![Screenshot der Felder und Optionen zum Hinzufügen einer ServiceNow-Verbindung](media/itsmc-connections-servicenow/itsm-connection-servicenow-connection-latest.png)

Bei erfolgreicher Verbindung und Synchronisierung:

- Ausgewählte Arbeitselemente aus der ServiceNow-Instanz werden in Log Analytics importiert. Die Zusammenfassung dieser Arbeitselemente können Sie auf der Kachel **IT Service Management Connector** anzeigen.

- Sie können Incidents über Log Analytics-Warnungen oder -Protokolldatensätze oder über Azure-Warnungen in dieser ServiceNow-Instanz erstellen.

> [!NOTE]
> ServiceNow weist eine Begrenzung der Anforderungen pro Stunde auf. Um den Grenzwert zu konfigurieren, definieren Sie die **Beschränkung für eingehende REST-API-Raten** in der ServiceNow-Instanz.

## <a name="next-steps"></a>Nächste Schritte

* [Übersicht über den ITSM-Connector](itsmc-overview.md)
* [Erstellen von ITSM-Arbeitselementen aus Azure-Warnungen](./itsmc-definition.md#create-itsm-work-items-from-azure-alerts)
* [Problembehandlung in ITSM-Connector](./itsmc-resync-servicenow.md)
