---
title: Integrieren in Application Gateway
description: In dieser ausführlichen Anleitung erfahren Sie, wie Sie eine App in Ihrer ILB-App Service-Umgebung mit einem Application Gateway integrieren.
author: madsd
ms.assetid: a6a74f17-bb57-40dd-8113-a20b50ba3050
ms.topic: article
ms.date: 10/12/2021
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 77e8ec3db7b66fe0c639bfd56942b171b9fc2129
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2021
ms.locfileid: "132294274"
---
# <a name="integrate-your-ilb-app-service-environment-with-the-azure-application-gateway"></a>Integrieren Ihrer ILB-App Service-Umgebung in ein Azure Application Gateway

Die [Azure App Service-Umgebung][AppServiceEnvironmentoverview] ist eine Bereitstellung von Azure App Service in einem Subnetz im virtuellen Azure-Netzwerk eines Kunden. Sie kann für den App-Zugriff über einen externen oder internen Endpunkt bereitgestellt werden. Die Bereitstellung der App Service-Umgebung mit einem internen Endpunkt wird als ILB-ASE (App Service-Umgebung mit internem Lastenausgleich) bezeichnet.

Web Application Firewalls tragen zum Schutz Ihrer Webanwendungen bei, indem sie eingehenden Webdatenverkehr untersuchen und die Einschleusung von SQL-Befehlen, websiteübergreifendes Scripting, das Hochladen von Schadsoftware sowie DDoS-Angriffe und andere Angriffe unterbinden. Sie können ein WAF-Gerät aus Azure-Marketplace oder das [Azure Application Gateway][appgw] verwenden.

Das Azure Application Gateway ist eine virtuelle Anwendung, die Lastenausgleich in Schicht 7, TLS/SSL-Abladung und WAF-Schutz (Web Application Firewall) bietet. Es kann eine öffentliche IP-Adresse überwachen und Datenverkehr an Ihren Anwendungsendpunkt routen. In den folgenden Informationen wird die Vorgehensweise beim Integrieren eines WAF-konfigurierten Anwendungsgateways mit einer App in einer ILB-App Service-Umgebung beschrieben.  

Die Integration des Anwendungsgateways mit der ILB-App Service-Umgebung erfolgt auf App-Ebene. Wenn Sie das Anwendungsgateway mit der ILB-App Service-Umgebung konfigurieren, erfolgt dies für bestimmte Apps in Ihrer ILB-App Service-Umgebung. Dieses Verfahren ermöglicht es, sichere mehrinstanzenfähige Anwendungen in einer einzelnen ILB-App Service-Umgebung zu hosten.  

:::image type="content" source="./media/integrate-with-application-gateway/appgw-highlevel.png" alt-text="Screenshot des Diagramms allgemeinen Integrationsdiagramms":::

In dieser exemplarischen Vorgehensweise führen Sie folgende Aktionen aus:

* Erstellen eines Azure Application Gateways.
* Konfigurieren des Anwendungsgateways zum Verweisen auf eine App in Ihrer ILB-App Service-Umgebung
* Bearbeiten des öffentlichen DNS-Hostnamens, der auf Ihr Application Gateway verweist

## <a name="prerequisites"></a>Voraussetzungen

Sie benötigen Folgendes, um Ihr Anwendungsgateway in die ILB-App Service-Umgebung zu integrieren:

* Eine ILB-App Service-Umgebung
* Eine private DNS-Zone für die ILB-App Service Umgebung
* Eine App, die in der ILB-App Service-Umgebung ausgeführt wird
* Einen öffentlichen DNS-Namen, über den später auf Ihr Anwendungsgateway verwiesen wird
* Wenn Sie TLS/SSL-Verschlüsselung für das Anwendungsgateway verwenden müssen, benötigen Sie ein gültiges öffentliches Zertifikat, das zum Binden an Ihr Anwendungsgateway verwendet wird.

### <a name="ilb-app-service-environment"></a>Eine ILB-App Service-Umgebung

Ausführliche Informationen zum Erstellen einer ILB-App Service-Umgebung finden Sie unter [Erstellen einer ASE über das Azure-Portal][creation] und [Erstellen einer ASE mit ARM][createfromtemplate].

* Nachdem die ILB-ASE erstellt wurde, lautet die Standarddomäne `<YourAseName>.appserviceenvironment.net`.

    :::image type="content" source="./media/integrate-with-application-gateway/ilb-ase.png" alt-text="Screenshot der Übersicht über die ILB-ASE":::

