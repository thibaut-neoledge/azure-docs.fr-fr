---
title: 'Azure AD Connect : authentification directe - Verrouillage intelligent | Documents Microsoft'
description: "Cet article décrit comment l’authentification directe Azure Active Directory (Azure AD) protège vos comptes locaux contre les attaques de mot de passe par recherche exhaustive dans le cloud."
services: active-directory
keywords: Authentification directe Azure AD Connect, installer Active Directory, composants requis pour Azure AD, SSO, Authentification unique
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: billmath
ms.openlocfilehash: 7e05c469260a445578c80cdf77fab2d5ffb48022
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>Authentification directe Azure Active Directory : Verrouillage intelligent

## <a name="overview"></a>Vue d'ensemble

Azure AD protège contre les attaques de mot de passe par recherche exhaustive et empêche le verrouillage des utilisateurs authentiques sur leurs applications Office 365 et SaaS. Cette fonctionnalité, appelée **Verrouillage intelligent**, est pris en charge lorsque vous utilisez l’authentification directe en tant que méthode de connexion. Le verrouillage intelligent est activé par défaut pour tous les clients et protège vos comptes d’utilisateur en permanence ; Il n’est pas nécessaire pour l’activer.

Le verrouillage intelligent fonctionne en conservant la trace des tentatives de connexion ayant échoué et après un certain **Seuil de verrouillage**, en démarrant une **Durée de verrouillage**. Toutes les tentatives de connexion de l’attaquant pendant la durée de verrouillage sont rejetées. Si l’attaque se poursuit, une fois la durée de verrouillage terminée, les durées de verrouillage pour les tentatives de connexion ayant échoué suivantes sont plus longues.

>[!NOTE]
>Le seuil de verrouillage par défaut est de 10 tentatives ayant échoué et la durée de verrouillage par défaut est de 60 secondes.

Le verrouillage intelligent fait également la distinction entre les connexions d’utilisateurs authentiques et des attaquants et ne verrouille que les attaquants dans la plupart des cas. Cette fonctionnalité empêche les attaquants de verrouiller à des fins malveillantes des utilisateurs authentiques. Nous utilisons l’ancien comportement de connexion, les appareils et les navigateurs utilisateurs et d’autres signaux pour faire la distinction entre les attaquants et les utilisateurs authentiques. Nous améliorons constamment nos algorithmes.

Étant donné que l’authentification directe transfère les requêtes de validation de mot de passe sur votre Active Directory (AD) local, vous devez empêcher les attaquants de verrouiller les comptes de vos utilisateurs AD. Étant donné que vous avez vos propres stratégies de verrouillage de compte Active Directory (en particulier, les stratégies[**Seuil de verrouillage de compte** ](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx) et [ **Réinitialiser le compteur de verrouillage du compte après**](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx)), vous devez configurer correctement les valeurs de seuil de verrouillage et de durée de verrouillage d’Azure AD pour filtrer les attaques dans le cloud avant qu’elles n’atteignent votre AD local.

>[!NOTE]
>La fonctionnalité Smart Lockout est gratuite et _activée_ par défaut pour tous les clients. Toutefois, la modification du seuil de verrouillage et des valeurs de durée de verrouillage d’Azure AD à l’aide de l’API Graph oblige votre locataire à avoir au moins une licence Azure AD Premium P2. Vous n’avez pas besoin d’une licence Azure AD Premium P2 _par utilisateur_ pour obtenir la fonctionnalité de verrouillage intelligent avec une authentification directe.

Pour vous assurer que les comptes AD locaux de vos utilisateurs sont correctement protégés, vous devez vérifier que :

1.  le seuil de verrouillage d’Azure AD est _inférieur_ au seuil de verrouillage du compte AD. Nous vous recommandons de définir les valeurs de sorte que le seuil de verrouillage du compte AD soit au moins deux ou trois fois égal au seuil de verrouillage d’Azure AD.
2.  La durée de verrouillage d’Azure AD (représentée en secondes) est _plus longue_ que Réinitialiser le compteur de verrouillage du compte après (exprimée en minutes) d’AD.

## <a name="verify-your-ad-account-lockout-policies"></a>Vérifiez vos stratégies de verrouillage de compte AD

Utilisez les instructions suivantes pour vérifier vos stratégies de verrouillage de compte AD :

1.  Ouvrez l’outil de gestion de stratégie de groupe.
2.  Modifiez la stratégie de groupe appliquée à tous les utilisateurs, par exemple la stratégie de domaine par défaut.
3.  Accédez à Configuration de l’ordinateur\Stratégie\Paramètres Windows\Paramètres de sécurité\Stratégies de compte\Stratégie de verrouillage du compte.
4.  Vérifiez vos valeurs de Seuil de verrouillage de compte et Réinitialiser le compteur de verrouillage du compte après.

