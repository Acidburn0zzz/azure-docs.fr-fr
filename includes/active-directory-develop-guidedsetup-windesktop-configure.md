---
title: Fichier Include
description: Fichier Include
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/17/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 220723988f349bf015d2de7633af78782bc03bac
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203181"
---
## <a name="register-your-application"></a>Inscrivez votre application

Vous pouvez inscrire votre application de deux manières.

### <a name="option-1-express-mode"></a>Option 1 : Mode Express

Vous pouvez inscrire rapidement votre application en procédant comme suit :
1. Accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=windowsDesktop&step=configure).

2. Sélectionnez **Ajouter une application**.

3. Dans la zone **Nom de l’application**, attribuez un nom à votre application.

4. Vérifiez que la case **Guided Setup** (Installation guidée) est cochée et sélectionnez **Créer**.

5. Suivez les instructions à l’écran pour obtenir l’ID de l’application et collez-le dans votre code.

### <a name="option-2-advanced-mode"></a>Option 2 : Mode Avancé

Pour inscrire votre application et ajouter les informations d’inscription de l’application à votre solution, procédez comme suit :
1. Si vous n’avez pas encore inscrit votre application, accédez au [portail d’inscription des applications de Microsoft](https://apps.dev.microsoft.com/portal/register-app).

2. Sélectionnez **Ajouter une application**.

3. Dans la zone **Nom de l’application**, attribuez un nom à votre application.

4. Vérifiez que la case **Guided Setup** (Installation guidée) est décochée et sélectionnez **Créer**.

5. Sélectionnez **Ajouter une plateforme**, puis **Application native**, et cliquez sur **Enregistrer**.

6. Dans la zone **ID de l’application**, copiez l’identificateur global unique (GUID).

7. Accédez à Visual Studio, ouvrez le fichier *App.xaml.cs*, puis remplacez `your_client_id_here` par l’ID de l’application que vous venez d’inscrire et de copier.

    ```csharp
    private static string ClientId = "your_application_id_here";
    ```
