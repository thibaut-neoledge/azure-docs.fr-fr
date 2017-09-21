---
title: Forum aux questions sur Azure App Service Web App for Containers | Microsoft Docs
description: Forum aux questions sur Azure App Service Web App for Containers.
keywords: azure app service, application web, faq, linux, oss
services: app-service
documentationCenter: 
author: ahmedelnably
manager: erikre
editor: 
ms.assetid: 
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: aelnably;wesmc
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: 146c598f5cb62612327ce679a6bfaadc8312b149
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---
# <a name="azure-app-service-web-app-for-containers-faq"></a>Forum aux questions sur Azure App Service Web App for Containers

Avec la publication de Web App for Containers, nous travaillons à l’ajout de fonctionnalités et à l’amélioration de notre plateforme. Voici quelques-unes des questions courantes que nos clients nous ont posées au cours des derniers mois.
Si vous avez une question, commentez l’article ; nous vous répondrons dès que possible.

## <a name="built-in-images"></a>Images prédéfinies

**Q :** Je veux répliquer les conteneurs Docker prédéfinis fournis par la plateforme. Où se trouvent ces fichiers ?

**R :** vous trouverez tous les fichiers Docker sur [GitHub](https://github.com/azure-app-service). Vous trouverez tous les conteneurs Docker sur [Docker Hub](https://hub.docker.com/u/appsvc/).

**Q :** Quelles sont les valeurs attendues de la section Fichier de démarrage lorsque je configure la pile d’exécution ?

**R :** Pour Node.Js, vous spécifiez le fichier de configuration PM2 ou votre fichier de script. Pour .NET Core, vous spécifiez le nom de votre DLL compilée. Pour Ruby, vous pouvez spécifier le script Ruby avec lequel initialiser votre application.

## <a name="management"></a>Gestion

**Q :** Que se passe-t-il lorsque j’appuie sur le bouton Redémarrer dans le portail Azure ?

**R :** Cela revient à redémarrer Docker.

**Q :** Puis-je utiliser Secure Shell (SSH) pour me connecter à la machine virtuelle du conteneur d’application ?

**R :** Oui, vous pouvez le faire via le site SCM.

**Q :** Je souhaite créer un plan App Service Linux via le Kit SDK ou un modèle ARM, comment faire ?

**R :** Vous devez définir le champ `reserved` du service d’application sur `true`.

## <a name="continuous-integrationdeployment"></a>Intégration/Déploiement en continu

**Q :** Mon application web utilise toujours une ancienne image de conteneur Docker après la mise à jour de l’image sur DockerHub. Prenez-vous en charge l’intégration continue / le déploiement continu de conteneurs personnalisés ?

**R :** Pour configurer l’intégration/le déploiement en continu des images Azure Container Registry ou DockerHub, consultez l’article [Déploiement continu avec Azure Web Apps for Containers](./app-service-linux-ci-cd.md). Pour les registres privés, vous pouvez actualiser le conteneur en arrêtant puis démarrant votre application web. Vous pouvez également modifier ou ajouter un paramètre d’application factice pour forcer une actualisation de votre conteneur.

**Q :** Prenez-vous en charge les environnements intermédiaires ?

**R :** Oui.

**Q :** Puis-je utiliser le **déploiement web** pour déployer mon application web ?

**R :** Oui, vous devez définir le paramètre d’application `WEBSITE_WEBDEPLOY_USE_SCM` sur `false`.

## <a name="language-support"></a>Support multilingue

**Q :** Prenez-vous en charge les applications .NET Core non compilées ?

**R :** Oui.

**Q :** Prenez-vous en charge Composer en tant que gestionnaire de dépendances pour les applications PHP ?

**R :** Oui. Lors d’un déploiement Git, Kudu doit détecter que vous déployez une application PHP (grâce à la présence d’un fichier composer.lock) et déclenchera une installation Composer pour vous.

## <a name="custom-containers"></a>Conteneurs personnalisés

**Q :** J’utilise mon propre conteneur personnalisé. Mon application se trouve dans le répertoire `/home/`, mais je ne trouve pas mes fichiers lorsque je parcours le contenu avec le [site SCM](https://github.com/projectkudu/kudu) ou un client FTP. Où sont mes fichiers ?

**R :** Nous montons un partage SMB dans le répertoire `/home/`. Cela remplace tout le contenu qui s’y trouve.

**Q :** J’utilise mon propre conteneur personnalisé. Je souhaite que la plateforme monte un partage SMB dans le répertoire `/home/`.

**R :** Vous pouvez le faire en définissant le paramètre d’application `WEBSITES_ENABLE_APP_SERVICE_STORAGE` sur `true` ou en le supprimant complètement. Gardez à l’esprit que cela entraîne le redémarrage du conteneur quand le stockage de la plateforme subit une modification. 

Notez que si WEBSITES_ENABLE_APP_SERVICE_STORAGE a pour valeur « false » votre répertoire /home/ n’est pas partagé par les instances d’échelle, et les fichiers qui y sont écrits ne sont pas conservés après un redémarrage.

**Q :** Mon conteneur personnalisé met longtemps à démarrer, et la plateforme le redémarre avant qu’il ait terminé.

**R :** Vous pouvez configurer le temps d’attente de la plateforme avant le redémarrage de votre conteneur. Pour cela, définissez le paramètre d’application `WEBSITES_CONTAINER_START_TIME_LIMIT` sur la valeur souhaitée en secondes. La valeur par défaut est de 230 secondes, la valeur maximale de 600 secondes.

**Q :** Quel est le format de l’URL du serveur de registre privé ?

**R :** Vous devez fournir l’URL de registre complète, y compris `http://` ou `https://`.

**Q :** Quel est le format du nom d’image dans l’option de registre privé ?

**R :** Vous devez ajouter le nom d’image complet, y compris l’URL de registre privé (par exemple : myacr.azurecr.io/dotnet:latest). Les noms d’image qui utilisent un port personnalisé [ne peuvent pas être entrés par le biais du portail](https://feedback.azure.com/forums/169385-web-apps/suggestions/31304650). Utilisez [l’outil en ligne de commande `az`](https://docs.microsoft.com/en-us/cli/azure/webapp/config/container?view=azure-cli-latest#az_webapp_config_container_set) pour définir `docker-custom-image-name`.

**Q :** Je veux exposer plusieurs ports sur l’image de mon conteneur personnalisé. Est-ce possible ?

**R :** Cela n’est actuellement pas pris en charge.

**Q :** Puis-je apporter mon propre système de stockage ?

**R :** Cela n’est pas actuellement pris en charge.

**Q :** Je n’arrive pas à parcourir le système de fichiers de mon conteneur personnalisé à partir du site SCM. Pourquoi ?

**R :** Le site SCM s’exécute dans un conteneur distinct. Vous ne pouvez pas vérifier le système de fichiers ou les processus en cours d’exécution du conteneur d’application.

**Q :** Mon conteneur personnalisé écoute un autre port que le port 80. Comment puis-je configurer mon application pour acheminer les demandes vers ce port ?

**R :** La détection automatique du port est activée, mais vous pouvez aussi spécifier un paramètre d’application appelé **WEBSITES_PORT** et lui attribuer la valeur du numéro de port attendu. Auparavant, la plateforme utilisait le paramètre d’application `PORT`, mais nous envisageons de déconseiller l’utilisation de ce paramètre d’application pour recommander une utilisation exclusive de `WEBSITES_PORT`.

**Q :** Dois-je implémenter HTTPS dans mon conteneur personnalisé ?

**R :** Non, la plateforme gère l’annulation HTTPS au niveau des serveurs frontaux partagés.

## <a name="pricing-and-sla"></a>Tarifs et contrat SLA

**Q :** Le service étant généralement disponible, quelle sont les tarifs ?

**R :** Le nombre d’heures d’exécution de votre application vous est facturé, selon les tarifs Azure App Service normaux.

## <a name="other"></a>Autres

**Q :** Quels sont les caractères pris en charge dans les noms de paramètres d’application ?

**R :** Vous ne pouvez utiliser que les caractères A-Z, a-z, 0-9 et le trait de soulignement pour les paramètres d’application.

**Q :** Où puis-je demander de nouvelles fonctionnalités ?

**R :** Vous pouvez proposer votre idée sur le [Forum de commentaires pour Web Apps](https://aka.ms/webapps-uservoice). Ajoutez « [Linux] » au titre de votre idée.

## <a name="next-steps"></a>Étapes suivantes

* [Présentation d’Azure Web Apps for Containers](app-service-linux-intro.md)
* [Configurer des environnements intermédiaires dans Azure App Service](../../app-service-web/web-sites-staged-publishing.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Déploiement continu avec Azure Web App for Containers](./app-service-linux-ci-cd.md)

