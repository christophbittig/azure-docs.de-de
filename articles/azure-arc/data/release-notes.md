---
title: Azure Arc-fähige Datendienste – Versionshinweise
description: Aktuelle Versionshinweise
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 11/03/2021
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: ac5b33de1a95b413c7eba92d2bdeaa8cc05b3a05
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059562"
---
# <a name="release-notes---azure-arc-enabled-data-services"></a>Versionshinweise: Azure Arc-fähige Datendienste

In diesem Artikel werden die Funktionen, Features und Verbesserungen hervorgehoben, die kürzlich für Azure Arc-fähige Datendienste veröffentlicht bzw. durchgeführt wurden.

## <a name="november-2021"></a>November 2021

Dieses Release wird am 3. November 2021 veröffentlicht.

#### <a name="tools"></a>Tools

##### <a name="azure-data-studio"></a>Azure Data Studio

Installieren Sie oder aktualisieren Sie auf die neueste Version der [Arc-Erweiterung für Azure Data Studio](/sql/azure-data-studio/extensions/azure-arc-extension).

##### <a name="azure-az-cli"></a>Azure (`az`) CLI

Installieren Sie oder aktualisieren Sie die Erweiterung `arcdata` für `az`-CLI, damit die Bereitstellung über eine direkte Verbindung unterstützt wird.

Die folgenden `sql`-Befehle unterstützen jetzt den direkten Verbindungsmodus:

   ```console
   az arcdata dc create
   az arcdata dc delete
   az arcdata sql mi-arc create
   az arcdata sql mi-arc delete
   ```
 
#### <a name="data-controller"></a>Datencontroller

- Direkter Verbindungsmodus allgemein verfügbar
- Direkt verbundene Azure Arc-Datencontrollererweiterungen auf Kubernetes-Clustern mit Azure Arc-Unterstützung verwenden jetzt systemseitig generierte verwaltete Identitäten anstelle des Dienstprinzipalnamens. Die verwaltete Identität wird automatisch erstellt, wenn eine neue Azure Arc-Datencontrollererweiterung erstellt wird. Sie müssen weiterhin die geeigneten Berechtigungen zum Hochladen von Verbrauchsdaten und Metriken erteilen.
- Beim Hochladen von Metriken wird die systemseitig generierte verwaltete Identität mit einem direkt verbundenen Azure Arc-Datencontroller genutzt. 
- Erstellen eines Azure Arc-Datencontrollers im direkten Verbindungsmodus aus Azure CLI (`az`).
- Automatisches Hochladen von Metriken in Azure Monitor
- Automatisches Hochladen von Protokollen in Azure Log Analytics
- Aktivieren oder deaktivieren Sie das automatische Hochladen von Metriken und/oder Protokollen in Azure nach der Bereitstellung des Azure Arc-Datencontrollers.
- Führen Sie mithilfe der Azure CLI ein direktes Upgrade vom Juli 2021-Release durch (nur für allgemein verfügbare Dienste wie Azure Arc-Datencontroller und universelle SQL Managed Instance).
- Legen Sie die Benutzernamen und Kennwörter der Metrik- und Protokolldashboards zur Datencontroller-Bereitstellungszeit separat fest, indem Sie die neuen Umgebungsvariablen verwenden:

   ```console
   AZDATA_LOGSUI_USERNAME
   AZDATA_LOGSUI_PASSWORD
   AZDATA_METRICSUI_USERNAME
   AZDATA_METRICSUI_PASSWORD
   ```
- Neuer Befehl: `az arcdata dc list-upgrades` zeigt die Liste der verfügbaren Upgrades vom derzeit bereitgestellten Datencontroller an.


Sie können die Umgebungsvariablen `AZDATA_USERNAME` und `AZDATA_PASSWORD` weiterhin wie zuvor verwenden. Wenn Sie nur `AZDATA_USERNAME` und `AZDATA_PASSWORD` angeben, verwendet die Bereitstellung sie sowohl für die Protokoll- als auch für die Metrikdashboards.

##### <a name="region-availability"></a>Regionale Verfügbarkeit

Dieses Release führt die Verfügbarkeit des direkt verbundenen Modus in den folgenden Azure-Regionen ein:

- USA Nord Mitte
- USA (Westen)
- USA, Westen 3

