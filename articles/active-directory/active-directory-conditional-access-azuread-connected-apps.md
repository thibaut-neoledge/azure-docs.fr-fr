<properties
	pageTitle="Vue d’ensemble de l’accès conditionnel Azure pour les applications SaaS| Microsoft Azure"
	description="L’accès conditionnel dans Azure AD vous permet de configurer des règles d’accès pour l’authentification multifacteur pour chaque application et de bloquer l’accès des utilisateurs qui ne sont pas connectés à un réseau approuvé."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="femila"/>

# Vue d’ensemble de l’accès conditionnel Azure pour les applications SaaS

L’accès conditionnel Azure pour les applications SaaS est disponible pour la version préliminaire publique. La version préliminaire vous permet de configurer des règles d’accès pour l’authentification multifacteur pour chaque application et de bloquer l’accès des utilisateurs qui ne sont pas connectés à un réseau approuvé.

Vous pouvez appliquer les règles d’authentification multifacteur à tous les utilisateurs affectés à l’application, ou uniquement aux utilisateurs au sein de groupes de sécurité spécifiques. Les utilisateurs qui accèdent à l’application à partir d’une adresse IP qui appartient au réseau de l’organisation peuvent être exclus de l’obligation d’une authentification multifacteur. Ces fonctionnalités sont disponibles pour les clients qui ont acheté une licence Azure Active Directory Premium.

## Configuration requise pour le scénario
* Abonnement à Azure Active Directory Premium

* Client Azure Active Directory fédéré ou géré

* Les clients fédérés nécessitent l’activation de l’authentification multifacteur (MFA).

## Problèmes connus dans cette version préliminaire
Cette version préliminaire s’applique aux applications SaaS fédérées préintégrées, aux applications qui utilisent l’authentification unique avec mot de passe, aux applications métier et développées inscrites et au proxy d’application Azure AD. Certaines applications supplémentaires sont toujours activées.

##Configurer des règles d’accès par application

Cette section décrit comment configurer des règles d’accès par application.

1. Connectez-vous au portail Microsoft Azure en tant qu’administrateur.
2. Dans le volet gauche, sélectionnez **Active Directory**.
3. Sous l’onglet Annuaire, sélectionnez votre annuaire.
4. Sélectionnez l’onglet **Applications**.
5. Sélectionnez l’application pour laquelle vous souhaitez définir la règle.
6. Sélectionnez l’onglet **Configurer**.
7. Faites défiler jusqu’à la section des règles d’accès. Sélectionnez la règle d’accès souhaitée.
8. Spécifiez les utilisateurs auxquels la règle doit s’appliquer.
9. Activez la stratégie en sélectionnant **Autorisé à être activé**.

##Présentation des règles d’accès

Cette section fournit une description détaillée des règles d’accès prises en charge dans la version préliminaire de l’accès conditionnel Azure pour les applications.

### Spécification des utilisateurs auxquels s’appliquent les règles d’accès

Par défaut, la stratégie s’applique à tous les utilisateurs qui ont accès à l’application. Toutefois, vous pouvez également limiter la stratégie aux utilisateurs qui sont membres de groupes de sécurité spécifiques. Le bouton **Ajouter un groupe** permet de sélectionner un ou plusieurs groupes à partir de la boîte de dialogue de sélection de groupe auxquels la règle d’accès s’applique. Cette boîte de dialogue peut également servir à supprimer les groupes sélectionnés. Quand les règles d’accès s’appliquent aux groupes, elles ne s’appliquent qu’aux utilisateurs qui appartiennent à un des groupes de sécurité spécifiés.

Des groupes de sécurité peuvent également être explicitement exclus de la stratégie en sélectionnant l’option Sauf et en spécifiant un ou plusieurs groupes. Les utilisateurs qui sont membres d’un groupe dans la liste Sauf ne sont pas soumis à l’exigence d’authentification multifacteur, même s’ils sont membres d’un groupe auquel s’applique la règle d’accès. La règle d’accès indiquée ci-dessous nécessite que tous les utilisateurs du groupe Gestionnaires utilisent l’authentification multifacteur pour accéder à l’application.

