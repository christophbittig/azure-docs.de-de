---
title: Festlegen der technischen Konfiguration des Plans für ein IoT Edge-Modulangebot auf Azure Marketplace
description: Festlegen der technischen Konfiguration des Plans für ein IoT Edge-Modulangebot auf Azure Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: aarathin
ms.author: aarathin
ms.date: 05/21/2021
ms.openlocfilehash: 28e9bbcb4086cc9a9bd5e18ef27eb1264f086e29
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070297"
---
# <a name="set-plan-technical-configuration-for-an-iot-edge-module-offer"></a>Festlegen der technischen Konfiguration des Plans für ein IoT Edge-Modulangebot

Der Angebotstyp IoT Edge-Modul ist ein spezifischer Typ Container, der auf einem IoT Edge-Gerät ausgeführt wird. Mit dem Plan **Technische Konfiguration** geben Sie Referenzinformationen für Ihr Containerimagerepository innerhalb der [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) zusammen mit Konfigurationseinstellungen an, mit deren Hilfe Kunden das Modul komfortabel verwenden können.

Nachdem Sie das Angebot übermittelt haben, wird Ihr IoT Edge-Containerimage in Azure Marketplace in eine bestimmte öffentliche Containerregistrierung kopiert. Alle Anforderungen von Azure-Benutzern zur Verwendung Ihres Moduls werden von der öffentlichen Containerregistrierung von Azure Marketplace bedient, nicht von Ihrer privaten Containerregistrierung.

Mithilfe von Tags können Sie mehrere Plattformen anzielen und verschiedene Versionen des Containerimages Ihres Moduls bereitstellen. Weitere Informationen zu Tags und Versionsverwaltung finden Sie unter [Vorbereiten der technischen Ressourcen für das IoT Edge-Modul](iot-edge-technical-asset.md).

## <a name="image-repository-details"></a>Details des Imagerepositorys

Wählen Sie **Azure Container Registry** als Bildquelle aus.

Geben Sie die **Azure-Abonnement-ID** an, unter der die Ressourcennutzung gemeldet und Dienste für die Azure Container Registry-Instanz abgerechnet werden, die Ihr Containerimage enthält. Sie finden diese ID auf der Seite [Abonnements](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) im Azure-Portal.

Geben Sie den [**Namen der Azure-Ressourcengruppe**](../azure-resource-manager/management/manage-resource-groups-portal.md) an, die die Azure Container Registry-Instanz mit Ihrem Containerimage enthält. Der Zugriff auf die Ressourcengruppe muss über die Abonnement-ID (oben) möglich sein. Sie finden den Namen im Azure-Portal auf der Seite [Ressourcengruppen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups).

Geben Sie den [**Namen der Azure Container Registry**](../container-registry/container-registry-intro.md) an, die Ihr Containerimage enthält. Die Containerregistrierung muss in der Azure-Ressourcengruppe vorhanden sein, die Sie zuvor angegeben haben. Geben Sie nur den Registrierungs Namen und nicht den vollständigen Anmelde Servernamen an. Lassen Sie **azurecr.io** beim Namen aus. Sie finden den Registrierungsnamen auf der Seite [Containerregistrierungen](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.ContainerRegistry%2Fregistries) im Azure-Portal.

