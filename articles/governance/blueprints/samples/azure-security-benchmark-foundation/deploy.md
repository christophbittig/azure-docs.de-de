---
title: Bereitstellen des Blaupausenbeispiels „Azure Security Benchmark Foundation“
description: Bereitstellungsschritte für das Blaupausenbeispiel „Azure Security Benchmark Foundation“, einschließlich Details zum Blaupausenartefaktparameter
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 51765db4a07ffe9399a09aa75c0b282c3fbc94ff
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599485"
---
# <a name="deploy-the-azure-security-benchmark-foundation-blueprint-sample"></a>Bereitstellen des Blaupausenbeispiels „Azure Security Benchmark Foundation“

Zum Bereitstellen des Blaupausenbeispiels „Azure Security Benchmark Foundation“ müssen die folgenden Schritte ausgeführt werden:

> [!div class="checklist"]
> - Erstellen einer neuen Blaupause mithilfe des Beispiels
> - Markieren Ihrer Kopie des Beispiels als **Veröffentlicht**
> - Zuweisen Ihrer Kopie der Blaupause zu einem vorhandenen Abonnement

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.

## <a name="create-blueprint-from-sample"></a>Erstellen einer Blaupause mithilfe des Beispiels

Implementieren Sie zuerst das Blaupausenbeispiel, indem Sie mithilfe des Beispiels eine neue Blaupause in Ihrer Umgebung erstellen.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Klicken Sie links auf der Seite **Erste Schritte** unter _Blaupause erstellen_ auf die Schaltfläche **Erstellen**.

1. Suchen Sie unter _Weitere Beispiele_ nach dem Blaupausenbeispiel **Azure Security Benchmark Foundation**, und wählen Sie **Dieses Beispiel verwenden** aus.

1. Geben Sie die _Grundlagen_ des Blaupausenbeispiels ein:

   - **Name der Blaupause**: Geben Sie einen Namen für Ihre Kopie des Blaupausenbeispiels „Azure Security Benchmark Foundation“ an.
   - **Definitionsspeicherort**: Klicken Sie auf die Schaltfläche mit den Auslassungspunkten, und wählen Sie die Verwaltungsgruppe aus, in der Sie Ihre Kopie des Beispiels speichern möchten.

1. Wählen Sie oben auf der Seite die Registerkarte _Artefakte_ oder unten auf der Seite die Option **Weiter: Artefakte** aus.

1. Überprüfen Sie die Liste der Artefakte, aus denen das Blaupausenbeispiel besteht. Viele der Artefakte enthalten Parameter, die Sie später definieren. Wählen Sie **Entwurf speichern** aus, nachdem Sie das Blaupausenbeispiel überprüft haben.

## <a name="publish-the-sample-copy"></a>Veröffentlichen der Kopie des Beispiels

Ihre Kopie des Blaupausenbeispiels wurde jetzt in Ihrer Umgebung erstellt. Sie wird im Modus **Entwurf** erstellt und muss **veröffentlicht** werden, bevor sie zugewiesen und bereitgestellt werden kann. Die Kopie des Blaupausenbeispiels kann an Ihre Umgebung und Ihre Anforderungen angepasst werden. Durch diese Änderungen ist sie aber möglicherweise nicht mehr mit der Azure Security Benchmark Foundation-Blaupause konform.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Verwenden Sie die Filter, um Ihre Kopie des Blaupausenbeispiels zu suchen, und wählen Sie es aus.

1. Wählen Sie oben auf der Seite die Option **Blaupause veröffentlichen** aus. Geben Sie auf der neuen Seite rechts für Ihre Kopie des Blaupausenbeispiels eine **Version** an. Diese Eigenschaft ist hilfreich, wenn Sie später Änderungen vornehmen. Geben Sie **Änderungshinweise** an, etwa „Erste Version aus dem Azure Security Benchmark Foundation-Blaupausenbeispiel veröffentlicht“. Wählen Sie dann unten auf der Seite **Veröffentlichen** aus.

## <a name="assign-the-sample-copy"></a>Zuweisen der Kopie des Beispiels

