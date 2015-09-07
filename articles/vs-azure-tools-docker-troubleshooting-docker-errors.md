<properties
   pageTitle="Dépannage d’erreurs client Docker sur Windows avec Visual Studio | Microsoft Azure"
	description="Résolvez les problèmes d’utilisation de Visual Studio pour créer et déployer des applications web dans Docker sur Windows avec Visual Studio."
	services="visual-studio-online"
	documentationCenter="na"
	authors="kempb"
	manager="douge"
	editor="tglee"/>
<tags
   ms.service="multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="multiple"
	ms.date="08/20/2015"
	ms.author="kempb"/>

# Résolution des erreurs Docker

Après avoir configuré tous les paramètres pour le conteneur Docker de votre application, vous devez vous assurer que les paramètres et les chemins d’accès sont corrects. Pour simplifier cette tâche, Visual Studio propose un bouton Valider dans la boîte de dialogue Publier.

Cette rubrique est destinée à vous aider à diagnostiquer et à corriger ou contourner les principaux problèmes rencontrés lors de l’hébergement d’une application Visual Studio dans Docker. Cette rubrique sera complétée à mesure que d’autres problèmes seront rencontrés.

## Message d’échec reçu quand vous essayez de valider la connexion à votre hôte Docker dans la boîte de dialogue Publier le site web

Il existe plusieurs solutions à ce problème.

- Dans l’onglet **Connexion** de la boîte de dialogue **Publier**, assurez-vous que l’**URL du serveur** est correcte et que le numéro `:<port_number>` indiqué dans l’**URL du serveur** correspond au port écouté par le démon Docker.

- Dans l’onglet **Connexion** de la boîte de dialogue **Publier**, développez la section **Options avancées de Docker**, puis vérifiez que les options d’**authentification** spécifiées sont correctes.
  - Si le démon Docker sur le serveur est configuré pour utiliser la sécurité TLS, l’interface de ligne de commande Windows Docker (docker.exe) recherche la clé (key.pem) et le certificat (cert.pem) du client par défaut dans le dossier `<%userprofile%>\.docker`. Si ces éléments ne sont pas présents, ils doivent être générés à l’aide d’OpenSSL. Pour plus d’informations sur la configuration de Docker pour TLS, consultez [Protection du socket démon de Docker avec HTTPS](https://docs.docker.com/articles/https/).

	Pour vous assurer que Docker s’authentifie correctement à partir du client Windows auprès du serveur Linux, vous pouvez copier le contenu de la zone de texte Aperçu dans une nouvelle fenêtre Commande, puis remplacer `<command>` par « info » comme suit :

    ```
    // This example assumes the Docker daemon is configured to use the default port
    // of 2376 to listen for connections.docker.

    --tls -H tcp://contoso.cloudapp.net:2376 info
    ```

    Au lieu de copier les fichiers de clé et de certificat du client dans le dossier .docker, vous pouvez modifier les options d’**authentification** en ajoutant les paramètres suivants :

    ```
    --tls --tlscert=C:\mycert\cert.pem --tlskey=C:\mycert\key.pem
    ```
- Assurez-vous que le démon Docker sur la machine hôte Docker est la version 1.6 ou une version ultérieure.

## Erreur de délai d’attente lors de l’utilisation de vos propres certificats sans certificat client dans le dossier Docker

Si vous voulez utiliser vos propres certificats lors de la création de l’hôte Docker dans Visual Studio (autrement dit, vous décochez la case **Générer automatiquement les certificats Docker** dans la boîte de dialogue **Créer une machine virtuelle sur Microsoft Azure**), vous devez copier les fichiers de certificat et de clé du client (cert.pem et key.pem) dans le dossier Docker (`<%userprofile%>\.docker`). Sinon, quand vous publiez votre projet, vous obtenez une erreur de délai d’attente dans l’heure et l’opération de publication échoue.

## Utilisation de PowerShell 3.0 nécessaire pour publier dans des conteneurs Docker

Si vous utilisez Windows 7 ou Windows Server 2008 comme système d’exploitation, vous devez installer PowerShell 3.0 pour pouvoir publier dans des conteneurs Docker. PowerShell 3.0 est fourni avec [Windows Management Framework 3.0](https://www.microsoft.com/fr-FR/download/details.aspx?id=34595). Après l’installation, vous devez redémarrer votre système.

Pour contourner le problème, vous pouvez effectuer une mise à niveau vers Windows 8.1 ou Windows 10, qui intègre déjà PowerShell 3.0.

## Fenêtre PowerShell qui ne se ferme pas automatiquement

Après la création d’une machine virtuelle, il arrive que la fenêtre PowerShell ne se ferme pas automatiquement. La fermeture de cette fenêtre ferme également Visual Studio. La fenêtre n’a aucune incidence sur les fonctionnalités des outils Visual Studio ou Docker. Vous pouvez donc la laisser ouverte jusqu’à ce que vous ayez terminé votre travail.

## Forum Aux Questions

Q : Comment créer une machine Linux prenant en charge Docker dans Azure à l’aide des outils Visual Studio ?

R : Pour plus d’informations sur la procédure à suivre, consultez [Hébergement d’applications web dans Docker](vs-azure-tools-docker-hosting-web-apps-in-docker.md).

Q : Quels modèles de projet Visual Studio sont pris en charge pour la publication dans un conteneur Docker Linux ?

R : Visual Studio prend actuellement en charge les modèles web Application Console C# (Package) et ASP.NET 5 C# en version préliminaire, y compris les suivants :

- Vide

- API Web

- Application web

Q : Comment publier mon projet de console ou web ASP.NET 5 dans Docker en utilisant MSBUILD à partir de la ligne de commande ?

R : Utilisez la commande MSBuild suivante :

    `msbuild <projectname.xproj> /p:deployOnBuild=true;publishProfile=<profilename>`

Q : Comment publier mon projet de console ou web ASP.NET 5 sur Docker en utilisant PowerShell à partir de la ligne de commande ?

R : Utilisez la commande PowerShell suivante :

```
.\contoso-Docker-publish.ps1 -packOutput $env:USERPROFILE\AppData\Local\Temp\PublishTemp -pubxmlFile .\contoso-Docker.pubxml
```

Q : J’ai mon propre serveur Linux où Docker est installé. Comment spécifier cela dans la boîte de dialogue **Publier sur le web** ?

R : Consultez la section **Spécifier un hôte Docker personnalisé** dans la rubrique [Hébergement d’applications web dans Docker](vs-azure-tools-docker-hosting-web-apps-in-docker.md).

Q : J’utilise mon propre serveur Linux où Docker est installé. Comment générer des clés et des certificats pour configurer l’authentification à l’aide de TLS ?

R : Une solution consiste à utiliser OpenSSL sur le serveur pour générer les certificats et clés nécessaires pour l’autorité de certification, le serveur et le client. Vous pouvez ensuite utiliser un logiciel tiers pour établir une connexion SSH/SFTP, puis copier les certificats sur la machine de développement Windows locale. Par défaut, Docker (CLI) essaie d’utiliser les certificats situés dans le dossier `<userprofile>\.docker`.

Une autre solution est de télécharger OpenSSL pour Windows, de générer les certificats et clés nécessaires, puis de charger l’autorité de certification, les certificats de serveur et les clés sur la machine Linux. Pour plus d’informations sur l’établissement d’une connexion sécurisée à Docker, consultez [Protection du socket démon de Docker avec HTTPS](https://docs.docker.com/articles/https/).

<!---HONumber=August15_HO9-->