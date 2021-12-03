---
title: Erstellen und Verwalten von abrufbaren VMs
description: Hier erfahren Sie mehr über das Hinzufügen eines abrufbaren virtuellen Computers in Azure DevTest Labs über das Azure-Portal sowie die folgenden Prozesse zum Anfordern bzw. Aufheben des Anspruchs eines virtuellen Computers.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 62d5719d118bfa182be60933b61e5294e1c219a8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278415"
---
# <a name="create-and-manage-claimable-vms-in-azure-devtest-labs"></a>Erstellen und Verwalten anforderbarer virtueller Computer in einem Lab in Azure DevTest Labs
Einen anforderbaren virtuellen Computer fügen Sie einem Lab in ähnlicher Weise hinzu wie [einen virtuellen Standardcomputer](devtest-lab-add-vm.md), d.h. über eine *Basis*, also entweder über ein [benutzerdefiniertes Image](devtest-lab-create-template.md), eine [Formel](devtest-lab-manage-formulas.md) oder ein [Marketplace-Image](devtest-lab-configure-marketplace-images.md). In diesem Tutorial wird erläutert, wie Sie über das Azure-Portal einem Lab in DevTest Labs einen anforderbaren virtuellen Computer hinzufügen. Zudem wird der Vorgang beschrieben, mit dem ein Benutzer den virtuellen Computer anfordert und diesen Anspruch auch wieder aufhebt.

## <a name="steps-to-add-a-claimable-vm-to-a-lab-in-azure-devtest-labs"></a>Schritte zum Hinzufügen eines anforderbaren virtuellen Computers zu einem Lab in Azure DevTest Labs
1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.
1. Wählen Sie **Alle Dienste** und dann im Abschnitt **DEVOPS** die Option **DevTest Labs**. Wählen Sie im Abschnitt **DEVOPS** das Sternchen (*) neben **DevTest Labs** aus. Mit dieser Aktion wird **DevTest Labs** dem linken Navigationsmenü hinzugefügt, sodass Sie beim nächsten Mal einfach darauf zugreifen können. Anschließend können Sie im linken Navigationsmenü **DevTest Labs** auswählen.

    ![Screenshot, der alle Dienste zeigt - wählen Sie DevTest Labs.](./media/devtest-lab-create-lab/all-services-select.png)
1. Wählen Sie in der Liste der Labs das Lab aus, in dem Sie den neuen virtuellen Computer (VM, Virtual Machine) erstellen möchten.
2. Wählen Sie auf der Seite **Übersicht** des Labs die Option **+ Hinzufügen** aus.

    ![Screenshot, der die Schaltfläche VM hinzufügen zeigt.](./media/devtest-lab-add-vm/devtestlab-home-blade-add-vm.png)
