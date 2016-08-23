<properties
	pageTitle="Utilisation du Cache Redis Azure avec Python | Microsoft Azure"
	description="Prise en main du Cache Redis Azure avec Python"
	services="redis-cache"
	documentationCenter=""
	authors="steved0x"
	manager="douge"
	editor="v-lincan"/>

<tags
	ms.service="cache"
	ms.devlang="python"
	ms.topic="hero-article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="08/16/2016"
	ms.author="sdanie"/>

# Utilisation du Cache Redis Azure avec Python

> [AZURE.SELECTOR]
- [.NET](cache-dotnet-how-to-use-azure-redis-cache.md)
- [ASP.NET](cache-web-app-howto.md)
- [Node.JS](cache-nodejs-get-started.md)
- [Java](cache-java-get-started.md)
- [Python](cache-python-get-started.md)

Cette rubrique montre comment utiliser le Cache Redis Azure avec Python.


## Composants requis

Installez [redis-py](https://github.com/andymccurdy/redis-py).


## Créer un Cache Redis sur Azure

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

## Récupérer les clés d’accès et le nom hôte

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## Activer le point de terminaison non SSL

Certains clients Redis ne prennent pas en charge SSL. Le [port non-SSL est désactivé par défaut pour les nouvelles instances Cache Redis Azure.](cache-configure.md#access-ports) Au moment de la rédaction de cet article, le client [redis-py](https://github.com/andymccurdy/redis-py) ne prend pas en charge SSL.

[AZURE.INCLUDE [redis-cache-create](../../includes/redis-cache-non-ssl-port.md)]


## Ajouter un élément au cache et le récupérer


	>>> import redis
	>>> r = redis.StrictRedis(host='<name>.redis.cache.windows.net',
	      port=6380, db=0, password='<key>', ssl=True)
	>>> r.set('foo', 'bar')
	True
	>>> r.get('foo')
	b'bar'


Remplacez `<name>` par le nom de votre cache, et `key` par votre clé d’accès.


<!--Image references-->
[1]: ./media/cache-python-get-started/redis-cache-new-cache-menu.png
[2]: ./media/cache-python-get-started/redis-cache-cache-create.png

<!---HONumber=AcomDC_0817_2016-->