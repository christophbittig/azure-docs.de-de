---
title: Verwalten von Formeln in Azure DevTest Labs zum Erstellen von VMs
description: Dieser Artikel veranschaulicht, wie Sie eine Formel anhand einer Basis (benutzerdefiniertes Image, Marketplace-Image oder eine andere Formel) oder aus einem vorhandenen virtuellen Computer erstellen.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 632e05a566e1891afff3440563a2fabde0b669d3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128678533"
---
# <a name="manage-azure-devtest-labs-formulas"></a>Verwalten von Azure DevTest Labs-Formeln

[!INCLUDE [devtest-lab-formula-definition](../../includes/devtest-lab-formula-definition.md)]

Dieser Artikel veranschaulicht, wie Sie eine Formel anhand einer Basis (benutzerdefiniertes Image, Marketplace-Image oder eine andere Formel) oder aus einem vorhandenen virtuellen Computer erstellen. In diesem Artikel wird auch gezeigt, wie vorhandene Formeln verwaltet werden.

## <a name="create-a-formula"></a>Erstellen einer Formel
Jeder Benutzer mit der Berechtigung *Benutzer* in DevTest Labs kann virtuelle Computer auf Basis einer Formel erstellen. Es gibt zwei Möglichkeiten, um Formeln zu erstellen: 

* Anhand einer Basis: Entscheiden Sie sich für diese Option, wenn Sie alle Merkmale der Formel definieren möchten.
* Aus einem vorhandenen virtuellen Labcomputer: Entscheiden Sie sich für diese Option, wenn Sie eine Formel basierend auf den Einstellungen eines vorhandenen virtuellen Computers erstellen möchten.

Weitere Informationen zum Hinzufügen von Benutzern und Berechtigungen finden Sie unter [Hinzufügen von Besitzern und Benutzern in Azure DevTest Labs](./devtest-lab-add-devtest-user.md).

### <a name="create-a-formula-from-a-base"></a>Erstellen einer Formel anhand einer Basis
In den folgenden Schritten wird beschrieben, wie Sie eine Formel auf der Basis eines benutzerdefinierten Images, eines Marketplace-Images oder auf Grundlage einer anderen Formel erstellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.

3. Wählen Sie in der Liste der Labs das gewünschte Lab aus.  

4. Wählen Sie auf der Seite für das Lab im linken Menü die Option **Formeln (wiederverwendbare Basen)** aus.
5. Wählen Sie auf der Seite **Formeln** die Option **+ Hinzufügen** aus.
   
    ![Formel hinzufügen](./media/devtest-lab-create-formulas/add-formula.png)
6. Wählen Sie auf der Seite **Basis auswählen** die Grundlage aus, anhand derer Sie die Formel erstellen möchten (also das benutzerdefinierte Image, das Marketplace-Image oder ein Shared Image Gallery-Image).

    :::image type="content" source="./media/devtest-lab-create-formulas/select-base.png" alt-text="Auswählen des Basisimages":::
1. Geben Sie auf der Registerkarte **Grundeinstellungen** der Seite **Formel erstellen** die folgenden Werte an:
   
    * **Formelname**: Geben Sie einen Namen für die Formel ein. Dieser Wert wird in der Liste der Basisimages angezeigt, wenn Sie einen virtuellen Computer erstellen. Der Name wird während der Eingabe überprüft. Falls er nicht gültig ist, werden Sie in einer Meldung über die Anforderungen für einen gültigen Namen informiert.
    - Geben Sie eine optionale **Beschreibung** für die Formel ein. 
    * **Benutzername**: Geben Sie einen Benutzernamen ein, dem Administratorrechte erteilt wurden.
    * **Kennwort**: Geben Sie einen Wert ein, der dem geheimen Schlüssel bzw. dem Kennwort zugeordnet ist, das für den angegebenen Benutzer verwendet werden soll, oder wählen Sie diesen Wert aus der Dropdownliste aus. Informationen zum Speichern von Geheimnissen in einem Schlüsseltresor und deren Verwendung beim Erstellen von Lab-Ressourcen finden Sie unter [Speichern von Geheimnissen in Azure DevTest Labs](devtest-lab-store-secrets-in-key-vault.md).

        Wählen Sie **Gespeichertes Geheimnis verwenden** aus, wenn Sie anstelle eines Kennworts ein Geheimnis aus Azure Key Vault verwenden möchten. 
    * **Virtueller Computer - Größe:** Wählen Sie **Größe ändern** aus, um die Größe des virtuellen Computers zu ändern. 
    - **Typ des Betriebssystemdatenträgers:** Wählen Sie die Art des zu verwendenden Datenträgers aus (HDD Standard, SSD Standard oder SSD Premium).
    * **Artefakte:** Wählen Sie die Seite **Artefakte hinzufügen oder entfernen** aus, auf der Sie die Artefakte auswählen und konfigurieren können, die dem Basisimage hinzugefügt werden sollen. Weitere Informationen zu Artefakten finden Sie unter [Erstellen benutzerdefinierter Artefakte für Ihren virtuellen DevTest Labs-Computer](devtest-lab-artifact-author.md).

        ![Seite mit den Grundeinstellungen](./media/devtest-lab-create-formulas/basic-settings.png)
