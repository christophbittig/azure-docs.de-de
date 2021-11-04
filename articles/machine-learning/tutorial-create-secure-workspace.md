---
title: Erstellen eines sicheren Arbeitsbereichs
titleSuffix: Azure Machine Learning
description: Erstellen Sie einen Azure Machine Learning-Arbeitsbereich und die erforderlichen Azure-Dienste in einem sicheren virtuellen Netzwerk.
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.reviewer: jhirono
ms.author: larryfr
author: blackmist
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: subject-rbac-steps
ms.openlocfilehash: c488b3ec12d0aabcfb84b0ebb700eb738e8698e1
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558058"
---
# <a name="how-to-create-a-secure-workspace"></a>So erstellen Sie einen sicheren Arbeitsbereich

In diesem Artikel erfahren Sie, wie Sie einen sicheren Arbeitsbereich erstellen und mit einem Azure Machine Learning-Arbeitsbereich verbinden. Für einen sicheren Arbeitsbereich wird Azure Virtual Network dazu verwendet, eine Sicherheitsgrenze um Ressourcen herum zu erstellen, die von Azure Machine Learning verwendet werden. 

Sie führen in diesem Tutorial die folgenden Aufgaben durch:

> [!div class="checklist"]
> * Sie erstellen ein Azure Virtual Network (VNet), um die __Kommunikation zwischen Diensten im virtuellen Netzwerk abzusichern__.
> * Sie erstellen ein Azure Storage-Konto (Blob und Datei) hinter dem VNet. Dieser Dienst wird als __Standardspeicher für den Arbeitsbereich__ verwendet.
> * Sie erstellen einen Azure Key Vault hinter dem VNet. Dieser Dienst wird zum __Speichern der vom Arbeitsbereich verwendeten Geheimnisse__ verwendet. Beispiel: Sicherheitsinformationen, die für den Zugriff auf das Speicherkonto erforderlich sind.
> * Sie erstellen eine Azure Container Registry(ACR)-Instanz. Dieser Dienst wird als Repository für Docker-Images verwendet. __Docker-Images stellen die Computeumgebungen bereit, die beim Trainieren eines Machine Learning-Modells oder beim Bereitstellen eines trainierten Modells als Endpunkt erforderlich sind__.
> * Erstellen Sie einen Azure Machine Learning-Arbeitsbereich.
> * Sie erstellen eine Jumpbox. Eine Jumpbox ist ein virtueller Azure-Computer, der sich hinter dem VNet befindet. Da das VNet den Zugriff über das öffentliche Internet einschränkt, __wird die Jumpbox als Möglichkeit zum Herstellen einer Verbindung mit Ressourcen hinter dem VNet verwendet__.
> * Sie konfigurieren Azure Machine Learning Studio für die Arbeit hinter einem VNet. Azure Machine Learning Studio stellt eine __Webschnittstelle für Azure Machine Learning__ bereit.
> * Erstellen eines Computeclusters für Azure Machine Learning Ein Computecluster wird verwendet, wenn __Machine Learning-Modelle in der Cloud trainiert werden__. In Konfigurationen, in denen sich Azure Container Registry hinter dem VNet befindet, wird es auch zum Erstellen von Docker-Images verwendet.
> * Sie stellen eine Verbindung zur Jumpbox her und verwenden Azure Machine Learning Studio.

