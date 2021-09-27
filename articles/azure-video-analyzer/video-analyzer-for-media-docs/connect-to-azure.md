---
title: Erstellen eines mit Azure verbundenen Kontos für Azure Video Analyzer for Media (früher Video Indexer)
description: Erfahren Sie, wie Sie ein mit Azure verbundenes Konto für Azure Video Analyzer for Media (früher Video Indexer) erstellen.
ms.topic: tutorial
ms.date: 01/14/2021
ms.author: juliako
ms.openlocfilehash: 13bf5c4eb6fc34848e90b80eb7cd242abf8824c9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662134"
---
# <a name="create-a-video-analyzer-for-media-account"></a>Erstellen eines Video Analyzer for Media-Kontos

Beim Erstellen eines Kontos für Azure Video Analyzer for Media (früher Video Analyzer) können Sie ein kostenloses Testkonto (für das Sie eine bestimmte Anzahl von kostenlosen Indizierungsminuten erhalten) oder eine kostenpflichtige Option (bei der Sie nicht aufgrund des Kontingents begrenzt sind) wählen. Bei der kostenlosen Testversion stellt der Video Analyzer bis zu 600 Minuten an kostenloser Indizierungszeit für Websitebenutzer und bis zu 2400 Minuten an kostenloser Indizierungszeit für API-Benutzer bereit. Bei der kostenpflichtigen Option erstellen Sie ein Video Analyzer for Media-Konto, das mit Ihrem Azure-Abonnement verbunden ist. Sie bezahlen pro Minute Indizierungszeit. Weitere Informationen finden Sie unter [Media Services – Preise](https://azure.microsoft.com/pricing/details/media-services/).

In diesem Artikel wird gezeigt, wie Sie ein Video Analyzer for Media-Konto erstellen, das mit einem Azure-Abonnement und einem Azure Media Services-Konto verknüpft ist. Außerdem werden die Schritte zur Verbindungsherstellung mit Azure unter Verwendung des automatischen Flows (Standard) vorgestellt. Darüber hinaus wird gezeigt, wie Sie eine manuelle Verbindung mit Azure herstellen (erweitert).

Wenn Sie von einem *Testkonto* zu einem *kostenpflichtigen* Konto für Video Analyzer for Media wechseln, haben Sie die Möglichkeit, alle Videos und Modellanpassungen in das neue Konto zu kopieren, wie im Abschnitt [Importieren Ihrer Inhalte aus dem Testkonto](#import-your-content-from-the-trial-account) erörtert.

Der Artikel behandelt auch das [Verknüpfen eines Video Analyzer for Media-Kontos mit Azure Government](#video-analyzer-for-media-in-azure-government).

## <a name="prerequisites-for-connecting-to-azure"></a>Voraussetzungen zum Herstellen einer Verbindung mit Azure

* Ein Azure-Abonnement.

    Wenn Sie noch kein Azure-Abonnement haben, können Sie sich für ein [kostenloses Azure-Testkonto](https://azure.microsoft.com/free/) anmelden.
* Eine Azure Active Directory-Domäne (Azure AD).

    Wenn Sie nicht über eine Azure AD-Domäne verfügen, können Sie diese mit Ihrem Azure-Abonnement erstellen. Weitere Informationen finden Sie unter [Verwalten von benutzerdefinierten Domänennamen in Ihrem Azure AD](../../active-directory/enterprise-users/domains-manage.md).
* Ein Benutzer in Ihrer Azure AD-Domäne mit der Rolle **Anwendungsadministrator**. Sie verwenden dieses Mitglied beim Herstellen der Verbindung für Ihr Video Analyzer for Media-Konto mit Azure.

    Bei diesem Benutzer sollte es sich um einen Azure AD Benutzer mit einem Geschäfts-, Schul-oder Unikonto handeln. Verwenden Sie kein persönliches Konto, wie outlook.com, live.com oder hotmail.com.

    ![Alle Azure AD-Benutzer](./media/create-account/all-aad-users.png)

### <a name="additional-prerequisites-for-automatic-flow"></a>Zusätzliche Voraussetzungen für den automatischen Flow

* Einen Benutzer und ein Mitglied in Ihrer Azure AD-Domäne.

    Sie verwenden dieses Mitglied beim Herstellen der Verbindung für Ihr Video Analyzer for Media-Konto mit Azure.

    Dieser Benutzer muss ein Mitglied Ihres Azure-Abonnements sein, der entweder der Rolle **Besitzer** oder den beiden Rollen **Mitwirkender** und **Benutzerzugriffsadministrator** zugewiesen sein muss. Ein Benutzer kann zweimal mit zwei Rollen hinzugefügt werden. Einmal mit „Mitwirkender“ und einmal mit „Benutzerzugriffsadministrator“. Weitere Informationen finden Sie unter [Anzeigen, welchen Zugriff ein Benutzer auf Azure-Ressourcen hat](../../role-based-access-control/check-access.md).

    ![Zugriffssteuerung](./media/create-account/access-control-iam.png)

### <a name="additional-prerequisites-for-manual-flow"></a>Zusätzliche Voraussetzungen für den manuellen Flow

* Registrieren Sie den EventGrid-Ressourcenanbieter mit dem Azure-Portal.

    Wechseln Sie im [Azure-Portal](https://portal.azure.com/) zu **Abonnements** > [Abonnement] > **ResourceProviders**.

    Suchen Sie nach **Microsoft.Media** und **Microsoft.EventGrid**. Klicken Sie auf **Registrieren**, falls der Status nicht „Registriert“ lautet. Die Registrierung nimmt einige Minuten in Anspruch.

    ![EventGrid](./media/create-account/event-grid.png)

## <a name="create-a-new-account-on-azure"></a>Erstellen eines neuen Kontos in Azure 

> [!NOTE]
> Wenn für Ihr Azure-Abonnement die zertifikatbasierte mehrstufige Authentifizierung verwendet wird, ist es wichtig, die folgenden Schritte auf einem Gerät auszuführen, auf dem die erforderlichen Zertifikate installiert sind.

1. Navigieren Sie zur [Video Analyzer for Media](https://www.videoindexer.ai/)-Website und melden Sie sich an.
1. Klicken Sie auf die Schaltfläche **Uneingeschränktes Konto erstellen**:

    ![Erstellen eines neuen Video Analyzer for Media-Kontos](./media/create-account/create-unlimited-account.png)
1. Wenn die Liste mit den Abonnements angezeigt wird, können Sie das gewünschte Abonnement auswählen.

    ![Verbinden von Video Analyzer for Media mit Azure](./media/create-account/new-account-on-azure-subscription.png)
1. Wählen Sie aus den unterstützten Standorten eine Azure-Region aus: „USA, Westen 2“, „Europa, Norden“ oder „Asien, Osten“.
1. Wählen Sie unter **Azure Media Services-Konto** eine dieser Optionen:

    * Wählen Sie **Neue Ressourcengruppe erstellen**, um ein neues Media Services-Konto zu erstellen. Geben Sie einen Namen für Ihre Ressourcengruppe an.

        Azure erstellt Ihr neues Konto unter Ihrem Abonnement, einschließlich eines neuen Azure Storage-Kontos.  
    * Wählen Sie **Vorhandene Ressource verwenden**, um ein vorhandenes Media Services-Konto zu verwenden. Wählen Sie Ihr Konto in der Liste mit den Konten aus.

        Ihr Media Services-Konto muss dieselbe Region wie Ihr Video Analyzer for Media-Konto aufweisen.

        > [!NOTE]
        > Um die Indizierungsdauer zu reduzieren und den Durchsatz zu verbessern, wird dringend empfohlen, den Typ und die Anzahl von [reservierten Einheiten](../../media-services/previous/media-services-scale-media-processing-overview.md ) in Ihrem Media Services-Konto auf **10 reservierte S3-Einheiten** festzulegen. Siehe [Verwenden des Portals zum Ändern der reservierten Einheiten](../../media-services/previous/media-services-portal-scale-media-processing.md). Die reservierten Einheiten werden für Ihr Konto in Rechnung gestellt. Weitere Informationen finden Sie bei den [Preisdetails](https://azure.microsoft.com/pricing/details/media-services/#analytics).
    * Um Ihre Verbindung manuell zu konfigurieren, wählen Sie den Link **Zur manuellen Konfiguration wechseln** aus.

        Ausführliche Informationen finden Sie im nachstehenden Abschnitt [Manuelle Verbindungsherstellung mit Azure (erweiterte Option)](#connect-to-azure-manually-advanced-option).
1. Wenn Sie fertig sind, wählen Sie **Erstellen** aus. Dieser Vorgang kann einige Minuten dauern.

    Nachdem Sie die Verbindung mit Azure hergestellt haben, wird Ihr neues Video Analyzer for Media-Konto in der Kontoliste angezeigt:

    ![Neues Konto](./media/create-account/new-account.png)
1. Stellen Sie sicher, dass der Streamingendpunkt des Media Services-Kontos aktiv ist, damit Sie Ihre Videos in der Video Analyzer for Media-Web-App wiedergeben können. (Klicken Sie auf „Starten“, wenn er angehalten ist.)

> [!TIP]
> Wechseln Sie zu **Einstellungen**, um einen benutzerfreundlichen Anzeigenamen für Ihr Konto festzulegen.

## <a name="connect-to-azure-manually-advanced-option"></a>Manuelle Verbindungsherstellung mit Azure (erweiterte Option)

Wenn die Verbindungsherstellung mit Azure nicht erfolgreich war, können Sie versuchen, das Problem über eine manuelle Verbindungsherstellung zu beheben.

> [!NOTE]
> Die folgenden drei Konten sollten unbedingt in derselben Region vorhanden sein: das Video Analyzer for Media-Konto, dessen Verbindung mit dem Media Services-Konto Sie herstellen, sowie das mit demselben Media Services-Konto verbundene Azure Storage-Konto.

### <a name="create-and-configure-a-media-services-account"></a>Erstellen und Konfigurieren eines Media Services-Konto

1. Verwenden Sie das [Azure](https://portal.azure.com/)-Portal, um ein Azure Media Services-Konto zu erstellen, wie beschrieben unter [Erstellen eines Kontos](../../media-services/previous/media-services-portal-create-account.md).

     Stellen Sie sicher, dass das Media Services-Konto mit den klassischen APIs erstellt wurde. 
 
    ![Media Services: klassische API](./media/create-account/enable-classic-api.png)


    Wenn Sie ein Speicherkonto für Ihr Media Services-Konto erstellen, wählen Sie als Kontoart **StorageV2** und für die Replikation **Georedundant** (GRS) aus.

    ![Neues Azure Media Services-Konto](./media/create-account/create-new-ams-account.png)

    > [!NOTE]
    > Notieren Sie sich die Media Services-Ressource und die Kontonamen. Sie benötigen diese Informationen für die Schritte im nächsten Abschnitt.
1. Ändern Sie den Typ und die Anzahl von [reservierten Einheiten](../../media-services/previous/media-services-scale-media-processing-overview.md ) im erstellten Media Services-Konto in **10 reservierte S3-Einheiten**. Siehe [Verwenden des Portals zum Ändern der reservierten Einheiten](../../media-services/previous/media-services-portal-scale-media-processing.md).

    Die reservierten Einheiten werden für Ihr Konto in Rechnung gestellt. Weitere Informationen finden Sie bei den [Preisdetails](https://azure.microsoft.com/pricing/details/media-services/#analytics).
1. Damit Sie Ihre Videos in der Video Analyzer for Media-Web-App abspielen können, müssen Sie den standardmäßigen **Streamingendpunkt** des neuen Media Services-Kontos starten.

    Wählen Sie im neuen Media Services-Konto **Streamingendpunkte** aus. Wählen Sie dann den Streamingendpunkt aus, und klicken Sie auf „Starten“.

    ![Streamingendpunkte](./media/create-account/create-ams-account-se.png)
4. Für die Video Analyzer for Media-Authentifizierung bei der Media Services-API muss eine AD-App erstellt werden. Die folgenden Schritte leiten Sie durch den Azure AD-Authentifizierungsvorgang, der in [Erste Schritte mit der Azure AD-Authentifizierung mithilfe des Azure-Portals](../../media-services/previous/media-services-portal-get-started-with-aad.md) beschrieben wird:

    1. Wählen Sie im neuen Media Services-Konto **API-Zugriff** aus.
    2. Wählen Sie die [Dienstprinzipalauthentifizierung](../../media-services/previous/media-services-portal-get-started-with-aad.md) aus.
    3. Abrufen der Client-ID und des geheimen Clientschlüssels

        Nachdem Sie **Einstellungen**->**Schlüssel** ausgewählt, eine **Beschreibung** hinzugefügt und auf **Speichern** geklickt haben, wird der Schlüsselwert aufgefüllt.

        Wenn der Schlüssel abläuft, muss der Kontobesitzer sich an den Video Analyzer for Media-Support wenden, um den Schlüssel zu erneuern.

        > [!NOTE]
        > Notieren Sie sich den Schlüsselwert und die Anwendungs-ID. Diese Informationen benötigen Sie für die Schritte im nächsten Abschnitt.

### <a name="connect-manually"></a>Manuelle Verbindungsherstellung

Klicken Sie auf der [Video Analyzer for Media](https://www.videoindexer.ai/)-Seite im Dialogfeld **Neues Konto für ein Azure-Abonnement erstellen** auf den Link **Zur manuellen Konfiguration wechseln**.

Geben Sie im Dialogfeld die folgenden Informationen ein:

|Einstellung|Beschreibung|
|---|---|
|Video Analyzer for Media-Kontoregion|Der Name der Region des Video Analyzer for Media-Kontos. Zur Verbesserung der Leistung und Senkung der Kosten wird dringend empfohlen, den Namen der Region anzugeben, in der sich die Azure Media Services-Ressourcen und das Azure Storage-Konto befinden. |
|Azure AD-Mandant|Der Name des Azure AD-Mandanten, z.B. „contoso.onmicrosoft.com“. Die Informationen zum Mandanten können über das Azure-Portal abgerufen werden. Platzieren Sie den Cursor in der oberen rechten Ecke über dem Namen des angemeldeten Benutzers. Der Name befindet sich rechts neben **Domäne**.|
|Abonnement-ID|Wählen Sie das Azure-Abonnement aus, unter dem die Verbindung erstellt werden soll. Die Abonnement-ID kann über das Azure-Portal abgerufen werden. Wählen Sie im linken Navigationsbereich **Alle Dienste** aus, und suchen Sie nach „Abonnements“. Wählen Sie **Abonnements** und dann in der Liste Ihrer Abonnements die gewünschte ID aus.|
|Name der Azure Media Services-Ressourcengruppen|Der Name der Ressourcengruppe, in der Sie das Media Services-Konto erstellt haben.|
|Name der Media Services-Ressource|Der Name des Azure Media Services-Kontos, das Sie im vorherigen Abschnitt erstellt haben.|
|Anwendungs-ID|Die Azure AD-Anwendungs-ID (mit Berechtigungen für das angegebene Media Services-Konto), die Sie im vorherigen Abschnitt erstellt haben.|
|Anwendungsschlüssel|Der Azure AD-Anwendungsschlüssel, den Sie im vorherigen Abschnitt erstellt haben. |

### <a name="import-your-content-from-the-trial-account"></a>Importieren Ihrer Inhalte aus dem *Testkonto*

Beim Erstellen eines neuen Kontos haben Sie die Möglichkeit, Ihre Inhalte aus dem *Testkonto* in das neue Konto zu importieren. Wenn Sie die Option *Importieren* im Dialogfeld **Neues Konto für ein Azure-Abonnement erstellen** aktivieren, werden alle Medien und Inhaltsmodellanpassungen aus dem *Testkonto* in das neue Konto kopiert.

Die Möglichkeit, den Inhalt zu importieren, gilt für den oben beschriebenen automatisierten Ansatz ebenso wie für den manuellen.

> [!NOTE]
> Der Inhalt kann aus jedem Konto nur einmal importiert werden.
>
> Das *Testkonto* ist in der Azure Government-Cloud nicht verfügbar.

## <a name="azure-media-services-considerations"></a>Überlegungen zu Azure Media Services

Für Azure Media Services gilt Folgendes:

* Wenn Sie beabsichtigen, eine Verbindung mit einem vorhandenen Media Services Konto herzustellen, stellen Sie sicher, dass das Media Services-Konto mit den klassischen APIs erstellt wurde. 
 
    ![Media Services: klassische API](./media/create-account/enable-classic-api.png)
* Falls Sie eine Verbindung mit einem vorhandenen Media Services-Konto hergestellt haben, ändert Video Analyzer for Media die vorhandene Konfiguration für **Reservierte Einheiten für Medien** nicht.

   Unter Umständen müssen Sie den Typ und die Anzahl von reservierten Einheiten für Medien gemäß Ihrer geplanten Last anpassen. Beachten Sie hierbei Folgendes: Wenn die Last hoch ist und Sie nicht über genügend Einheiten oder eine ausreichend hohe Geschwindigkeit verfügen, kann es bei der Videoverarbeitung zu Timeoutfehlern kommen.
* Bei der Verbindungsherstellung mit einem neuen Media Services-Konto startet Video Analyzer for Media dafür automatisch den standardmäßigen **Streamingendpunkt**:

    ![Media Services-Streamingendpunkt](./media/create-account/ams-streaming-endpoint.png)

    Streamingendpunkte benötigen relativ viel Zeit zum Starten. Deshalb kann es einige Minuten dauern, bis Ihre Videos gestreamt und in der Video Analyzer for Media-Web-App angesehen werden können.
* Falls Sie eine Verbindung mit einem vorhandenen Media Services-Konto hergestellt haben, ändert Video Analyzer for Media die Konfiguration für den standardmäßigen Streamingendpunkt nicht. Wenn kein ausgeführter **Streamingendpunkt** vorhanden ist, können Sie keine Videos über dieses Media Services-Konto oder in Video Analyzer for Media ansehen.
* Bei der automatischen Verbindungsherstellung legt Video Analyzer for Media die Einstellung für **Reservierte Einheiten für Medien** auf 10 S3-Einheiten fest:

    ![Reservierte Einheiten für Media Services](./media/create-account/ams-reserved-units.png)
    
## <a name="automate-creation-of-the-video-analyzer-for-media-account"></a>Automatisieren der Video Analyzer for Media-Kontoerstellung

Die Automatisierung des Erstellens des Kontos ist ein Prozess in zwei Schritten:
 
1. Verwenden von Azure Resource Manager zum Erstellen eines Azure Media Services-Kontos und einer Azure AD-Anwendung.

    Ein Beispiel für die Vorlage zum Erstellen eines Media Services-Kontos finden Sie [hier](https://github.com/Azure-Samples/media-services-v3-arm-templates).
1. Aufrufen von [Create-Account mit Media Services und der Azure AD-Anwendung](https://videoindexer.ai.azure.us/account/login?source=apim).

## <a name="video-analyzer-for-media-in-azure-government"></a>Video Analyzer for Media in Azure Government

### <a name="prerequisites-for-connecting-to-azure-government"></a>Voraussetzungen zum Herstellen einer Verbindung mit Azure Government

-   Ein Abonnement in [Azure Government](../../azure-government/index.yml)
- Ein Azure AD-Konto in Azure Government
- Alle Voraussetzungen für Berechtigungen und Ressourcen, wie oben unter [Voraussetzungen zum Herstellen einer Verbindung mit Azure](#prerequisites-for-connecting-to-azure) beschrieben. Informieren Sie sich über die [zusätzlichen Voraussetzungen für den automatischen Flow](#additional-prerequisites-for-automatic-flow) und die [zusätzlichen Voraussetzungen für den manuellen Flow](#additional-prerequisites-for-manual-flow).

### <a name="create-new-account-via-the-azure-government-portal"></a>Erstellen eines neuen Kontos über das Azure Government-Portal

> [!NOTE]
> Die Azure Government-Cloud umfasst keine *Testversion* von Video Analyzer for Media.

So erstellen Sie ein kostenpflichtiges Konto über das Video Analyzer for Media-Portal:

1. Besuchen Sie https://videoindexer.ai.azure.us. 
1. Melden Sie sich mit Ihrem Azure AD-Konto für Azure Government an.
1.  Wenn Sie über keine Video Analyzer for Media-Konten in Azure Government verfügen, bei denen Sie Besitzer oder Mitwirkender sind, erhalten Sie eine leere Umgebung, von der aus Sie Ihr Konto erstellen können. 

    Der Rest des Flows ist wie oben beschrieben. Nur die Regionen, aus denen ausgewählt wird, sind Government-Regionen, in denen Video Analyzer for Media verfügbar ist. 

    Wenn Sie bereits Mitwirkender oder Administrator eines bestehenden Video Analyzer for Media-Kontos in Azure Government sind, werden Sie zu diesem Konto weitergeleitet und können von dort aus bei Bedarf die Schritte zum Erstellen eines weiteren Kontos ausführen (wie oben beschrieben).
    
### <a name="create-new-account-via-the-api-on-azure-government"></a>Erstellen eines neuen Kontos über die API in Azure Government

Um ein kostenpflichtiges Konto in Azure Government zu erstellen, folgen Sie den Anweisungen zum [Erstellen eines kostenpflichtigen Kontos](). Dieser API-Endpunkt umfasst nur Government-Cloudregionen.

### <a name="limitations-of-video-analyzer-for-media-on-azure-government"></a>Einschränkungen von Video Analyzer for Media in Azure Government

*   In der Government-Cloud ist keine manuelle Inhaltsmoderation verfügbar. 

    Wenn in der öffentlichen Cloud Inhalte aufgrund einer Inhaltsmoderation als anstößig erachtet werden, kann der Kunde einen Mitarbeiter anfordern, der sich diese Inhalte ansieht und die Entscheidung möglicherweise rückgängig machen kann.  
*   Keine Testkonten. 
* Bing-Beschreibung – In der Government-Cloud werden wir keine Beschreibung der identifizierten Prominenten und benannten Entitäten präsentieren. Dies ist nur eine Funktion der Benutzeroberfläche. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Nachdem Sie dieses Tutorial abgeschlossen haben, löschen Sie Ressourcen, die Sie nicht verwenden möchten.

### <a name="delete-a-video-analyzer-for-media-account"></a>Löschen eines Video Analyzer for Media-Kontos

Wenn Sie ein Video Analyzer for Media-Konto löschen möchten, ist dies auf der Video Analyzer for Media-Website möglich. Sie müssen der Besitzer sein, um das Konto zu löschen.

Klicken Sie auf das Konto und dann auf **Einstellungen** -> **Dieses Konto löschen**. 

Das Konto wird in 90 Tagen dauerhaft gelöscht.

## <a name="firewall"></a>Firewall

Weitere Informationen finden Sie unter [Speicherkonto befindet sich hinter einer Firewall](faq.yml#can-a-storage-account-connected-to-the-media-services-account-be-behind-a-firewall).

## <a name="next-steps"></a>Nächste Schritte

Sie können programmgesteuert mit Ihrem Testkonto und/oder mit Ihren Video Analyzer for Media-Konten interagieren, die mit Azure verbunden sind. Befolgen Sie hierzu die Anweisungen unter [Verwenden von APIs](video-indexer-use-apis.md).

Sie sollten hierfür denselben Azure AD-Benutzer verwenden, den Sie beim Herstellen der Verbindung mit Azure genutzt haben.
