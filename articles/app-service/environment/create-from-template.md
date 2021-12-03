---
title: Erstellen einer ASE mit ARM
description: Erfahren Sie, wie eine externe oder ILB-basierte Azure App Service-Umgebung mithilfe einer Azure Resource Manager-Vorlage erstellt wird.
author: madsd
ms.assetid: 6eb7d43d-e820-4a47-818c-80ff7d3b6f8e
ms.topic: article
ms.date: 10/11/2021
ms.author: madsd
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 677703e455d985b91f77a71acce3f7809525b368
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/16/2021
ms.locfileid: "132519570"
---
# <a name="create-an-ase-by-using-an-azure-resource-manager-template"></a>Erstellen einer ASE mit einer Azure Resource Manager-Vorlage

## <a name="overview"></a>Übersicht
> [!NOTE]
> In diesem Artikel werden die App Service-Umgebungen v2 und v3 behandelt, die mit isolierten App Service-Plänen verwendet werden.
> 

Azure App Service-Umgebungen (App Service Environments, ASEs) können mit einem über das Internet zugänglichen Endpunkt oder einem Endpunkt an einer internen Adresse in einem virtuellen Azure-Netzwerk erstellt werden. Beim Erstellen mit einem internen Endpunkt wird dieser Endpunkt über eine Azure-Komponente, den sogenannten internen Lastenausgleich (Internal Load Balancer, ILB), bereitgestellt. Eine ASE an einer internen IP-Adresse wird als ILB-ASE bezeichnet. Eine ASE mit einem öffentlichen Endpunkt wird als externe ASE bezeichnet. 

Eine ASE kann über das Azure-Portal oder mit einer Azure Resource Manager-Vorlage erstellt werden. In diesem Artikel werden die Schritte und Syntaxelemente beschrieben, die Sie zum Erstellen einer externen ASE oder einer ILB-ASE mit Resource Manager-Vorlagen benötigen. Informationen zum Erstellen einer ASEv2 im Azure-Portal finden Sie unter [Erstellen einer externen ASE][MakeExternalASE] oder [Erstellen einer ILB-ASE][MakeILBASE].
Informationen zum Erstellen einer ASEv3 im Azure-Portal finden Sie unter [Erstellen einer ASEv3][Create ASEv3].

Beim Erstellen einer ASE im Azure-Portal können Sie entweder gleichzeitig ein VNet erstellen oder ein vorhandenes VNet auswählen, in dem die Bereitstellung erfolgt. 

Wenn Sie eine ASE aus einer Vorlage erstellen, benötigen Sie zunächst Folgendes: 

* Ein Azure Virtual Network.
* Ein Subnetz in dem virtuellen Netzwerk Wir empfehlen eine ASE-Subnetzgröße von `/24` mit 256 Adressen, um genügend Platz für zukünftiges Wachstum und Skalierungsanforderungen zu haben. Nachdem die ASE erstellt wurde, können Sie die Größe nicht mehr ändern.
* Wenn Sie eine ASE in einem bereits vorhandenen VNet und Subnetz erstellen, sind der vorhandene Ressourcengruppenname, der Name des virtuellen Netzwerks und der Subnetzname erforderlich.
* Das Abonnement, in dem die Bereitstellung erfolgen soll.
* Der Speicherort, in dem die Bereitstellung erfolgen soll.

Um die Erstellung Ihrer ASE zu automatisieren, befolgen Sie die Richtlinien in den folgenden Abschnitten. Wenn Sie eine ILB-ASEv2 mit benutzerdefiniertem dnsSuffix erstellen (z. B. `internal-contoso.com`), müssen Sie noch einige weitere Dinge erledigen.

1. Nach dem Erstellen Ihrer ILB-ASE mit benutzerdefiniertem dnsSuffix sollte ein TLS/SSL-Zertifikat hochgeladen werden, das Ihrer ILB-ASE-Domäne entspricht.

