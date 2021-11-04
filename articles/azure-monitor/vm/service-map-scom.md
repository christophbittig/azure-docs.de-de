---
title: Integrieren des Zuordnungsfeatures von VM Insights in Operations Manager | Microsoft-Dokumentation
description: VM Insights ermittelt automatisch Anwendungskomponenten auf Windows- und Linux-Systemen und stellt die Kommunikation zwischen Diensten dar. In diesem Artikel wird die Verwendung des Zuordnungsfeatures zum automatischen Erstellen von Diagrammen der verteilten Anwendungen in Operations Manager erörtert.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: eacbf24fdd4d92ac4da1419c02be7ae006b640d4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432242"
---
# <a name="integrate-system-center-operations-manager-with-vm-insights-map-feature"></a>Integrieren des Zuordnungsfeatures von VM Insights in System Center Operations Manager

In VM Insights können Sie sich ermittelte Anwendungskomponenten für Windows- und Linux-VMs anzeigen lassen, die in Azure oder in Ihrer eigenen Umgebung ausgeführt werden. Durch diese Integration zwischen dem Zuordnungsfeature und System Center Operations Manager können Sie in Operations Manager automatisch Diagramme der verteilten Anwendungen erstellen, die auf den dynamischen Abhängigkeitszuordnungen in VM Insights basieren. In diesem Artikel wird beschrieben, wie Sie Ihre System Center Operations Manager-Verwaltungsgruppe für die Unterstützung dieses Features konfigurieren.