Wenn Ihre Umgebung die Voraussetzungen erfüllt und Sie mit der Verwendung von ARM-Vorlagen vertraut sind, können Sie die ersten fünf Schritte in diesem Tutorial auch durch Auswählen der Schaltfläche „Bereitstellung in Azure“ ausführen. Sie können mit dem Lesen bei [Herstellen der Verbindung zum Arbeitsbereich](#connect-to-the-workspace) fortfahren.

[![Bereitstellung in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.machinelearningservices%2Fmachine-learning-workspace-vnet%2Fazuredeploy.json)
[![Bereitstellung in Azure US Gov](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazuregov.svg?sanitize=true)](https://portal.azure.us/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.machinelearningservices%2Fmachine-learning-workspace-vnet%2Fazuredeploy.json)

## <a name="prerequisites"></a>Voraussetzungen

* Vertrautheit mit virtuellen Azure-Netzwerken und IP-Netzwerken
* Während für die meisten Schritte in diesem Artikel das Azure-Portal oder Azure Machine Learning Studio verwendet werden, wird bei einigen Schritten die Azure CLI-Erweiterung für Machine Learning eingesetzt.

## <a name="create-a-virtual-network"></a>Erstellen eines virtuellen Netzwerks

Führen Sie die folgenden Schritte aus, um ein virtuelles Netzwerk zu erstellen:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) oben links das Portalmenü aus. Wählen Sie im Menü die Option __+ Ressource erstellen__ aus, und geben Sie anschließend im Suchfeld den Eintrag __Virtuelles Netzwerk__ ein. Suchen Sie nach dem Eintrag __Virtuelles Netzwerk__, und wählen Sie dann __Erstellen__ aus.


    :::image type="content" source="./media/tutorial-create-secure-workspace/create-resource-search-vnet.png" alt-text="Benutzeroberflächensuche zum Erstellen von Ressourcen":::

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-resource-vnet.png" alt-text="Erstellen eines virtuellen Netzwerks":::

1. Wählen Sie auf der Registerkarte __Grundlagen__ das Azure-__Abonnement__ aus, das für diese Ressource verwendet werden soll, und wählen Sie dann eine __Ressourcengruppe__ aus, oder erstellen Sie eine neue. Geben Sie unter __Instanzdetails__ einen benutzerfreundlichen __Namen__ für Ihr virtuelles Netzwerk ein, und wählen Sie die __Region__ aus, in der es erstellt werden soll.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-vnet-basics.png" alt-text="Abbildung der grundlegenden Konfiguration des virtuellen Netzwerks":::

1. Wählen Sie die Registerkarte __IP-Adressen__ aus. Die Standardeinstellungen sollten der folgenden Abbildung ähneln:

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-vnet-ip-address-default.png" alt-text="Bildschirm „Standard-IP-Adresse“":::

    Führen Sie die folgenden Schritte aus, um die IP-Adresse und ein Subnetz für Trainings- und Bewertungsressourcen zu konfigurieren:

    > [!TIP]
    > Sie können zwar ein einzelnes Subnetz für alle Azure ML-Ressourcen verwenden, die Schritte in diesem Artikel zeigen jedoch, wie Sie zwei Subnetze erstellen, um die Trainings- und die Bewertungsressourcen zu trennen.
    >
    > Der Arbeitsbereich und andere Abhängigkeitsdienste werden im Trainingssubnetz eingerichtet. Sie können weiterhin von Ressourcen in anderen Subnetzen verwendet werden, z. B. dem Bewertungssubnetz.

    1. Beachten Sie den Standardwert des __IPv4-Adressraums__. Im Screenshot lautet der Wert __172.17.0.0/16__. __Der Wert kann für Sie anders lauten__. Sie können zwar einen anderen Wert verwenden, die restlichen Schritte in diesem Tutorial basieren jedoch auf dem Wert 172.17.0.0/16.
    1. Wählen Sie das __Standardsubnetz__ und anschließend die Option __Subnetz entfernen__ aus.
    
        :::image type="content" source="./media/tutorial-create-secure-workspace/delete-default-subnet.png" alt-text="Screenshot: Löschen des Standardsubnetzes":::

    1. Wählen Sie __+ Subnetz hinzufügen__ aus, und verwenden Sie die folgenden Werte für das Subnetz, um ein Subnetz zu erstellen, das den Arbeitsbereich, die Abhängigkeitsdienste und die Ressourcen enthält, die für das Training verwendet werden:
        * __Subnetzname__: Training
        * __Subnetzadressbereich__: 172.17.0.0/24

        :::image type="content" source="./media/tutorial-create-secure-workspace/vnet-add-training-subnet.png" alt-text="Screenshot: Trainingssubnetz":::

        > [!TIP]
        > Wenn Sie einen _Dienstendpunkt_ verwenden möchten, um Ihr Azure Storage-Konto, Azure Key Vault und Azure Container Registry zum VNet hinzuzufügen, wählen Sie unter __Dienste__ Folgendes aus:
        > * __Microsoft.Storage__
        > * __Microsoft.KeyVault__
        > * __Microsoft.ContainerRegistry__
        >
        > Wenn Sie einen _privaten Endpunkt_ verwenden möchten, um diese Dienste dem VNet hinzuzufügen, müssen Sie diese Einträge nicht auswählen. Die Schritte in diesem Artikel verwenden einen privaten Endpunkt für diese Dienste, sodass Sie sie nicht auswählen müssen, wenn Sie diese Schritte befolgen.

    1. Wählen Sie erneut __+ Subnetz hinzufügen__ aus, und verwenden Sie die folgenden Werte, um ein Subnetz für Computeressourcen zu erstellen, die zur Bewertung Ihrer Modelle verwendet werden:
        * __Subnetzname__: Bewertung
        * __Subnetzadressbereich__: 172.17.1.0/24

        :::image type="content" source="./media/tutorial-create-secure-workspace/vnet-add-scoring-subnet.png" alt-text="Screenshot: Bewertungssubnetz":::

        > [!TIP]
        > Wenn Sie einen _Dienstendpunkt_ verwenden möchten, um Ihr Azure Storage-Konto, Azure Key Vault und Azure Container Registry zum VNet hinzuzufügen, wählen Sie unter __Dienste__ Folgendes aus:
        > * __Microsoft.Storage__
        > * __Microsoft.KeyVault__
        > * __Microsoft.ContainerRegistry__
        >
        > Wenn Sie einen _privaten Endpunkt_ verwenden möchten, um diese Dienste dem VNet hinzuzufügen, müssen Sie diese Einträge nicht auswählen. Die Schritte in diesem Artikel verwenden einen privaten Endpunkt für diese Dienste, sodass Sie sie nicht auswählen müssen, wenn Sie diese Schritte befolgen.

1. Wählen Sie __Sicherheit__ aus. Wählen Sie für __BastionHost__ die Option __Aktivieren__ aus. [Azure Bastion](../bastion/bastion-overview.md) bietet eine sichere Möglichkeit für den Zugriff auf die VM-Jumpbox, die Sie in einem späteren Schritt im VNet erstellen. Verwenden Sie die folgenden Werte für die restlichen Felder:

    * __Bastion-Name__: Ein eindeutiger Name für diese Bastion-Instanz
    * __AzureBastionSubnetAddress space__ (Azure-Bastion-Subnetz-Adressbereich): 172.17.2.0/27
    * __Öffentliche IP-Adresse__: Erstellen Sie eine neue öffentliche IP-Adresse.

    Behalten Sie für die anderen Felder den Standardwert bei.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-bastion.png" alt-text="Screenshot: Bastion-Konfiguration":::

1. Klicken Sie auf __Überprüfen + erstellen__.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-vnet-ip-address-final.png" alt-text="Screenshot: Schaltfläche „Überprüfen + erstellen“":::

1. Vergewissern Sie sich, dass die Informationen richtig sind, und wählen Sie dann __Erstellen__ aus.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-vnet-review.png" alt-text="Screenshot: Seite „Überprüfen“":::

## <a name="create-a-storage-account"></a>Erstellen eines Speicherkontos

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) oben links das Portalmenü aus. Wählen Sie im Menü die Option __+ Ressource erstellen__ aus, und geben Sie dann __Speicherkonto__ ein. Wählen Sie den __Speicherkonto__-Eintrag und dann __Erstellen__ aus.
1. Wählen Sie auf der Registerkarte __Grundlagen__ die Einträge für __Abonnement__, __Ressourcengruppe__ und __Region__ aus, die Sie zuvor für das virtuelle Netzwerk verwendet haben. Geben Sie einen eindeutigen __Speicherkontonamen__ ein, und legen Sie __Redundanz__ auf __Lokal redundanter Speicher (LRS)__ fest.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-storage.png" alt-text="Abbildung der grundlegenden Konfiguration des Speicherkontos":::

1. Wählen Sie auf der Registerkarte __Netzwerk__ die Option __Privater Endpunkt__ und dann __+ Privaten Endpunkt hinzufügen__ aus.

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-enable-private-endpoint.png" alt-text="Benutzeroberfläche zum Hinzufügen des privaten Blobnetzwerks":::

1. Verwenden Sie im Formular __Privaten Endpunkt erstellen__ die folgenden Werte:
    * __Abonnement__: Dasselbe Azure-Abonnement, das die zuvor erstellten Ressourcen enthält
    * __Ressourcengruppe__: Dieselbe Azure-Ressourcengruppe, die die zuvor erstellten Ressourcen enthält
    * __Standort__: Dieselbe Azure-Region, die die zuvor erstellten Ressourcen enthält
    * __Name__: Ein eindeutiger Name für diesen privaten Endpunkt
    * __Untergeordnete Zielressource__: Blob
    * __Virtuelles Netzwerk:__ Das zuvor erstellte virtuelle Netzwerk
    * __Subnetz__: Training (172.17.0.0/24)
    * __Private DNS-Integration__: Ja
    * __Private DNS-Zone__: privatelink.blob.core.windows.net

    Wählen Sie __OK__ aus, um den privaten Endpunkt zu erstellen.

1. Klicken Sie auf __Überprüfen + erstellen__. Vergewissern Sie sich, dass die Informationen richtig sind, und wählen Sie dann __Erstellen__ aus.

1. Wählen Sie nach dem Erstellen des Speicherkontos __Zu Ressource wechseln__ aus.

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-go-to-resource.png" alt-text="Zu neuer Speicherressource wechseln":::

1. Wählen Sie im linken Navigationsbereich die Option __Netzwerk__, die Registerkarte __Private Endpunktverbindungen__ und dann __+ Privater Endpunkt__ aus:

    > [!NOTE]
    > Während Sie in den vorherigen Schritten einen privaten Endpunkt für Blob Storage erstellt haben, müssen Sie auch einen für File Storage erstellen.

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-file-networking.png" alt-text="Benutzeroberfläche für Speicherkontonetzwerke":::

1. Verwenden Sie im Formular __Privaten Endpunkt erstellen__ dieselben Einträge für __Abonnement__, __Ressourcengruppe__ und __Region__, die Sie für vorherige Ressourcen verwendet haben. Geben Sie einen eindeutigen __Namen__ ein.

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-file-private-endpoint.png" alt-text="Benutzeroberfläche zum Hinzufügen des privaten Endpunkts für Dateien":::

1. Wählen Sie __Next : Resource__ (Weiter : Ressource) aus, und legen Sie dann __Untergeordnete Zielressource__ auf __Datei__ fest.

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-file-private-endpoint-resource.png" alt-text="Hinzufügen der untergeordneten Ressource „Datei“":::

1. Wählen Sie __Next : Configuration__ (Weiter : Konfiguration) aus, und verwenden Sie dann die folgenden Werte:
    * __Virtuelles Netzwerk__: Das zuvor erstellte virtuelle Netzwerk
    * __Subnetz__: Training
    * __Integration mit einer private DNS-Zone__: Ja
    * __Private DNS-Zone__: privatelink.file.core.windows.net

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-file-private-endpoint-config.png" alt-text="Benutzeroberfläche zum Konfigurieren des privaten Endpunkts für Dateien":::

1. Klicken Sie auf __Überprüfen + erstellen__. Vergewissern Sie sich, dass die Informationen richtig sind, und wählen Sie dann __Erstellen__ aus.

> [!TIP]
> Wenn Sie [ParallelRunStep](./tutorial-pipeline-batch-scoring-classification.md) in Ihrer Pipeline verwenden möchten, müssen Sie auch Zielunterressourcen vom Typ **Warteschlange** und **Tabelle** für private Endpunkte konfigurieren. „ParallelRunStep“ verwendet Warteschlangen und Tabellen im Hintergrund für die Planung und Verteilung von Aufgaben.

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) oben links das Portalmenü aus. Wählen Sie im Menü die Option __+ Ressource erstellen__ aus, und geben Sie dann __Schlüsseltresor__ ein. Wählen Sie den __Schlüsseltresor__-Eintrag und dann __Erstellen__ aus.
1. Wählen Sie auf der Registerkarte __Grundlagen__ die Einträge für __Abonnement__, __Ressourcengruppe__ und __Region__ aus, die Sie zuvor für das virtuelle Netzwerk verwendet haben. Geben Sie einen eindeutigen Namen für __Schlüsseltresorname__ an. Behalten Sie für die anderen Felder den Standardwert bei.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-key-vault.png" alt-text="Erstellen eines neuen Schlüsseltresors":::

