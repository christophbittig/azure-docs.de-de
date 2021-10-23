---
title: Aktivieren der geschachtelten Virtualisierung auf einem virtuellen Vorlagencomputer in Azure Lab Services | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie die geschachtelte Virtualisierung auf einem Vorlagencomputer in Azure Lab Services einrichten.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 0e730dc71150a5e201e4dcda84f9c6ed61ec2f90
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130180398"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Aktivieren der geschachtelten Virtualisierung auf einem virtuellen Vorlagencomputer in Azure Lab Services

Momentan können Sie mithilfe von Azure Lab Services in einem Lab eine Vorlage für virtuelle Computer einrichten und jedem Ihrer Benutzer jeweils eine Kopie zur Verfügung zu stellen. Wenn Sie aber eine Lehrkraft sind und Unterricht zu Netzwerken, Sicherheit oder IT geben, müssen Sie möglicherweise für jeden Ihrer Kursteilnehmer eine Umgebung bereitstellen, in der mehrere VMs über ein Netzwerk miteinander kommunizieren können.

Eine geschachtelte Virtualisierung ermöglicht Ihnen, innerhalb einer Vorlage für virtuelle Computer in einem Lab eine Umgebung mit mehreren virtuellen Computern zu erstellen. Wenn diese Vorlage veröffentlicht wird, wird für jeden Benutzer im Lab ein eingerichteter virtueller Computer bereitgestellt, in dem weitere virtuelle Computer eingeschlossen sind.  Dieser Artikel behandelt die Vorgehensweise zum Einrichten der geschachtelten Virtualisierung auf einem Vorlagencomputer in Azure Lab Services.

## <a name="what-is-nested-virtualization"></a>Was ist geschachtelte Virtualisierung?

Die geschachtelte Virtualisierung ermöglicht es Ihnen, virtuelle Computer innerhalb eines virtuellen Computers zu erstellen. Geschachtelte Virtualisierung erfolgt über Hyper-V, und steht nur für Windows-VMs zur Verfügung.

Weitere Informationen zur geschachtelten Virtualisierung finden Sie in den folgenden Artikeln:

- [Nested Virtualization in Azure (Geschachtelte Virtualisierung in Azure)](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Aktivieren der geschachtelten Virtualisierung auf einer Azure-VM](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization)

## <a name="considerations"></a>Überlegungen

Vor dem Einrichten eines Labs mit geschachtelter Virtualisierung sind folgende Punkte zu berücksichtigen.

