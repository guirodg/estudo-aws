# Serviços de Storage - S3

## O que é o S3
É um servico para armazenar e recuperar qualquer quantidade de dados, a qualquer momento, de qualquer lugar na internet.

Possui o minimo de features para se manter a simplicidade

## Criação de Buckets(baldes)
Te permite criar e nomear um bucket para armazenar dados. Buckets são os containeres, fundamentais para o serviço.

## Armazenando Dados
Te permite armazenar um volume infinito de dados em um balde. Subir quantos objetos quiser. Cada objeto pode ter até 5TB.

Cada objeto é armazenado e  recuperado usando uma chave.

## Download
Te permite baixar os dados e permitir que outros o façam.

## Permissões
Conceda ou revogue permissoes para outros que queiram subir ou baixar dados no seu Bucket.

## Interface Padrão
Usando protocolos REST e SOAP para poder ser usado em qualquer lugar da internet

# Conceitos - Buckets
- Buckets sao os containeres fundamentais da aplicação
- Buckets precisam ter um nome UNICO GLOBALMENTE
- Buckets sao definidos em uma regiao, *Pode ser replicado para outras regioes*

## Regras para o nome
- Nao pode ter letras maiusculas 
- nao pode ter underline
- precisa ter entre 3 e 63 caracteres
- nao pode ser um IP
- precisa comecar com uma letra minuscula ou numero

# Conceitos - Objetos
- Objetos(Arquivos) possuem uma chave
- A chave é o caminho completo;
    - S3://meu-bucket/meu_arquivo.txt
    - S3://meu-bucket/meu_diretorio/meu_subdiretorio/meu_arquivo.txt
- A chave é composta de um prefixo + nome do objeto
    - S3://meu-bucket/meu_diretorio/meu_subdiretorio/meu_arquivo
