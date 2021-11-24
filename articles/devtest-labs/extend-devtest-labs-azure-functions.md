---
title: Erweitern von Azure DevTest Labs mit Azure Functions
description: Hier erfahren Sie, wie Sie Azure DevTest Labs mit Azure Functions erweitern.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: a3160335bc233d0873c6e9cae5d32aef4f494ab9
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132401115"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Erweitern von DevTest Labs mithilfe von Azure Functions
Mithilfe von Azure Functions können Sie weitere Szenarien unterstützen, die über die bereits bestehenden Möglichkeiten von DevTest Labs hinausgehen. Sie können Azure Functions verwenden, um die integrierten Funktionen des Diensts entsprechend Ihren unternehmensspezifischen Anforderungen zu erweitern. Die folgende Liste enthält einige der möglichen Szenarien. In diesem Artikel wird gezeigt, wie Sie eines dieser Beispielszenarien implementieren.

- Eine Zusammenfassung auf oberster Ebene der VMs im Lab bereitstellen
- [Konfigurieren eines Labs zur Verwendung eines Remotedesktopgateways](configure-lab-remote-desktop-gateway.md)
- Auf der internen Supportseite Konformitätsberichte erstellen
- Benutzern die Durchführung von Vorgängen ermöglichen, die erweiterte Berechtigungen im Abonnement erfordern
- [Workflows basierend auf DevTest Labs-Ereignissen starten](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Übersicht
[Azure Functions](../azure-functions/functions-overview.md) ist eine serverlose Computing-Plattform in Azure. Durch die Verwendung von Azure Functions in einer Lösung mit DevTest Labs können wir die vorhandenen Funktionen mit unserem eigenen benutzerdefinierten Code erweitern. Weitere Informationen zu Azure Functions finden Sie in der [Azure Functions-Dokumentation](../azure-functions/functions-overview.md). Um zu veranschaulichen, wie Sie mit Azure Functions Ihre Anforderungen erfüllen oder Szenarien in DevTest Labs implementieren können, wird in diesem Artikel als Beispiel die Bereitstellung einer Zusammenfassung auf oberster Ebene der VMs im Lab verwendet:

**Beispielanforderung/-szenario:** Benutzer können Details zu allen VMs in einem Lab anzeigen, einschließlich Betriebssystem, Besitzer und angewandter Artefakte.  Wenn das Artefakt zum **Anwenden von Windows-Updates** in letzter Zeit nicht angewendet wurde, gibt es zudem eine einfache Möglichkeit, um dies nachzuholen.

Zum Implementieren des Szenarios verwenden Sie wie in der folgenden Abbildung gezeigt zwei Funktionen:  

![Allgemeiner Ablauf](./media/extend-devtest-labs-azure-functions/flow.png)

Den Quellcode für diese Beispielfunktionen finden Sie im [GitHub-Repository für DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) (C# -und PowerShell-Implementierungen).

- **UpdateInternalSupportPage:** Diese Funktion fragt DevTest Labs ab und aktualisiert die interne Supportseite direkt mit Details zu den VMs.
- **ApplyWindowsUpdateArtifact:** Diese Funktion wendet für eine VM in einem Lab das Artefakt **Windows Update** an.

## <a name="how-it-works"></a>Funktionsweise
Wenn Benutzer die **interne Supportseite** in DevTest Labs aufrufen, sehen sie eine Seite mit vorab aufgefüllten Informationen zu VMs, Labbesitzern und Supportkontakten.  

Wenn Sie die Schaltfläche **Select here to refresh** (Zum Aktualisieren hier klicken) auswählen, ruft die Seite die erste Azure-Funktion auf: **UpdateInternalSupportPage**. Die Funktion fragt Informationen von DevTest Labs ab und generiert die **interne Supportseite** dann erneut mit den neuen Informationen.

Es gibt eine weitere Maßnahme, die Sie ergreifen können. Für alle VMs, auf die Windows Update-Artefakte nicht angewendet wurden, gibt es eine Schaltfläche zum Anwenden von Windows-Updates auf die VM. Wenn Sie für eine VM die Schaltfläche ***Windows Update ausführen** auswählen, ruft die Seite die zweite Azure-Funktion auf: **ApplyWindowsUpdateArtifact**. Diese Funktion überprüft, ob die VM ausgeführt wird, und wendet das Artefakt [Windows Update](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates) direkt an, wenn dies der Fall ist.

## <a name="step-by-step-walkthrough"></a>Ausführliche exemplarische Vorgehensweise
Dieser Abschnitt enthält schrittweise Anweisungen zum Einrichten von Azure-Ressourcen, die zum Aktualisieren der **internen Supportseite** erforderlich sind. Diese exemplarische Vorgehensweise zeigt ein Beispiel für die Erweiterung von DevTest Labs. Sie können dieses Muster für andere Szenarien verwenden.

### <a name="step-1-create-a-service-principal"></a>Schritt 1: Erstellen eines Dienstprinzipals 
Der erste Schritt besteht darin, einen Dienstprinzipal mit Berechtigungen für das Abonnement mit dem Lab zu erstellen. Der Dienstprinzipal muss die kennwortbasierte Authentifizierung verwenden. Sie können für diesen Schritt die [Azure-Befehlszeilenschnittstelle (Azure CLI)](/cli/azure/create-an-azure-service-principal-azure-cli), [Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps) oder das [Azure-Portal](../active-directory/develop/howto-create-service-principal-portal.md) verwenden. Wenn Sie bereits einen Dienstprinzipal haben, können Sie diesen Schritt überspringen.

Notieren Sie sich die **Anwendungs-ID**, den **Schlüssel** und die **Mandanten-ID** für den Dienstprinzipal. Diese Daten benötigen Sie an späterer Stelle in dieser exemplarischen Vorgehensweise. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>Schritt 2: Herunterladen und Öffnen des Beispiels in Visual Studio 2019
Laden Sie eine Kopie des [C#-Beispiels für Azure Functions](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) lokal herunter (klonen Sie das Repository, oder laden Sie es [hier](https://github.com/Azure/azure-devtestlab/archive/master.zip) herunter).  

1. Öffnen Sie die Beispielprojektmappe in Visual Studio 2019.  
1. Installieren Sie die Workload **Azure-Entwicklung** für Visual Studio, wenn sie nicht bereits installiert ist. Sie kann über das Menüelement **Tools** -> **Tools und Features abrufen** installiert werden.

    ![Workload „Azure-Entwicklung“](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Erstellen Sie die Projektmappe. Klicken Sie auf **Erstellen** und dann auf **Projektmappe erstellen**.

### <a name="step-3-deploy-the-sample-to-azure"></a>Schritt 3: Bereitstellen des Beispiels in Azure
Klicken Sie in Visual Studio im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **AzureFunctions**, und wählen Sie dann **Veröffentlichen** aus. Folgen Sie den Schritten im Assistenten, um die Veröffentlichung in einer neuen oder vorhandenen Azure-Funktions-App abzuschließen. Ausführliche Informationen zum Entwickeln und Bereitstellen von Azure Functions mit Visual Studio finden Sie unter [Entwickeln von Azure Functions mithilfe von Visual Studio](../azure-functions/functions-develop-vs.md).

![Dialogfeld „Veröffentlichen“](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>Schritt 4: Sammeln der Anwendungseinstellungen
Nachdem die Funktionen veröffentlicht wurden, müssen Sie URLs für diese Funktionen aus dem Azure-Portal abrufen. 

1. Navigieren Sie zum [Azure-Portal](https://portal.azure.com). 
1. Suchen Sie nach der Funktions-App.
1. Wählen Sie auf der Seite **Funktionen-Apps** die Funktion aus. 
1. Wählen Sie wie in der folgenden Abbildung dargestellt **Funktions-URL abrufen** aus. 

    ![URLs von Azure-Funktionen](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Kopieren und speichern Sie die URL. Wiederholen Sie diese Schritte für die andere Azure-Funktion. 

Außerdem benötigen Sie Informationen zum Dienstprinzipal, z. B. Anwendungs-ID, Schlüssel und Mandanten-ID.


### <a name="step-5--update-application-settings"></a>Schritt 5: Aktualisieren der Anwendungseinstellungen
Wählen Sie nach dem Veröffentlichen der Azure-Funktion in Visual Studio unter **Aktionen** die Option **Azure App Service-Einstellungen bearbeiten** aus. Aktualisieren Sie die folgenden Anwendungseinstellungen (remote):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (Standardwert: 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Anwendungseinstellungen](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>Schritt 6: Testen der Azure-Funktion
Der letzte Schritt in dieser exemplarischen Vorgehensweise ist das Testen der Azure-Funktion.  

1. Navigieren Sie in der Funktions-App, die Sie in Schritt 3 erstellt haben, zur Funktion **UpdateInternalSupportPage**. 
1. Wählen Sie rechts auf der Seite **Test** aus. 
1. Geben Sie die Routeneigenschaften ein (LABNAME, RESOURCEGROUPNAME und SUBSCRIPTIONID).
1. Wählen Sie **Ausführen** aus, um die Funktion auszuführen.  

    Diese Funktion aktualisiert die interne Supportseite des angegebenen Labs. Sie enthält auch eine Schaltfläche, mit der Benutzer die Funktion beim nächsten Mal direkt aufrufen können.

    ![Testen der Funktion](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>Nächste Schritte
Mit Azure Functions können Sie die integrierten Funktionen von DevTest Labs erweitern und Kunden helfen, die individuellen Anforderungen ihrer Teams zu erfüllen. Dieses Muster kann beliebig erweitert werden, um noch mehr Szenarien zu unterstützen.  Weitere Informationen zu DevTest Labs finden Sie in den folgenden Artikeln: 

- [Azure DevTest Labs: Referenzarchitektur für Unternehmen](devtest-lab-reference-architecture.md)
- [Häufig gestellte Fragen](devtest-lab-faq.yml)
- [Zentrales Hochskalieren von DevTest Labs](devtest-lab-guidance-scale.md)
- [Automating DevTest Labs with PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests) (Automatisieren von DevTest Labs mit PowerShell)
