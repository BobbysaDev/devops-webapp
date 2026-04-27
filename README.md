

    subgraph VCS["Version Control"]
        Git[GitHub Repository]
    end

    subgraph Cloud["AWS Cloud Infrastructure"]
        subgraph EC2["EC2 Instance (Ubuntu)"]
            
            subgraph CI_CD["Jenkins CI/CD Pipeline"]
                Jen[Jenkins Server]
                TrivyFS[Trivy FS Scan]
                DockBuild[Docker Build]
                TrivyImg[Trivy Image Scan]
                AppDeploy[Deploy App Container]

                Jen -->|1. Pull Code| TrivyFS
                TrivyFS -->|2. Pass| DockBuild
                DockBuild -->|3. Build| TrivyImg
                TrivyImg -->|4. Pass| AppDeploy
            end

            subgraph Observability["Monitoring & Alerting Stack"]
                NodeExp[Node Exporter<br>Host Metrics]
                Prom[Prometheus<br>Port 9090]
                Graf[Grafana<br>Port 3000]
                Alert[Alertmanager<br>Port 9093]

                NodeExp -.->|Scrapes :9100| Prom
                Prom -.->|Queries Data| Graf
                Prom -.->|Fires Rules| Alert
            end
            
            AppDeploy -.->|Generates Load| NodeExp
        end
        
        ECR[(AWS ECR Optional)]
    end

    Email[Email Inbox]

    Dev -->|Push Code| Git
    Git -->|Trigger| Jen
    TrivyImg -->|Push| ECR
    ECR -.->|Pull| AppDeploy
    Alert -->|SMTP Alert| Email

    classDef aws fill:#FF9900,stroke:#232F3E,stroke-width:2px,color:white;
    class ECR aws;# DevOps CI/CD Project using GitHub Actions

