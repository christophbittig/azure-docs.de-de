---
title: Azure Bastion – Häufig gestellte Fragen | Microsoft-Dokumentation
description: Erfahren Sie mehr über häufig gestellte Fragen zu Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: cherylmc
ms.openlocfilehash: 74a1093f8a084360669c5b436fa56d6ea7aa33d8
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494575"
---
# <a name="azure-bastion-faq"></a>Häufig gestellte Fragen zu Azure Bastion

## <a name="faqs"></a>Häufig gestellte Fragen

### <a name="do-i-need-a-public-ip-on-my-virtual-machine-to-connect-via-azure-bastion"></a><a name="publicip"></a>Benötige ich eine öffentliche IP-Adresse auf meinem virtuellen Computer, um eine Verbindung über Azure Bastion herzustellen?

Nein. Wenn Sie mithilfe von Azure Bastion eine Verbindung mit einem virtuellen Computer herstellen, benötigen Sie auf diesem virtuellen Azure-Computer keine öffentliche IP-Adresse. Der Bastion-Dienst öffnet die RDP-/SSH-Sitzung oder -Verbindung mit Ihrem virtuellen Computer über die private IP des virtuellen Computers in Ihrem virtuellen Netzwerk.

### <a name="is-ipv6-supported"></a>Wird IPv6 unterstützt?

Derzeit wird IPv6 nicht unterstützt. Azure Bastion unterstützt nur IPv4. Dies bedeutet, dass Sie Ihrer Bastion-Ressource nur eine öffentliche IPv4-Adresse zuweisen können und dass Sie Bastion verwenden können, um eine Verbindung mit IPv4-Ziel-VMs herzustellen. Sie können Bastion auch verwenden, um eine Verbindung mit Dual Stack-Ziel-VMs herzustellen, aber Sie können über Azure Bastion nur IPv4-Datenverkehr senden. 

### <a name="can-i-use-azure-bastion-with-azure-private-dns-zones"></a>Kann ich Azure Bastion mit privaten Azure DNS-Zonen verwenden?

Azure Bastion muss mit bestimmten internen Endpunkten kommunizieren können, um erfolgreich eine Verbindung mit Zielressourcen herzustellen. Daher *können* Sie Azure Bastion mit Azure Privates DNS Zones verwenden, solange sich der ausgewählte Zonenname nicht mit der Benennung dieser internen Endpunkte überschneidet. Stellen Sie vor der Bereitstellung Ihrer Azure Bastion-Ressource sicher, dass das virtuelle Hostnetzwerk nicht mit einer privaten DNS-Zone mit der folgenden Angabe im Namen verknüpft ist:
* core.windows.net
* azure.com

