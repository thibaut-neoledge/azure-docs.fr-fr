<properties
	pageTitle="Gestion et contrôle de vos connecteurs et applications API dans App Service | Microsoft Azure"
	description="Afficher les performances de vos connecteurs et applications API dans Logic Apps ; architecture microservice"
	services="app-service\logic"
	documentationCenter=".net,nodejs,java"
	authors="MandiOhlinger"
	manager="dwrede"
	editor="cgronlun"/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2016"
	ms.author="mandia"/>

# Gestion et contrôle de vos connecteurs et applications API intégrés

>[AZURE.NOTE] Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques.

Vous avez créé une application API intégrée. Et maintenant ?

Dans Azure, chaque application API est un site web distinct hébergé sur Azure. Par conséquent, vous pouvez facilement voir le nombre de requêtes effectuées, ainsi que la quantité de données utilisée par le connecteur. Vous pouvez également sauvegarder votre application API, créer des alertes, activer Tinfoil Security et ajouter des utilisateurs et des rôles.

Cette rubrique décrit certaines des options de gestion de votre application API.

Pour voir ces fonctions intégrées, ouvrez votre application API dans le [portail Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040). Si l’application API est dans votre tableau d’accueil, sélectionnez-la pour ouvrir les propriétés. Vous pouvez également sélectionner **Parcourir**, **Applications API**, puis choisir votre application API :

![][browse]

## Consulter les propriétés entrées

Lorsque vous ouvrez l’application API, il existe plusieurs fonctionnalités et tâches disponibles :

![][settings]

Vous pouvez :

- L’onglet **Paramètres** affiche les informations spécifiques de l’application API, y compris les détails de votre abonnement, et répertorie les utilisateurs qui ont accès à votre application API. Vous pouvez également augmenter ou diminuer le nombre d’instances de votre application API à l’aide de la fonctionnalité de mise à l’échelle, entre autres.
- Utilisez les boutons **Démarrer** et **Arrêter** pour contrôler l’application API.
- Lorsque des mises à jours sont disponibles pour les fichiers sous-jacents utilisés par votre application API, vous pouvez cliquer sur **Mettre à jour** pour obtenir les dernières versions. Par exemple, s’il existe un correctif ou une mise à jour de sécurité publié par Microsoft, cliquez sur **Mettre à jour** pour actualiser automatiquement votre application API et inclure ce correctif.
- Sélectionnez **Modifier le plan** pour mettre à niveau ou passer à une version antérieure en fonction de l’utilisation des données de l’application API. Vous pouvez également utiliser cette fonctionnalité pour voir votre utilisation des données.
- Lorsque vous créez un connecteur qui possède des tables, comme le connecteur SQL, vous pouvez éventuellement entrer un nom de table à laquelle vous connecter. Un schéma basé sur la table est automatiquement créé et disponible lorsque vous cliquez sur **Télécharger les schémas**. Vous pouvez ensuite utiliser ce schéma téléchargé pour créer une transformation ou une carte.

## Modifier votre connecteur ou les valeurs de configuration API entrées

Après avoir configuré ou créé votre connecteur, vous pouvez modifier les valeurs que vous avez entrées. Par exemple, si vous avez configuré le connecteur SQL et que vous souhaitez en modifier le nom ou modifier le nom de la table, rendez-vous dans le panneau de l’application API de votre connecteur.

Procédez comme suit :

1. Ouvrez votre connecteur ou l’application API. Le panneau de l’application API s’ouvre.
2. Dans la fenêtre **Bases**, cliquez sur le lien hypertexte sous la propriété de l’hôte. Le lien hypertexte est nommé de la manière suivante : *slackconnector* ou *microsoftsqlconnector123* :

	![][apiapphost]

3. Dans le panneau Hôte d’application API, sélectionnez **Paramètres**. Dans le panneau Paramètres, sélectionnez **Paramètres de l’application**. Vos valeurs de configuration sont répertoriées sous **Paramètres de l’application** :

	![][hostsettings]

4. Cliquez sur le paramètre que vous souhaitez modifier, entrez la nouvelle valeur et **Enregistrez** vos modifications.


## Installer le Gestionnaire de connexion hybride - Facultatif

![][hcsetup]

Le Gestionnaire de connexion hybride vous donne la possibilité de vous connecter à un système SQL Server ou SAP local. Cette connectivité hybride utilise Microsoft Azure Service Bus pour effectuer la connexion et le contrôle de la sécurité entre vos ressources Microsoft Azure et vos ressources locales.

Consultez la page [Utilisation du Gestionnaire de connexion hybride dans Azure App Service](app-service-logic-hybrid-connection-manager.md).

> [AZURE.NOTE] Le Gestionnaire de connexion hybride est requis uniquement si vous vous connectez à une ressource locale derrière le pare-feu. Si vous n’êtes pas connecté à un système local, le Gestionnaire de connexion hybride peut ne pas figurer dans le panneau de votre connecteur.

## Analyser les performances
Les mesures de performance sont des fonctionnalités intégrées incluses avec chaque application API que vous créez. Ces mesures sont spécifiques de votre application API hébergée dans Azure : Exemples de mesures :

![][monitoring]

Vous pouvez :

