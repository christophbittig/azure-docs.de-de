---
title: Private Link für Azure API for FHIR
description: In diesem Artikel wird beschrieben, wie Sie einen privaten Endpunkt für Azure API for FHIR-Dienste einrichten.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/27/2021
ms.author: zxue
ms.openlocfilehash: 6b025c35c5faa64a2333710693c992a410cafecc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346303"
---
# <a name="configure-private-link"></a>Konfigurieren von Private Link

Private Link ermöglicht Den Zugriff auf Azure API for FHIR über einen privaten Endpunkt. Dabei handelt es sich um eine Netzwerkschnittstelle, die Sie privat und sicher über eine private IP-Adresse aus Ihrem virtuellen Netzwerk verbindet. Mit privatem Link können Sie sicher über Ihr VNET als Erstanbieterdienst auf unsere Dienste zugreifen, ohne ein öffentliches Domain Name System (DNS) verwenden zu müssen. In diesem Artikel wird beschrieben, wie Sie Ihren privaten Endpunkt für ihre Azure API for FHIR.

>[!Note]
>Weder Private Link noch Azure API for FHIR kann nach Aktivierung von Private Link aus einer Ressourcengruppe in eine andere oder einem Abonnement in ein anderes verschoben werden. Um eine Bewegung zu machen, löschen Sie zuerst die Private Link, und verschieben Sie dann Azure API for FHIR. Erstellen Sie eine neue Private Link, nachdem die Bewegung abgeschlossen ist. Bewerten Sie potenzielle Sicherheitskonsequenzen, bevor Sie die Private Link-Instanz löschen.
>
>Wenn das Exportieren von Überwachungsprotokollen und Metriken für Azure API for FHIR aktiviert ist, aktualisieren Sie die Exporteinstellung über Einstellungen **über** das Portal.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie einen privaten Endpunkt erstellen, müssen Sie zunächst einige Azure-Ressourcen erstellen:

- Ressourcengruppe: Die Ressourcengruppe, die das virtuelle Netzwerk und den privaten Endpunkt enthalten soll.
- Azure API for FHIR: Die FHIR-Ressource, die Sie hinter einem privaten Endpunkt platzieren möchten.
- Virtuelles Netzwerk: Das VNet, mit dem Ihre Clientdienste und der private Endpunkt verbunden werden.

Weitere Informationen finden Sie in [Private Link Dokumentation.](../../private-link/index.yml)

## <a name="create-private-endpoint"></a>Erstellen eines privaten Endpunkts

Zum Erstellen eines privaten Endpunkts kann ein Entwickler mit RBAC-Berechtigungen (Role-Based Access Control, rollenbasierte Zugriffssteuerung) für die FHIR-Ressource Azure-Portal, [Azure PowerShell](../../private-link/create-private-endpoint-powershell.md)oder [Azure CLI.](../../private-link/create-private-endpoint-cli.md) Dieser Artikel führt Sie durch die Schritte zur Verwendung Azure-Portal. Die Verwendung Azure-Portal wird empfohlen, da sie die Erstellung und Konfiguration der Privates DNS automatisiert. Weitere Informationen finden Sie unter [Private Link Schnellstart Handbücher.](../../private-link/create-private-endpoint-portal.md)

Ein privater Endpunkt kann auf zwei Arten erstellt werden. Mit dem automatischen Genehmigungsflow kann ein Benutzer, der über RBAC-Berechtigungen für die FHIR-Ressource verfügt, einen privaten Endpunkt erstellen, ohne dass eine Genehmigung erforderlich ist. Mit dem manuellen Genehmigungsflow kann ein Benutzer ohne RBAC-Berechtigungen für die FHIR-Ressource die Genehmigung eines privaten Endpunkts bei Besitzern der FHIR-Ressource anfordern.

> [!NOTE]
> Wenn ein genehmigter privater Endpunkt für Azure API for FHIR erstellt wird, wird der öffentliche Datenverkehr automatisch deaktiviert. 

### <a name="auto-approval"></a>Automatische Genehmigung

Stellen Sie sicher, dass die Region für den neuen privaten Endpunkt mit der Region für Ihr virtuelles Netzwerk identisch ist. Die Region für Ihre FHIR-Ressource kann abweichen.

![Registerkarte „Grundlagen“ im Azure-Portal](media/private-link/private-link-portal2.png)