2. Das hochgeladene TLS/SSL-Zertifikat wird der ILB-ASE als TLS/SSL-Standardzertifikat zugewiesen.  Dieses Zertifikat wird für TLS/SSL-Datenverkehr zu Apps in der ILB-ASE verwendet, wenn die Apps die allgemeine Stammdomäne verwenden, die der ASE zugewiesen ist (z. B. `https://someapp.internal-contoso.com`).


## <a name="create-the-ase"></a>Erstellen der ASE
Eine Resource Manager-Vorlage, die eine ASE und deren zugehörige Parameterdatei erstellt, finden Sie auf GitHub für [ASEv3][asev3quickstarts] und [ASEv2][quickstartasev2create].

Wenn Sie eine ASE erstellen möchten, verwenden Sie diese Resource Manager-Vorlagenbeispiele für [ASEv3][asev3quickstarts] oder [ASEv2][quickstartilbasecreate]. Diese sind für diesen Anwendungsfall bestimmt. Die meisten Parameter in der Datei *azuredeploy.parameters.json* gelten für die Erstellung sowohl einer ILB-ASE als auch einer externen ASE. In der folgenden Liste sind die Parameter aufgeführt, die für die Erstellung einer ILB-ASE mit einem vorhandenen Subnetz eine besondere Bedeutung haben bzw. nur dafür benötigt werden.
### <a name="asev3-parameters"></a>ASEv3-Parameter
* *aseName*: Erforderlich. Dieser Parameter definiert einen eindeutigen ASE-Namen. 
* *internalLoadBalancingMode*: Erforderlich. In den meisten Fällen auf 3 festlegen, was HTTP/HTTPS-Datenverkehr an den Ports 80/443 bedeutet. Wenn diese Eigenschaft auf 0 festgelegt ist, bleibt der HTTP/HTTPS-Datenverkehr in der öffentlichen VIP.
* *zoneRedundant*: Erforderlich. In den meisten Fällen auf „false“ festlegen, was bedeutet, dass die ASE nicht in Verfügbarkeitszonen bereitgestellt wird. Zonale ASEs können in einigen Regionen bereitgestellt werden. Informationen hierzu finden Sie [hier][AZ Support for ASEv3].
* *dedicatedHostCount*: Erforderlich. In den meisten Fällen auf 0 festlegen, was bedeutet, dass die ASE wie gewohnt ohne bereitgestellte dedizierte Hosts bereitgestellt wird.
* *useExistingVnetandSubnet*: Erforderlich. Wird bei Verwendung eines vorhandenen virtuellen Netzwerks und Subnetzes auf TRUE festgelegt. 
* *vNetResourceGroupName:* erforderlich bei Verwendung eines vorhandenen virtuellen Netzwerks und Subnetzes. Dieser Parameter definiert den Ressourcengruppennamen des vorhandenen VNets und Subnetzes, in denen sich die ASE befindet.
* *virtualNetworkName:* erforderlich bei Verwendung eines vorhandenen virtuellen Netzwerks und Subnetzes. Dieser Parameter definiert den VNet-Namen des vorhandenen virtuellen Netzwerks und Subnetzes, in denen sich die ASE befindet.
* *subnetName:* erforderlich bei Verwendung eines vorhandenen virtuellen Netzwerks und Subnetzes. Dieser Parameter definiert den Subnetznamen des vorhandenen virtuellen Netzwerks und Subnetzes, in denen sich die ASE befindet.
* *createPrivateDNS*: Auf „true“ festlegen, wenn Sie nach der Erstellung der ASEv3 eine private DNS-Zone erstellen möchten. Wenn dieser Parameter auf „true“ festgelegt ist, wird für eine ILB-ASE eine private DNS-Zone als ASE-Name mit dem DNS-Suffix *appserviceenvironment.net* erstellt. 
### <a name="asev2-parameters"></a>ASEv2-Parameter
* *aseName*: Dieser Parameter definiert einen eindeutigen ASE-Namen.
* *location*: Dieser Parameter definiert den Standort der App Service-Umgebung.
* *existingVirtualNetworkName:* Dieser Parameter definiert den Namen des virtuellen Netzwerks des vorhandenen virtuellen Netzwerks und Subnetzes, in denen sich die ASE befindet.
* *existingVirtualNetworkResourceGroup:* Dieser Parameter definiert den Namen der Ressourcengruppe des vorhandenen virtuellen Netzwerks und Subnetzes, in denen sich die ASE befindet.
* *subnetName:* Dieser Parameter definiert den Subnetznamen des vorhandenen virtuellen Netzwerks und Subnetzes, in denen sich die ASE befindet.
* *internalLoadBalancingMode*: Sollte in den meisten Fällen auf „3“ festgelegt werden. Dies bedeutet, dass sowohl der HTTP/HTTPS-Datenverkehr über die Ports 80/443 als auch die Steuer-/Datenkanalports, auf die der FTP-Dienst in der ASE lauscht, an eine per ILB zugeordnete Adresse des virtuellen Netzwerks gebunden sind. Wenn diese Eigenschaft auf „2“ festgelegt wird, sind nur die Ports, die sich auf den FTP-Dienst beziehen (sowohl Steuer- als auch Datenkanäle) an eine ILB-Adresse gebunden. Wenn diese Eigenschaft auf 0 festgelegt ist, bleibt der HTTP/HTTPS-Datenverkehr in der öffentlichen VIP.
* *dnsSuffix*: Dieser Parameter definiert die Standardstammdomäne, die der ASE zugewiesen wird. In der öffentlichen Variante von Azure App Service lautet die Standardstammdomäne für alle Web-Apps *azurewebsites.net*. Da eine ILB-ASE sich intern im virtuellen Netzwerk eines Kunden befindet, wäre es nicht sinnvoll, die Standardstammdomäne des öffentlichen Diensts zu verwenden. Stattdessen sollte eine ILB-ASE über eine Standardstammdomäne verfügen, für die die Verwendung im internen virtuellen Netzwerk eines Unternehmens sinnvoll ist. Die Contoso Corporation könnte beispielsweise die Standardstammdomäne *internal-contoso.com* für Apps verwenden, für die beabsichtigt ist, dass sie nur im virtuellen Netzwerk von Contoso auflösbar und zugänglich sind. 
* *ipSslAddressCount*: Für diesen Parameter wird in der Datei *azuredeploy.json* automatisch der Wert 0 verwendet, da ILB-ASEs nur über eine einzige ILB-Adresse verfügen. Es gibt keine expliziten IP-SSL-Adressen für eine ILB-ASE. Daher muss der IP-SSL-Adresspool für eine ILB-ASE auf 0 (null) festgelegt werden. Andernfalls tritt ein Bereitstellungsfehler auf.

