# Estudo realizado

## Objetivo

O objetivo desse repositório é realizar um estudo em cima de um projeto oficial do Terraform que utiliza o meta-argumento **count** para construir uma infraestrutura completa na AWS.
O foco aqui é entender como o **Terraform count** trabalha na prática.

---

## Meta-argumento `count` no Terraform

O meta-argumento `count` é um recurso do Terraform usado para gerenciar múltiplos blocos de recursos ou módulos de forma dinâmica, com base em um número definido.
Ele é ideal quando precisamos criar vários recursos parecidos sem precisar repetir código.

---

## Recursos a serem criados na AWS

* VPC
* Load Balancer
* Instâncias EC2

---

## Onde o `count` é usado

Nesse projeto, o `count` será aplicado nas **instâncias EC2**.

Antes, existiam dois blocos de instâncias:

```hcl
resource "aws_instance" "app_a" { ... }
resource "aws_instance" "app_b" { ... }
```

Essas instâncias eram divididas entre subnets públicas e privadas, dependendo da necessidade.
Mas essa abordagem não é prática, já que cada instância precisa ser declarada manualmente.

---

## Refatorando com o `count`

Vamos simplificar o código deixando apenas **um bloco de recurso** para as instâncias EC2:

```diff
- resource "aws_instance" "app_a"
+ resource "aws_instance" "app"
```

---

### Variável de controle

Criamos uma variável para controlar quantas instâncias EC2 serão criadas por subnet privada:

```hcl
variable "instances_per_subnet" {
  description = "Número de EC2 por subnet privada"
  type        = number
  default     = 2
}
```

---

### Aplicando o `count`

Agora adicionamos o `count` dentro do bloco da EC2:

```hcl
resource "aws_instance" "app" {
  depends_on = [module.vpc]

  count = var.instances_per_subnet * length(module.vpc.private_subnets)

  ami           = data.aws_ami.amazon_linux.id
  instance_type = var.instance_type

  subnet_id              = module.vpc.private_subnets[count.index % length(module.vpc.private_subnets)]
  vpc_security_group_ids = [module.app_security_group.security_group_id]
}
```

Aqui o `count.index` é usado para distribuir as instâncias de forma balanceada entre as subnets privadas.

---

## Ajustando o Load Balancer

Também é preciso ajustar o módulo `elb_http` para reconhecer todas as instâncias criadas dinamicamente.

Antes:

```hcl
number_of_instances = 2
instances           = [aws_instance.app_a.id, aws_instance.app_b.id]
```

Depois:

```hcl
number_of_instances = length(aws_instance.app)
instances           = aws_instance.app.*.id
```

Assim, o Load Balancer passa a usar automaticamente todas as instâncias geradas pelo `count`.

---

## Referências

* [https://developer.hashicorp.com/terraform/language/meta-arguments#count](https://developer.hashicorp.com/terraform/language/meta-arguments#count)
* [https://developer.hashicorp.com/terraform/tutorials/0-13/count](https://developer.hashicorp.com/terraform/tutorials/0-13/count)
* [https://github.com/hashicorp-education/learn-terraform-count](https://github.com/hashicorp-education/learn-terraform-count)