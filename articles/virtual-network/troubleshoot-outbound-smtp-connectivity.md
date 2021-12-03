---
title: Behandeln von Problemen mit ausgehenden SMTP-Verbindungen in Azure | Microsoft-Dokumentation
description: Lernen Sie die empfohlene Methode zum Versenden von E-Mails kennen und erfahren Sie, wie Sie Probleme mit der ausgehenden SMTP-Konnektivität in Azure beheben können.
services: virtual-network
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/28/2021
ms.author: genli
ms.openlocfilehash: 5c4c129514ccf2fe3b55fc173d3fd4b87fd50715
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/22/2021
ms.locfileid: "130248961"
---
# <a name="troubleshoot-outbound-smtp-connectivity-problems-in-azure"></a>Behandeln von Problemen mit ausgehenden SMTP-Verbindungen in Azure

Ausgehende E-Mails, die von einem virtuellen Computer (virtual machine, VM) über den Port 25 direkt an externe Domänen (z. B. „outlook.com“ oder „gmail.com“) gesendet werden, sind nur möglich, wenn der virtuelle Computer in bestimmten Abonnementtypen bereitgestellt wird.

> [!IMPORTANT]
> Für alle nachstehenden Beispiele gilt der Prozess *nur* für Virtual Machines- und Virtual Machine Scale Sets-Ressourcen (`Microsoft.Compute/virtualMachines` & `Microsoft.Compute/virtualMachineScaleSets`). Das Senden von E-Mails über Port 25 wird von allen Azure PaaS-Ressourcen (Platform-as-a-Service ) nicht unterstützt, einschließlich [Azure App Service](https://azure.microsoft.com/services/app-service) und [Azure Functions](https://azure.microsoft.com/services/functions).

## <a name="recommended-method-of-sending-email"></a>Empfohlene Methode zum Senden von E-Mails

Es wird empfohlen, authentifizierte SMTP-Relaydienste zu verwenden, um E-Mails von Azure-VMs oder von Azure App Service zu senden. (Diese Relaydienste stellen in der Regel eine Verbindung über den TCP-Port 587 her, unterstützen aber auch andere Ports.) Mithilfe dieser Dienste wird die Zuverlässigkeit der IP-Adresse und der Domäne bewahrt, um die Wahrscheinlichkeit zu minimieren, dass Ihre Nachrichten von externen Domänen abgelehnt oder im Spam-Ordner platziert werden. [SendGrid](https://sendgrid.com/partners/azure/) ist ein solcher SMTP-Relaydienst, aber es gibt auch andere. Möglicherweise befindet sich auf Ihren lokalen Servern auch ein authentifizierter SMTP-Relaydienst.

Das Verwenden dieser Dienste für die E-Mail-Zustellung ist in Azure nicht eingeschränkt – unabhängig vom Abonnementtyp.

## <a name="enterprise-agreement"></a>Enterprise Agreement

Bei virtuellen Computern, die in Enterprise Agreement-Abonnements bereitgestellt werden, werden die ausgehenden SMTP-Verbindungen am TCP-Port 25 nicht blockiert. Es gibt jedoch keine Garantie, dass externe Domänen die eingehenden E-Mails der virtuellen Computer akzeptieren. Sollten Ihre E-Mails von den externen Domänen abgelehnt oder gefiltert werden, wenden Sie sich an die E-Mail-Dienstanbieter, um die Probleme zu beheben. Diese Probleme werden vom Azure-Support nicht abgedeckt.

## <a name="pay-as-you-go"></a>Nutzungsbasierte Bezahlung

Die Azure-Plattform blockiert ausgehende SMTP-Verbindungen am TCP-Port 25 für virtuelle Computer, die in Abonnements mit nutzungsbasierter Bezahlung bereitgestellt werden. Diese Blockierung kann aufgehoben werden, wenn mit Ihrem Azure-Abonnement alles in Ordnung ist und es über einen tadellosen Zahlungsverlauf verfügt. Die Aufhebung der Beschränkung kann im [Azure-Portal](https://portal.azure.com) im Abschnitt **E-Mail kann nicht gesendet werden (SMTP-Port 25)** des Blatts **Diagnose und Problembehandlung** für eine Azure Virtual Network-Ressource angefordert werden. 

Nachdem ein Abonnement mit nutzungsbasierter Bezahlung von dieser Blockierung ausgenommen wurde und die virtuellen Computer im Azure-Portal beendet und neu gestartet wurden, sind ab sofort alle virtuellen Computer in diesem Abonnement ausgenommen. Die Ausnahme gilt nur für das angeforderte Abonnement und ausschließlich für den VM-Datenverkehr, der direkt an das Internet weitergeleitet wird.

> [!NOTE]
> Microsoft behält sich das Recht vor, diese Ausnahmeregelungen zu widerrufen, wenn ein Verstoß gegen die Vertragsbedingungen festgestellt wird.

## <a name="msdn-azure-pass-azure-in-open-education-azure-for-students-visual-studio-and-free-trial"></a>MSDN, Azure Pass, Azure in Open, Education, Microsoft Azure for Students, Visual Studio und Testversion

Die Azure-Plattform blockiert ausgehende SMTP-Verbindungen am TCP-Port 25 für virtuelle Computer, die in folgenden Arten von Abonnements bereitgestellt werden:

- MSDN
- Azure Pass
- Azure in Open
- Education
- Azure for Students
- Kostenlose Testversion
- Beliebiges Visual Studio-Abonnement  

Die Einschränkungen sollen Missbrauch verhindern. Anforderungen zum Entfernen dieser Einschränkungen werden nicht erteilt.

Falls Sie diese Abonnementtypen verwenden, sollten Sie einen authentifizierten SMTP-Relaydienst nutzen (wie weiter oben in diesem Artikel beschrieben) oder Ihren Abonnementtyp ändern.

## <a name="cloud-solution-provider"></a>Cloud Solution Provider

Die Azure-Plattform blockiert ausgehende SMTP-Verbindungen am TCP-Port 25 für virtuelle Computer, die in Cloud Solution Provider-Abonnements bereitgestellt werden. Diese Blockierung kann aufgehoben werden. Die Aufhebung der Blockierung kann im Azure-Portal im Abschnitt **E-Mail kann nicht gesendet werden (SMTP-Port 25)** des Blatts **Diagnose und Problembehandlung** für eine Azure Virtual Network-Ressource durch Erstellen einer Supportanfrage angefordert werden.

## <a name="microsoft-partner-network-bizspark-plus-or-azure-sponsorship"></a>Microsoft Partner Network, BizSpark Plus oder Azure Sponsorship

Die Azure-Plattform blockiert ausgehende SMTP-Zustellversuche am TCP-Port 25 für virtuelle Computer, die in folgenden Abonnements bereitgestellt werden:

- Microsoft Partner Network (MPN)
- BizSpark Plus
- Azure Sponsorship

Diese Blockierung kann aufgehoben werden. Die Aufhebung der Blockierung kann im Azure-Portal im Abschnitt **E-Mail kann nicht gesendet werden (SMTP-Port 25)** des Blatts **Diagnose und Problembehandlung** für eine Azure Virtual Network-Ressource durch Erstellen einer Supportanfrage angefordert werden.

Nachdem das Abonnement von dieser Blockierung ausgenommen wurde und die virtuellen Computer beendet und neu gestartet wurden, sind ab sofort alle virtuellen Computer in diesem Abonnement ausgenommen. Die Ausnahme gilt nur für das angeforderte Abonnement und ausschließlich für VM-Datenverkehr, der direkt an das Internet weitergeleitet wird.

> [!NOTE]
> Microsoft behält sich das Recht vor, diese Ausnahmen zu widerrufen, wenn ein Verstoß gegen die Vertragsbedingungen festgestellt wird.

## <a name="changing-subscription-type"></a>Ändern des Abonnementtyps

Wenn Sie Ihren Abonnementtyp von einem Enterprise Agreement in einen anderen Abonnementtyp ändern, können Änderungen an Ihren Bereitstellungen die Blockierung ausgehender SMTP-Verbindungen zur Folge haben. Falls Sie Ihren Abonnementtyp von einem Enterprise Agreement in einen anderen Abonnementtyp ändern möchten und ausgehende SMTP-Verbindungen am TCP-Port 25 benötigen, wenden Sie sich vor dem Ändern des Abonnementtyps an den Support, um die Blockierung Ihres Abonnements aufzuheben.

## <a name="need-help-contact-support"></a>Sie brauchen Hilfe? Support kontaktieren

[Wenden Sie sich an den Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade), falls Sie weitere Hilfe benötigen, um das Problem schnell beheben zu lassen. Verwenden Sie diesen Problemtyp: **Technisch** > **Virtuelles Netzwerk** > **E-Mail kann nicht gesendet werden (SMTP/Port 25)** .
