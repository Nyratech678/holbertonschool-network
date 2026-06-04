"""
    %%{init: {"theme": "default"}}%%
    sequenceDiagram
    participant Browser
    participant DNS
    participant RootTLD
    participant AuthDNS
    participant Internet
    participant Firewall
    participant LB as LoadBalancer
    participant Web as WebServer
    participant App as AppServer
    participant DB as Database

    Note over Browser,AuthDNS: 1. DNS — trouve l'adresse IP
    Browser->>DNS: Requête DNS pour google.com
    DNS->>RootTLD: Qui gère .com ?
    RootTLD-->>DNS: Serveurs de google.com
    DNS->>AuthDNS: Demande à l'autoritaire
    AuthDNS-->>DNS: 142.250.179.68
    DNS-->>Browser: Renvoie l'IP

    Note over Browser: 2. Connexion TCP (SYN / SYN-ACK / ACK)
    Browser->>Internet: TCP SYN vers 142.250.179.68:443
    Internet-->>Browser: SYN-ACK
    Browser-->>Internet: ACK

    Note over Browser,Internet: 3. TLS (HTTPS) — chiffrement
    Browser->>Internet: Handshake TLS (certificat, échange de clés)
    Internet-->>Browser: Session sécurisée établie

    Note over Internet,Firewall: 4. Passage par pare-feu
    Internet->>Firewall: Paquet chiffré (port 443)
    Firewall-->>LB: Paquet autorisé

    Note over LB: 5. Load balancer (choix du serveur)
    LB->>LB: Déchiffre si SSL termination
    LB->>Web: Requête HTTP interne

    Note over Web,App: 6. Web server → reverse proxy
    Web->>App: Transmet la requête

    Note over App,DB: 7. Application récupère les données
    App->>DB: Requête base de données
    DB-->>App: Résultat
    App->>App: Génère la page HTML

    Note over App,Web: 8. Réponse et rendu
    App-->>Web: HTML généré
    Web-->>LB: Renvoie le contenu
    LB-->>Firewall: Paquet (re)chiffré
    Firewall-->>Internet: Transmission
    Internet-->>Browser: Réponse HTTPS
    Browser->>Browser: Affiche la page
"""

Explication
1) DNS : ton navigateur demande l'IP correspondant à google.com.
2) TCP : ton navigateur établit une connexion avec cette IP.
3) TLS : le navigateur et le serveur négocient un chiffrement.
4) Pare-feu : le trafic est filtré selon des règles.
5) Load balancer : le trafic est dirigé vers un serveur disponible.
6) Reverse proxy : le serveur web transmet la requête à l'application.
7) Application : l'application consulte la base de données et construit la page.
8) Retour : la page traverse le chemin inverse et ton navigateur l'affiche.
