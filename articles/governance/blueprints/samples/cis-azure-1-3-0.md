---
title: Blaupausenbeispiel „CIS Microsoft Azure Foundations Benchmark v1.3.0“
description: Übersicht über das Blaupausenbeispiel „CIS Microsoft Azure Foundations Benchmark v1.3.0“ Dieses Blaupausenbeispiel unterstützt Kunden bei der Bewertung spezifischer Kontrollen.
ms.date: 09/08/2021
ms.topic: sample
ms.openlocfilehash: 56189a41cf6debcbd0421ddc3227b4fcc84f15ad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599523"
---
# <a name="cis-microsoft-azure-foundations-benchmark-v130-blueprint-sample"></a>Blaupausenbeispiel „CIS Microsoft Azure Foundations Benchmark v1.3.0“

Im Blaupausenbeispiel „CIS Microsoft Azure Foundations Benchmark v1.3.0“ werden mithilfe von [Azure Policy](../../policy/overview.md) Schutzmaßnahmen für Governance bereitgestellt, die zum Bewerten von CIS Microsoft Azure Foundations Benchmark v1.3.0-Empfehlungen hilfreich sind. Diese Blaupause hilft Kunden bei der Bereitstellung eines Kernsatzes von Richtlinien für jede in Azure bereitgestellte Architektur, die CIS Microsoft Azure Foundations Benchmark v1.3.0-Empfehlungen implementieren muss.

## <a name="recommendation-mapping"></a>Empfehlungszuordnung

In der [Azure Policy-Empfehlungszuordnung](../../policy/samples/cis-azure-1-3-0.md) finden Sie Details zu den in dieser Blaupause enthaltenen Richtliniendefinitionen. Darüber hinaus erfahren Sie, wie diese Richtlinien den **Empfehlungen** in CIS Microsoft Azure Foundations Benchmark v1.3.0 entsprechen. Bei der Zuweisung zu einer Architektur werden Ressourcen von Azure Policy auf Nichtkonformität mit den zugewiesenen Richtliniendefinitionen überprüft. Weitere Informationen finden Sie unter [Was ist Azure Policy?](../../policy/overview.md).

## <a name="deploy"></a>Bereitstellen

Zum Bereitstellen des Blaupausenbeispiels „CIS Microsoft Azure Foundations Benchmark v1.3.0“ von Azure Blueprints müssen die folgenden Schritte ausgeführt werden:

> [!div class="checklist"]
> - Erstellen einer neuen Blaupause mithilfe des Beispiels
> - Markieren Ihrer Kopie des Beispiels als **Veröffentlicht**
> - Zuweisen Ihrer Kopie der Blaupause zu einem vorhandenen Abonnement

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free) erstellen, bevor Sie beginnen.

### <a name="create-blueprint-from-sample"></a>Erstellen einer Blaupause mithilfe des Beispiels

Implementieren Sie zuerst das Blaupausenbeispiel, indem Sie mithilfe des Beispiels eine neue Blaupause in Ihrer Umgebung erstellen.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Klicken Sie links auf der Seite **Erste Schritte** unter _Blaupause erstellen_ auf die Schaltfläche **Erstellen**.

1. Navigieren Sie unter _Weitere Beispiele_ zum Blaupausenbeispiel **CIS Microsoft Azure Foundations Benchmark v1.3.0**, und wählen Sie **Dieses Beispiel verwenden** aus.

1. Geben Sie die _Grundlagen_ des Blaupausenbeispiels ein:

   - **Name der Blaupause**: Geben Sie einen Namen für Ihre Kopie des Blaupausenbeispiels „CIS Microsoft Azure Foundations Benchmark“ an.
   - **Definitionsspeicherort**: Klicken Sie auf die Schaltfläche mit den Auslassungspunkten, und wählen Sie die Verwaltungsgruppe aus, in der Sie Ihre Kopie des Beispiels speichern möchten.

1. Wählen Sie oben auf der Seite die Registerkarte _Artefakte_ oder unten auf der Seite die Option **Weiter: Artefakte** aus.

1. Überprüfen Sie die Liste der Artefakte, die im Blaupausenbeispiel enthalten sind. Viele der Artefakte enthalten Parameter, die Sie später definieren. Wählen Sie **Entwurf speichern** aus, nachdem Sie das Blaupausenbeispiel überprüft haben.

### <a name="publish-the-sample-copy"></a>Veröffentlichen der Kopie des Beispiels