Nachdem die Kopie des Blaupausenbeispiels erfolgreich **veröffentlicht** wurde, kann sie einem Abonnement innerhalb der Verwaltungsgruppe, in der sie gespeichert wurde, zugewiesen werden. In diesem Schritt werden Parameter angegeben, damit jede Bereitstellung der Kopie des Blaupausenbeispiels eindeutig ist.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Verwenden Sie die Filter, um Ihre Kopie des Blaupausenbeispiels zu suchen, und wählen Sie es aus.

1. Wählen Sie oben auf der Seite mit der Blaupausendefinition die Option **Blaupause zuweisen** aus.

1. Geben Sie die Parameterwerte für die Blaupausenzuweisung an:

   - Grundlagen
       - **Abonnements**: Wählen Sie mindestens eines der Abonnements in der Verwaltungsgruppe aus, in der Sie die Kopie des Blaupausenbeispiels gespeichert haben. Wenn Sie mehrere Abonnements auswählen, wird für jedes Abonnement eine Zuweisung mit den eingegebenen Parametern erstellt.
     - **Zuweisungsname:** Der Name wird basierend auf dem Namen der Blaupause vorab ausgefüllt.
       Sie können ihn nach Bedarf ändern oder unverändert übernehmen.
     - **Standort**: Wählen Sie eine Region aus, in der die verwaltete Identität erstellt werden soll.
     - Azure Blueprint verwendet diese verwaltete Identität zum Bereitstellen aller Artefakte in der zugewiesenen Blaupause.
       Weitere Informationen finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Version der Blaupausendefinition:** Wählen Sie eine **veröffentlichte** Version Ihrer Kopie des Blaupausenbeispiels aus.

   - Zuweisung sperren

     Wählen Sie die Blaupausensperreinstellung für die Umgebung aus. Weitere Informationen finden Sie unter [Grundlegendes zur Ressourcensperre in Azure Blueprint](../../concepts/resource-locking.md).

   - Verwaltete Identität

     Wählen Sie entweder die Standardoption für die _systemseitig zugewiesene verwaltete Identität_ oder die Option für die _benutzerseitig zugewiesene Identität_ aus.

   - Blaupausenparameter

     Die in diesem Abschnitt definierten Parameter werden in vielen der Artefakte in der Blaupausendefinition verwendet, um Konsistenz zu gewährleisten.

     - **Präfix für Ressourcen und Ressourcengruppen**: Diese Zeichenfolge wird als Präfix für alle Ressourcen- und Ressourcengruppennamen verwendet.
     - **Hub-Name:** Name des Hubs
     - **Protokollbeibehaltung (Tage)** : Anzahl der Tage, für die Protokolle beibehalten werden. Bei der Eingabe von „0“ werden Protokolle unbegrenzt beibehalten.
     - **Bereitstellen des Hubs**: Geben Sie „true“ oder „false“ ein, um anzugeben, ob durch die Zuweisung die Hubkomponenten der Architektur bereitgestellt werden.
     - **Hubspeicherort**: Speicherort für die Hubressourcengruppe
     - **Ziel-IP-Adressen**: Ziel-IP-Adressen für ausgehende Konnektivität; durch Trennzeichen getrennte Liste der IP-Adressen oder IP-Bereichspräfixe
     - **Network Watcher-Name**: Name der Network Watcher-Ressource
     - **Network Watcher-Ressourcengruppenname**: Name der Network Watcher-Ressourcengruppe
     - **Aktivieren von DDoS Protection**: Geben Sie „true“ oder „false“ ein, um anzugeben, ob DDoS Protection im virtuellen Netzwerk aktiviert ist.

     > [!NOTE]
     > Wenn Network Watcher bereits aktiviert ist, wird empfohlen, die vorhandene Network Watcher-Ressourcengruppe zu verwenden. Außerdem müssen Sie den Speicherort für die vorhandene Network Watcher-Ressourcengruppe für den Artefaktparameter **Network Watcher-Speicherort für Ressourcengruppen** angeben.

   - Artefaktparameter

     Die in diesem Abschnitt definierten Parameter gelten für das Artefakt, unter dem sie definiert werden. Diese Parameter sind [dynamische Parameter](../../concepts/parameters.md#dynamic-parameters), da sie während der Zuweisung der Blaupause definiert werden. Eine vollständige Liste der Artefaktparameter und die zugehörigen Beschreibungen finden Sie in der [Tabelle der Artefaktparameter](#artifact-parameters-table).

1. Nachdem Sie alle Parameter eingegeben haben, wählen Sie unten auf der Seite die Option **Zuweisen** aus. Die Blaupausenzuweisung wird erstellt, und die Artefaktbereitstellung wird gestartet. Die Bereitstellung dauert ungefähr eine Stunde. Um den Status der Bereitstellung zu überprüfen, öffnen Sie die Blaupausenzuweisung.

> [!WARNING]
> Der Azure Blueprints-Dienst und die integrierten Blaupausenbeispiele sind **kostenlos**. Azure-Ressourcen werden [nach Produkt abgerechnet](https://azure.microsoft.com/pricing/). Verwenden Sie den [Preisrechner](https://azure.microsoft.com/pricing/calculator/) zum Schätzen der Kosten für die Ausführung der Ressourcen, die mit diesem Blaupausenbeispiel bereitgestellt werden.

## <a name="artifact-parameters-table"></a>Tabelle der Artefaktparameter

Die folgende Tabelle enthält eine Aufstellung der Blaupausenparameter:

|Artefaktname|Artefakttyp|Parametername|BESCHREIBUNG|
|-|-|-|-|
|Hubressourcengruppe|Resource group|Ressourcengruppenname|Gesperrt: Verkettet das Präfix mit dem Hubnamen.|
|Hubressourcengruppe|Resource group|Ressourcengruppenstandort|Gesperrt: Verwendet den Hubspeicherort.|
|Azure Firewall-Vorlage|Resource Manager-Vorlage|Private IP-Adresse für Azure Firewall||
|Vorlage für Azure Log Analytics und Diagnose|Resource Manager-Vorlage|Log Analytics-Arbeitsbereich – Standort|Der Standort, an dem der Log Analytics-Arbeitsbereich erstellt wird. Führen Sie `Get-AzLocation | Where-Object Providers -like 'Microsoft.OperationalInsights' | Select DisplayName` in Azure PowerShell aus, um verfügbare Regionen anzuzeigen.|
|Vorlage für Azure Log Analytics und Diagnose|Resource Manager-Vorlage|Azure Automation-Konto-ID (optional) |Ressourcen-ID des Automation-Kontos. Wird zum Erstellen eines verknüpften Diensts zwischen Log Analytics und einem Automation-Konto verwendet.|
|Azure-Vorlage für Netzwerksicherheitsgruppen|Resource Manager-Vorlage|Aktivieren von NSG-Datenflussprotokollen|Geben Sie „true“ oder „false“ ein, um NSG-Datenflussprotokolle zu aktivieren oder deaktivieren.|
|Vorlage für Azure Virtual Network-Hubs|Resource Manager-Vorlage|Adresspräfix des virtuellen Netzwerks|Adresspräfix des virtuellen Netzwerks für das virtuelle Hubnetzwerk|
|Vorlage für Azure Virtual Network-Hubs|Resource Manager-Vorlage|Adresspräfix des Firewallsubnetzes|Adresspräfix des Firewallsubnetzes für das virtuelle Hubnetzwerk|
|Vorlage für Azure Virtual Network-Hubs|Resource Manager-Vorlage|Adresspräfix des Bastion-Subnetzes|Adresspräfix des Bastion-Subnetzes für das virtuelle Hubnetzwerk|
|Vorlage für Azure Virtual Network-Hubs|Resource Manager-Vorlage|Adresspräfix des Gatewaysubnetzes|Adresspräfix des Gatewaysubnetzes für das virtuelle Hubnetzwerk|
|Vorlage für Azure Virtual Network-Hubs|Resource Manager-Vorlage|Adresspräfix des Verwaltungssubnetzes|Adresspräfix des Verwaltungssubnetzes für das virtuelle Hubnetzwerk|
|Vorlage für Azure Virtual Network-Hubs|Resource Manager-Vorlage|Adresspräfix des Jumpboxsubnetzes|Adresspräfix des Jumpboxsubnetzes für das virtuelle Hubnetzwerk|
|Vorlage für Azure Virtual Network-Hubs|Resource Manager-Vorlage|Subnetzadressnamen (optional)|Array von Subnetznamen, die im virtuellen Hubnetzwerk bereitgestellt werden sollen. Beispiel: subnet1, subnet2|
|Vorlage für Azure Virtual Network-Hubs|Resource Manager-Vorlage|Subnetzadresspräfixe (optional)|Array von IP-Adresspräfixen für optionale Subnetze für das virtuelle Hubnetzwerk; Beispiel: 10.0.7.0/24, 10.0.8.0/24|
|Spoke-Ressourcengruppe|Resource group|Ressourcengruppenname|Gesperrt: Verkettet das Präfix mit dem Spoke-Namen.|
|Spoke-Ressourcengruppe|Resource group|Ressourcengruppenstandort|Gesperrt: Verwendet den Hubstandort.|
|Azure Virtual Network-Spoke-Vorlage|Resource Manager-Vorlage|Bereitstellen von Spoke|Geben Sie „true“ oder „false“ ein, um anzugeben, ob durch die Zuweisung die Spoke-Komponenten der Architektur bereitgestellt werden.|
|Azure Virtual Network-Spoke-Vorlage|Resource Manager-Vorlage|Hubabonnement-ID|Abonnement-ID, unter der der Hub bereitgestellt wird. Der Standardwert ist das Abonnement, in dem sich die Blaupausendefinition befindet.|
|Azure Virtual Network-Spoke-Vorlage|Resource Manager-Vorlage|Spoke-Name|Name der Spoke-Komponente|
|Azure Virtual Network-Spoke-Vorlage|Resource Manager-Vorlage|Adresspräfix für Virtual Network|Virtual Network-Adresspräfix für das virtuelle Spoke-Netzwerk|
|Azure Virtual Network-Spoke-Vorlage|Resource Manager-Vorlage|Adresspräfix des Subnetzes|Adresspräfix des Subnetzes für das virtuelle Spoke-Netzwerk|
|Azure Virtual Network-Spoke-Vorlage|Resource Manager-Vorlage|Subnetzadressnamen (optional)|Array von Subnetznamen, die im virtuellen Spoke-Netzwerk bereitgestellt werden sollen. Beispiel: subnet1, subnet2|
|Azure Virtual Network-Spoke-Vorlage|Resource Manager-Vorlage|Subnetzadresspräfixe (optional)|Array von IP-Adresspräfixen für optionale Subnetze für das virtuelle Spoke-Netzwerk; Beispiel: 10.0.7.0/24, 10.0.8.0/24|
|Azure Virtual Network-Spoke-Vorlage|Resource Manager-Vorlage|Bereitstellen von Spoke|Geben Sie „true“ oder „false“ ein, um anzugeben, ob durch die Zuweisung die Spoke-Komponenten der Architektur bereitgestellt werden.|
|Azure Network Watcher-Vorlage|Resource Manager-Vorlage|Network Watcher-Standort|Speicherort der Network Watcher-Ressource|
|Azure Network Watcher-Vorlage|Resource Manager-Vorlage|Network Watcher-Ressourcengruppenstandort|Ist Network Watcher bereits aktiviert, **muss** dieser Parameterwert mit dem Standort der vorhandenen Network Watcher-Ressourcengruppe identisch sein.|

## <a name="troubleshooting"></a>Problembehandlung

Wenn der Fehler `The resource group 'NetworkWatcherRG' failed to deploy due to the
following error: Invalid resource group location '{location}'. The Resource group already exists in
location '{location}'.` auftritt, prüfen Sie, ob der Blaupausenparameter **Network Watcher-Ressourcengruppenname** den bestehenden Network Watcher-Ressourcengruppennamen angibt und der Blaupausenparameter **Network Watcher-Ressourcengruppenspeicherort** auf den bestehenden Network Watcher-Ressourcengruppenspeicherort verweist.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie sich mit den Schritten zum Bereitstellen des Blaupausenbeispiels „Azure Security Benchmark Foundation“ vertraut gemacht haben, finden Sie in den folgenden Artikeln Informationen zur Architektur:

> [!div class="nextstepaction"]
> [Übersicht über das Blaupausenbeispiel „Azure Security Benchmark Foundation“](./index.md)

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../../how-to/update-existing-assignments.md).
