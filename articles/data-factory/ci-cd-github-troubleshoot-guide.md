---
title: Beheben von CI/CD-, Azure DevOps- und GitHub-Problemen in ADF
titleSuffix: Azure Data Factory & Azure Synapse
description: Verwenden Sie verschiedene Methoden, um CI/CD-Probleme in ADF zu beheben.
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.subservice: ci-cd
ms.custom: synapse
ms.topic: troubleshooting
ms.date: 06/27/2021
ms.openlocfilehash: 8f94e6b0e4afd06a68263efb0d78f3962bbd8560
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866400"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>Beheben von CI/CD-, Azure DevOps- und GitHub-Problemen in ADF 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel beschäftigen wir uns mit den gängigen Methoden zur Fehlerbehebung bei der Continuous Integration-Continuous Deployment (CI-CD), der Azure DevOps und den GitHub-Problemen in Azure Data Factory.

Wenn Sie Fragen oder Probleme bei der Quellcodeverwaltung oder DevOps-Verfahren haben, können die folgenden Artikel nützlich sein:

- Unter [Quellcodeverwaltung in Azure Data Factory](source-control.md) erfahren Sie, wie die Quellcodeverwaltung in ADF funktioniert. 
- Unter [Continuous Integration und Continuous Delivery in Azure Data Factory](continuous-integration-deployment.md) erfahren Sie, wie CI/CD für DevOps in ADF funktioniert.

## <a name="common-errors-and-messages"></a>Häufige Fehler und Meldungen

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>Fehler bei der Verbindungsherstellung mit Git-Repository aufgrund eines anderen Mandanten

#### <a name="issue"></a>Problem

Zuweilen können Authentifizierungsprobleme auftreten, beispielsweise der HTTP-Statusfehler 401. Das Ganze kann ziemlich kompliziert werden – insbesondere dann, wenn Sie über mehrere Mandanten mit Gastkonto verfügen.

#### <a name="cause"></a>Ursache

Folgendes lässt sich beobachten: Das Token wurde vom ursprünglichen Mandanten abgerufen, aber ADF befindet sich im Gastmandanten und versucht, das Token zum Zugreifen auf DevOps im Gastmandanten zu verwenden. Dies entspricht nicht dem erwarteten Verhalten.

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

Dieser Fehler ist auf eine Integration Runtime mit dem gleichen Namen in der Target Factory, jedoch mit einem anderen Typ zurückzuführen. Die Integration Runtime muss bei der Bereitstellung vom gleichen Typ sein.

#### <a name="recommendation"></a>Empfehlung

