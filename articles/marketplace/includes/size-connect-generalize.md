---
title: include file
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: 2c0ce50c1aac28b532d23ecd1203ff76f10529d4
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130077825"
---
## <a name="generalize-the-image"></a>Generalisieren des Images

Alle Images im Azure Marketplace müssen allgemein wiederverwendbar sein. Um dies zu erreichen, muss die Betriebssystem-VHD generalisiert werden. Dies ist ein Vorgang, bei dem alle instanzspezifischen Bezeichner und Softwaretreiber von einer VM entfernt werden.

### <a name="for-windows"></a>Für Windows

Windows-Betriebssystemdatenträger werden mit dem [Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview)-Tool generalisiert. Wenn Sie das Betriebssystem später aktualisieren oder neu konfigurieren, müssen Sie Sysprep erneut ausführen.

> [!WARNING]
> Fahren Sie die VM nach der Ausführung von Sysprep bis zur Bereitstellung herunter, da u. U. automatische Updates ausgeführt werden. Dadurch verhindern Sie, dass durch nachfolgende Updates instanzspezifische Änderungen am Betriebssystem oder den installierten Diensten vorgenommen werden. Weitere Informationen zum Ausführen von Sysprep finden Sie unter [Generalisieren einer Windows-VM](../../virtual-machines/generalize.md#windows).

### <a name="for-linux"></a>Für Linux

1. Entfernen Sie den Azure Linux-Agent.
    1. Stellen Sie mit einem SSH-Client eine Verbindung mit Ihrer Linux-VM her.
    2. Geben Sie im SSH-Fenster den Befehl `sudo waagent –deprovision+user` ein.
    3. Geben Sie Y ein, um fortzufahren (Sie können dem vorherigen Befehl den Parameter -force hinzufügen, um diesen Bestätigungsschritt zu umgehen).
    4. Geben Sie nach der Ausführung des Befehls **Exit** ein, um den SSH-Client zu schließen.
2. Beenden Sie die VM.
    1. Wählen Sie im Azure-Portal Ihre Ressourcengruppe (RG) aus, und heben Sie die Zuordnung der VM auf.
    2. Ihre VM ist jetzt generalisiert, und Sie können mit diesem VM-Datenträger eine neue VM erstellen.

### <a name="capture-image"></a>Erfassen des Images

> [!NOTE]
> Zum Veröffentlichen muss sich das Azure-Abonnement, das Shared Image Gallery enthält, unter demselben Mandanten wie das Herausgeberkonto befinden. Darüber hinaus muss das Herausgeberkonto mindestens über Zugriff als Mitwirkender auf das Abonnement verfügen, das SIG enthält.

Sobald Ihre VM bereit ist, können Sie sie in Azure Shared Image Gallery erfassen. Führen Sie hierzu die folgenden Schritte aus:

1. Navigieren Sie im [Azure-Portal](https://ms.portal.azure.com/) zur Seite Ihres virtuellen Computers.
2. Wählen Sie die Option **Erfassen** aus.
3. Klicken Sie unter **Image in Shared Image Gallery freigeben** auf **Ja, als Imageversion für einen Katalog freigeben**.
4. Wählen Sie unter **Betriebssystemstatus** die Option „Generalized“ (Generalisiert) aus.
5. Wählen Sie ein Image Gallery-Ziel aus, oder klicken Sie auf **Neu erstellen**.
6. Wählen Sie eine Zielimagedefinition aus, oder klicken Sie auf **Neu erstellen**.
7. Geben Sie eine **Versionsnummer** für das Image ein.
8. Wählen Sie **Bewerten + erstellen** aus, um Ihre Auswahl zu überprüfen.
9. Klicken Sie auf **Erstellen**, sobald die Überprüfung bestanden wurde.

## <a name="set-the-right-permissions"></a>Festlegen der richtigen Berechtigungen

Wenn Ihr Partner Center-Konto der Besitzer des Abonnements ist, das Shared Image Gallery hostet, ist für Berechtigungen nichts weiter erforderlich.

Wenn Sie nur Lesezugriff auf das Abonnement haben, verwenden Sie eine der beiden folgenden Optionen.

### <a name="option-one--ask-the-owner-to-grant-owner-permission"></a>Option 1: Besitzer bitten, die Besitzerberechtigung zu erteilen

Schritte zum Erteilen der Besitzerberechtigung durch den Besitzer:

1. Wechseln Sie zur Shared Image Gallery-Instanz (SIG).
2. Wählen Sie im linken Bereich **Zugriffssteuerung (IAM)** aus.
3. Wählen Sie **Hinzufügen** und dann **Rollenzuweisung hinzufügen** aus.<br>
    :::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="Screenshot des Fensters „Rollenzuweisung hinzufügen“":::
1. Wählen Sie für **Rolle** die Option **Besitzer** aus.
1. Wählen Sie für **Zugriff zuweisen zu** die Option **Benutzer, Gruppe oder Dienstprinzipal** aus.
1. Geben Sie die unter **Auswählen** die Azure-E-Mail-Adresse der Person ein, die das Image veröffentlichen wird.
1. Wählen Sie **Speichern** aus.

### <a name="option-two--run-a-command"></a>Option 2: Befehl ausführen

Bitten Sie den Besitzer, einen dieser Befehle auszuführen (verwenden Sie in beiden Fällen die SusbscriptionId des Abonnements, in dem Sie die Shared Image Gallery erstellt haben).

```azurecli
az login
az provider register --namespace Microsoft.PartnerCenterIngestion --subscription {subscriptionId}
```
 
```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId {subscriptionId}
Register-AzResourceProvider -ProviderNamespace Microsoft.PartnerCenterIngestion
```

> [!NOTE]
> Sie müssen keinen URI (Uniform Resource Identifier) generieren, da Sie nun ein Shared Image Gallery-Image in Partner Center veröffentlichen können. Wenn Sie dennoch Informationen zu den Schritten zum Erstellen eines SAS-URIs benötigen, finden Sie diese unter [Generieren eines SAS-URIs für ein VM-Image](../azure-vm-get-sas-uri.md).
