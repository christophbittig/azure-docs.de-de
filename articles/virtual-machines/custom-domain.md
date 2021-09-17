---
title: Erstellen und Verwenden einer benutzerdefinierten Domäne
description: Verbinden einer benutzerdefinierten Domäne mit einer VM in Azure
author: mimckitt
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/01/2021
ms.author: jamesser
ms.reviewer: cynthn
ms.openlocfilehash: 5bd9adb8354c3919a63e3de2e850513641af12a3
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696548"
---
# <a name="add-custom-domain-to-azure-vm-or-resource"></a>Hinzufügen einer benutzerdefinierten Domäne zu einer Azure-VM oder -Ressource

**Gilt für**: :heavy_check_mark: Linux-VMs :heavy_check_mark: Windows-VMs :heavy_check_mark: Flexible Skalierungsgruppen


In Azure gibt es mehrere Möglichkeiten, um eine benutzerdefinierte Domäne mit Ihrer VM oder Ressource zu verbinden. Für Ressourcen mit einer öffentlichen IP-Adresse (VM, Lastenausgleich, Anwendungsgateway) besteht die einfachste Möglichkeit darin, einen A-Eintrag in der entsprechenden Domänenregistrierungsstelle zu erstellen. 

## <a name="prerequisites"></a>Voraussetzungen 
- Sie benötigen eine VM, auf der ein Webserver ausgeführt wird. Sie können den Schritten im [Schnellstart](./linux/quick-create-cli.md) folgen, um eine VM zu erstellen und NGINX hinzuzufügen.

- Die VM muss über das Internet zugänglich sein (Port 80 oder 443 geöffnet). Wenn Sie eine sicherere Bereitstellung wünschen, platzieren Sie Ihre VM zuerst hinter einem Lastenausgleich oder Anwendungsgateway. Weitere Informationen finden Sie unter [Schnellstart: Erstellen eines Lastenausgleichs](../load-balancer/quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard).

- Sie benötigen eine vorhandene Domäne und Zugriff auf DNS-Einstellungen. Weitere Informationen finden Sie unter [Kaufen eines benutzerdefinierten Domänennamens für Azure App Service](../app-service/manage-custom-dns-buy-domain.md).


## <a name="add-custom-domain-to-vm-public-ip-address"></a>Hinzufügen einer benutzerdefinierten Domäne zur öffentlichen IP-Adresse der VM

Wenn Sie eine VM im Azure-Portal erstellen, wird automatisch eine öffentliche IP-Ressource für die VM erstellt. Ihre öffentliche IP-Adresse wird auf der Übersichtsseite der VM angezeigt. 
 
:::image type="content" source="media/custom-domain/essentials.png" alt-text="Öffentliche IP-Adresse im Abschnitt „Essentials“ der Übersichtsseite der VM":::

Wenn Sie die IP-Adresse auswählen, werden weitere Informationen dazu angezeigt. Vergewissern Sie sich, dass **IP-Adresszuweisung** auf **Statisch** festgelegt ist. Eine statische IP-Adresse ändert sich nicht, wenn die VM oder die Ressource neu gestartet oder heruntergefahren wird.

:::image type="content" source="media/custom-domain/ip-config.png" alt-text="Konfiguration der öffentlichen IP-Adresse, in der Sie überprüfen können, ob die IP-Adresse statisch ist":::

Wenn Ihre IP-Adresse nicht statisch ist, müssen Sie einen vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) erstellen. 

1. Wählen Sie im Portal Ihren virtuellen Computer aus. 
1. Wählen Sie im Menü auf der linken Seite die Option **Eigenschaften** aus.
1. Wählen Sie unter **Öffentliche IP-Adresse/DNS-Namensbezeichnung** Ihre IP-Adresse aus.
2. Geben Sie unter **DNS-Namensbezeichnung** das Präfix ein, das Sie verwenden möchten.
3. Wählen Sie im oberen Bereich der Seite **Speichern** aus.
4. Wählen Sie im Menü auf der linken Seite **Übersicht** aus, um zum VM-Übersichtsblatt zurückzukehren.
5. Überprüfen Sie, ob der *DNS-Name* richtig angezeigt wird. 

Öffnen Sie einen Browser, geben Sie Ihre IP-Adresse oder Ihren FQDN ein, und vergewissern Sie sich, dass der auf Ihrer VM ausgeführte Webinhalt angezeigt wird.
 
Wechseln Sie nach dem Überprüfen der statischen IP-Adresse oder des FQDN zu Ihrem Domänenanbieter, und navigieren Sie zu „DNS-Einstellungen“.

Fügen Sie dort einen *A-Eintrag* hinzu, der auf Ihre öffentliche IP-Adresse bzw. Ihren FQDN verweist. Für die Domänenregistrierungsstelle GoDaddy gilt beispielsweise folgende Vorgehensweise:
1. Melden Sie sich an, und wählen Sie die gewünschte benutzerdefinierte Domäne aus.
2. Wählen Sie im Abschnitt **Domänen** die Option **Alle verwalten** und dann **DNS | Manage Zones** (Zonen verwalten) aus.
3. Geben Sie unter **Domänenname** Ihre benutzerdefinierte Domäne ein, und wählen Sie anschließend **Auswählen**.
4. Wählen Sie auf der Seite „DNS-Verwaltung“ die Option „Hinzufügen“ und dann in der Liste „Typ“ den Eintrag „A“ aus.
5. Füllen Sie die Felder für den A-Eintrag aus:
    - Typ: Lassen Sie die Auswahl von **A** unverändert.
    - Host: Geben Sie **@** ein.
    - Verweist auf: Geben Sie die öffentliche IP-Adresse oder den FQDN Ihrer VM ein. 
    - TTL: Lassen Sie die Auswahl von „Eine Stunde“ unverändert.
6. Wählen Sie **Speichern**.

Der A-Eintrag wird der Tabelle mit den DNS-Einträgen hinzugefügt.
 
Die DNS-Verteilung nach dem Erstellen des Eintrags dauert in der Regel etwa eine Stunde, mitunter kann sie aber bis zu 48 Stunden in Anspruch nehmen. 


 
## <a name="next-steps"></a>Nächste Schritte
[Übersicht über TLS-Beendigung und End-to-End-TLS mit Application Gateway](../application-gateway/ssl-overview.md)

