<properties 
	pageTitle="Encodage d’une ressource à l’aide d’Azure Media Encoder" 
	description="Apprenez à utiliser Azure Media Encoder pour encoder un contenu multimédia sur Media Services. Les exemples de code utilisent l’API REST." 
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
	ms.date="06/18/2015" 
	ms.author="juliako"/>


#Encodage d’une ressource à l’aide d’Azure Media Encoder

Cet article fait partie de la série [workflow de vidéo à la demande Media Services](media-services-video-on-demand-workflow.md).

##Vue d'ensemble
Pour fournir une vidéo numérique sur Internet, vous devez compresser le contenu multimédia. Les fichiers vidéo numériques sont volumineux et peuvent être trop gros pour être fournis sur Internet ou pour que les périphériques de vos clients les affichent correctement. L’encodage est le processus de compression audio et vidéo permettant à vos clients d’afficher votre contenu multimédia.

Les tâches d’encodage sont une des opérations de traitement les plus courantes dans Media Services. Vous créez des tâches d’encodage pour convertir des fichiers multimédias d’un encodage à un autre. Lorsque vous les encodez, vous pouvez utiliser l’encodeur multimédia intégré de Media Services. Vous pouvez aussi utiliser un encodeur fourni par un partenaire Media Services. Ces encodeurs tiers sont disponibles sur Azure Marketplace. Vous pouvez spécifier les détails des tâches d’encodage à l’aide de chaînes de présélection définies pour votre encodeur ou en utilisant des fichiers de configuration prédéfinis. Pour voir les types de présélections disponibles, consultez [Présélections de tâches pour Azure Media Services](https://msdn.microsoft.com/library/azure/dn619392.aspx). Si vous avez utilisé un encodeur tiers, [validez vos fichiers](https://msdn.microsoft.com/library/azure/dn750842.aspx).

Chaque travail peut comporter une ou plusieurs tâches, en fonction du type de traitement que vous souhaitez accomplir. Via l'API REST, vous pouvez créer des travaux et leurs tâches associées de deux manières : les tâches peuvent être définies en ligne via la propriété de navigation de tâches sur les entités de travail ou via le traitement par lots OData. Le Kit de développement logiciel (SDK) Media Services utilise le traitement par lots. Toutefois, pour une meilleure lisibilité des exemples de code dans cette rubrique, les tâches sont définies inline. Pour plus d'informations sur le traitement par lots, consultez [Traitement par lots d'Open Data Protocol (OData)](http://www.odata.org/documentation/odata-version-3-0/batch-processing/). Vous trouverez également un exemple de traitement par lots dans la rubrique [Travail](https://msdn.microsoft.com/library/azure/hh974289.aspx).

Nous vous recommandons de toujours encoder vos fichiers mezzanine sous forme de jeu de fichiers MP4 à débit adaptatif, puis de convertir ce jeu au format souhaité au moyen de l'[empaquetage dynamique](https://msdn.microsoft.com/library/azure/jj889436.aspx). Pour tirer parti de l'empaquetage dynamique, vous devez d'abord obtenir au moins une unité de diffusion en continu à la demande pour le point de terminaison de diffusion en continu à partir duquel vous envisagez de distribuer votre contenu. Pour plus d’informations, voir [Mise à l’échelle de Media Services](media-services-manage-origins.md#scale_streaming_endpoints).

Si votre ressource de sortie est stockée sous forme chiffrée, vous devez configurer une stratégie de remise de ressources. Pour plus d'informations, consultez [Configuration de la stratégie de remise de ressources](media-services-rest-configure-asset-delivery-policy.md).



##Création d’un travail avec une seule tâche d’encodage 

>[AZURE.NOTE]Lorsque vous utilisez l’API REST de Media Services, les considérations suivantes s’appliquent :
>
>Lors de l’accès aux entités dans Media Services, vous devez définir les valeurs et les champs d’en-tête spécifiques dans vos requêtes HTTP. Pour plus d'informations, consultez [Installation pour le développement REST API de Media Services](media-services-rest-how-to-use.md).

>Après vous être connecté à https://media.windows.net, vous recevrez une redirection 301 spécifiant un autre URI Media Services. Vous devez effectuer les appels suivants au nouvel URI comme décrit dans [Connexion à Media Services à l'aide de l'API REST](media-services-rest-connect_programmatically.md).


L’exemple suivant montre comment créer et publier un projet avec une tâche visant à encoder une vidéo en une résolution et une qualité spécifiques. Quand vous encodez à l'aide de l'Encodeur multimédia Azure, vous pouvez utiliser les présélections de configuration de tâche spécifiées [ici](https://msdn.microsoft.com/library/azure/dn619389.aspx).
	
Demande :

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000
	Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3Aaab7f15b-3136-4ddf-9962-e9ecb28fb9d2')"}}],  "Tasks" : [{"Configuration" : "H264 Broadband 720p", "MediaProcessorId" : "nb:mpid:UUID:70bdc2c3-ebf4-42a9-8542-5afc1e55d217",  "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"}]}

Réponse :
	
	HTTP/1.1 201 Created

	. . . 

##Considérations

- les propriétés TaskBody DOIVENT utiliser un XML littéral pour définir le nombre de ressources d’entrée ou de sortie qui seront utilisées par la tâche. La rubrique Tâche contient la définition du schéma XML pour le XML.
- Dans la définition TaskBody, chaque valeur interne de <inputAsset> et <outputAsset> doit être définie en tant que JobInputAsset(valeur) ou JobOutputAsset(valeur).
- Une tâche peut comporter plusieurs ressources de sortie. Un JobOutputAsset(x) ne peut être utilisé qu’une fois en tant que résultat d’une tâche dans un travail.
- Vous pouvez spécifier JobInputAsset ou JobOutputAsset en tant que ressource d’entrée d’une tâche.
- Les tâches ne doivent pas former un cycle.
- Le paramètre de valeur que vous transmettez à JobInputAsset ou à JobOutputAsset représente la valeur d’index pour une ressource. Les ressources réelles sont définies dans les propriétés de navigation InputMediaAssets et OutputMediaAssets de la définition d’entité de travail. 
- Étant donné que Media Services est basé sur OData v3, les ressources dans les collections de propriétés de navigation InputMediaAssets et OutputMediaAssets sont référencées par une paire nom de valeur « __metadata : uri ».
- InputMediaAssets mappe vers une ou plusieurs ressources que vous avez créées dans Media Services. Les OutputMediaAssets sont créés par le système. Ils ne font pas référence à une ressource existante.
- OutputMediaAssets peut être nommé à l’aide de l’attribut assetName. Si cet attribut n’est pas présent, le nom d’OutputMediaAsset sera la valeur de texte interne de l’élément <outputAsset> avec le suffixe de la valeur du nom du travail ou de l’ID de travail (dans le cas où la propriété Name n’est pas définie). Par exemple, si vous affectez à assetName la valeur « Sample », la propriété de Nom d’OutputMediaAsset est définie sur « Sample ». Toutefois, si vous n’avez pas défini de valeur pour assetName, mais avez défini le nom du travail comme « NewJob », le nom d’OutputMediaAsset est « JobOutputAsset(value)_NewJob ». 

L’exemple suivant montre comment définir l’attribut assetName :

	{ "TaskBody" : "<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset assetName="CustomOutputAssetName">JobOutputAsset(0)</outputAsset></taskBody>"}



##Création d’un travail avec des tâches chaînées

Dans de nombreux scénarios d’application, les développeurs souhaitent créer une série de tâches de traitement. Dans Media Services, vous pouvez créer une série de tâches chaînées. Chaque tâche effectue différentes étapes de traitement et peut utiliser différents processeurs multimédias. Les tâches chaînées peuvent transférer une ressource d’une tâche à une autre, en effectuant une séquence linéaire de tâches sur la ressource. Toutefois, les tâches effectuées dans un travail ne le sont pas obligatoirement dans une séquence. Quand vous créez une tâche chaînée, les objets chaînés **ITask** sont créés dans un seul objet **IJob**.

>[AZURE.NOTE]Il existe actuellement une limite de 30 tâches par travail. Si vous devez chaîner plus de 30 tâches, créez plusieurs travaux pour contenir les tâches.


	POST https://media.windows.net/api/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	x-ms-client-request-id: 00000000-0000-0000-0000-000000000000

	{  
	   "Name":"NewTestJob",
	   "InputMediaAssets":[  
	      {  
	         "__metadata":{  
	            "uri":"https://testrest.cloudapp.net/api/Assets('nb%3Acid%3AUUID%3A910ffdc1-2e25-4b17-8a42-61ffd4b8914c')"
	         }
	      }
	   ],
	   "Tasks":[  
	      {  
	         "Configuration":"H264 Adaptive Bitrate MP4 Set 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version="1.0" encoding="utf-8"?><taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>"
	      },
	      {  
	         "Configuration":"H264 Smooth Streaming 720p",
	         "MediaProcessorId":"nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5",
	         "TaskBody":"<?xml version="1.0" encoding="utf-16"?><taskBody><inputAsset>JobOutputAsset(0)</inputAsset><outputAsset>JobOutputAsset(1)</outputAsset></taskBody>"
	      }
	   ]
	}

###Considérations

Pour activer le chaînage des tâches :

- un travail doit comporter au moins 2 tâches
- il doit y avoir au moins une tâche dont l’entrée correspond à la sortie d’une autre tâche du travail.


## Création d'un travail à l'aide d'un JobTemplate


Pendant le traitement de plusieurs ressources à l'aide d'un jeu commun de tâches, les JobTemplates sont particulièrement utiles pour spécifier les présélections de tâches par défaut, l'ordre des tâches, etc..

L'exemple suivant montre comment créer un JobTemplate avec un TaskTemplate défini en ligne. Le TaskTemplate utilise Azure Media Encoder en tant que MediaProcessor pour encoder le fichier de ressource. Toutefois, d'autres MediaProcessors peuvent être également utilisés.


	POST https://media.windows.net/API/JobTemplates HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewJobTemplate25", "JobTemplateBody" : "<?xml version="1.0" encoding="utf-8"?><jobTemplate><taskBody taskTemplateId="nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789"><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody></jobTemplate>", "TaskTemplates" : [{"Id" : "nb:ttid:UUID:071370A3-E63E-4E81-A099-AD66BCAC3789", "Configuration" : "H264 Smooth Streaming 720p", "MediaProcessorId" : "nb:mpid:UUID:2e7aa8f3-4961-4e0c-b4db-0e0439e524f5", "Name" : "SampleTaskTemplate2", "NumberofInputAssets" : 1, "NumberofOutputAssets" : 1}] }
 

>[AZURE.NOTE]Contrairement à d'autres entités Media Services, vous devez définir un nouvel identificateur GUID pour chaque TaskTemplate et le placer dans la propriété taskTemplateId et Id du corps de votre demande. Le schéma d'identification de contenu doit respecter le schéma décrit dans la rubrique Identifier les entités Azure Media Services. En outre, les JobTemplates ne peuvent pas être mis à jour. À la place, vous devez en créer un avec vos modifications mises à jour.
 

Si l’opération réussit, la réponse suivante est retournée :
	
	HTTP/1.1 201 Created
	
	. . .


L'exemple suivant montre comment créer un travail faisant référence à un ID de JobTemplate :

	POST https://media.windows.net/API/Jobs HTTP/1.1
	Content-Type: application/json;odata=verbose
	Accept: application/json;odata=verbose
	DataServiceVersion: 3.0
	MaxDataServiceVersion: 3.0
	x-ms-version: 2.11
	Authorization: Bearer <token value>
	Host: media.windows.net

	
	{"Name" : "NewTestJob", "InputMediaAssets" : [{"__metadata" : {"uri" : "https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A3f1fe4a2-68f5-4190-9557-cd45beccef92')"}}], "TemplateId" : "nb:jtid:UUID:15e6e5e6-ac85-084e-9dc2-db3645fbf0aa"}
	 

Si l’opération réussit, la réponse suivante est retournée :
	
	HTTP/1.1 201 Created
	
	. . . 


##Contrôle des noms de fichier de sortie de l’encodeur Media Services 

Par défaut, Azure Media Encoder crée des noms de fichier de sortie en combinant différents attributs de la ressource d'entrée et du processus d'encodage. Chaque attribut est identifié à l'aide d'une macro, comme indiqué ci-dessous.

Voici une liste complète des macros disponibles pour la dénomination des fichiers de sortie : Débit audio : le débit utilisé pour l'encodage de l'audio, spécifié en kbit/s

- Codec audio : le codec utilisé pour l'encodage audio, les valeurs valides sont : AAC, WMA et DDP
- Nombre de canaux : nombre de canaux audio encodés, les valeurs valides sont : 1, 2 ou 6
- Extension par défaut : l'extension de fichier par défaut 
- Langage : le code de langage BCP-47 représentant le langage utilisé dans l'audio. La valeur actuelle par défaut est « und ». 
- Nom du fichier d'origine : le nom du fichier téléchargé dans Azure Storage
- ID de flux : l'ID de flux défini par l'attribut streamID de l'élément <StreamInfo> dans le fichier de présélection 
- Codec vidéo : le codec utilisé pour l'encodage, les valeurs valides sont : H264 et VC1
- Débit vidéo : le débit utilisé pour l'encodage de la vidéo, spécifié en kbit/s

Ces macros peuvent être combinées dans n'importe quel ordre pour contrôler le nom des fichiers générés par Media Services Encoder. Par exemple, la convention d'affectation de noms par défaut est :

	{Original File Name}_{Video Codec}{Video Bitrate}{Audio Codec}{Language}{Channel Count}{Audio Bitrate}.{Default Extension}

La convention d'affectation de noms de fichier est spécifiée à l'aide de l'attribut DefaultMediaOutputFileName de l'élément [Preset](https://msdn.microsoft.com/library/azure/dn554334.aspx). Par exemple :

	<Preset DefaultMediaOutputFileName="{Original file name}{StreamId}_LongOutputFileName{Bit Rate}{Video Codec}{Video Bitrate}{Audio Codec}{Audio Bitrate}{Language}{Channel Count}.{Default extension}"
	  Version="5.0">
	<MediaFile …>
	   <OutputFormat>
	      <MP4OutputFormat StreamCompatibility="Standard">
	         <VideoProfile>
	            <MainH264VideoProfile … >
	               <Streams  AutoSize="False"
	                         FreezeSort="False">
	                  <StreamInfo StreamId="1"
	                              Size="1280, 720">
	                     <Bitrate>
	                       <ConstantBitrate Bitrate="3400"
	                                        IsTwoPass="False"
	                                        BufferWindow="00:00:05" />
	                     </Bitrate>
	                   </StreamInfo>
	                  </Streams>
	               </MainH264VideoProfile>
	            </VideoProfile>
	         </MP4OutputFormat>
	   </OutputFormat>
	</MediaFile>

L'encodeur insère des traits de soulignement entre chaque macro, par exemple, la configuration ci-dessus donnerait un nom de fichier du type : MyVideo_H264_4500kpbs_AAC_und_ch2_128kbps.mp4.

##Étapes suivantes
Maintenant que vous savez comment créer une tâche pour encoder un élément multimédia, consultez la rubrique [Vérification de la progression d'une tâche avec Media Services](media-services-rest-check-job-progress.md).

[Azure Marketplace]: https://datamarket.azure.com/
[Encoder Preset]: http://msdn.microsoft.com/library/dn619392.aspx
[How to: Get a Media Processor Instance]: http://go.microsoft.com/fwlink/?LinkId=301732
[How to: Upload an Encrypted Asset]: http://go.microsoft.com/fwlink/?LinkId=301733
[How to: Deliver an Asset by Download]: http://go.microsoft.com/fwlink/?LinkId=301734
[How to Check Job Progress]: http://go.microsoft.com/fwlink/?LinkId=301737
[Task Preset for Azure Media Packager]: http://msdn.microsoft.com/library/windowsazure/hh973635.aspx
 

<!---HONumber=58_postMigration-->