---
title: "Azure Multi-Factor Authentication : fonctionnement"
description: "Azure Multi-Factor Authentication contribue à sécuriser l&quot;accès aux données et aux applications tout en répondant à la demande de l&quot;utilisateur d&quot;un processus d&quot;authentification simple. Il fournit une sécurité supplémentaire en exigeant un deuxième formulaire d&quot;authentification et fournit une authentification renforcée via un éventail d&quot;options de vérification simples."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtland
ms.assetid: d14db902-9afe-4fca-b3a5-4bd54b3d8ec5
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: e028130569a1e55834f68bde87f21f6f7505d41f
ms.openlocfilehash: 9132e9bde7403e2868b92cb1149904d926ff5d8e
ms.lasthandoff: 02/23/2017


---
# <a name="how-azure-multi-factor-authentication-works"></a>Azure Multi-Factor Authentication : fonctionnement
La sécurité de l'authentification multi-facteur repose sur son approche en couche. Compromettre plusieurs facteurs d'authentification présente un défi de taille pour les attaquants. Même si un attaquant réussit à connaître le mot de passe de l'utilisateur, ce dernier est inutile sans posséder l'appareil de confiance. Si l'utilisateur perd l'appareil, la personne qui l'a trouvé ne pourra pas l'utiliser sans connaître le mot de passe de l'utilisateur.

![Vérification](./media/multi-factor-authentication-how-it-works/howitworks.png)

Azure Multi-Factor Authentication contribue à sécuriser l'accès aux données et aux applications tout en répondant à la demande de l'utilisateur d'un processus d'authentification simple.  Il fournit une sécurité supplémentaire en exigeant un deuxième formulaire d'authentification et fournit une authentification renforcée via un éventail d'options de vérification simples.

Pour des informations détaillées sur son fonctionnement, regardez la vidéo suivante :

> [!VIDEO https://channel9.msdn.com/Events/TechEd/Europe/2014/EM-B313/player]

## <a name="methods-available-for-two-step-verification"></a>Méthodes disponibles pour la vérification en deux étapes
Lorsqu'un utilisateur se connecte, une vérification supplémentaire lui est envoyée.  Voici la liste des méthodes qui peuvent être utilisées pour cette seconde vérification.

| Méthode de vérification | Description |
| --- | --- |
| appel téléphonique |Un appel est passé sur le téléphone enregistré de l’utilisateur demandant de vérifier qu’il se connecte en appuyant sur le symbole # ou en entrant un code confidentiel. |
| SMS |Un SMS sera envoyé sur le téléphone de l’utilisateur avec un code à six chiffres.  Entrez ce code pour finaliser le processus de vérification. |
| Notification sur l’application mobile |Une demande de vérification sera envoyée sur le smartphone de l’utilisateur lui indiquant de terminer la vérification en sélectionnant Vérifier depuis l’application mobile. Cela se produit si vous avez sélectionné la notification sur application comme méthode de vérification principale.  Si l’utilisateur reçoit cette notification lorsqu'il ne se connecte pas, il peut choisir de la signaler comme fraude. |
| Code de vérification de l’application mobile |L’application mobile, qui est exécutée sur le smartphone d’un utilisateur, affiche un code de vérification à 6 chiffres qui change toutes les 30 secondes. L’utilisateur trouve le code le plus récent et l’entre dans la page de connexion pour terminer le processus de vérification. Cela se produit si vous avez sélectionné un code de vérification comme méthode de vérification principale. |
| jetons OATH tiers | Azure Multi-Factor Authentication peut être configuré pour accepter les méthodes de vérification tierces. |

Azure Multi-Factor Authentication fournit des méthodes de vérification sélectionnables pour cloud et pour serveur. Cela signifie que vous pouvez choisir les méthodes mises à la disposition de vos utilisateurs : appel téléphonique, texte, notification sur l’application ou codes d’application. Pour plus d’informations, consultez [Méthodes de vérification sélectionnables](multi-factor-authentication-whats-next.md#selectable-verification-methods).

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur les différentes [versions et méthodes de consommation pour Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md)

- Choisissez de déployer Azure MFA [dans le cloud ou en local](multi-factor-authentication-get-started.md)
