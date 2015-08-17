<properties 
    pageTitle="Licences Azure RemoteApp" 
    description="Découvrez comment fonctionnent les licences dans Azure RemoteApp." 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/28/2015" 
    ms.author="elizapo" />


# Fonctionnement des licences dans Azure RemoteApp


Vous avez donc configuré votre service Azure RemoteApp, créé vos modèles et vous êtes prêt à publier des applications pour vos utilisateurs. Toutefois, vous devez encore résoudre la question des licences. Et comprendre comment elles fonctionnent pour RemoteApp et les applications que vous partagez par l’intermédiaire de RemoteApp.

RemoteApp ne requiert pas de licences Windows ni de licences d'accès client du Bureau à distance. Votre abonnement s'occupe du côté RemoteApp lui-même. (Consultez les détails des [plans de tarification](../../../pricing/details/remoteapp/).)

Si vous voulez utiliser l'image de modèle Office 365 incluse dans RemoteApp, vous devez disposer d'une offre Office 365 ProPlus *existante*. Cela s'applique également à n'importe quelle application Office 365 que vous publiez à l'aide d'un modèle personnalisé. Vous devez activer les applications avec votre propre abonnement. Cela s'applique aux abonnements d'évaluation et payants. Si vous voulez utiliser l'image de modèle Office 365 pendant la période d'évaluation, *et que vous ne disposez pas déjà d'un abonnement*, accédez à la page Office 365 pour [souscrire](https://go.microsoft.com/fwlink/p/?LinkID=403802) un abonnement d'évaluation. Consultez [Comment RemoteApp et Office fonctionnent ensemble](remoteapp-o365.md) pour plus d'informations.

Si pendant la période d'évaluation, vous ne voulez pas bénéficier d'un abonnement d'évaluation à Office 365, utilisez l'image de modèle Office Professionnel Plus 2013 incluse dans RemoteApp. Cette image de modèle peut uniquement être utilisée pendant 30 jours et ne peut pas être convertie en collection payante.

Pour les autres applications, vous devez vous assurer de disposer de la licence appropriée pour partager l'application.

Ce qui paraît logique. Vous pouvez publier une application que vous êtes légalement habilité à partager. Pour cela vous devez vous assurer que vous avez l'autorisation de partager vos programmes.

Notez que vous ne pouvez pas utiliser de contrat de licence d'accès client ou de contrat de licence en volume dans une collection cloud. Vous *pouvez* utiliser un contrat de licence en volume pour activer des applications dans votre collection hybride (sauf pour Office). Vous devez simplement les installer dans votre image de modèle à partir du support de la licence en volume. Suivez les instructions du fournisseur de l'application pour installer des licences dans un environnement Bureau à distance.
 

<!---HONumber=August15_HO6-->