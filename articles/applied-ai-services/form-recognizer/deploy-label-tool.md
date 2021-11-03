---
title: Bereitstellen des Beispiel-Beschriftungstools der Azure-Formularerkennung
titleSuffix: Azure Applied AI Services
description: Erfahren Sie mehr über die verschiedenen Möglichkeiten, wie Sie das Beispiel-Beschriftungstool der Azure-Formularerkennung bereitstellen können, um das beaufsichtigte Lernen zu unterstützen.
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 07/02/2021
ms.author: lajanuar
ms.custom: ignite-fall-2021
ms.openlocfilehash: 5c5a260a1f5c1ab6cd6232dbd0ea9c1035a110cf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/02/2021
ms.locfileid: "131027016"
---
# <a name="deploy-the-sample-labeling-tool"></a>Bereitstellen des Beispiel-Beschriftungstools

Das Beispiel-Beschriftungstool der Azure-Formularerkennung ist eine Anwendung mit einer einfachen Benutzeroberfläche, mit der Sie Formulare (Dokumente) zum beaufsichtigten Lernen manuell beschriften können. In diesem Artikel finden Sie Links und Anweisungen zu folgenden Aufgaben:

* [Lokales Ausführen des Beispiel-Beschriftungstools](#run-the-sample-labeling-tool-locally)
* [Bereitstellen des Beispiel-Beschriftungstools in einer Azure-Containerinstanz](#deploy-with-azure-container-instances-aci)
* [Verwenden des Open-Source-OCR Form Labeling Tool und Mitwirken am Tool](#open-source-on-github)

## <a name="run-the-sample-labeling-tool-locally"></a>Lokales Ausführen des Beispiel-Beschriftungstools

Die schnellste Möglichkeit, um Daten zu beschriften, ist die lokale Ausführung des Beispiel-Beschriftungstools. In der folgenden Schnellstartanleitung wird gezeigt, wie Sie die Formularerkennungs-REST-API und das Beispiel-Beschriftungstool verwenden, um ein benutzerdefiniertes Modell mit manuell beschrifteten Daten zu trainieren.

* [Erste Schritte mit der Azure-Formularerkennung](label-tool.md)

## <a name="deploy-with-azure-container-instances-aci"></a>Bereitstellen mit Azure Container Instances (ACI)

Zum Bereitstellen des Beispiel-Beschriftungstools in einer Azure-Containerinstanz gibt es zwei Möglichkeiten. Beide Optionen werden verwendet, um das Beispiel-Beschriftungstool mit ACI auszuführen:

* [Verwenden des Azure-Portals](#azure-portal)
* [Verwenden der Azure-Befehlszeilenschnittstelle](#azure-cli)

### <a name="azure-portal"></a>Azure-Portal

Führen Sie die folgenden Schritte aus, um eine neue Ressource im Azure-Portal zu erstellen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/signin/index/) an.
2. Wählen Sie **Ressource erstellen**.
3. Wählen Sie anschließend **Web-App** aus.

   > [!div class="mx-imgBorder"]
   > ![Auswählen von „Web-App“](./media/quickstarts/create-web-app.png)

4. Vergewissern Sie sich zunächst, dass die Registerkarte **Grundlagen** ausgewählt ist. Jetzt müssen Sie einige Informationen angeben:

   > [!div class="mx-imgBorder"]
   > ![Auswählen von „Grundlagen“](./media/quickstarts/select-basics.png)
   * Abonnement: Wählen Sie ein vorhandenes Azure-Abonnement aus.
   * Ressourcengruppe: Sie können eine vorhandene Ressourcengruppe wiederverwenden oder eine neue Ressourcengruppe für das Projekt erstellen. Es wird empfohlen, eine neue Ressourcengruppe zu erstellen.
   * Name: Geben Sie einen Namen für Ihre Web-App ein.
   * Veröffentlichen: Wählen Sie **Docker-Container** aus.
   * Betriebssystem: Wählen Sie **Linux** aus.
   * Region: Wählen Sie eine für Sie geeignete Region aus.
   * Linux-Plan: Wählen Sie einen Tarif/Plan für Ihre App Service-App aus.

   > [!div class="mx-imgBorder"]
   > ![Konfigurieren der Web-App](./media/quickstarts/select-docker.png)

5. Wählen Sie als Nächstes die Registerkarte **Docker** aus.

   > [!div class="mx-imgBorder"]
   > ![Auswählen von „Docker“](./media/quickstarts/select-docker.png)

6. Konfigurieren Sie nun Ihren Docker-Container. Alle Felder sind erforderlich, sofern nicht anders angegeben:
<!-- markdownlint-disable MD025 -->

* Optionen: Wählen Sie **Einzelner Container** aus.
* Imagequelle: Wählen Sie **Private Registrierung** aus.
* Server-URL: Legen Sie die URL auf `https://mcr.microsoft.com` fest.
* Benutzername (optional): Erstellen Sie einen Benutzernamen.
* Kennwort (optional): Erstellen Sie ein sicheres Kennwort, das Sie sich gut merken können.
* Image und Tag: Legen Sie diese Einstellung auf `mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-2.1` fest.
* Continuous Deployment: Legen Sie diesen Wert auf **On** fest, wenn Sie automatische Aktualisierungen erhalten möchten, sobald das Entwicklungsteam Änderungen am Beispiel-Beschriftungstool vornimmt.
* Startbefehl: Legen Sie diese Einstellung auf `./run.sh eula=accept` fest.

> [!div class="mx-imgBorder"]
> ![Konfigurieren von Docker](./media/quickstarts/configure-docker.png)

* Wählen Sie als Nächstes **Überprüfen + Erstellen** und dann **Erstellen** aus, um die Web-App bereitzustellen. Nach der Bereitstellung können Sie unter der URL, die in der **Übersicht** für Ihre Ressource angegeben ist, auf die Web-App zugreifen.

### <a name="continuous-deployment"></a>Continuous Deployment

Nachdem Sie Ihre Web-App erstellt haben, können Sie die Option „Continuous Deployment“ aktivieren:

* Wählen Sie hierzu im linken Bereich die Option **Containereinstellungen** aus.
* Navigieren Sie im Hauptfenster zu „Continuous Deployment“, und wählen Sie nach Bedarf **Ein** oder **Aus** aus:

:::image type="content" source="media/label-tool/continuous-deployment.png" alt-text="Screenshot: Vergrößerung der Containereinstellungen für „Continuous Deployment“" lightbox="media/label-tool/continuous-deployment-bigger.png":::

> [!NOTE]
> Beim Erstellen Ihrer Web-App können Sie auch die Autorisierung/Authentifizierung konfigurieren. Für die ersten Schritte ist dies jedoch nicht erforderlich.

> [!IMPORTANT]
> Möglicherweise müssen Sie TLS für Ihre Web-App aktivieren, um sie unter ihrer `https`-Adresse anzuzeigen. Befolgen Sie die Anweisungen in [Aktivieren eines TLS-Endpunkts](../../container-instances/container-instances-container-group-ssl.md), um einen Sidecar-Container einzurichten, und aktivieren Sie dann TLS/SSL für Ihre Web-App.
<!-- markdownlint-disable MD001 -->
### <a name="azure-cli"></a>Azure CLI

Als Alternative zum Azure-Portal können Sie die Azure-Befehlszeilenschnittstelle (Azure CLI) zum Erstellen einer Ressource verwenden. Bevor Sie fortfahren, müssen Sie die [Azure CLI](/cli/azure/install-azure-cli) installieren. Sie können diesen Schritt überspringen, falls Sie sie bereits verwenden.

Es gibt einige Dinge, die Sie über diesen Befehl wissen sollten:

* `DNS_NAME_LABEL=aci-demo-$RANDOM` generiert einen zufälligen DNS-Namen.
* Dieses Beispiel setzt voraus, dass Sie über eine Ressourcengruppe verfügen, die Sie zum Erstellen einer Ressource verwenden können. Ersetzen Sie `<resource_group_name>` durch eine gültige Ressourcengruppe, die Ihrem Abonnement zugeordnet ist.
* Sie müssen angeben, wo die Ressource erstellt werden soll. Ersetzen Sie `<region name>` durch die gewünschte Region für die Web-App.
* Dieser Befehl akzeptiert automatisch die Lizenzbedingungen.

Führen Sie in der Azure CLI den folgenden Befehl aus, um eine Web-App-Ressource für das Beispiel-Beschriftungstool zu erstellen:

<!-- markdownlint-disable MD024 -->

```azurecli
DNS_NAME_LABEL=aci-demo-$RANDOM

az container create \
  --resource-group <resource_group_name> \
  --name <name> \
  --image mcr.microsoft.com/azure-cognitive-services/custom-form/labeltool:latest-2.1 \
  --ports 3000 \
  --dns-name-label $DNS_NAME_LABEL \
  --location <region name> \
  --cpu 2 \
  --memory 8 \
  --command-line "./run.sh eula=accept"

```

### <a name="connect-to-azure-ad-for-authorization"></a>Herstellen einer Verbindung mit Azure AD zur Autorisierung

Es wird empfohlen, die Web-App mit Azure Active Directory (Azure AD) zu verbinden. Durch diese Verbindung wird sichergestellt, dass nur Benutzer mit gültigen Anmeldeinformationen sich anmelden und Ihre Web-App verwenden können. Folgen Sie den Anweisungen unter [Konfigurieren Ihrer App Service-App ](../../app-service/configure-authentication-provider-aad.md), um eine Verbindung mit Azure Active Directory herzustellen.

## <a name="open-source-on-github"></a>Open-Source auf GitHub

Das OCR Form Labeling Tool steht auch als Open-Source-Projekt auf GitHub zur Verfügung. Bei dem Tool handelt es sich um eine mit React + Redux erstellte und in TypeScript geschriebene Webanwendung. Besuchen Sie [OCR Form Labeling Tool](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md), wenn Sie weitere Informationen benötigen oder sich beteiligen möchten.

## <a name="next-steps"></a>Nächste Schritte

Im Schnellstart zum [Trainieren mit Beschriftungen](label-tool.md) erfahren Sie, wie Sie mit dem Tool Trainingsdaten manuell beschriften und überwachtes Lernen ausführen.
