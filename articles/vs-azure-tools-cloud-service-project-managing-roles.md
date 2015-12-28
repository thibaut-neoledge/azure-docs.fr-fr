<properties
   pageTitle="Gestion des rôles dans les projets de services cloud Azure avec Visual Studio | Microsoft Azure"
   description="Découvrez comment ajouter de nouveaux rôles à votre projet de service cloud Azure ou supprimer des rôles existants en utilisant Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/08/2015"
   ms.author="tarcher" />

# Gestion des rôles dans les projets de services cloud Azure avec Visual Studio

Après avoir créé votre projet de service cloud Azure, vous pouvez ajouter de nouveaux rôles ou supprimer des rôles existants. Vous pouvez également importer un projet existant et le convertir à un rôle. Par exemple, vous pouvez importer une application web ASP.NET et la désigner comme rôle web.

## Ajout ou suppression de rôles

**Pour ajouter un rôle**

Dans l’**Explorateur de solutions**, ouvrez le menu contextuel du nœud **Rôles** dans votre projet de service cloud et sélectionnez **Ajouter**. Vous pouvez sélectionner un rôle web existant ou un rôle de travail à partir de la solution actuelle ou créer un nouveau projet de rôle web ou de travail. Ou vous pouvez sélectionner un projet approprié, par exemple un projet d'application web ASP.NET, et l'associer à un projet de rôle.

**Pour supprimer une association de rôle**

Dans le nœud **Rôles** du projet de service cloud dans l’Explorateur de solutions, ouvrez le menu contextuel du rôle à supprimer et sélectionnez **Supprimer**.

## Suppression et ajout de rôles dans votre service cloud

Si vous supprimez un rôle dans votre projet de service cloud mais décidez ultérieurement de rétablir le rôle dans le projet, seuls la déclaration de rôle et les attributs de base, tels que les informations de diagnostic et les points de terminaison, sont ajoutés. Aucune ressource ou référence supplémentaire n’est ajoutée au fichier ServiceDefinition.csdef ou au fichier ServiceConfiguration.cscfg. Si vous souhaitez ajouter ces informations, vous devez les ajouter manuellement à ces fichiers.

Par exemple, vous pouvez supprimer un rôle de service web et décider ultérieurement de rétablir ce rôle dans votre solution. Dans ce cas, une erreur se produit. Pour éviter cette erreur, vous devez rajouter l’élément `<LocalResources>` indiqué dans le code XML suivant au fichier ServiceDefinition.csdef. Utilisez le nom du rôle de service web que vous avez rajouté au projet comme partie de l’attribut de nom pour l’élément **<LocalStorage>**. Dans cet exemple, le nom du rôle de service web est **WCFServiceWebRole1**.

	<WebRole name="WCFServiceWebRole1">
	    <Sites>
	      <Site name="Web">
	        <Bindings>
	          <Binding name="Endpoint1" endpointName="Endpoint1" />
	        </Bindings>
	      </Site>
	    </Sites>
	    <Endpoints>
	      <InputEndpoint name="Endpoint1" protocol="http" port="80" />
	    </Endpoints>
	    <Imports>
	      <Import moduleName="Diagnostics" />
	    </Imports>
	   <LocalResources>
	      <LocalStorage name="WCFServiceWebRole1.svclog" sizeInMB="1000" cleanOnRoleRecycle="false" />
	   </LocalResources>
	</WebRole>

## Étapes suivantes

Découvrez comment configurer des rôles dans Visual Studio en lisant [Configuration des rôles pour un service cloud Azure avec Visual Studio](vs-azure-tools-configure-roles-for-cloud-service.md).

<!---HONumber=AcomDC_1217_2015-->