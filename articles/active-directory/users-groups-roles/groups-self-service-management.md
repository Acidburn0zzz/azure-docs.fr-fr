---
title: Configurer la gestion de groupes en libre-service - Azure Active Directory | Microsoft Docs
description: Créer et gérer des groupes de sécurité ou Office 365 dans Azure Active Directory et demander des appartenances aux groupes de sécurité ou Office 365
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5122c9142eccf12193e7e429a3af5ac44fbb8fd6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/18/2019
ms.locfileid: "58111325"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Configurer la gestion de groupes en libre-service dans Azure Active Directory 

Vous pouvez autoriser les utilisateurs à créer et gérer leurs propres groupes de sécurité ou les groupes Office 365 dans Azure Active Directory (Azure AD). Le propriétaire du groupe permettre approuver ou refuser les demandes d’appartenance et peut déléguer le contrôle de l’appartenance au groupe. Fonctionnalités de gestion de groupes en libre-service ne sont pas disponibles pour les groupes de sécurité à extension messagerie ou des listes de distribution. 

## <a name="self-service-group-membership-defaults"></a>Valeurs par défaut de l’appartenance de groupes en libre-service

Lorsque les groupes de sécurité sont créés dans le portail Azure ou à l’aide d’Azure AD PowerShell, seuls les propriétaires du groupe mettre à jour l’appartenance. Groupes de sécurité créés dans le [volet d’accès](https://account.activedirectory.windowsazure.com/r#/joinGroups) et tous les groupes Office 365 sont disponibles pour participer à pour tous les utilisateurs, approuvé par le propriétaire ou approuvée automatiquement. Dans le volet d’accès, vous pouvez modifier les options d’appartenance lorsque vous créez le groupe.

Groupes créés dans | Comportement de valeur par défaut de groupe de sécurité | Comportement par défaut de groupe Office 365
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Seuls les propriétaires peuvent ajouter des membres<br>Visibles mais non disponible pour joindre dans le volet d’accès | Ouvrir pour tous les utilisateurs
[Portail Azure](https://portal.azure.com) | Seuls les propriétaires peuvent ajouter des membres<br>Visibles mais non disponible pour joindre dans le volet d’accès<br>Propriétaire n’est pas affecté automatiquement lors de la création de groupe | Ouvrir pour tous les utilisateurs
[Volet d’accès](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Ouvrir pour tous les utilisateurs<br>Options d’appartenance peuvent être modifiées lorsque le groupe est créé. | Ouvrir pour tous les utilisateurs<br>Options d’appartenance peuvent être modifiées lorsque le groupe est créé.

## <a name="self-service-group-management-scenarios"></a>Scénarios de gestion de groupes en libre-service

* **Gestion de groupes déléguée** : prenons l’exemple d’un administrateur qui gère l’accès à une application SaaS utilisée dans son entreprise. La gestion de ces droits d’accès devenant fastidieuse, cet administrateur demande au propriétaire de l’entreprise d’ajouter un nouveau groupe. L’administrateur accorde l’accès à l’application au nouveau groupe et ajoute au groupe toutes les personnes accédant déjà à l’application. Le propriétaire de l’entreprise peut ainsi ajouter d’autres utilisateurs, qui sont automatiquement approvisionnés dans l’application. Il n’a pas besoin d’attendre que l’administrateur gère l’accès des utilisateurs. Si l’administrateur accorde la même autorisation à un responsable d’un groupe Professionnel différents, cette personne peut également gérer l’accès pour leur propre appartenance aux groupes. Le propriétaire de l’entreprise, ni le gestionnaire peut visualiser ou gérer les appartenances au groupe entre eux. L’administrateur a toujours la possibilité de voir l’ensemble des utilisateurs ayant accès à l’application et, si nécessaire, de bloquer les droits d’accès.
* **Gestion de groupes en libre-service** Prenons l’exemple de deux utilisateurs disposant tous deux d’un site SharePoint Online. Ils les gèrent indépendamment. Cependant, ils veulent les rendre accessibles à chacune des deux équipes. Pour cela, ils peuvent créer un groupe dans Azure AD, puis, dans SharePoint Online, chacun d’eux choisit ce même groupe pour lui donner accès aux deux sites. Ainsi, quand une personne souhaite obtenir un accès, elle en effectue la demande dans le volet d’accès, et dès que sa demande a été approuvée, elle obtient automatiquement un accès aux deux sites SharePoint Online. Par la suite, l’un des deux utilisateurs décide que toutes les personnes ayant accès au site doivent également pouvoir accéder à une application SaaS. L’administrateur de l’application SaaS peut ajouter des droits d’accès à l’application pour le site SharePoint Online. Les demandes qu’il approuvera par la suite donneront ainsi accès non seulement aux deux sites SharePoint Online, mais également à cette application SaaS.

## <a name="make-a-group-available-for-user-self-service"></a>Mettre un groupe à disposition en libre-service pour un utilisateur
1. Connectez-vous au [centre d’administration Azure AD](https://aad.portal.azure.com) en utilisant un compte d’administrateur général pour le répertoire.
2. Sélectionnez **Utilisateurs et groupes**, puis **Paramètres de groupe**.
3. Définissez **Gestion de groupes en libre-service activée** sur **Oui**.
4. Définissez **Les utilisateurs peuvent créer des groupes de sécurité** ou **Les utilisateurs peuvent créer des groupes Office 365** sur **Oui**.
   * Une fois ces paramètres activés, tous les utilisateurs présents dans votre répertoire sont autorisés à créer des groupes de sécurité et à ajouter des membres à ces groupes. Ces nouveaux groupes apparaissent également dans le volet d’accès de tous les autres utilisateurs. Si le paramètre de stratégie du groupe l’autorise, d’autres utilisateurs peuvent créer des demandes d’adhésion à ces groupes. 
   * Quand ces paramètres sont désactivés, les utilisateurs ne peuvent pas créer de groupes, ni modifier les groupes existants dont ils sont propriétaires. Cependant, ils peuvent toujours gérer les appartenances de ces groupes et approuver les demandes d’adhésion d’autres utilisateurs à leurs groupes.

Vous pouvez également utiliser **Utilisateurs pouvant gérer les groupes de sécurité** et **Utilisateurs pouvant gérer les groupes Office 365** pour obtenir un contrôle d’accès plus fin de la gestion de groupes en libre-service pour vos utilisateurs. Quand l’option **Les utilisateurs peuvent créer des groupes** est activée, tous les utilisateurs présents dans votre locataire sont autorisés à créer des groupes et à ajouter des membres à ces groupes. En définissant l’option sur **Certains**, vous restreignez la gestion de groupes à un groupe d’utilisateurs limité uniquement. Dans ce cas, vous devez ajouter des utilisateurs au groupe SSGMSecurityGroupsUsers avant que ceux-ci ne puissent créer des groupes et y ajouter des membres. En définissant **Utilisateurs pouvant utiliser le libre-service pour les groupes de sécurité** et **Utilisateurs pouvant gérer les groupes Office 365** sur **Tous**, vous autorisez tous les utilisateurs de votre locataire à créer des groupes.

Vous pouvez également utiliser **Groupe pouvant gérer les groupes de sécurité** ou **Groupe pouvant gérer les groupes Office 365** pour spécifier un groupe unique dont les membres peuvent utiliser le libre-service.

## <a name="next-steps"></a>Étapes suivantes
Ces articles fournissent des informations supplémentaires sur Azure Active Directory.

* [Gérer l’accès aux ressources avec les groupes Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Configuration des paramètres de groupe avec les applets de commande Azure Active Directory](groups-settings-cmdlets.md)
* [Gestion des applications dans Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Qu’est-ce qu’Azure Active Directory ?](../fundamentals/active-directory-whatis.md)
* [Intégrer des répertoires locaux à Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
