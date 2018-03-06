# WIZportaal NLX Outway configuratie

Tijdens de [Common Ground Fieldlab](https://commonground.pleio.nl/cms/view/54475749/fieldlab-overzicht) hebben we een service gebouwd die vanuit de NLX-servicelaag aan te roepen is. De configuratie hiervan staat hier beschreven.

## Serviceconfiguratie

Op de [documentatiepagina van NLX](https://docs.nlx.io/) staat beschreven hoe je een [service moet aanmaken](https://docs.nlx.io/developing-on-nlx/creating-a-service/).
De configuratie is te vinden in `/home/docker/outway/service-config.toml`:
```
[services]

    ## Servicenaam zoals die getoond wordt in de service directory: https://directory.demo.nlx.io/
    ## Adres is het werkelijke endpoint waar de service draait
    [services.WIZportaal]
    address = "http://jsonplaceholder.typicode.com/posts/"
```

## Herladen bijgewerkte service

Wanneer de serviceconfiguratie wordt aangepast of wordt uitgebreid moet ook de `inway-container` opnieuw gestart worden.
Gebruik hiervoor de volgende commando's:

1. Vraag de id van de draaiende containers op met: `docker ps`, dit toont alle draaiende containers en geeft als het goed is één container terug
2. Kopieer de waarde onder `CONTAINER ID`
3. Stop de container met `docker stop {CONTAINER ID}`
4. Verwijder de gestopte container. We willen voorkomen dat we een grote verzameling niet gebruikte containers op de server krijgen: `docker rm {CONTAINER ID}`
5. Controleer of alle containers nu verwijderd zijn, alle inactieve containters kun je opvragen met: `docker ps -a`. Als het goed is zijn er geen inactieve containers. Zijn die er wel, dan mag je die ook verwijderen.
6. Als het [serviceregister](https://directory.demo.nlx.io/) de WIZportaal als offline toont weet je dat de container succesvol verwijderd is.
7. Start nu een nieuwe Inway-container met de aangepaste service configuratie:

```
docker run -d \
-v /home/docker/outway/root.crt:/certs/root.crt \
-v /home/docker/outway/wizportaal.solvishare.nl.crt:/certs/inway.crt \
-v /home/docker/outway/wizportaal.solvishare.nl.key:/certs/inway.key \
-v /home/docker/outway/service-config.toml:/service-config.toml \
-e DIRECTORY_ADDRESS=directory.demo.nlx.io:1984 \
-e SELF_ADDRESS=wizportaal.solvishare.nl:2018 \
-e SERVICE_CONFIG=/service-config.toml \
-e TLS_NLX_ROOT_CERT=/certs/root.crt \
-e TLS_ORG_CERT=/certs/inway.crt \
-e TLS_ORG_KEY=/certs/inway.key \
-p 2018:2018 \
nlxio/inway:latest
```

8. Als het [serviceregister](https://directory.demo.nlx.io/) de WIZportaal als online toont weet je dat de container succesvol herstart is.
