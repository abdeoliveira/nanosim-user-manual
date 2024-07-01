# Cluster “NanoSim”: Manual do Usuário

Prof. Dr. Alan Barros de Oliveira

Departamento de Física, Universidade Federal de Ouro Preto, Ouro Preto, MG, Ouro Preto, MG, Brasil. 35400-000.

## Conexão

A conexão é feita via ssh através de terminal, com o comando `ssh -p PORT USERNAME@HOSTIP`.
Os dados de  `PORT`, `USERNAME` e `HOSTIP` são fornecidos diretamente ao usuário, juntamente com a senha de acesso.

## Definições

O computador principal será denominado de `headnode` neste documento, enquanto os secundários serão chamados de `nodes`.
Estes últimos seguem o padrão de nomeclatura `n01, n02, ...`. Nos exemplos dados ao longo do texto, usamos `n##` para denominar
um `node` genérico.

## Visão Geral

O comando `top-nodes` pode ser usado para se ter uma visão do estado do cluster.
O número de *cores* listado refere-se aos cores reais, 
desprezando os cores [Intel Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html).

Os `nodes` `n01` e `n02` possuem 16 cores reais, 
mas 8 tem clock 4,90 GHz e 8 tem clock 3,60 GHz. Portanto, jobs submetidos com 16 cores serão processados com base nos cores mais lentos. 

## Quota em disco

Todos os usuários iniciam com uma cota em disco de `400 Gb` para o `/home`, 
com `100 Gb` *adicionais* por um período de `7 dias` (*grace period*). 
Pedidos justificados para aumento de quota podem ser feitos aos administradores do cluter.

## Submissão de jobs

* Jobs devem ser submetidos diretamente nos `nodes`. 
* **Não execute programas no `headnode`.** 
* Não há sistema de filas no momento. 

Sugerimos os passos a seguir:

1. No `headnode`, use o commando `top-nodes` para visualizar o estado geral do cluster.
2. Conecte-se a um `node` livre, fazendo `ssh -p 1003 n##`.
3. Execute seus programas.
4. Saia do `node` executando `exit` no terminal. Para conferir se seus jobs 
continuam rodando após logout do  `node`, execute `top-nodes n##` no `headnode`.

## Reserva de `nodes`

Mediante solicitação justificada aos administradores do cluster, qualquer usuário 
pode pedir a reserva de `nodes` para si por um período de tempo determinado. 
Geralmente o pedido se justifica devido a prazos curtos para finalizações de 
artigos, teses, dissertações etc. Unidades reservadas aparecerão com status 
`LOCKED (username)` , onde `username` é o nome de usuário a quem o `node` está reservado.

## Backups

Apesar de cada usuário ser considerado **único** responsável pela 
segurança dos seus dados, nós temos backup do `/home`.

Snapshots do `/home` são feitos uma vez por semana e armazenados 
em `/backups/users/`. 

A política de rentenção é a que se segue:

* Um snapshot por semana é mantido nos últimos 30 dias.
* Um snapshot por mês é mantidos nos últimos 6 meses.
* Nenhum snapshot é mantido por mais de 6 meses.

### LAMMPS 

Está instalado com o nome de `lmp` com os pacotes adicionais `CLASS2`, `EXTRA-COMPUTE`, `KSPACE`,`MANYBODY`,`MC`,`MISC`,`MOLECULE`, `OPENMP`, `REAXFF` e `RIGID`.

Caso necessite de uma compilação com outros pacotes que não os citados acima, contacte os administradores do cluster.

### SIESTA e TRANSIESTA

O Siesta está instalado com o nome de `siesta`, em modo PARALELO e com suporte ao Transiesta.

#### Transiesta

Não existe o executável `transiesta`. Para tanto, siga os passos abaixo (usaremos 4 cores
apenas como exemplo):

1. Para criar um eletrodo, execute `mpirun -np 4 siesta --electrode elec_input.fdf`
2. Após criar os eletrodos, execute o Siesta normalmente, como `mpirun -np 4 siesta < input.fdf`

## Configurações avançadas (opcional)

### Conexão ssh sem senha entre `headnode` e `nodes`

Siga os passos abaixo:

1. No `headnode`, execute `ssh-keygen` e 
aperte `enter` para todas as perguntas (sem digitar nada).
2. Execute: `cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys`.
3. Crie o arquivo `~/.ssh/config` e escreva nele:

```
Host n*
    Port 1003
    StrictHostKeyChecking=no
``` 

Você já deve ser capaz de se conectar aos `nodes` sem senha, 
executando `ssh n##`. 

**OBS.:** Se você achar que esqueceu de algum passo ou fez algo de errado, basta
fazer `rm -r ~/.ssh` no `headnode` e recomeçar.


