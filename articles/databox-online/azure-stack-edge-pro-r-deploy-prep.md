---
title: Tutorial zum Vorbereiten des Azure-Portals und der Rechenzentrumsumgebung für die Bereitstellung von Azure Stack Edge Pro R
description: Im ersten Tutorial zum Bereitstellen von Azure Stack Edge Pro R geht es um die Vorbereitung des Azure-Portals.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 08/06/2021
ms.author: alkohli
ms.openlocfilehash: 4ffc4af491380fe02028848b2abeb53c73d8cd8b
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/17/2021
ms.locfileid: "122324443"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-pro-r"></a>Tutorial: Vorbereiten der Bereitstellung von Azure Stack Edge Pro R

Dies ist das erste Tutorial einer Reihe von Bereitstellungstutorials, die zum vollständigen Bereitstellen von Azure Stack Edge Pro R erforderlich sind. In diesem Tutorial erfahren Sie, wie Sie das Azure-Portal für die Bereitstellung einer Azure Stack Edge-Ressource vorbereiten. Im Rahmen des Tutorials wird ein Azure Stack Edge Pro R-Gerät mit einem einzelnen Knoten verwendet, das mit einer unterbrechungsfreien Stromversorgung (USV) ausgeliefert wird.

Sie benötigen Administratorrechte, um die Einrichtung und Konfiguration abzuschließen. Die Vorbereitung des Portals erfordert weniger als 10 Minuten.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
>
> * Neue Ressource erstellen
> * Abrufen des Aktivierungsschlüssels

### <a name="get-started"></a>Erste Schritte

Führen Sie zum Bereitstellen von Azure Stack Edge Pro R die folgenden Tutorials in der angegebenen Reihenfolge aus.

