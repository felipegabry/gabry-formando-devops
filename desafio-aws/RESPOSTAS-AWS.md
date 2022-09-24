# Desafio AWS

## 1 - Setup de ambiente

Execute os mesmos passos de criação de ambiente descritos anteriormente, ***porém atenção:*** dessa vez utilize o arquivo "formandodevops-desafio-aws.json"

```
export STACK_FILE="file://formandodevops-desafio-aws.json"
aws cloudformation create-stack --region us-east-1 --template-body "$STACK_FILE" --stack-name "$STACK_NAME" --no-cli-pager
aws cloudformation wait stack-create-complete --stack-name "$STACK_NAME"
```
#### 1-R = Ambiente "deployado"

## 2 - Networking

A página web dessa vez não está sendo exibida corretamente. Verifique as **configurações de rede** que estão impedindo seu funcionamento.

#### 2-R = O Security Group atachado na instância estava com o range de portas (81 - 8080) abertos para o endereço (0.0.0.0/1)

<img src="/desafio-aws/Prints/2.png"/>

## 3 - EC2 Access

Para acessar a EC2 por SSH, você precisa de uma *key pair*, que **não está disponível**. Pesquise como alterar a key pair de uma EC2.

Após trocar a key pair

1 - acesse a EC2:
```
ssh -i [sua-key-pair] ec2-user@[ip-ec2]
```

2 - Altere o texto da página web exibida, colocando seu nome no início do texto do arquivo ***"/var/www/html/index.html"***.

#### 3-R = Criei um par de chaves chamado key-desafio-aws e criei uma outra instância Linux, desliguei a instância atual e removi o disco root. Anexei o disco root na nova instância para poder acessá-lo e alterar a chave pública no authorized_keys. Após isso eu desliguei, devolvi o disco para a instância do desafio e apaguei a instância usada para a operação.

#### 3.1-R = Abri a porta 22 no Security Group e me conectei na instância com a chave key-desafio-aws.
<img src="/desafio-aws/Prints/3.1.png"/>

#### 3.2-R = Alterei o arquivo ***"/var/www/html/index.html"*** como foi solicitado.
<img src="/desafio-aws/Prints/3.2.png"/>

## 4 - EC2 troubleshooting

No último procedimento, A EC2 precisou ser desligada e após isso o serviço responsável pela página web não iniciou. Encontre o problema e realize as devidas alterações para que esse **serviço inicie automaticamente durante o boot** da EC2.

#### 4-R = chequei o serviço httpd com `systemctl status httpd.service`, startei o serviço com `systemctl start httpd.service` e ativei o serviço para que fosse inicializado com a instância com `systemctl enable httpd.service`, após isso o serviço voltou a funcionar.
<img src="/desafio-aws/Prints/4.png"/>

## 5 - Balanceamento

Crie uma cópia idêntica de sua EC2 e inicie essa segunda EC2. Após isso, crie um balanceador, configure ambas EC2 nesse balancedor e garanta que, **mesmo com uma das EC2 desligada, o usuário final conseguirá acessar a página web.**

#### 5-R = Criei uma nova instância na AZ2, utilizando uma imagem da instância do desafio. Criei um Target Group com as duas instâncias e anexei a um Application Load Balancer que criei.
<img src="/desafio-aws/Prints/5.png"/>


## 6 - Segurança

Garanta que o acesso para suas EC2 ocorra somente através do balanceador, ou seja, chamadas HTTP diretamente realizadas da sua máquina para o EC2 deverão ser barradas. Elas **só aceitarão chamadas do balanceador** e esse, por sua vez, aceitará conexões externas normalmente.

#### 6-R = Criei um novo Application Load Balancer utilizando o Target Group que eu havia criado no passo anterior, dessa vez com um Security Group só dele, com a porta 80 aberta para o range 0.0.0.0/0. Editei o Security Group das instâncias, abrindo a porta 80 apenas para o Security Group do APPLB, dessa forma, não foi possível acessar via HTTP os servidores, apenas foi possível acessar o APPLB.
#### Target Group
<img src="/desafio-aws/Prints/6.1.png"/>

#### Application Load Balancer
<img src="/desafio-aws/Prints/6.2.png"/>

#### Instâncias e IP's
<img src="/desafio-aws/Prints/6.3.png"/>

#### Tentativa de acesso direto nas instâncias
<img src="/desafio-aws/Prints/6.4.png"/>
<img src="/desafio-aws/Prints/6.5.png"/>

#### Tentativa de acesso pelo Application Load Balancer
<img src="/desafio-aws/Prints/6.6.png"/>

#### Security Group das Instâncias
<img src="/desafio-aws/Prints/6.7.png"/>

#### Security Group do Application Load Balancer
<img src="/desafio-aws/Prints/6.8.png"/>
