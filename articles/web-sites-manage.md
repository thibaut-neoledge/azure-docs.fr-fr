<properties 
	pageTitle="Gérer les applications web à l'aide du portail Azure" 
	description="Vue d'ensemble des tâches de gestion d'application web dans Microsoft Azure à l'aide du portail Azure." 
	services="app-service\web" 
	documentationCenter="" 
	authors="MikeWasson" 
	writer="mwasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="mwasson"/>

# Gérer les applications web à l'aide du portail Azure

Dans le [portail Azure](https://portal.azure.com), vous gérez vos applications web par le biais d'une série de *blades*, qui sont des conteneurs pour les différents composants. (Pour découvrir une vue d'ensemble du portail Azure, reportez-vous au guide des modifications du [portail](http://go.microsoft.com/fwlink/?LinkId=529715).) 

Pour visualiser le panneau de votre application web, cliquez sur **Accueil**, puis sur le nom de l'application, ou cliquez sur **Parcourir** pour voir toutes vos ressources Azure.

![](./media/web-sites-manage/manage01.png)

La partie supérieure du panneau comporte des contrôles pour certaines actions courantes :

- **Paramètres** vous permet de visualiser la liste de tous les paramètres de gestion.

- **Parcourir** vous permet d'ouvrir l'application web dans la fenêtre du navigateur. 

- D'autres boutons vous permettent de démarrer ou d'arrêter l'application, de basculer les emplacements de déploiement, de supprimer l'application, etc.

**Informations de base** répertorie les informations de base concernant l'application, notamment l'URL, l'emplacement, le plan [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) et l'état actuel (en cours d'exécution, arrêtée, etc.).

Les différentes parties situées sous **Informations de base** vous permettent de surveiller, gérer et déployer votre application. Pour personnaliser la disposition du panneau, cliquez avec le bouton droit, puis sélectionnez **Personnaliser** ou **Ajouter des parties**. Le reste de cette rubrique décrit les parties de la disposition par défaut.

## Surveillance

L'intitulé **Surveillance** est suivi d'un graphique affichant les métriques relatives à votre application web. Pour configurer le graphique, cliquez sur **Modifier**. Vous pouvez sélectionner l'intervalle de temps et les métriques à afficher. Les métriques disponibles comprennent le nombre de demandes, le temps de réponse moyen, les erreurs de serveur et le temps processeur.   

![](./media/web-sites-manage/manage02.png)

Pour ajouter une règle d'alerte, cliquez sur le graphique, puis sur **Ajouter une alerte**. Une règle d'alerte vous informe lorsqu'une métrique spécifique atteint un seuil. Par exemple, vous pouvez être averti lorsque le nombre d'erreurs de serveur dépasse une valeur donnée sur une période de 5 minutes. 

**Surveillance** comporte également des parties permettant de configurer l'analyse et la surveillance de l'application, et de créer des tests de disponibilité. Pour plus d'informations, voir [Surveillance des notions de base pour les applications web dans Azure App Service](web-sites-monitor.md).

## Usage

![](./media/web-sites-manage/manage03.png)

- **Stockage de système de fichiers** indique l'espace de stockage utilisé par votre application.
- **Quotas** indique la façon dont votre application utilise son quota d'utilisation des ressources. Cliquez sur cette partie pour visualiser les détails associés.
- **Mettre à l'échelle** vous permet de faire évoluer le nombre d'instances et de configurer la fonction d'ajustement automatique. Voir [Faire évoluer une application web dans Azure App Service](how-to-scale-websites.md).  
- **Coût estimé** indique une estimation du coût de l'application.
- **Niveau de tarification** vous permet de modifier le niveau de tarification.

## Opérations

![](./media/web-sites-manage/manage04.png)

- **Événements** : vous permet de visualiser les journaux des événements.  
- **Règles d'alerte** : vous permet de visualiser les règles d'alerte et d'ajouter d'autres alertes.
- **Diffusion de journaux** (A) : vous permet de visualiser les journaux des applications. Pour activer les journaux, accédez à **Paramètres**, puis ouvrez le panneau **Journaux de diagnostics**. 
- **Console** : vous permet d'ouvrir une ligne de commande qui s'exécute dans le portail. Vous pouvez utiliser cette dernière pour exécuter des commandes telles que `mkdir` et `dir`.  
- **Explorateur de processus** (B) : vous permet de visualiser les processus en cours d'exécution dans l'application, notamment la plage de travail et le nombre de threads.

## Déploiement
 
![](./media/web-sites-manage/manage05.png)

- Configurer un déploiement continu (voir [Utilisation de Git pour déployer des applications web dans Azure App Service](web-sites-publish-source-control.md))
- Emplacements de déploiement (voir [Déployer dans des environnements intermédiaires pour les applications web dans Azure App Service](web-sites-staged-publishing.md))
- Définir les informations d'identification de déploiement pour Git ou FTP 

## Mise en réseau

- Connecter l'application à un réseau virtuel
- Ajouter des connexions hybrides

>[AZURE.NOTE] Si vous souhaitez commencer à utiliser Azure App Service avant d'ouvrir un compte Azure, accédez au site permettant d'[essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application web de départ de courte durée dans App Service. Aucune carte de crédit n'est requise, et vous ne prenez aucun engagement.

## Étapes suivantes

- [Faire évoluer une application web dans Azure App Service](how-to-scale-websites.md)
- [Exécuter des tâches en arrière-plan avec les tâches web](web-sites-create-web-jobs.md)
- [Sauvegardes des applications web Azure](web-sites-backup.md) et [Restauration](web-sites-restore.md)
- [Surveillance des notions de base pour les applications web dans Azure App Service](web-sites-monitor.md)

## Nouveautés
* Pour plus d'informations sur le remplacement de Sites Web par App Service, voir : [Azure App Service et son impact sur les services Azure existants (en anglais)](http://go.microsoft.com/fwlink/?LinkId=529714)
* Pour plus d'informations sur le remplacement de l'ancien portail par le nouveau portail, voir : [Référence en matière de navigation dans le portail en version préliminaire (en anglais)](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->