Nachdem alle notwendigen Informationen in der Datei *azuredeploy.parameters.json* eingegeben sind, erstellen Sie die ASE mithilfe des PowerShell-Codeausschnitts. Ändern Sie die Dateipfade in die Speicherorte, die in der Azure Resource Manager-Vorlagendatei auf Ihrem Computer angegeben sind. Denken Sie daran, Ihre eigenen Werte für den Resource Manager-Bereitstellungsnamen und den Ressourcengruppennamen anzugeben:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Die Erstellung der ASE dauert etwa zwei Stunden. Danach wird die ASE im Port in der Liste der ASEs für das Abonnement angezeigt, in dem die Bereitstellung ausgelöst wurde.

## <a name="upload-and-configure-the-default-tlsssl-certificate"></a>Hochladen und Konfigurieren des TLS/SSL-Standardzertifikats
Der ASE muss ein TLS/SSL-Zertifikat als TLS/SSL-Standardzertifikat zugeordnet werden, das zum Herstellen von TLS-Verbindungen mit Apps verwendet wird. Wenn das DNS-Standardsuffix der ASE *internal-contoso.com* lautet, ist für eine Verbindung mit `https://some-random-app.internal-contoso.com` ein TLS/SSL-Zertifikat erforderlich, das für * *.internal-contoso.com* gültig ist. 