1. Wählen Sie auf der Seite **Basis auswählen** ein Marketplace-Image für den virtuellen Computer aus.
1. Führen Sie auf der Seite **Virtuelle Computer** auf der Registerkarte **Grundeinstellungen** die folgenden Aktionen aus:
    1. Geben Sie im Textfeld **Name des virtuellen Computers** einen Namen für den virtuellen Computer ein. Das Textfeld wird für Sie mit einem eindeutigen, automatisch generierten Namen vorausgefüllt. Der Name entspricht dem Benutzernamen in Ihrer E-Mail-Adresse, gefolgt von einer eindeutigen dreistelligen Zahl. Diese Funktion erspart Ihnen die Zeit, sich einen Computernamen auszudenken und ihn jedes Mal einzugeben, wenn Sie einen Computer erstellen. Sie können dieses automatisch ausgefüllte Feld mit einem Namen Ihrer Wahl überschreiben, wenn Sie dies wünschen. Um den automatisch ausgefüllten Namen für die VM zu überschreiben, geben Sie einen Namen in das Textfeld **Name der virtuellen Maschine** ein.
    2. Geben Sie einen **Benutzernamen** ein, dem Administratorrechte auf dem virtuellen Computer erteilt werden. Der **Benutzername** für das Gerät ist mit einem eindeutigen, automatisch generierten Namen vorausgefüllt. Der Name entspricht dem Benutzernamen in Ihrer E-Mail-Adresse. Diese Funktion erspart Ihnen die Zeit, sich jedes Mal für einen Benutzernamen zu entscheiden, wenn Sie einen neuen Computer erstellen. Auch hier können Sie dieses automatisch ausgefüllte Feld mit einem Benutzernamen Ihrer Wahl überschreiben, wenn Sie dies wünschen. Um den automatisch ausgefüllten Wert für den Benutzernamen zu überschreiben, geben Sie einen Wert in das Textfeld **Benutzername** ein. Dieser Benutzer erhält auf dem virtuellen Computer **Administratorrechte**.
    3. Wenn Sie den ersten virtuellen Computer im Lab erstellen, geben Sie ein **Kennwort** für den Benutzer ein. Um dieses Kennwort als Standardkennwort im Azure-Schlüsseltresor des Labs zu speichern, wählen Sie **Als Standardkennwort speichern** aus. Das Standardkennwort wird im Schlüsseltresor mit dem folgenden Namen gespeichert: **VmPassword**. Wenn Sie versuchen, nachfolgende VMs im Lab zu erstellen, wird **VmPassword** automatisch für das **Kennwort** ausgewählt. Um den Wert zu überschreiben, deaktivieren Sie das Kontrollkästchen **Gespeichertes Geheimnis verwenden** und geben ein Kennwort ein.

        Alternativ können Sie Geheimnisse auch zuerst im Schlüsseltresor speichern und anschließend beim Erstellen einer VM im Lab verwenden. Weitere Informationen finden Sie unter [Speichern eines Geheimnisses in Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md). Zur Verwendung eines Kennworts, das in einem Schlüsseltresor hinterlegt ist, wählen Sie **Gespeichertes Geheimnis verwenden**, und geben Sie einen Schlüsselwert an, der Ihrem Geheimnis (Kennwort) entspricht.
    4. Wählen Sie im Abschnitt **Weitere Optionen** die Option **Größe ändern**. Wählen Sie eines der vordefinierten Elemente aus, die die Prozessorkerne, die RAM-Größe und die Größe der Festplatte für den zu erstellenden virtuellen Computer angeben.
    5. Wählen Sie **Artefakte hinzufügen oder entfernen**. Wählen Sie die Artefakte aus, die Sie dem Basisimage hinzufügen möchten, und konfigurieren Sie sie.
    
    >[!NOTE] 
    >Wenn Sie noch nicht mit DevTest Labs oder dem Konfigurieren von Artefakten vertraut sind, lesen Sie den Abschnitt [Hinzufügen eines Artefakts nach der Installation](./devtest-lab-add-vm.md#add-artifacts-after-installation), und kehren Sie dann hierher zurück.
2. Wechseln Sie oben zur Registerkarte **Erweiterte Einstellungen**, und führen Sie die folgenden Aktionen aus:
    1. Wählen Sie **VNET ändern** aus, um das virtuelle Netzwerk zu ändern, in dem sich der virtuelle Computer befindet.
    2. Wählen Sie zum Ändern des Subnetzes die Option **Subnetz ändern** aus.
    3. Geben Sie an, ob die IP-Adresse des virtuellen Computers **öffentlich, privat oder freigegeben** ist.
    4. Geben Sie **Ablaufdatum und -uhrzeit** an, damit der virtuelle Computer automatisch gelöscht wird.
    5. Wählen Sie für **Make this machine claimable** (Diesen Computer als abrufbar festlegen) die Option **Ja** aus, damit der virtuelle Computer von einem Labbenutzer abgerufen werden kann.
    6. Geben Sie die Anzahl von **VM-Instanzen** an, die Sie Labbenutzern zur Verfügung stellen möchten.
3. Wählen Sie **Erstellen** , um den angegebene virtuellen Computer dem Lab hinzuzufügen.

   Auf der Seite für das Lab wird der Status der VM-Erstellung angezeigt: erst als **Wird erstellt** und dann als **Wird ausgeführt**, nachdem die VM gestartet wurde.

> [!NOTE]
> Wenn Sie virtuelle Computer im Lab über [Azure Resource Manager-Vorlagen](devtest-lab-create-environment-from-arm.md) bereitstellen, können Sie anforderbare virtuelle Computer erstellen, indem Sie die Eigenschaft **allowClaim** im Abschnitt „Eigenschaften“ auf „true“ festlegen.


## <a name="use-a-claimable-vm"></a>Verwenden Sie eine anspruchsberechtigte VM

Ein Benutzer kann jede VM aus der Liste der **"** beanspruchbaren virtuellen Maschinen** beanspruchen, indem er einen der folgenden Schritte ausführt:

* Klicken Sie mit der rechten Maustaste auf eine der VMs in der Liste **Beanspruchbare virtuelle Maschinen** unten im Bereich **Übersicht** des Labors und wählen Sie **Maschine beanspruchen**.

  ![Screenshot, der die Beantragung einer bestimmten anspruchsberechtigten VM zeigt.](./media/devtest-lab-add-vm/devtestlab-claim-VM.png)


* Wählen Sie oben im Fenster **Übersicht** die Option **Anspruch beliebig**. Aus der Liste der anforderbaren virtuellen Computer wird ein virtueller Computer zufällig zugewiesen.

  ![Screenshot, der die Beantragung einer anspruchsberechtigten VM zeigt.](./media/devtest-lab-add-vm/devtestlab-claim-any.png)


Nachdem ein Benutzer eine VM beansprucht hat, startet DevTest Labs die Maschine und verschiebt sie nach oben in die Liste **Meine virtuellen Maschinen** des Laborbenutzers. Dies bedeutet, dass der Lab-Benutzer nun Besitzerberechtigungen für diesen Computer besitzt. Die für diesen Schritt benötigte Zeit kann je nach Startzeiten und anderen benutzerdefinierten Aktionen, die während des Anspruchsereignisses durchgeführt werden, variieren. Nachdem der Computer beansprucht wurde, ist er nicht mehr im beanspruchbaren Pool verfügbar.  

## <a name="unclaim-a-vm"></a>Aufgeben der Beanspruchung eines virtuellen Computers

Wenn ein Benutzer mit der Verwendung eines beanspruchten virtuellen Computers fertig ist und diesen jemand anderem verfügbar machen möchte, kann er den beanspruchten Computer an die Liste der anforderbaren virtuellen Computern zurückgeben, indem er einen der folgenden Schritte befolgt:

- Klicken Sie in der Liste **Meine virtuellen Maschinen** mit der rechten Maustaste auf eine der VMs in der Liste oder wählen Sie die Ellipse **...** und wählen Sie **Abmelden**.

  ![Screenshot, der zeigt, wie eine VM aus der Liste entfernt wird.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM2.png)

- Wählen Sie in der Liste **Meine virtuellen Maschinen** eine VM, um ihr Verwaltungsfenster zu öffnen, und wählen Sie dann **Aufheben** in der oberen Menüleiste.

  ![Screenshot, der das Aufheben der Beanspruchung einer VM im Verwaltungsbereich der VM zeigt.](./media/devtest-lab-add-vm/devtestlab-unclaim-VM.png)

Wenn ein Benutzer den Anspruch für einen virtuellen Computer zurücknimmt, verfügt er nicht mehr über Besitzerberechtigungen für diesen speziellen virtuellen Lab-Computer, und dieser kann von jedem anderen Lab-Benutzer in dem Zustand beansprucht werden, in dem er in den Pool zurückgegeben wurde. 

### <a name="transfer-the-data-disk"></a>Übertragen des Datenträgers
Wenn eine abrufbare VM über einen Datenträger verfügt, und ein Benutzer den Anspruch darauf aufhebt, bleibt der Datenträger in der VM erhalten. Wenn dann ein anderer Benutzer diese VM beansprucht, beansprucht dieser neue Benutzer den Datenträger und die VM.

Dieser Vorgang wird als Übertragen des Datenträgers bezeichnet. Der Datenträger wird dann in der Liste der **eigenen Datenträger** des neuen Benutzers verfügbar, und der Benutzer kann diese dann verwalten.

![Screenshot, der die Freigabe von Datenträgern zeigt.](./media/devtest-lab-add-vm/devtestlab-unclaim-datadisks.png)



## <a name="next-steps"></a>Nächste Schritte
* Nachdem der virtuelle Computer erstellt wurde, können Sie eine Verbindung mit diesem herstellen, indem sie im Verwaltungsbereich auf **Verbinden** klicken.
* Erkunden Sie den [DevTest Labs-Azure Resource Manager-Katalog mit Schnellstartvorlagen](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates).