- Beachten Sie die [bewährten Methoden für CI/CD](continuous-integration-deployment.md#best-practices-for-cicd).

- Integration Runtimes ändern sich nicht häufig und sind in allen CI/CD-Phasen gleich. Daher erwartet Data Factory, dass Sie in allen Phasen von CI/CD eine Integration Runtime mit demselben Namen und demselben Typ verwenden. Wenn sich die Namen, Typen und Eigenschaften unterscheiden, sollten Sie sicherstellen, dass Sie die Quell- und Zielkonfigurationen der Integration Runtime abgleichen und erst dann die Releasepipeline bereitstellen.

- Wenn Sie Integration Runtimes über alle Stufen hinweg freigeben möchten, können Sie eine ternäre Factory verwenden, die nur die freigegebenen Integration Runtimes enthält. Diese freigegebene Factory können Sie in allen Umgebungen als verknüpften Integration Runtime-Typ verwenden.

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>Fehler bei Dokumenterstellung oder -aktualisierung aufgrund eines ungültigen Verweises

#### <a name="issue"></a>Problem

Beim Versuch, Änderungen an einer Data Factory zu veröffentlichen, erhalten Sie die folgende Fehlermeldung:

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`
### <a name="cause"></a>Ursache

Sie haben die Git-Konfiguration getrennt und mit ausgewähltem Flag „Import resources“ erneut eingerichtet. Damit wird die Data Factory auf „synchron“ festgelegt. Dies bedeutet keine Änderung während der Veröffentlichung.

#### <a name="resolution"></a>Lösung

Trennen Sie die Git-Konfiguration, und richten Sie sie erneut ein. Stellen Sie sicher, dass Sie das Kontrollkästchen zum Importieren vorhandener Ressourcen NICHT aktivieren.

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>Fehler beim Verschieben von Data Factory von einer Ressourcengruppe in eine andere

#### <a name="issue"></a>Problem

Sie können Data Factory nicht von einer Ressourcengruppe in eine andere verschieben. Folgender Fehler tritt auf:

`
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

Sie haben als Benutzer eine Kundenrolle erstellt und verfügten nicht über die erforderliche Berechtigung. Wenn die Factory in die Benutzeroberfläche geladen wird, wird eine Reihe von Kontrollwerten zur Risikobewertung für die Factory überprüft. In diesem Fall besitzt die Zugriffsrolle des Benutzers keine Berechtigung für den Zugriff auf die *queryFeaturesValue*-API. Für den Zugriff auf diese API ist das Feature für globale Parameter deaktiviert. Der ARM-Exportcodepfad basiert zum Teil auf dem Feature für globale Parameter.

#### <a name="resolution"></a>Lösung

Um dieses Problem zu lösen, müssen Sie Ihrer Rolle die folgende Berechtigung hinzufügen: *Microsoft.DataFactory/factories/queryFeaturesValue/action*. Diese Berechtigung sollte standardmäßig in der Rolle „Data Factory-Mitwirkender“ enthalten sein.

###  <a name="cannot-automate-publishing-for-cicd"></a>Automatisierung der Veröffentlichung für CI/CD nicht möglich 

#### <a name="cause"></a>Ursache

Bis vor Kurzem war die einzige Möglichkeit zum Veröffentlichen einer ADF-Pipeline für Bereitstellungen das Klicken auf die Schaltfläche im ADF-Portal. Diesen Prozess können Sie nun automatisieren. 

#### <a name="resolution"></a>Lösung

Der CI/CD-Prozess wurde erweitert. Die **automatisierte** Veröffentlichungsfunktion übernimmt, validiert und exportiert alle ARM-Vorlagenfunktionen aus der ADF UX. Hierdurch kann die Logik über das öffentlich verfügbare npm-Paket [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) genutzt werden. Diese Methode ermöglicht es Ihnen, diese Aktionen programmatisch auszulösen, anstatt auf die ADF-Oberfläche zuzugreifen und einen Schaltflächenklick auszuführen. Mit dieser Methode erhalten Ihre CI/CD-Pipelines eine **authentische** kontinuierliche Integrationserfahrung. Weitere Informationen finden Sie unter [Automatisiertes Veröffentlichen für Continuous Integration und Delivery](./continuous-integration-deployment-improvements.md). 

###  <a name="cannot-publish-because-of-4-mb-arm-template-limit"></a>Die Veröffentlichung ist nicht möglich, da die ARM-Vorlage auf 4 MB begrenzt ist.  

#### <a name="issue"></a>Problem

Sie können nicht bereitstellen, da Sie den Azure Resource Manager-Grenzwert von 4 MB Gesamtvorlagengröße erreicht haben. Sie benötigen eine Lösung für die Bereitstellung nach dem Erreichen dieses Grenzwerts. 

#### <a name="cause"></a>Ursache

Azure Resource Manager beschränkt die Vorlagengröße auf 4 MB. Begrenzen Sie die Größe der Vorlage auf 4 MB und die jeder Parameterdatei auf 64 KB. Die 4-MB-Beschränkung gilt für den endgültigen Status der Vorlage, nachdem sie durch iterative Ressourcendefinitionen und Werte für variables und Parameter erweitert wurde. Sie haben den Grenzwert aber überschritten. 

#### <a name="resolution"></a>Lösung

Bei kleinen bis mittelgroßen Lösungen lässt sich eine Einzelvorlage einfacher verstehen und verwalten. Sie können alle Ressourcen und Werte in einer einzelnen Datei anzeigen. In erweiterten Szenarien können Sie mithilfe verknüpfter Vorlagen die Lösung in Zielkomponenten unterteilen. Befolgen Sie die Best Practice bei der [Verwendung von verknüpften und verschachtelten Templates](../azure-resource-manager/templates/linked-templates.md?tabs=azure-powershell).

### <a name="cannot-connect-to-git-enterprise"></a>Ich kann keine Verbindung mit Git Enterprise herstellen  

##### <a name="issue"></a>Problem

Aufgrund von Berechtigungsproblemen können Sie keine Verbindung mit Git Enterprise herstellen. Ein Fehler wie **422 – Einheit kann nicht bearbeitet werden.** wird angezeigt.

#### <a name="cause"></a>Ursache

* Sie haben OAuth für ADF nicht konfiguriert. 
* Ihre URL ist falsch konfiguriert. repoConfiguration muss vom Typ [FactoryGitHubConfiguration](/dotnet/api/microsoft.azure.management.datafactory.models.factorygithubconfiguration?view=azure-dotnet&preserve-view=true) sein.

#### <a name="resolution"></a>Lösung 

Sie gewähren zuerst den OAuth-Zugriff auf ADF. Anschließend müssen Sie durch Angabe der richtigen URL eine Verbindung mit Git Enterprise herstellen. Die Konfiguration muss auf die Kundenorganisation(en) festgelegt werden. Beispielsweise versucht ADF zunächst *https://hostname/api/v3/search/repositories?q=user%3&lt;Kundenanmeldeinformationen&gt;...* , was zu einem Fehler führt. Anschließend probiert der Dienst *https://hostname/api/v3/orgs/&lt;org&gt;/&lt; repo&gt;...* aus und ist damit erfolgreich.  
 
### <a name="cannot-recover-from-a-deleted-data-factory"></a>Wiederstellen aus einer gelöschten Data Factory nicht möglich

#### <a name="issue"></a>Problem
Der Kunde hat die Data Factory oder die Ressourcengruppe mit der Data Factory gelöscht. Der Kunde möchte wissen, wie er eine gelöschte Data Factory wiederherstellt.

#### <a name="cause"></a>Ursache

Die Data Factory kann nur dann wiederhergestellt werden, wenn der Kunde die Quellcodeverwaltung konfiguriert hat (DevOps oder Git). Diese Aktion stellt alle zuletzt veröffentlichten Ressourcen wieder her und **nicht** die unveröffentlichte Pipeline, den Datensatz und den verknüpften Dienst. Wenn keine Quellcodeverwaltung vorhanden ist, ist die Wiederherstellung einer gelöschten Data Factory aus dem Backend nicht möglich, da die Instanz gelöscht wird, sobald der Dienst den Löschbefehl erhält, und keine Sicherung gespeichert wurde.

#### <a name="resolution"></a>Lösung

Gehen Sie wie folgt vor, um die gelöschte Data Factory mit der Quellcodeverwaltung wiederherzustellen:

 * Erstellen Sie eine neue Azure Data Factory-Instanz.

 * Konfigurieren Sie Git mit denselben Einstellungen neu. Vergewissern Sie sich aber, dass „Import existing Data Factory resources to the selected repository“ (Vorhandene Data Factory-Ressourcen in das ausgewählte Repository importieren) aktiviert ist, und wählen Sie „Neuer Branch“ aus.

 * Erstellen Sie einen Pull Request zum Mergen der Änderungen in den Kollaborationsbranch und zum Veröffentlichen.

 * Wenn der Kunde eine selbst gehostete Integration Runtime in einem gelöschten ADF hätte, müsste er eine neue Instanz in einem neuen ADF erstellen, sowie die Instanz auf seinem lokalen Gerät/VM mit dem neu erhaltenen Schlüssel deinstallieren und neu installieren. Nachdem der Setupvorgang für die IR abgeschlossen ist, muss der Kunde den verknüpften Dienst so ändern, dass er auf die neue IR verweist, und dann die Verbindung testen. Andernfalls tritt der Fehler **Ungültiger Verweis** auf.

### <a name="cannot-deploy-to-different-stage-using-automatic-publish-method"></a>Die Bereitstellung auf einer anderen Stufe mit der automatischen Veröffentlichungsmethode ist nicht möglich

#### <a name="issue"></a>Problem
Der Kunde hat alle notwendigen Schritte wie die Installation des NPM-Pakets und das Einrichten einer höheren Stufe anhand Azure DevOps und ADF befolgt. Die Bereitstellung findet jedoch nicht statt.

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
Beim Bereitstellen Ihrer Data Factory wird Ihre Git-Repository- oder Purview-Verbindung getrennt.

#### <a name="cause"></a>Ursache
Wenn Sie für das Bereitstellen globaler Parameter die Option **In ARM-Vorlage einschließen** ausgewählt haben, wird Ihre Factory in die ARM-Vorlage integriert. Daher werden andere Factoryeigenschaften bei der Bereitstellung entfernt.

#### <a name="resolution"></a>Lösung
Deaktivieren Sie **In ARM-Vorlage einschließen**, und stellen Sie globale Parameter mit PowerShell wie unter „Globale Parameter in CI/CD“ beschrieben bereit. 
 
### <a name="extra--left--displayed-in-published-json-file"></a>In der veröffentlichten JSON-Datei wird eine zusätzliche öffnende eckige Klammer („[“) angezeigt

#### <a name="issue"></a>Problem
Beim Veröffentlichen von ADF mit DevOps wird eine weitere öffnende eckige Klammer („[“) angezeigt. In DevOps fügt ADF automatisch eine weitere öffnende eckige Klammer („[“) in ARM-Vorlagen hinzu. In der JSON-Datei wird ein Ausdruck wie „[[“ angezeigt.

#### <a name="cause"></a>Ursache
Da „[“ ein reserviertes Zeichen für ARM ist, wird automatisch eine zusätzliche öffnende eckige Klammer hinzugefügt, um für „[“ ein Escapezeichen zu verwenden.

#### <a name="resolution"></a>Lösung
Dies ist während des ADF-Veröffentlichungsprozesses für CI/CD ein normales Verhalten.
 
### <a name="perform-cicd-during--progressqueued-stage-of-pipeline-run"></a>Ausführen von **CI/CD** während der Status-/Warteschlangenstufe der Pipelineausführung

#### <a name="issue"></a>Problem
Sie möchten CI/CD während der Status-/Warteschlangenstufe der Pipelineausführung durchführen.

#### <a name="cause"></a>Ursache
Wenn die Pipeline auf der Status-/Warteschlangenstufe ist, müssen Sie zunächst die Pipeline und die Aktivitäten überwachen. Anschließend können Sie entscheiden, ob Sie warten möchten, bis die Pipeline abgeschlossen ist, oder ob Sie die Ausführung der Pipeline abbrechen. 
 
#### <a name="resolution"></a>Lösung
Sie können die Pipeline auch per **SDK**, **Azure Monitor** oder [ADF Monitor](./monitor-visually.md) überwachen. Anschließend befolgen Sie für die weitere Optimierung die [bewährten Methoden für CI/CD in ADF](./continuous-integration-deployment.md#best-practices-for-cicd). 

### <a name="perform-unit-testing-during-adf-development-and-deployment"></a>Ausführen von **KOMPONENTENTESTS** während der ADF-Entwicklung und -Bereitstellung

#### <a name="issue"></a>Problem
Sie möchten Komponententests während der Entwicklung und Bereitstellung von ADF-Pipelines durchführen.

#### <a name="cause"></a>Ursache
Möglicherweise möchten Sie während der Entwicklungs- und Bereitstellungszyklen Komponententests für Ihre Pipeline ausführen, bevor Sie die Pipeline manuell oder automatisch veröffentlichen. Mithilfe der Testautomatisierung können Sie mehr Tests in weniger Zeit und mit garantierter Wiederholbarkeit ausführen. Das automatische erneute Testen aller ADF-Pipelines vor der Bereitstellung bietet Ihnen einen gewissen Schutz vor Regressionsfehlern. Automatisierte Tests sind ein wichtiger Aspekt von Ansätzen zur CI/CD-Softwareentwicklung: Die Einbeziehung automatisierter Tests in CI/CD-Bereitstellungspipelines für Azure Data Factory kann die Qualität erheblich verbessern. Langfristig werden getestete ADF-Pipelineartefakte wiederverwendet und sparen damit Kosten und Zeit.  
 
#### <a name="resolution"></a>Lösung
Da die Anforderungen der Kunden an Komponententests sehr unterschiedlich sein können und verschiedene Skillsets erfordern, sollten Sie in der Regel die folgenden Schritte befolgen:

1. Richten Sie ein Azure DevOps-CI/CD-Projekt ein, oder entwickeln Sie eine SDK-gesteuerte Teststrategie für .NET/Python/REST.
2. Erstellen Sie für CI/CD ein Buildartefakt, das alle Skripts enthält, und stellen Sie Ressourcen in einer Releasepipeline bereit. Entwickeln Sie für einen SDK-basierten Ansatz Testeinheiten mit PyTest in Python, C# **Nunit** beim .NET SDK usw.
3. Führen Sie Komponententests als Teil der Releasepipeline oder unabhängig mit dem ADF-SDK für Python/PowerShell/.NET/REST aus. 

Sie können beispielsweise Duplikate in einer Datei löschen und dann die zusammengestellte Datei als Tabelle in einer Datenbank speichern. Um die Pipeline zu testen, richten Sie mithilfe von Azure DevOps ein CI/CD-Projekt ein.
Richten Sie eine Testphase für die Pipeline ein, in der Sie Ihre entwickelte Pipeline bereitstellen. Sie konfigurieren die Testphase zum Ausführen von Python-Tests, um sicherzustellen, dass die Tabellendaten Ihren Erwartungen entsprechen. Wenn Sie CI/CD nicht verwenden, können Sie bereitgestellte Pipelines mithilfe von **Nunit** mit den gewünschten Tests auslösen. Wenn Sie mit den Ergebnissen zufrieden sind, können Sie die Pipeline abschließend in einer Data Factory für die Produktion veröffentlichen. 


## <a name="next-steps"></a>Nächste Schritte

Weitere Hilfe zur Problembehandlung finden Sie in den folgenden Ressourcen:

*  [Data Factory-Blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory-Funktionsanfragen](/answers/topics/azure-data-factory.html)
*  [Azure-Videos](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack Overflow-Forum für Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-Informationen über Data Factory](https://twitter.com/hashtag/DataFactory)
