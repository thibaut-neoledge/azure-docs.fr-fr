<properties linkid="web-sites-staged-publishing" urlDisplayName="How to stage sites on Microsoft Azure" pageTitle="Staged Deployment on Microsoft Azure Web Sites" metaKeywords="Microsoft Azure Web Sites, Staged Deployment, Site Slots" description="Learn how to use staged publishing on Microsoft Azure Web Sites." metaCanonical="" services="web-sites" documentationCenter="" title="Staged Deployment on Microsoft Azure Web Sites" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

Déploiement intermédiaire dans Sites Web Microsoft Azure
========================================================

Sommaire
--------

-   [Vue d'ensemble](#Overview)
-   [Ajout d'un emplacement de déploiement à un site Web](#Add)
-   [À propos de la configuration des emplacements de déploiement](#AboutConfiguration)
-   [Basculement des emplacements de déploiement](#Swap)
-   [Basculement du site de production vers le site intermédiaire](#Rollback)
-   [Suppression de l'emplacement d'un site](#Delete)
-   [Cmdlets Azure PowerShell pour les emplacements de site](#PowerShell)
-   [Commandes de l'interface de ligne de commande interplateforme Azure (xplat-cli) pour les emplacements de site](#CLI)

## Présentation : ##
la capacité de créer des emplacements de site pour les sites en mode Standard exécutés dans Sites Web Microsoft Azure permet d'utiliser un flux de travail de déploiement intermédiaire. Vous pouvez créer un emplacement de site intermédiaire ou de développement pour chaque site de production par défaut (à présent nommé « emplacement de production ») et basculer entre ces emplacements sans temps mort. Le déploiement intermédiaire est essentiel pour les scénarios suivants :

-   **Validation pré-déploiement** : lorsque vous déployez un contenu ou une configuration dans un emplacement de site intermédiaire, vous pouvez valider les modifications avant de les basculer en production.

-   **Création et intégration de contenu de site** : vous pouvez ajouter progressivement des mises à jour de contenu dans votre emplacement de déploiement intermédiaire, puis basculer vers l'emplacement en production une fois vos mises à jour terminées.

-   **Restauration d'un site de production** : si les modifications basculées en production ne vous conviennent pas, vous pouvez restaurer le contenu d'origine de production immédiatement.

Microsoft Azure initialise toutes les instances du site source avant de basculer en production, éliminant ainsi les risques de démarrages à froid lorsque vous déployez votre contenu. La redirection du trafic est transparente et aucune demande n'est abandonnée durant les opérations de basculement. Actuellement, seul un emplacement de déploiement en plus de l'emplacement de production par défaut est pris en charge pour chaque site Web Standard.

## Ajout d'un emplacement de déploiement à un site Web ##

Le site doit s'exécuter en mode Standard pour permettre la création d'emplacement de site.

1.  Sur la page Démarrage rapide ou dans la section Aperçu rapide de la page Tableau de bord de votre site Web, cliquez sur **Add a new deployment slot**.

    ![Add a new deployment slot](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)

    > [WACOM.NOTE] Si le site Web n'est pas déjà en mode Standard, le message **Vous devez être en mode Standard pour activer la publication intermédiaire** s'affiche. À ce moment, vous pouvez sélectionner **Mise à niveau** et accéder à l'onglet **Mise à l'échelle** de votre site Web, avant de continuer.

2.  La boîte de dialogue **Add New Deployment Slot** s'affiche.

    ![Boîte de dialogue Add New Deployment Slot](./media/web-sites-staged-publishing/AddNewDeploymentSlotDialog.png)

    Entrez un nom pour l'emplacement de déploiement. Celui-ci ne doit pas dépasser 60 caractères alphanumériques. Aucun caractère spécial ou espace n'est autorisé.

3.  Dans la liste des sites Web, développez la coche à gauche du nom de votre site Web pour afficher l'emplacement de déploiement. Il a le nom de votre site de production, suivi du nom de l'emplacement de déploiement que vous avez fourni, entre parenthèses.

    ![Liste des sites avec emplacement de déploiement](./media/web-sites-staged-publishing/SiteListWithStagedSite.png)

4.  Lorsque vous sélectionnez le nom de l'emplacement de site de déploiement, une page s'ouvre. Elle contient plusieurs onglets, comme n'importe quel autre site Web. ***nom de votre site Web*(*nom de l'emplacement de déploiement*)** s'affiche en haut de la page du portail, pour vous rappeler que vous affichez l'emplacement de site de déploiement.

    ![Titre de l'emplacement de déploiement](./media/web-sites-staged-publishing/StagingTitle.png)

Vous pouvez à présent mettre à jour le contenu et la configuration de votre emplacement de site de déploiement. Utilisez le profil de publication ou les informations d'identification associées à l'emplacement du site de déploiement pour les mises à jour de contenu.

## À propos de la configuration des emplacements de déploiement ##
Lorsqu'un emplacement de déploiement est créé, la configuration de cet emplacement est clonée depuis l'emplacement du site de production par défaut. Vous pouvez modifier toutes les configurations d'emplacements de site.

**Configuration changée lors du basculement d'emplacement** :

-   Paramètres généraux :
-   Chaînes de connexion
-   Mappages de gestionnaires
-   Paramètres de surveillance et de diagnostics

**Configuration inchangée lors du basculement d'emplacement** :

-   Points de terminaison de publication
-   Noms de domaine personnalisés
-   Certificats SSL et liaisons
-   Paramètres de mise à l'échelle

**Remarques** :

-   Les emplacements de déploiement sont uniquement disponibles pour les sites en mode Standard.

-   Vous ne pouvez plus basculer un site lorsqu'il est en mode Gratuit, Partagé ou Basique.

-   Vous devez d'abord configurer un emplacement de déploiement comme s'il était en production avant de procéder à son basculement.

-   Un emplacement de déploiement pointe par défaut vers la même base de données que celle du site de production. Cependant, vous pouvez le configurer pour qu'il pointe vers une autre base de données en modifiant sa ou ses chaînes de connexion de base de données. Vous pouvez ensuite restaurer sa ou ses chaînes de connexions de base de données d'origine juste avant de le basculer en production.

## Basculement des emplacements de déploiement ##

1.  Pour basculer un emplacement de déploiement, sélectionnez-le dans la liste des sites Web puis, dans la barre de commandes, cliquez sur le bouton **Swap**.

    ![Bouton Swap](./media/web-sites-staged-publishing/SwapButtonBar.png)

2.  La boîte de dialogue Swap Deployments s'affiche. Elle vous permet de choisir l'emplacement de site source et le site de destination.

    ![Boîte de dialogue Swap Deployments](./media/web-sites-staged-publishing/SwapDeploymentsDialog.png)

3.  Cliquez sur la coche pour terminer l'opération. Une fois l'opération terminée, l'emplacement de site est basculé.

## Basculement du site de production vers le site intermédiaire : ##
si vous identifiez des erreurs de contenu ou de configuration après le basculement en production, vous pouvez restaurer en production un emplacement de déploiement (qui était auparavant votre site de production), puis résoudre les problèmes de la nouvelle version de votre site tandis qu'il est en mode intermédiaire.

> [WACOM.NOTE] Pour réussir la restauration, vous ne devez pas modifier le contenu et la configuration du précédent site de production que contient l'emplacement du site de déploiement.

## Suppression de l'emplacement d'un site ##

Dans la barre de commandes située en bas de la page du portail Sites Web Azure, cliquez sur **Supprimer**. Vous pourrez choisir de supprimer le site Web et tous ses emplacements de déploiement ou de supprimer uniquement l'emplacement de déploiement.

![Supprimer un emplacement de déploiement](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

Lorsque vous répondez **Oui** au message de confirmation, le système supprime l'un des emplacements ou tous les emplacements, en fonction de votre choix.

**Remarques** :

-   La mise à l'échelle est uniquement disponible pour les emplacements de site de production. Elle ne l'est pas pour les autres emplacements de site.

-   La gestion des ressources liées est uniquement prise en charge pour les emplacements de site de production.

-   Vous pouvez toujours publier directement vers votre site de production si vous le souhaitez.

-   Pour le moment, vos emplacements de déploiement (sites) partagent les mêmes ressources que vos emplacements de production (sites) et exécutent les mêmes machines virtuelles. Si vous démarrez un test de contrainte sur un emplacement intermédiaire, votre environnement de production sera soumis à une charge de contrainte similaire.

## Cmdlets Azure PowerShell pour les emplacements de site

Azure PowerShell est un module fournissant des cmdlets pour gérer Azure via Windows PowerShell, notamment la prise en charge des emplacements de déploiement Sites Web Azure.

-   Pour plus d'informations sur l'installation et la configuration d'Azure PowerShell et sur l'authentification d'Azure PowerShell avec votre abonnement Azure, consultez l'article [Installation et configuration d'Azure PowerShell](http://www.windowsazure.com/en-us/documentation/articles/install-configure-powershell).

-   Pour répertorier les cmdlets disponibles pour les sites Web Azure dans PowerShell, appelez `help AzureWebsite`.

* * * * *

### Get-AzureWebsite

La cmdlet **Get-AzureWebsite** permet d'obtenir des informations sur les sites Web Azure de l'abonnement en cours, comme le montre l'exemple suivant.

`Get-AzureWebsite siteslotstest`

* * * * *

### New-AzureWebsite

Vous pouvez créer un emplacement de site pour n'importe quel site Web en mode Standard en utilisant la cmdlet **New-AzureWebsite** et en indiquant les noms du site et de l'emplacement. Lors de la création de l'emplacement de déploiement, indiquez la même région que celle du site, comme le montre l'exemple suivant.

`New-AzureWebsite siteslotstest –Slot staging –Location “West US”`

* * * * *

### Publish-AzureWebsiteProject

La cmdlet **Publish-AzureWebsiteProject** permet de déployer du contenu, comme dans l'exemple suivant.

`Publish-AzureWebsiteProject -Name siteslotstest -Slot staging -Package [path].zip`

* * * * *

### Show-AzureWebsite

Une fois les mises à jour de contenu et de configuration appliquées au nouvel emplacement, vous pouvez les valider en accédant à l'emplacement en utilisant la cmdlet **Show-AzureWebsite**, comme le montre l'exemple suivant.

`Show-AzureWebsite -Name siteslotstest -Slot staging`

* * * * *

### Switch-AzureWebsiteSlot

La cmdlet **Switch-AzureWebsiteSlot** peut effectuer un basculement pour appliquer l'emplacement de déploiement mis à jour au site de production, comme le montre l'exemple suivant. Le site de production ne subira ni temps mort, ni démarrage à froid.

`Switch-AzureWebsiteSlot -Name siteslotstest`

* * * * *

### Remove-AzureWebsite

Si vous n'avez plus besoin d'un emplacement de déploiement, vous pouvez le supprimer en utilisant la cmdlet **Remove-AzureWebsite**, comme le montre l'exemple suivant.

`Remove-AzureWebsite -Name siteslotstest -Slot staging`

* * * * *

## Commandes de l'interface de ligne de commande interplateforme Azure (xplat-cli) pour les emplacements de site

L'interface de ligne de commande interplateforme Azure (xplat-cli) fournit des commandes interplateforme fonctionnant avec Azure, notamment la prise en charge de la gestion des emplacements de déploiement sur les sites Web Azure.

-   Pour obtenir des instructions sur l'installation et la configuration de l'interface xplat-cli, et notamment des informations sur la connexion de cette dernière à votre abonnement Azure, consultez l'article [Installation et configuration de l'interface de ligne de commande interplateforme Azure](http://www.windowsazure.com/en-us/documentation/articles/xplat-cli).

-   Pour répertorier les commandes disponibles pour les sites Web Azure dans xplat-cli, appelez `azure site -h`.

* * * * *

## azure site list
Pour plus d'informations sur les sites Web Azure de l'abonnement en cours, appelez **azure site list**, comme le montre l'exemple suivant.

`azure site list siteslotstest`

* * * * *

## azure site create
Pour créer un emplacement de site pour chaque site Web en mode Standard, appelez **azure site create** et indiquez le nom d'un site existant, ainsi que le nom de l'emplacement à créer, comme le montre l'exemple suivant.

`azure site create siteslotstest --slot staging`

Pour activer le contrôle de code source pour le nouvel emplacement, utilisez l'option **--git**, comme dans l'exemple suivant.

`azure site create –-git siteslotstest --slot staging`

* * * * *

## azure site swap
Pour appliquer l'emplacement de déploiement mis à jour au site de production, utilisez la commande **azure site swap** pour effectuer un basculement, comme le montre l'exemple suivant. Le site de production ne subira ni temps mort, ni démarrage à froid.

`azure site swap siteslotstest`

* * * * *

## azure site delete
Pour supprimer un emplacement de déploiement dont vous n'avez plus besoin, utilisez la commande **azure site delete**, comme dans l'exemple suivant.

`azure site delete siteslotstest --slot staging`

* * * * *
