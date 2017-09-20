---
title: Prise en main du fournisseur Azure Multi-Factor Auth | Microsoft Docs
description: "Découvrez comment créer un fournisseur Azure Multi-Factor Auth."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: a7dd5030-7d40-4654-8fbd-88e53ddc1ef5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/28/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.translationtype: HT
ms.sourcegitcommit: 4c2be7c35f678430d0ad83a3374ef25f68fd2509
ms.openlocfilehash: ed14a5a762bab20a1ccde699504dd21f25009b52
ms.contentlocale: fr-fr
ms.lasthandoff: 09/20/2017

---

# <a name="getting-started-with-an-azure-multi-factor-auth-provider"></a>Prise en main du fournisseur Azure Multi-Factor Auth
La vérification en deux étapes est disponible par défaut pour les administrateurs généraux disposant d’Azure Active Directory et les utilisateurs Office 365. Toutefois, si vous souhaitez tirer parti des [fonctionnalités avancées](multi-factor-authentication-whats-next.md), vous devez acheter la version complète d’Azure Multi-Factor Authentication (MFA).

Un fournisseur d’authentification multifacteur Azure permet de tirer parti des fonctionnalités fournies par la version complète de l’authentification multifacteur Azure. Il s’adresse aux utilisateurs **qui ne possèdent pas de licences via l’authentification multifacteur Azure (MFA), Azure AD Premium ou Enterprise Mobility + Security (EMS)**.  L’authentification multifacteur Azure, Azure AD Premium et EMS incluent la version complète de l’authentification Multifacteur Azure par défaut. Vous n’avez pas besoin d’un fournisseur d’authentification multifacteur Azure si vous possédez des licences.

Il sera en revanche nécessaire si vous souhaitez télécharger le Kit de développement logiciel (SDK).

> [!IMPORTANT]
> Pour télécharger le Kit de développement logiciel (SDK), vous devez créer un fournisseur d’authentification multifacteur Azure, même si vous disposez de licences EMS, AAD Premium ou Azure MFA.  Si vous créez un fournisseur d’authentification multifacteur Azure à cet effet et que vous avez déjà des licences, veillez à créer le fournisseur avec le modèle **Par utilisateur activé**. Ensuite, liez le fournisseur au répertoire qui contient les licences de l’authentification multifacteur Azure, Azure AD Premium ou EMS. Cette configuration garantit que vous n’êtes facturé que si vous avez plus d’utilisateurs uniques utilisant la vérification en deux étapes que le nombre de licences en votre possession.

## <a name="what-is-an-azure-multi-factor-auth-provider"></a>Qu’est-ce qu’un fournisseur Azure Multi-Factor Auth ?

Si vous n’avez pas de licences pour Azure Multi-Factor Authentication, vous pouvez créer un fournisseur d’authentification qui exige une vérification en deux étapes pour les utilisateurs. Si vous développez une application personnalisée et souhaitez activer Azure MFA, créez un fournisseur d’authentification et [téléchargez le SDK](multi-factor-authentication-sdk.md).

Il existe deux types de fournisseurs d’authentification, qui se distinguent par la façon dont votre abonnement Azure est facturé. L’option par authentification calcule le nombre d’authentifications effectuées pour votre locataire en un mois. Cette option est recommandée si de nombreux utilisateurs ne s’authentifient qu’occasionnellement (par exemple, si vous avez besoin de MFA pour une application personnalisée). L’option par utilisateur calcule le nombre de personnes sur votre client qui effectuent la vérification en deux étapes au cours d’un mois. Cette option est recommandée si vous disposez de certains utilisateurs dotés de licences, mais que vous avez besoin d’étendre MFA à davantage d’utilisateurs au-delà des limites de vos licences.

## <a name="create-a-multi-factor-auth-provider"></a>Créer un fournisseur Multi-Factor Authentication
Suivez les étapes ci-dessous pour créer un fournisseur Azure Multi-Factor Auth. Les fournisseurs Azure Multi-Factor Auth peuvent uniquement être créés dans le portail Azure classique. Si vous ne pouvez pas vous connecter au portail Azure classique, vérifiez que votre locataire Azure AD est [associé à un abonnement Azure](../active-directory/active-directory-how-subscriptions-associated-directory.md). 

