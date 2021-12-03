---
title: 'Schnellstart: Erstellen einer Intel SGX-VM im Azure-Portal'
description: Zum Einstieg in Ihre Bereitstellungen lernen Sie, wie Sie im Azure-Portal schnell eine Intel SGX-VM erstellen können.
author: stempesta
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: quickstart
ms.date: 11/1/2021
ms.author: stempesta
ms.custom: mode-portal, ignite-fall-2021
ms.openlocfilehash: 68eec52333636c54ab9598d4823e4e794e50a3c4
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331944"
---
# <a name="quickstart-create-intel-sgx-vm-in-the-azure-portal"></a>Schnellstart: Erstellen einer Intel SGX-VM im Azure-Portal

Dieses Tutorial führt Sie durch den Prozess der Bereitstellung von Intel SGX-VMs im Azure-Portal. Andernfalls wird empfohlen, [Azure Marketplace](quick-create-marketplace.md)-Vorlagen zu nutzen.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie kein Azure-Abonnement besitzen, [erstellen Sie ein Konto](https://azure.microsoft.com/pricing/purchase-options/pay-as-you-go/), bevor Sie beginnen.

> [!NOTE]
> Mit kostenlosen Testkonten ist kein Zugriff auf die VMs in diesem Tutorial möglich. Führen Sie ein Upgrade auf ein Abonnement mit nutzungsbasierter Zahlung durch.


## <a name="sign-in-to-azure"></a>Anmelden bei Azure

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/)an.

1. Wählen Sie oben die Option **Ressource erstellen** aus.

1. Wählen Sie im Bereich auf der linken Seite **Compute** aus.

1. Wählen Sie **Virtuellen Computer erstellen** aus.

    ![Bereitstellen einer VM](media/quick-create-portal/compute-virtual-machine.png)

## <a name="configure-an-intel-sgx-virtual-machine"></a>Konfigurieren einer Intel SGX-VM

1. Wählen Sie auf der Registerkarte **Grundlagen** Ihr **Abonnement** und Ihre **Ressourcengruppe** aus.

1. Geben Sie im Feld **Name des virtuellen Computers** einen Namen für Ihre neue VM ein.

1. Geben Sie folgende Werte ein bzw. wählen diese aus:

   * **Region**: Wählen Sie die für Sie geeignete Azure-Region aus.

        > [!NOTE]
        > Intel SGX-VMs werden in bestimmten Regionen auf spezieller Hardware ausgeführt. Informationen zur aktuellen regionalen Verfügbarkeit finden Sie in [Verfügbare Regionen](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) unter „DCsv2-Serie“ oder „DCsv3/DCdsv3-Serie“.

