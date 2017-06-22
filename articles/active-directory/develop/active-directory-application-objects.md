---
title: Objets application et principal du service Azure Active Directory | Microsoft Docs
description: "Présentation de la relation entre les objets application et principal du service dans Azure Active Directory"
documentationcenter: dev-center-name
author: dstrockis
manager: mbaldwin
services: active-directory
editor: 
ms.assetid: adfc0569-dc91-48fe-92c3-b5b4833703de
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/28/2016
ms.author: dastrock
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: e155891ff8dc736e2f7de1b95f07ff7b2d5d4e1b
ms.openlocfilehash: 6ee0c0b5e606b1fd9fc9eecc877d2718b7079ecb
ms.contentlocale: fr-fr
ms.lasthandoff: 05/02/2017


---
# <a name="application-and-service-principal-objects-in-azure-active-directory-azure-ad"></a>Objets application et principal du service dans Azure Active Directory (Azure AD)
Parfois, la signification du terme « application » peut être mal comprise lorsqu’il est utilisé dans le contexte d’Azure AD. Cet article a pour objectif de clarifier les choses en définissant les aspects conceptuels et concrets de l’intégration des applications dans Azure AD, puis en donnant un exemple d’inscription et de consentement pour une [application mutualisée](active-directory-dev-glossary.md#multi-tenant-application).

## <a name="overview"></a>Vue d'ensemble
Une application qui a été intégrée à Azure AD a des effets qui vont au-delà de l’aspect logiciel. « Application » est souvent utilisé en tant que concept, faisant référence non seulement au programme d’application, mais également à son inscription Azure AD et à son rôle lors des « conversations » d’authentification/autorisation au moment de l’exécution. Par définition, une application peut agir en tant que [client](active-directory-dev-glossary.md#client-application) (consommant une ressource), en tant que [serveur de ressources](active-directory-dev-glossary.md#resource-server) (exposant les API aux clients), voire les deux. Le protocole de conversation est défini par une [flux d’octroi d’autorisation OAuth 2.0](active-directory-dev-glossary.md#authorization-grant), permettant ainsi respectivement au client et à la ressource d’accéder aux données d’une ressource et de les protéger. Approfondissons maintenant les choses pour examiner la manière dont un modèle d’application Azure AD représente une application au moment de la conception et au moment de l’exécution. 

## <a name="application-registration"></a>Inscription de l’application
Lorsque vous inscrivez une application Azure AD dans le [portail Azure][AZURE-Portal], deux objets sont créés dans votre client Azure AD : un objet application et un objet principal du service.

#### <a name="application-object"></a>Objet application
Une application Azure AD est définie par son seul et unique objet application, qui réside dans le client Azure AD dans lequel l’application a été inscrite, appelé client « de base » de l’application. [L’entité Application][AAD-Graph-App-Entity] d’Azure AD Graph définit le schéma pour les propriétés d’un objet application. 

#### <a name="service-principal-object"></a>Objet principal du service
L’objet principal du service définit la stratégie et les autorisations pour l’utilisation d’une application dans un client spécifique, fournissant la base d’un principal de sécurité pour représenter l’application au moment de l’exécution. [L’entité ServicePrincipal][AAD-Graph-Sp-Entity] d’Azure AD Graph définit le schéma pour les propriétés d’un objet principal du service. 

#### <a name="application-and-service-principal-relationship"></a>Relation entre l’application et le principal de service
Considérez l’objet application comme la représentation *globale* de votre application pour une utilisation sur tous les clients et le principal du service comme la représentation *locale* pour une utilisation sur un client spécifique. L’objet application fait office de modèle à partir duquel les propriétés communes et par défaut sont *dérivées* pour une utilisation visant à créer des objets de principal de service correspondants. Un objet application présente donc une relation 1 à 1 avec l’application logicielle et une relation 1 à plusieurs avec le ou les objets de principal de service correspondants.

Un principal de service doit être créé dans chaque locataire sur lequel l’application est utilisée, ce qui lui permet d’établir une identité pour la connexion et/ou l’accès aux ressources sécurisées par le locataire. Une application à locataire unique n’a qu’un seul principal de service (dans son locataire de base), généralement créé et pouvant être utilisé pendant l’inscription de l’application. Une application web/API mutualisée a également un principal de service créé dans chaque locataire où un utilisateur de ce client a consenti à son utilisation.  

> [!NOTE]
> Toute modification apportée à l’objet application de votre application est également répercutée dans son objet principal du service, uniquement dans le client de base de l’application (le client où elle a été inscrite). Pour l’accès mutualisé, les modifications apportées à l’objet application ne sont pas répercutées dans les objets principal de service des clients consommateurs jusqu’à ce que l’accès soit supprimé via le [volet d’accès à l’application](https://myapps.microsoft.com) et à nouveau octroyé.
><br>  
> Notez également que les applications natives sont enregistrées en tant que mutualisées par défaut.
> 
> 

## <a name="example"></a>Exemple
Le schéma suivant illustre la relation entre un objet application d’une application et les objets principal du service correspondants dans le contexte d’un exemple d’application mutualisée appelée **RH**. Il existe trois clients Azure AD dans ce scénario : 

* **Adatum** : le client utilisé par la société qui a développé **l’application RH** ;
* **Contoso** : le client utilisé par l’entreprise Contoso, qui est un consommateur de **l’application RH** ;
* **Fabrikam** : le client utilisé par l’entreprise Fabrikam, qui est également un consommateur de **l’application RH**.

![Relation entre un objet application et un objet principal du service](./media/active-directory-application-objects/application-objects-relationship.png)

Dans le schéma précédent, l’étape 1 correspond au processus de création des objets application et principal du service dans le client de base de l’application.

À l’étape 2, lorsque les administrateurs de Contoso et Fabrikam accordent leur consentement, un objet principal du service est créé dans le client Azure AD de leur entreprise et se voit attribuer les autorisations accordées par l’administrateur. Notez également que l’application RH peut être configurée/conçue de manière à autoriser le consentement par les utilisateurs à des fins d’utilisation individuelle.

À l’étape 3, les clients consommateurs de l’application RH (Contoso et Fabrikam) ont chacun leur propre objet principal de service. Chacun représente leur utilisation d’une instance de l’application lors de l’exécution, régie par les autorisations consenties par l’administrateur respectif.

## <a name="next-steps"></a>Étapes suivantes
L’objet application d’une application est accessible via l’API Azure AD Graph, l’éditeur de manifeste d’application du [portail Azure][AZURE-Portal] ou les [applets de commande Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), tels que représentés par son entité [Application OData][AAD-Graph-App-Entity].

L’objet principal de service d’une application est accessible via l’API Azure AD Graph ou les [applets de commande Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azureadps-2.0), tels que représentés par son [entité ServicePrincipal OData][AAD-Graph-Sp-Entity].

[Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net/) permet d’interroger à la fois les objets d’application et de principal de service.

<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Portal]: https://portal.azure.com

