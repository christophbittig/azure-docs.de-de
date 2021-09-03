---
title: 'Tutorial: Integrieren von Azure Spring Cloud in Azure-Lastenausgleichslösungen'
description: Integrieren von Azure Spring Cloud in Azure-Lastenausgleichslösungen
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 058d8ea63c931f60f27b827874726791f79a31db
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122350036"
---
# <a name="integrate-azure-spring-cloud-with-azure-load-balance-solutions"></a>Integrieren von Azure Spring Cloud in Azure-Lastenausgleichslösungen

**Dieser Artikel gilt für:** ✔️ Java ✔️ C#

Azure Spring Cloud unterstützt Microservices in Azure.  Das Ausweiten von Geschäften kann mehrere Rechenzentren und die Verwaltung mehrerer Instanzen von Azure Spring Cloud erfordern.

Azure bietet bereits verschiedene Lösungen für den Lastenausgleich. Es gibt drei Optionen zum Integrieren von Azure Spring Cloud in Azure-Lastenausgleichslösungen:

1. Integrieren von Azure Spring Cloud mit Azure Traffic Manager
2. Integrieren von Azure Spring Cloud mit Azure App Gateway
3. Integrieren von Azure Spring Cloud mit Azure Front Door

## <a name="prerequisites"></a>Voraussetzungen

