---
title: Plug-In für die automatische Erfassung von Klickanalysen für das Application Insights-JavaScript-SDK
description: Installation und Verwendung des Plug-Ins für die automatische Erfassung von Klickanalysen für das Application Insights-JavaScript-SDK.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/14/2021
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: e1a0ab71ce8d5970b1239f70a058ee7a663117da
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132700"
---
# <a name="click-analytics-auto-collection-plugin-for-application-insights-javascript-sdk"></a>Plug-In für die automatische Erfassung von Klickanalysen für das Application Insights-JavaScript-SDK

Dieses Plug-In verfolgt automatisch Klickereignisse auf Webseiten und verwendet Attribute vom Typ „data-*“ in HTML-Elementen, um Ereignistelemetriedaten aufzufüllen.

## <a name="getting-started"></a>Erste Schritte

Benutzer können das Plug-In für die automatische Erfassung von Klickanalysen über npm einrichten.

### <a name="npm-setup"></a>NPM-Setup

Installieren Sie das npm-Paket:

```bash
npm install --save @microsoft/applicationinsights-clickanalytics-js @microsoft/applicationinsights-web
```

```js

import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ClickAnalyticsPlugin } from '@microsoft/applicationinsights-clickanalytics-js';

const clickPluginInstance = new ClickAnalyticsPlugin();
// Click Analytics configuration
const clickPluginConfig = {
  autoCapture: true
};
// Application Insights Configuration
const configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: clickPluginConfig
  },
};

const appInsights = new ApplicationInsights({ config: configObj });
appInsights.loadAppInsights();
```

## <a name="snippet-setup-ignore-if-using-npm-setup"></a>Snippet-Setup (bei Verwendung von NPM-Setup ignorieren)

```html
<script type="text/javascript" src="https://js.monitor.azure.com/scripts/b/ext/ai.clck.2.6.2.min.js"></script>
<script type="text/javascript">
  var clickPluginInstance = new Microsoft.ApplicationInsights.ClickAnalyticsPlugin();
  // Click Analytics configuration
  var clickPluginConfig = {
    autoCapture : true,
    dataTags: {
      useDefaultContentNameOrId: true
    }
  }
  // Application Insights Configuration
  var configObj = {
    instrumentationKey: "YOUR INSTRUMENTATION KEY",
    extensions: [
      clickPluginInstance
    ],
    extensionConfig: {
      [clickPluginInstance.identifier] : clickPluginConfig
    },
  };
  // Application Insights Snippet code
  !function(T,l,y){<!-- Removed the Snippet code for brevity -->}(window,document,{
    src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js",
    crossOrigin: "anonymous",
    cfg: configObj
  });
</script>
```

## <a name="how-to-effectively-use-the-plugin"></a>Effektive Verwendung des Plug-Ins

1. Aus Klickereignissen generierte Telemetriedaten werden als `customEvents` im Application Insights-Abschnitt des Azure-Portals gespeichert.
2. Der `name` von customEvent wird den folgenden Regeln entsprechend aufgefüllt:
    1.  Die in `data-*-id` bereitgestellte `id` wird als customEvent-Name verwendet. Wenn das HTML-Element, auf das geklickt wird, beispielsweise das Attribut „data-sample-id“=„button1“ aufweist, wird „button1“ als customEvent-Name verwendet.
    2. Wenn kein solches Attribut vorhanden ist und `useDefaultContentNameOrId` in der Konfiguration auf `true` festgelegt ist, wird die HTML-Attribut-`id` des Elements, auf das geklickt wurde, oder der Inhaltsname des Elements als customEvent-Name verwendet. Sind sowohl `id` als auch Inhaltsname vorhanden, hat `id` Vorrang.
    3. Wenn `useDefaultContentNameOrId` FALSE ist, lautet der customEvent-Name „not_specified“.

    > [!TIP]
    > Es empfiehlt sich, `useDefaultContentNameOrId` auf TRUE festzulegen, damit sinnvolle Daten generiert werden.  