- Wenn Sie ein neues Lab erstellen, wählen Sie als Größe des virtuellen Computers **Medium (Nested virtualization)** (Mittel (geschachtelte Virtualisierung)) oder **Large (Nested virtualization)** (Groß (geschachtelte Virtualisierung)) aus. Diese Größen virtueller Computer unterstützen geschachtelte Virtualisierung.
- Wählen Sie eine Größe aus, die sowohl für den Host als auch für den virtuellen Clientcomputer gute Leistung bereitstellt.  Denken Sie daran, dass bei der Verwendung von Virtualisierung die Größe, die Sie auswählen, nicht nur für einen Computer ausreichend sein muss, sondern sowohl für den Host als auch für alle Hyper-V-Computer, die gleichzeitig ausgeführt werden.
- Virtuelle Clientcomputer haben keinen Zugriff auf Azure-Ressourcen, z. B. DNS-Server im virtuellen Azure-Netzwerk.
- Beim virtuellen Hostcomputer muss die Einrichtung berücksichtigen, dass der Clientcomputer eine Internetverbindung besitzen muss.
- Virtuelle Clientcomputer werden als unabhängige Computer lizenziert. Informationen zur Lizenzierung für Microsoft-Betriebssysteme und -Produkte finden Sie unter [Microsoft-Lizenzierung](https://www.microsoft.com/licensing/default). Überprüfen Sie die Lizenzverträge für jegliche andere verwendete Software, bevor Sie den Vorlagencomputer einrichten.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Aktivieren der geschachtelten Virtualisierung auf einem virtuellen Vorlagencomputer

In diesem Artikel wird davon ausgegangen, dass Sie bereits ein Labkonto erstellt haben.  Weitere Informationen zum Erstellen eines neuen Labkontos finden Sie im [Tutorial zum Einrichten eines Labkontos](tutorial-setup-lab-account.md). Weitere Informationen zum Erstellen eines Labs finden Sie im Tutorial [Einrichten eines Classroom-Labs](tutorial-setup-classroom-lab.md).

>[!IMPORTANT]
>Wählen Sie bei der Erstellung des Labs als Größe für die VM **Large (nested virtualization)** (Groß (geschachtelte Virtualisierung)) oder **Medium (nested virtualization)** (Mittel (geschachtelte Virtualisierung)) aus.  Andernfalls funktioniert die verschachtelte Virtualisierung nicht.  

Informationen zum Herstellen einer Verbindung mit dem Vorlagencomputer finden Sie unter [Erstellen und Verwalten einer Classroom-Vorlage](how-to-create-manage-template.md).

Es müssen einige Aufgaben erledigt werden, um eine geschachtelte Virtualisierung zu ermöglichen.  

- **Aktivieren Sie die Hyper-V-Rolle**. Die Hyper-V-Rolle muss für die Erstellung und Ausführung von virtuellen Hyper-V-Computern auf dem virtuellen Lab Services-Computer aktiviert sein.
- **Aktivieren Sie DHCP**.  Wenn die DHCP-Rolle auf dem virtuellen Lab Services-Computer aktiviert ist, kann den virtuellen Hyper-V-Computern automatisch eine IP-Adresse zugewiesen werden.
- **Erstellen Sie ein NAT-Netzwerk für virtuelle Hyper-V-Computer**.  Das NAT-Netzwerk ist so eingerichtet, dass die virtuellen Hyper-V-Computer Zugang zum Internet haben.  Die virtuellen Hyper-V-Computer können miteinander kommunizieren.

>[!NOTE]
>Das auf dem virtuellen Lab Services-Computer erstellte NAT-Netzwerk ermöglicht einem virtuellen Hyper-V-Computer den Zugriff auf das Internet und auf andere virtuelle Hyper-V-Computer auf demselben virtuellen Lab Services-Computer.  Der virtuelle Hyper-V-Computer wird nicht in der Lage sein, auf Azure-Ressourcen wie DNS-Server im virtuellen Azure-Netzwerk zuzugreifen.

Die Ausführung der oben aufgeführten Aufgaben kann mithilfe eines Skripts oder mit Windows-Tools erfolgen.  Weitere Informationen finden Sie in den folgenden Abschnitten.

### <a name="using-script-to-enable-nested-virtualization"></a>Verwenden eines Skripts zum Aktivieren geschachtelter Virtualisierung

Weitere Informationen zur Verwendung des automatisierten Setups für geschachtelte Virtualisierung mit Windows Server 2016 oder Windows Server 2019 finden Sie unter [Aktivieren geschachtelter Virtualisierung auf einem virtuellen Vorlagencomputer in Azure Lab Services mithilfe eines Skripts](how-to-enable-nested-virtualization-template-vm-using-script.md). Sie verwenden Skripts aus [Lab Services-Hyper-V-Skripts](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV), um die Hyper-V-Rolle zu installieren.  Die Skripts richten auch das Netzwerk ein, sodass die virtuellen Hyper-V-Computer über Internetzugang verfügen können.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Verwenden von Windows-Tools zum Aktivieren geschachtelter Virtualisierung

Weitere Informationen zum Einrichten geschachtelter Virtualisierung mit Windows Server 2016 oder Windows Server 2019 mit Windows-Rollen und Verwaltungstools finden Sie unter [Manuelles Aktivieren geschachtelter Virtualisierung auf einem virtuellen Vorlagencomputer in Azure Lab Services](how-to-enable-nested-virtualization-template-vm-ui.md).  Die Anleitungen beziehen sich auch auf das Einrichten des Netzwerks, sodass die virtuellen Hyper-V-Computer über Internetzugang verfügen können.