1. Wählen Sie auf der Registerkarte __Netzwerk__ die Option __Privater Endpunkt__ und dann __+ Hinzufügen__ aus.

    :::image type="content" source="./media/tutorial-create-secure-workspace/key-vault-networking.png" alt-text="Schlüsseltresor – Netzwerk":::

1. Verwenden Sie im Formular __Privaten Endpunkt erstellen__ die folgenden Werte:
    * __Abonnement__: Dasselbe Azure-Abonnement, das die zuvor erstellten Ressourcen enthält
    * __Ressourcengruppe__: Dieselbe Azure-Ressourcengruppe, die die zuvor erstellten Ressourcen enthält
    * __Standort__: Dieselbe Azure-Region, die die zuvor erstellten Ressourcen enthält
    * __Name__: Ein eindeutiger Name für diesen privaten Endpunkt
    * __Untergeordnete Zielressource__: Tresor
    * __Virtuelles Netzwerk:__ Das zuvor erstellte virtuelle Netzwerk
    * __Subnetz__: Training (172.17.0.0/24)
    * __Private DNS-Integration__: Ja
    * __Private DNS-Zone__: privatelink.vaultcore.azure.net

    Wählen Sie __OK__ aus, um den privaten Endpunkt zu erstellen.

    :::image type="content" source="./media/tutorial-create-secure-workspace/key-vault-private-endpoint.png" alt-text="Konfigurieren eines privaten Endpunkts für einen Schlüsseltresor":::

