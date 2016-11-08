---
title: Gérer les paramètres de la vérification en deux étapes | Microsoft Docs
description: Gérez l’utilisation d’Azure Multi-Factor Authentication, notamment la modification des informations de contact ou la configuration des appareils.
services: multi-factor-authentication
keywords: client de l'authentification multifacteur, problème d'authentification, ID de corrélation
documentationcenter: ''
author: kgremban
manager: femila
editor: yossib

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: kgremban

---
# <a name="manage-your-settings-for-two-step-verification"></a>Gérer les paramètres de la vérification en deux étapes
Cet article répond aux questions en rapport avec la mise à jour des paramètres de Multi-Factor Authentication ou de la vérification en deux étapes. Si vous rencontrez des problèmes de connexion à votre compte, consultez [Problèmes avec la vérification en deux étapes](multi-factor-authentication-end-user-troubleshoot.md) pour obtenir de l’aide.

## <a name="where-to-find-the-settings-page"></a>Où trouver la page des paramètres
Selon la façon dont vous utilisez Azure Multi-Factor Authentication, vous pouvez modifier vos paramètres (comme votre numéro de téléphone) de différentes manières.

Si votre administrateur informatique vous a envoyé une URL spécifique ou des étapes pour gérer la vérification en deux étapes, suivez ces instructions. Dans le cas contraire, suivez celles indiquées ci-dessous. Si vous effectuez ces étapes et que vous ne voyez pas les mêmes options, cela signifie que votre entreprise ou établissement scolaire a personnalisé le portail. Demandez à l’administrateur de vous donner le lien à votre portail Azure Multi-Factor Authentication.

1. Connectez-vous à [https://myapps.microsoft.com](https://myapps.microsoft.com).  
2. En haut de la page, sélectionnez **profil**.  
3. Sélectionnez **Vérification de sécurité supplémentaire**.  
   
    ![MyApps](./media/multi-factor-authentication-end-user-manage/myapps1.png)
4. La page Vérification de sécurité supplémentaire se charge avec vos paramètres.
   
    ![Vérification](./media/multi-factor-authentication-end-user-manage-myapps/proofup.png)

## <a name="i-want-to-change-my-phone-number,-or-add-a-secondary-number"></a>Je souhaite modifier mon numéro de téléphone ou ajouter un numéro secondaire
Il est important de configurer un numéro de téléphone d’authentification secondaire.  Étant donné que vous utilisez probablement le même téléphone pour votre numéro de téléphone principal et votre application mobile, le numéro de téléphone secondaire est le seul moyen dont vous disposez pour revenir à votre compte en cas de perte ou de vol de votre téléphone.

> [!NOTE]
> Si vous n’avez pas accès à votre numéro de téléphone principal et que vous avez besoin d’aide pour accéder à votre compte, consultez nos rubriques d’aide dans [Problèmes avec la vérification en deux étapes](multi-factor-authentication-end-user-troubleshoot.md).
> 
> 

**Pour modifier votre numéro de téléphone principal :**  

1. Dans la page Vérification de sécurité supplémentaire, sélectionnez la zone de texte contenant votre numéro de téléphone et tapez votre nouveau numéro.  
2. Sélectionnez **Enregistrer**.  
3. Si ce numéro constitue votre option de vérification préférée, vous devez vérifier le nouveau numéro avant de pouvoir l’enregistrer.  

**Pour ajouter un numéro de téléphone secondaire :**  

1. Dans la page Vérification de sécurité supplémentaire, cochez la case **Autre téléphone d’authentification**.  
2. Entrez votre numéro de téléphone secondaire dans la zone de texte.  
3. Sélectionnez **Enregistrer**. Vos modifications sont apportées.  

## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one?"></a>Comment nettoyer Microsoft Authenticator sur mon ancien appareil et le transférer vers un autre ?
Quand vous désinstallez l’application de votre appareil ou que vous réinitialisez ce dernier, l’application n’est pas désactivée sur le serveur principal. Vous devez suivre la procédure décrite dans la section relative au [transfert vers un nouvel appareil](multi-factor-authentication-microsoft-authenticator.md#how-to-move-to-the-new-microsoft-authenticator-app).

## <a name="next-steps"></a>Étapes suivantes
* Obtenir des conseils de dépannage et de l’aide dans [Problèmes avec la vérification en deux étapes](multi-factor-authentication-end-user-troubleshoot.md)
* Configurer des [mots de passe d’application](multi-factor-authentication-end-user-app-passwords.md) pour les applications qui ne prennent pas en charge la vérification en deux étapes

<!--HONumber=Oct16_HO2-->


