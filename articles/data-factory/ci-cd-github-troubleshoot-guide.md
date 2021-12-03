---
title: Beheben von CI/CD-, Azure DevOps- und GitHub-Problemen
titleSuffix: Azure Data Factory & Azure Synapse
description: Wenden Sie verschiedene Methoden an, um CI/CD-Probleme mit in Azure Data Factory und Synapse Analytics zu beheben.
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.subservice: ci-cd
ms.custom: synapse
ms.topic: troubleshooting
ms.date: 11/09/2021
ms.openlocfilehash: d3d792f6b51dc24b17d86d6a6fecc83697445a5a
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157705"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-azure-data-factory-and-synapse-analytics"></a>Beheben von CI/CD-, Azure DevOps- und GitHub-Problemen in Azure Data Factory und Synapse Analytics 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel beschäftigen wir uns mit den gängigen Methoden zur Fehlerbehebung bei der Continuous Integration-Continuous Deployment (CI/CD), der Azure DevOps und den GitHub-Problemen in Azure Data Factory und Synapse Analytics.

Wenn Sie Fragen oder Probleme bei der Quellcodeverwaltung oder DevOps-Verfahren haben, können die folgenden Artikel nützlich sein:

- Unter [Quellcodeverwaltung](source-control.md) erfahren Sie, wie die Quellcodeverwaltung im Dienst funktioniert. 
- Unter [Continuous Integration und Continuous Delivery](continuous-integration-delivery.md) erfahren Sie, wie CI/CD für DevOps im Dienst funktioniert.

## <a name="common-errors-and-messages"></a>Häufige Fehler und Meldungen

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>Fehler bei der Verbindungsherstellung mit Git-Repository aufgrund eines anderen Mandanten

#### <a name="issue"></a>Problem

Zuweilen können Authentifizierungsprobleme auftreten, beispielsweise der HTTP-Statusfehler 401. Das Ganze kann ziemlich kompliziert werden – insbesondere dann, wenn Sie über mehrere Mandanten mit Gastkonto verfügen.

#### <a name="cause"></a>Ursache

Folgendes lässt sich beobachten: Das Token wurde vom ursprünglichen Mandanten abgerufen, aber der Dienst befindet sich im Gastmandanten und versucht, das Token zum Zugreifen auf DevOps im Gastmandanten zu verwenden. Dies entspricht nicht dem erwarteten Verhalten.

#### <a name="recommendation"></a>Empfehlung

Sie sollten das vom Gastmandanten ausgestellte Token verwenden. Beispielsweise müssen Sie Ihrem Gastmandanten und Ihrem DevOps dasselbe Azure Active Directory zuweisen, damit es das Token-Verhalten korrekt einstellen und den korrekten Mandanten verwenden kann.

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>Vorlagenparameter in der Parameterdatei sind nicht gültig

#### <a name="issue"></a>Problem

Wenn Sie einen Trigger im Entwicklungsbranch löschen, der bereits mit **derselben** Konfiguration (z. B. Häufigkeit und Intervall) im Test- oder Produktionsbranch verfügbar ist, kann die Releasepipeline erfolgreich bereitgestellt werden, und der entsprechende Trigger wird in den jeweiligen Umgebungen gelöscht. Wenn jedoch **unterschiedliche** Konfigurationen (z. B. für Häufigkeit und Intervall) für Trigger in Test- und Produktionsumgebungen vorliegen und Sie diesen Trigger in der Entwicklungsumgebung löschen, tritt bei der Bereitstellung ein Fehler auf.

#### <a name="cause"></a>Ursache

Bei der CI/CD-Pipeline tritt der folgende Fehler auf:

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>Empfehlung

Der Fehler tritt auf, weil wir oft einen Trigger löschen, der parametriert ist, daher werden die Parameter nicht in der Azure Resource Manager (ARM) Vorlage verfügbar sein (weil der Trigger nicht mehr existiert). Da sich die Parameter nicht mehr in der ARM-Vorlage befinden, müssen die überschriebenen Parameter in der DevOps-Pipeline aktualisiert werden. Andernfalls müssen Parameter bei jeder Änderung in der ARM-Vorlage die überschriebenen Parameter in der DevOps-Pipeline aktualisieren (im Bereitstellungstask).

