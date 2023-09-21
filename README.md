# **Gerêcia**
## Este código extrai informações de um PDF que contém os holerites dos funcionários de uma micro empresa. As informações extraidas são salvas em uma planilha.


# *Considerações*:
## - O código é baseado em um modelo de holerite específico, talvez o modelo que você usa seja diferente.
## - O código não contempla todos os eventos possíveis em um holerite, mas, contém os principais eventos, como, férias, horas extras, reembolsos, descontos...

# Passo a Passo
## 1- Encontrando a estrutura padrão do PDF
### Em pequenas empresas, a contabilidade é terceirizada, com isso o holerite dos funcionários é fornecido pelo escritório de contabilidade em um arquivo PDF.
### Para trabalhar com estes arquivos é necessário perceber a estrutura padrão do PDF. Neste projeto trabalhei com a biblioteca pdfplumber e com o seguinte padrão:
![HOLERITE MODELO](https://github.com/AbnerEFI/Ger-ncia/assets/145677273/8afbc3d0-cd1f-4c4d-85e3-948b4afbba74)

### A imagem acima, não deiza claro mas esses dados se repetem na página, isto é, cada página do PDF possui 2 vias deste documento na imagem. Isso é muito importante, pois, a biblioteca pdfplumber lê as strings de um pdf, separando as linhas e páginas de forma que o trabalho foi de manipulação de textos, acessando páginas, linhas e dividindo strings e acessando substrings baseando-se na estrutura do arquivo. Resumindo a estrutura temos:
### linha 1: Nome da empresa - Caractere que sempre se repete, caso exista mais de uma empresa, podemos usar como referência "Recibo de Pagamento de Salário" e tudo que vem antes é portanto o nome da empresa.
### linha 2: Note que "Mês:" é uma string fixa. Tudo que vem antes é o endereço, e tudo que vem depois é o mês de referência.
### linha 3: CNPJ da empresa e cidade separados por um espaço. O espaço é fixo, sempre separando essas informações
### linha 6: Código do funcionário e nome, separados por espaço, assim como demais informações do cabeçalho da linha anterior.

### assim como os exemplos dados acima, nas demais linhas também utilizou-se de caracteres estruturais do documento como referencial para obtenção de informações.
