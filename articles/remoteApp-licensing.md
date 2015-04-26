<properties title="Azure RemoteApp licensing" pageTitle="Licences Azure RemoteApp" description="Découvrez la gestion des licences dans Azure RemoteApp." metaKeywords="" services="" solutions="" documentationCenter="" authors="elizapo"  />

<tags ms.service="remoteapp" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/12/2014" ms.author="elizapo" />

#Fonctionnement des licences dans Azure RemoteApp


Vous avez donc configuré votre service Azure RemoteApp, créé vos modèles et vous êtes prêt à publier des applications pour vos utilisateurs. Toutefois, vous devez encore résoudre la question des licences. Qu'en est-il des licences pour les applications que vous partagez via RemoteApp ?

Vous pouvez proposer un programme ou une application pour lequel vous possédez une licence. 

Dans le cadre de votre abonnement Azure RemoteApp, vous n'aurez pas à acheter de licences Windows ou de licences d'accès client Bureau à distance, car elles sont comprises dans le prix de l'abonnement.

Si vous souhaitez utiliser l'image de modèle Office 365 incluse dans Azure RemoteApp, vous devez disposer d'une offre Office 365 ProPlus *existante*. Cela s'applique également à n'importe quelle application Office 365 que vous publiez à l'aide d'un modèle personnalisé. Vous devez activer les applications avec votre propre abonnement. Cela s'applique aux abonnements d'évaluation et payants. Si vous souhaitez utiliser l'image de modèle Office 365 pendant la période d'évaluation, *et que vous ne disposez pas d'un abonnement*, accédez à la page Office 365 pour [vous inscrire](https://go.microsoft.com/fwlink/p/?LinkID=403802) à un abonnement d'évaluation. Consultez [Comment RemoteApp et Office fonctionnent ensemble ?](http://azure.microsoft.com/fr-fr/documentation/articles/remoteapp-o365/) pour plus d'informations.

Si pendant la période d'évaluation, vous ne voulez pas bénéficier d'un abonnement d'évaluation à Office 365, utilisez l'image de modèle Office Professionnel Plus 2013 incluse dans RemoteApp. Cette image de modèle peut uniquement être utilisée pendant 30 jours et ne peut pas être convertie en collection payante. À la fin de la période d'évaluation, vous devrez supprimer la collection et en créer une autre pour continuer à utiliser Office 2013.  

Ce qui paraît logique. Vous pouvez publier une application que vous êtes légalement habilité à partager. Pour cela vous devez vous assurer que vous avez l'autorisation de partager vos programmes.

Notez que vous ne pouvez pas utiliser de contrat de licence d'accès client ou de contrat de licence en volume dans une collection cloud. Vous *pouvez* utiliser un contrat de licence en volume pour activer des applications dans votre collection hybride (sauf pour Office). Vous devez simplement les installer dans votre image de modèle à partir du support de la licence en volume. Suivez les instructions du fournisseur de l'application pour installer des licences dans un environnement Bureau à distance.

<!--HONumber=35.2-->
