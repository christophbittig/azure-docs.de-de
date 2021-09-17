---
title: 'Tutorial: Vorbereiten der Bereitstellung von Azure Stack Edge Pro FPGA über das Azure-Portal in Ihrem Rechenzentrum'
description: Im ersten Tutorial zum Bereitstellen von Azure Stack Edge Pro FPGA geht es um die Vorbereitung des Azure-Portals.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 07/23/2021
ms.author: alkohli
ms.custom: subject-rbac-steps
ms.openlocfilehash: d215c1cc8650eb23d1702f7ef0fd782278cfb6d8
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/20/2021
ms.locfileid: "122605204"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-fpga"></a>Tutorial: Vorbereiten der Bereitstellung von Azure Stack Edge Pro FPGA  

Dies ist das erste Tutorial einer Reihe von Bereitstellungstutorials, die zum vollständigen Bereitstellen von Azure Stack Edge Pro FPGA erforderlich sind. In diesem Tutorial erfahren Sie, wie Sie das Azure-Portal für die Bereitstellung einer Azure Stack Edge-Ressource vorbereiten. 

Sie benötigen Administratorrechte, um die Einrichtung und Konfiguration abzuschließen. Die Vorbereitung des Portals erfordert weniger als 10 Minuten.  

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Neue Ressource erstellen
> * Abrufen des Aktivierungsschlüssels

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="get-started"></a>Erste Schritte

Führen Sie zum Bereitstellen von Azure Stack Edge Pro FPGA die folgenden Tutorials in der angegebenen Reihenfolge aus.

| **#** | **Schritt** | **Dokumente** |
| --- | --- | --- | 
| 1. |**[Vorbereiten des Azure-Portals für Azure Stack Edge Pro FPGA](azure-stack-edge-deploy-prep.md)** |Erstellen und konfigurieren Sie Ihre Azure Stack Edge-Ressource, bevor Sie ein physisches Azure Stack Edge-Gerät installieren. |
| 2. |**[Installieren von Azure Stack Edge Pro FPGA](azure-stack-edge-deploy-install.md)**|Packen Sie das physische Azure Stack Edge Pro FPGA-Gerät aus, montieren Sie es in einem Rack, und verkabeln Sie es.  |
| 3. |**[Verbinden, Einrichten und Aktivieren von Azure Stack Edge Pro FPGA](azure-stack-edge-deploy-connect-setup-activate.md)** |Stellen Sie eine Verbindung mit der lokalen Webbenutzeroberfläche her, führen Sie die Einrichtung durch, und aktivieren Sie das Gerät. Das Gerät ist für die Einrichtung von SMB- oder NFS-Freigaben bereit.  |
| 4. |**[Übertragen von Daten mit Azure Stack Edge Pro FPGA](azure-stack-edge-deploy-add-shares.md)** |Fügen Sie Freigaben hinzu, und stellen Sie über SMB oder NFS eine Verbindung mit Freigaben her. |
| 5. |**[Transformieren der Daten mit Azure Stack Edge Pro FPGA](azure-stack-edge-deploy-configure-compute.md)** |Konfigurieren Sie Computemodule auf dem Gerät, um die Daten bei der Übertragung an Azure zu transformieren. |

Sie können jetzt mit der Einrichtung des Azure-Portals beginnen.

## <a name="prerequisites"></a>Voraussetzungen

Im Folgenden finden Sie die Konfigurationsvoraussetzungen für die Azure Stack Edge-Ressource, das Azure Stack Edge Pro FPGA-Gerät und das Rechenzentrumsnetzwerk.

### <a name="for-the-azure-stack-edge-resource"></a>Für die Azure Stack Edge-Ressource

Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Ihr Microsoft Azure-Abonnement ist für eine Azure Stack Edge-Ressource aktiviert. Stellen Sie sicher, dass Sie ein unterstütztes Abonnement verwendet haben, z. B. [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/overview/sales-number/), [Cloud Solution Provider (CSP)](/partner-center/azure-plan-lp) oder [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). Abonnements mit nutzungsbasierter Bezahlung werden nicht unterstützt.

