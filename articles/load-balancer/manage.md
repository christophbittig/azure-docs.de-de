---
title: Azure Load Balancer – Portaleinstellungen
description: Erfahren Sie mehr über Portaleinstellungen für Azure Load Balancer.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 08/16/2021
ms.author: allensu
ms.openlocfilehash: 1911c3a97892a440d0819025c6f040732d03e6e7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131079546"
---
# <a name="azure-load-balancer-portal-settings"></a>Azure Load Balancer – Portaleinstellungen

Wenn Sie eine Azure Load Balancer-Instanz erstellen, erfahren Sie anhand der Informationen in diesem Artikel mehr über die einzelnen Einstellungen und die richtige Konfiguration.

## <a name="create-load-balancer"></a>Erstellen eines Load Balancers

Azure Load Balancer ist ein Netzwerk-Lastenausgleich, der Datenverkehr über VM-Instanzen im Back-End-Pool verteilt. Um einen Lastenausgleich im Portal zu erstellen, wählen Sie oben auf der Seite das Suchfeld aus. Geben Sie **Lastenausgleich** ein. Wählen Sie in den Suchergebnissen **Lastenausgleichsmodule** aus. Wählen Sie auf der Seite **Lastenausgleich** die Option **+ Erstellen** aus.

### <a name="basics"></a>Grundlagen

Auf der Registerkarte **Grundlagen** der Portalseite zum Erstellen eines Lastenausgleichs sehen Sie folgende Informationen:

