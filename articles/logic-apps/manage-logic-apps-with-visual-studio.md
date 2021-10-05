---
title: Bearbeiten und Verwalten von Logik-Apps mit dem Cloud-Explorer von Visual Studio
description: Bearbeiten, Aktualisieren, Verwalten, Hinzufügen zur Quellcodeverwaltung und Bereitstellen von Logik-Apps mit dem Cloud-Explorer von Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.custom: mvc
ms.date: 04/23/2021
ms.openlocfilehash: fff316c328ac61f48693e635745ca42a9c773c4d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128646623"
---
# <a name="manage-logic-apps-with-visual-studio"></a>Verwalten von Logik-Apps mit Visual Studio

Sie können Logik-Apps zwar im [Azure-Portal](https://portal.azure.com) erstellen, bearbeiten, verwalten und bereitstellen, Sie können aber auch Visual Studio verwenden, wenn Sie Ihre Logik-Apps der Quellcodeverwaltung hinzufügen, unterschiedliche Versionen veröffentlichen und [Azure Resource Manager](../azure-resource-manager/management/overview.md)-Vorlagen für verschiedene Bereitstellungsumgebungen erstellen möchten. Mit dem Cloud-Explorer von Visual Studio können Sie Ihre Logik-Apps zusammen mit anderen Azure-Ressourcen suchen und verwalten. Sie können z.B. bereits im Azure-Portal bereitgestellte Logik-Apps öffnen, herunterladen, bearbeiten, ausführen, ihren Ausführungsverlauf anzeigen, sie deaktivieren und aktivieren. Wenn die Arbeit mit Azure Logic Apps in Visual Studio für Sie neu ist, lernen Sie, [Logik-Apps mit Visual Studio zu erstellen](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

Sie können [Ihre Logik-Apps auch im Azure-Portal verwalten](manage-logic-apps-with-azure-portal.md).

> [!IMPORTANT]
> Bereitstellen oder Veröffentlichen einer Logik-App von Visual Studio aus überschreibt die Version dieser App im Azure-Portal. Wenn Sie also Änderungen im Azure-Portal vornehmen, die Sie beibehalten möchten, achten Sie darauf, dass Sie [die Logik-App in Visual Studio](#refresh) vom Azure-Portal aus aktualisieren, bevor Sie sie das nächste Mal von Visual Studio aus bereitstellen oder veröffentlichen.

<a name="requirements"></a>

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Laden Sie diese Tools herunter, und installieren Sie sie, falls sie noch nicht vorhanden sind:

  * [Visual Studio 2019, 2017 oder 2015 – Community Edition oder höher](https://aka.ms/download-visual-studio). In dieser Schnellstartanleitung wird die kostenlose Version Visual Studio Community 2017 verwendet.

    > [!IMPORTANT]
    > Stellen Sie beim Installieren von Visual Studio 2019 oder 2017 sicher, dass Sie die Workload **Azure-Entwicklung** auswählen.
    > Weitere Informationen finden Sie unter [Verwalten der Ihren Azure-Konten zugeordneten Ressourcen im Visual Studio Cloud-Explorer](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer).

    Um Cloud-Explorer für Visual Studio 2015 zu installieren, [laden Sie Cloud-Explorer aus dem Visual Studio Marketplace herunter](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015). Weitere Informationen finden Sie unter [Verwalten der Ihren Azure-Konten zugeordneten Ressourcen im Visual Studio Cloud-Explorer (2015)](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer?view=vs-2015&preserve-view=true).

  * [Azure SDK (2.9.1 oder höher)](https://azure.microsoft.com/downloads/)

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Die neuesten Azure Logic Apps-Tools für die Visual Studio-Erweiterung der gewünschten Version:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    Sie können die Azure Logic Apps-Tools entweder direkt vom Visual Studio Marketplace herunterladen und installieren oder sich über das [Installieren dieser Erweiterung aus Visual Studio](/visualstudio/ide/finding-and-using-visual-studio-extensions) informieren. Achten Sie darauf, dass Sie Visual Studio nach Abschluss der Installation neu starten.

  * Informationen zur Verwendung von Azure Government Abonnements mit Visual Studio sowie zusätzliche Einrichtungsinformationen finden Sie in diesen Themen:

    * Visual Studio 2019: [Schnellstart: Herstellen einer Verbindung mit Azure Government über Visual Studio](../azure-government/documentation-government-connect-vs.md)

    * Visual Studio 2017: [Einführung in die Visual Studio-Erweiterung „Azure Environment Selector“ (Azure-Umgebungsauswahl)](https://devblogs.microsoft.com/azuregov/introducing-the-azure-environment-selector-visual-studio-extension/), die Sie im [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=SteveMichelotti.AzureEnvironmentSelector) herunterladen und installieren können.

* Internetzugriff bei Verwendung des eingebetteten Logic Apps-Designers

  Für den Designer ist eine Internetverbindung zum Erstellen von Ressourcen in Azure und zum Lesen der Eigenschaften und Daten von Connectors in Ihrer Logik-App erforderlich.

<a name="find-logic-apps-vs"></a>

## <a name="find-logic-apps"></a>Suchen nach Logik-Apps

In Visual Studio finden Sie alle Logik-Apps, die Ihrem Azure-Abonnement zugeordnet sind und mithilfe von Cloud-Explorer im Azure-Portal bereitgestellt werden.

1. Öffnen Sie Visual Studio. Wählen Sie im Menü **Ansicht** die Option **Cloud-Explorer** aus.

1. Klicken Sie im Cloud-Explorer auf die Option **Kontenverwaltung**. Wählen Sie das Azure-Abonnement aus, dem Ihre Logik-Apps zugeordnet sind, und klicken Sie dann auf **Übernehmen**. Beispiel:

   ![Auswählen von „Kontoverwaltung“](./media/manage-logic-apps-with-visual-studio/account-management-select-Azure-subscription.png)

1. Klicken Sie neben dem Symbol für **Kontoverwaltung** auf **Ressourcentypen**. Erweitern Sie unter Ihrem Azure-Abonnement **Logik-Apps**, damit alle bereitgestellten Logik-Apps angezeigt werden, die mit Ihrem Abonnement verknüpft sind.

Öffnen Sie Ihre Logik-App als nächsten Schritt im Logik-App-Editor.

<a name="open-designer"></a>

## <a name="open-logic-apps-in-visual-studio"></a>Öffnen von Logik-Apps in Visual Studio

In Visual Studio können Sie Logik-Apps öffnen, die zuvor entweder direkt über das Azure-Portal oder als Azure Resource Group-Projekte mit Visual Studio erstellt und bereitgestellt worden sind.

1. [Öffnen Sie den Cloud-Explorer, und suchen Sie Ihre Logik-App.](#find-logic-apps-vs)

1. Klicken Sie im Kontextmenü der Logik-App auf **Mit Logik-App-Editor öffnen**.

   > [!TIP]
   > Sollte dieser Befehl in Visual Studio 2019 nicht zur Verfügung stehen, überprüfen Sie, ob Sie über die neuesten Updates für Visual Studio verfügen.

   ![Bereitgestellte Logik-Apps vom Azure-Portal aus öffnen](./media/manage-logic-apps-with-visual-studio/open-logic-app-in-editor.png)

   Sobald die Logik-App am unteren Rand des Logic Apps-Designers angezeigt wird, können Sie **Codeansicht** auswählen, um die zugrunde liegende Definitionsstruktur der Logik-App zu überprüfen. Wenn Sie eine Bereitstellungsvorlage für die Logik-App erstellen möchten, informieren Sie sich, wie Sie für diese Logik-App [eine Azure Resource Manager-Vorlage herunterladen](#download-logic-app) können. Weitere Informationen zu [Resource Manager-Vorlagen](../azure-resource-manager/templates/overview.md).

<a name="download-logic-app"></a>

## <a name="download-from-azure"></a>Herunterladen aus Azure

Sie können Logik-Apps über das [Azure-Portal](https://portal.azure.com) [herunterladen](../azure-resource-manager/templates/export-template-portal.md#export-template-from-a-resource) und sie als [Azure Resource Manager](../azure-resource-manager/management/overview.md)-Vorlagen speichern. Sie können die Vorlagen dann lokal mit Visual Studio bearbeiten und Logik-Apps für verschiedene Bereitstellungsumgebungen anpassen.  Das Herunterladen von Logik-Apps *parametrisiert* automatisch deren Definitionen in [Resource Manager-Vorlagen](../azure-resource-manager/templates/overview.md), die auch JavaScript Object Notation (JSON) verwenden.

1. Öffnen Sie in Visual Studio den Cloud-Explorer, und [öffnen Sie die Logik-App, die Sie von Azure herunterladen möchten](#open-designer).

1. Klicken Sie im Kontextmenü der Logik-App auf **Mit Logik-App-Editor öffnen**.

   > [!TIP]
   > Sollte dieser Befehl in Visual Studio 2019 nicht zur Verfügung stehen, überprüfen Sie, ob Sie über die neuesten Updates für Visual Studio verfügen.

   Die Logik-App wird im Logik-App-Editor geöffnet.

1. Wählen Sie auf der Symbolleiste des Designers **Herunterladen** aus.

   ![Herunterladen der Logik-App aus dem Azure-Portal](./media/manage-logic-apps-with-visual-studio/download-logic-app-from-portal.png)

1. Wenn Sie aufgefordert werden, einen Speicherort einzugeben, navigieren Sie zu diesem Speicherort, und speichern Sie die Resource Manager-Vorlage für die Definition der Logik-App im JSON-Dateiformat (.json).

   Die Definition der Logik-App wird im `resources`-Unterabschnitt der Resource Manager-Vorlage angezeigt. Sie können nun die Definition der Logik-App und Resource Manager-Vorlage mit Visual Studio bearbeiten. Sie können die Vorlage auch als [Azure Resource Group-Projekt](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) einer Visual Studio-Projektmappe hinzufügen. Erfahren Sie mehr über [Azure Resource Group-Projekte für Logik-Apps in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

<a name="link-integration-account"></a>

## <a name="link-to-integration-account"></a>Verknüpfen mit einem Integrationskonto

Zum Erstellen von Logik-Apps für B2B-Unternehmensintegrationsszenarien (Business-to-Business) können Sie Ihre Logik-App mit einem zuvor erstellten [Integrationskonto](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) verknüpfen, das in derselben Region wie Ihre Logik-App vorhanden ist. Ein Integrationskonto enthält B2B-Artefakte wie Handelspartner, Vereinbarungen, Schemas und Zuordnungen und ermöglicht Ihrer Logik-App das Verwenden von B2B-Connectors für die XML-Validierung und die Flatfilecodierung oder -decodierung. Sie können diese [Verknüpfung mithilfe des Azure-Portals](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account) erstellen. Sie können aber auch Visual Studio verwenden, nachdem Sie die [Voraussetzungen](#requirements) erfüllt haben und ihre Logik-App als JSON-Datei (.json) in einem [ Azure Resource Group-Projekt](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) vorhanden ist. Erfahren Sie mehr über [Azure Resource Group-Projekte für Logik-Apps in Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#create-resource-group-project).

1. Öffnen Sie in Visual Studio das Azure Resource Group-Projekt, das Ihre Logik-App enthält.

1. Öffnen Sie im Projektmappen-Explorer das Kontextmenü der Datei **\<logic-app-name\>.json**, und wählen Sie **Mit Logik-App-Designer öffnen** aus. (Tastatur: STRG + L)

   ![Öffnen der JSON-Datei der Logik-App mit dem Logik-App-Designer](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Sollte dieser Befehl in Visual Studio 2019 nicht zur Verfügung stehen, überprüfen Sie, ob Sie über die neuesten Updates für Visual Studio und die Azure Logic Apps-Tools-Erweiterung verfügen.

1. Stellen Sie sicher, dass der Logik-App-Designer den Fokus besitzt, indem Sie die Registerkarte oder Oberfläche des Designers auswählen, sodass das Eigenschaftenfenster die Eigenschaft **Integrationskonto** für Ihre Logik-App anzeigt.

   ![Eigenschaftenfenster – Eigenschaft „Integrationskonto“](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-integration-account.png)

   > [!TIP]
   > Falls das Eigenschaftenfenster nicht bereits geöffnet ist, klicken Sie im Menü **Ansicht** auf **Eigenschaftenfenster**. (Tastatur: F4)

1. Öffnen Sie die Eigenschaftenliste **Integrationskonto**, und wählen Sie das Integrationskonto aus, das Sie mit Ihrer Logik-App verknüpfen möchten. Beispiel:

   ![Öffnen der Eigenschaftenliste „Integrationskonto“](./media/manage-logic-apps-with-visual-studio/select-integration-account.png)

1. Wenn Sie fertig sind, speichern Sie Ihre Visual Studio-Projektmappe.

Wenn Sie die Eigenschaft **Integrationskonto** in Visual Studio festlegen und ihre Logik-App als Azure Resource Manager-Vorlage speichern, enthält diese Vorlage auch eine Parameterdeklaration für das ausgewählte Integrationskonto. Weitere Informationen zu Vorlagenparametern und Logik-Apps finden Sie unter [Übersicht: Automatisieren der Logik-App-Bereitstellung](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="change-location"></a>

## <a name="change-deployment-location"></a>Ändern des Bereitstellungsstandorts

Wenn Ihre Logik-App in Visual Studio als JSON-Datei in einem [Azure Resource Group-Projekt](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) vorliegt, das Sie zur Automatisierung der Bereitstellung verwenden, wird für diese Logik-App ein Standorttyp und ein bestimmter Standort festgelegt. Dieser Standort ist entweder eine Azure-Region oder eine vorhandene [Integrationsdienstumgebung](connect-virtual-network-vnet-isolated-environment.md).

Um den Standorttyp oder den Standort für Ihre Logik-App zu ändern, müssen Sie die Workflowdefinitionsdatei (JSON) Ihrer Logik-App aus dem Projektmappen-Explorer öffnen, indem Sie den Logik-App-Designer verwenden. Sie können diese Eigenschaften nicht über den Cloud-Explorer ändern.

> [!IMPORTANT]
> Die Änderung des Standorttyps von **Region** in [**Integrationsdienstumgebung**](connect-virtual-network-vnet-isolated-environment-overview.md) wirkt sich auf das [Preismodell](logic-apps-pricing.md#ise-pricing) Ihrer Logik-App aus, das für Abrechnung, [Limits](logic-apps-limits-and-config.md#integration-account-limits), [Integrationskontounterstützung](connect-virtual-network-vnet-isolated-environment-overview.md#ise-skus) usw. herangezogen wird. Stellen Sie vor der Auswahl eines anderen Standorttyps sicher, dass Sie die Auswirkungen auf Ihre Logik-App verstehen.

1. Öffnen Sie in Visual Studio das Azure Resource Group-Projekt, das Ihre Logik-App enthält.

1. Öffnen Sie im Projektmappen-Explorer das Kontextmenü der Datei `<logic-app-name>.json`, und wählen Sie **Mit Logik-App-Designer öffnen** aus. (Tastatur: STRG + L)

   ![Öffnen der JSON-Datei der Logik-App mit dem Logik-App-Designer](./media/manage-logic-apps-with-visual-studio/open-logic-app-designer.png)

   > [!TIP]
   > Sollte dieser Befehl in Visual Studio 2019 nicht zur Verfügung stehen, überprüfen Sie, ob Sie über die neuesten Updates für Visual Studio und die Azure Logic Apps-Tools-Erweiterung verfügen.

1. Stellen Sie sicher, dass der Logik-App-Designer den Fokus besitzt, indem Sie die Registerkarte oder Oberfläche des Designers auswählen, sodass das Eigenschaftenfenster die Eigenschaften **Standorttyp auswählen** und **Standort** für Ihre Logik-App anzeigt. Der Standorttyp der App ist entweder auf **Region** oder **Integrationsdienstumgebung** festgelegt.

   ![Eigenschaftenfenster – Eigenschaften „Standorttyp auswählen“ und „Standort“](./media/manage-logic-apps-with-visual-studio/open-logic-app-properties-location.png)

   > [!TIP]
   > Falls das Eigenschaftenfenster nicht bereits geöffnet ist, klicken Sie im Menü **Ansicht** auf **Eigenschaftenfenster**. (Tastatur: F4)

1. Um den Standorttyp zu ändern, öffnen Sie die Eigenschaftenliste **Standorttyp auswählen** und wählen den gewünschten Standorttyp aus.

   Wenn der Standorttyp beispielsweise **Integrationsdienstumgebung** lautet, können Sie **Region** auswählen.

   ![Eigenschaft „Standorttyp auswählen“ – Ändern des Standorttyps](./media/manage-logic-apps-with-visual-studio/change-location-type.png)

1. Um den Standorttyp zu ändern, öffnen Sie die Eigenschaftenliste **Standort**. Wählen Sie basierend auf dem Standorttyp den gewünschten Standorttyp aus, z. B.:

   * Auswählen einer anderen Azure-Region:

     ![Öffnen der Eigenschaftenliste „Standort“, Auswählen einer anderen Region](./media/manage-logic-apps-with-visual-studio/change-azure-resource-group-region.png)

   * Auswählen einer anderen Integrationsdienstumgebung:

     ![Öffnen der Eigenschaftenliste „Standort“, Auswählen einer anderen Integrationsdienstumgebung](./media/manage-logic-apps-with-visual-studio/change-integration-service-environment.png)

1. Wenn Sie fertig sind, speichern Sie Ihre Visual Studio-Projektmappe.

Wenn Sie den Standorttyp oder den Standort in Visual Studio ändern und Ihre Logik-App als Azure Resource Manager-Vorlage speichern, enthält diese Vorlage auch Parameterdeklarationen für diesen Standorttyp und Standort. Weitere Informationen zu Vorlagenparametern und Logik-Apps finden Sie unter [Übersicht: Automatisieren der Logik-App-Bereitstellung](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters).

<a name="refresh"></a>

## <a name="refresh-from-azure"></a>Aktualisieren von Azure aus

Wenn Sie Ihre Logik-App im Azure-Portal bearbeiten und die Änderungen beibehalten möchten, stellen Sie sicher, dass Sie die Version dieser App in Visual Studio mit diesen Änderungen aktualisieren.

* Wählen Sie in Visual Studio auf der Logik-App-Designer-Symbolleiste **Aktualisieren** aus.

  Oder

* Öffnen Sie im Cloud-Explorer von Visual Studio das Kontextmenü Ihrer Logik-App, und wählen Sie **Aktualisieren** aus.

![Logik-App mit Updates aktualisieren](./media/manage-logic-apps-with-visual-studio/refresh-logic-app-with-updates-from-portal.png)

## <a name="publish-logic-app-updates"></a>Veröffentlichen von Logik-App-Updates

Wenn Sie bereit sind, Ihre Logik-App-Updates von Visual Studio aus in Azure bereitzustellen, wählen Sie auf der Logik-App-Designer-Symbolleiste **Veröffentlichen** aus.

![Veröffentlichen einer aktualisierte Logik-App im Azure-Portal](./media/manage-logic-apps-with-visual-studio/publish-logic-app-to-azure-portal.png)

## <a name="manually-run-your-logic-app"></a>Manuelles Ausführen Ihrer Logik-App

Sie können eine von Visual Studio aus in Azure bereitgestellte Logik-App manuell auslösen. Wählen Sie auf der Symbolleiste des Logik-App-Designers **Trigger ausführen** aus.

![Manueller Ausführungstrigger für Ihre Logik-App](./media/manage-logic-apps-with-visual-studio/manually-run-logic-app.png)

## <a name="review-run-history"></a>Überprüfen des Ausführungsverlaufs

Um den Status zu überprüfen und Probleme mit Logik-App-Ausführungen zu diagnostizieren, können Sie die Details wie Eingaben und Ausgaben für solche Ausführungen in Visual Studio überprüfen.

1. Öffnen Sie im Cloud-Explorer das Kontextmenü Ihrer Logik-App, und wählen Sie **Ausführungsverlauf öffnen** aus.

   ![Öffnen des Ausführungsverlaufs für Ihre Logik-App](./media/manage-logic-apps-with-visual-studio/open-run-history-for-logic-app.png)

1. Um die Details für eine bestimmte Ausführung anzuzeigen, doppelklicken Sie auf eine Ausführung. Beispiel:

   ![Anzeigen von Informationen zu einer bestimmten Ausführung](./media/manage-logic-apps-with-visual-studio/view-run-history-details.png)
  
   > [!TIP]
   > Wählen Sie zum Sortieren der Tabelle nach Eigenschaft die Spaltenüberschrift für diese Eigenschaft aus.

1. Erweitern Sie die Schritte, deren Eingaben und Ausgaben Sie überprüfen möchten. Beispiel:

   ![Eingaben und Ausgaben für jeden Schritt anzeigen](./media/manage-logic-apps-with-visual-studio/view-run-history-inputs-outputs.png)

<a name="disable-enable-logic-apps"></a>

## <a name="disable-or-enable-logic-apps"></a>Deaktivieren oder Aktivieren von Logik-Apps

Um zu verhindern, dass der Trigger das nächste Mal ausgelöst wird, wenn die Triggerbedingung erfüllt ist, deaktivieren Sie Ihre Logik-App. Das Deaktivieren einer Logik-App wirkt sich wie folgt auf Workflowinstanzen aus:

* Der Logic Apps-Dienst setzt alle aktiven und ausstehenden Ausführungen fort, bis sie abgeschlossen sind. Basierend auf dem Volume oder Backlog kann es einige Zeit dauern, bis dieser Prozess abgeschlossen ist.

* Der Logic Apps-Dienst erstellt keine neuen Workflowinstanzen und führt keine neuen Workflowinstanzen aus.

* Der Trigger wird nicht ausgelöst, wenn die definierten Bedingungen beim nächsten Mal erfüllt werden.

* Der Triggerstatus merkt sich den Punkt, an dem die Logik-App angehalten wurde. Wenn Sie die Logik-App erneut aktivieren, wird der Trigger für alle nicht verarbeiteten Elemente seit der letzten Ausführung ausgelöst.

  Wenn Sie die Auslösung eines Triggers für nicht verarbeitete Elemente seit der letzten Ausführung verhindern möchten, löschen Sie den Triggerstatus, bevor Sie die Logik-App wieder aktivieren:

  1. Bearbeiten Sie in der Logik-App einen beliebigen Teil des Workflowtriggers.
  1. Speichern Sie die Änderungen. Durch diesen Schritt wird der aktuelle Status Ihres Triggers zurückgesetzt.
  1. [Aktivieren Sie Ihre Logik-App wieder.](#enable-logic-apps)

<a name="disable-logic-apps"></a>

### <a name="disable-logic-apps"></a>Deaktivieren von Logik-Apps

Öffnen Sie im Cloud-Explorer das Kontextmenü Ihrer Logik-App, und wählen Sie **Deaktivieren** aus.

![Deaktivieren Ihrer Logik-App im Cloud-Explorer](./media/manage-logic-apps-with-visual-studio/disable-logic-app-cloud-explorer.png)

<a name="enable-logic-apps"></a>

### <a name="enable-logic-apps"></a>Aktivieren von Logik-Apps

Öffnen Sie im Cloud-Explorer das Kontextmenü Ihrer Logik-App, und wählen Sie **Aktivieren** aus.

![Deaktivieren der Logik-App im Cloud-Explorer](./media/manage-logic-apps-with-visual-studio/enable-logic-app-cloud-explorer.png)

<a name="delete-logic-apps"></a>

## <a name="delete-logic-apps"></a>Löschen von Logik-Apps

Das Löschen einer Logik-App wirkt sich wie folgt auf Workflowinstanzen aus:

* Der Logic Apps-Dienst unterbricht alle aktiven und ausstehenden Ausführungen so gut wie möglich.

  Selbst bei einer großen Menge oder einem umfangreichen Backlog werden die meisten Ausführungen abgebrochen, bevor sie abgeschlossen oder gestartet werden. Es kann jedoch einige Zeit dauern, bis der Abbruchvorgang abgeschlossen ist. In der Zwischenzeit werden möglicherweise einige Ausführungen gestartet, während die Runtime den Abbruchprozess durchläuft.

* Der Logic Apps-Dienst erstellt keine neuen Workflowinstanzen und führt keine neuen Workflowinstanzen aus.

* Wenn Sie einen Workflow löschen und dann denselben Workflow neu erstellen, hat der neu erstellte Workflow nicht die gleichen Metadaten wie der gelöschte Workflow. Sie müssen jeden Workflow, der den gelöschten Workflow aufgerufen hat, neu speichern. Auf diese Weise ruft der Aufrufer die richtigen Informationen für den neu erstellten Workflow ab. Andernfalls schlagen Aufrufe des neu erstellten Workflows mit einem `Unauthorized`-Fehler fehl. Dieses Verhalten gilt auch für Workflows, die Artefakte in Integrationskonten und Workflows verwenden, welche Azure-Funktionen aufrufen.

Um Ihre Logik-App aus dem Azure-Portal zu löschen, öffnen Sie im Cloud-Explorer das Kontextmenü Ihrer Logik-App, und wählen Sie **Löschen** aus.

![Löschen Ihrer Logik-App aus dem Azure-Portal](./media/manage-logic-apps-with-visual-studio/delete-logic-app-from-azure-portal.png)

## <a name="troubleshooting"></a>Problembehandlung

Wenn Sie Ihr Logik-App-Projekt im Designer für Logik-Apps öffnen, erhalten Sie möglicherweise nicht die Option zur Auswahl Ihres Azure-Abonnements. Stattdessen wird Ihre Logik-App in einem Azure-Abonnement geöffnet, das Sie nicht verwenden möchten. Dieses Verhalten tritt auf, da Visual Studio nach dem Öffnen der JSON-Datei einer Logik-App das erste ausgewählte Abonnement für die zukünftige Verwendung zwischenspeichert. Gehen Sie folgendermaßen vor, um dieses Problem zu beheben:

* Benennen Sie die JSON-Datei der Logik-App um. Die Abonnement-Cache basiert auf dem Dateinamen.

* Um zuvor ausgewählte Abonnements für *alle* Logik-Apps in Ihrer Lösung (Projektmappe) zu entfernen, löschen Sie den verborgenen Ordner für Visual Studio-Einstellungen (.vs) im Verzeichnis Ihrer Projektmappe. Hier werden Ihre Abonnementinformationen gespeichert.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, bereitgestellte Logik-Apps mit Visual Studio zu verwalten. Als Nächstes lernen Sie, Logik-App-Definitionen für die Bereitstellung anzupassen:

> [!div class="nextstepaction"]
> [Erstellen von Logik-App-Definitionen in JSON](../logic-apps/logic-apps-author-definitions.md)
