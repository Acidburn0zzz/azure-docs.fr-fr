---
title: Démarrage rapide du service Azure SignalR serverless – Java
description: Un démarrage rapide pour utiliser le service Azure SignalR et Azure Functions afin de créer une salle de conversation.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: b2f89d3fbb427cc3e14be231d4ffae879e641f2c
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554875"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-java"></a>Démarrage rapide : Créer une salle de conversation avec Azure Functions et le service SignalR en utilisant Java

Le service Azure SignalR vous permet d’ajouter facilement des fonctionnalités en temps réel à votre application. Azure Functions est une plateforme serverless qui vous permet d’exécuter votre code sans gérer d’infrastructures. Dans ce démarrage rapide, découvrez comment utiliser le service SignalR et Functions pour générer une application serverless de conversation en temps réel.

## <a name="prerequisites"></a>Prérequis

Ce démarrage rapide peut être exécuté sur macOS, Windows ou Linux.

Assurez-vous qu’un éditeur de code tel que [Visual Studio Code](https://code.visualstudio.com/) est installé.

Installez [Azure Functions Core Tools (v2)](https://github.com/Azure/azure-functions-core-tools#installing) pour exécuter des applications Azure Function en local.

> [!NOTE]
> Pour utiliser les liaisons de SignalR Service en Java, assurez-vous que vous utilisez la version 2.4.419 ou une version ultérieure d’Azure Functions Core Tools (version de l’hôte 2.0.12332).

Pour installer des extensions, Azure Functions Core Tools requiert actuellement que le [kit SDK .NET Core](https://www.microsoft.com/net/download) soit installé. Toutefois, aucune connaissance de .NET n’est requise pour générer des applications de fonction Azure JavaScript.

Pour développer une application de fonction avec Java, les éléments suivants doivent être installés :

* [Java Developer Kit (JDK)](https://www.azul.com/downloads/zulu/) version 8.
* [Apache Maven](https://maven.apache.org) version 3.0 ou ultérieure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure sur <https://portal.azure.com/> avec votre compte Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

## <a name="configure-and-run-the-azure-function-app"></a>Configurer et exécuter l’application de fonction Azure

1. Dans le navigateur dans lequel le portail Azure est ouvert, vérifiez que l’instance du service SignalR que vous avez déployée précédemment a bien été créée en recherchant son nom dans la zone de recherche en haut du portail. Sélectionnez l’instance pour l’ouvrir.

    ![Rechercher l’instance du service SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Sélectionnez **Clés** pour afficher les chaînes de connexion de l’instance du service SignalR.

1. Sélectionnez et copiez la chaîne de connexion principale.

    ![Créer un service SignalR](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. Dans votre éditeur de code, ouvrez le dossier *chat/src/java* dans le référentiel cloné.

1. Renommez *local.settings.sample.json* en *local.settings.json*.

1. Dans le fichier **local.settings.json**, collez la chaîne de connexion dans la valeur du paramètre **AzureSignalRConnectionString**. Enregistrez le fichier .

1. Le fichier principal contenant les fonctions se trouve dans *src/main/java/com/function/Functions.java* :

    - **negotiate** : utilise la liaison d’entrée *SignalRConnectionInfo* pour générer et retourner des informations de connexion valides.
    - **sendMessage** : reçoit un message de conversation dans le corps de la demande et utilise la liaison de sortie *SignalR* pour diffuser le message à toutes les applications clientes connectées.

1. Dans le terminal, vérifiez que vous vous trouvez dans le dossier *chat/src/java*. Créez l’application de fonction.

    ```bash
    mvn clean package
    ```

1. Exécutez l’application de fonction localement.

    ```bash
    mvn azure-functions:run
    ```

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce démarrage rapide, vous avez généré et exécuté une application serverless en temps réel à l’aide de Maven. Ensuite, découvrez comment créer des fonctions Azure Java à partir de rien.

> [!div class="nextstepaction"]
> [Créer votre première fonction avec Java et Maven](../azure-functions/functions-create-first-java-maven.md)