# **Recibo de Salário (holerite)**
 Este código extrai informações de um PDF que contém os holerites dos funcionários de uma micro empresa. As informações extraidas são salvas em uma planilha.


# *Considerações:*
- O código é baseado em um modelo de recibo específico, talvez o modelo que você usa seja diferente.
- O código não contempla todos os eventos possíveis em um recibo, mas, contém os principais eventos, como, férias, horas extras, reembolsos, descontos...

# Passo a Passo
## 1- Encontrando a estrutura padrão do PDF
Em pequenas empresas, a contabilidade é terceirizada, com isso o recibo dos funcionários é fornecido pelo escritório de contabilidade em um arquivo PDF.
Para trabalhar com estes arquivos é necessário perceber a estrutura padrão do PDF. Neste projeto trabalhei com a biblioteca pdfplumber e com o seguinte padrão:
![HOLERITE MODELO](https://github.com/AbnerEFI/Ger-ncia/assets/145677273/8afbc3d0-cd1f-4c4d-85e3-948b4afbba74)

A imagem acima, não deiza claro mas esses dados se repetem na página, isto é, cada página do PDF possui 2 vias deste documento na imagem. Isso é muito importante, pois, a biblioteca pdfplumber lê as strings de um pdf, separando as linhas e páginas de forma que o trabalho foi de manipulação de textos, acessando páginas, linhas e dividindo strings e acessando substrings baseando-se na estrutura do arquivo. Resumindo a estrutura temos:
linha 1: Nome da empresa - Caractere que sempre se repete, caso exista mais de uma empresa, podemos usar como referência "Recibo de Pagamento de Salário" e tudo que vem antes é portanto o nome da empresa.
linha 2: Note que "Mês:" é uma string fixa. Tudo que vem antes é o endereço, e tudo que vem depois é o mês de referência.
linha 3: CNPJ da empresa e cidade separados por um espaço. O espaço é fixo, sempre separando essas informações
linha 6: Código do funcionário e nome, separados por espaço, assim como demais informações do cabeçalho da linha anterior.

assim como nos exemplos dados acima, nas demais linhas também utilizou-se de caracteres estruturais do documento como referencial para obtenção de informações. Vamos prosseguir agora com o código.

## 2 - Importando bibliotecas:
Neste projeto utilizei pandas, numpy, pdfplumber e os, para criar Dataframe, manipular listas, manipular o PDF mudar diretórios, respectivamente.
```
import pdfplumber
import pandas as pd
import numpy as np
import os
```
## 3 - Criar lista de eventos:
Para cada item no recibo, existe um 'id', os quais foram listados a fim de comparar o 'id" encontrado no recibo com a lista e assim saber qual evento gerou crédito ou débito ao funcionário. Além disso uma lista vazia foi criada para receber os dados de cada funcionário de modo que, cada linha representa o recibo por mês, por funcionário.
```
#LISTA DE EVENTOS
colunas=['00001', '00009', '00013', '00018', '00039', '00204', '01005', '00210', '00080', '00081', '00253', '00252', '00037', '00077', '00121', '00122', '00123', '00125', '00128', '00129', '00423']

folha= []
```
## 4 - Mudando o 'path' 
Nessa etapa mudamos o path do python para a pasta onde se encontram os PDF's com os recibos. É importante que nesta pasta só existam PDF's de recibos, todos do mesmo modelo, para que não receber erros. Atribuímos o título de cada PDF a elementos de uma lista, é usada na próxima etapa para acessar os PDF's.
```
#MUDANÇA NO PATH PARA ABERTURA DOS ARQUIVOS.
path=('COLOQUE AQUI O DIRETÓRIO DOS SEUS DADOS')
os.chdir(path)
lista=os.listdir(path)
```
## 5 - Acessando os PDF's
No meu caso eu tinha muitos arquivos para abrir, e cada arquivo possui muitas págnas, então usei um loop 'for' para percorrer todos os arquivos e todas as páginas. Além de abrir um PDF da lista esse código atribui cada página a um elemento da lista 'page', que é usada na próxima etapa.
```
# ABERTURA DE PDF'S PARA LEITURA DE DADOS 
for j in range(1, len(lista)):
     print(lista[j])
     pdf=pdfplumber.open(lista[j])
     page=pdf.pages
     print('PDF=', j)
```
## 6 - Acessando cada Recibo
Cada página do PDF corresponde ao recibo de uma pessoa e possui duas guias do recibo. Aqui a página é acessada e o texto é extraído para a variável 'text'. O texto extraído com 'pdfplumber' possui o separador de linhas'\n', contamos a quantidade desses separadores e adicionamos 1, e dessa forma temos o total de linhas da página. O objetivo com isso é obter o número de linhas de somente um recibo. Ainda nesta etapa, uma lista é criada, essa lista é destinada a receber as informações retiradas do recibo, onde cada coluna é destinada a um tipo de informação que está contida no recibo e caso a informação não exista, a lista ja é pré iniciada com valor zero em suas entras.
```
#LOOP DE LETURA DE CADA PAGINA DA FOLHA DE PAGAMENTO
     for i in range(len(page)):
          text=page[i].extract_text()
          lines=int((text.count('\n')+1)/2)
          linha=[0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
          print('PAGINA=', i)
```
## 7  - Obtendo dados 
### 7.1 - Dados do cabeçalho
Nesta etapa obtemos informações de mês, ano, funcionário, função, e adicionamos as informasções nas colunas da lista 'linha'.  Primeiro a informação do cabeçalho é retirada, e depois disso declaramos algumas variáveis que receberão informações como 'NONE'.
A informação é obtida cortando a string 'text' que contém o texto do recibo, pelos caracteres estruturais, utilizando para isso a função 'split()' uma vez que a string é cortada selecionamos a substring que nos interessa. Por exemplo: text.split('\n')[0]. Vai separar o texto da variavel 'text', por "\n" (nova linha), dessa forma a string fica separada por linhas. O argumento[0], é a seleção da substring que está na posição '0', ou seja, a primeira linha.
```
#VARIÁVEIS PARA SALVAR
        
          mes_ref= text.split('\n')[1].split(':')[1].split('/')[0]
          linha[0]=mes_ref
          ano_ref=text.split('\n')[1].split(':')[1].split('/')[1]
          linha[1]=ano_ref
          funcionario= text.split('\n')[4].split(' ')[1]+ ' ' + text.split('\n')[4].split(' ')[2]+ ' ' + text.split('\n')[4].split(' ')[3]
          linha[2]=funcionario
          funcao= text.split('\n')[5].split(' ')[0]
          linha[3]=funcao

          gratificacao=None
          adiantamento=None
          sal_norm_dinheiro=None
          dif= None
             ...
```
### 7.2 - Informações dos eventos
Nesta etapa acesamos as informações e atualizamos a lista 'linha' percorrendo todas as linhas entre o cabeçalho e o rodapé do recibo, essa informação é possível de se obter pois sabemos o número exato de linhas do recibo, do cabeçalho e do rodapé. Aqui 'info' é a string separada por linhas. de modo que info[a] acessa a linha 'a' da string. Dentro de cada linha manipulamos as strings para separá-las por caracteres estruturais.

```
for k in range(7, lines-5):
               print('k=',k)
               info=text.split('\n')[k]
               if colunas[0]in info:
                    sal_norm_dinheiro=info.split(colunas[0])[1].split(' ')[4]
                    print(info.split(colunas[0])[1].split(' ')[3])
                    sal_norm_dias=info.split(colunas[0])[1].split(' ')[3].split('/')[1]
                    linha[4]=sal_norm_dias
                    linha[5]=sal_norm_dinheiro
                    continue
               elif sal_norm_dias==None:
                    sal_norm_dinheiro=0
                    sal_norm_dias=0
                    linha[4]=sal_norm_dias
                    linha[5]=sal_norm_dinheiro

if colunas[1] in info:
                    hora_extra_50= info.split(colunas[1])[1].split(' ')[4]
                    extra_50_val=info.split(colunas[1])[1].split(' ')[5]
                    linha[6]=hora_extra_50
                    linha[7]=extra_50_val
                    continue
               elif hora_extra_50 == None:
                    hora_extra_50= 0
                    extra_50_val=0
                    linha[6]=hora_extra_50
                    linha[7]=extra_50_val
                           ...
```
Como você perceberá resumi o código aqui. O que acontece aqui é que, em cada linha entre cabeçalho e rodapé, testamos todos os 'id' de eventos. Isso acontece porque nem sempre todos os eventos estão presentes, e podem aparecer em linhas diferentes. Por isso, em cada linha testa-se todos os 'id' quando ocorre um math entre o  'id'  do recibo (da linha em que estamos) com o 'id' listado as variáveis são atualizadas e inseridas na lista 'linha', enquanto isso não acontece, o valor é mantido o mesmo (pois iniciamos as variáveis como zero na etapa anterior), isto é, zero.

### 7.3 - Informações do rodapé
Esta etapa é muito parecida com a 7.1 a diferença é que a referência está no fim da string, isto é, procuramos a informação do fim para o início.
```
total_vencimentos=text.split('\n')[lines-4].split(' ')[0]
          linha[22]=total_vencimentos
          total_descontos=text.split('\n')[lines-4].split(' ')[1]
          linha[23]=total_descontos
          total_liquido=text.split('\n')[-3].split(' ')[2]
          linha[24]=total_liquido
          salario_base=text.split('\n')[lines-1].split(' ')[0]
          linha[25]=salario_base
```
## 8 - Exportando dados para uma planilha
Ao fim dos loops, todas as linhas de todas as páginas de todos os PDF's foram percorridas, e a lista 'linha' possui todas as informações. Agora um Dataframe é feito com um cabeçalho baseado nas informações que foram extraídas. O Dataframe é então exportado para uma planilha, no diretório que desejamos.
```
dataframe=pd.DataFrame(folha, columns=['Mês de Referência','Ano Referênca', 'Colaborador', 'Função', 'Dias Trabalho Normal', 'Salario Normal', 'Hora Extra 50%', 'Extra 50%', 'Hora Extra 100%', 'Extra 100%', 'Extra DSR (Integração)', 'Extra DSR', '% Insalubridade', 'Val. Insalubridade', 'Gratificação', 'DIF SALARIAL','Ajuda de Custo', 'INSS', '%IRRF', 'Val. IRRF', 'Adiantamento', 'Seguro de Vida', 'Total Vencimentos', 'Total Descontos', 'Total Líquido', 'Salário Base', 'Sal. Contr INSS', 'Base FGTS', 'FGTS', 'Base IRRF', 'Faixa IRRF', 'Add. Noturno Horas', 'Add Noturno Valor', 'Faltas Horas', 'Faltas Valor', 'Ferias Normais', 'INT.HE.FERIAS', 'INT. ADIC. INSAL. FERIAS', 'INT. ADIC. NOTURNO FERIAS', 'ADICIONAL 1/3 S/FERIAS', 'LIQUIDO FERIAS NORMAIS', 'Reembolso'])
print(dataframe)
#SALVA DATAFRAME COMO PLANILHA NA PASTA #
os.chdir('Diretório para salvar')
dataframe.to_excel('FOLHA.xlsx')
```
