---
marp: true
theme: dracula
paginate: true
footer: 'jose.carlos@globant.com'
style: |
  table {
    font-size: 0.68em;
  }
  p {
    font-size: 0.75em;
  }
  ul li {
    font-size: 0.9em;
  }
  blockquote {
    font-size: 1.5em;
  }
---
# Entendendo Containers
#### primeiros passos para começar no mundo de Cloud

---
## Primeiro: O que eles podem me oferecer?

* Quando falamos de containers no atual estado da arte:
  * São efêmeros, portanto, você pode parar a execução de um container e iniciar outro muito rapidamente;
  * Facilitam o versionamento de aplicações por meio de um sistema de tagging e layers;
  * Tornam mais simples o escalonamento de aplicações;
  * Permite processos de CI/CD muito mais leves (o que é um pouco mais complicado se estivéssemos falando de virtualização).

---
## Como se parece um container?

```sh
# Baixar a imagem
docker pull node:20-slim

# Executar o container com base na imagem
docker run --rm -td node:20-slim

# Listar os containers em execução
docker ps

# Acessar o shell do container em execução
docker exec -it <container-id> /bin/bash

```

---
# Sobre o exemplo anterior

```sh
docker run --rm -d node:20-slim
```

Se você executar o container sem a opção `-t` (que aloca um terminal interativo), seu container NodeJS vai executar e sair com o fim do processo principal, pois ele não tinha nenhum ponto de entrada além do próprio `REPL` para executar, e então com o fim do processo, o container também é encerrado.

---

# Como surgiu?

* Reaproveitando ferramentas e recursos já existentes no ecossistema linux;
* Surgiu o Linux Containers (LXC); e só então o Docker;
* Em 2015, foi criado o Open Container Initiative (OCI)
  * Um grupo com várias empresas que decidiram pôr ordem na casa
    * Objetivo: Padronizar e tornar os componentes que fazem containers funcionarem, mais portáveis;
    * OCI Image Specification, é a especificação do formato das imagens;
    * `runc` (usado por Podman, e Kubernetes) e `container.d` (usado pelo Docker), são exemplos de implementações desse padrão.
      * Veja: `docker inspect <container-id>`


---

# Que fundamentos são esses?


| Recurso/Ferramenta | O que faz? |
| ------------------ | ---------- |
| chroot             | Restringe a visão de um processo do sistema de arquivos           |
| unshare            | Cria namespaces com visão restrita dos sistemas (árvore de processos, interface de rede, e pontos de montagem)           |
| nsenter            | Permite reutilizar namespaces (o que abre margem para reaproveitar a mesma rede, como Pods fazem em Kubernetes)           |
| bind mounts        | Criar pontos de montagem entre o host e o chroot permitindo injetar arquivos, ou diretórios, ou então servem como pontes para armazenamento no host           |
| cgroups            | Grupos de controle. Basicamente permite impor isolamento de recursos como CPU e memória           |
| capabilities       | É um recurso que possibilita definir permissões que um root/user terão           |

---
# Deixa Eu mostrar um negócio proceis

Olha como fica o `chroot`

```sh
# Executo o chroot
sudo chroot rootfs /bin/bash
# Crio ponto de montagem para meu proc
mount -t proc proc /proc
# Executo um top
top

```

---
# Agora veja isso

Vou executar o `chroot` dentro do namespace do PID pra isolar os processos

```sh
# Crio o namespace para o PID, e executo o chroot
sudo unshare -p -f --mount-proc=$PWD/rootfs/proc \
    chroot rootfs /bin/bash
# Agora executo o top
top
# Posso executar o python que temos baixado aqui
/usr/bin/python -c 'print "Uai, parece que to em um container"'

```

---

# Se Eu fosse resumir, diria que...
> Um Container é uma Matrix para uma aplicação

E é isso que diferencia um container de um máquina virtual.

**>>> Uma máquina virtual executa um novo kernel possivelmente em outra arquitetura; um container reaproveita o kernel e a arquitetura**

---
# Algumas referências

- https://opencontainers.org/
- https://ericchiang.github.io/post/containers-from-scratch/

## Recomendo muito os canais:

- [@TechWorldwithNana](https://www.youtube.com/@TechWorldwithNana)
- [@LinuxTips](https://www.youtube.com/@LinuxTips)