Ihre Kopie des Blaupausenbeispiels wurde jetzt in Ihrer Umgebung erstellt. Sie wird im Modus **Entwurf** erstellt und muss **veröffentlicht** werden, bevor sie zugewiesen und bereitgestellt werden kann. Die Kopie des Blaupausenbeispiels kann Ihrer Umgebung und Ihren Anforderungen angepasst werden. Durch diese Änderungen ist sie aber möglicherweise nicht mehr mit den Empfehlungen zu „CIS Microsoft Azure Foundations Benchmark v1.3.0“ konform.

1. Wählen Sie **Alle Dienste** im linken Bereich aus. Suchen Sie nach **Blaupausen**, und wählen Sie die Option aus.

1. Wählen Sie links die Seite **Blaupausendefinitionen** aus. Verwenden Sie die Filter, um Ihre Kopie des Blaupausenbeispiels zu suchen, und wählen Sie es aus.

1. Wählen Sie oben auf der Seite die Option **Blaupause veröffentlichen** aus. Geben Sie auf der neuen Seite rechts für Ihre Kopie des Blaupausenbeispiels eine **Version** an. Diese Eigenschaft ist hilfreich, wenn Sie später Änderungen vornehmen. Geben Sie **Änderungshinweise** an, etwa „Erste vom Blaupausenbeispiel ‚CIS Microsoft Azure Foundations Benchmark‘ veröffentlichte Version“. Wählen Sie dann unten auf der Seite **Veröffentlichen** aus.

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

