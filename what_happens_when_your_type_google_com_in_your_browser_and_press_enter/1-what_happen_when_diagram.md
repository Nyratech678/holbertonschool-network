# Flux de chargement d'une page web

```mermaid
%%{init: {"theme": "default"}}%%
sequenceDiagram
    participant Browser as Navigateur
    participant DNS as DNS
    participant RootTLD as Serveur racine .com
    participant AuthDNS as DNS autoritaire
    participant Internet
    participant Firewall as Pare-feu
    participant LB as Load Balancer
    participant Web as Serveur Web
    participant App as Serveur d'app
    participant DB as Base de données

    Note over Browser, AuthDNS: 1. Résolution DNS → IP
    Browser->>DNS: Requête DNS pour google.com
    DNS->>RootTLD: Qui gère .com ?
    RootTLD-->>DNS: Serveurs de google.com
    DNS->>AuthDNS: Demande à l'autoritaire
    AuthDNS-->>DNS: 142.250.179.68
    DNS-->>Browser: IP 142.250.179.68

    Note over Browser, Internet: 2. Connexion TCP (SYN / SYN-ACK / ACK)
    Browser->>Internet: TCP SYN vers 142.250.179.68:443
    Internet-->>Browser: SYN-ACK
    Browser-->>Internet: ACK

    Note over Browser, Internet: 3. Handshake TLS (HTTPS)
    Browser->>Internet: Échange de certificat et clés
    Internet-->>Browser: Session chiffrée établie

    Note over Internet, Firewall: 4. Filtrage pare-feu
    Internet->>Firewall: Paquet chiffré (port 443)
    Firewall-->>LB: Paquet autorisé

    Note over LB: 5. Répartition de charge (SSL offload)
    LB->>LB: Déchiffrement SSL
    LB->>Web: Requête HTTP interne

    Note over Web, App: 6. Reverse proxy → application
    Web->>App: Transmet la requête

    Note over App, DB: 7. Logique métier & base de données
    App->>DB: Requête SQL
    DB-->>App: Données
    App->>App: Génération de la page HTML

    Note over App, Web: 8. Réponse et rendu final
    App-->>Web: HTML
    Web-->>LB: Contenu
    LB-->>Firewall: Paquet (re)chiffré
    Firewall-->>Internet: Transmission
    Internet-->>Browser: Réponse HTTPS
    Browser->>Browser: Affichage de la page

## Explication détaillée du flux

1. **Résolution DNS**  
   Le navigateur interroge le système DNS pour convertir le nom de domaine (ex. `google.com`) en adresse IP routable.  
   *Étapes :* requête récursive → serveur racine → serveur TLD → serveur autoritaire → réponse avec l’IP.

2. **Établissement de la connexion TCP**  
   Une connexion fiable est établie entre le navigateur et le serveur distant via le *three-way handshake* (SYN, SYN-ACK, ACK) sur le port 443 (HTTPS).

3. **Négociation TLS (HTTPS)**  
   Le navigateur et le serveur entament un handshake TLS pour négocier les paramètres de chiffrement, échanger les certificats et établir une session sécurisée. Toutes les données échangées par la suite sont chiffrées.

4. **Filtrage par le pare-feu**  
   Le paquet chiffré traverse un pare-feu qui applique des règles de sécurité : si le flux est autorisé (port, protocole, IP conformes), il est relayé vers l’infrastructure interne.

5. **Répartition de charge (Load Balancer)**  
   Le load balancer reçoit le trafic, peut effectuer une terminaison SSL (déchiffrement) puis distribue la requête vers un serveur web disponible selon la politique de répartition configurée (round-robin, moindre connexion, etc.).

6. **Reverse proxy et serveur web**  
   Le serveur web (souvent agissant comme reverse proxy) transmet la requête HTTP au serveur d’application approprié, en isolant l’infrastructure interne du client.

7. **Traitement par l’application**  
   Le serveur applicatif exécute la logique métier, interroge la base de données (ou d’autres services) et assemble la réponse, généralement une page HTML dynamique.

8. **Réponse et rendu final**  
   Le contenu HTML emprunte le chemin inverse : il remonte du serveur d’application au serveur web, au load balancer (chiffré de nouveau si nécessaire), au pare-feu puis via Internet jusqu’au navigateur.  
   Le navigateur interprète le HTML, le CSS et le JavaScript pour afficher la page à l’utilisateur.