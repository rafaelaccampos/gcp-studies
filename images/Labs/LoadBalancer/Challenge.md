### Caso de uso real: Website institucional ou landing page de alta disponibilidade
- Imagine que uma empresa ou organização deseja hospedar seu website institucional ou uma landing page para uma campanha de marketing. Como este website pode ter picos de acesso, principalmente durante períodos de promoção ou eventos, é importante que ele:

- Escale automaticamente para lidar com o aumento temporário no tráfego.
- Tenha alta disponibilidade para garantir que os visitantes sempre consigam acessar o conteúdo.
- Explicação do cenário
Nesse cenário, o objetivo é configurar uma infraestrutura que:

- Escale horizontalmente: O uso de um grupo gerenciado de instâncias permite que o sistema ajuste automaticamente o número de instâncias Nginx com base na demanda.
- Distribua a carga: Um balanceador de carga distribui o tráfego entre várias instâncias para garantir que o sistema responda rapidamente, mesmo com um número alto de acessos.
- Tenha alta resiliência: A criação de instâncias em uma zona específica e o uso de verificações de integridade permitem que o sistema identifique instâncias com problemas e distribua o tráfego para outras saudáveis.
- Garanta fácil configuração e reutilização: Usar um template de instância facilita a replicação e a criação de novas VMs idênticas sempre que necessário.
- Como os comandos atendem ao caso de uso
- Aqui está como cada comando ajuda a criar essa infraestrutura para um website institucional ou landing page:

**Configuração da Instância de Salto (Jump Host):**

- O primeiro comando cria uma instância chamada nucleus-jumphost-161, que serve como um ponto de acesso seguro para administradores. Esse jumphost permite que a equipe faça login na rede de forma segura para gerenciar as instâncias web ou o balanceador de carga.

**Template e Configuração do Servidor Web:**

- O template nucleus-nginx-template instala o Nginx e personaliza sua página inicial. Isso permite criar facilmente várias instâncias com a mesma configuração, de forma que cada VM sirva o mesmo conteúdo de maneira consistente.
- O script de inicialização instala e inicia o Nginx automaticamente, garantindo que as novas instâncias estejam prontas para servir conteúdo assim que forem criadas.

**Criação do Grupo de Instâncias Gerenciado:**

- O grupo nucleus-nginx-group usa o template para criar múltiplas instâncias de servidor web. Como é um grupo gerenciado, ele pode automaticamente adicionar ou remover instâncias com base na demanda, tornando o sistema escalável.

**Firewall para Acesso HTTP:**

- A regra de firewall permit-tcp-rule-906 permite tráfego HTTP externo na porta 80. Isso é necessário para que o público externo acesse o website.
Outra regra de firewall allow-http-from-internet garante que o tráfego HTTP (porta 80) de qualquer lugar na internet possa acessar as instâncias do grupo.

**Configuração do Balanceador de Carga:**

- Um endereço IP global (nucleus-lb-ipv4) é reservado para que o balanceador de carga tenha um IP estático, facilitando o apontamento de domínios.- 
- O balanceador de carga (nucleus-backend-service), configurado com verificações de integridade (nucleus-health-check), garante que o tráfego só seja direcionado para instâncias saudáveis.
- O serviço backend e o URL map (nucleus-url-map) são configurados para distribuir o tráfego de forma uniforme entre as instâncias.
- Finalmente, a regra de encaminhamento (nucleus-http-rule) direciona o tráfego da internet para o balanceador de carga na porta 80.

**Resumo**
- Esses comandos configuram uma infraestrutura ideal para um website institucional ou landing page, garantindo:

- Alta disponibilidade e escalabilidade automática para lidar com picos de tráfego.
- Facilidade de gestão através de um jumphost seguro.
- Balanceamento de carga e verificações de integridade, assegurando que o tráfego seja distribuído entre instâncias saudáveis.
- Este tipo de configuração é típico para sites que exigem resiliência e flexibilidade sem a necessidade de servidores web complexos, permitindo o foco em entregas de conteúdo rápidas e consistentes.