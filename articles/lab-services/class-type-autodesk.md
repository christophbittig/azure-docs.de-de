---
title: Einrichten eines Labs mit Autodesk mithilfe von Azure Lab Services
description: Erfahren Sie, wie Sie Labs einrichten, um Kurse für Ingenieurswesen mit Autodesk abzuhalten.
author: nicolela
ms.topic: how-to
ms.date: 04/21/2021
ms.author: nicolela
ms.openlocfilehash: 2c9abda8f42023ebc54de77caadbe2860afe137f
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177059"
---
# <a name="set-up-labs-for-autodesk"></a>Einrichten von Labs für Autodesk

In diesem Artikel wird beschrieben, wie Sie die Autodesk Inventor- und Autodesk Revit-Software für Kurse für Ingenieurswesen einrichten:
- [CAD (computer-aided design)](https://www.autodesk.com/products/inventor/new-features) und [CAM (computer-aided manufacturing)](https://www.autodesk.com/products/inventor-cam/overview) von Inventor bieten 3D-Modellierung und werden im Technikdesign verwendet.
- [Revit](https://www.autodesk.com/products/revit/overview) wird beim Architekturentwurf für die Modellierung von 3D-Gebäudeinformationen (Building Information Modelling, BIM) verwendet.

Autodesk wird häufig sowohl in Universitäten als auch in weiterführenden Schulen verwendet.  In weiterführenden Schulen ist AutoDesk beispielsweise Bestandteil des Lehrplans des [PLTW-Programms (Project Lead the Way)](./class-type-pltw.md).

## <a name="lab-configuration"></a>Labkonfiguration

Zum Einrichten dieses Labs benötigen Sie zunächst ein Azure-Abonnement und ein Lab-Konto. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/) erstellen, bevor Sie beginnen. Sobald Sie ein Azure-Abonnement erhalten, können Sie ein neues Lab-Konto in Azure Lab Services erstellen. Weitere Informationen zum Erstellen eines neuen Lab-Kontos finden Sie im Tutorial zum [Einrichten eines Lab-Kontos](./tutorial-setup-lab-account.md). Sie können auch ein vorhandenes Lab-Konto verwenden.

### <a name="lab-account-settings"></a>Lab-Kontoeinstellungen

Aktivieren Sie die Einstellungen Ihres Lab-Kontos wie in der folgenden Tabelle beschrieben. Weitere Informationen zum Aktivieren von Azure Marketplace-Images finden Sie unter [Angeben der für Lab-Ersteller verfügbaren Azure Marketplace-Images](./specify-marketplace-images.md).

| Lab-Kontoeinstellungen | Instructions |
| -------------------- | ----- |
| Marketplace-Image | Aktivieren Sie das Windows 10-Image für die Verwendung in Ihrem Lab-Konto. |

### <a name="lab-settings"></a>Lab-Einstellungen
Die empfohlene VM-Größe hängt von den Arten der Workloads ab, die Ihre Kursteilnehmer verwenden sollen.  Wir empfehlen, die Größe „Kleine GPU (Visualisierung)“ zu verwenden.

| Lab-Einrichtung | Wert und Beschreibung |
| ------------ | ------------------ |
| VM-Größe | **Kleine GPU (Visualisierung)**<br>Eignet sich am besten für Remotevisualisierung, Streaming, Gaming und die Codierung mit Frameworks wie beispielsweise OpenGL und DirectX. | 

> [!NOTE]
> Die VM-Größe **Kleine GPU (Visualisierung)** ist so konfiguriert, dass eine leistungsstarke Grafikdarstellung ermöglicht wird. Weitere Informationen zu dieser VM-Größe finden Sie im Artikel zum [Einrichten eines Labs mit GPUs](./how-to-setup-lab-gpu.md).

### <a name="license-server"></a>Lizenzserver
Sie müssen jedoch auf einen Lizenzserver zugreifen, wenn Sie beabsichtigen, das Netzwerklizenzierungsmodell von Autodesk zu verwenden.  Weitere Informationen finden Sie im Autodesk-Artikel zur [Netzwerklizenzverwaltung](https://knowledge.autodesk.com/customer-service/network-license-administration/network-deployment/preparing-for-deployment/determining-installation-type).

Für die Verwendung der Netzwerklizenzierung mit Software von Autodesk stellt [AutoDesk detaillierte Schritte zur Verfügung](https://knowledge.autodesk.com/customer-service/network-license-administration/install-and-configure-network-license), um Autodesk Network License Manager auf Ihrem Lizenzserver zu installieren.  Dieser Lizenzserver wird in der Regel in Ihrem lokalen Netzwerk platziert oder auf einer Azure-VM in einem virtuellen Azure-Netzwerk gehostet.

Nach dem Einrichten des Lizenzservers müssen Sie ein [Peering des virtuellen Netzwerks](./how-to-connect-peer-virtual-network.md) mit Ihrem [Lab-Konto](./tutorial-setup-lab-account.md) erstellen. Das Netzwerkpeering muss *vor* dem Erstellen des Labs durchgeführt werden, damit Ihre Lab-VMs auf den Lizenzserver zugreifen können und umgekehrt.

Von Autodesk generierte Lizenzdateien betten die MAC-Adresse des Lizenzservers ein.  Falls Sie Ihren Lizenzserver auf einer Azure-VM hosten möchten, müssen Sie sicherstellen, dass sich die MAC-Adresse des Lizenzservers nicht ändert. Wenn sich die MAC-Adresse ändert, müssen Sie die Lizenzdateien erneut generieren. Gehen Sie wie folgt vor, um zu verhindern, dass sich die MAC-Adresse ändert:

- Legen Sie für die Azure-VM, die Ihren Lizenzserver hostet, eine [statische private IP- und MAC-Adresse](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address) fest.
- Richten Sie sowohl Ihr Lab-Konto als auch das virtuelle Netzwerk des Lizenzservers in einer Region bzw. an einem Standort mit ausreichender VM-Kapazität ein, damit Sie diese Ressourcen nicht zu einem späteren Zeitpunkt in eine neue Region oder an einen neuen Standort verschieben müssen.

Weitere Informationen finden Sie unter [Einrichten eines Lizenzservers als freigegebene Ressource](./how-to-create-a-lab-with-shared-resource.md).

> [!WARNING]
> Vergessen Sie nicht, **vor** dem Erstellen des Labs ein [Peering für das virtuelle Netzwerk](./how-to-connect-peer-virtual-network.md) für das Lab-Konto und das virtuelle Netzwerk für den Lizenzserver durchzuführen.

### <a name="template-machine"></a>Vorlagencomputer
Die Schritte in diesem Abschnitt zeigen, wie Sie die Vorlage für virtuelle Computer einrichten:

1. Starten Sie die Vorlage für virtuelle Computer, und stellen Sie eine Verbindung mit dem Computer her.

1. Laden Sie Inventor und Revit herunter, und installieren Sie sie [mithilfe der Anweisungen von AutoDesk](https://knowledge.autodesk.com/customer-service/download-install/install-software).  Wenn Sie dazu aufgefordert werden, geben Sie den Computernamen Ihres Lizenzservers an.

1.  Veröffentlichen Sie zum Schluss die Vorlagen-VM, um die VMs der Kursteilnehmer zu erstellen.

## <a name="cost"></a>Cost
Sehen wir uns eine Beispielkostenschätzung für diesen Kurs an.  Diese Schätzung enthält nicht die Kosten für das Ausführen eines Lizenzservers. Angenommen, Sie haben einen Kurs mit 25 Kursteilnehmern, für die jeweils 20 Stunden Kurszeit geplant sind.  Zudem erhält jeder Kursteilnehmer ein Kontingent von 10 zusätzlichen Stunden für Hausaufgaben und Arbeitsaufträge außerhalb der geplanten Kurszeit.  Als VM-Größe wurde **Kleine GPU (Visualisierung)** ausgewählt, was 160 Lab-Einheiten entspricht.

- 25 Kursteilnehmer &times; (20 geplante Stunden + 10 Kontingentstunden) &times; 160 Lab-Einheiten &times; 0,01 USD pro Stunde = 1.200,00 USD

> [!IMPORTANT] 
> Diese Kostenschätzung dient ausschließlich zu Beispielzwecken.  Aktuelle Informationen zu Preisen finden Sie unter [Azure Lab Services – Preise](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie folgende Artikel zum Einrichten des Labs:

- [Hinzufügen von Benutzern](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Festlegen von Kontingenten](how-to-configure-student-usage.md#set-quotas-for-users)
- [Zeitplan festlegen](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [Registrierungslinks per E-Mail an Kursteilnehmer senden](how-to-configure-student-usage.md#send-invitations-to-users) 
