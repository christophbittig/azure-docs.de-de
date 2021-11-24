---
title: Verwalten einer Azure-Supportanfrage
description: Erfahren Sie mehr über das Anzeigen von Supportanfragen und das Senden von Nachrichten, das Hochladen von Dateien und das Verwalten von Optionen.
tags: billing
ms.topic: how-to
ms.date: 11/10/2021
ms.openlocfilehash: c74a6245da9023889be151415bce72ba0129881c
ms.sourcegitcommit: c434baa76153142256d17c3c51f04d902e29a92e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2021
ms.locfileid: "132179928"
---
# <a name="manage-an-azure-support-request"></a>Verwalten einer Azure-Supportanfrage

Nachdem Sie eine [Azure-Supportanfrage erstellt](how-to-create-azure-support-request.md) haben, können Sie sie im [Azure-Portal](https://portal.azure.com) verwalten. Anfragen können aber auch programmgesteuert mithilfe der [Azure-Supportticket-REST-API](/rest/api/support) oder der [Azure CLI](/cli/azure/azure-cli-support-request) erstellt und verwaltet werden.

Um eine Supportanfrage verwalten zu können, müssen Sie die Rolle [Besitzer](../../role-based-access-control/built-in-roles.md#owner), [Mitwirkender](../../role-based-access-control/built-in-roles.md#contributor) oder [Mitwirkender für Supportanfragen](../../role-based-access-control/built-in-roles.md#support-request-contributor) auf der Abonnementebene innehaben. Um eine Supportanfrage zu verwalten, die ohne Abonnement erstellt wurde, müssen Sie [Administrator](../../active-directory/roles/permissions-reference.md) sein.

## <a name="view-support-requests"></a>Anzeigen von Supportanfragen

Zeigen Sie Details und den Status von Supportanfragen an, indem Sie zu **Hilfe + Support** >  **Alle Supportanfragen** wechseln.

:::image type="content" source="media/how-to-manage-azure-support-request/all-requests-lower.png" alt-text="Alle Supportanfragen":::

Auf dieser Seite können Sie Supportanfragen durchsuchen, filtern und sortieren. Wählen Sie eine Supportanfrage aus, um Details anzuzeigen, einschließlich ihres Schweregrads und der zur Anfrage gehörenden Meldungen.

## <a name="send-a-message"></a>Senden einer Nachricht

1. Wählen Sie auf der Seite **Alle Supportanfragen** die Supportanfrage aus.

1. Wählen Sie auf der Seite **Supportanfrage** die Option **Neue Nachricht** aus.

1. Geben Sie Ihre Nachricht ein, und wählen Sie **Submit** (Senden) aus.

## <a name="change-the-severity-level"></a>Ändern des Schweregrads

> [!NOTE]
> Der maximale Schweregrad richtet sich nach Ihrem [Supportplan](https://azure.microsoft.com/support/plans).

1. Wählen Sie auf der Seite **Alle Supportanfragen** die Supportanfrage aus.

1. Wählen Sie auf der Seite **Supportanfrage** die Option **Change** (Ändern) aus.

1. Im Azure-Portal wird einer von zwei Bildschirmen angezeigt, der davon abhängt, ob die Anfrage bereits einem Supporttechniker zugewiesen ist:

    - Wurde Ihre Anfrage noch nicht zugewiesen, wird ein Bildschirm wie dieser angezeigt. Wählen Sie einen neuen Schweregrad aus, und wählen Sie dann **Change** (Ändern) aus.

        :::image type="content" source="media/how-to-manage-azure-support-request/unassigned-can-change-severity.png" alt-text="Auswählen eines neuen Schweregrads":::

    - Wenn Ihre Anfrage zugewiesen wurde, wird ein Bildschirm wie dieser angezeigt. Wählen Sie **OK** aus, und erstellen Sie dann eine [neue Nachricht](#send-a-message), um eine Änderung des Schweregrads anzufordern.

        :::image type="content" source="media/how-to-manage-azure-support-request/assigned-cant-change-severity.png" alt-text="Neuer Schweregrad kann nicht ausgewählt werden":::

## <a name="allow-collection-of-advanced-diagnostic-information"></a>Sammeln erweiterter Diagnoseinformationen zulassen ​

Wenn Sie eine Supportanfrage erstellen, können Sie im Abschnitt **Erweiterte Diagnoseinformationen** entweder **Ja** oder **Nein** auswählen. Diese Option bestimmt, ob der Azure-Support [Diagnoseinformationen](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) zu Ihrer Azure-Ressourcen, zum Beispiel [Protokolldateien](how-to-create-azure-support-request.md#advanced-diagnostic-information-logs), erfassen kann, die möglicherweise zur Behebung Ihres Problems beitragen. Der Azure-Support kann nur dann auf erweiterte Diagnoseinformationen zugreifen, wenn Ihr Fall über das Azure-Portal erstellt wurde und Sie die Erlaubnis dazu erteilt haben.

So ändern Sie Ihre Auswahl bei **Erweiterte Diagnoseinformationen**, nachdem die Anforderung erstellt wurde:

1. Wählen Sie auf der Seite **Alle Supportanfragen** die Supportanfrage aus.

1. Suchen Sie auf der Seite **Supportanfrage** nach **Erweiterten Dignoseinformationen**, und wählen Sie dann **Ändern** aus.

1. Wählen Sie **Ja** oder **Nein** aus, und klicken Sie dann auf **OK**, um dies zu bestätigen.

    :::image type="content" source="media/how-to-manage-azure-support-request/grant-permission-manage.png" alt-text="Erteilen von Berechtigungen für Diagnoseinformationen":::

## <a name="upload-files"></a>Hochladen von Dateien

Sie können mithilfe der Dateiupload-Option Diagnosedateien oder andere Dateien hochladen, die Sie als relevant für eine Supportanfrage erachten.

1. Wählen Sie auf der Seite **Alle Supportanfragen** die Supportanfrage aus.

1. Navigieren Sie auf der Seite **Supportanfrage** zu Ihrer Datei, und wählen Sie dann **Upload** aus. Wiederholen Sie den Vorgang, wenn mehrere Dateien hochgeladen werden sollen.

    :::image type="content" source="media/how-to-manage-azure-support-request/file-upload.png" alt-text="Hochladen einer Datei":::

### <a name="file-upload-guidelines"></a>Richtlinien für den Dateiupload

Befolgen Sie diese Richtlinien, wenn Sie die Dateiupload-Option verwenden:

- Um Ihre Privatsphäre zu schützen, fügen Sie keine persönlichen Informationen in Ihren Upload ein.
- Der Dateiname darf nicht länger als 110 Zeichen sein.
- Sie können nicht mehrere Dateien hochladen.
- Dateien dürfen nicht größer als 4 MB sein.
- Alle Dateien müssen ein Suffix aufweisen, z. B. *.docx* oder *.xlsx*. In der folgenden Tabelle sind die für den Upload zulässigen Dateinamenerweiterungen aufgeführt.

| 0-9, A-C    | D – G   | H-N         | O-Q   | R-T      | U-W        | X-Z     |
|-------------|-------|-------------|-------|----------|------------|---------|
| .7z         | .dat  | .har        | .odx  | .rar     | .uccapilog | .xlam   |
| .a          | .db   | .hwl        | .oft  | RDL     | .uccplog   | .xlr    |
| .abc        | .DMP  | .ics        | .old  | .rdlc    | .udcx      | .xls    |
| .adm        | .do_  | .ini        | .one  | .re_     | .vb_       | .xlsb   |
| .aspx       | .doc  | .java       | .osd  | .remove  | .vbs_      | .xlsm   |
| .ATF        | .docm | .jpg        | .OUT  | .ren     | .vcf       | .xlsx   |
| .b          | .docx | .LDF        | .p1   | .rename  | .vsd       | .xlt    |
| .ba_        | .dotm | .letterhead | .pcap | .rft     | .wdb       | .xltx   |
| .bak        | .dotx | .lo_        | .pdb  | .rpt     | .wks       | .xml    |
| .blg        | .dtsx | .log        | .pdf  | .rte     | .wma       | XMLA   |
| .CA_        | .eds  | .lpk        | .piz  | .rtf     | .wmv       | .xps    |
| .CAB        | .emf  | .manifest   | .pmls | .run     | .wmz       | .xsd    |
| .cap        | .eml  | .master     | .png  | .saz     | .wps       | .xsn    |
| .catx       | .emz  | .mdmp       | .potx | SQL     | .wpt       | .xxx    |
| .CFG        | .err  | .mof        | .ppt  | .sqlplan | .wsdl      | .z_     |
| .compressed | .etl  | .mp3        | .pptm | .stp     | .wsp       | .z01    |
| .Config     | .evt  | .mpg        | .pptx | .svclog  | .wtl       | .z02    |
| .cpk        | .evtx | .ms_        | .prn  | .tdb     | -          | .zi     |
| .cpp        | .EX   | .msg        | .psf  | .tdf     | -          | .zi_    |
| .cs         | .ex_  | .mso        | .pst  | .text    | -          | .zip    |
| .CSV        | .ex0  | .msu        | .pub  | .thmx    | -          | .zip_   |
| .cvr        | .FRD  | .nfo        | -     | .tif     | -          | .zipp   |
| -           | .gif  | -           | -     | .trc     | -          | .zipped |
| -           | .guid | -           | -     | .TTD     | -          | .zippy  |
| -           | .gz   | -           | -     | .tx_     | -          | .zipx   |
| -           | -     | -           | -     | .txt     | -          | .zit    |
| -           | -     | -           | -     | -        | -          | .zix    |
| -           | -     | -           | -     | -        | -          | .zzz    |

## <a name="close-a-support-request"></a>Schließen einer Supportanfrage

Um eine Supportanfrage zu schließen, [senden Sie eine Nachricht,](#send-a-message) und teilen Sie uns mit, dass Sie die Anfrage schließen möchten.

## <a name="reopen-a-closed-request"></a>Erneutes Öffnen einer geschlossenen Anfrage

Um eine geschlossene Supportanfrage wieder zu öffnen, erstellen Sie eine [neue Nachricht](#send-a-message), die die Anfrage automatisch wieder öffnet.

## <a name="cancel-a-support-plan"></a>Kündigen eines Supportplans

Um einen Support-Plan zu kündigen, siehe [Kündigen eines Support-Plans](../../cost-management-billing/manage/cancel-azure-subscription.md#cancel-a-support-plan).

## <a name="next-steps"></a>Nächste Schritte

- Überprüfen Sie den Prozess zur [Erstellung einer Azure-Supportanfrage](how-to-create-azure-support-request.md).
- Erfahren Sie mehr über die [Azure Support Ticket REST API](/rest/api/support).
