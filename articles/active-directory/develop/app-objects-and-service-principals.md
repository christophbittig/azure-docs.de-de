---
title: Apps und Dienstprinzipale in Azure AD
titleSuffix: Microsoft identity platform
description: Hier finden Sie Informationen zur Beziehung zwischen Anwendungsobjekten und Dienstprinzipalobjekten in Azure Active Directory.
author: rwike77
manager: CelesteDG
services: active-directory
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 06/23/2021
ms.author: ryanwi
ms.custom: contperf-fy21q4-portal, aaddev, identityplatformtop40
ms.reviewer: sureshja
ms.openlocfilehash: b8748bc5f628db654f67d68176e5cd10a1f02ec3
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/23/2021
ms.locfileid: "112539032"
---
# <a name="application-and-service-principal-objects-in-azure-active-directory"></a>Anwendungs- und Dienstprinzipalobjekte in Azure Active Directory

In diesem Artikel werden die Anwendungsregistrierung, Anwendungsobjekte und Dienstprinzipale in Azure Active Directory (Azure AD) mit ihren Eigenschaften, Verwendungsmöglichkeiten und den wechselseitigen Beziehungen beschrieben. Außerdem wird ein Beispielszenario mit mehreren Mandanten vorgestellt, um die Beziehung zwischen dem Anwendungsobjekt einer Anwendung und den entsprechenden Dienstprinzipalobjekten zu veranschaulichen.

## <a name="application-registration"></a>Anwendungsregistrierung

Um die Identitäts- und Zugriffsverwaltungsfunktionen an Azure AD zu delegieren, muss eine Anwendung bei einem Azure AD-Mandanten registriert werden. Wenn Sie Ihre Anwendung bei Azure AD registrieren, erstellen Sie eine Identitätskonfiguration für Ihre Anwendung, die die Integration in Azure AD ermöglicht. Wenn Sie eine App im Azure-Portal registrieren, wählen Sie aus, ob es sich um einen Mandanten (Zugriff nur in Ihrem Mandanten möglich) oder um mehrere Mandanten (Zugriff in anderen Mandanten möglich) handelt. Optional können Sie auch einen Umleitungs-URI festlegen, an den das Zugriffstoken gesendet wird. Eine ausführliche Anleitung zum Registrieren einer App finden Sie im [Schnellstart zur App-Registrierung](quickstart-register-app.md).

Wenn Sie die App-Registrierung abgeschlossen haben, verfügen Sie über eine global eindeutige Instanz der App (das Anwendungsobjekt), die sich in Ihrem Basismandanten oder -verzeichnis befindet. Außerdem verfügen Sie über eine global eindeutige ID für Ihre App (App- oder Client-ID). Im Portal können Sie dann Geheimnisse oder Zertifikate und Geltungsbereiche hinzufügen, damit Ihre App funktioniert. Sie können auch das Branding Ihrer App im Anmeldedialogfeld anpassen und vieles mehr.

Wenn Sie eine Anwendung im Portal registrieren, werden automatisch ein Anwendungsobjekt sowie ein Dienstprinzipalobjekt in Ihrem Basismandanten erstellt. Wenn Sie eine Anwendung mit den Microsoft Graph-APIs registrieren/erstellen, wird das Dienstprinzipalobjekt in einem separaten Schritt erstellt.

## <a name="application-object"></a>Anwendungsobjekt

Eine Azure AD-Anwendung wird durch ein einziges, eindeutiges Anwendungsobjekt definiert, das sich im Azure AD-Mandanten befindet, bei dem die Anwendung registriert wurde (der sogenannte „Basis“-Mandant der Anwendung).  Ein Anwendungsobjekt wird als Vorlage bzw. Blaupause zum Erstellen eines oder mehrerer Dienstprinzipalobjekte verwendet.  Ein Dienstprinzipal wird in jedem Mandanten erstellt, in dem die Anwendung verwendet wird. Vergleichbar einer Klasse in der objektorientierten Programmierung hat das Anwendungsobjekt einige statische Eigenschaften, die auf alle erstellten Dienstprinzipale (oder Anwendungsinstanzen) angewendet werden.

Das Anwendungsobjekt beschreibt drei Aspekte einer Anwendung: das Ausstellen von Token durch den Dienst für den Zugriff auf die Anwendung, die Ressourcen, auf die die Anwendung möglicherweise zugreifen muss, und die Aktionen, die die Anwendung ausführen kann.

Sie können das Blatt **App-Registrierungen** im [Azure-Portal][AZURE-Portal] verwenden, um die Anwendungsobjekte in Ihrem Basismandanten aufzulisten und zu verwalten.

![Blatt „App-Registrierungen“](./media/app-objects-and-service-principals/app-registrations-blade.png)

Die [Application-Entität][MS-Graph-App-Entity] von Microsoft Graph definiert das Schema für die Eigenschaften eines Anwendungsobjekts.

