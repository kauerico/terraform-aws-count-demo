# Estudo realizado

## Objetivo 
O objetivo desse repositório é realizar um estudo em cima de um ptrojet oficiar do terraform que utiliza o Meta-argumetns Count para construir um infraestrutura completa na AWS. O foco aqui vai ser como o Terraform Count trabalha. 


# Meta-arguments Count Terraform
O meta-arguments count é um recurso na Terraform para vc gerenciar multiplos blocos de recursos ou modulos com um numeri especifico de incrementação. O ideal é usar ele quando tem varios recursos quase iguais.


## Recursos a serem criados na AWS
* VPC
* Load Balancer
* Instâncias EC2

Onde esta agindo o Count? 
Primeiramente vamos entender qual recurso nesse projeto vai usar o Count, dando um spoiler é as Instâncias EC2. Mas como é usado, nesse projeto temos dois tipos de APPs que vão nas instãncias.
o app_a: 
```
resource "aws_instance" "app_a" 
```

e a app_b: 
```
resource "aws_instance" "app_b"
``` 

e porque tem duas? 
algumas vão receber subnets privadas e outra públicas, 


# **Referências**
https://developer.hashicorp.com/terraform/language/meta-arguments#count
https://developer.hashicorp.com/terraform/tutorials/0-13/count
https://github.com/hashicorp-education/learn-terraform-count