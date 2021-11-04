---
title: Einrichten von Azure Active Directory für die Clientauthentifizierung
description: Hier erfahren Sie, wie Azure Active Directory (Azure AD) für die Authentifizierung von Clients für Service Fabric-Cluster eingerichtet wird.
ms.topic: conceptual
ms.date: 6/28/2019
ms.custom: ignite-fall-2021
ms.openlocfilehash: e960b753e7d1c76992e59cb03133f27f7e305ea1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131004072"
---
# <a name="set-up-azure-active-directory-for-client-authentication"></a>Einrichten von Azure Active Directory für die Clientauthentifizierung

Für in Azure ausgeführte Cluster wird Azure Active Directory (Azure AD) zum Sichern des Zugriffs auf Verwaltungsendpunkte empfohlen. In diesem Artikel wird die Einrichtung von Azure AD zum Authentifizieren von Clients für einen Service Fabric-Cluster beschrieben.

In diesem Artikel wird der Begriff „Anwendung“ verwendet, um sich auf [Azure Active Directory-Anwendungen](../active-directory/develop/developer-glossary.md#client-application) zu beziehen, nicht auf Service Fabric-Anwendungen. Die Unterscheidung wird bei Bedarf vorgenommen. Mit Azure AD können Organisationen (so genannte Mandanten) den Benutzerzugriff auf Anwendungen verwalten.

Service Fabric-Cluster bieten unterschiedliche Einstiegspunkte für ihre Verwaltungsfunktionen. Hierzu zählen etwa [Service Fabric Explorer][service-fabric-visualizing-your-cluster] (webbasiert) und [Visual Studio][service-fabric-manage-application-in-visual-studio]. Daher werden Sie zwei Azure AD-Anwendungen erstellen, um den Zugriff auf den Cluster zu steuern: eine Webanwendung und eine native Anwendung. Nachdem die Anwendungen erstellt wurden, weisen Sie Benutzer zu schreibgeschützten Rollen und Administratorrollen zu.

> [!NOTE]
> Vor der Clustererstellung müssen folgende Schritte unter Linux ausgeführt werden. Unter Windows haben Sie auch die Möglichkeit, die [Azure AD-Authentifizierung für einen bestehenden Cluster zu konfigurieren](https://github.com/Azure/Service-Fabric-Troubleshooting-Guides/blob/master/Security/Configure%20Azure%20Active%20Directory%20Authentication%20for%20Existing%20Cluster.md).

> [!NOTE]
> Es ist ein [bekanntes Problem](https://github.com/microsoft/service-fabric/issues/399), dass Anwendungen und Knoten in Linux AAD-fähigen Clustern nicht im Azure-Portal angezeigt werden können.



## <a name="prerequisites"></a>Voraussetzungen
In diesem Artikel wird davon ausgegangen, dass Sie bereits einen Mandanten erstellt haben. Lesen Sie andernfalls zunächst den Artikel [Einrichten eines Azure Active Directory-Mandanten][active-directory-howto-tenant].

Wir haben eine Reihe von Windows PowerShell-Skripts erstellt, um einige Schritte der Konfiguration von Azure AD mit einem Service Fabric-Cluster zu vereinfachen.

1. [Klonen Sie das Repository](https://github.com/Azure-Samples/service-fabric-aad-helpers) auf Ihren Computer.
2. [Stellen Sie sicher, dass Sie die Voraussetzungen](https://github.com/Azure-Samples/service-fabric-aad-helpers#getting-started) für die Installation der Skripts erfüllen.

## <a name="create-azure-ad-applications-and-assign-users-to-roles"></a>Erstellen von Azure AD-Anwendungen und Zuweisen von Benutzern zu Rollen

Wir verwenden die Skripts zur Erstellung von zwei Azure AD-Anwendungen, um den Zugriff auf den Cluster zu steuern: eine Webanwendung und eine native Anwendung. Nachdem Sie die Anwendungen für Ihren Cluster erstellt haben, müssen Sie Benutzer für die [von Service Fabric unterstützten Rollen](service-fabric-cluster-security-roles.md) erstellen: „read-only“ (schreibgeschützt) und „admin“ (Administrator).

Führen Sie `SetupApplications.ps1` aus, und geben Sie die Mandanten-ID, den Clusternamen und die Antwort-URL der Webanwendung als Parameter an.  Geben Sie außerdem Benutzernamen und Kennwörter für die Benutzer an. Beispiel:

```powershell
$Configobj = .\SetupApplications.ps1 -TenantId '0e3d2646-78b3-4711-b8be-74a381d9890c' -ClusterName 'mysftestcluster' -WebApplicationReplyUrl 'https://mysftestcluster.eastus.cloudapp.azure.com:19080/Explorer/index.html' -AddResourceAccess
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestUser' -Password 'P@ssword!123'
.\SetupUser.ps1 -ConfigObj $Configobj -UserName 'TestAdmin' -Password 'P@ssword!123' -IsAdmin
```

> [!NOTE]
> Für nationale Clouds (etwa Azure Government, Azure China oder Azure Deutschland) sollte auch der Parameter `-Location` angegeben werden.

Die Mandanten-ID (*TenantId*) können Sie mithilfe des PowerShell-Befehls `Get-AzureSubscription` ermitteln. Wenn Sie diesen Befehl ausführen, wird die Mandanten-ID für jedes Abonnement angezeigt.

*ClusterName* wird als Präfix für durch das Skript erstellte Azure AD-Anwendungen verwendet. Der Wert muss nicht exakt dem tatsächlichen Clusternamen entsprechen. Er dient lediglich zur Vereinfachung der Zuordnung von Azure AD-Artefakten zu dem Service Fabric-Cluster, mit dem sie verwendet werden.

*WebApplicationReplyUrl* ist der Standardendpunkt, den Azure AD nach Abschluss des Anmeldeprozesses an Ihre Benutzer zurückgibt. Legen Sie diesen Endpunkt auf den Service Fabric Explorer-Endpunkt für Ihren Cluster fest. Wenn Sie Azure AD-Anwendungen erstellen, um einen bestehenden Cluster darzustellen, stellen Sie sicher, dass diese URL mit dem Endpunkt Ihres bestehenden Clusters übereinstimmt. Wenn Sie Anwendungen für einen neuen Cluster erstellen, planen Sie den Endpunkt, den Ihr Cluster haben wird, und achten Sie darauf, nicht den Endpunkt eines bestehenden Clusters zu verwenden. Standardmäßig ist der Service Fabric Explorer-Endpunkt:

https://&lt;Cluster-Domäne&gt;:19080/Explorer

Sie werden aufgefordert, sich bei einem Konto anzumelden, das über Administratorrechte für den Azure AD-Mandanten verfügt. Nach der Anmeldung erstellt das Skript die Webanwendung und die native Anwendung, die Ihren Service Fabric-Cluster darstellen. Wenn Sie sich die Anwendungen des Mandanten im [Azure-Portal][azure-portal] ansehen, sollten zwei neue Einträge angezeigt werden:

   * *Clustername*\_Cluster
   * *Clustername*\_Client

Das Skript gibt den für die Azure Resource Manager-Vorlage erforderlichen JSON-Code aus, wenn Sie [Ihren AAD-aktivierten Cluster erstellen](service-fabric-cluster-creation-create-template.md#add-azure-ad-configuration-to-use-azure-ad-for-client-access). Es empfiehlt sich daher, das PowerShell-Fenster geöffnet zu lassen.

```json
"azureActiveDirectory": {
  "tenantId":"<guid>",
  "clusterApplication":"<guid>",
  "clientApplication":"<guid>"
},
```

## <a name="troubleshooting-help-in-setting-up-azure-active-directory"></a>Problembehandlung beim Einrichten von Azure Active Directory
Das Einrichten und Verwenden von Azure AD ist unter Umständen recht schwierig. Im Folgenden finden Sie daher einige Hinweise, wie Sie Probleme beheben können.

### <a name="service-fabric-explorer-prompts-you-to-select-a-certificate"></a>Service Fabric Explorer fordert Sie auf, ein Zertifikat auszuwählen.
#### <a name="problem"></a>Problem
Nach erfolgreicher Anmeldung bei Azure AD über Service Fabric Explorer zeigt der Browser wieder die Startseite an und fordert Sie auf, ein Zertifikat auszuwählen.

![SFX-Zertifikatdialogfeld][sfx-select-certificate-dialog]

#### <a name="reason"></a>`Reason`
Dem Benutzer ist in der Azure AD-Clusteranwendung keine Rolle zugewiesen. Daher tritt bei der Azure AD-Authentifizierung im Service Fabric-Cluster ein Fehler auf. Service Fabric Explorer greift auf die Zertifikatauthentifizierung zurück.

#### <a name="solution"></a>Lösung
Führen Sie die Schritte zum Einrichten von Azure AD aus, und weisen Sie Benutzerrollen zu. Darüber hinaus empfiehlt es sich, „Benutzerzuweisung für den Zugriff auf die App erforderlich“ zu aktivieren, wie dies bei `SetupApplications.ps1` der Fall ist.

### <a name="connection-with-powershell-fails-with-an-error-the-specified-credentials-are-invalid"></a>Beim Herstellen der Verbindung mit PowerShell tritt der folgende Fehler auf: „Die angegebenen Anmeldeinformationen sind ungültig.“
#### <a name="problem"></a>Problem
Wenn Sie die Verbindung mit dem Cluster über PowerShell und unter Verwendung des Sicherheitsmodus „AzureActiveDirectory“ herstellen, tritt nach erfolgreicher Anmeldung bei Azure AD folgender Verbindungsfehler auf: „Die angegebenen Anmeldeinformationen sind ungültig.“

#### <a name="solution"></a>Lösung
Siehe vorherige Lösung.

### <a name="service-fabric-explorer-returns-a-failure-when-you-sign-in-aadsts50011"></a>Service Fabric Explorer gibt bei der Anmeldung den folgenden Fehler zurück: „AADSTS50011“
#### <a name="problem"></a>Problem
Wenn Sie versuchen, sich in Service Fabric Explorer bei Azure AD anzumelden, wird der folgende Fehler zurückgegeben: „AADSTS50011: Die Antwortadresse &lt;URL&gt; stimmt nicht mit den Antwortadressen überein, die für die Anwendung konfiguriert wurden: &lt;GUID&gt;.“

![Die SFX-Antwortadresse stimmt nicht überein.][sfx-reply-address-not-match]

#### <a name="reason"></a>`Reason`
Die Cluster(web)anwendung für Service Fabric Explorer versucht, sich bei Azure AD zu authentifizieren, und gibt im Rahmen der Anforderung die Rückgabe-URL für die Umleitung an. Die URL ist jedoch nicht in der Liste **ANTWORT-URL** der Azure AD-Anwendung enthalten.

#### <a name="solution"></a>Lösung
Wählen Sie auf der Registrierungsseite der Azure AD-App für Ihren Cluster die Option **Authentifizierung**, und fügen Sie unter dem Abschnitt **URIs umleiten** die Service Fabric Explorer-URL zur Liste hinzu. Speichern Sie die Änderung.

![Antwort-URL der Webanwendung][web-application-reply-url]

### <a name="connecting-to-the-cluster-using-azure-ad-authentication-via-powershell-gives-an-error-when-you-sign-in-aadsts50011"></a>Beim Herstellen einer Verbindung mit dem Cluster mithilfe von Azure AD Authentifizierung über PowerShell wird ein Fehler ausgegeben, wenn Sie sich anmelden: „AADSTS50011“
#### <a name="problem"></a>Problem
Wenn Sie versuchen, mithilfe von Azure AD über PowerShell eine Verbindung mit einem Service Fabric-Cluster herzustellen, gibt die Anmeldeseite einen Fehler zurück: „AADSTS50011: The reply url specified in the request does not match the reply urls configured for the application: &lt;guid&gt;.“ (Die in der Anforderung angegebene Antwort-URL stimmt nicht mit den für die Anwendung konfigurierten URLs überein.)

#### <a name="reason"></a>`Reason`
Ähnlich wie beim vorherigen Problem versucht PowerShell, sich bei Azure AD zu authentifizieren, wodurch eine Umleitungs-URL bereitgestellt wird, die nicht in der Liste **Antwort-URLs** der Azure AD-Anwendung aufgeführt ist.  

#### <a name="solution"></a>Lösung
Verwenden Sie den gleichen Vorgang wie für das vorherige Problem. Die URL muss aber auf `urn:ietf:wg:oauth:2.0:oob` festgelegt werden, eine spezielle Umleitung für Befehlszeilenauthentifizierung.

### <a name="connect-the-cluster-by-using-azure-ad-authentication-via-powershell"></a>Herstellen der Clusterverbindung unter Verwendung der Azure AD-Authentifizierung über PowerShell
Verwenden Sie das folgende PowerShell-Befehlsbeispiel, um eine Service Fabric-Clusterverbindung herzustellen:

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint <endpoint> -KeepAliveIntervalInSec 10 -AzureActiveDirectory -ServerCertThumbprint <thumbprint>
```

Weitere Informationen finden Sie unter [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster).

### <a name="can-i-reuse-the-same-azure-ad-tenant-in-multiple-clusters"></a>Kann ich den gleichen Azure AD-Mandanten in mehreren Clustern verwenden?
Ja. Denken Sie jedoch daran, die URL von Service Fabric Explorer Ihrer Cluster(web)anwendung hinzuzufügen. Andernfalls funktioniert Service Fabric Explorer nicht.

### <a name="why-do-i-still-need-a-server-certificate-while-azure-ad-is-enabled"></a>Warum benötige ich ein Serverzertifikat, auch wenn Azure AD aktiviert ist?
„FabricClient“ und „FabricGateway“ führen eine wechselseitige Authentifizierung durch. Bei der Azure AD-Authentifizierung stellt die Azure AD-Integration die Identität des Clients für den Server bereit, und das Serverzertifikat dient für den Client zum Überprüfen der Serveridentität. Weitere Informationen zu Service Fabric-Zertifikaten finden Sie unter [X.509-Zertifikate und Service Fabric][x509-certificates-and-service-fabric].

## <a name="next-steps"></a>Nächste Schritte
Nach dem Einrichten von Azure Active Directory-Anwendungen und dem Festlegen von Rollen für Benutzer [konfigurieren Sie einen Cluster und stellen ihn bereit](service-fabric-cluster-creation-via-arm.md).

<!-- Links -->

[azure-cli]: /cli/azure/get-started-with-azure-cli
[azure-portal]: https://portal.azure.com/
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
[sf-aad-ps-script-download]:http://servicefabricsdkstorage.blob.core.windows.net/publicrelease/MicrosoftAzureServiceFabric-AADHelpers.zip
[x509-certificates-and-service-fabric]: service-fabric-cluster-security.md#x509-certificates-and-service-fabric

<!-- Images -->
[sfx-select-certificate-dialog]: ./media/service-fabric-cluster-creation-setup-aad/sfx-select-certificate-dialog.png
[sfx-reply-address-not-match]: ./media/service-fabric-cluster-creation-setup-aad/sfx-reply-address-not-match.png
[web-application-reply-url]: ./media/service-fabric-cluster-creation-setup-aad/web-application-reply-url.png