* Azure Spring Cloud: [Erstellen eines Azure Spring Cloud-Diensts](./quickstart.md)
* Azure Traffic Manager: [Erstellen einer Traffic Manager-Instanz](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* Azure App Gateway: [Erstellen einer Application Gateway-Instanz](../application-gateway/quick-create-portal.md)
* Azure Front Door: [Erstellen einer Front Door-Instanz](../frontdoor/quickstart-create-front-door.md)

## <a name="integrate-azure-spring-cloud-with-azure-traffic-manager"></a>Integrieren von Azure Spring Cloud mit Azure Traffic Manager

Um Azure Spring Cloud mit Traffic Manager zu integrieren, fügen Sie die öffentlichen Endpunkte als Endpunkte von Traffic Manager hinzu, und konfigurieren Sie dann die benutzerdefinierte Domäne sowohl für Traffic Manager als auch für Azure Spring Cloud.

### <a name="add-endpoint-in-traffic-manager"></a>Hinzufügen eines Endpunkts in Traffic Manager

Hinzufügen von Endpunkten in Traffic Manager:

1. Geben Sie als **Typ** die Option *Externer Endpunkt* an.
1. Geben Sie den vollqualifizierten Domänennamen (FQDN) jedes öffentlichen Azure Spring Cloud-Endpunkts ein.
1. Klicken Sie auf **OK**.

    ![Traffic Manager 1](media/spring-cloud-load-balancers/traffic-manager-1.png) ![Traffic Manager 2](media/spring-cloud-load-balancers/traffic-manager-2.png)

### <a name="configure-custom-domain"></a>Konfigurieren einer benutzerdefinierten Domäne

So schließen Sie die Konfiguration ab

1. Melden Sie sich bei der Website Ihres Domänenanbieters an, und erstellen Sie eine CNAME-Datensatzzuordnung von Ihrer benutzerdefinierten Domäne zum Azure-Standarddomänennamen für Traffic Manager.
1. Befolgen Sie die Anweisungen unter [Hinzufügen einer benutzerdefinierten Domäne zu Azure Spring Cloud](./tutorial-custom-domain.md).
1. Fügen Sie dem App-Dienst für Azure Spring Cloud eine Bindung mit der oben angegebenen benutzerdefinierten Domäne hinzu, und laden Sie dort ein SSL-Zertifikat hoch.

    ![Traffic Manager 3](media/spring-cloud-load-balancers/traffic-manager-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-app-gateway"></a>Integrieren von Azure Spring Cloud mit Azure App Gateway

Schließen Sie für eine Integration mit dem Azure Spring Cloud-Dienst die folgenden Konfigurationen ab:

### <a name="configure-backend-pool"></a>Konfigurieren des Back-End-Pools

1. Geben Sie als **Zieltyp** entweder *IP-Adresse* oder *FQDN* an.
1. Geben Sie Ihre öffentlichen Azure Spring Cloud-Endpunkte ein.

    ![App Gateway 1](media/spring-cloud-load-balancers/app-gateway-1.png)

### <a name="add-custom-probe"></a>Hinzufügen eines benutzerdefinierten Tests

1. Wählen Sie **Integritätstests** und dann **Hinzufügen** aus, um das benutzerdefinierte Dialogfeld **Test** zu öffnen.
1. Der wichtigste Punkt besteht darin, unter **Hostnamen aus Back-End-HTTP-Einstellungen auswählen** die Option *Ja* auszuwählen.

    ![App Gateway 2](media/spring-cloud-load-balancers/app-gateway-2.png)

### <a name="configure-http-setting"></a>Konfigurieren der HTTP-Einstellungen

1. Wählen Sie **HTTP-Einstellungen** und dann **Hinzufügen** aus, um eine HTTP-Einstellung hinzuzufügen.
1. **Mit neuem Hostnamen überschreiben:** Wählen Sie *Ja* aus.
1. **Hostnamen außer Kraft setzen:** Wählen Sie **Hostnamen aus Back-End-Ziel auswählen** aus.
1. **Benutzerdefinierten Test verwenden:** Wählen Sie *Ja* und dann den oben erstellten benutzerdefinierten Test aus.

    ![App Gateway 3](media/spring-cloud-load-balancers/app-gateway-3.png)

### <a name="configure-rewrite-set"></a>Konfigurieren des Umschreibungssatzes

1. Klicken Sie erst auf **Umschreibungen** und anschließend auf **Umschreibungssatz**, um einen Umschreibungssatz hinzuzufügen.
1. Wählen Sie die Routingregeln aus, die Anforderungen an öffentliche Azure Spring Cloud-Endpunkte weiterleiten.
1. Wählen Sie auf der Registerkarte **Umschreibungsregelkonfiguration** die Option **Neuschreibungsregel hinzufügen** aus.
1. **Umschreibungstyp**: Wählen Sie **Anforderungsheader** aus.
1. **Aktionstyp**: Wählen Sie **Löschen** aus.
1. **Headername**: Wählen Sie **Allgemeiner Header** aus.
1. **Allgemeiner Header**: Wählen Sie **X-Forwarded-Proto** aus.

    ![App Gateway 4](media/spring-cloud-load-balancers/app-gateway-4.png)

## <a name="integrate-azure-spring-cloud-with-azure-front-door"></a>Integrieren von Azure Spring Cloud mit Azure Front Door

Um die Integration in den Azure Spring Cloud-Dienst auszuführen und den Back-End-Pool zu konfigurieren, verwenden Sie die folgenden Schritte:

1. **Fügen Sie einen Back-End-Pool hinzu**.
1. Geben Sie den Back-End-Endpunkt an, indem Sie einen Host hinzufügen.

    ![Front Door 1](media/spring-cloud-load-balancers/front-door-1.png)

1. Geben Sie für **Back-End-Hosttyp** die Option *Benutzerdefinierter Host* an.
1. Geben Sie den FQDN Ihrer öffentlichen Azure Spring Cloud-Endpunkte unter **Back-End-Hostname** ein.
1. Akzeptieren Sie den Standardwert für **Back-End-Hostheader**, der dem **Back-End-Hostnamen** entspricht.

    ![Front Door 2](media/spring-cloud-load-balancers/front-door-2.png)

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen einer Traffic Manager-Instanz](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* [Erstellen einer Application Gateway-Instanz](../application-gateway/quick-create-portal.md)
* [Erstellen einer Front Door-Instanz](../frontdoor/quickstart-create-front-door.md)