* Ein interner Lastenausgleich (ILB) wird für den eingehenden Zugriff bereitgestellt. Sie können die eingehende Adresse in den IP-Adressen unter den ASE-Einstellungen überprüfen. Sie können später eine private DNS-Zone erstellen, die dieser IP-Adresse zugeordnet wird.

    :::image type="content" source="./media/integrate-with-application-gateway/ip-addresses.png" alt-text="Screenshot des Abrufens der eingehenden Adresse aus den IP-Adresseneinstellungen der ILB-ASE":::

### <a name="a-private-dns-zone"></a>Eine private DNS-Zone

Sie benötigen eine [private DNS-Zone][privatednszone] für die interne Namensauflösung. Erstellen Sie diese mithilfe des ASE-Namens und der in der folgenden Tabelle gezeigten Datensätze (Anweisungen finden Sie im [Schnellstart: Erstellen einer privaten Azure DNS-Zone über das Azure-Portal][createprivatednszone]).

| Name  | type | Wert               |
| ----- | ---- | ------------------- |
| *     | Ein    | Eingangsadresse der ASE |
| @     | A    | Eingangsadresse der ASE |
| @     | SOA  | DNS-Name der ASE        |
| *.scm | A    | Eingangsadresse der ASE |

### <a name="app-service-on-ilb-ase"></a>App Service in der ILB-ASE

Sie müssen einen App Service-Plan und eine App in Ihrer ILB-ASE erstellen. Wählen Sie beim Erstellen der App im Portal Ihre ILB-ASE als **Region** aus.

### <a name="a-public-dns-name-to-the-application-gateway"></a>Ein öffentlicher DNS-Name für das Anwendungsgateway

