---
title: 'Azure Virtual Desktop-Hostpool im Azure-Portal: Azure'
description: Erfahren Sie, wie Sie einen Azure Virtual Desktop-Hostpool über das Azure-Portal erstellen.
author: Heidilohr
ms.topic: tutorial
ms.custom: references_regions
ms.date: 08/06/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 3b95302be3eda412f6941abe359f6da63e235d24
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132492036"
---
# <a name="tutorial-create-a-host-pool"></a>Tutorial: Erstellen eines Hostpools

>[!IMPORTANT]
>Dieser Inhalt gilt für Azure Virtual Desktop mit Azure Virtual Desktop-Objekten für Azure Resource Manager. Wenn Sie Azure Virtual Desktop (klassisch) ohne Azure Resource Manager-Objekte verwenden, finden Sie weitere Informationen in [diesem Artikel](./virtual-desktop-fall-2019/create-host-pools-azure-marketplace-2019.md). Objekte, die Sie mit Azure Virtual Desktop (klassisch) erstellen, können nicht mit dem Azure-Portal verwaltet werden.

Ein Hostpool ist eine Sammlung identischer VMs, auch als „Sitzungshosts“ bezeichnet, innerhalb von Azure Virtual Desktop-Umgebungen. Jeder Hostpool kann eine App-Gruppe enthalten, mit der Benutzer genau wie auf einem physischen Desktop interagieren können. Weitere Informationen zur Bereitstellungsarchitektur finden Sie unter [Azure Virtual Desktop-Umgebung](environment-setup.md). Wenn Sie ein App-Entwickler sind, der das Remote-App-Streaming für Azure Virtual Desktop verwendet, können Ihre Kunden oder Benutzer Ihre Apps wie lokale Apps auf einem physischen Gerät verwenden. Weitere Informationen zur Verwendung von Azure Virtual Desktop als App-Entwickler finden Sie in unserer Dokumentation zum [Streaming von Azure Virtual Desktop-Remote-Apps](./remote-app-streaming/custom-apps.md).

>[!NOTE]
>Wenn Sie ein App-Entwickler sind, der das Remote-App-Streaming für Azure Virtual Desktop verwendet und sich die Benutzer Ihrer App in derselben Organisation wie Ihre Bereitstellung befinden, können Sie Ihren vorhandenen Azure-Mandanten verwenden, um Ihren Hostpool zu erstellen. Falls sich Ihre Benutzer außerhalb Ihrer Organisation befinden, müssen Sie aus Sicherheitsgründen separate Azure-Mandanten mit mindestens einem Hostpool für jede Organisation erstellen. Weitere Informationen zu den empfohlenen Vorgehensweisen, um die Sicherheit Ihrer Bereitstellung zu gewährleisten, finden Sie in den [Architekturempfehlungen](./remote-app-streaming/architecture-recs.md).

In diesem Artikel wird der Einrichtungsprozess zum Erstellen eines Hostpools für eine Azure Virtual Desktop-Umgebung über das Azure-Portal detailliert beschrieben. Bei dieser Methode verwenden Sie eine browserbasierte Benutzeroberfläche, um einen Hostpool in Azure Virtual Desktop zu erstellen, eine Ressourcengruppe mit VMs in einem Azure-Abonnement zu erstellen und diese VMs dann entweder in die Active Directory-Domäne (AD) oder den Azure AD-Mandanten (Azure Active Directory) einzubinden und bei Azure Virtual Desktop zu registrieren.

## <a name="prerequisites"></a>Voraussetzungen

Die Anforderungen unterscheiden sich je nachdem, ob Sie ein IT-Experte sind, der eine Bereitstellung für Ihre Organisation einrichtet, oder ein App-Entwickler, der Anwendungen für Kunden betreut.

### <a name="requirements-for-it-professionals"></a>Anforderungen für IT-Experten

Zum Erstellen eines Hostpools müssen Sie die folgenden Parameter eingeben:

- Name des VM-Images
- Konfiguration des virtuellen Computers
- Domänen- und Netzwerkeigenschaften
- Eigenschaften des Azure Virtual Desktop-Hostpools

Außerdem müssen Sie Folgendes wissen:

- Wo befindet sich die Quelle des Images, das Sie verwenden möchten? Stammt es aus dem Azure-Katalog, oder handelt es sich um ein benutzerdefiniertes Image?
- Wo befinden sich Ihre Anmeldeinformationen für den Domänenbeitritt?