Ein gültiges TLS/SSL-Zertifikat können Sie erhalten, indem Sie interne Zertifizierungsstellen verwenden, ein Zertifikat von einem externen Aussteller erwerben oder ein selbstsigniertes Zertifikat verwenden. Unabhängig von der Quelle des TLS/SSL-Zertifikats müssen die folgenden Zertifikatattribute ordnungsgemäß konfiguriert werden:

* **Antragsteller:** Dieses Attribut muss auf * *.your-root-domain-here.com* festgelegt werden.
* **Alternativer Antragstellername:** Dieses Attribut muss sowohl * *.your-root-domain-here.com* als auch * *.scm.your-root-domain-here.com* enthalten. TLS-Verbindungen mit der SCM/Kudu-Website, die jeder App zugeordnet ist, verwenden eine Adresse im Format *your-app-name.scm.your-root-domain-here.com*.

Wenn ein gültiges TLS/SSL-Zertifikat vorhanden ist, sind zwei weitere Vorbereitungsschritte erforderlich. Konvertieren Sie das TLS/SSL-Zertifikat in eine PFX-Datei, und speichern Sie es. Denken Sie daran, dass die PFX-Datei alle Zwischenzertifikate und Stammzertifikate enthalten muss. Sichern Sie es mit einem Kennwort.

Die PFX-Datei muss in eine Base64-Zeichenfolge konvertiert werden, da das TLS/SSL-Zertifikat mithilfe einer Resource Manager-Vorlage hochgeladen wird. Da es sich bei Resource Manager-Vorlagen um Textdateien handelt, muss die PFX-Datei in eine Base64-Zeichenfolge konvertiert werden. Auf diese Weise kann sie als Parameter in die Vorlage eingeschlossen werde.

Verwenden Sie nachstehenden PowerShell-Codeausschnitt für folgende Zwecke:

* Generieren eines selbstsignierten Zertifikats
* Exportieren des Zertifikats als PFX-Datei
* Konvertieren der PFX-Datei in eine Base64-codierte Zeichenfolge
* Speichern der Base64-codierten Zeichenfolge als separate Datei 

Dieser PowerShell-Code für die Base64-Codierung wurde aus dem [Blog zu PowerShell-Skripts][examplebase64encoding] übernommen und angepasst:

```powershell
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

Nachdem das TLS/SSL-Zertifikat erfolgreich generiert und in eine Base64-codierte Zeichenfolge konvertiert wurde, verwenden Sie die Resource Manager-Vorlage [Configure the default SSL certificate][quickstartconfiguressl] (Konfigurieren des SSL-Standardzertifikats) auf GitHub. 

Die Parameter in der Datei *azuredeploy.parameters.json* sind hier aufgeführt:

* *appServiceEnvironmentName*: Der Name der ILB-ASE, die konfiguriert wird.
* *existingAseLocation*: Die Textzeichenfolge mit der Azure-Region, in der die ILB-ASE bereitgestellt wurde.  Beispiel: „USA, Süden-Mitte“
* *pfxBlobString*: Die Base64-codierte Zeichenfolgendarstellung der PFX-Datei. Verwenden Sie den oben gezeigten Codeausschnitt, und kopieren Sie die Zeichenfolge in „exportedcert.pfx.b64“. Fügen Sie sie als Wert des Attributs *pfxBlobString* ein.
* *password*: Das Kennwort, das zum Schützen der PFX-Datei verwendet wird.
* *certificateThumbprint*: Der Fingerabdruck des Zertifikats. Wenn Sie diesen Wert aus PowerShell abrufen (z.B. als *$certificate.Thumbprint* aus dem Codeausschnitt weiter oben), können Sie den Wert unverändert nutzen. Falls Sie den Wert aus dem Windows-Zertifikatdialogfeld kopieren, müssen Sie die überflüssigen Leerzeichen entfernen. *certificateThumbprint* sollte etwa wie folgt aussehen: AF3143EB61D43F6727842115BB7F17BBCECAECAE.
* *certificateName*: Ein benutzerfreundlicher Zeichenfolgenbezeichner zum Identifizieren des Zertifikats, den Sie selbst wählen können. Der Name wird als Teil des eindeutigen Resource Manager-Bezeichners für die Entität *Microsoft.Web/certificates* verwendet, die das TLS/SSL-Zertifikat repräsentiert. Der Name *muss* auf folgendes Suffix enden: \_yourASENameHere_InternalLoadBalancingASE. Dieses Suffix wird im Azure-Portal als Indikator dafür verwendet, dass das Zertifikat zum Sichern einer für den ILB geeigneten ASE genutzt wird.

Ein gekürztes Beispiel für *azuredeploy.parameters.json* sehen Sie hier:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "appServiceEnvironmentName": {
      "value": "yourASENameHere"
    },
    "existingAseLocation": {
      "value": "East US 2"
    },
    "pfxBlobString": {
      "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
    },
    "password": {
      "value": "PASSWORDGOESHERE"
    },
    "certificateThumbprint": {
      "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
    },
    "certificateName": {
      "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
    }
  }
}
```