## <a name="service-principal-object"></a>Dienstprinzipalobjekt

Um auf Ressourcen zugreifen zu können, die von einem Azure AD-Mandanten geschützt werden, muss die Entität, die Zugriff benötigt, durch einen Sicherheitsprinzipal dargestellt werden. Diese Anforderung gilt für Benutzer (Benutzerprinzipal) und Anwendungen (Dienstprinzipal). Der Sicherheitsprinzipal definiert die Zugriffsrichtlinie und Berechtigungen für den Benutzer/die Anwendung im Azure AD-Mandanten. Dies aktiviert die Kernfunktionen wie die Authentifizierung des Benutzers/der Anwendung während der Anmeldung und die Autorisierung beim Zugriff auf Ressourcen.

Es stehen drei Arten von Dienstprinzipalen zur Verfügung:

- **Anwendung:** Dieser Dienstprinzipaltyp ist die lokale Darstellung (bzw. Anwendungsinstanz) eines globalen Anwendungsobjekts in einem einzelnen Mandanten oder Verzeichnis. In diesem Fall ist der Dienstprinzipal eine konkrete Instanz, die aus dem Anwendungsobjekt erstellt wird und bestimmte Eigenschaften von diesem Anwendungsobjekt erbt. Ein Dienstprinzipal wird in jedem Mandanten erstellt, in dem die Anwendung verwendet wird, und verweist auf das global eindeutige Anwendungsobjekt.  Das Dienstprinzipalobjekt definiert, was die App tatsächlich im jeweiligen Mandanten ausführen kann, wer auf die App zugreifen kann und auf welche Ressourcen die App zugreifen kann.

   Wenn eine Anwendung die Berechtigung zum Zugriff auf Ressourcen in einem Mandanten erhält (bei der Registrierung oder Zustimmung), wird ein Dienstprinzipalobjekt erstellt. Wenn Sie eine Anwendung über das Azure-Portal registrieren, wird automatisch ein Dienstprinzipal erstellt. Sie können auch mit Azure PowerShell, der Azure-Befehlszeilenschnittstelle, Microsoft Graph und mit anderen Tools Dienstprinzipalobjekte in einem Mandanten erstellen.

- **Verwaltete Identität:** Dieser Dienstprinzipaltyp repräsentiert eine [verwaltete Identität](../managed-identities-azure-resources/overview.md). Dank verwalteter Identitäten müssen Entwickler keine Anmeldeinformationen mehr verwalten. Verwaltete Identitäten stellen eine Identität bereit, mit deren Hilfe Anwendungen eine Verbindung mit Ressourcen herstellen können, die die Azure AD-Authentifizierung unterstützen. Wenn eine verwaltete Identität aktiviert ist, wird in Ihrem Mandanten ein Dienstprinzipal erstellt, der diese verwaltete Identität repräsentiert. Dienstprinzipale, die verwaltete Identitäten repräsentieren, können Zugriff und Berechtigungen erhalten, sie können jedoch nicht direkt aktualisiert oder geändert werden.

- **Legacy:** Dieser Dienstprinzipaltyp repräsentiert eine Legacy-App. Bei einer Legacy-App handelt es sich um eine App, die vor der Einführung von App-Registrierungen oder über eine Legacybenutzeroberfläche erstellt wurde. Ein Legacydienstprinzipal kann Anmeldeinformationen, Dienstprinzipalnamen, Antwort-URLs und andere Eigenschaften aufweisen, die von einem autorisierten Benutzer bearbeitet werden können. Dieser Dienstprinzipaltyp kann jedoch keiner App-Registrierung zugeordnet werden. Ein Legacydienstprinzipal kann nur in dem Mandanten verwendet werden, in dem er erstellt wurde.

Die [ServicePrincipal-Entität][MS-Graph-Sp-Entity] von Microsoft Graph definiert das Schema für die Eigenschaften eines Dienstprinzipalobjekts.

Auf dem Blatt **Unternehmensanwendungen** im Azure-Portal können die Dienstprinzipale in einem Mandanten aufgelistet und verwaltet werden. Sie können die Berechtigungen des Dienstprinzipals, die vom Benutzer genehmigten Berechtigungen, die Benutzer, die diese Genehmigung erteilt haben, die Anmeldeinformationen und mehr anzeigen.

![Blatt „Unternehmens-Apps“](./media/app-objects-and-service-principals/enterprise-apps-blade.png)

## <a name="relationship-between-application-objects-and-service-principals"></a>Beziehung zwischen Anwendungsobjekten und Dienstprinzipalen

Das Anwendungsobjekt ist die *globale* Darstellung Ihrer Anwendung für die Verwendung bei allen Mandanten. Der Dienstprinzipal ist die *lokale* Darstellung für die Verwendung in einem bestimmten Mandanten. Das Anwendungsobjekt fungiert als Vorlage, aus der allgemeine und Standardeigenschaften zur Verwendung im entsprechenden Dienstprinzipal *abgeleitet* werden.

