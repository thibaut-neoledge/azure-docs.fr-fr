---
title: FAQ Azure App Service Web Apps sous Linux | Microsoft Docs
description: FAQ Azure App Service Web Apps sous Linux.
keywords: azure app service, application web, faq, linux, oss
services: app-service
documentationCenter: 
authors: ahmedelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/14/2017
ms.author: aelnably
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 148bc76b7f3e09745cbecfa41710a5e949704948
ms.lasthandoff: 03/08/2017


---

# <a name="azure-app-service-web-apps-on-linux-faq"></a>FAQ Azure App Service Web Apps sous Linux

Avec la mise en production d’Azure App Service sous Linux (actuellement en version préliminaire), nous travaillons à l’ajout de fonctionnalités et l’amélioration de notre plateforme. Voici quelques-unes des questions courantes que nos clients nous ont posées au cours des derniers mois.
Si vous avez une question, veuillez commenter l’article ; nous vous répondrons dès que possible.

## <a name="built-in-images"></a>Images prédéfinies

**Q :** Je veux répliquer les conteneurs Docker prédéfinis fournis par la plateforme. Où se trouvent ces fichiers ?

**R :** vous trouverez tous les fichiers Docker sur [GitHub](https://github.com/azure-app-service). Vous trouverez tous les conteneurs Docker sur [Docker Hub](https://hub.docker.com/u/appsvc/).

**Q :** Quelles sont les valeurs attendues de la section Fichier de démarrage lorsque je configure la pile d’exécution ?

**R :** Pour Node.Js, vous spécifiez le fichier de configuration PM2 ou votre fichier de script. Pour .NET Core, vous spécifiez le nom de votre DLL compilée. Pour Ruby, vous pouvez spécifier le script Ruby avec lequel initialiser votre application.

## <a name="management"></a>Gestion

**Q :** J’appuie sur le bouton Redémarrer dans le portail Azure, mais mon application web ne redémarre pas. Pourquoi ?

**R :** Nous travaillons à l’activation prochaine du bouton de réinitialisation. Maintenant, deux options s’offrent à vous :
- Ajoutez ou modifiez un paramètre d’application factice. Cela forcera votre application web à redémarrer.
- Arrêtez puis démarrez votre application web.

**Q :** Puis-je utiliser Secure Shell (SSH) pour me connecter à la machine virtuelle du conteneur d’application ?

**R :** Non. Nous fournirons prochainement un moyen d’utiliser SSH pour vous connecter au conteneur de l’application.

## <a name="continuous-integrationdeployment"></a>Intégration/Déploiement en continu

**Q :** Mon application web utilise toujours une ancienne image de conteneur Docker après la mise à jour de l’image sur DockerHub. Prenez-vous en charge l’intégration continue / le déploiement continu de conteneurs personnalisés ?

**R :** vous pouvez actualiser le conteneur en arrêtant puis démarrant votre application web. Vous pouvez également modifier ou ajouter un paramètre d’application factice pour forcer une actualisation de votre conteneur. Nous travaillons sur une fonctionnalité d’intégration et déploiement en continu pour les conteneurs personnalisés que nous espérons proposer dans une version ultérieure.

## <a name="language-support"></a>Support multilingue

**Q :** Prenez-vous en charge les applications .NET Core non compilées ?

**R :** Non. Vous devez déployer des applications .NET Core compilées avec toutes les dépendances. Nous envisageons la création d’une expérience de déploiement et de compilation complète pour une version ultérieure.

**Q :** Prenez-vous en charge Composer en tant que gestionnaire de dépendances pour les applications PHP ?

**R :** Non. Vous devez déployer des applications PHP avec toutes les dépendances. Nous envisageons la création d’une expérience de déploiement complète dans une version ultérieure.

## <a name="custom-containers"></a>Conteneurs personnalisés

**Q :** J’utilise mon propre conteneur personnalisé. Mon application se trouve dans le répertoire \home\, mais je ne trouve pas mes fichiers lorsque je parcours le contenu avec le [site SCM](https://github.com/projectkudu/kudu) ou un client FTP. Où sont mes fichiers ?

**R :** Nous montons un partage SMB dans le répertoire \home\. Cela remplace tout le contenu qui s’y trouve.

**Q :** Je veux exposer plusieurs ports sur l’image de mon conteneur personnalisé. Est-ce possible ?

**R :** Cela n’est actuellement pas pris en charge.

**Q :** Puis-je apporter mon propre système de stockage ?

**R :** Cela n’est pas actuellement pris en charge.

**Q :** Je n’arrive pas à parcourir le système de fichiers de mon conteneur personnalisé à partir du site SCM. Pourquoi ?

**R :** Le site SCM s’exécute dans un conteneur distinct. Vous ne pouvez pas vérifier le système de fichiers ou les processus en cours d’exécution du conteneur d’application.

**Q :** Mon conteneur personnalisé écoute un autre port que le port 80. Comment puis-je configurer mon application pour acheminer les demandes vers ce port ?

**R :** Vous pouvez spécifier un paramètre d’application appelé **PORT** et lui attribuer la valeur du numéro de port attendu.

## <a name="pricing-and-sla"></a>Tarifs et contrat SLA

**Q :** Quelle est la tarification pour l’utilisation de la préversion publique ?

**R :** La moitié du nombre d’heures d’exécution de votre application vous sera facturée, à la tarification Azure App Service normale. Cela signifie que vous bénéficiez d’une remise de 50 pour cent sur la tarification normale d’Azure App Service.

## <a name="other"></a>Autres

**Q :** Quels sont les caractères pris en charge dans les noms de paramètres d’application ?

**R :** Vous ne pouvez utiliser que les caractères A-Z, a-z, 0-9 et le trait de soulignement pour les paramètres d’application.

**Q :** Où puis-je demander de nouvelles fonctionnalités ?

**R :** Vous pouvez proposer votre idée sur le [Forum de commentaires pour Web Apps](https://aka.ms/webapps-uservoice). Ajoutez « [Linux] » au titre de votre idée.

## <a name="next-steps"></a>Étapes suivantes
* [Qu’est-ce qu’App Service sur Linux ?](app-service-linux-intro.md)
* [Création d’applications Web dans App Service sur Linux](app-service-linux-how-to-create-a-web-app.md)

