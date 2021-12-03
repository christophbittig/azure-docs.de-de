---
title: Netzwerkisolation
description: Hier erfahren Sie mehr über die Netzwerkisolation in Azure DevTest Labs.
ms.topic: how-to
ms.date: 08/25/2020
ms.openlocfilehash: 555c6b13a46ba6cd70ef136d9a6a4cf88d14c4f9
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397716"
---
# <a name="network-isolation-in-devtest-labs"></a>Netzwerkisolation in DevTest Labs

Ein [virtuelles Azure-Netzwerk](../virtual-network/virtual-networks-overview.md) fungiert als Sicherheitsgrenze und schirmt Ihre Azure-Ressourcen vom öffentlichen Internet ab. Sie können auch ein virtuelles Azure-Netzwerk mit Ihrem lokalen Netzwerk verknüpfen, um eine sichere Verbindung mit Ihren lokalen Ressourcen herzustellen. In DevTest Labs können Sie [alle virtuellen Labcomputer](devtest-lab-configure-vnet.md) und [Umgebungen in Ihrem Netzwerk isolieren](connect-environment-lab-virtual-network.md), um sicherzustellen, dass die Labressourcen die Netzwerkrichtlinien Ihrer Organisation einhalten. 

Als Labbesitzer können Sie das Lab auch vollständig isolieren. Sie isolieren virtuelle Computer und Umgebungen im ausgewählten Netzwerk. Sie können auch lab-Speicherkonten und Schlüsseltresore isolieren, die Sie in Ihrem Abonnement erstellt haben. In diesem Artikel wird die Erstellung eines isolierten Netzwerklabs Schritt für Schritt beschrieben. 

Lesen Sie auch die folgenden Artikel:

- [Verschlüsseln eines von einem Lab verwendeten Azure-Speichers in Azure DevTest Labs](encrypt-storage.md)
- [Speichern von Geheimnissen in einem Schlüsseltresor in Azure DevTest Labs](devtest-lab-store-secrets-in-key-vault.md)
 
> [!NOTE]
> Die Netzwerkisolation wird derzeit nur für die Erstellung neuer Labs unterstützt.

## <a name="steps-to-enable-network-isolation-during-lab-creation"></a>Schritte zum Aktivieren der Netzwerkisolation während der Erstellung von Labs

1. Wechseln Sie während der Lab-Erstellung zur Registerkarte **Netzwerk**.
1. Sie können entweder ein Netzwerk vom Typ **Standard** auswählen, das vom Lab für Sie erstellt wird, oder im Dropdownmenü ein vorhandenes Netzwerk auswählen. Sie können nur Netzwerke auswählen, die sich in derselben Region und demselben Abonnement wie das Lab befinden. 

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Erstellen eines Labs](./media/network-isolation/create-lab.png)
1. Wählen Sie ein Subnetz aus.

    > [!div class="mx-imgBorder"]
    > ![Screenshot: Erstellen eines Subnetzes](./media/network-isolation/create-lab-subnet.png)
1. Wenn Sie das Lab-Speicher-Konto und den Schlüsseltresor im Standardnetzwerk isolieren möchten, ist keine weitere Aktion erforderlich. Das Lab dient ab sofort zum Isolieren von Ressourcen.
 
    > [!div class="mx-imgBorder"]
    > ![Screenshot: Netzwerkisolation](./media/network-isolation/isolate-lab-resources.png)
1. Wenn Sie das Lab-Speicher-Konto und den Schlüsseltresor in einem vorhandenen Netzwerk isolieren möchten, das Sie ausgewählt haben, führen Sie nach der Laberstellung die folgenden Schritte aus. Mit diesen Schritten wird sichergestellt, dass das Lab weiterhin im isolierten Modus funktioniert. 
 
    > [!div class="mx-imgBorder"]
    > ![Screenshot: Isolieren von Ressourcen](./media/network-isolation/isolate-my-vnet.png)

    > [!IMPORTANT]
    > Der Labbesitzer muss diese Schritte ausführen, bevor er Ressourcen im Lab konfiguriert oder erstellt.

### <a name="steps-to-follow-post-lab-creation"></a>Schritte nach der Lab-Erstellung

1. Wählen Sie auf der Homepage für das Lab die **Ressourcengruppe** auf der Seite **Übersicht** aus. Die Seite **Ressourcengruppe** für die Ressourcengruppe, in der das Lab enthalten ist, sollte angezeigt werden. 
 
   > [!div class="mx-imgBorder"]
   > ![Screenshot: Contoso-Lab](./media/network-isolation/contoso-lab.png)
1. Wählen Sie das Azure-Speicherkonto des Labs aus. Die Namenskonvention für das Lab-Speicher-Konto lautet: \<*labNameWithoutInvalidCharacters*> . \<*4-digit number*> Wenn der Name des Labs beispielsweise „contosolab“ ist, könnte der Name des Speicherkontos „acontosolab1234“ lauten.
 
   > [!div class="mx-imgBorder"]
   > ![Screenshot: Contoso-Test](./media/network-isolation/contoso-test.png)