![Stratégies de verrouillage de compte AD](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-needs-premium-license"></a>Utilisez l’API Graph pour gérer les valeurs de verrouillage intelligent de votre locataire (licence Premium nécessaire)

>[!IMPORTANT]
>Modifier les valeurs du seuil de verrouillage et de la durée de verrouillage d’Azure AD à l’aide de l’API Graph est une fonctionnalité d’Azure AD Premium P2. Vous devez également être un Administrateur global sur votre client.

Vous pouvez utiliser [l’Explorateur graphique](https://developer.microsoft.com/graph/graph-explorer) pour lire, définir et mettre à jour les valeurs de verrouillage intelligent d’Azure AD. Mais vous pouvez également effectuer ces opérations par programme.

### <a name="read-smart-lockout-values"></a>Lire les valeurs de verrouillage intelligent

Suivez ces étapes pour lire les valeurs de verrouillage intelligent de votre client :

1. Connectez-vous à l’Explorateur graphique en tant qu’administrateur global de votre client. Si vous y êtes invité, accordez l’accès pour les autorisations demandées.
2. Cliquez sur « Modifier les autorisations » et sélectionnez l’autorisation « Directory.ReadWrite.All ».
3. Configurez la requête d’API Graph comme suit : définir la version sur « BETA », type de requête sur « GET » et l’URL sur `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Cliquez sur « Exécuter la requête » pour consulter les valeurs de verrouillage intelligent de votre client. Si vous n’avez pas défini les valeurs de votre client avant, un ensemble vide s’affiche.

### <a name="set-smart-lockout-values"></a>Définir les valeurs de verrouillage intelligent

Procédez comme suit pour définir les valeurs de verrouillage intelligent de votre client (pour la première fois uniquement) :

1. Connectez-vous à l’Explorateur graphique en tant qu’administrateur global de votre client. Si vous y êtes invité, accordez l’accès pour les autorisations demandées.
2. Cliquez sur « Modifier les autorisations » et sélectionnez l’autorisation « Directory.ReadWrite.All ».
3. Configurez la requête d’API Graph comme suit : définir la version sur « BETA », le type de requête sur « POST » et l’URL sur `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Copiez et collez la requête JSON suivante dans le champ « Corps de la requête ».
5. Cliquez sur « Exécuter la requête » pour définir les valeurs de verrouillage intelligent de votre client.

```
{
  "templateId": "5cf42378-d67d-4f36-ba46-e8b86229381d",
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "300"
    },
    {
      "name": "LockoutThreshold",
      "value": "5"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

>[!NOTE]
>Si vous ne les utiliser pas, vous pouvez laisser les valeurs **BannedPasswordList** et **EnableBannedPasswordCheck** vides (« ») et « false » respectivement.

Vérifiez que vous avez défini les valeurs de verrouillage intelligent de votre client correctement à l’aide de [ces étapes](#read-smart-lockout-values).

### <a name="update-smart-lockout-values"></a>Mettre à jour les valeurs de verrouillage intelligent

Suivez ces étapes pour mettre à jour les valeurs de verrouillage intelligent de votre client (si vous les avez déjà définies avant) :

1. Connectez-vous à l’Explorateur graphique en tant qu’administrateur global de votre client. Si vous y êtes invité, accordez l’accès pour les autorisations demandées.
2. Cliquez sur « Modifier les autorisations » et sélectionnez l’autorisation « Directory.ReadWrite.All ».
3. [Suivez ces étapes pour lire les valeurs de verrouillage intelligent de votre client](#read-smart-lockout-values). Copiez la valeur `id` (un GUID) de l’élément avec « displayName » en tant que « PasswordRuleSettings ».
4. Configurez la requête d’API Graph comme suit : définir la version sur « BETA », le type de requête sur « PATCH » et l’URL sur `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>` : utilisez le GUID de l’étape 3 pour `<id>`.
5. Copiez et collez la requête JSON suivante dans le champ « Corps de la requête ». Modifiez les valeurs de verrouillage intelligent comme il convient.
6. Cliquez sur « Exécuter la requête » pour mettre à jour les valeurs de verrouillage intelligent de votre client.

```
{
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "30"
    },
    {
      "name": "LockoutThreshold",
      "value": "4"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

Vérifiez que vous avez mis à jour les valeurs de verrouillage intelligent de votre client correctement à l’aide de [ces étapes](#read-smart-lockout-values).

## <a name="next-steps"></a>Étapes suivantes
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) : pour formuler des requêtes de nouvelles fonctionnalités.