- Sélectionner **Demandes et erreurs** pour ajouter des mesures de performances, notamment les codes d’erreur HTTP connus (tels que les codes d’état HTTP 200, 400 ou 500). Vous pouvez également consulter les temps de réponse et identifier le nombre de demandes envoyées à l’application API, ainsi que la quantité de données entrantes et sortantes. En fonction des mesures de performance, vous pouvez créer des alertes par courrier électronique au cas où une mesure dépasse un seuil de votre choix.
- Dans **Utilisation**, vous pouvez savoir quel pourcentage du **processeur** est utilisé par l’application API, connaître le **Quota d’utilisation** actuel (en Mo) et identifier le taux d’utilisation maximal des données, en fonction du niveau de coût. Consulter la valeur d’**Estimation des dépenses** peut vous aider à déterminer le coût potentiel d’exécution de votre application API.
- Sélectionner **Processus** pour ouvrir l’Explorateur de processus (ce champ répertorie vos instances web et leurs propriétés, y compris le nombre de threads et le taux d’utilisation de la mémoire).

À l’aide de ces outils, vous pouvez déterminer si le plan App Service doit être agrandi ou réduit, en fonction des besoins de votre entreprise. Ces fonctionnalités sont intégrées au portail sans besoin d’outils supplémentaires.

## Contrôler la sécurité

Les applications API utilisent une sécurité basée sur les rôles. Ces rôles s’appliquent à la totalité de l’expérience Azure, y compris les applications API et d’autres ressources Azure. Les rôles sont les suivants :

Rôle | Description
--- | ---
Propriétaire | Dispose d’un accès complet à l’expérience de gestion et peut autoriser l’accès à d’autres utilisateurs ou groupes.
Collaborateur | Dispose d’un accès complet à l’expérience de gestion. Ne peut pas accorder l’accès à d’autres utilisateurs ou groupes.
Lecteur | Peut afficher toutes les ressources à l’exception des secrets.
Administrateur de l'accès utilisateur | Peut afficher toutes les ressources, créer et gérer des rôles et créer/gérer des tickets de support.

Consultez la page [Contrôle d’accès en fonction basé sur les rôles dans le portail Microsoft Azure](../active-directory/role-based-access-control-configure.md).

Vous pouvez facilement ajouter des utilisateurs et leur attribuer des rôles spécifiques dans votre application API. Le portail affiche les utilisateurs disposant d’un accès, ainsi que leur rôle :

![][access]

- Sélectionnez **Utilisateurs** pour ajouter ou supprimer un utilisateur et attribuer un rôle.
- Sélectionnez **Rôles** pour voir tous les utilisateurs ayant un rôle spécifique et attribuer ou supprimer un rôle pour un utilisateur.


## Autres points positifs
- Sélectionnez **Définition API** pour ouvrir le fichier Swagger créé automatiquement pour votre application API spécifique.
- Sélectionnez **Dépendances** pour afficher les fichiers requis par votre application API. Par exemple, si vous utilisez le connecteur SAP, vous installez quelques fichiers supplémentaires sur le Gestionnaire de connexion hybride local. Ces dépendances sont affichées dans le panneau de votre application API.

>[AZURE.IMPORTANT] Lorsque vous ouvrez les propriétés de votre application API et regardez sous l’onglet **Bases**, les liens **Hôte** et **Passerelle** ouvrent de nouveaux panneaux :
>
> ![][host]
>
>Ces propriétés sont spécifiques du site web qui héberge votre application API. Lorsque vous utilisez une application API ou un connecteur intégré, la plupart de ces propriétés ne s’appliquent pas réellement et nous vous recommandons de ne pas les mettre à jour. Si vous avez créé votre propre application API dans Visual Studio et que vous l’avez déployée dans votre abonnement Azure, vous pouvez utiliser les panneaux Hôte et Passerelle. <br/><br/>


>[AZURE.NOTE] Pour commencer à utiliser Logic Apps avant de vous inscrire pour ouvrir un compte Azure, accédez à [Try Logic Apps (Essayer Logic Apps)](https://tryappservice.azure.com/?appservice=logic). Vous pouvez créer une application logique temporaire. Aucune carte de crédit n’est nécessaire et vous ne prenez aucun engagement.

## En savoir plus

[Analyser vos applications logiques](app-service-logic-monitor-your-logic-apps.md)<br/>
[Liste des connecteurs et applications API dans App Service](app-service-logic-connectors-list.md)<br/>
[Contrôle d’accès basé sur les rôles dans le portail Azure de Microsoft](../active-directory/role-based-access-control-configure.md)<br/>
[Utilisation du Gestionnaire de connexion hybride dans Azure App Service](app-service-logic-hybrid-connection-manager.md)


<!--Image references-->
[browse]: ./media/app-service-logic-monitor-your-connectors/browse.png
[settings]: ./media/app-service-logic-monitor-your-connectors/settings.png
[hcsetup]: ./media/app-service-logic-monitor-your-connectors/hcsetup.png
[monitoring]: ./media/app-service-logic-monitor-your-connectors/monitoring.png
[access]: ./media/app-service-logic-monitor-your-connectors/access.png
[host]: ./media/app-service-logic-monitor-your-connectors/host.png
[hostsettings]: ./media/app-service-logic-monitor-your-connectors/hostsettings.png
[apiapphost]: ./media/app-service-logic-monitor-your-connectors/apiapphost.png

<!---HONumber=AcomDC_0803_2016-->