### <a name="requirements-for-app-developers"></a>Anforderungen für App-Entwickler

Wenn Sie ein App-Entwickler sind, der das Remote-App-Streaming für Azure Virtual Desktop verwendet, um Apps für Kunden bereitzustellen, benötigen Sie Folgendes:

- Wenn Sie planen, die App Ihrer Organisation Endbenutzern zur Verfügung zu stellen, müssen Sie sicherstellen, dass die App einsatzbereit ist. Weitere Informationen finden Sie unter [Hosten benutzerdefinierter Apps mit Azure Virtual Desktop](./remote-app-streaming/custom-apps.md).
- Wenn die vorhandenen Imageauswahlmöglichkeiten im Azure-Katalog Ihre Anforderungen nicht erfüllen, müssen Sie auch Ihr eigenes benutzerdefiniertes Image für Ihre Sitzungshost-VMs erstellen. Weitere Informationen zur Erstellung von VM-Images finden Sie unter [Vorbereiten einer Windows-VHD oder -VHDX zum Hochladen in Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) bzw. unter [Erstellen eines verwalteten Images eines generalisierten virtuellen Computers in Azure](../virtual-machines/windows/capture-image-resource.md).
- Wo befinden sich Ihre Anmeldeinformationen für den Domänenbeitritt? Wenn Sie noch nicht über ein Identitätsverwaltungssystem verfügen, das mit Azure Virtual Desktop kompatibel ist, müssen Sie die Identitätsverwaltung für Ihren Hostpool einrichten. Weitere Informationen finden Sie unter [Einrichten verwalteter Identitäten](./remote-app-streaming/identities.md).

### <a name="final-requirements"></a>Abschließende Anforderungen

Stellen Sie abschließend sicher, dass Sie den Ressourcenanbieter „Microsoft.DesktopVirtualization“ registriert haben. Gehen Sie wie folgt vor, falls Sie dies noch nicht getan haben: Navigieren Sie zu **Abonnements**, und wählen Sie den Namen Ihres Abonnements und dann **Ressourcenanbieter** aus. Suchen Sie nach **DesktopVirtualization**, und wählen Sie **Microsoft.DesktopVirtualization** und dann **Registrieren** aus.

Wenn Sie als IT-Experte ein Netzwerk einrichten und im Rahmen dessen einen Azure Virtual Desktop-Hostpool mit der Azure Resource Manager-Vorlage erstellen, können Sie eine VM über den Azure-Katalog, ein verwaltetes Image oder ein nicht verwaltetes Image erstellen. Weitere Informationen zur Erstellung von VM-Images finden Sie unter [Vorbereiten einer Windows-VHD oder -VHDX zum Hochladen in Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) bzw. unter [Erstellen eines verwalteten Images eines generalisierten virtuellen Computers in Azure](../virtual-machines/windows/capture-image-resource.md). (Wenn Sie App-Entwickler sind, müssen Sie sich über diesen Teil keine Gedanken machen.)

Schließlich müssen Sie, wenn Sie noch kein Azure-Abonnement haben, [ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), bevor Sie die folgenden Anweisungen ausführen können.

## <a name="begin-the-host-pool-setup-process"></a>Erste Schritte des Einrichtungsprozesses für den Hostpool

### <a name="portal"></a>[Portal](#tab/azure-portal)

Zum Erstellen des neuen Hostpools führen Sie zunächst die folgenden Schritte aus:

