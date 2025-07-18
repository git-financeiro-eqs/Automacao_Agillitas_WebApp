# Arquitetura do Projeto

Este documento descreve a arquitetura do projeto **Automação Agillitas**, explicando a organização dos módulos, suas responsabilidades e como eles interagem entre si.

---

## Visão Geral

O projeto **Automação Agillitas** foi desenvolvido para automatizar o lançamento de RTs (Recibos e Danfes) no sistema ERP Microsiga. A automação é dividida em dois modos principais de operação:

1. **mariquinhaCorrente**: Realiza o lançamento sequencial de RTs pendentes.
2. **mariquinhaUnitária**: Realiza o lançamento de uma RT específica, definida pelo operador, podendo até lançar RTs baixadas parcialmente.

A automação é composta por **12 módulos**, cada um com responsabilidades específicas, que trabalham em conjunto para garantir o funcionamento correto do sistema.

---

## Módulos do Projeto

Abaixo estão os módulos que compõem o sistema, com suas responsabilidades e interações:

<table>
  <thead>
    <tr>
      <th>Módulo</th>
      <th>Responsabilidade</th>
      <th>Funcionalidades</th>
      <th>Interações</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><strong>acaoComum</strong></td>
      <td>Fornece funções compartilhadas pelos módulos <strong>mariquinhaCorrente</strong> e <strong>mariquinhaUnitária</strong>.</td>
      <td>
        <ul>
          <li>Ações comuns, como posicionar o cursor em pontos específicos da interface.</li>
          <li>Funções para filtrar RTs, solicitar XMLs, copiar chaves de acesso e tratar erros.</li>
        </ul>
      </td>
      <td>Utilizado por <strong>mariquinhaCorrente</strong> e <strong>mariquinhaUnitária</strong> para evitar duplicação de código.</td>
    </tr>
    <tr>
      <td><strong>extratorXML</strong></td>
      <td>Extrai e processa os dados dos XMLs das notas fiscais.</td>
      <td>
        <ul>
          <li>Leitura de XMLs para validação de valores e aplicação de TES (154 ou 155).</li>
          <li>Coleta de informações como valores totais, impostos e detalhes dos itens.</li>
        </ul>
      </td>
      <td>Utilizado pelos módulos de lançamento (<strong>mariquinhaCorrente</strong> e <strong>mariquinhaUnitária</strong>) para processar Danfes.</td>
    </tr>
    <tr>
      <td><strong>gui</strong></td>
      <td>Interface gráfica da automação.</td>
      <td>
        <ul>
          <li>Permite ao usuário interagir com a automação, selecionar modos de operação e configurar parâmetros.</li>
        </ul>
      </td>
      <td>Comunica-se com o módulo <strong>main</strong> para iniciar a execução.</td>
    </tr>
    <tr>
      <td><strong>main</strong></td>
      <td>Executa o programa e abre a interface gráfica.</td>
      <td>
        <ul>
          <li>Inicializa a automação e carrega a interface gráfica.</li>
        </ul>
      </td>
      <td>Depende do módulo <strong>gui</strong> para exibir a interface ao usuário.</td>
    </tr>
    <tr>
      <td><strong>mariquinhaCorrente</strong></td>
      <td>Realiza o lançamento sequencial de RTs.</td>
      <td>
        <ul>
          <li>Processa RTs pendentes de forma contínua.</li>
          <li>Utiliza funções do módulo <strong>acaoComum</strong> para ações compartilhadas.</li>
        </ul>
      </td>
      <td>Comunica-se com <strong>utils</strong> para se movimentar pela interface do SIGA e com <strong>operadoresLancamento</strong></td>
    </tr>
    <tr>
      <td><strong>mariquinhaUnitária</strong></td>
      <td>Realiza o lançamento de uma única RT.</td>
      <td>
        <ul>
          <li>Processa uma RT específica, definida pelo usuário, podendo ser parcial ou não.</li>
          <li>Utiliza funções do módulo <strong>acaoComum</strong> para ações compartilhadas.</li>
        </ul>
      </td>
      <td>Comunica-se com <strong>utils</strong> para se movimentar pela interface do SIGA, e com <strong>operadoresLancamento</strong></td>
    </tr>
    <tr>
      <td><strong>mensagens</strong></td>
      <td>Contém mensagens de apresentação e instruções para o usuário.</td>
      <td>
        <ul>
          <li>Manual de uso.</li>
        </ul>
      </td>
      <td>Apresentado assim que o executável da automação é clicado</td>
    </tr>
    <tr>
      <td><strong>operadoresLancamento</strong></td>
      <td>Fornece funções utilitárias para conferência e validação dos valores da NF.</td>
      <td>
        <ul>
          <li>Compara valores da NF com os valores apresentados na tela de lançamento do SIGA.</li>
        </ul>
      </td>
      <td>Utilizado pelo módulo <strong>acaoComum</strong> durante o lançamento.</td>
    </tr>
    <tr>
      <td><strong>ContingenciarResponsividade</strong></td>
      <td>Classe com funções de monitoramento de instabilidade no momento de lançamento da DANFE no Siga WebApp.</td>
      <td>
        <ul>
          <li>Seus métodos são executados em paralelo às funções de lançamento.</li>
          <li>Seu propósito é monitorar quando ocorrerá um deslize no percurso da automação no momento do lançamento,
            procurando por erros pontuais.</li>
          <li>Quando identificado um deslize, a automação dispara um gatilho que dá o comando para automação cancelar o lancamento
            para reinicia-lo.</li>
        </ul>
      </td>
      <td>Utilizado pelos módulos <strong>operadoresLancamento</strong> e <strong>acaoComum</strong> durante o lançamento.</td>
    </tr>
    <tr>
      <td><strong>tratamentoItem</strong></td>
      <td>Trata itens que foram fracionados durante o lançamento.</td>
      <td>
        <ul>
          <li>Aplica razão e proporção aos valores de itens fracionados.</li>
        </ul>
      </td>
      <td>Utilizado pelos módulos de lançamento para garantir a precisão dos valores.</td>
    </tr>
    <tr>
      <td><strong>utils</strong></td>
      <td>Fornece funções utilitárias para todos os módulos do sistema.</td>
      <td>
        <ul>
          <li>Funções genéricas, formatação de dados, log em E-mail e manipulação de strings, além de movimentações repetitivas.</li>
        </ul>
      </td>
      <td>Utilizado por todos os módulos que precisam de funcionalidades comuns.</td>
    </tr>
    <tr>
      <td><strong>atuadorWeb</strong></td>
      <td>Fornece funções para interação com paginas web, seja para abertura da pagina ou algumas manipulações específicas.</td>
      <td>
        <ul>
          <li>Função de abertura do microsiga WebApp, além de ações como clicar ou esperar um elemento HTML passado.</li>
        </ul>
      </td>
      <td>Utilizado pelas Mariquinhas para abertura do Microsiga WebApp.</td>
    </tr>
  </tbody>
