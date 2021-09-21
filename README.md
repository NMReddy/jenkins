version: "3"

services:

  # zaproxy:
  #   image: owasp/zap2docker-stable:latest
  #   volumes:
  #     - $PWD/projectname:/app
  #     - $PWD/data:/usr/share/dependency-check/data
  #     - $PWD/report:/report
  #   command: --scan /app --format "XML" --project "projectname"  

  # owasp:
  #   image: owasp/dependency-check
  #   volumes:
  #     - $PWD/projectname:/app
  #     - $PWD/data:/usr/share/dependency-check/data
  #     - $PWD/report:/report
  #   command: --scan /app --format "XML" --project "projectname"  

  #node js scan
  nodejsscan:
    image: opensecurity/nodejsscan:latest
    ports:
      - 9002:9090
    hostname: nodejsscan
    deploy:
      replicas: 1
      restart_policy:
        condition: on-failure
      
  #sonar cube app
  sonarqube:
    image: sonarqube:8.9.2-community
    ports:
      - 9001:9000
    networks:
      - sonarnet
    environment:
      - sonar.jdbc.username=sonar
      - sonar.jdbc.password=sonar
      - sonar.jdbc.url=jdbc:postgresql://db:5432/sonar
    volumes:
      - sonarqube_conf:/opt/sonarqube/conf
      - sonarqube_data:/opt/sonarqube/data
      - sonarqube_extensions:/opt/sonarqube/extensions
      - sonarqube_bundled-plugins:/opt/sonarqube/lib/bundled-plugins
  
  #sonar cube pgsql database
  db:
    image: postgres
    networks:
      - sonarnet
    environment:
      - DATABASE_HOST=localhost 
      - POSTGRES_USER=sonar
      - POSTGRES_PASSWORD=sonar
      - POSTGRES_DB=sonar
    ports:
      - 5432:5432
    volumes:
      - postgresql:/var/lib/postgresql
      - postgresql_data:/var/lib/postgresql/data

networks:
  sonarnet:

volumes:
  sonarqube_conf:
  sonarqube_data:
  sonarqube_extensions:
  sonarqube_bundled-plugins:
  postgresql:
  postgresql_data:
