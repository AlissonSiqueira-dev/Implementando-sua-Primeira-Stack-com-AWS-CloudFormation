# Implementando-sua-Primeira-Stack-com-AWS-CloudFormation
Desafio DIO AWS

📋 Descrição do Projeto
Este repositório documenta a implementação da minha primeira Stack com AWS CloudFormation, desenvolvida como parte do desafio da Digital Innovation One (DIO). O projeto demonstra a aplicação de conceitos de Infraestrutura como Código (IaC) na prática, criando uma infraestrutura completa na AWS de forma automatizada e reproduzível.

🎯 Objetivos de Aprendizagem
✅ Aplicar conceitos de CloudFormation em um ambiente prático

✅ Documentar processos técnicos de forma estruturada

✅ Utilizar GitHub como ferramenta de documentação

✅ Compreender a importância da IaC no desenvolvimento moderno

✅ Implementar boas práticas de AWS

🏗️ Arquitetura da Solução
Visão Geral
text
┌─────────────────────────────────────────────────────────────┐
│                         AWS Cloud                          │
│  ┌────────────────────────────────────────────────────┐    │
│  │                    VPC (10.0.0.0/16)              │    │
│  │  ┌──────────────────────────────────────────┐    │    │
│  │  │      Subnet Pública (10.0.1.0/24)        │    │    │
│  │  │  ┌────────────────────────────────┐      │    │    │
│  │  │  │     EC2 Instance (Apache)     │      │    │    │
│  │  │  │     Security Group (80,22)    │      │    │    │
│  │  │  └────────────────────────────────┘      │    │    │
│  │  └──────────────────────────────────────────┘    │    │
│  │                     │                             │    │
│  │              Internet Gateway                     │    │
│  └────────────────────────────────────────────────────┘    │
│                         │                                  │
│                      Internet                              │
└─────────────────────────────────────────────────────────────┘
Recursos Criados
Recurso	Descrição	Configuração
VPC	Rede virtual privada	CIDR: 10.0.0.0/16
Subnet	Sub-rede pública	CIDR: 10.0.1.0/24
Internet Gateway	Conexão com internet	Anexado à VPC
Route Table	Tabela de roteamento	Rota para 0.0.0.0/0
Security Group	Firewall	Portas 80 (HTTP) e 22 (SSH)
EC2 Instance	Servidor web	Amazon Linux 2 + Apache
📦 Pré-requisitos
Antes de começar, certifique-se de ter:

bash
✅ Conta AWS ativa (Free Tier)
✅ AWS CLI configurado
✅ Git instalado
✅ Editor de código (VSCode recomendado)
✅ Conhecimentos básicos de YAML e AWS

Configuração do AWS CLI
bash
chmod +x delete-stack.sh
./delete-stack.sh
📊 Template CloudFormation
O template template.yaml define todos os recursos utilizando a sintaxe YAML:

Parâmetros
yaml
Parameters:
  InstanceType:
    Type: String
    Default: t2.micro
    Description: Tipo da instância EC2
    AllowedValues:
      - t2.micro
      - t2.small
      - t2.medium
Recursos Principais
yaml
Resources:
  # VPC com DNS habilitado
  MyVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsSupport: true
      EnableDnsHostnames: true

  # EC2 com UserData para instalação do Apache
  WebServer:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-0c02fb55956c7d316
      InstanceType: !Ref InstanceType
      UserData:
        Fn::Base64: |
          #!/bin/bash
          yum update -y
          yum install -y httpd
          systemctl start httpd
          echo "Servidor Web - Desafio DIO" > /var/www/html/index.html
Outputs
yaml
Outputs:
  WebServerPublicIP:
    Description: "IP Público do Web Server"
    Value: !GetAtt WebServer.PublicIp
💡 Insights e Aprendizados
Desafios Encontrados
Validação de Template

Importância de validar antes do deploy

Comando: aws cloudformation validate-template

Permissões IAM

Necessário configurar políticas adequadas

Uso de CAPABILITY_IAM para recursos IAM

UserData

Scripts precisam ser testados separadamente

Logs disponíveis em /var/log/cloud-init-output.log

Monitoramento

CloudWatch para logs e métricas

Stack Events para troubleshooting

Melhores Práticas
✅ Sempre validar o template antes do deploy
✅ Usar wait commands para monitorar o status
✅ Documentar outputs para fácil acesso
✅ Versionar o código com Git
✅ Testar em ambiente de desenvolvimento primeiro

Comandos Úteis
bash
# Validar template
aws cloudformation validate-template \
    --template-body file://template.yaml

# Descrever stack
aws cloudformation describe-stacks \
    --stack-name desafio-dio-stack

# Visualizar eventos
aws cloudformation describe-stack-events \
    --stack-name desafio-dio-stack

# Listar recursos
aws cloudformation list-stack-resources \
    --stack-name desafio-dio-stack

# Ver logs da instância
aws ssm start-session \
    --target [INSTANCE_ID]
🔧 Troubleshooting
Problemas Comuns e Soluções
Problema	Causa	Solução
Stack em ROLLBACK	Falha na criação de recurso	Verificar eventos e logs
EC2 sem acesso	Security Group restritivo	Adicionar regras adequadas
Apache não responde	UserData com erro	Verificar logs de inicialização
Erro de permissão	IAM insuficiente	Configurar políticas corretas
Verificação de Logs
bash
# Conectar via SSH
ssh -i [KEY].pem ec2-user@[IP_PUBLICO]

# Ver logs de inicialização
sudo cat /var/log/cloud-init-output.log

# Ver status do Apache
sudo systemctl status httpd
📈 Resultados Obtidos
Métricas de Sucesso
✅ Tempo de deploy: ~5 minutos

✅ Recursos criados: 6

✅ Custo estimado: ~$0.01/hora (Free Tier)

✅ Disponibilidade: 100% durante testes

Capturas de Tela
https://images/console-aws.png
Console AWS mostrando a stack criada

https://images/web-server.png
Página do servidor web em execução

📚 Referências e Recursos
Documentação Oficial
AWS CloudFormation

AWS CLI Reference

AWS EC2 Documentation

Materiais Complementares
GitHub Quick Start

GitBook: Formação GitHub Certification

Guia de Markdown

Comunidade
AWS User Groups

DIO Community

🚀 Próximos Passos
Melhorias Futuras
Alta Disponibilidade

Adicionar Load Balancer (ALB)

Implementar Auto Scaling Group

Múltiplas AZs

Segurança

Adicionar WAF

Certificado SSL (ACM)

Secrets Manager

Observabilidade

CloudWatch Dashboards

SNS para alertas

X-Ray para tracing

CI/CD

GitHub Actions

CodePipeline

Testes automatizados

Roadmap

👨‍💻 Autor
Seu Nome Alisson Siqueira Ruela



🏆 Agradecimentos
Agradecimentos especiais à Digital Innovation One por proporcionar este desafio e à comunidade AWS pelo suporte contínuo.

