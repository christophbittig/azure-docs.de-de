---
title: Verwenden Sie Azure Chaos Studio, um einen Internet-DNS-Ausfall unter Verwendung des Fehlers der Netzwerksicherheitsgruppe zu replizieren
description: Beginnen Sie mit Chaos Studio, indem Sie einen DNS-Ausfall unter Verwendung der Netzwerksicherheitsgruppe fault erstellen.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 08/26/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: dc7a04c7bd20f1b778aa23f163b57161c0fe7745
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131095288"
---
# <a name="quickstart-internet-dns-outage-using-network-security-group-fault"></a>Schnellstart: Internet-DNS-Ausfall durch Fehler der Netzwerksicherheitsgruppe

Der Netzwerksicherheitsgruppen-Fehler ermöglicht es Ihnen, Ihre bestehenden Netzwerksicherheitsgruppen-Regeln im Rahmen eines Chaos-Experiments zu ändern. Mit diesem Fehler können Sie den Netzwerkverkehr zu Ihren Azure-Ressourcen blockieren und so den Verlust der Konnektivität oder Ausfälle abhängiger Ressourcen simulieren. In diesem Schnellstart werden Sie ein Chaos-Experiment durchführen, das den gesamten Datenverkehr zu externen (Internet-)DNS-Servern für 15 Minuten blockiert. Mit diesem Experiment können Sie überprüfen, dass Ressourcen, die mit dem virtuellen Azure-Netzwerk verbunden sind, das mit der Sicherheitsgruppe des Zielnetzwerks verknüpft ist, nicht von externen DNS-Servern abhängig sind, wodurch Sie eine der Anforderungen des Risikobedrohungsmodells validieren können.

## <a name="prerequisites"></a>Voraussetzungen

- Eine Netzwerksicherheitsgruppe, die mit der Azure-Ressource verknüpft ist, auf die Sie in Ihrem Experiment abzielen möchten.  
- Verwenden Sie die Bash-Umgebung in [Azure Cloud Shell.](../cloud-shell/quickstart.md).

## <a name="create-the-network-security-group-fault-provider"></a>Erstellen Sie die Netzwerksicherheitsgruppe Fault Provider

Zuerst registrieren Sie einen Fault Provider auf dem Abonnement, auf dem Ihre Netzwerksicherheitsgruppe gehostet wird, damit Chaos Studio mit ihm interagieren kann.

1. Erstellen Sie eine Datei mit dem Namen **AzureNetworkSecurityGroupChaos.json** mit dem folgenden Inhalt und speichern Sie sie auf Ihrem lokalen Rechner.

      ```json
      { 
        "properties": {
          "enabled": true,
          "providerConfiguration": {
            "type": "AzureNetworkSecurityGroupChaos"
          }
        }
      }
      ```

1. Starten Sie eine [Cloud Shell](https://shell.azure.com/).
1. Ersetzen Sie **$SUBSCRIPTION_ID** durch die Azure-Abonnement-ID, die die Netzwerksicherheitsgruppe enthält, die Sie in Ihrem Experiment verwenden möchten, und führen Sie den folgenden Befehl aus, um sicherzustellen, dass der Anbieter im richtigen Abonnement registriert wird.

    ```bash
    az account set --subscription $SUBSCRIPTION_ID
    ```

1. Ziehen Sie die Datei **AzureNetworkSecurityGroupChaos.json** in das Fenster der Cloud-Shell, um die Datei hochzuladen.
1. Ersetzen Sie die im vorherigen Schritt verwendete **$SUBSCRIPTION_ID** und führen Sie den folgenden Befehl aus, um den AzureNetworkSecurityGroupChaos-Fehleranbieter zu registrieren.

    ```bash
    az rest --method put --url "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/microsoft.chaos/chaosProviderConfigurations/AzureNetworkSecurityGroupChaos?api-version=2021-06-21-preview" --body @AzureNetworkSecurityGroupChaos.json --resource "https://management.azure.com"
    ```

1. (Optional) Löschen Sie die Datei **AzureNetworkSecurityGroupChaos.json**, die Sie zuvor erstellt hatten, da sie nicht mehr benötigt wird, und schließen Sie die Cloud Shell.

## <a name="create-a-chaos-experiment"></a>Ein Chaosexperiment erstellen

Sobald der Netzwerksicherheitsgruppen-Fault-Provider erstellt wurde, können Sie mit der Erstellung von Experimenten im Chaos Studio beginnen.

1. Öffnen Sie das Azure-Portal mit dem Chaos Studio-Funktionskennzeichen:
    * Wenn Sie ein @microsoft.com Konto verwenden, [klicken Sie auf diesen Link](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true).
    * Wenn Sie ein externes Konto verwenden, [klicken Sie auf diesen Link](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}).
1. Klicken Sie auf **Ein Experiment hinzufügen**.

    ![Hinzufügen eines Experiments im Azure-Portal](images/add-an-experiment.png)

1. Geben Sie den Namen ein, den Sie dem Experiment geben möchten, und wählen Sie das Abonnement, die Ressourcengruppe und den Standort (Region) aus, an dem das Experiment erstellt werden soll.
1. Klicken Sie auf **Next : Versuchsplaner >** .

    ![Ein Experiment im Azure-Portal erstellen](images/create-an-experiment-dns-outage.png)

1. Klicken Sie auf **Fehler hinzufügen**.
1. Wählen Sie **Netzwerksicherheitsgruppe Störung** aus dem Dropdown-Menü Störungen.

    ![Auswahl des NSG-Fehlers im Azure-Portal](images/network-security-group-fault.png)

