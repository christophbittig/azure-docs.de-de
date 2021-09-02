---
title: React Native-Plug-In für das Application Insights JavaScript SDK
description: Hier erfahren Sie, wie Sie das React Native-Plug-In für das Application Insights JavaScript SDK installieren und verwenden.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 6b407fdc0614d94862257ce3bebc0f58f971f9d7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 08/13/2021
ms.locfileid: "122339502"
---
# <a name="react-native-plugin-for-application-insights-javascript-sdk"></a>React Native-Plug-In für das Application Insights JavaScript SDK

Das React Native-Plug-In für das Application Insights JavaScript SDK sammelt Geräteinformationen. Standardmäßig werden die folgenden Informationen automatisch von diesem Plug-In gesammelt:

- **Eindeutige Geräte-ID** (auch als Installations-ID bezeichnet)
- **Name des Gerätemodells** (z. B. iPhone X, Samsung Galaxy Fold, Huawei P30 Pro usw.)
- **Gerätetyp** (z. B. Mobiltelefon, Tablet usw.)

## <a name="requirements"></a>Requirements (Anforderungen)

Sie müssen Version 2.0.0 oder höher von `@microsoft/applicationinsights-web` verwenden. Dieses Plug-In funktioniert nur in React Native-Apps. Es funktioniert nicht mit [Apps, die das Expo-Framework verwenden](https://docs.expo.io/). Daher funktioniert es nicht mit Create React Native App.

## <a name="getting-started"></a>Erste Schritte

Installieren und verknüpfen Sie das [react-native-device-info](https://www.npmjs.com/package/react-native-device-info)-Paket. Halten Sie das `react-native-device-info`-Paket auf dem neuesten Stand, um die neuesten Gerätenamen mithilfe Ihrer App zu erfassen.

```zsh

npm install --save @microsoft/applicationinsights-react-native @microsoft/applicationinsights-web
npm install --save react-native-device-info
react-native link react-native-device-info

```

## <a name="initializing-the-plugin"></a>Initialisieren des Plug-Ins

Um dieses Plug-In verwenden zu können, müssen Sie es erstellen und Ihrer vorhandenen Application Insights-Instanz als `extension` hinzufügen.

```typescript
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactNativePlugin } from '@microsoft/applicationinsights-react-native';

var RNPlugin = new ReactNativePlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [RNPlugin]
    }
});
appInsights.loadAppInsights();

```

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zum JavaScript SDK finden Sie in der [Dokumentation zum Application Insights JavaScript SDK](javascript.md).
- Informationen zur Kusto-Abfragesprache und zum Abfragen von Daten in Log Analytics finden Sie in der [Übersicht über Protokollabfragen](../../azure-monitor/logs/log-query-overview.md).
