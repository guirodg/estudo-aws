# O que é um Storage

    - Data Storage é um local onde você pode guardar um dado para ser usado posteriormente.

    - Existem três formas principais de armazenamento de dados:
        .1 Armazenamento de Arquivos
        .2 Armazenamento em Bloco
        .3 Armazenamento de Objetos

## Armazenamento de Arquivos
    Os Dados são armazenados em arquivos e pastas.
    Geralemente usamos HDs, PenDrives.
    Mas também pode ser usado um servidor de armazenamento de arquivos e usado
    atraves da rede.

## Armazenamento de Blocos
    Os dados são armazenados em blocos em um servidor de storage.
    Outras maquinas acessam os arquivos contidos nesses blocos.
    A comunicação é feita através da rede, usando o protocolo ISCSI

    Comumente usado por Hypervisors para armazenar sistemas operacionais das máquinas virtuais  

## Armazenamento de Objetos
    Os dados são armazenados como objetos com metadados e identificadores únicos.

    Geralemente é mais barato, mas é idela para dados que não precisam ser editados.
    Qualquer arquivo é suportado. 
    É altamente escalável

## AWS Serviços de Storage 
    .1 EBS (Elastic Block Storage)
    .2 EFS (Elastic File System)
    .3 S3 (Simple Storage Service)

## EBS - Elastic Block Storage 
Quando uma instância EC2 é criada, um volume EBS é criado para o sistema operacional. <br>
*Considerado como um driver de rede "um pendrive"*

Precisamos definir o tamanho do EBS, pagamento feito pelo tamanho definido!

### ESB Tem 4 tipos de volumes
    .1 GP2 Volume SSD para uso geral. Bom equilibrio entre preço e desempenho e é suficiente para a maioria dos trabalhos

    .2 IO1 Volume SSD de alta perfomace, usado para sistemas de alta criticidade e baixa latência ou alta taxa de transferencia

    .3 ST1 Volume HDD com alta taxa transferência
    .4 SC1 Volume HDD de baixa performance e baixo custo
    - Quando um volume é criado, é necessário definir Tamanho, Taxa de transferência, IOPS

# EBS Tipos de volume GP2
    GP2
    - Recomendado para a maioria dos trabalhos
    - Usado como System Boot
    - Aplicativos com volume de acesso considerável e Baixa latencia
    - Ambiente de teste e desenvolvimento
    - Suporta entre 1GB e 16TB
    - Volumes pequenos podem ter um burst de 3000 IOPS
    - Maximo de IOPS é 16k
    - A Aws provisiona 3 IOPS por Giga. Ou seja, com 5.3TB, ja temos o maximo de IOPS

    
# EBS Tipos de volume IO1
    IO1
    - Aplicações de alta criticidade e que necessitam de desempenho de IOPS, garantido, ou que precisem de mais de 16K IOPS por volume (limite do GP2)

    - Carga monstruosa de banco de dados, como:
    - MongoDB
    - Cassandra
    - SQL Server
    - Mysql
    - Postgree
    - Oracle

    Varia entre 4GB e 16TB
    - IOPS é constante, segundo o que foi provisionado
    - Minimo de 100 e maximo de 64mil para instancias Nitro
    - Maximo de 32mil para as demais instancias

# EBS Tipos de volume SC1
    - Aplicações que tem uma taxa de transferencia muito alta mas os dados nao sao acessados com frequencia, ex: Logs, arquivo morto

    - Cenarios onde o custo baixo e o mais importante
    - Nao pode ser volume de boot

    500GB e 16TB 
    - Maximo de IOPS e 250
    - Maximo throughput é 250MB/S

# Instance Store
    - Algumas instancias nao vem com um volume EBS, ao inves disso, elas vem com uma instance Store. Um storage efemero.

    - A instance Store é um disco fisico atachado na VM. (EBS é um drive de rede)

    -Pros: 
        Melhor desempenho de I/O
        Execelente para buffer/cache/processamento de arquivos locais
        Dados sobrevivem reboot
    -Contras:
        No stop e termination da instancia, os dados sao perdidos
    
    - IOPS Altissimo(de35K a 1.6 milhoes)
    - Suporta Block Storage
    - Disco podem ter até 7.5TB
    - Discos não podem ser aumentados
    - Risco de perda de dados se o hardware falhar

# EFS - Elastic File System
*"Drive de rede, exclusivo pro linux"*

1. Podemos ter varios EC2 Conectado no mesmo.
2. Funciona com instancias em mais de uma zona de disponibilidade
3. Alta Disponibilidade
4. Escalavel
5. Caro(3x o preço do EBS GP2)

## Caso de Uso
 1. Sistemas CMS(Content Management System), Websites, Compartilhamento de dados, wordpress
 2. Precisa de um security group para acessar o EFS
 3. Compatível somente com Linux. (Windows não)
 4. Criptografia "at rest" usando KMS
 5. Sistema de arquivos escala automaticamente, pay-per-use. Nao precisa planejar a capacidade
 6. Suporta criptogradia em transito usando o mount-helper

 ### Escalonamento
 1. Suporta milhares de cliente NFS, taxa de transferencia de 10GB/s
 2. Pode chegar a uma tamanho de petabyte, automaticamente
 ### Modos de performance
 1. General Purpose(default): Casos de uso de baixa latencia(webserver, CMS, etc)
 2. Max I/O: Maior latencia, mas melhor paralelismo, dando maior taxa de transferencia(Big data, processamento de grandes arquivos)
 ### Niveis de Storage(feature de gestao de ciclo de vida)
1. Standard: Usado para arquivos acessados com frequencia
2. infrequent Access(EFS-IA): Demora mais para recuperar o arquivo, mas é mais barato

<br></br>
# EBS vs EFS

## EBS
    - Volumes EBS so podem ser anexados em uma instancia por vez
    - Sao limitados a uma zona de disponibilidade
    - Gp2: IO aumenta se o disco aumenta(depois de 5.3GB nao faz diferenca)
    - IO1: POde aumentar o IO independentemente do volume

    Para Migrar um EBS para outro AZ
    - Tirar um snapshot
    - Restaurar o snapshot em outro AZ
    - Recomendado(mas nao obrigatorio) fazer isso com a instancia parada

    EBS Raiz sao eliminados quando a instancia e iniminada
    Voce paga pelo que foi provisionado independente do uso
    
## EFS    
    - Pode ser montado em milhares de instancias distribuidas em varias Azs
    - So pode ser usado no linux
    - Recomendado para sites de gerenciamento de conteudo ou processamento de grandes arquivos (big data, processamento de imagens/videos)

    - Muito mais caro que o EBS(3x)
    - Você paga somente pelo que usar