Für ein Anwendungsobjekt gilt Folgendes:

- Es hat eine 1:1-Beziehung mit der Softwareanwendung.
- Es hat eine 1:n-Beziehung mit den entsprechenden Dienstprinzipalobjekten.

Ein Dienstprinzipal muss in jedem Mandanten erstellt werden, in dem die Anwendung verwendet wird. Er ermöglicht, eine Identität für die Anmeldung und/oder den Zugriff auf vom Mandanten gesicherte Ressourcen einzurichten. Eine Einzelmandantenanwendung weist nur einen Dienstprinzipal (im eigenen Heimmandanten) auf, der während der Anwendungsregistrierung erstellt und für die Verwendung autorisiert wird. Eine Anwendung mit mehreren Mandanten verfügt außerdem über einen Dienstprinzipal, der in jedem Mandanten erstellt wird, für den ein Benutzer des Mandanten zur Verwendung zugestimmt hat.

### <a name="consequences-of-modifying-and-deleting-applications"></a>Konsequenzen des Änderns und Löschens von Anwendungen

Alle Änderungen, die Sie an Ihrem Anwendungsobjekt vornehmen, werden auch in seinem Dienstprinzipalobjekt ausschließlich im Basismandanten der Anwendung widergespiegelt (der Mandant, in dem die Anwendung registriert wurde). Das bedeutet, dass beim Löschen eines Anwendungsobjekts auch das Dienstprinzipalobjekt des Basismandanten gelöscht wird. Allerdings wird der entsprechende Dienstprinzipal nicht bei der Wiederherstellung des Anwendungsobjekts wiederhergestellt. Bei mehrinstanzenfähigen Anwendungen werden Änderungen am Anwendungsobjekt erst dann in den Dienstprinzipalobjekten von Endkundenmandanten übernommen, wenn der Zugriff über den [Anwendungszugriffsbereich](https://myapps.microsoft.com) entfernt und wieder gewährt wird.

## <a name="example"></a>Beispiel

Das folgende Diagramm veranschaulicht die Beziehung zwischen einem Anwendungsobjekt der Anwendung und den entsprechenden Dienstprinzipalobjekten im Kontext einer mehrinstanzenfähigen Beispielanwendung mit dem Namen **HR App**. In diesem Beispielszenario werden drei Azure AD-Mandanten verwendet:

- **Adatum:** Der Mandant, der von dem Unternehmen verwendet wird, das die **Personal-App** entwickelt hat
- **Contoso:** Der Mandant, der von der Organisation Contoso (einem Endkunden der **Personal-App**) verwendet wird
- **Fabrikam:** Der von der Organisation Fabrikam (ebenfalls ein Endkunde der **Person-App**) verwendete Mandant

![Beziehung zwischen einem App-Objekt und einem Dienstprinzipalobjekt](./media/app-objects-and-service-principals/application-objects-relationship.svg)

Dieses Beispielszenario:

| Schritt | BESCHREIBUNG |
|------|-------------|
| 1    | Schritt 1 ist der Prozess zur Erstellung der Anwendungs- und Dienstprinzipalobjekte im Home-Mandanten der Anwendung. |
| 2    | Nachdem die Administratoren von Contoso und Fabrikam die Zustimmung erteilt haben, wird im Azure AD-Mandanten des Unternehmens ein Dienstprinzipalobjekt erstellt und mit den Berechtigungen versehen, die vom Administrator gewährt wurden. Beachten Sie auch, dass die HR App so konfiguriert bzw. entworfen werden kann, dass die Zustimmung für die individuelle Nutzung durch die Benutzer erteilt wird. |
| 3    | Die Endkundenmandanten der Personalanwendung (Contoso und Fabrikam) erhalten jeweils ein eigenes Dienstprinzipalobjekt. Jedes Objekt repräsentiert die Verwendung einer Instanz der Anwendung zur Laufzeit, gesteuert durch die Berechtigungen, die vom jeweiligen Administrator per Zustimmung erteilt wurden. |

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich, wie Sie einen Dienstprinzipal erstellen:

- [Verwenden des Azure-Portals](howto-create-service-principal-portal.md)
- [Verwenden von Azure PowerShell](howto-authenticate-service-principal-powershell.md)
- [Verwenden der Azure-Befehlszeilenschnittstelle](/cli/azure/create-an-azure-service-principal-azure-cli)
- [Verwenden von Microsoft Graph](/graph/api/serviceprincipal-post-serviceprincipals?tabs=http) und von [Microsoft Graph-Tester](https://developer.microsoft.com/graph/graph-explorer), um die Anwendungs- und die Dienstprinzipalobjekte abzufragen.

<!--Reference style links -->
[MS-Graph-App-Entity]: /graph/api/resources/application
[MS-Graph-Sp-Entity]: /graph/api/resources/serviceprincipal
[AZURE-Portal]: https://portal.azure.com