1. Navigieren Sie im Speicherkonto zu „Firewalls und virtuelle Netzwerke“, und vergewissern Sie sich, dass das Kontrollkästchen „Vertrauenswürdigen Microsoft-Diensten den Zugriff auf dieses Speicherkonto erlauben“ aktiviert ist. Da [DevTest Labs ein vertrauenswürdiger Microsoft-Dienst](../storage/common/storage-network-security.md#trusted-microsoft-services) ist, ermöglicht diese Option die normale Nutzung des Labs im netzwerkisolierten Modus. 

   > [!div class="mx-imgBorder"]
   > ![Screenshot: Contoso-Labfirewalls](./media/network-isolation/contoso-lab-firewalls-vnets.png)
1. Klicken Sie als Nächstes auf **+ Vorhandenes virtuelles Netzwerk hinzufügen**, wählen Sie das virtuelle Netzwerk und das Subnetz aus, die Sie beim Erstellen des Labs ausgewählt haben, und klicken Sie auf **Aktivieren**. 

   > [!div class="mx-imgBorder"]
   > ![Screenshot: Contoso my vnet](./media/network-isolation/contoso-lab-my-vnet.png)
5.  Nachdem der Dienstendpunkt für das ausgewählte virtuelle Netzwerk aktiviert wurde, klicken Sie auf **Hinzufügen**. 

   > [!div class="mx-imgBorder"]
   > ![Screenshot: Hinzufügen](./media/network-isolation/contoso-firewall-add.png)
 
Dadurch lässt Azure Storage eingehende Verbindungen aus dem hinzugefügten virtuellen Netzwerk zu und ermöglicht die ordnungsgemäße Nutzung des Labs im netzwerkisolierten Modus. 

Sie können diese Schritte auch automatisieren, um diese Einstellung für mehrere Labs zu konfigurieren. 

[Weitere Informationen zum Verwalten standardmäßiger Netzwerkzugriffsregeln für Azure Storage mithilfe von PowerShell und der CLI](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#powershell)

## <a name="network-isolation-for-an-existing-lab"></a>Netzwerkisolation für ein vorhandenes Lab

Als Labbesitzer können Sie das Netzwerk für ein vorhandenes Lab isolieren. [Dieses Beispielskript](https://github.com/Azure/azure-devtestlab/blob/master/Tools/ConvertDtlLabToIsolatedNetwork/Convert-DtlLabToIsolatedNetwork.ps1) veranschaulicht, wie Sie ein vorhandenes Lab und zugeordnete Labressourcen in einen isolierten Netzwerkmodus versetzen. 

## <a name="things-to-remember-while-using-a-lab-in-a-network-isolated-mode"></a>Wichtige Aspekte bei der Verwendung eines Labs im netzwerkisolierten Modus

### <a name="accessing-labs-storage-account-outside-the-lab"></a>Zugreifen auf das Lab-Speicherkonto außerhalb des Labs 

Der Labbesitzer muss den Zugriff auf das Speicherkonto eines netzwerkisolierten Labs über einen zulässigen Endpunkt explizit aktivieren. Dieser Zugriff ist für Aktionen wie das Hochladen einer VHD in das Speicherkonto zum Erstellen benutzerdefinierter Images erforderlich. Sie können den Zugriff aktivieren, indem Sie einen virtuellen Computer erstellen und sicher von diesem virtuellen Computer aus auf das Speicherkonto des Labs zugreifen. 

[Weitere Informationen zum privaten Zugriff auf ein Speicherkonto über einen virtuellen Computer](../private-link/tutorial-private-endpoint-storage-portal.md)

### <a name="exporting-usage-data-from-the-lab"></a>Exportieren von Nutzungsdaten aus dem Lab 

Um [personenbezogene Nutzungsdaten für ein netzwerkisoliertes Lab](personal-data-delete-export.md)zu exportieren, muss der Labbesitzer explizit ein Speicherkonto bereitstellen und ein Blob innerhalb des Kontos generieren, um die Daten zu speichern. 

Wenn kein Lab-Speicher-Konto bereitgestellt wird, schlägt dieser Vorgang im netzwerkisolierten Modus fehl. Auf das Speicherkonto des Labs kann das Lab nicht zugreifen, wenn der Kunde kein Speicherkonto bereitstellt. 

[Weitere Informationen zum Exportieren der Labnutzungsdaten in einem bestimmten Speicherkonto](personal-data-delete-export.md#azure-powershell)

## <a name="next-steps"></a>Nächste Schritte

[Automatisches Erstellen oder Ändern von Labs mit Azure Resource Manager-Vorlagen und PowerShell](devtest-lab-use-arm-and-powershell-for-lab-resources.md)