1. Klicken Sie auf __Überprüfen + erstellen__. Vergewissern Sie sich, dass die Informationen richtig sind, und wählen Sie dann __Erstellen__ aus.

## <a name="create-a-container-registry"></a>Erstellen einer Containerregistrierung

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) oben links das Portalmenü aus. Wählen Sie im Menü die Option __+ Ressource erstellen__ aus, und geben Sie dann __Containerregistrierung__ ein. Wählen Sie den Eintrag __Containerregistrierung__ und dann __Erstellen__ aus.
1. Wählen Sie auf der Registerkarte __Grundlagen__ die Einträge für __Abonnement__, __Ressourcengruppe__ und __Ort__ aus, die Sie zuvor für das virtuelle Netzwerk verwendet haben. Geben Sie einen eindeutigen Wert für __Registrierungsname__ ein, und legen Sie die __SKU__ auf __Premium__ fest.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-container-registry.png" alt-text="Erstellen einer Containerregistrierung":::

1. Wählen Sie auf der Registerkarte __Netzwerk__ die Option __Privater Endpunkt__ und dann __+ Hinzufügen__ aus.

    :::image type="content" source="./media/tutorial-create-secure-workspace/container-registry-networking.png" alt-text="Containerregistrierung – Netzwerk":::

1. Verwenden Sie im Formular __Privaten Endpunkt erstellen__ die folgenden Werte:
    * __Abonnement__: Dasselbe Azure-Abonnement, das die zuvor erstellten Ressourcen enthält
    * __Ressourcengruppe__: Dieselbe Azure-Ressourcengruppe, die die zuvor erstellten Ressourcen enthält
    * __Standort__: Dieselbe Azure-Region, die die zuvor erstellten Ressourcen enthält
    * __Name__: Ein eindeutiger Name für diesen privaten Endpunkt
    * __Untergeordnete Zielressource__: Registrierung
    * __Virtuelles Netzwerk:__ Das zuvor erstellte virtuelle Netzwerk
    * __Subnetz__: Training (172.17.0.0/24)
    * __Private DNS-Integration__: Ja
    * __Private DNS-Zone__: privatelink.azurecr.io

    Wählen Sie __OK__ aus, um den privaten Endpunkt zu erstellen.

    :::image type="content" source="./media/tutorial-create-secure-workspace/container-registry-private-endpoint.png" alt-text="Konfigurieren eines privaten Endpunkts für eine Containerregistrierung":::

1. Klicken Sie auf __Überprüfen + erstellen__. Vergewissern Sie sich, dass die Informationen richtig sind, und wählen Sie dann __Erstellen__ aus.
1. Nachdem die Containerregistrierung erstellt wurde, wählen Sie __Zu Ressource wechseln__ aus.

    :::image type="content" source="./media/tutorial-create-secure-workspace/container-registry-go-to-resource.png" alt-text="Auswählen von „Zu Ressource wechseln“":::

1. Wählen Sie im linken Bereich der Seite die Option __Zugriffsschlüssel__ aus, und aktivieren Sie dann den __Administratorbenutzer__. Diese Einstellung ist erforderlich, wenn Sie Azure Container Registry in einem virtuellen Netzwerk mit Azure Machine Learning verwenden.

    :::image type="content" source="./media/tutorial-create-secure-workspace/container-registry-admin-user.png" alt-text="Screenshot: Umschalten der Einstellung „Administratorbenutzer“":::

