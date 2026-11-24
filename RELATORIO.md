# Relatorio de evidencias - Trabalho Pratico Integrador 01-06

Nome: ____________________________   Data: __________
URL do repositorio Git: ____________________________

## E01 - Ambiente (1 valor)
Comandos e versoes observadas:
- Docker version 29.9.2, build a82e528
- Docker Compose version v5.6.1
- git version 2.55.0.windows.2
- Server:
    Containers: 6
    Running: 0
    Paused: 0
    Stopped: 6
    Images: 8
    Server Version: 29.9.2
    Storage Driver: overlayfs
    driver-type: io.containerd.snapshotter.v1
    Logging Driver: local
    Cgroup Driver: cgroupfs
    Cgroup Version: 2
    Plugins:
    Volume: local
    Network: bridge host ipvlan macvlan null overlay
    Log: awslogs fluentd gcplogs gelf journald json-file local splunk syslog
    CDI spec directories:
    /etc/cdi
    /var/run/cdi
    Discovered Devices:
    cdi: docker.com/gpu=webgpu
    Swarm: inactive
    Runtimes: io.containerd.runc.v2 nvidia runc
    Default Runtime: runc
    Init Binary: docker-init
    containerd version: b7a25261e029d68978bbf290df0a62fc819b3c51
    runc version: v1.5.2-0-g29dd3dc2
    init version: de40ad0
    Security Options:
    seccomp
    Profile: builtin
    cgroupns
    Kernel Version: 6.18.58.1-microsoft-standard-WSL2
    Operating System: Docker Desktop
    OSType: linux
    Architecture: x86_64
    CPUs: 8
    Total Memory: 3.718GiB
    Name: docker-desktop
    ID: c65ca4df-8ba5-4b81-bfb7-bf0b5a70c605
    Docker Root Dir: /var/lib/docker
    Debug Mode: false
    HTTP Proxy: http.docker.internal:3128
    HTTPS Proxy: http.docker.internal:3128
    No Proxy: hubproxy.docker.internal
    Labels:
    com.docker.desktop.address=npipe://\\.\pipe\docker_cli
    Experimental: false
    Insecure Registries:
    hubproxy.docker.internal:5555
    127.0.0.0/8
    ::1/128
    Live Restore Enabled: false
    Firewall Backend: iptables
- Ficheiros identificados: app.py, tests/, compose.yaml, Jenkinsfile


## E02 - Dockerfile e container (3 valores)
Tag da imagem; docker ps; HTTP /health; utilizador efetivo do container:

- Tag: natixis-support:1.0
- Docker ps:
CONTAINER ID   IMAGE                 COMMAND                  CREATED          STATUS          PORTS                       NAMES
916706e65803   natixis-support:1.0   "python -m uvicorn a…"   26 seconds ago   Up 25 seconds   127.0.0.1:18080->8000/tcp   tp-api
- INFO:     172.17.0.1:36508 - "GET /health HTTP/1.1" 200 OK
- uid=1000(appuser)

## E03 - Compose, DNS, healthcheck e volume (4 valores)
Resultado de docker compose config; health; teste checker -> api; contador antes/depois da recriacao:
- config --quiet: sem erros
- Health:
NAME                       IMAGE                   COMMAND                  SERVICE   CREATED          STATUS                   PORTS
natixis-integrador-api-1   natixis-support:local   "python -m uvicorn a…"   api       10 seconds ago   Up 8 seconds (healthy)   127.0.0.1:18080->8000/tcp
- DNS (checker -> api): {"status":"ok","service":"support-api-integrador","version":"1.0"}
- Contador: antes = 1 | apos POST = 2 | apos --force-recreate = 2


## E04 - Incidente Docker (3 valores)
Sintoma, comandos de investigacao, causa-raiz, alteracao realizada, prova da recuperacao e contador preservado:

## E05 - Git (3 valores)
Branch, hashes de commits, merge e tag v1.0.0:

## E06 - Jenkins SUCCESS (4 valores)
URL/numero do build, etapas e identificador da imagem construida:

## E07 - Jenkins FAILURE e recuperacao (2 valores)
Build FAILURE, etapa que falhou e excerto relevante do log; build SUCCESS apos correcao:

## Verificacao final
[ ] Dockerfile [ ] compose.yaml [ ] app.py [ ] tests/ [ ] Jenkinsfile
[ ] Historico Git [ ] Tag v1.0.0 [ ] Evidencias E01-E07
