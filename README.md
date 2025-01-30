# Monitorar-Wrapper-Linux
UserParameter=  para monitorar Wrappers do linux


Passo 1: Criar um UserParameter no Zabbix Agent
nano /etc/zabbix/zabbix_agentd.conf

Adicione uma das seguintes linhas no final do arquivo,a primeira opção que funcionou para nossa empresa, alterar 0 "importacaowrapper" para o nome do seu wrapper !


UserParameter=concentrador.status,cd /usr/socin/econect/conc/bin && ./importacaowrapper status | grep -oE 'Java:[A-Z]+' | wc -l

ou

UserParameter=concentrador.status,/usr/socin/econect/conc/bin/importacaowrapper status | grep -q "running" && echo 1 || echo 0


Agora, reinicie o Zabbix Agent:
sudo systemctl restart zabbix-agent

Passo 2: Testar o UserParameter

No próprio servidor, execute:
zabbix_agentd -t concentrador.status

Se o serviço estiver rodando, o retorno será:
concentrador.status [t|1]

Se o serviço estiver parado:
concentrador.status [t|0]

Passo 3: Criar um Item no Zabbix Server
Agora, no Zabbix Web Interface:

Vá para Configuração → Hosts e selecione o host do servidor.
Vá para Configuração → Itens e clique em Criar Item.
Configure:
Nome: Monitoramento do Concentrador
Tipo: Zabbix agent
Chave: concentrador.status
Tipo de informação: Numérico (inteiro)
Unidade: Nenhuma
Intervalo de atualização: 30s
Host: Selecione o servidor
Clique em Adicionar e depois em Atualizar.


Passo 4: Criar um Trigger para Alertas

Vá para Configuração → Triggers e clique em Criar Trigger.
Configure:
Nome: Concentrador Parado
Expressão: {<HOST>:concentrador.status.last()}=0
Gravidade: Alta
Clique em Adicionar e depois Atualizar.

Passo 5: Testar o Monitoramento

Pare o serviço e veja se o alerta aparece no Zabbix:
./concentradorwrapper stop

Reinicie o serviço e veja se o alerta desaparece:
./concentradorwrapper start