## <a name="create-a-workspace"></a>Erstellen eines Arbeitsbereichs

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) oben links das Portalmenü aus. Wählen Sie im Menü die Option __+ Ressource erstellen__ aus, und geben Sie dann __Machine Learning__ ein. Wählen Sie den Eintrag __Machine Learning__ und dann __Erstellen__ aus.

    :::image type="content" source="./media/tutorial-create-secure-workspace/machine-learning-create.png" alt-text="{alt-text}":::

1. Wählen Sie auf der Registerkarte __Grundlagen__ die Einträge für __Abonnement__, __Ressourcengruppe__ und __Region__ aus, die Sie zuvor für das virtuelle Netzwerk verwendet haben. Verwenden Sie für die übrigen Felder die folgenden Werte:
    * __Arbeitsbereichsname__: Ein eindeutiger Name für den Arbeitsbereich
    * __Speicherkonto__: Wählen Sie das Speicherkonto aus, das Sie zuvor erstellt haben.
    * __Schlüsseltresor__: Wählen Sie den Schlüsseltresor aus, den Sie zuvor erstellt haben.
    * __Application Insights__: Verwenden Sie den Standardwert.
    * __Containerregistrierung__: Verwenden Sie die zuvor erstellte Containerregistrierung.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-machine-learning-workspace.png" alt-text="Grundlegende Arbeitsbereichskonfiguration":::

1. Wählen Sie auf der Registerkarte __Netzwerk__ die Option __Privater Endpunkt__ und dann __+ Hinzufügen__ aus.

    :::image type="content" source="./media/tutorial-create-secure-workspace/machine-learning-workspace-networking.png" alt-text="Arbeitsbereich – Netzwerk":::

1. Verwenden Sie im Formular __Privaten Endpunkt erstellen__ die folgenden Werte: 
    * __Abonnement__: Dasselbe Azure-Abonnement, das die zuvor erstellten Ressourcen enthält
    * __Ressourcengruppe__: Dieselbe Azure-Ressourcengruppe, die die zuvor erstellten Ressourcen enthält
    * __Standort__: Dieselbe Azure-Region, die die zuvor erstellten Ressourcen enthält
    * __Name__: Ein eindeutiger Name für diesen privaten Endpunkt
    * __Untergeordnete Zielressource__: amlworkspace
    * __Virtuelles Netzwerk:__ Das zuvor erstellte virtuelle Netzwerk
    * __Subnetz__: Training (172.17.0.0/24)
    * __Private DNS-Integration__: Ja
    * __Private DNS-Zone__: Lassen Sie die beiden privaten DNS-Zonen auf die Standardwerte __privatelink.api.azureml.ms__ und __privatelink.notebooks.azure.net__ festgelegt.

    Wählen Sie __OK__ aus, um den privaten Endpunkt zu erstellen.

    :::image type="content" source="./media/tutorial-create-secure-workspace/machine-learning-workspace-private-endpoint.png" alt-text="Screenshot: Konfiguration des privaten Netzwerks des Arbeitsbereichs":::

1. Klicken Sie auf __Überprüfen + erstellen__. Vergewissern Sie sich, dass die Informationen richtig sind, und wählen Sie dann __Erstellen__ aus.
1. Nachdem der Arbeitsbereich erstellt wurde, wählen Sie __Zu Ressource wechseln__ aus.
1. Wählen Sie links im Abschnitt __Einstellungen__ die Option __Private Endpunktverbindungen__ und dann den Link in der Spalte __Privater Endpunkt__ aus:

    :::image type="content" source="./media/tutorial-create-secure-workspace/workspace-private-endpoint-connections.png" alt-text="Screenshot: Verbindungen mit privaten Endpunkten für Arbeitsbereiche":::

1. Sobald die Informationen zum privaten Endpunkt angezeigt werden, wählen Sie links auf der Seite die Option __DNS-Konfiguration__ aus. Speichern Sie die IP-Adresse und den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) auf dieser Seite, da sie später verwendet werden.

    :::image type="content" source="./media/tutorial-create-secure-workspace/workspace-private-endpoint-dns.png" alt-text="Screenshot: Einträge IP-Adresse und FQDN":::

> [!IMPORTANT]
> Es sind noch einige Konfigurationsschritte erforderlich, bevor Sie den Arbeitsbereich vollständig verwenden können. Für diese müssen Sie jedoch eine Verbindung mit dem Arbeitsbereich herstellen.

## <a name="enable-studio"></a>Aktivieren von Studio

Azure Machine Learning Studio ist eine webbasierte Anwendung, mit der Sie Ihren Arbeitsbereich problemlos verwalten können. Es ist jedoch eine zusätzliche Konfiguration erforderlich, bevor die Anwendung mit Ressourcen verwendet werden kann, die in einem VNet abgesichert sind. Führen Sie die folgenden Schritte aus, um Studio zu aktivieren:

1. Wenn Sie ein Azure Storage-Konto verwenden, das über einen privaten Endpunkt verfügt, fügen Sie den Dienstprinzipal für den Arbeitsbereich als __Leser__ für die privaten Speicherendpunkte hinzu. Wählen Sie im Azure-Portal Ihr Speicherkonto und dann __Netzwerk__ aus. Wählen Sie als Nächstes __Private Endpunktverbindungen__ aus.

    :::image type="content" source="./media/tutorial-create-secure-workspace/storage-private-endpoint-select.png" alt-text="Screenshot: private Speicherendpunkte":::