| Schritt | Dokumente |
| --- | --- |
| **Vorbereitung** |Diese Schritte müssen zur Vorbereitung der anstehenden Bereitstellung ausgeführt werden. |
| **[Konfigurationsprüfliste für die Bereitstellung](#deployment-configuration-checklist)** |Verwenden Sie diese Prüfliste, um vor und während der Bereitstellung Informationen zu erfassen und aufzuzeichnen. |
| **[Voraussetzungen für die Bereitstellung](#prerequisites)** |Anhand dieser Voraussetzungen überprüfen Sie, ob die Umgebung für die Bereitstellung vorbereitet ist. |
|  | |
|**Tutorials für die Bereitstellung** |Diese Tutorials sind erforderlich, um Ihr Azure Stack Edge Pro R-Gerät in der Produktionsumgebung bereitzustellen. |
|**[1. Vorbereiten des Azure-Portals für das Gerät](azure-stack-edge-pro-r-deploy-prep.md)** |Erstellen und konfigurieren Sie Ihre Azure Stack Edge-Ressource, bevor Sie ein physisches Azure Stack Edge-Gerät installieren. |
|**[2. Installieren des Geräts](azure-stack-edge-pro-r-deploy-install.md)**|Untersuchen Sie Ihr physisches Gerät, und verkabeln Sie es.  |
|**[3. Herstellen einer Verbindung mit dem Gerät](azure-stack-edge-pro-r-deploy-connect.md)** |Sobald das Gerät installiert ist, stellen Sie eine Verbindung mit der lokalen Webbenutzeroberfläche des Geräts her.  |
|**[4. Konfigurieren der Netzwerkeinstellungen](azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md)** |Konfigurieren Sie das Netzwerk einschließlich Computenetzwerk und Webproxyeinstellungen für Ihr Gerät.   |
|**[5. Konfigurieren der Geräteeinstellungen](azure-stack-edge-pro-r-deploy-set-up-device-update-time.md)** |Weisen Sie einen Gerätenamen und eine DNS-Domäne zu, und konfigurieren Sie den Updateserver und die Gerätezeit. |
|**[6. Konfigurieren der Sicherheitseinstellungen](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)** |Konfigurieren Sie Zertifikate, VPN und die Verschlüsselung ruhender Daten für Ihr Gerät. Verwenden Sie von Geräten generierte Zertifikate, oder stellen Sie Ihre eigenen Zertifikate bereit.   |
|**[7. Aktivieren des Geräts](azure-stack-edge-pro-r-deploy-activate.md)** |Verwenden Sie den Aktivierungsschlüssel des Diensts, um das Gerät zu aktivieren. Das Gerät ist bereit, SMB- oder NFS-Freigaben einzurichten oder eine Verbindung über REST herzustellen. |
|**[8. Konfigurieren von Compute](azure-stack-edge-gpu-deploy-configure-compute.md)** |Konfigurieren Sie die Computerolle auf Ihrem Gerät. Außerdem wird ein Kubernetes-Cluster erstellt. |

Sie können jetzt mit der Einrichtung des Azure-Portals beginnen.

## <a name="deployment-configuration-checklist"></a>Konfigurationsprüfliste für die Bereitstellung

Bevor Sie Ihr Gerät bereitstellen, müssen Sie Informationen zum Konfigurieren der Software auf Ihrem Azure Stack Edge Pro-Gerät sammeln. Wenn Sie einige dieser Informationen im Voraus sammeln, können Sie das Gerät in Ihrer Umgebung schneller bereitstellen. Verwenden Sie die [Konfigurationsprüfliste für die Bereitstellung von Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-checklist.md), um die Konfigurationsdetails während der Bereitstellung Ihres Geräts zu notieren.

## <a name="prerequisites"></a>Voraussetzungen

Nachstehend finden Sie die Konfigurationsvoraussetzungen für die Azure Stack Edge-Ressource, das Azure Stack Edge-Gerät und das Netzwerk des Rechenzentrums.

### <a name="for-the-azure-stack-edge-resource"></a>Für die Azure Stack Edge-Ressource

[!INCLUDE [Azure Stack Edge resource prerequisites](../../includes/azure-stack-edge-gateway-resource-prerequisites.md)]

### <a name="for-the-azure-stack-edge-device"></a>Für das Azure Stack Edge-Gerät

Stellen Sie Folgendes sicher, bevor Sie ein physisches Gerät bereitstellen:

- Sie haben sich mit den Sicherheitsinformationen für dieses Gerät vertraut gemacht: [Sicherheitsanweisungen für Azure Stack Edge Pro R](azure-stack-edge-pro-r-safety.md).
[!INCLUDE [Azure Stack Edge device prerequisites](../../includes/azure-stack-edge-gateway-device-prerequisites.md)] 

### <a name="for-the-datacenter-network"></a>Für das Netzwerk des Rechenzentrums

Stellen Sie Folgendes sicher, bevor Sie beginnen:

- Das Netzwerk in Ihrem Rechenzentrum ist gemäß den Netzwerkanforderungen für das Azure Stack Edge-Gerät konfiguriert. Weitere Informationen finden Sie unter [Systemanforderungen für Azure Stack Edge Pro R](azure-stack-edge-pro-r-system-requirements.md).

- Für den Normalbetrieb Ihres Geräts benötigen Sie Folgendes:

    - Eine Downloadbandbreite von mindestens 10 MBit/s, um sicherzustellen, dass das Gerät auf dem neuesten Stand bleibt.
    - Eine dedizierte Upload- und Downloadbandbreite von mindestens 20 MBit/s zum Übertragen von Dateien.

## <a name="create-a-new-resource"></a>Neue Ressource erstellen

Falls Sie bereits über eine Azure Stack Edge-Ressource für die Verwaltung Ihres physischen Geräts verfügen, überspringen Sie diesen Schritt, und fahren Sie mit [Abrufen des Aktivierungsschlüssels](#get-the-activation-key) fort.

---

### <a name="azure-edge-hardware-center-preview"></a>[Azure Edge Hardware Center (Vorschau)](#tab/azure-edge-hardware-center)

Azure Edge Hardware Center (Vorschau) ist ein neuer Dienst, mit dem Sie eine breite Palette an Hardware aus dem Azure-Hybridportfolio einschließlich Azure Stack Edge Pro-Geräten untersuchen und bestellen können.

Wenn Sie eine Bestellung über das Azure Edge Hardware Center aufgeben, können Sie mehrere Geräte bestellen, die an mehrere Adressen versendet werden sollen, und Sie können die Lieferadressen aus anderen Bestellungen wiederverwenden.

Bei einer Bestellung über das Azure Edge Hardware Center wird eine Azure-Ressource mit allen Ihren bestellbezogenen Informationen erstellt. Für jede der bestellten Einheiten wird jeweils eine Ressource erstellt. Nachdem Sie das Gerät erhalten haben, müssen Sie eine Azure Stack Edge-Ressource erstellen, um es zu aktivieren und zu verwalten.

[!INCLUDE [Create order in Azure Edge Hardware Center](../../includes/azure-edge-hardware-center-new-order.md)]

#### <a name="create-management-resources-for-devices"></a>Erstellen von Verwaltungsressourcen für Geräte

Zum Verwalten von Geräten, die Sie im Azure Edge Hardware Center bestellen, erstellen Sie Verwaltungsressourcen in Azure Stack Edge. 

Beim Aktivieren eines Geräts wird die Verwaltungsressource dem Bestellartikel zugeordnet. Sie können den Bestellartikel aus der Verwaltungsressource und die Verwaltungsressource aus dem Bestellartikel öffnen. 

Nach dem Versand eines Geräts wird den Details zum Bestellartikel der Link **Hardware konfigurieren** hinzugefügt, sodass Sie einen Assistenten zum Erstellen einer Verwaltungsressource direkt öffnen können. Sie können dafür auch die Option **Verwaltungsressource erstellen** in Azure Stack Edge verwenden.

[!INCLUDE [Create management resource](../../includes/azure-edge-hardware-center-create-management-resource.md)]

### <a name="portal-classic"></a>[Portal (klassisch)](#tab/azure-portal)

Um eine Azure Stack Edge-Ressource zu erstellen, führen Sie im Azure-Portal die folgenden Schritte aus.

1. Melden Sie sich mit Ihren Microsoft Azure-Anmeldeinformationen beim Azure-Portal unter der folgenden URL an: [https://portal.azure.com](https://portal.azure.com).

2. Suchen Sie in **Azure-Dienste** nach **Azure Stack Edge**, und wählen Sie den Eintrag aus. Wählen Sie dann **+ Erstellen** aus. 

3. Wählen Sie in **Azure Stack Edge-Geräte verwalten** den Link **Azure Edge Hardware Center testen** aus.

    ![Screenshot: Der Bildschirm „Azure Stack Edge-Geräte verwalten“, geöffnet über die Schaltfläche „Plus Erstellen“. Der Link „Azure Edge Hardware Center testen“ ist hervorgehoben.](media/azure-stack-edge-pro-r-deploy-prep/classic-order-experience-1.png)

    Dadurch wird der Bildschirm **Erste Schritte** zum Erstellen einer Bestellung im Azure Edge Hardware Center geöffnet. 

4. Wenn Sie nicht über das Hardware Center bestellen möchten, wählen Sie im Bildschirm **Erste Schritte** die Option **Über die klassische Auftragsfunktionalität bestellen** aus.

   ![Screenshot: Bildschirm „Erste Schritte“ in Azure Stack Edge. Der Link „Über die klassische Auftragsfunktionalität bestellen“ ist hervorgehoben.](media/azure-stack-edge-pro-r-deploy-prep/classic-order-experience-2.png)

3. Wählen Sie das Abonnement aus, das Sie für das Azure Stack Edge Pro-Gerät verwenden möchten. Wählen Sie das Land aus, in das das physische Gerät versendet werden soll. Wählen Sie **Geräte anzeigen** aus.

    ![Screenshot: Bildschirm „Gerätetyp auswählen“ zum Auswählen eines Abonnements und zum Versand in die Region für eine Azure Stack Edge-Ressource. Die Schaltfläche „Geräte anzeigen“ ist hervorgehoben.](media/azure-stack-edge-pro-r-deploy-prep/create-resource-1.png)

4. Wählen Sie den Gerätetyp aus. Wählen Sie unter **Azure Stack Edge** die Option **Azure Stack Edge Pro R** und anschließend **Auswählen** aus. Wenn Probleme auftreten oder Sie den Gerätetyp nicht auswählen können, finden Sie unter [Behandeln von Problemen](azure-stack-edge-troubleshoot-ordering.md) weitere Informationen.

    ![Screenshot: Bildschirm „Gerätetyp auswählen" zum Auswählen eines Gerätetyps für eine Azure Stack Edge-Ressource. Die Schaltfläche „Auswählen“ für einen Gerätetyp ist hervorgehoben.](media/azure-stack-edge-pro-r-deploy-prep/create-resource-2.png#lightbox)

5. Abhängig von Ihren geschäftlichen Anforderungen können Sie entweder **Azure Stack Edge Pro R - single node** (Azure Stack Edge Pro R: einzelner Knoten) oder **Azure Stack Edge Pro R - single node with UPS** (Azure Stack Edge Pro R: einzelner Knoten mit USV) auswählen.  

    ![Screenshot: Bildschirm zum Auswählen einer Konfiguration für ein Azure Stack Edge Pro R-Gerät für eine Azure Stack Edge-Ressource. Eine Hardwarekonfiguration und die Schaltfläche „Auswählen“ sind hervorgehoben.](media/azure-stack-edge-pro-r-deploy-prep/create-resource-3.png)

6. Geben Sie auf der Registerkarte **Grundlagen** die folgenden **Projektdetails** ein, bzw. wählen Sie sie aus:
    
    |Einstellung  |Wert  |
    |---------|---------|
    |Subscription    |Das Abonnement wird auf der Grundlage der zuvor getroffenen Auswahl automatisch eingetragen. Das Abonnement ist mit Ihrem Abrechnungskonto verknüpft. |
    |Resource group  |Wählen Sie eine vorhandene Gruppe aus, oder erstellen Sie eine neue Gruppe.<br>Erfahren Sie mehr über [Azure-Ressourcengruppen](../azure-resource-manager/management/overview.md).     |

7. Geben Sie die folgenden **Instanzendetails** ein, bzw. wählen Sie sie aus:

    |Einstellung  |Wert  |
    |---------|---------|
    |Name   | Ein Anzeigename, der die Ressource identifiziert.<br>Der Name muss 2 bis 50 Zeichen lang sein und darf nur Buchstaben, Zahlen und Bindestriche enthalten.<br> Der Name muss mit einem Buchstaben oder einer Zahl beginnen und enden.        |
    |Region     |Eine Liste aller Regionen, in denen die Azure Stack Edge-Ressource verfügbar ist, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Die für Azure Government verfügbaren Regionen finden Sie bei Bedarf unter [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/regions/).<br> Wählen Sie den Standort aus, der der geografischen Region, in der Ihr Gerät bereitgestellt werden soll, am nächsten ist.|

    ![Screenshot der Registerkarte „Grundlagen“ des Assistenten zum Erstellen einer Ressource und Bestellen eines Geräts für Azure Stack Edge. Die Registerkarte „Grundlagen“ und die Schaltfläche „Weiter: Lieferadresse“ sind hervorgehoben.](media/azure-stack-edge-pro-r-deploy-prep/create-resource-4.png)


8. Klicken Sie auf **Weiter: Lieferanschrift**.

    - Falls Sie bereits über ein Gerät verfügen, aktivieren Sie das Kontrollkästchen **Ich besitze bereits ein Gerät.** .

        ![Screenshot: Registerkarte „Lieferadresse“ mit der ausgewählten Option „Ich habe bereits eine Lieferadresse für das Gerät“ im Assistenten zum Erstellen einer Ressource für Azure Stack Edge.](media/azure-stack-edge-pro-r-deploy-prep/create-resource-5.png)

    - Handelt es sich dabei um das neue Gerät, das Sie bestellen möchten, geben Sie Kontaktname, Unternehmen, Lieferadresse für das Gerät sowie Kontaktinformationen ein.

        ![Screenshot: Registerkarte „Lieferadresse“ im Assistenten zum Erstellen einer Ressource beim Erstellen einer neuen Azure Stack Edge-Ressource.](media/azure-stack-edge-pro-r-deploy-prep/create-resource-6.png)

9. Klicken Sie auf **Weiter: Tags**. Geben Sie optional Tags an, um Ressourcen zu kategorisieren und die Abrechnung zu konsolidieren. Klicken Sie auf **Weiter: Überprüfen + erstellen**.

10. Überprüfen Sie auf der Registerkarte **Überprüfen + erstellen** die **Preisdetails**, die **Nutzungsbedingungen** und die Details für Ihre Ressource. Aktivieren Sie das Kontrollkästchen **Ich habe die angegebenen Informationen gelesen und stimme den Datenschutzbestimmungen zu.** .

    ![Screenshot: Registerkarte „Überprüfen Plus erstellen“ für eine Azure Stack Edge-Bestellung.](media/azure-stack-edge-pro-r-deploy-prep/create-resource-7.png) 

    Sie werden auch darüber informiert, dass während der Ressourcenerstellung eine verwaltete Dienstidentität (Managed Service Identity, MSI) aktiviert wird, mit der Sie sich bei Clouddiensten authentifizieren können. Diese Identität bleibt so lange bestehen, wie die Ressource vorhanden ist.

11. Klicken Sie auf **Erstellen**.

    Die Erstellung der Ressource dauert einige Minuten. Außerdem wird eine MSI-Datei erstellt, die die Kommunikation des Azure Stack Edge-Geräts mit dem Ressourcenanbieter in Azure ermöglicht.

    Nachdem die Ressource erfolgreich erstellt und bereitgestellt wurde, erhalten Sie eine Benachrichtigung. Wählen Sie **Zu Ressource wechseln** aus.

    ![Screenshot: Anzeige mit der Angabe, dass die Bereitstellung einer neuen Azure Stack Edge-Ressource abgeschlossen ist. Die Schaltfläche „Zu Ressource wechseln“ ist hervorgehoben.](media/azure-stack-edge-pro-r-deploy-prep/azure-stack-edge-resource-1.png)

Nach der Bestellung wird diese von Microsoft geprüft, und Sie erhalten eine E-Mail mit den Versanddetails.

<!--![Notification for review of the Azure Stack Edge Pro order](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-2.png) - If this is restored, it must go above "After the resource is successfully created." The azure-stack-edge-resource-1.png would seem superfluous in that case.--> 

> [!NOTE]
> Wenn Sie mehrere Bestellungen gleichzeitig erstellen oder eine vorhandene Bestellung klonen möchten, können Sie die [Skripts in den Azure-Beispielen](https://github.com/Azure-Samples/azure-stack-edge-order) verwenden. Weitere Informationen finden Sie in der Infodatei.

Wenn Sie während des Bestellvorgangs auf Probleme stoßen, informieren Sie sich unter [Behandeln von Bestellproblemen](azure-stack-edge-troubleshoot-ordering.md).

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Bereiten Sie Ihre Umgebung ggf. auf die Azure CLI vor.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Führen Sie zum Erstellen einer Azure Stack Edge-Ressource die folgenden Schritte über die Azure CLI aus:

1. Führen Sie den Befehl [az group create](/cli/azure/group#az_group_create) aus, um eine Ressourcengruppe zu erstellen, oder verwenden Sie eine bereits vorhandene Ressourcengruppe:

   ```azurecli
   az group create --name myasepgpu1 --location eastus
   ```

1. Verwenden Sie zum Erstellen eines Geräts den Befehl [az databoxedge device create](/cli/azure/databoxedge/device#az_databoxedge_device_create):

   ```azurecli
   az databoxedge device create --resource-group myasepgpu1 \
      --device-name myasegpu1 --location eastus --sku EdgePR_Base
   ```

   Wählen Sie den Standort aus, der der geografischen Region, in der Ihr Gerät bereitgestellt werden soll, am nächsten ist. Die Region speichert nur die Metadaten für die Geräteverwaltung. Die tatsächlichen Daten können in jedem Speicherkonto gespeichert werden.

   Eine Liste aller Regionen, in denen die Azure Stack Edge-Ressource verfügbar ist, finden Sie unter [Verfügbare Produkte nach Region](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). Die für Azure Government verfügbaren Regionen finden Sie bei Bedarf unter [Azure-Regionen](https://azure.microsoft.com/global-infrastructure/regions/).

1. Führen Sie zum Erstellen einer Bestellung den Befehl [az databoxedge order create](/cli/azure/databoxedge/order#az_databoxedge_order_create) aus:

   ```azurecli
   az databoxedge order create --resource-group myasepgpu1 \
      --device-name myasegpu1 --company-name "Contoso" \
      --address-line1 "1020 Enterprise Way" --city "Sunnyvale" \
      --state "California" --country "United States" --postal-code 94089 \
      --contact-person "Gus Poland" --email-list gus@contoso.com --phone 4085555555
   ```

Die Erstellung der Ressource dauert einige Minuten. Führen Sie zum Anzeigen der Bestellung den Befehl [az databoxedge order show](/cli/azure/databoxedge/order#az_databoxedge_order_show) aus:

```azurecli
az databoxedge order show --resource-group myasepgpu1 --device-name myasegpu1 
```

Nach einer Bestellung wird diese von Microsoft geprüft, und Sie erhalten eine E-Mail mit den Versanddetails.

---

## <a name="get-the-activation-key"></a>Abrufen des Aktivierungsschlüssels

Wenn die Azure Stack Edge-Ressource betriebsbereit ist, müssen Sie den Aktivierungsschlüssel abrufen. Dieser Schlüssel wird verwendet, um Ihr Azure Stack Edge Pro-Gerät zu aktivieren und mit der Ressource zu verbinden. Sie können diesen Schlüssel jetzt abrufen, während Sie sich im Azure-Portal befinden.

1. Wählen Sie die Ressource aus, die Sie erstellt haben, und wählen Sie dann **Übersicht** aus.

2. Geben Sie im rechten Bereich einen Namen für Azure Key Vault an, oder übernehmen Sie den Standardnamen. Der Schlüsseltresorname kann aus 3 bis 24 Zeichen bestehen.

   Für jede mit Ihrem Gerät aktivierte Azure Stack Edge-Ressource wird ein Schlüsseltresor erstellt. Im Schlüsseltresor können Sie Geheimnisse speichern und darauf zugreifen. So wird beispielsweise der Channel Integrity Key (CIK) für den Dienst im Schlüsseltresor gespeichert.

   Nachdem Sie einen Schlüsseltresornamen angegeben haben, wählen Sie **Generate activation key** (Aktivierungsschlüssel generieren) aus, um einen Aktivierungsschlüssel zu erstellen.

   ![Screenshot: Bereich „Übersicht“ für eine neu erstellte Azure Stack Edge-Ressource. Die Schaltfläche „Generate activation key“ (Aktivierungsschlüssel generieren) ist hervorgehoben.](media/azure-stack-edge-pro-r-deploy-prep/azure-stack-edge-resource-3.png)

   Warten Sie einige Minuten, während Schlüsseltresor und Aktivierungsschlüssel erstellt werden. Wählen Sie das Kopiersymbol, um den Schlüssel zu kopieren und für die spätere Verwendung zu speichern.

> [!IMPORTANT]
> - Der Aktivierungsschlüssel läuft drei Tage nach seiner Generierung ab.
> - Generieren Sie einen neuen Schlüssel, falls der Schlüssel abgelaufen ist. Der ältere Schlüssel ist nicht mehr gültig.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurden unter anderem folgende Azure Stack Edge-Themen behandelt:

> [!div class="checklist"]
> * Neue Ressource erstellen
> * Abrufen des Aktivierungsschlüssels

Im nächsten Tutorial erfahren Sie, wie Sie Azure Stack Edge installieren.

> [!div class="nextstepaction"]
> [Installieren von Azure Stack Edge](./azure-stack-edge-pro-r-deploy-install.md)