- Não existe o conceito de "diretórios" dentro dos buckets(A interface pode te fazer pensar o contrario)
- Existem chaves com nomes muito grandes e que contrem barras("/")
- Voce pode pesquisar arquivos comecando por um prefixo
    - Meu_diretorio/*

- Os valores dos objetos sao o conteudo do corpo
    - Tamanho maximo = 5TB
    - Limite de 5GB por upload. Caso seja necessario, deve-se usar o "multi-part upload"

- Todos os objetos contem Metadados(Lista de chaves/valor em formato de texto)
    - alguns sao criados pelo sistema, outros pelo proprio usuario

- Suporta Tags(até 10)

- Possui um ID de versao(se o versionamento estiver ligado)

# Versionamento
- O S3 suporta versionamento de objetos
- É habilitado para o Bucket
- Quando um upload é feito com a mesma chave, o servico incrementa a versao: 1,2,3 ..
- É considerada uma boa pratica versionar os arquivos para evitar deleção acidental
    - permite restaurar um objeto deletado;
    - permite fazer rollback para uma versao anterior do arquivo

## Notas: 
- Arquivos pre-existentes ao momento em que o versionamento foi habilitado, ficam com a versao com o valor "null"
- Desabilitar o versionamento nao apagas as versoes anteriores

# Criptografia
- Existem 4 fromas de criptografar objetos no S3
    - SSE-S3: Criptografa os objetos usando chaves que sao gerenciadas pelo proprio S3
    - SSE-KMS: Usa o serviço AWS Key Management Service para gerenciar as chaves de criptografia
    - SEE-C: Você gerencia as chaves de criptografia
    - Client Side Encryption: Você ja envia o objeto criptografado
- É extremamente importante entender a diferenca entre eles e qual usar em qual situação

## SSE-S3
    - SSE-S3: Criptografia usando chaves que sao gerenciadas pelo S3
    - Os objetos são criptografados no servidor
    - Tipo de criptografia AES-256
    - Precisa enviar no header "x-amz-server-side-encryption":"AES-256"

## SSE-KMS
    - SSE-KMS: Criptografia usando chaves que sao gerenciados pelo Aws KMS(Key management Service)
    - KMS permite fazer controle de usuario e auditoria
    - Permite rotacionar a chave de segurança
    - A criptografia é feita no servidor
    - Precisa enviar o header "x-amz-server-side-encryption":"aws:kms"

## SSE-C
    - SSE-C: Criptografia usando chaves gerenciados pelo cliente, fora da AWS
    - O S3 não guarda as chaves de criptografia que você envia
    - A criptografia é feita no servidor
    - É obrigatorio o uso do HTTPS
    - A chave de criptografia é enviada no header de requisição

## Client Side Encryption
    - A criptografia é feita no cliente, antes de serem enviadas para o S3
    - Algumas bibliotecas te auxiliam a criptografar, como o Aws S3 Encryption Client
    - Precisa descriptografar o arquivo depois de buscar
    - O cliente gerencia totalmente as chaves e o processo de criptografia

# Segurança no S3
    - Baseado em Usuários
        - Permite criar politicas IAM - Quais apis um usuario pode executar

    - Baseado em recursos
        - Politicas de seguranca do bucket, diz quais usuarios podem ou nao executar acoes no bucket
        - Object Access Control List(ACL) - Permissoes para cada objeto
        - Bucket Access Control List(ACL) - Permissoes para todo o bucket
        
    - Um usuario IAM pode acessar um objeto SE:
    - As permissoes do usuario permitirem ou as politicas do recurso permitirem E nao haja nenhum "explicit deny"

## Politicas do Bucket
    - Politicas baseadas em JSON
        - Podem ser atribuidas a buckets ou objetos
        - Existe uma lista de API's para permitir ou negar acesso
        - Podem ser politicas de "Permitir(Allow)" ou "Negar(Deny)"
        - Principal: Conta ou usuario para aplicar a politica

    - Usos das politicas de bucket
        - Permitir acesso publico externo ao bucket
        - Forçar objetos a serem criptografados no upload
        - Permitir acesso a outras contas da aws

## Configuraçoes de bloqueio a Acesso Publico
    - Bloqueia o acesso público aos Buckets e objetos(mesmo com politicas de permissao)
        - Novos access control lists(ACLs)
        - Quaisquer access control lists(ACLs)
        - Novos acessos publicos ou access points
    
    - Bloqueia acesso publicos e de outras contas atraves de qualquer bucket ou pontos de acesso (Access point)

    - Essas configurações foram criadas para prevenir vazamento de dados

# Cors - Introdução
    - Uma ORIGEM é um esquema(protocolo)+host(dominio)+porta
        - Exemplo:https://www.exemplo.com(porta implicita 443 para https ou 80 para http)
    
    - CORS - Cross-Origin Resource Sharing(Compartilhamento de recursos entre origens)

    - Web Browsers: Por padrão, bloqueia o acesso recursos de outras origens, a não ser que a segunda origem permita

    - Mesma origem: http://www.exemplo.com/app1 e http://www.exemplo.com/app2
    - Origens Diferentes: http://www.exemplo.com e http://www.app1exemplo.com

## Headers
    - Os acessos sao liberados atraves de headers nos responses.
        -Access-Control-Allow-Origin:http://www.exemplo.com
        -Access-Control-Allow-Methods:GET,PUT,DELETE
        -Access-Control-Allow-Headers:authorization

# Cors no S3
    - Se um cliente fizer uma requisicao CORS ao seu bucket s3, é necessario habilitar os headers do CORS
    - Cai bastante essa pergunta na prova
    - Voce pode permitir uma origem especifica ou para todos(*)
    - È efinido como um JSON

# Modelo de Consistência
    Consistencia de leitura apos escrita para criacao de novos objetos(PUTObject)
        Assim que o objeto é escrito, ele pode ser lido

    Para DELERES e PUTS de objetos existentes, a consistencia é "eventual"
        Se voce ler um objeto assim que ele for substituido, voce pode receber o objeto antigo por um tempo ...

        Se voce acabou de apagar um arquivo, voce pode continuar recebendo o arquivo por um curto periodo de tempo
    
    Nao ha forma de garantir consistencia forte para update e delete

    Consistencia eventual = podemos ver objetos antigos ... apos atualizar