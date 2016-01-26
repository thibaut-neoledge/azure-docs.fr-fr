<properties
   pageTitle="Intégration de OneDrive Entreprise et d’Azure RemoteApp | Microsoft Azure"
   description="Découvrez comment utiliser OneDrive Entreprise avec Azure RemoteApp."
   services="remoteapp"
   documentationCenter=""
   authors="pavithir"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="01/13/2016"
   ms.author="elizapo"/>

# Intégration de OneDrive Entreprise et d’Azure RemoteApp

Vous pouvez utiliser OneDrive Entreprise comme référentiel de fichiers avec Azure RemoteApp. OneDrive Entreprise constitue un excellent moyen de conserver les fichiers synchronisés sur l’ensemble de vos appareils et espaces de travail. Le [disque de profil utilisateur](remoteapp-upd.md) (UPD) d’un utilisateur est l’emplacement dans lequel il peut stocker ses fichiers pour les applications Azure RemoteApp, lesquels ne sont cependant accessibles qu’à l’aide d’Azure RemoteApp. Parallèlement, OneDrive Entreprise permet à l’utilisateur d’accéder aux fichiers où qu’il soit et quand il le souhaite sans être tenu de passer via Azure RemoteApp. Cet article présente les versions de OneDrive Entreprise prises en charge ainsi que les différentes méthodes qui permettent aux administrateurs de configurer OneDrive Entreprise pour Azure RemoteApp.

## Toutes les versions de OneDrive sont-elles prises en charge ?

Il existe deux versions de OneDrive : OneDrive et OneDrive Entreprise. Seul OneDrive Entreprise est pris en charge sur Azure RemoteApp. L’application OneDrive personnelle fonctionne, mais officiellement elle n’est pas prise en charge. En outre, seule la dernière version de OneDrive Entreprise, alias le client de synchronisation nouvelle génération, est prise en charge sur Azure RemoteApp (et sur les serveurs RDSH/Citrix/Terminal Server).