3. Durch `parentDataTag` werden die folgenden beiden Aktionen ausgeführt:
    1. Wenn dieses Tag vorhanden ist, ruft das Plug-In die `data-*`-Attribute und -Werte aus allen übergeordneten HTML-Elementen des Elements ab, auf das geklickt wurde.
    2. Um die Effizienz zu verbessern, verwendet das Plug-In dieses Tag als Flag. Wenn es gefunden wird, wird die weitere aufwärtsgerichtete Verarbeitung des DOM (Dokumentobjektmodell) beendet.
    
    > [!CAUTION]
    > Wenn `parentDataTag` verwendet wird, sucht das SDK in der gesamten Anwendung und nicht nur in dem HTML-Element, in dem Sie es verwendet haben, nach übergeordneten Tags.
4. Das vom Benutzer angegebene `customDataPrefix` muss immer mit `data-` beginnen. Beispiel: `data-sample-`. In HTML bilden die globalen `data-*`-Attribute eine Klasse von Attributen, die als benutzerdefinierte Datenattribute bezeichnet werden. Diese ermöglichen den Austausch proprietärer Informationen zwischen HTML und der DOM-Darstellung durch Skripts. Ältere Browser (Internet Explorer, Safari) löschen Attribute, die nicht interpretiert werden können – es sei denn, sie beginnen mit `data-`.

    Das Zeichen `*` in `data-*` kann durch einen beliebigen Namen ersetzt werden, der der [Regel zur Erstellung von XML-Namen](https://www.w3.org/TR/REC-xml/#NT-Name) entspricht, mit folgende Einschränkungen:
    - Der Name darf nicht mit „xml“ beginnen, weder in Groß-, noch in Kleinschreibung.
    - Der Name darf kein Semikolon (U+003A) enthalten.
    - Der Name darf keine Großbuchstaben enthalten.

## <a name="what-data-does-the-plugin-collect"></a>Welche Daten sammelt das Plugin

Nachfolgend werden einige der Schlüsseleigenschaften angezeigt, die standardmäßig erfasst werden, wenn das Plug-In aktiviert ist:

### <a name="custom-event-properties"></a>Eigenschaften des benutzerdefinierten Ereignisses
| Name                  | Beschreibung                            | Beispiel          |
| --------------------- | ---------------------------------------|-----------------|
| name                  | Die `name` des customEvent. Weitere Informationen zum Aufbau dieses Feldes finden Sie [hier](#how-to-effectively-use-the-plugin).| Info              |
| itemType              | Ereignistyp.                                      | CustomEvent      |
|sdkVersion             | Version des Application Insights SDK zusammen mit dem Click-Plug-In|javascript:2.6.2_ClickPlugin2.6.2|

### <a name="custom-dimensions"></a>Benutzerdefinierte Dimensionen
| Name                  | Beschreibung                            | Beispiel          |
| --------------------- | ---------------------------------------|-----------------|
| actionType            | Aktionstyp, der das Klickereignis verursacht hat. Kann mit links oder rechts angeklickt werden. | CL              |
| baseTypeSource        | Basistypquelle des benutzerdefinierten Ereignisses.                                      | ClickEvent      |
| clickCoordinates      | Koordinaten, an denen das Klickereignis ausgelöst wird.                            | 659X47          |
| Inhalt               | Platzhalter zum Speichern zusätzlicher `data-*` Attribute und Werte.            | [{sample1:value1, sample2:value2}] |
| pageName              | Titel der Seite, auf der das Klickereignis ausgelöst wird.                      | Beispieltitel    |
| parentId              | ID oder Name des übergeordneten Elements                                           | navbarContainer |

### <a name="custom-measurements"></a>Benutzerdefinierte Messungen
| Name                  | Beschreibung                            | Beispiel          |
| --------------------- | ---------------------------------------|-----------------|
| timeToAction          | Zeit in Millisekunden, die der Benutzer seit dem ersten Laden der Seite benötigt, um das Element anzuklicken | 87407              |

## <a name="configuration"></a>Konfiguration

| Name                  | type                               | Standard | Beschreibung                                                                                                                              |
| --------------------- | -----------------------------------| --------| ---------------------------------------------------------------------------------------------------------------------------------------- |
| autoCapture           | boolean                            | true    | Konfiguration der automatischen Erfassung.                                |
| Rückruf              | [IValueCallback](#ivaluecallback)  | NULL    | Rückrufkonfiguration.                               |
| pageTags              | Zeichenfolge                             | NULL    | Seitentags.                                             |
| dataTags              | [ICustomDataTags](#icustomdatatags)| NULL    | Benutzerdefinierte Datentags zum Überschreiben von Standardtags, die zum Erfassen von Klickdaten verwendet werden. |
| urlCollectHash        | boolean                            | false   | Aktiviert die Protokollierung von Werten nach einem #-Zeichen in der URL.                |
| urlCollectQuery       | boolean                            | false   | Aktiviert die Protokollierung der Abfragezeichenfolge der URL.                            |
| behaviorValidator     | Funktion                           | NULL  | Rückruffunktion, die für die `data-*-bhvr`-Wertüberprüfung verwendet werden soll. Weitere Informationen finden Sie im [Abschnitt „behaviorValidator“](#behaviorvalidator).|
| defaultRightClickBhvr | Zeichenfolge (oder) Zahl                 | ''      | Wert für das Standardverhalten, wenn ein Rechtsklickereignis aufgetreten ist. Dieser Wert wird überschrieben, wenn das Element das `data-*-bhvr`-Attribut aufweist. |
| dropInvalidEvents     | boolean                            | false   | Flag zum Löschen von Ereignissen, die keine nützlichen Klickdaten aufweisen.                                                                                   |

### <a name="ivaluecallback"></a>IValueCallback

| Name               | type     | Standard | Beschreibung                                                                             |
| ------------------ | -------- | ------- | --------------------------------------------------------------------------------------- |
| pageName           | Funktion | NULL    | Funktion zum Überschreiben des standardmäßigen pageName-Erfassungsverhaltens.                           |
| pageActionPageTags | Funktion | NULL    | Eine Rückruffunktion zur Erweiterung der standardmäßigen pageTags, die während des pageAction-Ereignisses erfasst werden.  |
| contentName        | Funktion | NULL    | Eine Rückruffunktion zum Auffüllen des angepassten contentName.                                 |

### <a name="icustomdatatags"></a>ICustomDataTags

| Name                      | type    | Standard   | Standardtag für die Verwendung in HTML |   Beschreibung                                                                                |
|---------------------------|---------|-----------|-------------|----------------------------------------------------------------------------------------------|
| useDefaultContentNameOrId | boolean | false     | N/V         |Sammelt das HTML-Standardattribut für contentName, wenn ein bestimmtes Element nicht mit dem customDataPrefix-Standardwert gekennzeichnet ist oder customDataPrefix vom Benutzer nicht angegeben wurde. |
| customDataPrefix          | Zeichenfolge  | `data-`   | `data-*`| Automatische Erfassung der Inhaltsnamen und -werte von Elementen, die mit dem angegebenen Präfix gekennzeichnet sind. Beispielsweise können `data-*-id` und `data-<yourcustomattribute>` in den HTML-Tags verwendet werden.   |
| aiBlobAttributeTag        | Zeichenfolge  | `ai-blob` |  `data-ai-blob`| Das Plug-In unterstützt JSON-Blobattribute anstelle einzelner `data-*`-Attribute. |
| metaDataPrefix            | Zeichenfolge  | NULL      | Nicht zutreffend  | Automatische Erfassung der Namen und Inhalte von META-Elementen mit dem angegebenen Präfix im HTML-Kopfteil. Beispielsweise kann `custom-` im HTML-META-Tag verwendet werden. |
| captureAllMetaDataContent | boolean | false     | N/V   | Automatische Erfassung der Namen und Inhalte aller META-Elemente im HTML-Kopfteil. Der Standardwert ist "false". Ist diese Option aktiviert, wird das angegebene metaDataPrefix überschrieben. |
| parentDataTag             | Zeichenfolge  | NULL      |  Nicht zutreffend  | Beendet das aufwärtsgerichtete Durchlaufen des DOM zum Erfassen der Inhaltsnamen und -werte von Elementen, sobald dieses Tag gefunden wird. Beispielsweise kann `data-<yourparentDataTag>` in den HTML-Tags verwendet werden.|
| dntDataTag                | Zeichenfolge  | `ai-dnt`  |  `data-ai-dnt`| HTML-Elemente mit diesem Attribut werden vom Plug-In zum Erfassen von Telemetriedaten ignoriert.|

### <a name="behaviorvalidator"></a>behaviorValidator

Die behaviorValidator-Funktionen überprüfen automatisch, ob markierte Verhaltensweisen im Code einer vordefinierten Liste entsprechen. Dadurch wird sichergestellt, dass markierte Verhaltensweisen mit der von Ihrem Unternehmen eingerichteten Taxonomie übereinstimmen. Es wird nicht verlangt oder erwartet, dass diese Funktion von den meisten Azure Monitor-Kunden verwendet wird, aber sie steht für erweiterte Szenarien zur Verfügung. Drei verschiedene behaviorValidator-Rückruffunktionen werden im Rahmen dieser Erweiterung verfügbar gemacht. Benutzer können jedoch ihre eigenen Rückruffunktionen verwenden, wenn die verfügbar gemachten Funktionen nicht ihren Anforderungen entsprechen. Der Zweck besteht darin, dass Sie Ihre eigene Verhaltensdatenstruktur einbringen, und das Plug-In verwendet diese Validierungsfunktion beim Extrahieren der Verhaltensweisen aus den Datentags.

| Name                   | Beschreibung                                                                        |
| ---------------------- | -----------------------------------------------------------------------------------|
| BehaviorValueValidator | Verwenden Sie diese Rückruffunktion, wenn es sich bei der Verhaltensdatenstruktur um ein Array aus Zeichenfolgen handelt.|
| BehaviorMapValidator   | Verwenden Sie diese Rückruffunktion, wenn es sich bei der Verhaltensdatenstruktur um ein Wörterbuch handelt.       |
| BehaviorEnumValidator  | Verwenden Sie diese Rückruffunktion, wenn es sich bei der Verhaltensdatenstruktur um eine Enumeration handelt.            |

#### <a name="sample-usage-with-behaviorvalidator"></a>Beispielsyntax mit behaviorValidator

```js
var clickPlugin = Microsoft.ApplicationInsights.ClickAnalyticsPlugin;
var clickPluginInstance = new clickPlugin();

// Behavior enum values
var behaviorMap = {
  UNDEFINED: 0, // default, Undefined

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Page Experience [1-19]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  NAVIGATIONBACK: 1, // Advancing to the previous index position within a webpage
  NAVIGATION: 2, // Advancing to a specific index position within a webpage
  NAVIGATIONFORWARD: 3, // Advancing to the next index position within a webpage
  APPLY: 4, // Applying filter(s) or making selections
  REMOVE: 5, // Applying filter(s) or removing selections
  SORT: 6, // Sorting content
  EXPAND: 7, // Expanding content or content container
  REDUCE: 8, // Sorting content
  CONTEXTMENU: 9, // Context Menu
  TAB: 10, // Tab control
  COPY: 11, // Copy the contents of a page
  EXPERIMENTATION: 12, // Used to identify a third party experimentation event
  PRINT: 13, // User printed page
  SHOW: 14, //  Displaying an overlay
  HIDE: 15, //  Hiding an overlay
  MAXIMIZE: 16, //  Maximizing an overlay
  MINIMIZE: 17, // Minimizing an overlay
  BACKBUTTON: 18, //  Clicking the back button

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Scenario Process [20-39]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  STARTPROCESS: 20, // Initiate a web process unique to adopter
  PROCESSCHECKPOINT: 21, // Represents a checkpoint in a web process unique to adopter
  COMPLETEPROCESS: 22, // Page Actions that complete a web process unique to adopter
  SCENARIOCANCEL: 23, // Actions resulting from cancelling a process/scenario

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Download [40-59]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  DOWNLOADCOMMIT: 40, // Initiating an unmeasurable off-network download
  DOWNLOAD: 41, // Initiating a download

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Search [60-79]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SEARCHAUTOCOMPLETE: 60, // Auto-completing a search query during user input
  SEARCH: 61, // Submitting a search query
  SEARCHINITIATE: 62, // Initiating a search query
  TEXTBOXINPUT: 63, // Typing or entering text in the text box

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Commerce [80-99]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIEWCART: 82, // Viewing the cart
  ADDWISHLIST: 83, // Adding a physical or digital good or services to a wishlist
  FINDSTORE: 84, // Finding a physical store
  CHECKOUT: 85, // Before you fill in credit card info
  REMOVEFROMCART: 86, // Remove an item from the cart
  PURCHASECOMPLETE: 87, // Used to track the pageView event that happens when the CongratsPage or Thank You page loads after a successful purchase
  VIEWCHECKOUTPAGE: 88, // View the checkout page
  VIEWCARTPAGE: 89, // View the cart page
  VIEWPDP: 90, // View a PDP
  UPDATEITEMQUANTITY: 91, // Update an item's quantity
  INTENTTOBUY: 92, // User has the intent to buy an item
  PUSHTOINSTALL: 93, // User has selected the push to install option

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Authentication [100-119]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNIN: 100, // User sign-in
  SIGNOUT: 101, // User sign-out

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Social [120-139]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SOCIALSHARE: 120, // "Sharing" content for a specific social channel
  SOCIALLIKE: 121, // "Liking" content for a specific social channel
  SOCIALREPLY: 122, // "Replying" content for a specific social channel
  CALL: 123, // Click on a "call" link
  EMAIL: 124, // Click on an "email" link
  COMMUNITY: 125, // Click on a "community" link

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Feedback [140-159]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VOTE: 140, // Rating content or voting for content
  SURVEYCHECKPOINT: 145, // reaching the survey page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Registration, Contact [160-179]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  REGISTRATIONINITIATE: 161, // Initiating a registration process
  REGISTRATIONCOMPLETE: 162, // Completing a registration process
  CANCELSUBSCRIPTION: 163, // Canceling a subscription
  RENEWSUBSCRIPTION: 164, // Renewing a subscription
  CHANGESUBSCRIPTION: 165, // Changing a subscription
  REGISTRATIONCHECKPOINT: 166, // Reaching the registration page/form

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Chat [180-199]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  CHATINITIATE: 180, // Initiating a chat experience
  CHATEND: 181, // Ending a chat experience

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Trial [200-209]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  TRIALSIGNUP: 200, // Signing-up for a trial
  TRIALINITIATE: 201, // Initiating a trial

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Signup [210-219]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  SIGNUP: 210, // Signing-up for a notification or service
  FREESIGNUP: 211, // Signing-up for a free service

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Referals [220-229]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  PARTNERREFERRAL: 220, // Navigating to a partner's web property

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Intents [230-239]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  LEARNLOWFUNNEL: 230, // Engaging in learning behavior on a commerce page (ex. "Learn more click")
  LEARNHIGHFUNNEL: 231, // Engaging in learning behavior on a non-commerce page (ex. "Learn more click")
  SHOPPINGINTENT: 232, // Shopping behavior prior to landing on a commerce page

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  // Video [240-259]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  VIDEOSTART: 240, // Initiating a video
  VIDEOPAUSE: 241, // Pausing a video
  VIDEOCONTINUE: 242, // Pausing or resuming a video.
  VIDEOCHECKPOINT: 243, // Capturing predetermined video percentage complete.
  VIDEOJUMP: 244, // Jumping to a new video location.
  VIDEOCOMPLETE: 245, // Completing a video (or % proxy)
  VIDEOBUFFERING: 246, // Capturing a video buffer event
  VIDEOERROR: 247, // Capturing a video error
  VIDEOMUTE: 248, // Muting a video
  VIDEOUNMUTE: 249, // Unmuting a video
  VIDEOFULLSCREEN: 250, // Making a video full screen
  VIDEOUNFULLSCREEN: 251, // Making a video return from full screen to original size
  VIDEOREPLAY: 252, // Making a video replay
  VIDEOPLAYERLOAD: 253, // Loading the video player
  VIDEOPLAYERCLICK: 254, //  Click on a button within the interactive player
  VIDEOVOLUMECONTROL: 255, //  Click on video volume control
  VIDEOAUDIOTRACKCONTROL: 256, // Click on audio control within a video
  VIDEOCLOSEDCAPTIONCONTROL: 257, //  Click on the closed caption control
  VIDEOCLOSEDCAPTIONSTYLE: 258, //  Click to change closed caption style
  VIDEORESOLUTIONCONTROL: 259, //  Click to change resolution

  ///////////////////////////////////////////////////////////////////////////////////////////////////
  //    Advertisement Engagement [280-299]
  ///////////////////////////////////////////////////////////////////////////////////////////////////
  ADBUFFERING: 283, // Ad is buffering
  ADERROR: 284, // Ad error
  ADSTART: 285, // Ad start
  ADCOMPLETE: 286, // Ad complete
  ADSKIP: 287, // Ad skipped
  ADTIMEOUT: 288, // Ad timed-out
  OTHER: 300 // Other
};

// Application Insights Configuration
var configObj = {
  instrumentationKey: "YOUR INSTRUMENTATION KEY",
  extensions: [clickPluginInstance],
  extensionConfig: {
    [clickPluginInstance.identifier]: {
      behaviorValidator: Microsoft.ApplicationInsights.BehaviorMapValidator(behaviorMap),
      defaultRightClickBhvr: 9
    },
  },
};
var appInsights = new Microsoft.ApplicationInsights.ApplicationInsights({
  config: configObj
});
appInsights.loadAppInsights();
```

## <a name="sample-app"></a>Beispiel-App

[Einfache Web-App mit aktiviertem Plug-In für die automatische Erfassung von Klickanalysen](https://go.microsoft.com/fwlink/?linkid=2152871).

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich das [GitHub-Repository](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-clickanalytics-js) und das [npm-Paket](https://www.npmjs.com/package/@microsoft/applicationinsights-clickanalytics-js) für das Plug-In für die automatische Sammlung von Klickanalysen an.
- Verwenden Sie die [Ereignisanalyse in Nutzungsoberflächen](usage-segmentation.md), um die wichtigsten Klicks zu analysieren und nach verfügbaren Dimensionen aufzuteilen.
- Suchen Sie Klickdaten in [Log Analytics](../logs/log-analytics-tutorial.md#write-a-query) in der Tabelle „CustomEvents“ unter dem Inhaltsfeld innerhalb des Attributs „customDimensions“. Weitere Anleitungen finden Sie in der [Beispiel-App](https://go.microsoft.com/fwlink/?linkid=2152871).
- Erstellen Sie eine [Arbeitsmappe](../visualize/workbooks-overview.md), oder [exportieren Sie Daten in Power BI](../logs/log-powerbi.md#integrating-queries), um benutzerdefinierte Visualisierungen von Klickdaten zu erstellen.
