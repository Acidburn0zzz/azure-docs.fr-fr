---
title: Analytique sur la base de connaissances
titleSuffix: Azure Cognitive Services
description: QnA Maker stocke tous les journaux de conversation et d’autres données de télémétrie si vous avez activé App Insights pendant la création de votre service QnA Maker. Exécutez les exemples de requêtes pour obtenir vos journaux de conversation à partir d’App Insights.
services: cognitive-services
author: tulasim88
manager: nitinme
displayName: chat history, history, chat logs, logs
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 02/04/2019
ms.author: tulasim
ms.openlocfilehash: 09369e760c654e7e56067e6da02bb772bffa7400
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960834"
---
# <a name="get-analytics-on-your-knowledge-base"></a>Obtenir des analyses sur votre base de connaissances

QnA Maker stocke tous les journaux de conversation et d’autres données de télémétrie si vous avez activé App Insights pendant la [création de votre service QnA Maker](./set-up-qnamaker-service-azure.md). Exécutez les exemples de requêtes pour obtenir vos journaux de conversation à partir d’App Insights.

1. Accédez à votre ressource App Insights.

    ![Sélectionnez votre ressource Application Insights](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Sélectionnez **Analytics**. Une nouvelle fenêtre dans laquelle vous pouvez interroger les données de télémétrie QnA Maker s’ouvre.

    ![Sélectionner Analytics](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. Collez la requête suivante et exécutez-la.

    ```query
        requests
        | where url endswith "generateAnswer"
        | project timestamp, id, name, resultCode, duration
        | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
        | join kind= inner (
        traces | extend id = operation_ParentId
        ) on id
        | extend question = tostring(customDimensions['Question'])
        | extend answer = tostring(customDimensions['Answer'])
        | project KbId, timestamp, resultCode, duration, question, answer
    ```

    Sélectionnez **Exécuter** pour exécuter la requête.

    ![Exécuter une requête](../media/qnamaker-how-to-analytics-kb/run-query.png)

## <a name="run-queries-for-other-analytics-on-your-qna-maker-knowledge-base"></a>Exécuter des requêtes pour d’autres analyses sur votre base de connaissances QnA Maker

### <a name="total-90-day-traffic"></a>Trafic total pendant 90 jours

```query
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
```

### <a name="total-question-traffic-in-a-given-time-period"></a>Question sur le trafic total dans un laps de temps donné

```query
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### <a name="user-traffic"></a>Trafic utilisateur

```query
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId 
    ) on id
    | extend UserId = tostring(customDimensions['UserId'])
    | summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### <a name="latency-distribution-of-questions"></a>Distribution de la latence de questions

```query
    //Latency distribution of questions
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse kind = regex name with *"(?i)knowledgebases/"KbId"/generateAnswer"
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## <a name="next-steps"></a>Étapes suivantes

> [!div class="nextstepaction"]
> [Gérer les clés](./key-management.md)