8. Wechseln Sie zur Registerkarte **Erweiterte Einstellungen**, und geben Sie die folgenden Werte an:
    - **Virtuelles Netzwerk:** Wählen Sie **VNET ändern** aus, um das virtuelle Netzwerk zu ändern. 
    - **Subnetz:** Wählen Sie zum Ändern des Subnetzes die Option **Subnetz ändern** aus. 
    - **IP-Adresskonfiguration**: Geben Sie an, ob Sie öffentliche, private oder freigegebene IP-Adressen verwenden möchten. Weitere Informationen zu freigegebenen IP-Adressen finden Sie unter [Grundlegendes zu freigegebenen IP-Adressen in Azure DevTest Labs](./devtest-lab-shared-ip.md).
    - **Ablaufdatum und -zeit**: Dieses Feld kann nicht bearbeitet werden. 
    - **Diesen Computer als anforderbar festlegen**: Wenn Sie einen Computer als „anforderbar“ festlegen, bedeutet dies, dass dem Computer zum Zeitpunkt der Erstellung kein Besitz zugewiesen wird. Auf der Seite des Labs können Labbenutzer den Besitz des Computers übernehmen („abrufen“).  

        ![Screenshot, der die erweiterten Einstellungen für die Seite „Formel erstellen (wiederverwendbare Basis)“ zeigt.](./media/devtest-lab-create-formulas/advanced-settings.png)
    - Wenn Sie ein Shared Image Gallery-Image als Basis ausgewählt haben, wird auch das Feld **Imageversion** angezeigt, in dem Sie die Version des Images aus dem Katalog auswählen können, das Sie als Basis verwenden möchten. 

        ![Seite „Erweiterte Einstellungen“](./media/devtest-lab-create-formulas/advanced-settings-shared-image-gallery.png)
8. Wählen Sie **Senden** aus, um die Formel zu erstellen.

9. Wenn die Formel erstellt wurde, wird sie in der Liste auf der Seite **Formeln** angezeigt.

### <a name="create-a-formula-from-a-vm"></a>Erstellen einer Formel aus einem virtuellen Computer
Die folgenden Schritte führen Sie durch den Prozess der Erstellung einer neuen Formel aus einem vorhandenen virtuellen Labcomputer. 

> [!NOTE]
> Damit Sie anhand eines virtuellen Computers eine Formel erstellen können, muss dieser virtuelle Computer nach dem 30. März 2016 erstellt worden sein. 
> 
> 

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.
2. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
3. Wählen Sie in der Liste der Labs das gewünschte Lab aus.  
4. Wählen Sie auf der Seite **Übersicht** des Labs den virtuellen Computer aus, über den Sie die Formel erstellen möchten.
   
    ![Virtuelle Labs-Computer](./media/devtest-lab-create-formulas/my-vms.png)
5. Wählen Sie auf der Seite für den virtuellen Computer die Option **Formel erstellen (wiederverwendbare Basis)** aus.
   
    ![Formel erstellen](./media/devtest-lab-create-formulas/create-formula-menu.png)
6. Geben Sie auf der Seite **Formel erstellen** einen **Namen** und eine **Beschreibung** für die neue Formel ein.
   
    ![Seite „Formel erstellen“](./media/devtest-lab-create-formulas/create-formula-blade.png)
7. Wählen Sie **OK** , um die Formel zu erstellen.

## <a name="modify-a-formula"></a>Ändern einer Formel
Um eine Formel zu ändern, gehen Sie folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.
2. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
3. Wählen Sie in der Liste der Labs das gewünschte Lab aus.  
4. Wählen Sie auf der Seite für das Lab die Option **Formeln (wiederverwendbare Basen)** aus.
   
    ![Screenshot, der die Seite des Labs mit der Auswahl „Formeln (wiederverwendbare Basen)“ zeigt.](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Wählen Sie auf der Seite **Labformeln** die Formel aus, die Sie ändern möchten.
6. Nehmen Sie auf der Seite **Formel aktualisieren** die gewünschten Änderungen vor, und wählen Sie **Aktualisieren** aus.

## <a name="delete-a-formula"></a>Löschen einer Formel
Um eine Formel zu löschen, gehen Sie folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.
2. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.
3. Wählen Sie in der Liste der Labs das gewünschte Lab aus.  
4. Wählen Sie auf der Seite **Einstellungen** für das Lab die Option **Formeln** aus.
   
    ![Menü „Formel“](./media/devtest-lab-manage-formulas/lab-settings-formulas.png)
5. Wählen Sie auf der Seite **Labformeln** die Auslassungspunkte rechts neben der zu löschenden Formel aus.
   
    ![Screenshot, der die Seite „Lab-Formeln“ mit hervorgehobenen Auslassungspunkten für die Formeloptionen zeigt.](./media/devtest-lab-manage-formulas/lab-formulas-blade.png)
6. Wählen Sie im Kontextmenü der Formel die Option **Löschen** aus.
   
    ![Kontextmenü „Formel“](./media/devtest-lab-manage-formulas/formula-delete-context-menu.png)
7. Wählen Sie im Bestätigungsdialogfeld **Ja** .

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-blog-posts"></a>Verwandte Blogbeiträge
* [Custom images or formulas? (Benutzerdefinierte Images oder Formeln?)](/azure/devtest-labs/devtest-lab-faq#what-is-the-difference-between-a-custom-image-and-a-formula)

## <a name="next-steps"></a>Nächste Schritte
Nachdem Sie eine Formel erstellt haben, die zum Erstellen virtueller Computer verwendet werden soll, besteht der nächste Schritt darin, [Ihrem Lab einen virtuellen Computer hinzuzufügen](devtest-lab-add-vm.md).