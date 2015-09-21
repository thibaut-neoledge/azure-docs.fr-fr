<properties 
	pageTitle="Utilisation des paramètres de service web Azure Machine Learning | Microsoft Azure" 
	description="Comment utiliser les paramètres de service Web Azure Machine Learning pour modifier le comportement de votre modèle au moment d’accéder au service Web." 
	services="machine-learning" 
	documentationCenter="" 
	authors="raymondlaghaeian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/04/2015" 
	ms.author="raymondl;garye"/>

#Utilisation des paramètres de service Web Azure Machine Learning

Un service Web Azure Machine Learning est créé en publiant une expérience qui contient des modules avec des paramètres configurables. Il se peut que, dans certains cas, vous souhaitiez modifier le comportement du module lorsque le service Web est en cours d'exécution. Vous pouvez le faire grâce aux *paramètres de service Web*.

Un exemple courant consiste à configurer le module [Reader][reader], afin que l’utilisateur du service Web publié puisse spécifier une autre source de données lors de l’accès au service Web. Il est également possible de configurer le module [Writer][writer] de façon à spécifier une destination différente. Vous pouvez aussi, par exemple, modifier le nombre de bits pour le module [Feature Hashing][feature-hashing] ou le nombre de fonctionnalités souhaitées pour le module de [Sélection de fonctionnalités basée sur le filtre][filter-based-feature-selection].

Vous pouvez définir des paramètres de service web et les associer à un ou plusieurs paramètres de module dans votre expérience, en spécifiant s’ils sont obligatoires ou facultatifs. L’utilisateur du service web peut ensuite fournir des valeurs pour ces paramètres quand il appelle le service web.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##Comment configurer et utiliser les paramètres de service Web

Pour définir un paramètre de service Web, cliquez sur l'icône située à côté du paramètre pour module et sélectionnez « Définir en tant que paramètre du service Web ». Cette opération crée un nouveau paramètre de service Web et le connecte à ce paramètre de module. L'utilisateur peut ensuite, lorsqu’il accède au service Web, spécifier une valeur pour le paramètre de service Web. Celle-ci sera appliquée au paramètre de module.

Une fois le paramètre de service Web défini, il est disponible pour n'importe quel autre paramètre de module faisant partie de l'expérience. Si vous définissez un paramètre de service Web associé à un paramètre défini pour un module, vous pouvez utiliser ce même paramètre de service Web pour n’importe quel autre module, pourvu que la valeur attribuée au paramètre soit de même type. Par exemple, si le paramètre de service Web est une valeur numérique, il peut uniquement être utilisé pour les paramètres de module qui gèrent des valeurs numériques. Lorsque l'utilisateur définit une valeur pour le paramètre de service Web, elle est appliquée à tous les paramètres de modules associés.

Vous pouvez décider de définir ou non une valeur par défaut pour le paramètre de service Web. Si vous en définissez une, le paramètre est facultatif pour l'utilisateur du service Web. Si vous ne le faites pas, l'utilisateur doit entrer une valeur au moment d’accéder au service Web.

La documentation pour le service Web (fourni par le biais du lien de la **page d'aide de l'API** dans le **TABLEAU DE BORD** du service Web dans Machine Learning Studio) fournira à l'utilisateur de service Web des informations sur la façon de définir, en les programmant, les paramètres de service Web au moment d'accéder au service Web.


##Exemple

Par exemple, supposons que nous avons une expérience avec un module [Writer][writer] qui envoie des informations vers le stockage blob Azure. Nous allons définir un paramètre de service Web nommé « Chemin d'accès d'objet blob » qui permet à l'utilisateur de service Web de modifier le chemin d'accès pour le stockage d'objets blob lors de l’accès au service.

1.	Dans Machine Learning Studio, cliquez sur le module [Writer][writer] pour le sélectionner. Ses propriétés sont affichées dans le volet Propriétés à droite du canevas de l'expérience.

2.	Spécification du type de stockage :

    - Sous le message **Veuillez spécifier la destination des données**, sélectionnez « Stockage d'objets blob Azure ».
    - Sous le message **Veuillez spécifier le type d'authentification**, sélectionnez « Compte ».
    - Entrez les informations de compte correspondant au stockage d’objets blob Azure. 
    <p />

3.	Cliquez sur l'icône à droite du **Chemin d'accès d’objet blob commençant par le paramètre du conteneur**. Voici à quoi cela ressemble :

	![Icône de paramètre de service Web][icon]

    Sélectionnez « Définir en tant que paramètre du service Web ».

    Une entrée est ajoutée sous les **paramètres de service Web** en bas du volet Propriétés portant le nom de « Chemin d'accès d’objet blob commençant par le conteneur ». C’est le paramètre de service Web qui est désormais associé à ce paramètre de module [Writer][writer].

4.	Pour renommer le paramètre de service Web, cliquez sur son nom, entrez « Blob path », puis appuyez sur la touche **Entrée**.
 
5.	Pour attribuer au paramètre de service Web une valeur par défaut, cliquez sur l'icône à droite du nom, sélectionnez « Fournir la valeur par défaut », entrez une valeur (par exemple, « container1/output1.csv »), puis appuyez sur la touche **Entrée**.

	![Paramètre de service Web][parameter]

6.	Cliquez sur **Exécuter**.

7.	Cliquez sur **PUBLIER LE SERVICE WEB** pour publier le service Web.

L'utilisateur de service Web peut désormais indiquer une nouvelle destination pour le module [Writer][writer] au moment d’accéder au service Web.

##Plus d'informations

Vous trouverez un exemple plus détaillé en consultant l’entrée [Paramètres de service Web](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx) du blog [Machine Learning ](http://blogs.technet.com/b/machinelearning/archive/2014/11/25/azureml-web-service-parameters.aspx).

Pour plus d'informations sur l'accès à un service Web Machine Learning, consultez la section [Comment consommer un service Web Machine Learning publié](machine-learning-consume-web-services.md).



<!-- Images -->
[icon]: ./media/machine-learning-web-service-parameters/icon.png
[parameter]: ./media/machine-learning-web-service-parameters/parameter.png


<!-- Module References -->
[feature-hashing]: https://msdn.microsoft.com/library/azure/c9a82660-2d9c-411d-8122-4d9e0b3ce92a/
[filter-based-feature-selection]: https://msdn.microsoft.com/library/azure/918b356b-045c-412b-aa12-94a1d2dad90f/
[reader]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[writer]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
 

<!---HONumber=Sept15_HO2-->