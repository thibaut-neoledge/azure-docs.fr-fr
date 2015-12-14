<properties 
	pageTitle="CDN - Contrôle du comportement de mise en cache des demandes avec des chaînes de requête" 
	description="La mise en cache des chaînes de requête contrôle la manière dont les fichiers doivent être mis en cache lorsqu’ils contiennent des chaînes de requête." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>

#Contrôle du comportement de mise en cache des demandes CDN avec des chaînes de requête

> [AZURE.SELECTOR]
- [Standard](cdn-query-string.md)
- [Premium](cdn-query-string-premium.md)

##Vue d'ensemble

La mise en cache des chaînes de requête contrôle la manière dont les fichiers doivent être mis en cache lorsqu’ils contiennent des chaînes de requête.

> [AZURE.NOTE]Les niveaux Standard et Premium de CDN fournissent la même fonctionnalité de mise en cache de la chaîne de requête, mais l’interface utilisateur est différente. Ce document décrit l’interface utilisateur du niveau **Standard**. Pour le niveau Premium, consultez [Contrôle du comportement de mise en cache des demandes CDN avec des chaînes de requête - Premium](cdn-query-string-premium.md).

Trois modes sont disponibles :

- **Ignorer les chaînes de requête** : il s’agit du mode par défaut. Le nœud de périmètre CDN transmet la chaîne de requête du demandeur vers l’origine de la première demande et met en cache l’élément multimédia. Toutes les demandes ultérieures concernant cet élément multimédia traitées à partir du nœud de périmètre ignorent la chaîne de requête jusqu’à l’arrivée à expiration de l’élément multimédia mis en cache.
- **Ignorer la mise en cache des URL avec des chaînes de requête** : dans ce mode, les demandes avec des chaînes de requête ne sont pas mises en cache au niveau du nœud de périmètre CDN. Le nœud de périmètre récupère l’élément multimédia directement à partir de l’origine et le transmet au demandeur avec chaque demande.
- **Mettre en cache chaque URL unique** : ce mode traite chaque demande avec une chaîne de requête comme élément multimédia unique avec son propre cache. Par exemple, la réponse depuis l’origine d’une demande pour *foo.ashx?q=bar* est mise en cache au niveau du nœud de périmètre et renvoyée pour les caches suivants avec la même chaîne de requête. Une demande pour *foo.ashx?q=somethingelse* est mise en cache comme un élément multimédia distinct avec sa propre durée de vie.
	
	>[AZURE.WARNING]Ce mode ne doit pas être utilisé lorsque la chaîne de requête contient des paramètres qui changent avec chaque demande, comme un ID de session ou un nom d’utilisateur, car cela entraînerait un taux d’accès au cache très faible.

##Modification des paramètres de mise en cache des chaînes de requête

1. Dans le panneau du profil CDN, cliquez sur le point de terminaison CDN que vous souhaitez gérer.
	
	![Points de terminaison du panneau de profil CDN](./media/cdn-query-string/cdn-endpoints.png)

	Le panneau du point de terminaison CDN s’ouvre.

2. Cliquez sur le bouton **Configurer**.

	![Bouton de gestion du panneau de profil CDN](./media/cdn-query-string/cdn-config-btn.png)
	
	Le panneau de configuration CDN s’ouvre.
	
3. Sélectionnez un paramètre dans la liste déroulante **Comportement de mise en cache des chaînes de requête**.
	
	![Options de mise en cache des chaînes de requête CDN](./media/cdn-query-string/cdn-query-string.png)
	
4. Une fois vos sélections effectuées, cliquez sur le bouton **Enregistrer**.




	

<!---HONumber=AcomDC_1203_2015-->