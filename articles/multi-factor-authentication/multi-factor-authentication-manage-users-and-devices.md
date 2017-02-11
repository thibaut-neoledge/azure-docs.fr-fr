---
title: "Rapports Azure Multi-Factor Authentication"
description: "Cette section vous explique comment modifier les paramètres utilisateur tels que le fait de forcer les utilisateurs à réexécuter le processus de vérification."
documentationcenter: 
services: multi-factor-authentication
author: kgremban
manager: femila
editor: curtand
ms.assetid: aac3b922-7cc1-428c-9044-273579aa7b5a
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 80eb9d882fa6b057623e0a5559baf111542359da


---
# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>Gestion des paramètres utilisateur avec Azure Multi-Factor Authentication dans le cloud
En tant qu'administrateur, vous pouvez gérer les paramètres utilisateur et périphérique.  

* [Demander aux utilisateurs sélectionnés de fournir à nouveau des méthodes de contact](#require-selected-users-to-provide-contact-methods-again)
* [Supprimer les mots de passe d’application existants des utilisateurs](#delete-users-existing-app-passwords)
* [Restaurer MFA sur tous les appareils interrompus d’un utilisateur](#restore-mfa-on-all-suspended-devices-for-a-user)

Ceci est utile si un ordinateur ou un appareil a été perdu ou volé ou si vous devez supprimer l’accès aux utilisateurs.

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
Les administrateurs ont la possibilité de restaurer Multi-Factor Authentication sur les appareils et les navigateurs des utilisateurs. Cette opération supprime la mémorisation MFA de tous les appareils et navigateurs de l’utilisateur. Celui-ci devra utiliser l’authentification multifacteur lors de sa prochaine connexion.

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




<!--HONumber=Nov16_HO3-->