### <a name="updating-property-type-is-not-supported"></a>Aktualisieren des Eigenschaftstyps wird nicht unterstützt

#### <a name="issue"></a>Problem

Bei der CI/CD-Releasepipeline tritt der folgende Fehler auf:

```output
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
```

#### <a name="cause"></a>Ursache

Dieser Fehler ist auf eine Integration Runtime mit dem gleichen Namen in der Zieldienstinstanz, jedoch mit einem anderen Typ zurückzuführen. Die Integration Runtime muss bei der Bereitstellung vom gleichen Typ sein.

#### <a name="recommendation"></a>Empfehlung

- Beachten Sie die [bewährten Methoden für CI/CD](continuous-integration-delivery.md#best-practices-for-cicd).

- Integration Runtimes ändern sich nicht häufig und sind in allen CI/CD-Phasen gleich. Daher erwartet der Dienst, dass Sie in allen Phasen von CI/CD eine Integration Runtime mit demselben Namen und demselben Typ verwenden. Wenn sich die Namen, Typen und Eigenschaften unterscheiden, sollten Sie sicherstellen, dass Sie die Quell- und Zielkonfigurationen der Integration Runtime abgleichen und erst dann die Releasepipeline bereitstellen.

- Wenn Sie Integration Runtimes über alle Stufen hinweg freigeben möchten, können Sie eine ternäre Factory verwenden, die nur die freigegebenen Integration Runtimes enthält. Diese freigegebene Factory können Sie in allen Umgebungen als verknüpften Integration Runtime-Typ verwenden.

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>Fehler bei Dokumenterstellung oder -aktualisierung aufgrund eines ungültigen Verweises

#### <a name="issue"></a>Problem

Beim Versuch, Änderungen zu veröffentlichen, erhalten Sie die folgende Fehlermeldung:

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`
### <a name="cause"></a>Ursache

Sie haben die Git-Konfiguration getrennt und mit ausgewähltem Flag „Import resources“ erneut eingerichtet. Damit wird der Dienst auf „synchron“ festgelegt. Dies bedeutet keine Änderung während der Veröffentlichung.

#### <a name="resolution"></a>Lösung

Trennen Sie die Git-Konfiguration, und richten Sie sie erneut ein. Stellen Sie sicher, dass Sie das Kontrollkästchen zum Importieren vorhandener Ressourcen NICHT aktivieren.

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>Fehler beim Verschieben der Data Factory von einer Ressourcengruppe in eine andere

#### <a name="issue"></a>Problem

Sie können Data Factory nicht von einer Ressourcengruppe in eine andere verschieben. Folgender Fehler tritt auf: `
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>Lösung

Sie können die SSIS-IR und Shared IRs löschen, um den Verschiebevorgang zu ermöglichen. Wenn Sie die Integration Runtimes nicht löschen möchten, empfiehlt es sich, die Anweisungen im Dokument zum Kopieren und Klonen zu befolgen und anschließend die alte Data Factory zu löschen.

###  <a name="unable-to-export-and-import-arm-template"></a>Exportieren und Importieren von ARM-Vorlagen nicht möglich

#### <a name="issue"></a>Problem

Eine ARM-Vorlage kann nicht exportiert und importiert werden. Im Portal wurde kein Fehler angezeigt, aber in der Stapelüberwachung des Browsers bemerken Sie folgenden Fehler:

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>Ursache

Sie haben als Benutzer eine Kundenrolle erstellt und verfügten nicht über die erforderliche Berechtigung. Beim Laden der Benutzeroberfläche werden mehrere Steuerelementwerte überprüft, die verfügbar gemacht werden. In diesem Fall besitzt die Zugriffsrolle des Benutzers keine Berechtigung für den Zugriff auf die *queryFeaturesValue*-API. Für den Zugriff auf diese API ist das Feature für globale Parameter deaktiviert. Der ARM-Exportcodepfad basiert zum Teil auf dem Feature für globale Parameter.

#### <a name="resolution"></a>Lösung

Um dieses Problem zu lösen, müssen Sie Ihrer Rolle die folgende Berechtigung hinzufügen: *Microsoft.DataFactory/factories/queryFeaturesValue/action*. Diese Berechtigung ist standardmäßig in der Rolle **Mitwirkender von Data Factory** für Data Factory und in der Rolle **Mitwirkender** für Synapse Analytics enthalten.

###  <a name="cannot-automate-publishing-for-cicd"></a>Automatisierung der Veröffentlichung für CI/CD nicht möglich 

#### <a name="cause"></a>Ursache

Bis vor Kurzem war es nur möglich, eine Pipeline durch Klicken in der Benutzeroberfläche des Portals für Bereitstellungen zu veröffentlichen. Dieser Prozess kann jetzt automatisiert werden.

#### <a name="resolution"></a>Lösung

Der CI/CD-Prozess wurde erweitert. Die **automatisierte** Veröffentlichungsfunktion übernimmt, validiert und exportiert alle ARM-Vorlagenfunktionen aus der Benutzeroberfläche. Hierdurch kann die Logik über das öffentlich verfügbare npm-Paket [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) genutzt werden. Diese Methode ermöglicht es Ihnen, diese Aktionen programmgesteuert auszulösen, anstatt auf die Oberfläche zuzugreifen und auf eine Schaltfläche zu klicken. Mit dieser Methode erhalten Ihre CI/CD-Pipelines eine **authentische** kontinuierliche Integrationserfahrung. Weitere Informationen finden Sie im Artikel zum [automatisierten Veröffentlichen für Continuous Integration und Delivery](./continuous-integration-delivery-improvements.md). 

###  <a name="cannot-publish-because-of-4-mb-arm-template-limit"></a>Die Veröffentlichung ist nicht möglich, da die ARM-Vorlage auf 4 MB begrenzt ist  

#### <a name="issue"></a>Problem

Sie können nicht bereitstellen, weil Sie den Azure Resource Manager-Grenzwert von 4 MB Gesamtvorlagengröße erreicht haben. Sie benötigen eine Lösung für die Bereitstellung nach dem Erreichen dieses Grenzwerts. 

#### <a name="cause"></a>Ursache

Azure Resource Manager beschränkt die Vorlagengröße auf 4 MB. Begrenzen Sie die Größe der Vorlage auf 4 MB und die jeder Parameterdatei auf 64 KB. Der Grenzwert von 4 MB gilt für den endgültigen Zustand der Vorlage, nachdem sie mit iterativen Ressourcendefinitionen und Werten für Variablen und Parameter erweitert wurde. Sie haben den Grenzwert aber überschritten. 

#### <a name="resolution"></a>Lösung

Bei kleinen bis mittelgroßen Lösungen lässt sich eine Einzelvorlage einfacher verstehen und verwalten. Sie können alle Ressourcen und Werte in einer einzelnen Datei anzeigen. In erweiterten Szenarien können Sie mithilfe verknüpfter Vorlagen die Lösung in Zielkomponenten unterteilen. Befolgen Sie die Best Practice bei der [Verwendung von verknüpften und verschachtelten Templates](../azure-resource-manager/templates/linked-templates.md?tabs=azure-powershell).

### <a name="cannot-connect-to-git-enterprise"></a>Ich kann keine Verbindung mit Git Enterprise herstellen  

##### <a name="issue"></a>Problem

Aufgrund von Berechtigungsproblemen können Sie keine Verbindung mit Git Enterprise herstellen. Ein Fehler wie **422 – Einheit kann nicht bearbeitet werden.** wird angezeigt.

#### <a name="cause"></a>Ursache

* Sie haben OAuth nicht für den Dienst konfiguriert. 
* Ihre URL ist falsch konfiguriert. repoConfiguration muss vom Typ [FactoryGitHubConfiguration](/dotnet/api/microsoft.azure.management.datafactory.models.factorygithubconfiguration?view=azure-dotnet&preserve-view=true) sein.

#### <a name="resolution"></a>Lösung 

Gewähren Sie zuerst OAuth-Zugriff auf den Dienst. Anschließend müssen Sie durch Angabe der richtigen URL eine Verbindung mit Git Enterprise herstellen. Die Konfiguration muss auf die Kundenorganisation(en) festgelegt werden. Beispielsweise probiert der Dienst zunächst *https://hostname/api/v3/search/repositories?q=user%3&lt;customer credential&gt;...* aus, was zu einem Fehler führt. Anschließend probiert der Dienst *https://hostname/api/v3/orgs/&lt;org&gt;/&lt; repo&gt;...* aus und ist damit erfolgreich. 
 
### <a name="cannot-recover-from-a-deleted-instance"></a>Wiederstellen aus einer gelöschten Instanz nicht möglich

#### <a name="issue"></a>Problem
Eine Instanz des Diensts oder die Ressourcengruppe, in der die Instanz enthalten war, wurde gelöscht und muss wiederhergestellt werden.

#### <a name="cause"></a>Ursache

Die Instanz kann nur wiederhergestellt werden, wenn die Quellcodeverwaltung für die Instanz mit DevOps oder Git konfiguriert wurde. Diese Aktion stellt alle zuletzt veröffentlichten Ressourcen wieder her, aber **keine** unveröffentlichte Pipelines, Datasets oder verknüpfte Dienste. Wenn keine Quellcodeverwaltung vorhanden ist, ist das Wiederherstellen einer gelöschten Instanz aus dem Azure-Back-End nicht möglich, da die Instanz ohne Sicherung für immer gelöscht wird, sobald der Dienst den Löschbefehl empfängt.

#### <a name="resolution"></a>Lösung

Führen Sie zum Wiederherstellen einer gelöschten Dienstinstanz, für die die Quellcodeverwaltung konfiguriert ist, die folgenden Schritte aus:

 * Erstellen Sie eine neue Instanz des Diensts.

 * Konfigurieren Sie Git mit denselben Einstellungen neu. Vergewissern Sie sich aber, dass vorhandene Ressourcen in das ausgewählte Repository importiert werden, und wählen Sie „Neuer Branch“ aus.

 * Erstellen Sie einen Pull Request zum Mergen der Änderungen in den Kollaborationsbranch und zum Veröffentlichen.

 * Wenn eine selbstgehostete Integration Runtime in einer gelöschten Data Factory oder einem Synapse-Arbeitsbereich vorhanden ist, muss eine neue Instanz der IR in einer neuen Factory oder einem neuen Arbeitsbereich erstellt werden.  Die IR-Instanz des lokalen oder virtuellen Computers muss deinstalliert und neu installiert werden. Weiterhin muss ein neuer Schlüssel abgerufen werden. Nachdem die Einrichtung der neuen IR abgeschlossen wurde, muss der verknüpfte Dienst aktualisiert werden, sodass er auf die neue IR verweist, und die Verbindung muss erneut getestet werden. Andernfalls wird der Fehler gemeldet, dass der **Verweis ungültig** ist.

### <a name="cannot-deploy-to-different-stage-using-automatic-publish-method"></a>Die Bereitstellung auf einer anderen Stufe mit der automatischen Veröffentlichungsmethode ist nicht möglich

#### <a name="issue"></a>Problem
Der Kunde hat alle notwendigen Schritte wie die Installation des NPM-Pakets und das Einrichten einer höheren Stufe mit Azure DevOps befolgt, die Bereitstellung schlägt aber weiterhin fehl.

#### <a name="cause"></a>Ursache

npm-Pakete können zwar auf verschiedene Weise genutzt werden, aber einer der Hauptvorteile ist die Nutzung über Azure Pipelines. Bei jedem Merge in den Kollaborationsbranch kann eine Pipeline ausgelöst werden, die zuerst den gesamten Code überprüft und dann die ARM-Vorlage in ein Buildartefakt exportiert, das von einer Releasepipeline verwendet werden kann. In der Starter-Pipeline sollte die YAML-Datei gültig und vollständig sein.


#### <a name="resolution"></a>Lösung

Der folgende Abschnitt ist ungültig, da der Ordner package.json ungültig ist.

```
- task: Npm@1
  inputs:
    command: 'custom'
    workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
    customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
  displayName: 'Validate'
```
Es sollte DataFactory im customCommand enthalten, wie z.B. *'run build validate $(Build.Repository.LocalPath)/DataFactory/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName‘* . Stellen Sie sicher, dass die erzeugte YAML-Datei für die höhere Stufe die erforderlichen JSON-Artefakte enthalten sollte.

### <a name="git-repository-or-purview-connection-disconnected"></a>Die Git-Repository- oder Purview-Verbindung wurde getrennt

#### <a name="issue"></a>Problem
Beim Bereitstellen einer Dienstinstanz wird das Git-Repository oder die Purview-Verbindung getrennt.

#### <a name="cause"></a>Ursache
Wenn Sie für das Bereitstellen globaler Parameter die Option **In ARM-Vorlage einschließen** ausgewählt haben, wird Ihre Dienstinstanz in die ARM-Vorlage integriert. Daher werden andere Eigenschaften bei der Bereitstellung entfernt.

#### <a name="resolution"></a>Lösung
Deaktivieren Sie **In ARM-Vorlage einschließen**, und stellen Sie globale Parameter mit PowerShell wie unter „Globale Parameter in CI/CD“ beschrieben bereit. 
 
### <a name="extra--left--displayed-in-published-json-file"></a>In der veröffentlichten JSON-Datei wird eine zusätzliche öffnende eckige Klammer („[“) angezeigt

#### <a name="issue"></a>Problem
Bei der Bereitstellung mit DevOps wird ein zusätzliches „[“ angezeigt. Der Dienst fügt in DevOps automatisch eine weitere eckige Klammer („[“) in ARM-Vorlagen hinzu. In der JSON-Datei wird ein Ausdruck wie „[[“ angezeigt.

#### <a name="cause"></a>Ursache
Da „[“ ein reserviertes Zeichen für ARM ist, wird automatisch eine zusätzliche öffnende eckige Klammer hinzugefügt, um für „[“ ein Escapezeichen zu verwenden.

#### <a name="resolution"></a>Lösung
Dieses Verhalten ist während des Veröffentlichungsprozesses für CI/CD normal.
 
### <a name="perform-cicd-during--progressqueued-stage-of-pipeline-run"></a>Ausführen von **CI/CD** während der Status-/Warteschlangenstufe der Pipelineausführung

#### <a name="issue"></a>Problem
Sie möchten CI/CD während der Status-/Warteschlangenstufe der Pipelineausführung durchführen.

#### <a name="cause"></a>Ursache
Wenn die Pipeline auf der Status-/Warteschlangenstufe ist, müssen Sie zunächst die Pipeline und die Aktivitäten überwachen. Anschließend können Sie entscheiden, ob Sie warten möchten, bis die Pipeline abgeschlossen ist, oder ob Sie die Ausführung der Pipeline abbrechen. 
 
#### <a name="resolution"></a>Lösung
Sie können die Pipeline auch per **SDK**, **Azure Monitor** oder [Monitor](./monitor-visually.md) überwachen. Anschließend können Sie die [bewährten Methoden für CI/CD](./continuous-integration-delivery.md#best-practices-for-cicd) befolgen. 

### <a name="perform-unit-testing-during-development-and-deployment"></a>Ausführen von **KOMPONENTENTESTS** während der Entwicklung und Bereitstellung

#### <a name="issue"></a>Problem
Sie möchten Komponententests während der Entwicklung und Bereitstellung Ihrer Pipelines durchführen.

#### <a name="cause"></a>Ursache
Möglicherweise möchten Sie während der Entwicklungs- und Bereitstellungszyklen Komponententests für Ihre Pipeline ausführen, bevor Sie die Pipeline manuell oder automatisch veröffentlichen. Mithilfe der Testautomatisierung können Sie mehr Tests in weniger Zeit und mit garantierter Wiederholbarkeit ausführen. Das automatische erneute Testen aller Pipelines vor der Bereitstellung bietet Ihnen einen gewissen Schutz vor Regressionsfehlern. Automatisierte Tests sind ein wichtiger Aspekt von Ansätzen zur CI/CD-Softwareentwicklung: Die Einbeziehung automatisierter Tests in CI/CD-Bereitstellungspipelines kann die Qualität erheblich verbessern. Langfristig werden getestete Pipelineartefakte wiederverwendet und sparen damit Kosten und Zeit.  
 
#### <a name="resolution"></a>Lösung
Da die Anforderungen der Kunden an Komponententests sehr unterschiedlich sein können und verschiedene Skillsets erfordern, sollten Sie in der Regel die folgenden Schritte befolgen:

1. Richten Sie ein Azure DevOps-CI/CD-Projekt ein, oder entwickeln Sie eine SDK-gesteuerte Teststrategie für .NET/Python/REST.
2. Erstellen Sie für CI/CD ein Buildartefakt, das alle Skripts enthält, und stellen Sie Ressourcen in einer Releasepipeline bereit. Entwickeln Sie für einen SDK-basierten Ansatz Testeinheiten mit PyTest in Python, Nunit in C# mit dem .NET SDK usw.
3. Führen Sie Komponententests als Teil der Releasepipeline oder unabhängig mit dem ADF-SDK für Python/PowerShell/.NET/REST aus. 

Sie können beispielsweise Duplikate in einer Datei löschen und dann die zusammengestellte Datei als Tabelle in einer Datenbank speichern. Um die Pipeline zu testen, richten Sie mithilfe von Azure DevOps ein CI/CD-Projekt ein.
Richten Sie eine Testphase für die Pipeline ein, in der Sie Ihre entwickelte Pipeline bereitstellen. Sie konfigurieren die Testphase zum Ausführen von Python-Tests, um sicherzustellen, dass die Tabellendaten Ihren Erwartungen entsprechen. Wenn Sie CI/CD nicht verwenden, können Sie bereitgestellte Pipelines mithilfe von **Nunit** mit den gewünschten Tests auslösen. Wenn Sie mit den Ergebnissen zufrieden sind, können Sie die Pipeline abschließend in einer Produktionsinstanz veröffentlichen. 


### <a name="pipeline-runs-temporarily-fail-after-cicd-deployment-or-authoring-updates"></a>Bei Pipelineausführungen tritt nach der CI/CD-Bereitstellung oder Erstellungsupdates vorübergehend ein Fehler auf

#### <a name="issue"></a>Problem
Nach einiger Zeit werden neue Pipelineausführungen ohne Benutzeraktionen nach vorübergehenden Fehlern erfolgreich ausgeführt.

#### <a name="cause"></a>Ursache

Es gibt mehrere Szenarien, die dieses Verhalten auslösen können. Bei allen Szenarien wird eine neue Version einer abhängigen Ressource von der alten Version der übergeordneten Ressource aufgerufen. Angenommen, eine vorhandene untergeordnete Pipeline, die von „Execute Pipeline“ aufgerufen wird, wird aktualisiert, damit sie die erforderlichen Parameter erhält, und die vorhandene übergeordnete Pipeline wird aktualisiert, um diese Parameter zu übergeben. Wenn die Bereitstellung während der Ausführung einer übergeordneten Pipeline, aber vor der **Execute Pipeline**-Aktivität erfolgt, ruft die alte Version der Pipeline die neue Version der untergeordneten Pipeline auf, sodass die erwarteten Parameter nicht übergeben werden. Dies führt dazu, dass die Pipeline mit einem *UserError* fehlschlägt. Dies kann auch bei andersartigen Abhängigkeiten auftreten, z. B. wenn während einer Pipelineausführung, die auf einen verknüpften Dienst verweist, eine Breaking Change am verknüpften Dienst vorgenommen wird. 

#### <a name="resolution"></a>Lösung

Neue Ausführungen der übergeordneten Pipeline werden automatisch erfolgreich ausgeführt, sodass normalerweise keine Aktion erforderlich ist. Um diese Fehler zu vermeiden, sollten Kunden jedoch beim Erstellen und Planen von Bereitstellungen darauf achten, dass es bei Abhängigkeiten nicht zu Breaking Changes kommt. 

### <a name="cannot-parameterize-integration-run-time-in-linked-service"></a>Integrationslaufzeit im verknüpften Dienst kann nicht parametrisiert werden

#### <a name="issue"></a>Problem
Notwendigkeit der Parametrisierung der verknüpften Dienstintegration zur Laufzeit

#### <a name="cause"></a>Ursache
Diese Funktion wird nicht unterstützt. 

#### <a name="resolution"></a>Lösung
Sie müssen manuell auswählen und eine Integrationslaufzeit festlegen. Sie können auch die PowerShell-API zum Ändern verwenden.  Diese Veränderung kann nachgelagerte Auswirkungen haben. 

### <a name="updatechange-integration-runtime-during-cicd"></a>Aktualisierung/Änderung der Integrationslaufzeit während CI/CD. 
 
#### <a name="issue"></a>Problem
Ändern des Namens der Integrationslaufzeit während der CI/CD-Bereitstellung.  
 
#### <a name="cause"></a>Ursache
Die Parametrisierung einer Entitätsreferenz (Integrationslaufzeit in Linked Service, Dataset in Aktivität, Linked Service in Dataset) wird nicht unterstützt.  Das Ändern des Laufzeitnamens während der Bereitstellung führt dazu, dass die abhängige Ressource (Ressource, die die Integrationslaufzeit referenziert) mit einer ungültigen Referenz missgebildet wird.  
 
#### <a name="resolution"></a>Lösung
Data Factory erfordert, dass Sie in allen Phasen von CI/CD denselben Namen und Typ der Integrationslaufzeit verwenden. 

### <a name="arm-template-deployment-failing-with-error-datafactorypropertyupdatenotsupported"></a>ARM-Vorlagenbereitstellung schlägt mit dem Fehler DataFactoryPropertyUpdateNotSupported fehl

##### <a name="issue"></a>Problem
Die Bereitstellung der ARM-Vorlage schlägt mit einem Fehler wie DataFactoryPropertyUpdateNotSupported fehl: Das Aktualisieren des Eigenschaftstyps wird nicht unterstützt. 

##### <a name="cause"></a>Ursache
Das ARM-Template-Bereitstellung versucht, den Typ einer bestehenden Integrationslaufzeit zu ändern. Dies ist nicht zulässig und führt zu einem Bereitstellungsfehler, da Data Factory in allen Phasen von CI/CD denselben Namen und Typ der Integrationslaufzeit erfordert.

##### <a name="resolution"></a>Lösung
Wenn Sie Integration Runtimes über alle Stufen hinweg freigeben möchten, können Sie eine ternäre Factory verwenden, die nur die freigegebenen Integration Runtimes enthält. Diese freigegebene Factory können Sie in allen Umgebungen als verknüpften Integration Runtime-Typ verwenden. Weitere Informationen finden Sie unter [Kontinuierliche Integration und Bereitstellung - Azure Data Factory](https://docs.microsoft.com/azure/data-factory/continuous-integration-delivery#best-practices-for-cicd)

## <a name="next-steps"></a>Nächste Schritte

Weitere Hilfe zur Problembehandlung finden Sie in den folgenden Ressourcen:

*  [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
*  [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