Nachdem alle notwendigen Informationen in der Datei *azuredeploy.parameters.json* eingegeben sind, konfigurieren Sie das TLS/SSL-Standardzertifikat mithilfe des PowerShell-Codeausschnitts. Ändern Sie die Dateipfade in die Speicherorte, in denen sich die Resource Manager-Vorlagendateien auf Ihrem Computer befinden. Denken Sie daran, Ihre eigenen Werte für den Resource Manager-Bereitstellungsnamen und den Ressourcengruppennamen anzugeben:

```powershell
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Es dauert in jedem ASE-Front-End etwa 40 Minuten, bis die Änderungen angewendet wurden. Bei einer ASE mit Standardgröße, die zwei Front-Ends nutzt, dauert es beispielsweise ungefähr eine Stunde und 20 Minuten, bis der Vorgang für die Vorlage abgeschlossen ist. Während der Ausführung der Vorlage kann die ASE nicht skaliert werden.  

Nachdem die Ausführung der Vorlage abgeschlossen ist, kann über HTTPS auf Apps in der ILB-ASE zugegriffen werden. Die Verbindungen werden mit dem TLS/SSL-Standardzertifikat gesichert. Das TLS/SSL-Standardzertifikat wird verwendet, wenn der Zugriff auf Apps in der ILB-ASE mit einer Kombination aus dem Anwendungsnamen und dem Standardhostnamen angefordert wird. Für `https://mycustomapp.internal-contoso.com` wird beispielsweise das TLS/SSL-Standardzertifikat für * *.internal-contoso.com* verwendet.

Entwickler können jedoch genau wie bei Apps, die im öffentlichen Mehrinstanzendienst ausgeführt werden, benutzerdefinierte Hostnamen für einzelne Apps konfigurieren. Sie können auch eindeutige SNI-TLS/SSL-Zertifikatbindungen für einzelne Apps konfigurieren.

<!--Links-->
[quickstartilbasecreate]: https://azure.microsoft.com/resources/templates/web-app-asev2-ilb-create
[quickstartasev2create]: https://azure.microsoft.com/resources/templates/web-app-asev2-create
[quickstartconfiguressl]: https://azure.microsoft.com/resources/templates/web-app-ase-ilb-configure-default-ssl
[quickstartwebapponasev2create]: https://azure.microsoft.com/resources/templates/web-app-asp-app-on-asev2-create
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/resources/templates/web-app-ase-ilb-configure-default-ssl/
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../../app-service/configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: ./integrate-with-application-gateway.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ILBASEv1Template]: app-service-app-service-environment-create-ilb-ase-resourcemanager.md
[Create ASEv3]: creation.md
[asev3quickstarts]: https://azure.microsoft.com/resources/templates/web-app-asp-app-on-asev3-create
[AZ Support for ASEv3]: zone-redundancy.md