Geben Sie den [**Administratorbenutzernamen für die Azure Container Registry**](../container-registry/container-registry-authentication.md#admin-account) an, der der Azure Container Registry-Instanz mit Ihrem Containerimage zugeordnet ist. Benutzername und Kennwort (nächster Schritt) sind erforderlich, um sicherzustellen, dass Ihr Unternehmen Zugriff auf die Registrierung hat. Um den Administratorbenutzernamen und das zugeordnete Kennwort abzurufen, legen Sie die Eigenschaft **admin-enabled** (Von Administrator aktiviert) an der Azure-Befehlszeilenschnittstelle auf **True** fest. Optional können Sie im Azure-Portal **Administratorbenutzer** auf **Aktivieren** festlegen.

:::image type="content" source="media/iot-edge/example-iot-update-container-registry.png" alt-text="Darstellung des Dialogfelds „Containerregistrierung aktualisieren“.":::

#### <a name="call-out-description"></a>Beschreibungen zu den Nummern

1. Administratorbenutzer

<br>Geben Sie das **Administratorkennwort für die Azure Container Registry** für den Administratorbenutzernamen an, der der Azure Container Registry-Instanz mit Ihrem Containerimage zugeordnet ist. Benutzername und Kennwort sind erforderlich, um sicherzustellen, dass Ihr Unternehmen Zugriff auf die Registrierung hat. Sie können das Kennwort im Azure-Portal abrufen, indem Sie zu **Containerregistrierung** > **Zugriffsschlüssel** wechseln oder die Azure-Befehlszeilenschnittstelle und den Befehl [show](/cli/azure/acr/credential#az-acr-credential-show) verwenden.

:::image type="content" source="media/iot-edge/example-iot-access-keys.png" alt-text="Darstellung des Zugriffsschlüssel-Bildschirms im Azure-Portal.":::

#### <a name="call-out-descriptions"></a>Beschreibungen zu den Nummern

1. Zugriffsschlüssel
2. Username
3. Kennwort

Geben Sie den **Repositorynamen innerhalb der Azure Container Registry** an, die Ihr Image enthält. Sie geben den Namen des Repositorys an, wenn Sie das Image per Push an die Registrierung übertragen. Sie finden den Namen des Repositorys auf der Seite [Container Registry](https://azure.microsoft.com/services/container-registry/) > **Repositorys**. Weitere Informationen finden Sie unter [Anzeigen von Azure Container Registry-Repositorys im Azure-Portal](../container-registry/container-registry-repositories.md). Nachdem der Name festgelegt wurde, kann er nicht mehr geändert werden. Verwenden Sie für jedes Angebot in Ihrem Konto einen eindeutigen Namen.

> [!NOTE]
> Verschlüsselte Azure Container Registry für die Zertifizierung von Edge-Modulen wird nicht unterstützt. Azure Container Registry sollten ohne aktivierte Verschlüsselung erstellt werden.

## <a name="image-versions"></a>Imageversionen

Kunden müssen imstande sein, Updates beim Azure Marketplace automatisch zu erhalten, wenn Sie ein Update veröffentlichen. Wenn sie kein Update ausführen möchten, müssen sie die Möglichkeit haben, bei einer bestimmten Version Ihres Images zu bleiben. Sie können dies erreichen, indem Sie für jedes Update des Images neue Imagetags hinzufügen.

Wählen Sie **Imageversion hinzufügen** aus, um ein **Imagetag** hinzuzufügen, das auf die neueste Version Ihres Images auf allen unterstützten Plattformen verweist. Es muss außerdem ein Versionstag enthalten (beispielsweise beginnend mit xx.xx.xx, wobei xx eine Zahl ist). Kunden sollten [Manifesttags](https://github.com/estesp/manifest-tool) verwenden, um mehrere Zielplattformen anzugeben. Zudem müssen alle Tags, auf die von einem Manifesttag verwiesen wird, hinzugefügt werden, damit wir sie hochladen können. Alle Manifesttags (mit Ausnahme des aktuellsten Tags) müssen entweder mit X.Y- oder X.Y.Z- beginnen, wobei X, Y und Z ganze Zahlen sind. Wenn ein aktuelles Tag beispielsweise auf `1.0.1-linux-x64`, `1.0.1-linux-arm32` und `1.0.1-windows-arm32` verweist, müssen diese sechs Tags zum Feld hinzugefügt werden. Weitere Details zu Tags und Versionsverwaltung finden Sie unter [Vorbereiten der technischen Ressourcen für das IoT-Edge-Modul](iot-edge-technical-asset.md).

> [!TIP]
> Fügen Sie Ihrem Image ein Testtag hinzu, damit Sie das Image beim Testen erkennen können.

## <a name="default-deployment-settings"></a>Standardbereitstellungseinstellungen

Definieren Sie die gängigsten Einstellungen zum Bereitstellen Ihres IoT Edge -Moduls (optional). Sie können die Bereitstellungen von Kunden optimieren, indem diese Ihr IoT Edge-Modul mit diesen vorkonfigurierten Standardeinstellungen starten können.

**Standardrouten**. Der IoT Edge-Hub verwaltet die Kommunikation zwischen Modulen, IoT Hub und Geräten. Sie können Standardrouten für die Datenein- und -ausgabe zwischen Modulen und dem IoT Hub festlegen. So können Sie Nachrichten flexibel an das gewünschte Ziel senden, ohne zusätzliche Dienste zum Verarbeiten von Nachrichten zu benötigen oder zusätzlichen Code schreiben zu müssen. Routen werden mit Name-Wert-Paaren erstellt. Sie können Namen für bis zu fünf Standardrouten definieren, von denen jeder bis zu 512 Zeichen lang sein darf.

Verwenden Sie die ordnungsgemäße [Routensyntax](../iot-edge/module-composition.md#declare-routes) in Ihrem Routenwert (normalerweise definiert als „FROM/message/* INTO $upstream“). Dies bedeutet, dass alle Nachrichten, die von beliebigen Modulen gesendet werden, an Ihren IoT Hub geleitet werden. Um auf Ihr Modul zu verweisen, verwenden Sie seinen Standardmodulnamen, der aus Ihrem **Angebotsnamen** ohne Leerzeichen und Sonderzeichen besteht. Um auf weitere Module zu verweisen, die derzeit noch nicht bekannt sind, verwenden Sie die Konvention <VON_MODULNAME>, um Ihren Kunden deutlich zu machen, dass sie diese Informationen aktualisieren müssen. Ausführliche Informationen zu IoT Edge-Routen finden Sie unter [Deklarieren von Routen](../iot-edge/module-composition.md#declare-routes).

Wenn beispielsweise das Modul ContosoModule an ContosoInput nach Eingaben und an ContosoOutput nach Ausgaben lauscht, ist es sinnvoll, die folgenden beiden Standardrouten zu definieren:

- Name 1: ToContosoModule
- Wert 1: FROM /messages/modules/<VON_MODULNAME>/outputs/* INTO BrokeredEndpoint("/modules/ContosoModule/inputs/ContosoInput")
- Name 2: FromContosoModuleToCloud
- Wert 2: FROM /messages/modules/ContonsoModule/outputs/ContosoOutput INTO $upstream

**Gewünschte Standardeigenschaften für Modulzwilling**. Ein Modulzwilling ist ein auf dem IoT Hub gespeichertes JSON-Dokument mit den Zustandsinformationen für eine Modulinstanz, einschließlich der gewünschten Eigenschaften. Gewünschte Eigenschaften werden in Verbindung mit gemeldeten Eigenschaften zum Synchronisieren von Modulkonfigurationen oder -zuständen verwendet. Das Lösungs-Back-End kann gewünschte Eigenschaften festlegen, die vom Modul gelesen werden können. Das Modul kann außerdem Änderungsbenachrichtigungen zu den gewünschten Eigenschaften erhalten. Gewünschte Eigenschaften werden aus bis zu fünf Name-Wert-Paaren erstellt, und jeder Standardwert muss weniger als 512 Zeichen umfassen. Sie können bis zu fünf Namen und Werte für gewünschte Eigenschaften von Gerätezwillingen definieren. Die Werte für gewünschte Eigenschaften von Gerätezwillingen müssen gültige JSON-Werte ohne Escapezeichen und ohne Arrays sein. Sie können eine geschachtelte Hierarchie mit maximal vier Ebenen aufweisen. In einem Szenario, in dem ein für einen Standardwert erforderlicher Parameter nicht sinnvoll ist (z. B. die IP-Adresse eines Kundenservers), können Sie einen Parameter als Standardwert hinzufügen. Weitere Informationen zu gewünschten Eigenschaften von Gerätezwillingen finden Sie unter [Definieren oder Aktualisieren gewünschter Eigenschaften](../iot-edge/module-composition.md#define-or-update-desired-properties).

Wenn ein Modul z. B. mithilfe von gewünschten Eigenschaften von Gerätezwillingen eine dynamisch konfigurierbare Aktualisierungsrate unterstützt, empfiehlt es sich, die folgende gewünschte Standardeigenschaft für Gerätezwillinge zu definieren:

- Name 1: RefreshRate
- Wert 1: 60

**Default environment variables** (Standardumgebungsvariablen). Umgebungsvariablen geben zusätzliche Informationen zu einem Modul an, die den Konfigurationsvorgang vereinfachen. Umgebungsvariablen werden mithilfe von Name-Wert-Paaren erstellt. Jeder Name und Wert einer Standardumgebungsvariablen muss weniger als 512 Zeichen aufweisen. Sie können bis zu fünf davon definieren. Wenn ein für einen Standardwert erforderlicher Parameter nicht sinnvoll ist (z. B. die IP-Adresse eines Kundenservers), können Sie einen Parameter als Standardwert hinzufügen.

Wenn vor dem Start eines Moduls beispielsweise die Zustimmung zu den Nutzungsbedingungen erforderlich ist, können Sie die folgende Standardumgebungsvariable definieren:

- Name 1: ACCEPT_EULA
- Wert 1: J

**Standardoptionen für die Containererstellung**. Optionen für die Containererstellung steuern die Erstellung des Docker-Containers für das IoT Edge-Modul. IOT Edge unterstützt Optionen zur Containererstellung der Docker-Engine-API. Alle Optionen finden Sie unter [Container auflisten](https://docs.docker.com/engine/api/v1.30/#operation/ContainerList). Das Feld mit den Erstellungsoptionen muss gültigen JSON-Code ohne Escapezeichen und mit weniger als 512 Zeichen enthalten.

Wenn für ein Modul beispielsweise die Bindung an einen Port erforderlich ist, definieren Sie die folgenden Erstellungsoptionen:

"HostConfig":{"PortBindings":{"5012/tcp":[{"HostPort":"5012"}]}

## <a name="samples"></a>Beispiele

Hier sehen Sie ein Beispiel für Plandetails im Azure Marketplace (die aufgelisteten Preise dienen lediglich als Beispiele und stellen keine tatsächlichen Kosten dar):

:::image type="content" source="media/iot-edge/example-iot-azure-marketplace-plan.png" alt-text="Darstellung von Plandetails im Azure Marketplace.":::

#### <a name="call-out-descriptions"></a>Beschreibungen zu den Nummern

1. Angebotsname
2. Planname
3. Planbeschreibung

<br>Hier sehen Sie ein Beispiel für die Plandetails im Azure-Portal (die aufgelisteten Preise dienen lediglich als Beispiele und stellen keine tatsächlichen Kosten dar):

:::image type="content" source="media/iot-edge/example-iot-azure-marketplace-plan-details.png" alt-text="Darstellung der Plandetails im Azure-Portal.":::

#### <a name="call-out-descriptions"></a>Beschreibungen zu den Nummern

1. Angebotsname
2. Planname
3. Planbeschreibung

Wählen Sie **Entwurf speichern** und **← Planübersicht** im linken Navigationsmenü aus, um zur Übersichtsseite des Plans zurückzukehren.

## <a name="next-steps"></a>Nächste Schritte

- Für das **Co-Selling mit Microsoft** (optional) wählen Sie die entsprechende Option im linken Navigationsmenü aus. Ausführliche Informationen finden Sie unter [Partner-Engagement für Co-Selling](./co-sell-overview.md).
- Wenn Sie kein Co-Selling einrichten oder fertig sind, ist es an der Zeit, [Ihr Angebot zu überprüfen und zu veröffentlichen](review-publish-offer.md).