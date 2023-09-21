# *Gerêcia*
## Este código extrai informações de um PDF que contém os holerites dos funcionários de uma micro empresa. As informações extraidas são salvas em uma planilha.


# Considerações:
## - O código é baseado em um modelo de holerite específico, talvez o modelo que você usa seja diferente.
## - O código não contempla todos os eventos possíveis em um holerite, mas, contém os principais eventos, como, férias, horas extras, reembolsos, descontos...

# Passo a Passo
## 1- Analisando o PDF
### Em pequenas empresas, a contabilidade é terceirizada, com isso o holerite dos funcionários é fornecido pelo escritório de contabilidade em um arquivo PDF.
### Para trabalhar com estes arquivos é necessário perceber a estrutura padrão do PDF. Neste projeto trabalhei com a biblioteca pdfplumber e com o seguinte padrão:
![HOLERITE MODELO](https://github.com/AbnerEFI/Ger-ncia/assets/145677273/55b7a171-82dc-479b-afae-bf7891bf2551)
