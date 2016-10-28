<properties
pageTitle="Objets application et principal du service Azure Active Directory | Microsoft Azure"
description="Présentation de la relation entre les objets application et principal du service dans Azure Active Directory"
documentationCenter="dev-center-name"
authors="bryanla"
manager="mbaldwin"
services="active-directory"
editor=""/>

<tags
ms.service="active-directory"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="identity"
ms.date="08/10/2016"
ms.author="bryanla;mbaldwin"/>

# Objets application et principal du service dans Azure Active Directory
Lors de la lecture d’un document portant sur une « application » Azure Active Directory (AD), il n’est pas toujours évident de savoir exactement à quoi l’auteur fait référence. Cet article a pour objectif de clarifier les choses en définissant les aspects conceptuels et concrets de l’intégration des applications dans Azure AD, puis en donnant un exemple d’inscription et de consentement pour une [application mutualisée](active-directory-dev-glossary.md#multi-tenant-application).

## Vue d'ensemble
Une application Azure AD dépasse le cadre d’un simple élément logiciel. Il s’agit d’un terme conceptuel, qui fait référence non seulement à une application logicielle, mais également à son inscription (ou configuration d’identité) auprès d’Azure AD, qui lui permet de participer aux « conversations » d’authentification et d’autorisation lors de l’exécution. Par définition, une application peut agir en tant que [client](active-directory-dev-glossary.md#client-application) (consommant une ressource), en tant que [serveur de ressources](active-directory-dev-glossary.md#resource-server) (exposant les API aux clients), voire les deux. Le protocole de conversation est défini par une [flux d’octroi d’autorisation OAuth 2.0](active-directory-dev-glossary.md#authorization-grant) afin de permettre respectivement au client et à la ressource d’accéder aux données d’une ressource et de les protéger. Approfondissons maintenant les choses pour examiner la manière dont un modèle d’application Azure AD représente une application en interne.

## Inscription de l’application
Lorsque vous inscrivez une application dans le [portail Azure Classic][AZURE-Classic-Portal], deux objets sont créés dans votre client Azure AD : un objet application et un objet principal du service.

#### Objet application
Une application Azure AD est *définie* par son seul et unique objet application, qui réside dans le client Azure AD dans lequel l’application a été inscrite, appelé client « de base » de l’application. L’objet application fournit des informations liées à l’identité pour une application. Il s’agit du modèle dont le ou les objets principal du service correspondants d’une application sont *dérivés* à des fins d’utilisation lors de l’exécution.

L’application peut être considérée comme la représentation *globale* de votre application (pour une utilisation sur tous les clients) et le principal du service comme la représentation *locale* (pour une utilisation sur un client spécifique). [L’entité Application][AAD-Graph-App-Entity] d’Azure AD Graph définit le schéma d’un objet application. Un objet application présente donc une relation 1 à 1 avec l’application logicielle et une relation 1 à *n* avec les *n* objets principal du service correspondants de l’application.

#### Objet principal du service
L’objet principal du service définit la stratégie et les autorisations pour une application, fournissant la base d’un principal de sécurité pour représenter l’application lors de l’accès aux ressources au moment de l’exécution. [L’entité ServicePrincipal][AAD-Graph-Sp-Entity] d’Azure AD Graph définit le schéma d’un objet principal du service.

Un objet principal du service est requis dans chaque client pour lequel une instance de l’utilisation de l’application doit être représentée, permettant l’accès sécurisé aux ressources appartenant aux comptes d’utilisateur de ce client. Une application à client unique aura un seul principal de service (dans son client de base). Une [application web](active-directory-dev-glossary.md#web-client) mutualisée aura également un principal de service sur chaque client sur lequel un administrateur ou un ou plusieurs utilisateurs de ce client ont reçu l’autorisation, ce qui lui permet d’accéder à leurs ressources. Après le consentement, l’objet principal du service est consulté pour les demandes d’autorisation ultérieures.

> [AZURE.NOTE] Toute modification apportée à l’objet application de votre application est également répercutée dans son objet principal du service, uniquement dans le client de base de l’application (le client où elle a été inscrite). Pour l’accès mutualisé, les modifications apportées à l’objet application ne sont pas répercutées dans les objets principal du service des clients consommateurs tant que le client consommateur n’a pas supprimé, puis accordé de nouveau l’accès.

## Exemple
Le schéma suivant illustre la relation entre un objet application d’une application et les objets principal du service correspondants dans le contexte d’un exemple d’application mutualisée appelée **RH**. Il existe trois clients Azure AD dans ce scénario :

- **Adatum** : le client utilisé par la société qui a développé l’application **RH** ;
- **Contoso** : le client utilisé par l’entreprise Contoso, qui est un consommateur de l’application **RH** ;
- **Fabrikam** : le client utilisé par l’entreprise Fabrikam, qui est également un consommateur de l’application **RH**.

![Relation entre un objet application et un objet principal du service](./media/active-directory-application-objects/application-objects-relationship.png)

Dans le schéma précédent, l’étape 1 correspond au processus de création des objets application et principal du service dans le client de base de l’application.

À l’étape 2, lorsque les administrateurs de Contoso et Fabrikam accordent leur consentement, un objet principal du service est créé dans le client Azure AD de leur entreprise et se voit attribuer les autorisations accordées par l’administrateur. Notez également que l’application RH peut être configurée/conçue de manière à autoriser le consentement par les utilisateurs à des fins d’utilisation individuelle.

À l’étape 3, les clients consommateurs de l’application RH (Contoso et Fabrikam) ont chacun leur propre objet principal de service. Chacun représente leur utilisation d’une instance de l’application lors de l’exécution, régie par les autorisations consenties par l’administrateur respectif.

## Étapes suivantes
L’objet application d’une application est accessible via l’API Azure AD Graph, telle que représentée par son [entité Application][AAD-Graph-App-Entity] OData

L’objet application d’une application est accessible via l’API Azure AD Graph, telle que représentée par son [entité ServicePrincipal][AAD-Graph-Sp-Entity] OData



<!--Image references-->

<!--Reference style links -->
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AZURE-Classic-Portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0810_2016-->