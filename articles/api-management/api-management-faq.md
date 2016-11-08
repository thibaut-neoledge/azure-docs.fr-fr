---
title: FAQ sur la gestion des API Azure | Microsoft Docs
description: Découvrez les réponses aux questions les plus fréquentes, les modèles et les meilleures pratiques pour la gestion des API Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: erikre
editor: ''

ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2016
ms.author: mijiang

---
# FAQ sur la gestion des API Azure
Découvrez les réponses aux questions les plus fréquentes, les modèles et les meilleures pratiques pour la gestion des API Azure.

## Forum Aux Questions
* [Comment puis-je poser une question à l’équipe de gestion des API ?](#how-can-i-ask-a-question-to-the-api-management-team)
* [Qu’est-ce que cela signifie lorsqu’une fonctionnalité est disponible en version préliminaire ?](#what-does-it-mean-when-a-feature-is-in-preview)
* [Quelles sont les options prises en charge pour sécuriser la connexion entre la passerelle de gestion des API et mes services principaux ?](#what-are-the-supported-options-to-secure-the-connection-between-the-api-management-gateway-and-my-backend-services)
* [Comment puis-je copier une instance de la gestion des API vers une nouvelle instance ?](#how-can-i-copy-an-api-management-instance-to-a-new-instance)
* [Puis-je gérer mon instance de gestion des API par programme ?](#can-i-manage-my-api-management-instance-programmatically)
* [Comment puis-je ajouter un utilisateur au groupe d’administrateurs ?](#how-can-i-add-a-user-to-the-administrators-group)
* [Pourquoi la stratégie que je veux ajouter n’est-elle pas activée dans l’éditeur de stratégie ?](#why-is-the-policy-that-i-want-to-add-not-enabled-in-the-policy-editor)
* [Comment puis-je contrôler la version de l’API avec la gestion des API ?](#how-can-i-achieve-api-versioning-with-api-management)
* [Comment puis-je configurer plusieurs environnements d’API, par exemple Sandbox et Production ?](#how-can-i-configure-multiple-environments-of-apis-for-example-sandbox-and-production)
* [SOAP est-il pris en charge dans la gestion des API ?](#is-soap-supported-in-api-management)
* [L’adresse IP de la passerelle de gestion des API est-elle constante ? Puis-je l’utiliser dans les règles de pare-feu ?](#is-the-api-management-gateway-ip-address-constant-can-i-use-it-in-firewall-rules)
* [Puis-je configurer un serveur d’autorisation OAuth 2.0 avec la sécurité ADFS ?](#can-i-configure-an-oauth-20-authorization-server-with-adfs-security)
* [Quelle méthode de routage la gestion des API utilise-t-elle lors du déploiement sur plusieurs emplacements géographiques ?](#what-routing-method-does-api-management-use-when-deployed-to-multiple-geographic-locations)
* [Puis-je créer une instance de service de gestion des API à l’aide d’un modèle ARM ?](#can-i-create-an-api-management-service-instance-using-an-arm-template)
* [Puis-je utiliser un certificat SSL auto-signé pour un service principal ?](#can-i-use-a-self-signed-ssl-certificate-for-a-backend)
* [Pourquoi l’authentification échoue-t-elle lors de la tentative de clonage du référentiel GIT ?](#why-am-i-getting-authentication-failure-when-i-try-to-clone-the-git-repository)
* [Comment fonctionne la gestion des API avec Express Route ?](#does-api-management-work-with-express-route)
* [Puis-je déplacer l’instance de gestion des API d’un abonnement à un autre ?](#can-i-move-api-management-instance-from-one-subscription-to-another)

### Comment puis-je poser une question à l’équipe de gestion des API ?
* Vous pouvez publier vos questions sur notre [forum MSDN de gestion des API](https://social.msdn.microsoft.com/forums/azure/home?forum=azureapimgmt).
* Vous pouvez nous envoyer un courrier électronique à l’adresse : `apimgmt@microsoft.com`.
* Vous pouvez nous envoyer une [demande de fonctionnalité](https://feedback.azure.com/forums/248703-api-management).

### Qu’est-ce que cela signifie lorsqu’une fonctionnalité est disponible en version préliminaire ?
Une fonctionnalité en version préliminaire est complète et opérationnelle, mais elle est en version préliminaire pour nous permettre de collecter activement les commentaires client à son sujet. En fonction des commentaires client reçus, nous serons peut-être amenés à apporter des modifications avec rupture. Nous vous recommandons donc de ne pas utiliser cette fonctionnalité comme composant essentiel dans votre environnement de production. Si vous avez des commentaires sur les fonctionnalités en version préliminaire, n’hésitez pas à nous le faire savoir de l’une des façons décrites dans [Comment puis-je poser une question à l’équipe de gestion des API ?](#how-can-i-ask-a-question-to-the-api-management-team).

### Quelles sont les options prises en charge pour sécuriser la connexion entre la passerelle de gestion des API et mes services principaux ?
Il existe plusieurs options prises en charge.

1. Utilisez l’authentification HTTP de base. Pour plus d’informations, consultez [Configuration des paramètres de l’API](api-management-howto-create-apis.md#configure-api-settings).
2. Utilisez l’authentification mutuelle SSL telle que décrite dans [Comment sécuriser des services principaux à l’aide d’une authentification par certificat client dans la Gestion des API Azure](api-management-howto-mutual-certificates.md).
3. Utilisez une liste blanche des adresses IP sur votre service principal. Si vous avez une instance de gestion des API de niveau Standard ou Premium, l’adresse IP de la passerelle reste constante et vous pouvez configurer votre liste blanche pour autoriser cette adresse IP. Vous pouvez récupérer l’adresse IP de votre instance Gestion des API sur le **tableau de bord** dans le Portail Azure Classic.
4. Vous pouvez connecter votre instance de gestion des API à un réseau virtuel Azure (classique). Pour plus d’informations, consultez [Comment configurer des connexions VPN dans Gestion des API Azure](api-management-howto-setup-vpn.md).

### Comment puis-je copier une instance de la gestion des API vers une nouvelle instance ?
Il existe plusieurs options que vous pouvez utiliser pour copier une instance de service de gestion des API vers une nouvelle instance.

* Utilisez la fonctionnalité de sauvegarde et restauration de la gestion des API. Pour plus d’informations, consultez [Comment implémenter une récupération d’urgence à l’aide d’une sauvegarde de service et la récupérer dans Gestion des API Azure](api-management-howto-disaster-recovery-backup-restore.md).
* Créez votre propre fonctionnalité de sauvegarde et restauration à l’aide de [l’API REST de gestion des API](https://msdn.microsoft.com/library/azure/dn776326.aspx) pour enregistrer et restaurer les entités requises à partir de votre instance de service.
* Téléchargez la configuration du service à l’aide de Git et chargez-la vers une nouvelle instance. Pour plus d’informations, consultez [Comment enregistrer et configurer votre configuration du service Gestion des API à l’aide de Git](api-management-configuration-repository-git.md).

### Puis-je gérer mon instance de gestion des API par programme ?
Oui, vous pouvez la gérer à l’aide de [l’API REST Gestion des API](https://msdn.microsoft.com/library/azure/dn776326.aspx), du [Kit de développement logiciel (SDK) de la bibliothèque de gestion du service Gestion des API Microsoft Azure](http://aka.ms/apimsdk) et des applets de commande PowerShell de [déploiement du service](https://msdn.microsoft.com/library/mt619282.aspx) et de [gestion du service](https://msdn.microsoft.com/library/mt613507.aspx).

### Comment puis-je ajouter un utilisateur au groupe d’administrateurs ?
Vous pouvez suivre les étapes ci-dessous :

1. Connectez-vous au [portail Azure](https://portal.azure.com)
2. Accédez au groupe de ressources qui contient l’instance de gestion des API souhaitée
3. Ajoutez l’utilisateur souhaité au rôle « collaborateur de gestion des API »

Une fois cette opération effectuée, le contributeur nouvellement ajouté peut utiliser les [applets de commande](https://msdn.microsoft.com/library/mt613507.aspx) Azure PowerShell pour se connecter en tant qu’administrateur :

1. Utilisez l’applet de commande `Login-AzureRmAccount` pour vous connecter
2. Définissez le contexte sur l’abonnement qui contient le service à l’aide de `Set-AzureRmContext -SubscriptionID <subscriptionGUID>`
3. Obtenez l’URL d’authentification unique à l’aide de `Get-AzureRmApiManagementSsoToken -ResourceGroupName <rgName> -Name <serviceName>`
4. Utilisez l’URL pour accéder au portail d’administration

### Pourquoi la stratégie que je veux ajouter n’est-elle pas activée dans l’éditeur de stratégie ?
Si la stratégie que vous souhaitez ajouter n’est pas activée, vérifiez que vous êtes dans l’étendue correcte pour cette stratégie. Chaque instruction de stratégie est conçue pour une utilisation dans certaines étendues et sections de la stratégie. Pour consulter les sections de la stratégie et les étendues pour une stratégie, consultez la section **Utilisation** de cette stratégie dans la [Référence de la stratégie](https://msdn.microsoft.com/library/azure/dn894080.aspx).

### Comment puis-je contrôler la version de l’API avec la gestion des API ?
* Vous pouvez configurer des API distinctes dans Gestion des API représentant différentes versions. Par exemple, vous pouvez avoir `MyAPI v1` et `MyAPI v2` comme deux API différentes et les développeurs peuvent choisir la version qu’ils souhaitent utiliser.
* Vous pouvez également configurer votre API avec une URL de service qui n’inclut pas un segment de version, par exemple : `https://my.api`. Vous pouvez ensuite configurer un segment de version sur le modèle de [réécriture de l’URL](https://msdn.microsoft.com/library/azure/dn894083.aspx#RewriteURL) de chaque opération. Vous pouvez, par exemple, avoir une opération avec un [modèle d’URL](api-management-howto-add-operations.md#url-template) de type `/resource` et un modèle de [réécriture de l’URL](api-management-howto-add-operations.md#rewrite-url-template) de type `/v1/Resource`. De cette façon, vous pouvez modifier la valeur du segment de version de chaque opération séparément.
* Si vous souhaitez conserver un segment de version « par défaut » dans l’URL du service de l’API, vous pouvez définir, sur les opérations sélectionnées, une stratégie qui utilise la stratégie [Définir le service principal](https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBackendService) pour modifier le chemin d’accès de requête principal.

### Comment puis-je configurer plusieurs environnements d’API, par exemple Sandbox et Production ?
Actuellement, vos options sont :

* Vous pouvez héberger des API distinctes sur le même client
* Vous pouvez héberger les mêmes API sur plusieurs clients

### SOAP est-il pris en charge dans la gestion des API ?
Actuellement, nous proposons un support limité pour SOAP dans la gestion des API Azure. Nous étudions actuellement cette fonctionnalité. Nous aimerions obtenir des exemples de vos documents WSDL : cela nous aiderait à orienter nos réflexions. Veuillez nous contacter en utilisant les informations de contact référencées dans [Comment puis-je poser une question à l’équipe de gestion des API ?](#how-can-i-ask-a-question-to-the-api-management-team)

Si vous avez besoin que cette fonctionnalité soit opérationnelle, certains membres de notre communauté ont suggéré des solutions. Consultez [Gestion des API Azure - APIM, consommer un service WCF de SOAP sur HTTP](http://mostlydotnetdev.blogspot.com/2015/03/azure-api-management-apim-consuming.html).

Cette implémentation de la solution nécessite la configuration manuelle des stratégies, ne prend pas en charge l’importation ou l’exportation WSDL et les utilisateurs doivent former le corps des requêtes effectuées à l’aide de la console de test dans le portail des développeurs.

### L’adresse IP de la passerelle de gestion des API est-elle constante ? Puis-je l’utiliser dans les règles de pare-feu ?
Dans les niveaux Standard et Premium, l’adresse IP publique (VIP, adresse IP virtuelle) du client de gestion des API est statique pour la durée de vie du client, avec quelques exceptions répertoriées ci-dessous. Notez que les clients du niveau Premium configurés pour un déploiement dans plusieurs régions se voient attribués une seule adresse IP publique par région.

L’adresse IP est modifiée dans les circonstances suivantes :

* Le service est supprimé et recréé
* L’abonnement au service est suspendu (par exemple pour non-paiement) et relancé
* Le réseau virtuel est ajouté ou supprimé (le réseau virtuel n’est pris en charge que dans le niveau Premium)
* L’adresse régionale change si la région est libérée et réactivée (déploiement dans plusieurs régions pris en charge dans le niveau Premium uniquement)

L’adresse IP (ou les adresses dans le cas de déploiement dans plusieurs régions) est accessible sur la page client dans le portail Azure Classic.

### Puis-je configurer un serveur d’autorisation OAuth 2.0 avec la sécurité ADFS ?
Pour plus d’informations sur la configuration de ce scénario, consultez [Utilisation d’AD FS dans la gestion des API](https://phvbaars.wordpress.com/2016/02/06/using-adfs-in-api-management/).

### Quelle méthode de routage la gestion des API utilise-t-elle lors du déploiement sur plusieurs emplacements géographiques ?
La gestion des API utilise la [méthode de routage du trafic basé sur les performances](../traffic-manager/traffic-manager-routing-methods.md#performance-traffic-routing-method). Le trafic entrant est acheminé vers la passerelle API la plus proche. Si une région est déconnectée, le trafic entrant est automatiquement redirigé vers la passerelle suivante la plus proche. Pour plus d’informations sur les différentes méthodes de routage, consultez [Méthodes de routage de Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

### Puis-je créer une instance de service de gestion des API à l’aide d’un modèle ARM ?
Oui, consultez les modèles de démarrage rapide [Service Gestion des API Azure](http://aka.ms/apimtemplate).

### Puis-je utiliser un certificat SSL auto-signé pour un service principal ?
Oui. Suivez les étapes ci-dessous :

1. Créez une entité [Backend](https://msdn.microsoft.com/library/azure/dn935030.aspx) à l’aide de l’API de gestion
2. Affectez la valeur true à la propriété skipCertificateChainValidation.
3. Lorsque vous ne souhaitez plus autoriser le certificat auto-signé, vous pouvez supprimer l’entité Backend ou affecter la valeur false à la propriété skipCertificateChainValidation.

### Pourquoi l’authentification échoue-t-elle lors de la tentative de clonage du référentiel GIT ?
Si vous utilisez le Gestionnaire d’informations d’identification GIT ou si vous essayez de cloner le référentiel via Visual Studio, il est possible que vous rencontriez un problème connu dans la boîte de dialogue d’informations d’identification Windows, qui limite la longueur du mot de passe à 127 caractères seulement et tronque, par conséquent, le mot de passe que nous générons. Nous travaillons au raccourcissement du mot de passe. Pour l’instant, utilisez GIT Bash pour le clonage.

### Comment fonctionne la gestion des API avec Express Route ?
Oui.

### Puis-je déplacer l’instance de gestion des API d’un abonnement à un autre ?
Oui. Veuillez suivre les instructions de [cet article](../resource-group-move-resources.md).

<!---HONumber=AcomDC_0907_2016-->