1. Melden Sie sich unter [https://portal.azure.com](https://portal.azure.com/) beim Azure-Portal an.
   
   >[!NOTE]
   > Wenn Sie sich beim US Gov-Portal anmelden, wechseln Sie stattdessen zu [https://portal.azure.us/](https://portal.azure.us/).
   > 
   >Wenn Sie auf das Portal für „Azure China“ zugreifen, wechseln Sie zu [https://portal.azure.cn/](https://portal.azure.cn/).

2. Geben Sie **Azure Virtual Desktop** in die Suchleiste ein, und wählen Sie dann unter „Dienste“ den Eintrag **Azure Virtual Desktop** aus.

3. Wählen Sie auf der Übersichtsseite für **Azure Virtual Desktop** die Option **Hostpool erstellen** aus.

4. Wählen Sie auf der Registerkarte **Grundlagen** unter „Projektdetails“ das richtige Abonnement aus.

5. Klicken Sie entweder auf **Neu erstellen**, um eine neue Ressourcengruppe zu erstellen, oder wählen Sie im Dropdownmenü eine vorhandene Ressourcengruppe aus.

6. Geben Sie einen eindeutigen Namen für Ihren Hostpool ein.

7. Wählen Sie im Feld „Standort“ im Dropdownmenü die Region aus, in der Sie den Hostpool erstellen möchten.

   Die Metadaten für diesen Hostpool und die zugehörigen Objekte werden in der Azure-Geografie gespeichert, die den von Ihnen ausgewählten Regionen zugeordnet ist. Stellen Sie sicher, dass Sie die Regionen innerhalb der Geografie auswählen, in der die Dienstmetadaten gespeichert werden sollen.

     > [!div class="mx-imgBorder"]
     > ![Screenshot des Azure-Portals, der das Feld „Standort“ mit ausgewählter Region „USA, Osten“ zeigt. Neben dem Feld wird „Metadaten werden in ‚USA, Osten‘ gespeichert“ angezeigt.](media/portal-location-field.png)
  
   >[!NOTE]
   > Wenn Sie Ihren Hostpool in [einer unterstützten Region](data-locations.md) außerhalb der USA erstellen möchten, müssen Sie den Ressourcenanbieter erneut registrieren. Nach der erneuten Registrierung sollten die anderen Regionen in der Dropdownliste zum Auswählen des Standorts angezeigt werden. Informationen zum erneuten Registrieren finden Sie im Problembehandlungsartikel zur [Hostpoolerstellung](troubleshoot-set-up-issues.md#i-only-see-us-when-setting-the-location-for-my-service-objects).

8. Wählen Sie unter „Host pool type“ (Hostpooltyp) die Option **Persönlich** oder **In Pool** für Ihren Hostpool aus.

    - Wählen Sie bei Auswahl von **Persönlich** im Feld „Zuweisungstyp“ die Option **Automatisch** oder **Direkt** aus.

      > [!div class="mx-imgBorder"]
      > ![Screenshot des Dropdownmenüs „Zuweisungstyp“. Der Benutzer hat „Automatisch“ ausgewählt.](media/assignment-type-field.png)

9.  Geben Sie bei Auswahl von **In Pool** die folgenden Informationen ein:

     - Geben Sie für **Maximale Anzahl von Sitzungen** die Höchstanzahl von Benutzern ein, für die ein Lastenausgleich auf einem einzelnen Sitzungshost durchgeführt werden soll.
     - Wählen Sie für **Lastenausgleichsalgorithmus** je nach Nutzungsmuster „Breitenorientierter Lastenausgleich“ oder „Tiefenorientierter Lastenausgleich“ aus. Weitere Informationen zu den einzelnen Optionen finden Sie unter [Lastenausgleichsmethoden für Hostpools](host-pool-load-balancing.md).

       > [!div class="mx-imgBorder"]
       > ![Screenshot des Dropdownmenüs „Zuweisungstyp“ mit ausgewählter Option „In Pool“. Der Benutzer zeigt mit dem Cursor auf die Option „Breitenorientierter Lastenausgleich“ im Dropdownmenü „Lastenausgleichsalgorithmus“.](media/pooled-assignment-type.png)

10. Klicken Sie auf **Weiter: Virtuelle Computer >** .

11. Wenn Sie bereits virtuelle Computer erstellt haben und diese mit dem neuen Hostpool verwenden möchten, wählen Sie **Nein** und dann **Weiter: Arbeitsbereich >** aus, und wechseln Sie zum Abschnitt [Arbeitsbereichsinformationen](#workspace-information). Möchten Sie neue VMs erstellen und beim neuen Hostpool registrieren, wählen Sie **Ja** aus.

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Bereiten Sie zunächst Ihre Umgebung für die Azure-Befehlszeilenschnittstelle vor:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Verwenden Sie nach der Anmeldung den Befehl [az desktopvirtualization hostpool create](/cli/azure/desktopvirtualization#az_desktopvirtualization_hostpool_create), um den neuen Hostpool zu erstellen, und erstellen Sie optional ein Registrierungstoken für Sitzungshosts, um dem Hostpool beizutreten:

```azurecli
az desktopvirtualization hostpool create --name "MyHostPool" \
    --resource-group "MyResourceGroup" \ 
    --location "MyLocation" \
    --host-pool-type "Pooled" \
    --load-balancer-type "BreadthFirst" \
    --max-session-limit 999 \
    --personal-desktop-assignment-type "Automatic"  \
    --registration-info expiration-time="2022-03-22T14:01:54.9571247Z" registration-token-operation="Update" \
    --sso-context "KeyVaultPath" \
    --description "Description of this host pool" \
    --friendly-name "Friendly name of this host pool" \
    --tags tag1="value1" tag2="value2" 
```

Möchten Sie neue VMs erstellen und beim neuen Hostpool registrieren, fahren Sie mit dem folgenden Abschnitt fort. Wenn Sie bereits virtuelle Computer erstellt haben und diese mit dem neuen Hostpool verwenden möchten, springen Sie zum Abschnitt [Informationen zum Arbeitsbereich](#workspace-information). 

---

Nachdem Sie jetzt einen Hostpool erstellt haben, gehen wir zum nächsten Teil des Einrichtungsprozesses über, bei dem wir die VM erstellen.

## <a name="virtual-machine-details"></a>Details zum virtuellen Computer

Nachdem Sie den ersten Teil abgeschlossen haben, müssen Sie nun Ihre VM einrichten.

### <a name="portal"></a>[Portal](#tab/azure-portal)

So richten Sie Ihre VM im Rahmen des Einrichtungsprozesses für den Hostpool im Azure-Portal ein:

1. Wählen Sie unter **Ressourcengruppe** die Ressourcengruppe aus, in der Sie die virtuellen Computer erstellen möchten. Dabei kann es sich um eine andere Ressourcengruppe als die für den Hostpool handeln.

2. Geben Sie anschließend ein **Namenspräfix** für die Benennung der VMs an, die beim Einrichtungsprozess erstellt werden. Das Suffix ist `-` mit Zahlen ab 0.

3. Wählen Sie den **VM-Standort** aus, in dem Sie die virtuellen Computer erstellen möchten. Sie können die für den Hostpool ausgewählte Region oder eine andere Region verwenden. Beachten Sie, dass die VM-Preise je nach Region variieren und sich die VM-Standorte nach Möglichkeit in der Nähe Ihrer Benutzer befinden sollten, um die Leistung zu maximieren. Weitere Informationen finden Sie unter [Datenstandorte für Azure Virtual Desktop](data-locations.md).
   
4. Wählen Sie als Nächstes die Verfügbarkeitsoption aus, die Ihren Anforderungen am ehesten entspricht. Weitere Informationen dazu, welche Option für Sie geeignet ist, finden Sie unter [Verfügbarkeitsoptionen für virtuelle Computer in Azure](../virtual-machines/availability.md) und in den [häufig gestellten Fragen](./faq.yml#which-availability-option-is-best-for-me-).
   
   > [!div class="mx-imgBorder"]
   > ![Screenshot: Dropdownmenü für Verfügbarkeitszonen. Die Option „Verfügbarkeitszone“ ist hervorgehoben.](media/availability-zone.png)

5. Wählen Sie nun das Image aus, das zum Erstellen der VM verwendet werden muss. Sie können **Katalog** oder **Speicherblob** auswählen.

    - Wählen Sie bei Auswahl von **Katalog** im Dropdownmenü eines der empfohlenen Images aus:

      - Windows 10 Enterprise (mehrere Sitzungen), Version 1909
      - Windows 10 Enterprise (mehrere Sitzungen), Version 1909 + Microsoft 365 Apps
      - Windows Server 2019 Datacenter
      - Windows 10 Enterprise (mehrere Sitzungen), Version 2004
      - Windows 10 Enterprise (mehrere Sitzungen), Version 2004 + Microsoft 365 Apps

      Falls das gewünschte Image nicht angezeigt wird, wählen Sie **Alle Images anzeigen** aus. Sie können dann ein anderes Image in Ihrem Katalog oder ein von Microsoft und anderen Herausgebern bereitgestelltes Image auswählen. Wählen Sie unbedingt eines der [unterstützten Betriebssystemimages](overview.md#supported-virtual-machine-os-images) aus.

      > [!div class="mx-imgBorder"]
      > ![Screenshot des Azure-Portals mit einer Liste von Images von Microsoft](media/marketplace-images.png)

      Sie können auch zu **Meine Elemente** wechseln und ein benutzerdefiniertes Image auswählen, das Sie bereits hochgeladen haben.

      > [!div class="mx-imgBorder"]
      > ![Screenshot der Registerkarte „Meine Elemente“.](media/my-items.png)

    - Bei Auswahl von **Speicherblob** können Sie Ihren eigenen Imagebuild über Hyper-V oder auf einer Azure-VM nutzen. Sie müssen lediglich im Speicherblob den Speicherort des Images als URI eingeben.
   
   Der Speicherort des Images ist unabhängig von der Verfügbarkeitsoption. Die Zonenresilienz des Images bestimmt jedoch, ob das Image mit einer Verfügbarkeitszone verwendet werden kann. Wenn Sie beim Erstellen des Images eine Verfügbarkeitszone auswählen, stellen Sie sicher, dass Sie ein Image aus dem Katalog mit aktivierter Zonenresilienz verwenden. Weitere Informationen dazu, welche Zonenresilienzoption Sie verwenden sollten, finden Sie in den [häufig gestellten Fragen](./faq.yml#which-availability-option-is-best-for-me-).

6. Wählen Sie anschließend die **VM-Größe** aus. Sie können entweder die Standardgröße übernehmen oder **Größe ändern** auswählen, um die Größe zu ändern. Wenn Sie auf **Größe ändern** klicken, wählen Sie im angezeigten Fenster die passende VM-Größe für Ihre Workload aus. Weitere Informationen und Empfehlungen zu VM-Größen finden Sie unter [Richtlinien für VM-Größen](/windows-server/remote/remote-desktop-services/virtual-machine-recs?context=/azure/virtual-desktop/context/context).

7. Geben Sie unter **Anzahl von VMs** die Anzahl von virtuellen Computern an, die Sie für Ihren Hostpool erstellen möchten.

    >[!NOTE]
    >Sie können während der Einrichtung Ihres Hostpools bis zu 400 VMs erstellen, und bei jedem VM-Einrichtungsprozess werden vier Objekte in Ihrer Ressourcengruppe erstellt. Ihr Abonnementkontingent wird bei der Erstellung nicht überprüft. Achten Sie daher darauf, dass die von Ihnen angegebene Anzahl virtueller Computer den Azure-VM- und API-Grenzwerten für Ihre Ressourcengruppe und Ihr Abonnement entspricht. Sie können nach der Erstellung des Hostpools weitere VMs hinzufügen.

8. Wählen Sie den gewünschten Typ für die Betriebssystemdatenträger Ihrer VMs aus: „SSD Standard“, „SSD Premium“ oder „HDD Standard“.

9. Wählen Sie unter „Netzwerk und Sicherheit“ das **virtuelle Netzwerk** und das **Subnetz** aus, in dem Sie die von Ihnen erstellten virtuellen Computer platzieren möchten. Stellen Sie sicher, dass das virtuelle Netzwerk eine Verbindung mit dem Domänencontroller herstellen kann, da Sie die VMs im virtuellen Netzwerk der Domäne hinzufügen müssen. Die DNS-Server des virtuellen Netzwerks, das Sie ausgewählt haben, sollten für die Verwendung der IP-Adresse des Domänencontrollers konfiguriert werden.

10. Wählen Sie den gewünschten Sicherheitsgruppentyp aus: **Basic**, **Erweitert** oder **Keine**.

    Bei Auswahl von **Basic** müssen Sie auswählen, ob ein Eingangsport geöffnet sein soll. Wählen Sie bei Auswahl von **Ja** in der Liste der Standardports einen Port aus, um eingehende Verbindungen zuzulassen.

    >[!NOTE]
    >Aus Sicherheitsgründen empfehlen wir, öffentliche Eingangsports nicht zu öffnen.

    > [!div class="mx-imgBorder"]
    > ![Screenshot der Seite „Sicherheitsgruppe“ mit einer Liste verfügbarer Ports in einem Dropdownmenü.](media/available-ports.png)

    Wählen Sie bei Auswahl von **Erweitert** eine vorhandene Netzwerksicherheitsgruppe aus, die Sie bereits konfiguriert haben.

11. Wählen Sie anschließend aus, ob die VMs in **Active Directory** oder **Azure Active Directory** (Vorschau) eingebunden werden sollen.

    - Geben Sie für Active Directory ein Konto für den Beitritt zur Domäne an, und wählen Sie aus, ob die Einbindung in einer bestimmten Domäne und Organisationseinheit erfolgen soll.

        - Geben Sie beim UPN für den AD-Domänenbeitritt die Anmeldeinformationen für den Active Directory-Domänenadministrator des ausgewählten virtuellen Netzwerks an. Für das verwendete Konto darf die mehrstufige Authentifizierung (Multifactor Authentication, MFA) nicht aktiviert sein. Bei der Einbindung in eine Azure Active Directory Domain Services-Domäne (Azure AD DS) muss das verwendete Konto Teil der Gruppe „Azure AD DC-Administratoren“ sein, und das Kontokennwort muss in Azure AD DS gültig sein.

        - Um eine Domäne anzugeben, wählen Sie **Ja** aus, und geben Sie dann den Namen der Domäne für den Beitritt ein. Bei Bedarf können Sie auch eine bestimmte Organisationseinheit hinzufügen, in der sich die VMs befinden sollen. Dazu geben Sie den vollständigen Pfad (Distinguished Name) ohne Anführungszeichen ein. Wenn Sie keine Domäne angeben möchten, wählen Sie **Nein** aus. Die VMs treten automatisch der Domäne bei, die dem Suffix des **UPN für den AD-Domänenbeitritt** entspricht.
  
    - Für Azure Active Directory können Sie **VM bei Intune registrieren** auswählen, um die VM nach der Bereitstellung automatisch für die Verwaltung verfügbar zu machen.

12. Geben Sie unter **VM-Administratorkonto** die Anmeldeinformationen des lokalen Administratorkontos ein, das beim Erstellen der VM hinzugefügt werden soll. Sie können dieses Konto zu Verwaltungszwecken sowohl für in AD als auch für in Azure AD eingebundene VMs verwenden.

13. Unter **Post update custom configuration** (Benutzerdefinierte Konfiguration nach Update) können Sie den Speicherort einer Azure Resource Manager-Vorlage eingeben, um nach dem Erstellen benutzerdefinierte Konfigurationen für Ihre Sitzungshosts vorzunehmen. Sie müssen die URLs für die Azure Resource Manager-Vorlagendatei und die Azure Resource Manager-Vorlagenparameterdatei eingeben. 

      >[!NOTE]
      >Azure Virtual Desktop unterstützt die Bereitstellung von Azure-Ressourcen in der Vorlage nicht.

14. Klicken Sie auf **Weiter: Arbeitsbereich >** .

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az vm create](/cli/azure/vm#az_vm_create), um einen neuen virtuellen Computer in Azure zu erstellen:

```azurecli
az vm create --name "MyVMName" \
    --resource-group "MyResourceGroup" \
    --image "MyImage" \
    --generate-ssh-keys
```

Weitere Informationen zur Verwendung der Azure CLI zum Erstellen virtueller Azure-Computer finden Sie unter:
- Windows
    - [Erstellen eines virtuellen Windows-Computers mit der Azure-Befehlszeilenschnittstelle]( /azure/virtual-machines/windows/quick-create-cli)
    - [Tutorial: Erstellen und Verwalten virtueller Windows-Computer mit der Azure CLI](/cli/azure/azure-cli-vm-tutorial)
- Linux
    - [Erstellen einer Linux-VM von Grund auf mit der Azure-Befehlszeilenschnittstelle](../virtual-machines/linux/quick-create-cli.md)
    - [Tutorial: Erstellen und Verwalten virtueller Linux-Computer mit der Azure-Befehlszeilenschnittstelle]( /azure/virtual-machines/linux/tutorial-manage-vm) 
---

Damit sind Sie bereit für die nächste Phase der Einrichtung Ihres Hostpools: Registrieren Ihrer App-Gruppe in einem Arbeitsbereich.

## <a name="workspace-information"></a>Informationen zum Arbeitsbereich

Beim Einrichtungsprozess für den Hostpool wird standardmäßig eine Desktopanwendungsgruppe erstellt. Damit der Hostpool wie beabsichtigt funktioniert, müssen Sie diese App-Gruppe für Benutzer oder Benutzergruppen veröffentlichen und die App-Gruppe in einem Arbeitsbereich registrieren.

>[!NOTE]
>Wenn Sie ein App-Entwickler sind und die Apps Ihrer Organisation veröffentlichen möchten, können Sie MSIX-Apps dynamisch an Benutzersitzungen anfügen oder Ihre App-Pakete einem benutzerdefinierten VM-Image hinzufügen. Weitere Informationen finden Sie unter „Verwenden Ihrer benutzerdefinierten App mit Azure Virtual Desktop“.

### <a name="portal"></a>[Portal](#tab/azure-portal)

So registrieren Sie die Desktop-App-Gruppe in einem Arbeitsbereich:

1. Wählen Sie **Ja** aus.

   Wenn Sie **Nein** auswählen, können Sie die App-Gruppe zu einem späteren Zeitpunkt registrieren. Wir empfehlen jedoch, die Registrierung im Arbeitsbereich so schnell wie möglich zu erledigen, damit Ihr Hostpool korrekt funktioniert.

2. Wählen Sie aus, ob Sie einen neuen Arbeitsbereich erstellen oder einen vorhandenen Arbeitsbereich verwenden möchten. Die App-Gruppe kann nur in Arbeitsbereichen registriert werden, die am gleichen Standort wie der Hostpool erstellt werden.

3. Optional können Sie **Weiter: Tags >** auswählen.

    Hier können Sie Tags hinzufügen, damit Sie die Objekte mit Metadaten gruppieren und dadurch Ihren Administratoren die Arbeit erleichtern können.

4. Wählen Sie abschließend **Überprüfen + erstellen** aus.

     >[!NOTE]
     >Beim Überprüfungsprozess „Überprüfen + erstellen“ wird nicht überprüft, ob Ihr Kennwort den Sicherheitsstandards entspricht oder Ihre Architektur korrekt ist. Sie müssen daher selbst überprüfen, ob in dieser Hinsicht Probleme vorliegen.

5. Überprüfen Sie die Informationen zu Ihrer Bereitstellung, um sicherzustellen, dass alles richtig ist. Wählen Sie **Erstellen**, wenn Sie fertig sind. 

### <a name="azure-cli"></a>[Azure-Befehlszeilenschnittstelle](#tab/azure-cli)

Verwenden Sie den Befehl [az desktopvirtualization workspace create](/cli/azure/desktopvirtualization#az_desktopvirtualization_workspace_create), um den neuen Arbeitsbereich zu erstellen:

```azurecli
az desktopvirtualization workspace create --name "MyWorkspace" \
    --resource-group "MyResourceGroup" \
    --location "MyLocation" \
    --tags tag1="value1" tag2="value2" \
    --friendly-name "Friendly name of this workspace" \
    --description "Description of this workspace" 
```
---

Dadurch wird der Bereitstellungsprozess gestartet, bei dem die folgenden Objekte erstellt werden:

- Ihr neuer Hostpool.
- Eine Desktop-App-Gruppe.
- Ein Arbeitsbereich (sofern Sie keinen vorhandenen Arbeitsbereich verwenden).
- Wenn Sie sich für die Registrierung der Desktop-App-Gruppe entschieden haben, wird die Registrierung durchgeführt.
- VMs (sofern Sie sich für deren Erstellung entschieden haben), die der Domäne hinzugefügt und beim neuen Hostpool registriert werden.
- Ein Downloadlink für eine Azure-Ressourcenverwaltungsvorlage, die auf Ihrer Konfiguration basiert.

Danach sind Sie fertig!

## <a name="run-the-azure-resource-manager-template-to-provision-a-new-host-pool"></a>Ausführen der Azure Resource Manager-Vorlage zum Bereitstellen eines neuen Hostpools

Wenn Sie lieber einen automatisierten Prozess verwenden möchten, [laden Sie unsere Azure Resource Manager-Vorlage herunter](https://github.com/Azure/RDS-Templates/tree/master/ARM-wvd-templates), um stattdessen Ihren neuen Hostpool bereitzustellen.

>[!NOTE]
>Wenn Sie einen automatisierten Prozess zum Erstellen Ihrer Umgebung nutzen, benötigen Sie die aktuelle Version der JSON-Konfigurationsdatei. Die JSON-Datei finden Sie [hier](https://wvdportalstorageblob.blob.core.windows.net/galleryartifacts?restype=container&comp=list).

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie Ihren Hostpool erstellt haben, können Sie ihn nun mit RemoteApp-Programmen auffüllen. Weitere Informationen zum Verwalten von Apps in Azure Virtual Desktop finden Sie in unserem nächsten Tutorial:

> [!div class="nextstepaction"]
> [Manage app groups for Windows Virtual Desktop Preview](./manage-app-groups.md) (Verwalten von App-Gruppen für Windows Virtual Desktop (Vorschauversion))