1. Geben Sie die folgenden Parameter ein.

    | Parameter | Wert |
    | -- | -- |
    | Duration | `15` |
    | Name | `block_internet_dns` |
    | Präfix der Quelladresse | `*` |
    | Präfix der Zieladresse | `Internet` |
    | Access | `Deny` |
    | Quellanschlussbereich | `*` |
    | Zielhafenbereich | `53` |
    | Priorität | `1001` |
    | Direction | `Outbound` |

    ![Parameter der Netzwerksicherheitsgruppe im Azure-Portal](images/qs-network-outage-dns-parameters.png)

> [!NOTE]
> Die Felder "Name" und "Priorität" müssen möglicherweise angepasst werden, wenn eine der beiden Regeln bereits in der Sicherheitsgruppe des Zielnetzes vorhanden ist.

> [!NOTE]
> Die Priorität muss unter Umständen herabgesetzt werden, wenn es Regeln gibt, die den Verkehr von Port 53 ins Internet ausdrücklich zulassen.

1. Klicken Sie auf **Weiter : Zielressourcen >** .
1. Wählen Sie die Netzwerksicherheitsgruppen aus, auf die sich dieses Experiment beziehen soll.
1. Klicken Sie auf **Hinzufügen**.

    ![Zielressourcen auswählen](images/nsg-fault-targets.png)

1. Klicken Sie auf **Überprüfen + erstellen**.

    ![Experiment überprüfen und erstellen](images/review-create.png)

1. Klicken Sie auf **Erstellen**

    ![Schaltfläche "Experiment erstellen](images/create.png)

## <a name="grant-the-chaos-experiment-access-to-the-network-security-group"></a>Gewähren Sie dem Chaos-Experiment Zugriff auf die Netzwerksicherheitsgruppe

Als Sicherheitsvorkehrung muss allen Chaos-Experimenten Zugang zu den Azure-Ressourcen gewährt werden, auf die das Experiment abzielt.

1. Navigieren Sie zu den Ressourcen, die Gegenstand des Experiments sind.
1. Klicken Sie auf **Zugriffssteuerung (IAM)** .

    ![Zugang ändern](images/access-control.png)

1. Klicken Sie auf **Hinzufügen**.

    ![Schaltfläche „Hinzufügen“](images/add.png)

1. Klicken Sie auf **Rollenzuweisung hinzufügen**.

    ![Schaltfläche Rollenzuweisung hinzufügen](images/add-role-assignment.png)

1. In **Rolle** wählen Sie `Network Contributor`

    ![Netzwerkteilnehmer auswählen](images/role-network-contributor.png)

1. Geben Sie in **Auswahl** den Namen des Chaosexperiments ein und klicken Sie darauf.

    ![Name des Experiments auswählen](images/qs-network-outage-dns-select.png)

1. Klicken Sie unten auf der Seite auf **Speichern**.

    ![Speichern der Änderungen](images/save-discard.png)

## <a name="run-the-chaos-experiment"></a>Führen Sie das Chaos-Experiment durch

1. Öffnen Sie das Azure-Portal mit dem Chaos Studio-Funktionskennzeichen:
    * Wenn Sie ein @microsoft.com Konto verwenden, [klicken Sie auf diesen Link](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true).
    * Wenn Sie ein externes Konto verwenden, [klicken Sie auf diesen Link](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}).
1. Markieren Sie das Kästchen neben dem Namen des Experiments und klicken Sie auf **Experiment starten**.

    ![Experiment starten](images/qs-network-outage-dns-start.png)

1. Klicken Sie auf **Ja**, um zu bestätigen, dass Sie das Chaosexperiment starten möchten.

    ![Bestätigen Sie, dass Sie das Experiment beginnen möchten](images/start-experiment-confirmation.png)
1. (Optional) Klicken Sie auf den Namen des Experiments, um eine detaillierte Übersicht über den Ausführungsstatus des Experiments zu erhalten.
1. Sobald das Experiment gestartet wurde, können Sie Ihre vorhandenen Überwachungs-, Telemetrie- und/oder Protokollierungswerkzeuge verwenden, um zu überprüfen, welche Auswirkungen das Chaos-Experiment auf Ihren Dienst hatte.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Befolgen Sie diese Schritte, wenn Sie dieses Experiment nicht weiter verwenden und es löschen möchten.

Öffnen Sie das Azure-Portal mit dem Chaos Studio-Funktionskennzeichen:
    * Wenn Sie ein @microsoft.com Konto verwenden, [klicken Sie auf diesen Link](https://ms.portal.azure.com/?microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}&microsoft_azure_chaos=true).
    * Wenn Sie ein externes Konto verwenden, [klicken Sie auf diesen Link](https://portal.azure.com/?feature.customPortal=false&microsoft_azure_chaos_assettypeoptions={%22chaosStudio%22:{%22options%22:%22%22},%22chaosExperiment%22:{%22options%22:%22%22}}).
1. Markieren Sie das Kästchen neben dem Experimentnamen und klicken Sie auf **Löschen**.
1. Klicken Sie auf **Ja**, um zu bestätigen, dass Sie das Experiment löschen möchten.

Befolgen Sie diese Schritte, wenn Sie keine Fehler im Zusammenhang mit Netzwerksicherheitsgruppen mehr verwenden wollen.

1. Starten Sie eine [Cloud Shell](https://shell.azure.com/).
1. Ersetzen Sie **$SUBSCRIPTION_ID** durch die Azure-Abonnement-ID, unter der der Netzwerksicherheitsgruppen-Fehleranbieter bereitgestellt wurde, und führen Sie den folgenden Befehl aus.

    ```bash
    az rest --method delete --url "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/microsoft.chaos/chaosProviderConfigurations/AzureNetworkSecurityGroupChaos?api-version=2021-06-21-preview" --resource "https://management.azure.com"
    ```
