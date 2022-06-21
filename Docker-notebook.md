# Docker

---

“Docker is the ideal platform to run applications in containers” - Jefferson Fernando - Descomplicando o Docker.

---

###### Livro: Descomplicando o Docker

## O que é Container?

Trata-se do agrupamento de uma aplicação junto às suas dependências que compartilham o kernel do sistema operacional em que está rodando (host), seja em uma máquina virtual ou física.

São mais leves do que máquinas virtuais, embora tenha algumas semelhanças. A principal diferença é que uma VM vai virtualizar o hardware e emular um segundo sistema operacional, o que faz com que a máquina host tenha que gastar mais recursos. 

Os containers utilizam os recursos já compartilhados pelo host. Não é necessário emular um novo sistema operacional. 

![](1.png)

Outro fator interessante é a **portabilidade**. Não importa em qual ambiente o seu container foi criado, ele irá rodar em qualquer outro que possua, no caso, o docker instalado. Seja Linux, MacOS ou Windows. Todas as dependências estarão dentro do container.

O desenvolvedor consegue criar uma aplicação em container dentro da sua própria máquina e depois executá-la em um servidor de produção sem nenhum problema de dependência ou algo do tipo.

---

###### Curso: Descomplicando O Docker 

## O que É um Container?

### Isolamento

- Forma Lógica:
  - O seu container em servidor está rodando em um servidor de maneira isolada. Como isolar uma parte da máquina especificamente para um container, de modo que os processos, usuários, redes, endpoints etc são compartilhados entre o host e este próprio container. Isso evita que haja conflitos entre containers.
  - Namespaces

- Forma Física:
  - Podemos limitar/isolar recursos de hardware a determinados containers. Memoria, CPUs, Input/Outputs etc.
  - Gerenciamento: Cgroups

→ Isolamento!

---

O Docker te ajuda a administrar containers, e deixou os containers mais famosos, mas ele não é o único, tampouco o primeiro.

- Chroot - O avô do container - primeira forma de isolar um pedaço do disco do filesystem para usuário/serviço;
- LXC - Linux Container - O docker foi baseado no LXC, uma forma de executar containers no linux
- FreeBSD Jail - Uma forma de criar multiplos ambientes virtuais
- Solaris Containers/ Zones 

O Docker trouxe uma maior facilidade, também trouxe a ideia de clusters.

---

### Docker

Em meados de 2013, Solomon Hykes, pega o core do dotCloud (paas) e abre para a comunidade, batizando como docker no github, e vira um sucesso absoluto, com desenvolvedores do mundo todo atuando no projeto. 
Iniciou como vários scrips em LXC etc. Hoje, é impossível comparar.

---

## O que é o Docker?

Vamos ver um pouco mais sobre imagens mais para frente, mas vamos falar sobre alguns tópicos importantes:

**Camadas** → As imagens possuem várias camadas. Cada instrução cria uma camada, e não é legal que tenhamos uma instrução RUN que faça várias tarefas, pois isso vai gerar outras várias camadas. O problema de criar camadas é que, quando estamos executando um container, apenas a última camada é de READ/WRITE. Todas as demais são somente Leitura.

Neste caso, se você precisar sobrescrever um caracter que seja em algum arquivo que está abaixo, o arquivo vai ser copiado para a última camada para que aí sim seja feita a sobrescrição. Ou seja, vamos ter uma copia desta camada. O que antes era 100mb (por exemplo), agora serão 200mb.

Ou seja, as alterações serão em uma cópia do arquivo, e não no arquivo em si. 

Porque o Docker faz isso? Por que copiar o arquivo para que eu possa editar?  

Todos os containers em execução estarão sob a MESMA imagem. Eles podem compartilhar todas as camadas, e mudar apenas a última, que é em tempo de execução (contendo logs etc).

---

Imagine 5 container, os 5 com o apache instalado e esta instalação ocupando 500mb. Ora, se temos 5 containers usando o apache, cada um com 500mb, então temos 2.5GB, certo? 
Errado!
O container vai usar a mesma imagem. Esta imagem será read only. 

---

## O que é Docker - parte 2

Quando estamos mexendo em uma VM, cada uma tem o seu Kernel. O container utiliza o kernel do próprio host. 

Dentro do kernel do linux existem diversos modulos necessários para que tudo funcione. O iptables, por exemplo, que cuida de toda parte de redes, redirects dos containers etc. Os namespaces, cgroups, clinux etc, tudo isso o docker utiliza do kernel linux.

### Pra que usar?

Pro desenvolvedor, é muito bacana ter todas as aplicações rodando ali na maquina local e rapidinho subir tudo, do mesmo jeito, pra produção. 

O mesmo container pode ir para diversas áreas, dev, QA, prod. 

O deploy fica muito mais tranquilo, não importando qual é a linguagem utilizada.

