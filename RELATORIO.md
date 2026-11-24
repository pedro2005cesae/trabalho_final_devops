# Relatorio de evidencias - Trabalho Pratico Integrador 01-06

Nome: Pedro Jesus   Data: 24/11/2026
URL do repositorio Git: https://github.com/pedro2005cesae/trabalho_final_devops

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
Conclusao: Docker Engine, Docker Compose e Git responderam corretamente e foram identificados os componentes do projeto.

## E02 - Dockerfile e container (3 valores)
Tag da imagem; docker ps; HTTP /health; utilizador efetivo do container:
- Tag: natixis-support:1.0
- Docker ps:
CONTAINER ID   IMAGE                 COMMAND                  CREATED             STATUS         PORTS                       NAMES
040c583384c7   natixis-support:1.0   "python -m uvicorn a…"   Less than a second ago   Up 1 second   127.0.0.1:18081->8000/tcp   tp-api
- GET /health: {"status":"ok","service":"support-api","version":"1.0","team":"support"}
- HTTP: 200 OK
- Utilizador efetivo: uid=1000(appuser) gid=1000(appuser)
- Logs: "INFO: Uvicorn running on http://0.0.0.0:8000" e "GET /health HTTP/1.1" 200 OK
Conclusao: a imagem natixis-support:1.0 foi criada e executada sem privilegios. Como a porta 18080 estava ocupada pelo Compose, usei 127.0.0.1:18081 para a execucao standalone.

## E03 - Compose, DNS, healthcheck e volume (4 valores)
Resultado de docker compose config; health; teste checker -> api; contador antes/depois da recriacao:
- config --quiet: sem erros
- Health:
NAME                       IMAGE                   COMMAND                  SERVICE   CREATED          STATUS                   PORTS
natixis-integrador-api-1   natixis-support:local   "python -m uvicorn a…"   api       6 seconds ago   Up 4 seconds (healthy)   127.0.0.1:18080->8000/tcp
- DNS (checker -> api): {"status":"ok","service":"support-api-integrador","version":"1.0","team":"support"}
- Contador: antes = 2 | apos POST = 3 | apos --force-recreate = 3
Conclusao: docker compose config --quiet terminou sem erros, o healthcheck ficou healthy e o checker resolveu o nome api. O contador nao foi perdido porque os dados ficam no volume nomeado app-data.

## E04 - Incidente Docker (3 valores)
Sintoma, comandos de investigacao, causa-raiz, alteracao realizada, prova da recuperacao e contador preservado:
- Sintoma: apos aplicar incidente.yaml, o servico ficou em estado health starting/degraded e passou a ouvir na porta 8001.
- Diagnostico:
- docker compose -f compose.yaml -f incidente.yaml ps
- docker compose -f compose.yaml -f incidente.yaml logs --tail 30 api
- docker compose -f compose.yaml -f incidente.yaml config
- Evidencia: "Uvicorn running on http://0.0.0.0:8001"
- Causa: o incidente altera a porta da aplicacao para 8001, enquanto o healthcheck usa 127.0.0.1:8000/health e o mapeamento publica 127.0.0.1:18080 -> 8000.
- Alteracao: docker compose up -d --force-recreate, usando novamente a configuracao base sem remover o volume.
- Recuperacao: docker compose ps passou a mostrar o servico healthy.
- Prova: GET /health devolveu {"status":"ok","service":"support-api-integrador","version":"1.0","team":"support"}; GET /visits devolveu {"visits":3}.
Conclusao: a causa-raiz foi a porta incorreta aplicada pelo incidente. O servico recuperou sem remover app-data e o contador ficou preservado.

## E05 - Git (3 valores)
Branch, hashes de commits, merge e tag v1.0.0:
- Branch principal: main
- Branch integrada: fix/health-info
- Commit 24e0ac6: feat: ambiente integrador funcional
- Commit a83f073: fix: acrescentar identificacao no health
- Merge: fix/health-info integrada em main por fast-forward
- Tag anotada: v1.0.0
- Commits da demonstracao Jenkins: 55657e2 (demo: falha controlada da assercao health) e 7648e55 (fix: restaurar assercao health apos demonstracao)
- Historico: git log --oneline --graph --decorate -8
- Testes finais: 2 passed
- Remoto: https://github.com/pedro2005cesae/trabalho_final_devops
- Publicacao: main, fix/health-info e tag v1.0.0 publicadas no remoto origin.
Conclusao: a branch fix/health-info foi integrada em main, as referencias foram publicadas e ficaram registados os commits da falha controlada e da recuperacao.

## E06 - Jenkins SUCCESS (4 valores)
URL/numero do build, etapas e identificador da imagem construida:
- Job: http://localhost:8080/job/fastapi-ci/
- SCM: https://github.com/pedro2005cesae/trabalho_final_devops (branch */main)
- Jenkinsfile: Jenkinsfile (raiz do repositorio)
- URL/numero do build: http://localhost:8080/job/fastapi-ci/15/
- Etapas executadas: Checkout | Tests (2 passed) | Docker Build
- Resultado: SUCCESS
- Tag da imagem: natixis-support:ci-15
- Identificador da imagem: sha256:a7ddef98cfac2365794724b59dc96f73c4acffd527fde6f28184857262158028
Conclusao: o job executou Checkout, Tests e Docker Build com sucesso. Apos corrigir o agente Jenkins para disponibilizar o Docker CLI, a imagem foi construida e inspecionada.

## E07 - Jenkins FAILURE e recuperacao (2 valores)
Build FAILURE, etapa que falhou e excerto relevante do log; build SUCCESS apos correcao:
- Build FAILURE: http://localhost:8080/job/fastapi-ci/16/
- Etapa que falhou: Tests (1 failed, 1 passed); Docker Build foi ignorado.
- Excerto:
assert response.json()["status"] == "offline"
E       AssertionError: assert 'ok' == 'offline'
- Commit da falha: 55657e2 demo: falha controlada da assercao health
- Build SUCCESS apos correcao: http://localhost:8080/job/fastapi-ci/17/
- Resultado apos correcao: 2 passed; Docker Build SUCCESS (natixis-support:ci-17).
- Identificador da imagem: sha256:a7ddef98cfac2365794724b59dc96f73c4acffd527fde6f28184857262158028
- Commit da correcao: 7648e55 fix: restaurar assercao health apos demonstracao
Conclusao: o Jenkins confirmou a falha controlada na etapa Tests e o build seguinte ficou SUCCESS depois de restaurar a assercao.

## Verificacao final
[x] Dockerfile [x] compose.yaml [x] app.py [x] tests/ [x] Jenkinsfile
[x] Historico Git [x] Tag v1.0.0 [x] URL remoto configurado [x] Branch publicado [x] Evidencias reais E06/E07