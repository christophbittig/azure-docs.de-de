---
title: 'HIPAA HITRUST 9.2-Blaupausenbeispiel: Übersicht'
description: Enthält eine Übersicht über das HIPAA HITRUST 9.2-Blaupausenbeispiel. Dieses Blaupausenbeispiel unterstützt Kunden bei der Bewertung spezifischer HIPAA HITRUST 9.2-Steuerungen.
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: f566ea3fdc1d9922a99e375fab517203da59d526
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128658262"
---
# <a name="hipaa-hitrust-92-blueprint-sample"></a>HIPAA HITRUST 9.2-Blaupausenbeispiel

Im Blaupausenbeispiel „HIPAA HITRUST 9.2“ werden mithilfe von [Azure Policy](../../policy/overview.md) Schutzmaßnahmen für Governance bereitgestellt, die zum Bewerten bestimmter HIPAA HITRUST 9.2-Steuerungen hilfreich sind.
Diese Blaupause hilft Kunden bei der Bereitstellung eines Kernsatzes von Richtlinien für jede in Azure bereitgestellte Architektur, für die HIPAA HITRUST 9.2-Steuerungen implementiert werden müssen.

## <a name="control-mapping"></a>Steuerungszuordnung

In der [Azure Policy-Steuerungszuordnung](../../policy/samples/hipaa-hitrust-9-2.md) finden Sie Details zu den in dieser Blaupause enthaltenen Richtliniendefinitionen. Darüber hinaus erfahren Sie, wie diese Richtlinien den **Compliancebereichen** und **Steuerungen** in HIPAA HITRUST 9.2 entsprechen. Bei der Zuweisung zu einer Architektur werden Ressourcen von Azure Policy auf Nichtkonformität mit den zugewiesenen Richtliniendefinitionen überprüft. Weitere Informationen finden Sie unter [Was ist Azure Policy?](../../policy/overview.md).

## <a name="deploy"></a>Bereitstellen

Führen Sie zum Bereitstellen des Blaupausenbeispiels „HIPAA HITRUST 9.2“ von Azure Blueprints die folgenden Schritte aus:

> [!div class="checklist"]
> - Erstellen einer neuen Blaupause mithilfe des Beispiels
> - Markieren Ihrer Kopie des Beispiels als **Veröffentlicht**
> - Zuweisen Ihrer Kopie der Blaupause zu einem vorhandenen Abonnement

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.

### <a name="create-blueprint-from-sample"></a>Erstellen einer Blaupause mithilfe des Beispiels

Implementieren Sie zuerst das Blaupausenbeispiel, indem Sie mithilfe des Beispiels eine neue Blaupause in Ihrer Umgebung erstellen.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Klicken Sie links auf der Seite **Erste Schritte** unter _Blaupause erstellen_ auf die Schaltfläche **Erstellen**.

1. Navigieren Sie unter _Weitere Beispiele_ zum Blaupausenbeispiel **HIPAA HITRUST**, und wählen Sie **Dieses Beispiel verwenden** aus.

1. Geben Sie die _Grundlagen_ des Blaupausenbeispiels ein:

   - **Name der Blaupause**: Geben Sie einen Namen für Ihre Kopie des Blaupausenbeispiels „HIPAA HITRUST 9.2“ ein.
   - **Definitionsspeicherort**: Klicken Sie auf die Schaltfläche mit den Auslassungspunkten, und wählen Sie die Verwaltungsgruppe aus, in der Sie Ihre Kopie des Beispiels speichern möchten.

1. Wählen Sie oben auf der Seite die Registerkarte _Artefakte_ oder unten auf der Seite die Option **Weiter: Artefakte** aus.

1. Überprüfen Sie die Liste der Artefakte, aus denen das Blaupausenbeispiel besteht. Viele der Artefakte enthalten Parameter, die Sie später definieren. Wählen Sie **Entwurf speichern** aus, nachdem Sie das Blaupausenbeispiel überprüft haben.

### <a name="publish-the-sample-copy"></a>Veröffentlichen der Kopie des Beispiels

Ihre Kopie des Blaupausenbeispiels wurde jetzt in Ihrer Umgebung erstellt. Sie wird im Modus **Entwurf** erstellt und muss **veröffentlicht** werden, bevor sie zugewiesen und bereitgestellt werden kann. Die Kopie des Blaupausenbeispiels kann an Ihre Umgebung und Ihre Anforderungen angepasst werden. Durch diese Änderungen ist sie aber unter Umständen nicht mehr mit den Steuerungen von HIPAA HITRUST 9.2 konform.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Verwenden Sie die Filter, um Ihre Kopie des Blaupausenbeispiels zu suchen, und wählen Sie es aus.

