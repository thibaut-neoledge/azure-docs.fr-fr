Une zone DNS permet d’héberger les enregistrements DNS d’un domaine particulier. Afin d’héberger votre domaine, vous devez créer une zone DNS. Les enregistrements DNS créés pour un domaine particulier sont situés dans une zone DNS pour le domaine.

Par exemple, le domaine « contoso.com » peut contenir un certain nombre d’enregistrements DNS, tels que « mail.contoso.com » (pour un serveur de messagerie) et « www.contoso.com » (pour un site web).

## <a name="a-namenamesaabout-dns-zone-names"></a><a name="names"></a>À propos des noms de zones DNS
* Le nom de la zone doit être unique dans le groupe de ressources et la zone ne doit pas déjà exister. Sinon, l’opération échoue.
* Le même nom de zone peut être réutilisé dans un autre groupe de ressources ou abonnement Azure.
* Lorsque plusieurs zones partagent le même nom, chaque instance se voit affecter différentes adresses de serveur de noms, et une seule instance peut être déléguée à partir du domaine parent. Pour plus d’informations, consultez la page [Délégation d’un domaine à Azure DNS](../articles/dns/dns-domain-delegation.md).


<!--HONumber=Nov16_HO2-->