1. Führen Sie für __jeden aufgeführten privaten Endpunkt__ die folgenden Schritte aus:

    1. Wählen Sie den Link in der Spalte __Privater Endpunkt__ aus.
    
        :::image type="content" source="./media/tutorial-create-secure-workspace/storage-private-endpoint-selected.png" alt-text="Screenshot: auszuwählende Endpunkte":::

    1. Wählen Sie auf der linken Seite __Zugriffssteuerung (IAM)__ aus.
    1. Wählen Sie __+ Hinzufügen__ und dann __Rollenzuweisung hinzufügen (Vorschau)__ aus.

        ![Seite „Zugriffssteuerung (IAM)“ mit geöffnetem Menü „Rollenzuweisung hinzufügen“](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

    1. Wählen Sie auf der Registerkarte __Rollen__ die Rolle __Leser__ aus.

        ![Seite „Rollenzuweisung hinzufügen“ mit ausgewählter Registerkarte „Rolle“](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

    1. Wählen Sie auf der Registerkarte __Mitglieder__ im Bereich __Zugriff zuweisen zu__ die Option __Benutzer, Gruppe oder Dienstprinzipal__ und dann die Option __+ Mitglieder auswählen__ aus. Geben Sie im Dialogfeld __Mitglieder auswählen__ den Namen als Azure Machine Learning-Arbeitsbereich ein. Wählen Sie den Dienstprinzipal für den Arbeitsbereich aus, und verwenden Sie dann die Schaltfläche __Auswählen__.

    1. Wählen Sie auf der Registerkarte **Überprüfen und zuweisen** die Option **Überprüfen und zuweisen** aus, um die Rolle zuzuweisen.

## <a name="connect-to-the-workspace"></a>Herstellen einer Verbindung mit dem Arbeitsbereich

Es gibt mehrere Möglichkeiten, eine Verbindung mit dem geschützten Arbeitsbereich herzustellen. In den Schritten in diesem Artikel wird eine __Jumpbox__ verwendet, bei der es sich um einen virtuellen Computer im VNet handelt. Sie können über den Webbrowser und Azure Bastion eine Verbindung damit herstellen. In der folgenden Tabelle sind mehrere andere Möglichkeiten aufgeführt, wie Sie eine Verbindung mit dem sicheren Arbeitsbereich herstellen können:

| Methode | BESCHREIBUNG |
| ----- | ----- |
| [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) | Stellt über eine private Verbindung eine Verbindung zwischen lokalen Netzwerken und dem VNet her. Die Verbindung erfolgt über das öffentliche Internet. |
| [ExpressRoute](https://azure.microsoft.com/services/expressroute/) | Stellt über eine private Verbindung eine Verbindung zwischen lokalen Netzwerken und der Cloud her. Die Verbindung erfolgt mithilfe eines Konnektivitätsanbieters. |

> [!IMPORTANT]
> Wenn Sie ein __VPN-Gateway__ oder __ExpressRoute__ verwenden, müssen Sie planen, wie die Namensauflösung zwischen den lokalen Ressourcen und denen im VNet funktioniert. Weitere Informationen finden Sie unter [Verwenden eines benutzerdefinierten DNS-Servers](how-to-custom-dns.md).

### <a name="create-a-jump-box-vm"></a>Erstellen einer Jumpbox (VM)

Verwenden Sie die folgenden Schritte, um eine Data Science Virtual Machine für die Verwendung als Jumpbox zu erstellen:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) oben links das Portalmenü aus. Wählen Sie im Menü die Option __+ Ressource erstellen__ aus, und geben Sie dann __Data Science Virtual Machine__ ein. Wählen Sie den Eintrag __Data Science Virtual Machine – Windows__ und anschließend __Erstellen__ aus.
1. Wählen Sie auf der Registerkarte __Grundlagen__ die Einträge für __Abonnement__, __Ressourcengruppe__ und __Region__ aus, die Sie zuvor für das virtuelle Netzwerk verwendet haben. Geben Sie einen eindeutigen Wert für __Name des virtuellen Computers__, __Benutzername__ und __Kennwort__ ein. Behalten Sie für die anderen Felder den Standardwert bei.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-virtual-machine-basic.png" alt-text="Abbildung der grundlegenden VM-Konfiguration":::

1. Wählen Sie __Netzwerk__ und dann das zuvor erstellte __Virtuelle Netzwerk__ aus. Legen Sie die übrigen Felder mithilfe folgender Informationen fest:

    * Wählen Sie das Subnetz __Training__ aus.
    * Legen Sie das Feld __Öffentliche IP-Adresse__ auf __Keine__ fest.
    * Behalten Sie für die anderen Felder den Standardwert bei.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-virtual-machine-network.png" alt-text="Abbildung der VM-Netzwerkkonfiguration":::

1. Klicken Sie auf __Überprüfen + erstellen__. Vergewissern Sie sich, dass die Informationen richtig sind, und wählen Sie dann __Erstellen__ aus.


### <a name="connect-to-the-jump-box"></a>Herstellen einer Verbindung mit der Jumpbox

1. Nachdem der virtuelle Computer erstellt wurde, wählen Sie __Zu Ressource wechseln__ aus.
1. Wählen Sie oben auf der Seite die Option __Verbinden__ und dann __Bastion__ aus.

    :::image type="content" source="./media/tutorial-create-secure-workspace/virtual-machine-connect.png" alt-text="Abbildung der Benutzeroberfläche mit den Optionen „Verbinden“ und „Bastion“":::

1. Wählen Sie __Bastion verwenden__ aus, und geben Sie dann Ihre Authentifizierungsinformationen für den virtuellen Computer an. Daraufhin wird im Browser eine Verbindung hergestellt.

    :::image type="content" source="./media/tutorial-create-secure-workspace/use-bastion.png" alt-text="Abbildung des Dialogfelds „Bastion verwenden“":::

## <a name="create-a-compute-cluster-and-compute-instance"></a>Erstellen eines Computeclusters und einer Compute-Instanz

Ein Computecluster wird von Ihren Trainingsaufträgen verwendet. Eine Compute-Instanz ermöglicht eine Jupyter Notebook-Nutzung auf einer freigegebenen Computeressource, die an Ihren Arbeitsbereich angefügt ist.

1. Öffnen Sie über eine Azure Bastion-Verbindung mit der Jumpbox den __Microsoft Edge__-Browser auf dem Remotedesktop.
1. Wechseln Sie in der Remotebrowsersitzung zu __https://ml.azure.com__ . Authentifizieren Sie sich mit Ihrem Azure AD-Konto, wenn Sie dazu aufgefordert werden.
1. Wählen Sie auf der Seite __Willkommen bei Studio!__ den __Machine Learning-Arbeitsbereich__ aus, den Sie zuvor erstellt haben. Wählen Sie anschließend __Erste Schritte__ aus.

    > [!TIP]
    > Wenn Ihr Azure AD-Konto Zugriff auf mehrere Abonnements oder Verzeichnisse hat, wählen Sie in der Dropdownliste __Verzeichnis und Abonnement__ dasjenige aus, das den Arbeitsbereich enthält.

    :::image type="content" source="./media/tutorial-create-secure-workspace/studio-select-workspace.png" alt-text="Screenshot: Dialogfeld zur Auswahl des Arbeitsbereichs":::

1. Wählen Sie in Studio die Optionen __Compute__, __Computecluster__ und anschließend __+ Neu__ aus.

    :::image type="content" source="./media/tutorial-create-secure-workspace/studio-new-compute-cluster.png" alt-text="Screenshot: Workflow für neuen Computercluster":::

1. Wählen Sie im Dialogfeld __Virtueller Computer__ die Option __Weiter__ aus, um die Standardkonfiguration des virtuellen Computers zu akzeptieren.

    :::image type="content" source="./media/tutorial-create-secure-workspace/studio-new-compute-vm.png" alt-text="Screenshot: VM-Einstellungen für Computecluster":::
    
1. Geben Sie im Dialogfeld __Einstellungen konfigurieren__ den Eintrag __cpu-cluster__ als __Computenamen__ ein. Legen Sie das __Subnetz__ auf __Training__ fest, und wählen Sie dann __Erstellen__ aus, um den Cluster zu erstellen.

    > [!TIP]
    > Von Computeclustern werden die Knoten im Cluster nach Bedarf dynamisch skaliert. Es wird empfohlen, die Mindestanzahl von Knoten bei 0 zu belassen, um die Kosten zu senken, wenn der Cluster nicht verwendet wird.

    :::image type="content" source="./media/tutorial-create-secure-workspace/studio-new-compute-settings.png" alt-text="Screenshot: Einstellungen neuer Computecluster":::

1. Wählen Sie in Studio die Optionen __Compute__, __Compute-Instanz__ und dann __+ Neu__ aus.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-compute-instance.png" alt-text="Screenshot: Workflow für neue Compute-Instanz":::

1. Geben Sie im Dialogfeld __Virtueller Computer__ einen eindeutigen Wert für __Computername__ ein, und wählen Sie __Weiter: Erweitert Einstellungen__ aus.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-compute-instance-vm.png" alt-text="Screenshot: VM-Einstellungen der Compute-Instanz":::

1. Legen Sie im Dialogfeld __Erweiterte Einstellungen__ das __Subnetz__ auf __Training__ fest, und wählen Sie dann __Erstellen__ aus.

    :::image type="content" source="./media/tutorial-create-secure-workspace/create-compute-instance-settings.png" alt-text="Screenshot: Einstellungen der Compute-Instanz":::

> [!TIP]
> Wenn Sie einen Computecluster oder eine Compute-Instanz erstellen, wird von Azure Machine Learning eine Netzwerksicherheitsgruppe (NSG) dynamisch hinzugefügt. Diese NSG enthält die folgenden Regeln, die spezifisch für Computecluster und Compute-Instanzen sind:
> 
> * Zulassen des eingehenden TCP-Datenverkehrs an den Ports 29876-29877 vom Diensttag `BatchNodeManagement`.
> * Zulassen des eingehenden TCP-Datenverkehrs am Port 44224 vom Diensttag `AzureMachineLearning`.
>
> Im folgenden Screenshot sehen Sie ein Beispiel für diese Regeln:
>
> :::image type="content" source="./media/how-to-secure-training-vnet/compute-instance-cluster-network-security-group.png" alt-text="Screenshot: NSG":::

Weitere Informationen zum Erstellen von Computeclustern und Compute-Instanzen, einschließlich der diesbezüglichen Verwendung von Python und der CLI, finden Sie in den folgenden Artikeln:

* [Erstellen eines Computeclusters](how-to-create-attach-compute-cluster.md)
* [Erstellen einer Compute-Instanz](how-to-create-manage-compute-instance.md)

## <a name="configure-image-builds"></a>Konfigurieren der Imageerstellung

Wenn sich Azure Container Registry hinter dem virtuellen Netzwerk befindet, kann es von Azure Machine Learning nicht dazu verwendet werden, (für Training und Bereitstellung verwendete) Docker-Images direkt zu erstellen. Konfigurieren Sie stattdessen den Arbeitsbereich für die Verwendung des Computeclusters, den Sie zuvor erstellt haben. Führen Sie die folgenden Schritte aus, um einen Computecluster zu erstellen und den Arbeitsbereich so zu konfigurieren, dass er zum Erstellen von Images verwendet wird:

1. Navigieren Sie zu [https://shell.azure.com/](https://shell.azure.com/), um Azure Cloud Shell zu öffnen.
1. Verwenden Sie in Cloud Shell den folgenden Befehl, um die CLI 1.0 für Azure Machine Learning zu installieren:

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. So aktualisieren Sie den Arbeitsbereich, um den Computecluster zum Erstellen von Docker-Images zu verwenden. Ersetzen Sie `docs-ml-rg` durch die Ressourcengruppe. Ersetzen Sie `docs-ml-ws` durch den Arbeitsbereich. Ersetzen Sie `cpu-cluster` durch den zu verwendenden Computecluster.

    ```azurecli-interactive
    az ml workspace update -g docs-ml-rg -w docs-ml-ws --image-build-compute cpu-cluster
    ```

    > [!NOTE]
    > Sie können denselben Computecluster verwenden, um Modelle zu trainieren und Docker-Images für den Arbeitsbereich zu erstellen.

## <a name="use-the-workspace"></a>Verwenden des Arbeitsbereichs

An diesem Punkt können Sie Studio verwenden, um interaktiv mit Notebooks in der Compute-Instanz zu arbeiten und Trainingsaufträge im Computecluster ausführen zu können. Ein Tutorial zur Verwendung von Compute-Instanzen und Computeclustern finden Sie unter [Ausführen von Python-Skripts](tutorial-1st-experiment-hello-world.md).

## <a name="stop-compute-instance-and-jump-box"></a>Beenden von Compute-Instanz und Jumpbox

> [!WARNING]
> Während der Ausführung (nach dem Start) fallen durch Compute-Instanz und Jumpbox weiterhin Kosten für Ihr Abonnement an. __Beenden__ Sie zur Vermeidung übermäßiger Kosten Compute-Instanz und Jumpbox, wenn diese nicht verwendet werden.

Der Computecluster wird dynamisch zwischen der minimalen und maximalen Knotenanzahl skaliert, die Sie beim Erstellen festgelegt haben. Wenn Sie die Standardwerte akzeptiert haben, ist der Mindestwert 0, wodurch der Cluster praktisch deaktiviert wird, wenn er nicht verwendet wird.
### <a name="stop-the-compute-instance"></a>Beenden der Compute-Instanz

Wählen Sie in Studio die Optionen __Compute__, __Computecluster__ und anschließend die Compute-Instanz aus. Wählen Sie abschließend oben auf der Seite die Option __Beenden__ aus.

:::image type="content" source="./media/tutorial-create-secure-workspace/compute-instance-stop.png" alt-text="Screenshot: Schaltfläche „Beenden“ für die Compute-Instanz":::
### <a name="stop-the-jump-box"></a>Beenden der Jumpbox

Wählen Sie nach dem Erstellen des virtuellen Computers den virtuellen Computer im Azure-Portal aus, und verwenden Sie dann die Schaltfläche __Beenden__. Wenn Sie den virtuellen Computer wieder verwenden möchten, klicken Sie auf die Schaltfläche __Starten__, um ihn erneut zu starten.

:::image type="content" source="./media/tutorial-create-secure-workspace/virtual-machine-stop.png" alt-text="Screenshot: Schaltfläche „Beenden“ für den virtuellen Computer":::

Sie können die Jumpbox auch so konfigurieren, dass sie zu einem bestimmten Zeitpunkt automatisch heruntergefahren wird. Wählen Sie dazu __Automatisch herunterfahren__ aus, __aktivieren__ Sie die Funktion, legen Sie eine Zeit fest, und wählen Sie __Speichern__ aus.

:::image type="content" source="./media/tutorial-create-secure-workspace/virtual-machine-auto-shutdown.png" alt-text="Screenshot: Option für automatisches Herunterfahren":::

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie den geschützten Arbeitsbereich und andere Ressourcen weiterhin verwenden möchten, überspringen Sie diesen Abschnitt.

Führen Sie die folgenden Schritte aus, um alle in diesem Tutorial erstellten Ressourcen zu löschen:

1. Wählen Sie ganz links im Azure-Portal __Ressourcengruppen__ aus.
1. Wählen Sie in der Liste die Ressourcengruppe aus, die Sie in diesem Tutorial erstellt haben.
1. Klicken Sie auf __Ressourcengruppe löschen__.

    :::image type="content" source="./media/tutorial-create-secure-workspace/delete-resources.png" alt-text="Screenshot: Schaltfläche „Ressourcengruppe löschen“":::

1. Geben Sie den Ressourcengruppennamen ein, und wählen Sie die Option __Löschen__ aus.
## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie einen sicheren Arbeitsbereich erstellt haben und auf Studio zugreifen können, können Sie sich jetzt damit vertraut machen, wie Sie ein [Python-Skript mithilfe von Azure Machine Learning ausführen](tutorial-1st-experiment-hello-world.md).
