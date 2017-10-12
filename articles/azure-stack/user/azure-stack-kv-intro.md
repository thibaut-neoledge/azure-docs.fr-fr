---
title: "Présentation d’Azure Stack Key Vault | Microsoft Docs"
description: "Découvrez comment Azure Stack Key Vault gère les clés et les secrets"
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 70f1684a-3fbb-4cd1-bf29-9f9882e98fe9
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/04/2017
ms.author: sngun
ms.openlocfilehash: ecb542e967669fc4e4465ae59b3e9c37e4a5c332
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-key-vault-in-azure-stack"></a>Introduction à Key Vault dans Azure Stack

## <a name="before-you-start"></a>Avant de commencer
Cet article part des principes suivants :

* Les utilisateurs doivent s’abonner à une offre qui inclut le service Key Vault.  
* [PowerShell est configuré pour une utilisation avec Azure Stack](azure-stack-powershell-configure-user.md) 
 
## <a name="key-vault-basics"></a>Principes fondamentaux de Key Vault
Key Vault dans Azure Stack permet de protéger les clés de chiffrement et les secrets utilisés par les services et les applications cloud. En utilisant Key Vault, vous pouvez chiffrer les clés et les secrets (comme les clés d’authentification, les clés des comptes de stockage, les clés de chiffrement de données, les fichiers .pfx et les mots de passe).

Key Vault rationalise le processus de gestion de clés et vous permet de garder le contrôle des clés qui accèdent à vos données et les chiffrent. Les développeurs peuvent créer des clés pour le développement et le test en quelques minutes, puis les migrer en toute transparence en clés de production. Les administrateurs de sécurité peuvent accorder (et annuler) les autorisations sur les clés, si nécessaire.

Toute personne disposant d’un abonnement Azure Stack peut créer et utiliser des coffres de clés. Bien que Key Vault procure des avantages aux développeurs et aux administrateurs de sécurité, il peut être implémenté et géré par l’opérateur qui gère les autres services Azure Stack pour une organisation. Par exemple, cet opérateur Azure Stack peut se connecter avec un abonnement Azure Stack, créer un coffre pour l’organisation où stocker les clés, puis avoir la responsabilité de ces tâches opérationnelles :

* créer ou importer une clé ou un secret ;
* supprimer ou effacer une clé ou un secret ;
* autoriser des utilisateurs ou des applications à accéder au coffre de clés, afin qu’ils puissent gérer ou utiliser ses clés et ses clés secrètes ;
* configurer l’utilisation de la clé (par exemple, signer ou chiffrer) ;

Cet opérateur peut ensuite fournir aux développeurs des URI à appeler à partir de leurs applications et fournir à un administrateur de sécurité des informations de journalisation sur l’utilisation des clés.

Les développeurs peuvent également gérer les clés directement à l’aide d’API. Pour plus d’informations, consultez le guide du développeur Key Vault.

## <a name="scenarios"></a>Scénarios
Le tableau suivant décrit certains des scénarios où Key Vault peut permettre de répondre aux besoins des développeurs et des administrateurs de sécurité :

### <a name="developer-for-an-azure-stack-application"></a>Développeur d’une application Azure Stack
**Problème** : Je veux écrire une application pour Azure Stack qui utilise des clés pour la signature et le chiffrement, mais je veux que ces clés soient externes à mon application, afin que la solution soit adaptée à une application répartie au niveau géographique.

**Solution** : Les clés sont stockées dans un coffre et appelées par un URI quand c’est nécessaire.

### <a name="developer-for-software-as-a-service-saas"></a>Développeur de logiciels SaaS (Software as a service)
**Problème** : Je ne veux pas prendre la responsabilité des clés et des secrets pour mes clients.

**Solution :** Les clients peuvent importer leurs propres clés dans Azure Stack et les gérer. Je veux que les clients détiennent et gèrent leurs clés, pour pouvoir me concentrer sur ce que je fais le mieux, c’est-à-dire fournir les principales fonctionnalités du logiciel.

### <a name="chief-security-officer-cso"></a>Responsable de la sécurité
**Problème** : Je veux être sûr que mon organisation contrôle le cycle de vie des clés et puisse surveiller leur utilisation.

**Solution :** Key Vault a été conçu de façon que Microsoft ne puisse pas voir ni extraire vos clés.  Quand une application doit effectuer des opérations de chiffrement en utilisant des clés des clients, Key Vault le fait à la place de l’application. L’application ne voit pas les clés des clients.  Même si nous utilisons plusieurs services et ressources Azure Stack, je veux gérer les clés à partir d’un seul emplacement dans Azure. Le coffre fournit une interface unique, indépendamment du nombre de coffres dont vous disposez dans Azure Stack, des régions qui sont prises en charge et des applications qui les utilisent.

## <a name="next-steps"></a>Étapes suivantes

* [Gérer Key Vault dans Azure Stack en utilisant le portail](azure-stack-kv-manage-portal.md)  
* [Gérer Key Vault dans Azure Stack en utilisant PowerShell](azure-stack-kv-manage-powershell.md)
