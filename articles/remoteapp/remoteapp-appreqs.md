
<properties 
    pageTitle="Configuration requise des applications dans Azure RemoteApp"
	description="En savoir plus sur la configuration requise pour les applications que vous souhaitez utiliser dans Azure RemoteApp"
	services="remoteapp"
	documentationCenter=""
	authors="lizap"
	manager="mbaldwin"/>

<tags 
    ms.service="remoteapp"
	ms.workload="compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/12/2015"
	ms.author="elizapo"/>



# Configuration requise des applications
Azure RemoteApp prend en charge la diffusion en continu des applications Windows 32 bits ou 64 bits à partir d’une image de Windows Server 2012 R2. La plupart des applications Windows 32 bits ou 64 bits existantes s’exécutent « tel quel » dans l’environnement Azure RemoteApp (Services Bureau à distance ou anciennement Services Terminal Server). Toutefois, il existe une différence entre s’exécuter et fonctionner correctement : certaines applications fonctionnent bien et correctement, d’autres non. Les informations suivantes fournissent des conseils pour le développement d’applications dans un environnement de Services Bureau à distance et le test, afin de garantir la compatibilité.

Info : nous travaillons sur la création de quelques exemples d’applications fonctionnelles pour vous. Vous verrez de nouvelles rubriques décrivant l’utilisation de Microsoft Access, QuickBooks et App-V dans RemoteApp.

## Configuration requise
Si ces trois conditions sont réunies, votre application s’exécutera correctement dans RemoteApp :

1.	Les applications qui répondent à toutes les [Conditions de certification des applications de bureau Windows](https://msdn.microsoft.com/library/windows/desktop/hh749939.aspx) et respectent les [Instructions de programmation des Services Bureau à distance](https://msdn.microsoft.com/library/aa383490.aspx) sont entièrement compatibles avec RemoteApp. 
2.	Les applications ne doivent jamais stocker des données localement sur l’image ou sur les instances RemoteApp pouvant être perdues. Après la création d’une collection RemoteApp, les instances sont clonées et sans état et ne doivent contenir que des applications. Stockez les données dans une source externe ou dans le profil utilisateur. 
3.	Les images personnalisées ne doivent jamais contenir de données pouvant être perdues.  

## Test de vos applications
Suivez ces étapes pour tester des applications :

1.	Installez Windows Server 2012 R2 et votre application
2.	Activez le Bureau à distance
3.	Créez deux comptes d’utilisateur, UserA et UserB, puis ajoutez ces deux comptes d’utilisateur au groupe de sécurité du bureau à distance. 
4.	Vérifiez la compatibilité aux sessions multiples en établissant deux sessions RDS simultanées à l’ordinateur lors du lancement de l’application.
5.	Validation du comportement de l’application

## Conseils pour le développement d’applications
Utilisez les instructions suivantes pour développer des applications pour RemoteApp.

### Plusieurs utilisateurs
 
- L’installation d’une [application pour un seul utilisateur ](https://msdn.microsoft.com/library/aa380661.aspx)peut créer des problèmes dans un environnement multi-utilisateur. 
- Les applications doivent stocker [les informations spécifiques à l’utilisateur](https://msdn.microsoft.com/library/aa383452.aspx) dans des emplacements spécifiques à l’utilisateur, séparément des informations globales qui s’appliquent à tous les utilisateurs. 
- RemoteApp utilise plusieurs [espaces de noms pour les objets de noyau](https://msdn.microsoft.com/library/aa382954.aspx) ; un espace de noms global est utilisé principalement par les services dans les applications client/serveur. 
- Il est déconseillé de supposer que le nom d’ordinateur ou l’[adresse IP](https://msdn.microsoft.com/library/aa382942.aspx) affectée à l’ordinateur sont associés à un seul utilisateur, étant donné que plusieurs utilisateurs peuvent être connectés simultanément à un serveur hôte de Session Bureau à distance. 

### Performances
- Désactivez les [effets de graphismes](https://msdn.microsoft.com/library/aa380822.aspx) avant d’ajouter votre application à RemoteApp.
- Pour optimiser la disponibilité du processeur pour tous les utilisateurs, désactivez les [tâches en arrière-plan](https://msdn.microsoft.com/library/aa380665.aspx) ou créez des tâches en arrière-plan efficaces, qui ne nécessitent pas de nombreuses ressources. 
- Paramétrez et équilibrez l’[utilisation de threads](https://msdn.microsoft.com/library/aa383520.aspx) de l’application pour un environnement multi-utilisateur et multiprocesseur.
- Pour optimiser les performances, il est recommandé que les applications [détectent](https://msdn.microsoft.com/library/aa380798.aspx) si elles s’exécutent dans une session client. 
 

<!---HONumber=August15_HO9-->