Beachten Sie, dass sich der [empfohlene DNS-Zonenname](../private-link/private-endpoint-dns.md#azure-services-dns-zone-configuration) für mehrere Azure-Dienste mit den oben aufgeführten Namen überschneidet, wenn Sie eine in einen privaten Endpunkt integrierte private Azure DNS-Zone verwenden. Die Verwendung von Azure Bastion wird bei diesen Setups *nicht* unterstützt.

Die Verwendung von Azure Bastion wird auch für privaten Azure DNS-Zonen in nationalen Clouds nicht unterstützt.

### <a name="do-i-need-an-rdp-or-ssh-client"></a><a name="rdpssh"></a>Benötige ich einen RDP- oder SSH-Client?

Nein. Sie benötigen keinen RDP- oder SSH-Client, um auf das RDP/SSH Ihres virtuellen Computers über das Azure-Portal zuzugreifen. Verwenden Sie das [Azure-Portal](https://portal.azure.com), um direkt über den Browser RDP-/SSH-Zugriff auf Ihren virtuellen Computer zu erhalten.

### <a name="do-i-need-an-agent-running-in-the-azure-virtual-machine"></a><a name="agent"></a>Muss auf dem virtuellen Azure-Computer ein Agent ausgeführt werden?

Nein. Sie müssen keinen Agent oder eine andere Software in Ihrem Browser oder auf Ihrem virtuellen Azure-Computer installieren. Für den Bastion-Dienst ist kein Agent und keine zusätzliche Software für RDP/SSH erforderlich.

### <a name="what-features-are-supported-in-an-rdp-session"></a><a name="rdpfeaturesupport"></a>Welche Funktionen werden in einer RDP-Sitzung unterstützt?

Derzeit wird nur das Kopieren/Einfügen von Text unterstützt. Features wie das Kopieren von Dateien werden nicht unterstützt. Sie können uns Ihr Feedback zu neuen Features auf der [Azure Bastion-Feedbackseite](https://feedback.azure.com/d365community/forum/8ae9bf04-8326-ec11-b6e6-000d3a4f0789?c=c109f019-8326-ec11-b6e6-000d3a4f0789) mitteilen.

### <a name="does-bastion-hardening-work-with-aadj-vm-extension-joined-vms"></a><a name="aadj"></a>Funktioniert die Bastion-Härtung bei virtuellen Computern, die mit der AADJ VM-Erweiterung verknüpft sind?

Dieses Feature funktioniert nicht bei Computern, die mit der AADJ VM-Erweiterung verknüpft sind, unter Verwendung von Azure AD-Benutzern. Weitere Informationen finden Sie unter [Virtuelle Windows Azure-Computer und Azure AD](../active-directory/devices/howto-vm-sign-in-azure-ad-windows.md#requirements).

### <a name="which-browsers-are-supported"></a><a name="browsers"></a>Welche Browser werden unterstützt?

Der Browser muss HTML 5 unterstützen. Verwenden Sie unter Windows den Microsoft Edge-Browser oder Google Chrome. Verwenden Sie unter macOS Google Chrome. Microsoft Edge Chromium wird auch für Windows und Mac unterstützt.

### <a name="what-is-the-pricing"></a><a name="pricingpage"></a>Wie sieht die Preisgestaltung aus?

Weitere Informationen hierzu finden Sie in der [Preisübersicht](https://aka.ms/BastionHostPricing).

### <a name="where-does-azure-bastion-store-customer-data"></a><a name="data"></a>Wo speichert Azure Bastion Kundendaten?

Azure Bastion speichert Kundendaten in der Region, in der sie bereitgestellt werden, und verschiebt sie nicht aus dieser Region.

### <a name="are-any-roles-required-to-access-a-virtual-machine"></a><a name="roles"></a>Sind für den Zugriff auf einen virtuellen Computer Rollen erforderlich?

Zum Herstellen einer Verbindung sind die folgenden Rollen erforderlich:

* Rolle „Leser“ für den virtuellen Computer
* Rolle „Leser“ für den Netzwerkadapter mit privater IP-Adresse des virtuellen Computers
* Rolle „Leser“ für die Azure Bastion-Ressource
* Rolle „Leser“ im virtuellen Netzwerk des virtuellen Zielcomputers (falls sich die Bastion-Instanz in einem virtuellen Netzwerk mit Peering befindet).

### <a name="does-azure-bastion-require-an-rds-cal-for-administrative-purposes-on-azure-hosted-vms"></a><a name="rdscal"></a>Erfordert Azure Bastion auf virtuellen Computern, die von Azure gehostet werden, eine RDS-CAL für administrative Aufgaben?

Nein. Für den Zugriff auf virtuelle Windows Server-Computer durch Azure Bastion ist keine [RDS-CAL](https://www.microsoft.com/p/windows-server-remote-desktop-services-cal/dg7gmgf0dvsv?activetab=pivot:overviewtab) erforderlich, wenn sich die Verwendung rein auf administrative Aufgaben beschränkt.

### <a name="which-keyboard-layouts-are-supported-during-the-bastion-remote-session"></a><a name="keyboard"></a>Welche Tastaturlayouts werden während der Bastion-Remotesitzung unterstützt?

Azure Bastion unterstützt derzeit die folgenden Tastaturlayouts auf dem virtuellen Computer:
* en-us-qwerty
* en-gb-qwerty
* de-ch-qwertz
* de-de-qwertz
* fr-be-azerty
* fr-fr-azerty
* fr-ch-qwertz
* hu-hu-qwertz
* it-it-qwerty
* ja-jp-qwerty
* pt-br-qwerty
* es-es-qwerty
* es-latam-qwerty
* sv-se-qwerty
* tr-tr-qwerty

Um die richtigen Tastenzuordnungen für Ihre Zielsprache festzulegen, müssen Sie entweder die Sprache auf Ihrem lokalen Computer oder die Sprache innerhalb der Ziel-VM auf „Englisch (USA)“ einstellen. Dies bedeutet, dass die Sprache des lokalen Computers auf „Englisch (USA)“ festgelegt sein muss, während die Sprache der Ziel-VM auf Ihre Zielsprache festgelegt ist (oder umgekehrt). Sie können Ihrem Computer in den Computereinstellungen die Sprache „Englisch (USA)“ hinzufügen.

### <a name="does-azure-bastion-support-timezone-configuration-or-timezone-redirection-for-target-vms"></a><a name="timezone"></a>Unterstützt Azure Bastion die Zeitzonenkonfiguration oder Zeitzonenumleitung für virtuelle Zielcomputer?

Azure Bastion unterstützt derzeit keine Zeitzonenumleitung und ist nicht zeitzonenkonfigurierbar.

### <a name="is-user-defined-routing-udr-supported-on-an-azure-bastion-subnet"></a><a name="udr"></a>Wird benutzerdefiniertes Routing (User-Defined Routing, UDR) in einem Azure Bastion-Subnetz unterstützt?

Nein. UDR wird in einem Azure Bastion-Subnetz nicht unterstützt.

In Szenarien, bei denen Azure Bastion und Azure Firewall bzw. ein virtuelles Netzwerkgerät (Network Virtual Appliance, NVA) im gleichen virtuellen Netzwerk vorhanden sind, müssen Sie den Datenverkehr zwischen einem Azure Bastion-Subnetz und Azure Firewall nicht erzwingen, da die Kommunikation zwischen Azure Bastion und Ihren VMs privat ist. Weitere Informationen finden Sie unter [Zugreifen auf virtuelle Computer hinter Azure Firewall mit Bastion](https://azure.microsoft.com/blog/accessing-virtual-machines-behind-azure-firewall-with-azure-bastion/).

### <a name="can-i-upgrade-from-a-basic-sku-to-a-standard-sku"></a><a name="upgradesku"></a> Kann ich ein Upgrade von einer Basic-SKU auf eine Standard-SKU durchführen?

Ja. Die Schritte finden Sie unter [Upgraden einer SKU](upgrade-sku.md). Weitere Informationen finden Sie im Artikel zu den [Konfigurationseinstellungen](configuration-settings.md#skus).

### <a name="can-i-downgrade-from-a-standard-sku-to-a-basic-sku"></a><a name="downgradesku"></a> Kann ich eine Herabstufung von einer Standard-SKU auf eine Basic-SKU durchführen?

Nein. Eine Herabstufung von einer Standard-SKU auf eine Basic-SKU wird nicht unterstützt. Weitere Informationen finden Sie im Artikel zu den [Konfigurationseinstellungen](configuration-settings.md#skus).

### <a name="can-i-deploy-multiple-azure-resources-in-my-azure-bastion-subnet"></a><a name="subnet"></a> Kann ich mehrere Azure-Ressourcen in meinem Azure Bastion Subnetz bereitstellen?

Nein. Das Azure Bastion Subnetz (*AzureBastionSubnet*) ist nur für die Bereitstellung Ihrer Azure Bastion-Ressource reserviert.

### <a name="why-do-i-get-your-session-has-expired-error-message-before-the-bastion-session-starts"></a><a name="session"></a>Warum erhalte ich vor Beginn der Bastion-Sitzung die Fehlermeldung „Ihre Sitzung ist abgelaufen.“?

Eine Sitzung darf nur über das Azure-Portal initiiert werden. Melden Sie sich beim Azure-Portal an, und starten Sie Ihre Sitzung erneut. Dieser Fehler ist zu erwarten, wenn Sie direkt von einer anderen Browsersitzung oder -registerkarte aus zu der URL navigieren. Er trägt zum Schutz Ihrer Sitzung bei und sorgt dafür, dass nur über das Azure-Portal auf die Sitzung zugegriffen werden kann.

### <a name="how-do-i-handle-deployment-failures"></a><a name="udr"></a>Behandeln von Bereitstellungsfehlern

Überprüfen Sie die Fehlermeldungen, und stellen Sie bei Bedarf eine [Supportanfrage im Azure-Portal](../azure-portal/supportability/how-to-create-azure-support-request.md). Bereitstellungsfehler können durch [Einschränkungen für Azure-Abonnements, Kontingente und sonstige Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md) verursacht werden. Insbesondere können Kunden eine Beschränkung der Anzahl von zulässigen öffentlichen IP-Adressen pro Abonnement feststellen, die zu einem Fehler bei der Azure Bastion-Bereitstellung führen.

### <a name="how-do-i-incorporate-azure-bastion-in-my-disaster-recovery-plan"></a><a name="dr"></a>Wie integriere ich Azure Bastion in meinen Plan zur Notfallwiederherstellung?

Azure Bastion wird innerhalb von VNets oder VNets mit Peering bereitgestellt und ist einer Azure-Region zugeordnet. Sie sind verantwortlich für die Bereitstellung von Azure Bastion in einem VNet an einem Notfallwiederherstellungsstandort. Führen Sie im Falle eines Ausfalls der Azure-Region einen Failovervorgang für Ihre virtuellen Computer in die Notfallwiederherstellungsregion durch. Verwenden Sie dann den Azure-Bastion-Host, der in der Azure-Region bereitgestellt wird, um eine Verbindung mit den virtuellen Computern herzustellen, die dort jetzt bereitgestellt werden.

## <a name="vnet-peering"></a><a name="peering"></a>VNet-Peering

### <a name="can-i-still-deploy-multiple-bastion-hosts-across-peered-virtual-networks"></a>Kann ich weiterhin mehrere Bastionhosts in mittels Peering verbundenen virtuellen Netzwerken bereitstellen?

Ja. Standardmäßig wird Benutzern der Bastionhost angezeigt, der im selben virtuellen Netzwerk bereitgestellt ist, in dem sich die VM befindet. Im Menü **Verbinden** können Benutzer jedoch mehrere Bastionhosts anzeigen, die in den mittels Peering verbundenen Netzwerken erkannt wurden. Sie können den gewünschten Bastionhost auswählen, über den sie eine Verbindung mit der im virtuellen Netzwerk bereitgestellten VM herstellen möchten.

### <a name="if-my-peered-vnets-are-deployed-in-different-subscriptions-will-connectivity-via-bastion-work"></a>Funktionieren Verbindungen über Bastion auch, wenn meine Peering-VNETs in unterschiedlichen Abonnements bereitgestellt wurden?

Ja, die Konnektivität über Bastion funktioniert weiterhin für mittels Peering verbundene VNETs in verschiedenen Abonnements für einen einzelnen Mandanten. Abonnements von zwei unterschiedlichen Mandanten werden nicht unterstützt. Damit Bastion im Dropdownmenü **Verbinden** angezeigt wird, muss der Benutzer unter **Abonnement > Globales Abonnement** die Abonnements auswählen, auf die er Zugriff hat.

:::image type="content" source="./media/bastion-faq/global-subscriptions.png" alt-text="Globaler Abonnementfilter" lightbox="./media/bastion-faq/global-subscriptions.png":::

### <a name="does-bastion-support-connectivity-to-azure-virtual-desktop"></a>Unterstützt Bastion die Konnektivität mit Azure Virtual Desktop?
Nein, die Bastion-Konnektivität mit Azure Virtual Desktop wird nicht unterstützt.

### <a name="i-have-access-to-the-peered-vnet-but-i-cant-see-the-vm-deployed-there"></a>Ich habe Zugriff auf das Peering-VNET, sehe aber die dort bereitgestellte VM nicht.

Vergewissern Sie sich, dass der Benutzer über **Lesezugriff** auf die VM und das mittels Peering verbundene VNET verfügt. Überprüfen Sie außerdem unter IAM, ob der Benutzer **Lesezugriff** auf folgende Ressourcen hat:

* Rolle „Leser“ für den virtuellen Computer
* Rolle „Leser“ für den Netzwerkadapter mit privater IP-Adresse des virtuellen Computers
* Rolle „Leser“ für die Azure Bastion-Ressource
* Rolle „Leser“ im virtuellen Netzwerk (nicht erforderlich, wenn kein mittels Peering verbundenes virtuelles Netzwerk vorhanden ist)

|Berechtigungen|BESCHREIBUNG|Berechtigungstyp|
|---|---| ---|
|Microsoft.Network/bastionHosts/read |Ruft einen Bastionhost ab.|Aktion|
|Microsoft.Network/virtualNetworks/BastionHosts/action |Ruft Bastionhostverweise in einem virtuellen Netzwerk ab.|Aktion|
|Microsoft.Network/virtualNetworks/bastionHosts/default/action|Ruft Bastionhostverweise in einem virtuellen Netzwerk ab.|Aktion|
|Microsoft.Network/networkInterfaces/read|Ruft eine Netzwerkschnittstellendefinition ab.|Aktion|
|Microsoft.Network/networkInterfaces/ipconfigurations/read|Ruft eine IP-Konfigurationsdefinition für Netzwerkschnittstellen ab.|Aktion|
|Microsoft.Network/virtualNetworks/read|Dient zum Abrufen der Definition des virtuellen Netzwerks.|Aktion|
|Microsoft.Network/virtualNetworks/subnets/virtualMachines/read|Ruft Verweise auf alle virtuellen Computer im Subnetz eines virtuellen Netzwerks ab.|Aktion|
|Microsoft.Network/virtualNetworks/virtualMachines/read|Ruft Verweise auf alle virtuellen Computer in einem virtuellen Netzwerk ab.|Aktion|

### <a name="what-is-the-maximum-screen-resolution-supported-via-bastion"></a>Wie hoch ist die maximale Bildschirmauflösung, die von Bastion unterstützt wird?  
Derzeit ist 1920x1080 (1080p) die maximal unterstützte Auflösung. 