| Einstellung |  Details |
| ---------- | ---------- |
| Subscription  | Wählen Sie Ihr Abonnement aus. Diese Auswahl ist das Abonnement, in dem Ihr Lastenausgleich bereitgestellt werden soll. |
| Resource group | Wählen Sie **Neu erstellen** aus, und geben Sie den Namen für Ihre Ressourcengruppe in das Textfeld ein. Wenn Sie bereits über eine Ressourcengruppe verfügen, wählen Sie sie aus. |
| Name | Diese Einstellung ist der Name für Ihre Azure Load Balancer-Instanz. |
| Region | Wählen Sie eine Azure-Region aus, in der Sie Ihren Lastenausgleich bereitstellen möchten. |
| Typ | Es gibt zwei Lastenausgleichstypen: </br> **Intern (privat)** </br> **Öffentlich (extern)** .</br> Ein interner Lastenausgleich (Internal Load Balancer, ILB) leitet Datenverkehr über eine private IP-Adresse an die Back-End-Pool Mitglieder weiter.</br> Ein öffentlicher Lastenausgleich leitet Anforderungen von Clients über das Internet an den Back-End-Pool weiter.</br> Hier erfahren Sie mehr zu [Lastenausgleichstypen](components.md#frontend-ip-configuration-).|
| SKU  | Wählen Sie **Standard** aus. </br> Der Lastenausgleich bietet zwei SKUs: **Basic** und **Standard**. </br> „Basic“ hat eine eingeschränkte Funktionalität. </br> Für Produktionsworkloads wird **Standard** empfohlen. </br> Hier erfahren Sie mehr zu [SKUs](skus.md). |
| Tarif | Es gibt zwei Lastenausgleichstarife: </br> **Regional** </br> **Global** </br> Ein regionaler Lastenausgleich ist auf den Lastenausgleich innerhalb einer Region beschränkt. Global bezieht sich auf einen regionsübergreifenden Lastenausgleich, der einen regionsübergreifenden Lastenausgleich vornimmt. </br> Weitere Informationen zur **globalen** Dienstebene finden Sie unter [Regionsübergreifender Lastenausgleich (Vorschau)](cross-region-overview.md).

:::image type="content" source="./media/manage/create-public-load-balancer-basics.png" alt-text="Ein Screenshot, der „öffentlichen Lastenausgleich erstellen“ zeigt" border="true":::.

### <a name="frontend-ip-configuration"></a>Front-End-IP-Konfiguration

Wählen Sie auf der Portalseite „Lastenausgleich erstellen“ auf der Registerkarte **Frontend-IP-Konfiguration** die Option **+ Frontend-IP-Adresse hinzufügen** aus, um die Erstellungsseite zu öffnen.

:::image type="content" source="./media/manage/create-frontend.png" alt-text="Ein Screenshot, der „Front-End-IP-Konfigurationsseite erstellen“ zeigt" border="true":::.

#### <a name="-add-a-frontend-ip"></a>**+ Front-End-IP-Adresse hinzufügen**
##### <a name="public"></a>Öffentlich

Wenn Sie **Öffentlich** als Typ auswählen, werden die folgenden Informationen angezeigt:

| Einstellung | Details |
| ------- | ------- |
| Name | Der Name des Front-Ends, das dem Lastenausgleich hinzugefügt wird. |
| IP-Version | **IPv4** </br> **IPv6** </br> Lastenausgleich unterstützt Pv4 und IPv6 Frontends. </br> Weitere Informationen finden Sie unter [Load Balancer und IPv6](load-balancer-ipv6-overview.md). |
| IP-Typ | **IP-Adresse** </br> **IP-Präfix** </br> Lastenausgleich unterstützt eine IP-Adresse oder einen IP-Präfix für die Front-End-IP-Adresse. Weitere Informationen finden Sie unter [Präfix für die öffentliche IP-Adresse in Azure](../virtual-network/ip-services/public-ip-address-prefix.md). |

:::image type="content" source="./media/manage/add-frontend-public.png" alt-text="Ein Screenshot, der „Front-End-IP-Konfiguration hinzufügen“ zeigt" border="true":::.

Wenn Sie **IP-Adresse** als **IP-Typ** auswählen, werden Ihnen die folgenden Informationen angezeigt:

| Einstellung | Details |
| ------- | ------- |
| Öffentliche IP-Adresse | Wählen Sie **Neu erstellen** aus, um eine öffentliche IP-Adresse für den öffentlichen Lastenausgleich zu erstellen. </br> Wenn Sie über eine vorhandene öffentliche IP-Adresse verfügen, wählen Sie sie im Dropdownfeld aus. |
| Name | Der Name der öffentlichen IP-Adressressource. |
| SKU | Öffentliche IP-Adressen haben zwei SKUs: **Basic** und **Standard**. </br> „Basic“ unterstützt weder Zonenresilienz noch zonale Attribute. </br> Für Produktionsworkloads wird **Standard** empfohlen. </br> Die SKUs von Lastenausgleich und öffentlicher IP-Adresse **müssen übereinstimmen**. |
| Tarif | **Regional** </br> **Global** </br> Abhängig vom Typ des Lastenausgleichstarifs wird bestimmt, was ausgewählt wird. Regional wird für herkömmlichen und global für regionsübergreifenden Lastenausgleich ausgewählt. |
| Zuordnung | **Statisch** wird automatisch als Standard ausgewählt. </br> Es gibt zwei Typen öffentlicher Basic-IP-Adressen: **Dynamisch** und **Statisch**. </br> Dynamische öffentliche IP-Adressen werden erst nach der Erstellung zugewiesen. </br> IP-Adressen können verloren gehen, wenn die Ressource gelöscht wird. </br> Statische IP-Adressen werden empfohlen. |
| Verfügbarkeitszone | Wählen Sie **Zonenredundant** aus, um einen resilienten Lastenausgleich zu erstellen. </br> Wählen Sie zum Erstellen eines zonalen Lastenausgleichs eine bestimmte Zone aus **1**, **2** oder **3** aus. </br> Standard Load Balancer und öffentliche IP-Adressen unterstützen Zonen. </br> Weitere Informationen zu [Load Balancer und Verfügbarkeitszonen](load-balancer-standard-availability-zones.md) finden Sie hier. </br> Für „Basic“ wird keine Zonenauswahl angezeigt. Von Load Balancer Basic-Instanzen werden keine Zonen unterstützt. |
| Routingpräferenz | Wählen Sie **Microsoft-Netzwerk** aus. </br> Microsoft-Netzwerk bedeutet, dass Datenverkehr über das globale Microsoft-Netzwerk weitergeleitet wird. </br> Internet bedeutet, dass Datenverkehr über das Internetdienstanbieter-Netzwerk weitergeleitet wird. </br> Hier erfahren Sie mehr zu [Routingeinstellungen](../virtual-network/ip-services/routing-preference-overview.md).|

:::image type="content" source="./media/manage/create-public-ip.png" alt-text="Ein Screenshot, der „öffentlichen IP-Adresse erstellen“ zeigt" border="true":::.

Wenn Sie **IP-Präfix** als **IP-Typ** auswählen, werden Ihnen die folgenden Informationen angezeigt:

| Einstellung | Details |
| ------- | ------- |
| Präfix für öffentliche IP-Adressen | Wählen Sie **Neu erstellen** aus, um einen Präfix für öffentliche IP-Adressen für Ihren öffentlichen Lastenausgleich zu erstellen. </br> Wenn Sie über einen vorhandenen öffentlichen Präfix verfügen, wählen Sie ihn im Dropdownfeld aus. |
| Name | Der Name der Präfixressource für öffentliche IP-Adressen. |
| SKU | Präfixe für öffentliche IP-Adressen haben eine SKU **Standard**. |
| IP-Version | **IPv4** oder **IPv6**. </br> Die angezeigte Version entspricht der oben ausgewählten Version. |
| Präfixgröße | IPv4- oder IPv6-Präfixe werden abhängig von der obigen Auswahl angezeigt. </br> **IPv4** </br> /24 (256 Adressen) </br> /25 (128 Adressen) </br> /26 (64 Adressen) </br> /27 (32 Adressen) </br> /28 (16 Adressen) </br> /29 (8 Adressen) </br> /30 (4 Adressen) </br> /31 (2 Adressen) </br> **IPv6** </br> /124 (16 Adressen) </br> /125 (8 Adressen) </br> /126 (4 Adressen) </br> /127 (2 Adressen) |
| Verfügbarkeitszone | Wählen Sie **Zonenredundant** aus, um einen resilienten Lastenausgleich zu erstellen. </br> Wählen Sie zum Erstellen eines zonalen Lastenausgleichs eine bestimmte Zone aus **1**, **2** oder **3** aus. </br> Die Standard Load Balancer und die öffentliche IP-Adressen unterstützen die Zonen. </br> Weitere Informationen zu [Load Balancer und Verfügbarkeitszonen](load-balancer-standard-availability-zones.md) finden Sie hier.

:::image type="content" source="./media/manage/create-public-ip-prefix.png" alt-text="Ein Screenshot, der „Präfix für öffentliche IP-Adressen erstellen“ zeigt" border="true":::.

##### <a name="internal"></a>Intern

Wenn Sie in der Registerkarte **Grundlagen** **Intern** als Ihren Typ auswählen, werden Ihnen die folgenden Informationen angezeigt:

| Einstellung |  Details |
| ---------- | ---------- |
| Virtuelles Netzwerk | Das virtuelle Netzwerk, zu dem Ihr interner Lastenausgleich gehören soll. </br> Die private Front-End-IP-Adresse, die Sie für Ihren internen Lastenausgleich auswählen, kommt aus diesem virtuellen Netzwerk. |
| Subnet | Die Subnetze, die für die IP-Adresse der Front-End-IP verfügbar sind, werden hier angezeigt. |
| Zuweisung | Die Optionen sind **Statisch** oder **Dynamisch**. </br> „Statisch“ stellt sicher, dass die IP-Adresse sich nicht ändert. Eine dynamische IP-Adresse könnte sich ändern. |
| Verfügbarkeitszone | Folgende Optionen sind verfügbar: </br> **Zonenredundant** </br> **Zone 1** </br> **Zone 2** </br> **Zone 3** </br> Wählen Sie zum Erstellen eines Lastenausgleichs mit hoher Verfügbarkeit und Resilienz vor Verfügbarkeitszonenausfällen eine **zonenredundante** IP aus. |

:::image type="content" source="./media/manage/add-frontend-internal.png" alt-text="Ein Screenshot, der „internes Front-End hinzufügen“ zeigt" border="true":::.
### <a name="backend-pools"></a>Back-End-Pools

Wählen Sie auf der Portalseite „Lastenausgleich erstellen“ auf der Registerkarte **Backend-Pools** die Option **+ Back-End-Pool hinzufügen** aus, um die Erstellungsseite zu öffnen.

:::image type="content" source="./media/manage/create-backend-pool.png" alt-text=" Ein Screenshot, der die Registerkarte „Back-End-Pool erstellen“ zeigt" border="true":::.

#### <a name="-add-a-backend-pool"></a>**+ Back-End-Pool hinzufügen**

Auf der Erstellungsseite **+ Back-End-Pool hinzufügen** wird Folgendes angezeigt:

| Einstellung | Details |
| ---------- |  ---------- |
| Name | Der Name des Back-End-Pools. |
| Virtuelles Netzwerk | Das virtuelle Netzwerk, zu dem Ihre Back-End-Instanzen gehören. |
| Konfiguration des Back-End-Pools | Folgende Optionen sind verfügbar: </br> **NIC** </br> **IP-Adresse** </br> Die NIC konfiguriert den Back-End-Pool für die Verwendung der Netzwerkschnittstellenkarte der virtuellen Computer. </br> Die IP-Adresse konfiguriert den Back-End-Pool für die Verwendung der IP-Adresse der virtuellen Computer. </br> Weitere Informationen zur Konfiguration des Back-End-Pools finden Sie unter [Back-End-Poolverwaltung](backend-pool-management.md).
| IP-Version | Ihre Optionen sind **IPv4** oder **IPv6**. |

Sie können dem Back-End-Pool Ihrer Azure Load Balancer-Instanz VMs oder VM-Skalierungsgruppen hinzufügen. Erstellen Sie zuerst die VMs oder VM-Skalierungsgruppen. 

:::image type="content" source="./media/manage/add-backend-pool.png" alt-text="Ein Screenshot, der „Back-End-Pool hinzufügen“ zeigt" border="true":::.

### <a name="inbound-rules"></a>Eingangsregeln

Auf der Registerkarte **Eingehende Regeln**, gibt es zwei Abschnitte die **Lastausgleichsregel** und die **NAT-Regel für eingehenden Datenverkehr**.

Wählen Sie auf der Portalseite „Lastenausgleich erstellen“ auf der Registerkarte **Eingehende Regeln** die Option **+ Lastenausgleichsregel hinzufügen** aus, um die Erstellungsseite zu öffnen.

:::image type="content" source="./media/manage/inbound-rules.png" alt-text=" Ein Screenshot, der „eingehende Regel hinzufügen“ zeigt" border="true":::.

#### <a name="-add-a-load-balancing-rule"></a>**+ Lastenausgleichsregel hinzufügen**

Auf der Erstellungsseite **+ Lastenausgleichsregel hinzufügen** wird Folgendes angezeigt:

| Einstellung | Details |
| ---------- | ---------- |
| Name | Der Name der Lastenausgleichsregel. |
| IP-Version | Ihre Optionen sind **IPv4** oder **IPv6**.  |
| Front-End-IP-Adresse | Wählen Sie die Front-End-IP-Adresse aus. </br> Die Front-End-IP-Adresse Ihres Lastenausgleichs, dem Sie die Lastenausgleichsregel zuordnen möchten.|
| Protocol | Azure Load Balancer ist ein Netzwerk-Lastenausgleich auf Schicht 4. </br> Die Optionen sind wie folgt: **TCP** oder **UDP**. |
| Port | Diese Einstellung ist der Port, der der Front-End-IP-Adresse zugeordnet ist, an die der Datenverkehr auf Basis dieser Lastenausgleichsregel verteilt werden soll. |
| Back-End-Port | Diese Einstellung ist der Port auf den Instanzen im Back-End-Pool, an den der Lastenausgleich Datenverkehr senden soll. Diese Einstellung kann mit dem Front-End-Port identisch sein, oder anders, wenn Sie die Flexibilität für Ihre Anwendung benötigen. |
| Back-End-Pool | Der Back-End-Pool, auf den diese Lastenausgleichsregel angewendet werden soll. |
| Integritätstest | Wählen Sie **Neu erstellen** aus, um einen neuen Test zu erstellen.  </br> Nur fehlerfreie Instanzen empfangen neuen Datenverkehr. |
| Sitzungspersistenz |  Folgende Optionen sind verfügbar: </br> **None** </br> **Client-IP** </br> **Client-IP und Protokoll**</br> </br> Verwalten den Datenverkehr zwischen einem Client und demselben virtuellen Computer im Back-End-Pool. Dieser Datenverkehr wird für die Dauer der Sitzung beibehalten. </br> **Keine** gibt an, dass aufeinander folgende Anforderungen vom selben Client von jedem virtuellen Computer verarbeitet werden können. </br> **Client-IP** gibt an, dass aufeinanderfolgende Anforderungen von derselben Client-IP-Adresse vom selben virtuellen Computer verarbeitet werden. </br> **Client-IP und Protokoll** stellt sicher, dass aufeinanderfolgende Anforderungen von derselben Client-IP-Adresse und demselben Protokoll vom selben virtuellen Computer verarbeitet werden. </br> Erfahren Sie mehr zu [Verteilungsmodi](load-balancer-distribution-mode.md). |
| Leerlaufzeitüberschreitung (Minuten) | Eine **TCP**- oder **HTTP**-Verbindung wird offen gelassen, unabhängig von Clients, die Erhaltungsmeldungen senden. |  
| TCP-Zurücksetzung | Der Lastenausgleich kann **TCP-Zurücksetzungen** senden, damit das Anwendungsverhalten vorhersagbarer wird, wenn sich die Verbindung im Leerlauf befindet. </br> Hier erfahren Sie mehr zur [TCP-Zurücksetzung](load-balancer-tcp-reset.md).|
| Unverankerte IP | Floating IP ist die in Azure verwendete Benennung für die Komponente **Direct Server Return (DSR)** . </br> DSR besteht aus zwei Teilen: <br> 1. Datenflusstopologie </br> 2. Ein IP-Adressen-Zuordnungsschema auf Plattformebene. </br></br> Azure Load Balancer wird immer in einer DSR-Datenflusstopologie betrieben, unabhängig davon, ob Floating IP aktiviert ist. </br> Dieser Vorgang bedeutet, dass der ausgehende Teil eines Datenflusses immer ordnungsgemäß so umgeschrieben wird, dass er direkt wieder an den Ursprung übermittelt wird. </br> Ohne Floating IP wird von Azure ein herkömmliches Zuordnungsschema für IP-Adressen für den Lastenausgleich (IP-Adresse für VM-Instanzen) verfügbar gemacht. </br> Durch die Aktivierung von Floating IP wird die Zuordnung der IP-Adressen in die Front-End-IP-Adresse des Lastenausgleichsmoduls geändert, um für zusätzliche Flexibilität zu sorgen. </br> Weitere Informationen finden Sie unter [Mehrere Front-Ends für Azure Load Balancer](load-balancer-multivip-overview.md).|
| Übersetzung der Quellnetzwerkadresse (SNAT) für ausgehenden Datenverkehr | Folgende Optionen sind verfügbar: </br> **(Empfohlen) Verwenden Sie Ausgangsregeln, um Back-End-Poolmitgliedern den Zugriff auf das Internet zu gewähren**. </br> **Verwenden Sie eine Implizite Ausgangsregel. Diese Einstellung wird nicht empfohlen, weil sie zu einer SNAT-Portauslastung führen kann**. </br> Wählen Sie die Option **Empfohlen** aus, um eine SNAT Portauslastung zu verhindern. Ein **NAT-Gateway** oder **Ausgangsregeln** sind erforderlich, um SNAT für die Backend-Poolmitglieder bereitzustellen. Weitere Informationen zu **NAT Gateway** finden Sie unter [Was ist ein Virtual Network NAT?](../virtual-network/nat-gateway/nat-overview.md). </br> Weitere Informationen zu ausgehenden Verbindungen in Azure finden Sie unter [Verwenden von SNAT für ausgehende Verbindungen](load-balancer-outbound-connections.md). |

:::image type="content" source="./media/manage/add-load-balancing-rule.png" alt-text="Ein Screenshot, der „ Lastenausgleichsregel hinzufügen“ zeigt" border="true":::.

#### <a name="create-health-probe"></a>Erstellen eines Integritätstests

Wenn Sie in der Integritätstestkonfiguration der Lastenausgleichsregel oben **Neu erstellen** ausgewählt haben, werden die folgenden Optionen angezeigt:

| Einstellung | Details |
| ---------- | ---------- |
| Name | Der Name des Integritätstests. |
| Protocol | Das von Ihnen gewählte Protokoll bestimmt den Typ der Überprüfung, mit dem ermittelt wird, ob die Back-End-Instanzen fehlerfrei sind. </br> Folgende Optionen sind verfügbar: </br> **TCP** </br> **HTTPS** </br> **HTTP** </br> Stellen Sie sicher, dass Sie das richtige Protokoll verwenden. Diese Auswahl hängt von der Art der Anwendung ab. </br> Die Konfiguration des Integritätstests und der Testantworten bestimmt, welche Back-End-Poolinstanzen neue Flows empfangen. </br> Anhand von Integritätstests können Sie einen Fehler in einer Anwendung für einen Back-End-Endpunkt erkennen. </br> Hier erfahren Sie mehr zu [Integritätstests](load-balancer-custom-probe-overview.md). |
| Port | Der Zielport für den Integritätstest. </br> Diese Einstellung ist der Port auf der Back-End-Instanz, mit dem der Integritätstest die Integrität der Instanz bestimmt. |
| Intervall | Die Anzahl der Sekunden zwischen Testversuchen. </br> Mit dem Intervall wird bestimmt, wie häufig der Integritätstest versucht, die Back-End-Instanz zu erreichen. </br> Wenn Sie 5 auswählen, wird der zweite Testversuch nach 5 Sekunden durchgeführt usw. |
| Fehlerhafter Schwellenwert | Die Anzahl aufeinander folgender Testfehler, die auftreten müssen, damit ein virtueller Computer als fehlerhaft eingestuft wird.</br> Wenn Sie 2 auswählen, werden nach zwei aufeinanderfolgenden Fehlern keine neuen Flows auf diese Back-End-Instanz festgelegt. |

:::image type="content" source="./media/manage/add-health-probe.png" alt-text="Screenshot, der „Integritätstest hinzufügen“" border="true"::: zeigt.

Wählen Sie auf der Portalseite „Lastenausgleich erstellen“ auf der Registerkarte **Eingehende Regeln** die Option **+ NAT-Regel für eingehenden Datenverkehr hinzufügen** aus, um die Erstellungsseite zu öffnen.

#### <a name="-add-an-inbound-nat-rule"></a>**NAT-Regel für eingehenden Datenverkehr hinzufügen**

Auf der Erstellungsseite **+ NAT-Regel für eingehenden Datenverkehr hinzufügen** wird Folgendes angezeigt:

| Einstellung | Details |
| ---------- | ---------- |
| Name | Der Name Ihrer NAT-Regel für eingehenden Datenverkehr. |
| Front-End-IP-Adresse | Wählen Sie die Front-End-IP-Adresse aus. </br> Die Front-End-IP-Adresse Ihres Lastenausgleichs, dem Sie die NAT-Regel für eingehenden Datenverkehr	zuordnen möchten. |
| IP-Version | Ihre Optionen sind IPv4 und IPv6. |
| Dienst | Der Typ des Diensts, den Sie auf der Azure Load Balancer-Instanz ausführen. </br> Wenn Sie hier eine Auswahl treffen, werden die Portdaten entsprechend aktualisiert. |
| Protocol | Azure Load Balancer ist ein Netzwerk-Lastenausgleich auf Schicht 4. </br> Die Optionen sind wie folgt: TCP oder UDP. |
| Leerlaufzeitüberschreitung (Minuten) | Eine TCP- oder HTTP-Verbindung wird offen gelassen, unabhängig von Clients, die Erhaltungsmeldungen senden. |
| TCP-Zurücksetzung | Der Lastenausgleich kann TCP-Zurücksetzungen senden, damit das Anwendungsverhalten vorhersagbarer wird, wenn sich die Verbindung im Leerlauf befindet. </br> Hier erfahren Sie mehr zur [TCP-Zurücksetzung](load-balancer-tcp-reset.md). |
| Port | Diese Einstellung ist der Port, der der Front-End-IP-Adresse zugeordnet ist, an die der Datenverkehr auf Basis dieser NAT-Regel für eingehenden Datenverkehr verteilt werden soll. |
| Virtueller Zielcomputer | Der VM-Teil des Back-End-Pools, dem diese Regel zugeordnet werden soll. |
| Portzuordnung | Diese Einstellung kann basierend auf der Anwendungseinstellung standardmäßig oder benutzerdefiniert sein. |

:::image type="content" source="./media/manage/add-inbound-nat-rule.png" alt-text=" Ein Screenshot, der „NAT-Regel für eingehenden Datenverkehr hinzufügen“ zeigt" border="true":::.

### <a name="outbound-rules"></a>Ausgangsregeln

Wählen Sie auf der Portalseite „Lastenausgleich erstellen“ auf der Registerkarte **Ausgangsregeln** die Option **+ Ausgangsregel hinzufügen** aus, um die Erstellungsseite zu öffnen.

> [!NOTE]
> Die Registerkarte „Ausgangsegeln“ ist nur für einen öffentlichen Standard Load Balancer gültig. Die Ausgangsregeln für einen internen oder einfachen Lastenausgleich werden nicht unterstützt. Azure Virtual Network NAT wird empfohlen, um ausgehenden Internetzugriff für den Back-End-Pool bereitzustellen. Weitere Informationen zu **Azure Virtual Network NAT** und NAT Gateway-Ressource finden Sie unter **[Was ist Azure Virtual Network NAT?](../virtual-network/nat-gateway/nat-overview.md)** .

:::image type="content" source="./media/manage/create-outbound-rule.png" alt-text="Screenshot, der „Erstellen einer Ausgangsregel“ zeigt" border="true":::.

#### <a name="-add-an-outbound-rule"></a>**+ Ausgangsregel hinzufügen**

Auf der Erstellungsseite **+ Ausgangsregel hinzufügen** wird Folgendes angezeigt:

| Einstellung | Details |
| ------- | ------ |
| Name | Der Name der Regel für ausgehenden Datenverkehr. |
| Front-End-IP-Adresse | Wählen Sie die Front-End-IP-Adresse aus. </br> Die Front-End-IP-Adresse Ihres Lastenausgleichs, dem Sie die Regel für ausgehenden Datenverkehr	zuordnen möchten. |
| Protocol | Azure Load Balancer ist ein Netzwerk-Lastenausgleich auf Schicht 4. </br> Die Optionen sind wie folgt: **Alle**, **TCP** oder **UDP**. |
| Leerlaufzeitüberschreitung (Minuten) | Eine **TCP**- oder **HTTP**-Verbindung wird offen gelassen, unabhängig von Clients, die Erhaltungsmeldungen senden. |
| TCP-Zurücksetzung | Der Lastenausgleich kann **TCP-Zurücksetzungen** senden, damit das Anwendungsverhalten vorhersagbarer wird, wenn sich die Verbindung im Leerlauf befindet. </br> Hier erfahren Sie mehr zur [TCP-Zurücksetzung](load-balancer-tcp-reset.md). |
| Back-End-Pool | Der Back-End-Pool, auf den diese Ausgangsregel angewendet werden soll. |
| **Portzuordnung** |   |
| Portzuordnung | Folgende Optionen stehen zur Auswahl: </br> **Manuelle Auswahl der Anzahl der ausgehenden Ports** </br> **Verwenden der Standardanzahl der ausgehender Ports** </br> Es wird empfohlen, die Standardeinstellung **Manuelle Auswahl der Anzahl der ausgehenden Ports** zu wählen um eine Auslastung der SNAT-Ports zu verhindern. Wenn Sie **Verwenden der Standardanzahl der ausgehender Ports** wählen, ist die Auswahl **ausgehende Ports** deaktiviert. |
| Ausgehende Ports | Folgende Optionen stehen zur Auswahl: </br> **Ports pro Instanz** </br> **Maximale Anzahl von Back-End-Instanzen**. </br> Es wird empfohlen, **Ports pro Instanz** auszuwählen und **10.000** einzugeben. |

:::image type="content" source="./media/manage/add-outbound-rule.png" alt-text=" Ein Screenshot, der „Ausgangsregel hinzufügen“ zeigt" border="true":::.

## <a name="portal-settings"></a>Portaleinstellungen
### <a name="frontend-ip-configuration"></a>Front-End-IP-Konfiguration

Die IP-Adresse Ihres Azure Load Balancers. Sie ist der Kontaktpunkt für Clients. 

Sie können über eine oder mehrere Front-End-IP-Konfigurationen verfügen. Wenn Sie den obigen Abschnitt „Erstellen“ durchlaufen haben, haben Sie bereits ein Frontend für Ihren Lastenausgleich erstellt. 

Wenn Sie Ihrem Lastenausgleich eine Front-End-IP-Konfiguration hinzufügen möchten, navigieren Sie im Azure-Portal zu Ihrem Lastenausgleich, wählen Sie **Front-End-IP-Konfiguration** aus, und wählen Sie dann **+Hinzufügen** aus.

| Einstellung |  Details |
| ---------- | ---------- |
| Name | Der Name Ihrer Front-End-IP-Konfiguration. |
| IP-Version | Die IP-Adressversion, die ihr Front-End haben soll. </br> Load Balancer unterstützt IPv4- und IPv6-Front-End-Konfigurationen. |
| IP-Typ | Der IP-Typ bestimmt, ob mit Ihrem Front-End eine einzelne IP-Adresse oder ein IP-Adressbereich mit einem Präfix für IP-Adressen verknüpft ist. </br> Ein [Präfix für öffentliche IP-Adressen](../virtual-network/ip-services/public-ip-address-prefix.md) ist hilfreich, wenn Sie wiederholt eine Verbindung mit demselben Endpunkt herstellen müssen. Mit dem Präfix wird sichergestellt, dass genügend Ports zur Unterstützung bei SNAT-Portproblemen angegeben werden. |
| Öffentliche IP-Adresse (oder Präfix, wenn Sie oben das Präfix gewählt haben) | Wählen oder erstellen Sie eine neue öffentliche IP-Adresse (oder ein Präfix) für das Lastenausgleichs-Front-End. |

:::image type="content" source="./media/manage/frontend.png" alt-text="Erstellen einer Front-End-IP-Konfigurationsseite." border="true":::

### <a name="backend-pools"></a>Back-End-Pools

Ein Back-End-Adresspool enthält die IP-Adressen der virtuellen Netzwerkschnittstellen im Back-End-Pool. 

Wenn Sie Ihrem Lastenausgleich einen Back-End-Pool hinzufügen möchten, navigieren Sie im Azure-Portal zu Ihrem Lastenausgleich, wählen Sie **Back-End-Pools** aus, und wählen Sie dann **+Hinzufügen** aus.

| Einstellung | Details |
| ---------- |  ---------- |
| Name | Der Name des Back-End-Pools. |
| Virtuelles Netzwerk | Das virtuelle Netzwerk, zu dem Ihre Back-End-Instanzen gehören. |
| Konfiguration des Back-End-Pools | Folgende Optionen sind verfügbar: </br> **NIC** </br> **IP-Adresse** </br> Die NIC konfiguriert den Back-End-Pool für die Verwendung der Netzwerkschnittstellenkarte der virtuellen Computer. </br> Die IP-Adresse konfiguriert den Back-End-Pool für die Verwendung der IP-Adresse der virtuellen Computer. </br> Weitere Informationen zur Konfiguration des Back-End-Pools finden Sie unter [Back-End-Poolverwaltung](backend-pool-management.md). |
| IP-Version | Ihre Optionen sind **IPv4** oder **IPv6**. |

Sie können dem Back-End-Pool Ihrer Azure Load Balancer-Instanz VMs oder VM-Skalierungsgruppen hinzufügen. Erstellen Sie zuerst die VMs oder VM-Skalierungsgruppen. Fügen Sie sie dann im Portal dem Lastenausgleich hinzu.

:::image type="content" source="./media/manage/backend.png" alt-text="Erstellen einer Back-End-Pool-Seite." border="true":::

### <a name="health-probes"></a>Integritätstests

Mit einem Integritätstest wird der Status Ihrer Back-End-VMs oder -Instanzen überwacht. Der Integritätsteststatus bestimmt, wann neue Verbindungen auf der Grundlage von Integritätsprüfungen an eine Instanz gesendet werden. 

Wenn Sie Ihrem Lastenausgleich einen Integritätstest hinzufügen möchten, navigieren Sie im Azure-Portal zu Ihrem Lastenausgleich, wählen Sie **Integritätstests** aus, und wählen Sie dann **+Hinzufügen** aus.

| Einstellung | Details |
| ---------- | ---------- |
| Name | Der Name des Integritätstests. |
| Protocol | Das von Ihnen gewählte Protokoll bestimmt den Typ der Überprüfung, mit dem ermittelt wird, ob die Back-End-Instanzen fehlerfrei sind. </br> Folgende Optionen sind verfügbar: </br> **TCP** </br> **HTTPS** </br> **HTTP** </br> Stellen Sie sicher, dass Sie das richtige Protokoll verwenden. Diese Auswahl hängt von der Art der Anwendung ab. </br> Die Konfiguration des Integritätstests und der Testantworten bestimmt, welche Back-End-Poolinstanzen neue Flows empfangen. </br> Anhand von Integritätstests können Sie einen Fehler in einer Anwendung für einen Back-End-Endpunkt erkennen. </br> Hier erfahren Sie mehr zu [Integritätstests](load-balancer-custom-probe-overview.md). |
| Port | Der Zielport für den Integritätstest. </br> Diese Einstellung ist der Port auf der Back-End-Instanz, mit dem der Integritätstest die Integrität der Instanz bestimmt. |
| Intervall | Die Anzahl der Sekunden zwischen Testversuchen. </br> Mit dem Intervall wird bestimmt, wie häufig der Integritätstest versucht, die Back-End-Instanz zu erreichen. </br> Wenn Sie 5 auswählen, wird der zweite Testversuch nach 5 Sekunden durchgeführt usw. |
| Fehlerhafter Schwellenwert | Die Anzahl aufeinander folgender Testfehler, die auftreten müssen, damit ein virtueller Computer als fehlerhaft eingestuft wird.</br> Wenn Sie 2 auswählen, werden nach zwei aufeinanderfolgenden Fehlern keine neuen Flows auf diese Back-End-Instanz festgelegt. |

:::image type="content" source="./media/manage/health-probe.png" alt-text="Ein Screenshot, der Erstellen eines Integritätstest zeigt" border="true":::.

### <a name="load-balancing-rules"></a>Lastenausgleichsregeln

Definiert, wie eingehender Datenverkehr auf alle Instanzen innerhalb des Back-End-Pools verteilt wird. Eine Lastenausgleichsregel ordnet mehreren Back-End-IP-Adressen und Ports eine bestimmte Front-End-IP-Konfiguration und einen Port zu.

Wenn Sie Ihrem Lastenausgleich eine Lastenausgleichsregel hinzufügen möchten, navigieren Sie im Azure-Portal zu Ihrem Lastenausgleich, wählen Sie **Lastenausgleichsregeln** aus, und wählen Sie dann **+Hinzufügen** aus.
    
| Einstellung | Details |
| ---------- | ---------- |
| Name | Der Name der Lastenausgleichsregel. |
| IP-Version | Ihre Optionen sind **IPv4** oder **IPv6**.  |
| Front-End-IP-Adresse | Wählen Sie die Front-End-IP-Adresse aus. </br> Die Front-End-IP-Adresse Ihres Lastenausgleichs, dem Sie die Lastenausgleichsregel zuordnen möchten.|
| Protocol | Azure Load Balancer ist ein Netzwerk-Lastenausgleich auf Schicht 4. </br> Die Optionen sind wie folgt: **TCP** oder **UDP**. |
| Port | Diese Einstellung ist der Port, der der Front-End-IP-Adresse zugeordnet ist, an die der Datenverkehr auf Basis dieser Lastenausgleichsregel verteilt werden soll. |
| Back-End-Port | Diese Einstellung ist der Port auf den Instanzen im Back-End-Pool, an den der Lastenausgleich Datenverkehr senden soll. Diese Einstellung kann mit dem Front-End-Port identisch sein, oder anders, wenn Sie die Flexibilität für Ihre Anwendung benötigen. |
| Back-End-Pool | Der Back-End-Pool, auf den diese Lastenausgleichsregel angewendet werden soll. |
| Integritätstest | Der Integritätstest, den Sie erstellt haben, um den Integritätsstatus der Instanzen im Back-End-Pool zu ermitteln. </br> Nur fehlerfreie Instanzen empfangen neuen Datenverkehr. |
| Sitzungspersistenz |  Folgende Optionen sind verfügbar: </br> **None** </br> **Client-IP** </br> **Client-IP und Protokoll**</br> </br> Verwalten den Datenverkehr zwischen einem Client und demselben virtuellen Computer im Back-End-Pool. Dieser Datenverkehr wird für die Dauer der Sitzung beibehalten. </br> **Keine** gibt an, dass aufeinander folgende Anforderungen vom selben Client von jedem virtuellen Computer verarbeitet werden können. </br> **Client-IP** gibt an, dass aufeinanderfolgende Anforderungen von derselben Client-IP-Adresse vom selben virtuellen Computer verarbeitet werden. </br> **Client-IP und Protokoll** stellt sicher, dass aufeinanderfolgende Anforderungen von derselben Client-IP-Adresse und demselben Protokoll vom selben virtuellen Computer verarbeitet werden. </br> Erfahren Sie mehr zu [Verteilungsmodi](load-balancer-distribution-mode.md). |
| Leerlaufzeitüberschreitung (Minuten) | Eine **TCP**- oder **HTTP**-Verbindung wird offen gelassen, unabhängig von Clients, die Erhaltungsmeldungen senden. |  
| TCP-Zurücksetzung | Der Lastenausgleich kann **TCP-Zurücksetzungen** senden, damit das Anwendungsverhalten vorhersagbarer wird, wenn sich die Verbindung im Leerlauf befindet. </br> Hier erfahren Sie mehr zur [TCP-Zurücksetzung](load-balancer-tcp-reset.md).|
| Unverankerte IP | Floating IP ist die in Azure verwendete Benennung für die Komponente **Direct Server Return (DSR)** . </br> DSR besteht aus zwei Teilen: <br> 1. Datenflusstopologie </br> 2. Ein IP-Adressen-Zuordnungsschema auf Plattformebene. </br></br> Azure Load Balancer wird immer in einer DSR-Datenflusstopologie betrieben, unabhängig davon, ob Floating IP aktiviert ist. </br> Dieser Vorgang bedeutet, dass der ausgehende Teil eines Datenflusses immer ordnungsgemäß so umgeschrieben wird, dass er direkt wieder an den Ursprung übermittelt wird. </br> Ohne Floating IP wird von Azure ein herkömmliches Zuordnungsschema für IP-Adressen für den Lastenausgleich (IP-Adresse für VM-Instanzen) verfügbar gemacht. </br> Durch die Aktivierung von Floating IP wird die Zuordnung der IP-Adressen in die Front-End-IP-Adresse des Lastenausgleichsmoduls geändert, um für zusätzliche Flexibilität zu sorgen. </br> Weitere Informationen finden Sie unter [Mehrere Front-Ends für Azure Load Balancer](load-balancer-multivip-overview.md).|
| Übersetzung der Quellnetzwerkadresse (SNAT) für ausgehenden Datenverkehr | Folgende Optionen sind verfügbar: </br> **(Empfohlen) Verwenden Sie Ausgangsregeln, um Back-End-Poolmitgliedern den Zugriff auf das Internet zu gewähren**. </br> **Verwenden Sie eine Implizite Ausgangsregel. Diese Einstellung wird nicht empfohlen, weil sie zu einer SNAT-Portauslastung führen kann**. </br> Wählen Sie die Option **Empfohlen** aus, um eine SNAT Portauslastung zu verhindern. Ein **NAT-Gateway** oder **Ausgangsregeln** sind erforderlich, um SNAT für die Backend-Poolmitglieder bereitzustellen. Weitere Informationen zu **NAT Gateway** finden Sie unter [Was ist ein Virtual Network NAT?](../virtual-network/nat-gateway/nat-overview.md). </br> Weitere Informationen zu ausgehenden Verbindungen in Azure finden Sie unter [Verwenden von SNAT für ausgehende Verbindungen](load-balancer-outbound-connections.md). |

:::image type="content" source="./media/manage/load-balancing-rule.png" alt-text="Ein Screenshot, der „Lastenausgleichsregel hinzufügen“ zeigt" border="true":::.

### <a name="inbound-nat-rules"></a>Eingehende NAT-Regeln

Eine NAT-Regel für eingehenden Datenverkehr leitet eingehenden Datenverkehr weiter, der an eine Kombination aus Front-End-IP-Adresse und Port gesendet wird. 

Der Datenverkehr wird an einen bestimmten virtuellen Computer oder eine Instanz im Back-End-Pool gesendet. Der Portweiterleitung liegt die gleiche hashbasierte Verteilung zugrunde wie dem Lastenausgleich.

Wenn Ihr Szenario Remotedesktopprotokoll- oder Secure Shell-Sitzungen (RDP, SSH) zum Trennen von VM-Instanzen innerhalb eines Back-End-Pools erfordert. Ports können mehrere interne Endpunkte unter derselben Front-End-IP-Adresse zugeordnet werden. 

Sie können die Front-End-IP-Adressen verwenden, um für Ihre VMs die Remoteverwaltung ohne zusätzliche Jumpbox durchzuführen.

Wenn Sie Ihrem Lastenausgleich eine NAT-Regel für eingehenden Datenverkehr hinzufügen möchten, navigieren Sie im Azure-Portal zu Ihrem Lastenausgleich, wählen Sie **NAT-Regeln für eingehenden Datenverkehr** aus, und wählen Sie dann **+Hinzufügen** aus.

| Einstellung | Details |
| ---------- | ---------- |
| Name | Der Name Ihrer NAT-Regel für eingehenden Datenverkehr. |
| Front-End-IP-Adresse | Wählen Sie die Front-End-IP-Adresse aus. </br> Die Front-End-IP-Adresse Ihres Lastenausgleichs, dem Sie die NAT-Regel für eingehenden Datenverkehr	zuordnen möchten. |
| IP-Version | Ihre Optionen sind IPv4 und IPv6. |
| Dienst | Der Typ des Diensts, den Sie auf der Azure Load Balancer-Instanz ausführen. </br> Wenn Sie hier eine Auswahl treffen, werden die Portdaten entsprechend aktualisiert. |
| Protocol | Azure Load Balancer ist ein Netzwerk-Lastenausgleich auf Schicht 4. </br> Die Optionen sind wie folgt: TCP oder UDP. |
| Leerlaufzeitüberschreitung (Minuten) | Eine TCP- oder HTTP-Verbindung wird offen gelassen, unabhängig von Clients, die Erhaltungsmeldungen senden. |
| TCP-Zurücksetzung | Der Lastenausgleich kann TCP-Zurücksetzungen senden, damit das Anwendungsverhalten vorhersagbarer wird, wenn sich die Verbindung im Leerlauf befindet. </br> Hier erfahren Sie mehr zur [TCP-Zurücksetzung](load-balancer-tcp-reset.md). |
| Port | Diese Einstellung ist der Port, der der Front-End-IP-Adresse zugeordnet ist, an die der Datenverkehr auf Basis dieser NAT-Regel für eingehenden Datenverkehr verteilt werden soll. |
| Virtueller Zielcomputer | Der VM-Teil des Back-End-Pools, dem diese Regel zugeordnet werden soll. |
| Portzuordnung | Diese Einstellung kann basierend auf der Anwendungseinstellung standardmäßig oder benutzerdefiniert sein. |

:::image type="content" source="./media/manage/inbound-nat-rule.png" alt-text=" Ein Screenshot, der hinzufügen einer NAT-Regel für eingehenden Datenverkehr zeigt" border="true":::.

### <a name="outbound-rules"></a>Ausgangsregeln

Ausgangsregeln für den Lastenausgleich konfigurieren das ausgehende SNAT für virtuelle Computer im Back-End-Pool.

Wenn Sie Ihrem Lastenausgleich eine Regel für ausgehenden Datenverkehr hinzufügen möchten, navigieren Sie im Azure-Portal zu Ihrem Lastenausgleich, wählen Sie **Regeln für ausgehenden Datenverkehr** aus, und wählen Sie dann **+Hinzufügen** aus.

| Einstellung | Details |
| ------- | ------ |
| Name | Der Name der Regel für ausgehenden Datenverkehr. |
| Front-End-IP-Adresse | Wählen Sie die Front-End-IP-Adresse aus. </br> Die Front-End-IP-Adresse Ihres Lastenausgleichs, dem Sie die Regel für ausgehenden Datenverkehr	zuordnen möchten. |
| Protocol | Azure Load Balancer ist ein Netzwerk-Lastenausgleich auf Schicht 4. </br> Die Optionen sind wie folgt: **Alle**, **TCP** oder **UDP**. |
| Leerlaufzeitüberschreitung (Minuten) | Eine **TCP**- oder **HTTP**-Verbindung wird offen gelassen, unabhängig von Clients, die Erhaltungsmeldungen senden. |
| TCP-Zurücksetzung | Der Lastenausgleich kann **TCP-Zurücksetzungen** senden, damit das Anwendungsverhalten vorhersagbarer wird, wenn sich die Verbindung im Leerlauf befindet. </br> Hier erfahren Sie mehr zur [TCP-Zurücksetzung](load-balancer-tcp-reset.md). |
| Back-End-Pool | Der Back-End-Pool, auf den diese Ausgangsregel angewendet werden soll. |

:::image type="content" source="./media/manage/outbound-rule.png" alt-text="Ein Screehshot, der Hinzufügen einer Ausgangsregel" border="true"::: zeigt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie die unterschiedlichen Begriffe und Einstellungen im Azure-Portal für Azure Load Balancer kennengelernt.

* [Erfahren Sie mehr](./load-balancer-overview.md) über Azure Load Balancer.
* [Häufig gestellte Fragen](./load-balancer-faqs.yml) zu Azure Load Balancer.