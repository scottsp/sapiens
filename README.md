# sapiens
Alguns comandos para desenvolvimento em LSP da Senior Sistemas

GRAVAÇÃO DE ARQUIVOS NO HD

@------ DESMONTA A DATA PARA GRAVAÇÃO DO CAMINHO DO ARQUIVO ------@
converteMascara( 3, integra, data_string, "DDMMYYYY" );
converteMascara( 4, hs_number, hs_string, "hhmm"     );
caminho =  "C:\\EDI\\ImpEDI_" + data_string + "_" + hs_string + "Hs.log";

@------ ABRE O ARQUIVO DE LOGS PARA TRATAMENTOS DE ERROS ------@
arquivo = abrir( caminho, gravarnl );

@------ GRAVA A MENSAGEM ------@
gravarnl( arquivo, "Não houve erros de CÓDIGO DE PRODUTOS DO CLIENTE!" );

@------ FECHA O ARQUIVO DE LOGS ------@
fechar( arquivo );

============================================================================================================================

CRIAÇÃO DE XCURSOR

Definir Alfa xCursor;
SQL_Criar(xCursor);
SQL_UsarSQLSenior2(xCursor,0);  @define que o comando deverá utilizar a sintaxe nativa do banco de dados@
SQL_UsarAbrangencia(xCursor, 0);  @1 = Usa abrangência, 0 = Não usa abrangência@
SQL_DefinirComando(xCursor,"SELECT NUMARB, CODCLI, CODCRT \
                              FROM E301TCR \
                             WHERE (USU_DATENV IS NULL \
                                OR USU_DATENV = TO_DATE('31/12/1900', 'DD/MM/YYYY')) \
                               AND SITTIT = 'AB' \
                               AND CODPOR = :xCodPor \
                               AND CODEMP = :xCodEmp \
                               AND CODFIL = :xCodFil \
                          GROUP BY NUMARB, CODCLI, CODCRT \
                          ORDER BY NUMARB, CODCLI, CODCRT");                        
SQL_DefinirInteiro(xCursor, "xCodEmp", nCodEmp);
SQL_DefinirInteiro(xCursor, "xCodFil", nCodFil);
SQL_DefinirAlfa(xCursor, "xCodPor", aCodPor);
@SQL_DefinirData(xCursor, "xDatEmi", dDatEmi);@
SQL_AbrirCursor(xCursor);
  Enquanto (SQL_EOF(xCursor) = 0) {
    SQL_RetornarInteiro(xCursor,"NumArb", nNumArb);
    SQL_RetornarInteiro(xCursor,"CodCli", nCodCli);
    SQL_RetornarAlfa(xCursor,"CodCrt", aCodCrt);
	
	
    SQL_Proximo(xCursor);
  }
SQL_FecharCursor(xCursor);
SQL_Destruir(xCursor);

============================================================================================================================

INSTANCIANDO WEBSERVICE

                                    WebService.Porta    variavel
Definir interno.com.senior.g5.co.ger.Relatorio.Executar vRelatorio;
vRelatorio.prEntrada = "<ENumEmp="+ aNumEmp +">" +
                       "<ENumFil="+ aNumFil +">" +
                       "<ECodCli="+ aCodCli +">" +
                       "<ECodPor="+ aCodPor +">" +
                       "<ECodCrt="+ aCodCrt +">" +
                       "<ENumAbr="+ aNumArb +">" +
                       "<EContato="+ aContato +">" +
                       "<EAssDir="+ aAssDir +">" +
                       "<EDatEmi="+ aDatEmi +">" +
                       "<ETipRel="+ aTipRel +">"; /* os valores de entrada devem ficar como no exemplo acima separados por <Variavel de entrada = valor ><Variavel de entrada = valor > */
vRelatorio.prRelatorio = "FRCR200.GER"; @ nome do relatório @ 
vRelatorio.prRemetente = "BRASPAR Receber <receber@brasparparafusos.com.br>"; 
vRelatorio.prCc = "receber@brasparparafusos.com.br"; /* Solicitado por Michele - Financeiro 08/09/2020 - Retirei financeiro@brasparparafusos.com.br */
vRelatorio.prCco = "marcelo@brasparparafusos.com.br;rosane@brasparparafusos.com.br";
vRelatorio.prFileName = "RecebiveisWV.pdf"; @ nome do arquivo a ser gerado @ 
vRelatorio.prExecFmt = "tefMail"; @ Define como será o tipo de saída tefMail é por e-mail, tefFile = arquivo @ 
vRelatorio.prDest = aEmaCli; @ INSERIR AQUI O E-MAIL DO CLIENTE @
vRelatorio.prAssunto = "Gestão de Recebíveis Ind. Mec. Braspar para WV Arambul Serv. Adm."; 
vRelatorio.prAnexoBool = "T"; /* Indica se o relatório será enviado como anexo (T) ou no corpo da mensagem (F), padrão T. */ 
vRelatorio.prMensagem = aEmaCor; /* a variavel enter foi definida como alfa e através da função RetornaAscII é utilizada para quebra de linha */ 
@vRelatorio.prUniqueFile = "S";@ @ Se o tipo da imagem será Bitmap. Essa informação é opcional. @
vRelatorio.prEntranceIsXML = "F"; /* Para a execução do serviço de execução de relatório via regra é necessário preenche o campo prEntranceIsXML com o valor "F" e o valores de entrada, caso sejam necessários, na seguinte formatação */ 
@vRelatorio.prTypeBmp = "S";@ @ Padrao = N @   
@vRelatorio.ModoExecucao = 3;@ /* IMPORTANTE: Para execução local (na estação), deve-se utilizar o ModoExecucao = 1; o ModoExecucao = 3 é utilizado para execução no Servidor Middleware, onde o arquivo será gerado no Servidor. */
vRelatorio.prSaveFormat = "tsfPDF"; @ Indica o formato do arquivo salvo em arquivo @
vRelatorio.Executar();
aErro = vRelatorio.prLOG; @ retorna o erro a mensagem de erro @

============================================================================================================================

TRABALHANDO COM GRADE EM RELATÓRIOS MODO GRÁFICO

- Adição das Linhas e Colunas
Para adicionar as linhas e colunas em tempo de execução foi disponibilizado o método:

AdicionaDadosGrade(AlfaControlName,  Numero Linha, Numero Coluna, Alfa Texto);

- Limpeza das Linhas e Colunas
Para limpar os dados adicionados ao controle basta chamar a função:

LimpaDadosGrade(Alfa ControlName);

- Truncar Valores de Células
Trunca valores de células de uma grade no gerador de relatório.

TruncaDadosGrade(AlfaControlName,  Numero Linha, Numero Coluna);

============================================================================================================================

PULANDO LINHA EM REGRAS

Definir Alfa aLF;
Definir Alfa aCR;

CaractereParaAlfa(10, aLF)
CaractereParaAlfa(13, aCR)

Texto = Texto + aCR + aLF;

============================================================================================================================
