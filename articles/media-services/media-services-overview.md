<properties 
	pageTitle="Vue d’ensemble d’Azure Media Services" 
	description="Cette rubrique offre une vue d'ensemble d'Azure Media Services" 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/26/2015" 
	ms.author="juliako"/>

#Vue d’ensemble d’Azure Media Services

Microsoft Azure Media Services est une plateforme extensible basée sur le cloud qui permet aux développeurs de créer des applications évolutives de gestion et de diffusion de médias. Media Services est basé sur les API REST qui permettent de télécharger, stocker, encoder et empaqueter en toute sécurité du contenu vidéo ou audio destiné à être diffusé à la demande ou en direct sur différents clients (par exemple, téléviseurs, PC et appareils mobiles).

Vous pouvez créer des flux de travail de bout en bout en utilisant uniquement Media Services. Vous pouvez aussi choisir d'utiliser des composants tiers pour certaines parties de votre flux de travail (par exemple, en encodant avec un encodeur tiers). Le contenu est ensuite téléchargé, protégé, empaqueté et remis à l'aide de Media Services.

Pour créer des solutions Media Services, vous pouvez utiliser les composants suivants :

- [API REST Media Services](https://msdn.microsoft.com/library/azure/hh973617.aspx)
- Un des SDK clients disponibles : [SDK Azure Media Services pour .NET](https://github.com/Azure/azure-sdk-for-media-services), [SDK Azure pour Java](https://github.com/Azure/azure-sdk-for-java), [Azure Media Services pour Node.js](https://github.com/fritzy/node-azure-media), [SDK PHP Azure](https://github.com/Azure/azure-sdk-for-php)
- Outil existants : [Portail de gestion Azure](http://manage.windowsazure.com/) ou [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer).


L'affiche suivante présente les flux de travail Azure Media Services, de la création des médias à leur consommation. Vous pouvez télécharger l'affiche à partir d'ici : [Affiche Azure Media Services](http://www.microsoft.com/download/details.aspx?id=38195).

![Vue d'ensemble][overview]

**Contrat de Niveau de Service (SLA) **:

- Pour Media Services, nous garantissons une disponibilité de 99,9 % des transactions d'API REST.
- Pour la diffusion en continu, nous traiterons avec succès les demandes de service avec une garantie de disponibilité de 99,9 % pour le contenu multimédia existant quand au moins une unité de diffusion en continu est achetée.
- Pour les Canaux en Direct, nous garantissons que les canaux en cours d’exécution auront une connectivité externe au moins 99,9 % du temps.
- Pour la Protection de Contenu, nous garantissons que nous serons en mesure de traiter les demandes de clé au moins 99,9 % du temps.
- Pour l’Indexeur, nous serons en mesure d’assurer les demandes de tâche d’indexation traitées avec une unité réservée d’encodage 99,9 % du temps.

	Pour plus d’informations, consultez le [contrat SLA Microsoft Azure](http://azure.microsoft.com/support/legal/sla/).

##Concepts

Pour plus d’informations, consultez la page [Concepts](media-services-concepts.md).


##Diffusion multimédia à la demande avec Azure Media Services

La rubrique suivante décrit les étapes d’un flux de travail courant de vidéo à la demande Media Services. La rubrique comprend des liens vers d'autres rubriques qui montrent comment effectuer ces étapes à l'aide de technologies prises en charge par Media Services.

[Diffusion multimédia à la demande avec Azure Media Services](media-services-video-on-demand-workflow.md).

##Diffusion vidéo en flux continu avec Azure Media Services

La rubrique suivante décrit les étapes d’un flux de travail courant de diffusion en continu Media Services. La rubrique comprend des liens vers d'autres rubriques qui montrent comment effectuer ces étapes à l'aide de technologies prises en charge par Media Services.

[Diffusion vidéo en flux continu avec Azure Media Services](media-services-live-streaming-workflow.md).

##Utilisation de contenu

Azure Media Services fournit les outils dont vous avez besoin pour créer des applications de lecteur clientes riches et dynamiques pour la plupart des plateformes, notamment : appareils iOS, Android, Windows, Windows Phone, Xbox et décodeurs. La rubrique suivante fournit des liens vers les Kits de développement logiciel (SDK) et les infrastructures de lecteur que vous pouvez utiliser pour développer vos propres applications clientes pour utiliser la diffusion en continu de médias à partir de Media Services.

[Développement d'applications de lecteur vidéo](media-services-develop-video-players.md)

##Modèles et pratiques recommandées

[Modèles et pratiques recommandées](https://wamsg.codeplex.com/) [Documentation en ligne](https://msdn.microsoft.com/library/dn735912.aspx) [Livre électronique téléchargeable](https://www.microsoft.com/download/details.aspx?id=42629)

##Support

Le [support Azure](http://azure.microsoft.com/support/options/) propose des options de support pour Azure, y compris Media Services.

##Étapes suivantes

[Diffusion vidéo en flux continu avec Azure Media Services](media-services-live-streaming-workflow.md)

[Développement d'applications de lecteur vidéo](media-services-develop-video-players.md)
 
[Développement d'applications de lecteur vidéo](media-services-develop-video-players.md)


<!-- Images -->
[overview]: ./media/media-services-overview/media-services-overview.png
 

<!---HONumber=July15_HO2-->