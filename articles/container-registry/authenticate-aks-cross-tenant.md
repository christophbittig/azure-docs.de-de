---
title: Authentifizieren eines AKS-Clusters bei einer Azure-Containerregistrierung in einem anderen AD-Mandanten
description: Konfigurieren des Dienstprinzipals eines AKS-Clusters mit Berechtigungen für den Zugriff auf Ihre Azure-Containerregistrierung in einem anderen AD-Mandanten
ms.topic: article
author: dlepow
ms.author: danlep
ms.date: 09/13/2021
ms.openlocfilehash: da7a03e1f46523b67666a4a0ab7ab53fba909720
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545470"
---
# <a name="pull-images-from-a-container-registry-to-an-aks-cluster-in-a-different-azure-ad-tenant"></a>Pullen von Images aus einer Containerregistrierung in einen AKS-Cluster in einem anderen Azure AD-Mandanten

Es kann vorkommen, dass sich Ihr Azure AKS-Cluster in einem Azure Active Directory-Mandanten und Ihre Azure-Containerregistrierung in einem anderen Mandanten befindet. Dieser Artikel beschreibt das Vorgehen für die mandantenübergreifende Authentifizierung mit den Anmeldeinformationen des AKS-Dienstprinzipals zum Pullen aus der Containerregistrierung.

> [!NOTE]
> Sie können nicht die Registrierung anfügen und sich mithilfe einer verwalteten AKS-Identität authentifizieren, wenn sich der Cluster und die Containerregistrierung in unterschiedlichen Mandanten befinden.

## <a name="scenario-overview"></a>Übersicht über das Szenario
Annahmen für dieses Beispiel:

* Der AKS-Cluster befindet sich in **Mandant A** und die Azure-Containerregistrierung befindet sich in **Mandant B**. 
* Der AKS-Cluster ist mit Dienstprinzipalauthentifizierung in **Mandant A** konfiguriert. Erfahren Sie mehr über das Erstellen und Verwenden eines [Dienstprinzipals für Ihren AKS-Cluster](../aks/kubernetes-service-principal.md).

Sie benötigen mindestens die Rolle Mitwirkender im Abonnement des AKS-Clusters und die Rolle Besitzer im Abonnement der Containerregistrierung.

Mit diesen schrittweisen Anweisungen erreichen Sie Folgendes:

* Erstellen einer mehrinstanzenfähigen Anwendung (Dienstprinzipal) in **Mandant A** 
* Bereitstellen der Anwendung in **Mandant B**
* Konfigurieren des Dienstprinzipals zum Pullen aus der Registrierung in **Mandant B**
* Aktualisieren des AKS-Clusters in **Mandant A** für die Authentifizierung mit dem neuen Dienstprinzipal


## <a name="step-by-step-instructions"></a>Schrittweise Anweisungen

### <a name="step-1-create-multitenant-azure-ad-application"></a>Schritt 1: Erstellen einer mehrinstanzenfähigen Azure AD-Anwendung

1. Melden Sie sich im [Azure-Portal](https://portal.azure.com/) in **Mandant A** an.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Verwalten** Folgendes aus: **App-Registrierungen > + Neue Registrierung**.
1. Wählen Sie unter **Unterstützte Kontotypen** die Option **Konten in einem beliebigen Organisationsverzeichnis** aus.
1. Geben Sie *https://www.microsoft.com* als **Umleitungs-URI** ein.
1. Wählen Sie **Registrieren**.
1. Notieren Sie sich auf der Seite **Übersicht** die **Anwendungs-ID (Client-ID)** . Sie wird in Schritt 2 und Schritt 4 verwendet.

    :::image type="content" source="media/authenticate-kubernetes-cross-tenant/service-principal-overview.png" alt-text="Dienstprinzipal-Anwendungs-ID":::
1. Wählen Sie in **Zertifikate und Geheimnisse** unter **Geheimer Clientschlüssel** die Option **+Neuer geheimer Clientschlüssel**.
1. Geben Sie eine **Beschreibung** wie etwa *Kennwort* ein und wählen Sie **Hinzufügen** aus.
1. Notieren Sie unter **Geheimer Clientschlüssel** den Wert des geheimen Clientgeheimnisses. Sie verwenden ihn in Schritt 4, um den Dienstprinzipal des AKS-Clusters zu aktualisieren.

    :::image type="content" source="media/authenticate-kubernetes-cross-tenant/configure-client-secret.png" alt-text="Konfigurieren des geheimen Clientschlüssels":::
### <a name="step-2-provision-the-service-principal-in-the-acr-tenant"></a>Schritt 2: Bereitstellen des Dienstprinzipals im ACR-Mandanten

1. Öffnen Sie unter Verwendung eines Administratorkontos in **Mandant B** den folgenden Link. Fügen Sie an den entsprechenden Stellen die **ID von Mandant B** und die **Anwendungs-ID (Client-ID)** der mehrinstanzenfähigen App ein.

    ```console
    https://login.microsoftonline.com/<Tenant B ID>/oauth2/authorize?client_id=<Multitenant application ID>&response_type=code&redirect_uri=<redirect url>
    ```
1. Wählen Sie die Option **Zustimmung im Namen Ihrer Organisation** und dann **Annehmen** aus. 
    
    :::image type="content" source="media/authenticate-kubernetes-cross-tenant/multitenant-app-consent.png" alt-text="Der Anwendung Mandantenzugriff gewähren":::
 

### <a name="step-3-grant-service-principal-permission-to-pull-from-registry"></a>Schritt 3: Dem Dienstprinzipal die Berechtigung zum Pullen aus der Registrierung erteilen

Weisen Sie in **Mandant B** dem Dienstprinzipal die AcrPull-Rolle mit der Zielcontainerregistrierung als Bereich zu. Sie können das [Azure-Portal](../role-based-access-control/role-assignments-portal.md) oder andere Tools verwenden, um die Rolle zuzuweisen. Beispielschritte unter Verwendung der Azure CLI finden Sie unter [Azure Container Registry-Authentifizierung mit Dienstprinzipalen](container-registry-auth-service-principal.md#use-an-existing-service-principal).

:::image type="content" source="media/authenticate-kubernetes-cross-tenant/multitenant-app-acr-pull.png" alt-text="Einer mehrinstanzenfähigen Anwendung die AcrPull-Rolle zuweisen":::

### <a name="step-4-update-aks-with-the-azure-ad-application-secret"></a>Schritt 4: Aktualisieren von AKS mit dem Azure AD-Anwendungsgeheimnis

[Aktualisieren Sie die Anmeldeinformationen des AKS-Dienstprinzipals](../aks/update-credentials.md#update-aks-cluster-with-new-service-principal-credentials) mit der (Client-) ID der mehrinstanzenfähigen Anwendung und dem geheimen Clientschlüssel aus Schritt 1.

Das Aktualisieren des Dienstprinzipals kann einige Minuten dauern.

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr: [Azure Container Registry-Authentifizierung mit Dienstprinzipalen](container-registry-auth-service-principal.md)
* Weitere Informationen zu Geheimnissen für Imagepullvorgänge finden Sie in der [Kubernetes-Dokumentation](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod).
- Informationen zu [Anwendungs- und Dienstprinzipalobjekten in Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md)
- Weitere Informationen zu [Szenarien zum Authentifizieren per Azure Container Registry](authenticate-kubernetes-options.md) über einen Kubernetes-Cluster


