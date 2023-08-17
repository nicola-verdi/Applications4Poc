# Installazione GuestBook su openshift
##

### creare il progetto
``` 
oc new-project guestbook
```

### applicare in ordine  gli yaml che compongono l'infrastruttura di backend basata su redis:
```
oc apply -f 01-redis-leader.yaml  
oc apply -f 02-redis-leader-service.yaml  
oc apply -f 03-redis-slave.yaml  
oc apply -f 04-redis-slave-service.yaml  
```

### installare la parte front-end 
Nota: dato che l'immagine impostata esegue apache con utente root è necessario dare dei privilegi aggiuntivi all POD in fase di avvio.
per far questo creeremo un service account dedicato e daremo i priviliegi solo a quel deployment
 
####creare un service account 
```
oc create serviceaccount frontend
```

#### assegnare al service account i privilegi anyuid
da eseguire solo su openshift, su kubernetes vanilla non server
```
  oc adm policy add-scc-to-user anyuid -z frontend 
```

#### Creo i pod della parte di front-end
oc apply -f 05-frontend-deployment.yaml  
oc apply -f 06-frontend-service.yaml  

### creo l'ingress per permettere l'accesso al servizio
oc apply -f 07-ingress.yaml
