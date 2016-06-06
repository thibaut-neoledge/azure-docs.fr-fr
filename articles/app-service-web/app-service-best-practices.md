<properties
	pageTitle="Meilleures pratiques pour Azure App Service"
	description="Découvrez les meilleures pratiques et la résolution des problèmes pour Azure App Service."
	services="app-service"
	documentationCenter=""
	authors="dariagrigoriu"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/19/2016"
	ms.author="dariagrigoriu"/>
    
# Meilleures pratiques pour Azure App Service

Cet article résume les meilleures pratiques dans l’utilisation de [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714).

## <a name="colocation"></a>Colocalisation
Lorsqu’une base de données et des ressources Azure composant une solution, telle qu’une application web, se trouvent dans des régions différentes, les effets entraînés peuvent être les suivants :

*  Une latence plus élevée dans la communication entre les ressources
*  Des frais plus élevés pour le transfert de données sortantes entre les régions comme indiqué dans la [page de tarification Azure](https://azure.microsoft.com/pricing/details/data-transfers)

La colocalisation dans la même région est idéale pour les ressources Azure composant une solution telle qu’une application web et un compte de stockage ou de base de données utilisé pour conserver du contenu ou des données. Lors de la création de ressources, assurez-vous que celles-ci se trouvent dans la même région Azure, à moins que des motifs commerciaux ou conceptuels spécifiques entravent cette possibilité. Vous pouvez déplacer une application App Service vers la région dans laquelle se trouve votre base de données en tirant parti de la [fonctionnalité de clonage de l’application App Service](app-service-web-app-cloning-portal.md), actuellement disponible pour les applications du plan App Service Premium.

## <a name="memoryresources"></a>Quand les applications consomment plus de mémoire que prévu
Si vous remarquez qu’une application consomme plus de mémoire que prévu, indiqué par des analyses ou des recommandations de service, envisagez d’exécuter la [fonctionnalité auto-curative d’App Service](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Une des options de la fonctionnalité auto-curative consiste à prendre des actions personnalisées, basées sur un seuil de mémoire. Les actions vont des notifications par courrier électronique à la prévention immédiate des risques par le recyclage du processus de travail, en passant par l’examen via le vidage de la mémoire. La fonctionnalité auto-curative peut être configurée via le fichier web.config et une interface utilisateur conviviale, comme le décrit ce billet de blog pour l’[extension du site de support App Service](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).

## <a name="CPUresources"></a>Quand les applications consomment plus d’UC que prévu
Si vous remarquez qu’une application consomme plus d’UC que prévu ou qu’elle provoque des pics de consommation d’UC répétés, indiqué par des analyses ou des recommandations de service, envisagez de faire monter ou descendre en puissance le plan App Service. Si votre application est « avec état », la seule option possible est la montée en charge. Si l’application est « sans état », l’augmentation de la taille des instances vous confère plus de souplesse et un potentiel de mise à l’échelle plus élevé.

Pour plus d’informations sur les applications « sans état » et « avec état », visualisez la vidéo [Planning a Scalable End-to-End Multi-Tier Application on Microsoft Azure Web App](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid) (Planification d’une application multiniveau complète et évolutive sur l’application web Microsoft Azure). Pour plus d’informations sur les options de montée en charge et de mise à l’échelle automatique, consultez [Mise à l’échelle d’une application web dans Microsoft Azure App Service](web-sites-scale.md).

## <a name="socketresources"></a>Quand les ressources de socket sont épuisées
L’utilisation des bibliothèques clientes constitue une raison courante de l’épuisement des connexions TCP sortantes ; ces bibliothèques ne sont pas implémentées pour la réutilisation des connexions TCP ou, dans le cas d’un protocole de niveau supérieur tel que HTTP, la logique de connexion persistante n’est pas utilisée. Veuillez consulter la documentation pour chacune des bibliothèques référencées par les applications dans votre plan App Service afin de vous assurer que celles-ci sont configurées ou accessibles dans votre code pour une réutilisation efficace des connexions sortantes. Suivez également les instructions de la documentation des bibliothèques pour une création et une mise en production appropriées ainsi que pour un nettoyage servant à éviter la fuite des connexions. Lorsque les examens des bibliothèques clientes sont en cours d’exécution, leur impact peut être atténué en augmentant la taille des instances.

<!---HONumber=AcomDC_0525_2016-->