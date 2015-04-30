Azure détermine la version de Python à utiliser pour son environnement virtuel avec l'ordre de priorité suivant :

1. Version spécifiée dans runtime.txt dans le dossier racine
1. Version spécifiée par le paramètre Python dans la configuration du site web (page Configurer du portail Azure)
1. Python 2.7 est la valeur par défaut si aucune de ces versions n'est spécifiée.

Les valeurs valides pour le contenu de 

    \runtime.txt

sont :

- python-2.7
- python-3.4

Si la version micro (le troisième chiffre) est spécifiée, elle est ignorée.
<!--HONumber=52--> 