</table>

                                                          
---

## Diagrama de Arquitetura

Aqui está um diagrama que mostra como os módulos se relacionam:


[main] <- [gui] <- [mariquinhaCorrente]  
[mariquinhaCorrente] <- [acaoComum] <- [extratorXML]    
[main] <- [gui] <- [mariquinhaUnitária]   
[mariquinhaUnitária] <- [acaoComum] <- [extratorXML]    
[mariquinhaCorrente] <- [acaoComum] <- [operadoresLancamento] <- [ContingenciarResponsividade]  
[mariquinhaUnitária] <- [acaoComum] <- [operadoresLancamento] <- [ContingenciarResponsividade]  
[tratamentoItem] -> [acaoComum]    
[atuadorWeb] -> [acaoComum]  
[utils] -> Todos os módulos    
[mensagens] -> Todos os módulos    

---

## Fluxo de Execução

1. O módulo **main** inicia o programa e carrega a interface gráfica (**gui**).
2. O usuário seleciona o modo de operação (**mariquinhaCorrente** ou **mariquinhaUnitária**).
3. Dependendo do modo selecionado, o módulo correspondente inicia o processamento das RTs.
4. Durante o lançamento, os módulos **extratorXML** e **operadoresLancamento** são utilizados para validar e processar os dados.
5. O módulo **tratamentoItem** é acionado caso haja itens fracionados.
6. Todas as mensagens e logs são gerenciados pelos módulos **mensagens** e **utils**.
7. O processo se repete até que todas as RTs sejam processadas.

---

## Observações  

A Biblioteca Pyautogui é uma maneira diferente de execultar a técnica da raspagem de tela. Para o Pyautogui execultar essa técnica, é preciso tirar um print do elemento que se deseja procurar, salvá-lo em algum diretório, e passar o caminho desse arquivo para o método locateOnScreen. As imagens dos elementos que foram mapeadas para essa automação estão na pasta Imagens.  
  
Se você que está lendo esse documento parar pra estudar a diferença entre a versão da Mariquinha desenvolvida para o Microsiga Desktop e a versão da Mariquinha voltada para o Microsiga WebApp, que é essa daqui, você perceberá que além da nova forma de acessar o Microsiga, também tem algumas mudanças particulares neste código, como algumas manipulações de teclado execultadas pelo método send_keys() da lib Selenium. Isso, e tantas outras pequenas mudanças do modo de operar desta automação, aconteceram por causa das limitações do sistema WebApp. O Microsiga WebApp tem uma péssima responsividade, além de ser mais lento que o antigo SmartClient. Para contornar essas imprevisibilidades que a instabilidade que a interface WebApp oferece, em alguns pontos criticos da operação, atribui o controle do mouse e do teclado aos métodos disponibilizados pela lib Selenium em vez de manter tudo com o Pyautogui, além de criar o módulo contingenciarResponsividade, que nada mais é do que uma classe vigilante, que fica monitorando se o percurso que o bot executa não sofreu nenhuma Interferência da instabilidade da interface web do Siga. Foi a forma que encontrei para contornar as imperfeições do WebApp. Sei que o código parece um "Frankenstein" usando duas libs que fazem a mesma coisa, ora uma comanda as ações, ora a outra comanda, mas, com pouco tempo para fazer funcionar novamente a automação, creio que o que foi feito foi a melhor das alternativas.

---

## Conclusão

A arquitetura do projeto **Automação Agillitas** foi projetada para ser modular e flexível, permitindo a reutilização de código e a fácil manutenção. Cada módulo tem uma responsabilidade clara, e a interação entre eles é bem definida, garantindo um fluxo de trabalho eficiente e confiável.

---
