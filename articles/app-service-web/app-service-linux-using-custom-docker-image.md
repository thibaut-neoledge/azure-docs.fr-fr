---
title: "Comment utiliser une image Docker personnalisée pour Azure Web App sur Linux | Microsoft Docs"
description: "Comment utiliser une image Docker personnalisée pour Azure Web App sur Linux."
keywords: azure app service, application web, linux, docker, conteneur
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: 
ms.assetid: b97bd4e6-dff0-4976-ac20-d5c109a559a8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2017
ms.author: naziml;wesmc
ms.translationtype: HT
ms.sourcegitcommit: 1dbb1d5aae55a4c926b9d8632b416a740a375684
ms.openlocfilehash: f51cacb33251d479f48a39014cc2db60a23358d5
ms.contentlocale: fr-fr
ms.lasthandoff: 08/07/2017

---

# <a name="using-a-custom-docker-image-for-azure-web-app-on-linux"></a>Comment utiliser une image Docker personnalisée pour Azure Web App sur Linux #

[!INCLUDE [app-service-linux-preview](../../includes/app-service-linux-preview.md)]


App Service fournit des piles d’applications prédéfinies sur Linux avec la prise en charge de versions spécifiques, comme PHP 7.0 et Node.js 4.5. App Service sur Linux utilise des conteneurs Docker pour héberger ces piles d’applications prédéfinies. Vous pouvez également utiliser une image Docker personnalisée pour déployer votre application web sur une pile d’applications qui n’est pas encore définie dans Azure. Les images Docker personnalisées peuvent être hébergées sur un référentiel Docker public ou privé.