* RBAC-Rollen: Sie verfügen über die folgenden Rollenzuweisungen in der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure:

  * Um Azure Stack Edge-, IoT Hub- und Azure Storage-Ressourcen zu erstellen, muss ein Benutzer im Gültigkeitsbereich der Ressourcengruppe über die Rolle „Mitwirkender“ oder „Besitzer“ verfügen.

  * Um einem Benutzer im Bereich der Ressourcengruppe die Rolle „Mitwirkender“ zuweisen zu können, müssen Sie im Abonnementbereich über die Rolle „Besitzer“ verfügen.

  Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).

* Ressourcenanbieter: Die folgenden Ressourcenanbieter sind registriert: 

  * Um eine Azure Stack Edge-/Data Box Gateway-Ressource zu erstellen, müssen Sie sicherstellen, dass der Anbieter `Microsoft.DataBoxEdge` registriert ist.

  * Um eine IoT Hub-Ressource zu erstellen, müssen Sie sicherstellen, dass der Anbieter `Microsoft.Devices` registriert ist.

  * Um eine Azure Storage-Ressource zu erstellen, stellen Sie sicher, dass Azure Storage registriert ist. Der Azure Storage-Ressourcenanbieter (Storage Resource Provider, SRP) ist normalerweise standardmäßig registriert, in einigen Fällen ist jedoch eventuell eine Registrierung erforderlich.

  **Um einen Ressourcenanbieter zu registrieren, muss Ihnen die zugehörige RBAC-Rolle zugewiesen sein.**

  Informationen zur Registrierung finden Sie unter [Registrieren des Ressourcenanbieters](azure-stack-edge-manage-access-power-connectivity-mode.md#register-resource-providers).

* Sie haben als Administrator oder Benutzer Zugriff auf die Azure Active Directory Graph-API. Weitere Informationen finden Sie unter [Azure Active Directory Graph-API](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).

* Sie besitzen ein Microsoft Azure-Speicherkonto mit Anmeldeinformationen für den Zugriff.

* Sie werden durch eine Azure Policy-Zuweisung, die von Ihrem Systemadministrator eingerichtet wurde, nicht blockiert. Weitere Informationen zu Azure Policy finden Sie unter [Schnellstart: Erstellen einer Richtlinienzuweisung zum Identifizieren nicht konformer Ressourcen](../governance/policy/assign-policy-portal.md).


### <a name="for-the-azure-stack-edge-pro-fpga-device"></a>Für das Azure Stack Edge Pro FPGA-Gerät

Stellen Sie Folgendes sicher, bevor Sie ein physisches Gerät bereitstellen:

* Sie haben sich mit den beiliegenden Sicherheitsinformationen vertraut gemacht.
* In Ihrem Rechenzentrum ist für die Rackmontage des Geräts ein freier Einschub (1 HE) in einem 19-Zoll-Standardrack verfügbar.
* Sie haben Zugang zu einer flachen, stabilen und ebenen Arbeitsfläche, auf der das Gerät sicher abgelegt werden kann.
* Der Standort, an dem Sie das Gerät einrichten möchten, hat eine Standardstromversorgung von einer unabhängigen Quelle oder eine Rack-PDU (Power Distribution Unit) mit unterbrechungsfreier Stromversorgung (USV).
* Sie verfügen über ein physisches Gerät.

### <a name="for-the-datacenter-network"></a>Für das Netzwerk des Rechenzentrums

Stellen Sie Folgendes sicher, bevor Sie beginnen:

* Das Netzwerk in Ihrem Rechenzentrum ist gemäß den Netzwerkanforderungen für das Azure Stack Edge Pro FPGA-Gerät konfiguriert. Weitere Informationen finden Sie unter [Systemanforderungen für Azure Stack Edge Pro FPGA](azure-stack-edge-system-requirements.md).

* Für den Normalbetrieb von Azure Stack Edge Pro FPGA benötigen Sie Folgendes:

  * Eine Downloadbandbreite von mindestens 10 MBit/s, um sicherzustellen, dass das Gerät auf dem neuesten Stand bleibt
  * Eine dedizierte Upload- und Downloadbandbreite von mindestens 20 MBit/s zum Übertragen von Dateien

## <a name="create-new-resource-for-existing-device"></a>Erstellen einer neuen Ressource für das vorhandene Gerät

Wenn Sie bereits Azure Stack Edge Pro FPGA-Kunde sind, verwenden Sie das folgende Verfahren, um eine neue Ressource zu erstellen, wenn Sie Ihr vorhandenes Gerät ersetzen oder zurücksetzen müssen.

Wenn Sie Neukunde sind, empfehlen wir Ihnen, die Verwendung von Azure Stack Edge Pro-GPU-Geräten für Ihre Workloads in Erwägung zu ziehen. Weitere Informationen finden Sie unter [Was ist Azure Stack Edge Pro mit GPU?](azure-stack-edge-gpu-overview.md). Informationen zum Bestellen eines Azure Stack Edge Pro-GPU-Geräts finden Sie unter [Neue Ressource erstellen](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).

Führen Sie zum Erstellen einer neuen Azure Stack Edge-Ressource für ein vorhandenes Gerät die folgenden Schritte im Azure-Portal aus:

1. Melden Sie sich mit Ihren Microsoft Azure-Anmeldeinformationen bei einem der folgenden Portale an:

    - Azure-Portal (URL: [https://portal.azure.com](https://portal.azure.com))
    - Azure Government-Portal (URL: [https://portal.azure.us](https://portal.azure.us)). Ausführlichere Informationen finden Sie unter [Quickstart: Connect to Azure Government using portal](../azure-government/documentation-government-get-started-connect-with-portal.md) (Schnellstart: Herstellen einer Verbindung mit Azure Government über das Portal).

1. Wählen Sie **+ Ressource erstellen**. Suchen Sie nach **Azure Stack Edge**, und wählen Sie den Eintrag aus. Klicken Sie anschließend auf **Erstellen**.

1. Wählen Sie das Abonnement für das Azure Stack Edge Pro FPGA-Gerät und unter **Liefern an** das Land aus, in das das Gerät versendet werden soll.

   ![Auswählen des Abonnements und des Lands des Empfängers für Ihr Gerät](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-01.png)


1. Wählen Sie in der Liste der angezeigten Gerätetypen **Azure Stack Edge Pro – FPGA** aus. Klicken Sie anschließend auf **Auswählen**. 

   Der Gerätetyp **Azure Stack Edge Pro – FPGA** wird nur angezeigt, wenn Sie über ein vorhandenes Gerät verfügen. Wenn Sie ein neues Gerät bestellen müssen, gehen Sie zu [Neue Ressource erstellen](azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).

   ![Suchen nach dem Azure Stack Edge-Dienst](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-02.png)

1. Gehen Sie auf der Registerkarte **Grundlagen** wie folgt vor:

   1. Geben Sie die folgenden **Projektdetails** ein, oder wählen Sie sie aus:
    
       |Einstellung  |Wert  |
       |---------|---------|
       |Subscription    |Dieser Wert wird auf der Grundlage Ihrer zuvor getroffenen Auswahl automatisch aufgefüllt. Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft. |
       |Resource group  |Wählen Sie eine vorhandene Gruppe aus, oder erstellen Sie eine neue Gruppe.<br>Erfahren Sie mehr über [Azure-Ressourcengruppen](../azure-resource-manager/management/overview.md).     |

   1. Geben Sie die folgenden **Instanzendetails** ein, bzw. wählen Sie sie aus:

       |Einstellung  |Wert  |
       |---------|---------|
       |Name   | Ein Anzeigename, der die Ressource identifiziert.<br>Der Name muss 2 bis 50 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten.<br> Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden.        |
       |Region     |Eine Liste aller Regionen, in denen die Azure Stack Edge-Ressource verfügbar ist, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Die für Azure Government verfügbaren Regionen finden Sie bei Bedarf unter [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/regions/).<br> Wählen Sie den Standort aus, der der geografischen Region, in der Ihr Gerät bereitgestellt werden soll, am nächsten ist.|

   1. Klicken Sie auf **Überprüfen + erstellen**.

    ![Projekt- und Instanzendetails](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-03.png)

1. Überprüfen Sie auf der Registerkarte **Überprüfen und erstellen** die **Nutzungsbedingungen**, die **Preisdetails** und die Details für Ihre Ressource. Klicken Sie anschließend auf **Erstellen**.

    ![Überprüfen der Details und Datenschutzrichtlinien für Ihre Azure Stack Edge-Ressource](media/azure-stack-edge-deploy-prep/create-fpga-existing-resource-04.png)

1. Die Erstellung der Ressource dauert einige Minuten. Nachdem die Ressource erfolgreich erstellt und bereitgestellt wurde, erhalten Sie eine Benachrichtigung. Wählen Sie **Zu Ressource wechseln** aus.

   ![Navigieren zur Azure Stack Edge-Ressource](media/azure-stack-edge-deploy-prep/data-box-edge-resource-01.png)

Nach der Bestellung wird diese von Microsoft geprüft, und Sie erhalten eine E-Mail mit den Versanddetails.

![Benachrichtigung zur Überprüfung der Azure Stack Edge Pro FPGA-Bestellung](media/azure-stack-edge-deploy-prep/data-box-edge-resource-02.png)


## <a name="get-the-activation-key"></a>Abrufen des Aktivierungsschlüssels

Wenn die Azure Stack Edge-Ressource betriebsbereit ist, müssen Sie den Aktivierungsschlüssel abrufen. Dieser Schlüssel wird verwendet, um Ihr Azure Stack Edge Pro FPGA-Gerät zu aktivieren und mit der Ressource zu verbinden. Sie können diesen Schlüssel jetzt abrufen, während Sie sich im Azure-Portal befinden.

1. Navigieren Sie zu der von Ihnen erstellten Ressource, und wählen Sie **Übersicht** aus. Es wird eine Benachrichtigung mit dem Hinweis angezeigt, dass Ihre Bestellung verarbeitet wird.

    ![Auswählen von „Übersicht“](media/azure-stack-edge-deploy-prep/data-box-edge-select-device-setup.png)

2. Nachdem die Bestellung verarbeitet und das Gerät an Sie versendet wurde, wird die **Übersicht** aktualisiert. Übernehmen Sie den Standardwert für **Azure Key Vault-Name**, oder geben Sie einen neuen Namen ein. Wählen Sie **Generate activation key** (Aktivierungsschlüssel generieren) aus. Wählen Sie das Kopiersymbol, um den Schlüssel zu kopieren und für die spätere Verwendung zu speichern.

    ![Abrufen des Aktivierungsschlüssels](media/azure-stack-edge-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
>
> * Der Aktivierungsschlüssel läuft drei Tage nach seiner Generierung ab.
> * Generieren Sie einen neuen Schlüssel, falls der Schlüssel abgelaufen ist. Der ältere Schlüssel ist nicht mehr gültig.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden unter anderem folgende Azure Stack Edge Pro FPGA-Themen behandelt:

> [!div class="checklist"]
>
> * Neue Ressource erstellen
> * Abrufen des Aktivierungsschlüssels

Im nächsten Tutorial erfahren Sie, wie Sie Azure Stack Edge Pro FPGA installieren.

> [!div class="nextstepaction"]
> [Installieren von Azure Stack Edge Pro FPGA](./azure-stack-edge-deploy-install.md)