1. Konfigurieren Sie das Betriebssystemimage, das Sie für Ihren virtuellen Computer verwenden möchten.

    * **Image wählen**: Wählen Sie für dieses Tutorial „Ubuntu 20.04 LTS (Gen 2)“ aus. Sie können auch „Ubuntu 18.04 LTS (Gen2)“ oder „Windows Server 2019“ auswählen.
    
    * **Update auf 2. Generation**: Wählen Sie unter „Image“ im Flyout **VM-Generation konfigurieren** und dann **2. Generation** aus.
    
        ![image](https://user-images.githubusercontent.com/63871188/137009767-421ee49a-ded8-4cfd-ac53-a3d6750880b9.png)


1. Wählen Sie über **Größe ändern** in der Größenauswahl einen virtuellen Computer mit Intel SGX-Funktionen aus. Klicken Sie in der VM-Größenauswahl auf **Alle Filter löschen**. Wählen Sie die Option **Filter hinzufügen** und **Familie** als Filtertyp aus. Wählen Sie anschließend nur **Vertrauliche Computeressource** aus.

    ![VMs der DCsv2-Serie](media/quick-create-portal/dcsv2-virtual-machines.png)

    > [!TIP]
    > Die Größen **DC(Zahl)s_v2**, **DC(Zahl)s_v3** und **DC(Zahl)ds_v3** sollten angezeigt werden. [Weitere Informationen](virtual-machine-solutions-sgx.md)

1. Geben Sie die folgenden Informationen ein:

   * **Authentifizierungstyp:** Wählen Sie **Öffentlicher SSH-Schlüssel** aus, wenn Sie eine Linux-VM erstellen. 

        > [!NOTE]
        > Für die Authentifizierung können Sie einen öffentlichen SSH-Schlüssel oder ein Kennwort verwenden. SSH ist sicherer. Wie Sie einen SSH-Schlüssel generieren, erfahren Sie unter [Erstellen und Verwenden eines SSH-Schlüsselpaars (öffentlich und privat) für virtuelle Linux-Computer in Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

    * **Benutzername**: Geben Sie den Administratornamen für die VM ein.

    * **Öffentlicher SSH-Schlüssel**: Geben Sie Ihren öffentlichen RSA-Schlüssel ein (falls zutreffend).
    
    * **Kennwort**: Geben Sie Ihr Kennwort für die Authentifizierung ein (falls zutreffend).

    * **Öffentliche Eingangsports**: Wählen Sie die Option **Ausgewählte Ports zulassen** und dann in der Liste **Öffentliche Eingangsports hinzufügen** die Optionen **SSH (22)** und **HTTP (80)** aus. Wählen Sie bei der Bereitstellung einer Windows-VM die Optionen **HTTP (80)** und **RDP (3389)** aus.  

    >[!Note]
    > Das Zulassen von RDP-/SSH-Ports wird für Produktionsbereitstellungen nicht empfohlen.  

     ![Eingehende Ports](media/quick-create-portal/inbound-port-virtual-machine.png)


1. Nehmen Sie Änderungen auf der Registerkarte **Datenträger** vor.

   * Die **DCsv2-Serie** unterstützt **SSD Standard**. **Premium SSD** wird für DC1, DC2 und DC4 unterstützt. 
   * Die **DCsv3- und DCdsv3-Serie** unterstützen **SSD Standard**, **Premium SSD** und **Disk Ultra**.

1. Nehmen Sie auf den folgenden Registerkarten die gewünschten Änderungen an den Einstellungen vor, oder übernehmen Sie die Standardeinstellungen.

    * **Netzwerk**
    * **Verwaltung**
    * **Gastkonfiguration**
    * **Tags**

1. Klicken Sie auf **Überprüfen + erstellen**.

1. Klicken Sie im Bereich **Bewerten + erstellen** auf **Erstellen**.

> [!NOTE]
> Fahren Sie im nächsten Abschnitt mit diesem Tutorial fort, wenn Sie eine Linux-VM bereitgestellt haben. Gehen Sie bei Bereitstellung einer Windows-VM so vor, dass Sie [diese Schritte für die Verbindungsherstellung mit Ihrer Windows-VM ausführen](../virtual-machines/windows/connect-logon.md) und dann das [OE SDK unter Windows installieren](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md).


## <a name="connect-to-the-linux-vm"></a>Herstellen einer Verbindung mit dem virtuellen Linux-Computer

Wenn Sie bereits über eine BASH-Shell verfügen, verwenden Sie den Befehl **ssh**, um eine Verbindung mit dem virtuellen Azure-Computer herzustellen. Ersetzen Sie im folgenden Befehl den VM-Benutzernamen und die IP-Adresse, um eine Verbindung mit Ihrem virtuellen Linux-Computer herzustellen.

```bash
ssh azureadmin@40.55.55.555
```

Sie finden die öffentliche IP-Adresse Ihrer VM im Azure-Portal im Abschnitt mit der „Übersicht“ für Ihre VM.

:::image type="content" source="media/quick-create-portal/public-ip-virtual-machine.png" alt-text="IP-Adresse im Azure-Portal":::

Wenn Sie mit Windows arbeiten und nicht über eine BASH-Shell verfügen, installieren Sie einen SSH-Client (beispielsweise PuTTY).

1. [Laden Sie PuTTY herunter, und installieren Sie es.](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)

1. Führen Sie PuTTY aus.

1. Geben Sie im Konfigurationsbildschirm von PuTTY die öffentliche IP-Adresse Ihres virtuellen Computers ein.

1. Wählen Sie **Öffnen** aus, und geben Sie Ihren Benutzernamen und Ihr Kennwort ein.

Weitere Informationen zum Herstellen einer Verbindung mit virtuellen Linux-Computern finden Sie unter [Erstellen einer Linux-VM mit dem Azure-Portal](../virtual-machines/linux/quick-create-portal.md).

> [!NOTE]
> Falls eine PuTTY-Sicherheitswarnung angezeigt wird, dass der Hostschlüssel des Servers nicht in der Registrierung zwischengespeichert wird, wählen Sie eine der folgenden Optionen aus. Wenn Sie diesem Host vertrauen, wählen Sie **Ja** aus, um den Schlüssel dem Cache von PuTTy hinzuzufügen und die Verbindungsherstellung fortzusetzen. Wenn Sie die Verbindung nur einmal herstellen möchten, ohne den Schlüssel dem Cache hinzuzufügen, wählen Sie **Nein** aus. Wenn Sie diesem Host nicht vertrauen, wählen Sie **Abbrechen** aus, um die Verbindungsherstellung abzubrechen.

## <a name="install-azure-dcap-client"></a>Installieren des Azure-DCAP-Clients

> [!NOTE]
> Trusted Hardware Identity Management (THIM) ist ein kostenloser Azure-Dienst, mit dem Sie die Hardware-Identitäten verschiedener Trusted Execution Environments (TEEs) verwalten können. Der Dienst ruft Begleitdaten vom Intel Provisioning Certification Service (PCS) ab und speichert sie zwischen. Er erzwingt zu Nachweiszwecken als Azure-Sicherheitsbaseline eine minimale Trusted Compute Base (TCB). Für Azure-VMs der DCsv3- und DCdsv3-Serie können die Intel-Zertifikate nur aus THIM abgerufen werden, da es nicht möglich ist, von den VMs aus direkte Aufrufe an den Intel-Dienst durchzuführen. 

Mit der Veröffentlichung der skalierbaren Xeon-Prozessoren von Intel® ändert sich die Unterstützung des Remotenachweises. DCsv3 und DCdsv3 unterstützen nur [ECDSA-basierte Nachweise](https://www.intel.com/content/www/us/en/developer/tools/software-guard-extensions/attestation-services.html), und Benutzer müssen den [Azure DCAP](https://github.com/Microsoft/Azure-DCAP-Client)-Client installieren, um mit THIM zu interagieren und TEE-Begleitdaten für die Angebotserstellung während des Nachweisverfahrens abzurufen. DCsv2 unterstützt weiterhin [EPID-basierte Nachweise](https://www.intel.com/content/www/us/en/developer/tools/software-guard-extensions/attestation-services.html). 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Ressourcengruppe, der virtuelle Computer und alle zugehörigen Ressourcen nicht mehr benötigt werden, können Sie sie löschen. 

Wählen Sie die Ressourcengruppe für den virtuellen Computer und dann **Löschen** aus. Bestätigen Sie dann den Namen der Ressourcengruppe, um das Löschen der Ressourcen abzuschließen.

## <a name="next-steps"></a>Nächste Schritte

In dieser Schnellstart haben Sie eine Intel SGX-VM bereitgestellt und eine Verbindung mit ihr hergestellt. Weitere Informationen finden Sie unter [Lösungen auf virtuellen Computern](virtual-machine-solutions-sgx.md). 

Machen Sie sich damit vertraut, wie Sie Confidential Computing-Anwendungen entwickeln, indem Sie mit den Beispielen zum Open Enclave SDK auf GitHub fortfahren. 

> [!div class="nextstepaction"]
> [Entwickeln von Open Enclave SDK-Beispielen](https://github.com/openenclave/openenclave/blob/master/samples/README.md)

Microsoft Azure Attestation ist kostenlos und verwendet das ECDSA-basierte Nachweisframework, um die Vertrauenswürdigkeit mehrerer TEEs und die Integrität der darin ausgeführten Binärdateien remote zu überprüfen. Weitere Informationen finden Sie [hier](/azure/attestation/overview).