Um über das Internet eine Verbindung mit dem Anwendungsgateway herzustellen, benötigen Sie einen routingfähigen Domänennamen. In diesem Fall wurde der routingfähige Domänenname `asabuludemo.com` verwendet, und es ist geplant, eine Verbindung mit einer App Service-Instanz mit dem Domänennamen `app.asabuludemo.com` herzustellen. Die IP-Adressen, die diesem App-Domänennamen zugeordnet sind, müssen nach dem Erstellen des Anwendungsgateways auf die öffentliche IP-Adresse festgelegt werden.
Wenn dem Anwendungsgateway eine öffentliche Domäne zugeordnet ist, müssen Sie in App Service keine benutzerdefinierte Domäne konfigurieren. Sie können mit [App Service-Domänen](../manage-custom-dns-buy-domain.md#buy-an-app-service-domain) einen benutzerdefinierten Domänennamen erwerben. 

### <a name="a-valid-public-certificate"></a>Ein gültiges öffentliches Zertifikat

Zur Verbesserung der Sicherheit wird empfohlen, ein TLS/SSL-Zertifikat für die Sitzungsverschlüsselung zu binden. Zum Binden eines TLS/SSL-Zertifikats an das Anwendungsgateway ist ein gültiges öffentliches Zertifikat mit den folgenden Informationen erforderlich. Sie können mit [App Service Certificate](../configure-ssl-certificate.md#start-certificate-order) ein TLS/SSL-Zertifikat erwerben und im PFX-Format exportieren.

| Name  | Wert               | BESCHREIBUNG|
| ----- | ------------------- |------------|
| **Allgemeiner Name** |`<yourappname>.<yourdomainname>`, z. B.: `app.asabuludemo.com`  <br/> oder `*.<yourdomainname>`, z. B.: `*.asabuludemo.com` | Ein Standardzertifikat oder ein [Platzhalterzertifikat](https://wikipedia.org/wiki/Wildcard_certificate) für das Anwendungsgateway|
| **Alternativer Antragstellername** | `<yourappname>.scm.<yourdomainname>`, z. B.: `app.scm.asabuludemo.com`  <br/>oder `*.scm.<yourdomainname>`, z. B.: `*.scm.asabuludemo.com` |Das SAN, das das Herstellen einer Verbindung mit App Service-Kudu-Dienst ermöglicht. Dies ist eine optionale Einstellung, wenn Sie den App Service-Kudu-Dienst nicht im Internet veröffentlichen möchten.|

Die Zertifikatsdatei sollte einen privaten Schlüssel aufweisen und im PFX-Format gespeichert werden. Sie wird später in das Anwendungsgateway importiert.

## <a name="create-an-application-gateway"></a>Erstellen eines Anwendungsgateways

Informationen zur grundlegenden Erstellung des Anwendungsgateways finden Sie im [Tutorial: Erstellen eines Anwendungsgateways mit Web Application Firewall über das Azure-Portal][Tutorial: Create an application gateway with a Web Application Firewall using the Azure portal].

In diesem Tutorial wird das Azure-Portal verwendet, um ein Anwendungsgateway mit einer ILB-App Service-Umgebung zu erstellen.

Wählen Sie im Azure-Portal **Neu** > **Netzwerk** > **Anwendungsgateway** aus, um ein Anwendungsgateway zu erstellen.

1. Grundlegende Einstellungen

    In der Dropdownliste **Dienstebene** können Sie **Standard V2** oder **WAF V2** auswählen, um das **WAF**-Feature auf dem Anwendungsgateway zu aktivieren. 

2. Front-End-Einstellung

    Wählen Sie als Front-End-IP-Adresstyp eine der Optionen **Öffentlich**, **Privat** oder **Beide** aus. Wenn Sie **Privat** oder **Beide** festlegen, müssen Sie eine statische IP-Adresse im Subnetzbereich des Anwendungsgateways zuweisen. In diesem Fall wird nur für den öffentlichen Endpunkt eine öffentliche IP-Adresse festgelegt.
    
    * Öffentliche IP-Adresse: Sie müssen eine öffentliche IP-Adresse für den öffentlichen Zugriff auf das Anwendungsgateway zuordnen. Notieren Sie sich diese IP-Adresse. Sie müssen Ihrem DNS-Dienst später einen Eintrag hinzufügen.
    
        :::image type="content" source="./media/integrate-with-application-gateway/frontends.png" alt-text="Screenshot vom Abruf der öffentlichen IP-Adresse aus der Front-End-Einstellung des Anwendungsgateways":::

3. Back-End-Einstellung

    Geben Sie einen Back-End-Poolnamen ein, und wählen Sie als **Zieltyp** die Option **App Services** oder **IP-Adresse oder FQDN** aus. In diesem Fall legen wird **App Services** festgelegt und der App Service-Name aus der Dropdownliste für das Ziel ausgewählt.

    :::image type="content" source="./media/integrate-with-application-gateway/add-backend-pool.png" alt-text="Screenshot des Hinzufügens eines Back-End-Poolnamens in der Back-End-Einstellung":::

4. Konfigurationseinstellung

    Unter der Einstellung **Konfiguration** müssen Sie eine Routingregel hinzufügen, indem Sie auf das Symbol **Routingregel hinzufügen** klicken.

    :::image type="content" source="./media/integrate-with-application-gateway/configuration.png" alt-text="Screenshot des Hinzufügens einer Routingregel in der Konfigurationseinstellung":::

    Sie müssen in einer Routingregel einen **Listener** und **Back-End-Ziele** konfigurieren. Sie können einen HTTP-Listener für die Bereitstellung des Proof of Concept (Machbarkeitsnachweis) oder einen HTTPS-Listener zur Steigerung der Sicherheit hinzufügen.

    * Um für Verbindungen mit dem Anwendungsgateway HTTP zu verwenden, können Sie einen Listener mit den folgenden Einstellungen erstellen:
    
        | Parameter      | Wert                             | BESCHREIBUNG                                                  |
        | -------------- | --------------------------------- | ------------------------------------------------------------ |
        | Regelname      | Beispiel: `http-routingrule`    | Routingname                                                 |
        | Name des Listeners  | Beispiel: `http-listener`       | Name des Listeners                                                |
        | Front-End-IP    | Öffentlich                            | Legen Sie für Internetzugriff „Öffentlich“ fest.                           |
        | Protocol       | HTTP                             | Verwenden Sie keine TLS/SSL-Verschlüsselung.                                       |
        | Port           | 80                               | Standard-HTTP-Port                                           |
        | Listenertyp  | Mehrere Standorte                        | Lassen Sie das Lauschen an mehreren Standorten auf dem Anwendungsgateway zu.           |
        | Hosttyp      | Mehrere/Platzhalter                 | Die Festlegung auf mehrere Websitenamen oder Platzhalter wird empfohlen, wenn der Listenertyp auf mehrere Standorte festgelegt ist. |
        | Hostname      | Beispiel: `app.asabuludemo.com` | Legen Sie für App Service einen routingfähigen Domänennamen fest.              |
        
        :::image type="content" source="./media/integrate-with-application-gateway/http-routing-rule.png" alt-text="Screenshot des HTTP-Listeners der Routingregel für das Anwendungsgateway":::
    
    * Um für Verbindungen mit dem Anwendungsgateway die TLS/SSL-Verschlüsselung zu verwenden, können Sie einen Listener mit den folgenden Einstellungen erstellen:
    
        | Parameter      | Wert                             | BESCHREIBUNG                                                  |
        | -------------- | --------------------------------- | ------------------------------------------------------------ |
        | Regelname      | Beispiel: `https-routingrule`    | Routingname                                                 |
        | Name des Listeners  | Beispiel: `https-listener`       | Name des Listeners                                                |
        | Front-End-IP    | Öffentlich                            | Legen Sie für Internetzugriff „Öffentlich“ fest.                           |
        | Protocol       | HTTPS                             | Verwenden Sie TLS/SSL-Verschlüsselung.                                       |
        | Port           | 443                               | Standard-HTTPS-Port                                           |
        | HTTPS-Einstellungen | Hochladen eines Zertifikats              | Laden Sie ein Zertifikat mit dem CN und dem privaten Schlüssel im PFX-Format hoch. |
        | Listenertyp  | Mehrere Standorte                        | Lassen Sie das Lauschen an mehreren Standorten auf dem Anwendungsgateway zu.           |
        | Hosttyp      | Mehrere/Platzhalter                 | Die Festlegung auf mehrere Websitenamen oder Platzhalter wird empfohlen, wenn der Listenertyp auf mehrere Standorte festgelegt ist. |
        | Hostname      | Beispiel: `app.asabuludemo.com` | Legen Sie für App Service einen routingfähigen Domänennamen fest.              |
        
        :::image type="content" source="./media/integrate-with-application-gateway/https-routing-rule.png" alt-text="HTTPS-Listener der Routingregel für das Anwendungsgateway":::
    
    * Sie müssen den **Back-End-Pool** und die **HTTP-Einstellungen** in den **Back-End-Zielen** konfigurieren. Der Back-End-Pool wurde in den vorherigen Schritten konfiguriert. Klicken Sie auf **Neuen hinzufügen**, um eine HTTP-Einstellung hinzuzufügen.
    
        :::image type="content" source="./media/integrate-with-application-gateway/add-new-http-setting.png" alt-text="Screenshot des Hinzufügens eines neuen Links, um eine HTTP-Einstellung hinzufügen":::
    
    * Im Folgenden sind die HTTP-Einstellungen aufgeführt:
    
        | Parameter                     | Wert                                                        | BESCHREIBUNG                                                  |
        | ----------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
        | Name der HTTP-Einstellung             | Beispiel: `https-setting`                                   | Name der HTTP-Einstellung                                            |
        | Back-End-Protokoll              | HTTPS                                                        | Verwenden Sie TLS/SSL-Verschlüsselung.                                       |
        | Back-End-Port                  | 443                                                          | Standard-HTTPS-Port                                           |
        | Zertifikat einer bekannten Zertifizierungsstelle verwenden | Ja                                                          | Der Standarddomänenname der ILB-ASE lautet `.appserviceenvironment.net` und das Zertifikat dieser Domäne wird von einer öffentlichen vertrauenswürdigen Stammzertifizierungsstelle ausgestellt. In der Einstellung „Vertrauenswürdiges Stammzertifikat“ können Sie festlegen, dass ein **bekanntes vertrauenswürdiges Zertifizierungsstellen-Stammzertifikat** verwendet wird. |
        | Mit neuem Hostnamen überschreiben   | Ja                                                          | Der Hostnamenheader wird beim Herstellen einer Verbindung mit der App in der ILB-ASE überschrieben. |
        | Hostnamen außer Kraft setzen            | Hostnamen aus Back-End-Ziel auswählen | Wenn Sie den Back-End-Pool auf App Service festlegen, können Sie den Host am Back-End-Ziel auswählen. |
        | Benutzerdefinierte Tests erstellen | Nein | Verwenden Sie den Standardintegritätstest.|
        
        :::image type="content" source="./media/integrate-with-application-gateway/https-setting.png" alt-text="Screenshot des Dialogfelds **H T T P-Einstellung hinzufügen**":::


## <a name="configure-an-application-gateway-integration-with-ilb-ase"></a>Konfigurieren einer Anwendungsgatewayintegration mit einer ILB-ASE

Um über das Anwendungsgateway auf die ILB-ASE zuzugreifen, müssen Sie überprüfen, ob ein virtuelles Netzwerk mit der privaten DNS-Zone verknüpft ist. Wenn kein virtuelles Netzwerk mit dem VNet Ihres Anwendungsgateways verknüpft ist, fügen Sie mit den folgenden Schritten eine Verknüpfung mit dem virtuellen Netzwerk hinzu.

### <a name="configure-virtual-network-links-with-a-private-dns-zone"></a>Konfigurieren von Verknüpfungen virtueller Netzwerke mit einer privaten DNS-Zone

* Um eine Verknüpfung des virtuellen Netzwerks mit einer privaten DNS-Zone zu konfigurieren, wechseln Sie zum Konfigurationsbereich der privaten DNS-Zone. Wählen Sie **VNET-Verknüpfungen** > **Hinzufügen** aus. 

:::image type="content" source="./media/integrate-with-application-gateway/add-vnet-link.png" alt-text="Hinzufügen einer VNet-Verknüpfung zur privaten DNS-Zone":::

* Geben Sie den **Linknamen** ein, und wählen Sie das entsprechende Abonnement und virtuelle Netzwerk aus, in dem sich das Anwendungsgateway befindet.

:::image type="content" source="./media/integrate-with-application-gateway/vnet-link.png" alt-text="Screenshot der Eingabe von Details zum Verknüpfungsnamen für die Einstellung „VNET-Verknüpfungen“ in der privaten DNS-Zone":::

* Sie können den Back-End-Integritätsstatus unter **Back-End-Integrität** im Bereich des Anwendungsgateways überprüfen.

:::image type="content" source="./media/integrate-with-application-gateway/backend-health.png" alt-text="Screenshot der Überprüfung des Back-End-Integritätsstatus in „Back-End-Integrität“":::

### <a name="add-a-public-dns-record"></a>Hinzufügen eines öffentlichen DNS-Eintrags

Sie müssen eine ordnungsgemäße DNS-Zuordnung konfigurieren, wenn Sie über das Internet auf das Anwendungsgateway zugreifen.

* Die öffentliche IP-Adresse des Anwendungsgateways finden Sie im Bereich des Anwendungsgateways unter **Front-End-IP-Konfigurationen**.

:::image type="content" source="./media/integrate-with-application-gateway/frontend-ip.png" alt-text="Die Front-End-IP-Adresse des Anwendungsgateways finden Sie unter „Front-End-IP-Konfiguration“.":::

* Verwenden Sie den Azure DNS-Dienst als Beispiel. Sie können einen Datensatz hinzufügen, um den App-Domänennamen der öffentlichen IP-Adresse des Anwendungsgateways zuzuordnen.

:::image type="content" source="./media/integrate-with-application-gateway/dns-service.png" alt-text="Screenshot des Hinzufügens eines Datensatzes, um den App-Domänennamen der öffentlichen IP-Adresse des Anwendungsgateways zuzuordnen":::

### <a name="validate-connection"></a>Überprüfen der Verbindung

* Sie können auf einem Computer mit Internetzugriff die Namensauflösung für den App-Domänennamen mit der öffentlichen IP-Adresse des Anwendungsgateways überprüfen.

:::image type="content" source="./media/integrate-with-application-gateway/name-resolution.png" alt-text="Überprüfen der Namensauflösung über eine Eingabeaufforderung":::

* Testen Sie auf einem Computer mit Internetzugriff den Webzugriff über einen Browser.

:::image type="content" source="./media/integrate-with-application-gateway/access-web.png" alt-text="Screenshot des Öffnens eines Browsers und des Zugriffs auf das Internet":::

<!--LINKS-->
[appgw]: ../../application-gateway/overview.md
[custom-domain]: ../app-service-web-tutorial-custom-domain.md
[creation]: ./creation.md
[createfromtemplate]: ./create-from-template.md
[createprivatednszone]: ../../dns/private-dns-getstarted-portal.md
[AppServiceEnvironmentoverview]: ./overview.md
[privatednszone]: ../../dns/private-dns-overview.md
[Tutorial: Create an application gateway with a Web Application Firewall using the Azure portal]: ../../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md
