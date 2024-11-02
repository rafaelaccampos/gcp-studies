**O que é jump host e por que usar?**
- É um computador extra que fica entre você e a rede que você quer acessar
- Tem como propósito a segurança, ou seja, para acessar uma instância dentro de uma rede protegida é necessário passar pelo jumphost
- Centraliza o acesso, simplificando o controle, monitoramento e auditoria de quem acessa a rede privada
- Geralmente, instâncias de produção estão configuradas para não permitir acesso a internet, sendo o acesso somente feito pelo jumphost (único ponto exposto via SSH)
- É mais barato aplicar políticas de segurança em um único jumphost do que em cada instância da infraestrutura

**O que é um instance template e por que usar?**
- Um instance template é um modelo que define as configurações padrão de uma instância de máquina virtual no Google Cloud. Ele permite pré-configurar aspectos como o tipo de máquina, a imagem de sistema operacional, o disco de inicialização, a rede, as regras de firewall e outras opções de configuração
- É uma forma de criar um modelo das configurações de uma máquina e reaproveitar para a criação das demais
- Ideal usar quando se tem mais de uma instância com configurações semelhantes
- Ideal usar quando existe necessidade de escalar horizontalmente a infraestrutura, como em clusters de aplicação web
- Ideal usar quando se tem requisitos de consistência e uniformidade na configuração de várias instância

**O que é um MIG (managed instance group) e por que usar?**
 - Um MIG (Managed Instance Group) é um grupo de instâncias de máquina virtual idênticas gerenciadas automaticamente. Ele facilita a escalabilidade e a alta disponibilidade, ajustando a quantidade de instâncias com base na demanda e substituindo instâncias com falhas

**O que é um back-end service e por que usar?**
- Um back-end service gerencia como o tráfego é distribuído entre as instâncias de back-end (como VMs ou MIGs). Ele permite configurar regras de balanceamento de carga, definir políticas de sessão e aplicar verificações de integridade para garantir a disponibilidade dos serviços

**Por que usar load balancer e por que usar?**
- Um load balancer distribui o tráfego de rede entre várias instâncias para otimizar o desempenho, reduzir a sobrecarga em servidores individuais e garantir alta disponibilidade, mantendo a aplicação acessível e responsiva

**Por que o firewall está sendo definido na camada TCP e não HTTP?**
- Porque as regras de firewall são configuradas em termos de portas e protocolos de transporte (como TCP e UDP) e não diretamente por protocolos de aplicação, como HTTP ou HTTPS
- O tráfego HTTP (na porta 80) e HTTPS (na porta 443) usa TCP como protocolo de transporte. Portanto, ao permitir TCP na porta 80, estamos permitindo, na prática, que tráfego HTTP passe por essa porta
- Em resumo, o firewall trabalha numa camada de rede mais baixa, onde ele lida com transporte de pacotes (TCP/UDP) e não analisa conteúdo de pacotes para verificar o protocolo de aplicação, como HTTP ou HTTPS

**O que é por que usar o URL Map?**
- O URL map define como as requisições HTTP ou HTTPS devem ser roteadas para diferentes serviços de back-end com base no URL da solicitação ou em outros critérios, como cabeçalhos e parâmetros
- Nesse caso, ele encaminha todo o tráfego recebido diretamente para o serviço de back-end
- O uso de um URL map permite que você, no futuro, defina regras mais complexas, como direcionamento baseado em prefixos de URL (/api, /admin, etc.) ou condições específicas. Isso ajuda a escalar a aplicação sem precisar reconfigurar o balanceador de carga completamente
- Ao separar o roteamento e o balanceamento, o URL map permite que as regras de roteamento sejam mantidas separadas das configurações de infraestrutura. Isso torna a infraestrutura mais flexível e facilita ajustes futuros.

**O que é por que usar o Proxy HTTP?**
- O proxy HTTP atua como intermediário entre o balanceador de carga e o serviço de back-end, fazendo a conexão entre o tráfego de entrada e as regras definidas no URL map.
- Ele intercepta as requisições HTTP e as redireciona de acordo com as configurações do URL map associado. Dessa forma, o proxy sabe qual serviço de back-end deve processar cada solicitação com base nas regras definidas

**O que é uma porta nomeada e por que usar?**
- O balanceador de carga precisa saber qual porta utilizar para rotear o tráfego HTTP para as instâncias. o definir uma porta nomeada (http:80), o balanceador de carga entende que qualquer requisição HTTP deve ser direcionada para a porta 80 nas instâncias do grupo
- Definir uma porta nomeada (http) torna a configuração mais consistente e fácil de entender, especialmente se outras portas forem adicionadas no futuro

**O que é a regra de firewall com ingress e por que ela é necessária?**
- Permite que requisições HTTP externas (fora da rede VPC) sejam encaminhadas para as instâncias do grupo
- Sem essa regra, o tráfego HTTP da internet seria bloqueado pelo firewall, impedindo que o balanceador de carga se comunique com as instâncias na porta 80. Essa regra é essencial para que as instâncias possam receber requisições HTTP provenientes de usuários externos.