Pro dono do negocio, ele vai ganhar sinergia entre dev e adm. Mais agilidade no processo, economizar dinheiro reduzindo a quantidade de recursos utilizados quando em comparação com VMs. 

---

`docker version` - Versão do Docker

`docker container ls` - Mostra os containers em execução

`docker container run -ti <nome-da-imagem>`  - Cria um container caso este ainda não exista. 

1. O docker client se comunica com o Docker Deamon, dizendo que quer utilizar aquele container.
2. O docker faz o download da imagem (pull), caso ela não exista no host. Se ela já estivesse no host, ele apenas iria acessá-la. 
3. O Docker deamon criou um novo container
4. O deamon mandou o output, a mensagem impressa na tela.

`docker container ls -a` - Mostra todos os containers, que estão ou não em execução.

`docker container run -ti ubuntu` - Baixa a imagem ubuntu e se conecta

`-ti` - Pede para o docker executar um container já com um terminal para que a gente interaja com ele. Quando subir, já estaremos concectados nele.

`ps -ef` - Processos em execução no linux

`ctrl + D` - Encerra e sai do container e volta pro host.

`ctrl + p + q` - sai do container sem matá-lo.

**Entrepoint** - É o principal processo do nosso container. Quando esta função se encerra, o docker encerra este container. No caso do Ubuntu, o container que abrimos, o Enterpoint é o próprio bash.

→ Quero voltar para o container que ficou em execução, como fazer?

Attach → `docker container attach 'containerID ou nomeDoContainer'`

---

#### Rodando o nginx

Podemos rodar a imagem do nginx. Note que ao dar o comando `docker container run -ti nginx`, usando `-ti` para entrar no modo Interatividade, o nosso terminal é ‘travado’, certo? Não dá pra fazer nada. Isso acontece porque o bash não é o entrypoint do nginx. Precisamos fazer ele rodar como um Deamon, usando o `-d`.

`docker container run -d nginx` - Pra deixar o nginx rodando como deamon

`docker container exec -ti idDoContainer 'comando que tu quer executar'`.
`d` Exemplos:

```bash
docker container exec -ti <idDoContainer> ls /usr/share/nginx/html
docker container exec -ti <idDoContainer> bash
docker container exec -ti <idDoContainer> 
```

Ao dar o comando `exit`, podemos ver que a imagem do nginx não morreu. Porque? Porque o bash não é o principal entrypoint aqui. Eu pedi para executar o bash, apenas. Ele ta rodando como um deamon, ou seja, não rodar ele em primeiro plano, ali no seu terminal. 

Na maioria das vezes, vamos subir aplicações com `-d`, porque geralmente a aplicação já vai estar rodando, então nao precisamos ter qualquer interatividade com ela. 

---

> ### Anotações SemanaDEVOPS Day 1 - Docker e Containers
>
> - Containers não foram feitos para morrer! Eles podem morrer, mas precisam já estar prontos para subir em outro lugar.]
>
> ---
>
> ### Imagem
>
> Nada mais é do que uma .iso. 
>
> A primeira coisa que precisamos ter é um “sistema operacional”, uma imageBase. A partir de onde nós vamos começar? Podemos usar uma imagem pronta do nginx.
>
> Definimos usar o debian. Então agora precisamos instalar o nginx.
>
> Uma camada para adicionar os arquivos do meu site.
>
> Precisamos de mais uma camada para configurar o nosso site. 
>
> Temos 4 steps, e juntos eles representam uma imagem de container. Imagine que tem o nome z-web:1.0.
>
> Quando alguém quiser fazer o mesmo site, mudando algumas configurações apenas, basta pegar a imagem e zas!
>
> ---
>
> Sabemos que apenas a primeira (ultima) camada é read and write. As outras são read only. Isso é importante para manter a segurança de todos os containers que estão utilizando aquelas imagens também. 
>
> ---
>
> → FROM - Quem chama a imagem
> → Alpine - Imagem base disponíveis na comunidade.
> → dockerhub - Interessante.
>
> - Podemos utilizar imagens como base para nossas aplicações. É importante ler a documentação e o release delas para ficar a par de problemas de segurança e não colocar a perder o seu sistemas aí.
>
> ---
>
> - Nunca faça um container que junta muitos processos. Containers não podem ser muito grandes e as responsabilidades devem ser quebradas, pois no final, os containers são grandes bibliotecas e podemos reutilizar depois. 
>
> ---
>
> 
>
> 

---

## Docker Login, Docker Push, Docker Pull e o DockerHub | Descomplicando o Docker V1 - parte 12

## DockerHub

Um repositório público e privado mantido pela Docker. É onde podemos disponibilizar imagens e serve como se fosse o github. Pra quê?

Muitas empresas compartilham imagens oficiais na DockerHub. E pessoas comuns também sobem imagens pra lá.

Que imagens são essas? Muitas vezes precisamos de uma imagem pronta com configurações que precisamos etc, e é possível encontrar lá. 

