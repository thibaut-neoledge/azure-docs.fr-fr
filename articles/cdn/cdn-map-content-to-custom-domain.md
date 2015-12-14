<properties 
	 pageTitle="Comment mapper le contenu du réseau de distribution de contenu Azure (CDN) à un domaine personnalisé" 
	 description="Cette rubrique montre comment mapper un contenu CDN à un domaine personnalisé." 
	 services="cdn" 
	 documentationCenter="" 
	 authors="camsoper" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.workload="media" 
	 ms.tgt_pltfrm="na" 
	 ms.devlang="na" 
	 ms.topic="article" 
	 ms.date="12/02/2015" 
	 ms.author="casoper"/>

#Comment mapper un domaine personnalisé à un point de terminaison de réseau de distribution de contenu (CDN)

Vous pouvez mapper un domaine personnalisé à un point de terminaison CDN pour utiliser votre propre nom de domaine dans les URL de contenu mis en cache, plutôt que d'utiliser le point de terminaison CDN fourni. Pour ce faire, créez un enregistrement CNAME auprès de votre bureau d’enregistrement de domaines et mappez votre domaine personnalisé et votre sous-domaine au point de terminaison CDN. Un enregistrement CNAME est une fonctionnalité DNS qui mappe un domaine source à un domaine cible. Dans cet exemple, le domaine source est votre domaine personnalisé et son sous-domaine (le sous-domaine est dans tous les cas indispensable). Le domaine cible est le point de terminaison de votre CDN.

> [AZURE.NOTE]- Vous devez créer un enregistrement CNAME auprès de votre bureau d’enregistrement de domaines pour mapper votre domaine au point de terminaison CDN. Les enregistrements CNAME mappent des sous-domaines spécifiques tels que www.mydomain.com ou myblog.mydomain.com. Il n’est pas possible de mapper un enregistrement CNAME à un domaine racine, par exemple mydomain.com. - Un sous-domaine ne peut être associé qu’à un seul point de terminaison CDN. L'enregistrement CNAME que vous créez achemine tout le trafic adressé au sous-domaine vers le point de terminaison spécifié. Par exemple, si vous associez www.mydomain.com à votre point de terminaison CDN, vous ne pouvez pas l’associer à d’autres points de terminaison Azure, tels qu’un point de terminaison de compte de stockage ou un point de terminaison de service cloud. Toutefois, vous pouvez utiliser différents sous-domaines du même domaine pour différents points de terminaison de service. Vous pouvez également mapper différents sous-domaines au même point de terminaison CDN.

Les procédures décrites dans cette rubrique vous montrent comment effectuer les opérations suivantes :

-	[Inscrire un domaine personnalisé pour un point de terminaison CDN Azure](#subheading1)
-	[Vérifier que le sous-domaine personnalisé référence votre point de terminaison CDN](#subheading3) 

##<a name="subheading1"></a>Inscrire un domaine personnalisé pour un point de terminaison CDN Azure

1.	Connectez-vous au [portail Azure](http://portal.azure.com/).
2.	Cliquez sur **Parcourir**, sur **Profils CDN**, puis sélectionnez le profil CDN avec le point de terminaison que vous souhaitez mapper à un domaine personnalisé.  
3.	Dans le panneau **Profil CDN**, cliquez sur le point de terminaison CDN auquel vous souhaitez associer le sous-domaine.
4.	En haut du panneau du point de terminaison, cliquez sur le bouton **Ajouter un domaine personnalisé**. Dans le panneau **Ajouter un domaine personnalisé** s’affiche le nom du point de terminaison, dérivé de votre point de terminaison CDN, à utiliser pour créer un enregistrement CNAME. L’adresse de nom d’hôte se présente sous la forme **&lt;Nom\_Point\_Terminaison>.azureedge.net**. Vous pouvez copier ce nom d'hôte en vue de l'utiliser pour créer l'enregistrement CNAME.  

5.	Accédez au site web de votre bureau d'enregistrement de domaines et recherchez la section de la création d'enregistrements DNS. Vous pourrez trouver ces informations dans une section telle que **Domain Name**, **DNS** ou **Name Server Management**.
6.	Recherchez la section relative à la gestion des enregistrements CNAME. Pour cela, accédez à une page de paramètres avancés et recherchez les mots CNAME, Alias ou Sous-domaines.
7.	Créez un enregistrement CNAME qui mappe votre sous-domaine choisi (par exemple, **www** ou **cdn**) au nom d’hôte fourni dans le panneau **Ajouter un domaine personnalisé**.
8.	Revenez au panneau **Ajouter un domaine personnalisé**, puis entrez votre domaine personnalisé, dont le sous-domaine, dans la boîte de dialogue. Par exemple, entrez le nom de domaine dans le format www.mydomain.com ou cdn.mydomain.com.   

	Azure vérifiera que l'enregistrement CNAME existe pour le nom de domaine que vous avez entré. Si l'enregistrement CNAME est correct, votre domaine personnalisé est validé et est utilisable avec votre contenu CDN.

	Notez que dans certains cas la propagation de l'enregistrement CNAME aux serveurs de noms sur Internet peut prendre du temps. Si votre domaine n'est pas validé immédiatement et que vous pensez que l'enregistrement CNAME est correct, patientez quelques minutes, puis réessayez.

##<a name="subheading3"></a>Vérifier que le sous-domaine personnalisé référence votre point de terminaison CDN

-	Après avoir terminé l'enregistrement de votre domaine personnalisé, vous pouvez accéder au contenu mis en cache au point de terminaison CDN à l'aide du domaine personnalisé. Tout d'abord, assurez-vous d'avoir un contenu public qui est mis en cache au point de terminaison. Par exemple, si votre point de terminaison CDN est associé à un compte de stockage, le CDN met en cache le contenu dans des conteneurs d'objets blob publics. Pour tester le domaine personnalisé, assurez-vous que votre conteneur est configuré pour autoriser l'accès public et qu'il contient au moins un objet blob.
-	Dans votre navigateur, accédez à l'adresse de l'objet blob à l'aide du domaine personnalisé. Par exemple, si votre domaine personnalisé est **www.mydomain.com**, l'URL à un objet blob mis en cache sera similaire à l'URL suivante :  
	
		http://www.mydomain.com/mypubliccontainer/acachedblob.jpg
-	Si votre point de terminaison CDN est associé à un service cloud, l'adresse de votre contenu mis en cache sera similaire à l'URL suivante :

		http://www.mydomain.com/mycloudservice

##Voir aussi


[Comment activer le réseau de distribution de contenu (CDN) pour Azure](./cdn-create-new-endpoint.md)

 

<!---HONumber=AcomDC_1203_2015-->