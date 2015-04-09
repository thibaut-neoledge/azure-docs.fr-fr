<properties 
   pageTitle="Utilisation du Cache Redis Azure avec Python" 
   description="Prise en main du Cache Redis Azure avec Python" 
   services="redis-cache" 
   documentationCenter="" 
   authors="MikeWasson" 
   manager="wpickett" 
   editor=""/>

<tags
   ms.service="cache"
   ms.devlang="python"
   ms.topic="article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="required" 
   ms.date="02/19/2015"
   ms.author="mwasson"/>

# Utilisation du Cache Redis Azure avec Python 

Cette rubrique montre comment utiliser le Cache Redis Azure avec Python.


## Configuration requise

Installez [redis-py](https://github.com/andymccurdy/redis-py).


## Créer un Cache Redis sur Azure

Dans la [version préliminaire du portail de gestion Azure](http://go.microsoft.com/fwlink/?LinkId=398536), cliquez sur **Nouveau**, puis sélectionnez **Cache Redis**. 

  ![][1]

Entrez un nom d'hôte DNS. Il doit être au format suivant `<nom>.redis.cache.windows.net`. Cliquez sur **Créer**.

  ![][2]

Une fois le cache créé, cliquez dessus dans le portail pour afficher ses paramètres. Vous aurez besoin de ce qui suit :

- **Nom d'hôte** : c'est le nom que vous avez entré au moment de la création du cache.
- **Port** : cliquez sur le lien en dessous de **Ports** pour afficher les ports. Utilisez le port SSL. 
- **Clé d'accès** : cliquez sur le lien en dessous de **Clés**, puis copiez la clé primaire.

## Ajouter un élément au cache et le récupérer

    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'
    
Remplacez *&lt;name&gt;* par le nom de votre cache, et *&lt;key&gt;* par votre clé d'accès. 


<!--Image references-->
[1]: ./media/cache-python-get-started/cache01.png
[2]: ./media/cache-python-get-started/cache02.png


<!--HONumber=49-->