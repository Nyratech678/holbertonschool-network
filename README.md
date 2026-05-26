sequenceDiagram
    participant Opérateur
    participant CLI
    participant Orchestrateur
    participant Core(TechnoDetector+Request)
    participant MoteurRust
    participant NoSQLManager
    participant Cible

    Opérateur->>CLI: lance scan (URL, wordlist-dir, options)
    CLI->>Orchestrateur: start_scan(...)

    Note over Orchestrateur,Core: 1. Détection de technologie
    Orchestrateur->>Core: détecter la techno cible
    Core->>MoteurRust: requête initiale (furtive)
    MoteurRust->>Cible: HTTP
    Cible-->>MoteurRust: réponse
    MoteurRust-->>Core: réponse
    Core->>Core: analyse signatures regex → techno identifiée
    Core-->>Orchestrateur: wordlists adaptées

    Note over Orchestrateur,NoSQLManager: 2. Scan asynchrone avec évasion
    loop chaque mot des wordlists
        Orchestrateur->>Core: requête furtive(url + mot)
        Core->>Core: rotation proxy, user-agent, jitter
        Core->>MoteurRust: http_request
        MoteurRust->>Cible: requête obfusquée
        Cible-->>MoteurRust: réponse
        MoteurRust-->>Core: réponse
        Core-->>Orchestrateur: résultat
        Orchestrateur->>NoSQLManager: sauvegarder résultat
        CLI-->>Opérateur: affichage en direct

        Note over Orchestrateur,Core: 3. Extraction sous-domaines et rebouclage
        Orchestrateur->>Core: extraire sous-domaines du body
        Core-->>Orchestrateur: sous-domaines
        loop chaque sous-domaine non scanné
            Orchestrateur->>Core: lancer nouvelle requête furtive sur sous-domaine
            Core->>MoteurRust: http_request
            MoteurRust->>Cible: HTTP
            Cible-->>MoteurRust: réponse
            MoteurRust-->>Core: réponse
            Core-->>Orchestrateur: résultat
            Orchestrateur->>NoSQLManager: sauvegarder
        end
    end

    Orchestrateur->>NoSQLManager: finaliser session
    CLI-->>Opérateur: scan terminé