>[AZURE.NOTE]OneDrive (édition personnelle/pour consommateurs) n’est pas pris en charge sur Azure RemoteApp. Toutes les versions de OneDrive Entreprise ne sont pas prises en charge, car elles n’ont pas été certifiées pour fonctionner sur Windows Server. Même si le nouveau client (client de synchronisation nouvelle génération) et les anciennes versions de Groove semblent fonctionner correctement sur Azure RemoteApp, comme décrit dans [https://support.microsoft.com/fr-FR/kb/2965687](https://support.microsoft.com/kb/2965687), les anciens moteurs de synchronisation ne disposent pas des fonctionnalités complètes sur les serveurs Citrix/Terminal Server (Windows Server). Utilisez le nouveau client de synchronisation sur Azure RemoteApp (et d’autres déploiements de Windows Server).

## Quelles sont les différentes options de configuration de OneDrive Entreprise ?

- **Configuration traditionnelle du moteur de synchronisation de OneDrive Entreprise **: le client de synchronisation OneDrive Entreprise peut être installé sur une référence de serveur (Bureau à distance ainsi que session RemoteApp/Terminal Server) et dans des dossiers sélectionnés pour la synchronisation dans la session RemoteApp, comme vous le feriez sur une référence cliente Windows. L’emplacement par défaut où OneDrive Entreprise synchronise les fichiers est identique à celui dans lequel le disque de profil utilisateur utilisé pour stocker les données et paramètres utilisateur sur Azure RemoteApp réside : C:\users< nom\_utilisateur>. Ce disque suit l’utilisateur jusqu’à n’importe quelle machine virtuelle à laquelle il se connecte. Les fichiers ODB suivent donc également l’utilisateur. L’application OneDrive Entreprise doit être publiée par l’administrateur pour tous les utilisateurs, et les utilisateurs doivent la lancer à chaque nouvelle session (le lancement peut également être automatisé à l’aide d’un script de connexion) pour s’assurer de l’activation du moteur de synchronisation. OneDrive Entreprise télécharge l’intégralité du fichier sur la machine virtuelle sur laquelle la session s’exécute. Lors de la synchronisation du contenu d’un utilisateur, la charge de travail correspondant à la synchronisation de tous les éléments (processeur/données transférées/stockage) est lourde. Elle n’est pas simplement optimisée pour les machines virtuelles auxquelles de très nombreux utilisateurs se connectent brièvement. Avec la synchronisation sélective, la charge de travail est réduite, mais le problème persiste.
- **« Virtualiser » l’application OneDrive Entreprise/La rediriger du client fat local vers la session **: si vous synchronisez OneDrive avec un dossier sur l’appareil client, sur un lecteur, vous pouvez choisir de [rediriger](remoteapp-redirection.md) ce lecteur vers Azure RemoteApp : ce lecteur doit être identique sur tous les clients de vos utilisateurs, et OneDrive doit être synchronisé avec un dossier situé sur ce lecteur. S’ils accèdent à RemoteApp à partir d’un autre client, ces fichiers peuvent ne pas être disponibles (solution de contournement : leurs fichiers sont toujours accessibles à l’aide de la version en ligne de OneDrive). 
- **Présenter OneDrive Entreprise en tant que lecteur dans l’environnement Azure RemoteApp sans mise en cache/synchronisation des fichiers **: (mapper l’URL HTTP de OneDrive Entreprise à un lecteur de la machine virtuelle) le mappage de OneDrive Entreprise au lecteur réseau dans l’environnement RDSH est pris en charge. Cela peut être utilisé dans les scénarios suivants : 
	- Lorsque des clients légers (sans stockage local) sont utilisés pour accéder à Azure RemoteApp : l’application requiert des fichiers stockés sur OneDrive Entreprise, mais souhaite que leur apparence soit locale et l’administrateur ne veut pas synchroniser les fichiers avec une machine virtuelle.
	- Lorsque de nombreux fichiers volumineux existent sur OneDrive Entreprise et ont été sélectionnés pour la synchronisation. Étant donné la charge de travail de la synchronisation, tous les fichiers risquent de ne pas être synchronisés lorsque l’utilisateur souhaitera les utiliser. De même, si la taille totale des fichiers excède 50 Go, ils ne peuvent pas être stockés dans le disque de profil utilisateur.

Dans les scénarios ci-dessus, les administrateurs peuvent choisir de mapper un lecteur de la machine virtuelle à l’URL HTTP de OneDrive Entreprise de l’utilisateur. Options :

- Disposer de documents binaires Office dans l’image et ne pas activer le moteur de synchronisation de OneDrive Entreprise.
- Disposer de documents binaires autres qu’Office dans l’image. Ce cas est soumis à une condition préalable, le pack Expérience utilisateur. Plus précisément, le service WebClient (également appelé WebDAV) doit être installé dans le cadre du pack Expérience utilisateur. 

### Installer le pack Expérience utilisateur sur Windows Server 2012 R2
Pour installer le pack Expérience utilisateur :

1. Dans le Gestionnaire de serveur, cliquez sur **Gérer -> Ajouter des rôles et fonctionnalités**.
2. Cliquez sur **Fonctionnalités**, puis sur **Interfaces utilisateur et infrastructure -> Expérience utilisateur**.

### Mapper un lecteur à l’URL de OneDrive Entreprise

Suivez les instructions indiquées dans l’article du support technique : 
[https://support.microsoft.com/kb/2616712](https://support.microsoft.com/kb/2616712)
 
Lors de la configuration, il est important de s’assurer que la case **Maintenir la connexion** est cochée.

De manière générale, voici les instructions :

1.	Recherchez l’URL correspondant au lecteur. L’URL utilisée pour le mappage du lecteur est celle que vous obtenez lorsque vous accédez à votre répertoire de base dans OneDrive Entreprise en ligne. Par exemple :
 
	https://microsoft-my.sharepoint.com/personal/alias_microsoft_com/_layouts/15/onedrive.aspx?AjaxDelta=1&isStartPlt1=something
2.	Ouvrez l’URL à l’aide d’un navigateur dans la session de RemoteApp, puis sélectionnez **maintenir la connexion** avant de vous connecter à l’URL de votre compte.
3.	Ouvrez l’Explorateur Windows et mappez un lecteur à l’URL ci-dessus. Si l’URL n’est pas résolue, la forme abrégée peut être utilisée :
	
	https://microsoft-my.sharepoint.com/personal/alias_microsoft_com. 

	Le lecteur mappé, qui ressemble à ce qui suit, est immédiatement créé :
 
	![OneDrive Entreprise en tant que lecteur réseau mappé](./media/remoteapp-onedrive/ra-mappeddrive.png)

<!---HONumber=AcomDC_0121_2016-->