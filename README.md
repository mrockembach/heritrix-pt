# Guia de usuário Heritrix
## Introdução:
Heritrix é o rastreador da web do Internet Archive, com qualidade de arquivamento extensível, escálavel e de código aberto.
Esse guia explica como instalar, configurar e usar o Heritrix para rastrear a web. Assume-se que o usuário possui um conhecimento geral de conceitos computacionais como HTML e URLs.

### Usuários:
Os usuários desse documento constituem-se de administradores do Heritrix e outras equipes técnicas que desejam rastrear a internet usando o Heritrix.

### Versões:
As informações desse guia referem-se à versão 3.0 do Heritrix, a não ser que o contŕário seja informado. Seções referentes à versão 3.1 estão marcadas com a nota "A partir da versão 3.1"

## Requisitos do sistema

O heritrix possui os seguintes pré-requisitos: 

| Requisitos do sistema | Descrição |
| ------------- | ------------- |
| Distribuição Linux | A execução do Heritrix requer uma distribuição do Linux. O Heritrix talvez possa ser executado em outras plataformas, mas essa opção não é suportada. |
| Java Runtime Environment (JRE) 1.6  | O Heritrix requer o JRE 1.6. O JRE 1.6 pode ser baixado em: http://www.javasoft.com ou http://www.ibm.com/java. Observe que as atualizações 23 e 24 (e, possivelmente, posteriores) do Java 6 não podem ser usados com o Heritrix 3.0 (ou anterior) devido a um bug na funcionalidade GZIP do JRE, na qual o Heritrix depende para ler ARCs/WARCs. A partir do Hertirix 3.1, esse problema foi resolvido. |
| Hardware |O heap Java padrão para o Heritrix é de 256 MB de RAM, que geralmente é adequado para rastreamentos que abrangem centenas de hosts. Atribua mais da sua RAM disponível ao heap se você estiver rastreando milhares de hosts ou tiver problemas de falta de memória Java. Você pode usar a variável JAVA_OPTS para configurar a memória. Ver Configuração do Heritrix. |

Todas as bibliotecas gratuitas/de código aberto necessárias para executar o Heritrix estão incluídas na distribuição. Veja [dependencies] (http://crawler.archive.org/dependencies.html) para uma lista completa. Licenças para todas as bibliotecas são listadas na seção de dependências do project.xml ou no SourceForge. Cada versão vem em quatro versões, empacotadas como `.tar.gz` ou `.zip`, incluindo a fonte ou não.

## Atualizações das versões 3.0 e 3.1

Atualizações da versão 3.0:
1. Possibilidade de realizar vários rastreamentos simultâneos. O único limite de número de rastreamentos realizados simultaneamente refere-se a memória alocada no Heritrix.
2. Arquivo único XML de configuração baseado no framework Spring. Esse arquivo substitui o order.xml e outros arquivos de configuração do Heritrix 1.x.
3. Possibilidade de busca e modificação dos beans configurados por um navegador de uso simples. Olhar Bean Browser.
4. Extensabilidade aprimorada por meio do framework Spring. Por exemplo, substituições de domínios podem ser definidas em um nível mais refinado. Olhar Planilhas.
5. Controle de uso mais seguro. HTTPS são usado para acessar e manipular o controle de uso do usuário.
6. Maior escabilidade.  Nas versões anteriores, rastreamentos com grandes valores de seed (dezenas ou centenas de milhões) podiam tentar utilizar memória além da alocada no Heritrix. Isso causava o interrompimento do rastreamento. A versão Heritrix 3.0 arrumou esses problemas, permitindo o processamento estável de rastreamentos de grande tamanho.
7. Flexibilidade maior ao modificar um rastreamento em andamento. Rastreamentos em andamento podem ser modificados pelo navegador Bean ou pelo Action Directory.
8. Introdução de filas paralelas (parallel queues). Ao rastrear sites específicos que aguentam tráficos altos, a opção das filas paralelas pode ser usada para abrir várias conexões de rastreamento concomitantes em um único site.
9. Controle de script que aceita input de scripts em vários formatos, como o AppleScript e o ECMAScript. Scripting pode ser usado para acessar e manipular, de forma programada, os componentes do núcleo do Heritrix.

Atualizações da versão 3.1 podem ser encontradas aqui.

## Instalação do Heritrix
Uma distribuição binária do Heritrix pode ser baixada pelo link http://builds.archive.org:8080/maven2/org/archive/heritrix/heritrix/3.1.1/heritrix-3.1.1-dist.zip. Depois de baixado, expandir o arquivo. Esse processo funciona em algumas plataformas.

## Configuração do Heritrix

1. Configure a variável de ambiente `JAVA_HOME`. O valor deve apontar para a instalação do JRE 1.6 Java. Por exemplo, essa variável pode ser definida como `usr/local/java/jre`

```
export JAVA_HOME=/usr/local/java/jreP
```

2. Defina a variável de ambiente `HERITRIX_HOME`. O valor deve apontar para o diretório principal do Heritrix, que está abaixo do diretório bin.

```
export HERITRIX_HOME=/PATH/TO/HERITRIX
```

Por exemplo, uma instalação do Heritrix com um diretório `bin` localizado em `/home/user/heritrix3.1/bin` seria configurada da seguinte maneira.

```
export HERITRIX_HOME=/home/user/heritrix3.1
```

3. Configure a permissão de execução no arquivo de inicialização do Heritrix.

```
chmod u+x $HERITRIX_HOME/bin/heritrix
```

4. Para alterar a quantidade de memória alocada ao Heritrix (o tamanho de heap Java), configure a variável de ambiente `JAVA_OPTS`. O exemplo a seguir aloca 1 GB de memória ao Heritrix.

```
export JAVA_OPTS=-Xmx1024M
```

## Execução do Heritrix 3.0 e 3.1
O Heritrix pode ser usado a partir de diferentes linhas de comando. Insira o seguinte comando para visualizar as opções disponíveis. 
```
$HERITRIX_HOME/bin/heritrix --help
```
A tabela a seguir lista as opções de linhas de comando.

| Opções de linha de comando | Descrição |
| ------------- | ------------- |
| `-a` `--web-admin` `<arg>` | Especifica o nome de usuário e senha de autorização que devem ser fornecidos para acessar a IU da Web. Este parâmetro é necessário se você ativar a interface do usuário da web. O formato do parâmetro é `<adminname>: <adminpassword>`. Por exemplo, `admin:admin`. A partir da versão 3.1, se o parâmetro fornecido para a opção de linha de comando -a for uma cadeia começando com "@", o restante da cadeia será interpretado como um nome de arquivo local contendo o login e a senha do operador. Isso adiciona uma camada adicional de proteção ao nome de usuário e senha do administrador. |
| `-b` `--web-bind-hosts` `<arg>` | Especifica uma lista, separada por vírgulas, de nomes de host/endereços IP para vincular à IU da Web. Se nenhum for especificado, a interface do usuário da Web só estará disponível via localhost/127.0.0.1. Você pode usar '/' como um atalho para 'todos os endereços'. |
| `-h`,`--help` `<arg>` | Exibe informações de uso. |
| `-j`,`--jobs-dir` | Exibe o diretório de tarefas. O padrão é `./jobs.` |
| `-l`,`--logging-properties` | Exibe o caminho completo para o arquivo de propriedades de log (por exemplo, conf/logging.properties). Se presente, esse arquivo será usado para configurar log de Java. O padrão é `./conf/logging.properties`. |
| `-p`,`--web-port` `<arg>` | Especifica qual porta a IU da Web irá escutar (listen to). | 
| `-r`,`--run-job` <arg> | Especifica um nome de tarefa ou perfil pronto para ser iniciado quando o Heritrix for iniciado. Se um nome de perfil for especificado, o perfil será primeiro copiado para uma nova tarefa pronta, e essa tarefa pronta será iniciada. A partir da versão 3.1, esta opção foi eliminada. |
|`-s`,`--ssl-params` `<arg>` | Especifica um caminho de keystore, uma senha de keystore e uma senha de chave para uso de HTTPS. Separe os valores com vírgulas e não inclua espaços em branco. |
  
Para iniciar o Heritrix com a interface do usuário da Web ativada, digite o seguinte comando. O nome de usuário e a senha da interface do usuário da Web estão definidos como "admin" e "admin", respectivamente.

```
$HERITRIX_HOME/bin/heritrix -a admin:admin
```

Por padrão, o endereço de escuta da IU da Web é vinculado apenas ao endereço 'localhost'. Portanto, a interface do usuário da Web só pode ser acessada na mesma máquina a partir da qual foi iniciada. A opção '-b' pode ser usada para escutar endereços diferentes/adicionais. Veja as Considerações de Segurança antes de alterar esse padrão.


## Interface do usuário baseada na Web (IUW)
Depois de abrir o Heritrix, a interface do usuário baseada na web torna-se acessível.

A URI de acesso da Web IU normalmente é
https://(heritrixhost):8443

A IUW é protegida por senha. Não há nenhum login de acesso padrão. Um nome de usuário e uma senha devem ser fornecidos usando as opções de linha de comando -a ou -web-admin fornecidas no startup, ou definindo a propriedade do sistema heritrix.cmdline.admin. O nome de usuário e a senha ficarão salvas (printed) no console no startup. A partir da versão 3.1, o nome de usuário do administrador e a senha não ficarão salvas no controle no startup. Também, a partir da versão 3.1, se o parâmetro fornecido para a opção de linhas de comando -a -web-admin é uma string começando com "@", o resto da string será interpretada como um arquivo local de nome contendo o login e a senha do operador, adicionando mais uma medida de proteção ao nome de usuário e senha do administrador.

A página inicial de login solicita o nome de usuário e a senha. Depois de feito o login, a sessão será encerrada após um período sem uso.

O acesso da IUW é por HTTPS. O Heritrix é instalado com uma chave de acesso que contém um certificado auto-assinado. Isso fará com que o Mozila (navegador recomendado) exiba um prompt avisando que um certificaco auto-assinado está sendo usado. Siga as instruções abaixo para realizar o login no Heritrix pela primeira vez.

1. Clique no link "I understand the risks". Um botão que permite adicionar uma exceção de segurança será exibido. Clique no botão "Add Exception".

2. Uma caixa de diálogo será exibida permitindo que você confirme a exceção clicando em "Confirm Security Exception".

3. Depois de confirmar a exceção, você será solicitado a fornecer o nome de usuário e a senha da IUW.

4. Depois de digitar o nome de usuário e a senha do administrador, será concedido acessi à IUW.

## Considerações de segurança

O Heritrix é um aplicativo de rede grande e ativo que apresenta implicações de segurança tanto na máquina principal, onde ele roda, quanto remotamente, nas máquinas conectadas. 

###### Entendendo os riscos
É importante entender que a IU da Web permite o acesso remoto do rastreador de maneiras que poderiam, potencialmente, interromper o rastreamento, mudar seu comportamento, ler ou criar arquivos de acesso local e fazer ou ativar outras ações no Java VM ou na máquina local pela execução de scripts arbitrários fornecidos pelo operador.

Um acesso não autorizado da IU da Web pode encerrar ou corromper um rastreamento. Também pode alterar o comportamento do rastreador fazendo com que ele seja um problema para os outros hosts da rede. Os arquivos acessíveis ao processo do rastreador podem ser excluídos, corrompidos ou substituídos, o que pode causar problemas extensos na máquina de rastreamento. 

Outro risco possível é que conteúdos mal-intencionados, em conjunto com os problemas do rastreador, podem interromper o rastreamento ou outros arquivos e operações no sistema local. Por exemplo, no passado, sem má intencão, alguns conteúdos de mídia avançada causaram o uso descontrolado de memória em bibliotecas de terceiros usadas pelo rastreador. Isso causou uma exaustão de memória que interrompeu e corrompeu o rastreamento em andamento. De forma parecida, padrões atípicos de inputs causaram  uso indevido da CPU pelas expressões regulares de extração de links do rastreador, causando rastreamentos extremamente lentos. Operadores de rastreamento devem monitorar de perto seus rastreamentos e usar a lista de discussão do projeto e o banco de dados de problemas para se manter atualizado sobre os problemas do rastreador.

###### Controle de acesso à rede

Lançada sem nenhum bind-adress específico ('-b' flag), a IU da Web do rastreador vincula-se apenas ao endereço de localhost/loopback (127.0.0.1),  e, portanto, só é acessível pela rede na mesma máquina em que foi lançada.

Se prático, essa configuração padrão deve ser mantida. Uma técnica como o tunelamento SSH poderia ser usada por usuários autorizados da máquina de rastreamento para permitir o acesso à Web de sua máquina local à máquina de rastreamento. Por exemplo, imagine o Heritrix rodando numa máquina 'crawler.example.com', com sua IU da Web apenas ouvindo/ligado ao seu endereço de host local. Supondo que um usuário chamado 'crawloperator' tenha acesso SSH a 'crawler.example.com', ele pode emitir o seguinte comando SSH em sua máquina local:
```
ssh -L localhost:9999:localhost:8443 crawloperator@crawler.example.com -N
```
Isso diz ao SSH para abrir um túnel que encaminha conexões para "localhost: 9999" (na máquina local) para o "localhost: 8443" das máquinas remotas. Como resultado, a IU da Web do rastreador estará disponível por meio de "https: // localhost: 9999 /" enquanto o túnel existir (até que o comando ssh ou a conexão sejam interrompidos). Ninguém mais na rede pode conectar-se diretamente à porta 8443 em 'crawler.example.com' (já que está apenas escutando o endereço de lookpback local), e ninguém em outro lugar na rede pode se conectar diretamente à porta 9999 do operador (já que também está apenas escutando o endereço de lookback local). 

Se você precisar da porta de escuta do Heritrix vinculada a um endereço público, o sinalizador de linha de comando '-b' poderá ser usado.  Esse sinalizador usa (como argumento) o nome do host/endereço a ser usado. O caractere '/' pode ser usado para indicar todos os endereços.

Se essa opção for usada, um conjunto de credenciais de login ainda mais único e difícil deve ser escolhido. Talvez ainda deva ser considerado o uso de outras políticas de rede/firewall para bloquear o acesso de origens não autorizadas.

### Controle de acesso de autenticação de login 

O usuário e a senha administrativos proporcionam uma segurança rudimentar contra acessos não autorizados. Para mais segurança, você deve: 
1. Usar um nome de usuário e senha únicos e difíceis de adivinhar para proteger a IU da Web. O Heritrix usa HTTPS para criptografar comunicações entre os clientes e a IU da Web. Tenha em mente que definir o nome de usuário e senha na linha de comando pode causarfazer com que eles fiquem visíveis para outros usuários da máquina de rastreamento - por exemplo, através do output de uma ferramenta como 'ps' que mostra as linhas de comando usadas para processos de lançamento. Tenha em mente também que essas informações são ecoadas em texto simples no heritrix_out.log para referência do operador. A partir da versão 3.1, o nome de usuário e senha administrativos não são ecoados no heritrix_out.logl.  Ainda a partir da versão 3.1, se o parâmetro fornecido para a opção de linhas de comando -a -web-admin é uma string começando com "@", o resto da string será interpretada como um arquivo local de nome contendo o login e a senha do operador. Assim, as credenciais não são visíveis para as outras máquinas que usam o comando listar processos (ps).
  2. Inicie a VM Java de hospedagem do Heritrix com uma conta de usuário que tenha os privilégios mínimos necessários para operar o rastreador. Isso limitará os danos no caso da IU da Web ser acessada de maneira maliciosa.
  
## Guia rápido para executar seu primeiro rastreamento

A página do Console Principal aparece depois da instalação do Heritrix e do acesso da IUW.

1. Insira o nome da nova tarefa (job) na caixa de texto abaixo de "Create new job with recommended starting configuration". Depois clique em "create".

A tarefa recém criada aparecerá na lista de tarefas na página do Console Principal. No Heritrix 3.0, a tarefa será baseada no perfil profile-defaults. A partir da versão 3.1, esse perfil foi eliminado. Ver Perfis para mais informações.

2. Clique no nome da nova tarefa e você será redirecionado para a página da tarefa.
O nome do arquivo de configuração, crawler-beans.cxml, aparecerá no topo da página. Ao lado, encontra-se a opção de "edit".

3. CLique em "edit" e os conteúdos do arquivo de configuração aparecerão em uma área de texto editável.

4. Nesse passo, várias propriedades devem ser inseridas para a tarefa tornar-se executável.
i. Primeiro, adicione um valor válido na propriedade metadata.operatorContactUrl, como http://www.archive.org. 
ii. Em seguida, preencha o elemento `<prop>` do bean `longOverrides` com os valores do seed para o rastreamento.  Um seed de teste é configurado para referência. Quando terminar, clique em "save changes" no topo da página. Para mais informações sobre configuração de tarefas ver Configurando Tarefas e Perfis. 
  
5. Na página da tarefa, clique em "build". Esse comando montará a infraestrutura necessária para executar a tarefa. A seguinte mensagem será exibida no registro de tarefas: "INFO JOB instantiated."

6. Em seguida, clique no botão "launch". Este comando inicia a tarefa no modo "paused". Nesse passo, a tarefa está pronta para ser executada.

7. Para executar a tarefa, clique no botão "unpause". A tarefa, então, começará a enviar solicitações para os seeds do seu rastreamento. O status da tarefa será definido como "Running". Atualize a página para acompanhar as atualizações das estatísticas.

### Observação

* Uma tarefa não será modificada se o perfil ou a tarefa em que foi baseada for alterado.
* Tarefas baseadas no perfil padrão não estão prontas para serem executadas. O metadata.operatorContactUrl deve ser definido com um valor válido.

Mais informações sobre a avaliação do progresso de uma tarefa podem ser encontradas em Análise de Tarefas.

## Sair do Heritrix

Para sair do Heritrix, envie a combinação de teclas para o console, o que forçará o processo a sair (como o Control-C). Para sair de um processo em segundo plano no qual o Heritrix está em execução, use o comando kill.

Para sair da versão 3.1, clique no botão "Exit Java process" depois de marcar a caixa "I'm sure".

## Editar uma tarefa em andamento

A configuração de uma tarefa pode ser editada enquanto ela ainda está em andamento. Isso pode ser feito pelo Bean Browser ou pelo link do Console de Script na página da tarefa. O Bean Browser permite que as propriedades de tempo de execução sejam editadas.

O console de script também pode ser usado para editar, de forma programada, tarefas em execução.

Se for alterar um valor não atômico, é recomendável pausar o rastreamento antes de fazer a alteração, pois algumas modificações nas entidades de configuração composta podem não ocorrer de maneira segura. Um exemplo de uma mudança não atômica é a adição de uma nova planilha.

A partir da versão 3.1, o tratamento de operações de orçamento de fila e de rotação/retirada foi refatorado para garantir que alterações feitas  durante o rastreamento (por meio de novas planilhas sobrepostas ou edição direta feita com a ferramenta Bean Browser ou com o console de script) entrem em vigor imediatamente. O DispositionProcessor possui configuração de sobreposição de planilhas para aplicar essa marcação. Em geral, as alterações de configurações por meio de novas planilhas e associações de planilhas durante um rastreamento (conforme inserido via script) agora entram em vigor em todos os URIs que estão sendo retirados para processamento, em vez de apenas URIs recém-descobertos. Portanto, alterações feitas via bean-browse/scripting /new-sheet-overlays entram em vigor imediatamente.

## FTP

O Heritrix suporta rastreamento de sites FTP. Seeds devem ser adicionados no seguinte formato: ftp://ftphostname.org/ftpdirectory.

## Ponto de verificação (Checkpoint)

O ponto de verificação de uma tarefa de rastreamento salva uma representação do estado atual da tarefa em um diretório no `checkpointsPath`, que recebe o nome do ponto de verificação. Essa representação inclui a serialização dos principais objetos da tarefa de rastreamento, cópias do conjunto atual de arquivos de log de bdbje e outros arquivos do estado do rastreamento.  O diretório do ponto de verificação contém tudo o que é necessário para recuperar um rastreamento. O ponto de verificação também roda os logs de rastreamento, incluindo o log `recover.gz`, se ativado. Arquivos Log não são copiados para o diretório do ponto de verificação e permanecem no diretógio de `logs`, sendo diferenciados através de sufixos. O sufixo é o nome do ponto de verificação; por exemplo, para o ponto `000031` o log do rastreamento será `crawl.log.000031`.

Para executar um ponto de verificação, clique no botão do ponto de verificação (checkpointin) na página de tarefas da IUW ou invoque a funcionalidade do ponto de verificação pelo REST API. O ponto de verificação pode levar mais tempo em rastreamentos maiores (a partir da versão 3.1 esse processo é muito mais rápido). Enquanto o ponto de verificação estiver sendo feito, o status do rastreamento ficará como CHECKPOINTING. Após o término, o rastreador continuará o rastreamento, a não ser que ele estivesse pausado anteriormente. Nesse caso, o rastreamento continuará pausado.

A recuperação de um ponto de verificação é parecida com a recuperação de um rastreamento usando o `frontier.recovery.log`.

###### Ponto de verificação automático

Para configurar o Heritrix para executar pontos de verificação automaticamente, remova ou adicione a seguinte linha no arquivo `logging.properties`.

`org.archive.crawler.framework.Checkpointer.period=2`

Isso fará com que um Timer Thread seja instalado e executado em intervalos de hora. Veja `heritrix_out.log` para registrar informações sobre o Timer Thread.

A  partir da versão 3.1, 'hard links' (quando disponíveis) serão usados para coletar os arquivos BerkeleyDB-JE necessários para reproduzir o status do rastreamento. O deletamento automático de pontos de verificação antigos (arquivos ".DEL") foi reativado, eliminando a necessidade de excluir manualmente os arquivos desnecessários. Também facilita a movimentação e a limpeza de pontos de verificação antigos.

**Recomeçar a partir de um ponto de verificação**

A partir da versão 3.1, a IUW fornece a opção de recomeçar um rastreamento a partir de um ponto de verificação. Siga os passos abaixo para recomeçar um rastreamento a partir de um ponto de verificação:

1. Faça um ponto de verificação do rastreamento em execução clicando em "checkpoint".
2. Quando o checkpoint terminar (uma mensagem aparecerá avisando o operador), encerre o rastreamento clicando no botão "terminate".
3. Destrua a tarefa clicando no botão "teardown". 
4. Reconstrua a tarefa clicando no botão "build". Agora, uma caixa deve aparecer em cima dos botões de comando. A caixa tem os nomes dos pontos de verificação invocados anteriormente.
5. Selecione um ponto de verificação na caixa. O ponto de verificação selecionado será usado para iniciar a tarefa recém construída.
6. Clique em "launch".
7. Clique em "unpause".
A tarefa iniciará a partir do ponto de verificação escolhido.

## Página do Console Principal

A página do console principal aparece logo após o login. Esse página lista todas as tarefas e perfis.

### Elementos e operações de dados do controle principal

###### rescan

Faz com que o Heritrix examine o sistema de arquivos procurando qualquer mudança no diretório "tarefas". A exibição é, então, sincronizada com o sistema de arquivos.

###### create

Permite que um nome seja inserido e uma nova tarefa de rastreamento seja criada. A tarefa de rastreamento será baseada nos perfis padrões.

###### add

Permite que um diretório de tarefa não gerenciado pelo Heritrix seja especificado. Após inserir o caminho para o novo diretório e clicar em "add", o Heritrix permitirá que você administre o diretório. Por exemplo, será possível configurar a tarefa usando o arquivo crawler-beans.cxml.

###### status

O status das tarefas em andamento, o número de vezes que uma tarefa foi iniciada e o caminho para o arquivo de configuração das tarefas aparecem na página do Console Principal. Também aparecem as estatísticas de memória do Heritrix e se a tarefa é um perfil ou não.

###### Exit Java Process

A partir da versão 3.1, existe o botão "Exit Java Process". Após ser selecionado juntamente com a opção "I'm sure", fará com que o Heritrix seja encerrado e fechado.

## Nevegar pelos beans

A IUW fornece uma maneira de visualizar e editar os beans Spring que formam a configuração de um rastreamento. É importante notar que mudar a configuração de um rastreamento usando o Bean Browser não fará com que o arquivo crawler-beans.cxml seja atualizado. Portanto, mudanças de definições feitas com o Bean Browser não são permanente. O Bean Browser deve ser usado apenas para mudar as definições de um rastreamento em andamento.  Para acessar o navegador, clique no link do Bean Browser na página de tarefas. A hierarquia dos beans Spring aparecerá.

Os beans individuais podem ser detalhados clicando neles. O exemplo abaixo mostra a exibição depois de clicar nos seeds beans. 

## Perfis

Perfis são modelos para a criação de tarefas de rastreamento. Contêm todas as configurações de uma tarefa de rastreamento, mas não são considerados rastreáveis. O Heritrix não permite que um perfil seja diretamente rastreado, apenas tarefas baseadas em perfis.

Um exemplo comum de uma configuração de perfil é deixar a propriedade `metadata.operatorContactUrl` indefinida, forçando o operador a inserir um valor válido.

Os perfis podem ser usados como modelos ao deixar suas definições de configuração inválidas. Dessa meneira, o operador é obrigado a escolher suas configurações ao criar uma tarefa a partir de um perfil. Isso pode ser vantajoso quando um administrador precisa configurar várias tarefas de rastreamento diferentes para acomodar sua política de rastreamento.

Se uma tarefa de rastreamento é um perfil ou uma tarefa executável é determinado pelo nome de arquivo do arquivo de configuração principal. Se começar com "profile-", é um perfil. Tenha cuidado ao alterar o nome de um arquivo de configuração principal ao copiar manualmente o perfil para criar uma tarefa de rastreamento possível de incialização.

A partir da versã 3.1, a marca "profile-" foi eliminada. Não há mais restrições em nomes de perfis.

## Saídas do Heritrix

Além dos logs, os seguintes arquivos são gerados. Algumas das informações contidas neles também estão presentes na IUW.

* surts.dump
* negative-surts.dump
* heritrix_out.log
* crawl-report.txt
* hosts-report.txt
* mimetype-report.txt
* processors-report.txt
* responsecode-report.txt
* seeds-report.txt
* frontier-summary-report.txt
* source-report.txt
* threads-report.txt
* arquivos WARC

###### surts.dump

Contém os formatos SURTs dos seed URIs.

###### negative-surts.dump

Contém o formulário SUR de URIs que devem ser excluídos do rastreamento.

###### heritrix_out.log

Captura a saída para saída padrão e erro padrão. A maior parte da saída consiste em exceções de baixo nível e informações de registro.

Esse arquivo é criado no mesmo diretório do arquivo jar do Heritrix. Não é associado com nenhuma tarefa específica, mas contém saídas de todas as tarefas executadas pelo rastreador.

Exemplo de um output desse arquivo:

```
Darwin internet-archives-macbook-pro.local 10.0.0 Darwin Kernel Version 10.0.0: Fri Jul 31 22:47:34 PDT 2009; root:xnu-1456.1.25~1/RELEASE_I386 i386
java version "1.6.0_15"
Java(TM) SE Runtime Environment (build 1.6.0_15-b03-219)
Java HotSpot(TM) 64-Bit Server VM (build 14.1-b02-90, mixed mode)
JAVA_OPTS= -Xmx256m
core file size          (blocks, -c) 0
data seg size           (kbytes, -d) unlimited
file size               (blocks, -f) unlimited
max locked memory       (kbytes, -l) unlimited
max memory size         (kbytes, -m) unlimited
open files                      (-n) 256
pipe size            (512 bytes, -p) 1
stack size              (kbytes, -s) 8192
cpu time               (seconds, -t) unlimited
max user processes              (-u) 266
virtual memory          (kbytes, -v) unlimited
```

###### crawl-report.txt

Contém métricas úteis sobre tarefas terminadas. O relatório é criado pelo bean StatisticsTracker e é escrito no fim do rastreamento.  

Exemplo de uma saída desse arquivo:

```
Crawl Name: basic
Crawl Status: Finished
Duration Time: 1h33m38s651ms
Total Seeds Crawled: 1
Total Seeds not Crawled: 0
Total Hosts Crawled: 1
Total URIs Processed: 1337
URIs Crawled successfully: 1337
URIs Failed to Crawl: 0
URIs Disregarded: 0
Processed docs/sec: 0.24
Bandwidth in Kbytes/sec: 4
Total Raw Data Size in Bytes: 23865329 (23 MB)
Novel Bytes: 23877375 (23 MB)
```

###### hosts-report.txt

Contém uma visão geral dos hosts que foram rastreados. Também mostra o número de documentos rastreados e a quantidade de bytes baixados por cada host.

O arquivo é criado pelo bean StatisticsTracker e é escrito no fim do rastreamento. 

Exemplo de um output desse arquivo:

```
1337 23877316 www.smokebox.net 0 0
1 59 dns: 0 0
0 0 dns: 0 0
```

###### mimetype-report.txt

Contém um relatório mostrando o número de documentos e dados baixados por tipo mime.

O arquivo é criado pelo bean StatisticsTracker e é escrito no fim do rastreamento. 

Exemplo de uma saída desse arquivo:

```
624 13248443 image/jpeg
450 8385573 text/html
261 2160104 image/gif
1 74708 application/x-javascript
1 59 text/dns
1 8488 text/plain
processors-report.txt
```

Contém o relatório dos processadores, que mostra a atividade de cada processador do Heritrix. Para mais informações, ver Cadeias de Processamento. É escrito no fim do rastreamento. 

Exemplo de uma saída desse arquivo:

```
CandidateChain - Processors report - 200910300032
  Number of Processors: 2

Processor: org.archive.crawler.prefetch.CandidateScoper

Processor: org.archive.crawler.prefetch.FrontierPreparer


FetchChain - Processors report - 200910300032
  Number of Processors: 9

Processor: org.archive.crawler.prefetch.Preselector

Processor: org.archive.crawler.prefetch.PreconditionEnforcer

Processor: org.archive.modules.fetcher.FetchDNS

Processor: org.archive.modules.fetcher.FetchHTTP
  Function:          Fetch HTTP URIs
  CrawlURIs handled: 1337
  Recovery retries:   0

Processor: org.archive.modules.extractor.ExtractorHTTP
  Function:          Extracts URIs from HTTP response headers
  CrawlURIs handled: 1337  Links extracted:   0

Processor: org.archive.modules.extractor.ExtractorHTML
  Function:          Link extraction on HTML documents
  CrawlURIs handled: 449
  Links extracted:   6894

Processor: org.archive.modules.extractor.ExtractorCSS
  Function:          Link extraction on Cascading Style Sheets (.css)
  ExtractorURIs handled: 0
  Links extracted:   0

Processor: org.archive.modules.extractor.ExtractorJS
  Function:          Link extraction on JavaScript code
  CrawlURIs handled: 1
  Links extracted:   19

Processor: org.archive.modules.extractor.ExtractorSWF
  Function:          Link extraction on Shockwave Flash documents (.swf)
  CrawlURIs handled: 0
  Links extracted:   0


DispositionChain - Processors report - 200910300032
  Number of Processors: 3

Processor: org.archive.modules.writer.WARCWriterProcessor

Processor: org.archive.crawler.postprocessor.CandidatesProcessor

Processor: org.archive.crawler.postprocessor.DispositionProcessor
```

###### responsecode-report.txt

Contém um relatório que exibe o número de documentos baixados por código de status por código de status bem-sucedido. Para códigos de falha, consulte o arquivo crawl.log.

O arquivo é criado pelo bean StatisticsTracker e é escrito no fim do rastreamento. 

Exemplo de uma saída desse relatório:

```
[#urls] [rescode]
1306 200
31 404
1 1
```

###### seeds-report.txt

Contém o status de rastreamento de cada seed.

O arquivo é criado pelo bean StatisticsTracker e é escrito no fim do rastreamento. 

Exemplo de uma saída desse relatório:

```
[code] [status] [seed] [redirect]
200 CRAWLED http://www.smokebox.net
```

###### frontier-summary-report.txt

Este relatório contém um detalhamento da atividade de frontier por thread (encadeamento de execução). Para cada thread em execução, o status da fila frontier pode ser examinado.

Exemplo de uma saída desse relatório:

```
-----===== RETIRED QUEUES =====-----
internet-archives-macbook-pro:reports hstern$
internet-archives-macbook-pro:reports hstern$ cat frontier-summary-report.txt
Frontier report - 201001142216
 Job being crawled: basic

 -----===== STATS =====-----
 Discovered:    3
 Queued:        0
 Finished:      3
  Successfully: 2
  Failed:       0
  Disregarded:  1

 -----===== QUEUES =====-----
 Already included size:     3
               pending:     0

 All class queues map size: 1
             Active queues: 1
                    In-process: 0
                         Ready: 1
                       Snoozed: 0
           Inactive queues: 0 (p1: 0)
            Retired queues: 0
          Exhausted queues: 0

 -----===== MANAGER THREAD =====-----
Java Thread State: RUNNABLE
Blocked/Waiting On: NONE
    java.lang.Thread.getStackTrace(Thread.java:1460)
    org.archive.crawler.framework.ToeThread.reportThread(ToeThread.java:476)
    org.archive.crawler.frontier.WorkQueueFrontier.standardReportTo(WorkQueueFrontier.java:1424)
    org.archive.crawler.frontier.WorkQueueFrontier.reportTo(WorkQueueFrontier.java:1257)
    org.archive.crawler.reporting.FrontierSummaryReport.write(FrontierSummaryReport.java:41)
    org.archive.crawler.reporting.StatisticsTracker.writeReportFile(StatisticsTracker.java:973)
    org.archive.crawler.reporting.StatisticsTracker.dumpReports(StatisticsTracker.java:1000)
    org.archive.crawler.reporting.StatisticsTracker.crawlEnded(StatisticsTracker.java:572)
    org.archive.crawler.reporting.StatisticsTracker.onApplicationEvent(StatisticsTracker.java:1044)
    org.springframework.context.event.SimpleApplicationEventMulticaster$1.run(SimpleApplicationEventMulticaster.java:78)
    org.springframework.core.task.SyncTaskExecutor.execute(SyncTaskExecutor.java:49)
    org.springframework.context.event.SimpleApplicationEventMulticaster.multicastEvent(SimpleApplicationEventMulticaster.java:76)
    org.springframework.context.support.AbstractApplicationContext.publishEvent(AbstractApplicationContext.java:274)
    org.archive.crawler.framework.CrawlController.sendCrawlStateChangeEvent(CrawlController.java:323)
    org.archive.crawler.framework.CrawlController.completeStop(CrawlController.java:375)
    org.archive.crawler.framework.CrawlController.noteFrontierState(CrawlController.java:639)
    org.archive.crawler.frontier.AbstractFrontier.reachedState(AbstractFrontier.java:484)
    org.archive.crawler.frontier.AbstractFrontier.managementTasks(AbstractFrontier.java:412)
    org.archive.crawler.frontier.AbstractFrontier$2.run(AbstractFrontier.java:328)

 -----===== LONGEST QUEUE =====-----
Queue com,fizzandpop,www, (p1)
  0 items
    last enqueued: http://www.fizzandpop.com/
      last peeked: http://www.fizzandpop.com/
   total expended: 2 (total budget: -1)
   active balance: 2998
   last(avg) cost: 1(1)
   totalScheduled fetchSuccesses fetchFailures fetchDisregards fetchResponses robotsDenials successBytes totalBytes fetchNonResponses
   3 2 0 1 2 1 383 383 2
   SimplePrecedenceProvider
   1


 -----===== IN-PROCESS QUEUES =====-----

 -----===== READY QUEUES =====-----
READY#0:
Queue com,fizzandpop,www, (p1)
  0 items
    last enqueued: http://www.fizzandpop.com/
      last peeked: http://www.fizzandpop.com/
   total expended: 2 (total budget: -1)
   active balance: 2998
   last(avg) cost: 1(1)
   totalScheduled fetchSuccesses fetchFailures fetchDisregards fetchResponses robotsDenials successBytes totalBytes fetchNonResponses
   3 2 0 1 2 1 383 383 2
   SimplePrecedenceProvider
   1


 -----===== SNOOZED QUEUES =====-----

 -----===== INACTIVE QUEUES =====-----

 -----===== RETIRED QUEUES =====-----
 ```
 
###### source-report.txt

Contém uma linha de item para cada host, que inclui o seed por qual o host foi alcançado.

Exemplo de uma saída desse relatório:

```
[source] [host] [#urls]
http://www.fizzandpop.com/ dns: 1
http://www.fizzandpop.com/ www.fizzandpop.com 1
```

Observação

* A propriedade `sourceTagSeeds` do bean `TextSeedModule` deve ser definida como "true" para gerar esse relatório.

```
<bean id="seeds" class="org.archive.modules.seeds.TextSeedModule">

<property name="textSource">

<bean class="org.archive.spring.ConfigString">

<property name="value">
<value>
# [see override above]
        </value>
</property>
</bean>
</property>
<property name="sourceTagSeeds" value="true"/>
</bean>
```

###### threads-report.txt

Contém a lista de threads ainda ativos no final do rastreamento, com informações detalhadas sobre cada um.

arquivos WARC 

Supondo que você esteja usando o escritor WARC que acompanha o Heritrix, vários arquivos WARC contendo conteúdo rastreado serão gerados.

O local de armazenamento de arquivos WARC pode ser especificado definindo o valor do `diretório` do bean `WARCWriterProcessor`.

Arquivos WARC são nomeados conforme a seguinte convenção:

[prefix][12 digit timestamp][series padded to 5 digits][crawler hostname].warc.gz

O WARCWriterProcessor contém a configuração de prefixo padrão IAH.

Arquivos WARC com o sufixo `.open` estão no processo de serem escritos pelo Heritrix. Pode haver vários WARCs abertos a qualquer momento.

Arquivos WARC com o sufixo `.invalid` indicam problemas de gravação do arquivo. Isso pode ser causado por um disco danificado ou um disco sem espaço. Em um problema de I/O, o Heritrix fecha o arquivo WARC problemático e adiciona o sufixo `.invalid`. Esses arquivos devem ser verificados quanto à coerência.

A partir da versão 3.1, o bean "LowDiskPauseProcessor" foi substituido pelo bean "DiskSpaceMonitor". Ao gravar arquivos WARC, o DiskSpaceMonitor verifica o espaço disponível dos caminhos configurados. Se o espaço livre estiver abaixo do limite definido, o rastreamento será pausado. No exemplo abaixo, o caminho `/warcs` é monitorado. Se o espaço livre for inferior a 500MB, os rastreamentos sendo gravados no diretório `/warcs` serão pausado.
```
<bean id="diskSpaceMonitor" class="org.archive.crawler.monitor.DiskSpaceMonitor"> <property name="pauseThresholdMiB" value="500" /> <property name="monitorConfigPaths" value="true" /> <property name="monitorPaths"> <list> <value>/warcs</value> </list> </property> </bean>
```

A partir da versão 3.1, a nomenclatura de arquivos WARC foi alterada. Em vez de especificar a fórmula para a nomenclatura ARC/WARC no código e usar um 'prefixo' e 'sufixo' fornecidos, um modelo com interpolação variável pode ser usado.  O "prefixo" configurado continua disponível como uma variável, assim como outras propriedades úteis de máquina, rastreamento e gravações. O modelo padrão é:

```
${prefix}-${timestamp17}-${serialno}-${heritrix.pid}~${heritrix.hostname}~${heritrix.port}
```

O modelo adiciona o processo de ID local e a data e hora de 17 digitos. O registro de data e hora é fornecido por um serviço que assegura que cada registro é feito pelo menos 1 milissegundo após os valores anteriores em milissegundos. A nova convenção padrão também minimiza as chances de colisões de nomes ARC/WARD, mesmo quando vários rastreamentos estão sendo iniciados ou executados simultaneamente na mesma máquina local, usando o mesmo prexifo de nome de arquivo. Apesar dos nomes gerados serem longos, eles provavelmente serão únicos sob condiçoes normais. Não é recomendado que o modelo seja alterado, a menos que seja certo que a nomenclatura alternativa fornecerá nomes únicos. Isso é importante porque as ferramentas downstream que indexam ARCs/WARCs geralmente pressupõem a exclusividade do nome do arquivo e podem se beneficiar de sua geração exclusiva.

## Casos comuns de uso do Heritrix

Existem várias maneiras de executar um rastreamento da web. Aqui listamos vários casos que ajudam na familiarização com alguns dos parâmetros de rastreamento mais usados do Heritrix.

### Arquivando conteúdo de mídia avançada

Essa seção do guia de usuário Heritrix examina os desafios que arquivistas de conteúdo de mídia avançada enfrentam e oferece sugestões específicas para optimizar o rastreador do Heritrix, fazendo com que ele lide com esses desafios.

Mídia avançada abrange diferentes tipos de conteúdo da web que proporcionam aos usuários uma experiência superior a de páginas de text/html normais, entre eles: vídeos, animações, imagens e áudios. Tecnologias interativas que vão além da entrada básica de dados FORM também são um tipo de mídia avançada: wikis, como a Wikipedia; tecnologias que fornecem aos usuários controle refinado sobre conteúdo da Web, como AJAX; tecnologia Flash, que expõe uma ampla variedade de widgets de interface de usuário que permitem arranjos espaciais de dados muito além dos recursos da primeira geração de Websites. Além disso, é caracterizada por arquivos de tamanho maior que páginas de `text/html` normais.

* Arquivos de grande tamanho

Conteúdos de mídia avançada, como o Flash e vídeos, normalmente são muito maiores do que páginas `text/html` normais. O rastreamento desse tipo de conteúdo requer grandes investimentos em armazenamento e largura de banda (bandwidth). Para diminuir esses problemas, é recomendada a desduplicação em rastreamentos desse tipo de conteúdo. A desduplicação identifica conteúdos coletados anteriormente que são redundantes e pula seus downloads. Pointers em conteúdos duplicados permitem que eles apareçam em rastreamentos subsequentes. Para mais detalhes ver Configurando o Heritrix para Desduplicação.

* Links incorporados em mídia avançada

Várias tecnologias de mídia avançada permitem que links sejam incorporados em formatos de arquivos que não são propícios à extração de links. Ao rastrear um site de conteúdo de mídia avançada, é importante identificar se há um site-map no site. Um site-map é uma página HTML que contém links de todas as páginas importantes do site. Ao adicionar a URI do site-map como uma seed para um rastreamento de mídia avançada, links que, caso contrário, não seriam extraídos, serão arquivados.

* Uso excessivo de memória e CPU

O download de conteúdo de mídia avançada pode causar uma carga excessiva na memória e na CPU dos computadores de rastreamento. Por exemplo, a extração de links do Flash e de outros recursos de mídia avançada exige análise extensiva de dados, o que exige muito da CPU.
Padrões de entrada atípicos também podem causar uso excessivo da CPU quando expressões regulares usadas pelo Heritrix são executadas. Portanto, é recomendado que os rastreamentos de mídia avançada recebam mais memória e CPU do que os rastreamentos "normais". A memória alocada ao Heritrix é definida a partir da linha de comando. O exemplo abaixo mostra a opção de linha de comando usada para alocar 1GB de memória ao Heritrix, o que deve ser suficiente para a maioria dos rastreamentos de mídia avançada:

```
export JAVA_OPTS=-Xmx1024M
```

Processadores multi-core também são recomendados para rastreamentos de mídia avançada.

* Mídia de transmissão (streaming media)

Mídia de transmissão é o conteúdo de mídia entregue seqüencialmente, ao longo do tempo, para um consumidor de mídia a partir de um produtor de mídia. Exemplos de mídia de transmissão incluem Internet, rádio e TV. Esse tipo de mídia está preocupado com o mecanismo de entrega do formato de mídia e não o formato em si. O Heritrix pode capturar mídia transmitida por HTTP ou FTP, mas não reconhece outros protocolos de streaming, como o RTSP (Real Time Streaming Protocol). Essa limitação gerou interesse em incorporar um media player no Heritrix que reconhece a maioria dos formatos de transmissão. Para obter mais informações sobre como incorporar um Media Player ao Heritrix, consulte o artigo "Archiving Streaming Media on the Web Proof of Concept and First Results" no documento da conferência International Web Archiving Workshop, 2006, em http://iwaw.europarchive.org/06/PDF/iwaw06-proceedings.pdf.

* Redes sociais

Vários sites de redes sociais utilizam mídia avançada para melhorar a experiência dos ususários. Para diretrizes específicas sobre como arquivar sites de redes sociais, ver Archiving Social Networking Sites with Archive-It. Essas instruções se aplicam ao aplicativo Archive-It, criado a partir do Heritrix.

### Como evitar solicitações falsas ao processar certos tipos de conteúdos

A partir da versão 3.1, melhorias foram feitas na capacidade do rastreador de determinar se uma string é um URI válido. Essas melhorias proporcionam uma extração melhor de links de conteúdos como o JavaScript não analisado/não interpretado. No entanto, essa técnica pode ser propensa a erros, causando problemas ou incomodações no website alvo. Na versão 3.1, essa funcionalidade pode ser desativada para rastreamentos completos ou site-por-site. Para desativá-la, é necessário remover a referência bean "extractorJs" do bean "fetchProcessors" e definir as propriedades "extractJavascript" e "extractValueAttributes" do "extractionHtml" como "false".

1. Remova a referência do bean "fetchProcessors" para "extractorJs".

```
<bean id="fetchProcessors" class="org.archive.modules.FetchChain">
<property name="processors">
<list>
<!-- re-check scope, if so enabled... -->
<ref bean="preselector"/>
<!--
...then verify or trigger prerequisite URIs fetched, allow crawling...
-->
<ref bean="preconditions"/>
<!-- ...fetch if DNS URI... -->
<ref bean="fetchDns"/>
<!-- <ref bean="fetchWhois"/> -->
<!-- ...fetch if HTTP URI... -->
<ref bean="fetchHttp"/>
<!-- ...extract outlinks from HTTP headers... -->
<ref bean="extractorHttp"/>
<!-- ...extract outlinks from HTML content... -->
<ref bean="extractorHtml"/>
<!-- ...extract outlinks from CSS content... -->
<ref bean="extractorCss"/>
<!-- ...extract outlinks from Javascript content... -->
<!-- ****** <ref bean="extractorJs"/> ****** -->
<!-- ...extract outlinks from Flash content... -->
<ref bean="extractorSwf"/>
</list>
</property>
</bean>

```

2. Defina as configurações "extractJS" e "extractValueAttributes" do bean "extractHtml" como "false".

```

<bean id="extractorHtml" class="org.archive.modules.extractor.ExtractorHTML">
<property name="extractJavascript" value="false" />
<property name="extractValueAttributes" value="false" />
</bean>

```

### Como evitar grandes quantidades de conteúdos dinâmicos

Suponha que você queira apenas rastrear páginas de um determinado host (http://www.foo.org/) e também evitar o rastreamento de muitas páginas de um calendário gerado dinamicamente. Vamos supor que o calendário é acessado ao fornecer um ano, mês e dia para o diretório do calendário. 

Quando você cria uma tarefa de rastreamento, apenas um seed é especificado: http://www.foo.org/.  Por padrão, a nova tarefa de rastreamento usará a DecideRuleSequence, que irá conter um conjunto padrão de DecideRules. Uma das regras padrão é a SurtPrefixedDecideRule, que informa ao Heritrix para aceitar qualquer URI que corresponda ao prefixo SURT do URI de origem: http://(org,foo,www,)/. Se a URI http://foo.org/ for encontrada, será rejeitada, já que seu prefixo SURT http://(org,foo,) não corresponte ao prefixo SURT do seed.  Para permitir que ambos foo.org e www.foo.org sejam capturados, você pode adicionar dois seeds: http://www.foo.org/ and http://foo.org/. Para permitir que todo subdomínio de foo.org seja rastreado, você pode adicionar a semente http://foo.org. Observe a ausência de uma barra à direita.

Apague o TranclusionDecideRule, pois essa regra tem o potencial de levar o Heritrix para outro host. Por exemplo, se um URI retornar um código de resposta 301 (mover permanentemente) ou 302 (encontrado), bem como um URI que contenha um nome de host diferente do que os seeds, o Heritrix aceitaria esse URI usando o TransclusionDecideRule. Remover essa regra fará com que o Heritrix não se afaste do nosso host www.foo.org.

PathologicalPathDecideRule e TooManyPathSegmentsDecideRule permitirão que o Heritrix evite alguns tipos de armadilhas de rastreamento. TooManyHopsDecideRule fará com que o Heritrix não se distancie muito do seed. Dessa forma, o Heritrix não será preso num loop infinito pelo calendário.  Por padrão, o máximo de hops é definido como 20, mas esse valor pode ser alterado editando o arquivo crawler-beans.cxml.

Como alternativa, você pode adicionar o MatchesFilePatternDecideRule. Defina usePresetPattern como CUSTOM e defina o regexp como: .foo \ .org (?! / Calendar). |. * Foo \ .org / calendar? Year = 200 [56]. *

### Espelhamento de arquivos HTML

Suponha que você queira apenas rastrear URIs que correspondam a http://foo.org/bar/\*.html e salvar os arquivos rastreados em um formato de arquivo/diretório em vez de arquivos WARC. Além disso, suponha que o servidor da Web faz distinção entre maiúsculas e minúsculas.  Por exemplo, http://foo.org/bar/abc.html e http://foo.org/bar/ABC.HTML estão apontando para dois recursos diferentes.

Primeiro, crie uma tarefa com apenas um seed, http://foo.org/bar/. Configure o bean warcWriter para que sua classe seja org.archive.modules.writer.MirrorWriterProcessor. Esse Processador armazenará arquivos em uma estrutura de diretórios que corresponda aos URIs rastreados. Os arquivos serão armanezados na cópia do diretório da tarefa de rastreamento.  

### Armazenamento de páginas HTML bem-sucedidas

Suponha que você queira capturar as primeiras 50 páginas encontradas de um conjunto de seeds e arquivar apenas as páginas que retornam código de resposta 200 e text/html de tipo mime.  Além disso, você quer procurar links somente em recursos HTML.

Para verificar links apenas em documentos HTML, você precisará remover os seguintes *extractors* que informam ao Heritrix para procurar links em planilhas de estilo, JavaScript e arquivos Flash:

* ExtractorCss
* ExtractorJs
* ExtractorSwf

Mantenha o ExtractorHttp, já que é útil para localizar recursos que só podem ser encontrados usando um redirecionamento (código de resposta 301 ou 302).

O número de URIs baixados pode ser limitado definindo a propriedade maxDocumentsDownload no bean crawlLimiter. Configurar o valor para 50 provavelmente não fornecerá os resultados pretendidos. Já que cada resposta DNS e arquivo robots.txt são contados no número, o valor deve ser definido para 50 * o número de seeds * 2.

## Tarefas (jobs)

Uma tarefa (às vezes chamada de tarefa de rastreamento) é uma configuração que define a forma como um rastreamento é executado. Uma tarefa contém informações como os seeds a serem rastreados, o endereço de e-mail do operador e outras informações de configuração que determinam como os dados de seed são acessados e armazenados. Um rastreamento é uma isntância executada de uma tarefa.

No Heritrix, uma tarefa é baseada no Framework Spring. Beans Spring representam os atributos de uma tarefa. O snippet abaixo mostra um bean Spring que faz parte de uma configuração de tarefa (os beans Spring são definidos no formato XML). O bean tem uma propriedade que é configurada para os URIs dos seeds a serem rastreados. 

```
<bean id="longerOverrides" class="org.springframework.beans.factory.config.PropertyOverrideConfigurer">
<property name="properties">
<props>
      <prop key="seeds.textSource.value"># URLS HERE http://example.example/example</prop>
</props>
</property>
</bean>
```

## Análise de tarefa

O Heritrix oferece várias opções para examinar os detalhes de um rastreamento. Esses relatórios e logs também estão disponíveis durante o tempo de execução.

### Logs

Cada tarefa de rastreamento tem seu próprio conjunto de arquivos de logs.

Os logs podem sem encontrados no diretório "logs", que existe sob o diretório de uma tarefa específica. A localização de arquivos logs específicos é fornecida na seção "Configuration-referenced paths" da página da tarefa.

###### Propriedades de logs

As propriedades de log podem ser configuradas modificando o arquivo logging.properties localizado no diretório ./conf. Para obter informações sobre como usar propriedades de log, visite http://logging.apache.org/log4j/.

###### Arquivos log

**alerts.log**

Esse log contém alertas que indicam problemas com os rastreamentos.

**crawl.log**

Cada URI que o Heritrix tenta buscar fará com que uma linha de log seja gravada no arquivo crawl.log. Abaixo está um extrato de duas linhas do log.

```
2011-06-23T17:12:08.802Z   200       1299 http://content-5.powells.com/robots.txt LREP http://content-5.powells.com/cgi-bin/imageDB.cgi?isbn=9780385518635 text/plain #014 20110623171208574+225 sha1:YI
UOKDGOLGI5JYHDTXRFFQ5FF4N2EJRV - -
2011-06-23T17:12:09.591Z   200      15829 http://www.identitytheory.com/etexts/poetics.html L http://www.identitytheory.com/ text/html #025 20110623171208546+922 sha1:7AJUMSDTOMT4FN7MBFGGNJU3Z56MLCMW
- -
```

| Nome do campo  | Descrição |
| ------------- | ------------- |
| Timestamp  | Registro de data e hora no formato ISO8601, com resolução em milissegundos. A hora é o instante do registro. |
| Fetch Status Code  | Normalmente, esse é o código de resposta HTTP, mas também pode ser um número negativo se o processamento de URI tiver sido encerrado inesperadamente.  |
| Tamanho do documento | Tamanho do documento baixado, em bytes. Para HTTP, é apenas o tamanho do conteúdo. O tamanho exclui os cabeçalhos de resposta HTTP. Para DNS, o campo de tamanho é o tamanho total da resposta do DNS. |
| URI baixado | O URI do documento baixado. |
| Caminho de Descoberta | Códigos de navegação (caminho de descoberta) que mostram a trilha de downloads que levam ao URI baixado. A partir da versão 3.1, o comprimento do caminho de descoberta foi limitado aos últimos 50 hop-types.  Por exemplo, um caminho de 62-hop pode aparecer, a partir de agora, como "12+LLRLLLRELLLLRLLLRELLLLRLLLRELLLLRLLLRELLLLRLLLRELE".  Esse aprimoramento diminui o tamanho do log e limita o uso de memória. Os códigos de navegação são os seguintes. |
| Referenciador | O URI que precedeu imediatamente o URI baixado. Este é o referenciador. O caminho de descoberta e o referenciador estarão vazios para URIs de seeds. |
| Mime Type | Documento baixado do tipo mime. |
| Worker Thread ID | Identidade do worker thread que baixou o documento. |
| Fetch timestamp | O registro de data e hora no formato somente de dígitos condensados RFC2550/ARC, indicando quando a busca de rede foi iniciada. Se apropriado, a duração de milissegundos da busca é anexada ao registro de data e hora com um caractere "+" como separador. |
| SHA1 Digest | O digest do SHA1 (apenas de conteúdo, cabeçalhos não possuem digest). |
| Source Tag | A tag de origem herdada pelo URI, se a marcação de origem estiver ativada. |
| Annotations | Se uma anotação tiver sido feita, ela será exibida. Possíveis anotações incluem: o número de vezes que o URI foi tentado, o "lenTrunc" literal se o download foi truncado devido a limites de tamanho excedidos configurados, o "timeTrunc" literal se o download foi truncado devido a limites de tempo excedidos configurados, ou "midFetchTrunc" se um filtro midfetch determinar que o download deve ser truncado. |
| warc | Nome do arquivo WARC/ARC em que o conteúdo rastreado foi salvo. Esse valor só será salvo se a propriedade logExtraInfo do bean loggerModule está definida como true. Essa informação registrada será salva no formato JSON. |

**progress-statistics.log**

Esse log é salvo pelo bean StatisticsTracker.  Em intervalos configuráveis, uma linha de log detalhando o progresso do rastreamento é gravada nesse arquivo.

| Nome do campo  | Descrição |
| ------------- | ------------- |
| timestamp  | Registro de data e hora no formato ISO8601, indicando quando a linha de log foi gravada.  |
| discovered  | Número de URIs descobertos até o momento. |
| queued | Número de URIs enfileirados. |
| downloaded | Número de URIs baixados até o momento. |
| doc/s(avg) | Número de documentos baixados por segundo desde a última snapshot. O valor entre parênteses é medido desde o início do rastreamento. |
| KB/s(avg) | Quantidade em kilobytes baixados por segundo desde a última snapshot. O valor entre parênteses é medido desde o início do rastreamento. |
| dl-failures | Número de URIs que o Heritrix não conseguiu baixar. |
| busy-thread | Número de toe threads ocupados processando um URI. |
| mem-use-KB | Quantidade de memória sendo usada pelo Java Virtual Machine. |
| heap-size-KB| O tamanho de heap atual da Java Virtual Machine. |
|congestion | O índice de congestionamento é uma estimativa aproximada de quanta capacidade inicial, como um múltiplo da capacidade atual, seria necessária para rastrear a carga de trabalho atual, na taxa máxima disponível em determinadas configurações de politeness. Esse valor é calculado comparando o número de filas internas que estão progredindo em relação àquelas que estão aguardando a disponibilização de um thread. |
| max-depth | O tamanho da fila Frontier com o maior número de URIs enfileirados.  |
| avg-depth | O tamanho habitual de todas as filas Frontier. |

**runtime-errors.log**

Esse log captura exceções e erros inesperados que ocorrem durante o rastreamento, que podem ser resultantes de limitações do hardware (sem memória, embora esse erro possa ocorrer sem ser gravado neste log). No entanto, a maioria ocorre por causa de bugs do software, talvez no núcleo do Heritrix, mas provavelmente em uma de suas classes conectáveis.

**uri-errors.log**

Esse log armazena erros de tentativas de busca de URI, normalmente causados por URIs não existentes. Esse log normalmente é de interesse apenas para usuários avançados que tentam explicar comportamentos inesperados de rastreamento.

**frontier.recover.gz**

O arquivo frontier.recover.gz é um log gzipado de eventos Frontier que pode ser usado para restaurar a Frontier após uma falha.

### Relatórios

Os relatórios são encontrados no diretório "reports", que existe no diretório de uma tarefa específica. A localização dos arquivos de relatório específicos é fornecida na seção "Configuration-referenced paths" da página da tarefa.

###### Resumo de rastreamento (crawl-report.txt)

| Nome do campo  | Descrição |
| ------------- | ------------- |
| Crawl Name | O nome do rastreamento, definido pelo usuário. |
| Crawl Status | O status do rastreamento, como "Aborted" (abortado) ou "Finished" (concluído). |
| Duration Time | A duração do rastreamento até o milissegundo mais próximo. |
| Total Seeds Crawled | O número de seeds que foram rastreados com sucesso. |
| Total Seeds Not Crawled | O número de seeds que não foram rastreados com sucesso. |
| Total Hosts Crawled | O número de hosts que foram rastreados. |
| Total URIs Processed | O número de URIs que foram processados. |
| URIs Crawled Successfully | O número de URIs que foram rastreados com sucesso. |
| URIs Failed to Crawl | O número de URIs que não puderam ser rastreados. |
| URIs Disregarded | O número de URIs que não foram selecionados para rastreamento. |
| Processed docs/sec | O número médio de documentos processados por segundo. |
| Bandwidth in Kbytes/sec | O número médio de kilobytes processados por segundo. |
| Total Raw Data Size in Bytes | A quantidade total de dados rastreados. |
| Novel Bytes | Novos bytes desde o último rastreamento. |

###### Seeds (seeds-report.txt)

| Nome do campo  | Descrição |
| ------------- | ------------- |
| code | | 0 = não rastreado 1 = rastreado |
| status | Descrição legível informando se o seed foi rastreado. Por exemplo, "CRAWLED". |
| seed | O URI seed. |
| redirect | O URI para o qual o seed foi redirecionado. |

###### Hosts (hosts-report.txt)

| Nome do campo  | Descrição |
| ------------- | ------------- |
| #urls | O número de URIs rastreados para o host. |
| #bytes | O número de bytes rastreados para o host. |
| host | O nome do host. |
| #robots | O número de URIs, para este host, excluídos por causa das restrições do `robots.txt`. Esse número não inclui URIs vinculados aos URIs especificamente excluídos. |
| #remaining | O número de URIs, para esse host, que ainda não foram rastreados, mas estão na fila. |
| #novel-urls | O número de novos URIs rastreados para este host desde o último rastreamento. |
| #novel-bytes | A quantidade de novos bytes rastreados para este host desde o último rastreamento. |
| #dup-by-hash-urls | O número de URIs, para esse host, que tinham o mesmo hashcode e são, essencialmente, duplicados. |
| #dup-by-hash-bytes | O número de bytes de conteúdo, para este host, com o mesmo hashcode. |
| #not-modified-urls | O número de URIs, para esse host, que retornou um código de status 304. |
| #not-modified-bytes | A quantidade de bytes de conteúdo, para este host, cujos URIs retornaram um código de status 304. |

###### SourceTags (source-report.txt)

| Nome do campo  | Descrição |
| ------------- | ------------- |
| source | O seed. |
| host | O host que foi acessado pelo seed. |
| #urls | O número de URIs rastreados para essa combinação de host de origem. |

Observe que o relatório SourceTags só será gerado se a propriedade `sourceTagSeeds` do bean `TextSeedModule` estiver definida como true.

###### Mimetypes (mimetype-report.txt)

| Nome do campo  | Descrição |
| ------------- | ------------- |
| #urls | O número de URIs rastreados para um determinado tipo MIME. |
| #bytes | O número de bytes rastreados para um determinado tipo MIME. |
| mime-types | O tipo MIME. |

###### ResponseCode (responsecode-report.txt)

| Nome do campo  | Descrição |
| ------------- | ------------- |
| #urls | O número de URIs rastreados para um determinado código de resposta. |
| rescode | O código de resposta. |

###### Processors (processors-report.txt)

Este relatório mostra a atividade de cada processador envolvido no rastreamento. Por exemplo, o processador `FetchHTTP` é incluído no relatório. Para este processador, o número de URIs buscados é exibido. O relatório é organizado para informar sobre cada cadeia (Candidadate, Fetch e Disposition) e cada processador em cada cadeia. A ordem do relatório é de acordo com a ordem de configuração no arquivo crawler-beans.cxml.

###### FrontierSummary (frontier-summary-report.txt)

Este link exibe um relatório mostrando os hosts que estão na fila para serem capturados. Os hosts estão contidos em várias filas. Os detalhes de cada fila Frontier são relatados.

###### ToeThreads (threads-report.txt)

Este link exibe um relatório mostrando a atividade de cada thread usado pelo Heritrix. A quantidade de tempo que o thread está sendo executado é exibida, bem como o estado e o status Blocked/Waiting do thread.




## Configuração de Tarefas e Perfis

A criação de tarefas (Creating a Job) e perfis (Creating a Profile) é o primeiro passo no processo de usar o Heritrix para rastrear a web. Configurar tarefas e perfis é um trabalho um pouco mais complicado. A seção a seguir se aplica para a configuração tanto de tarefas quanto de perfis.

###### Note

* Para editar um rastreamento em andamento, ver Editing a Running Job para mais informações.

Configurar uma tarefa ou um perfil involve a edição do arquivo `crawler-beans.cxml`. Esse arquivo é um arquivo de configuração Spring. O Framework Spring é usado para definir as propriedades de tarefas e perfis. Cada tarefa é definida por "beans" Spring que contêm dados de configuração para a tarefa. Essa seção aborda como configurar propriedades comuns de uma tarefa de rastreamento ou perfil e descreve as várias seções do arquivo `crawler-beans-cxml`.

A primeira seção do arquivo `crawler-beans-cxml` permite que o operador altere qualquer propriedade simples do bean, como a `metadata.operatorContactUrl`. Por exemplo, o Heritrix pode ser configurado para ignorar cookies com a seguinte substituição de configuração: 

###### Sobreposição da propriedade de ignorar cookies

```
<!-- overrides from a text property list -->
<bean id="simpleOverrides" class="org.springframework.beans.factory.config.PropertyOverrideConfigurer">
<property name="properties">
<value>

# This Properties map is specified in the Java 'property list' text format
# http://java.sun.com/javase/6/docs/api/java/util/Properties.html#load%28java.io.Reader%29

metadata.operatorContactUrl=http://www.archive.org
metadata.jobName=basic
metadata.description=Basic crawl starting with useful defaults
ignoreCookies=true

##..more?..##

</value>
</property>
</bean>
```

O bean "longerOverrides" está disponível para sobreposições mais longas ou complicadas. É usado para substituir propriedades que contêm múltiplos valores ou que podem ser alteradas com um bean. Por exemplo, vários seeds podem ser configurados com a seguinte configuração:

### Sobreposição dos valores dos seeds

```
<bean id="longerOverrides" class="org.springframework.beans.factory.config.PropertyOverrideConfigurer">

<property name="properties">

<props>
<prop key="seeds.textSource.value">

# URLS HERE
http://www.myhost1.net
http://www.myhost2.net
http://www.myhost3.net/pictures

    </prop>
</props>
</property>
```

## Cadeias de processamento

No nível de tarefa, uma tarefa de rastreamento do Heritrix possui três pipelines principais conhecidas como Cadeias de Processadores (aplicação sequencial de módulos de Processador trocáveis -- ver Configurações de Processador), com a Frontier atuando como um buffer entre as duas primeiras:

* Chain Candidates:
    * Processa os URIs de rastreamento de entrada, decidindo se deve mantê-los (de acordo com o escopo) e se eles serão depositados na Frontier.
    * Ver [Processadores da Candidate Chain] (https://github.com/internetarchive/heritrix3/wiki/Candidate-Chain-Processors)
    
* Frontier:
    * Os URIs de rastreamento aceitos nesse rastreamento são armazenados aqui em ordem de prioridade, em um conjunto de filas distintas.
    * Normalmente, há uma fila por "autoridade" (por exemplo, `exemplo.com:80`) e o gerenciamento de filas garante que o atraso de rastreamento desejado seja respeitado para cada fila.
    * Ver [Frontier] (https://github.com/internetarchive/heritrix3/wiki/Frontier)

* Fetch Chain:
    * Como os URIs de rastreamento são emitidos pela Frontier, a cadeia de busca processa cada um deles e decide o que fazer, como baixar etc.
    * Também realiza operações como extração de links.
    * Ver [Processadores da Fetch Chain] (https://github.com/internetarchive/heritrix3/wiki/Fetch-Chain-Processors)

* Disposition Chain:
    * Trata de pós-processamentos necessários após o termino da cadeia de busca.
    * Por exemplo, é aqui que os recursos baixados são salvos no formato WARC.
    * Ver [Processadores da Disposition Chain] (https://github.com/internetarchive/heritrix3/wiki/Disposition-Chain-Processors)

Todo URI retirado da fila Frontier é executado pelas cadeias de processamento. Os URIs são sempre processados na ordem mostrada no diagrama abaixo, a menos que um processador em particular exiba um erro fatal ou decida interromper o processamento do URI atual.

Cada cadeia de processamento é composta de zero ou mais processadores individuais. Por exemplo, o FetchChain pode incluir os processadores extractorCss e extractorJs. Em uma etapa de processamento, a ordem em que os processadores são executados é a ordem em que eles são listados no arquivo crawler-beans.cxml.

### Processadores da Candidate Chain

| Nome do processador  | Descrição |
| ------------- | ------------- |
| candidateScoper | Aplica regras de escopo a cada URI candidato. |
| preparer | Prepara os URIs aceitos para enfileirar na Frontier.  |

### Processadores da Fetch Chain

| Nome do processador  | Descrição | Nome da classe |
| ------------- | ------------- | ------------- |
| preparer | Prepara os URIs aceitos para enfileirar na Frontier.  | |
| preconditions | Verifica ou aciona a busca de URIs de pré-requisito.  | |
| fetchDns | Busca DNS URIs.  | |
| fetchHttp | Busca os URIs HTTP.  A partir da versão 3.1, o rastreador decodificará corretamente a codificação de transferência 'em partes' ('chunked' transfer-encoding) - mesmo se encontrada quando não deveria ser usada, como em uma resposta a uma solicitação HTTP/1.0. Além disso, o processador fetchHttp agora inclui o parâmetro 'useHTTP11', que, se true, fará com que o Heritrix relate suas solicitações como 'HTTP/1.1'. Isso permite que os sites utilizem a codificação de transferência 'em partes'. (O padrão para esse parâmetro é, no momento, false, e o Heritrix ainda não reutiliza conexões persistentes para mais de uma solicitação para um site.) O fetchHttp também inclui o parâmetro 'acceptCompression', que, se for true, fará com que os pedidos do Heritrix incluam um cabeçalho "Accept-Encoding:gzip,deflate", que se oferece para receber respostas compactadas. (O padrão para este parâmetro, no momento, é false.)  | |
| extractorHttp | Extrai links de cabeçalhos HTTP. A partir da versão 3.1, o processador extractorHttp utiliza qualquer URI em um nome de host para indicar que o '/favicon.ico' do mesmo host deve ser buscado. Ainda a partir da versão 3.1, a propriedade "inferRootPage" foi adicionada ao bean extractorHttp. Se essa propriedade for "true", o Heritrix inferirá a página raiz '/' de qualquer outro URI no mesmo nome de host. O padrão para essa configuração é "false" (comportamento pré-3.1 de buscar apenas a página raiz se ela for um seed ou se for descoberta de alguma outra forma e o escopo no objeto permanecer em vigor. A descoberta por meio dessas novas heurísticas é considerada um novo tipo de hop 'I' (inferred) e é tratada da mesma forma em decisões de escopo/transclusão como 'E' (embed).  | org.archive.modules.extractor.ExtractorHTTP |
| extractorHtml | Extrai links de conteúdos HTML.  | org.archive.modules.extractor.ExtractorHTML |
| extractorCss | Extrai links de conteúdos CSS.  | org.archive.modules.extractor.ExtractorCSS |
| extractorJs | Extrai links de conteúdos JavaScript. | org.archive.modules.extractor.ExtractorJs |
| extractorSwf | Extrai links de conteúdos Flash.  | org.archive.modules.extractor.ExtractorSWF |
| extractorPdf | Extrai links de conteúdos PDF.  | org.archive.modules.extractor.ExtractorPDF |
| fetchXml | Extrai links de conteúdos XML.  | org.archive.modules.extractor.ExtractorXML |

A maioria dos processadores de extração é pré-configurada no arquivo de configuração `crawler-beans.cxml` da tarefa sob o bean "fetchProcessors". Para adicionar um novo extrator, como um extrator XML/RSS, defina o bean e, em seguida, vincule-o ao bean "fetchProcessors". Exemplo de um bean extractorXml:

1. Defina o bean para o extrator XML/RSS:

``` 
<bean id="extractorXml" class="org.archive.modules.extractor.ExtractorXML"></bean>
```

2. Vincule o bean "extractorXml" com o bean "fetchProcessor":

```
<bean id="fetchProcessors" class="org.archive.modules.FetchChain">
<property name="processors">
<list>
<!-- re-check scope, if so enabled... -->
<ref bean="preselector"/>
<!--
...then verify or trigger prerequisite URIs fetched, allow crawling...
-->
<ref bean="preconditions"/>
<!-- ...fetch if DNS URI... -->
<ref bean="fetchDns"/>
<!-- <ref bean="fetchWhois"/> -->
<!-- ...fetch if HTTP URI... -->
<ref bean="fetchHttp"/>
<!-- ...extract outlinks from HTTP headers... -->
<ref bean="extractorHttp"/>
<!-- ...extract outlinks from HTML content... -->
<ref bean="extractorHtml"/>
<!-- ************ ...extract outlinks from XML/RSS content.. ********** -->
<ref bean="extractorXml"/>
<!-- ...extract outlinks from CSS content... -->
<ref bean="extractorCss"/>
<!-- ...extract outlinks from Javascript content... -->
<ref bean="extractorJs"/>
<!-- ...extract outlinks from Flash content... -->
<ref bean="extractorSwf"/>
</list>
</property>
</bean>
```

### Processadores da cadeia de disposição (Disposition Chain Processors)

| Nome do processador  | Descrição |
| ------------- | ------------- | 
| warcWriter| Grava os arquivos WARC de arquivamento. |
| preconditions | Envia os links de saída do URI buscado para a cadeia de candidatos para processamento.  |
| fetchDns | Atualiza estatísticas de rastreamento, estruturas de dados e decisões da Frontier.  |

### Configurações de processador

Os processadores têm configurações que efetuam rastreamentos. Cada processador pode ser ativado ou desativado. Se desativado, o Processador não será aplicado a nenhum URI. Os seguintes processadores possuem configurações padrões configuradas pelo Heritrix.

###### preparer

* preferenceDepthHops -  Número de hops (de qualquer tipo) de um seed até a qual um URI tem uma prioridade de agendamento maior que qualquer seed restante. Por exemplo, se definido como 1, os itens a um hop (link, embed, redirect, etc.) de distância de um seed serão agendados com prioridade ALTA (high). Se definido como -1, nenhuma preferência ocorrerá e uma busca em largura (bread-first search) com seeds processados antes dos links descobertos continuará. Se definido como 0, apenas uma busca em profundidade (depth-first search) prosseguirá, com todos os links descobertos processados antes dos seeds remanescentes. Redirecionamentos de seeds são considerados um hop de distância de um seed.

* preferenceEmbedHops - Número de *embed hops* (ERX) para mover para a frente da fila ho host.

* canonicalizationPolicy - Lista ordenada de regras de canonização de URI. As regras são aplicadas na ordem listada, de cima para baixo.

* queueAssignmentPolicy - Define como atribuir URIs a filas. Pode atribuir por host, por IP, pela autoridade ordenada por SURT, pela autoridade ordenada por SURT truncada em um domínio atribuível mais alto e em um de um conjunto fixo de buckets (1k).

* uriPrecedencePolicy - Define um valor de precedência inteiro em URIs individuais quando eles são submetidos pela primeira vez a uma frontier para agendamento. A precedência de um URI afeta diretamente a fila de URI em que ele é colocado, mas não afeta a precedência de uma fila em relação a outras filas, a menos que seja escolhida uma política de precedência de fila que consulte valores de precedência de URI.

* costAssignmentPolicy - Calcula um valor de 'custo' total para o CrawlURI fornecido.
preselector

* recheckScope - Verifica novamente se o URI está no escopo. Isso é significativo se o escopo for alterado durante um rastreamento. Quando os URIs são adicionados às filas, eles são verificados no escopo. Definir esse valor como "true" força o URI a ser verificado em relação ao escopo quando sai da fila, possivelmente após o escopo ser alterado. 

* blockAll- Bloqueia todos os URIs de serem processados. É mais provável que seja usado em sobreposições para rejeitar facilmente determinados hosts de serem processados.

* blockByRegex - Bloqueia todos os URIs correspondentes à expressão regular de serem processados.

* allowByRegex - Permite que apenas URIs que correspondam à expressão regular sejam processados.

###### preconditions

* ipValidityDurationSeconds - O intervalo mínimo para o qual um registro de DNS será considerado válido (em segundos). Se o DNS TTL do registro for maior, ele será usado no lugar.

* robotsValidityDurationSeconds- O tempo em segundos que as informações obtidas do robots.txt são consideradas válidas. Se o valor estiver definido como '0', as informações do robots.txt nunca irão expirar.

* calculateRobotsOnly - Se deve calcular o status *robot* de um URI, sem aplicar quaisquer exclusões encontradas. Se for true, os URIs excluídos serão apenas anotados no arquivo crawl.log, mas ainda assim obtidos.

###### fetchDns

* acceptNonDnsResolves - Se deve ou não retornar à resolução InetAddress se uma pesquisa de DNS falhar. A resolução InetAddress pode usar arquivos 'hosts' locais ou outros mecanismos.

* digestContent - Se deve ou não fazer um digest instântaneo de um hash de conteúdo-de-corpo recuperado.

* digestAlgorithm - O algoritimo (por exemplo, MD5 ou SHA-1) usado para fazer o digest instântaneo de um hash de conteúdo-de-corpo recuperado.


###### fetchHttp

* timeoutSeconds - Determina por quanto tempo uma solicitação HTTP aguardará um recurso responder. Essa configuração deve ser configurada para um valor alto.

* maxLengthBytes - Determina o número máximo de bytes para download por documento. Quando o limite é atingido, o documento será truncado. Por padrão, essa configuração é um valor muito grande (no intervalo exabyte), que teoricamente nunca será atingido.

* maxFetchKBSec - A taxa máxima em KB/s para usar ao buscar dados de um servidor. Se o padrão for 0, significa que não há um valor máximo. 

* defaultEncoding - A codificação de caracteres a ser usada para arquivos que não possuem um especificado nos cabeçalhos de resposta HTTP. O padrão é ISO-8859 -1.

* shouldFetchBodyRule- DecideRules aplicado após o recebimento dos cabeçalhos de resposta HTTP, mas antes do download do corpo HTTP. Se alguma regra retornar FALSE, a busca será cancelada. Pré-requisitos, como o robots.txt, são excluídos da filtragem, ou seja, não podem ser interrompidos pelo midfetch.

* soTimeoutMs - Se o *socket* não responder por esse determinado número de milissegundos, a solicitação será cancelada. Definir o valor como zero (sem tempo limite) não é recomendado, pois poderia travar um thread em um servidor que não responde. Esse tempo limite é usado para limitar o tempo de abertura e leituras de *socket*. Certifique-se de que esse valor seja menor que timeoutSegundos para a configuração ideal. Isso garante pelo menos uma nova tentativa de leitura.

* sendIfModifiedSince - Envia o cabeçalho If-Modified-Since, se as informações do histórico de busca do Last-Modified estiverem disponíveis no histórico de URIs.

* sendIfNoneMatch - Envia o cabeçalho If-None-Match, se as informações anteriores do histórico de buscas do Etag estiverem disponíveis no histórico de URIs.

* sendConnectionClose - Enviar conexão: fecha o cabeçalho com todas as solicitações. - Documentação do cabeçalho de conexão w3.org

* sendReferer- O cabeçalho do Referer contém o local de onde o rastreador veio. Esta é a página em que o URI atual foi descoberto. O Referer geralmente é registrado no servidor remoto e pode ajudar os webmasters a tentar descobrir como um rastreador chegou a uma determinada área em um site.

* sendRange- Envia o cabeçalho Range quando houver um limite no tamanho do documento recuperado. Isso é para fins de cortesia.

* ignoreCookies - Desativa o manuseio de cookies.

* sslTrustLevel- O nível de confiança do certificado SSL. O intervalo vai desde o padrão "open" (confiar em todos os certs - incluindo expirados, autosignados e aqueles que não possuímos uma autoridade de certificação) até "loose" (confiar em todos os certificados válidos, incluindo autodesignados) e "strict" (Cert é válido e o DN deve corresponder ao nome do servidor).

* acceptHeaders - Aceita cabeçalhos para incluir em cada solicitação. Cada um deve ser o cabeçalho completo, por exemplo, Accept-Language: en.

* httpBindAddress- Endereço IP local ou nome do host a ser usado ao fazer conexões (binding sockets). Quando não especificado, usa endereços locais padrão.

* httpProxyHost - O endereço IP do host proxy.

* httpProxyPort - A porta do proxy.

* digestContent - Se deve ou não fazer um digest instântaneo de um hash de conteúdo-de-corpo recuperado.

* digestAlgorithm - Especifica qual algoritmo (por exemplo, MD5 ou SHA-1) é usado para fazer um digest instântaneo de um hash de conteúdo-de-corpo recuperado.

###### extractorHtml

* extractJavascript - Se definido como "true", o Javascript da página é escaneado a procura de strings que pareçam URIs. Esse processo normalmente encontra URIs válidos e inválidos. Tentativas de buscar URIs inválidos pode gerar preocupação entre os webmasters sobre o comportamento estranho do rastreador. O padrão é "true".

* extractValueAttributes- Se definido como "true", o Javascript in-page é verificado em busca de strings que parecem ser URIs. Esse processo normalmente encontra tanto URIs válidos quanto inválidos. As tentativas de buscar os URIs inválidos podem gerar preocupação com o webmaster em relação ao comportamento estranho do rastreador. A definição padrão é true.

* ignoreFormActionUrls - Se definido como "true", os URIs que aparecem como o atributo ACTION nos FORMULÁRIOS HTML são ignorados. A definição padrão é "false"

* extractOnlyFormGets - Se definido como "true", somente URIs de AÇÃO com um MÉTODO GET (explícito ou implícito) serão extraídos. A definição padrão é "true".

* treatFramesAsEmbedLinks- Se definido como "true", os links SRF do FRAME/IFRAME são tratados como recursos incorporados (como IMG, 'E' hop-type). Caso contrário, eles são tratados como links de navegação.  A definição padrão é "true".

* ignoreUnexpectedHtml - Se definido como "true", os URIs que terminam em extensões não HTML comuns (como .gif) não serão verificados como se fossem HTML. A definição padrão é "true".

* maxElementLength - Comprimento máximo de um elemento HTML.

* maxAttributeNameLength - Comprimento máximo de um nome de atributo HTML.

* maxAttributeValueLength - Comprimento máximo de um valor de atributo HTML.

###### warcWriter

* compress - Se essa configuração for definida como "true", conteúdo do arquivo WARC será compactado. Observe que a compactação se aplica a cada item de conteúdo armazenado no WARC.

* prefix - Prefixo do nome do arquivo WARC.

* maxFileSizeBytes- Essa configuração determina o tamanho máximo em bytes para cada arquivo WARC. Quando o arquivo WARC atingir esse tamanho, URIs não serão mais gravados nele e outro arquivo WARC será criado para manipular os URIs restantes. Esta configuração não é estritamente rígida: se URIs extremamente grandes estiverem sendo baixados, o arquivo WARC poderá exceder esse limite. Os itens de conteúdo armazenados nos arquivos WARC nunca são divididos entre os WARCs.

* storePaths- Essa configuração é uma lista de caminhos nos quais os arquivos WARC serão gravados. Se mais de um caminho for especificado, uma abordagem round-robin será usada para escolher o caminho. Essa configuração é segura para ser alterarada durante um rastreamento.

* poolMaxActive- O Heritrix mantém vários arquivos WARC prontos para receberem documentos baixados. Essa abordagem é usada para evitar que o WARC seja uma restrição em um ambiente multithread. Essa configuração estabelece o número máximo desses arquivos para manter-se pronto. O valor padrão é cinco. Para pequenos rastreamentos que você deseja limitar a um único arquivo WARC, essa configuração deve ser definida como um.

* maxWaitForIdleMs - Controla quanto tempo um thread espera por um gravador reutilizável antes de considerar a criação de um novo. Se a criação não for permitida, os threads aguardarão indefinidamente a disponibilização de um gravador.

* skipIdenticalDigests- Whether to skip the writing of a record when URI history information is available and indicates the prior fetch had an identical content digest.  Default is false.

* maxTotalBytesToWrite - Total de bytes de arquivo para gravar no disco. Quando o tamanho de todos os arquivos no disco exceder esse limite, esse processador interromperá o rastreador. Valor zero significa que não há limite superior.

* directory - O diretório no qual os storePaths serão configurados.

* writeRequests -  Decide se deve gravar registros do tipo de solicitação. A definição padrão é "true".

* writeMetadata - Decide se deve gravar registros do tipo de metadados. A definição padrão é "true".

* writeRevisitForIdenticalDigests- Decide se deve gravar registros do tipo revisit quando o histórico de um URI indica que a busca anterior tinha um resumo de conteúdo idêntico. A definição padrão é "true".

* writeRevisitForNotModified - Decide se deve gravar registros do tipo revisit quando uma resposta 304-Not Modified é recebida. A definição padrão é "true".


###### candidates

* seedsRedirectNewSeeds - Se ativado, qualquer URI encontrado porque um seed redirecionou para ele (seed original retornou código 301 ou 302) também será tratado como um seed.

###### disposition

* delayFactor - Quantos múltiplos do último tempo transcorrido para aguardar antes de entrar em contato com o mesmo servidor.

* minDelayMs- O tempo mínimo para aguardar após uma solicitação ser concluída antes de entrar em contato novamente com o mesmo servidor. Este valor sobrepõe o delayFactor.

* respectCrawlDelayUpToSeconds - Se deve ou não respeitar um Crawl-Delay (em segundos) fornecido pelo robots.txt do site.

* maxDelayMs- A quantidade máxima de tempo para esperar após uma solicitação ser concluída antes de entrar em contato novamente com o mesmo servidor. Este valor substitui o delayFactor.

* maxPerHostBandwidthUsageKbSec - O uso máximo de largura de banda por host.

### Rastreamento de estatíticas 

Há vários módulos de rastreamento de estatísticas que podem ser anexados a um rastreamento. Atualmente, apenas um é fornecido pelo Heritrix. O Spring bean `statisticsTracker` que vem com o Heritrix cria o arquivo `progress-statistics.log` e fornece a IUW com dados para exibir informações de progresso sobre o rastreamento. É altamente recomendável que qualquer rastreamento executado pela IUW use esse bean.

### Regras de canonização da URI

O Heritrix controla os URIs duplicados para não rastrear conteúdo redundante. No entanto, os URIs podem ser compostos de várias maneiras, mesmo que a página apontada pelos URIs seja idêntica. Por exemplo, a página http://www.archive.org/index.html é a mesma página que http://WWW.ARCHIVE.ORG. Para resolver esse problema, o Heritrix canoniza os URIs antes de serem comparados quanto à desduplicação. A canonização envolve a aplicação de uma série de regras. Por exemplo, uma regra de canonização transforma todos os URIs em minúsculas. Outra regra retira o prefixo 'www' dos domínios.

O bean `canonicalizationPolicy` permite especificar regras de canonização e a ordem em que elas são executadas.

Para registrar o processo de canonização, adicione a linha:

`org.archive.modules.canonicalize.RulesCanonicalizationPolicy.level = FINER`

para o `conf/logging.properties`.

As regras de canonização NÃO são executadas se o URI que está sendo verificado for o resultado de um redirecionamento. Isso é feito pelo seguinte motivo: suponha que uma regra de canonização que equalize o http://www.archive.org para http://archive.org esteja em vigor. Se o rastreador encontrar o http://archive.org, mas o servidor no archive.org direcionar para o http://www.archive.org/, o Heritrix vai pensar que o http://www.archive.org já foi rastreado. Esse problema é evitado, ignorando as regras de canonização para redirecionamentos.

Alterar a canonização de URI também afeta a Wayback

Observe que, se as regras de Canonicalização de URI forem modificadas, isso também poderá afetar a experiência de reprodução. Deve-se garantir que as configurações da Wayback e do Heritrix sejam consistentes, caso contrário, o mecanismo Wayback pode não conseguir resolver os URLs nas páginas da Web para o recurso canonicalizado correspondente.

Caso de uso de regra de canonização URI: remoção de IDs de sessão específicos do site

Neste caso de uso, assumimos que um site está retornando URIs com uma chave de ID de sessão de cid. Por exemplo, . Suponha que o ID da sessão tenha sempre 32 caracteres. Além disso, suponha que o cid aparece sempre no final do URI. Isso apresenta um problema porque o mesmo URI será capturado repetidamento devido aos IDs de sessão diferentes.

A solução é adicionar uma segunda política de canonização de URI derivada da padrão. A nova política incluirá uma regra de expressão regular que filtra o ID da sessão e que é anexada a uma sobreposição de planilha. Uma sobreposição de planilha é uma maneira de substituir propriedades de beans com base em diferentes conjuntos de valores. A sobreposição de planilha criada será configurada de forma a aplicar-se apenas a URIs com um domínio específico. O exemplo abaixo mostra a configuração do Spring usada para obter esse efeito.

Isso já está no cxml padrão:

```
 <!-- SHEETOVERLAYMANAGER: manager of sheets of contextual overlays
      Autowired to include any SheetForSurtPrefix or
      SheetForDecideRuled beans -->
 <bean id="sheetOverlaysManager" autowire="byType"
  class="org.archive.crawler.spring.SheetOverlaysManager">
 </bean>
 ```
 
 Isso é comentado no cxml padrão e precisa ser descomentado:
 
 ``` <!-- CANONICALIZATION POLICY -->
 <bean id="canonicalizationPolicy"
  class="org.archive.modules.canonicalize.RulesCanonicalizationPolicy">
  <property name="rules">
   <list>
    <bean class="org.archive.modules.canonicalize.LowercaseRule" />
    <bean class="org.archive.modules.canonicalize.StripUserinfoRule" />
    <bean class="org.archive.modules.canonicalize.StripWWWNRule" />
    <bean class="org.archive.modules.canonicalize.StripSessionIDs" />
    <bean class="org.archive.modules.canonicalize.StripSessionCFIDs" />
    <bean class="org.archive.modules.canonicalize.FixupQueryString" />
   </list>
  </property>
 </bean>
 ```
 
 E algo do tipo precisa ser adicionado:
 
 ```
  <bean id="altCanonicalizationPolicy"
  class="org.archive.modules.canonicalize.RulesCanonicalizationPolicy"
  parent="canonicalizationPolicy" autowire-candidate="false">
  <property name="rules">
   <list merge="true">
    <bean class="org.archive.modules.canonicalize.RegexRule">
     <property name="regex" value="^(.+)(?:cid=0-9a-zA-Z{32})?$"/>
    </bean>
   </list>
  </property>
 </bean>

 <bean class='org.archive.crawler.spring.SurtPrefixesSheetAssociation'>
  <property name='surtPrefixes'>
   <list>
    <value>http://(z,y,</value>
   </list>
  </property>
  <property name='targetSheetNames'>
   <list>
    <value>canonicalizationPolicySheet</value>
   </list>
  </property>
 </bean>
 
 <bean id='canonicalizationPolicySheet' class='org.archive.spring.Sheet'>
  <property name='map'>
   <map>
    <entry key='preparer.canonicalizationPolicy'>
     <ref bean='altCanonicalizationPolicy'/>
    </entry>
   </map>
  </property>
 </bean>
 ```
 
Para ver a regra em operação, defina o nível de logging para `org.archive.crawler.url.Canonicalizer` em `logging.properties`. Estude a saída e ajuste seu regex de acordo.

## Credenciais 

Credenciais podem ser adicionadas para que o Heritrix consiga obter acesso a áreas de sites que exigem autenticação. As credenciais são configuradas no arquivo de configuração do Spring, `crawler-beans.cxml`. O exemplo a seguir mostra uma credencial configurada.

```
<bean id="credential"
   class="org.archive.modules.credential.HttpAuthenticationCredential">

    <property name="domain">
        <value>
            domain
        </value>
    </property>

    <property name="realm">
        <value>
            myrealm
        </value>
    </property>

    <property name="login">
        <value>
            mylogin
        </value>
    </property>

    <property name="password">
        <value>
            mypassword
        </value>
    </property>

 </bean>
 ```
 
Uma das configurações de uma credencial é seu domínio. Portanto, é possível criar todas as credenciais em nível global. No entanto, como isso pode causar excesso de verificações desnecessárias de credenciais, recomenda-se que as credenciais sejam adicionadas a uma substituição de domínio. Dessa forma, a credencial só é verificada quando o domínio relevante está sendo rastreado.

O Heritrix oferece dois tipos de autenticação: RFC2617 (Autenticação BASIC e DIGEST) e POST e GET de um formulário HTML.

### Armazenamento de credenciais

O Armazenamento de Credenciais contém o http ou informações de login de formulário. Deve ser configurado no arquivo `crawler-beans.cxml`.

Este é um exemplo de uma CredentialStore vazia:

```
<bean id="credentialStore"
      class="org.archive.modules.credential.CredentialStore">
</bean>
```

No entanto, para que funcione, sua propriedade de mapa de 'credenciais' deve ser preenchida com instâncias de credenciais adequadas. Por exemplo, se você tiver um bean HtmlFormCredential definido em outro lugar com o id 'formCredential', o seguinte resultará em uma CredentialStore incluindo essa Credencial:

```
<bean id="credentialStore"
       class="org.archive.modules.credential.CredentialStore">
 <property key="credentials">
  <map>
   <entry key="formCredential" value-ref="formCredential" />
  </map>
 </property>
</bean>
```


### Formulário HTML GET ou POST

Para usar a credencial `GET` ou `POST` do Formulário HTML, forneça um `domínio`, um `método http`, um `login-uri` e `itens de formulárioP`.

Antes de um URI ser agendado para rastreamento, o Heritrix procura por pré-condições. Exemplos de pré-condições incluem a captura do registro DNS do servidor que hospeda o URI e a busca do arquivo robots.txt. As credenciais do formulário HTML também são processadas como pré-condição. Se houver credenciais de formulário HTML para um determinado CrawlServer no repositório de credenciais, o URI especificado no campo de login de credencial do formulário HTML será agendado como uma pré-condição para o site, após as condições prévias do DNS e do robots.txt.

###### domain

Ver o domínio (BASIC AND DIGEST Auth).

###### login-uri

O login-uri é um URI relativo ou absoluto ao qual o Formulário HTML é enviado. Não é necessariamente a página que contém o formulário HTML; mas sim o ACTION URI ao qual o formulário é submetido.

###### form-items

Itens de formulário (form-items) são uma listagem de pares de chave/valor de formulário HTML. O botão de envio geralmente deve ser incluído nos itens de formulário.

Uma configuração de formulário HTML `GET` ou `POST` é ilustrada abaixo. (Observe que esse bean deve aparecer no mapa 'credentials' da CredentialStore, seja por meio de uma referência de bean ou por estar definido como inline.)

```
<bean id="credential"
   class="org.archive.modules.credential.HtmlFormCredential">

    <property name="domain" value="example.com" />

    <property name="login-uri" value="http://example.com/login"/>

    <property name="form-items">
        <map>
            <entry key="login" value="mylogin"/>
            <entry key="password" value="mypassword"/>
            <entry key="submit" value="submit"/>
        </map>
    </property>
</bean>
```

Observação

* Para um site com uma credencial de Formulário em HTML, um login é realizado em relação a todas as credenciais de login do formulário HTML listado após as condições prévias do DNS e do robots.txt serem atendidas. O rastreador só visualizará sites que tenham credenciais de formulário HTML de uma perspectiva conectada. Não há, atualmente, uma maneira de um único trabalho do Heritrix rastrear um site em um estado não autenticado e, em seguida, rastrear novamente o site em um estado autenticado. (Isso teria que ser feito em dois lançamentos de tarefa configurados separadamente.) 

* O login do formulário é executado apenas uma vez. O Heritrix continua rastreando, independentemente de o login ser bem-sucedido. Não há como dizer ao Heritrix para repetir a autenticação se a primeira tentativa não for bem-sucedida. Também não existe um meio para o rastreador reportar autenticações com sucesso ou falhas. O operador de rastreamento deve examinar os logs para determinar se a autenticação foi bem-sucedida.
 
* Os formulários de login de alguns sites podem ter itens de formulário com nomes dinâmicos ou campos ocultos extras necessários, cujo valor requerido é alterado para cada visitante. Este mecanismo HtmlFormCredential não tem suporte para enviar esses formulários com êxito.

### Logging

Para habilitar o log do console de texto de interações de autenticação, defina os níveis de log `FetchHTTP` e `PreconditionEnforcer` para "fine".

```
org.archive.crawler.fetcher.FetchHTTP.level = FINE
org.archive.crawler.prefetch.PreconditionEnforcer.level = FINE
```

Isso é feito editando o arquivo `logging.properties` do diretório conf.

### RFC2617 (autenticação BASIC e DIGEST)

Para usar a credencial RFC2617, forneça um domínio, realm, nome de usuário e senha.

A maneira como a autenticação RFC2617 funciona no Heritrix é que, em resposta a um código de resposta 401 (não autorizado), o Heritrix usará uma chave composta do domínio e do realm para fazer uma pesquisa em seu Repositório de Credenciais. Se uma correspondência for encontrada, a credencial será carregada no CrawlURI, que será marcado para uma nova tentativa imediata. 

Quando a nova tentativa é feita, as credenciais encontradas são adicionadas à solicitação.  Se a solicitação for bem-sucedida com um código de resposta 200, as credenciais serão promovidas para o servidor CrawlServer e todas as solicitações subsequentes, feitas no CrawlServer, fornecerão a credencial de forma preventiva. Se a credencial falhar com um código de resposta 401, não haverão novas tentivas com o CrawlURI.

###### domain

Domain (domínio) é o URI de raiz canônico do RFC2617; é o nome ou a autoridade do URI do CrawlServer (domínio mais porta se diferente da porta 80). Exemplos de domínios são: 'www.archive.org' ou 'www.archive.org:8080'.

###### realm

O realm (território) é definido na FAQ RFC2617. A cadeia do realm deve corresponder exatamente ao nome do realm apresentado no desafio de autenticação atendido pelo servidor da web.

Uma configuração de credencial RFC2617 é mostrada abaixado:

```
<bean id="credential"
class="org.archive.modules.credential.HttpAuthenticationCredential">


<property name="domain">
<value>
domain
</value>
</property>


<property name="realm">
<value>
myrealm
</value>
</property>


<property name="login">
<value>
mylogin
</value>
</property>


<property name="password">
<value>
mypassword
</value>
</property>


</bean>
```

Observação

Apenas um realm por domínio de credencial é permitido. Consulte Logon (HTTP POST, Basic Auth, etc.) para mais informações.

## Criação de Tarefas e Perfis

Esta seção fornece detalhes sobre a criação de tarefas e perfis.

### Criando uma tarefa

Uma tarefa pode ser criada a partir de três maneiras:

1. Com base em uma tarefa existente

Essa opção permite que uma nova tarefa seja criada com base em uma tarefa existente, independentemente de a tarefa ter sido rastreada ou não. Essa opção pode ser útil para repetir rastreamentos ou recuperar rastreamentos que tiveram problemas. Veja Recovery of Frontier State e frontier.recover.gz. Para criar uma tarefa baseada em um trabalho existente, use o botão "copy" na página do trabalho original.

2. Com base em um perfil.

Essa opção permite que uma nova tarefa seja criada com base em um perfil existente. Para criar uma tarefa baseada em um perfil, use o botão "copy" na página do perfil.

3. Com base no perfil padrão.

Essa opção cria uma tarefa de rastreamento com base no perfil padrão. Para criar uma tarefa a partir do perfil padrão, insira um nome de trabalho na tela do controle principal e clique no botão "create".

Observação

* Mudanças feitas em tarefas ou perfis não afetam tarefas derivadas.

* Tarefas baseadas no perfil padrão fornecido pelo Heritrix não estão prontos para serem executados como estão. O `metadata.operatorContactUrl` precisa ser configurado com um valor válido.

### Criando um perfil

Perfis podem ser criados a partir de tarefas existentes ou outros perfis. Na página de tarefa ou perfil, insira o nome do novo perfil e marque a caixa "as profile". Em seguida, clique no botão "copy".

Para modificar o comportamento do novo perfil, consulte Configurando Tarefas e Perfis.

Observação

As alterações feitas no perfil ou na tarefa não afetam perfis derivados.

## Além da Interface de Usuário

Embora seja possível fazer muitas coisas com a IUW do Heritrix, há muitos recursos disponíveis fora dela.

### Recuperação de rastreamento

Durante a operação normal, a Frontier do Heritrix mantém um diário. O diário é mantido no diretório de log e é nomeado `frontier.recovery.gz`. Se ocorrer uma falha durante um rastreamento, o diário `frontier.recover.gz` poderá ser usado para recriar o status aproximado do rastreador no momento da falha. Em alguns casos, a recuperação pode demorar, mas normalmente é muito mais rápida do que repetir o rastreamento travado.

A utilização desse processo inicia um rastreamento totalmente novo, com a mesma configuração (ou modificada). No entanto, esse novo rastreamento fará um desvio maior no início, no qual é usado as saídas frontier-recover.gz do rastreamento anterior para simular o status da fronteira (URIs descobertos, URIs enfileirados) do rastreamento anterior. Todos os ARC/WARCs, logs e pontos de verificação do rastreamento anterior seriam deixados de lado, retendo apenas os logs e ARC/WARCs como um registro do rastreamento até o momento.

Quaisquer arquivos ARC/WARC que existam com o sufixo `.open` não foram fechados corretamente pela execução anterior e podem incluir dados corrompidos/truncados no último registro parcial. Os arquivos com sufixo `.warc.gz.open` podem ser renomeados para `.warc.gz`, mas considere a possibilidade de validar tais ARC/WARCs (zcat'ando o arquivo para/dev/null para verificar a validade do gzip ou outras ferramentas ARC/WARC para registro completo) antes de remover o sufixo ".open".

### Recuperação completa

Para executar o processo de recuperação, reinicie o rastreador com falha e copie o arquivo `frontier.recover.gz` no Action Directory. Em seguida, reinicie o rastreamento. O Heritrix carregará automaticamente o arquivo de recuperação e começará a colocar seus URIs na Frontier para rastreamento.

Se um arquivo `.recover.gz` estiver sendo usado, apenas um arquivo completo deve ser usado. (Isso é para que o diretório de ação processando um arquivo de cada vez possa fazer a primeira passagem completa de 'includes', em seguida, a passagem completa de 'schedules', de um arquivo. O fornecimento de vários arquivos `.recover.gz` em série resultará em um ciclo includes/schedule, includes/schedule, etc., que não produzirá o efeito desejado na frontier.)

Enquanto o arquivo estiver sendo processado, qualquer ponto de verificação (manual ou automático) não será um snapshot válido do estado do rastreador. (O processo de log de recuperação da frontier ocorre por meio de um thread/caminho separado, fora do sistema de verificação mais recente.). Somente quando o processamento do arquivo for concluído (arquivo movido para 'concluído') o rastreador estará em um estado de verificação confiável.

Depois que os URIs começarem a aparecer nas filas (quando a recuperação entrar na passagem de 'agendamentos'), o rastreador poderá ser despausado para começar a buscar URIs enquanto o restante da passagem de recuperação 'agendamentos' continuar. 

No entanto, a nota acima sobre pontos de verificação ainda se aplica: um ponto de verificação preciso só ocorrerá quando o processamento de arquivos de recuperação da frontier for concluído.

Além disso, despausar o rastreamento dessa maneira pode resultar na redescoberta de alguns URIs por meio de novos caminhos antes que a descoberta original seja reproduzida por meio do processo de recuperação. (Muitos rastreamentos podem não se importar com esse pequeno desvio do estado de rastreamento recuperado, mas se o escopo for muito dependente do caminho ou do hop, isso pode causar diferenças no que é incluído no escopo).

possibilidade de criação de Avisos

Observação: alimentar a frontier inteira de volta ao rastreador provavelmente resultará em vários avisos de "Problem line" no log de tarefas. Alguns operadores acham melhor permitir que todo o arquivo de recuperação seja processado pelo rastreador antes de tentar retomar (unpause) o rastreamento, ajudando a isolar essa conversação e minimizar a geração de crawldatas duplicados durante a recuperação.

### split recover

Uma forma alternativa de executar o processo de recuperação é ilustrada abaixo. Eliminar linhas irrelevantes logo no início (fora do processo de recuperação) pode permitir que o processo de recuperação seja concluído mais rápido do que o processo padrão. Também permite que o processo proceda de vários arquivos, em vez de um só, portanto, pode fornecer uma indicação de progresso melhor em andamento e chances de checkpoint a recuperação.

Para executar o processo de recuperação alternativo:

1. deixe de lado logs e ARCs/WARCs anteriores, como acima
2. Abrir novamente o rastreamento que falhou
3. Dividir qualquer arquivo de origem `frontier.recover.gz` usando comandos como os seguintes:

```
zcat frontier.recover.gz | grep '^Fs' | gzip > frontier.include.gz
zcat frontier.recover.gz | grep '^F+' | gzip > frontier.schedule.gz
```

1. Construa e inicie a tarefa que havia falhado (com a mesma configuração). A tarefa estará pausada.
2. Mova o(s) arquivo(s) `frontier.include.gz` para o diretório "action". O diretório está localizado no mesmo nível na hierarquia da estrutura de arquivos que o diretório bin. (Se houver muitos arquivos, é possível movê-los todos juntos de uma só vez, ou em pequenos lotes para monitorar melhor o progresso. No momento em que todos os arquivos previamente apresentados forem processados - ou seja, movidos para o diretório 'done', é possível fazer um checkpoint válido.
3. O progesso dessa fase 'includes' pode ser acompanhando visualizando a interface do usuário da web ou pelo contador `discovered` pelo `progress-statistics.log` 
4. Quando todos `.includes` terminarem de carregar, o processo pode ser repetido com todos os logs `.schedule`.
5. Se houver um número muito grande de URIs na contagem da fila (vários milhares), o rastreamento pode ser despausado para permitir que o novo rastreamento continue paralelamente ao enfileiramento de URIs mais antigos.

Você pode mover todos os arquivos `.include` e `.schedule` para o diretório "action" antes de iniciar, se tiver certeza de que a ordenação lexicográfica de seus nomes funcionará corretamente (mostrar todos os {{.include}}s primeiro e o {{. schedule}}s na mesma ordem que o rastreamento original). No entanto, isso deixa pouca oportunidade para ajustar/checkpoint o processo: o diretório "action" irá descobri-los e processá-los todos em um loop fechado.

Observação: Para ter certeza do sucesso e do atual status do rastreamento contra qualquer tipo de possível erros IO/format, em grandes recuperações de milhões de registros, é melhor aguardar a conclusão de cada etapa antes de mover um arquivo ou despausar a tarefa. Em vez de analisar as estatísticas de progresso, aguarde até que o arquivo seja movido de "action" para "action/done". Em seguida, adicione o segundo arquivo. Espere novamente. Finalmente, despause o rastreador.

Uma recuperação de 100M URIs pode levar dias, então, por favor, seja paciente.

## Processadores de Redução de Duplicação

### Redução de duplicação de URL-Agnostic 

Para configurar a deduplicação independente de URL de conteúdo idêntico, adicione esses beans de nível superior ao seu crawler-beans.cxml:

```
  <!-- optional, will use the main bdb module if omitted, just like old dedup -->
  <bean id="historyBdb" class="org.archive.bdb.BdbModule" autowire-candidate="false">
   <property name="dir" value="history" />
  </bean>

  <bean id="contentDigestHistory" class="org.archive.modules.recrawl.BdbContentDigestHistory">
   <property name="bdbModule">
    <ref bean="historyBdb" />
   </property>
  </bean>
  ```
  
Depois, insira esses beans na disposition chain, com o warcWriter no meio:
  
  ```
   <bean id="dispositionProcessors" class="org.archive.modules.DispositionChain">
   <property name="processors">
    <list>
==>    <bean class="org.archive.modules.recrawl.ContentDigestHistoryLoader" />
     <!-- write to aggregate archival files... -->
     <ref bean="warcWriter"/>
==>    <bean class="org.archive.modules.recrawl.ContentDigestHistoryStorer" />
     <!-- ...send each outlink candidate URI to CandidateChain,
          and enqueue those ACCEPTed to the frontier... -->
     <ref bean="candidates"/>
     <!-- ...then update stats, shared-structures, frontier decisions -->
     <ref bean="disposition"/>
     <!-- <ref bean="rescheduler" /> -->
    </list>
   </property>
  </bean> 
  ```
  
  ```
  Legacy Duplication Reduction Configuration
  ```
  
A partir da versão 1.12.0, vários processadores podem cooperar para transportar o histórico de conteúdo do URI entre os rastreamentos (consulte JavaDocs do pacote `org.archive.crawler.processor.recrawl`). Isso reduz a quantidade de material duplicado baixado ou armazenado em rastreamentos posteriores. Consulte Deduplicação (Redução de Duplicação) para detalhes de configuração de desduplicação do H1. No H3, use o arquivo `crawler-beans.cxml` para configurar a deduplicação e adicione os seguintes processadores, conforme descrito abaixo.

## Configurar armazenamento de histórico de URI persistente

Essas configurações devem ser adicionadas a qualquer rastreamento que forneça informações de busca de histórico para futuras deduplicações (como o primeiro de dois rastreamentos ou todos os rastreamentos de uma série em andamento).

(1) Defina um bean nomeado para `FetchHistoryProcessor`:

```
<bean id="fetchHistoryProcessor" class="org.archive.modules.recrawl.FetchHistoryProcessor">
 <!-- <property name="historyLength" value="2" /> -->
</bean>
```

(2) Defina um bean nomeado para `PersistStoreProcessor`:

```
<bean id="persistStoreProcessor" class="org.archive.modules.recrawl.PersistStoreProcessor">
</bean>
```

(3) Adicione esses beans de processador às cadeias de processamento relevantes.

O *fetchHistoryProcessor* deve ser adicionado ao *FetchChain* depois dos outros processadores de busca. O persistStoreProcessor deve ser adicionado depois de qualquer gravador no DispositionChain. A melhor forma de fazer isso é usando marcadores "ref" para se referir aos beans processadores acima nas definições do bean *FetchChain* e *DispositionChain*.

Exemplo em um FetchChain comum:

```
<bean id="fetchProcessors" class="org.archive.modules.FetchChain">
  <property name="processors">
   <list>
    <!-- recheck scope, if so enabled... -->
    <ref bean="preselector"/>
    <!-- ...then verify or trigger prerequisite URIs fetched, allow crawling... -->
    <ref bean="preconditions"/>
    <!-- ...fetch if DNS URI... -->
    <ref bean="fetchDns"/>
    <!-- ...fetch if HTTP URI... -->
    <ref bean="fetchHttp"/>
=>    <ref bean="fetchHistoryProcessor" />
    <!-- ...extract oulinks from HTTP headers... -->
    <ref bean="extractorHttp"/>
    <!-- ...extract oulinks from HTML content... -->
    <ref bean="extractorHtml"/>
    <!-- ...extract oulinks from CSS content... -->
    <ref bean="extractorCss"/>
    <!-- ...extract oulinks from Javascript content... -->
    <ref bean="extractorJs"/>
    <!-- ...extract oulinks from Flash content... -->
    <ref bean="extractorSwf"/>
   </list>
  </property>
</bean>
```

Exemplo em um DispositionChain comum:

```
<bean id="dispositionProcessors" class="org.archive.modules.DispositionChain">
  <property name="processors">
   <list>
    <!-- write to aggregate archival files... -->
    <ref bean="warcWriter"/>
=>  <ref bean="persistStoreProcessor" />
    <!-- ...send each outlink candidate URI to CandidatesChain,
         and enqueue those ACCEPTed to the frontier... -->
    <ref bean="candidates"/>
    <!-- ...then update stats, shared-structures, frontier decisions -->
    <ref bean="disposition"/>
   </list>
  </property>
</bean>
```

Feito isso, as informações do histórico URI de interesse para os rastreamentos posteriores são mantidas pelo ambiente BDB padrão do rastreamento (conforme gerenciado pelo BdbModule, residindo no disco no diretório 'state').

### Configurar o carregamento de histórico de URI persistente para rastreamentos subsequentes

Siga as etapas abaixo para qualquer tipo de rastreamento que tire proveito da deduplicação, como o segundo de dois rastreamentos, ou todos os rastreamentos de uma série (menos o primeiro).

(1) Defina um bean nomeado para `PersistLoadProcessor`:

```
<bean id="persistLoadProcessor" class="org.archive.modules.recrawl.PersistLoadProcessor">
</bean>
```

Conforme configurado aqui, as informações do histórico de URI serão carregadas a partir do ambiente BDB padrão do rastreamento (conforme gerenciado pelo BdbModule, residindo no disco no diretório 'state').

(2) Se ainda não existir, defina um bean nomeado para o `FetchHistoryProcessor`:

```
<bean id="fetchHistoryProcessor" class="org.archive.modules.recrawl.FetchHistoryProcessor">
 <property name="historyLength" value="10" />
</bean>
```

(3) Assegure-se de que esses beans de processador estejam nas cadeias de processamento apropriadas, nos locais certos. Adicione as tags `ref` dos processadores acima ao bean *FetchChain*. Adicione o processador *persistLoadProcessor* após o bean das pré-condições. Adicione o *fetchHistoryProcessor* após outros beans de busca.

```
<bean id="fetchProcessors" class="org.archive.modules.FetchChain">
  <property name="processors">
   <list>
    <!-- recheck scope, if so enabled... -->
    <ref bean="preselector"/>
    <!-- ...then verify or trigger prerequisite URIs fetched, allow crawling... -->
    <ref bean="preconditions"/>
=>    <ref bean="persistLoadProcessor"/>
    <!-- ...fetch if DNS URI... -->
    <ref bean="fetchDns"/>
    <!-- ...fetch if HTTP URI... -->
    <ref bean="fetchHttp"/>
=>    <ref bean="fetchHistoryProcessor"/>
    <!-- ...extract oulinks from HTTP headers... -->
    <ref bean="extractorHttp"/>
    <!-- ...extract oulinks from HTML content... -->
    <ref bean="extractorHtml"/>
    <!-- ...extract oulinks from CSS content... -->
    <ref bean="extractorCss"/>
    <!-- ...extract oulinks from Javascript content... -->
    <ref bean="extractorJs"/>
    <!-- ...extract oulinks from Flash content... -->
    <ref bean="extractorSwf"/>
   </list>
  </property>
</bean>
```

### opção *preloadSource*

Se você deseja extrair o histórico de URIs persistentes de uma origem não padrão, a propriedade `preloadSource` pode ser usada no PersistLoadProcessor. Seu valor deve ser o caminho para o diretório `state` de um rastreamento anterior ou um caminho ou URI para o log de persistência de um rastreamento anterior (`persistlog.txtser.gz`), no caso do PersistLogProcessor ser usado, o que não é recomendado no H3 e não descrito acima.

O pré-carregamento verifica o diretório "state" antigo ou registra na inicialização, carregando o diretório "state" do rastreamento atual com as informações do histórico. Isso pode ser uma etapa demorada, o que atrasa o início do rastreamento. Observe que preloadSource *não* deve ser o diretório "state" do rastreamento atual ou um erro ocorrerá.

```
<bean id="persistLoadProcessor" class="org.archive.modules.recrawl.PersistLoadProcessor">
 <property name="preloadSource" value="/Users/me/Documents/heritrix-3.0.0-SNAPSHOT/jobs/originalCrawl/state"/>
</bean>
```

OU

```
<bean id="persistLoadProcessor" class="org.archive.modules.recrawl.PersistLoadProcessor">
 <property name="preloadSource" value="file:///Users/me/Documents/heritrix-3.0.0-SNAPSHOT/jobs/originalCrawl/logs/persistlog.txtser.gz"/>
 </bean>
 ```
 
 ### Configurando outros processadores
 
A configuração acima garante que as informações de histórico de busca sejam retidas entre os rastreamentos. Se isso afeta a obtenção ou armazenamento de URIs depende das configurações em outros processadores, o mais importante é o processador FetchHTTP e o WARCWriterProcessor ou ARCWriterProcessor.

No processador FetchHTTP, as propriedades sendIfNoneMatch e sendIfModifiedSince controlam se os cabeçalhos HTTP If-None-Match ou If-Modified-Since são enviados em uma solicitação se as informações do histórico de URI (data de obtenção prévia ou informações etag) para suportá-los estiverem presentes.

Note que ao enviar esses cabeçalhos, o rastreamento pode receber uma resposta '304-Not Modified' do servidor, sem conteúdo de corpo. Dessa maneira, outros URIs podem acabar não sendo descobertos e os caminhos seguidos pelo rastreamento original não serão reconsiderados para uma nova busca. Assim, se você deixar *sendIfNoneMatch* e *sendIfModifiedSince* definidos com valores 'true' padrões em um rastreamento ativado para histórico de deduplicação, poderá usar outra técnica para garantir que todos os URIs dos rastreamentos anteriores sejam reconsiderados (como fornecer todos eles ao rastreador já no começo).

Tanto o ARCWriterProcessor quanto o WARCWriterProcessor possuem uma propriedade *skipIdenticalDigests* que, por padrão, é "false". Se marcada como "true", qualquer busca cujo conteúdo tenha um valor digest idêntico ao da busca anterior será ignorada completamente para fins de gravação. Para o ARCWriterProcessor, isso significa que o registro ARC não será gravado e nada mais será gravado em seu lugar. Da mesma forma para o WARCWriterProcessor, nenhum registro de qualquer tipo será gravado. Apenas o crawl.log registrará que a busca ocorreu.

O WARCWriterProcessor possui propriedades adicionais para manipulação de duplicação mais sofisticada. A propriedade *writeRevisitForIdenticalDigests*, por padrão "true", gravará um registro WARC 'revisit' de tamanho reduzido (sem conteúdo de corpo) em vez de um registro de 'response' quando um digest de repetição for detectado ao consultar as informações do histórico. (Observe que, se o skipIdenticalDigests acima for marcado como "true", nem mesmo isso será gravado - já que toda a escrita já foi ignorada.) Se "false", um registro "response" completo (com todo o conteúdo duplicado) ainda será gravado, mesmo com um valor digest repetido.

A propriedade *writeRevisitForNotModified*, padrão "true", gravará um registro WARC 'revisit' em vez de um registro 'response' quando as respostas '304-Not Modified' forem recebidas. Se "false", um registro normal de 'response' é gravado.

### Avançado: usando um ambiente BDB de histórico não padrão

Os rastreamentos podem desejar armazenar informações de histórico URI persistente em um local diferente do diretório do ambiente BDB padrão ('state'). Motivações para tal escolha podem incluir:

1. espalhar o disco de rastreamento IO para outro lugar
2. manter informações do histórico distintas de outras informações de rastreamento mais transitórias (filas, estado do rastreamento) para facilitar a análise ou a migração separadamente

Para fazer isso, a primeira etapa é definir mais de um BdbModule na configuração de rastreamento e dar a este segundo BdbModule um nome de bean e um diretório de ambiente distintos. Ele também deve estar fora de consideração para o auto-wiring do Spring (de modo que apenas um único BdbModule atenda a esse propósito). Por exemplo:

```
<bean id="historyBdb"
  class="org.archive.bdb.BdbModule" autowire-candidate="false">
  <property name="dir" value="history" />
 </bean>
```

Depois, faça com que o outro PersistLoadProcessor e/ou PersistStoreProcessor se refira explicitamente a este BdbModule por nome, em vez de simplesmente aceitar o padrão autowired. Por exemplo:

```
<bean id="persistLoadProcessor" class="org.archive.modules.recrawl.PersistLoadProcessor">
  <property name="bdbModule">
    <ref bean="historyBdb"/>
  </property>
</bean>
<bean id="persistStoreProcessor" class="org.archive.modules.recrawl.PersistStoreProcessor">
 <property name="bdbModule">
    <ref bean="historyBdb"/>
  </property>
</bean>
```

Você também pode carregar históricos passados de um ambiente diferente do qual as novas informações são armazenadas se desejar manter cada ciclo distinto.

### Considerações ao projetar um rastreamento usando redução de duplicação

A redução de duplicação em seu nível atual oferece dois grandes potenciais de benefício ao repetir um rastreamento: economia de largura de banda e economia de espaço de armazenamento.

Para que haja economia de largura de banda, as opções *sendIfNoneMatch* e/ou *sendIfModifiedSince* devem ser ativadas. Em seguida, os servidores podem informar que não houve alterações significativas desde a versão obtida anteriormente - economizando o tempo e o gasto de largura de banda de envio de conteúdo redundante.

No entanto, isso altera as informações disponíveis para o rastreamento subsequente. Ele não verá mais o conteúdo e, portanto, não descobrirá mais os links dessa página, a serem reconsiderados por conta própria. Portanto, se o único caminho para uma página alterada for por meio de uma página inalterada que, devido a uma resposta '304-Not Modified', nunca é extraída via link no rastreamento subsequente, a página alterada nunca será considerada para rastreamento e, portanto, a mudança não será descoberta, nem arquivada. Assim, é importante que, ao usar esses recursos condicionais-GET de redução de largura de banda, o operador garanta manualmente que todos os URIs de interesse do primeiro rastreamento sejam enfileirados no rastreamento subsequente (seja como seeds ou alguma outra adição durante o rastreamento). 

Devido a essa complicação, muitos rastreamentos podem querer usar apenas a redução de duplicação baseada em digest. Isso não oferece economia de largura de banda: todos os URIs são buscados novamente. Mas, quando o digest principal do conteúdo não é alterado, o conteúdo ou não é reescrito ou apenas reescrito de forma abreviada (como um registro WARC 'revisit'). Essa abordagem envolve a configuração das propriedades *sendIfModifiedSince* e *sendIfNoneMatch* do FetchHTTP como "false".

Um aprimoramento futuro para esses recursos pode levar adiante as informações de outlink descobertas como parte do histórico do URI, de modo que os outlinks possam ser 'virtualmente descobertos' mesmo quando '304-Not Modified' significa que nenhuma cópia extraível do conteúdo inalterado está disponível para o rastreador.

## Scripts de utilidade Unix

O Heritrix possui scripts de utilidade Unix.

###### manifest_bundle.pl

Esse script agrupa todos os recursos mencionados no arquivo de manifesto de rastreamento. Um pacote é um tarball não comprimido ou comprimido. A estrutura de diretórios do tarball é:

* Diretório de nível superior (nome do rastreamento)
* Três subdiretórios padrões
* Outros subdiretórios arbitrários
* Uso de script

```
manifest_bundle.pl crawl_name manifest_file -f output_tar_file -z [ -flag directory]
-f output tar file. If omitted output to stdout.
-z compress tar file with gzip.
-flag is any upper case letter. Default values C, L, and are R are set to
configuration, logs and reports
```

###### manifest-bundle.pl (exemplo)

```
manifest_bundle.pl testcrawl crawl-manifest.txt -f /0/testcrawl/manifest-bundle.tar.gz -z -F filters
```

Para o exemplo acima, o tarball conterá a seguinte estrutura de diretórios:
| - testcrawl

       |- configurations

       |- logs

       |- reports

       |- filters
       
###### hoppath.pl

Esse script Perl, localizado em (HERETRIX_HOME)/bin, recria o caminho do hop para o URI especificado. O caminho do hop é o caminho de links (URIs) que foram seguidos para chegar ao URI especificado.

Uso de script

```
hoppath.pl crawl.log URI_PREFIX
crawl.log Full-path to Heritrix crawl.log instance.
URI_PREFIX URI we're querying about. Must begin 'http(s)://' or 'dns:'.
Wrap this parameter in quotes to avoid shell interpretation
of any '&' present in URI_PREFIX.
```

###### hoppath.pl (exemplo)

```
hoppath.pl crawl.log 'http://www.house.gov/'
```

###### hoppath.pl (resultado)

```
2004-02-25-02-36-06 - http://www.house.gov/house/MemberWWW_by_State.html
2004-02-25-02-36-06 L http://wwws.house.gov/search97cgi/s97_cgi
2004-02-25-03-30-38 L http://www.house.gov/
```

O `L` no exemplo refere-se ao tipo de link seguido.

###### RecoveryLogMapper

A classe Java `org.archive.crawler.util.RecoveryLogMapper` é semelhante ao script `hoppath.pl`. Foi contribuído por Mike Schwartz. O `RecoveryLogMapper` analisa um arquivo de log de recuperação do Heritrix e cria mapas que permitem que um chamador pesquise qualquer URI de origem. O RecoveryLogMapper então retorna uma lista de todos os URIs rastreados com sucesso a partir do seed. O `RecoveryLogMapper` também pode encontrar o URI de origem do qual qualquer URI rastreado foi capturado.

## Guia rápido para criar um perfil

Os perfis podem ser criados ou na página de tarefa ou na página de perfil do Heritrix. Essas páginas exibem os detalhes de uma tarefa ou de um perfil, respectivamente. Para criar um novo perfil, escolha a tarefa ou o perfil em que o novo perfil será baseado. Clique na tarefa ou no perfil na página do console principal. A página de tarefa/perfil será exibida. Na parte inferior da página, insira o nome do novo perfil na caixa de texto "Copy job to" ou "Copy profile to". Selecione a caixa de seleção "as profile" e clique em "copy". Um novo perfil será criado. Nesse ponto, você pode configurar o perfil da mesma maneira que uma tarefa é configurada, editando o arquivo crawler-beans.cxml.

Observação

* Mudanças feitas na tarefa ou perfil base não modificam o novo perfil criado.

## Página de tarefa (Job Page)

O Heritrix 3.0/3.1 introduz a capacidade de executar várias tarefas simultaneamente na mesma instância do rastreador. No Heritrix 1.x, apenas uma tarefa poderia ser executada por vez, enquanto as outras tarefas eram enfileiradas atrás da tarefa em execução. O único limite que afeta o número de tarefas que podem ser executadas simultaneamente no Heritrix 3.0/3.1 é a quantidade de memória alocada para o heap Java. Se muitos rastreamentos forem executados, o heap Java poderá, em algum momento, estar esgotado. Isso resultará em um erro OutOfMemory que cancelará os rastreamentos em execução.

Depois que uma tarefa de rastreamento tiver sido criada e configurada corretamente, poderá ser executada. Para iniciar um rastreamento, o usuário deve ir para a página de tarefa clicando na tarefa específica na IUW.

### Elementos de dados da página de tarefa

###### Job name

O nome da tarefa e o número de vezes que foi executada.

###### Avaible checkpoints to recover

A partir da versão 3.1, se um rastreamento tiver sido verificado, uma caixa suspensa será exibida mostrando todos os pontos de verificação que foram executados.

###### Job Log

O log da tarefa contém um registro dos comandos emitidos na página da tarefa, incluindo respostas de comando.

###### Job Status

Exibe o status da tarefa.

###### Totals

Exibe estatísticas que fornecem informações sobre o número de documentos rastreados e baixados.

###### Alerts

Lista os alertas gerados pelo rastreamento. Os alertas podem ser avisos ou problemas que abortam o rastreamento mas que não prejudicam o rastreamento, mas podem prejudicar o resultado do rastreamento.

###### Rates

Exibe estatísticas que fornecem as taxas nas quais os documentos e bytes de dados estão sendo baixados. O número de URIs processados por segundo, com sucesso, é mostrado. Nessa estatística, é mostrada a taxa do último intervalo de amostragem e a taxa média (entre parênteses). O intervalo de amostragem é geralmente cerca de 20 segundos e pode ser controlado pela propriedade intervalSeconds do bean Spring do StatisticsTracker. A taxa mais recente de progresso pode flutuar consideravelmente à medida que a carga de trabalho do rastreador varia e ocorrem a memória de manutenção e as operações de arquivo. Isso é principalmente verdade se o intervalo de amostragem tiver sido definido com um valor baixo. A taxa de coleta de conteúdo bem-sucedida em KB /segundo para o intervalo de amostragem mais recente e (entre parênteses) a média desde o início do rastreamento também são exibidas.

###### Load

Exibe estatísticas que fornecem informações de carregamento. O número de threads ativos, comparado ao total de threads disponíveis, é mostrado. Normalmente, se apenas um número pequeno de threads estiver ativo, é porque ativar mais threads excederia as configurações de cortesia configuradas. Por exemplo, se todos os URIs restantes estiverem em um único host, apenas um thread estará ativo, a menos que as filas paralelas estejam ativadas. Às vezes, nenhum tópico estará ativo devido a pausas para considerações de cortesia.

###### Congestion Ratio

O índice de congestionamento é uma estimativa aproximada de quanta capacidade inicial, como um múltiplo da capacidade atual, seria necessária para rastrear a carga de tarefa atual na taxa máxima disponível em determinadas configurações de cortesia. Esse valor é calculado comparando o número de filas internas que estão progredindo em relação àquelas que estão aguardando a disponibilização de um thread.

###### Deepest Queue

A estatística de fila mais profunda é a cadeia mais longa de URIs que deve ser processada sequencialmente. Essa estatística é um indicador superior do trabalho restante do que o número total de URIs pendentes. Por exemplo, 1000 URIs em 1000 filas podem ser concluídos rapidamente, mas 1000 URIs em uma única fila levarão muito mais tempo para serem concluídos. A profundidade média é a profundidade média do último URI em todas as filas seqüenciais ativas.

###### Elapsed

Exibe o tempo decorrido, em milissegundos, que uma tarefa foi executada, excluindo o tempo no estado "pausado".

###### Threads

Esta área da página do trabalho exibe o número de threads sendo usados. Clicar em "threads" para ver um relatório de detalhado.

###### Frontier

Exibe estatísticas da Frontier, como o número de URIs enfileirados. Clicar em "frontier" para ver um relatório de detalhado.

###### Memory

Exibe a quantidade de memória alocada ao heap Java, a quantidade de memória em uso e o tamanho máximo do heap Java.

###### Crawl Log

Exibe a saída do log de rastreamento. O log de rastreamento contém informações detalhadas sobre um rastreamento em execução, como os URIs que foram buscados.

###### Advanced

Fornece acesso a recursos avançados que podem ser usados para controlar uma tarefa.

###### Configuration-referenced Paths

Exibe os caminhos relevantes para a configuração e registro de tarefa. Por exemplo, o caminho para o arquivo `crawl.log` é exibido.

### Operações da página de trabalho

###### Edit

Permite editar o arquivo `crawler-beans.cxml`. O arquivo `crawler-beans.cxml` contém a configuração Spring da tarefa de rastreamento. A edição desse arquivo é a maneira padrão de configurar uma tarefa ou perfil.

###### Build

Constrói as classes Java Spring que são configuradas por meio do arquivo `crawler-beans.cxml`. Antes de uma tarefa ser executada, ela deve ser construída.

###### Launch

Inicia uma tarefa de rastreamento. Antes de ser lançada, a tarefa deve ser construída. Uma vez que a tarefa é lançada, ela estará pausada ou em execução. Se estiver pausada, o botão "unpause" deve ser clicado para iniciar o rastreamento. A partir da versão 3.1, se um ponto de verificação ou vários pontos de verificação foi/foram executados, um ponto de verificação pode ser selecionado na caixa suspensa do ponto de verificação. A tarefa pode ser reiniciada no ponto de verificação clicando em "launch".

###### Pause

Pausa o rastramento em andamento.

###### Unpause

Despausa o rastreamento.

###### Checkpoint

Salva o estado atual do rastreamento no armazenamento. Durante o tempo em que o rastreamento está sendo verificado, ele é pausado e nenhum URI será rastreado. O ponto de verificação (checkpoint) é útil se um rastreamento deve ser interrompido e, em seguida, reiniciado.

###### Terminate

Encerra um rastreamento.

###### Teardown

Descarta as classes Spring Java atuais da tarefa e permite que uma nova configuração Spring seja construída. Qualquer alteração no arquivo `crawler-beans.cxml` após o botão "Build" ter sido chamado requer uma desmontagem (teardown) e outra montagem a ser executada.

###### Copy

Permite a cópia da configuração da tarefa atual para uma nova tarefa ou um novo perfil.

###### Scripting Console

Exibe um formulário de entrada que pode ser usado para inserir e executar comandos de script. Os comandos de script podem ser usados para controlar o comportamento de uma tarefa de rastreamento. Várias linguagens de script estão disponíveis, como AppleScript e ECMAScript. Exemplos de scripts podem ser encontrados aqui.


###### Browse Beans

Exibe a hierarquia dos beans Spring que formam uma tarefa de rastreamento. As propriedades e associações de cada bean podem ser visualizadas ou editadas clicando no bean.

## Frontier

A Frontier é um bean Spring que mantém o estado interno do rastreamento. O estado do rastreamento contém informações como URIs rastreadas ou descobertas anteriormente, bem como outras informações relevantes para o status do rastreamento.

Há apenas um bean Frontier por tarefa de rastreamento.

Crucialmente, a frontier do Heritrix3, além de armazenar várias filas de URLs para rastreamento em ordem de prioridade, também controla as configurações de cortesia de atraso de rastreamento por filas. Ou seja, controla, também, quando CrawlURIs devem ser rastreados, não somente a prioridade de rastreamento.

O Heritrix BdbFrontier também implementa a rotação de filas, para garantir que todas as filas sejam vistas, mesmo quando houver um número maior de filas do que de threads disponíveis para executar o rastreamento. Isso significa que as filas de rastreamento do Heritrix têm "orçamentos de sessão" (para manipular a rotação), além de cotas de rastreamento gerais (que são aplicadas a todo o rastreamento).

Nas versões 3.0 e 3.1, existe apenas um tipo de Frontier, o Heritrix BdbFrontier. Outras frontiers que foram incluídas no Heritrix 1.x não são mais suportados.

Para mais detalhes, ver:

* Configurações da Frontier 
* Frontier queue budgets
* Heritrix BdbFrontier (detalhes técnicos)

### Configurações do Frontier

Cortesia (Politeness)

Uma combinação de várias configurações controla a cortesia da Frontier. É importante observar que, a qualquer momento, apenas um URI de qualquer host é processado. As regras de cortesia a seguir impõem um tempo de espera adicional entre o final do processamento de um URI e o início do próximo.

* delayFactor - Essa configuração impõe um atraso entre a obtenção de URIs do mesmo host. O atraso é um múltiplo do tempo necessário para buscar o último URI baixado do host. Por exemplo, se foram necessários 800 milissegundos para buscar o último URI de um host e o delayFactor for 5 (um valor muito alto), a Frontier aguardará 4000 milissegundos (4 segundos) antes de permitir que outro URI desse host seja processado.

* maxDelayMs- Essa configuração impõe um limite máximo no tempo de espera criado pelo delayFactor. Se definido como 1000 milissegundos, o atraso máximo entre as buscas de URI do mesmo host nunca excederá esse valor.

* minDelayMs- Essa configuração impõe um limite mínimo de politeness. Tem precedência sobre o valor calculado pelo delayFactor. Por exemplo, o valor de minDelayMs pode ser definido como 100 milissegundos. Se o delayFactor gerar uma espera de apenas 20 milissegundos, o valor de minDelayMs irá substituí-lo e a busca de URI será atrasada por 100 milissegundos.

```
<bean id="disposition" class="org.archive.crawler.postprocessor.DispositionProcessor">
<property name="delayFactor" value="5.0" />
<property name="maxDelayMs" value="30000" />
<property name="minDelayMs" value="3000" /></bean>
```

Política de tentativas

A Frontier pode ser usada para limitar o número de tentativas de busca de um URI. O Heritrix tentará recuperar um URI porque o erro de busca inicial pode ser transitório.

* maxRetries - Essa configuração limita o número de novas tentativas de busca de um URI devido a erros transitórios.

* retryDelaySeconds - Essa configuração determina o tempo do período de espera entre novas tentativas.

```
<bean id="frontier"
   class="org.archive.crawler.frontier.BdbFrontier">

  <property name="retryDelaySeconds" value="900" />
  <property name="maxRetries" value="30" />

 </bean>
 ```
 
Limites de Largura de Banda
 
A Frontier permite ao usuário limitar o uso de largura de banda, retendo URIs quando o uso da largura de banda excedeu certos limites. Como as limitações de uso da largura de banda são calculadas ao longo de um período de tempo, ainda pode haver picos de uso que excedam os limites.

* maxPerHostBandwidthUsageKbSec - Essa configuração limita a largura de banda máxima a ser usada por qualquer host. Essa configuração limita a carga colocada pelo Heritrix no host. É, portanto, uma configuração de politeness.

```
<bean id="disposition" class="org.archive.crawler.postprocessor.DispositionProcessor">
<property name="maxPerHostBandwidthUsageKbSec" value="500" />
</bean>
```

Parâmetros de extração

A partir da versão 3.1, o comportamento da Frontier em relação à extração de links pode ser controlado pelos seguintes parâmetros.

* extract404s - Essa configuração permite que o operador evite a extração de links de páginas 404 (Not Found). O configuração padrão é "true", o que mantém o comportamento pré-3.1 de extração links de páginas 404.

```
<bean id="frontier" class="org.archive.crawler.frontier.BdbFrontier">
<property name="extract404s" value="true" />
</bean>
```

* extractIndependently - Essa configuração incentiva os processadores de extração a sempre executarem a extração da melhor forma possível, mesmo que um extrator anterior tenha marcado um URI como já manipulado. Defina o valor como "true" para ativar essa configuração. O definição padrão é "false", o que mantém o comportamento pré-3.1.

### Heritrix BdbFrontier

O BdbFrontier visita URIs e sites descobertos de maneira ampla. Oferece opções de configuração para controlar como ele controla a atividade em determinados hosts. Algumas configurações permitem a tendência de finalizar hosts em andamento (rastreamento "site-first") ou que haja alternações entre todos os hosts com URIs pendentes.

Os URIs descobertos são rastreados apenas uma vez, com exceção às informações do robots.txt e DNS, que podem ser configuradas para serem atualizadas em intervalos específicos para cada host.

A partir da versão 3.1, há duas novas propriedades.

| Propriedade  | Padrão | Descrição |
| ------------- | ------------- | | ------------- | 
| largestQueuesCount| 20 | Controla quantas, das filas maiores, são rastreadas e relatadas no "relatório frontier". |
| maxQueuesPerReportCategory | 2000  | Controla o número máximo de filas por categoria listadas no "relatório frontier". |

Quando as filas decaem do top-N ou o valor é alterado no meio do rastreamento, as informações sobre as filas maiores pode não ser exata. A lista é atualizada apenas quando uma fila passa para o grupo maior.

### Detalhes da implementação

O armazenamento da fila é gerenciado por meio de um banco de dados BDB JE incorporado. É um armazenamento simples de valor-chave, portanto, a multiplicidade de filas é implementada como prefixos-chave. Cada CrawlURI é armazenado no banco de dados BDB como um binary blob serializado usando Kryo, sob uma chave que combina o prefixo de fila (classKey) e a prioridade de rastreamento do CrawlURI.

A lista de de filas active/snoozed/etc são mantidas na memória e gravadas no disco durante o ponto de verificação no formato JSON. Se você retomar a fila a partir do ponto de vereificação, o BdbFrontier será reutilizado, mas as informações necessárias da fila serão fornecidas pelos arquivos JSON. Se o banco de dados da frontier *não* for reutilizado a partir de um ponto de verificação, o banco de dados será 'truncado' e todos os dados no BdbFrontier serão descartados.

A atualização do conteúdo da frontier a partir de vários threads exige cuidado, pois é necessário fazer alterações e confirmá-las no disco sem que ocorram conflitos. Uma vez que qualquer alteração tenha sido feita, por exemplo, um WorkQueue, a chamada `wq.makeDirty ()` é usada para iniciar um processo no qual o WorkQueue é serializado para o disco e lido novamente (para assegurar a consistência, mas descartando todos os campos temporários). Isso significa que as atualizações para cada WorkQueue devem ser sincronizadas nos threads para que não haja duas atualizações ao mesmo tempo. Por exemplo:

```
 synchronized (wq) {
      ...do updates...
      wq.makeDirty();
    }
```

Tudo isso é feito usando um ObjectIdentityBdbManualCache, que torna possível interagir com o banco de dados como uma coleção simples, enquanto mantém na memória apenas o que for necessário.

## Framework Spring

O software Heritrix é baseado no framework Spring para Java. O framework Spring define uma construção chamada bean. Um bean é um componente configurável que consiste em propriedades (como o endereço de email do operador do Heritrix) e referências a outros beans. A configuração do Heritrix é realizada configurando beans. Um bean é representado como um elemento XML nos arquivos de configuração do Spring. Um exemplo de um bean é mostrado abaixo.

**fetchProcessors Spring Bean

```
<bean id="fetchProcessors" class="org.archive.modules.FetchChain">
<property name="processors">
<list>
<!-- re-check scope, if so enabled... -->
<ref bean="preselector"/>
<!--
...then verify or trigger prerequisite URIs fetched, allow crawling...
-->
<ref bean="preconditions"/>
<!-- ...fetch if DNS URI... -->
<ref bean="fetchDns"/>
<!-- <ref bean="fetchWhois"/> -->
<!-- ...fetch if HTTP URI... -->
<ref bean="fetchHttp"/>
<!-- ...extract outlinks from HTTP headers... -->
<ref bean="extractorHttp"/>
<!-- ...extract outlinks from HTML content... -->
<ref bean="extractorHtml"/>
<!-- ...extract outlinks from CSS content... -->
<ref bean="extractorCss"/>
<!-- ...extract outlinks from Javascript content... -->
<ref bean="extractorJs"/>
<!-- ...extract outlinks from Flash content... -->
<ref bean="extractorSwf"/>
</list>
</property>
</bean>
```

Esse bean consiste em um identificador exclusivo (fetchProcessors) e uma lista de propriedades que são referências a outros beans. Um exemplo de alteração de configuração seria colocar o bean extrator XML à frente do bean extrator HTTP para que o conteúdo, como feeds RSS, possa ser rastreado. O bean fetchProcessors, após essa alteração de configuração, é mostrado abaixo.

**fetchProcessors Bean com extractorXML

```
<bean id="fetchProcessors" class="org.archive.modules.FetchChain">
<property name="processors">
<list>
<!-- re-check scope, if so enabled... -->
<ref bean="preselector"/>
<!--
...then verify or trigger prerequisite URIs fetched, allow crawling...
-->
<ref bean="preconditions"/>
<!-- ...fetch if DNS URI... -->
<ref bean="fetchDns"/>
<!-- <ref bean="fetchWhois"/> -->
<!-- ...fetch if HTTP URI... -->
<ref bean="fetchHttp"/>
<!-- ...extract outlinks from HTTP headers... -->
<ref bean="extractorHttp"/>
<!-- ...extract outlinks from XML... -->
<ref bean="extractorXML"/>
<!-- ...extract outlinks from HTML content... -->
<ref bean="extractorHtml"/>
<!-- ...extract outlinks from CSS content... -->
<ref bean="extractorCss"/>
<!-- ...extract outlinks from Javascript content... -->
<ref bean="extractorJs"/>
<!-- ...extract outlinks from Flash content... -->
<ref bean="extractorSwf"/>
</list>
</property>
</bean>
```

## Diretório "action"

Cada diretório de tarefa contém um diretório "action". Colocando arquivos nesse diretório, você pode acionar ações em uma tarefa de rastreamento em andamento, como a adição de novos URIs ao rastreamento.

Em intervalos regulares (por padrão, menos de um minuto), o rastreamento observará novos arquivos nesse diretório e executará ações com base no sufixo do nome do arquivo e no conteúdo. Quando a ação for concluída, o arquivo será movido para o diretório 'done' mais próximo. (Por esse motivo, os arquivos devem ser compostos fora do diretório "action" e movidos para lá como um todo atômico. Caso contrário, um arquivo pode ser processado e movido enquanto ainda está sendo composto.)

Os seguintes sufixos de arquivos são suportados:

| Sufixo  | Descrição |  
| ------------- | ------------- |  
| `.seeds`| Um arquivo `.seeds` deve conter seeds que o operador do Heritrix deseja incluir no rastreamento. Colocar um arquivo `.seeds` no diretório "action" adicionará os seeds ao rastreamento em andamento. As mesmas diretivas que podem ser usadas em listas de seeds durante a configuração inicial de rastreamento podem ser usadas aqui. Se os seeds introduzidos no rastreameno dessa maneira já estiverem na frontier (talvez já um seed), esse método não as força. |
| `.recover` | Um arquivo `.recover` será usado como um diário de recuperação (recovery journal) tradicional. (O diário de recuperação pode reproduzir aproximadamente o estado das filas de um rastreamento e o conjunto já incluído, repetindo todos os eventos de conclusão de URI e de descoberta de URI. Um diário de recuperação reproduz menos estados do que um ponto de verificação adequado.) Em uma primeira passagem, todas as linhas que começarem com `Fs` no diário de recuperação serão consideradas incluídas, para que não possam ser enfileiradas novamente. Em seguida, em uma segunda passagem, as linhas que começarem com `F+` serão enfileiradas novamente para rastreamento (se não forem impedidas pela primeira passagem).  | 
| `.include` | Um arquivo `.include` será usado como um diário de recuperação, mas todos os URIs, independentemente do prefixo de linha, serão marcados como já incluídos, evitando que eles sejam enfileirados novamente a partir desse ponto. (Os URIs já enfileirados ainda estarão qualificados para rastreamento quando surgirem.) O uso de um arquivo `.include` é uma maneira de suprimir o novo rastreamento de URIs. |
| `.schedule` | Um arquivo `.schedule` será usado como um diário de recuperação, mas todos os URIs, independentemente do prefixo de linha, serão oferecidos para enfileiramento. (No entanto, se eles forem reconhecidos como já incluídos, eles não serão enfileirados.) O uso de um arquivo `.schedule` é uma maneira de incluir URIs em um rastreamento em andamento, inserindo-os nas filas de rastreamento do Heritrix. |
| `.force` | Um arquivo `.force` será usado como um diário de recuperação com todos os URIs marcados para agendamento forçado. O uso de um arquivo `.force` é uma maneira de garantir que os URIs já incluídos sejam reenquadrados (e, portanto, sejam rastreados novamente).

Qualquer um desses arquivos pode ser gzipado. Qualquer um dos arquivos no formato de diário de recuperação (`.recover`, `.include`, `.schedule`, `.force`) pode ter um `.s` inserido antes do sufixo funcional (por exemplo, `frontier.s.recover.gz`), o que fará com que os URIs sejam testados no escopo antes que qualquer outra inserção ocorra.

Por exemplo, o seguinte arquivo pode ser movido para o diretório "action" para agendar um URL:

**example.schedule

```
F+ http://example.com
```

Para usar o diretório action, o bean `ActionDirectory` deve ser configurado no arquivo `crawler-beans.cxml`, conforme exemplificado abaixo.

```
<bean id="actionDirectory" class="org.archive.crawler.framework.ActionDirectory">
<property name="actionDir" value="action" />
<property name="initialDelaySeconds" value="10" />
<property name="delaySeconds" value="30" />
</bean>
```

A classe org.archive.crawler.frontier.FrontierJournal contém as constantes reconhecidas como diretivas possíveis em um diário de recuperação.

Observe que as linhas 'F +' do formato de diário de recuperação podem incluir um 'hops-path' e 'via URI', que são preservados quando um URI é enfileirado pelos mecanismos acima, mas isso pode não ser uma representação completa de todo o estado de URI a partir de sua descoberta em um rastreamento normal.

## Glossário

###### Conteúdo

* Conteúdo
* Bytes, KB and statistics
* Ponto de verificação
* CrawlURI
* Data e hora
* URIs descobertos
* Discovery Path
* Frontier
* Host
* Tarefa de rastreamento
* Contagem de hops de links
* URIs pendentes
* Perfil
* Cortesia
* Estados de fila 
* URIs enfileirados
* Expressões regulares
* SHA1
* Servidor
* Spring
* SURT
* Prefixo SURT
Toe Threads

###### Bytes, KB and statistics

O Heritrix adere às seguintes convenções para exibir quantidades de bytes e bits:

| Código  | Tipo |  
| ------------- | ------------- |  
| B | Bytes |
| KB Kilobytes | 1KB = 1024 B |
| MB Megabytes | 1MB = 1024 KB |
| GB Gigabytes | 1GB = 1024 MB |
| b | bits |
| KB Kilobits | 1Kb = 1000 b |
| Mb Megabits | 1Mb = 1000 kb |
| Gb Gigabits | 1000 Mb |

Isso também se aplica a todos os logs.

###### Ponto de verificação (Checkpointing)

O ponto de verificação do Heritrix é fortemente influenciado pelo ponto de verificação do rastreador Mercator. Em um artigo sobre o Mercator, o ponto de verificação é descrito da seguinte maneira: "O ponto de verificação é uma parte importante de qualquer processo de execução longo, como um rastreamento da Web. 'Ponto de verificação' é a gravação de uma representação do estado do rastreador em um armazenamento estável. Caso aconteça uma falha, o ponto de verificação deve ser suficiente para que, ao ser lido pelo rastreador, ele consiga recuperar seu estado antes da falha e retomar o rastreamento a partir desse ponto. Por essa definição, no caso de uma falha, qualquer trabalho realizado depois do ponto de verificação mais recente é perdido, apenas o trabalho realizado anteriormente fica salvo. No Mercator, a frequência com que o thread secundário realiza um ponto de verificação é configurada pelo usuário, geralmente de 1 a 4 vezes por dia."

Ver Ponto de Verificação para informações sobre a implementação do Heritrix.

###### CrawlURI

Um URI e seus dados associados, como o URI pai e o número de links.

###### Data e hora

Todos os horários no Heritrix são GMT, assumindo que o relógio e o fuso horário no sistema local estão corretos. Isso significa que todas as datas/horas nos registros são GMT, todas as datas/horas mostradas na IUW são GMT, e todas as horas ou datas inseridas pelo usuário devem estar em GMT.

###### URIs descobertos 

Um URI descoberto é qualquer URI confirmado dentro do "escopo". Isso inclui os URIs que foram processados, estão sendo processados e terminaram o processamento. Não inclui URIs que foram "esquecidos". Os URIs esquecidos são URIs considerados fora do escopo durante a busca. É provável que isso aconteça pela alteração da definição do escopo pelo operador.

Observação: Como o mesmo URI pode ser buscado várias vezes (pelo menos na maioria das Frontiers), o número de URIs descobertos pode ser um pouco menor do que os itens combinados enfileirados, em processo e finalizados. Isso ocorre porque os URIs duplicados estão sendo enfileirados e processados. É provável que a variação seja mais alta em Frontiers que estão implementando estratégias "revisit".

###### Discovery Path

Cada URI tem um discovery path. O caminho contém um caractere para cada link ou link incorporado do seed.

A legenda dos caracteres é a seguinte:

* R - Redirect
* E - Embed
* X - Speculative embed (aggressive JavaScript link extraction)
* L - Link
* P - Prerequisite (such as DNS lookup or robots.txt)
* I - A partir da versão 3.1. Não necessariamente no material de origem, mas deduzido por convenção (como /favicon.ico)

O disovery path de um seed é uma cadeia vazia.

###### Frontier

Módulo conectável do Heritrix que mantém o estado interno do rastreamento. Ver Frontier.

###### Host

Um host pode servir vários domínios ou um domínio pode ser servido por vários hosts. Para nossos propósitos, um host é o mesmo que o nome do host em um URI. O DNS não é considerado porque é volátil e pode estar indisponível. Por exemplo, se vários URIs apontarem para o mesmo endereço IP, eles serão considerados três hosts lógicos diferentes (no mesmo nível do protocolo URI/HTTP).

Os proxies HTTP em conformidade se comportam de maneira semelhante. Eles não consideram um URI e um endereço IP intercambiáveis.

Isso não é ideal para politeness porque aplica regras de cortesia ao host físico ao invés do host lógico.

###### Tarefa de rastreamento 

Para executar um rastreamento, uma configuração deve ser criada. No Heritrix, essa configuração é chamada de tarefa de rastreamento. Uma tarefa de rastreamento é baseada no framework Spring. A tarefa usa beans Spring como objetos de configuração que definem o rastreamento.

###### Contagem de hops de links

Número de links, seguidos do seed,  para alcançar um URI. Os seeds têm uma contagem de hops de links zero. A contagem de hops de link é igual à contagem de `Ls` em um discovery path de URIs.

###### URIs pendentes

Número de URIs que estão aguardando processamento detalhado. É, também, o número de URIs descobertos que não foram inspecionados quanto ao escopo ou às duplicatas. Dependendo da implementação da Frontier, isso pode sempre ser zero. Também pode ser um número ajustado que considera duplicatas.

###### Perfil

Um perfil é um modelo base para uma tarefa de rastreamento. Contém todas as configurações de uma tarefa de rastreamento, mas não é considerado "rastreável". O Heritrix não permite que um perfil seja diretamente rastreado, somente tarefas baseados em perfis.

Um exemplo comum de uma configuração de perfil é deixar a propriedade `metadata.operatorContactUrl` indefinida para forçar o operador a inserir um valor válido. Isso se aplica ao perfil padrão que acompanha o Heritrix. Outros exemplos seriam deixar a lista de seeds vazia ou não especificar um processador obrigatório.

Os perfis podem ser usados como modelos, deixando suas configurações em um estado inválido. Dessa forma, um operador é forçado a escolher suas configurações ao criar uma tarefa a partir de um perfil. Isso pode ser vantajoso quando um administrador precisa configurar muitas tarefas de rastreamento diferentes para acomodar sua política de rastreamento.

###### Cortesia

Tentativas do software do rastreador de limitar a carga do site que ele está rastreando. Sem restrições de cortesia, o rastreador pode sobrecarregar sites menores e até mesmo fazer com que sites de tamanhos moderados fiquem devagar. A menos que você tenha permissão expressa para rastrear um site de forma agressiva, você deve aplicar regras rígidas de cortesia em todos os rastreamentos.

###### Estados de filas

| Estado  | Significado |  
| ------------- | ------------- |
| ready | Filas prontas para emitir um URL imediatamente. |
| in-process | Filas que emitiram um URL que está sendo processado no momento. |
| snoozed | Devido ao atraso de rastreamento ou ao tempo de espera entre tentativas. |
| active | Total de in-proncess + ready + snoozed. |
| inactive | Filas que atualmente não estão sendo analisadas (devido à rotação de filas). |
| inegilible | Filas inativas em que a precedência de fila excede a precedência de valor mínimo. |
| retired | Desativadas por algum motivo, ex. a fila atingiu sua cota alocada. |
| exhausted | Filas que estão vazias. |

###### URIs enfileirados

O número de URIs enfileirados e aguardando processamento. Os URIs enfileirados incluem todos os URIs que falharam em serem buscados, mas serão tentados novamente.

###### Expressões regulares

Todas as expressões regulares no Heritrix são expressões regulares Java.

As expressões regulares Java diferem daquelas usadas em outras linguagens de programação, como o Perl. Para obter informações detalhadas sobre expressões regulares Java, consulte a descrição do Java API da classe `java.util.regex.Pattern`.

###### SHA1

Algorítmo (Secure Hash Algorithm (SHA)) usado pelo Heritrix para criptografar arquivos.

###### Servidor (Server)

Um servidor é um serviço em um host. Pode haver mais de um serviço em um host. Diferentes serviços são geralmente diferenciados pelo número da porta (port number).

###### Spring

Spring é uma estrutura de aplicativo Java usada pelo Heritrix. As tarefas de rastreamento são baseadas nos componentes Spring, conhecidos como "beans". Para visualizar os beans Spring de uma configuração de rastreamento, use a funcionalidade "Browse Beans".

###### SURT

SURT significa Sort-friendly URI Reordering Transform. É uma transformação aplicada a URIs que faz com que sua representação da esquerda para a direita corresponda melhor à hierarquia natural dos nomes de domínio.

Um URI <scheme: //domain.tld/path? Query> tem uma forma SURT <scheme://(tld,domain,)/path?query.

A conversão para o formulário SURT também envolve transformar todos os caracteres em minúsculas e a alteração do esquema https para http. Além disso, o caractere "/" após um componente de autoridade de URI só aparecerá no formato SURT se estiver em formato URI simples. Um exemplo de componente de autoridade de URI é a terceira barra em um URI HTTP regular. Essa convenção se mostra importante ao usar URIs reais como uma abreviação de prefixos SURT.

Os URIs de formato SURT normalmente não são usados para especificar URIs exatos para busca. Em vez disso, a forma SURT é útil ao comparar ou classificar URIs. URIs de formato SURT são classificados em grupos naturais. Por exemplo, todos os URIs "archive.org" serão adjacentes, independentemente de subdomínios como "books.archive.org" ou "movies.archive.org".

Mais importante, um URI de formato SURT ou uma versão truncada de um URI de formato SURT podem ser usados como prefixos SURT. Um prefixo SURT geralmente corresponderá a todos os URIs dentro de uma área comum de interesse. Por exemplo, o prefixo http://(is será compartilhado por todos os URIs no domínio de nível superior `.is`.

###### Prefixo SURT

Um URI de  formato SURT, especialmente se truncado, pode ser útil como um "prefixo SURT"; uma cadeia de prefixos compartilhados de todos os URIs de formulário SURT na mesma área de interesse. Por exemplo, o prefixo http://(is. será compartilhado por todos os URIs de formato SURT no domínio de nível superior `.is`. O prefixo http://(org, archive.www,)/movies será compartilhado por todos os URIs em www.archive.org com um caminho que comece com /movies. http://(org,archive.www,)/movies é também um URI de formato SURT completo válido.

Uma coleção de prefixos SURT classificados é uma maneira eficiente de especificar o escopo de rastreamento desejado. Por exemplo, qualquer URI cujo formato SURT comece com qualquer um dos prefixos deve ser incluído no escopo.

Um breve conjunto de convenções pode ser usado para calcular um "prefixo SURT implícito" de um URI normal (por exemplo, um URI fornecido como um seed de rastreamento). Essas convenções são:

1. Converter o URI para seu formato SURT.
2. Se houver pelo menos três barras ("/") no formato SURT, remova todos os caracteres após a última barra. Por exemplo, http://(org,example,www,)/main/subsection/ não é alterado. http://(org,example,www,)/main/subsection é truncado para http://(org,example,www,)/main/. http://(org.example,www,)/ não é alterado e http://(org,example,www) não é alterado.
3. Se o formato final terminar com um parentêses fora (")", remova o parênteses. Cada um dos exemplos acima, exceto o último, permanece inalterado. O último, http://(org,example,www,) , torna-se becomes http://(org,example,www,.

Isso permite que muitos URIs de seeds, em suas formas normais, indiquem os prefixos SURT mais úteis para rastrear URIs relacionados. A presença ou ausência de um "/" à direita, em URIs sem informações de caminhos adicionais, é um indicador se os subdomínios do domínio fornecido devem ser incluídos.

Por exemplo, o seed http://www.archive.org/ se tornará o formato SURT e fornecerá o prefixo SURT http://(org,archive,www,)/, e é o prefixo de todas as URIs de formato SURT em www.archive.org. No entanto, qualquer URI de subdomínio como http://homepages.www.archive.org/directory seria excluído porque seu formato SURT http://homepages.www.archive.org/directory não inicia com o prefixo SURT completo, incluindo o ")" removido do seed.

[Observação: esse parágrafo aplica-se apenas ao H1] Em contraste, o seed http://www.archive.org (note a falta de barra final) se tornará o formato SURT http://(org,archive,www,) e o prefixo SURT implícito http://(org,archive,www, (observe a falta de parênteses à direita). Esse será o prefixo de todos os URIs em www.archive.org, bem como quaisquer URIs de subdomínio como http://homepages.www.archive.org/directory, porque o prefixo SURT completo aparece no formato URI SURT de subdomínio.

###### Toe Threads

Ao realizar um rastreamento, o Heritrix emprega um número configurável de Toe Threads para processar URIs. Cada um desses threads solicitará um URI da Frontier, aplicará o conjunto de processadores a ele e, finalmente, o reportará como concluído para a Frontier.

## Configurando o escopo do rastreamento usando DecideRules

O escopo de rastreamento define um conjunto de possíveis URIs que podem ser capturados por um rastreamento. Esses URIs são determinados pelos DecideRules, que trabalham em conjunto para limitar ou expandir o conjunto de URIs rastreados. Cada DecideRule, quando apresentado com um objeto (na maioria das vezes algum tipo de URI), responde com uma das três decisões:

* ACCEPT: O objeto é aceito.
* REJECTED: O objeto é rejeitado.
* PASS: Nenhuma decisão foi feita, mantém-se a anterior.

Um URI sob consideração começa sem status definido. Cada regra é aplicada por vez ao URI candidato. Se a regra decidir ACCEPT ou REJECT, o status do URI será definido de acordo. Depois que todas as regras forem aplicadas, o URI será determinado como "no escopo" se seu status for ACCEPT. Se seu status for REJECT, será descartado.

Sugerimos começar as regras com nossas configurações padrões recomendadas e realizar pequenos rastreamentos de teste. Com esses testes, tente entender porque certos URIs são determinados ou descartados sob essas configurações. Em seguida, faça pequenas alterações individuais no escopo para obter efeitos desejados que não sejam padrões. Criar um novo conjunto de regras a partir do zero pode ser difícil e pode facilmente resultar em rastreamentos que não podem fazer o progresso mínimo que outras partes do rastreador esperam. Da mesma forma, fazer muitas mudanças de uma só vez pode obscurecer a importância da interação e ordenação das regras.

### DecideRules

A tabela a seguir lista os DecideRules disponíveis:

| Decide Rule  | Descrição |  
| ------------- | ------------- |
| AcceptDecideRule | Aceita qualquer URI. |
| ContentLengthDecideRule | Aceita URIs se o comprimento do conteúdo for menor que o limite. O limite padrão é 2^63, significando que qualquer documento será aceito. |
| PathologicalPathDecideRule | Rejeita qualquer URI que contém um número excessivo de segmentos de caminho idênticos e consecutivos. Por exemplo, http://example.com/a/a/a/a/a/foo.html |
| PredicatedDecideRule | Aplica uma decisão configurada apenas se um teste for avaliado como "true". |
| ExternalGeoLocationDecideRule | Aceita URIs localizados em um determinado país. |
| FetchStatusDecideRule | Aplica a decisão configurada a qualquer URI que tenha um status de busca igual à configuração do "target-status". |
| HasViaDecideRule | Aplica a decisão configurada a qualquer URI que tenha uma "via". Uma via é qualquer URI que seja um seed ou algum tipo de adição feita durante o rastreamento. |
| HopCrossesAssignmentLevelDomainDecideRule | Aplica a decisão configurada a qualquer URI que seja diferente na parte de seu nome de host/domínio que é atribuído/vendido por registradores. A parte é referida como o "assignment-level-domain" (ALD). |
| IdenticalDigestDecideRule | Aplica a decisão configurada a qualquer URI cujo histórico anterior de conteúdo digest corresponda à ultima busca. |
| MatchesListRegexDecideRule | Aplica a decisão configurada a qualquer URI que corresponda às expressões regulares fornecidas. |
| NotMatchesListRegexDecideRule | Aplica a decisão configurada a qualquer URI que não corresponda às expressões regulares fornecidas. |
| MatchesRegexDecideRule | Aplica a decisão configurada a qualquer URI que corresponda à expressão regular fornecida. |
| ClassKeyMatchesRegexDecideRule | Aplica a decisão configurada a qualquer URI chave de classe que corresponda à expressão regular fornecida. Um URI chave de classe é uma cadeia que especifica o nome da fila Frontier na qual um URI deve ser colocado. |
| ContentTypeMatchesRegexDecideRule | Aplica a decisão configurada a qualquer URI cujo tipo de conteúdo esteja presente e corresponda à expressão regular fornecida. |
| ContentTypeNotMatchesRegexDecideRule | Aplica a decisão configurada a qualquer URI cujo tipo de conteúdo não corresponda à expressão regular fornecida. |
| FetchStatusMatchesRegexDecideRule | Aplica a decisão configurada a qualquer URI que tenha um status de busca que corresponda à expressão regular fornecida. |
| FetchStatusNotMatchesRegexDecideRule | Aplica a decisão configurada a qualquer URI que tenha um status de busca que não corresponda à expressão regular suprida. |
| HopsPathMatchesRegexDecideRule | Aplica a decisão configurada a qualquer URI cujo "hops-path" corresponda à expressão regular fornecida. O hops-path é uma string que consiste em caracteres que representam o caminho que foi usado para acessar o URI. Um exemplo de um hops-path é "LLXE". |
| MatchesFilePatternDecideRule | Aplica a decisão configurada a qualquer URI cujo sufixo corresponda à expressão regular fornecida. |
| NotMatchesFilePatternDecideRule | Aplica a decisão configurada a qualquer URI cujo sufixo não corresponda à expressão regular fornecida. |
| NotMatchesRegexDecideRule | Aplica a decisão configurada a qualquer URI que não corresponda à expressão regular fornecida. |
| NotExceedsDocumentLengthThresholdDecideRule | Aplica a decisão configurada a qualquer URI cujo comprimento de conteúdo não exceda o limite configurado. O tamanho do conteúdo vem do cabeçalho HTTP ou do tamanho do conteúdo baixado do URI. A partir da versão 3.1, essa regra foi renomeada para ResourceNoLongerThanDecideRule. |
| ExceedsDocumentLengthThresholdDecideRule | Aplica a decisão configurada a qualquer URI cujo tamanho do conteúdo exceda o limite configurado. O tamanho do conteúdo vem do cabeçalho HTTP ou do tamanho do conteúdo baixado do URI. A partir da versão 3.1, essa regra foi renomeada para ResourceLongerThanDecideRule. |
| SurtPrefixedDecideRule | Aplica a decisão configurada a qualquer URI (expresso na forma SURT) que comece com um dos prefixos no conjunto configurado. Esse DecideRule retorna true quando o prefixo de um determinado URI corresponde a qualquer um dos SURTs listados. A lista de SURTs pode ser configurada de diferentes maneiras: o parâmetro surtsSourceFile especifica um arquivo para ler a lista de SURTs. Se o parâmetro seedsAsSurtPrefixes estiver definido como true, SurtPrefixedDecideRule adicionará todos os seeds à lista SURTs. Se a propriedade alsoCheckVia estiver configurada como true (padrão false), SurtPrefixedDecideRule também considerará Via URIs na correspondência. A partir da versão 3.1, o parâmetro "surtsSource" pode ser qualquer ReadSource, como um ConfigFile ou um ConfigString. Isso dá ao SurtPrefixedDecideRule a flexibilidade da propriedade "textSource" do bean TextSeedModule. |
| NotSurtPrefixedDecideRule | Aplica a decisão configurada a qualquer URI (expresso na forma SURT) que não comece com um dos prefixos no conjunto configurado. | 
| OnDomainsDecideRule | Aplica a decisão configurada a qualquer URI que esteja em um dos domínios do conjunto configurado. |
| NotOnDomainsDecideRule | Aplica a decisão configurada a qualquer URI que não esteja em um dos domínios do conjunto configurado. |
| OnHostsDecideRule | Aplica a decisão configurada a qualquer URI que esteja em um dos hosts do conjunto configurado. |
| NotOnHostsDecideRule | Aplica a decisão configurada a qualquer URI que não esteja em um dos hosts do conjunto configurado. |
| ScopePlusOneDecideRule | Aplica a decisão configurada a qualquer URI que seja um nível além do escopo configurado. |
| TooManyHopsDecideRule | Rejeita qualquer URI cujo número total de hops esteja acima do limite configurado. |
| TooManyPathSegmentsDecideRule | Rejeita qualquer URI cujo número total de segmentos de caminho esteja acima do limite configurado. Um segmento de caminho é uma cadeia no URI separado por um caractere "/", sem incluir o primeiro "//". |
| TransclusionDecideRule | aceita qualquer URI cujo path-from-seed termine em pelo menos um hop que não seja navlink. Um hop de navlink é representado por um "L". Além disso, o número de hops não-navlink no path-from-seed não pode exceder o valor configurado. |
| PrerequisiteAcceptsDecideRule | aceita todos os URIs de "pré-requisito". Os URIs de pré-requisito são aqueles cujo caminho de hop tem um "P" na última posição. |
| RejectDecideRule | Rejeita qualquer URI. |
| ScriptedDecideRule | Aplica a decisão configurada a qualquer URI que passe no teste das regras de um script JSR-223. A fonte do script deve ser uma função de um argumento chamada decisionFor. A função retorna o DecideResult apropriado. Variáveis disponíveis para o script incluem o objeto (o objeto a ser avaliado, como um URI), "self " (a ocorrência ScriptDecideRule), e contexto (ApplicationContext do rastreamento, do qual todos os beans de rastreamento nomeados são alcançáveis). |
| SeedAcceptDecideRule | aceita todos os URIs "seeds" (aqueles para os quais isSeed é "true"). |

DecideRules são configurados pelo bean com o id "scope" sob a propriedade denominada "rules".

### DecideRuleSequence Logging

Ative o log `FINEST` na classe `org.archive.crawler.deciderules.DecideRuleSequence` para observar todas as avaliações de URI do DecideRule. Isso pode ser feito no arquivo `logging.properties`.

**logging.properties 

```
org.archive.modules.deciderules.DecideRuleSequence.level = FINEST
```

em conjunto com o argumento da VM -Dsysprop,

```
-Djava.util.logging.config.file=/path/to/heritrix3/dist/src/main/conf/logging.properties
```

### escopo crawler-bean

A seção do bean de escopo do arquivo `crawler-beans.cxml` é reproduzida abaixo.

```
<bean id="scope" class="org.archive.modules.deciderules.DecideRuleSequence">

<property name="rules">

<list>
<!-- Begin by REJECTing all... -->
<bean class="org.archive.modules.deciderules.RejectDecideRule">
    </bean>

<!--
 ...then ACCEPT those within configured/seed-implied SURT prefixes...
-->

<bean class="org.archive.modules.deciderules.surt.SurtPrefixedDecideRule">

<!--
 <property name="seedsAsSurtPrefixes" value="true" />
-->
<!-- <property name="alsoCheckVia" value="true" /> -->
<!-- <property name="surtsSourceFile" value="" /> -->

<!--
 <property name="surtsDumpFile" value="surts.dump" />
-->
</bean>

<!--
 ...but REJECT those more than a configured link-hop-count from start...
-->

<bean class="org.archive.modules.deciderules.TooManyHopsDecideRule">
<!-- <property name="maxHops" value="20" /> -->
</bean>

<!--
 ...but ACCEPT those more than a configured link-hop-count from start...
-->

<bean class="org.archive.modules.deciderules.TransclusionDecideRule">
<!-- <property name="maxTransHops" value="2" /> -->
<!-- <property name="maxSpeculativeHops" value="1" /> -->
</bean>

<!--
 ...but REJECT those from a configurable (initially empty) set of REJECT SURTs...
-->

<bean class="org.archive.modules.deciderules.surt.SurtPrefixedDecideRule">
<property name="decision" value="REJECT"/>
<property name="seedsAsSurtPrefixes" value="false"/>
<property name="surtsDumpFile" value="negative-surts.dump"/>
<!-- <property name="surtsSourceFile" value="" /> -->
</bean>

<!--
 ...and REJECT those from a configurable (initially empty) set of URI regexes...
-->

<bean class="org.archive.modules.deciderules.MatchesListRegexDecideRule">
<!-- <property name="listLogicalOr" value="true" /> -->

<!--
 <property name="regexList">
           <list>
           </list>
          </property>
-->
</bean>

<!--
 ...and REJECT those with suspicious repeating path-segments...
-->

<bean class="org.archive.modules.deciderules.PathologicalPathDecideRule">
<!-- <property name="maxRepetitions" value="2" /> -->
</bean>

<!--
 ...and REJECT those with more than threshold number of path-segments...
-->

<bean class="org.archive.modules.deciderules.TooManyPathSegmentsDecideRule">
<!-- <property name="maxPathDepth" value="20" /> -->
</bean>

<!--
 ...but always ACCEPT those marked as prerequisitee for another URI...
-->
<bean class="org.archive.modules.deciderules.PrerequisiteAcceptDecideRule">
    </bean>
</list>
</property>
</bean>
```

## Suporte Whois

A partir da versão 3.1, um fetcher opcional é fornecido para dados do domínio 'whois'. Um pequeno conjunto de servidores 'whois' bem estabelecidos é pré-configurado. O fetcher usa uma interpretação ad-hoc/intuitive de um URI de esquema 'whois:'.

```
<bean id="fetchWhois" class="org.archive.modules.fetcher.FetchWhois">
<property name="specialQueryTemplates">
<map>
<entry key="whois.verisign-grs.com" value="domain %s" />
<entry key="whois.arin.net" value="z + %s" />
<entry key="whois.denic.de" value="-T dn %s" />
</map>
</property>
</bean>
```

Para configurar um seed whois, insira o seed no seguinte formato: whois://hostname/path (por exemplo,  whois://archive.org.) O fetcher whois tentará resolver cada host que o rastreamento encontra usando o domínio mais atribuído e o endereço IP do URL rastreado. Portanto, se você rastrear http://www.archive.org/details/texts, o fetcher whois tentará resolver whois:archive.org  e whois:207.241.224.2.

No momento, a funcionalidade whois é experimental. O bean fetchWhois é comentado no perfil padrão.

## Configurações básicas de tarefa de rastreamento

* Limites de rastreamento
* maxToeThreads
* metadata.operatorContactUrl
* Política de honra de robots

As configurações de rastreamento são configuradas editando o arquivo `crawler-beans.cxml` de uma tarefa. Cada tarefa tem um `crawler-beans.cxml` que contém a configuração Spring para a tarefa.

### Limites de rastreamento

Além dos limites impostos ao escopo do rastreamento, é possível impor limites arbitrários à duração e à extensão do rastreamento com as seguintes configurações:

* maxBytesDownload - Para o rastreamento depois que um número fixo de bytes tiver sido baixado. Zero significa ilimitado
* maxDocumentDownload - Para o rastreamento depois de baixar um número fixo de documentos. Zero significa ilimitado.
* maxTimeSeconds- Para o rastreamento após um determinado número de segundos. Zero significa ilimitado. Para referência, há 3600 segundos em uma hora e 86400 segundos em um dia.

Para definir esses valores, modifique o bean CrawlLimitEnforcer.

```
<bean id="crawlLimitEnforcer" class="org.archive.crawler.framework.CrawlLimitEnforcer">
<property name="maxBytesDownload" value="100000000" />
<property name="maxDocumentsDownload" value="100" />
<property name="maxTimeSeconds" value="10000" />
</bean>
```

**Observação

* Estes não são limites rígidos. Quando um dos limites for atingido, uma finalização completa da tarefa de rastreamento será acionada. Os URIs já sendo rastreados serão concluídos. Consequentemente, o limite definido será excedido por outro valor.

### maxToeThreads

O número máximo de toe threads para executar.

Se estiver executando um rastreamento de domínio menor que 100 hosts, um valor que seja aproximadamente duas vezes o número de hosts deve ser suficiente. Valores maiores que 150-200 raramente valem a pena, a menos que sejam executados em máquinas com recursos excepcionais.

```
<bean id="crawlController" class="org.archive.crawler.framework.CrawlController">
<property name="maxToeThreads" value="50" />
</bean>
```

### metadata.operatorContactUrl

O URI do iniciador de rastreamento. Essa configuração fornece ao administrador de um host que foi/está sendo rastreado um URI para referência em caso de problemas.

```
<bean id="simpleOverrides" class="org.springframework.beans.factory.config.PropertyOverrideConfigurer">


<property name="properties">


<value>


   1. This Properties map is specified in the Java 'property list' text format
   2. http://java.sun.com/javase/6/docs/api/java/util/Properties.html#load%28java.io.Reader%29


metadata.operatorContactUrl=http://www.archive.org
metadata.jobName=basic
metadata.description=Basic crawl starting with useful defaults


##..more?..##


</value>
</property>
</bean>
```

### Política Robots Honoring 

1. CLASSIC - Obedece todas as regras robots.txt para o user-agent configurado.
2. IGNORE - Ignora todas as regras robots.txt.
3. CUSTOM - Defere a uma definição robots customizada. 
4. MOST_FAVORED - Rastreia URIs se o robots.txt permitir que qualquer user-agent rastreie. 
5. MOST_FAVORED_SET- Requer que um conjunto de user-agent alternativos seja fornecido. Para cada página, se algum agent no conjunto for permitido, a página será rastreada.

```
<bean id="robotsHonoringPolicy" class="org.archive.modules.net.RobotsHonoringPolicy">
  <property name="type" value="CLASSIC"/>
</bean>
```

A escolha de opções 3-5 requer informações adicionais sobre configurações.

A partir da versão 3.1, a política de honra de robots pode ser definida no bean "metadata" usando a propriedade "robotsPolicyName".

```
<bean id="metadata" class="org.archive.modules.CrawlMetadata" autowire="byName">
...
    <property name="robotsPolicyName" value="obey"/>
...
</bean>
```

Valores válidos para "robotsPolicyName":

obey - Obedece as diretivas do robots.txt
classic - O mesmo que "obey"
ignore - Ignora as diretivas do robots.txt

A política de honra de robots também pode ser definida criando um bean que use uma das seguintes classes. O bean deve estar vinculado ao bean "metadados".


| Nome da classe | Desrição |  
| ------------- | ------------- |
| org.archive.modules.net.FirstNamedRobotsPolicy | Usa uma lista ordenada de User-Agents. O primeiro User-Agent na lista é o User-Agent que é regularmente configurado.  Os outros User-Agents na lista são aqueles configurados na lista candidateUserAgents. Assim que um conjunto de diretivas correspondentes for encontrado, essas diretivas serão seguidas. Se nenhuma for encontrada, as diretivas "wildcards" serão usadas (se existirem). |
| org.archive.modules.net.IgnoreRobotsPolicy | Ignora as diretivas do robots.txt |
| org.archive.modules.net.ObeyRobotsPolicy | Obedece as diretivas do robots.txt |
| org.archive.modules.net.CustomRobotsPolicy | Segue uma política de robots personalizada, em vez das próprias declarações do site |
| org.archive.modules.net.MostFavoredRobotsPolicy | Segue a política de robots mais favorecida que permite que um URI seja rastreado se o User-Agent configurado convencionalmente, ou qualquer número de User-Agents alternativos, forem permitidos. |


O exemplo abaixo mostra o uso da política org.archive.modules.net.IgnoreRobotsPolicy.

``` 
<bean id="ignorePolicy" class="org.archive.modules.net.IgnoreRobotsPolicy">
</bean>


<bean id="metadata" class="org.archive.modules.CrawlMetadata" autowire="byName">

        <property name="robotsPolicyName"> <ref bean="ignorePolicy"/> </property>

</bean>
```

Além disso, a partir da versão 3.1, a análise do robots.txt agora rastreia wildcards nas diretivas Disallow, que é um desvio comum do padrão original. A wildcard é equivalente ao mesmo prefixo de caminho sem o caractere do wildcard de rastreamento. Além disso, o tratamento das diretivas "Allow" e "Disallow" sobrepostas corresponde ao entendimento intuitivo dos webmasters e de outros rastreadores. As diretivas mais específicas/mais longas têm precedência.

## Códigos de status

Códigos de status

Cada URI rastreado recebe um código de status. Este código (ou número) indica o resultado de uma busca de URI no Heritrix.

Códigos que variam de 200 a 599 são códigos de resposta HTTP padrão e informações sobre seus significados estão disponíveis na [página da Web do consórcio World Wide Web] (http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html).

Outros códigos de status do Heritrix estão listados abaixo.

| Código | Significado |  
| ------------- | ------------- |
| 1 | Pesquisa de DNS bem-sucedida |
| 0 | Busca nunca iniciada (protocolo não suportado ou URI ilegal) |
| -1 | Falha na pesquisa de DNS |
| -2 | Conexão HTTP falhou |
| -3 | Conexão HTTP quebrou |
| -4 | Tempo limite de HTTP |
| -5 | Exceção de tempo de execução inesperada. Veja runtime-errors.log. |
| -6 | A pesquisa de domínio de pré-requisito falhou, impedindo a tentativa de busca. (O pré-requisito principal é a pesquisa WHOIS. Se esse código aparecer, é provável que o domínio não exista mais.) |
| -7 | URI reconhecida como não suportada ou ilegal. |
| -8 | Várias tentativas falharam, limite de novas tentativas foi atingido. |
| -50 | Status temporário atribuído a URIs aguardando condições prévias. Aparecimento em logs pode ser um bug. |
| -60 | Status de falha atribuído a URIs. Eles não puderam ser enfileirados pela Frontier e podem ser inacessíveis. |
| -61 | A obtenção do pré-requisito robots.txt falhou, impedindo uma tentativa de busca. |
| -62 | Algum outro pré-requisito falhou, impedindo uma tentativa de busca. |
| -63 | Um pré-requisito (de qualquer tipo) não pôde ser agendado, impedindo uma tentativa de busca. |
| -404 | Resposta HTTP vazia, interpretada como um 404. |
| -3000 | Ocorrência uma condição de erro Java grave, como OutOfMemoryError ou StackOverflowError, durante o processamento de URI. |
| -4000 | Detecção "chaff" de armadilhas/conteúdo com valor insignificante aplicado. |
| -4001 | O URI encontra-se muitos link-hops longe do seed.
| -4002 | O URI está a muitos hops incorporados/transitivos longe do último URI no escopo. |
| -5000 | O URI está fora do escopo durante a segunda examinação. Isso só acontece se o escopo mudar durante o rastreamento. |
| -5001 | Bloqueado de realizar buscas pela configuração do usuário.
| -5002 | Bloqueado por um processador personalizado, que poderia incluir o mapeador de hash (para rastreamento de vários nós), se ativado. |
| -5002 | Bloqueado por exceder uma cota estabelecida. |
| -5004 | Bloqueado por excedor um tempo de execução estabelecido. |
| -6000 | Excluído da Frontier pelo usuário.
| -7000 | Thread de processamento foi terminado pelo operador. Isso pode acontecer se um thread for uma condição sem resposta. |
| -9998 | As regras do Robots.txt impediram a busca. |

**Observação:

* Códigos e explicações também estão disponíveis no link "Help" na interface do usuário da web.
* Os códigos de status estão sujeitos a alterações entre as versões do Heritrix. Novos códigos podem ser adicionados para abordar novas áreas de problemas.

Os códigos de status do Heritrix também estão documentados no (código-fonte) [http://crawler.archive.org/xref/org/archive/crawler/datamodel/FetchStatusCodes.html#38] (ou no FishEye para H1 e H3) e no (glossário) [http://crawler.archive.org/articles/user_manual/glossary.html].

## Planilhas (sheets)

As planilhas fornecem a capacidade de substituir as configurações padrões por domínio. Planilhas são coleções de substituições. Contêm valores alternativos para propriedades de objetos que devem ser aplicados em determinados contextos. O destino é especificado como um property-path arbitrariamente longo, que é uma string descrevendo como acessar a propriedade a partir de um beanName em um BeanFactory.

Planilhas permitem que as configurações sejam sobrepostas com novos valores aplicados por domínios de nível superior (com, net, org, etc), por domínios de segundo nível (yahoo.com, archive.org, etc.), por subdomínios (crawler.archive. org, tech.groups.yahoo.com, etc.) e caminhos principais do URI (directory.google.com/Top/Computers/, etc.). Não há limite de tamanho do prefixo de domínio/caminho que especifica; a sintaxe de prefixos SURT é usada.

A criação de uma planilha envolve a configuração do arquivo `crawler-beans.cxml`, que contém a configuração Spring de uma tarefa.

Por exemplo, se você tiver permissão explícita para rastrear determinados domínios sem o limite habitual de cortesia rate-limiting, uma planilha poderá ser usada para criar uma política de rastreamento menos cortês associada a alguns desses domínios de destino. A configuração dessa planilhas para os domínios example.com e example1.com é mostrada abaixo. Este exemplo permite até 5 solicitações pendentes paralelas de cada vez (em vez do padrão 1) e elimina as pausas comuns entre as solicitações

**Observação importante**: A menos que um site alvo forneça permissão explícita para rastreamento extra-agressivo, os padrões típicos do Heritrix, que limitam o rastreador a não mais de um pedido pendente por vez, com esperas de vários segundos entre solicitações e esperas mais longas quando o site está respondendo mais devagar, é o caminho mais seguro. O rastreamento menos cortês pode fazer com que o rastreador seja totalmente bloqueado pelos webmasters. Por fim, mesmo com permissão, certifique-se de que a string do User-Agent de seu rastreador inclui um link para informações válidas de contato do operador de rastreamento, para que você possa ser alertado e corrigir qualquer efeito colateral indesejado. 

```
<!-- SHEETOVERLAYMANAGER: manager of sheets of contextual overlays
Autowired to include any SheetForSurtPrefix or
SheetForDecideRuled beans -->
<bean id="sheetOverlaysManager" autowire="byType"
class="org.archive.crawler.spring.SheetOverlaysManager">
</bean>


<bean class='org.archive.crawler.spring.SurtPrefixesSheetAssociation'>
<property name='surtPrefixes'>
<list>
<value>
http://(com,example,www,)/
</value>
<value>
http://(com,example1,www,)/
</value>
</list>
</property>
<property name='targetSheetNames'>
<list>
<value>lessPolite</value>
</list>
</property>
</bean>


<bean id='lessPolite' class='org.archive.spring.Sheet'>
<property name='map'>
<map>
<entry key='disposition.delayFactor' value='0.0'/>
<entry key='disposition.maxDelayMs' value='0'/>
<entry key='disposition.minDelayMs' value='0'/>
<entry key='queueAssignmentPolicy.parallelQueues' value='5'/>
</map>
</property>
</bean>
```

A planilha nomeada `lessPolite`, no exemplo, define sobreposições para várias propriedades de cortesia. A planilha é associada a domínios adicionando um bean `org.archive.crawler.spring.SurtPrefixesSheetAssociation`. Este bean contém os domínios para os quais as sobreposições serão aplicadas.

A penalidade de desempenho no uso de sobreposições é pequena, uma vez que etapas extras no início do processamento do URI decoram o URI com todas as sobreposições aplicáveis de uma vez só.

### Precedência

* `DecideRuledSheetAssociations` tem precedência sobre `SurtPrefixesSheetAssociations`
* As `DecideRuledSheetAssociations` que aparecem posteriormente em `crawler-beans.cxml` têm precedência sobre as que aparecem mais cedo
* No caso de `SurtPrefixesSheetAssociations` concorrentes, SURTs mais específicos têm precedência sobre os menos específicos

### Rastreamento com várias máquinas

Rastreamentos feitos com várias máquinas podem ser executados usando o hashCrawlMapper para atribuir URIs a máquinas diferentes com base em seus nomes de host.

Primeiro adicione o seguinte bean:

```
<bean id="hashCrawlMapper">
         <property name="enabled" value="true" />
         <property name="localName" value="0" />
         <property name="diversionDir" value="diversions" />
         <property name="checkUri" value="true" />
         <property name="checkOutlinks" value="false"/>
         <property name="rotationDigits" value="10" />

         <!-- Number of crawlers being used in the multi-machine setup-->
         <property name="crawlerCount" value="7" />
 </bean>
 ```
 
Chame o bean na cadeia do CandidateProcessor:
 
 ```
 <bean id="candidateProcessors">
  <property name="processors">
   <list>
<!-- apply scoping rules to each individual candidate URI... -->
 <ref bean="candidateScoper"/>
<!-- Check URIs for crawler assignment -->
 <ref bean="hashCrawlMapper"/>
 ```
 
Todos os rastreadores receberão a mesma lista inicial de seeds. O único valor que deve ser alterado entre diferentes rastreadores é "localName".

Quando o rastreamento iniciar, o diretório "diversions" começará a ser preenchido com arquivos .divert. Cada arquivo .divert será nomeado $timestamp-$localname_currentmachine-to-$localname_assignedmachine.

Os operadores de rastreamento devem configurar um processo em que os URIs contidos nos arquivos .divert sejam copiados de cada rastreador para seus rastreadores atribuídos e enfileirados no rastreamento ativo. Por exemplo, converter o arquivo .divert no formato esperado pelo diretório de ação como um arquivo .schedule seria suficiente.

## Heritrix3 no Mac OS X

### Usando o JAVA 6

* Baixe o Java para Mac OS X
* Vá para `/Applications/Utilities/Java` e execute o **Java Preferences.app**
* No painel *Java application versions*, arraste o **Java SE 6** para o topo da lista.

### Heritrix3 no Windows


### REST API

Este manual descreve a interface de programação de aplicativos REST (API) do rastreador da web Heritrix. Heritrix é o rastreador de web do Internet Archive com qualidade de arquivamento extensível, escálavel e de código aberto.. Para mais informações sobre o Heritrix, visite http://crawler.archive.org/.

Este documento destina-se a desenvolvedores de aplicativos e administradores interessados em controlar o rastreador da Web do Heritrix por meio de sua API REST.

Qualquer cliente que suporte HTTPS pode ser usado para invocar a API do Heritrix. Os exemplos neste documento usam o curl da ferramenta de linha de comando, que é normalmente encontrado na maioria dos ambientes unix. Curl está disponível para muitos sistemas, incluindo o Windows.

### Crate New Job

```
POST https://(heritrixhost):8443/engine [action=create]
```

Cria uma nova tarefa de rastreamento. Usa a configuração padrão fornecida pelo perfil profile-defaults.

Parâmetros do formulário:
 
action - deve ser `create` 
createpath - o nome da nova tarefa

Exemplo HTML:

```
curl -v -d "createpath=myjob&action=create" -k -u admin:admin --anyauth --location \
  https://localhost:8443/engine
```

Exemplo XML:

```
curl -v -d "createpath=myjob&action=create" -k -u admin:admin --anyauth --location \
  -H "Accept: application/xml" https://localhost:8443/engine
```

### Add Job Directory

```
POST https://(heritrixhost):8443/engine [action=add]
```

Adiciona um novo diretório de tarefa à configuração do Heritrix. O diretório deve conter um arquivo de configuração cxml.

Parâmetros do formulário:
 
* ação - deve ser `add`
* addpath - adicionar o diretório de tarefa 

Exemplo HTML:

```
curl -v -d "action=add&addpath=/Users/hstern/job" -k -u admin:admin --anyauth --location https://localhost:8443/engine
```

Exemplo XML:

```
curl -v -d "action=add&addpath=/Users/hstern/job" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine
```

### Build Job Configuration

```
POST https://(heritrixhost):8443/engine/job/(jobname) [action=build]
```

Cria a configuração de tarefa para a tarefa escolhida. Lê um arquivo descritor XML e usa o Spring para construir os objetos Java necessários para executar o rastreamento. Um rastreamento tem que ser criado para poder ser executado.

Parâmetros do formulário:

* action: deve ser `build`

Exemplo HTML:

```
curl -v -d "action=build" -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob
```

Exemplo XML:

```
curl -v -d "action=build" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine/job/myjob
```

### Launch Job

Inicia uma tarefa de rastreamento. A tarefa pode ser iniciada no estado “paused” ou no estado “unpaused”. Se for iniciado no estado “unpaused”, a tarefa iniciará o rastreamento imediatamene.

Parâmetros do formulário:

* action - deve ser `launch`
* checkpointing - campo opcional: Se fornecido, o Heritrix tentará iniciar a partir de um ponto de verificação. Deve ser o nome de um ponto de verificação (por exemplo, cp00001-20180102121229) ou (a partir da versão 3.3) o valor especial `latest`, que selecionará automaticamente o ponto de verificação mais recente. Se nenhum ponto de verificação for especificado (ou se o último ponto de verificação for solicitado e não houver pontos de verificação válidos), um novo rastreamento será iniciado.

Exemplo HTML:

```
curl -v -d "action=launch" -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob
```

Exemplo XML:

```
curl -v -d "action=launch" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine/job/myjob
```

### Rescan Job Directory

```
POST https://(heritrixhost):8443/engine [action=rescan]
```

Reexamina o diretório principal de tarefa e retorna uma página HTML contendo todos os nomes de tarefas. Também retorna informações sobre as tarefas, como o local do arquivo de configuração da tarefa e o número de iniciamentos.

Parâmetros do formulário:

* action - deve ser `rescan`

Exemplo HTML:

```
curl -v -d "action=rescan" -k -u admin:admin --anyauth --location https://localhost:8443/engine
```

Exemplo XML:

```
curl -v -d "action=rescan" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine
```

### Pause Job

```
POST https://(heritrixhost):8443/engine/job/(jobname) [action=pause]
```

Pausa uma tarefa. Não haverá nenhum rastreamento enquanto a tarefa estiver pausada.

Parâmetros de formulário:

* action - deve ser `pause`

Exemplo HTML:

```
curl -v -d "action=pause" -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob
```

Exemplo XML:

```
curl -v -d "action=pause" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine/job/myjob
```

### Unpause Job

Despausa uma tarefa pausada. O rastreamento será retomado (ou começará, no caso de uma tarefa iniciada no estado "paused"), se possível.

Parâmetros do formulário:

* action - deve ser `unpause`

Exemplo HTML:

```
curl -v -d "action=unpause" -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob
```

Exemplo XML:

```
curl -v -d "action=unpause" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine/job/myjob
```

### Terminate Job

```
POST https://(heritrixhost):8443/engine/job/(jobname) [action=terminate]
```

Encerra uma tarefa em andamento.

Parâmetros do formulário:

* action - deve ser `terminate`

Exemplo HTML:

```
curl -v -d "action=terminate" -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob
```

Exemplo XML:

```
curl -v -d "action=terminate" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine/job/myjob
```

### Teardown Job

Remove o código Spring usado para executar a tarefa. Quando uma tarefa é "demolida", ela deve ser reconstruída para ser executada.

Parâmetros de formulário:

* action - deve ser `teardown`

Exemplo HTML:

```
curl -v -d "action=teardown" -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob
```

Exemplo XML:

```
curl -v -d "action=teardown" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine/job/myjob
```

### Copy Job

```
POST https://(heritrixhost):8443/engine/job/(jobname) [copyTo]
```

Copia uma configuração de tarefa existente para uma nova configuração de tarefa. Se a caixa de seleção “as profile” estiver selecionada, a configuração da tarefa será copiada como uma configuração de perfil não executável.

Parâmetros de formulário:

* copyTo - o nome da nova tarefa ou configuração de perfil
* asProfile - se deseja copiar a tarefa como uma configuração executável ou como um perfil não executável. O valor `on` significa que a tarefa será copiada como um perfil. Se omitido, a tarefa será copiada como uma configuração executável.

Exemplo HTML:

```
curl -v -d "copyTo=mycopy&asProfile=on" -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob
```

Exemplo XML:

```
curl -v -d "copyTo=mycopy&asProfile=on" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine/job/myjob
```

### Checkpoint Job

```
POST https://(heritrixhost):8443/engine/job/(jobname) [action=checkpoint]
```

Faz um ponto de verificação da tarefa escolhida. O ponto de verificação grava o estado atual de um rastreamento no sistema de arquivos para que o rastreamento possa ser recuperado caso ocorra uma falha.

Parâmetros de formulário:

* action - must be `checkpoint`

Exemplo HTML:

```
curl -v -d "action=checkpoint" -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob
```

Exemplo XML:

```
curl -v -d "action=checkpoint" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine/job/myjob
```

### Execute Script in Job

```
POST https://(heritrixhost):8443/engine/job/(jobname)/script
```

Executa um script. O script pode ser escrito como Beanshell, ECMAScript, Groovy ou AppleScript.

Parâmetros do formulário:

* engine - o mecanismo de script a ser usado: `beanshell`, `js`, `groovy` ou `AppleScriptEngine`.
* script - o código de script a ser executado

Exemplo HTML:

```
curl -v -d "engine=beanshell&script=System.out.println%28%22test%22%29%3B" -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob/script
```

Exemplo XML:

```
curl -v -d "engine=beanshell&script=System.out.println%28%22test%22%29%3B" -k -u admin:admin --anyauth --location -H "Accept: application/xml" https://localhost:8443/engine/job/myjob/script
```

### Submitting a CXML Job Configuration File

```
PUT https://(heritrixhost):8443/engine/job/(jobname)/jobdir/crawler-beans.cxml
```

Submete o conteúdo de um arquivo CXML para uma tarefa escolhida. Os arquivos CXML são os arquivos de configuração usados para controlar uma tarefa de rastreamento. Cada tarefa tem um único arquivo CXML.

Exemplo:

```
curl -v -T my-crawler-beans.cxml -k -u admin:admin --anyauth --location https://localhost:8443/engine/job/myjob/jobdir/crawler-beans.cxml
```

Códigos de status: * 200 OK - Com sucesso, o Heritrix REST API retornará um HTTP 200 sem corpo.

### Convenções e Suposições

Os seguintes parâmetros *curl* são usados ao chamar a API.

| Parâmetros *curl* | Descrição |  
| ------------- | ------------- |
| -v | Verboso. Saída de um relatório detalhado do comando curl para uma saída padrão. |
| -d | Dados. Estes são os pares de nome/valor que são enviados no corpo de um POST. |
| -k | Permite conexões a sites SSL sem certificados. |
| -u | Usuário. Permite o envio de um nome de usuário e senha para autenticar a solicitação HTTP. |
| –anyauth | Qualquer tipo de autenticação. Permite a autenticação de uma solicitação com base em qualquer tipo de método de autenticação. | 
| –location | Segue redirecionamentos HTTP. Essa opção é usada para que as chamadas de API que retornam dados (como HTML) não sejam interrompidas após o recebimento de um código de redirecionamento (como um HTTP 303). |
| -H | Define o valor de um cabeçalho HTTP. Por exemplo, “Accept: application/xml”.

Supõe-se que o leitor tenha um conhecimento prático do protocolo HTTP e da funcionalidade do Heritrix. Além disso, os exemplos assumem que o Heritrix é executado com um nome de usuário administrativo e senha de "admin".

### Sobre a implementação do REST

Representational State Transfer (REST) é uma arquitetura de software para sistemas hipermídia distribuídos, como a World Wide Web (WWW). O REST é construído sobre o conceito de representações de recursos. Recursos podem ser qualquer conceito coerente e significativo que possa ser abordado. Um URI é um exemplo de um recurso. A representação do recurso é tipicamente um documento que captura o estado atual ou pretendido do recurso. Um exemplo de representação de um recurso é uma página HTML.

O Heritrix usa o REST para expor sua funcionalidade. A implementação do REST usada pelo Heritrix é Restlet. Restlet implementa os conceitos definidos pelo REST, incluindo recursos e representações. Também fornece um container REST que processa solicitações RESTful. O container é o Noelios Restlet Engine. Para informações detalhadas sobre o Restlet, visite http://www.restlet.org/.

O Heritrix expõe sua funcionalidade REST por meio de HTTPS. O protocolo HTTPS é usado para enviar solicitações para recuperar ou modificar configurações de definições e gerenciar tarefas de rastreamento.

## Base de Conhecimento

### Rastreamento responsável

Um rastreamento responsável é um rastreamento que segue as leis e as convenções estabelecidas do rastreamento da web, afim de minimizar os custos que o rastreamento impõe aos sites coletados.

Principais práticas:

* respeitar o robots.txt, a não ser que você tenha recebido permissão explícita para fazer o contrário
* fornecer informações de contato em seu User-Agent e responder prontamente quando for contatado
* usar politeness-delay e outras configurações que afetam a frequência de acessos a um único site para garantir que a maior parte da capacidade de veiculação do site permaneça disponível para outros visitantes
* monitorar regularmente os logs de rastreamento em busca de evidências de caminhos improdutivos/intermináveis (armadilhas) e ajustar ativamente o rastreador para interromper essa atividade quando observado

### Adicionar URIs no meio do rastreamento

(Procedimentos para o Heritrix 1.14.x. Procedimentos para H2/H3 irão variar.)

**Via IUW do operador**

Há duas maneiras principais de adicionar URIs a um rastreamento que já foi iniciado a partir da interface do usuário da Web: alterando o arquivo fonte do seed (seeds.txt) e acionando uma nova verificação ou usando a opção 'import URIs' da página 'View/Edit Frontier' (disponível apenas em rastreamentos pausados).

**Edições de seeds**

O arquivo seeds é, por padrão, relido depois de qualquer alteração de configuração (mesmo pequenas alterações não relacionadas); isso pode ser alterado através da configuração "reler-seeds-on-reconfig" do escopo.

(Você pode editar arquivos seeds menores na área de texto na parte inferior de todas as outras configurações editáveis. Arquivos grandes são difíceis ou impossíveis de editar através da área de texto da Web; nesse caso, você ainda pode editar o arquivo no disco por outros métodos.)

Uma nova verificação de arquivos seeds aciona uma tentativa de reagendamento de todos os URIs contidos. No entanto, URIs que já foram agendados não serão reprogramados. Assim, somente os URIs novos ao arquivo seed serão reagendados. (E se, por algum motivo, esses novos URIs já estiverem programados por algum outro *discovery-path*, eles não serão reagendados.)

Os escopos que são definidos em termos de seeds também são, por padrão, reconstruídos a partir de seeds em alterações de configurações. (Portanto, se estiver usando esse escopo, o recomendável é não remover os seeds originais, mesmo que eles já estejam agendados - caso contrário, seu escopo pode ser redefinido para excluir os sites correspondentes.)

**Importar URIs**

Quando o rastreador é pausado, um link "View or Edit Frontier" aparecerá no console. A página correspondente oferece um formulário 'import URIs'.

Este formulário espera um caminho de arquivo local para o rastreador; o arquivo pode estar em qualquer um dos três formatos listados (um URI por linha, um crawl.log ou um log de recuperação descompactado). Se o formato fornecido incluir informações de 'hops-path' e 'via', os URIs importados compartilharão essas informações. Se a caixa 'force revisit' estiver marcada, os URIs fornecidos serão programados à força, mesmo se previamente agendados.

Nenhuma das opções de importação de URI faz com que os URIs sejam tratados como seeds ou alteram o escopo de qualquer maneira. (Por exemplo, você poderia forçar a programação de um URI fora do escopo - mas se ainda tiver o processador padrão 'Preselector' que verifica novamente o escopo, ele ainda será rejeitado do rastreamento quando for exibido.)

**Via JMX

A interface de controle remoto do JMX inclui as operações importUri e importUris no bean CrawlJob que imitam a função de importação de URIs da IUW. Um exemplo:

[Exemplo de importação de JMX] (http://tech.groups.yahoo.com/group/archive-crawler/message/2438). 

### Parâmetros de cortesia

A cortesia é alcançada, principalmente, pela implementação da "Frontier" do rastreador, que só programa URIs para processamento a uma taxa que não deve sobrecarregar indevidamente os servidores de origem. 

(As informações aqui se aplicam à implementação atual do org.archive.crawler.basic.Frontier.)

O aspecto mais básico de cortesia robot é abrir apenas uma única conexão em um servidor de cada vez. A atual Frontier do Heritrix sempre segue essa regra. (Eventualmente, quando está claro que um servidor tem a capacidade, talvez fornecida por muitas máquinas de servidores independentes, para atender a várias solicitações simultâneas robot, podemos diminuir esse comportamento.)

Outras configurações podem ser especificadas para ajustar o nível de cortesia do Heritrix com os servidores visitados. Há três informações essenciais para a decisão da Heritrix sobre quando é certo visitar um servidor novamente:

* Quando a última solicitação contra esse servidor foi emitida.
* Quanto tempo essa solicitação levou para ser concluída.
* Quanto de um intervalo (tempo desde a última solicitação emitida) ou atraso (tempo desde a última solicitação finalizada) foi especificado pelo operador.

As restrições de tempo especificadas pelo operador podem ser especificadas como qualquer combinação de:

* um múltiplo da última conclusão (o parâmetro "delay-factor");
* um intervalo mínimo desde última solicitação emitida (o parâmetro "min-interval-ms");
* um atraso mínimo desde a última conclusão da solicitação (o parâmetro "min-delay-ms");
* um atraso máximo desde a última conclusão da solicitação (o parâmetro "max-delay-ms").

Considere o comportamento pretendido de nunca emitir mais de uma solicitação em um servidor a cada 2 segundos; nunca esperar mais de 30 segundos para revisitar um servidor; mas sempre que possível, dentro dessas restrições, dar a um servidor a mesma quantidade de tempo "off" para cada momento conectado. Isso pode ser feito com a seguinte configuração:

```
min-interval-ms=2000
max-delay-ms=30000
delay-factor=1
min-delay-ms=0 (irrelevant in this case because of the 'interval')
```

### Script BeanShell para download de vídeos

Aqui está um script BeanShell que pode ser usado para encontrar links de vídeo com o Heritrix. Atualmente, o script suporta apenas o youtube. Para fazer com que o script funcione, ele deve ser adicionado na fase de extração da cadeia do processador. Esse processo foi testado com o Heritrix 2.0.2 com uma planilha que limita o max-hops no youtube para 0, permitindo obter a primeira página dos vídeos do Youtube. Também é visível no modo proxy do Wayback 1.4.0. O script também funciona com vídeos do YouTube incorporados que usam "get_video_info" para recuperar tokens de vídeo.

Quaisquer comentários ou sugestões para melhorias do script são bem vindos.

```
// This is a beanshell processor that will allow downloading of vidoes in Heritrix.
// Currently only supports Youtube video pages or embedded youtube video that use "get_video_info".
//
// The script should be added at the extraction part of the processor chain in the Heritrix profile.
//
// NOTE:  The video URIs that are extracted will automatically be added as
// candidate URIs (normally this is done by LinksScoper) to ensure that they are always downloaded.
// This can be changed if needed to instead have them added as outlinks so
// that LinkScoper does a scope check after extraction.
//
// Author:  Adam Taylor (adam dot taylor at lac-bac dot gc dot ca)

import java.io.IOException;
import java.net.URL;
import java.net.URLEncoder;
import java.net.URLDecoder;
import java.util.regex.Matcher;
import java.util.regex.Pattern;

import org.apache.commons.httpclient.URIException;
import org.archive.crawler.datamodel.CrawlURI;
import org.archive.crawler.datamodel.SchedulingConstants;
import org.archive.net.UURI;
import org.archive.net.UURIFactory;
import org.archive.io.ReplayCharSequence;
import org.archive.modules.extractor.Hop;
import org.archive.modules.extractor.Link;
import org.archive.modules.extractor.LinkContext;
import org.archive.modules.fetcher.FetchStatusCodes;

    process(CrawlURI curi) {
        if (curi.toString().matches(".*www\\.youtube\\.com/watch\\?.*")) {

            ReplayCharSequence cs = null;
            cs = curi.getRecorder().getReplayCharSequence();

            Matcher m = Pattern.compile(".*fullscreenUrl.*video_id=([^&]+).*t=([^&]+)").matcher(cs);
            m.find();
            String videoId = m.group(1);
            String token = m.group(2);
            String getVideoUrl = "http://www.youtube.com/get_video?video_id=" + videoId + "&t=" + token + "&el=detailpage&ps=";

            Link getVideoLink1 = new Link (curi.getUURI(), UURIFactory.getInstance(getVideoUrl), LinkContext.EMBED_MISC, Hop.EMBED);
            curi.getOutCandidates().add(curi.createCrawlURI(curi.getBaseURI(), getVideoLink1, SchedulingConstants.HIGH, false));
        }
        if (curi.toString().matches(".*youtube\\.com/swf/l\\.swf.*")) {
            Matcher swfParamMatcher = Pattern.compile(".*swf=([^&]+).*video_id=([^&]+).*eurl=([^&]*).*iurl=([^&]+).*").matcher(curi.toString());
            swfParamMatcher.find();

            String swfUrl = swfParamMatcher.group(1);
            String videoId = swfParamMatcher.group(2);
            String eurl = swfParamMatcher.group(3);
            String iurl = swfParamMatcher.group(4);

            String iurlDecoded = URLDecoder.decode(iurl);
            String swfUrlDecoded = URLDecoder.decode(swfUrl);
            String iurlCrossdomainUrl = "http://" + new URL(iurlDecoded).getHost() + "/crossdomain.xml";
            String youtubeCrossdomainUrl = "http://www.youtube.com/crossdomain.xml";

            String getVideoInfoUrl1 = "http://www.youtube.com/get_video_info?&video_id="
                + videoId
                + "&el=embedded&ps=default&eurl="
                + eurl;

            if (curi.toString().matches(".*hl=[^&]+.*")) {
                Matcher hlMatcher = Pattern.compile(".*hl=([^&]+).*").matcher(curi.toString());
                hlMatcher.find();
                getVideoInfoUrl1 += "&hl=" + hlMatcher.group(1);
            }

            Link getVideoInfoLink1 = new Link (curi.getUURI(), UURIFactory.getInstance(getVideoInfoUrl1), LinkContext.EMBED_MISC, Hop.EMBED);
            Link swfLink = new Link (curi.getUURI(), UURIFactory.getInstance(swfUrlDecoded), LinkContext.EMBED_MISC, Hop.EMBED);
            Link iurlLink = new Link (curi.getUURI(), UURIFactory.getInstance(iurlDecoded), LinkContext.EMBED_MISC, Hop.EMBED);
            Link iurlCrossdomainLink = new Link (curi.getUURI(), UURIFactory.getInstance(iurlCrossdomainUrl), LinkContext.EMBED_MISC, Hop.EMBED);
            Link youtubeCrossdomainLink = new Link (curi.getUURI(), UURIFactory.getInstance(youtubeCrossdomainUrl), LinkContext.EMBED_MISC, Hop.EMBED);

            curi.getOutCandidates().add(curi.createCrawlURI(curi.getBaseURI(), getVideoInfoLink1, SchedulingConstants.HIGH, false));
            curi.getOutCandidates().add(curi.createCrawlURI(curi.getBaseURI(), swfLink, SchedulingConstants.HIGH, false));
            curi.getOutCandidates().add(curi.createCrawlURI(curi.getBaseURI(), iurlLink, SchedulingConstants.HIGH, false));
            curi.getOutCandidates().add(curi.createCrawlURI(curi.getBaseURI(), iurlCrossdomainLink, SchedulingConstants.HIGH, false));
            curi.getOutCandidates().add(curi.createCrawlURI(curi.getBaseURI(), youtubeCrossdomainLink, SchedulingConstants.HIGH, false));
        }

        if (curi.toString().matches(".*youtube\\.com/get_video_info\\?.*")) {
            Matcher videoInfoMatcher = Pattern.compile(".*video_id=([^&]+).*eurl=([^&]*).*").matcher(curi.toString());
            videoInfoMatcher.find();

            String videoId = videoInfoMatcher.group(1);
            String eurl = videoInfoMatcher.group(2);

            ReplayCharSequence cs = null;
            cs = curi.getRecorder().getReplayCharSequence();

            Matcher tokenMatcher = Pattern.compile(".*token=([^&]+).*").matcher(cs);
            tokenMatcher.find();
            String videoToken = tokenMatcher.group(1);

            String getVideoUrl1 = "http://www.youtube.com/get_video?video_id="
                + videoId
                + "&t="
                + videoToken
                + "&eurl="
                + eurl
                + "&el=embedded&ps=default";

            Link getVideoLink1 = new Link (curi.getUURI(), UURIFactory.getInstance(getVideoUrl1), LinkContext.EMBED_MISC, Hop.EMBED);

            curi.getOutCandidates().add(curi.createCrawlURI(curi.getBaseURI(), getVideoLink1, SchedulingConstants.HIGH, false));
        }
    }
```

### crawl manifest

Exemplo de um arquivo *crawl manifest* do Heritrix 1.15

crawl-manifest.txt

L+ /1/crawldata/CRAWL-03-16-2010/logs/crawl.log
L+ /1/crawldata/CRAWL-03-16-2010/logs/runtime-errors.log
L+ /1/crawldata/CRAWL-03-16-2010/logs/local-errors.log
L+ /1/crawldata/CRAWL-03-16-2010/logs/uri-errors.log
L+ /1/crawldata/CRAWL-03-16-2010/logs/progress-statistics.log
C+ /0/jobs/CRAWL-03-16-2010/order.xml
C+ /0/jobs/CRAWL-03-16-2010/settings/com/youtube/settings.xml
C+ /0/jobs/CRAWL-03-16-2010/seeds.txt
R+ /1/crawldata/CRAWL-03-16-2010/hosts-report.txt
R+ /1/crawldata/CRAWL-03-16-2010/mimetype-report.txt
R+ /1/crawldata/CRAWL-03-16-2010/responsecode-report.txt
R+ /1/crawldata/CRAWL-03-16-2010/seeds-report.txt
R+ /1/crawldata/CRAWL-03-16-2010/crawl-report.txt
R+ /1/crawldata/CRAWL-03-16-2010/processors-report.txt

L+: arquivos log
C+: arquivos config
R+: arquivos de relatório

O *crawl manifest* é gerado ao clicar em "terminate" em uma tarefa na interface da Web do Heritrix. Pode levar algum tempo para criar o arquivo (20 minutos?), então seja paciente e não desligue o Heritrix muito cedo.

### Opções JVM

Notas diversas sobre as opções JVM de interesse para os operadores do Heritrix. 

Recomendado:

-XX: + UseParallelOldGC

Pode ser útil para acelerar compactações completas de GC em máquinas com vários núcleos. Ver http://java.sun.com/javase/technologies/hotspot/gc/gc_tuning_6.html#par_gc

-XX: + DoEscapeAnalysis

Disponível em 6u14, pode melhorar o desempenho; ver http://java.sun.com/javase/6/webnotes/6u14.html

-XX: + UseCompressedOops

Disponível em 6u14, pode melhorar o uso de memória/GC if using 64-bit JVM on heaps <32GB in size;; ver http://java.sun.com/javase/6/webnotes/6u14.html

Talvez:

-XX: -UseGCOverheadLimit

Desativa um OOME que não é criado quando a memória está realmente esgotada, mas quando uma quantidade excessiva de tempo é gasta no GC. We've seen this OOME at times when a requested memory-intensive report may have completed if not for this OOME (albeit in an extreme length of time).

### Budgets da fila Frontier

(Essa página descreve o comportamento do Heritrix 1.4x, mas grande parte também se aplica ao H2/H3.)

###### Visão geral

O BdbFrontier foi estendido com uma série de configurações que permitem usar um processo de "orçamentação" para alocar sua atenção às filas internas (e, portanto, aos hosts individuais que mapeiam one-to-one para as filas).

Com essas configurações, é possível alternar as filas entre dentro e fora do status 'ativo', em intervalos regulares. (Uma fila que está 'ativa' está qualificada para fornecer URIs para os threads de trabalho prontos.) A decisão de desativar uma fila baseia-se no esgotamento do 'saldo de atividade' atual em execução; esses saldos são esgotados mais rapidamente por URIs que são considerados como tendo um 'custo' mais alto. Assim, as filas com URIs mais interessantes (com menos custo) recebem mais atenção, enquanto aquelas com URIs menos interessantes (com mais custo) recebem menos atenção. Uma fila inativa vai para o final de uma fila FIFO de todas as filas inativas. As filas inativas são ativadas quando necessário para fornecer trabalho a um thread de trabalho pronto (porque todas as outras filas 'ativas' já estão em andamento ou em "snooze"). Quando ativado, o saldo de atividade em execução de uma fila é reabastecido e cada URI subsequente tentado a partir dessa fila diminui esse saldo. Quando a balança não é positiva, a fila é novamente desativada, para dar às outras filas uma oportunidade de se tornarem ativas e receberem atenção do thread. (Se não houver outras filas inativas, uma fila desativada será imediatamente reativada com uma nova execução.)

Um 'orçamento total' para toda a vida útil pode ser definido para uma fila. Quando as despesas totais em uma fila (para todos os momentos em que esteve ativa) excedem esse orçamento, a fila é 'aposentada' - colocada ao lado, permanentemente inativa. Somente a intervenção do operador - como aumentar o "orçamento total" para essa fila - pode tirar uma fila da "aposentadoria". No entanto, enquanto aposentada, a fila retém todos os seus URIs pendentes e continua a receber URIs recém-descobertos. A 'aposentadoria' é um bom lugar para manter uma fila enquanto tenta tomar decisões sobre quais, se algum, esforços serão feitos continuando a visitá-la.

Qualquer número de filas desativadas, com qualquer número de URIs enfileiradas retiradas, não conta como URIs atualmente enfileiradas, portanto, não interromperá o rastreamento quando todas as filas ativas / inativas estiverem vazias. Assim, na presença de filas desativadas, é útil usar o recurso 'pause-at-finish' para evitar o término de um rastreamento que atingiu um estado finalizado. Em vez disso, pausando o rastreamento quando todas as filas não selecionadas estiverem vazias, o operador poderá inspecionar o progresso do rastreamento, incluindo as filas desativadas, e tomar uma decisão sobre rastreamento adicional, dentro do mesmo contexto, antes de realmente terminar o rastreamento.

###### Configuração e Uso

o BdbFrontier contém novas definições:

* **hold-queues:** se definido como "true", novas filas começam inativas - permitindo comportamentos 'site-first' e similares.
* **balance-replenish-amount:** each queue activated will be given a session activity balance to draw against of this many units.
* **queue-total-budget:**  o máximo para gastar em uma fila; além desse valor, a fila será aposentada. Padrão -1: significa que não há máximo.
* **cost-policy:** opções trocáveis do custo de cada URI. O padrão ZeroCostAssignmentPolicy considera todos os URIs com custo zero e, portanto, o orçamento não tem efeito. (O saldo da atividade nunca se esgota; o orçamento total nunca é excedido.) (O padrão deve ser UnitCostAssignmentPolicy.)

Os valores de balance-replenish-amount e queue-total-budget podem ser definidos globalmente ou substituídos por domínios específicos. As filas assumem os valores de saldo e orçamento dos URIs colocados nelas. (Cuidado: se estiver usando qualquer esquema de atribuição de fila não padrão (como baseado em IP) com base em um número fixo de buckets, ou forçando determinados URIs a determinadas filas, isso pode levar a um comportamento confuso, pois cada URI variado altera os valores da fila.)

###### Ajustes durante o rastreamento

Definir o valor efetivo do balance-replenish de uma fila como zero, ou seu orçamento total como menor do que seu gasto total atual, fará com que a fila se mova rapidamente para o status de "retired" (aposentado).

Nota: a alteração de qualquer configuração faz com que todas as filas aposentadas sejam movidas para inativas, de forma que, caso os novos números de reabastecimento/orçamento permitam que a fila se torne ativa, ela se torne ativa. (Se o momento de se tornar ativa chegar, mas a fila ainda atender às condições de aposentadoria, ela será aposentada novamente antes que qualquer URI seja tentado.)

###### Relatórios

Outras características notáveis:

O relatório Frontier agora inclui informações de despesas por fila, incluindo o orçamento total (total budget), o gasto total (total expenditure), o atual orçamento restante de ativação  (current activation budget remaining) e o custo mais recente/custo médio de URIs vistos (latest-cost/average-cost of URIs seen). Uma fila cujo custo mais recente parece alto está na parte de custo mais alto de sua carga de trabalho; uma fila cujo custo médio é alto tem gasto uma preponderância do seu tempo em URIs mais caras.

###### Cenários Comuns de Uso

Para que o BdbFrontier se comporte exatamente como antes dos recursos de orçamento serem adicionados, defina 'hold-queue' como 'false' (para que todas as filas comecem ativadas, em um grande arranjo round-robin). Defina 'cost-policy' para ZeroCostAssignmentPolicy. (As configurações de 'balance-replenish-amount' e 'queue-total-budget' são irrelevantes.) Essa abordagem NÃO é recomendada; 'hold-queue' deve ser true para permitir pelo menos algum foco intenso em um conjunto menor de filas para minimizar o I/O aleatório do disco.

Para usar o comportamento clássico 'site-first', defina 'hold-queue' como 'true' e mantenha o ZeroCostAssignmentPolicy. (Ou, se estiver usando uma política de custo diferente de zero, torne o 'balance-replenish-amount' muito grande). As filas começarão inativas e somente serão ativadas (começam a ser rastreadas) quando necessário, para manter os threads de trabalho ocupados. Em geral, somente quando os sites mais antigos terminam (ou diminuem a velocidade criando espaço para novas filas), novas filas serão ativadas, alcançando o efeito desejado de trabalhar uma fila até a conclusão antes de começar outras. No entanto, existe o risco de que, se as filas iniciais conterem materiais intermináveis de baixo valor/armadilhas, outras filas mais interessantes nunca serão ativadas.

Para introduzir a rotação orçamentada das filas, de modo que, uma vez que um certo progresso for feito em uma fila, ela fique inativa para permitir que outro processo ocorra em outras filas, altere a 'cost-policy' para outra coisa. UnitCostAssignmentPolicy atribui a todos os URIs um custo de 1, portanto, cada tentativa diminui um pouco o orçamento de ativação (tamanho padrão: 3000). WagCostAssignmentPolicy inclui algumas estimativas sobre quais URIs são menos interessantes e aumenta o custo de URIs com componentes de string de consulta, e que são idênticos a seu 'via', exceto em sua string de consulta. Isso garante que esses URIs 'mais caros' sejam (dentro de uma única fila) agendados após URIs de menos custo, e as filas dominadas por URIs mais caros permaneçam menos tempo ativas. Você também pode ajustar a "balance-replenish-amount" para controlar a rapidez com que as filas alternadas do status de ativas. (Um valor degenerado de '1' não se aproximaria de nenhum orçamento, round-robinning among all queues.)

Para limitar ainda mais o esforço dedicado às filas, você pode definir um valor 'queue-total-budget'. Qualquer fila cujo gasto total exceda esse orçamento será "aposentada". Isso é mais útil em conexão com a opção 'pause-on-finish', para que você tenha a chance de examinar as filas desativadas para conteúdo interessante antes que o rastreamento realmente termine. Ao alterar as configurações no meio do rastreamento (durante uma pausa), todas as filas retiradas serão reavaliadas para ativação.

### BeanShell (Notas de Usuário)

Stack sugeriu que deveríamos adicionar essa seção à documentação, então, por enquanto, estou escrevendo minhas notas (específicas) sobre o uso do BeanShell e, mais tarde, espero poder transformar essa anotações em algo genérico (em vez de específico):

Basicamente, estou fazendo um rastreamento pseudo-inteligente onde vamos, manualmente, buscar novamente uma página de links todos os dias - esta é uma página de links para o "mais referenciados", "mais comentados", etc do youtube.

```
import org.archive.crawler.datamodel.*;
import org.archive.net.*;

String linkspage = "http://localhost:8080/docs/articles/youtubelinks.html";


process(CrawlURI curi) {
    // only go through the for loop in some cases, for efficency
    if (curi.toString().matches(".*youtube\\.com/watch\\?.*") ||
        curi.toString().equals(linkspage) ||
        (curi.getVia()!=null && curi.getVia().toString().equals(linkspage)) ||
        curi.getSchedulingDirective()==CrawlURI.HIGH ) {

        for (final Iterator iter = curi.getOutLinks().iterator();
                            iter.hasNext();) {
            CandidateURI link = (CandidateURI) iter.next();
            if (curi.toString().equals(linkspage)) {
                    link.setSchedulingDirective(CrawlURI.HIGH);
                link.setForceFetch(true);   // need to revisit these
                print("youtube.bsh: " + link.toString() + " set high and forcefetch");
            } else if (curi.getVia()!=null && curi.getVia().toString().equals(linkspage) &&
                       link.toString().matches(".*youtube\\.com/watch\\?.*")) {
                link.setSchedulingDirective(CrawlURI.HIGH);
                // possibly want to revisit?
                print("youtube.bsh: " + link.toString() + " set high");
            } else if (link.toString().matches(".*/get_video\\?.*")) {
                //  set all /get_video HIGH
                link.setSchedulingDirective(CrawlURI.HIGH);
                print("youtube.bsh: " + link.toString() + " set high");
            }
        }

    }
}
```

Vou adicionar algumas explicações e expandir essa página, eventualmente.

### Facebook and Twitter scroll down 

Configuração para capturar o material que é carregado quando você rola o mouse (scroll down) após o final da página no Facebook e no Twitter usando o novo ExtractorMultipleRegex:

**Twitter**

Configurar *extractors* no nível superior:

```
<bean id="extractorTwitterScrollOne" class="org.archive.modules.extractor.ExtractorMultipleRegex">
                <property name="enabled" value="false"/>
                <property name="uriRegex" value="^https?://(?:www\.)?twitter\.com/([^/]+)/?(?:\?.*)?$"/>
                <property name="contentRegexes">
                        <map>
                                <entry key="maxId" value="data-max-id=&quot;(\d+)&quot;"/>
                        </map>
                </property>
                <property name="template">
                        <value>/i/profiles/show/${uriRegex[1]}/timeline/with_replies?include_available_features=1&amp;include_entities=1&amp;max_id=${maxId[1]}</value>
                </property>
        </bean>
        <bean id="extractorTwitterScrollFurther" class="org.archive.modules.extractor.ExtractorMultipleRegex">
                <property name="enabled" value="false"/>
                <property name="uriRegex" value="^https?://(?:www\.)?twitter\.com/i/profiles/show/([^/]+)/timeline/with_replies\?include_available_features=1&amp;include_entities=1&amp;max_id=\d+$"/>
                <property name="contentRegexes">
                        <map>
                                <entry key="maxId" value="&quot;max_id&quot;:&quot;(\d+)&quot;"/>
                        </map>
                </property>
                <property name="template">
                        <value>/i/profiles/show/${uriRegex[1]}/timeline/with_replies?include_available_features=1&amp;include_entities=1&amp;max_id=${maxId[1]}</value>
                </property>
        </bean>
```

Inserir na fetch chain:

```
 <ref bean="extractorHtml"/>
                                <ref bean="extractorCss"/>
                                <ref bean="extractorJs"/>
+                                <ref bean="extractorTwitterScrollOne"/>
+                                <ref bean="extractorTwitterScrollFurther"/> 
                        </list>
                </property
```

Use planilhas para permitir URLs relevantes:

```
<bean id="twitterScrollOne" class="org.archive.spring.Sheet">
                <property name="map">
                        <map>
                                <entry key="extractorTwitterScrollOne.enabled" value="true"/>
                        </map>
                </property>
        </bean>
        <bean class="org.archive.crawler.spring.SurtPrefixesSheetAssociation">
                <property name="surtPrefixes">
                        <list>
                                <value>http://(com,twitter,)/</value>
                                <value>http://(com,twitter,www,)/</value>
                        </list>
                </property>
                <property name="targetSheetNames">
                        <list>
                                <value>twitterScrollOne</value>
                        </list>
                </property>
        </bean>
        <bean id="twitterScrollFurther" class="org.archive.spring.Sheet">
                <property name="map">
                        <map>
                                <entry key="extractorTwitterScrollFurther.enabled" value="true"/>
                                <entry key="extractorTwitterScrollOne.enabled" value="false"/>
                        </map>
                </property>
        </bean>
        <bean class="org.archive.crawler.spring.SurtPrefixesSheetAssociation">
                <property name="surtPrefixes">
                        <list>
                                <value>http://(com,twitter,)/i/profiles/show/</value>
                                <value>http://(com,twitter,www,)/i/profiles/show/</value>
                        </list>
                </property>
                <property name="targetSheetNames">
                        <list>
                                <value>twitterScrollFurther</value>
                        </list>
                </property>
        </bean>
```

**Facebook**

Configurar *extractor* no nível superior:

```
<bean id="extractorFacebookScroll" class="org.archive.modules.extractor.ExtractorMultipleRegex">
                <property name="enabled" value="false"/>
                <property name="uriRegex" value="^https?://(?:www\.)?facebook\.com/[^/?]+$"/>
                <property name="contentRegexes">
                        <map>
                                <entry key="jsonBlob" value="\[&quot;TimelineContentLoader&quot;,&quot;registerTimePeriod&quot;,[^,]+,[^,]+,[^,]+,\{(&quot;profile_id&quot;:[^}]+)\},false,null,(\d+),"/>
                                <entry key="ajaxpipeToken" value="&quot;ajaxpipe_token&quot;:&quot;([^&quot;]+)&quot;"/>
                                <entry key="timeCutoff" value="&quot;setTimeCutoff&quot;,[^,]*,\[(\d+)\]\]"/>
                        </map>
                </property>
                <property name="template">
                        <value>/ajax/pagelet/generic.php/ProfileTimelineSectionPagelet?ajaxpipe=1&amp;ajaxpipe_token=${ajaxpipeToken[1]}&amp;no_script_path=1&amp;data=${java.net.URLEncoder.encode('{' + jsonBlob[1] , 'UTF-8')},&quot;time_cutoff&quot;%3A${java.net.URLEncoder.encode(timeCutoff[1] , 'UTF-8')},&quot;force_no_friend_activity&quot;%3Afalse%7D&amp;__user=0&amp;__a=1&amp;__adt=${jsonBlob[2]}</value>
                </property>
        </bean>
```

Inserir na fetch chain:

```
 <ref bean="extractorCss"/>
                                <ref bean="extractorJs"/>
                                <ref bean="extractorTwitterScrollOne"/>
                                <ref bean="extractorTwitterScrollFurther"/>
+                                <ref bean="extractorFacebookScroll"/>
                        </list>
                </property>
```

Use planilhas para permitir URLs relevantes:

```
<bean id="enableFacebookScroll" class="org.archive.spring.Sheet">
                <property name="map">
                        <map>
                                <entry key="extractorFacebookScroll.enabled" value="true"/>
                        </map>
                </property>
        </bean>
        <bean class="org.archive.crawler.spring.SurtPrefixesSheetAssociation">
                <property name="surtPrefixes">
                        <list>
                                <value>http://(com,facebook,</value>
                        </list>
                </property>
                <property name="targetSheetNames">
                        <list>
                                <value>enableFacebookScroll</value>
                        </list>
                </property>
        </bean>
```

### Deduping (redução de duplicação)

A partir do release 1.12.0, vários Processadores podem cooperar para transportar o histórico de conteúdo do URI entre os rastreamentos (consulte JavaDocs do pacote org.archive.crawler.processor.recrawl). Isso reduz a quantidade de material duplicado baixado ou armazenado em rastreamentos posteriores.

### Configuração de desduplicação H1

O Heritrix 1.x não suporta a execução de um mesmo rastreamento mais de uma vez, portanto, um rastreamento precisará ser configurado para *armazenar* dados de redução de duplicação, e o outro rastreamento precisará ser configurado para *carregar* dados de redução de duplicação. Exemplo (trecho do teste para o HER-1627):

**configure persist STORE crawl**

* adicione os processadores FetchHistory e PersistLog depois de `FetchHttp`
`org.archive.crawler.processor.recrawl.FetchHistoryProcessor`
`org.archive.crawler.processor.recrawl.PersistLogProcessor`

**configure persist LOAD crawl**

* *depois* de PreconditionEnforcer, *antes* de FetchDNS
`org.archive.crawler.processor.recrawl.PersistLoadProcessor`
* *depois* de FetchHTTP
`org.archive.crawler.processor.recrawl.FetchHistoryProcessor`
* preload-source:
`${HERITRIX_HOME}/jobs/${JOB}/logs/persistlog.txtser.gz`

### Configuração de desduplicação H3

O Heritrix 3.x permite executar o mesmo rastreamento repetidamente, mas exige uma configuração diferente para a execução de rastreamentos que *armazenam* dados de deduplicação e a execução de rastreamento que *carregam* dados deduplicação, conforme descrito em Processadores de Redução de Duplicação. O mesmo modelo é seguido para o H1, exceto pelo uso do beans CXML do rastreador do Spring-world (`crawler-beans.cxml`) para configuração.

### Forçar URIs embed especulativos em uma única fila

O uso da configuração a seguir enviará *embeds* especulativos (hoppath 'X' encontrado por meio da extração do javascript) em uma única fila separada.

Isso tornou-se necessário devido ao extrator de javascript que enfileirava um grande número de URIs defeituosos/inexistentes.

```
<!-- extracted uris go to their own queue -->
<bean class='org.archive.crawler.spring.DecideRuledSheetAssociation'>
  <property name='rules'>
    <bean>
     <property name="regex" value=".*X$" />
    </bean>
  </property>
  <property name='targetSheetNames'>
   <list>
    <value>speculativeSingleQueue</value>
    <value>speculativeSingleQueueBalance</value>
   </list>
  </property>
</bean> 

<bean id='speculativeSingleQueue' class='org.archive.spring.Sheet'>
  <property name='map'>
   <map>
    <entry key='queueAssignmentPolicy.forceQueueAssignment' value='speculative-queue' />
   </map>
  </property>
 </bean>
 <bean id='speculativeSingleQueueBalance' class='org.archive.spring.Sheet'>
   <property name='map'>
     <map>
       <entry key='frontier.balanceReplenishAmount' value='2000000000' />
     </map>
   </property>
</bean>
```

### Scripts úteis do Heritrix3

Scripts úteis do Heritrix 3 H3 para executar no console de script.

* obter uma lista de seeds do arquivo de seeds no disco
* imprimir variáveis disponíveis no contexto de script
* printProps (obj) e utilização do appCtx.getData ()
* mudando uma regra regex (decide rule)
* adicionar um surt de exclusão
* checanco as regras de decisão
* checando os metadados
* adicionar uma planilha para forçar várias filas ao estado 'retired'
* criar uma planilha para forçar a atribuição de filas, e associar dois surts a ela
* adicionar uma planilha de associação de regras de decisão
* aplicar planilha (ignoreRobots) a uma lista de URIs como strings, retirada do arquivo seeds.txt
* reconsiderRetiredQueues
* executar um comando arbitrário na máquina (TENHA CUIDADO)
* listar urls pendentes
* Informações sobre GC Garbage Collector Collection 
* Recuperando histórico para URI 
* despejar surts
* Adicionar cookies a um rastreamento em andamento
* Deletar urls correspondentes ao regex da Frontier
* Forçar um site a rastrear por meio de um proxy
* Forçar URLs dormentes (snoozed) a "acordar"
* Adicionar uma DecideRule ao escopo, rejeitando o secundo hop especulativo
* Desativar as filas que correspondem ao Regex
* Determinar qual rastreador de várias máquinas é responsável por determinado URI

**obtenção de uma lista de seeds do arquivo de seeds no disco**

```
//groovy
//this is not the original list of seeds. it's just the contents of the seeds file, filtered.
appCtx.getBean("seeds").textSource.file.readLines().findAll{l -> l =~ /^http/}.unique().each{seedStr ->
 rawOut.println(seedStr)
}
```

**impressão de variáveis disponíveis no contexto de script**

```
//groovy
this.binding.getVariables().each{ rawOut.println("${it.key}=\n ${it.value}\n") }
```

**printProps (obj) e utilização do appCtx.getData ()**

O comando bash `ls` é para o diretório de trabalho o que esse método é para um objeto java. Ele usa getProperties fornecido pela groovy.

Colocar printProps em appCtx.getData () significa que a definição printProps não precisa ser incluída posteriormente, o que ajuda a manter os scripts curtos e gerenciáveis. appCtx.getData () é um java.util.Map. Mais informações sobre o tópico fornecidas pelo Groovy (detalhadas) e pelo (IBM (concisas)) [https://www.ibm.com/developerworks/java/tutorials/j-groovy/j-groovy.html].

```
//Groovy
appCtxData = appCtx.getData()
appCtxData.printProps = { rawOut, obj ->
  rawOut.println "#properties"
  // getProperties is a groovy introspective shortcut. it returns a map
  obj.properties.each{ prop ->
    // prop is a Map.Entry
    rawOut.println "\n"+ prop
    try{ // some things don't like you to get their class. ignore those.
      rawOut.println "TYPE: "+ prop.value.class.name
    }catch(Exception e){}
  }
  rawOut.println "\n\n#methods"
  try {
  obj.class.methods.each{ method ->
    rawOut.println "\n${method.name} ${method.parameterTypes}: ${method.returnType}"
  } }catch(Exception e){}
}

// above this line need not be included in later script console sessions
def printProps(x) { appCtx.getData().printProps(rawOut, x) }

// example: see what can be accessed on the frontier
printProps(job.crawlController.frontier)
```

**alterando uma regra regex (regra de decisão)**

É recomendável pausar o rastreamento ao modificar as coleções das quais ele depende.

```
//Groovy
pat = ~/your-regex-here/
dec = org.archive.modules.deciderules.DecideResult.REJECT
regexRuleObj = appCtx.getBean("scope").rules.find{ it.class == org.archive.modules.deciderules.MatchesListRegexDecideRule }
regexRuleObj.decision = dec
rawOut.println("decision: "+ regexRuleObj.decision)
regexRuleObj.regexList.add(pat)
rawOut.println("regexList: "+ regexRuleObj.regexList)
```

**adicionando surt de exclusão**

```
//Groovy
rule = appCtx.getBean("scope").rules.find{ rule ->
  rule.class == org.archive.modules.deciderules.surt.SurtPrefixedDecideRule &&
  rule.decision == org.archive.modules.deciderules.DecideResult.REJECT
}

theSurt = "http://(org,northcountrygazette," // ncg is cranky. avoid.
rawOut.print( "result of adding theSurt: ")
rawOut.println( rule.surtPrefixes.considerAsAddDirective(theSurt) )
rawOut.println()

//dump the list of surts excluded to check results
rule.surtPrefixes.each{ rawOut.println(it) }
```

**Adicionando surt de exclusão para uma lista de URIs**

```
rule = appCtx.getBean("scope").rules.find{ rule ->
  rule.class == org.archive.modules.deciderules.surt.SurtPrefixedDecideRule &&
  rule.decision == org.archive.modules.deciderules.DecideResult.REJECT
}

def stringList = [ "www.example.com", "example.net", "foo.org" ]


stringList.each() { rawOut.println( rule.surtPrefixes.considerAsAddDirective("${it}")) }
rule.surtPrefixes.each{ rawOut.println(it) }
```

**checando as regras de decisão**

```
//Groovy
def printProps(obj){
  // getProperties is a groovy introspective shortcut. it returns a map
  obj.properties.each{ prop ->
    // prop is a Map.Entry
    rawOut.println "\n"+ prop
    try{ // some things don't like you to get their class. ignore those.
      rawOut.println "TYPE: "+ prop.value.class.name
    }catch(Exception e){}
  }
}
 
// loop through the rules
counter = 0
appCtx.getBean("scope").rules.each { rule ->
  rawOut.println("\n###############${counter++}\n")
  printProps( rule )
}
```

**checando os metadados**

```
appCtx.getBean("metadata").keyedProperties.each{ k, v ->
  rawOut.println( k)
  rawOut.println(" $v\n")
}
```

**adicionar uma planilha para forçar várias filas ao estado 'retired'**

```
// force-retire all .org queues
mgr = appCtx.getBean("sheetOverlaysManager")
mgr.putSheetOverlay("forceRetire","disposition.forceRetire",true)
mgr.addSurtAssociation("http://(org,","forceRetire")
```

**criar uma planilha para forçar a atribuição de filas, e associar dois surts a ela**

```
mgr = appCtx.getBean("sheetOverlaysManager");
newSheetName = "urbanOrgAndTaxpolicycenterOrgSingleQueue"
mgr.putSheetOverlay(newSheetName, "queueAssignmentPolicy.forceQueueAssignment", "urbanorg_and_taxpolicycenterorg");
mgr.addSurtAssociation("http://(org,urban,", newSheetName);
mgr.addSurtAssociation("http://(org,taxpolicycenter,", newSheetName);

//check your results
mgr.sheetNamesBySurt.each{ rawOut.println(it) }
rawOut.println(mgr.sheetNamesBySurt.size())
```

**adicionar uma planilha de associação de regras de decisão**

forçar atribuição de fila baseada no hop path

```
mgr = appCtx.getBean("sheetOverlaysManager");
newSheetName = "speculativeSingleQueue"
dr = new org.archive.crawler.spring.DecideRuledSheetAssociation()
hpreg = new org.archive.modules.deciderules.HopsPathMatchesRegexDecideRule()
hpreg.setRegex(~/.*X$/)
dr.setRules(hpreg)
dr.setTargetSheetNames([newSheetName])
dr.setBeanName("forceSpeculativeQueueAssociation")
mgr.putSheetOverlay(newSheetName, "queueAssignmentPolicy.forceQueueAssignment", "speculative-queue");
mgr.addRuleAssociation(dr)
```

xml parecido:

```
 <bean class='org.archive.crawler.spring.DecideRuledSheetAssociation'>
  <property name='rules'>
    <bean class="org.archive.modules.deciderules.HopsPathMatchesRegexDecideRule">
     <property name="regex" value=".*X$" />
    </bean>
  </property>
  <property name='targetSheetNames'>
   <list>
    <value>speculativeSingleQueue</value>
   </list>
  </property>
 </bean>
 <bean id='speculativeSingleQueue' class='org.archive.spring.Sheet'>
  <property name='map'>
   <map>
    <entry key='queueAssignmentPolicy.forceQueueAssignment' value='speculative-queue' />
   </map>
  </property>
 </bean>
 ```` 
 
**aplicar planilha (ignoreRobots) a uma lista de URIs como strings, retirada do arquivo seeds.txt**

```
//Groovy
sheetName = "ignoreRobots"
mgr = appCtx.getBean("sheetOverlaysManager")
 
//test that you got the name right
if ( ! mgr.sheetsByName.containsKey( sheetName ) ) {
 rawOut.println( "sheet $sheetName does not exist. your choices are:" )
 mgr.sheetsByName.keySet().each{ rawOut.println(it) }
 return;
}
 
//look for lines in the seeds.txt file starting with http
appCtx.getBean("seeds").textSource.file.readLines().findAll{ l ->
 l =~ /^http/
}.collect{ uriStr ->
 //turn the domain into a surt and remove www
 org.archive.util.SurtPrefixSet.prefixFromPlainForceHttp("http://"+ new org.apache.commons.httpclient.URI(uriStr).host).replaceAll( /www,$/, "" )
}.unique().each{ seedSurt ->
 rawOut.println("associating $seedSurt")
 try{
  //ignore robots on the domain
  mgr.addSurtAssociation( seedSurt, sheetName)
 } catch (Exception e) {
  println("caught $e on $seedSurt")
 }
}
//review the change
mgr.sheetNamesBySurt.each{ k, v -> rawOut.println("$k\n $v\n") }
```

**reconsiderRetiredQueues**

se definições relacionadas a Frontier tiverem mudado (por exemplo, budget), isso pode retirar as filas do estado "retired"

```
appCtx.getBean("frontier").reconsiderRetiredQueues()
```

**executar um comando arbitrário na máquina (TENHA CUIDADO)**

```
command = "ls";
proc = Runtime.getRuntime().exec(command);

stdout = new BufferedReader(new InputStreamReader(proc.getInputStream()));
while ((line = stdout.readLine()) != null) {
    rawOut.println("stdout: " + line);
}

stderr = new BufferedReader(new InputStreamReader(proc.getErrorStream()));
while ((line = stderr.readLine()) != null) {
    rawOut.println("stderr: " + line);
}
```

There are groovier ways to do it, starting with the basic

```
rawOut.println( "ls".execute().text )
```

**listar urls pendentes**

```
// groovy
// see org.archive.crawler.frontier.BdbMultipleWorkQueues.forAllPendingDo()

import com.sleepycat.je.DatabaseEntry;
import com.sleepycat.je.OperationStatus;

MAX_URLS_TO_LIST = 1000

pendingUris = job.crawlController.frontier.pendingUris

rawOut.println "(this seems to be more of a ceiling) pendingUris.pendingUrisDB.count()=" + pendingUris.pendingUrisDB.count()
rawOut.println()

cursor = pendingUris.pendingUrisDB.openCursor(null, null);
key = new DatabaseEntry();
value = new DatabaseEntry();
count = 0;

while (cursor.getNext(key, value, null) == OperationStatus.SUCCESS && count < MAX_URLS_TO_LIST) {
    if (value.getData().length == 0) {
        continue;
    }
    curi = pendingUris.crawlUriBinding.entryToObject(value);
    rawOut.println curi
    count++
}
cursor.close(); 

rawOut.println()
rawOut.println count + " pending urls listed"
```

**Informações sobre GC Garbage Collector Collection**

```
// beanshell
for (gcMxBean: java.lang.management.ManagementFactory.getGarbageCollectorMXBeans()) {
    rawOut.println(gcMxBean.getName() + " pools=" + java.util.Arrays.toString(gcMxBean.getMemoryPoolNames()) + " count=" + gcMxBean.getCollectionCount() + " time=" + gcMxBean.getCollectionTime());
}
```

**Recuperar histórico para URI**

```
//Groovy
uri="http://example.com/"
loadProcessor = appCtx.getBean("persistLoadProcessor") //this name depends on config
key = loadProcessor.persistKeyFor(uri)
history = loadProcessor.store.get(key)
history.get(org.archive.modules.recrawl.RecrawlAttributeConstants.A_FETCH_HISTORY).each{historyStr ->
    rawOut.println(historyStr)
}
```

**Despejar surts**

```
// beanshell

// permit access to protected variable surtPrefixes
setAccessibility(true);

// assumes SurtPrefixedDecideRule is second rule in scope; adjust number for nth rule
rawOut.print(appCtx.getBean("scope").rules.get(1).surtPrefixes);
```

**Adicionar cookies a um rastreamento em andamento**

```
//Groovy
cookies = appCtx.getBean("cookieStorage").getCookiesMap();
epochSeconds = Long.parseLong("2094586238") //Expiration in 2036
expirationDate = (epochSeconds >= 0 ? new Date(epochSeconds * 1000) : null);
 
//Domain (compare with actual cookie, may be partial domain), Name, Value, Path, Expiration, isSecure (cookie only over https)
cookie = new org.apache.commons.httpclient.Cookie("www.trivediforcongress.com", "splash","1","/",expirationDate,false); 
//Indicates whether the cookie had a path specified in a path attribute of the Set-Cookie header
cookie.setDomainAttributeSpecified(true);

rawOut.println(cookie.getSortKey());
rawOut.println(cookie);
cookies.put(cookie.getSortKey(), cookie);
```

**Deletar urls correspondentes ao regex da Frontier**

```
// groovy
count = job.crawlController.frontier.deleteURIs(".*", "^http://de.wikipedia.org/.*")
rawOut.println count + " uris deleted from frontier"
```

**Forçar um site a rastrear por meio de um proxy**

```
//groovy
mgr = appCtx.getBean("sheetOverlaysManager");
newSheetName = "proxyFetch"
mgr.putSheetOverlay(newSheetName, "fetchHttp.httpProxyHost", "my.proxy.host"); //hostname or ip
mgr.putSheetOverlay(newSheetName, "fetchHttp.httpProxyPort", "8443"); //port

mgr.addSurtAssociation("http://(com,timgriffinforcongress,", newSheetName);

//check your results
mgr.sheetNamesBySurt.each{ rawOut.println(it) }
rawOut.println(mgr.sheetNamesBySurt.size())
```

**Forçar URLs dormentes (snoozed) a "acordar"**

```
//Groovy
 
countBefore = job.crawlController.frontier.getSnoozedCount()


job.crawlController.frontier.forceWakeQueues()
countAfter = job.crawlController.frontier.getSnoozedCount()

rawOut.println("Snoozed queues.")
rawOut.println(" - Before: " + countBefore)
rawOut.println(" - After: " + countAfter)
```

**Adicionar uma DecideRule ao escopo, rejeitando o secundo hop especulativo**

Pause o rastreamento antes de fazer isso.

```
//Groovy

scope = appCtx.getBean("scope")
hpm = new org.archive.modules.deciderules.HopsPathMatchesRegexDecideRule()
hpm.regex = ~/.*X.*X.*/
hpm.decision = org.archive.modules.deciderules.DecideResult.REJECT
rawOut.println scope.rules.add(hpm)
scope.rules.each{rawOut.println it}
```

**Desativar as filas que correspondem ao Regex**

```
//Groovy

dr = new org.archive.crawler.spring.DecideRuledSheetAssociation()
matchreg = new org.archive.modules.deciderules.MatchesRegexDecideRule()
matchreg.setRegex(~/^https?:\/\/([^\/])*((discount)|(cheap))[^\/]*.*$/)
dr.setRules(matchreg)
dr.setBeanName("cheap-discount-domain-small-budget")
dr.setTargetSheetNames(["smallBudget"])

//smallBudget bean exists by default
```

**Determinar qual rastreador de várias máquinas é responsável por determinado URI**

Interprete a saída como um índice no mapa de desvio.

```
//Groovy
import org.archive.modules.CrawlURI
import org.archive.net.UURIFactory
uris=['http://foo.com','http://bar.org']
mapper = appCtx.getBean("hashCrawlMapper")
uris.each{ uri -> rawOut.println(uri + ":" + mapper.map(new CrawlURI(UURIFactory.getInstance(uri)))) }
``` 

### Como adicionar URLs em massa ao rastreador

Há várias maneiras de adicionar um grande número de URLs a um rastreamento, seja no seu início ou em qualquer outro momento antes de seu término.

Na prática, a primeira opção, adicionar como seeds, tem funcionado de forma aceitável com centenas de milhares de URLs - se o tratamento desses URLs como seeds for aceitável. A segunda opção, importar via interface do usuário da web ou JMX, funcionou bem para grupos de tamanhos variados, durante qualquer momento do rastreamento, e permite definir os valores *hops-path* e *via* de URLs que não são seeds. A terceira opção, importar um log de recuperação no começo do rastreamento, funcionou bem com dezenas de milhões de URLs e, diferentemente das outras opções, permite que o rastreamento comece enquanto os URLs ainda estão sendo importados (pausa menor).

### Adicionar como seeds

Os URLs podem ser inseridos (ou fornecidos em um arquivo de seeds) no começo do rastreamento. Esses URLs podem ser tratados de forma especial por algumas opções de escopo - automaticamente consideradas no escopo ou usadas para determinar outras classes de URLs relacionadas que também devem ser regidas no escopo.

A lista de seeds pode ser editada por meio das configurações da interface do usuário da Web durante um rastreamento, embora seja recomendável que o rastreamento seja pausado antes dessa edição. Depois de concluir a edição e retomar o rastreamento, a lista de seeds será verificada novamente (a menos que uma configuração de escopo tenha sido alterada para desabilitar essa verificação). Todos os URLs serão apresentados para agendamento na frontier - mas qualquer URL previamente agendado (inserido como um seed ou descoberto durante o rastreamento) será ignorado e considerao já incluído. Ou seja, apenas URLs novos, não descobertos e adicionados à lista de propagação serão agendados.

Normalmente, a área de entrada de texto da interface do usuário da web não é mais utilizável após algumas milhares de entradas. Quando a lista de seeds exceder um determinado tamanho, a área de entrada de texto da web será desativada. Listas de seeds maiores - centenas de milhares ou milhões - podem ser fornecidas por um arquivo de seeds (geralmente chamado seeds.txt). As edições feitas diretamente neste arquivo não serão detectadas por uma simples pausa/retomada do rastreador - mas a edição de qualquer outra configuração causará uma nova verificação (novamente, a menos que essa opção tenha sido desabilitada no escopo). Cada vez que uma lista muito grande de seeds é digitalizada, pode ocorrer uma pausa mais longa e o rastreamento só começa depois que todos os seeds forem importados.

### Importar via interface do usuário da web ou JMX

Quando um rastreamento é pausado, a opção "View or Edit Frontier URIs" aparecerá na área de informações de tarefa do console. Nessa página, um arquivo contendo URIs pode ser importado. O arquivo pode ter um URI por linha, estar no mesmo formato de um craw.log ou no formato de um diário de recuperação (descompactado). (Nesses casos, o hops-path e o via-URL também serão retidos). A caixa de seleção "force fetch" forçará os URIs do arquivo a serem agendados, mesmo que eles tenham sido previamente agendados/buscados.

Opções semelhantes existem através da interface JMX no Heritrix 1. No Heritrix 3, o diretório "action" é uma boa maneira.

### Começar o rastreamento com um log de recuperação 

Especificar um log de recuperação no começo do rastreamento, de acordo com o manual do usuário, fará com que um processo de duas etapas ocorra com o log, que pode ser usado para adicionar um grande número de URIs ao rastreamento.

Na primeira etapa, todos os URLs nas linhas de log de recuperação que começam com 'Fs' são marcados como tendo sido agendados - mas não foram agendados. Isso impede que esses URIs sejam agendados posteriormente pelo rastreamento. Na segunda etapa, todos os URLs nas linhas de log de recuperação que começam com 'F +' são apresentados para agendamento. Somente aqueles que ainda não estiverem marcados como agendados serão agendados.

Assim, editando ou compondo um arquivo de formato de log de recuperação, o rastreamento pode ser pré-configurado para incluir um grande número de URLs ou considerá-los já concluídos e, portanto, não programáveis.

Esse processo funciona de forma aceitável com dezenas de milhões de URLs, e o rastreamento padrão começa antes que todas as linhas sejam processadas na segunda etapa.

### MatchesListRegexDecideRule vs NotMatchesListRegexDecideRule

*class* NotMatchesListRegexDecideRule e MatchesListRegexDecideRule são usados pelo DecideRuleSequence para encontrar candidatos para rastreamento. Podem ser um pouco confusos no começo, mas descobri que:

*class* NotMatchesListRegexDecideRule herda MatchesListRegexDecideRule e retorna o oposto (em termos de avaliação de regex):

protected boolean evaluate(CrawlURI object) {
*       return ! super.evaluate(object);*
}

Novamente, o oposto em termos de "avaliação regex" e não de "decision (ACCEPT, REJECT, NONE)". *Decision* para ambos, por padrão, é "ACCEPT", então o *decision* deve ser definido manualmente.

Então:

se você quiser aceitar uma correspondência de padrão positiva: adicione

MatchesListRegexDecideRule with <property name="decision" value="ACCEPT"/>

se você quiser rejeitar uma correspondência padrão positiva: adicione

MatchesListRegexDecideRule with <property name="decision" value="REJECT"/>

e:

se você quiser aceitar uma correspondência de padrão negativa: adicione

NotMatchesListRegexDecideRule with <property name="decision" value="ACCEPT"/>

se você quiser rejeitar uma correspondência de padrão negativo: adicione

NotMatchesListRegexDecideRule with <property name="decision" value="REJECT"/>


Sinceramente, considero o comando NotMatchesListRegexDecideRule desnecessário, já que o MatchesListRegexDecideRule pode ser usado para quase tudo.

### WARC (Web ARChive)

### Formato de arquivo WARC

O formato de arquivo WARC é sucessor do formato ARC. (sO formato ARC tem sido usado, por muitos anos, para armazenar capturas da Web do Internet Archive.) Pequenos exemplos de arquivos ARC e WARC (v0.17) de um rastreamento raso (~ 2 hops) do site www.archive.org, feito pelo Heritrix, estão anexados nesta página wiki. É fácil criar arquivos ARC e WARC maiores e mais representativos usando qualquer versão recente do Heritrix.

Download All

Comparado ao ARC, observe que o WARC adiciona:

* Uma quantidade expansível de informações de cabeçalho por registro
* Novos tipos de registro opcionais para dados/metadados, além de respostas HTTP (que era só o que o arquivo ARC gravava)

### Padrão ISO

Em maio de 2009, um padrão WARC que havia sido proposto foi aprovado como padrão **ISO 28500:2009**, e as versões mais recentes dos arquivos WARC de saída do Heritrix estão em conformidade com este padrão, conforme descrito em http://bibnum.bnf.fr/WARC/ (último rascunho feito em novembro de 2008).

### Heritrix renomeia o arquivo crawl.log ao capturar a tela de um rastreamento

Ao tirar uma captura de tela de um rastreamento, o Heritrix renomeará o arquivo crawl.log. Por exemplo, o arquivo padrão 'crawl.log' será denominado crawl.logXXX, em que XXX é uma combinação de um ID sequencial e um registro de data e hora. Isso pode causar problemas se você estiver, por exemplo, usando um script para monitorar entradas de log referenciando, explicitamente, 'crawl.log'.

### YouTube

Para capturar vídeos do YouTube, adicione as regras de escopo:

```
+http://(com,youtube,c,
+http://(com,youtube-nocookie,c,
+http://(com,ytimg,
+http://(be,youtu,
+http://(com,youtube,www,)/crossdomain.xml
```

e inclua o seguinte em order.xml.

No final da seção *extractors":

```
 <newObject name="ExtractorImpliedURI-YoutubeEmbedded" class="org.archive.crawler.extractor.ExtractorImpliedURI">
        <boolean name="enabled">true</boolean>
        <newObject name="ExtractorImpliedURI-YoutubeEmbedded#decide-rules" class="org.archive.crawler.deciderules.DecideRuleSequence">
          <map name="rules">
          </map>
        </newObject>
        <string name="trigger-regexp">^(http://(?:www.)?youtube.com)/v/([a-zA-Z0-9_-]+).*$</string>
        <string name="build-pattern">$1/watch?v=$2</string>
        <boolean name="remove-trigger-uris">false</boolean>
      </newObject>
      <newObject name="ExtractorImpliedURI-YoutubeWatchPage" class="org.archive.crawler.extractor.ExtractorImpliedURI">
        <boolean name="enabled">false</boolean>
        <newObject name="ExtractorImpliedURI-YoutubeWatchPage#decide-rules" class="org.archive.crawler.deciderules.DecideRuleSequence">
          <map name="rules">
          </map>
        </newObject>
        <string name="trigger-regexp"/>
        <string name="build-pattern"/>
        <boolean name="remove-trigger-uris">false</boolean>
      </newObject>
 ```
 
Em settings/com/youtube/settings.xml:
 
 ```
   <object name="ExtractorImpliedURI-YoutubeWatchPage">
    <boolean name="enabled">true</boolean>
    <string name="trigger-regexp">^(http://[^/]*\.c\.youtube.com)/[^?]+\?(.*)$</string>
    <string name="build-pattern">$1/videoplayback?$2</string>
  </object>
  ```
  
 Em settings/com/youtube/c/settings.xml:
  
  ```
  <object name="robots-honoring-policy">
    <string name="type">ignore</string>
  </object>
  ```
  
Se houver alguma dúvida sobre o material documentado aqui, por favor, não hesite em entrar em contato com a lista em archive-crawler em yahoogroups.com.

## Problemas relatados

### Problemas de extração de JavaScript não resolvidos

Solicitado por https://webarchive.jira.com/browse/LOC-345

O Heritrix (ExtractorJS) tem dificuldade em encontrar os links que não são seqüências de caracteres codificadas em JavaScript.

Por outro lado, extrai strings que o código javascript não usa como URIs, geralmente resultando em 404s notados pelos webmasters.

Para tentar definir melhor o problema, criamos esse local para colocar exemplos de problemas de extração incorreta de javascript.

https://webarchive.jira.com/browse/LOC-345

https://webarchive.jira.com/browse/HER-1300

https://webarchive.jira.com/browse/HER-1522

https://webarchive.jira.com/browse/HER-1523

Possível solução: pesquise por chamadas location.replace e métodos semelhantes de acessar um URL. Se os parâmetros para tal função envolverem variáveis, tente resolvê-los.

## Leituras

**Leituras essenciais**

* Haydon, A; Najork, M. Mercator: A Scalable, Extensible Web Crawler (wayback (http://web.archive.org/web/\*/http://research.compaq.com/SRC/mercator/papers/www/paper.html)), 1999
* Haydon, A; Najork, M. High-performance web crawling, 2001
* Kimpton, Stata, Mohr. Internet Archive Crawler Requirements Analysis for library consortium, 2003
* Lee, H; Leonard, D; Wang, X; Loguinov, D. (IRLbot: Scaling to 6 Billion Pages and Beyond)[http://irl.cs.tamu.edu/people/hsin-tsang/papers/www2008.pdf] (new from WWW2008)

**Leituras complementares**

* Najork, M.; Wiener, J. (Breadth-First Search Crawling Yields High-Quality Pages)[http://www10.org/cdrom/papers/208/], 2001
* Cho, J.; Garcia-Molina, H.; Page, L. (Efficient Crawling Through URL Ordering)[http://ilpubs.stanford.edu:8090/347/], 1998
* Abiteboul, S.; Preda, M.; Cobena, G. (Computing web page importance without storing the graph of the web (extended abstract))[http://groups.yahoo.com/group/archive-crawler/files/xyleme-crawlorder.pdf], 2001
* Olsten, C.; Pandey, S. (Recrawl Scheduling Based on Information Longevity)[http://www.cs.cmu.edu/~spandey/www08.pdf] (new from WWW2008)

**Informações sobre Java em relação ao Heritrix/rastreamento**

* Haydon, A; Najork, M. Performance Limitations of the Java Core Libraries (may not reflect latest Java issues, Heritrix uses a high performance DNS package)

Os estudos abaixo podem ser encontrados (também podem estar desatualizados em relação ao Java atual e nossas opções de implementação) na página de arquivos (archive-crawler Yahoo Group)[http://tech.groups.yahoo.com/group/archive-crawler/files/]:

* G. B. Reddy Study of synch vs. asynch IO in Java (synch vs. asynch IO em Java)
* G. B. Reddy Study of multi-threaded DNS performance in Java (desempenho do DNS multi-threaded em Java)

**Outros**

* (Arquivos)[http://tech.groups.yahoo.com/group/archive-crawler/files/] de grupo archive-crawler
* Cho, J.; Garcia-Molina, H. (The Evolution of the Web and Implications for an Incremental Crawler)[https://scholar.google.com/scholar?hl=en&lr=&safe=off&client=firefox-a&q=author%3A%22Cho%22+intitle%3A%22The+evolution+of+the+web+and+implications+for+an+incremental+crawler%22+&btnG=Search], Conf. on Very Large Data Bases, 2000
* (Focused Crawling The Quest for Topic-specific Portals)[https://www.cse.iitb.ac.in/~soumen/focus/]
* Focused Crawling: : A New Approach to Topic-Specific Web Resource Discovery, 1999, WWW8
* (Intelligent Crawling on the World Wide Web with Arbitrary Predicates)[http://www10.org/cdrom/papers/110/], 2001, WWW10
* Web Crawling High-Quality Metadata using RDF and Dublin Core, 2002, WWW11
* Projeto Stanford (WebBase) [http://diglib.stanford.edu:8091/~testbed/doc2/WebBase/]
* An Introduction to Heritrix - Mohr et al, 4th International Web Archiving Workshop 2004

**Especificações relevantes**

* (RFC 2616: Hypertext Transfer Protocol - HTTP/1.1)[https://www.ietf.org/rfc/rfc2616.txt]
     * Clarifying the fundamentals of HTTP por Jeffery Mogul, um autor de RFC-2616.
* RFC 3986: Uniform Resource Identifiers (URI): Generic Syntax.
* (HTML 4.01 specification)[https://www.w3.org/TR/html401/] (from W3C).
* Embora o robots.txt seja importante para o rastreamento, ele nunca foi oficialmente ratificado como RFC. A especificação mínima do defacto está disponível em robotstxt.org. Os mecanismos de pesquisa fizeram várias extensões ad hoc; recentemente, o Google compartilhou algumas informações sobre como o (GoogleBot implementa o *Robots Exclusion Protocol*)[https://webmasters.googleblog.com/2008/06/improving-on-robots-exclusion-protocol.html].
* (RFC 1034: Domain Names - Concepts and Facilities)[ftp://ftp.rfc-editor.org/in-notes/rfc1034.txt]
* (RFC 1035: Domain Names - Implementation and Specification)[ftp://ftp.rfc-editor.org/in-notes/rfc1035.txt]

### Formato de arquivo ARC

Original em: http://www.archive.org/web/researcher/ArcFileFormat.php

Autores: Mike Burner e Brewster Kahle
Data: 15 de setembro de 1996, versão 1.0
Internet Archive

**Visão geral**

O Internet Archive armazena os dados coletados em grande escala (atualmente 100MB) e agrega arquivos para facilitar o armazenamento em um sistema de arquivos convencional. Sabe-se, pela experiência do Archive, que é difícil gerenciar centenas de milhões de arquivos pequenos na maioria dos sistemas de arquivos existentes.

Este documento descreve o formato dos arquivos agregados. O formato do arquivo foi projetado para atender a vários requisitos:

* O arquivo deve ser autocontido: ele deve permitir que os objetos agregados sejam identificados e descompactados sem o uso de um arquivo de índice complementar.

* O formato deve ser extensível para acomodar arquivos recuperados por meio de vários protocolos de rede, incluindo http, ftp, news, gopher e mail.

* O arquivo deve ser "capaz de transmitir": deve ser possível concatenar vários arquivos compactados em um fluxo de dados.

* Uma vez escrito, um registro deve ser viável: a integridade do arquivo não deve depender da criação subseqüente de um índice no arquivo do conteúdo.

* No entanto, o leitor reconhecerá rapidamente que um índice externo do conteúdo e dos deslocamentos de objeto aumentará muito a capacidade de recuperação de objetos armazenados nesse formato. O arquivo mantém tais índices, mas não procura padronizar seu formato.

**Formato de arquivo do Archive**

A descrição abaixo usa o pseudo-BNF para descrever o formato de arquivo do Archive. Por convenção, os arquivos archive são nomeados com uma extensão ".arc" (por exemplo, "IA-000001.arc").

```
arc_file == <version_block><rest_of_arc_file>
version_block == See definition below
rest_of_arc_file == <doc>|<doc><rest_of_arc_file>
doc == <nl><URL-record><nl><network_doc>
URL-record == See definition below
network_doc == whatever the protocol returned
nl == Unix-newline-delimiter
sp == ' ' (ascii space) comma is inappropriate because it can be in an URL.
```

**Version Block**

O *version block* identifica o nome do arquivo original, a versão do arquivo e os campos de registro de URL do arquivo archive.

```
version-block == filedesc://<path><sp><version specific data><sp><length><nl>
<version-number><sp><reserved><sp><origin-code><nl>
<URL-record-definition><nl>
<nl>
```

**Version 1 block**

```
version-1-block == filedesc://<path><sp><ip_address><sp><date><sp>text/plain<sp><length><nl>
1<sp><reserved><sp><origin-code><nl>
<URL IP-address ArchivArchivee-date Content-type Archive-length<nl>
<nl>
```

**Version 2 block**

```
version-2-block == filedesc://<path><sp><ip_address><sp><date><sp>text/plain<sp>200<sp>
-<sp>-<sp>0<sp><filename><sp><length><nl>

2<sp><reserved><sp><origin-code><nl>
URL<sp>IP-address<sp>Archive-date<sp>Content-type<sp>Result-code<sp>Checksum<sp>Location<sp> Offset<sp>Filename<sp>Archive-length<nl>

<nl>
```

A linha "filedesc" é um registro de URL de caso especial (veja abaixo). "path" é o nome do caminho original do arquivo archive, "IP-address" é o endereço (IP) da máquina que criou o arquivo, "date" é a data em que o arquivo archive foi criado. O tipo de conteúdo "text/plain" refere-se simplesmente ao restante do *version block*. "Lenght" especifica o tamanho, em bytes, do resto do *version block*.

```
version-number == 
in ascii
reserved == string with no white space
origin-code == Name of gathering organization with no white space
URL-record-definition == names of fields in URL records
```

### URL Record

introduz um objeto no arquivo do archive e fornece o nome e o tamanho do objeto, bem como vários metadados sobre sua recuperação.

**URL Record v1**

```
URL-record-v1 == <url><sp>
<ip-address><sp>
<archive-date><sp>
<content-type><sp>
<length><nl>
```
  
**URL Record v2**

```
URL-record-v2 == <url><sp>
<ip-address><sp>
<archive-date><sp>
<content-type><sp>
<result-code><sp>
<checksum><sp>
<location><sp>
<offset><sp>
<filename><sp>
<length><nl>
```
  
**URL Record (campos)**

```
url == ascii URL string (e.g., "http://www.alexa.com:80/")
ip_address == dotted-quad (eg 192.216.46.98 or 0.0.0.0)
archive-date == data da criação do arquivo archive
content-type == "no-type"|MIME type of data (e.g., "text/html")
length == representação ascii do tamanho do doc de rede em bytes
date == YYYYMMDDhhmmss (Greenwich Mean Time)
result-code == código de resultado ou código de resposta, (e.g. 200 or 302)
checksum == representação ascii de uma soma de verificação dos dados. As especificidades da soma de verificação são específicas da implementação.

location == "-"|url of re-direct
offset == deslocamento em bytes desde o início do arquivo até o início do registro de URL
filename == nome do arquivo arc
```

Observe que todos os valores de campo são texto ascii. Todos os campos possuem pelo menos um caractere. Nenhum valor de campo contém um espaço.

**Exemplo de um arquivo Archive**

No exemplo a seguir, lembre-se de que "lenght" inclui carriage returns e avanços de linha.

**versão 1**

```
filedesc://IA-001102.arc 0 19960923142103 text/plain 76
1 0 Alexa Internet
URL IP-address Archive-date Content-type Archive-length

http://www.dryswamp.edu:80/index.html 127.10.100.2 19961104142103 text/html 202
HTTP/1.0 200 Document follows
Date: Mon, 04 Nov 1996 14:21:06 GMT
Server: NCSA/1.4.1
Content-type: text/html Last-modified: Sat,10 Aug 1996 22:33:11 GMT
Content-length: 30
<HTML>
Hello World!!!
</HTML>
```

**versão 2**

```
filedesc://IA-001102.arc 0.0.0.0 19960923142103 text/plain 200 - - 0
IA-001102.arc 122
2 0 Alexa Internet
URL IP-address Archive-date Content-type Result-code Checksum
Location Offset Filename Archive-length

http://www.dryswamp.edu:80/index.html 127.10.100.2 19961104142103
text/html 200 fac069150613fe55599cc7fa88aa089d - 209 IA-001102.arc 202
HTTP/1.0 200 Document follows
Date: Mon, 04 Nov 1996 14:21:06 GMT
Server: NCSA/1.4.1
Content-type: text/html Last-modified: Sat,10 Aug 1996 22:33:11 GMT
Content-length: 30
<HTML>
Hello World!!!
</HTML>
```

**Lendo um arquivo ARC**

Como observado acima, a melhor maneira de recuperar um objeto específico de um arquivo archive é manter um banco de dados externo de nomes de objetos, os arquivos nos quais eles estão localizados, seus deslocamentos nos arquivos e os tamanhos dos objetos. Então, para recuperar o objeto, basta abrir o arquivo, buscar o offset e fazer uma única leitura de bytes `<size>`.

Programas que precisam ler o arquivo sem um índice (como para descompactar o arquivo inteiro) devem usar I/O armazenada em buffer. O registro de URL pode, então, ser lido com um fgets (), e os objetos podem ser lidos com um fread () de <size> bytes.
  
**Usando o formato Archive para outros tipos de URL**

Como o formato Archive usa a especificação de URL padrão para identificar objetos, ele naturalmente se empresta para o armazenamento de dados recuperados por meio de protocolos diferentes de HTTP. Por exemplo, um artigo de notícias pode aparecer da seguinte maneira:

```
news:28SEP96.21024750@alligator.dryswamp.edu 127.10.100.3 19960929142103 text/plain 328
Path: news.alexa.com!news1.best.com!news.dryswamp.edu!joebob
From: joebob@alligator.dryswamp.edu
Newsgroups: alt.food
Subject: Re: I'm hungry
Date: 28 SEP 96 21:02:47 GMT
Organization: Dry Swamp University
Lines: 1
Message-ID: <28SEP96.21024750@alligator.dryswamp.edu>
NNTP-Posting-Host: alligator.dryswamp.edu
```

**Para mais informações**

Entre em contato com info@archive.org

## Análise de Requisitos do Rastreador do Internet Archive

Análise de Requisitos do Rastreador do Internet Archive
2003-03-07
Michele Kimpton (michele@archive.org)
[Raymie Stata (raymie@archive.org)
Gordon Mohr (gojomo@archive.org)]

### 1. Introdução

O Internet Archive, em colaboração com várias *National Libraries*, está procurando construir um rastreador de código aberto que possa ser usado, principalmente, para fins de arquivamento da Web, atendendo aos requisitos definidos pelo National Libraries Web Archive Consortium.

Este documento descreve, em parte, os requisitos previstos para tal software de rastreamento. A primeira subseção discute os "casos de uso" que desejamos suportar (isto é, descreve, detalhadamente, nossos padrões de uso previstos). A seção a seguir fornece uma declaração de requisitos derivados desses casos de uso.

### 2. Análise de casos de uso

O Archive deseja suportar os seguintes casos de uso:

### 2.1 Rastreamentos amplos

Rastreamentos amplos são rastreamentos grandes, de alta largura de banda, nos quais o número de sites e o número de páginas interessantes e individuais "tocadas" é tão importante quanto a completude com que qualquer site é coberto. A Web não está catalogada; um rastreamento amplo é importante para descobrir páginas desconhecidas. Além disso, não se pode saber a priori quais partes da Web serão importantes no futuro; rastreamentos amplos tentam garantir cobertura suficiente para usos desconhecidos.

### 2.2 Rastreamentos restritos

Rastreamentos restritros são rastreamentos de tamanho pequeno ou médio, nos quais o critério de qualidade é a cobertura completa de alguns sites ou tópicos selecionados. Rastreamentos restritos são necessários para capturar sites que são conhecidos a priori como importantes ( intrinsecamente ou em relação a alguma coleção que está sendo montada).

### 2.3 Rastreamentos contínuos

Os rastreadores normalmente têm se esforçado para não fazer download de páginas que já foram baixadas. Esses rastreadores são conhecidos como rastreadores *batch* ou *snapshot crawlers*, pois geralmente são executados durante um período fixo para capturar a Web e, em seguida, são reiniciados para começarem um novo rastreamento.

Rastreamentos contínuos fazem o download contínuo de páginas buscadas anteriormente (procurando alterações), bem como (possivelmente) a descoberta e a busca de novas páginas. O rastreamento contínuo é útil para capturar páginas que mudam rapidamente. Além disso, quando as taxas de download das páginas são ajustadas para refletir as freqüências de alteração medidas, o rastreamento contínuo pode ser mais eficiente do que o rastreamento *batch*.

O Archive tem interesse em rastreamentos contínuos, tanto pelos amplos quanto restritos.

### 2.4 Rastreamentos de multiprotocolo

O Archive está interessado em rastrear páginas disponíveis através de protocolos diferentes de HTTP.RSS. Os protocolos adicionais incluem HTTPS, FTP e protocolos de streaming, como MMS, RTSP e PNA.

### 2.5 Rastreamento experimental

O Archive quer apoiar experimentos em técnicas de rastreamento e tecnologia executadas por organizações externas. O estudo da Biblioteca Nacional de Ciências Digitais (National Digital Science Library) feito na Universidade de Cornell é um bom exemplo do que queremos apoiar. Neste projeto, os pesquisadores queriam construir um reastreador focado em um tema, para encontrar materiais relacionados à ciência na Web. Eles foram capazes de escrever "analisadores" que usaram técnicas de vetor de termo para identificar material de interesse e foram capazes de modificar as políticas do rastreador para usar essas informações para direcionar o rastreamento.

Queremos apoiar projetos como o NSDL. Para fazer isso, o rastreador precisa ser fácil de estender e usar, não podendo ser sobrecarregado com restrições de licenciamento.

### 2.6 Administração "zero"/facilidade de operação

O Archive não tem uma grande equipe de operadores e, portanto, requer um alto nível de automação em seus rastreadores. Em particular, acreditamos que um único rastreamento amplo pode ser administrado, em tempo parcial, por uma única pessoa (assumindo conectividade de rede e hardware confiável). Além disso, o Archive não consegue fornecer suporte técnico para organizações externas usando seu software de rastreamento, portanto, este software deve ser fácil de operar sem precisar de suporte.


### 3. Requisitos

Acreditamos que os seguintes requisitos são necessários para suportar os casos de uso descritos acima:

### 3.1 Extensibilidade

Extensibilidade é a capacidade de alterar o comportamento do rastreador: sua política de seleção, sua política de agendamento, o que ele armazena em disco e assim por diante. É o nosso requisito mais importante, já que é necessário para suportar quase todos os casos de uso acima (rastreamentos amplo e restritos, rastreamentos contínuos, rastreamentos de multiprotocolo, facilidade de operação e rastreamentos experimentais).

Conforme enumerado abaixo, a extensibilidade se aplica a muitos aspectos do rastreador. Em um alto nível, o que realmente queremos é um *framework* para criar rastreadores, não um rastreador em particular que incorpore políticas ou recursos específicos. Um *designer de rastreamento* deve ser capaz de criar uma instância desta estrutura para atender às necessidades de um projeto de rastreamento específico. Ao mesmo tempo, não queremos um framework objeto-orientado tradicional que *exija* que a programação reúna sistemas úteis. Como discutiremos a seguir, enquanto a capacidade de conectar códigos novos e personalizados no framework é importante, também é importante que o framework também suporte extensibilidade através de parametrização.

### 3.1.1 Aspectos extensíveis do rastreador

Vários aspectos do rastreador precisam ser extensíveis. Nessa subseção, listamos o que precisa ser extensível no rastreador. Na subseção seguinte, listamos como essa extensibilidade deve ser alcançada.

### 3.1.1.1 Protocolos

A adição de novos protocolos de download ao framework deve ser fácil.

### 3.1.1.2. Seleção (descoberta e filtragem)

A política de seleção determina o conteúdo que o rastreador fará download. É, normalmente, produto de dois mecanismos: um mecanismo de descoberta pelo qual novas URLs são identificadas e um mecanismo de filtragem que decide quais URLs descobertas devem ser baixadas. Nos rastreamentos contínuos, em que URLs antigos são rastreados novamente, também é necessário que haja um processo de filtragem para eliminar os URLs que não são mais de interesse.

As políticas de seleção são de importância vital para rastreadores amplos e restritos. É uma área na qual esperamos que haja pesquisas e experimentos contínuos e significativos. Assim, nosso framework de rastreador deve oferecer suporte à extensibilidade mais ampla possível quando se trata de descoberta e mecanismos de filtragem.

Às vezes, o processo de descoberta pode ocorrer fora do próprio rastreador. Por exemplo, algumas organizações têm acesso a proxies da Web ou outras informações de uso que podem ser boas fontes de URLs para rastrear. Para dar suporte a mecanismos externos de descoberta e filtragem, deve ser possível tanto adicionar quanto remover um grande número de URLs do rastreador enquanto um rastreamento está em andamento.

### 3.1.1.3 Agendamento

A política de agendamento (às vezes chamada de política de classificação) determina a ordem na qual os URLs pendentes são baixados. As políticas de agendamento e seleção, juntas, definem o conteúdo da coleção obtida por um rastreamento e, portanto, são de enorme interesse para designers de rastreamento no que diz respeito a ambos. No entanto, a flexibilidade em relação ao agendamento é particularmente difícil de fornecer, pois a política de agendamento é determinada pela *frontier* de URLs pendentes do rastreador.

A *frontier* de um rastreador é a coleção de URLs que o rastreador pretende baixar no futuro. Em um rastreamento em massa, a *frontier* contém apenas URLs novos (URLs que não foram baixados nesse rastreamento). Em um rastreamento contínuo, a *frontier* pode conter URLs novos e URLs antigos que devem ser rastreados novamente. A política de agendamento determina a ordem na qual os elementos são removidos dessa *frontier*. Para rastreamentos amplos, a *frontier* pode se tornar muito grande, contendo até 10 de bilhões de URLs e, assim, em um rastreador com capacidade de escala, a *frontier* deve ser uma estrutura de dados baseado em disco. Estruturas de dados baseadas em disco podem ter alta latência, mas a frontier está no caminho crítico do rastreador, onde a latência não pode ser tolerada. Assim, uma boa frontier requer habilidade de programação significativa para implementar e ajustar.

Como resultado, não esperamos que novas *frontiers* sejam escritas com rapidez e facilidade e, portanto, a flexibilidade para agendar políticas não pode advir principalmente da criação de novas implementações de frontier. É importante que o framework do rastreador venha com uma variedade de implementações de frontier que suportam uma variedade de políticas de agendamento. Ao mesmo tempo, para apoiar a experimentação e desenvolvimento, deve haver uma boa API para definir novas implementações de frontier.

### 3.1.1.4 Cortesia (Politeness)

Os rastreadores de alta capacidade podem facilmente sobrecarregar um servidor Web de baixo custo e podem até sobrecarregar os servidores Web de ponta. Por isso, é muito importante que os rastreadores sejam "cortês", limitando a carga que eles colocam em um servidor da Web.

Infelizmente, a necessidade de cortesia às vezes entra em conflito com a política de agendamento de um rastreamento. Por exemplo, uma política simples de cortesia é de escalonar, com o tempo, as páginas baixadas de um determinado site. No entanto, como discutido acima, às vezes é desejável baixar imagens e outros objetos associados a uma página imediatamente após o download da própria página. Se este último requisito é particularmente importante para o rastreamento, é necessária uma política de cortesia mais sofisticada para acomodá-lo. Ao mesmo tempo, quando esses problemas de atualização não são importantes para um rastreamento, são geralmente considerados secundários ao espaço de downloads de um site tão distante quanto possível.

O rastreador deve ser flexível em relação às políticas de cortesia que implementa. Novamente, não existe um rastreador de "tamanho único"; o que é necessário é um framework que acomode uma ampla variedade de políticas.

### 3.1.1.5 Processamento de documentos

Depois que um documento é baixado, ele é processado por vários motivos. Recursos podem ser extraídos para fins de rastreamento adicional (por exemplo, links na maioria dos rastreamentos, além de texto de links para rastreamentos focados em um tema). Recursos podem ser extraídos simplesmente para coletar estatísticas e a própria página pode ser transformada em algum formato de saída apropriado.

Assim, o rastreador deve suportar um framework para "analisadores" conectáveis que processem documentos. Além disso, essa estrutura deve oferecer suporte à interoperabilidade efetiva entre os analisadores, garantindo que o bom trabalho de um grupo possa ser construído por outros.

Os analisadores devem ter acesso ao máximo de metadados possíveis. Isso inclui o URL usado para buscar o objeto, download de timestamps (data e hora), informações de resolução de DNS e cabeçalhos de solicitação e resposta usados durante a transação (e qualquer outro material usado para buscar o objeto, como senhas ou chaves SSL).

Os analisadores devem ter acesso aos resultados de todas as solicitações, não apenas as bem-sucedidas. Isso é necessário, por exemplo, para detectar alterações na Web ou para detectar as "bordas" da *deep web*. Isso significa registrar, por exemplo, respostas 30x (moved/redirect) e 40x (invalid/not found/unauthorized). Isso também significa registrar solicitações negadas por regras de robot exclusion. Por fim, os arquivos de robot exclusion buscados automaticamente também devem estar sujeitos a análises regulares (em vez de serem manipulados por um caminho separado).

A extração de links é, obviamente, uma forma muito importante de processamento de documentos. Extensibilidade, nesse respeito, significa que deve ser fácil adicionar módulos para lidar com Javascript, Flash e outros tipos de mídia que tenham links incorporados.

Outra forma de processamento de documentos é a eliminação de arquivos duplicados. Deve ser possível para o rastreador reconhecer, durante o rastreamento, que uma duplicata foi encontrada para que etapas especiais (mais eficientes) possam ser executadas. Qualquer tratamento especial de uma duplicata deve ser observado na saída do rastreador e passível de análise por ferramentas padrões. Deve ser possível variar a definição de "duplicate" usada (de muito restrito a muito amplo).

A saída (output) é outra forma importante de processamento de documentos. A saída do rastreador deve ser altamente configurável tanto na seleção dos dados a serem enviados quanto no formato em que são enviados. Embora um normalmente salve as páginas de um rastreamento, os rastreamentos geralmente são executados simplesmente para coletar recursos ou estatísticas. Nesses casos, as páginas em si não são armazenadas, em vez disso, os recursos e/ou estatísticas são extraídos no rastreador e armazenados diretamente no disco, reduzindo significativamente os requisitos de espaço em disco. O rastreador deve suportar esse tipo de saída.

Formatos de arquivo para saída variam. No caso de páginas, por exemplo, usam-se arquivos ZIP, arquivos tar ou o arquivo "ARC" do Internet Archive. Precisamos dar suporte a arquivos ARC, cujo formato iremos documentar, expandir (onde for necessário) e promover como padrão geral. Nosso framework deve acomodar a extensão programática de terceiros para outros formatos, quando necessário.

### 3.1.1.6 Interatividade

A Web está se tornando cada vez mais interativa: para receber páginas da Web, os internautas geralmente precisam preencher formulários, e os navegadores devem oferecer suporte a mecanismos de autenticação e cookies.

Para acompanhar essa Web cada vez mais interativa, os rastreadores devem oferecer suporte a esses mecanismos de interação. No mínimo, eles devem suportar cookies e esquemas simples de senha. Além disso, há um desejo crescente de ainda mais suporte, portanto, é preciso haver mecanismos de extensibilidade para permitir que os designers de rastreamento ampliem o repertório de interações suportadas pelo rastreamento.

### 3.1.2 Mecanismos de extensibilidade

Existem dois mecanismos bem conhecidos para tornar o software extensível: parametrização e extensões de código. A parametrização é fácil de usar, mas é restritiva no que pode suportar. Extensões de código requerem programação, mas suportam uma ampla gama de flexibilidade. Conforme discutido abaixo, a estrutura do rastreador deve oferecer suporte a ambas as formas de extensibilidade.

Além de oferecer suporte a ambas as formas de extensibilidade, o rastreador deve oferecer suporte à extensão dinâmica, ou seja, a alteração do rastreamento enquanto um rastreamento está em andamento. Discutimos esta questão abaixo, também.

### 3.1.2.1 Parametrização

Extensibilidade através de parametrização é a capacidade de personalizar o comportamento do rastreamento através do uso de módulos de código cujo comportamento pode ser alterado fornecendo parâmetros apropriados. Por exemplo, um módulo de frontier pode ter um parâmetro de cortesia controlando quanto tempo esperar antes de revisitar um determinado servidor ou um parâmetro de agendamento controlando se os objetos de imagem de uma página são colocados no início ou no final da fila de rastreamento. Outro exemplo é que um módulo de filtro pode usar um conjunto de expressões regulares que controlam os URLs recém-descobertos que devem ser permitidos na frontier.

Outra forma de parametrização é a capacidade de controlar, a partir de um arquivo de configuração simples em tempo de execução, quais módulos de código são usados durante o rastreamento. Por exemplo, uma implementação da frontier pode basear-se em uma política abrangente de rastreamento; uma implementação diferente pode ser baseada em uma política ordenada por classificação de página. Por meio de diretivas simples no arquivo de configuração do rastreador, o designer de rastreamento deve poder selecionar entre essas implementações de frontier. (É claro que esses módulos em si podem ser parametrizados mais adiante, como discutido acima. Por exemplo, os dois módulos podem ter um sinalizador indicando que as imagens de uma página devem ser tratadas como exceções à política geral de planejamento, garantindo que essas imagens sejam baixadas simultaneamente com as páginas referentes a elas.)

No mínimo, o framework do rastreador deve ter parâmetros controlando os seguintes aspectos do rastreamento:

*Filtragem.* Filtragem baseada em URL simples, na qual os padrões especificam nomes de host e/ou caminhos de interesse. Além disso, deve ser possível filtrar com base nas contagens de hops, ou seja, com base no número de hops a partir do apage permitido pelos testes baseados em URL.

*Agendamento.* Como mencionado acima, o agendamento é determinado em grande parte pelos tipos de frontiers permitidos pelo rastreador. Acreditamos que os seguintes tipos de frontiers são necessários:

*Frontiers de prioridade múltipla e ampla.* Essas frontiers permitem implementações eficientes, baseadas em disco, que podem ser dimensionadas para rastreamentos muito grandes. Prioridades múltiplas significa que há várias filas com prioridades diferentes, permitindo agendar downloads "urgentes" acima da fila básica. Esses downloads urgentes suportariam, por exemplo, o download de uma imagem e, ao mesmo tempo, da página que a contém. Essas prioridades também podem ser usadas em rastreamentos contínuos, em que páginas que mudam com frequência podem ter prioridade mais alta do que as que mudam menos.

*Site-first frontier.* Essas frontiers estão mais próximas de rastreadores depth-first, tentando concluir sites o mais rápido possível antes de iniciar novos sites. Como frontiers abrangentes, elas são muito escalonáveis e podem suportar um esquema de prioridade.

*Frontier classificada dinamicamente.* As frontiers anteriores atribuem uma prioridade imutável às URLs quando elas são (re)inseridas na frontier. Uma frontier classificada dinamicamente permite que o rastreamento atualize prioridades de URL após a inserção. Tais frontiers podem ser usadas, por exemplo, para fazer o download de páginas na ordem do Pagerank próximo e também permitir que as classificações sejam modificadas de acordo com considerações lingüísticas ou outras. Essas frontiers normalmente têm um componente na memória que limita sua escalabilidade de acordo com a quantidade de memória nas máquinas do rastreador e menos escalonável do que as frontiers classificadas estatisticamente.

Todas essas frontiers devem oferecer suporte a distribuição em várias máquinas. Elas devem oferecer suporte a rastreamentos contínuos e políticas flexíveis de politeness.

*HTTP.* O rastreador deve vir com suporte embutido para HTTP (v. 1.1). Essa implementação HTTP deve suportar robot exclusions, mas também deve ser possível ignorar exclusões de robot. Este módulo deve suportar a parametrização de cabeçalhos de saída (por exemplo, permitir que o designer de rastreamento defina o User-Agent e outros campos de cabeçalho). Este módulo deve suportar o uso do cabeçalho if-modified-since do HTTP (e também suportar não usá-lo).

*Extração de links.* O rastreador deve conseguir extrair links de HTML e de objetos Flash. Em objetos HTML, ele deve fazer um "best effort" para extrair links do Javascript e outras linguagens de script.

*Saída.* Como mencionado acima, a saída para arquivos ARC, arquivos TAR e arquivos ZIP precisa ser suportada. Deve haver um mecanismo para controlar quais objetos são salvos, com base no tipo de mídia e também nos testes baseados em URL.

Os designers de rastreamento não precisarão escrever novos códigos para obter as alterações listadas acima.

### 3.1.2.2 Extensões de código

Extensibilidade através de extensões de código é a capacidade de alterar o comportamento do rastreamento, inserindo no software de rastreamento módulos de personalização que substituem ou aumentam módulos padronizados. Isso permite que o designer de rastreamento desenvolva (ou tenha desenvolvido) módulos que atendem necessidades que não podem ser atendidas por meio da parametrização de módulos existentes.

Suporte para extensões de código é complicado. Uma coisa é quando os autores originais do rastreador escrevem dois ou três módulos a partir dos quais o operador de rastreamento pode escolher. Outra bem diferente é fazer um design e documentar APIs internas apropriadas que permitem que terceiros escrevam seus próprios módulos. Esse design extensível não pode ser adicionado a um rastreador depois dele já ter sido escrito - influencia todos os aspectos da composição interna do rastreador e deve ser contemplado desde o início.

Uma estrutura de rastreamento extensível deve suportar extensões de código em seus módulos de download (que impactam a extensibilidade de protocolos e interatividade), sua frontier (impacta o agendamento e a politeness), inserção de frontier (seleção) e processamento de documentos (extração de recursos, saída, descoberta, filtragem) . Também deve haver uma boa infraestrutura interna para os vários módulos comunicarem seus resultados uns aos outros; este é particularmente o caso para comunicar os resultados do processamento de documentos em todo o rastreador.

### 3.1.2.3 Extensibilidade dinâmica

Os rastreamentos geralmente se estendem por dias e até semanas; rastreamentos contínuos podem ser executados por meses. Não é suficiente que um rastreamento de longa duração seja extensível quando é iniciado pela primeira vez, deve ser possível alterar o rastreamento durante o próprio rastreamento. Para alterar as políticas, talvez seja necessário suspender temporariamente o rastreador, mas o processo de reinicialização deve ser razoavelmente rápido e não deve perder o controle do estado do rastreamento.

## 3.2 Alta largura de banda

Uma única máquina baseada em Pentium  (configurada adequadamente) deve ser capaz de baixar de 10 a 20 milhões de documentos por dia (o que significa cerca de 15-30 Mbps de largura de banda em média durante o dia inteiro). Além disso, deve ser possível distribuir um rastreamento 10-20 dessas máquinas para obter melhorias de 10 a 20 vezes na largura de banda de rastreamento.

### 3.3 Largura de banda sustentada

A largura de banda do rastreador não deve parar de cair até que pelo menos 2.5B documentos tenham sido baixados (se houver). (Isso não pressupõe interferência de questões de cortesia. Por exemplo, um conjunto adequado de sites para escolher).

### 3.4 Acréscimo

O rastreador deve ser utilizável para rastreamentos grandes ou pequenos. Embora ele deva ser fácil de usar para todos os tipos de rastreamentos, ele deve ser simples para rastreamentos pequenos, ou seja, o operador de um rastreamento pequeno não deve sofrer por conta de complexidade desnecessária apenas porque também queremos suportar grandes rastreamentos. Acreditamos que a melhor maneira de suportar esse requisito é uma configuração padrão do "toolkit" que faz um trabalho adequado para problemas simples de rastreamento.

### 3.5 Portabilidade

O rastreador deve rodar muito bem no Linux. Onde for prático, o rastreador também deve ser capaz de pequenas execuções de teste no Windows 2K/XP. Isso significa que o rastreador está escrito em linguagens e com middlewares e bibliotecas portáveis.

### 3.6 Facilidade de operação

Como sugerido em 3.4, "facilidade de operação" pode ser um termo relativo. Um rastreamento simples, com uma única máquina e com várias horas de trabalho, pode ser feito por operadores com habilidades administrativas do SO relativamente primitivas e sem habilidades de programação. Um rastreamento complexo de várias semanas e várias máquinas exigirá habilidades mais avançadas. Mas, em ambos os casos, por meio de uma combinação de simplicidade e boa documentação, o rastreador deve ser operável por qualquer pessoa com o SO e as habilidades de programação necessárias; apelos a uma "tradição oral" secreta (infelizmente comum quando se trata de rastreadores) não deveriam ser absolutamente necessários.

Além de ser simples de operar, o rastreador deve exigir pouca atenção ou esforço enquanto estiver em andamento. Embora condições excepcionais que exijam a intervenção do operador sempre existam, o rastreador deve ser tão robusto quanto possível à rede e a outros problemas. O operador não deve ser obrigado a fornecer uma lista contínua de URLs para download ou alimentar o rastreador para mantê-lo em andamento (embora seja possível que um operador insira URLs se quiser).

Rastreamentos longos (de vários dias) podem ser temporariamente interrompidos por diversos motivos: a conexão WAN está indisponível, um bug no software ou a política de rastreamento precisa ser alterada. Para suportar tais interrupções, deve ser possível reiniciar o rastreamento a partir de dados armazenados no disco. Idealmente, essas reinicializações devem ser "limpas", no sentido de não haver perda de dados; por exemplo, se um documento está sendo baixado, mas não termina quando o rastreador é desligado, o rastreador deve tentar baixar esse documento novamente quando ele for reiniciado.

Um bom registro (incluindo controle sobre a saída de log) também é necessário para monitorar o comportamento do rastreador e diagnosticar qualquer problema. O registro também deve suportar a confiança de longo prazo na saída da atividade de rastreamento, auxiliando na determinação da proveniência e autenticidade dos dados. No entanto, esses logs nunca devem se tornar necessários para analisar os resultados do rastreamento de maneira útil: os arquivos ARC independentes devem permanecer suficientes e completos como um registro de rastreamento autocontido.

Algum tipo de "painel de controle" é necessário para permitir que o operador realize operações básicas, como iniciar um rastreamento, monitorar o progresso do rastreamento, iniciar uma interrupção temporária do rastreamento ou alterar os parâmetros de registro. Uma interface baseada na web para executar essas tarefas, a partir da máquina local ou remotamente, deve ser disponibilizada.

Por fim, a "facilidade de operação" se estende ao software exigido pelo rastreador, bem como ao próprio rastreador. Por exemplo, embora os ORBs sejam aceitáveis, muitos deles são muito difíceis de administrar, e o rastreamento não deve depender de um deles.

### 3.7 Licença Aberta

Embora não seja um requisito técnico em si, acreditamos que o licenciamento aberto é necessário para atingir nossos objetivos, especialmente o objetivo da colaboração externa. O Archive tem ampla experiência no uso de rastreadores de outras organizações. Descobrimos que as atividades do Archive têm baixa prioridade nessas organizações e, portanto, pode levar muito tempo para obter permissão para fazer qualquer coisa (se é que podemos obtê-la). Além disso, já tentamos dar acesso à tecnologia de reastreamento a terceiros, afim de apoiar pesquisas e experimentos realizados em universidades e outras instituições, mas fomos impedidos.

Assim, queremos um framework de rastreamento no qual não tenhamos que pedir permissão para fazer a maioria das coisas que desejamos fazer. Idealmente, queremos um rastreador completamente aberto. No mínimo, queremos um rastreador que qualquer pessoa (por exemplo, o Archive, as universidades e até mesmo laboratórios de pesquisa de empresas) possa usar para fins não comerciais sem pedir permissão ao detentor dos direitos autorais.

Esperamos que a *GNU Lesser General Public License* (LGPL) atenda às nossas necessidades de licenciamento, e planejamos liberar nosso trabalho, no mínimo, sob esta licença. (Outras opções de licença são possíveis.)

Em um tópico relacionado, o rastreador não deve depender de middleware ou outro software que vem com termos de licenciamento proibitivos.

## Usuários do Heritrix

Se o seu projeto, instituição ou empresa usa o Heritrix, sinta-se à vontade para adicionar detalhes aqui.

### Bibliotecas, *Archives* e Instituições de memória

* Internet Archive - lidera o desenvolvimento de código aberto do Heritrix; usa o Heritrix como o rastreador de inúmeros projetos de rastreamento focados/temáticos e amplos, incluindo o serviço Archive-It, e rastreia alimentando nossa Wayback Machine e coleções de parceiros. Versões mais utilizadas: Heritrix 1.14.4-SNAPSHOT, 3.0.0-SNAPSHOT.

* Biblioteca Britânica (The British Library) - usa o Heritrix 1.14.3 como rastreador para o nosso Projeto de Pesquisa de Domínio. Colaborou com a Biblioteca Nacional da Nova Zelândia para desenvolver a *Web Curator Tool* (Ferramenta de Curadoria da Web), que também usa o Heritrix como o rastreador subjacente e é atualmente usado para impulsionar o UK Web Archive.

* A Biblioteca do Congresso (The Library of Congress) - trabalha com o Internet Archive para ajudar a criar arquivos da web de coleções focalizadas e temáticas. A equipe de arquivamento da web da Biblioteca também começou a fazer rastreamentos locais, usando, atualmente, o Heritrix 3.0.X para projetos selecionados.

* Biblioteca Digital da Califórnia, Serviço de Arquivamento da Web (California Digital Library, Web Archiving Service)

* Biblioteca Nacional Central de Florença (Biblioteca Nazionale Centrale Firenze) - usa heritrix3 e warc 1.0 para o programa de arquivamento de teses de doutorado eletrônico de repositórios de universidades.

* Smithsonian Institution Archives - testa o Heritrix 1.14.3 em uma máquina Windows para capturar seus inúmeros sites e redes sociais.

* Netarchive.dk - usa o Heritrix 1.14.3 integrado no NetarchiveSuite para coletar a internet dinamarquesa.

* Biblioteca Nacional e Universitária da Islândia (National and University Library of Iceland) - Participou ativamente do desenvolvimento do Heritrix desde o início do projeto. Usa o Heritrix 1 para conduzir rastreamentos de domínio e direcionados desde 2004 e o Heritrix 3 desde 2010.

* Biblioteca Nacional da França (French National Library) - usa o Heritrix para rastreamentos produtivos desde o final de 2006. Executou o primeiro rastreamento de domínio nacional francês com o Heritrix/NetarchiveSuite na primavera de 2010.

* Biblioteca Nacional Austríaca (Austrian National Library) - usa o Heritrix desde o início da Web@rchive Áustria, em 2008.

* Biblioteca Nacional da Catalunha - iniciou em junho de 2005 um projeto chamado PADICAT (Patrimônio Digital da Catalunha). Usa o Heritrix 1.14.4 como um rastreador para o domínio de nível superior .CAT, compilação seletiva de saída de sites de organizações catalãs e captura centrada de eventos públicos. Esses rastreamentos são usados para alimentar a Wayback 1.4.2 (pesquisa de URL) e WERA (pesquisa por palavra-chave) e são mostrados, com acesso livre, através do PADICAT. 

* [adicione o nome de sua instituição aqui]

### Empresas e Projetos Comerciais

* neofonie - usa o Heritrix 1.14.4 e 3.0.x para reunir dados não estruturados. Depois que os dados são processados, enriquecidos e classificados, são usados em mecanismos de pesquisa e aplicativos da Web.

* Dataclip - usa o Heritrix 3 para rastrear os top 10 milhões de websites de negócios, afim de oferecer inteligência de marketing e vendas com base nas tecnologias da web usadas por essas organizações.

* TNR Global - usa o Heritrix para rastrear conteúdo da web. Por exemplo: slide #12 dessa apresentação: Migration from Fast ESP to Lucene Solr - Michael McIntosh

* [adicione sua empresa ou projeto aqui]

### Projetos de pesquisa

* CiteSeerX - usa o Heritrix 1.14.4 e 3.0.x para rastrear documentos acadêmicos de acesso livre online.

* Web Archiving Integration Layer (WAIL) - agrupa um binário Heritrix 3.2.0 pré-configurado com outras ferramentas pessoais de arquivamento da Web em um aplicativo nativo e fornece uma interface gráfica de usuário para acesso.

* [adicione seu projeto aqui]

### Outros

* [adicione aqui]

## Como fazer um rastreamento

* Requisitos

* Espaço de trabalho e arquivos

* Configuração

* Executar o rastreamento

* Verificar o status do rastreamento

* Arquivos de Saída WARC

* Adicionar WARCs ao Archive

* Solução de problemas

> Crawl before you walk. - C. Darwin

### Requisitos

Para executar um rastreamento da Web com o Heritrix, você precisará do código (arquivos de classe Java), arquivos de configuração e recursos adequados do sistema:

* pelo menos 4 GB de RAM
* espaço em disco local suficiente para todos os arquivos WARC de saída
* menor número de processos concorrentes possível

### Espaço de trabalho e arquivos

Os exemplos neste documento são baseados em rastreamentos executados em uma VM chamada `blub-dev`, com arquivos denominados `blub_`...* nome*. Use suas próprias convenções de nomeação de arquivo.

```
blub@blub-dev:/1$ df -h
Filesystem Size Used Avail Use% Mounted on
udev 2.0G 4.0K 2.0G 1% /dev
tmpfs 396M 5.1M 391M 2% /run
/dev/vda1 28G 5.8G 21G 22% /
none 4.0K 0 4.0K 0% /sys/fs/cgroup
none 5.0M 0 5.0M 0% /run/lock
none 2.0G 0 2.0G 0% /run/shm
none 100M 0 100M 0% /run/user
/dev/vdb1 126G 41G 86G 33% /1
nfs-home:/home 7.1T 6.1T 990G 87% /home
```

O diretório `/1` tem muito espaço. Criamos um diretório chamado `/1/crawling` e copiamos o código Heritrix e os arquivos de configuração, criados originalmente por John Lekashman para rastreamento de sites cubanos.

```
# cd /1
# mkdir crawling
# chown blub.users crawling
^D
$ cd /1/crawling
$ cp /home/lekash/blub.crap/* .
$ tar xvf cuba00000.tar
$ mv cuba0000 blub_test
```

Precisamos de um repositório padrão ou outra fonte desses arquivos de código e configuração.

### Configurações

O `README` não é necessário, mas é útil para documentar detalhes do rastreamento que não estão especificados nos arquivos de configuração.

```
$ cd blub_test
$ vi README
```

Agora vamos modificar nossa configuração para esse rastreamento.

```
$ mv cuba test_config
$ cd test_config
```

Primeiro, precisamos de um arquivo de texto `seed` de URLs que originarão o rastreamento. Neste caso, criamos o arquivo `mcloud_seeds.txt` a partir do arquivo RSS https://core.mediacloud.org/static/mc_20160317.rss:

```
$ head mcloud.seeds.txt
http://feeds.washingtonpost.com/c/34656/f/636663/s/4e55a97b/sc/7/l/0L0Swashingtonpost0N0Cblogs0Cright0Eturn0Cwp0C20A160C0A30C170Cwe0Ecant0Edefend0Edemocracy0Eby0Eelecting0Ea0Ethug0C/story01.htm
http://feeds.washingtonpost.com/c/34656/f/636663/s/4e56fd0f/sc/7/l/0L0Swashingtonpost0N0Cblogs0Cright0Eturn0Cwp0C20A160C0A30C170Ca0Elot0Eof0Erepublicans0Eare0Ein0Elindsey0Egrahams0Eshoes0C/story01.htm
http://www.news.com.au/sport/nrl/five-things-you-need-to-know-about-new-nrl-ceo-todd-greenberg/story-fndujljl-1227793059847?from=public_rss
http://www.abc.es/opinion/abci-secta-201603180423_noticia.html
...
```

Agora editamos os valores de configuração:

```
$ mv cuba.cxml blub_test.cxml
$ vi blub_test.cxml
```

A configuração do Heritrix é uma combinação complexa de sintaxe XML e nomes de beans Java. As opções dependem do tamanho e profundidade do rastreamento, da verificação de arquivos duplicados e assim por diante. Este exemplo é de um rastreamento razoavelmente restrito.

Edite os valores mostrados abaixo e deixe as outras linhas como estão:

```
# (definições editáveis em blub_test.cxml)
 
# serão exibidos no painel de rastreamento:
metadata.jobName = blub_test
metadata.description = mediacloud_crawl
 
# prefixo do nome do arquivo WARC de saída, por convenção <= 6 caracteres, TUDO EM MAIÚSCULAS:
warcwriter.prefix = MCLOUD
 
# tópicos de gravação Java:
warcwriter.poolMaxActive = 6
 
# o arquivo "seed" de URLs:
<property name="path" value="mcloud.seeds.txt">
 
# inclua isso para ativar a deduplicação:
<import resource="hq-beans.xml">
 
# defina uma hop count (quão longe rastrear):
<bean class="org.archive.modules.deciderules.TooManyHopsDecideRule">
    <property name="maxHops" value="1" />
</bean>
 
# SURTs (URLs normalizados) para evitar (esse foi um dos arquivos copiados):
<bean class="org.archive.modules.deciderules.surt.SurtPrefixedDecideRule">
      <property name="surtsSourceFile" value="negative-surts.txt">
```

Ronomeie e edite este arquivo:

```
$ mv hq-beans.xml.0 hq-beans.xml
$ vi hq-beans.xml
```

```
Comente os beans uriUniqFilter, hqclient e hqpersist:
```

```
<!--
<bean id="uriUniqFilter"
      class="org.archive.modules.hq.OnlineCrawlMapper">
  <property name="nodeNo" value="0"/>
  <property name="totalNodes" value="4"/>
  <property name="discoveredBatchSize" value="500"/>
  <property name="feedBatchSize" value="80"/>
  <property name="localUniqFilter">
    <bean class="org.archive.crawler.util.BloomUriUniqFilter">
      <property name="bloomFilter">
        <bean class="org.archive.util.BloomFilter64bit">
          <constructor-arg value="250000000"/>
          <constructor-arg value="30"/>
        </bean>
      </property>
    </bean>
  </property>
</bean>
-->
 
<!-- headquarter client adapter used by HttpPersistProcessor and OnlineCrawlMapper -->
<!--
<bean id="hqclient" class="org.archive.modules.hq.HttpHeadquarterAdapter">
</bean>
-->

<!-- finished submitter to be included in disposition chain -->
<!--
<bean id="hqpersist" class="org.archive.modules.hq.HttpPersistProcessor">
</bean>
-->
```

### Executar o rastreamento

Se tudo for feito corretamente, isso deve funcionar:

```
$ ./startup.sh
```

Agora, um painel da web estará acessível na porta 9443 no mesmo host (obtenha o nome e a senha de autenticação pelo ```ps -ef | grep java```):

{height="250"}

Clique no link **test_config**, depois no botão azul **build**, no topo da página. Deve dizer "Job Is Ready". Depois, clique no botão azul **launch**. Ele deve exibir uma sequência de strings de status depois de "Job Is Active": `NASCENT`, depois `PREPARING` e, em seguida, `PAUSED`. Clique no botão azul `unpause` para (finalmente!) iniciar o rastreamento. 

A função de pausa (pause) depende das definições de configuração padrões como `True`:

```
<!-- <property name="pauseAtStart" value="true" /> -->
<!-- <property name="pauseAtFinish" value="true" /> -->
```

### Verificar o status do rastreamento

Atualize seu navegador para atualizar o painel:

{height="250"}

Isso mostra quantos URLs foram processados e quantos estão pendentes, taxas de processamento de URL, status do thread e outros dados.

### Arquivos de saída WARC

Arquivos WARC são escritos, por padrão, no subdiretório `warcs`. Cada WARC recebe cerca de um gigabyte de dados antes de outro ser criado:

```
$ ls -l warcs
total 41005888
-rw-r--r-- 1 blub users 1161204614 Jun  9 21:37 MCLOUD-20160609210332357-00000-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1064202832 Jun  9 21:56 MCLOUD-20160609210332359-00001-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1520977725 Jun  9 21:52 MCLOUD-20160609210332360-00002-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1011777223 Jun  9 21:24 MCLOUD-20160609210332361-00003-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1000574144 Jun  9 21:52 MCLOUD-20160609210332362-00004-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1000005845 Jun  9 21:51 MCLOUD-20160609210332363-00005-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1000015034 Jun  9 22:15 MCLOUD-20160609212455312-00006-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1000020459 Jun  9 22:36 MCLOUD-20160609213753231-00007-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1399930055 Jun  9 22:46 MCLOUD-20160609215107987-00008-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1292349285 Jun  9 23:37 MCLOUD-20160609215207090-00009-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1000039653 Jun  9 23:32 MCLOUD-20160609215226776-00010-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1000014926 Jun  9 22:29 MCLOUD-20160609215649483-00011-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1000021659 Jun 10 01:31 MCLOUD-20160609221555291-00012-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1000055761 Jun 10 02:23 MCLOUD-20160609222931584-00013-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1000005175 Jun 10 02:18 MCLOUD-20160609223645636-00014-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 2119870356 Jun 10 00:56 MCLOUD-20160609224628152-00015-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1462329740 Jun 10 03:29 MCLOUD-20160609233227602-00016-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1551420349 Jun 10 01:45 MCLOUD-20160609233722737-00017-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1000022245 Jun 10 05:31 MCLOUD-20160610005626706-00018-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1704015803 Jun 10 05:38 MCLOUD-20160610013124232-00019-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1511938554 Jun 10 02:58 MCLOUD-20160610014557756-00020-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1364297055 Jun 10 08:02 MCLOUD-20160610021836348-00021-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1013434840 Jun 10 08:21 MCLOUD-20160610022319581-00022-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1815742049 Jun 10 06:20 MCLOUD-20160610025853439-00023-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1000058523 Jun 10 05:40 MCLOUD-20160610032921930-00024-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1096104506 Jun 10 10:18 MCLOUD-20160610053126093-00025-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1325952334 Jun 10 09:10 MCLOUD-20160610053804044-00026-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1004554334 Jun 10 15:06 MCLOUD-20160610054041702-00027-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1006686502 Jun 10 07:13 MCLOUD-20160610062044379-00028-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1262064822 Jun 10 09:47 MCLOUD-20160610071347444-00029-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1346247746 Jun 10 12:31 MCLOUD-20160610080214994-00030-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users 1000015074 Jun 10 18:00 MCLOUD-20160610082131840-00031-5421~blub-dev.us.archive.org~9443.warc.gz
-rw-r--r-- 1 blub users  936592729 Jun 10 18:11 MCLOUD-20160610091017844-00032-5421~blub-dev.us.archive.org~9443.warc.gz.open
-rw-r--r-- 1 blub users  832670771 Jun 10 18:11 MCLOUD-20160610094801606-00033-5421~blub-dev.us.archive.org~9443.warc.gz.open
-rw-r--r-- 1 blub users  670126053 Jun 10 18:11 MCLOUD-20160610101843380-00034-5421~blub-dev.us.archive.org~9443.warc.gz.open
-rw-r--r-- 1 blub users  337385272 Jun 10 18:11 MCLOUD-20160610123108719-00035-5421~blub-dev.us.archive.org~9443.warc.gz.open
-rw-r--r-- 1 blub users  162329199 Jun 10 18:11 MCLOUD-20160610150641246-00036-5421~blub-dev.us.archive.org~9443.warc.gz.open
-rw-r--r-- 1 blub users   14747939 Jun 10 18:11 MCLOUD-20160610180030443-00037-5421~blub-dev.us.archive.org~9443.warc.gz.open
```

### Adicionar WARCs ao Archive

Primeiro, você precisa de uma coleção para abrigar seus WARCs. Se você precisar de uma nova, peça a alguém (atualmente Jeff ou Alexis) para fazer uma para você. Em seguida, edite `dtmon.cfg` com valores para (pelo menos) `crawljob`, `job_dir`, `xfer_dir`, `description`, `operator` e `collections`:

```
# dtmon.cfg  - draintasker "simple" config file
#
# * params must begin in col1, so preceding by anything
#   effectively causes the parameter to be ignored
# * this file is read everytime it is needed, so your
#   changes effect existing processes
# * please be verbose in your description_effort
#   palavras-chave necessárias: CRAWLHOST, CRAWLJOB, START_DATE, END_DATE
#
# siznax 2010

crawljob:           blub_test
job_dir:            warcs
xfer_dir:           sink

sleep_time:         300
max_size:           10
# WARC_naming      1 # {TLA}-{timestamp}-{serial}-{fqdn}.warc.gz
WARC_naming:        2 # {TLA}-{timestamp}-{serial}-{PID}~{fqdn}~{port}.warc.gz
block_delay:        120   # blocking
max_block_count:    120   # total blocking time = delay x count
retry_delay:        2400 # non-blocking

# ALL CAPS strings in the description below are substituted:
description:        Internet Archive crawldata from Media Cloud, captured by CRAWLHOST:CRAWLJOB from START_DATE to END_DATE.
 
# Operator's email
operator:           blub@archive.org
 
# These collections need to be precreated:
collections: # may have multiple, each on its own line
  - mediacloud

title_prefix:       Webwide Crawldata
creator:            Internet Archive
sponsor:            Internet Archive
contributor:        Internet Archive
scanningcenter:     sanfrancisco

derive:          1
compact_names:   1# Same as the parent directory:
```

Agora, execute o `draintasker` para obter seus WARCs de saída em coleções de petabox. Há alguns documentos disponíveis sobre draintasker, porém confusos: 

(Draintasker)[https://webarchive.jira.com/wiki/spaces/BOT/pages/4620451/Draintasker+Documentation]
(Draintasker Definitions([https://webarchive.jira.com/wiki/spaces/BOT/pages/6423168/Draintasker+Definitions] (definições)
(Draintasker Use Cases)[https://webarchive.jira.com/wiki/spaces/BOT/pages/6423170/Draintasker+Use+Cases] (casos de uso)
(Draintasker Config)[https://webarchive.jira.com/wiki/spaces/BOT/pages/6423157/Draintasker+Config] (configuração)
(Draintasker Execution)[https://webarchive.jira.com/wiki/spaces/BOT/pages/6423161/Draintasker+Execution] (execução)
(Draintasker Operations)[https://webarchive.jira.com/wiki/spaces/BOT/pages/6423172/Draintasker+Operations] (operações)
(Draintasker Processing)[https://webarchive.jira.com/wiki/spaces/BOT/pages/6423164/Draintasker+Processing] (processamento)
(Draintasker Output)[https://webarchive.jira.com/wiki/spaces/BOT/pages/6423166/Draintasker+Output] (saída)

Alguns desses documentos, além de algumas tentativas e erros, revelaram as etapas a seguir.

Primeiro, crie um arquivo `~/.ias3cfg`. Obtenha os valores de `access_key` e `secret_key` do seu arquivo `~/config/internetarchve.yml`. (Não use aspas nos valores das linhas de *key*).

```
access_key = ...
secret_key = ...
```

Depois:

```
$ cd /1/crawling/blub_test/site
$ touch warcs/DRAINME
$ ../draintasker/dtmon.py -L dtmon.log dtmon.cfg &
```

Os arquivos são retirados do diretório `warc` e processados no diretório `sink`.

### Solução de problemas

Arquivos de Entrada

`blub_test.cxml` - arquivo de configuração de rastreamento do heritrix
`dtmod.cfg` - arquivo de configuração do draintasker
`hq-beans.xml` - heritrix ?
`negative-surts.txt` - tenta evitar o rastreamento, por algum motivo
`seeds.txt` - arquivo de seed de URL

Arquivos de saída

`negative-surts.dump` -
`reports/frontier-summary-report.txt` -
`warcs/*.warc.gz.open` -
`warcs/*warc.gz` -

Arquivos de log

`job.log` -
`logs/alerts.log` -
`logs / crawl.log` -
`logs / nonfatal-errors.log` -
`logs / progress-statistics.log` -
`logs / runtime-errors.log` -
`logs / uri-errors.log` -

## Desenvolvimento

### Notas sobre o desenvolvimento H3 para operadores de rastreamento

Mudanças mais recentes, com notas sobre impacto/uso, para os operadores que seguem as versões oficiais de testes e compilações de desenvolvimento de troncos SVN.

###### Ponto de verificação

A funcionalidade de ponto de verificação do H1/H2 foi atualizada e aprimorada para funcionar com o H3. Mais notavelmente: um ponto de verificação agora pode ser acionado sem exigir uma pausa completa no rastreamento.

O principal objetivo dessa funcionalidade é capturar uma imagem consistente do estado de um rastreamento em um determinado momento, permitindo que uma futura reinicialização do rastreamento comece exatamente nesse ponto. Essa reinicialização pode ocorrer após um relançamento da JVM, reparo ou atualização de hardware, migração do estado de rastreamento para uma máquina diferente, alteração da configuração de rastreamento ou mesmo (dadas algumas limitações em quanto as classes podem alterar) alterações significativas no software de rastreamento.

Para ativar o ponto de verificação, sua configuração de rastreamento deve conter um bean "org.archive.crawler.framework.CheckpointService" (agora incluído em perfis agrupados). Esse bean pode ser configurado com uma configuração de 'checkpointIntervalMinutes' inteiro, para ativar pontos de verificação automáticos nesse intervalo uma vez que o rastreamento é iniciado. (O intervalo padrão, -1, significa 'nenhum ponto de verificação automático'.)

Para acionar um ponto de verificação manualmente, a página de tarefa da interface do usuário da web inclui um botão de 'ponto de verificação' que solicita um ponto de verificação imediatamente, com o rastreamento pausado ou não. (O método requestCrawlCheckpoint() do CheckpointService pode ser executado programaticamente.)

Por padrão, cada ponto de verificação cria, no mínimo, um novo diretório dentro do diretório 'checkpoints' configurado pelo CheckpointService. Este diretório tem um nome no formato "cp[número-sequencial] - [14-dígitos-timestamp]", por exemplo "cp00003-20091120103609".

Durante um ponto de verificação, todos os beans que marcam seu interesse no processo de ponto de verificação implementando a interface Checkpointable são instruídos a salvar seu estado relevante. (Observe que somente beans 'top-level', no jargão Spring, podem participar de pontos de verificação.) Beans com uma pequena quantidade de estado podem usar métodos utilitários (na classe Checkpoint) para salvar seu estado no diretório principal do ponto de verificação. Os beans com uma quantidade maior de estado, como aqueles apoiados por grandes estruturas em disco, podem armazenar seus estados de outras maneiras personalizadas, que evitam escrever ou copiar dados duplicados. Até agora, isso só acontece com os beans que usam os serviços BdbModule, que dependem do ponto de verificação customizado do BdbModule.

(O BdbModule armazena seu estado salvo pelo ponto de verificação nos subdiretórios do checkpoint dentro do diretório de ambiente preexistente. Cada diretório contém um manifesto listando exatamente os arquivos de log '.jdb' necessários para reconstruir o estado BDB no momento do ponto de verificação. O comportamento normal do BdbModule nunca exclui arquivos '.jdb' obsoletos, apenas os renomeia para '.del'.)

Cada ponto de verificação rotaciona todos os logs, renomeando o log atual para incluir o nome 'cp#####' como um sufixo, iniciando um novo log. Assim, a visualização de log padrão na interface do usuário mostrará os logs vazios imediatamente após um ponto de verificação, e a exibição de linhas de log mais antigas exige a solicitação do arquivo de log mais antigo por nome. Cada ponto de verificação também fecha qualquer ARCs/WARCs em andamento, resultando em ARCs/WARCs menores para os que estão em andamento no momento do ponto de verificação.

O ponto de verificação visa reproduzir o status da frontier (filas/já visto) e as estatísticas de rastreamento em execução, que "alimentam" relatórios perfeitamente, juntamente com qualquer outro componente que estados personalizados podem escolher salvar. Os processadores no 'FetchChain' podem salvar estatísticas de execução levemente inconsistentes *a menos que* o rastreamento seja pausado, pois é possível continuar o progresso entre os processadores da cadeia de busca durante um ponto de verificação sem pausa. (Se forem exigidos pontos de verificação perfeitamente consistentes desses processadores, pause o rastreamento completamente primeiro.) O ponto de verificação *não* procura, especificamente, salvar o estado que (1) vem diretamente da configuração de rastreamento; (2) já foi capturado em logs ou arquivos ARCs/WARCs e não é consultado novamente durante um rastreamento contínuo; ou (3) expiraria se o ponto de verificação fosse retomado em uma data arbitrária posterior. Ou seja, os resultados das buscas de DNS e robots não são salvos no ponto de verificação e a retomada do ponto de verificação, mesmo segundos depois, resultará na atualização automática dos dados buscados como necessários (como se o rastreamento tivesse sido iniciado novamente ou o ponto de verificação tivesse sido iniciado muito tempo depois que os dados atuais expiraram).

Portanto, para fazer backup ou migrar completamente um ponto de verificação de tarefa, inclua entre os arquivos movidos ou de backup: (1) arquivos de configuração (cxml ou qualquer outro arquivo como seeds ou listas SURT referentes); (2) os diretórios 'cp##### -##############' (com conteúdo completo) dos pontos de verificação de interesse, seja no diretório 'checkpoints' ou nos diretórios de ambiente BdbModule; (3) todas as referências de logs '.jdb' (ou suas versões renomeadas '.del') pelos pontos de verificação de interesse.

Para acionar uma recuperação de ponto de verificação, o ponto de verificação designado deve fazer parte da configuração de rastreamento antes dos estágios de inicialização 'compilar' (build) ou 'iniciar' (launch). (Cada componente Checkpointable percebe em seu método Lifecycle start() se é uma recuperação a partir de um ponto de verificação ou uma inicialização a partir do zero e pré-carrega seu estado conforme apropriado.) Na interface do usuário da web, se uma tarefa é construída e o CheckpointService reporta diretórios Checkpoint de aparência válida em seu diretório 'checkpoints', uma caixa de seleção oferecerá uma opção de pontos de verificação por nome. Selecionar um desses pontos antes de escolher 'launch' fará com que o lançamento comece a partir de uma retomada de ponto de verificação (programaticamente, o método CheckpointService.setRecoveryCheckpointByName pode ser usado para ativar um desses pontos de verificação descobertos).

Como alternativa, um bean 'org.archive.checkpointing.Checkpoint', configurado com a propriedade 'checkpointDir' adequada, pode ser declarado na configuração de rastreamento e será conectado automaticamente por Spring a um componente Checkpointable antes do lançamento/inicialização() para indicar um ponto de verificação de recuperação.

###### Reagendamento fixo

Um recurso (protótipo) para forçar URIs a serem reagendados em um intervalo de tempo fixo após cada busca está disponível. O recurso opcional, ReschedulingProcessor, pode ser adicionado como um pós-processador no DispositionChain, após o DispositionProcessor.

O ReschedulingProcessor tem uma configuração, 'rescheduleDelaySeconds'. Quando esse valor é <= 0, nenhum reagendamento ocorre (comportamento tradicional). Para valores maiores que 0, um tempo de reagendamento na mesma quantidade de segundos que o valor, no futuro, será calculado e gravado no CrawlURI; a frontier, em seguida, lembra do URI e organiza para que ele seja re-enfileirado o mais rápido possível após esse tempo, desde que o rastreamento ainda esteja em execução. (A rapidez com que o URI é repetido depende dos outros URIs já enfileirados no momento.)

A configuração 'rescheduleDelaySeconds' pode ser sobreposta com valores alternativos baseados em SURTs ou DecideRules usando o mecanismo de Planilhas, permitindo a experimentação com diferentes taxas de reagendamento para URIs em hosts diferentes ou correspondendo a padrões diferentes. (Além disso, o ReschedulingProcessor pode ser substituído por políticas personalizadas arbitrariamente complexas para calcular o intervalo de reagendamento.)

###### MigrateH1to3Tool

Um utilitário para auxiliar na criação de uma configuração de rastreamento do H3 em funcionamento, a partir de uma configuração do Heritrix 1.X, está disponível na classe org.archive.crawler.migrate.MigrateH1to3Tool.

A classe tem um main() para ser executado de forma autonoma. São necessários dois argumentos: um caminho para um H1 order.xml e um caminho para um diretório desejado onde o equivalente H3 deve ser construído.

Inicialmente, ele funciona em todas as configurações globais de valor simples do perfil de rastreamento padrão fornecido com o Heritrix 1.14.3 - copiando-as para uma configuração de modelo H3 em funcionamento como  'subreposições' de sintaxe de properties-map. Para outras configurações personalizadas de rastreamento global, um log do que não pode ser aplicado ao modelo H3 será registrado, para que a intenção dessa configuração possa ser manualmente portada.

Os scripts de inicialização 'bin/heritrix' (ou 'bin\heritrix.cmd', no Windows) incluídos podem ser usados para iniciar outras classes, como MigrateH1to3Tool, com todas as bibliotecas necessárias no caminho de classe (classpath), fornecendo o nome da classe alternativa em uma variável de ambiente CLASS_MAIN e, quando apropriado, solicitando operação de primeiro plano com uma variável "true" de ambiente FOREGROUND. Por exemplo, de um shell bash dentro do diretório 'heritrix-3.0.0-SNAPSHOT':


**Uso**

```
MigrateH2to3Tool - takes a H1 order.xml and creates a similar H3 job directory
Usage:
  $CLASS_MAIN $FOREGROUND bin/heritrix sourceOrderXmlFile destinationH3JobDir
where
  CLASS_MAIN          = org.archive.crawler.migrate.MigrateH1to3Tool
  FOREGROUND          = true
  sourceOrderXmlFile  = H1 order.xml
  destinationH3JobDir = H3 job directory (to be created)
```

**Exemplo**

```
$ cd $HERITRIX_HOME
$ CLASS_MAIN=org.archive.crawler.migrate.MigrateH1to3Tool\
  FOREGROUND=true bin/heritrix\
  ../heritrix-1.14.3/jobs/myoldjob-20090214002857765/order.xml\
  jobs/myH3job
```

Futuras evisões incluirão suporte para subreposições de configurações baseadas em nome de host e maior variedade de personalização na escolha de Escopo, Processor e classes de implementação na configuração H1 original.

###### Cadeia de processadores dividida em 3; LinksScoper/FrontierScheduler apagado

Veja [HER-1605] e Detalhes do Design de Separação da Frontier para detalhes. Motivações:

* tratar 'candidatos' que estão sendo analisados/colocados em escopo antes de programar a flexibilidade da mesma cadeia de processadores
* dividir a antiga cadeia de processamento em uma primeira parte, cujo trabalho em andamento sempre pode ser interrompido ou ignorado para fins de verificação, e uma segunda parte, que sempre completa atomicamente (agendando links externos e registrando o status final de um URI testado) em relação a pontos de verificação - para permitir uma verificação instantânea melhorada.
* fornecer locais para que várias tomadas de decisão/cálculos orientados por políticas ocorram fora da frontier sobrecarregada, aumentando a taxa de transferência

O que era feito pelo LinksScoper + FrontierScheduler agora acontece dentro de um novo processador do CandidatesProcessor, que envia cada outlink candidato através de um CandidateChain configurável de outros processadores. Essa CandidateChain inclui, atualmente, um processador CandidateScoper, que faz uma verificação de escopo de um único outlink e um FrontierPreparer, que prepara os URIs que passaram pela verificação de escopo para o agendamento da frontier. (O agendamento real ou promotion-to-seed acontece no CandidatesProcessor.)

Consulte o profile-crawler-beans.cxml padrão incluído no SVN para obter detalhes sobre a nova configuração recomendada das três cadeias de processador.

###### Milhões de seeds: ok

Os últimos lugares - principalmente na criação de relatórios - em que um rastreamento com (dezenas ou centenas de) milhões de seeds pode tentar usar uma quantidade ilimitada de RAM foram eliminados. Não deve haver problemas ao iniciar um rastreamento com milhões de seeds ou alimentar milhões de URIs extras a um rastreamento já iniciado (como o recurso ActionDirectory descrito abaixo).

###### Lista de seeds e instruções de suporte '-' de SurtPrefixedDecideRule

O bean que carrega o rastreamento com seeds agora anuncia linhas sem seeds (como aquelas que iniciam com '' ou '') para qualquer bean que ele saiba ser um SeedListener. (*Autowiring* de configuração significa que qualquer bean nomeado que implementa o SeedListener receberá tais anúncios.) Um SurtPrefixedDecideRule cuja decisão é ACCEPT considerará todas as linhas " como diretivas potenciais de "add-SURT-prefix" (adicionar prefixo SURT); Um SurtPrefixedDecideRule cuja decisão seja REJECT considerará todas as linhas " como diretivas potenciais de "add-SURT-prefix" (adicionar prefixo SURT).

O perfil padrão incluído contém uma segunda SurtPrefixedDecideRule, posterior, inicialmente vazia de todos os prefixos, que pode ser preenchida por esse mecanismo (ou outras opções de configuração) para URIs do Scope-REJECT.

Consulte o profile-crawler-beans.cxml padrão incluído no SVN para obter detalhes sobre as novas regras recomendadas de inicialização de escopo.

###### Lista de seeds SURT-hinting mudou

Em versões anteriores do Heritrix, se um seed sem caminho terminava ou não com um '/' (como "http://example.com/" ou "http://example.com") fazia diferença em qual prefixo SURT era implicado pelo seed. Especificamente, a presença do '/' significava 'exatamente este nome de host' (prefixo SURT "http://(com,example,)/") enquanto a ausência significava "este domínio e qualquer subdomínio" (prefixo SURT "http"://(com,exemplo,").

Isso era, estritamente, uma sutileza inventada pelo Heritrix; em ambos os casos, os URIs são estritamente idênticos no nível do protocolo HTTP ou nas regras de interpretação do URI.

Uma mudança na forma como os seeds são lidos e agendados para busca significa que a falta de um '/' é normalizada antes que as regras de escopo possam usar sua presença ou ausência como uma sugestão de interpretação. Assim, no H3, a semente "http://example.com" é equivalente em sua implicação SURT para "http://example.com/". O prefixo SURT mais antigo e permissivo antigo ainda pode ser adicionado por meio de uma diretiva "+" explícita no texto da lista de seeds.

###### ActionDirectory para o carregamento de URIs pós-lançamento

Uma classe de bean ActionDirectory, se presente em uma configuração de rastreamento (e é recomendável que se torne parte de todas as configurações padrão), observa um diretório 'action' configurado para quaisquer arquivos que apareçam (confirmando um intervalo configurável, padrão 30 segundos). Para cada arquivo, uma ação é executada de acordo com o sufixo do arquivo e, em seguida, o arquivo é movido para um diretório 'done'.

Um arquivo que termina com ".seeds" acionará a adição de mais seeds. Um arquivo que termine '.recover' será tratado como um log de recuperação tradicional - com todas as linhas 'Fs' consideradas incluídas (para suprimir um novo rastreamento) e todas as linhas 'F+' reagendadas. Um arquivo que termine com '.include', '.schedule' ou '.force', respectivamente, será tratado como se fosse um formato de log de recuperação (com tag de prefixo de 3 caracteres por linha), mas todos os URIs listados (independentemente da tag de prefixo) serão considerados incluídos, programados ou programados por força, respectivamente.

Todos esses arquivos podem ser compactados com gzip (com uma extensão '.gz') e aqueles no formato de log de recuperação podem ter '.s'. inserido antes do sufixo funcional (por exemplo, 'frontier.s.recover.gz') para indicar que, antes de outras etapas, o escopo deve ser tentado em relação aos URIs incluídos.

Colocar os arquivos apropriados (possivelmente filtrados) nesse diretório será, provavelmente, a maneira recomendada de recuperar o estado da frontier anterior ao rastreamento ou de realizar adições em massa a um rastreador em execução.

###### Mudanças no log de recuperação

O log de recuperação, anteriormente denominado 'recover.gz', agora é chamado de 'frontier.recover.gz' para enfatizar que ele registra apenas informações da frontier e para tornar '.recover' um sufixo (dotted-sufix) adequado para que outras ferramentas reconheçam arquivos neste formato.

###### A configuração 'sourceTagSeeds' foi movida da frontier para o SeedModule

Como o SeedModule agora tem a responsabilidade de criar seeds do CrawlURIs e anunciar sua existência para todos os consumidores (SeedListeners), esta configuração para pré-configurar seeds com uma tag de seu hostname, que é herdada por URIs *discovery-descendant*, foi movida para o SeedModule.

###### Padrão CrawlController 'pauseAtStart' agora true

A não ser que seja especificado o contrário, agora é padrão que as tarefas iniciam no modo pausado - dando uma chance de examinar o estado, carregar URIs, etc.

Os operadores podem "despausar" manualmente as tarefas através da interface da web ou adicionar a subreposição 'crawlController.pauseAtStart=true' à sua configuração.

###### QueueAssignmentPolicies: definições de 'parallelQueues' e 'deferToPrevious' 

SurtAuthorityQueueAssignmentPolicy e HostnameQueueAssignmentPolicy agora derivam de uma nova superclasse compartilhada, URIAuthorityBasedQueueAssignmentPolicy. Duas novas configurações estão disponíveis nesta superclasse:

parallelQueues: valor padrão (e comportamento histórico) é '1'. Se, em vez disso, for N, todos os URIs que anteriormente iam para uma mesma fila nomeada entrarão em filas relacionadas à N (por meio de um mapeamento hash consistente da parte path?query do URL). Cada fila é analisada separadamente para cortesia tradicional com base em conexões um por vez e snooze-delays-between-fetches - portanto, as filas N significam que as pesquisas podem estar em andamento em um site de uma só vez. Assim, só deve ser usado em uma configuração de sobreposição, aplicada a sites com probabilidade de manipular várias conexões bem.

deferToPrevious: o valor padrão é 'true'. comportamento histórico era "false". Se for true, quando um URI for atribuído a uma fila, ele não será verificado novamente para atribuição a outra fila quando ele sair da fila - a atribuição anterior é fixa. Por exemplo, se alterar a 'parallelQueues' para um N maior durante o rastreamento, o 'deferToPrevious' significa que os URIs antigos não serão recuperados entre as filas de N, apenas os novos URIs serão distribuídos dessa maneira.

### Notas de desenvolvimento

A maior parte dos esforços de desenvolvimento, no momento, é direcionada para a linha Heritrix3. Informações gerais sobre o desenvolvimento 3.x estão disponíveis no Heritrix3. Um tema importante dos lançamentos 3.x possibilitará o rastreamento adaptável e contínuo revisitado em larga escala. Os próximos trabalhos em direção a esse objetivo incluirão:

* refatoração e, possivelmente, uma combinação da estrutura interna de dados com estruturas de dados já incluídas; melhorando a flexibilidade das filas frontier (disponibilizando a possibilidade de filas com tempo de longa duração e várias filas por host/grupo de exclusão)

* permissão de revisita dos URIs descobertos de acordo com uma política de swappable, que pode levar em consideração os intervalos de revisita desejados e as taxas de alteração de URI detectadas em visitas anteriores

Outras áreas de foco, no futuro, embora ainda não agendadas para versões específicas, incluem:

* melhorar a usabilidade e a documentação de recursos recém-adicionados (supressão de duplicação; priorização ajustável) em fluxos de trabalho típicos do operador

* melhorar a cobertura de teste automatizada com rastreamento simulado, especialmente para configurações de recursos não padrões e execuções de teste mais longas e com maior desempenho

* melhor rastreamento de conteúdo de vídeo da Web com configurações padrões

* uma interface de serviços da Web como alternativa ao JMX para controle remoto do rastreador

* novas heurísticas e compartilhamento de conhecimento para redução de armadilhas/spam

* relatórios aprimorados durante a fila de rastreamento, incluindo previsões melhores do tempo de conclusão

* opções aprimoradas para rastrear sites de acesso controlado (senha/outra autenticação)

## Visão Geral de (Re)Rastreamento Contínuo

* Visão Geral de (Re)Rastreamento Contínuo
     * Cenário genérico
     * Cenário Alvo Concreto
     * Estratégia de Design
     
* Notas de design da Fase A de (re)Rastreamento contínuo
     * Frontier Unbundling Design Details
     * Springified Heritrix Design Details
     * Streamlined Checkpointing Design Details

* Notas de design da Fase B de (re)Rastreamento contínuo

* Notas de design da Fase C de (re)Rastreamento contínuo

### Cenário genérico

Em termos gerais, esta é a principal capacidade que queremos adicionar nesta fase de 'rastreamento contínuo' do desenvolvimento do Smart Crawler.

* capacidade de rastrear X mil sites (0 <X <100)
* sites/URIs  divididos em grupos N (principalmente, por domínio; possivelmente, por URI-padrão/discovery-path /etc.)
* cada grupo tem um intervalo mínimo de visita e intervalo de visita máximo nos destinos

O objetivo dos rastreadores é visitar as páginas de um site não mais frequentemente do que cada intervalo mínimo de visita, mas não menos frequentemente do que o intervalo mínimo.

A taxa real entre esses limites deve ser baseada nas taxas de mudança observadas - deduzidas a partir de conteúdo e cabeçalhos.

### Cenário Alvo Concreto

O teste de rastreamento realístico e de vários meses que pretendemos como o uso principal (e o local de teste primário) do Rastreamento Contínuo será uma variante do cenário genérico com esses parâmetros adicionais:

* 50K sites escolhidos: 45K 'gerais' e 5K 'intensos'
* sites gerais: min-visita 1 semana max-visita 3 meses
* sites intensos: min-visita 1 dia max-visita 1 mês
* sobreposições: tipos de arquivos 'geralmente estáticos': min-visita 1 mês, max-visita 6 meses

### Estratégia de design

Pretendemos dividir o trabalho de design e implementação em três fases amplas e, de certa forma, sobrepostas:

Os objetivos da fase A serão resolver as preocupações pendentes com o sistema de definições/configurações 2.x e aprimorar os pontos de verificação para suportar plausivelmente rastreamentos contínuos de duração arbitrária, mesmo que eles precisem reiniciar com um software significativamente atualizado. Essas mudanças culminarão no lançamento oficial 3.0. Notas sobre o design da fase A de Rastreamento Contínuo.

Os objetivos da fase B serão adicionar novos recursos às filas Frontier, estrutura já conhecida, e armazenamento de histórico de URI para suportar vários tipos de rastreamento. Essas alterações aparecerão nas primeiras versões de teste do 3.2. Notas sobre o design da fase B de Rastreamento Contínuo.

Os objetivos da fase C serão implementar um conjunto mínimo de políticas de revisão viáveis e acompanhar o trabalho da interface do usuário para dar suporte aos cenários de uso genéricos e concretos. Além disso, garantir a estabilidade do trabalho durante muitos meses de rastreamento, em combinação com os recursos anteriores da fase do Smart Crawler. Essas alterações estarão totalmente disponíveis na versão final 3.2. Notas sobre o design da fase C de Rastreamento Contínuo.

## Notas de design da fase A de (re)rastreamento contínuo

## **Notas de design da fase A: Definições e atualizações de pontos de verificação**

Esperamos que a fase A termine em uma versão 2.2 independente.

### Definições/Configurações

A configuração do Heritrix 2.0...

* é modelada a partir do framework Spring de maneira significativa
* não adotou o framework Spring diretamente devido a preocupações de que ele não suportaria nossa necessidade de substituir ambos valores e implementar classes para grupos de URIs
* ainda não oferece uma maneira fácil/automatizada de trazer as configurações da versão 1.x
* usa um formato personalizado, em disco, sensível a pedidos, que gerou preocupação no feedback inicial
* deixa sobreposições de mapeamentos (URIs-> configurações especiais) em um banco de dados BerkeleyDB de difícil arquivamento

Objetivo: mudar para uma configuração baseada no Spring 2.5

* faz com que a configuração do rastreador encaixe em um modelo padrão para conectar componentes alternativos e fornecer valores de configuração
* fornece um formato XML bem caracterizado para configuração

Questão: suporte de sobreposições

* Escopos personalizados e retornos de chamadas do Spring devem permitir a inserção de um nível extra de indireção (wrapper) em componentes e valores primitivos

* A resolução de valores/componentes seria adiada para o tempo de execução, e a consulta de sobreposições de mapeamentos aconteceria primeiro

Questão: configuração arquivável

* mapeamentos e valores sobrepostos alternativos também seriam especificados no modelo/sintaxe Spring
* podem ser incluídos no arquivo de configuração principal; outros podem ser incluídos por referência
* resultado: configuração de qualquer complexidade *pode* ser representada como um único arquivo grande - muito fácil de arquivar 
* ou, pode ser representada como uma coleção de arquivos relacionados, a maioria dos quais são componentes padronizados reutilizados por um longo período

Questão: mudança de definições no meio do rastreamento

* será possível, mas por manipulação direta de beans do componente do rastreador
* implicação: a configuração inicial precisará ser alterada manualmente e separadamente
* para investigar: maneiras de tornar isso mais fácil/menos propenso a erros

Questão: interface do usuário para compor a configuração do Spring

* oferecer edição de texto com opção de validação de formato/dependência
* oferecer modos de edição guiados (campos com texto de ajuda, XML como modelo)

Questão: ferramenta de migração 1.x para 2.x

* aguardará até que as configurações Spring-ified estejam prontas
* trabalhará por configurações simples; fornecerá lista de exceções de problemas que o operador precisa corrigir manualmente no final
* estratégia geral: vá até as configurações 1.x, encontre o manipulador para essa configuração, crie um novo XML.

Ver também: [Detalhes do Design do Heritrix Springified] (https://github.com/internetarchive/heritrix3/wiki/Springified%20Heritrix%20Design%20Details)

### Ponto de verificação

O ponto de verificação do Heritrix, atualmente...

* requer pausa total do rastreador
* depende fortemente de serialização Java, para todo o estado de componente
* muito frágil se o software for alterado antes da restauração (nunca tivemos compatibilidade de ponto de verificação entre os lançamentos, principais ou não)

Objetivo: ponto de verificação mais rápido, mais fácil e mais robusto

* dividir o processamento de URI em duas fases: uma transitória (pode ser descartada enquanto a URI for repetida), e uma que altera estatísticas ou estruturas persistentes (que devem ser concluídas até a consistência antes que o ponto de verificação continue)
* step right after laggy network fetch is threshold between phases
* permitir a retenção de URIs após a busca (rastreador semi-pausado) para que o ponto de verificação possa ocorrer assim que todo o processamento *needing-persistence* for concluído (mas sem esperar que todas as buscas sejam concluídas)
* desenfatizar serialização; executar a maioria dos componentes salvando o estado em um formato de texto solto (JSON ou XML) para facilitar a restauração de código alterado ou a edição manual offline 
* mover atividades que estão apenas copiando o processo externo do rastreador: ou seja, o ponto de verificação é, principalmente, um manifesto de arquivos para restaurar o rastreamento; cabe ao operador copiá-los em outro lugar, se desejar
* componente de ponto de verificação opcional na configuração; se presente, todos os componentes devem restaurar a partir dele.

Ver também: [Detalhes do Design de Ponto de Verificação Simplificado] (https://github.com/internetarchive/heritrix3/wiki/Streamlined%20Checkpointing%20Design%20Details)

### Separação da Frontier

Objetivo: separar o máximo possível, de dentro da Frontier, em processadores independentes que marcam o URI

* geral: deixar a Frontier ser a mais burra possível, apenas siga as instruções dentro das URIs agendadas/terminadas
* decisões atuais na fronteira que podem ser removidas:
      * canonização
      * decisão de disposição (success, retry, failure)
      * precedência de uri
      * atraso de cortesia
      * chave de fila
* frontier lançava erro se a orientação necessária não estivesse presente

Ver também: [Detalhes do Design de Separação da Frontier] (https://github.com/internetarchive/heritrix3/wiki/Frontier%20Unbundling%20Design%20Details)

## Detalhes do Design de Separação da Frontier

## Análise Inicial

### Cadeia de URI descoberto?

Coisas que acontecem a um URI descoberto, atualmente:

* regras de escopo são aplicadas - alguns URIs são rejeitados (LinksScoper)
* URI enviado a Frontier para agendamento (FrontierScheduler)
     * URI canonizado (entro da sincronização de frontier; bottleneck)
     * Valor de precedência de URI atribuído (dentro da sincronização de frontier; bottleneck)
     
Pode haver outros bits de processamento/classificação que podem ocorrer em URIs descobertos mas ainda não buscados, portanto, configurar todas essas etapas em uma nova cadeia configurável faz sentido do ponto de vista da flexibilidade, "desligando" a complexidade a partir do ponto de vista da frontier e diminuindo os afunilamentos serializados.

### Políticas aplicadas a URIs sendo buscados

Pode ser movido para um módulo de cadeia de processamento tardio:

* a decisão de disposição (success, retry, failure) move-se para um módulo dispositiondecision no final da cadeia de processadores
* atraso de cortesia passa para o módulo politenesspolicy na cadeia de processamento

### Três Cadeias de Processamento

Podemos refatorar a configuração de rastreamento para destacar três cadeias de processadores.

Duas são, juntas, análogas ao ProcessorChain existente e se aplicam a URIs que saem da frontier. A primeira cadeia é a 'fetch chain' e inclui todas as etapas que podem ser abortadas/repetidas livremente no caso de pontos de verificação. A segunda cadeia é a 'disposition chain' e qualquer URI que inicie essa cadeia deve finalizá-la, com toda a mutação atendente de filas frontier e estatísticas totais, antes que um ponto de verificação seja tentado. Na disposition chain, o antigo CrawlStateUpdater foi renomeado para DispositionProcessor e, além de sua atualização anterior de robots/estatísticas, agora atualiza o CrawlURI com informações (como atrasos calculados de cortesia) para a consideração da frontier.

A terceira cadeia toma o lugar das etapas que agora ocorrem em LinksScoper e FrontierScheduler e na frontier, para URIs que ainda não foram agendados para uma fila. Essa cadeia pode ser chamada de "candidates chain". Além do escopo, também prepara o CrawlURI com informações pré-calculadas para todas as decisões de frontier - permitindo que essas políticas sejam aplicadas em paralelo, fora dos bloqueios críticos de frontier/managerThread.

### Candidate Chain

A CandidateChain, geralmente, contém apenas dois processadores (embora mais possam ser adicionados):

* CandidateScoper: aplica o escopo ao URI que está sendo processado, configurando seu fetchStatucomo s negativo, caso não deva ser agendado
* FrontierPreparer:
     * calcula a priorização de 'schedulingDirective'
     * calcula o URI canonicalizado
     * calcula a chave da fila de destino
     * calcula o 'custo'
     * calcula a precedência de URI, se houver
     
A Candidate Chain é aplicada a URIs:

* descobertos durante o rastreamento

O FrontierPreparer, no entanto, também está disponível diretamente para a Frontier para ajudar a preparar quaisquer URIs que (ainda) não passam pela CandidateChain, como seeds ou outros URIs adicionados de maneiras diferents no meio do rastreamento.

A CandidateChain é aplicada a cada URI candidato por um novo processador, CandidatesProcessor, que substitui o LinksScoper e o FrontierScheduler.

### Fetch Chain

A FetchChain é a mesma que a primeira parte da cadeia de processamento geral do H1/H2. Inclui processadores que:

* verificam o escopo novamente, se desejado
* verificam/impõem condições prévias
* tentam fazer buscas
* fazem extração de link
* gravam arquivos ARC/WARC

### Disposition Chain

Todas as etapas contidas na Disposition Chain devem ser feitas atomicamente em relação ao ponto de verificação. Normalmente, incluirá apenas dois processadores:

* CandidatesProcessor: substitui o antigo LinksScoper e o FrontierScheduler, executando a candidates chain em cada URI descoberto e, em seguida, agendando (ou aplicando tratamento especial de seeds descobertas) para quaisquer URIs não cancelados por essa cadeia
* DispositionProcessor: o CrawlStateUpdater renomeado, atualizando estatísticas, robots e preenchendo o CrawlURI com decisões/atrasos para a frontier consultar

(Alguns rastreamentos podem querer mover gravações ARC/WARC para a disposition chain, se o risco de um pequeno número de gravações escritas duplicadas após a retomada do ponto de verificação for uma preocupação.)

## Detalhes do Design do Heritrix Springified

### Uso do Spring Core Container / Inversão-de-Controle

Uma configuração de rastreamento é, agora, uma coleção de beans, especificada e inicializada no estilo Spring.

Um arquivo de configuração do Spring nomeado, por convenção, 'crawler-beans.xml' (em vez do usual Spring 'beans.xml') é a principal maneira de especificar um rastreamento. Como tal, substitui o order.xml do Heritrix 1.X e o global.sheet (e possivelmente outras planilhas) do Heritrix 2.0.

Usar recursos do Spring para incluir outros arquivos de configuração por referência, a configuração pode, de fato, ser dividida em vários arquivos, incluindo arquivos padronizados reutilizados em vários rastreamentos (onde eles são idênticos).

O container, por meio de uma subclasse Spring ApplicationContext personalizada especial, pode ser instanciado a qualquer momento, ou muitas vezes (descartando resultados provisórios) ao compor a configuração. Assim, é importante que os componentes não executem ações intensivas em recursos ou destrutivas durante a instanciação/iniciação simples.

Em vez disso, a interface Spring 'Lifecycle' informa aos componentes quando começar (start), e é quando os recursos significativos devem ser alocados/abertos/mantidos (como grandes estruturas na memória, ambientes BDB, arquivos de disco).

### Implementando sobreposições

Todos os valores que podem ser sobrepostos terão acessores Java padrão (tipo bean), de seu tipo natural.

Uma classe com valores substituíveis deve implementar a interface HasKeyedProperties; isso dá acesso a um mapa (do tipo KeyedProperties) que contém todos os valores de bean que podem ser sobrepostos. O suporte para a interface HasKeyedProperties é tão simples quanto:

```
 KeyedProperties kp = new KeyedProperties();
    public KeyedProperties getKeyedProperties() {
        return kp;
    }
```

Uma propriedade com capacidade de sobreposição deve ser armazenada e recuperada do KeyedProperties; qualquer inicialização pode ocorrer em um bloco inicializador estático. Por exemplo:

```
  {
        setMinDelayMs(3000);
    }
    public int getMinDelayMs() {
        return (Integer) kp.get("minDelayMs");
    }
    public void setMinDelayMs(int minDelay) {
        kp.put("minDelayMs",minDelay);
    }
```

Um bean de planilha é declarado na configuração para qualquer sobreposição desejada. Inclui um mapeamento de chaves de estilo string property-path (por exemplo, beanName.property.property) para valores de sobreposição. Um exemplo de planilha:

```
<bean id="eduSheet" class="org.archive.spring.Sheet">
  <property name="overrideProperties">
   <value>
    frontier.minDelayMs=10000
   </value>
  </property>
</bean>
```

Os mapeamentos de prefixos SURT para o nome da planilha a ser aplicada são, semelhantemente, um bean:

```
<bean id="overrideMappings" class="org.archive.spring.SurtToSheetMap">
 <property name="map">
   <map>
    <entry key="http://(edu," value="eduSheet"/>
   </map>
  </property>
```

Uma instância de HesKeyedProperies, ou uma propriedade em si, não reconhece inerentemente seu nome de property-path completo. (Na verdade, pode ter vários desses nomes.) No entanto, antes que uma planilha possa ser usada, ela deve ser 'preparada'. Cada caminho dentro dela é interpretado em relação ao BeanFactory atual. Cada caminho deve resolver para uma propriedade de uma instância que implementa HasKeyedProperties; a instância KeyeProperties está, no momento da preparação, informada do nome do property-path.

Aplicar uma planilha significa empurrá-la para uma pilha ThreadLocal mantida em um campo estático KeyedProperties. Instâncias de KeyedProperties consultam essa pilha de planilhas antes de retornar seu valor interno/padrão.

Uma expressão usual para aplicar uma planilha incluiria operações push e pop emparelhadas. Por exemplo:

```
 try {
  KeyedProperties.pushOverridesMaps(sheet);
  // do stuff
 } finally {
  KeyedProperties .popOverridesMap(sheet);
 }
 ```
 
Uma planilha será automaticamente iniciada na primeira vez que for pressionada. Push/pop incompatíveis irão gerar um evento de log SEVERE; provavelmente um bug sério.

(Inicialmente, somente os mapeamentos de prefixos SURT para sobreposições serão permitidos, mas essa abordagem também deixa em aberto a possibilidade de que conjuntos de DecideRules possam ser usados para determinar se uma planilha de sobreposição deve ser aplicada a um URI específico. Ou, esse código personalizado pode aplicar uma planilha de substituição em qualquer ponto no processamento de URI.)

## Detalhes simplificados de design de pontos de verificação

## Objetivos

### Evitar pausa completa e lenta

* dividir o processamento de URI em duas fases: uma transitória (pode ser descartada enquanto a URI for repetida), e uma que altera estatísticas ou estruturas persistentes (que devem ser concluídas até a consistência antes que o ponto de verificação continue)
* step right after laggy network fetch is threshold between phases
* iniciar um ponto de verificação requer um bloqueio exclusivo em um *frontier-atomic-mutation-lock* que, geralmente, está disponível para vários threads
* permitir a retenção de URIs após a busca (rastreador semi-pausado) para que o ponto de verificação possa ocorrer assim que todo o processamento *needing-persistence* for concluído (mas sem esperar que todas as buscas sejam concluídas)

### Melhorar a velocidade

* usar estruturas em disco que podem ser congeladas/marcadas, em vez de exigirem novas cópias. (por exemplo: um snapshot LVM)
* mover tanto quanto simplesmente copiado o processo exterior do rastreador: o ponto de verificação é, principalmente, um manifesto de arquivos para restaurar o rastreamento; cabe ao operador copiá-los em outro lugar, se desejar

### Melhorar a robustez nas alterações de código

* cada objeto é responsável pelo próprio ponto de verificação
* desenfatizar serialização; executar a maioria dos componentes salvando o estado em um formato de texto solto (JSON ou XML) para facilitar a restauração de código alterado ou a edição manual offline
* componente de ponto de verificação opcional na configuração; se presente, todos os componentes devem restaurar a partir dele
* a fiação é transitória - sempre vem da configuração

## Abordagem geral

Cada componente do rastreador com estados em que é possível fazer ponto de verificação implementará uma interface diferenciada que permita a solicitação do ponto de verificação de seu estado e uma propriedade startState (opcional).

Quando um ponto de verificação é acionado/solicitado, ele gravará o estado do componente no local fornecido (possivelmente incluindo ponteiros para arquivos pré-existentes em andamento que fazem parte de seu estado).

Quando um rastreamento é iniciado, se a propriedade startState estiver configurada, uma carga adicional do disco de seu estado será executada.

## Notas de design da fase B de (re)rastreamento contínuo

## Notas de design da fase B: Histórico de URI e Atualizações de Fila

Ideia geral: expandir os recursos principais da Frontier padrão para que ela possa fazer revisits simplesmente por meio de orientações passadas de componentes externos opcionais.

Objetivo: expandir opções da estrutura já incluída

* mover-se para fora da frontier, se possível
* implementar a versão de lista de fusão estilo Mercator
* adicionar a opção para armazenar o histórico (ou pelo menos a fila/queueKey) na lista de fusão; permite acesso aleatório e eficiente a URIs enfileirados

Objetivo: aprimorar os recursos de enfileiramento:

* adicionar um WorkQueue de tipo DelayedQueue com suporte a disco/BDB que pode ser misturado com filas clássicas; só libera URIs em tempos *future* configurados
* resolução do DelayWorkQueue intencionalmente bruta (~ dia) para evitar inserções aleatórias em todos os lugares
* cada fila clássica se torna a fila 'ASAP' para o mesmo host/etc; uma fila opcional "future" pode ser adicionada
* implementar um processo de exclusão mútua para filas relacionadas (mesmo domínio, mesmo IP) para impedir a emissão simultânea de URIs; adquirir permissões nomeadas, esperar que a permissão fique disponível (FIFO)
* conciliar *budget-rotation* com precedência
* fazer com que o *session-budets* cause promoção/rebaixamento de precedência em vez de "deactivation"; ou
* avaliar a eliminação de *budgets* em favor de políticas de precedência mais sofisticadas e de grande alcance

Objetivo: introduzir heurísticas/previsões de progresso da fila

* coletar estatísticas de progresso nas taxas de descoberta e conclusão
* criar estimativas confiáveis de tempo de conclusão de fila

Objetivo: implementar uma política de *naive toy revisit*

* intervalo de revisita fixo como teste de capacidades de fila/frontier

A fase B provavelmente gerará uma versão inicial do teste de qualidade alfa de uma eventual versão 2.4.

## Notas de design da fase C de (re)rastreamento contínuo

## Notas de design da face C: Definições e atualizações de ponto de verificação

A fase C é a implementação e o teste, por meio de um rastreamento realista, de heurísticas de *revisit* mais sofisticadas.

Objetivo: implementar agendamento de *revisit* mais inteligente

* permitir diferentes tempos mínimos e máximos de *revisit* para sites diferentes e padrões de URL
* implementar crescimento/decaimento exponencial simples em reação à mudança observada
* implementar heurísticas baseadas em cabeçalho HTTP adicionais

Questão: Monitoramento do progresso/cobertura e ajustes do operador

* estender a análise dinâmica da fila para fornecer avisos caso as taxas de visitação desejadas forem inatingíveis
* adicionar interface do usuário para visualizar as taxas de progresso da fila/tempos de conclusão esperados
* fornecer operações de edição/reprogramação de filas em massa

Objetivo: executar 'cenário concreto' por mais 3 meses

* usar para avaliar desempenho e usabilidade

## Ferramenta de conversão de configurações 1.x (objetivo)

* Crawl Order
* Definições
* Escopo de rastreamento
* Diretórios
* De sobreposições para H3 Overlays
* Tarefas concluídas
* Teste e falha 
* Metas e não-metas

Nota: o objetivo agora é oferecer uma ferramenta que tenha configurações que funcionem no 1.14.3 e as converta em 3.0.

### Crawl Order

Deve ser bem direto ao ponto. As configurações que passaram pela maioria das alterações no H3 são as configurações que foram melhor estruturadas no 1.14 order.xml, nas configurações de CrawlOrder e nas configurações do CrawlController. Ao criar um modelo H3 para as migrações 1.14,  mover as configurações de CrawlOrder e CrawlController, por meio de um mapeamento simples, deve ser trivial.

(Uma grande tabela mapeando as porções 1.14 do order.xml para editar, que deve ser feita para um modelo H3 inicial, pode ser suficiente para lidar com quase todos os pedidos simples.)

### Definições

Os nomes das definições de módulo foram alterados, mas de maneira simples e fácil de entender. (Por exemplo, de palavra com hífens para camelCase.) Muitas configurações novas foram adicionadas, mas quase todas são configurações "autowire" para atrair outros beans singleton. Se o modelo de migração incluir beans padrões adequados, as novas configurações não deverão causar/ter problemas.

Os nomes dos pacotes das classes de módulos mudaram significativamente, mas de maneira quase algorítmica. Acho que devemos usar arquivos de propriedades externos para definir mapeamentos de nomes 1.x para nomes 3.x e permitir que os usuários finais especifiquem mapeamentos adicionais no caso de não notarmos algo.

### Escopo de rastreamento

Uma área de preocupação é a classe CrawlScope e suas subclasses, que não existem mais em 3.x. Foi substituído por um DecideRuleSequence genérico, que não pode ter status primário. Podemos criar conjuntos padrões de regras de decisão que se aproximam do comportamento das subclasses legadas do CrawlScope.

### Diretórios

O Heritrix 3 está mais próximo da abordagem 1.x, com várias camadas de relatividade, do que o Heritrix 2. Uma simples cópia de caminhos padrões para as novas configurações análogas pode ser suficiente.

### De sobreposições para H3 Overlays

A migração de sobresições da 1.14 para o H3 apresenta alguns desafios. Na versão 1.14, cada combinação de host/domínio + configuração tinha seu próprio arquivo. Em H3, queremos agregar valores de configuração semelhantes em planilhas e, em seguida, colocar o(s) host/domínios em associações para essa planilha. É um processo simples, supondo que podemos guardar tudo na memória, mas teremos que criar algum algoritmo sensato para nomear as planilhas criadas, já que as sobreposições 1.14 não têm nomes.

As opções do H3 para sobreposições de listas, mapas e módulos devem ser um superconjunto do que era possível na versão 1.14, mas devemos verificar isso novamente.

### Tarefas concluídas

Pode haver preocupação sobre como a ferramenta de migração deve lidar com tarefas concluídas. Elas devem migrar? Os seeds, relatórios, etc. devem ser migrados? Devemos copiar diretórios de estado e arquivos arc do diretório 1.x para o diretório H3? Inicialmente, a ferramenta só deve trazer informações de configuração - de modo que um rastreamento idêntico (na medida do possível) possa ser repetido no H3. A ferramente só deve ser expandida se surgir a necessidade urgente de converter outras porções de tarefas concluídas.

### Teste e falha

Podemos testar a ferramenta com todas as nossas configurações de rastreamento, mas, provavelmente, haverá casos que não encontraremos nesses testes. Não está claro como os erros de conversão devem ser tratados. Poderíamos incluir configurações defeituosas nos arquivos da planilha de saída; a interface do usuário da web notaria e "reclamaria" dessas configurações e, esperamos, facilitaria a intervenção humana. Também poderíamos registrar todas as partes do order.xml/settings.xml que não resultam em uma conversão formulaica. (Mesmo as que o fazem podem exigir um aviso de log que o comportamento não é idêntico.)

# Metas não-metas

É uma meta para qualquer rastreamento com base nos padrões do pacote 1.14.x, incluindo apenas alterações superficiais (alterações em Strings/valores primitivos ou adição/remoção de componentes opcionais comumente usados), fazer conversões sem que ocorra algum problema.

É uma meta para todas as configurações usadas em rastreamentos IA ativos - incluindo rastreamentos de contrato, rastreamentos de domínio nat'l e rastreamentos Archive-It - para fazer conversões sem problemas ou com apenas problemas triviais que são fáceis de corrigir manualmente.

Não é um objetivo dos componentes não utilizados nos rastreamentos IA fazer conversões sem que ocorra algum problemas. (Não temos o conhecimento para avaliar a conversão.) Idealmente, o mecanismo baseado em pesquisa para mapear configurações antigas para novas configurações será flexível o suficiente para que, se os autores originais ou usuários frequentes dessas ferramentas ajudarem, ele possa cobrir esses componentes também. No entanto, pelo menos até que as correções sejam contribuídas, um *error* que explique essas partes da configuração 1.14 não traduzidas é suficiente.

## Configurações de rastreamento Spring

O Heritrix3 agora faz uso do 'Spring Container' (e seu formato de configuração baseado em XML) para montar um rastreamento executável, escolhendo entre implementações e valores alternativos de configurações compatíveis.


Os desenvolvedores acharão útil revisar o capítulo da documentação de referência do Spring para aprender todas as opções fornecidas pelo container e pelo formato de configuração:

[Spring Framework, Chapter 3: The IoC Container] (https://docs.spring.io/spring/docs/2.5.x/reference/beans.html)

Algumas informações importantes para entender esse modelo são:

1. Os aplicativos são grandes agrupamentos de componentes de colaboração e, geralmente, os componentes têm implementações alternativas e intercambiáveis. (No nosso caso, um trabalho de rastreamento executável, com configurações e opções escolhidas, é um aplicativo.)
2. Os arquivos de configuração declaram todos os componentes participantes e, quando necessário, os valores iniciais da atribuição.
3. O 'container' usa o(s) arquivo(s) de configuração, além de outras dicas derivadas dos próprios componentes (como tipos compatíveis e nomes de configurações), para montar todos os componentes com seu estado inicial e referências diretas aos seus colaboradores. Se um componente for necessário (conforme implícito por outros componentes), mas insuficientemente declarado, erros serão lançados.

## Caixa de compilação

As compilações do H1 e do H3 ocorrem automaticamente logo após novos commits em nosso sistema de compilação baseado em [Jenkins] (https://builds.archive.org:1443/). O link para o [Heritrix 3 (pacotes de distribuição)] (https://builds.archive.org:1443/job/Heritrix-3/lastBuild/org.archive.heritrix%24heritrix/) mostra os pacotes padrões distribuíveis de um build.

Compilações também são carregadas no nosso repositório Maven2:

http://builds.archive.org:8080/maven2/org/archive/heritrix/heritrix/


Os nomes dos artefatos da versão de compilação de desenvolvimento começam com o número da versão que eles estão se aproximando e terminam com "-SNAPSHOT". Os produtos reais de compilação de desenvolvimento começam com o número da versão e incluem um registro de data e hora. Assim, por exemplo, a versão mais recente da compiação de desenvolvimento do H3, no momento desta publicação, aproximando-se de uma versão final do 3.1.0, é composta dos arquivos de último registro de data e hora disponíveis no diretório 3.1.0-SNAPSHOT.

## Refatorações de limpeza em potencial

### org.archive.util org.archive.crawler.util

* unir as classes *util* menores em um número menor de classes maiores?
* precisamos de ambos org.archive.util.IoUtils e org.archive.crawler.util.IoUtils?
* precisamos de IoUtils e FileUtils separados?
* o pacote deve ser reduzido ou dividido em subpacotes?
     * mover *blooms* para um subpacote?
     * mover classe SURT para um pacote relacionado a URI?

### ALists e estruturas genéricas de dados - JSON?

O CrawlURI precisa de uma estrutura de dados serializável, genérica e flexível para marcação arbitrária por extensões pouco coordenadas. Alist tem sido isso, mas nosso desconforto nos levou a preterir os acessores diretos ainda úteis.

### Ideias de limpeza do S

* avaliar para refatorar todas as classes com >1000 linhas?

### Diversos

* depreciar SupplementaryLinksScoper - incluído pelos mapeadores, o log principal do LinksScoper?
* conciliar as convenções dash-underscore_CamelCase?

### Contra o toString() significativo

Em alguns lugares (especificamente em torno das classes UURI/CrawlURI) nós sobrepomos o Object.toString () para retornar uma representação mais 'nua' de um objeto, e então contamos com toString() para funcionalidade.

Infelizmente, dada a função especial de toString() como *string-ification* padrão de qualquer objeto e uso através de logs/interfaces de depuração, isso oculta informações úteis - como a classe de alguma coisa que relate um URI toString().

Eu preferiria que qualquer versão de seqüência (string-version) de caracteres significativa de um objeto fosse acessada por outros métodos, retendo o toString() em sua implementação padrão ou alguma outra renderização centrada em depuração, que pudesse ser ajustada destemidamente sem afetar a funcionalidade do aplicativo.

### Banir todas as dependências de JS da interface do usuário da web

Tudo bem usar o JS, mas não deve ser necessário para a utilização da interface do usuário 

### Generalizar o rastreamento de estatísticas (stats-tracking)

Permitir contagens (e taxas?) de quantidades nomeadas genericamente, não apenas o conjunto estático de valores definidos em métodos de interface

## Brainstorming de direções para o futuro

Algumas ideias para mudanças futuras no rastreador.

### Cadeias de processador (Processor Chains)

A forma de agrupamento atual, por função (prefetch, fetch, extract, etc.), é um pouco restrita, especialmente quando um processador pode ser usado em vários locais ou vários processadores precisam trabalhar juntos.

A cadeia do processadores poderia ser substituída por *hooks* e *callbacks*? Poderia haver uma série de *callbacks* de chamada estabelecidos: start earlyPrereqs, latPreReqs, earlyFetch, lateFetch, earlyAnalysis, middleAnalysis, lateAnalysis, earlyFinish, lateFinish end. Um módulo poderia se conectar em vários lugares - desse modo, não teríamos que usar muitos Processadores pequenos para funcionalidades simples. Por padrão, os módulos se conectariam em locais razoáveis, mas isso poderia ser substituído por operadores especialistas.

Como alternativa, a cadeia de processadores pode ser unida em uma só cadeia, mas, talvez, ter ordens consultivas - seja números inteiros sugestivos de posição relativa ou uma série de pré-condições/pós-condições recomendadas, como "não deve ir atrás de nenhum *Fetch processor*".

### Cadeia/escopo pré-programados vs. alreadyIncluded

Um teste feito por escopo e alreadyIncluded pode ser mesclado no mesmo processo? Atualmente, fazemos o escopo primeiro e depois testamos o alreadyIncluded; em teoria, o escopo é mais barato (nunca requer IO), mas rastreamentos futuros podem se beneficiar do contrário, ou certos mecanismos eficientes do alreadyIncluded poderiam tornar a rejeição de um URI comumente encontrado mais barato do que o escopo.

## Novas definições para a interface de usuário baseada na web

(Brainstorming feito por Paul, Michael Magin, Vinay, and Gordon em 27 de abril)

A interface do usuário refeita para operar nas novas configurações provavelmente terá uma área para compor 'planilhas' e outra para atribuição de prefixos URIs/SURT às planilhas.

(Gordon questionou: pode haver mais de uma planilha de 'padrões globais', seja como um pacote ou uma substituição de prefixo vazio?)

Alguns problemas com as configurações atuais da interface do usuário da web incluem:

* as sobreposições às vezes não funcionam como esperado, não tendo efeito ou (em um momento, bug provavelmente corrigido) alterando as configurações globais

* não está claro o que pode ser alterado de forma efetiva no meio do rastreamento e se é necessário pausar para fazer isso. Podemos documentar/impor isso melhor? Podemos tornar todas as mudanças "seguras", seja por meio de manter as definições constantes para um thread até um momento que seja possível mudar atomicamente de forma segura?

Embora as substituições mapeadas com prefixo SURT sejam um superconjunto da funcionalidade atual, ainda deve ser possível inserir um nome de host simples.

Visualizar/editar a frontier parece útil, mas não é útil/eficiente em escala - isso pode ser corrigido?

Existe uma maneira de testar de forma interativa quais configurações se aplicariam a um URI na interface do usuário? (O mesmo vale para escopos.)

Relatório de frontier é frequentemente usado, mas não é o formato ideal para tarefas comuns. Uma classificação por tamanho de fila (ou outras características salientes) ajudaria. Muitas vezes, é difícil visualizar URIs longos (logo os mais interessantes para a avaliação de armadilhas). Cor/tamanho pode ser usado para destacar dados importantes que precisam de atenção? O relatório de seeds - erros no topo, URIs clicáveis - é um modelo bom.

Cross-links de relatórios para exibições *regex-filtered* de logs foram, algumas vezes, interrompidos no passado.

O crawl.log pode receber destaque de sintaxe ou ser clicável?

Bugs/defeitos/incomodações/comportamentos inesperados em UIs antigas e novas devem ser reportados de forma rápida e generalizada ao rastreamento de bugs. 

### Lista de desejo de documentação

* Revisar/corrigir/atualizar o Manual do Usuário 1.X através de alterações da 1.14.0
* Converter o manual do usuário 1.X em páginas wiki
* Rever/corrigir/atualizar o manual do usuário 1.X para 2.0+

## Detecção de Spam da Web para o Heritrix

### Detecção de Spam da Web para o Heritrix

[Este projeto] (https://code.google.com/soc/2008/intarch/appinfo.html?csaid=F6D044FD6B4943FF) é um dos quatro projetos orientados pelo [Internet Archive] (http://www.archive.org/index.php) no [Google Summer of Code 2008] (http://code.google.com/soc/2008/).

### Introdução

Spam na Web é um grande problema para o mecanismo de pesquisa atual. Alguns sites de spam não contêm informações úteis. Rastrear esses sites é apenas um desperdício de tempo, esforço e espaço de armazenamento. O Heritrix não é, primeiramente, um rastreador de mecanismo de pesquisa, então não tem tanto problema conter um pouco de spam. No entanto, atualmente, o spam na Web utiliza muito dos recursos, proporcionalmente, portanto, é necessário que o Heritrix consiga detectar spam durante o rastreamento.

Hoje em dia, sites de spam têm empregado todos os tipos de técnicas a fim de obter classificações mais elevadas do que merecem e esconder suas identidades. Essas técnicas podem ser categorizadas como *boosting* ou *hiding* [1]. Embora spam de conteúdo e link seja a técnica de *boosting* mais amplamente utilizada, redirecionamento (redirection) e cloaking são técnicas *hiding*. Ao mesmo tempo, muitas técnicas de detecção de spam na Web foram propostas na literatura. Algumas delas usam recursos como a estrutura de hiperlinks entre páginas e os registros DNS de hosts, e alguns métodos baseados em conteúdo precisam de estágio de treinamento antes da detecção.  Eles não se encaixam bem na detecção de spam do Heritrix (já que o Heritrix não é um mecanismo de busca) e nas análises complexas feitas rapidamente (como analisar a estrutura de hiperlinks) e o conteúdo da página aumentaria a sobrecarga e afetaria sua funcionalidade principal - rastreamento de sites. Propomos, então, nos concentrar na detecção de spam baseado em redirecionamento e cloaking por dois motivos: (1) Essas duas técnicas de spam são predominantes, atualmente. (2). As abordagens de detecção discutidas na próxima seção são simples e eficazes e causam menos sobrecarga. 


Além disso, há outro motivo pelo qual este projeto é de interesse para a comunidade Heritrix. Como discutiremos na seção de metodologia, para detectar spam de redirecionamento de JavaScript e spam de cloaking, é necessário o recurso de execução de JavaScript, e esse recurso também ajudará a descobrir links externos legítimos de uma página da web.

### Metodologia

Nesta seção, explicamos brevemente as técnicas de redirecionamento de JavaScript e as técnicas cloaking de spam, juntamente com as abordagens de detecção correspondentes. Essas abordagens de detecção discutidas aqui foram propostas em [1, 3]. Para mais informações, consulte esses documentos.

**Redirecionamente de JavaScript**

Os spammers geralmente criam um número de páginas da web que serão redirecionadas para o site verdadeiro de spam. O redirecionamento pode ser obtido usando códigos de status HTTP, atualização META e JavaScript. O redirecionamento de JavaScript é amplamente usado em spam na web e é difícil de ser detectado por análise estática. O exemplo a seguir é um algoritmo simples para detectar o redirecionamento de JavaScript, se um navegador for usado.

Carregue a página da Web duas vezes com o JavaScript ativado e desativado;
se DstURL(E) = OrgURL, então
  Sem redirecionamento;
se DstURL(E) != DstURL(D) então
  redirecionamento de JavaScript;
outro
  sem redirecionamento de JavaScript;
  
OrgURL, DstURL (E) e DstURL (D) representam o URL original, o URL de destino com JavaScript ativado e o URL de destino com o JavaScript desativado, respectivamente.

No entanto, não precisamos carregar a página duas vezes, porque podemos saber se existe redirecionamento de JavaScript avaliando o código JavaScript na página durante a análise. Portanto, o que precisamos fazer é:
Etapa 1: carregar a página;
Etapa 2: analisar a página com o JavaScript ativado;
Etapa 3: se o redirecionamento de JavaScript existir
                  marque a página e tome as ações correspondentes;
             ou
                  continue o processo normal;

Na Etapa 3, após a detecção do redirecionamento de JavaScript OrgURL => DstURL (E), algumas regras simples podem ser aplicadas para excluir casos de redirecionamentos comuns de não-spam, por exemplo:

* www2007.com => www.2007.com/;
* www2007.com => www.2007.com/{index, default, ...}.{htm, html, asp, aspx, php, ...};
* Redirecionamento dentro do mesmo host.
  O restante dos redirecionamentos é considerado como spam de redirecionamento de JavaScript.
  
Claro que haverá falsos positivos. Para ser mais preciso, a investigação manual ou outras detecções de spam da Web podem ser aplicadas offline.

### Cloaking

A ideia básica do *cloaking* é de servir conteúdo diferente para diferentes visitantes. O servidor da Web pode decidir qual página da Web será exibida com base no campo User Agent, endereços IP do visitante, etc. As páginas da Web podem conter scripts que reescreverão o conteúdo, de modo que os usuários verão conteúdo reconfigurado usando um navegador da Web, mas um rastreador talvez não consiga ver a mesma coisa, porque, normalmente, a maioria dos rastreadores não executa scripts.

*Cloaking por cliques (click-through cloaking)* é outra técnica de cloaking. Pode ser no lado do servidor ou no lado do cliente. Uma verificação do lado do servidor do campo Referer no cabeçalho HTTP é feita se o spammer possuir o site que hospeda o URL de spam; se a URL de spam for localizada em um site de hospedagem gratuita, uma verificação do lado do cliente do objeto document.referrer do navegador é realizada e, em seguida, com base no resultado, páginas diferentes são exibidas pelo servidor de hospedagem do site (primeiro caso) ou exibidas usando o documento .write () (segundo caso).

Portanto, podemos detectar sites de spam baseados em cloaking por cliques da seguinte maneira, o que também exige que o rastreador possa interpretar os scripts:

Primeiro passo. Primeira visita - acesse o site na forma normal do mecanismo de busca e obtenha o conteúdo do conteúdo da página (engine);
Segundo passo. Segunda visita - para verificações de referência tanto do lado do servidor quanto do lado do cliente, faça com que o acesso pareça vir de uma busca através de cliques e obtenha o conteúdo do conteúdo da página (click-through);
Etapa 3. Compare a diferença de conteúdo entre as duas visitas diff(content(engine) e content(click-through)). Se houver uma grande diferença, esse site é altamente suspeito de ser um site de spam e mais investigações precisam ser feitas, caso contrário, classifique-o como benigno.

Neste projeto, focamos em detectar cloaking por cliques.
               
### Bibliotecas de terceiros

**1. Mecanismo JavaScript incorporado**

Como é discutido na seção de metodologia, ambas as abordagens de detecção de spam baseado em cloaking e de spam de redirecionamento de JavaScript exigem que um rastreador da Web possa executar código JavaScript embutido/externo (inline/external).

Rhino [4] é um mecanismo JavaScript de código aberto escrito em Java sob o MPL 1.1/GPL 2.0. Vamos incorporá-lo no Heritrix.

**2. Analisador sintático de HTML**

Para executar o código JavaScript em uma página da Web, um mecanismo JavaScript incorporado não é suficiente, precisamos obter todos os objetos e atributos associados a ele de uma página da Web. Em outras palavras, precisamos de um analisador sintático de HTML capaz de analisar documentos HTML e gerar o HTML Document Object Model (HTML DOM).

Cobra[5] é um renderizador e analisador sintático de HTML escrito exclusivamente em Java. Seu analisador é capaz de gerar matrizes HTML DOM. O Cobra incorpora o Rhino JavaScript Engine, de tal forma que durante a análise, o código JavaScript inline e extenal pode ser executado e a modificação correspondente no DOM feita por esse código será refletida no DOM resultante.

Neste projeto, escolhemos usar o Cobra como analisador sintático de HTML e também utilizaremos sua capacidade de executar código JavaScript com a ajuda do Rhino.

### Detalhes de design e implementações

Execução de JavaScript(JS)

Um desafio de integração foi questionado pelo Sr. Mohr: como ser compatível com o fluxo de trabalho existente do Heritrix?

> "Como garantir que todos os recursos necessários sejam buscados (incluindo SCRIPTs e possível CSS) antes do Javascript ser executado, mas de uma maneira aproximadamente compatível com a descoberta/fluxo existente de URIs no rastreador. (Por exemplo, para que cada URI buscado seja escrito palavra por palavra nos arquivos W/ARC do rastreio. Ou, que regras claras sejam aplicadas quando há conflitos entre se um URI é necessário para execução JS, mas excluído pelo(a) outro(a) escopo/definição do operador do rastreador.)" Fazer o download desses recursos adicionais passa pelas etapas normais de programação, cortesia, análise etc. do Heritrix? Por um lado, queremos que os logs do rastreador e os arquivos (W)ARC sejam um registro preciso dos recursos que ele realmente recuperou. Pelo outro, não queremos ter os recursos necessários para o processamento de JS/DOM em uma única página ser planejado no futuro, ou seu carregamento ser abortado por outras configurações do rastreador. "

Porque o código JS pode ser interno (embutido no documento HTML) ou externo (isto é, <script src = "location">). No analisador sintático cobra de HTML, sempre que um código JS externo é necessário, ele tentará buscar o código-fonte dentro do próprio analisador. Gostaríamos que cada busca seguisse a cadeia de processadores, ou seja, passar pelas etapas normais de agendamento, cortesia, análise, etc. do Heritrix, e isso implica que precisamos buscar recursos JS externos necessários antes da execução do JS. Além disso, não queremos iimpedir a dupla busca de um documento HTML, portanto, durante a execução do JS, precisamos recuperar o conteúdo do documento HTML e também os recursos de JS necessários. Para fazer isso, introduzimos um mecanismo de cache - o cache de busca (fetch cache), que é usado para preservar esse tipo de informação.
  
**. Class FetchCache**

O cache de busca contém três grandes mapas: mapa de recursos, mapa dependente e mapa de localização de conteúdo ou conteúdo. No texto a seguir, "recurso" significa recurso JS requerido (por exemplo, arquivo de origem JS) e seu link é extraído de um documento HTML.

Mapa de recursos: <uri de um recurso, a lista de documentos HTML que dependem deste recurso>;
Mapa dependente: <uri de um documento HTML, a lista de recursos dos quais este documento HTML depende>;
Mapa de localização de conteúdo ou conteúdo: <uri de um recurso ou documento HTML, conteúdo ou localização de conteúdo deste uri>.

**. Class FetchCacheUpdater**

O FetchCacheUpdater é um pós-processador, semelhante ao CrawlStateUpdater, mas é usado para atualizar o cache de busca após um uri ter sido processado pelo LinksScoper.
O motivo pelo qual o FetchCacheUpdater deve ser executado após o LinksScoper é que alguns links externos de um documento podem estar fora do escopo, e pode haver alguns recursos necessários do JS; O LinksScoper, primeiro, vai lidar com esses links fora do escopo e, no FetchCacheUpdater, podemos encontrar facilmente os recursos necessários, mas fora do escopo, e registrar essas informações para o usuário.

Outra tarefa do FetchCacheUpdater é criar um uri de rastreamento especial que é projetado para fins de execução do JS. Depois de atualizar o cache de busca, o FetchCacheUpdater tentará obter todas as urs do HTML que estão prontas para avaliação do JS, e criar um uri de rastreamento cujo esquema é "x-jseval" para cada um deles, ou seja, se um uri normal for http://www.uri.com, o uri especial seria x-jseval: http://www.uri.com) e, em seguida, adicioná-los aos outCandidates do uri atual para agendamento posterior.

**. Class ExecuteJS**

Esse processador é usado para analisar um documento HTML com o JS ativado e também simular eventos HTML no documento e tentar descobrir novos links criados pelo código JS. Só aceita uris com o esquema de "x-jseval".

O conteúdo do uri HTML sendo processado e seus recursos necessários de JS podem ser recuperados do cache de busca.

Em relação aos eventos HTML, o onload é simulado durante a análise, onclick, onmouseover e onmouseout são simulados após a análise. Esses três tipos de eventos HTML podem ser simulados de forma acumulativa ou a partir de um novo DOM (criado após a análise) toda as vezes.

A descoberta de novos links é realizada após a análise e após cada simulação de evento HTML. Novos links serão adicionados aos outLinks do HTML uri.

### Detecção de redirecionamento de JavaScript

Detectar o redirecionamento de JS é fácil. Só precisamos verificar se o local do documento no HTML DOM resultante, após a análise ou a simulação de eventos HTML, é diferente do local original (o local onde buscamos o documento).

**. Class DetectJSRedirection**

Essa funcionalidade é implementada no processador DetectJSRedirection.

Se o processador ExecuteJS estiver incluído no perfil da tarefa e executado antes de DetectJSRedirection (o que normalmente acontece), precisamos apenas verificar se existe um novo link cujo contexto é "JSRedirection" em outCandidates do uri atual. Se assim, ocorreu o redirecionamento JS, caso contrário, nenhum redirecionamento de JS, porque no processador ExecuteJS, o contexto do link de redirecionamento de JS é definido como "JSRedirection".

O Processor DetectJSRedirection também pode funcionar sem o processador ExecuteJS. Nesse caso, o código JS precisa ser avaliado para detectar o redirecionamento de JS, portanto, todos os recursos necessários de JS devem estar disponíveis antes da detecção.

Não importa qual seja o caso, o DetectJSRedirection só pode aceitar rastreamento de uri com o esquema de "x-jseval".

### Detecção de cloaking

**. Class DetectCloaking**

A detecção de cloaking é implementada no processador DetectCloaking que, atualmente, foca apenas em cloaking por cliques. Como mencionado na seção de metodologia, para detectar cloaking por cliques do lado do cliente, precisamos comparar os documentos resultantes obtidos após a execução do código JS, com o document.referrer definido como empty e com o document.referrer definido como someting; se forem diferentes, cloaking do lado do cliente aconteceu. Para detectar cloaking por cliques do lado do servidor, precisamos comparar os documentos buscados com o campo referenciador no cabeçalho HTTP definido como null ou something; se forem diferentes, ocorreu o cloaking do lado do servidor.

Portanto, algumas informações devem estar disponíveis para detectar *clich-through cloaking*:
Para detecção de cloaking do lado do cliente
  - referrer
  - recursos: a lista de recursos requeridos pela execução do JavaScript.
  
  Para detecção de cloaking do lado do servidor
  - content-with-referrer: conteúdo obtido com o campo referrer definido como non null no cabeçalho HTTP.
  - content-without-referrer: conteúdo obtido com o campo referrer definido como null no cabeçalho HTTP.
  
Como podemos ver, a fim de detectar cloaking por cliques no lado do servidor, precisamos buscar a mesma página duas vezes com configurações de cabeçalho HTTP diferentes (claro que gostaríamos que as duas buscas passassem pela cadeia de processadores do Heritrix); também precisamos preservar as informações necessárias mencionadas acima. Então, para resolver esse problema, usamos o mesmo caminho que fizemos para a execução de JS, introduzimos outro cache - cache de cloaking, que possui um mapa, contendo todas as informações necessárias.

**. Class Cache4CloakingDetection**

Mapa: <uri de um documento HTML, pré-requisitos necessários>

Pré-requisitos necessários: "referrer", "js-required-resources", "content-with-referrer", and "content-without-referrer".

A tarefa de atualizar o cache de cloaking é executada dentro do DetectCloaking.

### Algoritmos principais

### Execução de JS

**. Processor chains:

Preprocesses -> fetcher -> extractors -> ExecuteJS -> ARCWriter -> CrawlStateUpdater -> LinksScoper -> FetchCacheUpdater -> FrontierScheduler

**. Algoritmo implementado no FetchCacheUpdater:**

Para obter explicações detalhadas sobre os mapas no FetchCache, consulte FetchCache em designs e na seção de detalhes de implementação.

```
curi é o uri atual sendo processado.

se o curi não for buscado com sucesso e seu script for um recurso, remova todas as informações relacionados a ele do cache de busca;
se o curi for buscado com sucesso, então {

    se o cursi é um recurso de script, então {

        ee o curi estiver no mapa de recursos, {
            para cada HTML uri dependendo dele {
                encontre o HTML uri no mapa dependente;
                atualize sua lista de recursos, ou seja, remova o curi de sua lista;

                encontre o HTML uri no mapa candidato;
                atualize sua lista de recursos, ou seja, adicione o curi à sua lista;

                e todos os recursos estiverem disponíveis {
                    adicione o curi à lista waitToSchedule;
                    remova a entrada correspondente no mapa dependente e candidato;
                }
            }
            remova a entrada do curi no mapa de recursos;
        }
        adicione uma entrada no mapa disponível (curi, conteúdo/localização de conteúdo);
   }

  se o curi é um uri em HTML, então {
      crie uma coleção de urls de recursos necessários da coleção outCandidate;
      para cada recurso uri na coleção de uris de recursos necessários JS {
          se estiver no mapa disponível, então {
              remova-o da coleção
          }
          ou {
               se estiver no mapa de recursos, adicione o curi à sua lista de dependentes;
             ou adicione uma entrada no map1 (recurso uri, curi);
          }
      }
            se a coleção necessária de recursos uris estiver vazia, então
          adicione o curi à lista waitToSchedule;
      ou {
          adicione uma entrada no mapa dependente (curi, coleção de recursos necessários uris);
          adicione uma entrada no mapa cadidado;
      }
      adicione uma entrada no mapa disponível (curi, conteúdo/localização do conteúdo);
  }
}
Por fim, crie um rastreamento especial para cada membro da lista waitToSchedule e coloque-o em outCandidates do curi;
```

### Detecção de redirecionamento de JS

**. Cadeias de processadores:**

Preprocesses -> fetcher -> extractors -> ARCWriter -> CrawlStateUpdater -> DetectJSRedirection -> LinksScoper -> FetchCacheUpdater -> FrontierScheduler

. Algoritmo implementado em DetectJSRedirection

```
if (ExecuteJS module is added in the profile) then {
    check if JS redirection happened;
    if yes, return true for JS redirection;
    else return false;
} else {  // Similar to the procedure in ExecuteJS, but only to check JS redirection, not find new links

    DOM dom = parse(HTML document referred by x-jseval crawl uri);
    check if JS redirection happened;
    if yes, return true for JS redirection
    if no, then simulate HTML event one by one from a fresh DOM:

    for every HTML event (onmouseover, onmouseout or onclick) do {
         get a fresh DOM (parse the document again);
         simulate the event;
         check if JS redirection happened;
         if yes, return true for JS redirection;
         else continue;
    }

    return false for JS redirection;
}
```

### Detecção de cloaking por cliques

Para o significado desses pré-requisitos, consulte a seção DetectCloaking em detalhes de design e implementação.

**. Cadeias de processadores:**

Preprocesses -> fetcher -> extractors -> ARCWriter -> CrawlStateUpdater -> DetectCloaking -> LinksScoper -> FetchCacheUpdater -> FrontierScheduler

**. Algoritmo implementado no DetectCloaking**

```
if uri is an HTML document then {
    if (context of uri is not null) {
        update Cache4CloakingDetection with (uri, data) and data.put("content-with-referrer", content);
        update Cache4CloakingDetection with (uri, data) and data.put("referrer", context);
        if (! data.containKey("content-without-referrer") {
            create a new crawl uri with context set to null, and force it to be crawled;
        }
   } esle {
       update Cache4CloakingDetection with (uri, data) and data.put("content-without-referrer", content);
       if (! data.containKey("content-with-referrer") {
           create a new crawl uri with context set to itself, and force it to be crawled;
       }
   }
}

if uri is an x-jseval uri {
    get required resources;
    update Cache4CloakingDetection with (uri, data) and data.put("resources", content);
}

check if all fields (referrer,resources, content-with-referrer and content-without-referrer) are available;
if yes {
    withReferrerDom = parse(uri document with null referrer);
    withoutReferrerDom = parse(uri document with referrer (itself));
    if (withoutReferrerDom == withReferrerDom) {
        no client side cloaking;
    } else {client side cloaking}

   withoutReferrer = get value of "content-without-referrer" from Cache4CloakingDetection for current uri;
   withReferrer = get value of "content-with-referrer" from Cache4CloakingDetection for current uri;

   if (withoutReferrer == withReferrer) {
       no server side cloaking;
   } else {server side cloaking}

} else {
   return;
}
```

### Modificações na biblioteca Cobra

**HTMLParser**

No Cobra, a classe que executa a análise sintática é a HtmlParser. Como mencionamos acima, quando o analisador vê uma tag <script>, ele tentará avaliar o código do script. Se o script estiver embutido, o analisador poderá ler facilmente o código do fluxo de entrada do documento, mas, se o script for um arquivo externo, ele tentará primeiro buscar o arquivo de script e, em seguida, fará a avaliação, ou seja, a busca é feita durante a análise. Neste projeto, introduzimos o mecanismo de cache de busca para garantir que todos os scripts externos sejam buscados antes da análise - por isso modificamos o analisador para obter o conteúdo de um arquivo de script externo localmente (já buscado) em vez de iniciar uma nova extração a partir da cadeia de processamento do Heritrix. 
  
As principais modificações são:

* A classe é renomeada como HTMLParser em vez de HtmlParser e colocada no pacote org.archive.modules.extractor.jsexecutor em vez de org.lobobrowser.html.parser.
* Um novo construtor é adicionado. Recebe um parâmetro a mais do que o usado originalmente. O parâmetro é um HashMap no qual o conteúdo dos arquivos de script externos são armazenados e as chaves são as URLs dos scripts.
* Durante a análise, quando uma tag <script> associada a um arquivo de origem externo é vista, o analisador tentará localizar o conteúdo do arquivo chamando a função de membro fillInSourceCode em vez de buscar o script.
* Como no HTMLParser, classes ElementInfo, LocatorImpl e StopException no pacote org.lobobrowser.html.parser são necessárias. Originalmente, essas classes eram protegidas, mas nós as mudamos para públicas a fim de poder usá-las no HTMLParser.
  
### Tag Archor

O código a seguir é permitido no documento HTML, onde o valor do atributo href é o código JavaScript.

```
<body>
  <script>
  var rootUri = 'cas'+'e2-success';
  function navigate() {
    document.location.href=rootUri;
  }
  </script>
  <a href="javascript:document.location.href=rootUri">href</a><br/>
</body>
```

No Cobra, eles consideram apenas o caso comum em que o valor de href é um URL. Portanto, para que o analisador HTML Cobra suporte o caso acima, foram feitas modificações na classe HtmlController e no HTMLAbstractUIElement.

### Funcionalidade JavaScript

**Redirecionamento de JS**

No Cobra, quando ocorre um redirecionamento de JS, a função de membro setHref na classe Location será invocada. Se um renderizador de HTML estiver associado ao aplicativo atual, um novo documento HTML será criado, buscado e analisado. Nesse projeto, não precisamos renderizar o documento HTML, só precisamos saber se o redirecionamento de JS acontece ou não, então pequenas modificações foram feitas na classe Location.

**setTimeout e setInterval em JS*

setTimeout e setInterval, duas funções JS, são implementadas no Cobra invocando a função de membro setTimeout e setInterval na classe Window. Neste projeto, precisamos apenas simular o que vai acontecer quando um evento HTML é acionado, o tempo exato não é necessário, portanto, essas funções de membro foram modificadas para essa finalidade.

### Discussão e trabalho futuro

###### Execução de JS**

**. Localização de conteúdo em FetchCache**

Atualmente, no cache de busca, o que é salvo no mapa disponível é o conteúdo de cada documento, e todos eles estão na memória. Obviamente, se uma tafera de rastreamento muito grande estiver em execução, mais cedo ou mais tarde o cache ficará sem memória se continuarmos adicionando conteúdos novos nele. Para resolver esse problema, um mecanismo de "idade" pode ser projetado, ou seja, cada documento disponível no cache de busca tem uma propriedade de idade. Basicamente, um limite de idade é predeterminado e, quando um documento está disponível, não apenas o conteúdo, mas também a localização do conteúdo (por exemplo, nome do arquivo arc e deslocamento dentro do arquivo arc) serão armazenados no mapa disponível. Conforme o tempo passa, a "idade" do documento aumenta. Quando a idade do documento passar do limite determinado, o conteúdo deste documento é apagado da memória e apenas sua localização é apresentada. Quando o documento for necessário novamente, seu conteúdo pode ser recuperado a partir do local especificado.

**. Usar JavaScript para importar outro JavaScript**

No ExecuteJS, um documento HTML só será analisado uma vez com o JS ativado, gerará o DOM correspondente e não será processado novamente. Portanto, no caso a seguir, não poderemos obter o DOM final correto.

test.html

```
<html>
<head>
<script id="jsfile1" src="code1.js"></script>
<script id="jsfile2"></script>
</head>
<body>
<P>Header</P>

<div id=DataContainer>
<script type="text/JavaScript">
DoRoutine('code2');
</script>
</div>

<P>Footer</P>
</body>
</html>
```

code1.js

```
function DoRoutine(routineID) {
    document.getElementById('jsfile2').src = routineID + ".js";
}
```

code2.js

```
function getDATA () {
    var data = new Array(3);
    data [1] = "This is DATA 1, ";
    data [2] = "This is DATA 2, ";
    data [3] = "This is DATA 3";

document.getElementById("DataContainer").innerHTML = data[1] + data[2] + data[3];

}

getDATA ();
```

Logo, para tornar o comportamento do ExecuteJS muito mais parecido com um navegador da Web, uma avaliação repetida do JS é necessária. No entanto, há problemas que precisam ser resolvidos:

* Como agendar e buscar novos recursos que aparecem no DOM resultante?
* Mesmo quando conseguimos extrair novos recursos, como podemos preservar o status de análise sintática atual e continuar a avaliação do JS quando os novos recursos estão disponíveis?

**. Simular eventos HTML de forma acumulativa ou de um estado recém carregado (um DOM novo criado após a análise)**

Neste projeto, as duas formas foram implementadas (no código, o primeiro foi comentado). A simulação de eventos HTML de forma acumulativa é mais eficiente do que a outra maneira, já que para a última o documento original é analisado todas as vezes antes de uma simulação de um evento (pode haver uma maneira melhor de implementar isso). No entanto, como não podemos prever o comportamento do usuário, a maneira antiga pode não ser muito precisa. Eu não tenho uma boa ideia de como comparar as duas meneiras para ver qual é a melhor.

###### Detecção de cloaking

**. Técnicas de cloaking**

Existem várias técnicas de cloaking:

* Cloaking por cliques: cloaking pelolado do cliente e cloaking pelo lado do servidor;
* Cloaking por User-Agent ou IP Delivery;
* Reescreve a página da web usando scripts, já que os rastreadores geralmente não executam scripts;
* ... (não estou ciente)
* Uma mistura dessas técnicas.

Neste projeto, apenas implementamos cloaking por cliques. O algoritmo é apresentado na seção principal de algoritmos. A detecção das outras duas técnicas de cloaking pode ser implementada facilmente com base no projeto atual.

Para as duas segundas técnicas de cloaking, ainda precisamos buscar a mesma página da Web duas vezes, mas com o campo User Agent ou o endereço de IP diferentes. Se houver uma grande diferença de conteúdo entre as duas buscas, será detectado cloaking do lado do servidor. O CrawlURI fornece a interface para definir o campo User Agent. Além disso, o conteúdo pode ser armazenado em cache de cloaking.

Para a terceira técnica de cloaking, já que nesse projeto o Heritrix é capaz de executar o código JS, o que podemos fazer é comparar a página da web original, buscada pelo processador fetchHTTP, com a página da Web resultante criada com o JS ativado. Apenas uma busca nesta página da Web é suficiente. Simulação de eventos HTML na página da web é opcional.

Os métodos de modificação/Modificando o método updatePrerequisitesCache e cloakingDetection na classe DetectCloaking devem ser capaz de implementar as duas técnicas de cloaking.

No entanto, a detecção individual de cada técnica de cloaking pode não funcionar se o site de spam usar duas ou mais técnicas ao mesmo tempo. Por exemplo, um servidor da Web pode fornecer conteúdo diferente não apenas com base no User Agent, mas também com base no valor do campo referenciador no cabeçalho HTTP, provavelmente mos impedindo de conseguir obter uma página da Web diferente se alterarmos apenas um deles. E, caso apenas uma técnica de cloaking seja usada, nossa detecção ainda assim pode falhar, já que o servidor da Web pode esperar um valor específico e fornecer conteúdo diferente, por exemplo, somente quando o referrer for "www.specificvalue.com" o site retorna o conteúdo real. Como só conseguimos adivinhar esse valor, a chance de acerto é muito pequena. O que poderíamos fazer é nos concentrarmos nas técnicas de cloaking mais comuns. No entanto, atualmente, ainda não encontramos informações sobre a prevalência de técnicas de cloaking. Espero que encontremos no futuro.

**. Comparação de conteúdo**

Como podemos ver, a comparação de conteúdo é um passo obrigatório na detecção de cloaking, não importa como obtemos o conteúdo. Nesse projeto, apenas uso o método de comparação de strings, o que significa que, se houver apenas uma diferença de caractere ou dígito, o cloaking será detectado. Obviamente, essa comparação simples não é apropriada para a detecção de cloaking. Portanto, um método de comparação mais sofisticado deve ser projetado. Precisamos definir quanta diferença ou diferença em qual parte da página da Web deve ser considerada como cloaking. Atualmente, as pessoas propuseram hashing difuso (mencionado pelo Sr. Mohr). No hash difuso, pequenas diferenças podem ser ignoradas. Para mais informações, consulte http://ssdeep.sourceforge.net/ e o artigo acadêmico completo em http://dfrws.org/2006/proceedings/12-Kornblum.pdf.

**. Busca dupla**

A busca dupla é necessária para a detecção de cloaking pelo lado do servidor. Fazer uma dupla busca para cada página da web não só diminuirá o desempenho do Heritrix, mas também afetará muito os sites que estão sendo rastreados. Além disso, o spammer pode facilmente perceber esse comportamento "anormal". Até agora, não tenho conhecimento de nenhum método heurístico para prever se um site é spam ou não antes de buscar conteúdo e fazer mais processamento. O que poderíamos fazer é amostrar o site e realizar apenas buscas duplas em um número limitado de páginas da Web em um site. Uma boa estratégia de amostragem pode ser um trabalho futuro.

###### Após a detecção de spam

Atualmente, o módulo de detecção de spam da Web - processador DetectJSRedirection e DetectCloaking - retorna "true" quando o redirecionamento de JS ou cloaking é detectado (uma mensagem de aviso é registrada no modo de depuração), mas nenhuma outra ação é executada. Registrar essas informações em um arquivo de log como crawl.log pode ser útil.

###### Problemas não resolvidos

**Testes de JUnit**

Não tive a chance de escrever testes JUnit para este projeto.

**Sincronização em FetchCache e Cache4CloakingDetection**

Há vários toeThreads para processar uris diferentes, ao mesmo tempo, no Heritrix. É possível que o cache de busca ou o cache de cloaking sejam acessados por vários threads e a sincronização incorreta pode resultar na leitura de dados incorretos ou em conflito. Nesse projeto, eu uso uma tabela *hash* para todos os dados compartilhados dentro desses dois caches, e a sincronização de granularidade aproximada é implementada. No futuro, talvez um HashMap possa ser usado e uma boa sincronização de granularidade pode ser feita.

### Uso e Teste

###### Código

O código fonte deste projeto está disponível no repositório Heritrxi SVN sob branches / jenniful_gsoc08 / no sourceforge, e pode ser verificado em http://archive-crawler.svn.sourceforge.net/svnroot/archive-crawler/branches/jenniful_gsoc08/heritrix2 /.

###### Perfil da tarefa de rastreamento

Para testar os módulos que desenvolvi, é necessário criar um novo perfil de tarefa.


O perfil da tarefa - profile-js-cache-rule-spamdetection (ver anexo) é um exemplo pronto para uso. É criado com base em profile-basic_seed_sites, com dois caches: FetchCache e Cache4CloakingDetection, quatro processadores: FetchCacheUpdater, ExecuteJS, DetectJSRedirection e DetectCloaking e um MatchesListRegExpDecideRule adicionado.

FetchCache, FetchCacheUpdater e MatchesListRegExpDecideRule são necessários para o ExecuteJS; DetectJSRedirection, DetectCloaking e Cache4CloakingDetection são necessários para o DetectCloaking; mas DetectJSRedirection, DetectCloaking e Cache4CloakingDetection não dependem um do outro, então eles podem ser adicionados ao perfil individualmente; e MatchesListRegExpDecideRule é usado para garantir que o crawl uri com o esquema de x-jseval esteja dentro do escopo do link.

###### Casos de teste

Alguns casos de teste estão anexados. Existem três subdiretórios: testFetchCache, testExecuteJS e testDetectCloaking. Todos os casos de teste são documentos HTML simples, testes complexos não são realizados.

No **testFetchCache**, os testes são usados para testar as funcionalidades do FetchCache, FetchCache e ExecuteJS. Depois de rastrear os casos de teste, verifique o arquivo crawl.log - você verá que todos os arquivos .html são buscados primeiro e, em seguida, os arquivos .js externos. Depois, uris com esquema de x-jseval são processados e, finalmente, novos links criados por código JS são rastreados.

test1.html, test2.html, test3.html, test4.html e test5.html são usados para testar habilidades simples de execução de JS, incluindo simulação de eventos HTML.
test6.html e test7.html são usados para testar o método setTimeout no JS.
test8.html é usado para testar o tratamento de erros quando um recurso é necessário, mas fora do escopo. No projeto atual, uma mensagem de aviso registrada pode ser vista no modo de depuração.

No **testExecuteJS**, os testes também são usados para testar as funcionalidades do FetchCache, FetchCache e ExecuteJS. Esses testes foram criados durante a avaliação intermediária e modificados pelo Sr. Mohr.

case1.html - case6.html são usados para testar a capacidade simples de execução de JS, incluindo a simulação de eventos em HTML. Em cada caso, um link out (uri terminado com "caseX-success", onde X é o número que aparece o nome do caso de teste), que não pode ser extraído com a versão atual liberada do Heritrix, pode ser descoberto por este Heritrix com capacidade de execução de JS.
mytest1.html - mytest3.html devem testar mais a execução de JS, e o código JS nesse documento é mais complexo.

Defina o seed como o alltests.html, o rastreador buscará todos os casos de teste. Depois que a tarefa de rastreamento for concluída, se você verificar o crawl.log e ver os uris terminarem com "caseX-success", isso significa que todos os módulos funcionaram.

Como quase todos os testes em testFetchCache e testExecuteJS envolvem o redirecionamento de JS, eles também podem ser usados para testar o DetectJSRedirection.

Em testDetectCloaking, os testes são usados para testar o DetectCloaking. O test9.html testa cloaking por cliques no lado do cliente e o testcloaking2.php implementa cloaking do lado do servidor.

Observação: como não há arquivo de log de saída para DetectJSRedirection e testDetectCloaking, os resultados da detecção são apresentados como mensagens de aviso e só podem ser vistos no modo de depuração.

## Agradecimentos

Gostaria de agradecer ao Google e ao Internet Archive por me dar essa oportunidade e agradecer a Gordon Mohr por suas grandes ideias (o design da pasta de busca foi proposto por ele), valiosos comentários, conselhos e seu valioso tempo.

Também gostaria de agradecer às pessoas no projeto Lobo por desenvolverem um analisador de DOM HTML tão bom quanto Javascript e CSS, o Cobra, e agradeço o Mozilla pelo JavaScript Engine - Rhino.

## Referências

[1] K. Chellapilla, and A. Maykov. A taxonomy of JavaScript redirection spam. In Proceedings of the 3rd international workshop on Adversarial information retrieval on the web, Banff, Alberta, Canada, 2007.
[2] Z. Gyöngyi and H. Garcia-Molina. Web Spam Taxonomy. In 1st International Workshop on Adversarial Information Retrieval on the Web, May 2005.
[3] Y. Niu, Y. M. Wang, H. Chen, M. Ma, and F. Hsu. A Quantitative Study of Forum Spamming Using Context-based Analysis. In Proceedings of Network and Distributed System Security (NDSS) Symposium, February 2007.
[4] Rhino, http://www.mozilla.org/rhino/
[5] Cobra, http://lobobrowser.org/cobra.jsp

## Um pouco sobre mim

Meu nome é Ping Wang, estudante de doutorado em Ciência da Computação na University of Central Florida.

Meu endereço de e-mail é pwang@cs.ucf.edu. Por favor, sinta-se à vontade para entrar em contato comigo se tiver algum comentário ou pergunta sobre este projeto.

## Guia de estilo

Essa seção wiki serve como guia de estilo de codificação e interação/processo de interface do usuário para o projeto Heritrix e projetos de arquivamento da Web de código-fonte aberto relacionados.

A orientação pode, ocasionalmente, ser contraditória enquanto os tópicos estão sendo discutidos; alternativas conflitantes devem ser bem descritas (com argumentos de apoio e precedentes) para auxiliar o progresso rumo a uma expressão consensual/canônica.

###### Outras autoridades

Referências úteis:

* [Code Conventions for the Java Programming Language] (https://www.oracle.com/technetwork/java/codeconvtoc-136057.html) do Sun
* [Java Programming Style Guidelines] (https://petroware.no/javastyle.html) da Geosoft 

Na ausência de orientações ao contrário abaixo, as recomendações nas fontes acima podem ser sempre seguidas.

###### Estilo de código

(Alguns tópicos retirados do [Manual de Desenvolvedor Heritrix 1.X] (http://crawler.archive.org/articles/developer_manual/conventions.html))

* Use espaços ao invés da tecla tab. Tabs não devem aparecer no código-fonte do projeto.
* Recuo de 4 espaços por nível.
* Coloque o colchete de abertura de um bloco de código na mesma linha que a declaração/teste esperando o bloco.
* Use colchetes mesmo quando uma ramificação/bloco for apenas uma única linha de código (para fornecer uma sugestão visual adicional e para robustez, se outras linhas forem adicionadas posteriormente).
* Prefira o uso de 'longs' ao inves de 'ints' quando uma grande quantidade de artefatos ou arquivos/intervalos de grande tamanho for possível.
* Prefira 'protected' em vez de 'private', a menos que uma consideração do uso de subclasses em potencial sugira que o acesso direto é perigoso.
* (Desvio das recomendações do Sun) É permissível declarar as variáveis locais o mais próximo possível do primeiro uso (ao contrário de declarar no início do bloqueio).
* (Desvio de algumas recomendações) Os retornos antecipados e múltiplos dos métodos são encorajados a minimizar os níveis de indentação e a lidar com casos simples ou de erro rapidamente.
* Todas as classes e métodos públicos devem ter comentários do Javadoc. Veja o [guia de estilo do Sun para Javadoc] (https://www.oracle.com/technetwork/java/index.html#styleguide) para dicas sobre bons comentários em Javadoc.
* Evite capturas amplas (de todas Exception ou todas Throwable) sempre que possível. (Exceto testes de código e situações de *all-or-nothing*.)
* [Preserve toString()] (https://www.oracle.com/technetwork/java/index.html#styleguide)

### Estilo de interação

* [Utilizável em Lynx] (https://github.com/internetarchive/heritrix3/wiki/Usable%20in%20Lynx)
* Recarregável (https://github.com/internetarchive/heritrix3/wiki/Reloadable)
* Use pontos de exclamação e ALL-CAPS com moderação. (A maioria dos avisos, erros ou outros relatórios de falhas não precisam dessa ênfase.)

### Estilo de desenvolvimento

* [Emmit best practices] (https://github.com/internetarchive/heritrix3/wiki/Issue%20best%20practices)
* [Commit best practices] (https://github.com/internetarchive/heritrix3/wiki/Commit%20best%20practices)

## Melhores práticas - Commit

### especificar problema, arquivos modificados e resumo

* especifique o número e o título do problema relacionado
* (opcional) preceda a primeira linha com "Fix for" ou algo parecido
* especifique o arquivo de origem alterado, precedido por *
* resumir as alterações para cada arquivo de origem, precedido (geralmente) por 4 espaços

por exemplo, changeset r6912

```
[HER-1796] H3: interval rescheduling becomes erratic after URI rescheduled more than maxRetries times
* CrawlURI.java
    improve comments for fetchAttempts methods
    (resetForRescheduling) added to clear per-scheduling state
* WorkQueueFrontier.java
    call resetForRescheduling just before scheduling-at-specific-time
* FrontierJournal.java, AbstractFrontier.java
    renamings: prefer 'reenqueue' instead of 'reschedule' for describing simple (non-timed) retries
```

## Melhores práticas - Issue

### inclua o número de revisão ao colar os comentários commit nos problemas

Isso facilita a revisão de alterações no FishEye e, em algum momento (talvez), os conjuntos de alterações podem ser [vinculados automaticamente] (http://confluence.atlassian.com/display/JIRASTUDIO/Creating+Links) ao usar o formato apropriado. Aqui está um bom exemplo do [HER-1760] (https://webarchive.jira.com/browse/HER-1760):

```
Noah Levitt added a comment - 2010-07-21 02:04
Committed possible fix suggested by Gordon. 

------------------------------------------------------------------------ 
r6919 | nlevitt | 2010-07-20 19:03:30 -0700 (Tue, 20 Jul 2010) | 6 lines 
Changed paths: 
   M /trunk/heritrix/src/java/org/archive/crawler/admin/CrawlJobHandler.java 

Possible fix from Gordon for [HER-1760] deadlock starting new job 
* CrawlJobHandler.java 
    terminateCurrentJob() - if startingNextJob thread exists, wait for it to 
    finish, to avoid competing for the lock on 
    CrawlController.registeredCrawlStatusListeners 
```

No [comentário do problema] (https://webarchive.jira.com/browse/HER-1760?focusedCommentId=25196&page=com.atlassian.jira.plugin.system.issuetabpanels%3Acomment-tabpanel#action_25196), "r6919" poderia/deveria estar vinculado ao conjunto de alterações FishEye, pois está, atualmente, nesta página da wiki.

## Preservar toString()

(Na verdade, somos muito ruins a esse respeito.)

O método toString tem um papel especial na descrição de objetos, inclusive em contextos de especialistas/desenvolvedores /depuração. Ao sobrepor toString (), deve ser melhor descrever o objeto e evitar que um objeto se pareça com objetos de outros tipos. Em particular, a forma de toString () não deve ser nem simplificada nem estendida de maneira que outro código esteja dependendo da análise para entregar a funcionalidade.

Em alguns lugares (especificamente em torno das classes UURI/CrawlURI) nós sobrepomos o Object.toString () para retornar uma representação mais 'nua' de um objeto, e então contamos com toString() para funcionalidade.

Infelizmente,  isso oculta informações úteis - como a classe de alguma coisa que relate um URI toString().

Se um objeto precisar de uma sequência de exibição de usuário leigo, um método para essa finalidade específica (por exemplo, 'toDisplayString') deve ser usado. Se um objeto precisar de uma representação de Cadeia de caracteres funcionalmente importante, digamos reduzida a um formato com sua própria lógica e talvez apenas interpretável com contexto extra, outro nome de método específico deve ser usado (por exemplo, 'toURIString' ou 'toCustomString').

Isso retém o toString() em sua função descritiva, seja em sua implementação padrão ou em alguma outra renderização rica centrada em depuração. Isso também significa que toString() pode ser estendido sem medo e sem arriscar a funcionalidade do aplicativo. (Na verdade, esse é um bom teste para qualquer uso planejado de toString() - se o valor retornado for alterado arbitrariamente, qualquer funcionalidade será interrompida? Se sim, toString() está sendo usado incorretamente.)

## Recarregável

Páginas da Web com informações que podem mudar após o carregamento inicial devem ser seguras e fáceis de recarregar - seja por meio do botão de recarga do navegador ou de algum controle na página.

Em particular, as páginas resultantes de POSTs devem, sempre que possível, redirecionar para uma versão estável de recarga no local dos resultados da ação (para a qual uma recarga não solicita rePOSTing nem duplica a ação original).

## Utilizável em Lynx

As interfaces de aplicativos/serviços da Web devem permanecer minimamente utilizáveis até mesmo em navegadores não-estilizados, com JavaScript desativado, dos quais o Lynx (ou Links) é um bom exemplo.

## Como enviar uma versão Heritrix

### Heritrix 1.X

**Antecedentes**

A caixa de compilação contínua (CruiseControl) está em:

http://builds.archive.org:8080/cruisecontrol/buildresults/HEAD-heritrix

O rastreador de problemas do Heritrix está em:

http://webteam.archive.org/jira/browse/HER

O projeto Sourceforge está em:

http://sourceforge.net/projects/archive-crawler/

A página inicial do projeto está em:

http://crawler.archive.org

A página de entrada dessa wiki está em:

http://webteam.archive.org/confluence/display/Heritrix

Em um número de lançamento X.Y.Z, X é o número de lançamento 'principal', Y é o número de lançamento 'secundário' e 'Z' é o número de lançamento 'micro'.

Todos os lançamentos oficiais têm um número de lançamento "menor"; qualquer compilação com um número de lançamento 'secundário' ímpar pode ser considerado como uma compilação do desenvolvedor ou algo fora da caixa de compilação contínua.

### Começando

Antes de qualquer lançamento, verifique se:

* todas as emissões rastreadas segmentadas para esse lançamentos são resolvidas ou reprogramadas para uma versão posterior
* a caixa de compilação contínua constroi com êxito e todos os testes de unidade automática são aprovados, tanto em uma caixa de desenvolvedor local quanto na caixa de compilação
* o desenvolvedor principal concorda que o código está pronto para ser lançado e analisou os logs de confirmação recentes para áreas de interesse
* os committers estão cientes de que o lançamento está previsto para um período razoável (dias para lançamentos "micro"; uma semana+ para lançamentos "secundários") e se absteve de fazer mudanças desestabilizadoras

(Para versões "secundárias" e "principais", outros rastreamentos de teste em escala de produção já devem ter ocorrido, e um "congelamento de código" anunciado no tronco relevante pode ter entrado em vigor por uma semana ou mais.)

Usando as Notas de Lançamento anteriores da página wiki como modelo, crie uma página wiki esqueleto "Notas de Lançamento" para a versão planejada. Deixe a área onde uma data de lançamento é declarada com uma notação 'planejada' ou 'TK'/'TBD' ('no futuro' ou 'a ser determinado').

Adicione notas sobre mudanças significativas que qualquer pessoa que esteja atualizando deve saber, com links para outras páginas wiki ou problemas do JIRA com mais informações.

Use os links de inclusão dinâmica para extrair uma cópia ativa da lista de ocorrências de 'notas de lançamento' do JIRA.

Dê crédidito a todos os colaboradores novos, ou de fora, deste lançamento.

### Verificar os números da versão oficial do lançamento

Faça um commit no tronco que define o número da versão oficial e vincula as 'notas de lançamento' na distribuição às notas completas da versão do wiki.

Especificamente no H1, isso afeta os arquivos 'src/articles/releasenotes.xml' e 'project.xml'. Em 'project.xml', a string:

```
<currentVersion>1.YODD.Z${version.build.suffix}</currentVersion>
```

deve ser alterada para...

```
<currentVersion>1.YEVEN.Z</currentVersion>
```

Quando o lançamento é um lançamento de patch "micro", YEVEN será o número antes do YODD. (Por exemplo, 1.15.3 em desenvolvimento dará origem ao lançamento micro 1.14.3.) Quando o lançamento for um lançamento 'secundário' novo, o número YEVEN será maior. (Por exemplo, se o trabalho que ocorre enquanto o trunk de desenvolvimento está rotulado como '1.15.4' é lançado como lançamento 'secundário' em vez de um lançamento 'micro', ele se tornará '1.16.0'.). 

Aguarde o término da compilação e do teste automáticos da caixa de compilação após o *commit*. Depois, verifique nos logs de *commit* se nenhum commit não intecional foi incluído.

### 'Teste de fumaça'

Verifique se todos os artefatos esperados (.tar.gz, .zip, -src.tar.gz, -src.zip) foram criados e possuem seus nomes de distribuição oficiais.

Faça o download dos artefatos para um diretório remoto e confirme que eles se expandem sem erros e criem as árvores de diretório esperadas.

Pelo menos para o .tar.gz, inicie o rastreador com um webui. Conecte-se ao webui e verifique se os identificadores de versão visíveis são os esperados.

Usando o perfil padrão, configure um rastreamento mínimo de teste de um site com várias páginas (> 1 página, <100). Inicie o rastreamento, verifique a saída esperada no crawl.log e a finalização normal do rastreamento quando terminar.

### Criar ramificação com nome de release no SVN

No repositório SVN, copie a árvore 'trunk/heritrix' para release-'branches/heritrix-1.YEVEN.Z/heritrix'. Isso fornece uma ramificação nomeada de acordo com o que foi inserido na compilação.

### Verificar o número da versão de desenvolvimento

Altere (commit) de volta para o tronco uma versão de 'project.xml' com um número de versão de desenvolvimento atualizado:

```
<currentVersion>1.YODD.Z${version.build.suffix}</currentVersion>
```

(Após um lançamento "micro", o YODD retorna ao seu valor anterior, mas Z deve ser maior. Após um lançamento "secundário" completo, o YODD aumenta para um YODD novo mais alto.)

### Fazer carregamento (upload) para o Sourceforge

As instruções da Sourceforge estão em:

http://apps.sourceforge.net/trac/sitedocs/wiki/Release%20files%20for%20download

O 'pacote' já existirá. Os arquivos são carregados (geralmente via SFTP para frs.sourceforge.net usando sua conta no Sourceforge) e depois reivindicados em um 'lançamento' através de seu aplicativo da web.

Siga as convenções de versões anteriores. Normalmente, incluímos apenas links para as listas wiki/issue nos campos release-notes/change-list do Sourceforge. Verificamos a opção "enviar notificação" para as pessoas acompanhando o projeto.

Por exemplo, fazendo o upload do Heritrix 1.14.4:

```
# ache a última versão do Build Artifacts na caixa de construção
  http://builds.archive.org:8080/cruisecontrol/buildresults/HEAD-heritrix
  05/06/2010 06:14:55 (build.420)
  /0/builds/artifacts/HEAD-heritrix/20100506061455
# visiste build artifacts como ${admin_user}
  ${user}@home$ ssh webteam
  ${user}@webteam$ sudo su ${admin_user}
  ${admin_user}@webteam$ cd /0/builds/artifacts/HEAD-heritrix/20100506061455
# faça o upload da mesma forma que os build artifacts anteriores para o SourceForge via SFTP
  ${admin_user}@webteam$ sftp iasf-admin,archive-crawler@frs.sourceforge.net
  sftp> cd /home/frs/project/a/ar/archive-crawler/
  sftp> mkdir 1.14.4
  sftp> put heritrix-1.14.4-src.zip
  sftp> put heritrix-1.14.4-src.tar.gz
  sftp> put heritrix-1.14.4.zip
  sftp> put heritrix-1.14.4.tar.gz
# definir arquivos de lançamento via SourceForge File Manager
  select Project Admin > File Manager
    /archive-crawler (heritrix 1.x)/
      1.14.4/
  Platform set as default download check boxes
  (select "gear" icon, then Properties for each file)
        heritrix-1.14.4.tar.gz  =>  linux, mac
        heritrix-1.14.4.zip     =>  windows
```

### Atualizar o JIRA

Em "Administer Project", "Manage Versions", marque a versão de lançamento de destino como lançada, com a data atual.

Se um lançamento 'micro' de continuação for planejado antes da próxima versão 'secundária', e ele ainda não existir nas versões do JIRA, inclua-o. (Não se preocupe; é fácil alterar, reorganizar e varrer os problemas de um para o outro.)

### Anunciar

Atualize as notas de lançamento da wiki com a data de lançamento real.

Atualize a página principal da wiki do projeto para listar a nova versão como a mais recente e atualize de acordo as páginas sobre lançamentos futuros.

Envie um email para a lista de projetos do rastreador de arquivos anunciando o lançamento, com links para as notas de lançamento e área de download.

Commit uma mudança ao arquivo 'xdocs/index.xml' no tronco do heritrix que gera, automaticamente, a página inicial do http://crawler.archive.org para incluir um item de notícias no local apropriado anunciando a última versão. (PROBLEMAS PRECISAM DE SOLUÇÕES: Atualmente, as compilações automáticas não estão fazendo o upload do site alterado automaticamente para crawler.archive.org.)

### Heritrix 3

TK

Muito semelhante, mas: a numeração de versão segue as convenções de [Numeração de Versão] (https://github.com/internetarchive/heritrix3/wiki/Version%20Numbering) - arquivos para alterar para refletir o novo número de versão são diferentes; caixa de compilação (Continuum) é uma máquina diferente; a nomenclatura da nota de lançamento da wiki é um pouco diferente.

## Numeração de versão

### Numeração de versões Heritrix

Essa página mostra a convenção de numeração de versão a ser usada, começando logo antes da versão 3.0.1.

Os números de versão H3 e superior são da forma X.Y.Z ou X.Y.Z-SUFIXO.

### Números

X é um  inteiro de versão principal (major). A partir de 2010, o último número da versão principal é '3'. Se o número da versão principal mudar, significa que ocorreram grandes mudanças na funcionalidade e na operação, o que pode exigir uma adaptação significativa dos processos e de outros softwares relacionados que usam o Heritrix ou suas saídas. 

Y é um inteiro de versão secundária. A partir de outubro de 2010, sob a versão principal "3", o maior número de versão secundária é "0". (Embora algumas compilações de desenvolvimento sob o esquema de numeração de versão antiga fossem numeradas como "3.1.1-SNAPSHOT".) Adições ao número de versão secundária ocorrem quando uma nova funcionalidade notável é adicionada, mas processos e software anteriores devem continuar a funcionar, com apenas pequenas alterações, conforme detalhado nas notas de lançamento.

Z é um inteiro de versão micro/patch. Antes do lançamento 3.0.1, o maior número de versão micro sob "3.0", em uma versão oficial, era "0" (para "3.0.0"). (Trabalho em andamento foi nomeado com um número "1" micro, originalmente "3.1.1-SNAPSHOT" sob as convenções mais antigas, mas agora simplesmente "3.0.1-SNAPSHOT".) Adições ao número de versão micro ocorrem quando uma versão oficial inclui correções de bugs e novas funcionalidades menores. Tentamos manter a compatibilidade dos formatos de configuração e até mesmo dos pontos de verificação entre adições de lançamento micro, embora algumas adaptações possam ser necessárias, conforme descrito nas notas de versão.

(Nenhum desses números X, Y, Z está limitado ao intervalo 0-9, e a ordem é baseada no número completo, não no dígito inicial. Portanto, "8.10.0" vem depois de "8.9.99" - porque o "10" no meio é maior que "9".)

### Sufixos

Se uma versão tiver um SUFIXO "SNAPSHOT" - por exemplo, "3.0.1-SNAPSHOT" -, é uma das compilações de desenvolvimento do nosso serviço de construção contínua automática, em preparação para a versão listada. (Isto é, muitas compilações "3.0.1-SNAPSHOT" precedem o lançamento "3.0.1" único e oficial.) Uma compilação "-SNAPSHOT" recebeu apenas o mínimo de testes automáticos e pode ter vários bugs reconhecidos. Essas compilações são recomendadas apenas para especialistas e desenvolvedores que podem solucionar muitos problemas. (TODO: determina como as construções de -SNAPSHOT são diferenciadas depois de descompactadas do registro de data/hora/número de compilação que aparece no pacote de distribuição.)

Outros sufixos de "alpha", "beta", "rc1", "rc2", etc. indicam versões de desenvolvimento específicas cada vez mais próximas de lançamento, que levam à versão nomeada. Cada sufixo (ao contrário de "SNAPSHOT") será uma construção única. Cada sufixo implica mais confiança na adequação da construção para uso em produção. Usuários avançados são encorajados a experimentar esses lançamentos, especialmente os candidatos ao lançamento do 'rc', para dar uma olhada antecipada nos novos recursos e testar aspectos únicos sobre seus rastreamentos. Dessa forma, é possível decobrir quaisquer problemas presentes antes do lançamento final.

## Heritrix em Eclipse

### Heritrix 3.x em Eclipse no Ubuntu

Especificamente Ubuntu 11.04, mas deve funcionar para outras do do período de tempo geral (10.10, 11.10, ...).

Observação importante: Há outras maneiras de fazer isso, por exemplo, usando plug-ins de eclipse adicionais para maven ou git, mas essa é uma maneira que funciona comprovadamente.

**1. Obtenha o software (java, eclipse, git, maven)**

```
sudo apt-get install sun-java6-jdk eclipse git maven2
sudo update-java-alternatives --set java-6-sun  
sudo update-java-alternatives --list
```
**2. Inicie o eclipse para ele inicializar seu espaço de trabalho, por padrão em ~/workspace

**3. Clone o repostório git**

```
cd ~/workspace
git clone git://github.com/internetarchive/heritrix3.git
```

**4. Obtenha as depedency jars com o maven**

```
cd ~/workspace/heritrix3
mvn -Dmaven.test.skip=true install
```

**5. Importe o projeto eclipse**

Em eclipse: File / Import... / Existing Projects Into Workspace ... choose ~/workspace/heritrix3

**6. Configurar a variável M2_REPO no Eclipse**

```
Select Project > Properties > Java Build path > 
Select Libraries tab > Add variable > Configure variables > New
  Name: M2_REPO
  Path: /home/{username}/.m2/repository
```

**7. criar nova configuração Debug**

* Run/Debug Configurations...
* duplo click em Java Applications para criar um novo
* escolha a classe principa org.archive.crawler.Heritrix
* guia Arguments
* Argumentos de programa: -a PASSWORD -l dist/src/main/conf/logging.properties
* Argumentos VM: -Dheritrix.development