1. Wählen Sie oben auf der Seite die Option **Blaupause veröffentlichen** aus. Geben Sie auf der neuen Seite rechts für Ihre Kopie des Blaupausenbeispiels eine **Version** an. Diese Eigenschaft ist hilfreich, wenn Sie später Änderungen vornehmen. Geben Sie **Änderungshinweise** an, z. B. „Erste Version aus dem Blaupausenbeispiel „HIPAA HITRUST 9.2“ veröffentlicht“. Wählen Sie dann unten auf der Seite **Veröffentlichen** aus.

### <a name="assign-the-sample-copy"></a>Zuweisen der Kopie des Beispiels

Nachdem die Kopie des Blaupausenbeispiels erfolgreich **veröffentlicht** wurde, kann sie einem Abonnement innerhalb der Verwaltungsgruppe, in der sie gespeichert wurde, zugewiesen werden. In diesem Schritt werden Parameter angegeben, damit jede Bereitstellung der Kopie des Blaupausenbeispiels eindeutig ist.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Verwenden Sie die Filter, um Ihre Kopie des Blaupausenbeispiels zu suchen, und wählen Sie es aus.

1. Wählen Sie oben auf der Seite mit der Blaupausendefinition die Option **Blaupause zuweisen** aus.

1. Geben Sie die Parameterwerte für die Blaupausenzuweisung an:

   - Grundlagen

     - **Abonnements**: Wählen Sie mindestens eines der Abonnements in der Verwaltungsgruppe aus, in der Sie die Kopie des Blaupausenbeispiels gespeichert haben. Wenn Sie mehrere Abonnements auswählen, wird für jedes Abonnement eine Zuweisung mit den eingegebenen Parametern erstellt.
     - **Zuweisungsname:** Der Name wird basierend auf dem Namen der Blaupause vorab ausgefüllt.
       Sie können ihn nach Bedarf ändern oder unverändert übernehmen.
     - **Standort**: Wählen Sie eine Region aus, in der die verwaltete Identität erstellt werden soll. Azure Blueprint verwendet diese verwaltete Identität zum Bereitstellen aller Artefakte in der zugewiesenen Blaupause. Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Version der Blaupausendefinition:** Wählen Sie eine **veröffentlichte** Version Ihrer Kopie des Blaupausenbeispiels aus.

   - Zuweisung sperren

     Wählen Sie die Blaupausensperreinstellung für die Umgebung aus. Weitere Informationen finden Sie unter [Grundlegendes zur Ressourcensperre in Azure Blueprint](../concepts/resource-locking.md).

   - Verwaltete Identität

     Behalten Sie die Standardeinstellung _Vom System zugewiesen_ für die verwaltete Identität bei.

   - Artefaktparameter

     Die in diesem Abschnitt definierten Parameter gelten für das Artefakt, unter dem sie definiert werden. Diese Parameter sind [dynamische Parameter](../concepts/parameters.md#dynamic-parameters), da sie während der Zuweisung der Blaupause definiert werden. Eine vollständige Liste der Artefaktparameter und die zugehörigen Beschreibungen finden Sie in der [Tabelle der Artefaktparameter](#artifact-parameters-table).

1. Nachdem Sie alle Parameter eingegeben haben, wählen Sie unten auf der Seite die Option **Zuweisen** aus. Die Blaupausenzuweisung wird erstellt, und die Artefaktbereitstellung wird gestartet. Die Bereitstellung dauert ungefähr eine Stunde. Um den Status der Bereitstellung zu überprüfen, öffnen Sie die Blaupausenzuweisung.

> [!WARNING]
> Der Azure Blueprints-Dienst und die integrierten Blaupausenbeispiele sind **kostenlos**. Azure-Ressourcen werden [nach Produkt abgerechnet](https://azure.microsoft.com/pricing/). Verwenden Sie den [Preisrechner](https://azure.microsoft.com/pricing/calculator/) zum Schätzen der Kosten für die Ausführung der Ressourcen, die mit diesem Blaupausenbeispiel bereitgestellt werden.

### <a name="artifact-parameters-table"></a>Tabelle der Artefaktparameter

Die folgende Tabelle enthält eine Aufstellung der Parameter des Blaupausenartefakts:

|Artefaktname |Parametername |BESCHREIBUNG |
|---|---|---|
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Zugriff über Endpunkt mit Internetzugriff sollte eingeschränkt werden |Hiermit wird die Überwachung zu freizügiger NSG-Regeln für eingehenden Datenverkehr aktiviert oder deaktiviert. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Konten: Gastkontenstatus |Hiermit wird angegeben, ob das lokale Gastkonto deaktiviert ist. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Die adaptive Anwendungssteuerung sollte auf virtuellen Computern aktiviert werden. |Hiermit wird die Überwachung einer Anwendungswhitelist in Azure Security Center aktiviert oder deaktiviert. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Gleichzeitige Verbindungen mit dem Internet oder einer Windows-Domäne zulassen |Hiermit wird angegeben, ob Computer daran gehindert werden sollen, zeitgleich eine domänenbasierte Netzwerkverbindung und eine nicht domänenbasierte Netzwerkverbindung herzustellen. Der Wert 0 lässt gleichzeitige Verbindungen zu, und beim Wert 1 werden gleichzeitige Verbindungen blockiert. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Zugriff auf API-App V2 nur über HTTPS gestatten |Hiermit wird die Überwachung der Verwendung von HTTPS in API-App V2 aktiviert oder deaktiviert. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Anwendungsnamen (Platzhalterzeichen werden unterstützt) |Eine durch Semikolons getrennte Liste mit den Namen der Anwendungen, die installiert werden müssen. Beispiel: 'Microsoft SQL Server 2014 (64-Bit); Microsoft Visual Studio Code' oder 'Microsoft SQL Server 2014*' (zur Abdeckung von Anwendungen, deren Name mit „Microsoft SQL Server 2014“ beginnt) |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Prozessbeendung überwachen |Hiermit wird angegeben, ob beim Beenden eines Prozesses Überwachungsereignisse generiert werden. Empfohlen zum Überwachen der Beendigung kritischer Prozesse. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Nicht eingeschränkten Netzwerkzugriff auf Speicherkonten überwachen |Hiermit wird die Überwachung des Netzwerkzugriffs auf Speicherkonten aktiviert oder deaktiviert. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Überwachung: System sofort herunterfahren, wenn Sicherheitsüberprüfungen nicht protokolliert werden können |Hiermit wird überwacht, ob das System heruntergefahren wird, wenn sicherheitsrelevante Ereignisse nicht protokolliert werden können. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Zertifikatfingerabdrücke |Eine durch Semikolons getrennte Liste mit Zertifikatfingerabdrücken, die im Zertifikatspeicher für vertrauenswürdige Stammzertifikate vorhanden sein müssen (Cert:\LocalMachine\Root). Beispiel: THUMBPRINT1;THUMBPRINT2;THUMBPRINT3 |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Diagnoseprotokolle in Batch-Konten sollten aktiviert sein. |Hiermit wird die Überwachung von Diagnoseprotokollen in Batch-Konten aktiviert oder deaktiviert. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |In Event Hub sollten Diagnoseprotokolle aktiviert sein |Hiermit wird die Überwachung von Diagnoseprotokollen in Event Hub-Konten aktiviert oder deaktiviert. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |In den Suchdiensten müssen Diagnoseprotokolle aktiviert sein |Hiermit wird die Überwachung von Diagnoseprotokollen im Azure Search-Dienst aktiviert oder deaktiviert. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |In Virtual Machine Scale Sets sollten Diagnoseprotokolle aktiviert sein. |Hiermit wird die Überwachung von Diagnoseprotokollen in Service Fabric aktiviert oder deaktiviert. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Die Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden. |Hiermit wird die Überwachung der VM-Datenträgerverschlüsselung aktiviert oder deaktiviert. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Unsichere Gastanmeldungen aktivieren |Hiermit wird angegeben, ob der SMB-Client unsichere Gastanmeldungen bei einem SMB-Server zulässt. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Auf VMs muss die Just-in-Time-Netzwerkzugriffssteuerung angewendet werden. |Hiermit wird die Überwachung des Just-In-Time-Netzwerkzugriffs aktiviert oder deaktiviert. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Verwaltungsports sollten auf Ihren virtuellen Computern geschlossen werden. |Hiermit wird die Überwachung offener Verwaltungsports auf VMs aktiviert oder deaktiviert. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |MFA sollte für Konten mit Schreibrechten für Ihr Abonnement aktiviert werden |Überwachung von MFA für Konten mit Schreibberechtigungen im Abonnement aktivieren oder deaktivieren |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein. |Überwachung von MFA für Konten mit Besitzerberechtigungen im Abonnement aktivieren oder deaktivieren |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Netzwerkzugriff: Registrierungspfade, auf die von anderen Computern aus zugegriffen werden kann |Hiermit wird angegeben, auf welche Registrierungspfade über das Netzwerk zugegriffen werden kann – unabhängig von den Benutzern oder Gruppen, die in der Zugriffssteuerungsliste (ACL) des Registrierungsschlüssels `winreg` aufgeführt sind. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Netzwerkzugriff: Remote zugängliche Registrierungspfade und Unterpfade |Hiermit wird angegeben, auf welche Registrierungspfade und Unterpfade über das Netzwerk zugegriffen werden kann – unabhängig von den Benutzern oder Gruppen, die in der Zugriffssteuerungsliste (ACL) des Registrierungsschlüssels `winreg` aufgeführt sind. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Netzwerkzugriff: Freigaben, auf die anonym zugegriffen werden kann |Hiermit wird angegeben, auf welche Netzwerkfreigaben anonyme Benutzer zugreifen können. Die Standardkonfiguration für diese Richtlinieneinstellung hat nur eine geringe Auswirkung, weil alle Benutzer authentifiziert werden müssen, bevor sie auf freigegebene Ressourcen auf dem Server zugreifen können. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Wiederherstellungskonsole: Diskettenkopiervorgänge und Zugriff auf alle Laufwerke und Ordner zulassen |Hiermit wird angegeben, ob der SET-Befehl der Wiederherstellungskonsole verfügbar gemacht werden soll, der die Festlegung von Umgebungsvariablen für die Wiederherstellungskonsole ermöglicht. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Remotedebuggen für API-App deaktivieren |Hiermit aktivieren oder deaktivieren Sie die Überwachung des Remotedebuggens für die API-App. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Remotedebuggen muss für Webanwendung deaktiviert werden |Hiermit aktivieren oder deaktivieren Sie die Überwachung des Remotedebuggens für die Web-App. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Erforderliche Aufbewahrung (in Tagen) für Protokolle in Batch-Konten |Die erforderliche Aufbewahrungsdauer für Diagnoseprotokolle in Tagen |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Erforderliche Aufbewahrung (in Tagen) von Protokollen im Azure Search-Dienst |Die erforderliche Aufbewahrungsdauer für Diagnoseprotokolle in Tagen |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Erforderliche Aufbewahrung (in Tagen) von Protokollen in Event Hub-Konten |Die erforderliche Aufbewahrungsdauer für Diagnoseprotokolle in Tagen |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Ressourcengruppenname für das Speicherkonto (muss vorhanden sein) zum Bereitstellen von Diagnoseeinstellungen für Netzwerksicherheitsgruppen |Die Ressourcengruppe, in der das Speicherkonto erstellt wird Diese Ressourcengruppe muss bereits vorhanden sein. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Die rollenbasierte Zugriffssteuerung (Role-Based Access Control, RBAC) sollte für Kubernetes Service verwendet werden. |Hiermit wird die Überwachung von Kubernetes-Diensten ohne aktivierte rollenbasierte Zugriffssteuerung (RBAC) aktiviert oder deaktiviert. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Die TDE-Schutzvorrichtung verwalteter SQL-Instanzen sollte mit Ihrem eigenen Schlüssel verschlüsselt werden. |Hiermit wird die Überwachung des TDE-Schutzes (Transparent Data Encryption) mit Unterstützung eigener Schlüssel aktiviert oder deaktiviert. TDE mit Unterstützung eines eigenen Schlüssels bietet mehr Transparenz und eine bessere Kontrolle über den TDE-Schutz, erhöht die Sicherheit durch einen HSM-basierten externen Dienst und fördert die Aufgabentrennung. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Die TDE-Schutzvorrichtung in SQL Server sollte mit Ihrem eigenen Schlüssel verschlüsselt werden. |Hiermit wird die Überwachung des TDE-Schutzes (Transparent Data Encryption) mit Unterstützung eigener Schlüssel aktiviert oder deaktiviert. TDE mit Unterstützung eines eigenen Schlüssels bietet mehr Transparenz und eine bessere Kontrolle über den TDE-Schutz, erhöht die Sicherheit durch einen HSM-basierten externen Dienst und fördert die Aufgabentrennung. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Speicherkontopräfix für regionales Speicherkonto zum Bereitstellen von Diagnoseeinstellungen für Netzwerksicherheitsgruppen |Dieses Präfix wird mit dem Standort der Netzwerksicherheitsgruppe kombiniert, um den Namen für das erstellte Speicherkonto zu bilden. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Systemupdates für VM-Skalierungsgruppen sollten installiert werden. |Hiermit aktivieren oder deaktivieren Sie die Berichterstellung für Systemupdates für VM-Skalierungsgruppen. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Systemupdates für VM-Skalierungsgruppen sollten installiert werden. |Hiermit aktivieren oder deaktivieren Sie die Berichterstellung für Systemupdates für VM-Skalierungsgruppen. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Multicastnamensauflösung deaktivieren |Hiermit wird angegeben, ob LLMNR aktiviert ist (ein sekundäres Protokoll für die Namensauflösung, das Multicastübertragungen über eine lokale Subnetzverbindung in einem einzigen Subnetz durchführt). |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |VMs sollten zu neuen Azure Resource Manager-Ressourcen migriert werden |Hiermit wird die Überwachung von klassischen Compute-VMs aktiviert oder deaktiviert. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Sicherheitsrisiken in der Sicherheitskonfiguration von VM-Skalierungsgruppen sollten beseitigt werden. |Hiermit aktivieren oder deaktivieren Sie die Überwachung von Betriebssystem-Sicherheitsrisiken für VM-Skalierungsgruppen. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Sicherheitsrisiken sollten durch eine Lösung zur Sicherheitsrisikobewertung beseitigt werden. |Hiermit wird die Erkennung von VM-Sicherheitsrisiken durch eine Lösung zur Sicherheitsrisikobewertung aktiviert oder deaktiviert. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Bewertung von Sicherheitsrisiken für verwaltete SQL-Instanzen aktivieren |Hiermit werden verwaltete SQL Server-Instanzen überwacht, für die keine regelmäßige Sicherheitsrisikobewertung durchgeführt wird. Die Sicherheitsrisikobewertung kann Sie dabei unterstützen, potenzielle Sicherheitsrisiken für Datenbanken zu erkennen, nachzuverfolgen und zu beheben. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Windows-Firewall (Domäne): Lokale Firewallregeln anwenden |Hiermit wird angegeben, ob lokale Administratoren lokale Firewallregeln erstellen dürfen, die gemeinsam mit den über die Gruppenrichtlinie für das Domänenprofil konfigurierten Firewallregeln angewendet werden. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Windows-Firewall (Domäne): Verhalten für ausgehende Verbindungen |Hiermit wird das Verhalten für ausgehende Verbindungen für das Domänenprofil angegeben, wenn die Verbindung mit keiner Firewallregel für ausgehenden Datenverkehr übereinstimmt. Beim Standardwert 0 sind Verbindungen zugelassen, und beim Wert 1 werden Verbindungen blockiert. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Windows-Firewall (Domäne): Verhalten für ausgehende Verbindungen |Hiermit wird das Verhalten für ausgehende Verbindungen für das Domänenprofil angegeben, wenn die Verbindung mit keiner Firewallregel für ausgehenden Datenverkehr übereinstimmt. Beim Standardwert 0 sind Verbindungen zugelassen, und beim Wert 1 werden Verbindungen blockiert. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Windows-Firewall (Domäne): Benachrichtigungen anzeigen |Hiermit wird angegeben, ob „Windows-Firewall mit erweiterter Sicherheit“ Benutzer benachrichtigt, wenn für ein Programm eingehende Verbindungen blockiert wurden (Domänenprofil). |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Windows-Firewall (Domäne): Profileinstellungen verwenden |Hiermit wird angegeben, ob „Windows-Firewall mit erweiterter Sicherheit“ mithilfe der Einstellungen für das Domänenprofil den Netzwerkdatenverkehr filtert. Wenn diese Einstellung deaktiviert ist, verwendet „Windows-Firewall mit erweiterter Sicherheit“ keine der Firewallregeln oder Verbindungssicherheitsregeln für dieses Profil. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Windows-Firewall (privat): Lokale Verbindungssicherheitsregeln anwenden |Hiermit wird angegeben, ob lokale Administratoren Verbindungssicherheitsregeln erstellen dürfen, die gemeinsam mit den über die Gruppenrichtlinie für das private Profil konfigurierten Verbindungssicherheitsregeln angewendet werden. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Windows-Firewall (privat): Lokale Firewallregeln anwenden |Hiermit wird angegeben, ob lokale Administratoren lokale Firewallregeln erstellen dürfen, die gemeinsam mit den über die Gruppenrichtlinie für das private Profil konfigurierten Firewallregeln angewendet werden. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Windows-Firewall (privat): Verhalten für ausgehende Verbindungen |Hiermit wird das Verhalten für ausgehende Verbindungen für das private Profil angegeben, wenn die Verbindung mit keiner Firewallregel für ausgehenden Datenverkehr übereinstimmt. Beim Standardwert 0 sind Verbindungen zugelassen, und beim Wert 1 werden Verbindungen blockiert. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Windows-Firewall (privat): Benachrichtigungen anzeigen |Hiermit wird angegeben, ob „Windows-Firewall mit erweiterter Sicherheit“ Benutzer benachrichtigt, wenn für ein Programm eingehende Verbindungen blockiert wurden (privates Profil). |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Windows-Firewall (privat): Profileinstellungen verwenden |Hiermit wird angegeben, ob „Windows-Firewall mit erweiterter Sicherheit“ mithilfe der Einstellungen für das private Profil den Netzwerkdatenverkehr filtert. Wenn diese Einstellung deaktiviert ist, verwendet „Windows-Firewall mit erweiterter Sicherheit“ keine der Firewallregeln oder Verbindungssicherheitsregeln für dieses Profil. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Windows-Firewall (öffentlich): Lokale Verbindungssicherheitsregeln anwenden |Hiermit wird angegeben, ob lokale Administratoren Verbindungssicherheitsregeln erstellen dürfen, die gemeinsam mit den über die Gruppenrichtlinie für das öffentliche Profil konfigurierten Verbindungssicherheitsregeln angewendet werden. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Windows-Firewall (öffentlich): Lokale Firewallregeln anwenden |Hiermit wird angegeben, ob lokale Administratoren lokale Firewallregeln erstellen dürfen, die gemeinsam mit den über die Gruppenrichtlinie für das öffentliche Profil konfigurierten Firewallregeln angewendet werden. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Windows-Firewall (öffentlich): Verhalten für ausgehende Verbindungen |Hiermit wird das Verhalten für ausgehende Verbindungen für das öffentliche Profil angegeben, wenn die Verbindung mit keiner Firewallregel für ausgehenden Datenverkehr übereinstimmt. Beim Standardwert 0 sind Verbindungen zugelassen, und beim Wert 1 werden Verbindungen blockiert. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Windows-Firewall (öffentlich): Benachrichtigungen anzeigen |Hiermit wird angegeben, ob „Windows-Firewall mit erweiterter Sicherheit“ Benutzer benachrichtigt, wenn für ein Programm eingehende Verbindungen blockiert wurden (öffentliches Profil). |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Windows-Firewall (öffentlich): Profileinstellungen verwenden |Hiermit wird angegeben, ob „Windows-Firewall mit erweiterter Sicherheit“ mithilfe der Einstellungen für das öffentliche Profil den Netzwerkdatenverkehr filtert. Wenn diese Einstellung deaktiviert ist, verwendet „Windows-Firewall mit erweiterter Sicherheit“ keine der Firewallregeln oder Verbindungssicherheitsregeln für dieses Profil. |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Windows-Firewall: Domäne: Unicastantwort zulassen |Hiermit wird angegeben, ob „Windows-Firewall mit erweiterter Sicherheit“ dem lokalen Computer das Empfangen von Unicastantworten auf ausgehende Multicast- oder Broadcastnachrichten gestattet (Domänenprofil). |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Windows-Firewall: Privat: Unicastantwort zulassen |Hiermit wird angegeben, ob „Windows-Firewall mit erweiterter Sicherheit“ dem lokalen Computer das Empfangen von Unicastantworten auf ausgehende Multicast- oder Broadcastnachrichten gestattet (privates Profil). |
|HITRUST/HIPAA-Kontrollen überwachen und spezifische VM-Erweiterungen zur Unterstützung von Überwachungsanforderungen bereitstellen |Windows-Firewall: Öffentlich: Unicastantwort zulassen |Hiermit wird angegeben, ob „Windows-Firewall mit erweiterter Sicherheit“ dem lokalen Computer das Empfangen von Unicastantworten auf ausgehende Multicast- oder Broadcastnachrichten gestattet (öffentliches Profil). |

## <a name="next-steps"></a>Nächste Schritte

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../how-to/update-existing-assignments.md).