>[!NOTE]
>Wenn Sie die Dienstzuordnung bereits bereitgestellt haben, können Sie Ihre Zuordnungen in VM Insights anzeigen. Diese Lösung enthält zusätzliche Funktionen zum Überwachen der Integrität und Leistung von virtuellen Computern. Das Zuordnungsfeature von VM Insights soll die eigenständige Dienstzuordnungslösung ersetzen. Weitere Informationen finden Sie unter [Übersicht über VM Insights](../vm/vminsights-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Eine System Center Operations Manager-Verwaltungsgruppe (2012 R2 oder höher).
* Ein für die Unterstützung von VM Insights konfigurierter Log Analytics-Arbeitsbereich.
* Ein oder mehrere virtuelle Windows- und Linux-Computer oder physische Computer, die von Operations Manager überwacht werden und Daten an Ihren Log Analytics Arbeitsbereich senden. Linux-Server, die Berichte an eine Operations Manager-Verwaltungsgruppe senden, müssen für eine direkte Verbindung mit Azure Monitor konfiguriert werden. Weitere Informationen finden Sie in der Übersicht unter [Sammeln von Protokolldaten mit dem Log Analytics-Agent](../agents/log-analytics-agent.md).
* Ein Dienstprinzipal mit Zugriff auf das Azure-Abonnement, das dem Log Analytics-Arbeitsbereich zugeordnet ist. Weitere Informationen finden Sie unter [Erstellen eines Dienstprinzipals](#create-a-service-principal).

## <a name="install-the-service-map-management-pack"></a>Installieren des Service Map Management Pack

Sie aktivieren die Integration zwischen Operations Manager und dem Zuordnungsfeature durch Importieren des Management Pack-Pakets „Microsoft.SystemCenter.ServiceMap“ („Microsoft.SystemCenter.ServiceMap.mpb“). Sie können das Management Pack-Paket aus dem [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=55763) herunterladen. Das Paket enthält die folgenden Management Packs:

* Microsoft Service Map Application Views
* Microsoft System Center Service Map Internal
* Microsoft System Center Service Map Overrides
* Microsoft System Center Service Map

## <a name="configure-integration"></a>Konfigurieren der Integration

Nach der Installation des Management Packs der Dienstzuordnung wird in Ihrer Operations Manager-Betriebskonsole im Bereich **Verwaltung** unter **Operations Management Suite** ein neuer Knoten mit dem Namen **Dienstzuordnung** angezeigt.

>[!NOTE]
>[Operations Management Suite war eine Sammlung von Diensten](../terminology.md#april-2018---retirement-of-operations-management-suite-brand), zu der auch Log Analytics gehörte, das jetzt Teil von [Azure Monitor](../overview.md) ist.

Gehen Sie folgendermaßen vor, um die Integration des Zuordnungsfeatures von VM Insights zu konfigurieren:

1. Um den Konfigurations-Assistenten zu öffnen, klicken Sie im Bereich **Service Map Overview** (Service Map-Übersicht) auf **Arbeitsbereich hinzufügen**.  

    ![Bereich „Service Map Overview“ (Service Map-Übersicht)](media/service-map-scom/scom-configuration.png)

2. Geben Sie im Fenster **Verbindungskonfiguration** den Mandantennamen oder die Mandanten-ID, die Anwendungs-ID (auch als Benutzername bzw. clientID bezeichnet) und das Kennwort des Dienstprinzipals ein, und klicken Sie auf **Weiter**. Weitere Informationen finden Sie unter „Erstellen eines Dienstprinzipals“.

    ![Das Fenster „Verbindungskonfiguration“](media/service-map-scom/scom-config-spn.png)

3. Wählen Sie im Fenster **Abonnementauswahl** das Azure-Abonnement, die Azure-Ressourcengruppe (diejenige, die den Log Analytics-Arbeitsbereich enthält) und den Log Analytics-Arbeitsbereich, und klicken Sie dann auf **Weiter**.

    ![Der Operations Manager-Konfigurationsarbeitsbereich](media/service-map-scom/scom-config-workspace.png)

4. Wählen Sie im Fenster **Computergruppenauswahl**, welche Service Map-Computergruppen Sie mit Operations Manager synchronisieren möchten. Klicken Sie auf **Computergruppen hinzufügen/entfernen**, wählen Sie Gruppen aus der Liste der **Verfügbaren Computergruppen**, und klicken Sie auf **Hinzufügen**.  Klicken Sie nach Auswahl der Gruppen auf **OK**, um den Vorgang abzuschließen.

    ![Die Operations Manager-Konfigurationscomputergruppen](media/service-map-scom/scom-config-machine-groups.png)

5. Konfigurieren Sie im Fenster **Serverauswahl** die Servergruppe für die Dienstzuordnung mit den Servern, die Sie zwischen Operations Manager und dem Zuordnungsfeature synchronisieren möchten. Klicken Sie auf **Server hinzufügen/entfernen**.

    Damit die Integration ein Diagramm der verteilten Anwendung für einen Server erstellen kann, muss der Server folgende Voraussetzungen erfüllen:

   * Er muss von Operations Manager überwacht werden.
   * Er muss für das Senden von Berichten an den mit VM Insights konfigurierten Log Analytics-Arbeitsbereich konfiguriert sein.
   * In der „Service Map-Servergruppe“ aufgelistet

     ![Die Operations Manager-Konfigurationsgruppe](media/service-map-scom/scom-config-group.png)

6. Optional: Wählen Sie den Ressourcenpool für alle Verwaltungsserver für die Kommunikation mit Log Analytics aus, und klicken Sie dann auf **Arbeitsbereich hinzufügen**.

    ![Screenshot: Bildschirm „Serverpool“ unter „Add Microsoft Operations Management Suite Workspace“ (Microsoft Operations Management Suite-Arbeitsbereich hinzufügen), auf dem „All Management Servers Resource Pool“ (Ressourcenpool für alle Verwaltungsserver) ausgewählt ist](media/service-map-scom/scom-config-pool.png)

    Es kann eine Minute dauern, den Log Analytics-Arbeitsbereich zu konfigurieren und registrieren. Nach Abschluss der Konfiguration des Arbeitsbereichs initiiert Operations Manager die erste Zuordnungssynchronisierung.

    ![Screenshot: Bildschirm „Abschluss“ unter „Add Microsoft Operations Management Suite Workspace“ (Microsoft Operations Management Suite-Arbeitsbereich hinzufügen) mit der Bestätigung, dass der Arbeitsbereich hinzugefügt wurde](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Überwachen der Integration

Nachdem die Verbindung mit dem Log Analytics-Arbeitsbereich hergestellt wurde, wird in der Operations Manager-Betriebskonsole im Bereich **Überwachung** ein neuer Ordner mit dem Namen „Dienstzuordnung“ angezeigt.

![Operations Manager-Bereich „Überwachung“](media/service-map-scom/scom-monitoring.png)

Der Ordner „Service Map“ umfasst vier Knoten:

* **Aktive Warnungen**: Listet alle aktiven Warnungen zur Kommunikation zwischen Operations Manager und Azure Monitor auf.  

  >[!NOTE]
  >Bei diesen Warnungen handelt es sich nicht um Log Analytics-Warnungen, die mit Operations Manager synchronisiert werden. Sie werden in der Verwaltungsgruppe basierend auf Workflows generiert, die im Management Pack der Dienstzuordnung definiert sind.

* **Server**: Listet die überwachten Server auf, die für die Synchronisierung über das Zuordnungsfeature von VM Insights konfiguriert sind.

    ![Der Serverüberwachungsbereich von Operations Manager](media/service-map-scom/scom-monitoring-servers.png)

* **Computerabhängigkeitsansichten**: Listet alle Computergruppen auf, die über das Zuordnungsfeature synchronisiert werden. Sie können auf eine beliebige Gruppe klicken, um das entsprechende Diagramm der verteilten Anwendungen anzuzeigen.

    ![Screenshot: Dienstzuordnung mit einem Diagramm mit Bildern für die einzelnen Computergruppen sowie Linien, die die Abhängigkeiten dazwischen darstellen](media/service-map-scom/scom-group-dad.png)

* **Serverabhängigkeitsansichten**: Listet alle Server auf, die über das Zuordnungsfeature synchronisiert werden. Sie können auf einen beliebigen Server klicken, um das entsprechende Diagramm der verteilten Anwendungen anzuzeigen.

    ![Screenshot: Dienstzuordnung mit einem Diagramm mit Bildern für die einzelnen Servern sowie Linien, die die Abhängigkeiten dazwischen darstellen](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Bearbeiten oder Löschen des Arbeitsbereichs

Sie können den konfigurierten Arbeitsbereich im Bereich der **Service Map-Übersicht** (Bereich **Verwaltung** > **Operations Management Suite** > **Service Map**) bearbeiten oder löschen.

> [!NOTE]
> [Operations Management Suite war eine Sammlung von Diensten](../terminology.md#april-2018---retirement-of-operations-management-suite-brand), zu der auch Log Analytics, das jetzt Teil von [Azure Monitor](../overview.md) ist, gehörte.

In diesem aktuellen Release können Sie nur einen Log Analytics-Arbeitsbereich konfigurieren.

![Der Bereich zum Bearbeiten des Arbeitsbereichs im Operations Manager](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Konfigurieren von Regeln und Außerkraftsetzungen

Die Regel *Microsoft.SystemCenter.ServiceMapImport.Rule* ruft regelmäßig Informationen aus dem Zuordnungsfeature von VM Insights ab. Wenn Sie das Synchronisierungsintervall ändern möchten, können Sie die Regel außer Kraft setzen und den Wert für den Parameter **IntervalMinutes** ändern.

![Das Operations Manager-Fenster für Eigenschaften von Außerkraftsetzungen](media/service-map-scom/scom-overrides.png)

* **Enabled**: Aktivieren oder Deaktivieren der automatischen Updates.
* **IntervalMinutes**: Gibt die Zeitspanne zwischen Updates an. Das Standardintervall beträgt eine Stunde. Wenn Sie Zuordnungen in kürzeren Abständen synchronisieren möchten, können Sie den Wert ändern.
* **TimeoutSeconds**: Gibt die Zeitspanne bis zum Timeout der Anforderung an.
* **TimeWindowMinutes**: Gibt das Zeitfenster zum Abfragen von Daten an. Der Standardwert ist 60 Minuten. Dies ist das maximal zulässige Intervall.

## <a name="known-issues-and-limitations"></a>Einschränkungen und bekannte Probleme

Das aktuelle Design weist folgende Probleme und Einschränkungen auf:

* Sie können nur eine Verbindung mit einem einzigen Log Analytics-Arbeitsbereich herstellen.
* Sie können im Bereich **Erstellen** der „Service Map-Servergruppe“ zwar manuell Server hinzufügen, aber die Zuordnungen für diese Server werden nicht sofort synchronisiert. Sie werden beim nächsten Synchronisierungszyklus vom VM Insights-Zuordnungsfeature synchronisiert.
* Wenn Sie Änderungen an den vom Management Pack erstellten Diagrammen der verteilten Anwendungen vornehmen, werden diese Änderungen wahrscheinlich bei der nächsten Synchronisierung mit VM Insights überschrieben.

## <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals

Die offizielle Azure-Dokumentation zum Erstellen eines Dienstprinzipals finden Sie in den folgenden Dokumenten:

* [Erstellen eines Dienstprinzipals für den Zugriff auf Ressourcen mithilfe von Azure PowerShell](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Erstellen eines Dienstprinzipals für den Zugriff auf Ressourcen mithilfe der Azure-Befehlszeilenschnittstelle](/cli/azure/create-an-azure-service-principal-azure-cli)
* [Erstellen einer Azure Active Directory-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff mithilfe des Portals](../../active-directory/develop/howto-create-service-principal-portal.md)

### <a name="suggestions"></a>Vorschläge

Möchten Sie uns Feedback zur Integration des Zuordnungsfeatures von VM Insights oder zu dieser Dokumentation geben? Besuchen Sie unsere [UserVoice-Webseite](https://feedback.azure.com/d365community/forum/aa68334e-1925-ec11-b6e6-000d3a4f09d0?c=ad4304e4-1925-ec11-b6e6-000d3a4f09d0), auf der Sie Features vorschlagen oder vorhandene Vorschläge unterstützen können.