---

Antes de baixar qualquer imagem podemos dar o comando `docker inspect 'nome da imagem' -f` para sacar os detalhes da imagem.

`docker history 'nome da imagem'`  nos mostra detalhes sobre a construção deste container.

É legal priorizar imagens que já possuem o docker file. Com o dockerfile nem precisamos dar pull na imagem, basta rodar o docker file ali e vamos ter ainda mais controle sobre esta imagem, as configurações etc. 

### Bora Subir essa imagem!!!

Nome: Antes o nome era apenas um nome:versão. Pra subir pro dockerHub, precisamos também do nome do usuário. `isaiaszanoni/webserver:1.0` (`usuario/nomeDaImagem:versao`).

Como mudar o nome de uma imagem já existente? `docker tag 'imageId' usuario/novoNome:1.0` 

Como apagar uma imagem na minha máquina? `docker rmi -f isaiaszanoni/hello-world:1.0`

Baixar a imagem do dockerHub - `docker pull isaiaszanoni/hello-world:1.0`

---

### Voltando pra aula do Descomplicanto o Docker...

\- Quero conectar novamente em um container em execução → `docker container attach 'ContainerID' ou 'NomeDoContainer'` 
\- `container ls` ou `docker ps` → Mostra os containers em execução
\- 

Vamos pegar uma imagem do nginx
`docker container run -ti nginx`

Beleza! Ficou tudo travado, porque? 
A nossa imagem do nginx não tem um entrypoint bash, por exemplo. E quando utilizamos o comando `-ti` estamos dizendo que queremos interatividade, mas só temos o próprio processo do nginx. Ele não pode estar rodando como um deamon, precisa estar rodando em primeiro plano!

Precisamos conectar com o nginx com o comando `-d`, que pede para que ele seja executado como deamon e não com interatividade `-ti`.
`docker container attach 'nomeDoContainerNginx'` → temos: O mesmo problema! Vamos para o próprio processo do nginx. 

`docker container exec -ti 'idDoContainer' 'comando'` 

```yaml
// já passando o -ti, nome/codigo do container e o comando que quero executar nele.
docker container exec -ti 'idDoContainer' ls /
docker container exec -ti 'idDoContainer' bash
ctrl + d // fecha o bash. 

```

Ao fechar o bash o container não morre, pois o principal entrypoint dele é o nginx, que não vai morrer nisso. 

#### Comando `-d`

É muito comum que a gente rode aplicações em segundo plano, ou como deamon. Muitas vezes nossa aplicação vai estar rodando e não vamos precisar interagir diretamente com ela.

---

---

# Brincando com o que vimos

- Crie um container chamado `hello-world`
  - `docker container run -ti hello-world`
- Como ver se o container que você acabou de criar está em execução?
  - `docker container ls -a` - Vai mostrar os containers criados com id, nome da imagem, tempo de criação, status, portas em uso e o nome. O que acabamos de criar está com o status Exited. Foi finalizado pois o seu entrypoint era apens a mensagem de inicio do docker.
- Descubra se você tem um container em execução. Se sim, entre nele.
  - `docker container ps`
  - `docker container run -ti 'containerId'`

---

## Executando e administrando containers Docker - Parte 2

`docker container stop <idDoContainer>` - para stopar um container.

`docker container start <idDoContainer>` - para iniciar um container. Ele vai abrir lá em modo deamon.

`docker container restart <idDoContainer>` - para reiniciar um container.

`docker container pause <idDoContainer>` - pausa um container. Também podemos usar o `unpause`.

---

`docker container inspect <idDoContainer>`  - retorna tudo quanto é informação sobre o container. 

---

`docker container rm  idDoContainer` - remove, mata um container. Você pode usar o `-f` no final do comando, para forçar matar um container ainda em execução.

----

## Configurando CPU e Memória para os meus containers

`docker container stats idDoContainer` - para ver o quanto o container está usando de recursos.

Retorna id, nome, uso de CPU, uso de memoria e limite de memoria, uso de memoria em %, internet I/O, Disco I/O e PIDs;

---

Criamos um novo nginx com o `-m` ou `--memory`. Este comando seta um maximo de memoria.
`docker container run -d -m 128M nginx`.

Podemos utilizar o comando `docker container update --cpu`

---

`docker container rm <id>` - para e remove um container
`docker container rm -f <id>` - para e remove um container mesmo que esteja em execução através do `force`

---

`docker image ls` - lista as imagens que nós utilizamos.

---

### Docker file tosko

- Criamos um dockerfile

```dockerfile
FROM debian

LABEL app="Giropops"
ENV ISAIAS="dev"

RUN apt-get update && apt-get install -y stress & apt-get clean

CMD stress --cepu 1 --vm-bytes 64M --vm 1
```

- Precisamos fazer o build deste dockerfile `docker image build -toskeira:1.0 . `