![Définition des règles d’accès conditionnel avec l’authentification multifacteur](./media/active-directory-conditional-access/conditionalaccess-saas-apps.png)

##Règles d’accès conditionnel avec l’authentification multifacteur
Si un utilisateur a été configuré à l’aide de la fonctionnalité d’authentification multifacteur en fonction de l’utilisateur, ce paramètre sur l’utilisateur a priorité sur les règles d’authentification multifacteur par application. Cela signifie qu’un utilisateur qui a été configuré pour l’authentification multifacteur en fonction de l’utilisateur doit effectuer l’authentification multifacteur, même s’il a été exclu des règles d’authentification multifacteur par application. En savoir plus sur l’authentification multifacteur et sur les paramètres pour chaque utilisateur.

###Options de règle d’accès
La version préliminaire actuelle prend en charge les options suivantes :

* **Exiger l’authentification multifacteur** : avec cette option, les utilisateurs auxquels les règles d’accès s’appliquent doivent effectuer une authentification multifacteur avant d’accéder à l’application à laquelle la stratégie s’applique.

* **Exiger l’authentification multifacteur à l’extérieur de l’entreprise** : avec cette option, un utilisateur provenant d’une adresse IP approuvée n’est pas obligé d’effectuer une authentification multifacteur. Les plages IP approuvées peuvent être configurées dans la page des paramètres de l’authentification multifacteur.

* **Bloquer l’accès quand l’utilisateur n’est pas au travail** : avec cette option, un utilisateur qui ne provient pas d’une adresse IP approuvée est bloqué. Les plages d’adresses IP approuvées peuvent être configurées dans la page des paramètres de l’authentification multifacteur.

###Définition de l’état des règles
L’état des règles d’accès permet l’activation ou la désactivation des règles. Quand les règles d’accès sont désactivées, l’obligation d’authentification multifacteur n’est pas appliquée.

### Évaluation des règles d’accès

Les règles d’accès sont évaluées lorsqu’un utilisateur accède à une application fédérée qui utilise OAuth 2.0, OpenID Connect, SAML ou WS-Federation. En outre, les règles d’accès sont évaluées quand un jeton d’actualisation est utilisé pour acquérir un jeton d’accès avec OAuth 2.0 et OpenID Connect. Si l’évaluation de la stratégie échoue quand un jeton d’actualisation est utilisé, l’erreur invalid\_grant est renvoyée, indiquant que l’utilisateur doit s’authentifier de nouveau auprès du client. Configurer les services de fédération pour fournir l’authentification multifacteur

Pour les clients fédérés, l’authentification multifacteur (MFA) peut être effectuée par Azure Active Directory ou par le serveur local AD FS.

Par défaut, l’authentification multifacteur a lieu sur une page hébergée par Azure Active Directory. Pour configurer l’authentification multifacteur localement, vous devez affecter la valeur **true** à la propriété –SupportsMFA dans Azure Active Directory à l’aide du module Azure AD pour Windows PowerShell.

L’exemple suivant montre comment activer l’authentification multifacteur localement à l’aide de l’[applet de commande Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) sur le client contoso.com :

    Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true

En plus de définir cet indicateur, vous devez configurer l’instance AD FS du client fédéré pour effectuer une authentification multifacteur. Suivez les instructions permettant de déployer Azure Multi-Factor Authentication localement.

##Articles connexes

- [Sécurisation de l’accès à Office 365 et à d’autres applications connectées à Azure AD](active-directory-conditional-access.md)
- [Index d’articles pour la gestion des applications dans Azure Active Directory](active-directory-apps-index.md)

<!---HONumber=AcomDC_0406_2016-->