1. Connectez-vous en tant qu’administrateur au [portail Azure Classic](https://manage.windowsazure.com).
2. Sélectionnez **Active Directory**à gauche.
3. En haut de la page Active Directory, sélectionnez **Multi-Factor Authentication Providers**(Fournisseurs Multi-Factor Authentication).
   
   ![Création d’un fournisseur d’authentification Multifacteur](./media/multi-factor-authentication-get-started-auth-provider/authprovider1.png)

4. Cliquez sur **Nouveau**au bas de la page.
   
   ![Création d’un fournisseur d’authentification Multifacteur](./media/multi-factor-authentication-get-started-auth-provider/authprovider2.png)

5. Sous App Services, sélectionnez **Fournisseur d’authentification multifacteur**
   
   ![Création d’un fournisseur d’authentification Multifacteur](./media/multi-factor-authentication-get-started-auth-provider/authprovider3.png)

6. Sélectionnez **Création rapide**.
   
   ![Création d’un fournisseur d’authentification Multifacteur](./media/multi-factor-authentication-get-started-auth-provider/authprovider4.png)

7. Renseignez les champs suivants et sélectionnez **Créer**.
   1. **Nom** : nom du fournisseur Multi-Factor Authentication.
   2. **Modèle d’utilisation** : vous pouvez choisir l’une des deux options suivantes :
      * Par authentification – modèle d'achat facturé par authentification. Généralement utilisé pour les scénarios qui utilisent Azure Multi-Factor Authentication dans une application orientée client.
      * Par utilisateur activé – modèle d'achat facturé par utilisateur activé. Généralement utilisé pour l’accès des employés aux applications telles qu’Office 365. Choisissez cette option si vous avez des utilisateurs qui disposent déjà d’une licence pour l’authentification multifacteur Azure.
   3. **Répertoire** : locataire Azure Active Directory auquel le fournisseur Multi-Factor Authentication est associé. Soyez conscient des éléments suivants :
      * Vous n'avez pas besoin d’un répertoire Azure AD pour créer un fournisseur Multi-Factor Auth. Laissez ce champ vide si vous prévoyez uniquement de télécharger le kit de développement logiciel (SDK) ou le serveur Azure Multi-Factor Authentication.
      * Le fournisseur Multi-Factor Auth doit être associé avec un répertoire Azure AD pour tirer parti des fonctionnalités avancées.
      * Un seul fournisseur Multi-Factor Auth peut être associé à un répertoire Azure AD.  
      ![Création d’un fournisseur d’authentification Multifacteur](./media/multi-factor-authentication-get-started-auth-provider/authprovider5.png)

8. Une fois que vous cliquez sur Créer, le fournisseur Multi-Factor Authentication est créé et vous devriez voir un message indiquant : **Le fournisseur Multi-Factor Authentication a été créé correctement**. Cliquez sur **OK**.  
   
   ![Création d’un fournisseur d’authentification Multifacteur](./media/multi-factor-authentication-get-started-auth-provider/authprovider6.png)  

## <a name="manage-your-multi-factor-auth-provider"></a>Gérer votre fournisseur Multi-Factor Auth

Vous ne pouvez pas modifier le modèle d’utilisation (par utilisateur activé ou par authentification) après avoir créé un fournisseur MFA. Toutefois, vous pouvez supprimer le fournisseur MFA, puis en créer un avec un autre modèle d’utilisation.

Si le fournisseur Multi-Factor Auth actuel est associé à un répertoire Azure AD (également appelé locataire Azure AD), vous pouvez en toute sécurité le supprimer et en créer un associé au même locataire Azure AD. Si vous avez acheté suffisamment de licences MFA, Azure AD Premium, ou Enterprise Mobility + Security (EMS) pour couvrir tous les utilisateurs activés pour MFA, vous pouvez également supprimer complètement le fournisseur MFA.

Si votre fournisseur MFA n’est pas associé à un locataire Azure AD, ou si vous associez le nouveau fournisseur MFA à un autre locataire Azure AD, les paramètres utilisateur et les options de configuration ne sont pas transférés. Par ailleurs, les serveurs Azure MFA existants doivent être réactivés à l’aide des informations d’identification d’activation générées via le nouveau fournisseur MFA. Le fait de réactiver les serveurs MFA afin de les lier au nouveau fournisseur MFA n’a pas de conséquence sur l’authentification des appels téléphoniques et des SMS. Toutefois, les notifications d’applications mobiles cessent de fonctionner pour tous les utilisateurs jusqu’à ce qu’ils réactivent l’application mobile.

## <a name="next-steps"></a>Étapes suivantes

[Création de Multi-Factor Authentication dans des applications personnalisées (SDK)](multi-factor-authentication-sdk.md)

[Configurer les paramètres d’Azure Multi-Factor Authentication](multi-factor-authentication-whats-next.md)

