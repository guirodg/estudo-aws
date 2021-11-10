# Delete usando MFA 
    O que é MFA ?
    Mode de autenticação via outro dispositivo

    Quando vamos apagar um arquivo no S3, temos a opção de habilitar o MFA
    
    Regra para habilitar o MFA
    Precisamos habilitar o versionamento no bucket.

    Apos ter habilitado o MFA, podemos apagar permanentemente uma versao de um objeto ou suspender o versionamento do bucket

    MFA Só pode ser habilitado e desabilitado pelo owner do bucket
    Só pode ser habilitado pela CLI

# Logs de Acesso
    Qualquer acesso consigo adicionar o logs.

    Segue o link abaixo com informaçoes sobre o que pode ser armazenado nos logs
    https://docs.aws.amazon.com/AmazonS3/latest/userguide/LogFormat.html

    LOGS DE ACESSO SEMPRE ADICIONAR EM OUTRO BUCKET

    Ferramenta para analisar log Athena

# Replicação Objetos S3
    Data centers em Sao Paulo, Campinas 
    quando criamos um bucket vai a informação vai para uma dessas regioes 

    Pra fazer replicação precisa habilitar o versinamento antes de habilitar a replicação

    Conseguuimos reclicar o bucket para uma outra conta!
    Não faz replicação de delete
    Não faz replicação encadeada

# S3 pre-signed URLs
    Permite criar uma url pre-assinadas(possui um token de acesso valido temporario) para acessar arquivos
        - Para download (é bem facil e é possivel criar usando CLI/SDK)
        - Para upload (É mais dificil, melhor usar o SDK)

    Essa url dura 3600 segundos (1 hora), mas pode ser alterado usando o argumento --expires-in

# S3 Classes de Armazenamento (Storage Classes)
    Quando guardamos o arquivo, aws disponibiliza alguns serviços

    Standard
    Alta durabilidade, dos objetos entre varias zonas de disponibilidade

    Infrequent Access (IA)
    Mais barato, uso dele para arquivo que nao são usados muito ... caso de use backup

    One zone Infrequent Access
    Fica disponivel em uma zona apenas 

    Intelligent Tiering
    Monitora os arquivos e faz essa "distribuicao para startard ou IA"

    Glacier
    Baixo custo ... Arquivos quase não usados

    custo adicional ao recuperar arquivo .. 
    modos : Expedited, Standard, Bulk.