Suchen Sie als Ressourcentyp nach **Microsoft.HealthcareApis/services, und wählen Sie diese Option aus.** Wählen Sie für die Ressource die FHIR-Ressource aus. Wählen Sie als Zielunterressource **FHIR aus.**

![Registerkarte „Ressource“ im Azure-Portal](media/private-link/private-link-portal1.png)

Wenn sie noch nicht über eine Privates DNS Zone verfügen, wählen **Sie (Neu)privatelink.azurehealthcareapis.com.** Wenn Sie Ihre private DNS-Zone bereits konfiguriert haben, können Sie diese in der Liste auswählen. Sie muss das Format **privatelink.azurehealthcareapis.com.**

![Registerkarte „Konfiguration“ im Azure-Portal](media/private-link/private-link-portal3.png)

Nach Abschluss der Bereitstellung können Sie zurück zur Registerkarte **Private** Endpunktverbindungen wechseln, deren Verbindungsstatus Genehmigt ist. 

### <a name="manual-approval"></a>Manuelle Genehmigung

Wählen Sie für die manuelle Genehmigung die zweite Option unter „Ressource“ aus: „Stellen Sie über eine Ressourcen-ID oder einen Alias eine Verbindung mit einer Azure-Ressource her“. Geben Sie für „Zielunterressource“ den Wert „FHIR“ ein, wie bei der automatischen Genehmigung.

![Manuelle Genehmigung](media/private-link/private-link-manual.png)

Nachdem die Bereitstellung abgeschlossen wurde, können Sie zur Registerkarte „Private Endpunktverbindungen“ zurückkehren, auf der Sie Ihre Verbindung „Genehmigen“, „Ablehnen“ oder „Entfernen“ können.

![Optionen](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>Privaten Endpunkt testen

Um sicherzustellen, dass Ihr FHIR-Server nach dem Deaktivieren des Zugriffs auf das öffentliche Netzwerk keinen öffentlichen Datenverkehr empfängt, wählen Sie auf Ihrem Computer den Endpunkt /metadata für Ihren Server aus. Sie sollten einen Fehler „403 Verboten“ erhalten. 


> [!NOTE]
> Es kann bis zu fünf Minuten nach dem Aktualisieren des Zugriffsflags für das öffentliche Netzwerk dauern, bevor öffentlicher Datenverkehr blockiert wird.

So stellen Sie sicher, dass Ihr privater Endpunkt Datenverkehr an Ihren Server senden kann:

1. Erstellen Sie einen virtuellen Computer (VM), der mit dem virtuellen Netzwerk und dem Subnetz verbunden ist, in dem Ihr privater Endpunkt konfiguriert ist. Um sicherzustellen, dass Ihr Datenverkehr von der VM nur das private Netzwerk verwendet, deaktivieren Sie den ausgehenden Internetdatenverkehr mithilfe der NSG-Regel (Netzwerksicherheitsgruppe).
2. Greifen Sie per RDP auf die VM zu.
3. Greifen Sie über den virtuellen Computer auf den Endpunkt /metadata Ihres FHIR-Servers zu. Sie sollten die Capability-Anweisung als Antwort erhalten.

## <a name="manage-private-endpoint"></a>Verwalten eines privaten Endpunkts

### <a name="view"></a>Sicht

Private Endpunkte und der zugehörige Netzwerkschnittstellencontroller (NIC) sind in der Azure-Portal der Ressourcengruppe sichtbar, in der sie erstellt wurden.

![„Ansicht“ in „Ressourcen“](media/private-link/private-link-view.png)

### <a name="delete"></a>Löschen

Private Endpunkte können nur aus der Azure-Portal  auf dem Blatt Übersicht  oder durch Auswählen der Option Entfernen auf der Registerkarte Private Endpunktverbindungen **für Netzwerke gelöscht** werden. Wenn Sie **Entfernen auswählen,** werden der private Endpunkt und die zugeordnete NIC gelöscht. Wenn Sie alle privaten Endpunkte für die FHIR-Ressource und das öffentliche Netzwerk löschen, ist der Zugriff deaktiviert, und es wird keine Anforderung an Ihren FHIR-Server gesendet.

![Löschen eines privaten Endpunkts](media/private-link/private-link-delete.png)
