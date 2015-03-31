<properties 
	pageTitle="Utilisation de l'émulateur de stockage Azure pour le développement et le test" 
	description="Découvrez comment utiliser l'émulateur de stockage Azure pour le développement et le test." 
	services="storage" 
	documentationCenter="" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>
<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="tamram"/>

# Utilisation de l'émulateur de stockage Azure pour le développement et le test

## Vue d'ensemble
L'émulateur de stockage Microsoft Azure fournit un environnement local qui émule les services BLOB, de File d'attente et de Table Azure à des fins de développement. L'émulateur de stockage vous permet de tester localement votre application sur les services de stockage, et ce sans frais.

> [AZURE.NOTE] L'émulateur de stockage fait partie du Kit de développement logiciel (SDK) Microsoft Azure. Vous pouvez également installer l'émulateur de stockage en tant que package autonome.
Pour configurer l'émulateur de stockage, vous devez disposer de privilèges d'administrateur sur l'ordinateur. 
> Notez qu'il n'est pas garanti que vous puissiez accéder aux données créées dans une version de l'émulateur de stockage à partir d'une autre version. Si vous devez rendre vos données persistantes à long terme, nous vous recommandons de stocker ces données dans un compte de stockage Azure plutôt que dans l'émulateur de stockage.
 
Il existe quelques différences entre l'émulateur de stockage et les services de stockage Azure. Pour plus d'informations sur ces différences, consultez [Différences entre l'émulateur de stockage et les services de stockage Azure](https://msdn.microsoft.com/library/azure/gg433135.aspx).

L'émulateur de stockage utilise une instance de Microsoft(r) SQL Server(tm) et le système de fichiers local pour émuler les services de stockage Azure. Par défaut, l'émulateur de stockage est configuré pour une base de données dans Microsoft(r) SQL Server(tm) 2012 Express LocalDB.  Vous pouvez installer SQL Server Management Studio Express pour gérer votre installation de LocalDB. L'émulateur de stockage se connecte à SQL Server ou LocalDB par l'intermédiaire de l'authentification Windows. Vous pouvez configurer l'émulateur de stockage de sorte qu'il accède à une instance locale de SQL Server plutôt qu'à LocalDB à l'aide de la Référence de l'outil en ligne de commande de l'émulateur de stockage.

## Authentification des demandes

L'émulateur de stockage prend en charge uniquement un compte fixe et une clé d'authentification connue. Ce compte et cette clé sont les seules informations d'identification autorisées pour une utilisation avec l'émulateur de stockage. Il s'agit de :

    Account name: devstoreaccount1
    Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtrKBHBeksoGMGw==
    
> [AZURE.NOTE] La clé d'authentification prise en charge par l'émulateur de stockage est destinée uniquement au test de la fonctionnalité de votre code d'authentification du client. Elle n'offre aucune fonction de sécurité. Vous ne pouvez pas utiliser votre compte et votre clé de stockage de production avec l'émulateur de stockage. Notez également que vous ne devez pas utiliser le compte de développement avec des données de production.
 

## Démarrer et initialiser l'émulateur de stockage
Pour démarrer l'émulateur de stockage Azure, sélectionnez le bouton Démarrer ou appuyez sur la touche Windows. Commencez à taper Émulateur de stockage Azure, puis sélectionnez Émulateur de stockage Windows Azure dans la liste des applications. 

Si l'émulateur de calcul Azure est déjà en cours d'exécution, vous pouvez également démarrer l'émulateur de stockage en cliquant avec le bouton droit sur l'icône de la zone de notification, puis en sélectionnant Démarrer l'émulateur de stockage. Pour plus d'informations sur l'exécution de l'émulateur de calcul, consultez [Exécution d'une application Azure dans l'émulateur de calcul](https://msdn.microsoft.com/library/azure/hh403990.aspx).

Au démarrage de l'émulateur de stockage, une ligne de commande apparaît. Vous pouvez utiliser cette ligne de commande pour démarrer et arrêter l'émulateur de stockage et pour effacer des données, obtenir l'état en cours et initialiser l'émulateur. Pour plus d'informations, consultez la [Référence de l'outil en ligne de commande de l'émulateur de stockage.](https://msdn.microsoft.com/library/azure/gg433005.aspx).

Lorsque la fenêtre de ligne de commande est fermée, l'émulateur de stockage continue de s'exécuter. Pour faire réapparaître la ligne de commande, suivez les étapes ci-dessus comme si vous démarriez l'émulateur de stockage.


La première fois que vous exécutez l'émulateur de stockage, l'environnement de stockage local est initialisé pour vous. Vous pouvez utiliser l'outil en ligne de commande de l'émulateur de stockage pour pointer vers une instance de base de données différente ou pour réinitialiser la base de données existante. Le processus d'initialisation crée une base de données dans LocalDB et réserve des ports HTTP pour chaque service de stockage local. Cette étape requiert des privilèges administratifs. Pour plus d'informations, consultez la [Référence de l'outil en ligne de commande de l'émulateur de stockage](https://msdn.microsoft.com/library/azure/gg433005.aspx).

## À propos des URI du service de stockage

La manière dont vous adressez une ressource dans les services de stockage Azure varie selon que la ressource réside dans Azure ou dans les services de l'émulateur de stockage. Un modèle d'URI est utilisé pour adresser une ressource de stockage dans Azure et un autre modèle d'URI est utilisé pour adresser une ressource de stockage dans l'émulateur de stockage. La différence est due au fait que l'ordinateur local n'effectue pas la résolution des noms de domaine. Les deux modèles d'URI incluent toujours le nom de compte et l'adresse de la ressource demandée.

## Adressage des ressources Azure Storage dans le cloud

Dans le modèle d'URI utilisé pour adresser les ressources de stockage dans Azure, le nom de compte fait partie du nom d'hôte de l'URI et la ressource étant adressée fait partie du chemin d'accès de l'URI. Le modèle d'adressage de base suivant est utilisé pour accéder aux ressources de stockage :

    <http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>


The `<account-name>` est le nom de votre compte de stockage. `<service-name>` est le nom du service faisant l'objet d'un accès et `<resource-path>` est le chemin d'accès à la ressource demandée. La liste suivante indique le modèle d'URI pour chaque service de stockage :

	Blob Service: <http|https>://<account-name>.blob.core.windows.net/<resource-path>
	Queue Service: <http|https>://<account-name>.queue.core.windows.net/<resource-path>
	Table Service: <http|https>://<account-name>.table.core.windows.net/<resource-path>

Par exemple, l'adresse suivante peut être utilisée pour accéder à un objet blob dans le cloud :
    
    http://myaccount.blob.core.windows.net/mycontainer/myblob.txt

> [AZURE.NOTE] Vous pouvez également associer un nom de domaine personnalisé à un point de terminaison de stockage d'objets blob dans le cloud et utiliser ce nom de domaine personnalisé pour adresser des objets blob et des conteneurs. Consultez la rubrique 
 
## Adressage des ressources Azure Storage locales dans l'émulateur de stockage

Dans l'émulateur de stockage, étant donné que l'ordinateur local n'effectue pas la résolution des noms de domaine, le nom du compte fait partie du chemin d'accès de l'URI. Le modèle d'URI pour une ressource exécutée dans l'émulateur de stockage a le format suivant :

    http://<local-machine-address>:<port>/<account-name>/<resource-path>

Le format suivant est utilisé pour adresser les ressources exécutées dans l'émulateur de stockage :

	Blob Service: http://127.0.0.1:10000/<account-name>/<resource-path>
	Queue Service: http://127.0.0.1:10001/<account-name>/<resource-path>
	Table Service: http://127.0.0.1:10002/<account-name>/<resource-path>

Par exemple, l'adresse suivante peut être utilisée pour accéder à un objet blob dans l'émulateur de stockage :

    http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt

> [AZURE.NOTE] HTTPS n'est pas un protocole autorisé pour l'adressage de ressources de stockage local.
 
## Adressage du compte secondaire avec RA-GRS
À partir de la version 3.1, le compte d'émulateur de stockage prend en charge la réplication géo-redondante avec accès en lecture. Pour les ressources de stockage dans le cloud et dans l'émulateur local, vous pouvez accéder à l'emplacement secondaire en ajoutant -secondary au nom du compte. Par exemple, vous pouvez utiliser l'adresse suivante pour accéder à un objet blob en utilisant l'emplacement secondaire en lecture seule dans l'émulateur de stockage :

    http://127.0.0.1:10000/myaccount-secondary/mycontainer/myblob.txt 

> [AZURE.NOTE] Pour un accès par programmation au stockage secondaire avec l'émulateur de stockage, utilisez la bibliothèque cliente de stockage pour .NET version 3.2 ou ultérieure. Pour plus d'informations, consultez la Référence de la bibliothèque cliente de stockage.
 
## Initialiser l'émulateur de stockage à l'aide de l'outil de ligne de commande
Vous pouvez utiliser l'outil en ligne de commande de l'émulateur de stockage pour initialiser l'émulateur de stockage afin qu'il pointe vers une autre instance de base de données que celle par défaut. Si vous souhaitez utiliser l'instance de base de données LocalDB par défaut, il n'est pas nécessaire d'exécuter une étape d'initialisation distincte.

Cet outil est installé par défaut dans le répertoire C:\Program Files(x86)\Microsoft SDKs\Windows Azure\Storage Emulator\. L'initialisation s'exécute automatiquement la première fois que vous démarrez l'émulateur.

> [AZURE.NOTE] Vous devez disposer de privilèges d'administrateur pour exécuter la commande " init " décrite ci-dessous.

1. Cliquez sur le bouton **Démarrer** ou appuyez sur la touche **Windows**. Commencez à taper `émulateur de stockage Azure` et sélectionnez-le lorsqu'il apparaît. Une fenêtre d'invite de commandes s'affiche.


2. Dans la fenêtre d'invites de commandes, tapez la commande suivante, où `<SQLServerInstance> est le nom de l'instance de SQL Server. Pour utiliser LocalDb, spécifiez `(localdb)\v11.0` comme instance de SQL Server.

    WAStorageEmulator init /sqlInstance <SQLServerInstance> 
    
Vous pouvez également exécuter la commande suivante, qui indique à l'émulateur d'utiliser l'instance SQL Server par défaut :

    WAStorageEmulator init /server .\\ 

En guise d'alternative, vous pouvez exécuter la commande suivante, qui réinitialise la base de données :

    WAStorageEmulator init /forceCreate 

## Référence de l'outil en ligne de commande de l'émulateur de stockage

À compter de la version 3.0, lorsque vous lancez l'émulateur de stockage, une fenêtre d'invite de commandes s'affiche. Utilisez-la pour démarrer ou arrêter l'émulateur, ainsi que pour interroger l'état et effectuer d'autres opérations.

> [AZURE.NOTE] Si l'émulateur de calcul est installé, une icône de zone de notification apparaît lorsque vous lancez l'émulateur de stockage. Cliquez avec le bouton droit sur l'icône pour faire apparaître un menu, qui fournit un moyen graphique pour démarrer et arrêter l'émulateur de stockage.

### Syntaxe de la ligne de commande

	WAStorageEmulator [/start] [/stop] [/status] [/clear] [/init] [/help]

### Options
Pour afficher la liste des options, tapez " /help " à l'invite de commandes.

## Étapes suivantes
- [Référence de l'outil en ligne de commande de l'émulateur de stockage](https://msdn.microsoft.com/library/azure/gg433005.aspx)
- et [Différences entre l'émulateur de stockage et Azure Storage Services](https://msdn.microsoft.com/library/azure/gg433135.aspx)
- [Notes de publication de l'émulateur de stockage](https://msdn.microsoft.com/library/azure/dn683879.aspx)

<!--HONumber=47-->
