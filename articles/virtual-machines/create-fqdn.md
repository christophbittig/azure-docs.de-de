---
title: Erstellen eines FQDN für VMs im Azure-Portal
description: Erfahren Sie, wie Sie im Azure-Portal einen vollqualifizierten Domänennamen (FQDN) für eine VM erstellen.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/07/2021
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8567097258305bab622fe8aa46d6a6a95be33d68
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122346659"
---
# <a name="create-a-fully-qualified-domain-name-for-a-vm-in-the-azure-portal"></a>Erstellen eines vollqualifizierten Domänennamens für eine VM im Azure-Portal

Beim Erstellen eines virtuellen Computers (Virtual Machine, VM) im [Azure-Portal](https://portal.azure.com) wird automatisch eine öffentliche IP als Ressource für den virtuellen Computer erstellt. Mit dieser öffentlichen IP-Adresse greifen Sie per Remotezugriff auf den virtuellen Computer zu. Obwohl das Portal standardmäßig keinen [vollqualifizierten Domänennamen](https://en.wikipedia.org/wiki/Fully_qualified_domain_name) (Fully Qualified Domain Name, FQDN) erstellt, können Sie nach der Erstellung des virtuellen Computers einen solchen hinzufügen. Dieser Artikel demonstriert die einzelnen Schritte, um einen DNS-Namen oder einen FQDN zu erstellen. Wenn Sie einen virtuellen Computer ohne öffentliche IP-Adresse erstellen, können Sie keinen FQDN erstellen.

## <a name="create-a-fqdn"></a>Erstellen eines FQDN
In diesem Artikel wird davon ausgegangen, dass Sie bereits einen virtuellen Computer erstellt haben. Bei Bedarf können Sie im Portal eine [Linux](./linux/quick-create-portal.md)- oder [Windows](./windows/quick-create-portal.md)-VM erstellen. Sobald Ihr virtueller Computer eingerichtet ist und ausgeführt wird, gehen Sie folgendermaßen vor:


1. Wählen Sie im Portal Ihren virtuellen Computer aus. 
1. Wählen Sie im Menü auf der linken Seite die Option **Eigenschaften** aus.
1. Wählen Sie unter **Öffentliche IP-Adresse/DNS-Namensbezeichnung** Ihre IP-Adresse aus.
2. Geben Sie unter **DNS-Namensbezeichnung** das Präfix ein, das Sie verwenden möchten.
3. Wählen Sie im oberen Bereich der Seite **Speichern** aus.
4. Wählen Sie im Menü auf der linken Seite **Übersicht** aus, um zum VM-Übersichtsblatt zurückzukehren.
5. Überprüfen Sie, ob der **DNS-Name** richtig angezeigt wird. 

## <a name="next-steps"></a>Nächste Schritte

Sie können auch DNS-Zonen mithilfe von [Azure DNS-Zonen](../dns/dns-getstarted-portal.md) verwalten.