Eine vollständige Liste finden Sie unter [Unterstützte Regionen](overview.md#supported-regions).

#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance mit Azure Arc-Unterstützung

- Direktes Upgrade von Instanzen von universeller SQL Managed Instance mit Azure Arc-Unterstützung
- Die SQL-Binärdateien werden auf eine neue Version aktualisiert.
- Bereitstellung im direkt verbundenen Modus von SQL Managed Instance mit Azure Arc-Unterstützung mithilfe von Azure CLI
- Die Zeitpunktwiederherstellung (PITR) für SQL Managed Instance mit Azure Arc-Unterstützung wird mit diesem Release allgemein verfügbar gemacht. Derzeit wird die Zeitpunktwiederherstellung (PITR) nur für die universelle SQL Managed Instance unterstützt. Zeitpunktwiederherstellung (PITR) für unternehmenskritische SQL Managed Instance befindet sich noch in der Vorschauphase.
- Neue `--dry-run`-Option für Zeitpunktwiederherstellung (PITR) bereitgestellt.
- Recovery Point Objective ist standardmäßig auf 5 Minuten festgelegt und kann nicht konfiguriert werden.
- Der Aufbewahrungszeitraum für Sicherungen ist standardmäßig auf 7 Tage festgelegt. Eine neue Option zum Festlegen des Aufbewahrungszeitraums auf 0 deaktiviert automatische Sicherungen für Entwicklungs- und Testinstanzen, die keine Sicherungen erfordern.
- Problem behoben, bei dem der Zeitpunktwiederherstellungsvorgang die konfigurierte Zeitzone nicht beachtet hat. 
- Wiederherstellen auf einen Zeitpunkt über Azure CLI oder Azure Data Studio
 

### <a name="known-issues"></a>Bekannte Probleme

#### <a name="data-controller-upgrade"></a>Datencontrollerupgrade

- Derzeit wird das Upgrade eines direkt verbundenen Datencontrollers über die CLI oder das Portal nicht unterstützt.
- Sie können derzeit nur ein Upgrade für allgemein verfügbare Dienste wie Azure Arc-Datencontroller und universelle SQL Managed Instance durchführen. Wenn Sie auch über unternehmenskritische SQL Managed Instance und/oder PostgreSQL Hyperscale mit Azure Arc-Unterstützung verfügen, entfernen Sie sie zuerst, bevor Sie mit dem Upgrade fortfahren.

#### <a name="commands"></a>Befehle

Die folgenden Befehle unterstützen derzeit keinen direkt verbundenen Modus:

```console
az arcdata dc update
az arcdata sql mi-arc update
```

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>PostgreSQL Hyperscale mit Azure Arc-Unterstützung

- Das Sichern und Wiederherstellen von Servern mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung wird in der aktuellen Vorschauversion nicht unterstützt.

- Es ist nicht möglich, die `pg_cron`-Erweiterung gleichzeitig zu aktivieren und zu konfigurieren. Hierfür müssen Sie zwei Befehle verwenden. Einen Befehl zum Aktivieren und einen Befehl zum Konfigurieren. Beispiel:

   1. Aktivieren der Erweiterung:

      ```console
      az postgres arc-server edit -n myservergroup --extensions pg_cron
      ```

   1. Neustarten des Servers.

   1. Konfigurieren der Erweiterung:

      ```console
      az postgres arc-server edit -n myservergroup --engine-settings cron.database_name='postgres'
      ```

   Wenn Sie den zweiten Befehl ausführen, bevor der Neustart abgeschlossen wurde, tritt ein Fehler auf. In diesem Fall warten Sie einfach einige weitere Augenblicke, und führen Sie den zweiten Befehl erneut aus.

- Durch das Übergeben eines ungültigen Werts an den Parameter `--extensions` beim Bearbeiten der Konfiguration einer Servergruppe, um zusätzliche Erweiterungen zu aktivieren, wird die Liste der aktivierten Erweiterungen fälschlicherweise auf die Liste zurückgesetzt, die zum Zeitpunkt der Erstellung der Servergruppe bestanden hat, und verhindert, dass Benutzer zusätzliche Erweiterungen erstellen können. Die einzige verfügbare Problemumgehung besteht in diesem Fall im Löschen und erneuten Bereitstellen der Servergruppe.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance mit Azure Arc-Unterstützung

- Wenn ein Pod erneut bereitgestellt wird, startet SQL Managed Instance einen neuen Satz vollständiger Sicherungen für alle Datenbanken.
- Wenn Ihr Datencontroller direkt verbunden ist, müssen Sie zunächst ein Upgrade des Datencontrollers auf die neueste Version durchführen, bevor Sie eine SQL Managed Instance bereitstellen können. Der Versuch, eine SQL Managed Instance mit einer imageVersion des Datencontrollers von `v1.0.0_2021-07-30` bereitzustellen, wird nicht erfolgreich sein.


##### <a name="other-limitations"></a>Weitere Einschränkungen

- Die Transaktionsreplikation wird derzeit nicht unterstützt.
- Der Protokollversand wird derzeit blockiert.
- Nur die SQL Server-Authentifizierung wird unterstützt.

## <a name="july-2021"></a>Juli 2021

Dieses Release wird am 30. Juli 2021 veröffentlicht.

Mit diesem Release wird auch die allgemeine Verfügbarkeit der [Dienstebene „Universell“](service-tiers.md) für SQL Managed Instance mit Azure Arc-Unterstützung im indirekt verbundenen Modus bekannt gegeben.

   > [!NOTE]
   > Darüber hinaus bietet dieses Release die folgenden Azure Arc-fähigen Dienste als Vorschauversionen: 
   > - SQL Managed Instance im direkt verbundenen Modus
   > - [Dienstebene „Unternehmenskritisch“](service-tiers.md) von SQL Managed Instance
   > - PostgreSQL Hyperscale

### <a name="breaking-changes"></a>Aktuelle Änderungen

#### <a name="tools"></a>Tools

Verwenden Sie die folgenden Tools:
- [Insiders-Build von Azure Data Studio](https://github.com/microsoft/azuredatastudio#try-out-the-latest-insiders-build-from-main)
- [`arcdata`-Erweiterung für die Azure-Befehlszeilenschnittstelle (`az`)](install-arcdata-extension.md) 


#### <a name="data-controller"></a>Datencontroller

- Der Parameter `--azure-subscription` für `az arcdata dc create` wurde dahin gehend geändert, dass der Standardparameter `--subscription` verwendet wird.
- Für die Bereitstellung in AKS-HCI ist eine spezielle Speicherklassenkonfiguration erforderlich. Einzelheiten finden Sie unter [Konfigurieren des Speichers (Azure Stack HCI mit AKS-HCI)](create-data-controller-indirect-cli.md#configure-storage-azure-stack-hci-with-aks-hci).
- Es gibt eine neue Anforderung zum Zulassen von Nicht-SSL-Verbindungen beim Exportieren von Daten. Legen Sie eine Umgebungsvariable fest, um die interaktive Eingabeaufforderung zu unterdrücken.

### <a name="whats-new"></a>Neues

#### <a name="data-controller"></a>Datencontroller

- Der direkte Verbindungsmodus befindet sich in der Vorschauphase. 

- Der direkte Verbindungsmodus (Vorschau) ist für dieses Release nur in den folgenden Azure-Regionen verfügbar:

   - USA, Norden-Mitte*
   - USA (Mitte)
   - East US
   - USA (Ost) 2
   - USA, Westen *
   - USA, Westen 2
   - USA, Westen 3 *
   - UK, Süden
   - Europa, Westen
   - Nordeuropa
   - Australien (Osten)
   - Asien, Südosten
   - Korea, Mitte
   - Frankreich, Mitte \* Neu hinzugefügt für November 2021.

- Derzeit können Grafana über die Grafana-Verwaltungsfunktionen zusätzliche Benutzer mit Standardauthentifizierung hinzugefügt werden. Das Anpassen von Grafana durch Ändern der INI-Dateien von Grafana wird nicht unterstützt.

- Derzeit wird das Ändern der Konfiguration von ElasticSearch und Kibana nur soweit unterstützt, wie dies über die Kibana-Verwaltungsfunktionen möglich ist. Nur die Standardauthentifizierung mit einem einzelnen Benutzer wird unterstützt.
    
- Benutzerdefinierte Metriken im Azure-Portal befinden sich in der Vorschauphase.

- Das Exportieren von Nutzungs-/Abrechnungsinformationen, Metriken und Protokollen mithilfe des Befehls `az arcdata dc export` erfordert vorerst die Umgehung der SSL-Überprüfung.  Sie werden aufgefordert, die SSL-Überprüfung zu umgehen, oder Sie können die Umgebungsvariable `AZDATA_VERIFY_SSL=no` festlegen, um Aufforderungen zu vermeiden.  Derzeit gibt es keine Möglichkeit, ein SSL-Zertifikat für die Datencontroller-Export-API zu konfigurieren.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance mit Azure Arc-Unterstützung

- Die automatisierte Sicherung und die Zeitpunktwiederherstellung befinden sich in der Vorschauphase.
- Die Zeitpunktwiederherstellung aus einer vorhandenen Datenbank in eine neue Datenbank innerhalb derselben Instanz von SQL Managed Instance mit Azure Arc-Unterstützung wird unterstützt.
- Wenn der aktuelle datetime-Wert im UTC-Format als Zeitpunkt angegeben wurde, wird er in den letzten gültigen Wiederherstellungszeitpunkt konvertiert, und die Datenbank wird bis zur letzten gültigen Transaktion wiederhergestellt.
- Eine Datenbank kann zu jedem Zeitpunkt wiederhergestellt werden, an dem die Transaktionen stattgefunden haben.
- Um ein bestimmtes Recovery Point Objective für eine Instanz von SQL Managed Instance mit Azure Arc-Unterstützung festzulegen, bearbeiten Sie die CRD von SQL Managed Instance, um die `recoveryPointObjectiveInSeconds`-Eigenschaft festzulegen. Die unterstützten Werte liegen zwischen 300 und 600.
- Um automatisierte Sicherungen zu deaktivieren, bearbeiten Sie die CRD der SQL Instanz und legen die `recoveryPointObjectiveInSeconds`-Eigenschaft auf 0 (null) fest.

### <a name="known-issues"></a>Bekannte Probleme

#### <a name="platform"></a>Plattform

- Sie können über das Azure-Portal einen Datencontroller, eine SQL Managed Instance oder eine PostgreSQL Hyperscale-Servergruppe in einem Cluster mit direkter Verbindung erstellen. Die Bereitstellung im direkten Verbindungsmodus wird bei anderen Azure Arc-fähigen Datendiensttools nicht unterstützt. Insbesondere können Sie während dieses Releases keinen Datencontroller im direkten Verbindungsmodus mit einem der folgenden Tools bereitstellen.
   - Azure Data Studio
   - Native Kubernetes-Tools (`kubectl`)
   - Die Erweiterung `arcdata` für die Azure-Befehlszeilenschnittstelle (`az`)

   Unter [Bereitstellen eines Azure Arc-Datencontrollers vom Microsoft Azure-Portal im direkten Verbindungsmodus](create-data-controller-direct-azure-portal.md) wird erläutert, wie Sie den Datencontroller im Portal erstellen.

- Sie können weiterhin `kubectl` verwenden, um Ressourcen direkt in einem Kubernetes-Cluster zu erstellen. Diese werden jedoch nicht im Azure-Portal angezeigt, wenn Sie den direkten Verbindungsmodus verwenden.

- Im direkten Verbindungsmodus ist das Hochladen von Verbrauchsdaten, Metriken und Protokollen mit `az arcdata dc upload` derzeit konzeptionell blockiert. Der Verbrauch wird automatisch hochgeladen. Der Upload für einen Datencontroller, der im indirekten Konnektivitätsmodus erstellt wurde, sollte weiterhin funktionieren.
- Der automatische Upload von Verbrauchsdaten im direkten Konnektivitätsmodus schlägt fehl, wenn ein Proxy über `–proxy-cert <path-t-cert-file>` verwendet wird.
- SQL Managed Instance mit Azure Arc-Unterstützung und PostgreSQL Hyperscale mit Azure Arc-Unterstützung sind nicht nach GB18030 zertifiziert.
- Derzeit wird nur ein Azure Arc-Datencontroller pro Kubernetes-Cluster unterstützt.

#### <a name="data-controller"></a>Datencontroller

- Wenn Azure Arc Datencontroller im Azure-Portal gelöscht werden, erfolgt eine Überprüfung, um das Löschen zu blockieren, wenn Instanzen von SQL Managed Instance mit Azure Arc-Unterstützung auf diesem Arc-Datencontroller bereitgestellt sind. Diese Überprüfung wird derzeit nur angewandt, wenn das Löschen auf der Seite „Übersicht“ des Azure Arc-Datencontrollers erfolgt. 

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>PostgreSQL Hyperscale mit Azure Arc-Unterstützung

- Derzeit kann PosgreSQL Hyperscale nicht in Kubernetes ab Version 1.22 verwendet werden. 
- Sicherungs- und Wiederherstellungsvorgänge funktionieren im Release vom 30. Juli nicht mehr. Dies ist eine vorübergehende Einschränkung. Verwenden Sie vorerst das Release vom Juni 2021, wenn Sie Sicherungen oder Wiederherstellungen durchführen müssen. Dieses Problem wird in einem kommenden Release behoben.

- Es ist nicht möglich, die `pg_cron`-Erweiterung gleichzeitig zu aktivieren und zu konfigurieren. Hierfür müssen Sie zwei Befehle verwenden. Einen Befehl zum Aktivieren und einen Befehl zum Konfigurieren. Beispiel:

   1. Aktivieren der Erweiterung:

      ```console
      azdata postgres arc-server edit -n myservergroup --extensions pg_cron
      ```

   1. Neustarten des Servers.

   1. Konfigurieren der Erweiterung:

      ```console
      azdata postgres arc-server edit -n myservergroup --engine-settings cron.database_name='postgres'
      ```

   Wenn Sie den zweiten Befehl ausführen, bevor der Neustart abgeschlossen wurde, tritt ein Fehler auf. In diesem Fall warten Sie einfach einige weitere Augenblicke, und führen Sie den zweiten Befehl erneut aus.

- Durch das Übergeben eines ungültigen Werts an den Parameter `--extensions` beim Bearbeiten der Konfiguration einer Servergruppe, um zusätzliche Erweiterungen zu aktivieren, wird die Liste der aktivierten Erweiterungen fälschlicherweise auf die Liste zurückgesetzt, die zum Zeitpunkt der Erstellung der Servergruppe bestanden hat, und verhindert, dass Benutzer zusätzliche Erweiterungen erstellen können. Die einzige verfügbare Problemumgehung besteht in diesem Fall im Löschen und erneuten Bereitstellen der Servergruppe.

- Die Point-in-Time-Wiederherstellung wird für NFS-Speicher vorerst nicht unterstützt.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance mit Azure Arc-Unterstützung

##### <a name="cant-see-resources-in-portal"></a>Ressourcen werden im Portal nicht angezeigt

- Im Portal werden keine Ressourcen für SQL Managed Instance mit Azure Arc-Unterstützung angezeigt, die im Juni-Release erstellt wurden. Löschen Sie SQL Managed Instance-Ressourcen aus der Listenansicht der Ressourcengruppe. Möglicherweise müssen Sie zuvor die benutzerdefinierte Standortressource löschen.

##### <a name="point-in-time-restorepitr-supportability-and-limitations"></a>Unterstützung und Einschränkungen bei der Zeitpunktwiederherstellung (Point-in-Time Restore, PITR):
    
- Die Wiederherstellung zwischen Azure Arc-fähigen SQL Managed Instance-Instanzen wird nicht unterstützt.  Die Datenbank kann nur in derselben Instanz von SQL Managed Instance mit Azure Arc-Unterstützung hergestellt werden, in der die Sicherungen erstellt wurden.
- Das Umbenennen einer Datenbank bei einer Zeitpunktwiederherstellung wird derzeit nicht unterstützt.
- Derzeit gibt es keinen CLI-Befehl und keine API, um Informationen zum zulässigen Zeitfenster für die Zeitpunktwiederherstellung anzugeben. Sie können einen Zeitraum innerhalb eines sinnvollen Zeitfensters seit der Datenbankerstellung angeben – die Wiederherstellung funktioniert, wenn der Zeitstempel gültig ist. Wenn der Zeitstempel ungültig ist, wird das zulässige Zeitfenster über eine Fehlermeldung angezeigt.
- Die Wiederherstellung einer Datenbank mit aktivierter TDE (Transparent Data Encryption) wird nicht unterstützt.
- Gelöschte Datenbanken können derzeit nicht wiederhergestellt werden.

#####   <a name="automated-backups"></a>Automatisierte Sicherungen

- Durch das Umbenennen einer Datenbank werden die automatisierten Sicherungen für diese Datenbank beendet.
- Es wird keine Aufbewahrung erzwungen. Sicherungen werden so lange aufbewahrt, wie Speicherplatz verfügbar ist. 
- Benutzerdatenbanken mit einfachem Wiederherstellungsmodell werden nicht gesichert.
- Die Systemdatenbank `model` wird nicht gesichert, um Konflikte beim Erstellen/Löschen der Datenbank zu verhindern. Die Datenbank wird gesperrt, wenn Administratorvorgänge ausgeführt werden. 
- Derzeit werden nur die Systemdatenbanken `master` und `msdb` gesichert. Es werden nur alle 12 Stunden vollständige Sicherungen ausgeführt.
- Es werden nur `ONLINE`-Benutzerdatenbanken gesichert.
- Standard-RPO (Recovery Point Objective): 5 Minuten. Im aktuellen Release ist keine Änderung möglich.
- Sicherungen werden unbegrenzt aufbewahrt. Löschen Sie Sicherungen manuell, um Speicherplatz freizugeben.

##### <a name="other-limitations"></a>Weitere Einschränkungen
- Die Transaktionsreplikation wird derzeit nicht unterstützt.
- Der Protokollversand wird derzeit blockiert.
- Nur die SQL Server-Authentifizierung wird unterstützt.

## <a name="june-2021"></a>Juni 2021

Dieses Vorschaurelease wird am 13. Juli 2021 veröffentlicht.

### <a name="breaking-changes"></a>Aktuelle Änderungen

#### <a name="new-deployment-templates"></a>Neue Bereitstellungsvorlagen

- Native Kubernetes-Bereitstellungsvorlagen für Datencontroller, Bootstrapper und SQL Managed Instance wurden geändert. Aktualisieren Sie Ihre YAML-Vorlagen. [YAML-Beispieldateien](https://github.com/microsoft/azure_arc/tree/main/arc_data_services/deploy/yaml)

#### <a name="new-azure-cli-extension-for-data-controller-and-azure-arc-enabled-sql-managed-instance"></a>Neue Erweiterung für die Azure-Befehlszeilenschnittstelle für Datencontroller und SQL Managed Instance mit Azure Arc-Unterstützung

Mit diesem Release wird die Erweiterung `arcdata` für die Azure-Befehlszeilenschnittstelle eingeführt. Führen Sie zum Hinzufügen der Erweiterung den folgenden Befehl aus:

```azurecli
az extension add --name arcdata
```

Die Erweiterung unterstützt die Befehlszeileninteraktion mit dem Datencontroller und SQL Managed Instance- und PostgreSQL Hyperscale-Ressourcen.

Ersetzen Sie `azdata arc dc...` durch `az arcdata dc...`, um Ihre Skripts für den Datencontroller zu aktualisieren.

Ersetzen Sie `azdata arc sql mi...` durch `az sql mi-arc...`, um Ihre Skripts für die verwaltete Instanz zu aktualisieren.

Ersetzen Sie für PostgreSQL Hyperscale mit Azure Arc-Unterstützung `azdata arc sql postgres...` durch `az postgres arc-server...`.

Zusätzlich zu den bereits vorhandenen Parametern für `azdata`-Befehle verfügen dieselben Befehle in der Erweiterung `arcdata` für die Azure-Befehlszeilenschnittstelle über einige neue Parameter wie `--k8s-namespace` und `--use-k8s`, die nun erforderlich sind. Mit dem Parameter `--use-k8s` wird unterschieden, wann der Befehl an die Kubernetes-API oder an die ARM-API gesendet werden soll. Derzeit zielen alle Befehle an der Azure-Befehlszeilenschnittstelle für Azure Arc-fähige Datendienste nur auf die Kubernetes-API ab.

Einige der Kurzformen der Parameternamen (z. B. `-cl` für `--core-limit`) wurden entfernt oder geändert. Verwenden Sie die neuen Kurznamen der Parameter oder den langen Namen.

Der Befehl `azdata arc dc export` funktioniert nicht mehr. Verwenden Sie stattdessen `az arcdata dc export`.

#### <a name="required-property-infrastructure"></a>Erforderliche Eigenschaft: `infrastructure`

Die `infrastructure`-Eigenschaft ist eine neue erforderliche Eigenschaft beim Bereitstellen eines Datencontrollers. Passen Sie Ihre YAML-Dateien, azdata-/az-Skripts und ARM-Vorlagen so an, dass dieser Eigenschaftswert berücksichtigt wird. Zulässige Werte sind: `alibaba`, `aws`, `azure`, `gpc`, `onpremises` und `other`.

#### <a name="kibana-login"></a>Kibana-Anmeldung

Das OpenDistro-Sicherheitspaket wurde entfernt. Die Anmeldung bei Kibana erfolgt jetzt über eine generische Eingabeaufforderung im Browser für Benutzername und Kennwort. Weitere Informationen finden Sie später im Thema zum Konfigurieren zusätzlicher Authentifizierungs-/Autorisierungsoptionen.

#### <a name="crd-version-bump-to-v1beta1"></a>Aktualisierung der CRD-Version auf `v1beta1`

Für alle CRDs wurde die Version für dieses Release von `v1alpha1` auf `v1beta1` aktualisiert. Achten Sie darauf, alle CRDs im Rahmen des Deinstallationsprozesses zu löschen, wenn Sie vor dem Release vom Juni 2021 eine Version von Azure Arc-fähigen Datendiensten bereitgestellt haben. Die neuen CRDs, die mit dem Release vom Juni 2021 bereitgestellt werden, weisen die Version „v1beta1“ auf.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance mit Azure Arc-Unterstützung

Der automatisierte Sicherungsdienst ist verfügbar und standardmäßig aktiviert. Achten Sie genau auf die Speicherplatzverfügbarkeit auf dem Sicherungsvolume.

### <a name="whats-new"></a>Neues

Mit diesem Release werden Erweiterungen für die `az`-Befehlszeilenschnittstelle für Azure Arc-fähige Datendienste eingeführt. Weitere Informationen finden Sie oben unter [Breaking Change](#breaking-change).

#### <a name="platform"></a>Plattform

#### <a name="data-controller"></a>Datencontroller

- Die Benutzerfunktionen für die Bereitstellung eines Datencontrollers im direkten Verbindungsmodus über das Azure-Portal wurden optimiert. Nachdem einem Kubernetes-Cluster Azure Arc-Unterstützung hinzugefügt wurde, können Sie den Datencontroller vollständig über das Portal mit dem Assistenten zum Erstellen von Arc-Datencontrollern in einem Vorgang bereitstellen. Bei dieser Bereitstellung werden auch der benutzerdefinierte Standort und die Azure Arc-fähige Datendiensterweiterung (Bootstrapper) erstellt. Sie können den benutzerdefinierten Standort oder die Erweiterung auch vorab erstellen und die Datencontrollerbereitstellung für deren Verwendung konfigurieren.
- Die neue `Infrastructure`-Eigenschaft ist eine erforderliche Eigenschaft für die Bereitstellung eines Arc-Datencontrollers. Diese Eigenschaft ist für Abrechnungszwecke erforderlich. Weitere Informationen werden bei allgemeiner Verfügbarkeit bereitgestellt.
- Verschiedene Verbesserungen an der Benutzerfreundlichkeit des Datencontrollers im Azure-Portal, einschließlich der Möglichkeit, den Bereitstellungsstatus von Ressourcen im Kubernetes-Cluster besser anzuzeigen.
- Der Datencontroller lädt automatisch Protokolle (optional) und jetzt auch Metriken im direkten Verbindungsmodus in Azure hoch.
- Der Überwachungsstapel (Metriken und Protokolldatenbanken/Dashboards) wurde jetzt in eine eigene benutzerdefinierte Ressourcendefinition (CRD) gepackt: `monitors.arcdata.microsoft.com`. Beim Erstellen dieser benutzerdefinierten Ressource werden die Überwachungsstapelpods erstellt. Beim Löschen werden auch die Überwachungsstapelpods gelöscht. Beim Erstellen des Datencontrollers wird automatisch die benutzerdefinierte Überwachungsressource erstellt.
- Der direkte Verbindungsmodus (Vorschau) wird nun in weiteren Regionen unterstützt: „USA, Osten 2“, „USA, Westen 2“, „USA, Süden-Mitte“, „Vereinigtes Königreich, Süden“, „Frankreich, Mitte“, „Asien, Südosten“, „Australien, Osten“.
- Das Diagramm der benutzerdefinierten Standortressource auf dem Blatt „Übersicht“ enthält jetzt Azure Arc-fähige Datendienstressourcen, die dort bereitgestellt wurden.
- Im Azure-Portal wurden Diagnosen und Lösungen für den Datencontroller hinzugefügt.
- Die neue `Observed Generation`-Eigenschaft wurde allen Arc-bezogenen benutzerdefinierten Ressourcen hinzugefügt.
- Der Dienst für die Anmeldeinformationsverwaltung ist jetzt enthalten und übernimmt die automatisierte Verteilung von Zertifikaten an alle Dienste, die vom Datencontroller verwaltet werden.

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>PostgreSQL Hyperscale mit Azure Arc-Unterstützung

- PostgreSQL Hyperscale mit Azure Arc-Unterstützung unterstützt jetzt NFS-Speicher.
- Bereitstellungen von PostgreSQL Hyperscale mit Azure Arc-Unterstützung unterstützen jetzt Zuweisungsstrategien von Kubernetes-Pods zu Knoten mit nodeSelector, nodeAffinity und anti-affinity.
- Sie können jetzt Computeparameter (virtueller Kern und Arbeitsspeicher) pro Rolle (Koordinator oder Worker) beim Bereitstellen einer PostgreSQL Hyperscale-Servergruppe oder nach der Bereitstellung über Azure Data Studio und das Azure-Portal konfigurieren.
- Sie können nun im Azure-Portal die Liste der PostgreSQL-Erweiterungen anzeigen, die in Ihrer PostgreSQL Hyperscale-Servergruppe erstellt wurden.
- Sie können im Azure-Portal Gruppen mit PostgreSQL Hyperscale mit Azure Arc-Unterstützung auf einem Datencontroller löschen, der direkt mit Azure verbunden ist.


#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance mit Azure Arc-Unterstützung

- Automatisierte Sicherungen sind jetzt aktiviert.
- Sie können nun eine Datenbanksicherung als neue Datenbank in derselben SQL-Instanz wiederherstellen, indem Sie eine neue benutzerdefinierte Ressource basierend auf der benutzerdefinierten Ressourcendefinition (CRD) `sqlmanagedinstancerestoretasks.tasks.sql.arcdata.microsoft.com` erstellen. Einzelheiten dazu finden Sie der Dokumentation. Es gibt noch keine Möglichkeit, eine Datenbank über eine Befehlszeilenschnittstelle (`azdata` oder `az`), das Azure-Portal oder Azure Data Studio wiederherzustellen.
- Die Version der Binärdateien der SQL-Engine in diesem Release entspricht den neuesten Binärdateien, die global in Azure SQL Managed Instance (PaaS in Azure) bereitgestellt wurden. Dieser Abgleich ermöglicht die Sicherung/Wiederherstellung zwischen Azure SQL Managed Instance als PaaS und SQL Managed Instance mit Azure Arc-Unterstützung. Weitere Informationen zur Kompatibilität werden später bereitgestellt.
- Sie können jetzt Instanzen von SQL Managed Instance mit Azure Arc-Unterstützung über das Azure-Portal im direkten Verbindungsmodus löschen.
- Sie können jetzt eine Instanz von SQL Managed Instance mit einem Tarif (`GeneralPurpose`, `BusinessCritical`) und einem Lizenztyp (`LicenseIncluded`, `BasePrice` (für AHB-Preise) oder `developer`) konfigurieren. Für die Verwendung von SQL Managed Instance mit Azure Arc-Unterstützung fallen bis zum Datum der allgemeinen Verfügbarkeit (öffentlich angekündigt zum 30. Juli 2021) und bis zum Upgrade auf die Version mit allgemeiner Verfügbarkeit des Diensts keine Gebühren an.
- Die `arcdata`-Erweiterung für Azure Data Studio verfügt jetzt über zusätzliche Parameter, die für die Bereitstellung und Bearbeitung von SQL Managed Instance konfiguriert werden können: Agent aktivieren/deaktivieren, Administratoranmeldegeheimnis, Anmerkungen, Bezeichnungen, Dienstanmerkungen, Dienstbezeichnungen, SSL-/TLS-Konfigurationseinstellungen, Sortierung, Sprache und Ablaufverfolgungsflags.
- Neue Befehle in `azdata`-Tasks und Tasks benutzerdefinierter Ressourcen zum Einrichten verteilter Verfügbarkeitsgruppen. Diese Befehle befinden sich in einer frühen Vorschauphase. Die Dokumentation wird in Kürze bereitgestellt.

   > [!NOTE]
   > Diese Befehle werden zur Erweiterung `az arcdata` migriert.

- `azdata arc dc export` ist veraltet. Sie wird durch `az arcdata dc export` in der Erweiterung `arcdata` für die Azure-Befehlszeilenschnittstelle (`az`) ersetzt. Es wird ein anderer Ansatz für das Exportieren der Daten verwendet. Dabei wird keine direkte Verbindung mehr mit der Datencontroller-API hergestellt. Stattdessen wird ein Exporttask basierend auf der benutzerdefinierten Ressourcendefinition (CRD) `exporttasks.tasks.arcdata.microsoft.com` erstellt. Die benutzerdefinierte Ressource für den erstellten Exporttask steuert einen Workflow zum Generieren eines herunterladbaren Pakets. Die Azure-Befehlszeilenschnittstelle wartet auf den Abschluss dieses Tasks und ruft dann die sichere URL aus dem Status der benutzerdefinierten Ressource des Tasks ab, um das Paket herunterzuladen.
- Unterstützung für die Verwendung von NFS-basierten Speicherklassen.
- Im Azure-Portal wurden Diagnosen und Lösungen für SQL Managed Instance mit Azure Arc-Unterstützung hinzugefügt.

## <a name="may-2021"></a>Mai 2021

Diese Vorschauversion wird am 2. Juni 2021 veröffentlicht.

Als Previewfunktion unterliegt die in diesem Artikel vorgestellte Technologie den [zusätzlichen Nutzungsbedingungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="breaking-change"></a>Unterbrechende Änderung

- Native Bereitstellungsvorlagen von Kubernetes wurden geändert. Aktualisieren Sie Ihre YML-Vorlagen.
    - Aktualisierte Vorlagen für Datencontroller, Bootstrapper und verwaltete SQL-Instanzen: [GitHub microsoft/azure-arc pr 574](https://github.com/microsoft/azure_arc/pull/574)
    - Aktualisierte Vorlagen für PostgreSQL Hyperscale: [GitHub microsoft/azure-arc pr 574](https://github.com/microsoft/azure_arc/pull/574)

### <a name="whats-new"></a>Neues

#### <a name="platform"></a>Plattform

- Erstellen und Löschen von Servergruppen für Datencontroller, SQL Managed Instance und PostgreSQL Hyperscale über das Azure-Portal.
- Validieren Sie Portalaktionen beim Löschen von Azure Arc-Datendiensten. Das Portal warnt beispielsweise, wenn Sie versuchen, den Datencontroller zu löschen, wenn mit ihm SQL Managed Instances bereitgestellt werden.
- Erstellen Sie benutzerdefinierte Konfigurationsprofile, um benutzerdefinierte Einstellungen zu unterstützen, wenn Sie Azure Arc-fähige Datencontroller mithilfe des Azure-Portals bereitstellen.
- Optional können Sie Ihre Protokolle im direkt verbundenen Modus automatisch in den Azure Log Analytics-Arbeitsbereich hochladen.

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>PostgreSQL Hyperscale mit Azure Arc-Unterstützung

Dieses Release führt folgende Funktionen ein:

- Löschen eines PostgreSQL Hyperscale mit Azure Arc-Unterstützung, dessen Datencontroller für den direkt verbundenen Modus konfiguriert wurde, aus dem Azure-Portal.
- Bereitstellung von PostgreSQL Hyperscale mit Azure Arc-Unterstützung über die Azure Database for Postgres-Bereitstellungsseite im Azure-Portal Weitere Informationen finden Sie unter [Auswählen der Azure Database for PostgreSQL-Bereitstellungsoption](https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer) – Microsoft Azure.
- Geben Sie Speicherklassen und Postgres-Erweiterungen an, wenn Sie PostgreSQL Hyperscale mit Azure Arc-Unterstützung über das Azure-Portal bereitstellen.
- Reduzieren Sie die Anzahl der Workerknoten in Ihrer Instanz von PostgreSQL Hyperscale mit Azure Arc-Unterstützung. Sie können diesen Vorgang über die `azdata`-Befehlszeile ausführen. Er wird als Abskalieren bezeichnet, im Gegensatz zum Aufskalieren, bei dem die Anzahl der Workerknoten erhöht wird.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance mit Azure Arc-Unterstützung

- Die neue [Azure CLI-Erweiterung](/cli/azure/azure-cli-extensions-overview) für SQL Managed Instance mit Azure Arc-Unterstützung verfügt über die gleichen Befehle wie `az sql mi-arc <command>`. Alle Befehle für SQL Managed Instance mit Azure Arc-Unterstützung befinden sich unter `az sql mi-arc`. Alle Arc-bezogenen `azdata`-Befehle werden als veraltet gekennzeichnet und in einem zukünftigen Release in die Azure CLI integriert.

   Hinzufügen der Erweiterung:

   ```azurecli
   az extension add --source https://azurearcdatacli.blob.core.windows.net/cli-extensions/arcdata-0.0.1-py2.py3-none-any.whl -y
   az sql mi-arc --help
   ```

- Lösen Sie manuell ein Failover mit Transact-SQL aus. Geben Sie der Reihe nach diese Befehle ein:

   1. Auf der Endpunktverbindung des primären Replikats:

      ```sql
       ALTER AVAILABILITY GROUP current SET (ROLE = SECONDARY);
      ```

   1. Auf der Endpunktverbindung des sekundären Replikats:

      ```sql
      ALTER AVAILABILITY GROUP current SET (ROLE = PRIMARY);
      ```

- Der Transact-SQL-Befehl `BACKUP` wird blockiert, sofern nicht die `COPY_ONLY`-Einstellung verwendet wird. Dies unterstützt die Point-in-Time-Wiederherstellung.

## <a name="april-2021"></a>April 2021

Diese Vorschauversion wird am 29. April 2021 veröffentlicht.

### <a name="whats-new"></a>Neuigkeiten

In diesem Abschnitt werden die neuen Features beschrieben, die für dieses Release eingeführt oder aktiviert wurden.

#### <a name="platform"></a>Plattform

- Direkt verbundene Cluster laden Telemetrieinformationen automatisch in Azure hoch.

####    <a name="azure-arc-enabled-postgresql-hyperscale"></a>PostgreSQL Hyperscale mit Azure Arc-Unterstützung

- PostgreSQL Hyperscale mit Azure Arc-Unterstützung wird jetzt im direkten Verbindungsmodus unterstützt. Sie können jetzt PostgreSQL Hyperscale mit Azure Arc-Unterstützung über den Azure Market Place im Azure-Portal bereitstellen.
- PostgreSQL Hyperscale mit Azure Arc-Unterstützung wird mit der Citus 10.0-Erweiterung ausgeliefert, die spaltenbasierte Tabellenspeicher bietet.
- PostgreSQL Hyperscale mit Azure Arc-Unterstützung unterstützt jetzt die vollständige Benutzer-/Rollenverwaltung.
- PostgreSQL Hyperscale mit Azure Arc-Unterstützung unterstützt jetzt zusätzliche Erweiterungen mit `Tdigest` und `pg_partman`.
- PostgreSQL Hyperscale mit Azure Arc-Unterstützung unterstützt jetzt die Konfiguration von Einstellungen für virtuelle Kerne und Arbeitsspeicher pro Rolle der PostgreSQL-Instanz in der Servergruppe.
- PostgreSQL Hyperscale mit Azure Arc-Unterstützung unterstützt jetzt die Konfiguration von Datenbank-Engine-/Servereinstellungen pro Rolle der PostgreSQL-Instanz in der Servergruppe.

#### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance mit Azure Arc-Unterstützung

- Stellen Sie eine Datenbank auf SQL Managed Instance mit drei Replikaten wieder her, und sie wird automatisch der Verfügbarkeitsgruppe hinzugefügt.
- Stellen Sie eine Verbindung mit einem sekundären schreibgeschützten Endpunkt auf SQL Managed Instance-Instanzen her, die mit drei Replikaten bereitgestellt wurden. Verwenden Sie `azdata arc sql endpoint list`, um den sekundären schreibgeschützten Verbindungsendpunkt anzuzeigen.

## <a name="march-2021"></a>März 2021

Das Release vom März 2021 wurde ursprünglich am 5. April 2021 eingeführt, und die letzten Releasephasen wurden am 9. April 2021 abgeschlossen.

Versionsnummer der Azure Data CLI (`azdata`): 20.3.2. Sie können `azdata` über [Installieren der Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata) installieren.

### <a name="data-controller"></a>Datencontroller

- Stellen Sie den Datencontroller der Azure Arc-fähige Datendienste im direkten Verbindungsmodus über das Portal bereit. Beginnen Sie mit [Bereitstellen des Datencontrollers: Direkter Konnektivitätsmodus – Voraussetzungen](create-data-controller-direct-prerequisites.md).

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>PostgreSQL Hyperscale mit Azure Arc-Unterstützung

Beide benutzerdefinierten Ressourcendefinitionen (Custom Resource Definitions, CRD) für PostgreSQL wurden in einer einzigen CRD konsolidiert. Siehe hierzu die folgende Tabelle.

|Release |CRD |
|-----|-----|
|Februar 2021 und früher| postgresql-11s.arcdata.microsoft.com<br/>postgresql-12s.arcdata.microsoft.com |
|Ab März 2021 | postgresqls.arcdata.microsoft.com

Beim Bereinigen früherer Installationen löschen Sie die vorherigen CRDs. Siehe [Bereinigen früherer Installationen](create-data-controller-using-kubernetes-native-tools.md#cleanup-from-past-installations).

### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance mit Azure Arc-Unterstützung

- Sie können jetzt eine SQL Managed Instance-Instanz über das Azure-Portal im direkt verbundenen Modus erstellen.

- Sie können jetzt eine Datenbank auf SQL Managed Instance mit drei Replikaten wiederherstellen, und sie wird automatisch der Verfügbarkeitsgruppe hinzugefügt.

- Sie können jetzt eine Verbindung mit einem sekundären schreibgeschützten Endpunkt auf SQL Managed Instance-Instanzen herstellen, die mit drei Replikaten bereitgestellt wurden. Verwenden Sie `azdata arc sql endpoint list`, um den sekundären schreibgeschützten Verbindungsendpunkt anzuzeigen.

## <a name="february-2021"></a>Februar 2021

### <a name="new-capabilities-and-features"></a>Neue Funktionen und Features

Versionsnummer der Azure Data CLI (`azdata`): 20.3.1. Sie können `azdata` über [Installieren der Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata) installieren.

Zusätzliche Updates umfassen:

- SQL Managed Instance mit Azure Arc-Unterstützung
   - Hochverfügbarkeit mit Always On-Verfügbarkeitsgruppen

- Azure Data Studio für PostgreSQL Hyperscale mit Azure Arc-Unterstützung:
   - Auf der Übersichtsseite wird der Status der Servergruppe angezeigt (aufgeschlüsselt nach Knoten).
   - Eine neue Eigenschaftenseite zeigt weitere Details zur Servergruppe.
   - Parameter der Postgres-Engine können auf der Seite für **Knotenparameter** konfiguriert werden.

## <a name="january-2021"></a>Januar 2021

### <a name="new-capabilities-and-features"></a>Neue Funktionen und Features

Versionsnummer der Azure Data CLI (`azdata`): 20.3.0. Sie können `azdata` über [Installieren der Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata) installieren.

Zusätzliche Updates umfassen:
- Lokalisiertes Portal für 17 neue Sprachen verfügbar.
- Kleinere Änderungen an Kube-nativen YAML-Dateien.
- Neue Versionen von Grafana und Kibana.
- Probleme mit Python-Umgebungen bei der Verwendung von azdata in Notebooks in Azure Data Studio gelöst.
- Die pg_audit-Erweiterung ist jetzt für die PostgreSQL Hyperscale verfügbar.
- Eine Sicherungs-ID ist bei einer vollständigen Wiederherstellung einer PostgreSQL Hyperscale-Datenbank nicht mehr erforderlich.
- Der Status (Integritätszustand) wird für jede der PostgreSQL-Instanzen gemeldet, die eine Servergruppe bilden.

   In früheren Versionen wurde der Status auf Servergruppenebene aggregiert und nicht auf PostgreSQL-Knotenebene aufgeschlüsselt.

- PostgreSQL-Bereitstellungen berücksichtigen die in create-Befehlen angegebenen Volumengrößenparameter.
- Die Engineversionsparameter werden beim Bearbeiten einer Servergruppe nun berücksichtigt.
- Die Namenskonvention der Pods für PostgreSQL Hyperscale mit Azure Arc-Unterstützung hat sich geändert.

    Sie hat nun folgende Form: `ServergroupName{c, w}-n`. Beispielsweise wird eine Servergruppe mit drei Knoten, einem Koordinatorknoten und zwei Workerknoten wie folgt dargestellt:
   - `Postgres01c-0` (Koordinatorknoten)
   - `Postgres01w-0` (Workerknoten)
   - `Postgres01w-1` (Workerknoten)

## <a name="december-2020"></a>Dezember 2020

### <a name="new-capabilities--features"></a>Neue Funktionen und Features

Versionsnummer der Azure Data CLI (`azdata`): 20.2.5. Sie können `azdata` über [Installieren der Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata) installieren.

Anzeigen von Endpunkten für SQL Managed Instance und PostgreSQL Hyperscale mithilfe der Azure Data-CLI (`azdata`) mit den Befehlen `azdata arc sql endpoint list` -und `azdata arc postgres endpoint list`.

Bearbeiten von Anforderungen und Grenzwerten für SQL Managed Instance-Ressourcen (CPU-Kern und Arbeitsspeicher) mithilfe von Azure Data Studio.

PostgreSQL Hyperscale mit Azure Arc-Unterstützung unterstützt jetzt zusätzlich zur vollständigen Wiederherstellung von Sicherungen die Zeitpunktwiederherstellung für die Versionen 11 und 12 von PostgreSQL. Mithilfe der Point-in-Time-Wiederherstellungsfunktion können Sie ein bestimmtes Datum und eine bestimmte Uhrzeit angeben, auf die wiederhergestellt werden soll.

Die Namenskonvention der Pods für PostgreSQL Hyperscale mit Azure Arc-Unterstützung hat sich geändert. Sie hat nun folgende Form: Servergroupname{r, s}-_n_. Beispielsweise wird eine Servergruppe mit drei Knoten, einem Koordinatorknoten und zwei Workerknoten wie folgt dargestellt:
- `postgres02r-0` (Koordinatorknoten)
- `postgres02s-0` (Workerknoten)
- `postgres02s-1` (Workerknoten)

### <a name="breaking-change"></a>Unterbrechende Änderung

#### <a name="new-resource-provider"></a>Neuer Ressourcenanbieter

Mit diesem Release wird ein aktualisierter [Ressourcenanbieter](../../azure-resource-manager/management/azure-services-resource-providers.md) mit dem Namen `Microsoft.AzureArcData` eingeführt. Bevor Sie diese Funktion verwenden können, müssen Sie diesen Ressourcenanbieter registrieren.

So registrieren Sie diesen Ressourcenanbieter

1. Wählen Sie im Azure-Portal **Abonnements** aus.
2. Auswählen Ihres Abonnements
3. Klicken Sie unter **Einstellungen** auf **Ressourcenanbieter**.
4. Suchen Sie nach `Microsoft.AzureArcData`, und klicken Sie auf **Registrieren**.

Detaillierte Schritte finden Sie unter [Azure-Ressourcenanbieter und -typen](../../azure-resource-manager/management/resource-providers-and-types.md). Diese Änderung entfernt auch alle vorhandenen Azure-Ressourcen, die Sie in das Azure-Portal hochgeladen haben. Um den Ressourcenanbieter verwenden zu können, müssen Sie den Datencontroller aktualisieren und die neueste `azdata`-CLI verwenden.

### <a name="platform-release-notes"></a>Versionshinweise zur Plattform

#### <a name="direct-connectivity-mode"></a>Direkter Konnektivitätsmodus

In dieser Version wird der direkte Konnektivitätsmodus eingeführt. Der Modus für die direkte Konnektivität ermöglicht dem Datencontroller das automatische Hochladen der Nutzungsinformationen in Azure. Im Rahmen des Nutzungsuploads wird die Arc-Datencontrollerressource automatisch im Portal erstellt, wenn Sie nicht bereits per `azdata`-Upload erstellt wurde.

Sie können die direkte Konnektivität angeben, wenn Sie den Datencontroller erstellen. Im folgenden Beispiel wird ein Datencontroller namens `arc` mithilfe von `az arcdata dc create` im direkten Konnektivitätsmodus (`connectivity-mode direct`) erstellt. Bevor Sie das Beispiel ausführen, ersetzen Sie `<subscription id>` durch Ihre Abonnement-ID.

```azurecli
az arcdata dc create --profile-name azure-arc-aks-hci  --k8s-namespace <namespace> --use-k8s --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

## <a name="october-2020"></a>Oktober 2020

Versionsnummer der Azure Data CLI (`azdata`): 20.2.3. Sie können `azdata` über [Installieren der Azure Data CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata) installieren.

### <a name="breaking-changes"></a>Aktuelle Änderungen

Dieses Release enthält folgende Breaking Changes:

* In der benutzerdefinierten PostgreSQL-Ressourcendefinition (Custom Resource Definition, CRD) wird der Begriff `shards` in `workers` umbenannt. Dieser Begriff (`workers`) entspricht dem Namen des Befehlszeilenparameters.

* Von `azdata arc postgres server delete` wird vor dem Löschen einer Postgres-Instanz eine Bestätigung angefordert. Verwenden Sie `--force`, um die Aufforderung zu überspringen.

### <a name="additional-changes"></a>Weitere Änderungen

* `azdata arc postgres server create` wurde ein neuer optionaler Parameter namens `--volume-claim mounts` hinzugefügt. Der Wert ist eine durch Trennzeichen getrennte Liste mit Volumeanspruchseinbindungen. Bei einer Volumeanspruchseinbindung handelt es sich um ein Paar aus Volumetyp und PVC-Name. Derzeit wird ausschließlich der Volumetyp `backup` unterstützt. Wenn der Volumetyp in PostgreSQL `backup` lautet, wird das PVC in `/mnt/db-backups` eingebunden. Dies ermöglicht die gemeinsame Nutzung von Sicherungen zwischen PostgresSQL-Instanzen, sodass die Sicherung einer PostgresSQL-Instanz in einer anderen Instanz wiederhergestellt werden kann.

* Neue Kurznamen für benutzerdefinierte PostgresSQL-Ressourcendefinitionen:
  * `pg11`
  * `pg12`
* Telemetrieupload bietet dem Benutzer Folgendes:
   * Anzahl der in Azure hochgeladenenen Punkte oder
   * Aufforderung zum erneuten Versuch, falls keine Daten in Azure geladen wurden.
* `az arcdata dc debug copy-logs` liest nun auch aus dem Ordner `/var/opt/controller/log` und sammelt PostgreSQL-Engineprotokolle unter Linux.
*   Anzeige eines Funktionsindikators beim Erstellen und Wiederherstellen einer Sicherung mit PostgreSQL Hyperscale.
* `azdata arc postrgres backup list` enthält jetzt Informationen zur Sicherungsgröße.
* Die Administratornamenseigenschaft von SQL Managed Instance wurde der rechten Spalte des Übersichtsblatts im Azure-Portal hinzugefügt.
* Azure Data Studio unterstützt das Konfigurieren der Anzahl von Workerknoten sowie von virtuellen Kernen und Arbeitsspeichereinstellungen für PostgreSQL Hyperscale.
* In der Vorschauversion werden Sicherungen/Wiederherstellungen für die Postgres-Versionen 11 und 12 unterstützt.

## <a name="september-2020"></a>September 2020

Azure Arc-fähige Datendienste sind für die öffentliche Vorschau freigegeben. Mit Azure Arc-fähigen Datendiensten können Sie Datendienste an jedem Ort verwalten.

- Verwaltete SQL-Instanz
- PostgreSQL Hyperscale

Anweisungen finden Sie unter [Was sind Azure Arc-fähige Datendienste?](overview.md)

## <a name="next-steps"></a>Nächste Schritte

> **Möchten Sie es selbst ausprobieren?**
> Mit dem [Azure Arc-Schnelleinstieg](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) für AKS, AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) oder auf einer Azure-VM können Sie schnell die ersten Schritte ausführen.

- [Installieren der Clienttools](install-client-tools.md)
- [Erstellen des Azure Arc-Datencontrollers](create-data-controller.md) (erfordert zuvor die Installation der Clienttools)
- [Erstellen einer Azure SQL Managed Instance-Instanz in Azure Arc](create-sql-managed-instance.md) (erfordert zuvor die Erstellung eines Azure Arc-Datencontrollers)
- [Erstellen einer Azure Database for PostgreSQL Hyperscale-Servergruppe in Azure Arc](create-postgresql-hyperscale-server-group.md) (erfordert zuvor die Erstellung eines Azure Arc-Datencontrollers)
- [Ressourcenanbieter für Azure-Dienste](../../azure-resource-manager/management/azure-services-resource-providers.md)
