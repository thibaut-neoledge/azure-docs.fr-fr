<properties 
	pageTitle="Gérer les applications API" 
	description="Découvrez comment gérer des applications API Microsoft Azure App Service en utilisant le portail Azure et l’Explorateur de serveurs de Visual Studio." 
	services="app-service\api" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na"
	ms.topic="article" 
	ms.date="06/17/2015" 
	ms.author="tdykstra"/>

# Gérer des applications web dans Azure App Service

Cet article vous a montré comment utiliser le [portail Azure en version préliminaire](https://portal.azure.com/) pour effectuer des tâches de gestion et de surveillance d’application API. Voici quelques-unes des tâches que vous pouvez effectuer :

- configurer l’authentification ; 
- activer la mise à l’échelle automatique ;
- afficher les journaux ;
- identifier le nombre de requêtes effectuées et la quantité de données utilisée par une application API ;
- sauvegarder une application API et créer des alertes ;
- configurer la sécurité d’accès par rôle.

L’article explique également comment effectuer certaines tâches de gestion dans la fenêtre Explorateur de serveurs de Visual Studio.

## Panneaux Passerelle et Application API dans le portail Azure en version préliminaire

Dans Microsoft Azure App Service, une application API est une [application web](../app-service-web/app-service-web-overview.md) qui dispose de fonctionnalités supplémentaires pour l’hébergement de services web. Le portail Azure inclut un panneau **Application API** pour gérer les fonctionnalités spécifiques à l’API, ainsi qu’un panneau **Hôte d’application API**, pour gérer l’application web sous-jacente.

Chaque groupe de ressources contenant au moins une application API comprend également une *passerelle*. La passerelle agit comme un proxy : elle gère l’authentification et d’autres fonctions d’administration pour toutes les applications API d’un groupe de ressources. Comme une application API, une passerelle est une application web avec des fonctionnalités supplémentaires. Il existe donc aussi deux panneaux du portail pour la gestion de la passerelle : un panneau **Passerelle** pour les fonctions spécifiques à la passerelle et un panneau **Hôte de passerelle** pour la gestion de l’application web sous-jacente.

### Tâches que vous pouvez effectuer sur le panneau Application API uniquement

Vous utilisez le panneau **Application API** pour les tâches suivantes :

- Configurer le niveau d’accès : cliquez sur **Paramètres > Paramètres de l’application**. La valeur par défaut est « interne » : qui signifie que seules les applications API du même groupe de ressources sont autorisées à appeler l’application API. Pour plus d’informations, consultez [Protéger une application API](app-service-api-dotnet-add-authentication.md).   
- Configurer la stratégie de mise à jour : cliquez sur **Paramètres > Paramètres de l’application**. La valeur par défaut est **Activé**. Cela signifie que quand une nouvelle version de l’application API est publiée sur le Marketplace, votre application API sera automatiquement mise à jour vers la nouvelle version, s’il s’agit d’une modification sans rupture.  
- Configurer l’authentification pour les appels sortants depuis l’application API : cliquez sur **Paramètres > Authentification**. Si l’application API effectue des appels à un service externe qui requiert une authentification, les valeurs de configuration requises sont entrées ici. Par exemple, un connecteur Dropbox requiert un ID de client et une question secrète du client pour accéder au service Dropbox.
- Configurer [RBAC](../role-based-access-control-configure.md) : cliquez sur **Paramètres > Utilisateurs**. L’accès utilisateur que vous configurez ici détermine seulement qui peut accéder aux fonctionnalités spécifiques à une application API. Pour configurer RBAC pour les fonctionnalités de l’application web, utilisez le panneau **Hôte d’application API**. Vous voudrez normalement conserver les paramètres RBAC pour l’application API et pour l’hôte d’application API synchronisés. Si vous donnez à un utilisateur l’accès à l’application API, mais pas à l’hôte d’application API, il ne pourra pas utiliser les fonctionnalités présentes sur le panneau **Application API** qui appartiennent en fait à l’hôte d’application API.
- Afficher la définition de l’API : cliquez sur **Définition d’API** dans la section **Résumé** pour afficher la liste des méthodes exposées par l’application API.
- [Installer le Gestionnaire de connexion hybride](../app-service-logic/app-service-logic-hybrid-connection-manager.md). Le Gestionnaire de connexion hybride vous donne la possibilité de vous connecter à un système SQL Server ou SAP local. Cette connectivité hybride utilise Microsoft Azure Service Bus pour effectuer la connexion et le contrôle de la sécurité entre vos ressources Microsoft Azure et vos ressources locales.

### Tâches que vous pouvez effectuer sur les panneaux Application API et Hôte d’application API 

Le panneau **Application API** vous permet d’effectuer de nombreuses tâches qui se rapportent à l’application web sous-jacente. Vous pouvez par exemple exécuter les tâches suivantes :

* Arrêter, démarrer et redémarrer l’application web qui héberge l’application API.  
- Sélectionner **Demandes et erreurs** pour ajouter des mesures de performances, notamment les codes d’erreur HTTP connus (tels que les codes d’état HTTP 200, 400 ou 500).
- Consulter les temps de réponse et identifier le nombre de demandes envoyées à l’application API, ainsi que la quantité de données entrantes et sortantes. 
- Créer des alertes par e-mail lorsqu’une mesure dépasse un seuil défini par vos soins. 
- Savoir quel pourcentage du **processeur** est utilisé par l’application API, connaître le **quota d’utilisation** actuel (en Mo) et identifier le taux d’utilisation maximal des données, en fonction du niveau de coût.
- Consulter la valeur **Estimation des dépenses** pour déterminer le coût potentiel de l’exécution de votre application API.
- Afficher les journaux d’applications et autres journaux IIS, y compris les journaux FREB et ceux des serveurs web.
- Sélectionner **Processus** pour ouvrir l’Explorateur de processus (ce champ répertorie vos instances web et leurs propriétés, y compris le nombre de threads et le taux d’utilisation de la mémoire).

Ces tâches peuvent également être effectuées via le panneau **Hôte d’application API**. C’est pourquoi les deux panneaux ont en grande partie la même interface utilisateur. Ainsi, les boutons **Arrêter**, **Démarrer** et **Redémarrer** du panneau **Application API** ont exactement le même effet que les boutons **Arrêter**, **Démarrer** et **Redémarrer** du panneau **Hôte d’application API**. De même, les informations de surveillance fournies dans le panneau **Application API** sont identiques à ce que montre le panneau **Hôte d’application API**.

Les seules fonctions fournies sur le panneau **Application API** qui ne sont pas des doublons du panneau **Hôte d’application API** sont répertoriées dans la section précédente.

### Tâches que vous pouvez effectuer sur le panneau Hôte d’application API uniquement

Vous utilisez le panneau **Hôte d’application API** pour toutes les tâches applicables à n’importe quelle application web. Pour plus d’informations, consultez [Gérer les applications web dans le portail](../app-service-web/web-sites-manage.md).

### Tâches que vous pouvez effectuer sur le panneau Passerelle uniquement

Vous utilisez le panneau **Passerelle** pour les tâches suivantes :

- Configuration du fournisseur d’authentification pour les appels entrants vers les applications API : cliquez sur **Paramètres > Identité**. Si la passerelle doit authentifier les utilisateurs avant de leur permettre d’appeler les applications API du groupe de ressources, les valeurs de configuration requises sont entrées ici. Pour plus d’informations, consultez [Configurer et tester un connecteur SaaS dans Azure App Service](app-service-api-connnect-your-app-to-saas-connector.md). 
- Configurer [RBAC](../role-based-access-control-configure.md) : cliquez sur **Paramètres > Utilisateurs**. L’accès utilisateur que vous configurez ici détermine seulement qui peut accéder aux fonctionnalités spécifiques à la passerelle, et non les fonctions partagées par toutes les applications web.

### Tâches que vous pouvez effectuer sur les panneaux Passerelle et Hôte de passerelle 

Les panneaux Passerelle et Hôte de passerelle partagent l’interface utilisateur comme le font les panneaux Application API et Hôte d’application API.

### Tâches que vous pouvez effectuer sur le panneau Hôte de passerelle uniquement

Vous utilisez le panneau **Hôte de passerelle** pour toutes les tâches applicables à n’importe quelle application web. Pour plus d’informations, consultez [Gérer les applications web dans le portail](web-sites-manage.md).

## <a id="navigate"></a>Accéder aux panneaux Application API et Passerelle 

Pour accéder au panneau **Application API**, vous avez diverses méthodes, l’une d’elles consistant à utiliser la fonctionnalité de navigation du portail Azure. Dans la page d’accueil du portail, cliquez sur **Parcourir > Applications API** pour voir toutes les applications API que vous pouvez gérer.

![](./media/app-service-api-manage-in-portal/browse.png)

![](./media/app-service-api-manage-in-portal/apiappslist.png)

### Accéder au panneau Application API

Quand vous cliquez sur une ligne dans le panneau de la liste **Applications API**, le portail affiche le panneau **Application API**.

![](./media/app-service-api-manage-in-portal/apiappblade.png)

### Accéder au panneau Hôte d’application API

Pour accéder au panneau **Hôte d’application API**, cliquez sur **Hôte d’application API** dans le panneau **Application API**.

![](./media/app-service-api-manage-in-portal/apiappbladetohost.png)

![](./media/app-service-api-manage-in-portal/apiapphostbladenocallouts.png)

### Accéder au panneau Passerelle

Pour accéder au panneau **Passerelle**, cliquez sur le lien **Passerelle** dans le panneau **Application API**.
   
![](./media/app-service-api-manage-in-portal/apiappbladegotogateway.png)

![](./media/app-service-api-manage-in-portal/gatewaybladenocallout.png)

### Accéder au panneau Hôte de passerelle

Pour accéder au panneau **Hôte de passerelle**, cliquez sur le lien **Hôte de passerelle** dans le panneau **Passerelle**.
   
![](./media/app-service-api-manage-in-portal/gatewaybladetohost.png)

![](./media/app-service-api-manage-in-portal/gatewayhost.png)

## Accéder aux applications API dans l’Explorateur de serveurs de Visual Studio

Dans l’**Explorateur de serveurs** de Visual Studio, vous pouvez démarrer une session de débogage à distance, afficher des journaux de diffusion en continu et cliquer sur une entrée de menu qui ouvre le panneau Application API dans le portail.

Pour accéder à une application API dans l’Explorateur de serveurs, cliquez sur **Azure > App Service > [nom du groupe de ressources] > [nom de l’application API]**, comme le montre l’illustration.

![](./media/app-service-api-manage-in-portal/se.png)

## Étapes suivantes

Cet article vous a montré comment utiliser le portail Azure pour effectuer des tâches de gestion d’application API. Pour les applications API que vous installez à partir de la galerie d’applications API, consultez également la page [Gestion et contrôle de vos connecteurs et applications API intégrés](../app-service-logic/app-service-logic-monitor-your-connectors.md).

Pour en savoir plus sur la gestion des applications API via la ligne de commande, consultez les articles de la section **Automatiser** du menu affiché à gauche de l’article (sur les fenêtres de navigation agrandies) ou sur sa partie supérieure (sur les fenêtres de navigation réduites).

<!---HONumber=July15_HO5-->