|Artefaktname|Artefakttyp|Parametername|BESCHREIBUNG|
|-|-|-|-|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Liste der zur Verwendung genehmigten VM-Erweiterungen|Eine durch Semikolons getrennte Liste mit VM-Erweiterungen. Zum Anzeigen einer vollständigen Liste der Erweiterungen verwenden Sie den Azure PowerShell-Befehl „Get-AzVMExtensionImage“.|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: SQL Managed Instance-Instanzen müssen kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für SQL Server müssen die Einstellungen der Sicherheitsrisikobewertung eine E-Mail-Adresse für den Empfang von Überprüfungsberichten enthalten|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: In Azure Data Lake Store müssen Diagnoseprotokolle aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Die Datenträgerverschlüsselung sollte auf virtuelle Computer angewendet werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für den Schlüsseltresor muss der Löschschutz aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für die API-App muss „Clientzertifikate (eingehende Clientzertifikate)“ auf „Ein“ festgelegt sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: SQL Server-Instanzen müssen kundenseitig verwaltete Schlüssel zur Verschlüsselung ruhender Daten verwenden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Funktions-App muss verwaltete Identität verwenden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Azure Defender für Key Vault muss aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Benutzerdefinierte Rollen für Abonnementbesitzer dürfen nicht vorhanden sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für Schlüssel muss ein Ablaufdatum festgelegt sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Transparent Data Encryption für SQL-Datenbanken sollte aktiviert werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für SQL Managed Instance muss eine Sicherheitsrisikobewertung aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Bei Verwendung als Teil der API-App neueste PHP-Version sicherstellen|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Ein Azure Active Directory-Administrator sollte für SQL-Server-Instanzen bereitgestellt werden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Azure Defender für App Service muss aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Speicherkonten müssen den Netzwerkzugriff mithilfe von VNET-Regeln einschränken|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Web-App muss verwaltete Identität verwenden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: SSH-Zugriff über das Internet blockieren|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Nicht angefügte Datenträger müssen verschlüsselt werden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Azure Defender für Speicher muss aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Netzwerkzugriff auf Speicherkonten einschränken|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: In Logic Apps müssen Diagnoseprotokolle aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: In IoT Hub müssen Diagnoseprotokolle aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: „Nur FTPS“ muss in Funktions-App erforderlich sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für bestimmte Sicherheitsvorgänge muss eine Aktivitätsprotokollwarnung vorliegen (Microsoft.Security/securitySolutions/delete)|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für bestimmte Sicherheitsvorgänge muss eine Aktivitätsprotokollwarnung vorliegen (Microsoft.Security/securitySolutions/write)|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Sichere Übertragung in Speicherkonten sollte aktiviert werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: In Batch-Konten müssen Diagnoseprotokolle aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für Ihr Abonnement muss die automatische Bereitstellung des Log Analytics-Agents aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Stellen Sie sicher, dass die neueste Java-Version angegeben ist, wenn sie als Teil der Web-App verwendet wird.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: FTPS muss in Web-App erforderlich sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Azure Defender für Server muss aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Abonnements müssen eine Kontakt-E-Mail-Adresse für Sicherheitsprobleme aufweisen|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Der öffentliche Zugriff auf Speicherkonten muss untersagt sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Azure Defender für Kubernetes muss aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für PostgreSQL-Datenbankserver muss die Verbindungsdrosselung aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Sicherstellen, dass für die Web-App „Clientzertifikate (eingehende Clientzertifikate)“ auf „Ein“ festgelegt ist|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Externe Konten mit Schreibberechtigungen sollten aus Ihrem Abonnement entfernt werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Externe Konten mit Leseberechtigungen sollten aus Ihrem Abonnement entfernt werden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Azure Defender für SQL Server-Instanzen auf Computern muss aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: E-Mail-Benachrichtigung bei Warnungen mit hohem Schweregrad muss aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für das Speicherkonto muss der kundenseitig verwaltete Schlüssel zur Verschlüsselung verwendet werden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Stellen Sie sicher, dass die neueste Python-Version angegeben ist, wenn sie als Teil der Web-App verwendet wird.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Stellen Sie sicher, dass die neueste Python-Version angegeben ist, wenn sie als Teil der Funktions-App verwendet wird.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Stellen Sie sicher, dass die neueste PHP-Version angegeben ist, wenn sie als Teil der Web-App verwendet wird.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Sicherstellen, dass die neueste Python-Version angegeben ist, wenn sie als Teil der API-App verwendet wird|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: In Virtual Machine Scale Sets müssen Diagnoseprotokolle aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Azure Defender für Azure SQL-Datenbank-Server muss aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: In Event Hub müssen Diagnoseprotokolle aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Systemupdates sollten auf Ihren Computern installiert sein.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Sicherstellen, dass die neueste Java-Version angegeben ist, wenn sie als Teil der API-App verwendet wird|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: SQL Server-Instanzen müssen mit einer Überwachungsaufbewahrungsdauer von mindestens 90 Tagen konfiguriert werden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Stellen Sie sicher, dass die neueste HTTP-Version angegeben ist, wenn sie zum Ausführen der Web-App verwendet wird.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: In Ihrer API-App sollte die neueste TLS-Version verwendet werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: MFA sollte für Konten mit Schreibrechten für Ihr Abonnement aktiviert werden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für Ihre Web-App muss die Authentifizierung aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für Geheimnisse muss ein Ablaufdatum festgelegt sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Sicherstellen, dass die neueste HTTP-Version angegeben ist, wenn sie zum Ausführen der API-App verwendet wird|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: „Nur FTPS“ muss in API-App erforderlich sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Sicherstellen, dass die neueste Java-Version angegeben ist, wenn sie als Teil der Funktions-App verwendet wird|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Zugriff auf Webanwendung nur über HTTPS gestatten|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Die Überwachung in SQL Server muss aktiviert werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: MFA sollte für Konten mit Besitzerberechtigungen in Ihrem Abonnement aktiviert sein.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Advanced Data Security muss für Ihre SQL-Server aktiviert werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für SQL Managed Instance muss Advanced Data Security aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Rollenbasierte Zugriffssteuerung (RBAC) für Kubernetes Service verwenden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Fehlenden Endpoint Protection-Schutz in Azure Security Center überwachen|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: In den Suchdiensten müssen Diagnoseprotokolle aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: In App Services müssen Diagnoseprotokolle aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für bestimmte administrative Vorgänge muss eine Aktivitätsprotokollwarnung vorliegen (Microsoft.Network/networkSecurityGroups/delete) |Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für bestimmte administrative Vorgänge muss eine Aktivitätsprotokollwarnung vorliegen (Microsoft.Network/networkSecurityGroups/securityRules/delete) |Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für bestimmte administrative Vorgänge muss eine Aktivitätsprotokollwarnung vorliegen (Microsoft.Network/networkSecurityGroups/securityRules/write) |Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für bestimmte administrative Vorgänge muss eine Aktivitätsprotokollwarnung vorliegen (Microsoft.Network/networkSecurityGroups/write) |Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für bestimmte administrative Vorgänge muss eine Aktivitätsprotokollwarnung vorliegen (Microsoft.Sql/servers/firewallRules/delete) |Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für bestimmte administrative Vorgänge muss eine Aktivitätsprotokollwarnung vorliegen (Microsoft.Sql/servers/firewallRules/write) |Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Nur genehmigte VM-Erweiterungen dürfen installiert werden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Azure Defender für Containerregistrierungen muss aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: API-App muss verwaltete Identität verwenden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für Ihre API-App muss die Authentifizierung aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für bestimmte Richtlinienvorgänge muss eine Aktivitätsprotokollwarnung vorliegen (Microsoft.Authorization/policyAssignments/delete) |Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für bestimmte Richtlinienvorgänge muss eine Aktivitätsprotokollwarnung vorliegen (Microsoft.Authorization/policyAssignments/write) |Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für Ihre Funktions-App muss die Authentifizierung aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: In Data Lake Analytics müssen Diagnoseprotokolle aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Speicherkonten müssen Zugriff von vertrauenswürdigen Microsoft-Diensten zulassen|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: In Key Vault müssen Diagnoseprotokolle aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Erzwingen einer SSL-Verbindung muss für PostgreSQL-Datenbankserver aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Stellen Sie sicher, dass die neueste HTTP-Version angegeben ist, wenn sie zum Ausführen der Funktions-App verwendet wird.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: MFA sollte für Ihre Abonnementkonten mit Leseberechtigungen aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: RDP-Zugriff über das Internet blockieren|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Erzwingen einer SSL-Verbindung muss für MySQL-Datenbankserver aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Sicherstellen, dass für die Funktions-App „Clientzertifikate (eingehende Clientzertifikate)“ auf „Ein“ festgelegt ist|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für PostgreSQL-Datenbankserver müssen Protokollprüfpunkte aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für PostgreSQL-Datenbankserver müssen Protokollverbindungen aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Für PostgreSQL-Datenbankserver müssen Verbindungstrennungen protokolliert werden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Für Ihre SQL Server-Instanzen muss eine Sicherheitsrisikobewertung aktiviert sein.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: In Ihrer Web-App sollte die neueste TLS-Version verwendet werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: Externe Konten mit Besitzerberechtigungen sollten aus Ihrem Abonnement entfernt werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: In Service Bus müssen Diagnoseprotokolle aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: In Azure Stream Analytics müssen Diagnoseprotokolle aktiviert sein|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für die Richtlinie: In Ihrer Funktions-App sollte die neueste TLS-Version verwendet werden.|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Auswirkung für Richtlinie: Das Speicherkonto, das den Container mit Aktivitätsprotokollen enthält, muss mit BYOK verschlüsselt werden|Weitere Informationen zu Auswirkungen finden Sie unter [https://aka.ms/policyeffects](../../policy/concepts/effects.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|AKS-Cluster in Überwachung einschließen, wenn Diagnoseprotokolle für VM-Skalierungsgruppen aktiviert werden||
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Neueste Java-Version für App Services|Neueste unterstützte Java-Version für App Services|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Neueste Python-Version für Linux für App Services|Neueste unterstützte Python-Version für App Services|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Liste der Regionen, in denen Network Watcher aktiviert werden soll|Führen Sie den PowerShell-Befehl „Get-AzLocation“ aus, um eine vollständige Liste der Regionen anzuzeigen.|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Neueste PHP-Version für App Services|Neueste unterstützte PHP-Version für App Services|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Erforderliche Aufbewahrungsdauer für Ressourcenprotokolle (Tage)|Weitere Informationen zu Ressourcenprotokollen finden Sie unter [https://aka.ms/resourcelogs](../../../azure-monitor/essentials/resource-logs.md). |
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Name der Ressourcengruppe für Network Watcher|Name der Ressourcengruppe, in der sich die Network Watcher-Instanzen befinden|
|CIS Microsoft Azure Foundations Benchmark v1.3.0|Richtlinienzuweisung|Erforderliche Überwachungseinstellung für SQL Server||

## <a name="next-steps"></a>Nächste Schritte

Weitere Artikel zu Blaupausen und ihrer Nutzung:

- Erfahren Sie mehr über den [Lebenszyklus von Blaupausen](../concepts/lifecycle.md).
- Machen Sie sich mit der Verwendung [statischer und dynamischer Parameter](../concepts/parameters.md) vertraut.
- Erfahren Sie, wie Sie die [Abfolge von Blaupausen](../concepts/sequencing-order.md) anpassen können.
- Erfahren Sie, wie Sie [Ressourcen in Blaupausen sperren](../concepts/resource-locking.md) können.
- Lernen Sie, wie Sie [vorhandene Zuweisungen aktualisieren](../how-to/update-existing-assignments.md).
