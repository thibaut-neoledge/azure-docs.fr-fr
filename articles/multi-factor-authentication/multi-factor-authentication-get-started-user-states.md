---
title: "Microsoft Azure Multi-Factor Authentication - État utilisateur"
description: "En savoir plus sur l’état utilisateur dans Azure MFA."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 999361daa2faebe3e88cab0b6085a938d6f40e9d
ms.openlocfilehash: 3079969783f589cb90f17eac116c5d57884db49f
ms.lasthandoff: 02/17/2017


---
# <a name="user-states-in-azure-multi-factor-authentication"></a>User States in Azure Multi-Factor Authentication (États des utilisateurs dans Azure Multi-Factor Authentication)
Les comptes d'utilisateur dans Azure Multi-Factor Authentication peuvent présenter les trois états suivants :

| State | Description | Applications affectées (autres que des navigateurs) | 
|:---:|:---:|:---:|
| Désactivé |État par défaut d’un nouvel utilisateur non inscrit dans l’authentification multifacteur Azure. |Non |
| Activé |L’utilisateur a été inscrit dans l’authentification multifacteur Azure, mais n’a pas été enregistré. Il sera invité à s’inscrire la prochaine fois qu’il se connectera. |Non.  Ils continuent de fonctionner jusqu’à ce que le processus d’inscription soit terminé. |
| Appliquée |L’utilisateur a été inscrit et a terminé le processus d’inscription pour utiliser l’authentification multifacteur Azure. |Oui.  Les applications requièrent des mots de passe d'application. |

## <a name="changing-a-user-state"></a>Modification de l’état utilisateur
L’état d’un utilisateur indique si un administrateur l’a inscrit dans l’authentification multifacteur Azure et s’il a terminé le processus d’inscription.

Tous les utilisateurs commencent avec l’état *désactivé*. Lorsque vous inscrivez des utilisateurs dans l’authentification multifacteur Azure, leur état devient *activé*. Lorsque les utilisateurs activés se connectent et terminent le processus d’inscription, leur état passe à *appliquée*.  

### <a name="view-user-states"></a>Afficher les états des utilisateurs

Pour accéder à la page où vous pouvez afficher et gérer les états des utilisateurs, procédez comme suit :

1. Connectez-vous en tant qu’administrateur au [portail Azure Classic](https://manage.windowsazure.com).
2. Sélectionnez **Active Directory**à gauche.
3. Sélectionnez le répertoire de l’utilisateur que vous souhaitez afficher.
   ![Sélectionner le répertoire - capture d’écran](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4. Sélectionnez **Utilisateurs**.
5. En bas de la page, sélectionnez **Gérer Multi-Factor Auth**. 
   ![Sélectionner Gérer Multi-Factor Auth - capture d’écran](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6. Un nouvel onglet, qui affiche les états de l’utilisateur, s’ouvre.
   ![État utilisateur pour l’authentification multifacteur - capture d’écran](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

### <a name="change-the-state-from-disabled-to-enabled"></a>Passer l’état de désactivé à activé

1. Utilisez les étapes précédentes pour accéder à la page des utilisateurs de l’authentification multifacteur. 
2. Recherchez l’utilisateur que vous souhaitez activer pour l’authentification multifacteur Azure. Vous devrez peut-être modifier l'affichage en haut de la page. Assurez-vous que l’état est **désactivé**.
   ![Rechercher un utilisateur - capture d’écran](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. Cochez la case en regard du nom de l’utilisateur.
4. À droite, sous les étapes rapides, cliquez sur **Activer**.
   ![Activer l’utilisateur sélectionné - capture d’écran](./media/multi-factor-authentication-get-started-cloud/user1.png)
5. Sélectionnez **Activer l’authentification multifacteur**.
   ![Activer l’authentification multifacteur - capture d’écran](./media/multi-factor-authentication-get-started-cloud/enable2.png)
6. Vous remarquerez que l’état est passé de **désactivé** à **activé**.
   ![L’utilisateur est maintenant activé - capture d’écran](./media/multi-factor-authentication-get-started-cloud/user.png)

Après avoir activé des utilisateurs, vous devez les en avertir par e-mail. Incluez le fait qu’ils devront s’inscrire la prochaine fois qu’ils se connecteront et que certaines applications sans navigateur peuvent ne pas fonctionner avec la vérification en deux étapes. Vous pouvez également inclure un lien vers notre [guide de l’utilisateur final sur l’authentification multifacteur Azure](./end-user/multi-factor-authentication-end-user.md) pour les aider à commencer. 

### <a name="to-change-the-state-from-enabledenforced-to-disabled"></a>Pour passer l’état d’activé/appliqué à désactivé

1. Utilisez les étapes de la rubrique [Afficher les états des utilisateurs](#view-user-states) pour accéder à la page des utilisateurs de l’authentification multifacteur.
6. Recherchez l’utilisateur que vous souhaitez désactiver. Vous devrez peut-être modifier l'affichage en haut de la page. Assurez-vous que l’état est soit **activé**, soit **appliqué**.
7. Cochez la case en regard du nom de l’utilisateur.
8. À droite, sous les étapes rapides, cliquez sur **Désactiver**.
   ![Désactiver l’utilisateur - capture d’écran](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
9. Vous êtes invité à confirmer l’opération. Cliquez sur **Oui**.
10. Si l’utilisateur a été désactivé avec succès, vous recevez un message qui le confirme. Cliquez sur **Fermer**.


