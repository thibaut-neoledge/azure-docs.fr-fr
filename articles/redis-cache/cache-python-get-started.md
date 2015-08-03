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
   ms.topic="hero-article"
   ms.tgt_pltfrm="cache-redis"
   ms.workload="required"
   ms.date="04/30/2015"
   ms.author="mwasson"/>

# Utilisation du Cache Redis Azure avec Python

Cette rubrique montre comment utiliser le Cache Redis Azure avec Python.


## Composants requis

Installez [redis-py](https://github.com/andymccurdy/redis-py).


## Créer un Cache Redis sur Azure

Dans le [Portail Azure en version préliminaire](http://go.microsoft.com/fwlink/?LinkId=398536), cliquez sur **Nouveau**, sur **Stockage + Données**, puis sélectionnez **Cache Redis**.

  ![][1]

Entrez un nom d'hôte DNS. Il se présente comme suit : `<name>.redis.cache.windows.net`. Cliquez sur **Create**.

  ![][2]

Une fois le cache créé, cliquez dessus dans le portail pour afficher ses paramètres. Vous aurez besoin de ce qui suit :

- **Nom d’hôte.** C’est le nom que vous avez entré au moment de la création du cache.
- **Port.** Cliquez sur le lien en dessous de **Ports** pour afficher les ports. Utilisez le port SSL.
- **Clé d’accès.** Cliquez sur le lien en dessous de **Clés**, puis copiez la clé primaire.

## Ajouter un élément au cache et le récupérer

    >>> import redis
    >>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
          port=6380, db=0, password='<key>', ssl=True)
    >>> r.set('foo', 'bar')
    True
    >>> r.get('foo')
    b'bar'

Remplacez *&lt;name&gt;* par le nom de votre cache, et *&lt;key&gt;* par votre clé d’accès.


<!--Image references-->
[1]: ./media/cache-python-get-started/cache01.png
[2]: ./media/cache-python-get-started/cache02.png

<!---HONumber=July15_HO4-->