## <a name="how-to-set-a-custom-docker-image-for-a-web-app"></a>Comment : définir une image Docker personnalisée pour une application web
Vous pouvez définir une image Docker personnalisée pour les applications web nouvelles et existantes. Lorsque vous créez une application web sur Linux dans le [portail Azure](https://portal.azure.com/#create/Microsoft.AppSvcLinux), cliquez sur **Configurer le conteneur** pour définir une image Docker personnalisée :

![Image Docker personnalisée pour une nouvelle application web sur Linux][1]


## <a name="how-to-use-a-custom-docker-image-from-docker-hub"></a>Comment : utiliser une image Docker personnalisée à partir de Docker Hub ##
Pour utiliser une image Docker personnalisée à partir de Docker Hub :

1. Dans le [portail Azure](https://portal.azure.com), localisez votre application web sur Linux, puis dans **Paramètres** cliquez sur **Conteneur Docker**.

2.  Sélectionnez **Docker Hub** comme **source de l’image**, puis cliquez sur **Public** ou **Privé** et tapez le **nom de l’image et de la balise facultative**, par exemple `node:4.5`. La **commande de démarrage** est définie automatiquement en fonction de ce qui est défini dans le fichier d’image Docker, mais vous pouvez définir vos propres commandes.  

    ![Configuration de l’image de référentiel public Docker Hub][2]

    Lorsque votre image est issue d’un référentiel privé, vous devez également entrer les informations d’identification Docker Hub (**Nom d’utilisateur** et **Mot de passe**) du référentiel privé Docker Hub.

    ![Configuration de l’image de référentiel public Docker Hub][3]

3. Une fois le conteneur configuré, cliquez sur **Enregistrer**.

## <a name="how-to-use-a-docker-image-from-a-private-image-registry"></a>Utilisation d’une image Docker à partir d’un registre d’images privé ##
Pour utiliser une image Docker personnalisée à partir d’un registre d’images privé :

1. Dans le [portail Azure](https://portal.azure.com), localisez votre application web sur Linux, puis dans **Paramètres** cliquez sur **Conteneur Docker**.

2.  Cliquez sur **Registre privé** comme **source de l’image**. Entrez l**’image et le nom facultatif de la balise**, l**’URL du serveur** du registre privé, ainsi que les informations d’identification (**nom d’utilisateur** et **mot de passe**). Cliquez sur **Save**.

    ![Configuration de l’image Docker à partir du registre privé][4]


## <a name="how-to-set-the-port-used-by-your-docker-image"></a>Comment : définir le port utilisé par votre image Docker ##

Lorsque vous utilisez une image Docker personnalisée pour votre application web, vous pouvez utiliser la variable d’environnement `WEBSITES_PORT` dans votre ficher Docker, qui est ajoutée au conteneur généré. Prenons l’exemple suivant d’un fichier Docker pour une application Ruby :

    FROM ruby:2.2.0
    RUN mkdir /app
    WORKDIR /app
    ADD . /app
    RUN bundle install
    CMD bundle exec puma config.ru -p WEBSITES_PORT -e production

Sur la dernière ligne de commande, la variable d’environnement WEBSITES_PORT est transmise au moment de l’exécution. N’oubliez pas les commandes sont sensibles à la casse.

Auparavant, la plateforme utilisait le paramètre d’application `PORT`, mais nous envisageons de déconseiller l’utilisation de ce paramètre d’application pour recommander une utilisation exclusive de `WEBSITES_PORT`.

Si vous utilisez une image Docker existante créée par une autre personne, vous devrez peut-être spécifier un port autre que le port 80 pour l’application. Pour configurer le port, ajoutez un paramètre d’application nommé `WEBSITES_PORT` avec la valeur, comme indiqué ci-dessous :

![Configuration du paramètre d’application PORT pour une image Docker personnalisée][6]


## <a name="how-to-switch-back-to-using-a-built-in-image"></a>Comment : revenir à l’utilisation d’une image intégrée ##

Pour passer d’une image personnalisée à une image intégrée :

1. Dans le [portail Azure](https://portal.azure.com), localisez votre application web sur Linux, puis dans **Paramètres** cliquez sur **App Service**.

2. Sélectionnez la **pile d’exécution** à utiliser pour l’image intégrée, puis cliquez sur **Enregistrer**. 

![Configuration de l’image Docker intégrée][5]


## <a name="troubleshooting"></a>Résolution de problèmes ##

Si le démarrage de l’application échoue avec une image Docker personnalisée, consultez les journaux Docker dans le répertoire LogFiles. Vous pouvez accéder à ce répertoire par le biais de votre site SCM ou d’un FTP.
Pour journaliser `stdout` et `stderr` à partir de votre conteneur, vous devez activer **Journalisation de conteneur Docker** sous **Journaux de diagnostic**.

![Activation de la journalisation][8]

![Affichage des journaux Docker avec Kudu][7]

Vous pouvez accéder au site SCM à partir d’**Outils avancés** dans le menu **Outils de développement**.

## <a name="next-steps"></a>Étapes suivantes ##

Suivez les liens ci-après pour vous familiariser avec Web App sur Linux.   

* [Présentation d’Azure Web App sur Linux](./app-service-linux-intro.md)
* [Création d’applications web dans Azure Web App sur Linux](./app-service-linux-how-to-create-web-app.md)
* [Utiliser la configuration PM2 pour Node.js dans Azure Web App sur Linux](./app-service-linux-using-nodejs-pm2.md)
* [FAQ de l’application web Azure App Service sur Linux](app-service-linux-faq.md)

Posez des questions et indiquez vos préoccupations sur [notre forum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).


<!--Image references-->
[1]: ./media/app-service-linux-using-custom-docker-image/new-configure-container.png
[2]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-public.png
[3]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-dockerhub-private.png
[4]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-privateregistry.png
[5]: ./media/app-service-linux-using-custom-docker-image/existingapp-configure-builtin.png
[6]: ./media/app-service-linux-using-custom-docker-image/setting-port.png
[7]: ./media/app-service-linux-using-custom-docker-image/kudu-docker-logs.png
[8]: ./media/app-service-linux-using-custom-docker-image/logging.png

