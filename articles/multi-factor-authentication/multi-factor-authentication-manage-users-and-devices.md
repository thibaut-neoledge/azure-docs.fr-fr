---
title: Gestion des utilisateurs et des appareils par les administrateurs - Azure MFA | Microsoft Docs
description: "Cette section vous explique comment modifier les paramètres utilisateur tels que le fait de forcer les utilisateurs à réexécuter le processus de vérification."
documentationcenter: 
services: multi-factor-authentication
author: kgremban
manager: femila
editor: yossib
ms.assetid: aac3b922-7cc1-428c-9044-273579aa7b5a
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 847a8bdcf880b56f587f6759058825fd1965d29e
ms.openlocfilehash: 43ab735b91bf3f3f1e9631067827f2c456dd7b72
ms.lasthandoff: 03/01/2017


---
# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gestion des paramètres utilisateur avec Azure Multi-Factor Authentication dans le cloud
En tant qu’administrateur, vous pouvez gérer les paramètres des utilisateurs et des appareils :

* Demander aux utilisateurs sélectionnés de fournir à nouveau des méthodes de contact
* Supprimer les mots de passe d’application existants des utilisateurs
* Restaurer MFA sur tous les appareils interrompus d’un utilisateur

## <a name="require-selected-users-to-provide-contact-methods-again"></a>Demander aux utilisateurs sélectionnés de fournir à nouveau des méthodes de contact
Ce paramètre forcera l'utilisateur à réexécuter le processus d'inscription lorsqu'il se connecte. N'oubliez pas que les applications sans navigateur continueront de fonctionner si l'utilisateur dispose pour elles de mots de passe d'application.  Vous pouvez supprimer les mots de passe d’application des utilisateurs en choisissant **Supprimer tous les mots d'application existants, générés par les utilisateurs sélectionnés**.

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>Demander aux utilisateurs sélectionnés de fournir à nouveau des méthodes de contact
1. Connectez-vous au portail Azure Classic.
2. Cliquez à gauche sur Active Directory.
3. Sous Directory, cliquez sur le répertoire de l'utilisateur de votre choix, pour que celui-ci vous fournisse à nouveau sa méthode de contact.
4. En haut de la page, cliquez sur Utilisateurs.
5. En bas de la page, cliquez sur Gérer Multi-Factor Auth. Cette action ouvre la page relative à l’authentification multifacteur.
6. Recherchez l’utilisateur que vous souhaitez gérer et cochez la case située à côté du nom. Vous devrez peut-être modifier l'affichage en haut de la page.
7. Cela fera apparaître le lien **Gérer les paramètres utilisateur** à droite de la page. Cliquez dessus.
8. Cochez la case **Demander aux utilisateurs sélectionnés de fournir à nouveau des méthodes de contact**.
   ![Fournir des méthodes de contact](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
9. Cliquez sur Enregistrer.
10. Cliquez sur Fermer

## <a name="delete-users-existing-app-passwords"></a>Supprimer les mots de passe d’application existants des utilisateurs
Cela supprime tous les mots de passe d'application créés par un utilisateur. Les applications sans navigateur qui ont été associées à ces mots de passe d'application cesseront de fonctionner jusqu'à ce qu'un nouveau mot de passe soit créé.

### <a name="how-to-delete-users-existing-app-passwords"></a>Supprimer les mots de passe d’application existants des utilisateurs
1. Connectez-vous au portail Azure Classic.
2. Cliquez à gauche sur Active Directory.
3. Sous Directory, cliquez sur le répertoire de l'utilisateur pour lequel vous souhaitez supprimer les mots de passe d'application.
4. En haut de la page, cliquez sur Utilisateurs.
5. En bas de la page, cliquez sur Gérer Multi-Factor Auth. Cette action ouvre la page relative à l’authentification multifacteur.
6. Recherchez l’utilisateur que vous souhaitez gérer et cochez la case située à côté du nom. Vous devrez peut-être modifier l'affichage en haut de la page.
7. Cela fera apparaître le lien **Gérer les paramètres utilisateur** à droite de la page. Cliquez dessus.
8. Cochez la case **Supprimer tous les mots de passe d’application existants, générés par les utilisateurs sélectionnés**.
   ![Supprimer des mots de passe d'application](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
9. Cliquez sur Enregistrer.
10. Cliquez sur Fermer.

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>Restaurer MFA sur tous les appareils mémorisés d’un utilisateur
L’une des fonctionnalités configurables d’Azure Multi-Factor Authentication permet à vos utilisateurs de marquer les appareils comme approuvés. Pour plus d’informations, consultez [Configurer les paramètres d’Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md#remember-multi-factor-authentication-for-devices-that-users-trust).

Les utilisateurs ont la possibilité de refuser la vérification en deux étapes pendant un nombre configurable de jours sur les appareils qu’ils utilisent régulièrement. Si un compte est compromis ou si un appareil de confiance est perdu, vous devez être en mesure de supprimer l’état approuvé et d’exiger à nouveau la vérification en deux étapes.

Le paramètre **Restaurer l’authentification multifacteur pour tous les appareils mémorisés** signifie que l’utilisateur sera invité à procéder à la vérification en deux étapes lors de sa prochaine connexion, qu’il ait choisi ou non de marquer son appareil comme approuvé. 

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>Restaurer MFA sur tous les appareils interrompus d’un utilisateur
1. Connectez-vous au portail Azure Classic.
2. Cliquez à gauche sur Active Directory.
3. Sous Directory, cliquez sur le répertoire de l'utilisateur sur lequel vous souhaitez restaurer MFA.
4. En haut de la page, cliquez sur Utilisateurs.
5. En bas de la page, cliquez sur Gérer Multi-Factor Auth. Cette action ouvre la page relative à l’authentification multifacteur.
6. Recherchez l’utilisateur que vous souhaitez gérer et cochez la case située à côté du nom. Vous devrez peut-être modifier l'affichage en haut de la page.
7. Cela fera apparaître le lien **Gérer les paramètres utilisateur** à droite de la page. Cliquez dessus.
8. Cochez la case **Restaurer Multi-Factor Authentication sur tous les appareils mémorisés**
   ![Supprimer des mots de passe d’application](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. Cliquez sur Enregistrer.
10. Cliquez sur Fermer.

