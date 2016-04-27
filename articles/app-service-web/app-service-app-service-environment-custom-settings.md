<properties 
	pageTitle="Paramètres personnalisés pour les environnements App Service" 
	description="Paramètres de configuration personnalisés pour les environnements App Service" 
	services="app-service" 
	documentationCenter="" 
	authors="stefsch" 
	manager="nirma" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/08/2016" 
	ms.author="stefsch"/>

# Paramètres de configuration personnalisés pour les environnements App Service

## Vue d’ensemble ##
Les environnements App Service étant isolés pour chaque client, certains paramètres de configuration peuvent être appliqués exclusivement à un environnement App Service. Cet article décrit les différentes personnalisations d’environnement App Service disponibles.

Les personnalisations d’environnement App Service sont stockées au moyen d’un tableau dans le nouvel attribut « clusterSettings » figurant dans le dictionnaire « Properties » de l’entité ARM *hostingEnvironments*.

L’extrait de modèle ARM abrégé ci-dessous montre l’attribut « clusterSettings » :


    "resources": [
    {
       "apiVersion": "2015-08-01",
       "type": "Microsoft.Web/hostingEnvironments",
       "name": ...,
       "location": ...,
       "properties": {
          "clusterSettings": [
             {
                 "name": "nameOfCustomSetting",
                 "value": "valueOfCustomSetting"
             }
          ],
          "workerPools": [ ...],
          etc...
       }
    }

L’attribut « clusterSettings » peut être inclus dans un modèle ARM pour mettre à jour l’environnement App Service.

Vous pouvez aussi mettre à jour la valeur de l’attribut via l’[Explorateur de ressources Azure](https://resources.azure.com). Dans l’Explorateur de ressources Azure, accédez au nœud de l’environnement App Service (abonnements--> resourceGroups--> fournisseurs--> Micrososft.Web--> hostingEnvironments), puis cliquez sur l’environnement App Service à mettre à jour.

Dans la fenêtre de navigation de droite, cliquez sur « Lecture/écriture » dans la barre d’outils du haut pour autoriser la modification interactive dans l’Explorateur de ressources. Cliquez ensuite sur le bouton bleu « Modifier » pour permettre la modification du modèle ARM. Accédez au bas de la fenêtre de navigateur de droite. L’attribut « clusterSettings », dont vous pouvez entrer ou mettre à jour la valeur, se trouve tout en bas.

Tapez (ou copiez et collez) le tableau de valeurs de configuration souhaité dans l’attribut « clusterSettings ». Cliquez ensuite sur le bouton vert « PUT » situé en haut de la fenêtre de navigation de droite pour valider la modification apportée à l’environnement App Service.

Quelle que soit le méthode de mise à jour de l’environnement App Service choisie, une fois la modification envoyée, il faut compter environ 30 minutes, multiplié par le nombre de serveurs frontaux présents dans l’environnement App Service, avant que la modification prenne effet. Par exemple, si un environnement App Service a quatre serveurs frontaux, la mise à jour de la configuration prendra environ deux heures. Tant que la modification de la configuration n’est pas déployée, aucune autre opération de mise à l’échelle ou de modification de configuration n’est possible dans l’environnement App Service.

## Désactivation de TLS 1.0 ##
Il est fréquent que des clients, en particulier ceux faisant l’objet d’audits de conformité PCI, demandent à ce qu’il soit possible de désactiver explicitement TLS 1.0 pour leurs applications.

TLS 1.0 peut être désactivé avec l’entrée *clusterSettings* suivante :

        "clusterSettings": [
            {
                "name": "DisableTls1.0",
                "value": "1"
            }
        ],



## Prise en main
Le site de modèles ARM de démarrage rapide Azure comprend un modèle dont la définition de base permet de [créer un environnement App Service](https://azure.microsoft.com/documentation/templates/201-web-app-ase-create/)


<!-- LINKS -->

<!-- IMAGES -->
 

<!---HONumber=AcomDC_0413_2016-->