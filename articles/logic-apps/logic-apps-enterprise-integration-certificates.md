---
title: Hinzufügen von Zertifikaten zum Schützen von B2B-Nachrichten in Workflows
description: Fügen Sie Zertifikate zum Schutz von B2B-Nachrichten in Azure Logic Apps-Workflows mit dem Enterprise Integration Pack hinzu.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 09/23/2021
ms.openlocfilehash: e2d2bc58c5a523667eef62b21791e953701f7bc1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128550090"
---
# <a name="secure-messages-using-certificates-for-workflows-in-azure-logic-apps"></a>Schützen von Nachrichten mithilfe von Zertifikaten für Workflows in Azure Logic Apps

Wenn Sie vertrauliche Nachrichten in einem B2B-Workflow (Business-to-Business) einer Logik-App austauschen möchten, können Sie die Sicherheit dieser Kommunikation durch die Verwendung von Zertifikaten erhöhen. Ein Zertifikat ist ein digitales Dokument, das die Kommunikation auf folgende Weise schützt:

* Überprüfen der Identitäten der Teilnehmer an der elektronischen Kommunikation

* Verschlüsseln des Nachrichteninhalts

* Digitales Signieren der Nachrichten

Sie können in Ihren Workflows die folgenden Zertifikattypen verwenden:

* [Öffentliche Zertifikate](https://en.wikipedia.org/wiki/Public_key_certificate), die Sie über eine öffentliche [Zertifizierungsstelle (ZS)](https://en.wikipedia.org/wiki/Certificate_authority) im Internet erwerben müssen. Für diese Zertifikate sind keine Schlüssel erforderlich.

* Private Zertifikate oder [*selbstsignierte Zertifikate*](https://en.wikipedia.org/wiki/Self-signed_certificate), die Sie selbst erstellen und ausstellen. Für diese Zertifikate sind jedoch private Schlüssel erforderlich.

Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](logic-apps-overview.md). Weitere Informationen zur B2B-Unternehmensintegration finden Sie unter [Workflows für die B2B-Unternehmensintegration mit Azure Logic Apps und dem Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Konto und ein Azure-Abonnement. Sollten Sie noch kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Eine [Integrationskontoressource](logic-apps-enterprise-integration-create-integration-account.md), in der Sie Artefakte wie Handelspartner, Vereinbarungen, Zertifikate usw. für die Verwendung in Ihrer Unternehmensintegration und in B2B-Workflows definieren und speichern. Diese Ressource muss die folgenden Anforderungen erfüllen:

  * Sie muss demselben Azure-Abonnement zugeordnet sein wie Ihre Logik-App-Ressource.

  * Sie muss sich am selben Standort oder in derselben Azure-Region wie Ihre Logik-App-Ressource befinden.

  * Wenn Sie den [Ressourcentyp **Logik-App (Verbrauch)** ](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, müssen Sie [Ihr Integrationskonto mit Ihrer Logik-App-Ressource verknüpfen](logic-apps-enterprise-integration-create-integration-account.md#link-account), bevor Sie Ihre Artefakte in Ihrem Workflow verwenden können.

    Zum Erstellen und Hinzufügen von Zertifikaten für die Verwendung in **Logik-App (Verbrauch)** -Workflows benötigen Sie noch keine Logik-App-Ressource. Wenn Sie jedoch bereit sind, diese Zertifikate in Ihren Workflows zu verwenden, benötigt Ihre Logik-App-Ressource ein verknüpftes Integrationskonto, das diese Zertifikate speichert.

  * Wenn Sie den [Ressourcentyp **Logik-App (Standard)** ](logic-apps-overview.md#resource-type-and-host-environment-differences) verwenden, benötigt Ihr Integrationskonto keine Verbindung mit Ihrer Logik-App-Ressource. Sie ist aber trotzdem erforderlich, um andere Artefakte wie Partner, Vereinbarungen und Zertifikate zusammen mit den [AS2](logic-apps-enterprise-integration-as2.md)-, [X12](logic-apps-enterprise-integration-x12.md)- und [EDIFACT](logic-apps-enterprise-integration-edifact.md)-Vorgängen zu speichern. Ihr Integrationskonto muss darüber hinaus weitere Anforderungen erfüllen. So muss es z. B. dasselbe Azure-Abonnement und denselben Standort wie Ihre Logik-App-Ressource verwenden.

    > [!NOTE]
    > Derzeit unterstützt nur der Ressourcentyp **Logik-App (Verbrauch)** [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)-Vorgänge. Der Ressourcentyp **Logik-App (Standard)** umfasst keine [RosettaNet](logic-apps-enterprise-integration-rosettanet.md)-Vorgänge.

* Für private Zertifikate müssen die folgenden Voraussetzungen erfüllt sein:

  * Sie müssen einen privaten Schlüssel in [Azure Key Vault](../key-vault/general/overview.md) hinzufügen und benötigen dessen **Schlüsselnamen**. Weitere Informationen finden Sie unter [Hinzufügen Ihres privaten Schlüssels in Azure Key Vault](../key-vault/certificates/certificate-scenarios.md#import-a-certificate).

  * Autorisieren Sie den Azure Logic Apps-Dienst für das Ausführen von Vorgängen für Ihren Schlüsseltresor. Verwenden Sie zum Erteilen des Zugriffs auf den Azure Logic Apps-Dienstprinzipal den PowerShell-Befehl [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy). Beispiel:

    `Set-AzKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`

    [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

  * [Fügen Sie Ihrem Schlüsseltresor ein entsprechendes öffentliches Zertifikat hinzu.](#add-public-certificate) Dieses Zertifikat wird in der [Vereinbarung in den **Sende**- und **Empfangseinstellungen** für das Signieren und Verschlüsseln von Nachrichten](logic-apps-enterprise-integration-agreements.md) angezeigt. Sehen Sie sich z. B. die [Referenz für AS2-Nachrichteneinstellungen in Azure Logic Apps](logic-apps-enterprise-integration-as2-message-settings.md) an.

* Mindestens zwei [Parteien](logic-apps-enterprise-integration-partners.md) (Handelspartner) und eine [Vereinbarung zwischen diesen Partnern](logic-apps-enterprise-integration-agreements.md) in Ihrem Integrationskonto. Eine Vereinbarung erfordert sowohl einen Host- als auch einen Gastpartner. Außerdem erfordert eine Vereinbarung, dass beide Partner denselben oder einen kompatiblen Qualifizierer für die *Geschäftsidentität* verwenden, der für eine AS2-, X12-, EDIFACT- oder RosettaNet-Vereinbarung geeignet ist.

* Optional die Logik-App-Ressource und den Workflow, in der bzw. dem Sie das Zertifikat verwenden möchten. Für den Workflow ist ein Trigger erforderlich, der den Workflow Ihrer Logik-App startet. Wenn Sie noch keinen Logik-App-Workflow erstellt haben, sehen Sie sich den [Schnellstart: Erstellen Ihrer ersten Logik-App](quickstart-create-first-logic-app-workflow.md) an.

<a name="add-public-certificate"></a>

## <a name="add-a-public-certificate"></a>Hinzufügen eines öffentlichen Zertifikats

Um ein *öffentliches Zertifikat* in Ihrem Workflow zu verwenden, müssen Sie Ihrem Integrationskonto zunächst das Zertifikat hinzufügen.

1. Geben Sie im [Azure-Portal](https://portal.azure.com) in das Suchfeld `integration accounts` ein, und wählen Sie **Integrationskonten** aus.

1. Wählen Sie unter **Integrationskonten** das Integrationskonto aus, dem Sie das Zertifikat hinzufügen möchten.

1. Wählen Sie im Menü des Integrationskontos unter **Einstellungen** den Eintrag **Zertifikate** aus.

1. Wählen Sie im Bereich **Zertifikate** die Option **Hinzufügen** aus.

1. Geben Sie im Bereich **Zertifikat hinzufügen** die folgenden Informationen zu dem Zertifikat an:

   | Eigenschaft | Erforderlich | Wert | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | **Name** | Ja | <*certificate-name*> (Zertifikatname) | Der Name Ihres Zertifikats (in diesem Beispiel `publicCert`) |
   | **Zertifikattyp** | Ja | **Public** | Der Typ Ihres Zertifikats |
   | **Certificate** | Ja | <*certificate-file-name*> (Name der Zertifikatdatei) | Wählen Sie neben dem Feld **Zertifikat** das Ordnersymbol aus, um die Zertifikatsdatei, die Sie hinzufügen möchten, zu suchen und auszuwählen. |
   |||||

   ![Screenshot von Azure-Portal und Integrationskonto mit ausgewählter Option „Hinzufügen“ und dem Bereich „Zertifikat hinzufügen“ mit Details zum öffentlichen Zertifikat](media/logic-apps-enterprise-integration-certificates/public-certificate-details.png)

1. Wenn Sie fertig sind, wählen Sie **OK**.

   Azure lädt das Zertifikat nach dem Überprüfen Ihrer Auswahl hoch.

   ![Screenshot von Azure-Portal und Integrationskonto mit dem öffentlichen Zertifikat in der Liste „Zertifikate“](media/logic-apps-enterprise-integration-certificates/new-public-certificate.png)

<a name="add-public-certificate"></a>

## <a name="add-a-private-certificate"></a>Hinzufügen eines privaten Zertifikats

Um ein *privates Zertifikat* in Ihrem Workflow zu verwenden, müssen Sie Ihrem Integrationskonto zunächst das Zertifikat hinzufügen. Stellen Sie sicher, dass auch die [Voraussetzungen für private Zertifikate](#prerequisites) erfüllt sind.

1. Geben Sie im [Azure-Portal](https://portal.azure.com) in das Suchfeld `integration accounts` ein, und wählen Sie **Integrationskonten** aus.

1. Wählen Sie unter **Integrationskonten** das Integrationskonto aus, dem Sie das Zertifikat hinzufügen möchten.

1. Wählen Sie im Menü des Integrationskontos unter **Einstellungen** den Eintrag **Zertifikate** aus.

1. Wählen Sie im Bereich **Zertifikate** die Option **Hinzufügen** aus.

1. Geben Sie im Bereich **Zertifikat hinzufügen** die folgenden Informationen zu dem Zertifikat an:

   | Eigenschaft | Erforderlich | Wert | Beschreibung |
   |----------|----------|-------|-------------|
   | **Name** | Ja | <*certificate-name*> (Zertifikatname) | Der Name Ihres Zertifikats (in diesem Beispiel `privateCert`) |
   | **Zertifikattyp** | Ja | **Privat** | Der Typ Ihres Zertifikats |
   | **Certificate** | Ja | <*certificate-file-name*> (Name der Zertifikatdatei) | Wählen Sie neben dem Feld **Zertifikat** das Ordnersymbol aus, um die Zertifikatsdatei, die Sie hinzufügen möchten, zu suchen und auszuwählen. Im Schlüsseltresor mit Ihrem privaten Schlüssel ist die Datei, die Sie hinzufügen, das öffentliche Zertifikat. |
   | **Ressourcengruppe** | Ja | <*integration-account-resource-group*> (Ressourcengruppe des Integrationskontos) | Die Ressourcengruppe Ihres Integrationskontos (in diesem Beispiel `Integration-Account-RG`) |
   | **Schlüsseltresor** | Ja | <*key-vault-name*> (Name des Schlüsseltresors) | Der Name Ihres Schlüsseltresors |
   | **Schlüsselname** | Ja | <*key-name*> | Der Name Ihres Schlüssels |
   |||||

   ![Screenshot von Azure-Portal und Integrationskonto mit ausgewählter Option „Hinzufügen“ und dem Bereich „Zertifikat hinzufügen“ mit Details zum privaten Zertifikat](media/logic-apps-enterprise-integration-certificates/private-certificate-details.png)

1. Wenn Sie fertig sind, wählen Sie **OK**.

   Azure lädt das Zertifikat nach dem Überprüfen Ihrer Auswahl hoch.

   ![Screenshot von Azure-Portal und Integrationskonto mit dem privaten Zertifikat in der Liste „Zertifikate“](media/logic-apps-enterprise-integration-certificates/new-private-certificate.png)

## <a name="next-steps"></a>Nächste Schritte

* [Austauschen von AS2-Nachrichten](logic-apps-enterprise-integration-as2.md)
* [Austauschen von EDIFACT-Nachrichten](logic-apps-enterprise-integration-edifact.md)
* [Austauschen von X12-Nachrichten](logic-apps-enterprise-integration-x12.md)
* [Austauschen von RosettaNet-Nachrichten](logic-apps-enterprise-integration-rosettanet.md)